---
title: Azure HDInsight 'ta fırtınası için SCP.NET Programlama Kılavuzu
description: Oluşturmak için SCP.NET kullanmayı öğrenin. Azure HDInsight 'ta çalışan fırtınası ile kullanım için NET tabanlı fırtınası topolojileri.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: b7bb26cd35daf67a3337907aded18e3302b19d81
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813874"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Storm için SCP Programlama Kılavuzu

SCP gerçek zamanlı, güvenilir, tutarlı ve yüksek performanslı veri işleme uygulaması oluşturmaya yönelik bir platformdur. Bu, OSS toplulukları tarafından tasarlanan bir akış işleme sistemi olan [Apache Storm](https://storm.incubator.apache.org/) üzerine kurulmuştur. Fırtınası, Nathan Marz tarafından tasarlanmıştır ve Twitter tarafından açıktır. Son derece güvenilir dağıtılmış koordinasyon ve durum yönetimini etkinleştirmek için [Apache ZooKeeper](https://zookeeper.apache.org/), başka bir Apache projesi kullanır. 

Yalnızca Windows üzerinde bir SCP projesi, ancak projenin uzantıları ve özelleştirmesi Windows ekosistemi için de eklenmiştir. Uzantılar .NET Geliştirici deneyimini ve kitaplıklarını içerir. özelleştirme, Windows tabanlı dağıtımı içerir. 

Uzantı ve özelleştirme, OSS projelerini çatallarına gerek duymadığımız bir şekilde yapılır ve fırtınası üzerinde oluşturulmuş türetilmiş ekosistemlerinden faydalanabilir.

## <a name="processing-model"></a>İşlem modeli
SCP 'deki veriler, tanımlama gruplarının sürekli akışları olarak modellenir. Genellikle, ilk olarak bir sırada bir sıraya akar ve bir fırtınası topolojisi içinde barındırılan iş mantığına göre dönüştürülür, son olarak başka bir SCP sistemine yönelik tanımlama grupları olarak veya dağıtılmış dosya sistemi veya veritabanları gibi mağazalara kaydedilmiş olabilir. SQL Server gibi.

![Bir kuyruğun bir diyagramı, veri deposu beslemelerini işlemek için veri beslenir](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Fırtınası ' da, bir uygulama topolojisi hesaplama grafiğini tanımlar. Bir topolojideki her düğüm, işlem mantığını içerir ve düğümler arasındaki bağlantılar veri akışını gösterir. Topolojiye giriş verisi ekleme düğümlerine, verileri sıralamak için kullanılabilecek _Spout_'lar adı verilir. Giriş verileri dosya günlüklerinde, işlem veritabanında, sistem performansı sayacında vb. olabilir. Hem giriş hem de çıkış verisi akışına sahip düğümler, gerçek veri filtreleme ve seçim ve toplama işlemlerini sağlayan _cıvatalar_olarak adlandırılır.

SCP, en az bir kez ve tam bir kez veri işleme olmak üzere en iyi çabaları destekler. Dağıtılmış bir akış işleme uygulamasında, veri işleme sırasında ağ kesintisi, makine arızası veya Kullanıcı kodu hatası gibi çeşitli hatalar oluşabilir. En az bir kez işleme, hata oluştuğunda otomatik olarak aynı veriler yeniden çalıştırılarak tüm verilerin en az bir kez işlenmesini sağlar. En az bir kez işleme basit ve güvenilirdir ve çok sayıda uygulamaya uygun olur. Ancak, bir uygulama tam sayım gerektirdiğinde, aynı veriler uygulama topolojisinde yürütülebildiğinden en az bir kez işleme yeterli değildir. Bu durumda, veriler yeniden çalındığında ve birden çok kez işlenebilse bile sonucun doğru olduğundan emin olmak için tam bir kez işleme tasarlanmıştır.

SCP, .NET geliştiricilerinin Java Sanal Makinesi (JVM) üzerinde, kapsamaları altında kaş ile yaşam sırasında gerçek zamanlı veri işlem uygulamaları geliştirmesini sağlar. .NET ve JVM, TCP yerel Yuvaları üzerinden iletişim kurar. Temel olarak her Spout/Cıvam, Kullanıcı mantığının .NET işleminde bir eklenti olarak çalıştırıldığı .NET/Java işlem çiftidir.

SCP 'nin üstünde bir veri işleme uygulaması oluşturmak için birkaç adım gerekir:

* Kuyruktaki verileri çekmek için Biriktirmalar tasarlayın ve uygulayın.
* Giriş verilerini işlemek ve verileri veritabanı gibi dış depolara kaydetmek için Cıvatları tasarlayın ve uygulayın.
* Topolojiyi tasarlayın, ardından topolojiyi oluşturup çalıştırın. Topoloji, izdüşümlerinin yanı sıra İzdüşümlü arasındaki veri akışlarını tanımlar. SCP, topoloji belirtimini alır ve her köşenin tek bir mantıksal düğümde çalıştırıldığı bir fırtınası kümesine dağıtır. Yük devretme ve ölçeklendirme, fırtınası görev zamanlayıcısı tarafından ele alınacaktır.

Bu belgede, SCP ile veri işleme uygulamasının nasıl oluşturulacağı hakkında yol göstermek için bazı basit örnekler kullanılmaktadır.

## <a name="scp-plugin-interface"></a>SCP eklenti arabirimi
SCP eklentileri (veya uygulamaları), hem geliştirme aşamasında Visual Studio içinde hem de üretim sırasında dağıtımdan sonra fırtınası işlem hattına takılmış tek başına EXEs ' dir. SCP eklentisinin yazılması, diğer standart Windows konsol uygulamalarının yazılmasında yalnızca aynıdır. SCP.NET platformu, Spout/cıvala ilgili bir arabirim bildirir ve Kullanıcı eklenti kodu bu arabirimleri uygulamalıdır. Bu tasarım ana amacı, kullanıcının kendi iş logics ve SCP.NET platformu tarafından işlenecek başka şeyler bırakarak odaklanabilirsiniz.

Kullanıcı eklenti kodu, izlenen arabirimlerinden birini uygulamalıdır, topolojinin işlem tabanlı mi yoksa işlem dışı mı olduğunu ve bileşenin bir Spout ya da bir sürgüsü olup olmamasına bağlıdır.

* ISCPSpout
* Iscpsürgüsü
* ISCPTxSpout
* Iscpbatchcıvata

### <a name="iscpplugin"></a>SCC
SCC, tüm eklenti türleri için ortak arabirimdir. Şu anda bu bir kukla arabirimdir.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout, işlemsel olmayan Spout için arabirimidir.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Çağrıldığında, C\# Kullanıcı kodu bir veya daha fazla tanımlama grubu yayabilir. `NextTuple()` Yayma için bir şey yoksa, bu yöntem hiçbir şey yaymadan döndürmelidir. `NextTuple()`, `Ack()`, Ve tümünün`Fail()` Cişlemindetekbirişparçacığındasıkıbirdöngüdeçağrıldığını\# not edilmelidir. Görüntülenecek bir tanımlama grubu olmadığında, çok fazla CPU harcanmaması için kısa bir süre (10 milisaniyelik gibi) için NextTuple uykuya geçme imkanı vardır.

`Ack()`ve `Fail()` yalnızca spec dosyasında ACK mekanizması etkinleştirildiğinde çağrılır. , `seqId` Onaylanan veya başarısız olan kayıt grubunu tanımlamak için kullanılır. Bu nedenle, ACK işlem dışı topolojide etkinleştirilirse, Spout 'de aşağıdaki yayma işlevi kullanılmalıdır:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

ACK, işlemsel olmayan topolojide `Ack()` desteklenmiyorsa, ve `Fail()` boş işlev olarak bırakılabilir.

Bu `parms` işlevlerdeki giriş parametresi boş bir sözlüktür ve gelecekte kullanılmak üzere ayrılmıştır.

### <a name="iscpbolt"></a>Iscpsürgüsü
Iscpcıvata, işlemsel olmayan bir sürgüdeki arabirimdir.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Yeni demet kullanılabilir olduğunda, `Execute()` işlevi işlemek için çağrılır.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout, işlemsel Spout için arabirimidir.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

İşlem `NextTx()`dışı sayaç `Ack()`olmayan,,, ve `Fail()` , C\# işleminde tek bir iş parçacığında sıkı bir döngüde çağırılır. Görüntülenecek veri olmadığında, `NextTx` çok fazla CPU için harcanmayacak şekilde kısa bir süre (10 milisaniye) için uyku moduna geçecek.

`NextTx()`Yeni bir işlem başlatmak için çağrılır, ve `seqId` `Fail()`içinde `Ack()` de kullanılan işlemi tanımlamak için out parametresi kullanılır. ' `NextTx()`De, Kullanıcı verileri Java tarafına yayabilir. Veriler, yeniden yürütmeyi desteklemek için ZooKeeper içinde depolanır. ZooKeeper kapasitesi sınırlı olduğundan, Kullanıcı işlem biriktirme listesinden toplu verileri değil yalnızca meta verileri yaymalıdır.

Fırtınası başarısız olursa bir işlemi otomatik olarak yeniden dener, bu `Fail()` nedenle normal durumda çağrılmamalıdır. Ancak SCP, işlemsel Spout tarafından yayılan meta verileri denet, meta veriler geçersiz olduğunda `Fail()` çağırabilir.

Bu `parms` işlevlerdeki giriş parametresi boş bir sözlüktür ve gelecekte kullanılmak üzere ayrılmıştır.

### <a name="iscpbatchbolt"></a>Iscpbatchcıvata
Iscpbatchcıvata, işlemsel bir rulonun arabirimidir.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()`, cıvata yeni bir tanımlama grubu bulunduğunda çağrılır. `FinishBatch()`Bu işlem bittiğinde çağrılır. `parms` Giriş parametresi gelecekte kullanılmak üzere ayrılmıştır.

İşlemsel topoloji için önemli bir kavram `StormTxAttempt`vardır:. İki alanı `TxId` vardır ve `AttemptId`. `TxId`belirli bir işlemi tanımlamak için kullanılır ve belirli bir işlem için işlem başarısız olursa ve yeniden yürütülemezse birden çok girişim olabilir. SCP.net, her birini işlemek için Java 'daki her `StormTxAttempt`ne gibi yeni bir ıscpbatchcıvam nesnesi oluşturur. Bu tasarımın amacı paralel işlem işlemeyi destekliyoruz. Kullanıcı, işlem denemesinin tamamlanıyorsa, karşılık gelen ıscpbatchcıvam nesnesinin yok edildiğini ve atık olarak toplandığını göz önünde bulundurmanız gerekir.

## <a name="object-model"></a>Nesne modeli
SCP.NET ayrıca geliştiricilerin ile programlama için basit bir temel nesne kümesi sağlar. Bunlar **bağlamlardır**, **Statestore**ve **SCPRuntime**. Bunlar, bu bölümün Rest bölümünde ele alınmıştır.

### <a name="context"></a>Bağlam
Bağlam, uygulamaya çalışan bir ortam sağlar. Her bir SCC (ıscpspout/Iscpsürgüsü/ıscptxspout/Iscpbatchcıvatsu) her SCC, karşılık gelen bir bağlam örneğine sahiptir. Bağlam tarafından sunulan işlevsellik iki parçaya ayrılabilir: (1) bütün C\# işleminde kullanılabilir olan statik bölüm, (2) yalnızca belirli bağlam örneği için kullanılabilen dinamik bölüm.

### <a name="static-part"></a>Statik bölüm
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger`günlük amacı için sağlanır.

`pluginType`, C\# işleminin eklenti türünü belirtmek için kullanılır. C\# işlemi yerel test modunda (Java olmadan) çalışıyorsa, eklenti türü olur `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config`Java tarafında yapılandırma parametreleri almak için sağlanır. C\# eklentisi başlatıldığında parametreler Java tarafında geçirilir. Parametreler iki bölüme ayrılır: `stormConf` ve `pluginConf`. `Config`

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf`Parametreler, fırtınası tarafından tanımlanır ve `pluginConf` SCP tarafından tanımlanan parametrelerdir. Örneğin:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext`, topoloji bağlamını almak için sağlanır, en çok paralellik olan bileşenler için kullanışlıdır. Örnek aşağıda verilmiştir:

    //demo how to get TopologyContext info
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)                      
    {
        Context.Logger.Info("TopologyContext info:");
        TopologyContext topologyContext = Context.TopologyContext;                    
        Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());          
        taskIndex = topologyContext.GetThisTaskIndex();
        Context.Logger.Info("taskIndex: {0}", taskIndex);
        string componentId = topologyContext.GetThisComponentId();                    
        Context.Logger.Info("componentId: {0}", componentId);
        List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
        Context.Logger.Info("taskNum: {0}", componentTasks.Count);                    
    }

### <a name="dynamic-part"></a>Dinamik bölüm
Aşağıdaki arabirimler belirli bir bağlam örneği ile ilgili. Bağlam örneği SCP.NET platformu tarafından oluşturulur ve Kullanıcı koduna geçirilir:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

İşlemsel olmayan Spout destekleme ACK için aşağıdaki yöntem sağlanır:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

ACK 'i destekleyen işlem dışı bir sürgülü için, açıkça `Ack()` veya `Fail()` aldığı tanımlama grubu gerekir. Ayrıca, yeni kayıt düzeni yayırken yeni demet 'in tutturucularını da belirtmelidir. Aşağıdaki yöntemler verilmiştir.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore`meta veri Hizmetleri, monoton dizisi oluşturma ve ücretsiz düzenleme koordinasyonunu sağlar. Dağıtılmış kilitler, dağıtılmış kuyruklar, engelleri ve işlem hizmetleri dahil `StateStore`olmak üzere daha yüksek düzeyde dağıtılmış eşzamanlılık soyutlamaları oluşturulabilir.

SCP uygulamaları, özellikle de `State` işlemsel topoloji için [Apache ZooKeeper](https://zookeeper.apache.org/)bazı bilgileri kalıcı hale getirmek için nesnesini kullanabilir. Böylece, işlem biriktirme listesinden Kilitlenmeler ve yeniden başlatırsanız, ZooKeeper adresinden gerekli bilgileri alabilir ve ardışık düzeni yeniden başlatabilir.

`StateStore` Nesnesi genellikle şu yöntemlere sahiptir:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

`State` Nesnesi genellikle şu yöntemlere sahiptir:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

`Commit()` Yöntemi için, simplemode true olarak ayarlandığında ZooKeeper içinde karşılık gelen znode silinir. Aksi takdirde, geçerli znode öğesini siler ve yürütülen\_yola yeni bir düğüm ekler.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime aşağıdaki iki yöntemi sağlar:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()`SCP çalışma zamanı ortamını başlatmak için kullanılır. Bu yöntemde, C\# işlemi Java tarafına bağlanır ve yapılandırma parametrelerini ve topoloji bağlamını alır.

`LaunchPlugin()`ileti işleme döngüsünü başlatma için kullanılır. Bu döngüde, C\# eklentisi iletileri Java tarafında alır (diziler ve denetim sinyalleri dahil) ve ardından, Kullanıcı kodu tarafından sağlanacak arabirim yöntemini çağıran iletileri işler. Yöntemi `LaunchPlugin()` için giriş parametresi, ıscpspout/iscpcıvam/ıscptxspout/iscpbatchcıvatu arabirimini uygulayan bir nesne döndürebileceğinizi temsil eden bir temsilcisidir.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Iscpbatchcıvam için, ' `StormTxAttempt` den gelen `parms`ve yeniden yürütülmüş bir girişimin olup olmadığını jtik hale getirmek için kullanabilirsiniz. Yeniden yürütme denemesine yönelik denetim genellikle yürütme sürgüsü üzerinde yapılır ve `HelloWorldTx` örnekte gösterilmiştir.

Genellikle, SCP eklentileri burada iki modda çalışabilir:

1. Yerel test modu: Bu modda, SCP eklentileri (C\# Kullanıcı kodu) geliştirme aşamasında Visual Studio içinde çalışır. `LocalContext`, oluşturulan tanımlama gruplarını yerel dosyalara serileştirmek ve bunları belleğe geri okumak için yöntemi sağlayan bu modda kullanılabilir.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Normal mod: Bu modda, SCP eklentileri, fırtınası Java işlemi tarafından başlatılır.
   
    SCP eklentisi başlatma örneği aşağıda verilmiştir:
   
        namespace Scp.App.HelloWorld
        {
        public class Generator : ISCPSpout
        {
            … …
            public static Generator Get(Context ctx, Dictionary<string, Object> parms)
            {
            return new Generator(ctx);
            }
        }
   
        class HelloWorld
        {
            static void Main(string[] args)
            {
            /* Setting the environment variable here can change the log file name */
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");
   
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
            }
        }
        }

## <a name="topology-specification-language"></a>Topoloji belirtim dili
SCP topolojisi belirtimi, SCP topolojilerini tanımlamak ve yapılandırmak için alana özgü bir dildir. Bu, fırtınası 'nın Clojure DSL 'yi (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) temel alır ve SCP tarafından genişletilir.

Topoloji belirtimleri ***runspec*** komutu aracılığıyla doğrudan fırtınası kümesine gönderilebilir.

SCP.NET, Işlem topolojilerini tanımlamak için aşağıdaki işlevleri ekledi:

| **Yeni Işlevler** | **Parametreler** | **Açıklama** |
| --- | --- | --- |
| **TX-topolopy** |topoloji-adı<br />Spout eşleme<br />cıvata eşleme |Topoloji adı, &nbsp;Spout tanım eşlemesi ve cıvatalar tanım eşlemesi ile bir işlem topolojisi tanımlayın |
| **SCP-TX-Spout** |exec-adı<br />args<br />alanını |İşlem biriktirme listesini tanımlayın. Uygulamayı, ***bağımsız değişkenleri***kullanarak ***Exec-Name*** ile çalıştırır.<br /><br />***Alanlar*** , Spout Için çıkış alanlarıdır |
| **SCP-TX-Batch-sürgüsü** |exec-adı<br />args<br />alanını |İşlem Batch sürgüsü tanımlayın. Uygulamayı, ***bağımsız değişkenleri*** kullanarak ***Exec-Name*** ile çalıştırır.<br /><br />Alanlar, cıvata çıkış alanlarıdır. |
| **SCP-TX-COMMIT-cıvata** |exec-adı<br />args<br />alanını |İşlemsel bir işleme sürgüsü tanımlayın. Uygulamayı, ***bağımsız değişkenleri***kullanarak ***Exec-Name*** ile çalıştırır.<br /><br />***Alanlar*** , cıvata çıkış alanlarıdır |
| **nontx-topoly** |topoloji-adı<br />Spout eşleme<br />cıvata eşleme |Topoloji adı,&nbsp; Spout tanım eşlemesi ve cıvatalar tanım eşlemesi ile işlem dışı bir topoloji tanımlayın |
| **SCP-Spout** |exec-adı<br />args<br />alanını<br />parameters |İşlem dışı bir Spout tanımlayın. Uygulamayı, ***bağımsız değişkenleri***kullanarak ***Exec-Name*** ile çalıştırır.<br /><br />***Alanlar*** , Spout Için çıkış alanlarıdır<br /><br />***Parametreler*** , "işlem dışı. ACK. Enabled" gibi bazı parametreleri belirtmek için kullanılarak isteğe bağlıdır. |
| **SCP-sürgüsü** |exec-adı<br />args<br />alanını<br />parameters |İşlem dışı bir sürgüsü tanımlayın. Uygulamayı, ***bağımsız değişkenleri***kullanarak ***Exec-Name*** ile çalıştırır.<br /><br />***Alanlar*** , cıvata çıkış alanlarıdır<br /><br />***Parametreler*** , "işlem dışı. ACK. Enabled" gibi bazı parametreleri belirtmek için kullanılarak isteğe bağlıdır. |

SCP.NET aşağıdaki anahtar kelimeleri tanımlanmış:

| **anahtar sözcükler** | **Açıklama** |
| --- | --- |
| **: ad** |Topoloji adını tanımlayın |
| **: topoloji** |Önceki işlevleri kullanarak topolojiyi tanımlayın ve içinde derleme yapın. |
| **:p** |Her Spout veya cıvata paralellik ipucunu tanımlayın. |
| **: yapılandırma** |Yapılandırma parametresini tanımlama veya var olanları güncelleştirme |
| **: şema** |Akışın şemasını tanımlayın. |

Ve sık kullanılan parametreler:

| **Parametre** | **Açıklama** |
| --- | --- |
| **"plugin.name"** |C# eklentinin exe dosya adı |
| **"Plugin. args"** |eklenti bağımsız değişkenleri |
| **"output. Schema"** |Çıktı şeması |
| **"işlem dışı. ACK. Enabled"** |Etkin olmayan topoloji için ACK 'in etkinleştirilip etkinleştirilmediği |

Runspec komutu bitler ile birlikte dağıtılır, kullanım şöyle olur:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

***Resource-dir*** parametresi isteğe bağlıdır, bir C\# uygulaması eklemek istediğinizde ve bu dizin uygulamayı, bağımlılıkları ve konfigürasyonları içerdiğinde belirtmeniz gerekir.

***Sınıfyolu*** parametresi de isteğe bağlıdır. Spec dosyası Java Spout veya Cıvatiçeriyorsa Java sınıfyolunu belirtmek için kullanılır.

## <a name="miscellaneous-features"></a>Çeşitli özellikler
### <a name="input-and-output-schema-declaration"></a>Giriş ve çıkış şeması bildirimi
Kullanıcılar, C\# işlemlerinde tanımlama gruplarını yayabilir, platformun tanımlama grubunu Byte [], Java tarafında aktarma ve fırtınası bu kayıt grubunu hedeflere aktarabilmesi gerekir. Aşağı akış bileşenlerinde, C\# işlemleri Java tarafından geri tanımlama gruplarını alır ve platforma göre orijinal türlere dönüştürür, tüm bu işlemler platform tarafından gizlenir.

Serileştirme ve seri durumdan çıkarma desteklemek için kullanıcı kodunun, giriş ve çıkış şemasını bildirmesi gerekir.

Giriş/çıkış akışı şeması bir sözlük olarak tanımlanır. Anahtar, Streamıd 'dir. Değer, sütunun türleridir. Bileşenin birden çok akışı tanımlanmış olabilir.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }


Bağlam nesnesinde aşağıdaki API eklendi:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Geliştiriciler, tanımlama gruplarının, bu akış için tanımlanan şemayı uyardığından emin olmalıdır, aksi takdirde sistem bir çalışma zamanı özel durumu oluşturur.

### <a name="multi-stream-support"></a>Çoklu akış desteği
SCP, aynı anda birden çok farklı akışı yayma veya alma için Kullanıcı kodunu destekler. , Yayma yöntemi isteğe bağlı bir akış KIMLIĞI parametresi alırsa, bağlam nesnesinde destek yansıtır.

SCP.NET Context nesnesindeki iki yöntem eklenmiştir. Bunlar, Streamıd belirtmek için tanımlama grubu veya tanımlama grupları oluşturmak için kullanılır. Streamıd bir dizedir ve hem C\# 'de hem de topoloji tanımı belirtiminde tutarlı olması gerekir.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Mevcut olmayan bir akışa yayma, çalışma zamanı özel durumlarına neden olur.

### <a name="fields-grouping"></a>Alanları gruplama
Fırtınası içinde yerleşik alanlar gruplandırmada SCP.NET içinde düzgün çalışmıyor. Java proxy tarafında, tüm alanlar veri türleri gerçekte Byte [] ve alanlar gruplandırmada, gruplandırmayı gerçekleştirmek için Byte [] nesne karma kodu kullanılır. Byte [] nesnesi karma kodu, bu nesnenin bellekteki adresidir. Bu nedenle, aynı içeriğe sahip ancak aynı adresi içermeyen iki Byte [] nesnesi için gruplama yanlış olur.

SCP.NET, özelleştirilmiş bir gruplama yöntemi ekler ve gruplandırmayı yapmak için Byte [] içeriğini kullanır. **Spec** dosyasında söz dizimi şöyle olur:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Şöyledir

1. "SCP-alan-grubu", "SCP tarafından uygulanan özelleştirilmiş alan gruplaması" anlamına gelir.
2. ": TX" veya ": TX olmayan", işlem topolojisi ise anlamına gelir. Başlangıç dizini TX ve TX olmayan topolojilerde farklı olduğundan bu bilgilere ihtiyacımız var.
3. [0, 1], 0 ' dan başlayan bir alan kimliği karma kümesi anlamına gelir.

### <a name="hybrid-topology"></a>Karma topolojisi
Yerel fırtınası Java 'da yazılmıştır. Ve SCP.net, c\# geliştiricilerinin iş mantığını işlemek üzere c\# kodu yazmasını sağlamak için BT 'yi geliştirmiştir. Ancak aynı zamanda yalnızca C\# Spoler/cıvatları değil, Java Spout/cıvatları içeren karma topolojileri da destekler.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Spec dosyasında Java Spout/Ruli belirtme
Spec dosyasında, Java spotları ve cıvataları belirtmek için "SCP-Spout" ve "SCP-sürgüsü" de kullanılabilir, örneğin:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Java `microsoft.scp.example.HybridTopology.Generator` Spout sınıfının adı aşağıda verilmiştir.

### <a name="specify-java-classpath-in-runspec-command"></a>RunSpec komutunda Java sınıfyolunu belirtme
Java spotları veya cıvataları içeren topoloji göndermek istiyorsanız, öncelikle Java spotları veya cıvataları derleyip jar dosyalarını almanız gerekir. Daha sonra, topoloji gönderirken jar dosyalarını içeren Java sınıf yolunu belirtmeniz gerekir. Örnek aşağıda verilmiştir:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Burada **hybridtopology\\Java\\hedefi\\ , Java Spout/cıvata jar dosyasını içeren klasördür.\\**

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java ve C arasında serileştirme ve seri durumdan çıkarma\#
SCP bileşeni, Java yan ve C\# tarafını içerir. Yerel Java spotları/cıvataları ile etkileşime geçmek için serileştirme/seri durumdan çıkarma, aşağıdaki grafikte gösterildiği gibi Java yan ve C\# tarafı arasında yürütülmesi gerekir.

![Java bileşeni ' ne Gönderen Java bileşeni diyagramı Java bileşenine gönderiliyor](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **C\# tarafında Java tarafında serileştirme ve seri durumdan çıkarma**
   
   İlk olarak, C\# tarafında Java tarafında serileştirme ve seri durumdan çıkarma için varsayılan uygulama sağlıyoruz. Java tarafında serileştirme yöntemi SPEC dosyasında belirtilebilir:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   C\# tarafında seri kaldırma yöntemi c\# Kullanıcı kodunda belirtilmelidir:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Bu varsayılan uygulama, veri türü çok karmaşık bir şekilde sağlanmış olan çoğu durumu işlemelidir. Bazı durumlarda, Kullanıcı veri türü çok karmaşık olduğundan ya da varsayılan uygulamamız performansı kullanıcının gereksinimini karşılamadığından, kullanıcılar kendi uygulamasını düzenleyebilir.
   
   Java tarafında serileştirme arabirimi şu şekilde tanımlanır:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   C\# tarafındaki seri kaldırma arabirimi şöyle tanımlanır:
   
   ortak arabirim ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Java tarafında C\# tarafında serileştirme ve seri durumdan çıkarma**
   
   C\# tarafında serileştirme yöntemi c\# Kullanıcı kodunda belirtilmelidir:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Java tarafında seri kaldırma yöntemi SPEC dosyasında belirtilmelidir:
   
     (SCP-Spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Burada "Microsoft. SCP. fırtınası. Multilang. CustomizedInteropJSONDeserializer", seri hale getiricinin adıdır ve "Microsoft. SCP. example. Hybridtopoloji. Person", verilerin Serisi kaldırılan hedef sınıftır.
   
   Kullanıcı aynı zamanda kendi C\# serileştirici ve Java seri hale getirici uygulamasını da ekleyebilir. Bu kod, C\# seri hale getirici için arabirimidir:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Bu kod, Java seri hale getiricisi için arabirimidir:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>SCP ana bilgisayar modu
Bu modda, Kullanıcı kodlarını DLL 'ye derleyebilir ve topolojiyi göndermek için SCP tarafından sunulan SCPHost. exe ' yi kullanabilir. Spec dosyası şu kod gibi görünür:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Burada, `plugin.name` SCP SDK 'sı `SCPHost.exe` tarafından sağlandığı şekilde belirtilmiştir. SCPHost. exe üç parametre kabul eder:

1. Birincisi, bu örnekte olan DLL adıdır `"HelloWorld.dll"` .
2. İkinci tane, bu örnekte olan sınıf adıdır `"Scp.App.HelloWorld.Generator"` .
3. Üçüncü tane, bir genel statik yöntemin adıdır ve bu, SCC 'in bir örneğini almak için çağrılabilir.

Ana bilgisayar modunda, Kullanıcı kodu DLL olarak derlenir ve SCP platformu tarafından çağrılır. Bu nedenle SCP platformu tüm işleme mantığının tam denetimini alabilir. Bu nedenle, geliştirme deneyimini basitleştirecek ve daha sonraki sürüm için daha fazla esneklik ve daha iyi uyumluluk ve daha iyi uyumluluk ve daha iyi bir uyumluluk sunabilmesi için müşterilerimizin SCP ana bilgisayar modunda topoloji göndermesini öneririz

## <a name="scp-programming-examples"></a>SCP programlama örnekleri
### <a name="helloworld"></a>HelloWorld
**HelloWorld** , SCP.net 'in gösterilmesi için basit bir örnektir. **Oluşturucu**adlı bir Spout ve **Splitter** ve **sayaç**adlı iki cıvatiyle birlikte işlemsel olmayan bir topoloji kullanır. Spout **Oluşturucu** rastgele cümleler oluşturur ve bu cümleleri **bölümlendiriciye**yayar. Cıvata **Bölümlendirici** , bu sözcükleri sözcüklere eklemek ve **sayaç** sürgüsü halinde göstermek için cümleleri böler. "Counter" sürgüsü, her sözcüğün oluşum numarasını kaydetmek için bir sözlük kullanır.

Bu örnek için **HelloWorld. spec** ve **\_HelloWorld enableack. spec** olmak üzere iki teknik dosya vardır. C\# kodunda, Java tarafında pluginconf dosyasını alarak ACK 'in etkinleştirilip etkinleştirilmeyeceğini bulabilir.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Spout, ACK etkinleştirilirse, kabul edilmedi olan tanımlama gruplarını önbelleğe almak için bir sözlük kullanılır. Başarısız olursa, başarısız kayıt düzeni yeniden yürütülür:

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        Context.Logger.Info("Fail, seqId: {0}", seqId);
        if (cachedTuples.ContainsKey(seqId))
        {
            /* get the cached tuple */
            string sentence = cachedTuples[seqId];

            /* replay the failed tuple */
            Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
        }
        else
        {
            Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
        }
    }

### <a name="helloworldtx"></a>Merhaba Worldtx
**Merhaba Worldtx** örneği, işlem topolojisinin nasıl uygulanacağını gösterir. **Oluşturucu**adlı bir Spout, **kısmi sayı**olarak adlandırılan bir yığın işareti ve **Count-Sum**adlı bir işleme işareti vardır. Ayrıca, önceden oluşturulmuş üç txt dosyası da vardır: **DataSource0. txt**, **DataSource1. txt**ve **DataSource2. txt**.

Her harekette, Spout **Oluşturucu** önceden oluşturulmuş üç dosyadan iki dosya seçer ve iki dosya adını **kısmi sayı** sürgüüne yayar. Cıvata **kısmi sayımı** , alınan tanımlama grubundan dosya adını alır, sonra dosyayı açıp bu dosyadaki sözcüklerin sayısını sayar ve son olarak, sözcük numarasını sayı **toplamı deyana** yayar. **Count-Sum** sürgüsü toplam sayıyı özetler.

**Tam bir kez** semantik bir şekilde ulaşmak için, COMMIT cıvatcount **sayısı-toplamın** yeniden yürütülmüş bir işlem olup olmadığını jkararmalıdır. Bu örnekte, bir statik üye değişkenine sahiptir:

    public static long lastCommittedTxId = -1; 

Bir ıscpbatchcıvam örneği oluşturulduğunda, bu, `txAttempt` from giriş parametrelerini alır:

    public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
    {
        /* for transactional topology, we can get txAttempt from the input parms */
        if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
        {
            StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
            return new CountSum(ctx, txAttempt);
        }
        else
        {
            throw new Exception("null txAttempt");
        }
    }

`FinishBatch()` Çağrıldığında`lastCommittedTxId` , yeniden yürütülmüş bir işlem değilse güncelleştirilir.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
Bu topoloji bir Java Spout ve C\# sürgüsü içerir. SCP platformu tarafından sunulan varsayılan serileştirme ve seri kaldırma uygulamasını kullanır. Spec dosya ayrıntıları ve Java sınıfyolunu belirtme için **submittopology. bat** adlı **örneklerde\\** **hybridtopology. spec** örneklerine bakın.

### <a name="scphostdemo"></a>SCPHostDemo
Bu örnek, özde HelloWorld ile aynıdır. Tek fark, kullanıcı kodunun DLL olarak derlenmesi ve topoloji SCPHost. exe kullanılarak gönderilir. Daha ayrıntılı açıklama için "SCP ana bilgisayar modu" bölümüne bakın.

## <a name="next-steps"></a>Sonraki Adımlar
SCP kullanılarak oluşturulan Apache Storm topolojileri örnekleri için aşağıdaki belgelere bakın:

* [Visual C# Studio kullanarak hdınsight 'ta Apache Storm için topolojiler geliştirin](apache-storm-develop-csharp-visual-studio-topology.md)
* [HDInsight üzerinde Apache Storm Azure Event Hubs olayları işleme](apache-storm-develop-csharp-event-hub-topology.md)
* [HDInsight üzerinde Apache Storm kullanarak Event Hubs araç algılayıcısı verilerini işleme](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Azure Event Hubs Apache HBase 'e ayıklama, dönüştürme ve yükleme (ETL)](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)

---
title: Azure HDInsight 'ta fırtınası için SCP.NET Programlama Kılavuzu
description: Oluşturmak için SCP.NET kullanmayı öğrenin. Azure HDInsight 'ta çalışan fırtınası ile kullanım için NET tabanlı fırtınası topolojileri.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: c993b3f70f609fb79c51ba9be08fa3d5dc7e8317
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864117"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Storm için SCP Programlama Kılavuzu

SCP gerçek zamanlı, güvenilir, tutarlı ve yüksek performanslı veri işleme uygulamaları oluşturmaya yönelik bir platformdur. Açık kaynaklı yazılım toplulukları tarafından tasarlanan akış işleme sistemi olan [Apache Storm](https://storm.incubator.apache.org/)üzerine kurulmuştur. Nathan Marz oluşturuldu fırtınası. Twitter tarafından açık kaynak olarak yayımlandı. Fırtınası, son derece güvenilir dağıtılmış koordinasyon ve durum yönetimine izin veren başka bir Apache projesi olan [Apache ZooKeeper](https://zookeeper.apache.org/)kullanır.

SCP projesi Windows üzerinde yalnızca bir bağlantı noktası ve ayrıca proje tarafından eklenen uzantıları ve özelleştirmeleri Windows ortamına değil. Uzantılar .NET Geliştirici deneyimini ve .NET kitaplıklarını içerir. Özelleştirme, Windows tabanlı dağıtımı içerir.

Uzantılar ve özelleştirme ile açık kaynaklı yazılım projelerini çatalla ihtiyacınız yoktur. Fırtınası üzerinde oluşturulmuş türetilmiş ortamları kullanabilirsiniz.

## <a name="processing-model"></a>İşlem modeli

SCP 'deki veriler, tanımlama gruplarının sürekli akışları olarak modellenir. Genellikle, tanımlama grupları:

1. Bir sıraya akar.
1. , Bir fırtınası topolojisi içinde barındırılan iş mantığına göre alınır ve dönüştürülür.
1. Çıkışları, başka bir SCP sistemine yönelik tanımlama grupları olarak veya dağıtılmış dosya sistemleri ve SQL Server gibi veritabanları gibi mağazalara kararlıdır.

:::image type="content" source="./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png" alt-text="Bir kuyruğun bir diyagramı, verileri işlenecek şekilde beslemenin yanı sıra bir veri deposunun beslemelerini sağlar" border="false":::

Fırtınası ' da, bir uygulama topolojisi bir hesaplama grafiğini tanımlar. Bir topolojideki her düğüm, işlem mantığını içerir. Düğümler arasındaki bağlantılar veri akışını gösterir.

Topolojiye giriş verisi eklenen düğümlere _Spout_ adı verilir. Bunları verileri sıralamak için kullanabilirsiniz. Giriş verileri, dosya günlükleri, bir işlem veritabanı veya sistem performans sayacı gibi bir kaynaktan gelebilir.

Hem giriş hem de çıkış verisi akışları olan düğümlere _cıvatalar_ denir. Gerçek veri filtreleme, seçimler ve toplama işlemlerini yapar.

SCP, en az bir kez ve tam bir kez veri işleme olmak üzere en iyi çabaları destekler.

Dağıtılmış bir akış işleme uygulamasında, veri işleme sırasında hatalar meydana gelebilir. Bu hatalar, bir ağ kesintisi, bir makine arızası veya kodunuzda bir hata içerir. En az bir kez işleme, bir hata oluştuğunda aynı verileri otomatik olarak yeniden alarak tüm verilerin en az bir kez işlenmesini sağlar.

En az bir kez işleme basit ve güvenilirdir ve birçok uygulama için tasarlanmıştır. Ancak bir uygulama tam sayım gerektirdiğinde, aynı veriler uygulama topolojisinde yeniden çalınabileceğinden, en az bir kez işleme yeterli değildir. Bu durumda, tam olarak bir kez işleme, veriler yeniden çalındığında ve birden çok kez işlendiğinde bile sonucun doğru olduğundan emin olur.

SCP, .NET geliştiricilerinin, fırtınası ile Java Sanal Makinesi (JVM) kullanırken gerçek zamanlı veri işleme uygulamaları oluşturmalarına olanak tanır. TCP yerel yuvaları aracılığıyla bir JVM ve .NET iletişim kurar. Her Spout/cıvata, Kullanıcı mantığının bir .NET işleminde eklenti olarak çalıştırıldığı .NET/Java işlem çiftidir.

SCP 'nin üstünde bir veri işleme uygulaması oluşturmak için aşağıdaki adımları izleyin:

1. Kuyruklardan verileri çekmek için biriktirmalar tasarlayın ve uygulayın.
1. Giriş verilerini işleyen ve veritabanı gibi dış depolara kaydedecek cıvatları tasarlayın ve uygulayın.
1. Topolojiyi tasarlayın, sonra, oluşturup çalıştırın.

Topoloji, izdüşümlerinin yanı sıra aralarında akan verileri tanımlar. SCP bir topoloji belirtimi alır ve bu kümeyi her bir köşenin tek bir mantıksal düğümde çalıştığı bir fırtınası kümesine dağıtır. Fırtınası Görev Zamanlayıcı, yük devretme ve ölçeklendirmeyi üstlenir.

Bu makalede, SCP ile veri işleme uygulamaları oluşturma konusunda yol göstermek için bazı basit örnekler kullanılmaktadır.

## <a name="scp-plug-in-interface"></a>SCP eklenti arabirimi

SCP eklentileri tek başına uygulamalardır. Geliştirme sırasında Visual Studio içinde çalışabilir ve üretim dağıtımından sonra fırtınası ardışık düzenine takılır.

SCP eklentisi yazmak, diğer Windows konsol uygulamalarının yazılmasında de aynıdır. SCP.NET platformu, Spout/cıvam için bazı arabirimler bildirir. Eklenti kodunuz bu arabirimleri uygular. Bu tasarımın ana amacı, SCP.NET platformunun diğer şeyleri işlemesini sağlayarak iş mantığınıza odaklanmanıza olanak sağlamaktır.

Eklenti kodunuz aşağıdaki arabirimlerden birini uygular. Hangi arabirim, topolojinin işlem veya işlem dışı olmasına ve bileşenin bir Spout ya da bir cıvata olup olmamasına bağlıdır.

* **ISCPSpout**
* **Iscpsürgüsü**
* **ISCPTxSpout**
* **Iscpbatchcıvata**

### <a name="iscpplugin"></a>SCC

**SCC** , birçok eklenti için ortak arabirimdir. Şu anda bu bir kukla arabirimdir.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**Icpspout** , işlem dışı bir Spout arabirimidir.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

**Nexttuple** çağrıldığında, C# kodunuz bir veya daha fazla tanımlama grubu yayabilir. Yayma için bir şey yoksa, bu yöntem hiçbir şeyi yaymadan döndürmelidir.

**Nexttuple**, **ACK** ve **Fail** yöntemlerinin hepsi, C# işleminin tek bir iş parçacığında sıkı bir döngüde çağırılır. Görüntülenecek bir tanımlama grubu olmadığında, 10 milisaniyeye benzer bir süre için **Nexttuple** uyumaya sahip olması gerekir. Bu uyku, CPU kullanılabilirliğini gereksiz şekilde önlemenize yardımcı olur.

**ACK** ve **Fail** yöntemleri yalnızca bir belirtim dosyası bildirim mekanizmasını etkinleştirmesine izin verecek şekilde çağrılır. *SeqID* parametresi, onaylanan veya başarısız olan kayıt grubunu tanımlar. Bildirim, işlem dışı bir topolojide etkinleştirilirse, aşağıdaki **yayma** işlevi bir Spout içinde kullanılmalıdır:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

İşlem dışı bir topoloji onay desteklemiyorsa, **ACK** ve **Fail** boş işlevler olarak kalabilir.

Bu işlevlerdeki *parametreleri* giriş parametresi boş bir sözlük belirtir ve gelecekte kullanılmak üzere ayrılmıştır.

### <a name="iscpbolt"></a>Iscpsürgüsü

**Iscpcıvata** , işlem dışı bir sürgüdeki arabirimdir.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Yeni bir tanımlama grubu kullanılabilir olduğunda, **yürütme** işlevi onu işlemek için çağırılır.

### <a name="iscptxspout"></a>ISCPTxSpout

**Iscptxspout** , işlem için bir Spout arabirimidir.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Yalnızca işlem dışı ortaklarınıza benzer şekilde, **Nexttx**, **ACK** ve **Fail** , C# işleminin tek bir iş parçacığında sıkı bir döngüde çağırılır. Görüntülenecek bir tanımlama grubu olmadığında, 10 milisaniyeye benzer bir süre için **Nexttx** uyumaya sahip olması gerekir. Bu uyku, CPU kullanılabilirliğini gereksiz şekilde önlemenize yardımcı olur.

Yeni bir işlem başlatmak için **Nexttx** çağrıldığında, *SeqID* çıkış parametresi işlemi tanımlar. İşlem **ACK** ve **Fail** içinde de kullanılır. **Nexttx** yönteminiz, Java tarafında veri yayabilir. Veriler, yeniden yürütmeyi desteklemek için ZooKeeper içinde depolanır. ZooKeeper sınırlı kapasiteye sahip olduğundan, kodunuz yalnızca bir işlem biriktirme dosyasında toplu verileri değil, yalnızca meta verileri göstermelidir.

Fırtınası başarısız bir işlemi otomatik olarak yeniden yürüttüğünde, **başarısızlık** genellikle çağrılmaz. Ancak SCP bir işlem biriktirme tarafından oluşturulan meta verileri denet, meta veriler geçersiz olduğunda **başarısız** olabilir.

Bu işlevlerdeki *parametreleri* giriş parametresi boş bir sözlük belirtir ve gelecekte kullanılmak üzere ayrılmıştır.

### <a name="iscpbatchbolt"></a>Iscpbatchcıvata

**Iscpbatchcıvata** bir işlem sürgüsü arabirimidir.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

**Execute** yöntemi, yeni bir tanımlama grubu, cıvata ulaştığında çağrılır. Bu işlem sona erdiğinde, **Sonlandırhbatch** yöntemi çağrılır. *Parametreleri* giriş parametresi gelecekte kullanılmak üzere ayrılmıştır.

Bir işlem topolojisi için **Stormtxdenemesi** önemli bir sınıftır. İki üye vardır: **Txıd** ve **AttemptId**. **Txıd** üyesi belirli bir işlemi tanımlar. İşlem başarısız olursa ve yeniden yürütülemezse bir işlem birden çok kez denenmeyebilir.

SCP.NET, her **Stormtxgirişim** nesnesini işlemek Için, Java 'daki her bir TBU nesnesi gibi yeni bir **ıscpbatchcıvam** nesnesi oluşturur. Bu tasarımın amacı paralel işlem işlemeyi destekliyoruz. İşlem girişiminden sonra karşılık gelen **ıscpbatchcıvam** nesnesi yok edilir ve atık olarak toplanır.

## <a name="object-model"></a>Nesne modeli

SCP.NET ayrıca geliştiricilerin ile programlama için basit bir temel nesne kümesi sağlar. Nesneler **bağlam**, **Statestore** ve **SCPRuntime**. Bunlar bu bölümde ele alınmıştır.

### <a name="context"></a>Bağlam

**Bağlam** nesnesi, bir uygulamaya çalışan bir ortam sağlar. **Icpspout**, **iscpcıvam**, **iscptxspout** veya **ıscpbatchcıvatinin** her bir **SCC** örneği karşılık gelen bir **bağlam** örneğine sahiptir. **Bağlam** tarafından sunulan işlevsellik, bu iki parçaya ayrılmıştır:

* Tüm C# işleminde bulunan statik bölüm
* Yalnızca belirli **bağlam** örneği için kullanılabilen dinamik bölüm

### <a name="static-part"></a>Statik bölüm

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

Günlüğe kaydetme amacıyla **günlükçü** nesnesi sağlanır.

**PluginType** nesnesi, C# işleminin eklenti türünü gösterir. İşlem Java olmadan yerel test modunda çalışıyorsa, eklenti türü **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

**Config** özelliği, Java tarafında yapılandırma parametrelerini alır, bu, C# eklentisi başlatıldığında bunları geçirir. **Yapılandırma** parametreleri iki parçaya ayrılmıştır: **stormconf** ve **pluginconf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**Stormconf** bölümü, fırtınası tarafından tanımlanan parametrelerdir ve **PLUGINCONF** bölümü SCP tarafından tanımlanan parametrelerdir. Aşağıda bir örnek verilmiştir:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

**Topologyıcontext** türü topoloji bağlamını alır. Birden çok paralel bileşen için en yararlı seçenektir. Aşağıda bir örnek verilmiştir:

```csharp
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
```

### <a name="dynamic-part"></a>Dinamik bölüm

Aşağıdaki arabirimler, SCP.NET platformu tarafından oluşturulan ve kodunuza geçilen belirli bir **bağlam** örneği ile ilgili olarak belirlenir:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Bildirimi destekleyen bir işlem dışı Spout için aşağıdaki yöntem verilmiştir:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Bildirimi destekleyen bir işlem dışı bir sürgülü **ACK** çağrısı veya aldığı kayıt düzeni ile **başarısız** olması gerekir. Yeni bir tanımlama grubu yayırken, rulı Ayrıca demet 'in tutturucularını belirtmelidir. Aşağıdaki yöntemler verilmiştir:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**Statestore** nesnesi meta veri Hizmetleri, monoton dizisi oluşturma ve ücretsiz düzenleme koordinasyonunu sağlar. **Statestore**'da daha yüksek düzeyde dağıtılmış eşzamanlılık soyutlamaları oluşturabilirsiniz. Bu soyutlamalar dağıtılmış kilitleri, dağıtılmış kuyrukları, engelleri ve işlem hizmetlerini içerir.

SCP uygulamaları, [Apache ZooKeeper](https://zookeeper.apache.org/)bilgileri seri hale getirmek için **durum** nesnesini kullanabilir. Bu özellik özellikle bir işlem topolojisi için değerlidir. Bir işlem biriktirme ve yeniden başlatma durdurulduğunda, **durum** ZooKeeper adresinden gerekli bilgileri alabilir ve ardışık düzeni yeniden başlatabilir.

**Statestore** nesnesi şu asıl yöntemlere sahiptir:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

**Durum** nesnesi şu asıl yöntemlere sahiptir:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

**Simplemode** **true** olarak ayarlandığında, **COMMIT** yöntemi ZooKeeper içinde karşılık gelen znode 'u siler. Aksi takdirde, yöntemi geçerli ZNode 'u siler ve KAYDEDILMIŞ yola yeni bir düğüm ekler \_ .

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime** sınıfı aşağıdaki iki yöntemi sağlar:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**Initialize** yöntemi SCP çalışma zamanı ortamını başlatır. Bu yöntemde, C# işlemi yapılandırma parametrelerini ve topoloji bağlamını almak için Java tarafına bağlanır.

**Launchplugin** yöntemi ileti işleme döngüsünü başlatır. Bu döngüde, C# eklentisi Java tarafında iletileri alır. Bu iletiler, tanımlama işaretleri ve denetim sinyalleri içerir. Daha sonra eklenti, belki kodunuzun verdiği arabirim yöntemini çağırarak iletileri işler.

**Launchplugin** için giriş parametresi bir temsilcisidir. Yöntemi, **ıscpspout**, **iscpcıvata**, **ıscptxspout** veya **ıscpbatchcıvatu** arabirimini uygulayan bir nesne döndürebilir.

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

**Iscpbatchcıvaı** için, *parametreleri* parametresinden bir **stormtxgirişim** nesnesi alabilir ve bu işlemi, denemesinin yeniden yürütülmüş bir girişimin olup olmadığını jeetmek için kullanabilirsiniz. Yeniden yürütme denemesine yönelik denetim genellikle yürütme sürgüsü üzerinden yapılır. Merhaba Worldtx örneği bu makalenin ilerleyen kısımlarında bu denetim gösterilmektedir.

SCP eklentileri genellikle iki modda çalışabilir: Yerel test modu ve normal mod.

#### <a name="local-test-mode"></a>Yerel test modu

Bu modda, C# kodunuzda bulunan SCP eklentileri geliştirme aşamasında Visual Studio içinde çalışır. Bu modda **ıkalcontext** arabirimini kullanabilirsiniz. Arabirim, yayınlanan tanımlama gruplarını yerel dosyalara serileştirmek ve bunları RAM 'e geri okumak için yöntemler sağlar.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Normal mod

Bu modda, fırtınası Java işlemi SCP eklentilerini çalıştırır. Örnek aşağıda verilmiştir:

```csharp
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
```

## <a name="topology-specification-language"></a>Topoloji belirtim dili

SCP topolojisi belirtimi, SCP topolojilerini tanımlamak ve yapılandırmak için bir etki alanına özgü dildir (DSL). Bu, [fırtınası 'Nın Clojure DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) TABANLıDıR ve SCP tarafından genişletilir.

**Runspec** komutu aracılığıyla, yürütme için topoloji belirtimlerini doğrudan bir fırtınası kümesine gönderebilirsiniz.

SCP.NET, işlem topolojilerini tanımlamak için aşağıdaki işlevleri ekledi:

| Yeni işlev | Parametreler | Description |
| --- | --- | --- |
| **TX-topolopy** |*topoloji-adı*<br />*Spout eşleme*<br />*cıvata eşleme* |Topoloji adı, Spout tanım eşlemesi ve cıvatalar tanım eşlemesi ile bir işlem topolojisini tanımlar. |
| **SCP-TX-Spout** |*exec-adı*<br />*args*<br />*alanını* |İşlem biriktirme listesini tanımlar. İşlevi, *Exec-Name* tarafından belirtilen uygulamayı çalıştırır ve *bağımsız değişkenleri* kullanır.<br /><br />*Fields* parametresi, Spout için çıkış alanlarını belirtir. |
| **SCP-TX-Batch-sürgüsü** |*exec-adı*<br />*args*<br />*alanını* |İşlem Batch sürgüsü tanımlar. İşlevi, *Exec-Name* tarafından belirtilen uygulamayı çalıştırır ve *bağımsız değişkenleri kullanır.*<br /><br />*Fields* parametresi, cıvatanın çıkış alanlarını belirtir. |
| **SCP-TX-COMMIT-cıvata** |*exec-adı*<br />*args*<br />*alanını* |İşlem bir işleme sürgüsü tanımlar. İşlevi, *Exec-Name* tarafından belirtilen uygulamayı çalıştırır ve *bağımsız değişkenleri* kullanır.<br /><br />*Fields* parametresi, cıvatanın çıkış alanlarını belirtir. |
| **TX olmayan topoloji** |*topoloji-adı*<br />*Spout eşleme*<br />*cıvata eşleme* |Topoloji adı, Spout tanım eşlemesi ve cıvatalar tanım eşlemesi ile işlem dışı bir topoloji tanımlar. |
| **SCP-Spout** |*exec-adı*<br />*args*<br />*alanını*<br />*parametrelere* |İşlem dışı bir Spout tanımlar. İşlevi, *Exec-Name* tarafından belirtilen uygulamayı çalıştırır ve *bağımsız değişkenleri* kullanır.<br /><br />*Fields* parametresi, Spout için çıkış alanlarını belirtir.<br /><br />*Parameters* parametresi isteğe bağlıdır. "İşlem dışı. ACK. Enabled" gibi parametreleri belirtmek için bunu kullanın. |
| **SCP-sürgüsü** |*exec-adı*<br />*args*<br />*alanını*<br />*parametrelere* |İşlem dışı bir sürgülü tanımlar. İşlevi, *Exec-Name* tarafından belirtilen uygulamayı çalıştırır ve *bağımsız değişkenleri* kullanır.<br /><br />*Fields* parametresi, cıvatanın çıkış alanlarını belirtir<br /><br />*Parameters* parametresi isteğe bağlıdır. "İşlem dışı. ACK. Enabled" gibi parametreleri belirtmek için bunu kullanın. |

SCP.NET aşağıdaki anahtar sözcükleri tanımlar:

| Sözcükle | Description |
| --- | --- |
| **: ad** |Topoloji adı |
| **: topoloji** |Önceki tabloda ve yerleşik işlevlerde bulunan işlevleri kullanan topoloji |
| **:p** |Her Spout veya cıvata paralellik İpucu |
| **: yapılandırma** |Parametrelerin yapılandırılacağını veya mevcut olanları güncelleştirmenize bakılmaksızın |
| **: şema** |Akışın şeması |

SCP.NET, sık kullanılan bu parametreleri de tanımlar:

| Parametre | Açıklama |
| --- | --- |
| "plugin.name" |C# eklentisinin. exe dosya adı |
| "Plugin. args" |Eklenti bağımsız değişkenleri |
| "output. Schema" |Çıktı şeması |
| "işlem dışı. ACK. Enabled" |İşlem dışı bir topoloji için onay etkinleştirilip etkinleştirilmediği |

**Runspec** komutu, BITS ile birlikte dağıtılır. Komut kullanımı aşağıda verilmiştir:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*Resource-dir* parametresi isteğe bağlıdır. C# uygulaması eklemek istediğinizde bunu belirtin. Belirtilen dizin, uygulamayı, bağımlılıkları ve konfigürasyonları içerir.

*Sınıfyolu* parametresi de isteğe bağlıdır. Belirtim dosyası bir Java Spout veya cıvatiçeriyorsa Java sınıfyolunu belirtir.

## <a name="miscellaneous-features"></a>Çeşitli özellikler

### <a name="input-and-output-schema-declarations"></a>Giriş ve çıkış şeması bildirimleri

C# işlemleriniz, tanımlama gruplarını yayabilir. Bunu yapmak için platform, tanımlama gruplarını **Byte []** nesnelerine serileştirir ve nesneleri Java tarafına aktarır. Sonra bu tanımlama gruplarını hedeflere aktarır.

Aşağı akış bileşenlerinde C# işlemi, başlıkları Java tarafında geri alır ve platformun orijinal türlerine dönüştürür. Tüm bu işlemler platform tarafından gizlenir.

Serileştirme ve seri durumdan çıkarmayı desteklemek için, kodunuzun giriş ve çıkış şemasını bildirmesi gerekir. Şema bir sözlük olarak tanımlanır. Akış KIMLIĞI sözlük anahtarıdır. Anahtar değeri, sütunların türleridir. Bir bileşen birden çok akış bildirebilir.

```csharp
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
```

Aşağıdaki işlev bir **bağlam** nesnesine eklenir:

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Geliştiriciler, oluşturulan tanımlama gruplarının bir akış için tanımlanan şemayı kullandığından emin olmalıdır. Aksi takdirde, sistem bir çalışma zamanı özel durumu oluşturur.

### <a name="multistream-support"></a>Çoklu akış desteği

SCP, kodunuzun aynı anda birden çok farklı akışı almasına veya almasına izin verir. **Bağlam** nesnesi bu desteği, **yayma** YÖNTEMININ isteğe bağlı akış kimliği parametresi olarak yansıtır.

SCP.NET **Context** nesnesindeki iki yöntem eklenmiştir. Belirli akışlara bir veya daha fazla tanımlama grubu yayırlar. *Streamıd* parametresi bir dizedir. Değeri hem C# kodunda hem de topoloji tanımı belirtiminde aynı olmalıdır.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Varolmayan bir akışa yayma, çalışma zamanı özel durumlarına neden olur.

### <a name="fields-grouping"></a>Alanları gruplama

Fırtınası içinde yerleşik alanlar gruplandırmada SCP.NET içinde düzgün çalışmıyor. Java proxy tarafında, tüm alanların veri türü aslında **Byte []** olur. Alanlar gruplandırması, gruplandırmayı yapmak için **Byte []** nesnesinin karma kodunu kullanır. Karma kodu, bu nesnenin RAM 'teki adresidir. Bu nedenle, aynı içeriğe sahip ancak aynı adresi değil çok baytlı nesneler için gruplama yanlış olur.

SCP.NET, özelleştirilmiş bir gruplama yöntemi ekler ve gruplandırmayı yapmak için **Byte []** nesnesinin içeriğini kullanır. Bir belirtim dosyasında söz dizimi aşağıdaki örnekteki gibi görünür:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

Önceki belirtim dosyasında:

* `scp-field-group` gruplandırmanın SCP tarafından uygulanan özelleştirilmiş bir alan gruplandırması olduğunu belirtir.
* `:tx` ya da `:non-tx` topolojinin işlem olup olmadığını belirtir. Başlangıç dizini işlem ve işlem dışı topolojiler arasında farklı olduğundan, bu bilgiye ihtiyacınız vardır.
* `[0,1]` sıfır ile başlayan bir alan kimliği karma kümesi belirtir.

### <a name="hybrid-topology"></a>Karma topolojisi

Yerel fırtınası kodu Java 'da yazılmıştır. SCP.NET, iş mantığınızı işlemek için C# kodu yazmanızı sağlamak üzere gelişmiş bir fırtınası içerir. Ancak SCP.NET aynı zamanda yalnızca C# Spoler/cıvatları değil, Java Spoler/cıvatları de içeren karma topolojilerini destekler.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Bir belirtim dosyasında Java Spout/Ruli belirtme

Java spotları ve cıvatları belirtmek için bir belirtim dosyasında **SCP-Spout** ve **SCP-sürgüsü** kullanabilirsiniz. Aşağıda bir örnek verilmiştir:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

`microsoft.scp.example.HybridTopology.Generator`Java Spout sınıfının adı aşağıda verilmiştir.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>RunSpec komutunda Java sınıfyolunu belirtme

Java spotları veya cıvataları içeren topoloji göndermek istiyorsanız, ilk olarak bunları JAR dosyaları oluşturacak şekilde derleyin. Ardından, topolojiyi gönderdiğinizde JAR dosyalarını içeren Java sınıf yolunu belirtin. Aşağıda bir örnek verilmiştir:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Burada, `examples\HybridTopology\java\target\` Java Spout/CıVAM jar dosyasını içeren klasördür.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java ve C arasında serileştirme ve seri durumdan çıkarma #

Bir SCP bileşeni, Java tarafını ve C# tarafını içerir. Yerel Java spotları/cıvataları ile etkileşim kurmak için aşağıdaki grafikte gösterildiği gibi, serileştirme ve seri durumdan çıkarma Java yan ve C# tarafı arasında gerçekleşmelidir:

:::image type="content" source="./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png" alt-text="SCP bileşenine gönderilen ve daha sonra farklı bir Java bileşenine gönderen Java bileşeni diyagramı" border="false":::

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>C# tarafında Java tarafında serileştirme ve seri durumdan çıkarma

İlk olarak, Java tarafında serileştirme için varsayılan uygulamayı ve C# tarafında seri durumdan çıkarmayı sağlayın.

Java tarafında serileştirme yöntemini bir belirtim dosyasında belirtin.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

C# kodunuzda C# tarafının serisini kaldırma yöntemini belirtin.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Veri türü çok karmaşık değilse, bu varsayılan uygulamanın çoğu durumu işlemesi gerekir. Kendi uygulamanızı ekleyebileceğiniz durumlar şunlardır:

* Veri türü, varsayılan uygulama için çok karmaşık.
* Varsayılan uygulamanızın performansı gereksinimlerinizi karşılamıyor.

Java tarafındaki serileştirme arabirimi şöyle tanımlanır:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C# tarafındaki seri kaldırma arabirimi şöyle tanımlanır:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Java tarafında C# tarafında serileştirme ve seri durumdan çıkarma

C# kodunuzda C# tarafının serileştirme yöntemini belirtin.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Bir belirtim dosyasında Java tarafında seri kaldırma yöntemini belirtin.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Burada, `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` seri hale getiricinin adıdır ve `"microsoft.scp.example.HybridTopology.Person"` verilerin Serisi kaldırılan hedef sınıftır.

Ayrıca, bir C# serileştirici ve Java seri hale getirici uygulamanızı da takabilirsiniz.

Bu kod, C# serileştirici için arabirimidir:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Bu kod, Java seri hale getiricisi için arabirimidir:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP ana bilgisayar modu

Bu modda, kodunuzu bir DLL olarak derleyebilir ve bir topoloji göndermek için SCP tarafından sağlandığı SCPHost.exe kullanabilirsiniz. Bir belirtim dosyası şu kod gibi görünür:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Burada, `"plugin.name"` `"SCPHost.exe"` SCP SDK 'sı tarafından sunulan olarak belirtilir. SCPHost.exe aşağıdaki sırada üç parametre kabul eder:

1. `"HelloWorld.dll"`Bu ÖRNEKTEKI dll adı.
1. `"Scp.App.HelloWorld.Generator"`Bu örnekteki sınıf adı.
1. Bir genel statik yöntemin adı, bu, bir **SCC, SCC**'in bir örneğini almak için çağrılabilir.

Ana bilgisayar modunda, SCP platformu tarafından çağrılması için kodunuzu bir DLL olarak derleyin. Platform daha sonra işlem mantığının tamamına tam denetim gönderebildiğinden, topolojiyi SCP ana bilgisayar modunda göndermeniz önerilir. Bunun yapılması geliştirme deneyimini basitleştirir. Ayrıca, daha fazla esneklik ve daha sonraki sürümlerde daha iyi uyumluluk sağlar.

## <a name="scp-programming-examples"></a>SCP programlama örnekleri

### <a name="helloworld"></a>HelloWorld

Aşağıdaki basit HelloWorld örneği, SCP.NET bir listesini gösterir. **Oluşturucu** adlı ve **Splitter** ve **sayaç** olarak adlandırılan iki cıvam ile birlikte işlem dışı bir topoloji kullanır. **Generator** Spout, cümleleri rastgele oluşturur ve bu cümleleri **bölümlendiriciye** yayar. **Bölümlendirici** , cümleleri sözcüklere böler ve bu sözcükleri **sayaç** sürgüsü 'a yayar. **Sayaç** sürgüsü, her sözcüğün oluşumunu kaydetmek için bir sözlük kullanır.

Bu örnek iki belirtim dosyasına sahiptir: HelloWorld. spec ve HelloWorld \_ enableack. spec. C# kodu, nesneyi Java tarafında alarak onay yapılıp yapılmayacağını bulabilir `pluginConf` .

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Spout 'de onay etkinse, sözlük, kabul edilmemiş olan tanımlama gruplarını önbelleğe alır. `Fail`Çağrılırsa, başarısız kayıt düzeni yeniden yürütülür.

```csharp
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
```

### <a name="helloworldtx"></a>Merhaba Worldtx

Aşağıdaki HelloWorldTx örneği, işlem topolojisinin nasıl uygulanacağını gösterir. Örnek, **Oluşturucu** adlı bir Spout, **kısmi sayı** olarak adlandırılan bir yığın işareti ve **Count-Sum** adlı bir COMMIT sürgüsü içerir. Örnekte ayrıca üç mevcut metin dosyası vardır: DataSource0.txt, DataSource1.txt ve DataSource2.txt.

Her işlemde, **Oluşturucu** Spout var olan üç dosyadan rastgele iki dosya seçer ve iki dosya adını **kısmi sayı** sürgüüne yayar. **Kısmi sayı** sürgüsü:

1. Alınan tanımlama grubundan bir dosya adı alır.
1. Karşılık gelen dosyayı açar.
1. Dosyadaki sözcüklerin sayısını sayar.
1. Sözcük sayısını **say-topla** sürgüsü olarak yayar.

**Count-Sum** sürgüsü toplam sayıyı özetler.

Tam bir kez semantik bir şekilde ulaşmak için, **Count-Sum** COMMIT sürgüsü, yeniden yürütülmüş bir işlem olup olmadığını Jbir şekilde gerektirir. Bu örnekte, aşağıdaki statik üye değişkenine sahiptir:

```csharp
public static long lastCommittedTxId = -1; 
```

Bir **ıscpbatchcıvam** örneği oluşturulduğunda, `txAttempt` nesne değerini Giriş parametrelerinden alır.

```csharp
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
```

`FinishBatch`Çağrıldığında, `lastCommittedTxId` yeniden yürütülmüş bir işlem yoksa güncelleştirilir.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Bu topoloji bir Java Spout ve C# sürgüsü içerir. SCP platformu tarafından belirtilen varsayılan serileştirme ve seri kaldırma uygulamasını kullanır. \\Belirtim dosyası ayrıntıları için bkz. örnek hybridtopology klasöründe hybridtopology. spec dosyasına bakın. Ayrıca bkz. Java sınıfyolunu belirtme SubmitTopology.bat.

### <a name="scphostdemo"></a>SCPHostDemo

Bu örnek, HelloWorld ile aynı özbir örnektir. Tek fark, kodunuzun DLL olarak derlenmesi ve topoloji SCPHost.exe kullanılarak gönderilir. Daha ayrıntılı bir açıklama için SCP konak modu bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

SCP kullanılarak oluşturulan Apache Storm topolojilerinin örnekleri için aşağıdaki makalelere bakın:

* [Visual Studio kullanarak HDInsight üzerinde Apache Storm için C# topolojileri geliştirme](apache-storm-develop-csharp-visual-studio-topology.md)
* [HDInsight üzerinde Apache Storm Azure Event Hubs olayları işleme](apache-storm-develop-csharp-event-hub-topology.md)
* [HDInsight üzerinde Apache Storm kullanarak Event Hubs araç algılayıcısı verilerini işleme](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Azure Event Hubs Apache HBase 'e ayıklama, dönüştürme ve yükleme (ETL)](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)

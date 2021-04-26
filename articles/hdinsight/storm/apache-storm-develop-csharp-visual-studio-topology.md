---
title: Visual Studio ve C# ile topoloji Apache Storm-Azure HDInsight
description: C# ' de bir fırtınası topolojisi oluşturmayı öğrenin. Visual Studio için Hadoop araçlarını kullanarak Visual Studio 'da bir sözcük sayısı topolojisi oluşturun.
ROBOTS: NOINDEX
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5360057ec2dfbd2b40c6f086f5e910103c544b62
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865460"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake araçları 'nı kullanarak Apache Storm için C# topolojileri geliştirme

Visual Studio için Azure Data Lake (Apache Hadoop) araçlarını kullanarak C# Apache Storm topolojisi oluşturmayı öğrenin. Bu belgede, Visual Studio 'da bir fırtınası projesi oluşturma, yerel olarak test etme ve Azure HDInsight kümesinde bir Apache Storm dağıtma işlemi gösterilmektedir.

Ayrıca C# ve Java bileşenlerini kullanan karma topolojiler oluşturmayı öğreneceksiniz.

C# topolojileri, .NET 4,5 kullanır ve HDInsight kümesinde çalıştırmak için mono kullanır. Olası uyumsuzluklar hakkında daha fazla bilgi için bkz. [mono uyumluluk](https://www.mono-project.com/docs/about-mono/compatibility/). C# topolojisi kullanmak için, `Microsoft.SCP.Net.SDK` projeniz tarafından kullanılan NuGet paketini 0.10.0.6 veya üzeri sürümüne güncelleştirmeniz gerekir. Paketin sürümünün ayrıca HDInsight üzerinde yüklü olan Storm ana sürümüyle eşleşmesi gerekir.

| HDInsight sürümü | Apache Storm sürümü | SCP.NET sürümü | Varsayılan mono sürümü |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0. x | 0.10.0. x | 3.2.8 |
| 3,5 | 1.0.2. x | 1.0.0. x | 4.2.1 |
| 3.6 | 1.1.0. x | 1.0.0. x | 4.2.8 |

## <a name="prerequisite"></a>Önkoşul

HDInsight üzerinde bir Apache Storm kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) ve **küme türü** için **fırtınası** seçme.

## <a name="install-visual-studio"></a>Visual Studio'yu yükleme

[Visual Studio 'yu](https://visualstudio.microsoft.com/downloads/)kullanarak SCP.net Ile C# topolojileri geliştirebilirsiniz. Buradaki yönergeler Visual Studio 2019 kullanır, ancak Visual Studio 'nun önceki sürümlerini de kullanabilirsiniz.

## <a name="install-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake araçları 'nı yükler

Visual Studio için Data Lake araçları 'nı yüklemek için, [Visual Studio için Data Lake araçları 'nı kullanmaya başlama](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)bölümündeki adımları izleyin.

## <a name="install-java"></a>Java 'Yı yükler

Visual Studio 'dan bir fırtınası topolojisi gönderdiğinizde, SCP.NET, topolojiyi ve bağımlılıklarını içeren bir ZIP dosyası oluşturur. Java, Linux tabanlı kümeler ile daha uyumlu bir biçim kullandığından, bu ZIP dosyalarını oluşturmak için kullanılır.

1. Geliştirme ortamınıza Java geliştirici seti (JDK) 7 veya üstünü yükler. Oracle JDK 'yi [Oracle](https://openjdk.java.net/)'dan edinebilirsiniz. [Diğer Java dağıtımlarını](/java/azure/jdk/)da kullanabilirsiniz.

2. `JAVA_HOME`Ortam değişkenini Java içeren dizine ayarlayın.

3. `PATH`Ortam değişkenini dizini içerecek şekilde ayarlayın `%JAVA_HOME%\bin` .

Java ve JDK 'nin doğru yüklendiğini doğrulamak için aşağıdaki C# konsol uygulamasını derleyip çalıştırabilirsiniz:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm şablonları

Visual Studio için Data Lake araçları aşağıdaki şablonları sağlar:

| Proje türü | Gösteriler |
| --- | --- |
| Fırtınası uygulaması |Boş bir fırtınası topolojisi projesi. |
| Fırtınası Azure SQL yazıcı örneği |Azure SQL veritabanı 'na yazma. |
| Fırtınası Azure Cosmos DB okuyucu örneği |Azure Cosmos DB nasıl okunmalıdır. |
| Fırtınası Azure Cosmos DB yazıcı örneği |Azure Cosmos DB yazma. |
| Fırtınası EventHub okuyucu örneği |Azure Event Hubs nasıl okunmalıdır. |
| Fırtınası EventHub yazıcı örneği |Azure Event Hubs yazma. |
| Fırtınası HBase okuyucu örneği |HDInsight kümelerinde HBase 'den okuma. |
| Fırtınası HBase yazıcı örneği |HDInsight kümelerinde HBase 'e yazma. |
| Fırtınası karma örneği |Java bileşeni kullanma. |
| Fırtınası örneği |Temel bir sözcük sayısı topolojisi. |

> [!WARNING]  
> Tüm şablonlar Linux tabanlı HDInsight ile çalışmaz. Şablonlar tarafından kullanılan NuGet paketleri Mono ile uyumlu olmayabilir. Olası sorunları belirlemek için bkz. [mono uyumluluğu](https://www.mono-project.com/docs/about-mono/compatibility/) ve [.net taşınabilirlik Çözümleyicisi](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis)'ni kullanma.

Bu belgedeki adımlarda, bir topoloji oluşturmak için temel fırtınası uygulaması proje türünü kullanırsınız.

### <a name="apache-hbase-templates"></a>Apache HBase şablonları

HBase okuyucu ve yazıcı şablonları, HDInsight kümesinde HBase ile iletişim kurmak için HBase Java API 'sini değil HBase REST API kullanır.

### <a name="eventhub-templates"></a>EventHub şablonları

> [!IMPORTANT]  
> EventHub okuyucu şablonuna dahil edilen Java tabanlı EventHub Spout bileşeni, HDInsight sürüm 3,5 veya sonraki bir sürümü üzerinde geçiş ile çalışmayabilir. Bu bileşenin güncelleştirilmiş bir sürümü [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib)' da bulunabilir.

Bu bileşeni kullanan ve HDInsight 3,5 üzerinde fırtınası ile birlikte çalışabilen örnek bir topoloji için bkz. [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>C# topolojisi oluşturma

Visual Studio 'da C# topolojisi projesi oluşturmak için:

1. Visual Studio'yu açın.

1. **Başlangıç** penceresinde **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur** penceresinde, ' ye kaydırın ve **fırtınası uygulaması**' nı seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın** penceresinde, *WORDCOUNT* için bir **Proje adı** girin, proje için bir **konum** dizini yolu seçin veya oluşturun ve ardından **Oluştur**' u seçin.

    :::image type="content" source="./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png" alt-text="Fırtınası uygulaması, yeni projenizi yapılandırma iletişim kutusu, Visual Studio" border="true":::

Projeyi oluşturduktan sonra, aşağıdaki dosyalara sahip olmanız gerekir:

* *Program. cs*: projeniz için topoloji tanımı. Bir Spout ve bir sürgüden oluşan varsayılan topoloji varsayılan olarak oluşturulur.

* *Spout. cs*: rastgele sayılar sağlayan bir Spout örneği.

* *Sürgüsü. cs*: Spout tarafından yayılan sayı sayısını tutan bir örnek sürgüsü.

Projeyi oluşturduğunuzda, NuGet en son [SCP.net paketini](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)indirir.

### <a name="implement-the-spout"></a>Spout 'yi uygulama

Ardından, bir dış kaynaktan bir topolojideki verileri okumak için kullanılan Spout için kodu ekleyin. Bu Spout, topolojiye rastgele bir tümce yayar.

1. *Spout. cs*'yi açın. Spout ana bileşenleri şunlardır:

   * `NextTuple`: Spout 'in yeni tanımlama gruplarını yaymak için izin verildiğinde fırtınası tarafından çağırılır.

   * `Ack` (yalnızca işlem topolojisi): Spout 'dan gönderilen tanımlama grupları için topolojideki diğer bileşenler tarafından başlatılan bildirimleri Işler. Bir tanımlama grubunun ele alındığı, biriktirme listesini aşağı akış bileşenleri tarafından başarıyla işlendiğini bildirir.

   * `Fail` (yalnızca işlem topolojisi): topolojideki diğer bileşenleri işlemek için başarısız olan tanımlama gruplarını Işler. Bir `Fail` yöntemi uygulamak, kayıt düzeninin yeniden işlenebilmesi için yeniden yaymasını sağlar.

2. `Spout`Sınıfının içeriğini aşağıdaki metinle değiştirin:

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Cıvatları uygulama

Şimdi bu örnekte iki ters fırtınası oluşturun:

1. Var olan *cıva. cs* dosyasını projeden silin.

2. **Çözüm Gezgini**, projeye sağ tıklayın ve   >  **Yeni öğe** Ekle ' yi seçin. Listeden **fırtınası**' ı seçin ve ad olarak *Splitter. cs* yazın. Yeni dosyanın kodunda, ad alanı adını olarak değiştirin `WordCount` . Sonra *Counter. cs* adlı ikinci bir sürgüyi oluşturmak için bu işlemi tekrarlayın.

   * *Splitter. cs*: cümleleri bağımsız sözcüklere ayıran bir sürgüler uygular ve yeni bir sözcük akışı yayar.

   * *Counter. cs*: her bir sözcüğü sayan bir sürgüsü uygular ve her bir sözcük için yeni bir sözcük akışı ve sayı yayar.

     > [!NOTE]  
     > Bu cıvatları, akışları okur ve yazar, ancak bir veritabanı veya hizmet gibi kaynaklarla iletişim kurmak için de bir cıvata kullanabilirsiniz.

3. *Splitter. cs*'yi açın. Varsayılan olarak yalnızca bir yönteme sahiptir: `Execute` . Bu `Execute` Yöntem, sürgülü işleme için bir demet aldığında çağrılır. Burada, gelen tanımlama gruplarını okuyabilir ve işleyebilir ve giden tanımlama gruplarını görüntüleyebilirsiniz.

4. `Splitter`Sınıfının içeriğini aşağıdaki kodla değiştirin:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. *Counter. cs*' yi açın ve sınıf içeriğini şu kodla değiştirin:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Topolojiyi tanımlama

Spout ve cıvatları, verilerin bileşenler arasında nasıl akacağını tanımlayan bir grafikte düzenlenir. Bu topoloji için grafik aşağıdaki gibidir:

:::image type="content" source="./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png" alt-text="Spout ve cıvam bileşeni düzenleme diyagramı, fırtınası topolojisi" border="false":::

Spout, Splitter sürgüsü örneklerine dağıtılan cümleler yayar. Bölümlendirici, tümce, sayaç ayraçına dağıtılan kelimelerin içine alınır.

Sayaç örneği yerel olarak sözcük sayısını taşıdığı için, belirli sözcüklerin aynı sayaç sürgüsü örneğine akmasını sağlamak istiyorsunuz. Her örnek belirli sözcükleri izler. Splitter sürgüsü durum içermediğinden, bu, ayırıcının hangi örneğinin hangi cümleyi alacağını oldukça önemlidir.

*Program.cs* dosyasını açın. Önemli yöntemi `GetTopologyBuilder` , fırtınası 'ya gönderilen topolojiyi tanımlamak için kullanılır. `GetTopologyBuilder`Daha önce açıklanan topolojiyi uygulamak için aşağıdaki kodla içeriğini değiştirin:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Topolojiyi gönder

Artık topolojiyi HDInsight kümenize göndermeye hazırsınız.

1. Sunucu Gezgini **görüntüle**' ye gidin  >  .

1. **Azure**' a sağ tıklayın, **Microsoft Azure aboneliğine Bağlan...** öğesini seçin ve oturum açma işlemini doldurun.

1. **Çözüm Gezgini**, projeye sağ tıklayın ve **HDInsight 'Ta fırtınası 'ya gönder**' i seçin.

1. **Topolojiyi gönder** iletişim kutusunda, **fırtınası kümesi** aşağı açılan listesinde, HDInsight kümesinde fırtınası ' ı seçin ve ardından **Gönder**' i seçin. **Çıkış** bölmesini görüntüleyerek gönderimin başarılı olup olmadığını kontrol edebilirsiniz.

    Topoloji başarıyla gönderildiğinde, küme için **fırtınası topolojileri görünüm** penceresi görünmelidir. Çalışan topoloji hakkındaki bilgileri görüntülemek için listeden **WORDCOUNT** topolojisini seçin.

    :::image type="content" source="./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png" alt-text="Fırtınası topolojisi Görünüm penceresi, HDInsight kümesi, Visual Studio" border="true":::

    > [!NOTE]  
    > Ayrıca, **Sunucu Gezgini** karşı **fırtınası topolojilerini** görüntüleyebilirsiniz. **Azure**  >  **HDInsight**' ı genişletin, HDInsight kümesinde bir fırtınası ' a sağ tıklayın ve ardından **fırtınası topolojilerini görüntüle**' yi seçin.

    Topolojideki bileşenler hakkında bilgi görüntülemek için diyagramda bir bileşen seçin.

1. Topoloji **Özeti** bölümünde, topolojiyi durdurmak için **Sonlandır** ' ı seçin.

    > [!NOTE]  
    > Fırtınası topolojileri devre dışı bırakılıncaya veya küme silinene kadar çalışmaya devam eder.

## <a name="transactional-topology"></a>İşlem topolojisi

Önceki topoloji işlem dışı. Topolojideki bileşenler, iletileri yeniden almak için işlevsellik uygulamaz. İşlem topolojisine bir örnek için, bir proje oluşturun ve proje türü olarak **fırtınası örneği** ' ni seçin.

İşlem topolojileri, verileri yeniden yürütmeyi desteklemek için aşağıdakileri uygular:

* **Meta veri önbelleğe alma**: Spout, bir hata oluşursa verilerin alınıp yeniden yayınlanabilmesi için, oluşturulan verilerle ilgili meta verileri depolaması gerekir. Örnek tarafından yayılan veriler küçük olduğundan, her bir demet için ham veriler yeniden oynatma için bir sözlükte depolanır.

* **ACK**: topolojideki her bir sürgülü, `this.ctx.Ack(tuple)` bir kayıt düzeni başarıyla işlendiğini bildirmek için çağrı yapabilir. Tüm cıvatları, kayıt kümesini kabul eder, `Ack` Spout yöntemi çağrılır. `Ack`Yöntemi, Spout 'ın yeniden oynatma için önbelleğe alınmış verileri kaldırmasına izin verir.

* **Başarısız**: her `this.ctx.Fail(tuple)` bir demet, işlemenin bir tanımlama grubu için başarısız olduğunu göstermek için çağırabilir. Hata, `Fail` kayıt düzeninin önbelleğe alınmış meta veriler kullanılarak yeniden çalınabilecek Spout yöntemine yayar.

* **Sıra kimliği**: bir kayıt düzeni yayırken, benzersiz BIR sıra kimliği belirtilebilir. Bu değer, yeniden yürütme ( `Ack` ve) işleme için tanımlama grubu tanımlar `Fail` . Örneğin, **fırtınası örnek** projesindeki Spout, verileri yayırken aşağıdaki yöntem çağrısını kullanır:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Bu kod, içinde yer alan sıra KIMLIĞI değeri ile varsayılan akışa bir cümle içeren bir tanımlama grubu yayar `lastSeqId` . Bu örnek için, `lastSeqId` her bir tanımlama grubu için artırılır.

**Fırtınası örnek** projesinde gösterildiği gibi, bir bileşenin hareketsel olup olmadığı, yapılandırmaya göre çalışma zamanında ayarlanabilir.

## <a name="hybrid-topology-with-c-and-java"></a>C# ve Java ile karma topolojisi

Visual Studio için Data Lake araçları 'nı, bazı bileşenlerin C# olduğu ve diğerlerinin Java olduğu karma topolojiler oluşturmak için de kullanabilirsiniz.

Karma topolojinin bir örneği için bir proje oluşturun ve sonra da **fırtınası karma örneği**' ni seçin. Bu örnek türü aşağıdaki kavramları gösterir:

* **Java Spout** ve **C# sürgüsü**: sınıfta tanımlı `HybridTopology_javaSpout_csharpBolt` .

  Sınıfında bir işlem sürümü tanımlanmıştır `HybridTopologyTx_javaSpout_csharpBolt` .

* **C# Spout** ve **Java sürgüsü**: sınıfında tanımlı `HybridTopology_csharpSpout_javaBolt` .

  Sınıfında bir işlem sürümü tanımlanmıştır `HybridTopologyTx_csharpSpout_javaBolt` .

  > [!NOTE]  
  > Bu sürüm ayrıca bir metin dosyasındaki Clojure kodunun Java bileşeni olarak nasıl kullanılacağını gösterir.

Proje gönderildiğinde kullanılan topolojiyi değiştirmek için, `[Active(true)]` ifadeyi kümeye göndermeden önce, kullanmak istediğiniz topolojiye taşıyın.

> [!NOTE]  
> Gerekli olan tüm Java dosyaları *Javadependency* klasöründe bu projenin bir parçası olarak sağlanır.

Karma topoloji oluştururken ve gönderirken aşağıdakileri göz önünde bulundurun:

* `JavaComponentConstructor`Bir Spout veya cıvata Java sınıfının bir örneğini oluşturmak için kullanın.

* Java `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` NESNELERINDEN JSON 'a veri aktarmak veya bu bileşenleri kapatmak için kullanın.

* Topoloji sunucuya gönderilirken, **Java dosya yollarını** belirtmek için **ek konfigürasyonlar** seçeneğini kullanmanız gerekir. Belirtilen yol, Java sınıflarınızı içeren JAR dosyalarına sahip olan dizin olmalıdır.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET Version 0.9.4.203, özellikle Olay Hub 'ı (Event Hubs okuyan bir Java Spout) ile birlikte çalışmak için yeni bir sınıf ve yöntem sunar. Bir olay hub 'ı (örneğin, **fırtınası EventHub okuyucu örnek** şablonunu kullanarak) kullanan bir topoloji oluşturduğunuzda, aşağıdaki API 'leri kullanın:

* `EventHubSpoutConfig` Sınıf: Spout bileşeni yapılandırmasını içeren bir nesne oluşturur.

* `TopologyBuilder.SetEventHubSpout` Yöntem: Olay Hub 'ı Spout bileşenini topolojiye ekler.

> [!NOTE]  
> `CustomizedInteropJSONSerializer`Spout tarafından üretilen verileri seri hale getirmek için kullanmaya devam etmeniz gerekir.

## <a name="use-configurationmanager"></a>ConfigurationManager kullanın

Cıvam ve Spout bileşenlerinden yapılandırma değerlerini almak için **ConfigurationManager** kullanmayın. Bunun yapılması null işaretçi özel durumuna neden olabilir. Bunun yerine, projenizin yapılandırmasını, topoloji bağlamına anahtar ve değer çifti olarak fırtınası topolojisine geçirin. Yapılandırma değerlerini kullanan her bir bileşen, başlatma sırasında bunları bağlamdan almalıdır.

Aşağıdaki kod, bu değerlerin nasıl alınacağını gösterir:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

`Get`Bileşeninizin bir örneğini döndürmek için bir yöntem kullanıyorsanız, oluşturucuya hem hem de parametrelerini geçirdiğinden emin olmanız gerekir `Context` `Dictionary<string, Object>` . Aşağıdaki örnek, `Get` Bu değerleri düzgün şekilde geçiren temel bir yöntemdir:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>SCP.NET 'i güncelleştirme

SCP.NET Destek paketinin son sürümleri NuGet aracılığıyla yükseltilir. Yeni bir güncelleştirme kullanılabilir olduğunda, bir yükseltme bildirimi alırsınız. Bir yükseltmeyi el ile denetlemek için şu adımları izleyin:

1. **Çözüm Gezgini**’nde projeye sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin.

2. Paket Yöneticisi ' nden **güncelleştirmeler**' i seçin. SCP.NET destek paketine yönelik bir güncelleştirme varsa, bu listelenir. Paket için **Güncelleştir** ' i seçin ve ardından **Değişiklikleri Önizle** iletişim kutusunda **Tamam** ' ı seçerek yüklemeyi seçin.

> [!IMPORTANT]  
> Projeniz NuGet kullanmayan bir SCP.NET önceki sürümüyle oluşturulduysa, daha yeni bir sürüme güncelleştirmek için aşağıdaki adımları gerçekleştirmeniz gerekir:
>
> 1. **Çözüm Gezgini**’nde projeye sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin.
> 2. **Arama** alanını kullanarak projeye arama yapın ve ekleyin `Microsoft.SCP.Net.SDK` .

## <a name="troubleshoot-common-issues-with-topologies"></a>Topolojilerle ilgili yaygın sorunları giderme

### <a name="null-pointer-exceptions"></a>Null işaretçi özel durumları

Linux tabanlı HDInsight kümesiyle C# topolojisi kullanırken, çalışma zamanında yapılandırma ayarlarını okumak için **ConfigurationManager** kullanan cıvam ve Spout bileşenleri null işaretçi özel durumları döndürebilir.

Projenizin yapılandırması, topoloji bağlamına anahtar ve değer çifti olarak fırtınası topolojisine geçirilir. Bunlar, başlatıldığında, bileşenlerinizi geçirilen sözlük nesnesinden alınabilir.

Daha fazla bilgi için bu belgenin [ConfigurationManager öğesini kullanma](#use-configurationmanager) bölümüne bakın.

### <a name="systemtypeloadexception"></a>System. TypeLoadException

Linux tabanlı HDInsight kümesiyle C# topolojisi kullanırken, şu hata boyunca karşılaşabilirsiniz:

`System.TypeLoadException: Failure has occurred while loading a type.`

Bu hata, mono 'nin desteklediği .NET sürümü ile uyumlu olmayan bir ikili kullandığınızda oluşur.

Linux tabanlı HDInsight kümeleri için, projenizin .NET 4,5 için derlenmiş ikili dosyaları kullandığından emin olun.

### <a name="test-a-topology-locally"></a>Topolojiyi yerel olarak test etme

Bir kümeye topoloji dağıtmak kolay olsa da, bazı durumlarda bir topolojiyi yerel olarak test etmeniz gerekebilir. Bu makaledeki örnek topolojiyi geliştirme ortamınızda yerel olarak çalıştırmak ve test etmek için aşağıdaki adımları kullanın.

> [!WARNING]  
> Yerel test yalnızca temel, yalnızca C# topolojileri için geçerlidir. Birden çok akış kullanan karma topolojiler veya topolojiler için yerel test kullanamazsınız.

1. **Çözüm Gezgini**, projeye sağ tıklayın ve **Özellikler**' i seçin. Proje özelliklerinde. Ardından **çıktı türünü** **konsol uygulaması** olarak değiştirin.

   :::image type="content" source="./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png" alt-text="HDInsight fırtınası uygulaması, proje özellikleri, çıkış türü" border="true":::

   > [!NOTE]
   > Topolojiyi bir kümeye dağıtmadan önce, **çıkış türünü** yeniden **sınıf kitaplığına** değiştirmeyi unutmayın.

1. **Çözüm Gezgini**, projeye sağ tıklayın ve ardından   >  **Yeni öğe** Ekle ' yi seçin. **Sınıf**' ı seçin ve sınıf adı olarak *localtest. cs* yazın. Son olarak, **Ekle**' yi seçin.

1. *Localtest. cs*' yi açın ve `using` üst kısımdaki aşağıdaki ifadeyi ekleyin:

    ```csharp
    using Microsoft.SCP;
    ```

1. Sınıfının içeriği olarak aşağıdaki kodu kullanın `LocalTest` :

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Kod açıklamalarını okumak için bir dakikanızı ayırın. Bu kod, `LocalContext` geliştirme ortamındaki bileşenleri çalıştırmak için kullanır. Yerel sürücüdeki bileşenler arasındaki veri akışını sürdürür.

1. *Program. cs*' yi açın ve yöntemine aşağıdaki kodu ekleyin `Main` :

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

1. Değişiklikleri kaydedin ve ardından **F5** ' i seçin veya   >  projeyi başlatmak için hata **ayıklamayı Başlat** ' ı seçin. Bir konsol penceresi görünür ve test ilerleme durumu olarak günlüğe kaydedilir. `Tests finished`Göründüğünde, pencereyi kapatmak için herhangi bir anahtar seçin.

1. Projenizi içeren dizini bulmak için **Windows Gezgini** 'ni kullanın. (Örneğin: *C: \\ Users \\ \<your_user_name> \\ kaynak \\ depoları \\ WORDCOUNT \\ WORDCOUNT*.) Sonra bu dizinde, *bin*' i açın ve ardından *Hata Ayıkla*' yı seçin. Testler çalıştırıldığında üretilen metin dosyalarını görmeniz gerekir: *sentences.txt*, *counter.txt* ve *splitter.txt*. Her metin dosyasını açın ve verileri inceleyin.

   > [!NOTE]  
   > Dize verileri, bu dosyalardaki ondalık değerlerin bir dizisi olarak devam ettirir. Örneğin, `[[97,103,111]]` **splitter.txt** dosyası *önce* kelimeyi temsil eder.

> [!NOTE]  
> HDInsight kümesindeki bir fırtınası üzerine dağıtılmadan önce proje özelliklerinde **proje türünü** **sınıf kitaplığına** geri ayarladığınızdan emin olun.

### <a name="log-information"></a>Günlük bilgileri

Kullanarak topoloji bileşenlerinizin bilgilerini kolayca günlüğe kaydedebilirsiniz `Context.Logger` . Örneğin, aşağıdaki komut bir bilgi günlüğü girişi oluşturur:

`Context.Logger.Info("Component started");`

Günlüğe kaydedilen bilgiler, **Sunucu Gezgini** bulunan **Hadoop hizmeti günlüğünden** görüntülenebilir. HDInsight kümesinde fırtınası için girişi genişletin ve ardından **Hadoop hizmeti günlüğü**' ne genişletin. Son olarak, görüntülenecek günlük dosyasını seçin.

> [!NOTE]  
> Günlükler, kümeniz tarafından kullanılan Azure depolama hesabında depolanır. Günlükleri Visual Studio 'da görüntülemek için, depolama hesabının sahibi olan Azure aboneliğinde oturum açmanız gerekir.

### <a name="view-error-information"></a>Hata bilgilerini görüntüle

Çalışan bir topolojide oluşan hataları görüntülemek için aşağıdaki adımları kullanın:

1. **Sunucu Gezgini**, HDInsight kümesindeki fırtınası kümesine sağ tıklayın ve **fırtınası topolojilerini görüntüle**' yi seçin.

   **Spout** ve **Cıvatları** için **son hata** sütunu son hata hakkındaki bilgileri içerir.

2. Listelenen bir hata içeren bileşen için **Spout kimliği** veya **cıvatıd kimliğini** seçin. Ayrıntılar sayfasında sayfanın altındaki **hatalar** bölümünde ek hata bilgileri görüntülenir.

3. Daha fazla bilgi edinmek için, sayfanın **Yürüticileri** bölümünde bir **bağlantı noktası** seçin.

### <a name="errors-submitting-topologies"></a>Topolojileri gönderme hataları

HDInsight 'a bir topoloji gönderme hatalarıyla karşılaşırsanız, HDInsight kümenizdeki topoloji gönderimini işleyen sunucu tarafı bileşenlerine yönelik Günlükler bulabilirsiniz. Bu günlükleri indirmek için komut satırından aşağıdaki komutu kullanın:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

*Sshuser* ÖĞESINI kümenin SSH kullanıcı hesabı ile değiştirin. *Clustername* değerini HDInsight kümesinin adıyla değiştirin. Ve HDInsight ile kullanma hakkında daha fazla bilgi için `scp` `ssh` bkz. [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

Gönderimler birden çok nedenden dolayı başarısız olabilir:

* JDK yüklü değil veya yolda değil.
* Gerekli Java bağımlılıkları göndermeye dahil değildir.
* Bağımlılıklar uyumsuz.
* Topoloji adları yineleniyor.

*Hdinsight-scpwebapi. out* günlük dosyası bir içeriyorsa `FileNotFoundException` , özel durumun nedeni aşağıdaki koşullardan biri olabilir:

* JDK, geliştirme ortamındaki yolda değil. JDK 'nin geliştirme ortamında yüklü olduğunu ve bu yolda olduğunu doğrulayın `%JAVA_HOME%/bin` .
* Java bağımlılığı eksik. Gönderim kapsamında gerekli. jar dosyalarını dahil ettiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Event Hubs verilerinin işlenmesiyle ilgili bir örnek için bkz. [HDInsight 'Ta fırtınası Ile Azure Event Hubs işleme olayları](apache-storm-develop-csharp-event-hub-topology.md).

Akış verilerini birden çok akışa ayıran C# topolojisi örneği için bkz. [C# fırtınası örneği](https://github.com/Blackmist/csharp-storm-example).

C# topolojileri oluşturma hakkında daha fazla bilgi edinmek için bkz. [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

HDInsight ve HDInsight örnekleri üzerinde daha fazla fırtınası ile çalışmanın daha fazla yolu için aşağıdaki belgelere bakın:

**Microsoft SCP.NET**

* [Azure HDInsight 'ta Apache Storm için SCP Programlama Kılavuzu](apache-storm-scp-programming-guide.md)

**HDInsight üzerinde Apache Storm**

* [Azure HDInsight 'ta Apache Storm topolojileri dağıtma ve yönetme](apache-storm-deploy-monitor-topology-linux.md)
* [Azure HDInsight 'ta örnek Apache Storm topolojileri](apache-storm-example-topology.md)

**HDInsight üzerinde Apache Hadoop**

* [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](../hadoop/hdinsight-use-hive.md)
* [HDInsight üzerinde Apache Hadoop ile MapReduce'u kullanma](../hadoop/hdinsight-use-mapreduce.md)

**HDInsight üzerinde Apache HBase**

* [Azure HDInsight’ta Apache HBase kullanma](../hbase/apache-hbase-tutorial-get-started-linux.md)

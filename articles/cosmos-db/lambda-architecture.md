---
title: Azure Cosmos DB ve HDInsight (Apache Spark) ile lambda mimarisi
description: Bu makalede Azure Cosmos DB, HDInsight ve Spark'ı kullanarak bir lambda mimarisi uygulama
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 56f293600d876a5bc52b618ce8eed044e93f424d
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616882"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Azure platformunda bir lambda mimarisi uygulama 

Lambda mimarisi etkin veri işleme, büyük hacimli veri kümeleri etkinleştirin. Lambda mimarisi, büyük verileri Sorgulama ilgili gecikme süresini en aza indirmek için toplu işlem, akış işleme ve Hizmet katmanını kullanın. 

Lambda mimarisi Azure'da uygulamak için gerçek zamanlı büyük veri analizi hızlandırmak için aşağıdaki teknolojileri birleştirebilirsiniz:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), sektörün ilk Global olarak dağıtılmış çok modelli veritabanı hizmetidir. 
* [Azure HDInsight için Apache Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/), büyük ölçekli veri analizi uygulamalarını çalıştıran bir işleme çerçevesi
* Azure Cosmos DB [değişiklik akışını](change-feed.md), toplu iş katmanı işlemek HDInsight için yeni veri akışları
* [Bağlayıcısı Azure Cosmos DB için Spark](spark-connector.md)

Bu makalede, özgün çok katmanlı tasarımı ve işlemlerini kolaylaştıran bir "rearchitected" lambda mimarisinin avantajları temel alan bir lambda mimarisinin temelleri açıklanır.  

## <a name="what-is-a-lambda-architecture"></a>Lambda mimarisi nedir?
Lambda mimarisi genel, ölçeklenebilir ve hataya dayanıklı veri işleme mimarisi adresine batch ve düşük gecikme süresi senaryoları açıklandığı hızlandırmak [Nathan Marz](https://twitter.com/nathanmarz).

![Lambda mimarisi gösteren diyagram](./media/lambda-architecture/lambda-architecture-intro.png)

Kaynak: http://lambda-architecture.net/

Bir lambda mimarisinin temel ilkeleri olarak başına önceki şemada açıklanan [ http://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Tüm **veri** halinde gönderilir *hem* *toplu iş katmanı* ve *hız katmanının*.
 2. **Toplu iş katmanı** ana veri kümesinde (sabit, salt ham veri kümesi) ve toplu görünümler önceden hesaplar.
 3. **Hizmet katmanını** hızlı sorgular için toplu görünüm vardır. 
 4. **Hız katmanının** işleme süresi (için Hizmet katmanını) dengeler ve yalnızca yeni verilerle ilgilidir.
 5. Tüm sorguları birleştirme sonuçları toplu görünümler ve gerçek zamanlı bir görünüm veya ayrı ayrı ping yanıtlanması gereken.

Daha fazla okuma sırasında biz yalnızca şunları kullanarak bu mimariyi uygulamak şunları yapabilir:

* Azure Cosmos kapsayıcıları
* HDInsight (Apache Spark 2.1) kümesi
* Spark Bağlayıcısı [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Hızı katmanı

İşlemler açısından bakıldığında, iki veri akışlarını veri doğru durumu sağlarken koruma karmaşık bir çaba olabilir. İşlemleri basitleştirmek için yazılımınız [Azure Cosmos DB değişiklik akışı desteği](change-feed.md) durumunu korumak için *toplu iş katmanı* aracılığıyla Azure Cosmos DB değişiklik günlüğü, devamlılığımız çalışırken *değiştirme akış API* için *hız katmanının*.  
![Yeni veriler, hız katmanı ve lambda mimarisinin ana veri kümesi bölümünü vurgulama diyagramı](./media/lambda-architecture/lambda-architecture-change-feed.png)

Bu katmanda önemli nedir:

 1. Tüm **veri** gönderildiğinde *yalnızca* Azure Cosmos DB'ye, bu nedenle, çok atama sorunlarını önleyebilirsiniz.
 2. **Toplu iş katmanı** ana veri kümesinde (sabit, salt ham veri kümesi) ve toplu görünümler önceden hesaplar.
 3. **Hizmet katmanını** sonraki bölümde ele alınmıştır.
 4. **Hız katmanının** Azure Cosmos DB değişiklik akışı okumak için HDInsight (Apache Spark) kullanır. Bu, sorgulama ve eşzamanlı olarak işlemek için verilerinizin kalıcı hale getirmek sağlar.
 5. Tüm sorguları birleştirme sonuçları toplu görünümler ve gerçek zamanlı bir görünüm veya ayrı ayrı ping yanıtlanması gereken.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Kod örneği: Azure Cosmos DB değişiklik akışına Spark yapılandırılmış akış
Azure Cosmos DB değişiklik akışı bir parçası olarak hızlı bir prototipi çalıştırmak için **hız katmanının**, Twitter verilerini kullanarak bir parçası olarak sınayabilirsiniz [Stream işleme değişiklikler Azure Cosmos DB değişiklik akışı ve Apache Sparkkullanarak](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)örnek. Twitter çıkış oluşturma sürecini hızlandıracak biçimde kod örnekte bkz [Stream Cosmos DB'ye Twitter'dan akış](https://github.com/tknandu/TwitterCosmosDBFeed). Önceki örnekte ile Twitter verilerini Azure Cosmos DB'ye yüklüyorsunuz ve değişiklik akışı bağlanmak için (Apache Spark) HDInsight kümenizi daha sonra ayarlayabilirsiniz. Bu yapılandırmayı ayarlamak nasıl hakkında daha fazla bilgi için bkz. [Azure Cosmos DB bağlayıcı kurulumu için Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Aşağıdaki kod parçacığını nasıl yapılandırılacağı gösterilmektedir `spark-shell` , çalışan bir aralık sayısı gerçekleştirmek için gerçek zamanlı Twitter veri akışı için gözden geçirmeleri için bir Azure Cosmos DB değişiklik akışı, bağlanmak için yapılandırılmış akış işi çalıştırmak için.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Tam kod örnekleri için bkz [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)de dahil olmak üzere:
* [Cosmos DB değişiklik Feed.scala akış sorgusu](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Cosmos DB değişiklik Feed.scala akış etiketleri sorgu](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Bu çıkış bir `spark-shell` konsolunda, sürekli olarak Azure Cosmos DB değişiklik akışı Twitter verilerini karşı bir aralık sayısı gerçekleştiren yapılandırılmış akış işi çalıştırır. Aşağıdaki görüntüde akışı tanımlı işlemin çıktısını gösterir ve aralığı sayar.

![Azure Cosmos DB değişiklik akışı Twitter verilerini karşı aralık sayısı gösteren akış çıkışı](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Değişiklik akışını Azure Cosmos DB hakkında daha fazla bilgi için bkz:

* [Azure Cosmos DB'de destek akış değişiklik ile çalışma](change-feed.md)
* [Karşınızda Azure CosmosDB değişiklik akışı işlemci kitaplığı](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Akış Işleme değişiklikleri: Azure CosmosDB değişiklik akışı + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch ve hizmet katmanları
Yeni veriler bu Azure Cosmos DB, (burada değişiklik akışı kullanılan hızı katman için) yüklendikten sonra yerdir **ana veri kümesi** (bir sabit, salt ham veri kümesi) yer alıyor. Bu noktadan itibaren başlayarak, HDInsight (Apache Spark) ön işlem işlevleri gerçekleştirmek için kullanmak **toplu iş katmanı** için **Hizmet katmanını**, aşağıdaki görüntüde gösterildiği gibi:

![Toplu iş katmanı ve Hizmet katmanını lambda mimarisinin vurgulama diyagramı](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Bu katmanda önemli nedir:

 1. Tüm **veri** yalnızca Azure Cosmos DB'ye (çok noktaya yayın sorunlarını önlemek için) gönderilir.
 2. **Toplu iş katmanı** olan Azure Cosmos DB'de depolanan ana veri kümesi (sabit, salt ham veri kümesi). HDI Spark'ı kullanarak hesaplanan batch görünümlerinizde depolanacak işlemlerinizi önceden hesaplayabilirsiniz.
 3. **Hizmet sunma katmanı** , ana veri kümesi ve hesaplanan toplu iş görünümü için koleksiyonlara sahip bir Azure Cosmos veritabanıdır.
 4. **Hız katmanının** bu makalenin sonraki bölümlerinde ele alınmıştır.
 5. Tüm sorguların sonuçlarını toplu görünümler ve gerçek zamanlı görünümleri birleştirme veya ayrı ayrı ping yanıtlanması.

### <a name="code-example-pre-computing-batch-views"></a>Kod örneği: Batch görünümlerini önceden hesaplama
Nasıl yürütüleceğini karşı önceden hesaplanmış görünümleri göstermek için **ana veri kümesi** Azure Cosmos DB'de Apache Spark'tan not defterlerini gelen aşağıdaki kod parçacıkları kullanma [Lambda mimarisi Rearchitected - toplu iş katmanı ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) ve [Lambda mimarisi Rearchitected - hizmet katmanı için Batch'i](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Bu senaryoda, Azure Cosmos DB'de depolanan Twitter verilerini kullanın.

Twitter verilerini aşağıdaki PySpark kodu kullanarak Azure Cosmos DB içinde yapılandırma bağlantısı oluşturarak başlayalım.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Yanında, ilk 10 diyez etiketlerini tweetleri kümesini belirlemek için aşağıdaki Spark SQL deyimi çalıştıralım. Bu bir Spark SQL sorgusu için Biz bu bir Jupyter not defteri doğrudan bu kod parçacığı aşağıdaki çıkış çubuk grafik çalıştırıyorsunuz.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Tweet diyez etiketi başına sayısını gösteren grafik](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Şimdi sorgunuzu olduğuna göre çıktı verilerini farklı bir koleksiyona kaydetmek için Spark Bağlayıcısı'nı kullanarak bir koleksiyon için geri kaydedin.  Bu örnekte, Scala bağlantıyı göstermek için kullanın. Önceki örneğe benzer şekilde, Apache Spark veri çerçevesini farklı bir Azure Cosmos kapsayıcısına kaydetmek için yapılandırma bağlantısını oluşturun.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Belirttikten sonra `SaveMode` (belirten kullanılıp kullanılmayacağını `Overwrite` veya `Append` belge), oluşturun bir `tweets_bytags` DataFrame benzer şekilde önceki örnekte Spark SQL sorgusu.  İle `tweets_bytags` kullanarak kaydedebilirsiniz, oluşturulan veri çerçevesi `write` yöntemi kullanarak daha önce belirtilen `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Bu son bildiri artık Spark DataFrame 'i yeni bir Azure Cosmos kapsayıcısına kaydettiniz. bir lambda mimarisi perspektifinden, bu, **hizmet veren katmandaki** **toplu iş görünümüdür** .
 
#### <a name="resources"></a>Kaynaklar

Tam kod örnekleri için bkz [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) dahil olmak üzere:
* Lambda mimarisi Rearchitected - toplu iş katmanı [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda mimarisi Rearchitected - Batch hizmet katmanına [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Hızı katmanı
Daha önce not ettiğiniz, kullanarak Azure Cosmos DB değişiklik akışı kitaplığı, batch ve hız Katmanlar arasındaki işlemleri basitleştirmek sağlar. Bu mimaride, Apache Spark (HDInsight) aracılığıyla gerçekleştirme *yapılandırılmış akış* verilere karşı sorgular. Bu verileri diğer sistemlere erişebilmesi için geçici olarak yapılandırılmış akış sorgularınızın sonuçlarını kalıcı hale getirmek isteyebilirsiniz.

![Lambda mimarisinin hız katmanına vurgulama diyagramı](./media/lambda-architecture/lambda-architecture-speed.png)

Bunu yapmak için, yapılandırılmış akış sorgularınızın sonuçlarını kaydetmek üzere ayrı bir Azure Cosmos kapsayıcısı oluşturun.  Bu, diğer sistemler erişim sağlamak için bu bilgileri sağlar yalnızca Apache Spark. De Cosmos DB için-yaşam süresi (TTL) özelliği ile kullanarak, belgeleriniz otomatik olarak ayarlanmış bir süre sonra silinecek şekilde yapılandırabilirsiniz.  Azure Cosmos DB TTL özelliği hakkında daha fazla bilgi için bkz. [Azure Cosmos kapsayıcılarındaki verileri yaşam süresi ile otomatik olarak bitirme](time-to-live.md)

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Lambda mimarisi: Yeniden tasarlanmıştır
Önceki bölümlerde belirtildiği gibi aşağıdaki bileşenleri'ni kullanarak özgün lambda mimarisinin basitleştirebilirsiniz:
* Azure Cosmos DB
* Çok noktaya yayın için gereken toplu ve hız Katmanlar arasındaki verilerinizi önlemek için Azure Cosmos DB değişiklik akışı kitaplığı
* HDInsight üzerinde Apache Spark
* Azure Cosmos DB için Spark Bağlayıcısı

![Azure Cosmos DB, Spark ve Azure Cosmos DB değişiklik akışı API'si kullanarak lambda mimarisinin rearchitecture gösteren diyagram](./media/lambda-architecture/lambda-architecture-re-architected.png)

Bu tasarımla, yalnızca iki yönetilen hizmetler, Azure Cosmos DB ile HDInsight gerekir. Bunlar birlikte, batch, sunma ve lambda mimarisinin hız katmanı adres. Bu, yalnızca işlem aynı zamanda veri akışı kolaylaştırır. 
 1. Tüm veri işleme için Azure Cosmos DB'ye gönderildi
 2. Toplu iş katmanı ana veri kümesinde (sabit, salt ham veri kümesi) ve toplu görünümler önceden hesaplar
 3. Hizmet katmanını, veri hızlı sorgular için toplu görünümler sahiptir.
 4. Hızı katman, işleme süresi (için Hizmet katmanını) dengeler ve yalnızca yeni verilerle ilgilidir.
 5. Tüm sorguların sonuçlarını toplu görünümler ve gerçek zamanlı görünümleri birleştirerek yanıtlanması gereken.

### <a name="resources"></a>Kaynaklar

* **Yeni veriler**: [Twitter 'Dan CosmosDB 'ye](https://github.com/tknandu/TwitterCosmosDBFeed), yeni verileri Azure Cosmos DB gönderme mekanizması olan akış akışı.
* **Batch katmanı:** Batch katmanı, *ana veri kümesinden* (değişmez, yalnızca bir ham veri kümesi) oluşur ve **hizmet katmanına**gönderilen verilerin toplu iş görünümlerini önceden hesaplamanıza olanak sağlar.
   * **Lambda mimarisi Rearchitected - toplu iş katmanı** not defteri [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) sorguları *ana veri kümesi* Toplu görünüm kümesi.
* **Katman sunma:** **Hizmet veren katman** , hızlı sorgularda toplu görünümlere (örneğin toplamalar, belirli Dilimleyiciler vb.) neden olan önceden hesaplanan verilerden oluşur.
  * **Lambda mimarisi Rearchitected - Batch hizmet katmanına** not defteri [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) toplu veriler için Hizmet katmanını gönderir; diğer bir deyişle, Spark tweetleri bir toplu iş koleksiyonu sorgular, işler ve başka bir koleksiyona (hesaplanan bir batch) depolar.
    * **Hız katmanı:** **Hız katmanı** , okuma ve hemen işlem yapması için Azure Cosmos DB değişiklik akışını kullanan Spark 'dan oluşur. Veriler için de kaydedilebilir *RT hesaplanan* diğer sistemlere sorgulayabilmesi işlenen gerçek zamanlı verileri gerçek zamanlı bir çalışan aksine kendilerini sorgu.
  * [Akış sorgudan Cosmos DB değişiklik akışı](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala betik spark-shell gelen bir aralık sayısı işlem için akış Azure Cosmos DB değişiklik akışı bir sorgu yürütür.
  * [Akış etiketleri sorgudan Cosmos DB değişiklik akışı](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala betik spark-shell etiketlerini bir aralık sayısını hesaplamak için akış Azure Cosmos DB değişiklik akışı sorgu yürütür.
  
## <a name="next-steps"></a>Sonraki adımlar
Henüz yapmadıysanız, Spark Azure Cosmos DB Bağlayıcısı'ndan indirin [azure cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub deposu ve depodaki ek kaynakları keşfedin:
* [Lambda mimarisi](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Dağıtılmış toplamalar örnekleri](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Örnek betikler ve Not Defterleri](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Yapılandırılmış akış tanıtımları](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Değişiklik akışı tanıtımları](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Azure Cosmos DB değişiklik akışı ile Apache Spark'ı kullanarak değişiklikleri işlemeye Stream](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

İncelemek isteyebilirsiniz [Apache Spark SQL ve DataFrames veri kümeleri Kılavuzu](https://spark.apache.org/docs/latest/sql-programming-guide.html) ve [Azure HDInsight üzerinde Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) makalesi.

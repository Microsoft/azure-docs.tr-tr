---
title: HDInsight ile YARN 'de Spark Azure Cosmos DB Cassandra API erişme
description: Bu makalede, bkz. HDInsight ile YARN 'de Spark 'tan Azure Cosmos DB Cassandra API nasıl çalışılacağı
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: e09ac39037024b5c360af4bb6ada26590b142924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359606"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>HDInsight ile YARN 'de Spark Azure Cosmos DB Cassandra API erişme
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Bu makalede, Azure Cosmos DB Cassandra API, YARN 'deki Spark-Shell HDInsight-Spark ile nasıl erişebileceğiniz ele alınmaktadır. HDInsight, Microsoft 'un Azure 'da bulunan Hortonçalışmalar Hadoop PaaS 'in, Azure 'da, [bir.](../hdinsight/spark/apache-spark-overview.md)  Bu belgedeki içerik HDInsight-Spark 'a başvurduğundan, tüm Hadoop dağıtımları için geçerlidir.  

## <a name="prerequisites"></a>Önkoşullar

* [Sağlama Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Azure Cosmos DB bağlanma temellerini inceleyin Cassandra API](cassandra-spark-generic.md)

* [HDInsight-Spark kümesi sağlama](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Cassandra API ile çalışmak için kod örneklerini gözden geçirin](cassandra-spark-generic.md#next-steps)

* [İsterseniz doğrulama için csqlsh kullanın](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Spark2 ' de yapılandırma Cassandra API** -Cassandra için Spark Bağlayıcısı, Cassandra bağlantı ayrıntılarının Spark bağlamının bir parçası olarak başlatılmasını gerektirir. Jupyter Not defterini başlattığınızda Spark oturumu ve bağlamı zaten başlatılmış ve HDInsight varsayılan Jupyter Not defteri 'nin bir parçası olarak her yapılandırma kümesiyle tamamlanmadıkça, Spark bağlamını durdurup yeniden başlatmanız önerilmez. Tek bir geçici çözüm, Cassandra örneği ayrıntılarını doğrudan ambarı, Spark2 hizmet yapılandırmasına eklemektir. Bu, küme başına bir Spark2 hizmeti yeniden başlatması gerektiren tek seferlik bir etkinliktir.
 
  1. Ambarı, Spark2 Service 'e gidin ve yapılandırmalarını 'leri seçin

  2. Ardından özel spark2-Varsayılanlar ' a gidin ve aşağıdaki ile yeni bir özellik ekleyin ve Spark2 hizmetini yeniden başlatın:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Spark kabuğu 'ndan Cassandra API Azure Cosmos DB erişim

Spark kabuğu test/araştırma amaçları için kullanılır.

* Kümenizin Spark sürümüyle uyumlu gereken Maven bağımlılıklarıyla Spark-Shell ' i başlatın.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Bazı DDL ve DML işlemlerini yürütün

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* CRUD işlemlerini çalıştırma

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Jupi not defterlerinden Cassandra API Azure Cosmos DB erişim

HDInsight-Spark Zeppelin ve Jupyter Not defteri hizmetleriyle birlikte gelir. Bunlar, Scala ve Python destekleyen Web tabanlı dizüstü bilgisayar ortamlardır. Not defterleri etkileşimli araştırmacı analizler ve işbirliği için harika, ancak işlemsel/etkili işlemler için tasarlanmamıştır.

Aşağıdaki jupi Not defterleri, HDInsight Spark kümenize yüklenebilir ve Azure Cosmos DB Cassandra API çalışmaya yönelik hazırlık örnekleri sağlayabilir. `1.0-ReadMe.ipynb`Azure Cosmos DB Cassandra API bağlanmaya yönelik Spark hizmeti yapılandırmasını gözden geçirmek için ilk not defterini gözden geçirdiğinizden emin olun.

Bu not defterlerini [Azure-Cosmos-DB-Cassandra-api-Spark-Not defterleri-jupyıter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/main/scala/) altında makinenize indirin.
  
### <a name="how-to-upload"></a>Karşıya yükleme:
Jupyıter 'ı başlattığınızda Scala 'ya gidin. Önce bir dizin oluşturun ve ardından not defterlerini dizine yükleyin. Karşıya Yükle düğmesi üst, sağ taraftaki bir düğmedir.  

### <a name="how-to-run"></a>Nasıl çalıştırılır:
Not defterlerini ve her bir not defteri hücresini sırayla çalıştırın.  Tüm hücreleri yürütmek için her bir not defterinin en üstündeki Çalıştır düğmesine veya her bir hücre için SHIFT + enter 'a tıklayın.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Spark Scala programınızdaki Azure Cosmos DB Cassandra API erişim

Üretim aşamasındaki otomatikleştirilmiş süreçler için Spark programları [Spark-gönder](https://spark.apache.org/docs/latest/submitting-applications.html)aracılığıyla kümeye gönderilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir IDE 'de Spark Scala programı oluşturma ve yürütme için Livy aracılığıyla HDInsight Spark kümesine gönderme](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Spark Scala programından Azure Cosmos DB Cassandra API bağlama](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Cassandra API ile çalışmak için kod örneklerinin tüm listesi](cassandra-spark-generic.md)

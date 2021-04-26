---
title: Spark’taki Azure Cosmos DB Cassandra API’si tablolarında toplama işlemlerini
description: Bu makalede Spark 'tan Azure Cosmos DB Cassandra API tablolarına yönelik temel toplama işlemleri ele alınmaktadır
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5939690d3f2c0bb9affa3e2fb425b909b96cf002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087630"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Spark’taki Azure Cosmos DB Cassandra API’si tablolarında toplama işlemlerini 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Bu makalede Spark'tan Azure Cosmos DB Cassandra API tablolarında temel toplama işlemlerini gerçekleştirme adımları anlatılmaktadır. 

> [!NOTE]
> Sunucu tarafında filtreleme ve sunucu tarafı toplama şu anda Azure Cosmos DB Cassandra API desteklenmiyor.

## <a name="cassandra-api-configuration"></a>Cassandra API yapılandırması

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="sample-data-generator"></a>Örnek veri Oluşturucu

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Sayım işlemi


### <a name="rdd-api"></a>RDD APı 'SI

```scala
sc.cassandraTable("books_ks", "books").count
```

**Çıktıların**
```bash
res48: Long = 5
```

### <a name="dataframe-api"></a>Dataframe API 'SI

Veri çerçevelerine karşı say Şu anda desteklenmiyor.  Aşağıdaki örnek, geçici bir çözüm olarak veri çerçevesini bellekten kalıcı hale geçtikten sonra bir dataframe sayısının nasıl yürütüleceğini göstermektedir.

"Bellek yetersiz" sorunlarından kaçınmak için aşağıdaki kullanılabilir seçeneklerden bir [depolama seçeneği]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) belirleyin:

* MEMORY_ONLY: varsayılan depolama seçeneğidir. RDD 'YI, JVM 'de Serisi kaldırılan Java nesneleri olarak depolar. RDD belleğe uygun değilse, bazı bölümler önbelleğe alınmaz ve her gerektiğinde yeniden hesaplanır.

* MEMORY_AND_DISK: RDD 'YI, JVM 'de Serisi kaldırılan Java nesneleri olarak depolar. RDD belleğe uygun değilse, diske sığmayan bölümleri ve gerektiğinde bunları depotıkları konumdan okuyun.

* MEMORY_ONLY_SER (Java/Scala): RDD 'yi serileştirilmiş Java nesneleri olarak depolar-bölüm başına bir baytlık dizi. Bu seçenek, özellikle hızlı bir seri hale getirici kullanılırken, ancak daha fazla CPU kullanımı daha fazla olması halinde, serisi kaldırılan nesneler ile karşılaştırıldığında boşluk açısından etkilidir.

* MEMORY_AND_DISK_SER (Java/Scala): Bu depolama seçeneği MEMORY_ONLY_SER gibidir, tek fark, ihtiyaç duyulduklarında onları yeniden hesaplama yerine DISK belleğine sığmayan bölümleri taşmanızdır.

* DISK_ONLY: RDD bölümlerini yalnızca diskte depolar.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2...: Yukarıdaki düzeyler ile aynı, ancak her bölümü iki küme düğümünde çoğaltır.

* OFF_HEAP (deneysel): MEMORY_ONLY_SER benzer, ancak verileri yığın dışı bellekte depolar ve daha önce etkin olması için yığın dışı bellek gerektirir. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Ortalama işlem

### <a name="rdd-api"></a>RDD APı 'SI

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Çıktıların**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Dataframe API 'SI

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Çıktıların**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Çıktıların**
```
16.016000175476073
```

## <a name="min-operation"></a>Min işlemi

### <a name="rdd-api"></a>RDD APı 'SI

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Çıktıların**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Dataframe API 'SI

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Çıktıların**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Çıktıların**
```
11.33
```

## <a name="max-operation"></a>En fazla işlem

### <a name="rdd-api"></a>RDD APı 'SI

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Dataframe API 'SI

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Çıktıların**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Çıktıların**
```
22.45
```

## <a name="sum-operation"></a>Toplam işlem

### <a name="rdd-api"></a>RDD APı 'SI

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Çıktıların**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Dataframe API 'SI

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Çıktıların**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Çıktıların**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Popüler veya karşılaştırılabilir işlem

### <a name="rdd-api"></a>RDD APı 'SI

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Çıktıların**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Dataframe API 'SI

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Çıktıların**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Sonraki adımlar

Tablo kopyalama işlemlerini gerçekleştirmek için bkz.:

* [Tablo kopyalama işlemleri](cassandra-spark-table-copy-ops.md)

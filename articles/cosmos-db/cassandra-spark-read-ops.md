---
title: Spark kullanarak Cassandra API tablo verilerini okuma
titleSufix: Azure Cosmos DB
description: Bu makalede Azure Cosmos DB Cassandra API tablolarından verilerin nasıl okunacağı açıklanır.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: ceede96cbf3be12a6129e27d34e318e4c4163458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073505"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Spark kullanarak Azure Cosmos DB Cassandra API tablolarından veri okuma
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 Bu makalede, Spark 'dan Azure Cosmos DB Cassandra API depolanan verilerin nasıl okunacağı açıklanır.

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
## <a name="dataframe-api"></a>Dataframe API 'SI

### <a name="read-table-using-sessionreadformat-command"></a>Session. Read. Format komutunu kullanarak tablo oku

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Spark. Read. cassandraFormat kullanarak tablo oku 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Tablodaki belirli sütunları oku

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Filtreleri uygulama

Daha iyi iyileştirilmiş Spark sorgularına izin vermek için koşulları veritabanına gönderebilirsiniz. Koşul, genellikle WHERE yan tümcesinde bulunan true veya false döndüren bir sorgu üzerinde koşuldur. Bir koşul gönderimi, veritabanından alınan giriş sayısını azaltarak ve sorgu performansını geliştirerek veritabanı sorgusundaki verileri filtreler. Spark veri kümesi API 'SI varsayılan olarak, geçerli WHERE yan tümcelerini veritabanına otomatik olarak gönderir. 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

Fiziksel planın PushedFilters bölümü, GreaterThan Push-ın filtresini içerir. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="bölümler":::

## <a name="rdd-api"></a>RDD APı 'SI

### <a name="read-table"></a>Tablo oku
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Tablodaki belirli sütunları oku

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL görünümleri 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Veri çerçevesinden geçici bir görünüm oluşturma

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Görünümde sorgu çalıştırma

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Sonraki adımlar

Spark 'tan Azure Cosmos DB Cassandra API ile çalışmaya yönelik ek makaleler aşağıda verilmiştir:
 
 * [Upsert işlemler](cassandra-spark-upsert-ops.md)
 * [Silme işlemleri](cassandra-spark-delete-ops.md)
 * [Toplama işlemleri](cassandra-spark-aggregation-ops.md)
 * [Tablo kopyalama işlemleri](cassandra-spark-table-copy-ops.md)


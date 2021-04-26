---
title: Azure SYNAPSE Analytics için Apache Spark Linux Foundation Delta Gölü kullanma konusuna genel bakış
description: Azure SYNAPSE Analytics için Apache Spark Delta Gölü kullanarak ACID özellikleriyle tablo oluşturma ve kullanma hakkında bilgi edinin.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 1e0dfd597e7f445eeba6cef332d8ea12b27dc3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676310"
---
# <a name="linux-foundation-delta-lake-overview"></a>Linux Foundation Delta Gölü genel bakış

Bu makale, [buradan](https://docs.delta.io/latest/quick-start.html)özgün karşılığından daha fazla açıklık için uyarlanmıştır. Bu makale, [Delta](https://delta.io)Gölü ana özelliklerini hızlı bir şekilde araştırmanıza yardımcı olur. Makale, etkileşimli, toplu iş ve akış sorgularından Delta Lake tablolarının nasıl okunacağını ve yazılacağını gösteren kod parçacıkları sağlar. Kod parçacıkları burada da [Pyspark](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb)Not defterleri, burada [Scala](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb)ve [C# burada](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb) bulunabilir

Aşağıda ele alınacaktır:

* Bir tablo oluşturma
* Verileri okuma
* Tablo verilerini Güncelleştir
* Tablo verilerinin üzerine yaz
* Üzerine yazma olmadan koşullu güncelleştirme
* Zaman gezisini kullanarak verilerin eski sürümlerini okuyun
* Bir tabloya veri akışı yazma
* Tablodaki değişikliklerin akışını okuma
* SQL desteği

## <a name="configuration"></a>Yapılandırma

Aşağıda, ortamınız için uygun şekilde değişiklik yaptığınızdan emin olun.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Sonuç:

'/Delta/Delta-Table-335323 '

## <a name="create-a-table"></a>Bir tablo oluşturma

Bir Delta Gölü tablosu oluşturmak için, Delta biçiminde veri çerçevesini dışarı bir veri çerçevesi yazın. Biçimi Parquet, CSV, JSON vb. şekilde değiştirebilirsiniz.

Aşağıdaki kod, veri Çerçevinizden çıkarılan şemayı kullanarak yeni bir Delta Gölü tablo oluşturmayı gösterir.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Sonuç:

| ID|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Verileri okuma

Dosyaların yolunu ve Delta biçimini belirterek Delta Gölü tablonuzdaki verileri okuyabilirsiniz.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Sonuç:

| ID|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

Sonuçların sırası silinmeden önce açıkça belirtilmediği için sonuçların sırası yukarıdan farklıdır.

## <a name="update-table-data"></a>Tablo verilerini Güncelleştir

Delta Gölü, standart veri çerçevesi API 'Lerini kullanarak tabloları değiştirmek için çeşitli işlemleri destekler, bu da Delta biçiminin eklediği büyük geliştirmelerden biridir. Aşağıdaki örnek, tablodaki verilerin üzerine yazmak için bir toplu iş çalıştırır.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Sonuç:

| ID|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

Burada beş kaydın tümünün yeni değerleri tutacak şekilde güncelleştirildiğini görebilirsiniz.

## <a name="save-as-catalog-tables"></a>Katalog tabloları olarak kaydet

Delta Gölü, yönetilen veya dış Katalog tablolarına yazabilir.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Sonuç:

|database|         tableName|Istesel önemli|
|--------|------------------|-----------|
| default|externaldeltatable|      yanlış|
| default| manageddeltatable|      yanlış|

Bu kodla, yönetilen tablo olarak adlandırılan mevcut bir veri çerçevesinin kataloğunda yeni bir tablo oluşturdunuz. Ardından, katalogda dış tablo olarak adlandırılan mevcut konumu kullanan yeni bir dış tablo tanımlamış olursunuz. Çıktıda, oluşturulduktan bağımsız olarak her iki tabloyu da görebilirsiniz.

Artık bu tabloların her ikisinin de genişletilmiş özelliklerine bakabilirsiniz

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Sonuç:

|col_name                    |data_type                                                                                                    |comment|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|kimlik                          |bigint                                                                                                       |null   |
|                            |                                                                                                             |       |
|Ayrıntılı tablo bilgileri  |                                                                                                             |       |
|Veritabanı                    |default                                                                                                      |       |
|Tablo                       |manageddeltatable                                                                                            |       |
|Sahip                       |Güvenilen hizmet-Kullanıcı                                                                                         |       |
|Oluşturma Zamanı                |Sat Nis 25 00:35:34 UTC 2020                                                                                 |       |
|Son erişim                 |Per Oca 01 00:00:00 UTC 1970                                                                                 |       |
|Oluşturan                  |Spark 2.4.4.2.6.99.201-11401300                                                                              |       |
|Tür                        |LEBILMESI                                                                                                      |       |
|Sağlayıcı                    |delta                                                                                                        |       |
|Tablo Özellikleri            |[transient_lastDdlTime = 1587774934]                                                                           |       |
|İstatistikler                  |2407 bayt                                                                                                   |       |
|Konum                    |abfss://data @ <data lake> . DFS.Core.Windows.net/SYNAPSE/Workspaces/ <workspace name> /Warehouse/manageddeltatable|       |
|Serde kitaplığı               |org. Apache. Hadoop. Hive. serde2. Lazy. LazySimpleSerDe                                                           |       |
|InPutFormat                 |org. Apache. Hadoop. mapred. Sequencefileınputformat                                                             |       |
|OutputFormat                |org. Apache. Hadoop. Hive. QL. IO. HiveSequenceFileOutputFormat                                                    |       |
|Depolama özellikleri          |[serileştirme. Format = 1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Sonuç:

|col_name                    |data_type                                                             |comment|
|----------------------------|----------------------------------------------------------------------|-------|
|kimlik                          |bigint                                                                |null   |
|                            |                                                                      |       |
|Ayrıntılı tablo bilgileri  |                                                                      |       |
|Veritabanı                    |default                                                               |       |
|Tablo                       |externaldeltatable                                                    |       |
|Sahip                       |Güvenilen hizmet-Kullanıcı                                                  |       |
|Oluşturma Zamanı                |Sat Nis 25 00:35:38 UTC 2020                                          |       |
|Son erişim                 |Per Oca 01 00:00:00 UTC 1970                                          |       |
|Oluşturan                  |Spark 2.4.4.2.6.99.201-11401300                                       |       |
|Tür                        |DıŞARıDAKI                                                              |       |
|Sağlayıcı                    |TAMAMLANMASı                                                                 |       |
|Tablo Özellikleri            |[transient_lastDdlTime = 1587774938]                                    |       |
|Konum                    |abfss://data @ <data lake> . DFS.Core.Windows.net/Delta/Delta-Table-587152|       |
|Serde kitaplığı               |org. Apache. Hadoop. Hive. serde2. Lazy. LazySimpleSerDe                    |       |
|InPutFormat                 |org. Apache. Hadoop. mapred. Sequencefileınputformat                      |       |
|OutputFormat                |org. Apache. Hadoop. Hive. QL. IO. HiveSequenceFileOutputFormat             |       |
|Depolama özellikleri          |[serileştirme. Format = 1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Üzerine yazma olmadan koşullu güncelleştirme

Delta Gölü, koşullu güncelleştirme, silme ve birleştirme için programlı API 'Ler sağlar (Bu, yaygın olarak tablo olarak adlandırılır).

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Sonuç:

| ID|
|---|
|106|
|108|
|  5|
|  7|
|  9|

Burada her iki KIMLIĞI de 100.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Sonuç:

| ID|
|---|
|  5|
|  7|
|  9|

Her iki satır da silinmiş olduğuna dikkat edin.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Sonuç:

| ID|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

Burada, mevcut verilerin bir birleşimini görürsünüz. Var olan verilere güncelleştirme (Wheneşleşti) kod yolunda-1 değeri atandı. Kod parçacığının en üstünde oluşturulan ve ekleme kodu yolu (WhenNotMatched) aracılığıyla eklenen yeni veriler de eklenmiştir.

### <a name="history"></a>Geçmiş

Delta Gölü, bir tablonun geçmişine bakmak için izin verebilir. Diğer bir deyişle, temel Delta tablosunda yapılan değişiklikler. Aşağıdaki hücrede, geçmişi incelemeye ne kadar basit olduğu gösterilmektedir.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Sonuç:

|sürüm|          timestamp|userId|userName|operation|                                                operationParameters| iş|not defteri|Lclusterıd|readVersion|'Sinden|IBU Dappend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  null|    null|    BIRLEÞTIRMEK|                       [koşul-> (oldData.`ID` = newData. `ID` )]|null|    null|     null|          3|          null|        yanlış|
|      3|2020-04-25 00:36:08|  null|    null|   DELETE|[koşul-> ["(( `ID` % cast (Iç BIGıNT)) = Cast (0 as bigint))"]]|null|    null|     null|          2|          null|        yanlış|
|      2|2020-04-25 00:35:51|  null|    null|   UPDATE| [koşul-> ((KIMLIK # 744L% cast (2 bigint)) = atama (0 as bigint))]|null|    null|     null|          1|          null|        yanlış|
|      1|2020-04-25 00:35:05|  null|    null|    YAZARKEN|                             [mode-> üzerine yazma, partitionBy-> []]|null|    null|     null|          0|          null|        yanlış|
|      0|2020-04-25 00:34:34|  null|    null|    YAZARKEN|                         [mode-> ErrorIfExists, partitionBy-> []]|null|    null|     null|       null|          null|         true|

Burada, yukarıdaki kod parçacıkları üzerinde yapılan tüm değişiklikleri görebilirsiniz.

## <a name="read-older-versions-of-data-using-time-travel"></a>Zaman gezisini kullanarak verilerin eski sürümlerini okuyun

Zaman gezme adlı bir özellik kullanarak, Delta Gölü tablonuzun önceki anlık görüntülerini sorgulamak mümkündür. Üzerine yazıldığımız verilere erişmek istiyorsanız, versionAsOf seçeneğini kullanarak ilk veri kümesini üzerine yazmadan önce tablonun anlık görüntüsünü sorgulayabilirsiniz.

Aşağıdaki hücreyi çalıştırdığınızda, üzerine yazmadan önce ilk veri kümesini görmeniz gerekir. Seyahat süresi, artık tabloda olmayan verilere erişmek için Delta Gölü işlem günlüğü gücünden faydalanan son derece güçlü bir özelliktir. Sürüm 0 seçeneğini (veya sürüm 1 ' i belirterek) kaldırmak, daha yeni verileri yeniden görmenizi sağlar. Daha fazla bilgi için bkz. [bir tablonun daha eski bir anlık görüntüsünü sorgulama](https://docs.delta.io/latest/delta-batch.html#deltatimetravel).

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Sonuç:

| ID|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

Burada, verilerin en eski sürümüne geri gittiğinden ulaşabilirsiniz.

## <a name="write-a-stream-of-data-to-a-table"></a>Bir tabloya veri akışı yazma

Spark 'ın yapılandırılmış akışını kullanarak bir Delta Gölü tablosuna da yazabilirsiniz. Delta Gölü işlem günlüğü, tabloya göre eşzamanlı olarak çalışan başka akışlar veya toplu iş sorguları olsa bile, işleme tam olarak bir kez işlem sağlar. Varsayılan olarak, akışlar tabloya yeni kayıtlar ekleyen Append modunda çalışır.

Yapılandırılmış akış ile Delta Gölü tümleştirmesi hakkında daha fazla bilgi için bkz. [tablo akışı okuma ve yazma](https://docs.delta.io/latest/delta-streaming.html).

Aşağıdaki hücrelerde yaptığımız aşağıda verilmiştir:

* Hücre 30 yeni eklenen verileri göster
* Hücre 31 Inceleme geçmişi
* Hücre 32 yapılandırılmış akış işini durdur
* Hücre 33 Inceleme geçmişi <--ekleme durduğunu fark edersiniz

İlk olarak bir sıra oluşturmak için basit bir Spark akış işi ayarlayacağım ve işi Delta tablonuza yazacak şekilde yaparsınız.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Tablodaki değişikliklerin akışını okuma

Akış, Delta Gölü tablosuna yazılırken, bu tablodan akış kaynağı olarak da okuyabilirsiniz. Örneğin, Delta Gölü tablosunda yapılan tüm değişiklikleri yazdıran başka bir akış sorgusu başlatabilirsiniz.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Sonuç:

| ID|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Sonuç:

|sürüm|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|AKıŞ GÜNCELLEŞTIRME|[outputMode-> Append, QueryId-> d26b4f8a-7e5a-44f2-A5FB-23a7bd02aef7, dönemler CHID-> 0]|          4|
|      4|2020-04-25 00:36:27|           BIRLEÞTIRMEK|                                         [koşul-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [koşul-> ["(( `id` % cast (Iç BIGıNT)) = Cast (0 as bigint))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [koşul-> ((kimlik # 744L% cast (2 bigint)) = atama (0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           YAZARKEN|                                               [mode-> üzerine yazma, partitionBy-> []]|          0|
|      0|2020-04-25 00:34:34|           YAZARKEN|                                           [mode-> ErrorIfExists, partitionBy-> []]|       null|

Burada, geçmiş görünümünün görüntüleme deneyimini basitleştirmek için daha az ilgi çekici sütundan bazılarını bırakmakta olursunuz.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Sonuç:

|sürüm|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|AKıŞ GÜNCELLEŞTIRME|[outputMode-> Append, QueryId-> d26b4f8a-7e5a-44f2-A5FB-23a7bd02aef7, dönemler CHID-> 0]|          4|
|      4|2020-04-25 00:36:27|           BIRLEÞTIRMEK|                                         [koşul-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [koşul-> ["(( `id` % cast (Iç BIGıNT)) = Cast (0 as bigint))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [koşul-> ((kimlik # 744L% cast (2 bigint)) = atama (0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           YAZARKEN|                                               [mode-> üzerine yazma, partitionBy-> []]|          0|
|      0|2020-04-25 00:34:34|           YAZARKEN|                                           [mode-> ErrorIfExists, partitionBy-> []]|       null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Parquet 'i Delta 'ya Dönüştür

Parquet biçiminden Delta 'a yerinde dönüştürme yapabilirsiniz.

Burada, var olan tablonun Delta biçiminde olup olmadığını test edersiniz.
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Sonuç:

Yanlış

Artık verileri Delta biçimine dönüştürecek ve çalıştığını doğrulayacağız.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Sonuç:

Doğru

## <a name="sql-support"></a>SQL desteği

Delta, SQL aracılığıyla tablo yardımcı programı komutlarını destekler. SQL 'i kullanarak şunları yapabilirsiniz:

* DeltaTable geçmişini al
* DeltaTable
* Bir Parquet dosyasını Delta 'a dönüştürme

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Sonuç:

|sürüm|          timestamp|userId|userName|       operation| operationParameters| iş|not defteri|Lclusterıd|readVersion|'Sinden|IBU Dappend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  null|    null|AKıŞ GÜNCELLEŞTIRME|[outputMode-> AP...|null|    null|     null|          4|          null|         true|
|      4|2020-04-25 00:36:27|  null|    null|           BIRLEÞTIRMEK|[koşul-> (ol...|null|    null|     null|          3|          null|        yanlış|
|      3|2020-04-25 00:36:08|  null|    null|          DELETE|[koşul-> ["(...|null|    null|     null|          2|          null|        yanlış|
|      2|2020-04-25 00:35:51|  null|    null|          UPDATE|[koşul-> ((i...|null|    null|     null|          1|          null|        yanlış|
|      1|2020-04-25 00:35:05|  null|    null|           YAZARKEN|[mode-> fazla yazıldı...|null|    null|     null|          0|          null|        yanlış|
|      0|2020-04-25 00:34:34|  null|    null|           YAZARKEN|[mode-> ErrorIfE...|null|    null|     null|       null|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Sonuç:

|                path|
|--------------------|
|abfss://data@arca...|

Artık bir tablonun bir Delta biçim tablosu olduğunu doğrulayacağız, sonra Spark SQL kullanarak değişim biçimine dönüştürebilmekte ve doğru şekilde dönüştürüldüğüne emin oldunuz.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Sonuç:

Doğru

Tam belgeler için bkz. Delta Gölü [belge sayfası](https://docs.delta.io/latest/delta-intro.html)

Daha fazla bilgi için bkz. [Delta Lake projesi](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Spark belgeleri için .NET](/dotnet/spark)
* [Azure Synapse Analytics](../index.yml)
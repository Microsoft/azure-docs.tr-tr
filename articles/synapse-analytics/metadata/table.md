---
title: Paylaşılan meta veri tabloları
description: Azure SYNAPSE Analytics, sunucusuz Apache Spark havuzunda tablo oluşturmak, verileri çoğaltmadan sunucusuz SQL havuzundan ve adanmış SQL havuzundan erişilebilir hale getirir.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: a8080720480beaeb7bc8692f2dcddddad5da0e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548470"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure SYNAPSE Analytics paylaşılan meta veri tabloları


Azure SYNAPSE Analytics, farklı çalışma alanı hesaplama altyapılarının, Apache Spark havuzları ve sunucusuz SQL havuzu arasında veritabanlarını ve Parquet tarafından desteklenen tabloları paylaşmasına izin verir.

Bir Spark işi tarafından bir veritabanı oluşturulduktan sonra, depolama biçimi olarak Parquet kullanan Spark ile birlikte tablo oluşturabilirsiniz. Tablo adları, küçük harflere dönüştürülecek ve küçük harf adı kullanılarak sorgulanmalıdır. Bu tablolar, Azure SYNAPSE çalışma alanı Spark havuzlarından herhangi biri tarafından sorgulanarak hemen kullanılabilir hale gelir. Bunlar ayrıca, izinlerle ilgili Spark işlerinin herhangi birinden de kullanılabilir.

Spark oluşturulan, yönetilen ve dış tablolar aynı zamanda sunucusuz SQL havuzundaki karşılık gelen eşitlenmiş veritabanında aynı ada sahip harici tablolar olarak da kullanılabilir hale getirilir. [SQL 'de Spark tablosunun kullanıma](#expose-a-spark-table-in-sql) sunulması tablo eşitlemesi hakkında daha fazla ayrıntı sağlar.

Tablolar sunucusuz SQL havuzunun zaman uyumsuz olarak eşitlendiğinden, görünene kadar bir gecikme olur.

## <a name="manage-a-spark-created-table"></a>Spark tarafından oluşturulan bir tabloyu yönetme

Spark tarafından oluşturulan veritabanlarını yönetmek için Spark 'ı kullanın. Örneğin, bir sunucusuz Apache Spark havuzu işi aracılığıyla silin ve Spark ' dan tablo oluşturun.

Bu tür bir veritabanında sunucusuz SQL havuzundan nesne oluşturursanız veya veritabanını bırakmaya çalışırsanız, işlem başarısız olur. Özgün Spark veritabanı sunucusuz SQL havuzu aracılığıyla değiştirilemez.

## <a name="expose-a-spark-table-in-sql"></a>SQL 'de Spark tablosu kullanıma sunma

### <a name="shared-spark-tables"></a>Paylaşılan Spark tabloları

Spark, Azure SYNAPSE 'in SQL 'de otomatik olarak sunduğu iki tür tablo sağlar:

- Yönetilen tablolar

  Spark, metın, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA ve LIBSVM gibi yönetilen tablolarda verilerin depolanması için birçok seçenek sunar. Bu dosyalar normalde `warehouse` yönetilen tablo verilerinin depolandığı dizinde depolanır.

- Dış tablolar

  Spark ayrıca, ya da Hive biçimini kullanarak var olan veriler üzerinde dış tablo oluşturmak için yollar sağlar `LOCATION` . Bu tür dış tablolar, Parquet dahil çeşitli veri biçimlerinin üzerinde olabilir.

Azure SYNAPSE Şu anda yalnızca SQL altyapılarıyla verileri Parquet formatında depolayan yönetilen ve harici Spark tablolarını paylaşır. Diğer biçimler tarafından desteklenen tablolar otomatik olarak eşitlenmez. SQL altyapısı tablonun temel biçimini destekliyorsa, bu tür tabloları kendi SQL veritabanınızda bir dış tablo olarak doğrudan eşitleyebilirsiniz.

### <a name="share-spark-tables"></a>Spark tablolarını paylaşma

SQL altyapısında aşağıdaki özelliklerle dış tablolar olarak sunulan paylaşılabilir yönetilen ve dış Spark tabloları:

- SQL dış tablosunun veri kaynağı Spark tablosunun konum klasörünü temsil eden veri kaynağıdır.
- SQL dış tablosunun dosya biçimi Parquet 'dir.
- SQL dış tablosunun erişim kimlik bilgileri geçişdir.

Tüm Spark tablosu adları geçerli SQL tablo adları olduğundan ve Spark sütun adları geçerli SQL sütun adları olduğundan, SQL dış tablosu için Spark tablosu ve sütun adları kullanılacaktır.

Spark tabloları, SYNAPSE SQL altyapılarından farklı veri türleri sağlar. Aşağıdaki tabloda Spark tablosu veri türleri SQL türleriyle eşlenir:

| Spark veri türü | SQL veri türü | Yorumlar |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Harmanlama ile `Latin1_General_100_BIN2_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Harmanlama ile JSON içine dizileştirir `Latin1_General_100_BIN2_UTF8` |
| `map`       |    `varchar(max)`   | Harmanlama ile JSON içine dizileştirir `Latin1_General_100_BIN2_UTF8` |
| `struct`    |    `varchar(max)`   | Harmanlama ile JSON içine dizileştirir `Latin1_General_100_BIN2_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Güvenlik modeli

Spark veritabanlarının ve tablolarının yanı sıra SQL altyapısındaki eşitlenmiş temsiller, temel alınan depolama düzeyinde güvenlik altına alınacaktır. Şu anda nesnelerin kendileri üzerinde izinleri olmadığından, nesneler Nesne Gezgini 'nde görülebilir.

Yönetilen tabloyu oluşturan güvenlik sorumlusu o tablonun sahibi kabul edilir ve hem o tablo hem de temel klasörler ve dosyalar üzerinde tüm haklara sahip olur. Buna ek olarak, veritabanının sahibi otomatik olarak tablonun ikincil sahibi olur.

Kimlik doğrulaması geçişiyle bir Spark veya SQL dış tablosu oluşturursanız, veriler yalnızca klasör ve dosya düzeylerinde güvenlik altına alır. Birisi bu tür bir dış tabloyu sorgularsa, sorguyu gönderenin güvenlik kimliği dosya sistemine geçirilir ve orada erişim hakları denetlenir.

Klasörler ve dosyalar üzerinde izinlerin nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. [Azure SYNAPSE Analytics Shared Database](database.md).

## <a name="examples"></a>Örnekler

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Spark ve sunucusuz SQL havuzundan sorgu ile desteklenen bir yönetilen tablo oluşturun

Bu senaryoda adlı bir Spark veritabanınız vardır `mytestdb` . Bkz. [sunucusuz SQL havuzu Ile Spark veritabanı oluşturma ve bu veritabanına bağlanma](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Aşağıdaki komutu çalıştırarak, mini bir SQL ile yönetilen Spark tablosu oluşturun:

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

Bu komut, tabloyu `myparquettable` veritabanında oluşturur `mytestdb` . Tablo adları, küçük harfe dönüştürülecek. Kısa bir gecikmeden sonra, tabloyu sunucusuz SQL havuzunuzdaki görebilirsiniz. Örneğin, sunucusuz SQL havuzunuzdaki aşağıdaki ifadeyi çalıştırın.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

`myparquettable`Sonuçlara dahil edildiğini doğrulayın.

>[!NOTE]
>Depolama biçimi olarak Parquet kullanmayan bir tablo eşitlenmeyecektir.

Sonra, Spark 'dan tabloya bazı değerler ekleyin, örneğin bir C# not defterinde aşağıdaki C# Spark deyimleriyle.

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

Artık sunucusuz SQL havuzunuzdaki verileri şu şekilde okuyabilirsiniz:

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

Sonuç olarak aşağıdaki satırı almalısınız:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Spark ve sunucusuz SQL havuzundan sorgu ile desteklenen bir dış tablo oluşturun

Bu örnekte, yönetilen tablo için önceki örnekte oluşturulan Parquet veri dosyaları üzerinde bir dış Spark tablosu oluşturun.

Örneğin, Mini SQL çalıştırması ile:

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Yer tutucusunu, `<fs>` çalışma alanı varsayılan dosya sistemi olan dosya sistemi adıyla ve `<synapse_ws>` Bu örneği çalıştırmak için kullandığınız SYNAPSE çalışma alanının adı ile birlikte yer tutucu ile değiştirin.

Önceki örnekte, tablosu veritabanında oluşturulur `myextneralparquettable` `mytestdb` . Kısa bir gecikmeden sonra, tabloyu sunucusuz SQL havuzunuzdaki görebilirsiniz. Örneğin, sunucusuz SQL havuzunuzdaki aşağıdaki ifadeyi çalıştırın.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

`myexternalparquettable`Sonuçlara dahil edildiğini doğrulayın.

Artık sunucusuz SQL havuzunuzdaki verileri şu şekilde okuyabilirsiniz:

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
```

Sonuç olarak aşağıdaki satırı almalısınız:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SYNAPSE Analytics 'te paylaşılan meta veriler hakkında daha fazla bilgi edinin](overview.md)
- [Azure SYNAPSE Analytics ' paylaşılan meta veri veritabanı hakkında daha fazla bilgi edinin](database.md)



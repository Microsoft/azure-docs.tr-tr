---
title: Sunucusuz Apache Spark havuzları ve SQL havuzları arasında verileri içeri ve dışarı aktarma
description: Bu makalede özel bağlayıcının, verileri adanmış SQL havuzları ve sunucusuz Apache Spark havuzları arasında taşımak için nasıl kullanılacağı hakkında bilgi verilmektedir.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 66a804f645ee56cce62053a51e6aa4fc25b3c1a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671333"
---
# <a name="introduction"></a>Giriş

Azure SYNAPSE Apache Spark to SYNAPSE SQL Connector, Azure SYNAPSE 'deki sunucusuz Apache Spark havuzlar ve adanmış SQL havuzları arasında verileri verimli bir şekilde aktarmak için tasarlanmıştır. Azure SYNAPSE Apache Spark to SYNAPSE SQL Connector yalnızca adanmış SQL havuzları üzerinde çalışır, sunucusuz SQL havuzu ile çalışmaz.

> [!WARNING]
> **Sqlanalytics ()** işlev adı **synapsesql ()** olarak değiştirildi. Sqlanalytics işlevi çalışmaya devam eder, ancak kullanım dışı olacaktır.  Gelecekte herhangi bir kesinti oluşmasını önlemek için lütfen **sqlanalytics ()** ile **synapsesql ()** arasındaki başvuruyu değiştirin.

## <a name="design"></a>Tasarım

Spark havuzları ve SQL havuzları arasında veri aktarımı, JDBC kullanılarak yapılabilir. Bununla birlikte, Spark ve SQL havuzları gibi iki Dağıtılmış Sistem, JDBC, seri veri aktarımı ile ilgili bir performans sorunu olduğunu eğilimi gösterir.

Azure SYNAPSE Apache Spark havuzu SYNAPSE SQL Bağlayıcısı Apache Spark için bir veri kaynağı uygulamasıdır. Spark kümesi ve SYNAPSE adanmış SQL örneği arasında verimli bir şekilde veri aktarmak için adanmış SQL havuzlarında Azure Data Lake Storage 2. ve PolyBase 'i kullanır.

![Bağlayıcı mimarisi](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te kimlik doğrulaması

Sistemler arasında kimlik doğrulaması, Azure SYNAPSE Analytics 'te sorunsuz hale getirilir. Belirteç hizmeti, depolama hesabına veya veri ambarı sunucusuna erişirken kullanılacak güvenlik belirteçlerini almak için Azure Active Directory ile bağlanır.

Bu nedenle, Azure AD-Auth depolama hesabında ve veri ambarı sunucusunda yapılandırıldığı sürece kimlik bilgileri oluşturmanız veya bunları bağlayıcı API 'de belirtmeniz gerekmez. Aksi takdirde, SQL kimlik doğrulaması belirlenebilir. [Kullanım](#usage) bölümünde daha fazla ayrıntı bulun.

## <a name="constraints"></a>Kısıtlamalar

- Bu bağlayıcı yalnızca Scala 'da kullanılabilir.
- PySpark için bkz. [Python kullanma](#use-pyspark-with-the-connector) bölümündeki ayrıntılar.
- Bu bağlayıcı SQL görünümlerinin sorgulanmasını desteklemez.

## <a name="prerequisites"></a>Önkoşullar

- Veri aktarmak istediğiniz veritabanında/SQL havuzunda **db_exporter** rolün bir üyesi olmalıdır.
- Varsayılan depolama hesabındaki Depolama Blobu veri katılımcısı rolünün bir üyesi olmalıdır.

Kullanıcı oluşturmak için SQL havuzu veritabanına bağlanın ve aşağıdaki örnekleri izleyin:

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Rol atamak için:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>Kullanım

İçeri aktarma deyimleri gerekli değildir, Not defteri deneyimi için önceden içeri aktarılır.

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-attached-within-the-workspace"></a>Çalışma alanı içinde bağlı adanmış bir SQL havuzuna veya veri aktarımı

> [!NOTE]
> **Not defteri deneyiminde içeri aktarmalar gerekmez**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API 'YI oku

```scala
val df = spark.read.synapsesql("<DBName>.<Schema>.<TableName>")
```

Yukarıdaki API, hem Iç (yönetilen) hem de SQL havuzundaki dış tablolar için çalışacaktır.

#### <a name="write-api"></a>Yazma API 'SI

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

Yazma API 'SI, tabloyu adanmış SQL havuzunda oluşturur ve sonra verileri yüklemek için PolyBase 'i çağırır.  Tablo adanmış SQL havuzunda bulunmamalıdır veya "zaten adlı bir nesne var..." şeklinde bir hata döndürülür.

TableType değerleri

- Sabitler. adanmış SQL havuzunda Iç yönetilen tablo
- Sabit bir SQL havuzunda sabit dış tablo

SQL havuzu tarafından yönetilen tablo

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

SQL havuzu dış tablosu

Adanmış bir SQL havuzu dış tablosuna yazmak için, adanmış SQL havuzunda bir dış VERI kaynağı ve bir dış dosya BIÇIMI bulunmalıdır.  Daha fazla bilgi için adanmış SQL havuzunda [dış veri kaynağı](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) ve [Harici dosya biçimleri](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) oluşturma makalesini okuyun.  Aşağıda, adanmış SQL havuzunda dış veri kaynağı ve harici dosya biçimleri oluşturma örnekleri verilmiştir.

```sql
--For an external table, you need to pre-create the data source and file format in dedicated SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

Depolama hesabına Azure Active Directory geçişli kimlik doğrulaması kullanılırken bir dış KIMLIK BILGISI nesnesi gerekli değildir.  Depolama hesabındaki "Depolama Blobu verileri katılımcısı" rolünün bir üyesi olduğunuzdan emin olun.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    synapsesql("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-or-database-outside-the-workspace"></a>Özel bir SQL havuzuna veya çalışma alanı dışında bir veritabanına veri aktarma

> [!NOTE]
> Not defteri deneyiminde içeri aktarmalar gerekmez

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API 'YI oku

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Yazma API 'SI

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-azure-ad"></a>Azure AD yerine SQL auth kullanın

#### <a name="read-api"></a>API 'YI oku

Şu anda bağlayıcı, belirteç tabanlı kimlik doğrulamasını, çalışma alanının dışında olan adanmış bir SQL havuzuna desteklemez. SQL kimlik doğrulaması kullanmanız gerekir.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Yazma API 'SI

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-pyspark-with-the-connector"></a>Bağlayıcıyla PySpark kullanma

> [!NOTE]
> Bu örnek yalnızca akılda tutulan Not defteri deneyimiyle verilmiştir.

DW içine yazmak istediğiniz bir "pyspark_df" veri çerçevesine sahip olduğunu varsayalım.

PySpark içindeki dataframe kullanarak geçici bir tablo oluşturun:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Mıknatıccs kullanarak PySpark not defterinde bir Scala hücresi çalıştırın:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

scala_df.write.synapsesql("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Benzer şekilde, okuma senaryosunda, Scala kullanarak verileri okuyun ve geçici bir tabloya yazın ve geçici tabloyu bir veri çerçevesinde sorgulamak için PySpark içinde Spark SQL kullanın.

## <a name="allow-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>Diğer kullanıcıların, çalışma alanınızdaki SQL bağlayıcısını SYNAPSE için Azure SYNAPSE Apache Spark kullanmasına izin ver

Diğer kullanıcıların eksik izinlerini değiştirmek için, çalışma alanına bağlı ADLS 2. depolama hesabında Depolama Blobu veri sahibi olmanız gerekir. Kullanıcının çalışma alanına erişimi olduğundan ve not defterlerini çalıştırma izinlerine sahip olduğundan emin olun.

### <a name="option-1"></a>1\. Seçenek

- Kullanıcıya Depolama Blobu verileri katılımcısı/Owner yapın

### <a name="option-2"></a>2\. Seçenek

- Klasör yapısında aşağıdaki ACL 'Leri belirtin:

| Klasör | / | synapse | çalışma alanı  | \<workspacename> | Mini veri havuzları | \<sparkpoolname>  | parlak havuzörnekleri  |
|--|--|--|--|--|--|--|--|
| Erişim Izinleri | --X | --X | --X | --X | --X | --X | -WX |
| Varsayılan Izinler | ---| ---| ---| ---| ---| ---| ---|

- "SYNAPSE" ve Azure portal 'den aşağı doğru tüm klasörlere ACL 'leri sağlayabilmelisiniz. Kök "/" klasörüne ACL eklemek için lütfen aşağıdaki yönergeleri izleyin.

- Azure AD kullanarak Depolama Gezgini çalışma alanıyla bağlantılı depolama hesabına bağlanma
- Hesabınızı seçin ve çalışma alanı için ADLS 2. URL 'sini ve varsayılan dosya sistemini verin
- Listelenen depolama hesabını gördüğünüzde, liste çalışma alanına sağ tıklayın ve "erişimi yönet" i seçin
- Kullanıcıyı "Yürüt" erişim Izniyle/klasörüne ekleyin. "Tamam" ı seçin

> [!IMPORTANT]
> ' Yi planlamıyorsanız "varsayılan" seçeneğini belirlediğinizden emin olun.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal kullanarak adanmış bir SQL havuzu oluşturma](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Azure portal kullanarak yeni bir Apache Spark havuzu oluşturma](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 

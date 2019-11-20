---
title: Azure SQL veritabanı ve SQL Server Spark Bağlayıcısı
description: Azure SQL veritabanı ve SQL Server Spark bağlayıcısını nasıl kullanacağınızı öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 03150ef3a8799cd0f84fb1bc03f2fd712cddd541
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889806"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Azure SQL veritabanı ve SQL Server için Spark Connector ile gerçek zamanlı büyük veri analizlerini hızlandırma

Azure SQL veritabanı ve SQL Server Spark Bağlayıcısı, Azure SQL veritabanı ve SQL Server dahil olmak üzere SQL veritabanlarının, Spark işleri için giriş veri kaynağı veya çıkış veri havuzu olarak çalışmasını sağlar. Büyük veri analizindeki gerçek zamanlı işlem verilerini kullanmanızı ve geçici sorgular veya raporlama sonuçlarını kalıcı hale getirme olanağı sağlar. Yerleşik JDBC Bağlayıcısı ile karşılaştırıldığında, bu bağlayıcı, verileri SQL veritabanlarına toplu olarak ekleme olanağı sunar. Satır ekleyerek 10 kat daha hızlı performans ile 20 kat daha hızlı performans sağlayabilir. Azure SQL veritabanı ve SQL Server Spark Bağlayıcısı AAD kimlik doğrulamasını da destekler. AAD hesabınızı kullanarak Azure Databricks Azure SQL veritabanınıza güvenli bir şekilde bağlanmanızı sağlar. Yerleşik JDBC Bağlayıcısı ile benzer arabirimler sağlar. Mevcut Spark işlerinizin bu yeni bağlayıcıyı kullanmak için geçirilmesi kolaydır.

## <a name="download"></a>İndir
Başlamak için, GitHub 'daki [Azure-SQLDB-Spark deposundan](https://github.com/Azure/azure-sqldb-spark) Spark ' ı SQL DB bağlayıcısına indirin.

## <a name="official-supported-versions"></a>Resmi desteklenen sürümler

| Bileşen                            |Sürüm                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 veya üzeri           |
| Scala                                |2,10 veya üzeri            |
| SQL Server için Microsoft JDBC Sürücüsü |6,2 veya üzeri             |
| Microsoft SQL Server                 |SQL Server 2008 veya üzeri |
| Azure SQL Veritabanı                   |Destekleniyor                |

Azure SQL veritabanı ve SQL Server Spark Bağlayıcısı, verileri Spark çalışan düğümleri ve SQL veritabanları arasında taşımak için SQL Server Microsoft JDBC sürücüsü kullanır:
 
Veri akışı aşağıdaki gibidir:
1. Spark ana düğümü SQL Server veya Azure SQL veritabanı 'na bağlanır ve verileri belirli bir tablodan veya belirli bir SQL sorgusunu kullanarak yükler
2. Spark ana düğümü, dönüştürme için verileri çalışan düğümlerine dağıtır. 
3. Çalışan düğümü SQL Server veya Azure SQL veritabanı 'na bağlanır ve veritabanına veri yazar. Kullanıcı, satır satır ekleme veya toplu ekleme kullanmayı seçebilir.

Aşağıdaki diyagramda veri akışı gösterilmektedir.

   ![architecture](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Spark 'ı SQL DB bağlayıcısına oluşturma
Şu anda bağlayıcı projesi Maven kullanır. Bağlayıcıyı bağımlılıklar olmadan derlemek için şunu çalıştırabilirsiniz:

- MVN Temizleme paketi
- Yayın klasöründen JAR 'nin en son sürümlerini indirin
- SQL DB Spark JAR 'i ekleme

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Bağlayıcıyı kullanarak Spark 'ı SQL DB 'ye bağlama
Azure SQL veritabanı 'na veya Spark işlerinin SQL Server bağlanabilir, verileri okuyabilir veya yazabilirsiniz. Ayrıca, bir Azure SQL veritabanında veya SQL Server veritabanında DML veya DDL sorgusu da çalıştırabilirsiniz.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Azure SQL veritabanı veya SQL Server verilerini okuyun

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Azure SQL veritabanı 'ndan veya belirtilen SQL sorgusuyla SQL Server veri okuma
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Verileri Azure SQL veritabanı 'na veya SQL Server yazma
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Azure SQL veritabanı 'nda DML veya DDL sorgusu çalıştırın veya SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>AAD kimlik doğrulaması kullanarak Spark 'ı Azure SQL veritabanı 'na bağlama
Azure SQL veritabanı 'na Azure Active Directory (AAD) kimlik doğrulaması kullanarak bağlanabilirsiniz. Veritabanı kullanıcılarının kimliklerini merkezi olarak yönetmek için AAD kimlik doğrulamasını kullanın ve SQL Server kimlik doğrulamasına alternatif olarak.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>ActiveDirectoryPassword kimlik doğrulama modunu kullanarak bağlanma
#### <a name="setup-requirement"></a>Kurulum gereksinimi
ActiveDirectoryPassword kimlik doğrulama modunu kullanıyorsanız, [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) ve bağımlılıklarını Indirmeniz ve Java derleme yoluna dahil etmeniz gerekir.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Erişim belirteci kullanarak bağlanma
#### <a name="setup-requirement"></a>Kurulum gereksinimi
Erişim belirteci tabanlı kimlik doğrulama modunu kullanıyorsanız, [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) ve bağımlılıklarını indirmeniz ve bunları Java derleme yoluna eklemeniz gerekir.

Azure SQL veritabanınıza erişim belirtecini nasıl alabileceğinizi öğrenmek için bkz. [SQL veritabanı ile kimlik doğrulaması için Azure Active Directory kimlik doğrulaması kullanma](sql-database-aad-authentication.md) .

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Toplu ekleme kullanarak Azure SQL veritabanı 'na veya SQL Server veri yazma
Geleneksel JDBC Bağlayıcısı, verileri Azure SQL veritabanı 'na veya satır satır ekleme kullanarak SQL Server yazar. Toplu ekleme kullanarak SQL veritabanı 'na veri yazmak için Spark 'ı SQL DB Bağlayıcısı ' nı kullanabilirsiniz. Büyük veri kümelerini yüklerken yazma performansını önemli ölçüde geliştirir veya bir sütun deposu dizininin kullanıldığı tablolara veri yükleme.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Sonraki adımlar
Henüz yapmadıysanız Azure SQL veritabanı için Spark bağlayıcısını indirin ve [Azure-SQLDB-Spark GitHub deposundan](https://github.com/Azure/azure-sqldb-spark) SQL Server ve depodaki ek kaynakları keşfedebilirsiniz:

-   [Örnek Azure Databricks Not defterleri](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Örnek betikler (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Ayrıca, [Apache Spark SQL, DataFrames ve veri kümeleri kılavuzunu](https://spark.apache.org/docs/latest/sql-programming-guide.html) ve [Azure Databricks belgelerini](https://docs.microsoft.com/azure/azure-databricks/)gözden geçirmek isteyebilirsiniz.


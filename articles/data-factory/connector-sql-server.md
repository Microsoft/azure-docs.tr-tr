---
title: SQL Server veri kopyalama
description: Azure Data Factory kullanarak şirket içinde veya Azure VM 'de bulunan SQL Server veritabanına veri taşıma hakkında bilgi edinin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 8187a71e89c364ef5a52d7ad4615ed03e6b24a4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592066"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Azure Data Factory kullanarak SQL Server veri kopyalama

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Azure Data Factory sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-sqlserver-connector.md)
> * [Güncel sürüm](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, verileri bir SQL Server veritabanına kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu SQL Server Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)

SQL Server veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Ya da desteklenen kaynak veri mağazalarından verileri bir SQL Server veritabanına kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu SQL Server Bağlayıcısı şunları destekler:

- SQL Server sürüm 2005 ve üzeri.
- SQL veya Windows kimlik doğrulaması kullanarak veri kopyalama.
- Kaynak olarak, bir SQL sorgusu veya saklı yordam kullanarak verileri alma. Ayrıca, SQL Server kaynaktan paralel kopyalama seçeneğini de belirleyebilirsiniz. Ayrıntılar için [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-database) bölümüne bakın.
- Havuz olarak, kaynak şemaya bağlı değilse, hedef tablo otomatik olarak oluşturuluyor; verileri bir tabloya ekleme veya kopyalama sırasında özel mantık ile saklı yordam çağırma. 

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) desteklenmez.

>[!NOTE]
>SQL Server [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) artık bu bağlayıcı tarafından desteklenmiyor. Geçici bir çözüm için [Genel BIR ODBC Bağlayıcısı](connector-odbc.md) ve SQL Server ODBC sürücüsü kullanabilirsiniz. ODBC sürücü indirme ve bağlantı dizesi yapılandırmalarına sahip [Bu kılavuzu](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver) izleyin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, SQL Server veritabanı bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

SQL Server bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği **SqlServer** olarak ayarlanmalıdır. | Yes |
| Dizisi |SQL kimlik doğrulaması veya Windows kimlik doğrulaması kullanarak SQL Server veritabanına bağlanmak için gereken **ConnectionString** bilgilerini belirtin. Aşağıdaki örneklere bakın.<br/>Ayrıca, Azure Key Vault bir parola koyabilirsiniz. SQL kimlik doğrulaması ise, `password` yapılandırmayı bağlantı dizesinin dışına çekin. Daha fazla bilgi için, Azure Key Vault tablo ve [Mağaza kimlik bilgilerini](store-credentials-in-key-vault.md)izleyen JSON örneğine bakın. |Yes |
| userName |Windows kimlik doğrulaması kullanıyorsanız, bir Kullanıcı adı belirtin. **DomainName \\ Kullanıcı adı** bir örnektir. |No |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için bir parola belirtin. Azure Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin. Veya [Azure Key Vault depolanan bir gizli](store-credentials-in-key-vault.md)dizi ile başvurabilirsiniz. |No |
| connectVia | Bu [tümleştirme çalışma zamanı](concepts-integration-runtime.md) , veri deposuna bağlanmak için kullanılır. [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. |No |

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" hata koduyla bir hatayla karşılaşırsanız ve "veritabanı için oturum sınırı XXX ve ulaşıldığında", `Pooling=false` Bağlantı dizenizi ekleyin ve yeniden deneyin.

**Örnek 1: SQL kimlik doğrulaması kullanma**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek 2: Azure Key Vault bir parolayla SQL kimlik doğrulaması kullanma**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek 3: Windows kimlik doğrulamasını kullanma**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm SQL Server veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Ve SQL Server veritabanından veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin Type özelliği **Sqlservertable** olarak ayarlanmalıdır. | Yes |
| schema | Şemanın adı. |Kaynak için Hayır, havuz için Evet  |
| tablo | Tablo/görünüm adı. |Kaynak için Hayır, havuz için Evet  |
| tableName | Şema ile tablonun/görünümün adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve kullanın `table` . | Kaynak için Hayır, havuz için Evet |

**Örnek**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamak için kullanılabilecek bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm SQL Server kaynak ve havuz tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sql-server-as-a-source"></a>Kaynak olarak SQL Server

>[!TIP]
>Veri bölümlemesini kullanarak SQL Server verileri verimli bir şekilde yüklemek için [SQL veritabanından paralel kopyadan](#parallel-copy-from-sql-database)daha fazla bilgi edinin.

SQL Server verileri kopyalamak için kopyalama etkinliğindeki kaynak türünü **SQLSource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik kaynağını kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği **SQLSource** olarak ayarlanmalıdır. | Yes |
| sqlReaderQuery |Verileri okumak için özel SQL sorgusunu kullanın. `select * from MyTable` bunun bir örneğidir. |No |
| sqlReaderStoredProcedureName |Bu özellik, kaynak tablodaki verileri okuyan saklı yordamın adıdır. Son SQL ifadesinin saklı yordamda bir SELECT ifadesinin olması gerekir. |No |
| storedProcedureParameters |Bu parametreler, saklı yordama yöneliktir.<br/>İzin verilen değerler ad veya değer çiftleridir. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle eşleşmelidir. |No |
| 'Sinden | SQL kaynağı için işlem kilitleme davranışını belirtir. İzin verilen değerler: **ReadCommitted**, **READUNCOMMITTED**, **RepeatableRead**, **Serializable**, **Snapshot**. Belirtilmemişse, veritabanının varsayılan yalıtım düzeyi kullanılır. Daha fazla ayrıntı için [Bu belgeye](/dotnet/api/system.data.isolationlevel) başvurun. | No |
| partitionOptions | SQL Server verileri yüklemek için kullanılan veri bölümleme seçeneklerini belirtir. <br>İzin verilen değerler: **none** (default), **Physicalpartitionsoftable** ve **DynamicRange**.<br>Bir bölüm seçeneği etkinleştirildiğinde (yani, değil `None` ), SQL Server eşzamanlı olarak veri yükleme için paralellik derecesi [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. | No |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin. <br>Bölüm seçeneği olmadığında uygulayın `None` . | No |
| ***Altında `partitionSettings` :*** | | |
| partitionColumnName |  `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` Paralel kopya için Aralık bölümleme tarafından kullanılacak tamsayı veya tarih/DateTime türünde (,,,,,,, veya) kaynak sütunun adını belirtin. Belirtilmemişse, tablonun dizini veya birincil anahtarı otomatik olarak algılanır ve bölüm sütunu olarak kullanılır.<br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız,  `?AdfDynamicRangePartitionCondition ` WHERE yan tümcesinde kanca. Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-database) bölümüne bakın. | No |
| Partitionüstsınırı | Bölüm aralığı bölme için bölüm sütununun en büyük değeri. Bu değer, tablodaki satırları filtrelemeye yönelik değil, bölümün ilerlemesine karar vermek için kullanılır. Tablodaki veya sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanabilir. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.  <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-database) bölümüne bakın. | No |
| Partitionalme sınırı | Bölüm aralığı bölme için bölüm sütununun en küçük değeri. Bu değer, tablodaki satırları filtrelemeye yönelik değil, bölümün ilerlemesine karar vermek için kullanılır. Tablodaki veya sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanabilir. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.<br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-database) bölümüne bakın. | No |

**Aşağıdaki noktalara dikkat edin:**

- SQLSource için **Sqlreaderquery** belirtilmişse , kopyalama etkinliği verileri almak için bu sorguyu SQL Server kaynağına göre çalıştırır. Saklı yordam parametreleri alırsa, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** belirterek bir saklı yordam de belirtebilirsiniz.
- Verileri almak için kaynakta saklı yordam kullanırken, saklı yordamınız farklı bir parametre değeri geçirildiğinde farklı bir şema döndürüyor olarak tasarlanmışsa, hata ile karşılaşabilirsiniz veya kullanıcı arabiriminden şemayı içeri aktarırken ya da otomatik tablo oluşturma ile verileri SQL veritabanına kopyalarken beklenmedik sonucu görebilirsiniz.

**Örnek: SQL sorgusunu kullanın**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Örnek: saklı yordam kullanma**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Saklı yordam tanımı**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>Havuz olarak SQL Server

> [!TIP]
> [SQL Server ' ye veri yüklemeye yönelik en iyi](#best-practice-for-loading-data-into-sql-server)uygulamalardan desteklenen yazma davranışları, konfigürasyonlar ve en iyi uygulamalar hakkında daha fazla bilgi edinin.

SQL Server verileri kopyalamak için kopyalama etkinliğindeki havuz türünü **Sqlsink** olarak ayarlayın. Aşağıdaki özellikler, kopyalama etkinliği havuzu bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği havuzunun Type özelliği **Sqlsink** olarak ayarlanmalıdır. | Yes |
| Ön Copyscrıpt |Bu özellik, SQL Server içine veri yazmadan önce, kopyalama etkinliğinin çalıştırılacağı bir SQL sorgusu belirtir. Her kopya çalıştırması için yalnızca bir kez çağrılır. Bu özelliği, önceden yüklenmiş verileri temizlemek için kullanabilirsiniz. |No |
| tableOption | Kaynak şemasına göre yoksa [Havuz tablosunun otomatik olarak oluşturulup](copy-activity-overview.md#auto-create-sink-tables) oluşturulmayacağını belirtir. Havuz, saklı yordamı belirttiğinde otomatik tablo oluşturma desteklenmez. İzin verilen değerler: `none` (varsayılan), `autoCreate` . |No |
| sqlWriterStoredProcedureName | Hedef tabloya kaynak verilerinin nasıl uygulanacağını tanımlayan saklı yordamın adı. <br/>Bu saklı yordam *toplu iş başına çağırılır*. Yalnızca bir kez çalıştırılan ve kaynak verilerle hiçbir şey olmayan işlemler için, örneğin, DELETE veya TRUNCATE, `preCopyScript` özelliğini kullanın.<br>[BIR SQL havuzundan saklı yordam çağırma](#invoke-a-stored-procedure-from-a-sql-sink)örneğine bakın. | No |
| storedProcedureTableTypeParameterName |Saklı yordamda belirtilen tablo türünün parametre adı.  |No |
| sqlWriterTableType |Saklı yordamda kullanılacak tablo türü adı. Kopyalama etkinliği, verileri bu tablo türüyle geçici bir tabloda kullanılabilir hale getirir. Saklı yordam kodu daha sonra mevcut verilerle Kopyalanmakta olan verileri birleştirebilir. |No |
| storedProcedureParameters |Saklı yordamın parametreleri.<br/>İzin verilen değerler ad ve değer çiftleridir. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. | No |
| writeBatchSize |*Toplu iş BAŞıNA* SQL tablosuna eklenecek satır sayısı.<br/>İzin verilen değerler, satır sayısı için tamsayılardır. Varsayılan olarak, Azure Data Factory satır boyutuna göre uygun toplu iş boyutunu dinamik olarak belirler. |No |
| writeBatchTimeout |Bu özellik, toplu ekleme işleminin zaman aşımına uğramadan önce tamamlaması için bekleme süresini belirtir.<br/>İzin verilen değerler TimeSpan içindir. 30 dakika boyunca "00:30:00" bir örnektir. Hiçbir değer belirtilmemişse, zaman aşımı varsayılan olarak "02:00:00" olur. |No |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

**Örnek 1: veri ekleme**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Örnek 2: kopyalama sırasında saklı yordam çağırma**

[BIR SQL havuzundan saklı yordam çağırma](#invoke-a-stored-procedure-from-a-sql-sink)hakkında daha fazla bilgi edinin.

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>SQL veritabanından paralel kopya

Copy etkinliğinde SQL Server Bağlayıcısı, verileri paralel olarak kopyalamak için yerleşik veri bölümlendirme sağlar. Kopyalama etkinliğinin **kaynak** sekmesinde veri bölümleme seçeneklerini bulabilirsiniz.

![Bölüm seçeneklerinin ekran görüntüsü](./media/connector-sql-server/connector-sql-partition-options.png)

Bölümlenmiş kopyayı etkinleştirdiğinizde kopyalama etkinliği, verileri bölümlere göre yüklemek için SQL Server kaynağınızdan paralel sorgular çalıştırır. Paralel derece [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu, SQL Server verilerin bir kısmını alır.

SQL Server büyük miktarda veriyi yüklerken, özellikle veri bölümleme ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adı belirtin) yazılması önerilir, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                                     | Önerilen ayarlar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fiziksel bölümlerle büyük tablodan tam yük.        | **Bölüm seçeneği**: tablonun fiziksel bölümleri. <br><br/>Yürütme sırasında Data Factory fiziksel bölümleri otomatik olarak algılar ve verileri bölümlere göre kopyalar. <br><br/>Tablonuzun fiziksel bölümü olup olmadığını denetlemek için [Bu sorguya](#sample-query-to-check-physical-partition)başvurabilirsiniz. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı veya tarih saat sütunuyla büyük tablodan tam yük. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Bölüm sütunu** (isteğe bağlı): verileri bölümlemek için kullanılan sütunu belirtin. Belirtilmezse, dizin veya birincil anahtar sütunu kullanılır.<br/>**Bölüm üst sınırı** ve **bölüm alt sınırı** (isteğe bağlı): Bölüm Ilerlemesiyle mı belirlemek istediğinizi belirtin. Bu, tablodaki satırları filtrelemeye yönelik değildir, tablodaki tüm satırlar bölümlenecek ve kopyalanır. Belirtilmemişse, kopyalama etkinliği değerleri otomatik olarak algılar.<br><br>Örneğin, "ID" adlı bölüm sütununuzu 1 ile 100 arasında değerler varsa ve alt sınırı 20 ve üst sınır olarak 80 olarak ayarlarsanız, paralel kopyalama Data Factory 4 ' e kadar, verileri 4 bölüm kimliği aralığında <= 20, [21, 50], [51, 80] ve >= 81 sırasıyla alır. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı veya tarih/tarih/saat sütunuyla büyük miktarda veri yükleyin. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin.<br>**Bölüm üst sınırı** ve **bölüm alt sınırı** (isteğe bağlı): Bölüm Ilerlemesiyle mı belirlemek istediğinizi belirtin. Bu, tablodaki satırları filtrelemeye yönelik değildir, sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanır. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.<br><br>Yürütme sırasında, Data Factory `?AdfRangePartitionColumnName` her bölüm için gerçek sütun adı ve değer aralıklarıyla değiştirilir ve SQL Server gönderir. <br>Örneğin, "ID" adlı bölüm sütununuzu 1 ile 100 arasında değerler varsa ve alt sınırı 20 ve üst sınır olarak 80 olarak ayarlarsanız, paralel kopyalama Data Factory 4 ' e kadar, verileri 4 bölüm kimliği aralığında <= 20, [21, 50], [51, 80] ve >= 81 sırasıyla alır. <br><br>Farklı senaryolar için daha fazla örnek sorgu aşağıda verilmiştir:<br> 1. tüm tabloyu sorgulayın: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. sütun seçimi ve ek WHERE yan tümcesi filtreleri içeren bir tablodan sorgu: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. sorgular ile sorgu: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. alt sorguda bölüm ile sorgulama: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Bölüm seçeneğiyle verileri yüklemek için en iyi uygulamalar:

1. Veri eğriliğini önlemek için bölüm sütunu olarak (birincil anahtar veya benzersiz anahtar gibi) farklı bir sütun seçin. 
2. Tabloda yerleşik bölüm varsa, daha iyi performans sağlamak için "tablonun fiziksel bölümleri" Bölüm seçeneğini kullanın.    
3. Verileri kopyalamak için Azure Integration Runtime kullanırsanız daha fazla bilgi işlem kaynağı kullanmak için daha büyük "[veri tümleştirme birimleri (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) ayarlayabilirsiniz. İlgili senaryolara göz atın.
4. "[Kopya paralelliği derecesi](copy-activity-performance-features.md#parallel-copy)", Bölüm numaralarını denetler, bu sayının performansı karşılamamasından çok büyük bir şekilde ayarlanması, bu sayıyı (ya da şirket IÇINDE barındırılan IR düğümlerinin sayısı) * (2-4) olarak ayarlamayı öneririz.

**Örnek: fiziksel bölümlerle büyük tablodan tam yük**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Örnek: Dinamik Aralık bölümü ile sorgulama**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Fiziksel bölümü denetlemek için örnek sorgu

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Tabloda fiziksel bölüm varsa, aşağıda gösterildiği gibi "HasPartition" öğesini "Yes" olarak görürsünüz.

![SQL sorgu sonucu](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-server"></a>SQL Server içine veri yüklemek için en iyi uygulama

SQL Server verileri kopyaladığınızda, farklı yazma davranışı gerekebilir:

- [Append](#append-data): Kaynak verilerinizde yalnızca yeni kayıtlar vardır.
- [Upsert](#upsert-data): Kaynak verilerinizde hem ekler hem de güncelleştirmeler vardır.
- [Üzerine yaz](#overwrite-the-entire-table): her seferinde boyut tablosunun tamamını yeniden yüklemek istiyorum.
- [Özel mantık Ile yaz](#write-data-with-custom-logic): hedef tabloya son ekleme yapmadan önce fazladan işleme ihtiyacım var.

Azure Data Factory ve en iyi yöntemleri yapılandırmak için ilgili bölümlere bakın.

### <a name="append-data"></a>Veri Ekle

Verilerin eklenmesi, bu SQL Server havuz bağlayıcısının varsayılan davranışıdır. Azure Data Factory tablonuza verimli bir şekilde yazmak için toplu bir ekleme yapar. Kopyalama etkinliğinde kaynağı ve havuzu uygun şekilde yapılandırabilirsiniz.

### <a name="upsert-data"></a>Verileri upsert etme

**Seçenek 1:** Kopyalamak için büyük miktarda veriniz varsa, kopyalama etkinliğini kullanarak tüm kayıtları bir hazırlama tablosuna toplu olarak yükleyebilir, sonra bir bir çekde [birleştirme](/sql/t-sql/statements/merge-transact-sql) veya ekleme/güncelleştirme ifadesini uygulamak için bir saklı yordam etkinliği çalıştırabilirsiniz. 

Kopyalama etkinliği şu anda verileri veritabanı geçici tablosuna yüklemeyi yerel olarak desteklemiyor. Birden çok etkinliğin birleşimiyle ayarlanmasının gelişmiş bir yolu vardır ve [SQL veritabanı toplu ön Ekle senaryolarına en iyileştirme](https://github.com/scoriani/azuresqlbulkupsert)bölümüne bakın. Aşağıda, kalıcı bir tablonun hazırlama olarak kullanılması örneği gösterilmektedir.

Örnek olarak, Azure Data Factory, **saklı yordam etkinliği** ile **kopyalama etkinliği** zincirli bir işlem hattı oluşturabilirsiniz. Eski, kaynak deponuzdaki verileri SQL Server hazırlama tablosuna kopyalar, örneğin, **UpsertStagingTable**, veri kümesindeki tablo adı olarak. İkinci olarak, hazırlama tablosundaki kaynak verileri hedef tabloya birleştirmek ve hazırlama tablosunu temizlemek için bir saklı yordam çağırır.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Veritabanınızda, önceki saklı yordam etkinliğinden işaret edilen aşağıdaki örnekte olduğu gibi, BIRLEŞTIRME mantığı ile bir saklı yordam tanımlayın. Hedefin üç sütunlu **Pazarlama** tablosu olduğunu varsayın: **ProfileId**, **State** ve **category**. **ProfileId** sütununu temel alarak büyük ölçüde yapın.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Seçenek 2:** [Kopyalama etkinliği içinde bir saklı yordamı çağırmayı](#invoke-a-stored-procedure-from-a-sql-sink)seçebilirsiniz. Bu yaklaşım, `writeBatchSize` kopyalama etkinliğinde varsayılan yaklaşım olarak toplu ekleme kullanmak yerine kaynak tablodaki her bir toplu işi (özelliği tarafından yönetilir) çalıştırır.

### <a name="overwrite-the-entire-table"></a>Tüm tablonun üzerine yaz

Bir kopyalama etkinliği havuzunda **Precopyscript** özelliğini yapılandırabilirsiniz. Bu durumda, çalıştıran her kopyalama etkinliği için önce betiği çalıştırır Azure Data Factory. Ardından, verileri eklemek için kopyayı çalıştırır. Örneğin, en son verilerle tüm tablonun üzerine yazmak için, kaynaktan yeni verileri toplu yüklemeden önce tüm kayıtları silmek üzere bir komut dosyası belirtin.

### <a name="write-data-with-custom-logic"></a>Özel mantık ile veri yazma

Özel mantık ile veri yazma adımları, [upsert veri](#upsert-data) bölümünde açıklananlara benzerdir. Kaynak verilerin son olarak hedef tabloya eklenmesinin önüne ek işlem uygulamanız gerektiğinde, hazırlama tablosuna yükleyebilir, sonra saklı yordam etkinliğini çağırabilir veya veri uygulamak için kopyalama etkinlik havuzunda saklı bir yordam çağırabilirsiniz.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Bir SQL havuzundan saklı yordam çağırma

Verileri SQL Server veritabanına kopyaladığınızda, kaynak tablodaki her yığında ek parametrelerle Kullanıcı tarafından belirtilen bir saklı yordamı yapılandırabilir ve çağırabilirsiniz. Saklı yordam özelliği [tablo değerli parametrelerin](/dotnet/framework/data/adonet/sql/table-valued-parameters)avantajlarından yararlanır.

Yerleşik kopyalama mekanizmaları amaca uygun olmadığında, saklı bir yordam kullanabilirsiniz. Kaynak verilerin son olarak hedef tabloya eklenmesinin önüne daha fazla işlem uygulamak istediğinizde örnek bir örnektir. Bazı ek işleme örnekleri, sütunları birleştirmek, ek değerleri aramak ve birden fazla tabloya eklemek istebilmenizdir.

Aşağıdaki örnek, SQL Server veritabanındaki bir tabloya bir üsert yapmak için saklı yordamın nasıl kullanılacağını göstermektedir. Giriş verilerinin ve havuz **Pazarlama** tablosunun her birinin üç sütunu olduğunu varsayalım: **ProfileId**, **State** ve **category**. **ProfileId** sütununu temel alarak ve yalnızca "Producta" adlı belirli bir kategori için uygulayın.

1. Veritabanınızda, **Sqlwritertabletype** ile aynı ada sahip tablo türünü tanımlayın. Tablo türünün şeması, giriş verileriniz tarafından döndürülen şemayla aynıdır.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Veritabanınızda, **sqlWriterStoredProcedureName** ile aynı ada sahip saklı yordamı tanımlayın. Belirtilen kaynağınızdan gelen giriş verilerini işler ve çıkış tablosu ile birleştirir. Saklı yordamdaki tablo türünün parametre adı, veri kümesinde tanımlanan **TableName** ile aynıdır.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Azure Data Factory, kopyalama etkinliğinde **SQL havuzu** bölümünü aşağıdaki gibi tanımlayın:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>SQL Server için veri türü eşlemesi

Ve SQL Server verileri kopyaladığınızda, SQL Server veri türlerinden, geçici veri türlerine Azure Data Factory için aşağıdaki eşlemeler kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

| SQL Server veri türü | Azure Data Factory geçici veri türü |
|:--- |:--- |
| bigint |Int64 |
| ikili |Byte [] |
| bit |Boole |
| char |Dize, Char [] |
| date |DateTime |
| Tarih saat |DateTime |
| datetime2 |DateTime |
| Türünde |DateTimeOffset |
| Ondalık |Ondalık |
| FıLESTREAM özniteliği (varbinary (max)) |Byte [] |
| Float |Çift |
| image |Byte [] |
| int |Int32 |
| etmenize |Ondalık |
| nchar |Dize, Char [] |
| n |Dize, Char [] |
| sayısal |Ondalık |
| nvarchar |Dize, Char [] |
| real |Tek |
| rowversion |Byte [] |
| girişin |DateTime |
| smallint |Int16 |
| küçük para |Ondalık |
| sql_variant |Nesne |
| metin |Dize, Char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| ikili |Byte [] |
| varchar |Dize, Char [] |
| xml |Dize |

>[!NOTE]
> Ondalık geçici türle eşlenen veri türleri için şu anda kopyalama etkinliği, 28 ' ye kadar duyarlık destekler. 28 ' den daha büyük bir duyarlık gerektiren verileriniz varsa, bir SQL sorgusunda bir dizeye dönüştürmeyi düşünün.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md) denetleyin 

## <a name="using-always-encrypted"></a>Always Encrypted kullanma

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)ile SQL Server verileri kopyaladığınızda, [Genel ODBC bağlayıcısını](connector-odbc.md) kullanın ve şirket IÇINDE barındırılan Integration Runtime aracılığıyla ODBC sürücüsünü SQL Server. Bu SQL Server Bağlayıcısı şimdi Always Encrypted desteklemiyor. 

Daha ayrıntılı belirtmek gerekirse:

1. Kendi kendine barındırılan Integration Runtime yoksa ayarlayın. Ayrıntılar için bkz. [Şirket içinde barındırılan Integration Runtime](create-self-hosted-integration-runtime.md) makalesi.

2. SQL Server için 64 bit ODBC sürücüsünü [buradan](/sql/connect/odbc/download-odbc-driver-for-sql-server)indirin ve Integration Runtime makinesine yükleyin. Bu sürücünün [SQL Server ODBC sürücüsüyle Always Encrypted kullanarak](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider)nasıl çalıştığı hakkında daha fazla bilgi edinin.

3. SQL veritabanınıza bağlanmak için ODBC türü ile bağlı hizmet oluşturun. SQL kimlik doğrulamasını kullanmak için aşağıdaki gibi ODBC bağlantı dizesini belirtin ve **temel** kimlik doğrulaması ' nı seçerek kullanıcı adını ve parolayı ayarlayın.

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. Veri kümesi ve kopyalama etkinliğini ODBC türü ile buna uygun olarak oluşturun. [ODBC Bağlayıcısı](connector-odbc.md) makalesinden daha fazla bilgi edinin.

## <a name="troubleshoot-connection-issues"></a>Bağlantı sorunlarını giderme

1. SQL Server örneğinizi uzak bağlantıları kabul edecek şekilde yapılandırın. **SQL Server Management Studio** başlatın, **sunucu**' ya sağ tıklayın ve **Özellikler**' i seçin. Listeden **Bağlantılar** ' ı seçin ve **Bu sunucuya uzaktan bağlantılara izin ver** onay kutusunu seçin.

    ![Uzak bağlantıları etkinleştir](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Ayrıntılı adımlar için bkz. [Uzaktan erişim sunucu yapılandırma seçeneğini yapılandırma](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option).

2. **SQL Server Yapılandırma Yöneticisi** başlatın. İstediğiniz örnek için **SQL Server ağ yapılandırması** ' nı GENIŞLETIN ve **MSSQLSERVER protokolleri**' ni seçin. Protokoller sağ bölmede görüntülenir. **TCP/IP ' ye** sağ tıklayıp **Etkinleştir**' i seçerek TCP/IP 'yi etkinleştirin.

    ![TCP/IP 'yi etkinleştir](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    TCP/IP protokolünü etkinleştirmenin daha fazla bilgi ve diğer yolları için bkz. [sunucu ağ protokolünü etkinleştirme veya devre dışı bırakma](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).

3. Aynı pencerede, TCP/IP **Özellikler** penceresini başlatmak için **TCP/IP** ' ye çift tıklayın.
4. **IP adresleri** sekmesine geçin. **IPAll** bölümünü görmek için aşağı kaydırın. **TCP bağlantı noktasını** yazın. Varsayılan değer **1433**' dir.
5. Bu bağlantı noktası üzerinden gelen trafiğe izin vermek için makinede **Windows Güvenlik Duvarı için bir kural** oluşturun. 
6. **Bağlantıyı doğrula**: tam nitelikli adı kullanarak SQL Server bağlanmak için, farklı bir makineden SQL Server Management Studio kullanın. `"<machine>.<domain>.corp.<company>.com,1433"` bunun bir örneğidir.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
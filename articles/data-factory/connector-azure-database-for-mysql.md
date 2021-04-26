---
title: MySQL için Azure veritabanı 'nda verileri kopyalama ve dönüştürme
description: Azure Data Factory kullanarak MySQL için Azure veritabanı 'na veri kopyalamayı ve dönüştürmeyi öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 4d13f6f435a21b467cae1b8e14211a001792787f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012620"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Azure Data Factory kullanarak MySQL için Azure veritabanı 'nda verileri kopyalama ve dönüştürme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, MySQL için Azure veritabanı 'na veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir ve veri akışı kullanarak MySQL için Azure veritabanı 'ndaki verileri dönüştürebilirsiniz. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

Bu bağlayıcı, [MySQL Için Azure veritabanı hizmeti](../mysql/overview.md)için özel bir hizmettir. Şirket içinde veya bulutta bulunan genel MySQL veritabanından veri kopyalamak için [MySQL bağlayıcısını](connector-mysql.md)kullanın.

## <a name="supported-capabilities"></a>Desteklenen özellikler

MySQL için Azure Veritabanı Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, MySQL için Azure Veritabanı Bağlayıcısı 'na özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

MySQL için Azure veritabanı bağlantılı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **AzureMySql** olarak ayarlanmalıdır | Yes |
| Dizisi | MySQL için Azure veritabanı örneğine bağlanmak için gereken bilgileri belirtin. <br/> Ayrıca, Azure Key Vault parolayı yerleştirebilir ve `password` yapılandırmayı bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault makalesine depolayın](store-credentials-in-key-vault.md) . | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime (veri depolduğunuz özel ağda yer alıyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |

Tipik bir bağlantı dizesi `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Servis talebi başına ayarlayabileceğiniz daha fazla özellik:

| Özellik | Açıklama | Seçenekler | Gerekli |
|:--- |:--- |:--- |:--- |
| SSLMode | Bu seçenek, sürücünün MySQL 'e bağlanırken TLS şifrelemesini ve doğrulamasını kullanıp kullanmadığını belirtir. Örneğin `SSLMode=<0/1/2/3/4>`| DEVRE DıŞı (0)/tercıh EDILEN (1) **(varsayılan)** /gerekli (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | No |
| UseSystemTrustStore | Bu seçenek, sistem güven deposundan veya belirtilen ped dosyasından bir CA sertifikası kullanılıp kullanılmayacağını belirtir. Örneğin `UseSystemTrustStore=<0/1>;`| Etkin (1)/devre dışı (0) **(varsayılan)** | No |

**Örnek:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Azure Key Vault parola depola**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
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

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, MySQL için Azure veritabanı veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

MySQL için Azure veritabanı 'ndan veri kopyalamak için veri kümesinin Type özelliğini **AzureMySqlTable** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **AzureMySqlTable** olarak ayarlanmalıdır | Yes |
| tableName | MySQL veritabanındaki tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, MySQL için Azure veritabanı kaynak ve havuzu tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="azure-database-for-mysql-as-source"></a>MySQL için Azure veritabanı kaynak olarak

MySQL için Azure veritabanı 'ndan veri kopyalamak için, etkinlik **kaynağını** kopyalama bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **AzureMySqlSource** olarak ayarlanmalıdır | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM MyTable"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |
| queryCommandTimeout | Sorgu isteğinin zaman aşımına uğramadan önce beklenecek bekleme süresi. Varsayılan değer 120 dakikadır (02:00:00) | No |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>MySQL için Azure veritabanı havuz olarak

MySQL için Azure veritabanı 'na veri kopyalamak için aşağıdaki özellikler, etkinlik **havuzunu** Kopyala bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği havuzunun Type özelliği: **AzureMySqlSink** olarak ayarlanmalıdır | Yes |
| Ön Copyscrıpt | Her çalıştırmada MySQL için Azure veritabanı 'na veri yazmadan önce yürütülecek kopyalama etkinliği için bir SQL sorgusu belirtin. Bu özelliği, önceden yüklenmiş verileri temizlemek için kullanabilirsiniz. | No |
| writeBatchSize | Arabellek boyutu writeBatchSize ulaştığında, MySQL için Azure veritabanı tablosuna veri ekler.<br>İzin verilen değer, satır sayısını temsil eden bir tamsayıdır. | Hayır (varsayılan değer 10.000) |
| writeBatchTimeout | Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi.<br>İzin verilen değerler TimeSpan. Örnek olarak 00:30:00 (30 dakika). | Hayır (varsayılan değer 00:00:30) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Eşleme veri akışındaki verileri dönüştürürken, MySQL için Azure veritabanı 'ndan tabloları okuyabilir ve yazdırabilirsiniz. Daha fazla bilgi için bkz. veri akışlarını eşleme içindeki [kaynak dönüştürme](data-flow-source.md) ve [Havuz dönüşümü](data-flow-sink.md) . Kaynak ve havuz türü olarak bir MySQL için Azure veritabanı veri kümesi veya [satır içi veri kümesi](data-flow-source.md#inline-datasets) kullanmayı tercih edebilirsiniz.

### <a name="source-transformation"></a>Kaynak dönüştürme

Aşağıdaki tabloda, MySQL için Azure veritabanı kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tablo | Giriş olarak tablo ' yı seçerseniz veri akışı, veri kümesinde belirtilen tablodaki tüm verileri getirir. | No | - |*(yalnızca satır içi veri kümesi için)*<br>tableName |
| Sorgu | Giriş olarak Sorgula ' yı seçerseniz, kaynaktan veri getirmek için bir SQL sorgusu belirtin. Bu, veri kümesinde belirttiğiniz tüm tabloları geçersiz kılar. Sorguları kullanmak, test veya arama için satırları azaltmanın harika bir yoludur.<br><br>**Order by** yan tümcesi desteklenmez, ancak BIR tam select from deyimi ayarlayabilirsiniz. Kullanıcı tanımlı tablo işlevleri de kullanabilirsiniz. **select * from udfGetData ()** , veri akışında kullanabileceğiniz bir tablo döndüren SQL 'de bir UDF 'dir.<br>Sorgu örneği: `select * from mytable where customerId > 1000 and customerId < 2000` veya `select * from "MyTable"` .| Hayır | Dize | sorgu |
| Toplu iş boyutu | Büyük verileri toplu işlemlere Bölmek için bir toplu iş boyutu belirtin. | No | Tamsayı | batchSize |
| Yalıtım düzeyi | Aşağıdaki yalıtım düzeylerinden birini seçin:<br>-Kaydedilen okuma<br>-Read UNCOMMITTED (varsayılan)<br>-Yinelenebilir okuma<br>-Serileştirilebilir<br>-None (yalıtım düzeyini yoksay) | No | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERI hale GETIRILEBILIR<br/>SEÇIM</small> |'Sinden |

#### <a name="azure-database-for-mysql-source-script-example"></a>MySQL için Azure veritabanı kaynak betiği örneği

MySQL için Azure veritabanı 'nı kaynak türü olarak kullandığınızda, ilişkili veri akışı betiği şu şekilde olur:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Havuz dönüştürme

Aşağıdaki tabloda, MySQL için Azure veritabanı havuzu tarafından desteklenen özellikler listelenmiştir. Bu özellikleri, **havuz seçenekleri** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Yöntemi Güncelleştir | Veritabanı Hedefinizdeki işlemlere izin verileceğini belirtin. Varsayılan değer yalnızca eklemeleri izin verir.<br>Satırları güncelleştirmek, kaldırmak veya silmek için, bu eylemler için satırları etiketlemek üzere bir [alter Row dönüşümü](data-flow-alter-row.md) gereklidir. | Yes | `true` veya `false` | siler <br/>eklenebilir <br/>güncellenebilir <br/>upsertable |
| Anahtar sütunlar | Güncelleştirmeler, üst üste ve siler için, hangi satırın ekleneceğini belirleyen anahtar sütunlar ayarlanmalıdır.<br>Anahtar olarak seçtiğiniz sütun adı, sonraki güncelleştirme, upsert, DELETE 'in bir parçası olarak kullanılacaktır. Bu nedenle, havuz eşlemesinde var olan bir sütun seçmeniz gerekir. | No | Dizi | keys |
| Anahtar sütunları yazmayı atla | Değeri anahtar sütununa yazmak istiyorsanız "anahtar sütunlarını yazmayı atla" seçeneğini belirleyin. | No | `true` veya `false` | Skipkeyyazmaları |
| Tablo eylemi |Yazmadan önce hedef tablodaki tüm satırların yeniden oluşturulup kaldırılacağını belirler.<br>- **Hiçbiri**: tabloya hiçbir eylem yapılmaz.<br>- **Yeniden oluştur**: tablo bırakılır ve yeniden oluşturulur. Dinamik olarak yeni bir tablo oluşturuluyoruz gereklidir.<br>- **Kes**: hedef tablodaki tüm satırlar kaldırılacak. | No | `true` veya `false` | Oluştur<br/>kesilemedi |
| Toplu iş boyutu | Her bir toplu işte kaç satır yazıldığını belirtin. Daha büyük toplu işlem boyutları sıkıştırma ve bellek iyileştirmeyi iyileştirir, ancak verileri önbelleğe alırken bellek dışında özel durumlar riskini ortadan kaldıracak. | No | Tamsayı | batchSize |
| SQL betikleri öncesi ve sonrası | (Ön işleme) ve sonra (işlem sonrası) verileri havuz veritabanınıza yazıldıktan önce yürütülecek çok satırlı SQL betikleri belirtin. | Hayır | Dize | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>MySQL için Azure veritabanı havuz betiği örneği

MySQL için Azure veritabanı 'nı havuz türü olarak kullandığınızda, ilişkili veri akışı betiği şu şekilde olur:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı veri türü eşlemesi

MySQL için Azure veritabanından veri kopyalarken aşağıdaki eşlemeler, MySQL veri türlerinden, geçici veri türlerini Azure Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| MySQL için Azure veritabanı veri türü | Veri Fabrikası geçici veri türü |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

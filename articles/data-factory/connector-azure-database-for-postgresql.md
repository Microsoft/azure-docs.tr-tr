---
title: Azure Data Factory kullanarak PostgreSQL için Azure veritabanı 'na veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak PostgreSQL için Azure veritabanı 'na ve veri kopyalama hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: 7443ab3abb12451619e77d925912b4bfc118cfaa
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681216"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Azure Data Factory kullanarak PostgreSQL için Azure veritabanı 'na veri kopyalama

Bu makalede, PostgreSQL için Azure veritabanı 'ndan veri kopyalamak için Azure Data Factory etkinlik kopyalama özelliğinin nasıl kullanılacağı açıklanır. Kopyalama etkinliğine genel bir bakış sunan [Azure Data Factory makalesinde kopyalama etkinliği](copy-activity-overview.md) oluşturur.

Bu bağlayıcı [PostgreSQL Için Azure veritabanı hizmeti](../postgresql/overview.md)için özelleştirilmiştir. Şirket içinde veya bulutta bulunan genel bir PostgreSQL veritabanından veri kopyalamak için [PostgreSQL bağlayıcısını](connector-postgresql.md)kullanın.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

PostgreSQL için Azure Veritabanı Bağlayıcısı, aşağıdaki etkinlikler için desteklenir:

- [Etkinliği](copy-activity-overview.md) [desteklenen bir kaynak/havuz matrisi](copy-activity-overview.md) ile kopyalama
- [Arama etkinliği](control-flow-lookup-activity.md)

PostgreSQL için Azure veritabanı 'ndan desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Ya da desteklenen herhangi bir kaynak veri deposundan verileri PostgreSQL için Azure veritabanı 'na kopyalayabilirsiniz. Kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Azure Data Factory, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar. Bu nedenle, bu bağlayıcıyı kullanmak için herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, PostgreSQL için Azure veritabanı bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi verilmektedir.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

PostgreSQL için Azure veritabanı bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **AzurePostgreSql**olarak ayarlanmalıdır. | Evet |
| connectionString | PostgreSQL için Azure veritabanı 'na bağlanmak üzere bir ODBC bağlantı dizesi.<br/>Bu alanı, Data Factory güvenli bir şekilde depolamak için SecureString olarak işaretleyin. Ayrıca, Azure Key Vault bir parola yerleştirebilir ve `password` yapılandırmasını bağlantı dizesinden dışarı çekebilirsiniz. Daha fazla ayrıntı için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault](store-credentials-in-key-vault.md) . | Evet |
| connectVia | Bu özellik, veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanını](concepts-integration-runtime.md) temsil eder. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime (veri depolduğunuz özel ağda yer alıyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Hayır |

Tipik bir bağlantı dizesi `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Servis talebi başına ayarlayabileceğiniz daha fazla özellik aşağıda verilmiştir:

| Özellik | Açıklama | Seçenekler | Gerekli |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Sürücünün sürücü ve veritabanı sunucusu arasında gönderilen verileri şifrelemek için kullandığı yöntem. Örneğin, `EncryptionMethod=<0/1/6>;`| 0 (şifreleme yok) **(varsayılan)** /1 (SSL)/6 (requestssl) | Hayır |
| ValidateServerCertificate (VSC) | Sürücünün, SSL şifrelemesi etkinleştirildiğinde veritabanı sunucusu tarafından gönderilen sertifikayı doğrulayıp doğrulamayacağını belirler (şifreleme yöntemi = 1). Örneğin, `ValidateServerCertificate=<0/1>;`| 0 (devre dışı) **(varsayılan)** /1 (etkin) | Hayır |

**Örnek**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        }
    }
}
```

**Örnek**:

***Parolayı Azure Key Vault içinde depola***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [Azure Data Factory veri kümeleri](concepts-datasets-linked-services.md). Bu bölüm, veri kümelerinde PostgreSQL için Azure veritabanı 'nın desteklediği özelliklerin bir listesini sağlar.

PostgreSQL için Azure veritabanı 'ndan veri kopyalamak için veri kümesinin Type özelliğini **AzurePostgreSqlTable**olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin Type özelliği **AzurePostgreSqlTable** olarak ayarlanmalıdır | Evet |
| tableName | Tablonun adı | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için, bkz. Azure Data Factory işlem [hatları ve etkinlikleri](concepts-pipelines-activities.md). Bu bölüm, PostgreSQL için Azure veritabanı kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="azure-database-for-postgresql-as-source"></a>Kaynak olarak PostgreSql için Azure veritabanı

PostgreSQL için Azure veritabanı 'ndan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **AzurePostgreSqlSource**olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği **AzurePostgreSqlSource** olarak ayarlanmalıdır | Evet |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin, `"SELECT * FROM MyTable"` | Hayır (veri kümesindeki tableName özelliği belirtilmişse) |

**Örnek**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Havuz olarak PostgreSQL için Azure veritabanı

PostgreSQL için Azure veritabanı 'na veri kopyalamak için, etkinlik **havuzunu** kopyalama bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği havuzunun Type özelliği **AzurePostgreSQLSink**olarak ayarlanmalıdır. | Evet |
| Ön Copyscrıpt | Her çalıştırmada PostgreSQL için Azure veritabanı 'na veri yazmadan önce, kopyalama etkinliğinin yürütülmesi için bir SQL sorgusu belirtin. Bu özelliği, önceden yüklenmiş verileri temizlemek için kullanabilirsiniz. | Hayır |
| writeBatchSize | Arabellek boyutu writeBatchSize ulaştığında, PostgreSQL için Azure veritabanı tablosuna veri ekler.<br>İzin verilen değer, satır sayısını temsil eden bir tamsayıdır. | Hayır (varsayılan değer 10.000) |
| writeBatchTimeout | Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi.<br>İzin verilen değerler TimeSpan dizeleridir. Örnek olarak 00:30:00 (30 dakika). | Hayır (varsayılan değer 00:00:30) |

**Örnek**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özellikler hakkında daha fazla bilgi için [Azure Data Factory arama etkinliği](control-flow-lookup-activity.md)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

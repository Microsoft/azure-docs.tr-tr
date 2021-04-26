---
title: Azure Data Factory kullanarak MySQL 'ten veri kopyalama
description: MySQL veritabanından havuz olarak desteklenen bir veri deposuna veri kopyalamanızı sağlayan Azure Data Factory MySQL Bağlayıcısı hakkında bilgi edinin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: jingwang
ms.openlocfilehash: b6a2253a4a124fe5e3725863c799f91714e66cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375264"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Azure Data Factory kullanarak MySQL 'ten veri kopyalama

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-onprem-mysql-connector.md)
> * [Güncel sürüm](connector-mysql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, bir MySQL veritabanından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

>[!NOTE]
>Veya [MySQL hizmeti Için Azure veritabanı](../mysql/overview.md) 'na veri kopyalamak için, özel [MySQL için Azure veritabanı bağlayıcısını](connector-azure-database-for-mysql.md)kullanın.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu MySQL Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

MySQL veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu MySQL Bağlayıcısı MySQL **sürüm 5,6, 5,7 ve 8,0 ' i** destekler.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime sürüm 3,7 ' den başlayarak yerleşik bir MySQL sürücüsü sağlar, bu nedenle herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, MySQL bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

MySQL bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **MySQL** olarak ayarlanmalıdır | Yes |
| Dizisi | MySQL için Azure veritabanı örneğine bağlanmak için gereken bilgileri belirtin.<br/> Ayrıca, Azure Key Vault parolayı yerleştirebilir ve `password` yapılandırmayı bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault makalesine depolayın](store-credentials-in-key-vault.md) . | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |

Tipik bir bağlantı dizesi `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Servis talebi başına ayarlayabileceğiniz daha fazla özellik:

| Özellik | Açıklama | Seçenekler | Gerekli |
|:--- |:--- |:--- |:--- |
| SSLMode | Bu seçenek, sürücünün MySQL 'e bağlanırken TLS şifrelemesini ve doğrulamasını kullanıp kullanmadığını belirtir. Ör.,  `SSLMode=<0/1/2/3/4>` .| DEVRE DıŞı (0)/tercıh EDILEN (1) **(varsayılan)** /gerekli (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | No |
| SSLCert | İstemcinin kimliğini temin etmek için kullanılan SSL sertifikasını içeren bir. ped dosyasının tam yolu ve adı. <br/> Sunucuya göndermeden önce bu sertifikayı şifrelemek üzere özel bir anahtar belirtmek için `SSLKey` özelliğini kullanın.| | Evet, iki yönlü SSL doğrulaması kullanıyorsanız. |
| SSLKey | İki yönlü SSL doğrulaması sırasında istemci tarafı sertifikayı şifrelemek için kullanılan özel anahtarı içeren dosyanın tam yolu ve adı.|  | Evet, iki yönlü SSL doğrulaması kullanıyorsanız. |
| UseSystemTrustStore | Bu seçenek, sistem güven deposundan veya belirtilen ped dosyasından bir CA sertifikası kullanılıp kullanılmayacağını belirtir. Örneğin `UseSystemTrustStore=<0/1>;`| Etkin (1)/devre dışı (0) **(varsayılan)** | No |

**Örnek:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
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
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
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

MySQL bağlantılı hizmetini aşağıdaki yük ile kullanıyorsanız, yeni bir tane olduğu gibi, ileri ' yi kullanmanız önerilir.

**Önceki yük:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, MySQL veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

MySQL 'ten veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **Mysqltable** olarak ayarlanmalıdır | Yes |
| tableName | MySQL veritabanındaki tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable`Türü belirtilmiş veri kümesi kullanıyorsanız, hala olduğu gibi desteklenir, ancak yeni bir adım ileri kullanmanız önerilir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, MySQL kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="mysql-as-source"></a>Kaynak olarak MySQL

MySQL 'ten veri kopyalamak için aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Mysqlsource** olarak ayarlanmalıdır | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM MyTable"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Yazılan kaynağı kullanıyorsanız, `RelationalSource` hala olduğu gibi desteklenmektedir, ileri ' yi kullanmaya devam etmeniz önerilir.

## <a name="data-type-mapping-for-mysql"></a>MySQL için veri türü eşlemesi

MySQL 'ten veri kopyalarken, MySQL veri türlerinden aşağıdaki eşlemeler, ara veri türleri Azure Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| MySQL veri türü | Veri Fabrikası geçici veri türü |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
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
| `year` |`Int` |


## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

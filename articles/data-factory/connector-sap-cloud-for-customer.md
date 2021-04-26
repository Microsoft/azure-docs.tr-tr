---
title: Müşteri için SAP buluttan veri kopyalama
description: Müşteri için SAP buluttan verileri desteklenen kaynak veri depolarından (veya), Data Factory kullanarak müşteri için SAP bulutuna kopyalamayı öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: b86559422b6efeed666a3ae35022563a66d9c7e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597341"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Azure Data Factory kullanarak müşteri için SAP bulutlarından (C4C) veri kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, müşteri için SAP buluttan (C4C) verileri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

>[!TIP]
>ADF 'nin SAP veri tümleştirme senaryosunda genel desteğini öğrenmek için, her SAP Bağlayıcısı, karşılaştırma ve kılavuza ayrıntılı giriş ile [Azure Data Factory Teknik İnceleme kullanarak SAP veri tümleştirme](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) konusuna bakın.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu SAP bulutu for Customer Connector, aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Müşteri için SAP buluttan verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilir veya desteklenen herhangi bir kaynak veri deposundan verileri, müşteri için SAP bulutuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu bağlayıcı Sales için SAP bulutu, hizmet için SAP bulutu ve sosyal katılım çözümleri için SAP bulutu dahil olmak üzere, müşteri için SAP buluttan veri kopyalamasını Azure Data Factory sağlar.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, müşteri Bağlayıcısı için SAP bulutuna özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Müşteri bağlantılı hizmeti için SAP bulutu için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Sapcloudforcustomer** olarak ayarlanmalıdır. | Yes |
| url | SAP C4C OData hizmetinin URL 'SI. | Yes |
| username | SAP C4C bağlanmak için Kullanıcı adını belirtin. | Yes |
| password | Kullanıcı adı için belirttiğiniz kullanıcı hesabının parolasını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | No |

**Örnek:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, müşteri veri kümesi için SAP bulutu tarafından desteklenen özelliklerin bir listesini sağlar.

Müşteri için SAP bulutundaki verileri kopyalamak için veri kümesinin Type özelliğini **Sapcloudforcustomerresource** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin Type özelliği: **Sapcloudforcustomerresource** olarak ayarlanmalıdır |Yes |
| path | SAP C4C OData varlığının yolunu belirtin. |Yes |

**Örnek:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, müşteri kaynağı için SAP bulutu tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sap-c4c-as-source"></a>Kaynak olarak SAP C4C

Müşteri için SAP buluttan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **SapCloudForCustomerSource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **SapCloudForCustomerSource** olarak ayarlanmalıdır  | Yes |
| sorgu | Verileri okumak için özel OData sorgusunu belirtin. | No |
| httpRequestTimeout | HTTP isteğinin yanıt almak için zaman aşımı ( **TimeSpan** değeri). Bu değer, yanıt verilerinin okunması için zaman aşımı değil, yanıt almaya yönelik zaman aşımı değeridir. Belirtilmemişse, varsayılan değer **00:30:00** ' dir (30 dakika). | No |

Belirli bir güne ait verileri almak için örnek sorgu: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>Havuz olarak SAP C4C

Müşteri için SAP bulutuna veri kopyalamak için kopyalama etkinliğindeki havuz türünü **Sapcloudforcustomersink** olarak ayarlayın. Aşağıdaki özellikler, kopyalama etkinliği **havuzu** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Sapcloudforcustomersink** olarak ayarlanmalıdır  | Yes |
| writeBehavior | İşlemin yazma davranışı. "INSERT", "Update" olabilir. | Hayır. Varsayılan "Ekle". |
| writeBatchSize | Yazma işleminin toplu iş boyutu. En iyi performansı elde etmek için toplu iş boyutu, farklı tablo veya sunucu için farklı olabilir. | Hayır. Varsayılan 10. |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Müşteri için SAP bulutu için veri türü eşlemesi

Müşteri için SAP bulutlarından veri kopyalarken aşağıdaki eşlemeler, istemci veri türleri için SAP bulutu 'ndan, geçici veri türlerini Azure Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| SAP C4C OData veri türü | Veri Fabrikası geçici veri türü |
|:--- |:--- |
| EDM. Binary | Byte [] |
| Edm.Boolean | Bool |
| EDM. Byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. Decimal | Ondalık |
| Edm.Double | Çift |
| EDM. Single | Tek |
| EDM. Guid | Guid |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. SByte | Int16 |
| Edm.String | Dize |
| EDM. Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

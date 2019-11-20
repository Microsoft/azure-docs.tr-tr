---
title: Azure Data Factory kullanarak müşteri için SAP buluttan veri kopyalama
description: Müşteri için SAP buluttan verileri desteklenen kaynak veri depolarından (veya), Data Factory kullanarak müşteri için SAP bulutuna kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 5c47eead8bc8428f533374b2d4892ed684c1f3c7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680273"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Azure Data Factory kullanarak müşteri için SAP bulutlarından (C4C) veri kopyalama

Bu makalede, müşteri için SAP buluttan (C4C) verileri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

>[!TIP]
>ADF 'nin SAP veri tümleştirme senaryosunda genel desteğini öğrenmek için ayrıntılı giriş, comparme ve kılavuzla [Azure Data Factory Teknik İnceleme kullanarak SAP veri tümleştirme](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) konusuna bakın.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu SAP bulutu for Customer Connector, aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Müşteri için SAP buluttan verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilir veya desteklenen herhangi bir kaynak veri deposundan verileri, müşteri için SAP bulutuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu bağlayıcı Sales için SAP bulutu, hizmet için SAP bulutu ve sosyal katılım çözümleri için SAP bulutu dahil olmak üzere, müşteri için SAP buluttan veri kopyalamasını Azure Data Factory sağlar.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, müşteri Bağlayıcısı için SAP bulutuna özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Müşteri bağlantılı hizmeti için SAP bulutu için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **Sapcloudforcustomer**olarak ayarlanmalıdır. | Evet |
| url | SAP C4C OData hizmetinin URL 'SI. | Evet |
| kullanıcı adı | SAP C4C bağlanmak için Kullanıcı adını belirtin. | Evet |
| password | Kullanıcı adı için belirttiğiniz kullanıcı hesabının parolasını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | Kaynak için Hayır, havuz için Evet |

>[!IMPORTANT]
>Müşteri için SAP bulutuna veri kopyalamak için, müşteri için SAP bulutunuzun yakınında bir konum ile açık [bir Azure IR oluşturun](create-azure-integration-runtime.md#create-azure-ir) ve bağlı hizmette aşağıdaki örnekte ilişkilendirin:

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

Müşteri için SAP bulutundaki verileri kopyalamak için veri kümesinin Type özelliğini **Sapcloudforcustomerresource**olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin Type özelliği: **Sapcloudforcustomerresource** olarak ayarlanmalıdır |Evet |
| Yolun | SAP C4C OData varlığının yolunu belirtin. |Evet |

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

Müşteri için SAP buluttan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **SapCloudForCustomerSource**olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **SapCloudForCustomerSource** olarak ayarlanmalıdır  | Evet |
| sorgu | Verileri okumak için özel OData sorgusunu belirtin. | Hayır |

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

Müşteri için SAP bulutuna veri kopyalamak için kopyalama etkinliğindeki havuz türünü **Sapcloudforcustomersink**olarak ayarlayın. Aşağıdaki özellikler, kopyalama etkinliği **havuzu** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **Sapcloudforcustomersink** olarak ayarlanmalıdır  | Evet |
| WriteBehavior | İşlemin yazma davranışı. "Insert", "Update" olabilir. | Hayır. Varsayılan "Insert". |
| writeBatchSize | Yazma işleminin toplu iş boyutu. En iyi performansı elde etmek için toplu iş boyutu, farklı tablo veya sunucu için farklı olabilir. | Hayır. Varsayılan 10. |

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
| EDM.Binary | Byte [] |
| Edm.Boolean | Bool |
| EDM.Byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM.Decimal | Kategori |
| Edm.Double | Çift |
| EDM.Single | Tek |
| EDM.Guid | Guid |
| EDM.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Dize |
| EDM.Time | TimeSpan |
| Edm.DateTimeOffset | Türünde |


## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

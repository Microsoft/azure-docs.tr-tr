---
title: Verileri arama dizinine Kopyala
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak bir Azure Search dizinine veri gönderme veya kopyalama hakkında bilgi edinin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 82f5d4f4c3f12b6e14e260fa004bb031247e1096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597513"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Bilişsel Arama dizinine veri kopyalama

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-azure-search-connector.md)
> * [Güncel sürüm](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Azure Bilişsel Arama dizinine veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Desteklenen herhangi bir kaynak veri deposundan verileri arama dizinine kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Azure Bilişsel Arama Connector 'a özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Azure Bilişsel Arama bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Azuresearch** olarak ayarlanmalıdır | Yes |
| url | Arama Hizmeti URL 'SI. | Yes |
| anahtar | Arama hizmeti için yönetici anahtarı. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime (veri depolduğunuz özel ağda yer alıyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |

> [!IMPORTANT]
> Verileri bir bulut veri deposundan arama dizinine kopyalarken, Azure Bilişsel Arama bağlı hizmetinde, Connactto aracılığıyla açık bölge ile bir Azure Integration Runtime başvurmanız gerekir. Bölgeyi, arama hizmetinizin bulunduğu konum olarak ayarlayın. [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)hakkında daha fazla bilgi edinin.

**Örnek:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Azure Bilişsel Arama veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Verileri Azure Bilişsel Arama 'e kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **AzureSearchIndex** olarak ayarlanmalıdır | Yes |
| indexName | Arama dizininin adı. Data Factory dizini oluşturmaz. Dizinin Azure Bilişsel Arama mevcut olması gerekir. | Yes |

**Örnek:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Azure Bilişsel Arama kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="azure-cognitive-search-as-sink"></a>Havuz olarak Azure Bilişsel Arama

Verileri Azure Bilişsel Arama 'e kopyalamak için kopyalama etkinliğindeki kaynak türünü **AzureSearchIndexSink** olarak ayarlayın. Aşağıdaki özellikler, kopyalama etkinliği **havuzu** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **AzureSearchIndexSink** olarak ayarlanmalıdır | Yes |
| writeBehavior | Dizinde bir belgenin zaten mevcut olduğu zaman birleştirilip birleştirilmeyeceğini veya değiştirip edilmeyeceğini belirtir. Bkz. [Writebehavior özelliği](#writebehavior-property).<br/><br/>İzin verilen değerler şunlardır: **birleştirme** (varsayılan) ve **karşıya yükleme**. | No |
| writeBatchSize | Arabellek boyutu writeBatchSize ulaştığında verileri arama dizinine yükler. Ayrıntılar için bkz. [Writebatchsize özelliği](#writebatchsize-property) .<br/><br/>İzin verilen değerler: tamsayı 1 ile 1.000 arasında; Varsayılan değer 1000 ' dir. | No |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

### <a name="writebehavior-property"></a>WriteBehavior özelliği

Veri yazarken AzureSearchSink upları. Diğer bir deyişle, bir belge yazarken belge anahtarı arama dizininde zaten mevcutsa Azure Bilişsel Arama bir çakışma özel durumu oluşturmak yerine mevcut belgeyi güncelleştirir.

AzureSearchSink aşağıdaki iki yukarı yönlü davranışı sağlar (AzureSearch SDK kullanarak):

- **Birleştir**: yeni belgedeki tüm sütunları mevcut bir belge ile birleştirin. Yeni belgede null değeri olan sütunlarda, var olan bir değer korunur.
- **Karşıya yükle**: yeni belge var olanı yenisiyle değiştirir. Yeni belgede belirtilmeyen sütunlar için, varolan belgede null olmayan bir değer olup olmadığı için değer null olarak ayarlanır.

Varsayılan davranış **birleştirilir**.

### <a name="writebatchsize-property"></a>WriteBatchSize özelliği

Azure Bilişsel Arama hizmeti, toplu iş olarak belge yazmayı destekler. Batch, 1 ile 1.000 arasında eylem içerebilir. Bir eylem, karşıya yükleme/birleştirme işlemini gerçekleştirmek için bir belgeyi işler.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Veri türü desteği

Aşağıdaki tabloda bir Azure Bilişsel Arama veri türünün desteklenip desteklenmediğini belirtir.

| Azure Bilişsel Arama veri türü | Azure Bilişsel Arama havuzunda desteklenir |
| ---------------------- | ------------------------------ |
| Dize | Y |
| Int32 | Y |
| Int64 | Y |
| Çift | Y |
| Boole | Y |
| Veri Timesapmayı | Y |
| Dize dizisi | N |
| GeographyPoint | N |

Şu anda diğer veri türleri, yani ComplexType desteklenmez. Desteklenen Azure Bilişsel Arama veri türlerinin tam listesi için bkz. [desteklenen veri türleri (Azure bilişsel arama)](/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
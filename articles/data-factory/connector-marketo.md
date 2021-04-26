---
title: Azure Data Factory kullanarak Marketo 'dan veri kopyalama (Önizleme)
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak Marketo 'dan desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/04/2020
ms.author: jingwang
ms.openlocfilehash: aae25536f0ac1d5d3b43b5173d3e5789cd66a774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385651"
---
# <a name="copy-data-from-marketo-using-azure-data-factory-preview"></a>Azure Data Factory kullanarak Marketo 'dan veri kopyalama (Önizleme)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Marketo 'dan veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

> [!IMPORTANT]
> Bu bağlayıcı Şu anda önizleme aşamasındadır. Deneyebilir ve geri bildirimde bulunun. Çözümünüzde bir önizleme bağlayıcısı bağımlılığı olmasını istiyorsanız lütfen [Azure desteğine](https://azure.microsoft.com/support/) başvurun.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Marketo Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Marketo 'dan, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Şu anda, dış CRM ile tümleştirilmiş olan Marketo örneği desteklenmez.

>[!NOTE]
>Bu Marketo Bağlayıcısı REST API Marketo 'nın üzerine kurulmuştur. Marketo 'nın hizmet tarafında [eşzamanlı istek sınırına](https://developers.marketo.com/rest-api/) sahip olduğunu unutmayın. "REST API kullanılmaya çalışılırken hata oluştu:" 100 "en yüksek hız sınırı ' 20 ' saniye (606) ile aşıldı" veya "REST API kullanılmaya çalışılırken hata oluştu: eşzamanlı erişim sınırı ' 10 ' değerine ulaşıldı (615)", hizmete gönderilen isteklerin sayısını azaltmak için eşzamanlı kopyalama etkinliği çalıştırmalarını azaltmayı göz önünde bulundurun.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, Marketo bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler Marketo bağlantılı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Marketo** olarak ayarlanmalıdır | Yes |
| endpoint | Marketo sunucusunun uç noktası. (örn. 123-ABC-321.mktorest.com)  | Yes |
| clientId | Marketo hizmetinizin istemci kimliği.  | Yes |
| clientSecret | Marketo hizmetinizin istemci gizli anahtarı. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| useEncryptedEndpoints | Veri kaynağı uç noktalarının HTTPS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer true şeklindedir.  | No |
| Usehostdoğrulaması | Sunucu sertifikasında, TLS üzerinden bağlanırken sunucunun ana bilgisayar adıyla eşleşecek şekilde, ana bilgisayar adının istenip istenmeyeceğini belirtir. Varsayılan değer true şeklindedir.  | No |
| Usepeerdoğrulaması | TLS üzerinden bağlanılırken sunucu kimliğinin doğrulanıp doğrulanmayacağını belirtir. Varsayılan değer true şeklindedir.  | No |

**Örnek:**

```json
{
    "name": "MarketoLinkedService",
    "properties": {
        "type": "Marketo",
        "typeProperties": {
            "endpoint" : "123-ABC-321.mktorest.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Marketo veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Marketo 'dan veri kopyalamak için, veri kümesinin Type özelliğini **menkul ToObject** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin Type özelliği şu şekilde ayarlanmalıdır: **menkul, ToObject** | Yes |
| tableName | Tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "MarketoDataset",
    "properties": {
        "type": "MarketoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Marketo linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Marketo kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="marketo-as-source"></a>Kaynak olarak Marketo

Marketo 'dan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **Pazarlamakaynağı** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği şu şekilde ayarlanmalıdır: **menkul tosource** | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM Activitiy_Types"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromMarketo",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Marketo input dataset name>",
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
                "type": "MarketoSource",
                "query": "SELECT top 1000 * FROM Activitiy_Types"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

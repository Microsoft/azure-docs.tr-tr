---
title: Azure Data Factory kullanarak QuickBooks Online 'dan veri kopyalama (Önizleme)
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak QuickBooks Online 'dan desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: eecbcb817ad31480f8f6c3c7272328d06b17c081
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384070"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Azure Data Factory kullanarak QuickBooks Online 'dan veri kopyalama (Önizleme)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, QuickBooks Online 'dan veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

> [!IMPORTANT]
> Bu bağlayıcı Şu anda önizleme aşamasındadır. Deneyebilir ve geri bildirimde bulunun. Çözümünüzde bir önizleme bağlayıcısı bağımlılığı olmasını istiyorsanız lütfen [Azure desteğine](https://azure.microsoft.com/support/) başvurun.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu QuickBooks Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

QuickBooks Online 'daki verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Bu bağlayıcı, QuickBooks OAuth 2,0 kimlik doğrulamasını destekler.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, QuickBooks bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özelliklerle ilgili ayrıntıları sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

QuickBooks bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **QuickBooks** olarak ayarlanmalıdır | Yes |
| connectionProperties | QuickBooks 'a nasıl bağlanılacağını tanımlayan bir özellik grubu. | Yes |
| ***Altında `connectionProperties` :*** | | |
| endpoint | QuickBooks Online sunucusunun uç noktası. (yani, quickbooks.api.intuit.com)  | Yes |
| CompanyID | Yetkilendirmek için QuickBooks şirketinin şirket KIMLIĞI. Şirket KIMLIĞINI bulma hakkında bilgi için, bkz. [ŞIRKET kimliğini bulma nasıl yaparım?](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551). | Yes |
| consumerKey | OAuth 2,0 kimlik doğrulaması için QuickBooks Online uygulamanızın istemci KIMLIĞI. [Buradan](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app)daha fazla bilgi edinebilirsiniz. | Yes |
| consumerSecret | OAuth 2,0 kimlik doğrulaması için QuickBooks Online uygulamanızın istemci gizli dizisi. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| refreshToken | QuickBooks uygulamasıyla ilişkili OAuth 2,0 yenileme belirteci. [Buradan](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app)daha fazla bilgi edinebilirsiniz. Göz yenileme belirtecinin 180 gün sonra zaman aşımına erdiğini göreceksiniz. Müşterinin yenileme belirtecini düzenli olarak güncelleştirmesi gerekiyor. <br/>Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın.| Yes |
| useEncryptedEndpoints | Veri kaynağı uç noktalarının HTTPS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer true şeklindedir.  | No |

**Örnek:**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "connectionProperties": {
                "endpoint": "quickbooks.api.intuit.com",
                "companyId": "<company id>",
                "consumerKey": "<consumer key>", 
                "consumerSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "refreshToken": {
                     "type": "SecureString",
                     "value": "<refresh token>"
                },
                "useEncryptedEndpoints": true
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, QuickBooks veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

QuickBooks Online 'daki verileri kopyalamak için, veri kümesinin Type özelliğini **Quickbooksobject** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **Quickbooksobject** olarak ayarlanmalıdır | Yes |
| tableName | Tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, QuickBooks kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="quickbooks-as-source"></a>Kaynak olarak QuickBooks

QuickBooks Online 'dan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **Quickbookssource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Quickbookssource** olarak ayarlanmalıdır | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromQuickBooks",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<QuickBooks input dataset name>",
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
                "type": "QuickBooksSource",
                "query": "SELECT * FROM \"Bill\" WHERE Id = '123' "
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="copy-data-from-quickbooks-desktop"></a>QuickBooks Desktop 'tan veri kopyalama

Azure Data Factory kopyalama etkinliği verileri doğrudan QuickBooks Desktop 'tan kopyalayamıyor. QuickBooks Desktop 'tan veri kopyalamak için, QuickBooks verilerinizi bir virgülle ayrılmış değerler (CSV) dosyasına aktarın ve ardından dosyayı Azure Blob depolama alanına yükleyin. Buradan, verileri istediğiniz havuza kopyalamak için Data Factory kullanabilirsiniz.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

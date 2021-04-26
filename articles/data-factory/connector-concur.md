---
title: Azure Data Factory kullanarak Concur verilerini kopyalama (Önizleme)
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak, verileri Concur 'tan desteklenen havuz veri depolarına kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jingwang
ms.openlocfilehash: 974682aa9e9bebd6e2d17237decd8389174ff28d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383679"
---
# <a name="copy-data-from-concur-using-azure-data-factory-preview"></a>Azure Data Factory kullanarak Concur verilerini kopyalama (Önizleme)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Concur verilerini kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

> [!IMPORTANT]
> Bu bağlayıcı Şu anda önizleme aşamasındadır. Deneyebilir ve geri bildirimde bulunun. Çözümünüzde bir önizleme bağlayıcısı bağımlılığı olmasını istiyorsanız lütfen [Azure desteğine](https://azure.microsoft.com/support/) başvurun.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Concur Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Concur kaynağından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

> [!NOTE]
> İş ortağı hesabı şu anda desteklenmiyor.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Concur bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Concur bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Concur** olarak ayarlanmalıdır | Yes |
| connectionProperties | Concur 'e nasıl bağlanılacağını tanımlayan bir özellik grubu. | Yes |
| ***Altında `connectionProperties` :*** | | |
| authenticationType | İzin verilen değerler `OAuth_2.0_Bearer` ve `OAuth_2.0` (eski). OAuth 2,0 kimlik doğrulama seçeneği, Şubat 2017 ' den beri kullanım dışı olan eski Concur API 'siyle birlikte kullanılabilir. | Yes |
| konak | Concur sunucusunun uç noktası, `implementation.concursolutions.com` ör.  | Yes |
| baseUrl | Concur 'in yetkilendirme URL 'sinin temel URL 'SI. | `OAuth_2.0_Bearer`Kimlik doğrulaması için Evet |
| clientId | Concur uygulama yönetimi tarafından sağlanan uygulama istemci KIMLIĞI.  | Yes |
| clientSecret | İstemci KIMLIĞINE karşılık gelen istemci parolası. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | `OAuth_2.0_Bearer`Kimlik doğrulaması için Evet |
| username | Concur Service 'e erişmek için kullandığınız Kullanıcı adı. | Yes |
| password | Kullanıcı adı alanında belirttiğiniz kullanıcı adına karşılık gelen parola. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| useEncryptedEndpoints | Veri kaynağı uç noktalarının HTTPS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer true şeklindedir.  | No |
| Usehostdoğrulaması | Sunucu sertifikasında, TLS üzerinden bağlanırken sunucunun ana bilgisayar adıyla eşleşecek şekilde, ana bilgisayar adının istenip istenmeyeceğini belirtir. Varsayılan değer true şeklindedir.  | No |
| Usepeerdoğrulaması | TLS üzerinden bağlanılırken sunucu kimliğinin doğrulanıp doğrulanmayacağını belirtir. Varsayılan değer true şeklindedir.  | No |

**Örnek:**

```json
{ 
    "name": "ConcurLinkedService", 
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "connectionProperties": {
                "host":"<host e.g. implementation.concursolutions.com>",
                "baseUrl": "<base URL for authorization e.g. us-impl.api.concursolutions.com>",
                "authenticationType": "OAuth_2.0_Bearer",
                "clientId": "<client id>",
                "clientSecret": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "username": "fakeUserName",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
} 
```

**Örnek (eski):**

Aşağıda, `connectionProperties` kimlik doğrulaması olmadan ve kullanarak eski bir bağlı hizmet modeli verilmiştir `OAuth_2.0` .

```json
{
    "name": "ConcurLinkedService",
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "clientId" : "<clientId>",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Concur veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Concur 'den veri kopyalamak için, veri kümesinin Type özelliğini **Concurobject** olarak ayarlayın. Bu veri kümesi türünde ek türe özgü özellik yok. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **Concurobject** olarak ayarlanmalıdır | Yes |
| tableName | Tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |


**Örnek**

```json
{
    "name": "ConcurDataset",
    "properties": {
        "type": "ConcurObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Concur linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Concur source tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="concursource-as-source"></a>Kaynak olarak ConcurSource

Concur 'den veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **Concursource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Concursource** olarak ayarlanmalıdır | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM Opportunities where Id = xxx "`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromConcur",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Concur input dataset name>",
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
                "type": "ConcurSource",
                "query": "SELECT * FROM Opportunities where Id = xxx"
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

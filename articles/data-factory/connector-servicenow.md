---
title: Azure Data Factory kullanarak ServiceNow 'dan veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak ServiceNow 'dan desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 933b12f852fcbcc20e50f3c89d597bbe6b84bd8e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680225"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Azure Data Factory kullanarak ServiceNow 'dan veri kopyalama

Bu makalede, ServiceNow 'dan veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu ServiceNow Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

ServiceNow 'dan, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Azure Data Factory, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar, bu nedenle bu bağlayıcıyı kullanarak herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler ServiceNow bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

ServiceNow bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **ServiceNow** olarak ayarlanmalıdır | Evet |
| endpoint | ServiceNow sunucusunun uç noktası (`http://<instance>.service-now.com`).  | Evet |
| authenticationType | Kullanılacak kimlik doğrulaması türü. <br/>İzin verilen değerler: **Basic**, **OAuth2** | Evet |
| kullanıcı adı | Temel ve OAuth2 kimlik doğrulaması için ServiceNow sunucusuna bağlanmak için kullanılan Kullanıcı adı.  | Evet |
| password | Temel ve OAuth2 kimlik doğrulaması için Kullanıcı adına karşılık gelen parola. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Evet |
| ClientID | OAuth2 kimlik doğrulaması için istemci KIMLIĞI.  | Hayır |
| clientSecret | OAuth2 kimlik doğrulaması için istemci parolası. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Hayır |
| useEncryptedEndpoints | Veri kaynağı uç noktalarının HTTPS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer true 'dur.  | Hayır |
| Usehostdoğrulaması | SSL üzerinden bağlanılırken sunucunun ana bilgisayar adıyla eşleşmesi için Sunucu sertifikasında ana bilgisayar adının gerekip gerekmediğini belirtir. Varsayılan değer true 'dur.  | Hayır |
| Usepeerdoğrulaması | SSL üzerinden bağlanırken sunucunun kimliğini doğrulayıp doğrulamayamayacağını belirtir. Varsayılan değer true 'dur.  | Hayır |

**Örnek:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm ServiceNow veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

ServiceNow 'dan verileri kopyalamak için, veri kümesinin Type özelliğini **Servicenowobject**olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in Type özelliği: **Servicenowobject** olarak ayarlanmalıdır | Evet |
| tableName | Tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, ServiceNow kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="servicenow-as-source"></a>Kaynak olarak ServiceNow

ServiceNow 'dan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **Servicenowsource**olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği: **Servicenowsource** olarak ayarlanmalıdır | Evet |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM Actual.alm_asset"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

Sorgudaki ServiceNow için şemayı ve sütunu belirtirken aşağıdaki gibi bir yere göz önünde, **kopyalama performansı ile Ilgili [performans ipuçlarına](#performance-tips) bakın**.

- **Şema:** dosyayı ServiceNow sorgusunda `Actual` veya `Display` olarak belirtin; Bu, [ServiceNow Restalow API 'lerini](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET)çağırırken doğru veya yanlış olarak `sysparm_display_value` parametresi olarak bakabilmenizdir. 
- **Sütun:** `Actual` şeması altındaki gerçek değerin sütun adı `[column name]_value`, ancak `Display` şeması altındaki görüntü değeri `[column name]_display_value`. Sütun adının sorguda kullanılan şemaya eşlenmesi gerektiğini aklınızda olacak şekilde ayarlayın.

**Örnek sorgu:** 
`SELECT col_value FROM Actual.alm_asset` veya 
`SELECT col_display_value FROM Display.alm_asset`

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Performans ipuçları

### <a name="schema-to-use"></a>Kullanılacak şema

ServiceNow 2 farklı şemaya sahiptir, biri gerçek verileri döndüren **"gerçek"** , diğeri ise veri görüntüleme değerlerini döndüren **"görüntü"** olur. 

Sorgunuzda bir filtreniz varsa, daha iyi kopyalama performansı olan "gerçek" şemayı kullanın. "Gerçek" şemaya göre sorgulama yaparken ServiceNow, verileri yalnızca filtrelenmiş sonuç kümesi döndürecek şekilde alırken filtreyi yerel olarak destekler, ancak "Display" şeması sorgulanırken, ADF tüm verileri alın ve filtreyi dahili olarak uygulayın.

### <a name="index"></a>Dizin oluşturma

ServiceNow tablo dizini sorgu performansının artırılmasına yardımcı olabilir ve [Tablo dizini oluşturma](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html)bölümüne bakın.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

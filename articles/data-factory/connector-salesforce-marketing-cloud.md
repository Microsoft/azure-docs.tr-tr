---
title: Azure Data Factory kullanarak Salesforce pazarlama bulutlarından veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak Salesforce pazarlama bulutlarından desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: d66341507b9237e4d41c31997fd59b8a038cb433
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680371"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Azure Data Factory kullanarak Salesforce pazarlama bulutlarından veri kopyalama

Bu makalede, Salesforce pazarlama bulutundaki verileri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu Salesforce Marketing Cloud Connector, aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Salesforce pazarlama bulutundaki verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Salesforce Marketing Cloud Connector, OAuth 2 kimlik doğrulamasını destekler. [Salesforce Marketing Cloud REST API](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm)üzerine kurulmuştur.

>[!NOTE]
>Bu bağlayıcı özel nesneler veya özel veri uzantıları almayı desteklemiyor.

## <a name="getting-started"></a>Başlarken

.NET SDK, Python SDK, Azure PowerShell, REST API veya Azure Resource Manager şablonu kullanarak kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](quickstart-create-data-factory-dot-net.md) .

Aşağıdaki bölümler Salesforce pazarlama bulut bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler, Salesforce pazarlama bulutu bağlı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği şu şekilde ayarlanmalıdır: **Salesforcepazarbulutu** | Evet |
| ClientID | Salesforce pazarlama bulut uygulamasıyla ilişkili istemci KIMLIĞI.  | Evet |
| clientSecret | Salesforce pazarlama bulut uygulamasıyla ilişkili istemci gizli dizisi. Bu alanı, ADF 'de güvenli bir şekilde depolamak veya Azure Key Vault parolayı depolamak için bir SecureString olarak işaretlemeyi seçebilir ve veri kopyalama işlemini gerçekleştirirken ADF kopyalama etkinliği çekmeye izin verebilir. [Key Vault mağaza kimlik bilgilerinden](store-credentials-in-key-vault.md)daha fazla bilgi edinin. | Evet |
| useEncryptedEndpoints | Veri kaynağı uç noktalarının HTTPS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer true 'dur.  | Hayır |
| Usehostdoğrulaması | SSL üzerinden bağlanılırken sunucunun ana bilgisayar adıyla eşleşmesi için Sunucu sertifikasında ana bilgisayar adının gerekip gerekmediğini belirtir. Varsayılan değer true 'dur.  | Hayır |
| Usepeerdoğrulaması | SSL üzerinden bağlanırken sunucunun kimliğini doğrulayıp doğrulamayamayacağını belirtir. Varsayılan değer true 'dur.  | Hayır |

**Örnek:**

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Salesforce pazarlama bulut veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Salesforce pazarlama bulutundaki verileri kopyalamak için veri kümesinin Type özelliğini **Salesforcemarketing Cloudobject**olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin Type özelliği şu şekilde ayarlanmalıdır: **Salesforcepazarbir dobject** | Evet |
| tableName | Tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Salesforce pazarlama bulut kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="salesforce-marketing-cloud-as-source"></a>Kaynak olarak Salesforce pazarlama bulutu

Salesforce pazarlama bulutundaki verileri kopyalamak için kopyalama etkinliğindeki kaynak türünü **Salesforcemarketing CloudSource**olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği şu şekilde ayarlanmalıdır: **Salesforcepazarlamakaynağı** | Evet |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM MyTable"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
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

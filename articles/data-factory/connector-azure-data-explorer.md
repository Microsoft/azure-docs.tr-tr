---
title: Azure Data Factory kullanarak Azure Veri Gezgini veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak verileri Azure Veri Gezgini kopyalama hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 6945e4dcf6baf44881bd5b13571dd03e3dee41ed
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300550"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Veri Gezgini veri kopyalama

Bu makalede, [Azure Veri Gezgini](../data-explorer/data-explorer-overview.md)veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanır. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Azure Veri Gezgini Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Desteklenen herhangi bir kaynak veri deposundan Azure Veri Gezgini veri kopyalayabilirsiniz. Ayrıca, Azure Veri Gezgini verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliğinin kaynak veya havuz olarak desteklediği veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

>[!NOTE]
>Şirket içinde barındırılan tümleştirme çalışma zamanını kullanarak şirket içi veri deposu aracılığıyla veya Azure Veri Gezgini 'den veri kopyalama, sürüm 3,14 ve sonrasında desteklenir.

Azure Veri Gezgini Bağlayıcısı ile şunları yapabilirsiniz:

* Bir **hizmet sorumlusu**ile Azure Active Directory (Azure AD) uygulama belirteci kimlik doğrulamasını kullanarak verileri kopyalayın.
* Kaynak olarak, bir KQL (kusto) sorgusu kullanarak verileri alın.
* Havuz olarak, hedef tabloya veri ekleyin.

## <a name="getting-started"></a>Başlarken

>[!TIP]
>Azure Veri Gezgini Bağlayıcısı hakkında yönergeler için, bkz. [Azure Data Factory kullanarak verileri azure Veri Gezgini kopyalama/buradan kopyalama](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Azure Veri Gezgini Connector 'a özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmeti özellikleri

Azure Veri Gezgini Bağlayıcısı hizmet sorumlusu kimlik doğrulamasını kullanır. Hizmet sorumlusu almak ve izin vermek için aşağıdaki adımları izleyin:

1. Uygulamanızı [bir Azure AD kiracısıyla kaydetme](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)bölümündeki adımları izleyerek Azure Active Directory bir uygulama varlığını kaydedin. Bağlı hizmetini tanımlamak için kullandığınız şu değerleri not edin:

    - Uygulama Kimliği
    - Uygulama anahtarı
    - Kiracı Kimliği

2. Hizmet sorumlusuna Azure Veri Gezgini 'de doğru izinleri verin. Roller ve izinler ve izinleri yönetme hakkında ayrıntılı bilgi için bkz. [Azure Veri Gezgini veritabanını yönetme izinleri](../data-explorer/manage-database-permissions.md) . Genel olarak şunları yapmanız gerekir:

    - **Kaynak olarak**, veritabanınıza en azından **veritabanı Görüntüleyicisi** rolü verin
    - **Havuz olarak**, veritabanınıza en azından **veritabanı** alma rolünü verin

>[!NOTE]
>Yazmak için Data Factory Kullanıcı arabirimini kullandığınızda, oturum açma Kullanıcı hesabınız Azure Veri Gezgini kümelerini, veritabanlarını ve tablolarını listelemek için kullanılır. Bu işlemler için izniniz yoksa el ile adı girin.

Azure Veri Gezgini bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | **Type** özelliği **AzureDataExplorer**olarak ayarlanmalıdır. | Evet |
| endpoint | Biçiminde olacak şekilde `https://<clusterName>.<regionName>.kusto.windows.net`Azure Veri Gezgini kümesinin uç nokta URL 'si. | Evet |
| database | Veritabanının adı. | Evet |
| tenant | Kiracı bilgileri (etki alanı adı veya Kiracı kimliği), uygulamanızın bulunduğu altında belirtin. Bu, [kusto bağlantı dizesinde](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)"Authority ID" olarak bilinir. Azure portal, fare işaretçisini, sağ üst köşesine getirerek alın. | Evet |
| servicePrincipalId | Uygulamanın istemci kimliği belirtin. Bu, [kusto bağlantı dizesinde](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)"AAD uygulama istemci kimliği" olarak bilinir. | Evet |
| servicePrincipalKey | Uygulama anahtarını belirtin. Bu, [kusto bağlantı dizesinde](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)"AAD uygulama anahtarı" olarak bilinir. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan güvenli verilere başvurun](store-credentials-in-key-vault.md). | Evet |

**Bağlı hizmet özellikleri örneği:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [Azure Data Factory veri kümeleri](concepts-datasets-linked-services.md). Bu bölümde, Azure Veri Gezgini veri kümesinin desteklediği özellikler listelenmiştir.

Verileri Azure Veri Gezgini kopyalamak için, veri kümesinin Type özelliğini **AzureDataExplorerTable**olarak ayarlayın.

Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | **Type** özelliği **AzureDataExplorerTable**olarak ayarlanmalıdır. | Evet |
| table | Bağlı hizmetin başvurduğu tablonun adı. | Havuz için Evet; Kaynak için Hayır |

**Veri kümesi özellikleri örneği:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için, bkz. Azure Data Factory işlem [hatları ve etkinlikleri](concepts-pipelines-activities.md). Bu bölümde Azure Veri Gezgini kaynakları ve havuzları desteğinin özelliklerinin bir listesi verilmiştir.

### <a name="azure-data-explorer-as-source"></a>Kaynak olarak Azure Veri Gezgini

Azure Veri Gezgini 'den veri kopyalamak için, kopyalama etkinliği kaynağındaki **Type** özelliğini **AzureDataExplorerSource**olarak ayarlayın. Kopyalama etkinliği aşağıdaki özellikler desteklenir **source** bölümü:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının **Type** özelliği şu şekilde ayarlanmalıdır: **AzureDataExplorerSource** | Evet |
| query | Bir [KQL biçiminde](/azure/kusto/query/)verilen salt okunurdur bir istek. Özel KQL sorgusunu başvuru olarak kullanın. | Evet |
| queryTimeout | Sorgu isteğinin zaman aşımına uğramadan önce beklenecek bekleme süresi. Varsayılan değer 10 dakikadır (00:10:00); izin verilen en büyük değer 1 saattir (01:00:00). | Hayır |
| Kesilmesi | Döndürülen sonuç kümesinin kesilme edilip edilmeyeceğini belirtir. Varsayılan olarak, sonuç 500.000 kayıt veya 64 megabayt (MB) sonra kesilir. Etkinliğin doğru davranış sağlamak için kesme kesinlikle önerilir. |Hayır |

>[!NOTE]
>Varsayılan olarak, Azure Veri Gezgini kaynağı 500.000 kayıt veya 64 MB boyutunda bir boyut sınırına sahiptir. Tüm kayıtları kesme olmadan almak için sorgunuzun başlangıcında belirtebilirsiniz `set notruncation;` . Daha fazla bilgi için bkz. [sorgu sınırları](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Havuz olarak Azure Veri Gezgini

Verileri Azure Veri Gezgini kopyalamak için kopyalama etkinliği havuzundan tür özelliğini **AzureDataExplorerSink**olarak ayarlayın. Kopyalama etkinliği aşağıdaki özellikler desteklenir **havuz** bölümü:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği havuzunun **Type** özelliği şu şekilde ayarlanmalıdır: **AzureDataExplorerSink**. | Evet |
| ınestionmappingname | Kusto tablosundaki önceden oluşturulmuş [eşlemenin](/azure/kusto/management/mappings#csv-mapping) adı. Sütunları kaynaktan Azure Veri Gezgini eşlemek için (CSV/JSON/avro biçimleri de dahil olmak üzere [tüm desteklenen kaynak depoları ve biçimler](copy-activity-overview.md#supported-data-stores-and-formats)için geçerlidir), kopyalama etkinliği [sütun eşlemesini](copy-activity-schema-and-type-mapping.md) (örtük olarak ada veya açıkça yapılandırılmış şekilde) kullanabilirsiniz ve /veya Azure Veri Gezgini eşlemeleri. | Hayır |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özellikler hakkında daha fazla bilgi için bkz. [arama etkinliği](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Sonraki adımlar

* Azure Data Factory içindeki kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).

* [Azure Data Factory verileri Azure Veri Gezgini kopyalama](/azure/data-explorer/data-factory-load-data)hakkında daha fazla bilgi edinin.

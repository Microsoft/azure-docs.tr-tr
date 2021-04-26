---
title: Azure Izleyici 'de çalışma alanı verilerini dışarı aktarma Log Analytics (Önizleme)
description: Log Analytics veri dışa aktarma, seçili tabloların verilerini Log Analytics çalışma alanınızdan Azure depolama hesabına veya Azure Event Hubs toplandığından sürekli olarak dışarı aktaralmanıza olanak sağlar.
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 02/07/2021
ms.openlocfilehash: 981ebbecd4783ae529c5b0d97c82ea052511f77f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384196"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Azure Izleyici 'de çalışma alanı verilerini dışarı aktarma Log Analytics (Önizleme)
Azure Izleyici 'de Log Analytics çalışma alanı verileri dışarı aktarma işlemi, Log Analytics çalışma alanınızdaki seçili tablolardan verileri sürekli olarak bir Azure depolama hesabına veya Azure Event Hubs toplanarak dışarı aktaralmanıza olanak sağlar. Bu makalede, bu özellik hakkında ayrıntılar ve çalışma alanlarınızdaki veri dışarı aktarmayı yapılandırma adımları sağlanmaktadır.

## <a name="overview"></a>Genel Bakış
Log Analytics çalışma alanınız için veri dışa aktarma yapılandırıldıktan sonra, çalışma alanındaki seçili tablolara gönderilen tüm yeni veriler, saatlik ekleme Blobları veya Olay Hub 'ında neredeyse gerçek zamanlı olarak depolama hesabınıza otomatik olarak dışarı aktarılabilir.

![Veri dışarı aktarmaya genel bakış](media/logs-data-export/data-export-overview.png)

Dahil edilen tablolardaki tüm veriler bir filtre olmadan verilir. Örneğin, *Securityevent* tablosu için bir veri dışa aktarma kuralı yapılandırdığınızda, *securityevent* tablosuna gönderilen tüm veriler yapılandırma zamanından itibaren dışarı aktarılabilir.


## <a name="other-export-options"></a>Diğer dışarı aktarma seçenekleri
Log Analytics çalışma alanı verileri dışarı aktarma bir Log Analytics çalışma alanından sürekli olarak veri aktarır. Belirli senaryolara yönelik verileri dışarı aktarmaya yönelik diğer seçenekler şunlardır:

- Mantıksal uygulama kullanılarak günlük sorgusundan zamanlanan dışarı aktarma. Bu, veri dışa aktarma özelliğine benzerdir, ancak filtrelenmiş veya toplanmış verileri Azure depolama 'ya göndermenizi sağlar. Bu yöntem, [günlük sorgusu sınırlarına](../service-limits.md#log-analytics-workspaces)tabidir, bkz. [Logic App kullanarak Log Analytics çalışma alanındaki verileri Azure depolama 'ya arşivleme](logs-export-logic-app.md).
- PowerShell betiği kullanılarak yerel makineye bir kerelik dışarı aktarma. Bkz. [Invoke-Azoperationalınsightsqueryexport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="limitations"></a>Sınırlamalar

- Yapılandırma, şu anda CLı veya REST istekleri kullanılarak gerçekleştirilebilir. Azure portal veya PowerShell henüz desteklenmiyor.
- ```--export-all-tables```CLI ve REST seçenekleri desteklenmez ve kaldırılacak. Dışarı aktarma kurallarında Tablo listesini açıkça sağlamanız gerekir.
- Desteklenen tablolar şu anda aşağıdaki [desteklenen tablolar](#supported-tables) bölümünde yer aldığı sınırlandırıldı. Örneğin, özel günlük tabloları Şu anda desteklenmiyor.
- Veri dışa aktarma kuralı desteklenmeyen bir tablo içeriyorsa, işlem başarılı olur ancak tablo desteklenene kadar bu tablo için hiçbir veri aktarılmaz. 
- Veri dışa aktarma kuralı mevcut olmayan bir tablo içeriyorsa hata vererek başarısız olur ```Table <tableName> does not exist in the workspace``` .
- Log Analytics çalışma alanınız aşağıdakiler dışında herhangi bir bölgede olabilir:
  - Azure Kamu bölgeleri
  - Batı Japonya
  - Güney Brezilya Doğu
  - Norveç Doğu
  - BAE Kuzey
- Bir çalışma alanında iki dışarı aktarma kuralı oluşturabilirsiniz; içindeki bir olay hub 'ı ve depolama hesabına yönelik bir kural olabilir.
- Hedef depolama hesabı veya Olay Hub 'ı Log Analytics çalışma alanıyla aynı bölgede olmalıdır.
- Aktarılacak tabloların adları, bir depolama hesabı için 60 karakterden uzun ve bir olay hub 'ına en fazla 47 karakterden oluşabilir. Daha uzun adlara sahip tablolar verilmeyecektir.
- Azure Data Lake Storage için blob desteğinin eklenmesi artık [sınırlı genel önizlemede](https://azure.microsoft.com/updates/append-blob-support-for-azure-data-lake-storage-preview/)

## <a name="data-completeness"></a>Veri bütünlüğü
Verilerin dışarı aktarılması, hedefin kullanılamaz durumda olması durumunda 30 dakikaya kadar veri göndermeyi yeniden denemeye devam edecektir. 30 dakika sonra hala kullanılamıyorsa, veriler hedef kullanılabilir olana kadar atılır.

## <a name="cost"></a>Maliyet
Şu anda veri dışa aktarma özelliği için ek ücret alınmaz. Veri dışa aktarma fiyatlandırması gelecekte duyurulacaktır ve faturalandırma başlamadan önce bir uyarı verilir. Bildirim süresinden sonra veri dışa aktarmayı kullanmaya devam etmeyi seçerseniz, ilgili ücret üzerinden faturalandırılırsınız.

## <a name="export-destinations"></a>Hedefleri dışarı aktar

### <a name="storage-account"></a>Depolama hesabı
Veriler, Azure Izleyici 'ye ulaştığı ve saatlik ekleme Blobları 'nda depolanan depolama hesaplarına gönderilir. Veri dışa aktarma yapılandırması, depolama hesabındaki her tablo için, adı *ve ardından tablonun adını taşıyan* bir kapsayıcı oluşturur. Örneğin, *securityevent* tablosu, *har-securityevent* adlı bir kapsayıcıya gönderilir.

Depolama hesabı blob yolu *WorkspaceResourceId =/Subscriptions/Subscription-id/ResourceGroups/ \<resource-group\> /providers/Microsoft.operationalinsights/Workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h = \<two-digit 24-hour clock hour\> /m = 00/PT1H.js'* dir. Ekleme Blobları depolama alanındaki 50K yazmaları ile sınırlı olduğundan, eklenen BLOB sayısı yüksek ise, eklenen Blobların sayısı uzatabilirler. Bu tür bir örnekte Blobların adlandırma deseninin PT1H_ #. JSON olması, burada #, artımlı blob sayısıdır.

Depolama hesabı veri biçimi [JSON hatdır](../essentials/resource-logs-blob-format.md). Bu, her kaydın, dış kayıt dizisi olmadan ve JSON kayıtları arasında hiçbir virgül olmadan bir yeni satır tarafından sınırlandırılır. 

[![Depolama örneği verileri](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics veri dışa aktarma, zaman tabanlı bekletme ilkelerinde *allowProtectedAppendWrites* ayarı etkinse, sabit depolama hesaplarına ekleme Blobları yazabilir. Bu, bir ekleme blobuna yeni blokların yazılmasına izin verir, bu sayede koruma ve uyumluluğu koruma sağlar. Bkz. [korumalı ekleme Blobları yazmasına Izin ver](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

> [!NOTE]
> Azure Data Lake depolama için blob desteği ekleme işlemi, tüm Azure bölgelerinde önizleme aşamasında kullanıma sunulmuştur. Depolama Azure Data Lake için dışarı aktarma kuralı oluşturmadan önce [sınırlı genel önizlemeye kaydolun](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pURDk2NjMzUTVEVzU5UU1XUlRXSTlHSlkxQS4u) . Dışarı aktarma işlemi bu kayıt olmadan çalışmaz.

### <a name="event-hub"></a>Olay hub'ı
Veriler, Azure Izleyici 'ye ulaştığında neredeyse gerçek zamanlı olarak olay hub 'ınıza gönderilir. Bir olay hub 'ı, adı " *ve ardından tablonun adı ile* dışarı aktarma yaptığınız her veri türü için oluşturulur. Örneğin, *securityevent* tablosu, *har-securityevent* adlı bir olay hub 'ına gönderilir. Dışarı aktarılmış verilerin belirli bir olay hub 'ına ulaşmasını istiyorsanız veya 47 karakter sınırını aşan bir ada sahip bir tablonuz varsa, kendi olay hub 'ınızın adını girip tanımlanmış tablolar için tüm verileri buna aktarabilirsiniz.

> [!IMPORTANT]
> [Ad alanı başına desteklenen Olay Hub 'ları sayısı 10 ' dur](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). 10 ' dan fazla tablo dışa aktardığınızda, tüm tablolarınızı bu olay hub 'ına aktarmak için kendi olay hub 'ınızın adını sağlayın.

Dikkat edilmesi gerekenler:
1. ' Temel ' Olay Hub 'ı SKU, daha düşük olay boyutu [sınırını](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) destekler ve çalışma alanınızdaki bazı Günlükler onu aşabilir ve bırakılamaz. Dışarı aktarma hedefi olarak ' Standard ' veya ' adanmış ' Olay Hub 'ı kullanmanızı öneririz.
2. İçe aktarılmış verilerin hacmi genellikle zaman içinde artar ve daha büyük aktarım hızlarını işlemek ve kısıtlama senaryolarından ve veri gecikmesinden kaçınmak için Olay Hub 'ı ölçeğinin artması gerekir. İşleme birimlerinin sayısını otomatik olarak artırmak ve kullanım ihtiyaçlarını karşılamak için Event Hubs otomatik Şişir özelliğini kullanmanız gerekir. Ayrıntılar için bkz. [Azure Event Hubs üretilen iş birimlerini otomatik olarak ölçeklendirme](../../event-hubs/event-hubs-auto-inflate.md) .

## <a name="prerequisites"></a>Önkoşullar
Log Analytics veri dışarı aktarma yapılandırmadan önce tamamlanması gereken önkoşullar aşağıda verilmiştir.

- Depolama hesabının ve Olay Hub 'ının zaten oluşturulması ve Log Analytics çalışma alanıyla aynı bölgede olması gerekir. Verilerinizi diğer depolama hesaplarına çoğaltmanız gerekiyorsa [Azure depolama yedekliliği seçeneklerinin](../../storage/common/storage-redundancy.md)herhangi birini kullanabilirsiniz.  
- Depolama hesabı StorageV1 veya StorageV2 olmalıdır. Klasik depolama desteklenmiyor  
- Depolama hesabınızı seçili ağlardan erişime izin verecek şekilde yapılandırdıysanız, Azure Izleyici 'nin depolama alanına yazmasını sağlamak için depolama hesabı ayarlarınıza bir özel durum eklemeniz gerekir.

## <a name="enable-data-export"></a>Veri vermeyi etkinleştir
Log Analytics verilerinin dışarı aktarılmasını etkinleştirmek için aşağıdaki adımları uygulamanız gerekir. Hakkında daha fazla bilgi için aşağıdaki bölümlere bakın.

- Kaynak sağlayıcısını kaydedin.
- Güvenilen Microsoft hizmetlerine izin verin.
- Dışarı aktarılacak tabloları ve bunların hedefini tanımlayan bir veya daha fazla veri dışa aktarma kuralı oluşturun.

### <a name="register-resource-provider"></a>Kaynak sağlayıcısını kaydetme
Log Analytics verilerini dışarı aktarmaya olanak tanımak için aşağıdaki Azure Kaynak sağlayıcısının aboneliğinize kayıtlı olması gerekir. 

- Microsoft. Insights

Bu kaynak sağlayıcısı muhtemelen çoğu Azure Izleyici kullanıcısı için zaten kayıtlı olacak. Doğrulamak için Azure portal **abonelikler** ' e gidin. Aboneliğinizi seçin ve ardından menüdeki **Ayarlar** bölümünde **kaynak sağlayıcıları** ' na tıklayın. **Microsoft. Insights**'ı bulun. Durumu **kayıtlıysa**, zaten kayıtlı olur. Aksi takdirde, kaydetmek için **Kaydet** ' e tıklayın.

[Azure kaynak sağlayıcıları ve türleri](../../azure-resource-manager/management/resource-providers-and-types.md)bölümünde açıklandığı gibi, bir kaynak sağlayıcısını kaydetmek için de kullanılabilir yöntemlerden birini kullanabilirsiniz. Aşağıda PowerShell kullanan örnek bir komut verilmiştir:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Güvenilen Microsoft hizmetlerine izin ver
Depolama hesabınızı seçili ağlardan erişime izin verecek şekilde yapılandırdıysanız, Azure Izleyici 'nin hesaba yazmasına izin vermek için bir özel durum eklemeniz gerekir. Depolama Hesabınıza yönelik **güvenlik duvarları ve sanal ağlardan** , **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin ver**' i seçin.

[![Depolama hesabı güvenlik duvarları ve sanal ağlar](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>Veri dışarı aktarma kuralı oluştur veya güncelleştir
Veri dışa aktarma kuralı, verilerin dışarı aktarıldığı tabloları ve hedefi tanımlar. Şu anda her bir hedef için tek bir kural oluşturabilirsiniz.

Dışarı aktarma kuralı, çalışma alanınızdaki tabloları içermelidir. Çalışma alanınızdaki kullanılabilir tabloların bir listesi için bu sorguyu çalıştırın.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Yok

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLı kullanarak bir depolama hesabına veri dışarı aktarma kuralı oluşturmak için aşağıdaki komutu kullanın.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

CLı kullanarak bir olay hub 'ına veri dışarı aktarma kuralı oluşturmak için aşağıdaki komutu kullanın. Her tablo için ayrı bir olay hub 'ı oluşturulur.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

CLı kullanarak belirli bir olay hub 'ına veri dışarı aktarma kuralı oluşturmak için aşağıdaki komutu kullanın. Tüm tablolar, belirtilen olay hub 'ının adına verilir. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

REST API kullanarak bir veri dışarı aktarma kuralı oluşturmak için aşağıdaki isteği kullanın. İstek, taşıyıcı belirteç yetkilendirmesi ve içerik türü uygulama/JSON kullanmalıdır.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

İsteğin gövdesi, tabloların hedefini belirtir. Aşağıda, bir depolama hesabı için REST isteğine yönelik bir örnek gövde verilmiştir.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Aşağıda bir olay hub 'ı için REST isteğinin örnek bir gövdesi verilmiştir.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Aşağıda, Olay Hub 'ı adının sağlandığı bir olay hub 'ı için REST isteği için örnek bir gövde verilmiştir. Bu durumda, tüm aktarılmış veriler bu olay hub 'ına gönderilir.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[Şablon](#tab/json)

Şablon kullanarak bir depolama hesabına veri dışarı aktarma kuralı oluşturmak için aşağıdaki komutu kullanın.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Şablon kullanarak bir olay hub 'ına veri dışarı aktarma kuralı oluşturmak için aşağıdaki komutu kullanın. Her tablo için ayrı bir olay hub 'ı oluşturulur.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Şablonu kullanarak belirli bir olay hub 'ına veri dışarı aktarma kuralı oluşturmak için aşağıdaki komutu kullanın. Tüm tablolar, belirtilen olay hub 'ının adına verilir.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>Veri dışa aktarma kuralı yapılandırmasını görüntüle

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Yok

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLı kullanarak bir veri dışa aktarma kuralının yapılandırmasını görüntülemek için aşağıdaki komutu kullanın.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

REST API kullanarak bir veri dışarı aktarma kuralının yapılandırmasını görüntülemek için aşağıdaki isteği kullanın. İstek, taşıyıcı belirteç yetkilendirmesi kullanmalıdır.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Şablon](#tab/json)

Yok

---

## <a name="disable-an-export-rule"></a>Dışarı aktarma kuralını devre dışı bırak

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Yok

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Sınama gerçekleştirilirken belirli bir süre için verileri tutmanız gerekmiyorsa dışarı aktarma kuralları devre dışı bırakılabilir. CLı kullanarak bir veri dışarı aktarma kuralını devre dışı bırakmak için aşağıdaki komutu kullanın.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Sınama gerçekleştirilirken belirli bir süre için verileri tutmanız gerekmiyorsa dışarı aktarma kuralları devre dışı bırakılabilir. REST API kullanarak bir veri dışarı aktarma kuralını devre dışı bırakmak için aşağıdaki isteği kullanın. İstek, taşıyıcı belirteç yetkilendirmesi kullanmalıdır.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[Şablon](#tab/json)

Sınama gerçekleştirilirken belirli bir süre için verileri tutmanız gerekmiyorsa dışarı aktarma kuralları devre dışı bırakılabilir. ```"enable": false```Verilerin dışarı aktarılmasını devre dışı bırakmak için şablonda ayarlanır.

---

## <a name="delete-an-export-rule"></a>Dışarı aktarma kuralını silme

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Yok

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLı kullanarak bir veri dışarı aktarma kuralını silmek için aşağıdaki komutu kullanın.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

REST API kullanarak bir veri dışarı aktarma kuralını silmek için aşağıdaki isteği kullanın. İstek, taşıyıcı belirteç yetkilendirmesi kullanmalıdır.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Şablon](#tab/json)

Yok

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Çalışma alanındaki tüm veri dışarı aktarma kurallarını görüntüleme

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Yok

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLı kullanarak bir çalışma alanındaki tüm veri dışarı aktarma kurallarını görüntülemek için aşağıdaki komutu kullanın.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

REST API kullanarak bir çalışma alanındaki tüm veri dışarı aktarma kurallarını görüntülemek için aşağıdaki isteği kullanın. İstek, taşıyıcı belirteç yetkilendirmesi kullanmalıdır.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[Şablon](#tab/json)

Yok

---

## <a name="unsupported-tables"></a>Desteklenmeyen tablolar
Veri dışa aktarma kuralı desteklenmeyen bir tablo içeriyorsa, yapılandırma başarılı olur, ancak bu tablo için hiçbir veri aktarılmaz. Tablo daha sonra destekleniyorsa, verileri o anda dışarıya kaydedilir.

Veri dışa aktarma kuralı mevcut olmayan bir tablo içeriyorsa, "tablo <tableName> çalışma alanında yok" hatasıyla başarısız olur.


## <a name="supported-tables"></a>Desteklenen tablolar
Desteklenen tablolar şu anda aşağıda belirtilenler ile sınırlıdır. Kısıtlama belirtilmediği takdirde tablodaki tüm veriler verilecek. Bu liste, ek tablolara yönelik destek eklendikçe güncelleştirilecektir.


| Tablo | Sınırlamalar |
|:---|:---|
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| Aadmanagedıdentitysignınlogs |  |
| Aadnonınteractiveusersignınlogs |  |
| AADProvisioningLogs |  |
| Aadserviceprincipalsignınlogs |  |
| ABSBotRequests |  |
| ACSBillingUsage |  |
| ACSSMSIncomingOperations |  |
| Adassessmentönerisi |  |
| ADFActivityRun |  |
| Adfardışık düzen eylemsizlik |  |
| ADFTriggerRun |  |
| ADReplicationResult |  |
| Adsecurityassessmentönerisi |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| Ayumurdeliveryfailurelogs |  |
| Ayumurpublishfailurelogs |  |
| Uyarı |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| Appcenterhatası |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| Oto Scalescaleactionslog |  |
| Awscses Izi |  |
| Azureassessmentönerisi |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | Kısmi destek – bazı veriler dışarı aktarma için desteklenmeyen iç hizmetlerden alınır. Bu bölüm şu anda dışa aktarma içinde yok. |
| Containerımageınventory |  |
| Containerınventory |  |
| ContainerLog |  |
| Containernodeınventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| Databricksınstancepools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| Databricksgizlilikler |  |
| Databricksstopermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DnsEvents |  |
| DnsInventory |  |
| Dynamics365Activity |  |
| Olay | Kısmi destek – bu tablodaki bazı veriler depolama hesabı üzerinden alınır. Bu bölüm şu anda dışa aktarma içinde yok. |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| Sinyal |  |
| HuntingBookmark |  |
| Insightsölçümlerini | Kısmi destek – bazı veriler dışarı aktarma için desteklenmeyen iç hizmetlerden alınır. Bu bölüm şu anda dışa aktarma içinde yok. |
| Intuneauditlogs |  |
| Intunedevices |  |
| Intuneoperationallogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| Kubenodeınventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs 'lar |  |
| McasShadowItReporting |  |
| MicrosoftAzureBastionAuditLogs |  |
| Microsoftdatasharerecei, Snapshotlog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| Microsofthealthumererererce |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| Officeetkinliği | Kısmi destek: O365 'den LA 'ya Web kancaları aracılığıyla alınacak verilerden bazıları. Bu bölüm şu anda dışa aktarma içinde yok. |
| İşlem | Kısmi destek – bazı veriler dışarı aktarma için desteklenmeyen iç hizmetlerden alınır. Bu bölüm şu anda dışa aktarma içinde yok. |
| Perf | Kısmi destek: Şu anda yalnızca Windows Perf verileri desteklenir. Linux performans verileri dışarı aktarma sırasında yok. |
| PowerBIDatasetsTenant |  |
| Powerbıdatasetsworkspace |  |
| Powerbıdatasetsworkspace önizlemesi |  |
| Sccmassessmentönerisi |  |
| Scomassessmentönerisi |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| SecurityEvent | Kısmi destek – bu tablodaki bazı veriler depolama hesabı üzerinden alınır. Bu bölüm şu anda dışa aktarma içinde yok. |
| Securityıncident |  |
| Securityıotrawevent |  |
| Securitynestedönerisi |  |
| Securityönerisi |  |
| Sfbassessmentönerisi |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| Signalrservicediagnoyapışlogs |  |
| Signınlogs |  |
| Spassessmentönerisi |  |
| Sqlassessmentönerisi |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| Syslog | Kısmi destek – bu tablodaki bazı veriler depolama hesabı üzerinden alınır. Bu bölüm şu anda dışa aktarma içinde yok. |
| Threatıntelligenceındicator |  |
| Güncelleştir | Kısmi destek – bazı veriler dışarı aktarma için desteklenmeyen iç hizmetlerden alınır. Bu bölüm şu anda dışa aktarma içinde yok. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| Kullanım |  |
| Listem |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| Iletilen veri | Kısmi destek – bazı veriler dışarı aktarma için desteklenmeyen iç hizmetlerden alınır. Bu bölüm şu anda dışa aktarma içinde yok. |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Veri Gezgini 'den aktarılmış verileri sorgulayın](../logs/azure-data-explorer-query-storage.md).

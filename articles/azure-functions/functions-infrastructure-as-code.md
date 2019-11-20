---
title: Azure Işlevlerinde bir işlev uygulaması için kaynak dağıtımını otomatikleştirme | Microsoft Docs
description: İşlev uygulamanızı dağıtan bir Azure Resource Manager şablonu oluşturmayı öğrenin.
author: ggailey777
manager: gwallace
keywords: Azure işlevleri, işlevler, sunucusuz mimari, kod olarak altyapı, Azure Resource Manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 8435aab65d26627de26fb8b5ad0510fcd7c57c33
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575947"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Işlevlerinde işlev uygulamanız için kaynak dağıtımını otomatikleştirme

Bir işlev uygulamasını dağıtmak için bir Azure Resource Manager şablonu kullanabilirsiniz. Bu makalede, bu işlemi gerçekleştirmek için gerekli kaynaklar ve parametreler özetlenmektedir. İşlev uygulamanızdaki [tetikleyicilere ve bağlamalara](functions-triggers-bindings.md) bağlı olarak ek kaynaklar dağıtmanız gerekebilir.

Şablon oluşturma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma](../azure-resource-manager/resource-group-authoring-templates.md).

Örnek şablonlar için bkz.:
- [Tüketim planında işlev uygulaması]
- [Azure App Service planındaki işlev uygulaması]

## <a name="required-resources"></a>Gerekli kaynaklar

Azure Işlevleri dağıtımı genellikle şu kaynaklardan oluşur:

| Kaynak                                                                           | Gereksinim | Sözdizimi ve Özellikler başvurusu                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Bir işlev uygulaması                                                                     | Gerekli    | [Microsoft. Web/siteler](/azure/templates/microsoft.web/sites)                             |   |
| Bir [Azure depolama](../storage/index.yml) hesabı                                   | Gerekli    | [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) bileşeni | İsteğe bağlı    | [Microsoft. Insights/bileşenler](/azure/templates/microsoft.insights/components)         |   |
| [Barındırma planı](./functions-scale.md)                                             | İsteğe bağlı<sup>1</sup>    | [Microsoft. Web/sunucugrupları](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup> Barındırma planı yalnızca bir [Premium planda](./functions-premium-plan.md) (önizlemede) veya [App Service bir planda](../app-service/overview-hosting-plans.md)işlev uygulamanızı çalıştırmayı seçtiğinizde gereklidir.

> [!TIP]
> Gerekli olmasa da, uygulamanız için Application Insights yapılandırmanız önemle tavsiye edilir.

<a name="storage"></a>
### <a name="storage-account"></a>Depolama hesabı

İşlev uygulaması için bir Azure depolama hesabı gereklidir. Blob 'ları, tabloları, kuyrukları ve dosyaları destekleyen genel amaçlı bir hesabınız olması gerekir. Daha fazla bilgi için bkz. [Azure işlevleri depolama hesabı gereksinimleri](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Ayrıca, özellik `AzureWebJobsStorage`, site yapılandırmasındaki bir uygulama ayarı olarak belirtilmelidir. İşlev uygulaması izleme için Application Insights kullanmıyorsa, uygulama ayarı olarak `AzureWebJobsDashboard` de belirtmelidir.

Azure Işlevleri çalışma zamanı, iç kuyruklar oluşturmak için `AzureWebJobsStorage` bağlantı dizesini kullanır.  Application Insights etkin olmadığında, çalışma zamanı, Azure Tablo Depolaması 'nda oturum açmak ve portalda **İzle** sekmesini açmak için `AzureWebJobsDashboard` bağlantı dizesini kullanır.

Bu özellikler, `siteConfig` nesnesindeki `appSettings` koleksiyonunda belirtilir:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

İşlev uygulamalarınızı izlemek için Application Insights önerilir. Application Insights kaynak, **Microsoft. Insights/Components** ve Kind **Web**türü ile tanımlanmıştır:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

Ayrıca, izleme anahtarının `APPINSIGHTS_INSTRUMENTATIONKEY` uygulama ayarı kullanılarak işlev uygulamasına sağlanması gerekir. Bu özellik, `siteConfig` nesnesindeki `appSettings` koleksiyonunda belirtilir:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Barındırma planı

Barındırma planının tanımı değişir ve aşağıdakilerden biri olabilir:
* [Tüketim planı](#consumption) (varsayılan)
* [Premium plan](#premium) (önizlemede)
* [App Service planı](#app-service-plan)

### <a name="function-app"></a>İşlev uygulaması

İşlev uygulaması kaynağı, **Microsoft. Web/Sites** ve Kind **functionapp**türünde bir kaynak kullanılarak tanımlanır:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
```

> [!IMPORTANT]
> Bir barındırma planını açıkça tanımlıyorsanız, Bağımlıdson dizisinde ek bir öğe olması gerekir: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Bir işlev uygulaması şu uygulama ayarlarını içermelidir:

| Ayar adı                 | Açıklama                                                                               | Örnek değerler                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | İç sıraya alma için Işlev çalışma zamanının bulunduğu depolama hesabına yönelik bağlantı dizesi | [Depolama hesabını](#storage) gör       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Işlevleri çalışma zamanının sürümü                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Bu uygulamadaki işlevler için kullanılacak dil yığını                                   | `dotnet`, `node`, `java`veya `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Yalnızca `node` dil yığını kullanılıyorsa gereklidir, kullanılacak sürümü belirtir              | `10.14.1`                             |

Bu özellikler `siteConfig` özelliğindeki `appSettings` koleksiyonunda belirtilir:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Tüketim planına dağıt

Tüketim planı, kodunuz çalışırken otomatik olarak işlem gücü ayırır, yükü işlemek için gereken şekilde ölçeklenmez ve kod çalışmadığı zaman ölçeği ölçeklendirir. Boştaki VM 'Ler için ödeme yapmanız gerekmez ve kapasiteyi önceden ayırmanız gerekmez. Daha fazla bilgi için bkz. [Azure işlevleri ölçeklendirme ve barındırma](functions-scale.md#consumption-plan).

Örnek bir Azure Resource Manager şablonu için bkz. [Tüketim planında işlev uygulaması].

### <a name="create-a-consumption-plan"></a>Tüketim planı oluşturma

Tüketim planının tanımlanması gerekmez. Uygulama kaynağının kendisini oluşturduğunuzda bölge başına otomatik olarak bir tane oluşturulur veya seçilir.

Tüketim planı, "ServerFarm" kaynağının özel bir türüdür. Windows için, `computeMode` ve `sku` özellikleri için `Dynamic` değerini kullanarak bunu belirtebilirsiniz:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Tüketim planı Linux için açıkça tanımlanamaz. Otomatik olarak oluşturulur.

Tüketim planınızı açıkça tanımlarsanız, uygulamanın `serverFarmId` özelliğini, planın kaynak KIMLIĞINE işaret edecek şekilde ayarlamanız gerekecektir. İşlev uygulamasının plan için `dependsOn` bir ayarı olduğundan emin olmanız gerekir.

### <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

#### <a name="windows"></a>Windows

Windows 'da, bir tüketim planı site yapılandırmasında iki ek ayar gerektirir: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` ve `WEBSITE_CONTENTSHARE`. Bu özellikler, uygulama kodu ve yapılandırmasının depolandığı depolama hesabını ve dosya yolunu yapılandırır.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

Linux 'ta, işlev uygulamasının `kind` `functionapp,linux`olarak ayarlanması ve `reserved` özelliğinin `true`olarak ayarlanmış olması gerekir:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Premium planda dağıt

Premium planı, tüketim planıyla aynı ölçeklendirmeyi sunar, ancak adanmış kaynakları ve ek özellikleri içerir. Daha fazla bilgi için bkz. [Azure Işlevleri Premium planı](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Premium planı oluşturma

Premium plan, "ServerFarm" kaynağının özel bir türüdür. `sku` özelliği değeri için `EP1`, `EP2`veya `EP3` kullanarak belirtebilirsiniz.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

Premium plandaki bir işlev uygulaması, daha önce oluşturulan planın kaynak KIMLIĞI olarak `serverFarmId` özelliği ayarlanmalıdır. Ayrıca, Premium planı site yapılandırmasında iki ek ayar gerektirir: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` ve `WEBSITE_CONTENTSHARE`. Bu özellikler, uygulama kodu ve yapılandırmasının depolandığı depolama hesabını ve dosya yolunu yapılandırır.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a> 

## <a name="deploy-on-app-service-plan"></a>App Service planda dağıt

App Service planında, işlev uygulamanız, Web uygulamalarına benzer şekilde temel, standart ve Premium SKU 'Larda adanmış VM 'lerde çalışır. App Service planının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [ayrıntılı genel bakış Azure App Service planları](../app-service/overview-hosting-plans.md).

Örnek bir Azure Resource Manager şablonu için, bkz. [Azure App Service planındaki işlev uygulaması].

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

Bir App Service planı bir "ServerFarm" kaynağı tarafından tanımlanır.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Uygulamanızı Linux üzerinde çalıştırmak için `kind` da `Linux`ayarlamanız gerekir:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>İşlev uygulaması oluşturma 

App Service planındaki bir işlev uygulaması, daha önce oluşturulan planın kaynak KIMLIĞI için `serverFarmId` özelliğinin ayarlanmış olması gerekir.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Linux uygulamaları, `siteConfig`altına bir `linuxFxVersion` özelliği de içermelidir. Yalnızca kod dağıtıyorsanız, bunun değeri istenen çalışma zamanı yığınınıza göre belirlenir:

| yığın            | Örnek değer                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

[Özel bir kapsayıcı görüntüsü dağıtıyorsanız](./functions-create-function-linux-custom-image.md), `linuxFxVersion` ile belirtmeniz ve resminizin çekilme için [kapsayıcılar için Web App](/azure/app-service/containers)gibi yapılandırmayı dahil etmeniz gerekir. Ayrıca, uygulama içeriğiniz kapsayıcının kendisinde sağlandığı için `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false`olarak ayarlayın:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Bir dağıtımı özelleştirme

Bir işlev uygulamasının, dağıtımınızda kullanabileceğiniz uygulama ayarları ve kaynak denetimi seçenekleri dahil birçok alt kaynağı vardır. Ayrıca **sourcecontrols** alt kaynağını kaldırmayı ve bunun yerine farklı bir [dağıtım seçeneğini](functions-continuous-deployment.md) kullanmanızı da tercih edebilirsiniz.

> [!IMPORTANT]
> Azure Resource Manager kullanarak uygulamanızı başarıyla dağıtmak için, kaynakların Azure 'da nasıl dağıtıldığını anlamak önemlidir. Aşağıdaki örnekte, üst düzey konfigürasyonlar **SiteConfig**kullanılarak uygulanır. Işlevlerin çalışma zamanına ve dağıtım altyapısına bilgi ilettikleri için, bu yapılandırmaların üst düzeyde ayarlanması önemlidir. Alt **sourcecontrols/Web** kaynağı uygulanmadan önce en üst düzey bilgiler gereklidir. Bu ayarları alt düzey **config/appSettings** kaynağında yapılandırmak mümkün olsa da, bazı durumlarda işlev uygulamanızın **config/appSettings** uygulanmadan *önce* dağıtılması gerekir. Örneğin, [Logic Apps](../logic-apps/index.yml)işlevleri kullanırken, işlevleriniz başka bir kaynağın bağımlılığı olan bir bağımlılıktır.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Bu şablon, Işlev dağıtım altyapısının (kudu) dağıtılabilir kod aradığı temel dizini ayarlayan [Proje](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) uygulama ayarları değerini kullanır. Depomızda, işlevlerimiz **src** klasörünün bir alt klasörüdür. Bu nedenle, önceki örnekte, uygulama ayarları değerini `src`olarak ayarlarız. İşlevleriniz deponuzın kökünde ise veya kaynak denetiminden dağıtdıysanız, bu uygulama ayarları değerini kaldırabilirsiniz.

## <a name="deploy-your-template"></a>Şablonunuzu dağıtma

Şablonunuzu dağıtmak için aşağıdaki yolların herhangi birini kullanabilirsiniz:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Azure 'a dağıt düğmesi

```<url-encoded-path-to-azuredeploy-json>```, GitHub 'daki `azuredeploy.json` dosyanızın ham yolunun [URL kodlu](https://www.bing.com/search?q=url+encode) bir sürümü ile değiştirin.

Markaşağı kullanan bir örnek aşağıda verilmiştir:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

HTML kullanan bir örnek aşağıda verilmiştir:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>PowerShell kullanarak dağıtma

Aşağıdaki PowerShell komutları, bir kaynak grubu oluşturur ve gerekli kaynaklarıyla birlikte bir işlev uygulaması oluşturan bir şablon dağıtır. Yerel olarak çalıştırmak için [Azure PowerShell](/powershell/azure/install-az-ps) yüklü olmalıdır. Oturum açmak için [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) çalıştırın.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Bu dağıtımı test etmek için, bir tüketim planında Windows üzerinde bir işlev uygulaması oluşturan [Bu gibi bir şablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) kullanabilirsiniz. `<function-app-name>`, işlev uygulamanız için benzersiz bir adla değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Işlevleri geliştirme ve yapılandırma hakkında daha fazla bilgi edinin.

* [Azure İşlevleri geliştirici başvurusu](functions-reference.md)
* [Azure işlevi uygulama ayarlarını yapılandırma](functions-how-to-use-azure-function-app-settings.md)
* [İlk Azure işlevinizi oluşturma](functions-create-first-azure-function.md)

<!-- LINKS -->

[Tüketim planında işlev uygulaması]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Azure App Service planındaki işlev uygulaması]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json

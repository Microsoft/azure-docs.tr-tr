---
title: Azure 'da işlev uygulama kaynağı dağıtımını otomatikleştirme
description: İşlev uygulamanızı dağıtan bir Azure Resource Manager şablonu oluşturmayı öğrenin.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 4bbd3491c45b15d43ae0e94b37b916cd8091e655
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834219"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Işlevlerinde işlev uygulamanız için kaynak dağıtımını otomatikleştirme

Bir işlev uygulamasını dağıtmak için bir Azure Resource Manager şablonu kullanabilirsiniz. Bu makalede, bu işlemi gerçekleştirmek için gerekli kaynaklar ve parametreler özetlenmektedir. İşlev uygulamanızdaki [tetikleyicilere ve bağlamalara](functions-triggers-bindings.md) bağlı olarak ek kaynaklar dağıtmanız gerekebilir.

Şablon oluşturma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma](../azure-resource-manager/templates/template-syntax.md).

Örnek şablonlar için bkz.:
- [Tüketim planında işlev uygulaması]
- [Azure App Service planındaki işlev uygulaması]

## <a name="required-resources"></a>Gerekli kaynaklar

Azure Işlevleri dağıtımı genellikle şu kaynaklardan oluşur:

| Kaynak                                                                           | Gereksinim | Sözdizimi ve Özellikler başvurusu                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Bir işlev uygulaması                                                                     | Gerekli    | [Microsoft. Web/siteler](/azure/templates/microsoft.web/sites)                             |
| Bir [Azure depolama](../storage/index.yml) hesabı                                   | Gerekli    | [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) bileşeni | İsteğe Bağlı    | [Microsoft. Insights/bileşenler](/azure/templates/microsoft.insights/components)         |
| [Barındırma planı](./functions-scale.md)                                             | İsteğe bağlı<sup>1</sup>    | [Microsoft. Web/sunucugrupları](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Barındırma planı yalnızca bir [Premium planda](./functions-premium-plan.md) veya [App Service planında](../app-service/overview-hosting-plans.md)işlev uygulamanızı çalıştırmayı seçtiğinizde gereklidir.

> [!TIP]
> Gerekli olmasa da, uygulamanız için Application Insights yapılandırmanız önemle tavsiye edilir.

<a name="storage"></a>
### <a name="storage-account"></a>Depolama hesabı

İşlev uygulaması için bir Azure depolama hesabı gereklidir. Blob 'ları, tabloları, kuyrukları ve dosyaları destekleyen genel amaçlı bir hesabınız olması gerekir. Daha fazla bilgi için bkz. [Azure işlevleri depolama hesabı gereksinimleri](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Ayrıca, özelliği `AzureWebJobsStorage` site yapılandırmasında bir uygulama ayarı olarak belirtilmelidir. İşlev uygulaması izleme için Application Insights kullanmıyorsa, `AzureWebJobsDashboard` bir uygulama ayarı olarak da belirtilmelidir.

Azure Işlevleri çalışma zamanı, `AzureWebJobsStorage` iç sıralar oluşturmak için bağlantı dizesini kullanır.  Application Insights etkin olmadığında, çalışma zamanı `AzureWebJobsDashboard` Azure Tablo depolama alanına oturum açmak ve portalda **izleyici** sekmesini desteklemek için bağlantı dizesini kullanır.

Bu özellikler `appSettings` nesnedeki koleksiyonda belirtilir `siteConfig` :

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

İşlev uygulamalarınızı izlemek için Application Insights önerilir. Application Insights kaynak, **Microsoft. Insights/Components** ve Kind **Web** türü ile tanımlanmıştır:

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
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Ayrıca, izleme anahtarının uygulama ayarı kullanılarak işlev uygulamasına sağlanması gerekir `APPINSIGHTS_INSTRUMENTATIONKEY` . Bu özellik `appSettings` nesnedeki koleksiyonda belirtilir `siteConfig` :

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
* [Premium planı](#premium)
* [App Service planı](#app-service-plan)

### <a name="function-app"></a>İşlev uygulaması

İşlev uygulaması kaynağı, **Microsoft. Web/Sites** ve Kind **functionapp** türünde bir kaynak kullanılarak tanımlanır:

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
}
```

> [!IMPORTANT]
> Bir barındırma planını açıkça tanımlıyorsanız, Bağımlıdson dizisinde ek bir öğe olması gerekir: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Bir işlev uygulaması şu uygulama ayarlarını içermelidir:

| Ayar adı                 | Description                                                                               | Örnek değerler                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Işlevlerin çalışma zamanının iç sıraya alma için kullandığı bir depolama hesabına yönelik bağlantı dizesi | [Depolama hesabını](#storage) gör       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Işlevleri çalışma zamanının sürümü                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Bu uygulamadaki işlevler için kullanılacak dil yığını                                   | `dotnet`,,, `node` `java` `python` veya `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Yalnızca dil yığını kullanılıyorsa gereklidir `node` , kullanılacak sürümü belirtir              | `10.14.1`                             |

Bu özellikler, `appSettings` özelliğindeki koleksiyonda belirtilir `siteConfig` :

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Tüketim planına dağıt

Tüketim planı, kodunuz çalışırken otomatik olarak işlem gücü ayırır, yükü işlemek için gereken şekilde ölçeklenmez ve kod çalışmadığı zaman içinde ölçeklendirilir. Boştaki VM 'Ler için ödeme yapmanız gerekmez ve kapasiteyi önceden ayırmanız gerekmez. Daha fazla bilgi için bkz. [Azure işlevleri ölçeklendirme ve barındırma](consumption-plan.md).

Örnek bir Azure Resource Manager şablonu için bkz. [Tüketim planındaki işlev uygulaması].

### <a name="create-a-consumption-plan"></a>Tüketim planı oluşturma

Tüketim planının tanımlanması gerekmez. Uygulama kaynağının kendisini oluşturduğunuzda bölge başına otomatik olarak bir tane oluşturulur veya seçilir.

Tüketim planı, "ServerFarm" kaynağının özel bir türüdür. Windows için, `Dynamic` ve özellikleri için değerini kullanarak bunu belirtebilirsiniz `computeMode` `sku` :

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

Tüketim planınızı açık bir şekilde tanımlarsanız, `serverFarmId` uygulamayı planın kaynak kimliğine işaret edecek şekilde ayarlamanız gerekecektir. İşlev uygulamasının plan için bir ayara sahip olduğundan emin olmanız gerekir `dependsOn` .

### <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

Tüketim planında çalışan bir işlev uygulamasının gerektirdiği ayarlar Windows ve Linux arasında erteleyin. 

#### <a name="windows"></a>Windows

Windows 'da, bir tüketim planı site yapılandırmasında ek bir ayar gerektirir: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Bu özellik, uygulama kodu ve yapılandırmasının depolandığı depolama hesabını yapılandırır.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare)Site ilk oluşturulduğunda, ayarı sizin için oluşturulmuşa ayarlayın.  

#### <a name="linux"></a>Linux

Linux 'ta, işlev uygulamasının `kind` için ayarlanmış olması `functionapp,linux` ve `reserved` özelliği olarak ayarlanmış olması gerekir `true` . 

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

[`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring)Ve [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) ayarları Linux üzerinde desteklenmez.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Premium planda dağıt

Premium planı, tüketim planıyla aynı ölçeklendirmeyi sunar, ancak adanmış kaynakları ve ek özellikleri içerir. Daha fazla bilgi için bkz. [Azure Işlevleri Premium planı](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Premium planı oluşturma

Premium plan, "ServerFarm" kaynağının özel bir türüdür. `EP1` `EP2` `EP3` `Name` `sku` [Açıklama nesnesindeki](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)Özellik değeri için, veya öğesini kullanarak belirtebilirsiniz.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

Premium plandaki bir işlev uygulamasının, `serverFarmId` daha önce oluşturulan planın kaynak kimliği olarak ayarlanmış özelliği olmalıdır. Ayrıca, Premium planı site yapılandırmasında ek bir ayar gerektirir: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Bu özellik, uygulama kodu ve yapılandırmasının depolandığı depolama hesabını yapılandırır.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare)Site ilk oluşturulduğunda, ayarı sizin için oluşturulmuşa ayarlayın.  

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

Uygulamanızı Linux üzerinde çalıştırmak için, öğesini de olarak ayarlamanız gerekir `kind` `Linux` :

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

App Service planındaki bir işlev uygulamasının, `serverFarmId` daha önce oluşturulan planın kaynak kimliği olarak ayarlanmış özelliği olmalıdır.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```

Linux uygulamaları altında bir özelliği de içermelidir `linuxFxVersion` `siteConfig` . Yalnızca kod dağıtıyorsanız, bunun değeri istenen çalışma zamanı yığını tarafından şu biçimde belirlenir ```runtime|runtimeVersion``` :

| Yığın            | Örnek değer                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

[Özel bir kapsayıcı görüntüsü dağıtıyorsanız](./functions-create-function-linux-custom-image.md), bunu ile belirtmeniz `linuxFxVersion` ve resminizin çekilme olanağı sağlayan yapılandırmayı [kapsayıcılar için Web App](../app-service/index.yml)gibi bir yapılandırma dahil etmeniz gerekir. Ayrıca, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false` uygulama içeriğiniz kapsayıcının kendisinde sağlandığı için olarak olarak ayarlayın:

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
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
> Azure Resource Manager kullanarak uygulamanızı başarıyla dağıtmak için, kaynakların Azure 'da nasıl dağıtıldığını anlamak önemlidir. Aşağıdaki örnekte, üst düzey konfigürasyonlar **SiteConfig** kullanılarak uygulanır. Işlevlerin çalışma zamanına ve dağıtım altyapısına bilgi ilettikleri için, bu yapılandırmaların üst düzeyde ayarlanması önemlidir. Alt **sourcecontrols/Web** kaynağı uygulanmadan önce en üst düzey bilgiler gereklidir. Bu ayarları alt düzey **config/appSettings** kaynağında yapılandırmak mümkün olsa da, bazı durumlarda işlev uygulamanızın **config/appSettings** uygulanmadan *önce* dağıtılması gerekir. Örneğin, [Logic Apps](../logic-apps/index.yml)işlevleri kullanırken, işlevleriniz başka bir kaynağın bağımlılığı olan bir bağımlılıktır.

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
                "value": "~3"
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
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
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
> Bu şablon, Işlev dağıtım altyapısının (kudu) dağıtılabilir kod aradığı temel dizini ayarlayan [Proje](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) uygulama ayarları değerini kullanır. Depomızda, işlevlerimiz **src** klasörünün bir alt klasörüdür. Bu nedenle, önceki örnekte, uygulama ayarları değerini olarak ayarlarız `src` . İşlevleriniz deponuzın kökünde ise veya kaynak denetiminden dağıtdıysanız, bu uygulama ayarları değerini kaldırabilirsiniz.

## <a name="deploy-your-template"></a>Şablonunuzu dağıtma

Şablonunuzu dağıtmak için aşağıdaki yolların herhangi birini kullanabilirsiniz:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure portalı](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Azure'a Dağıt düğmesi

```<url-encoded-path-to-azuredeploy-json>```GitHub 'daki dosyanızın ham yolunun [URL kodlu](https://www.bing.com/search?q=url+encode) bir sürümüyle değiştirin `azuredeploy.json` .

Markaşağı kullanan bir örnek aşağıda verilmiştir:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

HTML kullanan bir örnek aşağıda verilmiştir:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>PowerShell kullanarak dağıtma

Aşağıdaki PowerShell komutları, bir kaynak grubu oluşturur ve gerekli kaynaklarıyla birlikte bir işlev uygulaması oluşturan bir şablon dağıtır. Yerel olarak çalıştırmak için [Azure PowerShell](/powershell/azure/install-az-ps) yüklü olmalıdır. [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount)Oturum açmak için çalıştırın.

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

Bu dağıtımı test etmek için, bir tüketim planında Windows üzerinde bir işlev uygulaması oluşturan [Bu gibi bir şablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) kullanabilirsiniz. `<function-app-name>`İşlev uygulamanız için benzersiz bir adla değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Işlevleri geliştirme ve yapılandırma hakkında daha fazla bilgi edinin.

* [Azure İşlevleri geliştirici başvurusu](functions-reference.md)
* [Azure işlevi uygulama ayarlarını yapılandırma](functions-how-to-use-azure-function-app-settings.md)
* [İlk Azure işlevinizi oluşturma](./functions-get-started.md)

<!-- LINKS -->

[Tüketim planında işlev uygulaması]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Azure App Service planındaki işlev uygulaması]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json

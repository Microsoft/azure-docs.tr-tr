---
title: Azure App Service + Application Insights kaynakları için şablon örnekleri Kaynak Yöneticisi
description: Bir Azure App Service Application Insights kaynağı ile dağıtmak için örnek Azure Resource Manager şablonları.
ms.topic: sample
ms.custom: devx-track-dotnet
author: lgayhardt
ms.author: lagayhar
ms.date: 08/06/2020
ms.openlocfilehash: 4e287d600e596539aec2763157036d2bb71b141c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383330"
---
# <a name="resource-manager-template-samples-for-creating-azure-app-services-web-apps-with-application-insights-monitoring"></a>Application Insights izleme ile Azure Uygulama Hizmetleri Web uygulamaları oluşturmak için şablon örnekleri Kaynak Yöneticisi

Bu makale, [klasik Application Insights kaynaklarını](../app/create-new-resource.md) bir Azure Uygulama Hizmetleri Web uygulamasıyla birlikte dağıtmak ve yapılandırmak için örnek [Azure Resource Manager şablonlar](../../azure-resource-manager/templates/template-syntax.md) içerir. Her örnek, şablona sağlanacak örnek değerleri içeren bir şablon dosyası ve bir parametre dosyası içerir.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="net-core-runtime"></a>.NET Core çalışma zamanı

Aşağıdaki örnek, .NET Core çalışma zamanı ve izleme etkin bir [klasik Application Insights kaynağı](../app/create-new-resource.md) ile temel bir Azure App Service Web uygulaması oluşturur. 

### <a name="template-file"></a>Şablon dosyası

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "currentStack": {
            "type": "string"
        },
        "phpVersion": {
            "type": "string"
        },
        "errorLink": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        },
                        {
                            "name": "ANCM_ADDITIONAL_ERROR_PAGE_LINK",
                            "value": "[parameters('errorLink')]"
                        }
                    ],
                    "metadata": [
                        {
                            "name": "CURRENT_STACK",
                            "value": "[parameters('currentStack')]"
                        }
                    ],
                    "phpVersion": "[parameters('phpVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parametre dosyası

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "WebApplication2720191003010920Plan"
    },
    "serverFarmResourceGroup": {
      "value": "Testwebapp01"
    },
    "alwaysOn": {
      "value": true
    },
    "currentStack": {
      "value": "dotnetcore"
    },
    "phpVersion": {
      "value": "OFF"
    },
    "errorLink": {
      "value": "https://web-app-name-01.scm.azurewebsites.net/detectors?type=tools&name=eventviewer"
    }
  }
}

```

## <a name="aspnet-runtime"></a>ASP.NET çalışma zamanı

Aşağıdaki örnek, ASP.NET çalışma zamanına sahip bir temel Azure App Service Web uygulaması ve izleme etkin bir [klasik Application Insights kaynağı](../app/create-new-resource.md) oluşturur. 

### <a name="template-file"></a>Şablon dosyası

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "currentStack": {
            "type": "string"
        },
        "phpVersion": {
            "type": "string"
        },
        "netFrameworkVersion": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        }
                    ],
                    "metadata": [
                        {
                            "name": "CURRENT_STACK",
                            "value": "[parameters('currentStack')]"
                        }
                    ],
                    "phpVersion": "[parameters('phpVersion')]",
                    "netFrameworkVersion": "[parameters('netFrameworkVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameters-file"></a>Parametre dosyası

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "WebApplication2720191003010920Plan"
    },
    "serverFarmResourceGroup": {
      "value": "Testwebapp01"
    },
    "alwaysOn": {
      "value": true
    },
    "currentStack": {
      "value": "dotnet"
    },
    "phpVersion": {
      "value": "OFF"
    },
    "netFrameworkVersion": {
      "value": "v4.0"
    }
  }
}
```

## <a name="nodejs-runtime-linux"></a>Node.js çalışma zamanı (Linux)

Aşağıdaki örnek, izleme etkinleştirilmiş node.js çalışma zamanına ve [klasik Application Insights kaynağına](../app/create-new-resource.md) sahip temel bir Azure App Service Linux Web uygulaması oluşturur. 

### <a name="template-file"></a>Şablon dosyası

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "linuxFxVersion": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        }
                    ],
                    "linuxFxVersion": "[parameters('linuxFxVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": false
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": null,
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parametre dosyası

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "App-planTest01-916e"
    },
    "serverFarmResourceGroup": {
      "value": "app_resource_group_custom"
    },
    "alwaysOn": {
      "value": true
    },
    "linuxFxVersion": {
      "value": "NODE|12-lts"
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure izleyici için diğer örnek şablonları alın](../resource-manager-samples.md).
* [Klasik Application Insights kaynakları hakkında daha fazla bilgi edinin](../app/create-new-resource.md).

---
title: Uygulama başına ölçeklendirmeyi kullanarak yüksek yoğunluklu barındırma-Azure App Service | Microsoft Docs
description: Azure App Service yüksek yoğunluklu barındırma
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 7130c9547e0778ce40a0ad1c1ea41607a02df23e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088095"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Uygulama başına ölçeklendirmeyi kullanarak Azure App Service yüksek yoğunluklu barındırma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

App Service kullanırken, üzerinde çalıştıkları [App Service planı](overview-hosting-plans.md) ölçeklendirerek uygulamalarınızı ölçeklendirebilirsiniz. Aynı App Service planında birden çok uygulama çalıştırıldığında, her ölçeklenen örnek plandaki tüm uygulamaları çalıştırır.

Uygulama *başına ölçeklendirme* , uygulamayı barındıran App Service planından bağımsız olarak bir uygulamanın ölçeklendirilmesine olanak tanımak için App Service plan düzeyinde etkinleştirilebilir. Bu şekilde, bir App Service planı 10 örneğe ölçeklendirilebilir, ancak bir uygulama yalnızca beş adet kullanılacak şekilde ayarlanabilir.

> [!NOTE]
> Uygulama başına ölçeklendirme yalnızca **Standart**, **Premium**, **Premium v2** ve **yalıtılmış** fiyatlandırma katmanları için kullanılabilir.
>

Uygulamalar, örnekler arasında eşit bir dağıtım için en iyi çaba yaklaşımını kullanarak kullanılabilir App Service planına ayrılır. Eşit bir dağıtım garanti edilmediği sürece, platform aynı uygulamanın iki örneğinin aynı App Service plan örneği üzerinde barındırılmaz olmasını sağlayacaktır.

Platform, çalışan ayırması için karar vermek üzere ölçümlere bağlı değildir. Uygulamalar, App Service planına yalnızca örnekler eklendiğinde veya kaldırıldığında yeniden dengelenebilir.

## <a name="per-app-scaling-using-powershell"></a>PowerShell kullanarak uygulama Ölçeklendirmesi başına

```-PerSiteScaling $true``` Parametresini cmdlet```New-AzAppServicePlan``` 'e geçirerek uygulama başına ölçeklendirmeyle bir plan oluşturun.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

`-PerSiteScaling $true` Parametreyi cmdlet```Set-AzAppServicePlan``` 'e geçirerek mevcut bir App Service planıyla uygulama başına ölçeklendirmeyi etkinleştirin.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Uygulama düzeyinde, uygulamanın App Service planında kullanabileceği örnek sayısını yapılandırın.

Aşağıdaki örnekte, temel alınan App Service planının kaç örnek tarafından ölçeklendirdiğine bakılmaksızın uygulama iki örnekle sınırlandırılmıştır.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers`, öğesinden `$newapp.MaxNumberOfWorkers`farklıdır. Uygulama başına ölçeklendirme, uygulamanın `$newapp.SiteConfig.NumberOfWorkers` ölçek özelliklerini belirlemede kullanır.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Azure Resource Manager kullanarak uygulama başına ölçeklendirme

Aşağıdaki Azure Resource Manager şablonu şunları oluşturur:

- 10 örneğe ölçeklenebilen bir App Service planı
- en fazla beş örnek için ölçeklendirilecek şekilde yapılandırılmış bir uygulama.

App Service planı, **Persiteölçeklendirme** özelliğini true `"perSiteScaling": true`olarak ayarlıyor. Uygulama, 5 `"properties": { "numberOfWorkers": "5" }`' e kadar kullanılacak **çalışan sayısını** ayarlıyor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Yüksek yoğunluklu barındırma için önerilen yapılandırma

Uygulama başına ölçeklendirme, hem genel Azure bölgelerinde hem de [App Service ortamlarda](environment/app-service-app-service-environment-intro.md)etkinleştirilen bir özelliktir. Ancak, önerilen strateji gelişmiş özelliklerden ve daha büyük App Service planı kapasitesinden yararlanmak için App Service ortamları kullanmaktır.  

Uygulamalarınız için yüksek yoğunluklu barındırma yapılandırmak için aşağıdaki adımları izleyin:

1. App Service planını yüksek yoğunluklu plan olarak belirleyin ve istediğiniz kapasiteye göre ölçeklendirin.
1. App Service planında `PerSiteScaling` bayrağı true olarak ayarlayın.
1. Yeni uygulamalar oluşturulur ve bu App Service, Numberofworker özelliği **1**olarak ayarlanan plana atanır.
   - Bu yapılandırmanın kullanılması mümkün olan en yüksek yoğunluğu verir.
1. Çalışan sayısı, gerektiğinde ek kaynaklar sağlamak için her uygulama için bağımsız olarak yapılandırılabilir. Örneğin:
   - Yüksek kullanım uygulaması, bu uygulama için daha fazla işlem kapasitesi sağlamak üzere numberofworker 'ları **3** olarak ayarlayabilir.
   - Az kullanılan uygulamalar, Numberofworker 'ları **1**olarak ayarlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Derinlemesine Azure App Service planlar genel bakış](overview-hosting-plans.md)
- [App Service Ortamı’na giriş](environment/app-service-app-service-environment-intro.md)

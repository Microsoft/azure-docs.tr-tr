---
title: Redsıs için Azure önbelleği ile Web uygulaması sağlama
description: Redsıs için Azure Cache ile Web uygulaması dağıtmak üzere Azure Resource Manager şablonu kullanın.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 089d7d7990f0f94135f629a5cd7a461700aa3433
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830781"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Şablon kullanarak Redsıs için bir Web uygulaması ve Azure önbelleği oluşturma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu konu başlığında, Reda için Azure Cache ile Azure Web uygulaması dağıtan bir Azure Resource Manager şablonu oluşturmayı öğreneceksiniz. Hangi kaynakların dağıtıldığını ve dağıtım yürütüldüğünde belirtilen parametrelerin nasıl tanımlanacağını nasıl tanımlayacağınızı öğreneceksiniz. Bu şablonu kendi dağıtımlarınız için kullanabilir veya kendi gereksinimlerinize göre özelleştirebilirsiniz.

Şablon oluşturma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma](../azure-resource-manager/templates/template-syntax.md). Önbellek kaynak türleri için JSON sözdizimi ve özellikleri hakkında bilgi edinmek için bkz. [Microsoft. Cache kaynak türleri](/azure/templates/microsoft.cache/allversions).

Tüm şablon için bkz. [redsıs şablonu Için Azure Cache Ile Web uygulaması](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Ne dağıtacaksınız
Bu şablonda şunları dağıtacaksınız:

* Azure Web App
* Redis için Azure Cache

Dağıtımı otomatik olarak çalıştırmak için aşağıdaki düğmeye tıklayın:

[![Azure’a dağıtın](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Belirtme parametreleri
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Adlar için değişkenler
Bu şablon, kaynakların adlarını oluşturmak için değişkenleri kullanır. Kaynak grubu kimliğini temel alan bir değer oluşturmak için [Uniquestring](../azure-resource-manager/templates/template-functions-string.md#uniquestring) işlevini kullanır.

```json
"variables": {
  "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
  "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
  "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
},
```


## <a name="resources-to-deploy"></a>Dağıtılacak kaynaklar
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Redis için Azure Cache
Web uygulamasıyla birlikte kullanılan redo için Azure önbelleğini oluşturur. Önbelleğin adı **CacheName** değişkeninde belirtilir.

Şablon, kaynak grubuyla aynı konumda bulunan önbelleği oluşturur.

```json
{
  "name": "[variables('cacheName')]",
  "type": "Microsoft.Cache/Redis",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-08-01",
  "dependsOn": [ ],
  "tags": {
    "displayName": "cache"
  },
  "properties": {
    "sku": {
      "name": "[parameters('cacheSKUName')]",
      "family": "[parameters('cacheSKUFamily')]",
      "capacity": "[parameters('cacheSKUCapacity')]"
    }
  }
}
```


### <a name="web-app"></a>Web uygulaması
**WebSiteName** değişkeninde belirtilen ada sahip Web uygulaması oluşturur.

Web uygulamasının Redsıs için Azure önbelleğiyle çalışmasını sağlayan uygulama ayarı özellikleriyle yapılandırıldığını unutmayın. Bu uygulama ayarları, dağıtım sırasında belirtilen değerlere göre dinamik olarak oluşturulur.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
    "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
      }
    }
  ]
}
```

## <a name="commands-to-run-deployment"></a>Dağıtımı çalıştırma komutları
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
```

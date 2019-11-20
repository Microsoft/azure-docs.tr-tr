---
title: Azure Resource Manager kullanarak Reda için Azure Önbelleği sağlama
description: Redsıs için bir Azure önbelleği dağıtmak üzere Azure Resource Manager şablonu kullanın.
services: app-service
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: f215cb79adc02da857f7aa93d8933bd12c06818a
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121788"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Şablon kullanarak Redsıs için Azure önbelleği oluşturma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu konu başlığında, Reda için Azure önbelleği dağıtan bir Azure Resource Manager şablonu oluşturmayı öğreneceksiniz. Önbellek, tanılama verilerini korumak için mevcut bir depolama hesabıyla birlikte kullanılabilir. Ayrıca, hangi kaynakların dağıtıldığını ve dağıtım yürütüldüğünde belirtilen parametrelerin nasıl tanımlanacağını nasıl tanımlayacağınızı öğreneceksiniz. Bu şablonu kendi dağıtımlarınız için kullanabilir veya kendi gereksinimlerinize göre özelleştirebilirsiniz.

Şu anda, Tanılama ayarları bir abonelik için aynı bölgedeki tüm önbellekler için paylaşılır. Bölgedeki bir önbelleğin güncelleştirilmesi bölgedeki diğer tüm önbellekleri etkiler.

Şablon oluşturma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma](../azure-resource-manager/resource-group-authoring-templates.md). Önbellek kaynak türleri için JSON sözdizimi ve özellikleri hakkında bilgi edinmek için bkz. [Microsoft. Cache kaynak türleri](/azure/templates/microsoft.cache/allversions).

Tüm şablon için bkz. [redsıs şablonu Için Azure önbelleği](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Yeni [Premium katmanının](cache-premium-tier-intro.md) Kaynak Yöneticisi şablonları kullanılabilir. 
> 
> * [Kümeleme ile Reda için Premium Azure önbelleği oluşturma](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Veri kalıcılığı olan Redsıs için Premium Azure önbelleği oluşturma](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Sanal bir ağa dağıtılan Premium Redis Cache oluşturma](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> En son şablonları denetlemek için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/documentation/templates/) ve `Azure Cache for Redis`arama.
> 
> 

## <a name="what-you-will-deploy"></a>Ne dağıtacaksınız
Bu şablonda, tanılama verileri için mevcut bir depolama hesabını kullanan redo için bir Azure önbelleği dağıtacaksınız.

Dağıtımı otomatik olarak çalıştırmak için aşağıdaki düğmeye tıklayın:

[![Azure’a dağıtma](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametreler
Azure Resource Manager sayesinde, şablon dağıtıldığında belirtmek istediğiniz değerlerin parametrelerini siz tanımlarsınız. Şablon, parametre değerlerinin tümünü içeren parametreler adlı bir bölüm içerir.
Dağıtmakta olduğunuz projeye veya dağıtım yaptığınız ortama göre değişen değerler için bir parametre tanımlamanız gerekir. Her zaman aynı kalan değerler için parametre tanımlamayın. Her parametre değeri, dağıtılan kaynakları tanımlamak için şablonda kullanılır. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Redsıs için Azure önbelleğinin konumu. En iyi performansı elde etmek için, önbellekle birlikte kullanılacak uygulamayla aynı konumu kullanın.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Tanılama için kullanılacak mevcut depolama hesabının adı. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>EnableNonSslPort
SSL olmayan bağlantı noktaları üzerinden erişime izin verilip verilmeyeceğini gösteren bir Boolean değer.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Tanılamanın etkinleştirilip etkinleştirilmediğini gösteren bir değer. Açık veya kapalı ' yı kullanın.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Dağıtılacak kaynaklar
### <a name="azure-cache-for-redis"></a>Redis için Azure Önbelleği
Redsıs için Azure önbelleği oluşturur.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2017-05-01-preview",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Dağıtımı çalıştırma komutları
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup



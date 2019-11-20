---
title: REST API ve şablonla kaynakları dağıtma
description: Kaynakları Azure 'a dağıtmak için Azure Resource Manager ve Kaynak Yöneticisi REST API kullanın. Kaynaklar, bir Resource Manager şablonunda tanımlanır.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 3b4242e1fec98b583d5204904bc1adbd388f7955
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149651"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Kaynakları Resource Manager şablonları ve Resource Manager REST API’si ile dağıtma

Bu makalede, kaynaklarınızı Azure 'a dağıtmak için Kaynak Yöneticisi şablonlarla birlikte Kaynak Yöneticisi REST API nasıl kullanılacağı açıklanmaktadır.  

Şablonunuzu istek gövdesine ekleyebilir veya bir dosyaya bağlayabilirsiniz. Bir dosya kullanılırken, yerel bir dosya veya bir URI aracılığıyla kullanılabilen bir dış dosya olabilir. Şablonunuz bir depolama hesabsunsa, şablona erişimi kısıtlayabilir ve dağıtım sırasında bir paylaşılan erişim imzası (SAS) belirtebilirsiniz.

## <a name="deployment-scope"></a>Dağıtım kapsamı

Dağıtımınızı bir yönetim grubuna, bir Azure aboneliğine veya bir kaynak grubuna hedefleyebilirsiniz. Çoğu durumda, dağıtımları bir kaynak grubuna hedefleyebilirsiniz. Belirtilen kapsamda ilke ve rol atamaları uygulamak için yönetim grubu veya abonelik dağıtımlarını kullanın. Ayrıca, abonelik dağıtımlarını bir kaynak grubu oluşturmak ve kaynakları dağıtmak için de kullanabilirsiniz. Dağıtımın kapsamına bağlı olarak, farklı komutlar kullanırsınız.

Bir **kaynak grubuna**dağıtmak Için, [dağıtımlar-oluştur](/rest/api/resources/deployments/createorupdate)' u kullanın. İsteğin gönderildiği yer:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Bir **aboneliğe**dağıtmak için [dağıtımlar-abonelik kapsamında oluştur](/rest/api/resources/deployments/createorupdateatsubscriptionscope)' u kullanın. İsteğin gönderildiği yer:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Abonelik düzeyi dağıtımları hakkında daha fazla bilgi için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](deploy-to-subscription.md).

Bir **yönetim grubuna**dağıtmak Için, [dağıtımlar-yönetim grubu kapsamında oluştur](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)' u kullanın. İsteğin gönderildiği yer:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Yönetim grubu düzeyi dağıtımları hakkında daha fazla bilgi için bkz. [Yönetim grubu düzeyinde kaynak oluşturma](deploy-to-management-group.md).

Bu makaledeki örnekler, kaynak grubu dağıtımlarını kullanır.

## <a name="deploy-with-the-rest-api"></a>REST API ile dağıtma

1. Kimlik doğrulama belirteçleri dahil olmak üzere [ortak parametreleri ve üst bilgileri](/rest/api/azure/)ayarlayın.

1. Mevcut bir kaynak grubunuz yoksa, bir kaynak grubu oluşturun. Abonelik KIMLIĞINIZI, yeni kaynak grubunun adını ve çözümünüz için gereken konumu belirtin. Daha fazla bilgi için bkz. [kaynak grubu oluşturma](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Şunun gibi bir istek gövdesi ile:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. [Bir şablon dağıtımını doğrulama](/rest/api/resources/deployments/validate) işlemini çalıştırarak yürütmeden önce dağıtımınızı doğrulayın. Dağıtımı sınarken, dağıtımı yürütürken yaptığınız gibi parametreleri tam olarak sağlayın (bir sonraki adımda gösterilmektedir).

1. Bir şablonu dağıtmak için, abonelik KIMLIĞINIZI, kaynak grubunun adını, istek URI 'sindeki dağıtımın adını belirtin. 

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   İstek gövdesinde, şablonunuz ve parametre dosyanıza bir bağlantı sağlayın. Parametre dosyası hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi parametre dosyası oluşturma](resource-manager-parameter-files.md).

   **Modun** **artımlı**olarak ayarlandığını unutmayın. Dağıtımı tamamen çalıştırmak için **modu** , **tamamlanmış**olarak ayarlayın. Şablonunuzda olmayan kaynakları yanlışlıkla silebilmeniz için, tüm modunu kullanırken dikkatli olun.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Yanıt içeriğini günlüğe kaydetmek, içerik istemek veya her ikisini de kullanmak istiyorsanız, istekte **Debugsetting** 'i ekleyin.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Depolama hesabınızı, paylaşılan erişim imzası (SAS) belirtecini kullanacak şekilde ayarlayabilirsiniz. Daha fazla bilgi için bkz. [paylaşılan erişim Imzasıyla erişim yetkisi verme](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Bir parametre (örneğin, parola) için hassas bir değer sağlamanız gerekiyorsa, bu değeri bir anahtar kasasına ekleyin. Önceki örnekte gösterildiği gibi dağıtım sırasında anahtar kasasını alın. Daha fazla bilgi için bkz. [dağıtım sırasında güvenli değerleri geçirme](resource-manager-keyvault-parameter.md). 

1. Şablon ve parametreler için dosyalara bağlantı yapmak yerine, bunları istek gövdesine dahil edebilirsiniz. Aşağıdaki örnek, şablon ve parametre satır içi olan istek gövdesini gösterir:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Şablon dağıtımının durumunu almak için [dağıtımlar-al](/rest/api/resources/deployments/get)' ı kullanın.

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Sonraki adımlar

- Hata aldığınızda başarılı bir dağıtıma geri dönmek için, [başarılı bir dağıtımda hata durumunda geri alma](rollback-on-error.md)konusuna bakın.
- Kaynak grubunda var olan, ancak şablonda tanımlanmamış kaynakların nasıl işleneceğini belirtmek için bkz. [Azure Resource Manager Dağıtım modları](deployment-modes.md).
- Zaman uyumsuz REST işlemlerini işleme hakkında bilgi edinmek için bkz. [zaman uyumsuz Azure Işlemlerini izleme](resource-manager-async-operations.md).
- Şablonlar hakkında daha fazla bilgi edinmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](resource-group-authoring-templates.md).


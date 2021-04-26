---
title: REST API ve şablonla kaynakları dağıtma
description: Kaynakları Azure 'a dağıtmak için Azure Resource Manager ve Kaynak Yöneticisi REST API kullanın. Kaynaklar, bir Resource Manager şablonunda tanımlanır.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 90e50598176ddc0327a81df105740f58afd930bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732576"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>ARM şablonlarıyla kaynakları dağıtma ve Azure Resource Manager REST API

Bu makalede, kaynaklarınızı Azure 'a dağıtmak için Azure Resource Manager şablonları (ARM şablonları) ile Azure Resource Manager REST API nasıl kullanılacağı açıklanmaktadır.

Şablonunuzu istek gövdesine ekleyebilir veya bir dosyaya bağlayabilirsiniz. Bir dosya kullanılırken, yerel bir dosya veya bir URI aracılığıyla kullanılabilen bir dış dosya olabilir. Şablonunuz bir depolama hesabsunsa, şablona erişimi kısıtlayabilir ve dağıtım sırasında bir paylaşılan erişim imzası (SAS) belirtebilirsiniz.

## <a name="deployment-scope"></a>Dağıtım kapsamı

Dağıtımınızı bir kaynak grubuna, Azure aboneliğine, yönetim grubuna veya kiracıya hedefleyebilirsiniz. Dağıtımın kapsamına bağlı olarak, farklı komutlar kullanırsınız.

- Bir **kaynak grubuna** dağıtmak Için, [dağıtımlar-oluştur](/rest/api/resources/resources/deployments/createorupdate)' u kullanın. İsteğin gönderildiği yer:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

- Bir **aboneliğe** dağıtmak için [dağıtımlar-abonelik kapsamında oluştur](/rest/api/resources/resources/deployments/createorupdateatsubscriptionscope)' u kullanın. İsteğin gönderildiği yer:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Abonelik düzeyi dağıtımları hakkında daha fazla bilgi için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](deploy-to-subscription.md).

- Bir **yönetim grubuna** dağıtmak Için, [dağıtımlar-yönetim grubu kapsamında oluştur](/rest/api/resources/resources/deployments/createorupdateatmanagementgroupscope)' u kullanın. İsteğin gönderildiği yer:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Yönetim grubu düzeyi dağıtımları hakkında daha fazla bilgi için bkz. [Yönetim grubu düzeyinde kaynak oluşturma](deploy-to-management-group.md).

- Bir **kiracıya** dağıtmak için [dağıtımlar-kiracı kapsamında oluştur veya Güncelleştir](/rest/api/resources/resources/deployments/createorupdateattenantscope)' i kullanın. İsteğin gönderildiği yer:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Kiracı düzeyinde dağıtımlar hakkında daha fazla bilgi için bkz. [kiracı düzeyinde kaynak oluşturma](deploy-to-tenant.md).

Bu makaledeki örnekler, kaynak grubu dağıtımlarını kullanır.

## <a name="deploy-with-the-rest-api"></a>REST API ile dağıtma

1. Kimlik doğrulama belirteçleri dahil olmak üzere [ortak parametreleri ve üst bilgileri](/rest/api/azure/)ayarlayın.

1. Mevcut olmayan bir kaynak grubuna dağıtıyorsanız, kaynak grubunu oluşturun. Abonelik KIMLIĞINIZI, yeni kaynak grubunun adını ve çözümünüz için gereken konumu belirtin. Daha fazla bilgi için bkz. [kaynak grubu oluşturma](/rest/api/resources/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
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

1. Şablonunuzu dağıtmadan önce, şablonun ortamınızda yapacağız değişiklikleri önizleyebilirsiniz. Şablonun beklediğiniz değişiklikleri yaptığını doğrulamak için [ne yapılır işlemini](template-deploy-what-if.md) kullanın. Ayrıca, şablonu hatalara yönelik olarak doğrular.

1. Bir şablonu dağıtmak için, abonelik KIMLIĞINIZI, kaynak grubunun adını, istek URI 'sindeki dağıtımın adını belirtin.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2020-10-01
   ```

   İstek gövdesinde, şablonunuz ve parametre dosyanıza bir bağlantı sağlayın. Parametre dosyası hakkında daha fazla bilgi için bkz. [Resource Manager parametre dosyası oluşturma](parameter-files.md).

   `mode` **Artımlı** olarak ayarlandığını unutmayın. Dağıtımı tamamen çalıştırmak için, `mode` **tamamlanmış** olarak ayarlayın. Şablonunuzda olmayan kaynakları yanlışlıkla silebilmeniz için, tüm modunu kullanırken dikkatli olun.

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

    Yanıt içeriğini günlüğe kaydetmek, içerik istemek veya her ikisini de kullanmak istiyorsanız, `debugSetting` isteğe ekleyin.

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

    Depolama hesabınızı, paylaşılan erişim imzası (SAS) belirtecini kullanacak şekilde ayarlayabilirsiniz. Daha fazla bilgi için bkz. [paylaşılan erişim imzasıyla erişim yetkisi verme](/rest/api/storageservices/delegate-access-with-shared-access-signature).

    Bir parametre (örneğin, parola) için hassas bir değer sağlamanız gerekiyorsa, bu değeri bir anahtar kasasına ekleyin. Önceki örnekte gösterildiği gibi dağıtım sırasında anahtar kasasını alın. Daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](key-vault-parameter.md).

1. Şablon ve parametreler için dosyalara bağlantı yapmak yerine, bunları istek gövdesine dahil edebilirsiniz. Aşağıdaki örnek, şablon ve parametre satır içi olan istek gövdesini gösterir:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
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

1. Şablon dağıtımının durumunu almak için [dağıtımlar-al](/rest/api/resources/resources/deployments/get)' ı kullanın.

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
   ```

## <a name="deployment-name"></a>Dağıtım adı

Dağıtımınıza, gibi bir ad verebilirsiniz `ExampleDeployment` .

Bir dağıtımı her çalıştırdığınızda, kaynak grubunun dağıtım geçmişine dağıtım adı ile bir giriş eklenir. Başka bir dağıtım çalıştırırsanız ve aynı adı verirseniz, önceki girdi geçerli dağıtımla değiştirilmiştir. Dağıtım geçmişinde benzersiz girişleri sürdürmek istiyorsanız, her dağıtıma benzersiz bir ad verin.

Benzersiz bir ad oluşturmak için rastgele bir sayı atayabilirsiniz. Veya bir tarih değeri ekleyin.

Aynı dağıtım adına sahip aynı kaynak grubuna eşzamanlı dağıtımlar çalıştırırsanız, yalnızca son dağıtım tamamlanmıştır. Tamamlanmayan aynı ada sahip dağıtımlar son dağıtımla değiştirilmiştir. Örneğin, adlı bir depolama hesabı dağıtan adlı bir dağıtım çalıştırırsanız `newStorage` `storage1` ve aynı zamanda adlı bir depolama hesabı dağıtan adlı bir dağıtımı çalıştırırsanız `newStorage` `storage2` , yalnızca bir depolama hesabı dağıtırsınız. Elde edilen depolama hesabının adı `storage2` .

Ancak, adlı bir depolama hesabı dağıtan adlı bir dağıtımı çalıştırırsanız `newStorage` `storage1` ve bu işlemi tamamladıktan hemen sonra adlı bir depolama hesabı dağıtan adlı başka bir dağıtım çalıştırırsanız `newStorage` `storage2` , iki depolama hesabınız olur. Biri olarak adlandırılır `storage1` ve diğeri olarak adlandırılır `storage2` . Ancak, dağıtım geçmişinde yalnızca bir girişiniz vardır.

Her dağıtım için benzersiz bir ad belirttiğinizde, bunları çakışma olmadan eşzamanlı olarak çalıştırabilirsiniz. Adlı bir depolama hesabı dağıtan adlı bir dağıtım çalıştırırsanız `newStorage1` `storage1` ve aynı zamanda adında bir depolama hesabı dağıtan adlı başka bir dağıtımı çalıştırırsanız `newStorage2` `storage2` , dağıtım geçmişinde iki depolama hesabınız ve iki giriş vardır.

Eşzamanlı dağıtımlar ile çakışmalardan kaçınmak ve dağıtım geçmişinde benzersiz girişler sağlamak için her dağıtıma benzersiz bir ad verin.

## <a name="next-steps"></a>Sonraki adımlar

- Hata aldığınızda başarılı bir dağıtıma geri dönmek için, [başarılı bir dağıtımda hata durumunda geri alma](rollback-on-error.md)konusuna bakın.
- Kaynak grubunda var olan, ancak şablonda tanımlanmamış kaynakların nasıl işleneceğini belirtmek için bkz. [Azure Resource Manager Dağıtım modları](deployment-modes.md).
- Zaman uyumsuz REST işlemlerini işleme hakkında bilgi edinmek için bkz. [zaman uyumsuz Azure Işlemlerini izleme](../management/async-operations.md).
- Şablonlar hakkında daha fazla bilgi edinmek için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).

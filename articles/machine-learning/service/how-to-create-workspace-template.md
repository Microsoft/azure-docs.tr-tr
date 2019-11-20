---
title: Azure Resource Manager şablonuyla çalışma alanı oluşturma
titleSuffix: Azure Machine Learning
description: Yeni bir Azure Machine Learning çalışma alanı oluşturmak için Azure Resource Manager şablonu kullanmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 8bcfb80d42e7cd1fad6ff4c04415bd8627a3293e
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932137"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Azure Machine Learning için bir çalışma alanı oluşturmak üzere Azure Resource Manager şablonu kullanma

Bu makalede, Azure Resource Manager şablonları kullanarak Azure Machine Learning çalışma alanı oluşturmanın birkaç yolunu öğreneceksiniz. Kaynak Yöneticisi şablonu, kaynakları tek ve eşgüdümlü bir işlem olarak oluşturmayı kolaylaştırır. Şablon, bir dağıtım için gereken kaynakları tanımlayan bir JSON belgesidir. Ayrıca, dağıtım parametrelerini de belirtebilir. Parametreler, şablon kullanılırken giriş değerleri sağlamak için kullanılır.

Daha fazla bilgi için bkz. [Azure Resource Manager şablonuyla uygulama dağıtma](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Önkoşullar

* Bir **Azure aboneliği**. Bir tane yoksa, [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

* CLı 'dan bir şablon kullanmak için [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ya da [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)gerekir.

## <a name="resource-manager-template"></a>Resource Manager şablonu

Aşağıdaki Kaynak Yöneticisi şablonu, bir Azure Machine Learning çalışma alanı ve ilişkili Azure kaynakları oluşturmak için kullanılabilir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
        "allowedValues": [
          "basic",
          "enterprise"
        ],
        "metadata": {
          "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
        }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    }
  ]
}
```

Bu şablon aşağıdaki Azure hizmetlerini oluşturur:

* Azure Kaynak Grubu
* Azure Depolama Hesabı
* Azure Key Vault
* Azure Application Insights
* Azure Container Kayıt Defteri
* Azure Machine Learning çalışma alanı

Kaynak grubu, Hizmetleri tutan kapsayıcıdır. Azure Machine Learning çalışma alanı için çeşitli hizmetler gereklidir.

Örnek şablonda iki parametre vardır:

* Kaynak grubunun ve hizmetlerin oluşturulacağı **konum** .

    Şablon, çoğu kaynak için seçtiğiniz konumu kullanacaktır. Özel durum, diğer hizmetlerin bulunduğu tüm konumlarda kullanılamayan Application Insights hizmetidir. Kullanılabilir olmayan bir konum seçerseniz, hizmet Orta Güney ABD konumunda oluşturulur.

* Azure Machine Learning çalışma alanının kolay adı olan **çalışma alanı adı**.

    Diğer hizmetlerin adları rastgele oluşturulur.

> [!TIP]
> Bu belgeyle ilişkili şablon yeni bir Azure Container Registry oluştururken, kapsayıcı kayıt defteri oluşturmadan de yeni bir çalışma alanı oluşturabilirsiniz. Çalışma alanında kapsayıcı kayıt defteri varsa, bir kapsayıcı kayıt defteri gerektiren bir işlem gerçekleştirdiğinizde bir tane oluşturulacaktır. Örneğin, bir modeli eğitme veya dağıtma.
>
> Ayrıca, yeni bir tane oluşturmak yerine Azure Resource Manager şablonundaki mevcut bir kapsayıcı kayıt defterine veya depolama hesabına başvurabilirsiniz.

Şablonlar hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure Resource Manager şablonları yaz](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager şablonlarıyla uygulama dağıtma](../../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft. MachineLearningServices kaynak türleri](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Azure portalı kullanma

1. [Özel şablondan kaynak dağıtma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)bölümündeki adımları izleyin. __Şablonu Düzenle__ ekranına geldiğinizde, şablonu bu belgeden yapıştırın.
1. Şablonu kullanmak için __Kaydet__ ' i seçin. Aşağıdaki bilgileri sağlayın ve listelenen hüküm ve koşulları kabul edin:

   * Abonelik: Bu kaynaklar için kullanılacak Azure aboneliğini seçin.
   * Kaynak grubu: hizmetleri içeren bir kaynak grubu seçin veya oluşturun.
   * Çalışma alanı adı: oluşturulacak Azure Machine Learning çalışma alanı için kullanılacak ad. Çalışma alanı adı 3 ile 33 karakter arasında olmalıdır. Yalnızca alfasayısal karakterler ve '-' içerebilir.
   * Konum: kaynakların oluşturulacağı konumu seçin.

Daha fazla bilgi için bkz. [özel şablondan kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Azure PowerShell kullanma

Bu örnekte, şablonu geçerli dizinde `azuredeploy.json` adlı bir dosyaya kaydettiğiniz varsayılır:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ile kaynak dağıtma ve Azure POWERSHELL](../../azure-resource-manager/resource-group-template-deploy.md) [SAS belirteci ve Azure PowerShell ile özel kaynak yöneticisi şablonu dağıtma](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Azure CLI kullanma

Bu örnekte, şablonu geçerli dizinde `azuredeploy.json` adlı bir dosyaya kaydettiğiniz varsayılır:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure CLI ile kaynakları dağıtma](../../azure-resource-manager/resource-group-template-deploy-cli.md) ve [SAS BELIRTECI ve Azure clı ile özel kaynak yöneticisi şablonu dağıtma](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault erişim ilkesi ve Azure Resource Manager şablonları

Çalışma alanını ve ilişkili kaynakları (Azure Key Vault dahil) oluşturmak için bir Azure Resource Manager şablonu kullandığınızda, birden çok kez. Örneğin, şablonu bir sürekli tümleştirme ve dağıtım işlem hattının bir parçası ile aynı parametrelerle birden çok kez kullanmak.

Şablonlar aracılığıyla kaynak oluşturma işlemlerinin çoğu ıdempotent, ancak Key Vault şablon her kullanıldığında erişim ilkelerini temizler. Erişim ilkelerinin temizlenmesi, onu kullanan var olan bir çalışma alanının Key Vault erişimini keser. Örneğin, Azure Notebooks VM 'yi durdur/oluştur işlevleri başarısız olabilir.  

Bu sorundan kaçınmak için aşağıdaki yaklaşımlardan birini öneririz:

* Aynı parametreler için şablonu birden çok kez dağıtmayın. Ya da yeniden oluşturmak için şablonu kullanmadan önce mevcut kaynakları silin.
  
* Key Vault erişim ilkelerini inceleyin ve sonra şablonun accessPolicies özelliğini ayarlamak için bu ilkeleri kullanın.
* Key Vault kaynağının zaten var olup olmadığını denetleyin. Varsa, şablon aracılığıyla yeniden oluşturmayın. Örneğin, zaten varsa Key Vault kaynağı oluşturmayı devre dışı bırakmanızı sağlayan bir parametre ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Kaynak Yöneticisi şablonları ve Kaynak Yöneticisi REST API ile kaynakları dağıtın](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Visual Studio aracılığıyla Azure Kaynak grupları oluşturma ve dağıtma](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

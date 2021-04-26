---
title: Şablon belirtimini bağlantılı şablon olarak dağıtma
description: Bağlı bir dağıtımda var olan bir şablon belirtimini dağıtmayı öğrenin.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 8d4ccd77c8b37a696fab7494a8d3f8052fc89b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889272"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Öğretici: bir şablon belirtimini bağlantılı şablon olarak dağıtma (Önizleme)

[Bağlı bir dağıtım](linked-templates.md#linked-template)kullanarak var olan bir [şablon belirtimini](template-specs.md) dağıtmayı öğrenin. ARM şablonlarını kuruluşunuzdaki diğer kullanıcılarla paylaşmak için şablon özelliklerini kullanırsınız. Şablon belirtimini oluşturduktan sonra, Azure PowerShell veya Azure CLı kullanarak şablon belirtimini dağıtabilirsiniz. Ayrıca, bağlantılı bir şablon kullanarak, çözümünüzün bir parçası olarak şablon belirtimini dağıtabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Şablon özellikleri şu anda önizleme aşamasındadır. Azure PowerShell ile kullanmak için, [5.0.0 veya üzeri sürümünü](/powershell/azure/install-az-ps)yüklemelisiniz. Azure CLı ile kullanmak için, [Version 2.14.2 veya üstünü](/cli/azure/install-azure-cli)kullanın.

## <a name="create-a-template-spec"></a>Şablon belirtimi oluşturma

Hızlı başlangıç: bir depolama hesabı dağıtımı için şablon belirtimi oluşturmak üzere [şablon belirtimini oluşturma ve dağıtma](quickstart-create-template-specs.md) . Sonraki bölümde şablon belirtiminin, şablon belirtiminin adı ve şablon belirtimi sürümünün kaynak grubu adına ihtiyacınız vardır.

## <a name="create-the-main-template"></a>Ana şablon oluşturma

Bir ARM şablonunda bir şablon belirtimini dağıtmak için ana şablonunuza bir [dağıtımlar kaynağı](/azure/templates/microsoft.resources/deployments) ekleyin. `templateLink`Özelliğinde, bir şablon belirtiminin kaynak kimliğini belirtin. AŞAĞıDAKI JSON ile **azuredeploy.js** adlı bir şablon oluşturun. Bu öğretici bir yola **c:\Templates\deployTS\azuredeploy.js** kaydettiğiniz, ancak herhangi bir yolu kullanabileceğiniz varsayılmaktadır.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

Şablon belirtimi KIMLIĞI, işlevi kullanılarak oluşturulur [`resourceID()`](template-functions-resource.md#resourceid) . TemplateSpec geçerli dağıtımın aynı kaynak grubunda ise RESOURCEID () işlevindeki kaynak grubu bağımsız değişkeni isteğe bağlıdır.  Ayrıca, kaynak KIMLIĞINI bir parametre olarak doğrudan geçirebilirsiniz. KIMLIĞI almak için şunu kullanın:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Şablon spec KIMLIĞI alma ve bunu Windows PowerShell 'de bir değişkene atama ile ilgili bilinen bir sorun vardır.

---

Parametreleri şablon belirtimine geçirmek için sözdizimi şöyledir:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> ApiVersion, `Microsoft.Resources/deployments` 2020-06-01 veya üzeri olmalıdır.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Bağlı şablonu dağıttığınızda, hem Web uygulamasını hem de depolama hesabını dağıtır. Dağıtım, diğer ARM şablonlarının dağıtılmasıyla aynıdır.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bağlantılı şablonlar içeren bir şablon belirtimi oluşturma hakkında bilgi edinmek için bkz. [bağlantılı şablonun şablon belirtimini oluşturma](template-specs-create-linked.md).

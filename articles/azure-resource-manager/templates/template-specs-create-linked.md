---
title: Bağlantılı şablonlarla şablon belirtimi oluşturma
description: Bağlantılı şablonlar içeren bir şablon spec oluşturmayı öğrenin.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: b1c757895faee208590b638094591d246bf605d8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310623"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Öğretici: bağlı şablonlar ile şablon belirtimi oluşturma (Önizleme)

Ana şablon ve [bağlantılı şablon](linked-templates.md#linked-template)ile [şablon spec](template-specs.md) oluşturmayı öğrenin. ARM şablonlarını kuruluşunuzdaki diğer kullanıcılarla paylaşmak için şablon özelliklerini kullanırsınız. Bu makalede, `relativePath` [dağıtım kaynağının](/azure/templates/microsoft.resources/deployments)özelliğini kullanarak ana şablonu ve bağlı şablonlarını paketlemek için bir şablon belirtiminin nasıl oluşturulacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Şablon özellikleri şu anda önizleme aşamasındadır. Azure PowerShell ile kullanmak için, [5.0.0 veya üzeri sürümünü](/powershell/azure/install-az-ps)yüklemelisiniz. Azure CLı ile kullanmak için, [Version 2.14.2 veya üstünü](/cli/azure/install-azure-cli)kullanın.

## <a name="create-linked-templates"></a>Bağlı şablonlar oluşturma

Ana şablonu ve bağlantılı şablonu oluşturun.

Bir şablonu bağlamak için ana şablonunuza bir [dağıtımlar kaynağı](/azure/templates/microsoft.resources/deployments) ekleyin. `templateLink`Özelliğinde, ana şablonun yoluna uygun olarak bağlı şablonun göreli yolunu belirtin.

Bağlantılı şablon **üzerindelinkedTemplate.js** çağrılır ve ana şablonun depolandığı yolda **yapıtlar** adlı bir alt klasörde depolanır.  RelativePath için aşağıdaki değerlerden birini kullanabilirsiniz:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath`Özelliği, her zaman bildirildiği şablon dosyasına görelidir `relativePath` . bu nedenle, üzerinde linkedTemplate.jsçağrılan ve üzerinde linkedTemplate2.jsaynı yapıt alt klasöründe depolanan başka bir linkedTemplate2.jsvarsa, üzerinde linkedTemplate.js' de belirtilen relativePath yalnızca ' dir `linkedTemplate2.json` .

1. Aşağıdaki JSON ile ana şablon oluşturun. Ana şablonu yerel bilgisayarınıza **azuredeploy.js** olarak kaydedin. Bu öğretici bir yola **c:\Templates\linkedTS\azuredeploy.js** kaydettiğiniz, ancak herhangi bir yolu kullanabileceğiniz varsayılmaktadır.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
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
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > ApiVersion, `Microsoft.Resources/deployments` 2020-06-01 veya üzeri olmalıdır.

1. Ana şablonun kaydedildiği klasörde **yapıtlar** adlı bir dizin oluşturun.
1. Aşağıdaki JSON ile bağlantılı şablonu oluşturun:

    ```json
    {
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
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
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
    }
    ```

1. Şablonu **yapılar** klasöründe **linkedTemplate.js** olarak kaydedin.

## <a name="create-template-spec"></a>Şablon belirtimi oluşturma

Şablon Özellikleri, kaynak gruplarında depolanır.  Bir kaynak grubu oluşturun ve ardından aşağıdaki betiği kullanarak bir şablon belirtimi oluşturun. Şablon belirtiminin adı **Webspec**' dir.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "<path-to-main-template>"
```

---

İşiniz bittiğinde, Azure portal şablon belirtimini veya aşağıdaki cmdlet 'i kullanarak görüntüleyebilirsiniz:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Şablon belirtimini dağıt

Artık şablon belirtimini dağıtabilirsiniz. Şablon belirtiminin dağıtım, şablon belirtiminin kaynak KIMLIĞI ' ni geçirmeniz dışında, içerdiği şablonu dağıtmaktan benzer. Aynı dağıtım komutlarını kullanırsınız ve gerekirse, şablon belirtiminin parametre değerlerini geçirin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Şablon spec KIMLIĞI alma ve bunu Windows PowerShell 'de bir değişkene atama ile ilgili bilinen bir sorun vardır.

---

## <a name="next-steps"></a>Sonraki adımlar

Şablon belirtimini bağlantılı şablon olarak dağıtma hakkında bilgi edinmek için bkz. [öğretici: şablon belirtimini bağlantılı şablon olarak dağıtma](template-specs-deploy-linked-template.md).

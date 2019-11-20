---
title: Şablon kaynak konumu
description: Azure Resource Manager şablonunda kaynak konumunun nasıl ayarlanacağını açıklar.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 1e711b2cfeb42c33dbfa68b1fbdabd42cbd46d10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150631"
---
# <a name="set-resource-location-in-resource-manager-template"></a>Kaynak Yöneticisi şablonunda kaynak konumunu ayarla

Bir şablonu dağıttığınızda, her kaynak için bir konum sağlamanız gerekir. Konumun kaynak grubu konumuyla aynı konumda olması gerekmez.

## <a name="get-available-locations"></a>Kullanılabilir konumları al

Farklı konumlarda farklı kaynak türleri desteklenir. Kaynak türü için desteklenen konumları almak üzere Azure PowerShell veya Azure CLı kullanın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Konum parametresini kullan

Şablonunuzun dağıtımı sırasında esneklik sağlamak için, kaynakların konumunu belirtmek için bir parametre kullanın. Parametrenin varsayılan değerini `resourceGroup().location`olarak ayarlayın.

Aşağıdaki örnekte, parametresi olarak belirtilen bir konuma dağıtılan bir depolama hesabı gösterilmektedir:

```json
{
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
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
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

## <a name="next-steps"></a>Sonraki adımlar

* Şablon işlevlerinin tam listesi için bkz. [Azure Resource Manager şablon işlevleri](resource-group-template-functions.md).
* Şablon dosyaları hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](resource-group-authoring-templates.md).

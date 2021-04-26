---
title: Kapsamlı dağıtımlarda şablon işlevleri
description: Şablon işlevlerinin kapsamlı dağıtımlarda nasıl çözümlendiğini açıklar. Kapsam bir kiracı, yönetim grubu, abonelik ve kaynak grupları olabilir.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: f128448380612bc9b8d9114226e8a3036feeead8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492102"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>Dağıtım kapsamlarında ARM şablon işlevleri

Azure Resource Manager şablonları (ARM şablonları) sayesinde kaynak gruplarına, aboneliklere, yönetim gruplarına veya kiracılara dağıtabilirsiniz. Genellikle, [ARM şablon işlevleri](template-functions.md) tüm kapsamlar için aynı şekilde çalışır. Bu makalede, bazı işlevlerde kapsama bağlı olarak var olan farklılıklar açıklanır.

## <a name="supported-functions"></a>Desteklenen işlevler

Farklı kapsamlara dağıtım yaparken bazı önemli noktalar vardır:

* [ResourceGroup ()](template-functions-resource.md#resourcegroup) işlevi, kaynak grubu dağıtımları için **desteklenir** .
* [Abonelik ()](template-functions-resource.md#subscription) işlevi, kaynak grubu ve abonelik dağıtımları için **desteklenir** .
* [Reference ()](template-functions-resource.md#reference) ve [List ()](template-functions-resource.md#list) işlevleri tüm kapsamlar için **desteklenir** .
* Kaynak grubunda dağıtılan bir kaynağın KIMLIĞINI almak için [RESOURCEID ()](template-functions-resource.md#resourceid) kullanın.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* Abonelikte dağıtılan bir kaynağın KIMLIĞINI almak için [Subscriptionresourceıd ()](template-functions-resource.md#subscriptionresourceid) işlevini kullanın.

  Örneğin, bir aboneliğe dağıtılan bir ilke tanımının kaynak KIMLIĞINI almak için şunu kullanın:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Yönetim grubunun uzantıları olarak uygulanan kaynaklar için [Extensionresourceıd ()](template-functions-resource.md#extensionresourceid) işlevini kullanın. Yönetim grubuna dağıtılan özel ilke tanımları, yönetim grubunun uzantılarıdır.

  Yönetim grubu düzeyinde özel bir ilke tanımının kaynak KIMLIĞINI almak için şunu kullanın:

  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* Kiracıda dağıtılan bir kaynağın KIMLIĞINI almak için [Tenantresourceıd ()](template-functions-resource.md#tenantresourceid) işlevini kullanın. Yerleşik ilke tanımları, kiracı düzeyi kaynaklarıdır. Yönetim grubu düzeyinde yerleşik bir ilke atarken, Tenantresourceıd işlevini kullanın.

  Yerleşik bir ilke tanımının kaynak KIMLIĞINI almak için şunu kullanın:

  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Kapsamlardaki işlev çözünürlüğü

Birden fazla kapsama dağıtırken, [resourceGroup ()](template-functions-resource.md#resourcegroup) ve [Subscription ()](template-functions-resource.md#subscription) işlevleri, şablonu nasıl belirtbir şekilde farklı şekilde çözümlenir. Bir dış şablona bağlandığınızda işlevler her zaman ilgili şablonun kapsamına çözümlenir. Bir şablonu bir üst şablon içine yuvalandığınızda, `expressionEvaluationOptions` işlevlerin ana şablon veya iç içe şablon için kaynak grubuna ve aboneliğe çözümlenip çözümlenmediğini belirtmek için özelliğini kullanın. Özelliğini, `inner` iç içe geçmiş şablonun kapsamına çözülecek şekilde ayarlayın. Özelliğini, `outer` üst şablonun kapsamına çözülecek şekilde ayarlayın.

Aşağıdaki tabloda, işlevlerin üst veya katıştırılmış kaynak grubuna ve aboneliğe çözümlenip çözümlenmediğini gösterir.

| Şablon türü | Kapsam | Çözüm |
| ------------- | ----- | ---------- |
| ble        | dış (varsayılan) | Üst kaynak grubu |
| ble        | Dahili | Alt kaynak grubu |
| bağlandı        | Yok   | Alt kaynak grubu |

Aşağıdaki [örnek şablonda](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) şunları gösterir:

* Varsayılan (dış) kapsamlı iç içe şablon
* iç kapsama sahip iç içe şablon
* bağlantılı şablon

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Önceki şablonu test etmek ve sonuçları görmek için PowerShell veya Azure CLı kullanın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Yukarıdaki örnekteki çıktı:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Yukarıdaki örnekteki çıktı:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Sonraki adımlar

* Şablonunuzda parametrelerin nasıl tanımlanacağını anlamak için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
* Yaygın dağıtım hatalarını çözümlemeye yönelik ipuçları için bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarını giderme](common-deployment-errors.md).
* SAS belirteci gerektiren bir şablonu dağıtma hakkında daha fazla bilgi için bkz. [özel ARM ŞABLONUNU SAS belirteci Ile dağıtma](secure-template-with-sas-token.md).

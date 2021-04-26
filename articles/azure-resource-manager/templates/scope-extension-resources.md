---
title: Uzantı kaynak türlerindeki kapsam
description: Uzantı kaynak türleri dağıtımında kapsam özelliğinin nasıl kullanılacağını açıklar.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: ce08ca951e24c1c0a5450052cf814a68888837c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492170"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>ARM şablonlarındaki uzantı kaynakları için kapsam ayarlama

Uzantı kaynağı, başka bir kaynağı değiştiren bir kaynaktır. Örneğin, bir kaynağa bir rol atayabilirsiniz. Rol ataması, bir uzantı kaynak türüdür.

Uzantı kaynak türlerinin tam listesi için bkz. [diğer kaynakların özelliklerini genişleten kaynak türleri](../management/extension-resource-types.md).

Bu makalede, bir Azure Resource Manager şablonuyla (ARM şablonu) dağıtıldığında bir uzantı kaynak türü kapsamının nasıl ayarlanacağı gösterilmektedir. Bir kaynağa uygulanırken uzantı kaynakları için kullanılabilir kapsam özelliğini açıklar.

> [!NOTE]
> Scope özelliği yalnızca uzantı kaynak türlerinde kullanılabilir. Uzantı türü olmayan bir kaynak türü için farklı bir kapsam belirtmek üzere, iç içe veya bağlı bir dağıtım kullanın. Daha fazla bilgi için bkz. [kaynak grubu dağıtımları](deploy-to-resource-group.md), [abonelik dağıtımları](deploy-to-subscription.md), [Yönetim grubu dağıtımları](deploy-to-management-group.md)ve [kiracı dağıtımları](deploy-to-tenant.md).

## <a name="apply-at-deployment-scope"></a>Dağıtım kapsamına Uygula

Hedef dağıtım kapsamında bir uzantı kaynak türü uygulamak için kaynağı, herhangi bir kaynak türünde olduğu gibi şablonunuza eklersiniz. Kullanılabilir kapsamlar [kaynak grubu](deploy-to-resource-group.md), [abonelik](deploy-to-subscription.md), [Yönetim grubu](deploy-to-management-group.md)ve [kiracıya](deploy-to-tenant.md)ait. Dağıtım kapsamı, kaynak türünü desteklemelidir.

Aşağıdaki şablon bir kilit dağıtır.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Bir kaynak grubuna dağıtıldığında, kaynak grubunu kilitler.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

Bir sonraki örnek bir rol atar.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Bir aboneliğe dağıtıldığında, rolü aboneliğe atar.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Kaynağa uygula

Bir kaynağa uzantı kaynağı uygulamak için `scope` özelliğini kullanın. Kapsam özelliğini, uzantısı eklemekte olduğunuz kaynağın adı olarak ayarlayın. Scope özelliği, uzantı kaynak türü için bir kök özelliktir.

Aşağıdaki örnek bir depolama hesabı oluşturur ve buna bir rol uygular.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Sonraki adımlar

* Şablonunuzda parametrelerin nasıl tanımlanacağını anlamak için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
* Yaygın dağıtım hatalarını çözümlemeye yönelik ipuçları için bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarını giderme](common-deployment-errors.md).
* SAS belirteci gerektiren bir şablonu dağıtma hakkında daha fazla bilgi için bkz. [özel ARM ŞABLONUNU SAS belirteci Ile dağıtma](secure-template-with-sas-token.md).

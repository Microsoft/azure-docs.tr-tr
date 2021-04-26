---
title: Kaynakları kiracıya dağıtma
description: Azure Resource Manager şablonundaki kiracı kapsamındaki kaynakların nasıl dağıtılacağını açıklar.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 0b17b8741d1701720de86d8039be3b6cd28ace5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781910"
---
# <a name="tenant-deployments-with-arm-templates"></a>ARM şablonlarıyla kiracı dağıtımları

Kuruluşunuz geliştikçe, Azure AD kiracınızda [ilkeler](../../governance/policy/overview.md) veya [Azure rol tabanlı erişim denetımı (Azure RBAC)](../../role-based-access-control/overview.md) tanımlamanız ve atamanız gerekebilir. Kiracı düzeyinde şablonlar ile genel olarak ilkeleri uygulayabilir ve genel düzeyde roller atayabilirsiniz.

## <a name="supported-resources"></a>Desteklenen kaynaklar

Tüm kaynak türleri kiracı düzeyine dağıtılamaz. Bu bölümde hangi kaynak türlerinin desteklendiği listelenmektedir.

Azure Ilkeleri için şunu kullanın:

* [Poliyasatamaları](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Azure rol tabanlı erişim denetimi (Azure RBAC) için şunu kullanın:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

Yönetim gruplarına, aboneliklerine veya kaynak gruplarına dağıtan iç içe şablonlar için şunu kullanın:

* [dağıtımlar](/azure/templates/microsoft.resources/deployments)

Yönetim grupları oluşturmak için şunu kullanın:

* [Yönetim grupları](/azure/templates/microsoft.management/managementgroups)

Abonelik oluşturmak için şunu kullanın:

* [deyim](/azure/templates/microsoft.subscription/aliases)

Maliyetleri yönetmek için şunu kullanın:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [yönergelerin](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [ınvoicesections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Portalı yapılandırmak için şunu kullanın:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Şema

Kiracı dağıtımları için kullandığınız şema, kaynak grubu dağıtımları için şemadan farklıdır.

Şablonlar için şunu kullanın:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

Bir parametre dosyasının şeması, tüm dağıtım kapsamları için aynıdır. Parametre dosyaları için şunu kullanın:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>Gerekli erişim

Şablonu dağıtmanın asıl, kiracı kapsamında kaynak oluşturmak için izinlere sahip olmalıdır. Sorumlunun, dağıtım eylemlerini ( `Microsoft.Resources/deployments/*` ) yürütme ve şablonda tanımlanan kaynakları oluşturma izni olması gerekir. Örneğin, bir yönetim grubu oluşturmak için sorumlu, kiracı kapsamında katkıda bulunan iznine sahip olmalıdır. Rol atamaları oluşturmak için sorumlunun sahip iznine sahip olması gerekir.

Azure Active Directory genel yöneticisinin rol atama izni otomatik olarak yoktur. Şablon dağıtımlarını kiracı kapsamında etkinleştirmek için, genel yönetici aşağıdaki adımları vermelidir:

1. Genel yöneticinin rol atayabilmesi için hesap erişimini yükseltin. Daha fazla bilgi için bkz. [tüm Azure aboneliklerini ve Yönetim gruplarını yönetmek için erişimi yükseltme](../../role-based-access-control/elevate-access-global-admin.md).

1. Şablonları dağıtmak için gereken sorumlu sahibine veya katkıda bulunanı atayın.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Asıl öğe artık şablonu dağıtmak için gerekli izinlere sahiptir.

## <a name="deployment-commands"></a>Dağıtım komutları

Kiracı dağıtımları komutları, kaynak grubu dağıtımları için komutlardan farklıdır.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı için [az Deployment Tenant Create](/cli/azure/deployment/tenant#az_deployment_tenant_create)kullanın:

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell için [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)kullanın.

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

ARM şablonları dağıtmaya yönelik dağıtım komutları ve seçenekleri hakkında daha ayrıntılı bilgi için bkz.:

* [ARM şablonları ve Azure portal kaynak dağıtma](deploy-portal.md)
* [ARM şablonları ve Azure CLı ile kaynak dağıtma](deploy-cli.md)
* [ARM şablonları ve Azure PowerShell kaynak dağıtma](deploy-powershell.md)
* [ARM şablonlarıyla kaynakları dağıtma ve Azure Resource Manager REST API](deploy-rest.md)
* [GitHub deposundan şablon dağıtmak için bir dağıtım düğmesi kullanın](deploy-to-azure-button.md)
* [ARM şablonlarını Cloud Shell dağıtma](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Dağıtım konumu ve adı

Kiracı düzeyinde dağıtımlar için dağıtım için bir konum sağlamanız gerekir. Dağıtımın konumu, dağıttığınız kaynakların konumundan ayrıdır. Dağıtım konumu, dağıtım verilerinin depolanacağı konumu belirtir. [Abonelik](deploy-to-subscription.md) ve [Yönetim grubu](deploy-to-management-group.md) dağıtımları da bir konum gerektirir. [Kaynak](deploy-to-resource-group.md) grubu dağıtımları için, kaynak grubunun konumu dağıtım verilerini depolamak için kullanılır.

Dağıtım için bir ad verebilir veya varsayılan dağıtım adını kullanabilirsiniz. Varsayılan ad şablon dosyasının adıdır. Örneğin, _üzerindeazuredeploy.js_ adlı bir şablon dağıtmak, **azuredeploy** varsayılan dağıtım adını oluşturur.

Her dağıtım adı için konum sabittir. Farklı bir konumda aynı ada sahip mevcut bir dağıtım olduğunda tek bir konumda dağıtım oluşturamazsınız. Örneğin, **deployment1** adına sahip bir kiracı dağıtımı oluşturursanız, daha sonra **deployment1** adında bir dağıtım **oluşturalım**, ancak **westus**. Hata kodunu alırsanız `InvalidDeploymentLocation` , bu ad için önceki dağıtımla farklı bir ad veya aynı konumu kullanın.

## <a name="deployment-scopes"></a>Dağıtım kapsamları

Bir kiracıya dağıtırken kaynakların dağıtımını yapabilirsiniz:

* Kiracı
* kiracının içindeki yönetim grupları
* Aboneliklerin
* kaynak grupları

[Uzantı kaynağı](scope-extension-resources.md) , dağıtım hedefinden farklı bir hedef kapsamına eklenebilir.

Şablonu dağıtan kullanıcının belirtilen kapsama erişimi olmalıdır.

Bu bölümde, farklı kapsamların nasıl ayarlanacağı gösterilmektedir. Bu farklı kapsamları tek bir şablonda birleştirebilirsiniz.

### <a name="scope-to-tenant"></a>Kapsam-kiracı

Şablonun kaynaklar bölümünde tanımlanan kaynaklar kiracıya uygulanır.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Kapsam-yönetim grubu

Kiracıdaki bir yönetim grubunu hedeflemek için, iç içe geçmiş bir dağıtım ekleyin ve `scope` özelliğini belirtin.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Abonelik kapsamı

Ayrıca, Kiracıdaki abonelikleri de hedefleyebilirsiniz. Şablonu dağıtan kullanıcının belirtilen kapsama erişimi olmalıdır.

Kiracıdaki bir aboneliği hedeflemek için, iç içe geçmiş bir dağıtım ve `subscriptionId` özelliğini kullanın.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Kapsam-kaynak grubu

Ayrıca, kiracının içindeki kaynak gruplarını hedefleyebilirsiniz. Şablonu dağıtan kullanıcının belirtilen kapsama erişimi olmalıdır.

Kiracının içindeki bir kaynak grubunu hedeflemek için, iç içe geçmiş bir dağıtım kullanın. `subscriptionId`Ve özelliklerini ayarlayın `resourceGroup` . Kaynak grubunun konumunda dağıtıldığından, iç içe dağıtım için bir konum ayarlamazsanız.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Yönetim grubu oluşturma

Aşağıdaki şablon bir yönetim grubu oluşturur.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Hesabınızın kiracıya dağıtma izni yoksa başka bir kapsama dağıtarak yönetim grupları oluşturabilirsiniz. Daha fazla bilgi için bkz. [Yönetim grubu](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Rol atama

Aşağıdaki şablon, kiracı kapsamına bir rol atar.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Sonraki adımlar

* Rol atama hakkında bilgi edinmek için bkz. [Azure Resource Manager şablonları kullanarak Azure rol atamaları ekleme](../../role-based-access-control/role-assignments-template.md).
* Ayrıca, şablonları [abonelik düzeyinde](deploy-to-subscription.md) veya [Yönetim grubu düzeyinde](deploy-to-management-group.md)dağıtabilirsiniz.

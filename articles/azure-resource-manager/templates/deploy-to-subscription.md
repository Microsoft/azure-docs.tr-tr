---
title: Kaynakları aboneliğe dağıtma
description: Azure Resource Manager şablonunda bir kaynak grubu oluşturmayı açıklar. Ayrıca Azure abonelik kapsamındaki kaynakların nasıl dağıtılacağını gösterir.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 3598fe290fd993cbbc662ba9d3a3c5ba8c207bc0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781928"
---
# <a name="subscription-deployments-with-arm-templates"></a>ARM şablonlarıyla abonelik dağıtımları

Kaynakların yönetimini basitleştirmek için, Azure aboneliğinizin düzeyindeki kaynakları dağıtmak üzere bir Azure Resource Manager şablonu (ARM şablonu) kullanabilirsiniz. Örneğin, abonelikleriniz için [ilkeler](../../governance/policy/overview.md) ve [Azure rol tabanlı erişim denetımı (Azure RBAC)](../../role-based-access-control/overview.md) dağıtabilirsiniz. Ayrıca, abonelik içinde kaynak grupları oluşturabilir ve kaynakları abonelikte kaynak gruplarına dağıtabilirsiniz.

> [!NOTE]
> Abonelik düzeyi dağıtımında 800 farklı kaynak grubuna dağıtım yapabilirsiniz.

Şablonları abonelik düzeyinde dağıtmak için Azure CLı, PowerShell, REST API veya portal kullanın.

## <a name="supported-resources"></a>Desteklenen kaynaklar

Tüm kaynak türleri abonelik düzeyine dağıtılamaz. Bu bölümde hangi kaynak türlerinin desteklendiği listelenmektedir.

Azure şemaları için şunu kullanın:

* [Yapıt](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [Blueprint](/azure/templates/microsoft.blueprint/blueprints)
* [Şema tasmi](/azure/templates/microsoft.blueprint/blueprintassignments)
* [sürümler (planlar)](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure Ilkeleri için şunu kullanın:

* [Poliyasatamaları](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [düzeltmeler](/azure/templates/microsoft.policyinsights/remediations)

Azure rol tabanlı erişim denetimi (Azure RBAC) için şunu kullanın:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Kaynak gruplarına dağıtan iç içe şablonlar için şunu kullanın:

* [dağıtımlar](/azure/templates/microsoft.resources/deployments)

Yeni kaynak grupları oluşturmak için şunu kullanın:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Aboneliğinizi yönetmek için şunu kullanın:

* [Danışman yapılandırması](/azure/templates/microsoft.advisor/configurations)
* [bütçelerinin](/azure/templates/microsoft.consumption/budgets)
* [Analiz profilini değiştir](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [lerimi](/azure/templates/microsoft.resources/tags)

Desteklenen diğer türler şunlardır:

* [Scopeasyleri](/azure/templates/microsoft.managednetwork/scopeassignments)
* [Eventabonelikleri](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Şema

Abonelik düzeyi dağıtımlar için kullandığınız şema, kaynak grubu dağıtımları için şemadan farklıdır.

Şablonlar için şunu kullanın:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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

## <a name="deployment-commands"></a>Dağıtım komutları

Bir aboneliğe dağıtmak için abonelik düzeyinde dağıtım komutlarını kullanın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı için [az Deployment Sub Create](/cli/azure/deployment/sub#az_deployment_sub_create)kullanın. Aşağıdaki örnek, bir kaynak grubu oluşturmak için bir şablon dağıtır:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell dağıtım komutu için [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) veya diğer adını kullanın `New-AzSubscriptionDeployment` . Aşağıdaki örnek, bir kaynak grubu oluşturmak için bir şablon dağıtır:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
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

Abonelik düzeyindeki dağıtımlar için, dağıtım için bir konum sağlamanız gerekir. Dağıtımın konumu, dağıttığınız kaynakların konumundan ayrıdır. Dağıtım konumu, dağıtım verilerinin depolanacağı konumu belirtir. [Yönetim grubu](deploy-to-management-group.md) ve [kiracı](deploy-to-tenant.md) dağıtımları da bir konum gerektirir. [Kaynak](deploy-to-resource-group.md) grubu dağıtımları için, kaynak grubunun konumu dağıtım verilerini depolamak için kullanılır.

Dağıtım için bir ad verebilir veya varsayılan dağıtım adını kullanabilirsiniz. Varsayılan ad şablon dosyasının adıdır. Örneğin, _üzerindeazuredeploy.js_ adlı bir şablon dağıtmak, **azuredeploy** varsayılan dağıtım adını oluşturur.

Her dağıtım adı için konum sabittir. Farklı bir konumda aynı ada sahip mevcut bir dağıtım olduğunda tek bir konumda dağıtım oluşturamazsınız. Örneğin, **deployment1** adında bir abonelik dağıtımı **oluşturursanız, daha** sonra adı **deployment1** ancak **westus** olan başka bir dağıtım oluşturamazsınız. Hata kodunu alırsanız `InvalidDeploymentLocation` , bu ad için önceki dağıtımla farklı bir ad veya aynı konumu kullanın.

## <a name="deployment-scopes"></a>Dağıtım kapsamları

Bir aboneliğe dağıtırken, kaynakların dağıtımını yapabilirsiniz:

* işlemin hedef aboneliği
* Kiracıdaki tüm abonelikler
* abonelik veya diğer abonelikler içindeki kaynak grupları
* abonelik için kiracı

[Uzantı kaynağı](scope-extension-resources.md) , dağıtım hedefinden farklı bir hedef kapsamına eklenebilir.

Şablonu dağıtan kullanıcının belirtilen kapsama erişimi olmalıdır.

Bu bölümde, farklı kapsamların nasıl ayarlanacağı gösterilmektedir. Bu farklı kapsamları tek bir şablonda birleştirebilirsiniz.

### <a name="scope-to-target-subscription"></a>Hedef aboneliğin kapsamı

Kaynakları hedef aboneliğe dağıtmak için bu kaynakları şablonun kaynaklar bölümüne ekleyin.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Aboneliğe dağıtım örnekleri için bkz. [kaynak grupları oluşturma](#create-resource-groups) ve [ilke tanımı atama](#assign-policy-definition).

### <a name="scope-to-other-subscription"></a>Kapsamı diğer aboneliğe

Kaynakları, işlemden abonelikten farklı bir aboneliğe dağıtmak için, iç içe geçmiş bir dağıtım ekleyin. Özelliğini, `subscriptionId` dağıtmak istediğiniz ABONELIĞIN kimliği olarak ayarlayın. `location`İç içe dağıtım için özelliği ayarlayın.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Kapsam-kaynak grubu

Abonelik içindeki bir kaynak grubuna kaynak dağıtmak için, iç içe geçmiş bir dağıtım ekleyin ve özelliğini ekleyin `resourceGroup` . Aşağıdaki örnekte, iç içe dağıtım adlı bir kaynak grubunu hedefler `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Kaynak grubuna dağıtım örneği için bkz. [kaynak grubu ve kaynakları oluşturma](#create-resource-group-and-resources).

### <a name="scope-to-tenant"></a>Kapsam-kiracı

Kiracıda kaynak oluşturmak için öğesini `scope` olarak ayarlayın `/` . Şablonu dağıtan kullanıcının [kiracıya dağıtmak için gerekli erişimi](deploy-to-tenant.md#required-access)olmalıdır.

İç içe bir dağıtım kullanmak için `scope` ve ayarlayın `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

Ya da kapsamını `/` Yönetim grupları gibi bazı kaynak türleri için olarak ayarlayabilirsiniz.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

Daha fazla bilgi için bkz. [Yönetim grubu](deploy-to-management-group.md#management-group).

## <a name="resource-groups"></a>Kaynak grupları

### <a name="create-resource-groups"></a>Kaynak grupları oluşturma

ARM şablonunda bir kaynak grubu oluşturmak için kaynak grubu için bir ad ve konum içeren bir [Microsoft. resources/resourceGroups](/azure/templates/microsoft.resources/allversions) kaynağı tanımlayın.

Aşağıdaki şablon boş bir kaynak grubu oluşturur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Birden fazla kaynak grubu oluşturmak için kaynak gruplarıyla [Kopyala öğesini](copy-resources.md) kullanın.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Kaynak yinelemesi hakkında daha fazla bilgi için bkz. [ARM şablonlarındaki kaynak yinelemesi](./copy-resources.md)ve [öğretici: ARM şablonlarıyla birden çok kaynak örneği oluşturma](./template-tutorial-create-multiple-instances.md).

### <a name="create-resource-group-and-resources"></a>Kaynak grubu ve kaynakları oluşturma

Kaynak grubu oluşturmak ve kaynaklarına kaynak dağıtmak için, iç içe geçmiş bir şablon kullanın. İç içe şablon, kaynak grubuna dağıtılacak kaynakları tanımlar. Kaynak grubunun kaynakları dağıtımdan önce mevcut olduğundan emin olmak için, iç içe geçmiş şablonu kaynak grubuna bağımlı olarak ayarlayın. En fazla 800 kaynak grubuna dağıtım yapabilirsiniz.

Aşağıdaki örnek, bir kaynak grubu oluşturur ve kaynak grubuna bir depolama hesabı dağıtır.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="azure-policy"></a>Azure İlkesi

### <a name="assign-policy-definition"></a>İlke tanımı ata

Aşağıdaki örnek, aboneliğe var olan bir ilke tanımını atar. İlke tanımı parametreleri alırsa, bunları bir nesne olarak sağlayın. İlke tanımı parametre almadıysanız varsayılan boş nesneyi kullanın.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Bu şablonu Azure CLı ile dağıtmak için şunu kullanın:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Bu şablonu PowerShell ile dağıtmak için şunu kullanın:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>İlke tanımları oluşturma ve atama

Aynı şablonda bir ilke tanımı [tanımlayabilir](../../governance/policy/concepts/definition-structure.md) ve atayabilirsiniz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Aboneliğinizde ilke tanımı oluşturmak ve aboneliği aboneliğe atamak için aşağıdaki CLı komutunu kullanın:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Bu şablonu PowerShell ile dağıtmak için şunu kullanın:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Şema tanımı oluştur

Şablondan bir şema tanımı [oluşturabilirsiniz](../../governance/blueprints/tutorials/create-from-sample.md) .

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Aboneliğinizde şema tanımını oluşturmak için aşağıdaki CLI komutunu kullanın:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Bu şablonu PowerShell ile dağıtmak için şunu kullanın:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Erişim denetimi

Rol atama hakkında bilgi edinmek için bkz. [Azure Resource Manager şablonları kullanarak Azure rol atamaları ekleme](../../role-based-access-control/role-assignments-template.md).

Aşağıdaki örnek, bir kaynak grubu oluşturur, buna bir kilit uygular ve bir sorumlusu bir rol atar.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Sonraki adımlar

* Azure Güvenlik Merkezi için çalışma alanı ayarlarını dağıtmaya ilişkin bir örnek için bkz. [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Örnek Şablonlar [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments)' da bulunabilir.
* Ayrıca, şablonları [Yönetim grubu düzeyinde](deploy-to-management-group.md) ve [kiracı düzeyinde](deploy-to-tenant.md)dağıtabilirsiniz.

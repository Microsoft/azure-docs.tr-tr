---
title: Kaynakları aboneliğe dağıtma
description: Azure Resource Manager şablonunda bir kaynak grubu oluşturmayı açıklar. Ayrıca Azure abonelik kapsamındaki kaynakların nasıl dağıtılacağını gösterir.
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: d8a99a19038f98766df25551343a48b9789c8587
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147135"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma

Genellikle Azure kaynaklarını Azure aboneliğinizdeki bir kaynak grubuna dağıtırsınız. Ancak, abonelik düzeyinde kaynaklar da oluşturabilirsiniz. Bu düzeyde, kaynak grupları oluşturma veya [rol tabanlı erişim denetimi](../role-based-access-control/overview.md)atama gibi işlemler gerçekleştirmek için abonelik düzeyi dağıtımlarını kullanırsınız.

Şablonları abonelik düzeyinde dağıtmak için Azure CLı, PowerShell veya REST API kullanın. Azure portal, abonelik düzeyinde dağıtımı desteklemez.

## <a name="supported-resources"></a>Desteklenen kaynaklar

Aşağıdaki kaynak türlerini abonelik düzeyinde dağıtabilirsiniz:

* [dağıtımlar](/azure/templates/microsoft.resources/deployments)
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [Poliyasatamaları](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Şema

Abonelik düzeyi dağıtımlar için kullandığınız şema, kaynak grubu dağıtımları için şemadan farklıdır.

Şablonlar için şunu kullanın:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Parametre dosyaları için şunu kullanın:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Dağıtım komutları

Abonelik düzeyi dağıtımlara yönelik komutlar, kaynak grubu dağıtımları için komutlardan farklıdır.

Azure CLı için [az Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)kullanın. Aşağıdaki örnek, bir kaynak grubu oluşturmak için bir şablon dağıtır:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


PowerShell dağıtım komutu için [New-AzDeployment](/powershell/module/az.resources/new-azdeployment)kullanın. Aşağıdaki örnek, bir kaynak grubu oluşturmak için bir şablon dağıtır:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

REST API için [dağıtımlar-abonelik kapsamında oluştur](/rest/api/resources/deployments/createorupdateatsubscriptionscope)' u kullanın.

## <a name="deployment-location-and-name"></a>Dağıtım konumu ve adı

Abonelik düzeyindeki dağıtımlar için, dağıtım için bir konum sağlamanız gerekir. Dağıtımın konumu, dağıttığınız kaynakların konumundan ayrıdır. Dağıtım konumu, dağıtım verilerinin depolanacağı konumu belirtir.

Dağıtım için bir ad verebilir veya varsayılan dağıtım adını kullanabilirsiniz. Varsayılan ad şablon dosyasının adıdır. Örneğin, **azuredeploy. JSON** adlı bir şablon dağıtmak, **azuredeploy**varsayılan dağıtım adını oluşturur.

Her dağıtım adı için konum sabittir. Farklı bir konumda aynı ada sahip mevcut bir dağıtım olduğunda tek bir konumda dağıtım oluşturamazsınız. `InvalidDeploymentLocation`hata kodunu alırsanız, bu adın önceki dağıtımıyla farklı bir ad veya aynı konumu kullanın.

## <a name="use-template-functions"></a>Şablon işlevlerini kullanma

Abonelik düzeyindeki dağıtımlar için, Şablon işlevleri kullanılırken bazı önemli noktalar vardır:

* [ResourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) **işlevi desteklenmiyor.**
* [RESOURCEID ()](resource-group-template-functions-resource.md#resourceid) işlevi desteklenir. Abonelik düzeyi dağıtımlarında kullanılan kaynakların kaynak KIMLIĞINI almak için kullanın. Örneğin, `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`bir ilke tanımının kaynak KIMLIĞINI alın. Ya da, abonelik düzeyi kaynağı için kaynak KIMLIĞINI almak üzere [Subscriptionresourceıd ()](resource-group-template-functions-resource.md#subscriptionresourceid) işlevini kullanın.
* [Reference ()](resource-group-template-functions-resource.md#reference) ve [List ()](resource-group-template-functions-resource.md#list) işlevleri desteklenir.

## <a name="create-resource-groups"></a>Kaynak grupları oluşturma

Azure Resource Manager şablonunda bir kaynak grubu oluşturmak için kaynak grubu için bir ad ve konum içeren bir [Microsoft. resources/resourceGroups](/azure/templates/microsoft.resources/allversions) kaynağı tanımlayın. Kaynak grubu oluşturabilir ve aynı şablondaki kaynak grubuna kaynak dağıtabilirsiniz.

Aşağıdaki şablon boş bir kaynak grubu oluşturur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Birden fazla kaynak grubu oluşturmak için kaynak gruplarıyla [Kopyala öğesini](resource-group-create-multiple.md) kullanın. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
            "apiVersion": "2018-05-01",
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

Kaynak yinelemesi hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonlarındaki bir kaynak veya özelliğin birden fazla örneğini dağıtma](./resource-group-create-multiple.md)ve [öğretici: Kaynak Yöneticisi şablonlarla birden çok kaynak örneği oluşturma](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Kaynak grubu ve kaynaklar

Kaynak grubu oluşturmak ve kaynaklarına kaynak dağıtmak için, iç içe geçmiş bir şablon kullanın. İç içe şablon, kaynak grubuna dağıtılacak kaynakları tanımlar. Kaynak grubunun kaynakları dağıtımdan önce mevcut olduğundan emin olmak için, iç içe geçmiş şablonu kaynak grubuna bağımlı olarak ayarlayın.

Aşağıdaki örnek, bir kaynak grubu oluşturur ve kaynak grubuna bir depolama hesabı dağıtır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
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

## <a name="create-policies"></a>İlkeleri oluşturma

### <a name="assign-policy"></a>İlke ata

Aşağıdaki örnek, aboneliğe var olan bir ilke tanımını atar. İlke parametreleri alırsa, bunları bir nesne olarak sağlayın. İlke parametre almadıysanız varsayılan boş nesneyi kullanın.

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
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
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
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Bu şablonu PowerShell ile dağıtmak için şunu kullanın:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>İlke tanımlama ve atama

Aynı şablonda bir ilke [tanımlayabilir](../governance/policy/concepts/definition-structure.md) ve atayabilirsiniz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
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
            "name": "location-lock",
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Aboneliğinizde ilke tanımı oluşturmak ve bunu aboneliğe uygulamak için aşağıdaki CLı komutunu kullanın:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Bu şablonu PowerShell ile dağıtmak için şunu kullanın:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>Sonraki adımlar

* Rol atama hakkında bilgi edinmek için bkz. [RBAC ve Azure Resource Manager şablonlarını kullanarak Azure kaynaklarına erişimi yönetme](../role-based-access-control/role-assignments-template.md).
* Azure Güvenlik Merkezi için çalışma alanı ayarlarını dağıtmaya ilişkin bir örnek için bkz. [Deployascwithworkspace Settings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Örnek Şablonlar [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments)' da bulunabilir.
* Azure Resource Manager şablonları oluşturma hakkında bilgi edinmek için bkz. [yazma şablonları](resource-group-authoring-templates.md). 
* Bir şablondaki kullanılabilir işlevlerin listesi için bkz. [Şablon işlevleri](resource-group-template-functions.md).

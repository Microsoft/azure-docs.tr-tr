---
title: Azure Resource Manager şablonları kullanarak Azure rolleri atama-Azure RBAC
description: Azure Resource Manager şablonları ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak kullanıcılar, gruplar, hizmet sorumluları veya yönetilen kimlikler için Azure kaynaklarına nasıl erişim sağlayacağınızı öğrenin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: ba1df23b40de82a8ef901541884ef29ea0b504a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771884"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak Azure rolleri atama

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Azure PowerShell veya Azure CLı kullanmaya ek olarak, [Azure Resource Manager şablonları](../azure-resource-manager/templates/template-syntax.md)kullanarak roller atayabilirsiniz. Kaynakları sürekli ve sürekli olarak dağıtmanız gerektiğinde şablonlar yararlı olabilir. Bu makalede, şablonlar kullanılarak rollerin nasıl atanacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>Nesne kimliklerini al

Rol atamak için, rolü atamak istediğiniz kullanıcı, Grup veya uygulamanın KIMLIĞINI belirtmeniz gerekir. KIMLIK şu biçimdedir: `11111111-1111-1111-1111-111111111111` . Azure portal, Azure PowerShell veya Azure CLı kullanarak KIMLIĞI edinebilirsiniz.

### <a name="user"></a>Kullanıcı

Bir kullanıcının KIMLIĞINI almak için [Get-AzADUser](/powershell/module/az.resources/get-azaduser) veya [az ad User Show](/cli/azure/ad/user#az_ad_user_show) komutlarını kullanabilirsiniz.

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Grup

Bir grubun KIMLIĞINI almak için [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) veya [az Ad Group Show](/cli/azure/ad/group#az_ad_group_show) komutlarını kullanabilirsiniz.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Yönetilen kimlikler

Yönetilen kimliğin KIMLIĞINI almak için [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) veya [az ad SP](/cli/azure/ad/sp) komutlarını kullanabilirsiniz.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Uygulama

Bir hizmet sorumlusunun KIMLIĞINI (bir uygulama tarafından kullanılan kimlik) almak için [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) veya [az ad SP List](/cli/azure/ad/sp#az_ad_sp_list) komutlarını kullanabilirsiniz. Hizmet sorumlusu için uygulama KIMLIĞINI **değil** , nesne kimliğini kullanın.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Azure rolü atama

Azure RBAC 'de, erişim izni vermek için bir rol atarsınız.

### <a name="resource-group-scope-without-parameters"></a>Kaynak grubu kapsamı (parametresiz)

Aşağıdaki şablonda rol atama için temel bir yol gösterilmektedir. Bazı değerler şablon içinde belirtilmiştir. Aşağıdaki şablonda şunları gösterilmektedir:

-  Bir kaynak grubu kapsamındaki Kullanıcı, Grup veya uygulamaya [okuyucu](built-in-roles.md#reader) rolü atama

Şablonu kullanmak için aşağıdakileri yapmanız gerekir:

- Yeni bir JSON dosyası oluşturun ve şablonu kopyalayın
- `<your-principal-id>`Rolün atanacağı bir kullanıcının, grubun, yönetilen kimliğin veya UYGULAMANıN kimliğiyle değiştirin

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Örnek [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ve [az dağıtım grubu oluşturma](/cli/azure/deployment/group#az_deployment_group_create) komutları aşağıda verilmiştir grup adlı bir kaynak grubunda dağıtımı başlatmak için kullanılır.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Aşağıda, şablonu dağıttıktan sonra bir kaynak grubu için kullanıcıya okuyucu rolü atamasının bir örneği gösterilmektedir.

![Kaynak grubu kapsamında rol ataması](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Kaynak grubu veya abonelik kapsamı

Önceki şablon çok esnek değildir. Aşağıdaki şablon parametreleri kullanır ve farklı kapsamlarda kullanılabilir. Aşağıdaki şablonda şunları gösterilmektedir:

- Bir kaynak grubunda veya abonelik kapsamında bir Kullanıcı, Grup veya uygulamaya rol atama
- Sahip, katkıda bulunan ve okuyucu rollerini parametre olarak belirtme

Şablonu kullanmak için aşağıdaki girişleri belirtmeniz gerekir:

- Rolün atanacağı bir kullanıcının, grubun, yönetilen kimliğin veya uygulamanın KIMLIĞI
- Rol ataması için kullanılacak benzersiz bir KIMLIK veya varsayılan KIMLIĞI kullanabilirsiniz

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Bu şablon, `roleNameGuid` şablon dağıtımı için bir parametre olarak aynı değer sağlanmamışsa ıdempotent değildir. Hayır `roleNameGuid` sağlanmazsa, varsayılan olarak her dağıtımda yeni BIR GUID oluşturulur ve sonraki dağıtımlar hata vererek başarısız olur `Conflict: RoleAssignmentExists` .

Rol atamasının kapsamı, dağıtımın düzeyinden belirlenir. Örnek [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ve [az dağıtım grubu oluşturma](/cli/azure/deployment/group#az_deployment_group_create) komutları bir kaynak grubu kapsamında nasıl başlayaöğreneceksiniz?.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Aşağıda örnek [New-azdeployment](/powershell/module/az.resources/new-azdeployment) ve bir abonelik kapsamında dağıtımın başlatılması ve konumun belirtilmesi için [az dağıtım alt oluşturma](/cli/azure/deployment/sub#az_deployment_sub_create) komutları verilmiştir.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Kaynak kapsamı

Bir kaynak düzeyinde bir rol atamanız gerekiyorsa, `scope` rol atamasında özelliğini kaynağın adına ayarlayın.

Aşağıdaki şablonda şunları gösterilmektedir:

- Yeni depolama hesabı oluşturma
- Depolama hesabı kapsamındaki bir Kullanıcı, Grup veya uygulamaya rol atama
- Sahip, katkıda bulunan ve okuyucu rollerini parametre olarak belirtme

Şablonu kullanmak için aşağıdaki girişleri belirtmeniz gerekir:

- Rolün atanacağı bir kullanıcının, grubun, yönetilen kimliğin veya uygulamanın KIMLIĞI

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Önceki şablonu dağıtmak için kaynak grubu komutlarını kullanırsınız. Aşağıda örnek [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ve dağıtım [grubu oluşturma](/cli/azure/deployment/group#az_deployment_group_create) komutları bir kaynak kapsamında başlatılır.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Aşağıda, şablonu dağıttıktan sonra bir depolama hesabı için kullanıcıya katkıda bulunan rol atamasının bir örneği gösterilmektedir.

![Kaynak kapsamında rol ataması](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Yeni hizmet sorumlusu

Yeni bir hizmet sorumlusu oluşturur ve bu hizmet sorumlusuna hemen bir rol atamayı denerseniz, bu rol ataması bazı durumlarda başarısız olabilir. Örneğin, yeni bir yönetilen kimlik oluşturup aynı Azure Resource Manager şablonunda bu hizmet sorumlusuna bir rol atamayı denerseniz, rol ataması başarısız olabilir. Bu hatanın nedeni büyük olasılıkla çoğaltma gecikmesi. Hizmet sorumlusu tek bir bölgede oluşturulur; Ancak, rol ataması henüz hizmet sorumlusunu çoğaltılmamış farklı bir bölgede gerçekleşebilir.

Bu senaryoya yönelik olarak, `principalType` rol atamasını oluştururken özelliğini olarak ayarlamanız gerekir `ServicePrincipal` . `apiVersion`Rol atamasının öğesini `2018-09-01-preview` veya daha sonra da ayarlamanız gerekir.

Aşağıdaki şablonda şunları gösterilmektedir:

- Yeni bir yönetilen kimlik hizmeti sorumlusu oluşturma
- Şunu belirtme `principalType`
- Kaynak grubu kapsamındaki bu hizmet sorumlusuna katkıda bulunan rolü atama

Şablonu kullanmak için aşağıdaki girişleri belirtmeniz gerekir:

- Yönetilen kimliğin temel adı veya varsayılan dizeyi kullanabilirsiniz

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Örnek [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ve [az dağıtım grubu oluşturma](/cli/azure/deployment/group#az_deployment_group_create) komutları bir kaynak grubu kapsamında nasıl başlayaöğreneceksiniz?.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Aşağıda, şablonu dağıttıktan sonra yeni bir yönetilen kimlik hizmeti sorumlusuna katkıda bulunan rolü atamasının bir örneği gösterilmektedir.

![Yeni yönetilen kimlik hizmeti sorumlusu için rol ataması](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Azure portal kullanarak ARM şablonları oluşturma ve dağıtma](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [ARM şablonlarının yapısını ve söz dizimini anlama](../azure-resource-manager/templates/template-syntax.md)
- [Abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure Hızlı Başlangıç Şablonları](https://azure.microsoft.com/resources/templates/?term=rbac)

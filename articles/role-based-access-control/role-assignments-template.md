---
title: RBAC ve Azure Resource Manager şablonlarını kullanarak Azure kaynaklarına erişimi yönetme | Microsoft Docs
description: Rol tabanlı erişim denetimi (RBAC) ve Azure Resource Manager şablonları kullanarak kullanıcılar, gruplar ve uygulamalar için Azure kaynaklarına erişimi yönetmeyi öğrenin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5f57ea658df0569c4e69e476513863abe6940471
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692899"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>RBAC ve Azure Resource Manager şablonlarını kullanarak Azure kaynaklarına erişimi yönetme

[Rol tabanlı erişim denetimi (RBAC)](overview.md) , Azure kaynaklarına erişimi yönetme yöntemidir. Azure PowerShell veya Azure CLı kullanmanın yanı sıra, [Azure Resource Manager şablonlarını](../azure-resource-manager/resource-group-authoring-templates.md)kullanarak Azure kaynaklarına erişimi de yönetebilirsiniz. Kaynakları sürekli ve sürekli olarak dağıtmanız gerektiğinde şablonlar yararlı olabilir. Bu makalede, RBAC ve şablonlar kullanarak erişimi nasıl yönetebileceğinizi anlatmaktadır.

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>Kaynak grubu kapsamında (parametresiz) bir rol ataması oluşturma

RBAC'de erişim vermek için bir rol ataması oluşturmanız gerekir. Aşağıdaki şablonda rol ataması oluşturmanın temel bir yolu gösterilmektedir. Bazı değerler şablon içinde belirtilmiştir. Aşağıdaki şablonda şunları gösterilmektedir:

-  Bir kaynak grubu kapsamındaki Kullanıcı, Grup veya uygulamaya [okuyucu](built-in-roles.md#reader) rolü atama

Şablonu kullanmak için aşağıdakileri yapmanız gerekir:

- Yeni bir JSON dosyası oluşturun ve şablonu kopyalayın
- @No__t_0, rolün atanacağı bir Kullanıcı, Grup veya uygulamanın benzersiz tanımlayıcısı ile değiştirin. Tanımlayıcı şu biçimdedir: `11111111-1111-1111-1111-111111111111`

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

Örnek [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ve [az Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) komutları, örneğin examplegroup adlı bir kaynak grubunda dağıtımı başlatma komutlarını aşağıda bulabilirsiniz.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Aşağıda, şablonu dağıttıktan sonra bir kaynak grubu için kullanıcıya okuyucu rolü atamasının bir örneği gösterilmektedir.

![Kaynak grubu kapsamında rol ataması](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>Kaynak grubunda veya abonelik kapsamında rol ataması oluşturma

Önceki şablon çok esnek değildir. Aşağıdaki şablon parametreleri kullanır ve farklı kapsamlarda kullanılabilir. Aşağıdaki şablonda şunları gösterilmektedir:

- Bir kaynak grubunda veya abonelik kapsamında bir Kullanıcı, Grup veya uygulamaya rol atama
- Sahip, katkıda bulunan ve okuyucu rollerini parametre olarak belirtme

Şablonu kullanmak için aşağıdaki girişleri belirtmeniz gerekir:

- Rolün atanacağı bir kullanıcının, grubun veya uygulamanın benzersiz tanımlayıcısı
- Atanacak rol
- Rol ataması için kullanılacak benzersiz bir tanımlayıcı ya da varsayılan tanımlayıcıyı kullanabilirsiniz

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

Rolü atamak üzere bir kullanıcının benzersiz tanımlayıcısını almak için [Get-AzADUser](/powershell/module/az.resources/get-azaduser) veya [az ad User Show](/cli/azure/ad/user#az-ad-user-show) komutlarını kullanabilirsiniz.

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

Rol atamasının kapsamı, dağıtımın düzeyinden belirlenir. Örnek [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ve bir kaynak grubu kapsamında dağıtımın nasıl başlatılacağı hakkında daha [az grup dağıtımı oluşturma](/cli/azure/group/deployment#az-group-deployment-create) komutları aşağıda verilmiştir.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

Aşağıda örnek [New-azdeployment](/powershell/module/az.resources/new-azdeployment) ve dağıtım [oluşturma](/cli/azure/deployment#az-deployment-create) komutları bir abonelik kapsamında başlatılır ve konumu belirtebilirsiniz.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

> [!NOTE]
> Bu şablon, şablon dağıtımı için bir parametre olarak aynı `roleNameGuid` değeri sağlanmamışsa ıdempotent değildir. @No__t_0 sağlanmazsa, varsayılan olarak her dağıtımda yeni bir GUID oluşturulur ve sonraki dağıtımlar `Conflict: RoleAssignmentExists` hata ile başarısız olur.

## <a name="create-a-role-assignment-at-a-resource-scope"></a>Kaynak kapsamında rol ataması oluşturma

Bir kaynak düzeyinde bir rol ataması oluşturmanız gerekiyorsa, rol atamasının biçimi farklıdır. Rolün atanacağı kaynağın kaynak sağlayıcısı ad alanını ve kaynak türünü sağlarsınız. Ayrıca, rol atamasının adına kaynağın adını da dahil edersiniz.

Rol atamasının türü ve adı için aşağıdaki biçimi kullanın:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Aşağıdaki şablonda şunları gösterilmektedir:

- Yeni depolama hesabı oluşturma
- Depolama hesabı kapsamındaki bir Kullanıcı, Grup veya uygulamaya rol atama
- Sahip, katkıda bulunan ve okuyucu rollerini parametre olarak belirtme

Şablonu kullanmak için aşağıdaki girişleri belirtmeniz gerekir:

- Rolün atanacağı bir kullanıcının, grubun veya uygulamanın benzersiz tanımlayıcısı
- Atanacak rol

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
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
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

Önceki şablonu dağıtmak için kaynak grubu komutlarını kullanırsınız. Örnek [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ve bir kaynak kapsamında dağıtımın başlatılması için [az Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) komutları aşağıda verilmiştir.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

Aşağıda, şablonu dağıttıktan sonra bir depolama hesabı için kullanıcıya katkıda bulunan rol atamasının bir örneği gösterilmektedir.

![Kaynak kapsamında rol ataması](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>Yeni hizmet sorumlusu için rol ataması oluşturma

Yeni bir hizmet sorumlusu oluşturur ve bu hizmet sorumlusuna hemen bir rol atamayı denerseniz, bu rol ataması bazı durumlarda başarısız olabilir. Örneğin, yeni bir yönetilen kimlik oluşturup aynı Azure Resource Manager şablonunda bu hizmet sorumlusuna bir rol atamayı denerseniz, rol ataması başarısız olabilir. Bu hatanın nedeni büyük olasılıkla çoğaltma gecikmesi. Hizmet sorumlusu tek bir bölgede oluşturulur; Ancak, rol ataması henüz hizmet sorumlusunu çoğaltılmamış farklı bir bölgede gerçekleşebilir. Bu senaryoya yönelik olarak, rol atamasını oluştururken `principalType` özelliğini `ServicePrincipal` olarak ayarlamanız gerekir.

Aşağıdaki şablonda şunları gösterilmektedir:

- Yeni bir yönetilen kimlik hizmeti sorumlusu oluşturma
- @No__t_0 belirtme
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
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Örnek [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ve bir kaynak grubu kapsamında dağıtımın nasıl başlatılacağı hakkında daha [az grup dağıtımı oluşturma](/cli/azure/group/deployment#az-group-deployment-create) komutları aşağıda verilmiştir.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Aşağıda, şablonu dağıttıktan sonra yeni bir yönetilen kimlik hizmeti sorumlusuna katkıda bulunan rolü atamasının bir örneği gösterilmektedir.

![Yeni yönetilen kimlik hizmeti sorumlusu için rol ataması](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Azure portalı kullanarak Azure Resource Manager şablonu oluşturma ve dağıtma](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Azure Resource Manager şablonlarının yapısını ve söz dizimini anlama](../azure-resource-manager/resource-group-authoring-templates.md)
- [Abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](../azure-resource-manager/deploy-to-subscription.md)
- [Azure Hızlı Başlangıç Şablonları](https://azure.microsoft.com/resources/templates/?term=rbac)

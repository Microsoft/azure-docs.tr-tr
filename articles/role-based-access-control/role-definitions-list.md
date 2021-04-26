---
title: Azure rol tanımlarını listeleme-Azure RBAC
description: Azure portal, Azure PowerShell, Azure CLı veya REST API kullanarak Azure yerleşik ve özel rolleri nasıl listeleyeceğinizi öğrenin.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/26/2021
ms.author: rolyon
ms.openlocfilehash: b285755d24cdbf1f8ef06eb850fc218a00734f16
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771740"
---
# <a name="list-azure-role-definitions"></a>Azure rol tanımlarını listeleyin

Rol tanımı, okuma, yazma ve silme gibi gerçekleştirilebileceği izinlerin bir koleksiyonudur. Genellikle bir rol olarak adlandırılır. [Azure rol tabanlı erişim denetimi (Azure RBAC)](overview.md) , 120 [yerleşik rolüne](built-in-roles.md) sahiptir veya kendi özel rollerinizi oluşturabilirsiniz. Bu makalede, Azure kaynaklarına erişim izni vermek için kullanabileceğiniz yerleşik ve özel rollerin nasıl listeleneceğini açıklanmaktadır.

Azure Active Directory için Yönetici rollerinin listesini görmek için, [Azure Active Directory Içindeki yönetici rolü izinleri](../active-directory/roles/permissions-reference.md)bölümüne bakın.

## <a name="azure-portal"></a>Azure portalı

### <a name="list-all-roles"></a>Tüm rolleri Listele

Azure portal tüm rolleri listelemek için aşağıdaki adımları izleyin.

Güncelleştirilmiş bir rol deneyimini görüntülemeyi ilgileniyorsanız, şu anda genel önizleme aşamasında olan **Roller (Önizleme)** sekmesine göz atın. **Roller (Önizleme)** sekmesi, bazı ek özelliklerle **Roller sekmesi olarak** aynı rol listesini görüntüler. Rolleriniz ile çalışmak için roller sekmesini kullanabilirsiniz, ancak özel roller oluşturur veya silerseniz, en son değişiklikleri görmek için sayfayı el ile yenilemeniz gerekebilir.

#### <a name="roles"></a>[Roller](#tab/roles/)

1. Azure portal, **tüm hizmetler** ' e tıklayın ve ardından herhangi bir kapsamı seçin. Örneğin, **Yönetim grupları**, **abonelikler**, **kaynak grupları** veya bir kaynak seçebilirsiniz.

1. Belirli kaynağa tıklayın.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. Tüm yerleşik ve özel rollerin listesini görmek için **Roller** sekmesine tıklayın.

   Geçerli kapsamdaki her bir role atanan kullanıcı ve grup sayısını görebilirsiniz.

   ![Rolleri listesi](./media/role-definitions-list/roles-list-current.png)

#### <a name="roles-preview"></a>[Roller (Önizleme)](#tab/roles-preview/)

1. Azure portal, **tüm hizmetler** ' e tıklayın ve ardından herhangi bir kapsamı seçin. Örneğin, **Yönetim grupları**, **abonelikler**, **kaynak grupları** veya bir kaynak seçebilirsiniz.

1. Belirli kaynağa tıklayın.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. Tüm yerleşik ve özel rollerin listesini görmek için **Roller (Önizleme)** sekmesine tıklayın.

   ![Önizleme deneyimini kullanarak rol listesi](./media/role-definitions-list/roles-list.png)

1. Belirli bir rolün izinlerini görmek için, **Ayrıntılar** sütununda **Görünüm** bağlantısına tıklayın.

    İzinler bölmesi görüntülenir.

1. Seçili rolün izinlerini görüntülemek ve aramak için **izinler** sekmesine tıklayın.

   ![Önizleme deneyimini kullanarak rol izinleri](./media/role-definitions-list/role-permissions.png)

---

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Tüm rolleri Listele

Azure PowerShell tüm rolleri listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)komutunu kullanın.

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Rol tanımı listeleme

Belirli bir rolün ayrıntılarını listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)komutunu kullanın.

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>JSON biçiminde bir rol tanımı listeleme

JSON biçiminde bir rol listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)komutunu kullanın.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Rol tanımının listeleme izinleri

Belirli bir rolün izinlerini listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)komutunu kullanın.

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>Tüm rolleri Listele

Azure CLı 'deki tüm rolleri listelemek için [az role Definition List](/cli/azure/role/definition#az_role_definition_list)kullanın.

```azurecli
az role definition list
```

Aşağıdaki örnekte, tüm kullanılabilir rol tanımlarının adı ve açıklaması listelenmektedir:

```azurecli
az role definition list --output json --query '[].{roleName:roleName, description:description}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role"
  },

  ...

]
```

Aşağıdaki örnek tüm yerleşik rolleri listeler.

```azurecli
az role definition list --custom-role-only false --output json --query '[].{roleName:roleName, description:description, roleType:roleType}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role",
    "roleType": "BuiltInRole"
  },
  
  ...

]
```

### <a name="list-a-role-definition"></a>Rol tanımı listeleme

Rolün ayrıntılarını listelemek için [az role Definition List](/cli/azure/role/definition#az_role_definition_list)kullanın.

```azurecli
az role definition list --name {roleName}
```

Aşağıdaki örnek, *katkıda bulunan* rol tanımını listeler:

```azurecli
az role definition list --name "Contributor"
```

```json
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action",
          "Microsoft.Blueprint/blueprintAssignments/write",
          "Microsoft.Blueprint/blueprintAssignments/delete"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>Rol tanımının listeleme izinleri

Aşağıdaki örnek, *katkıda bulunan* rolünün yalnızca *eylemlerini* ve *notActions* listeler.

```azurecli
az role definition list --name "Contributor" --output json --query '[].{actions:permissions[0].actions, notActions:permissions[0].notActions}'
```

```json
[
  {
    "actions": [
      "*"
    ],
    "notActions": [
      "Microsoft.Authorization/*/Delete",
      "Microsoft.Authorization/*/Write",
      "Microsoft.Authorization/elevateAccess/Action",
      "Microsoft.Blueprint/blueprintAssignments/write",
      "Microsoft.Blueprint/blueprintAssignments/delete"
    ]
  }
]
```

Aşağıdaki örnek yalnızca *sanal makine katılımcısı* rolünün eylemlerini listeler.

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/locations/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/virtualMachineScaleSets/*",
    "Microsoft.Compute/disks/write",
    "Microsoft.Compute/disks/read",
    "Microsoft.Compute/disks/delete",
    "Microsoft.DevTestLab/schedules/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
    "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

    ...

    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Storage/storageAccounts/read",
    "Microsoft.Support/*"
  ]
]
```

## <a name="rest-api"></a>REST API

### <a name="list-role-definitions"></a>Rol tanımlarını listeleme

Rol tanımlarını listelemek için, [rol tanımları-liste](/rest/api/authorization/roledefinitions/list) REST API kullanın. Sonuçlarınızı iyileştirmek için bir kapsam ve isteğe bağlı bir filtre belirtirsiniz.

1. Aşağıdaki istekle başlayın:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. URI içinde, *{scope}* değerini rol tanımlarını listelemek istediğiniz kapsamla değiştirin.

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Kaynak grubu |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Kaynak |

    Önceki örnekte, Microsoft. Web bir App Service örneğine başvuran bir kaynak sağlayıcıdır. Benzer şekilde, diğer herhangi bir kaynak sağlayıcısını kullanabilir ve kapsamı belirtebilirsiniz. Daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md) ve desteklenen [Azure Kaynak sağlayıcısı işlemleri](resource-provider-operations.md).  
     
1. *{Filter}* değerini, rol tanımı listesini filtrelemek için uygulamak istediğiniz koşulla değiştirin.

    > [!div class="mx-tableFixed"]
    > | Filtre | Description |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Belirtilen kapsam ve tüm alt kapsamlar için rol tanımlarını listeler. |
    > | `$filter=type+eq+'{type}'` | Belirtilen türdeki rol tanımlarını listeler. Rol türü `CustomRole` veya olabilir `BuiltInRole` . |

Aşağıdaki istek, abonelik kapsamındaki özel rol tanımlarını listeler:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=type+eq+'CustomRole'
```

Aşağıda çıktının bir örneği gösterilmektedir:

```json
{
    "value": [
        {
            "properties": {
                "roleName": "Billing Reader Plus",
                "type": "CustomRole",
                "description": "Read billing data and download invoices",
                "assignableScopes": [
                    "/subscriptions/{subscriptionId1}"
                ],
                "permissions": [
                    {
                        "actions": [
                            "Microsoft.Authorization/*/read",
                            "Microsoft.Billing/*/read",
                            "Microsoft.Commerce/*/read",
                            "Microsoft.Consumption/*/read",
                            "Microsoft.Management/managementGroups/read",
                            "Microsoft.CostManagement/*/read",
                            "Microsoft.Billing/invoices/download/action",
                            "Microsoft.CostManagement/exports/*"
                        ],
                        "notActions": [
                            "Microsoft.CostManagement/exports/delete"
                        ]
                    }
                ],
                "createdOn": "2020-02-21T04:49:13.7679452Z",
                "updatedOn": "2020-02-21T04:49:13.7679452Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId1}",
            "type": "Microsoft.Authorization/roleDefinitions",
            "name": "{roleDefinitionId1}"
        }
    ]
}
```

### <a name="list-a-role-definition"></a>Rol tanımı listeleme

Belirli bir rolün ayrıntılarını listelemek için [rol tanımlarını kullanın-Get](/rest/api/authorization/roledefinitions/get) veya [role Definitions-kimliğe göre Al](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Aşağıdaki istekle başlayın:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    Dizin düzeyindeki bir rol tanımı için bu isteği kullanabilirsiniz:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI içinde, *{scope}* değerini rol tanımını listelemek istediğiniz kapsamla değiştirin.

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Kaynak grubu |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Kaynak |
     
1. *{Roledefinitionıd}* değerini rol tanımı tanımlayıcısı ile değiştirin.

Aşağıdaki istek [okuyucu](built-in-roles.md#reader) rol tanımını listeler:

```http
GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7?api-version=2015-07-01
```

Aşağıda çıktının bir örneği gösterilmektedir:

```json
{
    "properties": {
        "roleName": "Reader",
        "type": "BuiltInRole",
        "description": "Lets you view everything, but not make any changes.",
        "assignableScopes": [
            "/"
        ],
        "permissions": [
            {
                "actions": [
                    "*/read"
                ],
                "notActions": []
            }
        ],
        "createdOn": "2015-02-02T21:55:09.8806423Z",
        "updatedOn": "2019-02-05T21:24:35.7424745Z",
        "createdBy": null,
        "updatedBy": null
    },
    "id": "/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Yerleşik Azure rolleri](built-in-roles.md)
- [Özel Azure rolleri](custom-roles.md)
- [Azure portal kullanarak Azure rol atamalarını listeleyin](role-assignments-list-portal.md)
- [Azure portal kullanarak Azure rolleri atama](role-assignments-portal.md)

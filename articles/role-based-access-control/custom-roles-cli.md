---
title: Azure CLı kullanarak Azure özel rolleri oluşturma veya güncelleştirme-Azure RBAC
description: Azure CLı ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak Azure özel rollerini listeleme, oluşturma, güncelleştirme veya silme hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d3d05ba65e0d3918f1651c36cd17700ebf74de76
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778346"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Azure CLı kullanarak Azure özel rolleri oluşturma veya güncelleştirme

> [!IMPORTANT]
> ' Ye bir yönetim grubu eklemek `AssignableScopes` Şu anda önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure yerleşik rolleri](built-in-roles.md) , kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa, kendi özel rollerinizi de oluşturabilirsiniz. Bu makalede, Azure CLı kullanılarak özel rolleri listeleme, oluşturma, güncelleştirme veya silme işlemlerinin nasıl yapılacağı açıklanır.

Özel rol oluşturma hakkında adım adım bir öğretici için bkz. [öğretici: Azure CLI kullanarak Azure özel rolü oluşturma](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Önkoşullar

Özel Roller oluşturmak için şunlar gerekir:

- [Sahip](built-in-roles.md#owner) veya [Kullanıcı Erişimi Yöneticisi](built-in-roles.md#user-access-administrator) gibi özel rol oluşturma izni
- [Azure Cloud Shell](../cloud-shell/overview.md) veya [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Özel rolleri listeleme

Atama için kullanılabilen özel rolleri listelemek için [az role Definition List](/cli/azure/role/definition#az_role_definition_list)kullanın. Aşağıdaki örnekte, geçerli abonelikteki tüm özel roller listelenmektedir.

```azurecli
az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
```

```json
[
  {
    "roleName": "My Management Contributor",
    "type": "CustomRole"
  },
  {
    "roleName": "My Service Reader Role",
    "type": "CustomRole"
  },
  {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole"
  }
]
```

## <a name="list-a-custom-role-definition"></a>Özel bir rol tanımı listeleme

Özel bir rol tanımı listelemek için [az role Definition List](/cli/azure/role/definition#az_role_definition_list)kullanın. Bu, yerleşik bir rol için kullandığınız komuttur.

```azurecli
az role definition list --name {roleName}
```

Aşağıdaki örnek, *sanal makine operatörü* rol tanımını listeler:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Aşağıdaki örnek yalnızca *sanal makine operatörü* rolünün eylemlerini listeler:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ]
]
```

## <a name="create-a-custom-role"></a>Özel rol oluşturma

Özel bir rol oluşturmak için [az role Definition Create](/cli/azure/role/definition#az_role_definition_create)kullanın. Rol tanımı bir JSON açıklaması veya JSON açıklaması içeren bir dosyanın yolu olabilir.

```azurecli
az role definition create --role-definition {roleDefinition}
```

Aşağıdaki örnek, *sanal makine işleci* adlı özel bir rol oluşturur. Bu özel rol, *Microsoft. COMPUTE*, *Microsoft. Storage* ve *Microsoft. Network* kaynak sağlayıcılarının tüm okuma işlemlerine erişim atar ve sanal makinelere başlatma, yeniden başlatma ve izleme erişimi atar. Bu özel rol iki abonelik için kullanılabilir. Bu örnek, bir JSON dosyasını girdi olarak kullanır.

Üzerinde vmoperator.js

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Özel rolü güncelleştirme

Özel bir rolü güncelleştirmek için öncelikle rol tanımını almak üzere [az role Definition List](/cli/azure/role/definition#az_role_definition_list) öğesini kullanın. İkinci olarak, rol tanımında istenen değişiklikleri yapın. Son olarak, güncelleştirilmiş rol tanımını kaydetmek için [az role Definition Update](/cli/azure/role/definition#az_role_definition_update) kullanın.

```azurecli
az role definition update --role-definition {roleDefinition}
```

Aşağıdaki örnek, ' a *Microsoft. Insights/diagnosticSettings/* Operation öğesini ekler `Actions` ve `AssignableScopes` *sanal makine operatörü* özel rolü için öğesine bir yönetim grubu ekler. ' Ye bir yönetim grubu eklemek `AssignableScopes` Şu anda önizlemededir.

Üzerinde vmoperator.js

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Özel rolü silme

Özel bir rolü silmek için [az role Definition Delete](/cli/azure/role/definition#az_role_definition_delete)kullanın. Silinecek rolü belirtmek için rol adını veya rol KIMLIĞINI kullanın. Rol KIMLIĞINI öğrenmek için [az role Definition List](/cli/azure/role/definition#az_role_definition_list)kullanın.

```azurecli
az role definition delete --name {roleNameOrId}
```

Aşağıdaki örnek, *sanal makine operatörü* özel rolünü siler.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure CLı kullanarak Azure özel rolü oluşturma](tutorial-custom-role-cli.md)
- [Özel Azure rolleri](custom-roles.md)
- [Azure Kaynak sağlayıcısı işlemleri](resource-provider-operations.md)
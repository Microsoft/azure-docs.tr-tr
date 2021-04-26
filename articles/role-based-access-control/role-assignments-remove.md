---
title: Azure rol atamalarını kaldırma-Azure RBAC
description: Azure portal, Azure PowerShell, Azure CLı veya REST API kullanarak kullanıcılar, gruplar, hizmet sorumluları veya yönetilen kimlikler için Azure kaynaklarına erişimi kaldırmayı öğrenin.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100561762"
---
# <a name="remove-azure-role-assignments"></a>Azure rol atamalarını kaldırma

Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../articles/role-based-access-control/overview.md) , Azure kaynaklarına erişimi yönetmek için kullandığınız yetkilendirme sistemidir. Bir Azure kaynağından erişimi kaldırmak için, rol atamasını kaldırırsınız. Bu makalede, rol atamalarının Azure portal, Azure PowerShell, Azure CLı ve REST API kullanılarak nasıl kaldırılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Rol atamalarını kaldırmak için şunları yapmanız gerekir:

- `Microsoft.Authorization/roleAssignments/delete`[Kullanıcı erişimi Yöneticisi](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator) veya [sahibi](../../articles/role-based-access-control/built-in-roles.md#owner) gibi izinler

## <a name="azure-portal"></a>Azure portalı

1. Erişim **denetimi 'ni (IAM)** , erişimi kaldırmak istediğiniz yönetim grubu, abonelik, kaynak grubu veya kaynak gibi bir kapsamda açın.

1. Bu kapsamdaki tüm rol atamalarını görüntülemek için **rol atamaları** sekmesine tıklayın.

1. Rol ataması listesinde kaldırmak istediğiniz rol atamasına sahip güvenlik sorumlusunun yanına onay işareti ekleyin.

   ![Kaldırılacak rol ataması seçildi](./media/role-assignments-remove/rg-role-assignments-select.png)

1. **Kaldır**’a tıklayın.

   ![Rol atamasını kaldırma iletisi](./media/role-assignments-remove/remove-role-assignment.png)

1. Görüntülenen rol atamasını Kaldır iletisinde **Evet**' e tıklayın.

    Devralınan rol atamalarının kaldırılamadığını belirten bir ileti görürseniz, bir alt kapsamda rol atamasını kaldırmaya çalışıyorsunuz. Rolün atandığı kapsamdaki erişim denetimini (ıAM) açmanız ve yeniden denemeniz gerekir. Doğru kapsamda erişim denetimini (ıAM) açmak için hızlı bir yol, **kapsam** sütununa bakmanız ve **(devralınmış)** yanındaki bağlantıya tıklamanız.

   ![Devralınan rol atamaları için rol atama iletisini kaldır](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell, [Remove-Azroleatama](/powershell/module/az.resources/remove-azroleassignment)kullanarak bir rol atamasını kaldırırsınız.

Aşağıdaki örnek, *ilaç-Sales* kaynak grubundaki *patlong \@ contoso.com* kullanıcısının [sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolü atamasını kaldırır:

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Bir abonelik kapsamındaki KIMLIĞI 22222222-2222-2222-2222-222222222222 olan *Ann Mack ekip* grubundan [okuyucu](built-in-roles.md#reader) rolünü kaldırır.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Yönetim grubu kapsamındaki *Alain \@ example.com* kullanıcısının [faturalandırma okuyucusu](built-in-roles.md#billing-reader) rolünü kaldırır.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

"Belirtilen bilgiler bir rol atamasıyla eşlenmiyor" hata iletisini alırsanız, veya parametrelerini de belirttiğinizden emin olun `-Scope` `-ResourceGroupName` . Daha fazla bilgi için bkz. [Azure RBAC sorunlarını giderme](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="azure-cli"></a>Azure CLI’si

Azure CLı 'da, rol atamasını [az role atama Delete](/cli/azure/role/assignment#az_role_assignment_delete)kullanarak kaldırırsınız.

Aşağıdaki örnek, *ilaç-Sales* kaynak grubundaki *patlong \@ contoso.com* kullanıcısının [sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolü atamasını kaldırır:

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Bir abonelik kapsamındaki KIMLIĞI 22222222-2222-2222-2222-222222222222 olan *Ann Mack ekip* grubundan [okuyucu](built-in-roles.md#reader) rolünü kaldırır.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Yönetim grubu kapsamındaki *Alain \@ example.com* kullanıcısının [faturalandırma okuyucusu](built-in-roles.md#billing-reader) rolünü kaldırır.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>REST API

REST API rol [atamalarını-Sil](/rest/api/authorization/roleassignments/delete)' i kullanarak bir rol atamasını kaldırırsınız.

1. Rol atama tanımlayıcısını (GUID) alın. Bu tanımlayıcı, rol atamasını ilk oluşturduğunuzda döndürülür veya rol atamalarını listeleyerek alabilir.

1. Aşağıdaki istekle başlayın:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. URI içinde, *{scope}* değerini rol atamasını kaldırma kapsamı ile değiştirin.

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Kaynak grubu |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Kaynak |

1. *{Roleatamamentıd}* değerini rol atamasının GUID tanımlayıcısı ile değiştirin.

Aşağıdaki istek, abonelik kapsamında belirtilen rol atamasını kaldırır:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Aşağıda çıktının bir örneği gösterilmektedir:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>ARM şablonu

Azure Resource Manager şablonu (ARM şablonu) kullanarak rol atamasını kaldırmanın bir yolu yoktur. Rol atamasını kaldırmak için Azure portal, Azure PowerShell, Azure CLı veya REST API gibi diğer araçları kullanmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal kullanarak Azure rol atamalarını listeleyin](role-assignments-list-portal.md)
- [Azure PowerShell kullanarak Azure rol atamalarını listeleme](role-assignments-list-powershell.md)
- [Azure RBAC sorunlarını giderme](troubleshooting.md)

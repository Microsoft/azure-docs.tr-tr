---
title: 'Öğretici: Azure PowerShell Azure RBAC kullanarak Azure kaynaklarına Kullanıcı erişimi verme'
description: Bu öğreticide Azure PowerShell ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak Azure kaynaklarına Kullanıcı erişimi verme hakkında bilgi edinin.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: 45993d617028dec13c7a8b57587c7204322965cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555195"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-azure-powershell"></a>Öğretici: Azure PowerShell kullanarak Azure kaynaklarına Kullanıcı erişimi verme

Azure [rol tabanlı erişim denetimi (Azure RBAC)](overview.md) , Azure kaynaklarına erişimi yönetme yöntemidir. Bu öğreticide bir kullanıcıya bir abonelik içindeki her şeyi görüntüleme ve bir kaynak grubundaki her şeyi yönetme izni vermek için Azure PowerShell'i kullanacaksınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir kullanıcıya farklı kapsamlarda erişim izni verme
> * Erişimi listeleme
> * Erişimi kaldırma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

- Azure Active Directory'de kullanıcı oluşturma izni (veya mevcut bir kullanıcı)
- [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)

## <a name="role-assignments"></a>Rol atamaları

Azure RBAC 'de, erişim izni vermek için bir rol ataması oluşturun. Rol ataması üç öğeden oluşur: güvenlik sorumlusu, rol tanımı ve kapsam. Bu öğreticide gerçekleştireceğiniz iki rol ataması aşağıda verilmiştir:

| Güvenlik sorumlusu | Rol tanımı | Kapsam |
| --- | --- | --- |
| Kullanıcı<br>(RBAC Tutorial User) | [Okuyucu](built-in-roles.md#reader) | Abonelik |
| Kullanıcı<br>(RBAC Tutorial User)| [Katkıda Bulunan](built-in-roles.md#contributor) | Kaynak grubu<br>(rbac-tutorial-resource-group) |

   ![Bir kullanıcıya rol atama](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>Kullanıcı oluşturma

Rol atamak için kullanıcı, grup veya hizmet sorumlu gerekir. Kullanıcınız yoksa bir tane oluşturabilirsiniz.

1. Azure Cloud Shell'de parola karmaşıklık gereksinimlerinize uygun bir parola oluşturun.

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. [New-AzureADUser](/powershell/module/azuread/new-azureaduser) komutunu kullanarak etki alanınızda yeni bir kullanıcı oluşturun.

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu kapsamında rol atamasını göstermek için bir kaynak grubu kullanmanız gerekir.

1. [Get-AzLocation](/powershell/module/az.resources/get-azlocation) komutunu kullanarak bölge konumlarının bir listesini alın.

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Size yakın bir konum seçip bir değişkene atayın.

   ```azurepowershell
   $location = "westus"
   ```

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak yeni bir kaynak grubu oluşturun.

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Erişim verme

Kullanıcıya erişim izni vermek için, [New-Azroleatama](/powershell/module/az.resources/new-azroleassignment) komutunu kullanarak bir rol atamalısınız. Güvenlik sorumlusunu, rol tanımını ve kapsamı belirtmeniz gerekir.

1. [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) komutunu kullanarak aboneliğinizin kimliğini alın.

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Abonelik kapsamını bir değişkene kaydedin.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. [Okuyucu](built-in-roles.md#reader) rolünü abonelik kapsamında kullanıcıya atayın.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. [Katılımcı](built-in-roles.md#contributor) rolünü kaynak grubu kapsamında kullanıcıya atayın.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>Erişimi listeleme

1. Abonelik erişimini doğrulamak için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment) komutunu kullanarak rol atamalarını listeleyin.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Çıktıda Okuyucu rolünün abonelik kapsamında RBAC Tutorial User kullanıcısına atanmış olduğunu görebilirsiniz.

1. Kaynak grubunun erişimini doğrulamak için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment) komutunu kullanarak rol atamalarını listeleyin.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    Çıktıda hem Katılımcı hem de Okuyucu rollerinin RBAC Tutorial User kullanıcısına atanmış olduğunu görebilirsiniz. Katılımcı rolü rbac-tutorial-resource-group kapsamındadır, Okuyucu rolü ise abonelik kapsamından devralınır.

## <a name="optional-list-access-using-the-azure-portal"></a>(İsteğe bağlı) Azure Portal'ı kullanarak erişimi listeleme

1. Rol atamalarının Azure portalında nasıl göründüğünü görmek için aboneliğin **Erişim denetimi (IAM)** dikey penceresini görüntüleyin.

    ![Bir kullanıcının abonelik kapsamındaki rol atamaları](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. Kaynak grubunun **Erişim denetimi (IAM)** dikey penceresini görüntüleyin.

    ![Bir kullanıcının kaynak grubu kapsamındaki rol atamaları](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>Erişimi kaldırma

Kullanıcılar, gruplar ve uygulamalar için erişimi kaldırmak üzere, bir rol atamasını kaldırmak için [Remove-Azroleatama](/powershell/module/az.resources/remove-azroleassignment) komutunu kullanın.

1. Kullanıcının kaynak grubu kapsamındaki Katılımcı rol atamasını kaldırmak için aşağıdaki komutu kullanın.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Kullanıcının abonelik kapsamındaki Okuyucu rol atamasını kaldırmak için aşağıdaki komutu kullanın.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici ile oluşturulan kaynakları temizlemek için kaynak grubunu ve kullanıcıyı silebilirsiniz.

1. [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu silin.

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Onaylamanız istendiğinde **Y** yazın. Bu işlem birkaç saniye sürer.

1. Kullanıcıyı silmek için [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser) komutunu kullanın.

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure PowerShell kullanarak Azure rolleri atama](role-assignments-powershell.md)
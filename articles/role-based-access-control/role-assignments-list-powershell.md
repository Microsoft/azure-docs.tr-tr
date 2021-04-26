---
title: Azure PowerShell-Azure RBAC kullanarak Azure rol atamalarını listeleme
description: Azure PowerShell ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak kullanıcıların, grupların, hizmet sorumlularının veya yönetilen kimliklerin hangi kaynakların erişimi olduğunu belirlemeyi öğrenin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bf5445f6ca04e56aab466e97967a58c3e4b735a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556938"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>Azure PowerShell kullanarak Azure rol atamalarını listeleme

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Bu makalede, Azure PowerShell kullanarak rol atamalarının nasıl listeleneceğini açıklanmaktadır.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Kuruluşunuzun, [Azure tarafından yetkilendirilen kaynak yönetimi](../lighthouse/concepts/azure-delegated-resource-management.md)kullanan bir hizmet sağlayıcısına dış kaynaklı yönetim işlevleri varsa, bu hizmet sağlayıcısı tarafından yetkilendirilen rol atamaları burada gösterilmez.

## <a name="prerequisites"></a>Önkoşullar

- [Azure Cloud Shell](../cloud-shell/overview.md) veya [Azure PowerShell](/powershell/azure/install-az-ps) PowerShell

## <a name="list-role-assignments-for-the-current-subscription"></a>Geçerli abonelik için rol atamalarını listeleyin

Geçerli abonelikteki tüm rol atamalarının bir listesini almanın en kolay yolu (kök ve yönetim gruplarından devralınan rol atamaları dahil), herhangi bir parametre olmadan [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment) kullanmaktır.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Bir aboneliğin rol atamalarını listeleme

Tüm rol atamalarını bir abonelik kapsamında listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)' yı kullanın. Abonelik KIMLIĞINI almak için Azure portal **abonelikler** dikey penceresinde bulabilir veya [Get-azsubscription](/powershell/module/Az.Accounts/Get-AzSubscription)' u kullanabilirsiniz.

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Bir kullanıcının rol atamalarını listeleme

Belirtilen bir kullanıcıya atanan tüm rolleri listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)kullanın.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Belirtilen bir kullanıcıya atanan tüm rolleri ve kullanıcının ait olduğu gruplara atanan rolleri listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)kullanın.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Kaynak grubunun rol atamalarını listeleme

Bir kaynak grubu kapsamındaki tüm rol atamalarını listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)' yı kullanın.

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Bir yönetim grubu için rol atamalarını listeleyin

Bir yönetim grubu kapsamındaki tüm rol atamalarını listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)' yı kullanın. Yönetim grubu KIMLIĞINI almak için Azure portal **Yönetim grupları** dikey penceresinde bulabilir veya [Get-azmanagementgroup](/powershell/module/az.resources/get-azmanagementgroup)' yi kullanabilirsiniz.

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>Bir kaynak için rol atamalarını listeleme

Belirli bir kaynak için rol atamalarını listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment) ve `-Scope` parametresini kullanın. Kapsam, kaynağa bağlı olarak farklı olacaktır. Kapsamı almak için, `Get-AzRoleAssignment` tüm rol atamalarını listelemek ve ardından listelemek istediğiniz kapsamı bulmak için herhangi bir parametre olmadan çalıştırabilirsiniz.

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

Aşağıdaki örnek, bir depolama hesabı için rol atamalarının nasıl ekleneceğini gösterir. Bu komutun Ayrıca, bu depolama hesabı için uygulanan kaynak grupları ve abonelikler gibi daha yüksek kapsamlardaki rol atamalarını da listeleceğini unutmayın.

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

Yalnızca bir kaynakta doğrudan atanan rol atamalarını listelemek isterseniz, listeyi filtrelemek için [WHERE-Object](/powershell/module/microsoft.powershell.core/where-object) komutunu kullanabilirsiniz.

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Klasik hizmet yöneticisi ve ortak yöneticiler için rol atamalarını listeleyin

Klasik abonelik Yöneticisi ve ortak yöneticiler için rol atamalarını listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)' yı kullanın.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Yönetilen bir kimlik için rol atamalarını listeleyin

1. Sistem tarafından atanan veya Kullanıcı tarafından atanan yönetilen kimliğin nesne KIMLIĞINI alır. 

    Kullanıcı tarafından atanan yönetilen kimliğin nesne KIMLIĞINI almak için [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)komutunu kullanabilirsiniz.

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. Rol atamalarını listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)' yı kullanın.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure PowerShell kullanarak Azure rolleri atama](role-assignments-powershell.md)
---
title: Azure PowerShell-Azure RBAC kullanarak Azure reddetme atamalarını listeleme
description: Azure PowerShell ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak belirli kapsamlardaki belirli Azure Kaynak eylemlerine erişimi reddedilmiş kullanıcıları, grupları, hizmet sorumlularını ve yönetilen kimlikleri nasıl listeleyeceğinizi öğrenin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6cf379b051ba42be2d7df7d288f07cdc2a0002a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94657644"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>Azure PowerShell kullanarak Azure reddetme atamalarını listeleme

[Azure reddetme atamaları](deny-assignments.md) , bir rol ataması erişim izni veriyorsa bile kullanıcıların belirli Azure Kaynak eylemlerini gerçekleştirmesini engeller. Bu makalede Azure PowerShell kullanılarak reddedilen atamaları listeleme açıklanmaktadır.

> [!NOTE]
> Kendi reddetme atamalarınızı doğrudan oluşturamazsınız. Atamaları reddetme hakkında daha fazla bilgi için bkz. [Azure atamaları reddetme](deny-assignments.md).

## <a name="prerequisites"></a>Önkoşullar

Reddetme atama hakkında bilgi almak için, şunları yapmanız gerekir:

- `Microsoft.Authorization/denyAssignments/read`[Azure yerleşik rollerinin](built-in-roles.md) çoğunda bulunan izin
- [Azure Cloud Shell](../cloud-shell/overview.md) veya [Azure PowerShell](/powershell/azure/install-az-ps) PowerShell

## <a name="list-deny-assignments"></a>Ret atamalarını listeleme

### <a name="list-all-deny-assignments"></a>Tüm reddetme atamalarını Listele

Geçerli abonelik için tüm reddetme atamalarını listelemek için [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)kullanın.

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki atamaları reddetme listesi

Bir kaynak grubu kapsamındaki tüm reddetme atamalarını listelemek için [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)kullanın.

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Abonelik kapsamındaki atamaları reddetme listesi

Tüm reddedilen atamaları bir abonelik kapsamında listelemek için [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)kullanın. Abonelik KIMLIĞINI almak için Azure portal **abonelikler** dikey penceresinde bulabilir veya [Get-azsubscription](/powershell/module/Az.Accounts/Get-AzSubscription)' u kullanabilirsiniz.

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure reddetme atamalarını anlama](deny-assignments.md)
- [Azure portal kullanarak Azure reddetme atamalarını listeleyin](deny-assignments-portal.md)
- [REST API kullanarak Azure reddetme atamalarını listeleyin](deny-assignments-rest.md)
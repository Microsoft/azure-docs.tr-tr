---
title: RBAC ve Azure PowerShell kullanarak Azure kaynaklarına erişimi yönetme | Microsoft Docs
description: Rol tabanlı erişim denetimi (RBAC) ve Azure PowerShell kullanarak kullanıcılar, gruplar ve uygulamalar için Azure kaynaklarına erişimi yönetmeyi öğrenin. Buna erişimi listeleme, erişim verme ve erişimi kaldırma işlemleri dahildir.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5de62fd52360511fe660255dc023721a2837fe85
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819784"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>RBAC ve Azure PowerShell kullanarak Azure kaynaklarına erişimi yönetme

[Rol tabanlı erişim denetimi (RBAC)](overview.md) , Azure kaynaklarına erişimi yönetme yöntemidir. Bu makalede, RBAC ve Azure PowerShell kullanarak kullanıcılar, gruplar ve uygulamalar için erişimi nasıl yöneteceğiniz açıklanmaktadır.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Erişimi yönetmek için aşağıdakilerden birine ihtiyacınız vardır:

* [Azure Cloud Shell 'de PowerShell](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>Rolleri listeleme

### <a name="list-all-available-roles"></a>Tüm kullanılabilir rolleri listeleyin

Atama için kullanılabilir olan RBAC rollerini listelemek ve erişim izni verilecek işlemleri denetlemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)komutunu kullanın.

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

### <a name="list-a-specific-role"></a>Belirli bir rolü listeleme

Belirli bir rolü listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)komutunu kullanın.

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

## <a name="list-a-role-definition"></a>Rol tanımı listeleme

### <a name="list-a-role-definition-in-json-format"></a>JSON biçiminde bir rol tanımı listeleme

JSON biçiminde bir rol tanımı listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)komutunu kullanın.

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

### <a name="list-actions-of-a-role"></a>Rolün listeleme eylemleri

Belirli bir rolün eylemlerini listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)komutunu kullanın.

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

## <a name="list-access"></a>Erişimi listeleme

RBAC 'de, erişimi listelemek için rol atamalarını listeleyin.

### <a name="list-all-role-assignments-in-a-subscription"></a>Bir abonelikteki tüm rol atamalarını listeleme

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

### <a name="list-role-assignments-for-a-user"></a>Bir kullanıcının rol atamalarını listeleme

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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki rol atamalarını listeleme

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

### <a name="list-role-assignments-at-a-subscription-scope"></a>Abonelik kapsamındaki rol atamalarını listeleme

Tüm rol atamalarını bir abonelik kapsamında listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)' yı kullanın. Abonelik KIMLIĞINI almak için Azure portal **abonelikler** dikey penceresinde bulabilir veya [Get-azsubscription](/powershell/module/Az.Accounts/Get-AzSubscription)' u kullanabilirsiniz.

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Bir yönetim grubu kapsamındaki rol atamalarını listeleyin

Bir yönetim grubu kapsamındaki tüm rol atamalarını listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)' yı kullanın. Yönetim grubu KIMLIĞINI almak için Azure portal **Yönetim grupları** dikey penceresinde bulabilir veya [Get-azmanagementgroup](/powershell/module/az.resources/get-azmanagementgroup)' yi kullanabilirsiniz.

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Klasik hizmet yöneticisi ve ortak yöneticiler için rol atamalarını listeleyin

Klasik abonelik Yöneticisi ve ortak yöneticiler için rol atamalarını listelemek için [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment)' yı kullanın.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Erişim verme

RBAC'de erişim vermek için bir rol ataması oluşturmanız gerekir.

### <a name="search-for-object-ids"></a>Nesne kimliklerini ara

Rol atamak için, hem nesneyi (Kullanıcı, Grup, ya da uygulama) hem de kapsamı belirlemeniz gerekir.

Abonelik KIMLIĞINI almak için Azure portal **abonelikler** dikey penceresinde bulabilir veya [Get-azsubscription](/powershell/module/Az.Accounts/Get-AzSubscription)' u kullanabilirsiniz.

Bir Azure AD kullanıcısının nesne KIMLIĞINI almak için [Get-AzADUser](/powershell/module/az.resources/get-azaduser)komutunu kullanın.

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
```

Bir Azure AD grubunun nesne KIMLIĞINI almak için [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)komutunu kullanın.

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
```

Bir Azure AD hizmet sorumlusu veya uygulamasının nesne KIMLIĞINI almak için [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)komutunu kullanın.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki bir kullanıcı için rol ataması oluşturma

Bir kaynak grubu kapsamındaki bir kullanıcıya erişim sağlamak için [New-Azroleatama](/powershell/module/az.resources/new-azroleassignment)kullanın.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Benzersiz rol KIMLIĞINI kullanarak bir rol ataması oluşturma

Rol adının değişebilir birkaç zaman vardır, örneğin:

- Kendi özel rolünüzü kullanıyorsunuz ve adı değiştirmeye karar verdiniz.
- Adında **(Önizleme)** olan bir önizleme rolü kullanıyorsunuz. Rol serbest bırakıldığında, rol yeniden adlandırılır.

> [!IMPORTANT]
> Bir önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Rol yeniden adlandırılsa bile, rol KIMLIĞI değişmez. Rol atamalarınızı oluşturmak için betikler veya Otomasyon kullanıyorsanız, rol adı yerine benzersiz rol KIMLIĞINI kullanmak en iyi uygulamadır. Bu nedenle, bir rol yeniden adlandırılırsa, betiklerinizin çalışması daha olasıdır.

Rol adı yerine benzersiz rol KIMLIĞI kullanarak bir rol ataması oluşturmak için [New-Azroleatama](/powershell/module/az.resources/new-azroleassignment)kullanın.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

Aşağıdaki örnek, [sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolünü *ilaç-Sales* kaynak grubu kapsamındaki *alain@example.com* kullanıcısına atar. Benzersiz rol KIMLIĞINI almak için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ' ı kullanabilir veya [Azure kaynakları için yerleşik roller](built-in-roles.md)' e bakabilirsiniz.

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>Kaynak kapsamındaki bir grup için rol ataması oluşturma

Bir kaynak kapsamındaki bir gruba erişim sağlamak için [New-Azroleatama](/powershell/module/az.resources/new-azroleassignment)kullanın.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>Abonelik kapsamındaki bir uygulama için rol ataması oluşturma

Bir abonelik kapsamındaki bir uygulamaya erişim sağlamak için [New-Azroleatama](/powershell/module/az.resources/new-azroleassignment)kullanın.

```azurepowershell
New-AzRoleAssignment -ObjectId <application_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Yönetim grubu kapsamındaki bir kullanıcı için rol ataması oluşturma

Bir yönetim grubu kapsamındaki bir kullanıcıya erişim sağlamak için [New-Azroleatama](/powershell/module/az.resources/new-azroleassignment)' yı kullanın. Yönetim grubu KIMLIĞINI almak için Azure portal **Yönetim grupları** dikey penceresinde bulabilir veya [Get-azmanagementgroup](/powershell/module/az.resources/get-azmanagementgroup)' yi kullanabilirsiniz.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-access"></a>Erişimi kaldırma

RBAC 'de, erişimi kaldırmak için [Remove-Azroleatama](/powershell/module/az.resources/remove-azroleassignment)kullanarak bir rol atamasını kaldırırsınız.

Aşağıdaki örnek, *ilaç-Sales* kaynak grubundaki *Alain \@example. com* kullanıcısının *sanal makine katılımcısı* rolü atamasını kaldırır:

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Aşağıdaki örnek, < Role_name > rolünü bir abonelik kapsamındaki < object_id > kaldırır.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

Aşağıdaki örnek, yönetim grubu kapsamındaki < Role_name > rolünü < object_id > kaldırır.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

"Belirtilen bilgiler bir rol atamasıyla eşlenmiyor" hata iletisini alırsanız, `-Scope` veya `-ResourceGroupName` parametrelerini de belirttiğinizden emin olun. Daha fazla bilgi için bkz. [Azure kaynakları IÇIN RBAC sorunlarını giderme](troubleshooting.md#role-assignments-with-unknown-security-principal).

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: RBAC ve Azure PowerShell kullanarak Azure kaynaklarına grup erişimi verme](tutorial-role-assignments-group-powershell.md)
- [Öğretici: Azure PowerShell kullanarak Azure kaynakları için özel bir rol oluşturma](tutorial-custom-role-powershell.md)
- [Azure PowerShell ile kaynakları yönetme](../azure-resource-manager/manage-resources-powershell.md)

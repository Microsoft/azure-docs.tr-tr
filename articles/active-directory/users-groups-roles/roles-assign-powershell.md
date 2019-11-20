---
title: Azure PowerShell kullanarak özel roller atama-Azure AD | Microsoft Docs
description: Azure PowerShell ile bir Azure AD yöneticisi özel rolünün üyelerini yönetin.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025330"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Azure Active Directory 'de PowerShell kullanarak kaynak kapsamıyla özel roller atama

Bu makalede, Azure Active Directory (Azure AD) içinde kuruluş genelinde bir kapsamda rol atamasının nasıl oluşturulacağı açıklanır. Kuruluş genelindeki kapsamda rol atama, Azure AD kuruluşunda erişim sağlar. Tek bir Azure AD kaynağının kapsamıyla bir rol ataması oluşturmak için, bkz. [nasıl özel rol oluşturma ve kaynak kapsamında atama](roles-create-custom.md). Bu makale, [Azure Active Directory PowerShell sürüm 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) modülünü kullanır.

Azure AD yönetici rolleri hakkında daha fazla bilgi için, bkz. [Azure Active Directory yönetici rolleri atama](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Gerekli izinler

Rolleri atamak veya kaldırmak için bir genel yönetici hesabı kullanarak Azure AD kiracınıza bağlanın.

## <a name="prepare-powershell"></a>PowerShell 'i hazırlama

[PowerShell Galerisi](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)Azure AD PowerShell modülünü yükler. Ardından, aşağıdaki komutu kullanarak Azure AD PowerShell önizleme modülünü içeri aktarın:

``` PowerShell
import-module azureadpreview
```

Modülün kullanıma hazır olduğunu doğrulamak için, aşağıdaki komutla döndürülen sürümü burada listelenen sürüme eşleştirin:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Artık modüldeki cmdlet 'leri kullanmaya başlayabilirsiniz. Azure AD modülündeki cmdlet 'lerin tam açıklaması için bkz. [Azure AD önizleme modülü](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)için çevrimiçi başvuru belgeleri.

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Kaynak kapsamı ile bir kullanıcıya veya hizmet sorumlusuna rol atama

1. Azure AD önizleme PowerShell modülünü açın.
1. Komutu `Connect-AzureAD`çalıştırarak oturum açın.
1. Aşağıdaki PowerShell betiğini kullanarak yeni bir rol oluşturun.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Rolü bir kullanıcı yerine bir hizmet sorumlusuna atamak için, [Get-AzureADMSServicePrincipal cmdlet 'ini](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)kullanın.

## <a name="operations-on-roledefinition"></a>RoleDefinition üzerinde işlemler

Rol tanımı nesneleri, yerleşik veya özel rolün tanımını, bu rol ataması tarafından verilen izinlerle birlikte içerir. Bu kaynak hem özel rol tanımlarını hem de yerleşik directoryRoles (roleDefinition ile eşdeğer biçimde görüntülenir) görüntüler. Günümüzde, bir Azure AD kuruluşunda en fazla 30 benzersiz özel RoleDefinitions tanımlanmış olabilir.

### <a name="create-operations-on-roledefinition"></a>RoleDefinition üzerinde Işlem oluşturma

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>RoleDefinition üzerinde okuma Işlemleri

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>RoleDefinition üzerinde güncelleştirme Işlemleri

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>RoleDefinition üzerinde silme işlemleri

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Roleatama üzerinde işlemler

Rol atamaları, belirli bir güvenlik sorumlusunu (bir kullanıcı veya uygulama hizmeti sorumlusu) rol tanımına bağlayan bilgiler içerir. Gerekirse, atanan izinler için tek bir Azure AD kaynağının kapsamını ekleyebilirsiniz.  İzin kapsamını kısıtlama, yerleşik ve özel roller için desteklenir.

### <a name="create-operations-on-roleassignment"></a>Roleatama üzerinde Işlem oluşturma

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-operations-on-roleassignment"></a>Roleatama 'da okuma Işlemleri

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Roleatamasında silme Işlemleri

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD Yönetim rolleri forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)bizimle paylaşabilirsiniz.
- Roller ve Azure AD yöneticisi rol atamaları hakkında daha fazla bilgi için bkz. [yönetici rolleri atama](directory-assign-admin-roles.md).
- Varsayılan Kullanıcı izinleri için bkz. [varsayılan Konuk ve üye Kullanıcı izinlerinin karşılaştırması](../fundamentals/users-default-permissions.md).

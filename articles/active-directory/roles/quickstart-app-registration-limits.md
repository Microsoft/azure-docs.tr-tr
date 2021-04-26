---
title: Uygulama kaydı oluşturma sınırlarını kaldırma-Azure AD | Microsoft Docs
description: Azure AD Active Directory Kısıtlanmamış uygulama kayıtları vermek için özel bir rol atama
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: quickstart
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48740c52e6713735957e1ebf00e8996800eabec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015786"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Hızlı başlangıç: sınırsız uygulama kaydı oluşturmak için izin verme

Bu hızlı başlangıç kılavuzunda sınırsız sayıda uygulama kaydı oluşturma izni olan özel bir rol oluşturacak ve ardından bu rolü bir kullanıcıya atayacaksınız. Atanan Kullanıcı daha sonra uygulama kayıtları oluşturmak için Azure AD portalını, Azure AD PowerShell 'i veya Microsoft Graph API 'sini kullanabilir. Yerleşik uygulama geliştirici rolünün aksine, bu özel rol sınırsız sayıda uygulama kaydı oluşturma olanağı verir. Uygulama geliştirici rolü özelliği verir, ancak oluşturulan nesnelerin toplam sayısı, [Dizin genelinde nesne kotasına](../enterprise-users/directory-service-limits-restrictions.md)vurmasını engellemek için 250 ile sınırlıdır. Azure AD özel rollerini oluşturmak ve atamak için gereken en az ayrıcalıklı rol, ayrıcalıklı rol yöneticisidir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="create-a-custom-role-using-the-azure-ad-portal"></a>Azure AD portalını kullanarak özel bir rol oluşturma

1. Azure AD kuruluşunda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Azure Active Directory** seçin, **Roller ve yöneticiler**' i seçin ve ardından **Yeni özel rol**' i seçin.

    ![Roller ve yöneticiler sayfasından roller oluşturma veya düzenleme](./media/quickstart-app-registration-limits/new-custom-role.png)

1. **Temel bilgiler** sekmesinde, rolün adı olarak "uygulama kaydı Oluşturucusu" ve rol açıklaması için "sınırsız sayıda uygulama kaydı oluşturabilir" seçeneğini belirtin ve ardından **İleri**' yi seçin.

    ![Temel bilgiler sekmesinde özel rol için bir ad ve açıklama girin](./media/quickstart-app-registration-limits/basics-tab.png)

1. **İzinler** sekmesinde, arama kutusuna "Microsoft. Directory/Applications/Create" yazın ve ardından istediğiniz izinlerin yanındaki onay kutularını işaretleyin ve ardından **İleri**' yi seçin.

    ![Izinler sekmesinde özel bir rol için izinleri seçin](./media/quickstart-app-registration-limits/permissions-tab.png)

1. **Gözden geçir + oluştur** sekmesinde izinleri gözden geçirin ve **Oluştur**' u seçin.

### <a name="assign-the-role-in-the-azure-ad-portal"></a>Azure AD portalında rol atama

1. Azure AD kuruluşunuzda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Azure Active Directory** ' yi seçin ve ardından **Roller ve yöneticiler '** i seçin.
1. Uygulama kaydı oluşturan rolünü seçin ve **atama Ekle**' yi seçin.
1. İstediğiniz kullanıcıyı seçin ve kullanıcıyı role eklemek için **Seç** ' e tıklayın.

Bitti! Bu hızlı başlangıçta, sınırsız sayıda uygulama kaydı oluşturma izni olan özel bir rolü başarıyla oluşturdunuz ve ardından bu rolü bir kullanıcıya atayacaksınız.

> [!TIP]
> Rolü Azure AD portalını kullanarak bir uygulamaya atamak için, atama sayfasının arama kutusuna uygulamanın adını girin. Uygulamalar, varsayılan olarak listede gösterilmez, ancak arama sonuçlarında döndürülür.

### <a name="app-registration-permissions"></a>Uygulama kaydı izinleri

Her biri farklı davranışa sahip olan uygulama kayıtları oluşturmak için iki izin mevcuttur.

- Microsoft. Directory/Apps/createAsOwner: Bu iznin atanması, oluşturucunun oluşturulan uygulama kaydının ilk sahibi olarak eklenmekte ve oluşturulan uygulama kaydı, oluşturucunun 250 tarafından oluşturulan nesne kotasına göre sayılır.
- Microsoft. Directory/Applications/Create: Bu izin atama, Oluşturucu oluşturulan uygulama kaydının ilk sahibi olarak eklenmeyecektir ve oluşturulan uygulama kaydı, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılmaz. Bu izni dikkatle kullanın çünkü dizin düzeyi kotasına ulaşılana kadar atanan uygulama kaydı oluşturulmasını hiçbir şey yok. Her iki izin atanırsa, bu izin önceliklidir.

## <a name="create-a-custom-role-in-azure-ad-powershell"></a>Azure AD PowerShell 'de özel bir rol oluşturma

### <a name="prepare-powershell"></a>PowerShell 'i hazırlama

İlk olarak, [PowerShell Galerisi](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)Azure AD PowerShell modülünü yüklemeniz gerekir. Ardından, aşağıdaki komutu kullanarak Azure AD PowerShell önizleme modülünü içeri aktarın:

```powershell
Import-Module -Name AzureADPreview
```

Modülün kullanıma hazır olduğunu doğrulamak için, aşağıdaki komutla döndürülen sürümü burada listelenen sürüme eşleştirin:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role-in-azure-ad-powershell"></a>Azure AD PowerShell 'de özel rol oluşturma

Aşağıdaki PowerShell betiğini kullanarak yeni bir rol oluşturun:

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role-in-azure-ad-powershell"></a>Azure AD PowerShell 'de rol atama

Aşağıdaki PowerShell betiğini kullanarak rolü atayın:

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-in-the-microsoft-graph-api"></a>Microsoft Graph API 'sinde özel bir rol oluşturma

Özel rol oluşturmak için HTTP isteği.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Gövde

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role-in-the-microsoft-graph-api"></a>Microsoft Graph API 'de rol atama

Rol ataması bir güvenlik sorumlusu KIMLIĞINI (bir kullanıcı veya hizmet sorumlusu olabilir), bir rol tanımı (rol) KIMLIĞI ve bir Azure AD kaynak kapsamını birleştirir.

Özel bir rol atamak için HTTP isteği.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Gövde

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD Yönetim rolleri forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)bizimle paylaşabilirsiniz.
- Azure AD rol atamaları hakkında daha fazla bilgi için bkz. [yönetici rolleri atama](permissions-reference.md).
- Varsayılan Kullanıcı izinleri hakkında daha fazla bilgi için bkz. [varsayılan Konuk ve üye Kullanıcı izinlerinin karşılaştırması](../fundamentals/users-default-permissions.md).

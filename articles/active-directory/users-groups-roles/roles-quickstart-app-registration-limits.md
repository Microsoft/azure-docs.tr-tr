---
title: Uygulama kaydı oluşturma sınırlarını kaldırma-Azure AD | Microsoft Docs
description: Azure AD Active Directory Kısıtlanmamış uygulama kayıtları vermek için özel bir rol atama
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
ms.openlocfilehash: 12803e2f65e17155e8bbcaf4842789adc101b0dd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024404"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Hızlı başlangıç: sınırsız uygulama kaydı oluşturmak için izin verme

Bu hızlı başlangıçta sınırsız sayıda uygulama kaydı oluşturma izni olan özel bir rol oluşturacak ve ardından bu rolü bir kullanıcıya atayacaksınız. Atanan Kullanıcı daha sonra uygulama kayıtları oluşturmak için Azure AD portalını, Azure AD PowerShell 'i, Azure AD Graph API veya Microsoft Graph API 'sini kullanabilir. Yerleşik uygulama geliştirici rolünün aksine, bu özel rol sınırsız sayıda uygulama kaydı oluşturma olanağı verir. Uygulama geliştirici rolü özelliği verir, ancak oluşturulan nesnelerin toplam sayısı, [Dizin genelinde nesne kotasına](directory-service-limits-restrictions.md)vurmasını engellemek için 250 ile sınırlıdır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisite"></a>Önkoşul

Azure AD özel rollerini oluşturmak ve atamak için gereken en az ayrıcalıklı rol, ayrıcalıklı rol yöneticisidir.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Azure AD portalını kullanarak yeni bir özel rol oluşturma

1. Azure AD kuruluşunda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezi](https://aad.portal.azure.com) oturum açın.
1. **Azure Active Directory**seçin, **Roller ve yöneticiler**' i seçin ve ardından **Yeni özel rol**' i seçin.

    ![Roller ve yöneticiler sayfasından roller oluşturma veya düzenleme](./media/roles-create-custom/new-custom-role.png)

1. **Temel bilgiler** sekmesinde, rolün adı olarak "uygulama kaydı Oluşturucusu" ve rol açıklaması için "sınırsız sayıda uygulama kaydı oluşturabilir" seçeneğini belirtin ve ardından **İleri**' yi seçin.

    ![Temel bilgiler sekmesinde özel rol için bir ad ve açıklama girin](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. **İzinler** sekmesinde, arama kutusuna "Microsoft. Directory/Applications/Create" yazın ve ardından istediğiniz izinlerin yanındaki onay kutularını işaretleyin ve ardından **İleri**' yi seçin.

    ![Izinler sekmesinde özel bir rol için izinleri seçin](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. **Gözden geçir + oluştur** sekmesinde izinleri gözden geçirin ve **Oluştur**' u seçin.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Azure AD portalını kullanarak rolü bir kullanıcıya atama

1. Azure AD kuruluşunuzda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle  [Azure AD Yönetim Merkezi](https://aad.portal.azure.com) 'nde oturum açın.
1. **Azure Active Directory** ' yi seçin ve ardından **Roller ve yöneticiler '** i seçin.
1. Uygulama kaydı oluşturan rolünü seçin ve **atama Ekle**' yi seçin.
1. İstediğiniz kullanıcıyı seçin ve kullanıcıyı role eklemek için **Seç** ' e tıklayın.

Bitti! Bu hızlı başlangıçta, sınırsız sayıda uygulama kaydı oluşturma izni olan özel bir rolü başarıyla oluşturdunuz ve ardından bu rolü bir kullanıcıya atayacaksınız.

> [!TIP]
> Rolü Azure AD portalını kullanarak bir uygulamaya atamak için, atama sayfasının arama kutusuna uygulamanın adını girin. Uygulamalar, varsayılan olarak listede gösterilmez, ancak arama sonuçlarında döndürülür.

### <a name="app-registration-permissions"></a>Uygulama kayıt izinleri

Her biri farklı davranışa sahip olan uygulama kayıtları oluşturmak için iki izin mevcuttur.

- Microsoft. Directory/Apps/createAsOwner: Bu iznin atanması, oluşturucunun oluşturulan uygulama kaydının ilk sahibi olarak eklenmekte ve oluşturulan uygulama kaydı, oluşturucunun 250 tarafından oluşturulan nesne kotasına göre sayılır.
- Microsoft. Directory/applicationPolicies/Create: Bu izin atama, Oluşturucu oluşturulan uygulama kaydının ilk sahibi olarak eklenmeyecektir ve oluşturulan uygulama kaydı, oluşturucunun 250 tarafından oluşturulan nesneler kotasına göre sayılmaz. Bu izni dikkatle kullanın çünkü dizin düzeyi kotasına ulaşılana kadar atanan uygulama kaydı oluşturulmasını hiçbir şey yok. Her iki izin atanırsa, bu izin önceliklidir.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell kullanarak özel bir rol oluşturma

Aşağıdaki PowerShell betiğini kullanarak yeni bir rol oluşturun:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell kullanarak özel rol atama

#### <a name="prepare-powershell"></a>PowerShell 'i hazırlama

İlk olarak, [PowerShell Galerisi](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)Azure AD PowerShell modülünü yüklemeniz gerekir. Ardından, aşağıdaki komutu kullanarak Azure AD PowerShell önizleme modülünü içeri aktarın:

```powershell
import-module azureadpreview
```

Modülün kullanıma hazır olduğunu doğrulamak için, aşağıdaki komutla döndürülen sürümü burada listelenen sürüme eşleştirin:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Özel rolü atama

Aşağıdaki PowerShell betiğini kullanarak rolü atayın:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Microsoft Graph API kullanarak özel bir rol oluşturma

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
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Microsoft Graph API kullanarak özel rol atama

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
- Roller ve yönetici rolü atama hakkında daha fazla bilgi için bkz. [yönetici rolleri atama](directory-assign-admin-roles.md).
- Varsayılan Kullanıcı izinleri için bkz. [varsayılan Konuk ve üye Kullanıcı izinlerinin karşılaştırması](../fundamentals/users-default-permissions.md).

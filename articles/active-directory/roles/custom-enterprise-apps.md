---
title: Azure Active Directory 'de kurumsal uygulamaları yönetmek için özel roller oluşturma
description: Azure Active Directory 'de kurumsal uygulamalar için özel Azure AD rolleri oluşturma ve atama
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c04afe76ced0abf40abf8e30362005fb269172
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534720"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Azure Active Directory 'de kurumsal uygulamaları yönetmek için özel roller oluşturma

Bu makalede, Azure Active Directory (Azure AD) içindeki kullanıcılar ve gruplar için kurumsal uygulama atamalarını yönetme izinleri ile özel bir rolün nasıl oluşturulacağı açıklanmaktadır. Rol atamalarının öğeleri ve alt tür, izin ve özellik kümesi gibi koşulların anlamı için bkz. [özel rollere genel bakış](custom-overview.md).

## <a name="enterprise-app-role-permissions"></a>Kurumsal uygulama rolü izinleri

Bu makalede ele alınan iki kurumsal uygulama izni vardır. Tüm örnekler güncelleştirme iznini kullanır.

* Kapsamdaki Kullanıcı ve grup atamalarını okumak için `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` izin verin
* Kapsamdaki Kullanıcı ve grup atamalarını yönetmek için `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` izin verin

Güncelleştirme izninin verilmesi, Kullanıcı ve grup atamalarını kurumsal uygulamalara yönetebilmek için atanan bir sonuçlardır. Kullanıcı ve/veya grup atamalarının kapsamı, tek bir uygulama için verilebilir veya tüm uygulamalar için verilir. Kuruluş genelinde bir düzeyde verilmişse, atanan tüm uygulamalar için atamaları yönetebilir. Bir uygulama düzeyinde yapılmışsa atanan e-posta, atamaları yalnızca belirtilen uygulama için yönetebilir.

Güncelleştirme izninin verilmesi iki adımda yapılır:

1. İzne sahip özel bir rol oluşturma `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Kurumsal uygulamalara Kullanıcı ve grup atamalarını yönetmek için kullanıcılara veya gruplara izin verin. Bu, kapsamı kuruluş genelinde veya tek bir uygulamayla ayarlayabilmeniz durumunda olur.

## <a name="use-the-azure-ad-admin-center"></a>Azure AD Yönetim merkezini kullanma

### <a name="create-a-new-custom-role"></a>Yeni bir özel rol oluştur

>[!NOTE]
> Özel roller, kuruluş genelindeki bir düzeyde oluşturulur ve yönetilir ve yalnızca kuruluşun genel bakış sayfasından kullanılabilir.

1. Kuruluşunuzda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Azure Active Directory** seçin, **Roller ve yöneticiler**' i seçin ve ardından **Yeni özel rol**' i seçin.

    ![Azure AD 'de roller listesinden yeni bir özel rol ekleme](./media/custom-enterprise-apps/new-custom-role.png)

1. **Temel bilgiler** sekmesinde, rol adı için "Kullanıcı ve grup atamalarını Yönet" ve rol açıklaması için "Kullanıcı ve grup atamalarını yönetme izinleri verme" seçeneğini belirtin ve ardından **İleri**' yi seçin.

    ![Özel rol için bir ad ve açıklama girin](./media/custom-enterprise-apps/role-name-and-description.png)

1. **İzinler** sekmesinde, arama kutusuna "Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update" yazın ve ardından istediğiniz izinlerin yanındaki onay kutularını işaretleyin ve ardından **İleri**' yi seçin.

    ![Özel role izinleri ekleme](./media/custom-enterprise-apps/role-custom-permissions.png)

1. **Gözden geçir + oluştur** sekmesinde izinleri gözden geçirin ve **Oluştur**' u seçin.

    ![Artık özel rolü oluşturabilirsiniz](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Azure AD portalını kullanarak rolü bir kullanıcıya atama

1. Ayrıcalıklı rol yöneticisi rol izinleriyle [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Azure Active Directory** ' yi seçin ve ardından **Roller ve yöneticiler '** i seçin.
1. **Kullanıcı ve grup atamalarını yönetmek için Izinleri ver** rolünü seçin.

    ![Rolleri ve yöneticileri açın ve özel rolü arayın](./media/custom-enterprise-apps/select-custom-role.png)

1. **Atama Ekle**' yi seçin, istediğiniz kullanıcıyı seçin ve ardından kullanıcıya rol ataması eklemek için **Seç** ' e tıklayın.

    ![Kullanıcıya özel rol için bir atama ekleyin](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Atama ipuçları

* Tüm kurumsal uygulamalarda kuruluş genelinde kullanıcıları ve grup erişimini yönetmek üzere atanan izinleri vermek için, kuruluşunuzun Azure AD **genel bakış** sayfasında kuruluş genelindeki **Roller ve yöneticiler** listesinden başlayın.
* Belirli bir kurumsal uygulama için kullanıcıları ve grup erişimini yönetmek üzere atanan izinler vermek için, Azure AD 'de bu uygulamaya gidin ve bu uygulamanın **Roller ve yöneticiler** listesinden açın. Yeni özel rolü seçin ve Kullanıcı veya grup atamasını doldurun. Atanan kullanıcılar, yalnızca belirli bir uygulama için kullanıcıları ve grup erişimini yönetebilir.
* Özel rol atamasını test etmek için, **Kullanıcı Ekle** seçeneğinin etkin olduğunu doğrulamak üzere atanan ve bir uygulamanın **Kullanıcılar ve gruplar** sayfasını açarak oturum açın.

    ![Kullanıcı izinlerini doğrulama](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Azure AD PowerShell 'i kullanma

Daha fazla ayrıntı için bkz. [özel rol oluşturma ve atama](custom-create.md) ve [PowerShell kullanarak kaynak kapsamıyla özel roller atama](custom-assign-powershell.md).

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

### <a name="create-a-custom-role"></a>Özel rol oluşturma

Aşağıdaki PowerShell betiğini kullanarak yeni bir rol oluşturun:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Özel rolü atama

Bu PowerShell betiğini kullanarak rolü atayın.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="use-the-microsoft-graph-api"></a>Microsoft Graph API’sini kullanma

Microsoft Graph API 'sinde, belirtilen örneği kullanarak özel bir rol oluşturun. Daha fazla ayrıntı için bkz. [özel rol oluşturma ve atama](custom-create.md) ve [Microsoft Graph API kullanarak özel yönetici rolleri atama](custom-assign-graph.md).

Özel rol oluşturmak için HTTP isteği.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
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

Rol ataması bir güvenlik sorumlusu KIMLIĞINI (bir kullanıcı veya hizmet sorumlusu olabilir), bir rol tanımı KIMLIĞI ve bir Azure AD kaynak kapsamını birleştirir. Rol atamasının öğeleri hakkında daha fazla bilgi için bkz. [özel rollere genel bakış](custom-overview.md)

Özel bir rol atamak için HTTP isteği.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Kurumsal uygulamalar için kullanılabilir özel rol izinlerini keşfet](custom-enterprise-app-permissions.md)

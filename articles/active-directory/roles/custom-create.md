---
title: Azure AD rol tabanlı erişim denetiminde özel roller oluşturma | Microsoft Docs
description: Azure Active Directory kaynaklarda kaynak kapsamıyla özel Azure AD rolleri oluşturun ve atayın.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0e1543f18c18c7fdf97c39f35ba38ded658392
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007826"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Azure Active Directory özel rol oluşturma ve atama

Bu makalede, Azure Active Directory (Azure AD) içinde yeni özel roller oluşturma açıklanır. Özel rollerin temelleri için bkz. [özel rollere genel bakış](custom-overview.md). Rol, yalnızca dizin düzeyindeki kapsamda veya yalnızca bir uygulama kayıt kaynağı kapsamında atanabilir.

Özel roller, Azure AD Genel Bakış sayfasındaki [Roller ve yöneticiler](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) sekmesinde oluşturulabilir.

## <a name="create-a-role-in-the-azure-portal"></a>Azure portal rol oluşturma

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Uygulama kayıtlarını yönetmek için erişim izni vermek üzere yeni bir özel rol oluşturun

1. Azure AD kuruluşunda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Azure Active Directory**  >  **Roller ve yöneticiler**  >  **Yeni özel rol**' i seçin.

   ![Roller ve yöneticiler sayfasından roller oluşturma veya düzenleme](./media/custom-create/new-custom-role.png)

1. **Temel bilgiler** sekmesinde, rol için bir ad ve açıklama girin ve ardından **İleri**' ye tıklayın.

   ![Temel bilgiler sekmesinde özel rol için bir ad ve açıklama girin](./media/custom-create/basics-tab.png)

1. **İzinler** sekmesinde, uygulama kayıtlarının temel özelliklerini ve kimlik bilgisi özelliklerini yönetmek için gerekli izinleri seçin. Her iznin ayrıntılı bir açıklaması için bkz. [Azure Active Directory 'de uygulama kaydı alt türleri ve izinleri](custom-available-permissions.md).
   1. İlk olarak, arama çubuğuna "kimlik bilgileri" girin ve izni seçin `microsoft.directory/applications/credentials/update` .

      ![Izinler sekmesinde özel bir rol için izinleri seçin](./media/custom-create/permissions-tab.png)

   1. Sonra, arama çubuğuna "temel" yazın, `microsoft.directory/applications/basic/update` izni seçin ve ardından **İleri**' ye tıklayın.
1. **Gözden geçir + oluştur** sekmesinde izinleri gözden geçirin ve **Oluştur**' u seçin.

Özel rolünüzün atanacak kullanılabilir roller listesinde görünür.

## <a name="create-a-role-using-powershell"></a>PowerShell kullanarak rol oluşturma

### <a name="prepare-powershell"></a>PowerShell 'i hazırlama

İlk olarak, [Azure AD önizleme PowerShell modülünü indirmeniz](https://www.powershellgallery.com/packages/AzureADPreview)gerekir.

Azure AD PowerShell modülünü yüklemek için aşağıdaki komutları kullanın:

``` PowerShell
Install-Module -Name AzureADPreview 
Import-Module -Name AzureADPreview 
```

Modülün kullanıma hazırsa emin olmak için aşağıdaki komutu kullanın:

``` PowerShell
Get-Module -Name AzureADPreview 

  ModuleType Version      Name                         ExportedCommands 
  ---------- ---------    ----                         ---------------- 
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...} 
```

### <a name="connect-to-azure"></a>Azure'a Bağlanma

Azure Active Directory bağlanmak için aşağıdaki komutu kullanın:

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>Özel rol oluşturma

Aşağıdaki PowerShell betiğini kullanarak yeni bir rol oluşturun:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell kullanarak özel rol atama

Aşağıdaki PowerShell betiğini kullanarak rolü atayın:

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

## <a name="create-a-role-with-graph-api"></a>Graph API bir rol oluşturma

1. Rol tanımını oluşturun.

    Özel bir rol tanımı oluşturmak için HTTP isteği.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Gövde

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > , `"templateId": "GUID"` Gereksinime göre gövdede gönderilen isteğe bağlı bir parametredir. Ortak parametrelerle birden çok farklı özel rol oluşturma gereksinimine sahipseniz, bir şablon oluşturmak ve bir değer tanımlamak en iyisidir `templateId` . `templateId`PowerShell cmdlet 'ini kullanarak önceden bir değer oluşturabilirsiniz `(New-Guid).Guid` . 

1. Rol atamasını oluşturun.

    Özel bir rol tanımı oluşturmak için HTTP isteği.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Gövde

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Kaynağa kapsamlı özel bir rol atama

Yerleşik roller gibi, kuruluşunuzdaki tüm uygulama kayıtları üzerinde erişim izinleri vermek için varsayılan kuruluş genelindeki kapsamda varsayılan olarak özel roller atanır. Ancak, yerleşik rollerden farklı olarak, özel roller tek bir Azure AD kaynağı kapsamında de atanabilir. Bu, kullanıcıya ikinci bir özel rol oluşturmak zorunda kalmadan tek bir uygulamanın kimlik bilgilerini ve temel özelliklerini güncelleştirme izni vermenizi sağlar.

1. Azure AD kuruluşunda uygulama geliştirici izinleriyle [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Uygulama kayıtları**’nı seçin.
1. Yönetmek üzere erişim verdiğiniz uygulama kaydını seçin. Azure AD kuruluşunuzda uygulama kayıtlarının tüm listesini görmek için **tüm uygulamalar** ' ı seçmeniz gerekebilir.

    ![Rol ataması için kaynak kapsamı olarak uygulama kaydını seçin](./media/custom-create/appreg-all-apps.png)

1. Uygulama kaydında **Roller ve yöneticiler**' i seçin. Henüz bir tane oluşturmadıysanız, yönergeler [önceki yordamda](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)bulunur.

1. **Atamalar** sayfasını açmak için rolü seçin.
1. Kullanıcı eklemek için **atama Ekle** ' yi seçin. Kullanıcıya yalnızca seçili uygulama kaydı üzerinden herhangi bir izin verilecek.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD Yönetim rolleri forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)bizimle paylaşabilirsiniz.
- Roller ve yönetici rolü atama hakkında daha fazla bilgi için bkz. [yönetici rolleri atama](permissions-reference.md).
- Varsayılan Kullanıcı izinleri için bkz. [varsayılan Konuk ve üye Kullanıcı izinlerinin karşılaştırması](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context).
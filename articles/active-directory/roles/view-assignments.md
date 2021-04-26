---
title: Azure AD rol atamalarını listeleme
description: Artık Azure Active Directory Yönetim merkezinde Azure Active Directory yönetici rolünün üyelerini görebilir ve yönetebilirsiniz.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de546ef091b1a8e996f286b0c9af45e93488b5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467666"
---
# <a name="list-azure-ad-role-assignments"></a>Azure AD rol atamalarını listeleme

Bu makalede, Azure Active Directory (Azure AD) ' de atadığınız rollerin nasıl listeleneceğini açıklanmaktadır. Azure Active Directory (Azure AD) içinde roller, kuruluş genelinde bir kapsamda veya tek uygulama kapsamında atanabilir.

- Organizasyon genelindeki kapsamdaki rol atamaları öğesine eklenir ve tek uygulama rolü atamaları listesinde görülebilir.
- Tek uygulama kapsamındaki rol atamaları öğesine eklenmez ve kuruluş genelinde kapsamlı atamalar listesinde görülmez.

## <a name="list-role-assignments-in-the-azure-portal"></a>Azure portal rol atamalarını listeleyin

Bu yordam, rol atamalarının kuruluş genelinde kapsama nasıl listeleneceğini açıklar.

1. Azure AD kuruluşunda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Azure Active Directory** seçin, **Roller ve yöneticiler**' i seçin ve ardından bu rolü açmak ve özelliklerini görüntülemek için bir rol seçin.
1. Rol atamalarını listelemek için **atamalar** ' ı seçin.

    ![Listeden bir rol açtığınızda rol atamalarını ve izinleri listeleyin](./media/view-assignments/role-assignments.png)

## <a name="list-my-role-assignments"></a>Rol atamalarımı Listele

Kendi izinlerinizi de listelemek kolay bir işlemdir. Size atanmış olan rolleri görmek için **Roller ve yöneticiler** sayfasında **rolünüzü** seçin.

## <a name="download-role-assignments"></a>Rol atamalarını indir

Belirli bir rolün tüm atamalarını indirmek için, **Roller ve yöneticiler** sayfasında bir rol seçin ve ardından **rol atamalarını indir**' i seçin. Bu rolün tüm kapsamlarındaki atamaları listeleyen bir CSV dosyası indirilir.

![bir rol için tüm atamaları indirin](./media/view-assignments/download-role-assignments.png)

## <a name="list-role-assignments-using-azure-ad-powershell"></a>Azure AD PowerShell kullanarak rol atamalarını listeleme

Bu bölümde, bir rolün kurumsal çapta kapsama sahip atamalarını görüntüleme açıklanmaktadır. Bu makale, [Azure Active Directory PowerShell sürüm 2](/powershell/module/azuread/#directory_roles) modülünü kullanır. PowerShell kullanarak tek uygulama kapsamı atamalarını görüntülemek için, [PowerShell ile özel roller atama](custom-assign-powershell.md)içindeki cmdlet 'leri kullanabilirsiniz.

### <a name="prepare-powershell"></a>PowerShell 'i hazırlama

İlk olarak, [Azure AD önizleme PowerShell modülünü indirmeniz](https://www.powershellgallery.com/packages/AzureAD/)gerekir.

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

### <a name="list-role-assignments"></a>Rol atamalarını listeleme

Rol atamalarını listeleme örneği.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="list-role-assignments-using-microsoft-graph-api"></a>Microsoft Graph API kullanarak rol atamalarını listeleme

Bu bölümde, rol atamalarının kuruluş genelinde kapsam ile nasıl listeleneceğini açıklanmaktadır.  Graph API kullanarak tek uygulama kapsamı rol atamalarını listelemek için, [Graph API ile özel roller atama](custom-assign-graph.md)' da işlemleri kullanabilirsiniz.

Belirli bir rol tanımı için rol ataması almak için HTTP isteği.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Yanıt

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="list-role-assignments-with-single-application-scope"></a>Tek uygulama kapsamındaki rol atamalarını listeleyin

Bu bölümde, tek uygulama kapsamındaki rol atamalarının nasıl listeleneceğini açıklanmaktadır. Bu özellik şu anda genel önizleme aşamasındadır.

1. Azure AD kuruluşunda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Uygulama kayıtları**' yi seçin ve ardından özelliklerini görüntülemek için uygulama kaydını seçin. Azure AD kuruluşunuzda uygulama kayıtlarının tüm listesini görmek için **tüm uygulamalar** ' ı seçmeniz gerekebilir.

    ![Uygulama kayıtları sayfasından uygulama kayıtları oluşturma veya düzenleme](./media/view-assignments/app-reg-all-apps.png)

1. Uygulama kaydında **Roller ve yöneticiler**' i seçin ve ardından özelliklerini görüntülemek için bir rol seçin.

    ![Uygulama kayıtları sayfasından uygulama kaydı rol atamalarını listeleyin](./media/view-assignments/app-reg-assignments.png)

1. Rol atamalarını listelemek için **atamalar** ' ı seçin. Atamalar sayfasını uygulama kaydı içinden açmak, bu Azure AD kaynağı kapsamındaki rol atamalarını gösterir.

    ![Uygulama kaydı özelliklerinden uygulama kaydı rol atamalarını listeleyin](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Yönetim rolleri forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)bizimle paylaşabilirsiniz.
* Roller ve yönetici rolü atama hakkında daha fazla bilgi için bkz. [yönetici rolleri atama](permissions-reference.md).
* Varsayılan Kullanıcı izinleri için bkz. [varsayılan Konuk ve üye Kullanıcı izinlerinin karşılaştırması](../fundamentals/users-default-permissions.md).

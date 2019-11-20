---
title: Azure AD portalındaki özel rol atamalarını görüntüleme | Microsoft Docs
description: Artık Azure AD Yönetim merkezinde bir Azure AD yönetici rolünün üyelerini görebilir ve yönetebilirsiniz.
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
ms.openlocfilehash: d989a515a1a3c1dc12ca357a3ab2126e78800ffa
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024429"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Azure Active Directory özel rol atamalarını görüntüleme

Bu makalede, Azure Active Directory (Azure AD) ' de atadığınız özel rollerin nasıl görüntüleneceği açıklanır. Azure Active Directory (Azure AD) içinde roller, kuruluş genelinde bir kapsamda veya tek uygulama kapsamında atanabilir.

- Organizasyon genelindeki kapsamdaki rol atamaları öğesine eklenir ve tek uygulama rolü atamaları listesinde görülebilir.
- Tek uygulama kapsamındaki rol atamaları öğesine eklenmez ve kuruluş genelinde kapsamlı atamalar listesinde görülmez.

## <a name="view-role-assignments-in-the-azure-portal"></a>Azure portal rol atamalarını görüntüleme

Bu yordam, bir rolün kurumsal çapta kapsama sahip atamalarını görüntülemeyi açıklar.

1. Azure AD kuruluşunda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezi](https://aad.portal.azure.com) oturum açın.
1. **Azure Active Directory**seçin, **Roller ve yöneticiler**' i seçin ve ardından bu rolü açmak ve özelliklerini görüntülemek için bir rol seçin.
1. Rolün atamalarını görüntülemek için **atamalar** ' ı seçin.

    ![Listeden bir rol açtığınızda rol atamalarını ve izinleri görüntüleme](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Azure AD PowerShell kullanarak rol atamalarını görüntüleme

Bu bölümde, bir rolün kurumsal çapta kapsama sahip atamalarını görüntüleme açıklanmaktadır. Bu makale, [Azure Active Directory PowerShell sürüm 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) modülünü kullanır. PowerShell kullanarak tek uygulama kapsamı atamalarını görüntülemek için, [PowerShell ile özel roller atama](roles-assign-powershell.md)içindeki cmdlet 'leri kullanabilirsiniz.

### <a name="prepare-powershell"></a>PowerShell 'i hazırlama

İlk olarak, [Azure AD önizleme PowerShell modülünü indirmeniz](https://www.powershellgallery.com/packages/AzureAD/)gerekir.

Azure AD PowerShell modülünü yüklemek için aşağıdaki komutları kullanın:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Modülün kullanıma hazırsa emin olmak için aşağıdaki komutu kullanın:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Rolün atamalarını görüntüleme

Bir rolün atamalarını görüntüleme örneği.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Microsoft Graph API kullanarak rol atamalarını görüntüleme

Bu bölümde, bir rolün kurumsal çapta kapsama sahip atamalarını görüntüleme açıklanmaktadır.  Graph API kullanarak tek uygulama kapsamı atamalarını görüntülemek için, [Graph API ile özel rol atama](roles-assign-graph.md)içindeki işlemleri kullanabilirsiniz.

Belirli bir rol tanımı için rol ataması almak için HTTP isteği.

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
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

## <a name="view-assignments-of-single-application-scope"></a>Tek uygulama kapsamının atamalarını görüntüleme

Bu bölümde, tek uygulama kapsamıyla bir rolün atamalarının görüntülenmesi açıklanmaktadır. Bu özellik şu anda genel önizleme aşamasındadır.

1. Azure AD kuruluşunda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezi](https://aad.portal.azure.com) oturum açın.
1. **Uygulama kayıtları**' yi seçin ve ardından özelliklerini görüntülemek için uygulama kaydını seçin. Azure AD kuruluşunuzda uygulama kayıtlarının tüm listesini görmek için **tüm uygulamalar** ' ı seçmeniz gerekebilir.

    ![Uygulama kayıtları sayfasından uygulama kayıtları oluşturma veya düzenleme](./media/roles-create-custom/appreg-all-apps.png)

1. Uygulama kaydında **Roller ve yöneticiler**' i seçin ve ardından özelliklerini görüntülemek için bir rol seçin.

    ![Uygulama kayıtları sayfasından uygulama kaydı rol atamalarını görüntüleme](./media/roles-view-assignments/appreg-assignments.png)

1. Rolün atamalarını görüntülemek için **atamalar** ' ı seçin. Atamalar görünümünü uygulama kaydı içinden açmak, bu Azure AD kaynağı kapsamındaki atamaları gösterir.

    ![Uygulama kaydının özelliklerinden uygulama kaydı rol atamalarını görüntüleme](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Yönetim rolleri forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)bizimle paylaşabilirsiniz.
* Roller ve yönetici rolü atama hakkında daha fazla bilgi için bkz. [yönetici rolleri atama](directory-assign-admin-roles.md).
* Varsayılan Kullanıcı izinleri için bkz. [varsayılan Konuk ve üye Kullanıcı izinlerinin karşılaştırması](../fundamentals/users-default-permissions.md).

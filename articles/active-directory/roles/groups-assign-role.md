---
title: Azure Active Directory bir bulut grubuna rol atama | Microsoft Docs
description: Azure portal, PowerShell veya Graph API rol atanabilir bir gruba bir Azure AD rolü atayın.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816009abb688525cd7663311c79300a6d12cf146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742956"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Azure Active Directory bir bulut grubuna rol atama

Bu bölümde, bir BT yöneticisinin bir Azure AD grubuna Azure Active Directory (Azure AD) rolü atayabileceği açıklanmaktadır.

## <a name="using-azure-ad-admin-center"></a>Azure AD Yönetim Merkezi 'ni kullanma

Bir Azure AD rolüne Grup atamak, yalnızca rol atanabilir olan grupların kullanılabilmesi dışında, Kullanıcı ve hizmet sorumluları atamaya benzer. Azure portal, yalnızca rol atanabilir olan gruplar görüntülenir.

1. Azure AD kuruluşunda ayrıcalıklı rol yöneticisi veya genel yönetici izinleriyle [Azure AD Yönetim merkezinde](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) oturum açın.

1. **Azure Active Directory**  >  **Roller ve yöneticiler**' i seçin ve atamak istediğiniz rolü seçin.

1. ***Rol adı** _ sayfasında > _ * atama Ekle * * ' yi seçin.

   ![Yeni rol atamasını ekleyin](./media/groups-assign-role/add-assignment.png)

1. Grubu seçin. Yalnızca Azure AD rollerine atanabileceği gruplar görüntülenir.

    [![Yalnızca atanabilen gruplar yeni bir rol ataması için gösterilir.](./media/groups-assign-role/eligible-groups.png "Yalnızca atanabilen gruplar yeni bir rol ataması için gösterilir.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. **Add (Ekle)** seçeneğini belirleyin.

Rol izinleri atama hakkında daha fazla bilgi için bkz. [kullanıcılara yönetici ve yönetici olmayan roller atama](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="using-powershell"></a>PowerShell’i kullanma

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Role atanabilecek bir grup oluşturun

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Atamak istediğiniz rolün rol tanımını alın

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Rol ataması oluşturma

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API 'sini kullanma

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Azure AD rolü atanabileceği bir grup oluşturun

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Rol tanımını al

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Rol atamasını oluşturma

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Sonraki adımlar

- [Rol atamalarını yönetmek için bulut gruplarını kullanma](groups-concept.md)
- [Bulut gruplarına atanan rollerle ilgili sorunları giderme](groups-faq-troubleshooting.md)

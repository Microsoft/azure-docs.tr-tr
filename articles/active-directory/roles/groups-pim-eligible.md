---
title: Azure AD 'de Privileged Identity Management kullanarak bir gruba rol atama | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) kullanarak bir Azure Active Directory (Azure AD) rolünü bir gruba nasıl atayabileceğinizi öğrenin.
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
ms.openlocfilehash: 02cd3f54823b80ae201316fee29c02616b9d8502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012046"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Privileged Identity Management kullanarak bir gruba rol atama

Bu makalede, bir Azure Active Directory (Azure AD) rolünü Azure AD Privileged Identity Management (PıM) kullanarak bir gruba nasıl atayabileceğinizi açıklamaktadır.

> [!NOTE]
> PıM kullanarak bir Azure AD rolüne Grup atayabilmek için Privileged Identity Management güncelleştirilmiş sürümünü kullanıyor olmanız gerekir. Azure AD kuruluşunuz Privileged Identity Management API 'sini kullanıyorsa, PıM 'nin eski sürümünde olabilirsiniz. Bu durumda, pim_preview@microsoft.com kuruluşunuzu taşımak ve API 'nizi güncelleştirmek için lütfen diğer ada ulaşın. [PIM 'de Azure AD rolleri ve özellikleri](../privileged-identity-management/azure-ad-roles-features.md)hakkında daha fazla bilgi edinin.

## <a name="using-azure-ad-admin-center"></a>Azure AD Yönetim Merkezi 'ni kullanma

1. [Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) , kuruluşunuzda ayrıcalıklı rol yöneticisi veya genel yönetici olarak oturum açın.

1. **Privileged Identity Management**  >  **Azure AD rolleri**  >  **rollerini** seçin  >  **atama Ekle**

1. Bir rol seçin ve ardından bir grup seçin. Yalnızca rol ataması (rol atanabilir gruplar) için uygun olan gruplar, tüm gruplar değil görüntülenir.

    !["Rol Ekle" sayfasını ve "Select role" ve "Select member (s)" bölümünü gösteren ekran görüntüsü.](./media/groups-pim-eligible/select-member.png)

1. İstenen üyelik ayarını seçin. Etkinleştirme gerektiren roller için **uygun**' ı seçin. Varsayılan olarak, Kullanıcı kalıcı olarak uygun olacaktır, ancak kullanıcının uygunluğu için bir başlangıç ve bitiş saati de ayarlayabilirsiniz. Tamamlandıktan sonra, rol atamasını tamamladıktan sonra Kaydet ve Ekle ' ye basın.

    ![rolü atadığınız kullanıcıyı seçin](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>PowerShell’i kullanma

### <a name="download-the-azure-ad-preview-powershell-module"></a>Azure AD önizleme PowerShell modülünü indirin

Azure AD #PowerShell modülünü yüklemek için aşağıdaki cmdlet 'leri kullanın:

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Modülün kullanıma hazırolduğunu doğrulamak için aşağıdaki cmdlet 'i kullanın:

```powershell
Get-Module -Name AzureADPreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Bir rolün uygun bir üyesi olarak Grup atama

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API 'sini kullanma

```http
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests
{
 "roleDefinitionId": {roleDefinitionId},
 "resourceId": {tenantId},
 "subjectId": {GroupId},
 "assignmentState": "Eligible",
 "type": "AdminAdd",
 "reason": "reason string",
 "schedule": {
   "startDateTime": {DateTime},
   "endDateTime": {DateTime},
   "type": "Once"
 }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Rol atamalarını yönetmek için bulut gruplarını kullanma](groups-concept.md)
- [Bulut gruplarına atanan rollerle ilgili sorunları giderme](groups-faq-troubleshooting.md)
- [Privileged Identity Management 'de Azure AD yönetici rolü ayarlarını yapılandırma](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Privileged Identity Management Azure Kaynak rolleri atama](../privileged-identity-management/pim-resource-roles-assign-roles.md)

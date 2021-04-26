---
title: Azure Active Directory bir gruba atanmış rolleri görüntüle | Microsoft Docs
description: Bir gruba atanan rollerin Azure AD Yönetim Merkezi kullanılarak nasıl görüntülenebileceğini öğrenin. Grupları ve atanan rolleri görüntüleme varsayılan kullanıcı izinleridir.
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
ms.openlocfilehash: 7dac23897f8a17c9adb4ae78736a6a8afa85a18b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012012"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Azure Active Directory bir gruba atanan rolleri görüntüleme

Bu bölümde, bir gruba atanan rollerin Azure AD Yönetim Merkezi kullanılarak nasıl görüntülenebileceğini açıklar. Grupları ve atanan rolleri görüntüleme varsayılan kullanıcı izinleridir.

1. Yönetici olmayan veya yönetici kimlik bilgileriyle [Azure AD Yönetim merkezinde](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) oturum açın.

1. İlgilendiğiniz grubu seçin.

1. **Atanan roller**' i seçin. Artık bu gruba atanmış tüm Azure AD rollerini görebilirsiniz.

   ![Seçili bir gruba atanan tüm rolleri görüntüle](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>PowerShell’i kullanma

### <a name="get-object-id-of-the-group"></a>Grubun nesne KIMLIĞINI al

```powershell
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>Rol atamasını bir gruba görüntüle

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API 'sini kullanma

### <a name="get-object-id-of-the-group"></a>Grubun nesne KIMLIĞINI al

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>Gruba rol atamaları al

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Sonraki adımlar

- [Rol atamalarını yönetmek için bulut gruplarını kullanma](groups-concept.md)
- [Bulut gruplarına atanan rollerle ilgili sorunları giderme](groups-faq-troubleshooting.md)

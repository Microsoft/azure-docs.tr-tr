---
title: Raporlama için Azure AD PowerShell cmdlet 'leri | Microsoft Docs
description: Raporlama için Azure AD PowerShell cmdlet 'lerinin başvurusu.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 329036f3ed815eaaba94f441e372f4be86edd629
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558092"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Raporlama için Azure AD PowerShell cmdlet’leri

> [!NOTE] 
> Bu PowerShell cmdlet 'leri Şu anda yalnızca [Azure AD önizleme](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#directory_auditing) modülüyle çalışır. Önizleme modülünün üretim kullanımı için önerilmediğini lütfen unutmayın. 

Genel önizleme sürümünü yüklemek için aşağıdakileri kullanın. 

```powershell
Install-module AzureADPreview
```

PowerShell kullanarak Azure AD 'ye bağlanma hakkında daha fazla bilgi için lütfen [Graph Için Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)makalesine bakın.  

Azure Active Directory (Azure AD) raporlarında, yönlerinizde (denetim günlükleri) ve kimlik doğrulama verilerinde (oturum açma günlükleri) tüm yazma işlemlerinde bulunan etkinliklerle ilgili ayrıntılı bilgi edinebilirsiniz. Bilgiler MS Graph API kullanılarak kullanılabilir olsa da, artık raporlama için Azure AD PowerShell cmdlet 'lerini kullanarak aynı verileri alabilirsiniz.

Bu makale, denetim günlükleri ve oturum açma günlükleri için kullanılacak PowerShell cmdlet 'lerine genel bir bakış sunar.

## <a name="audit-logs"></a>Denetim günlükleri

[Denetim günlükleri](concept-audit-logs.md) , Azure AD içindeki çeşitli özelliklerle gerçekleştirilen tüm değişiklikler için Günlükler aracılığıyla izlenebilirlik sağlar. Denetim günlüklerine örnek olarak, Azure AD 'de Kullanıcı, uygulama, Grup, rol ve ilke ekleme veya kaldırma gibi herhangi bir kaynak üzerinde yapılan değişiklikler verilebilir.

' Get-AzureADAuditDirectoryLogs cmdlet 'ini kullanarak denetim günlüklerine erişebilirsiniz.


| Senaryo                      | PowerShell komutu |
| :--                           | :--                |
| Uygulama görünen adı      | Get-AzureADAuditDirectoryLogs-filtre "/uygulama/displayName EQ ' Azure AD bulut eşitlemesi '" |
| Kategori                      | Get-AzureADAuditDirectoryLogs filtre "Kategori EQ ' ApplicationManagement '" |
| Etkinlik tarihi saati            | Get-AzureADAuditDirectoryLogs-filtre "activityDateTime gt 2019-04-18" |
| Yukarıdakilerin tümü              | Get-AzureADAuditDirectoryLogs-filtre "/uygulama/displayName EQ ' Azure AD bulut eşitleme ' ve kategori EQ ' ApplicationManagement ' ve activityDateTime gt 2019-04-18"|


Aşağıdaki görüntüde bu komut için bir örnek gösterilmektedir. 

![Ekran görüntüsü bir D denetim dizin günlükleri komutunun sonucunu gösterir Get-Azure.](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Oturum açma günlükleri

[Oturum açma](concept-sign-ins.md) günlükleri, yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinliklerinin kullanımı hakkında bilgi sağlar.

' Get-Azureadauditsignınlogs cmdlet 'ini kullanarak oturum açma günlüklerine erişim edinirsiniz.


| Senaryo                      | PowerShell komutu |
| :--                           | :--                |
| Kullanıcı görünen adı             | Get-AzureADAuditSignInLogs-"userDisplayName EQ ' Timokirli Perkins '" filtrelemeye |
| Tarih saat oluştur              | Get-AzureADAuditSignInLogs-filtre "createdDateTime gt 2019-04-18T17:30:00.0 Z" (4/18 tarihinde 5:30 ile bu yana her şey) |
| Durum                        | Get-AzureADAuditSignInLogs-filtre "durum/hata kodu EQ 50105" |
| Uygulama görünen adı      | Get-AzureADAuditSignInLogs-filtre "appDisplayName EQ ' StoreFrontStudio [wsbesenabled] '" |
| Yukarıdakilerin tümü              | Get-AzureADAuditSignInLogs-"userDisplayName EQ ' Timoilperkins ' ve Status/errorCode 0 ve appDisplayName EQ ' StoreFrontStudio [wsbessenabled] '" filtrelenir |


Aşağıdaki görüntüde bu komut için bir örnek gösterilmektedir. 

![Ekran görüntüsü, bir D Denetim oturum açma günlüğü komutunun Get-Azure sonucunu gösterir.](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD raporlarına genel bakış](overview-reports.md).
- [Denetim günlükleri raporu](concept-audit-logs.md). 
- [Azure AD raporlarına programlı erişim](concept-reporting-api.md)

---
title: Azure AD ile izin sınıflandırmalarını yapılandırma
description: Temsil edilen izin sınıflandırmalarını yönetmeyi öğrenin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: be58f5cd18d32302d1e92f00afb7d7e0aae09410
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374480"
---
# <a name="configure-permission-classifications"></a>İzin sınıflandırmalarını yapılandırma

İzin sınıflandırmaları, farklı izinlerin kuruluşunuzun ilkelerine ve risk değerlendirmelere göre sahip olduğu etkiyi tanımlamanızı sağlar. Örneğin, kullanıcıların izin verdiği izin kümesini belirlemek için izin sınıflandırmalar ' da izin sınıflandırmalarını kullanabilirsiniz.

## <a name="manage-permission-classifications"></a>İzin sınıflandırmalarını yönetme

Şu anda yalnızca "düşük etki" izin sınıflandırması desteklenir. Yalnızca yönetici onayı gerektirmeyen temsilci izinleri "düşük etki" olarak sınıflandırılabilirler.

> [!TIP]
> Temel oturum açma için gereken en düşük izinler `openid` , `profile` `email` `User.Read` `offline_access` Microsoft Graph tüm temsilci izinleri olan,, ve ' dir. Bu izinlerle bir uygulama, oturum açmış kullanıcının tüm profil ayrıntılarını okuyabilir ve Kullanıcı artık uygulamayı kullanmıyor olsa bile bu erişimi koruyabilir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak izinleri sınıflandırmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) [genel yönetici](../roles/permissions-reference.md#global-administrator), [Uygulama Yöneticisi](../roles/permissions-reference.md#application-administrator)veya [bulut uygulama Yöneticisi](../roles/permissions-reference.md#cloud-application-administrator) olarak oturum açın
1. **Azure Active Directory**  >  **kurumsal uygulamaların**  >  **onay ve** izin  >  **izin sınıflandırmalarını** seçin.
1. Başka bir izni "düşük etki" olarak sınıflandırmak için **Izin Ekle** ' yi seçin.
1. API 'YI seçin ve ardından temsilci izinleri seçin.

Bu örnekte, çoklu oturum açma için gereken en düşük izin kümesini sınıflandırdık:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="İzin sınıflandırmaları":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

İzinleri sınıflandırmak için en son Azure AD PowerShell önizleme modülünü [Azureadpreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)' i kullanabilirsiniz. İzin sınıflandırmaları, izinleri yayımlayan API 'nin **ServicePrincipal** nesnesinde yapılandırılır.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Bir API için geçerli izin sınıflandırmalarını listeleme

1. API için **ServicePrincipal** nesnesini alın. Burada Microsoft Graph API 'SI için ServicePrincipal nesnesini alıyoruz:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. API için Temsilcili izin sınıflandırmalarını okuyun:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Bir izni "düşük etki" olarak sınıflandırın

1. API için **ServicePrincipal** nesnesini alın. Burada Microsoft Graph API 'SI için ServicePrincipal nesnesini alıyoruz:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Sınıflandırmak istediğiniz temsilci iznini bulun:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. İzin adını ve KIMLIĞINI kullanarak izin sınıflandırmasını ayarlayın:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Temsilci bir izin sınıflandırmasını kaldırma

1. API için **ServicePrincipal** nesnesini alın. Burada Microsoft Graph API 'SI için ServicePrincipal nesnesini alıyoruz:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Kaldırmak istediğiniz Temsilcili izin sınıflandırmasını bulun:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. İzin sınıflandırmasını silin:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Sonraki adımlar

Daha fazlasını öğrenin:

* [Kullanıcı izin ayarlarını yapılandırma](configure-user-consent.md)
* [Yönetici onayı iş akışını yapılandırma](configure-admin-consent-workflow.md)
* [Uygulama onayını yönetmeyi ve izin isteklerini değerlendirmeyi öğrenin](manage-consent-requests.md)
* [Uygulamaya kiracı genelinde yönetici onayı verme](grant-admin-consent.md)
* [Microsoft Identity platformunda izinler ve onay](../develop/v2-permissions-and-consent.md)

Yardım almak veya sorularınıza yanıt bulmak için:
* [Microsoft Q&A 'da Azure AD](/answers/topics/azure-active-directory.html)
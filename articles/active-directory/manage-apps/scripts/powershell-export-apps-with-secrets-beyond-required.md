---
title: PowerShell örneği-Azure Active Directory kiracısındaki gerekli tarihin ötesinde gizli dizileri ve sertifikaları dışarı aktarma.
description: Azure Active Directory kiracınızdaki belirtilen uygulamalar için gerekli tarihin ötesinde gizli dizi ve sertifikalara sahip tüm uygulamaları dışa aktaran PowerShell örneği.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 692ab2cfd480fd15760c13c63922fe76d23058ea
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375398"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Uygulamaları gizli ve sertifikalara göre, gerekli tarihin ötesinde süresi dolacak şekilde dışa aktarma

Bu PowerShell betiği örneği, bir CSV dosyasındaki dizininizden etkileşimli olmayan bir şekilde, belirtilen uygulamalar için gerekli bir sürenin ötesine geçen tüm uygulama kaydı gizli dizilerini ve sertifikalarını dışa aktarır.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik etkileşimli olmayan şekilde çalışıyor. Bunu kullanan yöneticinin, kendi uygulama KIMLIĞI, uygulama gizli anahtarı, kiracı adı, uygulama kimlik bilgilerinin süre sonu ve CSV 'nin aktarılacağı yol ile "DEĞIŞTIRILECEK #PARAMETERS" bölümündeki değerleri değiştirmesi gerekir.
Bu betik, "RefreshToken" işlevi, yönetici tarafından değiştirilen parametrelerin değerlerine göre erişim belirtecini oluşturacak [Client_Credential OAuth akışını](../../develop/v2-oauth2-client-creds-grant-flow.md) kullanır.

"Add-Member" komutu CSV dosyasındaki sütunları oluşturmaktan sorumludur.

| Komut | Notlar |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Bir Web sayfasına veya Web hizmetine HTTP ve HTTPS istekleri gönderir. Yanıtı ayrıştırır ve bağlantılar, görüntüler ve diğer önemli HTML öğeleri için koleksiyonları döndürür. |

## <a name="next-steps"></a>Sonraki adımlar

Azure AD PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure AD PowerShell modülüne genel bakış](/powershell/azure/active-directory/overview).

Uygulama yönetimi için diğer PowerShell örnekleri için bkz. [uygulama yönetimi Için Azure AD PowerShell örnekleri](../app-management-powershell-samples.md).

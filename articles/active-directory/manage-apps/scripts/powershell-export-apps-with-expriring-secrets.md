---
title: PowerShell örneği-Azure Active Directory kiracısındaki süresi dolan gizli dizileri ve sertifikaları dışarı aktarın.
description: Azure Active Directory kiracınızda belirtilen uygulamalar için süresi dolan gizli dizileri ve sertifikaları olan tüm uygulamaları dışa aktaran PowerShell örneği.
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
ms.openlocfilehash: 7f129e06904497b43eff8a3f0221fb57565ac112
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375415"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Süresi dolan gizlilikler ve sertifikalarla uygulamaları dışa aktarma

Bu PowerShell betiği örneği, bir CSV dosyasındaki dizininizden belirtilen uygulamalar için süresi dolan gizli dizileri, sertifikaları ve onların sahiplerini içeren tüm uygulama kayıtlarını dışa aktarır.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Bu örnek, Graf modülü (azuread) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2) 'ı veya Graf modülü önizleme sürümü (azureadpreview) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) 'i gerektirir.

## <a name="sample-script"></a>Örnek betik

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Betik açıklaması

Betik, herhangi bir değişiklik yapılmadan doğrudan kullanılabilir. Yöneticiye son kullanma tarihi ve süresi dolmayan gizli dizileri veya sertifikaları görmek isteyip istemediğiniz sorulur.

"Add-Member" komutu CSV dosyasındaki sütunları oluşturmaktan sorumludur.
"New-Object" komutu CSV dosyası dışarı aktarma içindeki sütunlarda kullanılacak bir nesne oluşturur.
"$Path" değişkenini doğrudan PowerShell 'de bir CSV dosyası yoluyla değiştirebilirsiniz, böylece dışa aktarma işlemini etkileşimli olmayan şekilde tercih edebilirsiniz.

| Komut | Notlar |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Dizininizden bir uygulama alır. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Bir uygulamanın sahiplerini dizininizden alır. |

## <a name="next-steps"></a>Sonraki adımlar

Azure AD PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure AD PowerShell modülüne genel bakış](/powershell/azure/active-directory/overview).

Uygulama yönetimi için diğer PowerShell örnekleri için bkz. [uygulama yönetimi Için Azure AD PowerShell örnekleri](../app-management-powershell-samples.md).

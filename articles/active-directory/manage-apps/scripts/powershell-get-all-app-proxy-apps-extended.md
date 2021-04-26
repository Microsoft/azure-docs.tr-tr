---
title: PowerShell örneği-uygulama proxy 'Si uygulamaları için genişletilmiş bilgileri listeleyin
description: Tüm Azure Active Directory (Azure AD) uygulama proxy uygulamalarını, uygulama KIMLIĞI (AppID), adı (DisplayName), dış URL (ExternalUrl), iç URL (InternalUrl) ve kimlik doğrulama türü (ExternalAuthenticationType) ile birlikte listeleyen PowerShell örneği.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 1b3234a13a0e3fac760a899ce66e7faa7e7b642c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377370"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Tüm uygulama proxy 'Si uygulamalarını al ve genişletilmiş bilgileri Listele

Bu PowerShell betiği örneği, uygulama KIMLIĞI (AppID), adı (DisplayName), dış URL (ExternalUrl), iç URL (InternalUrl), kimlik doğrulama türü (ExternalAuthenticationType), SSO modu ve diğer ayarlar dahil olmak üzere tüm Azure Active Directory (Azure AD) uygulama proxy uygulamaları hakkındaki bilgileri listeler.

$SsoMode değişkenin değerini değiştirmek, filtrelenmiş bir çıktıyı SSO moduna izin vermez. Diğer ayrıntılar betikte belgelenmiştir.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Bu örnek, Graf modülü (azuread) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2) gerektirir.

## <a name="sample-script"></a>Örnek betik

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Betik açıklaması

| Komut | Notlar |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hizmet sorumlusu alır. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Bir Azure AD uygulaması alır. |
|[Get-Azureadapplicationproxun](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Azure AD 'de uygulama proxy 'Si için yapılandırılmış bir uygulamayı alır. |

## <a name="next-steps"></a>Sonraki adımlar

Azure AD PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure AD PowerShell modülüne genel bakış](/powershell/azure/active-directory/overview).

Uygulama proxy 'Si için diğer PowerShell örnekleri için bkz. Azure [ad uygulama ara sunucusu Için Azure AD PowerShell örnekleri](../application-proxy-powershell-samples.md).

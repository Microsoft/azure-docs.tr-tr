---
title: PowerShell örneği-uygulama proxy 'Si uygulamalarındaki sertifikayı değiştirme
description: Azure Active Directory (Azure AD) uygulama proxy 'Si uygulamaları genelinde bir sertifikayı toplu olarak değiştiren PowerShell örneği.
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
ms.openlocfilehash: a2775ad9de9fa422f32342ea4f9e7bdba1a50d5f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376996"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>Aynı sertifikayla yayınlanan tüm uygulama proxy uygulamalarını alın ve değiştirin

Bu PowerShell betiği örneği, aynı sertifikayla yayınlanan tüm Azure Active Directory (Azure AD) uygulama proxy uygulamaları için sertifikayı toplu olarak değiştirmenize izin verir.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Bu örnek, Graf modülü (azuread) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2) 'ı veya Graf modülü önizleme sürümü (azureadpreview) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) 'i gerektirir.

## <a name="sample-script"></a>Örnek betik

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>Betik açıklaması

| Komut | Notlar |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hizmet sorumlusu alır. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Bir Azure AD uygulaması alır. |
|[Get-Azureadapplicationproxun](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Azure AD 'de uygulama proxy 'Si için yapılandırılmış bir uygulamayı alır. |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate) | Azure AD 'de uygulama proxy 'Si için yapılandırılmış bir uygulamaya bir sertifika atar. Bu komut sertifikayı karşıya yükler ve uygulamanın özel etki alanlarını kullanmasına izin verir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure AD PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure AD PowerShell modülüne genel bakış](/powershell/azure/active-directory/overview).

Uygulama proxy 'Si için diğer PowerShell örnekleri için bkz. Azure [ad uygulama ara sunucusu Için Azure AD PowerShell örnekleri](../application-proxy-powershell-samples.md).

---
title: PowerShell örneği-uygulama proxy 'Si uygulamalarını başka bir gruba taşıma
description: Bir bağlayıcı grubuna atanmış olan tüm uygulamaları farklı bir bağlayıcı grubuna taşımak için kullanılan Azure Active Directory (Azure AD) uygulama proxy PowerShell örneği.
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
ms.openlocfilehash: eb421a1a8040a406c8f3e4be04f9a99d881237d9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376928"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Bir bağlayıcı grubuna atanan tüm uygulamaları başka bir bağlayıcı grubuna Taşı

Bu PowerShell betiği örneği, bir bağlayıcı grubuna şu anda atanmış olan tüm Azure Active Directory (Azure AD) uygulama proxy uygulamalarını farklı bir bağlayıcı grubuna taşırlar.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Bu örnek, Graf modülü (azuread) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2) 'ı veya Graf modülü önizleme sürümü (azureadpreview) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) 'i gerektirir.

## <a name="sample-script"></a>Örnek betik

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Betik açıklaması

| Komut | Notlar |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hizmet sorumlusu alır. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Bir Azure AD uygulaması alır. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Tüm bağlayıcı gruplarının bir listesini veya belirtilmişse, belirtilen bağlayıcı grubunun ayrıntılarını alır. |
| [Set-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup) | Verilen bağlayıcı grubunu belirtilen bir uygulamaya atar.|

## <a name="next-steps"></a>Sonraki adımlar

Azure AD PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure AD PowerShell modülüne genel bakış](/powershell/azure/active-directory/overview).

Uygulama proxy 'Si için diğer PowerShell örnekleri için bkz. Azure [ad uygulama ara sunucusu Için Azure AD PowerShell örnekleri](../application-proxy-powershell-samples.md).

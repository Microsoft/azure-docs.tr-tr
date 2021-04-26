---
title: PowerShell örneği-uygulama proxy 'Si uygulaması için kullanıcıları & grupları listeleme
description: Belirli bir Azure Active Directory (Azure AD) uygulama proxy 'Si uygulamasına atanan tüm kullanıcıları ve grupları listeleyen PowerShell örneği.
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
ms.openlocfilehash: 0fdbf2ab7d33978a72e7455f3e2f5ba591cc4ab7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375500"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Uygulama proxy 'Si uygulamasına atanan kullanıcıları ve grupları görüntüleme

Bu PowerShell betiği örneği, belirli bir Azure Active Directory (Azure AD) uygulama proxy 'Si uygulamasına atanan kullanıcıları ve grupları listeler.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Bu örnek, Graf modülü (azuread) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2) 'ı veya Graf modülü önizleme sürümü (azureadpreview) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) 'i gerektirir.

## <a name="sample-script"></a>Örnek betik

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Betik açıklaması

| Komut | Notlar |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| Bir kullanıcıyı alır. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| Bir grup alır. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hizmet sorumlusu alır. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | Kullanıcı uygulama rolü ataması alın. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | Grup uygulama rolü ataması alın. |

## <a name="next-steps"></a>Sonraki adımlar

Azure AD PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure AD PowerShell modülüne genel bakış](/powershell/azure/active-directory/overview).

Uygulama proxy 'Si için diğer PowerShell örnekleri için bkz. Azure [ad uygulama ara sunucusu Için Azure AD PowerShell örnekleri](../application-proxy-powershell-samples.md).

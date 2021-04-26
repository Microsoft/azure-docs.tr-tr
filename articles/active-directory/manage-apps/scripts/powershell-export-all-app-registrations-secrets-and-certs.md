---
title: PowerShell örneği-Azure Active Directory kiracısındaki uygulama kayıtları için gizli dizileri ve sertifikaları dışarı aktarın.
description: Azure Active Directory kiracınızda belirtilen uygulama kayıtlarına yönelik tüm gizli dizileri ve sertifikaları dışarı aktaran PowerShell örneği.
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
ms.openlocfilehash: b5cbb6b3843e81d9265405dcea24a092e57bf65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377055"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Uygulama kayıtları için gizli dizileri ve sertifikaları dışa aktarma

Bu PowerShell betiği örneği, belirtilen uygulama kayıtlarına ait tüm gizli dizileri ve sertifikaları dizininizden bir CSV dosyasına dışarı aktarır.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Bu örnek, Graf modülü (azuread) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2) 'ı veya Graf modülü önizleme sürümü (azureadpreview) [Için Azuread v2 PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) 'i gerektirir.

## <a name="sample-script"></a>Örnek betik

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Betik açıklaması

"Add-Member" komutu CSV dosyasındaki sütunları oluşturmaktan sorumludur.
"$Path" değişkenini doğrudan PowerShell 'de bir CSV dosyası yoluyla değiştirebilirsiniz, böylece dışa aktarma işlemini etkileşimli olmayan şekilde tercih edebilirsiniz.

| Komut | Notlar |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Dizininizden bir uygulama alır. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Bir uygulamanın sahiplerini dizininizden alır. |

## <a name="next-steps"></a>Sonraki adımlar

Azure AD PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure AD PowerShell modülüne genel bakış](/powershell/azure/active-directory/overview).

Uygulama yönetimi için diğer PowerShell örnekleri için bkz. [uygulama yönetimi Için Azure AD PowerShell örnekleri](../app-management-powershell-samples.md).

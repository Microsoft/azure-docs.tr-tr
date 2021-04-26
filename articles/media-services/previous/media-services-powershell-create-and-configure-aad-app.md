---
title: PowerShell kullanarak Azure Media Services API 'sine erişmek için bir Azure AD uygulaması oluşturun | Microsoft Docs
description: PowerShell kullanarak Azure Active Directory (Azure AD) uygulaması oluşturma ve Azure Media Services API 'sine erişecek şekilde ayarlama hakkında bilgi edinin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d16c2bbb16a19e5cb22b2b2b0378880ec9aa48b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009666"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>PowerShell kullanarak Azure Media Services API ile kullanmak üzere bir Azure AD uygulaması oluşturma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services kaynaklarına erişmek için bir Azure Active Directory (Azure AD) uygulaması ve hizmet sorumlusu oluşturmak üzere bir PowerShell betiği kullanmayı öğrenin.  

## <a name="prerequisites"></a>Önkoşullar

- Azure hesabı. Hesabınız yoksa [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)ile başlayın. 
- Bir Media Services hesabı. Daha fazla bilgi için [Azure portal Azure Media Services hesap oluşturma](media-services-portal-create-account.md)konusuna bakın.

- Azure PowerShell. Daha fazla bilgi için bkz. [Azure PowerShell kullanma](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>PowerShell kullanarak bir Azure AD uygulaması oluşturma  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Kaynaklara erişmek üzere hizmet sorumlusu oluşturmak için Azure PowerShell kullanma](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Azure PowerShell kullanarak Azure rol atamalarını ekleme veya kaldırma](../../role-based-access-control/role-assignments-powershell.md)
- [Sertifikaları kullanarak Daemon uygulamalarını el ile yapılandırma](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>Sonraki adımlar

[Hesabınıza dosya yüklemeye](media-services-portal-upload-files.md)başlayın.

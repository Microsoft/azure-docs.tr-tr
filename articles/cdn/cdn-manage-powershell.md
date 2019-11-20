---
title: PowerShell ile Azure CDN'yi yönetme | Microsoft Docs
description: Azure CDN'yi yönetmek için Azure PowerShell cmdlet'lerini kullanmayı öğrenin.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 44274c2a46ce51e51d3d8f16d96a0b50c43a3aa1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593701"
---
# <a name="manage-azure-cdn-with-powershell"></a>PowerShell ile Azure CDN'yi yönetme
PowerShell, Azure CDN profili ve uç noktaları yönetmek için en esnek yöntemi sağlar.  Yönetim görevlerini otomatik hale getirmek için etkileşimli olarak veya komut dosyası yazma PowerShell kullanabilirsiniz.  Bu öğretici, birkaç gösterir PowerShell ile Azure CDN profili ve uç noktaları yönetmek için en yaygın görevleri gerçekleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure CDN profili ve uç noktaları yönetmek için PowerShell'i kullanma hakkında bilgi için Azure PowerShell Modülü yüklü olmalıdır.  Azure PowerShell'i yükleme ve Azure kullanarak bağlanma hakkında bilgi edinmek için `Connect-AzAccount` cmdlet'ini bkz [Azure PowerShell'i yükleme ve yapılandırma işlemini](/powershell/azure/overview).

> [!IMPORTANT]
> İle oturum açmanız gerekir `Connect-AzAccount` önce Azure PowerShell cmdlet'lerini çalıştırabilirsiniz.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Azure CDN cmdlet öğelerini listeleme
Azure CDN cmdlet'leri kullanarak tüm listeleyebilirsiniz `Get-Command` cmdlet'i.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>Yardım alma
Kullanarak bu cmdlet'lerden herhangi birini ile Yardım alabilirsiniz `Get-Help` cmdlet'i.  `Get-Help` Kullanım ve söz dizimi sağlar ve isteğe bağlı olarak örnekleri gösterir.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Listenin mevcut Azure CDN profilleri
`Get-AzCdnProfile` Cmdlet'i parametre olmadan, tüm mevcut CDN profili alır.

```powershell
Get-AzCdnProfile
```

Bu çıkış, numaralandırma cmdlet'lerinin ayrıştırılabilir.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Ayrıca, profil adı ve kaynak grubu belirterek tek bir profil döndürebilir.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Farklı kaynak gruplarında bulunan oldukları sürece aynı ada sahip birden çok CDN profili olması mümkündür.  Atlama `ResourceGroupName` parametresi eşleşen bir ada sahip tüm profilleri döndürür.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Listenin mevcut CDN uç noktası
`Get-AzCdnEndpoint` tek bir uç nokta veya bir profildeki tüm uç noktalar alabilirsiniz.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>CDN profili ve uç noktaları oluşturma
`New-AzCdnProfile` ve `New-AzCdnEndpoint` CDN profili ve uç noktaları oluşturmak için kullanılır. Aşağıdaki SKU'ları desteklenir:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Uç nokta adın kullanılabilirliği denetleniyor
`Get-AzCdnEndpointNameAvailability` bir uç nokta adı kullanılabilir olup olmadığını gösteren bir nesne döndürür.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Özel bir etki alanı ekleme
`New-AzCdnCustomDomain` özel etki alanı için mevcut bir uç nokta ekler.

> [!IMPORTANT]
> CNAME DNS sağlayıcınızda açıklanan şekilde ayarlamanız gerekir [nasıl özel etki alanını Content Delivery Network (CDN) uç noktasına eşleme](cdn-map-content-to-custom-domain.md).  Uç noktayı kullanarak değiştirmeden önce eşleme sınayabilirsiniz `Test-AzCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Bir uç nokta değiştirme
`Set-AzCdnEndpoint` Varolan bir uç noktada değiştirir.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Temizleme/öncesi-loading CDN varlıklar
`Unpublish-AzCdnEndpointContent` temizler önbelleğe alınan varlıkları sırada `Publish-AzCdnEndpointContent` desteklenen uç noktasında varlıkları önceden yükler.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Başlatma/durdurma CDN uç noktası
`Start-AzCdnEndpoint` ve `Stop-AzCdnEndpoint` başlatıp tekil uç noktalarını ya da uç noktalar grupları durdurmak için kullanılabilir.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>CDN kaynakları silme
`Remove-AzCdnProfile` ve `Remove-AzCdnEndpoint` profillerini ve uç noktalarını kaldırmak için kullanılabilir.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Sonraki Adımlar
Azure CDN’yi [.NET](cdn-app-dev-net.md) veya [Node.js](cdn-app-dev-node.md) ile nasıl otomatik hale getireceğinizi öğrenin.

CDN özellikleri hakkında bilgi edinmek için [CDN'ye genel bakış](cdn-overview.md).


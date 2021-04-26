---
title: Belirteçler için yaşam sürelerini ayarlama
titleSuffix: Microsoft identity platform
description: Microsoft Identity platform tarafından verilen belirteçler için yaşam sürelerini ayarlamayı öğrenin. Bir kuruluşun varsayılan ilkesini yönetmeyi, Web 'de oturum açma ilkesi oluşturmayı, bir Web API 'SI çağıran yerel uygulama için ilke oluşturmayı ve gelişmiş bir ilkeyi yönetmeyi öğrenin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363895"
---
# <a name="configure-token-lifetime-policies-preview"></a>Belirteç ömür ilkelerini yapılandırma (Önizleme)
Microsoft Identity platform tarafından verilen erişim, SAML veya KIMLIK belirtecinin ömrünü belirtebilirsiniz. Kuruluşunuzdaki tüm uygulamalar, çok kiracılı (çok kuruluşlu) bir uygulama veya belirli bir hizmet sorumlusu için belirteç ömrünü ayarlayabilirsiniz. Daha fazla bilgi için, [yapılandırılabilir belirteç yaşam sürelerini](active-directory-configurable-token-lifetimes.md)okuyun.

Bu bölümde, belirteç ömrü için yeni kurallar almanıza yardımcı olabilecek ortak bir ilke senaryosuna kılavuzluk ederiz. Örnekte, kullanıcıların Web uygulamanızda daha sık kimlik doğrulaması yapmasını gerektiren bir ilke oluşturmayı öğreneceksiniz.

## <a name="get-started"></a>başlarken

Başlamak için en son [Azure AD PowerShell modülü genel önizleme sürümünü](https://www.powershellgallery.com/packages/AzureADPreview)indirin.

Sonra, `Connect` Azure AD yönetici hesabınızda oturum açmak için komutunu çalıştırın. Her yeni oturumu başlattığınızda bu komutu çalıştırın.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Web 'de oturum açma ilkesi oluşturma

Bu örnekte, kullanıcıların Web uygulamanızda daha sık kimlik doğrulaması yapmasını gerektiren bir ilke oluşturacaksınız. Bu ilke, erişim/KIMLIK belirteçlerinin ömrünü Web uygulamanızın hizmet sorumlusu olarak ayarlar.

1. Belirteç ömür ilkesi oluşturun.

    Bu ilke, Web 'de oturum açmak için erişim/KIMLIK belirteci ömrünü iki saate ayarlar.

    İlkeyi oluşturmak için [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet 'ini çalıştırın:

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Yeni ilkenize bakmak ve ilke **ObjectID**'yi almak için [Get-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet 'ini çalıştırın:

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. İlkeyi hizmet sorumlusuna atayın. Ayrıca hizmet sorumlunun **ObjectID** 'sini almanız gerekir.

    Tüm kuruluşunuzun hizmet sorumlularını veya tek bir hizmet sorumlusunu görmek için [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet 'ini kullanın.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    Hizmet sorumlusu varsa [Add-Azureadservicesprincipalpolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet 'ini çalıştırın:

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Bir Kiracıdaki mevcut ilkeleri görüntüleme

Kuruluşunuzda oluşturulan tüm ilkeleri görmek için [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet 'ini çalıştırın.  Yukarıda listelenen varsayılandan farklı olarak tanımlanmış özellik değerleri olan sonuçlar, emeklilik kapsamıdır.

```powershell
Get-AzureADPolicy -All $true
```

Belirlediğiniz belirli bir ilkeye bağlı olan uygulamaları ve hizmet sorumlularını görmek için, aşağıdaki [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) cmdlet 'ini çalıştırarak **1a37dad8-5dad7-4cc8-87c7-efbc0326cf20** ' i ilke kimliklerinizle değiştirin. Daha sonra koşullu erişim oturum açma sıklığını yapılandırıp yapılandırmamaya veya Azure AD varsayılanları ile kalmaya karar verebilirsiniz.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Kiracınızda yenileme ve oturum belirteci yapılandırma özellikleri için özel değerler tanımlayan ilkeler varsa, Microsoft bu ilkeleri yukarıda belirtilen Varsayılanları yansıtan değerlere güncelleştirmenizi önerir. Hiçbir değişiklik yapılgerekmediğinden, Azure AD varsayılan değerleri otomatik olarak kabul eder.

### <a name="troubleshooting"></a>Sorun giderme
Bazı kullanıcılar `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` cmdlet 'ini çalıştırdıktan sonra bir hata bildirdi `Get-AzureADPolicy` . Geçici bir çözüm olarak, AzureAD modülünü kaldırmak/yeniden yüklemek ve ardından AzureADPreview modülünü yüklemek için aşağıdaki komutu çalıştırın:

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Sonraki adımlar
Azure AD koşullu erişim 'de [kimlik doğrulama oturumu yönetimi özellikleri](../conditional-access/howto-conditional-access-session-lifetime.md) hakkında bilgi edinin.

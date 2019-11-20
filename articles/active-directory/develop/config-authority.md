---
title: İOS ve macOS için MSAL 'ı farklı kimlik sağlayıcıları kullanacak şekilde yapılandırma
titleSuffix: Microsoft identity platform
description: MSAL for iOS ve MacOS ile B2C, bağımsız bulutları ve Konuk kullanıcılar gibi farklı yetkilileri nasıl kullanacağınızı öğrenin.
services: active-directory
documentationcenter: ''
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4df5067317170e18a52fbac53bc02a2ba7d35aba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473800"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Nasıl yapılır: iOS ve macOS için MSAL 'ı farklı kimlik sağlayıcıları kullanacak şekilde yapılandırma

Bu makalede, iOS ve MacOS için Microsoft kimlik doğrulama kitaplığı uygulamanızı (msal) Azure Active Directory (Azure AD), işletmeden müşteriye (B2C), bağımsız ve Konuk kullanıcılar gibi farklı yetkililer için nasıl yapılandıracağınız gösterilir.  Bu makalede, genellikle kimlik sağlayıcısı olarak bir yetkilendirmeyi düşünebilirsiniz.

## <a name="default-authority-configuration"></a>Varsayılan yetkili yapılandırması

`MSALPublicClientApplication`, en Azure Active Directory (AAD) senaryosu için uygun olan `https://login.microsoftonline.com/common`varsayılan yetkili URL 'SI ile yapılandırılır. Ulusal bulutlar gibi gelişmiş senaryolar uygulamadıysanız veya B2C ile çalışmadığınız takdirde, bunu değiştirmeniz gerekmez.

> [!NOTE]
> Kimlik sağlayıcısı (ADFS) olarak Active Directory Federasyon Hizmetleri (AD FS) modern kimlik doğrulaması desteklenmez (Ayrıntılar için bkz. [geliştiriciler Için ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) ). ADFS, Federasyon aracılığıyla desteklenir.

## <a name="change-the-default-authority"></a>Varsayılan yetkiyi değiştirme

İşletmeden müşteriye (B2C) gibi bazı senaryolarda varsayılan yetkiyi değiştirmeniz gerekebilir.

### <a name="b2c"></a>B2C

B2C ile çalışmak için, [Microsoft kimlik doğrulama kitaplığı (msal)](reference-v2-libraries.md) farklı bir yetki yapılandırması gerektirir. MSAL, tek bir yetkili URL biçimini kendi B2C olarak tanır. Tanınan B2C yetkilisi biçimi `https://<host>/tfp/<tenant>/<policy>`, örneğin `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`. Bununla birlikte, yetkiyi B2C yetkilisi olarak açıkça bildirerek desteklenen herhangi bir B2C yetkilisi URL 'sini de kullanabilirsiniz.

B2C için rastgele bir URL biçimi desteklemek üzere `MSALB2CAuthority` aşağıdaki gibi rastgele bir URL ile ayarlanabilir:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Varsayılan B2C yetkilisi biçimini kullanmayan tüm B2C yetkilileri bilinen yetkililer olarak bildirilmelidir.

Yetkililer yalnızca ilkede farklıysa bile, her farklı B2C yetkilisini bilinen yetkililer listesine ekleyin.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Uygulamanız yeni bir ilke istediğinde, yetkili URL her ilke için farklı olduğundan, yetkili URL 'sinin değiştirilmesi gerekir. 

B2C uygulamasını yapılandırmak için, `MSALPublicClientApplication`oluşturmadan önce `MSALPublicClientApplicationConfig` bir `MSALB2CAuthority` örneğiyle `@property MSALAuthority *authority` ayarlayın:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Bağımsız bulutlar

Uygulamanız bir bağımsız bulutu 'nda çalışıyorsa, `MSALPublicClientApplication`yetkili URL 'sini değiştirmeniz gerekebilir. Aşağıdaki örnek, bir yetkili URL 'YI Almanya AAD bulutu ile çalışacak şekilde ayarlar:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Her bir bağımsız bulutuna farklı kapsamlar geçirmeniz gerekebilir. Hangi kapsamların gönderileceği, kullanmakta olduğunuz kaynağa bağlıdır. Örneğin, dünya çapındaki bulutta `"https://graph.microsoft.com/user.read"` ve Almanya bulutu 'nda `"https://graph.microsoft.de/user.read"` kullanabilirsiniz.

### <a name="signing-a-user-into-a-specific-tenant"></a>Kullanıcıyı belirli bir kiracıya imzalama

Yetkili URL 'SI `"login.microsoftonline.com/common"`olarak ayarlandığında, Kullanıcı kendi giriş kiracısında oturum açacaktır. Ancak bazı uygulamaların kullanıcı farklı bir kiracıda oturum açması gerekebilir ve bazı uygulamalar yalnızca tek bir kiracı ile çalışır.

Kullanıcıyı belirli bir kiracıda imzalamak için `MSALPublicClientApplication` belirli bir yetkiliyle yapılandırın. Örneğin:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Aşağıda, bir kullanıcının belirli bir kiracıya nasıl imzalanasının yapılacağı gösterilmektedir:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Desteklenen yetkililer

### <a name="msalauthority"></a>MSALAuthority

`MSALAuthority` sınıfı, MSAL Authority sınıfları için temel soyut sınıftır. `alloc` veya `new`kullanarak örneğini oluşturmayı denemeyin. Bunun yerine, alt sınıflarından birini doğrudan oluşturun (`MSALAADAuthority`, `MSALB2CAuthority`) ya da bir yetkili URL 'SI kullanarak alt sınıflar oluşturmak için `authorityWithURL:error:` Factory metodunu kullanın.

Normalleştirilmiş bir yetkili URL 'SI almak için `url` özelliğini kullanın. Ek parametreler ve yol bileşenleri veya yetkilinin parçası olmayan parçalar döndürülen normalleştirilmiş yetkilendirme URL 'sinde olmayacaktır.

Aşağıda, kullanmak istediğiniz yetkiliye göre örneklenebilen `MSALAuthority` alt sınıfları verilmiştir.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` bir AAD yetkilisini temsil eder. Yetkili URL 'si aşağıdaki biçimde olmalıdır; burada `<port>` isteğe bağlıdır: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` bir B2C yetkilisini temsil eder. Varsayılan olarak, B2C yetkilisi URL 'si aşağıdaki biçimde olmalıdır; burada `<port>` isteğe bağlıdır: `https://<host>:<port>/tfp/<tenant>/<policy>`. Ancak, MSAL diğer isteğe bağlı B2C yetkilendirme biçimlerini de destekler.

## <a name="next-steps"></a>Sonraki adımlar

[Kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md) hakkında daha fazla bilgi edinin

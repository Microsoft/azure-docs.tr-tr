---
title: Kimlik sağlayıcılarını yapılandırma (MSAL iOS/macOS) | Mavisi
titleSuffix: Microsoft identity platform
description: MSAL for iOS ve MacOS ile B2C, bağımsız bulutları ve Konuk kullanıcılar gibi farklı yetkilileri nasıl kullanacağınızı öğrenin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d8a176fff0da932d0fafd40b9ab895b635acc5f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96169452"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Nasıl yapılır: iOS ve macOS için MSAL 'ı farklı kimlik sağlayıcıları kullanacak şekilde yapılandırma

Bu makalede, iOS ve MacOS için Microsoft kimlik doğrulama kitaplığı uygulamanızı (msal) Azure Active Directory (Azure AD), işletmeden müşteriye (B2C), bağımsız ve Konuk kullanıcılar gibi farklı yetkililer için nasıl yapılandıracağınız gösterilir.  Bu makalede, genellikle kimlik sağlayıcısı olarak bir yetkilendirmeyi düşünebilirsiniz.

## <a name="default-authority-configuration"></a>Varsayılan yetkili yapılandırması

`MSALPublicClientApplication``https://login.microsoftonline.com/common`, en çok Azure Active Directory (AAD) senaryosu için uygun olan varsayılan bir yetkili URL 'si ile yapılandırılır. Ulusal bulutlar gibi gelişmiş senaryolar uygulamadıysanız veya B2C ile çalışmadığınız takdirde, bunu değiştirmeniz gerekmez.

> [!NOTE]
> Kimlik sağlayıcısı (ADFS) olarak Active Directory Federasyon Hizmetleri (AD FS) modern kimlik doğrulaması desteklenmez (Ayrıntılar için bkz. [geliştiriciler Için ADFS](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) ). ADFS, Federasyon aracılığıyla desteklenir.

## <a name="change-the-default-authority"></a>Varsayılan yetkiyi değiştirme

İşletmeden müşteriye (B2C) gibi bazı senaryolarda varsayılan yetkiyi değiştirmeniz gerekebilir.

### <a name="b2c"></a>B2C

B2C ile çalışmak için, [Microsoft kimlik doğrulama kitaplığı (msal)](reference-v2-libraries.md) farklı bir yetki yapılandırması gerektirir. MSAL, tek bir yetkili URL biçimini kendi B2C olarak tanır. Örneğin, tanınan B2C yetkilisinin biçimi `https://<host>/tfp/<tenant>/<policy>` `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` . Bununla birlikte, yetkiyi B2C yetkilisi olarak açıkça bildirerek desteklenen herhangi bir B2C yetkilisi URL 'sini de kullanabilirsiniz.

B2C için rastgele bir URL biçimi desteklemek amacıyla, aşağıdaki `MSALB2CAuthority` gibi rastgele BIR URL ile ayarlanabilir:

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

B2C uygulamasını yapılandırmak için, `@property MSALAuthority *authority` oluşturmadan önce ' ın bir örneği ile ayarlayın `MSALB2CAuthority` `MSALPublicClientApplicationConfig` , örneğin `MSALPublicClientApplication` :

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

Uygulamanız bir sogeign bulutu 'nda çalışıyorsa, içindeki yetkili URL 'sini değiştirmeniz gerekebilir `MSALPublicClientApplication` . Aşağıdaki örnek, bir yetkili URL 'YI Almanya AAD bulutu ile çalışacak şekilde ayarlar:

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

Her bir bağımsız bulutuna farklı kapsamlar geçirmeniz gerekebilir. Hangi kapsamların gönderileceği, kullanmakta olduğunuz kaynağa bağlıdır. Örneğin, `"https://graph.microsoft.com/user.read"` dünya çapındaki bulutta ve `"https://graph.microsoft.de/user.read"` Almanya bulutu 'nda kullanabilirsiniz.

### <a name="signing-a-user-into-a-specific-tenant"></a>Kullanıcıyı belirli bir kiracıya imzalama

Yetkili URL 'SI olarak ayarlandığında `"login.microsoftonline.com/common"` , Kullanıcı kendi giriş kiracısında oturum açacaktır. Ancak bazı uygulamaların kullanıcı farklı bir kiracıda oturum açması gerekebilir ve bazı uygulamalar yalnızca tek bir kiracı ile çalışır.

Kullanıcıyı belirli bir kiracıda imzalamak için `MSALPublicClientApplication` belirli bir yetkiliyle yapılandırın. Örnek:

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

`MSALAuthority`Sınıfı, msal Authority sınıfları için temel soyut sınıftır. Veya kullanarak örneğini oluşturmayı denemeyin `alloc` `new` . Bunun yerine, alt sınıflarından birini doğrudan ( `MSALAADAuthority` ,) oluşturun `MSALB2CAuthority` ya da `authorityWithURL:error:` bir yetkili URL 'si kullanarak alt sınıflar oluşturmak için Factory yöntemini kullanın.

`url`Normalleştirilmiş bir yetkilendirme URL 'si almak için özelliğini kullanın. Ek parametreler ve yol bileşenleri veya yetkilinin parçası olmayan parçalar döndürülen normalleştirilmiş yetkilendirme URL 'sinde olmayacaktır.

Aşağıda, `MSALAuthority` kullanmak istediğiniz yetkiliye bağlı olarak örneklenebilen alt sınıfları verilmiştir.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` bir AAD yetkilisini temsil eder. Yetkili URL 'sinin aşağıdaki biçimde olması gerekir; burada, `<port>` isteğe bağlıdır: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` bir B2C yetkilisini temsil eder. Varsayılan olarak, B2C yetkilisi URL 'si aşağıdaki biçimde olmalıdır; burada `<port>` isteğe bağlıdır: `https://<host>:<port>/tfp/<tenant>/<policy>` . Ancak, MSAL diğer isteğe bağlı B2C yetkilendirme biçimlerini de destekler.

## <a name="next-steps"></a>Sonraki adımlar

[Kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md) hakkında daha fazla bilgi edinin
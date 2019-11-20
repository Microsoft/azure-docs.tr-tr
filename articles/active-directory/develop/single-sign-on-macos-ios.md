---
title: MacOS ve iOS 'ta SSO 'yu yapılandırma
titleSuffix: Microsoft identity platform
description: MacOS ve iOS 'ta çoklu oturum açma (SSO) yapılandırma hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd944af95f80cf456260beb072c703aab0d15ceb
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175284"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Nasıl yapılır: macOS ve iOS 'ta SSO 'yu yapılandırma

MacOS ve iOS için Microsoft kimlik doğrulama kitaplığı (MSAL), macOS/iOS uygulamaları ve tarayıcıları arasında çoklu oturum açmayı (SSO) destekler. Bu makalede aşağıdaki SSO senaryoları ele alınmaktadır:

- [Birden çok uygulama arasında sessiz SSO](#silent-sso-between-apps)

Bu tür SSO, aynı Apple geliştiricisi tarafından dağıtılan birden çok uygulama arasında çalışmaktadır. Anahtar anahtardan diğer uygulamalar tarafından yazılan yenileme belirteçlerini okuyarak ve bunları sessizce erişim belirteçleri için değiştirerek sessiz SSO (yani, Kullanıcı kimlik bilgileri istenmez) sağlar.  

- [Kimlik doğrulama Aracısı aracılığıyla SSO](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Bu akış macOS 'ta kullanılamaz.

Microsoft, mobil cihaz Azure Active Directory (AAD) ile kaydedildiği sürece farklı satıcılardan uygulamalar arasında SSO 'yu etkinleştiren aracılar adlı uygulamalar sağlar. Bu tür SSO, kullanıcının cihazında bir aracı uygulamasının yüklü olmasını gerektirir.

- **MSAL ve Safari arasındaki SSO**

SSO, [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) sınıfı aracılığıyla elde edilir. Diğer uygulamalardan ve Safari tarayıcısından var olan oturum açma durumunu kullanır. Aynı Apple geliştiricisi tarafından dağıtılan uygulamalarla sınırlı değildir, ancak bazı kullanıcı etkileşimini gerektirir.

Uygulamanızda oturum açmak için varsayılan Web görünümünü kullanıyorsanız, MSAL tabanlı uygulamalar ve Safari arasında otomatik SSO alırsınız. MSAL tarafından desteklenen Web görünümleri hakkında daha fazla bilgi edinmek için [tarayıcıları ve Web görünümlerini özelleştirme](customize-webviews.md)makalesini ziyaret edin.

> [!IMPORTANT]
> Bu tür SSO, macOS 'ta Şu anda kullanılamıyor. MacOS on MSAL, yalnızca Safari ile SSO desteği olmayan WKWebView 'u destekliyor. 

- **ADAL ve MSAL macOS/iOS uygulamaları arasında sessiz SSO**

MSAL hedefi-C, ADAL hedefi-C tabanlı uygulamalarla geçişi ve SSO 'yu destekler. Uygulamalar aynı Apple geliştiricisi tarafından dağıtılmalıdır.

ADAL ve MSAL tabanlı uygulamalar arasında platformlar arası SSO için yönergeler için [macOS ve iOS 'TA adal ve msal uygulamaları arasındaki SSO](sso-between-adal-msal-apps-macos-ios.md) 'yu inceleyin.

## <a name="silent-sso-between-apps"></a>Uygulamalar arasında sessiz SSO

MSAL, iOS Anahtarlık erişim grupları aracılığıyla SSO paylaşımını destekler.

Uygulamalarınızda SSO 'yu etkinleştirmek için aşağıda daha ayrıntılı olarak açıklanan aşağıdaki adımları gerçekleştirmeniz gerekir:

1. Tüm uygulamalarınızın aynı Istemci KIMLIĞINI veya uygulama KIMLIĞINI kullandığından emin olun.
1. Anahtar zincirlerini paylaşabilmek için tüm uygulamalarınızın Apple 'dan aynı imzalama sertifikasını paylaştığından emin olun.
1. Uygulamalarınızın her biri için aynı Anahtarlık yetkilendirmesini isteyin.
1. MSAL SDK 'larına, varsayılan bir bilgisayardan farklıysa, kullanmamızı istediğiniz paylaşılan Anahtarlık hakkında bilgi verin.

### <a name="use-the-same-client-id-and-application-id"></a>Aynı Istemci KIMLIĞINI ve uygulama KIMLIĞINI kullanın

Hangi uygulamaların belirteçleri paylaşabileceği hakkında bilgi sahibi olmak için Microsoft Identity platformu için, bu uygulamaların aynı Istemci KIMLIĞINI veya uygulama KIMLIĞINI paylaşması gerekir. Bu, portalda ilk uygulamanızı kaydettiğinizde size sağlanmış olan benzersiz tanıtıcıdır.

Microsoft Identity platform 'un aynı uygulama KIMLIĞINI kullanan uygulamalara **yeniden yönlendirme URI 'leri**tarafından nasıl olduğunu söyleme şekli. Her uygulamanın, ekleme portalında kayıtlı birden çok yeniden yönlendirme URI 'si olabilir. Paketinizdeki her uygulamanın farklı bir yeniden yönlendirme URI 'SI olacaktır. Örnek:

APP1 Redirect URI: `msauth.com.contoso.mytestapp1://auth` app2 yeniden yönlendirme URI 'SI: `msauth.com.contoso.mytestapp2://auth` App3 yeniden yönlendirme URI 'si: `msauth.com.contoso.mytestapp3://auth`

> [!IMPORTANT]
> Yeniden yönlendirme URI 'lerinin biçimi, [msal yeniden yönlendirme URI 'si biçim gereksinimleri](redirect-uris-ios.md#msal-redirect-uri-format-requirements)bölümünde belgelenen msal desteği biçimiyle uyumlu olmalıdır.

### <a name="setup-keychain-sharing-between-applications"></a>Uygulamalar arasında Anahtarlık paylaşımı kurma

Anahtarlık paylaşımını etkinleştirmek için Apple 'ın [özellik ekleme](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) makalesine bakın. Önemli olan şey, anahtarlarınızın ne şekilde çağrdığına karar vereceğinize ve bu yeteneği SSO 'ya dahil edilecek tüm uygulamalarınıza eklemektir.

Yetkilendirmeler doğru şekilde ayarlandığında, proje dizininizde bu örneğe benzer bir şey içeren bir `entitlements.plist` dosyası görürsünüz:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Her bir uygulamanızda Anahtarlık yetkilendirme yetkilerini etkinleştirdikten sonra, SSO kullanmaya hazırsanız, aşağıdaki örnekte olduğu gibi Anahtarlık erişim grubumun `MSALPublicClientApplication` yapılandırın:

Amaç-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

SWIFT

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"
        
do {
    let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



> [!WARNING]
> Uygulamalarınız genelinde bir anahtarlık paylaştığınızda, herhangi bir uygulama kullanıcı veya hatta uygulamanızın tamamında tüm belirteçleri silebilir.
> Arka plan çalışması yapmak için belirteçleri kullanan uygulamalarınız varsa, bu özellikle etkili bir şekilde belirlenir.
> Anahtarlık paylaşımı, uygulamanız Microsoft Identity SDK kaldırma işlemlerini kullandığında çok dikkatli olmanız gerektiği anlamına gelir.

İşte bu kadar! Microsoft Identity SDK artık tüm uygulamalarınızda kimlik bilgilerini paylaşacaktır. Hesap listesi, uygulama örnekleri arasında da paylaşılır.

## <a name="sso-through-authentication-broker-on-ios"></a>İOS üzerinde kimlik doğrulama Aracısı aracılığıyla SSO

MSAL, Microsoft Authenticator ile aracılı kimlik doğrulama desteği sağlar. Microsoft Authenticator, AAD kayıtlı cihazlar için SSO sağlar ve uygulamanızın koşullu erişim ilkelerini izlemesine de yardımcı olur.

Aşağıdaki adımlar, uygulamanız için bir kimlik doğrulama Aracısı kullanarak SSO 'yu nasıl etkinleştirirsiniz:

1. Uygulamanın Info. plist dosyasında uygulama için bir aracı uyumlu yeniden yönlendirme URI 'SI biçimi kaydedin. Aracı uyumlu yeniden yönlendirme URI 'SI biçimi `msauth.<app.bundle.id>://auth`. ' < App. demeti. ID > ' ' değerini uygulamanızın paket KIMLIĞIYLE değiştirin. Örnek:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. `LSApplicationQueriesSchemes`altındaki uygulamanızın Info. plist ' e aşağıdaki şemaları ekleyin:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Geri çağırmaları işlemek için `AppDelegate.m` dosyanıza aşağıdakileri ekleyin:

    Amaç-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    SWIFT
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Xcode 11**kullanıyorsanız, bunun yerine msal geri çağırma işlemini `SceneDelegate` dosyasına yerleştirmeniz gerekir.
Daha eski iOS ile uyumluluk için hem UISceneDelegate hem de Uıapplicationdelegate 'i destekediyorsanız, MSAL geri çağrısının her iki dosyaya da yerleştirilmesi gerekir.

Amaç-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

SWIFT

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```
    
## <a name="next-steps"></a>Sonraki adımlar

[Kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md) hakkında daha fazla bilgi edinin

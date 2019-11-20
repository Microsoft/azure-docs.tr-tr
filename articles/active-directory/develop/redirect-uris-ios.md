---
title: Microsoft kimlik doğrulama kitaplığı ile yeniden yönlendirme URI 'Leri kullanma
titleSuffix: Microsoft identity platform
description: ObjectiveC için Microsoft kimlik doğrulama kitaplığı (iOS ve macOS için MSAL) ve ObjectiveC (ADAL için Azure AD kimlik doğrulama kitaplığı) arasındaki farklar hakkında bilgi edinin. ObjC) ve aralarında geçiş yapma.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: jak
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b78906a03eb9dc96fb58dea4ceabff953f2a3e4f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803791"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>İOS ve macOS için Microsoft kimlik doğrulama kitaplığı ile yeniden yönlendirme URI 'Leri kullanma

Bir kullanıcı kimliğini doğruladığında, Azure Active Directory (Azure AD), Azure AD uygulamasına kayıtlı yeniden yönlendirme URI 'sini kullanarak belirteci uygulamaya gönderir.

Microsoft kimlik doğrulama kitaplığı (MSAL), yeniden yönlendirme URI 'sinin Azure AD uygulamasına belirli bir biçimde kaydedilmesini gerektirir. MSAL, bir varsayılan yeniden yönlendirme URI 'sini belirtmezseniz kullanır. Biçim `msauth.[Your_Bundle_Id]://auth`.

Varsayılan yeniden yönlendirme URI biçimi, aracılı kimlik doğrulaması ve sistem Web görünümü dahil olmak üzere çoğu uygulama ve senaryo için geçerlidir. Mümkün olan her seferinde varsayılan biçimi kullanın.

Ancak, aşağıda açıklandığı gibi gelişmiş senaryolar için yeniden yönlendirme URI 'sini değiştirmeniz gerekebilir.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Farklı bir yeniden yönlendirme URI 'SI gerektiren senaryolar

### <a name="cross-app-single-sign-on-sso"></a>Çoklu uygulama çoklu oturum açma (SSO)

Microsoft Identity platformu 'nın uygulamalar arasında belirteçleri paylaşması için, her uygulamanın aynı istemci KIMLIĞINE veya uygulama KIMLIĞINE sahip olması gerekir. Bu, uygulamanızı portalda kaydettiğinizde (Apple ile uygulama başına kaydettiğiniz uygulama paketi KIMLIĞINI değil), belirtilen benzersiz tanıtıcıdır.

Yeniden yönlendirme URI 'Lerinin her bir iOS uygulaması için farklı olması gerekir. Bu, Microsoft Identity Service 'in bir uygulama KIMLIĞINI paylaşan farklı uygulamaları benzersiz şekilde tanımlamasına olanak sağlar. Her uygulamanın Azure portal kayıtlı birden fazla yeniden yönlendirme URI 'si olabilir. Paketinizdeki her uygulamanın farklı bir yeniden yönlendirme URI 'SI olacaktır. Örnek:

Azure portal aşağıdaki uygulama kaydı verildiğinde:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

APP1 App3 kullanımları `msauth.com.contoso.app1://auth` kullanarak yeniden yönlendirme `msauth.com.contoso.app1://auth` app2 kullanır `msauth.com.contoso.app2://auth`

### <a name="migrating-from-adal-to-msal"></a>ADAL 'dan MSAL 'e geçiş

Azure AD kimlik doğrulama kitaplığı 'nı (ADAL) kullanan kodu MSAL 'e geçirirken, uygulamanız için yapılandırılmış bir yeniden yönlendirme URI 'SI zaten olabilir. ADAL uygulamanızın aracılı senaryoları destekleyecek şekilde yapılandırıldığı ve yeniden yönlendirme URI 'si MSAL yeniden yönlendirme URI 'SI biçim gereksinimlerini karşıladığından, aynı yeniden yönlendirme URI 'sini kullanmaya devam edebilirsiniz.

## <a name="msal-redirect-uri-format-requirements"></a>MSAL yeniden yönlendirme URI biçimi gereksinimleri

* MSAL yeniden yönlendirme URI 'SI şu biçimde olmalıdır `<scheme>://host`

    Burada `<scheme>`, uygulamanızı tanımlayan benzersiz bir dizedir. Bu, öncelikle, benzersizlik sağlamak için uygulamanızın paket tanımlayıcısına dayalıdır. Örneğin, uygulamanızın paket KIMLIĞI `com.contoso.myapp`ise, yeniden yönlendirme URI 'niz şu biçimde olur: `msauth.com.contoso.myapp://auth`.

    ADAL 'dan geçiş yapıyorsanız, yeniden yönlendirme URI 'SI muhtemelen bu biçime sahip olur: `<scheme>://[Your_Bundle_Id]`, `scheme` benzersiz bir dizedir. Bu biçim, MSAL kullandığınızda çalışmaya devam edecektir.

* `<scheme>`, `CFBundleURLTypes > CFBundleURLSchemes`altındaki uygulamanızın Info. plist dosyasında kayıtlı olmalıdır.  Bu örnekte, Info. plist kaynak kodu olarak açıldı:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

MSAL, yeniden yönlendirme URI 'nizin doğru şekilde kaydolmadığını doğrular ve değilse bir hata döndürür.
    
* Evrensel bağlantıları yeniden yönlendirme URI 'SI olarak kullanmak istiyorsanız, `<scheme>` `https` olmalı ve `CFBundleURLSchemes`olarak bildirilmesine gerek yoktur. Bunun yerine, uygulama ve etki alanını Apple 'ın [evrensel bağlantılarında geliştiriciler için](https://developer.apple.com/ios/universal-links/) yapılandırın ve uygulamanız bir evrensel bağlantı aracılığıyla açıldığında `MSALPublicClientApplication` `handleMSALResponse:sourceApplication:` yöntemini çağırın.

## <a name="use-a-custom-redirect-uri"></a>Özel yeniden yönlendirme URI 'SI kullan

Özel bir yeniden yönlendirme URI 'SI kullanmak için `redirectUri` parametresini `MSALPublicClientApplicationConfig` geçirin ve nesneyi başlattığınızda bu nesneyi `MSALPublicClientApplication` geçirin. Yeniden yönlendirme URI 'SI geçersizse, başlatıcı `nil` döndürür ve ek bilgilerle `redirectURIError`ayarlar.  Örnek:

Amaç-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

SWIFT

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Açılan URL olayını işle

Uygulamanız, URL şemaları veya evrensel bağlantılar aracılığıyla herhangi bir yanıt aldığında MSAL öğesini çağırmalıdır. Uygulamanız açıldığında `MSALPublicClientApplication` `handleMSALResponse:sourceApplication:` yöntemini çağırın. Özel düzenler için bir örnek aşağıda verilmiştir:

Amaç-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

SWIFT

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Sonraki adımlar

[Kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md) hakkında daha fazla bilgi edinin

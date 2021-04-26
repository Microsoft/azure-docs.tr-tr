---
title: MSAL ile yeniden yönlendirme URI 'Leri kullanma (iOS/macOS) | Mavisi
titleSuffix: Microsoft identity platform
description: ObjectiveC için Microsoft kimlik doğrulama kitaplığı (iOS ve macOS için MSAL) ve ObjectiveC (ADAL için Azure AD kimlik doğrulama kitaplığı) arasındaki farklar hakkında bilgi edinin. ObjC) ve aralarında geçiş yapma.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 2c728854846a3add1f5b41cb318d7dc5fd86e742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064207"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>İOS ve macOS için Microsoft kimlik doğrulama kitaplığı ile yeniden yönlendirme URI 'Leri kullanma

Bir kullanıcı kimliğini doğruladığında, Azure Active Directory (Azure AD), Azure AD uygulamasına kayıtlı yeniden yönlendirme URI 'sini kullanarak belirteci uygulamaya gönderir.

Microsoft kimlik doğrulama kitaplığı (MSAL), yeniden yönlendirme URI 'sinin Azure AD uygulamasına belirli bir biçimde kaydedilmesini gerektirir. MSAL, bir varsayılan yeniden yönlendirme URI 'sini belirtmezseniz kullanır. Biçim `msauth.[Your_Bundle_Id]://auth` .

Varsayılan yeniden yönlendirme URI biçimi, aracılı kimlik doğrulaması ve sistem Web görünümü dahil olmak üzere çoğu uygulama ve senaryo için geçerlidir. Mümkün olan her seferinde varsayılan biçimi kullanın.

Ancak, aşağıda açıklandığı gibi gelişmiş senaryolar için yeniden yönlendirme URI 'sini değiştirmeniz gerekebilir.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Farklı bir yeniden yönlendirme URI 'SI gerektiren senaryolar

### <a name="cross-app-single-sign-on-sso"></a>Çoklu uygulama çoklu oturum açma (SSO)

Microsoft Identity platformu 'nın uygulamalar arasında belirteçleri paylaşması için, her uygulamanın aynı istemci KIMLIĞINE veya uygulama KIMLIĞINE sahip olması gerekir. Bu, uygulamanızı portalda kaydettiğinizde (Apple ile uygulama başına kaydettiğiniz uygulama paketi KIMLIĞINI değil), belirtilen benzersiz tanıtıcıdır.

Yeniden yönlendirme URI 'Lerinin her bir iOS uygulaması için farklı olması gerekir. Bu, Microsoft Identity Service 'in bir uygulama KIMLIĞINI paylaşan farklı uygulamaları benzersiz şekilde tanımlamasına olanak sağlar. Her uygulamanın Azure portal kayıtlı birden fazla yeniden yönlendirme URI 'si olabilir. Paketinizdeki her uygulamanın farklı bir yeniden yönlendirme URI 'SI olacaktır. Örnek:

Azure portal aşağıdaki uygulama kaydı verildiğinde:

* İstemci KIMLIĞI: `ABCDE-12345` (Bu tek bir ISTEMCI kimliğidir)
* Redirecturdir: `msauth.com.contoso.app1://auth` , `msauth.com.contoso.app2://auth` , `msauth.com.contoso.app3://auth`

APP1 yeniden yönlendirme kullanır `msauth.com.contoso.app1://auth` . \
App2 kullanımları `msauth.com.contoso.app2://auth` . \
App3 kullanır `msauth.com.contoso.app1://auth` .

### <a name="migrating-from-adal-to-msal"></a>ADAL 'dan MSAL 'e geçiş

Azure AD kimlik doğrulama kitaplığı 'nı (ADAL) kullanan kodu MSAL 'e geçirirken, uygulamanız için yapılandırılmış bir yeniden yönlendirme URI 'SI zaten olabilir. ADAL uygulamanızın aracılı senaryoları destekleyecek şekilde yapılandırıldığı ve yeniden yönlendirme URI 'si MSAL yeniden yönlendirme URI 'SI biçim gereksinimlerini karşıladığından, aynı yeniden yönlendirme URI 'sini kullanmaya devam edebilirsiniz.

## <a name="msal-redirect-uri-format-requirements"></a>MSAL yeniden yönlendirme URI biçimi gereksinimleri

* MSAL yeniden yönlendirme URI 'SI şu biçimde olmalıdır `<scheme>://host`

    `<scheme>`, Uygulamanızı tanımlayan benzersiz bir dizedir. Bu, öncelikle, benzersizlik sağlamak için uygulamanızın paket tanımlayıcısına dayalıdır. Örneğin, uygulamanızın paket KIMLIĞI ise `com.contoso.myapp` , yeniden yönlendirme URI 'si şu biçimdedir: `msauth.com.contoso.myapp://auth` .

    ADAL 'dan geçiş yapıyorsanız, yeniden yönlendirme URI 'SI muhtemelen şu biçime sahip olacaktır: `<scheme>://[Your_Bundle_Id]` , burada `scheme` benzersiz bir dizedir. Bu biçim, MSAL kullandığınızda çalışmaya devam edecektir.

* `<scheme>` Uygulamanızın Info. plist dosyasında kayıtlı olmalıdır `CFBundleURLTypes > CFBundleURLSchemes` .  Bu örnekte, Info. plist kaynak kodu olarak açıldı:

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
    
* Evrensel bağlantıları yeniden yönlendirme URI 'SI olarak kullanmak istiyorsanız, `<scheme>` olmalıdır `https` ve içinde bildirilmelidir `CFBundleURLSchemes` . Bunun yerine, uygulama ve etki alanını Apple 'ın [evrensel bağlantılarında geliştiriciler için](https://developer.apple.com/ios/universal-links/) yapılandırın ve `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication` uygulamanızın evrensel bir bağlantı üzerinden açıldığı yöntemi çağırın.

## <a name="use-a-custom-redirect-uri"></a>Özel yeniden yönlendirme URI 'SI kullan

Özel bir yeniden yönlendirme URI 'SI kullanmak için, `redirectUri` nesneyi başlattığınızda parametresini öğesine geçirin `MSALPublicClientApplicationConfig` ve öğesine geçirin `MSALPublicClientApplication` . Yeniden yönlendirme URI 'SI geçersizse, başlatıcı döndürür `nil` ve `redirectURIError` ek bilgilerle birlikte ayarlanır.  Örnek:

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

Uygulamanız, URL şemaları veya evrensel bağlantılar aracılığıyla herhangi bir yanıt aldığında MSAL öğesini çağırmalıdır. `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication` Uygulamanızın açıldığı zaman yöntemini çağırın. Özel düzenler için bir örnek aşağıda verilmiştir:

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

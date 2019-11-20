---
title: Tarayıcıları ve WebView’ları özelleştirme
titleSuffix: Microsoft identity platform
description: İOS ve macOS için MSAL tarafından kullanılan tarayıcı deneyiminin kullanıcıları oturum açmasını nasıl özelleştireceğinizi öğrenin
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
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
ms.openlocfilehash: fcb314e46094bb6c283a17508c35b7fc17e010e5
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803383"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Nasıl yapılır: iOS/macOS için tarayıcıları ve Web görünümlerini özelleştirme

Etkileşimli kimlik doğrulaması için bir Web tarayıcısı gerekir. İOS 'ta, Microsoft kimlik doğrulama kitaplığı (MSAL), kullanıcıların oturum açması için etkileşimli kimlik doğrulaması yapmak üzere varsayılan olarak sistem Web tarayıcısını (uygulamanızın üstünde görünebilir) kullanır. Sistem tarayıcısının kullanılması, çoklu oturum açma (SSO) durumunu diğer uygulamalarla ve Web uygulamalarıyla paylaşma avantajına sahiptir.

Yapılandırmayı, Web içeriğini görüntülemek için diğer seçeneklerle özelleştirerek deneyimi değiştirebilirsiniz, örneğin:

Yalnızca iOS için:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

İOS ve macOS için:

- [Wkwebview](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MacOS için MSAL yalnızca `WKWebView`destekler.

## <a name="system-browsers"></a>Sistem tarayıcıları

İOS için `ASWebAuthenticationSession`, `SFAuthenticationSession` ve `SFSafariViewController` sistem tarayıcıları olarak kabul edilir. Genel olarak, sistem tarayıcıları Safari tarayıcı uygulamasıyla tanımlama bilgilerini ve diğer Web sitesi verilerini paylaşır.

Varsayılan olarak, MSAL, iOS sürümünü dinamik olarak algılayacak ve bu sürümde sunulan önerilen sistem tarayıcısını seçmeyecektir. İOS 12 + ' de `ASWebAuthenticationSession` olur. 

| Sürüm | Web tarayıcısı |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

Geliştiriciler, MSAL uygulamaları için farklı bir sistem tarayıcısı da seçebilir:

- `SFAuthenticationSession`, `ASWebAuthenticationSession` ' in iOS 11 sürümüdür.
- `SFSafariViewController` daha genel amaçlıdır ve Web 'e göz atmak için bir arabirim sağlar ve oturum açma amaçları için de kullanılabilir. İOS 9 ve 10 ' da, tanımlama bilgileri ve diğer Web sitesi verileri Safari ile paylaşılır, ancak iOS 11 ve üzeri sürümlerde değildir.

## <a name="in-app-browser"></a>Uygulama içi tarayıcı

[Wkwebview](https://developer.apple.com/documentation/webkit/wkwebview) , Web içeriğini görüntüleyen bir uygulama içi tarayıcıdır. Tanımlama bilgilerini veya Web sitesi verilerini diğer **Wkwebview** örnekleriyle veya Safari tarayıcısıyla paylaşmaz. WKWebView, hem iOS hem de macOS için kullanılabilen platformlar arası bir tarayıcıdır.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Tanımlama bilgisi paylaşımı ve çoklu oturum açma (SSO) etkileri

Kullandığınız tarayıcı, tanımlama bilgilerini paylaşma nedeniyle SSO deneyimini etkiler. Aşağıdaki tablolar tarayıcı başına SSO deneyimlerini özetler.

| Teknoloji    | Tarayıcı türü  | iOS kullanılabilirliği | macOS kullanılabilirliği | Tanımlama bilgilerini ve diğer verileri paylaşır  | MSAL kullanılabilirliği | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Sistem | iOS12 ve yukarı | macOS 10,15 ve yukarı | Yes | yalnızca iOS | w/Safari örnekleri
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Sistem | iOS11 ve yukarı | Yok | Yes | yalnızca iOS |  w/Safari örnekleri
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Sistem | iOS11 ve yukarı | Yok | Hayır | yalnızca iOS | Hayır * *
| **SFSafariViewController** | Sistem | iOS10 | Yok | Yes | yalnızca iOS |  w/Safari örnekleri
| **WKWebView**  | Uygulama içi | iOS8 ve yukarı | macOS 10,10 ve yukarı | Hayır | iOS ve macOS | Hayır * *

\* * SSO 'nun çalışması için, belirteçlerin uygulamalar arasında paylaşılması gerekir. Bu, bir belirteç önbelleği veya iOS için Microsoft Authenticator gibi aracı uygulaması gerektirir.

## <a name="change-the-default-browser-for-the-request"></a>İstek için varsayılan tarayıcıyı değiştirme

`MSALWebviewParameters`' de aşağıdaki özelliği değiştirerek, UX gereksinimlerinize bağlı olarak, uygulama içi tarayıcı veya belirli bir sistem tarayıcısı kullanabilirsiniz:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Etkileşimli istek başına değiştirme

Her istek, `acquireTokenWithParameters:completionBlock:` API 'sine geçirmeden önce `MSALInteractiveTokenParameters.webviewParameters.webviewType` özelliğini değiştirerek varsayılan tarayıcıyı geçersiz kılmak üzere yapılandırılabilir.

Ayrıca, MSAL, `MSALInteractiveTokenParameters.webviewParameters.customWebView` özelliğini ayarlayarak özel bir `WKWebView` geçirmeyi destekler.

Örnek:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Özel bir WebView kullanıyorsanız, görüntülenen Web içeriğinin durumunu göstermek için bildirimler kullanılır, örneğin:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Seçenekler

MSAL tarafından desteklenen tüm Web tarayıcısı türleri [Msalwebviewtype numaralandırması](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47) içinde bildiriliyor

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Sonraki adımlar

[Kimlik doğrulama akışları ve uygulama senaryoları](authentication-flows-app-scenarios.md) hakkında daha fazla bilgi edinin

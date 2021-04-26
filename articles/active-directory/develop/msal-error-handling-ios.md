---
title: iOS/macOS için MSAL.js’de hataları ve özel durumları işleme
titleSuffix: Microsoft identity platform
description: İOS/macOS uygulamalarında hata ve özel durumları, koşullu erişim talep sorunlarını ve MSAL ' de yeniden denemeleri nasıl ele alabileceğinizi öğrenin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: f7f2abadb7586e1b19168eb46a54bad53caa05cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761104"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>iOS/macOS için MSAL.js’de hataları ve özel durumları işleme

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>İOS/macOS için MSAL 'de hata işleme

İOS ve macOS hatalarının tüm MSAL listesi [Msalerror numaralandırması](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)bölümünde listelenmiştir.

MSAL tarafından oluşturulan tüm hatalar `MSALErrorDomain` etki alanı ile döndürülür.

Sistem hataları için, MSAL `NSError` SISTEM API 'sinden orijinali döndürür. Örneğin, bir ağ bağlantısı olmaması nedeniyle belirteç alımı başarısız olursa, MSAL `NSURLErrorDomain` etki alanı ve kodla bir hata döndürür `NSURLErrorNotConnectedToInternet` .

İstemci tarafında en az aşağıdaki iki MSAL hatasını işlemenizi öneririz:

- `MSALErrorInteractionRequired`: Kullanıcının etkileşimli bir istek yapması gerekir. Bu hataya, zaman aşımına uğradı bir kimlik doğrulama oturumu veya ek kimlik doğrulama gereksinimleri gereksinimi gibi pek çok koşul neden olabilir. Kurtarmak için MSAL Interactive Token alma API 'sini çağırın. 

- `MSALErrorServerDeclinedScopes`: Bazı kapsamlar veya tüm kapsamlar reddedildi. Yalnızca verilen kapsamlarla devam edilip edilmeyeceğini veya oturum açma sürecini durdurmayı belirleyin.

> [!NOTE]
> `MSALInternalError`Enum yalnızca başvuru ve hata ayıklama için kullanılmalıdır. Çalışma zamanında bu hataları otomatik olarak işlemeye çalışmayın. Uygulamanız, altında kalan hatalardan biriyle karşılaşırsa `MSALInternalError` , ne olduğunu açıklayan genel bir kullanıcıya yönelik iletiyi göstermek isteyebilirsiniz.

Örneğin, `MSALInternalErrorBrokerResponseNotReceived` kullanıcının kimlik doğrulamasını tamamlamadığını ve uygulamaya el ile geri döndürmediği anlamına gelir. Bu durumda, uygulamanız kimlik doğrulamanın tamamlanmamış olduğunu açıklayan genel bir hata iletisi göstermelidir ve yeniden kimlik doğrulaması yapmayı denemeleri önerilir.

Aşağıdaki amaç-C örnek kodu, bazı genel hata koşullarını işlemek için en iyi yöntemleri gösterir.

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

İOS ve macOS için MSAL, hem etkileşimli hem de sessiz belirteç alma senaryolarında belirli talepler talep etmenizi sağlar.

Özel talepler istemek için `claimsRequest` veya ' de `MSALSilentTokenParameters` belirtin `MSALInteractiveTokenParameters` .

Daha fazla bilgi için bkz. [iOS IÇIN msal ve macOS kullanarak özel talepler isteme](request-custom-claims.md) .

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Sonraki adımlar

Sorunları tanılamanıza ve hata ayıklamanıza yardımcı olmak üzere [iOS IÇIN msal/macOS 'Ta günlüğe kaydetmeyi](msal-logging-ios.md) etkinleştirmeyi düşünün.

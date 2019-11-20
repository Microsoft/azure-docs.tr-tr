---
title: Hatalar ve özel durumlar (MSAL)
titleSuffix: Microsoft identity platform
description: MSAL uygulamalarında hataları ve özel durumları, koşullu erişimi ve talep sorunlarını nasıl ele alabileceğinizi öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5bfc5e6471d768b89a66610a2618bc1a44cf709d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145920"
---
# <a name="handle-msal-exceptions-and-errors"></a>MSAL özel durumlarını ve hatalarını işleme

Bu makalede, yaygın oturum açma hatalarını işlemeye yönelik farklı hata türleri ve önerilerin bir özeti verilmektedir.

## <a name="msal-error-handling-basics"></a>MSAL hata işleme temelleri

Microsoft kimlik doğrulama kitaplığı 'ndaki (MSAL) özel durumlar, uygulama geliştiricilerinin son kullanıcılara görüntüleme konusunda sorun gidermesi ve bu işlemleri sürdürmelerine yöneliktir. Özel durum iletileri yerelleştirilmemiş.

Özel durumları ve hataları işlerken, özel durum türünü ve hata kodunu kullanarak özel durumlar arasında ayrım yapabilirsiniz.  Hata kodlarının bir listesi için bkz. [kimlik doğrulama ve yetkilendirme hata kodları](reference-aadsts-error-codes.md).

Sessiz veya etkileşimli belirteç alma sırasında, uygulamalar, koşullu erişim (MFA, cihaz yönetimi, konum tabanlı kısıtlamalar), belirteç verme ve teminat ve Kullanıcı gibi oturum açma deneyimi sırasında hatalara gelebilir. özelliklerinin.

## <a name="msal-for-ios-and-macos-errors"></a>İOS ve macOS hataları için MSAL

Hataların tamamı listesi [Msalerror numaralandırması](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)bölümünde listelenmiştir.

MSAL tarafından oluşturulan tüm hatalar `MSALErrorDomain` etki alanı ile döndürülür.

Sistem hataları için, MSAL sistem API 'sinden özgün `NSError` döndürür. Örneğin, bir ağ bağlantısı olmaması nedeniyle belirteç alımı başarısız olursa, MSAL `NSURLErrorDomain` etki alanı ve `NSURLErrorNotConnectedToInternet` kodla bir hata döndürür.

İstemci tarafında en az aşağıdaki iki MSAL hatasını işlemenizi öneririz:

- `MSALErrorInteractionRequired`: kullanıcının etkileşimli bir istek yapması gerekir. Bu hataya, zaman aşımına uğradı bir kimlik doğrulama oturumu veya ek kimlik doğrulama gereksinimleri gereksinimi gibi pek çok koşul neden olabilir. Kurtarmak için MSAL Interactive Token alma API 'sini çağırın. 

- `MSALErrorServerDeclinedScopes`: bazı kapsamlar veya tüm kapsamlar reddedildi. Yalnızca verilen kapsamlarla devam edilip edilmeyeceğini veya oturum açma sürecini durdurmayı belirleyin.

> [!NOTE]
> `MSALInternalError` numaralandırması yalnızca başvuru ve hata ayıklama için kullanılmalıdır. Çalışma zamanında bu hataları otomatik olarak işlemeye çalışmayın. Uygulamanız `MSALInternalError`altına denk gelen hatalardan biriyle karşılaşırsa, ne olduğunu açıklayan genel bir kullanıcıya yönelik iletiyi göstermek isteyebilirsiniz.

Örneğin `MSALInternalErrorBrokerResponseNotReceived`, kullanıcının kimlik doğrulamasını tamamlamadığını ve uygulamaya el ile geri dönmediği anlamına gelir. Bu durumda, uygulamanız kimlik doğrulamanın tamamlanmamış olduğunu açıklayan genel bir hata iletisi göstermelidir ve yeniden kimlik doğrulaması yapmayı denemeleri önerilir.

Aşağıdaki amaç-C örnek kodu, bazı genel hata koşullarını işlemek için en iyi yöntemleri göstermektedir:

Objective-C
```ObjC
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

Swift
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

## <a name="msal-for-python-error-handling"></a>Python hata işleme için MSAL

Python için MSAL içinde, çoğu hata API çağrısından bir dönüş değeri olarak dağıtılır. Hata, Microsoft Identity platformundan gelen JSON yanıtını içeren bir sözlük olarak temsil edilir.

* Başarılı bir yanıt `"access_token"` anahtarını içerir. Yanıtın biçimi OAuth2 protokolü tarafından tanımlanır. Daha fazla bilgi için bkz. [5,1 başarılı yanıt](https://tools.ietf.org/html/rfc6749#section-5.1)
* Hata yanıtı `"error"` içerir ve genellikle `"error_description"`. Yanıtın biçimi OAuth2 protokolü tarafından tanımlanır. Daha fazla bilgi için bkz. [5,2 hata yanıtı](https://tools.ietf.org/html/rfc6749#section-5.2)

Bir hata döndürüldüğünde `"error_description"` anahtarı, okunabilir bir ileti içerir. Bu, genellikle Microsoft Identity platform hata kodunu içerir. Çeşitli hata kodları hakkında daha fazla bilgi için bkz. [kimlik doğrulama ve yetkilendirme hata kodları](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

Python için MSAL içinde, çoğu hata bir hata değeri döndürerek işlendiği için özel durumlar nadir bir durumdur. `ValueError` özel durumu yalnızca, API parametrelerinin hatalı biçimlendirilmiş olduğu gibi, kitaplığı nasıl kullanmaya çalıştığınız ile ilgili bir sorun olduğunda oluşturulur.

## <a name="net-exceptions"></a>.NET özel durumları

Özel durumları işlerken, özel durumları ayırt etmek için özel durum türünün kendisini ve `ErrorCode` üyesini kullanabilirsiniz. `ErrorCode` değerler [Msalerror](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet)türünde sabitler.

Ayrıca, [Msalclientexception](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [msalserviceexception](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)ve [msaluırequiredexception](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)alanlarına bakabilirsiniz.

[Msalserviceexception](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) oluşturulursa, kodun orada listelenip listelenmediğini görmek Için [kimlik doğrulama ve yetkilendirme hata kodlarını](reference-aadsts-error-codes.md) deneyin.

### <a name="common-exceptions"></a>Sık karşılaşılan özel durumlar

Aşağıda, ortaya çıkabilecek ve bazı olası azaltmalar bulunan yaygın özel durumlar verilmiştir:  

| Özel durum | Hata kodu | Risk azaltma|
| --- | --- | --- |
| [Msaluırequiredexception](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Kullanıcı veya yönetici ' {appName} ' adlı ' {AppID} ' KIMLIĞIYLE uygulamayı kullanmak üzere onay vermedi. Bu Kullanıcı ve kaynak için etkileşimli bir yetkilendirme isteği gönderin.| Önce kullanıcı onayı almanız gerekir. .NET Core (herhangi bir Web Kullanıcı arabirimine sahip olmayan) kullanmıyorsanız, (yalnızca) `AcquireTokeninteractive`çağırın. .NET Core kullanıyorsanız veya `AcquireTokenInteractive`yapmak istemiyorsanız, Kullanıcı onay vermek için bir URL 'ye gidebilir: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. `AcquireTokenInteractive`çağırmak için: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [Msaluırequiredexception](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: kullanıcının Multi-Factor Authentication 'ı (MFA) kullanması gerekir.| Risk azaltma yoktur. Kiracınız için MFA yapılandırıldıysa ve Azure Active Directory (AAD) uygulamayı zorlamaya karar verirse, `AcquireTokenInteractive` veya `AcquireTokenByDeviceCode`gibi etkileşimli bir akışa geri dönüş yapmanız gerekir.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: izin türü *sık karşılaşılan* veya */tüketiciler* uç noktaları üzerinden desteklenmiyor. */Kuruluşlar* veya kiracıya özgü uç noktayı kullanın. *Sık karşılaşılan*kullandınız.| Azure AD 'nin iletisinde açıklandığı gibi, yetkilinin bir kiracıya ya da aksi takdirde */kuruluşlara*sahip olması gerekir.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: istek gövdesi şu parametreyi içermelidir: `client_secret or client_assertion`.| Bu özel durum, uygulamanız Azure AD 'de ortak bir istemci uygulaması olarak kaydettirilmemişse oluşturulabilir. Azure portal, uygulamanızın bildirimini düzenleyin ve `allowPublicClient` `true`olarak ayarlayın. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: oturum açmış kullanıcı tanımlanamadı| Kitaplık, geçerli Windows oturum açmış kullanıcıyı sorgulayamadı veya bu kullanıcı AD veya AAD 'ye katılmış değil (iş yeri katılmış kullanıcıları desteklenmez). Hafifletme 1: UWP 'de, uygulamanın şu yeteneklere sahip olup olmadığını denetleyin: kurumsal kimlik doğrulaması, özel ağlar (Istemci ve sunucu), Kullanıcı hesabı bilgileri. Hafifletme 2: Kullanıcı adını (örneğin, john@contoso.com) getirmek için kendi mantığınızı uygulayın ve Kullanıcı adında alan `AcquireTokenByIntegratedWindowsAuth` formunu kullanın.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Bu yöntem, Active Directory (AD) tarafından sunulan bir protokolü kullanır. Kullanıcı, AD ("yönetilen" Kullanıcı) olmadan Azure Active Directory oluşturulduysa, bu yöntem başarısız olur. AD 'de oluşturulan ve AAD tarafından desteklenen kullanıcılar ("federe" kullanıcılar), etkileşimli olmayan bu kimlik doğrulama yönteminden yararlanabilir. Risk azaltma: etkileşimli kimlik doğrulaması kullanın.|

### `MsalUiRequiredException`

`AcquireTokenSilent()` çağrılırken MSAL.NET 'den döndürülen yaygın durum kodlarından biri `MsalError.InvalidGrantError`. Bu durum kodu, uygulamanın kimlik doğrulama kitaplığını yeniden çağırması, ancak etkileşimli modda (genel istemci uygulamaları için Acquiretokenınteractive veya AcquireTokenByDeviceCodeFlow) ve Web uygulamalarında bir zorluk yapması gerektiği anlamına gelir. Bunun nedeni, kimlik doğrulama belirtecinin verilebilmesi için ek kullanıcı etkileşiminin gerekli olmasından kaynaklanır.

`AcquireTokenSilent` başarısız olduğunda, bunun nedeni belirteç önbelleğinde isteğinizle eşleşen belirteçleri yoktur. Erişim belirteçleri 1 saat içinde sona erer ve `AcquireTokenSilent` yenileme belirtecine göre yeni bir tane getirmeye çalışacaktır (OAuth2 koşullarında, bu "belirteci Yenile ' akışındadır). Bu akış çeşitli nedenlerle da başarısız olabilir. Örneğin, bir kiracı yöneticisi daha sıkı oturum açma ilkeleri yapılandırır. 

Kullanıcının bir eylem yapması için etkileşim amaçlar. Bu koşullardan bazılarının, kullanıcıların çözebilmesi (örneğin, tek tıklamayla kullanım koşullarını kabul etmesi) ve bazıları geçerli yapılandırmayla çözümlenemez (örneğin, söz konusu makinenin belirli bir şirket ağına bağlanması gerekir). Kullanıcı, Multi-Factor Authentication 'ı ayarlamaya veya Microsoft Authenticator yüklemesine yardımcı olur.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` sınıflandırma numaralandırması

MSAL, daha iyi bir kullanıcı deneyimi sağlamak için okuyabilecekleri bir `Classification` alanı sunar. Örneğin, kullanıcıya parolasının dolduğunu veya bazı kaynakları kullanma onayı sağlamaları gerektiğini söylemek için. Desteklenen değerler `UiRequiredExceptionClassification` numaralandırmasının bir parçasıdır:

| Sınıflandırma    | Anlamı           | Önerilen işleme |
|-------------------|-------------------|----------------------|
| BasicAction | Durum, etkileşimli kimlik doğrulama akışı sırasında Kullanıcı etkileşimi tarafından çözülebilir. | Acquiretokenetkileşimli () çağrısı yapın. |
| Ek | Durum, sistem ile etkileşimli kimlik doğrulama akışının dışında ek düzeltici etkileşim tarafından çözülebilir. | Düzeltici eylemi açıklayan bir ileti göstermek için Acquiretokenetkileşimli () çağrısı yapın. Çağıran uygulama, Kullanıcı düzeltici eylemi tamamlamayabilir additional_action gerektiren akışları gizlemeyi seçebilir. |
| Yalnızca Message      | Koşul şu anda çözümlenemiyor. Etkileşimli kimlik doğrulama akışının başlatılması, koşulu açıklayan bir ileti gösterir. | Koşulu açıklayan bir ileti göstermek için Acquiretokenetkileşimli () çağırın. Acquiretokenetkileşimli () Kullanıcı iletiyi okuduktan sonra pencereyi kapattığında Useriptal hatası döndürür. Çağıran uygulama, kullanıcının iletiden daha fazla avantaj sağlamaması halinde message_only elde edilen akışları gizlemek için seçim gösterebilir.|
| ConsentRequired  | Kullanıcı onayı eksik veya iptal edildi. | Kullanıcının izin vermesi için Acquiretokenetkileşimli () çağrısı yapın. |
| UserPasswordExpired | Kullanıcının parolasının süresi doldu. | Kullanıcının parolasını sıfırlayabilmesi için Acquiretokenetkileşimli () çağırın. |
| Promptnewverfailed| Etkileşimli kimlik doğrulaması, parametre istemiyle birlikte çağrıldı = hiçbir şekilde, MSAL, tarayıcı tanımlama bilgilerine dayanmaya zorlanıyor ve tarayıcının görüntülenmesini gerektirmez. Bu başarısız oldu. | Prompt. None olmadan Acquiretokenetkileşimli () çağrısı yapın |
| AcquireTokenSilentFailed | MSAL SDK, önbellekten belirteç getirmek için yeterli bilgi içermiyor. Bunun nedeni önbellekte hiçbir belirteç olmaması veya bir hesabın bulunamaması olabilir. Hata iletisinde daha fazla ayrıntı vardır.  | Acquiretokenetkileşimli () çağrısı yapın. |
| None    | Daha fazla ayrıntı sağlanmaz. Durum, etkileşimli kimlik doğrulama akışı sırasında Kullanıcı etkileşimi tarafından çözülebilir. | Acquiretokenetkileşimli () çağrısı yapın. |

## <a name="code-example"></a>Kod örneği

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
   res = await application.AcquireTokenInteractive(scopes)
                          .ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascript-errors"></a>JavaScript hataları

MSAL. js, soyut ve farklı türlerde yaygın hataların sınıflandırmakta olan hata nesneleri sağlar. Ayrıca, uygun şekilde işlemek üzere hata iletileri gibi hataların belirli ayrıntılarına erişmek için arabirim sağlar.

### <a name="error-object"></a>Hata nesnesi

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Hata sınıfını genişleterek aşağıdaki özelliklere erişebilirsiniz:
* **Kimlik doğrulama hatası. ileti:**  ErrorMessage ile aynı.
* **AuthError. Stack:** Oluşan hatalar için yığın izlemesi. Kaynak hata noktasına izlemenin yapılmasına izin verir.

### <a name="error-types"></a>Hata türleri

Aşağıdaki hata türleri kullanılabilir:

- `AuthError`: MSAL. js kitaplığı için temel hata sınıfı, ayrıca beklenmeyen hatalar için kullanılır.

- `ClientAuthError`: Istemci kimlik doğrulamasıyla ilgili bir sorunu gösteren hata sınıfı. Kitaplıktan gelen hataların çoğu ClientAuthErrors olur. Bu hatalar, oturum açma işlemi devam ederken bir oturum açma yöntemi çağırma gibi işlemlerden kaynaklanır, Kullanıcı oturum açma işlemini iptal eder ve bu şekilde devam eder.

- `ClientConfigurationError`: belirtilen kullanıcı yapılandırma parametreleri hatalı biçimlendirilmiş ya da eksik olduğunda istekler yapılmadan önce `ClientAuthError` genişleyen hata sınıfı.

- `ServerError`: Error sınıfı, kimlik doğrulama sunucusu tarafından gönderilen hata dizelerini temsil eder. Bunlar geçersiz istek biçimleri veya parametreler gibi hatalar ya da sunucunun kimlik doğrulamasını veya yetkilendirmasını engelleyen başka hatalar olabilir.

- `InteractionRequiredAuthError`: hata sınıfı, `ServerError` genişleterek etkileşimli çağrı gerektiren sunucu hatalarını temsil eder. Bu hata, kullanıcının kimlik doğrulaması/yetkilendirme için kimlik bilgileri veya onay sağlamak üzere sunucuyla etkileşmesi gerekiyorsa `acquireTokenSilent` tarafından oluşturulur. Hata kodları `"interaction_required"`, `"login_required"`ve `"consent_required"`içerir.

Yeniden yönlendirme yöntemleriyle (`loginRedirect`, `acquireTokenRedirect`) kimlik doğrulaması akışlarında hata işleme için, `handleRedirectCallback()` yöntemi kullanılarak yeniden yönlendirme sonrasında aşağıdaki gibi, başarılı veya başarısız olarak çağrılan geri çağırma işlemini kaydetmeniz gerekir:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Açılır deneyim (`loginPopup`, `acquireTokenPopup`) yöntemleri, ' i geri döndürmekte, bu nedenle bunları gösterildiği gibi işlemek için Promise modelini (. then ve. catch) kullanabilirsiniz:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Etkileşim gerekli, hatalar

`acquireTokenSilent`gibi bir belirteç almak için etkileşimli olmayan bir yöntem kullanmaya çalıştığınızda bir hata döndürülür, ancak MSAL bunu sessizce yapamadık.

Olası nedenler şunlardır:

- oturum açmanız gerekiyor
- onay yapmanız gerekir
- Multi-Factor Authentication deneyiminden gitmeniz gerekir.

Düzeltme, `acquireTokenPopup` veya `acquireTokenRedirect`gibi etkileşimli bir yöntemi çağırmalıdır:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>Koşullu erişim ve talep sorunları

Belirteçleri sessizce alırken, erişmeye çalıştığınız bir API 'nin MFA İlkesi gibi [koşullu erişim talep zorluğu](conditional-access-dev-guide.md) gerektiğinde uygulamanız hatalar alabilir.

Bu hatayı işleme deseninin, MSAL kullanarak etkileşimli bir şekilde belirteç edinmesi. Bir belirteci etkileşimli bir şekilde almak kullanıcıya sorar ve gerekli koşullu erişim ilkesini karşılamak için bu fırsata yanıt verir.

Belirli durumlarda, koşullu erişim gerektiren bir API 'yi çağırırken API 'den hata ile bir talep zorluğu alabilirsiniz. Örneğin, koşullu erişim ilkesinde yönetilen bir cihaz (Intune) varsa, hata AADSTS53000 gibi bir şey olacaktır [: cihazınızın bu kaynağa erişmek için yönetilmesi gerekir](reference-aadsts-error-codes.md) veya benzer bir şeydir. Bu durumda, kullanıcıdan uygun ilkeyi karşılaması istenir diye, talepleri alma çağrısında geçirebilirsiniz.

### <a name="net"></a>.NET

MSAL.NET 'ten koşullu erişim gerektiren bir API çağrılırken, uygulamanızın talep sınama özel durumlarını işlemesi gerekir. Bu, [talepler](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) özelliğinin boş olacağı bir [Msalserviceexception](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) olarak görünür.

Talep sınamasını işlemek için `PublicClientApplicationBuilder` sınıfının `.WithClaim()` yöntemini kullanmanız gerekir.

### <a name="javascript"></a>JavaScript

MSAL. js kullanarak belirteçleri sessizce alırken (`acquireTokenSilent`kullanarak), erişmeye çalıştığınız bir API için MFA İlkesi gibi [koşullu erişim talep zorluğu](conditional-access-dev-guide.md) gerektiğinde uygulamanız hatalar alabilir.

Bu hatayı işleme deseninin, aşağıdaki örnekte olduğu gibi, MSAL. js ' de `acquireTokenPopup` veya `acquireTokenRedirect` gibi belirteç almak için etkileşimli bir çağrı yapmak için kullanabileceğiniz bir yol vardır:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

Belirteci etkileşimli olarak almak kullanıcıya sorar ve gerekli koşullu erişim ilkesini karşılamak için bu fırsata yanıt verir.

Koşullu erişim gerektiren bir API çağrılırken, API 'den hatada bir talep zorluğu alabilirsiniz. Bu durumda, hatada döndürülen talepleri, uygun ilkeyi karşılamak için `AuthenticationParameters.ts` sınıfının `claimsRequest` alanına geçirebilirsiniz. 

Daha fazla ayrıntı için bkz. [ek talepler isteme](active-directory-optional-claims.md) .

### <a name="msal-for-ios-and-macos"></a>iOS ve macOS için MSAL

İOS ve macOS için MSAL, hem etkileşimli hem de sessiz belirteç alma senaryolarında belirli talepler talep etmenizi sağlar.

Özel talepler istemek için `MSALSilentTokenParameters` veya `MSALInteractiveTokenParameters``claimsRequest` belirtin.

Daha fazla bilgi için bkz. [iOS IÇIN msal ve macOS kullanarak özel talepler isteme](request-custom-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Hatalar ve özel durumlar sonrasında yeniden deneniyor

MSAL çağrılırken size kendi yeniden deneme ilkelerinizi uygulamanız bekleniyor. MSAL AAD hizmetine HTTP çağrıları yapar ve zaman zaman meydana gelebilir, örneğin ağ kapatılabilir veya sunucu aşırı yüklenmiş olabilir.  

### <a name="http-error-codes-500-600"></a>HTTP hata kodları 500-600

MSAL.NET, HTTP hata kodları 500-600 ile hatalar için basit bir yeniden deneme mekanizması uygular.

### <a name="http-429"></a>HTTP 429

Hizmet belirteci sunucusu (STS) çok fazla istek ile aşırı yüklendiğinde, `Retry-After` Response alanında yeniden deneyebilmeniz için ne kadar süreyle bir ipucu ile HTTP hatası 429 döndürür.

### <a name="net"></a>.NET

[Msalserviceexception](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) yüzeyleri bir özellik `namedHeaders`olarak `System.Net.Http.Headers.HttpResponseHeaders`. Uygulamalarınızın güvenilirliğini artırmak için hata kodundaki ek bilgileri kullanabilirsiniz. Açıklanan örnekte, `RetryAfterproperty` (`RetryConditionHeaderValue`türü) kullanabilir ve yeniden deneneceği işlem yapabilirsiniz.

İstemci kimlik bilgileri akışını kullanan bir Daemon uygulaması için bir örnek aşağıda verilmiştir. Bunu, belirteç alma yöntemlerinden herhangi birine uyarlayabilirsiniz.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```

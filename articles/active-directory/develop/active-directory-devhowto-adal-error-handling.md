---
title: Azure AD kimlik doğrulama kitaplığı (ADAL) istemcileri için en iyi yöntemler işlenirken hata oluştu
description: ADAL istemci uygulamaları için hata işleme Kılavuzu ve en iyi uygulamalar sağlar.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7008a5909d8f530920628125fec1b826be3f984
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374200"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Azure Active Directory kimlik doğrulaması kitaplığı (ADAL) istemcileri için en iyi yöntemler işlenirken hata oluştu

Bu makalede, kullanıcıların kimliğini doğrulamak için ADAL kullanılırken geliştiricilerin karşılaşabileceği hataların türü hakkında rehberlik sağlanır. ADAL kullanırken, bir geliştiricinin hata adımları ve hataları işlemesi gerekebilecek birkaç durum vardır. Doğru hata işleme, iyi bir son kullanıcı deneyimi sağlar ve son kullanıcının oturum açması için gereken süreyi kısıtlar.

Bu makalede, ADAL tarafından desteklenen her platformun belirli durumlarını ve uygulamanızın her bir durumu düzgün bir şekilde nasıl işleyebileceğini araştırıyoruz. Hata Kılavuzu, ADAL API 'Leri tarafından sunulan belirteç alma desenlerine bağlı olarak iki geniş kategoriye ayrılır:

- **Acquiretokensilent**: istemci bir belirteci sessizce almaya çalışır (Kullanıcı arabirimi olmadan) ve adal başarısız olursa başarısız olabilir. 
- **Acquiretoken**: istemci sessiz alımı deneyebilir, ancak oturum açma gerektiren etkileşimli istekleri de gerçekleştirebilir.

> [!TIP]
> ADAL ve Azure AD kullanırken tüm hataları ve özel durumları günlüğe kaydetmek iyi bir fikirdir. Günlükler yalnızca uygulamanızın genel durumunu anlamak için yararlı değildir, ancak daha geniş sorunlar ayıklandığında de önemlidir. Uygulamanız belirli hatalardan kurtarılarken, çözülmesi için kod değişikliği gerektiren daha geniş tasarım sorunları hakkında ipucu edebilirler. 
> 
> Bu belgede ele alınan hata koşullarını uygularken, daha önce açıklanan nedenlerden dolayı hata kodunu ve açıklamasını günlüğe yazmanız gerekir. Günlüğe kaydetme kodu örnekleri için [hata ve günlüğe kaydetme başvurusu](#error-and-logging-reference) bölümüne bakın. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent, son kullanıcının bir kullanıcı arabirimi (UI) görmediğinden emin olmak için bir belirteç almaya çalışır. Sessiz almanın başarısız olabileceği ve etkileşimli istekler veya varsayılan bir işleyici tarafından işlenmesi gereken birkaç durum vardır. İzleyen bölümlerde her bir durumun ne zaman ve nasıl kullanılacağına ilişkin ayrıntıları inceleyeceğiz.

İşletim sistemi tarafından oluşturulan ve uygulamaya özel hata işleme gerektirebilecek bir hata kümesi vardır. Daha fazla bilgi için [hata ve günlüğe kaydetme başvurusunda](#error-and-logging-reference)"işletim sistemi" hataları bölümüne bakın. 

### <a name="application-scenarios"></a>Uygulama senaryoları

- [Yerel istemci](developer-glossary.md#native-client) uygulamaları (IOS, Android, .net masaüstü veya Xamarin)
- Bir [kaynağı](developer-glossary.md#resource-server) çağıran [Web istemcisi](developer-glossary.md#web-client) uygulamaları (.net)

### <a name="error-cases-and-actionable-steps"></a>Hata durumları ve eyleme dönüştürülebilir adımlar

Temelde, AcquireTokenSilent hataların iki durumu vardır:

| Harflerini | Açıklama |
|------|-------------|
| **Durum 1**: etkileşimli bir oturum açma ile hata çözülebilir | Geçerli belirteçlerin olmamasından kaynaklanan hatalar için, etkileşimli bir istek gereklidir. Özellikle, önbellek araması ve geçersiz/zaman aşımına uğradı yenileme belirteci, çözümlemek için bir AcquireToken çağrısı gerektirir.<br><br>Bu durumlarda, son kullanıcıdan oturum açması istenir. Uygulama, Son Kullanıcı etkileşiminden sonra etkileşimli bir istek yapmayı tercih edebilir (bir oturum açma düğmesine veya daha sonra bir oturum açma düğmesine vurmayın). Seçim, uygulamanın istenen davranışına bağlıdır.<br><br>Bu özel durum ve bunu tanılayan hatalar için aşağıdaki bölümde yer alan koda bakın.|
| **Durum 2**: hataya etkileşimli bir oturum açma ile çözümlenemez | Ağ ve geçici/geçici hatalar veya diğer hatalar için, etkileşimli bir AcquireToken isteği gerçekleştirmek sorunu çözmez. Gereksiz oturum açma istemlerinin yanı sıra son kullanıcıları da rahatsız edebilir. ADAL, AcquireTokenSilent hatalarıyla ilgili birçok hata için otomatik olarak tek bir yeniden denemeye çalışır.<br><br>İstemci uygulaması, daha sonraki bir noktada yeniden denemeye da deneyebilir, ancak uygulama davranışına ve istenen son kullanıcı deneyimine ne zaman ve nasıl bağlı olur. Örneğin, uygulama birkaç dakika sonra veya son kullanıcı eylemine yanıt olarak bir AcquireTokenSilent yeniden deneme gerçekleştirebilir. Anında yeniden deneme uygulamanın azaltılmakta olmasının yanı sıra denenmemelidir.<br><br>Aynı hatayla başarısız olan sonraki bir yeniden deneme, hatayı çözemediğinden, istemcinin AcquireToken kullanan etkileşimli bir istek yapması gerektiği anlamına gelmez.<br><br>Bu özel durum ve bunu tanılayan hatalar için aşağıdaki bölümde yer alan koda bakın. |

### <a name="net"></a>.NET

Aşağıdaki kılavuz, ADAL yöntemleriyle birlikte hata işleme örnekleri sağlar: 

- acquireTokenSilentAsync (...)
- acquireTokenSilentSync (...) 
- [kullanım dışı] acquireTokenSilent (...)
- [kullanım dışı] acquireTokenByRefreshToken (...) 

Kodunuz aşağıdaki gibi uygulanır:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

Aşağıdaki kılavuz, ADAL yöntemleriyle birlikte hata işleme örnekleri sağlar: 

- acquireTokenSilentSync (...)
- acquireTokenSilentAsync (...)
- [kullanım dışı] acquireTokenSilent (...)

Kodunuz aşağıdaki gibi uygulanır:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

Aşağıdaki kılavuz, ADAL yöntemleriyle birlikte hata işleme örnekleri sağlar: 

- acquireTokenSilentWithResource (...)

Kodunuz aşağıdaki gibi uygulanır:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken, belirteçleri almak için kullanılan varsayılan ADAL yöntemidir. Kullanıcı kimliğinin gerekli olduğu durumlarda, AcquireToken öncelikle sessizce bir belirteç almaya çalışır ve gerekirse kullanıcı arabirimini (PromptBehavior. hiçbir zaman geçirilmemişse) görüntüler. Uygulama kimliğinin gerekli olduğu durumlarda, AcquireToken bir belirteç almaya çalışır, ancak son kullanıcı bulunmadığından Kullanıcı ARABIRIMINI göstermez. 

AcquireToken hatalarını işlerken, hata işleme, uygulamanın elde edilmeye çalıştığı platforma ve senaryoya bağlıdır. 

İşletim sistemi, belirli bir uygulamaya bağımlı hata işleme gerektiren bir hata kümesi de oluşturabilir. Daha fazla bilgi için bkz. "Işletim sistemi hataları" [hata ve günlüğe kaydetme başvurusu](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Uygulama senaryoları

- Yerel istemci uygulamaları (iOS, Android, .NET masaüstü veya Xamarin)
- Kaynak API 'SI çağıran Web uygulamaları (.NET)
- Tek sayfalı uygulamalar (JavaScript)
- Hizmetten hizmete uygulamalar (.NET, Java)
  - -Adına dahil tüm senaryolar
  - Yerinde belirli senaryolar

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Hata durumları ve eyleme dönüştürülebilir adımlar: yerel istemci uygulamaları

Yerel bir istemci uygulaması oluşturuyorsanız, ağ sorunları, geçici hatalar ve platforma özgü diğer hatalarla ilgili olarak dikkate alınması gereken birkaç hata işleme durumu vardır. Çoğu durumda, bir uygulama anında yeniden denemeler gerçekleştirmemelidir, ancak oturum açma işlemini isteyen son kullanıcı etkileşimini beklemek yerine. 

Tek bir yeniden deneme sorunu çözebileceği birkaç özel durum vardır. Örneğin, bir kullanıcının bir cihazda verileri etkinleştirmesi gerektiğinde veya ilk hatadan sonra Azure AD Aracısı indirmesi tamamlandığında. 

Bir uygulama, hata durumunda son kullanıcının yeniden denenme isteyen bir etkileşim gerçekleştirmesine izin vermek için Kullanıcı arabirimi sunabilir. Örneğin, cihaz çevrimdışı bir hata için başarısız olduysa, "yeniden oturum açmayı deneyin" düğmesine hemen hatayı yeniden denemek yerine bir AcquireToken yeniden denemesini sorma. 

Yerel uygulamalarda hata işleme, iki durum tarafından tanımlanabilir:

|  |  |
|------|-------------|
| **Durum 1**:<br>Yeniden denenmeyen hata (çoğu zaman) | 1. anında yeniden denemeyi denemeyin. Yeniden denemeyi çağıran belirli bir hataya göre Son Kullanıcı Kullanıcı arabirimini sunun (örneğin, "yeniden oturum açmayı deneyin" veya "Azure AD Broker uygulamasını Indirme"). |
| **Durum 2**:<br>Yeniden denenebilir hata | 1. Son Kullanıcı başarı ile sonuçlanan bir durum girmiş olabileceğinden tek bir yeniden deneme gerçekleştirin.<br><br>2. yeniden deneme başarısız olursa, Son Kullanıcı Kullanıcı arabirimini yeniden denemeyi çağıran özel hataya göre sunun ("yeniden oturum açmayı deneyin", "Azure AD Broker uygulamasını Indir" vb.). |

> [!IMPORTANT]
> Bir kullanıcı hesabı bir sessiz çağrıda ADAL öğesine geçirilirse ve başarısız olursa, sonraki etkileşimli istek, son kullanıcının farklı bir hesap kullanarak oturum açmasına olanak tanır. Başarılı bir AcquireToken bir kullanıcı hesabı kullanarak, uygulamanın oturum açmış kullanıcının, uygulamaların yerel kullanıcı nesnesiyle eşleşip eşleşmediğini doğrulaması gerekir. Uyuşmazlık bir özel durum oluşturmaz (hedef C dışında), ancak kimlik doğrulama isteklerinden önce (başarısız sessiz çağrı gibi) bir kullanıcının yerel olarak bilinen olduğu durumlarda göz önünde bulundurulmalıdır.
>

#### <a name="net"></a>.NET

Aşağıdaki kılavuz, tüm sessiz AcquireToken (...) ile birlikte hata işleme örnekleri sağlar ADAL yöntemleri şunları *hariç*: 

- AcquireTokenAsync (..., ılientassertioncertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync (..., UserAssertion,...)   

Kodunuz aşağıdaki gibi uygulanır:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> ADAL .NET, PromptBehavior 'ı desteklediğine yönelik ek bir konudur. Bu, AcquireTokenSilent gibi davranışa sahiptir.
>

Aşağıdaki kılavuz, ADAL yöntemleriyle birlikte hata işleme örnekleri sağlar: 

- acquireToken (..., PromptBehavior. hiç)

Kodunuz aşağıdaki gibi uygulanır:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Aşağıdaki kılavuz, tüm sessiz AcquireToken (...) ile birlikte hata işleme örnekleri sağlar ADAL yöntemleri. 

Kodunuz aşağıdaki gibi uygulanır:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Aşağıdaki kılavuz, tüm sessiz AcquireToken (...) ile birlikte hata işleme örnekleri sağlar ADAL yöntemleri. 

Kodunuz aşağıdaki gibi uygulanır:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Hata durumları ve eyleme dönüştürülebilir adımlar: bir kaynak API 'SI çağıran Web uygulamaları (.NET)

Çağıran bir .NET Web uygulaması oluşturuyorsanız, bir kaynak için yetkilendirme kodu kullanarak bir belirteç alır, yalnızca genel durum için gereken tek kod varsayılan bir işleyicidir. 

Aşağıdaki kılavuz, ADAL yöntemleriyle birlikte hata işleme örnekleri sağlar: 

- AcquireTokenByAuthorizationCodeAsync (...)

Kodunuz aşağıdaki gibi uygulanır:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Hata durumları ve eyleme dönüştürülebilir adımlar: tek sayfalı uygulamalar (ADAL. js)

AcquireToken ile adal. js ' yi kullanarak tek sayfalı bir uygulama oluşturuyorsanız hata işleme kodu tipik bir sessiz çağrıdan benzerdir. Özellikle adal. js ' de, AcquireToken hiçbir koşulda Kullanıcı arabirimini göstermez. 

Başarısız bir AcquireToken aşağıdaki durumları içerir:

|  |  |
|------|-------------|
| **Durum 1**:<br>Etkileşimli bir istek ile çözülebilir | 1. oturum açma () başarısız olursa, anında yeniden deneme gerçekleştirmeyin. Yalnızca Kullanıcı eylemi bir yeniden denemeye girdikten sonra yeniden deneyin.|
| **Durum 2**:<br>Etkileşimli bir istek ile çözümlenemez. Hata yeniden denenebilir. | 1. Son Kullanıcı büyük bir başarı ile sonuçlanan bir durum girdiğinden tek bir yeniden deneme gerçekleştirin.<br><br>2. yeniden deneme başarısız olursa, son kullanıcıyı yeniden denemeyi çağırabilen belirli bir hataya göre bir eylem ile sunun ("yeniden oturum açmayı deneyin"). |
| **Durum 3**:<br>Etkileşimli bir istek ile çözümlenemez. Hata yeniden denenmedi. | 1. anında yeniden denemeyi denemeyin. Son kullanıcıyı, yeniden deneme çağırabilen belirli bir hataya göre bir eylem ile sunun ("yeniden oturum açmayı deneyin"). |

Kodunuz aşağıdaki gibi uygulanır:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Hata durumları ve eyleme dönüştürülebilir adımlar: hizmetten hizmete uygulamalar (yalnızca .NET)

AcquireToken kullanan bir hizmet-hizmet uygulaması oluşturuyorsanız, kodunuzun işlemesi gereken birkaç temel hata vardır. Hata için tek yanıt, hatayı çağıran uygulamaya geri döndürmeli (örneğin, şirket içi) veya yeniden deneme stratejisi uygular. 

#### <a name="all-scenarios"></a>Tüm senaryolar

-Adına dahil olmak üzere *Tüm* hizmetten hizmete uygulama senaryoları için:

- Anında yeniden denemeye çalışmayın. ADAL, başarısız olan bazı istekler için tek bir yeniden denemeye çalışır. 
- Yalnızca bir kullanıcı veya uygulama eylemi bir yeniden denemeye girdikten sonra denemeye devam edin. Örneğin, bazı küme aralıklarında çalışan bir Daemon uygulaması, yeniden denenene kadar bir sonraki aralığa kadar beklemelidir.

Aşağıdaki kılavuz, ADAL yöntemleriyle birlikte hata işleme örnekleri sağlar: 

- AcquireTokenAsync (..., ılientassertioncertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync (..., UserAssertion,...)

Kodunuz aşağıdaki gibi uygulanır:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Şirket adına senaryolar

*Adına* hizmet-hizmet uygulama senaryoları için.

Aşağıdaki kılavuz, ADAL yöntemleriyle birlikte hata işleme örnekleri sağlar: 

- AcquireTokenAsync (..., UserAssertion,...)

Kodunuz aşağıdaki gibi uygulanır:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Bu senaryoyu gösteren bir [bütün örnek](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) oluşturduk.

## <a name="error-and-logging-reference"></a>Hata ve günlüğe kaydetme başvurusu

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Kişisel olarak tanımlanabilen bilgileri & Kurumsal olarak tanımlanabilen bilgileri günlüğe kaydetme 
ADAL günlüğü, varsayılan olarak, kişisel olarak tanımlanabilen bilgileri veya kurumsal olarak tanımlanabilen bilgileri yakalamaz veya günlüğe almaz. Kitaplık, uygulama geliştiricilerinin günlükçü sınıfındaki bir ayarlayıcı aracılığıyla bunu açmasına olanak sağlar. Uygulama, kişisel olarak tanımlanabilir bilgileri veya kurumsal olarak tanımlanabilen bilgileri günlüğe kaydederek, yüksek oranda hassas verileri güvenli bir şekilde işlemek ve herhangi bir düzenleme gereksinimleriyle uyumlu olmak için sorumluluk kazanır

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL kitaplığı hataları

Belirli ADAL hatalarını araştırmak için, [Azure-ActiveDirectory-Library-for-DotNet deposundaki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) kaynak kodu en iyi hata başvurusudur.

#### <a name="guidance-for-error-logging-code"></a>Hata günlüğü kodu için kılavuz

ADAL .NET günlüğü, üzerinde çalışılan platforma bağlı olarak değişir. Günlüğe kaydetmenin nasıl etkinleştirileceği hakkında kod için [günlük wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) bölümüne bakın.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL kitaplığı hataları

Belirli ADAL hatalarını araştırmak için, [Azure-ActiveDirectory-Library-for-Android deposundaki](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) kaynak kodu en iyi hata başvurusudur.

#### <a name="operating-system-errors"></a>İşletim sistemi hataları

Android işletim sistemi hataları ADAL içinde AuthenticationException aracılığıyla gösterilir, "SERVER_INVALID_REQUEST" olarak tanımlanabilir ve hata açıklamalarıyla daha ayrıntılı olabilir. 

Yaygın hataların tam listesi ve uygulamanız veya son kullanıcılarınız bu sorunlarla karşılaştığında, [adal Android wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki)' ye başvurun. 

#### <a name="guidance-for-error-logging-code"></a>Hata günlüğü kodu için kılavuz

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>ADAL kitaplığı hataları

Belirli ADAL hatalarını araştırmak için, [Azure-ActiveDirectory-Library-for-ObjC deposundaki](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) kaynak kodu en iyi hata başvurusudur.

#### <a name="operating-system-errors"></a>İşletim sistemi hataları

Kullanıcılar Web görünümlerini ve kimlik doğrulamanın yapısını kullanırken oturum açma sırasında iOS hataları ortaya çıkabilir. Bu durum, SSL hataları, zaman aşımları veya ağ hataları gibi koşullardan kaynaklanabilir:

- Yetkilendirme paylaşımı için, oturum açmalar kalıcı değildir ve önbellek boş görünür. Aşağıdaki kod satırını anahtarlığa ekleyerek çözebilirsiniz: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- NsUrlDomain hata kümesi için eylem, uygulama mantığına göre değişir. İşlenebilen belirli örnekler için [Nsurlerrordomain başvuru belgelerine](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) bakın.
- ADAL hedefi-C ekibi tarafından tutulan yaygın hataların listesi için [adal obj-c ortak sorunları](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) bölümüne bakın.

#### <a name="guidance-for-error-logging-code"></a>Hata günlüğü kodu için kılavuz

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Hata günlüğü kodu için kılavuz-JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```
## <a name="related-content"></a>İlgili içerik

* [Azure AD Geliştirici Kılavuzu][AAD-Dev-Guide]
* [Azure AD kimlik doğrulama kitaplıkları][AAD-Auth-Libraries]
* [Azure AD kimlik doğrulama senaryoları][AAD-Auth-Scenarios]
* [Uygulamaları Azure Active Directory tümleştirme][AAD-Integrating-Apps]

Geri bildirim sağlamak ve içeriğimizi iyileştirmemizi ve şekillendirmemize yardımcı olmak için aşağıdaki açıklamalar bölümünü kullanın.

[!["Microsoft hesabıyla oturum açın" düğmesini gösterir][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png


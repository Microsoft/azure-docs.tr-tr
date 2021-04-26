---
title: MSAL.js’de hataları ve özel durumları işleme
titleSuffix: Microsoft identity platform
description: MSAL.js uygulamalarında hata ve özel durumları, koşullu erişim talep sorunlarını ve yeniden denemeleri nasıl işleyeceğinizi öğrenin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761333"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>MSAL.js’de hataları ve özel durumları işleme

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>MSAL.js işlenirken hata oluştu

MSAL.js, soyut ve farklı türlerde ortak hataları sınıflandıran hata nesneleri sağlar. Ayrıca, bunları uygun şekilde işlemek üzere hata iletileri gibi hataların belirli ayrıntılarına erişmek için bir arabirim sağlar.

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
- `AuthError.message`: İle aynı `errorMessage` .
- `AuthError.stack`: Oluşturulan hatalar için yığın izlemesi.

### <a name="error-types"></a>Hata türleri

Aşağıdaki hata türleri kullanılabilir:

- `AuthError`: MSAL.js kitaplığı için temel hata sınıfı, ayrıca beklenmeyen hatalar için kullanılır.

- `ClientAuthError`: Istemci kimlik doğrulamasıyla ilgili bir sorunu gösteren hata sınıfı. Kitaplıktan gelen hataların çoğu ClientAuthErrors olur. Bu hatalar, oturum açma işlemi devam ederken bir oturum açma yöntemi çağırma gibi işlemlerden kaynaklanır, Kullanıcı oturum açma işlemini iptal eder ve bu şekilde devam eder.

- `ClientConfigurationError`: `ClientAuthError` Belirtilen kullanıcı yapılandırma parametreleri hatalı biçimlendirilmiş veya eksik olduğunda istekler yapılmadan önce, hata sınıfı, genişletiliyor.

- `ServerError`: Error sınıfı, kimlik doğrulama sunucusu tarafından gönderilen hata dizelerini temsil eder. Bunlar geçersiz istek biçimleri veya parametreler gibi hatalar ya da sunucunun kimlik doğrulamasını veya yetkilendirmasını engelleyen başka hatalar olabilir.

- `InteractionRequiredAuthError`: Hata sınıfı, `ServerError` etkileşimli çağrı gerektiren sunucu hatalarını temsil edecek şekilde genişletilir. Bu hata, `acquireTokenSilent` kullanıcının kimlik doğrulaması/yetkilendirme için kimlik bilgileri veya onay sağlamak üzere sunucuyla etkileşmesi gerekiyorsa oluşur. Hata kodları `"interaction_required"` ,, `"login_required"` ve içerir `"consent_required"` .

Yeniden yönlendirme Yöntemleri (,) ile kimlik doğrulaması akışlarında hata işleme için `loginRedirect` `acquireTokenRedirect` , aşağıdaki gibi, using yönteminden sonra başarılı veya başarısız olarak çağrılan geri çağırma işlemini kaydetmeniz gerekir `handleRedirectCallback()` :

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Açılır deneyim (,) için yöntemler, `loginPopup` `acquireTokenPopup` ' ı geri döndürmekte, bu nedenle bunları gösterildiği gibi işlemek için Promise modelini (. then ve. catch) kullanabilirsiniz:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Etkileşim gerektiren hatalar

Bir belirteci almak için etkileşimli olmayan bir yöntem kullanmaya çalıştığınızda bir hata döndürülür `acquireTokenSilent` , ancak msal bunu sessizce yapamadık.

Olası nedenler şunlardır:

- oturum açmanız gerekiyor
- onay yapmanız gerekir
- Multi-Factor Authentication deneyiminden gitmeniz gerekir.

Düzeltme, veya gibi etkileşimli bir yöntemi çağırmalıdır `acquireTokenPopup` `acquireTokenRedirect` :

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

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

MSAL.js kullanarak belirteçleri sessizce (kullanarak `acquireTokenSilent` ) alırken, erişmeye çalıştığınız BIR API IÇIN MFA İlkesi gibi bir [koşullu erişim talep zorluğu](../azuread-dev/conditional-access-dev-guide.md) gerektiğinde uygulamanız hatalar alabilir.

Bu hatayı işleme deseninin, `acquireTokenPopup` Aşağıdaki örnekte olduğu gibi MSAL.js belirteç almak için etkileşimli bir çağrı yapılır `acquireTokenRedirect` :

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

Belirteci etkileşimli olarak almak kullanıcıya sorar ve gerekli koşullu erişim ilkesini karşılamak için bu fırsata yanıt verir.

Koşullu erişim gerektiren bir API çağrılırken, API 'den hatada bir talep zorluğu alabilirsiniz. Bu durumda, hatada döndürülen talepleri, `claimsRequest` `AuthenticationParameters.ts` uygun ilkeyi karşılamak üzere sınıfının alanına geçirebilirsiniz. 

Daha fazla ayrıntı için bkz. [ek talepler isteme](active-directory-optional-claims.md) .


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Sonraki adımlar

Sorunları tanılamanıza ve hata ayıklamanıza yardımcı olmak için [MSAL.jsoturum açmayı ](msal-logging-js.md) etkinleştirmeyi düşünün.

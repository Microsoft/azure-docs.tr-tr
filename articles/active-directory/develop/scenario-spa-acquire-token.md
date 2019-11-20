---
title: Tek sayfalı uygulama (API çağrısı için belirteç alma)-Microsoft Identity platform
description: Tek sayfalı uygulama oluşturmayı öğrenin (bir API çağırmak için belirteç alma)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135705"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Tek sayfalı uygulama-API 'YI çağırmak için belirteç alma

MSAL. js ile API 'ler için belirteçleri alma deseninin, `acquireTokenSilent` yöntemi kullanarak sessiz bir belirteç isteği denemesi gerekir. Bu yöntem çağrıldığında, kitaplık, geçerli bir belirtecin mevcut olup olmadığını görmek için ilk olarak tarayıcı depolamada önbelleği denetler ve döndürür. Önbellekte geçerli bir belirteç yoksa, gizli bir iframe 'den Azure Active Directory (Azure AD) için sessiz bir belirteç isteği gönderir. Bu yöntem, kitaplığın belirteçleri yenilemesini de sağlar. Azure AD 'de çoklu oturum açma oturumu ve belirteç yaşam süresi değerleri hakkında daha fazla bilgi için bkz. [belirteç yaşam süreleri](active-directory-configurable-token-lifetimes.md).

Azure AD 'ye yönelik sessiz Belirteç istekleri, zaman aşımına uğradı bir Azure AD oturumu veya parola değişikliği gibi bazı nedenlerle başarısız olabilir. Bu durumda, bir etkileşimli yöntemden birini çağırabilirsiniz (kullanıcıdan, belirteçleri, belirteç almasına izin verir).

* Kullanarak [bir açılır pencereyle belirteç alma](#acquire-token-with-a-pop-up-window)`acquireTokenPopup`
* Kullanarak [yeniden yönlendirme ile belirteç al](#acquire-token-with-redirect)`acquireTokenRedirect`

**Bir açılır pencere veya yeniden yönlendirme deneyimi arasında seçim yapma**

 Uygulamanızda hem açılır hem de yeniden yönlendirme yöntemlerinin bir birleşimini kullanamazsınız. Açılır veya yeniden yönlendirme deneyimi arasındaki seçim, uygulama akışınıza bağlıdır.

* Kimlik doğrulaması sırasında kullanıcının ana uygulama sayfanızda uzaklaşmak istemiyorsanız, açılır yöntemlerin kullanılması önerilir. Kimlik doğrulama yeniden yönlendirmesi bir açılır pencerede olduğundan, ana uygulamanın durumu korunur.

* Yeniden yönlendirme yöntemlerini kullanmanız gerekebilecek bazı durumlar vardır. Uygulamanızın kullanıcılarının açılır pencereler devre dışı bırakılmış tarayıcı kısıtlamaları veya ilkeleri varsa, yeniden yönlendirme yöntemlerini kullanabilirsiniz. Açılır pencereleri gerçekleştirirken [Internet Explorer ile ilgili bazı bilinen sorunlar](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) olduğundan, Internet Explorer tarayıcısı ile yeniden yönlendirme yöntemlerini de kullanmanız önerilir.

Erişim belirteci isteği oluştururken erişim belirtecinin içermesini istediğiniz API kapsamlarını ayarlayabilirsiniz. Tüm istenen kapsamların erişim belirtecine verilmediğini ve kullanıcının onayını belirtebileceğini unutmayın.

## <a name="acquire-token-with-a-pop-up-window"></a>Bir açılır pencere ile belirteç alma

### <a name="javascript"></a>JavaScript

Bir açılır deneyim için yöntemleri kullanarak yukarıdaki düzende:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

MSAL angular sarmalayıcısı, otomatik olarak erişim belirteçleri alacak ve bunları API 'lere HTTP isteklerine ekleyecek HTTP yakalayıcısını eklemenin kolaylığını sağlar.

API 'ler `protectedResourceMap` için kapsamları, belirteçleri otomatik olarak alırken msalyakalayıcısı 'nın isteyeceğini yapılandırma seçeneğinde belirtebilirsiniz.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

Sessiz belirteç alma başarısızlığının başarısı ve başarısızlığı için MSAL angular, abone olabileceğiniz geri çağrılar sağlar. Aboneliğinizi kaldırmak da önemlidir.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternatif olarak, çekirdek MSAL. js kitaplığı 'nda açıklandığı gibi belirteci al yöntemlerini kullanarak açıkça belirteçleri de edinebilirsiniz.

## <a name="acquire-token-with-redirect"></a>Yeniden yönlendirme ile belirteç al

### <a name="javascript"></a>JavaScript

Bu model yukarıda açıklanmıştır, ancak belirteci etkileşimli olarak almak için bir yeniden yönlendirme yöntemiyle gösterilir. Yeniden yönlendirme geri aramasını yukarıda belirtilen şekilde kaydetmeniz gerekir.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-for-optional-claims"></a>İsteğe bağlı talepler için istek
Uygulamanıza yönelik belirteçlere hangi ek taleplerin ekleneceğini belirtmek için isteğe bağlı talepler isteyebilirsiniz. İd_token içinde isteğe bağlı talepler istemek için, AuthenticationParameters. TS sınıfının claimsRequest alanına bir strıngiingclaim nesnesi gönderebilirsiniz.

Aşağıdaki amaçla isteğe bağlı talepler kullanabilirsiniz:

- Uygulamanıza yönelik belirteçlere ek talepler eklemek için.
- Azure AD 'nin belirteçlerde döndürdüğü belirli taleplerin davranışını değiştirin.
- Uygulamanız için özel talepler ekleyin ve erişin.


### <a name="javascript"></a>JavaScript
```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```
İsteğe bağlı talepler hakkında daha fazla bilgi edinmek için [isteğe bağlı talepler](active-directory-optional-claims.md) alın


### <a name="angular"></a>Angular

Bu, yukarıda açıklanan şekilde aynıdır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Web API 'SI çağırma](scenario-spa-call-api.md)

---
title: Çoklu oturum açma (JavaScript için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: JavaScript için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL. js) kullanarak çoklu oturum açma deneyimleri oluşturma hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: da39b8435acdd11108a945c6bac5147dc8b6ad50
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150584"
---
# <a name="single-sign-on-with-msaljs"></a>MSAL.js ile çoklu oturum açma

Çoklu oturum açma (SSO), kullanıcıların kimlik doğrulaması gerektirmeden birden çok uygulama arasında yeniden kullanılabilecek bir oturum oluşturmak ve oturum açması için kullanıcıların kimlik bilgilerini bir kez girmelerini sağlar. Bu, kullanıcıya sorunsuz bir deneyim sağlar ve kimlik bilgileri için yinelenen istekleri azaltır.

Azure AD, Kullanıcı ilk kez kimlik doğrulaması gerçekleştirdiğinde bir oturum tanımlama bilgisi ayarlayarak uygulamalara SSO özellikleri sağlar. MSAL. js kitaplığı, uygulamaların bunu birkaç şekilde kullanmasına olanak sağlar.

## <a name="sso-between-browser-tabs"></a>Tarayıcı sekmeleri arasındaki SSO

Uygulamanız birden çok sekmede açık olduğunda ve öncelikle bir sekmede Kullanıcı oturum açabilirseniz, Kullanıcı sorulmadan diğer sekmelerde da oturum açar. MSAL. js Kullanıcı için KIMLIK belirtecini tarayıcı `localStorage` önbelleğe alır ve diğer açık sekmelerdeki uygulamada kullanıcıyı oturum açar.

Varsayılan olarak, MSAL. js `sessionStorage` kullanır ve bu, oturumun sekmeler arasında paylaşılmasına izin vermez. Sekmeler arasında SSO almak için, MSAL. js ' de `cacheLocation` aşağıda gösterildiği gibi `localStorage` ayarladığınızdan emin olun.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Uygulamalar arasında SSO

Bir kullanıcı kimlik doğrulaması gerçekleştirdiğinde, tarayıcıda Azure AD etki alanı üzerinde bir oturum tanımlama bilgisi ayarlanır. MSAL. js, Kullanıcı için farklı uygulamalar arasında SSO sağlamak üzere bu oturum tanımlama bilgisine bağımlıdır. MSAL. js Ayrıca, uygulama etki alanı başına tarayıcı depolama alanındaki kullanıcının KIMLIK belirteçlerini ve erişim belirteçlerini önbelleğe alır. Sonuç olarak, SSO davranışı farklı durumlar için farklılık gösterir:  

### <a name="applications-on-the-same-domain"></a>Aynı etki alanındaki uygulamalar

Uygulamalar aynı etki alanında barındırıldığı zaman, Kullanıcı bir kez oturum açıp daha sonra bir istem olmadan diğer uygulamalara kimlik doğrulaması yapabilir. MSAL. js, SSO sağlamak için etki alanındaki Kullanıcı için önbelleğe alınmış belirteçlerden yararlanır.

### <a name="applications-on-different-domain"></a>Farklı etki alanındaki uygulamalar

Uygulamalar farklı etki alanlarında barındırılıyorsa, etki alanı A 'da önbelleğe alınan belirteçlere etki alanı B 'de MSAL. js tarafından erişilemez.

Bu, etki alanı A 'da oturum açan kullanıcılar B etki alanındaki bir uygulamaya gittiklerinde, bu kullanıcıların Azure AD sayfasında yeniden yönlendirilmesi veya istenir olması anlamına gelir. Azure AD hala Kullanıcı oturumu tanımlama bilgisine sahip olduğundan, Kullanıcı oturum açacaktır ve kimlik bilgilerini yeniden girmesi gerekmez. Kullanıcının oturum açmak için Azure AD ile birden çok kullanıcı hesabı varsa, kullanıcıdan oturum açması için ilgili hesabı seçmesi istenir.

### <a name="automatically-select-account-on-azure-ad"></a>Azure AD 'de otomatik olarak hesap Seç

Bazı durumlarda, uygulamanın kullanıcının kimlik doğrulama bağlamına erişimi vardır ve birden çok hesap oturum açarken Azure AD hesabı seçim isteminden kaçınmak istiyor.  Bu, birkaç farklı şekilde yapılabilir:

**Oturum KIMLIĞINI (SID) kullanma**

Oturum KIMLIĞI, KIMLIK belirteçlerinde yapılandırılabilecek [isteğe bağlı bir talep](active-directory-optional-claims.md) . Bu talep, uygulamanın kullanıcının hesap adının veya Kullanıcı adından bağımsız olarak kullanıcının Azure AD oturumunu belirlemesine izin verir. SID 'yi istek parametrelerine `acquireTokenSilent` çağrısına geçirebilirsiniz. Bu, Azure AD 'nin hesap seçimini atlamasına izin verir. SID, oturum tanımlama bilgisine bağlanır ve tarayıcı bağlamlarına geçmez.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID, yalnızca MSAL. js içinde `acquireTokenSilent` çağrısı tarafından yapılan sessiz kimlik doğrulama istekleri ile kullanılabilir.
Uygulama bildiriminizde isteğe bağlı talepler yapılandırma adımlarını [buradan](active-directory-optional-claims.md)öğrenebilirsiniz.

**Oturum açma Ipucu kullanma**

SID talebi yapılandırılmamışsa veya etkileşimli kimlik doğrulama çağrılarında hesap seçim sorgusunu atlamanıza gerek yoksa, istek parametrelerine bir `login_hint` sağlayarak ve isteğe bağlı olarak MSAL. js etkileşimli yöntemlerinde `extraQueryParameters` olarak bir `domain_hint` sağlayarak bunu yapabilirsiniz (@no __t_3_, `loginRedirect`, `acquireTokenPopup` ve `acquireTokenRedirect`). Örnek:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Login_hint ve domain_hint değerlerini, kullanıcının KIMLIK belirtecinde döndürülen talepleri okuyarak alabilirsiniz.

* **Loginipucu** , kimlik belirtecinde `preferred_username` talebine ayarlanmalıdır.

* **domain_hint** yalnızca sık karşılaşılan yetkilisi kullanılırken geçirilmesi gerekir. Etki alanı ipucu, kiracı KIMLIĞI (TID) tarafından belirlenir.  KIMLIK belirtecindeki `tid` talebi `9188040d-6c67-4c5b-b112-36a304b66dad` tüketicilerdir. Aksi takdirde, kuruluşlardır.

Oturum açma ipucu ve etki alanı ipucu değerleri hakkında daha fazla bilgi için [buraya](v2-oauth2-implicit-grant-flow.md) okuyun.

> [!Note]
> SID ve login_hint aynı anda geçirilemez. Bu, hata yanıtına neden olur.

## <a name="sso-without-msaljs-login"></a>MSAL. js oturum açma olmadan SSO

Tasarıma göre, MSAL. js, API 'Ler için belirteçleri almadan önce bir kullanıcı bağlamı oluşturmak üzere bir oturum açma yönteminin çağrılması gerekir. Oturum açma yöntemleri etkileşimli olduğundan Kullanıcı bir istem görür.

Uygulamaların kimliği doğrulanmış kullanıcının bağlamına veya kimlik belirtecine, başka bir uygulamada başlatılan kimlik doğrulaması aracılığıyla erişiminin olduğu bazı durumlar vardır ve ilk olarak MSAL. js aracılığıyla oturum açmadan belirteçleri almak için SSO 'yu kullanmasını istersiniz.

Buna bir örnek: bir Kullanıcı, eklenti veya eklenti olarak çalışan başka bir JavaScript uygulamasını barındıran bir üst Web uygulamasında oturum açtı.

Bu senaryodaki SSO deneyimi aşağıdaki gibi sağlanabilir:

Varsa `sid` (veya `login_hint` ve isteğe bağlı olarak `domain_hint`) MSAL. js `acquireTokenSilent` çağrısına istek parametreleri olarak aşağıdaki gibi geçirin:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>ADAL. js ile MSAL. js güncelleştirmesine yönelik SSO

MSAL. js, Azure AD kimlik doğrulama senaryoları için ADAL. js ile özellik eşliği getirir. ADAL. js ' den MSAL. js ' ye geçiş yapmak ve kullanıcılarınızın yeniden oturum açmasını önlemek için, kitaplık kullanıcının ADAL. js önbelleğinde Kullanıcı oturumunu temsil eden KIMLIK belirtecini okur ve Kullanıcı için MSAL. js ' de sorunsuzca oturum açar.  

ADAL. js ' den güncelleştirme sırasında çoklu oturum açma (SSO) davranışından yararlanmak için, kitaplıkların önbelleğe alma belirteçleri için `localStorage` kullandığından emin olmanız gerekir. `cacheLocation`, başlatma sırasında hem MSAL. js hem de ADAL. js yapılandırmasında şu şekilde `localStorage` olarak ayarlayın:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Bu yapılandırıldıktan sonra, MSAL. js, ADAL. js ' de kimliği doğrulanmış kullanıcının önbelleğe alınmış durumunu okuyabilir ve bunu MSAL. js ' de SSO sağlamak için kullanabilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD 'de [Çoklu oturum açma oturumu ve belirteç yaşam süresi](active-directory-configurable-token-lifetimes.md) değerleri hakkında daha fazla bilgi edinin.

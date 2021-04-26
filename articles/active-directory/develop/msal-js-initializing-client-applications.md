---
title: MSAL.js istemci uygulamalarını Başlat | Mavisi
titleSuffix: Microsoft identity platform
description: JavaScript (MSAL.js) için Microsoft kimlik doğrulama kitaplığı 'nı kullanarak istemci uygulamalarını başlatma hakkında bilgi edinin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: fee241ba9f4a340513951c515f1996fc7e2385bf
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063602"
---
# <a name="initialize-client-applications-using-msaljs"></a>MSAL.js kullanarak istemci uygulamalarını başlatma

Bu makalede, bir Kullanıcı Aracısı uygulamasının örneğiyle JavaScript (MSAL.js) için Microsoft kimlik doğrulama Kitaplığı başlatma açıklanmaktadır.

Kullanıcı Aracısı uygulaması, istemci kodunun web tarayıcısı gibi bir Kullanıcı aracısında yürütüldüğü bir ortak istemci uygulaması biçimidir. Tarayıcı bağlamı düzgün şekilde erişilebilir olduğundan bu tür istemciler gizli dizileri depolamaz.

İstemci uygulama türleri ve uygulama yapılandırma seçenekleri hakkında daha fazla bilgi edinmek için bkz. [msal 'de ortak ve gizli istemci uygulamaları](msal-client-applications.md).

## <a name="prerequisites"></a>Önkoşullar

Bir uygulamayı başlatmadan önce, uygulamanız ile Microsoft Identity platformu arasında bir güven ilişkisi kurarak, ilk olarak [Azure Portal kaydetmeniz](scenario-spa-app-registration.md)gerekir.

Uygulamanızı kaydettikten sonra, Azure portal bulunan aşağıdaki değerlerin bazılarına veya tümüne ihtiyaç duyarsınız.

| Değer | Gerekli | Açıklama |
|:----- | :------: | :---------- |
| Uygulama (istemci) kimliği | Gerekli | Uygulamanızı Microsoft Identity platform içinde benzersiz bir şekilde tanımlayan GUID. |
| Yetkili | İsteğe Bağlı | Uygulamanız için kimlik sağlayıcısı URL 'SI ( *örnek*) ve *oturum açma hedef kitlesi* . Örnek ve oturum açma hedef kitlesi, birleştirilmiş olduğunda *yetkiyi* yapar. |
| Dizin (kiracı) kimliği | İsteğe Bağlı | Yalnızca kuruluşunuz için bir iş kolu uygulaması oluşturuyorsanız, genellikle *tek kiracılı uygulama* olarak adlandırılan bunu belirtin. |
| Yeniden Yönlendirme URI'si | İsteğe Bağlı | Bir Web uygulaması oluşturuyorsanız, `redirectUri` kimlik sağlayıcısının (Microsoft Identity Platform) verdiği güvenlik belirteçlerini döndürmesi gerektiğini belirtir. |

## <a name="initialize-msaljs-2x-apps"></a>MSAL.js 2. x uygulamalarını başlatın

[Yapılandırma][msal-js-configuration] nesnesiyle bir [Publicclientapplication][msal-js-publicclientapplication] örneği oluşturarak msal kimlik doğrulaması bağlamını başlatın. Gerekli en düşük yapılandırma özelliği, `clientID` Azure Portal uygulama kaydının **genel bakış** sayfasında **uygulama (istemci) kimliği** olarak gösterilen uygulamanızın uygulamasıdır.

Örnek bir yapılandırma nesnesi ve örneklenmesi aşağıda verilmiştir `PublicClientApplication` :

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

Uygulamanız yeniden yönlendirme akışlarını kullandığında [Handleredirectpromise][msal-js-handleredirectpromise] komutunu çağırın. Yeniden yönlendirme akışları kullanıldığında, `handleRedirectPromise` her sayfa yüküne çalıştırılmalıdır.

Taahhüdden üç olası sonuç vardır:

- `.then` çağrıldı ve `tokenResponse` Bu işlem başarılı oldu: uygulama başarılı bir yeniden yönlendirme işleminden döndürülüyor.
- `.then` çağrıldı ve `tokenResponse` falsy ( `null` ): uygulama bir yeniden yönlendirme işleminden dönmedi.
- `.catch` çağrıldı: uygulama bir yeniden yönlendirme işleminden döndürülüyor ve bir hata oluştu.

## <a name="initialize-msaljs-1x-apps"></a>MSAL.js 1. x uygulamalarını başlatın

Yapılandırma nesnesiyle bir [userbir TApplication][msal-js-useragentapplication] ÖRNEĞI oluşturarak msal 1. x kimlik doğrulama bağlamını başlatın. Gerekli en düşük yapılandırma özelliği, `clientID` Azure Portal uygulama kaydının **genel bakış** sayfasında **uygulama (istemci) kimliği** olarak gösterilen uygulamanızın uygulamasıdır.

MSAL.js 1.2. x veya önceki sürümlerde yeniden yönlendirme akışlarıyla ([loginredirect][msal-js-loginredirect] ve [acquiretokenredirect][msal-js-acquiretokenredirect]) kimlik doğrulama yöntemleri için, yöntem aracılığıyla başarılı veya hata için bir geri çağırma işlemini açıkça kaydetmeniz gerekir `handleRedirectCallback()` . Geri çağırma, MSAL.js 1.2. x ve daha önceki sürümlerde gereklidir çünkü yeniden yönlendirme akışları, bir açılır deneyimle birlikte Yöntemler gibi ödün vermez. Geri aramanın kaydedilmesi MSAL.js 1.3. x ve üzeri bir sürümde *isteğe bağlıdır* .

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>Tek örnek ve yapılandırma

Hem MSAL.js 1. x hem de 2. x, `UserAgentApplication` `PublicClientApplication` tek bir kimlik doğrulama bağlamını göstermek için sırasıyla tek bir örneğe ve yapılandırmaya sahip olacak şekilde tasarlanmıştır.

`UserAgentApplication`Ya da, `PublicClientApplication` tarayıcıda çakışan önbellek girişlerine ve davranışına neden olan birden çok örneği önerilmez.

## <a name="next-steps"></a>Sonraki adımlar

GitHub 'daki bu MSAL.js 2. x kod örneği, bir [yapılandırma][msal-js-configuration] nesnesi Ile bir [Publicclientapplication][msal-js-publicclientapplication] örneğini oluşturmayı gösterir:

[Azure-Samples/MS-Identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal.html#configuration
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html

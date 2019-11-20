---
title: İstemci uygulamalarını başlatma (JavaScript için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: JavaScript için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL. js) kullanarak istemci uygulamalarını başlatma hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25f2776b9b5683a345c55af4bc0020fef8109edd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150636"
---
# <a name="initialize-client-applications-using-msaljs"></a>MSAL. js kullanarak istemci uygulamalarını başlatma
Bu makalede, Kullanıcı Aracısı uygulamasının bir örneğiyle JavaScript (MSAL. js) için Microsoft kimlik doğrulama Kitaplığı başlatma açıklanmaktadır. Kullanıcı Aracısı uygulaması, istemci kodunun web tarayıcısı gibi bir Kullanıcı aracısında yürütüldüğü bir ortak istemci uygulaması biçimidir. Tarayıcı bağlamı başarıyla erişilebilir olduğundan bu istemciler gizli dizileri depolamaz. İstemci uygulama türleri ve uygulama yapılandırma seçenekleri hakkında daha fazla bilgi edinmek için [genel bakış](msal-client-applications.md)makalesini okuyun.

## <a name="prerequisites"></a>Önkoşullar
Bir uygulamayı başlatmadan önce, uygulamanızın Microsoft Identity platformu ile tümleştirilebilmesi için öncelikle [bu Azure Portal kaydetmeniz](scenario-spa-app-registration.md) gerekir. Kayıttan sonra, aşağıdaki bilgiler (Azure portal bulunabilir) gerekebilir:

- İstemci KIMLIĞI (uygulamanız için GUID 'ı temsil eden bir dize)
- Kimlik sağlayıcısı URL 'SI (örnek olarak adlandırılır) ve uygulamanız için oturum açma hedef kitlesi. Bu iki parametre, her topluca yetkili olarak bilinir.
- Yalnızca kuruluşunuz için bir iş kolu uygulaması yazıyorsanız (tek kiracılı uygulama olarak da adlandırılır) kiracı KIMLIĞI.
- Web Apps için, kimlik sağlayıcısının güvenlik belirteçleriyle uygulamanıza geri dönebileceği redirectUri 'yi de ayarlamanız gerekir.

## <a name="initializing-applications"></a>Uygulamalar başlatılıyor

MSAL. js ' i düz bir JavaScript/TypeScript uygulamasında aşağıdaki gibi kullanabilirsiniz. Yapılandırma nesnesiyle `UserAgentApplication` oluşturarak MSAL kimlik doğrulaması bağlamını başlatın. MSAL. js ' nin başlatılması için gereken en düşük yapılandırma, uygulamanızın kayıt portalından almanız gereken ClientID 'dir.

Yeniden yönlendirme akışlarıyla (`loginRedirect` ve `acquireTokenRedirect`) kimlik doğrulama yöntemleri için, `handleRedirectCallback()` yöntemi aracılığıyla başarılı veya hata için bir geri çağırma işlemini açıkça kaydetmeniz gerekecektir. Bu, yeniden yönlendirme akışları, bir açılır deneyimle birlikte olan yöntemler olarak geri dönmediğinden gereklidir.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL. js, tek bir kimlik doğrulama bağlamını temsil etmek için `UserAgentApplication` tek bir örneğe ve yapılandırmaya sahip olacak şekilde tasarlanmıştır. Birden çok örnek, tarayıcıda çakışan önbellek girişlerine ve davranışına neden olduğundan önerilmez.

## <a name="configuration-options"></a>Yapılandırma seçenekleri

MSAL. js, bir `UserAgentApplication`örneği oluşturmak için kullanılabilen yapılandırılabilir seçenekler gruplandırması sağlayan aşağıda gösterilen bir yapılandırma nesnesine sahiptir.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Şu anda yapılandırma nesnesinde desteklenen yapılandırılabilir seçeneklerin toplam kümesi aşağıda verilmiştir:

- **ClientID**: gerekli. Uygulamanızın ClientID 'si, bunu uygulama kayıt portalından almalısınız.

- **yetkili**: isteğe bağlı. MSAL 'in belirteçleri isteyebileceğini bir dizin gösteren URL. Varsayılan değer: `https://login.microsoftonline.com/common`.
    * Azure AD 'de,&gt;örnek &lt;kimlik sağlayıcısı etki alanı (örneğin,&gt;) ve `https://login.microsoftonline.com`seyirci &lt;bir tanımlayıcı olduğu/&lt;hedef kitle&gt; olan https://&lt;örneği&gt;. oturum açma kitini temsil etme. Bu, aşağıdaki değerler olabilir:
        * `https://login.microsoftonline.com/<tenant>`kiracı, contoso.onmicrosoft.com gibi kiracıyla ilişkili bir etki alanıdır veya yalnızca belirli bir kuruluşun kullanıcılarına oturum açmak için kullanılan dizinin `TenantID` özelliğini temsil eden GUID 'dir.
        * `https://login.microsoftonline.com/common`-kullanıcılara iş ve okul hesaplarıyla veya bir Microsoft kişisel hesabıyla oturum açmak için kullanılır.
        * `https://login.microsoftonline.com/organizations/`-kullanıcılara iş ve okul hesaplarıyla oturum açmak için kullanılır.
        * `https://login.microsoftonline.com/consumers/`-kullanıcılara yalnızca kişisel Microsoft hesabı (canlı) oturum açmak için kullanılır.
    * Azure AD B2C, örneğin `https://<instance>/tfp/<tenant>/<policyName>/`Azure AD B2C etki alanı olduğu, kiracı Azure AD B2C kiracının adı, policyName ise uygulanacak B2C ilkesinin adıdır.


- **Validateauthority**: isteğe bağlı.  Belirteçlerin vereni doğrulayın. `true` varsayılan değerdir. B2C uygulamaları için, yetkili değeri bilindiğinden ve ilke başına farklı olduğundan, yetkili doğrulaması çalışmaz ve `false`olarak ayarlanması gerekir.

- **Redirecturi**: isteğe bağlı.  Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın yeniden yönlendirme URI 'SI. Portalda kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. Varsayılan olarak `window.location.href`olur.

- **Postlogoutredirecturi**: isteğe bağlı.  Oturumu kapattıktan sonra kullanıcıyı `postLogoutRedirectUri` yönlendirir. Varsayılan değer `redirectUri`.

- **Navigatetologinrequesturl**: isteğe bağlı. Oturum açtıktan sonra başlangıç sayfasına varsayılan gezintiyi kapatma özelliği. Varsayılan değer true 'dur. Bu yalnızca yeniden yönlendirme akışları için kullanılır.

- **Önbellekelocation**: isteğe bağlı.  Tarayıcı depolamayı `localStorage` ya da `sessionStorage`olarak ayarlar. Varsayılan değer: `sessionStorage`.

- **Storeauthstateıncookie**: isteğe bağlı.  Bu bayrak, Microsoft Internet Explorer ve Microsoft Edge 'de [kimlik doğrulama döngüsü sorunları](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) için bir çözüm olarak msal. js v 0.2.2 ' de kullanıma sunulmuştur. Bu düzeltmeden yararlanmak için bayrağını doğru olarak etkinleştirin `storeAuthStateInCookie`. Bu etkinleştirildiğinde, MSAL. js, tarayıcı tanımlama bilgilerinde kimlik doğrulama akışlarının doğrulanması için gereken kimlik doğrulama isteği durumunu depolar. Varsayılan olarak, bu bayrak `false`olarak ayarlanır.

- **günlükçü**: isteğe bağlı.  Günlükleri özel bir biçimde tüketmek ve yayımlamak için geliştirici tarafından sağlanarak geri çağırma örneği olan bir günlükçü nesnesi. Günlükçü nesnesini geçirme hakkında ayrıntılı bilgi için bkz. [msal. js ile günlüğe kaydetme](msal-logging.md).

- **Loadframetimeout**: isteğe bağlı.  Azure AD 'den bir belirteç yenileme yanıtı zaman aşımına uğramadan önce geçen işlem yapılmayan milisaniye sayısı. Varsayılan değer 6 saniyedir.

- **tokenRenewalOffsetSeconds**: isteğe bağlı. Süre sonu öncesinde belirteci yenilemek için gereken konum penceresini ayarlayan milisaniye sayısı. Varsayılan değer 300 milisaniyedir.

- **Navigateframewait**: isteğe bağlı. Gizli iframe 'ler hedefine geçmeden önce bekleme süresini ayarlayan milisaniye sayısı. Varsayılan değer 500 milisaniyedir.

Bunlar yalnızca MSAL angular sarmalayıcı kitaplığından geçirilme için geçerlidir:
- **Unprotectedresources**: isteğe bağlı.  Korumasız kaynakları olan URI 'Ler dizisi. MSAL, bu URI 'ye sahip giden isteklere belirteç iliştirmez. Varsayılan olarak `null`olur.

- **Protectedresourcemap**: isteğe bağlı.  Bu, Web API çağrılarında erişim belirteçlerini otomatik olarak eklemek için MSAL tarafından kullanılan kapsamlara yönelik kaynakları eşleştirmekte. Kaynak için tek bir erişim belirteci alındı. Bu nedenle, belirli bir kaynak yolunu şu şekilde eşleyebilirsiniz: {"https://graph.microsoft.com/v1.0/me", ["User. Read"]} veya kaynağın uygulama URL 'SI: {"https://graph.microsoft.com/", ["User. Read", "MAIL. Send"]}. CORS çağrıları için bu gereklidir. Varsayılan olarak `null`olur.

---
title: 'Öğretici: tek sayfalı bir uygulamada kimlik doğrulamasını etkinleştirme'
titleSuffix: Azure AD B2C
description: Bu öğreticide, JavaScript tabanlı tek sayfalı uygulama (SPA) için Kullanıcı oturumu açma sağlamak üzere Azure Active Directory B2C nasıl kullanacağınızı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6a9f3b864bd8aba2140c7d32d4b5474ff7b95f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96171237"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Öğretici: Azure AD B2C ile tek sayfalı bir uygulamada kimlik doğrulamasını etkinleştirme

Bu öğreticide, tek sayfalı bir uygulamada (SPA) kullanıcıları kaydolmak ve oturum açmak için aşağıdakilerden birini kullanarak Azure Active Directory B2C (Azure AD B2C) nasıl kullanılacağı gösterilmektedir:
* [OAuth 2,0 yetkilendirme kodu akışı](./authorization-code-flow.md) ( [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)kullanarak)
* [OAuth 2,0 örtük verme akışı](./implicit-flow-single-page-application.md) ( [MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)kullanarak)

Bu öğreticide, birincisi iki bölümlü bir seride:

> [!div class="checklist"]
> * Azure AD B2C kiracınızda kayıtlı bir uygulamaya yanıt URL 'SI ekleme
> * GitHub 'dan bir kod örneği indirin
> * Örnek uygulamanın kodunu kiracınızla çalışacak şekilde değiştirin
> * Kaydolma/oturum açma Kullanıcı akışınızı kullanarak kaydolun

Serideki [sonraki öğretici](tutorial-single-page-app-webapi.md) , kod ÖRNEĞININ Web API bölümünü sunar.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticideki adımlara devam etmeden önce aşağıdaki Azure AD B2C kaynaklara sahip olmanız gerekir:

* [Azure AD B2C kiracı](tutorial-create-tenant.md)
* Kiracınızda [kayıtlı uygulama](tutorial-register-spa.md)
* Kiracınızda [oluşturulan kullanıcı akışları](tutorial-create-user-flows.md)

Ayrıca, yerel geliştirme ortamınızda aşağıdakiler gereklidir:

* [Visual Studio Code](https://code.visualstudio.com/) veya başka bir kod Düzenleyicisi
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Uygulamayı güncelleştirme

Önkoşulların bir parçası olarak tamamladığınız [ikinci öğreticide](./tutorial-register-spa.md) , Azure AD B2C bir tek sayfalı uygulama kaydettiniz. Bu öğreticide kod örneğiyle iletişimi etkinleştirmek için uygulama kaydına bir yanıt URL 'SI (yeniden yönlendirme URI 'SI olarak da bilinir) ekleyin.

Azure AD B2C kiracınızdaki bir uygulamayı güncelleştirmek için yeni Birleşik **uygulama kayıtları** deneyimimizi veya eski  **uygulamalarımız (eski)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](./app-registrations-training-guide.md).

#### <a name="app-registrations-auth-code-flow"></a>[Uygulama kayıtları (kimlik doğrulama kodu akışı)](#tab/app-reg-auth/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin, **sahip olunan uygulamalar** sekmesini seçin ve ardından *spaapp1* uygulamasını seçin.
1. **Tek sayfalı uygulama** altında **URI Ekle** bağlantısını seçin ve girin `http://localhost:6420` .
1. **Kaydet**’i seçin.
1. **Genel bakış**'ı seçin.
1. Tek sayfalı Web uygulamasındaki kodu güncelleştirdiğinizde daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

#### <a name="app-registrations-implicit-flow"></a>[Uygulama kayıtları (örtük akış)](#tab/app-reg-implicit/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin, **sahip olunan uygulamalar** sekmesini seçin ve ardından *spaapp1* uygulamasını seçin.
1. **Tek sayfalı uygulama** altında **URI Ekle** bağlantısını seçin ve girin `http://localhost:6420` .
1. **Örtük izin**' ın altında, **erişim belirteçleri** ve **Kimlik belirteçleri** için henüz seçili değilse onay kutularını seçin ve ardından **Kaydet**' i seçin.
1. **Genel bakış**'ı seçin.
1. Tek sayfalı Web uygulamasındaki kodu güncelleştirdiğinizde daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve sonra **Azure AD B2C**' i arayıp seçin.
1. **Uygulamalar (eski)** öğesini seçin ve ardından *spaapp1* uygulamasını seçin.
1. **Yanıt URL 'si** altında, ekleyin `http://localhost:6420` .
1. **Kaydet**’i seçin.
1. Özellikler sayfasında, **uygulama kimliğini** kaydedin. Uygulama KIMLIĞI ' ni, tek sayfalı Web uygulamasındaki kodu güncelleştirdiğinizde sonraki bir adımda kullanırsınız.

* * *

## <a name="get-the-sample-code"></a>Örnek kodunu alma

Bu öğreticide, GitHub 'dan yüklediğiniz bir kod örneğini B2C kiracınızla çalışacak şekilde yapılandırırsınız. Örnek, tek sayfalı bir uygulamanın kullanıcı kaydı ve oturum açma için Azure AD B2C nasıl kullanılabileceğini ve korumalı bir Web API 'SI (serideki bir sonraki öğreticide Web API 'sini etkinleştirmenizi) nasıl çağırabileceğinizi gösterir.

* MSAL.js 2. x yetkilendirme kodu akış örneği:

    [Bir zip dosyası indirin](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) veya GitHub 'dan örneği kopyalayın:

    ```
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```
* MSAL.js 1. x örtük akış örneği:

    [Bir zip dosyası indirin](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) veya GitHub 'dan örneği kopyalayın:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="update-the-sample"></a>Örneği güncelleştirme

Örneği edindiğinizden, kodu Azure AD B2C kiracı adınızla ve önceki bir adımda kaydettiğiniz uygulama KIMLIĞIYLE güncelleştirin.

#### <a name="auth-code-flow-sample"></a>[Kimlik doğrulama kod akışı örneği](#tab/config-auth/)

1. *authConfig.js* dosyasını *uygulama* klasörü içinde açın.
1. `msalConfig`Nesnesinde, atamasını bulun `clientId` ve daha önceki bir adımda kaydettiğiniz **uygulama (istemci) kimliğiyle** değiştirin.
1. `policies.js` dosyasını açın.
1. Altında girişleri bulun `names` ve daha önceki bir adımda oluşturduğunuz Kullanıcı akışlarının adı ile atamasını değiştirin (örneğin,) `B2C_1_signupsignin1` .
1. Altındaki girdileri bulun `authorities` ve daha önceki bir adımda oluşturduğunuz Kullanıcı akışlarının adlarıyla uygun şekilde değiştirin `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` . örneğin.
1. Atamasını bulun `authorityDomain` ve ile değiştirin `<your-tenant-name>.b2clogin.com` .
1. `apiConfig.js` dosyasını açın.
1. İçin atamayı bulun `b2cScopes` ve URL 'yi, Web API 'si için oluşturduğunuz kapsam URL 'si ile değiştirin (örneğin,) `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` .
1. İçin atamayı bulun `webApi` ve geçerlI URL 'yi, 4. adımda Web API 'nizi DAĞıTTıĞıNıZ URL ile değiştirin `webApi: http://localhost:5000/hello` .

#### <a name="implicit-flow-sample"></a>[Örtük akış örneği](#tab/config-implicit/)

1. *authConfig.js* dosyasını *javascriptspa* klasörü içinde açın.
1. `msalConfig`Nesnesinde, atamasını bulun `clientId` ve daha önceki bir adımda kaydettiğiniz **uygulama (istemci) kimliğiyle** değiştirin.
1. `policies.js` dosyasını açın.
1. Altında girişleri bulun `names` ve daha önceki bir adımda oluşturduğunuz Kullanıcı akışlarının adı ile atamasını değiştirin (örneğin,) `B2C_1_signupsignin1` .
1. Altındaki girdileri bulun `authorities` ve daha önceki bir adımda oluşturduğunuz Kullanıcı akışlarının adlarıyla uygun şekilde değiştirin `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` . örneğin.
1. `apiConfig.js` dosyasını açın.
1. İçin atamayı bulun `b2cScopes` ve URL 'yi, Web API 'si için oluşturduğunuz kapsam URL 'si ile değiştirin (örneğin,) `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` .
1. İçin atamayı bulun `webApi` ve geçerlI URL 'yi, 4. adımda Web API 'nizi DAĞıTTıĞıNıZ URL ile değiştirin `webApi: http://localhost:5000/hello` .

* * *

Elde edilen kodunuz aşağıdakine benzer görünmelidir:

#### <a name="auth-code-flow-sample"></a>[Kimlik doğrulama kod akışı örneği](#tab/review-auth/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "fabrikamb2c.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

#### <a name="implicit-flow-sample"></a>[Örtük akış örneği](#tab/review-implicit/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

* * *


## <a name="run-the-sample"></a>Örneği çalıştırma

1. Bir konsol penceresi açın ve örneği içeren dizine gidin. 

    - MSAL.js 2. x yetkilendirmesi kod akışı örneği:

        ```console
        cd ms-identity-b2c-javascript-spa
        ```
    - MSAL.js 1. x örtük akış örneği: 

        ```console
        cd active-directory-b2c-javascript-msal-singlepageapp
        ```

1. Aşağıdaki komutları çalıştırın:

    ```console
    npm install && npm update
    npm start
    ```

    Konsol penceresinde yerel olarak çalışan Node.js sunucusunun bağlantı noktası numarası görüntülenir:

    ```console
    Listening on port 6420...
    ```
1. `http://localhost:6420`Yerel makinenizde çalışan Web uygulamasını görüntülemek için öğesine gidin.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Yerel olarak çalışan tek sayfalı uygulamayı gösteren Web tarayıcısı":::

### <a name="sign-up-using-an-email-address"></a>E-posta adresi kullanarak kaydolma

Bu örnek uygulama kaydolma, oturum açma ve parola sıfırlama 'yı destekler. Bu öğreticide, bir e-posta adresi kullanarak kaydolabilirsiniz.

1. Daha önceki bir adımda belirttiğiniz *B2C_1_signupsignin1* Kullanıcı akışını başlatmak Için **oturum aç '** ı seçin.
1. Azure AD B2C, kaydolma bağlantısı içeren bir oturum açma sayfası sunar. Henüz bir hesabınız olmadığından **Şimdi kaydolun** bağlantısını seçin.
1. Kaydolma iş akışı, bir e-posta adresi kullanarak kullanıcının kimliğini toplayıp doğrulamak için bir sayfa sunar. Kaydolma iş akışı, kullanıcının parolasını ve Kullanıcı akışında tanımlanan istenen öznitelikleri de toplar.

    Geçerli bir e-posta adresi kullanın ve doğrulama kodunu kullanarak doğrulamayı gerçekleştirin. Parola ayarlayın. İstenen öznitelikler için değerleri girin.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Azure AD B2C Kullanıcı akışı tarafından görünen kaydolma sayfası":::

1. Azure AD B2C dizininde yerel bir hesap oluşturmak için **Oluştur** ' u seçin.

**Oluştur**' u seçtiğinizde, uygulama, oturum açmış kullanıcının adını gösterir.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Oturum açmış kullanıcı ile tek sayfalı uygulamayı gösteren Web tarayıcısı":::

Oturum açma sınamasını yapmak **isterseniz oturumu Kapat düğmesini seçin** , **oturum aç** ' ı seçin ve kaydolduğunuzda girdiğiniz e-posta adresi ve parolayla oturum açın.

### <a name="what-about-calling-the-api"></a>API 'YI çağırma hakkında ne olacak?

Oturum açtıktan sonra **API çağrısı** düğmesini SEÇERSENIZ, API çağrısının sonuçları yerine kaydolma/oturum açma Kullanıcı akışı sayfası görüntülenir. Bu, uygulamanın API bölümünü Azure AD B2C *kiracınızda* kayıtlı BIR Web API uygulamasıyla iletişim kuracak şekilde yapılandırmadığınız için beklenmektedir.

Bu noktada, uygulama, tanıtım kiracısında (fabrikamb2c.onmicrosoft.com) kayıtlı olan API ile iletişim kurmaya çalışıyor ve bu kiracı ile kimlik doğrulamamış olduğunuzdan kaydolma/oturum açma sayfası görüntülenir.

Korumalı API 'yi etkinleştirmek için içindeki serideki bir sonraki öğreticiye geçin ( [sonraki adımlar](#next-steps) bölümüne bakın).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, oturum açma ve oturum açma yeteneğini sağlamak üzere Azure AD B2C kiracınızdaki bir Kullanıcı akışı ile çalışacak şekilde tek sayfalı bir uygulamayı yapılandırmış olursunuz. Şu adımları tamamladınız:

> [!div class="checklist"]
> * Azure AD B2C kiracınızda kayıtlı bir uygulamaya yanıt URL 'SI eklendi
> * GitHub 'dan bir kod örneği indirildi
> * Örnek uygulamanın kodunu kiracınızla çalışacak şekilde değiştirdi
> * Kaydolma/oturum açma Kullanıcı akışınız kullanılarak kaydolup

Artık, SPA 'dan korumalı bir Web API 'sine erişim sağlamak için serideki bir sonraki öğreticiye geçin:

> [!div class="nextstepaction"]
> [Öğretici: tek sayfalı bir uygulamadan Web API 'sine koruma ve erişim izni verme >](tutorial-single-page-app-webapi.md)
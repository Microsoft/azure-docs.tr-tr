---
title: 'Hızlı başlangıç: JavaScript tek sayfalı uygulamalardaki kullanıcıları oturum açma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, JavaScript uygulamasının Microsoft Identity platformu tarafından verilen erişim belirteçleri gerektiren bir API 'YI nasıl çağırabileceğinizi öğrenirsiniz.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 8e35342bd704f662d41f676f58e2cc14b54f29a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023393"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Hızlı başlangıç: bir JavaScript SPA 'da Kullanıcı oturumu açma ve erişim belirteci edinme

Bu hızlı başlangıçta, JavaScript tek sayfalı uygulamanın (SPA) kullanıcılara nasıl oturum açıp Microsoft Graph çağırabileceğinizi gösteren bir kod örneği indirip çalıştırırsınız. Kod örneği Ayrıca, Microsoft Graph API 'sini veya herhangi bir Web API 'sini çağırmak için bir erişim belirtecinin nasıl alınacağını gösterir.

Örneğin bir çizim için [nasıl çalıştığını](#how-the-sample-works) görün.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (proje dosyalarını düzenlemek için)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Hızlı başlangıç uygulamanızı kaydedin ve indirin
> Hızlı başlangıç uygulamanızı başlatmak için aşağıdaki seçeneklerden birini kullanın.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Seçenek 1 (Express): uygulamanızı kaydedin ve otomatik olarak yapılandırın ve ardından kod örneğinizi indirin
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure portal uygulama kayıtları</a> hızlı başlangıç deneyimine gidin.
> 1. Uygulamanız için bir ad girin.
> 1. **Desteklenen hesap türleri** altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
> 1. **Kaydet**’i seçin.
> 1. Yeni uygulamanızı indirip otomatik olarak yapılandırmak için yönergeleri izleyin.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Seçenek 2 (El Ile): uygulamanızı ve kod örneğinizi kaydetme ve el ile yapılandırma
>
> #### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
> 1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Azure Active Directory**'yi bulun ve seçin.
> 1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
> 1. Uygulamanız için bir **ad** girin. Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
> 1. **Desteklenen hesap türleri** altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
> 1. **Kaydet**’i seçin. Uygulamaya **genel bakış** sayfasında, daha sonra kullanılmak üzere **uygulama (istemci) kimliği** değerini aklınızda edin.
> 1. Bu hızlı başlangıç, [örtük izin akışının](v2-oauth2-implicit-grant-flow.md) etkinleştirilmesini gerektirir. **Yönet** altında **kimlik doğrulaması**' nı seçin.
> 1. **Platform yapılandırmalarında**  >  **Platform ekleme**. **Web**'i seçin.
> 1. **Yeniden YÖNLENDIRME URI** değerini olarak ayarlayın `http://localhost:3000/` . 
> 1. **Örtük verme ve karma akışlar** altında **erişim belirteçleri** ve **Kimlik belirteçleri** ' ni seçin.
> 1. **Yapılandır**'ı seçin.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. Adım: uygulamanızı Azure portal yapılandırma
> Bu hızlı başlangıçtaki kod örneğinin çalışması için, bir **yeniden yönlendirme URI 'si** ekleyin `http://localhost:3000/` ve **örtülü izni** etkinleştirin.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişiklikleri benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-javascript/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-the-project"></a>2. Adım: Projeyi indirme

> [!div renderon="docs"]
> Node.js kullanarak projeyi bir Web sunucusuyla çalıştırmak için, [temel proje dosyalarını indirin](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Node.js kullanarak projeyi bir Web sunucusu ile çalıştırma

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Kod örneğini indirin](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3. Adım: JavaScript uygulamanızı yapılandırma
>
> *Javascriptspa* klasöründe *authConfig.js* düzenleyin ve `clientID` `authority` `redirectUri` altında ve değerlerini ayarlayın `msalConfig` .
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Konum:
> - `Enter_the_Application_Id_Here` , kaydettiğiniz uygulamanın **uygulama (istemci) kimliğidir** .
>
>    **Uygulama (istemci) kimliğinin** değerini bulmak için, Azure Portal uygulamanın **genel bakış** sayfasına gidin.
> - `Enter_the_Cloud_Instance_Id_Here` , Azure bulutu örneğidir. Ana veya küresel Azure bulutu için yalnızca girmeniz yeterlidir `https://login.microsoftonline.com/` . **Ulusal** bulutlar (örneğin, Çin) için bkz. [Ulusal bulutlar](./authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` Aşağıdaki seçeneklerden birine ayarlanır:
>    - Uygulamanız *bu kuruluş dizinindeki hesapları* destekliyorsa, bu DEĞERI **Kiracı kimliği** veya **kiracı adı** (örneğin,) ile değiştirin `contoso.microsoft.com` .
>
>    **Dizin (kiracı) kimliğinin** değerini bulmak için, Azure Portal uygulama kaydının **genel bakış** sayfasına gidin.
>    - Uygulamanız *herhangi bir kuruluş dizinindeki hesapları* destekliyorsa, bu değeri ile değiştirin `organizations` .
>    - Uygulamanız *herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesapları* destekliyorsa, bu değeri ile değiştirin `common` . *Yalnızca kişisel Microsoft hesaplarına* yönelik desteği kısıtlamak için bu değeri ile değiştirin `consumers` .
>
>    **Desteklenen hesap türlerinin** değerini bulmak için, Azure Portal uygulama kaydının **genel bakış** sayfasına gidin.
> - `Enter_the_Redirect_Uri_Here`, `http://localhost:3000/` değeridir.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. Adım: uygulamanız yapılandırıldı ve çalıştırılmaya hazırlanıyor
> Projenizi uygulamanızın özelliklerinin değerleriyle yapılandırdık.

> [!div renderon="docs"]
>
> Ardından, aynı klasörde, ve nesnesini ayarlamak için *graphConfig.js* dosyayı düzenleyin `graphMeEndpoint` `graphMeEndpoint` `apiConfig` .
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Konum:
> - *\<Enter_the_Graph_Endpoint_Here>* , API çağrılarının üzerinde hale getirilme bitiş noktasıdır. Ana veya küresel Microsoft Graph API hizmeti için yalnızca girmeniz yeterlidir `https://graph.microsoft.com/` . Daha fazla bilgi için bkz. [Ulusal bulut dağıtımı](/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>4. Adım: projeyi çalıştırma

[Node.js](https://nodejs.org/en/download/)kullanarak projeyi bir Web sunucusuyla çalıştırın:

1. Sunucuyu başlatmak için, proje dizininden aşağıdaki komutu çalıştırın:
    ```cmd
    npm install
    npm start
    ```
1. Bir Web tarayıcısı açın ve adresine gidin `http://localhost:3000/` .

1. Oturum açmak için **oturum aç** ' ı seçin ve ardından Microsoft Graph API 'yi çağırın.

Tarayıcı uygulamayı yükledikten sonra **oturum aç**' ı seçin. İlk kez oturum açtığınızda, uygulamanın profilinize erişmesine ve oturumunuzu açmasını sağlamak için onayınızı vermeniz istenir. Başarıyla oturum açtıktan sonra, Kullanıcı profili bilgilerinizin sayfada görüntülenmesi gerekir.

## <a name="more-information"></a>Daha fazla bilgi

### <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı

![Örnek JavaScript SPA 'nın çalışması: 1. SPA, oturum açma işlemini başlatır. 2. SPA, Microsoft Identity platformundan bir KIMLIK belirteci alır. 3. SPA, belirteç alma 'yı çağırır. 4. Microsoft Identity platformu, SPA 'ya bir erişim belirteci döndürür. 5. SPA, Microsoft Graph API 'sine yönelik acess belirtecini ve HTTP GET isteğini yapar. 6. Graph API SPA 'ya HTTP yanıtı döndürür.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL kitaplığı, kullanıcıları imzalar ve Microsoft Identity platform tarafından korunan bir API 'ye erişmek için kullanılan belirteçleri ister. Hızlı Başlangıç *index.html* dosyası kitaplığa bir başvuru içerir:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```

Önceki sürümü, [MSAL.js sürümleri](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)altındaki en son yayınlanan sürümle değiştirebilirsiniz.

Alternatif olarak, Node.js yüklüyse, en son sürümü Node.js Paket Yöneticisi (NPM) aracılığıyla indirebilirsiniz:

```cmd
npm install msal
```

### <a name="msal-initialization"></a>MSAL başlatma

Hızlı başlangıç kodu ayrıca MSAL kitaplığının nasıl başlatılacağını gösterir:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

|Konum  | Description |
|---------|---------|
|`clientId`     | Azure portal kayıtlı uygulamanın uygulama KIMLIĞI.|
|`authority`    | Seçim Daha önce yapılandırma bölümünde açıklandığı gibi, hesap türlerini destekleyen yetkili URL 'SI. Varsayılan yetkili `https://login.microsoftonline.com/common` . |
|`redirectUri`     | Uygulama kaydının yapılandırılmış yanıtı/redirectUri. Bu durumda, `http://localhost:3000/` . |
|`cacheLocation`  | Seçim Kimlik doğrulama durumu için tarayıcı depolamayı ayarlar. Varsayılan değer sessionStorage ' dır.   |
|`storeAuthStateInCookie`  | Seçim Tarayıcı tanımlama bilgilerinde kimlik doğrulama akışlarının doğrulanması için gerekli olan kimlik doğrulama isteği durumunu depolayan kitaplık. Bu tanımlama bilgisi, bazı [bilinen sorunları](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)azaltmak üzere IE ve Edge tarayıcıları için ayarlanır. |

Kullanılabilir yapılandırılabilir seçenekler hakkında daha fazla bilgi için bkz. [istemci uygulamalarını başlatma](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Oturum açma kullanıcıları

Aşağıdaki kod parçacığı, kullanıcıların oturum açma şeklini gösterir:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

|Konum  | Description |
|---------|---------|
| `scopes`   | Seçim Oturum açma sırasında kullanıcı onayı için istenen kapsamları içerir. Örneğin, `[ "user.read" ]` Microsoft Graph veya `[ "<Application ID URL>/scope" ]` özel Web API 'leri için (yani, `api://<Application ID>/access_as_user` ). |

Alternatif olarak, `loginRedirect` geçerli sayfayı bir açılan pencere yerine oturum açma sayfasına yeniden yönlendirmek için yöntemini kullanmak isteyebilirsiniz.

### <a name="request-tokens"></a>İstek belirteçleri

MSAL belirteçleri elde etmek için üç yöntem kullanır: `acquireTokenRedirect` , `acquireTokenPopup` ve `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

`acquireTokenSilent`Yöntemi, Kullanıcı etkileşimi olmadan belirteç alma ve yenileme işlemleri gerçekleştirir. Ya da `loginRedirect` `loginPopup` yöntemi ilk kez yürütüldükten sonra, `acquireTokenSilent` sonraki çağrılar için korunan kaynaklara erişmek üzere kullanılan belirteçleri elde etmek için yaygın olarak kullanılan yöntemdir. Belirteçleri istek veya yenileme çağrıları sessizce yapılır.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

|Konum  | Description |
|---------|---------|
| `scopes`   | API için erişim belirtecine döndürülmek istenen kapsamları içerir. Örneğin, `[ "mail.read" ]` Microsoft Graph veya `[ "<Application ID URL>/scope" ]` özel Web API 'leri için (yani, `api://<Application ID>/access_as_user` ).|

#### <a name="get-a-user-token-interactively"></a>Etkileşimli olarak kullanıcı belirteci alma

Kullanıcıları Microsoft Identity platformu ile etkileşime zorlayan durumlar vardır. Örnek:
* Parolasının süresi sona erdiği için kullanıcıların kimlik bilgilerini yeniden girmesi gerekebilir.
* Uygulamanız, kullanıcının onaylaması gereken ek kaynak kapsamlarına erişim istiyor.
* İki öğeli kimlik doğrulaması gereklidir.

Çoğu uygulama için olağan olarak önerilen desenler ilk olarak çağrı yapılır `acquireTokenSilent` , sonra özel durumu yakalar ve sonra `acquireTokenPopup` `acquireTokenRedirect` etkileşimli bir istek başlatmak için (veya) çağırır.

`acquireTokenPopup`Oturum açmak için sonuçları açılan pencerede çağırma. (Veya `acquireTokenRedirect` kullanıcıları Microsoft Identity platformu 'na yönlendirmeye de neden olur). Bu pencerede, kullanıcıların kimlik bilgilerini onaylayarak, gerekli kaynağa onay vererek veya iki öğeli kimlik doğrulamasını tamamlayarak etkileşimde olmaları gerekir.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Bu hızlı başlangıç, `loginRedirect` `acquireTokenRedirect` Internet Explorer tarafından açılan pencerelerin işlenmesiyle ilgili bilinen bir [sorun](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) nedeniyle Microsoft Internet Explorer ile ve yöntemlerini kullanır.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıç için uygulama oluşturmaya yönelik daha ayrıntılı bir adım adım kılavuz için bkz.:

> [!div class="nextstepaction"]
> [Öğretici: Kullanıcı oturum açma ve JavaScript tek sayfalı uygulamadan (SPA) Microsoft Graph API 'sini çağırma](tutorial-v2-javascript-spa.md)

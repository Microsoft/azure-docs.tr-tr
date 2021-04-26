---
title: 'Hızlı başlangıç: kimlik doğrulama kodlu JavaScript tek sayfalı uygulamalarda (SPA) Kullanıcı oturum açma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, bir JavaScript tek sayfalı uygulamasının (SPA), yetkilendirme kodu akışını kullanarak kişisel hesap, iş hesabı ve okul hesaplarının kullanıcılarına nasıl oturum açabildiğini öğrenin.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 1e99e8ff25d895ba2248ddd1ba2520e9f14871a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022815"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow-with-pkce"></a>Hızlı başlangıç: Kullanıcı oturum açma ve bir JavaScript SPA 'da, PKCE ile kimlik doğrulama kod akışını kullanarak bir erişim belirteci edinme 

Bu hızlı başlangıçta, JavaScript tek sayfalı uygulamanın (SPA) Kullanıcı oturumunu nasıl çalıştırabildiğini ve kod değişimi (PKCE) için kanıt anahtarı ile yetkilendirme kodu akışını kullanarak Microsoft Graph arayacağını gösteren bir kod örneği indirip çalıştırırsınız. Kod örneği, Microsoft Graph API 'sini veya herhangi bir Web API 'sini çağırmak için bir erişim belirtecinin nasıl alınacağını gösterir. 

Örneğin bir çizim için [nasıl çalıştığını](#how-the-sample-works) görün.

Bu hızlı başlangıçta yetkilendirme kodu akışıyla MSAL.js v2 kullanılır. Örtük Flow ile MSAL.js v1 kullanan benzer bir hızlı başlangıç için bkz. [hızlı başlangıç: JavaScript tek sayfalı uygulamalarda oturum açma](./quickstart-v2-javascript.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz bir Azure aboneliği oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) veya başka bir kod Düzenleyicisi

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Hızlı başlangıç uygulamanızı kaydedin ve indirin
> Hızlı başlangıç uygulamanızı başlatmak için aşağıdaki seçeneklerden birini kullanın.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Seçenek 1 (Express): uygulamanızı kaydedin ve otomatik olarak yapılandırın ve ardından kod örneğinizi indirin
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure portal uygulama kayıtları</a>gidin.
> 1. Uygulamanız için bir ad girin.
> 1. **Desteklenen hesap türleri** altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
> 1. **Kaydet**’i seçin.
> 1. Hızlı başlangıç bölmesine gidin ve yeni uygulamanızı indirip otomatik olarak yapılandırmak için yönergeleri izleyin.
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
> 1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
> 1. **Platform yapılandırması** altında **Platform Ekle**' yi seçin. Açılan bölmedeki **tek sayfalı uygulama** Seç ' i seçin.
> 1. **Yeniden YÖNLENDIRME URI** değerini olarak ayarlayın `http://localhost:3000/` .
> 1. **Yapılandır**'ı seçin.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. Adım: uygulamanızı Azure portal yapılandırma
> Bu hızlı başlangıçtaki kod örneğinin çalışması için bir **yeniden yönlendirme URI 'si** ekleyin `http://localhost:3000/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişiklikleri benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-javascript/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-the-project"></a>2. Adım: Projeyi indirme

> [!div renderon="docs"]
> Node.js kullanarak projeyi bir Web sunucusuyla çalıştırmak için, [temel proje dosyalarını indirin](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Node.js kullanarak projeyi bir Web sunucusu ile çalıştırma

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Kod örneğini indirin](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3. Adım: JavaScript uygulamanızı yapılandırma
>
> *Uygulama* klasöründe *authConfig.js* dosyasını açın ve `clientID` `authority` nesnesindeki, ve değerlerini güncelleştirin `redirectUri` `msalConfig` .
>
> ```javascript
> // Config object to be passed to Msal on creation
> const msalConfig = {
>   auth: {
>     clientId: "Enter_the_Application_Id_Here",
>     authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>     redirectUri: "Enter_the_Redirect_Uri_Here",
>   },
>   cache: {
>     cacheLocation: "sessionStorage", // This configures where your cache will be stored
>     storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>   }
> };
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> `msalConfig`Bölümündeki değerleri burada açıklandığı gibi değiştirin:
>
> - `Enter_the_Application_Id_Here` , kaydettiğiniz uygulamanın **uygulama (istemci) kimliğidir** .
>
>    **Uygulama (istemci) kimliğinin** değerini bulmak için, Azure Portal uygulama kaydının **genel bakış** sayfasına gidin.
> - `Enter_the_Cloud_Instance_Id_Here` , Azure bulutu örneğidir. Ana veya küresel Azure bulutu için girin `https://login.microsoftonline.com/` . **Ulusal** bulutlar (örneğin, Çin) için bkz. [Ulusal bulutlar](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` aşağıdakilerden birine ayarlanır:
>   - Uygulamanız *bu kuruluş dizinindeki hesapları* destekliyorsa, bu DEĞERI **Kiracı kimliği** veya **kiracı adı** ile değiştirin. Örneğin, `contoso.microsoft.com`.
>
>    **Dizin (kiracı) kimliğinin** değerini bulmak için, Azure Portal uygulama kaydının **genel bakış** sayfasına gidin.
>   - Uygulamanız *herhangi bir kuruluş dizinindeki hesapları* destekliyorsa, bu değeri ile değiştirin `organizations` .
>   - Uygulamanız *herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesapları* destekliyorsa, bu değeri ile değiştirin `common` . **Bu hızlı başlangıç için** kullanın `common` .
>   - *Yalnızca kişisel Microsoft hesaplarına* yönelik desteği kısıtlamak için bu değeri ile değiştirin `consumers` .
>
>    **Desteklenen hesap türlerinin** değerini bulmak için, Azure Portal uygulama kaydının **genel bakış** sayfasına gidin.
> - `Enter_the_Redirect_Uri_Here`, `http://localhost:3000/` değeridir.
>
> `authority`Ana (genel) Azure bulutunu kullanıyorsanız, *authConfig.js* değerin aşağıdakine benzer olması gerekir:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. Adım: uygulamanız yapılandırıldı ve çalıştırılmaya hazırlanıyor
> Projenizi uygulamanızın özelliklerinin değerleriyle yapılandırdık.

> [!div renderon="docs"]
>
> Ardından, hala aynı klasörde *graphConfig.js* dosyasını düzenleyin ve `graphMeEndpoint` ve `graphMailEndpoint` değerlerini ve değerlerini güncelleştirin `apiConfig` .
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
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
> `Enter_the_Graph_Endpoint_Here` , API çağrılarının üzerinde hale getirilme bitiş noktasıdır. Ana (genel) Microsoft Graph API hizmeti için `https://graph.microsoft.com/` (sondaki eğik çizgiyi dahil et) girin. Ulusal bulutlarda Microsoft Graph hakkında daha fazla bilgi için bkz. [Ulusal bulut dağıtımı](/graph/deployments).
>
> `graphMeEndpoint` `graphMailEndpoint` Ana (global) Microsoft Graph API hizmetini kullanıyorsanız, *graphConfig.js* dosyadaki ve değerleri aşağıdakine benzer olmalıdır:
>
> ```javascript
> graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
> graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
> ```
>
> #### <a name="step-4-run-the-project"></a>4. Adım: projeyi çalıştırma

Node.js kullanarak projeyi bir Web sunucusuyla çalıştırın:

1. Sunucuyu başlatmak için, proje dizini içinden aşağıdaki komutları çalıştırın:
    ```console
    npm install
    npm start
    ```
1. `http://localhost:3000/` adresine gidin.

1. Oturum açma işlemini başlatmak için **oturum aç** ' ı seçin ve ardından Microsoft Graph API 'sini çağırın.

    İlk kez oturum açtığınızda, uygulamanın profilinize erişmesine izin vermek için onayınızı sağlamanız ve oturumunuzu açmanız istenir. Başarıyla oturum açtıktan sonra, Kullanıcı profili bilgilerinizin sayfada görüntülenmesi gerekir.

## <a name="more-information"></a>Daha fazla bilgi

### <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı

![Tek sayfalı bir uygulama için yetkilendirme kodu akışını gösteren diyagram.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

MSAL.js kitaplığı, kullanıcıları imzalar ve Microsoft Identity platform tarafından korunan bir API 'ye erişmek için kullanılan belirteçleri ister. Örneğin *index.html* dosyası, kitaplığa bir başvuru içerir:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```

Node.js yüklüyse, Node.js paket yöneticisini (NPM) kullanarak en son sürümü indirebilirsiniz:

```console
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta kullanılan uygulamayı oluşturmaya yönelik daha ayrıntılı bir adım adım kılavuz için aşağıdaki öğreticiye bakın:

> [!div class="nextstepaction"]
> [Oturum açma ve MS Graf çağırma öğreticisi](./tutorial-v2-javascript-auth-code.md)

---
title: "Hızlı başlangıç: JavaScript 'te oturum açma, kimlik doğrulama kodu ve çağrı Microsoft Graph tek sayfalı uygulamaları (SPA) tepki verir | Mavisi"
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, bir JavaScript 'e tek sayfalı uygulamanın (SPA), yetkilendirme kodu akışını kullanarak kişisel hesap, iş hesabı ve okul hesapları kullanıcılarına nasıl oturum açabildiğini öğrenin ve Microsoft Graph çağırın.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/14/2021
ms.author: jamesmantu
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 0bf08c45e82dc6f36d4e179e95e1b58e655b14db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224376"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-a-react-spa-using-the-auth-code-flow"></a>Hızlı başlangıç: kimlik doğrulama kod akışını kullanarak oturum açma ve yanıt olarak bir SPA 'da erişim belirteci edinme

Bu hızlı başlangıçta, JavaScript 'e tek sayfalı uygulamanın (SPA) nasıl oturum açıp yetkilendirme kodu akışını kullanarak Microsoft Graph çağırabildiğini gösteren bir kod örneği indirip çalıştırırsınız. Kod örneği, Microsoft Graph API 'sini veya herhangi bir Web API 'sini çağırmak için bir erişim belirtecinin nasıl alınacağını gösterir. 

Örneğin bir çizim için [nasıl çalıştığını](#how-the-sample-works) görün.

Bu hızlı başlangıçta yetkilendirme kodu akışıyla MSAL yanıt verme kullanılır. Örtülü akışla MSAL.js kullanan benzer bir hızlı başlangıç için bkz. [hızlı başlangıç: JavaScript tek sayfalı uygulamalarda oturum açma kullanıcıları](./quickstart-v2-javascript.md).

> [!IMPORTANT]
> MSAL tepki verme [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz bir Azure aboneliği oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) veya başka bir kod Düzenleyicisi

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Hızlı başlangıç uygulamanızı kaydedin ve indirin
> Hızlı başlangıç uygulamanızı başlatmak için aşağıdaki seçeneklerden birini kullanın.
>
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Seçenek 1 (Express): uygulamanızı kaydedin ve otomatik olarak yapılandırın ve ardından kod örneğinizi indirin
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure portal uygulama kayıtları</a> hızlı başlangıç deneyimine gidin.
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
> 1. **Uygulamayı kaydet** sayfası görüntülendiğinde, uygulamanız için ad girin.
> 1. **Desteklenen hesap türleri** altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
> 1. **Kaydet**’i seçin. Uygulamaya **genel bakış** sayfasında, daha sonra kullanılmak üzere **uygulama (istemci) kimliği** değerini aklınızda edin.
> 1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
> 1. **Platform yapılandırması** altında **Platform Ekle**' yi seçin. Açılan bölmedeki **tek sayfalı uygulama** Seç ' i seçin.
> 1. **Yeniden yönlendirme URI** değerlerini olarak ayarlayın `http://localhost:3000/` . Bu, NodeJS ' nin yerel makinenizde dinleyeceği varsayılan bağlantı noktasıdır. Kullanıcının kimliğini başarıyla doğruladıktan sonra bu URI 'ye kimlik doğrulama yanıtı döndürüyoruz. 
> 1. Değişiklikleri uygulamak için **Yapılandır** ' ı seçin.
> 1. **Platform yapılandırması** ' nın altında **tek sayfalı uygulama**' yı genişletin.
> 1.  ![ Yeniden yönlendirme URI 'nizi zaten yapılandırılmış olan sağlama türleri altında ](media/quickstart-v2-javascript/green-check.png) , yetkilendirme kodu akışı için PKI CE 'nin uygun olduğunu doğrulayın.

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
> Node.js kullanarak projeyi bir Web sunucusuyla çalıştırmak için, [temel proje dosyalarını indirin](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Node.js kullanarak projeyi bir Web sunucusu ile çalıştırma

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Kod örneğini indirin](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3. Adım: JavaScript uygulamanızı yapılandırma
>
> *Src* klasöründe *authConfig.js* dosyasını açın ve `clientID` `authority` nesnesindeki, ve değerlerini güncelleştirin `redirectUri` `msalConfig` .
>
> ```javascript
> /**
> * Configuration object to be passed to MSAL instance on creation. 
> * For a full list of MSAL.js configuration parameters, visit:
> * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md 
> */
> export const msalConfig = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>        redirectUri: "Enter_the_Redirect_Uri_Here"
>    },
>    cache: {
>        cacheLocation: "sessionStorage", // This configures where your cache will be stored
>        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    },
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
> Aynı dosyayı aşağı kaydırın ve güncelleştirin `graphMeEndpoint` . 
> - Dizeyi ile değiştirin `Enter_the_Graph_Endpoint_Herev1.0/me``https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` , API çağrılarının üzerinde hale getirilme bitiş noktasıdır. Ana (genel) Microsoft Graph API hizmeti için `https://graph.microsoft.com/` (sondaki eğik çizgiyi dahil et) girin. Daha fazla bilgi için [belgelere](/graph/deployments) bakın.
>
>
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>    export const graphConfig = {
>        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me"
>    };
> ```
>
>
#### <a name="step-4-run-the-project"></a>4. Adım: projeyi çalıştırma

Node.js kullanarak projeyi bir Web sunucusuyla çalıştırın:

1. Sunucuyu başlatmak için, proje dizini içinden aşağıdaki komutları çalıştırın:
    ```console
    npm install
    npm start
    ```
1. `http://localhost:3000/` adresine gidin.

1. Oturum açma işlemini başlatmak için **oturum aç** ' ı seçin ve ardından Microsoft Graph API 'sini çağırın.

    İlk kez oturum açtığınızda, uygulamanın profilinize erişmesine izin vermek için onayınızı sağlamanız ve oturumunuzu açmanız istenir. Başarıyla oturum açtıktan sonra, sayfada profil bilgilerinizi göstermek için **Istek profili bilgilerine** tıklayın.

## <a name="more-information"></a>Daha fazla bilgi

### <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı

![Tek sayfalı bir uygulama için yetkilendirme kodu akışını gösteren diyagram.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

MSAL.js kitaplığı, kullanıcıları imzalar ve Microsoft Identity platformu tarafından korunan bir API 'ye erişmek için kullanılan belirteçleri ister.

Node.js yüklüyse, Node.js paket yöneticisini (NPM) kullanarak en son sürümü indirebilirsiniz:

```console
npm install @azure/msal-browser @azure/msal-react
```

## <a name="next-steps"></a>Sonraki adımlar

Vanilla JavaScript kullanarak kimlik doğrulama kod akışı uygulaması oluşturmaya yönelik ayrıntılı adım adım kılavuz için aşağıdaki öğreticiye bakın:

> [!div class="nextstepaction"]
> [Oturum açma ve MS Graf çağırma öğreticisi](./tutorial-v2-javascript-auth-code.md)
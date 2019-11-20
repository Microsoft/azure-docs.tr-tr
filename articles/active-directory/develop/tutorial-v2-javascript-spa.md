---
title: Azure AD v 2.0 JavaScript tek sayfalı uygulama (SPA) destekli kurulum
titleSuffix: Microsoft identity platform
description: JavaScript SPA uygulamaları, Azure Active Directory v 2.0 uç noktası tarafından erişim belirteçleri gerektiren bir API 'YI nasıl çağırabilir
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc03e6f1610fe6cef9ce72c981f6f800da8a9951
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802522"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Kullanıcı oturum açma ve JavaScript tek sayfalı uygulamadan (SPA) Microsoft Graph API 'sini çağırma

Bu kılavuzda bir JavaScript tek sayfalı uygulamanın (SPA) nasıl kullanılabileceğini gösterilmektedir:
- Kişisel hesapların yanı sıra iş ve okul hesaplarında oturum açın 
- Erişim belirteci alma
- *Microsoft Identity platform uç noktasından* erişim belirteçleri GEREKTIREN Microsoft Graph API 'sini veya diğer API 'leri çağırma

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Bu kılavuz tarafından oluşturulan örnek uygulamanın nasıl çalıştığı

![Bu öğretici tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Daha fazla bilgi

Bu kılavuz tarafından oluşturulan örnek uygulama, JavaScript SPA 'nın Microsoft Graph API 'sini veya Microsoft Identity platform uç noktasından belirteçleri kabul eden bir Web API 'sini sorgulamasına olanak sağlar. Bu senaryoda, bir Kullanıcı oturum açtıktan sonra, yetkilendirme üst bilgisi aracılığıyla bir erişim belirteci istenir ve HTTP isteklerine eklenir. Belirteç alma ve yenileme işlemleri Microsoft kimlik doğrulama kitaplığı (MSAL) tarafından gerçekleştirilir.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Kitaplıklar

Bu kılavuz aşağıdaki kitaplığı kullanır:

|Kitaplık|Açıklama|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript önizlemesi için Microsoft kimlik doğrulama kitaplığı|

> [!NOTE]
> *Msal. js* , kişisel hesapların ve okulun ve iş hesaplarının oturum açmasını ve belirteçleri almasını sağlayan Microsoft Identity platform uç noktasını hedefler. Microsoft Identity platform uç noktasında [bazı sınırlamalar](azure-ad-endpoint-comparison.md#limitations)vardır.
> V 1.0 ve v 2.0 uç noktaları arasındaki farkları anlamak için [uç nokta karşılaştırma kılavuzuna](azure-ad-endpoint-comparison.md)bakın.

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Web sunucunuzu veya projenizi ayarlama

> Bunun yerine bu örneğin projesini indirmeyi tercih et mi? Aşağıdakilerden birini yapın:
> 
> - Projeyi Node. js gibi yerel bir Web sunucusu kullanarak çalıştırmak için [Proje dosyalarını indirin](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - Seçim Projeyi Microsoft Internet Information Services (IIS) sunucusunu kullanarak çalıştırmak için, [Visual Studio projesini indirin](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Yürütmeden önce kod örneğini yapılandırmak için [yapılandırma adımına](#register-your-application)atlayın.

## <a name="prerequisites"></a>Önkoşullar

* Bu öğreticiyi çalıştırmak için [Node. js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core)veya [Visual Studio 2017](https://www.visualstudio.com/downloads/)ile IIS Express tümleştirme gibi yerel bir Web sunucusuna ihtiyacınız vardır.

* Projeyi çalıştırmak için Node. js kullanıyorsanız, proje dosyalarını düzenlemek için [Visual Studio Code](https://code.visualstudio.com/download)gibi bir tümleşik geliştirme ORTAMı (IDE) yükleyebilirsiniz.

* Bu kılavuzdaki yönergeler hem Node. js hem de Visual Studio 2017 tabanlıdır, ancak başka bir geliştirme ortamı veya Web sunucusu kullanabilirsiniz.

## <a name="create-your-project"></a>Projenizi oluşturma

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Seçenek 1: node. js veya diğer Web sunucuları
> [Node. js](https://nodejs.org/en/download/) ' nin yüklü olduğundan emin olun ve ardından uygulamanızı barındırmak için bir klasör oluşturun.
>
> ### <a name="option-2-visual-studio"></a>Seçenek 2: Visual Studio
> Visual Studio kullanıyorsanız ve yeni bir proje oluşturuyorsanız, şu adımları izleyin:
> 1. Visual Studio 'da **dosya**  > **Yeni**  > **Proje**' yi seçin.
> 1. **Visual C#\Web** bölümünde **ASP.NET Web Uygulaması (.NET Framework)** girişini seçin.
> 1. Uygulamanız için bir ad girin ve ardından **Tamam**' ı seçin.
> 1. **Yeni ASP.NET Web uygulaması**altında **boş**' ı seçin.

## <a name="create-the-spa-ui"></a>SPA Kullanıcı arabirimini oluşturma
1. JavaScript SPA 'niz için bir *index. html* dosyası oluşturun. Visual Studio kullanıyorsanız, projeyi (proje kök klasörü) seçin. Sağ tıklayın ve > **Yeni öğe** > **HTML sayfası** **Ekle** ' yi seçin ve dosya *index. html*olarak adlandırın.

1. *İndex. html* dosyasında aşağıdaki kodu ekleyin:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Önceki betikteki MSAL. js sürümünü [msal. js yayınları](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)altındaki en son yayınlanan sürümle değiştirebilirsiniz.

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Kullanıcı oturumu açmak için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanın

Aşağıdaki kodu `<script></script>` etiketleri içinde `index.html` dosyanıza ekleyin:

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>Daha fazla bilgi

Kullanıcı **oturum aç** düğmesini ilk kez seçtikten sonra, `signIn` yöntemi kullanıcının oturum açması için `loginPopup` çağırır. Bu yöntem, kullanıcının kimlik bilgilerini sormak ve doğrulamak için *Microsoft Identity platform uç noktasıyla* bir açılır pencere açar. Başarılı bir oturum açma işleminden sonra, Kullanıcı özgün *index. html* sayfasına yeniden yönlendirilir. Bir belirteç alınır, `msal.js`tarafından işlenir ve belirteçte bulunan bilgiler önbelleğe alınır. Bu belirteç, *kimlik belirteci* olarak bilinir ve Kullanıcı ile ilgili Kullanıcı görünen adı gibi temel bilgileri içerir. Herhangi bir amaçla bu belirteç tarafından verilen herhangi bir veriyi kullanmayı planlıyorsanız, belirtecin uygulamanız için geçerli bir kullanıcıya verildiğini garantilemek için bu belirtecin arka uç sunucunuz tarafından doğrulanması gerekir.

Bu kılavuz tarafından oluşturulan SPA, Kullanıcı profili bilgileri için Microsoft Graph API 'sini sorgulamak için kullanılan bir *erişim belirteci* almak üzere `acquireTokenSilent` ve/veya `acquireTokenPopup` çağırır. KIMLIK belirtecini doğrulayan bir örneğe ihtiyacınız varsa GitHub 'da [Bu](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-JavaScript-singlepageapp-DotNet-WebApi-v2 örneği") örnek uygulamaya göz atın. Örnek, belirteç doğrulaması için bir ASP.NET Web API 'SI kullanır.

#### <a name="getting-a-user-token-interactively"></a>Kullanıcı belirtecini etkileşimli olarak alma

İlk oturum açma işleminden sonra, kullanıcılardan bir kaynağa erişmek için bir belirteç isteme ihtiyacı olan her seferinde yeniden kimlik doğrulaması yapmasını istemeniz gerekmez. Bu nedenle, en çok belirteçleri almak için *Acquiretokensilent* kullanılması gerekir. Ancak, kullanıcıların Microsoft Identity platform uç noktasıyla etkileşime geçmesini zorunlu hale getirmeniz gereken durumlar vardır. Örneğin:

- Parolanın süresi sona erdiği için kullanıcıların kimlik bilgilerini yeniden girmesi gerekir.
- Uygulamanız bir kaynağa erişim istiyor ve kullanıcının izni gerekiyor.
- İki öğeli kimlik doğrulaması gereklidir.

*Acquiretokenpopup* çağrısı, bir açılır pencere açar (veya *Acquiretokenredirect* kullanıcıları Microsoft Identity platform uç noktasına yönlendirir). Bu pencerede, kullanıcıların kimlik bilgilerini onaylayarak, gerekli kaynağa onay vererek veya iki öğeli kimlik doğrulamasını tamamlayarak etkileşimde olmaları gerekir.

#### <a name="getting-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

`acquireTokenSilent` yöntemi herhangi bir kullanıcı etkileşimi olmadan belirteç alımı ve yenilemeyi işler. `loginPopup` (veya `loginRedirect`) ilk kez yürütüldükten sonra, `acquireTokenSilent`, sonraki çağrılar için korunan kaynaklara erişmek üzere kullanılan belirteçleri elde etmek için yaygın olarak kullanılan yöntemdir. (Belirteçleri istek veya yenileme çağrısı sessizce yapılır.) `acquireTokenSilent` bazı durumlarda başarısız olabilir. Örneğin, kullanıcının parolasının kullanım süreniz olabilir. Uygulamanız bu özel durumu iki şekilde işleyebilir:

1. Bir Kullanıcı oturum açma istemi tetikleyen hemen `acquireTokenPopup` çağrısı yapın. Bu model yaygın olarak, uygulamada kullanıcının kullanabileceği kimliği doğrulanmamış içerik olmayan çevrimiçi uygulamalarda kullanılır. Bu Kılavuzlu kurulum tarafından oluşturulan örnek bu düzeni kullanır.

2. Uygulamalar, kullanıcıya etkileşimli bir oturum açma işlemi gerektiğini de görsel bir şekilde belirtebilir, böylece Kullanıcı oturum açmak için doğru zamanı seçebilir veya uygulama daha sonra `acquireTokenSilent` yeniden deneyebilir. Bu, genellikle kullanıcı uygulamanın kesintiye uğramadan diğer işlevlerini kullanabilmesi durumunda kullanılır. Örneğin, uygulamada kimliği doğrulanmamış içerik bulunabilir. Bu durumda, Kullanıcı, korunan kaynağa erişmek veya güncel olmayan bilgileri yenilemek için ne zaman oturum açmak istediğinize karar verebilir.

> [!NOTE]
> Bu hızlı başlangıç, Internet Explorer kullanılan tarayıcı olduğunda `loginRedirect` ve `acquireTokenRedirect` yöntemlerini kullanır. Internet Explorer 'ın açılır pencereleri işleme yöntemiyle ilgili [bilinen bir sorun](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) nedeniyle bu uygulamayı izliyoruz.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Yeni aldığınız belirteci kullanarak Microsoft Graph API 'sini çağırın

Aşağıdaki kodu `<script></script>` etiketleri içinde `index.html` dosyanıza ekleyin:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Korunan bir API 'ye karşı REST çağrısı yapma hakkında daha fazla bilgi

Bu kılavuz tarafından oluşturulan örnek uygulamada `callMSGraph()` yöntemi, bir belirteç gerektiren korumalı bir kaynağa karşı HTTP `GET` isteği oluşturmak için kullanılır. İstek daha sonra içeriği çağırana döndürür. Bu yöntem, alınan belirteci *http yetkilendirme üst bilgisine*ekler. Bu kılavuz tarafından oluşturulan örnek uygulama için, kaynak, kullanıcının profil bilgilerini görüntüleyen Microsoft Graph API *Me* uç noktasıdır.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Kullanıcının oturumunu kapatmak için bir yöntem ekleyin

Aşağıdaki kodu `<script></script>` etiketleri içinde `index.html` dosyanıza ekleyin:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Uygulamanızı kaydetme

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.

1. Hesabınız birden fazla kiracıya erişim veriyorsa, sağ üst köşedeki hesabı seçin ve ardından Portal oturumunuzu kullanmak istediğiniz Azure AD kiracısına ayarlayın.
1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin.
1. **Bir uygulamayı kaydet** sayfası göründüğünde, uygulamanız için bir ad girin.
1. **Desteklenen hesap türleri**altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** bölümünde, açılan listeden **Web** platformunu seçin ve ardından değeri Web sunucunuza bağlı olan uygulama URL 'si olarak ayarlayın.

   Node. js ve Visual Studio için yeniden yönlendirme URL 'sini ayarlama ve alma hakkında daha fazla bilgi için, aşağıdaki "Node. js için yeniden yönlendirme URL 'SI ayarlama" bölümüne bakın ve [Visual Studio için bir yeniden yönlendirme URL 'Si ayarlayın](#set-a-redirect-url-for-visual-studio).

1. **Kaydol**’u seçin.
1. Uygulamaya **genel bakış** sayfasında, daha sonra kullanılmak üzere **uygulama (istemci) kimliği** değerini aklınızda edin.
1. Bu hızlı başlangıç, [örtük izin akışının](v2-oauth2-implicit-grant-flow.md) etkinleştirilmesini gerektirir. Kayıtlı uygulamanın sol bölmesinde **kimlik doğrulaması**' nı seçin.
1. **Gelişmiş ayarlar**' da, **örtük izin**' ın altında, **Kimlik belirteçleri** ve **erişim belirteçleri** onay kutularını seçin. KIMLIK belirteçleri ve erişim belirteçleri gereklidir çünkü bu uygulamanın kullanıcıları oturum açması ve bir API çağırması gerekir.
1. **Kaydet**’i seçin.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Node. js için yeniden yönlendirme URL 'SI ayarlama
> Node. js için, *Server. js* dosyasında Web sunucusu bağlantı noktasını ayarlayabilirsiniz. Bu öğretici 30662 numaralı bağlantı noktasını kullanır, ancak kullanılabilir başka herhangi bir bağlantı noktasını kullanabilirsiniz.
>
> Uygulama kayıt bilgilerinde bir yeniden yönlendirme URL 'SI ayarlamak için, **uygulama kayıt** bölmesine dönün ve aşağıdakilerden birini yapın:
>
> - **Yeniden yönlendirme URL 'si**olarak *`http://localhost:30662/`* ayarlayın.
> - Özel bir TCP bağlantı noktası kullanıyorsanız, *`http://localhost:<port>/`* ( *\<bağlantı noktası >* özel TCP bağlantı noktası numarasıdır) kullanın.
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Visual Studio için yeniden yönlendirme URL 'SI ayarlama
> Visual Studio için yeniden yönlendirme URL 'sini almak için aşağıdaki adımları izleyin:
> 1. Çözüm Gezgini, projeyi seçin.
>
>    **Özellikler** penceresi açılır. Değilse, F4 tuşuna basın.
>
>    ![JavaScriptSPA projesi Özellikler penceresi](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. **URL** değerini kopyalayın.
> 1. **Uygulama kayıt** bölmesine dönün ve kopyalanmış değeri **yeniden yönlendirme URL 'si**olarak yapıştırın.

#### <a name="configure-your-javascript-spa"></a>JavaScript SPA 'nizi yapılandırma

1. Proje kurulumu sırasında oluşturduğunuz *index. html* dosyasında, uygulama kayıt bilgilerini ekleyin. Dosyanın üst kısmında, `<script></script>` etiketleri içinde aşağıdaki kodu ekleyin:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Nerede:
    - *\<Enter_the_Application_Id_here >* , kaydettiğiniz uygulamanın **uygulama (istemci) kimliğidir** .
    - *\<Enter_the_Tenant_info_here >* aşağıdaki seçeneklerden birine ayarlanır:
       - Uygulamanız *bu kuruluş dizinindeki hesapları*destekliyorsa, bu DEĞERI **Kiracı kimliği** veya **kiracı adı** (örneğin, *contoso.Microsoft.com*) ile değiştirin.
       - Uygulamanız *herhangi bir kuruluş dizinindeki hesapları*destekliyorsa, bu değeri **kuruluşlar**ile değiştirin.
       - Uygulamanız *herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesapları*destekliyorsa, bu değeri **ortak**ile değiştirin. *Yalnızca kişisel Microsoft hesaplarına*yönelik desteği kısıtlamak için bu değeri **tüketicilerle**değiştirin.

## <a name="test-your-code"></a>Kodunuzu test etme

Aşağıdaki ortamlardan birini kullanarak kodunuzu test edin.

### <a name="test-with-nodejs"></a>Node. js ile test etme

Visual Studio kullanmıyorsanız, Web sunucunuzun başlatıldığından emin olun.

1. Sunucuyu, *index. html* dosyanızın konumunu temel alan bir TCP bağlantı noktasını dinleyecek şekilde yapılandırın. Node. js için, uygulama klasöründen bir komut satırı isteminde aşağıdaki komutları çalıştırarak bağlantı noktasını dinlemek üzere Web sunucusunu başlatın:

    ```bash
    npm install
    node server.js
    ```
1. Tarayıcınızda, **http://\<span > girin\</span > localhost: 30662** veya **http://\<span >\</span > localhost: {Port}** , burada *bağlantı noktası* Web sunucunuzun dinlediği bağlantı noktasıdır. *İndex. html* dosyanızın Içeriğini ve **oturum aç** düğmesini görmeniz gerekir.

### <a name="test-with-visual-studio"></a>Visual Studio ile test

Visual Studio kullanıyorsanız, proje çözümünü seçin ve F5 tuşuna basarak projenizi çalıştırın. Tarayıcı, http://<span></span>localhost: {Port} konumu olarak açılır ve **oturum aç** düğmesi görünür olmalıdır.

## <a name="test-your-application"></a>Uygulamanızı test etme

Tarayıcı *index. html* dosyanızı yükledikten sonra **oturum aç**' ı seçin. Microsoft Identity platform uç noktası ile oturum açmanız istenir:

![JavaScript SPA hesabı oturum açma penceresi](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Uygulama erişimi için onay sağlayın

Uygulamanızda ilk kez oturum açtığınızda profilinize erişim vermeniz ve oturumunuzu açmanız istenir:

!["Izin isteniyor" penceresi](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Uygulama sonuçlarını görüntüle

Oturum açtıktan sonra, Kullanıcı profili bilgileriniz görüntülenen Microsoft Graph API yanıtında döndürülür:

![Microsoft Graph API çağrısının sonuçları](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Kapsamlar ve temsilci izinleri hakkında daha fazla bilgi

Microsoft Graph API 'SI, kullanıcının profilini okumak için *Kullanıcı. Read* kapsamını gerektirir. Varsayılan olarak, bu kapsam kayıt portalı 'nda kayıtlı her uygulamaya otomatik olarak eklenir. Microsoft Graph için diğer API 'Ler ve arka uç sunucunuza yönelik özel API 'Ler için ek kapsamlar gerekebilir. Örneğin, Microsoft Graph API 'SI, kullanıcının takvimlerini listelemek için *takvimlerin. Read* kapsamını gerektirir.

Kullanıcının takvimlerine bir uygulama bağlamında erişmek için, *takvimler.* uygulama kayıt bilgilerine, temsilci olarak oku iznini ekleyin. Ardından, `acquireTokenSilent` çağrısına *takvimlere. Read* kapsamını ekleyin.

>[!NOTE]
>Kapsam sayısını artırdıkça kullanıcıdan ek Yarışması istenebilir.

Bir arka uç API 'SI bir kapsam gerektirmiyorsa (önerilmez), belirteçleri almak için çağrılarındaki kapsam olarak *ClientID* 'yi kullanabilirsiniz.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Microsoft Identity platformunu geliştirmemize yardımcı olun. Kısa bir iki sorulık anketi tamamlayarak düşüncelerinizi bize söyleyin.

> [!div class="nextstepaction"]
> [Microsoft Identity platform Anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

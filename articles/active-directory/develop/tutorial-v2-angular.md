---
title: 'Öğretici: kimlik doğrulaması için Microsoft Identity platformunu kullanan bir angular uygulaması oluşturun | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu öğreticide, kullanıcıların oturum açması için Microsoft Identity platformunu kullanan bir angular tek sayfalı uygulama (SPA) oluşturun ve Microsoft Graph API 'sini adına çağırmak için bir erişim belirteci alın.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 105353598a2af60c407bacf02b4527b2de84e450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756149"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Öğretici: Kullanıcı oturum açma ve angular tek sayfalı uygulamadan Microsoft Graph API 'sini çağırma

Bu öğreticide, kullanıcılar oturumunu açan ve Microsoft Graph API 'sini çağıran bir angular tek sayfalı uygulama (SPA) oluşturacaksınız.

Bu öğreticide:

> [!div class="checklist"]
> * İle angular projesi oluşturma `npm`
> * Uygulamayı Azure portal kaydetme
> * Kullanıcı oturum açma ve oturum kapatma desteği için kod ekleme
> * Microsoft Graph API 'sini çağırmak için kod ekleme
> * Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

* Yerel bir Web sunucusu çalıştırmak için [Node.js](https://nodejs.org/en/download/) .
* Proje dosyalarını değiştirmek için [Visual Studio Code](https://code.visualstudio.com/download) veya başka bir düzenleyici.

## <a name="how-the-sample-app-works"></a>Örnek uygulamanın nasıl çalıştığı

![Bu öğreticide oluşturulan örnek uygulamanın nasıl çalıştığını gösteren diyagram](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

Bu öğreticide oluşturulan örnek uygulama, Microsoft Graph API 'sini veya Microsoft Identity platform tarafından verilen belirteçleri kabul eden bir Web API 'sini sorgulamak için angular SPA 'yı sağlar. Çekirdek MSAL.js kitaplığının bir sarmalayıcısı olan angular için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanır. MSAL angular, Azure Active Directory (Azure AD) kullanarak kurumsal kullanıcıların kimliğini ve ayrıca Facebook, Google ve LinkedIn gibi sosyal kimlik kimliklerini ve kullanıcıları kimlik doğrulamasını sağlar. Kitaplık Ayrıca uygulamaların Microsoft bulut hizmetlerine ve Microsoft Graph erişmesini sağlar.

Bu senaryoda, bir Kullanıcı oturum açtıktan sonra, yetkilendirme üst bilgisi aracılığıyla bir erişim belirteci istenir ve HTTP isteklerine eklenir. Belirteç alma ve yenileme, MSAL tarafından işlenir.

### <a name="libraries"></a>Kitaplıklar

Bu öğretici aşağıdaki kitaplığı kullanır:

|Kitaplık|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript angular sarmalayıcı için Microsoft kimlik doğrulama kitaplığı|

GitHub 'da, MSAL.js kitaplığının kaynak kodunu, [Azuread/Microsoft-Authentication-Library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) deposunda bulabilirsiniz.

## <a name="create-your-project"></a>Projenizi oluşturun

Aşağıdaki NPM komutlarını kullanarak yeni bir angular uygulaması oluşturun:

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Uygulamanızı kaydetme

Azure portal [tek sayfalı bir uygulamayı kaydetmek için yönergeleri](./scenario-spa-app-registration.md) izleyin.

Kaydlarınızın uygulamaya **genel bakış** sayfasında, daha sonra kullanılmak üzere **uygulama (istemci) kimlik** değeri ' ne göz atın.

**Yeniden YÖNLENDIRME URI** değerini kaydedin **http://localhost:4200/** ve örtülü izin ayarlarını etkinleştirin.

## <a name="configure-the-application"></a>Uygulamayı yapılandırma

1. *Src/App* klasöründe *app. Module. TS* ' yi düzenleyin ve `MSALModule` `imports` sabitine ek olarak ekleyin `isIE` :

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Şu değerleri değiştirin:

    |Değer adı|Hakkında|
    |---------|---------|
    |Enter_the_Application_Id_Here|Uygulama kaydlarınızın **genel bakış** sayfasında bu, **uygulamanızın (istemci) kimlik** değeridir. |
    |Enter_the_Cloud_Instance_Id_Here|Bu, Azure bulutunun örneğidir. Ana veya küresel Azure bulutu için girin **https://login.microsoftonline.com** . Ulusal bulutlar (örneğin, Çin) için bkz. [Ulusal bulutlar](./authentication-national-cloud.md).|
    |Enter_the_Tenant_Info_Here| Aşağıdaki seçeneklerden birine ayarlayın: uygulamanız *bu kuruluş dizinindeki hesapları* destekliyorsa, bu değeri dizin (KIRACı) kimliği veya kiracı adı (örneğin, **contoso.Microsoft.com**) ile değiştirin. Uygulamanız *herhangi bir kuruluş dizinindeki hesapları* destekliyorsa, bu değeri **kuruluşlar** ile değiştirin. Uygulamanız *herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesapları* destekliyorsa, bu değeri **ortak** ile değiştirin. *Yalnızca kişisel Microsoft hesaplarına* yönelik desteği kısıtlamak için bu değeri **tüketicilerle** değiştirin. |
    |Enter_the_Redirect_Uri_Here|İle değiştirin **http://localhost:4200** .|

    Kullanılabilir yapılandırılabilir seçenekler hakkında daha fazla bilgi için bkz. [istemci uygulamalarını başlatma](msal-js-initializing-client-applications.md).

2. Aynı dosyanın en üstünde aşağıdaki içeri aktarma ifadesini ekleyin:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Aşağıdaki import deyimlerini en üst öğesine ekleyin `src/app/app.component.ts` :

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Kullanıcı oturumu açma

`AppComponent`Kullanıcı oturumu açmak için aşağıdaki kodu ekleyin:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> `loginRedirect`Internet Explorer kullanıcıları için kullanmanızı öneririz.

## <a name="acquire-a-token"></a>Belirteç alma

### <a name="angular-interceptor"></a>Angular yakalayıcısı

MSAL angular `Interceptor` , bilinen korumalı kaynaklara angular istemcisini kullanan giden istekler için otomatik olarak belirteçler elde eden bir sınıf sağlar `http` .

İlk olarak, `Interceptor` sınıfı uygulamanıza sağlayıcı olarak ekleyin:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Daha sonra, `MsalModule.forRoot()` `protectedResourceMap` ' ye bu kapsamları dahil etme ve dahil korumalı kaynakların haritasını sağlayın `consentScopes` . Koleksiyonda sağladığınız URL 'Ler `protectedResourceMap` büyük/küçük harfe duyarlıdır.

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Son olarak, bir kullanıcının profilini HTTP isteğiyle alın:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent, acquireTokenPopup, acquireTokenRedirect
MSAL belirteçleri elde etmek için üç yöntem kullanır: `acquireTokenRedirect` , `acquireTokenPopup` , ve `acquireTokenSilent` . Ancak, `MsalInterceptor` önceki bölümde gösterildiği gibi, angular uygulamaları için sınıfını kullanmanızı öneririz.

#### <a name="get-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

`acquireTokenSilent`Yöntemi, Kullanıcı etkileşimi olmadan belirteç alma ve yenileme işlemleri gerçekleştirir. Ya da `loginRedirect` `loginPopup` yöntemi ilk kez yürütüldükten sonra, `acquireTokenSilent` daha sonraki çağrılarındaki korunan kaynaklara erişmek için kullanılan belirteçleri almak için genellikle kullanılır. Belirteçleri istek veya yenileme çağrıları sessizce yapılır.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

Bu kodda, `scopes` API için erişim belirtecinde döndürülmek istenen kapsamları içerir.

Örnek:

* `["user.read"]` Microsoft Graph için
* `["<Application ID URL>/scope"]` Özel Web API 'Leri için (yani, `api://<Application ID>/access_as_user` )

#### <a name="get-a-user-token-interactively"></a>Etkileşimli olarak kullanıcı belirteci alma

Bazen kullanıcının Microsoft Identity platformu ile etkileşim kurması gerekir. Örnek:

* Parolasının süresi sona erdiği için kullanıcıların kimlik bilgilerini yeniden girmesi gerekebilir.
* Uygulamanız, kullanıcının onaylaması gereken ek kaynak kapsamlarına erişim istiyor.
* İki öğeli kimlik doğrulaması gereklidir.

Çoğu uygulama için önerilen model ilk olarak çağrı yapmak `acquireTokenSilent` , sonra özel durumu yakalamak ve sonra `acquireTokenPopup` `acquireTokenRedirect` etkileşimli bir istek başlatmak için (veya) çağırır.

`acquireTokenPopup`Sonuçları açılan bir oturum açma penceresinde çağırma. Alternatif olarak, `acquireTokenRedirect` kullanıcıları Microsoft Identity platformuna yeniden yönlendirir. Bu pencerede, kullanıcıların kimlik bilgilerini onaylaması, gerekli kaynağa onay vermesi veya iki öğeli kimlik doğrulamayı tamamlaması gerekir.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> Bu hızlı başlangıç, `loginRedirect` `acquireTokenRedirect` Internet Explorer tarafından açılır pencerelerin işlenmesiyle ilgili [bilinen bir sorun](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) nedeniyle Microsoft Internet Explorer ile ve yöntemlerini kullanır.

## <a name="log-out"></a>Oturumu Kapat

Bir kullanıcının oturumunu kapatmak için aşağıdaki kodu ekleyin:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>UI Ekle
Angular malzeme bileşen kitaplığı 'nı kullanarak Kullanıcı arabirimi ekleme hakkında bir örnek için bkz. [örnek uygulama](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular).

## <a name="test-your-code"></a>Kodunuza test etme

1.  Uygulama klasöründen bir komut satırı isteminde aşağıdaki komutları çalıştırarak bağlantı noktasını dinlemek için Web sunucusunu başlatın:

    ```bash
    npm install
    npm start
    ```
1. Tarayıcınızda, veya yazın; **http://localhost:4200** **http://localhost:{port}** burada *bağlantı noktası* , Web sunucunuzun dinlediği bağlantı noktasıdır.


### <a name="provide-consent-for-application-access"></a>Uygulama erişimi için onay sağlayın

Uygulamanızda oturum açmaya ilk kez başladığınızda, profilinize bu profile erişim vermeniz ve oturum açmasını sağlamanız istenir:

!["Izin isteniyor" penceresi](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Kapsam ve temsilci izinleri ekleme

Microsoft Graph API 'SI, kullanıcının profilini okumak için *Kullanıcı. Read* kapsamını gerektirir. Varsayılan olarak, bu kapsam kayıt portalı 'nda kayıtlı her uygulamaya otomatik olarak eklenir. Microsoft Graph için diğer API 'Ler ve arka uç sunucunuza yönelik özel API 'Ler için ek kapsamlar gerekebilir. Örneğin, Microsoft Graph API 'SI, kullanıcının takvimlerini listelemek için *takvimlerin. Read* kapsamını gerektirir.

Kullanıcının takvimlerine bir uygulama bağlamında erişmek için, *takvimler.* uygulama kayıt bilgilerine, temsilci olarak oku iznini ekleyin. Ardından, *takvimlere. Read* kapsamını `acquireTokenSilent` çağrıya ekleyin.

>[!NOTE]
>Kapsam sayısını artırdıkça kullanıcıdan ek Yarışması istenebilir.

Bir arka uç API 'SI bir kapsam gerektirmiyorsa (önerilmez), belirteçleri almak için çağrılarındaki kapsam olarak *ClientID* 'yi kullanabilirsiniz.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Çok bölgeli makale serimizin içindeki Microsoft Identity platformunda tek sayfalı uygulama (SPA) geliştirmeye daha ayrıntılı bir şekilde Delve yapın.

> [!div class="nextstepaction"]
> [Senaryo: tek sayfalı uygulama](scenario-spa-overview.md)

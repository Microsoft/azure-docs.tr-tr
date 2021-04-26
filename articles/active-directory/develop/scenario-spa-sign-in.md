---
title: Tek sayfalı uygulama oturum açma & oturum kapatma
titleSuffix: Microsoft identity platform
description: Tek sayfalı bir uygulama (oturum açma) oluşturmayı öğrenin
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5892904c3cfc475683d081a58b47e4bec1266ab9
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105966818"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Tek sayfalı uygulama: oturum açma ve oturum kapatma

Tek sayfalı uygulamanız için koda oturum açma eklemeyi öğrenin.

Uygulamanızdaki API 'Lere erişim belirteçleri alabilmeniz için önce kimliği doğrulanmış bir Kullanıcı bağlamına ihtiyacınız vardır. Kullanıcılara iki şekilde MSAL.js, uygulamanızda oturum açabilirsiniz:

* [Açılır pencere](#sign-in-with-a-pop-up-window), `loginPopup` yöntemini kullanarak
* Yöntemini kullanarak [yeniden yönlendirin](#sign-in-with-redirect) `loginRedirect`

İsteğe bağlı olarak, kullanıcının oturum açma sırasında izin vermesini gerektiren API 'lerin kapsamlarını da geçirebilirsiniz.

> [!NOTE]
> Uygulamanızın kimliği doğrulanmış bir Kullanıcı bağlamına veya KIMLIK belirtecine zaten erişimi varsa, oturum açma adımını atlayabilir ve belirteçleri doğrudan elde edebilirsiniz. Ayrıntılar için bkz. [MSAL.js oturum açma olmadan SSO](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Bir açılır pencere veya yeniden yönlendirme deneyimi arasında seçim yapma

Uygulamanızda hem açılır hem de yeniden yönlendirme yöntemlerini kullanamazsınız. Açılır veya yeniden yönlendirme deneyimi arasındaki seçim, uygulama akışınıza bağlıdır:

* Kimlik doğrulama sırasında kullanıcıların ana uygulama sayfasından uzakta geçiş yapmak istemiyorsanız, açılır yöntemi öneririz. Kimlik doğrulama yeniden yönlendirmesi bir açılır pencerede olduğundan, ana uygulamanın durumu korunur.

* Kullanıcıların, açılır pencerelerin devre dışı bırakıldığı tarayıcı kısıtlamaları veya ilkeleri varsa, yeniden yönlendirme yöntemini kullanabilirsiniz. [Internet Explorer 'da açılır pencereler ile ilgili bilinen sorunlar](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)olduğundan, Internet Explorer tarayıcısı ile yeniden yönlendirme yöntemini kullanın.

## <a name="sign-in-with-a-pop-up-window"></a>Açılır pencere ile oturum açma


# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success

        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();
    
        if (currentAccounts === null) {
            // no accounts detected
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
        }
    
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL angular sarmalayıcısı, yol tanımına ekleyerek uygulamanızdaki belirli yolları güvenli hale getirmeye olanak tanır `MsalGuard` . Bu koruma, bu rotaya erişildiğinde oturum açma yöntemini çağırır.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Açılır pencere deneyimi için `popUp` yapılandırma seçeneğini etkinleştirin. Ayrıca, onay gerektiren kapsamları aşağıdaki gibi geçirebilirsiniz:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Yeniden yönlendirme ile oturum açma

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    //handle redirect response

    // In case multiple accounts exist, you can select
    const currentAccounts = myMsal.getAllAccounts();

    if (currentAccounts === null) {
        // no accounts detected
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
    }
}

myMsal.handleRedirectPromise().then(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

Yeniden yönlendirme yöntemleri, ana uygulamadan uzağa geçiş nedeniyle Promise döndürmez. Döndürülen belirteçleri işlemek ve erişmek için, yeniden yönlendirme yöntemlerini çağırmadan önce başarı ve hata geri çağırmaları kaydedin.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

Buradaki kod, açılır pencere ile oturum açma hakkında bölümünde daha önce açıklananla aynıdır. Varsayılan akış yeniden yönlendirme 'dir.

---

## <a name="sign-out"></a>Oturumu kapatma

MSAL kitaplığı, `logout` tarayıcı depolamada önbelleği temizleyen ve Azure Active Directory (Azure AD) için bir oturum kapatma isteği gönderen bir yöntem sağlar. Kaydolduktan sonra, kitaplık varsayılan olarak uygulama başlatma sayfasına yeniden yönlendirilir.

' İ ayarlayarak, oturum kapatıldıktan sonra yeniden yönlendirileceği URI 'yi yapılandırabilirsiniz `postLogoutRedirectUri` . Bu URI Ayrıca uygulama kaydlarınızın oturum kapatma URI 'SI olarak kaydedilmelidir.

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByUsername(username)
}

myMsal.logout(logoutRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryoda, [uygulama için bir belirteç edinerek bir](scenario-spa-acquire-token.md)sonraki makaleye geçin.
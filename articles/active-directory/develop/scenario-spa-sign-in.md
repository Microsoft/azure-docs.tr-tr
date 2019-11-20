---
title: Tek sayfalı uygulama (oturum açma)-Microsoft Identity platform
description: Tek sayfalı uygulama oluşturmayı öğrenin (oturum açın)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bf614a5523e78fc72918db973ef8d738a171fff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031790"
---
# <a name="single-page-application---sign-in"></a>Tek sayfalı uygulama-oturum açma

Tek sayfalı uygulamanız için koda oturum açma eklemeyi öğrenin.

Uygulamanızdaki API 'Lere erişim belirteçleri alabilmeniz için önce kimliği doğrulanmış bir Kullanıcı bağlamına ihtiyacınız olacaktır. MSAL. js ' de uygulamanızdaki kullanıcılara iki şekilde oturum açabilirsiniz:

* Yöntemi kullanarak `loginPopup` [bir açılır pencere ile oturum açın](#sign-in-with-a-pop-up-window)
* Yöntemi kullanarak `loginRedirect` [yeniden yönlendirme ile oturum açın](#sign-in-with-redirect)

Ayrıca isteğe bağlı olarak, kullanıcının oturum açma sırasında izin vermesini gerektiren API 'lerin kapsamlarını geçirebilirsiniz.

> [!NOTE]
> Uygulamanızın kimliği doğrulanmış bir Kullanıcı bağlamına veya kimlik belirtecine zaten erişimi varsa, oturum açma adımını atlayabilir ve belirteçleri doğrudan elde edebilirsiniz. Daha ayrıntılı bilgi için bkz. [msal. js oturum açma olmadan SSO](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Bir açılır pencere veya yeniden yönlendirme deneyimi arasında seçim yapma

Uygulamanızda hem açılır hem de yeniden yönlendirme yöntemlerinin bir birleşimini kullanamazsınız. Açılır veya yeniden yönlendirme deneyimi arasındaki seçim, uygulama akışınıza bağlıdır.

* Kimlik doğrulaması sırasında kullanıcının ana uygulama sayfanızda uzaklaşmak istemiyorsanız, açılır yöntemlerin kullanılması önerilir. Kimlik doğrulama yeniden yönlendirmesi bir açılır pencerede olduğundan, ana uygulamanın durumu korunur.

* Yeniden yönlendirme yöntemlerini kullanmanız gerekebilecek bazı durumlar vardır. Uygulamanızın kullanıcılarının açılır pencereler devre dışı bırakılmış tarayıcı kısıtlamaları veya ilkeleri varsa, yeniden yönlendirme yöntemlerini kullanabilirsiniz. Açılır pencereleri gerçekleştirirken [Internet Explorer ile ilgili bazı bilinen sorunlar](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) olduğundan, Internet Explorer tarayıcısı ile yeniden yönlendirme yöntemlerini kullanın.

## <a name="sign-in-with-a-pop-up-window"></a>Açılır pencere ile oturum açın

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

MSAL angular sarmalayıcı, yalnızca ' i yol tanımına ekleyerek `MsalGuard` uygulamanızdaki belirli yolları güvenli hale getirmeye olanak tanır. Bu koruma, bu rotaya erişildiğinde oturum açma yöntemini çağırır.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Açılır pencere deneyimi için `popUp` yapılandırma seçeneğini etkinleştirin. Ayrıca, onay gerektiren kapsamları aşağıdaki gibi geçirebilirsiniz:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Yeniden yönlendirme ile oturum açın

### <a name="javascript"></a>JavaScript

Yeniden yönlendirme yöntemleri, ana uygulamadan gezinme nedeniyle bir Promise döndürmez. Döndürülen belirteçleri işlemek ve erişmek için, yeniden yönlendirme yöntemlerini çağırmadan önce başarı ve hata geri çağırmaları kaydetmeniz gerekir.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Buradaki kod, yukarıdaki bir açılır pencere ile oturum açma bölümünde açıklananla aynıdır. Varsayılan akış yeniden yönlendirme 'dir.

> [!NOTE]
> KIMLIK belirteci, onaylı kapsamları içermez ve yalnızca kimliği doğrulanmış kullanıcıyı temsil eder. Verilen kapsamlar, bir sonraki adımda elde ettiğiniz erişim belirtecinde döndürülür.

## <a name="sign-out"></a>Oturumu kapat

MSAL kitaplığı, tarayıcı depolamada `logout` önbelleği temizleyecek bir yöntem sağlar ve Azure AD 'ye bir oturum kapatma isteği gönderir. Oturumu kapattıktan sonra varsayılan olarak uygulama başlatma sayfasına yeniden yönlendirilir.

' İ ayarlayarak `postLogoutRedirectUri`oturumu kapattıktan sonra yeniden yönlendirileceği URI 'yi yapılandırabilirsiniz. Bu URI Ayrıca uygulama kaydlarınızın oturum kapatma URI 'SI olarak kaydedilmelidir.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama için bir belirteç alınıyor](scenario-spa-acquire-token.md)

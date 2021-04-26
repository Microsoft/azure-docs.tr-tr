---
title: Azure AD B2C ile MSAL.js kullanma
titleSuffix: Microsoft identity platform
description: JavaScript (MSAL.js) için Microsoft kimlik doğrulama kitaplığı, uygulamaların, güvenli Web API 'Lerini çağırmak için Azure AD B2C birlikte çalışmasını ve belirteçleri almasını sağlar. Bu Web API 'Leri Microsoft Graph, diğer Microsoft API 'Leri, başkalarından Web API 'Leri veya kendi Web API 'niz olabilir.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 5168dfb531c40a3efeef978abf65473ee59d2921
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255605"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Azure AD B2C çalışmak için JavaScript için Microsoft kimlik doğrulama kitaplığı 'nı kullanın

[JavaScript (MSAL.js) Için Microsoft kimlik doğrulama kitaplığı](https://github.com/AzureAD/microsoft-authentication-library-for-js) , javascript geliştiricilerinin [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C) kullanarak sosyal ve yerel kimliklerle kullanıcıların kimliğini doğrulamasını sağlar.

Kimlik yönetimi hizmeti olarak Azure AD B2C kullanarak müşterilerinizin uygulamalarınızı kullandıklarında, oturum açma ve profillerini yönetme şeklini özelleştirebilir ve kontrol edebilirsiniz. Azure AD B2C Ayrıca, kimlik doğrulama işlemi sırasında uygulamanızın görüntüleyeceği Kullanıcı arabirimini markalamanıza ve özelleştirmenize olanak sağlar.

Aşağıdaki bölümlerde aşağıdakilerin nasıl yapılacağı gösterilmektedir:

- Node.js Web API 'sini koruma
- Tek sayfalı bir uygulamada (SPA) oturum açma desteği *ve korunan Web* API 'sini çağırma
- Parola sıfırlama desteğini etkinleştir

## <a name="prerequisites"></a>Önkoşullar

Henüz yapmadıysanız, bir [Azure AD B2C kiracı](../../active-directory-b2c/tutorial-create-tenant.md)oluşturun.

## <a name="nodejs-web-api"></a>Node.js web API’si

Aşağıdaki adımlarda, bir **Web API 'sinin** kendisini korumak ve seçilen kapsamları bir istemci uygulamasına göstermek için Azure AD B2C nasıl kullanabileceği gösterilmektedir.

Düğüm için MSAL.js Şu anda geliştirme aşamasındadır. Daha fazla bilgi için GitHub 'daki [yol haritasını](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) inceleyin. Şu anda Microsoft tarafından geliştirilen ve desteklenen Node.js için bir kimlik doğrulama kitaplığı olan [Passport-Azure-AD](https://github.com/AzureAD/passport-azure-ad)' ı kullanmanızı öneririz.

### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme

Web API 'nizi Azure AD B2C korumak için önce kaydetmeniz gerekir. Bkz. ayrıntılı adımlar için [Uygulamanızı kaydetme](../../active-directory-b2c/add-web-api-application.md) .

### <a name="step-2-download-the-sample-application"></a>2. Adım: örnek uygulamayı Indirme

Örneği bir zip dosyası olarak indirin veya GitHub 'dan kopyalayın:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>3. Adım: kimlik doğrulamasını yapılandırma

1. Örnekteki dosyadaki *config.js* açın.

2. Uygulamanızı kaydederken daha önce edindiğiniz uygulama kimlik bilgileriyle örneği yapılandırın. Değerleri kiracı adınız, istemci KIMLIĞINIZ ve ilke adınızla değiştirerek aşağıdaki kod satırlarını değiştirin.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

Daha fazla bilgi için bu [Node.js B2C Web API örneğine](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)göz atın.

## <a name="javascript-spa"></a>JavaScript SPA

Aşağıdaki adımlarda, **tek sayfalı bir uygulamanın** , oturum açmak, oturum açmak ve korumalı BIR Web API 'si çağırmak için Azure AD B2C nasıl kullanabileceği gösterilmektedir.

### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme

Kimlik doğrulaması uygulamak için öncelikle uygulamanızı kaydetmeniz gerekir. Bkz. ayrıntılı adımlar için [Uygulamanızı kaydetme](../../active-directory-b2c/tutorial-register-applications.md) .

### <a name="step-2-download-the-sample-application"></a>2. Adım: örnek uygulamayı Indirme

Kod örneğini indirin [. ZIP arşivi](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) veya GitHub deposunu kopyalayın:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3. Adım: kimlik doğrulamasını yapılandırma

Uygulamanızı yapılandırmanın iki ilgisi vardır:

- API uç noktası ve sunulan Kapsamları yapılandırma
- Kimlik doğrulama parametrelerini ve belirteç kapsamlarını yapılandırma

1. Örnekteki *apiConfig.js* dosyasını açın.

2. Örneği, Web API 'nizi kaydederken daha önce edindiğiniz parametrelerle yapılandırın. Aşağıdaki kod satırlarını, değerlerini Web API 'nizin adresiyle ve sunulan kapsamlarla değiştirerek değiştirin.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Örnekteki *authConfig.js* dosyasını açın.

1. Örneği, daha önce tek sayfalı Uygulamanızı kaydederken elde ettiğiniz parametrelerle yapılandırın. Değerleri ClientID, yetkili meta verileri ve belirteç istek kapsamlarınız ile değiştirerek aşağıdaki kod satırlarını değiştirin.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Daha fazla bilgi için bu [JAVASCRIPT B2C tek sayfalı uygulama örneğine](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)göz atın.

## <a name="support-password-reset"></a>Parola sıfırlamayı destekle

Bu bölümde, tek sayfalı uygulamanızı, Azure AD B2C parola sıfırlama kullanıcı akışını kullanacak şekilde genişletmenizi sağlar. MSAL.js Şu anda birden çok kullanıcı akışını veya özel ilkeleri yerel olarak desteklemez, ancak parola sıfırlama gibi yaygın kullanım durumlarını işlemek için kitaplığı kullanabilirsiniz.

Aşağıdaki adımlarda önceki [JAVASCRIPT Spa](#javascript-spa) bölümündeki adımları zaten takip ettiğiniz varsayılır.

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>1. Adım: parola sıfırlama Kullanıcı akışı için yetkili dizesini tanımlama

1. İlk olarak, yetkili URI 'larınızı depoladığınız bir nesne oluşturun:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Sonra, MSAL nesnesini `signInSignUp` ilkeyle varsayılan olarak başlatın (Yukarıdaki kod parçacığına bakın). Bir Kullanıcı oturum açmaya çalıştığında, bunlar aşağıdaki ekranda sunulur:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Azure AD B2C tarafından görünen oturum açma ekranı":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>2. Adım: oturum açma yönteminizin kimlik doğrulama hatalarını yakalama ve işleme

Kullanıcı **parolayı unutma** seçeneğini belirlediğinde, uygulamanız kodunuzda yakalamalı bir hata oluşturur ve ardından uygun kullanıcı akışını sunarak idare edersiniz. Bu durumda, `b2c_1_reset` parola sıfırlama akışı.

1. Oturum açma yönteminizi aşağıdaki şekilde genişletin:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. Yukarıdaki kod parçacığı, kodla hatayı gönderdikten sonra parola sıfırlama ekranının nasıl gösterileceğini gösterir `AADB2C90118` .

    Parolasını sıfırladıktan sonra, Kullanıcı yeniden oturum açmak için uygulamaya geri döndürülür.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Azure AD B2C tarafından gösterilen parola sıfırlama akışı ekranları" border="false":::

    Hata kodları ve özel durumları işleme hakkında daha fazla bilgi için bkz. [msal hata ve özel durum kodları](msal-error-handling-js.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu Azure AD B2C kavramları hakkında daha fazla bilgi edinin:

- [Kullanıcı akışları](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow)
- [Özel ilkeler](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-custom-policy)
- [UX özelleştirme](../../active-directory-b2c/configure-user-input.md)

---
title: Azure AD B2C (Android için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: Android için Microsoft kimlik doğrulama kitaplığı (MSAL) ile Azure AD B2C kullanırken belirli hususlar hakkında bilgi edinin. Android
services: active-directory
documentationcenter: dev-center-name
author: brianmel
manager: omkrishn
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5061f1ab341e5872dfa82c9f5c5b133ae40bdf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803245"
---
# <a name="use-msal-for-android-with-b2c"></a>B2C ile Android için MSAL kullanma

Microsoft kimlik doğrulama kitaplığı (MSAL), uygulama geliştiricilerinin [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)kullanarak sosyal ve yerel kimliklere sahip kullanıcıların kimliğini doğrulamasını sağlar. Azure AD B2C bir kimlik yönetimi hizmetidir. Müşterilerinizin uygulamalarınızı kullandıklarında, nasıl oturum açıp yönetebileceğini özelleştirmek ve denetlemek için bu uygulamayı kullanın.

## <a name="configure-known-authorities-and-redirect-uri"></a>Bilinen yetkilileri ve yeniden yönlendirme URI 'sini Yapılandır

Android için MSAL ' de, B2C ilkeleri (Kullanıcı, neys) bireysel yetkililer olarak yapılandırılır.

İki ilkeye sahip olan B2C uygulaması verildi:
- Kaydolma/oturum açma
    * `B2C_1_SISOPolicy` çağrıldı
- Profili Düzenle
    * `B2C_1_EditProfile` çağrıldı

Uygulamanın yapılandırma dosyası iki `authorities`bildirir. Her ilke için bir tane. Her yetkilinin `type` özelliği `B2C`.

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

`redirect_uri`, uygulama yapılandırmasında kayıtlı olmalıdır ve [yetkilendirme kodu verme akışı](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)sırasında yeniden yönlendirmeyi desteklemek için `AndroidManifest.xml` ' de.

## <a name="initialize-ipublicclientapplication"></a>Ipublicclientapplication 'ı Başlat

`IPublicClientApplication`, uygulama yapılandırmasının zaman uyumsuz olarak ayrıştırılabilmesi için bir fabrika yöntemiyle oluşturulur.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Etkileşimli olarak belirteç alma

MSAL ile etkileşimli bir belirteç almak için `AcquireTokenParameters` bir örnek oluşturun ve `acquireToken` yöntemine sağlayın. Aşağıdaki belirteç isteği `default` yetkilisini kullanır.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Bir belirteci sessizce yenileme

MSAL ile sessizce bir belirteç almak için `AcquireTokenSilentParameters` bir örnek oluşturun ve `acquireTokenSilentAsync` yöntemine sağlayın. `acquireToken` yönteminden farklı olarak, belirteci sessizce almak için `authority` belirtilmelidir.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>İlke belirtin

B2C 'deki ilkeler ayrı yetkililer olarak temsil edildiğinden, `acquireToken` veya `acquireTokenSilent` parametreleri oluşturulurken bir `fromAuthority` yan tümcesi belirtilerek varsayılan dışında bir ilke çağırma elde edilir.  Örnek:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Parola değiştirme ilkelerini işle

Yerel hesap kaydolma veya oturum açma Kullanıcı akışında bir '**parola unuttum?** ' görüntülenir bağlantısının. Bu bağlantıya tıkladığınızda parola sıfırlama Kullanıcı akışı otomatik olarak tetiklenemez.

Bunun yerine, `AADB2C90118` hata kodu uygulamanıza döndürülür. Uygulamanız, parolayı sıfırlayan belirli bir kullanıcı akışını çalıştırarak bu hata kodunu işlemelidir.

Parola sıfırlama hata kodunu yakalamak için aşağıdaki uygulama `AuthenticationCallback`içinde kullanılabilir:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Iauthenticationresult kullanın

Başarılı bir belirteç alımı `IAuthenticationResult` nesne ile sonuçlanır. Erişim belirtecini, Kullanıcı taleplerini ve meta verileri içerir.

### <a name="get-the-access-token-and-related-properties"></a>Erişim belirtecini ve ilgili özellikleri al

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Yetkili hesabı al

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>Idtoken talepleri

Idtoken 'da döndürülen talepler, MSAL tarafından değil güvenlik belirteci hizmeti (STS) tarafından doldurulur. Kullanılan kimlik sağlayıcısına (IDP) bağlı olarak bazı talepler bulunmayabilir. Bazı IDPs 'Ler Şu anda `preferred_username` talebi sağlamıyor. Bu talep, önbelleğe alma için MSAL tarafından kullanıldığından, yerine bir yer tutucu değeri `MISSING FROM THE TOKEN RESPONSE`kullanılır. B2C ıdtoken talepleri hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C belirteçlere genel bakış](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Hesapları ve ilkeleri yönetme

B2C her bir ilkeyi ayrı bir yetkili olarak değerlendirir. Bu nedenle, her ilkeden döndürülen erişim belirteçleri, belirteçleri Yenile ve KIMLIK belirteçleri birbirini değiştirmez. Bu, her ilkenin belirteçleri diğer ilkeleri çağırmak için kullanılamayan ayrı bir `IAccount` nesnesi döndürdüğü anlamına gelir.

Her ilke, her kullanıcı için önbelleğe bir `IAccount` ekler. Bir Kullanıcı bir uygulamada oturum açarsa ve iki ilkeyi çağırlarsa iki `IAccount`s vardır. Bu kullanıcıyı önbellekten kaldırmak için her ilke için `removeAccount()` çağırmanız gerekir.

`acquireTokenSilent`bir ilke için belirteçleri yenilediğinizde, ilkenin önceki etkinleştirilmelerinde döndürülen aynı `IAccount` sağlayın `AcquireTokenSilentParameters`. Başka bir ilke tarafından döndürülen bir hesabın sağlanması hataya neden olur.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) Azure Active Directory B2C (Azure AD B2C) hakkında daha fazla bilgi edinin.

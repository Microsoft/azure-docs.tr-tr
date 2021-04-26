---
title: Azure AD B2C (MSAL Android) | Mavisi
titleSuffix: Microsoft identity platform
description: Android için Microsoft kimlik doğrulama kitaplığı (MSAL) ile Azure AD B2C kullanırken belirli hususlar hakkında bilgi edinin. Android
services: active-directory
author: iambmelt
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 1a9b9481d0b4086505bbfd3c2cd654ce228d1ae2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688884"
---
# <a name="use-msal-for-android-with-b2c"></a>B2C ile Android için MSAL kullanma

Microsoft kimlik doğrulama kitaplığı (MSAL), uygulama geliştiricilerinin [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml)kullanarak sosyal ve yerel kimliklere sahip kullanıcıların kimliğini doğrulamasını sağlar. Azure AD B2C bir kimlik yönetimi hizmetidir. Müşterilerinizin uygulamalarınızı kullandıklarında, nasıl oturum açıp yönetebileceğini özelleştirmek ve denetlemek için bu uygulamayı kullanın.

## <a name="choosing-a-compatible-authorization_user_agent"></a>Uyumlu authorization_user_agent seçme
B2C kimlik yönetimi sistemi, Google, Facebook, Twitter ve Amazon gibi birçok sosyal hesap sağlayıcısıyla kimlik doğrulamasını destekler. Uygulamanızda bu tür hesap türlerini desteklemeyi planlıyorsanız, MSAL genel istemci uygulamanızı, `DEFAULT` `BROWSER` [`authorization_user_agent`](msal-configuration.md#authorization_user_agent) bazı dış kimlik sağlayıcılarıyla WebView tabanlı kimlik doğrulamasının kullanımını engelleyen kısıtlamalardan dolayı veya değerini kullanacak şekilde yapılandırmanız önerilir.

## <a name="configure-known-authorities-and-redirect-uri"></a>Bilinen yetkilileri ve yeniden yönlendirme URI 'sini Yapılandır

Android için MSAL ' de, B2C ilkeleri (Kullanıcı, neys) bireysel yetkililer olarak yapılandırılır.

İki ilkeye sahip olan B2C uygulaması verildi:
- Kaydolma/oturum açma
    * Çağırılır `B2C_1_SISOPolicy`
- Profili Düzenle
    * Çağırılır `B2C_1_EditProfile`

Uygulamanın yapılandırma dosyası iki bildirmelidir `authorities` . Her ilke için bir tane. `type`Her bir yetkilinin özelliği `B2C` .

>Note: `account_mode` B2C uygulamaları Için **birden çok** olarak ayarlanması gerekir. [Birden çok hesap genel istemci uygulaması](./single-multi-account.md#multiple-account-public-client-application)hakkında daha fazla bilgi için belgelerine bakın.

### `app/src/main/res/raw/msal_config.json`

```json
{
  "client_id": "<your_client_id_here>",
  "redirect_uri": "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "authorization_user_agent" : "DEFAULT",
  "authorities": [
    {
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

, `redirect_uri` Uygulama yapılandırmasında kayıtlı olmalıdır ve  `AndroidManifest.xml` [yetkilendirme kodu verme akışı](../../active-directory-b2c/authorization-code-flow.md)sırasında yeniden yönlendirmeyi desteklemek için içinde.

## <a name="initialize-ipublicclientapplication"></a>Ipublicclientapplication 'ı Başlat

`IPublicClientApplication` , uygulama yapılandırmasının zaman uyumsuz olarak ayrıştırılabilmesi için bir fabrika yöntemiyle oluşturulur.

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

Bir belirteci MSAL ile etkileşimli bir şekilde almak için bir `AcquireTokenParameters` örnek oluşturun ve `acquireToken` yönteme sağlayın. Aşağıdaki belirteç isteği, yetkilisini kullanır `default` .

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

## <a name="silently-renew-a-token"></a>Belirteci sessizce yenileme

MSAL ile sessizce bir belirteç elde etmek için bir `AcquireTokenSilentParameters` örnek oluşturup `acquireTokenSilentAsync` yönteme sağlayın. Yönteminden farklı olarak, `acquireToken` `authority` belirteci sessizce almak için belirtilmelidir.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown
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
            // Token request was unsuccessful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>İlke belirtin

B2C 'deki ilkeler ayrı yetkililer olarak temsil edildiğinden, veya parametreleri oluşturulurken bir yan tümce belirtilerek varsayılan dışında bir ilke çağırma elde edilir `fromAuthority` `acquireToken` `acquireTokenSilent` .  Örnek:

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

Yerel hesap kaydolma veya oturum açma Kullanıcı akışında bir '**parola unuttum?**' görüntülenir ücretsiz bir hesap oluşturabilirsiniz. Bu bağlantıya tıkladığınızda parola sıfırlama Kullanıcı akışı otomatik olarak tetiklenemez.

Bunun yerine, hata kodu `AADB2C90118` uygulamanıza döndürülür. Uygulamanız, parolayı sıfırlayan belirli bir kullanıcı akışını çalıştırarak bu hata kodunu işlemelidir.

Parola sıfırlama hata kodunu yakalamak için aşağıdaki uygulama içinde kullanılabilir `AuthenticationCallback` :

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

Başarılı belirteç alımı bir nesne ile sonuçlanır `IAuthenticationResult` . Erişim belirtecini, Kullanıcı taleplerini ve meta verileri içerir.

### <a name="get-the-access-token-and-related-properties"></a>Erişim belirtecini ve ilgili özelliklerini alma

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

Idtoken 'da döndürülen talepler, MSAL tarafından değil güvenlik belirteci hizmeti (STS) tarafından doldurulur. Kullanılan kimlik sağlayıcısına (IDP) bağlı olarak bazı talepler bulunmayabilir. Bazı IDPs talepleri Şu anda sağlamıyor `preferred_username` . Bu talep, önbelleğe alma için MSAL tarafından kullanıldığı için, yerine bir yer tutucu değeri `MISSING FROM THE TOKEN RESPONSE` kullanılır. B2C ıdtoken talepleri hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C belirteçlere genel bakış](../../active-directory-b2c/tokens-overview.md#claims).

## <a name="managing-accounts-and-policies"></a>Hesapları ve ilkeleri yönetme

B2C her bir ilkeyi ayrı bir yetkili olarak değerlendirir. Bu nedenle, her ilkeden döndürülen erişim belirteçleri, belirteçleri Yenile ve KIMLIK belirteçleri birbirini değiştirmez. Bu, her ilkenin `IAccount` belirteçleri diğer ilkeleri çağırmak için kullanılamayan ayrı bir nesne döndürdüğü anlamına gelir.

Her ilke `IAccount` , her kullanıcı için önbelleğe ekler. Bir Kullanıcı bir uygulamada oturum açarsa ve iki ilkeyi çağırlarsa iki `IAccount` s vardır. Bu kullanıcıyı önbellekten kaldırmak için her ilke için çağrı yapmanız gerekir `removeAccount()` .

Bir ilke için belirteçleri yenilediğinizde `acquireTokenSilent` , `IAccount` ilkenin önceki etkinleştirilmelerinde döndürülen aynısını sağlayın  `AcquireTokenSilentParameters` . Başka bir ilke tarafından döndürülen bir hesabın sağlanması hataya neden olur.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory B2C](../../active-directory-b2c/overview.md) Azure Active Directory B2C (Azure AD B2C) hakkında daha fazla bilgi edinin.

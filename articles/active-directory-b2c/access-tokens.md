---
title: Erişim belirteci isteme - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C'den erişim belirteci istemeyi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a633a7656a287c0b8df050b6d14afb73c27a7460
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382155"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Azure Active Directory B2C'de erişim belirteci isteme

*Erişim belirteci*, API'lerinize verilen izinleri tanımlamak için Azure Active Directory B2C'de (Azure AD B2C) kullanabileceğiniz beyanlar içerir. Kaynak sunucusunu çağırırken HTTP isteğinde erişim belirteci bulunmalıdır. Erişim belirteci Azure AD B2C'den gelen yanıtlarda **access_token** olarak gösterilir.

Bu makalede web uygulaması ve web API'si için nasıl erişim belirteci isteyebileceğiniz gösterilir. Azure AD B2C'deki belirteçler hakkında daha fazla bilgi için [Azure Active Directory B2C'de belirteçlere genel bakış](tokens-overview.md) konusuna bakın.

> [!NOTE]
> **Web API zincirleri (Kullanıcı Adına) Azure AD B2C tarafından desteklenmez.** - Çoğu mimari başka bir aşağı akış web API'si çağırmayı gerektiren bir web API'si içerir; her iki API'nin güvenliği de Azure AD B2C tarafından sağlanır. Bu senaryo bir web API'si arka ucu olan ve başka bir hizmet çağıran istemcilerde yaygındır. Bu zincirli web API'si senaryosu, Kullanıcı Adına akışı olarak da bilinen OAuth 2.0 JWT Taşıyıcı Kimlik Bilgisi yetkisi kullanılarak desteklenebilir. Öte yandan Kullanıcı Adına akışı şu anda Azure AD B2C'de uygulanmamaktadır.

## <a name="prerequisites"></a>Ön koşullar

- Kullanıcıların uygulamanıza kaydolmasını ve oturum açmasını sağlamak için [bir kullanıcı akışı oluşturun](tutorial-create-user-flows.md).
- Daha önce yapmadıysanız, [Azure Active Directory B2C kiracınıza bir web API'si uygulaması ekleyin](add-web-api-application.md).

## <a name="scopes"></a>Kapsamlar

Kapsamlar korumalı kaynaklar üzerindeki izinleri yönetmek için bir yol sağlar. Erişim belirteci istenirken, istemci uygulamanın isteğin **scope** parametresinde istenen izinleri belirtmesi gerekir. Örneğin **App ID URI'si** `https://contoso.onmicrosoft.com/api` olan API'nin **Kapsam Değeri** olarak `read` belirtmek için, kapsam `https://contoso.onmicrosoft.com/api/read` olabilir.

Kapsamlar web API’si tarafından kapsam tabanlı erişim denetimi uygulamak için kullanılır. Örneğin web API'sinin kullanıcıları hem okuma hem de yazma veya yalnızca okuma erişimine sahip olabilir. Aynı istekte birden çok izin almak için, isteğin tek **scope** parametresine birden çok girdiyi boşluklarla ayırarak ekleyebilirsiniz.

Aşağıdaki örnekte URL'de kodu çözülmüş kapsamlar gösterilir:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Aşağıdaki örnekte URL'de kodlanmış kapsamlar gösterilir:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

İstemci uygulamanıza sağlanandan daha fazla kapsam isterseniz, en az bir izin verildiğinde çağrı başarılı olur. Sonuçta elde edilen erişim belirtecinde **scp** beyanı yalnızca başarılı bir şekilde verilmiş izinlerle doldurulur. 

### <a name="openid-connect-scopes"></a>OpenID Connect kapsamları

OpenID Connect standardı çeşitli özel kapsam değerlerini belirtir. Aşağıdaki kapsamlar kullanıcının profiline erişme iznini temsil eder:

- **openid** - Kimlik belirteci ister.
- **offline_access** - [Auth Code akışlarını](authorization-code-flow.md) kullanarak yenileme belirteci ister.
- **00000000-0000-0000-0000-000000000000** -kapsam olarak istemci kimliğini kullanmak, uygulamanızın aynı istemci kimliğiyle temsil edilen kendi hizmetinize veya Web API 'niz için kullanılabilecek bir erişim belirteci gerektiğini gösterir.

`/authorize` isteğindeki **response_type** parametresi `token` içeriyorsa, **scope** parametresi verilecek `openid` ve `offline_access` dışında en az bir kaynak kapsamı içermelidir. Aksi takdirde `/authorize` isteği başarısız olur.

## <a name="request-a-token"></a>Belirteç isteme

Erişim belirteci istemek için bir yetkilendirme kodu gerekir. Aşağıda yetkilendirme kodu için `/authorize` uç noktasına yönelik bir istek örneği verilmiştir. Özel etki alanlarının erişim belirteçleriyle kullanılması desteklenmez. İstek URL'sinde kiracı-adı.onmicrosoft.com etki alanınızı kullanın.

Aşağıdaki örnekte, şu değerleri değiştirirsiniz:

- `<tenant-name>` - Azure AD B2C kiracınızın adı.
- `<policy-name>` - Özel ilkenizin veya kullanıcı akışınızın adı.
- `<application-ID>` - Kullanıcı akışını desteklemek için kaydettiğiniz web uygulamasının uygulama tanımlayıcısı.
- `<application-ID-URI>` -İstemci uygulamasının **BIR API dikey penceresini açığa çıkarmak** altında ayarladığınız uygulama tanımlayıcı URI 'si.
- `<scope-name>` -Altına eklediğiniz kapsamın adı, istemci uygulamasının **BIR API dikey penceresini açığa çıkarır** .
- `<redirect-uri>` - İstemci uygulamayı kaydederken girdiğiniz **Yeniden Yönlendirme URI'si**.

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=<application-ID-URI>/<scope-name>
&response_type=code
```

Yetkilendirme kodunu içeren yanıt şu örneğe benzer olmalıdır:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Yetkilendirme kodunu başarıyla aldıktan sonra bu kodu kullanarak erişim belirteci isteyebilirsiniz:

```http
POST <tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=<application-ID-URI>/<scope-name>
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Aşağıdaki yanıta benzer bir sonuç görmeniz gerekir:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Döndürülen erişim belirtecini incelemek için https://jwt.ms kullandığınızda aşağıdaki örnekte gösterilene benzer bir şey görmeniz gerekir:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C'de belirteçleri yapılandırma](configure-tokens.md) hakkında bilgi edinin

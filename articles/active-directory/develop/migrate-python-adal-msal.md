---
title: Python için ADAL MSAL geçiş kılavuzu | Mavisi
description: Azure Active Directory kimlik doğrulaması kitaplığı (ADAL) Python uygulamanızı Python için Microsoft kimlik doğrulama kitaplığı 'na (MSAL) geçirmeyi öğrenin.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f4f19b1a9f6ee77fef2ca2634fb346025f41a6d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152755"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Python için ADAL MSAL geçiş kılavuzu

Bu makalede, Microsoft kimlik doğrulama kitaplığı 'nı (ADAL) Azure Active Directory kullanan bir uygulamayı Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanmak üzere geçirmek için gereken değişiklikler vurgulanmaktadır.

## <a name="difference-highlights"></a>Fark vurguları

ADAL Azure Active Directory v 1.0 uç noktasıyla birlikte çalışmaktadır. Microsoft kimlik doğrulama kitaplığı (MSAL), daha önce Azure Active Directory v 2.0 uç noktası olarak bilinen Microsoft Identity platformu ile birlikte kullanılır. Microsoft Identity platform bu Azure Active Directory v 1.0 'dan farklıdır:

Desteklememektedir
  - İş ve okul hesapları (Azure AD tarafından sağlanan hesaplar)
  - Kişisel hesaplar (Outlook.com veya Hotmail.com gibi)
  - Kendi e-postalarını veya sosyal kimlik bilgilerini (LinkedIn, Facebook, Google gibi) Azure AD B2C teklif aracılığıyla getiren müşterileriniz

- , İle uyumlu standartlara sahiptir:
  - OAuth v 2.0
  - OpenID Connect (OıDC)

Daha fazla ayrıntı için bkz. [Microsoft Identity platform (v 2.0) uç noktası hakkında ne fark var?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

### <a name="scopes-not-resources"></a>Kapsam kaynakları değil

ADAL Python, kaynaklar için belirteçleri alır, ancak MSAL Python kapsamlar için belirteçleri alır. MSAL Python 'daki API yüzeyinde artık kaynak parametresi yok. İstenen izinleri ve istenen kaynakları bildiren dizelerin bir listesi olarak kapsamlar sağlamanız gerekir. Kapsamların bir örneğini görmek için bkz. [Microsoft Graph kapsamları](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Hata işleme

Python için Azure Active Directory kimlik doğrulama kitaplığı (ADAL), bir sorun olduğunu göstermek için özel durum `AdalError` kullanır. Python için MSAL, genellikle bunun yerine hata kodlarını kullanır. Daha fazla bilgi için bkz. [msal for Python hata işleme](msal-handling-exceptions.md#msal-for-python-error-handling).

### <a name="api-changes"></a>API değişiklikleri

Aşağıdaki tabloda, Python için ADAL içindeki bir API ve Python için MSAL içindeki yerine kullanılacak bir API listelenmektedir:

| Python API 'SI için ADAL  | Python API için MSAL |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication veya ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Yok  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Yok (yukarıdaki bölüme bakın) |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) ve [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) ve [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| Yok | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| Yok | [Msal uzantılarından](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) kullanılabilen kalıcı önbellek |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>MSAL Python için mevcut yenileme belirteçlerini geçirme

Microsoft kimlik doğrulama kitaplığı (MSAL), yenileme belirteçleri kavramını soyutlar. MSAL Python, varsayılan olarak bir bellek içi belirteç önbelleği sunarak yenileme belirteçlerini depolamanıza, aramaya da güncelleştirmenize gerek kalmaz. Ayrıca, yenileme belirteçleri Kullanıcı müdahalesi olmadan güncelleştirilebildiğinden, kullanıcılar daha az oturum açma istemi görür. Belirteç önbelleği hakkında daha fazla bilgi için bkz. [Python IÇIN msal Içindeki özel belirteç önbelleği serileştirme](msal-python-token-cache-serialization.md).

Aşağıdaki kod, Python için MSAL tarafından yönetilmek üzere başka bir OAuth2 kitaplığı (ADAL Python dahil ancak bunlarla sınırlı olmamak üzere) tarafından yönetilen yenileme belirteçlerinizi geçirmenize yardımcı olur. Bu yenileme belirteçlerini geçirmenin bir nedeni, mevcut kullanıcıların, uygulamanızı Python için MSAL 'e geçirdiğinizde yeniden oturum açmasını önlemektir.

Yenileme belirtecini geçirme yöntemi, Python için MSAL kullanarak önceki yenileme belirtecini kullanarak yeni bir erişim belirteci elde etmek içindir. Yeni yenileme belirteci döndürüldüğünde, Python için MSAL bunu önbellekte depolar. Bunun nasıl yapılacağını gösteren bir örnek aşağıda verilmiştir:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için [v 1.0 ve v 2.0 karşılaştırması](active-directory-v2-compare.md)bölümüne bakın.

---
title: Python ADAL MSAL geçiş kılavuzu | Mavisi
titleSuffix: Microsoft identity platform
description: Azure Active Directory kimlik doğrulaması kitaplığı (ADAL) Python uygulamanızı Python için Microsoft kimlik doğrulama kitaplığı 'na (MSAL) geçirmeyi öğrenin.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 60d6e40b568c1189cdc01ef4239612d3717063b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578847"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Python için ADAL MSAL geçiş kılavuzu

Bu makalede, Microsoft kimlik doğrulama kitaplığı 'nı (ADAL) Azure Active Directory kullanan bir uygulamayı Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanmak üzere geçirmek için gereken değişiklikler vurgulanmaktadır.

## <a name="difference-highlights"></a>Fark vurguları

ADAL Azure Active Directory (Azure AD) v 1.0 uç noktasıyla birlikte çalışmaktadır. Microsoft kimlik doğrulama kitaplığı (MSAL), daha önce Azure Active Directory v 2.0 uç noktası olarak bilinen Microsoft Identity platformu ile birlikte kullanılır. Microsoft Identity platformu, Azure AD v 1.0 'dan farklıdır:

Desteklememektedir
  - İş ve okul hesapları (Azure AD tarafından sağlanan hesaplar)
  - Kişisel hesaplar (Outlook.com veya Hotmail.com gibi)
  - Kendi e-postalarını veya sosyal kimlik bilgilerini (LinkedIn, Facebook, Google gibi) Azure AD B2C teklif aracılığıyla getiren müşterileriniz

- , İle uyumlu standartlara sahiptir:
  - OAuth v 2.0
  - OpenID Connect (OıDC)

Daha fazla ayrıntı için bkz. [Microsoft Identity platformu hakkında ne fark var?](../azuread-dev/azure-ad-endpoint-comparison.md)

### <a name="scopes-not-resources"></a>Kapsam kaynakları değil

ADAL Python, kaynaklar için belirteçleri alır, ancak MSAL Python kapsamlar için belirteçleri alır. MSAL Python 'daki API yüzeyinde artık kaynak parametresi yok. İstenen izinleri ve istenen kaynakları bildiren dizelerin bir listesi olarak kapsamlar sağlamanız gerekir. Kapsamların bir örneğini görmek için bkz. [Microsoft Graph kapsamları](/graph/permissions-reference).

`/.default`Uygulamalarınızı v 1.0 uç noktasından (ADAL) Microsoft Identity platformu 'na (msal) geçirmeye yardımcı olması için kapsam sonekini kaynağa ekleyebilirsiniz. Örneğin, kaynak değeri için `https://graph.microsoft.com` , eşdeğer kapsam değeri `https://graph.microsoft.com/.default` .  Kaynak URL biçiminde değilse, ancak formun kaynak KIMLIĞI ise `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , kapsam değerini kullanmaya devam edebilirsiniz `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Farklı kapsam türleri hakkında daha fazla bilgi için [Microsoft Identity platformunda izinler ve onay](./v2-permissions-and-consent.md) ' i ve [v 1.0 belirteçleri 'ni kabul eden bir Web API 'si için kapsamları](./msal-v1-app-scopes.md) inceleyin.

### <a name="error-handling"></a>Hata işleme

Python için Azure Active Directory kimlik doğrulama kitaplığı (ADAL), `AdalError` bir sorun olduğunu göstermek için özel durumu kullanır. Python için MSAL, genellikle bunun yerine hata kodlarını kullanır. Daha fazla bilgi için bkz. [msal for Python hata işleme](msal-error-handling-python.md).

### <a name="api-changes"></a>API değişiklikleri

Aşağıdaki tabloda, Python için ADAL içindeki bir API ve Python için MSAL içindeki yerine kullanılacak bir API listelenmektedir:

| Python API 'SI için ADAL  | Python API için MSAL |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication veya ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Yok  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Yok |
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

Yenileme belirtecini geçirme yöntemi, Python için MSAL kullanarak önceki yenileme belirtecini kullanarak yeni bir erişim belirteci elde etmek içindir. Yeni yenileme belirteci döndürüldüğünde, Python için MSAL bunu önbellekte depolar.
MSAL Python 1.3.0 'den itibaren, bu amaçla MSAL içinde bir API sunuyoruz.
[Msal Python ile yenileme belirteçleri geçirmede tamamlanan bir örnekten](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67) alıntı yapın, lütfen aşağıdaki kod parçacığına bakın

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için [v 1.0 ve v 2.0 karşılaştırması](../azuread-dev/azure-ad-endpoint-comparison.md)bölümüne bakın.

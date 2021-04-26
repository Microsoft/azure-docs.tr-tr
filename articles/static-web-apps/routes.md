---
title: Azure statik Web Apps rotalar
description: Arka uç yönlendirme kuralları ve rollerle yolların güvenliğini sağlama hakkında bilgi edinin.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 0ddecff0162f8bd405c9f5fe6d3fdc20c6bc24aa
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313649"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Azure statik Web Apps önizlemede rotalar

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

Azure statik Web Apps yönlendirme, hem statik içerik hem<sup>de API 'ler</sup>için arka uç yönlendirme kurallarını ve yetkilendirme davranışını tanımlar. Kurallar, _staticwebapp.config.js_ dosyadaki bir kural dizisi olarak tanımlanır.

- Dosyadaki _staticwebapp.config.js_ , uygulamanın derleme yapıtı klasörünün kökünde bulunmalıdır.
- Kurallar dizide göründükleri sırada yürütülür `routes` .
- Kural değerlendirmesi ilk eşleşmede durduruluyor. Yönlendirme kuralları birlikte zincirlenmiş değildir.
- Roller dosyada _staticwebapp.config.js_ tanımlanmıştır ve kullanıcılar, [davetler](authentication-authorization.md)aracılığıyla rollerle ilişkilendirilir.
- Rol adları üzerinde tam denetim sahibi olursunuz.

Yönlendirme konusu kimlik doğrulama ve yetkilendirme kavramları ile önemli ölçüde örtüşüyor. Bu makaleyle birlikte [kimlik doğrulama ve yetkilendirme](authentication-authorization.md) kılavuzunu okuduğunuzdan emin olun.

Ayrıntılar için [örnek yol dosyasına](#example-route-file) bakın.

## <a name="location"></a>Konum

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

Dosyadaki _staticwebapp.config.js_ , uygulamanın derleme yapıtı klasörünün kökünde bulunmalıdır. Web uygulamanız belirli bir klasörden yapı yapıtı klasörünüze oluşturulan dosyaları kopyalayan bir yapı adımı içeriyorsa, dosyadaki _staticwebapp.config.js_ söz konusu klasörde bulunması gerekir.

Aşağıdaki tabloda, bir dizi ön uç çerçeve ve kitaplığı için _staticwebapp.config.js_ dosyaya yerleştirmek üzere uygun konum listelenmektedir.

| Çerçeve/kitaplık | Konum  |
| ------------------- | --------- |
| Angular             | _varlıklar_  |
| React               | _genel_  |
| Svelte              | _genel_  |
| Vue                 | _genel_  |
| Blazor              | _wwwroot_ |

Yukarıdaki tabloda yalnızca Azure static Web Apps ile uyumlu birkaç çerçeve ve kitaplık temsilcisidir. Daha fazla bilgi için [ön uç çerçeveleri ve kitaplıkları yapılandırma](./front-end-frameworks.md) bölümüne bakın.

## <a name="defining-routes"></a>Rotaları tanımlama

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

Yollar dosyada _staticwebapp.config.js_ , özelliğindeki yol kuralları dizisi olarak tanımlanmıştır `routes` . Her kural, bir veya daha fazla isteğe bağlı kural özelliği ile birlikte bir yol düzeninden oluşur. Kullanım örnekleri için [örnek yol dosyasına](#example-route-file) bakın.

| Rule özelliği  | Gerekli | Varsayılan değer | Yorum                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| -------------- | -------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`        | Yes      | yok           | Çağıran tarafından istenen yol deseninin.<ul><li>[Joker karakterler](#wildcards) rota yollarının sonunda desteklenir. Örneğin, Route _admin/ \*_ , _yönetici_ yolu altındaki herhangi bir rota ile eşleşir.<li>Yolun varsayılan dosyası _index.html_'dir.</ul>                                                                                                                                                                                                                                               |
| `serve`        | Hayır       | yok           | İstekten döndürülen dosyayı veya yolu tanımlar. Dosya yolu ve adı, istenen yoldan farklı olabilir. Bir `serve` değer tanımlanmamışsa, istenen yol kullanılır. QueryString parametreleri desteklenmiyor; `serve` değerler gerçek dosyalara işaret etmelidir.                                                                                                                                                                                                                 |
| `allowedRoles` | Hayır       | deðeri     | Rol adları dizisi. <ul><li>Geçerli karakterler, `a-z` , `A-Z` `0-9` ve içerir `_` .<li>Yerleşik rol `anonymous` tüm kimliği doğrulanmamış kullanıcılar için geçerlidir.<li>Yerleşik rol, `authenticated` oturum açmış tüm kullanıcılar için geçerlidir.<li>Kullanıcılar en az bir role ait olmalıdır.<li>Roller bir _veya_ temelinde eşleştirilir. Bir Kullanıcı listelenen rollerden varsa erişim izni verilir.<li>Bireysel kullanıcılar, [davetler](authentication-authorization.md)aracılığıyla rollerle ilişkilendirilir.</ul> |
| `statusCode`   | Hayır       | 200           | İstek için [http durum kodu](https://wikipedia.org/wiki/List_of_HTTP_status_codes) yanıtı.                                                                                                                                                                                                                                                                                                                                                                                        |

## <a name="securing-routes-with-roles"></a>Rollerle olan yolların güvenliğini sağlama

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

Bir kuralın dizisine bir veya daha fazla rol adı eklenerek rotalar güvenli hale getirilir `allowedRoles` . Kullanım örnekleri için [örnek yol dosyasına](#example-route-file) bakın.

Varsayılan olarak, her Kullanıcı yerleşik `anonymous` role aittir ve oturum açan tüm kullanıcılar `authenticated` rolün üyeleridir. Örneğin, bir yolu yalnızca kimliği doğrulanmış kullanıcılarla kısıtlamak için, yerleşik `authenticated` rolünü `allowedRoles` diziye ekleyin.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Dizide gerektiğinde yeni roller oluşturabilirsiniz `allowedRoles` . Bir yolu yalnızca yöneticilere kısıtlamak için, `administrator` dizide bir rol tanımlayabilirsiniz `allowedRoles` .

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Rol adları üzerinde tam denetiminiz vardır; rollerinizin uyması gereken hiçbir liste yok.
- Bireysel kullanıcılar, [davetler](authentication-authorization.md)aracılığıyla rollerle ilişkilendirilir.

## <a name="wildcards"></a>Joker karakterler

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

Joker karakter kuralları, belirli bir yol deseninin altındaki tüm isteklerle eşleşir. `serve`Kuralınıza bir değer tanımlarsanız, adlandırılan dosya veya yol yanıt olarak sunulur.

Örneğin, bir Takvim uygulamasının yollarını uygulamak için, tek bir dosyaya yönelik _Takvim_ yolu altında kalan tüm URL 'leri eşleyebilirsiniz.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

_calendar.html_ dosyası daha sonra, ve gibi URL çeşitlemeleri için farklı bir görünüme sahip istemci tarafı yönlendirmeyi kullanabilir `/calendar/january/1` `/calendar/2020` `/calendar/overview` .

Yolları joker karakterlerle da güvenli hale getirebilirsiniz. Aşağıdaki _örnekte, yönetici yolu altında_ istenen herhangi bir dosya, _yönetici_ rolünün bir üyesi olan kimliği doğrulanmış bir kullanıcı gerektirir.

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> Sunulan bir dosya tarafından başvurulan dosya istekleri yalnızca kimlik doğrulaması denetimleri için değerlendirilir. Örneğin, bir joker karakter yolu altındaki CSS dosyasına yapılan istekler CSS dosyasını sunar ve dosya olarak tanımlanmaz `serve` . CSS dosyası, Kullanıcı gerekli kimlik doğrulama gereksinimlerini karşıladığı sürece sunulur.

## <a name="fallback-routes"></a>Geri dönüş yolları

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

Tek sayfalı uygulamalar, ön uç JavaScript çerçeveleri veya kitaplıklarını ya da Blazor gibi WebAssembly platformlarını kullanıp kullanmadığını, genellikle Web uygulaması gezintisi için istemci tarafı yönlendirmeye güvenir. Bu istemci tarafı yönlendirme kuralları, isteğin sunucuya geri dönmesi gerekmeden tarayıcının pencere konumunu güncelleştirir. Sayfayı yenilerseniz veya doğrudan istemci tarafı yönlendirme kuralları tarafından oluşturulan konumlara gittiğinizde, uygun HTML sayfasına ulaşmak için sunucu tarafı geri dönüş yolu gereklidir.

Aşağıdaki örnekte ortak bir geri dönüş yolu gösterilmektedir:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Geri dönüş yolunun, önceden tanımlanmış kurallar tarafından yakalanmayan tüm istekleri yakalayarak, yönlendirme kurallarınızın son bölümünde listelenmesi gerekir.

## <a name="redirects"></a>Melere

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

İstekleri bir rotadan diğerine yönlendirmek için [301](https://en.wikipedia.org/wiki/HTTP_301) ve [302](https://en.wikipedia.org/wiki/HTTP_302) http durum kodlarını kullanabilirsiniz.

Örneğin, aşağıdaki kural _old-page.html_ 'den _new-page.html_'ye bir 301 yeniden yönlendirme oluşturur.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Yeniden yönlendirmeler, farklı dosyaları tanımlamayan yollarla da çalışır.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Özel hata sayfaları

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

Kullanıcılar, bir hataya neden olabilecek birçok farklı durum ile karşılaşabilir. Diziyi kullanarak `platformErrorOverrides` , bu hatalara yanıt olarak özel bir deneyim sağlayabilirsiniz. _staticwebapp.config.js_ dosyadaki dizinin yerleştirilmesi için [örnek yol dosyasına](#example-route-file) bakın.

> [!NOTE]
> İstek, platformu geçersiz kılma düzeyine yaptığında, yol kuralları yeniden çalıştırılmaz.

Aşağıdaki tabloda, kullanılabilir platform hatası geçersiz kılmaları listelenmektedir:

| Hata türü                                 | HTTP durum kodu | Açıklama                                                                                                                                                                                                                                                                                      |
| ------------------------------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `NotFound`                                 | 404              | Sunucuda bir sayfa bulunamadı.                                                                                                                                                                                                                                                               |
| `Unauthenticated`                          | 401              | Kullanıcı bir [kimlik doğrulama sağlayıcısıyla](authentication-authorization.md)oturum açmamış.                                                                                                                                                                                                    |
| `Unauthorized_InsufficientUserInformation` | 401              | Kimlik doğrulama sağlayıcısındaki kullanıcının hesabı, gerekli verileri göstermek için yapılandırılmamış. Bu hata, uygulamanın kimlik doğrulama sağlayıcısını kullanıcının e-posta adresine sorduğu, ancak kullanıcının e-posta adresine erişimi kısıtlamayı seçtiği durumlar gibi durumlarda meydana gelebilir.                    |
| `Unauthorized_InvalidInvitationLink`       | 401              | Bir davetin süresi sona erer veya Kullanıcı başka bir alıcı için oluşturulmuş bir davet bağlantısı izler.                                                                                                                                                                                       |
| `Unauthorized_MissingRoles`                | 401              | Kullanıcı gerekli bir rolün üyesi değil.                                                                                                                                                                                                                                                     |
| `Unauthorized_TooManyUsers`                | 401              | Site, en fazla kullanıcı sayısına ulaştı ve sunucu daha fazla eklemeleri kısıtlıyor. Bu hata, oluşturabileceğiniz [davetiye](authentication-authorization.md) sayısıyla sınırlı olmadığından ve bazı kullanıcıların davetlerini hiç kabul etmeyeceğinden istemciye sunulur. |
| `Unauthorized_Unknown`                     | 401              | Kullanıcının kimliğini doğrulamaya çalışırken bilinmeyen bir sorun var. Bu hatanın bir nedeni, uygulamanın izin vermediği için kullanıcının tanınmaması olabilir.                                                                                                          |

## <a name="custom-mime-types"></a>Özel mime türleri

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

`mimeTypes`Dizi ile aynı düzeyde listelenen nesne `routes` , [MIME türlerini](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types) dosya uzantılarıyla ilişkilendirmenize olanak tanır.

```json
{
  "routes": [],
  "mimeTypes": {
    "custom": "text/html"
  }
}
```

Yukarıdaki örnekte, uzantısına sahip tüm dosyalar `.custom` MIME türü ile birlikte sunulur `text/html` .

Aşağıdaki önemli noktalar, MIME türleriyle çalışırken önemlidir:

- Anahtarlar null veya boş olamaz ya da 50 karakterden uzun olamaz
- Değerler null veya boş olamaz ya da 1000 karakterden uzun olamaz

> [!NOTE]
> Statik Web Apps, Blazor uygulamalarını ve ıSTREAM ve DLL dosyaları için beklenen MIME türlerini anladığından, bunlar için eşlemeler eklemeniz gerekmez.

## <a name="default-headers"></a>Varsayılan üstbilgiler

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

`defaultHeaders`Dizi ile aynı düzeyde listelenen nesne `routes` , [yanıt üst bilgilerini](https://developer.mozilla.org/docs/Web/HTTP/Headers)eklemenize, değiştirmenize veya kaldırmanıza olanak sağlar.

Üst bilgi için bir değer sağlamak üstbilgiyi ekler ya da değiştirir. Boş bir değer sağlamak, üstbilginin istemciye sunulmasını kaldırır.

```json
{
  "routes": [],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'",
    "cache-control": "must-revalidate, max-age=6000",
    "x-dns-prefetch-control": ""
  }
}
```

Yukarıdaki örnekte, yeni bir `content-security-policy` üst bilgi eklenir, `cache-control` sunucu varsayılan değerini değiştirir ve `x-dns-prefetch-control` üstbilgi kaldırılır.

Aşağıdaki noktalar, üstbilgileriyle çalışırken önemlidir:

- Anahtarlar null veya boş olamaz.
- Null veya boş değerler bir üstbilgiyi işlemeden kaldırır.
- Anahtarlar veya değerler 8.000 karakterden uzun olamaz.
- Tanımlı üst bilgiler tüm isteklerle birlikte sunulur.
- _staticwebapp.config.js_ ' de tanımlanan üstbilgiler yalnızca statik içerik için geçerlidir. İşlevin kodundaki bir API uç noktasının yanıt üstbilgilerini özelleştirebilirsiniz.

## <a name="example-route-file"></a>Örnek yol dosyası

> [!IMPORTANT]
> Dosyasında _routes.js_ tanımlanan işlevsellik artık kullanım dışıdır ve Azure statik Web Apps [yapılandırma dosyasında](./configuration.md#routes)daha iyi uygulanmıştır.

Aşağıdaki örnek, bir _staticwebapp.config.js_ dosyasındaki statik Içerik ve API 'ler için yol kurallarının nasıl oluşturulacağını gösterir. Bazı yollar, kimlik doğrulaması ile ilgili uç noktalara erişen [ _/. auth_ sistem klasörünü](authentication-authorization.md) kullanır.

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "302",
      "serve": "/login"
    }
  ],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
    "custom": "text/html"
  }
}
```

Aşağıdaki örneklerde bir istek bir kuralla eşleştiğinde ne olacağı açıklanır.

| İstekleri...                     | Sonuç...                                                                                                                                                                                                                                                                                                   |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _PROFILE_                         | Kimliği doğrulanmış kullanıcılara _/profile/index.html_ dosyası sunulur. Kimliği doğrulanmamış kullanıcılar, _/login_'a yönlendirilir.                                                                                                                                                                                                   |
| _/admin/reports_                   | _Yöneticiler_ rolündeki kimliği doğrulanmış kullanıcılar, _/admin/Reports/index.html_ dosyası olarak sunulur. _Yöneticiler_ rolünde olmayan kimliği doğrulanmış kullanıcılar 401 hatası <sup>2</sup>olarak sunulur. Kimliği doğrulanmamış kullanıcılar, _/login_'a yönlendirilir.                                                                       |
| _/api/admin_                       | _Yöneticiler_ rolündeki kimliği doğrulanmış kullanıcılardan gelen istekler API 'ye gönderilir. _Yönetici_ rolünde olmayan kimliği doğrulanmış kullanıcılar ve kimliği doğrulanmamış kullanıcılar 401 hatası olarak sunulur.                                                                                                                     |
| _/Customers/contoso_               | _Yöneticiler_ veya _customers_contoso_ rollerine ait olan kimliği doğrulanmış kullanıcılar _/customers/contoso/index.html_ dosya <sup>2</sup>' ye sunulur. _Yöneticiler_ veya _customers_contoso_ rollerde bulunmayan kimliği doğrulanmış kullanıcılar 401 hatası olarak sunulur. Kimliği doğrulanmamış kullanıcılar, _/login_'a yönlendirilir. |
| _/Login_                           | Kimliği doğrulanmamış kullanıcılar GitHub ile kimlik doğrulaması yapmak için kullanılır.                                                                                                                                                                                                                                              |
| _/54/Auth/login/Twitter_             | Twitter ile yetkilendirme devre dışı bırakıldı. Sunucu bir 404 hatasıyla yanıt verir.                                                                                                                                                                                                                                  |
| _/Logout_                          | Kullanıcılar herhangi bir kimlik doğrulama sağlayıcısından oturum açtı.                                                                                                                                                                                                                                                           |
| _/Calendar/2020/01_                | Tarayıcıya _/calendar.html_ dosyası sunulur.                                                                                                                                                                                                                                                               |
| _/Özel_                        | Tarayıcı, _/anlaşmalar_'a yönlendirilir.                                                                                                                                                                                                                                                                         |
| _/Unknown-klasörü_                  | _/custom-404.html_ dosyası sunulur.                                                                                                                                                                                                                                                                         |
| `.custom`Uzantılı dosyalar | MIME türü ile birlikte sunulur `text/html`                                                                                                                                                                                                                                                                      |

Tüm yanıtlar, `content-security-policy` değerine sahip olan üst bilgileri içerir `default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'` .

<sup>1</sup> API işlevleri için yol kuralları yalnızca yeniden [yönlendirmeleri](#redirects) destekler ve [yolların rollerle güvenliğini sağlama](#securing-routes-with-roles).

<sup>2</sup> dizide bir kural tanımlayarak özel bir hata sayfası sağlayabilirsiniz `Unauthorized_MissingRoles` `platformErrorOverrides` .

## <a name="restrictions"></a>Kısıtlamalar

- Dosyadaki _staticwebapp.config.js_ 100 KB 'tan fazla olamaz
- Dosyadaki _staticwebapp.config.js_ en fazla 50 farklı rolü destekler

Genel kısıtlamalar ve sınırlamalar için [Kotalar makalesine](quotas.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kimlik doğrulama ve yetkilendirme kurulumu](authentication-authorization.md)

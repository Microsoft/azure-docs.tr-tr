---
title: Azure statik Web Apps yapılandırma
description: Azure statik Web Apps için yolları yapılandırmayı, güvenlik kurallarını ve genel ayarları zorunlu yapmayı öğrenin.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 9494bcc9941491bbb82c6a948dce720cb9e51424
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502292"
---
# <a name="configure-azure-static-web-apps"></a>Azure statik Web Apps yapılandırma

Azure statik Web Apps yapılandırması, aşağıdaki ayarları denetleyen dosyada _staticwebapp.config.js_ tanımlanmıştır:

- Yönlendirme
- Kimlik Doğrulaması
- Yetkilendirme
- Geri dönüş kuralları
- HTTP yanıtı geçersiz kılmaları
- Genel HTTP üst bilgi tanımları
- Özel MIME türleri

## <a name="file-location"></a>Dosya konumu

_staticwebapp.config.js_ için önerilen konum, `app_location` [iş akışı dosyasında](./github-actions-workflow.md)olarak ayarlanan klasör. Ancak, dosya, uygulama kaynak kodu klasörünüzün içinde herhangi bir konuma yerleştirilebilir.

Ayrıntılar için bkz. [örnek yapılandırma](#example-configuration-file) dosyası.

> [!IMPORTANT]
> Bir _staticwebapp.config.js_ varsa, [dosyadaki _routes.js_](./routes.md) yok sayılır.

## <a name="routes"></a>Yollar

Yol kuralları, uygulamanıza Web 'e erişim izni veren URL 'lerin modelini tanımlamanızı sağlar. Yollar, yönlendirme kuralları dizisi olarak tanımlanır. Kullanım örnekleri için bkz. [örnek yapılandırma dosyası](#example-configuration-file) .

- `routes`Yalnızca bir yönlendirmenize sahip olsanız bile, kurallar dizide tanımlanır.
- Kurallar dizide göründükleri sırada yürütülür `routes` .
- Kural değerlendirmesi ilk eşleşme sırasında durduruluyor-yönlendirme kuralları birlikte zincirlemiyor.
- Özel rol adları üzerinde tam denetim sahibi olursunuz.
  - Ve içeren birkaç yerleşik rol adı vardır [`anonymous`](./authentication-authorization.md) [`authenticated`](./authentication-authorization.md) .

Yönlendirme, kimlik doğrulaması (Kullanıcı tanımlayarak) ve yetkilendirme (kullanıcıya yetenekler atama) kavramlarıyla önemli ölçüde çakışacak. Bu makaleyle birlikte [kimlik doğrulama ve yetkilendirme](authentication-authorization.md) kılavuzunu okuduğunuzdan emin olun.

Statik içerik için varsayılan dosya _index.html_ dosyasıdır.

## <a name="defining-routes"></a>Rotaları tanımlama

Her kural, bir veya daha fazla isteğe bağlı kural özelliği ile birlikte bir yol düzeninden oluşur. Yol kuralları `routes` dizide tanımlanmıştır. Kullanım örnekleri için bkz. [örnek yapılandırma dosyası](#example-configuration-file) .

| Rule özelliği                       | Gerekli | Varsayılan değer                        | Yorum                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------- | -------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`                             | Yes      | yok                                  | Çağıran tarafından istenen yol deseninin.<ul><li>[Joker karakterler](#wildcards) rota yollarının sonunda desteklenir.<ul><li>Örneğin, Route _admin/ \*_ , _yönetici_ yolu altındaki herhangi bir rota ile eşleşir.</ul></ul>                                                                                                                                                                                                                                                                                                                                                                                                        |
| `rewrite`                           | Hayır       | yok                                  | İstekten döndürülen dosyayı veya yolu tanımlar.<ul><li>Bir kurala birbirini dışlar `redirect`<li>Yeniden yazma kuralları tarayıcının konumunu değiştirmez.<li>Değerler uygulamanın köküne göreli olmalıdır</ul>                                                                                                                                                                                                                                                                                                                                                                                                      |
| `redirect`                          | Hayır       | yok                                  | İstek için dosya veya yol yeniden yönlendirme hedefini tanımlar.<ul><li>Bir kurala birbirini dışlar `rewrite` .<li>Yeniden yönlendirme kuralları tarayıcının konumunu değiştirir.<li>Varsayılan yanıt kodu bir [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (geçici yeniden yönlendirme), ancak bir [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (kalıcı yeniden yönlendirme) ile geçersiz kılınabilir.</ul>                                                                                                                                                                                                              |
| `allowedRoles`                      | Hayır       | deðeri                            | Bir rotaya erişmek için gereken rol adlarının listesini tanımlar. <ul><li>Geçerli karakterler, `a-z` , `A-Z` `0-9` ve içerir `_` .<li>Yerleşik rol, [`anonymous`](./authentication-authorization.md) tüm kimliği doğrulanmamış kullanıcılar için geçerlidir<li>Yerleşik rol, [`authenticated`](./authentication-authorization.md) her oturum açmış kullanıcı için geçerlidir.<li>Kullanıcılar en az bir role ait olmalıdır.<li>Roller bir _veya_ temelinde eşleştirilir.<ul><li>Bir Kullanıcı listelenen rollerden varsa erişim izni verilir.</ul><li>Bireysel kullanıcılar, [davetler](authentication-authorization.md)aracılığıyla rollerle ilişkilendirilir.</ul> |
| `headers`<a id="route-headers"></a> | Hayır       | yok                                  | Yanıta eklenen [http üstbilgileri](https://developer.mozilla.org/docs/Web/HTTP/Headers) kümesi. <ul><li>Rotaya özgü üst bilgi, [`globalHeaders`](#global-headers) yanıtta genel üst bilgiyle aynı olduğunda, rotaya özgü üstbilgiler geçersiz kılınır.<li>Bir üst bilgiyi kaldırmak için değeri boş bir dizeye ayarlayın.</ul>                                                                                                                                                                                                                                                                                          |
| `statusCode`                        | Hayır       | `200`, `301` , veya yeniden `302` yönlendirmeler için | Yanıtın [http durum kodu](https://developer.mozilla.org/docs/Web/HTTP/Status) .                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `methods`                           | Hayır       | Tüm Yöntemler                          | Bir rota ile eşleşen istek yöntemlerinin listesi. Kullanılabilir yöntemler şunlardır: `GET` , `HEAD` , `POST` , `PUT` , `DELETE` , `CONNECT` , `OPTIONS` , `TRACE` , ve `PATCH` .                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

Her özelliğin istek/yanıt ardışık düzeninde belirli bir amacı vardır.

| Amaç                                        | Özellikler                                                                                   |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Yolları Eşleştir                                   | `route`, `methods`                                                                           |
| Bir yol eşleştirdikten sonra yetkilendir             | `allowedRoles`                                                                               |
| Kural eşleştirildiğinde ve yetkilendirildikten sonra işlem | `rewrite` (isteği değiştirir) <br><br>`redirect`, `headers` , `statusCode` (yanıtı değiştirir) |

## <a name="securing-routes-with-roles"></a>Rollerle olan yolların güvenliğini sağlama

Yollar, bir kuralın dizisine bir veya daha fazla rol adı eklenerek `allowedRoles` ve kullanıcılar, [davetler](./authentication-authorization.md)aracılığıyla özel rollerle ilişkilendirilerek güvenliği sağlanır. Kullanım örnekleri için bkz. [örnek yapılandırma dosyası](#example-configuration-file) .

Varsayılan olarak, her Kullanıcı yerleşik `anonymous` role aittir ve oturum açan tüm kullanıcılar `authenticated` rolün üyeleridir.

Örneğin, bir yolu yalnızca kimliği doğrulanmış kullanıcılarla kısıtlamak için, yerleşik `authenticated` rolünü `allowedRoles` diziye ekleyin.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Dizide gerektiğinde yeni roller oluşturabilirsiniz `allowedRoles` . Bir yolu yalnızca yöneticilere kısıtlamak için, dizisinde adlı kendi rolünüzü tanımlayabilirsiniz `administrator` `allowedRoles` .

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Rol adları üzerinde tam denetiminiz vardır; rollerinizin uyması gereken hiçbir liste yok.
- Bireysel kullanıcılar, [davetler](authentication-authorization.md)aracılığıyla rollerle ilişkilendirilir.

## <a name="wildcards"></a>Joker karakterler

Joker karakter kuralları bir yol deseninin tüm isteklerini eşleştirirken, yalnızca bir yolun sonunda desteklenir ve dosya uzantısına göre filtrelenebilir. Kullanım örnekleri için bkz. [örnek yapılandırma dosyası](#example-configuration-file) .

Örneğin, bir Takvim uygulamasının yollarını uygulamak için, tek bir dosya sağlamak üzere _Takvim_ yolu altına düşen tüm URL 'leri yeniden yazabilirsiniz.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

_calendar.html_ dosyası daha sonra, ve gibi URL çeşitlemeleri için farklı bir görünüme sahip istemci tarafı yönlendirmeyi kullanabilir `/calendar/january/1` `/calendar/2020` `/calendar/overview` .

Joker karakter eşleşmelerini dosya uzantısına göre filtreleyebilirsiniz. Örneğin, belirli bir yoldaki yalnızca HTML dosyalarıyla eşleşen bir kural eklemek istiyorsanız aşağıdaki kuralı oluşturabilirsiniz:

```json
{
  "route": "/articles/*.html",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Birden çok dosya uzantısını filtrelemek için, aşağıdaki örnekte gösterildiği gibi, seçenekleri küme ayraçları içine dahil edersiniz:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Joker karakter yolları için genel kullanım örnekleri şunları içerir:

- Tüm yol deseninin belirli bir dosyasına sunma
- Farklı HTTP yöntemlerini yol deseninin tamamına eşleme
- Kimlik doğrulama ve yetkilendirme kurallarını zorlama
- Özel önbelleğe alma kuralları uygulama

## <a name="fallback-routes"></a>Geri dönüş yolları

Tek sayfalı uygulamalar genellikle istemci tarafı yönlendirmeye güvenir. Bu istemci tarafı yönlendirme kuralları, isteğin sunucuya geri dönmesi gerekmeden tarayıcının pencere konumunu güncelleştirir. Sayfayı yenilerseniz veya doğrudan istemci tarafı yönlendirme kuralları tarafından oluşturulan URL 'lere gittiğinizde, uygun HTML sayfasına (genellikle istemci tarafı uygulamanız için _index.html_ ) sahip olan bir sunucu tarafı geri dönüş yolu gereklidir.

Uygulamanızı, dosya filtresiyle bir yol joker karakteri kullanan aşağıdaki örnekte gösterildiği gibi bir geri dönüş yolu uygulayan kuralları kullanacak şekilde yapılandırabilirsiniz:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

Aşağıda örnek dosya yapısı, bu kural ile aşağıdaki sonuçlar mümkündür.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| İstekleri...                                         | döndürür...                                                                                                    | durumu ile... |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- | ------------------ |
| _bilgi_                                              | _/index.html_ dosyası                                                                                        | `200`              |
| _/images/logo.png_                                     | Görüntü dosyası                                                                                                | `200`              |
| _/images/icon.exe_                                     | _/index.html_ dosyası- _SVG_ dosya uzantısı filtrede listelenmediğinden `/images/*.{png,jpg,gif}` | `200`              |
| _/images/unknown.png_                                  | Dosya bulunamadı hatası                                                                                          | `404`              |
| _/CSS/Unknown.exe_                                     | Dosya bulunamadı hatası                                                                                          | `404`              |
| _/CSS/Global.exe_                                      | Stil sayfası dosyası                                                                                           | `200`              |
| _/Images_ veya _/CSS_ klasörlerinin dışında başka bir dosya | _/index.html_ dosyası                                                                                        | `200`              |

## <a name="global-headers"></a>Genel üstbilgiler

Bu `globalHeaders` bölüm, bir [yol üst bilgisi](#route-headers) kuralı tarafından geçersiz kılınmadığı müddetçe her yanıta uygulanan bir [http üst bilgileri](https://developer.mozilla.org/docs/Web/HTTP/Headers) kümesi sağlar, aksi takdirde hem rotadaki hem de genel üst bilgilerden birleşim döndürülür.

Kullanım örnekleri için bkz. [örnek yapılandırma dosyası](#example-configuration-file) .

Bir üst bilgiyi kaldırmak için değeri boş bir dize () olarak ayarlayın `""` .

Genel üst bilgiler için bazı yaygın kullanım örnekleri şunlardır:

- Özel önbelleğe alma kuralları
- Güvenlik ilkelerini zorlama
- Kodlama ayarları

## <a name="response-overrides"></a>Yanıt geçersiz kılmaları

Bu `responseOverrides` bölüm, sunucu başka bir hata kodu döndürene zaman özel bir yanıt tanımlamaya yönelik bir fırsat sağlar. Kullanım örnekleri için bkz. [örnek yapılandırma dosyası](#example-configuration-file) .

Geçersiz kılmak için aşağıdaki HTTP kodları kullanılabilir:

| Durum Kodu                                                   | Anlamı      | Olası nedeni                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Hatalı istek  | Geçersiz davet bağlantısı                                                                                                                                                                                                                                                                            |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Yetkisiz | Kimliği doğrulanmamış sırada kısıtlanmış sayfalara yönelik istek                                                                                                                                                                                                                                                  |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Yasak    | <ul><li>Kullanıcı oturum açtı, ancak sayfayı görüntülemek için gerekli rolleri yok.<li>Kullanıcı oturum açtı, ancak çalışma zamanı, kimlik taleplerinden Kullanıcı ayrıntılarını alamıyor.<li>Sitede özel rolleri olan çok sayıda kullanıcı oturum açtı, bu nedenle çalışma zamanı kullanıcının oturumunu açamaz.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Bulunamadı    | Dosya bulunamadı                                                                                                                                                                                                                                                                                     |

Aşağıdaki örnek yapılandırma bir hata kodunun nasıl geçersiz kılınacağını göstermektedir.

```json
{
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html",
      "statusCode": 200
    },
    "401": {
      "statusCode": 302,
      "redirect": "/login"
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html",
      "statusCode": 200
    },
    "404": {
      "rewrite": "/custom-404.html",
      "statusCode": 200
    }
  }
}
```

## <a name="example-configuration-file"></a>Örnek yapılandırma dosyası

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
      "route": "/images/*",
      "headers": {
        "cache-control": "must-revalidate, max-age=15770000"
      }
    },
    {
      "route": "/api/*",
      "methods": ["GET"],
      "allowedRoles": ["registeredusers"]
    },
    {
      "route": "/api/*",
      "methods": ["PUT", "POST", "PATCH", "DELETE"],
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/*",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "rewrite": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": 404
    },
    {
      "route": "/logout",
      "redirect": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "rewrite": "/calendar.html"
    },
    {
      "route": "/specials",
      "redirect": "/deals",
      "statusCode": 301
    }
  ],
  "navigationFallback": {
    "rewrite": "index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  },
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html"
    },
    "401": {
      "redirect": "/login",
      "statusCode": 302
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html"
    },
    "404": {
      "rewrite": "/404.html"
    }
  },
  "globalHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
    ".json": "text/json"
  }
}
```

Yukarıdaki yapılandırmaya bağlı olarak, aşağıdaki senaryoları gözden geçirin.

| İstekleri...                                                    | Sonuç...                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _PROFILE_                                                        | Kimliği doğrulanmış kullanıcılara _/profile/index.html_ dosyası sunulur. Kimliği doğrulanmamış kullanıcılar, _/login_'a yönlendirilir.                                                                                                                                                                                                                                                                                                                              |
| _/admin_                                                         | _Yönetici_ rolündeki kimliği doğrulanmış kullanıcılar, _/admin/index.html_ dosyası olarak sunulur. _Yönetici_ rolünde olmayan kimliği doğrulanmış kullanıcılar bir `403` hata <sup>1</sup>olarak sunulur. Kimliği doğrulanmamış kullanıcılar, _/login_'a yönlendirilir.                                                                                                                                                                                                          |
| _/logo.png_                                                       | En fazla yaş 182 gün (15.770.000 saniye) olan bir özel önbellek kuralıyla görüntüye hizmet verir.                                                                                                                                                                                                                                                                                                                                   |
| _/api/admin_                                                      | `GET`_registeredusers_ rolündeki kimliği doğrulanmış kullanıcılardan gelen istekler API 'ye gönderilir. _Registeredusers_ rolünde olmayan kimliği doğrulanmış kullanıcılar ve kimliği doğrulanmamış kullanıcılar bir hata olarak sunulur `401` .<br/><br/>`POST``PUT` `PATCH` `DELETE` _yönetici_ rolündeki kimlik doğrulamalı kullanıcılardan,,, ve istekleri API 'ye gönderilir. _Yönetici_ rolünde olmayan kimliği doğrulanmış kullanıcılar ve kimliği doğrulanmamış kullanıcılar bir hata olarak sunulur `401` . |
| _/Customers/contoso_                                              | _Yönetici_ veya _customers_contoso_ rollerine ait olan kimliği doğrulanmış kullanıcılar _/customers/contoso/index.html_ dosyası olarak sunulur. _Yönetici_ veya _customers_contoso_ rollerde bulunmayan kimliği doğrulanmış kullanıcılar bir `403` hata <sup>1</sup>olarak sunulur. Kimliği doğrulanmamış kullanıcılar, _/login_'a yönlendirilir.                                                                                                                            |
| _/Login_                                                          | Kimliği doğrulanmamış kullanıcılar GitHub ile kimlik doğrulaması yapmak için kullanılır.                                                                                                                                                                                                                                                                                                                                                                             |
| _/54/Auth/login/Twitter_                                            | Twitter ile yetkilendirme, yönlendirme kuralı tarafından devre dışı bırakıldığı `404` için, bir durum kodu ile hizmet vermeye _/index.html_ 'ye geri kalan hata döndürülür `200` .                                                                                                                                                                                                                                                                                     |
| _/Logout_                                                         | Kullanıcılar herhangi bir kimlik doğrulama sağlayıcısından oturum açtı.                                                                                                                                                                                                                                                                                                                                                                                          |
| _/Calendar/2021/01_                                               | Tarayıcıya _/calendar.html_ dosyası sunulur.                                                                                                                                                                                                                                                                                                                                                                                              |
| _/Özel_                                                       | Tarayıcı, _/anlaşmalar_'e kalıcı olarak yönlendirilir.                                                                                                                                                                                                                                                                                                                                                                                            |
| _/data.js_                                                      | `text/json`MIME türüyle sunulan dosya.                                                                                                                                                                                                                                                                                                                                                                                               |
| _/About_ veya istemci tarafı yönlendirme desenleriyle eşleşen herhangi bir klasör | _/index.html_ dosyası bir durum kodu ile birlikte sunulur `200` .                                                                                                                                                                                                                                                                                                                                                                                    |
| _/İmages/_ klasöründe var olmayan bir dosya                     | Bir `404` hata.                                                                                                                                                                                                                                                                                                                                                                                                                                |

<sup>1</sup> [yanıt geçersiz kılma kuralı](#response-overrides)kullanarak özel bir hata sayfası sağlayabilirsiniz.

## <a name="restrictions"></a>Kısıtlamalar

Dosyasında _staticwebapps.config.js_ için aşağıdaki kısıtlamalar vardır.

- En büyük dosya boyutu 100 KB
- En fazla 50 farklı rol

Genel kısıtlamalar ve sınırlamalar için [Kotalar makalesine](quotas.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kimlik doğrulama ve yetkilendirme kurulumu](authentication-authorization.md)

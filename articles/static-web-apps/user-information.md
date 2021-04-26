---
title: Azure statik Web Apps kullanıcı bilgilerine erişme
description: Yetkilendirme sağlayıcısı 'nın döndürdüğü Kullanıcı verilerini okumayı öğrenin.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 9aca1e76c825de52744da817f6a0bf236eef617c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313615"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Azure statik Web Apps önizlemede Kullanıcı bilgilerine erişme

Azure statik Web Apps, bir [doğrudan erişim uç noktası](#direct-access-endpoint) ve [API işlevleri](#api-functions)aracılığıyla kimlik doğrulamaya ilişkin Kullanıcı bilgilerini sağlar.

Birçok kullanıcı arabirimi, Kullanıcı kimlik doğrulama verilerini yoğun bir şekilde kullanır. Doğrudan erişim uç noktası, özel bir işlev uygulamak zorunda kalmadan kullanıcı bilgilerini kullanıma sunan bir yardımcı program API 'sidir. Kolaylık olmadan, doğrudan erişim uç noktası sunucusuz mimariyle ilişkili soğuk başlangıç gecikmelerine tabi değildir.

## <a name="client-principal-data"></a>İstemci asıl verileri

İstemci sorumlusu veri nesnesi, uygulamanıza Kullanıcı tarafından tanımlanabilir bilgiler sunar. Aşağıdaki özellikler istemci sorumlusu nesnesinde öne çıkanlar:

| Özellik           | Açıklama                                                                                                                                                                                                                                                                                                                                                        |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `identityProvider` | [Kimlik sağlayıcısının](authentication-authorization.md)adı.                                                                                                                                                                                                                                                                                              |
| `userId`           | Kullanıcı için Azure statik Web Apps özgü benzersiz tanımlayıcı. <ul><li>Değer, uygulama başına temelinde benzersizdir. Örneğin, aynı kullanıcı `userId` farklı bir statik Web Apps kaynağı üzerinde farklı bir değer döndürür.<li>Değer, bir kullanıcının ömrü boyunca devam ettirir. Aynı kullanıcıyı siler ve uygulamaya geri eklerseniz, yeni bir `userId` oluşturulur.</ul> |
| `userDetails`      | Kullanıcının Kullanıcı adı veya e-posta adresi. Bazı sağlayıcılar [kullanıcının e-posta adresini](authentication-authorization.md)döndürür, diğer bir deyişle [Kullanıcı tanıtıcısı](authentication-authorization.md)gönderilir.                                                                                                                                                                    |
| `userRoles`        | [Kullanıcının atanan rollerinin](authentication-authorization.md)dizisi.                                                                                                                                                                                                                                                                                          |

Aşağıdaki örnek bir örnek istemci asıl nesnesidir:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": ["anonymous", "authenticated"]
}
```

## <a name="direct-access-endpoint"></a>Doğrudan erişim uç noktası

Yola bir istek gönderebilir `GET` `/.auth/me` ve istemci asıl verilerine doğrudan erişim elde edebilirsiniz. Görünümlerinizin durumu yetkilendirme verilerini temel aldığından, en iyi performans için bu yaklaşımı kullanın.

Oturum açmış kullanıcılar için, yanıt bir istemci sorumlusu JSON nesnesi içerir. Kimliği doğrulanmamış kullanıcılardan gelen istekler geri döner `null` .

[Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> API 'sini kullanarak, aşağıdaki sözdizimini kullanarak istemci asıl verilerine erişebilirsiniz.

```javascript
async function getUserInfo() {
  const response = await fetch('/.auth/me');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API işlevleri

Azure Işlevleri arka ucu aracılığıyla statik Web Apps kullanılabilir olan API işlevleri, istemci uygulamasıyla aynı kullanıcı bilgilerine erişebilir. API Kullanıcı tanımlı bilgiler aldığından, kullanıcının kimliği doğrulandıysa veya gerekli bir rolle eşleşiyorsa kendi denetimlerini gerçekleştirmez. Erişim denetimi kuralları [`staticwebapp.config.json`](routes.md) dosyada tanımlanmıştır.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

İstemci sorumlusu verileri, istek üstbilgisindeki API işlevlerine geçirilir `x-ms-client-principal` . İstemci sorumlusu verileri, seri hale getirilmiş JSON nesnesi içeren [Base64](https://www.wikipedia.org/wiki/Base64)kodlu bir dize olarak gönderilir.

Aşağıdaki örnek işlev, Kullanıcı bilgilerinin nasıl okunacağını ve geri alınacağını gösterir.

```javascript
module.exports = async function (context, req) {
  const header = req.headers['x-ms-client-principal'];
  const encoded = Buffer.from(header, 'base64');
  const decoded = encoded.toString('ascii');

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded),
    },
  };
};
```

Yukarıdaki işlevin adlandırıldığını varsayarsak `user` , aşağıdaki sözdizimini kullanarak API 'nin yanıtına erişmek için [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> tarayıcı API 'sini kullanabilirsiniz.

```javascript
async function getUser() {
  const response = await fetch('/api/user');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

Bir C# işlevinde, Kullanıcı bilgileri `x-ms-client-principal` bir `ClaimsPrincipal` nesneye veya kendi özel türüne seri durumdan çıkarılabilen üst bilgide kullanılabilir. Aşağıdaki kod, `ClientPrincipal` daha sonra bir örneğe açık olan bir aracı türüne üstbilginin nasıl paketten alınacağını gösterir `ClaimsPrincipal` .

```csharp
  public static class StaticWebAppsAuth
  {
    private class ClientPrincipal
    {
        public string IdentityProvider { get; set; }
        public string UserId { get; set; }
        public string UserDetails { get; set; }
        public IEnumerable<string> UserRoles { get; set; }
    }

    public static ClaimsPrincipal Parse(HttpRequest req)
    {
        var principal = new ClientPrincipal();

        if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
        {
            var data = header[0];
            var decoded = Convert.FromBase64String(data);
            var json = Encoding.ASCII.GetString(decoded);
            principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
        }

        principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

        if (!principal.UserRoles?.Any() ?? true)
        {
            return new ClaimsPrincipal();
        }

        var identity = new ClaimsIdentity(principal.IdentityProvider);
        identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
        identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
        identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

        return new ClaimsPrincipal(identity);
    }
  }
```

---

<sup>1</sup> [Fetch](https://caniuse.com/#feat=fetch) API ve [await](https://caniuse.com/#feat=mdn-javascript_operators_await) işleci Internet Explorer 'da desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama ayarlarını yapılandırma](application-settings.md)

---
title: Kimlik doğrulaması belirtecini önbelleğe alma
titleSuffix: Azure Cognitive Services
description: Bu makalede, kimlik doğrulama belirtecinin nasıl önbelleğe alınacağını gösterilecektir.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: metang
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e79ae3914e32038e2823fb37e3eee658c95e0003
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608776"
---
# <a name="how-to-cache-the-authentication-token"></a>Kimlik doğrulama belirtecini önbelleğe alma

Bu makalede, uygulamanızın performansını artırmak için kimlik doğrulama belirtecinin nasıl önbelleğe alınacağını gösterilmektedir.

## <a name="using-aspnet"></a>ASP.NET kullanma

Bir belirteci almak için kullanılan **Microsoft. IdentityModel. clients. ActiveDirectory** NuGet paketini içeri aktarın. Daha sonra, `AuthenticationResult` [tam ekran okuyucu kaynağını](./how-to-create-immersive-reader.md)oluştururken aldığınız kimlik doğrulama değerlerini kullanarak bir elde etmek için aşağıdaki kodu kullanın.

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

`AuthenticationResult`Nesnesi, `AccessToken` SDK 'Yı kullanarak derinlikli okuyucu başlatılırken kullanacağınız gerçek belirteç olan bir özelliğine sahiptir. Ayrıca `ExpiresOn` belirtecin ne zaman sona ereceğini belirten bir özelliğine sahiptir. Tam ekran okuyucuyu başlatmadan önce belirtecin süresi dolup dolmadığını denetleyebilir ve yalnızca süresi dolmuşsa yeni bir belirteç elde edebilirsiniz.

## <a name="using-nodejs"></a>Node.JS kullanma

[**İstek**](https://www.npmjs.com/package/request) NPM paketini projenize ekleyin. [Tam ekran okuyucu kaynağını](./how-to-create-immersive-reader.md)oluştururken aldığınız kimlik doğrulama değerlerini kullanarak bir belirteci almak için aşağıdaki kodu kullanın.

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

`expires_on`Özelliği, belirtecin süresinin dolacağı tarih ve saat olarak 1 ocak 1970 ' den bu yana saniye sayısı olarak ifade edilir. Yeni bir tane edinmeyi denemeden önce belirtecinizin süresi dolup gösterilmeyeceğini öğrenmek için bu değeri kullanın.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Tam ekran okuyucu SDK başvurusunu](./reference.md) keşfet
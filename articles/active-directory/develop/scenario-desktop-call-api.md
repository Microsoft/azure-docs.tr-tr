---
title: Bir masaüstü uygulamasından Web API 'Lerini çağırma | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir masaüstü uygulaması oluşturmayı öğrenin
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b5b52b679506a5c8b4d183c9ad5925c20238621c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798927"
---
# <a name="desktop-app-that-calls-web-apis-call-a-web-api"></a>Web API 'Lerini çağıran masaüstü uygulaması: Web API 'SI çağırma

Artık bir belirteciniz olduğuna göre, korumalı bir Web API 'SI çağırabilirsiniz.

## <a name="call-a-web-api"></a>Web API çağrısı

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="macos"></a>[MacOS](#tab/macOS)

## <a name="call-a-web-api-in-msal-for-ios-and-macos"></a>İOS ve macOS için MSAL içinde bir Web API 'SI çağırma

Belirteçleri elde etmek için yöntemler bir `MSALResult` nesne döndürür. `MSALResult``accessToken`bir Web API 'si çağırmak için kullanılabilecek bir özellik sunar. Korumalı Web API 'sine erişmek için çağrıyı yapmadan önce HTTP yetkilendirme üstbilgisine bir erişim belirteci ekleyin.

Amaç-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };

NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

SWIFT

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="call-several-apis-incremental-consent-and-conditional-access"></a>Çeşitli API 'Leri çağırma: artımlı izin ve koşullu erişim

Aynı kullanıcı için çeşitli API 'Leri çağırmak için, ilk API için bir belirteç aldıktan sonra çağrısı yapın `AcquireTokenSilent` . Diğer API 'Ler için çoğu zaman sessizce bir belirteç alacaksınız.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Şu durumlarda etkileşim gerekir:

- Kullanıcı ilk API 'ye onay verdi, ancak şimdi daha fazla kapsam için onay gerektirir. Bu tür bir onay, artımlı izin olarak bilinir.
- İlk API, çok faktörlü kimlik doğrulaması gerektirmez, ancak bir sonraki tane.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Axios](https://www.npmjs.com/package/axios)gıbı bir http istemcisi kullanarak, *Yetkilendirme taşıyıcı* olarak bir erişim BELIRTECIYLE API uç noktası URI 'sini çağırın.

```javascript
const axios = require('axios');

async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

```

<!--
More includes will come later for Python and Java
-->
# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki sonraki makaleye geçin, [üretime geçin](scenario-desktop-production.md).

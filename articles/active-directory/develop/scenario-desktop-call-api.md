---
title: Web API 'Lerini çağıran masaüstü uygulaması (bir Web API 'SI çağırma)-Microsoft Identity platform
description: Web API 'Leri çağıran bir masaüstü uygulaması oluşturmayı öğrenin (Web API 'SI çağırma)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8160ec489f891764b102b5ba23a687b53376f738
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175366"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Web API 'Lerini çağıran masaüstü uygulaması-bir Web API 'SI çağırma

Artık bir belirteciniz olduğuna göre, korumalı bir Web API 'SI çağırabilirsiniz.

## <a name="calling-a-web-api"></a>Web API'sini çağırma

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>İOS ve macOS için MSAL içinde bir Web API 'SI çağırma

Belirteçleri elde etmek için yöntemler bir `MSALResult` nesnesi döndürür. `MSALResult`, bir Web API 'sini çağırmak için kullanılabilen bir `accessToken` özelliğini kullanıma sunar. Korunan Web API 'sine erişmek için çağrı yapılmadan önce, erişim belirtecinin HTTP yetkilendirme üstbilgisine eklenmesi gerekir.

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

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Çeşitli API 'Leri çağırma-artımlı onay ve koşullu erişim

Aynı kullanıcı için birden çok API çağırmanız gerekiyorsa, ilk API için bir belirteç aldıktan sonra yalnızca `AcquireTokenSilent`çağırabilirsiniz ve diğer API 'Ler için çoğu zaman sessizce bir belirteç alırsınız.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Etkileşimin gerekli olduğu durumlar şunlardır:

- Kullanıcı ilk API 'yi kabul etmiş, ancak artık daha fazla kapsam için onay gerektirir (artımlı onay)
- İlk API çok faktörlü kimlik doğrulaması gerektirmez, ancak bir sonraki tane.

```CSharp
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
---

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Üretime taşı](scenario-desktop-production.md)

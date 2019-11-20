---
title: Web API 'Lerini çağıran Daemon uygulaması (Web API 'Lerini çağırma)-Microsoft Identity platform
description: Web API 'Lerini çağıran bir Daemon uygulaması derlemeyi öğrenin (Web API 'Lerini çağırma)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 8af0bb023136ba07057e4078d5abfcb4a3194a56
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175389"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Web API 'Lerini çağıran Daemon uygulaması-uygulamadan bir Web API 'SI çağırma

Bir Daemon uygulaması, .NET Daemon uygulamasından bir Web API 'si çağırabilir veya önceden onaylanmış birkaç Web API 'si çağırabilir.

## <a name="calling-a-web-api-daemon-application"></a>Web API Daemon uygulaması çağırma

Bir API 'yi çağırmak için belirteci kullanma

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

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

---

## <a name="calling-several-apis"></a>Çeşitli API 'Ler çağırma

Daemon uygulamaları için, çağırdığınız Web API 'Lerinin önceden onaylanmış olması gerekir. Daemon uygulamalarında artımlı izin yoktur (Kullanıcı etkileşimi yoktur). Kiracı yöneticisinin uygulamayı ve tüm API izinlerini önceden onaylaması gerekir. Çeşitli API 'Ler çağırmak isterseniz, her bir kaynak için `AcquireTokenForClient` her seferinde bir belirteç edinmeniz gerekir. MSAL, gereksiz hizmet çağrılarını önlemek için uygulama belirteci önbelleğini kullanır.

## <a name="next-steps"></a>Sonraki adımlar

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon uygulaması-üretime taşı](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon uygulaması-üretime taşı](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon uygulaması-üretime taşı](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---

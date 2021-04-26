---
title: Daemon uygulamasından bir Web API 'SI çağırma | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'sini çağıran bir Daemon uygulaması derlemeyi öğrenin.
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
ms.openlocfilehash: f4b223c9195168b445b7eb81c2709a61807fddac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578405"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Web API 'Lerini çağıran Daemon uygulaması-uygulamadan bir Web API 'SI çağırma

.NET Daemon uygulamaları, bir Web API 'SI çağırabilir. .NET Daemon uygulamaları, önceden onaylanmış birkaç Web API 'Sini de çağırabilir.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Bir Daemon uygulamasından Web API 'SI çağırma

Bir API 'yi çağırmak için belirtecin nasıl kullanılacağı aşağıda verilmiştir:

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

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Axios](https://www.npmjs.com/package/axios)gıbı bir http istemcisi kullanarak, *Yetkilendirme taşıyıcının* bir erişim BELIRTECIYLE API uç noktası URI 'sini çağırın.

```JavaScript
const axios = require('axios');

async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

---

## <a name="calling-several-apis"></a>Çeşitli API 'Ler çağırma

Daemon uygulamaları için, çağırdığınız Web API 'Lerinin önceden onaylanmış olması gerekir. Daemon uygulamalarıyla artımlı izin yoktur. (Kullanıcı etkileşimi yoktur.) Kiracı yöneticisinin, uygulama ve tüm API izinleri için önceden onay sağlaması gerekir. Çeşitli API 'Ler çağırmak isterseniz her bir kaynak için her seferinde bir belirteç alın `AcquireTokenForClient` . MSAL, gereksiz hizmet çağrılarını önlemek için uygulama belirteci önbelleğini kullanır.

## <a name="next-steps"></a>Sonraki adımlar

# <a name="net"></a>[.NET](#tab/dotnet)

Bu senaryodaki sonraki makaleye geçin, [üretime geçin](./scenario-daemon-production.md?tabs=dotnet).

# <a name="java"></a>[Java](#tab/java)

Bu senaryodaki sonraki makaleye geçin, [üretime geçin](./scenario-daemon-production.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu senaryodaki sonraki makaleye geçin, [üretime geçin](./scenario-daemon-production.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Bu senaryodaki sonraki makaleye geçin, [üretime geçin](./scenario-daemon-production.md?tabs=python).

---

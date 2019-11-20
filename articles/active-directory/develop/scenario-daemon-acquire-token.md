---
title: Web API 'Lerini çağıran Daemon uygulaması (uygulama belirteçleri alınıyor)-Microsoft Identity platform
description: Web API 'Lerini çağıran bir Daemon uygulamasının nasıl oluşturulduğunu öğrenin (belirteçler alınıyor)
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
ms.openlocfilehash: 4a5a3ac1438d5e958317f1899fc6c447f5c149ac
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175524"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Web API 'Lerini çağıran Daemon uygulaması-belirteç alma

Gizli istemci uygulaması oluşturulduktan sonra, ``AcquireTokenForClient`` çağırarak, kapsamı geçirerek ve belirteci yenilemeyi zorlayarak ve değil, uygulama için bir belirteç elde edebilirsiniz.

## <a name="scopes-to-request"></a>İstek için kapsamlar

İstemci kimlik bilgisi akışı için istenen kapsam, kaynağın adı ve ardından `/.default`. Bu gösterim, Azure AD 'nin uygulama kaydı sırasında statik olarak belirtilen **uygulama düzeyi izinlerini** kullanmasını söyler. Ayrıca, daha önce görüldüğü gibi, bu API izinlerinin bir kiracı yöneticisi tarafından verilmesi gerekir

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL Python 'da, yapılandırma dosyası aşağıdaki kod parçacığı gibi görünür:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Azure AD (v 1.0) kaynaklarının durumu

İstemci kimlik bilgileri için kullanılan kapsamın her zaman RESOURCEID + "/.exe" olması gerekir

> [!IMPORTANT]
> Bir v 1.0 erişim belirtecini kabul eden bir kaynak için erişim belirteci isteyen MSAL için, Azure AD, son eğik çizgiden önce her şeyi alarak ve bunu kaynak tanımlayıcısı olarak kullanarak istenen kapsamdaki hedef kitleyi ayrıştırır.
> Bu nedenle, Azure SQL ( **https://database.windows.net** ) gibi kaynak, eğik çizgi ile biten bir izleyiciyi beklediğinde (Azure sql: `https://database.windows.net/` ), `https://database.windows.net//.default` kapsamını istemeniz gerekir (çift eğik çizgiyi aklınızda bulunan). Ayrıca bkz. MSAL.NET sorun [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): kaynak URL 'sinin sondaki eğik çizgi atlandığından SQL kimlik doğrulama hatasına neden olur.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API 'SI

Uygulama için bir belirteç almak üzere platformları bağlı olarak `AcquireTokenForClient` veya eşdeğerini kullanacaksınız.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

Bu, [msal Java dev örneklerinden](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)bir ayıklandır.

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protokol

Seçtiğiniz diliniz için henüz bir kitaplığınız yoksa, protokolü doğrudan kullanmak isteyebilirsiniz:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>İlk durum: paylaşılan gizli dizi ile belirteç isteğine erişin

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>İkinci durum: bir sertifikayla erişim belirteci isteği

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Daha fazla bilgi için bkz. protokol belgeleri: [Microsoft Identity platform ve OAuth 2,0 istemci kimlik bilgileri akışı](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Uygulama belirteci önbelleği

`AcquireTokenForClient`, **uygulama belirteci önbelleğini** kullanır (tüm diğer AcquireTokenXX yöntemleri kullanıcı belirteci önbelleğini kullanır), `AcquireTokenSilent` **Kullanıcı** belirteci önbelleğini kullandığından `AcquireTokenForClient` çağrılmadan önce `AcquireTokenSilent` çağırmaz. `AcquireTokenForClient`, **uygulama** belirteci önbelleğinin kendisini denetler ve güncelleştirir.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="did-you-use-the-resourcedefault-scope"></a>Resource/. Default kapsamını mı kullanıyorsunuz?

Geçersiz bir kapsam kullandığını söyleyen bir hata mesajı alırsanız, büyük olasılıkla `resource/.default` kapsamını kullanmadınız.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Yönetici onayı sağlamayı unuttunuz mu? Daemon uygulamalarında şunlar gerekir!

API 'YI çağırırken **işlemi tamamlamaya yönelik ayrıcalıklar yetersizse**bir hata alırsanız, kiracı yöneticisinin uygulamaya izin vermesi gerekir. Yukarıdaki istemci uygulamasını kaydettirme adım 6 ' ya bakın.
Genellikle aşağıdaki hata açıklamasına benzer bir hata görürsünüz:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon uygulaması-bir Web API 'SI çağırma](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon uygulaması-bir Web API 'SI çağırma](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon uygulaması-bir Web API 'SI çağırma](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---

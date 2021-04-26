---
title: Bir Web API 'SI (daemon uygulaması) çağırmak için belirteçleri alma-Microsoft Identity platform | Mavisi
description: Web API 'Lerini çağıran bir Daemon uygulamasının nasıl oluşturulduğunu öğrenin (belirteçler alınıyor)
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
ms.openlocfilehash: 19ead7fe063992e95588641f7fd739081cf54a2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578422"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Web API 'Lerini çağıran Daemon uygulaması-belirteç alma

Gizli bir istemci uygulaması oluşturduktan sonra, uygulamayı çağırarak `AcquireTokenForClient` , kapsamı geçirerek ve isteğe bağlı olarak belirtecin yenilenmesini zorlayarak uygulama için bir belirteç elde edebilirsiniz.

## <a name="scopes-to-request"></a>İstek için kapsamlar

İstemci kimlik bilgisi akışı için istenen kapsam, kaynağın ve sonrasında gelen addır `/.default` . Bu gösterim Azure Active Directory (Azure AD) uygulama kaydı sırasında statik olarak belirtilen *uygulama düzeyi izinleri* kullanmasını söyler. Ayrıca, bu API izinlerinin bir kiracı yöneticisi tarafından verilmesi gerekir.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```JavaScript
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'], // e.g. 'https://graph.microsoft.com/.default'
};
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 'da, yapılandırma dosyası şu kod parçacığına benzer şekilde görünür:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD (v 1.0) kaynakları

İstemci kimlik bilgileri için kullanılan kapsam, her zaman kaynak KIMLIĞI ve sonrasında gelmelidir `/.default` .

> [!IMPORTANT]
> MSAL, sürüm 1,0 erişim belirtecini kabul eden bir kaynak için bir erişim belirteci istediğinde, Azure AD, son eğik çizgiden önce her şeyi alarak ve bunu kaynak tanımlayıcısı olarak kullanarak istenen kapsamdaki hedef kitleyi ayrıştırır.
> Bu nedenle, Azure SQL veritabanı (**https: \/ /Database.Windows.net**) gibi, kaynak bir eğik ÇIZGI (Azure SQL veritabanı için) ile biten bir hedef kitle beklediğinde `https://database.windows.net/` , bir kapsamını istemeniz gerekir `https://database.windows.net//.default` . (Çift eğik çizgiye göz önünde edin.) Ayrıca bkz. MSAL.NET sorun [#747: kaynak URL 'sinin sondaki eğik çizgi atlandığından SQL kimlik doğrulama hatasına neden olur](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API 'SI

Uygulamanın bir belirtecini almak için `AcquireTokenForClient` platforma bağlı olarak veya eşdeğerini kullanacaksınız.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

### <a name="acquiretokenforclient-uses-the-application-token-cache"></a>AcquireTokenForClient uygulama belirteci önbelleğini kullanır

MSAL.NET ' de, `AcquireTokenForClient` uygulama belirteci önbelleğini kullanır. (Tüm diğer AcquireToken *xx* yöntemleri kullanıcı belirteci önbelleğini kullanır.) `AcquireTokenSilent` Çağrısı yapmadan önce çağrı yapmayın `AcquireTokenForClient` , çünkü `AcquireTokenSilent` *Kullanıcı* belirteci önbelleğini kullanır. `AcquireTokenForClient`*uygulama* belirteci önbelleğinin kendisini denetler ve güncelleştirir.

# <a name="java"></a>[Java](#tab/java)

Bu kod, [msal Java dev örneklerinden](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)ayıklanır.

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Aşağıdaki kod parçacığı, MSAL node gizli istemci uygulamasındaki belirteç alımını gösterir:

```JavaScript
try {
    const authResponse = await cca.acquireTokenByClientCredential(tokenRequest);
    console.log(authResponse.accessToken) // display access token
} catch (error) {
    console.log(error);
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

---

### <a name="protocol"></a>Protokol

Seçtiğiniz dil için henüz bir kitaplığınız yoksa, protokolü doğrudan kullanmak isteyebilirsiniz:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>İlk durum: paylaşılan bir gizli dizi kullanarak belirteç isteğine erişin

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>İkinci durum: bir sertifika kullanarak belirteç isteğine erişin

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Daha fazla bilgi için bkz. protokol belgeleri: [Microsoft Identity platform ve OAuth 2,0 istemci kimlik bilgileri akışı](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Sorun giderme

### <a name="did-you-use-the-resourcedefault-scope"></a>Resource/. Default kapsamını mı kullanıyorsunuz?

Geçersiz bir kapsam kullanacağınızı söyleyen bir hata mesajı alırsanız, büyük olasılıkla kapsamı kullanmadınız `resource/.default` .

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Yönetici onayı sağlamayı unuttunuz mu? Daemon uygulamalarında şunlar gerekir!

API 'yi çağırdığınızda **işlem hatasını tamamlamaya yetecek ayrıcalıklara** sahipseniz, kiracı yöneticisinin uygulamaya izin vermesi gerekir. Yukarıdaki istemci uygulamasını kaydettirme adım 6 ' ya bakın.
Genellikle şu hata gibi görünen bir hata görürsünüz:

```json
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

### <a name="are-you-calling-your-own-api"></a>Kendi API 'nizi mi arıyorsunuz?

Kendi Web API 'nizi çağırırsanız ve arka plan uygulamanız için uygulama kaydına bir uygulama izni ekleyemediyseniz, Web API 'niz üzerinde bir uygulama rolü sergiledınız mı?

Ayrıntılar için bkz. [uygulama Izinlerini gösterme (uygulama rolleri)](scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) ve özellikle de [Azure AD 'nın, Web API 'niz için belirteçleri yalnızca izin verilen Istemcilere olarak görmesini sağlama](scenario-protected-web-api-app-registration.md#ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients).

## <a name="next-steps"></a>Sonraki adımlar

# <a name="net"></a>[.NET](#tab/dotnet)

Bu senaryonun [bir Web API 'Sini çağırarak bir](./scenario-daemon-call-api.md?tabs=dotnet)sonraki makaleye geçin.

# <a name="java"></a>[Java](#tab/java)

Bu senaryonun [bir Web API 'Sini çağırarak bir](./scenario-daemon-call-api.md?tabs=java)sonraki makaleye geçin.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu senaryonun [bir Web API 'Sini çağırarak bir](./scenario-daemon-call-api.md?tabs=nodejs)sonraki makaleye geçin.

# <a name="python"></a>[Python](#tab/python)

Bu senaryonun [bir Web API 'Sini çağırarak bir](./scenario-daemon-call-api.md?tabs=python)sonraki makaleye geçin.

---

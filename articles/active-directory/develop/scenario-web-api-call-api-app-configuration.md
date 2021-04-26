---
title: Web API 'Lerini çağıran bir Web API 'SI yapılandırma | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini (uygulamanın kod yapılandırması) çağıran bir Web API 'SI oluşturmayı öğrenin
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5072ae58d3a9412237e70a9bc98970296ce1e1fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686589"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Web API 'Leri çağıran bir Web API 'SI: kod yapılandırması

Web API 'nizi kaydettikten sonra, uygulamanın kodunu yapılandırabilirsiniz.

Web API 'nizi, bir Web API 'sini korumak için kullanılan kodun üstüne, akış Web API 'Lerini çağıran şekilde yapılandırmak için kullandığınız kod. Daha fazla bilgi için bkz. [korumalı Web API 'si: uygulama yapılandırması](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

Microsoft, aşağı akış Web API 'Lerini çağıran ASP.NET Core korumalı bir API geliştirirken [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet paketini kullanmanızı önerir. Bkz [. korumalı Web API 'si: kod yapılandırması | ](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) Bir Web API 'si bağlamında söz konusu kitaplığın hızlı bir sunumu için Microsoft. Identity. Web.

## <a name="client-secrets-or-client-certificates"></a>İstemci parolaları veya istemci sertifikaları

Web API 'niz artık bir aşağı akış Web API 'sini çağırdığı için, dosyasında *appsettings.js* bir istemci parolası veya istemci sertifikası sağlayın. Aşağıdakileri belirten bir bölüm de ekleyebilirsiniz:

- Aşağı akış Web API 'sinin URL 'si
- API 'YI çağırmak için gereken kapsamlar

Aşağıdaki örnekte, `GraphBeta` bölümü bu ayarları belirtir.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

İstemci parolası yerine bir istemci sertifikası sağlayabilirsiniz. Aşağıdaki kod parçacığı, Azure Key Vault depolanan bir sertifikanın kullanımını gösterir.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft. Identity. Web, sertifikaları yapılandırma veya koda göre tanımlamaya yönelik çeşitli yollar sağlar. Ayrıntılar için bkz. [Microsoft. Identity. Web wiki-GitHub 'da sertifikaları kullanma](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) .

## <a name="startupcs"></a>Startup.cs

Web API 'nizin aşağı akış API 'SI için bir belirteç edinmesi gerekir. `.EnableTokenAcquisitionToCallDownstreamApi()`Sonra satırı ekleyerek belirlersiniz `.AddMicrosoftIdentityWebApi(Configuration)` . Bu satır `ITokenAcquisition` , denetleyici/sayfalar eylemlerinizde kullanabileceğiniz hizmeti sunar. Bununla birlikte, sonraki iki madde işareti noktasında göreceğiniz gibi daha da kolay hale getirebilirsiniz. Ayrıca, bir belirteç önbelleği uygulamasını (örneğin `.AddInMemoryTokenCaches()` , *Startup. cs*) seçmeniz gerekir:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Belirteci kendiniz almak istemiyorsanız, *Microsoft. Identity. Web* başka bir API 'den bir aşağı akış Web API 'si çağırmak için iki mekanizma sağlar. Seçtiğiniz seçenek, Microsoft Graph veya başka bir API 'yi çağırmak isteyip istemediğinize bağlıdır.

### <a name="option-1-call-microsoft-graph"></a>Seçenek 1: çağrı Microsoft Graph

Microsoft Graph çağırmak isterseniz, Microsoft. Identity. Web, `GraphServiceClient` API eylemlerinizin (MICROSOFT Graph SDK tarafından açığa çıkarılan) doğrudan kullanmanıza olanak sağlar. Microsoft Graph ortaya çıkarmak için:

1. Projenize [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet paketini ekleyin.
1. `.AddMicrosoftGraph()` `.EnableTokenAcquisitionToCallDownstreamApi()` *Başlangıç. cs* dosyasında öğesinden sonra ekleyin. `.AddMicrosoftGraph()` birkaç geçersiz kılma içerir. Bir yapılandırma bölümünü parametre olarak alan geçersiz kılmayı kullanarak, kod şu şekilde olur:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>2. seçenek: Microsoft Graph dışında bir aşağı akış Web API 'SI çağırma

Microsoft Graph dışında bir aşağı akış API 'SI çağırmak için, *Microsoft. Identity. Web* ' `.AddDownstreamWebApi()` i ve bu sayede, bir aşağı akış Web API 'sine çağrı ister.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Web uygulamalarında olduğu gibi, çeşitli belirteç önbelleği uygulamalarını seçebilirsiniz. Ayrıntılar için bkz. GitHub 'da [Microsoft Identity Web belirteci önbelleği serileştirme](https://aka.ms/ms-id-web/token-cache-serialization) .

Aşağıdaki görüntüde *Microsoft. Identity. Web* 'in çeşitli olanakları ve *Başlangıç. cs* dosyasındaki etkileri gösterilmektedir:

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Bir Web API 'SI çağırmak ve bir belirteç önbelleği uygulamasını belirtmek için başlangıç noktası C 'ler içindeki hizmet yapılandırma seçeneklerini gösteren blok diyagramı":::

> [!NOTE]
> Kod örneklerini burada tam olarak anlamak için [ASP.NET Core temelleri](/aspnet/core/fundamentals)hakkında bilgi sahibi olun ve özellikle de [bağımlılık ekleme](/aspnet/core/fundamentals/dependency-injection) ve [seçenekleriyle](/aspnet/core/fundamentals/configuration/options)ilgili bilgi edinin.

# <a name="java"></a>[Java](#tab/java)

Şirket adına (OBO) akışı, aşağı akış Web API 'sini çağırmak için bir belirteç almak üzere kullanılır. Bu akışta, Web API 'niz, istemci uygulamasından Kullanıcı temsilcisi izinleri olan bir taşıyıcı belirteç alır ve ardından bu belirteci başka bir erişim belirteci için aşağı akış Web API 'sini çağırmak üzere değiş tokuş eder.

Aşağıdaki kod, `SecurityContextHolder` doğrulanan taşıyıcı belirtecini almak için Web API 'Sindeki Spring Security Framework 'ü kullanır. Daha sonra, ile çağrısını kullanarak bir aşağı akış API belirteci almak için MSAL Java kitaplığını kullanır `acquireToken` `OnBehalfOfParameters` . MSAL, API 'ye yapılan sonraki çağrıların `acquireTokenSilently` önbelleğe alınmış belirteci almak için kullanabilmesi için belirteci önbelleğe alır.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Şirket adına (OBO) akışı, aşağı akış Web API 'sini çağırmak için bir belirteç almak üzere kullanılır. Bu akışta, Web API 'niz, istemci uygulamasından Kullanıcı temsilcisi izinleri olan bir taşıyıcı belirteç alır ve ardından bu belirteci başka bir erişim belirteci için aşağı akış Web API 'sini çağırmak üzere değiş tokuş eder.

Bir Python Web API 'sinin istemciden alınan taşıyıcı belirtecini doğrulamak için bazı ara yazılım kullanması gerekir. Web API 'si daha sonra yöntemi çağırarak MSAL Python kitaplığı kullanarak aşağı akış API 'sine yönelik erişim belirtecini alabilir [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . Bu API 'nin kullanımıyla ilgili bir örnek için bkz. [GitHub 'da Microsoft-Authentication-Library-for-Python için test kodu](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Ayrıca, orta katman uygulama ihtiyacını atlayan bir yaklaşım için aynı depodaki [53 sorun](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) hakkındaki tartışmaya bakın.

Ayrıca, örneğin, [MS-Identity-Python-on---](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of) ------------------

---

Ayrıca, [Node.js ve Azure işlevlerinde](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61)OBO akış uygulamasına bir örnek de görebilirsiniz.

## <a name="protocol"></a>Protokol

OBO protokolü hakkında daha fazla bilgi için bkz. [Microsoft Identity platform ve OAuth 2,0-adına akışı](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki bir sonraki makaleye geçin, [uygulama için bir belirteç alın](scenario-web-api-call-api-acquire-token.md).

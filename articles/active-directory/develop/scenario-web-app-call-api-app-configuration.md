---
title: Web API 'Lerini çağıran bir Web uygulaması yapılandırma | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir Web uygulamasının kodunu yapılandırmayı öğrenin
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: aa377547f7f4961e199ec8d62bf0f1435296f983
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669313"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Web API 'Leri çağıran bir Web uygulaması: kod yapılandırması

Web uygulaması, kullanıcılar senaryosunda oturum [açan Web](scenario-web-app-sign-user-overview.md) uygulamasında gösterildiği gibi, Kullanıcı oturumu açmak için [OAuth 2,0 yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md) kullanır. Bu akışta iki adım vardır:

1. Yetkilendirme kodu isteyin. Bu bölüm, kullanıcıyla Microsoft Identity platformu için özel bir iletişim kutusu devreder. Bu iletişim kutusunda, Kullanıcı oturum açar ve Web API 'Leri kullanımına izin verir. Özel iletişim kutusu başarıyla sona erdiğinde, Web uygulaması, yeniden yönlendirme URI 'sinde bir yetkilendirme kodu alır.
1. Yetkilendirme kodunu benimseerek API için bir erişim belirteci isteyin.

[Kullanıcı senaryolarında oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md) yalnızca ilk adımın kapsamına alınır. Burada, Web uygulamanızı yalnızca içindeki kullanıcıları imzaladığında, ancak artık Web API 'Lerini çağıran bir şekilde nasıl değiştireceğiniz hakkında bilgi edineceksiniz.

## <a name="microsoft-libraries-supporting-web-apps"></a>Web uygulamalarını destekleyen Microsoft kitaplıkları

Aşağıdaki Microsoft kitaplıkları Web uygulamalarını destekler:

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

İlgilendiğiniz platformun sekmesini seçin:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>İstemci parolaları veya istemci sertifikaları

Web uygulamanız artık bir aşağı akış Web API 'sini çağırdığı için, dosyasında *appsettings.js* bir istemci parolası veya istemci sertifikası sağlayın. Aşağıdakileri belirten bir bölüm de ekleyebilirsiniz:

- Aşağı akış Web API 'sinin URL 'si
- API 'YI çağırmak için gereken kapsamlar

Aşağıdaki örnekte, `GraphBeta` bölümü bu ayarları belirtir.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
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
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
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

*Microsoft. Identity. Web* , sertifikaları yapılandırma veya koda göre tanımlamaya yönelik çeşitli yollar sağlar. Ayrıntılar için bkz. GitHub 'da [Microsoft. Identity. Web-using sertifikaları](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) .

## <a name="startupcs"></a>Startup.cs

Web uygulamanızın aşağı akış API 'SI için bir belirteç edinmesi gerekir. `.EnableTokenAcquisitionToCallDownstreamApi()`Sonra satırı ekleyerek belirlersiniz `.AddMicrosoftIdentityWebApi(Configuration)` . Bu satır, `ITokenAcquisition` denetleyicinizde ve sayfa eylemlerinde kullanabileceğiniz hizmeti sunar. Ancak, aşağıdaki iki seçenekten de göreceğiniz gibi daha basit bir şekilde yapılabilir. Ayrıca, bir belirteç önbelleği uygulamasını (örneğin `.AddInMemoryTokenCaches()` , *Startup. cs*) seçmeniz gerekir:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

' Ye geçirilen kapsamlar `EnableTokenAcquisitionToCallDownstreamApi` isteğe bağlıdır ve oturum açtıklarında Web uygulamanızın kapsamları ve kullanıcının iznini bu kapsamlara istemesini sağlar. Kapsamları belirtmezseniz, *Microsoft. Identity. Web* , artımlı bir onay deneyimini etkinleştirir.

Belirteci kendiniz almak istemiyorsanız, *Microsoft. Identity. Web* bir Web uygulamasından Web API 'si çağırmak için iki mekanizma sağlar. Seçtiğiniz seçenek, Microsoft Graph veya başka bir API 'yi çağırmak isteyip istemediğinize bağlıdır.

### <a name="option-1-call-microsoft-graph"></a>Seçenek 1: çağrı Microsoft Graph

Microsoft Graph çağırmak isterseniz, *Microsoft. Identity. Web* , `GraphServiceClient` API eylemlerinizin (Microsoft Graph SDK tarafından açığa çıkarılan) doğrudan kullanmanıza olanak sağlar. Microsoft Graph ortaya çıkarmak için:

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
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>2. seçenek: Microsoft Graph dışında bir aşağı akış Web API 'SI çağırma

Microsoft Graph dışındaki bir Web API 'sini çağırmak için, *Microsoft. Identity. Web* ' `.AddDownstreamWebApi()` i tarafından sağlanan ve aşağı akış Web API 'sini çağıran.

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>Özet

Web API 'Lerinde olduğu gibi çeşitli belirteç önbelleği uygulamaları da seçebilirsiniz. Ayrıntılar için bkz. GitHub 'da [Microsoft. Identity. Web-Token Cache serileştirme](https://aka.ms/ms-id-web/token-cache-serialization) .

Aşağıdaki görüntüde *Microsoft. Identity. Web* 'in çeşitli olanakları ve *Başlangıç. cs* dosyasındaki etkileri gösterilmektedir:

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Bir Web API 'SI çağırmak ve bir belirteç önbelleği uygulamasını belirtmek için başlangıç noktası C 'ler içindeki hizmet yapılandırma seçeneklerini gösteren blok diyagramı":::

> [!NOTE]
> Kod örneklerini burada tam olarak anlamak için [ASP.NET Core temelleri](/aspnet/core/fundamentals)hakkında bilgi sahibi olun ve özellikle de [bağımlılık ekleme](/aspnet/core/fundamentals/dependency-injection) ve [seçenekleriyle](/aspnet/core/fundamentals/configuration/options)ilgili bilgi edinin.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kullanıcı oturum açma, OpenID Connect (OıDC) ara yazılımı için Temsilcili olduğundan, OıDC işlemiyle etkileşimde bulunmalısınız. Etkileşim kurma, kullandığınız çerçeveye bağlıdır.

ASP.NET için, ara yazılım OıDC olaylarına abone olacaksınız:

- Açık KIMLIK Connect ara yazılımı aracılığıyla ASP.NET Core bir yetkilendirme kodu istemesini sağlayabilirsiniz. ASP.NET veya ASP.NET Core, kullanıcının oturum açmasını ve onayını sağlar.
- Yetkilendirme kodunu almak için Web uygulamasına abone olacaksınız. Bu abonelik bir C# temsilcisi kullanılarak yapılır.
- Yetkilendirme kodu alındığında, kullanmak için MSAL kitaplıklarını kullanırsınız. Elde edilen erişim belirteçleri ve yenileme belirteçleri, belirteç önbelleğinde depolanır. Önbellek, diğer belirteçleri sessizce elde etmek için, örneğin denetleyiciler gibi uygulamanın diğer bölümlerinde kullanılabilir.

Bu makaledeki kod örnekleri ve aşağıdakiler [ASP.NET Web uygulaması örneğinden](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)ayıklanır. Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

# <a name="java"></a>[Java](#tab/java)

Bu makaledeki kod örnekleri ve aşağıdaki bir örnek, Java için MSAL kullanan bir Web uygulaması örneği olan [Microsoft Graph çağıran Java Web uygulamasından](https://github.com/Azure-Samples/ms-identity-java-webapp)ayıklanır.
Örnek şu anda Java için MSAL 'in yetkilendirme kodu URL 'sini oluşturmasını sağlar ve Microsoft Identity platform için yetkilendirme uç noktasına gezinmeyi işler. Sprint Security 'yi ' de Kullanıcı imzalamak üzere kullanmak da mümkündür. Tam uygulama ayrıntıları için örneğe başvurmak isteyebilirsiniz.

# <a name="python"></a>[Python](#tab/python)

Bu makaledeki kod örnekleri ve aşağıdaki bir örnek, MSAL kullanan bir Web uygulaması örneği olan [Microsoft Graph çağıran Python web uygulamasından](https://github.com/Azure-Samples/ms-identity-python-webapp)ayıklanır. Python.
Örnek şu anda MSAL sağlar. Python, yetkilendirme kodu URL 'sini üretir ve Microsoft Identity platform için yetkilendirme uç noktasına gezinmeyi işler. Tam uygulama ayrıntıları için örneğe başvurmak isteyebilirsiniz.

---

## <a name="code-that-redeems-the-authorization-code"></a>Yetkilendirme kodunu kullanan kod

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web, doğru OpenID Connect ayarlarını ayarlayarak, alınan kod olayına abone olmak ve kodu benimseerek kodunuzu basitleştirir. Yetkilendirme kodunu kullanmak için ek kod gerekmez. Bunun nasıl çalıştığına ilişkin ayrıntılar için bkz. [Microsoft. Identity. Web kaynak kodu](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET \Startup.Auth.cs dosyasında, OpenID Connect 'in yapılandırması ve App_Start olay aboneliği olması dışında, ASP.NET Core benzer şeyleri işler `OnAuthorizationCodeReceived` . [](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) Kavramlar, ASP.NET ' deki ASP.NET Core de benzerdir, ancak bu da ' deWeb.config#L15 belirtmeniz gerekir `RedirectUri` . [](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15) Uygulamanızı dağıtırken değiştirmeniz gerektiğinden, bu yapılandırma ASP.NET Core ' den daha az bir sağlamdır.

Başlangıç. auth. cs kodu aşağıda verilmiştir:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Bkz. [Kullanıcılar oturum açan Web uygulaması:](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) Java örneğinin yetkilendirme kodunu nasıl kullandığını anlamak için kod yapılandırma. Uygulama kodu aldıktan sonra [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. `AuthHelper.processAuthenticationCodeRedirect` [Authhelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)içindeki yöntemi devreder.
1. Çağırır `getAuthResultByAuthCode` .

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

`getAuthResultByAuthCode`Yöntemi [authhelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)içinde tanımlanır. Bir MSAL oluşturur `ConfidentialClientApplication` ve sonra `acquireToken()` `AuthorizationCodeParameters` Yetkilendirme kodundan oluşturulan ile çağırır.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

Yetkilendirme kodu akışı, [kullanıcılar tarafından oturum açan Web](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)uygulamasında gösterildiği gibi Istenir: kod yapılandırması. Daha sonra kod, `authorized` URL 'Den Flask yönlendirdiğini işlevde alınır `/getAToken` . Bu kodun tam bağlamı için bkz [. app. Sip # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) :

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Gizli istemci uygulaması, bir istemci parolası yerine, bir istemci sertifikası veya bir istemci onayı kullanarak kimliğini de kanıtlayabilirler.
İstemci onaylamaları kullanımı, [istemci onaylamaları](msal-net-client-assertions.md)hakkında ayrıntılı olarak açıklanan gelişmiş bir senaryodur.

## <a name="token-cache"></a>Belirteç önbelleği

> [!IMPORTANT]
> Web uygulamaları veya Web API 'Leri için belirteç önbelleği uygulaması, genellikle [dosya tabanlı](scenario-desktop-acquire-token.md#file-based-token-cache)olan masaüstü uygulamaları için uygulamadan farklıdır.
> Güvenlik ve performans nedenleriyle, Web uygulamaları ve Web API 'Leri için Kullanıcı hesabı başına bir belirteç önbelleği olduğundan emin olmak önemlidir. Her hesap için belirteç önbelleğini serileştirmelidir.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core öğreticisi, uygulamanızın başlangıç. cs dosyasında belirteç önbelleği uygulamasına karar vermenize olanak sağlamak için bağımlılık ekleme özelliğini kullanır. Microsoft. Identity. Web, [belirteç önbelleği serileştirme](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)bölümünde açıklanan önceden oluşturulmuş belirteç önbelleği serileştiricileri ile gelir. İlginç bir olasılık ASP.NET Core [Dağıtılmış bellek önbellekler](/aspnet/core/performance/caching/distributed#distributed-memory-cache)' ı seçerdir:

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Belirteç önbelleği sağlayıcıları hakkındaki ayrıntılar için Ayrıca bkz. Microsoft. Identity. Web 'in [belirteç önbelleği serileştirme](https://aka.ms/ms-id-web/token-cache-serialization) makalesi ve [ASP.NET Core Web uygulaması öğreticileri | ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) Web Apps öğreticisinin belirteç önbellekleri aşaması.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Web uygulamaları veya Web API 'Leri için belirteç önbelleği uygulaması, genellikle [dosya tabanlı](scenario-desktop-acquire-token.md#file-based-token-cache)olan masaüstü uygulamaları için uygulamadan farklıdır.

Web uygulaması uygulaması, ASP.NET oturumunu veya sunucu belleğini kullanabilir. Örneğin, [Msalappbuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)içinde msal.NET uygulaması oluşturulduktan sonra önbellek uygulamasının nasıl takılduğunu öğrenin:

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java, belirteç önbelleğini serileştirmek ve serisini kaldırmak için yöntemler sağlar. Java örneği, `getAuthResultBySilentFlow` [authhelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Sınıfının ayrıntıları, `SessionManagementHelper` [Java için msal örneğinde](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)verilmiştir.

# <a name="python"></a>[Python](#tab/python)

Python örneğinde, her istek için bir gizli istemci uygulaması yeniden oluşturarak ve sonra Flask oturum önbelleğinde serileştirilerek hesap başına bir önbellek gerekir:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu noktada, Kullanıcı oturum açtığında belirteç önbelleğinde bir belirteç depolanır. Daha sonra, Web uygulamasının diğer bölümlerinde nasıl kullanıldığını görelim.

[Genel oturum açma sırasında önbellekten hesapları kaldır](scenario-web-app-call-api-sign-in.md)

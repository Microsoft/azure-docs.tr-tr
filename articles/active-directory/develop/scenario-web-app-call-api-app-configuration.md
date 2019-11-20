---
title: Web API 'Lerini çağıran Web uygulaması (kod yapılandırması)-Microsoft Identity platform
description: Web API 'Lerini (uygulamanın kod yapılandırması) çağıran bir Web uygulaması oluşturmayı öğrenin
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
ms.openlocfilehash: 231ecdb6afae1fc36d11b2c12aa82c7e860bb708
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175307"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Web API 'Lerini çağıran Web uygulaması-kod yapılandırması

Web uygulaması, [Web uygulaması](scenario-web-app-sign-user-overview.md)oturum açma senaryosunda görüldüğü gibi, Kullanıcı oturumu açmak için OAuth 2.0 [Yetkilendirme kod akışını](v2-oauth2-auth-code-flow.md) kullanır. Bu akış iki bölümden oluşur:

1. Yetkilendirme kodu isteyin. Bu bölüm, Microsoft Identity platformu ile kullanıcıya özel bir iletişim kutusu devreder. Kullanıcı oturum açar ve Web API 'Leri kullanımına izin verir. Bu özel iletişim kutusu başarıyla sona erdiğinde, uygulama yeniden yönlendirme URI 'sinde bir yetkilendirme kodu alır.
1. Yetkilendirme kodunu benimseerek API için bir erişim belirteci isteyin.

[Web uygulaması oturum açan kullanıcılar senaryosu](scenario-web-app-sign-user-overview.md) yalnızca ilk bayı yapıyor. Burada, kullanıcıları oturum açan Web API 'nizi nasıl değiştireceğiniz hakkında bilgi edinin. böylece artık Web API 'lerini çağırır.

## <a name="libraries-supporting-web-app-scenarios"></a>Web uygulaması senaryolarını destekleyen kitaplıklar

Web uygulamaları için yetkilendirme kodu akışını destekleyen kitaplıklar şunlardır:

| MSAL kitaplığı | Açıklama |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Desteklenen platformlar .NET Framework ve .NET Core platformları (UWP, Xamarin. iOS ve Xamarin. Android değil, bu platformlar ortak istemci uygulamaları oluşturmak için kullanılır) |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Geliştirme devam ediyor-genel önizlemede |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Geliştirme devam ediyor-genel önizlemede |

İlgilendiğiniz platforma karşılık gelen sekmeyi seçin:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Bir Kullanıcı oturum açmaya izin vermek için, açma KIMLIĞI Connect (OıDC) ara yazılımı, OıDC işlemine bağlamak istiyorsunuz. Bunu yapmanın yolu, kullandığınız çerçeveye bağlı olarak farklılık gösteren bir yöntemdir.
ASP.NET Core durumda, ara yazılım OıDC olaylarına abone olacaksınız. Prensibi şunlardır:

- Açık KIMLIK Connect ara yazılımı aracılığıyla ASP.NET Core 'un bir yetkilendirme kodu istemesine izin vereceksiniz. Bu ASP.NET/ASP.NET çekirdeğini yaparak kullanıcının oturum açmasını ve izin vermesini sağlar,
- Web uygulaması tarafından yetkilendirme kodunun alımına abone olacaksınız. Bu abonelik bir C# temsilci aracılığıyla yapılır.
- Kimlik doğrulama kodu alındığında, kodu kullanmak için MSAL kitaplıklarını kullanacaksınız ve elde edilen erişim belirteçleri ve yenileme belirteçleri belirteç önbelleğinde depolanır. Buradan, önbellek diğer belirteçleri sessizce elde etmek için, denetleyicilerde, örneğin, uygulamanın diğer bölümlerinde kullanılabilir.

Bu makaledeki kod parçacıkları ve aşağıdakiler [ASP.NET Core Web uygulaması artımlı öğreticisi, Bölüm 2 '](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)den ayıklanır. Tam uygulama ayrıntıları için Bu öğreticiye başvurmak isteyebilirsiniz.

> [!NOTE]
> Aşağıdaki kod parçacıklarını tam olarak anlamak için [ASP.NET Core temel](https://docs.microsoft.com/aspnet/core/fundamentals)bilgileri ve belirli [bağımlılık ekleme](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) ve [seçenekleri](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) hakkında bilgi sahibi olmanız gerekir

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Bir Kullanıcı oturum açmaya izin vermek için, açma KIMLIĞI Connect (OıDC) ara yazılımı, OıDC işlemine bağlamak istiyorsunuz. Bunu yapmanın yolu, kullandığınız çerçeveye bağlı olarak farklılık gösteren bir yöntemdir.
ASP.NET durumunda, ara yazılım OıDC olaylarına abone olacaksınız. Prensibi şunlardır:

- Açık KIMLIK Connect ara yazılımı aracılığıyla ASP.NET Core 'un bir yetkilendirme kodu istemesine izin vereceksiniz. Bu ASP.NET/ASP.NET çekirdeğini yaparak kullanıcının oturum açmasını ve izin vermesini sağlar,
- Web uygulaması tarafından yetkilendirme kodunun alımına abone olacaksınız. Bu bir C# temsilcisidir.
- Kimlik doğrulama kodu alındığında, kodu kullanmak için MSAL kitaplıklarını kullanırsınız. Elde edilen erişim belirteçleri ve yenileme belirteçleri, ardından belirteç önbelleğinde depolanır. Buradan, önbellek diğer belirteçleri sessizce elde etmek için, denetleyicilerde, örneğin, uygulamanın diğer bölümlerinde kullanılabilir.

Bu makaledeki kod parçacıkları ve aşağıdakiler [ASP.NET Web uygulaması örneğinden](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)ayıklanır. Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

# <a name="javatabjava"></a>[Java](#tab/java)

Bu makaledeki kod parçacıkları ve aşağıdakiler, Microsoft Graph msal Java Web uygulaması örneğini [çağıran Java Web uygulamasından](https://github.com/Azure-Samples/ms-identity-java-webapp) ayıklanır.
Örnek şu anda MSAL Java 'nın yetkilendirme kodu URL 'sini oluşturmasını sağlar ve Microsoft Identity platform yetkilendirme uç noktası 'na gezinmeyi işler. Kullanıcı oturumu açmak için Sprint güvenliği kullanmak da mümkündür. Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

# <a name="pythontabpython"></a>[Python](#tab/python)

Bu makaledeki kod parçacıkları ve aşağıdakiler, [Microsoft Graph msal çağıran Python web uygulamasından](https://github.com/Azure-Samples/ms-identity-python-webapp) ayıklanır. Python web uygulaması örneği.
Örnek şu anda MSAL sağlar. Python, yetkilendirme kodu URL 'sini üretir ve Microsoft Identity platform yetkilendirme uç noktası 'na gezinmeyi işler. Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

---

## <a name="code-that-redeems-the-authorization-code"></a>Yetkilendirme kodunu kullanan kod

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

ASP.NET Core, ilke `Startup.cs` dosyasında olur. `OnAuthorizationCodeReceived` açma KIMLIĞI Connect olayına abone olmak ve bu olaydan MSAL çağrısı yapmak isteyeceksiniz. NET 'in, belirteç önbelleğinde depolamanın etkisi, istenen `scopes`erişim belirteci ve süresi dolma yakın olduğunda erişim belirtecini yenilemek veya aynı kullanıcı adına bir belirteç almak için kullanılacak yenileme belirteci olan `AcquireTokenFromAuthorizationCode`. , ancak farklı bir kaynak için.

Uygulama [ASP.NET Core Web uygulaması öğreticisi](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) , Web uygulamalarınız için yeniden kullanılabilir kod sağlamaya çalışır.

İşte, Web uygulamasına kimlik doğrulaması ekleyen `AddMicrosoftIdentityPlatformAuthentication` yöntemine yapılan çağrıyı sunan ve Web API 'Lerini çağırma özelliğini ekleyen `AddMsal`, [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) kodudur. `AddInMemoryTokenCaches` çağrısı, olası olanlar arasında bir belirteç önbelleği uygulamasını seçme ile ilgilidir:

```CSharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead` sabitler olarak tanımlanır [. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```CSharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Web uygulamasındaki `AddMicrosoftIdentityPlatformAuthentication` içeriğini, [Kullanıcılar-kod yapılandırması 'nda oturum açan Web](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code) uygulamasında zaten araştırdık

### <a name="the-addmsal-method"></a>AddMsal yöntemi

`AddMsal` kodu, [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)içinde bulunur.

```CSharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

`AddMsal` yöntemi şunları sağlar:

- ASP.NET Core Web uygulaması, Kullanıcı için hem ıdtoken hem de bir kimlik doğrulama kodu (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`) ister
- `offline_access` kapsamı eklenir. Kullanıcının uygulamanın bir yenileme belirteci almasına izin vermek için bu gereklidir.
- Uygulama OıDC `OnAuthorizationCodeReceived` olayına abone olur ve bu çağrıyı, burada `ITokenAcquisition`uygulayan yeniden kullanılabilir bir bileşene Kapsüllenen olan MSAL.NET kullanarak çağırır.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Tokenalımı. AddAccountToCacheFromAuthorizationCodeAsync yöntemi

`TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` yöntemi [Microsoft. Identity. Web/Tokenalımı. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)' de bulunur. Şunları sağlar:

- ASP.NET, MSAL.NET 'e paralel olarak kimlik doğrulama kodunu kullanmaya çalışmaz (`context.HandleCodeRedemption();`)
- Idtoken 'daki talepler, MSAL 'in Kullanıcı hesabı için bir belirteç önbelleği anahtarını hesaplaması için kullanılabilir
- MSAL.NET uygulaması gerekirse örneği oluşturulur
- kod MSAL.NET uygulaması tarafından kullanıldı
- Yeni KIMLIK belirteci ASP.NET Core paylaşılır (`context.HandleCodeRedemption(null, result.IdToken);`çağrısı sırasında). Erişim belirteci, ASP.NET Core paylaşılmaz. Bu, kullanıcıyla ilişkili MSAL.NET belirteç önbelleğinde kalır ve bu, ASP.NET Core denetleyicilerde kullanılmak üzere hazırlanmaktadır.

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Tokenedinme. BuildConfidentialClientApplication yöntemi

ASP.NET Core, gizli istemci uygulaması oluşturmak HttpContext 'teki bilgileri kullanır. `CurrentHttpContext` özelliğinden erişilen, istekle ilişkili HttpContext, Web uygulaması URL 'sini ve oturum açmış kullanıcıyı (bir `ClaimsPrincipal`) bilir. `BuildConfidentialClientApplication` Ayrıca, "AzureAD" bölümüne sahip olan ve ' yi ile bağlantılı ASP.NET Core yapılandırmayı kullanır:

- [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) türünün veri yapısı `_applicationOptions`
- ASP.NET Core `Authentication.AzureAD.UI`tanımlanan [Azureadoseçenekler](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) türü `azureAdOptions` örneği. Son olarak, uygulamanın belirteç önbelleklerini koruması gerekir. Sonraki bölümde bunun hakkında daha fazla bilgi edineceksiniz.

`GetOrBuildConfidentialClientApplication()` yöntemi için kod [Microsoft. Identity. Web/Tokenalımı. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333)' dir. Bağımlılık ekleme tarafından eklenen üyeleri kullanır ( [Microsoft. Identity. Web/tokenalımı. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59))

```CSharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Özet

Toplama için, ASP.NET tarafından istenen yetkilendirme kodunu gerçekten `AcquireTokenByAuthorizationCode` ve MSAL.NET Kullanıcı belirteci önbelleğine eklenen belirteçleri alır. Buradan, ASP.NET Core denetleyicilerinde kullanılır.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET yöntemi ASP.NET Core benzer şekilde, Openıdconnect yapılandırması ve `OnAuthorizationCodeReceived` olayına olan aboneliğin [App_Start\Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) dosyasında gerçekleşmesidir. ASP.NET ' de olduğu ASP.NET Core gibi benzer kavramları, [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)Içinde RedirectUri belirtmeniz gerekir. Bu yapılandırma, uygulamanızı dağıtırken değiştirmeniz gereken ASP.NET Core, bu yapılandırmanın çok daha az bir sağlamdır.

```CSharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
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
      // Upon successful sign in, get the access token & cache it using MSAL
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

# <a name="javatabjava"></a>[Java](#tab/java)

Java örneğinin yetkilendirme kodunu nasıl kullandığını anlamak için, bkz. [Kullanıcılar-kod yapılandırması ' nı oturum açan Web uygulaması](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) . Uygulama tarafından alındıktan sonra [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) , [authhelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)içindeki `AuthHelper.processAuthenticationCodeRedirect` yöntemine temsilciler ve sonra `getAuthResultByAuthCode`çağırır:

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

`getAuthResultByAuthCode` yöntemi [Authhelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)içinde tanımlanmıştır. Bir MSAL `ConfidentialClientApplication` oluşturur ve yetkilendirme kodundan oluşturulan `AuthorizationCodeParameters` ile `acquireToken()` çağırır.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Yetkilendirme kodu akışı, [Kullanıcılar-kod yapılandırması ' nda oturum açan Web](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)uygulamasında görüldüğü gibi istendiğinde, kimlik doğrulama kodu,/getAToken URL 'sinden yönlendiren `authorized` işlevinde alınır. Bkz [. app. Sip # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Gizli istemci uygulaması, bir istemci parolası yerine bir istemci sertifikası ya da bir istemci onayı kullanarak kimliğini kanıtlayabilirler.
İstemci onaylamaları kullanmak, [istemci onaylamaları](msal-net-client-assertions.md) hakkında ayrıntılı olarak açıklanan gelişmiş bir senaryodur

## <a name="token-cache"></a>Belirteç önbelleği

> [!IMPORTANT]
> Web uygulamalarında (veya aslında Web API 'Lerinde), belirteç önbelleği uygulaması masaüstü uygulamaları belirteç önbelleği uygulamalarından farklıdır (genellikle [dosya tabanlı](scenario-desktop-acquire-token.md#file-based-token-cache)olur).
> Güvenlik ve performans nedenleriyle, Web uygulamaları ve Web API 'Leri için, Kullanıcı başına bir belirteç önbelleği (hesap başına) olması önemlidir. Her hesap için belirteç önbelleğini seri hale getirmeniz gerekir.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core öğreticisi, uygulamanızın Startup.cs dosyasında belirteç önbelleği uygulamasına karar vermenize olanak sağlamak için bağımlılık ekleme işlemini kullanır. Microsoft. Identity. Web, [belirteç önbelleği serileştirme](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)bölümünde açıklanan bir dizi önceden oluşturulmuş belirteç önbelleği serileştiriciyle gelir. İlginç bir olasılık ASP.NET Core [Dağıtılmış bellek önbellekler](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)' ı seçerdir:

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Belirteç önbelleği sağlayıcıları hakkındaki ayrıntılar için, Ayrıca bkz. [Web uygulaması öğreticileri ASP.NET Core | Öğreticinin belirteç önbellekleri](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) aşaması

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Web uygulamalarında (veya aslında Web API 'Lerinde), belirteç önbelleği uygulaması masaüstü uygulamaları belirteç önbelleği uygulamalarından farklıdır (genellikle [dosya tabanlı](scenario-desktop-acquire-token.md#file-based-token-cache)olur. ASP.NET oturumunu veya sunucu belleğini kullanabilir. MSAL.NET uygulamasının [Msalappbuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51) ' de oluşturulduktan sonra önbellek uygulamasının nasıl takılduğunu görmek için bkz.

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java, belirteç önbelleğini serileştirmek ve serisini kaldırmak için yöntemler sağlar. Java örneği, Authhelper içindeki `getAuthResultBySilentFlow` yönteminde gösterildiği gibi, oturumdan serileştirme işler [. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

`SessionManagementHelper` sınıfının ayrıntıları içinde verilmiştir[](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

Python örneğinde, hesaba göre önbellekten, her istek için bir gizli istemci uygulamasını yeniden oluşturup Flask oturum önbelleğinde serileştirerek,

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

Bu noktada, Kullanıcı bir belirteçle oturum açtığında belirteç önbelleğinde depolanır. Daha sonra, Web uygulamasının diğer bölümlerinde nasıl kullanıldığını görelim.

> [!div class="nextstepaction"]
> [Web uygulamasında oturum açma](scenario-web-app-call-api-sign-in.md)

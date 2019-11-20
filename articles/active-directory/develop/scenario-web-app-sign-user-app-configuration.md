---
title: Kullanıcılarda oturum açan Web uygulaması (kod yapılandırması)-Microsoft Identity platform
description: Kullanıcılara oturum açan bir Web uygulaması oluşturma hakkında bilgi edinin (kod yapılandırması)
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
ms.openlocfilehash: f52fc70b54c27362575bef00c39a93d13e77cc2e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175340"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Kullanıcıları oturum açan Web uygulaması-kod yapılandırması

Kullanıcıları oturum açan Web uygulamanız için kodu yapılandırmayı öğrenin.

## <a name="libraries-used-to-protect-web-apps"></a>Web Apps korumak için kullanılan kitaplıklar

<!-- This section can be in an include for Web App and Web APIs -->
Bir Web uygulamasını (ve bir Web API 'sini) korumak için kullanılan kitaplıklar şunlardır:

| Platform | Kitaplık | Açıklama |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [.NET için kimlik modeli uzantıları](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Doğrudan ASP.NET ve ASP.NET Core tarafından kullanılan .NET için Microsoft Identity Extensions, hem .NET Framework hem de .NET Core üzerinde çalışan bir dll kümesini önerir. Bir ASP.NET/ASP.NET Core Web uygulamasından **Tokenvalidationparameters** sınıfını kullanarak belirteç doğrulamayı denetleyebilirsiniz (bazı ISV senaryolarında özellikle) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Java için MSAL-Şu anda genel önizlemede |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Python için MSAL-Şu anda genel önizlemede |

İlgilendiğiniz platforma karşılık gelen sekmeyi seçin:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Bu makaledeki kod parçacıkları ve aşağıdakiler [ASP.NET Core Web uygulaması artımlı öğreticisi, Bölüm 1 '](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)den ayıklanır.

Tam uygulama ayrıntıları için Bu öğreticiye başvurmak isteyebilirsiniz.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Bu makaledeki kod parçacıkları ve aşağıdakiler [ASP.NET Web uygulaması](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) örneğinden ayıklanır

Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

# <a name="javatabjava"></a>[Java](#tab/java)

Bu makaledeki kod parçacıkları ve aşağıdakiler, Microsoft Graph msal Java Web uygulaması örneğini [çağıran Java Web uygulamasından](https://github.com/Azure-Samples/ms-identity-java-webapp) ayıklanır.

Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

# <a name="pythontabpython"></a>[Python](#tab/python)

Bu makaledeki kod parçacıkları ve aşağıdakiler, [Microsoft Graph msal çağıran Python web uygulamasından](https://github.com/Azure-Samples/ms-identity-python-webapp) ayıklanır. Python web uygulaması örneği

Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

---

## <a name="configuration-files"></a>Yapılandırma dosyaları

Microsoft Identity platformu ile kullanıcıların oturum açmasını sağlayan Web uygulamaları genellikle yapılandırma dosyaları aracılığıyla yapılandırılır. Doldurmanız gereken ayarlar şunlardır:

- uygulamanızın çalışmasını istiyorsanız bulut `Instance` (örneğin, Ulusal bulutlarda)
- `tenantId` hedef kitle
- Azure portal kopyalandığı şekilde uygulamanızın `clientId`.

Bazen, uygulamalar, `instance` ve `tenantId` birleşimi olan `authority` tarafından parametrized olabilir.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core, bu ayarlar "AzureAD" bölümünde [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) dosyasında bulunur.

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

ASP.NET Core, uygulamanız ve çeşitli profilleriniz için URL (`applicationUrl`) ve SSL bağlantı noktasını (`sslPort`) içeren başka bir dosya [properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) vardır.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Azure portal, uygulamanız için **kimlik doğrulama** sayfasına kaydolmanız gereken yanıt URI 'Lerinin bu URL 'lerle eşleşmesi gerekir; diğer bir deyişle, yukarıdaki iki yapılandırma dosyası için, applicationUrl `http://localhost:3110`, ancak `sslPort` belirtildiğinde (44321) `https://localhost:44321/signin-oidc` ve `CallbackPath` `/signin-oidc` tanımlandığı gibi `appsettings.json`.

Aynı şekilde, oturum açma URI 'SI `https://localhost:44321/signout-callback-oidc` olarak ayarlanır.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET ' de, uygulama [Web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) dosya satırları 12-15 ile yapılandırılır.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Azure portal, uygulamanız için **kimlik doğrulama** sayfasına kaydolmanız gereken yanıt URI 'Lerinin bu URL 'lerle eşleşmesi gerekir; `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

Java 'da, yapılandırma `src/main/resources` altında bulunan [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) dosyasında bulunur.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Azure portal, uygulamanız için **kimlik doğrulama** sayfasına kaydolmanız gereken yanıt URI 'lerinin, uygulama tarafından, `http://localhost:8080/msal4jsample/secure/aad` ve `http://localhost:8080/msal4jsample/graph/me` tarafından tanımlanan yeniden yönlendirilebilir.

# <a name="pythontabpython"></a>[Python](#tab/python)

İşte [app_config. Kopyala](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py) içindeki Python yapılandırma dosyası

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> Bu hızlı başlangıç, istemci gizliliğini basitlik için yapılandırma dosyasında depolamayı önerir.
> Üretim uygulamanızda, anahtar kasası gibi gizli dizinizi veya bir ortam değişkenini Flask belgelerinde açıklandığı şekilde depolamak için başka yollar kullanmak isteyeceksiniz: https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Başlatma kodu

Başlatma kodu platforma bağlı olarak farklılık açmış. ASP.NET Core ve ASP.NET için, kullanıcıların oturum açması Openıdconnect ara yazılımı için temsilci olarak oluşturulur. Bugün ASP.NET/ASP.NET Core şablonu, Azure AD v 1.0 uç noktası için Web uygulamaları oluşturur. Bu nedenle, bunları Microsoft Identity platform (v 2.0) uç noktasına uyarlamak için bir dizi yapılandırma gerekir. Java söz konusu olduğunda, uygulamanın iş birliği ile Spring tarafından işlenir.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core Web Apps (ve Web API 'Lerinde), denetleyicilerde veya denetleyici eylemlerinde bir `[Authorize]` özniteliğine sahip olduğunuzdan uygulama korunur. Bu öznitelik, kullanıcının kimliğinin doğrulandığını denetler. Uygulama başlatmayı yapan kod `Startup.cs` dosyasında bulunur ve Microsoft Identity platformu (eskiden Azure AD v 2.0) ile kimlik doğrulaması eklemek için aşağıdaki kodu eklemeniz gerekir. Koddaki yorumların kendine açıklayıcı olması gerekir.

  > [!NOTE]
  > Projenizi Visual Studio içinde varsayılan ASP.NET Core Web projesi veya `dotnet new mvc` kullanarak başlatırsanız, ilgili paketler otomatik olarak yüklendiğinden varsayılan olarak `AddAzureAD` kullanılabilir.
  > Ancak, sıfırdan bir proje oluşturup aşağıdaki kodu kullanmaya çalışıyorsanız, `AddAzureAD` yöntemini kullanılabilir hale getirmek için projenize **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** NuGet paketini eklemenizi öneririz.

Aşağıdaki kod [Başlangıçta kullanılabilir. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

`AddMicrosoftIdentityPlatformAuthentication`, [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)içinde tanımlanan bir genişletme yöntemidir. İçerdiği

- kimlik doğrulama hizmetini ekler
- yapılandırma dosyasını okumak için seçenekleri yapılandırın
- OpenID Connect seçeneklerini, kullanılan yetkilinin Microsoft Identity platform (eski adıyla Azure AD v 2.0) uç noktası olması için yapılandırır
- belirtecin vereni onaylanır
- ada karşılık gelen talepler, KIMLIK belirtecindeki "preferred_username" talebi ile eşleştirilir

Yapılandırmaya ek olarak, `AddMicrosoftIdentityPlatformAuthentication` çağırırken şunları belirtebilirsiniz:

- yapılandırma bölümünün adı (varsayılan olarak AzureAD)
- kimlik doğrulaması çalışmazsa Web uygulamanızın sorunlarını gidermenize yardımcı olabilecek Openıdconnect ara yazılım olaylarını izlemek istiyorsanız: `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` olarak ayarlamak, bilgilerin ASP.NET Core ara yazılım kümesi tarafından nasıl ayrıntılı olacağını gösterir HTTP yanıtından `HttpContext.User` kullanıcının kimliğine kadar ilerler.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

`AadIssuerValidator` sınıfı, kullanıcıların kendi kişisel Microsoft hesaplarıyla, Azure genel bulutunda veya ulusal olarak oturum açtığı birçok durumda (v 1.0 veya v 2.0 belirteci, tek kiracılı veya çok kiracılı uygulama ya da uygulama veya şirket içinde) doğrulanması için izin veren bulutlar). [Microsoft. Identity. Web/Resource/Aadıssuervalidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs) tarafından kullanılabilir

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET Web App/Web API 'Lerinde kimlik doğrulamasıyla ilgili kod [App_Start/Startup. auth. cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) dosyasında bulunur.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java örneği yay çerçevesini kullanır. Uygulama korunur çünkü her HTTP yanıtını karşılar bir `Filter` uygulamanız. Java Web uygulaması hızlı başlangıç bölümünde, bu filtre `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` `AuthFilter`. Filtre, OAuth 2,0 yetkilendirme kodu akışını işler ve bu nedenle:

- kullanıcının kimlik doğrulamasının yapılıp yapılmadığını doğrular (`isAuthenticated()` yöntemi)
- kullanıcının kimliği doğrulanmadıysa, Azure AD yetkilendirme uç noktalarının URL 'sini hesaplar ve tarayıcıyı bu URI 'ye yönlendirir
- Yanıt geldiğinde, MSAL Java kullanarak belirteci elde eden auth kodunu içeren kimlik doğrulama kodunu içerir.
- son olarak belirteç uç noktasından (yeniden yönlendirme URI 'sindeki) belirteci aldığında Kullanıcı oturum açmış olur.

Ayrıntılar için bkz [. AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java) 'daki `doFilter()` yöntemi

> [!NOTE]
> `doFilter()` kodu biraz farklı bir düzende yazılır, ancak akış tanımlanan bir sıradır.

Bu yöntem tarafından tetiklenen yetkilendirme kodu akışı hakkında daha fazla bilgi için bkz. [Microsoft Identity platform ve OAuth 2,0 yetkilendirme kodu akışı](v2-oauth2-auth-code-flow.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Python örneği Flask kullanır. Flask ve MSAL başlatması. Python, uygulama içinde yapılır [. Kopyala # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Sonraki adımlar

Sonraki makalede, oturum açma ve oturum kapatma işlemlerinin nasıl tetikleneceğini öğreneceksiniz.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Oturum aç ve oturumu Kapat](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Oturum aç ve oturumu Kapat](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Oturum aç ve oturumu Kapat](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Oturum aç ve oturumu Kapat](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---

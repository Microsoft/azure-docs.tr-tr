---
title: Kullanıcılara oturum açan bir Web uygulaması yapılandırma | Mavisi
titleSuffix: Microsoft identity platform
description: Kullanıcılara oturum açan bir Web uygulaması oluşturma hakkında bilgi edinin (kod yapılandırması)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: cafd42653ca220670081cff102ba8be2de58f4a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779962"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Kullanıcılara oturum açan Web uygulaması: kod yapılandırması

Kullanıcılara oturum açan Web uygulamanız için kodu yapılandırmayı öğrenin.

## <a name="microsoft-libraries-supporting-web-apps"></a>Web uygulamalarını destekleyen Microsoft kitaplıkları

<!-- This section can be in an include for web app and web APIs -->
Aşağıdaki Microsoft kitaplıkları bir Web uygulamasını (ve bir Web API 'sini) korumak için kullanılır:

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

İlgilendiğiniz platforma karşılık gelen sekmeyi seçin:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Bu makaledeki kod parçacıkları ve aşağıdakiler [ASP.NET Core Web uygulaması artımlı öğreticisi, Bölüm 1 '](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)den ayıklanır.

Tam uygulama ayrıntıları için Bu öğreticiye başvurmak isteyebilirsiniz.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Bu makaledeki kod parçacıkları ve aşağıdakiler [ASP.NET Web uygulaması örneğinden](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)ayıklanır.

Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

# <a name="java"></a>[Java](#tab/java)

Bu makaledeki kod parçacıkları ve aşağıdakiler, MSAL Java 'daki [Microsoft Graph örneğini çağıran Java Web uygulamasından](https://github.com/Azure-Samples/ms-identity-java-webapp) ayıklanır.

Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu makaledeki kod parçacıkları ve aşağıdakiler, MSAL düğümünde örnek [ olarak bulunan Web uygulaması imzalama kullanıcılarıNode.js](https://github.com/Azure-Samples/ms-identity-node) ayıklanır.

Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

# <a name="python"></a>[Python](#tab/python)

Bu makaledeki kod parçacıkları ve aşağıdakiler, MSAL Python 'da [Microsoft Graph örneğini çağıran Python web uygulamasından](https://github.com/Azure-Samples/ms-identity-python-webapp) ayıklanır.

Tam uygulama ayrıntıları için bu örneğe başvurmak isteyebilirsiniz.

---

## <a name="configuration-files"></a>Yapılandırma dosyaları

Microsoft Identity platformunu kullanarak kullanıcıların oturum açmasını sağlayan Web uygulamaları, yapılandırma dosyaları aracılığıyla yapılandırılır. Bunlar, yapılandırmada belirtmeniz gereken değerlerdir:

- `Instance`Uygulamanızın Ulusal bulutlarda çalışmasını istiyorsanız (örneğin,) bulut örneği ()
- Kiracı KIMLIĞINDEKI () hedef kitle `TenantId`
- `ClientId`Uygulamanız için Azure Portal kopyalandığı şekilde ISTEMCI kimliği ()

Ayrıca, başvuruları da görebilirsiniz `Authority` . `Authority`Değer, ve değerlerinin bitiştirilmesi olur `Instance` `TenantId` .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core, bu ayarlar "AzureAd" bölümünde [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) dosyasında bulunur.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.partner.microsoftonline.cn/common" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

ASP.NET Core, başka bir dosya ([properties\launchSettings.js](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)), `applicationUrl` `sslPort` uygulamanız ve çeşitli profiller için URL () ve TLS/SSL bağlantı noktasını () içerir.

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

Azure portal, uygulamanızın **kimlik doğrulama** sayfasına kaydolmanızı sağlayan yeniden yönlendirme URI 'Lerinin bu URL 'leri eşleşmesi gerekir. Önceki iki yapılandırma dosyası için olmaları gerekir `https://localhost:44321/signin-oidc` . Bunun nedeni, `applicationUrl` `http://localhost:3110` ancak `sslPort` belirtilmiştir (44321). `CallbackPath``/signin-oidc`, içinde tanımlandığı gibi `appsettings.json` .

Aynı şekilde, oturum açma URI 'SI olarak ayarlanır `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET ' de, uygulama [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) dosyası aracılığıyla yapılandırılır, satırlar 12 ile 15 arası.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
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

Azure portal, uygulamanızın **kimlik doğrulama** sayfasına kaydolmanızı sağlayan yanıt URI 'Lerinin bu URL 'leri eşleşmesi gerekir. Diğer bir deyişle, olmaları gerekir `https://localhost:44326/` .

# <a name="java"></a>[Java](#tab/java)

Java 'da, yapılandırma altında bulunan [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) dosyasında bulunur `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Azure portal, uygulamanızın **kimlik doğrulama** sayfasına Kaydolmakta olduğunuz yanıt URI 'lerinin, `redirectUri` uygulamanın tanımladığı örneklerle eşleşmesi gerekir. Yani, `http://localhost:8080/msal4jsample/secure/aad` ve olmalıdır `http://localhost:8080/msal4jsample/graph/me` .

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Burada, yapılandırma parametreleri şurada bulunur `index.js`

```javascript

const REDIRECT_URI = "http://localhost:3000/redirect";

const config = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/",
        clientSecret: "Enter_the_Client_Secret_Here"
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: msal.LogLevel.Verbose,
        }
    }
};
```

Azure portal, uygulamanızın kimlik doğrulama sayfasına Kaydolmakta olduğunuz yanıt URI 'Lerinin, uygulamanın tanımladığı Redirecturı örnekleriyle eşleşmesi gerekir ( `http://localhost:3000/redirect` ).

> [!NOTE]
> Bu hızlı başlangıç, istemci gizliliğini basitlik için yapılandırma dosyasında depolamayı önerir. Üretim uygulamanızda, bir Anahtar Kasası veya ortam değişkeni gibi gizli dizinizi depolamak için başka yollar kullanmak isteyeceksiniz.

# <a name="python"></a>[Python](#tab/python)

İşte [app_config. Kopyala](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)içindeki Python yapılandırma dosyası:

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Bu hızlı başlangıç, istemci gizliliğini basitlik için yapılandırma dosyasında depolamayı önerir. Üretim uygulamanızda, [Flask belgelerinde](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)açıklandığı gibi bir Anahtar Kasası veya ortam değişkeni gibi gizli dizinizi depolamak için başka yollar kullanmak isteyeceksiniz.
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Başlatma kodu

Başlatma kodu platforma bağlı olarak farklılık açmış. ASP.NET Core ve ASP.NET için, kullanıcıların oturum açması, OpenID Connect ara yazılımı için temsilci olarak oluşturulur. ASP.NET veya ASP.NET Core şablonu, Azure Active Directory (Azure AD) v 1.0 uç noktası için Web uygulamaları oluşturur. Bu yapılandırma, Microsoft Identity platformu 'na uyum sağlamak için gereklidir. Java söz konusu olduğunda, uygulamanın iş birliği ile Spring tarafından işlenir.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core Web Apps (ve Web API 'Leri) içinde, `[Authorize]` denetleyicilerde veya denetleyici eylemleriyle ilgili bir özniteliğe sahip olduğunuz için uygulama korunur. Bu öznitelik, kullanıcının kimliğinin doğrulandığını denetler. Uygulamayı başlatmakta olan kod *Startup. cs* dosyasında bulunur.

Microsoft Identity platform (eski adıyla Azure AD v 2.0) ile kimlik doğrulaması eklemek için aşağıdaki kodu eklemeniz gerekir. Koddaki yorumların kendine açıklayıcı olması gerekir.

> [!NOTE]
> Microsoft. Identity. Web 'den yararlanan Microsoft Identity platform için yeni ASP.NET Core şablonlarıyla doğrudan başlamak istiyorsanız, .NET Core 3,1 ve .NET 5,0 için proje şablonları içeren bir önizleme NuGet paketi indirebilirsiniz. Sonra, yüklendikten sonra doğrudan ASP.NET Core Web uygulamaları (MVC veya Blazor) örneğini oluşturabilirsiniz. Ayrıntılar için bkz. [Microsoft. Identity. Web Web uygulaması proje şablonları](https://aka.ms/ms-id-web/webapp-project-templates) . Bu en basit yaklaşım, sizin için aşağıdaki adımları sizin için yapacağız.
>
> Projenizi Visual Studio içinde geçerli varsayılan ASP.NET Core Web projesi veya ya da kullanarak başlatmak isterseniz `dotnet new mvc --auth SingleAuth` `dotnet new webapp --auth SingleAuth` , aşağıdaki gibi bir kod görürsünüz:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> Bu kod, bir Azure AD v 1.0 uygulaması oluşturmak için kullanılan eski **Microsoft. AspNetCore. Authentication. AzureAD. UI** NuGet paketini kullanır. Bu makalede, bu kodun yerini alan bir Microsoft Identity platform (Azure AD v 2.0) uygulamasının nasıl oluşturulacağı açıklanmaktadır.
>

1. Projenize [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) ve [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) NuGet paketlerini ekleyin. Varsa Microsoft. AspNetCore. Authentication. AzureAD. UI NuGet paketini kaldırın.

2. ' Deki kodu `ConfigureServices` , ve yöntemlerini kullanacak şekilde güncelleştirin `AddMicrosoftIdentityWebAppAuthentication` `AddMicrosoftIdentityUI` .

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. `Configure` *Başlangıç. cs* içindeki yönteminde, bir çağrısıyla kimlik doğrulamasını etkinleştirin`app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

Yukarıdaki kodda:
- `AddMicrosoftIdentityWebAppAuthentication`Uzantı yöntemi **Microsoft. Identity. Web** içinde tanımlanmıştır. İçerdiği
  - Kimlik doğrulama hizmetini ekler.
  - Yapılandırma dosyasını okuma seçeneklerini yapılandırır (burada "AzureAD" bölümünden)
  - OpenID Connect seçeneklerini, yetkilinin Microsoft Identity platformu olmasını sağlayacak şekilde yapılandırır.
  - Belirtecin vereni doğrular.
  - Ada karşılık gelen taleplerin `preferred_username` kimlik belirtecindeki talepten eşlenmesini sağlar.

- Yapılandırma nesnesine ek olarak, çağrılırken yapılandırma bölümünün adını belirtebilirsiniz `AddMicrosoftIdentityWebAppAuthentication` . Varsayılan olarak, bu `AzureAd` .

- `AddMicrosoftIdentityWebAppAuthentication` , gelişmiş senaryolar için başka parametrelere sahiptir. Örneğin, OpenID Connect ara yazılım olaylarını izlemek, kimlik doğrulaması çalışmazsa Web uygulamanızın sorunlarını gidermenize yardımcı olabilir. İsteğe bağlı parametresinin olarak ayarlanması, `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` HTTP yanıtından ' deki kullanıcının kimliğine ilerledikçe, bilgilerin ASP.NET Core ara yazılım tarafından nasıl işlendiğini gösterir `HttpContext.User` .

- `AddMicrosoftIdentityUI`Genişletme yöntemi **Microsoft. Identity. Web. UI** içinde tanımlanmıştır. Oturum açma ve oturum kapatma işlemek için varsayılan bir denetleyici sağlar.

Microsoft. Identity. Web ' de Web uygulamaları oluşturmanıza nasıl olanak tanıyan hakkında daha fazla ayrıntı bulabilirsiniz. <https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> Şu anda Microsoft. Identity. Web, Azure AD ve dış oturum açma sağlayıcısı kullanılırken **bireysel kullanıcı hesaplarının** (uygulama içi kullanıcı hesaplarını depolayan) senaryoyu desteklemez. Ayrıntılar için bkz. [Azuread/Microsoft-Identity-Web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Bir ASP.NET Web uygulamasında ve Web API 'Lerinde kimlik doğrulamasıyla ilgili kod [App_Start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) dosyasında bulunur.

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Java örneği yay çerçevesini kullanır. Her HTTP yanıtını karşılar bir filtre uyguladığınız için uygulama korunur. Java Web uygulamalarına yönelik hızlı başlangıçta bu filtre bulunur `AuthFilter` `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` .

Filtre, OAuth 2,0 yetkilendirme kodu akışını işler ve kullanıcının kimliğinin doğrulandığını denetler ( `isAuthenticated()` Yöntem). Kullanıcının kimliği doğrulanmadıysa, Azure AD yetkilendirme uç noktalarının URL 'sini hesaplar ve tarayıcıyı bu URI 'ye yönlendirir.

Yanıt geldiğinde, yetkilendirme kodunu içeren, MSAL Java kullanarak belirteci alır. Son olarak belirteç uç noktasından (yeniden yönlendirme URI 'sindeki) belirteci aldığında Kullanıcı oturum açmış olur.

Ayrıntılar için bkz `doFilter()` . [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)içindeki yöntemi.

> [!NOTE]
> Kodu `doFilter()` biraz farklı bir düzende yazılır, ancak akış tanımlanan bir sıradır.

Bu yöntemin tetiklediği yetkilendirme kodu akışı hakkında daha fazla bilgi için bkz. [Microsoft Identity platform ve OAuth 2,0 yetkilendirme kodu akışı](v2-oauth2-auth-code-flow.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const msal = require('@azure/msal-node');

// Create msal application object
const cca = new msal.ConfidentialClientApplication(config);
```

# <a name="python"></a>[Python](#tab/python)

Python örneği Flask kullanır. Flask ve MSAL Python başlatma işlemi [app. Sip # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

Sonraki makalede, oturum açma ve oturum kapatma tetiklemeyi öğreneceksiniz.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Bu senaryodaki bir sonraki makaleye geçin, [oturum açın ve oturumu kapatın](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Bu senaryodaki bir sonraki makaleye geçin, [oturum açın ve oturumu kapatın](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Bu senaryodaki bir sonraki makaleye geçin, [oturum açın ve oturumu kapatın](./scenario-web-app-sign-user-sign-in.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu senaryodaki sonraki makaleye geçin, [oturum açın](./scenario-web-app-sign-user-sign-in.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Bu senaryodaki bir sonraki makaleye geçin, [oturum açın ve oturumu kapatın](./scenario-web-app-sign-user-sign-in.md?tabs=python).

---

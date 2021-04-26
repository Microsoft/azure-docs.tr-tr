---
title: Kullanıcıları oturum açan/kullananlar için bir Web uygulaması yazma | Mavisi
titleSuffix: Microsoft identity platform
description: Kullanıcıları oturum açan/kullananlar için bir Web uygulaması oluşturmayı öğrenin
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
ms.openlocfilehash: 10ddee404de21c5bc04672fdb6dd32c30f481ba3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578252"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Kullanıcı oturumu açan Web uygulaması: oturum açma ve oturum kapatma

Web uygulamanız için Kullanıcı oturumu açan koda oturum açma eklemeyi öğrenin. Daha sonra, nasıl oturum açacağınızı öğrenin.

## <a name="sign-in"></a>Oturum açma

Oturum açma iki bölümden oluşur:

- HTML sayfasındaki oturum açma düğmesi
- Denetleyicinin arka plan kodundaki oturum açma eylemi

### <a name="sign-in-button"></a>Oturum açma düğmesi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core, Microsoft Identity platform uygulamaları için, **oturum açma** düğmesi ' de gösterilir `Views\Shared\_LoginPartial.cshtml` (bir MVC uygulaması için) veya `Pages\Shared\_LoginPartial.cshtm` (Razor uygulaması için). Yalnızca kullanıcının kimliği doğrulanmadığı zaman gösterilir. Diğer bir deyişle, Kullanıcı henüz oturum açmamış veya oturumu kapatmamışsa görüntülenir. Aksine, Kullanıcı zaten oturum açtığında **oturumu** Kapat düğmesi görüntülenir. Hesap denetleyicisinin **Microsoft. Identity. Web. UI** NuGet paketinde, **microsoftıdentity** adlı alanda tanımlandığını unutmayın.

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC 'de, oturum kapatma düğmesi ' de kullanıma sunuldu `Views\Shared\_LoginPartial.cshtml` . Yalnızca kimliği doğrulanmış bir hesap olduğunda gösterilir. Diğer bir deyişle, Kullanıcı daha önce oturum açmış olduğu zaman görüntülenir.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

Java hızlı başlangıç bölümünde, oturum açma düğmesi [ana/kaynak/şablonlar/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) dosyasında bulunur.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Node.js hızlı başlangıçta oturum açma düğmesi yok. Arka plan kodu, kullanıcıya Web uygulamasının köküne ulaşıldığında oturum açma için otomatik olarak sorar.

```javascript
app.get('/', (req, res) => {
    // authentication logic
});
```

# <a name="python"></a>[Python](#tab/python)

Python hızlı başlangıçta oturum açma düğmesi yok. Arka plan kodu, kullanıcıya Web uygulamasının köküne ulaşıldığında oturum açma için otomatik olarak sorar. Bkz. [app. Sip # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` denetleyicinin eylemi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET ' de, Web uygulamasındaki **oturum açma** düğmesinin seçilmesi, `SignIn` `AccountController` denetleyiciyi denetleyiciye tetikler. ASP.NET Core şablonlarının önceki sürümlerinde, `Account` Denetleyici Web uygulamasıyla katıştırılmıştır. Denetleyici artık **Microsoft. Identity. Web. UI** NuGet paketinin bir parçası olduğundan bu durum artık böyle değildir. Ayrıntılar için bkz. [accountcontroller. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

Bu denetleyici Ayrıca Azure AD B2C uygulamalarını da işler.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET ' de, oturum kapatma `SignOut()` bir denetleyicideki yöntemden tetiklenir (örneğin, [accountcontroller. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Bu yöntem, ASP.NET çerçevesinin bir parçası değildir (ASP.NET Core olduğu gibi). Bir yeniden yönlendirme URI 'SI önerdikten sonra bir OpenID oturum açma sınaması gönderir.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Java 'da, oturum kapatma doğrudan Microsoft Identity Platform `logout` uç noktası çağırarak ve değer sağlanarak işlenir `post_logout_redirect_uri` . Ayrıntılar için bkz. [Authpagecontroller. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Diğer platformlardan farklı olarak, MSAL düğümü Kullanıcı oturum açma sayfasından oturum açmaya izin verme işlemini gerçekleştirir.

```javascript

// 1st leg of auth code flow: acquire a code
app.get('/', (req, res) => {
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // get url to sign user in and consent to scopes needed for application
    pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => console.log(JSON.stringify(error)));
});

// 2nd leg of auth code flow: exchange code for token
app.get('/redirect', (req, res) => {
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
        res.sendStatus(200);
    }).catch((error) => {
        console.log(error);
        res.status(500).send(error);
    });
});
```

# <a name="python"></a>[Python](#tab/python)

Diğer platformlardan farklı olarak, MSAL Python Kullanıcı oturum açma sayfasından oturum açmaya izin verir. Bkz. [app. Sip # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

`_build_msal_app()`Yöntemi [app. Sip # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) içinde aşağıdaki gibi tanımlanır:

```Python
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

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Kullanıcı uygulamanızda oturum açtıktan sonra, bunları oturumu kapatmak için etkinleştirmek isteyeceksiniz.

## <a name="sign-out"></a>Oturumu kapatma

Bir Web uygulamasından oturum açmak, Web uygulamasının durumundan oturum açmış hesap hakkındaki bilgileri kaldırmayı kullanmaktan daha fazlasını içerir.
Web uygulaması oturumu kapatmak için kullanıcıyı Microsoft Identity platform uç noktasına yönlendirmelidir `logout` .

Web uygulamanız kullanıcıyı `logout` uç noktaya yönlendirirse, bu uç nokta kullanıcının oturumunu tarayıcıdan temizler. Uygulamanız `logout` uç noktaya gitmediyse, Kullanıcı kimlik bilgilerini tekrar girmeden uygulamanızı yeniden dener. Bunun nedeni, Microsoft Identity platformu ile geçerli bir çoklu oturum açma oturumuna sahip olabileceksiniz.

Daha fazla bilgi edinmek için [Microsoft Identity platform ve OpenID Connect Protocol](v2-protocols-oidc.md) belgelerindeki [oturum açma isteği gönderme](v2-protocols-oidc.md#send-a-sign-out-request) bölümüne bakın.

### <a name="application-registration"></a>Uygulama kaydı

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Uygulama kaydı sırasında ön kanal oturum kapatma URL 'sini kaydedersiniz. Öğreticimizde, `https://localhost:44321/signout-oidc` **kimlik doğrulama** sayfasındaki **ön kanal oturum kapatma URL 'si** alanına kaydolduysanız. Ayrıntılar için bkz. [WebApp uygulamasını kaydetme](scenario-web-app-sign-user-app-registration.md#register-an-app-by-using-the-azure-portal).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Uygulama kaydı sırasında, ek bir ön kanal oturum kapatma URL 'sini kaydetmeniz gerekmez. Uygulama, ana URL 'sinde geri çağrılacaktır. 

# <a name="java"></a>[Java](#tab/java)

Uygulama kaydında ön kanal oturum kapatma URL 'SI gerekli değildir.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Uygulama kaydında ön kanal oturum kapatma URL 'SI gerekli değildir.

# <a name="python"></a>[Python](#tab/python)

Uygulama kaydı sırasında, ek bir ön kanal oturum kapatma URL 'sini kaydetmeniz gerekmez. Uygulama, ana URL 'sinde geri çağrılacaktır.

---

### <a name="sign-out-button"></a>Oturum kapatma düğmesi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET ' de, Web uygulamasındaki **oturumu** Kapat düğmesinin seçilmesi `SignOut` `AccountController` denetleyicideki eylemi tetikler (aşağıya bakın)

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC 'de, oturum kapatma düğmesi ' de kullanıma sunuldu `Views\Shared\_LoginPartial.cshtml` . Yalnızca kimliği doğrulanmış bir hesap olduğunda gösterilir. Diğer bir deyişle, Kullanıcı daha önce oturum açmış olduğu zaman görüntülenir.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

Java hızlı başlangıç bölümünde, oturum kapatma düğmesi ana/kaynak/şablonlar/auth_page.html dosyasında bulunur.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu örnek uygulama, oturum kapatma uygulamaz.

# <a name="python"></a>[Python](#tab/python)

Python hızlı başlangıçta, oturum kapatma düğmesi [Templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) dosyasında bulunur.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` denetleyicinin eylemi

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core şablonlarının önceki sürümlerinde, `Account` Denetleyici Web uygulamasıyla katıştırılmıştır. Denetleyici artık **Microsoft. Identity. Web. UI** NuGet paketinin bir parçası olduğundan bu durum artık böyle değildir. Ayrıntılar için bkz. [accountcontroller. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

- `/Account/SignedOut`Azure AD oturum kapatma işlemini tamamladığında denetleyicinin geri çağrılması için bir OpenID yeniden yönlendirme URI 'si olarak ayarlar.
- Çağrı `Signout()` , OpenID Connect ara yazılım Microsoft Identity platform uç noktasıyla bağlantı sağlar `logout` . Bitiş noktası bundan sonra:

  - Oturum tanımlama bilgisini tarayıcıdan temizler.
  - Kapatma sonrası yeniden yönlendirme URI 'sini geri çağırır. Varsayılan olarak, oturum kapatma sonrası yeniden yönlendirme URI 'SI, imzalanmış görünüm sayfasını [Signeout. cshtml. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs)görüntüler. Bu sayfa Microsoft. Identity. Web 'in bir parçası olarak da sağlanır.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET ' de, oturum kapatma `SignOut()` bir denetleyicideki yöntemden tetiklenir (örneğin, [accountcontroller. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Bu yöntem, ASP.NET Core olan ASP.NET Framework 'ün bir parçası değildir. İçerdiği

- Bir OpenID oturum kapatma sınaması gönderir.
- Önbelleği temizler.
- İstediği sayfaya yeniden yönlendirir.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

Java 'da, oturum kapatma doğrudan Microsoft Identity Platform `logout` uç noktası çağırarak ve değer sağlanarak işlenir `post_logout_redirect_uri` . Ayrıntılar için bkz. [Authpagecontroller. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu örnek uygulama, oturum kapatma uygulamaz.

# <a name="python"></a>[Python](#tab/python)

Kullanıcının imzaladığında kod, [app. Sip # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Uç nokta çağrısını kesintiye uğratan `logout`

Oturum kapatma sonrası URI, uygulamaların genel oturum açma 'ya katılmasına olanak sağlar.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect ara yazılımı, uygulamanızın `logout` adlı bir OpenID Connect olayı sağlayarak Microsoft Identity platform uç noktası çağrısını ele almasını sağlar `OnRedirectToIdentityProviderForSignOut` . Bu, Microsoft. Identity. Web tarafından otomatik olarak işlenir (Web uygulamanızın Web API 'lerini çağırdığı durumda hesapları temizler)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET ' de, oturum açmayı çalıştırmak ve oturum tanımlama bilgisini temizlemek için, ara yazılıma temsilci siz olursunuz:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

Java hızlı penceresinde, kapatma sonrası yeniden yönlendirme URI 'SI yalnızca index.html sayfasını görüntüler.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu örnek uygulama, oturum kapatma uygulamaz.

# <a name="python"></a>[Python](#tab/python)

Python hızlı başlangıcı ' nda, oturum kapatma sonrası yeniden yönlendirme URI 'SI yalnızca index.html sayfasını görüntüler.

---

## <a name="protocol"></a>Protokol

Oturum kapatma hakkında daha fazla bilgi edinmek istiyorsanız, [Açık kimlik Connect](./v2-protocols-oidc.md)'ten erişilebilen protokol belgelerini okuyun.

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki sonraki makaleye geçin, [üretime geçin](scenario-web-app-sign-user-production.md).
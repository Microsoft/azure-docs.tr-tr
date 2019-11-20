---
title: Kullanıcılara oturum açan Web uygulaması (oturum açma)-Microsoft Identity platform
description: Kullanıcıları oturum açan bir Web uygulaması oluşturmayı öğrenin (oturum açın)
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
ms.openlocfilehash: d727b570361e721c49173138bb60ae89df710e81
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175229"
---
# <a name="web-app-that-signs-in-users---sign-in-and-sign-out"></a>Kullanıcılarda oturum açan ve oturumu açtığı Web uygulaması

Kullanıcıları oturum açan Web uygulamanız için koda oturum açma ve daha sonra oturum açmayı nasıl sağlayacağınızı öğrenin

## <a name="sign-in"></a>Oturum açma

Oturum açma iki bölümden oluşur:

- HTML sayfasındaki oturum açma düğmesi
- denetleyicinin arkasındaki koddaki oturum açma eylemi

### <a name="sign-in-button"></a>Oturum açma düğmesi

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core, oturum açma düğmesi `Views\Shared\_LoginPartial.cshtml` gösterilir ve yalnızca kimliği doğrulanmış bir hesap olmadığında (Kullanıcı henüz oturum açmamış veya oturumu kapatmamışsa) görüntülenir.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC 'de, oturum kapatma düğmesi `Views\Shared\_LoginPartial.cshtml` gösterilir ve yalnızca kimliği doğrulanmış bir hesap olduğunda (kullanıcının daha önce oturum açmış olduğu zaman) görüntülenir.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Java hızlı başlangıç bölümünde, oturum kapatma düğmesi [Main/Resources/Templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) dosyasında bulunur

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Python hızlı başlangıçta oturum açma düğmesi yok. Kullanıcı, Web uygulamasının köküne ulaşıldığında, arka planda kod tarafından oturum açmak üzere otomatik olarak istenir. Bkz [. app. Sip # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="login-action-of-the-controller"></a>denetleyicinin `Login` eylemi

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET ' de, Web uygulamasındaki **oturum açma** düğmesine basmak `AccountController` denetleyicisindeki `SignIn` eylemini tetikler. ASP.NET Core şablonlarının önceki sürümlerinde `Account` denetleyicisi Web uygulamasıyla katıştırılmıştır, ancak artık ASP.NET Core çerçevesinin bir parçası olduğu için bu durum artık böyle değildir.

`AccountController` kodu, [accountcontroller.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)' den ASP.NET Core deposundan kullanılabilir. Hesap denetimi, kullanıcının Microsoft Identity platform uç noktasına yönlendirerek zorluk yaptığı sorunları ele alır. Ayrıntılar için ASP.NET Core kapsamında sunulan [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) yöntemine bakın.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET ' de, oturum kapatma bir denetleyicideki `SignOut()` yönteminden tetiklenir (örneğin [accountcontroller. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Bu yöntem, ASP.NET çerçevesinin bir parçası değildir (ASP.NET Core olduğu gibi). İçerdiği

- yeniden yönlendirme URI 'SI önerdikten sonra bir OpenID oturum açma sınaması gönderir

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java 'da, oturum kapatma doğrudan Microsoft Identity platform Logout uç noktası çağırarak ve post_logout_redirect_uri sağlayarak işlenir. Ayrıntılar için bkz [. Authpagecontroller. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Diğer platformlardan farklı olarak, MSAL Python Kullanıcı oturum açma sayfasından oturum açmaya izin vermekten yararlanır. Bkz [. app. Sip # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use empty list [] to just sign in,
                           # here we choose to also collect end user consent upfront
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

[app. Sip # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) içinde tanımlanan _build_msal_app () yöntemiyle aşağıdaki gibi:

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
    if accounts:  # So all account(s) belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Kullanıcı uygulamanıza oturum açtıktan sonra muhtemelen oturumu kapatmalarını sağlamak isteyeceksiniz.

## <a name="sign-out"></a>Oturumu kapat

Bir Web uygulamasından oturum açmak, Web uygulamasının durumundan oturum açmış hesap hakkındaki bilgileri kaldırmasından daha fazla.
Web uygulaması oturumu kapatmak için kullanıcıyı Microsoft Identity platformu `logout` uç noktasına yönlendirmelidir. Web uygulamanız kullanıcıyı `logout` uç noktasına yönlendirirse, bu uç nokta kullanıcının oturumunu tarayıcıdan temizler. Uygulamanız `logout` uç noktasına gitmediyse, Kullanıcı Microsoft Identity platform uç noktası ile geçerli bir çoklu oturum açma oturumuna sahip olduklarından, kimlik bilgilerini tekrar girmeden uygulamanızı yeniden doğrulayacak.

Daha fazla bilgi edinmek için [Microsoft Identity platform ve OpenID Connect Protocol](v2-protocols-oidc.md) kavramsal belgelerindeki [oturum açma isteği gönderme](v2-protocols-oidc.md#send-a-sign-out-request) bölümüne bakın.

### <a name="application-registration"></a>Uygulama kaydı

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Uygulama kaydı sırasında, bir **Post Logout URI 'si**kaydettiniz. Öğreticimizde, **kimlik doğrulama** sayfasındaki **Gelişmiş ayarlar** bölümünün **oturum kapatma URL 'si** alanında `https://localhost:44321/signout-oidc` kaydettiniz. Ayrıntılar için bkz [. WebApp uygulamasını kaydetme](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Uygulama kaydı sırasında, bir **Post Logout URI 'si**kaydettiniz. Öğreticimizde, **kimlik doğrulama** sayfasındaki **Gelişmiş ayarlar** bölümünün **oturum kapatma URL 'si** alanında `https://localhost:44308/Account/EndSession` kaydettiniz. Ayrıntılar için bkz [. WebApp uygulamasını kaydetme](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="javatabjava"></a>[Java](#tab/java)

Uygulama kaydı sırasında, bir **Post Logout URI 'si**kaydetmelisiniz. Öğreticimizde, **kimlik doğrulama** sayfasındaki **Gelişmiş ayarlar** bölümünün **oturum kapatma URL 'si** alanında `http://localhost:8080/msal4jsample/sign_out` kaydettiniz.

# <a name="pythontabpython"></a>[Python](#tab/python)

Uygulama kaydı sırasında, fazladan bir oturum kapatma URL 'SI kaydetmeniz gerekmez. Uygulama, ana URL 'sinde geri çağrılacaktır

---

### <a name="sign-out-button"></a>Oturum kapatma düğmesi

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core, oturum kapatma düğmesi `Views\Shared\_LoginPartial.cshtml` gösterilir ve yalnızca kimliği doğrulanmış bir hesap olduğunda (Kullanıcı daha önce oturum açtığında) görüntülenir.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC 'de, oturum kapatma düğmesi `Views\Shared\_LoginPartial.cshtml` gösterilir ve yalnızca kimliği doğrulanmış bir hesap olduğunda (kullanıcının daha önce oturum açmış olduğu zaman) görüntülenir.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Java hızlı başlangıç bölümünde, oturum kapatma düğmesi main/resources/templates/auth_page.html dosyasında bulunur

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python hızlı başlangıçta, oturum kapatma düğmesi [Şablonlar/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) dosyasında bulunur

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>denetleyicinin `Signout` eylemi

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET ' de, Web uygulamasındaki **Oturumu Kapat** düğmesine basıldığında `AccountController` denetleyicisindeki `SignOut` eylemi tetiklenir. ASP.NET Core şablonlarının önceki sürümlerinde `Account` denetleyicisi Web uygulamasıyla katıştırılmıştır, ancak artık ASP.NET Core çerçevesinin bir parçası olduğu için bu durum artık böyle değildir.

`AccountController` kodu, [accountcontroller.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)adresinden ASP.NET Core deposundan kullanılabilir. Hesap denetimi:

- Azure AD oturum kapatma işlemini tamamladığında denetleyicinin geri çağrılması için `/Account/SignedOut` için bir OpenID yeniden yönlendirme URI 'SI ayarlar
- `Signout()`çağırır, bu, Openıdconnect ara yazılımı 'nın Microsoft Identity platform `logout` uç noktasıyla iletişim kurmasına olanak tanır:

  - Oturum tanımlama bilgisini tarayıcıdan temizler ve
  - son olarak, oturum **kapatma URL**'sini geri çağırır. Bu, varsayılan olarak, ASP.NET Core bir parçası olarak da sağlanmış olan imzalanmış görünüm sayfasını [signeout. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) ' i görüntüler.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET ' de, oturum kapatma bir denetleyicideki `SignOut()` yönteminden tetiklenir (örneğin [accountcontroller. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Bu yöntem, ASP.NET çerçevesinin bir parçası değildir (ASP.NET Core olduğu gibi). İçerdiği

- bir OpenID oturum kapatma sınaması gönderir
- önbelleği temizler
- istediği sayfaya yeniden yönlendirir

```CSharp
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

# <a name="javatabjava"></a>[Java](#tab/java)

Java 'da, oturum kapatma doğrudan Microsoft Identity platform Logout uç noktası çağırarak ve post_logout_redirect_uri sağlayarak işlenir. Ayrıntılar için bkz [. Authpagecontroller. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Kullanıcıyı imzalayan kod, [app. Kopyala # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out user and its token cache from session
    return redirect(  # Also need to logout from Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>`logout` uç noktasına çağrıyı kesintiye uğratan

Logout Post sonrası URI, uygulamaların genel oturum açma 'ya katılmasına olanak sağlar.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core Openıdconnect ara yazılımı, uygulamanızın `OnRedirectToIdentityProviderForSignOut`adlı bir Openıdconnect olayı sağlayarak Microsoft Identity platform `logout` uç noktası çağrısını kesmesini sağlar. Bu olaya abone olunacak bir örnek için bkz. [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) (belirteç önbelleğini temizlemek için)

```CSharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET ' de, oturum açma bilgilerini temizleyerek oturumu kapatma işlemini yürütmek için ara yazılım temsilcisine temsilci seçin:

```CSharp
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

# <a name="javatabjava"></a>[Java](#tab/java)

Java hızlı başlangıç bölümünde, oturum açma sonrası yeniden yönlendirme URI 'SI yalnızca index. html sayfasını görüntüler

# <a name="pythontabpython"></a>[Python](#tab/python)

Python hızlı başlangıçta, oturum açma sonrası yeniden yönlendirme URI 'SI yalnızca index. html sayfasını görüntüler.

---

## <a name="protocol"></a>Protokol

Oturum kapatma hakkında daha fazla bilgi edinmek istiyorsanız, [Açık kimlik Connect](./v2-protocols-oidc.md)'ten erişilebilen protokol belgelerini okuyun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Üretime taşı](scenario-web-app-sign-user-production.md)

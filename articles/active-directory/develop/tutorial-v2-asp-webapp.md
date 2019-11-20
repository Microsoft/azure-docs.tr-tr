---
title: Azure AD v 2.0 ASP.NET Web sunucusu Başlarken
titleSuffix: Microsoft identity platform
description: Geleneksel bir Web tarayıcısı tabanlı uygulama ve OpenID Connect Standard kullanarak ASP.NET çözümünde Microsoft oturum açma uygulama
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66d2a9a958afd536dcffeca211a3fc56cf09ef8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803712"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Microsoft 'a bir ASP.NET Web uygulamasına oturum açma ekleme

Bu kılavuzda, geleneksel bir Web tarayıcısı tabanlı uygulama ve OpenID Connect kullanılarak ASP.NET MVC çözümü aracılığıyla Microsoft 'a oturum açma işlemlerinin nasıl uygulanacağı gösterilmektedir.

Bu kılavuzu tamamladığınızda, uygulamanız kişisel hesapların oturum açma işlemlerini outlook.com ve live.com beğeni kabul edebilir. Ayrıca, Azure Active Directory (Azure AD) ile tümleştirilmiş herhangi bir şirketten veya kuruluştan iş ve okul hesapları uygulamanızda oturum açabiliyor.

> Bu kılavuz Microsoft Visual Studio 2019 gerektirir.  Sizde yok mu?  [Visual Studio 2019 ' ü ücretsiz indirin](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Bu kılavuz tarafından oluşturulan örnek uygulamanın nasıl çalıştığı

![Bu öğretici tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Oluşturduğunuz örnek uygulama, kullanıcının oturum açma düğmesiyle kimlik doğrulaması yapmasını isteyen bir ASP.NET Web sitesine erişmek için tarayıcıyı kullandığınız bir senaryoya dayalıdır. Bu senaryoda web sayfasını oluşturma işlemlerinin çoğu sunucu tarafında gerçekleştirilmektedir.

## <a name="libraries"></a>Kitaplıklar

Bu kılavuz aşağıdaki kitaplıkları kullanır:

|Kitaplık|Açıklama|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Uygulamanın kimlik doğrulaması için OpenIdConnect kullanmasını sağlayan ara yazılım|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Bir uygulamanın tanımlama bilgilerini kullanarak bir Kullanıcı oturumu korumasına olanak tanıyan ara yazılım|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|ASP.NET istek işlem hattını kullanarak OWıN tabanlı uygulamaların Internet Information Services (IIS) üzerinde çalışmasını sağlayan ara yazılım|

## <a name="set-up-your-project"></a>Projenizi ayarlama

Bu bölümde, OpenID Connect kullanılarak bir ASP.NET projesine OWıN ara yazılımı aracılığıyla kimlik doğrulama işlem hattının nasıl yükleneceği ve yapılandırılacağı açıklanmaktadır.

> Bunun yerine bu örneğin Visual Studio projesini indirmeyi tercih etmek istiyor musunuz? [Bir proje indirin](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) ve yürütmeden önce kod örneğini yapılandırmak Için [Uygulamanızı kaydetme](#register-your-application) bölümüne atlayın.

### <a name="create-your-aspnet-project"></a>ASP.NET projenizi oluşturma

1. Visual Studio 'da: **dosya** > **Yeni** > **projesi**' ne gidin.
2. **Visual C#\Web** bölümünde **ASP.NET Web Uygulaması (.NET Framework)** girişini seçin.
3. Uygulamanıza bir ad verin ve **Tamam**'ı seçin.
4. **Boş**' ı seçin ve ardından **MVC** başvurularını eklemek için onay kutusunu işaretleyin.

## <a name="add-authentication-components"></a>Kimlik doğrulama bileşenleri ekleme

1. Visual Studio 'da: **araçlar** > **Nuget Paket Yöneticisi** > **Paket Yöneticisi konsolu**' na gidin.
2. Paket Yöneticisi Konsolu penceresinde aşağıdakileri yazarak *OWIN ara yazılım NuGet paketlerini* ekleyin:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Bu kitaplıklar hakkında
> Bu kitaplıklar, tanımlama bilgisi tabanlı kimlik doğrulaması aracılığıyla OpenID Connect kullanarak çoklu oturum açmayı (SSO) etkinleştirir. Kimlik doğrulaması tamamlandıktan ve kullanıcıyı temsil eden belirteç uygulamanıza gönderildikten sonra OWIN ara yazılımı bir oturum tanımlama bilgisi oluşturur. Daha sonra tarayıcı bu tanımlama bilgisini sonraki isteklerde kullanır, böylece Kullanıcı parolayı yeniden yazması gerekmez ve ek bir doğrulama gerekmez.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Kimlik doğrulama işlem hattını yapılandırma

Aşağıdaki adımlar, OpenID Connect kimlik doğrulamasını yapılandırmak için bir OWıN ara yazılım başlangıç sınıfı oluşturmak için kullanılır. Bu sınıf, IIS işleminiz başladığında otomatik olarak yürütülür.

> [!TIP]
> Projenizin kök klasöründe `Startup.cs` adlı bir dosya yoksa:
> 1. Projenin kök klasörüne sağ tıklayın ve ardından > **Yeni öğe** **Ekle** ' > **owın başlangıç sınıfı**' nı seçin.<br/>
> 2. **Startup.cs**olarak adlandırın.
>
>> Seçilen sınıfın bir OWıN başlangıç sınıfı olduğundan ve standart C# sınıf olmadığından emin olun. [Assembly: Owınstartup (typeof ({NameSpace}) gördüğünüz doğrulayarak bunu onaylayın. Başlangıç))] ad alanı üzerinde.

1. Startup.cs 'e *Owın* ve *Microsoft. IdentityModel* başvuruları ekleyin:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Başlangıç sınıfını aşağıdaki kodla değiştirin:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Bu hızlı başlangıç için `ValidateIssuer = false` ayarı basitleştirmesi. Gerçek uygulamalarda, sertifikayı vereni doğrulamanız gerekir.
> Bunun nasıl yapılacağını öğrenmek için örneklere bakın.

<!--start-collapse-->
> ### <a name="more-information"></a>Daha fazla bilgi
> *OpenIDConnectAuthenticationOptions* içinde sağladığınız parametreler uygulamanın Azure AD ile iletişim kurmak için kullanacağı koordinatlara benzer. OpenID Connect ara yazılımı arka planda tanımlama bilgileri kullandığından, önceki kodun gösterdiği gibi tanımlama bilgisi kimlik doğrulamasını da ayarlamanız gerekir. *Validateıssuer* değeri, Openıdconnect 'in belirli bir kuruluşa erişimi kısıtlayamayacağını söyler.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Oturum açma ve oturum kapatma isteklerini işlemek için bir denetleyici ekleme

Oturum açma ve oturum kapatma yöntemlerini kullanıma sunmak üzere yeni bir denetleyici oluşturmak için aşağıdaki adımları izleyin:

1.  **Denetleyiciler** klasörüne sağ tıklayın ve > **denetleyicisi** **Ekle** ' yi seçin.
2.  **MVC (.NET sürümü) Denetleyici – Boş** girişini seçin.
3.  **Add (Ekle)** seçeneğini belirleyin.
4.  **HomeController** olarak adlandırın ve **Ekle**' yi seçin.
5.  Sınıfa OWıN başvuruları ekleyin:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Oturum açmayı işlemek ve bir kimlik doğrulama sınaması başlatarak denetleyicinize kaydolmak için aşağıdaki iki yöntemi ekleyin:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Kullanıcının oturum açması için uygulamanın giriş sayfasını oluşturma

Visual Studio 'da, oturum açma düğmesini eklemek ve kimlik doğrulamasından sonra Kullanıcı bilgilerini görüntülemek için yeni bir görünüm oluşturun:

1.  **Görünümler\Giriş Sayfası** klasörüne sağ tıklayın ve **Görünüm Ekle**'yi seçin.
2.  Yeni Görünüm **dizinini**adlandırın.
3.  Oturum açma düğmesini de içeren aşağıdaki HTML kodunu dosyaya ekleyin:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Daha fazla bilgi
> Bu sayfa, SVG formatında siyah bir arka plana sahip bir oturum açma düğmesi ekler:<br/>![Microsoft hesabıyla oturum açın](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Daha fazla oturum açma düğmesi için [marka yönergelerine](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Branding yönergeleri ")gidin.
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Kullanıcının taleplerini göstermek için bir denetleyici ekleme
Bu denetleyici bir denetleyiciyi koruma amacıyla `[Authorize]` özniteliğini kullanma şeklini gösterir. Bu öznitelik yalnızca kimliği doğrulanmış kullanıcılara izin vererek denetleyiciye erişimi kısıtlar. Aşağıdaki kod, oturum açma kapsamında alınan kullanıcı taleplerini göstermek için özniteliğini kullanır:

1.  **Denetleyiciler** klasörüne sağ tıklayın ve ardından > **denetleyicisi** **Ekle** ' yi seçin.
2.  **MVC {sürüm} Denetleyici – Boş** girişini seçin.
3.  **Add (Ekle)** seçeneğini belirleyin.
4.  **ClaimsController** olarak adlandırın.
5.  Denetleyici sınıfınızın kodunu aşağıdaki kodla değiştirin. Bu, sınıfına `[Authorize]` özniteliğini ekler:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Daha fazla bilgi
> `[Authorize]` özniteliğinin kullanımı nedeniyle, bu denetleyicideki tüm yöntemler yalnızca kullanıcının kimliği doğrulandıysa yürütülebilir. Kullanıcı kimliği doğrulanmazsa ve denetleyiciye erişmeye çalışırsa, OWıN bir kimlik doğrulama sınaması başlatır ve kullanıcıyı kimlik doğrulamaya zorlar. Yukarıdaki kod, kullanıcının kimlik belirtecine dahil edilen belirli kullanıcı özniteliklerinin talepler listesine bakar. Bu öznitelik kullanıcının tam adını, kullanıcı adını ve genel kullanıcı tanımlayıcısı nesnesini içerir. Ayrıca kullanıcının kuruluşunun kimliğini temsil eden *Kiracı Kimliği* değerini de içerir. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Kullanıcının taleplerini görüntülemek için bir görünüm oluşturun

Visual Studio'da kullanıcının taleplerini bir web sayfasında görüntülemek için yeni bir görünüm oluşturun:

1.  **Views\claim** klasörüne sağ tıklayın ve ardından **Görünüm Ekle**' yi seçin.
2.  Yeni Görünüm **dizinini**adlandırın.
3.  Aşağıdaki HTML kodunu dosyaya ekleyin:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Uygulamanızı kaydetme

Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze eklemek için iki seçeneğiniz vardır:

### <a name="option-1-express-mode"></a>Seçenek 1: hızlı mod

Uygulamanızı hızlı bir şekilde kaydetmek için şu adımları izleyin:

1. Yeni [Azure Portal-uygulama kayıtları](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) bölmesine gidin.
1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
1. Yeni uygulamanızı tek bir tıklama ile indirip otomatik olarak yapılandırmak için yönergeleri izleyin.

### <a name="option-2-advanced-mode"></a>Seçenek 2: Gelişmiş mod

Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze el ile eklemek için şu adımları izleyin:

1. Visual Studio 'yu açın ve ardından:
   1. Çözüm Gezgini, projeyi seçin ve Özellikler penceresi görüntüleyin (Özellikler penceresi görmüyorsanız F4 tuşuna basın).
   1. SSL 'yi `True`için etkin olarak değiştirin.
   1. Visual Studio 'da projeye sağ tıklayın, **Özellikler**' i seçin ve ardından **Web** sekmesini seçin. **Sunucular** bölümünde, **proje URL** ayarını **SSL URL 'si**olarak değiştirin.
   1. SSL URL 'sini kopyalayın. Bu URL 'YI, sonraki adımda, kayıt portalının yeniden yönlendirme URL 'leri listesinde bulunan yeniden yönlendirme URL 'Leri listesine ekleyeceksiniz.<br/><br/>![Proje özellikleri](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Bir iş veya okul hesabı kullanarak veya kişisel bir Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Hesabınız birden fazla kiracıya erişim veriyorsa, sağ üst köşede hesabınızı seçin ve Portal oturumunuzu istediğiniz Azure AD kiracısına ayarlayın.
1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin.
1. **Yeni kayıt**seçeneğini belirleyin.
1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:
   1. **Ad** bölümünde, uygulama kullanıcılarına, **ASPNET-öğreticisi**gibi görüntülenecek anlamlı bir uygulama adı girin.
   1. Adım 1 ' de Visual Studio 'dan kopyaladığınız SSL URL 'sini (örneğin, `https://localhost:44368/`), **yanıt URL**'si ' nde ekleyin ve **Kaydet**' i seçin.
1. **Kimlik doğrulama** menüsünü seçin, **örtük izin**altında **Kimlik belirteçleri** ' ni seçin ve ardından **Kaydet**' i seçin.
1. Aşağıdakini `configuration\appSettings` bölümündeki kök klasörde bulunan Web. config dosyasına ekleyin:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. `ClientId` yeni kaydettiğiniz uygulama KIMLIĞIYLE değiştirin.
1. `redirectUri`, projenizin SSL URL 'siyle değiştirin.

## <a name="test-your-code"></a>Kodunuzu test etme

Visual Studio 'da uygulamanızı test etmek için F5 'e basarak projenizi çalıştırın. Tarayıcı, http://<span></span>localhost: {Port} konumunda açılır ve **Microsoft hesabıyla oturum açın** düğmesini görürsünüz. Oturum açma işlemini başlatmak için düğmeyi seçin.

Testinizi çalıştırmaya hazırsanız bir Azure AD hesabı (iş veya okul hesabı) veya kişisel Microsoft hesabı (canlı) kullanın<span>.</span> com veya <span>Outlook.</span> com) oturum açın.

![Microsoft hesabıyla oturum açın](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Microsoft hesabı oturum açın](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Microsoft Identity platform uç noktasındaki izinler ve onay
>  Microsoft Identity platformu ile tümleştirilen uygulamalar, kullanıcılara ve yöneticilere verilere nasıl erişilebileceği üzerinde denetim sağlayan bir yetkilendirme modeli izler. Bu uygulamaya erişmek için Kullanıcı Azure AD ile kimlik doğrulamasından geçtikten sonra, uygulama tarafından istenen izinleri ("temel profilinizi görüntüleme" ve "erişim vermiş olduğunuz verilere erişimi koruma") onaylaması istenir. Bu izinleri kabul ettikten sonra, Kullanıcı uygulama sonuçlarına devam edecektir. Bununla birlikte, aşağıdakilerden biri gerçekleştiğinde kullanıcıya **Yönetici onay sayfası gereksinimi** istenebilir:
>  > - Uygulama geliştiricisi, **yönetici onayı**gerektiren herhangi bir ek izin ekler.
>  > - Ya da kiracı, kullanıcıların kendi adına şirket verilerine erişen uygulamalara izin veremediği ( **Kurumsal uygulamalarda > Kullanıcı ayarları**) yapılandırılır.
>
> Daha fazla bilgi için [Microsoft Identity platform uç noktasındaki izinler ve onay](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)bölümüne bakın.
<!--end-collapse-->

#### <a name="view-application-results"></a>Uygulama sonuçlarını görüntüle

Oturum açtıktan sonra, Kullanıcı Web sitenizin giriş sayfasına yönlendirilir. Giriş sayfası, Microsoft uygulama kayıt portalındaki uygulama kayıt bilgilerinizdeki belirtilen HTTPS URL 'sidir. Giriş sayfası *"merhaba \<kullanıcı >"* hoş geldiniz iletisi, oturumu kapatma bağlantısı ve kullanıcının taleplerini görüntülemek için bir bağlantı içerir. Kullanıcı taleplerinin bağlantısı, daha önce oluşturduğunuz talep denetleyicisine bağlanır.

### <a name="view-the-users-claims"></a>Kullanıcının taleplerini görüntüleme

Kullanıcının taleplerini görüntülemek için, yalnızca kimliği doğrulanmış kullanıcılar için kullanılabilen denetleyici görünümüne gitmek için bağlantıyı seçin.

#### <a name="view-the-claims-results"></a>Talep sonuçlarını görüntüleme

Denetleyici görünümüne gözatdıktan sonra, kullanıcının temel özelliklerini içeren bir tablo görmeniz gerekir:

|Özellik |Değer |Açıklama |
|---|---|---|
|**Adı** |Kullanıcının tam adı | Kullanıcının adı ve soyadı
|**Kullanıcı Adı** |Kullanıcı<span>@domain.com</span> | Kullanıcıyı tanımlamak için kullanılan Kullanıcı adı|
|**Konu** |Özne |Kullanıcıyı web genelinde benzersiz şekilde tanımlayan bir dize|
|**Kiracı KIMLIĞI** |Guid | Kullanıcının Azure AD organizasyonunu benzersiz bir şekilde temsil eden bir **GUID**|

Ayrıca, kimlik doğrulama isteğinde olan tüm taleplerin bir tablosunu görmeniz gerekir. Daha fazla bilgi için bkz. [Azure AD kimlik belirtecinde olan taleplerin listesi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Yetkilendir özniteliğine sahip bir yönteme erişimi test etme (isteğe bağlı)

Erişimi, `Authorize` özniteliğiyle korunan bir denetleyiciye anonim kullanıcı olarak test etmek için şu adımları izleyin:

1. Kullanıcının oturumunu kapatmak için bağlantıyı seçin ve oturum kapatma sürecini doldurun.
2. Tarayıcınızda, `Authorize` özniteliğiyle korunan denetleyicinize erişmek için http://<span></span>localhost: {Port}/için talepler yazın.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Korumalı denetleyiciye erişimi sonrasında beklenen sonuçlar

Korumalı denetleyici görünümünü kullanmak için kimlik doğrulamanız istenir.

## <a name="advanced-options"></a>Gelişmiş Seçenekler

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Web sitenizin tamamını koruyun
Web sitenizin tamamını korumak için, **Global. asax** dosyasında, `Application_Start` yönteminde `GlobalFilters` filtresine `AuthorizeAttribute` özniteliğini ekleyin:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Uygulamanızda oturum açabilen kişileri kısıtla

Varsayılan olarak, bu kılavuz tarafından oluşturulan uygulamayı yapılandırdığınızda, uygulamanız kişisel hesapların (outlook.com, live.com ve diğerleri dahil) oturum açma işlemlerini kabul eder ve bunlarla tümleştirilmiş olan herhangi bir şirketten veya kuruluştan iş ve okul hesapları kabul eder. Azure AD. Bu, SaaS uygulamaları için önerilen bir seçenektir.

Uygulamanıza yönelik kullanıcı oturum açma erişimini kısıtlamak için birden çok seçenek mevcuttur.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Seçenek 1: kullanıcıların uygulamanızda oturum açmasını sağlamak için yalnızca bir kuruluşun Active Directory örneğinden kullanıcıları kısıtlayın (tek kiracılı)

Bu seçenek *LOB uygulamaları*için sık sık kullanılır: uygulamanızın yalnızca belirli BIR Azure AD örneğine ait olan hesaplardan (Bu örneğin *Konuk hesapları* dahil) oturum açma işlemlerini kabul etmesini istiyorsanız, şu adımları izleyin:

1. Web. config dosyasında, `Common` `Tenant` parametresinin değerini, kuruluşun kiracı adına `contoso.onmicrosoft.com`gibi değiştirin.
2. [Owın başlangıç sınıfında](#configure-the-authentication-pipeline)`ValidateIssuer` bağımsız değişkenini `true`olarak ayarlayın.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Seçenek 2: belirli bir kuruluş listesindeki kullanıcılara erişimi kısıtlama

Oturum açma erişimini yalnızca, izin verilen kuruluşlar listesindeki bir Azure AD kuruluşunda bulunan Kullanıcı hesaplarıyla kısıtlayabilirsiniz:
1. [Owın başlangıç sınıfında](#configure-the-authentication-pipeline)`ValidateIssuer` bağımsız değişkenini `true`olarak ayarlayın.
2. `ValidIssuers` parametresinin değerini izin verilen kuruluşlar listesine ayarlayın.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Seçenek 3: verenler doğrulamak için özel bir yöntem kullanma

**Issuervalidator** parametresini kullanarak verenler doğrulamak için özel bir yöntem uygulayabilirsiniz. Bu parametrenin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Tokenvalidationparameters sınıfı](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Sonraki adımlar

Web uygulamalarının Web API 'Lerini nasıl çağırabileceğinizi öğrenin.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Bu hızlı başlangıç kılavuzunda kullanılan uygulamayı oluşturmayı öğrenin

Microsoft Identity platformu ile Web API 'Lerini çağıran Web uygulamaları hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Web API 'Lerini çağıran Web uygulamaları](scenario-web-app-sign-user-overview.md)

Microsoft Graph çağıran Web uygulamaları oluşturmayı öğrenin:

> [!div class="nextstepaction"]
> [Microsoft Graph ASP.NET öğreticisi](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Microsoft Identity platformunu geliştirmemize yardımcı olun. İki sorulık bir anketi tamamlayarak düşüncelerinizi bize söyleyin:

> [!div class="nextstepaction"]
> [Microsoft Identity platform Anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

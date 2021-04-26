---
title: "Hızlı başlangıç: Microsoft 'a ASP.NET Web uygulamasına oturum açma ekleme | Mavisi"
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, OpenID Connect kullanarak bir ASP.NET Web uygulamasında Microsoft oturum açma 'yı nasıl uygulayacağınızı öğrenin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 87948ed04f7b50820d94993d4c4fbcf2dfd94b31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578694"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Hızlı başlangıç: ASP.NET Web uygulamasına Microsoft Identity platformu oturum açma ekleme

Bu hızlı başlangıçta, bir ASP.NET Web uygulamasının kullanıcılara herhangi bir Azure Active Directory (Azure AD) kuruluştan nasıl oturum açabileceğinizi gösteren bir kod örneği indirip çalıştırırsınız. 

> [!div renderon="docs"]
> Aşağıdaki diyagramda örnek uygulamanın nasıl çalıştığı gösterilmektedir:
>
> ![Web tarayıcısı, Web uygulaması ve örnek uygulamadaki Microsoft Identity platformu arasındaki etkileşimin diyagramı.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Önkoşullar
>
> * Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2 +](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>Uygulamayı kaydedin ve indirin
> Uygulamanızı oluşturmaya başlamak için iki seçeneğiniz vardır: otomatik veya el ile yapılandırma.
>
> ### <a name="automatic-configuration"></a>Otomatik yapılandırma
> Uygulamanızı otomatik olarak yapılandırmak ve ardından kod örneğini indirmek istiyorsanız aşağıdaki adımları izleyin:
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">Uygulama kaydı için Azure Portal sayfasına</a>gidin.
> 1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
> 1. Yeni uygulamanızı tek tıklamayla indirmek ve otomatik olarak yapılandırmak için yönergeleri izleyin.
>
> ### <a name="manual-configuration"></a>El ile yapılandırma
> Uygulamanızı ve kod örneğinizi el ile yapılandırmak istiyorsanız aşağıdaki yordamları kullanın.
>
> #### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
> 1. Birden çok kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Azure Active Directory**'yi bulun ve seçin.
> 1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
> 1. **Ad** için, uygulamanız için bir ad girin. Örneğin, **ASPNET-hızlı başlangıç** girin. Uygulamanızın kullanıcıları bu adı görür ve daha sonra değiştirebilirsiniz.
> 1. **https://localhost:44368/** **Yeniden yönlendirme URI 'si** ekleyin ve **Kaydet**' i seçin.
> 1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
> 1. **Örtük verme ve karma akışlar** bölümünde **Kimlik belirteçleri**' ni seçin.
> 1. **Kaydet**’i seçin.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. Adım: uygulamanızı Azure portal yapılandırma
> Bu hızlı başlangıçtaki kod örneğinin çalışması için **https://localhost:44368/** **yeniden yönlendirme URI 'si** girin.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişikliği benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış ](media/quickstart-v2-aspnet-webapp/green-check.png) uygulamanız Bu öznitelikle yapılandırıldı.

#### <a name="step-2-download-the-project"></a>2. Adım: Projeyi indirme

> [!div renderon="docs"]
> [Visual Studio 2019 çözümünü indirin](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Visual Studio 2019 kullanarak projeyi çalıştırın.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Kod örneğini indirin](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. Adım: uygulamanız yapılandırıldı ve çalıştırılmaya hazırlanıyor
> Projenizi uygulamanızın özelliklerinin değerleriyle yapılandırdık.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>3. Adım: Visual Studio projenizi çalıştırma

1. . Zip dosyasını kök klasöre yakın bir yerel klasöre ayıklayın. Örneğin, *C:\Azure-Samples*' a ayıklayın.
   
   Windows üzerinde yol uzunluğu sınırlamalarından kaynaklanan hataları önlemek için, arşivin sürücünüzün köküne yakın bir dizine ayıklanmasını öneririz.
2. Visual Studio 'da çözümü açın (*AppModelv2-WebApp-OpenIDConnect-DotNet. sln*).
3. Visual Studio sürümüne bağlı olarak, **AppModelv2-WebApp-Openıdconnect-DotNet** projesine sağ tıklayıp **NuGet paketlerini geri yükle**' yi seçmeniz gerekebilir.
4.   >  **Diğer Windows**  >  **Package Manager konsolunu** göster ' i seçerek Paket Yöneticisi konsolunu açın. Ardından `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r` komutunu çalıştırın.

> [!div renderon="docs"]
> 5. *Web.config* düzenleyin ve parametreleri, `ClientId` `Tenant` ve `redirectUri` ile değiştirin:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    Bu kodda:
>
>    - `Enter_the_Application_Id_here` , daha önce oluşturduğunuz uygulama kaydının uygulama (istemci) KIMLIĞIDIR. Uygulamanın **genel bakış** sayfasında uygulama (ISTEMCI) kimliğini Azure Portal **uygulama kayıtları** bulun.
>    - `Enter_the_Tenant_Info_Here` Aşağıdaki seçeneklerden biridir:
>      - Uygulamanız **yalnızca Kuruluşumu** destekliyorsa, bu değeri dizin (KIRACı) kimliği veya kiracı adı (örneğin,) ile değiştirin `contoso.onmicrosoft.com` . Uygulamanın **genel bakış** sayfasında, Azure Portal **uygulama kayıtları** DIZIN (kiracı) kimliğini bulun.
>      - Uygulamanız **herhangi bir kuruluş dizinindeki hesapları** destekliyorsa, bu değeri ile değiştirin `organizations` .
>      - Uygulamanız **tüm Microsoft hesabı kullanıcıları** destekliyorsa, bu değeri ile değiştirin `common` .
>    - `redirectUri`, daha önce Azure portal **uygulama kayıtları** Içinde girdiğiniz **yeniden yönlendirme URI 'sidir** .
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Daha fazla bilgi

Bu bölüm, kullanıcıların oturum açması için gereken koda genel bir bakış sağlar. Bu genel bakış, kodun nasıl çalıştığını, ana bağımsız değişkenlerin ne olduğunu ve mevcut bir ASP.NET uygulamasına nasıl oturum ekleneceğini anlamak için yararlı olabilir.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı
>
> ![Web tarayıcısı, Web uygulaması ve örnek uygulamadaki Microsoft Identity platformu arasındaki etkileşimin diyagramı.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN ara yazılımı NuGet paketleri

ASP.NET içindeki OpenID Connect ile OWıN ara yazılım paketleriyle, kimlik bilgisi tabanlı kimlik doğrulama ile kimlik doğrulama işlem hattını ayarlayabilirsiniz. Bu paketleri, Visual Studio 'da Paket Yöneticisi konsolunda aşağıdaki komutları çalıştırarak yükleyebilirsiniz:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWıN başlangıç sınıfı

OWıN ara yazılımı, barındırma işlemi başladığında çalışan bir *Başlangıç sınıfı* kullanır. Bu hızlı *Başlangıçta, başlangıç. cs* dosyası kök klasöründedir. Aşağıdaki kod, bu hızlı başlangıçta kullanılan parametreleri gösterir:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Konum  | Description |
> |---------|---------|
> | `ClientId`     | Azure portal kayıtlı uygulamadaki uygulama KIMLIĞI. |
> | `Authority`    | Kullanıcının kimlik doğrulaması için güvenlik belirteci hizmeti (STS) uç noktası. Genellikle `https://login.microsoftonline.com/{tenant}/v2.0` genel bulut içindir. Bu URL 'de, *{Tenant}* kiracınızın adı, kiracı kimliğiniz veya `common` ortak uç nokta başvurusu için. (Ortak uç nokta çok kiracılı uygulamalar için kullanılır.) |
> | `RedirectUri`  | Microsoft Identity platformunda kimlik doğrulamasından sonra kullanıcıların gönderildiği URL. |
> | `PostLogoutRedirectUri`     | Oturum kapatıldıktan sonra kullanıcıların gönderildiği URL. |
> | `Scope`     | İstenen kapsamların listesi, boşluklarla ayrılır. |
> | `ResponseType`     | Kimlik doğrulamasından gelen yanıtın bir yetkilendirme kodu ve KIMLIK belirteci içerdiği istek. |
> | `TokenValidationParameters`     | Belirteç doğrulaması için parametre listesi. Bu durumda, `ValidateIssuer` `false` herhangi bir kişisel, iş veya okul hesabı türünden oturum açma işlemlerini kabul edemeyeceğini belirtmek için olarak ayarlanır. |
> | `Notifications`     | İletilerde çalıştırılabilen temsilcilerin listesi `OpenIdConnect` . |


> [!NOTE]
> Ayar `ValidateIssuer = false` , bu hızlı başlangıç için bir basitleştirmesi. Gerçek uygulamalarda, vereni doğrulayın. Bunun nasıl yapılacağını anlamak için örneklere bakın.

### <a name="authentication-challenge"></a>Kimlik doğrulama sınaması

Denetleyicinizde kimlik doğrulaması sınaması isteyerek kullanıcıyı oturum açmaya zorlayabilirsiniz:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Bu yöntemi kullanarak kimlik doğrulama sınaması istemek isteğe bağlıdır. Genellikle, bir görünümün hem kimliği doğrulanmış hem de kimliği doğrulanmamış kullanıcılardan erişilebilir olmasını istediğinizde kullanırsınız. Alternatif olarak, bir sonraki bölümde açıklanan yöntemle denetleyicileri koruyabilirsiniz.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Denetleyiciyi veya denetleyici eylemlerini koruma özniteliği

Özniteliğini kullanarak bir denetleyiciyi veya denetleyici eylemlerini koruyabilirsiniz `[Authorize]` . Bu öznitelik, denetleyicideki eylemlere yalnızca kimliği doğrulanmış kullanıcıların erişmesine izin vererek denetleyiciye veya eylemlere erişimi kısıtlar. Kimliği doğrulanmamış bir Kullanıcı, öznitelik tarafından düzenlenmiş eylemlerden veya denetleyicilerden birine erişmeyi denediğinde, kimlik doğrulama sınaması otomatik olarak gerçekleşir `[Authorize]` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıç hakkında tam açıklama dahil olmak üzere uygulamalar ve yeni özellikler oluşturmaya yönelik eksiksiz bir adım adım kılavuz için, ASP.NET öğreticisini deneyin.

> [!div class="nextstepaction"]
> [ASP.NET Web uygulamasına oturum açma ekleme](tutorial-v2-asp-webapp.md)

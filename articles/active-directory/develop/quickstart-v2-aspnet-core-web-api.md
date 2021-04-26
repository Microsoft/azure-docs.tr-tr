---
title: "Hızlı başlangıç: ASP.NET Core Web API 'sini Microsoft Identity platformu ile koruma | Mavisi"
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, yetkilendirme için Microsoft Identity platformunu kullanarak bir ASP.NET Core Web API 'sini nasıl koruyabileceğinizi gösteren bir kod örneği indirip değiştirirsiniz.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 30593c51f17b99989409ddd22c9c1caa28468039
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720840"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Hızlı başlangıç: ASP.NET Core Web API 'sini Microsoft Identity platformu ile koruma

Bu hızlı başlangıçta, bir ASP.NET Core Web API kodu örneği indirir ve kaynak erişimini yalnızca yetkili hesaplara kısıtlama şeklini inceleyebilirsiniz. Örnek, tüm Azure Active Directory (Azure AD) kuruluşunda kişisel Microsoft hesaplarının ve hesaplarının yetkilendirmesini destekler.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Önkoşullar
>
> - Etkin aboneliği olan Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Azure Active Directory kiracı](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) veya [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>1. Adım: uygulamayı kaydetme
>
> İlk olarak, Web API 'sini Azure AD kiracınıza kaydedin ve aşağıdaki adımları izleyerek bir kapsam ekleyin:
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
> 1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Azure Active Directory**'yi bulun ve seçin.
> 1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
> 1. **Ad** için, uygulamanız için bir ad girin. Örneğin, **Aspnetcorewebapi-QuickStart** yazın. Uygulamanızın kullanıcıları bu adı görür ve daha sonra değiştirebilirsiniz.
> 1. **Kaydet**’i seçin.
> 1. **Yönet**' in altında **bir API 'yi kullanıma**  >  **ekleme kapsam Ekle**' yi seçin. **Uygulama kimliği URI 'si** için **Kaydet ve devam et**' i seçerek varsayılanı kabul edin ve ardından aşağıdaki ayrıntıları girin:
>    - **Kapsam adı**: `access_as_user`
>    - **Kimler izin verebilir?**: **Yöneticiler ve kullanıcılar**
>    - **Yönetici onayı görünen adı**: `Access AspNetCoreWebApi-Quickstart`
>    - **Yönetici onay açıklaması**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Kullanıcı izni görünen adı**: `Access AspNetCoreWebApi-Quickstart`
>    - **Kullanıcı onay açıklaması**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Durum**: **etkin**
> 1. Kapsam **eklemeyi tamamlayacak kapsam Ekle** ' yi seçin.

## <a name="step-2-download-the-aspnet-core-project"></a>2. Adım: ASP.NET Core projesini Indirin

> [!div renderon="docs"]
> [ASP.NET Core çözümünü](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) GitHub 'dan indirin.

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>3. Adım: ASP.NET Core projesini yapılandırma
>
> Bu adımda, örnek kodu daha önce oluşturduğunuz uygulama kaydıyla çalışacak şekilde yapılandırın.
>
> 1. . Zip arşivini sürücünüzün köküne yakın bir klasöre ayıklayın. Örneğin, *C:\Azure-Samples*' a ayıklayın.
>
>    Windows üzerinde yol uzunluğu sınırlamalarından kaynaklanan hataları önlemek için, arşivin sürücünüzün köküne yakın bir dizine ayıklanmasını öneririz.
>
> 1. Çözümü kod düzenleyicinizdeki *WebApi* klasöründe açın.
> 1. appsettings.jsdosya *üzerinde* açın ve aşağıdaki kodu değiştirin:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - `Enter_the_Application_Id_here`Azure Portal kaydettiğiniz uygulamanın uygulama (istemci) kimliğiyle değiştirin. Uygulamanın **genel bakış** sayfasında uygulama (ISTEMCI) kimliğini bulabilirsiniz.
>    - `Enter_the_Tenant_Info_Here`Aşağıdakilerden biriyle değiştirin:
>       - Uygulamanız **yalnızca bu kuruluş dizinindeki hesapları** destekliyorsa, bu değeri dizin (KIRACı) KIMLIĞI (GUID) veya kiracı adı (örneğin,) ile değiştirin `contoso.onmicrosoft.com` . Uygulamanın **genel bakış** sayfasında Dizin (KIRACı) kimliğini bulabilirsiniz.
>       - Uygulamanız **herhangi bir kuruluş dizinindeki hesapları** destekliyorsa, bu değeri ile değiştirin `organizations` .
>       - Uygulamanız **tüm Microsoft hesabı kullanıcıları** destekliyorsa, bu değeri olarak bırakın `common` .
>
> Bu hızlı başlangıçta, dosyadaki *appsettings.js* başka bir değeri değiştirmeyin.

## <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı

Web API 'SI bir istemci uygulamasından bir belirteç alır ve Web API 'sindeki kod belirteci doğrular. Bu senaryo [Senaryo: korumalı Web API 'si](scenario-protected-web-api-overview.md)ile daha ayrıntılı olarak açıklanmıştır.

### <a name="startup-class"></a>Başlangıç sınıfı

*Microsoft. AspNetCore. Authentication* ara yazılımı, `Startup` barındırma işlemi başladığında yürütülen bir sınıfı kullanır. `ConfigureServices`Yönteminde, `AddMicrosoftIdentityWebApi` *Microsoft. Identity. Web* tarafından verilen uzantı yöntemi çağırılır.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

`AddAuthentication()`Yöntemi, hizmeti Jwttaşıyıcı tabanlı kimlik doğrulaması ekleyecek şekilde yapılandırır.

İçeren satır, `.AddMicrosoftIdentityWebApi` Web API 'Nize Microsoft Identity platform yetkilendirmesi ekler. Daha sonra, `AzureAD` yapılandırma dosyası *appsettings.js* bölümündeki bilgileri temel alarak Microsoft Identity platformu tarafından verilen erişim belirteçlerini doğrulamak üzere yapılandırılmıştır:

| *appsettings.js* anahtarda | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure portal kayıtlı uygulamanın uygulama (istemci) KIMLIĞI.                                                                                       |
| `Instance`             | Kullanıcının kimlik doğrulaması için güvenlik belirteci hizmeti (STS) uç noktası. Bu değer genellikle `https://login.microsoftonline.com/` Azure genel bulutu 'nı gösterir. |
| `TenantId`             | Kiracınızın adı veya kiracı KIMLIĞI (bir GUID) ya da `common` iş veya okul hesaplarıyla veya Microsoft kişisel hesaplarıyla kullanıcıları oturum açmak için.                             |

`Configure()`Yöntemi, `app.UseAuthentication()` adlandırılmış işlevlerini etkinleştiren iki önemli yöntem içerir `app.UseAuthorization()` :

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Denetleyiciyi, denetleyicinin yöntemini veya Razor sayfasını koruma

Özniteliğini kullanarak bir denetleyiciyi veya denetleyici yöntemlerini koruyabilirsiniz `[Authorize]` . Bu öznitelik yalnızca kimliği doğrulanmış kullanıcılara izin vererek denetleyiciye veya yöntemlere erişimi kısıtlar. Kullanıcının kimliği doğrulanmadıysa, denetleyiciye erişmek için bir kimlik doğrulama sınaması başlatılabilir.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>Denetleyicide kapsam doğrulaması

API 'deki kod, aşağıdakileri kullanarak, gerekli kapsamların belirteçte olduğunu doğrular `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` :

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu ASP.NET Core Web API kodu örneğini içeren GitHub deposu, şu şekilde nasıl yapılacağını gösteren yönergeler ve daha fazla kod örneği içerir:

- Yeni bir ASP.NET Core Web API 'sine kimlik doğrulaması ekleyin.
- Web API 'sini bir masaüstü uygulamasından çağırın.
- Microsoft Graph ve diğer Microsoft API 'Leri gibi aşağı akış API 'Lerini çağırın.

> [!div class="nextstepaction"]
> [GitHub 'da Web API öğreticilerini ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)

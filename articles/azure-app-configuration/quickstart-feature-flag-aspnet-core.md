---
title: ASP.NET Core özellik bayrakları eklemeye yönelik hızlı başlangıç | Microsoft Docs
description: ASP.NET Core uygulamalara özellik bayrakları eklemeye ve bunları Azure Uygulama yapılandırmasında yönetmeye yönelik bir hızlı başlangıç
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 778f1568038ef6e4f073ef0a5eb75d193f661a9c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129734"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Hızlı başlangıç: ASP.NET Core uygulamasına özellik bayrakları ekleme

Bu hızlı başlangıçta, özellik yönetiminin uçtan uca bir uygulamasını oluşturmak için Azure uygulama yapılandırmasını bir ASP.NET Core Web uygulamasına katabilirsiniz. Tüm özellik Bayraklarınızı merkezi olarak depolamak ve durumlarını denetlemek için uygulama yapılandırma hizmetini kullanabilirsiniz. 

.NET Core Özellik Yönetimi kitaplıkları Framework 'ü kapsamlı özellik bayrağı desteğiyle genişletir. Bu kitaplıklar, .NET Core yapılandırma sisteminin üzerine kurulmuştur. Bunlar, .NET Core yapılandırma sağlayıcısı aracılığıyla uygulama yapılandırmasıyla sorunsuz bir şekilde tümleşir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Aşağıdaki özellik bayraklarını eklemek için **özellik yöneticisi** >  **+ Ekle** ' yi seçin:

    | Anahtar | Durum |
    |---|---|
    | Beta | Kapalı |

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core web uygulaması oluşturma

Yeni bir ASP.NET Core MVC web uygulaması projesi oluşturmak için [.NET Core komut satırı arabirimini (CLI)](https://docs.microsoft.com/dotnet/core/tools/) kullanabilirsiniz. Visual Studio yerine .NET Core CLI kullanmanın avantajı, .NET Core CLI Windows, macOS ve Linux platformları arasında kullanılabilir.

1. Projeniz için yeni bir klasör oluşturun. Bu hızlı başlangıç için *Testfeatureflags*olarak adlandırın.

1. Yeni klasörde, yeni bir ASP.NET Core MVC web uygulaması projesi oluşturmak için aşağıdaki komutu çalıştırın:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Gizli dizi Yöneticisi ekleme

Gizli dizi [Yöneticisi aracını](https://docs.microsoft.com/aspnet/core/security/app-secrets) projenize ekleyin. Gizli dizi Yöneticisi Aracı, geliştirme çalışması için hassas verileri proje ağacınızdaki dışında depolar. Bu yaklaşım, uygulama gizli dizilerini kaynak kodunun içinde yanlışlıkla paylaşmayı önlemeye yardımcı olur.

1. *. Csproj* dosyasını açın.
1. Aşağıdaki örnekte gösterildiği gibi bir `UserSecretsId` öğesi ekleyin ve değerini, genellikle GUID olan kendi değeri ile değiştirin:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

1. Dosyayı kaydedin.

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

1. Aşağıdaki komutları çalıştırarak `Microsoft.Azure.AppConfiguration.AspNetCore` ve `Microsoft.FeatureManagement.AspNetCore` NuGet paketlerine başvuru ekleyin:

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009470001-12
    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-009000001-1251
    ```

1. Projenizin paketlerini geri yüklemek için aşağıdaki komutu çalıştırın:

    ```
    dotnet restore
    ```

1. Gizli dizi adlı **ConnectionString: AppConfig** adında bir gizli dizi ekleyin.

    Bu gizli anahtar, uygulama yapılandırma deponuza erişmek için bağlantı dizesini içerir. Aşağıdaki komutta `<your_connection_string>` değerini, uygulama yapılandırma deponuzdaki bağlantı dizesiyle değiştirin.

    Bu komut, *.csproj* dosyası ile aynı dizinde yürütülmelidir.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Gizli dizi Yöneticisi yalnızca Web uygulamasını yerel olarak test etmek için kullanılır. Uygulamayı [Azure App Service](https://azure.microsoft.com/services/app-service)dağıtırken, örneğin, bağlantı dizesini depolamak Için gizli yönetici kullanmak yerine App Service **bağlantı dizeleri** adlı bir uygulama ayarı kullanırsınız.

    Bu gizli dizi ile App Configuration API 'sini kullanabilirsiniz. İki nokta (:) yapılandırma adında, desteklenen tüm platformlarda uygulama yapılandırma API 'SI ile birlikte kullanılır. Bkz. [ortama göre yapılandırma](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. *Program.cs*'i açın ve .NET Core uygulama yapılandırma sağlayıcısına bir başvuru ekleyin:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. `config.AddAzureAppConfiguration()` metodunu çağırarak uygulama yapılandırmasını kullanmak için `CreateWebHostBuilder` yöntemini güncelleştirin.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` `CreateWebHostBuilder` .NET Core 3,0 ' de yer alır.  Ortamınıza göre doğru söz dizimini seçin.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>.NET Core 2. x için güncelleştirme `CreateWebHostBuilder`

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                        .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>.NET Core 3. x için `CreateHostBuilder` güncelleştirme

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```


1. *Startup.cs*'i açın ve .NET Core Feature Manager 'a başvurular ekleyin:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. `services.AddFeatureManagement()` metodunu çağırarak özellik bayrağı desteği eklemek için `ConfigureServices` yöntemini güncelleştirin. İsteğe bağlı olarak, `services.AddFeatureFilter<FilterType>()`çağırarak özellik bayraklarıyla birlikte kullanılacak herhangi bir filtre ekleyebilirsiniz:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. ASP.NET Core Web uygulaması istekleri almaya devam ederken, özellik bayrağı değerlerinin yinelenen bir aralıkta yenilenmesini sağlamak üzere bir ara yazılım eklemek için `Configure` yöntemini güncelleştirin.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

1. *MyFeatureFlags.cs* dosyası ekleyin:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. *Controllers* dizinine *BetaController.cs* ekleyin:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. *Görünümler* dizininde *_ViewImports. cshtml* dosyasını açın ve Feature Manager etiketi yardımcısını ekleyin:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. *Görünümler*\\*paylaşılan* dizinde *_Layout. cshtml* dosyasını açın ve `<body>` > altındaki `<nav>` barkodunu aşağıdaki kodla değiştirin:`<header>`

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. *Görünümler* altında bir *Beta* dizini oluşturun ve buna *Index. cshtml* ekleyin:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. .NET Core CLI kullanarak uygulamayı derlemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

    ```
    dotnet build
    ```

1. Oluşturma başarıyla tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

    ```
    dotnet run
    ```

1. Bir tarayıcı penceresi açın ve yerel olarak barındırılan Web uygulamasının varsayılan URL 'SI olan `https://localhost:5001`' a gidin.

    ![Hızlı başlangıç uygulaması başlatma yerel](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

1. **Özellik Yöneticisi**' ni seçin ve **Beta** anahtarının durumunu **Açık**olarak değiştirin:

    | Anahtar | Durum |
    |---|---|
    | Beta | Açık |

1. Komut isteminizde yeniden geçerek ve çalışan `dotnet` işlemini iptal etmek için `Ctrl-C` tuşuna bastıktan sonra `dotnet run`yeniden çalıştırarak uygulamanızı yeniden başlatın.

1. Yeni yapılandırma ayarlarını görmek için tarayıcı sayfasını yenileyin.

    ![Hızlı başlangıç uygulaması başlatma yerel](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta yeni bir uygulama yapılandırma deposu oluşturdunuz ve bunu, [Özellik Yönetimi kitaplıkları](https://go.microsoft.com/fwlink/?linkid=2074664)aracılığıyla bir ASP.NET Core Web uygulamasındaki özellikleri yönetmek için kullandınız.

- [Özellik yönetimi](./concept-feature-management.md)hakkında daha fazla bilgi edinin.
- [Özellik bayraklarını yönetin](./manage-feature-flags.md).
- [ASP.NET Core uygulamasında Özellik bayraklarını kullanın](./use-feature-flags-dotnet-core.md).
- [ASP.NET Core uygulamasında dinamik yapılandırma kullanma](./enable-dynamic-configuration-aspnet-core.md)

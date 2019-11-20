---
title: Azure Uygulama yapılandırması için ASP.NET Core ile hızlı başlangıç | Microsoft Docs
description: Azure uygulama yapılandırmasını ASP.NET Core uygulamalarla kullanmaya yönelik hızlı başlangıç
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 10/11/2019
ms.author: yegu
ms.openlocfilehash: 4e08192788329e7a835ddb0b6b3f1aa01b2c73e1
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299943"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Hızlı başlangıç: Azure Uygulama yapılandırmasıyla ASP.NET Core uygulaması oluşturma

Bu hızlı başlangıçta, kodınızdan ayrı uygulama ayarlarının depolanmasını ve yönetimini merkezileştirmek için Azure uygulama yapılandırmasını bir ASP.NET Core uygulamasına katabilirsiniz. ASP.NET Core, bir uygulama tarafından belirtilen bir veya daha fazla veri kaynağından ayarları kullanarak tek bir anahtar-değer tabanlı yapılandırma nesnesi oluşturur. Bu veri kaynakları *yapılandırma sağlayıcıları*olarak bilinir. Uygulama yapılandırmasının .NET Core istemcisi bu tür bir sağlayıcı olarak uygulandığından, hizmet başka bir veri kaynağı gibi görünür.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Aşağıdaki anahtar-değer çiftlerini eklemek için **yapılandırma gezgini** >  **+ Oluştur** ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: BackgroundColor | Beyaz |
    | TestApp: ayarlar: FontSize | 24 |
    | TestApp: ayarlar: FontColor | Siyah |
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından veriler |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core web uygulaması oluşturma

Yeni bir ASP.NET Core MVC web uygulaması projesi oluşturmak için [.NET Core komut satırı arabirimini (CLI)](https://docs.microsoft.com/dotnet/core/tools/) kullanabilirsiniz. Visual Studio üzerinden .NET Core CLI kullanmanın avantajı, Windows, macOS ve Linux platformları genelinde kullanılabilir hale gelir.

1. Projeniz için yeni bir klasör oluşturun. Bu hızlı başlangıç için, *Testappconfig*olarak adlandırın.

2. Yeni klasörde, yeni bir ASP.NET Core MVC web uygulaması projesi oluşturmak için aşağıdaki komutu çalıştırın:

    ```CLI
        dotnet new mvc --no-https
    ```

## <a name="add-secret-manager"></a>Gizli dizi Yöneticisi ekleme

Gizli dizi Yöneticisi 'ni kullanmak için *. csproj* dosyanıza `UserSecretsId` öğesi ekleyin.

- *. Csproj* dosyasını açın. Burada gösterildiği gibi `UserSecretsId` öğesi ekleyin. Aynı GUID 'i kullanabilir veya bu değeri kendi kendinizinkini kullanarak değiştirebilirsiniz. Dosyayı kaydedin.

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

Gizli Dizi Yöneticisi aracı, geliştirme işine yönelik hassas verileri proje ağacınızın dışında depolar. Bu yaklaşım, uygulama gizli dizilerini kaynak kodunun içinde yanlışlıkla paylaşmayı önlemeye yardımcı olur. Gizli yönetici hakkında daha fazla bilgi için lütfen bkz. [ASP.NET Core geliştirme sırasında uygulama gizli dizileri Için güvenli depolama](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

1. Aşağıdaki komutu çalıştırarak `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet paketine bir başvuru ekleyin:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```
2. Projenizin paketlerini geri yüklemek için aşağıdaki komutu çalıştırın:

    ```CLI
        dotnet restore
    ```
3. Gizli dizi adlı *ConnectionString: AppConfig* adında bir gizli dizi ekleyin.

    Bu gizli anahtar, uygulama yapılandırma deponuza erişmek için bağlantı dizesini içerir. Aşağıdaki komutta bulunan değeri, uygulama yapılandırma deponuzdaki bağlantı dizesiyle değiştirin.

    Bu komut, *.csproj* dosyası ile aynı dizinde yürütülmelidir.

    ```CLI
        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Bazı kabuklar, tırnak içine alınmadığı takdirde bağlantı dizesini keser. @No__t-0 komutunun çıktısının tüm bağlantı dizesini belirttiğinden emin olun. Aksi takdirde, bağlantı dizesini tırnak içine alarak komutu yeniden çalıştırın.

    Gizli dizi Yöneticisi yalnızca Web uygulamasını yerel olarak test etmek için kullanılır. Uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/web)dağıtıldığında, örneğin, bağlantı dizesini depolamak Için gizli yönetici yerine App Service bir uygulama ayarı **bağlantı** dizesi kullanırsınız.

    Bu gizli dizi Yapılandırma API 'siyle erişilir. İki nokta (:) yapılandırma adında, desteklenen tüm platformlarda Yapılandırma API 'SI ile birlikte kullanılır. Bkz. [ortama göre yapılandırma](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. *Program.cs*'i açın ve .NET Core uygulama yapılandırma sağlayıcısına bir başvuru ekleyin.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. @No__t-0 yöntemini, `config.AddAzureAppConfiguration()` yöntemini çağırarak uygulama yapılandırmasını kullanacak şekilde güncelleştirin.
    
    > [!IMPORTANT]
    > `CreateHostBuilder`, .NET Core 3,0 ' de `CreateWebHostBuilder` ' i değiştirir.  Ortamınıza göre doğru söz dizimini seçin.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>.NET Core 2. x için güncelleştirme `CreateWebHostBuilder`

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>.NET Core 3. x için güncelleştirme `CreateHostBuilder`

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

6. Görünümler > giriş dizininde *Index. cshtml* dosyasını açın ve içeriğini aşağıdaki kodla değiştirin:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Görünümler > paylaşılan dizinde *_Layout. cshtml* dosyasını açın ve içeriğini aşağıdaki kodla değiştirin:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. .NET Core CLI kullanarak uygulamayı derlemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

    ```CLI
       dotnet build
    ```

2. Oluşturma başarıyla tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

    ```CLI
        dotnet run
    ```

3. Bir tarayıcı penceresi açın ve yerel olarak barındırılan Web uygulamasının varsayılan URL 'SI olan `http://localhost:5000` ' a gidin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, yeni bir uygulama yapılandırma deposu oluşturdunuz ve bunu [uygulama yapılandırma sağlayıcısı](https://go.microsoft.com/fwlink/?linkid=2074664)aracılığıyla bir ASP.NET Core Web uygulamasıyla kullandınız. Uygulama yapılandırmasını kullanma hakkında daha fazla bilgi edinmek için, Web uygulamanızı yapılandırma ayarlarını dinamik olarak yenilemek üzere nasıl yapılandıracağınızı gösteren bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [ASP.NET Core uygulamasında dinamik yapılandırma kullanma](./enable-dynamic-configuration-aspnet-core.md)

---
title: ASP.NET Core uygulamasında Azure Uygulama yapılandırması dinamik yapılandırmasını kullanma öğreticisi | Microsoft Docs
description: Bu öğreticide ASP.NET Core uygulamalar için yapılandırma verilerini dinamik olarak güncelleştirme hakkında bilgi edineceksiniz.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 7fc7bd6fa0067857bde64d43be5799bd50712490
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469687"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Öğretici: ASP.NET Core uygulamasında dinamik yapılandırmayı kullanma

ASP.NET Core, çeşitli kaynaklardan yapılandırma verilerini okuyabilen takılabilir bir yapılandırma sistemine sahiptir. Uygulamanın yeniden başlatılmasına neden olmadan anında değişiklikleri işleyebilir. ASP.NET Core, yapılandırma ayarlarının kesin belirlenmiş .NET sınıflarına bağlamasını destekler. Çeşitli `IOptions<T>` düzenlerini kullanarak bunları kodunuza çıkartır. Bu modellerden biri, temelde `IOptionsSnapshot<T>`, temel alınan veriler değiştiğinde uygulamanın yapılandırmasını otomatik olarak yeniden yükler. Azure Uygulama yapılandırmasında depolanan en son yapılandırmaya erişmek için uygulamanızdaki denetleyicilere `IOptionsSnapshot<T>` ekleyebilirsiniz.

Ayrıca, bir ara yazılım kullanarak bir yapılandırma ayarları kümesini dinamik olarak yenilemek için, uygulama yapılandırması ASP.NET Core istemci Kitaplığı ' nı da ayarlayabilirsiniz. Web uygulaması istekleri almaya devam ettiği sürece yapılandırma ayarları yapılandırma deposu ile çalışmaya devam eder.

Ayarları güncel tutmak ve yapılandırma deposuna çok fazla çağrı önlemek için, her bir ayar için bir önbellek kullanılır. Bir ayarın önbelleğe alınmış değeri sona erene kadar, yenileme işlemi değeri, değer yapılandırma deposunda değiştiği zaman bile güncelleştirmez. Her istek için varsayılan kullanım süresi 30 saniyedir, ancak gerekirse geçersiz kılınabilir.

Bu öğreticide, kodunuzda dinamik yapılandırma güncelleştirmelerini nasıl uygulayabileceğinizi gösterir. Hızlı başlangıçlarda tanıtılan web uygulamasında oluşturulur. Devam etmeden önce, önce [uygulama yapılandırması ile bir ASP.NET Core uygulaması oluşturun](./quickstart-aspnet-core-app.md) .

Bu öğreticideki adımları uygulamak için herhangi bir kod düzenleyicisi kullanabilirsiniz. [Visual Studio Code](https://code.visualstudio.com/) , Windows, MacOS ve Linux platformlarında kullanılabilen harika bir seçenektir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Uygulama yapılandırma deposundaki değişikliklere yanıt olarak yapılandırmasını güncelleştirmek için uygulamanızı ayarlayın.
> * En son yapılandırmayı uygulamanızın denetleyicilerine ekleme.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi yapmak için [.NET Core SDK](https://dotnet.microsoft.com/download)' yi yüklemelisiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Devam etmeden önce, önce [uygulama yapılandırması ile bir ASP.NET Core uygulaması oluşturun](./quickstart-aspnet-core-app.md) .

## <a name="reload-data-from-app-configuration"></a>Uygulama yapılandırmasından verileri yeniden yükleme

1. Aşağıdaki komutu çalıştırarak `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet paketine bir başvuru ekleyin:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```

1. *Program.cs*öğesini açın ve `config.AddAzureAppConfiguration()` metodunu eklemek için `CreateWebHostBuilder` yöntemini güncelleştirin.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                           {
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    `ConfigureRefresh` yöntemi, bir yenileme işlemi tetiklendiğinde, yapılandırma verilerini uygulama yapılandırma deposu ile güncelleştirmek için kullanılan ayarları belirtmek için kullanılır. Yenileme işlemini gerçekten tetikleyebilmek için, herhangi bir değişiklik gerçekleştiğinde uygulamanın yapılandırma verilerini yenilemesi için bir yenileme ara yazılımı yapılandırılması gerekir.

2. Yeni bir `Settings` sınıfını tanımlayan ve uygulayan bir *Settings.cs* dosyası ekleyin.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. *Startup.cs*' i açın ve yapılandırma verilerini `Settings` sınıfına bağlamak için `ConfigureServices` yöntemi güncelleştirin.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

4. ASP.NET Core Web uygulaması istekleri almaya devam ederken yenileme için kaydedilen yapılandırma ayarlarının güncelleştirilmesini sağlamak üzere bir ara yazılım eklemek için `Configure` yöntemini güncelleştirin.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    Ara yazılım, ASP.NET Core Web uygulaması tarafından alınan her istek için yenileme tetiklenmesi için `Program.cs` `AddAzureAppConfiguration` yönteminde belirtilen yenileme yapılandırmasını kullanır. Her istek için bir yenileme işlemi tetiklenir ve istemci kitaplığı, kayıtlı yapılandırma ayarları için önbelleğe alınan değerin dolduğunu kontrol eder. Tarihi geçen önbelleğe alınmış değerler için, ayarların değerleri uygulama yapılandırma deposuyla güncelleştirilir ve kalan değerler değişmeden kalır.
    
    > [!NOTE]
    > Yapılandırma ayarı için varsayılan önbellek süre sonu zamanı 30 saniyedir, ancak `SetCacheExpiration` yöntemi bir bağımsız değişken olarak geçirilen seçenekler başlatıcısında `ConfigureRefresh` yöntemine çağırarak geçersiz kılınabilir.

## <a name="use-the-latest-configuration-data"></a>En son yapılandırma verilerini kullan

1. *HomeController.cs* ' ı denetleyiciler dizininde açın ve `Microsoft.Extensions.Options` paketine bir başvuru ekleyin.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Bağımlılık ekleme yoluyla `Settings` almak için `HomeController` sınıfını güncelleştirin ve değerlerini kullanın.

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

3. Görünümler > giriş dizininde *Index. cshtml* dosyasını açın ve içeriğini aşağıdaki komut dosyasıyla değiştirin:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. .NET Core CLI kullanarak uygulamayı derlemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

        dotnet build

2. Oluşturma başarıyla tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

        dotnet run

3. Bir tarayıcı penceresi açın ve yerel olarak barındırılan Web uygulamasının varsayılan URL 'SI olan `http://localhost:5000`' a gidin.

    ![Hızlı başlangıç uygulaması başlatma yerel](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

5. **Yapılandırma Gezgini**' ni seçin ve aşağıdaki anahtarların değerlerini güncelleştirin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: BackgroundColor | yeşil |
    | TestApp: ayarlar: FontColor | Açık gri |
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından alınan veriler-canlı güncelleştirmeler ile şimdi! |

6. Yeni yapılandırma ayarlarını görmek için tarayıcı sayfasını yenileyin. Değişikliklerin yansıtılması için tarayıcı sayfasının birden fazla yenilenmesi gerekebilir.

    ![Hızlı başlangıç uygulaması yerel yenileme](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Yapılandırma ayarları varsayılan süre sonu 30 saniyelik bir süre içinde önbellekte bulunduğundan, uygulama yapılandırma deposundaki ayarlarda yapılan değişiklikler yalnızca önbelleğin süresi dolduğunda Web uygulamasına yansıtılır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulama yapılandırmasına erişimi kolaylaştırmak ve uygulamanız için kimlik bilgisi yönetimini geliştirmek üzere bir Azure yönetilen hizmet kimliği eklediniz. Uygulama yapılandırmasını kullanma hakkında daha fazla bilgi için Azure CLı örneklerine devam edin.

> [!div class="nextstepaction"]
> [CLI örnekleri](./cli-samples.md)

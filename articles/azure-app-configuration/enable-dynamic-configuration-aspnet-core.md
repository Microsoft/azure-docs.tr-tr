---
title: "Öğretici: ASP.NET Core 'de uygulama yapılandırması dinamik yapılandırması 'Nı kullanın"
titleSuffix: Azure App Configuration
description: Bu öğreticide ASP.NET Core uygulamalar için yapılandırma verilerini dinamik olarak güncelleştirme hakkında bilgi edineceksiniz.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 083bd56b2b211d11206a277bf31eea797b37cdb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979938"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Öğretici: ASP.NET Core uygulamasında dinamik yapılandırmayı kullanma

ASP.NET Core, çeşitli kaynaklardan yapılandırma verilerini okuyabilen takılabilir bir yapılandırma sistemine sahiptir. Bir uygulamanın yeniden başlatılmasına neden olmadan değişiklikleri dinamik olarak işleyebilir. ASP.NET Core, yapılandırma ayarlarının kesin belirlenmiş .NET sınıflarına bağlamasını destekler. Çeşitli desenleri kullanarak bunları kodunuza çıkartır `IOptions<T>` . Bu modellerden biri, `IOptionsSnapshot<T>` temel alınan veriler değiştiğinde uygulamanın yapılandırmasını otomatik olarak yeniden yükler. `IOptionsSnapshot<T>`Azure Uygulama yapılandırmasında depolanan en son yapılandırmaya erişmek için uygulamanızdaki denetleyicilere ekleyebilirsiniz.

Ayrıca, bir ara yazılım kullanarak bir yapılandırma ayarları kümesini dinamik olarak yenilemek için, uygulama yapılandırması ASP.NET Core istemci Kitaplığı ' nı da ayarlayabilirsiniz. Web uygulaması istekleri aldığı sürece yapılandırma ayarları her seferinde yapılandırma deposu ile güncelleştirilir.

Yapılandırma deposuna çok fazla çağrı yapmaktan kaçınmak için uygulama yapılandırması her ayarı otomatik olarak önbelleğe alır. Yenileme işlemi, yapılandırma deposundaki değeri değiştiğinde bile bu ayarı güncelleştirmek için bir ayarın önbelleğe alınmış değerinin süresinin dolacağını bekler. Varsayılan önbellek süre sonu zamanı 30 saniyedir. Gerekirse bu süre sonu süresini geçersiz kılabilirsiniz.

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

## <a name="add-a-sentinel-key"></a>Sentinel anahtarı ekleme

*Sentinel anahtarı* , yapılandırma değiştirildiğinde sinyal almak için kullanılan özel bir anahtardır. Uygulamanız, değişiklikler için Sentinel anahtarını izler. Bir değişiklik algılandığında, tüm yapılandırma değerlerini yenileyebilirsiniz. Bu yaklaşım, uygulamanız tarafından uygulama yapılandırması için yapılan isteklerin genel sayısını azaltarak tüm anahtarların değişiklikler için izlenmesiyle karşılaştırılır.

1. Azure portal, **yapılandırma gezgini > oluştur > anahtar-değer**' i seçin.
1. **Anahtar** için *TestApp: Settings: Sentinel* yazın. **Değer** için 1 girin. **Etiketi** ve **içerik türünü** boş bırakın.
1. **Uygula**’yı seçin.

> [!NOTE]
> Sentinel anahtarı kullanmıyorsanız, izlemek istediğiniz her anahtarı el ile kaydetmeniz gerekir.

## <a name="reload-data-from-app-configuration"></a>Uygulama yapılandırmasından verileri yeniden yükleme

1. `Microsoft.Azure.AppConfiguration.AspNetCore`Aşağıdaki komutu çalıştırarak NuGet paketine bir başvuru ekleyin:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. *Program. cs*' yi açın ve yöntemi `CreateWebHostBuilder` eklemek için yöntemi güncelleştirin `config.AddAzureAppConfiguration()` .

   #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```   
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```
    ---

    `ConfigureRefresh`Yöntemi, bir yenileme işlemi tetiklendiğinde, yapılandırma verilerini uygulama yapılandırma deposu ile güncelleştirmek için kullanılan ayarları belirtmek için kullanılır. `refreshAll`Yöntemi parametresi, `Register` Sentinel anahtarı değişirse tüm yapılandırma değerlerinin yenilenmesi gerektiğini gösterir.

    Ayrıca, `SetCacheExpiration` yöntemi 30 saniyelik varsayılan önbellek süre sonu süresini geçersiz kılar, bunun yerine 5 dakikalık bir saat belirtin. Bu, uygulama yapılandırmasına yapılan isteklerin sayısını azaltır.

    > [!NOTE]
    > Sınama amacıyla, önbelleğin süre sonu süresini azaltmak isteyebilirsiniz.

    Bir yenileme işlemini gerçekten tetiklemek için, herhangi bir değişiklik gerçekleştiğinde uygulamanın yapılandırma verilerini yenilemesi için bir yenileme ara yazılımı yapılandırmanız gerekir. Bu, sonraki bir adımda nasıl yapılacağını göreceksiniz.

2. Yeni bir sınıfı tanımlayan ve uygulayan Controllers dizinine bir *Settings. cs* dosyası ekleyin `Settings` . Ad alanını projenizin adıyla değiştirin. 

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

3. *Başlangıç. cs*' yi açın ve `IServiceCollection.Configure<T>` yöntemi içinde kullanarak `ConfigureServices` yapılandırma verilerini `Settings` sınıfa bağlayın.

    #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        services.AddAzureAppConfiguration();
    }
    ```
    ---
    > [!Tip]
    > Yapılandırma değerlerini okurken seçenekler deseni hakkında daha fazla bilgi için, [ASP.NET Core Içindeki seçenekler desenlerine](/aspnet/core/fundamentals/configuration/options)bakın.

4. `Configure` `UseAzureAppConfiguration` ASP.NET Core Web uygulaması istekleri almaya devam ederken, yenilemenin güncelleştirilmesi için kaydedilen yapılandırma ayarlarının güncelleştirilmesine izin vermek üzere ara yazılımı ekleyerek yöntemini güncelleştirin.


    #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```
    ---
    
    Ara yazılım, `AddAzureAppConfiguration` `Program.cs` ASP.NET Core Web uygulaması tarafından alınan her istek için yenileme tetiklemesi için içindeki yönteminde belirtilen yenileme yapılandırmasını kullanır. Her istek için bir yenileme işlemi tetiklenir ve istemci kitaplığı, kayıtlı yapılandırma ayarı için önbelleğe alınan değerin dolup dolmadığını denetler. Bu süre dolmuşsa, yenilenir.

    > [!NOTE]
    > Yapılandırmanın yenilenmesini sağlamak için, ara yazılımı istek ardışık düzenine uygun olarak erken bir şekilde ekleyin, böylece uygulamanızdaki başka bir ara yazılım tarafından kısa devre dışı olmayacaktır.

## <a name="use-the-latest-configuration-data"></a>En son yapılandırma verilerini kullan

1. Controllers dizinindeki *HomeController. cs* ' yi açın ve pakete bir başvuru ekleyin `Microsoft.Extensions.Options` .

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. `HomeController`Bağımlılık ekleme yoluyla almak için sınıfı güncelleştirin `Settings` ve değerlerini kullanın.

 #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
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

        // ...
    }
```
#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
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

        // ...
    }
```
#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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
---



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
            font-size: @ViewData["FontSize"]px;
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

    ```console
        dotnet build
    ```

1. Oluşturma başarıyla tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

    ```console
        dotnet run
    ```

1. Bir tarayıcı penceresi açın ve çıktıda gösterilen URL 'ye gidin `dotnet run` .

    ![Hızlı başlangıç uygulamasını yerel olarak başlatma](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

1. **Yapılandırma Gezgini**' ni seçin ve aşağıdaki anahtarların değerlerini güncelleştirin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: BackgroundColor | green |
    | TestApp: ayarlar: FontColor | Açık gri |
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından alınan veriler-canlı güncelleştirmeler ile şimdi! |
    | TestApp: ayarlar: Sentinel | 2 |

1. Yeni yapılandırma ayarlarını görmek için tarayıcı sayfasını yenileyin. Değişikliklerin yansıtılması için birden çok kez yenilemeniz veya otomatik yenileme hızınızı 5 dakikadan kısa bir süre içinde değiştirmeniz gerekebilir. 

    ![Güncelleştirilmiş hızlı başlangıç uygulaması yerel olarak başlatılıyor](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulama yapılandırmasından yapılandırma ayarlarını dinamik olarak yenilemek için ASP.NET Core Web uygulamanızı etkinleştirdiniz. Uygulama yapılandırmasına erişimi kolaylaştırmak için Azure tarafından yönetilen bir kimlik kullanmayı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)

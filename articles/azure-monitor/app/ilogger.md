---
title: ILogger ile Azure Application Insights 'de .NET izleme günlüklerini keşfet
description: Azure Application Insights ILogger sağlayıcısını ASP.NET Core ve konsol uygulamalarıyla kullanma örnekleri.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0e93e2a98d96ca7f436f20e579ab625341024686
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819484"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>.NET Core ıllogger günlükleri için Applicationınsightsloggerprovider

ASP.NET Core, farklı türlerde yerleşik ve üçüncü taraf günlük sağlayıcılarla birlikte çalışarak günlüğe kaydetme API 'sini destekler. Günlüğe kaydetme, **günlük ()** veya *ıllogger* örneklerine ait bir değişken çağırarak yapılır. Bu makalede, *Applicationınsightsloggerprovider* ' ın konsol ve ASP.NET Core uygulamalarında ILogger günlüklerini yakalamak için nasıl kullanılacağı gösterilmektedir. Bu makalede, Applicationınsightsloggerprovider 'ın diğer Application Insights telemetrisi ile nasıl tümleştirildiğini de açıklanmaktadır.
Daha fazla bilgi için bkz. [oturum açma ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core uygulamalar

Applicationınsightsloggerprovider, standart yöntemlerden birini kullanarak düzenli Application Insights izlemeyi açtığınızda [Microsoft. ApplicationInsights. Aspnet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) Version 2.7.0-Beta3 (ve üzeri) içinde varsayılan olarak etkindir:
- Iwebhostbuilder üzerinde **Useapplicationınsights** genişletme yöntemini çağırarak 
- Ivicecollection üzerinde **Addavpplicationınsiıstelemetry** genişletme yöntemini çağırarak

Applicationınsightsloggerprovider yakalamaları, toplanan diğer telemetriyle aynı yapılandırmaya tabidir. Aynı TelemetryInitializers ve TelemetryProcessors kümesine sahiptir, aynı TelemetryChannel kullanır ve bağıntılı ve örneklenir ve diğer telemetriyle aynı şekilde örneklenir. 2\.7.0-Beta3 veya üzeri sürümü kullanıyorsanız, ILogger günlüklerini yakalamak için herhangi bir eylem gerekmez.

Yalnızca *Uyarı* veya daha yüksek bir ILogger günlüğü (tüm kategoriler) varsayılan olarak Application Insights gönderilir. Ancak, [Bu davranışı değiştirmek için filtre uygulayabilirsiniz](#control-logging-level). **Program.cs** veya **Startup.cs**' den ıllogger günlüklerini yakalamak için ek adımlar gereklidir. (Bkz. [ASP.NET Core uygulamalarında Startup.cs ve program.cs ' den ILogger günlüklerini yakalama](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Microsoft. ApplicationInsights. AspNet SDK 'sının önceki bir sürümünü kullanıyorsanız veya başka bir Application Insights izleme olmadan yalnızca Applicationınsightsloggerprovider kullanmak istiyorsanız aşağıdaki yordamı kullanın:

1. NuGet paketini yükler:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. **Program.cs** burada gösterildiği gibi değiştirin:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

2\. adımdaki kod `ApplicationInsightsLoggerProvider`yapılandırır. Aşağıdaki kod, günlükleri göndermek için `ILogger` kullanan örnek bir denetleyici sınıfını gösterir. Günlükler Application Insights tarafından yakalanır.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ASP.NET Core uygulamalarda Startup.cs ve Program.cs ' den ILogger günlüklerini yakala

> [!NOTE]
> ASP.NET Core 3,0 ve üzeri sürümlerde, artık Startup.cs ve Program.cs içinde `ILogger` eklemek mümkün değildir. Daha fazla bilgi için bkz. https://github.com/aspnet/Announcements/issues/353.

Yeni Applicationınsightsloggerprovider, uygulama başlatma ardışık düzeninde günlüklerin erken yakalanmasını sağlayabilir. Applicationınsightsloggerprovider Application Insights ' de otomatik olarak etkinleştirilebilir (2.7.0-Beta3 sürümünden itibaren), ardışık düzende daha sonra bir izleme anahtarı ayarlanmamış. Bu nedenle, yalnızca **Denetleyici**/diğer sınıflardan Günlükler yakalanır. **Program.cs** ve **Startup.cs** ile başlayan her günlüğü yakalamak Için, Applicationınsightsloggerprovider için bir izleme anahtarını açıkça etkinleştirmeniz gerekir. Ayrıca, **program.cs** veya **Startup.cs** içinden oturum açtığınızda *TelemetryConfiguration* tam olarak ayarlanamaz. Bu nedenle, bu Günlükler ınmemorychannel, örnekleme olmadan ve standart telemetri başlatıcıları ya da işlemciler kullanan minimum yapılandırmaya sahip olur.

Aşağıdaki örneklerde bu özellik **program.cs** ve **Startup.cs**ile gösterilmektedir.

#### <a name="example-programcs"></a>Örnek Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Örnek Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Eski Applicationınsightsloggerprovider 'dan geçiş

2\.7.0-Beta2 'den önceki Microsoft. ApplicationInsights. AspNet SDK sürümleri artık kullanım dışı olan bir günlük sağlayıcısı destekliyordu. Bu sağlayıcı, ıloggerfactory 'nin **Addadınsights ()** genişletme yöntemi aracılığıyla etkinleştirildi. Yeni sağlayıcıya geçiş yapmanızı öneririz, bu iki adımdan oluşur:

1. Çift günlüğü önlemek için, **Başlangıç. configure ()** yönteminden *ıloggerfactory. Addavpplicationınsights ()* çağrısını kaldırın.
2. Yeni sağlayıcı tarafından dikkate alınmadığından, koddaki filtreleme kurallarını yeniden uygulayın. *Iloggerfactory. Addadpplicationınsights ()* aşırı yüklemeleri en düşük LogLevel veya filtre işlevleri yaptı. Yeni sağlayıcı ile filtreleme, günlük çerçevesinin bir parçasıdır. Application Insights sağlayıcısı tarafından yapılmaz. Bu nedenle, *ıloggerfactory. Addadpplicationınsights ()* aşırı yüklemeleri ile sunulan tüm filtreler kaldırılmalıdır. Ve filtreleme kuralları [Denetim günlüğü düzeyi](#control-logging-level) yönergeleriyle aşağıdaki şekilde sağlanmalıdır. Günlüğe kaydetmeyi filtrelemek için *appSettings. JSON* kullanırsanız, her ikisi de aynı sağlayıcı diğer adı, *ApplicationInsights*' ı kullandığından, yeni sağlayıcıyla çalışmaya devam edecektir.

Eski sağlayıcıyı kullanmaya devam edebilirsiniz. (Yalnızca ana sürüm 3 ' e değişmeyecektir. *xx*.) ancak aşağıdaki nedenlerden dolayı yeni sağlayıcıya geçiş yapmanızı öneririz:

- Önceki sağlayıcıda [günlük kapsamları](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)desteği eksik. Yeni sağlayıcıda, kapsamdaki Özellikler otomatik olarak toplanan telemetriye özel özellikler olarak eklenir.
- Günlükler artık uygulama başlatma ardışık düzeninde daha önce yakalanamaz. **Program** ve **Başlangıç** sınıflarının günlükleri artık yakalanabilir.
- Yeni sağlayıcı ile, filtreleme, çerçeve düzeyinde yapılır. Application Insights sağlayıcıya, konsol, hata ayıklama vb. gibi yerleşik sağlayıcılar da dahil olmak üzere, diğer sağlayıcılarla aynı şekilde filtre uygulayabilirsiniz. Aynı filtreleri birden çok sağlayıcıya da uygulayabilirsiniz.
- ASP.NET Core (2,0 ve üzeri) ' de, [günlüğe kaydetme sağlayıcılarının etkinleştirilmesi](https://github.com/aspnet/Announcements/issues/255) için önerilen yol, **program.cs** içinde ILoggingBuilder üzerindeki genişletme yöntemlerini kullanmaktır.

> [!Note]
> Yeni sağlayıcı, NETSTANDARD 2.0 veya üstünü hedefleyen uygulamalar tarafından kullanılabilir. Uygulamanız .NET Core 1,1 gibi eski .NET Core sürümlerini hedefliyorsa veya .NET Framework hedefliyorsa, eski sağlayıcıyı kullanmaya devam edin.

## <a name="console-application"></a>Konsol uygulaması

> [!NOTE]
> Herhangi bir konsol uygulaması için Application Insights (ILogger ve diğer Application Insights telemetri) etkinleştirmek üzere kullanılan [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) adlı yeni bir beta Application Insights SDK 'sı vardır. Bu paketin ve ilgili yönergelerin [burada](../../azure-monitor/app/worker-service.md)kullanılması önerilir.
Bu yeni paketin kararlı sürümü yayımlandıktan sonra aşağıdaki örnek kullanım dışı olacaktır.

Aşağıdaki kod, Application Insights için ILogger izlemeleri gönderecek şekilde yapılandırılmış örnek bir konsol uygulamasını gösterir.

Yüklenen paketler:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Bu örnek `Microsoft.Extensions.Logging.ApplicationInsights`tek başına paketini kullanır. Bu yapılandırma varsayılan olarak, Application Insights veri göndermek için "en az" TelemetryConfiguration kullanır. En az, ınmemorychannel 'ın kullanılan kanal olduğu anlamına gelir. Örnekleme yok ve standart TelemetryInitializers yok. Aşağıdaki örnekte gösterildiği gibi, bu davranış bir konsol uygulaması için geçersiz kılınabilir.

Bu ek paketi yüklemelisiniz:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Aşağıdaki bölümde, hizmetler kullanılarak varsayılan TelemetryConfiguration nasıl geçersiz kılındığı gösterilmektedir **.\<TelemetryConfiguration > () metodunu yapılandırın** . Bu örnek `ServerTelemetryChannel` ve örneklemeyi ayarlar. TelemetryConfiguration 'e özel bir ılemetrybaşlatıcısı ekler.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Denetim günlüğü düzeyi

ASP.NET Core *ILogger* Infra, [günlük filtrelemeyi](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)uygulamak için yerleşik bir mekanizmaya sahiptir. Bu, Application Insights sağlayıcısı da dahil olmak üzere, kayıtlı her sağlayıcıya gönderilen günlükleri denetlemenize olanak tanır. Filtreleme, yapılandırmada (genellikle bir *appSettings. JSON* dosyası kullanılarak) veya kodda yapılabilir. Bu özellik Framework 'ün kendisi tarafından sağlanır. Application Insights sağlayıcıya özgü değildir.

Aşağıdaki örnekler Applicationınsightsloggerprovider öğesine filtre kuralları uygular.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>AppSettings. JSON ile yapılandırmada filtre kuralları oluşturma

Applicationınsightsloggerprovider için sağlayıcı diğer adı `ApplicationInsights`. *AppSettings. JSON* ' nin aşağıdaki bölümü, *Uyarı* ve yukarıdaki tüm kategoriler ve *hatalar* ve yukarıdaki `ApplicationInsightsLoggerProvider`gönderilmek üzere "Microsoft" ile başlayan kategorilerden oluşan günlükleri yapılandırır.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Kodda filtre kuralları oluşturma

Aşağıdaki kod parçacığı, *Uyarı* ve yukarıdaki kayıtları tüm kategorilerden ve *hata* için ve yukarıdaki `ApplicationInsightsLoggerProvider`gönderilmek üzere "Microsoft" ile başlayan kategorilerden bir şekilde yapılandırır. Bu yapılandırma, *appSettings. JSON*içindeki önceki bölümde bulunan ile aynıdır.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Applicationınsightsloggerprovider 'ın eski ve yeni sürümleri nelerdir?

[Microsoft. ApplicationInsights. Aspnet SDK 'sı](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) , ıloggerfactory aracılığıyla etkinleştirilen yerleşik bir Applicationınsightsloggerprovider (Microsoft. ApplicationInsights. aspnetcore. Logging. Applicationınsightsloggerprovider) içeriyorduuzantı yöntemleri. Bu sağlayıcı, 2.7.0-Beta2 sürümünden kullanılmıyor olarak işaretlendi. Bu, bir sonraki ana sürüm değişikliğine tamamen kaldırılacak. [Microsoft. ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) paketinin kendisi artık kullanılmıyor. İsteklerin, bağımlılıkların ve benzeri izlemenin etkinleştirilmesi gerekir.

Önerilen alternatif, gelişmiş bir Applicationınsightsloggerprovider içeren yeni tek başına [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)paketidir ( ' İ etkinleştirmek için ıloggerbuilder üzerinde Microsoft. Extensions. Logging. ApplicationInsights. Applicationınsightsloggerprovider) ve genişletme yöntemleri.

[Microsoft. ApplicationInsights. Aspnet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) sürümü 2.7.0-Beta3, yeni pakete bir bağımlılık alır ve otomatik olarak, ILogger yakalamaya izin vermez.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Neden bazı ILogger günlükleri Application Insights iki kez gösteriliyor?

`ILoggerFactory`üzerinde `AddApplicationInsights` çağırarak Applicationınsightsloggerprovider 'ın eski (kullanımdan kalkmış) sürümü etkinse çoğaltma gerçekleşebilir. **Yapılandırma** yönteminizin aşağıdakilere sahip olup olmadığını denetleyin ve kaldırın:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio 'da hata ayıklarken çift günlük ile karşılaşırsanız, Application Insights olanak sağlayan koddaki `EnableDebugLogger` *false* olarak ayarlayın. Bu yineleme ve düzeltme yalnızca uygulamanın hatalarını ayıklarken geçerlidir.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>[Microsoft. ApplicationInsights. Aspnet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) sürümü 2.7.0-Beta3 ve ILogger günlüklerinden Günlükler otomatik olarak yakalanır. Bu özelliği tamamen kapatmak Nasıl yaparım?.

Günlükleri genel olarak filtrelemeye bakmak için [Denetim günlüğü düzeyi](../../azure-monitor/app/ilogger.md#control-logging-level) bölümüne bakın. Applicationınsightsloggerprovider 'ı kapatmak için `LogLevel.None`kullanın:

**Kodda:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**Yapılandırmada:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Bazı ILogger günlükleri neden başkalarıyla aynı özelliklere sahip değil?

Application Insights, tüm diğer telemetri için kullanılan aynı TelemetryConfiguration kullanarak ILogger günlüklerini yakalar ve gönderir. Ancak bir özel durum var. TelemetryConfiguration, **program.cs** veya **Startup.cs**oturumunu açtığınızda varsayılan olarak tam olarak ayarlanamaz. Bu yerlerdeki Günlükler varsayılan yapılandırmaya sahip olmayacaktır, bu nedenle tüm TelemetryInitializers ve TelemetryProcessors çalıştırmazlar.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Microsoft. Extensions. Logging. ApplicationInsights tek başına paketini kullanıyorum ve bazı ek özel telemetrisi el ile günlüğe kaydetmek istiyorum. Bunu nasıl yapmalıyım?

Tek başına paketini kullandığınızda `TelemetryClient`, dı kapsayıcısına eklenmiş değildir, bu nedenle, aşağıdaki kodda gösterildiği gibi, yeni bir `TelemetryClient` örneği oluşturmanız ve günlükçü sağlayıcısı tarafından kullanılan yapılandırmayı kullanmanız gerekir. Bu, tüm özel telemetri ve ILogger telemetri açısından aynı yapılandırmanın kullanılmasını sağlar.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Application Insights etkinleştirmek için Microsoft. ApplicationInsights. AspNetCore paketini kullanırsanız, bu kodu doğrudan oluşturucuda `TelemetryClient` almak için değiştirin. Bir örnek için [Bu SSS](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)bölümüne bakın.


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger günlüklerinden hangi Application Insights telemetri türü üretildi? Ya da Application Insights içinde ıllogger günlüklerini nereden görebilirim?

Applicationınsightsloggerprovider, ILogger günlüklerini yakalar ve bunlardan Izlenetelemetriyi oluşturur. ILogger üzerinde **log ()** yöntemine bir özel durum nesnesi geçirilirse, Tracetelemetri yerine *exceptiontelemetri* oluşturulur. Bu telemetri öğeleri Portal, analiz veya Visual Studio yerel hata ayıklayıcı da dahil olmak üzere Application Insights yönelik diğer Izleme telemetrisi veya Exceptiontelemetrisi ile aynı yerlerde bulunabilir.

Her zaman Tracetelemetri gönderilmesini tercih ediyorsanız şu kod parçacığını kullanın: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK yüklü değil ve ASP.NET Core Uygulamalarım için Application Insights etkinleştirmek üzere Azure Web Apps uzantısını kullanıyorum. Yeni sağlayıcıyı kullanmak Nasıl yaparım? mı? 

Azure Web Apps Application Insights uzantısı eski sağlayıcıyı kullanır. Uygulamanızın *appSettings. JSON* dosyasındaki filtreleme kurallarını değiştirebilirsiniz. Yeni sağlayıcıdan yararlanmak için SDK 'da bir NuGet bağımlılığı alarak derleme zamanı izleme kullanın. Bu makale, uzantı yeni sağlayıcıyı kullanacak şekilde geçtiğinde güncelleştirilecektir.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Tek başına Microsoft. Extensions. Logging. ApplicationInsights paketini kullanıyorum ve oluşturucuyu çağırarak Application Insights sağlayıcısı etkinleştiriliyor **. Addadınsights ("Ikey")** . Yapılandırmadan bir izleme anahtarı almak için bir seçenek var mı?


Program.cs ve appSettings. JSON öğesini aşağıdaki şekilde değiştirin:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   `appsettings.json`ilgili bölüm:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Bu kod yalnızca bir tek başına günlük sağlayıcısı kullandığınızda gereklidir. Düzenli Application Insights izleme için, izleme anahtarı, *ApplicationInsights: ınstrumentationkey*yapılandırma yolundan otomatik olarak yüklenir. AppSettings. JSON şöyle görünmelidir:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdakiler hakkında daha fazla bilgi edinin:

* [ASP.NET Core oturum açma](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Application Insights .NET izleme günlükleri](../../azure-monitor/app/asp-net-trace-logs.md)

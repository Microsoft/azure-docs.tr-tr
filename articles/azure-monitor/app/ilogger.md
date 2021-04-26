---
title: Azure Application Insights ILogger ile .NET izleme günlüklerini keşfet
description: Azure Application Insights ILogger sağlayıcısını ASP.NET Core ve konsol uygulamalarıyla kullanma örnekleri.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a4781e3f0208d355c06df506bab3b0a3dd457078
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568599"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>Microsoft. Extension. Logging için Applicationınsightsloggerprovider

Bu makalede `ApplicationInsightsLoggerProvider` `ILogger` , konsolunda ve ASP.NET Core uygulamalarda günlükleri yakalamak için nasıl kullanılacağı gösterilmektedir.
Daha fazla bilgi için bkz. [oturum açma ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core uygulamalar

`ApplicationInsightsLoggerProvider` , ApplicationInsights [kod](./asp-net-core.md) veya [kod-daha az](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) yaklaşım kullanılarak yapılandırıldığında ASP.NET Core uygulamalar için varsayılan olarak etkinleştirilir.

Yalnızca *Uyarı* ve yukarıdaki `ILogger` Günlükler (tüm [kategoriler](/aspnet/core/fundamentals/logging/#log-category)) varsayılan olarak Application Insights gönderilir. Ancak [Bu davranışı özelleştirebilirsiniz](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection). **Program. cs** veya **Startup. cs**' den ıllogger günlüklerini yakalamak için ek adımlar gereklidir. (Bkz. [ASP.NET Core uygulamalarında Startup. cs ve program. cs öğesinden ILogger günlüklerini yakalama](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Yalnızca `ApplicationInsightsLoggerProvider` başka bir Application Insights izleme olmadan kullanmak istiyorsanız, aşağıdaki adımları kullanın.

1. NuGet paketini yükler:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. `Program.cs`Burada gösterildiği gibi değiştirin:

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
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

2. adımdaki kod yapılandırır `ApplicationInsightsLoggerProvider` . Aşağıdaki kod, günlükleri göndermek için kullanılan örnek bir denetleyici sınıfını gösterir `ILogger` . Günlükler Application Insights tarafından yakalanır.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ASP.NET Core uygulamalarda başlangıç. cs ve program. cs ' den ILogger günlüklerini yakala

> [!NOTE]
> ASP.NET Core 3,0 ve üzeri sürümlerde, artık `ILogger` Startup. cs ve program. cs içinde ekleme yapılamaz. https://github.com/aspnet/Announcements/issues/353Daha fazla ayrıntı için bkz..

`ApplicationInsightsLoggerProvider` , uygulama başlangıcında günlüklerin erken yakalanmasını sağlayabilir. Applicationınsightsloggerprovider, Application Insights (sürüm 2.7.1 ile başlayarak) otomatik olarak etkin olsa da, ardışık düzende daha sonra bir izleme anahtarı ayarlanmamış. Bu nedenle, yalnızca **Denetleyici**/diğer sınıflardan Günlükler yakalanır. **Program. cs** ve **Startup. cs** ile başlayan her günlüğü yakalamak Için, Applicationınsightsloggerprovider için bir izleme anahtarını açıkça etkinleştirmeniz gerekir. Ayrıca, **program. cs** veya **Startup. cs** ' den oturum açtığınızda *TelemetryConfiguration* tam olarak ayarlanamaz. Bu nedenle, bu Günlükler [ınmemorychannel](./telemetry-channels.md), [örnekleme](./sampling.md)olmadan ve standart [telemetri başlatıcıları ya da işlemciler](./api-filtering-sampling.md)kullanan minimum yapılandırmaya sahip olur.

Aşağıdaki örneklerde bu özellik **program. cs** ve **Startup. cs** ile gösterilmektedir.

#### <a name="example-programcs"></a>Örnek program. cs

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

#### <a name="example-startupcs"></a>Örnek başlatma. cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Eski Applicationınsightsloggerprovider 'dan geçiş

2.7.1 Before Microsoft. ApplicationInsights. AspNet SDK sürümleri, artık kullanımdan kalkmış bir günlüğe kaydetme sağlayıcısı destekliyordu. Bu sağlayıcı, ıloggerfactory 'nin **Addadınsights ()** genişletme yöntemi aracılığıyla etkinleştirildi. Yeni sağlayıcıya geçiş yapmanızı öneririz, bu iki adımdan oluşur:

1. Çift günlüğü önlemek için **Startup.Configure ()** yönteminden *ıloggerfactory. Addavpplicationınsights ()* çağrısını kaldırın.
2. Yeni sağlayıcı tarafından dikkate alınmadığından, koddaki filtreleme kurallarını yeniden uygulayın. *Iloggerfactory. Addadpplicationınsights ()* aşırı yüklemeleri en düşük LogLevel veya filtre işlevleri yaptı. Yeni sağlayıcı ile filtreleme, günlük çerçevesinin bir parçasıdır. Application Insights sağlayıcısı tarafından yapılmaz. Bu nedenle, *ıloggerfactory. Addadpplicationınsights ()* aşırı yüklemeleri ile sunulan tüm filtreler kaldırılmalıdır. Ve filtreleme kuralları [Denetim günlüğü düzeyi](#control-logging-level) yönergeleriyle aşağıdaki şekilde sağlanmalıdır. Günlüğe kaydetmeyi filtrelemek için *appsettings.js* kullanıyorsanız, her ikisi de aynı sağlayıcı diğer adı, *ApplicationInsights*'ı kullandığından, bu, yeni sağlayıcıyla çalışmaya devam edecektir.

Eski sağlayıcıyı kullanmaya devam edebilirsiniz. (Yalnızca ana sürüm 3 ' e değişmeyecektir. *xx*.) ancak aşağıdaki nedenlerden dolayı yeni sağlayıcıya geçiş yapmanızı öneririz:

- Önceki sağlayıcıda [günlük kapsamları](/aspnet/core/fundamentals/logging#log-scopes)desteği eksik. Yeni sağlayıcıda, kapsamdaki Özellikler otomatik olarak toplanan telemetriye özel özellikler olarak eklenir.
- Günlükler artık uygulama başlatma ardışık düzeninde daha önce yakalanamaz. **Program** ve **Başlangıç** sınıflarının günlükleri artık yakalanabilir.
- Yeni sağlayıcı ile, filtreleme, çerçeve düzeyinde yapılır. Application Insights sağlayıcıya, konsol, hata ayıklama vb. gibi yerleşik sağlayıcılar da dahil olmak üzere, diğer sağlayıcılarla aynı şekilde filtre uygulayabilirsiniz. Aynı filtreleri birden çok sağlayıcıya da uygulayabilirsiniz.
- ASP.NET Core (2,0 ve üzeri) ' de,  [günlük sağlayıcılarını etkinleştirmek](https://github.com/aspnet/Announcements/issues/255) için önerilen yol, **program. cs** ' deki ILoggingBuilder üzerindeki genişletme yöntemlerini kullanmaktır.

> [!Note]
> Yeni sağlayıcı, NETSTANDARD 2.0 veya üstünü hedefleyen uygulamalar tarafından kullanılabilir. [Microsoft. ApplicationInsights. Aspnet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) sürümü 2.14.0 ve sonraki sürümlerde, yeni sağlayıcı, .NET Framework NET461 veya üstünü hedefleyen uygulamalar için de kullanılabilir. Uygulamanız .NET Core 1,1 gibi eski .NET Core sürümlerini hedefliyorsa veya NET46 'den daha az .NET Framework hedefliyorsa, eski sağlayıcıyı kullanmaya devam edin.

## <a name="console-application"></a>Konsol uygulaması

> [!NOTE]
> Herhangi bir konsol uygulaması için Application Insights (ILogger ve diğer Application Insights telemetri) etkinleştirmek üzere kullanılan [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) adlı yenı bir SDK Application Insights. Bu paketin ve ilgili yönergelerin [burada](./worker-service.md)kullanılması önerilir.

Applicationınsightsloggerprovider 'ı başka bir Application Insights izleme olmadan kullanmak istiyorsanız, aşağıdaki adımları kullanın.

Yüklenen paketler:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
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

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Bu örnek tek başına paketini kullanır `Microsoft.Extensions.Logging.ApplicationInsights` . Bu yapılandırma varsayılan olarak, Application Insights veri göndermek için "en az" TelemetryConfiguration kullanır. En az, ınmemorychannel 'ın kullanılan kanal olduğu anlamına gelir. Örnekleme yok ve standart TelemetryInitializers yok. Aşağıdaki örnekte gösterildiği gibi, bu davranış bir konsol uygulaması için geçersiz kılınabilir.

Bu ek paketi yüklemelisiniz:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Aşağıdaki bölümde, **services.Configure \<TelemetryConfiguration> ()** yöntemi kullanılarak varsayılan TelemetryConfiguration nasıl geçersiz kılındığı gösterilmektedir. Bu örnek `ServerTelemetryChannel` , ve örneklemeyi belirler. TelemetryConfiguration 'e özel bir ılemetrybaşlatıcısı ekler.

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
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Denetim günlüğü düzeyi

`ILogger` , [günlük filtrelemeyi](/aspnet/core/fundamentals/logging#log-filtering)uygulamak için yerleşik bir mekanizmaya sahiptir. Bu, Application Insights sağlayıcısı da dahil olmak üzere, kayıtlı her sağlayıcıya gönderilen günlükleri denetlemenize olanak tanır. Filtreleme, yapılandırmada (genellikle bir *appsettings.js* dosya kullanılarak) veya kodda yapılabilir.

Aşağıdaki örneklerde, filtre kurallarının nasıl uygulanacağı gösterilmektedir `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Yapılandırmada appsettings.jsile filtre kuralları oluşturma

Applicationınsightsloggerprovider için sağlayıcı diğer adı `ApplicationInsights` . *appsettings.jsüzerindeki* aşağıdaki bölümü, günlüğe kaydetme sağlayıcılarının genel olarak *Uyarı* ve yukarıdaki düzeyde günlüğe kaydedilmesini sağlar. Daha sonra `ApplicationInsightsLoggerProvider` "Microsoft" ile başlayan ve yukarıdaki düzey *hata* ve yukarıdaki günlük kategorilerini geçersiz kılar.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Kodda filtre kuralları oluşturma

Aşağıdaki kod parçacığı, *Uyarı* ve yukarıdaki kayıtları tüm kategorilerden ve *hata* Için ve yukarıdaki "Microsoft" ile başlayan kategorilerden bir şekilde yapılandırır `ApplicationInsightsLoggerProvider` . Bu yapılandırma, *appsettings.jsüzerindeki* önceki bölümde bulunan ile aynıdır.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>Günlüğe kaydetme kapsamları

`ApplicationInsightsLoggingProvider`[günlük kapsamlarını](/aspnet/core/fundamentals/logging#log-scopes) destekler ve kapsamlar varsayılan olarak etkindir.

Kapsam tür ise `IReadOnlyCollection<KeyValuePair<string,object>>` , koleksiyondaki her anahtar-değer çifti özel özellikler olarak Application Insights telemetrisine eklenir. Aşağıdaki örnekte, günlükler olarak yakalanacaktır `TraceTelemetry` ve özelliklerde ("MyKey", "MyValue") olacaktır.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Kapsam olarak başka herhangi bir tür kullanılırsa, bu, Application Insights telemetri bölümünde "kapsam" özelliği altında depolanır. Aşağıdaki örnekte, `TraceTelemetry` kapsamı içeren "scope" adlı bir özellik olacaktır.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Applicationınsightsloggerprovider 'ın eski ve yeni sürümleri nelerdir?

[Microsoft. ApplicationInsights. Aspnet SDK 'sı](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) , **ıloggerfactory** uzantı yöntemleri aracılığıyla etkinleştirilen yerleşik bir applicationınsightsloggerprovider (Microsoft. ApplicationInsights. Aspnetcore. Logging. Applicationınsightsloggerprovider) içeriyordu. Bu sağlayıcı, 2.7.1 sürümünden kullanılmıyor olarak işaretlendi. Bu, bir sonraki ana sürüm değişikliğine tamamen kaldırılacak. [Microsoft. ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) paketinin kendisi artık kullanılmıyor. İsteklerin, bağımlılıkların ve benzeri izlemenin etkinleştirilmesi gerekir.

Önerilen alternatif, [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), gelişmiş bir applicationınsightsloggerprovider (Microsoft. Extensions. log. ApplicationInsights. Applicationınsightsloggerprovider) ve bunu etkinleştirmek Için ıloggerbuilder üzerinde uzantı yöntemleri içeren yeni tek başına pakettir.

[Microsoft. ApplicationInsights. Aspnet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) sürümü 2.7.1, yeni pakete bir bağımlılık alır ve ILogger yakalama 'yı otomatik olarak sunar.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Neden bazı ILogger günlükleri Application Insights iki kez gösteriliyor?

' İ çağırarak, Applicationınsightsloggerprovider 'ın eski (artık eski) sürümüne sahipseniz çoğaltma gerçekleşebilir `AddApplicationInsights` `ILoggerFactory` . **Yapılandırma** yönteminizin aşağıdakilere sahip olup olmadığını denetleyin ve kaldırın:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio 'da hata ayıklarken çift günlüğe kaydetmeye karşılaşırsanız, `EnableDebugLogger` Application Insights aşağıdaki gibi, kodda  *false* olarak ayarlayın. Bu yineleme ve düzeltme yalnızca uygulamanın hatalarını ayıklarken geçerlidir.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>[Microsoft. ApplicationInsights. Aspnet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) sürüm 2.7.1 'e güncelleştirdim ve ILogger günlüklerinden Günlükler otomatik olarak yakalanır. Bu özelliği tamamen kapatmak Nasıl yaparım?.

Günlükleri genel olarak filtrelemeye bakmak için [Denetim günlüğü düzeyi](#control-logging-level) bölümüne bakın. Applicationınsightsloggerprovider 'ı kapatmak için şunu kullanın `LogLevel.None` :

**Kod:**

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

Application Insights, tüm diğer telemetri için kullanılan aynı TelemetryConfiguration kullanarak ILogger günlüklerini yakalar ve gönderir. Ancak bir özel durum var. Varsayılan olarak, TelemetryConfiguration, **program. cs** veya **Startup. cs** oturumunu açtığınızda tam olarak ayarlanır. Bu yerlerdeki Günlükler varsayılan yapılandırmaya sahip olmayacaktır, bu nedenle tüm TelemetryInitializers ve TelemetryProcessors çalıştırmazlar.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Microsoft. Extensions. Logging. ApplicationInsights tek başına paketini kullanıyorum ve bazı ek özel telemetrisi el ile günlüğe kaydetmek istiyorum. Bunu nasıl yapmalıyım?

Tek başına paketini kullandığınızda, `TelemetryClient` dı kapsayıcısına eklenmiş değildir, bu nedenle, aşağıdaki kodda gösterildiği gibi, yeni bir örneğini oluşturmanız `TelemetryClient` ve günlükçü sağlayıcısı tarafından kullanılan yapılandırmayı kullanmanız gerekir. Bu, tüm özel telemetri ve ILogger telemetri açısından aynı yapılandırmanın kullanılmasını sağlar.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Application Insights etkinleştirmek için Microsoft. ApplicationInsights. AspNetCore paketini kullanırsanız, bu kodu doğrudan oluşturucuda almak için değiştirin `TelemetryClient` . Bir örnek için [Bu SSS](./asp-net-core.md#frequently-asked-questions)bölümüne bakın.


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger günlüklerinden hangi Application Insights telemetri türü üretildi? Ya da Application Insights içinde ıllogger günlüklerini nereden görebilirim?

Applicationınsightsloggerprovider, ILogger günlüklerini yakalar ve bunlardan Izlenetelemetriyi oluşturur. Bir özel durum nesnesi `Log` üzerinde yöntemine geçirilirse `ILogger` , tracetelemetri yerine *exceptiontelemetri* oluşturulur. Bu telemetri öğeleri Portal, analiz veya Visual Studio yerel hata ayıklayıcı da dahil olmak üzere Application Insights yönelik diğer Izleme telemetrisi veya Exceptiontelemetrisi ile aynı yerlerde bulunabilir.

Her zaman Izlenetelemetriyi göndermek isterseniz şu kod parçacığını kullanın: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK yüklü değil ve ASP.NET Core Uygulamalarım için Application Insights etkinleştirmek üzere Azure Web Apps uzantısını kullanıyorum. Yeni sağlayıcıyı kullanmak Nasıl yaparım? mı? 

Azure Web Apps Application Insights uzantısı yeni sağlayıcıyı kullanır. Uygulamanız için dosyadaki *appsettings.js* filtreleme kurallarını değiştirebilirsiniz.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Tek başına Microsoft. Extensions. Logging. ApplicationInsights paketini kullanıyorum ve oluşturucuyu çağırarak Application Insights sağlayıcısı etkinleştiriliyor **. Addadınsights ("Ikey")**. Yapılandırmadan bir izleme anahtarı almak için bir seçenek var mı?


Program. cs ' i ve appsettings.jsaşağıdaki gibi değiştirin:

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

   İlgili bölümün konusu `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Bu kod yalnızca bir tek başına günlük sağlayıcısı kullandığınızda gereklidir. Düzenli Application Insights izleme için, izleme anahtarı, *ApplicationInsights: ınstrumentationkey* yapılandırma yolundan otomatik olarak yüklenir. Appsettings.js, aşağıdaki gibi görünmelidir:

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

> [!IMPORTANT]
> Yeni Azure bölgeleri, izleme anahtarları yerine bağlantı dizelerinin kullanılmasını **gerektirir** . [Bağlantı dizesi](./sdk-connection-string.md?tabs=net) , telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. Ayrıca, kaynağınızın telemetri için hedef olarak kullanacağı uç noktaları değiştirmenize de olanak tanır. Bağlantı dizesini kopyalamanız ve uygulamanızın koduna veya bir ortam değişkenine eklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdakiler hakkında daha fazla bilgi edinin:

* [ASP.NET Core oturum açma](/aspnet/core/fundamentals/logging)
* [Application Insights .NET izleme günlükleri](./asp-net-trace-logs.md)


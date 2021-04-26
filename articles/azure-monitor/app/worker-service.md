---
title: Çalışan hizmeti uygulamaları için Application Insights (HTTP olmayan uygulamalar)
description: Azure Izleyici Application Insights .NET Core/. NET Framework HTTP olmayan uygulamaları izleme.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.openlocfilehash: c1ca594626d4384c9dfb62990ee2017d2094fca4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371864"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Çalışan hizmeti uygulamaları için Application Insights (HTTP olmayan uygulamalar)

[Çalışan hizmeti için APPLICATION INSIGHTS SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , mesajlaşma, arka plan görevleri, konsol uygulamaları vb. gibi http olmayan iş yükleri için en uygun olan yenı bir SDK 'dir. Bu tür uygulamalar, bir geleneksel ASP.NET/ASP.NET Core Web uygulaması gibi gelen HTTP isteği kavramı içermez ve bu nedenle [ASP.net](asp-net.md) veya [ASP.NET Core](asp-net-core.md) uygulamaları için Application Insights paketleri kullanılması desteklenmez.

Yeni SDK hiçbir telemetri koleksiyonunu kendisi yapmaz. Bunun yerine, [Dependencycollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [perfcountercollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [applicationınsightsloggingprovider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) vb. gibi diğer iyi bilinen Application Insights otomatik toplayıcılarda yer alır. Bu SDK `IServiceCollection` , telemetri toplamayı etkinleştirmek ve yapılandırmak için üzerinde uzantı yöntemleri sunar.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

[Çalışan hizmeti için APPLICATION INSIGHTS SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , her ne kadar veya nasıl çalıştırıldıklarından BAĞıMSıZ olarak http olmayan uygulamalar için idealdir. Uygulamanız çalışıyorsa ve Azure ile ağ bağlantısı varsa telemetri toplanabilir. Application Insights izleme .NET Core 'un desteklendiği her yerde desteklenir. Bu paket, yeni sunulan [.NET Core 3,0 Worker hizmetinde](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [ASP.NET Core 2.1/2.2 'de arka plan görevlerinde](/aspnet/core/fundamentals/host/hosted-services), konsol uygulamalarında (.NET Core/.NET Framework) vb. kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

Geçerli bir Application Insights izleme anahtarı. Bu anahtar, Application Insights telemetri göndermek için gereklidir. Bir izleme anahtarı almak için yeni bir Application Insights kaynağı oluşturmanız gerekiyorsa, bkz. [Application Insights kaynağı oluşturma](./create-new-resource.md).

## <a name="using-application-insights-sdk-for-worker-services"></a>Çalışan hizmetleri için Application Insights SDK 'sını kullanma

1. [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) paketini uygulamaya yükler.
   Aşağıdaki kod parçacığında, projenizin dosyasına eklenmesi gereken değişiklikler gösterilmektedir `.csproj` .

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. ' `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` De `IServiceCollection` , izleme anahtarını sağlayan genişletme yöntemini çağırın. Bu yöntem, uygulamanın başlangıcında çağrılmalıdır. Tam konum, uygulamanın türüne bağlıdır.

1. `ILogger` `TelemetryClient` Oluşturucu ekleme işlemini çağırarak veya kullanarak bağımlılık ekleme (dı) kapsayıcısından bir örnek veya örnek alın `serviceProvider.GetRequiredService<TelemetryClient>();` . Bu adım, `TelemetryConfiguration` ve otomatik toplama modülleri ayarlamayı tetikler.

Her uygulama türü için belirli yönergeler aşağıdaki bölümlerde açıklanmıştır.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3,0 çalışan hizmeti uygulaması

Tam örnek [burada](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) paylaşılır

1. [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) indirin ve yükleyin
2. Visual Studio yeni proje şablonu veya komut satırı kullanarak yeni bir çalışan hizmeti projesi oluşturma `dotnet new worker`
3. [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) paketini uygulamaya yükler.

4. `services.AddApplicationInsightsTelemetryWorkerService();` `CreateHostBuilder()` `Program.cs` Aşağıdaki örnekte olduğu gibi sınıfınıza yöntemine ekleyin:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. `Worker.cs`Aşağıdaki örnekteki gibi değiştirin.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. İzleme anahtarını ayarlayın.

    İzleme anahtarını için bir bağımsız değişken olarak sağlayabilseniz de `AddApplicationInsightsTelemetryWorkerService` , yapılandırmada izleme anahtarını belirtmenizi öneririz. Aşağıdaki kod örneği, içinde bir izleme anahtarının nasıl ekleneceğini gösterir `appsettings.json` . `appsettings.json`Yayımlama sırasında uygulama kök klasörüne kopyalandığından emin olun.

```json
    {
        "ApplicationInsights":
        {
            "InstrumentationKey": "putinstrumentationkeyhere"
        },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Alternatif olarak, aşağıdaki ortam değişkenlerinden birinde izleme anahtarını belirtin.
`APPINSIGHTS_INSTRUMENTATIONKEY` veya `ApplicationInsights:InstrumentationKey`

Örnek: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
VEYA `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Genellikle, `APPINSIGHTS_INSTRUMENTATIONKEY` Web işleri olarak Web Apps dağıtılan uygulamalar için izleme anahtarını belirler.

> [!NOTE]
> Diğer seçenekler üzerinde WINS olan ortam değişkeni üzerinden WINS kodunda belirtilen bir izleme anahtarı `APPINSIGHTS_INSTRUMENTATIONKEY` .

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>Barındırılan hizmetlerle arka plan görevlerini ASP.NET Core

[Bu](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio) belgede ASP.NET Core 2.1/2.2 uygulamasında arka plan görevlerinin nasıl oluşturulacağı açıklanmaktadır.

Tam örnek [burada](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/BackgroundTasksWithHostedService) paylaşılır

1. [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) paketini uygulamaya yükler.
2. `services.AddApplicationInsightsTelemetryWorkerService();` `ConfigureServices()` Aşağıdaki örnekteki gibi yöntemine ekleyin:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

`TimedHostedService`Arka plan görevi mantığının bulunduğu kod aşağıda verilmiştir.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. İzleme anahtarını ayarlayın.
   `appsettings.json`Yukarıdaki .NET Core 3,0 çalışan Hizmeti örneğinde aynısını kullanın.

## <a name="net-corenet-framework-console-application"></a>.NET Core/. NET Framework konsol uygulaması

Bu makalenin başlangıcında bahsedildiği gibi, yeni paket, düzenli bir konsol uygulamasından bile Application Insights Telemetri sağlamak için kullanılabilir. Bu paket [`NetStandard2.0`](/dotnet/standard/net-standard) , .NET Core 2,0 veya üzeri konsol uygulamaları için ve .NET Framework 4.7.2 veya üzeri için kullanılabilir.

Tam örnek [burada](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) paylaşılır

1. [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) paketini uygulamaya yükler.

2. Program. cs ' i aşağıda gösterildiği gibi değiştirin.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Bu konsol uygulaması aynı varsayılanı kullanır `TelemetryConfiguration` ve önceki bölümde örneklerle aynı şekilde özelleştirilebilir.

## <a name="run-your-application"></a>Uygulamanızı çalıştırma

Uygulamanızı çalıştırın. Yukarıdaki tüm Yukarıdaki örnek çalışanlar, bing.com ' ye her saniye bir http çağrısı yapar ve kullanarak birkaç günlük de yayar `ILogger` . Bu satırlar `StartOperation` `TelemetryClient` , bir işlem oluşturmak için kullanılan (Bu örnekte `RequestTelemetry` "işlem" adlı) çağrısı içinde sarmalanır. Application Insights, bu ILogger günlüklerini (varsayılan olarak uyarı veya üstü) ve bağımlılıkları toplayacak ve `RequestTelemetry` üst-alt ilişkisiyle bağıntılı olacak. Bağıntı Ayrıca çapraz işlem/ağ sınırını da işler. Örneğin, çağrı başka bir izlenen bileşene yapılmışsa, bu üst öğeyle da bağıntılı olur.

Bu özel işlemi, `RequestTelemetry` tipik bir Web uygulamasındaki gelen bir Web isteğinin eşdeğeri olarak düşünülebilir. Bir Işlemi kullanmak gerekli olmasa da, [](./correlation.md) `RequestTelemetry` üst işlem olarak davrandığı ve çalışan yinelemede oluşturulan her telemetriyi aynı işleme göre mantıksal olarak kabul edilen Application Insights bağıntı veri modeliyle en iyi şekilde uyum sağlar. Bu yaklaşım ayrıca oluşturulan tüm Telemetriyi (otomatik ve el ile) aynı olacak şekilde sağlar `operation_id` . Örnekleme temel alınarak `operation_id` örnekleme algoritması, tek bir yinelemeden Telemetriyi korur ya da bırakır.

Aşağıda, Application Insights tarafından otomatik olarak toplanan tüm telemetri listelenmektedir.

### <a name="live-metrics"></a>Canlı Ölçümler

[Canlı ölçümler](./live-stream.md) , Application Insights izlemenin doğru yapılandırılıp yapılandırılmadığını hızlı bir şekilde doğrulamak için kullanılabilir. Telemetri portalda ve analizler üzerinde görünmesinin birkaç dakika sürebilirken, canlı ölçümler çalışan işlemin neredeyse gerçek zamanlı olarak CPU kullanımını gösterir. Ayrıca Istekler, bağımlılıklar, Izlemeler vb. gibi diğer telemetrileri de gösterebilir.

### <a name="ilogger-logs"></a>ILogger günlükleri

`ILogger`Önem derecesi `Warning` veya daha büyük aracılığıyla yayılan Günlükler otomatik olarak yakalanır. Application Insights tarafından yakalanan günlük düzeylerini özelleştirmek için [ILogger belgelerini](ilogger.md#control-logging-level) izleyin.

### <a name="dependencies"></a>Bağımlılıklar

Bağımlılık koleksiyonu varsayılan olarak etkindir. [Bu](asp-net-dependencies.md#automatically-tracked-dependencies) makalede otomatik olarak toplanan bağımlılıklar açıklanmakta ve ayrıca el ile izleme yapmak için adımlar yer verilmektedir.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` Varsayılan olarak etkindir ve .NET Core 3,0 uygulamalarından varsayılan sayaç kümesini toplar. [EventCounter](eventcounters.md) öğreticisi, toplanan varsayılan sayaç kümesini listeler. Ayrıca, listeyi özelleştirmeye yönelik yönergeler de vardır.

### <a name="manually-tracking-additional-telemetry"></a>Ek Telemetriyi el ile izleme

SDK, yukarıda açıklandığı gibi telemetri otomatik olarak toplarken, çoğu durumda kullanıcının Application Insights hizmetine ek telemetri gönderebilmesi gerekir. Ek Telemetriyi izlemek için önerilen yol, `TelemetryClient` bağımlılık ekleme ve sonra desteklenen `TrackXXX()` [API](api-custom-events-metrics.md) yöntemlerinden birini çağırma. Başka bir tipik kullanım örneği, [işlemlerin özel izlemedir](custom-operations-tracking.md). Bu yaklaşım yukarıdaki çalışan örneklerde gösterilmiştir.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK 'sını yapılandırma

`TelemetryConfiguration`Çalışan hizmeti SDK 'sı tarafından kullanılan varsayılan yapılandırma, ASP.NET veya ASP.NET Core uygulamasında kullanılan otomatik yapılandırmaya benzerdir ve bu da Telemetriyi zenginleştirmek için kullanılan TelemetryInitializers `HttpContext` .

Çalışan hizmeti için Application Insights SDK 'sını, varsayılan yapılandırmayı değiştirecek şekilde özelleştirebilirsiniz. Application Insights ASP.NET Core SDK kullanıcıları ASP.NET Core yerleşik [bağımlılık ekleme](/aspnet/core/fundamentals/dependency-injection)kullanılarak yapılandırma değişikliği hakkında bilgi sahibi olabilir. WorkerService SDK 'Sı benzer ilkeleri de temel alır. `ConfigureServices()`Aşağıda açıklandığı gibi, üzerinde uygun yöntemleri çağırarak, bölümünde neredeyse tüm yapılandırma değişikliklerini yapın `IServiceCollection` .

> [!NOTE]
> Bu SDK kullanılırken, değiştirilerek yapılandırmayı değiştirme `TelemetryConfiguration.Active` desteklenmiyor ve değişiklikler yansıtılmayacak.

### <a name="using-applicationinsightsserviceoptions"></a>Applicationınsightsserviceoptions kullanma

`ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService` Aşağıdaki örnekte olduğu gibi, ' a geçirerek birkaç ortak ayarı değiştirebilirsiniz:

```csharp
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    var aiOptions = new ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
}
```

`ApplicationInsightsServiceOptions`Bu SDK 'daki ASP.NET Core SDK 'nın aksine ad alanında olduğunu unutmayın `Microsoft.ApplicationInsights.WorkerService` `Microsoft.ApplicationInsights.AspNetCore.Extensions` .

İçinde yaygın olarak kullanılan ayarlar `ApplicationInsightsServiceOptions`

|Ayar | Açıklama | Varsayılan
|---------------|-------|-------
|EnableQuickPulseMetricStream | Liveölçümlerini etkinleştir/devre dışı bırak özelliği | true
|Enableadaptiveörnekleme | Uyarlamalı örneklemeyi etkinleştir/devre dışı bırak | true
|Enablesinyal | Düzenli aralıklarla (15 dakikalık varsayılan), .NET sürümü, Azure ortam bilgileri, varsa vb. gibi çalışma zamanı hakkında bilgi içeren "HeartBeatState" adlı özel bir ölçüm göndererek, sinyalleri etkinleştir/devre dışı bırak özelliği. | true
|Addadutocollectedmetricextractor | Örnekleme gerçekleşmeden önce Istekler/bağımlılıklar hakkında önceden toplanmış ölçümler gönderen bir TelemetryProcessor olan otomatik Collectedölçümler ayıklayıcısı 'nı etkinleştirin/devre dışı bırakın. | true
|EnableDiagnosticsTelemetryModule | Etkinleştir/devre dışı bırak `DiagnosticsTelemetryModule` . Bunu devre dışı bırakmak, aşağıdaki ayarların yoksayılmasına neden olur; `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

En güncel listesi için [içindeki `ApplicationInsightsServiceOptions` yapılandırılabilir ayarlar '](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) a bakın.

### <a name="sampling"></a>Örnekleme

Çalışan hizmeti için Application Insights SDK hem sabit hızlı hem de Uyarlamalı örneklemeyi destekler. Uyarlamalı örnekleme varsayılan olarak etkindir. `EnableAdaptiveSampling` [Applicationınsightsserviceoptions](#using-applicationinsightsserviceoptions) seçeneği kullanılarak örnekleme devre dışı bırakılabilir

Ek örnekleme ayarlarını yapılandırmak için aşağıdaki örnek kullanılabilir.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new ApplicationInsightsServiceOptions();
    
    // Disable adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);

    // Add Adaptive Sampling with custom settings.
    // the following adds adaptive sampling with 15 items per sec.
    services.Configure<TelemetryConfiguration>((telemetryConfig) =>
        {
            var builder = telemetryConfig.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond: 15);
            builder.Build();
        });
    //...
}
```

[Örnekleme](#sampling) belgesinde daha fazla bilgi bulunabilir.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers ekleme

Tüm telemetri ile gönderilen özellikleri tanımlamak istediğinizde [telemetri başlatıcıları](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) 'nı kullanın.

Kapsayıcıya yeni bir Ekle `TelemetryInitializer` `DependencyInjection` ve SDK 'yi otomatik olarak öğesine ekleyin `TelemetryConfiguration` .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers kaldırılıyor

Telemetri başlatıcıları varsayılan olarak mevcuttur. Tüm veya belirli telemetri başlatıcıları 'nı kaldırmak için, çağrısından *sonra* aşağıdaki örnek kodu kullanın `AddApplicationInsightsTelemetryWorkerService()` .

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Telemetri işlemcisi ekleme

`TelemetryConfiguration`Üzerinde genişletme yöntemi kullanarak ' ye özel telemetri işlemcileri ekleyebilirsiniz `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` . Application Insights hizmetine göndereceğiniz telemetride nelerin dahil edildiğini veya dışlandığından daha doğrudan denetim sağlamak için, [Gelişmiş filtreleme senaryolarında](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer) telemetri işlemcileri kullanırsınız. Aşağıdaki örneği kullanın.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Varsayılan TelemetryModules yapılandırma veya kaldırma

Application Insights, el ile izlemeye gerek duymadan, belirli iş yükleri hakkında telemetri otomatik olarak toplamak için telemetri modüllerini kullanır.

Aşağıdaki otomatik koleksiyon modülleri varsayılan olarak etkinleştirilmiştir. Bu modüller, otomatik olarak telemetri toplamanın sorumluluğundadır. Varsayılan davranışlarını değiştirecek şekilde devre dışı bırakabilir veya yapılandırabilirsiniz.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule` -(Şu anda bu telemetri modülüyle ilgili bir sorun var. Geçici bir çözüm için bkz. [GitHub sorunu 1689](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
).)
* `AzureInstanceMetadataTelemetryModule`

Herhangi bir Varsayılanı yapılandırmak için, `TelemetryModule` `ConfigureTelemetryModule<T>` `IServiceCollection` Aşağıdaki örnekte gösterildiği gibi genişletme yöntemini üzerinde kullanın.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Telemetri kanalını yapılandırma

Varsayılan kanal `ServerTelemetryChannel` . Aşağıdaki örnekte gösterildiği gibi, geçersiz kılabilirsiniz.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Telemetrisi dinamik olarak devre dışı bırak

Telemetriyi koşullu ve dinamik olarak devre dışı bırakmak istiyorsanız, `TelemetryConfiguration` kodunuzu kodunuzda herhangi bir yere ASP.NET Core bağımlılık ekleme kapsayıcısı ile çözümleyebilir ve `DisableTelemetry` üzerinde bayrak ayarlayabilirsiniz.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Otomatik olarak toplanmayan telemetrileri nasıl izleyebilirim?

`TelemetryClient`Oluşturucu Ekleme kullanarak bir örneğini alın ve `TrackXXX()` üzerinde gerekli yöntemi çağırın. Yeni örnek oluşturmanız önerilmez `TelemetryClient` . Tek bir örneği `TelemetryClient` kapsayıcıda zaten kayıtlı olduğundan `DependencyInjection` `TelemetryConfiguration` telemetri geri kalanıyla birlikte paylaşır. Yeni bir `TelemetryClient` örnek oluşturmak yalnızca telemetri geri kalanından ayrı bir yapılandırmaya ihtiyaç duyduğunda önerilir.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Bir çalışan hizmeti projesine Application Insights eklemek için Visual Studio IDE kullanabilir miyim?

Visual Studio IDE ekleme Şu anda yalnızca ASP.NET/ASP.NET Core uygulamaları için desteklenmektedir. Bu belge, Visual Studio çalışan hizmeti uygulamalarını ekleme desteği geldiğinde güncelleştirilecektir.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Durum İzleyicisi gibi araçları kullanarak Application Insights izlemeyi etkinleştirebilir miyim?

Hayır. [Durum İzleyicisi](./monitor-performance-live-website-now.md) ve [durum İzleyicisi v2](./status-monitor-v2-overview.md) Şu anda yalnızca ASP.NET 4. x desteğine sahiptir.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Uygulamamı Linux 'ta çalıştırdım, tüm özellikler destekleniyor mu?

Evet. Bu SDK için özellik desteği, aşağıdaki özel durumlarla birlikte tüm platformlarda aynıdır:

* Performans sayaçları yalnızca Windows 'da, canlı ölçümlerde gösterilen Işlem CPU 'SU/belleği dışında desteklenir.
* `ServerTelemetryChannel`Varsayılan olarak etkin olsa da, uygulama Linux veya MacOS 'ta çalışıyorsa, ağ sorunları varsa Telemetriyi geçici tutmak için kanal otomatik olarak yerel bir depolama klasörü oluşturmaz. Bu sınırlama nedeniyle, geçici ağ veya sunucu sorunları olduğunda telemetri kaybedilir. Bu sorunu geçici olarak çözmek için, kanal için yerel bir klasör yapılandırın:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Örnek uygulamalar

[.NET Core konsol uygulaması](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) .NET Core (2,0 veya üzeri) veya .NET Framework (4.7.2 ya da üzeri) olarak yazılmış bir konsol uygulaması kullanıyorsanız bu örneği kullanın

[HostedServices Ile ASP .NET Core arka plan görevleri](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/BackgroundTasksWithHostedService) Bu örneği Asp.Net Core 2.1/2.2 ' de kullanıyorsanız ve [burada](/aspnet/core/fundamentals/host/hosted-services) resmi olmayan kılavuza göre arka plan görevleri oluşturuyorsanız kullanın

[.NET Core 3,0 çalışan hizmeti](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) [Burada](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio#worker-service-template) resmi kılavuza göre .net Core 3,0 çalışan hizmet uygulamanız varsa bu örneği kullanın

## <a name="open-source-sdk"></a>Açık kaynaklı SDK

* [Kodu okuyun ve koda katkıda bulunun](https://github.com/microsoft/ApplicationInsights-dotnet).

En son güncelleştirmeler ve hata düzeltmeleri için [sürüm notlarına bakın](./release-notes.md).

## <a name="next-steps"></a>Sonraki adımlar

* Uygulamanızın performans ve kullanımının ayrıntılı bir görünümü için kendi olay ve ölçümlerinizi göndermek üzere [API 'Yi kullanın](./api-custom-events-metrics.md) .
* [Otomatik olarak izlenmeyen ek bağımlılıkları izleyin](./auto-collect-dependencies.md).
* [Otomatik olarak toplanan Telemetriyi zenginleştirin veya filtreleyin](./api-filtering-sampling.md).
* [ASP.NET Core bağımlılık ekleme](/aspnet/core/fundamentals/dependency-injection).

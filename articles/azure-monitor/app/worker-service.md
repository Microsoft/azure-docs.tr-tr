---
title: Çalışan hizmeti uygulamaları için Application Insights (HTTP olmayan uygulamalar) | Microsoft Docs
description: Application Insights ile .NET Core/. NET Framework olmayan HTTP uygulamalarını izleme.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 09/15/2019
ms.openlocfilehash: 5f812d5fe1b25358a0bf09ebf879569ae29b33f3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131892"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Çalışan hizmeti uygulamaları için Application Insights (HTTP olmayan uygulamalar)

Application Insights, mesajlaşma, arka plan görevleri, konsol uygulamaları vb. gibi HTTP olmayan iş yükleri için en uygun olan `Microsoft.ApplicationInsights.WorkerService`adlı yeni bir SDK 'Yı serbest bırakmaktadır. Bu tür uygulamalar, bir geleneksel ASP.NET/ASP.NET Core Web uygulaması gibi gelen HTTP isteği kavramı içermez ve bu nedenle [ASP.net](asp-net.md) veya [ASP.NET Core](asp-net-core.md) uygulamaları için Application Insights paketleri kullanılması desteklenmez.

Yeni SDK hiçbir telemetri koleksiyonunu kendisi yapmaz. Bunun yerine, [Dependencycollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [perfcountercollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [applicationınsightsloggingprovider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) vb. gibi diğer iyi bilinen Application Insights otomatik toplayıcılarda yer alır. Bu SDK, telemetri toplamayı etkinleştirmek ve yapılandırmak için `IServiceCollection` uzantı yöntemleri sunar.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

[Çalışan hizmeti için APPLICATION INSIGHTS SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , her ne kadar veya nasıl çalıştırıldıklarından BAĞıMSıZ olarak http olmayan uygulamalar için idealdir. Uygulamanız çalışıyorsa ve Azure ile ağ bağlantısı varsa telemetri toplanabilir. Application Insights izleme .NET Core 'un desteklendiği her yerde desteklenir. Bu paket, yeni sunulan [.NET Core 3,0 Worker hizmetinde](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [ASP.NET Core 2.1/2.2 'de arka plan görevlerinde](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), konsol uygulamalarında (.NET Core/.NET Framework) vb. kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

Geçerli bir Application Insights izleme anahtarı. Bu anahtar, Application Insights telemetri göndermek için gereklidir. Bir izleme anahtarı almak için yeni bir Application Insights kaynağı oluşturmanız gerekiyorsa, bkz. [Application Insights kaynağı oluşturma](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Çalışan hizmetleri için Application Insights SDK 'sını kullanma

1. [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) paketini uygulamaya yükler.
   Aşağıdaki kod parçacığında, projenizin `.csproj` dosyasına eklenmesi gereken değişiklikler gösterilmektedir.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.8.0" />
    </ItemGroup>
```

1. İzleme anahtarını sağlayarak `IServiceCollection``AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` genişletme yöntemini çağırın. Bu yöntem, uygulamanın başlangıcında çağrılmalıdır. Tam konum, uygulamanın türüne bağlıdır.

1. `serviceProvider.GetRequiredService<TelemetryClient>();` çağırarak veya Oluşturucu Ekleme kullanarak bağımlılık ekleme (DI) kapsayıcısından bir `ILogger` örneği veya `TelemetryClient` örneği alın. Bu adım, `TelemetryConfiguration` ve otomatik toplama modüllerinin kurulumunu tetikler.

Her uygulama türü için belirli yönergeler aşağıdaki bölümlerde açıklanmıştır.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3,0 çalışan hizmeti uygulaması

Tam örnek [burada](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) paylaşılır

1. [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) indirin ve yükleyin
2. Visual Studio yeni proje şablonu veya komut satırı kullanarak yeni bir çalışan hizmeti projesi oluşturun `dotnet new worker`
3. [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) paketini uygulamaya yükler.

4. Bu örnekte olduğu gibi `Program.cs` sınıfınıza `CreateHostBuilder()` yöntemine `services.AddApplicationInsightsTelemetryWorkerService();` ekleyin:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. `Worker.cs` aşağıdaki örneğe göre değiştirin.

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

    İzleme anahtarını `AddApplicationInsightsTelemetryWorkerService`bir bağımsız değişken olarak sağlayabilseniz de, yapılandırmada izleme anahtarını belirtmenizi öneririz. Aşağıdaki kod örneği, `appsettings.json`' de bir izleme anahtarının nasıl ekleneceğini gösterir. Yayımlama sırasında `appsettings.json` uygulama kök klasörüne kopyalandığından emin olun.

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

Örneğin, `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
VEYA `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Genellikle `APPINSIGHTS_INSTRUMENTATIONKEY`, Web Işleri olarak Web Apps dağıtılan uygulamalar için izleme anahtarını belirtir.

> [!NOTE]
> Diğer seçenekler üzerinden WINS `APPINSIGHTS_INSTRUMENTATIONKEY`ortam değişkeni üzerinden WINS kodunda belirtilen bir izleme anahtarı.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>Barındırılan hizmetlerle arka plan görevlerini ASP.NET Core
[Bu](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) belgede ASP.NET Core 2.1/2.2 uygulamasında arka plan görevlerinin nasıl oluşturulacağı açıklanmaktadır.

Tam örnek [burada](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) paylaşılır

1. Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) paketini uygulamaya yükler.
2. Bu örnekte olduğu gibi `ConfigureServices()` yöntemine `services.AddApplicationInsightsTelemetryWorkerService();` ekleyin:

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

Arka plan görevi mantığının bulunduğu `TimedHostedService` için kod aşağıda verilmiştir.

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
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. İzleme anahtarını ayarlayın.
   Yukarıdaki .NET Core 3,0 çalışan Hizmeti örneğinde aynı `appsettings.json` kullanın.

## <a name="net-corenet-framework-console-application"></a>.NET Core/. NET Framework konsol uygulaması

Bu makalenin başlangıcında bahsedildiği gibi, yeni paket, düzenli bir konsol uygulamasından bile Application Insights Telemetri sağlamak için kullanılabilir. Bu paket [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)hedefler ve bu nedenle, .net Core 2,0 veya üzeri sürümlerde konsol uygulamaları için ve .NET Framework 4.7.2 veya üzeri bir sürümü kullanılabilir.

Tam örnek [burada](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) paylaşılır

1. Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) paketini uygulamaya yükler.

2. Aşağıdaki örnekte Program.cs değiştirin.

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
                // Hence instrumentation key must be specified here.
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

Bu konsol uygulaması ayrıca aynı varsayılan `TelemetryConfiguration`kullanır ve önceki bölümde örneklerle aynı şekilde özelleştirilebilir.

## <a name="run-your-application"></a>Uygulamanızı çalıştırma

Uygulamanızı çalıştırın. Yukarıdaki yukarıdaki tüm Yukarıdaki örnek çalışanlar, bing.com ' ye her saniye bir http çağrısı yapar ve ayrıca ILogger kullanarak birkaç günlük yayar. Bu satırlar, bir işlem oluşturmak için kullanılan `TelemetryClient``StartOperation` çağrısı içinde (Bu örnekte "işlem" `RequestTelemetry`) sarmalanır. Application Insights, bu ILogger günlüklerini (varsayılan olarak uyarı veya üstü) ve bağımlılıkları toplayacak ve üst-alt ilişkisiyle `RequestTelemetry` bağıntılı olacaktır. Bağıntı Ayrıca çapraz işlem/ağ sınırını da işler. Örneğin, çağrı başka bir izlenen bileşene yapılmışsa, bu üst öğeyle da bağıntılı olur.

Bu `RequestTelemetry` özel işlemi, tipik bir Web uygulamasındaki gelen bir Web isteğinin eşdeğeri olarak düşünülebilir. Bir Işlemi kullanmak gerekli olmasa da, üst işlem olarak davranan `RequestTelemetry` ve çalışan yinelemede oluşturulan her telemetriyi aynı işleme göre mantıksal olarak kabul edilen [Application Insights bağıntı veri modeliyle](https://docs.microsoft.com/azure/azure-monitor/app/correlation) en iyi şekilde uyum sağlar. Bu yaklaşım ayrıca oluşturulan tüm telemetrinin (otomatik ve el ile) aynı `operation_id`sahip olacağını da sağlar. Örnekleme `operation_id`temel aldığı için, örnekleme algoritması tek bir yinelemeden Telemetriyi korur ya da bırakır.

Aşağıda, Application Insights tarafından otomatik olarak toplanan tüm telemetri listelenmektedir.

### <a name="live-metrics"></a>Canlı ölçümler

[Canlı ölçümler](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) , Application Insights izlemenin doğru yapılandırılıp yapılandırılmadığını hızlı bir şekilde doğrulamak için kullanılabilir. Telemetri portalda ve analizler üzerinde görünmesinin birkaç dakika sürebilirken, canlı ölçümler çalışan işlemin neredeyse gerçek zamanlı olarak CPU kullanımını gösterir. Ayrıca Istekler, bağımlılıklar, Izlemeler vb. gibi diğer telemetrileri de gösterebilir.

### <a name="ilogger-logs"></a>ILogger günlükleri

Önem derecesi `Warning` veya daha büyük `ILogger` ile oluşturulan Günlükler otomatik olarak yakalanır. Application Insights tarafından yakalanan günlük düzeylerini özelleştirmek için [ILogger belgelerini](ilogger.md#control-logging-level) izleyin.

### <a name="dependencies"></a>Bağımlılıkları

Bağımlılık koleksiyonu varsayılan olarak etkindir. [Bu](asp-net-dependencies.md#automatically-tracked-dependencies) makalede otomatik olarak toplanan bağımlılıklar açıklanmakta ve ayrıca el ile izleme yapmak için adımlar yer verilmektedir.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`, varsayılan olarak etkindir ve .NET Core 3,0 uygulamalarından varsayılan sayaç kümesini toplar. [EventCounter](eventcounters.md) öğreticisi, toplanan varsayılan sayaç kümesini listeler. Ayrıca, listeyi özelleştirmeye yönelik yönergeler de vardır.

### <a name="manually-tracking-additional-telemetry"></a>Ek Telemetriyi el ile izleme

SDK, yukarıda açıklandığı gibi telemetri otomatik olarak toplarken, çoğu durumda kullanıcının Application Insights hizmetine ek telemetri gönderebilmesi gerekir. Ek Telemetriyi izlemek için önerilen yol, bağımlılık ekleme işleminden `TelemetryClient` bir örnek edinerek ve üzerinde desteklenen `TrackXXX()` [API](api-custom-events-metrics.md) yöntemlerinden birini çağırmaya göre yapılır. Başka bir tipik kullanım örneği, [işlemlerin özel izlemedir](custom-operations-tracking.md). Bu yaklaşım yukarıdaki çalışan örneklerde gösterilmiştir.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK 'sını yapılandırma

Çalışan hizmeti SDK 'Sı tarafından kullanılan varsayılan `TelemetryConfiguration`, bir ASP.NET veya ASP.NET Core uygulamasında kullanılan otomatik yapılandırmaya benzerdir, bu da `HttpContext`telemetri zenginleştirmek için kullanılan TelemetryInitializers.

Çalışan hizmeti için Application Insights SDK 'sını, varsayılan yapılandırmayı değiştirecek şekilde özelleştirebilirsiniz. Application Insights ASP.NET Core SDK kullanıcıları ASP.NET Core yerleşik [bağımlılık ekleme](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)kullanılarak yapılandırma değişikliği hakkında bilgi sahibi olabilir. WorkerService SDK 'Sı benzer ilkeleri de temel alır. Aşağıda açıklandığı gibi uygun yöntemleri `IServiceCollection`çağırarak `ConfigureServices()` bölümünde neredeyse tüm yapılandırma değişikliklerini yapın.

> [!NOTE]
> Bu SDK kullanılırken, `TelemetryConfiguration.Active` değiştirilerek yapılandırmayı değiştirmek desteklenmez ve değişiklikler yansıtılmayacaktır.

### <a name="using-applicationinsightsserviceoptions"></a>Applicationınsightsserviceoptions kullanma

Aşağıdaki örnekte olduğu gibi `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService`geçirerek birkaç ortak ayarı değiştirebilirsiniz:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Bu SDK 'daki `ApplicationInsightsServiceOptions`, ASP.NET Core SDK 'daki `Microsoft.ApplicationInsights.AspNetCore.Extensions` aksine `Microsoft.ApplicationInsights.WorkerService` ad alanında olduğunu unutmayın.

`ApplicationInsightsServiceOptions` içinde yaygın olarak kullanılan ayarlar

|Ayar | Açıklama | Varsayılan
|---------------|-------|-------
|EnableQuickPulseMetricStream | Liveölçümlerini etkinleştir/devre dışı bırak özelliği | true
|Enableadaptiveörnekleme | Uyarlamalı örneklemeyi etkinleştir/devre dışı bırak | true
|Enablesinyal | Düzenli aralıklarla (15 dakikalık varsayılan), .NET sürümü, Azure ortam bilgileri, varsa vb. gibi çalışma zamanı hakkında bilgi içeren "HeartBeatState" adlı özel bir ölçüm göndererek, sinyalleri etkinleştir/devre dışı bırak özelliği. | true
|Addadutocollectedmetricextractor | Örnekleme gerçekleşmeden önce Istekler/bağımlılıklar hakkında önceden toplanmış ölçümler gönderen bir TelemetryProcessor olan otomatik Collectedölçümler ayıklayıcısı 'nı etkinleştirin/devre dışı bırakın. | true

En güncel liste için [`ApplicationInsightsServiceOptions`yapılandırılabilir ayarları](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) inceleyin.

### <a name="sampling"></a>Örnekleme

Çalışan hizmeti için Application Insights SDK hem sabit hızlı hem de Uyarlamalı örneklemeyi destekler. Uyarlamalı örnekleme varsayılan olarak etkindir. Çalışan hizmeti için örnekleme yapılandırması, [ASP.NET Core uygulamalarla](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)aynı şekilde yapılır.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers ekleme

Tüm telemetri ile gönderilen özellikleri tanımlamak istediğinizde [telemetri başlatıcıları](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) 'nı kullanın.

`DependencyInjection` kapsayıcısına yeni `TelemetryInitializer` ekleyin ve SDK `TelemetryConfiguration`otomatik olarak ekler.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers kaldırılıyor

Telemetri başlatıcıları varsayılan olarak mevcuttur. Tüm veya belirli telemetri başlatıcıları 'nı kaldırmak için, `AddApplicationInsightsTelemetryWorkerService()`*çağırdıktan sonra* aşağıdaki örnek kodu kullanın.

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

`IServiceCollection``AddApplicationInsightsTelemetryProcessor` uzantı yöntemini kullanarak `TelemetryConfiguration` için özel telemetri işlemcileri ekleyebilirsiniz. Application Insights hizmetine göndereceğiniz telemetride nelerin dahil edildiğini veya dışlandığından daha doğrudan denetim sağlamak için, [Gelişmiş filtreleme senaryolarında](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) telemetri işlemcileri kullanırsınız. Aşağıdaki örneği kullanın.

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
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Varsayılan `TelemetryModule`yapılandırmak için, aşağıdaki örnekte gösterildiği gibi, `ConfigureTelemetryModule<T>` uzantı yöntemini `IServiceCollection`kullanın.

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

Varsayılan kanal `ServerTelemetryChannel`. Aşağıdaki örnekte gösterildiği gibi, geçersiz kılabilirsiniz.

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

Telemetriyi koşullu ve dinamik olarak devre dışı bırakmak istiyorsanız, kodunuzun herhangi bir yerinden ASP.NET Core bağımlılık ekleme kapsayıcısı ile `TelemetryConfiguration` örneğini çözümleyebilir ve üzerinde `DisableTelemetry` bayrağını ayarlayabiliriz.

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

Oluşturucu Ekleme kullanarak `TelemetryClient` örneğini alın ve üzerinde gerekli `TrackXXX()` yöntemini çağırın. Yeni `TelemetryClient` örnekleri oluşturmanızı önermiyoruz. `TelemetryClient` tekil bir örneği zaten telemetri geri kalanı ile `TelemetryConfiguration` paylaşan `DependencyInjection` kapsayıcıda kayıtlı. Yeni bir `TelemetryClient` örneği oluşturmak yalnızca telemetriden ayrı bir yapılandırmaya ihtiyaç duyduğunda önerilir.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Bir çalışan hizmeti projesine Application Insights eklemek için Visual Studio IDE kullanabilir miyim?

Visual Studio IDE ekleme Şu anda yalnızca ASP.NET/ASP.NET Core uygulamaları için desteklenmektedir. Bu belge, Visual Studio çalışan hizmeti uygulamalarını ekleme desteği geldiğinde güncelleştirilecektir.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Durum İzleyicisi gibi araçları kullanarak Application Insights izlemeyi etkinleştirebilir miyim?

Hayır. [Durum İzleyicisi](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) ve [durum İzleyicisi v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) Şu anda yalnızca ASP.NET 4. x desteğine sahiptir.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Uygulamamı Linux 'ta çalıştırdım, tüm özellikler destekleniyor mu?

Evet. Bu SDK için özellik desteği, aşağıdaki özel durumlarla birlikte tüm platformlarda aynıdır:

* Performans sayaçları yalnızca Windows 'da, canlı ölçümlerde gösterilen Işlem CPU 'SU/belleği dışında desteklenir.
* `ServerTelemetryChannel`, varsayılan olarak etkinleştirilmiş olsa da, uygulama Linux veya MacOS 'ta çalışıyorsa, ağ sorunları varsa Telemetriyi geçici tutmak için kanal otomatik olarak yerel bir depolama klasörü oluşturmaz. Bu sınırlama nedeniyle, geçici ağ veya sunucu sorunları olduğunda telemetri kaybedilir. Bu sorunu geçici olarak çözmek için, kanal için yerel bir klasör yapılandırın:

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

[.NET Core konsol uygulaması](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) .NET Core (2,0 veya üzeri) veya .NET Framework (4.7.2 ya da üzeri) olarak yazılmış bir konsol uygulaması kullanıyorsanız bu örneği kullanın

[HostedServices Ile ASP .NET Core arka plan görevleri](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Bu örneği Asp.Net Core 2.1/2.2 ' de kullanıyorsanız ve [burada](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2) resmi olmayan kılavuza göre arka plan görevleri oluşturuyorsanız kullanın

[.NET Core 3,0 çalışan hizmeti](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) [Burada](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template) resmi kılavuza göre .net Core 3,0 çalışan hizmet uygulamanız varsa bu örneği kullanın

## <a name="open-source-sdk"></a>Açık kaynaklı SDK

[Kodu okuyun ve koda katkıda bulunun](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Sonraki adımlar

* Uygulamanızın performans ve kullanımının ayrıntılı bir görünümü için kendi olay ve ölçümlerinizi göndermek üzere [API 'Yi kullanın](../../azure-monitor/app/api-custom-events-metrics.md) .
* [Otomatik olarak izlenmeyen ek bağımlılıkları izleyin](../../azure-monitor/app/auto-collect-dependencies.md).
* [Otomatik olarak toplanan Telemetriyi zenginleştirin veya filtreleyin](../../azure-monitor/app/api-filtering-sampling.md).
* [ASP.NET Core bağımlılık ekleme](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

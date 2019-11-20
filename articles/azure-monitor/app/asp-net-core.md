---
title: ASP.NET Core uygulamalar için Azure Application Insights | Microsoft Docs
description: Kullanılabilirlik, performans ve kullanım için ASP.NET Core Web uygulamalarını izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/22/2019
ms.openlocfilehash: 8f39556fcffea5f254e5362dbb1b55762f60c9b3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131947"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core uygulamalar için Application Insights

Bu makalede, [ASP.NET Core](https://docs.microsoft.com/aspnet/core) bir uygulama için Application Insights nasıl etkinleştirileceği açıklanır. Bu makaledeki yönergeleri tamamladığınızda Application Insights, ASP.NET Core uygulamanızdan istekleri, bağımlılıkları, özel durumları, performans sayaçlarını, sinyalleri ve günlükleri toplar.

Burada kullanacağınız örnek, `netcoreapp2.2`hedefleyen bir [MVC uygulamasıdır](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) . Bu yönergeleri tüm ASP.NET Core uygulamalarına uygulayabilirsiniz.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

[ASP.NET Core için APPLICATION INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) , uygulamalarınızı nerede veya nasıl çalıştırdıklarından bağımsız olarak izleyebilir. Uygulamanız çalışıyorsa ve Azure ile ağ bağlantısı varsa telemetri toplanabilir. Application Insights izleme .NET Core 'un desteklendiği her yerde desteklenir. Destek şunları içerir:
* **İşletim sistemi**: Windows, Linux veya Mac.
* **Barındırma yöntemi**: işlemde veya işlem dışı.
* **Dağıtım yöntemi**: çerçeveye bağımlı veya kendine dahil.
* **Web sunucusu**: IIS (Internet Information Server) veya Kestrel.
* **Barındırma platformu**: Azure App Service, Azure VM, Docker, Azure Kubernetes hizmeti (aks) ve benzeri Web Apps özelliği.
* **.NET Core çalışma zamanı sürümü**: 1. xx, 2. xx veya 3. xx
* **IDE**: Visual Studio, vs Code veya komut satırı.

> [!NOTE]
> Application Insights ile birlikte ASP.NET Core 3,0 kullanıyorsanız, lütfen [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) sürümünü veya üstünü kullanın. Bu, ASP.NET Core 3,0 ' i destekleyen tek sürümdür.

## <a name="prerequisites"></a>Önkoşullar

- Çalışan bir ASP.NET Core uygulaması. Bir ASP.NET Core uygulaması oluşturmanız gerekiyorsa, bu [ASP.NET Core öğreticisini](https://docs.microsoft.com/aspnet/core/getting-started/)izleyin.
- Geçerli bir Application Insights izleme anahtarı. Bu anahtar, Application Insights telemetri göndermek için gereklidir. Bir izleme anahtarı almak için yeni bir Application Insights kaynağı oluşturmanız gerekiyorsa, bkz. [Application Insights kaynağı oluşturma](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Sunucu tarafı telemetrisini Application Insights etkinleştirme (Visual Studio)

1. Projenizi Visual Studio 'da açın.

    > [!TIP]
    > İsterseniz, Application Insights yapacağı tüm değişiklikleri izleyebilmek için projeniz için kaynak denetimi ayarlayabilirsiniz. Kaynak denetimini etkinleştirmek için **dosya** > **kaynak denetimine Ekle**' yi seçin.

2.  > **Application Insights telemetri Ekle** **Proje** ' yi seçin.

3. **Kullanmaya**başlayın ' ı seçin. Bu seçimin metni, Visual Studio sürümünüze bağlı olarak farklılık gösterebilir. Bazı önceki sürümler bunun yerine **ücretsiz Başlat** düğmesini kullanır.

4. Aboneliğinizi seçin. Sonra **kaynak** > **Kaydet**' i seçin.

5. Projenize Application Insights ekledikten sonra, SDK 'nın en son kararlı sürümünü kullandığınızı onaylamak için denetleyin. **Microsoft. ApplicationInsights. AspNetCore** > , **Proje** > **NuGet Paketlerini Yönet** ' e gidin. Gerekirse **Güncelleştir**' i seçin.

     ![Güncelleştirme için Application Insights paketinin nerede seçileceğini gösteren ekran görüntüsü](./media/asp-net-core/update-nuget-package.png)

6. İsteğe bağlı ipucunu izlediyseniz ve projenizi kaynak denetimine eklediyseniz **, >  > ** **Takım Gezgini** **görüntüle** ' ye gidin. Sonra Application Insights telemetri tarafından yapılan değişikliklerin fark görünümünü görmek için her bir dosyayı seçin.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Sunucu tarafı telemetrisini Application Insights etkinleştirme (Visual Studio yok)

1. [ASP.NET Core için APPLICATION INSIGHTS SDK NuGet paketini](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)yükler. Her zaman en son kararlı sürümü kullanmanızı öneririz. [Açık kaynaklı GitHub](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)deposunda SDK için tam sürüm notları bulun.

    Aşağıdaki kod örneğinde, projenizin `.csproj` dosyasına eklenecek değişiklikler gösterilmektedir.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.0" />
        </ItemGroup>
    ```

2. Bu örnekte olduğu gibi `Startup` sınıfınıza `ConfigureServices()` yöntemine `services.AddApplicationInsightsTelemetry();` ekleyin:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. İzleme anahtarını ayarlayın.

    İzleme anahtarını `AddApplicationInsightsTelemetry`bir bağımsız değişken olarak sağlayabilseniz de, yapılandırmada izleme anahtarını belirtmenizi öneririz. Aşağıdaki kod örneği, `appsettings.json`' de bir izleme anahtarının nasıl ekleneceğini gösterir. Yayımlama sırasında `appsettings.json` uygulama kök klasörüne kopyalandığından emin olun.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Alternatif olarak, aşağıdaki ortam değişkenlerinden birinde izleme anahtarını belirtin:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Örneğin:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Genellikle `APPINSIGHTS_INSTRUMENTATIONKEY` Azure Web Apps dağıtılan uygulamalar için izleme anahtarını belirtir.

    > [!NOTE]
    > Diğer seçenekler üzerinden WINS `APPINSIGHTS_INSTRUMENTATIONKEY`ortam değişkeni üzerinden WINS kodunda belirtilen bir izleme anahtarı.

## <a name="run-your-application"></a>Uygulamanızı çalıştırma

Uygulamanızı çalıştırın ve ona istek yapın. Telemetri artık Application Insights akışı sağlamalıdır. Application Insights SDK, hem gelen Web isteklerini hem de aşağıdaki telemetri ile birlikte uygulamanıza otomatik olarak toplar.

### <a name="live-metrics"></a>Canlı ölçümler

[Canlı ölçümler](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) , Application Insights izlemenin doğru yapılandırılıp yapılandırılmadığını hızlı bir şekilde doğrulamak için kullanılabilir. Telemetri portalda ve analizler üzerinde görünmesinin birkaç dakika sürebilirken, canlı ölçümler çalışan işlemin neredeyse gerçek zamanlı olarak CPU kullanımını gösterir. Ayrıca Istekler, bağımlılıklar, Izlemeler vb. gibi diğer telemetri de gösterilebilir.

### <a name="ilogger-logs"></a>ILogger günlükleri

Önem derecesi `Warning` veya daha büyük `ILogger` ile oluşturulan Günlükler otomatik olarak yakalanır. Application Insights tarafından yakalanan günlük düzeylerini özelleştirmek için [ILogger belgelerini](ilogger.md#control-logging-level) izleyin.

### <a name="dependencies"></a>Bağımlılıkları

Bağımlılık koleksiyonu varsayılan olarak etkindir. [Bu](asp-net-dependencies.md#automatically-tracked-dependencies) makalede otomatik olarak toplanan bağımlılıklar açıklanmakta ve ayrıca el ile izleme yapmak için adımlar yer verilmektedir.

### <a name="performance-counters"></a>Performans sayaçları

ASP.NET Core [performans sayaçları](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) için destek sınırlıdır:

* SDK sürümleri 2.4.1 ve üzeri, uygulama Azure Web Apps (Windows) üzerinde çalışıyorsa performans sayaçlarını toplar.
* SDK sürümleri 2.7.1 ve üzeri, uygulama Windows 'da çalışıyorsa ve `NETSTANDARD2.0` veya daha sonra hedeflerse performans sayaçlarını toplar.
* .NET Framework hedefleyen uygulamalar için SDK 'nın tüm sürümleri performans sayaçlarını destekler.
* SDK sürümleri 2.8.0 ve üzeri, Linux 'ta CPU/bellek sayacını destekler. Linux 'ta başka bir sayaç desteklenmez. Linux 'ta (ve diğer Windows dışı ortamlarda) sistem sayaçlarını almanın önerilen yolu [Eventcounters](#eventcounter) kullanmaktır

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`, varsayılan olarak etkindir ve .NET Core 3,0 uygulamalarından varsayılan sayaç kümesini toplar. [EventCounter](eventcounters.md) öğreticisi, toplanan varsayılan sayaç kümesini listeler. Ayrıca, listeyi özelleştirmeye yönelik yönergeler de vardır.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Web uygulamaları için istemci tarafı telemetrisini etkinleştir

Yukarıdaki adımlar, sunucu tarafı Telemetriyi toplamaya başlamanıza yardımcı olacak kadar yeterlidir. Uygulamanızda istemci tarafı bileşenleri varsa, [kullanım telemetrisini](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)toplamaya başlamak için sonraki adımları izleyin.

1. `_ViewImports.cshtml`ekleme:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. `_Layout.cshtml`, `<head>` bölümünün sonuna, diğer herhangi bir betikten önce `HtmlHelper` ekleyin. Sayfadan herhangi bir özel JavaScript telemetrisini raporlamak istiyorsanız, bu kod parçacığına sonra ekleme:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Daha önce başvurulan `.cshtml` dosya adları varsayılan bir MVC uygulama şablonundan alınır. Sonuç olarak, uygulamanız için istemci tarafı izlemeyi doğru şekilde etkinleştirmek istiyorsanız, uygulamanızın izlemek istediğiniz her sayfanın `<head>` bölümünde JavaScript kod parçacığı görünmelidir. `_Layout.cshtml`için JavaScript kod parçacığını ekleyerek bu uygulama şablonu için bu hedefi gerçekleştirebilirsiniz. 

Projeniz `_Layout.cshtml`içermiyorsa, [istemci tarafı izleme](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)eklemeye devam edebilirsiniz. Bu, JavaScript kod parçacığını uygulamanızdaki tüm sayfaların `<head>` denetleyen eşdeğer bir dosyaya ekleyerek yapabilirsiniz. Ya da kod parçacığını birden çok sayfaya ekleyebilirsiniz, ancak bu çözümün korunması zordur ve genellikle bunu önermiyoruz.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK 'sını yapılandırma

Varsayılan yapılandırmayı değiştirmek için ASP.NET Core Application Insights SDK 'sını özelleştirebilirsiniz. Application Insights ASP.NET SDK kullanıcıları, `ApplicationInsights.config` kullanılarak veya `TelemetryConfiguration.Active`değiştirilerek yapılandırmayı değiştirme konusunda bilgi sahibi olabilir. Yapılandırmayı ASP.NET Core için farklı şekilde değiştirirsiniz. ASP.NET Core SDK 'sını uygulamaya ekleyin ve ASP.NET Core yerleşik [bağımlılık ekleme](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)eklentisini kullanarak yapılandırın. Başka türlü yönlendirilmediğiniz takdirde, `Startup.cs` sınıfınızın `ConfigureServices()` yönteminde neredeyse tüm yapılandırma değişikliklerini yapın. Aşağıdaki bölümler daha fazla bilgi sunar.

> [!NOTE]
> ASP.NET Core uygulamalarda, `TelemetryConfiguration.Active` değiştirilerek yapılandırmayı değiştirmek desteklenmez.

### <a name="using-applicationinsightsserviceoptions"></a>Applicationınsightsserviceoptions kullanma

Aşağıdaki örnekte olduğu gibi `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`geçirerek birkaç ortak ayarı değiştirebilirsiniz:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

`ApplicationInsightsServiceOptions` ayarların tam listesi

|Ayar | Açıklama | Varsayılan
|---------------|-------|-------
|EnableQuickPulseMetricStream | Liveölçümlerini etkinleştir/devre dışı bırak özelliği | true
|Enableadaptiveörnekleme | Uyarlamalı örneklemeyi etkinleştir/devre dışı bırak | true
|Enablesinyal | Düzenli aralıklarla (15 dakikalık varsayılan), .NET sürümü, Azure ortam bilgileri, varsa vb. gibi çalışma zamanı hakkında bilgi içeren "HeartBeatState" adlı özel bir ölçüm göndererek, sinyalleri etkinleştir/devre dışı bırak özelliği. | true
|Addadutocollectedmetricextractor | Örnekleme gerçekleşmeden önce Istekler/bağımlılıklar hakkında önceden toplanmış ölçümler gönderen bir TelemetryProcessor olan otomatik Collectedölçümler ayıklayıcısı 'nı etkinleştirin/devre dışı bırakın. | true
|RequestCollectionOptions. TrackExceptions | Istek koleksiyonu modülü tarafından işlenmeyen özel durum izlemenin raporlamasını etkinleştirin/devre dışı bırakın. | NETSTANDARD 2.0'da false (özel durumlar Applicationınsightsloggerprovider ile izleniyor), aksi takdirde true.

En güncel liste için [`ApplicationInsightsServiceOptions`yapılandırılabilir ayarları](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) inceleyin.

### <a name="sampling"></a>Örnekleme

ASP.NET Core için Application Insights SDK hem sabit hızlı hem de Uyarlamalı örneklemeyi destekler. Uyarlamalı örnekleme varsayılan olarak etkindir. 

Daha fazla bilgi için bkz. [ASP.NET Core uygulamalar için uyarlamalı örnekleme yapılandırma](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers ekleme

Tüm telemetriyle gönderilen genel özellikleri tanımlamak istediğinizde [telemetri başlatıcıları](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) 'nı kullanın.

Aşağıdaki kodda gösterildiği gibi, `DependencyInjection` kapsayıcısına yeni `TelemetryInitializer` ekleyin. SDK, `DependencyInjection` kapsayıcısına eklenen `TelemetryInitializer` otomatik olarak seçer.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers kaldırılıyor

Telemetri başlatıcıları varsayılan olarak mevcuttur. Tüm veya belirli telemetri başlatıcıları 'nı kaldırmak için, `AddApplicationInsightsTelemetry()`*çağırdıktan sonra* aşağıdaki örnek kodu kullanın.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

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

`IServiceCollection``AddApplicationInsightsTelemetryProcessor` uzantı yöntemini kullanarak `TelemetryConfiguration` için özel telemetri işlemcileri ekleyebilirsiniz. [Gelişmiş filtreleme senaryolarında](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)telemetri işlemcileri kullanıyorsunuz. Aşağıdaki örneği kullanın.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Varsayılan TelemetryModules yapılandırma veya kaldırma

Application Insights, Kullanıcı tarafından el ile izlemeye gerek duymadan belirli iş yükleri hakkında faydalı telemetri otomatik olarak toplamak için telemetri modüllerini kullanır.

Aşağıdaki otomatik koleksiyon modülleri varsayılan olarak etkinleştirilmiştir. Bu modüller, otomatik olarak telemetri toplamanın sorumluluğundadır. Varsayılan davranışlarını değiştirecek şekilde devre dışı bırakabilir veya yapılandırabilirsiniz.

* `RequestTrackingTelemetryModule`-gelen web isteklerinden Requesttelemetri toplar.
* `DependencyTrackingTelemetryModule`-giden http çağrılarından ve SQL çağrılarından Dependencytelemetri toplar.
* `PerformanceCollectorModule`-Windows PerformanceCounters ' i toplar.
* `QuickPulseTelemetryModule`-canlı ölçüm portalında göstermek için telemetri toplar.
* `AppServicesHeartbeatTelemetryModule`-uygulamanın barındırıldığı Azure App Service ortam hakkında kalp sinyal (özel ölçümler olarak gönderilir) toplar.
* `AzureInstanceMetadataTelemetryModule`-uygulamanın barındırıldığı Azure VM ortamı hakkında (özel ölçümler olarak gönderilen) kalp sinyal toplar.
* `EventCounterCollectionModule`- [Eventcounters toplar.](eventcounters.md) Bu modül yeni bir özelliktir ve SDK sürümü 2.8.0 ve üzeri sürümlerde kullanılabilir.

Varsayılan `TelemetryModule`yapılandırmak için, aşağıdaki örnekte gösterildiği gibi, `ConfigureTelemetryModule<T>` uzantı yöntemini `IServiceCollection`kullanın.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

### <a name="configuring-a-telemetry-channel"></a>Telemetri kanalını yapılandırma

Varsayılan kanal `ServerTelemetryChannel`. Aşağıdaki örnekte gösterildiği gibi, geçersiz kılabilirsiniz.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Telemetrisi dinamik olarak devre dışı bırak

Telemetriyi koşullu ve dinamik olarak devre dışı bırakmak istiyorsanız, kodunuzun herhangi bir yerinden ASP.NET Core bağımlılık ekleme kapsayıcısı ile `TelemetryConfiguration` örneğini çözümleyebilir ve üzerinde `DisableTelemetry` bayrağını ayarlayabiliriz.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Yukarıdaki bir otomatik koleksiyon modüllerinin telemetri toplamasını engellemez. Yalnızca Application Insights telemetri gönderilmesi yukarıdaki yaklaşımla devre dışı bırakılır. Belirli bir otomatik toplama modülü istenmiyorsa, [telemetri modülünü kaldırmak](#configuring-or-removing-default-telemetrymodules) en iyisidir

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="does-application-insights-support-aspnet-core-30"></a>Application Insights ASP.NET Core 3,0 destekliyor mu?

Evet. ASP.NET Core sürüm 2.8.0 veya üzeri [IÇIN SDK Application Insights](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) güncelleştirme. SDK 'nın daha eski sürümleri ASP.NET Core 3,0 ' i desteklemez.

Ayrıca, [burada](#enable-application-insights-server-side-telemetry-visual-studio)Visual Studio tabanlı yönergeler kullanıyorsanız, eklemek Için visual Studio 2019 (16.3.0) uygulamasının en son sürümüne güncelleştirin. Visual Studio 'nun önceki sürümleri ASP.NET Core 3,0 uygulamaları için otomatik ekleme 'yi desteklemez.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Otomatik olarak toplanmayan telemetrileri nasıl izleyebilirim?

Oluşturucu Ekleme kullanarak `TelemetryClient` örneğini alın ve üzerinde gerekli `TrackXXX()` yöntemini çağırın. Bir ASP.NET Core uygulamasında yeni `TelemetryClient` örnekleri oluşturmanız önerilmez. `TelemetryClient` tekil bir örneği zaten telemetri geri kalanı ile `TelemetryConfiguration` paylaşan `DependencyInjection` kapsayıcıda kayıtlı. Yeni bir `TelemetryClient` örneği oluşturmak yalnızca telemetriden ayrı bir yapılandırmaya ihtiyaç duyduğunda önerilir.

Aşağıdaki örnek, bir denetleyiciden ek Telemetriyi nasıl izleyeceğinizi gösterir.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Application Insights özel veri raporlama hakkında daha fazla bilgi için bkz. [Application Insights özel ölçümler API başvurusu](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Bazı Visual Studio şablonları, Application Insights etkinleştirmek için ıwebhostbuilder üzerinde Useapplicationınsights () genişletme yöntemini kullandı. Bu kullanım hala geçerli mi?

Uzantı metodu `UseApplicationInsights()` hala desteklenirken, Application Insights SDK sürümü 2.8.0 ve sonraki sürümlerinde artık kullanılmıyor olarak işaretlenir. SDK 'nın bir sonraki ana sürümünde kaldırılacaktır. Application Insights telemetrinin etkinleştirilmesi için önerilen yol, bazı yapılandırmayı denetlemek için aşırı yüklemeler sağladığından `AddApplicationInsightsTelemetry()` kullanmaktır. Ayrıca, ASP.NET Core 3,0 uygulamalarda, Application Insights 'ı etkinleştirmenin tek yolu `services.AddApplicationInsightsTelemetry()`.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>ASP.NET Core uygulamamı Web Apps 'ye dağıttım. Web Apps Application Insights uzantısını yine de etkinleştirmem gerekir mi?

SDK, bu makalede gösterildiği gibi derleme zamanında yüklendiyse, App Service portalından [Application Insights uzantısını](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) etkinleştirmeniz gerekmez. Uzantı yüklü olsa bile, SDK 'nın uygulamaya zaten eklendiğini algıladığında, bu geri açılır. Uzantıyı Application Insights etkinleştirirseniz SDK 'yı yükleyip güncelleştirmeniz gerekmez. Ancak bu makaledeki yönergeleri izleyerek Application Insights etkinleştirirseniz şu nedenle daha fazla esneklik elde edersiniz:

   * Application Insights telemetri şu şekilde çalışmaya devam edecektir:
       * Windows, Linux ve Mac gibi tüm işletim sistemleri.
       * Kendi içinde veya çerçevesine bağımlı olan tüm yayımlama modları.
       * Tam .NET Framework dahil olmak üzere tüm hedef çerçeveler.
       * Web Apps, VM 'Ler, Linux, kapsayıcılar, Azure Kubernetes hizmeti ve Azure olmayan barındırma dahil olmak üzere tüm barındırma seçenekleri.
       * Önizleme sürümleri dahil olmak üzere tüm .NET Core sürümleri.
   * Visual Studio 'da hata ayıklarken telemetri ' i yerel olarak görebilirsiniz.
   * `TrackXXX()` API 'sini kullanarak ek özel telemetri izleyebilirsiniz.
   * Yapılandırma üzerinde tam denetim sahibi olursunuz.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Durum İzleyicisi gibi araçları kullanarak Application Insights izlemeyi etkinleştirebilir miyim?

Hayır. [Durum İzleyicisi](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) ve [durum İzleyicisi v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) Şu anda yalnızca ASP.NET 4. x desteğine sahiptir.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights ASP.NET Core 2,0 uygulaması için otomatik olarak etkinleştirildi mi?

`Microsoft.AspNetCore.All` 2,0 metapackage, Application Insights SDK 'sını (sürüm 2.1.0) içerir. Uygulamayı Visual Studio hata ayıklayıcısı altında çalıştırırsanız, Visual Studio Application Insights etkinleştirilir ve doğrudan IDE 'de telemetri gösterir. Bir izleme anahtarı belirtilmediği takdirde telemetri Application Insights hizmetine gönderilmedi. 2,0 uygulamaları için bile Application Insights etkinleştirmek üzere bu makaledeki yönergeleri takip etmenizi öneririz.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Uygulamamı Linux 'ta çalıştırdım, tüm özellikler destekleniyor mu?

Evet. SDK için özellik desteği, aşağıdaki özel durumlarla birlikte tüm platformlarda aynıdır:

* Performans sayaçları yalnızca Windows 'ta desteklenir.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-30-worker-service-template-applications"></a>Bu SDK, yeni .NET Core 3,0 çalışan hizmeti şablonu uygulamaları için destekleniyor mu?

Bu SDK `HttpContext`gerektirir ve bu nedenle, .NET Core 3,0 çalışan hizmeti uygulamaları dahil olmak üzere HTTP olmayan uygulamalarda çalışmaz. Yeni yayınlanan Microsoft. ApplicationInsights. WorkerService SDK 'sını kullanarak bu uygulamalarda uygulama öngörülerini etkinleştirmek için [Bu](worker-service.md) belgeye başvurun.

## <a name="open-source-sdk"></a>Açık kaynaklı SDK

[Kodu okuyun ve koda katkıda bulunun](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- [.NET Core ve Visual Studio 'yu sıfırdan Application Insights yapılandırmak](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) için bu dış adım adım videoya göz atın.
- [.NET Core ve sıfırdan Visual Studio Code Application Insights yapılandırmak](https://youtu.be/ygGt84GDync) için bu dış adım adım videoya göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* Kullanıcıların uygulamanızda nasıl gezintiğini anlamak için [Kullanıcı akışlarını araştırma](../../azure-monitor/app/usage-flows.md) .
* Bir özel durum oluştuğunda kaynak kodu ve değişkenlerin durumunu görmek için [bir anlık görüntü koleksiyonu yapılandırın](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) .
* Uygulamanızın performans ve kullanımının ayrıntılı bir görünümü için kendi olay ve ölçümlerinizi göndermek üzere [API 'Yi kullanın](../../azure-monitor/app/api-custom-events-metrics.md) .
* Uygulamanızı dünyanın her yerindeki sürekli denetlemek için [kullanılabilirlik testlerini](../../azure-monitor/app/monitor-web-app-availability.md) kullanın.
* [ASP.NET Core bağımlılık ekleme](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)

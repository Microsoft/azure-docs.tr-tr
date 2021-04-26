---
title: ASP.NET Core uygulamalar için Azure Application Insights | Microsoft Docs
description: Kullanılabilirlik, performans ve kullanım için ASP.NET Core Web uygulamalarını izleyin.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020
ms.openlocfilehash: be4cbe5f95fa6901ae4299662750c232d4700a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711510"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core uygulamalar için Application Insights

Bu makalede, [ASP.NET Core](/aspnet/core) bir uygulama için Application Insights nasıl etkinleştirileceği açıklanır. Bu makaledeki yönergeleri tamamladığınızda Application Insights, ASP.NET Core uygulamanızdan istekleri, bağımlılıkları, özel durumları, performans sayaçlarını, sinyalleri ve günlükleri toplar.

Burada kullanacağınız örnek, ' i hedefleyen bir [MVC uygulamasıdır](/aspnet/core/tutorials/first-mvc-app) `netcoreapp3.0` . Bu yönergeleri tüm ASP.NET Core uygulamalarına uygulayabilirsiniz. [Çalışan hizmetini](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)kullanıyorsanız [buradaki](./worker-service.md)yönergeleri kullanın.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

[ASP.NET Core için APPLICATION INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) , uygulamalarınızı nerede veya nasıl çalıştırdıklarından bağımsız olarak izleyebilir. Uygulamanız çalışıyorsa ve Azure ile ağ bağlantısı varsa telemetri toplanabilir. Application Insights izleme .NET Core 'un desteklendiği her yerde desteklenir. Destek şunları içerir:
* **İşletim sistemi**: Windows, Linux veya Mac.
* **Barındırma yöntemi**: işlemde veya işlem dışı.
* **Dağıtım yöntemi**: çerçeveye bağımlı veya kendine dahil.
* **Web sunucusu**: IIS (Internet Information Server) veya Kestrel.
* **Barındırma platformu**: Azure App Service, Azure VM, Docker, Azure Kubernetes hizmeti (aks) ve benzeri Web Apps özelliği.
* **.NET Core sürümü**: tüm resmi olarak [desteklenen](https://dotnet.microsoft.com/download/dotnet-core) .NET Core sürümleri.
* **IDE**: Visual Studio, vs Code veya komut satırı.

> [!NOTE]
> ASP.NET Core 3,1 [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) veya üstünü gerektirir.

## <a name="prerequisites"></a>Önkoşullar

- Çalışan bir ASP.NET Core uygulaması. Bir ASP.NET Core uygulaması oluşturmanız gerekiyorsa, bu [ASP.NET Core öğreticisini](/aspnet/core/getting-started/)izleyin.
- Geçerli bir Application Insights izleme anahtarı. Bu anahtar, Application Insights telemetri göndermek için gereklidir. Bir izleme anahtarı almak için yeni bir Application Insights kaynağı oluşturmanız gerekiyorsa, bkz. [Application Insights kaynağı oluşturma](./create-new-resource.md).

> [!IMPORTANT]
> Yeni Azure bölgeleri, izleme anahtarları yerine bağlantı dizelerinin kullanılmasını **gerektirir** . [Bağlantı dizesi](./sdk-connection-string.md?tabs=net) , telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. Ayrıca, kaynağınızın telemetri için hedef olarak kullanacağı uç noktaları değiştirmenize de olanak tanır. Bağlantı dizesini kopyalamanız ve uygulamanızın koduna veya bir ortam değişkenine eklemeniz gerekir.


## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Sunucu tarafı telemetrisini Application Insights etkinleştirme (Visual Studio)

Mac için Visual Studio için [el ile Kılavuzu](#enable-application-insights-server-side-telemetry-no-visual-studio)kullanın. Bu yordamı yalnızca Visual Studio 'nun Windows sürümü destekler.

1. Projenizi Visual Studio’da açın.

    > [!TIP]
    > İsterseniz, Application Insights yapacağı tüm değişiklikleri izleyebilmek için projeniz için kaynak denetimi ayarlayabilirsiniz. Kaynak denetimini etkinleştirmek için **Dosya**  >  **kaynak denetimine Ekle**' yi seçin.

2. **Proje**  >  **Ekle Application Insights telemetri** seçin.

3. **Kullanmaya** başlayın ' ı seçin. Bu seçimin metni, Visual Studio sürümünüze bağlı olarak farklılık gösterebilir. Bazı önceki sürümler bunun yerine **ücretsiz Başlat** düğmesini kullanır.

4. Aboneliğinizi seçin. Sonra **kaynak**  >  **kaydı**' nı seçin.

5. Projenize Application Insights ekledikten sonra, SDK 'nın en son kararlı sürümünü kullandığınızı onaylamak için denetleyin. **Project**'e git  >  Microsoft. ApplicationInsights. aspnetcore **NuGet Paketlerini Yönet**  >  . Gerekirse **Güncelleştir**' i seçin.

     ![Güncelleştirme için Application Insights paketinin nerede seçileceğini gösteren ekran görüntüsü](./media/asp-net-core/update-nuget-package.png)

6. İsteğe bağlı ipucunu izlediyseniz ve projenizi kaynak denetimine eklediyseniz,   >  **Takım Gezgini**  >  **değişiklikleri** görüntüle ' ye gidin. Sonra Application Insights telemetri tarafından yapılan değişikliklerin fark görünümünü görmek için her bir dosyayı seçin.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Sunucu tarafı telemetrisini Application Insights etkinleştirme (Visual Studio yok)

1. [ASP.NET Core için APPLICATION INSIGHTS SDK NuGet paketini](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)yükler. Her zaman en son kararlı sürümü kullanmanızı öneririz. [Açık kaynaklı GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/releases)deposunda SDK için tam sürüm notları bulun.

    Aşağıdaki kod örneğinde, projenizin dosyasına eklenecek değişiklikler gösterilmektedir `.csproj` .

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.16.0" />
        </ItemGroup>
    ```

2. `services.AddApplicationInsightsTelemetry();` `ConfigureServices()` `Startup` Aşağıdaki örnekte olduğu gibi sınıfınıza yöntemine ekleyin:

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

    İzleme anahtarını için bir bağımsız değişken olarak sağlayabilseniz de `AddApplicationInsightsTelemetry` , yapılandırmada izleme anahtarını belirtmenizi öneririz. Aşağıdaki kod örneği, içinde bir izleme anahtarının nasıl ekleneceğini gösterir `appsettings.json` . `appsettings.json`Yayımlama sırasında uygulama kök klasörüne kopyalandığından emin olun.

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

    Örnek:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` genellikle [Azure Web Apps](./azure-web-apps.md?tabs=net)' de kullanılır, ancak bu SDK 'nın desteklendiği her yerde de kullanılabilir. (Kodsuz kullanacaksınız Web uygulaması izleme yapıyorsanız, bağlantı dizelerini kullanmıyorsanız bu biçim gereklidir.)

    İzleme anahtarlarını ayarlama yerine artık [bağlantı dizelerini](./sdk-connection-string.md?tabs=net)de kullanabilirsiniz.

    > [!NOTE]
    > Diğer seçenekler üzerinde WINS olan ortam değişkeni üzerinden WINS kodunda belirtilen bir izleme anahtarı `APPINSIGHTS_INSTRUMENTATIONKEY` .

### <a name="user-secrets-and-other-configuration-providers"></a>Kullanıcı gizli dizileri ve diğer yapılandırma sağlayıcıları

İzleme anahtarını ASP.NET Core Kullanıcı gizlilikleri içinde depolamak veya başka bir yapılandırma sağlayıcısından almak istiyorsanız, aşırı yüklemeyi bir `Microsoft.Extensions.Configuration.IConfiguration` parametresiyle kullanabilirsiniz. Örneğin, `services.AddApplicationInsightsTelemetry(Configuration);`.
Microsoft. ApplicationInsights. AspNetCore sürüm [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)'dan başlayarak, çağırma `services.AddApplicationInsightsTelemetry()` işlemi otomatik olarak uygulamanın içindeki izleme anahtarını okur `Microsoft.Extensions.Configuration.IConfiguration` . Açıkça sağlanması gerekmez `IConfiguration` .

## <a name="run-your-application"></a>Uygulamanızı çalıştırma

Uygulamanızı çalıştırın ve ona istek yapın. Telemetri artık Application Insights akışı sağlamalıdır. Application Insights SDK, hem gelen Web isteklerini hem de aşağıdaki telemetri ile birlikte uygulamanıza otomatik olarak toplar.

### <a name="live-metrics"></a>Canlı Ölçümler

[Canlı ölçümler](./live-stream.md) , Application Insights izlemenin doğru yapılandırılıp yapılandırılmadığını hızlı bir şekilde doğrulamak için kullanılabilir. Telemetri portalda ve analizler üzerinde görünmesinin birkaç dakika sürebilirken, canlı ölçümler çalışan işlemin neredeyse gerçek zamanlı olarak CPU kullanımını gösterir. Ayrıca Istekler, bağımlılıklar, Izlemeler vb. gibi diğer telemetri de gösterilebilir.

### <a name="ilogger-logs"></a>ILogger günlükleri

Varsayılan yapılandırma, `ILogger` önem derecesi `Warning` ve üzeri günlükleri toplar. Bu yapılandırma [özelleştirilebilir](#how-do-i-customize-ilogger-logs-collection).

### <a name="dependencies"></a>Bağımlılıklar

Bağımlılık koleksiyonu varsayılan olarak etkindir. [Bu](asp-net-dependencies.md#automatically-tracked-dependencies) makalede otomatik olarak toplanan bağımlılıklar açıklanmakta ve ayrıca el ile izleme yapmak için adımlar yer verilmektedir.

### <a name="performance-counters"></a>Performans sayaçları

ASP.NET Core [performans sayaçları](./performance-counters.md) için destek sınırlıdır:

* SDK sürümleri 2.4.1 ve üzeri, uygulama Azure Web Apps (Windows) üzerinde çalışıyorsa performans sayaçlarını toplar.
* SDK sürümleri 2.7.1 ve üzeri, uygulama Windows ve hedefler veya sonraki sürümlerde çalışıyorsa performans sayaçlarını toplar `NETSTANDARD2.0` .
* .NET Framework hedefleyen uygulamalar için SDK 'nın tüm sürümleri performans sayaçlarını destekler.
* SDK sürümleri 2.8.0 ve üzeri, Linux 'ta CPU/bellek sayacını destekler. Linux 'ta başka bir sayaç desteklenmez. Linux 'ta (ve diğer Windows dışı ortamlarda) sistem sayaçlarını almanın önerilen yolu [Eventcounters](#eventcounter) kullanmaktır

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` Varsayılan olarak etkindir. [EventCounter](eventcounters.md) öğreticisi toplanacak sayaçların listesini yapılandırmaya ilişkin yönergeler içerir.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Web uygulamaları için istemci tarafı telemetrisini etkinleştir

Yukarıdaki adımlar, sunucu tarafı Telemetriyi toplamaya başlamanıza yardımcı olacak kadar yeterlidir. Uygulamanızda istemci tarafı bileşenleri varsa, [kullanım telemetrisini](./usage-overview.md)toplamaya başlamak için sonraki adımları izleyin.

1. İçinde ekleme `_ViewImports.cshtml` Ekle:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. İçinde `_Layout.cshtml` , `HtmlHelper` bölümünün sonuna, `<head>` ancak diğer herhangi bir betikten önce ekleyin. Sayfadan herhangi bir özel JavaScript telemetrisini raporlamak istiyorsanız, bu kod parçacığına sonra ekleme:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Alternatif olarak, `FullScript` `ScriptBody` SDK v 2.14 ' den itibaren kullanılabilir. `<script>`Bir Içerik güvenlik ilkesi ayarlamak için etiketi kontrol etmeniz gerekiyorsa bunu kullanın:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

`.cshtml`Daha önce başvurulan dosya adları varsayılan BIR MVC uygulama şablonundan alınır. Sonuç olarak, uygulamanız için istemci tarafı izlemeyi doğru şekilde etkinleştirmek istiyorsanız, `<head>` uygulamanızın izlemek istediğiniz her sayfasının bölümünde JavaScript kod parçacığı görünmelidir. Bu uygulama şablonu için JavaScript kod parçacığını ekleyerek bu hedefi gerçekleştirebilirsiniz `_Layout.cshtml` . 

Projeniz içermiyorsa `_Layout.cshtml` , [istemci tarafı izleme](./website-monitoring.md)eklemeye devam edebilirsiniz. Bu, JavaScript kod parçacığını uygulamanızdaki tüm sayfaları denetleyen eşdeğer bir dosyaya ekleyerek yapabilirsiniz `<head>` . Ya da kod parçacığını birden çok sayfaya ekleyebilirsiniz, ancak bu çözümün korunması zordur ve genellikle bunu önermiyoruz.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK 'sını yapılandırma

Varsayılan yapılandırmayı değiştirmek için ASP.NET Core Application Insights SDK 'sını özelleştirebilirsiniz. Application Insights ASP.NET SDK kullanıcıları, kullanarak veya değiştirerek yapılandırma değiştirme konusunda bilgi sahibi olabilir `ApplicationInsights.config` `TelemetryConfiguration.Active` . ASP.NET Core için, siz de yönlendirilmediğiniz takdirde, neredeyse tüm yapılandırma değişiklikleri `ConfigureServices()` sınıfınızın yönteminde yapılır `Startup.cs` . Aşağıdaki bölümler daha fazla bilgi sunar.

> [!NOTE]
> ASP.NET Core uygulamalarda, değiştirme ile yapılandırmayı değiştirme `TelemetryConfiguration.Active` desteklenmez.

### <a name="using-applicationinsightsserviceoptions"></a>Applicationınsightsserviceoptions kullanma

`ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry` Aşağıdaki örnekte olduğu gibi, ' a geçirerek birkaç ortak ayarı değiştirebilirsiniz:

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

İçindeki ayarların tam listesi `ApplicationInsightsServiceOptions`

|Ayar | Açıklama | Varsayılan
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Etkinleştir/devre dışı bırak `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Etkinleştir/devre dışı bırak `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Etkinleştir/devre dışı bırak `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Etkinleştir/devre dışı bırak `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Etkinleştir/devre dışı bırak `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Etkinleştir/devre dışı bırak `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Liveölçümlerini etkinleştir/devre dışı bırak özelliği | true
|Enableadaptiveörnekleme | Uyarlamalı örneklemeyi etkinleştir/devre dışı bırak | true
|Enablesinyal | Düzenli aralıklarla (15 dakikalık varsayılan), .NET sürümü, Azure ortam bilgileri, varsa vb. gibi çalışma zamanı hakkında bilgi içeren "HeartbeatState" adlı özel bir ölçüm göndererek, sinyalleri etkinleştir/devre dışı bırak özelliği. | true
|Addadutocollectedmetricextractor | Örnekleme gerçekleşmeden önce Istekler/bağımlılıklar hakkında önceden toplanmış ölçümler gönderen bir TelemetryProcessor olan otomatik Collectedölçümler ayıklayıcısı 'nı etkinleştirin/devre dışı bırakın. | true
|RequestCollectionOptions. TrackExceptions | Istek koleksiyonu modülü tarafından işlenmeyen özel durum izlemenin raporlamasını etkinleştirin/devre dışı bırakın. | NETSTANDARD 2.0'da false (özel durumlar Applicationınsightsloggerprovider ile izleniyor), aksi takdirde true.
|EnableDiagnosticsTelemetryModule | Etkinleştir/devre dışı bırak `DiagnosticsTelemetryModule` . Bunu devre dışı bırakmak, aşağıdaki ayarların yoksayılmasına neden olur; `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

En güncel listesi için [içindeki `ApplicationInsightsServiceOptions` yapılandırılabilir ayarlar '](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) a bakın.

### <a name="configuration-recommendation-for-microsoftapplicationinsightsaspnetcore-sdk-2150--above"></a>Yukarıdaki & Microsoft. ApplicationInsights. AspNetCore SDK 2.15.0 için yapılandırma önerisi

Microsoft. ApplicationInsights. AspNetCore SDK sürümü [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0)'dan başlayarak, ' de bulunan her ayarı `ApplicationInsightsServiceOptions` , uygulamalar örneği kullanılarak ınstrumentationkey dahil olmak üzere yapılandırmaktır `IConfiguration` . Aşağıdaki örnekte gösterildiği gibi, ayarlar "ApplicationInsights" bölümünde olmalıdır. appsettings.jsüzerindeki aşağıdaki bölüm, izleme anahtarını yapılandırır ve ayrıca Uyarlamalı örnekleme ve performans sayacı toplamayı devre dışı bırakır.

```json
{
    "ApplicationInsights": {
    "InstrumentationKey": "putinstrumentationkeyhere",
    "EnableAdaptiveSampling": false,
    "EnablePerformanceCounterCollectionModule": false
    }
}
```

Kullanılıyorsa `services.AddApplicationInsightsTelemetry(aiOptions)` , ayarlarını geçersiz kılar `Microsoft.Extensions.Configuration.IConfiguration` .

### <a name="sampling"></a>Örnekleme

ASP.NET Core için Application Insights SDK hem sabit hızlı hem de Uyarlamalı örneklemeyi destekler. Uyarlamalı örnekleme varsayılan olarak etkindir.

Daha fazla bilgi için bkz. [ASP.NET Core uygulamalar için uyarlamalı örnekleme yapılandırma](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers ekleme

Ek bilgilerle telemetri zenginleştirmek istediğinizde [telemetri başlatıcıları](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) 'nı kullanın.

`TelemetryInitializer` `DependencyInjection` Aşağıdaki kodda gösterildiği gibi kapsayıcıya yenilerini ekleyin. SDK otomatik olarak kapsayıcıya eklenen her birini seçer `TelemetryInitializer` `DependencyInjection` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

> [!NOTE]
> `services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();` basit başlatıcılar için geçerlidir. Diğerleri için aşağıdakiler gereklidir: `services.AddSingleton(new MyCustomTelemetryInitializer() { fieldName = "myfieldName" });`
    
### <a name="removing-telemetryinitializers"></a>TelemetryInitializers kaldırılıyor

Telemetri başlatıcıları varsayılan olarak mevcuttur. Tüm veya belirli telemetri başlatıcıları 'nı kaldırmak için, çağrısından *sonra* aşağıdaki örnek kodu kullanın `AddApplicationInsightsTelemetry()` .

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

`TelemetryConfiguration`Üzerinde genişletme yöntemi kullanarak ' ye özel telemetri işlemcileri ekleyebilirsiniz `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` . [Gelişmiş filtreleme senaryolarında](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer)telemetri işlemcileri kullanıyorsunuz. Aşağıdaki örneği kullanın.

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

* `RequestTrackingTelemetryModule` -Gelen web isteklerinden Requesttelemetri toplar.
* `DependencyTrackingTelemetryModule` -Giden http çağrılarından ve SQL çağrılarından [Dependencytelemetri](./asp-net-dependencies.md) toplar.
* `PerformanceCollectorModule` -Windows PerformanceCounters sayaçlarını toplar.
* `QuickPulseTelemetryModule` -Canlı ölçüm portalında göstermek için telemetri toplar.
* `AppServicesHeartbeatTelemetryModule` -Uygulamanın barındırıldığı Azure App Service ortamı hakkında, kalpler (özel ölçümler olarak gönderilir) toplar.
* `AzureInstanceMetadataTelemetryModule` -Uygulamanın barındırıldığı Azure VM ortamı hakkında (özel ölçümler olarak gönderilen) kalpler toplar.
* `EventCounterCollectionModule` - [Eventcounters toplar.](eventcounters.md) Bu modül yeni bir özelliktir ve SDK sürümü 2.8.0 ve üzeri sürümlerde kullanılabilir.

Herhangi bir Varsayılanı yapılandırmak için, `TelemetryModule` `ConfigureTelemetryModule<T>` `IServiceCollection` Aşağıdaki örnekte gösterildiği gibi genişletme yöntemini üzerinde kullanın.

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

2.12.2 sürümünden itibaren, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) varsayılan modüllerin hiçbirini devre dışı bırakma kolay bir seçenek içerir.

### <a name="configuring-a-telemetry-channel"></a>Telemetri kanalını yapılandırma

Varsayılan [telemetri kanalı](./telemetry-channels.md) `ServerTelemetryChannel` . Aşağıdaki örnekte gösterildiği gibi, geçersiz kılabilirsiniz.

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

Telemetriyi koşullu ve dinamik olarak devre dışı bırakmak istiyorsanız, `TelemetryConfiguration` kodunuzu kodunuzda herhangi bir yere ASP.NET Core bağımlılık ekleme kapsayıcısı ile çözümleyebilir ve `DisableTelemetry` üzerinde bayrak ayarlayabilirsiniz.

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

### <a name="does-application-insights-support-aspnet-core-3x"></a>Application Insights ASP.NET Core 3. X destekliyor mu?

Evet. ASP.NET Core sürüm 2.8.0 veya üzeri [IÇIN SDK Application Insights](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) güncelleştirme. SDK 'nın daha eski sürümleri ASP.NET Core 3. X sürümünü desteklemez.

Ayrıca, [burada](#enable-application-insights-server-side-telemetry-visual-studio)Visual Studio tabanlı yönergeler kullanıyorsanız, eklemek Için visual Studio 2019 (16.3.0) uygulamasının en son sürümüne güncelleştirin. Visual Studio 'nun önceki sürümleri, ASP.NET Core 3. X uygulamaları için otomatik ekleme 'yi desteklemez.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Otomatik olarak toplanmayan telemetrileri nasıl izleyebilirim?

`TelemetryClient`Oluşturucu Ekleme kullanarak bir örneğini alın ve `TrackXXX()` üzerinde gerekli yöntemi çağırın. `TelemetryClient`ASP.NET Core uygulamasında yeni veya örnek oluşturmanız önerilmez `TelemetryConfiguration` . Tek bir örneği `TelemetryClient` kapsayıcıda zaten kayıtlı olduğundan `DependencyInjection` `TelemetryConfiguration` telemetri geri kalanıyla birlikte paylaşır. Yeni bir `TelemetryClient` örnek oluşturmak yalnızca telemetri geri kalanından ayrı bir yapılandırmaya ihtiyaç duyduğunda önerilir.

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

Application Insights özel veri raporlama hakkında daha fazla bilgi için bkz. [Application Insights özel ölçümler API başvurusu](./api-custom-events-metrics.md). Benzer bir yaklaşım, [GetMetric API 'si](./get-metric.md)kullanılarak Application Insights özel ölçümler göndermek için kullanılabilir.

### <a name="how-do-i-customize-ilogger-logs-collection"></a>ILogger günlükleri koleksiyonunu özelleştirmek Nasıl yaparım? mı?

Varsayılan olarak, yalnızca önem derecesi `Warning` ve yukarıdaki Günlükler otomatik olarak yakalanır. Bu davranışı değiştirmek için, sağlayıcının günlük yapılandırmasını `ApplicationInsights` aşağıda gösterildiği gibi açıkça geçersiz kılın.
Aşağıdaki yapılandırma, ApplicationInsights 'ın tüm önem derecesi ve üst günlüklerini yakalamasına olanak tanır `Information` .

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

Aşağıdaki, ApplicationInsights sağlayıcısının günlükleri yakalamasına neden olmaz `Information` . Bunun nedeni, SDK 'nın `ApplicationInsights` yalnızca ve üstünü yakalamalarını ve bir varsayılan günlük filtresi eklemesine olanak sağlar `Warning` . Bu nedenle, ApplicationInsights için açık bir geçersiz kılma gereklidir.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

[ILogger yapılandırması](ilogger.md#control-logging-level)hakkında daha fazla bilgi edinin.

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Bazı Visual Studio şablonları, Application Insights etkinleştirmek için ıwebhostbuilder üzerinde Useapplicationınsights () genişletme yöntemini kullandı. Bu kullanım hala geçerli mi?

Genişletme yöntemi `UseApplicationInsights()` hala desteklenirken, APPLICATION INSIGHTS SDK sürümü 2.8.0 ve sonraki sürümlerinde artık kullanılmıyor olarak işaretlenir. SDK 'nın bir sonraki ana sürümünde kaldırılacaktır. Application Insights Telemetriyi etkinleştirmenin önerilen yolu, `AddApplicationInsightsTelemetry()` bazı yapılandırmayı denetlemek için aşırı yüklemeler sağladığından kullanmaktır. Ayrıca, ASP.NET Core 3. X uygulamalarında, `services.AddApplicationInsightsTelemetry()` Application Insights 'ı etkinleştirmenin tek yolu vardır.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>ASP.NET Core uygulamamı Web Apps 'ye dağıttım. Web Apps Application Insights uzantısını yine de etkinleştirmem gerekir mi?

SDK, bu makalede gösterildiği gibi derleme zamanında yüklendiyse, App Service portalından [Application Insights uzantısını](./azure-web-apps.md) etkinleştirmeniz gerekmez. Uzantı yüklü olsa bile, SDK 'nın uygulamaya zaten eklendiğini algıladığında, bu geri açılır. Uzantıyı Application Insights etkinleştirirseniz SDK 'yı yükleyip güncelleştirmeniz gerekmez. Ancak bu makaledeki yönergeleri izleyerek Application Insights etkinleştirirseniz şu nedenle daha fazla esneklik elde edersiniz:

   * Application Insights telemetri şu şekilde çalışmaya devam edecektir:
       * Windows, Linux ve Mac gibi tüm işletim sistemleri.
       * Kendi içinde veya çerçevesine bağımlı olan tüm yayımlama modları.
       * Tam .NET Framework dahil olmak üzere tüm hedef çerçeveler.
       * Web Apps, VM 'Ler, Linux, kapsayıcılar, Azure Kubernetes hizmeti ve Azure olmayan barındırma dahil olmak üzere tüm barındırma seçenekleri.
       * Önizleme sürümleri dahil olmak üzere tüm .NET Core sürümleri.
   * Visual Studio 'da hata ayıklarken telemetri ' i yerel olarak görebilirsiniz.
   * API kullanarak ek özel telemetri izleyebilirsiniz `TrackXXX()` .
   * Yapılandırma üzerinde tam denetim sahibi olursunuz.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Durum İzleyicisi gibi araçları kullanarak Application Insights izlemeyi etkinleştirebilir miyim?

Hayır. [Durum İzleyicisi](./monitor-performance-live-website-now.md) ve [durum İzleyicisi v2](./status-monitor-v2-overview.md) Şu anda yalnızca ASP.NET 4. x desteğine sahiptir.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Uygulamamı Linux 'ta çalıştırdım, tüm özellikler destekleniyor mu?

Evet. SDK için özellik desteği, aşağıdaki özel durumlarla birlikte tüm platformlarda aynıdır:

* SDK, [performans sayaçları](./performance-counters.md) yalnızca Windows 'Da desteklendiğinden Linux üzerinde [olay sayaçlarını](./eventcounters.md) toplar. Çoğu ölçüm aynıdır.
* `ServerTelemetryChannel`Varsayılan olarak etkin olsa da, uygulama Linux veya macOS 'ta çalışıyorsa, ağ sorunları varsa Telemetriyi geçici tutmak için kanal otomatik olarak yerel bir depolama klasörü oluşturmaz. Bu sınırlama nedeniyle, geçici ağ veya sunucu sorunları olduğunda telemetri kaybedilir. Bu sorunu geçici olarak çözmek için, kanal için yerel bir klasör yapılandırın:

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

Bu sınırlama, [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) ve daha yeni sürümlerden geçerli değildir.

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Bu SDK, yeni .NET Core 3. X çalışan hizmeti şablonu uygulamaları için destekleniyor mu?

Bu SDK `HttpContext` , .NET Core 3. X çalışan hizmeti uygulamaları dahil olmak üzere http olmayan uygulamalarda çalışmaz. Yeni yayınlanan Microsoft. ApplicationInsights. WorkerService SDK 'sını kullanarak bu uygulamalarda uygulama öngörülerini etkinleştirmek için [Bu](worker-service.md) belgeye başvurun.

## <a name="open-source-sdk"></a>Açık kaynaklı SDK

* [Kodu okuyun ve koda katkıda bulunun](https://github.com/microsoft/ApplicationInsights-dotnet).

En son güncelleştirmeler ve hata düzeltmeleri için [sürüm notlarına bakın](./release-notes.md).

## <a name="next-steps"></a>Sonraki adımlar

* Kullanıcıların uygulamanızda nasıl gezintiğini anlamak için [Kullanıcı akışlarını araştırma](./usage-flows.md) .
* Bir özel durum oluştuğunda kaynak kodu ve değişkenlerin durumunu görmek için [bir anlık görüntü koleksiyonu yapılandırın](./snapshot-debugger.md) .
* Uygulamanızın performans ve kullanımının ayrıntılı bir görünümü için kendi olay ve ölçümlerinizi göndermek üzere [API 'Yi kullanın](./api-custom-events-metrics.md) .
* Uygulamanızı dünyanın her yerindeki sürekli denetlemek için [kullanılabilirlik testlerini](./monitor-web-app-availability.md) kullanın.
* [ASP.NET Core'da Bağımlılık Ekleme](/aspnet/core/fundamentals/dependency-injection)

---
title: Azure Application Insights SDK 'sında filtreleme ve ön işleme | Microsoft Docs
description: Telemetri Application Insights portalına gönderilmeden önce verileri filtrelemek veya eklemek için SDK için telemetri Işlemcileri ve telemetri başlatıcıları yazın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/23/2016
ms.openlocfilehash: 550ac9ff3b425e682fdda16501613aa41a80d765
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847240"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK 'da telemetri filtreleme ve ön işleme

Application Insights hizmetine gönderilmeden önce Telemetriyi nasıl zenginleştirdiğini ve işlenebileceğinizi özelleştirmek için Application Insights SDK 'Sı için eklentiler yazabilir ve yapılandırabilirsiniz.

* [Örnekleme](sampling.md) , istatistiklerinizi etkilemeden telemetri hacmini azaltır. Bir sorunu tanılarken aralarında gezinebilmeniz için ilgili veri noktalarını birlikte tutar. Portalda toplam sayımlar, örnekleme için telafi ile çarpılır.
* Telemetri Işlemcilerle filtreleme, SDK 'da, sunucuya gönderilmeden önce Telemetriyi filtrelemenizi sağlar. Örneğin, robotlardan gelen istekleri dışlayarak telemetri hacmini azaltabilirsiniz. Filtreleme, trafiği örneklemeden azaltmak için daha temel bir yaklaşımdır. Aktarılmaları üzerinde daha fazla denetim sağlar, ancak tüm başarılı istekleri filtrelemeniz gibi, istatistiklerinizi etkilediğine dikkat etmeniz gerekir.
* Telemetri başlatıcıları, standart modüllerden gelen telemetri dahil olmak üzere uygulamanızdan gönderilen herhangi bir telemetriye [Özellikler ekler veya değiştirir](#add-properties) . Örneğin, hesaplanmış değerler ekleyebilirsiniz; veya portaldaki verilerin filtreleneceği sürüm numaraları.
* [SDK API 'si](../../azure-monitor/app/api-custom-events-metrics.md) özel olayları ve ölçümleri göndermek için kullanılır.

Başlamadan önce:

* Uygulamanız için uygun SDK 'Yı yükler: .NET/.NET Core, [Java](../../azure-monitor/app/java-get-started.md) veya [JavaScript](javascript.md) için [ASP.net](asp-net.md), [ASP.NET Core](asp-net-core.md), [http olmayan/Worker](worker-service.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtreleme

Bu teknik, telemetri akışından dahil edilen veya dışlanan Özellikler üzerinde doğrudan denetim elde etmenizi sağlar. Filtreleme, telemetri öğelerini Application Insights gönderilmeden bırakmak için kullanılabilir. Bunu örneklemeyle veya ayrı olarak kullanabilirsiniz.

Telemetriyi filtrelemek için bir telemetri işlemcisi yazın ve `TelemetryConfiguration`kaydedin. Tüm telemetri işlemcinizden geçer ve akıştan bırakmayı veya zincirdeki bir sonraki işlemciye vermenizi seçebilirsiniz. Bu, HTTP istek toplayıcısı ve bağımlılık toplayıcısı gibi standart modüllerden ve kendi kendinize izettiğiniz Telemetriyi içerir. Örneğin, robots veya başarılı bağımlılık çağrılarından gelen istekler hakkında telemetri filtreleyebilirsiniz.

> [!WARNING]
> İşlemciler kullanılarak SDK 'dan gönderilen telemetrinin filtrelenmesi, portalda gördüğünüz istatistikleri eğebilir ve ilgili öğeleri izlemeyi zorlaştırır.
>
> Bunun yerine [örnekleme](../../azure-monitor/app/sampling.md)kullanmayı düşünün.
>
>

### <a name="create-a-telemetry-processor-c"></a>Telemetri işlemcisi (C#) oluşturma

1. Bir filtre oluşturmak için `ITelemetryProcessor`uygulayın.

    Telemetri Işlemcilerin bir işlem zinciri oluşturduğuna dikkat edin. Bir telemetri işlemcisini örneklediğinizde, zincirdeki bir sonraki işlemciye bir başvuru verilir. Bir telemetri veri noktası Işlem yöntemine geçirildiğinde, işi çalışır ve sonra zincirde bir sonraki telemetri Işlemcisini çağırır (veya çağırmaz).

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. İşlemcinizi ekleyin.

**ASP.NET uygulamaları** Bu kod parçacığını ApplicationInsights. config dosyasına ekleyin:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Sınıfınıza ortak adlandırılmış özellikler sağlayarak. config dosyasından dize değerlerini geçirebilirsiniz.

> [!WARNING]
> . Config dosyasındaki tür adı ve tüm özellik adlarını, koddaki sınıf ve özellik adlarına eşleştirmek için dikkatli olmak. . Config dosyası var olmayan bir türe veya özelliğe başvuruyorsa, SDK hiçbir Telemetriyi sessizce gönderemeyebilir.
>

**Alternatif olarak,** kodda filtreyi başlatabilirsiniz. Uygun bir başlatma sınıfında; Örneğin, `Global.asax.cs`: uygulama zincirinde

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Bu noktadan sonra oluşturulan TelemetryClients, işlemcinizi kullanacaktır.

**ASP.NET Core/çalışan hizmeti uygulamaları**

> [!NOTE]
> `ApplicationInsights.config` veya `TelemetryConfiguration.Active` kullanarak işlemciyi eklemek ASP.NET Core uygulamaları için geçerli değildir veya Microsoft. ApplicationInsights. WorkerService SDK kullanıyorsanız.

[ASP.NET Core](asp-net-core.md#adding-telemetry-processors) veya [workerservice](worker-service.md#adding-telemetry-processors)kullanılarak yazılan uygulamalar için, aşağıda gösterildiği gibi yeni bir `TelemetryProcessor` eklemek `IServiceCollection`üzerinde `AddApplicationInsightsTelemetryProcessor` genişletme yöntemi kullanılarak yapılır. Bu yöntem, `Startup.cs` sınıfınızın `ConfigureServices` yönteminde çağrılır.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Örnek filtreler

#### <a name="synthetic-requests"></a>Yapay istekler

Botları ve Web testlerini filtreleyin. Ölçüm Gezgini yapay kaynakları filtreleme seçeneği sunmakla birlikte bu seçenek, trafiği ve alma boyutunu SDK 'da filtreleyerek azaltır.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Kimlik doğrulaması başarısız

İstekleri "401" yanıtıyla filtreleyin.

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Hızlı uzak bağımlılık çağrılarını filtrele

Yalnızca yavaş olan çağrıları tanılamak istiyorsanız hızlı bir şekilde filtreleyin.

> [!NOTE]
> Bu, portalda gördüğünüz istatistikleri eğriltebilir.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Bağımlılık sorunlarını tanılama

[Bu blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) , bağımlılıklara otomatik olarak ping göndererek bağımlılık sorunlarını tanılamaya yönelik bir projeyi açıklar.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript Web uygulamaları

**Ilemetrybaşlatıcısı kullanarak filtreleme**

1. Telemetri başlatıcısı geri çağırma işlevi oluşturun. Geri çağırma işlevi, işlenen olay olan `ITelemetryItem` bir parametre olarak alır. Bu geri aramadan `false` döndürmek telemetri öğesinin filtrelenmesini sağlar.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. Telemetri başlatıcısı geri aramasını ekleyin:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Özellik Ekle/Değiştir: ıtelemetrybaşlatıcısı


Telemetri başlatıcıları 'nı ek bilgilerle birlikte zenginleştirmek ve/veya standart telemetri modülleri tarafından ayarlanan telemetri özelliklerini geçersiz kılmak için kullanın.

Örneğin, Web paketi için Application Insights HTTP istekleri hakkında telemetri toplar. Varsayılan olarak, yanıt kodu > = 400 olan herhangi bir istek başarısız olarak işaretler. Ancak, 400 ' i başarılı olarak değerlendirmek istiyorsanız, başarı özelliğini ayarlayan bir telemetri başlatıcısı sağlayabilirsiniz.

Bir telemetri başlatıcısı sağlarsanız, her bir Track * () yöntemi çağrıldığında çağırılır. Buna standart telemetri modülleri tarafından çağrılan `Track()` Yöntemler dahildir. Kurala göre, bu modüller zaten bir başlatıcı tarafından ayarlanmış herhangi bir özelliği ayarlamayın. Telemetri başlatıcıları, telemetri işlemcileri çağrılmadan önce çağrılır. Bu nedenle, başlatıcıların yaptığı her türlü zenginleştirme işlemciler tarafından görülebilir.

**Başlatıcısını tanımlama**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET uygulamalar: başlatıcısını yükleme**

ApplicationInsights. config dosyasında:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Alternatif olarak,* Global.aspx.cs içinde örneğin, başlatıcıda Başlatıcı örneğini oluşturabilirsiniz:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Bu örnekten daha fazla bilgi görüntüleyin.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core/çalışan hizmeti uygulamaları: başlatıcısını yükleyin**

> [!NOTE]
> `ApplicationInsights.config` veya `TelemetryConfiguration.Active` kullanarak Başlatıcı eklemek ASP.NET Core uygulamaları için geçerli değildir veya Microsoft. ApplicationInsights. WorkerService SDK kullanıyorsanız.

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) veya [workerservice](worker-service.md#adding-telemetryinitializers)kullanılarak yazılan uygulamalar için, aşağıda gösterildiği gibi, yeni bir `TelemetryInitializer` eklemek, bağımlılık ekleme kapsayıcısına eklenerek yapılır. Bu, `Startup.ConfigureServices` yönteminde yapılır.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Java telemetri başlatıcıları

[Java SDK belgeleri](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Ardından, özel başlatıcıyı ApplicationInsights. xml dosyanıza kaydedin.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>JavaScript telemetri başlatıcıları
*JavaScript*

Portaldan aldığınız başlatma kodundan hemen sonra bir telemetri başlatıcısı ekleyin:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

TelemetryItem üzerinde bulunan özel olmayan özelliklerin bir özeti için bkz. [Application Insights dışarı aktarma veri modeli](../../azure-monitor/app/export-data-model.md).

İstediğiniz kadar fazla Başlatıcı ekleyebilirsiniz ve bunlar eklendikleri sırada çağırılır.

### <a name="example-telemetryinitializers"></a>Örnek TelemetryInitializers

#### <a name="add-custom-property"></a>Özel Özellik Ekle

Aşağıdaki örnek başlatıcı, izlenen her telemetriye özel bir özellik ekler.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Bulut rolü adı Ekle

Aşağıdaki örnek başlatıcı, bulut rolü adını izlenen her telemetriye ayarlar.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>Ilemetryprocessor ve ılemetrybaşlatıcısı

Telemetri işlemcileri ve telemetri başlatıcıları arasındaki fark nedir?

* Bunlarla neler yapabileceğinize ilişkin bazı örtüşmeler vardır: her ikisi de telemetri özellikleri eklemek veya değiştirmek için kullanılabilir, ancak bu amaçla başlatıcılar kullanılması önerilir.
* TelemetryInitializers, TelemetryProcessors 'den önce her zaman çalıştırın.
* TelemetryInitializers birden çok kez çağrılabilir. Kurala göre, zaten ayarlanmış herhangi bir özelliği ayarlamayın.
* TelemetryProcessors bir telemetri öğesini tamamen değiştirmenizi veya atmayı sağlar.
* Tüm kayıtlı TelemetryInitializers her telemetri öğesi için çağrılması garanti edilir. Telemetri işlemcileri için SDK, ilk telemetri işlemcisinin çağrılmasını garanti eder. İşlemcilerin geri kalanının adlandırılıp çağrılmayacağı, önceki telemetri işlemcileri tarafından belirlenir.
* Ek özelliklerle telemetri zenginleştirmek veya var olanı geçersiz kılmak için TelemetryInitializers kullanın. Telemetriyi filtrelemek için TelemetryProcessor kullanın.

## <a name="troubleshooting-applicationinsightsconfig"></a>ApplicationInsights. config sorunlarını giderme

* Tam nitelikli tür adı ve derleme adının doğru olduğunu onaylayın.
* ApplicationInsights. config dosyasının çıkış dizininizde olduğunu ve en son değişiklikleri içerdiğini doğrulayın.

## <a name="reference-docs"></a>Başvuru belgeleri

* [API’ye Genel Bakış](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET başvurusu](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK kodu

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK'sı](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Sonraki adımlar
* [Olayları ve günlükleri ara](../../azure-monitor/app/diagnostic-search.md)
* [Örnekleme](../../azure-monitor/app/sampling.md)
* [Sorun giderme](../../azure-monitor/app/troubleshoot-faq.md)

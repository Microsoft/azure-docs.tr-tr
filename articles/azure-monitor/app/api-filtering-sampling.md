---
title: Application Insights SDK 'da filtreleme ve ön işleme | Microsoft Docs
description: Telemetri Application Insights portalına gönderilmeden önce verileri filtrelemek veya eklemek için SDK için telemetri işlemcileri ve telemetri başlatıcıları yazın.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cb9159b98b219c6fb04beb7bbbaade64fc72a30b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98927755"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK 'da telemetri filtreleme ve ön işleme

Application Insights hizmetine gönderilmeden önce Telemetriyi nasıl zenginleştirdiğini ve işlenebileceğinizi özelleştirmek için Application Insights SDK 'Sı için eklentiler yazabilir ve yapılandırabilirsiniz.

* [Örnekleme](sampling.md) , istatistiklerinizi etkilemeden telemetri hacmini azaltır. Bir sorunu tanılarken bunlar arasında gezinebilmeniz için ilgili veri noktalarını birlikte tutar. Portalda toplam sayımlar, örnekleme için telafi ile çarpılır.
* Telemetri işlemcileriyle filtreleme, SDK 'da, sunucuya gönderilmeden önce Telemetriyi filtrelemenizi sağlar. Örneğin, robotlardan gelen istekleri dışlayarak telemetri hacmini azaltabilirsiniz. Filtreleme, trafiği örneklemeden azaltmak için daha temel bir yaklaşımdır. İletilen Özellikler üzerinde daha fazla denetim sağlar, ancak istatistiklerinizi etkiler. Örneğin, tüm başarılı istekleri filtreleyebilirsiniz.
* Telemetri başlatıcıları, uygulamanız tarafından gönderilen ve standart modüllerden Telemetriyi içeren herhangi bir telemetriye [Özellikler ekler veya değiştirir](#add-properties) . Örneğin, portalda verilerin filtreleneceği Hesaplanmış değerler veya sürüm numaraları ekleyebilirsiniz.
* [SDK API 'si](./api-custom-events-metrics.md) özel olayları ve ölçümleri göndermek için kullanılır.

Başlamadan önce:

* Uygulamanız için uygun SDK 'yı yükler: .NET/.NET Core veya [JavaScript](javascript.md)için [ASP.net](asp-net.md), [ASP.NET Core](asp-net-core.md), [http olmayan/Worker](worker-service.md).

<a name="filtering"></a>

## <a name="filtering"></a>Filtreleme

Bu teknik, telemetri akışından dahil edilen veya hariç tutulan Özellikler üzerinde doğrudan denetim sağlar. Filtreleme, telemetri öğelerini Application Insights gönderilmeden bırakmak için kullanılabilir. Süzmeyi, örneklemeyle veya ayrı olarak kullanabilirsiniz.

Telemetriyi filtrelemek için bir telemetri işlemcisi yazın ve bunu ile kaydedin `TelemetryConfiguration` . Tüm telemetri işlemcinizde gider. Akıştan bırakmayı veya zincirde bir sonraki işlemciye vermenizi seçebilirsiniz. HTTP istek toplayıcısı ve bağımlılık toplayıcısı gibi standart modüllerden alınan telemetri ve kendi kendinize izlenen telemetri dahil edilmiştir. Örneğin, robots veya başarılı bağımlılık çağrılarından gelen istekler hakkında telemetri filtreleyebilirsiniz.

> [!WARNING]
> İşlemciler kullanılarak SDK 'dan gönderilen telemetrinin filtrelenmesi, portalda gördüğünüz istatistikleri eğebilir ve ilgili öğeleri izlemeyi zorlaştırır.
>
> Bunun yerine [örnekleme](./sampling.md)kullanmayı düşünün.
>
>

### <a name="create-a-telemetry-processor-c"></a>Telemetri işlemcisi oluşturma (C#)

1. Filtre oluşturmak için uygulamasını uygulayın `ITelemetryProcessor` .

    Telemetri işlemcileri bir işlem zinciri oluşturur. Bir telemetri işlemcisini örneklediğinizde, zincirdeki bir sonraki işlemciye bir başvuru vermiş olursunuz. Bir telemetri veri noktası işlem yöntemine geçirildiğinde, işi çalışır ve sonra zincirde bir sonraki telemetri işlemcisini çağırır (veya çağırmaz).

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

ASP.NET **uygulamaları**

Bu kod parçacığını ApplicationInsights.config ekleyin:

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
> . Config dosyasındaki tür adı ve tüm özellik adlarını, koddaki sınıf ve özellik adlarına eşleştirmek için dikkatli olmak. . Config dosyası varolmayan bir tür veya özelliğe başvuruyorsa, SDK hiçbir Telemetriyi sessizce gönderemeyebilir.
>

Alternatif olarak, kodda filtreyi başlatabilirsiniz. Uygun bir başlatma sınıfında, örneğin, AppStart içinde, `Global.asax.cs` işlemcisini zincirine ekleyin:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Bu noktadan sonra oluşturulan telemetri istemcileri, işlemcinizi kullanacaktır.

ASP.NET **Core/Worker hizmeti uygulamaları**

> [!NOTE]
> Veya kullanarak bir işlemci eklemek `ApplicationInsights.config` `TelemetryConfiguration.Active` ASP.NET Core uygulamalar için geçerli değildir veya Microsoft. ApplicationInsights. WORKERSERVICE SDK 'sını kullanıyorsanız.

[ASP.NET Core](asp-net-core.md#adding-telemetry-processors) veya [workerservice](worker-service.md#adding-telemetry-processors)kullanılarak yazılan uygulamalar için yeni bir telemetri işlemcisi eklemek, ' de `AddApplicationInsightsTelemetryProcessor` gösterildiği gibi, üzerindeki genişletme yöntemi kullanılarak yapılır `IServiceCollection` . Bu yöntem, `ConfigureServices` sınıfınızın yönteminde çağrılır `Startup.cs` .

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
> Bu filtreleme, portalda gördüğünüz istatistikleri çarpıtacaktır.
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

#### <a name="diagnose-dependency-issues"></a>Bağımlılık sorunlarını Tanıla

[Bu blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) , bağımlılıklara otomatik olarak ping göndererek bağımlılık sorunlarını tanılamaya yönelik bir projeyi açıklar.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript Web uygulamaları

**Ilemetrybaşlatıcısı kullanarak filtrele**

1. Telemetri başlatıcısı geri çağırma işlevi oluşturun. Geri çağırma işlevi `ITelemetryItem` , işlenen olay olan bir parametre olarak alır. `false`Bu geri aramadan geri dönmek için telemetri öğesinin filtrelenme sonucu olur.

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

Telemetri başlatıcıları 'nı kullanarak ek bilgilerle telemetri zenginleştirin veya standart telemetri modülleri tarafından ayarlanan telemetri özelliklerini geçersiz kılabilirsiniz.

Örneğin, bir Web paketi için Application Insights HTTP istekleri hakkında telemetri toplar. Varsayılan olarak, yanıt kodu >= 400 olan herhangi bir istek başarısız olarak işaretler. Ancak, 400 ' i başarılı olarak değerlendirmek istiyorsanız, başarı özelliğini ayarlayan bir telemetri başlatıcısı sağlayabilirsiniz.

Bir telemetri başlatıcısı sağlarsanız, her bir Track * () yöntemi çağrıldığında çağırılır. Bu `Track()` , standart telemetri modülleri tarafından çağrılan yöntemleri içerir. Kurala göre, bu modüller zaten bir başlatıcı tarafından ayarlanmış herhangi bir özelliği ayarlanmamış. Telemetri başlatıcıları, telemetri işlemcileri çağrılmadan önce çağrılır. Bu nedenle, başlatıcıların yaptığı her türlü zenginleştirme işlemciler tarafından görülebilir.

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

ASP.NET **uygulamalar: başlatıcısını yükleme**

ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

Alternatif olarak, örneğin Global. aspx. cs içinde, kodda Başlatıcı örneği oluşturabilirsiniz:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Bu örnekten](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)daha fazla bilgi görüntüleyin.

ASP.NET **Core/Worker hizmeti uygulamaları: başlatıcısını yükleyin**

> [!NOTE]
> Veya kullanarak bir başlatıcı eklemek `ApplicationInsights.config` `TelemetryConfiguration.Active` ASP.NET Core uygulamalar için geçerli değildir veya Microsoft. ApplicationInsights. WORKERSERVICE SDK 'sını kullanıyorsanız.

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) veya [workerservice](worker-service.md#adding-telemetryinitializers)kullanılarak yazılan uygulamalar için yeni bir telemetri başlatıcısı eklemek, bunu gösterildiği gibi bağımlılık ekleme kapsayıcısına ekleyerek yapılır. Bu, `Startup.ConfigureServices` yönteminde yapılır.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
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

    appInsights.addTelemetryInitializer(function (envelope) {
        var telemetryItem = envelope.data.baseData;

        // To check the telemetry items type - for example PageView:
        if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
            // this statement removes url from all page view documents
            telemetryItem.url = "URL CENSORED";
        }

        // To set custom properties:
        telemetryItem.properties = telemetryItem.properties || {};
        telemetryItem.properties["globalProperty"] = "boo";
        
        // To set cloud role name / instance
        envelope.tags["ai.cloud.role"] = "your role name";
        envelope.tags["ai.cloud.roleInstance"] = "your role instance";
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Telemetri öğesinde kullanılabilen özel olmayan özelliklerin bir özeti için bkz. [Application Insights dışa aktarma veri modeli](./export-data-model.md).

İstediğiniz kadar fazla sayıda Başlatıcı ekleyebilirsiniz. Bunlar eklendikleri sırayla çağırılır.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python telemetri işlemcileri

OpenCensus Python 'da telemetri işlemcileri, yalnızca Telemetriyi işlemek için çağrılan geri çağırma işlevleridir. Geri çağırma işlevi, parametresi olarak bir [Envelope](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) veri türü kabul etmelidir. Telemetriyi dışarı aktarma işleminden filtrelemek için geri çağırma işlevinin döndürdüğünden emin olun `False` . Azure Izleyici veri türleri şemasını [GitHub](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)'daki zarfların içinde görebilirsiniz.

> [!NOTE]
> `cloud_RoleName`Alanındaki özniteliğini değiştirerek ' i değiştirebilirsiniz `ai.cloud.role` `tags` .

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
İstediğiniz kadar çok işlemci ekleyebilirsiniz. Bunlar eklendikleri sırayla çağırılır. Bir işlemci bir özel durum oluşturursa, aşağıdaki işlemcileri etkilemez.

### <a name="example-telemetryinitializers"></a>Örnek TelemetryInitializers

#### <a name="add-a-custom-property"></a>Özel özellik ekleme

Aşağıdaki örnek başlatıcı, izlenen her telemetriye özel bir özellik ekler.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>Bulut rolü adı ekleme

Aşağıdaki örnek başlatıcı, bulut rolü adını izlenen her telemetriye ayarlar.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>HttpContext 'ten bilgi ekleme

Aşağıdaki örnek başlatıcı verileri okur [`HttpContext`](/aspnet/core/fundamentals/http-context) ve bir `RequestTelemetry` örneğe ekler. , `IHttpContextAccessor` Oluşturucu bağımlılığı ekleme yoluyla otomatik olarak sağlanır.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>Ilemetryprocessor ve ılemetrybaşlatıcısı

Telemetri işlemcileri ve telemetri başlatıcıları arasındaki fark nedir?

* Bunlarla neler yapabileceğinize ilişkin bazı örtüşmeler vardır. Her ikisi de telemetri özellikleri eklemek veya değiştirmek için kullanılabilir, ancak bu amaçla başlatıcılar kullanmanızı öneririz.
* Telemetri başlatıcıları her zaman telemetri işlemcisinden önce çalışır.
* Telemetri başlatıcıları birden çok kez çağrılabilir. Kurala göre, önceden ayarlanmış herhangi bir özelliği ayarlanmazlar.
* Telemetri işlemcileri, bir telemetri öğesini tamamen değiştirmenizi veya atmayı sağlar.
* Tüm kayıtlı telemetri başlatıcılarının her telemetri öğesi için çağrılması garanti edilir. Telemetri işlemcileri için SDK, ilk telemetri işlemcisinin çağrılmasını garanti eder. İşlemcilerin geri kalanının adlandırılıp çağrılmayacağı, önceki telemetri işlemcileri tarafından kararanıp kararanıp kararanıp olmadığına bakılmaksızın.
* Telemetri başlatıcıları 'nı kullanarak ek özelliklerle telemetri zenginleştirin veya var olan bir Varolanı geçersiz kılabilirsiniz. Telemetriyi filtrelemek için telemetri işlemcisi kullanın.

## <a name="troubleshoot-applicationinsightsconfig"></a>ApplicationInsights.config sorunlarını giderme

* Tam nitelikli tür adı ve derleme adının doğru olduğunu onaylayın.
* applicationinsights.config dosyasının çıkış dizininizde olduğunu ve en son değişiklikleri içerdiğini doğrulayın.

## <a name="reference-docs"></a>Başvuru belgeleri

* [API’ye genel bakış](./api-custom-events-metrics.md)
* [ASP.NET başvurusu](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>SDK kodu

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK'sı](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Sonraki adımlar
* [Olayları ve günlükleri ara](./diagnostic-search.md)
* [aşağıdakine](./sampling.md)
* [Sorun giderme](../faq.md)


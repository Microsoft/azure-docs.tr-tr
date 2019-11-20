---
title: Azure Application Insights telemetri bağıntısı | Microsoft Docs
description: Application Insights telemetri bağıntısı
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: bcdc6633980ec3684217c8c19b4799befe2af3a3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576868"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights telemetri bağıntısı

Mikro hizmetler dünyasında, her mantıksal işlem, hizmetin çeşitli bileşenlerinde iş yapılmasını gerektirir. Bu bileşenlerin her biri, [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)tarafından ayrı olarak izlenebilir. Application Insights, hangi bileşenin hatalardan veya performans düşüşüne karşı sorumlu olduğunu saptamak için kullandığınız dağıtılmış telemetri bağıntısını destekler.

Bu makalede, birden çok bileşen tarafından gönderilen telemetrinin ilişkilendirilmesi için Application Insights tarafından kullanılan veri modeli açıklanmaktadır. Bağlam yayma tekniklerini ve protokollerini içerir. Ayrıca farklı diller ve platformlarda bağıntı kavramlarının uygulanmasını da içerir.

## <a name="data-model-for-telemetry-correlation"></a>Telemetri bağıntısı için veri modeli

Application Insights, dağıtılmış telemetri bağıntısı için bir [veri modeli](../../azure-monitor/app/data-model.md) tanımlar. Telemetrinin mantıksal işlemle ilişkilendirilmesi için, her telemetri öğesinin `operation_Id`adlı bir bağlam alanı vardır. Bu tanımlayıcı, dağıtılmış izlemede her telemetri öğesi tarafından paylaşılır. Bu nedenle, tek bir katmanda telemetri kaybı ile birlikte, diğer bileşenler tarafından raporlanan Telemetriyi yine de ilişkilendirebilirsiniz.

Dağıtılmış bir mantıksal işlem genellikle bileşenlerden biri tarafından işlenen istekler olan daha küçük işlemler kümesinden oluşur. Bu işlemler [istek telemetrisi](../../azure-monitor/app/data-model-request-telemetry.md)tarafından tanımlanır. Her istek telemetrisi, kendisini benzersiz ve küresel olarak tanımlayan kendi `id` sahiptir. Ve bu istekle ilişkili tüm telemetri öğeleri (izlemeler ve özel durumlar gibi), `operation_parentId` istek `id`değerine ayarlanmalıdır.

Başka bir bileşene yönelik HTTP çağrısı gibi her giden işlem, [bağımlılık telemetrisi](../../azure-monitor/app/data-model-dependency-telemetry.md)tarafından temsil edilir. Bağımlılık telemetrisi, genel olarak benzersiz olan kendi `id` de tanımlar. Bu bağımlılık çağrısıyla başlatılan istek telemetrisi, `operation_parentId`olarak bu `id` kullanır.

`dependency.id`ile `operation_Id`, `operation_parentId`ve `request.id` kullanarak dağıtılmış mantıksal işlemin bir görünümünü oluşturabilirsiniz. Bu alanlar telemetri çağrılarının önem derecesine göre de tanımlar.

Mikro hizmetler ortamında, bileşenlerden izlemeler farklı depolama öğelerine gidebilir. Her bileşen Application Insights kendi izleme anahtarına sahip olabilir. Mantıksal işlem için telemetri almak için Application Insights UX her depolama öğesinden verileri sorgular. Depolama öğelerinin sayısı çok büyük olduğunda, ileri bakabileceğiniz bir ipucu gerekir. Application Insights veri modeli, bu sorunu çözmek için iki alanı tanımlar: `request.source` ve `dependency.target`. İlk alan, bağımlılık isteğini Başlatan bileşeni tanımlar ve ikincisi, bağımlılık çağrısının yanıtını döndüren bileşeni tanımlar.

## <a name="example"></a>Örnek

`Stock`adlı bir dış API kullanarak bir hisse senedinin geçerli pazar fiyatını gösteren Stok fiyatları adlı bir uygulamaya örnek atalım. Hisse senedi fiyatları uygulamasının, `GET /Home/Stock`kullanarak istemci Web tarayıcısının açtığı `Stock page` adlı bir sayfası vardır. Uygulama, HTTP çağrısı `GET /api/stock/value`kullanarak `Stock` API 'sini sorgular.

Bir sorgu çalıştırarak elde edilen telemetrisini çözümleyebilirsiniz:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Sonuçlarda, tüm telemetri öğelerinin kök `operation_Id`paylaştığından emin olduğunu unutmayın. Sayfadan bir AJAX çağrısı yapıldığında, bağımlılık telemetrisine yeni bir benzersiz KIMLIK (`qJSXU`) atanır ve pageView KIMLIĞI `operation_ParentId`olarak kullanılır. Sunucu isteği daha sonra `operation_ParentId`olarak Ajax KIMLIĞINI kullanır.

| ItemType   | ad                      | Kimlik           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| Sayfa görüntülemesi   | Hisse senedi sayfası                |              | Stilz               | Stilz         |
| bağımlılık | /Home/Stock al           | qJSXU        | Stilz               | Stilz         |
| İsteyen    | Giriş/stok al            | KqKwlrSt9PA = | qJSXU              | Stilz         |
| bağımlılık | /Api/Stock/Value al      | bBrf2L7mm2g = | KqKwlrSt9PA =       | Stilz         |

Çağrı `GET /api/stock/value` bir dış hizmete yapıldığında, `dependency.target` alanını uygun şekilde ayarlayabilmeniz için bu sunucunun kimliğini bilmek istersiniz. Dış hizmet izlemeyi desteklemiyorsa, `target` hizmetin ana bilgisayar adına ayarlanır (örneğin, `stock-prices-api.com`). Bununla birlikte, hizmet kendisini önceden tanımlı bir HTTP üst bilgisi döndürerek tanımlarsa, Application Insights, bu hizmetten telemetri sorgulayarak dağıtılmış bir izleme oluşturmasına izin veren hizmet kimliğini `target` içerir.

## <a name="correlation-headers"></a>Bağıntı üstbilgileri

Şunu tanımlayan [W3C Trace-Context](https://w3c.github.io/trace-context/) 'e geçiş yapıyoruz:

- `traceparent`: genel olarak benzersiz işlem KIMLIĞI ve çağrının benzersiz tanımlayıcısını taşır.
- `tracestate`: sisteme özgü izleme bağlamını taşır.

Application Insights SDK 'ların en son sürümleri Trace-Context protokolünü destekler, ancak bunu kabul etmeniz gerekebilir (ApplicationInsights SDK 'Ları tarafından desteklenen eski Bağıntı protokolü ile geriye dönük uyumluluk sağlar).

[Bağıntı http Protokolü diğer adıyla istek kimliği](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) , kullanımdan kaldırma yolunda. Bu protokol iki üstbilgiyi tanımlar:

- `Request-Id`: çağrının genel benzersiz KIMLIĞINI taşır.
- `Correlation-Context`: dağıtılmış izleme özelliklerinin ad-değer çiftleri koleksiyonunu taşır.

Application Insights ayrıca bağıntı HTTP protokolünün [uzantısını](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) tanımlar. Anında çağıran veya çağrılan tarafından kullanılan özellik koleksiyonunu yaymak için `Request-Context` ad-değer çiftlerini kullanır. Application Insights SDK `dependency.target` ve `request.source` alanlarını ayarlamak için bu üstbilgiyi kullanır.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Klasik ASP.NET uygulamaları için W3C dağıtılmış izleme desteğini etkinleştir
 
  > [!NOTE]
  > `Microsoft.ApplicationInsights.Web` ve `Microsoft.ApplicationInsights.DependencyCollector` başlayarak yapılandırma gerekmiyor 

W3C Trace-Context support, geriye dönük olarak uyumlu şekilde yapılır ve bağıntı, önceki SDK sürümleriyle (W3C desteği olmadan) işaretlenmiş uygulamalarla birlikte çalışmak üzere beklenmektedir. 

Eski `Request-Id` protokolünü kullanarak korumak istediğiniz nedenlerden dolayı, izleme bağlamını aşağıdaki yapılandırmayla *devre dışı bırakabilirsiniz*

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

SDK 'nın eski bir sürümünü çalıştırırsanız, bunu güncelleştirmenizi veya Izleme bağlamını etkinleştirmek için aşağıdaki yapılandırmayı uygulamayı öneririz.
Bu özellik, 2.8.0-Beta1 sürümü ile başlayan `Microsoft.ApplicationInsights.Web` ve `Microsoft.ApplicationInsights.DependencyCollector` paketlerinde kullanılabilir.
Varsayılan olarak devre dışıdır. Etkinleştirmek için `ApplicationInsights.config`değiştirin:

- `RequestTrackingTelemetryModule`altında, değeri `true`olarak ayarlanan `EnableW3CHeadersExtraction` öğesini ekleyin.
- `DependencyTrackingTelemetryModule`altında, değeri `true`olarak ayarlanan `EnableW3CHeadersInjection` öğesini ekleyin.
- Şuna benzer `TelemetryInitializers` `W3COperationCorrelationTelemetryInitializer` ekleyin 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>ASP.NET Core uygulamalar için W3C dağıtılmış izleme desteğini etkinleştir

 > [!NOTE]
  > `Microsoft.ApplicationInsights.AspNetCore` Version 2.8.0 ile başlayan yapılandırma gerekmiyor.
 
W3C Trace-Context support, geriye dönük olarak uyumlu şekilde yapılır ve bağıntı, önceki SDK sürümleriyle (W3C desteği olmadan) işaretlenmiş uygulamalarla birlikte çalışmak üzere beklenmektedir. 

Eski `Request-Id` protokolünü kullanarak korumak istediğiniz nedenlerden dolayı, izleme bağlamını aşağıdaki yapılandırmayla *devre dışı bırakabilirsiniz*

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

SDK 'nın eski bir sürümünü çalıştırırsanız, bunu güncelleştirmenizi veya Izleme bağlamını etkinleştirmek için aşağıdaki yapılandırmayı uygulamayı öneririz.

Bu özellik `Microsoft.ApplicationInsights.AspNetCore` sürüm 2.5.0-Beta1 ve `Microsoft.ApplicationInsights.DependencyCollector` Version 2.8.0-Beta1 ' dır.
Varsayılan olarak devre dışıdır. Etkinleştirmek için `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` `true`olarak ayarlayın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Java uygulamaları için W3C dağıtılmış izleme desteğini etkinleştir

- **Gelen yapılandırma**

  - Java EE uygulamaları için, ApplicationInsights. xml içinde `<TelemetryModules>` etiketine aşağıdakileri ekleyin:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Spring Boot uygulamaları için aşağıdaki özellikleri ekleyin:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Giden yapılandırma**

  Aşağıdakileri AI-Agent. xml ' ye ekleyin:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Geriye dönük uyumluluk modu varsayılan olarak etkindir ve `enableW3CBackCompat` parametresi isteğe bağlıdır. Bunu yalnızca geriye dönük uyumluluğu kapatmak istediğinizde kullanın.
  >
  > İdeal olarak, tüm hizmetleriniz W3C protokolünü destekleyen SDK 'ların daha yeni sürümlerine güncelleştirildiği zaman bunu devre dışı bırakabilirsiniz. Bu yeni SDK 'lara mümkün olan en kısa sürede geçiş yapmanızı önemle tavsiye ederiz.

> [!IMPORTANT]
> Hem gelen hem de giden yapılandırmaların tam olarak aynı olduğundan emin olun.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Web Apps için W3C dağıtılmış izleme desteğini etkinleştir

Bu özellik `Microsoft.ApplicationInsights.JavaScript`. Varsayılan olarak devre dışıdır. Etkinleştirmek için `distributedTracingMode` config kullanın. AI_AND_W3C, eski Application Insights belgelenmiş hizmetlerle geri uyumluluk için sağlanır:

- **NPM kurulumu (kod parçacığı kurulumu kullanılıyorsa yoksay)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Kod parçacığı kurulumu (NPM kurulumu kullanılıyorsa yoksay)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...Other Configuration Options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing ve Application Insights

[Opentracing veri modeli belirtimi](https://opentracing.io/) ve Application Insights veri modelleri aşağıdaki şekilde eşlenir:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` `span.kind = server`                  |
| `Dependency`                          | `Span` `span.kind = client`                  |
| `Request` ve `Dependency` `Id`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `ChildOf` türü `Reference` (üst Aralık)   |

Daha fazla bilgi için bkz. [telemetri veri modeli Application Insights](../../azure-monitor/app/data-model.md). 

OpenTracing kavramlarının tanımları için bkz. OpenTracing [belirtimi](https://github.com/opentracing/specification/blob/master/specification.md) ve [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>OpenCensus Python 'da telemetri bağıntısı

OpenCensus Python, yukarıda özetlenen `OpenTracing` veri modeli belirtimlerini izler. Ayrıca, herhangi bir yapılandırmaya ihtiyaç duymadan [W3C Trace-Context](https://w3c.github.io/trace-context/) ' i destekler.

### <a name="incoming-request-correlation"></a>Gelen istek bağıntısı

OpenCensus Python, W3C Izleme bağlamı üst bilgilerini gelen isteklerden, isteklerden oluşturulan yayılmaya ilişkilendirir. OpenCensus bunu, aşağıdaki popüler web uygulaması çerçeveleri için tümleştirmelerle otomatik olarak yapılır: `flask`, `django` ve `pyramid`. W3C Izleme bağlamı üst bilgilerinin [doğru biçimde](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) doldurulması ve istekle birlikte gönderilmesi gerekir. Aşağıda bunu gösteren bir örnek `flask` verilmiştir.

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Bu, yerel makinenizde bir örnek `flask` uygulaması çalıştırır ve bağlantı noktası `8080`dinler. İzleme bağlamını ilişkilendirmek için uç noktaya bir istek göndereceğiz. Bu örnekte, bir `curl` komutu kullanabiliriz.
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
[Izleme bağlamı üst bilgisi biçimine](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)bakarak şu bilgileri türetik: `version`: `00`
`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`
`parent-id/span-id`: `00f067aa0ba902b7`
`trace-flags`: `01`

Azure Izleyici 'ye gönderilen istek girişine göz atalım, izleme üst bilgisi bilgileriyle doldurulmuş alanları görebiliriz. Bu verileri Azure Izleyici Application Insights kaynak bölümünde Günlükler (Analiz) altında bulabilirsiniz.

![Kırmızı kutuda vurgulanan izleme üst bilgi alanları ile günlüklerde (Analiz) istek telemetriinin ekran görüntüsü](./media/opencensus-python/0011-correlation.png)

`id` alanı, `trace-id` istekte geçirilen izleme başlığından alındığı ve `span-id` Bu yayılma için oluşturulmuş bir 8 baytlık dizi olduğu biçimde `<trace-id>.<span-id>`biçimindedir. 

`operation_ParentId` alanı, istekte iletilen izleme başlığından hem `trace-id` hem de `parent-id` alındığı biçimde `<trace-id>.<parent-id>`biçimindedir.

### <a name="logs-correlation"></a>Günlük bağıntısı

OpenCensus Python, günlük kayıtlarını izleme KIMLIĞI, yayılma KIMLIĞI ve örnekleme bayrağıyla zenginleştirerek günlüklerin bağıntı almasına izin verir. Bu işlem, OpenCensus [günlüğe kaydetme tümleştirmesi](https://pypi.org/project/opencensus-ext-logging/)yüklenerek yapılır. Aşağıdaki öznitelikler Python `LogRecord`s: `traceId`, `spanId` ve `traceSampled`eklenecektir. Bunun yalnızca tümleştirmeden sonra oluşturulan Günlükçüler için geçerli olduğunu unutmayın.
Bunu gösteren örnek bir uygulama aşağıda verilmiştir.

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Bu kod çalıştırıldığında, konsolunda aşağıdakileri sunuyoruz:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
`hello`adlı yayılmasına ait aynı Spanıd olan, yayılma dahilinde olan günlük iletisi için bir Spanıd 'nin nasıl bulunduğunu gözlemleyin.

`AzureLogHandler`kullanarak günlük verilerini dışa aktarabilirsiniz. [Burada](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs) daha fazla bilgi bulabilirsiniz

## <a name="telemetry-correlation-in-net"></a>.NET 'te telemetri bağıntısı

Zaman içinde, .NET telemetri ve tanılama günlüklerini ilişkilendirmek için çeşitli yollar tanımladı:

- `System.Diagnostics.CorrelationManager` [LogicalOperationStack ve ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)'nin izlenmesine izin verir. 
- `System.Diagnostics.Tracing.EventSource` ve Windows için olay Izleme (ETW) [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) metodunu tanımlar.
- `ILogger`, [günlük kapsamlarını](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)kullanır. 
- Windows Communication Foundation (WCF) ve HTTP dağıtımı "geçerli" bağlam yayma.

Ancak, bu yöntemler otomatik dağıtılmış izleme desteğini etkinleştirmiyordu. `DiagnosticSource`, otomatik makine çapraz bağıntısını desteklemeye yönelik bir yoldur. .NET kitaplıkları ' DiagnosticSource ' öğesini destekler ve HTTP gibi bir aktarım aracılığıyla bağıntı bağlamının otomatik makine çapraz yayılmasını sağlar.

`DiagnosticSource` [etkinliklere yönelik kılavuz](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) , etkinliklerin izlenmesi hakkında temel bilgileri açıklar.

ASP.NET Core 2,0, HTTP üstbilgilerinin ayıklanmasını ve yeni bir etkinliğin başlatılmasını destekler.

sürüm 4.1.0 ile başlayan `System.Net.Http.HttpClient`, bağıntı HTTP üst bilgilerinin otomatik olarak eklenmesine ve HTTP çağrısının etkinlik olarak izlenmesini destekler.

Klasik ASP.NET için [Microsoft. Aspnet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)yenı bir http modülü vardır. Bu modül `DiagnosticSource`kullanarak telemetri bağıntısını uygular. Gelen istek üst bilgilerine göre bir etkinlik başlatır. Ayrıca, her bir Internet Information Services (IIS) işleminin farklı bir yönetilen iş parçacığında çalıştığı durumlar da dahil olmak üzere istek işlemenin farklı aşamalarından Telemetriyi de ilişkilendirir.

2\.4.0-Beta1 sürümünden başlayarak Application Insights SDK, telemetri toplamak ve geçerli etkinlikle ilişkilendirmek için `DiagnosticSource` ve `Activity` kullanır.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Java SDK 'sında telemetri bağıntısı

[Java için APPLICATION INSIGHTS SDK](../../azure-monitor/app/java-get-started.md) , sürüm 2.0.0 ile başlayan Telemetriyi otomatik olarak destekler. Bir isteğin kapsamında verilen tüm telemetri (izlemeler, özel durumlar ve özel olaylar gibi) için `operation_id` otomatik olarak doldurur. [Java SDK Aracısı](../../azure-monitor/app/java-agent.md) YAPıLANDıRıLDıYSA, http aracılığıyla hizmet-hizmet çağrıları için bağıntı üst bilgilerini (daha önce açıklanan) yayma işlemini de gerçekleştirir.

> [!NOTE]
> Bağıntı özelliği için yalnızca Apache HTTPClient aracılığıyla yapılan çağrılar desteklenir. Yay RestTemplate veya Feign kullanıyorsanız, her ikisi de aynı şekilde Apache HTTPClient ile kullanılabilir.

Şu anda, mesajlaşma teknolojileri genelinde otomatik bağlam yayma (Kafka, Kbbitmq veya Azure Service Bus) desteklenmez. Ancak, `trackDependency` ve `trackRequest` API 'Lerini kullanarak bu senaryolara el ile kod eklemek mümkündür. Bu API 'lerde bir bağımlılık telemetrisi, bir üretici tarafından kuyruğa alınan bir iletiyi temsil eder ve istek bir tüketici tarafından işlenen bir iletiyi temsil eder. Bu durumda, hem `operation_id` hem de `operation_parentId` iletinin özelliklerinde yayılmalıdır.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Zaman uyumsuz Java uygulamasında telemetri bağıntısı

Zaman uyumsuz Spring Boot uygulamasındaki Telemetriyi ilişkilendirmek için lütfen [Bu](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) ayrıntılı makaleyi izleyin. Bu, bahar 'in [Threadpooltaskyürütücü](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) 'in yanı sıra [Threadpooltaskscheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)' a yönelik rehberlik sağlar. 


<a name="java-role-name"></a>
## <a name="role-name"></a>Rol adı

Her zaman, bileşen adlarının [uygulama eşlemesinde](../../azure-monitor/app/app-map.md)görüntülenme şeklini özelleştirmek isteyebilirsiniz. Bunu yapmak için aşağıdakilerden birini yaparak `cloud_RoleName` el ile ayarlayabilirsiniz:

- Application Insights Java SDK 2.5.0 ' den başlayarak, bulut rolü adını `ApplicationInsights.xml` dosyanıza `<RoleName>` ekleyerek belirtebilirsiniz, örn.

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Spring Boot uygulamasını Application Insights Spring Boot Starter ile kullanırsanız, tek yapmanız gereken tek değişiklik, uygulama. Özellikler dosyasında uygulama için özel adınızı ayarlamanıza yöneliktir.

  `spring.application.name=<name-of-app>`

  Spring Boot Starter, `spring.application.name` özelliği için girdiğiniz değere `cloudRoleName` otomatik olarak atar.

## <a name="next-steps"></a>Sonraki adımlar

- [Özel telemetri](../../azure-monitor/app/api-custom-events-metrics.md)yazın.
- ASP.NET Core ve ASP.NET ' deki gelişmiş bağıntı senaryoları için [özel işlemleri izleme](custom-operations-tracking.md) makalesine başvurun.
- Diğer SDK 'lar için [cloud_RoleName ayarlama](../../azure-monitor/app/app-map.md#set-cloud-role-name) hakkında daha fazla bilgi edinin.
- Mikro hizmetinizin tüm bileşenlerini Application Insights ekleyin. [Desteklenen platformları](../../azure-monitor/app/platforms.md)inceleyin.
- Application Insights türleri için [veri modeline](../../azure-monitor/app/data-model.md) bakın.
- [Telemetrinin nasıl genişletileceğini ve filtreleneceğini](../../azure-monitor/app/api-filtering-sampling.md)öğrenin.
- [Application Insights config başvurusunu](configuration-with-applicationinsights-config.md)gözden geçirin.

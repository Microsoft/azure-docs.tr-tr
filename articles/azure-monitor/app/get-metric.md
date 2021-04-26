---
title: Azure Izleyici 'de Get-Metric Application Insights
description: Azure Izleyici ile .NET ve .NET Core uygulamaları için önceden toplanmış yerel ölçümleri yakalamak üzere GetMetric () çağrısını etkin bir şekilde nasıl kullanacağınızı öğrenin Application Insights
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 22baa1ae9554601a72ffdb848b87d99281067967
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384298"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>.NET ve .NET Core 'da özel ölçüm koleksiyonu

.NET ve .NET Core SDK 'larının Azure Izleyici Application Insights, ve özel ölçümleri toplamaya yönelik iki farklı yöntem vardır `TrackMetric()` `GetMetric()` . Bu iki yöntem arasındaki önemli fark yerel toplamadır. `TrackMetric()` ön toplamadır, ön toplama `GetMetric()` işlemi yoktur. Bu nedenle önerilen yaklaşım, toplamayı kullanmak için `TrackMetric()` artık özel ölçümleri toplamanın tercih edilen yöntemi değildir. Bu makale, GetMetric () yöntemini ve bu yöntemin nasıl çalıştığına ilişkin bir kısmını kullanarak size kılavuzluk eder.

## <a name="pre-aggregating-vs-non-pre-aggregating-api"></a>Ön toplama ve toplanmayan API karşılaştırması

`TrackMetric()` bir ölçümü belirten ham telemetri gönderir. Her bir değer için tek bir telemetri öğesi gönderilmesi verimsiz bir öğedir. `TrackMetric()` , `TrackMetric(item)` telemetri başlatıcılarının ve işlemcilerin tam SDK ardışık düzeninde gezindiğinden performans açısından da verimsiz olur. Farklı olarak `TrackMetric()` , `GetMetric()` yerel ön toplamasını sizin için işler ve sonra yalnızca bir dakikalık sabit bir aralıkta toplanmış bir özet ölçümü gönderir. Bu nedenle, bazı özel metrikleri ikinci veya hatta milisaniyelik düzeyde yakından izlemeniz gerekiyorsa, bunu yalnızca her dakika izlemenin yalnızca depolama ve ağ trafiği maliyetlerini alırken yapabilirsiniz. Bu Ayrıca, toplanan bir ölçüm için gönderilmesi gereken toplam telemetri öğesi sayısının büyük ölçüde azaltılmasından dolayı, kısıtlama riskini önemli ölçüde azaltır.

Application Insights, ve ile toplanan özel ölçümler `TrackMetric()` , `GetMetric()` [örneklemeye](./sampling.md)tabi değildir. Önemli ölçümleri örnekleme, bu ölçümler etrafında derleyebileceğiniz uyarı, güvenilmez hale gelebileceği senaryolara yol açabilir. Özel ölçümlerinizi hiçbir zaman örnekleyerek, genellikle uyarı eşikleriniz ihlal edildiğinde bir uyarının tetikleneceği şekilde emin olabilirsiniz.  Ancak, özel ölçümler örneklenolmadığından, bazı olası sorunlar vardır.

Her saniye bir ölçümde eğilimleri izlemeniz gerekiyorsa veya daha da ayrıntılı bir aralıkta bu, şunlar olabilir:

- Daha fazla veri depolama maliyeti. Azure Izleyici 'ye ne kadar veri göndereceğiniz ile ilişkili bir maliyet vardır. (Daha fazla veri, izlemenin genel maliyetini daha fazla gönderir.)
- Ağ trafiği/performans yükü artar. (Bazı senaryolarda bu, hem parasal hem de uygulama performans maliyeti olabilir.)
- Alım azaltma riski. (Uygulamanız kısa bir zaman aralığında çok yüksek bir telemetri gönderdiğinde Azure Izleyici hizmeti ("kısıtlar") veri noktalarını bırakır.)

Daraltma, örnekleme gibi belirli bir konudur, bir uyarının tetiklenmesi koşulu yerel olarak gerçekleşeceğinden ve daha sonra gönderilen çok fazla veri olduğu için alma uç noktasında bırakıldıklarından, azaltma kaçırılmış uyarılara yol açabilir. Bu, `TrackMetric()` kendi yerel toplama mantığınızı gerçekleştirmediğiniz takdirde .net ve .NET Core 'un kullanılmasını önermiyoruz. Belirli bir süre boyunca bir olay meydana gelen her örneği izlemeye çalışıyorsanız, [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) daha iyi bir uyum olduğunu fark edebilirsiniz. Özel ölçümlerin aksine, özel olayların örneklemeye tabi olduğunu aklınızda bulundurun. `TrackMetric()`Kendi yerel ön toplamalarınızı yazmadan bile kullanabilirsiniz, ancak bunu yaparsanız, bunların farkında olun.

Özet içinde, `GetMetric()` ön toplama işlemi yaptığı için önerilen yaklaşım, tüm iz () çağrılarındaki değerleri toplar ve her dakikada bir Özet/toplama gönderir. Bu, daha az veri noktası göndererek maliyet ve performans yükünü önemli ölçüde azaltabilir, ancak yine de tüm ilgili bilgileri toplarken.

> [!NOTE]
> Yalnızca .NET ve .NET Core SDK 'larının bir GetMetric () yöntemi vardır. Java kullanıyorsanız, [mikro ölçüm ölçümlerini](./micrometer-java.md) veya kullanabilirsiniz `TrackMetric()` . JavaScript ve Node.js için kullanmaya devam edersiniz `TrackMetric()` , ancak önceki bölümde özetlenen uyarıları aklınızda bulundurun. Python için, [Opencensus. stats](./opencensus-python.md#metrics) ' i kullanarak özel ölçümler gönderebilirsiniz ancak ölçüm uygulamasının farklı olması gerekir.

## <a name="getting-started-with-getmetric"></a>GetMetric ile çalışmaya başlama

Örneklerimizde, temel bir .NET Core 3,1 çalışan hizmeti uygulaması kullanacağız. Bu örneklerle kullanılan test ortamını tam olarak çoğaltmak isterseniz, temel bir çalışan hizmeti proje şablonuna Application Insights eklemek için [izleme çalışan hizmeti makalesinin](./worker-service.md#net-core-30-worker-service-application) 1-6 adımlarını izleyin. Bu kavramlar, SDK 'nın Web Apps ve konsol uygulamaları dahil kullanılabileceği tüm genel uygulamalar için geçerlidir.

### <a name="sending-metrics"></a>Ölçümleri gönderme

`worker.cs`Dosyanızın içeriğini aşağıdakiler ile değiştirin:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Yukarıdaki kodu çalıştırır ve Visual Studio çıkış penceresi veya Telerik 'ın Fiddler gibi bir araçla gönderilen Telemetriyi izlerken, while döngüsünü bir Telemetriyi sürekli olarak yürütmeyi, sonra da test edimizin aşağıdaki gibi göründüğünden, 60 saniyelik işaret üzerinden tek bir telemetri öğesi gönderileceğini görürsünüz. :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Bu tek telemetri öğesi, 41 farklı ölçüm ölçümlerinin toplamını temsil eder. Aynı değeri tekrar tekrar gönderdiğimiz için, aynı *maksimum (max)* ve *Minimum (min)* değerler içeren *Standart bir sapma (stDev)* 0 ' dır. *Value* özelliği, toplanan tüm değerlerin toplamını temsil eder.

> [!NOTE]
> GetMetric, son değerin (yani "ölçer") veya izleme histogramları/dağıtımlarını izlemeyi desteklemez.

Günlükler (Analiz) deneyiminde Application Insights kaynağını inceliyoruz, bu tek bir telemetri öğesi şöyle görünür:

![Log Analytics sorgu görünümü](./media/get-metric/log-analytics.png)

> [!NOTE]
> Ham telemetri öğesi bir açık Sum özelliği/alanı içermediği sırada sizin için bir tane oluşturuyoruz. Bu durumda, `value` ve `valueSum` özelliği aynı şeyi temsil eder.

Ayrıca, portalın [_ölçümler_](../essentials/metrics-charts.md) bölümünde özel ölçüm telemetrinize de erişebilirsiniz. Hem [günlük tabanlı hem de özel ölçüm](pre-aggregated-metrics-log-metrics.md)olarak. (Aşağıdaki ekran görüntüsü, günlük tabanlı bir örnektir.) ![Ölçüm Gezgini görünümü](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Yüksek aktarım hızı kullanımı için önbelleğe alma ölçümü başvurusu

Bazı durumlarda ölçüm değerleri çok sık gözlemlenmiştir. Örneğin, 500 isteği/saniye işleyen bir yüksek aktarım hızı hizmeti her istek için 20 telemetri ölçümü yaymak isteyebilir. Bu, saniyede 10.000 değer izlemenin ortalamadığını gösterir. Bu tür yüksek işleme senaryolarında, kullanıcıların bazı aramaları önleyerek SDK 'ya yardımcı olması gerekebilir.

Örneğin, bu durumda yukarıdaki örnek "ComputersSold" ölçümü için bir tanıtıcı araması gerçekleştirdi ve gözlemlenen 42 değerini izlediniz. Bunun yerine, tanıtıcı birden çok izleme etkinleştirmeleri için önbelleğe alınmış olabilir:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Ölçüm tanıtıcısını önbelleğe almanın yanı sıra, yukarıdaki örnek `Task.Delay` 772 ' e 50 kadar milisaniyeye kadar azaltılır ve bu sayede, döngünün daha sık devam eden `TrackValue()` etkinleştirmeleri olur.

## <a name="multi-dimensional-metrics"></a>Çok boyutlu ölçümler

Önceki bölümdeki örneklerde Sıfır boyutlu ölçümler gösterilmektedir. Ölçümler de çok boyutlu olabilir. Şu anda en fazla 10 boyut destekliyoruz.

 Tek boyutlu bir ölçüm oluşturma örneği aşağıda verilmiştir:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Bu kodu en az 60 saniyede çalıştırmak, her biri üç form faktöründe oluşan toplamayı temsil eden üç farklı telemetri öğesinin Azure 'a gönderilmesine neden olur. Bu durumda, Günlükler (Analiz) görünümünde bunları inceleyebilirsiniz:

![Çok boyutlu ölçümün Log Analytics görünümü](./media/get-metric/log-analytics-multi-dimensional.png)

Ölçüm Gezgini deneyiminde de:

![Özel ölçümler](./media/get-metric/custom-metrics.png)

Bununla birlikte, ölçüyü yeni özel boyutuz ile bölmeyebilirsiniz veya ölçüm görünümü ile özel boyutlarınızı görüntüleyemezsiniz.

![Desteği bölme](./media/get-metric/splitting-support.png)

Varsayılan olarak, Ölçüm Gezgini deneyimi içindeki çok boyutlu ölçümler Application Insights kaynaklarda açık değildir.

### <a name="enable-multi-dimensional-metrics"></a>Çok boyutlu ölçümleri etkinleştir

Bir Application Insights kaynağı için çok boyutlu ölçümleri etkinleştirmek üzere **kullanım ve tahmini maliyetler**  >  **özel ölçümler**' i seçin  >  **özel ölçüm boyutlarında uyarı ' ı etkinleştirin**  >  . Bu konuda daha fazla ayrıntı için [burada](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)bulunabilir.

Bu değişikliği yaptıktan ve yeni çok boyutlu telemetri gönderdikten sonra, **bölmeyi uygulayacaksınız**.

> [!NOTE]
> Yalnızca yeni gönderilen ölçümler portalda özelliği etkinleştirildikten sonra, boyutları depolanır.

![Bölmeyi Uygula](./media/get-metric/apply-splitting.png)

Ve her bir _FormFactor_ boyutu için ölçüm toplamaların görünümünü görüntüleyin:

![Form faktörleri](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions&quot;></a>Üçten fazla boyut olduğunda Metricıdentifier kullanma

Şu anda 10 boyut desteklenir, ancak üç boyuttan büyük bir kullanım gerektirir `MetricIdentifier` :

```csharp
// Add &quot;using Microsoft.ApplicationInsights.Metrics;&quot; to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier(&quot;[metricNamespace]&quot;,&quot;[metricId],&quot;[dim1]&quot;,&quot;[dim2]&quot;,&quot;[dim3]&quot;,&quot;[dim4]&quot;,&quot;[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>Özel Ölçüm yapılandırması

Ölçüm yapılandırmasını değiştirmek istiyorsanız, bunu ölçümün başlatıldığı yerde yapmanız gerekir.

### <a name="special-dimension-names"></a>Özel boyut adları

Ölçümler, `TelemetryClient` bunlara erişmek için kullanılan telemetri bağlamını kullanmaz. sınıfta sabitler olarak kullanılabilen özel boyut adları, `MetricDimensionNames` Bu sınırlamaya yönelik en iyi çözümdür.

Aşağıdaki "özel Işlem Isteği boyutu" tarafından gönderilen ölçüm toplamaları-ölçüm  `Context.Operation.Name` "özel işlem" olarak ayarlanmayacak. Öte yandan `TrackMetric()` veya diğer bir TrackXXX (), `OperationName` "özel işlem" olarak ayarlanır.

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

Bu durumda, `MetricDimensionNames` değerleri belirtmek için sınıfında listelenen özel boyut adlarını kullanın `TelemetryContext` .

Örneğin, bir sonraki deyimden kaynaklanan ölçüm toplamı Application Insights bulut uç noktasına gönderildiğinde, `Context.Operation.Name` veri alanı "özel işlem" olarak ayarlanır:

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

Bu özel boyutun değerleri içine kopyalanacak `TelemetryContext` ve ' normal ' bir boyut olarak kullanılmayacak. Ayrıca normal ölçüm araştırması için bir işlem boyutu tutmak istiyorsanız, bu amaçla ayrı bir boyut oluşturmanız gerekir:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Boyut ve zaman serisi dönüşü

 Telemetri alt sisteminin yanlışlıkla kaynaklarınızı kullanmasını engellemek için, ölçüm başına maksimum veri serisi sayısını kontrol edebilirsiniz. Varsayılan sınırlar, her ölçüm için 1000 ' den fazla toplam veri serisi ve boyut başına 100 ' den fazla farklı değer değildir.

 Boyut ve zaman dizisi bağlamında, `Metric.TrackValue(..)` limitlerin gözlemlendiği emin olmak için kullandık. Sınırlara zaten ulaşılmışsa, `Metric.TrackValue(..)` "false" döndürür ve değer izlenmeyecektir. Aksi takdirde, "true" döndürür. Bu, bir ölçüm verileri Kullanıcı girişinden kaynaklanıyorsa yararlıdır.

`MetricConfiguration`Oluşturucu, ilgili ölçüm içinde farklı serinin nasıl yönetileceği hakkında bazı seçenekler ve `IMetricSeriesConfiguration` her bir ölçüm serisi için toplama davranışını belirten bir sınıf nesnesi kullanır:

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` bir ölçümün içerebileceği maksimum veri süresi serisi sayısıdır. Bu sınıra ulaşıldığında, bu, `TrackValue()` normal olarak yeni bir serinin sonucunu döndürür.
* `valuesPerDimensionLimit` Boyut başına farklı değer sayısını benzer şekilde sınırlandırır.
* `restrictToUInt32Values` Yalnızca negatif olmayan tamsayı değerlerinin izlenmesi gerekip gerekmediğini belirler.

Cap limitlerinin aşıldığını öğrenmek için bir ileti gönderme örneği aşağıda verilmiştir:

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>Sonraki adımlar

* Çalışan hizmeti uygulamalarını izleme hakkında [daha fazla bilgi edinin ](./worker-service.md).
* [Günlük tabanlı ve önceden toplanmış ölçümler](./pre-aggregated-metrics-log-metrics.md)hakkında daha fazla bilgi için.
* [Ölçüm Gezgini](../essentials/metrics-getting-started.md)
* [ASP.NET Core uygulamalar](asp-net-core.md) için Application Insights etkinleştirme
* [ASP.NET uygulamaları](asp-net.md) için Application Insights etkinleştirme

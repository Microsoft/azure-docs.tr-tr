---
title: Azure Izleyici ile Python uygulamalarını izleme | Microsoft Docs
description: Azure Izleyici ile OpenCensus Python 'ı bağlamak için yönergeler sağlar
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
author: lzchen
ms.author: lechen
ms.openlocfilehash: 548cfd9d593e9adaeaaf984f756e58d242ca9f45
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576559"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Python uygulamanız için Azure Izleyicisini ayarlama

Azure Izleyici, [Opencensus](https://opencensus.io)ile tümleştirme yoluyla, Python uygulamalarının dağıtılmış izlemeyi, ölçüm toplamayı ve günlüğe kaydedilmesini destekler. Bu makalede, Python için OpenCensus ayarlama ve izleme verilerinizi Azure Izleyici 'ye gönderme işlemi adım adım gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- Python yüklemesi. Bu makalede [Python 3.7.0](https://www.python.org/downloads/release/python-370/)kullanılmaktadır, ancak diğer sürümler büyük olasılıkla küçük değişikliklerle çalışabilse de. SDK yalnızca Python v 2.7 ve v 3.4-v 3.7 destekler.
- Application Insights [kaynağı](./create-new-resource.md)oluşturun. Kaynağınız için kendi izleme anahtarınızı (Ikey) atadınız.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Azure Izleyici için OpenCensus Python SDK 'Sı ile işaretleme

OpenCensus Azure Izleyici exporler 'i yükler:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> `python -m pip install opencensus-ext-azure`Komutu, `PATH` Python yüklemeniz için ayarlanmış bir ortam değişkeni olduğunu varsayar. Bu değişkeni yapılandırmadıysanız, Python yürütülebilirinizin bulunduğu konuma tam dizin yolu sağlamanız gerekir. Sonuç şöyle bir komuttur: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

SDK, Azure Izleyici 'ye farklı telemetri türleri göndermek için üç Azure Izleyici exporler kullanır. Bunlar izliyoruz, ölçümler ve günlükleridir. Bu telemetri türleri hakkında daha fazla bilgi için bkz. [veri platformuna genel bakış](../data-platform.md). Bu telemetri türlerini üç dışarı aktarmak yoluyla göndermek için aşağıdaki yönergeleri kullanın.

## <a name="telemetry-type-mappings"></a>Telemetri türü eşlemeleri

Bu, OpenCensus 'ın Azure Izleyici 'de gördüğünüz telemetri türlerine eşlenme sağladığı dışarı vericiler aşağıda verilmiştir.

| Observability | Azure Izleyici 'de telemetri türü    | Açıklama                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Günlükler                    | İzlemeler, özel durumlar, customEvents   | Günlük telemetrisi, özel durum telemetrisi, olay telemetrisi |
| Ölçümler                 | Customölçümler, performanceCounters | Özel Ölçüm performans sayaçları                |
| İzleme                 | İstek bağımlılıkları             | Gelen istekler, giden istekler                |

### <a name="logs"></a>Günlükler

1. İlk olarak, bazı yerel günlük verileri oluşturalım.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Kod sürekli olarak bir değer girilmesini ister. Her girilen değer için bir günlük girişi yayınlanır.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. Değer girilmesi, tanıtım amacıyla yararlı olsa da, sonuçta günlük verilerini Azure Izleyici 'ye yaymak istiyoruz. Bağlantı dizenizi doğrudan dışarı aktarılmasına geçirin. Ya da, bunu bir ortam değişkeninde belirtebilirsiniz `APPLICATIONINSIGHTS_CONNECTION_STRING` . Aşağıdaki kod örneğine göre kodunuzu önceki adımdan değiştirin:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Dışarı Aktarıcı günlük verilerini Azure Izleyici 'ye gönderir. Verileri altında bulabilirsiniz `traces` . 

    > [!NOTE]
    > Bu bağlamda, `traces` ile aynı değildir `tracing` . Burada, kullandığınızda `traces` Azure izleyici 'de göreceğiniz telemetri türünü ifade eder `AzureLogHandler` . Ancak, `tracing` OpenCensus içindeki bir kavram anlamına gelir ve [Dağıtılmış izleme](./distributed-tracing.md)ile ilgilidir.

    > [!NOTE]
    > Kök günlükçüsü, uyarı düzeyiyle yapılandırılır. Diğer bir deyişle, daha az önem derecesine sahip olan ve sırasıyla Azure Izleyici 'ye gönderilmemiş olan tüm Günlükler göz ardı edilir. Daha fazla bilgi için bkz. [Belgeler](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel).

1. Ayrıca, custom_dimensions alanını kullanarak *fazladan* anahtar sözcük bağımsız değişkeninde günlük iletilerinize özel özellikler ekleyebilirsiniz. Bu özellikler Azure Izleyici 'de anahtar-değer çiftleri olarak görünür `customDimensions` .
    > [!NOTE]
    > Bu özelliğin çalışması için custom_dimensions alana bir sözlük geçirmeniz gerekir. Diğer herhangi bir türün bağımsız değişkenlerini geçirirseniz, günlükçü onları yoksayar.

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>Docgo uygulamaları için günlüğü yapılandırma

Günlük kaydını, Docgo uygulamalarınız için yukarıdaki uygulama kodunuzda açıkça yapılandırabilir veya Docgo 'nun günlük yapılandırmasında belirtebilirsiniz. Bu kod, Docgo ayarları yapılandırması için kullandığınız herhangi bir dosyaya gidebilir. Docgo ayarlarını yapılandırma hakkında bilgi için bkz. [Docgo ayarları](https://docs.djangoproject.com/en/3.0/topics/settings/). Günlüğe kaydetmeyi yapılandırma hakkında daha fazla bilgi için bkz. [Docgo günlüğü](https://docs.djangoproject.com/en/3.0/topics/logging/).

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Aynı ada sahip günlükçü 'yi yapılandırmanızda belirtilen adla kullandığınızdan emin olun.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Özel durum gönder

OpenCensus Python, Telemetriyi otomatik olarak izlemez ve göndermez `exception` . Bunlar `AzureLogHandler` , Python günlüğü kitaplığı aracılığıyla özel durumlar kullanılarak gönderilir. Normal günlüğe kaydetme ile tıpkı özel özellikler ekleyebilirsiniz.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
Özel durumları açıkça günlüğe kaydetmek zorunda olduğunuzdan, bu, Kullanıcı tarafından işlenmeyen özel durumları nasıl günlüğe kaydetmek istediğinize kadar olur. OpenCensus, bir özel durum telemetrisini açıkça günlüğe kaydeden sürece kullanıcının bunu nasıl yapmak istediğini gösteren kısıtlamalar yerleştirmez.

#### <a name="send-events"></a>Olayları gönderme

`customEvent`Telemetrisini, `trace` bunun yerine kullanarak yalnızca Telemetriyi göndereceğiniz şekilde gönderebilirsiniz `AzureEventHandler` .

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Örnekleme

OpenCensus 'de örnekleme hakkında bilgi edinmek için, [opencensus 'de örneklemeye](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)göz atın.

#### <a name="log-correlation"></a>Günlük bağıntısı

Günlüklerinizi izleme bağlamı verileriyle zenginleştirme hakkında daha fazla bilgi için bkz. OpenCensus Python [günlükleri tümleştirmesi](./correlation.md#log-correlation).

#### <a name="modify-telemetry"></a>Telemetriyi değiştirme

İzlenen Telemetriyi Azure Izleyici 'ye gönderilmeden önce değiştirme hakkında daha fazla bilgi için bkz. OpenCensus Python [telemetri işlemcileri](./api-filtering-sampling.md#opencensus-python-telemetry-processors).


### <a name="metrics"></a>Ölçümler

OpenCensus. stats 4 toplama yöntemini destekler, ancak Azure Izleyici için kısmi destek sağlar:

- **Sayı:** Ölçüm noktası sayısı. Değer birikimlidir, yeniden başlatma sırasında yalnızca 0 ' a artırabilir ve sıfırlanır. 
- **Toplam:** Ölçüm noktalarının toplamı. Değer birikimlidir, yeniden başlatma sırasında yalnızca 0 ' a artırabilir ve sıfırlanır. 
- **LastValue:** Son kaydedilen değeri korur, diğer her şeyi bırakır.
- **Dağıtım:** Ölçüm noktalarının histogram dağılımı. Bu yöntem **, Azure Dışarı Aktarıcı tarafından desteklenmez**.

### <a name="count-aggregation-example"></a>Count toplama örneği

1. İlk olarak, bazı yerel ölçüm verileri oluşturalım. Kullanıcının **ENTER** tuşunu kaç kez seçtireceğini izlemek için basit bir ölçüm oluşturacağız.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. Kodu tekrar tekrar çalıştırmak, **ENTER**' u seçmenizi ister. **ENTER** 'un kaç kez seçili olduğunu izlemek için bir ölçüm oluşturulur. Her giriş ile, değer artırılır ve ölçüm bilgileri konsolda görüntülenir. Bu bilgiler, ölçüm güncellendiği zaman geçerli değeri ve geçerli zaman damgasını içerir.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. Değer girilmesi, tanıtım amacıyla yararlı olsa da, sonunda ölçüm verilerini Azure Izleyici 'ye yayma istiyoruz. Bağlantı dizenizi doğrudan dışarı aktarılmasına geçirin. Ya da, bunu bir ortam değişkeninde belirtebilirsiniz `APPLICATIONINSIGHTS_CONNECTION_STRING` . Aşağıdaki kod örneğine göre kodunuzu önceki adımdan değiştirin:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. Dışarı Aktarıcı, ölçüm verilerini Azure Izleyici 'ye sabit bir aralıkla gönderir. Varsayılan değer 15 saniyedir. Tek bir ölçümü izliyoruz, bu nedenle içerdiği değer ve zaman damgasıyla birlikte bu ölçüm verileri her aralığa gönderilir. Değer birikimlidir, yeniden başlatma sırasında yalnızca 0 ' a artırabilir ve sıfırlanır. Verilerin altında bulabilirsiniz `customMetrics` , ancak `customMetrics` ValueCount, Valuesum, ValueMin, ValueMax ve valueStdDev özellikleri etkin bir şekilde kullanılmaz.

### <a name="setting-custom-dimensions-in-metrics"></a>Ölçümlerde özel boyutlar ayarlama

Opencensus Python SDK 'Sı `tags` , genel olarak anahtar/değer çiftleri sözlüğü olan ölçüm telemetrinize özel boyutlar eklemeye olanak tanır. 

1. Etiket eşlemesinde kullanmak istediğiniz etiketleri ekleyin. Etiket eşlemesi, kullanabileceğiniz tüm kullanılabilir etiketlerin "havuz" sıralaması gibi davranır.

```python
...
tmap = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
...
```

1. Belirli bir için `View` , etiket anahtarı aracılığıyla bu görünümle ölçümleri kaydederken kullanmak istediğiniz etiketleri belirtin.

```python
...
prompt_view = view_module.View("prompt view",
                               "number of prompts",
                               ["url"], # <-- A sequence of tag keys used to specify which tag key/value to use from the tag map
                               prompt_measure,
                               aggregation_module.CountAggregation())
...
```

1. Ölçüm eşlemesinde kayıt sırasında etiket haritasını kullandığınızdan emin olun. İçinde belirtilen etiket anahtarlarının, `View` kaydetmek için kullanılan etiket eşlemesinde bulunması gerekir.

```python
...
mmap = stats_recorder.new_measurement_map()
mmap.measure_int_put(prompt_measure, 1)
mmap.record(tmap) # <-- pass the tag map in here
...
```

1. Tablosu altında, `customMetrics` kullanılarak oluşturulan tüm ölçüm kayıtları `prompt_view` özel boyutlara sahip olur `{"url":"http://example.com"}` .

1. Aynı anahtarları kullanarak farklı değerlere sahip etiketler üretmek için, bunlar için yeni etiket haritaları oluşturun.

```python
...
tmap = tag_map_module.TagMap()
tmap2 = tag_map_module.TagMap()
tmap.insert("url", "http://example.com")
tmap2.insert("url", "https://www.wikipedia.org/wiki/")
...
```

#### <a name="performance-counters"></a>Performans sayaçları

Varsayılan olarak, ölçüm Dışarı Aktarıcı Azure Izleyici 'ye bir dizi performans sayacı gönderir. Bunu, `enable_standard_metrics` `False` ölçüm verme programı oluşturucusunda bayrağını olarak ayarlayarak devre dışı bırakabilirsiniz.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

Şu anda bu performans sayaçları gönderiliyor:

- Kullanılabilir bellek (bayt)
- CPU Işlemci zamanı (yüzde)
- Gelen Istek oranı (saniye başına)
- Gelen Istek ortalama yürütme süresi (milisaniye)
- İşlem CPU kullanımı (yüzde)
- İşlem özel baytları (bayt)

Bu ölçümleri ' de görebilmeniz gerekir `performanceCounters` . Daha fazla bilgi için bkz. [performans sayaçları](./performance-counters.md).

#### <a name="modify-telemetry"></a>Telemetriyi değiştirme

İzlenen telemetrinin Azure Izleyici 'ye gönderilmeden önce nasıl değiştirileceği hakkında bilgi için bkz. OpenCensus Python [telemetri işlemcileri](./api-filtering-sampling.md#opencensus-python-telemetry-processors).

### <a name="tracing"></a>İzleme

> [!NOTE]
> OpenCensus ' de, `tracing` [Dağıtılmış izlemeyi](./distributed-tracing.md)ifade eder. `AzureExporter` `requests` `dependency` Azure izleyici 'ye gönderir ve telemetri sağlar.

1. İlk olarak, bazı izleme verilerini yerel olarak oluşturalım. Python boştayken veya istediğiniz Düzenleyicinizde aşağıdaki kodu girin:

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Kodu sürekli olarak çalıştırmak sizden bir değer girmenizi ister. Her giriş ile, değer kabuğa yazdırılır. OpenCensus Python modülü ilgili bir parçasını oluşturur `SpanData` . OpenCensus projesi bir izlemeyi bir [yayılma ağacı olarak](https://opencensus.io/core-concepts/tracing/)tanımlar.
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. Değer girilmesi, sunum amaçları için yararlı olsa da, sonunda `SpanData` Azure izleyici 'ye yayma istiyoruz. Bağlantı dizenizi doğrudan dışarı aktarılmasına geçirin. Ya da, bunu bir ortam değişkeninde belirtebilirsiniz `APPLICATIONINSIGHTS_CONNECTION_STRING` . Aşağıdaki kod örneğine göre kodunuzu önceki adımdan değiştirin:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. Artık Python betiğini çalıştırdığınızda, yine de değer girmeniz istenir, ancak yalnızca değeri kabukta yazdırılıyor. Oluşturulan `SpanData` Azure izleyici 'ye gönderilir. Yayınlanan yayılmış verileri ' de bulabilirsiniz `dependencies` . Giden istekler hakkında daha fazla bilgi için bkz. OpenCensus Python [bağımlılıkları](./opencensus-python-dependency.md).
Gelen istekler hakkında daha fazla bilgi için bkz. OpenCensus Python [istekleri](./opencensus-python-request.md).

#### <a name="sampling"></a>Örnekleme

OpenCensus 'de örnekleme hakkında bilgi edinmek için, [opencensus 'de örneklemeye](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)göz atın.

#### <a name="trace-correlation"></a>İzleme bağıntısı

İzleme verilerinizde telemetri bağıntısı hakkında daha fazla bilgi için, OpenCensus Python [telemetri bağıntı](./correlation.md#telemetry-correlation-in-opencensus-python)' na göz atın.

#### <a name="modify-telemetry"></a>Telemetriyi değiştirme

İzlenen telemetrinin Azure Izleyici 'ye gönderilmeden önce nasıl değiştirileceği hakkında daha fazla bilgi için bkz. OpenCensus Python [telemetri işlemcileri](./api-filtering-sampling.md#opencensus-python-telemetry-processors).

## <a name="configure-azure-monitor-exporters"></a>Azure Izleyici exporicileri yapılandırma

Gösterildiği gibi, OpenCensus 'i destekleyen üç farklı Azure Izleyici dışarı aktarabilirliğini de vardır. Her biri Azure Izleyici 'ye farklı telemetri türleri gönderir. Her bir dışarı aktarma tarafından gönderilen telemetri türlerini görmek için aşağıdaki listeye bakın.

Her bir dışarı aktarma, oluşturuculardan geçen yapılandırma için aynı bağımsız değişkenleri kabul eder. Her biri hakkındaki ayrıntıları burada görebilirsiniz:

- `connection_string`: Azure Izleyici kaynağına bağlanmak için kullanılan bağlantı dizesi. Üzerinden önceliklidir `instrumentation_key` .
- `enable_standard_metrics`: İçin kullanılır `AzureMetricsExporter` . Dışarı Aktarıcı 'nın [performans sayacı](../essentials/app-insights-metrics.md#performance-counters) ölçümlerini Azure izleyici 'ye otomatik olarak göndermesini bildirir. Varsayılan olarak olur `True` .
- `export_interval`: Verme işleminin saniye cinsinden sıklığını belirtmek için kullanılır.
- `instrumentation_key`: Azure Izleyici kaynağına bağlanmak için kullanılan izleme anahtarı.
- `logging_sampling_rate`: İçin kullanılır `AzureLogHandler` . Günlükleri dışarı aktarmak için bir örnekleme hızı [0, 1.0] sağlar. Varsayılan olarak 1,0 ' dir.
- `max_batch_size`: Bir kerede verilen telemetri en büyük boyutunu belirtir.
- `proxies`: Azure Izleyici 'ye veri göndermek için kullanılacak bir proxy dizisi belirtir. Daha fazla bilgi için bkz. [proxy 'ler](https://requests.readthedocs.io/en/master/user/advanced/#proxies).
- `storage_path`: Yerel depolama klasörünün bulunduğu konum (gönderilmemiş telemetri). `opencensus-ext-azure`V 1.0.3 itibariyle varsayılan yol, işletim sistemi geçici dizini + ' dır `opencensus-python`  +  `your-ikey` . V 1.0.3 'den önce varsayılan yol $USER + ' dır `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Sorgular ile verilerinizi görüntüleme

Uygulamalarınızdan gönderilen telemetri verilerini **Günlükler (Analiz)** sekmesinden görüntüleyebilirsiniz.

![Kırmızı kutuda seçilen "Günlükler (Analiz)" ile genel bakış bölmesinin ekran görüntüsü](./media/opencensus-python/0010-logs-query.png)

**Etkin** altındaki listede:

- Azure Izleyici izleme dışarı aktarıcı ile gönderilen telemetri için, gelen istekler altında görünür `requests` . Giden veya işlem içi istekler altında görünür `dependencies` .
- Azure Izleyici ölçümleri dışarı aktarıcı ile gönderilen telemetri için, gönderilen ölçümler altında görüntülenir `customMetrics` .
- Azure Izleyici günlükleri dışarı aktarıcı ile gönderilen telemetri için Günlükler altında görüntülenir `traces` . Özel durumlar altında görünür `exceptions` .

Sorguları ve günlükleri kullanma hakkında daha ayrıntılı bilgi için bkz. [Azure izleyici 'de Günlükler](../logs/data-platform-logs.md).

## <a name="learn-more-about-opencensus-for-python"></a>Python için OpenCensus hakkında daha fazla bilgi edinin

* [GitHub 'da OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [Özelleştirme](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub 'da Azure Izleyici dışarı vericiler](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus tümleştirmeleri](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Izleyici örnek uygulamaları](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Sonraki adımlar

* [Gelen istekleri izleme](./opencensus-python-dependency.md)
* [Devam eden istekleri izleme](./opencensus-python-request.md)
* [Uygulama eşlemesi](./app-map.md)
* [Uçtan uca performans izleme](../app/tutorial-performance.md)

### <a name="alerts"></a>Uyarılar

* [Kullanılabilirlik testleri](./monitor-web-app-availability.md): Sitenizin web’de görünür olduğundan emin olmaya yönelik testler oluşturun.
* [Akıllı tanılama](./proactive-diagnostics.md): Bu testler otomatik olarak çalıştığından, bunları ayarlamak için herhangi bir şey yapmanız gerekmez. Uygulamanızda olağan dışı oranda başarısız istek olup olmadığını bildirirler.
* [Ölçüm uyarıları](../alerts/alerts-log.md): bir ölçüm bir eşiği kesiştyolursa sizi uyarmak için Uyarılar ayarlayın. Bunları, uygulamanıza kodladığınız özel ölçümlerde ayarlayabilirsiniz.


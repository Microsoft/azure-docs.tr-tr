---
title: Azure Izleyici (Önizleme) ile Python uygulamalarını izleme | Microsoft Docs
description: Azure Izleyici ile OpenCensus Python 'ı bağlamak için yönergeler sağlar
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: ca34a92dc69cb500efb55f575420d47607cd1a46
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132216"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Python uygulamanız için Azure Izleyicisini ayarlama (Önizleme)

Azure Izleyici, [Opencensus](https://opencensus.io)ile tümleştirme yoluyla, Python uygulamalarının dağıtılmış izlemeyi, ölçüm toplamayı ve günlüğe kaydedilmesini destekler. Bu makale, Python için OpenCensus ayarlama ve izleme verilerinizi Azure Izleyici 'ye gönderme sürecinde size yol gösterecektir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- Python yüklemesi. Bu makalede [Python 3.7.0](https://www.python.org/downloads/)kullanılmaktadır, ancak önceki sürümler büyük olasılıkla küçük değişikliklerle çalışacaktır.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Azure Izleyici 'de Application Insights kaynağı oluşturma

İlk olarak Azure Izleyici 'de bir Application Insights kaynak oluşturmanız gerekir ve bu işlem bir izleme anahtarı (Ikey) oluşturur. Ikey daha sonra, Azure Izleyici 'ye telemetri verileri gönderecek şekilde OpenCensus SDK 'sını yapılandırmak için kullanılır.

1. **Kaynak oluştur** > **Geliştirici araçları** > **Application Insights** seçeneğini belirleyin.

   ![Application Insights kaynağı ekleme](./media/opencensus-python/0001-create-resource.png)

1. Bir yapılandırma kutusu görünür. Giriş alanlarını doldurun için aşağıdaki tabloyu kullanın.

   | Ayar        | Değer           | Açıklama  |
   | ------------- |:-------------|:-----|
   | **Adı**      | Genel benzersiz değer | İzlemekte olduğunuz uygulamayı tanımlayan ad |
   | **Kaynak Grubu**     | myResourceGroup      | Application Insights verileri barındıracak yeni kaynak grubunun adı |
   | **Konum** | Doğu ABD | Size yakın veya uygulamanızın barındırıldığı yerin yakınında bir konum |

1. **Oluştur**'u seçin.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Azure Izleyici için OpenCensus Python SDK 'Sı ile işaretleme

OpenCensus Azure Izleyici exporler 'i yükler:

```console
python -m pip install opencensus-ext-azure
```

Paketlerin ve tümleştirmelerin tam listesi için bkz. [Opencensus paketleri](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> `python -m pip install opencensus-ext-azure` komutu, Python yüklemeniz için ayarlanmış bir `PATH` ortam değişkenine sahip olduğunuzu varsayar. Bu değişkeni yapılandırmadıysanız, Python yürütülebilirinizin bulunduğu konuma tam dizin yolu sağlamanız gerekir. Sonuç şöyle bir komuttur: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

SDK, Azure Izleyici 'ye farklı telemetri türleri göndermek için üç Azure Izleyici dışarı aktarmak kullanır: izleme, ölçümler ve Günlükler. Bu telemetri türleri hakkında daha fazla bilgi için bkz. [veri platformuna genel bakış](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Bu telemetri türlerini üç dışarı aktarmak yoluyla göndermek için aşağıdaki yönergeleri kullanın.

### <a name="trace"></a>İzleme

1. İlk olarak, bazı izleme verilerini yerel olarak oluşturalım. Python boş veya istediğiniz Düzenleyicinizde aşağıdaki kodu girin.

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

2. Kodu çalıştırmak, sürekli olarak bir değer girmenizi ister. Her giriş ile, değer kabuğa yazdırılır ve OpenCensus Python modülü karşılık gelen `SpanData`bir parçasını oluşturacaktır. OpenCensus projesi bir izlemeyi bir [yayılma ağacı olarak](https://opencensus.io/core-concepts/tracing/)tanımlar.
    
    ```
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

3. Değer girilmesi, tanıtım amacıyla yararlı olsa da, son olarak `SpanData` Azure Izleyici 'ye yayma istiyoruz. Aşağıdaki kod örneğine göre kodunuzu önceki adımdan değiştirin:

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

4. Artık Python betiğini çalıştırdığınızda, yine de değer girmeniz istenir, ancak yalnızca değeri kabukta yazdırılıyor. Oluşturulan `SpanData` Azure Izleyici 'ye gönderilir. `dependencies`' ın altında, yayılan yayılmış verileri bulabilirsiniz.

5. OpenCensus 'de örnekleme hakkında bilgi edinmek için, [opencensus 'de örneklemeye](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-fixed-rate-sampling-in-opencensus-python)göz atın.

6. İzleme verilerinizde telemetri bağıntısı hakkında ayrıntılı bilgi edinmek için, OpenCensus [telemetri bağıntısını](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)göz atın.

### <a name="metrics"></a>Ölçümler

1. İlk olarak, bazı yerel ölçüm verileri oluşturalım. Kullanıcının Enter tuşuna bastığı süreyi izlemek için basit bir ölçüm oluşturacağız.

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
2. Kodu çalıştırmak, sürekli olarak ENTER tuşuna basmanız istenir. ENTER tuşuna basıldığı sayıyı izlemek için bir ölçüm oluşturulur. Her giriş ile, değer artırılır ve ölçüm bilgileri konsolda görüntülenir. Bu bilgiler, ölçüm güncellendiği zaman geçerli değeri ve geçerli zaman damgasını içerir.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Değer girilmesi, tanıtım amacıyla yararlı olsa da, sonunda ölçüm verilerini Azure Izleyici 'ye yayma istiyoruz. Aşağıdaki kod örneğine göre kodunuzu önceki adımdan değiştirin:

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

4. Dışarı Aktarıcı, ölçüm verilerini Azure Izleyici 'ye sabit bir aralıkla gönderir. Varsayılan değer 15 saniyedir. Tek bir ölçümü izliyoruz, bu nedenle içerdiği değer ve zaman damgasıyla birlikte bu ölçüm verileri her aralığa gönderilir. `customMetrics`altında verileri bulabilirsiniz.

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

2.  Kod sürekli olarak bir değer girilmesini ister. Her girilen değer için bir günlük girişi yayınlanır.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Değer girilmesi, tanıtım amacıyla yararlı olsa da, sonunda ölçüm verilerini Azure Izleyici 'ye yayma istiyoruz. Aşağıdaki kod örneğine göre kodunuzu önceki adımdan değiştirin:

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

4. Dışarı aktarma programı günlük verilerini Azure Izleyici 'ye gönderir. `traces`altında verileri bulabilirsiniz.

5. Günlüklerinizi izleme bağlamı verileriyle zenginleştirme hakkında daha fazla bilgi için bkz. OpenCensus Python [günlükleri tümleştirmesi](https://docs.microsoft.com/azure/azure-monitor/app/correlation#logs-correlation).

## <a name="view-your-data-with-queries"></a>Sorgular ile verilerinizi görüntüleme

Uygulamalarınızdan gönderilen telemetri verilerini **Günlükler (Analiz)** sekmesinden görüntüleyebilirsiniz.

![Kırmızı kutuda seçilen "Günlükler (Analiz)" ile genel bakış bölmesinin ekran görüntüsü](./media/opencensus-python/0010-logs-query.png)

**Etkin**altındaki listede:

- Azure Izleyici izleme programı ile gönderilen telemetri için, gelen istekler `requests`altında görünür. Giden veya işlem içi istekler `dependencies`altında görüntülenir.
- Azure Izleyici ölçümleri dışarı aktarıcı ile gönderilen telemetri için `customMetrics`altında gönderilen ölçümler görüntülenir.
- Azure Izleyici günlükleri dışarı aktarıcı ile gönderilen telemetri için Günlükler `traces`altında görüntülenir. Özel durumlar `exceptions`altında görünür.

Sorguları ve günlükleri kullanma hakkında daha ayrıntılı bilgi için bkz. [Azure izleyici 'de Günlükler](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Python için OpenCensus hakkında daha fazla bilgi edinin

* [GitHub 'da OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [Liş](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask tümleştirmesi](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Docgo tümleştirmesi](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL tümleştirmesi](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulama eşlemesi](./../../azure-monitor/app/app-map.md)
* [Uçtan uca performans izleme](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Uyarılar

* [Kullanılabilirlik testleri](../../azure-monitor/app/monitor-web-app-availability.md): Sitenizin web’de görünür olduğundan emin olmaya yönelik testler oluşturun.
* [Akıllı tanılama](../../azure-monitor/app/proactive-diagnostics.md): Bu testler otomatik olarak çalıştığından, bunları ayarlamak için herhangi bir şey yapmanız gerekmez. Uygulamanızda olağan dışı oranda başarısız istek olup olmadığını bildirirler.
* [Ölçüm uyarıları](../../azure-monitor/app/alerts.md): bir ölçüm bir eşiği kesiştyolursa sizi uyarmak için Uyarılar ayarlayın. Bunları, uygulamanıza kodladığınız özel ölçümlerde ayarlayabilirsiniz.

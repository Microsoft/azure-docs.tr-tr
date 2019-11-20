---
title: Azure Application Insights günlük tabanlı ölçümler | Microsoft Docs
description: Bu makalede, desteklenen toplamalar ve boyutlarla Azure Application Insights ölçümleri listelenmektedir. Günlük tabanlı ölçümler hakkındaki ayrıntılar, temeldeki kusto sorgu deyimlerini içerir.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 847c56faae61483813286c46190764327e287783
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887246"
---
# <a name="application-insights-log-based-metrics"></a>Günlük tabanlı ölçümleri Application Insights

Application Insights günlük tabanlı ölçümler, izlenen uygulamalarınızın durumunu analiz etmenize, güçlü panolar oluşturmanıza ve uyarıları yapılandırmanıza olanak tanır. İki çeşit ölçüm vardır:

* Sahnenin arkasındaki [günlük tabanlı ölçümler](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) , depolanan olaylardaki [kusto sorgularına](https://docs.microsoft.com/azure/kusto/query/) çevrilir.
* [Standart ölçümler](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) önceden toplanmış zaman serisi olarak depolanır.

Koleksiyon sırasında *Standart ölçümler* önceden toplanmış olduğundan, sorgu zamanında daha iyi performansa sahip olurlar. Bu, dinamik olarak ve gerçek zamanlı uyarı oluşturma için daha iyi bir seçenek sunar. *Günlük tabanlı ölçümler* daha fazla boyuta sahiptir. Bu, veri analizi ve geçici Tanılamalar için üst seçenek sağlar. [Ölçüm Gezgini](metrics-getting-started.md)'nde günlük tabanlı ve standart ölçümler arasında geçiş yapmak için [ad alanı seçiciyi](metrics-getting-started.md#create-your-first-metric-chart) kullanın.

## <a name="interpret-and-use-queries-from-this-article"></a>Bu makaledeki sorguları yorumlama ve kullanma

Bu makalede, desteklenen toplamalar ve boyutlarla ölçümler listelenir. Günlük tabanlı ölçümler hakkındaki ayrıntılar, temeldeki kusto sorgu deyimlerini içerir. Kolaylık olması için, her sorgu zaman ayrıntı düzeyi, grafik türü için varsayılan değerleri kullanır ve bazı durumlarda Log Analytics sorgu kullanımını kolaylaştıran boyutları böyor.

Ölçüm [Gezgini](metrics-getting-started.md)'nde aynı ölçümü çizerseniz varsayılan yoktur; sorgu, grafik ayarlarınıza göre dinamik olarak ayarlanır:

- Seçilen **zaman aralığı** , yalnızca seçilen zaman aralığından olayları seçmek için ek bir *WHERE timestamp...* yan tümcesine çevrilir. Örneğin, en son 24 saat için verileri gösteren bir grafik, sorgu *| WHERE zaman damgası > önce (24 h)* ' i içerir.

- Seçilen **zaman ayrıntı düzeyi** nihai özetlemeye yerleştirsin *... by bin (zaman damgası, [zaman çizgisi])* yan tümcesi.

- Seçili herhangi bir **filtre** boyutu, diğer *WHERE* yan tümcelerlerine çevrilir.

- Seçili **bölünmüş grafik** boyutu fazladan bir özetleme özelliğine çevrilir. Örneğin, grafiğinizi *konuma*göre bölüerseniz ve 5 dakikalık bir zaman ayrıntı düzeyi kullanarak çizim yaparsanız, *özetleme* yan tümcesi özetlenir *... bin (zaman damgası, 5 milyon), konum*.

> [!NOTE]
> Kusto sorgu diline yeni başladıysanız, kusto deyimlerini kopyalayıp Log Analytics sorgu bölmesine hiçbir değişiklik yapmadan başlayabilirsiniz. Temel grafiği görmek için **Çalıştır** ' a tıklayın. Sorgu dilinin söz dizimini anlamaya başladığınızda, küçük değişiklikler yapmaya başlayabilir ve yaptığınız değişikliğin etkisini görebilirsiniz. Kendi verilerinizi keşfetmek, [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) ve [Azure izleyici](../../azure-monitor/overview.md)'nin tam gücünü yapmaya başlamak için harika bir yoldur.

## <a name="availability-metrics"></a>Kullanılabilirlik ölçümleri

Kullanılabilirlik kategorisindeki ölçümler, Web uygulamanızın durumunu dünyanın dört bir yanındaki noktalarda gözlemleyerek görmenizi sağlar. [Kullanılabilirlik testlerini](../../azure-monitor/app/monitor-web-app-availability.md) bu kategorideki herhangi bir ölçümü kullanmaya başlamak üzere yapılandırın.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Kullanılabilirlik (Kullanılabilirbilityresults/Kullanılabilirbilitypercentage)
*Kullanılabilirlik* ölçümü, herhangi bir sorunu algılamadığınız Web testi çalışmalarının yüzdesini gösterir. Olası en düşük değer 0 ' dır. Bu, tüm Web testinin çalıştığını gösterir. 100 değeri, tüm Web testinin doğrulama ölçütlerini geçirdiğini gösterir.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|---|---|---|
|Yüzde|Ortalama|Çalıştırma konumu, test adı|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Kullanılabilirlik testi süresi (Kullanılabilirbilityresults/Duration)

*Kullanılabilirlik testi süresi* ölçümü, Web testinin çalışması için ne kadar zaman sürdüğünü gösterir. [Çok adımlı Web testleri](../../azure-monitor/app/availability-multistep.md)için, ölçüm tüm adımların toplam yürütme süresini yansıtır.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|Çalıştırma konumu, test adı, test sonucu

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Kullanılabilirlik testleri (Kullanılabilirbilityresults/Count)

*Kullanılabilirlik testleri* ölçümü, Azure izleyici tarafından çalıştırılan Web testlerinin sayısını yansıtır.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|---|---|---|
|Sayı|Sayı|Çalıştırma konumu, test adı, test sonucu|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Tarayıcı ölçümleri

Tarayıcı ölçümleri, gerçek Son Kullanıcı tarayıcılarından Application Insights JavaScript SDK 'Sı tarafından toplanır. Kullanıcılarınızın web uygulamanızla ilgili deneyimi hakkında harika öngörüler sağlar. Tarayıcı ölçümleri genellikle örneklenir, yani örnekleme tarafından çarpıtılmış olan sunucu tarafı ölçümleriyle karşılaştırıldığında kullanım numaralarının daha yüksek hassasiyetini sağlar.

> [!NOTE]
> Tarayıcı ölçümlerini toplamak için uygulamanızın [Application Insights JavaScript SDK 'sı](../../azure-monitor/app/javascript.md)ile işaretlenmiş olması gerekir.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tarayıcı sayfa yükleme süresi (Browserzamanlamalar/totalDuration)

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|None|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>İstemci işlem süresi (Browserzamanlaması/processingDuration)

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|None|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Sayfa yükleme ağ bağlantı süresi (Browserzamanlamalar/networkDuration)

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|None|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Yanıt süresini alma (Browserzamanlamalar/receiveDuration)

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|None|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Gönderme isteği süresi (Browserzamanlamalar/sendDuration)

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|None|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Hata ölçümleri

**Hatalarda** ölçümler, işleme istekleri, bağımlılık çağrıları ve oluşturulan özel durumlarla ilgili sorunları gösterir.

### <a name="browser-exceptions-exceptionsbrowser"></a>Tarayıcı özel durumları (özel durumlar/tarayıcı)

Bu ölçüm, tarayıcıda çalışan uygulama kodunuzda oluşan özel durum sayısını yansıtır. Ölçüsüne yalnızca bir ```trackException()``` Application Insights API çağrısıyla izlenen özel durumlar dahildir.

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|Notlar|
|---|---|---|---|
|Sayı|Sayı|None|Günlük tabanlı sürüm **Sum** toplamasını kullanır|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Bağımlılık çağrısı sorunları (bağımlılıklar/başarısız)

Başarısız bağımlılık çağrılarının sayısı.

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|Notlar|
|---|---|---|---|
|Sayı|Sayı|None|Günlük tabanlı sürüm **Sum** toplamasını kullanır|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Özel durumlar (özel durumlar/sayı)

Application Insights için bir özel durum kaydettiğinizde, SDK 'nın [trackexception () yöntemine](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) bir çağrı vardır. Özel durumlar ölçümü, günlüğe kaydedilen özel durumların sayısını gösterir.

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|Notlar|
|---|---|---|---|
|Sayı|Sayı|Bulut rolü adı, bulut rolü örneği, cihaz türü|Günlük tabanlı sürüm **Sum** toplamasını kullanır|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Başarısız istekler (istek/başarısız)

*Başarısız*olarak işaretlenen izlenen sunucu isteklerinin sayısı. Varsayılan olarak, Application Insights SDK, HTTP yanıt kodu 5 xx veya 4xx döndüren her sunucu isteğini başarısız bir istek olarak otomatik olarak işaretler. [Özel bir telemetri başlatıcısında](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)istek telemetri öğesinin *başarı* özelliğini değiştirerek bu mantığı özelleştirebilirsiniz.

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|Notlar|
|---|---|---|---|
|Sayı|Sayı|Bulut rolü örneği, bulut rolü adı, gerçek veya yapay trafik, Istek performansı, yanıt kodu|Günlük tabanlı sürüm **Sum** toplamasını kullanır|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Sunucu özel durumları (özel durumlar/sunucu)

Bu ölçüm, sunucu özel durumlarının sayısını gösterir.

|Ölçü birimi|Desteklenen toplamalar|Önceden toplanmış Boyutlar|Notlar|
|---|---|---|---|
|Sayı|Sayı|Bulut rolü adı, bulut rolü örneği|Günlük tabanlı sürüm **Sum** toplamasını kullanır|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Performans sayaçları

[Application Insights tarafından toplanan sistem performans sayaçlarına](../../azure-monitor/app/performance-counters.md)erişmek için **performans sayaçları** kategorisindeki ölçümleri kullanın.

### <a name="available-memory-performancecountersavailablememory"></a>Kullanılabilir bellek (performanceCounters/kullanılabilirliği Blememory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Özel durum oranı (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP isteği yürütme süresi (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP istek hızı (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Uygulama kuyruğundaki HTTP istekleri (performanceCounters/Requestsınqueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>İşlem CPU 'SU (performanceCounters/processCpuPercentage)

Ölçüm, izlenen uygulamanızı barındıran işlem tarafından toplam işlemci kapasitesinin ne kadarının tüketildiğini gösterir.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
|Yüzde|Ortalama, en düşük, en fazla|Bulut rolü örneği

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>İşlem GÇ oranı (performanceCounters/Processıobitespersecond)

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
|Bayt/saniye|Ortalama, en düşük, en fazla|Bulut rolü örneği

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>İşlem özel baytları (performanceCounters/processPrivateBytes)

İzlenen işlemin verileri için ayırdığı, paylaşılmayan bellek miktarı.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
|Sayacının|Ortalama, en düşük, en fazla|Bulut rolü örneği

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>İşlemci zamanı (performanceCounters/processorCpuPercentage)

İzlenen sunucu örneğinde çalışan *Tüm* IŞLEMLERE göre CPU tüketimi.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
|Yüzde|Ortalama, en düşük, en fazla|Bulut rolü örneği

>[!NOTE]
> İşlemci zamanı ölçümü, Azure Uygulama Hizmetleri 'nde barındırılan uygulamalar için kullanılamaz. Uygulama hizmetlerinde barındırılan Web uygulamalarının CPU kullanımını izlemek için [Işlem CPU](#process-cpu-performancecountersprocesscpupercentage) ölçüsünü kullanın.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Sunucu ölçümleri

### <a name="dependency-calls-dependenciescount"></a>Bağımlılık çağrıları (bağımlılıklar/sayı)

Bu ölçüm, bağımlılık çağrılarının sayısıyla ilgili olarak yapılır.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Bağımlılık süresi (bağımlılıklar/süre)

Bu ölçüm, bağımlılık çağrılarının süresini ifade eder.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Sunucu istekleri (istek/sayı)

Bu ölçüm, Web uygulamanız tarafından alınan gelen sunucu isteklerinin sayısını yansıtır.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Sunucu yanıt süresi (istek/süre)

Bu ölçüm, sunucuların gelen istekleri işlemesi için geçen süreyi yansıtır.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Ölçümleri kullanma

### <a name="page-view-load-time-pageviewsduration"></a>Sayfa görünümü yükleme süresi (pageViews/Duration)

Bu ölçüm, PageView olaylarının yüklenmesi için geçen süre miktarını ifade eder.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Sayfa görünümleri (pageViews/Count)

TrackPageView () Application Insights API ile günlüğe kaydedilen PageView olaylarının sayısı.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Oturumlar (oturum/sayı)

Bu ölçüm, farklı oturum kimliklerinin sayısını ifade eder.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>İzlemeler (izlemeler/sayım)

TrackTrace () Application Insights API çağrısıyla günlüğe kaydedilen izleme deyimlerinin sayısı.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Kullanıcılar (Kullanıcı/sayı)

Uygulamanıza erişen farklı kullanıcı sayısı. Bu ölçümün doğruluğu, telemetri örnekleme ve filtreleme kullanılarak önemli ölçüde etkilenebilir.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Kullanıcılar, kimliği doğrulanmış (kullanıcılar/kimlik doğrulamalı)

Uygulamanızda kimliği doğrulanan farklı kullanıcı sayısı.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```

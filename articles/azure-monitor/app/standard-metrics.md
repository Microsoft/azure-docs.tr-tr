---
title: Azure Application Insights standart ölçümleri | Microsoft Docs
description: Bu makalede, desteklenen toplamalar ve boyutlarla Azure Application Insights ölçümleri listelenmektedir.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.openlocfilehash: 494976bf09e5d1717bc60a1f12e039621061e6fa
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385471"
---
# <a name="application-insights-standard-metrics"></a>Standart ölçümleri Application Insights

Standart ölçümler koleksiyon sırasında önceden toplanır ve sorgu zamanında daha iyi performansa sahiptir. Böylece panolar ve gerçek zamanlı uyarı için en iyi seçim yapılır.

## <a name="availability-metrics"></a>Kullanılabilirlik ölçümleri

Kullanılabilirlik kategorisindeki ölçümler, Web uygulamanızın durumunu dünyanın dört bir yanındaki noktalarda gözlemleyerek görmenizi sağlar. [Kullanılabilirlik testlerini](../app/monitor-web-app-availability.md) bu kategorideki herhangi bir ölçümü kullanmaya başlamak üzere yapılandırın.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Kullanılabilirlik (Kullanılabilirbilityresults/Kullanılabilirbilitypercentage)
*Kullanılabilirlik* ölçümü, herhangi bir sorunu algılamadığınız Web testi çalışmalarının yüzdesini gösterir. Olası en düşük değer 0 ' dır. Bu, tüm Web testinin çalıştığını gösterir. 100 değeri, tüm Web testinin doğrulama ölçütlerini geçirdiğini gösterir.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|---|---|---|
|Yüzde|Ortalama|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>Kullanılabilirlik testi süresi (Kullanılabilirbilityresults/Duration)

*Kullanılabilirlik testi süresi* ölçümü, Web testinin çalışması için ne kadar zaman sürdüğünü gösterir. [Çok adımlı Web testleri](../app/availability-multistep.md)için, ölçüm tüm adımların toplam yürütme süresini yansıtır.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>Kullanılabilirlik testleri (Kullanılabilirbilityresults/Count)

*Kullanılabilirlik testleri* ölçümü, Azure izleyici tarafından çalıştırılan Web testlerinin sayısını yansıtır.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|---|---|---|
|Count|Count|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>Tarayıcı ölçümleri

Tarayıcı ölçümleri, gerçek Son Kullanıcı tarayıcılarından Application Insights JavaScript SDK 'Sı tarafından toplanır. Kullanıcılarınızın web uygulamanızla ilgili deneyimi hakkında harika öngörüler sağlar. Tarayıcı ölçümleri genellikle örneklenir, yani örnekleme tarafından çarpıtılmış olan sunucu tarafı ölçümleriyle karşılaştırıldığında kullanım numaralarının daha yüksek hassasiyetini sağlar.

> [!NOTE]
> Tarayıcı ölçümlerini toplamak için uygulamanızın [Application Insights JavaScript SDK 'sı](../app/javascript.md)ile işaretlenmiş olması gerekir.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tarayıcı sayfa yükleme süresi (Browserzamanlamalar/totalDuration)

|Ölçü birimi|Desteklenen toplamalar| Desteklenen boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|Yok|

### <a name="client-processing-time-browsertimingprocessingduration"></a>İstemci işlem süresi (Browserzamanlaması/processingDuration)

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|Yok|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Sayfa yükleme ağ bağlantı süresi (Browserzamanlamalar/networkDuration)

|Ölçü birimi|Desteklenen toplamalar| Desteklenen boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|Yok|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Yanıt süresini alma (Browserzamanlamalar/receiveDuration)

|Ölçü birimi|Desteklenen toplamalar| Desteklenen boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|Yok|

### <a name="send-request-time-browsertimingssendduration"></a>Gönderme isteği süresi (Browserzamanlamalar/sendDuration)

|Ölçü birimi|Desteklenen toplamalar| Desteklenen boyutlar|
|---|---|---|
|Mayacak|Ortalama, en düşük, en fazla|Yok|

## <a name="failure-metrics"></a>Hata ölçümleri

**Hatalarda** ölçümler, işleme istekleri, bağımlılık çağrıları ve oluşturulan özel durumlarla ilgili sorunları gösterir.

### <a name="browser-exceptions-exceptionsbrowser"></a>Tarayıcı özel durumları (özel durumlar/tarayıcı)

Bu ölçüm, tarayıcıda çalışan uygulama kodunuzda oluşan özel durum sayısını yansıtır. ```trackException()```Ölçüsüne yalnızca bir APPLICATION INSIGHTS API çağrısıyla izlenen özel durumlar dahildir.

|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar|
|---|---|---|---|
| Count | Count | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>Bağımlılık çağrısı sorunları (bağımlılıklar/başarısız)

Başarısız bağımlılık çağrılarının sayısı.

|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar |
|---|---|---|---|
|Count|Count| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>Özel durumlar (özel durumlar/sayı)

Application Insights için bir özel durum kaydettiğinizde, SDK 'nın [trackexception () yöntemine](../app/api-custom-events-metrics.md#trackexception) bir çağrı vardır. Özel durumlar ölçümü, günlüğe kaydedilen özel durumların sayısını gösterir.

|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>Başarısız istekler (istek/başarısız)

*Başarısız* olarak işaretlenen izlenen sunucu isteklerinin sayısı. Varsayılan olarak, Application Insights SDK, HTTP yanıt kodu 5 xx veya 4xx döndüren her sunucu isteğini başarısız bir istek olarak otomatik olarak işaretler. [Özel bir telemetri başlatıcısında](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)istek telemetri öğesinin *başarı* özelliğini değiştirerek bu mantığı özelleştirebilirsiniz.


|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>Sunucu özel durumları (özel durumlar/sunucu)

Bu ölçüm, sunucu özel durumlarının sayısını gösterir.

|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>Performans sayaçları

[Application Insights tarafından toplanan sistem performans sayaçlarına](../app/performance-counters.md)erişmek için **performans sayaçları** kategorisindeki ölçümleri kullanın.

### <a name="available-memory-performancecountersavailablememory"></a>Kullanılabilir bellek (performanceCounters/kullanılabilirliği Blememory)

|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar |
|---|---|---|---|
|Bağımlı veriler: megabayt, gigabayt|Average, Max, min|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>Özel durum oranı (performanceCounters/exceptionRate)

|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar |
|---|---|---|---|
| Count | Average, Max, min | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP isteği yürütme süresi (performanceCounters/requestExecutionTime)

|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar |
|---|---|---|---|
| Mayacak | Average, Max, min | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP istek hızı (performanceCounters/requestsPerSecond)

|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar |
|---|---|---|---|
| Saniyedeki istek | Average, Max, min | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Uygulama kuyruğundaki HTTP istekleri (performanceCounters/Requestsınqueue)

|Ölçü birimi|Desteklenen toplamalar | Desteklenen boyutlar |
|---|---|---|---|
| Count | Average, Max, min | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>İşlem CPU 'SU (performanceCounters/processCpuPercentage)

Ölçüm, izlenen uygulamanızı barındıran işlem tarafından toplam işlemci kapasitesinin ne kadarının tüketildiğini gösterir.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
|Yüzde|Average, Max, min| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>İşlem GÇ oranı (performanceCounters/Processıobitespersecond)

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
|Bayt/saniye|Ortalama, en düşük, en fazla|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>İşlem özel baytları (performanceCounters/processPrivateBytes)

İzlenen işlemin verileri için ayırdığı, paylaşılmayan bellek miktarı.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
|Bayt|Ortalama, en düşük, en fazla|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>İşlemci zamanı (performanceCounters/processorCpuPercentage)

İzlenen sunucu örneğinde çalışan *Tüm* IŞLEMLERE göre CPU tüketimi.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
|Yüzde|Ortalama, en düşük, en fazla|`Cloud role instance` |

>[!NOTE]
> İşlemci zamanı ölçümü, Azure Uygulama Hizmetleri 'nde barındırılan uygulamalar için kullanılamaz. Uygulama hizmetlerinde barındırılan Web uygulamalarının CPU kullanımını izlemek için  [Işlem CPU](#process-cpu-performancecountersprocesscpupercentage) ölçüsünü kullanın.

## <a name="server-metrics"></a>Sunucu ölçümleri

### <a name="dependency-calls-dependenciescount"></a>Bağımlılık çağrıları (bağımlılıklar/sayı)

Bu ölçüm, bağımlılık çağrılarının sayısıyla ilgili olarak yapılır.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>Bağımlılık süresi (bağımlılıklar/süre)

Bu ölçüm, bağımlılık çağrılarının süresini ifade eder.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
| Saat | Ortalama, en düşük, en fazla | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>Sunucu isteği hızı (istek/sayı)

Bu ölçüm, Web uygulamanız tarafından alınan gelen sunucu isteklerinin sayısını yansıtır.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
| Count | Ortalama | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>Sunucu istekleri (istek/sayı)

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>Sunucu yanıt süresi (istek/süre)

Bu ölçüm, sunucuların gelen istekleri işlemesi için geçen süreyi yansıtır.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
| Saat | Ortalama, en düşük, en fazla | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>Kullanım ölçümleri

### <a name="page-view-load-time-pageviewsduration"></a>Sayfa görünümü yükleme süresi (pageViews/Duration)

Bu ölçüm, PageView olaylarının yüklenmesi için geçen süre miktarını ifade eder.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
| Saat | Ortalama, en düşük, en fazla | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>Sayfa görünümleri (pageViews/Count)

TrackPageView () Application Insights API ile günlüğe kaydedilen PageView olaylarının sayısı.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
| Count | Count | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>İzlemeler (izlemeler/sayım)

TrackTrace () Application Insights API çağrısıyla günlüğe kaydedilen izleme deyimlerinin sayısı.

|Ölçü birimi|Desteklenen toplamalar|Desteklenen boyutlar|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>Sonraki adımlar

* [Günlük tabanlı ve önceden toplanmış ölçümler](./pre-aggregated-metrics-log-metrics.md)hakkında bilgi edinin.
* [Günlük tabanlı ölçüm sorguları ve tanımları](../essentials/app-insights-metrics.md).
---
title: Azure Application Insights veri modeli | Microsoft Docs
description: JSON 'da sürekli dışarı aktarma işleminden dışarı aktarılmış özellikleri açıklar ve filtre olarak kullanılır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/08/2019
ms.openlocfilehash: 8f84e3179a6f949e4a322a2218736fc9ebe60442
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677903"
---
# <a name="application-insights-export-data-model"></a>Application Insights veri modelini dışarı aktarma
Bu tabloda, [Application Insights](../../azure-monitor/app/app-insights-overview.md) SDK 'lardan portala gönderilen telemetrinin özellikleri listelenmektedir.
Bu özellikleri [sürekli dışarı aktarmanın](export-telemetry.md)veri çıkışında görürsünüz.
Ayrıca, [Ölçüm Gezgini](../../azure-monitor/app/metrics-explorer.md) ve [Tanılama aramasında](../../azure-monitor/app/diagnostic-search.md)Özellik filtrelerinde görünürler.

Şunlara işaret eder:

* Bu tablolardaki `[0]`, dizin eklemeniz gereken yoldaki bir noktayı belirtir; Ancak her zaman 0 değildir.
* Süreler, mikro saniyenin onda biri olan süre, 10000000 = = 1 saniye.
* Tarihler ve saatler UTC biçimindedir ve ISO biçiminde verilir `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Örnek
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Bağlam
Tüm telemetri türlerine bir bağlam bölümü eşlik eder. Bu alanların hepsi her veri noktasıyla aktarılmaz.

| Yol | Tür | Notlar |
| --- | --- | --- |
| Context. Custom. Dimensions [0] |Object [] |Özel özellikler parametresi tarafından ayarlanan anahtar-değer dize çiftleri. Anahtar en fazla uzunluğu 100, değer en fazla uzunluk 1024. 100 ' den fazla benzersiz değer, özellik aranabilir ancak kesimde kullanılamaz. Ikey başına en fazla 200 anahtar. |
| Context. Custom. ölçümler [0] |Object [] |Özel ölçümler parametresi ve Trackölçümler tarafından ayarlanan anahtar-değer çiftleri. Anahtar en fazla uzunluğu 100, değerler sayısal olabilir. |
| Context. Data. eventTime |string |UTC |
| Context. Data. ısyapay |boole |İstek bir bot veya Web testinde geliyor gibi görünüyor. |
| Context. Data. samplingRate |number |Portala gönderilen SDK tarafından oluşturulan telemetri yüzdesi. Aralık 0.0-100.0. |
| Context. Device |object |İstemci cihazı |
| Context. Device. Browser |string |IE, Chrome,... |
| Context. Device. browserVersion |string |Chrome 48,0,... |
| Context. Device. deviceModel |string | |
| Context. Device. aygıtadı |string | |
| context.device.id |string | |
| Context. Device. locale |string |en-GB, de-DE,... |
| Context. Device. Network |string | |
| Context. Device. oemName |string | |
| Context. Device. OS |string | |
| Context. Device. osVersion |string |Konak işletim sistemi |
| Context. Device. Roleınstance |string |Sunucu konağının KIMLIĞI |
| Context. Device. roleName |string | |
| Context. Device. screenResolution |string | |
| Context. Device. Type |string |BILGISAYAR, tarayıcı,... |
| Context. Location |object |Clienentip 'den türetilmiş. |
| Context. Location. City |string |Bilinmiyorsa, clienentip 'den türetilmiş |
| Context. Location. clienentip |string |Son sekizgen, anonim olarak 0 ' dır. |
| Context. Location. kıent |string | |
| Context. Location. Country |string | |
| Context. Location. eyalet |string |Eyalet veya il |
| context.operation.id |string |Aynı işlem kimliğine sahip öğeler portalda Ilgili öğeler olarak gösterilir. Genellikle istek kimliği. |
| context.operation.name |string |URL veya istek adı |
| Context. Operation. parentID |string |İç içe ilişkili öğelere izin verir. |
| context.session.id |string |Aynı kaynaktan bir işlem grubunun kimliği. İşlem olmadan 30 dakikalık bir süre, bir oturumun sonuna işaret eder. |
| Context. Session. IsFirst |boole | |
| Context. User. accountAcquisitionDate |string | |
| Context. User. AccountID |string | |
| Context. User. anonAcquisitionDate |string | |
| Context. User. anonId |string | |
| Context. User. Authtanışisitiondate |string |[Kimliği doğrulanmış Kullanıcı](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| Context. User. Authıd |string | |
| Context. User. IsAuthenticated |boole | |
| Context. User. storeRegion |string | |
| Internal. Data. documentVersion |string | |
| internal.data.id |string | Bir öğe Application Insights yapıldığında atanan benzersiz kimlik |

## <a name="events"></a>Etkinlikler
[Trackevent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)tarafından oluşturulan özel olaylar.

| Yol | Tür | Notlar |
| --- | --- | --- |
| olay [0] sayısı |integer |100/([örnekleme](../../azure-monitor/app/sampling.md) hızı). Örneğin 4 = &gt;% 25. |
| olay [0] adı |string |Olay adı.  Maksimum uzunluk 250. |
| olay [0] URL 'si |string | |
| olay [0] urlData. Base |string | |
| olay [0] urlData. Host |string | |

## <a name="exceptions"></a>Özel Durumlar
Sunucudaki ve tarayıcıdaki [özel durumları](../../azure-monitor/app/asp-net-exceptions.md) raporlar.

| Yol | Tür | Notlar |
| --- | --- | --- |
| basicException [0] derlemesi |string | |
| basicException [0] sayısı |integer |100/([örnekleme](../../azure-monitor/app/sampling.md) hızı). Örneğin 4 = &gt;% 25. |
| basicException [0] exceptionGroup |string | |
| basicException [0] exceptionType |string | |
| basicException [0] failedUserCodeMethod |string | |
| basicException [0] failedUserCodeAssembly |string | |
| basicException [0] handledAt |string | |
| basicException [0] hasFullStack |boole | |
| basicException [0] kimliği |string | |
| basicException [0] yöntemi |string | |
| basicException [0] iletisi |string |Özel durum iletisi. En fazla uzunluk 10.000. |
| basicException [0] outerExceptionMessage |string | |
| basicException [0] outerExceptionThrownAtAssembly |string | |
| basicException [0] outerExceptionThrownAtMethod |string | |
| basicException [0] outerExceptionType |string | |
| basicException [0] outerId |string | |
| basicException [0] parsedStack [0] derlemesi |string | |
| basicException [0] parsedStack [0] dosya adı |string | |
| basicException [0] parsedStack [0] düzeyi |integer | |
| basicException [0] parsedStack [0] satırı |integer | |
| basicException [0] parsedStack [0] yöntemi |string | |
| basicException [0] yığını |string |En fazla uzunluk 10.000 |
| basicException [0] typeName |string | |

## <a name="trace-messages"></a>İzleme Iletileri
[Tracktrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)tarafından ve [günlük bağdaştırıcıları](../../azure-monitor/app/asp-net-trace-logs.md)tarafından gönderilir.

| Yol | Tür | Notlar |
| --- | --- | --- |
| ileti [0] loggerName |string | |
| ileti [0] Parametreler |string | |
| ileti [0] RAW |string |Günlük iletisi, en fazla uzunluk 10.000. |
| ileti [0] severityLevel |string | |

## <a name="remote-dependency"></a>Uzak bağımlılık
TrackDependency tarafından gönderildi. Sunucudaki [bağımlılıklara yapılan çağrıların](../../azure-monitor/app/asp-net-dependencies.md) performansını ve kullanımını ve tarayıcıdaki Ajax çağrılarını raporlamak için kullanılır.

| Yol | Tür | Notlar |
| --- | --- | --- |
| remoteDependency bağımlılığı [0] zaman uyumsuz |boole | |
| remoteDependency bağımlılığı [0] baseName |string | |
| remoteDependency bağımlılığı [0] commandName |string |Örneğin, "giriş/Dizin" |
| remoteDependency bağımlılığı [0] sayısı |integer |100/([örnekleme](../../azure-monitor/app/sampling.md) hızı). Örneğin 4 = &gt;% 25. |
| remoteDependency bağımlılığı [0] dependencyTypeName |string |HTTP, SQL,... |
| remoteDependency bağımlılığı [0] durationMetric. Value |number |Çağrıya göre yanıtın tamamlanmasına yönelik çağrıdan geçen süre |
| remoteDependency bağımlılığı [0] kimliği |string | |
| remoteDependency bağımlılığı [0] adı |string |'Deki. Maksimum uzunluk 250. |
| remoteDependency bağımlılığı [0] resultCode |string |HTTP bağımlılığıyla |
| remoteDependency bağımlılığı [0] başarılı |boole | |
| remoteDependency bağımlılığı [0] tür |string |Http, SQL,... |
| remoteDependency bağımlılığı [0] URL |string |Maksimum uzunluk 2000 |
| remoteDependency bağımlılığı [0] urlData. Base |string |Maksimum uzunluk 2000 |
| remoteDependency bağımlılığı [0] urlData. hashTag |string | |
| remoteDependency bağımlılığı [0] urlData. Host |string |Maksimum uzunluk 200 |

## <a name="requests"></a>İstekler
[Trackrequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)tarafından gönderildi. Standart modüller bunu, sunucuda ölçülen sunucu yanıt süresini raporlar olarak kullanır.

| Yol | Tür | Notlar |
| --- | --- | --- |
| istek [0] sayısı |integer |100/([örnekleme](../../azure-monitor/app/sampling.md) hızı). Örneğin: 4 = &gt;% 25. |
| istek [0] durationMetric. Value |number |İsteğin yanıt gelme süresi. 1E7 = = 1s |
| istek [0] kimliği |string |İşlem kimliği |
| istek [0] adı |string |Al/postala + URL tabanı.  Maksimum uzunluk 250 |
| istek [0] yanıt kodu |integer |İstemciye gönderilen HTTP yanıtı |
| istek [0] başarılı |boole |Varsayılan = = (yanıt kodu &lt; 400) |
| istek [0] URL 'si |string |Konak dahil değildir |
| istek [0] urlData. Base |string | |
| istek [0] urlData. hashTag |string | |
| istek [0] urlData. Host |string | |

## <a name="page-view-performance"></a>Sayfa görünümü performansı
Tarayıcı tarafından gönderilir. Bir sayfayı işleme süresini ölçer, kullanıcıdan isteği başlatan (zaman uyumsuz AJAX çağrıları hariç).

Bağlam değerleri istemci işletim sistemini ve tarayıcı sürümünü gösterir.

| Yol | Tür | Notlar |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |integer |Sayfayı görüntülemek için HTML alma sonundan itibaren geçen süre. |
| clientPerformance [0] adı |string | |
| clientPerformance [0] Ağbağlantısı. değer |integer |Ağ bağlantısı kurmak için geçen süre. |
| clientPerformance [0] receiveRequest. Value |integer |İsteğin, yanıt olarak HTML almaya yönelik gönderilme bitiş zamanı. |
| clientPerformance [0] sendRequest. Value |integer |HTTP isteğini göndermek için geçen süre. |
| clientPerformance [0] toplam. değer |integer |Sayfayı görüntülemek için isteğin gönderilmesi için başlangıç zamanı. |
| clientPerformance [0] URL 'si |string |Bu isteğin URL 'SI |
| clientPerformance [0] urlData. Base |string | |
| clientPerformance [0] urlData. diyez etiketi |string | |
| clientPerformance [0] urlData. Host |string | |
| clientPerformance [0] urlData. Protocol |string | |

## <a name="page-views"></a>Sayfa görünümleri
TrackPageView () veya [Stoptrackpage](../../azure-monitor/app/api-custom-events-metrics.md#page-views) tarafından gönderildi

| Yol | Tür | Notlar |
| --- | --- | --- |
| [0] sayısını görüntüle |integer |100/([örnekleme](../../azure-monitor/app/sampling.md) hızı). Örneğin 4 = &gt;% 25. |
| [0] durationMetric. Value görüntüle |integer |Değer, isteğe bağlı olarak trackPageView () veya startTrackPage ()-stopTrackPage () tarafından ayarlanır. ClientPerformance değerleriyle aynı değildir. |
| [0] adını görüntüle |string |Sayfa başlığı.  Maksimum uzunluk 250 |
| [0] URL 'sini görüntüle |string | |
| [0] urlData. Base öğesini görüntüle |string | |
| [0] urlData. diyez etiketini görüntüle |string | |
| [0] urlData. Host öğesini görüntüle |string | |

## <a name="availability"></a>Erişilebilirlik
[Kullanılabilirlik Web testlerini](../../azure-monitor/app/monitor-web-app-availability.md)raporlar.

| Yol | Tür | Notlar |
| --- | --- | --- |
| kullanılabilirlik [0] availabilityMetric.name |string |availability |
| kullanılabilirlik [0] Kullanılabilirbilitymetric. değer |number |1,0 veya 0,0 |
| kullanılabilirlik [0] sayısı |integer |100/([örnekleme](../../azure-monitor/app/sampling.md) hızı). Örneğin 4 = &gt;% 25. |
| kullanılabilirlik [0] dataSizeMetric.name |string | |
| kullanılabilirlik [0] dataSizeMetric. değer |integer | |
| kullanılabilirlik [0] durationMetric.name |string | |
| kullanılabilirlik [0] durationMetric. Value |number |Test süresi. 1E7 = = 1s |
| kullanılabilirlik [0] ileti |string |Hata tanılama |
| kullanılabilirlik [0] Sonuç |string |Başarılı/başarısız |
| kullanılabilirlik [0] runLocation |string |Http REQ coğrafi kaynağı |
| kullanılabilirlik [0] testName |string | |
| kullanılabilirlik [0] TestRunId |string | |
| kullanılabilirlik [0] test zaman damgası |string | |

## <a name="metrics"></a>Ölçümler
TrackMetric () tarafından oluşturuldu.

Ölçüm değeri Context. Custom. ölçümler [0] içinde bulunur

Örnek:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Ölçüm değerleri hakkında
Ölçüm değerleri, hem ölçüm raporlarında hem de başka bir yerde, standart bir nesne yapısıyla raporlanır. Örnek:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Şu anda bu durum, standart SDK modülleriyle raporlanan tüm değerlerde `count==1` de değişebilir ve yalnızca `name` ve `value` alanları yararlı olur. Diğer parametreleri ayarladığınız kendi TrackMetric çağrılarınızı yazarsanız, farklı oldukları durumlar tek durumdur.

Diğer alanların amacı, portala gelen trafiği azaltmak için ölçümlerin SDK 'da toplanmasına izin vermaktır. Örneğin, her ölçüm raporunu göndermeden önce birkaç ardışık readusing ortalamasını alabilirsiniz. Daha sonra Min, Max, standart sapma ve toplam değeri (Sum veya Average) hesaplar ve sayısı, rapor tarafından temsil edilen okuma sayısı olarak ayarlanır.

Yukarıdaki tablolarda, nadiren kullanılan alanları say, min, Max, stdDev ve sampledValue değerlerini attık.

Ölçümleri önceden toplamak yerine, telemetri hacmini azaltmanız gerekiyorsa [örnekleme](../../azure-monitor/app/sampling.md) kullanabilirsiniz.

### <a name="durations"></a>Sürelerde
Aksi belirtilmedikçe, süreler bir mikro saniyenin onda temsil edildiği sürece 10000000,0, 1 saniye anlamına gelir.

## <a name="see-also"></a>Ayrıca bkz.
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Sürekli dışarı aktarma](export-telemetry.md)
* [Kod örnekleri](export-telemetry.md#code-samples)

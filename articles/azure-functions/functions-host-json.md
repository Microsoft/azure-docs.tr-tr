---
title: Azure Işlevleri 2. x için başvuru host.js
description: Azure Işlevleri için başvuru belgeleri v2 çalışma zamanı ile dosyada host.js.
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: cbedf2212c52d8f1996d3cce0d96d494313ea525
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608828"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure İşlevleri 2.x ve üzeri için host.json başvurusu 

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Azure Işlevleri çalışma zamanının sürümünü seçin: "]
> * [Sürüm 1](functions-host-json-v1.md)
> * [Sürüm 2 +](functions-host-json.md)

Meta veri dosyası *host.js* , bir işlev uygulaması için tüm işlevleri etkileyen genel yapılandırma seçeneklerini içerir. Bu makalede, Azure Işlevleri çalışma zamanının 2. x sürümünden itibaren kullanılabilen ayarlar listelenir.  

> [!NOTE]
> Bu makale, Azure Işlevleri 2. x ve sonraki sürümleri için kullanılır.  1. x Işlevleri içindeki host.jsbaşvurusu için bkz. [ Azure işlevleri için başvuru üzerindehost.js, 1. x](functions-host-json-v1.md).

Diğer işlev uygulama yapılandırma seçenekleri, [uygulama ayarlarınızda](functions-app-settings.md) (dağıtılan uygulamalar için) veya [local.settings.js](functions-run-local.md#local-settings-file) dosyasına (yerel geliştirme için) yönetilir.

Bağlamalarla ilgili host.jsolan konfigürasyonlar, işlev uygulamasındaki her bir işleve eşit olarak uygulanır. 

Ayrıca, uygulama ayarlarını kullanarak [ortam başına ayarları geçersiz kılabilir veya uygulayabilirsiniz](#override-hostjson-values) .

## <a name="sample-hostjson-file"></a>Dosyadaki örnek host.js

Sürüm 2. x + için aşağıdaki örnek *host.js* , belirtilen tüm seçenekleri (yalnızca iç kullanım için olan) içerir.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "blobs": {},
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "retry": {
      "strategy": "fixedDelay",
      "maxRetryCount": 5,
      "delayInterval": "00:00:05"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "watchFiles": [ "myFile.txt" ]
}
```

Bu makalenin aşağıdaki bölümlerinde her üst düzey özellik açıklanmaktadır. Aksi belirtilmedikçe tümü isteğe bağlıdır.

## <a name="aggregator"></a>'yı

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>ApplicationInsights

Bu ayar [günlüğe kaydetme](#logging)işleminin bir alt öğesidir.

[Örnekleme seçenekleri](./configure-monitoring.md#configure-sampling)de dahil olmak üzere Application Insights seçeneklerini denetler.

Tüm JSON yapısı için bkz. önceki [örnek host.jsdosyası](#sample-hostjson-file).

> [!NOTE]
> Günlük örnekleme, bazı yürütmelerin Application Insights İzleyicisi dikey penceresinde gösterilmemesine neden olabilir. Günlük örneklemeyi önlemek için `excludedTypes: "Request"` `samplingSettings` değere ekleyin.

| Özellik | Varsayılan | Description |
| --------- | --------- | --------- | 
| samplingSettings | yok | Bkz. [ApplicationInsights. samplingSettings](#applicationinsightssamplingsettings). |
| Enableliveölçümleri | true | Canlı ölçüm toplamayı etkin bir şekilde sunar. |
| enableDependencyTracking | true | Bağımlılık izlemeyi etkinleştirilir. |
| enablePerformanceCountersCollection | true | Kudu performans sayaçlarını toplamayı etkinleştirilir. |
| liveMetricsInitializationDelay | 00:00:15 | Yalnızca dahili kullanım içindir. |
| httpAutoCollectionOptions | yok | Bkz. [ApplicationInsights. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | yok | Bkz. [ApplicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>ApplicationInsights. samplingSettings

Bu ayarlar hakkında daha fazla bilgi için bkz. [örnekleme Application Insights](../azure-monitor/app/sampling.md). 

|Özellik | Varsayılan | Description |
| --------- | --------- | --------- | 
| IsEnabled | true | Örneklemeyi etkinleştirilir veya devre dışı bırakır. | 
| maxTelemetryItemsPerSecond | 20 | Her bir sunucu konağında saniyede günlüğe kaydedilen telemetri öğelerinin hedef sayısı. Uygulamanız birçok ana bilgisayarda çalışıyorsa, bu değeri, genel hedef trafik hızında kalacak şekilde küçültün. | 
| evaluationInterval | 01:00:00 | Geçerli telemetri hızının yeniden değerlendirilme aralığı. Değerlendirme, hareketli ortalama olarak gerçekleştirilir. Telemetriyi ani bursts 'e tabi olursa bu aralığı kısaltmak isteyebilirsiniz. |
| ınitialörneklingpercentage| 100,0 | Örnekleme işleminin başlangıcında uygulanan ilk örnekleme yüzdesi, yüzdeyi dinamik olarak farklılık gösterir. Hata ayıklarken değeri küçültmeyin. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Örnekleme yüzdesi değeri değiştiğinde, bu özellik daha sonra Application Insights daha sonra örnekleme yüzdesini daha fazla veri yakalamaya ne kadar süre sonra yeniden tetiklemesine izin verileceğini belirler. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Örnekleme yüzdesi değeri değiştiğinde, bu özellik daha sonra daha az veri yakalamak için örnekleme yüzdesini yeniden ne kadar küçük bir süre sonra Application Insights izin verileceğini belirler. |
| Minörneklingpercentage | 0.1 | Örnekleme yüzdesi farklılık gösterdiğinden, bu özellik izin verilen minimum örnekleme yüzdesini belirler. |
| maxSamplingPercentage | 100,0 | Örnekleme yüzdesi farklılık gösterdiğinden, bu özellik izin verilen en fazla örnekleme yüzdesini belirler. |
| movingAverageRatio | 1.0 | Hareketli ortalama hesaplamasında, en son değere atanan ağırlık. 1 ' den küçük veya buna eşit bir değer kullanın. Daha küçük değerler, algoritmayı ani değişikliklere karşı daha az reaktif hale getirir. |
| excludedTypes | null | Örneklemeyi istemediğiniz türlerin noktalı virgülle ayrılmış listesi. Tanınan türler şunlardır: `Dependency` , `Event` , `Exception` , `PageView` , `Request` , ve `Trace` . Belirtilen türlerin tüm örnekleri iletilir; belirtilmeyen türler örneklenir. |
| ıncludedtypes | null | Örneklemeyi yapmak istediğiniz türlerin noktalı virgülle ayrılmış listesi; boş liste tüm türleri gösterir. `excludedTypes`Burada listelenen geçersiz kılma türlerinde listelenen tür. Tanınan türler şunlardır: `Dependency` , `Event` , `Exception` , `PageView` , `Request` , ve `Trace` . Belirtilen türlerin örnekleri örneklenir; belirtilmemiş veya zımni türler, örnekleme olmadan iletilir. |

### <a name="applicationinsightshttpautocollectionoptions"></a>ApplicationInsights. httpAutoCollectionOptions

|Özellik | Varsayılan | Description |
| --------- | --------- | --------- | 
| Enablehttptriggerextendedınınfocollection | true | HTTP Tetikleyicileri için genişletilmiş HTTP isteği bilgilerini etkinleştirilir veya devre dışı bırakır: gelen istek bağıntı üstbilgileri, çok izleme anahtarları desteği, HTTP yöntemi, yol ve yanıt. |
| enableW3CDistributedTracing | true | W3C dağıtılmış izleme protokolünün desteğini etkinleştirir veya devre dışı bırakır (ve eski bağıntı şemasını etkinleştirir). True ise varsayılan olarak etkindir `enableHttpTriggerExtendedInfoCollection` . `enableHttpTriggerExtendedInfoCollection`Yanlış ise, bu bayrak gelen isteklere değil yalnızca giden istekler için geçerlidir. |
| Enableresponseheaderınjection | true | Çoklu bileşen bağıntı üstbilgilerinin yanıtlara eklenmesine izin vermez veya devre dışı bırakır. Ekleme özelliğinin etkinleştirilmesi, birden çok izleme anahtarı kullanıldığında Application Insights bir uygulama eşlemesi oluşturmasına izin verir. True ise varsayılan olarak etkindir `enableHttpTriggerExtendedInfoCollection` . Bu ayar `enableHttpTriggerExtendedInfoCollection` yanlışsa uygulanmaz. |

### <a name="applicationinsightssnapshotconfiguration"></a>ApplicationInsights. snapshotConfiguration

Anlık görüntüler hakkında daha fazla bilgi için bkz. [.NET uygulamalarında özel durumlarla Ilgili hata ayıklama anlık görüntüleri](../azure-monitor/app/snapshot-debugger.md) ve [Application Insights Snapshot Debugger veya anlık görüntüleri görüntüleme sorunlarını giderme](../azure-monitor/app/snapshot-debugger-troubleshoot.md).

|Özellik | Varsayılan | Description |
| --------- | --------- | --------- | 
| Tendtendpoint | null | Application Insights Snapshot Debugger hizmetine bağlanmak için kullanılan uç nokta. Null ise, varsayılan bir uç nokta kullanılır. |
| captureSnapshotMemoryWeight | 0,5 | Anlık görüntü almak için yeterli bellek olup olmadığı denetlenirken geçerli işlem belleği boyutuna verilen ağırlık. Beklenen değer 0 ' dan büyük bir doğru kesenden (0 < CaptureSnapshotMemoryWeight < 1) fazla. |
| failedRequestLimit | 3 | Telemetri işlemcisi devre dışı bırakılmadan önce anlık görüntü istemek için başarısız istek sayısı sınırı.|
| handleUntrackedExceptions | true | Application Insights telemetri tarafından izlenmeyen özel durumların izlenmesini mümkün veya devre dışı bırakır. |
| IsEnabled | true | Anlık görüntü toplamayı etkinleştirilir veya devre dışı bırakır | 
| isEnabledInDeveloperMode | yanlış | Geliştirici modunda anlık görüntü toplamayı etkinleştirme veya devre dışı bırakır. |
| Isenabledwhenprofilleme | true | Application Insights Profiler ayrıntılı bir profil oluşturma oturumu toplasa bile, anlık görüntü oluşturmayı etkinleştirilir veya devre dışı bırakır. |
| ısexceptionsnappointsenabled | yanlış | Özel durumların filtrelenmesini mümkün veya devre dışı bırakır. |
| ılowprioritysnapshotuploader | true | Anlık görüntüyle ayarlana Tuploader işleminin normal öncelikte çalıştırılıp çalıştırılmayacağını belirler. |
| maximumCollectionPlanSize | 50 | Bire 9999 arasında bir aralıkta izleyediğimiz en fazla sorun sayısı. |
| Maximumanlık görüntüyle gerekli | 3 | Tek bir sorun için toplanan en fazla anlık görüntü sayısı, bire 999 arasındadır. Bir sorun, uygulamanızda tek bir throw deyimleri olarak düşünülebilir. Bir sorun için toplanan anlık görüntü sayısı bu değere ulaştığında, sorun sayaçları sıfırlanana kadar bu sorun için daha fazla anlık görüntü toplanmaz (bkz `problemCounterResetInterval` .) ve `thresholdForSnapshotting` sınıra yeniden ulaşılırsa. |
| Problemcounterresetınterval | 24:00:00 | Bir aralıktaki sorun sayaçlarını bir dakikadan yedi güne sıfırlama sıklığı. Bu aralığa ulaşıldığında, tüm sorun sayıları sıfıra sıfırlanır. Anlık görüntü yapma eşiğine zaten ulaşmış, ancak henüz ' de anlık görüntü sayısı üretilmemiş olan mevcut sorunlar `maximumSnapshotsRequired` etkin kalır. |
| Provideanonymoustelemetrisi | true | Microsoft 'a anonim kullanım ve hata telemetrisi gönderileceğini belirler. Bu telemetri, Snapshot Debugger sorunları gidermeye yardımcı olmak için Microsoft 'a başvurdıysanız kullanılabilir. Kullanım düzenlerini izlemek için de kullanılır. |
| Reconnectınterval | 00:15:00 | Snapshot Debugger uç noktasına ne sıklıkta yeniden bağlanıyoruz. İzin verilen Aralık, bir güne kadar bir dakikadır. |
| shadowCopyFolder | null | Gölge kopyalama ikilileri için kullanılacak klasörü belirtir. Ayarlanmamışsa, aşağıdaki ortam değişkenleri tarafından belirtilen klasörler sırayla denenir: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | True ise, bir Snapshot Tuploader 'ın yalnızca bir örneği, ınstrumentationkey 'i paylaşan birden çok uygulama için anlık görüntüleri toplayıp karşıya yükler. False olarak ayarlanırsa, anlık görüntüyle ilgili Tuploader her bir (ProcessName, ınstrumentationkey) tanımlama grubu için benzersiz olacaktır. |
| Anlık Ileti Tinlowprioritythread | true | Düşük GÇ öncelikli iş parçacığında anlık görüntülerin işleneceğini belirler. Anlık görüntü oluşturma hızlı bir işlemdir, ancak Snapshot Debugger hizmetine bir anlık görüntü yüklemek için öncelikle diske bir mini döküm olarak yazılması gerekir. Bu, anlık görüntüyle ilgili Tuploader sürecinde meydana gelir. Bu değerin true olarak ayarlanması, mini döküm yazmak için düşük öncelikli GÇ kullanır ve bu, kaynaklar için uygulamanızla rekabet etmenize izin vermez. Bu değerin false olarak ayarlanması, uygulamanızı yavaşlatarak mini döküm oluşturma işlemini hızlandırır. |
| Anlık görüntüyle (gün sayısı) | 30 | Bir gün içinde izin verilen en fazla anlık görüntü sayısı (24 saat). Bu sınır Application Insights hizmet tarafında da zorlanır. Karşıya yüklemeler, uygulama başına günlük 50 (yani, izleme anahtarı başına) ile sınırlıdır. Bu değer, karşıya yükleme sırasında reddedilecek ek anlık görüntülerin oluşturulmasına engel olur. Sıfır değeri sınırı tamamen kaldırır, önerilmez. |
| snapshotsPerTenMinutesLimit | 1 | 10 dakika içinde izin verilen en fazla anlık görüntü sayısı. Bu değerde üst sınır olmasa da, uygulamanızın performansını etkileyebileceğinden, üretim iş yükleri üzerinde dikkatli davranmaya dikkat edin. Anlık görüntü oluşturmak hızlıdır, ancak anlık görüntünün bir mini döküm oluşturması ve Snapshot Debugger hizmetine yüklenmesi, kaynak (hem CPU hem de g/ç) için uygulamanızla rekabet edecek çok daha yavaş bir işlemdir. |
| tempFolder | null | Mini dökümler ve Uploader günlük dosyalarının yazılacağı klasörü belirtir. Ayarlanmamışsa, *%Temp%\dökümler* kullanılır. |
| Thresholdfori biçimlendirme | 1 | Anlık görüntü sorulmadan önce kaç Application Insights kez bir özel durum görmeniz gerekir. |
| uploaderProxy | null | Snapshot Uploader işleminde kullanılan proxy sunucusunu geçersiz kılar. Uygulamanız bir ara sunucu üzerinden İnternet 'e bağlanıyorsa bu ayarı kullanmanız gerekebilir. Snapshot Collector uygulamanızın sürecinde çalışır ve aynı ara sunucu ayarlarını kullanacaktır. Ancak, anlık görüntü yükleyicisi ayrı bir işlem olarak çalışır ve proxy sunucusunu el ile yapılandırmanız gerekebilir. Bu değer null ise Snapshot Collector, sistem .net. WebRequest. DefaultWebProxy ' yi inceleyerek ve değeri Snapshot Uploader ' e geçirerek proxy 'nin adresini otomatik olarak yeniden dener. Bu değer null değilse, oto algılaması kullanılmaz ve burada belirtilen proxy sunucusu, anlık görüntü yükleyicisi 'nde kullanılacaktır. |

## <a name="blobs"></a>larını

Yapılandırma ayarları, [Depolama Blobu Tetikleyicileri ve bağlamaları](functions-bindings-storage-blob.md#hostjson-settings)bölümünde bulunabilir.  

## <a name="cosmosdb"></a>cosmosDb

Yapılandırma ayarı, [Cosmos DB Tetikleyiciler ve bağlamalarda](functions-bindings-cosmosdb-v2-output.md#host-json)bulunabilir.

## <a name="customhandler"></a>customHandler

Özel işleyicinin yapılandırma ayarları. Daha fazla bilgi için bkz. [Azure işlevleri özel işleyiciler](functions-custom-handlers.md#configuration).

```json
"customHandler": {
  "description": {
    "defaultExecutablePath": "server",
    "workingDirectory": "handler",
    "arguments": [ "--port", "%FUNCTIONS_CUSTOMHANDLER_PORT%" ]
  },
  "enableForwardingHttpRequest": false
}
```

|Özellik | Varsayılan | Description |
| --------- | --------- | --------- |
| defaultExecutablePath | yok | Özel işleyici işlemi olarak başlatılacak yürütülebilir dosya. Özel işleyiciler kullanılırken ve değeri uygulama köküne göreli olduğunda, bu gerekli bir ayardır. |
| workingDirectory | *işlev uygulama kökü* | Özel işleyici işleminin başlatılacağı çalışma dizini. Bu, isteğe bağlı bir ayardır ve değeri uygulama köküne göre değişir. |
| değişkenlerinden | yok | Özel işleyici işlemine geçirilecek komut satırı bağımsız değişkenlerinin dizisi. |
| enableForwardingHttpRequest | yanlış | Ayarlanırsa, yalnızca bir HTTP tetikleyicisinden ve HTTP çıktısından oluşan tüm işlevler özel işleyici [isteği yükü](functions-custom-handlers.md#request-payload)yerıne özgün http isteğini iletilir. |

## <a name="durabletask"></a>durableTask

Yapılandırma ayarı, [dayanıklı işlevler bağlamalarında](durable/durable-functions-bindings.md#host-json)bulunabilir.

## <a name="eventhub"></a>eventHub

Yapılandırma ayarları, [Olay Hub 'ı Tetikleyicileri ve bağlamaları](functions-bindings-event-hubs.md#host-json)' nda bulunabilir. 

## <a name="extensions"></a>uzantılardan

[Http](#http) ve [eventHub](#eventhub)gibi bağlamaya özgü tüm ayarları içeren bir nesne döndüren özellik.

## <a name="extensionbundle"></a>Extensiondemeti 

Uzantı paketleri, işlev uygulamanıza uygun bir Işlev kümesi bağlama uzantısı eklemenize olanak sağlar. Daha fazla bilgi için bkz. [yerel geliştirme Için uzantı paketleri](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a> işlevleri

İş konağının çalıştığı işlevlerin listesi. Boş bir dizi tüm işlevleri Çalıştır anlamına gelir. Yalnızca [yerel olarak çalışırken](functions-run-local.md)kullanılmak üzere tasarlanmıştır. Azure 'daki işlev uygulamaları ' nda, bu ayarı kullanmak yerine belirli işlevleri devre dışı bırakmak için [Azure işlevlerinde işlevleri devre dışı](disable-function.md) bırakma bölümündeki adımları izlemeniz gerekir.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Tüm işlevler için zaman aşımı süresini gösterir. TimeSpan dize biçimini izler. 

| Plan türü | Varsayılan (min) | Maksimum (min) |
| -- | -- | -- |
| Tüketim | 5 | 10 |
| Premium<sup>1</sup> | 30 | -1 (sınırsız)<sup>2</sup> |
| Adanmış (App Service) | 30 | -1 (sınırsız)<sup>2</sup> |

<sup>1</sup> Premium plan yürütmesi yalnızca 60 dakika boyunca garanti edilir ancak teknik olarak sınırsız olur.   
<sup>2</sup> değeri `-1` , sınırsız yürütmeyi gösterir, ancak sabit bir üst sınırı korumak önerilir.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[Konak sistem durumu izleyicisinin](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)yapılandırma ayarları.

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Özellik  |Varsayılan | Description |
|---------|---------|---------| 
|enabled|true|Özelliğin etkinleştirilip etkinleştirilmeyeceğini belirtir. | 
|Healthcheckınterval|10 saniye|Düzenli arka plan sistem durumu denetimleri arasındaki zaman aralığı. | 
|healthCheckWindow|2 dakika|Ayarla birlikte kullanılan bir kayan zaman penceresi `healthCheckThreshold` .| 
|healthCheckThreshold|6|Konak geri dönüşüm başlatılmadan önce sistem durumu denetiminin başarısız olması için en fazla sayı.| 
|Onay eşiği|0,80|Performans sayacının sağlıksız olduğu kabul edilecek eşik.| 

## <a name="http"></a>http

Yapılandırma ayarları, [http Tetikleyicileri ve bağlamaları](functions-bindings-http-webhook-output.md#hostjson-settings)içinde bulunabilir.

## <a name="logging"></a>günlüğe kaydetme

Application Insights dahil olmak üzere, işlev uygulamasının günlük davranışlarını denetler.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Özellik  |Varsayılan | Description |
|---------|---------|---------|
|fileLoggingMode|yalnızca Debug|Hangi dosya günlüğü düzeyinin etkin olduğunu tanımlar.  Seçenekler `never` , `always` , `debugOnly` . |
|logLevel|yok|Uygulamadaki işlevler için günlük kategorisi filtrelemeyi tanımlayan nesne. Bu ayar, belirli işlevler için günlüğü filtrelemenizi sağlar. Daha fazla bilgi için bkz. [günlük düzeylerini yapılandırma](configure-monitoring.md#configure-log-levels). |
|console|yok| [Konsol](#console) günlüğü ayarı. |
|ApplicationInsights|yok| [ApplicationInsights](#applicationinsights) ayarı. |

## <a name="console"></a>console

Bu ayar [günlüğe kaydetme](#logging)işleminin bir alt öğesidir. Hata ayıklama modunda olmadığında konsol günlüğünü denetler.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Özellik  |Varsayılan | Description |
|---------|---------|---------| 
|IsEnabled|yanlış|Konsol günlüğünü etkinleştir veya devre dışı bırakır.| 

## <a name="manageddependency"></a>managedDependency bağımlılığı

Yönetilen bağımlılık Şu anda yalnızca PowerShell tabanlı işlevlerde desteklenen bir özelliktir. Bağımlılıkların hizmet tarafından otomatik olarak yönetilmesine olanak sağlar. `enabled`Özelliği olarak ayarlandığında `true` `requirements.psd1` Dosya işlenir. Tüm küçük sürümler bırakıldığında bağımlılıklar güncelleştirilir. Daha fazla bilgi için bkz. PowerShell makalesinde [yönetilen bağımlılık](functions-reference-powershell.md#dependency-management) .

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>klarında

Yapılandırma ayarları, [depolama kuyruğu Tetikleyicileri ve bağlamaları](functions-bindings-storage-queue.md#host-json)bölümünde bulunabilir.  

## <a name="retry"></a>retry

Uygulamadaki tüm yürütmeler için [yeniden deneme ilkesi](./functions-bindings-error-pages.md#retry-policies-preview) seçeneklerini denetler.

```json
{
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 2,
        "delayInterval": "00:00:03"  
    }
}
```

|Özellik  |Varsayılan | Description |
|---------|---------|---------| 
|inize|null|Gereklidir. Kullanılacak yeniden deneme stratejisi. Geçerli değerler veya ' dir `fixedDelay` `exponentialBackoff` .|
|maxRetryCount|null|Gereklidir. İşlev yürütmesi başına izin verilen en fazla yeniden deneme sayısı. `-1` süresiz olarak yeniden deneneceği anlamına gelir.|
|Delayınterval|null|Bir stratejiyle denemeler arasında kullanılan gecikme `fixedDelay` .|
|MinimumInterval|null|Strateji kullanılırken en düşük yeniden deneme gecikmesi `exponentialBackoff` .|
|Maximumınterval|null|Strateji kullanılırken en fazla yeniden deneme gecikmesi `exponentialBackoff` .| 

## <a name="sendgrid"></a>sendGrid

Yapılandırma ayarı, [SendGrid Tetikleyicileri ve bağlamaları](functions-bindings-sendgrid.md#host-json)içinde bulunabilir.

## <a name="servicebus"></a>serviceBus

Yapılandırma ayarı, [Service Bus Tetikleyiciler ve bağlamalarda](functions-bindings-service-bus-output.md#host-json)bulunabilir.

## <a name="singleton"></a>adet

Tek kilit davranışı için yapılandırma ayarları. Daha fazla bilgi için bkz. [Singleton desteği hakkında GitHub sorunu](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Özellik  |Varsayılan | Description |
|---------|---------|---------| 
|Kilit dönemi|00:00:15|İşlev düzeyi kilitlerinin alındığı dönem için. Kilitleri otomatik yenileme.| 
|listenerLockPeriod|00:01:00|Dinleyici kilitlerinin alındığı dönem.| 
|Listenerlockrecoverypollingınterval|00:01:00|Başlangıçta dinleyici kilidi alınamadığından, dinleyici kilidi kurtarma için kullanılan zaman aralığı.| 
|Locktanışılationtimeout|00:01:00|Çalışma zamanının kilit edinmeye çalışacak en uzun süre.| 
|Locktanışmalationpollingınterval|yok|Kilit alma denemeleri arasındaki Aralık.| 

## <a name="version"></a>sürüm

Bu değer, host.jsşema sürümünü gösterir. Sürüm dizesi, `"version": "2.0"` v2 çalışma zamanını veya sonraki bir sürümünü hedefleyen bir işlev uygulaması için gereklidir. V2 ve v3 arasındaki şema değişikliklerinde host.jsyok.

## <a name="watchdirectories"></a>watchDirectories

Değişiklikler için izlenmesi gereken bir [paylaşılan kod dizinleri](functions-reference-csharp.md#watched-directories) kümesi.  Bu dizinlerdeki kod değiştirildiğinde, değişikliklerin işlevleriniz tarafından çekilmesini sağlar.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="watchfiles"></a>watchFiles

Uygulamanızın yeniden başlatılmasını gerektiren değişiklikler için izlenen bir veya daha fazla dosya adı dizisi.  Bu, bu dosyalardaki kod değiştirildiğinde güncelleştirmelerin işlevleriniz tarafından çekilme garantisi sağlar.

```json
{
    "watchFiles": [ "myFile.txt" ]
}
```

## <a name="override-hostjson-values"></a>Değerler üzerinde host.jsgeçersiz kıl

Belirli bir ortam için dosyadaki bir host.jsbelirli ayarları yapılandırmak veya değiştirmek istediğiniz örnekler olabilir. Bu durumda, dosyanın kendisinde host.jsdeğiştiremezsiniz.  Değerler üzerinde belirli host.jsgeçersiz kılabilirsiniz ve bir uygulama ayarı olarak eşdeğer bir değer oluşturabilirsiniz. Çalışma zamanı, biçimde bir uygulama ayarı bulduğunda `AzureFunctionsJobHost__path__to__setting` , JSON içinde bulunan ayarı üzerindeki eşdeğer host.jsgeçersiz kılar `path.to.setting` . Bir uygulama ayarı olarak ifade edildiğinde, `.` JSON hiyerarşisini göstermek için kullanılan nokta () bir çift alt çizgi () ile değiştirilmiştir `__` . 

Örneğin, yerel olarak çalışırken Application Insight örneklemesi devre dışı bırakmak istediğinizi varsayalım. Dosyayı Application Insights devre dışı bırakmak için yerel host.jsdeğiştirdiyseniz, bu değişiklik dağıtım sırasında üretim uygulamanıza gönderilir. Bunu yapmanın daha güvenli yolu, bunun yerine dosyada olduğu gibi bir uygulama ayarı oluşturmaktır `"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"` `local.settings.json` . Bunu `local.settings.json` , yayımlanmadığı aşağıdaki dosyada görebilirsiniz:

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dosyadaki host.jsgüncelleştirmeyi öğrenin](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Ortam değişkenlerinde genel ayarları gör](functions-app-settings.md)

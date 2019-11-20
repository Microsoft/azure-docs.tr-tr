---
title: Azure Işlevleri 1. x için Host. JSON başvurusu
description: V1 çalışma zamanına sahip Azure Işlevleri Host. JSON dosyası için başvuru belgeleri.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 614e02e4ba2599154cd3308d6a4fe222b6f63d3d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576140"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Azure Işlevleri 1. x için Host. JSON başvurusu

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Azure Işlevleri çalışma zamanının sürümünü seçin: "]
> * [Sürüm 1](functions-host-json-v1.md)
> * [Sürüm 2](functions-host-json.md)

*Host. JSON* meta veri dosyası, bir işlev uygulaması için tüm işlevleri etkileyen genel yapılandırma seçeneklerini içerir. Bu makalede v1 çalışma zamanı için kullanılabilen ayarlar listelenir. JSON şeması http://json.schemastore.org/host.

> [!NOTE]
> Bu makale, Azure Işlevleri 1. x içindir.  2\. x Işlevleri içindeki Host. JSON başvurusu için bkz. [Azure işlevleri için Host. JSON başvurusu 2. x](functions-host-json.md).

Diğer işlev uygulaması yapılandırma seçenekleri [uygulama ayarlarınızda](functions-app-settings.md)yönetilir.

Bazı Host. JSON ayarları yalnızca [yerel. Settings. JSON](functions-run-local.md#local-settings-file) dosyasında yerel olarak çalıştırılırken kullanılır.

## <a name="sample-hostjson-file"></a>Örnek Host. JSON dosyası

Aşağıdaki örnek *Host. JSON* dosyaları tüm olası seçenekleri belirtti.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
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
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

Bu makalenin aşağıdaki bölümlerinde her üst düzey özellik açıklanmaktadır. Aksi belirtilmedikçe tümü isteğe bağlıdır.

## <a name="aggregator"></a>'yı

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>ApplicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

[Azure Cosmos DB tetikleyicisi ve bağlamaları](functions-bindings-cosmosdb.md)için yapılandırma ayarları.

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------|
|GatewayMode|Ağ geçidi|Azure Cosmos DB hizmetine bağlanırken işlev tarafından kullanılan bağlantı modu. Seçenekler `Direct` ve `Gateway`|
|Protokol|'Dir|Azure Cosmos DB hizmetine bağlantı sırasında işlev tarafından kullanılan bağlantı protokolü.  [Her iki modun açıklaması için buraya](../cosmos-db/performance-tips.md#networking) okuyun|
|leasePrefix|yok|Bir uygulamadaki tüm işlevler genelinde kullanılacak kira öneki.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

[Olay Hub 'ı Tetikleyicileri ve bağlamaları](functions-bindings-event-hubs.md)için yapılandırma ayarları.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>işlevler

İş konağının çalıştığı işlevlerin listesi. Boş bir dizi tüm işlevleri Çalıştır anlamına gelir. Yalnızca [yerel olarak çalışırken](functions-run-local.md)kullanılmak üzere tasarlanmıştır. Azure 'daki işlev uygulamaları ' nda, bu ayarı kullanmak yerine belirli işlevleri devre dışı bırakmak için [Azure işlevlerinde işlevleri devre dışı](disable-function.md) bırakma bölümündeki adımları izlemeniz gerekir.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Tüm işlevler için zaman aşımı süresini gösterir. Sunucusuz tüketim planında geçerli Aralık 1 saniye ila 10 dakika ve varsayılan değer 5 dakikadır. Bir App Service planında, genel bir sınır yoktur ve varsayılan değer, zaman aşımı olmadığını gösteren _null_olur.

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

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
|enabled|doğru|Özelliğin etkinleştirilip etkinleştirilmeyeceğini belirtir. | 
|Healthcheckınterval|10 saniye|Düzenli arka plan sistem durumu denetimleri arasındaki zaman aralığı. | 
|healthCheckWindow|2 dakika|`healthCheckThreshold` ayarıyla birlikte kullanılan bir kayan zaman penceresi.| 
|healthCheckThreshold|6|Konak geri dönüşüm başlatılmadan önce sistem durumu denetiminin başarısız olması için en fazla sayı.| 
|Onay eşiği|0,80|Performans sayacının sağlıksız olduğu kabul edilecek eşik.| 

## <a name="http"></a>http

[Http Tetikleyicileri ve bağlamaları](functions-bindings-http-webhook.md)için yapılandırma ayarları.

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|Bu ayar etkinleştirildiğinde, istek işleme işlem hattının bağlantılar/iş parçacıkları/işlemler/bellek/CPU/vb gibi sistem performans sayaçlarını düzenli olarak denetlemesini sağlar. bu sayaçlardan herhangi biri yerleşik yüksek bir eşiğin üzerindeyse (%80%), istek şöyle olur sayaç (ler) normal düzeylerine dönene kadar 429 "çok meşgul" yanıtıyla reddedildi.|
|maxConcurrentRequests|Sınırsız (`-1`)|Paralel olarak yürütülecek http işlevlerinin maksimum sayısı. Bu, kaynak kullanımının yönetilmesine yardımcı olabilecek eşzamanlılık denetlemenize olanak tanır. Örneğin, eşzamanlılık çok yüksek olduğunda sorunlara yol açacağından, çok fazla sistem kaynağı (bellek/CPU/yuva) kullanan bir http işleviniz olabilir. Ya da bir üçüncü taraf hizmetine giden istekleri yapan bir işleviniz olabilir ve bu çağrıların hız sınırlı olması gerekir. Bu durumlarda, burada bir kısıtlama uygulanması yardımcı olabilir.|
|maxOutstandingRequests|Sınırsız (`-1`)|Belirli bir zamanda tutulan bekleyen istek sayısı üst sınırı. Bu sınır, kuyruğa alınmış ancak yürütmeyi başlatmayan isteklerin yanı sıra devam eden yürütmeler içerir. Bu sınırın üzerindeki tüm gelen istekler, 429 "çok meşgul" yanıtıyla reddedilir. Bu, çağıranların zamana dayalı yeniden deneme stratejileri kullanmasına izin verir ve ayrıca en fazla istek gecikme sürelerini denetlemenize yardımcı olur. Bu, yalnızca betik ana bilgisayar yürütme yolu içinde oluşan kuyruğu denetler. ASP.NET istek kuyruğu gibi diğer kuyruklar da etkin olmaya devam eder ve bu ayardan etkilenmez.|
|routePrefix|API|Tüm yollar için geçerli olan rota öneki. Varsayılan ön eki kaldırmak için boş bir dize kullanın. |

## <a name="id"></a>id

Bir iş konağının benzersiz KIMLIĞI. Kesik çizgileri kaldırılmış küçük bir harf olabilir. Yerel olarak çalıştırılırken gereklidir. Azure 'da çalışırken, bir KIMLIK değeri ayarlamanıza önerilir. `id` atlandığında Azure 'da otomatik olarak bir KIMLIK oluşturulur. 

Birden çok işlev uygulamasında bir depolama hesabı paylaşırsanız, her bir işlev uygulamasının farklı bir `id`sahip olduğundan emin olun. `id` özelliğini atlayabilir veya her bir işlev uygulamasının `id` farklı bir değere el ile ayarlayabilirsiniz. Zamanlayıcı tetikleyicisi, bir işlev uygulaması birden çok örneğe ölçeklenirken yalnızca bir zamanlayıcı örneği olacağını sağlamak için bir depolama kilidi kullanır. İki işlev uygulaması aynı `id` paylaşıyorsa ve her biri bir Zamanlayıcı tetikleyicisi kullanıyorsa, yalnızca bir Zamanlayıcı çalışır.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Medi

Bir [ILogger nesnesi](functions-monitoring.md#write-logs-in-c-functions) veya [Context. log](functions-monitoring.md#write-logs-in-javascript-functions)tarafından yazılmış günlükler için filtrelemeyi denetler.

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
|categoryFilter|yok|Kategoriye göre filtrelemeyi belirtir| 
|defaultLevel|Bilgi|`categoryLevels` dizide belirtilmeyen hiçbir kategori için, günlükleri bu düzeyde ve yukarıdaki Application Insights gönderin.| 
|categoryLevels|yok|Her kategori için Application Insights gönderilmek üzere en düşük günlük düzeyini belirten kategori dizisi. Burada belirtilen kategori, aynı değerle başlayan tüm kategorileri denetler ve daha uzun değerler öncelik kazanır. Önceki örnek *Host. JSON* dosyasında, "Host. toplayıcısı" ile başlayan tüm kategoriler `Information` düzeyinde günlüğe kaydedilir. "Host. yürütücü" gibi "Host" ile başlayan tüm diğer kategoriler `Error` düzeyinde oturum açın.| 

## <a name="queues"></a>klarında

[Depolama kuyruğu Tetikleyicileri ve bağlamaları](functions-bindings-storage-queue.md)için yapılandırma ayarları.

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
|Maxpollingınterval|60000|Sıra yoklamaları arasındaki milisaniye olarak en fazla Aralık.| 
|visibilityTimeout|0|Bir ileti işlenirken yeniden denemeler arasındaki zaman aralığı başarısız olur.| 
|batchSize|16|Işlevlerin çalışma zamanının aynı anda ve işlemleri paralel olarak aldığı sıra iletilerinin sayısı. İşlenen sayı `newBatchThreshold`dolduğunda, çalışma zamanı başka bir toplu iş alır ve bu iletileri işlemeye başlar. Bu nedenle, işlev başına işlenen en fazla eşzamanlı ileti sayısı `batchSize` ve `newBatchThreshold`. Bu sınır, kuyruğa tetiklenen her bir işlev için ayrı olarak uygulanır. <br><br>Bir kuyrukta alınan iletiler için paralel yürütmeyi önlemek istiyorsanız, `batchSize` 1 olarak ayarlayabilirsiniz. Ancak, bu ayar yalnızca işlev uygulamanız tek bir sanal makinede (VM) çalıştığı sürece eşzamanlılık ortadan kaldırır. İşlev uygulaması birden çok VM 'ye ölçekleniyorsa, her VM, her bir kuyruk tetiklenen işlevin bir örneğini çalıştırabilir.<br><br>En büyük `batchSize` 32 ' dir. | 
|maxDequeueCount|5|Zarar sırasına taşımadan önce bir iletiyi işlemeyi deneme sayısı.| 
|newBatchThreshold|batchSize/2|Aynı anda işlenen ileti sayısı bu sayıya indiğinde, çalışma zamanı başka bir toplu işi alır.| 

## <a name="sendgrid"></a>SendGrid

[Sendgrind çıkış bağlamasının](functions-bindings-sendgrid.md) yapılandırma ayarı

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
|Kaynak|yok|Tüm işlevler genelinde gönderenin e-posta adresi.| 

## <a name="servicebus"></a>serviceBus

[Service Bus Tetikleyicileri ve bağlamaları](functions-bindings-service-bus.md)yapılandırma ayarı.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
|Maxconcurrentçağrıları|16|İleti göndericisinin başlatması gereken geri çağrıya yönelik eşzamanlı çağrı sayısı üst sınırı. Varsayılan olarak, Işlevler çalışma zamanı birden çok iletiyi eşzamanlı olarak işler. Çalışma zamanını aynı anda yalnızca tek bir kuyruğu veya konu iletisini işleyecek şekilde yönlendirmek için `maxConcurrentCalls` ayarlayın. | 
|prefetchCount|yok|Temel alınan MessageReceiver tarafından kullanılacak varsayılan PrefetchCount.| 
|autoRenewTimeout|00:05:00|İleti kilidinin otomatik olarak yenilenebileceği en uzun süre.| 

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

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
|Kilit dönemi|00:00:15|İşlev düzeyi kilitlerinin alındığı dönem için. Kilitleri otomatik yenileme.| 
|listenerLockPeriod|00:01:00|Dinleyici kilitlerinin alındığı dönem.| 
|Listenerlockrecoverypollingınterval|00:01:00|Başlangıçta dinleyici kilidi alınamadığından, dinleyici kilidi kurtarma için kullanılan zaman aralığı.| 
|Locktanışılationtimeout|00:01:00|Çalışma zamanının kilit edinmeye çalışacak en uzun süre.| 
|Locktanışmalationpollingınterval|yok|Kilit alma denemeleri arasındaki Aralık.| 

## <a name="tracing"></a>izleniyor

*Sürüm 1. x*

`TraceWriter` nesnesi kullanarak oluşturduğunuz Günlükler için yapılandırma ayarları. Bkz [ C# . Logging](functions-reference-csharp.md#logging) ve [Node. js günlüğü](functions-reference-node.md#writing-trace-output-to-the-console).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------| 
|consoleLevel|Bilgisine|Konsol günlüğü için izleme düzeyi. Seçenekler şunlardır: `off`, `error`, `warning`, `info`ve `verbose`.|
|fileLoggingMode|yalnızca Debug|Dosya günlüğü için izleme düzeyi. Seçenekler `never`, `always``debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Değişiklikler için izlenmesi gereken bir [paylaşılan kod dizinleri](functions-reference-csharp.md#watched-directories) kümesi.  Bu dizinlerdeki kod değiştirildiğinde, değişikliklerin işlevleriniz tarafından çekilmesini sağlar.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Host. json dosyasını güncelleştirme hakkında bilgi edinin](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Ortam değişkenlerinde genel ayarları gör](functions-app-settings.md)

---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356202"
---
Bir Azure Işlevlerinde oluşan hatalar aşağıdaki kaynaklardan herhangi birinden gelebilir:

- Yerleşik Azure Işlevleri [Tetikleyicileri ve bağlamaları](..\articles\azure-functions\functions-triggers-bindings.md) kullanımı
- Temel alınan Azure Hizmetleri API 'Leri çağrıları
- REST uç noktalarına çağrılar
- İstemci kitaplıkları, paketler veya üçüncü taraf API 'Leri çağrıları

Aşağıdaki iyi hata işleme uygulamaları, veri kaybını veya eksik iletileri kaybetmemek için önemlidir. Önerilen hata işleme uygulamaları aşağıdaki eylemleri içerir:

- [Application Insights’ı Etkinleştir](../articles/azure-functions/functions-monitoring.md)
- [Yapılandırılmış hata işlemeyi kullan](#use-structured-error-handling)
- [Idempotlik tasarımı](../articles/azure-functions/functions-idempotent.md)
- [Yeniden deneme Ilkelerini Uygula](#retry-policies-preview) (uygun olduğunda)

### <a name="use-structured-error-handling"></a>Yapılandırılmış hata işlemeyi kullan

Yakalama ve günlüğe kaydetme hataları uygulamanızın durumunu izlemek için kritik öneme sahiptir. Herhangi bir işlev kodunun en üst düzeyinde bir try/catch bloğu bulunmalıdır. Catch bloğunda, hataları yakalayabilir ve günlüğe kaydedebilirsiniz.

## <a name="retry-policies-preview"></a>Yeniden deneme ilkeleri (Önizleme)

Bir yeniden deneme ilkesi, işlev uygulamanızdaki herhangi bir tetikleyici türü için herhangi bir işlevde tanımlanabilir.  Yeniden deneme ilkesi, başarılı bir yürütme ya da en fazla yeniden deneme sayısı gerçekleşene kadar bir işlevi yeniden yürütür.  Yeniden deneme ilkeleri, bir uygulamadaki tüm işlevler için veya tek tek işlevlerde tanımlanabilir.  Varsayılan olarak, bir işlev uygulaması iletileri yeniden denemez ( [tetikleyici kaynağında yeniden deneme ilkesine sahip olan belirli tetikleyicilerden](#using-retry-support-on-top-of-trigger-resilience)).  Bir yürütme yakalanamayan özel durum ile sonuçlanamadığında yeniden deneme ilkesi değerlendirilir.  En iyi uygulama olarak, kodunuzda tüm özel durumları yakalamalı ve yeniden denemeye neden olacak hataları yeniden oluşturmanız gerekir.  Event Hubs ve Azure Cosmos DB kontrol noktaları, yürütme için yeniden deneme ilkesi tamamlanana kadar yazılmaz, bu bölümde ilerleyerek geçerli toplu işlem tamamlanana kadar duraklatılır.

### <a name="retry-policy-options"></a>Yeniden deneme ilkesi seçenekleri

Yeniden deneme ilkesi tanımlamak için aşağıdaki seçenekler kullanılabilir.

**En fazla yeniden deneme sayısı** , bir yürütmenin son hatadan önce yeniden denenme sayısı üst sınırıdır. Bir değeri `-1` sonsuza kadar yeniden deneneceği anlamına gelir. Geçerli yeniden deneme sayısı, örnek bellekte depolanır. Bir örnek yeniden deneme girişimleri arasında bir hata olabilir.  Yeniden deneme ilkesi sırasında bir örnek başarısız olduğunda, yeniden deneme sayısı kaybedilir.  Örnek arızalar olduğunda, Event Hubs, Azure Cosmos DB ve kuyruk depolaması gibi Tetikleyiciler işlemeyi sürdürebilir ve toplu işi yeni bir örnekte yeniden deneyebilir ve yeniden deneme sayısı sıfıra sıfırlanır.  HTTP ve zamanlayıcı gibi diğer Tetikleyiciler, yeni bir örnek üzerinde sürdürülmez.  Bu, en fazla yeniden deneme sayısı en iyi çaba olduğu anlamına gelir ve bazı nadir durumlarda yürütme en yüksek süreden daha fazla yeniden denenebileceği gibi, ya da HTTP gibi Tetikleyiciler ve Zamanlayıcı en büyük değerden daha az yeniden denenecektir.

**Yeniden deneme stratejisi** , yeniden denemelerin nasıl davranacağını denetler.  Aşağıda iki desteklenen yeniden deneme seçeneği verilmiştir:

| Seçenek | Açıklama|
|---|---|
|**`fixedDelay`**| Her yeniden deneme arasında belirli bir süre geçmesini izin verilir,|
| **`exponentialBackoff`**| İlk yeniden deneme en kısa gecikme süresi için bekler. Sonraki yeniden denemeler sırasında, en fazla gecikme süresine ulaşılana kadar, zaman her yeniden deneme için ilk süreye katlanarak eklenir.  Üstel geri alma, yüksek performanslı senaryolarda yeniden denemeler yapmak için gecikmelere bazı küçük rastgele seçim ekler.|

#### <a name="app-level-configuration"></a>Uygulama düzeyi yapılandırma

Bir yeniden deneme ilkesi, bir uygulamadaki tüm işlevler için [ `host.json` dosyayı kullanarak](../articles/azure-functions/functions-host-json.md#retry)tanımlanabilir. 

#### <a name="function-level-configuration"></a>İşlev düzeyi yapılandırma

Bir yeniden deneme ilkesi, belirli bir işlev için tanımlanabilir.  İşleve özgü yapılandırma, uygulama düzeyinde yapılandırma üzerinden önceliklidir.

#### <a name="fixed-delay-retry"></a>Sabit gecikme yeniden denemesi

# <a name="c"></a>[C#](#tab/csharp)

Yeniden denemeler için NuGet paketi gerekiyor [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

İşte *function.js* dosyadaki yeniden deneme ilkesi:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

İşte *function.js* dosyadaki yeniden deneme ilkesi:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

İşte *function.js* dosyadaki yeniden deneme ilkesi:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

İşte *function.js* dosyadaki yeniden deneme ilkesi:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

İşte *function.js* dosyadaki yeniden deneme ilkesi:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Üstel geri alma yeniden deneme

# <a name="c"></a>[C#](#tab/csharp)

Yeniden denemeler için NuGet paketi gerekiyor [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

İşte *function.js* dosyadaki yeniden deneme ilkesi:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

İşte *function.js* dosyadaki yeniden deneme ilkesi:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

İşte *function.js* dosyadaki yeniden deneme ilkesi:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

İşte *function.js* dosyadaki yeniden deneme ilkesi:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

İşte *function.js* dosyadaki yeniden deneme ilkesi:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.jsözelliği  |Öznitelik özelliği | Description |
|---------|---------|---------| 
|inize|yok|Gereklidir. Kullanılacak yeniden deneme stratejisi. Geçerli değerler veya ' dir `fixedDelay` `exponentialBackoff` .|
|maxRetryCount|yok|Gereklidir. İşlev yürütmesi başına izin verilen en fazla yeniden deneme sayısı. `-1` süresiz olarak yeniden deneneceği anlamına gelir.|
|Delayınterval|yok|Strateji kullanılırken yeniden denemeler arasında kullanılacak gecikme `fixedDelay` .|
|MinimumInterval|yok|Strateji kullanılırken en düşük yeniden deneme gecikmesi `exponentialBackoff` .|
|Maximumınterval|yok|Strateji kullanılırken en fazla yeniden deneme gecikmesi `exponentialBackoff` .| 

### <a name="retry-limitations-during-preview"></a>Önizleme sırasında yeniden deneme sınırlamaları

- .NET projeleri için [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 bir sürümünü el ile çekmeniz gerekebilir.
- Tüketim planında, bir kuyruktaki son iletiler yeniden denenirken uygulama sıfıra ölçeklenebiliyor olabilir.
- Tüketim planında, uygulama yeniden denemeler gerçekleştirilirken ölçeği azaltılabilir.  En iyi sonuçlar için <= 00:01:00 ve <= 5 yeniden deneme aralığı seçin.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>Tetikleyici esnekliği üzerinde yeniden deneme desteğini kullanma

İşlev uygulaması yeniden deneme ilkesi, tetikleyicinin sağladığı herhangi bir yeniden denemeden veya dayanıklılığı birbirinden bağımsızdır.  İşlev yeniden deneme ilkesi yalnızca bir tetikleyicinin dayanıklı yeniden denenme üzerinde katman olacak.  Örneğin, Azure Service Bus kullanılıyorsa varsayılan kuyruklarda ileti teslim sayısı 10 ' dur.  Varsayılan teslim sayısı, bir kuyruk iletisi 10 ' un denendiğinde Service Bus, iletinin atılacak ileti sayısını gösterir.  Service Bus tetikleyicisi olan bir işlev için yeniden deneme ilkesi tanımlayabilirsiniz, ancak yeniden denemeler Service Bus teslim denemelerinin üzerine katman kullanacaktır.  

Örneğin, varsayılan Service Bus teslim sayısını 10 kullandıysanız ve 5. bir işlev yeniden deneme ilkesi tanımlıysa.  İleti önce sıradan çıkar, Service Bus teslim hesabını 1 ' e artırdı.  Her yürütme başarısız olursa, beş aynı iletiyi tetikleme denemesinden sonra bu ileti terk edildi olarak işaretlenir.  İletiyi hemen yeniden kuyruğa alma Service Bus, işlevi tetikler ve teslim sayısını 2 olarak artırır.  Son olarak, 50 nihai girişimden sonra (10 hizmet veri yolu teslimi * teslim başına beş işlev yeniden denemesi), ileti bırakıldı ve Service Bus 'da atılacak bir harf tetiklenebilir.

> [!WARNING]
> Service Bus kuyrukları gibi bir tetikleyicinin teslimat sayısının 1 olarak ayarlanması önerilmez, yani ileti tek bir işlev yeniden deneme döngüsünden hemen sonra atılacak.  Bunun nedeni, tetikleyicilerin yeniden denemeler ile dayanıklılık sağladığından, işlev yeniden deneme ilkesi en iyi çabadır ve istenen toplam yeniden deneme sayısından daha az sonuç verebilir.

### <a name="triggers-with-additional-resiliency-or-retries"></a>Ek dayanıklılık veya yeniden denemeler ile Tetikleyiciler

Aşağıdaki Tetikleyiciler tetikleyici kaynağında yeniden denemeleri destekler:

* [Azure Blob Depolama](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Kuyruk Depolama](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (kuyruk/konu)](../articles/azure-functions/functions-bindings-service-bus.md)

Varsayılan olarak, çoğu zaman istekleri en fazla beş kez yeniden dener. Beşinci yeniden denemeden sonra, her iki Azure kuyruk depolaması da bir [Poison kuyruğuna](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)ileti yazar.  Varsayılan Service Bus kuyruğu ve konu ilkesi, 10 denemeden sonra [atılacak ileti kuyruğuna](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) bir ileti yazar.

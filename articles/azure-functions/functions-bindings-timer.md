---
title: Azure Işlevleri için süreölçer tetikleyicisi
description: Azure Işlevleri 'nde Zamanlayıcı Tetikleyicileri kullanmayı anlayın.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f99f9d240a0a9220d3b7f57cddd0a4f8ba6b6101
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596000"
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Işlevleri için süreölçer tetikleyicisi

Bu makalede, Azure Işlevlerinde süreölçer tetikleyicilerle nasıl çalışılacağı açıklanmaktadır. Zamanlayıcı tetikleyicisi bir zamanlamaya göre bir işlevi çalıştırmanızı sağlar.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Zamanlayıcı ile tetiklenen bir işlevi el ile çalıştırma hakkında daha fazla bilgi için bkz. [http ile tetiklenen olmayan bir Işlevi el ile çalıştırma](./functions-manually-run-non-http.md).

## <a name="packages---functions-2x-and-higher"></a>Paketler-Işlevler 2. x ve üzeri

Zamanlayıcı tetikleyicisi [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet paketi, sürüm 3. x ' te sağlanır. Paketin kaynak kodu, [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub deposunda bulunur.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>Paketler-Işlevler 1. x

Zamanlayıcı tetikleyicisi [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet paketi, sürüm 2. x ' te sağlanır. Paketin kaynak kodu, [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub deposunda bulunur.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Örnek

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnek, dakikada beş ile bölünebilen bir [C# işlevini](functions-dotnet-class-library.md) gösterir (örneğin, işlev 18:57:00 ' de başlıyorsa, sonraki performans 19:00:00 ' de olur). [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)Nesnesi işlevine geçirilir.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bir *function.js* dosyadaki bir Zamanlayıcı tetikleyicisi bağlamasını ve bağlamayı kullanan bir [C# betik işlevini](functions-reference-csharp.md) gösterir. İşlev, bu işlev çağrısının kaçırılmış bir zamanlama oluşumu olup olmadığını gösteren bir günlük yazar. [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)Nesnesi işlevine geçirilir.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# betik kodu aşağıda verilmiştir:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

Aşağıdaki örnek işlev her beş dakikada bir tetiklenir ve yürütülür. `@TimerTrigger`İşlevindeki ek açıklama [cron ifadeleriyle](https://en.wikipedia.org/wiki/Cron#CRON_expression)aynı dize biçimini kullanarak zamanlamayı tanımlar.

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosyadaki bir Zamanlayıcı tetikleyicisi bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlev, bu işlev çağrısının kaçırılmış bir zamanlama oluşumu olup olmadığını gösteren bir günlük yazar. İşleve bir [Zamanlayıcı nesnesi](#usage) geçirilir.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript kodu aşağıda verilmiştir:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki örnek, [PowerShell](./functions-reference-powershell.md)'de bir Zamanlayıcı tetikleyicisi için *function.js* ve *run.ps1* dosyasının nasıl yapılandırılacağını gösterir.

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

[Timer nesnesinin](#usage) bir örneği, işlevine ilk bağımsız değişken olarak geçirilir.

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, yapılandırması *function.jsdosya üzerinde* açıklanan bir Zamanlayıcı tetikleyicisi bağlamayı kullanır. Bağlamayı kullanan gerçek [Python işlevi](functions-reference-python.md) *__init__. Kopyala* dosyasında açıklanmıştır. İşleve geçirilen nesne, [Azure. Functions. TimerRequest nesnesi](/python/api/azure-functions/azure.functions.timerrequest)türündedir. İşlev mantığı, geçerli çağrının kaçırılmış bir zamanlama oluşumunda olup olmadığını gösteren günlüklere yazar.

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Python kodu aşağıda verilmiştir:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md), [timertriggerattribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)' u kullanın.

Özniteliğin Oluşturucusu bir CRON ifadesi ya da olarak alır `TimeSpan` . `TimeSpan`Yalnızca işlev uygulaması App Service bir planda çalışıyorsa kullanabilirsiniz. `TimeSpan` Tüketim veya elastik Premium Işlevleri için desteklenmez.

Aşağıdaki örnek bir CRON ifadesini gösterir:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

`@TimerTrigger`İşlevindeki ek açıklama [cron ifadeleriyle](https://en.wikipedia.org/wiki/Cron#CRON_expression)aynı dize biçimini kullanarak zamanlamayı tanımlar.

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Öznitelikler PowerShell tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `TimerTrigger` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok | "TimerTrigger" olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**Görünüm** | yok | "In" olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | yok | İşlev kodundaki Timer nesnesini temsil eden değişkenin adı. | 
|**çizelgesini**|**ScheduleExpression**|Bir [cron ifadesi](#ncrontab-expressions) veya [TimeSpan](#timespan) değeri. `TimeSpan`Yalnızca, App Service planında çalışan bir işlev uygulaması için kullanılabilir. Zamanlama ifadesini bir uygulama ayarına yerleştirebilir ve bu özelliği **%** Şu örnekte gösterildiği gibi, işaretlere kaydırılmış uygulama ayarı adı olarak ayarlayabilirsiniz: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|İse `true` , çalışma zamanı başlatıldığında işlev çağrılır. Örneğin, çalışma zamanı, işlev uygulaması eylemsizlik nedeniyle boşta kaldıktan sonra uyandığında başlatılır. işlev değişiklikleri nedeniyle uygulama yeniden başlatıldığında ve işlev uygulaması ölçeklenirken. Bu nedenle **runOnStartup** `true` , özellikle üretimde olarak ayarlandıysa nadiren gerekir. |
|**useMonitor**|**UseMonitor**|`true` `false` Zamanlamanın izlenmesi gerekip gerekmediğini belirtmek için veya olarak ayarlayın. Zamanlamayı zamanla, işlev uygulama örnekleri yeniden başlatıldığında bile zamanlamanın doğru bir şekilde tutulmasını sağlamaya yardımcı olmak için zamanlama oluşumları devam ettirir. Açıkça ayarlanmamışsa varsayılan değer `true` 1 dakikadan büyük veya ona eşit bir yinelenme aralığına sahip zamanlamalara yöneliktir. Dakikada birden çok kez tetikleyen zamanlamalar için varsayılan olarak ' dir `false` .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> **RunOnStartup** ' nin üretimde ayarlanmasını öneririz `true` . Bu ayarın kullanılması, kodun son derece öngörülemeyen zamanlarda yürütülmesine neden olur. Bazı üretim ayarlarında, bu ek yürütmeler, tüketim planlarında barındırılan uygulamalar için önemli ölçüde daha yüksek maliyetlere neden olabilir. Örneğin, **runOnStartup** etkin olduğunda tetikleyici, işlev uygulamanız her ölçeklendirildiğinde çağrılır. **RunOnStartup** etkinleştirilmeden önce işlevlerinizin üretim davranışını tam olarak anladığınızdan emin olun.

## <a name="usage"></a>Kullanım

Bir Zamanlayıcı tetikleyicisi işlevi çağrıldığında, işleve bir Zamanlayıcı nesnesi geçirilir. Aşağıdaki JSON, Timer nesnesinin örnek bir gösterimidir.

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

`isPastDue`Özelliği, `true` geçerli işlev çağırma zamanlanmadan daha sonra olduğunda olur. Örneğin, bir işlev uygulamasının yeniden başlatılması, çağrının kaçırılmasını sağlayabilir.

## <a name="ncrontab-expressions"></a>NCRONTAB ifadeleri

Azure Işlevleri, NCRONTAB ifadelerini yorumlamak için [ncrontab](https://github.com/atifaziz/NCrontab) kitaplığını kullanır. Bir NCRONTAB ifadesi, bir CRON ifadesine benzer ve saniyeler içinde zaman duyarlığı için kullanılmak üzere ek bir altıncı alan içerir:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Her alan aşağıdaki değer türlerinden birine sahip olabilir:

|Tür  |Örnek  |Tetiklendiğinde  |
|---------|---------|---------|
|Belirli bir değer |<nobr>`0 5 * * * *`</nobr>| Her saatin dakikada 5 ' te günde bir kez |
|Tüm değerler ( `*` )|<nobr>`0 * 5 * * *`</nobr>| Saatin her dakikada, saat 5 ' ten başlayarak |
|Bir Aralık ( `-` işleç)|<nobr>`5-7 * * * * *`</nobr>| Her bir günün her bir dakikası için dakikada üç kez, 5 ila 7 |
|Bir değerler kümesi ( `,` işleç)|<nobr>`5,8,10 * * * * *`</nobr>| Her bir günün her bir dakikası için dakikada üç kez 5, 8 ve 10 saniye |
|Bir Aralık değeri ( `/` işleç)|<nobr>`0 */5 * * * *`</nobr>| Her gün 12 kez, her günün her bir saat için her 5 dakikada bir, ikinci 0 |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB örnekleri

Azure Işlevlerinde süreölçer tetikleyicisi için kullanabileceğiniz bazı NCRONTAB ifadeleri örnekleri aşağıda verilmiştir.

| Örnek            | Tetiklendiğinde                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | Beş dakikada bir            |
| `0 0 * * * *`      | her saatin üstünde bir kez      |
| `0 0 */2 * * *`    | Her iki saatte bir               |
| `0 0 9-17 * * *`   | her saat 9 ' dan 5 ' e kadar  |
| `0 30 9 * * *`     | Her gün 9:30 saat               |
| `0 30 9 * * 1-5`   | Her gün 9:30 saat           |
| `0 30 9 * Jan Mon` | Ocak 9:30 ' de her Pazartesi |

> [!NOTE]
> NCRONTAB ifadesi **altı alan** biçimi gerektirir. Altıncı alan konumu, ifadenin başına yerleştirilmiş saniyeler için bir değerdir. Azure 'da beş alan cron ifadesi desteklenmez.

### <a name="ncrontab-time-zones"></a>NCRONTAB saat dilimleri

Bir CRON ifadesindeki sayılar, zaman aralığı değil, bir saat ve tarihe başvurur. Örneğin, `hour` alandaki 5 saat, 5 saatte bir değil 5:00 ' e başvurur.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 `TimeSpan`Yalnızca, App Service planında çalışan bir işlev uygulaması için kullanılabilir.

Bir CRON ifadesinin aksine bir `TimeSpan` değer, her bir işlev çağrısı arasındaki zaman aralığını belirtir. Bir işlev, belirtilen aralıktan daha uzun çalıştıktan sonra tamamlandığında, süreölçer işlevi hemen yeniden çağırır.

Dize olarak ifade `TimeSpan` edildiğinde, biçim 24 ' `hh:mm:ss` `hh` ten küçüktür. İlk iki basamak 24 veya daha büyükse, biçim olur `dd:hh:mm` . İşte bazı örnekler:

| Örnek      | Tetiklendiğinde |
|--------------|----------------|
| "01:00:00"   | her saat     |
| "00:01:00"   | her dakika   |
| "25:00:00"   | her 25 günde bir  |
| "1,00:00:00" | Her gün      |

## <a name="scale-out"></a>Ölçeği genişletme

Bir işlev uygulaması birden çok örneğe ölçekleniyorsa, tüm örneklerde yalnızca bir Zamanlayıcı tetiklenen işlevin tek bir örneği çalıştırılır. Bekleyen bir çağrı hala çalışıyorsa yeniden tetiklemez.

## <a name="function-apps-sharing-storage"></a>İşlev uygulamaları paylaşım depolaması

App Service 'e dağıtılmamış işlev uygulamalarında depolama hesaplarını paylaşıyorsanız, her uygulamaya açıkça ana bilgisayar KIMLIĞI atamanız gerekebilir.

| İşlevler sürümü | Ayar                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x (ve üzeri)  | `AzureFunctionsWebHost__hostid` ortam değişkeni |
| 'in               | `id`*üzerindehost.js*                                  |

Tanımlayıcı değeri atlayabilir veya her bir işlev uygulamasının tanımlayıcı yapılandırmasını farklı bir değere el ile ayarlayabilirsiniz.

Zamanlayıcı tetikleyicisi, bir işlev uygulaması birden çok örneğe ölçeklenirken yalnızca bir zamanlayıcı örneği olduğundan emin olmak için bir depolama kilidi kullanır. İki işlev uygulaması aynı tanımlayıcı yapılandırmayı paylaşıyorsa ve her biri bir Zamanlayıcı tetikleyicisi kullanıyorsa, yalnızca bir Zamanlayıcı çalışır.

## <a name="retry-behavior"></a>Yeniden deneme davranışı

Sıra tetikleyicisinden farklı olarak, bir işlev başarısız olduktan sonra Zamanlayıcı tetikleyicisi yeniden denenmez. Bir işlev başarısız olduğunda, zamanlamaya bir sonraki sefer kadar bir kez çağrılmaz.

## <a name="manually-invoke-a-timer-trigger"></a>El ile bir Zamanlayıcı tetikleyicisi çağırma

Azure Işlevleri için süreölçer tetikleyicisi, işlevi el ile tetiklemek için çağrılabilecek bir HTTP Web kancası sağlar. Bu, aşağıdaki senaryolarda son derece yararlı olabilir.

* Tümleştirme testleri
* Duman testi veya ısınma etkinliğinin parçası olarak yuva takas
* Bir veritabanındaki önbelleği veya arama tablosunu hemen doldurmak için bir işlevin ilk dağıtımı

Zamanlayıcı tarafından tetiklenen bir işlevi el ile çağırma hakkındaki ayrıntılar için lütfen [http ile tetiklenen bir işlevi el ile çalıştırma](./functions-manually-run-non-http.md) bölümüne bakın.

## <a name="troubleshooting"></a>Sorun giderme

Zamanlayıcı tetikleyicisi beklendiği gibi çalışmazsa yapılacaklar hakkında daha fazla bilgi için, bkz. [Zamanlayıcı tarafından tetiklenen işlevlerle ilgili sorunları araştırma ve raporlama](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Zamanlayıcı tetikleyicisi kullanan bir hızlı başlangıca git](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

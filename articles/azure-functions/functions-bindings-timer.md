---
title: Azure Işlevleri için süreölçer tetikleyicisi
description: Azure Işlevleri 'nde Zamanlayıcı Tetikleyicileri kullanmayı anlayın.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure işlevleri, işlevler, olay işleme, dinamik işlem, sunucusuz mimari
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: azure-functions
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 439e5ab4bf943293ff4ed20ed477bc98bb683836
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299321"
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Işlevleri için süreölçer tetikleyicisi 

Bu makalede, Azure Işlevlerinde süreölçer tetikleyicilerle nasıl çalışılacağı açıklanmaktadır. Zamanlayıcı tetikleyicisi bir zamanlamaya göre bir işlevi çalıştırmanızı sağlar. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paketler-Işlevler 1. x

Zamanlayıcı tetikleyicisi [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet paketi, sürüm 2. x ' te sağlanır. Paketin kaynak kodu, [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub deposunda bulunur.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Paketler-Işlevler 2. x

Zamanlayıcı tetikleyicisi [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet paketi, sürüm 3. x ' te sağlanır. Paketin kaynak kodu, [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub deposunda bulunur.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Örnek

Dile özgü örneğe bakın:

* [C#](#c-example)
* [C#betik (. CSX)](#c-script-example)
* [F#](#f-example)
* [Java](#java-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>C#örneğinde

Aşağıdaki örnek, dakikada beş ile bölünebilen bir [ C# işlevi](functions-dotnet-class-library.md) gösterir (örneğin, işlev 18:57:00 ' de başlıyorsa, sonraki performans 19:00:00 ' de olur). [@No__t-1](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) nesnesi işleve geçirilir.

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

### <a name="c-script-example"></a>C#betik örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir Zamanlayıcı tetikleyicisi bağlamasını ve bağlamayı kullanan bir [ C# betik işlevini](functions-reference-csharp.md) gösterir. İşlev, bu işlev çağrısının kaçırılmış bir zamanlama oluşumu olup olmadığını gösteren bir günlük yazar. [@No__t-1](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) nesnesi işleve geçirilir.

Bu, *function. JSON* dosyasındaki bağlama verileri:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# Betik kodu aşağıda verilmiştir:

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

### <a name="f-example"></a>F#örneğinde

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir Zamanlayıcı tetikleyicisi bağlamasını ve bağlamayı kullanan bir [ F# betik işlevini](functions-reference-fsharp.md) gösterir. İşlev, bu işlev çağrısının kaçırılmış bir zamanlama oluşumu olup olmadığını gösteren bir günlük yazar. [@No__t-1](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) nesnesi işleve geçirilir.

Bu, *function. JSON* dosyasındaki bağlama verileri:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

F# Betik kodu aşağıda verilmiştir:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="java-example"></a>Java örneği

Aşağıdaki örnek işlev her beş dakikada bir tetiklenir ve yürütülür. İşlevindeki `@TimerTrigger` ek açıklaması, [cron ifadeleriyle](https://en.wikipedia.org/wiki/Cron#CRON_expression)aynı dize biçimini kullanarak zamanlamayı tanımlar.

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

### <a name="javascript-example"></a>JavaScript örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir Zamanlayıcı tetikleyicisi bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlev, bu işlev çağrısının kaçırılmış bir zamanlama oluşumu olup olmadığını gösteren bir günlük yazar. İşleve bir [Zamanlayıcı nesnesi](#usage) geçirilir.

Bu, *function. JSON* dosyasındaki bağlama verileri:

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

### <a name="python-example"></a>Python örneği

Aşağıdaki örnek, bir yapılandırma *function. JSON* dosyasında açıklanan bir Zamanlayıcı tetikleyicisi bağlamayı kullanır. Bağlamayı kullanan gerçek [Python işlevi](functions-reference-python.md)  *__init__. Kopyala* dosyasında açıklanmıştır. İşleve geçirilen nesne, [Azure. Functions. TimerRequest nesnesi](/python/api/azure-functions/azure.functions.timerrequest)türündedir. İşlev mantığı, geçerli çağrının kaçırılmış bir zamanlama oluşumunda olup olmadığını gösteren günlüklere yazar. 

Bu, *function. JSON* dosyasındaki bağlama verileri:

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

## <a name="attributes"></a>Öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [timertriggerattribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)' u kullanın.

Özniteliğin Oluşturucusu bir CRON ifadesi veya `TimeSpan` alır. Yalnızca işlev uygulaması bir App Service planı üzerinde çalışıyorsa `TimeSpan` ' yı kullanabilirsiniz. Aşağıdaki örnek bir CRON ifadesini gösterir:

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

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `TimerTrigger` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | Yok | "TimerTrigger" olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**direction** | Yok | "In" olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | Yok | İşlev kodundaki Timer nesnesini temsil eden değişkenin adı. | 
|**schedule**|**ScheduleExpression**|Bir [cron ifadesi](#ncrontab-expressions) veya [TimeSpan](#timespan) değeri. @No__t-0, yalnızca bir App Service planı üzerinde çalışan bir işlev uygulaması için kullanılabilir. Zamanlama ifadesini bir uygulama ayarına yerleştirebilir ve bu özelliği şu örnekte gösterildiği gibi **%** işaretlere Sarmalanan uygulama ayarı adı olarak ayarlayabilirsiniz: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|@No__t-0 ise, çalışma zamanı başlatıldığında işlev çağrılır. Örneğin, çalışma zamanı, işlev uygulaması eylemsizlik nedeniyle boşta kaldıktan sonra uyandığında başlatılır. işlev değişiklikleri nedeniyle uygulama yeniden başlatıldığında ve işlev uygulaması ölçeklenirken. Bu nedenle **runOnStartup** , özellikle üretimde `true` olarak ayarlandıysa nadiren gerekir. |
|**useMonitor**|**UseMonitor**|Zamanlamanın izlenmesi gerekip gerekmediğini belirtmek için `true` veya `false` olarak ayarlayın. Zamanlamayı zamanla, işlev uygulama örnekleri yeniden başlatıldığında bile zamanlamanın doğru bir şekilde tutulmasını sağlamaya yardımcı olmak için zamanlama oluşumları devam ettirir. Açıkça ayarlanmamışsa, 1 dakikadan büyük veya ona eşit bir yinelenme aralığı olan zamanlamalar için varsayılan değer `true` ' dır. Dakikada birden çok kez tetikleyen zamanlamalar için varsayılan değer `false` ' dır.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Üretimde `true` ' i **runOnStartup** ayarlamayı öneririz. Bu ayarın kullanılması, kodun son derece öngörülemeyen zamanlarda yürütülmesine neden olur. Bazı üretim ayarlarında, bu ek yürütmeler, tüketim planlarında barındırılan uygulamalar için önemli ölçüde daha yüksek maliyetlere neden olabilir. Örneğin, **runOnStartup** etkin olduğunda tetikleyici, işlev uygulamanız her ölçeklendirildiğinde çağrılır. **RunOnStartup** etkinleştirilmeden önce işlevlerinizin üretim davranışını tam olarak anladığınızdan emin olun.   

## <a name="usage"></a>Kullanım

Bir Zamanlayıcı tetikleyicisi işlevi çağrıldığında, işleve bir Zamanlayıcı nesnesi geçirilir. Aşağıdaki JSON, Timer nesnesinin örnek bir gösterimidir.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

Geçerli işlev çağırma zamanlanmadan daha sonra olduğunda `IsPastDue` özelliği `true` ' dir. Örneğin, bir işlev uygulamasının yeniden başlatılması, çağrının kaçırılmasını sağlayabilir.

## <a name="ncrontab-expressions"></a>NCRONTAB ifadeleri 

Azure Işlevleri, NCRONTAB ifadelerini yorumlamak için [ncrontab](https://github.com/atifaziz/NCrontab) kitaplığını kullanır. Bir NCRONTAB ifadesi, bir CRON ifadesine benzer, ancak Saniyeler içinde zaman duyarlığı için kullanılmak üzere ek bir altıncı alan içerir:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Her alan aşağıdaki değer türlerinden birine sahip olabilir:

|Tür  |Örnek  |Tetiklendiğinde  |
|---------|---------|---------|
|Belirli bir değer |<nobr>"0 5 * * * *"</nobr>|ss: 05:00, SS her saat (saat)|
|Tüm değerler (`*`)|<nobr>"0 * 5 * * *"</nobr>|5: AA: 00 ' da her gün, DD 'nin saatte bir dakikası (günde 60 kez)|
|Bir Aralık (`-` işleci)|<nobr>"5-7 * * * * *"</nobr>|ss: DD: 05, ss: DD: 06, ve hh: mm: 07 saat|
|Bir değerler kümesi (`,` işleci)|<nobr>"5, 8, 10 * * * * *"</nobr>|ss: DD: 05, ss: DD: 08 ve hh: mm: 10 burada ss: DD her saatin dakikada bir (3 kez bir dakika)|
|Aralık değeri (`/` işleci)|<nobr>"0 */5 * * * *"</nobr>|ss: 05:00, ss: 10:00, hh: 15:00 ve bu şekilde ss: 00 ' da her saat (12 kez saat)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB örnekleri

Azure Işlevlerinde süreölçer tetikleyicisi için kullanabileceğiniz bazı NCRONTAB ifadeleri örnekleri aşağıda verilmiştir.

|Örnek|Tetiklendiğinde  |
|---------|---------|
|`"0 */5 * * * *"`|Beş dakikada bir|
|`"0 0 * * * *"`|her saatin üstünde bir kez|
|`"0 0 */2 * * *"`|Her iki saatte bir|
|`"0 0 9-17 * * *"`|her saat 9 ' dan 5 ' e kadar|
|`"0 30 9 * * *"`|Her gün 9:30 saat|
|`"0 30 9 * * 1-5"`|Her gün 9:30 saat|
|`"0 30 9 * Jan Mon"`|Ocak 9:30 ' de her Pazartesi|


### <a name="ncrontab-time-zones"></a>NCRONTAB saat dilimleri

Bir CRON ifadesindeki sayılar, zaman aralığı değil, bir saat ve tarihe başvurur. Örneğin, `hour` alanındaki 5, her 5 saatte bir değil 5:00 ÖÖ 'a başvurur.

CRON ifadeleriyle kullanılan varsayılan saat dilimi Eşgüdümlü Evrensel Saat (UTC) ' dir. CRON ifadenizi başka bir saat dilimine göre oluşturmak için, `WEBSITE_TIME_ZONE` adlı işlev uygulamanız için bir uygulama ayarı oluşturun. Değeri, [Microsoft saat dilimi dizininde](https://technet.microsoft.com/library/cc749073)gösterildiği gibi istenen saat diliminin adı olarak ayarlayın. 

Örneğin, *Doğu Standart saatı* UTC-05:00 ' dir. Zamanlayıcı tetikleyicinizin her gün 10:00 ' de tetiklenmesi için, UTC saat dilimi hesaplarının aşağıdaki NCRONTAB ifadesini kullanın:

```
"0 0 15 * * *"
``` 

Veya işlev uygulamanız için `WEBSITE_TIME_ZONE` adlı bir uygulama ayarı oluşturun ve değeri **Doğu Standart Saati**olarak ayarlayın.  Ardından aşağıdaki NCRONTAB ifadesini kullanır: 

```
"0 0 10 * * *"
``` 

@No__t-0 ' ı kullandığınızda, saat, belirli bir saat dilimlerinde gün ışığından yararlanma saati gibi zaman değişikliği için ayarlanır. 

## <a name="timespan"></a>TimeSpan

 @No__t-0, yalnızca bir App Service planı üzerinde çalışan bir işlev uygulaması için kullanılabilir.

Bir CRON ifadesinin aksine, `TimeSpan` değeri her bir işlev çağrısı arasındaki zaman aralığını belirtir. Bir işlev, belirtilen aralıktan daha uzun çalıştıktan sonra tamamlandığında, süreölçer işlevi hemen yeniden çağırır.

Bir dize olarak ifade edildiğinde, @no__t 2 ' den az olduğunda `TimeSpan` biçimi `hh:mm:ss` ' dir. İlk iki basamak 24 veya daha büyükse, biçim `dd:hh:mm` ' dır. İşte bazı örnekler:

|Örnek |Tetiklendiğinde  |
|---------|---------|
|"01:00:00" | her saat        |
|"00:01:00"|her dakika         |
|"24:00:00" | 24 saatte bir        |
|"1,00:00:00" | Her gün        |

## <a name="scale-out"></a>Ölçeklendirme

Bir işlev uygulaması birden çok örneğe ölçekleniyorsa, tüm örneklerde yalnızca bir Zamanlayıcı tetiklenen işlevin tek bir örneği çalıştırılır.

## <a name="function-apps-sharing-storage"></a>İşlev uygulamaları paylaşım depolaması

App Service 'e dağıtılmamış işlev uygulamalarında depolama hesaplarını paylaşıyorsanız, her uygulamaya açıkça ana bilgisayar KIMLIĞI atamanız gerekebilir.

| İşlevler sürümü | Ayar                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x               | `AzureFunctionsWebHost__hostid` ortam değişkeni |
| 'in               | *Host. JSON* içinde `id`                                  |

Tanımlayıcı değeri atlayabilir veya her bir işlev uygulamasının tanımlayıcı yapılandırmasını farklı bir değere el ile ayarlayabilirsiniz.

Zamanlayıcı tetikleyicisi, bir işlev uygulaması birden çok örneğe ölçeklenirken yalnızca bir zamanlayıcı örneği olduğundan emin olmak için bir depolama kilidi kullanır. İki işlev uygulaması aynı tanımlayıcı yapılandırmayı paylaşıyorsa ve her biri bir Zamanlayıcı tetikleyicisi kullanıyorsa, yalnızca bir Zamanlayıcı çalışır.

## <a name="retry-behavior"></a>Yeniden deneme davranışı

Sıra tetikleyicisinden farklı olarak, bir işlev başarısız olduktan sonra Zamanlayıcı tetikleyicisi yeniden denenmez. Bir işlev başarısız olduğunda, zamanlamaya bir sonraki sefer kadar bir kez çağrılmaz.

## <a name="troubleshooting"></a>Sorun giderme

Zamanlayıcı tetikleyicisi beklendiği gibi çalışmazsa yapılacaklar hakkında daha fazla bilgi için, bkz. [Zamanlayıcı tarafından tetiklenen işlevlerle ilgili sorunları araştırma ve raporlama](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Zamanlayıcı tetikleyicisi kullanan bir hızlı başlangıca git](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

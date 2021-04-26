---
title: Dayanıklı İşlevler zamanlayıcılar-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısında dayanıklı zamanlayıcıları nasıl uygulayacağınızı öğrenin.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: bb91f205a9b83b0b4b410644ef6c0fcbbf60876a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91876456"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler süreölçerler (Azure Işlevleri)

[Dayanıklı işlevler](durable-functions-overview.md) , gecikme süreleri uygulamak veya zaman uyumsuz eylemlerde zaman aşımları ayarlamak için Orchestrator işlevlerinde kullanılmak üzere *dayanıklı zamanlayıcılar* sağlar. Dayanıklı zamanlayıcılar, `Thread.Sleep` ve `Task.Delay` (C#), ya da ( `setTimeout()` `setInterval()` JavaScript) veya ( `time.sleep()` Python) yerine Orchestrator işlevlerinde kullanılmalıdır.

`CreateTimer` `createTimer` [Düzenleme tetikleyicisi bağlamasının](durable-functions-bindings.md#orchestration-trigger)(.net) yöntemini veya (JavaScript) yöntemini çağırarak dayanıklı bir Zamanlayıcı oluşturursunuz. Yöntemi, belirtilen tarih ve saatte tamamlanan bir görev döndürür.

## <a name="timer-limitations"></a>Süreölçer sınırlamaları

4:30 pm tarihinde süresi dolan bir Zamanlayıcı oluşturduğunuzda, temeldeki dayanıklı görev çerçevesi yalnızca 4:30 PM 'de görünür hale gelen bir iletiyi sıraya alır. Azure Işlevleri tüketim planında çalışırken, yeni görünür Zamanlayıcı iletisi, işlev uygulamasının uygun bir VM üzerinde etkinleştirilmesini sağlayacaktır.

> [!NOTE]
> * Dayanıklı bir uzantının [2.3.0 sürümünden](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) başlayarak, dayanıklı zamanlayıcılar sınırsızdır. Uzantının önceki sürümlerinde, dayanıklı zamanlayıcılar yedi gün ile sınırlıdır. Daha eski bir sürüm kullanırken ve yedi günden daha uzun bir gecikme olması gerekiyorsa, `while` Bu gecikmeyi taklit etmek için bir döngüde Zamanlayıcı API 'lerini kullanın.
> * `CurrentUtcDateTime` `DateTime.UtcNow` `currentUtcDateTime` `Date.now` `Date.UTC` Dayanıklı zamanlayıcılar için yangın süresini hesaplarken, .NET veya yerine her zaman kullanın JavaScript. Daha fazla bilgi için bkz. [Orchestrator işlev kodu kısıtlamaları](durable-functions-code-constraints.md) makalesi.

## <a name="usage-for-delay"></a>Gecikme kullanımı

Aşağıdaki örnek, bir erteleme yürütmesi için dayanıklı zamanlayıcıları nasıl kullanacağınızı gösterir. Örnek, 10 gün boyunca her gün bir fatura bildirimi yayınlanıyor.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Önceki C# örneği Dayanıklı İşlevler 2. x ' i hedefler. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> Orchestrator işlevlerinde sonsuz döngüleri önleyin. Sonsuz döngü senaryolarını güvenle ve verimli bir şekilde uygulama hakkında daha fazla bilgi için bkz. [Eternal düzenlemeleri](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Zaman aşımı kullanımı

Bu örnek, zaman aşımlarını uygulamak için dayanıklı zamanlayıcıları nasıl kullanacağınızı gösterir.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> Önceki C# örneği Dayanıklı İşlevler 2. x ' i hedefler. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> Kodunuzun tamamlanmasını `CancellationTokenSource` `cancel()` `TimerTask` beketmeyeceğini dayanıklı bir zamanlayıcıyı iptal etmek için (.net) veya döndürülen (JavaScript) çağrısı kullanın. Tüm bekleyen görevler tamamlanana veya iptal edilene kadar dayanıklı görev çerçevesi bir düzenleme durumunun "tamamlandı" olarak değiştirilmesine izin vermez.

Bu iptal mekanizması devam eden etkinlik işlevini veya alt düzenleme yürütmelerini sonlandırır. Bunun yerine, Orchestrator işlevinin sonucu yok saymasına ve üzerinde hareket etmesine izin verir. İşlev uygulamanız tüketim planını kullanıyorsa, bırakılan etkinlik işlevi tarafından tüketilen herhangi bir zaman ve bellek için faturalandırılırsınız. Varsayılan olarak, tüketim planında çalışan işlevlerin beş dakikalık bir zaman aşımı vardır. Bu sınır aşılırsa, tüm yürütmeyi durdurmak ve geri ödeme durumunun önlenmesi için Azure Işlevleri ana bilgisayarı geri dönüştürülür. [İşlev zaman aşımı yapılandırılabilir](../functions-host-json.md#functiontimeout).

Orchestrator işlevlerinde zaman aşımları uygulama hakkında daha ayrıntılı bir örnek için bkz. [ınsan etkileşimi & zaman aşımları-telefon doğrulama](durable-functions-phone-verification.md) makalesi.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dış olayları nasıl yükselteceğinizi ve işleyeceğinizi öğrenin](durable-functions-external-events.md)

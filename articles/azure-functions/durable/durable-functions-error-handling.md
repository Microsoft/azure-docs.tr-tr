---
title: Dayanıklı İşlevler hataları işleme-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısı 'ndaki hataların nasıl işleneceğini öğrenin.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 023f9dfcc421935c3f7515e847108925d5e5521e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673656"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler hataları işleme (Azure Işlevleri)

Dayanıklı Işlev düzenlemeleri kodda uygulanır ve programlama dilinin yerleşik hata işleme özelliklerini kullanabilir. Organize etmeniz için hata işleme ve dengeleme eklemek için öğrenmeniz gereken yeni kavramlar aslında yoktur. Ancak, bilmeniz gereken birkaç davranış vardır.

## <a name="errors-in-activity-functions"></a>Etkinlik işlevlerinde hatalar

Bir etkinlik işlevinde oluşturulan herhangi bir özel durum Orchestrator işlevine geri hazırlanır ve bir olarak oluşturulur `FunctionFailedException` . Orchestrator işlevindeki gereksinimlerinize uyan hata işleme ve dengeleme kodu yazabilirsiniz.

Örneğin, bir hesaptan diğerine fonları aktaran aşağıdaki Orchestrator işlevini göz önünde bulundurun:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Önceki C# örnekleri Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```

---

İlk **alacaklı Taccount** işlev çağrısı başarısız olursa, Orchestrator işlevi fontları kaynak hesaba geri alacak şekilde dengeler.

## <a name="automatic-retry-on-failure"></a>Hata durumunda otomatik yeniden deneme

Etkinlik işlevlerini veya alt düzenleme işlevlerini çağırdığınızda otomatik yeniden deneme ilkesi belirtebilirsiniz. Aşağıdaki örnek, bir işlevi üç kez çağırmaya çalışır ve her yeniden deneme arasında 5 saniye bekler:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Önceki C# örnekleri Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

Önceki örnekteki etkinlik işlevi çağrısı otomatik yeniden deneme ilkesini yapılandırmak için bir parametre alır. Otomatik yeniden deneme ilkesini özelleştirmek için çeşitli seçenekler vardır:

* **Deneme sayısı** üst sınırı: en fazla yeniden deneme denemesi sayısı.
* **İlk yeniden deneme aralığı**: ilk yeniden deneme girişiminden önce beklenecek süre.
* **Geri dönüşlü katsayı**: geri alma artışının oranını belirlemede kullanılan katsayı. Varsayılan değer 1 ' dir.
* **En fazla yeniden deneme aralığı**: yeniden deneme girişimleri arasında beklenecek en uzun süre.
* **Yeniden deneme zaman aşımı**: yeniden denemeler yapmak için harcayabileceğiniz en uzun süre. Varsayılan davranış süresiz olarak yeniden denenecektir.
* **Tanıtıcı**: bir işlevin yeniden denenip denenmeyeceğini anlamak için Kullanıcı tanımlı geri çağırma belirtilebilir. 

> [!NOTE]
> Kullanıcı tanımlı geri çağrılar Şu anda JavaScript () içinde Dayanıklı İşlevler tarafından desteklenmiyor `context.df.RetryOptions` .


## <a name="function-timeouts"></a>İşlev zaman aşımları

Bir Orchestrator işlevi içindeki bir işlev çağrısını, tamamlanamayacak kadar uzun sürerse iptal etmek isteyebilirsiniz. Bu işlemi bugün yapmanın doğru yolu, (.net), [](durable-functions-timers.md) (JavaScript) veya (Python) ile birlikte (.net), (JavaScript) veya ( `context.CreateTimer` Python) `context.df.createTimer` `context.create_timer` ile birlikte, `Task.WhenAny` `context.df.Task.any` `context.task_any` Aşağıdaki örnekte olduğu gibi dayanıklı bir Zamanlayıcı oluşturmaktır:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> Önceki C# örnekleri Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
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
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!NOTE]
> Bu mekanizma, devam eden etkinlik işlevi yürütmeyi gerçekten sonlandırır. Bunun yerine, Orchestrator işlevinin sonucu yok saymasına ve üzerinde hareket etmesine izin verir. Daha fazla bilgi için bkz. [zamanlayıcılar](durable-functions-timers.md#usage-for-timeout) belgeleri.

## <a name="unhandled-exceptions"></a>İşlenmeyen özel durumlar

Orchestrator işlevi işlenmeyen bir özel durumla başarısız olursa, özel durumun ayrıntıları günlüğe kaydedilir ve örnek bir durum ile tamamlanır `Failed` .

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dış düzenlemeleri hakkında bilgi edinin](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Sorunları tanılamayı öğrenin](durable-functions-diagnostics.md)

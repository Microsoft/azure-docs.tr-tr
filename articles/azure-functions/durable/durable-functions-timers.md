---
title: Dayanıklı İşlevler zamanlayıcılar-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısında dayanıklı zamanlayıcıları nasıl uygulayacağınızı öğrenin.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: a24d6e96df3abf385b0a64ec4bc7e1f1c248998b
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614632"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler süreölçerler (Azure Işlevleri)

[Dayanıklı işlevler](durable-functions-overview.md) , gecikme süreleri uygulamak veya zaman uyumsuz eylemlerde zaman aşımları ayarlamak için Orchestrator işlevlerinde kullanılmak üzere *dayanıklı zamanlayıcılar* sağlar. Dayanıklı zamanlayıcılar `Thread.Sleep` ve `Task.Delay` (C#) veya `setTimeout()` ve `setInterval()` (JavaScript) yerine Orchestrator işlevlerinde kullanılmalıdır.

[Düzenleme tetikleyicisi bağlamasının](durable-functions-bindings.md#orchestration-trigger)`CreateTimer` (.net) yöntemini veya `createTimer` (JavaScript) yöntemini çağırarak dayanıklı bir Zamanlayıcı oluşturursunuz. Yöntemi, belirtilen tarih ve saatte tamamlanan bir görev döndürür.

## <a name="timer-limitations"></a>Süreölçer sınırlamaları

4:30 pm tarihinde süresi dolan bir Zamanlayıcı oluşturduğunuzda, temeldeki dayanıklı görev çerçevesi yalnızca 4:30 PM 'de görünür hale gelen bir iletiyi sıraya alır. Azure Işlevleri tüketim planında çalışırken, yeni görünür Zamanlayıcı iletisi, işlev uygulamasının uygun bir VM üzerinde etkinleştirilmesini sağlayacaktır.

> [!NOTE]
> * Dayanıklı zamanlayıcılar Şu anda 7 gün ile sınırlıdır. Daha uzun gecikmeler gerekliyse, bir `while` döngüsünde Zamanlayıcı API 'Leri kullanılarak simülabilirler.
> * Sürekli zamanlayıcılar için yangın süresini hesaplarken JavaScript 'te `Date.now` veya `Date.UTC` yerine `CurrentUtcDateTime` her zaman .NET veya `currentUtcDateTime` `DateTime.UtcNow` kullanın. Daha fazla bilgi için bkz. [Orchestrator işlev kodu kısıtlamaları](durable-functions-code-constraints.md) makalesi.

## <a name="usage-for-delay"></a>Gecikme kullanımı

Aşağıdaki örnek, bir erteleme yürütmesi için dayanıklı zamanlayıcıları nasıl kullanacağınızı gösterir. Örnek, 10 gün boyunca her gün bir fatura bildirimi yayınlanıyor.

### <a name="c"></a>C#

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
> Önceki C# örnek dayanıklı işlevler 2. x ' i hedefliyor. Dayanıklı İşlevler 1. x için `IDurableOrchestrationContext`yerine `DurableOrchestrationContext` kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Orchestrator işlevlerinde sonsuz döngüleri önleyin. Sonsuz döngü senaryolarını güvenle ve verimli bir şekilde uygulama hakkında daha fazla bilgi için bkz. [Eternal düzenlemeleri](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Zaman aşımı kullanımı

Bu örnek, zaman aşımlarını uygulamak için dayanıklı zamanlayıcıları nasıl kullanacağınızı gösterir.

### <a name="c"></a>C#

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
> Önceki C# örnek dayanıklı işlevler 2. x ' i hedefliyor. Dayanıklı İşlevler 1. x için `IDurableOrchestrationContext`yerine `DurableOrchestrationContext` kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

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

> [!WARNING]
> Kodunuzun tamamlanmasını beketmeyeceğini dayanıklı bir zamanlayıcıyı (.NET) iptal etmek veya döndürülen `TimerTask` (JavaScript) üzerinde `cancel()` çağırmak için `CancellationTokenSource` kullanın. Tüm bekleyen görevler tamamlanana veya iptal edilene kadar dayanıklı görev çerçevesi bir düzenleme durumunun "tamamlandı" olarak değiştirilmesine izin vermez.

Bu iptal mekanizması devam eden etkinlik işlevini veya alt düzenleme yürütmelerini sonlandırır. Bunun yerine, Orchestrator işlevinin sonucu yok saymasına ve üzerinde hareket etmesine izin verir. İşlev uygulamanız tüketim planını kullanıyorsa, bırakılan etkinlik işlevi tarafından tüketilen herhangi bir zaman ve bellek için faturalandırılırsınız. Varsayılan olarak, tüketim planında çalışan işlevlerin beş dakikalık bir zaman aşımı vardır. Bu sınır aşılırsa, tüm yürütmeyi durdurmak ve geri ödeme durumunun önlenmesi için Azure Işlevleri ana bilgisayarı geri dönüştürülür. [İşlev zaman aşımı yapılandırılabilir](../functions-host-json.md#functiontimeout).

Orchestrator işlevlerinde zaman aşımları uygulama hakkında daha ayrıntılı bir örnek için bkz. [ınsan etkileşimi & zaman aşımları-telefon doğrulama](durable-functions-phone-verification.md) makalesi.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Dış olayları nasıl yükselteceğinizi ve işleyeceğinizi öğrenin](durable-functions-external-events.md)

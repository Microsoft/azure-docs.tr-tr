---
title: Dayanıklı İşlevler dış olayları işleme-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısı 'nda dış olayları nasıl işleyeceğinizi öğrenin.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1c2a2f08c31c427241bbd5e482906118a90ee178
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614843"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler dış olayları işleme (Azure Işlevleri)

Orchestrator işlevlerinin dış olayları bekleme ve dinleme yeteneği vardır. Bu [dayanıklı işlevler](durable-functions-overview.md) özelliği, genellikle insan etkileşimini veya diğer dış Tetikleyicileri işlemek için yararlıdır.

> [!NOTE]
> Dış olaylar tek yönlü zaman uyumsuz işlemlerdir. Bu istemciler, olayı gönderen istemcinin Orchestrator işlevinden zaman uyumlu bir yanıt ihtiyacı olduğu durumlar için uygun değildir.

## <a name="wait-for-events"></a>Olayları bekle

[Orchestration tetikleyicisi bağlamasının](durable-functions-bindings.md#orchestration-trigger) `WaitForExternalEvent` (.net) ve `waitForExternalEvent` (JavaScript) yöntemleri bir Orchestrator işlevinin bir dış olayı zaman uyumsuz olarak bekleyip dinlemesine olanak tanır. Dinleme Orchestrator işlevi, olayın *adını* ve almayı beklediği *verilerin şeklini* bildirir.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> Önceki C# kod dayanıklı işlevler 2. x içindir. Dayanıklı İşlevler 1. x için `IDurableOrchestrationContext`yerine `DurableOrchestrationContext` kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

Yukarıdaki örnek, belirli bir olayı dinler ve alındığında işlem gerçekleştirir.

Aşağıdaki örnekte olduğu gibi birden çok olayı aynı anda dinleyebilir. Bu, olası üç olay bildiriminin birini bekler.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> Önceki C# kod dayanıklı işlevler 2. x içindir. Dayanıklı İşlevler 1. x için `IDurableOrchestrationContext`yerine `DurableOrchestrationContext` kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

#### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

Önceki örnek, birden çok olayı dinler. *Tüm* olayları beklemek da mümkündür.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> Önceki kod Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `IDurableOrchestrationContext`yerine `DurableOrchestrationContext` kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

#### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

`WaitForExternalEvent` bazı girişler için süresiz olarak bekler.  İşlev uygulaması beklerken güvenli bir şekilde kaldırılabilir. Bu düzenleme örneğine bir olay ulaştığında, başlatılabilmesi uyandırılır otomatik olarak oluşturulur ve olayı anında işler.

> [!NOTE]
> İşlev uygulamanız tüketim planını kullanıyorsa, bir Orchestrator işlevi `WaitForExternalEvent` (.NET) veya `waitForExternalEvent` (JavaScript) 'den bir görevi beklerken, ne kadar bekleyeceğini bağımsız olarak hiçbir faturalandırma ücreti tahakkuk etmez.

.NET sürümünde, olay yükü beklenen tür `T`dönüştürülemiyorsa, bir özel durum oluşturulur.

## <a name="send-events"></a>Olayları gönderme

[Orchestration Client bağlamasının](durable-functions-bindings.md#orchestration-client) `RaiseEventAsync` (.net) veya `raiseEvent` (JavaScript) yöntemi, `WaitForExternalEvent` (.net) veya `waitForExternalEvent` (JavaScript) için beklediği olayları gönderir.  `RaiseEventAsync` yöntemi, *EventName* ve *eventdata* parametrelerini parametre olarak alır. Olay verileri JSON ile seri hale getirilebilir olmalıdır.

Aşağıda, bir Orchestrator işlev örneğine "onay" olayı gönderen örnek bir Queue-tetiklenen işlev verilmiştir. Orchestration örnek KIMLIĞI kuyruk iletisinin gövdesinden gelir.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Önceki C# kod dayanıklı işlevler 2. x içindir. Dayanıklı İşlevler 1. x için `DurableClient` özniteliği yerine `OrchestrationClient` özniteliğini kullanmanız gerekir ve `IDurableOrchestrationClient`yerine `DurableOrchestrationClient` parametre türünü kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Dahili olarak, `RaiseEventAsync` (.NET) veya `raiseEvent` (JavaScript), bekleyen Orchestrator işlevi tarafından çekilen bir iletiyi sıraya alır. Örnek, belirtilen *olay adında* beklemmediyse, olay iletisi bir bellek içi kuyruğa eklenir. Düzenleme örneği daha sonra bu *olay adını* dinlemeye başlarsa, olay iletileri için sırayı denetler.

> [!NOTE]
> Belirtilen *örnek kimliğine*sahip bir düzenleme örneği yoksa, olay iletisi atılır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hata işlemeyi nasıl uygulayacağınızı öğrenin](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [İnsan etkileşimini bekleyen bir örnek çalıştırın](durable-functions-phone-verification.md)
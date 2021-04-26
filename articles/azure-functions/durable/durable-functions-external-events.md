---
title: Dayanıklı İşlevler dış olayları işleme-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısı 'nda dış olayları nasıl işleyeceğinizi öğrenin.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: a7625a6fcd1000595c2c582935c839ba6d26b20d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728496"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler dış olayları işleme (Azure Işlevleri)

Orchestrator işlevlerinin dış olayları bekleme ve dinleme yeteneği vardır. Bu [dayanıklı işlevler](durable-functions-overview.md) özelliği, genellikle insan etkileşimini veya diğer dış Tetikleyicileri işlemek için yararlıdır.

> [!NOTE]
> Dış olaylar tek yönlü zaman uyumsuz işlemlerdir. Bu istemciler, olayı gönderen istemcinin Orchestrator işlevinden zaman uyumlu bir yanıt ihtiyacı olduğu durumlar için uygun değildir.

## <a name="wait-for-events"></a>Olayları bekle

Orchestration tetikleyicisi bağlamasının [WaitForExternalEvent](/dotnet/api/microsoft.azure.webjobs.durableorchestrationcontextbase.waitforexternalevent?view=azure-dotnet-legacy&preserve-view=true) (.net), `waitForExternalEvent` (JavaScript) ve `wait_for_external_event` (Python) yöntemleri bir [](durable-functions-bindings.md#orchestration-trigger) Orchestrator işlevinin bir dış olayı zaman uyumsuz olarak bekleyip dinlemesine izin verir. Dinleme Orchestrator işlevi, olayın *adını* ve almayı beklediği *verilerin şeklini* bildirir.

# <a name="c"></a>[C#](#tab/csharp)

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
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

Yukarıdaki örnek, belirli bir olayı dinler ve alındığında işlem gerçekleştirir.

Aşağıdaki örnekte olduğu gibi birden çok olayı aynı anda dinleyebilir. Bu, olası üç olay bildiriminin birini bekler.

# <a name="c"></a>[C#](#tab/csharp)

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
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

Önceki örnek, birden çok  olayı dinler. *Tüm* olayları beklemek da mümkündür.

# <a name="c"></a>[C#](#tab/csharp)

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
> Önceki kod Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

.NET sürümünde, olay yükü beklenen türe dönüştürülemiyorsa `T` , bir özel durum oluşturulur.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` Bazı girişler için süresiz olarak bekler.  İşlev uygulaması beklerken güvenli bir şekilde kaldırılabilir. Bu düzenleme örneğine bir olay ulaştığında, başlatılabilmesi uyandırılır otomatik olarak oluşturulur ve olayı anında işler.

> [!NOTE]
> İşlev uygulamanız tüketim planını kullanıyorsa, bir Orchestrator işlevi bir görevi beklerken `WaitForExternalEvent` (.net), `waitForExternalEvent` (JavaScript) veya (Python), ne kadar bekleyeceğini bağımsız olarak hiçbir faturalandırma ücreti tahakkuk etmez `wait_for_external_event` .

## <a name="send-events"></a>Olayları gönderme

[](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclientbase.raiseeventasync?view=azure-dotnet-legacy&preserve-view=true) `raiseEventAsync` Bir Orchestration 'a dış olay göndermek için RaiseEventAsync (.net) veya (JavaScript) yöntemlerini kullanabilirsiniz. Bu yöntemler [Orchestration istemci](durable-functions-bindings.md#orchestration-client) bağlaması tarafından gösterilir. Ayrıca, bir Orchestration 'a dış olay göndermek için yerleşik oluşturma [OLAYı HTTP API](durable-functions-http-api.md#raise-event) 'sini de kullanabilirsiniz.

Oluşturulan olay bir *örnek kimliği*, bir *EventName* ve *eventdata* parametrelerini içerir. Orchestrator işlevleri, `WaitForExternalEvent` (.net) veya `waitForExternalEvent` (JavaScript) API 'lerini kullanarak bu olayları işler. Olayın işlenebilmesi için, söz konusu hem gönderme hem de alma uçları *ile eşleşmelidir.* Olay verileri de JSON ile seri hale getirilebilir olmalıdır.

Dahili olarak, "olay oluştur" mekanizmaları, bekleyen Orchestrator işlevi tarafından çekilen bir iletiyi sıraya alır. Örnek, belirtilen *olay adında* beklemmediyse, olay iletisi bir bellek içi kuyruğa eklenir. Düzenleme örneği daha sonra bu *olay adını* dinlemeye başlarsa, olay iletileri için sırayı denetler.

> [!NOTE]
> Belirtilen *örnek kimliğine* sahip bir düzenleme örneği yoksa, olay iletisi atılır.

Aşağıda, bir Orchestrator işlev örneğine "onay" olayı gönderen örnek bir Queue-tetiklenen işlev verilmiştir. Orchestration örnek KIMLIĞI kuyruk iletisinin gövdesinden gelir.

# <a name="c"></a>[C#](#tab/csharp)

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
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

Dahili, `RaiseEventAsync` (.net), `raiseEvent` (JavaScript) veya `raise_event` (Python), bekleyen Orchestrator işlevinin oluşturduğu bir iletiyi sıraya alır. Örnek, belirtilen *olay adında* beklemmediyse, olay iletisi bir bellek içi kuyruğa eklenir. Düzenleme örneği daha sonra bu *olay adını* dinlemeye başlarsa, olay iletileri için sırayı denetler.

> [!NOTE]
> Belirtilen *örnek kimliğine* sahip bir düzenleme örneği yoksa, olay iletisi atılır.

### <a name="http"></a>HTTP

Aşağıda, bir Orchestration örneğine "onay" olayı oluşturan HTTP isteğinin bir örneği verilmiştir. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

Bu durumda, örnek KIMLIĞI *Myınstanceıd* olarak sabit olarak kodlanır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hata işlemeyi nasıl uygulayacağınızı öğrenin](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [İnsan etkileşimini bekleyen bir örnek çalıştırın](durable-functions-phone-verification.md)
---
title: Dayanıklı İşlevler için alt düzenlemeler-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısı 'ndaki genişletmelerin nasıl çağrılacağını çağırma.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5625bc2ddfa4b6f527ca16f19f33d257a1834d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85340810"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler alt düzenlemeler (Azure Işlevleri)

Orchestrator işlevleri, etkinlik işlevlerini çağırmanın yanı sıra diğer Orchestrator işlevlerini de çağırabilir. Örneğin, daha küçük bir Orchestrator işlevleri kitaplığından daha büyük bir düzenleme oluşturabilirsiniz. Alternatif olarak, bir Orchestrator işlevinin birden fazla örneğini paralel olarak çalıştırabilirsiniz.

Orchestrator işlevi, .NET 'teki veya yöntemlerini ya da `CallSubOrchestratorAsync` `CallSubOrchestratorWithRetryAsync` `callSubOrchestrator` JavaScript içindeki yöntemleri kullanarak başka bir Orchestrator işlevini çağırabilir `callSubOrchestratorWithRetry` . [& Dengeleme makalesindeki hata işleme](durable-functions-error-handling.md#automatic-retry-on-failure) otomatik yeniden deneme hakkında daha fazla bilgi içerir.

Alt Orchestrator işlevleri, çağıranın perspektifinden yalnızca etkinlik işlevleri gibi davranır. Bunlar bir değer döndürebilir, bir özel durum oluşturabilir ve üst Orchestrator işlevi tarafından beklelenebilir. 

> [!NOTE]
> Alt düzenlemeler şu anda .NET ve JavaScript 'de desteklenmektedir.

## <a name="example"></a>Örnek

Aşağıdaki örnekte, sağlanması gereken birden çok cihaz olduğu IoT ("Nesnelerin İnterneti") senaryosu gösterilmektedir. Aşağıdaki işlev her bir cihaz için yürütülmesi gereken sağlama iş akışını temsil eder:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

---

Bu Orchestrator işlevi, tek seferlik cihaz sağlama için olduğu gibi kullanılabilir veya daha büyük bir düzenleme parçası olabilir. İkinci durumda, üst Orchestrator işlevi `DeviceProvisioningOrchestration` `CallSubOrchestratorAsync` (.net) veya `callSubOrchestrator` (JavaScript) API 'sini kullanarak örnekleri zamanlayabilir.

Birden çok Orchestrator işlevinin paralel olarak nasıl çalıştırılacağını gösteren bir örnek aşağıda verilmiştir.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> Önceki C# örnekleri Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> Alt düzenlemeler, üst düzenleme ile aynı işlev uygulamasında tanımlanmalıdır. Başka bir işlev uygulamasındaki düzenlemeleri çağırmanız ve beklemeniz gerekiyorsa, HTTP API 'Leri için yerleşik desteği ve HTTP 202 yoklaması tüketici modelini kullanmayı göz önünde bulundurun. Daha fazla bilgi için bkz. [http özellikleri](durable-functions-http-features.md) konusu.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel bir düzenleme durumu ayarlamayı öğrenin](durable-functions-custom-orchestration-status.md)

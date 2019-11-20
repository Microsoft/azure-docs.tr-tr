---
title: Dayanıklı İşlevler-Azure için tekton
description: Azure Işlevleri için Dayanıklı İşlevler uzantısında tekton kullanımı.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: ec7eb1eba2bc029d592560b39cde20e93e5afcd6
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614674"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler içindeki tekil düzenleyiciler (Azure Işlevleri)

Arka plan işleri için, genellikle belirli bir Orchestrator örneğinin aynı anda çalıştığından emin olmanız gerekir. Bu tür bir tek [dayanıklı işlevler](durable-functions-overview.md) davranışın, bir Orchestrator 'a belirli BIR örnek kimliği atayarak bir Orchestrator 'a atanmasını sağlayabilirsiniz.

## <a name="singleton-example"></a>Tek örnek

Aşağıdaki örnek, tek bir arka plan işi düzenlemesi oluşturan bir HTTP tetikleyici işlevini gösterir. Kod, belirtilen örnek KIMLIĞI için yalnızca bir örneğin mevcut olmasını sağlar.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> Önceki C# kod dayanıklı işlevler 2. x içindir. Dayanıklı İşlevler 1. x için `DurableClient` özniteliği yerine `OrchestrationClient` özniteliğini kullanmanız gerekir ve `IDurableOrchestrationClient`yerine `DurableOrchestrationClient` parametre türünü kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

İşte function. JSON dosyası:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

JavaScript kodu aşağıda verilmiştir:
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Varsayılan olarak, örnek kimlikleri rastgele oluşturulan GUID 'lerdir. Bununla birlikte, önceki örnekte, örnek KIMLIĞI URL 'den rota verilerinde geçirilir. Kod, belirtilen KIMLIĞE sahipC#bir örneğin zaten çalışır durumda olup olmadığını denetlemek için `GetStatusAsync`() veya `getStatus` (JavaScript) öğesini çağırır. Böyle bir örnek çalışmıyorsa, bu KIMLIKLE yeni bir örnek oluşturulur.

> [!NOTE]
> Bu örnekte olası bir yarış durumu var. İki tane **Httpstartone** örneği aynı anda yürütülecektir, her iki işlev çağrısı de başarıyı bildirir ancak yalnızca bir düzenleme örneği başlatılır. Gereksinimlerinize bağlı olarak, bu istenmeyen yan etkilere sahip olabilir. Bu nedenle, bu tetikleyici işlevini aynı anda yürütebilmesi için iki isteğin olmadığından emin olmanız önemlidir.

Orchestrator işlevinin uygulama ayrıntıları gerçek anlamda değildir. Bu, başlayan ve tamamlanmış normal bir Orchestrator işlevi olabilir ya da sonsuza kadar (yani, bir [Eternal düzenleme](durable-functions-eternal-orchestrations.md)) çalışan bir düzenleyici olabilir. Önemli nokta, tek seferde çalışan yalnızca bir örnek vardır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Düzenleyicmelerin yerel HTTP özellikleri hakkında bilgi edinin](durable-functions-http-features.md)

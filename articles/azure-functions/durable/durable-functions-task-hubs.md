---
title: Dayanıklı İşlevler görev hub 'ları-Azure
description: Bir görev hub 'ının Azure Işlevleri için Dayanıklı İşlevler uzantısı 'nda olduğunu öğrenin. Görev hub 'larını yapılandırmayı öğrenin.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009526"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler görev hub 'ları (Azure Işlevleri)

[Dayanıklı işlevler](durable-functions-overview.md) bir *görev hub 'ı* , düzenleme için kullanılan Azure depolama kaynakları için mantıksal bir kapsayıcıdır. Orchestrator ve Activity işlevleri aynı görev merkezine ait olduklarında yalnızca birbirleriyle etkileşim kurabilir.

Birden çok işlev uygulaması bir depolama hesabını paylaşıyorsa, her işlev uygulamasının ayrı bir görev hub 'ı adıyla yapılandırılması *gerekir* . Bir depolama hesabı, birden çok görev hub 'ı içerebilir. Aşağıdaki diyagramda, paylaşılan ve ayrılmış depolama hesaplarında işlev uygulaması başına bir görev hub 'ı gösterilmektedir.

![Paylaşılan ve ayrılmış depolama hesaplarını gösteren diyagram.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure depolama kaynakları

Bir görev hub 'ı aşağıdaki depolama kaynaklarından oluşur:

* Bir veya daha fazla denetim kuyruğu.
* Bir iş öğesi kuyruğu.
* Bir geçmiş tablosu.
* Tek örnek tablosu.
* Bir veya daha fazla kira blob içeren bir depolama kapsayıcısı.
* Varsa, büyük ileti yüklerini içeren bir depolama kapsayıcısı.

Orchestrator, varlık veya etkinlik işlevleri çalıştırıldığında veya çalıştırılmak üzere zamanlandığında, bu kaynakların tümü varsayılan Azure depolama hesabında otomatik olarak oluşturulur. [Performans ve ölçeklendirme](durable-functions-perf-and-scale.md) makalesinde bu kaynakların nasıl kullanıldığı açıklanmaktadır.

## <a name="task-hub-names"></a>Görev hub 'ı adları

Görev hub 'ları, bu kurallara uyan bir ad tarafından tanımlanır:

* Yalnızca alfasayısal karakterler içerir
* Bir harfle başlar
* En az 3 karakter uzunluğunda, en fazla 45 karakter uzunluğunda

Aşağıdaki örnekte gösterildiği gibi, görev hub 'ı adı dosyada *host.js* olarak belirtilir:

### <a name="hostjson-functions-20"></a>host.json (Işlevler 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json (Işlevler 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Görev hub 'ları, aşağıdaki örnek dosyada gösterildiği gibi uygulama ayarları kullanılarak da yapılandırılabilir `host.json` :

### <a name="hostjson-functions-10"></a>host.json (Işlevler 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Işlevler 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

Görev hub 'ı adı, `MyTaskHub` uygulama ayarının değerine ayarlanır. Aşağıda, `local.settings.json` ayarın nasıl tanımlanacağı gösterilmektedir `MyTaskHub` `samplehubname` :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Aşağıdaki kod, bir uygulama ayarı olarak yapılandırılmış bir görev hub 'ı ile çalışmak üzere [Orchestration istemci bağlamasını](durable-functions-bindings.md#orchestration-client) kullanan bir işlevin nasıl yazılacağını göstermektedir:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Önceki C# örneği Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dosyadaki görev hub 'ı özelliği `function.json` uygulama ayarı aracılığıyla ayarlanır:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

Dosyadaki görev hub 'ı özelliği `function.json` uygulama ayarı aracılığıyla ayarlanır:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Görev hub 'ı adları bir harfle başlamalı ve yalnızca harf ve sayılardan oluşmalıdır. Belirtilmemişse, aşağıdaki tabloda gösterildiği gibi varsayılan bir görev hub 'ı adı kullanılacaktır:

| Dayanıklı uzantı sürümü | Varsayılan görev hub 'ı adı |
| - | - |
| 2.x | Azure 'da dağıtıldığında, görev hub 'ı adı _işlev uygulamasının_ adından türetilir. Azure dışında çalışırken, varsayılan görev hub 'ı adı olur `TestHubName` . |
| 'in | Tüm ortamların varsayılan görev hub 'ı adı `DurableFunctionsHub` . |

Uzantı sürümleri arasındaki farklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

> [!NOTE]
> Ad, paylaşılan bir depolama hesabında birden çok görev hub 'ı olduğunda, bir görev hub 'ını diğerinden farklılaştırır. Paylaşılan bir depolama hesabını paylaşan birden çok işlev uygulamanız varsa, dosyalardaki *host.js* her bir görev hub 'ı için farklı adlar açıkça yapılandırmanız gerekir. Aksi halde, birden çok işlev uygulaması iletiler için birbirleriyle rekabet eder ve bu da, veya durumunda beklenmedik şekilde "takılmalar" gibi, tanımsız davranışa neden olabilir `Pending` `Running` .

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Düzenleme sürümü oluşturmayı nasıl ele alabileceğinizi öğrenin](durable-functions-versioning.md)

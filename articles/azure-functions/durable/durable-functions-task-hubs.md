---
title: Dayanıklı İşlevler görev hub 'ları-Azure
description: Bir görev hub 'ının Azure Işlevleri için Dayanıklı İşlevler uzantısı 'nda olduğunu öğrenin. Görev hub 'larını yapılandırmayı öğrenin.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: b42294fdcf60add8496116bd1f83bf64f54a5f63
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614694"
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

Görev hub 'ları, aşağıdaki örnekte gösterildiği gibi *Host. JSON* dosyasında belirtilen bir adla tanımlanır:

### <a name="hostjson-functions-20"></a>Host. JSON (Işlevler 2,0)

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

### <a name="hostjson-functions-1x"></a>Host. JSON (Işlevler 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Görev hub 'ları, aşağıdaki `host.json` örnek dosyasında gösterildiği gibi, uygulama ayarları kullanılarak da yapılandırılabilir:

### <a name="hostjson-functions-10"></a>Host. JSON (Işlevler 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>Host. JSON (Işlevler 2,0)

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

Görev hub 'ı adı `MyTaskHub` uygulama ayarı değerine ayarlanır. Aşağıdaki `local.settings.json` `MyTaskHub` ayarının `samplehubname`olarak nasıl tanımlanacağını göstermektedir:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Aşağıdaki kod, bir uygulama ayarı C# olarak yapılandırılmış bir görev hub 'ı ile çalışmak üzere [Orchestration istemci bağlamasını](durable-functions-bindings.md#orchestration-client) kullanan bir işlevin nasıl yazılacağı konusunda önceden derlenmiş bir örnektir:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Önceki C# örnek dayanıklı işlevler 2. x içindir. Dayanıklı İşlevler 1. x için `IDurableOrchestrationContext`yerine `DurableOrchestrationContext` kullanmanız gerekir. Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

### <a name="javascript"></a>JavaScript

`function.json` dosyasındaki görev hub 'ı özelliği uygulama ayarı aracılığıyla ayarlanır:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Görev hub 'ı adları bir harfle başlamalı ve yalnızca harf ve sayılardan oluşmalıdır. Belirtilmemişse, aşağıdaki tabloda gösterildiği gibi varsayılan bir görev hub 'ı adı kullanılacaktır:

| Dayanıklı uzantı sürümü | Varsayılan görev hub 'ı adı |
| - | - |
| 2.x | Azure 'da dağıtıldığında, görev hub 'ı adı _işlev uygulamasının_adından türetilir. Azure dışında çalışırken, varsayılan görev hub 'ı adı `TestHubName`. |
| 'in | Tüm ortamların varsayılan görev hub 'ı adı `DurableFunctionsHub`. |

Uzantı sürümleri arasındaki farklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

> [!NOTE]
> Ad, paylaşılan bir depolama hesabında birden çok görev hub 'ı olduğunda, bir görev hub 'ını diğerinden farklılaştırır. Paylaşılan bir depolama hesabını paylaşan birden çok işlev uygulamanız varsa, *Host. JSON* dosyalarındaki her bir görev hub 'ı için farklı adlar açıkça yapılandırmanız gerekir. Aksi halde, birden çok işlev uygulaması iletiler için birbirleriyle rekabet eder ve bu da, `Pending` veya `Running` durumunda beklenmedik şekilde "takılmalar" gibi, tanımsız davranışa neden olabilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Düzenleme sürümü oluşturmayı nasıl ele alabileceğinizi öğrenin](durable-functions-versioning.md)

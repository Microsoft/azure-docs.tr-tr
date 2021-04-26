---
title: Dayanıklı İşlevler örnekleri yönetme-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısı 'ndaki örnekleri yönetmeyi öğrenin.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 7329962d547fcb0635e3a9af3d80e562da59f7f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199783"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Azure 'da Dayanıklı İşlevler örnekleri yönetme

Azure Işlevleri için [dayanıklı işlevler](durable-functions-overview.md) uzantısını kullanıyorsanız veya bunu yapmaya başlamak istiyorsanız, en iyi kullanım düzeyini kullandığınızdan emin olun. Nasıl yönetileceği hakkında daha fazla bilgi edinmek için Dayanıklı İşlevler düzenleme örneklerinizi iyileştirebilirsiniz. Bu makale her örnek yönetimi işleminin ayrıntılarına gider.

Örneğin, örnekleri başlatabilir ve sonlandırabilirsiniz, ancak tüm örnekleri ve sorgu örneklerini filtre ile sorgulama özelliği de dahil olmak üzere örnekleri sorgulayabilirsiniz. Ayrıca, örneklere olay gönderebilir, düzenleme tamamlanmasını bekleyebilir ve HTTP yönetimi Web kancası URL 'Lerini alabilirsiniz. Bu makalede, örnekleri geri sarma, örnek geçmişini temizleme ve bir görev merkezini silme dahil olmak üzere diğer yönetim işlemleri de ele alınmaktadır.

Dayanıklı İşlevler, bu yönetim işlemlerinin her birini nasıl uygulamak istediğinize ilişkin seçenekleriniz vardır. Bu makalede, .NET (C#), JavaScript ve Python için [Azure Functions Core Tools](../functions-run-local.md) kullanan örnekler sağlanmaktadır.

## <a name="start-instances"></a>Başlangıç örnekleri

Bir düzenleme örneğini başlamamak önemlidir. Bu, başka bir işlevin tetikleyicisinde Dayanıklı İşlevler bağlama kullandığınızda genellikle yapılır.

`StartNewAsync` `startNew` Orchestration istemcisi bağlamasındaki (.net), (JavaScript) veya `start_new` (Python) yöntemi [](durable-functions-bindings.md#orchestration-client) yeni bir örnek başlatır. Bu yöntem, dahili olarak bir iletiyi denetim kuyruğuna sıraya alır, daha sonra [düzenleme tetikleyicisi bağlamasını](durable-functions-bindings.md#orchestration-trigger)kullanan belirtilen ada sahip bir işlevin başlangıcını tetikler.

Bu zaman uyumsuz işlem, Orchestration işlemi başarıyla zamanlandığında tamamlanır.

Yeni bir Orchestration örneği başlatmak için parametreler aşağıdaki gibidir:

* **Ad**: zamanlanacak Orchestrator işlevinin adı.
* **Giriş**: Orchestrator işlevine giriş olarak geçirilmesi gereken tüm JSON seri hale getirilebilir verileri.
* **InstanceId**: (isteğe bağlı) ÖRNEĞIN benzersiz kimliği. Bu parametreyi belirtmezseniz, yöntemi rastgele bir KIMLIK kullanır.

> [!TIP]
> Örnek KIMLIĞI için rastgele bir tanımlayıcı kullanın. Rastgele örnek kimlikleri, Orchestrator işlevlerini birden çok VM genelinde ölçeklendirirken eşit yük dağıtımına yardımcı olur. Rastgele olmayan örnek kimlikleri kullanmak için uygun zaman, KIMLIğIN bir dış kaynaktan gelmesi veya [Singleton Orchestrator](durable-functions-singletons.md) deseninin ne zaman uygulanacağı olur.

Aşağıdaki kod, yeni bir Orchestration örneği başlatan örnek bir işlevdir:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Aksi belirtilmediği takdirde, bu sayfadaki örnekler üzerinde aşağıdaki function.jsHTTP tetikleyicisini kullanır.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Bu örnek, sürüm 2. x Dayanıklı İşlevler hedefler. Sürüm 1. x içinde yerine kullanın `orchestrationClient` `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>Aksi belirtilmediği takdirde, bu sayfadaki örnekler üzerinde aşağıdaki function.jsHTTP tetikleyicisini kullanır.

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Bu örnek, sürüm 2. x Dayanıklı İşlevler hedefler. Sürüm 1. x içinde yerine kullanın `orchestrationClient` `durableClient` .

**__init__. Kopyala**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Ayrıca, [Azure Functions Core Tools](../functions-run-local.md) komutunu kullanarak doğrudan bir örnek başlatabilirsiniz `durable start-new` . Aşağıdaki parametreleri alır:

* **`function-name` (gerekli)**: başlatılacak işlevin adı.
* **`input` (isteğe bağlı)**: işleve satır ıçı veya bir JSON dosyası aracılığıyla giriş. Dosyalar için, dosyasının yolunu içeren bir ön eki ekleyin `@` `@path/to/file.json` .
* **`id` (isteğe bağlı)**: Orchestration örneğinin kimliği. Bu parametreyi belirtmezseniz, komut rastgele bir GUID kullanır.
* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer AzureWebJobsStorage ' dir.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan değer DurableFunctionsHub ' dır. Bunu, durableTask: HubName kullanarak [host.js](durable-functions-bindings.md#host-json) de ayarlayabilirsiniz.

> [!NOTE]
> Temel araçlar komutları, bunları bir işlev uygulamasının kök dizininden kullandığınızı varsayar. `connection-string-setting`Ve `task-hub-name` parametrelerini açık bir şekilde sağlarsanız, komutları herhangi bir dizinden çalıştırabilirsiniz. Bu komutları çalışan bir işlev uygulama konağı olmadan çalıştırabilmenize karşın, ana bilgisayar çalışmadığı takdirde bazı etkileri gözlemleyemiyorum olabilirsiniz. Örneğin, `start-new` komut bir başlangıç iletisini hedef görev merkezine sıralar, ancak iletiyi işleyemeyen bir işlev uygulama ana bilgisayarı işlemi olmadıkça düzenleme aslında çalışmaz.

Aşağıdaki komut HelloWorld adlı işlevi başlatır ve dosyanın içeriğini `counter-data.json` buna geçirir:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Sorgu örnekleri

Düzenlemeleri yönetme çabalarınızın bir parçası olarak, büyük olasılıkla bir düzenleme örneğinin durumu hakkında bilgi toplamanız gerekir (örneğin, normal veya başarısız olup olmadığını).

`GetStatusAsync` `getStatus` Orchestration istemci bağlamasındaki (.net), (JavaScript) veya `get_status` (Python) yöntemi bir [](durable-functions-bindings.md#orchestration-client) Orchestration örneğinin durumunu sorgular.

`instanceId`(Gerekli) ( `showHistory` isteğe bağlı), (isteğe bağlı) `showHistoryOutput` ve `showInput` (isteğe bağlı) parametreleri parametre olarak alır.

* **`showHistory`**: Olarak ayarlanırsa `true` , yanıt yürütme geçmişini içerir.
* **`showHistoryOutput`**: Olarak ayarlanırsa `true` , yürütme geçmişi etkinlik çıkışları içerir.
* **`showInput`**: Olarak ayarlanırsa `false` , yanıt işlevin girişini içermez. `true` varsayılan değerdir.

Yöntemi aşağıdaki özelliklerle bir nesne döndürür:

* **Ad**: Orchestrator işlevinin adı.
* **InstanceId**: Orchestration 'un örnek kimliği (girişle aynı olmalıdır `instanceId` ).
* **CreatedTime**: Orchestrator işlevinin çalışmaya başladığı zaman.
* **LastUpdatedTime**: Orchestration son denetim noktası zaman.
* **Giriş**: işlevin JSON değeri olarak girişi. Bu alan `showInput` , yanlışsa doldurulur.
* **Customstatus**: JSON biçiminde özel düzenleme durumu.
* **Çıktı**: işlevin JSON değeri olarak çıktısı (işlev tamamlandıysa). Orchestrator işlevi başarısız olduysa, bu özellik hata ayrıntılarını içerir. Orchestrator işlevi sonlandırıldıysa, bu özellik sonlandırma sebebini (varsa) içerir.
* **RuntimeStatus**: aşağıdaki değerlerden biri:
  * **Bekliyor**: örnek zamanlandı ancak henüz çalışmaya başlamadı.
  * **Çalışıyor**: örnek çalışmaya başladı.
  * **Tamamlandı**: örnek normal şekilde tamamlandı.
  * **Devam dasnew**: örnek, kendisini yeni bir geçmiş ile yeniden başlattı. Bu durum, geçici bir durumdur.
  * **Başarısız**: örnek bir hata vererek başarısız oldu.
  * **Sonlandırıldı**: örnek aniden durduruldu.
* **Geçmiş**: Orchestration yürütme geçmişi. Bu alan yalnızca, `showHistory` olarak ayarlanmışsa doldurulur `true` .

> [!NOTE]
> Orchestrator `Completed` , tüm zamanlanmış görevlerinin bitene _ve_ Orchestrator döndürülünceye kadar olarak işaretlenmez. Diğer bir deyişle, bir Orchestrator 'ın, olarak işaretlenmesi için bildirimine ulaşması yeterli değildir `return` `Completed` . Bu durum özellikle kullanılan durumlar için geçerlidir `WhenAny` ; Bu, genellikle `return` tüm zamanlanmış görevler yürütülmeden önce kullanılır.

`null`Örnek yoksa, bu Yöntem (.net), `undefined` (JavaScript) veya `None` (Python) döndürür.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
    // example: if status.runtimeStatus === df.OrchestrationRuntimeStatus.Running: ...
}
```

Yapılandırma function.jsiçin bkz. [Başlangıç örnekleri](#javascript-function-json) .

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
    # example: if (existing_instance.runtime_status is df.OrchestrationRuntimeStatus.Running) { ...
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Ayrıca, [Azure Functions Core Tools](../functions-run-local.md) komutunu kullanarak bir düzenleme örneğinin durumunu doğrudan almak mümkündür `durable get-runtime-status` . Aşağıdaki parametreleri alır:

* **`id` (gerekli)**: Orchestration örneğinin kimliği.
* **`show-input` (isteğe bağlı)**: olarak ayarlanırsa `true` , yanıt işlevin girdisini içerir. `false` varsayılan değerdir.
* **`show-output` (isteğe bağlı)**: olarak ayarlanırsa `true` , yanıt işlevin çıktısını içerir. `false` varsayılan değerdir.
* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer: `AzureWebJobsStorage`.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan değer: `DurableFunctionsHub`. Ayrıca, durableTask: HubName kullanılarak [host.jsüzerinde](durable-functions-bindings.md#host-json)de ayarlanabilir.

Aşağıdaki komut, 0ab8c55a66644d68a3a8b220b12d209c düzenleme örneği KIMLIĞINE sahip bir örnek için durumu (giriş ve çıkış dahil) alır. `func`İşlev uygulamasının kök dizininden komutunu çalıştırdığınız varsayılır:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

`durable get-history`Bir Orchestration örneğinin geçmişini almak için komutunu kullanabilirsiniz. Aşağıdaki parametreleri alır:

* **`id` (gerekli)**: Orchestration örneğinin kimliği.
* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer: `AzureWebJobsStorage`.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan değer: `DurableFunctionsHub`. Ayrıca, durableTask: HubName kullanılarak host.jsüzerinde de ayarlanabilir.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Tüm örnekleri sorgula

Tek seferde düzenleme yaptığınız bir örneği sorgulamak yerine, bunların tümünü aynı anda sorgulamak daha verimli olabilir.

Tüm düzenleme örneklerinin durumlarını sorgulamak için [Listınstancesasync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.net), [getstatusall](/javascript/api/durable-functions/durableorchestrationclient#getstatusall--) (JavaScript) veya `get_status_all` (Python) yöntemini kullanabilirsiniz. .NET ' te, bir `CancellationToken` nesneyi iptal etmek istediğiniz durumda geçirebilirsiniz. Yöntemi, sorgu parametreleriyle eşleşen düzenleme örneklerini temsil eden nesnelerin bir listesini döndürür.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
    
    // Note: ListInstancesAsync only returns the first page of results.
    // To request additional pages provide the result.ContinuationToken
    // to the OrchestrationStatusQueryCondition's ContinuationToken property.
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

Yapılandırma function.jsiçin bkz. [Başlangıç örnekleri](#javascript-function-json) .

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Ayrıca, [Azure Functions Core Tools](../functions-run-local.md) komutu kullanılarak örnekleri doğrudan sorgulamak de mümkündür `durable get-instances` . Aşağıdaki parametreleri alır:

* **`top` (isteğe bağlı)**: Bu komut sayfalamayı destekler. Bu parametre, istek başına alınan örnek sayısına karşılık gelir. Varsayılan değer 10 ' dur.
* **`continuation-token` (isteğe bağlı)**: hangi sayfa veya örneklerin hangi bölüme alındığını belirten bir belirteç. Her `get-instances` yürütme, bir sonraki örnek kümesine belirteç döndürür.
* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer: `AzureWebJobsStorage`.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan değer: `DurableFunctionsHub`. Ayrıca, durableTask: HubName kullanılarak [host.jsüzerinde](durable-functions-bindings.md#host-json)de ayarlanabilir.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Filtreleri olan sorgu örnekleri

Standart örnek sorgusunun sağlayabilecekleri tüm bilgilere gerçekten ihtiyaç duymuyorsanız ne yapabilirsiniz? Örneğin, düzenleme oluşturma zamanını veya Orchestration çalışma zamanı durumunu yalnızca siz arıyorsanız ne olacak? Filtre uygulayarak sorgunuzu daraltabilirsiniz.

Önceden tanımlanmış bir filtre kümesiyle eşleşen düzenleme örneklerinin bir listesini almak için [Listınstancesasync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.net) veya [getstatusby](/javascript/api/durable-functions/durableorchestrationclient#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---) (JavaScript) metodunu kullanın.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Yapılandırma function.jsiçin bkz. [Başlangıç örnekleri](#javascript-function-json) .

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2018, 3, 10, 10, 1, 0),
        datetime(2018, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Azure Functions Core Tools, `durable get-instances` komutu filtreler ile de kullanabilirsiniz. Belirtilen `top` ,,, ve parametrelerine ek olarak `continuation-token` `connection-string-setting` `task-hub-name` , üç filtre parametresi ( `created-after` , `created-before` ve `runtime-status` ) kullanabilirsiniz.

* **`created-after` (isteğe bağlı)**: Bu tarih/saat (UTC) sonrasında oluşturulan örnekleri alın. ISO 8601 biçimlendirildi tarih saat kabul edildi.
* **`created-before` (isteğe bağlı)**: Bu tarih/saat (UTC) öncesinde oluşturulan örnekleri alın. ISO 8601 biçimlendirildi tarih saat kabul edildi.
* **`runtime-status` (isteğe bağlı)**: belirli bir durumdaki örnekleri alın (örneğin, çalışıyor veya tamamlandı). Birden çok (boşlukla ayrılmış) durum sağlayabilir.
* **`top` (isteğe bağlı)**: istek başına alınan örnek sayısı. Varsayılan değer 10 ' dur.
* **`continuation-token` (isteğe bağlı)**: hangi sayfa veya örneklerin hangi bölüme alındığını belirten bir belirteç. Her `get-instances` yürütme, bir sonraki örnek kümesine belirteç döndürür.
* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer: `AzureWebJobsStorage`.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan değer: `DurableFunctionsHub`. Ayrıca, durableTask: HubName kullanılarak [host.jsüzerinde](durable-functions-bindings.md#host-json)de ayarlanabilir.

Herhangi bir filtre ( `created-after` , `created-before` , veya `runtime-status` ) sağlamazsanız, komut `top` çalışma zamanı durumu veya oluşturulma zamanına göre yalnızca örnekleri alır.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Örnekleri Sonlandır

Çalışması çok uzun süren bir düzenleme örneğiniz varsa veya herhangi bir nedenle tamamlanmadan önce bunu durdurmanız gerekiyorsa, bunu sonlandırma seçeneğine sahipsiniz.

`TerminateAsync` `terminate` `terminate` Örnekleri sonlandırmak için [Orchestration istemci bağlamasının](durable-functions-bindings.md#orchestration-client) (.net), (JavaScript) veya (Python) yöntemini kullanabilirsiniz. İki parametre `instanceId` `reason` , günlüklere ve örnek durumuna yazılan bir ve bir dizedir.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Yapılandırma function.jsiçin bkz. [Başlangıç örnekleri](#javascript-function-json) .

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "It was time to be done."
    return client.terminate(instance_id, reason)
```

---

Sonlandırılan bir örnek sonunda `Terminated` durumuna geçer. Ancak, bu geçiş hemen gerçekleşmeyecektir. Bunun yerine, sonlandırma işlemi görev hub 'ında bu örneğe ilişkin diğer işlemlerle birlikte sıraya alınır. [Örnek sorgu](#query-instances) API 'lerini kullanarak, sonlandırılmış bir örnek duruma gerçekten ulaştığında emin olabilirsiniz `Terminated` .

> [!NOTE]
> Örnek sonlandırma Şu anda yaymıyor. Etkinlik işlevleri ve alt düzenlemeler, onları çağıran düzenleme örneğini sonlandırıp sonlandırmadığına bakılmaksızın tamamlama için çalışır.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Ayrıca, [Azure Functions Core Tools](../functions-run-local.md) komutunu kullanarak bir düzenleme örneğini doğrudan sonlandırabilirsiniz `durable terminate` . Aşağıdaki parametreleri alır:

* **`id` (gerekli)**: sonlandırılacak düzenleme örneğinin kimliği.
* **`reason` (isteğe bağlı)**: sonlandırma nedeni.
* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer: `AzureWebJobsStorage`.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan değer: `DurableFunctionsHub`. Ayrıca, durableTask: HubName kullanılarak [host.jsüzerinde](durable-functions-bindings.md#host-json)de ayarlanabilir.

Aşağıdaki komut, 0ab8c55a66644d68a3a8b220b12d209c KIMLIKLI bir Orchestration örneğini sonlandırır:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Olayları örneklere gönder

Bazı senaryolarda, Orchestrator işlevlerinin dış olayları bekleyip dinleyebilmesi önemlidir. Bu, [insan etkileşimi](durable-functions-overview.md#human)için bekleyen [izleme işlevlerini](durable-functions-overview.md#monitoring) ve işlevlerini içerir.

`RaiseEventAsync` `raiseEvent` [Düzenleme istemci bağlamasının](durable-functions-bindings.md#orchestration-client)(.net) yöntemini veya (JavaScript) yöntemini kullanarak çalışan örneklere olay bildirimleri gönderin. Bu olayları işleyebilen örnekler `WaitForExternalEvent` (.net) veya `waitForExternalEvent` (JavaScript) çağrısına bir çağrı bekleyen olanlardır.

`RaiseEventAsync`(.Net) ve `raiseEvent` (JavaScript) parametreleri aşağıdaki gibidir:

* **InstanceId**: ÖRNEĞIN benzersiz kimliği.
* **EventName**: gönderileceği etkinliğin adı.
* **Eventdata**: örneğe gönderilmek üzere bir JSON seri hale getirilebilir yük.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Yapılandırma function.jsiçin bkz. [Başlangıç örnekleri](#javascript-function-json) .

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> Belirtilen örnek KIMLIĞINE sahip bir düzenleme örneği yoksa, olay iletisi atılır. Bir örnek varsa ancak henüz olay bekliyorsa, olay alınana ve işlenmek üzere hazırlanana kadar örnek durumunda depolanır.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Ayrıca, [Azure Functions Core Tools](../functions-run-local.md) komutunu kullanarak bir düzenleme örneğine doğrudan bir olay da yükseltebilirsiniz `durable raise-event` . Aşağıdaki parametreleri alır:

* **`id` (gerekli)**: Orchestration örneğinin kimliği.
* **`event-name`**: Tetikedilecek etkinliğin adı.
* **`event-data` (isteğe bağlı)**: Orchestration örneğine gönderilen veriler. Bu bir JSON dosyasının yolu olabilir veya verileri doğrudan komut satırına sağlayabilirsiniz.
* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer: `AzureWebJobsStorage`.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan değer: `DurableFunctionsHub`. Ayrıca, durableTask: HubName kullanılarak [host.jsüzerinde](durable-functions-bindings.md#host-json)de ayarlanabilir.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Düzenleme tamamlanmasını bekle

Uzun süre çalışan düzenlemeler içinde, bir düzenleme işleminin sonuçlarını beklemek ve almak isteyebilirsiniz. Bu gibi durumlarda, Orchestration üzerinde bir zaman aşımı süresi tanımlayabilmek de yararlı olur. Zaman aşımı aşılırsa, sonuçlar yerine Orchestration 'un durumu döndürülmelidir.

`WaitForCompletionOrCreateCheckStatusResponseAsync`(.Net) veya `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) yöntemi bir Orchestration örneğinden zaman uyumlu olarak gerçek çıktıyı almak için kullanılabilir. Varsayılan olarak, bu yöntemler için varsayılan değer olan 10 saniye `timeout` ve için 1 saniye kullanır `retryInterval` .  

Bu API 'nin nasıl kullanılacağını gösteren örnek bir HTTP tetikleyici işlevi aşağıda verilmiştir:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Yapılandırma function.jsiçin bkz. [Başlangıç örnekleri](#javascript-function-json) .

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

Aşağıdaki satırı kullanarak işlevi çağırın. Zaman aşımı için 2 saniye ve yeniden deneme aralığı için 0,5 saniye kullanın:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Orchestration örneğinden yanıt almak için gereken zamana bağlı olarak iki durum vardır:

* Düzenleme örnekleri tanımlı zaman aşımı (Bu durumda 2 saniye) içinde tamamlanır ve yanıt, zaman uyumlu olarak teslim edilen gerçek düzenleme örneği çıktıdır:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Düzenleme örnekleri tanımlı zaman aşımı içinde tamamlanamaz ve yanıt, [http API URL 'si bulma](durable-functions-http-api.md)bölümünde açıklanan varsayılan bir değer değildir:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Web kancası URL 'Lerinin biçimi, çalıştırdığınız Azure Işlevleri ana bilgisayarı sürümüne bağlı olarak farklılık gösterebilir. Yukarıdaki örnek, Azure Işlevleri 2,0 konağına yöneliktir.

## <a name="retrieve-http-management-webhook-urls"></a>HTTP yönetimi Web kancası URL 'Lerini alma

Bir düzenleme için olayları izlemek veya izlemek üzere bir dış sistem kullanabilirsiniz. Dış sistemler, [http API URL 'si bulma](durable-functions-http-features.md#http-api-url-discovery)bölümünde açıklanan varsayılan yanıtın parçası olan Web kancası URL 'leri aracılığıyla dayanıklı işlevler ile iletişim kurabilir. Web kancası URL 'Lerine alternatif olarak [Orchestration istemci bağlaması](durable-functions-bindings.md#orchestration-client)kullanılarak programlı bir şekilde erişilebilir. `CreateHttpManagementPayload`(.Net) veya `createHttpManagementPayload` (JavaScript) yöntemleri, bu Web kancası URL 'lerini içeren bir seri hale getirilebilir nesne almak için kullanılabilir.

`CreateHttpManagementPayload`(.Net) ve `createHttpManagementPayload` (JavaScript) yöntemlerinde bir parametre vardır:

* **InstanceId**: ÖRNEĞIN benzersiz kimliği.

Yöntemler aşağıdaki dize özelliklerine sahip bir nesne döndürür:

* **ID**: Orchestration 'un örnek kimliği (girişle aynı olmalıdır `InstanceId` ).
* **Statusquerygeturi**: Orchestration örneğinin durum URL 'si.
* **SendEventPostUri**: Orchestration örneğinin "olay oluştur" URL 'si.
* **Terminateposturi**: Orchestration örneğinin "Terminate" URL 'si.
* **PurgeHistoryDeleteUri**: Orchestration örneğinin "Temizleme geçmişi" URL 'si.

İşlevler, aşağıdaki örneklerde gösterildiği gibi, ilgili düzenleyiclerdeki olayları izlemek veya yükseltmek için bu nesnelerin örneklerini Harici sistemlere gönderebilir:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableActivityContext` `IDurableActivityContext` , özniteliği yerine özniteliğini kullanmanız gerekir `OrchestrationClient` `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

Yapılandırma function.jsiçin bkz. [Başlangıç örnekleri](#javascript-function-json) .

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>Örnekleri geri sar (Önizleme)

Beklenmeyen bir nedenden dolayı düzenleme hatası varsa, bu amaçla derlenen bir API kullanarak örneği daha önceden sağlıklı bir duruma *geri sarmaya* dönüştürebilirsiniz.

> [!NOTE]
> Bu API, doğru hata işleme ve yeniden deneme ilkelerine yönelik bir değişiklik yapmak üzere tasarlanmamıştır. Bunun yerine, yalnızca düzenleme örneklerinin beklenmeyen nedenlerle başarısız olduğu durumlarda kullanılmak üzere tasarlanmıştır. Hata işleme ve yeniden deneme ilkeleri hakkında daha fazla bilgi için bkz. [hata işleme](durable-functions-error-handling.md) makalesi.

Orchestration `RewindAsync` istemci bağlamasının (.net) veya `rewind` (JavaScript) yöntemini kullanarak [](durable-functions-bindings.md#orchestration-client) düzenlemeyi *çalışır* duruma geri yerleştirebilirsiniz. Bu yöntem, düzenleme hatasına neden olan etkinliği veya alt düzenleme yürütme hatalarını da yeniden çalıştırır.

Örneğin, bir dizi [insan onaylarını](durable-functions-overview.md#human)içeren bir iş akışınız olduğunu varsayalım. Birinin onayını gerekli olduğunu bildiren ve gerçek zamanlı yanıtı bekleyen bir dizi etkinlik işlevi olduğunu varsayalım. Tüm onay etkinlikleri yanıt aldıktan veya zaman aşımına uğradıktan sonra, geçersiz bir veritabanı bağlantı dizesi gibi uygulamanın yanlış yapılandırılması nedeniyle başka bir etkinliğin başarısız olduğunu varsayalım. Sonuç, iş akışının derinlemesine bir düzenleme hatasıdır. `RewindAsync`(.Net) veya `rewind` (JavaScript) API 'si ile, bir uygulama Yöneticisi yapılandırma hatasını giderebilir ve başarısız düzenlemeyi hatadan hemen önce geri Sara geri sarabilirler. İnsan etkileşimi adımlarının hiçbirinin yeniden onaylanması gerekmez ve düzenleme artık başarıyla tamamlanabilir.

> [!NOTE]
> *Geri sarma* özelliği, dayanıklı zamanlayıcılar kullanan düzenleme örneklerinin yeniden sarlarını desteklemez.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Yapılandırma function.jsiçin bkz. [Başlangıç örnekleri](#javascript-function-json) .

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Bu özellik şu anda Python 'da desteklenmiyor.

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Ayrıca, [Azure Functions Core Tools](../functions-run-local.md) komutunu kullanarak bir düzenleme örneğini doğrudan geri sarın `durable rewind` . Aşağıdaki parametreleri alır:

* **`id` (gerekli)**: Orchestration örneğinin kimliği.
* **`reason` (isteğe bağlı)**: Orchestration örneğini yeniden sargı nedeni.
* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer: `AzureWebJobsStorage`.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan olarak, [host.js](durable-functions-bindings.md#host-json) dosyadaki görev hub 'ı adı kullanılır.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Örnek geçmişini temizle

Bir düzenleme ile ilişkili tüm verileri kaldırmak için örnek geçmişini temizleyebilirsiniz. Örneğin, tamamlanmış bir örnekle ilişkili tüm Azure Tablo satırlarını ve büyük ileti bloblarını silmek isteyebilirsiniz. Bunu yapmak için `PurgeInstanceHistoryAsync` `purgeInstanceHistory` [Orchestration istemci bağlamasının](durable-functions-bindings.md#orchestration-client)(.net) veya (JavaScript) metodunu kullanın.

Bu yöntemin iki aşırı yüklemesi vardır. İlk aşırı yükleme, düzenleme örneğinin KIMLIĞINE göre geçmişi temizler:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Yapılandırma function.jsiçin bkz. [Başlangıç örnekleri](#javascript-function-json) .

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

Sonraki örnek, belirtilen zaman aralığından sonra tamamlanan tüm düzenleme örneklerinin geçmişini temizler bir Zamanlayıcı tarafından tetiklenen bir işlev gösterir. Bu durumda, 30 veya daha fazla gün önce tamamlanan tüm örnekler için verileri kaldırır. Her gün bir kez çalışmak üzere zamanlandı, 12:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`purgeInstanceHistoryBy`Yöntemi, birden çok örnek için örnek geçmişini koşullu olarak temizlemek üzere kullanılabilir.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Bu örnek, sürüm 2. x Dayanıklı İşlevler hedefler. Sürüm 1. x içinde yerine kullanın `orchestrationClient` `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.min
    created_time_to = datetime.today() + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> Temizleme geçmişi işleminin başarılı olması için, hedef örneğin çalışma zamanı durumu **tamamlanmalıdır**, **sonlandırılmış** veya **başarısız** olmalıdır.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools](../functions-run-local.md) komutunu kullanarak bir düzenleme örneğinin geçmişini temizleyebilirsiniz `durable purge-history` . Önceki bölümdeki ikinci C# örneğine benzer şekilde, belirtilen bir zaman aralığı boyunca oluşturulan tüm düzenleme örneklerinin geçmişini temizler. Temizlenen örnekleri çalışma zamanı durumuna göre daha fazla filtrelemek için. Komutun çeşitli parametreleri vardır:

* **`created-after` (isteğe bağlı)**: Bu tarih/saat sonra oluşturulan örneklerin GEÇMIŞINI temizle (UTC). ISO 8601 biçimlendirildi tarih saat kabul edildi.
* **`created-before` (isteğe bağlı)**: Bu tarih/saat (UTC) öncesinde oluşturulan örneklerin geçmişini temizle. ISO 8601 biçimlendirildi tarih saat kabul edildi.
* **`runtime-status` (isteğe bağlı)**: belirli bir duruma sahip örnek geçmişini (örneğin, çalışıyor veya tamamlandı) temizleyin. Birden çok (boşlukla ayrılmış) durum sağlayabilir.
* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer: `AzureWebJobsStorage`.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan olarak, [host.js](durable-functions-bindings.md#host-json) dosyadaki görev hub 'ı adı kullanılır.

Aşağıdaki komut 14 Kasım 2018, 7:35 PM (UTC) tarihinden önce oluşturulan tüm başarısız örneklerin geçmişini siler.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Bir görev hub 'ını silme

[Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` komutunu kullanarak, Azure depolama tabloları, kuyrukları ve Blobları dahil olmak üzere belirli bir görev hub 'ı ile ilişkili tüm depolama yapılarını silebilirsiniz. Komutun iki parametresi vardır:

* **`connection-string-setting` (isteğe bağlı)**: kullanılacak depolama bağlantı dizesini içeren uygulama ayarının adı. Varsayılan değer: `AzureWebJobsStorage`.
* **`task-hub-name` (isteğe bağlı)**: kullanılacak dayanıklı işlevler görev hub 'ının adı. Varsayılan olarak, [host.js](durable-functions-bindings.md#host-json) dosyadaki görev hub 'ı adı kullanılır.

Aşağıdaki komut, görev hub 'ı ile ilişkili tüm Azure depolama verilerini siler `UserTest` .

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sürüm oluşturmayı nasıl ele alabileceğinizi öğrenin](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Örnek yönetimi için yerleşik HTTP API başvurusu](durable-functions-http-api.md)

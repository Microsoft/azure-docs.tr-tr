---
title: Dayanıklı İşlevler için bağlamalar-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısı için Tetikleyiciler ve bağlamaları kullanma.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 26c9da85b27ba46e9c48bdc7872a65d452a7acb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607104"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Dayanıklı İşlevler bağlamaları (Azure Işlevleri)

[Dayanıklı işlevler](durable-functions-overview.md) uzantısı, Orchestrator ve etkinlik işlevlerinin yürütülmesini denetleyen iki yeni tetikleyici bağlama sunar. Ayrıca, Dayanıklı İşlevler çalışma zamanı için istemci görevi gören bir çıkış bağlaması da sunar.

## <a name="orchestration-trigger"></a>Düzenleme tetikleyicisi

Orchestration tetikleyicisi, [dayanıklı Orchestrator işlevlerini](durable-functions-types-features-overview.md#orchestrator-functions)yazmanıza olanak sağlar. Bu tetikleyici, yeni Orchestrator işlev örneklerinin başlamasını ve "bekleyen" bir görevi olan mevcut Orchestrator işlev örneklerinin devam ettirmeyi destekler.

Azure Işlevleri için Visual Studio Araçları 'nı kullandığınızda Orchestration tetikleyicisi, [Orchestrationtriggerattribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .net özniteliği kullanılarak yapılandırılır.

Komut dosyası dillerinde Orchestrator işlevleri yazdığınızda (örneğin, JavaScript veya C# komut dosyası), düzenleme tetikleyicisi `bindings` dosyadaki *function.js* dizisinde aşağıdaki JSON nesnesi tarafından tanımlanır:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` , istemcilerin bu Orchestrator işlevinin yeni örneklerini başlatmak istediklerinde kullanması gereken Orchestration 'un adıdır. Bu özellik isteğe bağlıdır. Belirtilmemişse, işlevin adı kullanılır.

Dahili olarak bu tetikleyici bağlama, işlev uygulaması için varsayılan depolama hesabındaki bir dizi kuyruğu yoklar. Bu kuyruklar, uzantının iç uygulama ayrıntılardır ve bu nedenle bağlama özelliklerinde açıkça yapılandırılmazlar.

### <a name="trigger-behavior"></a>Tetikleyici davranışı

Düzenleme tetikleyicisiyle ilgili bazı notlar aşağıda verilmiştir:

* **Tek iş parçacığı** oluşturma-tek bir konak örneğindeki tüm Orchestrator işlev yürütmesi için tek bir dağıtıcı iş parçacığı kullanılır. Bu nedenle, Orchestrator işlev kodunun etkin olduğundan ve herhangi bir g/ç gerçekleştirmediğinden emin olmak önemlidir. Bu iş parçacığının Dayanıklı İşlevler özel görev türlerinde beklerken zaman uyumsuz bir iş gerektirmediğinden emin olmak da önemlidir.
* **Zehirli ileti işleme** -düzenleme tetikleyicilerinde bir zarar iletisi desteği yoktur.
* **İleti görünürlüğü** -düzenleme tetikleyici iletileri kuyruğa alınır ve yapılandırılabilir bir süre boyunca görünmez tutulur. İşlev uygulaması çalıştığı ve sağlıklı olduğu sürece bu iletilerin görünürlüğü otomatik olarak yenilenir.
* **Dönüş değerleri** -dönüş değerleri JSON olarak serileştirilir ve Azure Tablo depolama alanındaki Orchestration geçmişi tablosunda kalıcı hale getirilir. Bu dönüş değerleri, daha sonra açıklanan Orchestration istemci bağlaması tarafından sorgulanabilir.

> [!WARNING]
> Orchestrator işlevleri, düzenleme tetikleyicisi bağlaması dışında herhangi bir giriş veya çıkış bağlamasını asla kullanmamalıdır. Bunun yapılması, bu bağlamalar tek iş parçacığı ve g/ç kurallarına uymadığı için dayanıklı görev uzantısıyla ilgili sorunlara neden olabilir. Diğer bağlamaları kullanmak istiyorsanız, bunları Orchestrator işlevinizden çağrılan bir etkinlik işlevine ekleyin.

> [!WARNING]
> JavaScript Orchestrator işlevleri asla bildirilmelidir `async` .

### <a name="trigger-usage-net"></a>Kullanım tetiklemesi (.NET)

Düzenleme tetikleyicisi bağlaması hem giriş hem de çıkışları destekler. Giriş ve çıkış işleme hakkında daha fazla bilgi edinmek için aşağıdaki noktalara dikkat edin:

* **girişler** -.net Orchestration işlevleri yalnızca `DurableOrchestrationContext` bir parametre türü olarak desteklenir. Doğrudan işlev imzasında girişlerin serisini kaldırma desteklenmez. Kod, `GetInput<T>` `getInput` Orchestrator işlev girişlerini getirmek için (.net) veya (JavaScript) yöntemini kullanmalıdır. Bu girişler JSON serileştirilebilir türler olmalıdır.
* **çıktılar** -düzenleme Tetikleyicileri, çıkış değerlerinin yanı sıra girdileri de destekler. İşlevin dönüş değeri, çıkış değerini atamak için kullanılır ve JSON ile seri hale getirilebilir olmalıdır. Bir .NET işlevi veya döndürürse `Task` `void` , bir `null` değer çıktı olarak kaydedilir.

### <a name="trigger-sample"></a>Tetikleyici örneği

Aşağıdaki örnek kod, en basit "Merhaba Dünya" Orchestrator işlevinin nasıl görünebileceğini göstermektedir:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Önceki kod Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

#### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> `context`JavaScript içindeki nesne, DurableOrchestrationContext, ancak [işlev bağlamını bir bütün olarak](../functions-reference-node.md#context-object)temsil etmez. Düzenleme yöntemlerine `context` nesnenin özelliği aracılığıyla erişebilirsiniz `df` .

> [!NOTE]
> JavaScript düzenleyicilerinin kullanması gerekir `return` . `durable-functions`Kitaplık, yöntemi çağırma işlemini gerçekleştirir `context.done` .

Çoğu Orchestrator işlevi etkinlik işlevlerini çağırır, bu nedenle bir etkinlik işlevinin nasıl çağrılacağını gösteren bir "Merhaba Dünya" örneği aşağıda verilmiştir:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Önceki kod Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableOrchestrationContext` `IDurableOrchestrationContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

#### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Etkinlik tetikleyicisi

Etkinlik tetikleyicisi, [etkinlik işlevleri](durable-functions-types-features-overview.md#activity-functions)olarak bilinen Orchestrator işlevleri tarafından çağrılan işlevleri yazmanıza olanak sağlar.

Visual Studio kullanıyorsanız, etkinlik tetikleyicisi `ActivityTriggerAttribute` .net özniteliği kullanılarak yapılandırılır.

Geliştirme için VS Code veya Azure portal kullanıyorsanız, etkinlik tetikleyicisi `bindings` *üzerindefunction.js* dizisinde aşağıdaki JSON nesnesi tarafından tanımlanır:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` etkinliğin adıdır. Bu değer, Orchestrator işlevlerinin bu etkinlik işlevini çağırmak için kullandığı addır. Bu özellik isteğe bağlıdır. Belirtilmemişse, işlevin adı kullanılır.

Dahili olarak bu tetikleyici bağlama, işlev uygulaması için varsayılan depolama hesabındaki bir kuyruğu yoklar. Bu kuyruk, uzantının iç uygulama ayrıntısı olduğundan, bağlama özelliklerinde açıkça yapılandırılmamış olabilir.

### <a name="trigger-behavior"></a>Tetikleyici davranışı

Etkinlik tetikleyicisiyle ilgili bazı notlar aşağıda verilmiştir:

* **Iş parçacığı** oluşturma tetikleyicisinden farklı olarak, etkinlik tetikleyicilerinin iş parçacığı veya g/ç etrafında herhangi bir kısıtlama yoktur. Bunlar, normal işlevler gibi davranılanırlar.
* **Zehirli ileti işleme** -etkinlik tetikleyicilerinde bir zarar iletisi desteği yoktur.
* **İleti görünürlüğü** -etkinlik tetikleyicisi iletileri kuyruğa alınır ve yapılandırılabilir bir süre boyunca görünmez tutulur. İşlev uygulaması çalıştığı ve sağlıklı olduğu sürece bu iletilerin görünürlüğü otomatik olarak yenilenir.
* **Dönüş değerleri** -dönüş değerleri JSON olarak serileştirilir ve Azure Tablo depolama alanındaki Orchestration geçmişi tablosunda kalıcı hale getirilir.

> [!WARNING]
> Etkinlik işlevleri için depolama arka ucu, bir uygulama ayrıntısı ve Kullanıcı kodu bu depolama varlıklarıyla doğrudan etkileşmemelidir.

### <a name="trigger-usage-net"></a>Kullanım tetiklemesi (.NET)

Etkinlik tetikleyicisi bağlaması, tıpkı düzenleme tetikleyicisi gibi giriş ve çıkışları destekler. Giriş ve çıkış işleme hakkında daha fazla bilgi edinmek için aşağıdaki noktalara dikkat edin:

* **girişler** -.net etkinliği işlevleri yerel `DurableActivityContext` olarak parametre türü olarak kullanılır. Alternatif olarak, bir etkinlik işlevi JSON ile seri hale getirilebilir olan herhangi bir parametre türüyle bildirilebilecek. Kullandığınızda `DurableActivityContext` , `GetInput<T>` etkinlik işlevi girişini getirmek ve serisini kaldırmak için öğesini çağırabilirsiniz.
* **çıktılar** -etkinlik işlevleri, çıkış değerlerinin yanı sıra girdileri de destekler. İşlevin dönüş değeri, çıkış değerini atamak için kullanılır ve JSON ile seri hale getirilebilir olmalıdır. Bir .NET işlevi veya döndürürse `Task` `void` , bir `null` değer çıktı olarak kaydedilir.
* **meta veri** -.net etkinlik işlevleri, `string instanceId` üst düzenleme örnek kimliğini almak için bir parametreye bağlanabilir.

### <a name="trigger-sample"></a>Tetikleyici örneği

Aşağıdaki örnek kod, basit bir "Merhaba Dünya" Activity işlevinin nasıl görünebileceğini göstermektedir:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Önceki kod Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için yerine kullanmanız gerekir `DurableActivityContext` `IDurableActivityContext` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

.NET bağlaması için varsayılan parametre türü ' `ActivityTriggerAttribute` dir `IDurableActivityContext` . Ancak, .NET etkinliği Tetikleyicileri doğrudan JSON-serializlenebilir türlere (ilkel türler dahil) bağlamayı da destekler, bu nedenle aynı işlev aşağıdaki gibi basitleştirilebilir:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript bağlamaları ek parametreler olarak da geçirilebilir, bu nedenle aynı işlev şu şekilde basitleştirilebilir:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Giriş ve çıkış bağlamalarını kullanma

Etkinlik tetikleyicisi bağlamasının yanı sıra normal giriş ve çıkış bağlamalarını de kullanabilirsiniz. Örneğin, etkinlik Bağlamalarınızın girişini alabilir ve EventHub çıkış bağlamasını kullanarak bir EventHub 'e ileti gönderebilirsiniz:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Orchestration istemcisi

Orchestration istemci bağlaması, Orchestrator işlevleriyle etkileşime geçen işlevler yazmanızı sağlar. Bu işlevler bazen [istemci işlevleri](durable-functions-types-features-overview.md#client-functions)olarak adlandırılır. Örneğin, düzenleme örnekleri üzerinde aşağıdaki yollarla işlem yapabilirsiniz:

* Bunları başlatın.
* Durumlarını sorgulayın.
* Sonlandırın.
* Olayları çalışırken onlara gönderin.
* Örnek geçmişini temizle.

Visual Studio kullanıyorsanız, `OrchestrationClientAttribute` Dayanıklı İşlevler 1,0 için .net özniteliğini kullanarak Orchestration istemcisine bağlanabilirsiniz. Dayanıklı İşlevler 2,0 ' den başlayarak, .net özniteliğini kullanarak Orchestration istemcisine bağlanabilirsiniz `DurableClientAttribute` .

Geliştirme için betik dilleri (örneğin, *. CSX* veya *. js* dosyaları) kullanıyorsanız, düzenleme tetikleyicisi `bindings` *üzerindefunction.js* dizisinde aşağıdaki JSON nesnesi tarafından tanımlanır:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` -Birden çok işlevli uygulamanın aynı depolama hesabını paylaştığı ancak birbirinden yalıtılması gereken senaryolarda kullanılır. Belirtilmemişse, varsayılan değer `host.json` kullanılır. Bu değer, hedef Orchestrator işlevleri tarafından kullanılan değerle aynı olmalıdır.
* `connectionName` -Depolama hesabı bağlantı dizesi içeren bir uygulama ayarının adı. Bu bağlantı dizesi tarafından temsil edilen depolama hesabı, hedef Orchestrator işlevlerinin kullandığı aynı olmalıdır. Belirtilmemişse, işlev uygulaması için varsayılan depolama hesabı bağlantı dizesi kullanılır.

> [!NOTE]
> Çoğu durumda, bu özellikleri atlamanızı ve varsayılan davranışa güvenmenizi öneririz.

### <a name="client-usage"></a>İstemci kullanımı

.NET işlevlerinde, genellikle `IDurableOrchestrationClient` dayanıklı işlevler tarafından desteklenen tüm Orchestration Istemci API 'lerine tam erişim sağlayan öğesine bağlayabilirsiniz. Daha eski Dayanıklı İşlevler 2. x sürümleriyle, bunun yerine sınıfına bağlamanız gerekir `DurableOrchestrationClient` . JavaScript 'te, aynı API 'Ler tarafından döndürülen nesne tarafından gösterilir `getClient` . İstemci nesnesindeki API 'Ler şunları içerir:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternatif olarak, .NET işlevleri, `IAsyncCollector<T>` veya nerede `T` olduğunu de bağlayabilir `StartOrchestrationArgs` `JObject` .

Bu işlemler hakkında daha fazla bilgi için `IDurableOrchestrationClient` API belgelerine bakın.

### <a name="client-sample-visual-studio-development"></a>İstemci örneği (Visual Studio geliştirme)

Aşağıda, "HelloWorld" düzenlemesini başlatan örnek bir Queue-tetiklenen işlev verilmiştir.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Önceki C# kodu Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `OrchestrationClient` özniteliği yerine özniteliği kullanmanız gerekir `DurableClient` ve `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

### <a name="client-sample-not-visual-studio"></a>İstemci örneği (Visual Studio değil)

Geliştirme için Visual Studio kullanmıyorsanız, dosyasında aşağıdaki *function.js* oluşturabilirsiniz. Bu örnek, dayanıklı düzenleme istemci bağlamasını kullanan bir Queue-tetiklenen işlevin nasıl yapılandırılacağını gösterir:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Önceki JSON Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için, `orchestrationClient` tetikleyici türü olarak yerine kullanmanız gerekir `durableClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

Yeni Orchestrator işlev örnekleri Başlatan dile özgü örnekler aşağıda verilmiştir.

#### <a name="c-script-sample"></a>C# betik örneği

Aşağıdaki örnek, bir Queue-tetiklenen C# işlevinden yeni bir işlev örneğini başlatmak için dayanıklı düzenleme istemci bağlamasının nasıl kullanılacağını gösterir:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Önceki kod Dayanıklı İşlevler 2. x içindir. Dayanıklı İşlevler 1. x için `DurableOrchestrationClient` yerine parametre türünü kullanmanız gerekir `IDurableOrchestrationClient` . Sürümler arasındaki farklılıklar hakkında daha fazla bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

#### <a name="javascript-sample"></a>JavaScript örneği

Aşağıdaki örnek, bir JavaScript işlevinden yeni bir işlev örneği başlatmak için dayanıklı düzenleme istemci bağlamasının nasıl kullanılacağını gösterir:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

[Örnek yönetimi](durable-functions-instance-management.md)'nde, başlatma örnekleri hakkında daha fazla ayrıntı bulunabilir.

## <a name="entity-trigger"></a>Varlık tetikleyicisi

Varlık Tetikleyicileri, [varlık işlevleri](durable-functions-entities.md)yazmanıza izin verir. Bu tetikleyici belirli bir varlık örneği için olayları işlemeyi destekler.

Azure Işlevleri için Visual Studio Araçları 'nı kullandığınızda, varlık tetikleyicisi `EntityTriggerAttribute` .net özniteliği kullanılarak yapılandırılır.

> [!NOTE]
> Varlık Tetikleyicileri Dayanıklı İşlevler 2. x ile başlayarak kullanılabilir.

Dahili olarak bu tetikleyici bağlama, işlev uygulaması için varsayılan depolama hesabındaki bir dizi kuyruğu yoklar. Bu kuyruklar, uzantının iç uygulama ayrıntılardır ve bu nedenle bağlama özelliklerinde açıkça yapılandırılmazlar.

### <a name="trigger-behavior"></a>Tetikleyici davranışı

Varlık tetikleyicisiyle ilgili bazı notlar aşağıda verilmiştir:

* **Tek iş parçacıklı**: belirli bir varlığa yönelik işlemleri işlemek için tek bir dağıtıcı iş parçacığı kullanılır. Aynı anda birden fazla ileti tek bir varlığa gönderilirse, işlemler bir kerelik olarak işlenir.
* **Zehirli ileti işleme** -varlık tetikleyicilerinde bir zarar iletisi desteği yoktur.
* **İleti görünürlüğü** -varlık tetikleyici iletileri kuyruğa alınır ve yapılandırılabilir bir süre boyunca görünmez tutulur. İşlev uygulaması çalıştığı ve sağlıklı olduğu sürece bu iletilerin görünürlüğü otomatik olarak yenilenir.
* **Dönüş değerleri** -varlık işlevleri dönüş değerlerini desteklemiyor. Durumu kaydetmek veya değerleri düzenlemeye geri geçirmek için kullanılabilecek belirli API 'Ler vardır.

Yürütme işlemi sırasında bir varlıkta yapılan herhangi bir durum değişikliği, yürütme tamamlandıktan sonra otomatik olarak kalıcı hale getirilir.

### <a name="trigger-usage-net"></a>Kullanım tetiklemesi (.NET)

Her varlık işlevinin `IDurableEntityContext` , aşağıdaki üyelere sahip olan parametre türü vardır:

* **EntityName**: Şu anda yürütülmekte olan varlığın adı.
* **EntityKey**: Şu anda yürütülmekte olan varlığın anahtarı.
* **EntityId**: Şu anda yürütülmekte olan varlığın kimliği.
* **OperationName**: geçerli işlemin adı.
* **Hasstate**: varlığın var olup olmadığı, yani bir durumu var. 
* **GetState \<TState> ()**: varlığın geçerli durumunu alır. Zaten mevcut değilse, oluşturulur ve olarak başlatılır `default<TState>` . `TState`Parametre bir ilkel veya JSON seri hale getirilen tür olmalıdır. 
* **GetState \<TState> (ınitfunction)**: varlığın geçerli durumunu alır. Zaten mevcut değilse, belirtilen parametresi çağırarak oluşturulur `initfunction` . `TState`Parametre bir ilkel veya JSON seri hale getirilen tür olmalıdır. 
* **Setstate (arg)**: varlığın durumunu oluşturur veya güncelleştirir. `arg`PARAMETRENIN JSON-serializlenebilir nesne veya ilkel olması gerekir.
* **DeleteState ()**: varlığın durumunu siler. 
* **Getınput \<TInput> ()**: geçerli işlem için girişi alır. `TInput`Tür parametresi, ilkel veya JSON seri hale getirilen bir tür olmalıdır.
* **Return (arg)**: işlemi çağıran düzenleme için bir değer döndürür. `arg`Parametre bir ilkel veya JSON-serializlenebilir nesne olmalıdır.
* **İmza (EntityId, scheduledTimeUtc, işlem, giriş)**: bir varlığa tek yönlü bir ileti gönderir. `operation`Parametre null olmayan bir dize olmalıdır; isteğe bağlı, `scheduledTimeUtc` işlemin ÇAĞıRALıNACAĞı UTC Tarih/saat olması gerekir ve `input` parametre BIR basit veya JSON-serializlenebilir nesne olmalıdır.
* **CreateNewOrchestration (Orchestratorfonksiyonadı, giriş)**: yeni bir düzenleme başlatır. `input`Parametre bir ilkel veya JSON-serializlenebilir nesne olmalıdır.

`IDurableEntityContext`Varlık işlevine geçirilen nesneye `Entity.Current` Async-Local özelliği kullanılarak erişilebilir. Bu yaklaşım, sınıf tabanlı programlama modeli kullanılırken kullanışlıdır.

### <a name="trigger-sample-c-function-based-syntax"></a>Trigger örneği (C# işlev tabanlı sözdizimi)

Aşağıdaki kod, dayanıklı bir işlev olarak uygulanan basit bir *sayaç* varlığına bir örnektir. Bu işlev, `add` `reset` `get` her biri bir tamsayı durumu üzerinde çalışan üç işlem,,, ve tanımlar.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

İşlev tabanlı sözdizimi ve nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Işlev tabanlı sözdizimi](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Trigger örneği (C# sınıf tabanlı sözdizimi)

Aşağıdaki örnek, `Counter` sınıfları ve yöntemleri kullanarak varlığın eşdeğer bir uygulamasıdır.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Bu varlığın durumu `Counter` , sayacın geçerli değerini depolayan bir alan içeren türünde bir nesnedir. Bu nesneyi depolamada kalıcı hale getirmek için, [JSON.net](https://www.newtonsoft.com/json) kitaplığı tarafından serileştirilmiş ve seri durumdan çıkarılmış olur. 

Sınıf tabanlı sözdizimi ve nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [varlık sınıfları tanımlama](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> `[FunctionName]`Varlık sınıfları kullanılırken özniteliği olan işlev giriş noktası  yöntemi bildirilmelidir `static` . Statik olmayan giriş noktası yöntemleri, birden fazla nesne başlatmaya ve olasılıkla diğer tanımsız davranışlara neden olabilir.

Varlık sınıfları bağlamalarla ve .NET bağımlılığı ekleme ile etkileşim kurmak için özel mekanizmalarda yer vardır. Daha fazla bilgi için bkz. [varlık oluşturma](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Tetikleyici örneği (JavaScript)

Aşağıdaki kod, JavaScript 'te yazılmış dayanıklı bir işlev olarak uygulanan basit bir *sayaç* varlığına bir örnektir. Bu işlev, `add` `reset` `get` her biri bir tamsayı durumu üzerinde çalışan üç işlem,,, ve tanımlar.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Kalıcı varlıklar, NPM paketinin **1.3.0** sürümünden başlayarak JavaScript 'te kullanılabilir `durable-functions` .

## <a name="entity-client"></a>Varlık istemcisi

Varlık istemci bağlaması, [varlık işlevlerini](#entity-trigger)zaman uyumsuz olarak tetiklemenize olanak sağlar. Bu işlevler bazen [istemci işlevleri](durable-functions-types-features-overview.md#client-functions)olarak adlandırılır.

Visual Studio kullanıyorsanız, .net özniteliğini kullanarak Entity Client 'a bağlanabilirsiniz `DurableClientAttribute` .

> [!NOTE]
> `[DurableClientAttribute]` [Orchestration istemcisine](#orchestration-client)bağlamak için de kullanılabilir.

Geliştirme için betik dilleri (örneğin, *. CSX* veya *. js* dosyaları) kullanıyorsanız, varlık tetikleyicisi, `bindings` *üzerindefunction.js* dizisinde aşağıdaki JSON nesnesi tarafından tanımlanır:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` -Birden çok işlevli uygulamanın aynı depolama hesabını paylaştığı ancak birbirinden yalıtılması gereken senaryolarda kullanılır. Belirtilmemişse, varsayılan değer `host.json` kullanılır. Bu değer, hedef varlık işlevleri tarafından kullanılan değerle eşleşmelidir.
* `connectionName` -Depolama hesabı bağlantı dizesi içeren bir uygulama ayarının adı. Bu bağlantı dizesi tarafından temsil edilen depolama hesabı, hedef varlık işlevleri tarafından kullanılan bir aynı olmalıdır. Belirtilmemişse, işlev uygulaması için varsayılan depolama hesabı bağlantı dizesi kullanılır.

> [!NOTE]
> Çoğu durumda, isteğe bağlı özellikleri atlamanızı ve varsayılan davranışa güvenmenizi öneririz.

### <a name="entity-client-usage"></a>Varlık istemci kullanımı

.NET işlevlerinde, genellikle `IDurableEntityClient` , dayanıklı varlıkların desteklediği tüm Istemci API 'lerine tam erişim sağlayan öğesine bağlayabilirsiniz. Ayrıca `IDurableOrchestrationClient` , her iki varlık ve düzenleme için Istemci API 'lerine erişim sağlayan arabirimine de bağlanabilirsiniz. İstemci nesnesindeki API 'Ler şunları içerir:

* **Readentitystateasync \<T>**: bir varlığın durumunu okur. Hedef varlığın mevcut olup olmadığını ve Öyleyse durumunun ne olduğunu belirten bir yanıt döndürür.
* **Tiflentityasync**: bir varlığa tek yönlü bir ileti gönderir ve kuyruğa alınıp alınmasını bekler.
* **Listentitiesasync**: birden çok varlığın durumu için sorgular. Varlıklar, *ad* ve *son işlem zamanına* göre sorgulanabilir.

Bir sinyal göndermeden önce hedef varlık oluşturmanız gerekmez; varlık durumu, sinyali işleyen varlık işlevinin içinden oluşturulabilir.

> [!NOTE]
> İstemciden gönderilen "sinyaller", daha sonra zaman uyumsuz olarak işlenmek üzere sıraya alınmış olduğunu anlamak önemlidir. Özellikle, `SignalEntityAsync` genellikle varlık işlemden önce döndürülür ve dönüş değerini geri almak veya özel durumları gözlemlemek mümkün değildir. Daha güçlü garantiler gerekliyse (örneğin, iş akışları için), *Orchestrator işlevlerinin* kullanılması gerekir ve bu da varlık işlemlerinin tamamlanmasını bekleyebilir ve dönüş değerlerini işleyebilir ve özel durumları gözlemleyebilirsiniz.

### <a name="example-client-signals-entity-directly---c"></a>Örnek: istemci varlığına doğrudan-C sinyalleri bildirir #

Aşağıda, bir "Counter" varlığı çağıran, kuyruğa geçirilmiş örnek bir işlev verilmiştir.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>Örnek: istemci, bir arabirim-C aracılığıyla varlığa işaret eder #

Mümkün olduğunda, daha fazla tür denetimi sağladığından [varlıklara arabirimler üzerinden erişmenizi](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) öneririz. Örneğin, `Counter` daha önce bahsedilen varlığın `ICounter` aşağıdaki gibi tanımlanmış bir arabirim uyguladığını varsayalım:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

İstemci kodu daha sonra `SignalEntityAsync<ICounter>` tür açısından güvenli bir ara sunucu oluşturmak için kullanılabilir:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

`proxy`Parametresi, `ICounter` ' nin çağrısını iç olarak `Add` eşdeğer (türsüz) çağrısına çeviren, dinamik olarak üretilmiş bir örneğidir `SignalEntityAsync` .

> [!NOTE]
> `SignalEntityAsync`API 'ler tek yönlü işlemleri temsil eder. Bir varlık arabirimleri döndürürse `Task<T>` , `T` parametresinin değeri her zaman null veya olur `default` .

Özellikle, `Get` hiçbir değer döndürülmediğinden işlemin işaret etmek mantıklı değildir. Bunun yerine, istemciler `ReadStateAsync` sayaç durumuna doğrudan erişmek için ya da işlemi çağıran bir Orchestrator işlevini başlatabilir `Get` .

### <a name="example-client-signals-entity---javascript"></a>Örnek: istemci sinyalleri varlığı-JavaScript

JavaScript 'te bir "Counter" varlığına işaret eden örnek bir Queue-tetiklenen işlev aşağıda verilmiştir.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
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

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Kalıcı varlıklar, NPM paketinin **1.3.0** sürümünden başlayarak JavaScript 'te kullanılabilir `durable-functions` .

<a name="host-json"></a>
## <a name="hostjson-settings"></a>Ayarlar üzerinde host.js

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Örnek yönetimi için yerleşik HTTP API başvurusu](durable-functions-http-api.md)

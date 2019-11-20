---
title: Dayanıklı varlıklar-Azure Işlevleri
description: Dayanıklı varlıkların ne olduğunu ve Azure Işlevleri için Dayanıklı İşlevler uzantısı 'nda nasıl kullanılacağını öğrenin.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d62281b4ccb522b3a784428bcf0284730f120628
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904036"
---
# <a name="entity-functions"></a>Varlık işlevleri

Varlık işlevleri, *dayanıklı varlıklar*olarak bilinen küçük durum parçalarını okumak ve güncelleştirmek için işlemleri tanımlar. Orchestrator işlevleri gibi, varlık işlevleri de özel tetikleyici türü olan işlevlerdir, *varlık tetikleyicisi*. Orchestrator işlevlerinin aksine, varlık işlevleri, durumu denetim akışı aracılığıyla örtük olarak temsil etmek yerine bir varlığın durumunu açıkça yönetir.
Varlıklar, her biri modestly boyutlu bir duruma sahip birçok varlık genelinde iş dağıtarak uygulamaları ölçeklendirmek için bir yol sağlar.

> [!NOTE]
> Varlık işlevleri ve ilgili işlevler yalnızca Dayanıklı İşlevler 2,0 ve üzeri sürümlerde kullanılabilir.

## <a name="general-concepts"></a>Genel kavramlar

Varlıklar, iletiler üzerinden iletişim kuran çok küçük hizmetler gibi davranır. Her varlığın benzersiz bir kimliği ve bir iç durumu vardır (varsa). Hizmetler veya nesneler gibi varlıklar, istendiğinde işlemler gerçekleştirir. Bir işlem yürütüldüğünde, varlığın iç durumunu güncelleştirebilir. Ayrıca, dış Hizmetleri çağırıp bir yanıt bekler. Varlıklar, güvenilir kuyruklar aracılığıyla örtük olarak gönderilen iletileri kullanarak diğer varlıklar, düzenlemeler ve istemcilerle iletişim kurar. 

Çakışmaları engellemek için, tek bir varlıktaki tüm işlemlerin, diğer bir deyişle, diğer bir deyişle, bir diğeri de yürütülmesi garanti edilir. 

### <a name="entity-id"></a>Varlık KIMLIĞI
Varlıklara benzersiz bir tanımlayıcı, *VARLıK kimliği*üzerinden erişilir. Bir varlık KIMLIĞI, bir varlık örneğini benzersiz bir şekilde tanımlayan dizelerin bir çiftidir. Aşağıdakilerden oluşur:

* Varlığın türünü tanımlayan bir ad olan **varlık adı**. Örnek olarak "Counter" bir örnektir. Bu ad, varlığı uygulayan varlık işlevinin adı ile aynı olmalıdır. Büyük/küçük harfe duyarlı değildir.
* Aynı ada sahip diğer tüm varlıklar arasında varlığı benzersiz bir şekilde tanımlayan bir dize olan **varlık anahtarı**. Bir GUID örneğidir.

Örneğin, bir `Counter` varlık işlevi çevrimiçi bir oyunda puanı korumak için kullanılabilir. Oyunun her örneğinin, `@Counter@Game1` ve `@Counter@Game2`gibi benzersiz bir varlık KIMLIĞI vardır. Belirli bir varlığı hedefleyen tüm işlemler, bir varlık KIMLIĞINI parametre olarak belirtmeyi gerektirir.

### <a name="entity-operations"></a>Varlık işlemleri ###

Bir varlıkta bir işlem çağırmak için şunu belirtin:

* Hedef varlığın **VARLıK kimliği** .
* **İşlem adı**, gerçekleştirilecek işlemi belirten bir dizedir. Örneğin, `Counter` varlık `add`, `get`veya `reset` işlemlerini destekleyebilir.
* İşlem için isteğe bağlı bir giriş parametresi olan **işlem girişi**. Örneğin, ekleme işlemi girdi olarak bir tamsayı miktarı alabilir.

İşlemler bir sonuç değeri veya bir JavaScript hatası ya da .NET özel durumu gibi bir hata sonucu döndürebilir. Bu sonuç veya hata, işlemi çağıran uyarlamalar tarafından gözlemlenebilir.

Bir varlık işlemi varlık durumunu da oluşturabilir, okuyabilir, güncelleştirebilir ve silebilir. Varlığın durumu her zaman depolamada kalıcı olarak kalıcı hale getirilir.

## <a name="define-entities"></a>Varlıkları tanımlama

Şu anda varlıkları tanımlamaya yönelik iki ayrı API şunlardır:

**İşlev tabanlı sözdizimi**, varlıkların işlevler olarak temsil edildiği ve uygulamalar tarafından açıkça dağıtılan işlev. Bu söz dizimi basit durum, birkaç işlem veya uygulama çerçeveleri gibi dinamik bir işlem kümesi olan varlıklar için iyi sonuç verir. Bu söz dizimi, derleme zamanında tür hatalarını yakalayamadığından sürdürmek sıkıcı olabilir.

Varlıkların ve işlemlerin sınıflar ve yöntemlerle temsil edildiği **sınıf tabanlı sözdizimi**. Bu sözdizimi daha kolay okunabilir kod üretir ve işlemlerin tür açısından güvenli bir şekilde çağrılmasına izin verir. Sınıf tabanlı sözdizimi, işlev tabanlı sözdiziminin üzerinde ince bir katmandır, bu nedenle her iki çeşit de aynı uygulamadaki birbirlerinin yerine kullanılabilir.

### <a name="example-function-based-syntax---c"></a>Örnek: Işlev tabanlı sözdizimi-C#

Aşağıdaki kod, dayanıklı bir işlev olarak uygulanan basit bir `Counter` varlığına bir örnektir. Bu işlev, her biri bir tamsayı durumu üzerinde çalışan üç işlem, `add`, `reset`ve `get`tanımlar.

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

İşlev tabanlı sözdizimi ve nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [işlev tabanlı sözdizimi](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Örnek: sınıf tabanlı sözdizimi-C#

Aşağıdaki örnek, sınıfları ve yöntemleri kullanarak `Counter` varlığın eşdeğer bir uygulamasıdır.

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

Bu varlığın durumu, sayacın geçerli değerini depolayan bir alan içeren `Counter`türünde bir nesnedir. Bu nesneyi depolamada kalıcı hale getirmek için, [JSON.net](https://www.newtonsoft.com/json) kitaplığı tarafından serileştirilmiş ve seri durumdan çıkarılmış olur. 

Sınıf tabanlı sözdizimi ve nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [varlık sınıfları tanımlama](durable-functions-dotnet-entities.md#defining-entity-classes).

### <a name="example-javascript-entity"></a>Örnek: JavaScript varlığı

Dayanıklı varlıklar, `durable-functions` NPM paketinin sürüm **1.3.0** Ile başlayan JavaScript 'te kullanılabilir. Aşağıdaki kod, JavaScript 'te yazılmış dayanıklı bir işlev olarak uygulanan `Counter` varlıktır.

**function. JSON**
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

**index. js**
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

## <a name="access-entities"></a>Erişim varlıkları

Varlıklara, tek yönlü veya çift yönlü iletişim kullanılarak erişilebilir. Aşağıdaki terminoloji iki iletişim formunu ayırır: 

* Bir varlığı **çağırmak** iki yönlü (gidiş dönüş) iletişimini kullanır. Varlığa bir işlem iletisi gönderir ve devam etmeden önce yanıt iletisini bekleyin. Yanıt iletisi bir sonuç değeri veya bir JavaScript hatası ya da bir .NET özel durumu gibi bir hata sonucu sağlayabilir. Bu sonuç veya hata, çağıran tarafından izlenir.
* Bir varlığın **sinyal sinyali** , tek yönlü (ateş ve unutma) iletişimi kullanır. İşlem iletisi gönderir ancak yanıt bekleyemez. İletinin sonunda teslim edilmesi garanti edilirken gönderici, hiçbir sonuç değerini veya hatayı ne zaman gözlemlemediğini bilmez.

Varlıklar, Orchestrator işlevleri içinden veya varlık işlevleri içinden istemci işlevleri içinden erişilebilir. Tüm iletişim biçimleri tüm bağlamlar tarafından desteklenmez:

* İstemcilerin içinden varlıklara sinyal verebilir ve varlık durumunu okuyabilirsiniz.
* Düzenleyicmeler içinden varlıkları işaret edebilir ve varlıkları çağırabilirsiniz.
* Varlıkların içinden varlıklara sinyal getirebilirsiniz.

Aşağıdaki örneklerde varlıklara erişmenin çeşitli yolları gösterilmektedir.

> [!NOTE]
> Basitlik için aşağıdaki örneklerde varlıklara erişim için gevşek yazılmış sözdizimi gösterilmektedir. Genel olarak, varlıklara daha fazla tür denetimi sağladığından, [arabirimlere erişmenizi](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) öneririz.

### <a name="example-client-signals-an-entity"></a>Örnek: Istemci bir varlığa işaret eder

İstemci işlevi olarak da bilinen sıradan bir Azure Işlevinden varlıklara erişmek için, [varlık istemci çıkış bağlamayı](durable-functions-bindings.md#entity-client)kullanın. Aşağıdaki örnek, bu bağlamayı kullanarak bir varlığı işaret eden kuyruk tarafından tetiklenen bir işlev gösterir.

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

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

*Sinyal* terimi, varlık API çağrısı tek yönlü ve zaman uyumsuz olduğu anlamına gelir. Bir istemci işlevinin, varlığın işlemi ne zaman işlediğini bilmesi mümkün değildir. Ayrıca, istemci işlevi sonuç değerlerini veya özel durumları gözlemleyemiyorum. 

### <a name="example-client-reads-an-entity-state"></a>Örnek: Istemci bir varlık durumunu okur

İstemci işlevleri, aşağıdaki örnekte gösterildiği gibi bir varlığın durumunu da sorgulayabilir:

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    return context.df.readEntityState(entityId);
};
```

Varlık durumu sorguları, dayanıklı izleme deposuna gönderilir ve varlığın en son kalıcı durumunu döndürür. Bu durum her zaman bir "taahhüt" durumudur, yani bir işlemin yürütüldüğü ortasında hiçbir zaman geçici bir ara durum kabul edilir. Bununla birlikte, bu durum varlığın bellek içi durumuna kıyasla eski olabilir. Aşağıdaki bölümde açıklandığı gibi, yalnızca düzenlemeler bir varlığın bellek içi durumunu okuyabilir.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Örnek: Orchestration sinyalleri ve bir varlığı çağırır

Orchestrator işlevleri, [düzenleme tetikleyicisi bağlamasında](durable-functions-bindings.md#orchestration-trigger)API 'leri kullanarak varlıklara erişebilir. Aşağıdaki örnek kod, bir `Counter` varlığı çağıran ve sinyal eden bir Orchestrator işlevi gösterir.

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
    if (currentValue < 10) {
        // One-way signal to the entity which updates the value - does not await a response
        yield context.df.signalEntity(entityId, "add", 1);
    }
});
```

Yalnızca düzenlemeler, varlık çağırma ve bir dönüş değeri ya da özel durum olabilen bir yanıt alma yeteneğine sahiptir. [İstemci bağlamasını](durable-functions-bindings.md#entity-client) kullanan istemci işlevleri yalnızca varlıkları işaret edebilir.

> [!NOTE]
> Bir Orchestrator işlevinden varlık çağırmak, bir Orchestrator işlevinden [etkinlik işlevi](durable-functions-types-features-overview.md#activity-functions) çağırmaya benzerdir. Temel fark, varlık işlevlerinin varlık KIMLIĞI olan bir adrese sahip dayanıklı nesneler olması anlamına gelir. Varlık işlevleri bir işlem adı belirtmeyi destekler. Diğer yandan etkinlik işlevleri durum bilgisiz değildir ve işlem kavramına sahip değildir.

### <a name="example-entity-signals-an-entity"></a>Örnek: varlık bir varlığa işaret eder

Bir varlık işlevi, bir işlemi yürütürken diğer varlıklara veya hatta kendisine sinyal gönderebilir.
Örneğin, önceki `Counter` varlık örneğini, sayaç 100 değerine ulaştığında, bazı izleyici varlığına "kilometre taşı" sinyali gönderecek şekilde değiştirebiliriz.

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

## <a name="entity-coordination"></a>Varlık düzenlemesi

Birden çok varlık arasında işlem koordine etmeniz gerektiğinde zaman alabilir. Örneğin, bir bankacılık uygulamasında, tek tek banka hesaplarını temsil eden varlıklara sahip olabilirsiniz. Fonları bir hesaptan diğerine aktardığınızda, kaynak hesapta yeterli fon bulunduğundan emin olmanız gerekir. Ayrıca, kaynak ve hedef hesaplara yönelik güncelleştirmelerin işlem temelli olarak tutarlı bir şekilde yapıldığından emin olmanız gerekir.

### <a name="example-transfer-funds-c"></a>Örnek: transfer fonlarıC#()

Aşağıdaki örnek kod, bir Orchestrator işlevi kullanarak iki hesap varlığı arasındaki fonları aktarır. Varlık güncelleştirmelerini koordine etmek için, düzenleme içinde _kritik bir bölüm_ oluşturmak üzere `LockAsync` yöntemi kullanılması gerekir.

> [!NOTE]
> Kolaylık olması için bu örnek daha önce tanımlanan `Counter` varlığını yeniden kullanır. Gerçek bir uygulamada, daha ayrıntılı bir `BankAccount` varlığı tanımlanması daha iyidir.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

.NET sürümünde `LockAsync`, bırakıldığında kritik bölümü sonlandıran `IDisposable`döndürür. Bu `IDisposable` sonucu, kritik bölümün sözdizimsel bir gösterimini almak için bir `using` bloğuyla birlikte kullanılabilir.

Önceki örnekte, bir Orchestrator işlevi bir kaynak varlıktan bir hedef varlığa fon aktardı. `LockAsync` yöntemi hem kaynak hem de hedef hesap varlıklarını kilitlediği. Bu, düzenleme mantığı `using` deyimin sonundaki önemli bölümden çıkana kadar başka hiçbir istemcinin herhangi bir hesabın durumunu sorgulayamayarak veya değiştirememesi için bu kilidi kilitler. Bu davranış, kaynak hesaptan daha fazla taslak oluşturma olasılığını ortadan kaldırmaya engel olur.

> [!NOTE] 
> Bir düzenleme, normalde veya bir hata ile sonlandırıldığında, devam eden tüm kritik bölümler örtük olarak sonlandırılır ve tüm kilitler serbest bırakılır.

### <a name="critical-section-behavior"></a>Kritik bölüm davranışı

`LockAsync` yöntemi bir düzenleme içinde kritik bir bölüm oluşturur. Bu kritik bölümler, diğer düzenlemeler için, belirli bir varlık kümesinde çakışan değişiklikler yapılmasını engeller. Dahili olarak, `LockAsync` API 'SI varlıklara "kilit" işlemleri gönderir ve aynı varlıkların her birinden "kilit alındı" yanıt iletisi aldığında bunu döndürür. Hem kilit hem de kilit açma, tüm varlıkların desteklediği yerleşik işlemlerdir.

Kilitli durumda olduğu sürece, bir varlıkta diğer istemcilerden işlem yapılmasına izin verilmez. Bu davranış, tek seferde yalnızca bir düzenleme örneğinin bir varlığı kilitleyebilmesini sağlar. Bir çağıran bir düzenleme tarafından kilitliyken bir varlık üzerinde bir işlem çağırmaya çalışırsa, bu işlem bekleyen bir işlem kuyruğuna yerleştirilir. Bekleyen bir işlem, saklama düzenleme kilidini serbest bırakana kadar işlenmeyecektir.

> [!NOTE] 
> Bu davranış, içindeki C#`lock` yöntemi gibi çoğu programlama dilinde kullanılan eşitleme temel larından biraz farklıdır. Örneğin, ' de C#, birden çok iş parçacığı genelinde doğru eşitlemeyi sağlamak için `lock` deyimin tüm iş parçacıkları tarafından kullanılması gerekir. Ancak varlıklar, tüm çağıranların bir varlığı açık bir şekilde kilitlemesine gerek kalmaz. Herhangi bir çağıran bir varlığı kilitlerse, söz konusu varlıktaki tüm diğer işlemler engellenir ve bu kilidin arkasında sıraya alınır.

Varlıkların kilitleri dayanıklı olduğundan, yürütülmekte olan işlem geri dönüştürülüp bile kalıcı hale getiriyordur. Kilitler, bir varlığın dayanıklı durumunun bir parçası olarak dahili olarak kalıcı hale getirilir.

İşlemlerden farklı olarak, kritik bölümler hata durumunda değişiklikleri otomatik olarak geri almaz. Bunun yerine, geri alma veya yeniden deneme gibi herhangi bir hata işleme, örneğin hatalar veya özel durumlar yakalanarak açıkça kodlanmalıdır. Bu tasarım seçeneği bilerek yapılır. Düzenleme işlemleri, genel olarak daha zor veya imkansız hale getirilir, çünkü düzenlemeler etkinlikleri çalıştırabilir ve geri alınamaz harici hizmetlere çağrı yapabilirler. Ayrıca, geri alma girişimleri de başarısız olabilir ve daha fazla hata işleme gerektirebilir.

### <a name="critical-section-rules"></a>Kritik bölüm kuralları

Çoğu programlama dilinde alt düzey kilitleme temel elemanlarından farklı olarak, kritik bölümler *kilitlenmeyen garanti*edilir. Kilitlenmeleri engellemek için aşağıdaki kısıtlamaları uyguladık: 

* Kritik bölümler iç içe olamaz.
* Kritik bölümler, alt düzenlemeler oluşturamaz.
* Kritik bölümler, yalnızca kilitlediği varlıkları çağırabilir.
* Kritik bölümler birden çok paralel çağrı kullanarak aynı varlığı çağıramaz.
* Kritik bölümler yalnızca kilitlediği varlıkları işaret edebilir.

Bu kuralların herhangi bir ihlali, .NET 'teki `LockingRulesViolationException` gibi bir çalışma zamanı hatasına neden olur. Bu, hangi kuralın bozuk olduğunu açıklayan bir ileti içerir.

## <a name="comparison-with-virtual-actors"></a>Sanal aktörler ile karşılaştırma

Birçok dayanıklı varlık özelliği [aktör modeli](https://en.wikipedia.org/wiki/Actor_model)tarafından ilham olarak sağlanır. Aktörlerle zaten bilgi sahibiyseniz, bu makalede açıklanan kavramların birçoğunu de tanıyabilirsiniz. Dayanıklı varlıklar özellikle, [Orleans projesi](http://dotnet.github.io/orleans/)tarafından popularas olarak [sanal aktörlerin](https://research.microsoft.com/projects/orleans/)veya grasındalara benzer. Örneğin:

* Dayanıklı varlıklar bir varlık KIMLIĞI aracılığıyla adreslidir.
* Yarış durumlarını engellemek için, sürekli varlık işlemleri tek seferde bir kez yürütülür.
* Dayanıklı varlıklar, çağrıldığında veya sinyal edildiğinde örtük olarak oluşturulur.
* İşlemler yürütümemekte, kalıcı varlıkların belleği sessizce kaldırılır.

Dikkat edilmesi gereken bazı önemli farklılıklar vardır:

* Dayanıklı varlıklar dayanıklılığı gecikme süresine göre önceliklendirmez ve bu nedenle, katı gecikme süresi gereksinimleri olan uygulamalar için uygun olmayabilir.
* Dayanıklı varlıklarda iletiler için yerleşik zaman aşımları yoktur. Orleans 'da, yapılandırılabilir bir süreden sonra tüm iletiler zaman aşımına uğrar. Varsayılan değer 30 saniyedir.
* Varlıklar arasında gönderilen iletiler güvenilir bir şekilde ve sırayla dağıtılır. Orleans 'da, güvenilir veya sıralı teslim akışlar aracılığıyla gönderilen içerikler için desteklenir, ancak grasınlar arasındaki tüm iletiler için garanti edilmez.
* Varlıklarda istek-yanıt desenleri, düzenleme ile sınırlıdır. Varlıkların içinden, özgün aktör modelinde olduğu gibi yalnızca tek yönlü mesajlaşmaya (sinyal olarak da bilinir) izin verilir. 
* Dayanıklı varlıklar kilitlenmeyin. Orleans 'da, kilitlenmeler meydana gelebilir ve iletiler zaman aşımına gelene kadar çözümlenmeyebilir.
* Dayanıklı varlıklar, dayanıklı düzenlemeler ile birlikte kullanılabilir ve dağıtılmış kilitleme mekanizmalarını destekler. 


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [.NET 'teki dayanıklı varlıklara yönelik geliştirici kılavuzunu okuyun](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Görev hub 'ları hakkında bilgi edinin](durable-functions-task-hubs.md)

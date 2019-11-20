---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 0f94c89a52de138b261796cbef25c0acb57622c4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800179"
---
## <a name="trigger"></a>Tetikleyici

Bir olay hub 'ı olay akışına gönderilen olaya yanıt vermek için işlev tetikleyicisini kullanın. Tetikleyiciyi ayarlamak için temeldeki Olay Hub 'ına okuma erişiminizin olması gerekir. İşlev tetiklendiğinde, işleve geçirilen ileti bir dize olarak yazılır.

## <a name="trigger---scaling"></a>Tetikleyici-ölçekleme

Olay tetiklenen bir işlevin her örneği tek bir [Eventprocessorhost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) örneği tarafından desteklenir. Tetikleyici (Event Hubs tarafından desteklenir) yalnızca bir [Eventprocessorhost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) örneğinin belirli bir bölüm üzerinde kira almasını sağlar.

Örneğin, aşağıdaki gibi bir olay hub 'ı göz önünde bulundurun:

* 10 Bölüm
* 1\.000 olay, her bölümde 100 ileti ile tüm bölümler arasında eşit olarak dağıtılır

İşleviniz ilk etkinleştirildiğinde, işlevin yalnızca bir örneği vardır. `Function_0`ilk işlev örneğini arayalım. `Function_0` işlevi, her on bölümde bir kira tutan [Eventprocessorhost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) öğesinin tek bir örneğine sahiptir. Bu örnek, 0-9 bölümlerinden olayları okuyor. Bu noktadan sonra aşağıdakilerden biri olur:

* **Yeni işlev örnekleri gerekli değildir**: `Function_0` işlevler ölçekleme mantığı yürürlüğe girmeden önce tüm 1.000 olaylarını işleyebilir. Bu durumda, tüm 1.000 iletileri `Function_0`tarafından işlenir.

* **Ek bir işlev örneği eklendi**: işlevlerin ölçeklendirilmesi mantığı `Function_0`, yeni bir işlev uygulama örneği (`Function_1`) oluşturulur. Bu yeni işlevin aynı zamanda [Eventprocessorhost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)ilişkili bir örneği de vardır. Temeldeki Event Hubs, yeni bir konak örneğinin okunmuş iletileri denediğinden emin olup, bu yükleme, bölümleri konak örnekleri genelinde dengeler. Örneğin, 0-4 bölümleri `Function_1``Function_0` ve Bölüm 5-9 ' e atanabilir.

* **N daha fazla işlev örneği eklendi**: işlevler ölçeklendirme mantığı, hem `Function_0` hem de `Function_1` işleyebileceğinden daha fazla ileti olduğunu belirlerse, yeni `Functions_N` işlevi uygulama örnekleri oluşturulur.  Uygulamalar, `N` Olay Hub 'ı bölümlerinin sayısından daha büyük olduğu noktaya oluşturulur. Bizim örneğimizde, bu durumda `Function_0`...`Functions_9`örnekleri arasında Event Hubs yeniden yükleme, bölümleri daha sonra dengeler.

Işlevler ölçeklenirken, `N` örnekleri, Olay Hub 'ı bölümlerinin sayısından daha büyük bir sayıdır. Bu işlem, [Eventprocessorhost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) örneklerinin diğer örneklerden kullanılabilir hale gelebileceği bölümlerde kilitleri elde etmek için kullanılabilir olmasını sağlamak için yapılır. Yalnızca işlev örneği yürütüldüğünde kullanılan kaynaklar için ücretlendirilirsiniz. Diğer bir deyişle, bu aşırı sağlama için ücretlendirilirsiniz.

Tüm işlev yürütmesi tamamlandığında (hata ile veya hatasız), denetim noktaları ilişkili depolama hesabına eklenir. İade etme işlemi başarılı olduğunda, tüm 1.000 iletileri hiçbir zaman geri alınamaz.

## <a name="trigger---example"></a>Tetikleyici-örnek

Dile özgü örneğe bakın:

* [C#](#trigger---c-example)
* [C#betik (. CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Tetikleyici- C# örnek

Aşağıdaki örnek, Olay Hub 'ı tetikleyicisinin ileti gövdesini günlüğe kaydeden bir [ C# işlevi](../articles/azure-functions/functions-dotnet-class-library.md) gösterir.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

İşlev kodundaki [olay meta verilerine](#trigger---event-metadata) erişim sağlamak Için bir [eventdata](/dotnet/api/microsoft.servicebus.messaging.eventdata) nesnesine bağlayın (`Microsoft.Azure.EventHubs`için bir using açıklaması gerekir). Ayrıca, yöntem imzasında bağlama ifadeleri kullanarak aynı özelliklere erişebilirsiniz.  Aşağıdaki örnek, aynı verileri almanın her iki yolunu da göstermektedir:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Bir toplu işteki olayları almak için `string` veya `EventData` bir dizi oluşturun.  

> [!NOTE]
> Bir toplu işte alırken Yukarıdaki örnekte olduğu gibi Yöntem parametrelerine `DateTime enqueuedTimeUtc` bağlanamaz ve bunları her bir `EventData` nesnesinden almalıdır  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Tetikleyici- C# betik örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [ C# betik işlevini](../articles/azure-functions/functions-reference-csharp.md) gösterir. İşlevi, Olay Hub 'ı tetikleyicisinin ileti gövdesini günlüğe kaydeder.

Aşağıdaki örneklerde, *function. JSON* dosyasında Event Hubs bağlama verisi gösterilmektedir.

#### <a name="version-2x"></a>Sürüm 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Sürüm 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

C# Betik kodu aşağıda verilmiştir:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

İşlev kodundaki [olay meta verilerine](#trigger---event-metadata) erişim sağlamak Için bir [eventdata](/dotnet/api/microsoft.servicebus.messaging.eventdata) nesnesine bağlayın (`Microsoft.Azure.EventHubs`için bir using açıklaması gerekir). Ayrıca, yöntem imzasında bağlama ifadeleri kullanarak aynı özelliklere erişebilirsiniz.  Aşağıdaki örnek, aynı verileri almanın her iki yolunu da göstermektedir:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Bir toplu işteki olayları almak için `string` veya `EventData` bir dizi oluşturun:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Tetikleyici- F# örnek

Aşağıdaki örnek, bir *function. JSON* dosyası ve bağlamayı kullanan bir [ F# işlev](../articles/azure-functions/functions-reference-fsharp.md) içindeki bir olay hub 'ı tetikleme bağlantısını göstermektedir. İşlevi, Olay Hub 'ı tetikleyicisinin ileti gövdesini günlüğe kaydeder.

Aşağıdaki örneklerde, *function. JSON* dosyasında Event Hubs bağlama verisi gösterilmektedir. 

#### <a name="version-2x"></a>Sürüm 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Sürüm 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

F# Kod şu şekildedir:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Trigger-JavaScript örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [JavaScript işlevini](../articles/azure-functions/functions-reference-node.md) gösterir. İşlevi [olay meta verilerini](#trigger---event-metadata) okur ve iletiyi günlüğe kaydeder.

Aşağıdaki örneklerde, *function. JSON* dosyasında Event Hubs bağlama verisi gösterilmektedir.

#### <a name="version-2x"></a>Sürüm 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Sürüm 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Bir toplu işteki olayları almak için, aşağıdaki örneklerde gösterildiği gibi, `cardinality` *function. JSON* dosyasında `many` olarak ayarlayın.

#### <a name="version-2x"></a>Sürüm 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Sürüm 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---python-example"></a>Trigger-Python örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [Python işlevini](../articles/azure-functions/functions-reference-python.md) gösterir. İşlevi [olay meta verilerini](#trigger---event-metadata) okur ve iletiyi günlüğe kaydeder.

Aşağıdaki örneklerde, *function. JSON* dosyasında Event Hubs bağlama verisi gösterilmektedir.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Python kodu aşağıda verilmiştir:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Trigger-Java örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [Java işlevini](../articles/azure-functions/functions-reference-java.md) gösterir. İşlevi, Olay Hub 'ı tetikleyicisinin ileti gövdesini günlüğe kaydeder.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 [Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, değeri Olay Hub 'ından gelen parametrelerde `EventHubTrigger` ek açıklamasını kullanın. Bu ek açıklamaların bulunduğu parametreler, bir olay geldiğinde işlevin çalışmasına neden olur.  Bu ek açıklama, Isteğe bağlı\<T > kullanılarak yerel Java türleri, POJOs veya null yapılabilir değerler ile kullanılabilir.

## <a name="trigger---attributes"></a>Tetikleyici-öznitelikler

[ C# Sınıf kitaplıkları](../articles/azure-functions/functions-dotnet-class-library.md)' nda [eventhubtriggerattribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) özniteliğini kullanın.

Özniteliğin Oluşturucusu, Olay Hub 'ının adını, tüketici grubunun adını ve bağlantı dizesini içeren bir uygulama ayarının adını alır. Bu ayarlar hakkında daha fazla bilgi için [tetikleyici yapılandırma bölümüne](#trigger---configuration)bakın. İşte bir `EventHubTriggerAttribute` özniteliği örneği:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Tam bir örnek için bkz. [tetikleyici- C# örnek](#trigger---c-example).

## <a name="trigger---configuration"></a>Tetikleyici-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `EventHubTrigger` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | yok | `eventHubTrigger`olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**direction** | yok | `in`olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | yok | İşlev kodundaki olay öğesini temsil eden değişkenin adı. |
|**Yolun** |**EventHubName** | Yalnızca 1. x işlevleri. Olay Hub 'ının adı. Aynı zamanda, Olay Hub 'ı adı bağlantı dizesinde de mevcutsa, bu değer çalışma zamanında bu özelliği geçersiz kılar. |
|**eventHubName** |**EventHubName** | Yalnızca 2. x işlevleri. Olay Hub 'ının adı. Aynı zamanda, Olay Hub 'ı adı bağlantı dizesinde de mevcutsa, bu değer çalışma zamanında bu özelliği geçersiz kılar. |
|**consumerGroup** |**ConsumerGroup** | Hub 'daki olaylara abone olmak için kullanılan [Tüketici grubunu](../articles/event-hubs/event-hubs-features.md#event-consumers) ayarlayan isteğe bağlı bir özellik. Atlanırsa, `$Default` Tüketici grubu kullanılır. |
|**ite** | yok | JavaScript için. Toplu işleme ' i etkinleştirmek için `many` olarak ayarlayın.  Atlanırsa veya `one`olarak ayarlandıysa, tek bir ileti işleve geçirilir. |
|**bağlanma** |**Bağlanma** | Olay Hub 'ının ad alanına bağlantı dizesini içeren bir uygulama ayarının adı. Bu bağlantı dizesini, Olay Hub 'ının değil, [ad alanı](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)Için **bağlantı bilgileri** düğmesine tıklayarak kopyalayın. Bu bağlantı dizesinin tetikleyiciyi etkinleştirmek için en azından okuma izinlerine sahip olması gerekir.|
|**Yolun**|**EventHubName**|Olay Hub 'ının adı. Uygulama ayarları aracılığıyla başvurulabilirler `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Tetikleyici-olay meta verileri

Event Hubs tetikleyicisi çeşitli [meta veri özellikleri](../articles/azure-functions/./functions-bindings-expressions-patterns.md)sağlar. Bu özellikler, diğer bağlamalardaki veya kodunuzda parametre olarak bağlama ifadelerinin bir parçası olarak kullanılabilir. Bunlar [eventdata](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) sınıfının özellikleridir.

|Özellik|Tür|Açıklama|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` örneği.|
|`EnqueuedTimeUtc`|`DateTime`|UTC olarak sıraya alınan zaman.|
|`Offset`|`string`|Olay Hub 'ı bölüm akışına göre verilerin uzaklıkları. Bu, Event Hubs akışı içindeki bir olayın işaretçisi veya tanımlayıcısıdır. Tanımlayıcı, Event Hubs akışının bir bölümü içinde benzersizdir.|
|`PartitionKey`|`string`|Olay verilerinin gönderileceği bölüm.|
|`Properties`|`IDictionary<String,Object>`|Olay verilerinin Kullanıcı özellikleri.|
|`SequenceNumber`|`Int64`|Etkinliğin mantıksal sıra numarası.|
|`SystemProperties`|`IDictionary<String,Object>`|Olay verileri de dahil olmak üzere sistem özellikleri.|

Bu makalenin önceki kısımlarında bu özellikleri kullanan [kod örneklerine](#trigger---example) bakın.

## <a name="trigger---hostjson-properties"></a>Trigger-Host. JSON özellikleri

[Host. JSON](../articles/azure-functions/functions-host-json.md#eventhub) dosyası Event Hubs tetikleme davranışını denetleyen ayarları içerir.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Çıktı

Olayları bir olay akışına yazmak için Event Hubs çıkış bağlamasını kullanın. Olayları yazmak için bir olay hub 'ına gönderme izninizin olması gerekir.

Gerekli paket başvurularının yerinde olduğundan emin olun: 1. x veya Işlevler 2. x Işlevleri

## <a name="output---example"></a>Çıkış-örnek

Dile özgü örneğe bakın:

* [C#](#output---c-example)
* [C#betik (. CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Çıkış- C# örnek

Aşağıdaki örnek, bir olay hub 'ına bir ileti yazan, çıkış olarak yöntem dönüş değeri kullanan bir [ C# işlevi](../articles/azure-functions/functions-dotnet-class-library.md) göstermektedir:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Aşağıdaki örnek, `IAsyncCollector` arabiriminin bir toplu ileti göndermek için nasıl kullanılacağını gösterir. Bu senaryo, bir olay hub 'ından gelen iletileri işlerken ve sonucu başka bir olay hub 'ına gönderdiğinizde yaygındır.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

### <a name="output---c-script-example"></a>Çıkış C# betiği örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [ C# betik işlevini](../articles/azure-functions/functions-reference-csharp.md) gösterir. İşlevi bir olay hub 'ına bir ileti yazar.

Aşağıdaki örneklerde, *function. JSON* dosyasında Event Hubs bağlama verisi gösterilmektedir. İlk örnek 2. x Işlevleri içindir ve ikincisi 1. x Işlevleri içindir. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Aşağıda bir C# ileti oluşturan betik kodu verilmiştir:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Birden çok C# ileti oluşturan betik kodu aşağıda verilmiştir:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Çıkış- F# örnek

Aşağıdaki örnek, bir *function. JSON* dosyası ve bağlamayı kullanan bir [ F# işlev](../articles/azure-functions/functions-reference-fsharp.md) içindeki bir olay hub 'ı tetikleme bağlantısını göstermektedir. İşlevi bir olay hub 'ına bir ileti yazar.

Aşağıdaki örneklerde, *function. JSON* dosyasında Event Hubs bağlama verisi gösterilmektedir. İlk örnek 2. x Işlevleri içindir ve ikincisi 1. x Işlevleri içindir. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

F# Kod şu şekildedir:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Output-JavaScript örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [JavaScript işlevini](../articles/azure-functions/functions-reference-node.md) gösterir. İşlevi bir olay hub 'ına bir ileti yazar.

Aşağıdaki örneklerde, *function. JSON* dosyasında Event Hubs bağlama verisi gösterilmektedir. İlk örnek 2. x Işlevleri içindir ve ikincisi 1. x Işlevleri içindir. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Tek bir ileti gönderen JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Birden çok ileti gönderen JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Output-Python örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [Python işlevini](../articles/azure-functions/functions-reference-python.md) gösterir. İşlevi bir olay hub 'ına bir ileti yazar.

Aşağıdaki örneklerde, *function. JSON* dosyasında Event Hubs bağlama verisi gösterilmektedir.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

İşte tek bir ileti gönderen Python kodu:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Output-Java örneği

Aşağıdaki örnek, bir olay hub 'ına geçerli zamanı contianing bir ileti yazan bir Java işlevini gösterir.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, değeri Olay Hub 'ına yayımlanacak parametrelerde `@EventHubOutput` ek açıklamasını kullanın.  Parametrenin `OutputBinding<T>` türünde olması gerekir; burada T bir POJO veya herhangi bir yerel Java türüdür.

## <a name="output---attributes"></a>Çıkış-öznitelikler

[ C# Sınıf kitaplıkları](../articles/azure-functions/functions-dotnet-class-library.md)için [eventhubattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) özniteliğini kullanın.

Özniteliğin Oluşturucusu, Olay Hub 'ının adını ve bağlantı dizesini içeren bir uygulama ayarının adını alır. Bu ayarlar hakkında daha fazla bilgi için bkz. [çıkış-yapılandırma](#output---configuration). İşte bir `EventHub` özniteliği örneği:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Tam bir örnek için bkz. [Çıkış- C# örnek](#output---c-example).

## <a name="output---configuration"></a>Çıkış-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `EventHub` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | yok | "EventHub" olarak ayarlanmalıdır. |
|**direction** | yok | "Out" olarak ayarlanmalıdır. Bu parametre, Azure portal bağlamayı oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | yok | Olayı temsil eden işlev kodunda kullanılan değişken adı. |
|**Yolun** |**EventHubName** | Yalnızca 1. x işlevleri. Olay Hub 'ının adı. Aynı zamanda, Olay Hub 'ı adı bağlantı dizesinde de mevcutsa, bu değer çalışma zamanında bu özelliği geçersiz kılar. |
|**eventHubName** |**EventHubName** | Yalnızca 2. x işlevleri. Olay Hub 'ının adı. Aynı zamanda, Olay Hub 'ı adı bağlantı dizesinde de mevcutsa, bu değer çalışma zamanında bu özelliği geçersiz kılar. |
|**bağlanma** |**Bağlanma** | Olay Hub 'ının ad alanına bağlantı dizesini içeren bir uygulama ayarının adı. Bu bağlantı dizesini, Olay Hub 'ının değil, *ad alanı*Için **bağlantı bilgileri** düğmesine tıklayarak kopyalayın. Bu bağlantı dizesinin iletiyi olay akışına göndermek için gönderme izinleri olmalıdır.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Çıkış kullanımı

C# Ve C# komut dosyasında, `out string paramName`gibi bir yöntem parametresi kullanarak iletiler gönderin. Betik C# ' de, `paramName` *function. json*' nin `name` özelliğinde belirtilen değerdir. Birden çok ileti yazmak için `out string`yerine `ICollector<string>` veya `IAsyncCollector<string>` kullanabilirsiniz.

JavaScript 'te `context.bindings.<name>`kullanarak çıkış olayına erişin. `<name>`, *function. JSON*' nin `name` özelliğinde belirtilen değerdir.

## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlayıcısı | Başvuru |
|---|---|
| Olay Hub'ı | [İşlemler Kılavuzu](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host. JSON ayarları

Bu bölümde, sürüm 2. x içinde bu bağlama için kullanılabilen genel yapılandırma ayarları açıklanmaktadır. Aşağıdaki örnek Host. JSON dosyası, bu bağlamanın yalnızca sürüm 2. x ayarlarını içerir. Sürüm 2. x içindeki genel yapılandırma ayarları hakkında daha fazla bilgi için bkz. [Azure işlevleri sürüm 2. x için Host. JSON başvurusu](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> 1\. x Işlevleri içindeki Host. JSON başvurusu için bkz. [Azure işlevleri için Host. JSON başvurusu 1. x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------|
|maxBatchSize|64|Alma döngüsü başına alınan en yüksek olay sayısı.|
|prefetchCount|yok|Temel alınan EventProcessorHost tarafından kullanılacak varsayılan PrefetchCount.|
|Batchcheckpointflik|1|Bir EventHub imleç denetim noktası oluşturmadan önce işlenecek olay toplu işlemlerinin sayısı.|

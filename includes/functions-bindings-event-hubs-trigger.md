---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 32f98eb9b98168bdab270ecff07446c31f8d706d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729794"
---
Bir olay hub 'ı olay akışına gönderilen olaya yanıt vermek için işlev tetikleyicisini kullanın. Tetikleyiciyi ayarlamak için temeldeki Olay Hub 'ına okuma erişiminizin olması gerekir. İşlev tetiklendiğinde, işleve geçirilen ileti bir dize olarak yazılır.

## <a name="scaling"></a>Ölçeklendirme

Olay tetiklenen bir işlevin her örneği tek bir [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor) örneği tarafından desteklenir. Tetikleyici (Event Hubs tarafından desteklenir) yalnızca bir [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor) örneğinin belirli bir bölüm üzerinde kira almasını sağlar.

Örneğin, aşağıdaki gibi bir olay hub 'ı göz önünde bulundurun:

* 10 Bölüm
* 1.000 olay, her bölümde 100 ileti ile tüm bölümler arasında eşit olarak dağıtılır

İşleviniz ilk etkinleştirildiğinde, işlevin yalnızca bir örneği vardır. İlk işlev örneğini arayalım `Function_0` . `Function_0`İşlevin tüm on bölümde kira tutan [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor) ' un tek bir örneği vardır. Bu örnek, 0-9 bölümlerinden olayları okuyor. Bu noktadan sonra aşağıdakilerden biri olur:

* **Yeni işlev örnekleri gerekli değildir**: `Function_0` işlevler ölçekleme mantığı yürürlüğe girmeden önce tüm 1.000 olaylarını işleyebilir. Bu durumda, tüm 1.000 iletileri tarafından işlenir `Function_0` .

* **Ek bir işlev örneği eklendi**: işlevleri ölçeklendirme mantığı, `Function_0` işleyebileceğinden daha fazla ileti olduğunu belirlerse, yeni bir işlev uygulama örneği ( `Function_1` ) oluşturulur. Bu yeni işlevin aynı zamanda [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor)ilişkili bir örneği de vardır. Temeldeki Event Hubs, yeni bir konak örneğinin okunmuş iletileri denediğinden emin olup, bu yük, konak örnekleri genelinde bölümleri dengeler. Örneğin, 0-4 bölümlerine `Function_0` ve bölümleri 5-9 ' e atanabilir `Function_1` .

* **N daha fazla işlev örneği eklendi**: işlevlerin ölçeklendirilmesi mantığı, her ikisinin de `Function_0` `Function_1` işleyebileceğinden daha fazla ileti olduğunu belirlerse, yeni `Functions_N` işlev uygulama örnekleri oluşturulur.  Uygulamalar, `N` Olay Hub 'ı bölümlerinin sayısından daha büyük olan noktada oluşturulur. Bizim örneğimizde, bu durumda örneklerin tamamında Event Hubs yeniden yük dengeler `Function_0` . `Functions_9`

Ölçeklendirme gerçekleştiğinde, `N` örnekler Olay Hub 'ı bölümlerinin sayısından daha büyük bir sayıdır. Bu model, [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor) örneklerinin diğer örneklerden kullanılabilir hale geldiklerinden, bölümler üzerinde kilitler elde etmek için kullanılabilir olmasını sağlamak için kullanılır. Yalnızca işlev örneği yürütüldüğünde kullanılan kaynaklar için ücretlendirilirsiniz. Diğer bir deyişle, bu aşırı sağlama için ücretlendirilirsiniz.

Tüm işlev yürütmesi tamamlandığında (hata ile veya hatasız), denetim noktaları ilişkili depolama hesabına eklenir. İade etme işlemi başarılı olduğunda, tüm 1.000 iletileri hiçbir zaman geri alınamaz.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnekte, Olay Hub 'ı tetikleyicisinin ileti gövdesini günlüğe kaydeden bir [C# işlevi](../articles/azure-functions/functions-dotnet-class-library.md) gösterilmektedir.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

İşlev kodundaki [olay meta verilerine](#event-metadata) erişim sağlamak Için bir [eventdata](/dotnet/api/microsoft.servicebus.messaging.eventdata) nesnesine bağlayın (için using deyimleri gerekir `Microsoft.Azure.EventHubs` ). Ayrıca, yöntem imzasında bağlama ifadeleri kullanarak aynı özelliklere erişebilirsiniz.  Aşağıdaki örnek, aynı verileri almanın her iki yolunu da göstermektedir:

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

Bir toplu iş, oluşturma veya dizi içinde olayları almak için `string` `EventData` .  

> [!NOTE]
> Bir toplu işte alırken yukarıdaki örnekteki gibi Yöntem parametrelerine bağlanamaz `DateTime enqueuedTimeUtc` ve bunları her bir `EventData` nesneden almalıdır  

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

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bir *function.js* dosyadaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [C# betik işlevini](../articles/azure-functions/functions-reference-csharp.md) gösterir. İşlevi, Olay Hub 'ı tetikleyicisinin ileti gövdesini günlüğe kaydeder.

Aşağıdaki örneklerde Event Hubs *function.js* dosyadaki verileri bağlama gösterilmektedir.

### <a name="version-2x-and-higher"></a>Sürüm 2. x ve üzeri

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Sürüm 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

C# betik kodu aşağıda verilmiştir:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

İşlev kodundaki [olay meta verilerine](#event-metadata) erişim sağlamak Için bir [eventdata](/dotnet/api/microsoft.servicebus.messaging.eventdata) nesnesine bağlayın (için using deyimleri gerekir `Microsoft.Azure.EventHubs` ). Ayrıca, yöntem imzasında bağlama ifadeleri kullanarak aynı özelliklere erişebilirsiniz.  Aşağıdaki örnek, aynı verileri almanın her iki yolunu da göstermektedir:

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

Bir toplu işte olayları almak için, `string` veya `EventData` dizisi oluşturun:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosyadaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [JavaScript işlevini](../articles/azure-functions/functions-reference-node.md) gösterir. İşlevi [olay meta verilerini](#event-metadata) okur ve iletiyi günlüğe kaydeder.

Aşağıdaki örneklerde Event Hubs *function.js* dosyadaki verileri bağlama gösterilmektedir.

### <a name="version-2x-and-higher"></a>Sürüm 2. x ve üzeri

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Sürüm 1. x

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

Bir toplu işteki olayları almak için `cardinality` `many` Aşağıdaki örneklerde gösterildiği gibi, dosyasında *function.js* olarak ayarlayın.

### <a name="version-2x-and-higher"></a>Sürüm 2. x ve üzeri

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

### <a name="version-1x"></a>Sürüm 1. x

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

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, bir *function.js* dosyadaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [Python işlevini](../articles/azure-functions/functions-reference-python.md) gösterir. İşlevi [olay meta verilerini](#event-metadata) okur ve iletiyi günlüğe kaydeder.

Aşağıdaki örneklerde Event Hubs *function.js* dosyadaki verileri bağlama gösterilmektedir.

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
    logging.info(f'Function triggered to process a message: {event.get_body().decode()}')
    logging.info(f'  EnqueuedTimeUtc = {event.enqueued_time}')
    logging.info(f'  SequenceNumber = {event.sequence_number}')
    logging.info(f'  Offset = {event.offset}')

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = {event.metadata[key]}')
```

# <a name="java"></a>[Java](#tab/java)

Aşağıdaki örnekte, Olay Hub 'ı tetikleyicisinin ileti gövdesini günlüğe kaydeden bir olay hub 'ı tetikleyicisi bağlantısı gösterilmektedir.

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

 [Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, `EventHubTrigger` değeri Olay Hub 'ından gelen parametrelerde ek açıklama kullanın. Bu ek açıklamaların bulunduğu parametreler, bir olay geldiğinde işlevin çalışmasına neden olur.  Bu ek açıklama, kullanılarak yerel Java türleri, POJOs veya null atanabilir değerlerle kullanılabilir `Optional<T>` .

 ---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](../articles/azure-functions/functions-dotnet-class-library.md) [Eventhubtriggerattribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) özniteliğini kullanın.

Özniteliğin Oluşturucusu, Olay Hub 'ının adını, tüketici grubunun adını ve bağlantı dizesini içeren bir uygulama ayarının adını alır. Bu ayarlar hakkında daha fazla bilgi için [tetikleyici yapılandırma bölümüne](#configuration)bakın. Aşağıda bir `EventHubTriggerAttribute` öznitelik örneği verilmiştir:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Tam bir örnek için bkz. [Trigger-C# örneği](#example).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

Java [işlevleri çalışma zamanı kitaplığından](/java/api/overview/azure/functions/runtime), değeri Olay Hub 'ından gelen parametrelerde [Eventhubtrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) ek açıklamasını kullanın. Bu ek açıklamaların bulunduğu parametreler, bir olay geldiğinde işlevin çalışmasına neden olur. Bu ek açıklama, kullanılarak yerel Java türleri, POJOs veya null atanabilir değerlerle kullanılabilir `Optional<T>` .

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `EventHubTrigger` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok | Olarak ayarlanmalıdır `eventHubTrigger` . Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**Görünüm** | yok | Olarak ayarlanmalıdır `in` . Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | yok | İşlev kodundaki olay öğesini temsil eden değişkenin adı. |
|**Yolun** |**EventHubName** | Yalnızca 1. x işlevleri. Olay Hub 'ının adı. Aynı zamanda, Olay Hub 'ı adı bağlantı dizesinde de mevcutsa, bu değer çalışma zamanında bu özelliği geçersiz kılar. |
|**eventHubName** |**EventHubName** | İşlevler 2. x ve üzeri. Olay Hub 'ının adı. Aynı zamanda, Olay Hub 'ı adı bağlantı dizesinde de mevcutsa, bu değer çalışma zamanında bu özelliği geçersiz kılar. [Uygulama ayarları](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings) aracılığıyla başvurulabilirler`%eventHubName%` |
|**consumerGroup** |**ConsumerGroup** | Hub 'daki olaylara abone olmak için kullanılan [Tüketici grubunu](../articles/event-hubs/event-hubs-features.md#event-consumers) ayarlayan isteğe bağlı bir özellik. Atlanırsa, `$Default` Tüketici grubu kullanılır. |
|**ite** | yok | C olmayan tüm diller için kullanılır. Toplu işlemeyi `many` etkinleştirmek için olarak ayarlayın.  Atlanırsa veya olarak ayarlandıysa `one` , işleve tek bir ileti geçirilir.<br><br>C# ' de, tetikleyici tür için bir diziye sahip her seferinde bu özellik otomatik olarak atanır.|
|**bağlanma** |**Bağlantı** | Olay Hub 'ının ad alanına bağlantı dizesini içeren bir uygulama ayarının adı. Bu bağlantı dizesini, Olay Hub 'ının değil, [ad alanı](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)Için **bağlantı bilgileri** düğmesine tıklayarak kopyalayın. Bu bağlantı dizesinin tetikleyiciyi etkinleştirmek için en azından okuma izinlerine sahip olması gerekir.<br><br>[Uzantının 5. x veya daha yüksek bir sürümünü](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)kullanıyorsanız, bağlantı dizesi yerine bağlantıyı tanımlayan bir yapılandırma bölümüne başvuru sağlayabilirsiniz. Bkz. [Bağlantılar](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Varsayılan

Tetikleyici olay hub 'ı için aşağıdaki parametre türlerini kullanabilirsiniz:

* `string`
* `byte[]`
* `POCO`
* `EventData` -EventData 'ın varsayılan özellikleri, [Microsoft. Azure. EventHubs ad alanı](/dotnet/api/microsoft.azure.eventhubs.eventdata)için ' de verilmiştir.

### <a name="additional-types"></a>Ek türler 
Olay Hub uzantısının 5.0.0 veya daha yüksek sürümünü kullanan uygulamalar, `EventData` [Microsoft. Azure. eventhubs ad alanı](/dotnet/api/microsoft.azure.eventhubs.eventdata)yerine [Azure. Messaging. eventhubs](/dotnet/api/azure.messaging.eventhubs.eventdata) türünü kullanır. Bu sürüm, `Body` aşağıdaki türlerin yerine eski tür için desteği bırakır:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

### <a name="default"></a>Varsayılan

Tetikleyici olay hub 'ı için aşağıdaki parametre türlerini kullanabilirsiniz:

* `string`
* `byte[]`
* `POCO`
* `EventData` -EventData 'ın varsayılan özellikleri, [Microsoft. Azure. EventHubs ad alanı](/dotnet/api/microsoft.azure.eventhubs.eventdata)için ' de verilmiştir.

### <a name="additional-types"></a>Ek türler 
Olay Hub uzantısının 5.0.0 veya daha yüksek sürümünü kullanan uygulamalar, `EventData` [Microsoft. Azure. eventhubs ad alanı](/dotnet/api/microsoft.azure.eventhubs.eventdata)yerine [Azure. Messaging. eventhubs](/dotnet/api/azure.messaging.eventhubs.eventdata) türünü kullanır. Bu sürüm, `Body` aşağıdaki türlerin yerine eski tür için desteği bırakır:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="java"></a>[Java](#tab/java)

Ayrıntılar için Java [tetikleme örneğine](#example) bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ayrıntılar için JavaScript [tetikleyicisi örneğine](#example) bakın.

# <a name="python"></a>[Python](#tab/python)

Ayrıntılar için Python [tetikleyicisi örneğine](#example) bakın.


---


## <a name="event-metadata"></a>Olay meta verileri

Event Hubs tetikleyicisi çeşitli [meta veri özellikleri](../articles/azure-functions/./functions-bindings-expressions-patterns.md)sağlar. Meta veri özellikleri, diğer bağlamalardaki veya kodunuzda parametre olarak bağlama ifadelerinin bir parçası olarak kullanılabilir. Özellikler [eventdata](/dotnet/api/microsoft.servicebus.messaging.eventdata) sınıfından gelir.

|Özellik|Tür|Description|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` örneği.|
|`EnqueuedTimeUtc`|`DateTime`|UTC olarak sıraya alınan zaman.|
|`Offset`|`string`|Olay Hub 'ı bölüm akışına göre verilerin uzaklıkları. Bu, Event Hubs akışı içindeki bir olayın işaretçisi veya tanımlayıcısıdır. Tanımlayıcı, Event Hubs akışının bir bölümü içinde benzersizdir.|
|`PartitionKey`|`string`|Olay verilerinin gönderileceği bölüm.|
|`Properties`|`IDictionary<String,Object>`|Olay verilerinin Kullanıcı özellikleri.|
|`SequenceNumber`|`Int64`|Etkinliğin mantıksal sıra numarası.|
|`SystemProperties`|`IDictionary<String,Object>`|Olay verileri de dahil olmak üzere sistem özellikleri.|

Bu makalenin önceki kısımlarında bu özellikleri kullanan [kod örneklerine](#example) bakın.

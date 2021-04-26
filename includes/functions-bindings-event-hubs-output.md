---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: bc2bec364f8d752b7416ecccf0b00d0fbec4c8e8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729835"
---
Olayları bir olay akışına yazmak için Event Hubs çıkış bağlamasını kullanın. Olay yazmak için ilgili olay hub'ında gönderme iznine sahip olmanız gerekir.

Çıkış bağlamayı uygulamayı denemeden önce gerekli paket başvurularının yerinde olduğundan emin olun.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnek, bir olay hub 'ına bir ileti yazan, çıkış olarak yöntem dönüş değeri kullanan bir [C# işlevini](../articles/azure-functions/functions-dotnet-class-library.md) göstermektedir:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Aşağıdaki örnek, `IAsyncCollector` bir dizi ileti göndermek için arabirimi nasıl kullanacağınızı gösterir. Bu senaryo, bir olay hub 'ından gelen iletileri işlerken ve sonucu başka bir olay hub 'ına gönderdiğinizde yaygındır.

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

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bir *function.js* dosyadaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [C# betik işlevini](../articles/azure-functions/functions-reference-csharp.md) gösterir. İşlevi bir olay hub 'ına bir ileti yazar.

Aşağıdaki örneklerde Event Hubs *function.js* dosyadaki verileri bağlama gösterilmektedir. İlk örnek 2. x ve üzeri Işlevleri için, ikincisi ise 1. x Işlevleri içindir. 

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

Aşağıda bir ileti oluşturan C# betik kodu verilmiştir:

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

Birden çok ileti oluşturan C# betik kodu aşağıda verilmiştir:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosyadaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [JavaScript işlevini](../articles/azure-functions/functions-reference-node.md) gösterir. İşlevi bir olay hub 'ına bir ileti yazar.

Aşağıdaki örneklerde Event Hubs *function.js* dosyadaki verileri bağlama gösterilmektedir. İlk örnek 2. x ve üzeri Işlevleri için, ikincisi ise 1. x Işlevleri içindir. 

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

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, bir *function.js* dosyadaki bir olay hub 'ı tetikleme bağlantısını ve bağlamayı kullanan bir [Python işlevini](../articles/azure-functions/functions-reference-python.md) gösterir. İşlevi bir olay hub 'ına bir ileti yazar.

Aşağıdaki örneklerde Event Hubs *function.js* dosyadaki verileri bağlama gösterilmektedir.

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

# <a name="java"></a>[Java](#tab/java)

Aşağıdaki örnekte, bir olay hub 'ına geçerli saati içeren bir ileti yazan bir Java işlevi gösterilmektedir.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, `@EventHubOutput` değeri Olay Hub 'ına yayımlanacak parametrelerde ek açıklama kullanın.  Parametresi türünde olmalıdır `OutputBinding<T>` ; burada T BIR POJO veya herhangi bir yerel Java türüdür.

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıkları](../articles/azure-functions/functions-dotnet-class-library.md)Için [Eventhubattribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) özniteliğini kullanın.

Özniteliğin Oluşturucusu, Olay Hub 'ının adını ve bağlantı dizesini içeren bir uygulama ayarının adını alır. Bu ayarlar hakkında daha fazla bilgi için bkz. [çıkış-yapılandırma](#configuration). Aşağıda bir `EventHub` öznitelik örneği verilmiştir:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Tam bir örnek için bkz. [output-C# örneği](#example).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)' nda, değeri Olay Hub 'ına yayımlanacak parametrelerde [Eventhui put](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) ek açıklamasını kullanın. Parametresi `OutputBinding<T>` , `T` BIR Pojo veya herhangi bir yerel Java türü olan türünde olmalıdır.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `EventHub` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok | "EventHub" olarak ayarlanmalıdır. |
|**Görünüm** | yok | "Out" olarak ayarlanmalıdır. Bu parametre, Azure portal bağlamayı oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | yok | Olayı temsil eden işlev kodunda kullanılan değişken adı. |
|**Yolun** |**EventHubName** | Yalnızca 1. x işlevleri. Olay Hub 'ının adı. Aynı zamanda, Olay Hub 'ı adı bağlantı dizesinde de mevcutsa, bu değer çalışma zamanında bu özelliği geçersiz kılar. |
|**eventHubName** |**EventHubName** | İşlevler 2. x ve üzeri. Olay Hub 'ının adı. Aynı zamanda, Olay Hub 'ı adı bağlantı dizesinde de mevcutsa, bu değer çalışma zamanında bu özelliği geçersiz kılar. |
|**bağlanma** |**Bağlantı** | Olay Hub 'ının ad alanına bağlantı dizesini içeren bir uygulama ayarının adı. Bu bağlantı dizesini, Olay Hub 'ının değil, [ad alanı](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)Için **bağlantı bilgileri** düğmesine tıklayarak kopyalayın. Bu bağlantı dizesinin iletiyi olay akışına göndermek için gönderme izinleri olmalıdır. <br><br>[Uzantının 5. x veya daha yüksek bir sürümünü](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)kullanıyorsanız, bağlantı dizesi yerine bağlantıyı tanımlayan bir yapılandırma bölümüne başvuru sağlayabilirsiniz. Bkz. [Bağlantılar](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Varsayılan

Olay Hub 'ının çıkış bağlaması için aşağıdaki parametre türlerini kullanabilirsiniz:

* `string`
* `byte[]`
* `POCO`
* `EventData` -EventData 'ın varsayılan özellikleri, [Microsoft. Azure. EventHubs ad alanı](/dotnet/api/microsoft.azure.eventhubs.eventdata)için ' de verilmiştir.

Gibi bir yöntem parametresi kullanarak ileti gönderin `out string paramName` . C# komut dosyasında, `paramName` `name` *üzerindefunction.js* özelliğinde belirtilen değerdir. Birden çok ileti yazmak için, `ICollector<string>` veya yerinde kullanabilirsiniz `IAsyncCollector<string>` `out string` .

### <a name="additional-types"></a>Ek türler 
Olay Hub uzantısının 5.0.0 veya daha yüksek sürümünü kullanan uygulamalar, `EventData` [Microsoft. Azure. eventhubs ad alanı](/dotnet/api/microsoft.azure.eventhubs.eventdata)yerine [Azure. Messaging. eventhubs](/dotnet/api/azure.messaging.eventhubs.eventdata) türünü kullanır. Bu sürüm, `Body` aşağıdaki türlerin yerine eski tür için desteği bırakır:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

### <a name="default"></a>Varsayılan

Olay Hub 'ının çıkış bağlaması için aşağıdaki parametre türlerini kullanabilirsiniz:

* `string`
* `byte[]`
* `POCO`
* `EventData` -EventData 'ın varsayılan özellikleri, [Microsoft. Azure. EventHubs ad alanı](/dotnet/api/microsoft.azure.eventhubs.eventdata)için ' de verilmiştir.

Gibi bir yöntem parametresi kullanarak ileti gönderin `out string paramName` . C# komut dosyasında, `paramName` `name` *üzerindefunction.js* özelliğinde belirtilen değerdir. Birden çok ileti yazmak için, `ICollector<string>` veya yerinde kullanabilirsiniz `IAsyncCollector<string>` `out string` .

### <a name="additional-types"></a>Ek türler 
Olay Hub uzantısının 5.0.0 veya daha yüksek sürümünü kullanan uygulamalar, `EventData` [Microsoft. Azure. eventhubs ad alanı](/dotnet/api/microsoft.azure.eventhubs.eventdata)yerine [Azure. Messaging. eventhubs](/dotnet/api/azure.messaging.eventhubs.eventdata) türünü kullanır. Bu sürüm, `Body` aşağıdaki türlerin yerine eski tür için desteği bırakır:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Çıkış olayına, `context.bindings.<name>` `<name>` `name` *üzerindefunction.js* özelliğinde belirtilen değerin nerede olduğunu kullanarak erişin.

# <a name="python"></a>[Python](#tab/python)

Bir işlevden bir olay hub 'ı iletisini almak için iki seçenek vardır:

- **Dönüş değeri**: `name` *üzerindefunction.js* özelliğini olarak ayarlayın `$return` . Bu yapılandırmayla, işlevin dönüş değeri bir olay hub 'ı iletisi olarak kalıcıdır.

- **Zorunludur**: bir değeri, [Out](/python/api/azure-functions/azure.functions.out) türü olarak belirtilen parametresinin [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) yöntemine geçirin. Geçirilen değer `set` bir olay hub 'ı iletisi olarak kalıcıdır.

# <a name="java"></a>[Java](#tab/java)

[Eventhui put](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) ek açıklamasını kullanarak bir Işlevden Olay Hub 'ı iletisini almak için iki seçenek vardır:

- **Dönüş değeri**: ek açıklamanın işleve uygulanması için, işlevin dönüş değeri bir olay hub 'ı iletisi olarak kalıcıdır.

- Zorunlu **: ileti** değerini açıkça ayarlamak için, ek açıklamayı türün belirli bir parametresine uygulayın [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) , burada `T` bir Pojo veya herhangi bir yerel Java türü olur. Bu yapılandırmayla, yöntemine bir değer geçirilmesi `setValue` değeri bir olay hub 'ı iletisi olarak devam ettirir.

---

## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlama | Başvuru |
|---|---|
| Olay Hub'ı | [İşlemler Kılavuzu](/rest/api/eventhub/publisher-policy-operations) |
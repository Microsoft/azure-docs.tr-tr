---
title: Azure Işlevleri için Azure kuyruk depolama çıkış bağlaması
description: Azure Işlevleri 'nde Azure kuyruk depolama iletileri oluşturmayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 5d94625e3eb121e556b28038cf59626be1332966
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455814"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Azure Işlevleri için Azure kuyruk depolama çıkışı bağlamaları

Azure Işlevleri, çıkış bağlaması ayarlayarak yeni Azure kuyruk depolama iletileri oluşturabilir.

Kurulum ve yapılandırma ayrıntıları hakkında bilgi için bkz. [genel bakış](./functions-bindings-storage-queue.md).

## <a name="example"></a>Örnek

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnekte, alınan her HTTP isteği için bir kuyruk iletisi oluşturan bir [C# işlevi](functions-dotnet-class-library.md) gösterilmektedir.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bağlamayı kullanan dosya ve [C# betiği (. CSX)](functions-reference-csharp.md) kodundaki *function.js* bir http tetikleyici bağlamayı gösterir. İşlevi, alınan her HTTP isteği için bir **Customqueuemessage** nesne yüküne sahip bir kuyruk öğesi oluşturur.

Dosyada *function.js* :

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

Tek bir sıra iletisi oluşturan C# betik kodu aşağıda verilmiştir:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Bir veya parametresini kullanarak bir kerede birden çok ileti gönderebilirsiniz `ICollector` `IAsyncCollector` . Burada, biri HTTP istek verileriyle, diğeri ise sabit kodlanmış değerlerle birden çok ileti gönderen C# betik kodu:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 Aşağıdaki örnek, bir HTTP isteği tarafından tetiklendiğinde kuyruk iletisi oluşturan bir Java işlevini gösterir.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, `@QueueOutput` değeri kuyruk depolamaya yazılacak olan parametrelerde ek açıklamayı kullanın.  Parametre türü olmalıdır `OutputBinding<T>` , burada `T` BIR Pojo 'Nın yerel Java türüdür.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* DOSYADAKI bir http tetikleyicisi bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi, alınan her HTTP isteği için bir kuyruk öğesi oluşturur.

Dosyada *function.js* :

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Çıkış bağlaması için bir ileti dizisi tanımlayarak bir kerede birden çok ileti gönderebilirsiniz `myQueueItem` . Aşağıdaki JavaScript kodu, alınan her HTTP isteği için sabit kodlanmış değerler içeren iki kuyruk iletisi gönderir.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki kod örnekleri, bir HTTP ile tetiklenen işlevden bir sıra iletisinin nasıl çıktısının alınacağını göstermektedir. İçindeki yapılandırma bölümü, `type` `queue` Çıkış bağlamayı tanımlar.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Bu bağlama yapılandırmasını kullanarak bir PowerShell işlevi kullanarak bir kuyruk iletisi oluşturabilir `Push-OutputBinding` . Bu örnekte, bir sorgu dizesinden veya gövde parametresinden bir ileti oluşturulur.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Aynı anda birden çok ileti göndermek için, bir ileti dizisi tanımlayın ve `Push-OutputBinding` Iletileri kuyruk çıkış bağlamasına göndermek için kullanın.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, Depolama kuyruklarına tek ve birden çok değerin nasıl alınacağını gösterir. *function.js* için gereken yapılandırma her iki yoldan de aynıdır.

Bir depolama kuyruğu bağlama, *türünün türü* olarak ayarlandığı *function.js* tanımlanmıştır `queue` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Kuyrukta tek bir ileti ayarlamak için yöntemine tek bir değer geçirirsiniz `set` .

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Kuyrukta birden çok ileti oluşturmak için, uygun liste türü olarak bir parametre bildirin ve yönteme (liste türüyle eşleşen) bir değer dizisi geçirin `set` .

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md) [queueattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs)kullanın.

Özniteliği bir `out` parametre veya işlevin dönüş değeri için geçerlidir. Aşağıdaki örnekte gösterildiği gibi özniteliğin Oluşturucusu sıranın adını alır:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

`Connection`Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için özelliğini ayarlayabilirsiniz:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Tüm örnek için bkz. [çıkış örneği](#example).

`StorageAccount`Sınıfı, yöntemi veya parametre düzeyinde depolama hesabını belirtmek için özniteliğini kullanabilirsiniz. Daha fazla bilgi için bkz. tetikleyici-öznitelikler.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

`QueueOutput`Ek açıklama, bir işlevin çıktısı olarak bir ileti yazmanızı sağlar. Aşağıdaki örnekte, bir kuyruk iletisi oluşturan HTTP ile tetiklenen bir işlev gösterilmektedir.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Özellik    | Açıklama |
|-------------|-----------------------------|
|`name`       | İşlev imzasında parametre adını bildirir. İşlev tetiklendiğinde, bu parametrenin değeri kuyruk iletisinin içeriğine sahiptir. |
|`queueName`  | Depolama hesabındaki sıra adını bildirir. |
|`connection` | Depolama hesabı bağlantı dizesine işaret eder. |

`QueueOutput`Ek açıklamayla ilişkili parametre bir [OutputBinding \<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) örneği olarak yazılır.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Öznitelikler PowerShell tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `Queue` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok | Olarak ayarlanmalıdır `queue` . Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**Görünüm** | yok | Olarak ayarlanmalıdır `out` . Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | yok | İşlev kodundaki kuyruğu temsil eden değişkenin adı. `$return`İşlev dönüş değerine başvuracak şekilde ayarlanır.|
|**Adı** |**Adı** | Kuyruğun adı. |
|**bağlanma** | **Bağlantı** |Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz.<br><br>Örneğin, `connection` "Mystorage" olarak ayarlarsanız, işlevler çalışma zamanı "MyStorage" adlı bir uygulama ayarı arar. `connection`Boş bırakırsanız, işlevler çalışma zamanı adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır `AzureWebJobsStorage` .<br><br>[Uzantının 5. x veya daha yüksek bir sürümünü](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)kullanıyorsanız, bağlantı dizesi yerine bağlantıyı tanımlayan bir yapılandırma bölümüne başvuru sağlayabilirsiniz. Bkz. [Bağlantılar](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Varsayılan

Gibi bir yöntem parametresi kullanarak tek bir kuyruk iletisi yazın `out T paramName` . Bir parametre yerine yöntem dönüş türünü kullanabilir `out` ve `T` aşağıdaki türlerden herhangi biri olabilir:

* JSON olarak seri hale getirilebilir bir nesne
* `string`
* `byte[]`
* [CloudQueueMessage] 

' `CloudQueueMessage` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

C# ve C# komut dosyasında, aşağıdaki türlerden birini kullanarak birden çok kuyruk iletisi yazın: 

* `ICollector<T>` veya `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Ek türler

[Depolama uzantısının 5.0.0 veya daha yüksek sürümünü](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) kullanan uygulamalar, [.NET için Azure SDK 'sının](/dotnet/api/overview/azure/storage.queues-readme)türlerini de kullanabilir. Bu sürüm, `CloudQueue` aşağıdaki türlerin yerine eski ve türler için destek bırakır `CloudQueueMessage` :

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- Birden çok kuyruk iletisi yazmak için [Queueclient](/dotnet/api/azure.storage.queues.queueclient)

Bu türleri kullanan örnekler için, [uzantının GitHub deposuna](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)bakın.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

### <a name="default"></a>Varsayılan

Gibi bir yöntem parametresi kullanarak tek bir kuyruk iletisi yazın `out T paramName` . , `paramName` `name` *Üzerindefunction.js* özelliğinde belirtilen değerdir. Bir parametre yerine yöntem dönüş türünü kullanabilir `out` ve `T` aşağıdaki türlerden herhangi biri olabilir:

* JSON olarak seri hale getirilebilir bir nesne
* `string`
* `byte[]`
* [CloudQueueMessage] 

' `CloudQueueMessage` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

C# ve C# komut dosyasında, aşağıdaki türlerden birini kullanarak birden çok kuyruk iletisi yazın: 

* `ICollector<T>` veya `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Ek türler

[Depolama uzantısının 5.0.0 veya daha yüksek sürümünü](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) kullanan uygulamalar, [.NET için Azure SDK 'sının](/dotnet/api/overview/azure/storage.queues-readme)türlerini de kullanabilir. Bu sürüm, `CloudQueue` aşağıdaki türlerin yerine eski ve türler için destek bırakır `CloudQueueMessage` :

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- Birden çok kuyruk iletisi yazmak için [Queueclient](/dotnet/api/azure.storage.queues.queueclient)

Bu türleri kullanan örnekler için, [uzantının GitHub deposuna](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)bakın.

# <a name="java"></a>[Java](#tab/java)

[Queueoutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) ek açıklamasını kullanarak bir işlevden sıra iletisini almak için iki seçenek vardır:

- **Dönüş değeri**: ek açıklamanın işleve uygulanması için işlevin dönüş değeri bir kuyruk iletisi olarak kalıcıdır.

- Zorunlu **: ileti** değerini açıkça ayarlamak için, ek açıklamayı türün belirli bir parametresine uygulayın [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , burada `T` bir Pojo veya herhangi bir yerel Java türü olur. Bu yapılandırmayla, yönteme bir değer geçirilmesi `setValue` değeri bir kuyruk iletisi olarak devam ettirir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Çıkış kuyruğu öğesine, `context.bindings.<NAME>` `<NAME>` *üzerindefunction.js* tanımlanan adla eşleşen WHERE aracılığıyla erişilebilir. Kuyruk öğesi yükü için bir dize veya JSON-serileştirilebilir nesnesi kullanabilirsiniz.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kuyruk iletisine çıkış, `Push-OutputBinding` dosyadakifunction.jsbağlamanın parametresi tarafından belirlenen adla eşleşen bağımsız değişkenleri geçirdiğiniz yerde kullanılabilir `name` . 

# <a name="python"></a>[Python](#tab/python)

Bir işlevden sıra iletisini almak için iki seçenek vardır:

- **Dönüş değeri**: `name` *üzerindefunction.js* özelliğini olarak ayarlayın `$return` . Bu yapılandırmayla, işlevin dönüş değeri kuyruk depolama iletisi olarak kalıcıdır.

- **Zorunludur**: bir değeri, [Out](/python/api/azure-functions/azure.functions.out) türü olarak belirtilen parametresinin [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) yöntemine geçirin. Geçirilen değer `set` kuyruk depolama iletisi olarak kalıcıdır.

---

## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlama |  Başvuru |
|---|---|
| Kuyruk | [Sıra hata kodları](/rest/api/storageservices/queue-service-error-codes) |
| Blob, tablo, kuyruk | [Depolama hatası kodları](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tablo, kuyruk |  [Sorun giderme](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Sonraki adımlar

- [Bir işlevi kuyruk depolama veri değişiklikleri (tetikleyici) olarak çalıştırma](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage

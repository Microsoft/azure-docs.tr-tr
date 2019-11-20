---
title: Azure Işlevleri için Azure kuyruk depolama bağlamaları
description: Azure 'da Azure kuyruk depolama tetikleyicisi ve çıkış bağlamayı nasıl kullanacağınızı anlayın.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure işlevleri, işlevler, olay işleme, dinamik işlem, sunucusuz mimari
ms.service: azure-functions
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: c7f143f5d026b2fa6fa34c75d3616b05c3e97092
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294300"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Işlevleri için Azure kuyruk depolama bağlamaları

Bu makalede Azure Işlevleri 'nde Azure kuyruk depolama bağlamalarıyla nasıl çalışılacağı açıklanmaktadır. Azure Işlevleri, kuyruklar için tetikleyici ve çıkış bağlamalarını destekler.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paketler-Işlevler 1. x

Kuyruk depolama bağlamaları [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet paketi, sürüm 2. x ' de verilmiştir. Paketin kaynak kodu, [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub deposundadır.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Paketler-Işlevler 2. x

Kuyruk depolama bağlamaları [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet paketi, sürüm 3. x içinde verilmiştir. Paketin kaynak kodu, [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub deposundadır.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encoding
İşlevler bir *Base64* kodlamalı dize bekler. Kodlama türünde yapılan ayarlamaların (verileri *Base64* kodlamalı bir dize olarak hazırlamak için), çağıran hizmette uygulanması gerekir.

## <a name="trigger"></a>Tetikleyici

Kuyruktaki yeni bir öğe alındığında bir işlev başlatmak için kuyruk tetikleyicisini kullanın. Kuyruk iletisi işleve giriş olarak sağlanır.

## <a name="trigger---example"></a>Tetikleyici-örnek

Dile özgü örneğe bakın:

* [C#](#trigger---c-example)
* [C#betik (. CSX)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Tetikleyici- C# örnek

Aşağıdaki örnek, `myqueue-items` kuyruğunu yoklayan ve kuyruk öğesi her işlendiğinde bir günlük yazan bir [ C# işlevi](functions-dotnet-class-library.md) gösterir.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Tetikleyici- C# betik örneği

Aşağıdaki örnek, bağlamayı kullanan bir *function. JSON* dosyası ve [ C# betik (. CSX)](functions-reference-csharp.md) kodunda bir sıra tetikleyicisi bağlamasını gösterir. İşlevi `myqueue-items` sırasını yoklar ve bir kuyruk öğesi işlendiğinde bir günlük yazar.

İşte *function. JSON* dosyası:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[Yapılandırma](#trigger---configuration) bölümünde bu özellikler açıklanmaktadır.

C# Betik kodu aşağıda verilmiştir:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

[Kullanım](#trigger---usage) bölümünde, function. json içinde `name` özelliği tarafından adlandırılan `myQueueItem` açıklanmaktadır.  [İleti meta verileri bölümü](#trigger---message-metadata) gösterilen diğer değişkenlerin tümünü açıklar.

### <a name="trigger---javascript-example"></a>Trigger-JavaScript örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir kuyruk tetikleyicisi bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi `myqueue-items` sırasını yoklar ve bir kuyruk öğesi işlendiğinde bir günlük yazar.

İşte *function. JSON* dosyası:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[Yapılandırma](#trigger---configuration) bölümünde bu özellikler açıklanmaktadır.

> [!NOTE]
> Ad parametresi, kuyruk öğesi yükünü içeren JavaScript kodunda `context.bindings.<name>` olarak yansıtır. Bu yük aynı zamanda işleve ikinci parametre olarak geçirilir.

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

[Kullanım](#trigger---usage) bölümünde, function. json içinde `name` özelliği tarafından adlandırılan `myQueueItem` açıklanmaktadır.  [İleti meta verileri bölümü](#trigger---message-metadata) gösterilen diğer değişkenlerin tümünü açıklar.

### <a name="trigger---java-example"></a>Trigger-Java örneği

Aşağıdaki Java örneği, tetiklenen iletiyi sıraya kaydeden `myqueuename` olarak kaydeden bir depolama kuyruğu tetikleme işlevlerini gösterir.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

### <a name="trigger---python-example"></a>Trigger-Python örneği

Aşağıdaki örnek, bir tetikleyici aracılığıyla işleve geçirilen bir sıra iletisinin nasıl okunacağını gösterir.

Bir depolama kuyruğu tetikleyicisi, *Type* 'ın `queueTrigger` olarak ayarlandığı *function. JSON* içinde tanımlanır.

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
    }
  ]
}
```

*_@No__t-2init_\_.py* kodu, işlevinizdeki sıra iletisini okumanızı sağlayan `func.ServiceBusMessage` olarak bir parametre bildirir.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Tetikleyici-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda, bir kuyruk tetikleyicisi yapılandırmak için aşağıdaki öznitelikleri kullanın:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Aşağıdaki örnekte gösterildiği gibi özniteliğin Oluşturucusu, izlenecek sıranın adını alır:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için `Connection` özelliğini ayarlayabilirsiniz:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Tam bir örnek için bkz. [tetikleyici- C# örnek](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Kullanılacak depolama hesabını belirtmek için başka bir yol sağlar. Oluşturucu, depolama bağlantı dizesi içeren bir uygulama ayarının adını alır. Özniteliği parametresi, yöntemi veya sınıf düzeyinde uygulanabilir. Aşağıdaki örnek, sınıf düzeyi ve Yöntem düzeyini gösterir:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Kullanılacak depolama hesabı aşağıdaki sırayla belirlenir:

* @No__t-0 özniteliğinin `Connection` özelliği.
* @No__t-0 özniteliği, `QueueTrigger` özniteliğiyle aynı parametreye uygulandı.
* İşleve uygulanan `StorageAccount` özniteliği.
* Sınıfına uygulanan `StorageAccount` özniteliği.
* "AzureWebJobsStorage" uygulama ayarı.

## <a name="trigger---configuration"></a>Tetikleyici-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `QueueTrigger` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | Yok| @No__t-0 olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**direction**| Yok | Yalnızca *function. JSON* dosyasında. @No__t-0 olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | Yok |İşlev kodundaki kuyruk öğesi yükünü içeren değişkenin adı.  |
|**Adı** | **Adı**| Yoklamaya yönelik kuyruğun adı. |
|**bağlanma** | **Bağlanma** |Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, `connection` ' ı "MyStorage" olarak ayarlarsanız, Işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. @No__t-0 boş bırakırsanız, Işlevler çalışma zamanı `AzureWebJobsStorage` adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Tetikleyici-kullanım

C# Ve C# komut dosyasında, `string paramName` gibi bir yöntem parametresi kullanarak ileti verilerine erişin. C# Komut dosyasında `paramName`, *function. json*' nin `name` özelliğinde belirtilen değerdir. Aşağıdaki türlerden birine bağlanabilirsiniz:

* Nesne-Işlevler çalışma zamanı, kodunuzda tanımlanan rastgele bir sınıfın örneğine bir JSON yükünü seri durumdan çıkarır. 
* `string`
* `byte[]`
* [CloudQueueMessage]

@No__t-0 ' a bağlamaya ve bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

JavaScript 'te, kuyruk öğesi yüküne erişmek için `context.bindings.<name>` kullanın. Yük JSON ise, bir nesne için seri hale gelir.

## <a name="trigger---message-metadata"></a>Tetikleyici-ileti meta verileri

Sıra tetikleyicisi çeşitli [meta veri özellikleri](./functions-bindings-expressions-patterns.md#trigger-metadata)sağlar. Bu özellikler, diğer bağlamalardaki veya kodunuzda parametre olarak bağlama ifadelerinin bir parçası olarak kullanılabilir. Bunlar [Cloudqueuemessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) sınıfının özellikleridir.

|Özellik|Tür|Açıklama|
|--------|----|-----------|
|`QueueTrigger`|`string`|Kuyruk yükü (geçerli bir dize varsa). Bir dize olarak Kuyruk iletisi yükü `QueueTrigger`, *function. JSON*içindeki `name` özelliği tarafından adlandırılan değişkenle aynı değere sahiptir.|
|`DequeueCount`|`int`|Bu iletinin sıraya alınma sayısı.|
|`ExpirationTime`|`DateTimeOffset`|İletinin süre sonu.|
|`Id`|`string`|Kuyruk ileti KIMLIĞI.|
|`InsertionTime`|`DateTimeOffset`|İletinin sıraya eklendiği zaman.|
|`NextVisibleTime`|`DateTimeOffset`|İletinin bir sonraki görünür olacağı zaman.|
|`PopReceipt`|`string`|İletinin pop makbuzu.|

## <a name="trigger---poison-messages"></a>Tetikleyici-zarar iletileri

Bir kuyruk tetikleyicisi işlevi başarısız olduğunda, Azure Işlevleri, ilk deneme dahil olmak üzere belirli bir kuyruk iletisi için işlevi en fazla beş kez yeniden dener. Beş deneme başarısız olursa, işlevler çalışma zamanı *&lt;originalsıraadı >-Poison*adlı bir kuyruğa ileti ekler. Onları günlüğe kaydederek veya el ile ilgilenilmesi gereken bir bildirim göndererek, zarar kuyruğundan iletileri işlemek için bir işlev yazabilirsiniz.

Zarar iletilerini el ile işlemek için kuyruk iletisinin [Dequeuecount](#trigger---message-metadata) değerini kontrol edin.

## <a name="trigger---polling-algorithm"></a>Tetikleyici-yoklama algoritması

Sıra tetikleyicisi, depolama işlem maliyetlerinde boşta sıra yoklamanın etkisini azaltmak için rastgele bir üstel geri alma algoritması uygular.  Bir ileti bulunduğunda, çalışma zamanı iki saniye bekler ve sonra başka bir ileti olup olmadığını denetler; hiçbir ileti bulunamadığında, yeniden denemeden önce dört saniye bekler. Sonraki başarısız bir kuyruk iletisi almaya çalıştıktan sonra, bekleme süresi, varsayılan olarak bir dakika olacak şekilde en fazla bekleme süresine ulaşana kadar artmaya devam eder. En uzun bekleme süresi, [Host. JSON dosyasındaki](functions-host-json.md#queues)`maxPollingInterval` özelliği aracılığıyla yapılandırılabilir.

## <a name="trigger---concurrency"></a>Tetikleyici-eşzamanlılık

Bekleyen birden çok kuyruk iletisi olduğunda, kuyruk tetikleyicisi bir toplu ileti alır ve işlev örneklerini işlemek için eşzamanlı olarak çağırır. Varsayılan olarak, toplu iş boyutu 16 ' dır. İşlenen sayı 8 ' e iniyorsa, çalışma zamanı başka bir Batch alır ve bu iletileri işlemeye başlar. Bu nedenle, bir sanal makinede (VM) işlev başına işlenen en fazla eşzamanlı ileti sayısı 24 ' dir. Bu sınır, her bir sanal makinenin her bir sıraya göre tetiklenen işleve ayrı olarak uygulanır. İşlev uygulamanız birden çok VM 'ye ölçeklenirken, her sanal makine Tetikleyicileri bekler ve işlevleri çalıştırmaya çalışır. Örneğin, bir işlev uygulaması 3 VM 'ye ölçekleniyorsa, bir sıra tetiklenen işlevin varsayılan en fazla eşzamanlı örneği 72 ' dir.

Toplu iş boyutu ve yeni bir toplu işlem alma eşiği, [Host. json dosyasında](functions-host-json.md#queues)yapılandırılabilir. Bir işlev uygulamasındaki Queue-tetiklenen işlevler için paralel yürütmeyi en aza indirmek isterseniz, toplu iş boyutunu 1 olarak ayarlayabilirsiniz. Bu ayar, yalnızca işlev uygulamanız tek bir sanal makinede (VM) çalıştığı sürece eşzamanlılık ortadan kaldırır. 

Sıra tetikleyicisi, bir işlevin bir sıra iletisini birden çok kez işlemesini otomatik olarak önler; işlevlerin ıdempotent olarak yazılması gerekmez.

## <a name="trigger---hostjson-properties"></a>Trigger-Host. JSON özellikleri

[Host. JSON](functions-host-json.md#queues) dosyası, sıra tetikleyicisi davranışını denetleyen ayarları içerir. Kullanılabilir ayarlarla ilgili ayrıntılar için [Host. JSON ayarları](#hostjson-settings) bölümüne bakın.

## <a name="output"></a>Çıktı

Bir kuyruğa ileti yazmak için Azure kuyruk depolama çıkış bağlamasını kullanın.

## <a name="output---example"></a>Çıkış-örnek

Dile özgü örneğe bakın:

* [C#](#output---c-example)
* [C#betik (. CSX)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Çıkış- C# örnek

Aşağıdaki örnek, alınan her http isteği için bir kuyruk iletisi oluşturan bir [ C# işlevi](functions-dotnet-class-library.md) gösterir.

```csharp
[StorageAccount("AzureWebJobsStorage")]
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

### <a name="output---c-script-example"></a>Çıkış C# betiği örneği

Aşağıdaki örnek, bağlamayı kullanan bir *function. JSON* dosyası ve [ C# betik (. CSX)](functions-reference-csharp.md) kodunda bir http tetikleyici bağlamasını gösterir. İşlevi, alınan her HTTP isteği için bir **Customqueuemessage** nesne yüküne sahip bir kuyruk öğesi oluşturur.

İşte *function. JSON* dosyası:

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
      "name": "return"
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

[Yapılandırma](#output---configuration) bölümünde bu özellikler açıklanmaktadır.

Tek bir C# sıra iletisi oluşturan betik kodu aşağıda verilmiştir:

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

@No__t-0 veya `IAsyncCollector` parametresini kullanarak bir kerede birden çok ileti gönderebilirsiniz. Aşağıda, C# biri http istek verileriyle, diğeri ise sabit kodlanmış değerlerle birden çok ileti gönderen betik kodu verilmiştir:

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

### <a name="output---javascript-example"></a>Output-JavaScript örneği

Aşağıdaki örnek, bir *function. JSON* DOSYASıNDAKI bir http tetikleyicisi bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi, alınan her HTTP isteği için bir kuyruk öğesi oluşturur.

İşte *function. JSON* dosyası:

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
      "name": "return"
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

[Yapılandırma](#output---configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

@No__t-0 çıkış bağlaması için bir ileti dizisi tanımlayarak bir kerede birden çok ileti gönderebilirsiniz. Aşağıdaki JavaScript kodu, alınan her HTTP isteği için sabit kodlanmış değerler içeren iki kuyruk iletisi gönderir.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Output-Java örneği

 Aşağıdaki örnek, bir HTTP isteği tarafından tetiklendikleri sırada bir kuyruk iletisi oluşturan Java işlevini gösterir.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, değeri kuyruk depolamaya yazılacak olan parametrelerde `@QueueOutput` ek açıklamasını kullanın.  Parametre türü `OutputBinding<T>` olmalıdır; burada T bir POJO 'nın herhangi bir yerel Java türüdür.

### <a name="output---python-example"></a>Output-Python örneği

Aşağıdaki örnek, Depolama kuyruklarına tek ve birden çok değerin nasıl alınacağını gösterir. *Function. JSON* için gereken yapılandırma her iki yoldan de aynıdır.

Bir depolama kuyruğu bağlama, *Type* 'ın `queue` olarak ayarlandığı *function. JSON* içinde tanımlanır.

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

Kuyrukta tek bir ileti ayarlamak için `set` yöntemine tek bir değer geçirirsiniz.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Kuyrukta birden çok ileti oluşturmak için, uygun liste türü olarak bir parametre bildirin ve `set` yöntemine bir değer dizisi geçirin (liste türüyle eşleşen).

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

## <a name="output---attributes"></a>Çıkış-öznitelikler

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [queueattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs)' ı kullanın.

Öznitelik `out` parametresi veya işlevin dönüş değeri için geçerlidir. Aşağıdaki örnekte gösterildiği gibi özniteliğin Oluşturucusu sıranın adını alır:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için `Connection` özelliğini ayarlayabilirsiniz:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Tam bir örnek için bkz. [Çıkış- C# örnek](#output---c-example).

Sınıf, yöntem veya parametre düzeyinde depolama hesabını belirtmek için `StorageAccount` özniteliğini kullanabilirsiniz. Daha fazla bilgi için bkz. tetikleyici-öznitelikler.

## <a name="output---configuration"></a>Çıkış-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `Queue` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | Yok | @No__t-0 olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**direction** | Yok | @No__t-0 olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | Yok | İşlev kodundaki kuyruğu temsil eden değişkenin adı. İşlev dönüş değerine başvurmak için `$return` olarak ayarlayın.|
|**Adı** |**Adı** | Kuyruğun adı. |
|**bağlanma** | **Bağlanma** |Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, `connection` ' ı "MyStorage" olarak ayarlarsanız, Işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. @No__t-0 boş bırakırsanız, Işlevler çalışma zamanı `AzureWebJobsStorage` adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Çıkış kullanımı

Ve C# C# betikte, `out T paramName` gibi bir yöntem parametresi kullanarak tek bir kuyruk iletisi yazın. C# Komut dosyasında `paramName`, *function. json*' nin `name` özelliğinde belirtilen değerdir. @No__t-0 parametresi yerine yöntem dönüş türünü kullanabilirsiniz ve `T` aşağıdaki türlerden herhangi biri olabilir:

* JSON olarak seri hale getirilebilir bir nesne
* `string`
* `byte[]`
* [CloudQueueMessage] 

@No__t-0 ' a bağlamaya ve bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

C# Ve C# komut dosyasında, aşağıdaki türlerden birini kullanarak birden çok kuyruk iletisi yazın: 

* `ICollector<T>` veya `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

JavaScript işlevlerinde, çıkış kuyruğu iletisine erişmek için `context.bindings.<name>` kullanın. Kuyruk öğesi yükü için bir dize veya JSON-serileştirilebilir nesnesi kullanabilirsiniz.


## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlayıcısı |  Başvuru |
|---|---|
| Kuyruk | [Sıra hata kodları](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, tablo, kuyruk | [Depolama hatası kodları](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tablo, kuyruk |  [Sorun giderme](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host. JSON ayarları

Bu bölümde, sürüm 2. x içinde bu bağlama için kullanılabilen genel yapılandırma ayarları açıklanmaktadır. Aşağıdaki örnek Host. JSON dosyası, bu bağlamanın yalnızca sürüm 2. x ayarlarını içerir. Sürüm 2. x içindeki genel yapılandırma ayarları hakkında daha fazla bilgi için bkz. [Azure işlevleri sürüm 2. x için Host. JSON başvurusu](functions-host-json.md).

> [!NOTE]
> 1\. x Işlevleri içindeki Host. JSON başvurusu için bkz. [Azure işlevleri için Host. JSON başvurusu 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```


|Özellik  |Varsayılan | Açıklama |
|---------|---------|---------|
|Maxpollingınterval|00:00:01|Sıra yoklamaları arasındaki en uzun Aralık. En az 00:00:00.100 (100 ms) ve 00:01:00 (1 dak) artar.  1\. x içinde veri türü milisaniyedir ve 2. x içinde bir TimeSpan değeri vardır.|
|visibilityTimeout|00:00:00|Bir ileti işlenirken yeniden denemeler arasındaki zaman aralığı başarısız olur. |
|batchSize|16|Işlevlerin çalışma zamanının aynı anda ve işlemleri paralel olarak aldığı sıra iletilerinin sayısı. İşlenen sayı `newBatchThreshold` ' a doğru aldığında, çalışma zamanı başka bir toplu iş alır ve bu iletileri işlemeye başlar. Bu nedenle, işlev başına işlenen en fazla eşzamanlı ileti sayısı `batchSize` ve `newBatchThreshold` ' dir. Bu sınır, kuyruğa tetiklenen her bir işlev için ayrı olarak uygulanır. <br><br>Bir kuyrukta alınan iletiler için paralel yürütmeyi önlemek istiyorsanız, `batchSize` ' ı 1 olarak ayarlayabilirsiniz. Ancak, bu ayar yalnızca işlev uygulamanız tek bir sanal makinede (VM) çalıştığı sürece eşzamanlılık ortadan kaldırır. İşlev uygulaması birden çok VM 'ye ölçekleniyorsa, her VM, her bir kuyruk tetiklenen işlevin bir örneğini çalıştırabilir.<br><br>En fazla `batchSize` 32. |
|maxDequeueCount|5|Zarar sırasına taşımadan önce bir iletiyi işlemeyi deneme sayısı.|
|newBatchThreshold|batchSize/2|Aynı anda işlenen ileti sayısı bu sayıya indiğinde, çalışma zamanı başka bir toplu işi alır.|

## <a name="next-steps"></a>Sonraki adımlar

* [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Kuyruk depolama çıkış bağlaması kullanan bir öğreticiye gitme](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage

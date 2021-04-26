---
title: Azure Işlevleri için Azure kuyruk depolama tetikleyicisi
description: Azure kuyruk depolama verileri değişiklikleri olarak bir Azure Işlevi çalıştırmayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: f4477a09f151695b826d0becf28e92ceaf3f9e85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453215"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure Işlevleri için Azure kuyruk depolama tetikleyicisi

Kuyruk depolama tetikleyicisi, Azure kuyruk depolamaya iletiler eklendikçe bir işlevi çalıştırır.

## <a name="encoding"></a>Encoding

İşlevler bir *Base64* kodlamalı dize bekler. Kodlama türünde yapılan ayarlamaların (verileri *Base64* kodlamalı bir dize olarak hazırlamak için), çağıran hizmette uygulanması gerekir.

## <a name="example"></a>Örnek

Kuyruktaki yeni bir öğe alındığında bir işlev başlatmak için kuyruk tetikleyicisini kullanın. Kuyruk iletisi işleve giriş olarak sağlanır.

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnek, kuyruğu [](functions-dotnet-class-library.md) `myqueue-items` yoklayan ve kuyruk öğesi her işlendiğinde bir günlük yazan bir C# işlevini gösterir.

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

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bağlamayı kullanan dosya ve [C# betiği (. CSX)](functions-reference-csharp.md) kodundaki *function.js* bir sıra tetikleyicisi bağlamasını gösterir. İşlevi `myqueue-items` kuyruğu yoklar ve bir kuyruk öğesi işlendiğinde bir günlük yazar.

Dosyada *function.js* :

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

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

C# betik kodu aşağıda verilmiştir:

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

[Kullanım](#usage) bölümü `myQueueItem` , `name` üzerinde function.jsözelliği tarafından adlandırılan açıklanmaktadır.  [İleti meta verileri bölümü](#message-metadata) gösterilen diğer değişkenlerin tümünü açıklar.

# <a name="java"></a>[Java](#tab/java)

Aşağıdaki Java örneğinde, tetiklenen iletiyi kuyruğa kaydeden bir depolama kuyruğu tetikleme işlevi gösterilmektedir `myqueuename` .

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosyadaki bir kuyruk tetikleyicisi bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlevi `myqueue-items` kuyruğu yoklar ve bir kuyruk öğesi işlendiğinde bir günlük yazar.

Dosyada *function.js* :

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

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

> [!NOTE]
> Ad parametresi, `context.bindings.<name>` kuyruk öğesi yükünü Içeren JavaScript kodunda olduğu gibi yansıtır. Bu yük aynı zamanda işleve ikinci parametre olarak geçirilir.

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

[Kullanım](#usage) bölümü `myQueueItem` , `name` üzerinde function.jsözelliği tarafından adlandırılan açıklanmaktadır.  [İleti meta verileri bölümü](#message-metadata) gösterilen diğer değişkenlerin tümünü açıklar.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki örnek, bir tetikleyici aracılığıyla işleve geçirilen bir sıra iletisinin nasıl okunacağını gösterir.

Depolama kuyruğu tetikleyicisi, olarak ayarlandığı dosyada *function.js* tanımlanır `type` `queueTrigger` .

```json
{
  "bindings": [
    {
      "name": "QueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

*Run.ps1* dosyasındaki kod, `$QueueItem` işlevinizdeki sıra iletisini okumanızı sağlayan olarak bir parametresini bildirir.

```powershell
# Input bindings are passed in via param block.
param([string] $QueueItem, $TriggerMetadata)

# Write out the queue message and metadata to the information log.
Write-Host "PowerShell queue trigger function processed work item: $QueueItem"
Write-Host "Queue item expiration time: $($TriggerMetadata.ExpirationTime)"
Write-Host "Queue item insertion time: $($TriggerMetadata.InsertionTime)"
Write-Host "Queue item next visible time: $($TriggerMetadata.NextVisibleTime)"
Write-Host "ID: $($TriggerMetadata.Id)"
Write-Host "Pop receipt: $($TriggerMetadata.PopReceipt)"
Write-Host "Dequeue count: $($TriggerMetadata.DequeueCount)"
```

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, bir tetikleyici aracılığıyla işleve geçirilen bir sıra iletisinin nasıl okunacağını gösterir.

Depolama kuyruğu tetikleyicisi, *türünün türü* olarak ayarlandığı *function.js* tanımlanır `queueTrigger` .

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

*_\_ İnit_ \_ . Kopyala* kodu bir parametre bildirir `func.QueueMessage` ve bu, işlevinizdeki sıra iletisini okumanızı sağlar.

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

 ---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md), bir kuyruk tetikleyicisi yapılandırmak için aşağıdaki öznitelikleri kullanın:

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

  `Connection`Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabı bağlantı dizesini içeren uygulama ayarını belirtmek için özelliğini ayarlayabilirsiniz:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Tüm örnek için bkz. [örnek](#example).

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

* `QueueTrigger`Özniteliğin `Connection` özelliği.
* Özniteliği `StorageAccount` ile aynı parametreye uygulanan öznitelik `QueueTrigger` .
* `StorageAccount`İşleve uygulanan öznitelik.
* `StorageAccount`Sınıfına uygulanan öznitelik.
* "AzureWebJobsStorage" uygulama ayarı.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

`QueueTrigger`Ek açıklama, işlevi tetikleyen sıraya erişmenizi sağlar. Aşağıdaki örnek, kuyruk iletisini parametresi aracılığıyla işlev için kullanılabilir hale getirir `message` .

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Özellik    | Açıklama |
|-------------|-----------------------------|
|`name`       | İşlev imzasında parametre adını bildirir. İşlev tetiklendiğinde, bu parametrenin değeri kuyruk iletisinin içeriğine sahiptir. |
|`queueName`  | Depolama hesabındaki sıra adını bildirir. |
|`connection` | Depolama hesabı bağlantı dizesine işaret eder. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Öznitelikler PowerShell tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `QueueTrigger` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok| Olarak ayarlanmalıdır `queueTrigger` . Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**Görünüm**| yok | Yalnızca *function.js* dosyasında. Olarak ayarlanmalıdır `in` . Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. |
|**ada** | yok |İşlev kodundaki kuyruk öğesi yükünü içeren değişkenin adı.  |
|**Adı** | **Adı**| Yoklamaya yönelik kuyruğun adı. |
|**bağlanma** | **Bağlantı** |Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz.<br><br>Örneğin, `connection` "Mystorage" olarak ayarlarsanız, işlevler çalışma zamanı "MyStorage" adlı bir uygulama ayarı arar. `connection`Boş bırakırsanız, işlevler çalışma zamanı adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır `AzureWebJobsStorage` .<br><br>[Uzantının 5. x veya daha yüksek bir sürümünü](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)kullanıyorsanız, bağlantı dizesi yerine bağlantıyı tanımlayan bir yapılandırma bölümüne başvuru sağlayabilirsiniz. Bkz. [Bağlantılar](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Varsayılan

Gibi bir yöntem parametresi kullanarak ileti verilerine erişin `string paramName` . Aşağıdaki türlerden birine bağlanabilirsiniz:

* Nesne-Işlevler çalışma zamanı, kodunuzda tanımlanan rastgele bir sınıfın örneğine bir JSON yükünü seri durumdan çıkarır.
* `string`
* `byte[]`
* [CloudQueueMessage]

' `CloudQueueMessage` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

### <a name="additional-types"></a>Ek türler

[Depolama uzantısının 5.0.0 veya daha yüksek sürümünü](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) kullanan uygulamalar, [.NET için Azure SDK 'sının](/dotnet/api/overview/azure/storage.queues-readme)türlerini de kullanabilir. Bu sürüm, `CloudQueueMessage` aşağıdaki türlerin yerine eski tür için desteği bırakır:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
 
Bu türleri kullanan örnekler için, [uzantının GitHub deposuna](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)bakın.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

### <a name="default"></a>Varsayılan

Gibi bir yöntem parametresi kullanarak ileti verilerine erişin `string paramName` . , `paramName` `name` *Üzerindefunction.js* özelliğinde belirtilen değerdir. Aşağıdaki türlerden birine bağlanabilirsiniz:

* Nesne-Işlevler çalışma zamanı, kodunuzda tanımlanan rastgele bir sınıfın örneğine bir JSON yükünü seri durumdan çıkarır. 
* `string`
* `byte[]`
* [CloudQueueMessage]

' `CloudQueueMessage` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

### <a name="additional-types"></a>Ek türler

[Depolama uzantısının 5.0.0 veya daha yüksek sürümünü](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) kullanan uygulamalar, [.NET için Azure SDK 'sının](/dotnet/api/overview/azure/storage.queues-readme)türlerini de kullanabilir. Bu sürüm, `CloudQueueMessage` aşağıdaki türlerin yerine eski tür için desteği bırakır:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)

Bu türleri kullanan örnekler için, [uzantının GitHub deposuna](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)bakın.

# <a name="java"></a>[Java](#tab/java)

[Queuetrigger](/java/api/com.microsoft.azure.functions.annotation.queuetrigger) ek açıklaması, işlevi tetikleyen kuyruk iletisine erişmenizi sağlar.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kuyruk öğesi yükü, `context.bindings.<NAME>` `<NAME>` *üzerindefunction.js* tanımlanan adla eşleşen bir ile kullanılabilir. Yük JSON ise, değer bir nesne olarak seri durumdan çıkarılacak.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Dosyadakifunction.jsbağlama parametresi tarafından belirlenen adla eşleşen dize parametresi aracılığıyla kuyruk iletisine erişin `name` . 

# <a name="python"></a>[Python](#tab/python)

Sıra iletisine [queuemessage](/python/api/azure-functions/azure.functions.queuemessage)olarak yazılan parametre aracılığıyla erişin.

---

## <a name="message-metadata"></a>İleti meta verileri

Sıra tetikleyicisi çeşitli [meta veri özellikleri](./functions-bindings-expressions-patterns.md#trigger-metadata)sağlar. Bu özellikler, diğer bağlamalardaki veya kodunuzda parametre olarak bağlama ifadelerinin bir parçası olarak kullanılabilir. Özellikler [Cloudqueuemessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) sınıfının üyeleridir.

|Özellik|Tür|Description|
|--------|----|-----------|
|`QueueTrigger`|`string`|Kuyruk yükü (geçerli bir dize varsa). Kuyruk iletisi yükü bir dizeyse, `QueueTrigger` `name` *üzerindefunction.js* özelliği tarafından adlandırılan değişkenle aynı değere sahiptir.|
|`DequeueCount`|`int`|Bu iletinin sıraya alınma sayısı.|
|`ExpirationTime`|`DateTimeOffset`|İletinin süre sonu.|
|`Id`|`string`|Kuyruk ileti KIMLIĞI.|
|`InsertionTime`|`DateTimeOffset`|İletinin sıraya eklendiği zaman.|
|`NextVisibleTime`|`DateTimeOffset`|İletinin bir sonraki görünür olacağı zaman.|
|`PopReceipt`|`string`|İletinin pop makbuzu.|

## <a name="poison-messages"></a>Zarar iletileri

Bir kuyruk tetikleyicisi işlevi başarısız olduğunda, Azure Işlevleri, ilk deneme dahil olmak üzere belirli bir kuyruk iletisi için işlevi en fazla beş kez yeniden dener. Tüm beş deneme başarısız olursa, işlevler çalışma zamanı *&lt; originalsıra>-Poison* adlı bir kuyruğa ileti ekler. Onları günlüğe kaydederek veya el ile ilgilenilmesi gereken bir bildirim göndererek, zarar kuyruğundan iletileri işlemek için bir işlev yazabilirsiniz.

Zarar iletilerini el ile işlemek için kuyruk iletisinin [Dequeuecount](#message-metadata) değerini kontrol edin.

## <a name="polling-algorithm"></a>Yoklama algoritması

Sıra tetikleyicisi, depolama işlem maliyetlerinde boşta sıra yoklamanın etkisini azaltmak için rastgele bir üstel geri alma algoritması uygular.

Algoritma aşağıdaki mantığı kullanır:

- Bir ileti bulunduğunda, çalışma zamanı iki saniye bekler ve sonra başka bir ileti arar
- Hiçbir ileti bulunamadığında, yeniden denemeden önce dört saniye bekler.
- Sonraki başarısız bir kuyruk iletisi almaya çalıştıktan sonra, bekleme süresi, varsayılan olarak bir dakika olacak şekilde en fazla bekleme süresine ulaşana kadar artmaya devam eder.
- En uzun bekleme süresi, `maxPollingInterval` [ dosyadakihost.js](functions-host-json-v1.md#queues)özelliği aracılığıyla yapılandırılabilir.

Yerel geliştirme için en fazla yoklama aralığı varsayılan olarak iki saniyedir.

Faturalandırmayla ilgili olarak, çalışma zamanı tarafından yoklama harcanan süre "ücretsizdir" ve hesabınıza göre sayılmaz.

## <a name="concurrency"></a>Eşzamanlılık

Bekleyen birden çok kuyruk iletisi olduğunda, kuyruk tetikleyicisi bir toplu ileti alır ve işlev örneklerini işlemek için eşzamanlı olarak çağırır. Varsayılan olarak, toplu iş boyutu 16 ' dır. İşlenen sayı 8 ' e iniyorsa, çalışma zamanı başka bir Batch alır ve bu iletileri işlemeye başlar. Bu nedenle, bir sanal makinede (VM) işlev başına işlenen en fazla eşzamanlı ileti sayısı 24 ' dir. Bu sınır, her bir sanal makinenin her bir sıraya göre tetiklenen işleve ayrı olarak uygulanır. İşlev uygulamanız birden çok VM 'ye ölçeklenirken, her sanal makine Tetikleyicileri bekler ve işlevleri çalıştırmaya çalışır. Örneğin, bir işlev uygulaması 3 VM 'ye ölçekleniyorsa, bir sıra tetiklenen işlevin varsayılan en fazla eşzamanlı örneği 72 ' dir.

Toplu iş boyutu ve yeni bir toplu iş alma eşiği [host.jsdosya üzerinde](functions-host-json.md#queues)yapılandırılabilir. Bir işlev uygulamasındaki Queue-tetiklenen işlevler için paralel yürütmeyi en aza indirmek isterseniz, toplu iş boyutunu 1 olarak ayarlayabilirsiniz. Bu ayar, yalnızca işlev uygulamanız tek bir sanal makinede (VM) çalıştığı sürece eşzamanlılık ortadan kaldırır. 

Kuyruk tetikleyicisi, bir işlevin aynı anda bir kuyruk iletisini işlemesini otomatik olarak önler.

## <a name="hostjson-properties"></a>host.jsözelliklerde

Dosyadaki [host.js](functions-host-json.md#queues) , sıra tetikleyicisi davranışını denetleyen ayarları içerir. Kullanılabilir ayarlarla ilgili ayrıntılar için [ ayarlarıhost.js](functions-bindings-storage-queue.md#hostjson-settings) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Yazma kuyruğu depolama iletileri (çıkış bağlaması)](./functions-bindings-storage-queue-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage

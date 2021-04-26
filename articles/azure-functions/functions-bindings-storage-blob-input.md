---
title: Azure Işlevleri için Azure Blob depolama girişi bağlama
description: Azure Işlevine Azure Blob depolama girişi bağlama verileri sağlamayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 191722d02b493cfe0197c3e45771543fd8c5926a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961056"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Azure Işlevleri için Azure Blob depolama girişi bağlama

Giriş bağlama, BLOB depolama verilerini bir Azure Işlevine girdi olarak okumanızı sağlar.

Kurulum ve yapılandırma ayrıntıları hakkında bilgi için bkz. [genel bakış](./functions-bindings-storage-blob.md).

## <a name="example"></a>Örnek

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnek, bir kuyruk tetikleyicisi ve bir giriş blobu bağlama kullanan bir [C# işlevidir](functions-dotnet-class-library.md) . Kuyruk iletisi Blobun adını içerir ve işlev Blobun boyutunu günlüğe kaydeder.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

<!--Same example for input and output. -->

Aşağıdaki örnekte, bağlamaları kullanan dosya ve [C# betiği (. CSX)](functions-reference-csharp.md) kodundaki *function.js* blob giriş ve çıkış bağlamaları gösterilmektedir. İşlevi, bir metin blobunun kopyasını oluşturur. İşlev, kopyalanacak Blobun adını içeren bir kuyruk iletisi tarafından tetiklenir. Yeni blob *{originalblobname}-Copy* olarak adlandırılmıştır.

Dosyadaki *function.js* , `queueTrigger` özelliklerde blob adını belirtmek için meta veri özelliği kullanılır `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

C# betik kodu aşağıda verilmiştir:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

Bu bölüm aşağıdaki örnekleri içerir:

* [HTTP tetikleyicisi, sorgu dizesinden blob adı ara](#http-trigger-look-up-blob-name-from-query-string)
* [Kuyruk tetikleyicisi, kuyruk iletisinden blob adı Al](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP tetikleyicisi, sorgu dizesinden blob adı ara

 Aşağıdaki örnek, `HttpTrigger` bir BLOB depolama kapsayıcısında bir dosyanın adını içeren bir parametre almak için ek açıklamayı kullanan bir Java işlevini gösterir. `BlobInput`Daha sonra ek açıklama dosyayı okur ve içeriğini işleve ' a geçirir `byte[]` .

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Kuyruk tetikleyicisi, kuyruk iletisinden blob adı Al

 Aşağıdaki örnek, `QueueTrigger` bir BLOB depolama kapsayıcısında bir dosyanın adını içeren bir ileti almak için ek açıklamayı kullanan bir Java işlevini gösterir. `BlobInput`Daha sonra ek açıklama dosyayı okur ve içeriğini işleve ' a geçirir `byte[]` .

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, `@BlobInput` değeri bir Blobun geldiği parametrelerde ek açıklamayı kullanın.  Bu ek açıklama, kullanılarak yerel Java türleri, POJOs veya null atanabilir değerlerle kullanılabilir `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Aşağıdaki örnek, dosya ve bağlamaları kullanan [JavaScript kodundaki](functions-reference-node.md) *function.js* blob giriş ve çıkış bağlamalarını gösterir. İşlevi bir Blobun kopyasını oluşturur. İşlev, kopyalanacak Blobun adını içeren bir kuyruk iletisi tarafından tetiklenir. Yeni blob *{originalblobname}-Copy* olarak adlandırılmıştır.

Dosyadaki *function.js* , `queueTrigger` özelliklerde blob adını belirtmek için meta veri özelliği kullanılır `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki örnek, gelen blob verilerini [PowerShell](functions-reference-powershell.md) işlevi için kullanılabilir hale getiren dosyasında _function.js_ tanımlanan bir blob giriş bağlamasını gösterir.

JSON yapılandırması şu şekildedir:

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

İşlev kodu aşağıda verilmiştir:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Aşağıdaki örnek, dosya ve bağlamaları kullanan [Python kodundaki](functions-reference-python.md) *function.js* blob giriş ve çıkış bağlamalarını gösterir. İşlevi bir Blobun kopyasını oluşturur. İşlev, kopyalanacak Blobun adını içeren bir kuyruk iletisi tarafından tetiklenir. Yeni blob *{originalblobname}-Copy* olarak adlandırılmıştır.

Dosyadaki *function.js* , `queueTrigger` özelliklerde blob adını belirtmek için meta veri özelliği kullanılır `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

`dataType`Özelliği hangi bağlamanın kullanıldığını belirler. Farklı bağlama stratejilerini desteklemek için aşağıdaki değerler mevcuttur:

| Bağlama değeri | Varsayılan | Açıklama | Örnek |
| --- | --- | --- | --- |
| `string` | N | Genel bağlamayı kullanır ve giriş türünü `string` | `def main(input: str)` |
| `binary` | N | Genel bağlamayı kullanır ve giriş blobunu `bytes` Python nesnesi olarak yayınlar | `def main(input: bytes)` |

`dataType`Özelliği üzerinde function.jstanımlı değilse, varsayılan değer olur `string` .

Python kodu aşağıda verilmiştir:

```python
import logging
import azure.functions as func


# The type func.InputStream is not supported for blob input binding.
# The input binding field inputblob can either be 'bytes' or 'str' depends
# on dataType in function.json, 'binary' or 'string'.
def main(queuemsg: func.QueueMessage, inputblob: bytes) -> bytes:
    logging.info(f'Python Queue trigger function processed {len(inputblob)} bytes')
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md) [blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)kullanın.

Özniteliğin Oluşturucusu, `FileAccess` Aşağıdaki örnekte gösterildiği gibi, blob 'un yolunu ve okundu veya yaz belirten bir parametreyi alır:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

`Connection`Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için özelliğini ayarlayabilirsiniz:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`StorageAccount`Sınıfı, yöntemi veya parametre düzeyinde depolama hesabını belirtmek için özniteliğini kullanabilirsiniz. Daha fazla bilgi için bkz. [tetikleyici-öznitelikler ve ek açıklamalar](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

`@BlobInput`Özniteliği, işlevi tetikleyen bloba erişmenizi sağlar. Özniteliği ile bir bayt dizisi kullanırsanız, `dataType` olarak ayarlayın `binary` . Ayrıntılar için [giriş örneğine](#example) bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Öznitelikler PowerShell tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `Blob` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok | Olarak ayarlanmalıdır `blob` . |
|**Görünüm** | yok | Olarak ayarlanmalıdır `in` . [Kullanım](#usage) bölümünde özel durumlar belirtilmiştir. |
|**ada** | yok | İşlev kodundaki blobu temsil eden değişkenin adı.|
|**Yolun** |**Blobpath değerini adıyla** | Blobun yolu. |
|**bağlanma** |**Bağlantı**| Bu bağlama için kullanılacak [depolama bağlantı dizesini](../storage/common/storage-configure-connection-string.md) içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, `connection` "MyStorage" olarak ayarlarsanız, işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. `connection`Boş bırakırsanız, işlevler çalışma zamanı adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır `AzureWebJobsStorage` .<br><br>Bağlantı dizesi, [yalnızca BLOB depolama hesabı](../storage/common/storage-account-overview.md#types-of-storage-accounts)değil, genel amaçlı bir depolama hesabı için olmalıdır.<br><br>[Uzantının 5. x veya daha yüksek bir sürümünü](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)kullanıyorsanız, bağlantı dizesi yerine bağlantıyı tanımlayan bir yapılandırma bölümüne başvuru sağlayabilirsiniz. Bkz. [Bağlantılar](./functions-reference.md#connections).|
|**dataType**| yok | Dinamik olarak belirlenmiş diller için, temel alınan veri türünü belirtir. Olası değerler `string` , `binary` , veya `stream` . Daha fazla ayrıntı için [Tetikleyiciler ve bağlamalar kavramlarını](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions)inceleyin. |
|yok | **Erişim** | Okuma veya yazma yapılıp yapılmayacağını belirtir. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobInput`Özniteliği, işlevi tetikleyen bloba erişmenizi sağlar. Özniteliği ile bir bayt dizisi kullanırsanız, `dataType` olarak ayarlayın `binary` . Ayrıntılar için [giriş örneğine](#example) bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<NAME>` `<NAME>` *Üzerindefunction.js* tanımlanan değerle eşleşen blob verilerine erişin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Blob verilerine, dosyanın _function.js_ , bağlamanın Name parametresiyle belirtilen adla eşleşen bir parametre aracılığıyla erişin.

# <a name="python"></a>[Python](#tab/python)

Blob verilerine [InputStream](/python/api/azure-functions/azure.functions.inputstream)olarak yazılan parametre aracılığıyla erişin. Ayrıntılar için [giriş örneğine](#example) bakın.

---

## <a name="next-steps"></a>Sonraki adımlar

- [BLOB depolama verileri değiştiğinde bir işlev Çalıştır](./functions-bindings-storage-blob-trigger.md)
- [Bir işlevden BLOB depolama verileri yazma](./functions-bindings-storage-blob-output.md)

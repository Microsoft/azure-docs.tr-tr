---
title: Azure Işlevleri için Azure Blob depolama çıkış bağlaması
description: Azure Işlevine Azure Blob depolama çıkışı bağlama verileri sağlamayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: bb5382014f82854086d6ec6f07cfe7f67e80726a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961039"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Azure Işlevleri için Azure Blob depolama çıkış bağlaması

Çıktı bağlama, bir Azure Işlevindeki BLOB depolama verilerini değiştirmenize ve silmenizi sağlar.

Kurulum ve yapılandırma ayrıntıları hakkında bilgi için bkz. [genel bakış](./functions-bindings-storage-blob.md).

## <a name="example"></a>Örnek

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnek bir blob tetikleyicisi ve iki çıkış blobu bağlaması kullanan bir [C# işlevidir](functions-dotnet-class-library.md) . İşlev, *örnek görüntüler* kapsayıcısında bir görüntü blobu oluşturma işlemi tarafından tetiklenir. Görüntü blobunun küçük ve orta boyutlu kopyalarını oluşturur.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

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

* [HTTP tetikleyicisi, OutputBinding kullanma](#http-trigger-using-outputbinding-java)
* [İşlev dönüş değeri kullanarak kuyruk tetikleyicisi](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP tetikleyicisi, OutputBinding (Java) kullanma

 Aşağıdaki örnek, `HttpTrigger` bir BLOB depolama kapsayıcısında bir dosyanın adını içeren bir parametre almak için ek açıklamayı kullanan bir Java işlevini gösterir. `BlobInput`Daha sonra ek açıklama dosyayı okur ve içeriğini işleve ' a geçirir `byte[]` . `BlobOutput`Ek açıklama öğesine bağlanır `OutputBinding outputItem` , bu daha sonra işlev tarafından, giriş blobunun içeriğini yapılandırılmış depolama kapsayıcısına yazmak için kullanılır.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>İşlev dönüş değeri (Java) kullanarak kuyruk tetikleyicisi

 Aşağıdaki örnek, `QueueTrigger` bir BLOB depolama kapsayıcısında bir dosyanın adını içeren bir ileti almak için ek açıklamayı kullanan bir Java işlevini gösterir. `BlobInput`Daha sonra ek açıklama dosyayı okur ve içeriğini işleve ' a geçirir `byte[]` . `BlobOutput`Ek açıklama, işlev dönüş değerine bağlanır ve ardından çalışma zamanı tarafından yapılandırılan depolama kapsayıcısına giriş blobunun içeriğini yazmak için kullanılır.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 [Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, `@BlobOutput` değeri BLOB depolama alanındaki bir nesneye yazılacak olan işlev parametrelerinde ek açıklamayı kullanın.  Parametre türü olmalıdır `OutputBinding<T>` ; burada T herhangi bir yerel Java türü veya POJO olur.

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

Aşağıdaki örnek, bir [PowerShell işlevinden](functions-reference-powershell.md)çıkış olarak bir gelen Blobun kopyasının nasıl oluşturulacağını gösterir.

İşlevin yapılandırma dosyasında (*function.js*), `trigger` meta veri özelliği, özelliklerde çıkış blob adını belirtmek için kullanılır `path` .

> [!NOTE]
> Sonsuz döngüleri önlemek için, giriş ve çıkış yollarınızın farklı olduğundan emin olun.

```json
{
  "bindings": [
    {
      "name": "myInputBlob",
      "path": "data/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in",
      "type": "blobTrigger"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "data/copy/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

PowerShell kodu aşağıda verilmiştir:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $myInputBlob, $TriggerMetadata)
Write-Host "PowerShell Blob trigger function Processed blob Name: $($TriggerMetadata.Name)"
Push-OutputBinding -Name myOutputBlob -Value $myInputBlob
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
      "name": "outputblob",
      "type": "blob",
      "dataType": "binary",
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

Python kodu aşağıda verilmiştir:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: bytes, outputblob: func.Out[bytes]):
    logging.info(f'Python Queue trigger function processed {len(inputblob)} bytes')
    outputblob.set(inputblob)
```

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md) [blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)kullanın.

Özniteliğin Oluşturucusu, `FileAccess` Aşağıdaki örnekte gösterildiği gibi, blob 'un yolunu ve okundu veya yaz belirten bir parametreyi alır:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

`Connection`Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için özelliğini ayarlayabilirsiniz:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput`Özniteliği, işlevi tetikleyen bloba erişmenizi sağlar. Özniteliği ile bir bayt dizisi kullanırsanız, `dataType` olarak ayarlayın `binary` . Ayrıntılar için [Çıkış örneğine](#example) bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Öznitelikler PowerShell tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

---

Tüm örnek için bkz. [çıkış örneği](#example).

`StorageAccount`Sınıfı, yöntemi veya parametre düzeyinde depolama hesabını belirtmek için özniteliğini kullanabilirsiniz. Daha fazla bilgi için bkz. [tetikleyici-öznitelikler](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `Blob` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok | Olarak ayarlanmalıdır `blob` . |
|**Görünüm** | yok | `out`Çıkış bağlaması için olarak ayarlanmalıdır. [Kullanım](#usage) bölümünde özel durumlar belirtilmiştir. |
|**ada** | yok | İşlev kodundaki blobu temsil eden değişkenin adı.  `$return`İşlev dönüş değerine başvuracak şekilde ayarlanır.|
|**Yolun** |**Blobpath değerini adıyla** | Blob kapsayıcısının yolu. |
|**bağlanma** |**Bağlantı**| Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, `connection` "MyStorage" olarak ayarlarsanız, işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. `connection`Boş bırakırsanız, işlevler çalışma zamanı adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır `AzureWebJobsStorage` .<br><br>Bağlantı dizesi, [yalnızca BLOB depolama hesabı](../storage/common/storage-account-overview.md#types-of-storage-accounts)değil, genel amaçlı bir depolama hesabı için olmalıdır.<br><br>[Uzantının 5. x veya daha yüksek bir sürümünü](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)kullanıyorsanız, bağlantı dizesi yerine bağlantıyı tanımlayan bir yapılandırma bölümüne başvuru sağlayabilirsiniz. Bkz. [Bağlantılar](./functions-reference.md#connections).|
|yok | **Erişim** | Okuma veya yazma yapılıp yapılmayacağını belirtir. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput`Özniteliği, işlevi tetikleyen bloba erişmenizi sağlar. Özniteliği ile bir bayt dizisi kullanırsanız, `dataType` olarak ayarlayın `binary` . Ayrıntılar için [Çıkış örneğine](#example) bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Blob verilerine, `context.bindings.<BINDING_NAME>` bağlama adının dosyada _function.js_ tanımlandığı yerde erişin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Blob verilerine, dosyanın _function.js_ , bağlamanın Name parametresiyle belirtilen adla eşleşen bir parametre aracılığıyla erişin.

# <a name="python"></a>[Python](#tab/python)

Blob depolamaya yazmak için aşağıdaki türler olarak işlev parametreleri bildirebilirsiniz:

* Farklı dizeler `func.Out[str]`
* Farklı akışlar `func.Out[func.InputStream]`

Ayrıntılar için [Çıkış örneğine](#example) bakın.

---

## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlama |  Başvuru |
|---|---|
| Blob | [Blob hata kodları](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, tablo, kuyruk |  [Depolama hatası kodları](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tablo, kuyruk |  [Sorun giderme](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Sonraki adımlar

- [BLOB depolama verileri değiştiğinde bir işlev Çalıştır](./functions-bindings-storage-blob-trigger.md)
- [Bir işlev çalıştığında BLOB depolama verilerini okuma](./functions-bindings-storage-blob-input.md)

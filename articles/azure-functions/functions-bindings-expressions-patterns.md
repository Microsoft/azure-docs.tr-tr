---
title: Azure Işlevleri bağlama ifadeleri ve desenleri
description: Ortak desenleri temel alan farklı Azure Işlevleri bağlama ifadeleri oluşturmayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 7245b0c0fb1e96959ef5dca4992cf52a38accb58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920299"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Işlevleri bağlama ifadesi desenleri

[Tetikleyiciler ve bağlamaların](./functions-triggers-bindings.md) en güçlü özelliklerinden biri *bağlama ifadeleri*. Dosyadaki *function.js* ve işlev parametreleri ve kodu ' nda, çeşitli kaynaklardaki değerlere çözüm veren ifadeleri kullanabilirsiniz.

Çoğu ifade küme ayracı içinde tanımlanır. Örneğin, bir kuyruk tetikleyicisi işlevinde, `{queueTrigger}` kuyruk ileti metnine çözülür. `path`Blob çıkış bağlamasının özelliği ise `container/{queueTrigger}` ve işlev bir kuyruk iletisi tarafından tetikleniyorsa `HelloWorld` adlı bir blob `HelloWorld` oluşturulur.

Bağlama ifadesi türleri

* [Uygulama ayarları](#binding-expressions---app-settings)
* [Tetikleyici dosya adı](#trigger-file-name)
* [Tetikleyici meta verileri](#trigger-metadata)
* [JSON yükleri](#json-payloads)
* [Yeni GUID](#create-guids)
* [Geçerli tarih ve saat](#current-time)

## <a name="binding-expressions---app-settings"></a>Bağlama ifadeleri-uygulama ayarları

En iyi uygulama olarak, parolalar ve bağlantı dizeleri yapılandırma dosyaları yerine uygulama ayarları kullanılarak yönetilmelidir. Bu, bu gizli dizi erişimini kısıtlar ve *function.js* gibi dosyaları genel kaynak denetimi depolarında depolamayı güvenli hale getirir.

Ayrıca, ortama göre yapılandırmayı değiştirmek istediğinizde uygulama ayarları da faydalıdır. Örneğin, bir test ortamında, farklı bir kuyruğu veya blob depolama kapsayıcısını izlemek isteyebilirsiniz.

Uygulama ayarı bağlama ifadeleri, diğer bağlama ifadelerinden farklı şekilde tanımlanır: küme ayraçları yerine yüzde işaretlerine paketlenir. Örneğin, blob çıkış bağlama yolu `%Environment%/newblob.txt` ve `Environment` uygulama ayarı değeri ise, `Development` kapsayıcıda bir blob oluşturulur `Development` .

Bir işlev yerel olarak çalıştığında, uygulama ayarı değerleri dosyadaki *local.settings.js* gelir.

> [!NOTE]
> `connection`Tetikleyiciler ve bağlamaların özelliği özel bir durumdur ve yüzde işaretleri olmadan değerleri otomatik olarak uygulama ayarları olarak çözümler. 

Aşağıdaki örnek, `%input_queue_name%` üzerinde tetiklenecek sırayı tanımlamak için bir uygulama ayarı kullanan bir Azure kuyruk depolama tetikleyicisinin bir örneğidir.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input_queue_name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Sınıf kitaplıklarında aynı yaklaşımı kullanabilirsiniz:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input_queue_name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Tetikleyici dosya adı

`path`BLOB tetikleyicisi için, diğer bağlamalarda ve işlev kodunda tetikleyici Blobun adına başvurmanızı sağlayan bir model olabilir. Bu düzende, bir işlev çağrısını tetikleyebilen Blobları belirten filtreleme ölçütleri de yer alabilir.

Örneğin, aşağıdaki blob tetikleyicisi bağlamasında, `path` `sample-images/{filename}` Bu, adlı bir bağlama ifadesi oluşturan, olur `filename` :

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

`filename`Daha sonra ifade, oluşturulan Blobun adını belirtmek için bir çıkış bağlamasında kullanılabilir:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

İşlev kodu, parametre adı olarak kullanılarak aynı değere erişebilir `filename` :

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Bağlama ifadeleri ve desenleri kullanma özelliği, sınıf kitaplıkları içindeki öznitelikler için geçerlidir. Aşağıdaki örnekte, öznitelik Oluşturucu parametreleri `path` yukarıdaki *function.js* örneklerle aynı değerlerdir: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Ayrıca dosya adının parçaları için ifadeler de oluşturabilirsiniz. Aşağıdaki örnekte, işlev yalnızca bir desenli eşleşen dosya adlarında tetiklenir: `anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Blob yol dizesinde ifadelerin ve desenlerin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Depolama Blobu bağlama başvurusu](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Tetikleyici meta verileri

Bir tetikleyici tarafından belirtilen veri yüküne ek olarak (bir işlevi tetikleyen kuyruk iletisinin içeriği gibi), birçok tetikleyici ek meta veri değerleri sağlar. Bu değerler, C# ve F # ' ta giriş parametresi olarak ve `context.bindings` JavaScript 'teki nesnedeki özellikler olarak kullanılabilir. 

Örneğin, bir Azure kuyruk depolama tetikleyicisi aşağıdaki özellikleri destekler:

* QueueTrigger-geçerli bir dize olursa ileti içeriği tetikleniyor
* DequeueCount
* ExpirationTime
* Id
* Insertiontime
* NextVisibleTime
* PopReceipt

Bu meta veri değerlerine dosya özelliklerindeki *function.js* erişilebilir. Örneğin, bir kuyruk tetikleyicisi kullandığınızı ve kuyruk iletisinin, okumak istediğiniz bir Blobun adını içerdiğini varsayalım. Dosyadaki *function.js* , `queueTrigger` `path` Aşağıdaki örnekte gösterildiği gibi BLOB özelliğinde meta veri özelliğini kullanabilirsiniz:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
}
```

Her tetikleyicinin meta veri özelliklerinin ayrıntıları, ilgili başvuru makalesinde açıklanmıştır. Bir örnek için bkz. [kuyruk tetikleyicisi meta verileri](functions-bindings-storage-queue-trigger.md#message-metadata). Belgeler, portalın **tümleştirme** sekmesinde, bağlama yapılandırma alanının altındaki **Belgeler** bölümünde de bulunur.  

## <a name="json-payloads"></a>JSON yükleri

Bir tetikleyici yükü JSON olduğunda, aynı işlevdeki ve işlev kodundaki diğer bağlamalar için yapılandırma içindeki özelliklerine başvurabilirsiniz.

Aşağıdaki örnek, JSON içinde bir blob adı alan bir Web kancası işlevinin dosya *function.js* gösterir: `{"BlobName":"HelloWorld.txt"}` . Blob giriş bağlaması blobu okur ve HTTP çıkış bağlaması, HTTP yanıtında blob içeriğini döndürür. Blob giriş bağlamasının, doğrudan `BlobName` özelliğe () başvurarak blob adını aldığından emin olun. `"path": "strings/{BlobName}"`

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Bunun C# ve F # dilinde çalışması için, aşağıdaki örnekte olduğu gibi, seri durumdan çıkarılacak alanları tanımlayan bir sınıfa ihtiyacınız vardır:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JavaScript 'te JSON serisini kaldırma işlemi otomatik olarak gerçekleştirilir.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Nokta gösterimi

JSON yükündeki özelliklerden bazıları özellikleri olan nesnelerdir, doğrudan nokta gösterimini kullanarak bunlara başvurabilirsiniz. Örneğin, JSON 'larınızın şöyle göründüğünü varsayalım:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Uygulamasına doğrudan başvurabilirsiniz `FileName` `BlobName.FileName` . Bu JSON biçimiyle, `path` Yukarıdaki örnekteki özelliğin şöyle görünebilecekleri aşağıda verilmiştir:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

C# ' de iki sınıfa ihtiyacınız vardır:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>GUID 'Ler oluştur

`{rand-guid}`Bağlama ifadesi BIR GUID oluşturur. Bir dosyadaki aşağıdaki blob yolu, `function.json` *50710cb5-84b9-4d87-9d83-a03d6976a682.txt* gibi bir ada sahip bir blob oluşturur.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Geçerli saat

Bağlama ifadesi `DateTime` olarak çözümlenir `DateTime.UtcNow` . Bir dosyadaki aşağıdaki blob yolu, `function.json` *2018-02-16T17-59-55Z.txt* gibi bir ada sahip bir blob oluşturur.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Çalışma zamanında bağlama

C# ve diğer .NET dillerinde, *function.jsüzerinde* ve özniteliklerde bildirim temelli bağlamaların aksine, kesinlik temelli bağlama modelini kullanabilirsiniz. Bağlama parametrelerinin tasarım zamanı yerine çalışma zamanında hesaplanması gerektiğinde, kesinlik temelli bağlama kullanışlıdır. Daha fazla bilgi edinmek için bkz. [C# Geliştirici başvurusu](functions-dotnet-class-library.md#binding-at-runtime) veya [c# betik geliştirici başvurusu](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure Işlevi dönüş değerini kullanma](./functions-bindings-return-value.md)

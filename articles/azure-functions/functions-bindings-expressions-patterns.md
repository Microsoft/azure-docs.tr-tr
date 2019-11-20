---
title: Azure Işlevleri bağlama ifadeleri ve desenleri
description: Ortak desenleri temel alan farklı Azure Işlevleri bağlama ifadeleri oluşturmayı öğrenin.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: db6f4f938b1555091dc51e310d4d31f96f93200c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097349"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Işlevleri bağlama ifadesi desenleri

[Tetikleyiciler ve bağlamaların](./functions-triggers-bindings.md) en güçlü özelliklerinden biri *bağlama ifadeleri*. *Function. JSON* dosyasında ve işlev parametreleri ve kodunda, çeşitli kaynaklardaki değerlere çözüm veren ifadeleri kullanabilirsiniz.

Çoğu ifade, küme ayraçları halinde sarmalanarak tanımlanır. Örneğin, bir kuyruk tetikleyicisi işlevinde, `{queueTrigger}` kuyruk ileti metnine çözülür. Blob çıkış bağlamasının `container/{queueTrigger}` `HelloWorld` `HelloWorld` özelliği ise ve işlev bir kuyruk iletisi tarafından tetikleniyorsa adlı bir blob oluşturulur. `path`

Bağlama ifadesi türleri

* [Uygulama ayarları](#binding-expressions---app-settings)
* [Tetikleyici dosya adı](#trigger-file-name)
* [Verileri tetikleme](#trigger-metadata)
* [JSON yükleri](#json-payloads)
* [Yeni GUID](#create-guids)
* [Geçerli tarih ve saat](#current-time)

## <a name="binding-expressions---app-settings"></a>Bağlama ifadeleri-uygulama ayarları

En iyi uygulama olarak, parolalar ve bağlantı dizeleri yapılandırma dosyaları yerine uygulama ayarları kullanılarak yönetilmelidir. Bu, bu gizli dizi erişimini kısıtlar ve Public kaynak denetimi depolarında *function. JSON* gibi dosyaları depolamayı güvenli hale getirir.

Ayrıca, ortama göre yapılandırmayı değiştirmek istediğinizde uygulama ayarları da faydalıdır. Örneğin, bir test ortamında, farklı bir kuyruğu veya blob depolama kapsayıcısını izlemek isteyebilirsiniz.

Uygulama ayarı bağlama ifadeleri, diğer bağlama ifadelerinden farklı şekilde tanımlanır: küme ayraçları yerine yüzde işaretlerine paketlenir. Örneğin, `%Environment%/newblob.txt` blob çıkış bağlama yolu `Environment` ve uygulama ayarı değeri ise `Development`, `Development` kapsayıcıda bir blob oluşturulur.

Bir işlev yerel olarak çalıştığında, uygulama ayarı değerleri *yerel. Settings. JSON* dosyasından gelir.

Tetikleyiciler ve bağlamaların özelliğinin özel bir durum olduğunu ve değerleri yüzde işaretleri olmadan uygulama ayarları olarak otomatik olarak çözümleyeceğini unutmayın. `connection` 

Aşağıdaki örnek, üzerinde tetiklenecek sırayı tanımlamak için bir uygulama ayarı `%input-queue-name%` kullanan bir Azure kuyruk depolama tetikleyicisinin bir örneğidir.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Sınıf kitaplıklarında aynı yaklaşımı kullanabilirsiniz:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Tetikleyici dosya adı

Blob `path` tetikleyicisi için, diğer bağlamalarda ve işlev kodunda tetikleyici Blobun adına başvurmanızı sağlayan bir model olabilir. Bu düzende, bir işlev çağrısını tetikleyebilen Blobları belirten filtreleme ölçütleri de yer alabilir.

Örneğin, aşağıdaki blob tetikleyicisi bağlamasında `path` , bu, adlı `filename`bir bağlama ifadesi `sample-images/{filename}`oluşturan, olur:

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

Daha sonra `filename` ifade, oluşturulan Blobun adını belirtmek için bir çıkış bağlamasında kullanılabilir:

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

İşlev kodu, parametre adı olarak kullanılarak `filename` aynı değere erişebilir:

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

Bağlama ifadeleri ve desenleri kullanma özelliği, sınıf kitaplıkları içindeki öznitelikler için geçerlidir. Aşağıdaki örnekte, öznitelik Oluşturucu parametreleri önceki işlevle aynı `path` değerlerdir *. JSON* örnekleri: 

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

Uzantı gibi dosya adının parçaları için de ifadeler oluşturabilirsiniz. Blob yol dizesinde ifadelerin ve desenlerin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Depolama Blobu bağlama başvurusu](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Verileri tetikleme

Bir tetikleyici tarafından belirtilen veri yüküne ek olarak (bir işlevi tetikleyen kuyruk iletisinin içeriği gibi), birçok tetikleyici ek meta veri değerleri sağlar. Bu değerler, içinde C# giriş parametreleri olarak ve F# veya JavaScript içindeki `context.bindings` nesne üzerinde özellikler olarak kullanılabilir. 

Örneğin, bir Azure kuyruk depolama tetikleyicisi aşağıdaki özellikleri destekler:

* QueueTrigger-geçerli bir dize olursa ileti içeriği tetikleniyor
* DequeueCount
* ExpirationTime
* Id
* Insertiontime
* NextVisibleTime
* PopReceipt

Bu meta veri değerlerine *function. JSON* dosya özelliklerinden erişilebilir. Örneğin, bir kuyruk tetikleyicisi kullandığınızı ve kuyruk iletisinin, okumak istediğiniz bir Blobun adını içerdiğini varsayalım. *Function. JSON* dosyasında, aşağıdaki örnekte gösterildiği gibi BLOB `queueTrigger` `path` özelliğinde meta veri özelliğini kullanabilirsiniz:

```json
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
```

Her tetikleyicinin meta veri özelliklerinin ayrıntıları, ilgili başvuru makalesinde açıklanmıştır. Bir örnek için bkz. [kuyruk tetikleyicisi meta verileri](functions-bindings-storage-queue.md#trigger---message-metadata). Belgeler, portalın **tümleştirme** sekmesinde, bağlama yapılandırma alanının altındaki **Belgeler** bölümünde de bulunur.  

## <a name="json-payloads"></a>JSON yükleri

Bir tetikleyici yükü JSON olduğunda, aynı işlevdeki ve işlev kodundaki diğer bağlamalar için yapılandırma içindeki özelliklerine başvurabilirsiniz.

Aşağıdaki örnek, JSON içinde bir blob adı alan bir Web kancası işlevi için *function. JSON* dosyasını gösterir: `{"BlobName":"HelloWorld.txt"}`. Blob giriş bağlaması blobu okur ve HTTP çıkış bağlaması, HTTP yanıtında blob içeriğini döndürür. Blob giriş bağlamasının, doğrudan `BlobName` özelliğe (`"path": "strings/{BlobName}"`) başvurarak blob adını aldığından emin olun.

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

Bu ve C# F#' de çalışması için, aşağıdaki örnekte olduğu gibi, seri durumdan çıkarılacak alanları tanımlayan bir sınıfa ihtiyacınız vardır:

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

`FileName` Uygulamasına`BlobName.FileName`doğrudan başvurabilirsiniz. Bu JSON biçimiyle, yukarıdaki örnekteki `path` özelliğin şöyle görünebilecekleri aşağıda verilmiştir:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

' C#De, iki sınıfa ihtiyacınız vardır:

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

`{rand-guid}` Bağlama ifadesi bir GUID oluşturur. Bir `function.json` dosyadaki aşağıdaki blob yolu, *50710cb5-84b9-4d87-9d83-a03d6976a682. txt*gibi bir ada sahip bir blob oluşturur.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Geçerli zaman

Bağlama ifadesi `DateTime` olarak `DateTime.UtcNow`çözümlenir. Bir `function.json` dosyadaki aşağıdaki blob yolu, *2018-02-16T17-59 -55z. txt*gibi bir ada sahip bir blob oluşturur.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Çalışma zamanında bağlama

C# Ve diğer .NET dillerinde, *function. JSON* ve Attributes içindeki bildirime dayalı bağlamaların aksine, bir zorunlu bağlama kalıbı kullanabilirsiniz. Bağlama parametrelerinin tasarım zamanı yerine çalışma zamanında hesaplanması gerektiğinde, kesinlik temelli bağlama kullanışlıdır. Daha fazla bilgi edinmek için bkz. [ C# geliştirici başvurusu](functions-dotnet-class-library.md#binding-at-runtime) veya [ C# betik geliştirici başvurusu](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure Işlevi dönüş değerini kullanma](./functions-bindings-return-value.md)

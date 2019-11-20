---
title: Azure Işlevleri için Azure Blob depolama bağlamaları
description: Azure Işlevleri 'nde Azure Blob depolama Tetikleyicileri ve bağlamalarını nasıl kullanacağınızı anlayın.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure işlevleri, işlevler, olay işleme, dinamik işlem, sunucusuz mimari
ms.service: azure-functions
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: b565a48b56162d19a07f0f54bfe780b7dda04b96
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177395"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Işlevleri için Azure Blob depolama bağlamaları

Bu makalede Azure Işlevleri 'nde Azure Blob depolama bağlamalarıyla nasıl çalışılacağı açıklanmaktadır. Azure Işlevleri, Bloblar için tetikleyici, giriş ve çıkış bağlamalarını destekler. Makale her bağlama için bir bölüm içerir:

* [Blob tetikleyicisi](#trigger)
* [Blob girişi bağlama](#input)
* [Blob çıkış bağlama](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Yalnızca BLOB depolama alanı için BLOB depolama tetikleyicisi yerine Event Grid tetikleyiciyi kullanın, yüksek ölçekli için veya gecikmeyi azaltın. Daha fazla bilgi için [tetikleyici](#trigger) bölümüne bakın.

## <a name="packages---functions-1x"></a>Paketler-Işlevler 1. x

BLOB depolama bağlamaları [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet paketi, sürüm 2. x içinde sağlanır. Paketin kaynak kodu, [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub deposundadır.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Paketler-Işlevler 2. x

BLOB depolama bağlamaları [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet paketi, sürüm 3. x içinde sağlanır. Paketin kaynak kodu, [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub deposundadır.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Tetikleyici

BLOB depolama tetikleyicisi, yeni veya güncelleştirilmiş bir blob algılandığında bir işlevi başlatır. Blob içerikleri, işleve giriş olarak sağlanır.

[Event Grid tetikleyicisi](functions-bindings-event-grid.md) [BLOB olayları](../storage/blobs/storage-blob-event-overview.md) için yerleşik desteğe sahiptir ve yeni veya güncelleştirilmiş bir blob algılandığında bir işlevi başlatmak için de kullanılabilir. Bir örnek için [Event Grid öğreticisiyle görüntü yeniden boyutlandırma](../event-grid/resize-images-on-storage-blob-upload-event.md) bölümüne bakın.

Aşağıdaki senaryolar için BLOB depolama tetikleyicisi yerine Event Grid kullanın:

* Blob depolama hesapları
* Yüksek ölçekli
* En aza indirme gecikmesi

### <a name="blob-storage-accounts"></a>Blob depolama hesapları

Blob [depolama hesapları](../storage/common/storage-account-overview.md#types-of-storage-accounts) blob girdileri ve çıkış bağlamaları için desteklenir, ancak blob Tetikleyicileri için desteklenmez. BLOB depolama Tetikleyicileri, genel amaçlı bir depolama hesabı gerektirir.

### <a name="high-scale"></a>Yüksek ölçekli

Yüksek ölçek, bir saniyede 100.000 ' den fazla bloba sahip kapsayıcılar veya saniyede 100 ' ten fazla BLOB güncelleştirmesi olan depolama hesapları ile gevşek olarak tanımlanabilir.

### <a name="latency-issues"></a>Gecikme sorunları

İşlev uygulamanız tüketim planalıyorsa, bir işlev uygulaması boşta kalırsa, yeni Blobları işlemede 10 dakikalık bir gecikme olabilir. Bu gecikmeyi önlemek için, her zaman etkin olan bir App Service planına geçebilirsiniz. BLOB depolama hesabınızla bir [Event Grid tetikleyicisi](functions-bindings-event-grid.md) de kullanabilirsiniz. Bir örnek için [Event Grid öğreticisine](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)bakın. 

### <a name="queue-storage-trigger"></a>Kuyruk depolama tetikleyicisi

Event Grid yanı sıra, Blobların işlenmesine yönelik başka bir alternatif de kuyruk depolama tetikleyicisi olur, ancak blob olayları için yerleşik bir desteği yoktur. Blob 'lar oluştururken veya güncelleştirirken kuyruk mesajları oluşturmanız gerekir. Bunu yaptığınızı varsayan bir örnek için, [Bu makalenin sonraki kısımlarında yer alan blob giriş bağlama örneğine](#input---example)bakın.

## <a name="trigger---example"></a>Tetikleyici-örnek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aşağıdaki örnek, `samples-workitems` kapsayıcısında bir blob eklendiğinde veya güncelleştirilirse bir günlüğü yazan bir [ C# işlevi](functions-dotnet-class-library.md) gösterir.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Blob tetikleyici yolundaki `{name}` dizesi `samples-workitems/{name}`, tetikleyici Blobun dosya adına erişmek için işlev kodunda kullanabileceğiniz bir [bağlama ifadesi](./functions-bindings-expressions-patterns.md) oluşturur. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [BLOB adı desenleri](#trigger---blob-name-patterns) bölümüne bakın.

@No__t-0 özniteliği hakkında daha fazla bilgi için bkz. [tetikleyici-öznitelikler](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

Aşağıdaki örnek, bir *function. JSON* dosyası ve bağlamayı kullanan kod içindeki bir blob tetikleyicisi bağlamasını gösterir. @No__t-0 [kapsayıcısında](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)blob eklendiğinde veya güncelleştirilirse, işlevi bir günlük yazar.

Bu, *function. JSON* dosyasındaki bağlama verileri:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Blob tetikleyici yolundaki `{name}` dizesi `samples-workitems/{name}`, tetikleyici Blobun dosya adına erişmek için işlev kodunda kullanabileceğiniz bir [bağlama ifadesi](./functions-bindings-expressions-patterns.md) oluşturur. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [BLOB adı desenleri](#trigger---blob-name-patterns) bölümüne bakın.

*Function. JSON* dosya özellikleri hakkında daha fazla bilgi için bkz. [yapılandırma](#trigger---configuration) bölümüne bu özellikler açıklanmaktadır.

@No__t- C# 1 ' e bağlanan betik kodu:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

@No__t- C# 1 ' e bağlanan betik kodu:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function. JSON* dosyası ve bağlamayı kullanan [JavaScript kodu](functions-reference-node.md) içindeki bir blob tetikleyicisi bağlamasını gösterir. @No__t-0 kapsayıcısında blob eklendiğinde veya güncelleştirilirse, işlevi bir günlük yazar.

İşte *function. JSON* dosyası:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Blob tetikleyici yolundaki `{name}` dizesi `samples-workitems/{name}`, tetikleyici Blobun dosya adına erişmek için işlev kodunda kullanabileceğiniz bir [bağlama ifadesi](./functions-bindings-expressions-patterns.md) oluşturur. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [BLOB adı desenleri](#trigger---blob-name-patterns) bölümüne bakın.

*Function. JSON* dosya özellikleri hakkında daha fazla bilgi için bkz. [yapılandırma](#trigger---configuration) bölümüne bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Aşağıdaki örnek, bir *function. JSON* dosyası ve bağlamayı kullanan [Python kodu](functions-reference-python.md) içindeki bir blob tetikleyicisi bağlamasını gösterir. @No__t-0 [kapsayıcısında](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)blob eklendiğinde veya güncelleştirilirse, işlevi bir günlük yazar.

İşte *function. JSON* dosyası:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Blob tetikleyici yolundaki `{name}` dizesi `samples-workitems/{name}`, tetikleyici Blobun dosya adına erişmek için işlev kodunda kullanabileceğiniz bir [bağlama ifadesi](./functions-bindings-expressions-patterns.md) oluşturur. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [BLOB adı desenleri](#trigger---blob-name-patterns) bölümüne bakın.

*Function. JSON* dosya özellikleri hakkında daha fazla bilgi için bkz. [yapılandırma](#trigger---configuration) bölümüne bu özellikler açıklanmaktadır.

Python kodu aşağıda verilmiştir:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Aşağıdaki örnek, bir *function. JSON* dosyası ve bağlamayı kullanan [Java kodunda](functions-reference-java.md) bir blob tetikleyicisi bağlamasını gösterir. @No__t-0 kapsayıcısında blob eklendiğinde veya güncelleştirilirse, işlevi bir günlük yazar.

İşte *function. JSON* dosyası:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Java kodu aşağıda verilmiştir:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="trigger---attributes"></a>Tetikleyici-öznitelikler

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda, bir blob tetikleyicisi yapılandırmak için aşağıdaki öznitelikleri kullanın:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Özniteliğin Oluşturucusu, izlenecek kapsayıcıyı ve isteğe bağlı olarak bir [BLOB adı modelini](#trigger---blob-name-patterns)gösteren bir yol dizesi alır. Bir örneği aşağıda verilmiştir:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için `Connection` özelliğini ayarlayabilirsiniz:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Tüm örnek için bkz. [tetikleyici örneği](#trigger---example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Kullanılacak depolama hesabını belirtmek için başka bir yol sağlar. Oluşturucu, depolama bağlantı dizesi içeren bir uygulama ayarının adını alır. Özniteliği parametresi, yöntemi veya sınıf düzeyinde uygulanabilir. Aşağıdaki örnek, sınıf düzeyi ve Yöntem düzeyini gösterir:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Kullanılacak depolama hesabı aşağıdaki sırayla belirlenir:

* @No__t-0 özniteliğinin `Connection` özelliği.
* @No__t-0 özniteliği, `BlobTrigger` özniteliğiyle aynı parametreye uygulandı.
* İşleve uygulanan `StorageAccount` özniteliği.
* Sınıfına uygulanan `StorageAccount` özniteliği.
* İşlev uygulaması için varsayılan depolama hesabı ("AzureWebJobsStorage" uygulama ayarı).

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

Öznitelikler komut dosyası tarafından C# desteklenmiyor.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="pythontabpython"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

# <a name="javatabjava"></a>[Java](#tab/java)

@No__t-0 özniteliği, işlevi tetikleyen bloba erişim sağlamak için kullanılır. Ayrıntılar için [tetikleyici örneğine](#trigger---example) bakın.

---

## <a name="trigger---configuration"></a>Tetikleyici-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `BlobTrigger` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | Yok | @No__t-0 olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**direction** | Yok | @No__t-0 olarak ayarlanmalıdır. Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. [Kullanım](#trigger---usage) bölümünde özel durumlar belirtilmiştir. |
|**ada** | Yok | İşlev kodundaki blobu temsil eden değişkenin adı. |
|**Yolun** | **Blobpath değerini adıyla** |İzlenecek [kapsayıcı](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) .  Bir [BLOB adı stili](#trigger---blob-name-patterns)olabilir. |
|**bağlanma** | **Bağlanma** | Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, `connection` ' ı "MyStorage" olarak ayarlarsanız, Işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. @No__t-0 boş bırakırsanız, Işlevler çalışma zamanı `AzureWebJobsStorage` adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır.<br><br>Bağlantı dizesi, [BLOB depolama hesabı](../storage/common/storage-account-overview.md#types-of-storage-accounts)değil, genel amaçlı bir depolama hesabı için olmalıdır.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Tetikleyici-kullanım

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

@No__t-0 kullanarak blob verilerine erişin.

# <a name="pythontabpython"></a>[Python](#tab/python)

Blob verilerine [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)olarak yazılan parametre aracılığıyla erişin. Ayrıntılar için [tetikleyici örneğine](#trigger---example) bakın.

# <a name="javatabjava"></a>[Java](#tab/java)

@No__t-0 özniteliği, işlevi tetikleyen bloba erişim sağlamak için kullanılır. Ayrıntılar için [tetikleyici örneğine](#trigger---example) bakın.

---

## <a name="trigger---blob-name-patterns"></a>Tetikleyici-blob adı desenleri

Bir blob ad modelini *function. JSON* içindeki `path` özelliğinde veya `BlobTrigger` öznitelik oluşturucusunda belirtebilirsiniz. Ad stili bir [filtre veya bağlama ifadesi](./functions-bindings-expressions-patterns.md)olabilir. Aşağıdaki bölümlerde örnekler sağlanmaktadır.

### <a name="get-file-name-and-extension"></a>Dosya adı ve uzantısını al

Aşağıdaki örnek, blob dosya adına ve uzantısına ayrı olarak nasıl bağlanılacağını gösterir:

```json
"path": "input/{blobname}.{blobextension}",
```

Blob *Original-Blob1. txt*olarak adlandırılmışsa, işlev kodundaki `blobname` ve `blobextension` değişkenlerinin değerleri *özgün-Blob1* ve *txt*' dir.

### <a name="filter-on-blob-name"></a>Blob adını filtrele

Aşağıdaki örnek yalnızca, "özgün-" dizesiyle başlayan `input` kapsayıcısındaki bloblarda tetiklenir:

```json
"path": "input/original-{name}",
```

Blob adı *Original-Blob1. txt*ise, işlev kodundaki `name` değişkeninin değeri `Blob1` ' dir.

### <a name="filter-on-file-type"></a>Dosya türünü filtrele

Aşağıdaki örnek yalnızca *. png* dosyalarında tetiklenir:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Dosya adlarında küme ayraçları üzerine filtrele

Dosya adlarında küme ayraçları aramak için, iki küme ayracı kullanarak küme ayracını kaçış. Aşağıdaki örnek, adında küme ayracı olan Bloblar için filtre uygular:

```json
"path": "images/{{20140101}}-{name}",
```

Blob *@no__t -1-soundfile. mp3*olarak adlandırılmışsa, işlev kodundaki `name` değişken değeri *soundfile. mp3*olur.

## <a name="trigger---metadata"></a>Tetikleyici-meta veriler

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Meta veriler Python 'da kullanılamıyor.

# <a name="javatabjava"></a>[Java](#tab/java)

Java 'da meta veriler kullanılamıyor.

---

## <a name="trigger---blob-receipts"></a>Tetikleyici-blob alındıları

Azure Işlevleri çalışma zamanı, aynı yeni veya güncelleştirilmiş blob için bir blob tetikleyici işlevinin birden çok kez çağrılmamasını sağlar. Belirli bir blob sürümünün işlenip işlenmeyeceğini anlamak için, *BLOB alındıları*saklar.

Azure Işlevleri, blob alındıları, işlev uygulamanız için Azure depolama hesabındaki Azure *-WebJobs-Konakları* adlı bir kapsayıcıda depolar (`AzureWebJobsStorage` uygulama ayarı tarafından tanımlanır). Blob alındı bilgisi aşağıdaki bilgilere sahiptir:

* Tetiklenen işlev (" *&lt;işlevi uygulama adı >* . Lerdir. *&lt;function adı >* ", örneğin:" MyFunctionApp. Functions. copyblob ")
* Kapsayıcı adı
* Blob türü ("BlockBlob" veya "PageBlob")
* Blob adı
* ETag (bir blob sürüm tanımlayıcısı, örneğin: "0x8D1DC6E70A277EF")

Bir Blobun yeniden işlenmesini zorlamak için, bu Blobun blob alındığını *Azure-WebJobs-hosts* kapsayıcısından el ile silin. Yeniden işleme hemen gerçekleşmeyebilir, daha sonraki bir noktada gerçekleşmesi garanti edilir.

## <a name="trigger---poison-blobs"></a>Tetikleyici-zarar Blobları

Belirli bir blob için bir blob tetikleme işlevi başarısız olduğunda, Azure Işlevleri varsayılan olarak toplam 5 kez çalışır.

5 ' in tamamı başarısız olursa, Azure Işlevleri *WebJobs-blobtrigger-zeadlı*bir depolama kuyruğuna bir ileti ekler. Zarar Blobları için kuyruk iletisi aşağıdaki özellikleri içeren bir JSON nesnesidir:

* FunctionID ( *&lt;function uygulama adı >* biçimindedir. Lerdir. *&lt;function ad >* )
* BlobType ("BlockBlob" veya "PageBlob")
* ContainerName
* BlobName
* ETag (bir blob sürüm tanımlayıcısı, örneğin: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Tetikleyici-eşzamanlılık ve bellek kullanımı

Blob tetikleyicisi bir kuyruğu dahili olarak kullanır, bu nedenle maksimum eşzamanlı işlev çağırma sayısı, [Host. JSON içindeki kuyruklar yapılandırması](functions-host-json.md#queues)tarafından denetlenir. Varsayılan ayarlar eşzamanlılık, 24 çağırma ile sınırlıdır. Bu sınır, blob tetikleyicisi kullanan her bir işleve ayrı olarak uygulanır.

[Tüketim planı](functions-scale.md#how-the-consumption-and-premium-plans-work) bir sanal MAKINEDE (VM) bir işlev UYGULAMASıNı 1,5 GB bellek ile sınırlandırır. Bellek, her eşzamanlı çalıştırılan işlev örneği ve Işlevler çalışma zamanının kendisi tarafından kullanılır. Blob ile tetiklenen bir işlev tüm blobu belleğe yüklerse, bu işlev tarafından yalnızca blob 'lar için kullanılan en fazla bellek 24 * en yüksek blob boyutudur. Örneğin, üç blob ile tetiklenen işlevlere sahip bir işlev uygulaması ve varsayılan ayarlar en fazla VM başına eşzamanlılık 3 * 24 = 72 işlev etkinleştirmeleri olacaktır.

JavaScript ve Java işlevleri, tüm Blobun belleği belleğe yükler ve C# `string`, `Byte[]` veya poco 'ye bağlarsanız bu işlemleri yapar.

## <a name="trigger---polling"></a>Tetikleyici-yoklama

İzlenen blob kapsayıcısı 10.000 'den fazla BLOB (tüm kapsayıcılarda) içeriyorsa, Işlevler çalışma zamanı yeni veya değiştirilmiş blob 'ları izlemek için günlük dosyalarını tarar. Bu işlem gecikmelere neden olabilir. Bir işlev, blob oluşturulduktan sonra birkaç dakika veya daha uzun bir süre boyunca tetiklenmeyebilir.

> [!WARNING]
> Ayrıca, [depolama günlükleri "en iyi çaba" temelinde oluşturulur](/rest/api/storageservices/About-Storage-Analytics-Logging) . Tüm olayların yakalandığı garanti yoktur. Bazı koşullarda Günlükler eksik olabilir.
> 
> Daha hızlı veya daha güvenilir bir blob işleme ihtiyacınız varsa, blobu oluştururken bir [kuyruk iletisi](../storage/queues/storage-dotnet-how-to-use-queues.md) oluşturmayı düşünün. Sonra blobu işlemek için blob tetikleyicisi yerine bir [kuyruk tetikleyicisi](functions-bindings-storage-queue.md) kullanın. Başka bir seçenek Event Grid kullanmaktır; [Event Grid kullanarak karşıya yüklenen görüntüleri yeniden boyutlandırmayı otomatikleştirme](../event-grid/resize-images-on-storage-blob-upload-event.md)öğreticisine bakın.
>

## <a name="input"></a>Girdi

Blob 'ları okumak için bir BLOB depolama girişi bağlama kullanın.

## <a name="input---example"></a>Giriş-örnek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aşağıdaki örnek, bir kuyruk tetikleyicisi ve bir giriş blobu bağlama kullanan bir [ C# işlevdir](functions-dotnet-class-library.md) . Kuyruk iletisi Blobun adını içerir ve işlev Blobun boyutunu günlüğe kaydeder.

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

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

<!--Same example for input and output. -->

Aşağıdaki örnek, bağlamaları kullanan bir *function. JSON* dosyası ve [ C# betik (. CSX)](functions-reference-csharp.md) kodunda blob giriş ve çıkış bağlamalarını gösterir. İşlevi, bir metin blobunun kopyasını oluşturur. İşlev, kopyalanacak Blobun adını içeren bir kuyruk iletisi tarafından tetiklenir. Yeni blob *{originalblobname}-Copy*olarak adlandırılmıştır.

*Function. JSON* dosyasında, `path` özelliklerinde blob adını belirtmek için `queueTrigger` meta veri özelliği kullanılır:

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

[Yapılandırma](#input---configuration) bölümünde bu özellikler açıklanmaktadır.

C# Betik kodu aşağıda verilmiştir:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Aşağıdaki örnek, blob giriş ve çıkış bağlamalarını bir *function. JSON* dosyası ve bağlamaları kullanan [JavaScript kodu](functions-reference-node.md) gösterir. İşlevi bir Blobun kopyasını oluşturur. İşlev, kopyalanacak Blobun adını içeren bir kuyruk iletisi tarafından tetiklenir. Yeni blob *{originalblobname}-Copy*olarak adlandırılmıştır.

*Function. JSON* dosyasında, `path` özelliklerinde blob adını belirtmek için `queueTrigger` meta veri özelliği kullanılır:

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

[Yapılandırma](#input---configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Aşağıdaki örnekte, bir *function. JSON* dosyası ve bağlamaları kullanan [Python kodu](functions-reference-python.md) içindeki blob girişi ve çıkış bağlamaları gösterilmektedir. İşlevi bir Blobun kopyasını oluşturur. İşlev, kopyalanacak Blobun adını içeren bir kuyruk iletisi tarafından tetiklenir. Yeni blob *{originalblobname}-Copy*olarak adlandırılmıştır.

*Function. JSON* dosyasında, `path` özelliklerinde blob adını belirtmek için `queueTrigger` meta veri özelliği kullanılır:

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

[Yapılandırma](#input---configuration) bölümünde bu özellikler açıklanmaktadır.

Python kodu aşağıda verilmiştir:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

Bu bölüm aşağıdaki örnekleri içerir:

* [HTTP tetikleyicisi, sorgu dizesinden blob adı ara](#http-trigger-look-up-blob-name-from-query-string)
* [Kuyruk tetikleyicisi, kuyruk iletisinden blob adı Al](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP tetikleyicisi, sorgu dizesinden blob adı ara

 Aşağıdaki örnek, bir BLOB depolama kapsayıcısında bir dosyanın adını içeren bir parametre almak için `HttpTrigger` ek açıklamasını kullanan bir Java işlevini gösterir. @No__t-0 ek açıklaması, dosyayı okur ve içeriğini işleve `byte[]` olarak geçirir.

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

 Aşağıdaki örnek, bir BLOB depolama kapsayıcısında bir dosyanın adını içeren bir ileti almak için `QueueTrigger` ek açıklamasını kullanan bir Java işlevini gösterir. @No__t-0 ek açıklaması, dosyayı okur ve içeriğini işleve `byte[]` olarak geçirir.

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

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, değeri bir Blobun geldiği parametrelerde `@BlobInput` ek açıklamasını kullanın.  Bu ek açıklama `Optional<T>` kullanılarak yerel Java türleri, POJOs veya null atanabilir değerlerle kullanılabilir.

---

## <a name="input---attributes"></a>Giriş öznitelikleri

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)kullanın.

Özniteliğin Oluşturucusu, aşağıdaki örnekte gösterildiği gibi, blob yolunu ve okuma veya yazma belirten bir `FileAccess` parametresini alır:

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

Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için `Connection` özelliğini ayarlayabilirsiniz:

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

Sınıf, yöntem veya parametre düzeyinde depolama hesabını belirtmek için `StorageAccount` özniteliğini kullanabilirsiniz. Daha fazla bilgi için bkz. [tetikleyici-öznitelikler](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

Öznitelikler komut dosyası tarafından C# desteklenmiyor.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="pythontabpython"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

# <a name="javatabjava"></a>[Java](#tab/java)

@No__t-0 özniteliği, işlevi tetikleyen bloba erişmenizi sağlar. Özniteliği ile bir bayt dizisi kullanıyorsanız, `dataType` ' ı `binary` olarak ayarlayın. Ayrıntılar için [giriş örneğine](#input---example) bakın.

---

## <a name="input---configuration"></a>Giriş-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `Blob` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | Yok | @No__t-0 olarak ayarlanmalıdır. |
|**direction** | Yok | @No__t-0 olarak ayarlanmalıdır. [Kullanım](#input---usage) bölümünde özel durumlar belirtilmiştir. |
|**ada** | Yok | İşlev kodundaki blobu temsil eden değişkenin adı.|
|**Yolun** |**Blobpath değerini adıyla** | Blobun yolu. |
|**bağlanma** |**Bağlanma**| Bu bağlama için kullanılacak [depolama bağlantı dizesini](../storage/common/storage-configure-connection-string.md) içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, `connection` ' ı "MyStorage" olarak ayarlarsanız, Işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. @No__t-0 boş bırakırsanız, Işlevler çalışma zamanı `AzureWebJobsStorage` adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır.<br><br>Bağlantı dizesi, [yalnızca BLOB depolama hesabı](../storage/common/storage-account-overview.md#types-of-storage-accounts)değil, genel amaçlı bir depolama hesabı için olmalıdır.|
|Yok | **Erişim** | Okuma veya yazma yapılıp yapılmayacağını belirtir. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Giriş kullanımı

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Blob verilerine `context.bindings.<name from function.json>` kullanarak erişin.

# <a name="pythontabpython"></a>[Python](#tab/python)

Blob verilerine [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)olarak yazılan parametre aracılığıyla erişin. Ayrıntılar için [giriş örneğine](#input---example) bakın.

# <a name="javatabjava"></a>[Java](#tab/java)

@No__t-0 özniteliği, işlevi tetikleyen bloba erişmenizi sağlar. Özniteliği ile bir bayt dizisi kullanıyorsanız, `dataType` ' ı `binary` olarak ayarlayın. Ayrıntılar için [giriş örneğine](#input---example) bakın.

---

## <a name="output"></a>Çıktı

Blob 'ları yazmak için BLOB depolama çıkış bağlamalarını kullanın.

## <a name="output---example"></a>Çıkış-örnek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aşağıdaki örnek bir blob tetikleyicisi ve iki çıkış blobu bağlaması kullanan bir [ C# işlevdir](functions-dotnet-class-library.md) . İşlev, *örnek görüntüler* kapsayıcısında bir görüntü blobu oluşturma işlemi tarafından tetiklenir. Görüntü blobunun küçük ve orta boyutlu kopyalarını oluşturur.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    IImageFormat format;

    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageSmall, ImageSize.Small, format);
    }

    image.Position = 0;
    using (Image<Rgba32> input = Image.Load(image, out format))
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
```

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

<!--Same example for input and output. -->

Aşağıdaki örnek, bağlamaları kullanan bir *function. JSON* dosyası ve [ C# betik (. CSX)](functions-reference-csharp.md) kodunda blob giriş ve çıkış bağlamalarını gösterir. İşlevi, bir metin blobunun kopyasını oluşturur. İşlev, kopyalanacak Blobun adını içeren bir kuyruk iletisi tarafından tetiklenir. Yeni blob *{originalblobname}-Copy*olarak adlandırılmıştır.

*Function. JSON* dosyasında, `path` özelliklerinde blob adını belirtmek için `queueTrigger` meta veri özelliği kullanılır:

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

[Yapılandırma](#output---configuration) bölümünde bu özellikler açıklanmaktadır.

C# Betik kodu aşağıda verilmiştir:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Aşağıdaki örnek, blob giriş ve çıkış bağlamalarını bir *function. JSON* dosyası ve bağlamaları kullanan [JavaScript kodu](functions-reference-node.md) gösterir. İşlevi bir Blobun kopyasını oluşturur. İşlev, kopyalanacak Blobun adını içeren bir kuyruk iletisi tarafından tetiklenir. Yeni blob *{originalblobname}-Copy*olarak adlandırılmıştır.

*Function. JSON* dosyasında, `path` özelliklerinde blob adını belirtmek için `queueTrigger` meta veri özelliği kullanılır:

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

[Yapılandırma](#output---configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Aşağıdaki örnekte, bir *function. JSON* dosyası ve bağlamaları kullanan [Python kodu](functions-reference-python.md) içindeki blob girişi ve çıkış bağlamaları gösterilmektedir. İşlevi bir Blobun kopyasını oluşturur. İşlev, kopyalanacak Blobun adını içeren bir kuyruk iletisi tarafından tetiklenir. Yeni blob *{originalblobname}-Copy*olarak adlandırılmıştır.

*Function. JSON* dosyasında, `path` özelliklerinde blob adını belirtmek için `queueTrigger` meta veri özelliği kullanılır:

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
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
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

[Yapılandırma](#output---configuration) bölümünde bu özellikler açıklanmaktadır.

Python kodu aşağıda verilmiştir:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Bu bölüm aşağıdaki örnekleri içerir:

* [HTTP tetikleyicisi, OutputBinding kullanma](#http-trigger-using-outputbinding-java)
* [İşlev dönüş değeri kullanarak kuyruk tetikleyicisi](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP tetikleyicisi, OutputBinding (Java) kullanma

 Aşağıdaki örnek, bir BLOB depolama kapsayıcısında bir dosyanın adını içeren bir parametre almak için `HttpTrigger` ek açıklamasını kullanan bir Java işlevini gösterir. @No__t-0 ek açıklaması, dosyayı okur ve içeriğini işleve `byte[]` olarak geçirir. @No__t-0 ek açıklaması `OutputBinding outputItem` ' e bağlanır ve ardından işlev tarafından yapılandırılan depolama kapsayıcısına giriş blobunun içeriğini yazmak için kullanılır.

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

 Aşağıdaki örnek, bir BLOB depolama kapsayıcısında bir dosyanın adını içeren bir ileti almak için `QueueTrigger` ek açıklamasını kullanan bir Java işlevini gösterir. @No__t-0 ek açıklaması, dosyayı okur ve içeriğini işleve `byte[]` olarak geçirir. @No__t-0 ek açıklaması, işlev dönüş değerine bağlanır ve ardından çalışma zamanı tarafından yapılandırılan depolama kapsayıcısına giriş blobunun içeriğini yazmak için kullanılır.

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

 [Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, değeri BLOB depolama alanındaki bir nesneye yazılacak işlev parametrelerinde `@BlobOutput` ek açıklamasını kullanın.  Parametre türü `OutputBinding<T>` olmalıdır; burada T herhangi bir yerel Java türü veya bir POJO olur.

---

## <a name="output---attributes"></a>Çıkış-öznitelikler

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)kullanın.

Özniteliğin Oluşturucusu, aşağıdaki örnekte gösterildiği gibi, blob yolunu ve okuma veya yazma belirten bir `FileAccess` parametresini alır:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için `Connection` özelliğini ayarlayabilirsiniz:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

Öznitelikler komut dosyası tarafından C# desteklenmiyor.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="pythontabpython"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

# <a name="javatabjava"></a>[Java](#tab/java)

@No__t-0 özniteliği, işlevi tetikleyen bloba erişmenizi sağlar. Özniteliği ile bir bayt dizisi kullanıyorsanız, `dataType` ' ı `binary` olarak ayarlayın. Ayrıntılar için [Çıkış örneğine](#output---example) bakın.

---

Tüm örnek için bkz. [çıkış örneği](#output---example).

Sınıf, yöntem veya parametre düzeyinde depolama hesabını belirtmek için `StorageAccount` özniteliğini kullanabilirsiniz. Daha fazla bilgi için bkz. [tetikleyici-öznitelikler](#trigger---attributes).

## <a name="output---configuration"></a>Çıkış-yapılandırma

Aşağıdaki tabloda, *function. JSON* dosyasında ve `Blob` özniteliğinde ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır.

|function. JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | Yok | @No__t-0 olarak ayarlanmalıdır. |
|**direction** | Yok | Çıkış bağlaması için `out` olarak ayarlanmalıdır. [Kullanım](#output---usage) bölümünde özel durumlar belirtilmiştir. |
|**ada** | Yok | İşlev kodundaki blobu temsil eden değişkenin adı.  İşlev dönüş değerine başvurmak için `$return` olarak ayarlayın.|
|**Yolun** |**Blobpath değerini adıyla** | Blob kapsayıcısının yolu. |
|**bağlanma** |**Bağlanma**| Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, `connection` ' ı "MyStorage" olarak ayarlarsanız, Işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. @No__t-0 boş bırakırsanız, Işlevler çalışma zamanı `AzureWebJobsStorage` adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır.<br><br>Bağlantı dizesi, [yalnızca BLOB depolama hesabı](../storage/common/storage-account-overview.md#types-of-storage-accounts)değil, genel amaçlı bir depolama hesabı için olmalıdır.|
|Yok | **Erişim** | Okuma veya yazma yapılıp yapılmayacağını belirtir. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Çıkış kullanımı

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#SCRIPT](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 'te `context.bindings.<name from function.json>` kullanarak blob verilerine erişin.

# <a name="pythontabpython"></a>[Python](#tab/python)

Blob depolamaya yazmak için aşağıdaki türler olarak işlev parametreleri bildirebilirsiniz:

* @No__t olarak dizeler-0
* @No__t olarak akışlar-0

Ayrıntılar için [Çıkış örneğine](#output---example) bakın.

# <a name="javatabjava"></a>[Java](#tab/java)

@No__t-0 özniteliği, işlevi tetikleyen bloba erişmenizi sağlar. Özniteliği ile bir bayt dizisi kullanıyorsanız, `dataType` ' ı `binary` olarak ayarlayın. Ayrıntılar için [Çıkış örneğine](#output---example) bakın.

---

## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlayıcısı |  Başvuru |
|---|---|
| Blob | [Blob hata kodları](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, tablo, kuyruk |  [Depolama hatası kodları](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tablo, kuyruk |  [Sorun giderme](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Sonraki adımlar

* [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->

---
title: Azure Işlevleri için Azure Blob depolama tetikleyicisi
description: Azure Blob depolama veri değişiklikleri olarak bir Azure Işlevi çalıştırmayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 1d83a828829d27d85749b3fa7b283cad9683bffc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455928"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Azure Işlevleri için Azure Blob depolama tetikleyicisi

BLOB depolama tetikleyicisi, yeni veya güncelleştirilmiş bir blob algılandığında bir işlevi başlatır. Blob içerikleri, [işleve giriş](./functions-bindings-storage-blob-input.md)olarak sağlanır.

Azure Blob depolama tetikleyicisi, genel amaçlı bir depolama hesabı gerektirir. [Hiyerarşik ad alanları](../storage/blobs/data-lake-storage-namespace.md) olan Storage v2 hesapları da desteklenir. Yalnızca blob hesabı kullanmak için veya uygulamanızda özelleştirilmiş gereksinimler varsa, bu tetikleyiciyi kullanmaya yönelik alternatifleri gözden geçirin.

Kurulum ve yapılandırma ayrıntıları hakkında bilgi için bkz. [genel bakış](./functions-bindings-storage-blob.md).

## <a name="polling"></a>Yoklama

Yoklama, günlükleri İnceleme ve düzenli kapsayıcı taramaları çalıştırma arasında karma olarak çalışır. Blob 'lar, aralıklar arasında kullanılan devamlılık belirtecine sahip bir zamanda 10.000 gruplarında taranır.

> [!WARNING]
> Ayrıca, [depolama günlükleri "en iyi çaba" temelinde oluşturulur](/rest/api/storageservices/About-Storage-Analytics-Logging) . Tüm olayların yakalandığı garanti yoktur. Bazı koşullarda Günlükler eksik olabilir.
> 
> Daha hızlı veya daha güvenilir bir blob işleme ihtiyacınız varsa, blobu oluştururken bir [kuyruk iletisi](../storage/queues/storage-dotnet-how-to-use-queues.md) oluşturmayı düşünün. Sonra blobu işlemek için blob tetikleyicisi yerine bir [kuyruk tetikleyicisi](functions-bindings-storage-queue.md) kullanın. Başka bir seçenek Event Grid kullanmaktır; [Event Grid kullanarak karşıya yüklenen görüntüleri yeniden boyutlandırmayı otomatikleştirme](../event-grid/resize-images-on-storage-blob-upload-event.md)öğreticisine bakın.
>

## <a name="alternatives"></a>Alternatifler

### <a name="event-grid-trigger"></a>Event Grid tetikleyicisi

[Event Grid tetikleyicisinin](functions-bindings-event-grid.md) [BLOB olayları](../storage/blobs/storage-blob-event-overview.md)için yerleşik desteği de vardır. Aşağıdaki senaryolar için BLOB depolama tetikleyicisi yerine Event Grid kullanın:

- **Yalnızca BLOB depolama hesapları**: blob ve çıkış bağlamaları için [yalnızca BLOB depolama hesapları](../storage/common/storage-account-overview.md#types-of-storage-accounts) desteklenir, ancak blob Tetikleyicileri için desteklenmez.

- **Yüksek ölçekli**: yüksek ölçek, bunlarda 100.000 ' den fazla BLOB veya saniyede 100 ' den fazla BLOB güncelleştirmesi olan bir depolama hesabı olan kapsayıcılar olarak esnek bir şekilde tanımlanabilir.

- En **aza indirme gecikmesi**: Işlev uygulamanız tüketim planalıyorsa, bir işlev uygulaması boşta kalırsa, yeni Blobları işlerken 10 dakikalık bir gecikme olabilir. Bu gecikmeyi önlemek için, her zaman etkin olan bir App Service planına geçebilirsiniz. BLOB depolama hesabınızla bir [Event Grid tetikleyicisi](functions-bindings-event-grid.md) de kullanabilirsiniz. Bir örnek için [Event Grid öğreticisine](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)bakın.

Event Grid bir örnek [Event Grid öğreticisiyle görüntü yeniden boyutlandırmaya](../event-grid/resize-images-on-storage-blob-upload-event.md) bakın.

### <a name="queue-storage-trigger"></a>Kuyruk depolama tetikleyicisi

Blobları işlemeye yönelik başka bir yaklaşım, oluşturulmakta veya değiştirilmekte olan bloblara karşılık gelen sıra iletilerini yazmak ve sonra işlemeye başlamak için bir [kuyruk depolama tetikleyicisi](./functions-bindings-storage-queue.md) kullanmaktır.

## <a name="example"></a>Örnek

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnek, kapsayıcıda bir blob eklendiğinde veya güncelleştirilirken bir günlüğü yazan bir [C# işlevini](functions-dotnet-class-library.md) gösterir `samples-workitems` .

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

`{name}`BLOB tetikleyici yolundaki dize, `samples-workitems/{name}` tetikleyici Blobun dosya adına erişmek için işlev kodunda kullanabileceğiniz bir [bağlama ifadesi](./functions-bindings-expressions-patterns.md) oluşturur. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [BLOB adı desenleri](#blob-name-patterns) bölümüne bakın.

Özniteliği hakkında daha fazla bilgi için `BlobTrigger` bkz. [öznitelikler ve ek açıklamalar](#attributes-and-annotations).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, birfunction.jsdosya ve bağlamayı kullanan kod *üzerindeki* bir blob tetikleyicisi bağlamasını gösterir. Kapsayıcıda bir blob eklendiğinde veya güncelleştirilirse, işlev bir günlük yazar `samples-workitems` [](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Dosyadaki *function.js* bağlama verileri aşağıda verilmiştir:

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

`{name}`BLOB tetikleyici yolundaki dize, `samples-workitems/{name}` tetikleyici Blobun dosya adına erişmek için işlev kodunda kullanabileceğiniz bir [bağlama ifadesi](./functions-bindings-expressions-patterns.md) oluşturur. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [BLOB adı desenleri](#blob-name-patterns) bölümüne bakın.

Dosya özellikleriyle ilgili *function.js* hakkında daha fazla bilgi için bkz. [yapılandırma](#configuration) bölümü bu özellikleri açıklar.

Aşağıda bir öğesine bağlanan C# betik kodu verilmiştir `Stream` :

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Aşağıda bir öğesine bağlanan C# betik kodu verilmiştir `CloudBlockBlob` :

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="java"></a>[Java](#tab/java)

Bu işlev, kapsayıcıda bir blob eklendiğinde veya güncelleştirilirken bir günlük yazar `myblob` .

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosya ve bağlamayı kullanan [JavaScript kodundaki](functions-reference-node.md) bir blob tetikleyicisi bağlamasını gösterir. Kapsayıcıda bir blob eklendiğinde veya güncelleştirilirse, işlev bir günlük yazar `samples-workitems` .

Dosyada *function.js* :

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

`{name}`BLOB tetikleyici yolundaki dize, `samples-workitems/{name}` tetikleyici Blobun dosya adına erişmek için işlev kodunda kullanabileceğiniz bir [bağlama ifadesi](./functions-bindings-expressions-patterns.md) oluşturur. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [BLOB adı desenleri](#blob-name-patterns) bölümüne bakın.

Dosya özellikleriyle ilgili *function.js* hakkında daha fazla bilgi için bkz. [yapılandırma](#configuration) bölümü bu özellikleri açıklar.

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki örnek, BLOB depolama kapsayıcısına bir dosya eklendiğinde çalışan bir işlevin nasıl oluşturulacağını göstermektedir `source` .

İşlev yapılandırma dosyası (_function.js_), öğesinin öğesine sahip bir bağlama içerir `type` `blobTrigger` ve ' a `direction` ayarlanır `in` .

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "MyStorageAccountConnectionString"
    }
  ]
}
```

_run.ps1_ dosyanın ilişkili kodu aşağıda verilmiştir.

```powershell
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, bir *function.js* dosya ve bağlamayı kullanan [Python kodundaki](functions-reference-python.md) bir blob tetikleyicisi bağlamasını gösterir. Kapsayıcıda bir blob eklendiğinde veya güncelleştirilirse, işlev bir günlük yazar `samples-workitems` [](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Dosyada *function.js* :

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

`{name}`BLOB tetikleyici yolundaki dize, `samples-workitems/{name}` tetikleyici Blobun dosya adına erişmek için işlev kodunda kullanabileceğiniz bir [bağlama ifadesi](./functions-bindings-expressions-patterns.md) oluşturur. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [BLOB adı desenleri](#blob-name-patterns) bölümüne bakın.

Dosya özellikleriyle ilgili *function.js* hakkında daha fazla bilgi için bkz. [yapılandırma](#configuration) bölümü bu özellikleri açıklar.

Python kodu aşağıda verilmiştir:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıklarında](functions-dotnet-class-library.md), bir blob tetikleyicisi yapılandırmak için aşağıdaki öznitelikleri kullanın:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Özniteliğin Oluşturucusu, izlenecek kapsayıcıyı ve isteğe bağlı olarak bir [BLOB adı modelini](#blob-name-patterns)gösteren bir yol dizesi alır. Aşağıda bir örnek verilmiştir:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  `Connection`Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için özelliğini ayarlayabilirsiniz:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Tüm örnek için bkz. [tetikleyici örneği](#example).

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

* `BlobTrigger`Özniteliğin `Connection` özelliği.
* Özniteliği `StorageAccount` ile aynı parametreye uygulanan öznitelik `BlobTrigger` .
* `StorageAccount`İşleve uygulanan öznitelik.
* `StorageAccount`Sınıfına uygulanan öznitelik.
* İşlev uygulaması için varsayılan depolama hesabı ("AzureWebJobsStorage" uygulama ayarı).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger`Özniteliği, işlevi tetikleyen bloba erişim sağlamak için kullanılır. Ayrıntılar için [tetikleyici örneğine](#example) bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Öznitelikler PowerShell tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `BlobTrigger` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok | Olarak ayarlanmalıdır `blobTrigger` . Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır.|
|**Görünüm** | yok | Olarak ayarlanmalıdır `in` . Bu özellik, Azure portal tetikleyiciyi oluşturduğunuzda otomatik olarak ayarlanır. [Kullanım](#usage) bölümünde özel durumlar belirtilmiştir. |
|**ada** | yok | İşlev kodundaki blobu temsil eden değişkenin adı. |
|**Yolun** | **Blobpath değerini adıyla** |İzlenecek [kapsayıcı](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) .  Bir [BLOB adı stili](#blob-name-patterns)olabilir. |
|**bağlanma** | **Bağlantı** | Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, `connection` "MyStorage" olarak ayarlarsanız, işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. `connection`Boş bırakırsanız, işlevler çalışma zamanı adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır `AzureWebJobsStorage` .<br><br>Bağlantı dizesi, [BLOB depolama hesabı](../storage/common/storage-account-overview.md#types-of-storage-accounts)değil, genel amaçlı bir depolama hesabı için olmalıdır.<br><br>[Uzantının 5. x veya daha yüksek bir sürümünü](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)kullanıyorsanız, bağlantı dizesi yerine bağlantıyı tanımlayan bir yapılandırma bölümüne başvuru sağlayabilirsiniz. Bkz. [Bağlantılar](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger`Özniteliği, işlevi tetikleyen bloba erişim sağlamak için kullanılır. Ayrıntılar için [tetikleyici örneğine](#example) bakın.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<NAME>` `<NAME>` *Üzerindefunction.js* tanımlanan değerle eşleşen blob verilerine erişin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Blob verilerine, dosyanın _function.js_ , bağlamanın Name parametresiyle belirtilen adla eşleşen bir parametre aracılığıyla erişin.

# <a name="python"></a>[Python](#tab/python)

Blob verilerine [InputStream](/python/api/azure-functions/azure.functions.inputstream)olarak yazılan parametre aracılığıyla erişin. Ayrıntılar için [tetikleyici örneğine](#example) bakın.

---

## <a name="blob-name-patterns"></a>Blob adı desenleri

Öğesinde `path` veya öznitelik oluşturucusunda *function.js* özelliğinde bir blob adı stili belirtebilirsiniz `BlobTrigger` . Ad stili bir [filtre veya bağlama ifadesi](./functions-bindings-expressions-patterns.md)olabilir. Aşağıdaki bölümlerde örnekler sağlanmaktadır.

> [!TIP]
> Kapsayıcı adı, ad düzeninde çözümleyici içeremez.

### <a name="get-file-name-and-extension"></a>Dosya adı ve uzantısını al

Aşağıdaki örnek, blob dosya adına ve uzantısına ayrı olarak nasıl bağlanılacağını gösterir:

```json
"path": "input/{blobname}.{blobextension}",
```

Blob *original-Blob1.txt* adlandırılmış ise, `blobname` ve `blobextension` işlev kodundaki değişkenlerin değerleri *özgün-Blob1* ve *txt*' dir.

### <a name="filter-on-blob-name"></a>Blob adını filtrele

Aşağıdaki örnek yalnızca `input` kapsayıcıda "özgün-" dizesiyle başlayan bloblarda tetiklenir:

```json
"path": "input/original-{name}",
```

Blob adı *original-Blob1.txt* ise, `name` işlev kodundaki değişkenin değeri olur `Blob1.txt` .

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

Blob *{20140101}-soundfile.mp3* adlandırılmışsa, `name` işlev kodundaki değişken değeri *soundfile.mp3*.

## <a name="metadata"></a>Meta veri

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="java"></a>[Java](#tab/java)

Java 'da meta veriler kullanılamıyor.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Meta veriler parametresi aracılığıyla kullanılabilir `$TriggerMetadata` .

# <a name="python"></a>[Python](#tab/python)

Meta veriler Python 'da kullanılamıyor.

---

## <a name="blob-receipts"></a>Blob alındıları

Azure Işlevleri çalışma zamanı, aynı yeni veya güncelleştirilmiş blob için bir blob tetikleyici işlevinin birden çok kez çağrılmamasını sağlar. Belirli bir blob sürümünün işlenip işlenmeyeceğini anlamak için, *BLOB alındıları* saklar.

Azure Işlevleri, blob bilgilerini, işlev uygulamanız için Azure depolama hesabındaki *Azure-WebJobs-Konakları* adlı bir kapsayıcıda depolar (uygulama ayarı tarafından tanımlanır `AzureWebJobsStorage` ). Blob alındı bilgisi aşağıdaki bilgilere sahiptir:

* Tetiklenen işlev ( `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>` Örneğin: `MyFunctionApp.Functions.CopyBlob` )
* Kapsayıcı adı
* Blob türü ( `BlockBlob` veya `PageBlob` )
* Blob adı
* ETag (bir blob sürüm tanımlayıcısı, örneğin: `0x8D1DC6E70A277EF` )

Bir Blobun yeniden işlenmesini zorlamak için, bu Blobun blob alındığını *Azure-WebJobs-hosts* kapsayıcısından el ile silin. Yeniden işleme hemen gerçekleşmeyebilir, daha sonraki bir noktada gerçekleşmesi garanti edilir. Hemen tekrar işlemek için, *Azure-WebJobs-hosts/blobscanınfo* 'daki *scanınfo* blobu güncelleştirilebilen bulunabilir. Özellikten sonra son değiştirilen zaman damgasına sahip tüm Bloblar `LatestScan` yeniden taranır.

## <a name="poison-blobs"></a>Poison blob 'ları

Belirli bir blob için bir blob tetikleme işlevi başarısız olduğunda, Azure Işlevleri varsayılan olarak toplam 5 kez çalışır.

5 ' in tamamı başarısız olursa, Azure Işlevleri *WebJobs-blobtrigger-zeadlı* bir depolama kuyruğuna bir ileti ekler. En fazla yeniden deneme sayısı yapılandırılabilir. Aynı MaxDequeueCount ayarı, zarar blobu işleme ve zarar sırası ileti işleme için kullanılır. Zarar Blobları için kuyruk iletisi aşağıdaki özellikleri içeren bir JSON nesnesidir:

* FunctionID (biçimde `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>` )
* BlobType ( `BlockBlob` veya `PageBlob` )
* ContainerName
* BlobName
* ETag (bir blob sürüm tanımlayıcısı, örneğin: `0x8D1DC6E70A277EF` )

## <a name="concurrency-and-memory-usage"></a>Eşzamanlılık ve bellek kullanımı

Blob tetikleyicisi bir kuyruğu dahili olarak kullanır, bu nedenle eşzamanlı işlev çağırma sayısı üst sınırı, [host.jsüzerinde bulunan kuyruklar yapılandırması](functions-host-json.md#queues)tarafından denetlenir. Varsayılan ayarlar eşzamanlılık, 24 çağırma ile sınırlıdır. Bu sınır, blob tetikleyicisi kullanan her bir işleve ayrı olarak uygulanır.

> [!NOTE]
> [Depolama uzantısının 5.0.0 veya daha yüksek sürümünü](functions-bindings-storage-blob.md#storage-extension-5x-and-higher)kullanan uygulamalar için, host.js' deki kuyruklar yapılandırması yalnızca kuyruk Tetikleyicileri için geçerlidir. Blob tetikleyicisi eşzamanlılık, bunun yerine [host.jsüzerindeki blob yapılandırması](functions-host-json.md#blobs)tarafından denetlenir.

[Tüketim planı](event-driven-scaling.md) bir sanal MAKINEDE (VM) bir işlev UYGULAMASıNı 1,5 GB bellek ile sınırlandırır. Bellek, her eşzamanlı çalıştırılan işlev örneği ve Işlevler çalışma zamanının kendisi tarafından kullanılır. Blob ile tetiklenen bir işlev tüm blobu belleğe yüklerse, bu işlev tarafından yalnızca blob 'lar için kullanılan en fazla bellek 24 * en yüksek blob boyutudur. Örneğin, üç blob ile tetiklenen işlevlere sahip bir işlev uygulaması ve varsayılan ayarlar en fazla VM başına eşzamanlılık 3 * 24 = 72 işlev etkinleştirmeleri olacaktır.

JavaScript ve Java işlevleri tüm Blobun belleğe yüklenmesini sağlar ve C# işlevleri `string` , veya ' a bağlarsanız, veya `Byte[]` .

## <a name="hostjson-properties"></a>host.jsözelliklerde

Dosyadaki [host.js](functions-host-json.md#blobs) blob tetikleme davranışını denetleyen ayarları içerir. Kullanılabilir ayarlarla ilgili ayrıntılar için [ ayarlarıhost.js](functions-bindings-storage-blob.md#hostjson-settings) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Bir işlev çalıştığında BLOB depolama verilerini okuma](./functions-bindings-storage-blob-input.md)
- [Bir işlevden BLOB depolama verileri yazma](./functions-bindings-storage-blob-output.md)

---
title: Visual Studio (WebJob projeleri) kullanarak kuyruk depolamayı kullanmaya başlama
description: Visual Studio bağlı hizmetler 'i kullanarak bir depolama hesabına bağlandıktan sonra bir WebJob projesinde Azure kuyruk depolamayı kullanmaya başlama.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ffba203bafaf3837cd2d7fc1a6fd962a6926b186
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298757"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Azure kuyruk depolama ve Visual Studio bağlı hizmetleri (WebJob projeleri) ile çalışmaya başlama
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Genel Bakış
Bu makalede, Visual Studio **bağlı hizmetler Ekle** iletişim kutusunu kullanarak bir Azure depolama hesabı oluşturduktan veya başvurduktan sonra bir Visual Studio Azure WebJob projesinde Azure kuyruk depolama hizmetini kullanmaya başlama işlemi açıklanır. Visual Studio **bağlı hizmetler Ekle** iletişim kutusunu kullanarak bir Web işi projesine bir depolama hesabı eklediğinizde, uygun Azure depolama NuGet paketleri yüklenir, projeye uygun .NET başvuruları eklenir ve için bağlantı dizeleri depolama hesabı App. config dosyasında güncelleştirilir.  

Bu makalede, C# Azure kuyruk depolama hizmeti Ile Azure WEBJOBS SDK sürüm 1. x ' in nasıl kullanılacağını gösteren kod örnekleri sağlanmaktadır.

Azure Kuyruk depolama, HTTP veya HTTPS kullanan kimlik doğrulaması yapılmış çağrılar aracılığıyla dünyanın her yerinden erişilebilen çok sayıda iletinin depolanması için bir hizmettir. Tek bir kuyruk iletisinin boyutu 64 KB’ye kadar olabilir ve bir kuyrukta, depolama hesabının toplam kapasite sınırına kadar milyonlarca ileti bulunabilir. Daha fazla bilgi için bkz. [.NET kullanarak Azure kuyruk depolama ile çalışmaya başlama](../storage/queues/storage-dotnet-how-to-use-queues.md) . ASP.NET hakkında daha fazla bilgi için bkz. [ASP.net](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Bir kuyruk iletisi alındığında bir işlevi tetikleme
Web Işleri SDK 'sının bir kuyruk iletisi alındığında çağırdığı bir işlev yazmak için **Queuetrigger** özniteliğini kullanın. Öznitelik Oluşturucusu, yoklamak için kuyruğun adını belirten bir String parametresi alır. Sıra adını dinamik olarak ayarlamayı öğrenmek için [yapılandırma seçeneklerini ayarlamayı](#how-to-set-configuration-options)inceleyin.

### <a name="string-queue-messages"></a>Dize sırası iletileri
Aşağıdaki örnekte, kuyruk bir dize iletisi içerir, bu nedenle **Queuetrigger** , kuyruk iletisinin Içeriğini Içeren **LogMessage** adlı bir dize parametresine uygulanır. İşlevi [panoya bir günlük iletisi yazar](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

**Dizenin**yanı sıra, parametresi bir bayt dizisi, **cloudqueuemessage** nesnesi veya tanımladığınız bir poco olabilir.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>POCO [(düz eskı CLR nesnesi](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) sıra iletileri
Aşağıdaki örnekte, kuyruk iletisi **Blobname** özelliği Içeren bir **BlobInformation** nesnesi için JSON içeriyor. SDK, nesneyi otomatik olarak seri hale getirir.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

SDK, iletileri seri hale getirmek ve seri durumdan çıkarmak için [Newtonsoft. JSON NuGet paketini](https://www.nuget.org/packages/Newtonsoft.Json) kullanır. Web Işleri SDK 'sını kullanmayan bir programda kuyruk iletileri oluşturursanız, SDK 'nın ayrıştırabileceği bir POCO kuyruğu iletisi oluşturmak için aşağıdaki örneğe benzer bir kod yazabilirsiniz.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Zaman uyumsuz işlevler
Aşağıdaki zaman uyumsuz işlevi [panoya bir günlük yazar](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Zaman uyumsuz işlevler, bir blobu kopyalayan aşağıdaki örnekte gösterildiği gibi bir [iptal belirteci](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)alabilir. ( **Queuetrigger** yer tutucusu hakkında bir açıklama Için, [Bloblar](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) bölümüne bakın.)

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>QueueTrigger özniteliği ile birlikte çalışarak türler
Aşağıdaki türlerle **Queuetrigger** kullanabilirsiniz:

* **dizisinde**
* JSON olarak seri hale getirilmiş bir POCO türü
* **Byte []**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Yoklama algoritması
SDK, depolama işlem maliyetlerinde boşta sıra yoklamanın etkisini azaltmak için rastgele bir üstel geri alma algoritması uygular.  Bir ileti bulunduğunda, SDK iki saniye bekler ve sonra başka bir ileti olup olmadığını denetler; hiçbir ileti bulunamadığında, yeniden denemeden önce dört saniye bekler. Sonraki başarısız bir kuyruk iletisi almaya çalıştıktan sonra, bekleme süresi, varsayılan olarak bir dakika olacak şekilde en fazla bekleme süresine ulaşana kadar artmaya devam eder. [En uzun bekleme süresi yapılandırılabilir](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Birden çok örnek
Web uygulamanız birden çok örnek üzerinde çalışıyorsa, her makinede sürekli bir Web Işi çalışır ve her makine Tetikleyicileri bekler ve işlevleri çalıştırmaya çalışır. Bazı senaryolarda bu, bazı işlevlerin aynı verileri iki kez işlemesine neden olabilir, bu nedenle işlevler ıdempotent olmalıdır (aynı giriş verileriyle tekrar tekrar çağırmak yinelenen sonuçlar üretmez).  

## <a name="parallel-execution"></a>Paralel yürütme
Farklı kuyruklarda dinleme yapan birden çok işlevleriniz varsa, iletiler aynı anda alındığında SDK bu uygulamaları paralel olarak çağırır.

Tek bir sıra için birden fazla ileti alındığında aynı değer geçerlidir. Varsayılan olarak SDK, bir seferde 16 kuyruk iletisi toplu işi alır ve bunları paralel olarak işleyen işlevi yürütür. [Toplu iş boyutu yapılandırılabilir](#how-to-set-configuration-options). İşlenen sayı, toplu iş boyutunun yarısını altına iniyorsa, SDK başka bir Batch alır ve bu iletileri işlemeye başlar. Bu nedenle, işlev başına işlenen en fazla eşzamanlı ileti sayısı bir ve toplu iş boyutunun yarı bir katına biridir. Bu sınır, bir **Queuetrigger** özniteliği olan her bir işleve ayrı olarak uygulanır. Bir kuyrukta alınan iletiler için paralel yürütmeyi istemiyorsanız, toplu iş boyutunu 1 olarak ayarlayın.

## <a name="get-queue-or-queue-message-metadata"></a>Kuyruğu veya kuyruk iletisi meta verilerini alma
Yöntem imzasına parametreler ekleyerek aşağıdaki ileti özelliklerini alabilirsiniz:

* **DateTimeOffset** ExpirationTime
* **DateTimeOffset** ınsertiontime
* **DateTimeOffset** nextvisibletime
* **dize** queuetrigger (ileti metnini içerir)
* **dize** kimliği
* **dize** PopReceipt
* **int** dequeuecount

Doğrudan Azure Storage API 'SI ile çalışmak istiyorsanız, **Cloudstorageaccount** parametresi de ekleyebilirsiniz.

Aşağıdaki örnek, tüm bu meta verileri bir BILGI uygulama günlüğüne yazar. Örnekte, hem logMessage hem de queueTrigger kuyruk iletisinin içeriğini içerir.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Örnek kod tarafından yazılan örnek bir günlük aşağıda verilmiştir:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Düzgün kapanma
Sürekli bir WebJob 'ta **çalışan bir işlev, WebJob parametresini kabul** edebilir. Bu, Işletim sisteminin Web işi sonlandırıldıktan sonra işleve bildirmesini sağlar. Bu bildirimi, işlevin verileri tutarsız bir durumda bırakmak için beklenmedik bir şekilde sonlandığından emin olmak için kullanabilirsiniz.

Aşağıdaki örnek, bir işlevde yaklaşan WebJob sonlandırmasının nasıl kontrol alınacağını gösterir.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Note:** Pano, kapatılmış işlevlerin durumunu ve çıkışını doğru şekilde gösteremeyebilir.

Daha fazla bilgi için bkz. [WebJobs düzgün kapanma](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Kuyruk iletisi işlenirken kuyruk iletisi oluşturma
Yeni bir kuyruk iletisi oluşturan bir işlev yazmak için **Queue** özniteliğini kullanın. **Queuetrigger**gibi, kuyruk adını bir dize olarak geçitirsiniz veya [kuyruk adını dinamik olarak ayarlayabilirsiniz](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Dize sırası iletileri
Aşağıdaki zaman uyumsuz olmayan kod örneği, "InputQueue" adlı kuyrukta alınan sıra iletisiyle aynı içeriğe sahip "outputqueue" adlı sırada yeni bir kuyruk iletisi oluşturur. (Zaman uyumsuz işlevler için bu bölümün ilerleyen kısımlarında gösterildiği gibi **ıasynccollector @ no__t-1T >** kullanın.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>POCO [(düz eskı CLR nesnesi](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) sıra iletileri
Bir dize yerine POCO içeren bir kuyruk iletisi oluşturmak için, POCO türünü **Queue** özniteliği oluşturucusuna bir output parametresi olarak geçirin.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

SDK, nesneyi otomatik olarak JSON 'a serileştirir. Nesne null olsa bile kuyruk iletisi her zaman oluşturulur.

### <a name="create-multiple-messages-or-in-async-functions"></a>Birden çok ileti veya zaman uyumsuz işlevlerde oluşturma
Birden çok ileti oluşturmak için, aşağıdaki örnekte gösterildiği gibi, **ICollector @ no__t-1T >** veya **ıasynccollector @ No__t-3T >** çıkış sırasının parametre türünü yapın.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Her kuyruk iletisi, **Add** yöntemi çağrıldığında hemen oluşturulur.

### <a name="types-that-the-queue-attribute-works-with"></a>Kuyruk özniteliğinin birlikte çalışması için gereken türler
Aşağıdaki parametre türlerinde **Queue** özniteliğini kullanabilirsiniz:

* **Out dizesi** (işlev sonlandığında parametre değeri null değilse kuyruk iletisi oluşturur)
* **Out baytı []** ( **dize**gibi)
* **Out CloudQueueMessage** ( **dize**gibi)
* **Out POCO** (seri hale getirilebilir bir tür, işlev sona erdiğinde parametre null ise null nesnesiyle bir ileti oluşturur)
* **ICollector**
* **Iasynccollector**
* **Cloudqueue** (Azure Storage API 'sini doğrudan kullanarak el ile ileti oluşturmak için)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Bir işlevin gövdesinde WebJobs SDK özniteliklerini kullanma
**Queue**, **BLOB**veya **Table**gibi bir WebJobs SDK özniteliği kullanmadan önce Işlevinizde bazı çalışmalar yapmanız gerekiyorsa, **ıciltçi** arabirimini kullanabilirsiniz.

Aşağıdaki örnek bir giriş kuyruğu iletisi alır ve bir çıkış kuyruğunda aynı içeriğe sahip yeni bir ileti oluşturur. Çıkış sırası adı, işlevin gövdesinde kodla ayarlanır.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

**Iciltçi** arabirimi **tablo** ve **BLOB** öznitelikleriyle de kullanılabilir.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Kuyruk iletisini işlerken blob 'ları ve tabloları okuma ve yazma
**BLOB** ve **tablo** öznitelikleri, Blobları ve tabloları okuyup yazmanızı sağlar. Bu bölümdeki örnekler Bloblar için geçerlidir. Blobların oluşturulduğu veya güncelleştirildiği sırada işlemlerin nasıl tetikleneceğini gösteren kod örnekleri için bkz. [Azure Blob depolamayı Web İşleri SDK 'sı ile kullanma](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Blob işlemlerini tetikleyen dize sırası iletileri
Bir dize içeren bir kuyruk iletisi için **Queuetrigger** , ileti Içeriğini içeren **BLOB** özniteliğinin **blobpath** parametresinde kullanabileceğiniz bir yer tutucudur.

Aşağıdaki örnek, blob 'ları okumak ve yazmak için **Stream** nesnelerini kullanır. Kuyruk iletisi, textblobları kapsayıcısında bulunan bir Blobun adıdır. "-New" adlı Blobun bir kopyası ada eklenerek aynı kapsayıcıda oluşturulur.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

**BLOB** öznitelik Oluşturucusu, kapsayıcıyı ve BLOB adını belirten bir **blobpath** parametresi alır. Bu yer tutucu hakkında daha fazla bilgi için bkz. [WebJobs SDK Ile Azure Blob depolamayı kullanma](https://github.com/Azure/azure-webjobs-sdk/wiki).

Öznitelik bir **Stream** nesnesini tasarlaştırır, başka bir oluşturucu parametresi **FileAccess** modunu okuma, yazma veya okuma/yazma olarak belirtir.

Aşağıdaki örnek bir blobu silmek için bir **Cloudblockblob** nesnesi kullanır. Kuyruk iletisi, Blobun adıdır.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>POCO [(düz eskı CLR nesnesi](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) sıra iletileri
Kuyruk iletisinde JSON olarak depolanan bir POCO için, **Queue** özniteliğinin **blobpath** parametresindeki nesnenin özelliklerini adlandırmak için yer tutucuları kullanabilirsiniz. Sıra meta veri özellik adlarını yer tutucular olarak da kullanabilirsiniz. Bkz. [kuyruk veya kuyruk iletisi meta verilerini alma](#get-queue-or-queue-message-metadata).

Aşağıdaki örnek, bir blobu farklı bir uzantıya sahip yeni bir bloba kopyalar. Kuyruk iletisi **Blobname** ve **BlobNameWithoutExtension** özelliklerini içeren bir **BlobInformation** nesnesidir. Özellik adları, **BLOB** öznitelikleri için blob yolunda yer tutucu olarak kullanılır.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

SDK, iletileri seri hale getirmek ve seri durumdan çıkarmak için [Newtonsoft. JSON NuGet paketini](https://www.nuget.org/packages/Newtonsoft.Json) kullanır. Web Işleri SDK 'sını kullanmayan bir programda kuyruk iletileri oluşturursanız, SDK 'nın ayrıştırabileceği bir POCO kuyruğu iletisi oluşturmak için aşağıdaki örneğe benzer bir kod yazabilirsiniz.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Bir blobu bir nesneye bağlamadan önce işlevinizde bazı çalışmalar yapmanız gerekiyorsa, [bir işlevin gövdesinde WebJobs SDK özniteliklerini kullanma](#use-webjobs-sdk-attributes-in-the-body-of-a-function)bölümünde gösterildiği gibi, işlev gövdesinde özniteliğini kullanabilirsiniz.

### <a name="types-you-can-use-the-blob-attribute-with"></a>Blob özniteliği ile birlikte kullanabileceğiniz türler
**BLOB** özniteliği aşağıdaki türlerle kullanılabilir:

* **Stream** (FileAccess Oluşturucu parametresi kullanılarak belirtilen okuma veya yazma)
* **Değerine**
* **TextWriter**
* **dize** (okuma)
* **Out dizesi** (Write; yalnızca işlev döndürüldüğünde dize parametresi null değilse bir blob oluşturur)
* POCO (okuma)
* Out POCO (Write; her zaman bir blob oluşturur, işlev döndürüldüğünde POCO parametresi null ise null nesne olarak oluşturulur)
* **Cloudblobstream** (yazma)
* **Ihoparlör blobu** (okuma veya yazma)
* **Cloudblockblob** (okuma veya yazma)
* **Cloudpageblob** (okuma veya yazma)

## <a name="how-to-handle-poison-messages"></a>Zarar iletilerini işleme
İçeriği bir işleve başarısız olmasına neden olan iletiler, *zarar iletileri*olarak adlandırılır. İşlev başarısız olursa, kuyruk iletisi silinmez ve sonuç olarak yeniden oluşturulur ve bu da döngüyü tekrarlanabilir. SDK, sınırlı sayıda yinelemeden sonra döngüyü otomatik olarak kesebilir veya el ile yapabilirsiniz.

### <a name="automatic-poison-message-handling"></a>Otomatik zehirli ileti işleme
SDK, bir kuyruk iletisini işlemek için 5 kata kadar bir işlev çağırır. Beşinci deneme başarısız olursa, ileti bir Poison kuyruğuna taşınır. [Yapılandırma seçeneklerini ayarlama](#how-to-set-configuration-options)bölümünde en fazla yeniden deneme sayısını nasıl yapılandıracağınızı görebilirsiniz.

Zarar sırası *{originalsıraadı}* -zarar olarak adlandırılmıştır. Onları günlüğe kaydederek veya el ile ilgilenilmesi gereken bir bildirim göndererek, zarar kuyruğundan iletileri işlemek için bir işlev yazabilirsiniz.

Aşağıdaki örnekte, bir kuyruk iletisi mevcut olmayan bir Blobun adını içerdiğinde **Copyblob** işlevi başarısız olur. Bu durumda, ileti copyblobqueue sırasından copyblobqueue-Poison kuyruğuna taşınır. **Processzemessage** , daha sonra zarar iletisini günlüğe kaydeder.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

Aşağıdaki çizimde, bir zarar iletisi işlendiğinde bu işlevlerden konsol çıktısı gösterilmektedir.

![Zarar iletisi işleme için konsol çıkışı](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>El ile zarar iletisi işleme
İşlevinizin **Dequeuecount** adlı bir **int** parametre ekleyerek işlenmek üzere bir ileti alma işleminin kaç kez çekileceğini görebilirsiniz. Daha sonra, işlev kodundaki sıradan çıkarma sayısını denetleyebilir ve aşağıdaki örnekte gösterildiği gibi, sayı bir eşiği aştığında kendi zararlı ileti işlemeyi gerçekleştirebilirsiniz.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Yapılandırma seçeneklerini ayarlama
Aşağıdaki yapılandırma seçeneklerini ayarlamak için **Jobhostconfiguration** türünü kullanabilirsiniz:

* Koddaki SDK bağlantı dizelerini ayarlayın.
* Maksimum sıradan çıkarma sayısı gibi **Queuetrigger** ayarlarını yapılandırın.
* Yapılandırmadan sıra adlarını alın.

### <a name="set-sdk-connection-strings-in-code"></a>Koddaki SDK bağlantı dizelerini ayarla
Kodda SDK bağlantı dizelerini ayarlamak, aşağıdaki örnekte gösterildiği gibi yapılandırma dosyalarında veya ortam değişkenlerinde kendi bağlantı dizesi adlarınızı kullanmanıza olanak sağlar.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>QueueTrigger ayarlarını yapılandırma
Kuyruk iletisi işleme için uygulanan aşağıdaki ayarları yapılandırabilirsiniz:

* Paralel olarak yürütülmesi gereken en fazla sıra iletisi sayısı (varsayılan değer 16 ' dır).
* Bir kuyruk iletisi bir Zemi kuyruğuna gönderilmeden önce en fazla yeniden deneme sayısı (varsayılan 5 ' tir).
* Bir kuyruk boş olduğunda tekrar yoklamadan önce beklenecek en uzun süre (varsayılan 1 dakikadır).

Aşağıdaki örnek, bu ayarların nasıl yapılandırılacağını göstermektedir:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Koddaki WebJobs SDK Oluşturucu parametreleri için değerleri ayarla
Bazen bir kuyruk adı, bir blob adı veya kapsayıcısı ya da kod içinde sabit kod yerine bir tablo adı belirtmek isteyebilirsiniz. Örneğin, bir yapılandırma dosyasında veya ortam değişkeninde **Queuetrigger** için sıra adını belirtmek isteyebilirsiniz.

Bunu, **Jobhostconfiguration** türüne bir **Nameresolver** nesnesi geçirerek yapabilirsiniz. Yüzde (%) ile çevrelenen özel yer tutucuları dahil edebilirsiniz WebJobs SDK öznitelik Oluşturucu parametrelerinde oturum açar ve **Nameresolver** kodunuz, bu yer tutucuların yerine kullanılacak gerçek değerleri belirtir.

Örneğin, test ortamında logqueuetest adlı bir kuyruk kullanmak istediğinizi ve bir tane de üretimde logqueueprod adlı bir sıra kullanacağınızı varsayalım. Sabit kodlanmış bir sıra adı yerine, **appSettings** koleksiyonunda gerçek sıra adına sahip bir girdinin adını belirtmek istersiniz. **AppSettings** anahtarı logqueue ise, işleviniz aşağıdaki örneğe benzeyebilir.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Aşağıdaki örnekte gösterildiği gibi, **Nameresolver** sınıfınız daha sonra **appSettings** 'ten sıra adını alabilir:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Aşağıdaki örnekte gösterildiği gibi, **Nameresolver** sınıfını ' de **jobhost** nesnesine geçirirsiniz.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Note:** Bir işlev her çağrıldığında kuyruk, tablo ve BLOB adları çözümlenir, ancak blob kapsayıcı adları yalnızca uygulama başlatıldığında çözümlenir. İş çalışırken blob kapsayıcısı adını değiştiremezsiniz.

## <a name="how-to-trigger-a-function-manually"></a>Bir işlevi el ile tetikleme
Bir işlevi el ile tetiklemek için, aşağıdaki örnekte gösterildiği gibi, **Jobhost** nesnesi üzerinde **Call** veya **Callasync** yöntemini ve işlevindeki **noautomatictrigger** özniteliğini kullanın.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Günlükleri yazma
Panoda Günlükler iki yerde gösterilir: WebJob için sayfa ve belirli bir WebJob çağrısı için sayfa.

![WebJob 'daki Günlükler sayfası](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![İşlev çağırma sayfasındaki Günlükler](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Bir işlevde veya **Main ()** yönteminde çağırdığınız konsol yöntemlerinden çıkış, belirli bir yöntem çağrısı için sayfada değil, WebJob için Pano sayfasında görünür. Yöntem imzanızın bir parametresinden aldığınız TextWriter nesnesinden alınan çıkış, bir yöntem çağırma için Pano sayfasında görüntülenir.

Konsol çıktısı tek iş parçacıklı olduğundan, çok sayıda iş işlevi aynı anda çalışıyor olabileceğinden konsol çıkışı belirli bir yöntem çağrısına bağlanamaz. Bu nedenle, SDK 'nın her bir işlev çağrısını kendi benzersiz günlük yazıcı nesnesiyle sağladığını unutmayın.

[Uygulama izleme günlüklerini](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview)yazmak için **Console. out** (bilgi olarak işaretlenen Günlükler oluşturur) ve **Console. Error** (hata olarak işaretlenen Günlükler oluşturur) kullanın. Alternatif olarak, bilgi ve hatanın yanı sıra ayrıntılı, uyarı ve kritik düzeyler sağlayan [Trace veya TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)kullanılır. Azure Web uygulamanızı nasıl yapılandırdığınıza bağlı olarak, uygulama izleme günlükleri Web uygulaması günlük dosyalarında, Azure tablolarında veya Azure Bloblarında görüntülenir. Tüm konsol çıktılarına doğru olduğu gibi, en son 100 uygulama günlükleri de bir işlev çağırma sayfası için değil, WebJob için Pano sayfasında de görüntülenir.

Konsol çıktısı, panoda, program yerel olarak veya başka bir ortamda çalışıyorsa değil, yalnızca bir Azure WebJob 'ta çalışıyorsa görüntülenir.

Pano bağlantı dizesini null olarak ayarlayarak günlüğe kaydetmeyi devre dışı bırakabilirsiniz. Daha fazla bilgi için bkz. [yapılandırma seçeneklerini ayarlama](#how-to-set-configuration-options).

Aşağıdaki örnek, günlükleri yazmanın çeşitli yollarını göstermektedir:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

WebJobs SDK panosunda, belirli bir işlev çağrısı için sayfaya gittiğinizde ve **çıkışı geç**' i seçtiğinizde **TextWriter** nesnesinden alınan çıkış görüntülenir.

![Çağırma bağlantısı](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![İşlev çağırma sayfasındaki Günlükler](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

WebJobs SDK panosunda, WebJob 'un en son 100 satırı, Web Işi sayfasına gittiğinizde (işlev çağrısı için değil), **çıkışı geç**' i seçtiğinizde görüntülenir.

![Çıkışı geç](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Sürekli bir WebJob 'ta uygulama günlükleri, Web uygulaması dosya sisteminde/Data/Jobs/Continuous/ *{webjobname}* /job_log.txt içinde görünür.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Azure Blob 'da uygulama günlükleri şu şekilde görünür: 2014-09-26T21:01:13, Information, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 17, Console. Write-Hello World!, 2014-09-26T21:01:13, hata, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 19, konsol. hata-Merhaba Dünya!, 2014-09-26T21:01:13, bilgi, contosoadsnew, 491e54, 635473620738529920, 0, 17404, 17, Console. out-Merhaba Dünya!,

Ve bir Azure tablosunda **konsol. out** ve **Console. hata** günlükleri şöyle görünür:

![Tablodaki bilgi günlüğü](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Tablodaki hata günlüğü](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Azure kuyrukları ile çalışmaya yönelik yaygın senaryoları nasıl işleyebileceğini gösteren kod örnekleri verilmiştir. Azure WebJobs ve WebJobs SDK 'sını kullanma hakkında daha fazla bilgi için bkz. [Azure WebJobs belge kaynakları](https://go.microsoft.com/fwlink/?linkid=390226).


---
title: ".NET kullanarak Azure Kuyruk depolamaya başlama | Microsoft Belgeleri"
description: "Azure Queues, uygulama bileşenleri arasında güvenilir ve zaman uyumsuz mesajlaşma sağlar. Bulut mesajlaşma özelliği uygulama bileşenlerinizin bağımsız olarak ölçeklendirilmesini sağlar."
services: storage
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: c0f82537-a613-4f01-b2ed-fc82e5eea2a7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 5876a22651a5ebee2d60992cf08aeae54256fb7d
ms.lasthandoff: 03/28/2017

---

# <a name="get-started-with-azure-queue-storage-using-net"></a>.NET kullanarak Azure Kuyruk Depolamaya başlayın
[!INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Genel Bakış
Azure Queue depolama birimi, uygulama bileşenleri arasında bulut mesajlaşma özelliği sağlar. Ölçeklendirmek üzere uygulama tasarlarken, uygulama bileşenleri birbirinden bağımsız şekilde ölçeklenebilmek için genellikle birbirinden ayrılır. Kuyruk depolama bulutta, masaüstünde, şirket içi sunucuda veya mobil bir cihazda çalışan uygulama bileşenleri arasındaki iletişim için zaman uyumsuz mesajlaşma sunar. Kuyruk depolama ayrıca zaman uyumsuz görevlerin yönetilmesini ve süreç iş akışlarının oluşturulmasını destekler.

### <a name="about-this-tutorial"></a>Bu öğretici hakkında
Bu öğreti, Azure kuyruk depolama kullanarak bazı genel senaryolar için .NET kodunun nasıl yazılacağını göstermektedir. Kapsanan senaryolara kuyruk oluşturma ve silme ile kuyruk iletileri ekleme, okuma ve silme dahildir.

**Tahmini tamamlanma süresi:** 45 dakika

**Önkoşullar:**

* [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
* [.NET için Azure Depolama İstemcisi](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [.NET için Azure Yapılandırma Yöneticisi](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Bir [Azure Storage hesabı](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Using yönergeleri ekleme
Aşağıdaki `using` yönergelerini `Program.cs` dosyasının üst tarafına ekleyin:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="parse-the-connection-string"></a>Bağlantı dizesini ayrıştırma
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Kuyruk hizmeti istemcisi oluşturma
**CloudQueueClient** sınıfı, Kuyruk depolamada depolanan kuyrukları almanızı sağlar. Hizmet istemcisini oluşturma yöntemlerinden biri aşağıda verilmiştir:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```
    
Artık Kuyruk depolamadan veri okuyan ve bu depolamaya veri yazan kodu yazmaya hazırsınız.

## <a name="create-a-queue"></a>Bir kuyruk oluşturma
Bu örnek, zaten yoksa, nasıl bir kuyruk oluşturulacağını gösterir:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Kuyruğa bir ileti yerleştirme
Varolan bir sıraya bir ileti yerleştirmek için ilk olarak yeni bir **CloudQueueMessage** oluşturun. Ardından **AddMessage** yöntemini çağırın. **CloudQueueMessage** bir dizeden (UTF-8 biçiminde) veya bir **bayt** dizisinden oluşturulabilir. Burada, bir kuyruk oluşturan (eğer yoksa) ve 'Hello, World' iletisini yerleştiren bir kod yer almaktadır:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Sonraki iletiye gözatın
**PeekMessage** yöntemini çağırarak iletiyi kuyruktan kaldırmadan kuyruğun önündeki iletiye göz atabilirsiniz.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

## <a name="change-the-contents-of-a-queued-message"></a>Kuyruğa alınan iletinin içeriğini değiştirme
Kuyrukta yer alan bir iletinin içeriğini değiştirebilirsiniz. Eğer ileti bir iş görevini temsil ediyorsa, bu özelliği kullanarak iş görevinin durumunu güncelleştirebilirsiniz. Aşağıdaki kod kuyruk iletisini yeni içeriklerle güncelleştirir ve görünürlük zaman aşımını 60 saniye daha uzatır. Bu, ileti ile ilişkili işin durumunu kaydeder ve istemciye ileti üzerinde çalışmaya devam etmesi için bir dakika daha zaman verir. Bir işleme adımı donanım veya yazılım arızasından dolayı başarısız olursa baştan başlamanıza gerek kalmadan kuyruk iletilerindeki çok adımlı iş akışlarını izlemek için bu yöntemi kullanabilirsiniz. Genellikle bir yeniden deneme sayacı tutmanı gerekir ve bir ileti *n* seferden daha fazla yeniden denenirse, silebilirsiniz. Bu, her işlendiğinde bir uygulama hatası tetikleyen bir iletiye karşı koruma sağlar.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent("Updated contents.");
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>Sonraki iletiyi sıradan çıkarmak
Kodunuz, bir iletiyi bir kuyruktan iki adımda çıkarır. **GetMessage**’ı çağırdığınzda, bir kuyruktaki bir sonraki iletiyi alırsınız. **GetMessage**’dan dönen bir ileti bu kuyruktaki kod okuyan iletilere karşı görünmez olur. Varsayılan olarak bu ileti 30 saniye görünmez kalır. İletiyi kuyruktan kaldırmayı tamamlamak için ayrıca **DeleteMessage**’ı çağırmanız gerekir. Bir iletinin iki adımlı kaldırılma süreci, donanım veya yazılım arızasından dolayı kodunuzun bir iletiyi işleyememesi durumunda kodunuzun başka bir örneğinin aynı iletiyi alıp yeniden denemesini sağlar. Kodunuz ileti işlendikten hemen sonra **DeleteMessage**’ı çağırır.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Genel Kuyruk depolama API’leri ile Zaman Uyumsuz-Bekleme yöntemini kullanma
Bu örnek, genel Kuyruk depolama API’leri ile Zaman Uyumsuz-Bekleme yönteminin nasıl kullanılacağını gösterir. Örnek, her yönteme eklenen *Async* soneki ile belirtildiği şekilde kullanılan yöntemlerin her birinin zaman uyumsuz sürümlerini çağırır. Zaman uyumsuz bir yöntem kullanıldığında, zaman uyumsuz-bekleme yöntemi çağrı tamamlanana kadar yerel çalıştırmayı askıya alır. Bu davranış geçerli iş parçacığının başka işler yapmasını sağlar ve böylece performans sorunlarını engellemeye yardımcı olur, uygulamanızın genel yanıt hızını iyileştirir. .NET’te Zaman Uyumsuz-Bekleme yönteminin kullanılması ile ilgili daha fazla ayrıntı için bkz. [Zaman Uyumsuz ve Bekleme (C# ve Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```
    
## <a name="leverage-additional-options-for-de-queuing-messages"></a>İletilerin kuyruktan çıkarılması için ek seçenekleri kullanma
İletilerin bir kuyruktan alınma şeklini iki yöntemle özelleştirebilirsiniz.
İlk olarak toplu iletiler alabilirsiniz (en fazla 32). İkinci olarak daha uzun veya daha kısa bir görünmezlik süresi ayarlayarak kodunuzun her iletiyi tamamen işlemesi için daha az veya daha fazla zaman tanıyabilirsiniz. Aşağıdaki kod örneğinde tek çağrıda 20 ileti almak için **GetMessages** yöntemi kullanılmıştır. Ardından her ileti bir **foreach** döngüsü ile işlenir. Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır. 5 dakikalık sürenin tüm iletiler için aynı zamanda başladığını unutmayın, bu nedenle **GetMessages** çağrısından itibaren 5 dakika geçtikten sonra silinmeyen tüm iletiler görünür olacaktır.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Kuyruk uzunluğu alma
Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz. **FetchAttributes** yöntemi, ileti sayısı dahil olmak üzere Kuyruk hizmetinden kuyruk özniteliklerini almasını ister. **ApproximateMessageCount** özelliği Kuyruk hizmetini çağırmadan **FetchAttributes** yöntemi tarafından alınan en son değeri döndürür.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Bir kuyruk silme
Bir kuyruğu ve içinde yer alan tüm iletileri silmek için kuyruk nesnesindeki **Sil** yöntemini çağırın.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```
    

## <a name="next-steps"></a>Sonraki adımlar
Kuyruk depolamanın temellerini öğrendiğinize göre, daha karmaşık depolama görevleri hakkında daha fazla bilgi edinmek için bu bağlantıları takip edin:

* Kullanılabilir API’ler ile ilgili eksiksiz bilgiler için Kuyruk hizmeti başvuru belgelerini görüntüleyin:
  * [.NET başvurusu için Depolama İstemci Kitaplığı](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [REST API başvurusu](http://msdn.microsoft.com/library/azure/dd179355)
* [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) kullanarak Azure Storage ile birlikte çalışmak üzere yazdığınız kodları nasıl sadeleştireceğinizi öğrenin.
* Azure’da veri depolama ile ilgili ek seçenekler hakkında daha fazla bilgi edinmek için daha fazla özellik kılavuzu görüntüleyin.
  * Yapılandırılmış verileri depolamak için [.NET kullanarak Azure Table Storage’ı kullanmaya başlayın](storage-dotnet-how-to-use-tables.md).
  * Yapılandırılmamış verileri depolamak için [.NET kullanarak Azure Blob Storage’ı kullanmaya başlayın](storage-dotnet-how-to-use-blobs.md).
  * İlişkisel verileri depolamak için [.NET (C#) kullanarak SQL Veritabanı'na bağlanın](../sql-database/sql-database-develop-dotnet-simple.md).

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: http://nuget.org/packages/System.Spatial/5.0.2


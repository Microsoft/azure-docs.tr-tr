---
title: Visual Studio kullanarak kuyruk depolamayı kullanmaya başlama (bulut Hizmetleri)
description: Visual Studio bağlı hizmetler 'i kullanarak bir depolama hesabına bağlandıktan sonra Visual Studio 'da bir bulut hizmeti projesinde Azure kuyruk depolamayı kullanmaya başlama
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603bb2b9a862ad4ed2cbde63e2d82b9a82fbeaa1
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298782"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Azure kuyruk depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (bulut hizmetleri projeleri)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Genel Bakış
Bu makalede, Visual Studio **bağlı hizmetler Ekle** iletişim kutusunu kullanarak bir bulut hizmetleri projesinde bir Azure depolama hesabı oluşturduktan veya başvurduktan sonra Visual Studio 'Da Azure kuyruk depolama hizmetini kullanmaya nasıl başlacağınız açıklanır.

Kodda bir sıranın nasıl oluşturulacağını göstereceğiz. Ayrıca, sıra iletilerini ekleme, değiştirme, okuma ve kaldırma gibi temel kuyruk işlemlerini nasıl gerçekleştireceğiniz de göstereceğiz. Örnekler C# kodda yazılır ve [.net Için Microsoft Azure depolama istemci kitaplığını](https://msdn.microsoft.com/library/azure/dn261237.aspx)kullanır.

**Bağlı hizmetler ekleme** işlemi, projenizdeki Azure depolama 'ya erişmek Için uygun NuGet paketlerini yükleyerek depolama hesabı için bağlantı dizesini proje yapılandırma dosyalarınıza ekler.

* Koddaki kuyrukları düzenleme hakkında daha fazla bilgi için bkz. [.NET kullanarak Azure kuyruk depolama ile çalışmaya başlama](../storage/queues/storage-dotnet-how-to-use-queues.md) .
* Azure depolama hakkında genel bilgi için bkz. [depolama belgeleri](https://azure.microsoft.com/documentation/services/storage/) .
* Azure Cloud Services hakkında genel bilgi için bkz. [Cloud Services belgeleri](https://azure.microsoft.com/documentation/services/cloud-services/) .
* ASP.NET uygulamalarını programlama hakkında daha fazla bilgi için bkz. [ASP.net](https://www.asp.net) .

Azure Kuyruk depolama, HTTP veya HTTPS kullanan kimlik doğrulaması yapılmış çağrılar aracılığıyla dünyanın her yerinden erişilebilen çok sayıda iletinin depolanması için bir hizmettir. Tek bir kuyruk iletisinin boyutu 64 KB’ye kadar olabilir ve bir kuyrukta, depolama hesabının toplam kapasite sınırına kadar milyonlarca ileti bulunabilir.

## <a name="access-queues-in-code"></a>Koddaki erişim kuyrukları
Visual Studio Cloud Services projelerindeki kuyruklara erişmek için, Azure kuyruk depolama 'ya erişen herhangi bir C# kaynak dosyasına aşağıdaki öğeleri eklemeniz gerekir.

1. C# Dosyanın en üstündeki ad alanı bildirimlerinin bu **using** deyimlerini içerdiğinden emin olun.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Depolama hesabı bilgilerinizi temsil eden bir **Cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama Bağlantı dizenizi ve depolama hesabı bilgilerinizi almak için aşağıdaki kodu kullanın.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Depolama hesabınızdaki sıra nesnelerine başvurmak için bir **Cloudqueueclient** nesnesi alın.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Belirli bir kuyruğa başvurmak için bir **Cloudqueue** nesnesi alın.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Note:** Aşağıdaki örneklerde kodun önünde yukarıdaki kodu kullanın.

## <a name="create-a-queue-in-code"></a>Kodda kuyruk oluşturma
Kuyruğu kodda oluşturmak için, **Createifnotexists**öğesine bir çağrı eklemeniz yeterlidir.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Kuyruğa ileti ekleme
Mevcut bir sıraya bir ileti eklemek için, yeni bir **Cloudqueuemessage** nesnesi oluşturun ve sonra **AddMessage** yöntemini çağırın.

**Cloudqueuemessage** nesnesi, bir DIZEDEN (UTF-8 biçiminde) ya da bir bayt dizisinden oluşturulabilir.

' Hello, World ' iletisini ekleyen bir örnek aşağıda verilmiştir.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Kuyruktaki bir iletiyi okuyun
**PeekMessage** yöntemini çağırarak iletiyi kuyruktan kaldırmadan kuyruğun önündeki iletiye göz atabilirsiniz.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Kuyruktaki bir iletiyi okuma ve kaldırma
Kodunuz, iki adımda bir kuyruktan bir ileti kaldırabilir (kuyruktan çıkarabilirsiniz).

1. Sıradaki bir sonraki iletiyi almak için **GetMessage** çağrısı yapın. **GetMessage**’dan dönen bir ileti bu kuyruktaki kod okuyan iletilere karşı görünmez olur. Varsayılan olarak bu ileti 30 saniye görünmez kalır.
2. İletiyi kuyruktan kaldırmayı tamamlaması için, **deleteMessage**' ı çağırın.

Bir iletinin iki adımlı kaldırılma süreci, donanım veya yazılım arızasından dolayı kodunuzun bir iletiyi işleyememesi durumunda kodunuzun başka bir örneğinin aynı iletiyi alıp yeniden denemesini sağlar. Aşağıdaki kod, ileti işlendikten hemen sonra **deleteMessage** yöntemini çağırır.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Sıra iletilerini işlemek ve kaldırmak için ek seçenekleri kullanın
İletilerin bir kuyruktan alınma şeklini iki yöntemle özelleştirebilirsiniz.

* Toplu bir ileti alabilirsiniz (en fazla 32).
* Daha uzun veya daha kısa görünürlük zaman aşımı ayarlayabilir, böylece her iletiyi tamamen işlemek için kodunuzun daha fazla veya daha az zaman aşımına uğramamasını sağlayabilirsiniz. Aşağıdaki kod örneğinde tek çağrıda 20 ileti almak için **GetMessages** yöntemi kullanılmıştır. Ardından her ileti bir **foreach** döngüsü ile işlenir. Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır. 5 dakikalık sürenin tüm iletiler için aynı zamanda başladığını unutmayın, bu nedenle **GetMessages** çağrısından itibaren 5 dakika geçtikten sonra silinmeyen tüm iletiler görünür olacaktır.

Bir örneği aşağıda verilmiştir:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Kuyruk uzunluğu alma
Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz. **FetchAttributes** yöntemi, ileti sayısı dahil olmak üzere Kuyruk hizmetinden kuyruk özniteliklerini almasını ister. **Beklenen Temethodcount** özelliği, kuyruk hizmeti çağrılmadan, **fetchattributes** yönteminin aldığı son değeri döndürür.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Ortak Azure kuyruğu API 'Leri ile Async-Await modelini kullanın
Bu örnek, ortak Azure kuyruğu API 'Leri ile Async-Await modelinin nasıl kullanılacağını gösterir. Örnek, verilen yöntemlerin her birinin zaman uyumsuz sürümünü çağırır, bu, her yöntemin **zaman uyumsuz** onarma sonrasında görülebilir. Zaman uyumsuz bir yöntem kullanıldığında, zaman uyumsuz-await deseninin, çağrı tamamlanana kadar yerel yürütmeyi askıya alır. Bu davranış, performans sorunlarını önlemeye yardımcı olan ve uygulamanızın genel yanıt hızını geliştiren geçerli iş parçacığının diğer işleri yapmasına izin verir. .NET’te Zaman Uyumsuz-Bekleme yönteminin kullanılması ile ilgili daha fazla ayrıntı için bkz. [Zaman Uyumsuz ve Bekleme (C# ve Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Bir kuyruk silme
Bir kuyruğu ve içinde yer alan tüm iletileri silmek için kuyruk nesnesindeki **Sil** yöntemini çağırın.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]


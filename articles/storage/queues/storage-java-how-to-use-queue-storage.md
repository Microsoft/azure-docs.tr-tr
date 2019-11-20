---
title: Java-Azure Storage 'da kuyruk depolama 'yı kullanma
description: Kuyruk depolama 'yı kullanarak kuyruklar oluşturup silme ve Java için Azure Storage istemci kitaplığı ile ileti ekleme, alma ve silme hakkında bilgi edinin.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/08/2016
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 17d02d8df96927aa506683fef94899e5c5114684
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839031"
---
# <a name="how-to-use-queue-storage-from-java"></a>Java’dan Kuyruk depolama kullanma

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

Bu kılavuzda, Azure kuyruk depolama hizmetini kullanarak genel senaryoları nasıl gerçekleştireceğiniz gösterilmektedir. Örnekler Java dilinde yazılır ve [Java Için Azure depolama SDK 'sını][Azure Storage SDK for Java]kullanır. Kapsanan senaryolar sıra iletilerini **ekleme**, göz **atma**, **alma**ve **silme** , Ayrıca kuyruk **oluşturma** ve **silme** içerir. Kuyruklar hakkında daha fazla bilgi için [sonraki adımlar](#next-steps) bölümüne bakın.

> [!NOTE]
> Android cihazlarda Azure Depolama kullanan geliştiriciler için bir SDK mevcuttur. Daha fazla bilgi için bkz. [Android Için Azure Storage SDK][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java uygulaması oluşturma

Bu kılavuzda, yerel olarak bir Java uygulaması içinde veya Azure 'da bir Web uygulaması içinde çalışan kodda çalıştırılabilen depolama özelliklerini kullanacaksınız.

Bunu yapmak için, Java Development Kit (JDK) yüklemeniz ve Azure aboneliğinizde bir Azure depolama hesabı oluşturmanız gerekir. Bu işlemi tamamladıktan sonra, geliştirme sisteminizin GitHub 'daki [Java Için Azure depolama SDK 'sı][Azure Storage SDK for Java] deposunda listelenen en düşük gereksinimleri ve bağımlılıkları karşıladığını doğrulamanız gerekir. Sisteminiz bu gereksinimleri karşılıyorsa, bu depodan sisteminizde Java için Azure depolama kitaplıklarını indirme ve yükleme yönergelerini takip edebilirsiniz. Bu görevleri tamamladıktan sonra, bu makaledeki örnekleri kullanan bir Java uygulaması oluşturabileceksiniz.

## <a name="configure-your-application-to-access-queue-storage"></a>Uygulamanızı kuyruk depolamaya erişecek şekilde yapılandırma

Aşağıdaki Import deyimlerini, kuyruklara erişmek için Azure depolama API 'Lerini kullanmak istediğiniz Java dosyasının en üstüne ekleyin:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure depolama bağlantı dizesi ayarlama

Azure depolama istemcisi, veri yönetimi hizmetlerine erişmek üzere uç noktaları ve kimlik bilgilerini depolamak için bir depolama bağlantı dizesi kullanır. Bir istemci uygulamada çalışırken, [AccountName](https://portal.azure.com) ve *AccountKey* için *Azure portalında* listelenen depolama hesabınızın adını ve depolama hesabı Birincil erişim anahtarını kullanarak depolama bağlantı dizesini aşağıdaki biçimde sağlamanız gerekir. Bu örnekte bağlantı dizesini tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Microsoft Azure bir rol içinde çalışan bir uygulamada, bu dize hizmet yapılandırma dosyasında *ServiceConfiguration. cscfg*' de depolanabilir ve **Roleenvironment. getconfigurationsettings** yöntemi çağrısıyla erişilebilir. Yapılandırma dosyasındaki **StorageConnectionString** adlı bir *Ayar* öğesinden bağlantı dizesini alma örneği aşağıda verilmiştir:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Aşağıdaki örnekler, depolama bağlantı dizesini almak için bu iki yöntemden birini kullandığınızı varsayar.

## <a name="how-to-create-a-queue"></a>Nasıl yapılır: kuyruk oluşturma
**Cloudqueueclient** nesnesi, kuyruklar için başvuru nesneleri almanızı sağlar. Aşağıdaki kod bir **Cloudqueueclient** nesnesi oluşturur. (Örneğin, **cloudstorageaccount** nesneleri oluşturmanın başka yolları vardır; daha fazla bilgi için bkz. [Azure Depolama İstemcisi SDK Başvurusu] **cloudstorageaccount** .)

Kullanmak istediğiniz kuyruğa bir başvuru almak için **Cloudqueueclient** nesnesini kullanın. Sıra yoksa kuyruğu oluşturabilirsiniz.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>Nasıl yapılır: bir kuyruğa ileti ekleme
Varolan bir sıraya bir ileti yerleştirmek için ilk olarak yeni bir **CloudQueueMessage** oluşturun. Sonra **AddMessage** yöntemini çağırın. Bir **Cloudqueuemessage** , bir DIZEDEN (UTF-8 biçiminde) ya da bir bayt dizisinden oluşturulabilir. Burada bir sıra (yoksa) oluşturan ve "Hello, World" iletisini ekleyen kod verilmiştir.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>Nasıl yapılır: bir sonraki iletiye göz atma
**PeekMessage**çağırarak bir kuyruğun ön tarafındaki iletiye göz atmayı sağlayabilirsiniz.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Nasıl yapılır: kuyruğa alınan iletinin içeriğini değiştirme
Kuyrukta yer alan bir iletinin içeriğini değiştirebilirsiniz. Eğer ileti bir iş görevini temsil ediyorsa, bu özelliği kullanarak iş görevinin durumunu güncelleştirebilirsiniz. Aşağıdaki kod kuyruk iletisini yeni içeriklerle güncelleştirir ve görünürlük zaman aşımını 60 saniye daha uzatır. Görünürlük zaman aşımını genişletmek iletiyle ilişkili çalışmanın durumunu kaydeder ve istemciye ileti üzerinde çalışmaya devam etmesi için başka bir dakika verir. Bir işleme adımı donanım veya yazılım arızasından dolayı başarısız olursa baştan başlamanıza gerek kalmadan kuyruk iletilerindeki çok adımlı iş akışlarını izlemek için bu yöntemi kullanabilirsiniz. Genellikle bir yeniden deneme sayacı tutmanı gerekir ve bir ileti *n* seferden daha fazla yeniden denenirse, silebilirsiniz. Bu, her işlendiğinde bir uygulama hatası tetikleyen bir iletiye karşı koruma sağlar.

Aşağıdaki kod örneği, ileti sırasını arar, içerik için "Hello, World" ile eşleşen ilk iletiyi bulur, ardından ileti içeriğini değiştirir ve çıkar.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Alternatif olarak, aşağıdaki kod örneği kuyruktaki yalnızca ilk görünür iletiyi günceller.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>Nasıl yapılır: kuyruk uzunluğunu alma
Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz. **Downloadattributes** yöntemi, bir kuyrukta kaç ileti olduğunu gösteren bir sayı dahil olmak üzere birkaç geçerli değer için kuyruk hizmeti ister. Bu sayı yalnızca, Kuyruk hizmeti isteğiniz yanıt verdiğinde iletiler eklenebildiğinden veya kaldırılacağından yaklaşık olur. **Getapproximatemessagecount** metodu, kuyruk hizmeti çağırılmadan **downloadattributes**çağrısının aldığı son değeri döndürür.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>Nasıl yapılır: sonraki iletiyi sıradan çıkarma
Kodunuz, iki adımda bir kuyruktan bir ileti sırasını kaldırır. **Retrievemessage**çağırdığınızda bir kuyruktaki sonraki iletiyi alırsınız. **Retrievemessage** 'ten döndürülen bir ileti, bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. Varsayılan olarak bu ileti 30 saniye görünmez kalır. İletiyi kuyruktan kaldırmayı tamamlaması için, **deleteMessage**' ı da çağırmanız gerekir. Bir iletinin iki adımlı kaldırılma süreci, donanım veya yazılım arızasından dolayı kodunuzun bir iletiyi işleyememesi durumunda kodunuzun başka bir örneğinin aynı iletiyi alıp yeniden denemesini sağlar. Kodunuz, ileti işlendikten hemen sonra **deleteMessage** ' i çağırır.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Dequeuing iletileri için ek seçenekler
İletilerin bir kuyruktan alınma şeklini iki yöntemle özelleştirebilirsiniz. İlk olarak toplu iletiler alabilirsiniz (en fazla 32). İkinci olarak daha uzun veya daha kısa bir görünmezlik süresi ayarlayarak kodunuzun her iletiyi tamamen işlemesi için daha az veya daha fazla zaman tanıyabilirsiniz.

Aşağıdaki kod örneği, tek bir çağrıda 20 ileti almak için **retrievemessages** yöntemini kullanır. Sonra her iletiyi bir **for** döngüsü kullanarak işler. Ayrıca, her ileti için geçersiz kılma zaman aşımını beş dakikaya (300 saniye) ayarlar. Beş dakika aynı anda tüm iletiler için başlar. bu nedenle, **retrievemessages**çağrısından bu yana beş dakika geçtiğinde, silinmeyen tüm iletiler yeniden görünür hale gelir.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>Nasıl yapılır: kuyrukları listeleme
Geçerli sıraların bir listesini almak için **Cloudqueueclient. listQueues ()** yöntemini çağırın ve bu, **cloudqueue** nesnelerinin bir koleksiyonunu döndürür.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>Nasıl yapılır: kuyruğu silme
Bir kuyruğu ve içerdiği tüm iletileri silmek için **Cloudqueue** nesnesinde **deleteifexists** yöntemini çağırın.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Sonraki adımlar
Sıra depolamanın temellerini öğrendiğinize göre, daha karmaşık depolama görevleri hakkında bilgi edinmek için bu bağlantıları izleyin.

* [Java için Azure depolama SDK 'Sı][Azure Storage SDK for Java]
* [Azure Depolama İstemcisi SDK Başvurusu][Azure Storage Client SDK Reference]
* [Azure Depolama Hizmetleri REST API'si][Azure Storage Services REST API]
* [Azure Depolama Ekibi Blog’u][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Depolama İstemcisi SDK Başvurusu]: https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

---
title: Java-Azure Storage 'da kuyruk depolama 'yı kullanma
description: Kuyruk depolamayı kullanarak kuyruklar oluşturma ve silme hakkında bilgi edinin. Java için Azure Storage istemci kitaplığı ile ileti eklemeyi, Peek, almak ve silmek hakkında bilgi edinin.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 5ea00adacff1f76eb5ec81728e3a11f703a5fe8c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276272"
---
# <a name="how-to-use-queue-storage-from-java"></a>Java 'da kuyruk depolama 'yı kullanma

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Genel Bakış

Bu kılavuzda, Azure kuyruk depolama hizmetini kullanarak yaygın senaryolar için nasıl kod yapılacağı gösterilir. Örnekler Java dilinde yazılır ve [Java için Azure Depolama SDK’sı](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage) kullanır. Senaryolar, sıra iletilerini **ekleme**, göz **atma**, **alma** ve **silmeyi** içerir. Sıra **oluşturma** ve **silme** için kod de ele alınmıştır. Kuyruklar hakkında daha fazla bilgi için [sonraki adımlar](#next-steps) bölümüne bakın.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java uygulaması oluşturma

# <a name="java-v12"></a>[Java V12](#tab/java)

İlk olarak, geliştirme sisteminizin [Java Için Azure kuyruk depolama istemci kitaplığı V12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)' nda listelenen önkoşulları karşıladığını doğrulayın.

Adında bir Java uygulaması oluşturmak için `queues-how-to-v12` :

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), Maven kullanarak ada sahip yeni bir konsol uygulaması oluşturun `queues-how-to-v12` . `mvn`Bir "Hello World" Java projesi oluşturmak için aşağıdaki komutu yazın.

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

1. Projenin üretilme çıktısı şuna benzer görünmelidir:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created `queues-howto-v12` directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>Paketi yükler

`pom.xml`Dosyayı metin düzenleyicinizde açın. Aşağıdaki bağımlılık öğesini bağımlılıklar grubuna ekleyin.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[Java V8](#tab/java8)

İlk olarak, geliştirme sisteminizin [Java V8 Için Azure depolama SDK 'sında](https://github.com/azure/azure-storage-java)listelenen önkoşulları karşıladığını doğrulayın. Java için Azure depolama kitaplıklarını indirme ve yükleme yönergelerini izleyin. Daha sonra bu makaledeki örnekleri kullanarak bir Java uygulaması oluşturabilirsiniz.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Uygulamanızı kuyruk depolamaya erişecek şekilde yapılandırma

Aşağıdaki Import deyimlerini, kuyruklara erişmek için Azure depolama API 'Lerini kullanmak istediğiniz Java dosyasının en üstüne ekleyin:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Depolama bağlantı dizesini ayarlama

Azure Storage istemcisi, veri yönetimi hizmetlerine erişmek için bir depolama bağlantı dizesi kullanır. [Azure Portal](https://portal.azure.com)listelenen depolama hesabınız için bir ad ve birincil erişim anahtarı alın. Bunları `AccountName` `AccountKey` bağlantı dizesindeki ve değerleri olarak kullanın. Bu örnekte bağlantı dizesini tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Bu dizeyi adlı hizmet yapılandırma dosyasında saklayabilirsiniz `ServiceConfiguration.cscfg` . Microsoft Azure rolü içinde çalışan bir uygulama için, çağırarak bağlantı dizesine erişin `RoleEnvironment.getConfigurationSettings` . Aşağıda adlı bir öğeden bağlantı dizesi alma örneği verilmiştir `Setting` `StorageConnectionString` :

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

Aşağıdaki örnekler, `String` depolama bağlantı dizesi içeren bir nesneniz olduğunu varsayar.

## <a name="how-to-create-a-queue"></a>Nasıl yapılır: kuyruk oluşturma

# <a name="java-v12"></a>[Java V12](#tab/java)

Bir `QueueClient` nesne, bir kuyrukla etkileşim kurma işlemlerini içerir. Aşağıdaki kod bir nesnesi oluşturur `QueueClient` . Kullanmak istediğiniz `QueueClient` kuyruğu oluşturmak için nesnesini kullanın.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Bir `CloudQueueClient` nesne, kuyruklar için başvuru nesneleri almanızı sağlar. Aşağıdaki kod, `CloudQueueClient` kullanmak istediğiniz kuyruğun başvurusunu sağlayan bir nesnesi oluşturur. Sıra yoksa kuyruğu oluşturabilirsiniz.

> [!NOTE]
> Nesne oluşturmanın diğer yolları vardır `CloudStorageAccount` . Daha fazla bilgi için bkz `CloudStorageAccount` . [Azure Storage istemcisi SDK başvurusu](https://azure.github.io/azure-sdk-for-java/storage.html).)

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

---

## <a name="how-to-add-a-message-to-a-queue"></a>Nasıl yapılır: bir kuyruğa ileti ekleme

# <a name="java-v12"></a>[Java V12](#tab/java)

Mevcut bir sıraya bir ileti eklemek için `sendMessage` yöntemini çağırın. İleti bir dize (UTF-8 biçiminde) ya da bir bayt dizisi olabilir. Sıraya bir dize iletisi gönderen kod aşağıda verilmiştir.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Mevcut bir kuyruğa ileti eklemek için ilk olarak yeni bir oluştur `CloudQueueMessage` . Sonra, yöntemini çağırın `addMessage` . Bir `CloudQueueMessage` dizeden (UTF-8 biçiminde) ya da bir bayt dizisinden oluşturulabilir. Bir sıra (yoksa) oluşturan ve iletiyi ekleyen kod aşağıda verilmiştir `Hello, World` .

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

---

## <a name="how-to-peek-at-the-next-message"></a>Nasıl yapılır: bir sonraki iletiye göz atma

' İ çağırarak bir kuyruğun önündeki iletiye göz atmayı sağlayabilirsiniz `peekMessage` .

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

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

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Nasıl yapılır: kuyruğa alınan iletinin içeriğini değiştirme

Kuyrukta yer alan bir iletinin içeriğini değiştirebilirsiniz. İleti bir iş görevini temsil ediyorsa, durumu güncelleştirmek için bu özelliği kullanabilirsiniz. Aşağıdaki kod, yeni içerikle bir kuyruk iletisini güncelleştirir ve görünürlük zaman aşımını 30 saniye daha uzatmak için ayarlar. Görünürlük zaman aşımını genişletmek, istemciye ileti üzerinde çalışmaya devam etmesi için bir 30 saniye daha verir. Yeniden deneme sayısı da tutabilirsiniz. İleti *n* kereden fazla yeniden denenip onu silersiniz. Bu senaryo, her işlendiği zaman bir uygulama hatasını tetikleyen bir iletiye karşı koruma sağlar.

# <a name="java-v12"></a>[Java V12](#tab/java)

Aşağıdaki kod örneği, ileti sırasını arar, bir arama dizesiyle eşleşen ilk ileti içeriğini bulur, ileti içeriğini değiştirir ve çıkar.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Aşağıdaki kod örneği, ileti kuyruğu boyunca arama yapar, eşleşen ilk ileti içeriğini bulur `Hello, world` , ileti içeriğini değiştirir ve çıkar.

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

---

Aşağıdaki kod örneği, kuyruktaki yalnızca ilk görünen iletiyi güncelleştirir.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

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

---

## <a name="how-to-get-the-queue-length"></a>Nasıl yapılır: kuyruk uzunluğunu alma

Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz.

# <a name="java-v12"></a>[Java V12](#tab/java)

`getProperties`Yöntemi, şu anda bir kuyruktaki ileti sayısı dahil olmak üzere birkaç değer döndürür. Sayım yalnızca, isteğiniz sonunda iletiler eklenebildiğinden veya kaldırılacağından yaklaşık olur. `getApproximateMessageCount`Yöntemi, `getProperties` kuyruk depolaması çağrılmadan, çağrısının aldığı son değeri döndürür.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

`downloadAttributes`Yöntemi, şu anda bir kuyruktaki ileti sayısı dahil olmak üzere birkaç değer alır. Sayım yalnızca, isteğiniz sonunda iletiler eklenebildiğinden veya kaldırılacağından yaklaşık olur. `getApproximateMessageCount`Yöntemi, `downloadAttributes` kuyruk depolaması çağrılmadan, çağrısının aldığı son değeri döndürür.

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

---

## <a name="how-to-dequeue-the-next-message"></a>Nasıl yapılır: sonraki iletiyi sıradan çıkarma

# <a name="java-v12"></a>[Java V12](#tab/java)

Kodunuz, iki adımda bir kuyruktan bir ileti sırasını kaldırır. `receiveMessage`' İ çağırdığınızda bir kuyruktaki sonraki iletiyi alırsınız. Öğesinden döndürülen bir ileti, `receiveMessage` Bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. Varsayılan olarak bu ileti 30 saniye görünmez kalır. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, öğesini de çağırmanız gerekir `deleteMessage` . Kodunuz bir iletiyi işleyemezse, bu iki adımlı işlem aynı iletiyi almanızı ve yeniden denemeyi sağlar. `deleteMessage`İleti işlendikten sonra kodunuz doğru şekilde çağırır.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Kodunuz, iki adımda bir kuyruktan bir ileti sırasını kaldırır. `retrieveMessage`' İ çağırdığınızda bir kuyruktaki sonraki iletiyi alırsınız. Öğesinden döndürülen bir ileti, `retrieveMessage` Bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. Varsayılan olarak bu ileti 30 saniye görünmez kalır. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, öğesini de çağırmanız gerekir `deleteMessage` . Kodunuz bir iletiyi işleyemezse, bu iki adımlı işlem aynı iletiyi almanızı ve yeniden denemeyi sağlar. `deleteMessage`İleti işlendikten sonra kodunuz doğru şekilde çağırır.

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

---

## <a name="additional-options-for-dequeuing-messages"></a>Dequeuing iletileri için ek seçenekler

İleti alımı bir kuyruktan özelleştirmenin iki yolu vardır. İlk olarak, bir toplu ileti alın (32 'e kadar). İkincisi, daha uzun veya daha kısa bir zaman aşımı ayarlayın, böylece kodunuzun her iletiyi tamamen işlemesi için daha fazla veya daha az zaman.

# <a name="java-v12"></a>[Java V12](#tab/java)

Aşağıdaki kod örneği, `receiveMessages` tek bir çağrıda 20 ileti almak için yöntemini kullanır. Ardından, her iletiyi bir döngü kullanarak işler `for` . Ayrıca, her ileti için geçersiz kılma zaman aşımını beş dakikaya (300 saniye) ayarlar. Zaman aşımı, tüm iletiler için aynı anda başlar. Çağrısından bu yana beş dakika geçtiğinde `receiveMessages` , silinmeyen iletiler yeniden görünür hale gelir.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Aşağıdaki kod örneği, `retrieveMessages` tek bir çağrıda 20 ileti almak için yöntemini kullanır. Ardından, her iletiyi bir döngü kullanarak işler `for` . Ayrıca, her ileti için geçersiz kılma zaman aşımını beş dakikaya (300 saniye) ayarlar. Zaman aşımı, tüm iletiler için aynı anda başlar. Çağrısından bu yana beş dakika geçtiğinde `retrieveMessages` , silinmeyen iletiler yeniden görünür hale gelir.

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

---

## <a name="how-to-list-the-queues"></a>Nasıl yapılır: kuyrukları listeleme

# <a name="java-v12"></a>[Java V12](#tab/java)

Geçerli sıraların bir listesini almak için, `QueueServiceClient.listQueues()` bir nesne koleksiyonu döndürecek yöntemi çağırın `QueueItem` .

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Geçerli sıraların bir listesini almak için, `CloudQueueClient.listQueues()` bir nesne koleksiyonu döndürecek yöntemi çağırın `CloudQueue` .

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

---

## <a name="how-to-delete-a-queue"></a>Nasıl yapılır: kuyruğu silme

# <a name="java-v12"></a>[Java V12](#tab/java)

Bir kuyruğu ve içerdiği tüm iletileri silmek için `delete` nesnesi üzerinde yöntemini çağırın `QueueClient` .

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Bir kuyruğu ve içerdiği tüm iletileri silmek için `deleteIfExists` nesnesi üzerinde yöntemini çağırın `CloudQueue` .

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

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Sonraki adımlar

Sıra depolamanın temellerini öğrendiğinize göre, daha karmaşık depolama görevleri hakkında bilgi edinmek için bu bağlantıları izleyin.

- [Java için Azure Depolama SDK'sı](https://github.com/Azure/Azure-SDK-for-Java)
- [Azure depolama istemci SDK 'Sı başvurusu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [Azure depolama hizmetleri REST API](/rest/api/storageservices/)
- [Azure depolama ekibi blogu](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)

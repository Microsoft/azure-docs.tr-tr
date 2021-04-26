---
title: 'Hızlı başlangıç: Azure kuyruk depolama istemci kitaplığı V12-Java'
description: Bir kuyruk oluşturmak ve bu iletiye ileti eklemek için Azure kuyruk depolama istemci kitaplığı V12 ' nı nasıl kullanacağınızı öğrenin. Sonra sıradan iletileri okumayı ve silmeyi öğrenin. Ayrıca, bir kuyruğu silmeyi de öğreneceksiniz.
author: twooley
ms.author: twooley
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: f4e33cac6ba00be56b0f63cf5a10b2dce32e1be7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534468"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Hızlı başlangıç: Java için Azure kuyruk depolama istemci kitaplığı V12

Java için Azure kuyruk depolama istemci kitaplığı V12 ile çalışmaya başlayın. Azure kuyruk depolaması, daha sonra almak ve işlemek üzere çok sayıda ileti depolamaya yönelik bir hizmettir. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere bu adımları izleyin.

Java için Azure kuyruk depolama istemci kitaplığı V12 ' nı kullanarak şunları yapın:

- Bir kuyruk oluşturma
- Bir kuyruğa ileti ekleme
- Kuyruktaki iletilere göz atın
- Kuyruktaki bir iletiyi güncelleştirme
- Kuyruktaki iletileri alma ve silme
- Bir kuyruk silme

Ek kaynaklar:

- [API başvuru belgeleri](/java/api/overview/azure/storage-queue-readme)
- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Paket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Örnekler](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Önkoşullar

- [Java Development Kit (JDK)](/java/azure/jdk/) sürüm 8 veya üstü
- [Apache Maven](https://maven.apache.org/download.cgi)
- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- Azure depolama hesabı- [depolama hesabı oluşturma](../common/storage-account-create.md)

## <a name="setting-up"></a>Ayarlanıyor

Bu bölüm, Java için Azure kuyruk depolama istemci kitaplığı V12 ile çalışmak üzere bir proje hazırlama konusunda size yol gösterir.

### <a name="create-the-project"></a>Proje oluşturma

Adlı bir Java uygulaması oluşturun `queues-quickstart-v12` .

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), Maven kullanarak ada sahip yeni bir konsol uygulaması oluşturun `queues-quickstart-v12` . `mvn`Bir "Hello World" Java projesi oluşturmak için aşağıdaki komutu yazın.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.quickstart `
        --define artifactId=queues-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.quickstart \
        --define artifactId=queues-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

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
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Yeni oluşturulan dizine geçiş yapın `queues-quickstart-v12` .

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Paketi yükler

`pom.xml`Dosyayı metin düzenleyicinizde açın. Aşağıdaki bağımlılık öğesini bağımlılıklar grubuna ekleyin.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Proje dizininden:

1. `/src/main/java/com/queues/quickstart`Dizine git
1. `App.java`Dosyayı Düzenleyicinizde açın
1. İfadeyi sil `System.out.println("Hello, world");`
1. `import`Yönergeler ekleme

Kod şu şekildedir:

```java
package com.queues.quickstart;

/**
 * Azure Queue Storage client library v12 quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Nesne modeli

Azure Kuyruk Depolama, çok sayıda iletiyi depolamaya yönelik bir hizmettir. Kuyruk iletisi boyutu 64 KB 'ye kadar olabilir. Bir kuyruk, depolama hesabının toplam kapasite sınırına kadar milyonlarca ileti içerebilir. Kuyruklar genellikle zaman uyumsuz olarak işlenecek iş biriktirme listesi oluşturmak için kullanılır. Kuyruk depolama, üç tür kaynak sunar:

- Depolama hesabı
- Depolama hesabındaki bir kuyruk
- Kuyruktaki iletiler

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![Kuyruk depolama mimarisi diyagramı](./media/storage-queues-introduction/queue1.png)

Şu kaynaklarla etkileşim kurmak için aşağıdaki Java sınıflarını kullanın:

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): `QueueClientBuilder` Sınıfı bir nesneyi yapılandırır ve başlatır `QueueClient` .
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient):, `QueueServiceClient` Depolama hesabınızdaki tüm kuyrukları yönetmenizi sağlar.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): `QueueClient` Sınıfı tek bir kuyruğu ve iletilerini yönetmenizi ve düzenlemenizi sağlar.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): `QueueMessageItem` Sınıfı, bir sıraya çağrılırken döndürülen ayrı nesneleri temsil eder [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) .

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, Java için Azure kuyruk depolama istemci kitaplığı ile aşağıdaki eylemleri nasıl yapılacağını gösterir:

- [Bağlantı dizesini alma](#get-the-connection-string)
- [Bir kuyruk oluşturma](#create-a-queue)
- [Bir kuyruğa ileti ekleme](#add-messages-to-a-queue)
- [Kuyruktaki iletilere göz atın](#peek-at-messages-in-a-queue)
- [Kuyruktaki bir iletiyi güncelleştirme](#update-a-message-in-a-queue)
- [Kuyruktaki iletileri alma ve silme](#receive-and-delete-messages-from-a-queue)
- [Bir kuyruk silme](#delete-a-queue)

### <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Aşağıdaki kod, depolama hesabı için bağlantı dizesini alır. Bağlantı dizesi, [depolama Bağlantı dizenizi yapılandırma](#configure-your-storage-connection-string) bölümünde oluşturulan ortam değişkenini saklı tutulur.

Bu kodu metodun içine ekleyin `main` :

```java
System.out.println("Azure Queue Storage client library v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Bir kuyruk oluşturma

Yeni sıra için bir ad belirleyin. Aşağıdaki kod, benzersiz olduğundan emin olmak için kuyruk adına bir GUID değeri ekler.

> [!IMPORTANT]
> Kuyruk adları yalnızca küçük harf, sayı ve kısa çizgi içerebilir ve bir harf veya sayı ile başlamalıdır. Her kısa çizginin önünde ve arkasında kısa çizgi dışında bir karakter bulunmalıdır. Ad ayrıca 3 ila 63 karakter uzunluğunda olmalıdır. Adlandırma sıraları hakkında daha fazla bilgi için bkz. [adlandırma sıraları ve meta verileri](/rest/api/storageservices/naming-queues-and-metadata).

Sınıfının bir örneğini oluşturun [`QueueClient`](/java/api/com.azure.storage.queue.queueclient) . Ardından, [`Create`](/java/api/com.azure.storage.queue.queueclient.create) depolama hesabınızda kuyruğu oluşturmak için yöntemini çağırın.

Bu kodu yönteminin sonuna ekleyin `main` :

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Bir kuyruğa ileti ekleme

Aşağıdaki kod parçacığı, yöntemini çağırarak kuyruğa ileti ekler [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) . Ayrıca, [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) bir çağrıdan döndürülen bir `sendMessage` çağrı kaydeder. Sonuç, programın ilerleyen kısımlarında iletiyi güncelleştirmek için kullanılır.

Bu kodu yönteminin sonuna ekleyin `main` :

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Kuyruktaki iletilere göz atın

Yöntemi çağırarak kuyruktaki iletilere göz atın [`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) . Bu yöntem, sıranın önüne bir veya daha fazla ileti alır ancak iletinin görünürlüğünü değiştirmez.

Bu kodu yönteminin sonuna ekleyin `main` :

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Kuyruktaki bir iletiyi güncelleştirme

Yöntemini çağırarak bir iletinin içeriğini güncelleştirin [`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) . Bu yöntem, bir iletinin görünürlük zaman aşımını ve içeriğini değiştirebilir. İleti içeriği, boyutu 64 KB 'a kadar olan bir UTF-8 kodlu dize olmalıdır. İletinin yeni içeriğiyle birlikte, `SendMessageResult` kodda daha önce kaydedilen öğesini kullanarak ILETI kimliğini ve pop alındı bilgisini geçirin. İleti KIMLIĞI ve pop alındısı hangi iletinin güncelleştirilecek olduğunu belirler.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Kuyruktaki iletileri alma ve silme

Yöntemini çağırarak önceden eklenmiş iletileri indirin [`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) . Örnek kod aynı zamanda iletileri alındıktan ve işlendikten sonra kuyruktan de siler. Bu durumda, işleme yalnızca konsolda iletiyi görüntülüyor.

Uygulama, `System.console().readLine();` iletileri almadan ve silmeden önce çağırarak kullanıcı girişi için duraklatılır. [Azure Portal](https://portal.azure.com) , kaynakların silinmeden önce doğru şekilde oluşturulduğunu doğrulayın. Açıkça silinmeyen tüm iletiler, daha sonra bu işlemleri işlemek için bir süre sonra sırada görünür hale gelir.

Bu kodu yönteminin sonuna ekleyin `main` :

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Bir kuyruk silme

Aşağıdaki kod, yöntemi kullanılarak sıranın silindiği, uygulamanın oluşturduğu kaynakları temizler [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete) .

Bu kodu yönteminin sonuna ekleyin `main` :

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama, bir Azure kuyruğuna üç ileti oluşturur ve ekler. Kod kuyruktaki iletileri listeler, ardından kuyruğu silmeden önce bunları alır ve siler.

Konsol pencerenizde uygulama dizininize gidip uygulamayı derleyin ve çalıştırın.

```console
mvn compile
```

Ardından, paketini oluşturun.

```console
mvn package
```

`mvn`Uygulamayı çalıştırmak için aşağıdaki komutu kullanın.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Azure Queue Storage client library v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

Uygulama iletileri almadan önce durakladığında, [Azure Portal](https://portal.azure.com)depolama hesabınızı kontrol edin. İletilerin kuyrukta olduğunu doğrulayın.

`Enter`İletileri almak ve silmek için tuşuna basın. İstendiğinde, `Enter` kuyruğu silmek ve tanıtımı sona almak için tuşa basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir kuyruk oluşturmayı ve Java kodunu kullanarak buna ileti eklemeyi öğrendiniz. Ardından iletileri göz atmayı, almayı ve silmeyi öğrendiniz. Son olarak, bir ileti sırasının nasıl silineceğini öğrendiniz.

Öğreticiler, örnekler, hızlı bir şekilde ve diğer belgeler için şu adresi ziyaret edin:

> [!div class="nextstepaction"]
> [Java bulut geliştiricileri için Azure](/azure/developer/java/)

- Daha fazla Azure kuyruk depolama örneği uygulaması için bkz. [Java Için Azure kuyruk depolama istemci kitaplığı V12-örnekler](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).

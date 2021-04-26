---
title: Java ile Azure Service Bus kuyruklarını kullanma (Azure-mesajlaşma-ServiceBus)
description: Bu öğreticide, Java kullanarak Azure Service Bus kuyruğundan ileti gönderme ve iletileri alma hakkında bilgi edineceksiniz. Yeni Azure-mesajlaşma-ServiceBus paketini kullanın.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: e3998e812bb921ff8bea5e83199bc4e142ed2e83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533426"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Azure Service Bus kuyruklarından ileti gönderme ve iletileri alma (Java)
Bu hızlı başlangıçta, bir Azure Service Bus sırasından ileti göndermek ve ileti almak için bir Java uygulaması oluşturacaksınız. 

> [!IMPORTANT]
> Bu hızlı başlangıç, yeni Azure-mesajlaşma-ServiceBus paketini kullanır. Eski Azure-ServiceBus paketini kullanan bir hızlı başlangıç için bkz. [Azure-ServiceBus kullanarak Ileti gönderme ve alma](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Birlikte çalışmak için bir kuyruğunuz yoksa, bir kuyruk oluşturmak için [Service Bus kuyruğu oluşturmak üzere Azure Portal kullanma](service-bus-quickstart-portal.md) adımlarını izleyin. Service Bus ad alanınız ve oluşturduğunuz **kuyruğun** adı için **bağlantı dizesini** aklınızda edin.
- [Java Için Azure SDK][Azure SDK for Java]'yı yükler. Çakışan Küreler kullanıyorsanız, Java için Azure SDK 'sını içeren [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] yükleyebilirsiniz. Ardından, projenize **Java için Microsoft Azure kitaplıklarını** ekleyebilirsiniz. IntelliJ kullanıyorsanız, bkz. [Azure Toolkit for IntelliJ yüklemesi](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Kuyruğa ileti gönderme
Bu bölümde, bir Java konsol projesi oluşturacak ve daha önce oluşturduğunuz kuyruğa ileti göndermek için kod ekleyeceksiniz. 

### <a name="create-a-java-console-project"></a>Java konsol projesi oluşturma
Çakışan Küreler veya tercih ettiğiniz bir aracı kullanarak bir Java projesi oluşturun. 

### <a name="configure-your-application-to-use-service-bus"></a>Uygulamanızı kullanmak için yapılandırma Service Bus
Azure Core ve Azure Service Bus kitaplıklarına başvurular ekleyin. 

Çakışan Küreler kullanıyorsanız ve Java konsol uygulaması oluşturduysanız, Java projenizi Maven 'e dönüştürün: **Paket Gezgini** penceresinde projeye sağ tıklayın,   ->  **Maven projesine dönüştürmeyi** Yapılandır ' ı seçin. Ardından, aşağıdaki örnekte gösterildiği gibi bu iki kitaplıklara de bağımlılıklar ekleyin.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Kuyruğa ileti göndermek için kod ekleme
1. `import`Java dosyasının konusunun aşağıdaki deyimlerini ekleyin. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Sınıfında aşağıda gösterildiği gibi bağlantı dizesini ve sıra adını tutacak değişkenleri tanımlayın: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    `<NAMESPACE CONNECTION STRING>`Service Bus ad alanınız için bağlantı dizesiyle değiştirin. Ve öğesini `<QUEUE NAME>` kuyruğun adıyla değiştirin.
3. `sendMessage`Kuyruğa bir ileti göndermek için sınıfında adlı bir yöntem ekleyin. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. `createMessages`İleti listesi oluşturmak için sınıfında adlı bir yöntem ekleyin. Genellikle, bu iletileri uygulamanızın farklı bölümlerinden alırsınız. Burada, örnek mesajların bir listesini oluşturacağız.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. `sendMessageBatch`Oluşturduğunuz sıraya ileti göndermek için yöntemi adlı bir yöntem ekleyin. Bu yöntem `ServiceBusSenderClient` kuyruk için bir oluşturur, `createMessages` ileti listesini almak için yöntemini çağırır, bir veya daha fazla toplu işi hazırlar ve toplu işleri kuyruğa gönderir. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-queue"></a>Kuyruktan ileti alma
Bu bölümde, kuyruktan ileti almak için kod ekleyeceksiniz. 

1. `receiveMessages`Sıradan ileti almak için adlı bir yöntem ekleyin. Bu yöntem `ServiceBusProcessorClient` , iletileri işlemek için bir işleyici ve hataları işlemek için başka bir tane belirterek kuyruk için bir oluşturur. Ardından, işlemciyi başlatır, birkaç saniye bekler, alınan iletileri yazdırır ve sonra işlemciyi durdurup kapatır.

    > [!IMPORTANT]
    > `QueueTest` `QueueTest::processMessage` Kodunuzda, sınıfınızın adıyla değiştirin. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }   
    ```
2. `processMessage`Service Bus aboneliğinden alınan bir iletiyi işlemek için yöntemini ekleyin. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. `processError`Hata iletilerini işlemek için yöntemini ekleyin.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
2. `main` `sendMessage` , `sendMessageBatch` , Ve `receiveMessages` yöntemlerini çağırmak ve throw metodunu güncelleştirin `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Uygulamayı çalıştırma
Uygulamayı çalıştırdığınızda, konsol penceresinde aşağıdaki iletileri görürsünüz. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

Azure portal Service Bus ad alanının **genel bakış** sayfasında **gelen** ve **giden** ileti sayısını görebilirsiniz. Bir dakika beklemeniz gerekebilir ve en son değerleri görmek için sayfayı yenilemeniz gerekebilir. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Gelen ve giden ileti sayısı" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

**Service Bus kuyruğu** sayfasına gitmek Için bu **genel bakış** sayfasında kuyruğu seçin. Bu sayfada **gelen** ve **giden** ileti sayısını da görürsünüz. Ayrıca, sıranın **geçerli boyutu** , **en büyük boyut**, **etkin ileti sayısı** vb. gibi diğer bilgileri de görürsünüz. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Kuyruk ayrıntıları" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Sonraki Adımlar
Aşağıdaki belgelere ve örneklere bakın:

- [Java için Azure Service Bus istemci kitaplığı-Benioku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub 'daki örnekler](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API'si başvurusu](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

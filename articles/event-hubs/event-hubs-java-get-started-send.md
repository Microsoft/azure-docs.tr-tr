---
title: Java kullanarak Azure Event Hubs olay gönderme veya alma (en son)
description: Bu makalede, Azure Event Hubs en son Azure-Messaging-eventhubs paketini kullanarak olayları gönderen/alan bir Java uygulaması oluşturmaya yönelik izlenecek yol sunulmaktadır.
ms.topic: quickstart
ms.date: 04/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: bae0d4147fddf57398d494ca7f13c347f892e45e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448449"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Azure Event Hubs (Azure-Messaging-eventhubs) olay göndermek veya olayları almak için Java 'Yı kullanma
Bu hızlı başlangıçta, **Azure-Messaging-eventhubs** Java paketini kullanarak Olay Hub 'ından olayları gönderme ve olayları alma işlemlerinin nasıl yapılacağı gösterilir.

> [!IMPORTANT]
> Bu hızlı başlangıç, yeni **Azure-Messaging-eventhubs** paketini kullanır. Eski **Azure-eventhubs** ve **Azure-eventhubs-EPH** paketlerini kullanan bir hızlı başlangıç için bkz. [Azure-eventhubs ve Azure-eventhubs-EPH kullanarak olay gönderme ve alma](event-hubs-java-get-started-send-legacy.md). 


## <a name="prerequisites"></a>Önkoşullar
Azure Event Hubs yeni başladıysanız, bu hızlı başlangıcı uygulamadan önce [Event Hubs genel bakış](event-hubs-about.md) bölümüne bakın. 

Bu hızlı başlangıcı tamamlayabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

- **Microsoft Azure aboneliği**. Azure Event Hubs dahil olmak üzere Azure hizmetlerini kullanmak için bir aboneliğiniz olması gerekir.  Mevcut bir Azure hesabınız yoksa, [ücretsiz deneme](https://azure.microsoft.com/free/) için kaydolabilir veya [BIR hesap oluştururken](https://azure.microsoft.com)MSDN abonesi avantajlarınızı kullanabilirsiniz.
- Bir Java geliştirme ortamı. Bu hızlı başlangıç, [tutulma](https://www.eclipse.org/)kullanır. Java Development Kit (JDK) sürümü 8 veya üzeri gereklidir. 
- **Event Hubs bir ad alanı ve bir olay hub 'ı oluşturun**. İlk adım, Event Hubs türünde bir ad alanı oluşturmak için [Azure Portal](https://portal.azure.com) ve uygulamanızın Olay Hub 'ı ile iletişim kurması için gereken yönetim kimlik bilgilerini elde etmek için kullanılır. Bir ad alanı ve Olay Hub 'ı oluşturmak için [Bu makaledeki](event-hubs-create.md)yordamı izleyin. Ardından, makalenin yönergelerini izleyerek **Event Hubs ad alanı için bağlantı dizesini** alın: [bağlantı dizesi al](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Bağlantı dizesini daha sonra bu hızlı başlangıçta kullanacaksınız.

## <a name="send-events"></a>Olayları gönderme 
Bu bölümde, Olay Hub 'ına olay göndermek için bir Java uygulaması oluşturma gösterilmektedir. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs kitaplığı 'na başvuru ekleme

Event Hubs için Java istemci kitaplığı, [Maven merkezi deposunda](https://search.maven.org/search?q=a:azure-messaging-eventhubs)bulunabilir. Bu kitaplığa, Maven proje dosyanızda aşağıdaki bağımlılık bildirimini kullanarak başvurabilirsiniz:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.6.0</version>
</dependency>
```

> [!NOTE]
> Sürümü, Maven deposunda yayınlanan en son sürüme güncelleştirin. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Olay hub'ına ileti göndermek için kod yazma

Aşağıdaki örnek için önce en sevdiğiniz Java geliştirme ortamında bir konsol/kabuk uygulaması için yeni bir Maven projesi oluşturun. Adlı bir sınıf ekleyin `Sender` ve aşağıdaki kodu sınıfına ekleyin:

> [!IMPORTANT]
> `<Event Hubs namespace connection string>`Event Hubs ad alanınız için bağlantı dizesiyle güncelleştirin. `<Event hub name>`Ad alanındaki Olay Hub 'ınızın adıyla güncelleştirin. 

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```
### <a name="add-code-to-publish-events-to-the-event-hub"></a>Olayları Olay Hub 'ına yayımlamak için kod ekleme
`publishEvents` `Sender` Aşağıda gösterildiği gibi sınıfa adlı bir yöntem ekleyin. 

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the event data is bigger than max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));
        
        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

Programı oluşturun ve hata olmadığından emin olun. Alıcı programını çalıştırdıktan sonra bu programı çalıştırırsınız. 

## <a name="receive-events"></a>Olayları alma
Bu öğreticideki kod, [GitHub 'Daki Eventprocessorclient örneğine](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java)dayalıdır ve bu, tam çalışma uygulamasını görmek için inceleyebilirsiniz.

> [!WARNING]
> Bu kodu Azure Stack hub 'da çalıştırırsanız, belirli bir depolama API sürümünü hedefetmediğiniz takdirde çalışma zamanı hatalarıyla karşılaşırsınız. Bunun nedeni, Event Hubs SDK 'sının Azure 'da kullanılabilen ve Azure Stack hub platformunda kullanılamayan en son Azure Storage API 'sini kullanması nedeniyle oluşur. Azure Stack hub, Azure 'da genel kullanıma sunulan farklı bir Storage blob SDK sürümü destekleyebilir. Azure blog depolamayı bir denetim noktası deposu olarak kullanıyorsanız, [Azure Stack hub derlemesi için desteklenen Azure depolama API sürümünü](/azure-stack/user/azure-stack-acs-differences?#api-version) denetleyin ve bu sürümü kodunuzda hedefleyin. 
>
> Örneğin, Azure Stack hub sürümü 2005 üzerinde çalıştırıyorsanız, depolama hizmeti için en yüksek sürüm 2019-02-02 ' dir. Event Hubs SDK istemci kitaplığı, varsayılan olarak Azure 'da kullanılabilen en yüksek sürümü (SDK 'nın sürümü sırasında 2019-07-07) kullanır. Bu durumda, bu bölümdeki adımların yanı sıra Storage Service API sürüm 2019-02-02 ' i hedeflemek için de kod eklemeniz gerekecektir. Belirli bir depolama API sürümünün nasıl hedeflenecek hakkında bir örnek için [GitHub 'da bu örneğe](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)bakın. 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure depolama ve BLOB kapsayıcısı oluşturma
Bu hızlı başlangıçta, denetim noktası deposu olarak Azure Storage 'ı (özellikle BLOB depolama) kullanacaksınız. Checkişaret, bir olay işlemcisinin bir bölüm içinde son başarılı bir şekilde işlenen etkinliğin konumunu işaretleyen veya işleme yaptığı bir işlemdir. Bir kontrol noktasının işaretlenmesi genellikle olayları işleyen işlev içinde yapılır. Checkişaret hakkında daha fazla bilgi için bkz. [olay işlemcisi](event-processor-balance-partition-load.md).

Azure depolama hesabı oluşturmak için bu adımları izleyin. 

1. [Azure Depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blob kapsayıcısı oluşturma](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Bağlantı dizesini depolama hesabına al](../storage/common/storage-configure-connection-string.md)

    **Bağlantı dizesini** ve **kapsayıcı adını** aklınızda edin. Bunları alma kodunda kullanacaksınız. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Java projenize Event Hubs kitaplıkları ekleyin
Aşağıdaki bağımlılıkları pom.xml dosyasına ekleyin. 

- [Azure-mesajlaşma-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [Azure-mesajlaşma-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.1</version>
    </dependency>
</dependencies>
```

1. Aşağıdaki **Import** deyimlerini Java dosyasının en üstüne ekleyin. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    ```
2. Adlı bir sınıf oluşturun `Receiver` ve aşağıdaki dize değişkenlerini sınıfına ekleyin. Yer tutucuları doğru değerlerle değiştirin. 

    > [!IMPORTANT]
    > Yer tutucuları doğru değerlerle değiştirin.
    > - `<Event Hubs namespace connection string>` Event Hubs ad alanına bağlantı dizesi ile. Güncelleştir 
    > - `<Event hub name>` ad alanındaki Olay Hub 'ınızın adı ile. 
    > - `<Storage connection string>` Azure depolama hesabınıza bağlantı dizesi ile. 
    > - `<Storage container name>` Azure Blob depolama alanındaki kapsayıcının adı ile. 

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
1. `main`Sınıfına aşağıdaki yöntemi ekleyin. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString) 
            .containerName(storageContainerName) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. `PARTITION_PROCESSOR` `ERROR_HANDLER` Olayları ve hataları sınıfına işleyen iki yardımcı yöntemi (ve) ekleyin `Receiver` . 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. Programı oluşturun ve hata olmadığından emin olun. 

## <a name="run-the-applications"></a>Uygulamaları çalıştırma
1. Önce **alıcı** uygulamasını çalıştırın.
1. Ardından, **Gönderen** uygulamasını çalıştırın. 
1. **Alıcı** uygulaması penceresinde, gönderen uygulama tarafından yayınlanan olayları görtığınızdan emin olun.

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    ```
1. Uygulamayı durdurmak için alıcı uygulaması penceresinde **ENTER** tuşuna basın. 

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    
    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>Sonraki adımlar
GitHub 'da aşağıdaki örneklere bakın:

- [Azure-mesajlaşma-eventhubs örnekleri](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Azure-Messaging-eventhubs-checkpointstore-blob örnekleri](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  

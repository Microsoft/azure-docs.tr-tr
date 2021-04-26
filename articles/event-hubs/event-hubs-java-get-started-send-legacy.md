---
title: Java kullanarak Azure Event Hubs olay gönderme veya alma (eski)
description: Bu makalede, Azure Event Hubs eski Azure-eventhubs paketini kullanarak olayları gönderen/alan bir Java uygulaması oluşturmaya yönelik izlenecek yol sunulmaktadır.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: b9bbd5d4290261ec96755f02a6179a7cde267d82
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309416"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Azure Event Hubs (Azure-eventhubs) olay göndermek veya olayları almak için Java 'Yı kullanma

Bu hızlı başlangıçta, **Azure-eventhubs** Java paketini kullanarak Olay Hub 'ından olayları gönderme ve olayları alma işlemlerinin nasıl yapılacağı gösterilir.

> [!WARNING]
> Bu hızlı başlangıçta eski **Azure-eventhubs** ve **Azure-eventhubs-EPH** paketleri kullanılmaktadır. En son  **Azure-Messaging-eventhubs** paketini kullanan bir hızlı başlangıç için bkz. [Azure-Messaging-eventhubs kullanarak olay gönderme ve alma](event-hubs-java-get-started-send.md). Uygulamanızı eski paketi kullanarak yeni bir pakete taşımak için [Azure-eventhubs 'den Azure-Messaging-eventhubs ' e geçiş kılavuzuna](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)bakın. 

## <a name="prerequisites"></a>Önkoşullar

Azure Event Hubs 'yi yeni kullanıyorsanız, bu hızlı başlangıcı uygulamadan önce [Event Hubs genel bakış](event-hubs-about.md) bölümüne bakın. 

Bu hızlı başlangıcı tamamlayabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

- **Microsoft Azure aboneliği**. Azure Event Hubs dahil olmak üzere Azure hizmetlerini kullanmak için bir aboneliğiniz olması gerekir.  Mevcut bir Azure hesabınız yoksa, [ücretsiz deneme](https://azure.microsoft.com/free/) için kaydolabilir veya [BIR hesap oluştururken](https://azure.microsoft.com)MSDN abonesi avantajlarınızı kullanabilirsiniz.
- Bir Java geliştirme ortamı. Bu hızlı başlangıç, [tutulma](https://www.eclipse.org/)kullanır.
- **Event Hubs bir ad alanı ve bir olay hub 'ı oluşturun**. İlk adım, Event Hubs türünde bir ad alanı oluşturmak için [Azure Portal](https://portal.azure.com) ve uygulamanızın Olay Hub 'ı ile iletişim kurması için gereken yönetim kimlik bilgilerini elde etmek için kullanılır. Bir ad alanı ve Olay Hub 'ı oluşturmak için [Bu makaledeki](event-hubs-create.md)yordamı izleyin. Ardından, makaledeki yönergeleri izleyerek Olay Hub 'ı için erişim anahtarı değerini alın: [bağlantı dizesi al](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Bu hızlı başlangıçta yazdığınız kodda erişim anahtarını kullanın. Varsayılan anahtar adı: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Olayları gönderme 
Bu bölümde, Olay Hub 'ına olay göndermek için bir Java uygulaması oluşturma gösterilmektedir. 

> [!NOTE]
> Bu hızlı başlangıcı [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend)’dan örnek olarak indirebilir, `EventHubConnectionString` ve `EventHubName` dizelerini olay hub’ınızdaki değerlerle değiştirebilir ve çalıştırabilirsiniz. Alternatif olarak, bu hızlı başlangıçta kendi oluşturduğunuz adımları izleyebilirsiniz.

### <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs kitaplığı 'na başvuru ekleme

Event Hubs için Java istemci kitaplığı, [Maven merkezi deposundaki](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)Maven projelerinde kullanıma sunulmuştur. Bu kitaplığa, Maven proje dosyanızda aşağıdaki bağımlılık bildirimini kullanarak başvurabilirsiniz:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Farklı türlerde derleme ortamları için, [Maven merkezi deposundaki](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)en son yayınlanan jar dosyalarını açıkça alabilirsiniz.  

Basit bir olay yayımcısı için *com. Microsoft. Azure. eventhubs* paketini Event Hubs istemci sınıfları için ve *com. Microsoft. Azure. servicebus* paketini, Azure Service Bus mesajlaşma istemcisiyle paylaşılan ortak özel durumlar gibi yardımcı program sınıflarına yönelik olarak içeri aktarın. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Olay hub'ına ileti göndermek için kod yazma

Aşağıdaki örnek için önce en sevdiğiniz Java geliştirme ortamında bir konsol/kabuk uygulaması için yeni bir Maven projesi oluşturun. Adlı bir sınıf ekleyin `SimpleSend` ve aşağıdaki kodu sınıfına ekleyin:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {

    }
 }
```

### <a name="construct-connection-string"></a>Bağlantı dizesi oluştur

Event Hubs istemci örneğine geçirilecek bir bağlantı dizesi değeri oluşturmak için ConnectionStringBuilder sınıfını kullanın. Yer tutucuları, ad alanını ve Olay Hub 'ını oluştururken elde ettiğiniz değerlerle değiştirin:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Olayları göndermek için kod yazma

Bir dizeyi UTF-8 bayt kodlamasıyla dönüştürerek tekil bir olay oluşturun. Sonra, bağlantı dizesinden yeni bir Event Hubs istemci örneği oluşturun ve şu iletiyi gönderin:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);

        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Programı derleyin ve çalıştırın ve hata olmadığından emin olun.

Tebrikler! Bir olay hub'ına ileti gönderdiniz.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Ek: iletilerin EventHub bölümlerine nasıl yönlendirildiği

İletiler tüketicilerle alınmadan önce, yayımcılar tarafından önce bölümlere yayımlanmaları gerekir. İletiler, com. Microsoft. Azure. eventhubs. EventHubClient nesnesindeki sendSync () yöntemi kullanılarak Olay Hub 'ına zaman uyumlu olarak yayımlandığında, bu ileti belirli bir bölüme gönderilebilir veya bölüm anahtarının belirtildiğine bağlı olarak hepsini bir kez deneme sırasında tüm kullanılabilir bölümlere dağıtılabilir.

Bölüm anahtarını temsil eden bir dize belirtildiğinde, olayın hangi bölüme gönderileceğini belirleyen anahtar karma olur.

Bölüm anahtarı ayarlanmamışsa, iletiler kullanılabilir tüm bölümlerle gösterilir

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Olayları alma
Bu öğreticideki kod, [GitHub 'Daki Eventprocessorsample koduna](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)dayanır ve bu da tam çalışma uygulamasını görmek için inceleyebilirsiniz.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Java’da EventProcessorHost bulunan iletiler alma

**Eventprocessorhost** , bu Event Hubs kalıcı denetim noktaları ve paralel alma işlemlerini yöneterek Event Hubs olayların alınmasını kolaylaştıran bir Java sınıfıdır. EventProcessorHost’u kullanarak, farklı düğümlerde barındırıldığında bile birden çok alıcı arasında olayları bölebilirsiniz. Bu örnek, tek alıcı için EventProcessorHost’un nasıl kullanıldığını göstermektedir.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

EventProcessorHost 'u kullanmak için [Azure Storage hesabınız] [Azure Storage hesabınız] olması gerekir:

1. [Azure Portal](https://portal.azure.com)oturum açın ve ekranın sol tarafındaki **kaynak oluştur** ' u seçin.
2. **Depolama**'yı ve ardından **Depolama hesabı**'nı seçin. **Depolama hesabı oluştur** penceresinde, depolama hesabı için bir ad yazın. Kalan alanları tamamlayın, istediğiniz bölgeyi seçin ve ardından **Oluştur**' u seçin.

    ![Azure portal bir depolama hesabı oluşturun](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Yeni oluşturulan depolama hesabını seçin ve **erişim anahtarları**' nı seçin:

    ![Azure portal erişim anahtarlarınızı alın](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    KEY1 değerini geçici bir konuma kopyalayın. Daha sonra bu öğreticide kullanacaksınız.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>EventProcessor Ana Bilgisayarını kullanarak Java projesi oluşturma

Event Hubs için Java istemci kitaplığı, Maven [Merkezi deposundaki](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)Maven projelerinde kullanılabilir ve Maven proje dosyanızda aşağıdaki bağımlılık bildirimi kullanılarak başvurulabilirler: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Farklı türlerde derleme ortamları için, [Maven merkezi deposundaki](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)en son yayınlanan jar dosyalarını açıkça alabilirsiniz.

1. Aşağıdaki örnek için önce en sevdiğiniz Java geliştirme ortamında bir konsol/kabuk uygulaması için yeni bir Maven projesi oluşturun. Sınıfı çağrılır `ErrorNotificationHandler` .     

    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. `EventProcessorSample` adlı yeni bir sınıf oluşturmak için aşağıdaki kodu kullanın. Yer tutucuları, Olay Hub 'ı ve depolama hesabını oluştururken kullanılan değerlerle değiştirin:

   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";

           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);

           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);

           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
                 try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
            {
                return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!

           System.out.println("End of sample");
       }
   }
   ```
3. Aşağıdaki kodu kullanarak adlı bir daha fazla sınıf oluşturun `EventProcessor` :

    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }

        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;

                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                               data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Bu öğretici, EventProcessorHost’un tek bir örneğini kullanır. Aktarım hızını artırmak için, tercihen farklı makinelerde EventProcessorHost ' un birden çok örneğini çalıştırmanızı öneririz.  Artıklık de sağlar. Böyle durumlarda, alınan olayların yük dengesi için çeşitli örnekler otomatik olarak birbirleriyle koordine olurlar. Birden çok alıcının her birinin *tüm* olayları işlemesini istiyorsanız **ConsumerGroup** kavramını kullanmalısınız. Olaylar farklı makinelerden alındığında, dağıtıldıkları makineleri (veya rolleri) temel alan EventProcessorHost örnekleri için ad belirtmek yararlı olabilir.

### <a name="publishing-messages-to-eventhub"></a>EventHub 'e Ileti yayımlama

İletiler tüketicilerle alınmadan önce, yayımcılar tarafından önce bölümlere yayımlanmaları gerekir. Bu, iletiler, com. Microsoft. Azure. eventhubs. EventHubClient nesnesindeki sendSync () yöntemi kullanılarak Olay Hub 'ına zaman uyumlu olarak yayımlandığında, ileti belirli bir bölüme gönderilebilir veya bölüm anahtarının belirtildiğine bağlı olarak hepsini bir kez denenecek şekilde, tüm kullanılabilir bölümlere dağıtılabilir.

Bölüm anahtarını temsil eden bir dize belirtildiğinde, olayın hangi bölüme gönderileceğini belirleyen anahtar karma hale getirilir.

Bölüm anahtarı ayarlanmamışsa, iletiler kullanılabilir tüm bölümler için de gösterilir

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>EventProcessorHost (EPH) için özel CheckpointManager uygulama

API, varsayılan uygulamanın kullanım durumu ile uyumlu olmadığı senaryolar için özel denetim noktası Yöneticisi 'ni uygulamak için bir mekanizma sağlar.

Varsayılan denetim noktası Yöneticisi blob depolamayı kullanır, ancak EPH tarafından kullanılan denetim noktası yöneticisini kendi uygulamanız ile geçersiz kılarsınız, kontrol noktası Yöneticisi uygulamanızı geri almak istediğiniz herhangi bir depoyu kullanabilirsiniz.

Com. Microsoft. Azure. eventprocessorhost. ICheckpointManager arabirimini uygulayan bir sınıf oluşturun

Denetim noktası yöneticisinin özel uygulamasını kullanın (com. Microsoft. Azure. eventprocessorhost. ICheckpointManager)

Uygulamanızda, varsayılan denetim noktası mekanizmasını geçersiz kılabilir ve kendi veri deponuzu (SQL Server, CosmosDB ve Redsıs için Azure önbelleği gibi) temel alarak kendi kontrol noktalarınızı uygulayabilirsiniz. Denetim noktası Yöneticisi uygulamanızı geri yüklemek için kullanılan deponun, tüketici grubuna yönelik olayları işleyen tüm EPH örnekleri tarafından erişilebilir olmasını öneririz.

Ortamınızda kullanılabilir olan herhangi bir veri deposunu kullanabilirsiniz.

Com. Microsoft. Azure. eventprocessorhost. EventProcessorHost sınıfı size EventProcessorHost için denetim noktası yöneticisini geçersiz kılmanıza olanak tanıyan iki Oluşturucu sağlar.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleleri okuyun: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs'ın özellikleri ve terminolojisi](event-hubs-features.md)
- [Event Hubs ile ilgili SSS](event-hubs-faq.yml)

---
title: Azure-mesajlaşma-ServiceBus kullanarak Azure Service Bus konularına ileti gönderme
description: Bu hızlı başlangıçta, Azure-mesajlaşma-ServiceBus paketini kullanarak Azure Service Bus konularına nasıl ileti göndereceğiniz gösterilmektedir.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 79eb7783fd3daf546539dd5b9048f4e9f484374f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279808"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Azure Service Bus konuya ileti gönderme ve aboneliklerden konuya ileti alma (.NET)
Bu öğreticide, aşağıdaki görevleri yapmak için bir C# uygulaması oluşturacaksınız:

1. Service Bus bir konuya ileti gönderin. 

    Service Bus konu başlığı, gönderen uygulamaların ileti göndermesini sağlayan bir uç nokta sağlar. Bir konu, bir veya daha fazla aboneliğe sahip olabilir. Bir konunun her aboneliği, konuya gönderilen iletinin bir kopyasını alır. Service Bus konuları hakkında daha fazla bilgi için bkz. [Azure Service Bus nedir?](service-bus-messaging-overview.md). 
1. Konunun aboneliğinden ileti alma. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Service Bus konu başlıkları ve abonelikler":::

    > [!Important]
    > Bu hızlı başlangıç, yeni **Azure. Messaging. ServiceBus** paketini kullanır. Eski Microsoft. Azure. ServiceBus paketini kullanıyorsanız, bkz. [Microsoft. Azure. ServiceBus paketini kullanarak Ileti gönderme ve alma](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [Visual Studio veya MSDN abonesi avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Bu [hızlı](service-bus-quickstart-topics-subscriptions-portal.md) başlangıçtaki adımları izleyerek konuya bir Service Bus konu ve abonelik oluşturun. 

    > [!NOTE]
    > Bağlantı dizesini ad alanı, konu adı ve bu öğreticideki konunun aboneliklerinden birinin adı olarak kullanacaksınız.  
- [Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>Konu başlığına ileti gönderme
Bu bölümde, Visual Studio 'da bir .NET Core konsol uygulaması oluşturacaksınız, oluşturduğunuz Service Bus konuya ileti göndermek için kod eklersiniz. 

### <a name="create-a-console-application"></a>Konsol uygulaması oluşturma
Visual Studio 'Yu kullanarak bir .NET Core konsol uygulaması oluşturun. 

1. Visual Studio 'Yu başlatın.  
1. **Başlarken** sayfasını görürseniz **Yeni proje oluştur**' u seçin. 
1. **Yeni proje oluştur** sayfasında, şu adımları izleyin: 
    1. Programlama dili için **C#**' ı seçin. 
    1. Proje türü için **konsol**' ı seçin. 
    1. Şablonlar listesinden **konsol uygulaması (.NET Core)** seçeneğini belirleyin. 
    1. Ardından **İleri**' yi seçin. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="Konsol uygulaması projesi oluşturma":::
1. **Yeni projenizi yapılandırın** sayfasında, şu adımları izleyin: 
    1. **Proje adı** için proje için bir ad girin. 
    1. **Konum** için, proje ve çözüm dosyaları için bir konum seçin. 
    1. **Çözüm adı** için çözüm için bir ad girin. Visual Studio çözümünde bir veya daha fazla proje olabilir. Bu hızlı başlangıç bölümünde, çözümün yalnızca bir projesi olacaktır. 
    1. Projeyi oluşturmak için **Oluştur**'u seçin. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="Proje ve çözüm adı ve konumunu girin":::    


### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet paketi ekleme
1. Yeni oluşturulan projeye sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="NuGet paketleri menüsünü Yönet":::        
1. **Tarama** sekmesine geçin.
1. **[Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** araması yapın ve seçin.
1. Yüklemeyi tamamlamak için **Yükle**'yi seçin.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="NuGet paketini Service Bus seçin.":::
5. **Değişiklikleri Önizle** iletişim kutusunu görürseniz devam etmek için **Tamam** ' ı seçin. 
1. **Lisans kabulü** sayfasını görürseniz devam etmek Için **kabul ediyorum** ' u seçin. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>Konuya ileti göndermek için kod ekleme 

1. **Çözüm Gezgini** penceresinde, **program. cs** ' ye çift tıklayarak dosyayı düzenleyicide açın. 
1. Aşağıdaki deyimlerini, `using` Sınıf bildiriminden önce ad alanı tanımının üst kısmına ekleyin:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. `Program`Sınıfında, `Main` işlevinin üzerinde aşağıdaki değişkenleri bildirin:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    Aşağıdaki değerleri değiştirin:
    - `<NAMESPACE CONNECTION STRING>` Service Bus ad alanına bağlantı dizesi ile
    - `<TOPIC NAME>` konusunun adı ile
    - `<SUBSCRIPTION NAME>` Abonelik adı ile

### <a name="send-a-single-message-to-the-topic"></a>Konuya tek bir ileti gönderin
`SendMessageToTopicAsync` `Program` Yönteminin altındaki veya altındaki sınıfa adlı bir yöntem ekleyin `Main` . Bu yöntem, konuya tek bir ileti gönderir.

```csharp
    static async Task SendMessageToTopicAsync()
    {
        // create a Service Bus client 
        await using (ServiceBusClient client = new ServiceBusClient(connectionString))
        {
            // create a sender for the topic
            ServiceBusSender sender = client.CreateSender(topicName);
            await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
            Console.WriteLine($"Sent a single message to the topic: {topicName}");
        }
    }
```

Bu yöntem aşağıdaki adımları yapar: 
1. Ad alanına bağlantı dizesini kullanarak bir [Servicebusclient](/dotnet/api/azure.messaging.servicebus.servicebusclient) nesnesi oluşturur. 
1. , `ServiceBusClient` Belirtilen Service Bus konusu Için [Servicebussender](/dotnet/api/azure.messaging.servicebus.servicebussender) nesnesi oluşturmak için nesnesini kullanır. Bu adım [Servicebusclient. CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) metodunu kullanır.
1. Sonra, yöntemi [Servicebussender. SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) yöntemini kullanarak Service Bus konuya tek bir test iletisi gönderir. 

### <a name="send-a-batch-of-messages-to-the-topic"></a>Konuya bir toplu ileti gönderin
1. `CreateMessages`Sınıfına ileti için bir sıra (.NET kuyruğu değil, Service Bus kuyruğu) oluşturma adlı bir yöntem ekleyin `Program` . Genellikle, bu iletileri uygulamanızın farklı bölümlerinden alırsınız. Burada, örnek iletiler için bir kuyruk oluşturacağız. .NET kuyrukları hakkında bilgi sahibi değilseniz, bkz. [Queue. kuyruğa al](/dotnet/api/system.collections.queue.enqueue).

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Sınıfı adlı bir yöntem `SendMessageBatchAsync` ekleyin `Program` ve aşağıdaki kodu ekleyin. Bu yöntem bir ileti kuyruğu alır ve bir veya daha fazla toplu işi Service Bus konusuna gönderecek şekilde hazırlar. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```    

    Koddan önemli adımlar şunlardır:
    1. Ad alanına bağlantı dizesini kullanarak bir [Servicebusclient](/dotnet/api/azure.messaging.servicebus.servicebusclient) nesnesi oluşturur. 
    1. [](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) `ServiceBusClient` Belirtilen Service Bus konusu Için [servicebussender](/dotnet/api/azure.messaging.servicebus.servicebussender) nesnesi oluşturmak üzere nesnesinde createsender yöntemini çağırır. 
    1. `GetMessages`Service Bus konusuna gönderilecek ileti kuyruğunu almak için yardımcı yöntemini çağırır. 
    1. [Servicebussender. CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync)kullanarak bir [Servicebusmessagebatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) oluşturur.
    1. [Servicebusmessagebatch. TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage)kullanarak toplu işe ileti ekleyin. İletiler toplu işe eklendikçe, .NET kuyruğundan kaldırılır. 
    1. [Servicebussender. SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) yöntemini kullanarak ileti toplu işini Service Bus konusuna gönderir.

### <a name="update-the-main-method"></a>Main metodunu güncelleştirme
`Main()`Yöntemini aşağıdaki **Async** `Main` yöntemiyle değiştirin. Tek bir ileti ve konuya bir toplu ileti göndermek için gönderme yöntemlerini çağırır.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>Konuya ileti göndermek için uygulamayı test etme
1. Uygulamayı çalıştırın. Aşağıdaki çıkışı görmeniz gerekir:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. Azure portal, aşağıdaki adımları izleyin:
    1. Service Bus ad alanına gidin. 
    1. **Genel bakış** sayfasında, alt orta bölmedeki **konular** sekmesine geçin ve Service Bus konusunu seçin. Aşağıdaki örnekte, bu `mytopic` .
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Konu seçin":::
    1. **Service Bus konu** sayfasında, alt **ölçümler** bölümündeki **iletiler** grafiğinde, konu için dört gelen ileti olduğunu görebilirsiniz. Değeri görmüyorsanız, birkaç dakika bekleyin ve güncelleştirilmiş grafiği görmek için sayfayı yenileyin. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Konuya gönderilen iletiler" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Alt bölmedeki aboneliği seçin. Aşağıdaki örnekte **S1**' dir. **Service Bus abonelik** sayfasında **etkin ileti sayısını** **4** olarak görürsünüz. Abonelik, konuya gönderdiğiniz dört iletiyi aldı, ancak henüz hiç alıcı çekmedi. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Abonelikte alınan iletiler" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Bir abonelikten ileti alma

1. `Program`İletileri ve hataları işleyen sınıfa aşağıdaki yöntemleri ekleyin. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Sınıfına aşağıdaki yöntemi ekleyin `ReceiveMessagesFromSubscriptionAsync` `Program` .

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```

    Koddan önemli adımlar şunlardır:
    1. Ad alanına bağlantı dizesini kullanarak bir [Servicebusclient](/dotnet/api/azure.messaging.servicebus.servicebusclient) nesnesi oluşturur. 
    1. [](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) `ServiceBusClient` Belirtilen Service Bus konusu ve abonelik birleşimi Için [servicebusprocessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) nesnesi oluşturmak üzere nesnesinde createprocessor yöntemini çağırır. 
    1. Nesnenin [Processmessageasync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) ve [processerrorasync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) olayları için işleyicileri belirtir `ServiceBusProcessor` . 
    1. Nesnesinde [Startprocessingasync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) öğesini çağırarak iletileri işlemeye başlar `ServiceBusProcessor` . 
    1. Kullanıcı işlemeyi bitirmek için bir tuşa bastığında, nesnesinde [Stopprocessingasync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) öğesini çağırır `ServiceBusProcessor` . 
1. Yöntemine bir çağrı ekleyin `ReceiveMessagesFromSubscriptionAsync` `Main` . `SendMessagesToTopicAsync`Yalnızca ileti alma sınamasını yapmak istiyorsanız yöntemine açıklama ekleyin. Bunu yapmazsanız, konuya başka dört ileti gönderildiğini görürsünüz. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Uygulamayı çalıştırma
Uygulamayı çalıştırın. Bir dakika bekleyip ileti almayı durdurmak için herhangi bir tuşa basın. Aşağıdaki çıktıyı görmeniz gerekir (anahtar için ara çubuğu). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Portalı yeniden kontrol edin. 

- **Service Bus konu** sayfasındaki **iletiler** grafiğinde, sekiz gelen ileti ve sekiz giden ileti görürsünüz. Bu numaraları görmüyorsanız, birkaç dakika bekleyin ve güncelleştirilmiş grafiği görmek için sayfayı yenileyin. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Gönderilen ve alınan iletiler" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- **Service Bus abonelik** sayfasında **etkin ileti sayısını** sıfır olarak görürsünüz. Bunun nedeni, alıcı bu abonelikten ileti almış ve iletileri tamamlamıştır. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Son sırada abonelikte etkin ileti sayısı" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki belgelere ve örneklere bakın:

- [.NET için Azure Service Bus istemci kitaplığı-Benioku](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub 'da Azure Service Bus için .NET örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API'si başvurusu](/dotnet/api/azure.messaging.servicebus?preserve-view=true)
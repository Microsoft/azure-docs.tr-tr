---
title: Azure Service Bus konuları ve abonelikleri ile çalışmaya başlama | Microsoft Docs
description: Service Bus mesajlaşma konuları ve aboneliklerini kullanan bir C# .NET Core konsol uygulaması yazın.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 09/02/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a64ccec49248f48a85782cfe9537513a97b2bdfc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868168"
---
# <a name="get-started-with-service-bus-topics"></a>Service Bus konuları ile çalışmaya başlama
Bu öğreticide aşağıdaki adımlar yer almaktadır:

1. Konuya bir dizi ileti göndermek için bir .NET Core konsol uygulaması yazın.
2. Bu iletileri abonelikten almak için bir .NET Core konsol uygulaması yazın.

> [!WARNING]
> Bu hızlı başlangıç, eski Microsoft. Azure. ServiceBus paketini kullanır. En son Azure. Messaging. ServiceBus paketini kullanan bir hızlı başlangıç için bkz. [Azure. Messaging. ServiceBus paketini kullanarak Ileti gönderme ve alma](service-bus-dotnet-how-to-use-topics-subscriptions.md). Uygulamanızı eski kitaplığı kullanarak yeni bir kitaplık ile taşımak için [Microsoft. Azure. ServiceBus 'Dan Azure. Messaging. ServiceBus 'a geçiş kılavuzuna](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/MigrationGuide.md)bakın. 

## <a name="prerequisites"></a>Önkoşullar

1. Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [Visual Studio veya MSDN abonesi avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
2. Hızlı Başlangıç bölümündeki adımları uygulayın: Azure portal kullanarak aşağıdaki görevleri yapmak için [Service Bus konu başlığı ve konuya yönelik abonelikler oluşturun](service-bus-quickstart-topics-subscriptions-portal.md) :
    1. Service Bus **ad alanı** oluşturun.
    2. **Bağlantı dizesini** alın.
    3. Ad alanında bir **Konu** oluşturun.
    4. Ad alanındaki konuya **bir abonelik** oluşturun.
3. [Visual Studio 2017 Güncelleştirme 3 (sürüm 15.3, 26730.01)](https://www.visualstudio.com/vs) veya sonraki sürümler.
4. [NET Core SDK](https://dotnet.microsoft.com/download), sürüm 2.0 veya sonraki sürümler.
 
## <a name="send-messages-to-the-topic"></a>Konuya ileti gönderme

Konuya ileti göndermek için Visual Studio kullanılarak bir C# konsol uygulaması yazın.

### <a name="create-a-console-application"></a>Konsol uygulaması oluşturma

Visual Studio'yu başlatın ve yeni bir **Konsol Uygulaması (.NET Core)** projesi oluşturun.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet paketi ekleme

1. Yeni oluşturulan projeye sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin.
2. **Gözat** sekmesine tıklayın, **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** araması yapın ve ardından **Microsoft.Azure.ServiceBus** öğesini seçin. Yüklemeyi tamamlamak için **Yükle**'ye tıklayın, ardından bu iletişim kutusunu kapatın.
   
    ![NuGet paketi seçme][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Konuya ileti göndermek için kod yazma

1. Program.cs’de, ad alanı tanımının üst kısmına, sınıf bildiriminden önce aşağıdaki `using` deyimlerini ekleyin:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` sınıfında, aşağıdaki değişkenleri bildirin. `ServiceBusConnectionString` değişkenini, ad alanını oluştururken elde ettiğiniz bağlantı dizesine, `TopicName` değerini ise konuyu oluştururken kullandığınız ada ayarlayın:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Yöntemini, `Main()`  `Main` bir sonraki adımda ekleyeceğiniz sendmessagesasync yöntemini kullanarak zaman uyumsuz olarak ileti gönderen aşağıdaki Async yöntemiyle değiştirin. 

    ```csharp
    public static async Task Main(string[] args)
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```
5. `Main` metodundan hemen sonra, `numberOfMessagesToSend` tarafından belirlenen sayıda mesajı (geçerli değer: 10) gönderen aşağıdaki `SendMessagesAsync()` metodunu ekleyin:

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Gönderen Program.cs dosyanız aşağıdaki gibi görünmelidir.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            public static async Task Main(string[] args)
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);
    
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");
    
                // Send messages.
                await SendMessagesAsync(numberOfMessages);
    
                Console.ReadKey();
    
                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Programı çalıştırın ve Azure portalını denetleyin: Ad alanının **Genel Bakış** penceresinde konunuzun adına tıklayın. Konunun **Temel Bileşenler** penceresi gösterilir. Pencerenin alt kısmının yanında listelenen aboneliklerde, aboneliğin **İleti Sayısı** değerinin artık **10** olmasına dikkat edin. İletileri almadan gönderen uygulamasını her çalıştırdığınızda (sonraki bölümde açıklandığı gibi), bu değer 10 artar. Ayrıca, uygulamanın konuya ileti eklediği her durumda konunun geçerli boyutu, **Temel Bileşenler** penceresindeki **Geçerli** değerini artırır.
   
      ![İleti boyutu][topic-message]

## <a name="receive-messages-from-the-subscription"></a>Abonelikten ileti alma

Gönderdiğiniz iletileri almak için, başka bir .NET Core konsol uygulaması oluşturun ve önceki gönderen uygulamaya benzer şekilde **Microsoft. Azure. ServiceBus** NuGet paketini yükleyebilirsiniz.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Abonelikten ileti almak için kod yazın

1. Program.cs’de, ad alanı tanımının üst kısmına, sınıf bildiriminden önce aşağıdaki `using` deyimlerini ekleyin:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` sınıfında, aşağıdaki değişkenleri bildirin. `ServiceBusConnectionString` değişkenini ad alanını oluştururken kullandığınız bağlantı dizesine, `TopicName` değişkenini konuyu oluştururken kullandığınız ada ve `SubscriptionName` değişkenini konu için abonelik oluştururken kullandığınız ada ayarlayın:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. `Main()`Yöntemini aşağıdaki **Async** `Main` yöntemiyle değiştirin. `RegisterOnMessageHandlerAndReceiveMessages()`Bir sonraki adımda ekleyeceğiniz yöntemi çağırır. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();    
    }
   ```
5. `Main()` metodundan hemen sonra, ileti işleyiciyi kaydeden ve gönderen uygulama tarafından gönderilen iletileri alan aşağıdaki metodu ekleyin:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Önceki metottan hemen sonra, alınan mesajları işlemek için aşağıdaki `ProcessMessagesAsync()` metodunu ekleyin:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Son olarak, olası özel durumları işlemek için aşağıdaki metodu ekleyin:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. Alıcı Program.cs dosyanız aşağıdaki gibi görünmelidir:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            public static async Task Main(string[] args)
            {    
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);
        
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");
        
                // Register subscription message handler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
        
                Console.ReadKey();
        
                await subscriptionClient.CloseAsync();    
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Programı çalıştırın ve portalı tekrar denetleyin. **İleti Sayısı** ve **Geçerli** değerlerinin artık **0** olduğuna dikkat edin.
   
    ![Konu uzunluğu][topic-message-receive]

Tebrikler! .NET Standard kitaplığını kullanarak bir konu ve abonelik oluşturdunuz, 10 ileti gönderdiniz ve bu iletileri aldınız.

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, kullanıcıların bir Service Bus ad alanına bağlanmasına ve mesajlaşma varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konu, kuyruk, abonelik, geçiş Hizmetleri, Bildirim Hub 'ları ve Olay Hub 'larını test etme yeteneği sağlar. 

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşmasının daha gelişmiş özelliklerini gösteren [örneklerin bulunduğu Service Bus GitHub deposuna](https://github.com/Azure/azure-service-bus/tree/master/samples) göz atın.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com

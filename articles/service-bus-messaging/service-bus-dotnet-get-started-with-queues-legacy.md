---
title: Azure Service Bus kuyrukları ile çalışmaya başlama | Microsoft Docs
description: Bu öğreticide, Service Bus kuyruğuna ileti göndermek ve ileti almak için .NET Core konsol uygulamaları oluşturacaksınız.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 09/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d92a5e0b0d1a6953d0043f85ff98f6740aaacebb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864023"
---
# <a name="get-started-with-service-bus-queues"></a>Service Bus kuyrukları ile çalışmaya başlama
Bu öğreticide, Service Bus kuyruğuna ileti göndermek ve ileti almak için .NET Core konsol uygulamaları oluşturacaksınız.

> [!WARNING]
> Bu hızlı başlangıç, eski Microsoft. Azure. ServiceBus paketini kullanır. En son Azure. Messaging. ServiceBus paketini kullanan bir hızlı başlangıç için bkz. [Azure. Messaging. ServiceBus paketini kullanarak olayları gönderme ve alma](service-bus-dotnet-get-started-with-queues.md). 

## <a name="prerequisites"></a>Önkoşullar

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Core SDK](https://dotnet.microsoft.com/download), sürüm 2.0 veya sonraki sürümler.
- Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Birlikte çalışmak için bir kuyruğunuz yoksa, bir kuyruk oluşturmak için [Service Bus kuyruğu oluşturmak üzere Azure Portal kullanma](service-bus-quickstart-portal.md) adımlarını izleyin.

  - Service Bus kuyruklara hızlı genel bakış konusunu okuyun.
  - Service Bus ad alanı oluşturun.
  - Bağlantı dizesini alın.
  - Service Bus kuyruğu oluşturun.

## <a name="send-messages"></a>İleti gönderme

Kuyruğa ileti göndermek için, Visual Studio'yu kullanarak bir C# konsol uygulaması yazın.

### <a name="create-a-console-application"></a>Konsol uygulaması oluşturma

Visual Studio 'Yu başlatın ve C# için yeni bir **konsol uygulaması (.NET Core)** projesi oluşturun. Bu örnek, uygulama *Coresenderapp*' i adlandırır.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet paketi ekleme

1. Yeni oluşturulan projeye sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin.
1. **Gözat**'ı seçin. **[Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** araması yapın ve seçin.
1. Yüklemeyi **gerçekleştirmek için yükleme** ' yi seçin ve ardından NuGet Paket Yöneticisi ' ni kapatın.

    ![NuGet paketi seçme][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Kuyruğa ileti göndermek için kod yazma

1. *Program. cs*' de, `using` Sınıf bildiriminden önce, ad alanı tanımının en üstüne aşağıdaki deyimleri ekleyin:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. `Program`Sınıfında, aşağıdaki değişkenleri bildirin:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Ad alanı için Bağlantı dizenizi değişken olarak girin `ServiceBusConnectionString` . Sıra adınızı girin.

1. `Main()`Yöntemini aşağıdaki **Async** `Main` yöntemiyle değiştirin. `SendMessagesAsync()`Kuyruğa ileti göndermek için bir sonraki adımda ekleyeceğiniz yöntemi çağırır. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. Doğrudan yönteminden sonra `MainAsync()` , `SendMessagesAsync()` tarafından belirtilen ileti sayısını gönderme işini yapan aşağıdaki yöntemi ekleyin `numberOfMessagesToSend` (Şu anda 10 ' a ayarlanır):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

*Program. cs* dosyanızı şöyle görünmelidir.

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
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

Programı çalıştırın ve Azure portal denetleyin.

Sıra **Essentials 'ı** göstermek için ad alanı **genel bakış** penceresinde kuyruğunuzun adını seçin.

![Sayı ve boyut ile alınan iletiler][queue-message]

Kuyruğun **etkin ileti sayısı** değeri artık **10**' dur. Bu gönderen uygulamayı iletileri almadan her çalıştırdığınızda, bu değer 10 artar.

Kuyruğun geçerli boyutu, uygulamanın sıraya ileti eklemesi her seferinde **Essentials** 'daki **geçerli** değeri arttırır.

Sonraki bölümde bu iletilerin nasıl alınacağını açıklanmaktadır.

## <a name="receive-messages"></a>İleti alma

Gönderdiğiniz iletileri almak için başka bir **konsol uygulaması (.NET Core)** uygulaması oluşturun. Gönderen uygulaması için yaptığınız gibi **Microsoft. Azure. ServiceBus** NuGet paketini yükleyebilirsiniz.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Kuyruktan ileti almak için kod yazma

1. *Program. cs*' de, `using` Sınıf bildiriminden önce, ad alanı tanımının en üstüne aşağıdaki deyimleri ekleyin:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. `Program`Sınıfında, aşağıdaki değişkenleri bildirin:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Ad alanı için Bağlantı dizenizi değişken olarak girin `ServiceBusConnectionString` . Sıra adınızı girin.

1. `Main()` yöntemini aşağıdaki kod ile değiştirin:

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. Doğrudan yönteminden sonra `MainAsync()` , ileti işleyicisini kaydeden ve gönderen uygulama tarafından gönderilen iletileri alan aşağıdaki yöntemi ekleyin:

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
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. Önceki metottan hemen sonra, alınan mesajları işlemek için aşağıdaki `ProcessMessagesAsync()` metodunu ekleyin:

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. Son olarak, olası özel durumları işlemek için aşağıdaki metodu ekleyin:

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

*Program. cs* dosyanızın şöyle görünmesi gerekir:

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
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

Programı çalıştırın ve portalı tekrar denetleyin. **Etkin ileti sayısı** ve **geçerli** değerler artık **0**' dır.

![İletiler alındıktan sonra kuyruğa al][queue-message-receive]

Tebrikler! Artık bir kuyruk oluşturdunuz, bu kuyruğa bir ileti kümesi gönderdiniz ve bu iletileri aynı kuyruktan almış oldunuz.

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus Gezgini kullanıcıların bir Service Bus ad alanına kolayca bağlanmasına ve mesajlaşma varlıklarını yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konuları, kuyrukları, abonelikleri, geçiş hizmetlerini, Bildirim Hub 'larını ve Olay Hub 'larını test etme özelliğini sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşmasının daha gelişmiş özelliklerini gösteren [örneklerin bulunduğu GitHub depomuza](https://github.com/Azure/azure-service-bus/tree/master/samples) göz atın.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues-legacy/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues-legacy/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues-legacy/queue-message-receive-in-essentials.png


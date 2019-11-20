---
title: Azure Service Bus kuyrukları ile çalışmaya başlama | Microsoft Docs
description: Service Bus mesajlaşması kuyruklarını kullanan bir C# konsol uygulaması yazın.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/04/2019
ms.author: aschhab
ms.openlocfilehash: cecd37c16d34c0cd6cf7a4d98732762d16073864
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884143"
---
# <a name="get-started-with-service-bus-queues"></a>Service Bus kuyrukları ile çalışmaya başlama
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Bu öğreticide, Service Bus kuyruğuna ileti göndermek ve ileti almak için .NET Core konsol uygulamaları oluşturacaksınız.

## <a name="prerequisites"></a>Ön koşullar

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Core SDK](https://www.microsoft.com/net/download/windows), sürüm 2.0 veya sonraki sürümler.
- Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Birlikte çalışmak için bir kuyruğunuz yoksa, bir kuyruk oluşturmak için [Service Bus kuyruğu oluşturmak üzere Azure Portal kullanma](service-bus-quickstart-portal.md) adımlarını izleyin.

  - Service Bus kuyruklara hızlı genel bakış konusunu okuyun.
  - Service Bus ad alanı oluşturun.
  - Bağlantı dizesini alın.
  - Service Bus kuyruğu oluşturun.

## <a name="send-messages-to-the-queue"></a>Kuyruğa ileti gönderme

Kuyruğa ileti göndermek için, Visual Studio'yu kullanarak bir C# konsol uygulaması yazın.

### <a name="create-a-console-application"></a>Konsol uygulaması oluşturma

Visual Studio 'Yu başlatın ve için C#yeni bir **konsol uygulaması (.NET Core)** projesi oluşturun. Bu örnek, uygulama *Coresenderapp*' i adlandırır.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet paketi ekleme

1. Yeni oluşturulan projeye sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin.
1. **Gözat**’ı seçin. **[Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** araması yapın ve seçin.
1. Yüklemeyi **gerçekleştirmek için yükleme** ' yi seçin ve ardından NuGet Paket Yöneticisi ' ni kapatın.

    ![NuGet paketi seçme][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Kuyruğa ileti göndermek için kod yazma

1. *Program.cs*' de, Sınıf bildiriminden önce, ad alanı tanımının en üstüne aşağıdaki `using` deyimlerini ekleyin:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. `Program` sınıfında, aşağıdaki değişkenleri bildirin:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Ad alanı için Bağlantı dizenizi `ServiceBusConnectionString` değişkeni olarak girin. Sıra adınızı girin.

1. `Main()` öğesinin varsayılan içeriklerini aşağıdaki kod satırıyla değiştirin:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

1. `Main()` öğesinden hemen sonra, gönderme iletilerini metoda çağıran aşağıdaki zaman uyumsuz `MainAsync()` metodunu ekleyin:

    ```csharp
    static async Task MainAsync()
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

1. `MainAsync()` yönteminden hemen sonra, `numberOfMessagesToSend` tarafından belirtilen ileti sayısını gönderme işini yapan aşağıdaki `SendMessagesAsync()` yöntemini ekleyin (Şu anda 10 ' a ayarlanmıştır):

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

*Program.cs* dosyanız şöyle görünmelidir.

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

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");

            // Send Messages
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

Sıra **Essentials 'ı**göstermek için ad alanı **genel bakış** penceresinde kuyruğunuzun adını seçin.

![Sayı ve boyut ile alınan iletiler][queue-message]

Kuyruğun **etkin ileti sayısı** değeri artık **10**' dur. Bu gönderen uygulamayı iletileri almadan her çalıştırdığınızda, bu değer 10 artar.

Kuyruğun geçerli boyutu, uygulamanın sıraya ileti eklemesi her seferinde **Essentials** 'daki **geçerli** değeri arttırır.

Sonraki bölümde bu iletilerin nasıl alınacağını açıklanmaktadır.

## <a name="receive-messages-from-the-queue"></a>Kuyruktan ileti alma

Gönderdiğiniz iletileri almak için başka bir **konsol uygulaması (.NET Core)** uygulaması oluşturun. Gönderen uygulaması için yaptığınız gibi **Microsoft. Azure. ServiceBus** NuGet paketini yükleyebilirsiniz.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Kuyruktan ileti almak için kod yazma

1. *Program.cs*' de, Sınıf bildiriminden önce, ad alanı tanımının en üstüne aşağıdaki `using` deyimlerini ekleyin:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. `Program` sınıfında, aşağıdaki değişkenleri bildirin:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Ad alanı için Bağlantı dizenizi `ServiceBusConnectionString` değişkeni olarak girin. Sıra adınızı girin.

1. `Main()` öğesinin varsayılan içeriklerini aşağıdaki kod satırıyla değiştirin:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

1. `Main()` öğesinden hemen sonra, `MainAsync()` metoduna çağrı yapan aşağıdaki zaman uyumsuz `RegisterOnMessageHandlerAndReceiveMessages()` metodunu ekleyin:

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. `MainAsync()` yönteminden hemen sonra, ileti işleyicisini kaydeden ve gönderen uygulama tarafından gönderilen iletileri alan aşağıdaki yöntemi ekleyin:

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

*Program.cs* dosyanız şu şekilde görünmelidir:

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

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive-in-essentials.png


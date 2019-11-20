---
title: 'Hızlı başlangıç: .NET Framework Azure Event Hubs kullanarak olay gönderme ve alma'
description: 'Hızlı başlangıç: Bu makale, Azure Event Hubs olayları Gönderen .NET Framework bir uygulama oluşturmaya yönelik bir yol sağlar.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 89419e9a3ef364d4095800a617a84ff2f63c09a0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720655"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Hızlı başlangıç: .NET Framework kullanarak Azure Event Hubs olay gönderme veya olayları alma
Azure Event Hubs saniyede milyonlarca olay alıp işleme kapasitesine sahip olan bir Büyük Veri akış platformu ve olay alma hizmetidir. Event Hubs dağıtılan yazılımlar ve cihazlar tarafından oluşturulan olayları, verileri ve telemetrileri işleyebilir ve depolayabilir. Bir olay hub’ına gönderilen veriler, herhangi bir gerçek zamanlı analiz sağlayıcısı ve işlem grubu oluşturma/depolama bağdaştırıcıları kullanılarak dönüştürülüp depolanabilir. Olay Hub’larının ayrıntılı genel bakışı için bkz. [Olay Hub’larına genel bakış](event-hubs-about.md) ve [Olay Hub’ları özellikleri](event-hubs-features.md).

Bu öğreticide, bir eventhub 'e olay göndermek veya C# bir eventhub olayları almak için ' de .NET Framework konsol uygulamalarının nasıl oluşturulacağı gösterilmektedir. 

## <a name="prerequisites"></a>Ön koşullar
Bu öğreticiyi tamamlamak için aşağıdaki önkoşulları karşılamanız gerekir:

- [Microsoft Visual Studio 2019](https://visualstudio.com).
- **Event Hubs bir ad alanı ve bir olay hub 'ı oluşturun**. İlk adımda [Azure portalını](https://portal.azure.com) kullanarak Event Hubs türünde bir ad alanı oluşturun, ardından uygulamanızın olay hub’ı ile iletişim kurması için gereken yönetim kimlik bilgilerini edinin. Bir ad alanı ve Olay Hub 'ı oluşturmak için [Bu makaledeki](event-hubs-create.md)yordamı izleyin. Ardından, makaledeki yönergeleri izleyerek **Olay Hub 'ı ad alanı için bağlantı dizesini** alın: [bağlantı dizesi al](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Bu öğreticide daha sonra bağlantı dizesi kullanılır.

## <a name="send-events"></a>Olayları gönderme 
Bu bölümde, olayları bir olay hub 'ına göndermek için bir .NET Framework konsol uygulamasının nasıl oluşturulacağı gösterilmektedir. 

### <a name="create-a-console-application"></a>Konsol uygulaması oluşturma

Visual Studio'da, **Konsol Uygulaması** proje şablonunu kullanarak yeni bir Visual C# Masaüstü Uygulaması projesi oluşturun. Projeyi **Gönderen** için bir ad verin.
   
![Konsol uygulaması oluşturma](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet paketini ekleme

1. Çözüm Gezgini'nde **Gönderen** projesine sağ tıklayın ve ardından **Çözüm için NuGet Paketlerini Yönet**'e tıklayın. 
2. **Gözat** sekmesine tıklayıp `WindowsAzure.ServiceBus` için arama yapın. **Yükle**'ye tıklayın ve kullanım koşullarını kabul edin. 
   
    ![Service Bus NuGet paketini yükler](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio, [Azure Service Bus kitaplığı NuGet paketini](https://www.nuget.org/packages/WindowsAzure.ServiceBus) indirir, yükler ve ona bir başvuru ekler.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Olay hub'ına ileti göndermek için kod yazma

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Aşağıdaki alanları **Program** sınıfına ekleyin; bu işlemi yaparken yer tutucu değerlerini önceki bölümde oluşturduğunuz olay hub’ı adıyla ve daha önce kaydettiğiniz ad alanı düzeyinde bağlantı dizesiyle değiştirin. Olay Hub 'ınız için bağlantı dizesini, Azure portal olay hub 'ı sayfasında, **RootManageSharedAccessKey** altındaki **bağlantı dizesi-birincil** anahtardan kopyalayabilirsiniz. Ayrıntılı adımlar için bkz. [bağlantı dizesi al](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. **Program** sınıfına aşağıdaki yöntemi ekleyin:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Bu yöntem, olayları 200 ms'lik bir gecikmeyle sürekli olarak olay hub'ınıza gönderir.
4. Son olarak, **Main** yöntemine aşağıdaki satırları ekleyin:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Programı çalıştırın ve herhangi bir hata olmadığından emin olun.
  
## <a name="receive-events"></a>Olayları alma
Bu bölümde, [olay Işlemcisi konağını](event-hubs-event-processor-host.md)kullanarak bir olay hub 'ından iletiler alan bir .NET Framework konsol uygulaması yazarsınız. [Olay İşleyicisi Ana Bilgisayarı](event-hubs-event-processor-host.md), olay hub’larına ait kalıcı denetim noktalarını ve paralel alımları yöneterek bu olay hub’larına ait alma olaylarını basitleştiren bir .NET sınıfıdır. Olay İşleyicisi Ana Bilgisayarı’nı kullanarak, farklı düğümlerde barındırıldığında bile birden çok alıcı arasında olayları bölebilirsiniz. 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Konsol uygulaması oluşturma

Visual Studio'da, **Konsol Uygulaması** proje şablonunu kullanarak yeni Visual C# Masaüstü Uygulaması projesi oluşturun. Proje **Alıcı** için bir ad verin.
   
![Konsol uygulaması oluşturma](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet paketini ekleme

1. Çözüm Gezgini'nde **Alıcı** projesine sağ tıklayın ve ardından **Çözüm için NuGet Paketlerini Yönet**'e tıklayın.
2. **Gözat** sekmesine tıklayıp `Microsoft Azure Service Bus Event Hub - EventProcessorHost` için arama yapın. **Yükle**'ye tıklayın ve kullanım koşullarını kabul edin.
   
    ![Olay Işlemcisi Konağı NuGet paketini ara](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio, tüm bağımlılıklarıyla birlikte [Azure Service Bus Olay Hub'ı - EventProcessorHost NuGet paketini](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) indirir, yükler ve ona başvuru ekler.

### <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor arabirimini gerçekleştirme

1. **Alıcı** projesine sağ tıklayın, **Ekle** ve **Sınıf**’a tıklayın. Yeni sınıf **SimpleEventProcessor**’ı adlandırın ve ardından sınıfı oluşturmak için **Ekle**’ye tıklayın.
   
    ![SimpleEventProcessor sınıfı Ekle](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Aşağıdaki deyimleri SimpleEventProcessor.cs dosyasının en üstüne ekleyin:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Sınıfının gövdesi için aşağıdaki kodu değiştirin:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Olay hub'ından alınan olayları işlemesi için bu sınıf, **EventProcessorHost** tarafından çağrılır. `SimpleEventProcessor` sınıfı, **EventProcessorHost** bağlamında düzenli olarak denetim noktası yöntemini çağırmak için bir kronometre kullanır. Bu işlem, alıcının yeniden çağrılması durumunda, işleme sürecinde beş dakikadan fazla zaman kaybedilmemesini sağlar.

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>SimpleEventProcessor’ı kullanmak için Ana yöntemi güncelleştirme

1. **Program** sınıfında aşağıdaki `using` deyimini dosyanın üst kısmına ekleyin:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. `Program` sınıfındaki `Main` yöntemi, daha önce kaydettiğiniz Olay Hub 'ı adını ve ad alanı düzeyinde bağlantı dizesini ve önceki bölümlerde kopyaladığınız depolama hesabı ve anahtarı değiştirerek aşağıdaki kodla değiştirin. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Programı çalıştırın ve herhangi bir hata olmadığından emin olun.
  
## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleleri okuyun: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs Özellikler ve terminoloji](event-hubs-features.md).
- [Event Hubs ile ilgili SSS](event-hubs-faq.md)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com

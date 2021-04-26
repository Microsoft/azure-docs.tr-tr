---
title: .NET kullanarak Azure Event Hubs olay gönderme veya alma (en son)
description: Bu makalede, Azure Event Hubs en son Azure. Messaging. EventHubs paketini kullanarak olayları gönderen/alan bir .NET Core uygulaması oluşturmak için İzlenecek yol sunulmaktadır.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 58da331336481614cf0f85bdf6c1136c8bdc8db7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536508"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Azure Event Hubs-.NET (Azure. Messaging. EventHubs) ile olayları gönderme ve olayları alma 
Bu hızlı başlangıçta, **Azure. Messaging. EventHubs** .NET kitaplığı kullanılarak Olay Hub 'ından olayları gönderme ve olayları alma işlemlerinin nasıl yapılacağı gösterilir. 

> [!IMPORTANT]
> Bu hızlı başlangıç, yeni **Azure. Messaging. EventHubs** kitaplığını kullanır. Eski **Microsoft. Azure. EventHubs** kitaplığını kullanan bir hızlı başlangıç için bkz. [Microsoft. Azure. eventhubs kitaplığını kullanarak olay gönderme ve alma](event-hubs-dotnet-standard-get-started-send-legacy.md). 



## <a name="prerequisites"></a>Önkoşullar
Azure Event Hubs yeni başladıysanız, bu hızlı başlangıcı uygulamadan önce [Event Hubs genel bakış](event-hubs-about.md) bölümüne bakın. 

Bu hızlı başlangıcı tamamlayabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

- **Microsoft Azure aboneliği**. Azure Event Hubs dahil olmak üzere Azure hizmetlerini kullanmak için bir aboneliğiniz olması gerekir.  Mevcut bir Azure hesabınız yoksa, [ücretsiz deneme](https://azure.microsoft.com/free/) için kaydolabilir veya [BIR hesap oluştururken](https://azure.microsoft.com)MSDN abonesi avantajlarınızı kullanabilirsiniz.
- **Microsoft Visual Studio 2019**. Azure Event Hubs istemci kitaplığı, C# 8,0 ' de tanıtılan yeni özelliklerden yararlanmaktadır.  Kitaplığı önceki C# dil sürümleriyle kullanmaya devam edebilirsiniz, ancak yeni sözdizimi kullanılamaz. Tam sözdizimini kullanmak için, [.NET Core SDK](https://dotnet.microsoft.com/download) 3,0 veya üzeri ve [dil sürümü](/dotnet/csharp/language-reference/configure-language-version#override-a-default) olarak ayarlanmış bir derleme yapmanız önerilir `latest` . Visual Studio kullanıyorsanız, Visual Studio 2019 ' den önceki sürümler C# 8,0 projeleri oluşturmak için gereken araçlarla uyumlu değildir. Ücretsiz topluluk sürümü de dahil olmak üzere Visual Studio 2019 [buradan](https://visualstudio.microsoft.com/vs/)indirilebilir.
- **Event Hubs bir ad alanı ve bir olay hub 'ı oluşturun**. İlk adım, Event Hubs türünde bir ad alanı oluşturmak için [Azure Portal](https://portal.azure.com) ve uygulamanızın Olay Hub 'ı ile iletişim kurması için gereken yönetim kimlik bilgilerini elde etmek için kullanılır. Bir ad alanı ve Olay Hub 'ı oluşturmak için [Bu makaledeki](event-hubs-create.md)yordamı izleyin. Ardından, makalenin yönergelerini izleyerek **Event Hubs ad alanı için bağlantı dizesini** alın: [bağlantı dizesi al](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Bağlantı dizesini daha sonra bu hızlı başlangıçta kullanacaksınız.

## <a name="send-events"></a>Olayları gönderme 
Bu bölümde, olayları bir olay hub 'ına göndermek için bir .NET Core konsol uygulamasının nasıl oluşturulacağı gösterilmektedir. 

### <a name="create-a-console-application"></a>Konsol uygulaması oluşturma

1. Visual Studio 2019’u başlatın. 
1. **Yeni proje oluştur**' u seçin. 
1. **Yeni proje oluştur** iletişim kutusunda, aşağıdaki adımları uygulayın: Bu iletişim kutusunu görmüyorsanız menüdeki **Dosya** ' yı seçin, **Yeni**' yi seçin ve ardından **Proje**' yi seçin. 
    1. Programlama dili için **C#** ' ı seçin.
    1. Uygulamanın türü için **konsol** ' ı seçin. 
    1. Sonuçlar listesinden **konsol uygulaması (.NET Core)** seçeneğini belirleyin. 
    1. Ardından **İleri**' yi seçin. 

        ![Yeni proje iletişim kutusu](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Proje adı için **Eventhubssender** yazın, çözüm adı olarak **Eventhubsquickstart** yazın ve ardından projeyi oluşturmak için **Tamam** ' ı seçin. 

    ![C# > konsol uygulaması](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet paketini ekleme

1. Menüden **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** ' nu seçin. 
1. **Azure. Messaging. EventHubs** NuGet paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Olay hub'ına ileti göndermek için kod yazma

1. Aşağıdaki `using` deyimlerini **program. cs** dosyasının en üstüne ekleyin:

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. `Program`Event Hubs bağlantı dizesi ve Olay Hub 'ı adı için sınıfa sabitler ekleyin. Köşeli ayraçlar içindeki yer tutucuları, Olay Hub 'ı oluştururken aldığınız uygun değerlerle değiştirin. `{Event Hubs namespace connection string}` dizesinin olay hub'ı dizesi değil ad alanı düzeyi bağlantı dizesi olduğundan emin olun. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. `Main`Yöntemini aşağıdaki `async Main` yöntemle değiştirin. Ayrıntılar için bkz. kod açıklamaları. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Projeyi derleyin ve hata olmadığından emin olun.
6. Programı çalıştırın ve onay iletisini bekleyin. 
7. Azure portal, Olay Hub 'ının iletileri aldığını doğrulayabilirsiniz. **Ölçümler** bölümünde **iletiler** görünümüne geçin. Grafiği güncelleştirmek için sayfayı yenileyin. İletilerin alındığını göstermesi birkaç saniye sürebilir. 

    [![Olay Hub 'ının iletileri aldığını doğrulama](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Daha bilgilendirici açıklamalarla tüm kaynak kodu için [GitHub 'daki bu dosyaya](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md) bakın

## <a name="receive-events"></a>Olayları alma
Bu bölümde olay işlemcisi kullanarak bir olay hub 'ından iletiler alan bir .NET Core konsol uygulamasının nasıl yazılacağı gösterilmektedir. Olay işlemcisi, bu olay hub 'larından kalıcı kontrol noktaları ve paralel alım seçenekleri yöneterek Olay Hub 'larından olay almayı basitleştirir. Olay işlemcisi, belirli bir olay hub 'ı ve bir tüketici grubuyla ilişkilendirilir. Olay Hub 'ında birden çok bölümden gelen olayları alır ve bunları, sağladığınız kodu kullanarak işlemeye yönelik bir işleyici temsilcisine geçirme. 


> [!WARNING]
> Bu kodu Azure Stack hub 'da çalıştırırsanız, belirli bir depolama API sürümünü hedefetmediğiniz takdirde çalışma zamanı hatalarıyla karşılaşırsınız. Bunun nedeni, Event Hubs SDK 'sının Azure 'da kullanılabilen ve Azure Stack hub platformunda kullanılamayan en son Azure Storage API 'sini kullanması nedeniyle oluşur. Azure Stack hub, Azure 'da genel kullanıma sunulan farklı bir Storage blob SDK sürümü destekleyebilir. Azure Blob depolamayı bir denetim noktası deposu olarak kullanıyorsanız, [Azure Stack hub derlemeniz için desteklenen Azure depolama API sürümünü](/azure-stack/user/azure-stack-acs-differences?#api-version) denetleyin ve bu sürümü kodunuzda hedefleyin. 
>
> Örneğin, Azure Stack hub sürümü 2005 üzerinde çalıştırıyorsanız, depolama hizmeti için en yüksek sürüm 2019-02-02 ' dir. Event Hubs SDK istemci kitaplığı, varsayılan olarak Azure 'da kullanılabilen en yüksek sürümü (SDK 'nın sürümü sırasında 2019-07-07) kullanır. Bu durumda, bu bölümdeki adımların yanı sıra Storage Service API sürüm 2019-02-02 ' i hedeflemek için de kod eklemeniz gerekecektir. Belirli bir depolama API sürümünün nasıl hedeflenecek hakkında bir örnek için [GitHub 'da bu örneğe](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)bakın. 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure depolama ve BLOB kapsayıcısı oluşturma
Bu hızlı başlangıçta, Azure Storage 'ı denetim noktası deposu olarak kullanacaksınız. Azure depolama hesabı oluşturmak için bu adımları izleyin. 

1. [Azure Depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blob kapsayıcısı oluşturma](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Bağlantı dizesini depolama hesabına al](../storage/common/storage-configure-connection-string.md)

    Bağlantı dizesini ve kapsayıcı adını aklınızda edin. Bunları alma kodunda kullanacaksınız. 


### <a name="create-a-project-for-the-receiver"></a>Alıcı için proje oluşturma

1. Çözüm Gezgini penceresinde **Eventhubquickstart** çözümüne sağ tıklayın, **Ekle**' nin üzerine gelin ve **Yeni proje**' yi seçin. 
1. **Konsol uygulaması (.NET Core)** seçeneğini belirleyin ve **İleri**' yi seçin. 
1. **Proje adı** olarak **Eventhubsalıcı** girin ve **Oluştur**' u seçin. 

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet paketini ekleme

1. Menüden **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** ' nu seçin. 
1. **Azure. Messaging. EventHubs** NuGet paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. **Azure. Messaging. EventHubs. Processor** NuGet paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Main metodunu güncelleştirme 

1. `using` **Program. cs** dosyasının en üstüne aşağıdaki deyimleri ekleyin.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. `Program`Event Hubs bağlantı dizesi ve Olay Hub 'ı adı için sınıfa sabitler ekleyin. Köşeli ayraçlar içindeki yer tutucuları, Olay Hub 'ı oluştururken aldığınız uygun değerlerle değiştirin. Köşeli ayraçlar içindeki yer tutucuları, Olay Hub 'ı ve depolama hesabını oluştururken aldığınız uygun değerlerle değiştirin (erişim anahtarları-birincil bağlantı dizesi). `{Event Hubs namespace connection string}` dizesinin olay hub'ı dizesi değil ad alanı düzeyi bağlantı dizesi olduğundan emin olun.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. `Main`Yöntemini aşağıdaki `async Main` yöntemle değiştirin. Ayrıntılar için bkz. kod açıklamaları. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Şimdi, sınıfına aşağıdaki olay ve hata işleyicisi yöntemlerini ekleyin. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Projeyi derleyin ve hata olmadığından emin olun.

    > [!NOTE]
    > Daha bilgilendirici açıklamalarla tüm kaynak kodu için [GitHub 'daki bu dosyaya](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md)bakın.
6. Alıcı uygulamasını çalıştırın. 
1. Olayın alındığını belirten bir ileti görmeniz gerekir. 

    ![Alınan olay](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Bu olaylar, gönderen programını çalıştırarak, daha önce olay hub 'ına gönderdiğiniz üç olaydır. 


## <a name="next-steps"></a>Sonraki adımlar
GitHub 'daki örneklere göz atın. 

- [GitHub 'daki Event Hubs örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [GitHub 'daki olay işlemcisi örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Azure rol tabanlı erişim denetimi (Azure RBAC) örneği](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

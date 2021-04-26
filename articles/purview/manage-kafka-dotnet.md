---
title: .NET aracılığıyla Event Hubs aracılığıyla Azure purview 'ın Atlas Kafka konularındaki iletileri yayımlayın ve işleyin
description: Bu makalede, en son Azure. Messaging. EventHubs paketini kullanarak purview 'un Apache Atlas Kafka konularına/kaynağından olay gönderen/alan bir .NET Core uygulaması oluşturmaya yönelik izlenecek yol sunulmaktadır.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 60c177c913c78dbcfcbfe1d465044b69b0e6dd2e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590440"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>.NET aracılığıyla Event Hubs aracılığıyla Azure purview 'ın Atlas Kafka konularındaki iletileri yayımlayın ve işleyin 
Bu hızlı başlangıçta, **Azure. Messaging. EventHubs** .net kitaplığını kullanarak Olay Hub 'ı aracılığıyla Azure purview 'ın Atlas Kafka konularından olayları gönderme ve olayları alma işlemlerinin nasıl yapılacağı gösterilmektedir. 

> [!IMPORTANT]
> Yönetilen bir olay hub 'ı, purview hesabı oluşturma 'nın bir parçası olarak oluşturulur, bkz. [purview hesabı oluşturma](create-catalog-portal.md). ATLAS_HOOK Event hub Kafka konusuna iletiler yayımlayabilirsiniz ve takip eder. Purview, varlık değişikliklerini Event hub Kafka konu ATLAS_ENTITIES olarak bilgilendirir ve Kullanıcı bu uygulamayı kullanabilir ve işleyebilir. Bu hızlı başlangıç, yeni **Azure. Messaging. EventHubs** kitaplığını kullanır. 


## <a name="prerequisites"></a>Önkoşullar
Azure Event Hubs yeni başladıysanız, bu hızlı başlangıcı uygulamadan önce [Event Hubs genel bakış](../event-hubs/event-hubs-about.md) bölümüne bakın. 

Bu hızlı başlangıcı tamamlayabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

- **Microsoft Azure aboneliği**. Azure Event Hubs dahil olmak üzere Azure hizmetlerini kullanmak için bir aboneliğiniz olması gerekir.  Mevcut bir Azure hesabınız yoksa, [ücretsiz deneme](https://azure.microsoft.com/free/) için kaydolabilir veya [BIR hesap oluştururken](https://azure.microsoft.com)MSDN abonesi avantajlarınızı kullanabilirsiniz.
- **Microsoft Visual Studio 2019**. Azure Event Hubs istemci kitaplığı, C# 8,0 ' de tanıtılan yeni özelliklerden yararlanmaktadır.  Kitaplığı önceki C# dil sürümleriyle kullanmaya devam edebilirsiniz, ancak yeni sözdizimi kullanılamaz. Tam sözdizimini kullanmak için, [.NET Core SDK](https://dotnet.microsoft.com/download) 3,0 veya üzeri ve [dil sürümü](/dotnet/csharp/language-reference/configure-language-version#override-a-default) olarak ayarlanmış bir derleme yapmanız önerilir `latest` . Visual Studio kullanıyorsanız, Visual Studio 2019 ' den önceki sürümler C# 8,0 projeleri oluşturmak için gereken araçlarla uyumlu değildir. Ücretsiz topluluk sürümü de dahil olmak üzere Visual Studio 2019 [buradan](https://visualstudio.microsoft.com/vs/)indirilebilir.

## <a name="publish-messages-to-purview"></a>İletileri takip etmek için yayımlama 
Bu bölümde, Olay Hub 'ı Kafka konu **ATLAS_HOOK** aracılığıyla bir takip görünümüne olayları göndermek üzere .NET Core konsol uygulaması oluşturma gösterilmektedir. 

## <a name="create-a-visual-studio-project"></a>Visual Studio projesi oluşturma

Ardından, Visual Studio 'da bir C# .NET konsol uygulaması oluşturun:

1. **Visual Studio 'yu** başlatın.
2. Başlangıç penceresinde **Yeni proje**  >  **konsol uygulaması (.NET Framework)** oluştur ' u seçin. .NET sürüm 4.5.2 veya üzeri gereklidir.
3. **Proje adı** alanına **PurviewKafkaProducer** girin.
4. Projeyi oluşturmak için **Oluştur**'u seçin.

### <a name="create-a-console-application"></a>Konsol uygulaması oluşturma

1. Visual Studio 2019’u başlatın. 
1. **Yeni proje oluştur**' u seçin. 
1. **Yeni proje oluştur** iletişim kutusunda, aşağıdaki adımları uygulayın: Bu iletişim kutusunu görmüyorsanız menüdeki **Dosya** ' yı seçin, **Yeni**' yi seçin ve ardından **Proje**' yi seçin. 
    1. Programlama dili için **C#** ' ı seçin.
    1. Uygulamanın türü için **konsol** ' ı seçin. 
    1. Sonuçlar listesinden **konsol uygulaması (.NET Core)** seçeneğini belirleyin. 
    1. Ardından **İleri**' yi seçin. 


### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet paketini ekleme

1. Menüden **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** ' nu seçin. 
1. **Azure. Messaging. eventhubs** NuGet paketini ve **Azure. Messaging. eventhubs. Producer** NuGet paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
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

2. `Program`Event Hubs bağlantı dizesi ve Olay Hub 'ı adı için sınıfa sabitler ekleyin. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Sağlamayla hesabı ' nın Özellikler sekmesinde Atlas Kafka uç nokta birincil/ikincil bağlantı dizelerine bakarak, sağlamayla hesabı ile ilişkili olay hub 'ı ad alanını alabilirsiniz.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Olay Hub 'ı ad alanı":::

    Olay Hub 'ı adı, purview 'a ileti göndermek için **ATLAS_HOOK** olmalıdır.

3. `Main`Yöntemini aşağıdaki `async Main` yöntemle değiştirin ve `async ProduceMessage` purview 'a bir anında iletme iletisi ekleyin. Ayrıntılar için bkz. kod açıklamaları. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Projeyi derleyin ve hata olmadığından emin olun.
6. Programı çalıştırın ve onay iletisini bekleyin. 

    > [!NOTE]
    > Daha bilgilendirici açıklamalarla tüm kaynak kodu için [GitHub 'daki bu dosyaya](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md) bakın

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>İki sütunlu bir SQL tablosu oluşturmak için örnek varlık JSON iletisi oluştur.

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>Purview 'tan iletileri tüketme
Bu bölümde olay işlemcisi kullanarak bir olay hub 'ından iletiler alan bir .NET Core konsol uygulamasının nasıl yazılacağı gösterilmektedir. Purview 'dan ileti almak için ATLAS_ENTITIES Event hub 'ı kullanmanız gerekir. olay işlemcisi, bu olay hub 'larından kalıcı kontrol noktaları ve paralel alma seçenekleri yöneterek Olay Hub 'larından olay almayı basitleştirir. 

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
1. **Proje adı** için **PurviewKafkaConsumer** girin ve **Oluştur**' u seçin. 

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
    
    Sağlamayla hesabı ' nın Özellikler sekmesinde Atlas Kafka uç nokta birincil/ikincil bağlantı dizelerine bakarak, sağlamayla hesabı ile ilişkili olay hub 'ı ad alanını alabilirsiniz.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Olay Hub 'ı ad alanı":::

    Olay Hub 'ı adı, purview 'a ileti göndermek için **ATLAS_ENTITIES** olmalıdır.

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

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

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

### <a name="sample-message-received-from-purview"></a>Purview 'dan alınan örnek Ileti

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas Şu anda şu işlem türlerini destekliyor: **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2** **ENTITY_DELETE_V2**. İletileri takip edilecek şekilde iletme, şu anda varsayılan olarak etkindir. Senaryo, takip etenden okumayı içeriyorsa, izin verilenler listesinde olması gerektiğinden bizimle iletişim kurun. (abonelik kimliğini ve purview hesabının adını belirtin).


## <a name="next-steps"></a>Sonraki adımlar
GitHub 'daki örneklere göz atın. 

- [GitHub 'daki Event Hubs örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [GitHub 'daki olay işlemcisi örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Atlas bildirimlere giriş](https://atlas.apache.org/2.0.0/Notifications.html)
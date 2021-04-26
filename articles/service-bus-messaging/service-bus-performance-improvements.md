---
title: Azure Service Bus kullanarak performansı iyileştirmeye yönelik en iyi uygulamalar
description: Aracılı iletileri değiş tokuşu yaparken performansı iyileştirmek için Service Bus nasıl kullanılacağını açıklar.
ms.topic: article
ms.date: 03/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: d4093d93da11e992ed9e6558a5386eb88f417ef9
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967770"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Service Bus mesajlaşma kullanarak performans iyileştirmeleri için en iyi uygulamalar

Bu makalede, aracılı iletileri değiş tokuşu yaparken performansı iyileştirmek için Azure Service Bus nasıl kullanılacağı açıklanır. Bu makalenin ilk bölümünde, performansı artırmak için farklı mekanizmalar açıklanmıştır. İkinci bölüm, belirli bir senaryoda en iyi performansı sunabileceği şekilde Service Bus kullanımı hakkında rehberlik sağlar.

Bu makale boyunca, "Client" terimi Service Bus erişen herhangi bir varlığa başvurur. Bir istemci, gönderenin veya alıcının rolünü alabilir. "Gönderici" terimi, bir Service Bus kuyruğuna veya bir konuya ileti gönderen bir Service Bus kuyruğu istemcisi ya da bir konu istemcisi için kullanılır. "Alıcı" terimi, bir Service Bus kuyruğundan veya bir abonelikten ileti alan Service Bus kuyruğu istemci veya abonelik istemcisine başvurur.

## <a name="protocols"></a>Protokoller
Service Bus, istemcilerin üç protokolden birini kullanarak ileti göndermesini ve almasını sağlar:

1. Gelişmiş İleti Sıraya Alma Protokolü (AMQP)
2. Service Bus mesajlaşma Protokolü (SBMP)
3. Köprü Metni Aktarım Protokolü (HTTP)

Service Bus bağlantısını sürdürdüğü için AMQP en verimli yoldur. [Toplu işleme](#batching-store-access) ve [önceden getirme](#prefetching)de uygular. Açıkça belirtilmedikçe, bu makaledeki tüm içerikler AMQP veya SBMP kullanımını varsayar.

> [!IMPORTANT]
> SBMP yalnızca .NET Framework için kullanılabilir. AMQP, .NET Standard için varsayılandır.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Uygun Service Bus .NET SDK 'sını seçme
Desteklenen üç Azure Service Bus .NET SDK 'Sı vardır. API 'Leri benzerdir ve hangisinin seçeceğiniz kafa karıştırıcı olabilir. Kararınızı göstermeye yardımcı olması için aşağıdaki tabloya bakın. Azure. Messaging. ServiceBus SDK, en son ve diğer SDK 'larda kullanılması önerilir. Hem Azure. Messaging. ServiceBus hem de Microsoft. Azure. ServiceBus SDK 'Ları modern, performanslı ve platformlar arası uyumludur. Ayrıca, WebSockets üzerinden AMQP destekleirler ve açık kaynaklı projelerin Azure .NET SDK koleksiyonunun bir parçasıdır.

| NuGet paketi | Birincil ad alanı (ler) | Minimum platform (ler) | Protokoller |
|---------------|----------------------|---------------------|-------------|
| [Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) | `Azure.Messaging.ServiceBus`<br>`Azure.Messaging.ServiceBus.Administration` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5,4<br>Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Evrensel Windows Platformu 10.0.16299 | AMQP<br>HTTP |
| [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5,4<br>Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Evrensel Windows Platformu 10.0.16299 | AMQP<br>HTTP |
| [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

En düşük .NET Standard platform desteği hakkında daha fazla bilgi için bkz. [.NET uygulama desteği](/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Fabrikaları ve istemcileri yeniden kullanma
# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
[Servicebusclient](/dotnet/api/azure.messaging.servicebus.servicebusclient), [servicebussender](/dotnet/api/azure.messaging.servicebus.servicebussender), [Servicebusalıcısı](/dotnet/api/azure.messaging.servicebus.servicebusreceiver)ve [servicebusprocessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)gibi hizmetle etkileşime geçen Service Bus nesneleri, tek tonlar olarak bağımlılık ekleme (veya bir kez örneklenmiş ve paylaşılan) için kaydedilmelidir. ServiceBusClient, [Servicebusclientbuilderextensions](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/src/Compatibility/ServiceBusClientBuilderExtensions.cs)ile bağımlılık ekleme için kaydedilebilir. 

Her iletiyi gönderdikten veya aldıktan sonra bu nesneleri kapatmayın veya atmemenizi öneririz. Varlığa özgü nesneleri kapatma veya elden atma (ServiceBusSender/alıcı/Işlemci), Service Bus hizmetine olan bağlantıyı aşağı doğru bir şekilde kapatıyor. ServiceBusClient, Service Bus hizmetine yapılan bağlantıyı aşağı doğru bir şekilde elden atılıyor. 

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Veya uygulamaları gibi istemci nesnelerinin Service Bus [`IQueueClient`][QueueClient] [`IMessageSender`][MessageSender] , tek tonlar olarak bağımlılık ekleme (veya bir kez örneklenmiş ve paylaşılan) için kaydedilmesi gerekir. İletiyi gönderdikten sonra ileti fabrikalarını, kuyruğu, konuyu veya abonelik istemcilerini kapatmemenizi ve sonraki iletiyi gönderdiğinizde yeniden oluşturmanız önerilir. Bir ileti fabrikası kapatıldığında Service Bus hizmetine olan bağlantı silinir. Fabrika yeniden oluşturulurken yeni bir bağlantı oluşturulur. 

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK 'Sı](#tab/net-framework-sdk)

Ya da gibi istemci nesneleri Service Bus `QueueClient` , `MessageSender` bağlantı iç yönetimi de sağlayan bir [messagingfactory][MessagingFactory] nesnesi aracılığıyla oluşturulur. İletiyi gönderdikten sonra ileti fabrikalarını, kuyruğu, konuyu veya abonelik istemcilerini kapatmemenizi ve sonraki iletiyi gönderdiğinizde yeniden oluşturmanız önerilir. Bir mesajlaşma fabrikasının kapatılması Service Bus hizmetine olan bağlantıyı siler ve fabrikası yeniden oluştururken yeni bir bağlantı oluşturulur. 

---

Aşağıdaki notta tüm SDK 'lar için geçerli olur:

> [!NOTE]
> Bir bağlantı kurmak, birden çok işlem için aynı fabrika ve istemci nesnelerini yeniden kullandığınızda kaçınmanın pahalı bir işlemdir. Bu istemci nesnelerini, eşzamanlı zaman uyumsuz işlemler ve birden çok iş parçacığından güvenle kullanabilirsiniz.

## <a name="concurrent-operations"></a>Eş zamanlı işlemler
Gönderme, alma, silme, vb. gibi işlemler, biraz zaman alabilir. Bu süre, Service Bus hizmetinin işlemi ve isteğin gecikme süresini ve yanıtı işlemek için aldığı süreyi içerir. Zaman başına işlem sayısını artırmak için, işlemlerin eşzamanlı olarak yürütülmesi gerekir.

İstemci **zaman uyumsuz** işlemler gerçekleştirerek eşzamanlı işlemleri zamanlar. Sonraki istek, önceki istek tamamlanmadan önce başlatılır. Aşağıdaki kod parçacığı, zaman uyumsuz gönderme işlemine bir örnektir:

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
```csharp
var messageOne = new ServiceBusMessage(body);
var messageTwo = new ServiceBusMessage(body);

var sendFirstMessageTask =
    sender.SendMessageAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    sender.SendMessageAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");

```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK 'Sı](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

Aşağıdaki kod, zaman uyumsuz alma işlemine bir örnektir.

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)

```csharp
var client = new ServiceBusClient(connectionString);
var options = new ServiceBusProcessorOptions 
{

      AutoCompleteMessages = false,
      MaxConcurrentCalls = 20
};
await using ServiceBusProcessor processor = client.CreateProcessor(queueName,options);
processor.ProcessMessageAsync += MessageHandler;
processor.ProcessErrorAsync += ErrorHandler;

static Task ErrorHandler(ProcessErrorEventArgs args)
{
    Console.WriteLine(args.Exception);
    return Task.CompletedTask;
};

static async Task MessageHandler(ProcessMessageEventArgs args)
{
    Console.WriteLine("Handle message");
    await args.CompleteMessageAsync(args.Message);
}

await processor.StartProcessingAsync();
```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Tam <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">kaynak kodu örnekleri <span class="docon docon-navigate-external x-hidden-focus"></span> </a>için GitHub deposuna bakın:

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

`MessageReceiver`Nesne, bağlantı dizesi, kuyruk adı ve bir göz atma alma moduyla birlikte oluşturulur. Sonra, `receiver` örnek ileti işleyicisini kaydetmek için kullanılır.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK 'Sı](#tab/net-framework-sdk)

Tam <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">kaynak kodu örnekleri <span class="docon docon-navigate-external x-hidden-focus"></span> </a>için GitHub deposuna bakın:

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

, `MessagingFactory` `factory` Bağlantı dizesinden bir nesne oluşturur. `factory`Örneğinde, bir örneği `MessageReceiver` oluşturulur. Ardından, `receiver` örnek ileti işleyicisini kaydetmek için kullanılır.

---

## <a name="receive-mode"></a>Alma modu

Bir kuyruk veya abonelik istemcisi oluştururken, bir alma modu belirtebilirsiniz: *Peek-kilitle* veya *al ve Sil*. Varsayılan alma modu `PeekLock` . Varsayılan modda çalışırken, istemci Service Bus ileti alma isteği gönderir. İstemci iletiyi aldıktan sonra iletiyi tamamlamaya yönelik bir istek gönderir.

Alma modu olarak ayarlandığında `ReceiveAndDelete` , her iki adım tek bir istekte birleştirilir. Bu adımlar genel işlem sayısını azaltır ve genel ileti işleme hızını iyileştirebilir. Bu performans kazancı, iletileri kaybetme riskiyle gelir.

Service Bus, alma ve silme işlemlerine yönelik işlemleri desteklemez. Ayrıca, istemcinin bir iletiyi erteleme veya [atılacak](service-bus-dead-letter-queues.md) bir ileti almak istediği her senaryo için Peek kilit semantiği gerekir.

## <a name="client-side-batching"></a>İstemci tarafı toplu işleme

İstemci tarafı toplu işleme, bir kuyruk veya konu istemcisinin belirli bir süre için ileti gönderilmesini geciktirmesini sağlar. İstemci bu süre içinde başka iletiler gönderirse, iletileri tek bir toplu iş olarak gönderir. İstemci tarafı toplu işleme Ayrıca bir kuyruk veya abonelik istemcisinin birden çok **tamamlanmış** isteği tek bir istek halinde toplu olarak yığın oluşturmasına neden olur. Toplu işleme yalnızca zaman uyumsuz **gönderme** ve **Tamamlanan** işlemler için kullanılabilir. Zaman uyumlu işlemler hemen Service Bus hizmetine gönderilir. Toplu işleme, göz atma veya alma işlemleri veya istemciler arasında toplu işlem gerçekleşmiyor.

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
.NET Standard SDK için toplu işlem işlevselliği henüz işlemek üzere bir özellik sunmaz.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

.NET Standard SDK için toplu işlem işlevselliği henüz işlemek üzere bir özellik sunmaz.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK 'Sı](#tab/net-framework-sdk)

Varsayılan olarak, bir istemci 20 MS toplu iş aralığı kullanır. İleti fabrikası oluşturmadan önce [Batchflushınterval][BatchFlushInterval] özelliğini ayarlayarak Batch aralığını değiştirebilirsiniz. Bu ayar, bu fabrika tarafından oluşturulan tüm istemcileri etkiler.

Toplu işlemeyi devre dışı bırakmak için [Batchflushınterval][BatchFlushInterval] özelliğini **TimeSpan. Zero** olarak ayarlayın. Örnek:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

Toplu işlem, faturalandırılabilir mesajlaşma işlemlerinin sayısını etkilemez ve yalnızca [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) kitaplığını kullanan Service Bus istemci protokolü için kullanılabilir. HTTP protokolü toplu işlemeyi desteklemiyor.

> [!NOTE]
> Ayar `BatchFlushInterval` , toplu işleme uygulamanın perspektifinden örtük olmasını sağlar. ör.; uygulama, `SendAsync` ve `CompleteAsync` çağırır ve belirli toplu çağrıları yapmaz.
>
> Açık istemci tarafı toplu işleme, aşağıdaki yöntem çağrısının kullanılmasıyla uygulanabilir:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Burada iletilerin Birleşik boyutunun fiyatlandırma katmanının desteklediği en büyük boyuttan küçük olması gerekir.

---

## <a name="batching-store-access"></a>Depo erişimini toplu işleme

Bir kuyruğun, konunun veya aboneliğin aktarım hızını artırmak için Service Bus, iç deposuna yazarken birden çok ileti işler. 

- Bir kuyrukta toplu işlemeyi etkinleştirdiğinizde, depoya ileti yazma ve depodan ileti silme toplu olarak oluşturulur. 
- Bir konu üzerinde toplu işlemeyi etkinleştirdiğinizde, depoya ileti yazmak toplu olarak oluşturulur. 
- Bir abonelikte toplu işlemeyi etkinleştirdiğinizde, depodan ileti silme toplu olarak oluşturulur. 
- Bir varlık için toplu depolama erişimi etkinleştirildiğinde, Service Bus bu varlık için bir mağaza yazma işlemini 20 MS 'ye kadar geciktirir.

> [!NOTE]
> 20 MS toplu işlem aralığının sonunda Service Bus hatası olsa bile, toplu işleme ile iletileri kaybetme riski yoktur.

Bu Aralık süresince gerçekleşen ek mağaza işlemleri toplu işe eklenir. Toplu depolama erişimi yalnızca **gönderme** ve **tamamlanma** işlemlerini etkiler; alma işlemleri etkilenmez. Toplu depo erişimi, bir varlık üzerindeki bir özelliktir. Toplu işleme, toplu depo erişimini etkinleştiren tüm varlıklarda oluşur.

Yeni bir kuyruk, konu veya abonelik oluştururken, toplu mağaza erişimi varsayılan olarak etkindir.


# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
Toplu depo erişimini devre dışı bırakmak için bir örneğine ihtiyacınız vardır `ServiceBusAdministrationClient` . `CreateQueueOptions`Özelliğini olarak ayarlayan bir sıra açıklamasıyla oluşturun `EnableBatchedOperations` `false` .

```csharp
var options = new CreateQueueOptions(path)
{
    EnableBatchedOperations = false
};
var queue = await administrationClient.CreateQueueAsync(options);
```


# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Toplu depo erişimini devre dışı bırakmak için bir örneğine ihtiyacınız vardır `ManagementClient` . Özelliği olarak ayarlayan bir sıra açıklamasıyla kuyruk oluşturun `EnableBatchedOperations` `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK 'Sı](#tab/net-framework-sdk)

Toplu depo erişimini devre dışı bırakmak için bir örneğine ihtiyacınız vardır `NamespaceManager` . Özelliği olarak ayarlayan bir sıra açıklamasıyla kuyruk oluşturun `EnableBatchedOperations` `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

Toplu depo erişimi, faturalandırılabilir mesajlaşma işlemlerinin sayısını etkilemez. Bu, bir kuyruğun, konunun veya aboneliğin bir özelliğidir. Bu, alma modundan ve bir istemci ile Service Bus hizmeti arasında kullanılan protokolden bağımsızdır.

## <a name="prefetching"></a>Önceden getiriliyor

[Önceden getirme](service-bus-prefetch.md) , kuyruk veya abonelik istemcisinin ileti aldığında hizmetten ek iletiler yüklemesine olanak sağlar. İstemci bu iletileri yerel bir önbellekte depolar. Önbelleğin boyutu `QueueClient.PrefetchCount` veya özellikleri tarafından belirlenir `SubscriptionClient.PrefetchCount` . Ön getirmeyi sağlayan her istemci kendi önbelleğini korur. Bir önbellek istemciler arasında paylaşılmaz. İstemci bir alma işlemi başlattığında ve önbelleği boşsa, hizmet bir toplu ileti iletir. Toplu iş boyutu, Önbelleğin boyutuna veya 256 KB değerine eşitse, hangisi daha küçüktür. İstemci bir alma işlemi başlattığında ve önbellek bir ileti içeriyorsa, ileti önbellekten alınır.

Bir ileti önceden getirilme durumunda hizmet, önceden getirilen iletiyi kilitler. Kilit ile, önceden getirilen ileti farklı bir alıcı tarafından alınamaz. Alıcı, kilidin süresi dolmadan önce iletiyi tamamlayamadığında ileti diğer alıcılar tarafından kullanılabilir hale gelir. İletinin önceden getirilen kopyası önbellekte kalır. Önbelleğe alınmış önbelleğe alınmış kopyayı tüketen alıcı, iletiyi tamamlamaya çalıştığında bir özel durum alır. Varsayılan olarak, ileti kilidinin 60 saniye sonra süresi dolar. Bu değer 5 dakikaya genişletilebilir. Süresi dolma iletilerinin kullanımını engellemek için, önbellek boyutunu istemcinin kilit zaman aşımı aralığı içinde tüketebileceği ileti sayısından küçük olarak ayarlayın.

60 saniyelik varsayılan kilit sona erme süresini kullanırken, için iyi bir değer, `PrefetchCount` fabrika 'nin tüm alıcılarının en yüksek işlem hızlarının 20 katı olur. Örneğin, bir fabrika üç alıcı oluşturuyor ve her alıcı saniyede en fazla 10 ileti işleyebilir. Önceden getirme sayısı 20 X 3 X 10 = 600 ' i aşmamalıdır. Varsayılan olarak, `PrefetchCount` 0 olarak ayarlanır; Bu, hizmetten başka hiçbir ileti getirilmediği anlamına gelir.

İletileri önceden getirme, ileti işlemlerinin genel sayısını veya gidiş dönüş sayısını azalttığından bir sıranın veya aboneliğin genel aktarım hızını artırır. Ancak ilk iletinin getirilmesi daha uzun sürer (daha fazla ileti boyutu nedeniyle). Bu iletiler istemci tarafından zaten indirildiğinden önbellekten önceden getirilen iletilerin alınması daha hızlı olacaktır.

Bir iletinin yaşam süresi (TTL) özelliği, sunucunun istemciye ileti göndermesi sırasında sunucu tarafından denetlenir. İleti alındığında istemci iletinin TTL özelliğini denetlemez. Bunun yerine ileti, ileti istemci tarafından önbelleğe alındıktan sonra iletinin TTL 'SI geçtiğinde bile alınabilir.

Önceden getirme, faturalandırılabilir mesajlaşma işlemlerinin sayısını etkilemez ve yalnızca Service Bus istemci protokolü için kullanılabilir. HTTP protokolü, ön getirmeyi desteklemiyor. Önceden getirme, hem zaman uyumlu hem de zaman uyumsuz alma işlemleri için kullanılabilir.

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
Daha fazla bilgi için aşağıdaki özelliklere bakın `PrefetchCount` :

- [Servicebusalıcı. PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount)
- [ServiceBusProcessor. PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount)

Bu özelliklerin değerlerini [ServiceBusReceiverOptions](/dotnet/api/azure.messaging.servicebus.servicebusreceiveroptions) veya [Servicebusprocessoroptions](/dotnet/api/azure.messaging.servicebus.servicebusprocessoroptions)' da ayarlayabilirsiniz.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Daha fazla bilgi için aşağıdaki özelliklere bakın `PrefetchCount` :

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK 'Sı](#tab/net-framework-sdk)

Daha fazla bilgi için aşağıdaki özelliklere bakın `PrefetchCount` :

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Ön getirme ve ReceiveBatch
Birden çok iletiyi önceden getirme kavramlarının bir toplu işte () iletileri işlemeye benzer semantiklerine sahip olsa `ReceiveBatch` da, bu yaklaşımları birlikte kullanırken aklınızda tutulması gereken küçük farklılıklar vardır.

Önceden getirme, istemcideki bir yapılandırma (veya mod) `QueueClient` `SubscriptionClient` ve `ReceiveBatch` bir işlemdir (istek-yanıt semantiğini içeren).

Bu yaklaşımları birlikte kullanırken, aşağıdaki durumları göz önünde bulundurun-

* Önceden getirme, alma beklediğiniz ileti sayısından büyük veya buna eşit olmalıdır `ReceiveBatch` .
* Önceden getirme, saniye başına işlenen ileti sayısının en fazla n/3 katı olabilir, burada n varsayılan kilit süresi olur.

Bir doyumsuz yaklaşımına sahip olan bazı sorunlar vardır, yani önceden getirme sayısının yüksek tutulması, iletinin belirli bir alıcıya kilitli olması anlamına gelir. Bu öneri, yukarıda bahsedilen eşikler arasında önceden getirme değerlerini denemek ve neyin uygun olduğunu belirlemektir.

## <a name="multiple-queues"></a>Birden çok kuyruk

Tek bir kuyruk veya konu beklenen bir şekilde işleyemiyorsa, birden çok mesajlaşma varlığı kullanın. Birden çok varlık kullanırken, tüm varlıklar için aynı istemciyi kullanmak yerine, her varlık için ayrılmış bir istemci oluşturun.

## <a name="development-and-testing-features"></a>Geliştirme ve test özellikleri

> [!NOTE]
> Bu bölüm yalnızca WindowsAzure. ServiceBus SDK için geçerlidir, Microsoft. Azure. ServiceBus ve Azure. Messaging. ServiceBus bu işlevselliği sunmaz.

Service Bus, özellikle geliştirme için kullanılan ve **Üretim yapılandırmalarında hiçbir şekilde kullanılmamalıdır** olan bir özelliğe sahiptir: [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] .

Konuya yeni kurallar veya filtreler eklendiğinde, [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] Yeni filtre ifadesinin beklendiği gibi çalıştığını doğrulamak için kullanabilirsiniz.

## <a name="scenarios"></a>Senaryolar

Aşağıdaki bölümlerde tipik mesajlaşma senaryoları açıklanır ve tercih edilen Service Bus ayarları ana hatlarıyla verilmiştir. Aktarım hızı ücretleri küçük (1 ileti/saniye), Orta (1 ileti/saniye veya daha fazla, 100 ileti/saniye) ve yüksek (100 mesaj/saniye veya daha büyük) olarak sınıflandırılır. İstemci sayısı küçük (5 veya daha az), Orta (5 ' ten fazla veya daha az) ve büyük (20 ' den fazla) olarak sınıflandırılır.

### <a name="high-throughput-queue"></a>Yüksek aktarım hızı kuyruğu

Hedef: tek bir sıranın verimini en üst düzeye çıkarın. Gönderenlerin ve alıcıların sayısı küçüktür.

* Genel gönderme oranını sıraya göre artırmak için, göndericiler oluşturmak üzere birden çok ileti fabrikası kullanın. Her gönderici için zaman uyumsuz işlemler veya birden çok iş parçacığı kullanın.
* Kuyruktaki genel alma hızını artırmak için, alıcı oluşturmak üzere birden çok ileti fabrikası kullanın.
* İstemci tarafı toplu işleme avantajlarından yararlanmak için zaman uyumsuz işlemler kullanın.
* Service Bus istemci protokol iletimi sayısını azaltmak için toplu işlem aralığını 50 ms olarak ayarlayın. Birden çok gönderici kullanılıyorsa, toplu işlem aralığını 100 ms olarak artırın.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, iletilerin sıraya yazılabileceği genel oranı artırır.
* Önceden getirme sayısını, bir fabrika 'nin tüm alıcılarının maksimum işlem hızlarına 20 kez ayarlayın. Bu sayı, Service Bus istemci protokol iletimi sayısını azaltır.

### <a name="multiple-high-throughput-queues"></a>Birden çok yüksek işleme kuyruğu

Hedef: birden çok sıranın genel verimini en üst düzeye çıkarın. Tek bir kuyruğun üretilen işi orta veya yüksek.

Birden çok sırada en yüksek aktarım hızını elde etmek için, tek bir sıranın verimini en üst düzeye çıkarmak üzere özetlenen ayarları kullanın. Ayrıca, farklı kuyruklardan gönderen veya alan istemcileri oluşturmak için farklı fabrikalar kullanın.

### <a name="low-latency-queue"></a>Düşük gecikme süresi kuyruğu

Hedef: bir kuyruğun veya konunun gecikmesini en aza Indirir. Gönderenlerin ve alıcıların sayısı küçüktür. Kuyruğun üretilen işi küçük veya orta.

* İstemci tarafı toplu işlemeyi devre dışı bırakın. İstemci hemen bir ileti gönderir.
* Toplu depo erişimini devre dışı bırakın. Hizmet iletiyi depoya hemen yazar.
* Tek bir istemci kullanıyorsanız, önceden getirme sayısını alıcının işleme hızının 20 katı olarak ayarlayın. Aynı anda sıraya birden çok ileti ulaştığında, Service Bus istemci protokolü bunları aynı anda iletir. İstemci bir sonraki iletiyi aldığında, bu ileti zaten yerel önbellekte bulunur. Önbelleğin küçük olması gerekir.
* Birden çok istemci kullanılıyorsa, önceden getirme sayısını 0 olarak ayarlayın. Sayıyı ayarlayarak ikinci istemci ilk iletiyi hala işlerken ikinci iletiyi alabilir.

### <a name="queue-with-a-large-number-of-senders"></a>Çok sayıda gönderici içeren kuyruk

Hedef: bir kuyruğun veya konunun iş verimini çok sayıda gönderen ile en üst düzeye çıkarın. Her gönderici, bir orta oranına sahip iletiler gönderir. Alıcı sayısı küçüktür.

Service Bus, bir mesajlaşma varlığına 1000 adede kadar eşzamanlı bağlantı sağlar. Bu sınır ad alanı düzeyinde zorlanır, kuyruklar, konular veya abonelikler ad alanı başına eşzamanlı bağlantı sınırına göre belirlenir. Kuyruklar için bu sayı, Gönderenler ve alıcılar arasında paylaşılır. Göndericiler için tüm 1000 bağlantıları gerekliyse, kuyruğu bir konuyla ve tek bir abonelikle değiştirin. Bir konu, gönderenlerden en fazla 1000 eşzamanlı bağlantı kabul eder. Abonelik, alıcıların diğer 1000 eşzamanlı bağlantılarını kabul eder. 1000 'den fazla eşzamanlı gönderici gerekliyse, göndericiler HTTP aracılığıyla Service Bus protokolüne ileti göndermelidir.

Aktarım hızını en üst düzeye çıkarmak için şu adımları izleyin:

* Her Gönderici farklı bir işlemden farklıysa, işlem başına yalnızca tek bir fabrika kullanın.
* İstemci tarafı toplu işleme avantajlarından yararlanmak için zaman uyumsuz işlemler kullanın.
* Service Bus istemci protokol iletimi sayısını azaltmak için 20 MS varsayılan toplu işlem aralığını kullanın.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, iletilerin kuyruğa veya konuya yazıtırabileceği genel oranı artırır.
* Önceden getirme sayısını, bir fabrika 'nin tüm alıcılarının maksimum işlem hızlarına 20 kez ayarlayın. Bu sayı, Service Bus istemci protokol iletimi sayısını azaltır.

### <a name="queue-with-a-large-number-of-receivers"></a>Çok sayıda alıcı içeren kuyruk

Hedef: çok sayıda alıcıya sahip bir kuyruğun veya aboneliğin alma oranını en üst düzeye çıkarın. Her alıcı, iletileri orta bir hızda alır. Gönderenlerin sayısı küçüktür.

Service Bus, bir varlığa en fazla 1000 eşzamanlı bağlantı sağlar. Bir kuyruk 1000 'den fazla alıcı gerektiriyorsa, kuyruğu bir konuyla ve birden çok aboneliğe göre değiştirin. Her abonelik 1000 adede kadar eşzamanlı bağlantıyı destekleyebilir. Alternatif olarak, alıcılar kuyruğa HTTP protokolü aracılığıyla erişebilir.

Aktarım hızını en üst düzeye çıkarmak için aşağıdaki yönergeleri izleyin:

* Her alıcı farklı bir işlemdir, işlem başına yalnızca tek bir fabrika kullanın.
* Alıcılar, zaman uyumlu veya zaman uyumsuz işlemler kullanabilir. Tek bir alıcının orta alım oranı verildiğinde, tam bir isteğin istemci tarafında toplu işleme alıcı aktarım hızını etkilemez.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, varlığın genel yükünü azaltır. Ayrıca, iletilerin kuyruğa veya konuya yazılma hızının genel oranını azaltır.
* Önceden getirme sayısını küçük bir değere ayarlayın (örneğin, PrefetchCount = 10). Bu sayı, diğer alıcıların önbelleğe alınmış çok sayıda iletisi olduğunda alıcıların boşta kalmasını engeller.

### <a name="topic-with-a-few-subscriptions"></a>Birkaç aboneliğe sahip konu

Hedef: birkaç abonelikle bir konunun verimini en üst düzeye çıkarın. Birçok abonelik tarafından bir ileti alınır; bu, tüm aboneliklerdeki Birleşik Alım oranının gönderme hızından büyük olduğu anlamına gelir. Gönderenlerin sayısı küçüktür. Abonelik başına alıcı sayısı küçüktür.

Aktarım hızını en üst düzeye çıkarmak için aşağıdaki yönergeleri izleyin:

* Genel gönderme oranını konuya yükseltmek için, gönderici oluşturmak üzere birden çok ileti fabrikası kullanın. Her gönderici için zaman uyumsuz işlemler veya birden çok iş parçacığı kullanın.
* Bir abonelikteki genel alma hızını artırmak için, alıcılar oluşturmak üzere birden çok ileti fabrikası kullanın. Her alıcı için zaman uyumsuz işlemler veya birden çok iş parçacığı kullanın.
* İstemci tarafı toplu işleme avantajlarından yararlanmak için zaman uyumsuz işlemler kullanın.
* Service Bus istemci protokol iletimi sayısını azaltmak için 20 MS varsayılan toplu işlem aralığını kullanın.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, iletilerin konuya yazıtırabileceği genel oranı artırır.
* Önceden getirme sayısını, bir fabrika 'nin tüm alıcılarının maksimum işlem hızlarına 20 kez ayarlayın. Bu sayı, Service Bus istemci protokol iletimi sayısını azaltır.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Çok sayıda aboneliğe sahip konu

Hedef: çok sayıda aboneliğe sahip bir konunun verimini en üst düzeye çıkarın. Birçok abonelik tarafından bir ileti alınır. Bu, tüm aboneliklerdeki Birleşik Alım oranının gönderme hızından çok daha büyük olduğu anlamına gelir. Gönderenlerin sayısı küçüktür. Abonelik başına alıcı sayısı küçüktür.

Çok sayıda aboneliğe sahip olan konular genellikle tüm iletiler tüm aboneliklere yönlendirilse genel olarak düşük bir verimlilik sunar. Bunun nedeni, her iletinin birçok kez alınması ve bir konudaki tüm iletilerin ve tüm aboneliklerinin aynı depoda depolanmasıdır. Buradaki varsayım, her abonelik için gönderen ve alıcı sayısı küçük olduğunda oluşur. Service Bus, konu başına en fazla 2.000 aboneliği destekler.

Aktarım hızını en üst düzeye çıkarmak için aşağıdaki adımları deneyin:

* İstemci tarafı toplu işleme avantajlarından yararlanmak için zaman uyumsuz işlemler kullanın.
* Service Bus istemci protokol iletimi sayısını azaltmak için 20 MS varsayılan toplu işlem aralığını kullanın.
* Toplu mağaza erişimini devre dışı bırakın. Bu erişim, iletilerin konuya yazıtırabileceği genel oranı artırır.
* Ön hazırlık sayısını saniye cinsinden beklenen alma hızının 20 katı olarak ayarlayın. Bu sayı, Service Bus istemci protokol iletimi sayısını azaltır.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md

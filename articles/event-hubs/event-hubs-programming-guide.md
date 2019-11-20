---
title: .NET programlama kılavuzu-Azure Event Hubs | Microsoft Docs
description: Bu makalede kod yazmak için nasıl Azure Event Hubs için Azure .NET SDK'sı kullanarak bilgi sağlanır.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 09/25/2019
ms.author: shvija
ms.openlocfilehash: c2e23c38abbec5fd0e6010bdfc0feca882a6180d
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309826"
---
# <a name="net-programming-guide-for-azure-event-hubs"></a>Azure Event Hubs için .NET Programlama Kılavuzu
Bu makalede, Azure Event Hubs'ı kullanarak kod yazma bazı yaygın senaryolar açıklanmaktadır. Burada Event Hubs’ın önceden bilindiği varsayılır. Event Hubs’a kavramsal genel bakış için bkz. [Event Hubs’a genel bakış](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Olay yayımcıları

Olaylar, olay hub'ına ya da HTTP POST kullanılarak veya bir AMQP 1.0 bağlantısı üzerinden gönder. Seçimi kullanılacağı ve ne zaman çalışılmaktadır belirli bir senaryoya bağlıdır. AMQP 1.0 bağlantıları Service Bus içinde aracılı bağlantılar olarak ölçülür ve sıklıkla daha yüksek ileti hacimlerine ve düşük gecikme gereksinimlerine sahip senaryolar kalıcı bir mesajlaşma kanalı sağladığından bu senaryolarda daha uygundur.

.NET ile yönetilen API’ler kullanılırken Event Hubs’a veri yayımlamaya yönelik birincil yapılar [EventHubClient][] ve [EventData][] sınıflarıdır. [EventHubClient][] üzerinde olayları olay hub'ına gönderildiği AMQP iletişim kanalını sağlar. [EventData][] sınıfı bir olayı temsil eder ve olay hub'ına iletileri yayımlamak için kullanılır. Bu sınıf, olayla ilgili olarak gövde, bazı meta veriler (Özellikler) ve başlık bilgilerini (SystemProperties) içerir. Diğer özellikler eklenir [EventData][] bir olay hub'ından geçtikçe nesne.

## <a name="get-started"></a>başlarken
Event Hubs sunulmaktadır destekleyen .NET sınıfları [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet paketi. Visual Studio Çözüm Gezgini'ni kullanarak yükleyebilirsiniz veya [Paket Yöneticisi Konsolu](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) Visual Studio'da. Bunu yapmak için [Paket Yöneticisi Konsolu](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) penceresinde aşağıdaki komutu yürütün:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Event Hubs oluşturmak için Azure portal, Azure PowerShell veya Azure CLI'yı kullanabilirsiniz. Ayrıntılar için bkz [bir Event Hubs ad alanı ve Azure portalını kullanarak bir olay hub'ı oluşturma](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Event Hubs istemcisi oluşturma

Event Hubs ile etkileşim kurmaya yönelik birincil sınıf [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Kullanarak bu sınıfın örneğini oluşturabilir [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) aşağıdaki örnekte gösterildiği gibi yöntemi:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Olay hub'ına olayları gönderme

Oluşturarak olayları bir event hub'ına gönderme bir [EventHubClient][] örneği ve zaman uyumsuz olarak aracılığıyla göndermeden [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) yöntemi. Bu yöntem tek bir alan [EventData][] Instance parametresine ve olay hub'ına zaman uyumsuz olarak gönderir.

## <a name="event-serialization"></a>Olayı seri hale getirme

[EventData][] sınıfında [aşırı yüklü iki Oluşturucu](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) çeşitli parametreler, bayt veya olay veri yükü temsil eden bir bayt dizisi alır. JSON’u [EventData][] ile kullanırken JSON ile kodlanmış bir dize için bayt dizisini almak üzere **Encoding.UTF8.GetBytes()** kullanabilirsiniz. Örneğin:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Bölüm anahtarı

> [!NOTE]
> Bölümler hakkında bilginiz yoksa, [Bu makaleye](event-hubs-features.md#partitions)bakın. 

Olay verileri gönderilirken bir bölüm ataması oluşturmak üzere karma hale getirilmiş bir değer belirtebilirsiniz. Bölüm kullanarak belirttiğiniz [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) özelliği. Ancak, karar bölümleri kullanılabilirlik ve tutarlılık arasında seçim yapma anlamına gelir. 

### <a name="availability-considerations"></a>Kullanılabilirlik konusunda dikkat edilmesi gerekenler

İsteğe bağlı bir bölüm anahtarının kullanılması ve dikkatli bir şekilde bir kullanılıp kullanılmayacağı düşünmelisiniz. Bir olayı yayımlarken bölüm anahtarı belirtmezseniz hepsini bir kez deneme ataması kullanılır. Olay sıralama önemlidir, çoğu durumda, bir bölüm anahtarının kullanılması iyi bir seçimdir. Bir bölüm anahtarı kullandığınızda, tek bir düğümde kullanılabilirlik bu bölümler gerektirir ve zaman içinde kesintiler; Örneğin, düğümlerin yeniden başlatılması ve düzeltme zaman işlem. Bu nedenle, bölüm kimliği ayarlayın ve bu bölümü herhangi bir nedenle kullanılamıyorsa, bu bölümdeki veriler erişme denemesi başarısız olur. Yüksek kullanılabilirlik en önemli ise, bir bölüm anahtarı belirtmeyin; Bu durumda olayları daha önce açıklanan hepsini bir kez deneme model kullanılarak bölümlere gönderilir. Bu senaryoda, kullanılabilirlik (bölüm kimliği) ve tutarlılık (bölüm kimliği olayları sabitleme) arasında açık bir seçim hale getiriyoruz.

Diğer bir durum olayları işlenmesinde gecikmelere işleyen. Bazı durumlarda, veri ve potansiyel olarak daha da aşağı akış işleme gecikmelere neden olabilir işlemeyle tutmaya çalışın daha yeniden deneme daha iyi olabilir. Örneğin, bir bandı tam değilse, tüm güncel verilere, ancak canlı sohbet ya da verileri hızlı bir şekilde yerine gerekir VoIP senaryo bekleyin daha iyi olur.

Bu senaryolarda bu kullanılabilirlik değerlendirmelerine verilen aşağıdaki hata işleme stratejileri birini:

- Stop (Durdur şeyler düzeltilene kadar Event Hubs'dan okuma)
- Bırakma (iletileri önemli olmayan sürükleyip bırakın)
- (İletileri uygun şekilde yeniden) deneyin

Daha fazla bilgi ve kullanılabilirlik ile tutarlılık arasındaki dengelemeler hakkında bir tartışma için bkz: [kullanılabilirlik ve tutarlılık Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Toplu olay gönderme işlemleri

Yardımcı olayların toplu olarak gönderilmesi üretilen işi artırabilir. Kullanabileceğiniz [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) veri nesneleri daha sonra eklenebilir için bir toplu iş oluşturmak için API bir [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) çağırın.

Tek bir toplu işlem, bir olayın 1 MB sınırını aşmamalıdır. Ayrıca, toplu işteki her bir ileti aynı yayımcı kimliğini kullanır. Toplu işin en büyük olay boyutu aşmamasını sağlamak gönderenin sorumluluğundadır. Aşması durumunda bir istemci **Gönderme** hatası oluşturulur. Batch 'in 1 MB 'ı aşmadığından emin olmak için [Eventhubclient. CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) yardımcı yöntemini kullanabilirsiniz. Boş bir alma [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) gelen [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API ve ardından [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) toplu iş oluşturmak için olay eklemek için. 

## <a name="send-asynchronously-and-send-at-scale"></a>Zaman uyumsuz olarak gönderme ve ölçekli gönderme

Zaman uyumsuz olarak bir olay hub'ına olayları gönderirsiniz. Zaman uyumsuz gönderme bir istemcinin olayları gönderebildiği olduğu hızı artar. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) döndürür bir [görev](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) nesne. Kullanabileceğiniz [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) Denetim İstemcisi için istemci üzerindeki sınıf seçenekleri yeniden deneyin.

## <a name="event-consumers"></a>Olay tüketicileri
[EventProcessorHost][] sınıfı Event Hubs verilerini işler. .NET platformu üzerinde olay okuyucuları oluştururken bu uygulamayı kullanmanız gerekir. [EventProcessorHost][] aynı zamanda denetim noktası oluşturma ve bölüm kiralama yönetimi sağlayan olay işlemcisi uygulamaları için iş parçacığı güvenli, çok işlemli, güvenli bir çalışma zamanı ortamı sağlar.

[EventProcessorHost][] sınıfını kullanmak için [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) uygulayabilirsiniz. Bu arabirim, dört yöntemleri içerir:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Olay işlemeyi başlatmak için örneği [EventProcessorHost][], event hub'ınıza uygun parametreleri sağlayarak. Örneğin:

> [!NOTE]
> EventProcessorHost ve ilgili sınıfları **Microsoft. Azure. EventHubs. Processor** paketinde sunulmaktadır. [Bu makaledeki](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) yönergeleri Izleyerek veya [Paket Yöneticisi konsolu](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) penceresinde aşağıdaki komutu vererek, paketi Visual Studio projenize ekleyin:`Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Ardından, arama [Ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) kaydetmek için [Ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) uygulama çalışma zamanı ile:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Bu noktada konak bir "Hızlı" algoritma kullanarak event hub'ındaki her bölüm üzerinde bir kira dener. Bu kiralar belirli bir zaman çerçevesi boyunca ve sonrasında yenilenmelidir. Bu örnekte çalışan örnekleri olan yeni düğümler çevrimiçi oldukça kiralama ayırmaları yapar ve zaman içerisinde yük daha fazla kira elde etmeye çalıştığından düğümler arasında kayar.

![Olay İşlemcisi Konağı](./media/event-hubs-programming-guide/IC759863.png)

Zaman içerisinde bir denge sağlanır. Bu dinamik özellik hem ölçek artırma hem de ölçek azaltma için tüketicilere CPU tabanlı otomatik ölçeklendirmenin uygulanmasını sağlar. Event Hubs bir ileti sayısı doğrudan kavramına sahip olmadığından ortalama CPU kullanımı genellikle arka uç veya tüketici ölçeğini ölçmeye yönelik en iyi mekanizmadır. Yayımcılar tüketicilerin işleyebileceğinden daha fazla olay yayımlamaya başlarsa, tüketiciler üzerindeki CPU artışı çalışan örnek sayısı üzerinde otomatik ölçeklendirmeye neden olmak için kullanılabilir.

[EventProcessorHost][] sınıfı ayrıca bir Azure depolama tabanlı denetim noktası oluşturma mekanizması kullanır. Bu mekanizma uzaklığı bölüm başına temelinde depolar, böylece her tüketici önceki tüketicinin son denetim noktasının ne olduğunu belirleyebilir. Bölümler kiralamalar aracılığıyla düğümler arasında geçiş yaptığında yük kaymasını kolaylaştıran eşitleme mekanizması budur.

## <a name="publisher-revocation"></a>Yayımcı iptali

Olay Işleyicisi ana bilgisayarının gelişmiş çalışma zamanı özelliklerine ek olarak Event Hubs hizmeti, belirli yayımcıların Olay Hub 'ına olay göndermesini engellemek için [Yayımcı iptalinin](/rest/api/eventhub/revoke-publisher) yapılmasını sağlar. Bu özellikler, bir yayımcı belirteci tehlike girdiğinde veya bir yazılım güncelleştirmesi bunları uygunsuz bir şekilde davranmasına neden yararlıdır. Bu durumlarda SAS belirtecinin bir parçası olan yayımcı kimliğinin olayları yayımlaması engellenebilir.

> [!NOTE]
> Şu anda, bu özelliği yalnızca REST API destekler ([Yayımcı iptali](/rest/api/eventhub/revoke-publisher)).

Yayımcı iptali ve yayımcı olarak Event Hubs’a gönderme hakkında daha fazla bilgi için [Event Hubs Büyük Ölçekli Güvenli Yayımlama](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) örneğine bakın.

## <a name="next-steps"></a>Sonraki adımlar

Event Hubs senaryoları hakkında daha fazla bilgi almak için aşağıdaki bağlantıları ziyaret edin:

* [Event Hubs API’sine genel bakış](event-hubs-api-overview.md)
* [Event Hubs nedir](event-hubs-what-is-event-hubs.md)
* [Event Hubs’da kullanılabilirlik ve tutarlılık](event-hubs-availability-and-consistency.md)
* [Olay işlemcisi konağı API başvurusu](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor

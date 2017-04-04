---
title: ".NET içeren Azure Service Bus konu başlıklarını kullanma | Microsoft Docs"
description: "Azure&quot;da .NET içeren Service Bus konu başlıklarını ve abonelikleri kullanmayı öğrenin. Kod örnekleri .NET uygulamalarına yönelik yazılır."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 31d0bc29-6524-4b1b-9c7f-aa15d5a9d3b4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: bec18e91ef8798a791d4b1fe93bd529593197e01
ms.lasthandoff: 03/24/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Service Bus konu başlıklarını ve aboneliklerini kullanma
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Bu makale, Service Bus konu başlıklarını ve aboneliklerini kullanmayı açıklar. Örnekler C# dilinde yazılmıştır ve .NET API'lerini kullanır. Kapsamdaki senaryolar; konu başlığı ve abonelik oluşturma, abonelik filtreleri oluşturma, bir konu başlığına ileti gönderme ve aboneliklerden ileti almanın yanı sıra konu bağlığı ve abonelikleri silmeyi içerir. Konu başlıkları ve abonelikler hakkında daha fazla bilgi edinmek için [Sonraki adımlar](#next-steps) bölümüne bakın.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Service Bus hizmetini kullanmak için uygulamayı yapılandırma
Service Bus kullanan bir uygulama oluştururken Service Bus derlemesine yönelik bir başvuru eklemeniz ve ilgili ad alanlarını dahil etmeniz gerekir. Bunu yapmanın en kolay yolu uygun [NuGet](https://www.nuget.org) paketini indirmektir.

## <a name="get-the-service-bus-nuget-package"></a>Service Bus NuGet paketi alma
[Service Bus NuGet paketi](https://www.nuget.org/packages/WindowsAzure.ServiceBus), uygulamanızı gerekli tüm Service Bus bağımlılıklarıyla yapılandırmanın en kolay yoludur. Service Bus NuGet paketini projenize yüklemek için aşağıdakileri yapın:

1. Çözüm Gezgini'nde, **Başvurular**'a sağ tıklayın ve ardından **NuGet Paketlerini Yönet**'e tıklayın.
2. **Gözat**’a tıklayın, “Azure Service Bus” araması yapın ve **Microsoft Azure Service Bus**’ı seçin. Yüklemeyi tamamlamak için **Yükle**'ye tıklayın, ardından iletişim kutusunu kapatın:
   
   ![][7]

Artık Service Bus için kod yazmaya hazırsınız.

## <a name="create-a-service-bus-connection-string"></a>Service Bus bağlantı dizesi oluşturma
Service Bus, uç noktaları ve kimlik bilgilerini depolamak için bir bağlantı dizesi kullanır. Sabit kodlama yerine, bağlantı dizenizi bir yapılandırma dosyasının içine koyabilirsiniz:

* Azure hizmetlerini kullanırken bağlantı dizenizi Azure hizmet yapılandırma sistemini (.csdef ve .cscfg dosyaları) kullanarak depolamanız önerilir.
* Azure web sitelerini veya Azure Virtual Machines hizmetini kullanırken bağlantı dizenizi .NET yapılandırma sistemini (örneğin, Web.config dosyası) kullanarak depolamanız önerilir.

Her iki durumda da, bağlantı dizenizi bu makalenin sonraki bölümlerinde açıklanan `CloudConfigurationManager.GetSetting` yöntemini kullanarak alabilirsiniz.

### <a name="configure-your-connection-string"></a>Bağlantı dizenizi yapılandırma
Hizmet yapılandırma mekanizması, uygulamanızı yeniden dağıtmanıza gerek kalmadan [Azure portalı][Azure portal] aracılığıyla yapılandırma ayarlarınızı dinamik olarak değiştirmenize olanak sağlar. Örneğin, bir sonraki örnekte gösterildiği gibi hizmet tanımı (**.csdef**) dosyanıza bir `Setting` etiketi ekleyin.

```xml
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Ardından, hizmet yapılandırma (.cscfg) dosyasında değerleri belirtirsiniz.

```xml
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Daha önce açıklandığı gibi portaldan alınan Paylaşılan Erişim İmzası (SAS) anahtar adını ve anahtar değerlerini kullanın.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Azure web sitelerini veya Azure Virtual Machines hizmetini kullanırken bağlantı dizesini yapılandırma
Web sitelerini veya Virtual Machines hizmetini kullanırken, .NET yapılandırma sistemini kullanmanız önerilir (örneğin, Web.config). `<appSettings>` ögesini kullanarak bağlantı dizenizi depolarsınız.

```xml
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Daha önce açıklandığı gibi [Azure portalı][Azure portal] aldığınız SAS adını ve anahtar değerlerini kullanın.

## <a name="create-a-topic"></a>Konu başlığı oluşturma
[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) sınıfını kullanarak Service Bus konu başlıklarına ve aboneliklerine yönelik yönetim işlemlerini gerçekleştirebilirsiniz. Bu sınıfın sağladığı yöntemlerle konu oluşturabilir, konu başlıklarını numaralandırabilir ve silebilirsiniz.

Aşağıdaki örnek, Service Bus hizmeti ad alanı taban adresini ve yönetme izniyle birlikte uygun SAS kimlik bilgilerinin bulunduğu bir bağlantı dizesini içeren Azure `CloudConfigurationManager` sınıfını kullanan bir `NamespaceManager` nesnesi oluşturur. Bu bağlantı dizesi aşağıdaki şekildedir:

```xml
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Önceki bölümde verilen yapılandırma ayarlarıyla aşağıdaki örneği kullanın.

```csharp
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Konunun özelliklerini ayarlamanıza olanak sağlayan (örneğin, konu başlığına gönderilen iletilere uygulanacak varsayılan yaşam süresi (TTL) değerini ayarlama) [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager) yönteminde aşırı yükleme yapılmıştır. Bu ayarlar [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) sınıfı kullanılarak uygulanır. Aşağıdaki örnek, maksimum 5 GB boyuta sahip olan ve 1 dakikalık varsayılan ileti TTL'si içeren, **TestTopic** olarak adlandırılan bir konu başlığının nasıl oluşturulacağını gösterir.

```csharp
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [!NOTE]
> Konu başlığı için belirtilen adın zaten bir hizmet ad alanında kullanılıp kullanılmadığını kontrol etmek için [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nesnelerinde [TopicExists](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_TopicExists_System_String_) yöntemini kullanabilirsiniz.
> 
> 

## <a name="create-a-subscription"></a>Abonelik oluşturma
Ayrıca, [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) sınıfını kullanarak da konu başlığı abonelikleri oluşturabilirsiniz. Abonelikler adlandırılır ve aboneliğin sanal kuyruğuna gönderilen ileti kümesini sınırlayan isteğe bağlı bir filtre içerebilir.

> [!IMPORTANT]
> İletilerin bir abonelik tarafından alınabilmesi için konuya herhangi bir ileti göndermeden önce ilgili aboneliği oluşturmanız gerekir. Bir konuya abonelik yoksa konu bu iletileri atar.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Varsayılan (MatchAll) filtreyle abonelik oluşturma
Yeni bir abonelik oluşturulurken filtre belirtilmezse kullanılan varsayılan filtre **MatchAll** filtresidir. **MatchAll** filtresini kullandığınızda konu başlığında yayımlanan tüm iletiler aboneliğin sanal kuyruğuna yerleştirilir. Aşağıdaki örnekte "AllMessages" adlı bir abonelik oluşturulur ve varsayılan **MatchAll** filtresi kullanılır.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Filtre içeren abonelik oluşturma
Bir konu başlığına gönderilen iletilerden hangilerinin belirli bir konu başlığı aboneliğinde görüneceğini belirlemenize olanak sağlayan filtreler de ayarlayabilirsiniz.

Abonelikler tarafından desteklenen en esnek filtre türü, SQL92 alt kümesi uygulayan [SqlFilter][SqlFilter] sınıfıdır. SQL filtreleri, konu başlığında yayımlanan iletilerin özelliklerinde çalışır. SQL filtresi ile kullanılabilen ifadeler hakkında daha fazla bilgi edinmek için [SqlFilter.SqlExpression][SqlFilter.SqlExpression] söz dizimine bakın.

Aşağıdaki örnekte, yalnızca 3’ten büyük özel **MessageNumber** özelliğini bulunduran iletileri seçen [SqlFilter][SqlFilter] nesnesini içeren **HighMessages** adlı bir abonelik oluşturulur.

```csharp
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Benzer şekilde, aşağıdaki örnekte yalnızca 3’e eşit veya bu değerden daha az **MessageNumber** özelliğini bulunduran iletileri seçen [SqlFilter][SqlFilter] nesnesini içeren **LowMessages** adlı bir abonelik oluşturulur.

```csharp
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Artık `TestTopic` konu başlığına bir ileti gönderildiğinde bu ileti **AllMessages** konu başlığı aboneliği bulunan tüm alıcılara teslim edilir. **HighMessages** ve **LowMessages** konu başlığı aboneliklerini seçen diğer alıcılara ise ileti teslimi seçime bağlı olarak gerçekleştirilir (ileti içeriğine göre).

## <a name="send-messages-to-a-topic"></a>Konu başlığına ileti gönderme
Bir Service Bus konu başlığına bir ileti göndermek için uygulamanız bağlantı dizesini kullanarak bir [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) nesnesi oluşturur.

Aşağıdaki kodda, daha önce [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.topicclient#Microsoft_ServiceBus_Messaging_TopicClient_CreateFromConnectionString_System_String_System_String_) API kullanarak oluşturulan **TestTopic** konu başlığına yönelik [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) nesnesi oluşturma işleminin nasıl gerçekleştirileceği gösterilir.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Service Bus konu başlıklarına gönderilen iletiler, [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) sınıfının örnekleridir. **BrokeredMessage** nesneleri, bir standart özellikler kümesi ([Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) ve [TimetoLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) gibi) uygulamaya özgü özel özellikleri tutmak için kullanılan bir sözlük ve rastgele uygulama verileri gövdesi içerir. Uygulama herhangi bir seri haline getirebilir nesneyi **BrokeredMessage** nesnesinin oluşturucusuna geçirerek ileti gövdesini ayarlayabilir ve ardından nesneyi seri haline getirmek için uygun **DataContractSerializer** kullanılır. Alternatif olarak, bir **System.IO.Stream** nesnesi sağlanabilir.

Aşağıdaki örnek, bir önceki kod örneğinde elde ettiğiniz **TestTopic** [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) nesnesine nasıl beş test iletisi göndereceğinizi gösterir. Döngü tekrarına bağlı olarak her iletinin [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) özelliği değerinin değişebileceğini unutmayın (iletileri alacak abonelikleri belirler).

```csharp
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageId"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Service Bus konu başlıkları, [Standart katmanda](service-bus-premium-messaging.md) maksimum 256 KB ve [Premium katmanda](service-bus-premium-messaging.md) maksimum 1 MB ileti boyutunu destekler. Standart ve özel uygulama özelliklerini içeren üst bilginin maksimum dosya boyutu 64 KB olabilir. Konu başlığında tutulan ileti sayısına ilişkin bir sınır yoktur ancak konu başlığı tarafından tutulan iletilerin toplam boyutu için uç sınır vardır. Bu konu başlığı boyutu, üst sınır 5 GB olacak şekilde oluşturulma zamanında belirlenir. Bölümlendirme etkinse üst sınır daha yüksektir. Daha fazla bilgi için bkz. [Bölümlenmiş mesajlaşma varlıkları](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Abonelikten ileti alma
Abonelikten ileti almak için tavsiye edilen yöntem [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) nesnesi kullanmaktır. **SubscriptionClient** nesneleri iki farklı modda çalışabilir: [*ReceiveAndDelete* ve *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode) **PeekLock** varsayılan değerdir.

**ReceiveAndDelete** modunu kullanırken alma işlemi tek aşamalıdır. Service Bus abonelikte bir iletiye yönelik okuma isteği aldığında, iletiyi kullanılıyor olarak işaretler ve uygulamaya döndürür. **ReceiveAndDelete** modu, en basit modeldir ve uygulamanın hata oluştuğunda bir iletinin işlenmemesine izin verebileceği senaryolarda en iyi şekilde çalışır. Bu durumu daha iyi anlamak için müşterinin bir alma isteği bildirdiğini ve bu isteğin işlenmeden çöktüğünü varsayın. Service Bus iletiyi kullanılmış olarak işaretlediğinden, uygulama yeniden başlatılıp iletileri tekrar kullanmaya başladığında çökmenin öncesinde kullanılan iletiyi atlamış olur.

**PeekLock** modunda (varsayılan mod), atlanan iletilere izin veremeyen uygulamaları desteklemenin mümkün olması için alma işlemi iki aşamalıdır. Service Bus bir istek aldığında bir sonraki kullanılacak iletiyi bulur, diğer tüketicilerin bu iletiyi almasını engellemek için kilitler ve ardından uygulamaya döndürür. Uygulama iletiyi işlemeyi tamamladıktan sonra (veya iletiyi daha sonra işlemek üzere güvenli şekilde depoladıktan sonra) alınan iletide [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) yöntemini çağırarak alma işleminin ikinci aşamasını tamamlar. Service Bus **Complete** çağrısını gördüğünde iletiyi kullanılıyor olarak işaretler ve abonelikten kaldırır.

Aşağıdaki örnekte, varsayılan **PeekLock** modu kullanılarak iletilerin nasıl alınıp işlenebileceği gösterilir. Farklı bir [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode) değeri belirtmek üzere [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_CreateFromConnectionString_System_String_System_String_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_) için başka bir aşırı yükü kullanabilirsiniz. Bu örnekte, iletilerin **HighMessages** aboneliğine ulaştığında işlenmesi için [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) geri çağrısı kullanılır.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Bu örnek, bir [OnMessageOptions](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions) nesnesini kullanarak [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) geri çağrısını yapılandırır. Alınan iletide [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) çağrısı yapıldığında ileti üzerinde kontrol sağlamanız için [AutoComplete](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoComplete) **yanlış** olarak ayarlanmıştır. Otomatik yenileme özelliği sonlandırılmadan önce istemcinin ileti için en fazla bir dakika beklemesini ve iletileri kontrol etmek için istemcinin yeni bir çağrı yapmasını sağlamak için [AutoRenewTimeout](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout) 1 dakika olarak ayarlanır. Bu özellik değeri sayesinde, istemci tarafından gerçekleştirilen, ileti almayan ücretlendirilebilir çağrı sayısı azaltılır.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Uygulama çökmelerini ve okunmayan iletileri giderme
Service Bus, uygulamanızda gerçekleşen hataları veya ileti işlenirken oluşan zorlukları rahat bir şekilde ortadan kaldırmanıza yardımcı olmak için işlevsellik sağlar. Bazı nedenlerden dolayı alıcı uygulamanın iletiyi işleyememesi durumunda, alınan iletide [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon_System_Collections_Generic_IDictionary_System_String_System_Object__) yöntemini ([Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) yöntemi yerine) çağrılabilir. Bu işlem, Service Bus hizmetinin abonelikteki iletinin kilidini açmasına ve iletiyi aynı veya başka bir kullanıcı uygulama tarafından tekrar alınabilir hale getirmesine neden olur.

Ayrıca abonelikte kilitlenen iletiye ilişkin bir zaman aşımı vardır. Uygulama, kilitleme zaman aşımı dolmadan önce iletiyi işleyemezse (örneğin, uygulama çökerse), Service Bus otomatik olarak iletinin kilidini açar ve tekrar alınabilmesini sağlar.

Uygulamanın iletiyi işleyip [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) isteği bildirilmeden önce çökmesi durumunda ise uygulama yeniden başlatıldığında ileti uygulamaya tekrar teslim edilir. Bu durum *En Az Bir Kez İşleme* olarak adlandırılır. Her ileti en az bir kez işlenir ancak belirli durumlarda aynı ileti yeniden teslim edilebilir. Senaryo yinelenen işlemeyi kabul etmiyorsa yinelenen ileti teslimine izin vermek için uygulama geliştiricilerin uygulamaya ilave bir mantık eklemesi gerekir. Bu işlem genellikle iletinin teslimat denemelerinde korunan [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) özelliği kullanılarak gerçekleştirilir.

## <a name="delete-topics-and-subscriptions"></a>Konu başlıklarını ve abonelikleri silme
Aşağıdaki örnekte **HowToSample** hizmeti ad alanından **TestTopic** konu başlığının nasıl silineceği gösterilir.

```csharp
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Bir konu başlığı silindiğinde bu konu başlığıyla kaydedilen tüm abonelikler de silinir. Ayrıca, abonelikler bağımsız olarak da silinebilir. Aşağıdaki kodda, **HighMessages** olarak adlandırılan aboneliğin **TestTopic** konu başlığından nasıl silineceği gösterilir.

```csharp
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Sonraki adımlar
Artık Service Bus konu başlıklarına ve aboneliklerine ilişkin temel bilgileri öğrendiniz, daha fazla bilgi edinmek için aşağıdaki bağlantıları izleyin.

* [Kuyruklar, konu başlıkları ve abonelikler][Queues, topics, and subscriptions].
* [Konu başlığı filtreleri örneği][Topic filters sample]
* [SqlFilter][SqlFilter] için API başvurusu.
* Service Bus kuyruğundan ileti alıp gönderen, çalışan bir uygulama oluşturun: [Service Bus aracılı mesajlaşma .NET öğreticisi][Service Bus brokered messaging .NET tutorial].
* Service Bus örnekleri: [Azure örneklerinden][Azure samples] indirin veya [genel bakışı](service-bus-samples.md) gözden geçirin.

[Azure portal]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Topic filters sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2


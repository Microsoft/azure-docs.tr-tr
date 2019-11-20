---
title: Notification Hubs-kurumsal gönderim mimarisi
description: Kurumsal ortamda Azure Notification Hubs kullanma kılavuzu
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 5b65fe6acb1fdf7ba79b106c876527c9b6736c5f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211914"
---
# <a name="enterprise-push-architectural-guidance"></a>Kurumsal gönderim mimari kılavuzu

Günümüzde kuruluşlar, son kullanıcıları (harici) veya çalışanlar (iç) için mobil uygulamalar oluşturmaya doğru yavaş hareket edebilir. BT ana bilgisayarları veya mobil uygulama mimarisiyle tümleştirilebilen bazı LoB uygulamaları, mevcut arka uç sistemlerine sahiptir. Bu kılavuzda, bu tümleştirmenin en iyi şekilde yaygın senaryolar için olası çözümü önerme konusu ele alınabilir.

Sık kullanılan bir gereksinim, arka uç sistemlerinde ilgi çekici bir olay gerçekleştiğinde kullanıcılara mobil uygulamaları aracılığıyla anında iletme bildirimi göndermeye yöneliktir. Örneğin, bir iPhone 'da bankanın bankacılık uygulamasına sahip olan bir banka müşterisi, hesap veya bir intranet senaryosuyla, Windows Phone bir bütçe onayı uygulamasına sahip olan Finans departmanından çalışanların belirli bir miktarın üzerinde yapılmadığı zaman bildirim almak istiyor.  onay isteği alındığında bildirilmesi için.

Banka hesabı veya onay işlemenin, kullanıcıya bir gönderim başlatması gereken bazı arka uç sistemlerinde yapılması olasıdır. Bir olay bir bildirim tetiklendiğinde, bu tür bir mantığın aynı tür mantığı oluşturması gereken birden fazla arka uç sistemi olabilir. Buradaki karmaşıklık, bazı arka uç sistemlerini, son kullanıcıların farklı bildirimlere abone olabileceği ve hatta birden çok mobil uygulama bile olabilecek tek bir gönderim sistemiyle tümleştirilmesine yer alıyor. Örneğin, bir mobil uygulamanın birden fazla arka uç sisteminden bildirim almak istedikleri intranet mobil uygulamaları. Burada yaygın olarak kullanılan bir çözümün, bir bileşeni tanıtmak için, arka uç sistemleri, her türlü olay için arka uç sistemlerini yoklayıp ve anında iletme iletilerinin şuraya gönderilmesi hakkında istemcilerinin.

Daha iyi bir çözüm Azure Service Bus konu/abonelik modelini kullanmaktır, bu da çözümü ölçeklenebilir yaparken karmaşıklığı azaltır.

Çözümün genel mimarisi aşağıda verilmiştir (birden çok mobil uygulamayla Genelleştirilmiş, ancak yalnızca bir mobil uygulama olduğunda aynı şekilde geçerlidir)

## <a name="architecture"></a>Mimari

![][1]

Bu mimari diyagramdaki anahtar parçası Azure Service Bus konular/abonelikler programlama modeli (daha fazla [Service Bus pub/Sub programlama]) sağlar. Bu durumda, mobil arka uç (mobil uygulamalara gönderim başlatan [Azure Mobil Hizmeti]), doğrudan arka uç sistemlerinden ileti almaz, bunun yerine tarafından [Azure Service Bus], mobil arka ucun bir veya daha fazla arka uç sisteminden ileti almasına olanak sağlar. Bir Service Bus konunun her bir arka uç sistemi için oluşturulması gerekir; örneğin, bir hesap, ık ve finans, bu, temel olarak ilgi çekici bir "konu başlığı" olan ve anında iletme bildirimi olarak gönderilecek iletileri başlatır. Arka uç sistemleri bu konulara iletiler gönderir. Mobil arka uç, Service Bus bir abonelik oluşturarak bir veya daha fazla konuya abone olabilir. Bu, ilgili arka uç sisteminden bildirim almak için mobil arka uca sahibine. Mobil arka uç, aboneliklerindeki iletileri dinlemeye devam eder ve bir ileti ulaştığında, geri döner ve Bildirim Hub 'ına bildirim olarak gönderir. Bildirim Hub 'ları, sonunda iletiyi mobil uygulamaya teslim edebilir. Anahtar bileşenlerinin listesi aşağıdadır:

1. Arka uç sistemleri (LoB/eski sistemler)
   * Service Bus konu oluşturur
   * Ileti gönderir
1. Mobil arka uç
   * Hizmet aboneliği oluşturur
   * Iletiyi alır (arka uç sisteminden)
   * İstemcilere bildirim gönderir (Azure Notification Hub 'ı aracılığıyla)
1. Mobil uygulama
   * Bildirim alır ve görüntüler

### <a name="benefits"></a>Avantajlar

1. Alıcı (Bildirim Hub 'ı aracılığıyla mobil uygulama/hizmet) ve gönderici (arka uç sistemleri) arasındaki ayırma, ek arka uç sistemlerinin en az değişiklik ile tümleştirilebilmesine izin verebilir.
1. Ayrıca, birden çok mobil uygulama senaryosunun bir veya daha fazla arka uç sisteminden olay alabilmesini sağlar.  

## <a name="sample"></a>Örnek

### <a name="prerequisites"></a>Önkoşullar

Kavramların yanı sıra ortak oluşturma & yapılandırma adımları hakkında bilgi edinmek için aşağıdaki öğreticilerini doldurun:

1. [Service Bus pub/Sub programlama] -Bu öğreticide, Service Bus konuları/abonelikleri ile çalışmanın ayrıntıları, konuları/abonelikleri içeren bir ad alanı oluşturma, onlardan ileti alma & gönderme işlemleri açıklanmaktadır.
2. [Notification Hubs-Windows Universal öğreticisi] -Bu öğreticide bir Windows Mağazası uygulamasının nasıl ayarlanacağı ve bildirimlerin nasıl kaydedileceği ve Notification Hubs kullanılacağı açıklanmaktadır.

### <a name="sample-code"></a>Örnek kod

Tam örnek kod, [Bildirim Hub 'ı örnekleri]kullanılabilir. Üç bileşene ayrılır:

1. **EnterprisePushBackendSystem**

    a. Bu proje **windowsazure. ServiceBus** NuGet paketini kullanır ve [Service Bus pub/Sub programlama]dayanır.

    b. Bu uygulama, mobil uygulamaya C# teslim edilecek iletiyi Başlatan bir lob sisteminin benzetimini yapmak için basit bir konsol uygulamasıdır.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic`Service Bus konusunu oluşturmak için kullanılır.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage`iletileri bu Service Bus konusuna göndermek için kullanılır. Bu kod, örnek amacına uygun olarak konuya düzenli olarak bir rastgele ileti kümesi gönderir. Normalde bir olay gerçekleştiğinde ileti gönderen bir arka uç sistemi vardır.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification**

    a. Bu proje *windowsazure. ServiceBus* ve **Microsoft. Web. WebJobs.** NuGet paketlerini yayımlayın ve [Service Bus pub/Sub programlama]tabanlıdır.

    b. Aşağıdaki konsol uygulaması, LoB/arka uç sistemlerinden iletileri dinlemek için sürekli olarak çalıştırılması gerektiğinden, bir [Azure WebJob] olarak çalışır. Bu uygulama, mobil arka ucunuzun bir parçasıdır.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription`arka uç sisteminin ileti gönderdiği konu için Service Bus bir abonelik oluşturmak için kullanılır. İş senaryosuna bağlı olarak, bu bileşen ilgili konulara bir veya daha fazla abonelik oluşturur (örneğin, bazı bir HR sisteminden, bazıları finans sisteminden ve bu şekilde devam edebilir)

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification`, aboneliğini kullanarak konudan iletiyi okumak için kullanılır ve okuma başarılı olursa Azure Notification Hubs kullanılarak mobil uygulamaya gönderilmek üzere bir bildirim (örneğin, bir Windows yerel bildirim bildirimi) oluşturun.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Bu uygulamayı **WebJob**olarak yayımlamak Için, Visual Studio 'da çözüme sağ tıklayın ve **WebJob olarak Yayımla** ' yı seçin.

    ![][2]

    f. Yayımlama profilinizi seçin ve henüz yoksa yeni bir Azure Web sitesi oluşturun, bu WebJob 'u barındırır ve Web sitesi yayımlandıktan sonra **yayımlayın**.

    ![][3]

    g. İşi "sürekli Çalıştır" olacak şekilde yapılandırın, böylece [Azure Portal] oturum açtığınızda aşağıdakine benzer bir şey görmeniz gerekir:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Bu uygulama, mobil arka ucunuzun bir parçası olarak çalışan WebJob 'tan bildirimler alan ve onu görüntüleyen bir Windows Mağazası uygulamasıdır. Bu kod, [Notification Hubs-Windows Universal öğreticisi]temel alır.  

    b. Uygulamanızın bildirim almak için etkinleştirildiğinden emin olun.

    c. Uygulamanın başlangıcında aşağıdaki Notification Hubs kayıt kodunun çağrıldığından emin olun ( `HubName` ve `DefaultListenSharedAccessSignature` değerlerini değiştirdikten sonra:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>Örneği çalıştırma

1. WebJob 'larınızın başarıyla çalıştığından ve sürekli çalışacak şekilde zamanlandığından emin olun.
2. Windows Mağazası uygulamasını başlatan **EnterprisePushMobileApp**çalıştırın.
3. LoB arka ucunu taklit eden ve ileti göndermeye başlayan **EnterprisePushBackendSystem** konsol uygulamasını çalıştırın ve aşağıdaki görüntüde olduğu gibi bildirim bildirimleri görmeniz gerekir:

    ![][5]

4. İletiler başlangıçta Web işinizdeki Service Bus abonelikleri tarafından izlenmekte olan Service Bus konularına gönderilmiştir. Bir ileti alındıktan sonra mobil uygulamaya bir bildirim oluşturulup gönderilir. Web Işiniz için [Azure Portal] Günlükler bağlantısına gittiğinizde işlemeyi onaylamak için WebJob günlüklerine bakabilirsiniz:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Bildirim Hub 'ı örnekleri]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobil Hizmeti]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus pub/Sub programlama]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Notification Hubs-Windows Universal öğreticisi]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure portal]: https://portal.azure.com/

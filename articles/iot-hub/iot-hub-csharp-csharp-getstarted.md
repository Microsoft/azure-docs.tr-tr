---
title: "Azure IoT Hub&quot;ı (.NET) kullanmaya başlama | Microsoft Belgeleri"
description: ".NET için Azure IoT SDK&quot;larını kullanarak bir cihazdan bir Azure IoT hub&quot;ına cihazdan buluta iletiler gönderme. İleti göndermek için bir sanal cihaz uygulaması, cihazınızı kimlik kayıt defterine kaydetmek için bir hizmet uygulaması ve cihazdan buluta gönderilen iletileri IoT hub&quot;ından okumak için bir hizmet uygulaması oluşturmanız gerekir."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 969e6bd55ca69e293f13b66f1a51f1d5fd1996b7
ms.lasthandoff: 03/06/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-net"></a>.NET kullanarak sanal cihazınızı IoT hub’ınıza bağlama
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Bu öğreticinin sonunda üç .NET konsol uygulamanız olacak:

* Bir cihaz kimliği ve sanal cihaz uygulamanızı bağlamak için ilişkili güvenlik anahtarı oluşturan **CreateDeviceIdentity**.
* Sanal cihaz uygulamanız tarafından gönderilen telemetriyi görüntüleyen **ReadDeviceToCloudMessages**.
* Daha önce oluşturulan cihaz kimliğiyle IoT hub'ınızı bağlayan ve MQTT protokolünü kullanarak her saniye bir telemetri iletisi gönderen **SimulatedDevice**.

> [!NOTE]
> Hem cihazlarınızda hem de çözüm arka ucunuzda çalıştırılacak uygulamalar oluşturmak için kullanabileceğiniz Azure IoT SDK'ları hakkında bilgi için bkz. [Azure IoT SDK'ları][lnk-hub-sdks].
> 
> 

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Visual Studio 2015 veya Visual Studio 2017.
* Etkin bir Azure hesabı. (Hesabınız yoksa, yalnızca birkaç dakika içinde [ücretsiz bir hesap][lnk-free-trial] oluşturabilirsiniz.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

IoT hub'ınızı oluşturdunuz ve bu öğreticinin geri kalanını tamamlamak için gereken ana bilgisayar adı ve IoT Hub bağlantı dizesine sahipsiniz.

## <a name="create-a-device-identity"></a>Cihaz kimliği oluşturma
Bu bölümde, IoT hub'ınızdaki kimlik kayıt defterinde cihaz kimliği oluşturan bir .NET konsol uygulaması oluşturursunuz. Kimlik kayıt defterinde girişi olmayan bir cihaz IoT hub'ına bağlanamaz. Daha fazla bilgi için [IoT Hub geliştirici kılavuzunun][lnk-devguide-identity] "Kimlik kayıt defteri" bölümüne bakın. Bu konsol uygulamasını çalıştırdığınızda, cihazınızın IoT Hub'a cihaz-bulut iletileri gönderdiğinde kendisini tanımlamak için kullanabileceği benzersiz bir cihaz kimliği ve anahtarı oluşturulur.

1. Visual Studio’da **Konsol Uygulaması (.NET Framework)** proje şablonunu kullanarak yeni bir çözüme bir Visual C# Windows Klasik Masaüstü projesi ekleyin. .NET Framework sürümünün 4.5.1 veya sonraki bir sürüm olduğundan emin olun. Projeyi **CreateDeviceIdentity** ve çözümü **IoTHubGetStarted** olarak adlandırın.
   
    ![Yeni Visual C# Windows Klasik Masaüstü projesi][10]
2. Çözüm Gezgini'nde **CreateDeviceIdentity** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**'e tıklayın.
3. **NuGet Paket Yöneticisi** penceresinde **Gözat**'ı seçin, **microsoft.azure.devices**'ı aratın, **Microsoft.Azure.Devices** paketini yüklemek için **Yükle**'yi seçin ve kullanım koşullarını kabul edin. Bu yordam ile [Azure IoT hizmet SDK'sı][lnk-nuget-service-sdk] NuGet paketi ve bağımlılıkları indirilir, yüklenir ve bu pakete bir başvuru eklenir.
   
    ![NuGet Paket Yöneticisi penceresi][11]
4. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;
5. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu değerini, önceki bölümde hub için oluşturduğunuz IoT Hub bağlantı dizesiyle değiştirin.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. **Program** sınıfına aşağıdaki yöntemi ekleyin:
   
        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }
   
    Bu yöntem, **myFirstDevice** kimliği ile bir cihaz kimliği oluşturur. (Bu cihaz kimliği, kimlik kayıt defterinde zaten varsa, kod yalnızca mevcut cihaz bilgilerini alır.) Bu durumda uygulama, bu kimliğin birincil anahtarını görüntüler. IoT hub'ınıza bağlanmak için sanal cihaz uygulamasında bu anahtarı kullanırsınız.
7. Son olarak, **Main** yöntemine aşağıdaki satırları ekleyin:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();
8. Bu uygulamayı çalıştırın ve cihaz anahtarını not edin.
   
    ![Uygulama tarafından üretilen cihaz anahtarı][12]

> [!NOTE]
> IoT Hub kimlik kayıt defteri, yalnızca IoT hub'ına güvenli erişim sağlamak amacıyla cihaz kimliklerini depolar. Güvenlik kimlik bilgileri olarak kullanılmak üzere cihaz kimliklerini ve anahtarlarını ve tek bir cihaza erişimi devre dışı bırakmak için kullanabileceğiniz etkin/devre dışı bayrağını depolar. Uygulamanızın cihaza özgü diğer meta verileri depolaması gerekiyorsa uygulamaya özgü bir depo kullanması gerekir. Daha fazla bilgi için bkz. [IoT Hub geliştirici kılavuzu][lnk-devguide-identity].
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Cihazdan buluta iletileri alma
Bu bölümde IoT Hub'dan cihaz-bulut iletilerini okuyan bir .NET konsol uygulaması oluşturacaksınız. IoT hub'ı, cihazdan buluta iletileri okumanızı sağlamak için [Azure Event Hubs][lnk-event-hubs-overview] ile uyumlu bir uç noktasını kullanıma sunar. Sade ve basit bir anlatım gözetildiği için bu öğretici yüksek işleme dağıtımına uygun olmayan temel bir okuyucu oluşturur. Cihazdan buluta iletilerin ölçekli olarak nasıl işleneceğini öğrenmek için [Cihazdan buluta iletileri işleme][lnk-process-d2c-tutorial] öğreticisine bakın. Event Hubs'dan iletilerin nasıl işleneceği hakkında daha fazla bilgi için [Event Hubs ile Çalışmaya Başlama][lnk-eventhubs-tutorial] öğreticisine bakın. (Bu öğretici, IoT Hub ve Event Hub ile uyumlu uç noktalar için geçerlidir.)

> [!NOTE]
> Cihazdan buluta iletileri okumak için Event Hub ile uyumlu uç nokta her zaman AMQP protokolünü kullanır.
> 
> 

1. Visual Studio’da **Konsol Uygulaması (.NET Framework)** proje şablonunu kullanarak geçerli çözüme bir Visual C# Windows Klasik Masaüstü projesi ekleyin. .NET Framework sürümünün 4.5.1 veya sonraki bir sürüm olduğundan emin olun. Projeye **ReadDeviceToCloudMessages** adını verin.
   
    ![Yeni Visual C# Windows Klasik Masaüstü projesi][10a]
2. Çözüm Gezgini'nde **ReadDeviceToCloudMessages** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**'e tıklayın.
3. **NuGet Paket Yöneticisi** penceresinde **WindowsAzure.ServiceBus**'ı aratın, **Yükle**'yi seçin ve kullanım koşullarını kabul edin. Bu yordam ile [Azure Service Bus][lnk-servicebus-nuget] ile tüm bağımlılıkları indirilir, yüklenir ve bu pakete bir başvuru eklenir. Bu paket, uygulamanın IoT hub'ınızdaki Event Hub ile uyumlu uç noktaya bağlanmasını sağlar.
4. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:
   
        using Microsoft.ServiceBus.Messaging;
        using System.Threading;
5. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu değerini, "IoT hub oluşturma" bölümünde hub için oluşturduğunuz IoT Hub bağlantı dizesiyle değiştirin.
   
        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;
6. **Program** sınıfına aşağıdaki yöntemi ekleyin:
   
        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;
   
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
   
    Bu yöntem, tüm IoT hub'ı cihazdan buluta alma bölümlerinden iletileri almak için bir **EventHubReceiver** örneği kullanır. **EventHubReceiver** nesnesini oluştururken, yalnızca başladıktan sonra gönderilen iletileri alması için bir `DateTime.Now` parametresini nasıl geçirdiğinize dikkat edin. Bu filtre, geçerli ileti kümesini görebilmeniz açısından bir test ortamında kullanışlıdır. Bir üretim ortamında, kodunuzun tüm iletileri işlediğinden emin olmanız gerekir. Daha fazla bilgi için [IoT Hub cihazdan buluta iletiler nasıl işlenir?][lnk-process-d2c-tutorial] öğreticisine bakın.
7. Son olarak, **Main** yöntemine aşağıdaki satırları ekleyin:
   
        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);
   
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
   
        CancellationTokenSource cts = new CancellationTokenSource();
   
        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };
   
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>Sanal cihaz uygulaması oluşturma
Bu bölümde, IoT Hub'a cihazdan buluta iletiler gönderen bir cihaza benzetim yapan bir .NET konsol uygulaması oluşturacaksınız.

1. Visual Studio’da **Konsol Uygulaması (.NET Framework)** proje şablonunu kullanarak geçerli çözüme bir Visual C# Windows Klasik Masaüstü projesi ekleyin. .NET Framework sürümünün 4.5.1 veya sonraki bir sürüm olduğundan emin olun. Projeye **SimulatedDevice** adını verin.
   
    ![Yeni Visual C# Windows Klasik Masaüstü projesi][10b]
2. Çözüm Gezgini'nde **SimulatedDevice** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**'e tıklayın.
3. **NuGet Paket Yöneticisi** penceresinde **Gözat**'ı seçin, **Microsoft.Azure.Devices.Client**'ı aratın, **Microsoft.Azure.Devices.Client** paketini yüklemek için **Yükle**'yi seçin ve kullanım koşullarını kabul edin. Bu yordam ile [Azure IoT cihaz SDK'sı NuGet paketi][lnk-device-nuget] ve bağımlılıkları indirilir, yüklenir ve bu pakete bir başvuru eklenir.
4. Aşağıdaki `using` deyimini **Program.cs** dosyasının üst kısmına ekleyin:
   
        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
5. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucuyu, "IoT hub'ı oluşturma" bölümünde aldığınız IoT hub'ı ana bilgisayar adı ve "Bir cihaz kimliği oluşturma" bölümünde aldığınız cihaz anahtarı ile değiştirin.
   
        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";
6. **Program** sınıfına aşağıdaki yöntemi ekleyin:
   
        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();
   
            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;
   
                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));
   
                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);
   
                await Task.Delay(1000);
            }
        }
   
    Bu yöntem, her saniye yeni bir cihazdan buluta iletisi gönderir. İleti, cihaz kimliğine ve bir rüzgar hızı sensörü benzetimi gerçekleştirmeye yönelik rastgele oluşturulmuş bir sayıya sahip bir JSON ile seri hale getirilmiş bir nesneyi içerir.
7. Son olarak, **Main** yöntemine aşağıdaki satırları ekleyin:
   
        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey), TransportType.Mqtt);
   
        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();
   
   Varsayılan olarak, **Create** yöntemiyle IoT hub'ıyla iletişim kurmak için AMQP protokolünü kullanan bir **DeviceClient** örneği oluşturulur. MQTT veya HTTP protokolünü kullanmak için **Create** yönteminin protokolü belirtmenize olanak tanıyan geçersiz kılmasını kullanın. HTTP protokolünü kullanıyorsanız **System.Net.Http.Formatting** ad alanını dahil etmek için projenize **Microsoft.AspNet.WebApi.Client** NuGet paketini de eklemeniz gerekir.

Bu öğretici, IoT Hub sanal cihaz uygulaması oluşturma adımlarında size rehberlik eder. Cihaz uygulamanıza gerekli kodu eklemek için [Azure IoT Hub için Bağlı Hizmet][lnk-connected-service] Visual Studio uzantısını da kullanabilirsiniz.

> [!NOTE]
> Sade ve basit bir anlatım gözetildiği için bu öğretici herhangi bir yeniden deneme ilkesi uygulamaz. [Geçici Hata İşleme][lnk-transient-faults] adlı MSDN makalesinde önerildiği üzere, üretim kodunda yeniden deneme ilkelerini (üstel geri alma gibi) uygulamanız gerekir.
> 
> 

## <a name="run-the-apps"></a>Uygulamaları çalıştırma
Şimdi uygulamaları çalıştırmaya hazırsınız.

1. Visual Studio'daki Çözüm Gezgini'nde çözümünüze sağ tıklayın ve ardından **Başlangıç projelerini ayarla**'ya tıklayın. **Birden fazla başlangıç projesi**'ni seçin ve ardından hem **ReadDeviceToCloudMessages** hem de **SimulatedDevice** projeleri için eylem olarak **Başla**'yı seçin.
   
    ![Başlangıç projesinin özellikleri][41]
2. Her iki uygulamanın da çalışmasını başlatmak için **F5**'e basın. **SimulatedDevice** uygulamasından konsol çıktısı, sanal cihaz uygulamanızın IoT hub'ınıza gönderdiği iletileri gösterir. **ReadDeviceToCloudMessages** uygulamasından konsol çıktısı, IoT hub'ınızın aldığı iletileri gösterir.
   
    ![Uygulamalardan konsol çıktısı][42]
3. [Azure portalındaki][lnk-portal] **Kullanım** kutucuğu, IoT hub'ına gönderilen ileti sayısını gösterir:
   
    ![Azure portalı Kullanım kutucuğu][43]

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure portalında bir IoT hub'ı yapılandırdınız ve ardından IoT hub'ının kimlik kayıt defterinde bir cihaz kimliği oluşturdunuz. Bu cihaz kimliğini, sanal cihaz uygulamasının, IoT hub'ına cihazdan buluta iletileri göndermesini sağlamak için kullandınız. Ayrıca, IoT hub’ı tarafından alınan iletileri görüntüleyen bir uygulama da oluşturdunuz. 

IoT Hub’ı kullanmaya başlamak ve diğer IoT senaryolarını keşfetmek için bkz:

* [Cihazınızı bağlama][lnk-connect-device]
* [Cihaz yönetimini kullanmaya başlama][lnk-device-management]
* [IoT Gateway SDK'sını kullanmaya başlama][lnk-gateway-SDK]

IoT çözümünüzün nasıl genişletileceğini ve cihazdan buluta iletilerin doğru ölçekte nasıl işleneceğini öğrenmek için [Cihazdan buluta iletileri işleme][lnk-process-d2c-tutorial] öğreticisine bakın.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


---
title: Azure IoT Hub ile buluttan cihaza iletiler (.NET) | Microsoft Docs
description: .NET için Azure IoT SDK 'larını kullanarak Azure IoT Hub 'ından bir cihaza buluttan cihaza ileti gönderme. Bir cihaz uygulamasını, buluttan cihaza iletiler alacak şekilde değiştirirsiniz ve bir arka uç uygulamasını, buluttan cihaza iletileri gönderecek şekilde değiştirirsiniz.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 99acd43128bedcf3dba470f84c0a406861d77e2d
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147787"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>IoT Hub (.NET) ile buluttan cihazınıza ileti gönderme

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub, milyonlarca cihaz ile bir çözüm arka ucu arasında güvenilir ve güvenli çift yönlü iletişimin sağlanmasına yardımcı olan, tam olarak yönetilen bir hizmettir. [Bir cihazdan IoT Hub 'ına yönelik Telemetriyi, bir](quickstart-send-telemetry-dotnet.md) IoT Hub 'ı oluşturmayı, bu kodda bir cihaz kimliği sağlamayı ve cihazdan buluta iletiler gönderen bir cihaz uygulamasını nasıl kodlayacağınızı gösterir.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu öğretici, [bir cihazdan IoT Hub 'ına telemetri gönderme hakkında bir](quickstart-send-telemetry-dotnet.md)yapı oluşturur. Aşağıdaki görevlerin nasıl yapılacağını gösterir:

* Çözüm arka ucundan, IoT Hub aracılığıyla buluttan cihaza iletileri tek bir cihaza gönderin.

* Bir cihazda buluttan cihaza iletiler alın.

* Çözüm arka uçta, IoT Hub bir cihaza gönderilen iletiler için teslim bildirimi (*geri bildirim*) isteyin.

[IoT Hub Ile D2C ve C2D mesajlaşma](iot-hub-devguide-messaging.md)'da buluttan cihaza iletiler hakkında daha fazla bilgi edinebilirsiniz.

Bu öğreticinin sonunda iki .NET konsol uygulaması çalıştırırsınız.

* **SimulatedDevice**. Bu uygulama, IoT Hub 'ınıza bağlanır ve buluttan cihaza iletileri alır. Bu uygulama, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-dotnet.md)bölümünde oluşturulan uygulamanın değiştirilmiş bir sürümüdür.

* **Sendcloudtodevice**. Bu uygulama, IoT Hub aracılığıyla cihaz uygulamasına buluttan cihaza bir ileti gönderir ve ardından teslim alındı bildirimi alır.

> [!NOTE]
> IoT Hub, [Azure IoT cihaz SDK 'ları](iot-hub-devguide-sdks.md)aracılığıyla C, Java, Python ve JavaScript gibi birçok cihaz platformu ve DILI için SDK desteğine sahiptir. Cihazınızı Bu öğreticinin koduna bağlama ve genellikle Azure IoT Hub 'e yönelik adım adım yönergeler için, [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)' na bakın.
>

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio

* Etkin bir Azure hesabı. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

## <a name="receive-messages-in-the-device-app"></a>Cihaz uygulamasında ileti alma

Bu bölümde, IoT Hub 'ından buluttan cihaza iletileri almak için [bir cihazdan telemetri gönderme bölümünde oluşturduğunuz cihaz uygulamasını bir IoT Hub 'ına](quickstart-send-telemetry-dotnet.md) değiştirin.

1. Visual Studio 'da, **SimulatedDevice** projesinde, **Program** sınıfına aşağıdaki yöntemi ekleyin.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Aşağıdaki yöntemi, `Console.ReadLine()` satırın hemen öncesine, **Main** yöntemine ekleyin:

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync` Yöntemi, cihaz tarafından alındığı sırada alınan iletiyi zaman uyumsuz olarak döndürür. Belirli bir zaman aşımı süresinden sonra *null* değerini döndürür. Bu örnekte, bir dakikalık varsayılan değer kullanılır. Uygulama *null*aldığında yeni iletileri beklemeye devam etmelidir. Bu gereksinim, `if (receivedMessage == null) continue` çizginin nedenidir.

' A yapılan `CompleteAsync()` çağrı, iletinin başarıyla işlendiğini IoT Hub bildirir. İleti, cihaz sırasından güvenle kaldırılabilir. Cihaz uygulamasının ileti işlemeyi tamamlamasını önleyen bir sorun oluştuysa, IoT Hub yeniden teslim eder. Aynı iletiyi birden çok kez almak aynı sonucu ürettiğinden, cihaz uygulamasındaki ileti işleme mantığı *ıdempotent*olmalıdır.

Bir uygulama bir iletiyi geçici olarak iptal edebilir ve bu da IoT Hub 'ı, gelecekteki tüketim için kuyruktaki iletiyi korur. Ya da uygulama, iletiyi sıradan kalıcı olarak kaldıran bir iletiyi reddedebilirler. Buluttan cihaza ileti yaşam döngüsü hakkında daha fazla bilgi için, bkz. [D2C and C2D Messaging with IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Aktarım olarak MQTT veya AMQP yerine https kullandığınızda, `ReceiveAsync` yöntemi hemen döndürür. HTTPS ile buluttan cihaza iletiler için desteklenen model, iletileri seyrek olarak denetleyen (25 dakikada bir daha az) zaman zaman bağlı cihazlardır. Daha fazla HTTPS vermek istekleri IoT Hub azaltarak sonuçları alır. MQTT, AMQP ve HTTPS desteği arasındaki farklar ve IoT Hub azaltma hakkında daha fazla bilgi için, bkz. [D2C and C2D Messaging with IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-dotnet.md)bölümünde oluşturduğunuz IoT Hub 'ı aracılığıyla buluttan cihaza iletileri göndermek için bir arka uç hizmeti oluşturursunuz. Buluttan cihaza iletiler göndermek için hizmetinize **hizmet bağlantısı** izni verilmesi gerekir. Varsayılan olarak, her IoT Hub, bu izni veren **hizmet** adlı paylaşılan bir erişim ilkesiyle oluşturulur.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Buluttan cihaza ileti gönderme

Artık cihaz uygulamasına buluttan cihaza iletiler gönderen bir .NET konsol uygulaması yazarsınız.

1. Geçerli Visual Studio çözümünde **Dosya** > **Yeni** > **Proje**' yi seçin. **Yeni proje oluştur**' da **konsol uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.

1. Projeyi *Sendcloudtodevice*olarak adlandırın. **Çözüm**altında **çözüme Ekle** ' yi seçin ve .NET Framework en son sürümünü kabul edin. Projeyi oluşturmak için **Oluştur**'u seçin.

   ![Visual Studio 'da yeni bir proje yapılandırma](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. Çözüm Gezgini ' de, yeni çözüme sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paketlerini Yönet**bölümünde, **Araştır**' ı seçin ve ardından **Microsoft. Azure. Devices**' i arayıp seçin. **Yüklemeyi**seçin.

   Bu adım, [Azure IoT hizmeti SDK 'Sı NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.Devices/)'ni indirir, yükler ve buna bir başvuru ekler.

1. **Program.cs** dosyasının en `using` üstüne aşağıdaki ifadeyi ekleyin.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu değerini, [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)' da daha önce kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. **Program** sınıfına aşağıdaki yöntemi ekleyin. Cihaz adını [bir cihazdan IoT Hub 'ına bir cihazdan Telemetriyi gönderme](quickstart-send-telemetry-dotnet.md)bölümünde kullandığınız şekilde ayarlayın.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Bu yöntem, cihaza, `myFirstDevice`kimliğine sahip yeni bir buluttan cihaza ileti gönderir. Bu parametreyi yalnızca [bir cihazdan IoT Hub 'ına Telemetriyi göndermek için](quickstart-send-telemetry-dotnet.md)kullanılan bir cihazdan değiştirdiyseniz değiştirin.

1. Son olarak, aşağıdaki satırları **Main** yöntemine ekleyin.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. Çözüm Gezgini 'nde çözümünüze sağ tıklayın ve **Başlangıç projelerini ayarla**' yı seçin.

1. **Ortak özellikler** > **Başlangıç projesinde** **birden çok başlangıç**projesi ' ni seçin ve ardından **readdevicetocloudmessages**, **SimulatedDevice**ve sendcloudtodevice için **başlatma** eylemini seçin. Değişikliklerinizi kaydetmek için **Tamam**’ı seçin.

1. **F5**tuşuna basın. Üç uygulamanın tümünün başlaması gerekir. **Sendcloudtodevice** pencerelerini seçin ve **ENTER**tuşuna basın. Cihaz uygulaması tarafından alınan iletiyi görmeniz gerekir.

   ![Uygulama alma iletisi](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Teslim geri bildirimi alma

Her buluttan cihaza ileti için IoT Hub teslim (veya sona erme) bildirimleri talep etmek mümkündür. Bu seçenek, çözüm arka ucunun yeniden deneme veya dengeleme mantığını kolayca bilgilendirmesini sağlar. Buluttan cihaza geri bildirim hakkında daha fazla bilgi için, bkz. [D2C and C2D Messaging with IoT Hub](iot-hub-devguide-messaging.md).

Bu bölümde, **Sendcloudtodevice** uygulamasını, geri bildirim Isteyecek ve IoT Hub 'ından alacak şekilde değiştirirsiniz.

1. Visual Studio 'da, **Sendcloudtodevice** projesinde, **Program** sınıfına aşağıdaki yöntemi ekleyin.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Bu alma deseninin, cihaz uygulamasından gelen buluttan cihaza iletileri almak için kullandığı aynı olduğunu göz önünde bir şekilde yapın.

1. **Main** yöntemine hemen sonra `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`aşağıdaki satırı ekleyin.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Buluttan cihaza iletinizin teslimine ilişkin geri bildirim istemek için **Sendcloudtodevicemessageasync** yönteminde bir özellik belirtmeniz gerekir. Aşağıdaki satırı, `var commandMessage = new Message(...);` satırdan hemen sonra ekleyin.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. **F5**tuşuna basarak uygulamaları çalıştırın. Üç uygulamanın tümünün başlatılmasını görmeniz gerekir. **Sendcloudtodevice** pencerelerini seçin ve **ENTER**tuşuna basın. Cihaz uygulaması tarafından alınan iletiyi ve birkaç saniye sonra, **Sendcloudtodevice** uygulamanız tarafından alınan geri bildirim iletisini görmeniz gerekir.

   ![Uygulama alma iletisi](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Kolaylık olması için, bu öğretici herhangi bir yeniden deneme ilkesi uygulamaz. Üretim kodunda, [geçici hata işleme](/azure/architecture/best-practices/transient-faults)bölümünde önerildiği gibi, üstel geri alma gibi yeniden deneme ilkeleri uygulamanız gerekir.
>

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır, buluttan cihaza iletileri gönderme ve alma hakkında daha fazla öğrendiniz.

IoT Hub kullanan uçtan uca çözümlerin tam örneklerini görmek için bkz. [Azure IoT uzaktan izleme Çözüm Hızlandırıcısı](https://docs.microsoft.com/azure/iot-suite/).

IoT Hub çözümleri geliştirme hakkında daha fazla bilgi edinmek için [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)' na bakın.

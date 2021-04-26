---
title: Azure IoT Hub ile buluttan cihaza iletiler (.NET) | Microsoft Docs
description: .NET için Azure IoT SDK 'larını kullanarak Azure IoT Hub 'ından bir cihaza buluttan cihaza ileti gönderme. Bir cihaz uygulamasını, buluttan cihaza iletiler alacak şekilde değiştirirsiniz ve bir arka uç uygulamasını, buluttan cihaza iletileri gönderecek şekilde değiştirirsiniz.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: d8df9884c0104792240d85d9ebd4235ef2a18741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142349"
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

* Etkin bir Azure hesabı. Hesabınız yoksa, yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

* Güvenlik duvarınızdaki 8883 numaralı bağlantı noktasını açık olduğundan emin olun. Bu makaledeki cihaz örneği, 8883 numaralı bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Cihaz uygulamasında ileti alma

Bu bölümde, IoT Hub 'ından buluttan cihaza iletileri almak için [bir cihazdan telemetri gönderme bölümünde oluşturduğunuz cihaz uygulamasını bir IoT Hub 'ına](quickstart-send-telemetry-dotnet.md) değiştirin.

1. Visual Studio 'da, **SimulatedDevice** projesinde aşağıdaki yöntemi **SimulatedDevice** sınıfına ekleyin.

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

1. Aşağıdaki yöntemi, satırın hemen öncesine, **Main** yöntemine ekleyin `Console.ReadLine()` :

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync`Yöntemi, cihaz tarafından alındığı sırada alınan iletiyi zaman uyumsuz olarak döndürür. Belirli bir zaman aşımı süresinden sonra *null* değerini döndürür. Bu örnekte, bir dakikalık varsayılan değer kullanılır. Uygulama *null* aldığında yeni iletileri beklemeye devam etmelidir. Bu gereksinim, çizginin nedenidir `if (receivedMessage == null) continue` .

' A yapılan çağrı, `CompleteAsync()` iletinin başarıyla işlendiğini ve iletinin cihaz sırasından güvenle kaldırılabileceği IoT Hub bildirir. İşlem, kullandığı Protokolden bağımsız olarak işlemi başarıyla tamamlandığında cihaz bu yöntemi çağırmalıdır.

AMQP ve HTTPS ile, ancak MQTT ile cihaz de şunları yapabilir:

* İleti, gelecekteki tüketim için cihaz sırasındaki iletiyi saklamaya IoT Hub neden olan bir iletiyi iptal edin.
* İletiyi, cihaz sırasından kalıcı olarak kaldıran bir iletiyi reddedin.

Cihazın iletiyi tamamlamasını, çıkarmasını veya reddetmesini önleyen bir sorun oluşursa IoT Hub, sabit bir zaman aşımı süresinden sonra iletiyi teslim için yeniden kuyruğa alır. Bu nedenle, cihaz uygulamasındaki ileti işleme mantığı *ıdempotent* olmalıdır, böylece aynı iletiyi birden çok kez almak aynı sonucu üretir.

Bulut-cihaz ileti yaşam döngüsünün ayrıntıları dahil IoT Hub, buluttan cihaza iletileri nasıl işlediği hakkında daha ayrıntılı bilgi için bkz. [IoT Hub 'ından buluttan cihaza Ileti gönderme](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Aktarım olarak MQTT veya AMQP yerine HTTPS kullandığınızda, `ReceiveAsync` yöntemi hemen döndürür. HTTPS ile buluttan cihaza iletiler için desteklenen model, iletileri seyrek olarak denetleyen (en az 25 dakikada bir) zaman zaman bağlı cihazlardır. Daha fazla HTTPS vermek istekleri IoT Hub azaltarak sonuçları alır. MQTT, AMQP ve HTTPS desteği arasındaki farklar hakkında daha fazla bilgi için bkz. [buluttan cihaza iletişim Kılavuzu](iot-hub-devguide-c2d-guidance.md) ve [iletişim protokolü seçme](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-dotnet.md)bölümünde oluşturduğunuz IoT Hub 'ı aracılığıyla buluttan cihaza iletileri göndermek için bir arka uç hizmeti oluşturursunuz. Buluttan cihaza iletiler göndermek için hizmetinize **hizmet bağlantısı** izni verilmesi gerekir. Varsayılan olarak, her IoT Hub, bu izni veren **hizmet** adlı paylaşılan bir erişim ilkesiyle oluşturulur.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Buluttan cihaza ileti gönderme

Bu bölümde, sanal cihaz uygulamasına buluttan cihaza iletiler gönderen bir .NET konsol uygulaması oluşturacaksınız.

1. Geçerli Visual Studio çözümünde **Dosya**  >  **Yeni**  >  **Proje**' yi seçin. **Yeni proje oluştur**' da **konsol uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.

1. Projeyi *Sendcloudtodevice* olarak adlandırın. **Çözüm** altında **çözüme Ekle** ' yi seçin ve .NET Framework en son sürümünü kabul edin. Projeyi oluşturmak için **Oluştur**'u seçin.

   ![Visual Studio 'da yeni bir proje yapılandırma](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. Çözüm Gezgini ' de, yeni projeye sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paketlerini Yönet** bölümünde, **Araştır**' ı seçin ve ardından **Microsoft. Azure. Devices**' i arayıp seçin. **Yüklemeyi** seçin.

   Bu adım, [Azure IoT hizmeti SDK 'Sı NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.Devices/)'ni indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` Ifadeyi **program. cs** dosyasının üst kısmına ekleyin.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. `{iot hub connection string}`Yer tutucu değerini, daha önce [IoT Hub bağlantı dizesini alın](#get-the-iot-hub-connection-string)bölümünde not ettiğiniz IoT Hub bağlantı dizesiyle değiştirin. `{device id}`Yer tutucu değerini, [bir cihazdan IoT Hub 'ına bir cihazdan Telemetriyi gönder](quickstart-send-telemetry-dotnet.md) hızlı başlangıç listesine EKLEDIĞINIZ cihazın cihaz kimliğiyle değiştirin.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   static string targetDevice = "{device id}";
   ```

1. Cihazınıza bir ileti göndermek için **Program** sınıfına aşağıdaki yöntemi ekleyin.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync(targetDevice, commandMessage);
   }
   ```

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

1. **Ortak özellikler**  >  **Başlangıç projesinde** **birden çok başlangıç** projesi ' ni seçin ve ardından **SimulatedDevice** ve **sendcloudtodevice** için **başlatma** eylemini seçin. Değişikliklerinizi kaydetmek için **Tamam**’ı seçin.

1. **F5** tuşuna basın. Her iki uygulama da başlamalıdır. **Sendcloudtodevice** penceresini seçin ve **ENTER** tuşuna basın. Cihaz uygulaması tarafından alınan iletiyi görmeniz gerekir.

   ![Cihaz uygulaması alma iletisi](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

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

1. **Main** yöntemine hemen sonra aşağıdaki satırı ekleyin `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Buluttan cihaza iletinizin teslimine ilişkin geri bildirim istemek için **Sendcloudtodevicemessageasync** yönteminde bir özellik belirtmeniz gerekir. Aşağıdaki satırı, satırdan hemen sonra ekleyin `var commandMessage = new Message(...);` .

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. **F5** tuşuna basarak uygulamaları çalıştırın. Her iki uygulamanın da başlatılmasını görmeniz gerekir. **Sendcloudtodevice** penceresini seçin ve **ENTER** tuşuna basın. Cihaz uygulaması tarafından alınan iletiyi ve birkaç saniye sonra, **Sendcloudtodevice** uygulamanız tarafından alınan geri bildirim iletisini görmeniz gerekir.

   ![Cihaz uygulaması ileti ve hizmet uygulaması geri bildirimi alma](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Kolaylık olması için, bu öğretici herhangi bir yeniden deneme ilkesi uygulamaz. Üretim kodunda, [geçici hata işleme](/azure/architecture/best-practices/transient-faults)bölümünde önerildiği gibi, üstel geri alma gibi yeniden deneme ilkeleri uygulamanız gerekir.
>

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır, buluttan cihaza iletileri gönderme ve alma hakkında daha fazla öğrendiniz.

IoT Hub kullanan uçtan uca çözümlerin tam örneklerini görmek için bkz. [Azure IoT uzaktan izleme Çözüm Hızlandırıcısı](/azure/iot-suite/).

IoT Hub çözümleri geliştirme hakkında daha fazla bilgi edinmek için [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)' na bakın.
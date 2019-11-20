---
title: Azure IoT Hub X. 509.440 güvenliği öğreticisi | Microsoft Docs
description: Sanal bir ortamda Azure IoT Hub 'ınızdaki X. 509.952 tabanlı güvenliği kullanmaya başlayın.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 32219eeaee7980b685ac3453c6af3beff716abe2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824077"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Azure IoT Hub 'ınızda X. 509.440 güvenliğini ayarlama

Bu öğreticide, *X. 509.952 sertifika kimlik doğrulamasını*kullanarak Azure IoT Hub 'ınızı güvence altına almak için gereken adımlar gösterilmektedir. Çizim amacına yönelik olarak, Windows makinenizde yerel olarak sertifika oluşturmak için OpenSSL açık kaynaklı aracını kullanırız. Bu öğreticiyi yalnızca test amacıyla kullanmanızı öneririz. Üretim ortamında, sertifikaları bir *kök sertifika yetkilisinden (CA)* satın almalısınız.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticide, aşağıdaki kaynakların size hazırlanmaya yönelik olması gerekir:

* Azure aboneliğiniz ile bir IoT Hub 'ı oluşturdunuz. Ayrıntılı adımlar için bkz. [Portal üzerinden IoT Hub 'ı oluşturma](iot-hub-create-through-portal.md) .

* [Visual studio 2017 veya Visual studio 2019](https://www.visualstudio.com/vs/) yüklü.

## <a name="get-x509-ca-certificates"></a>X. 509.952 CA sertifikalarını al

IoT Hub X. 509.440 sertifika tabanlı güvenlik, kök sertifikayı ve yaprak sertifikaya kadar tüm ara sertifikaları içeren bir [X. 509.440 sertifika zinciriyle](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)başlamanız gerektirir.

Sertifikalarınızı almak için aşağıdaki yollarla seçim yapabilirsiniz:

* Bir *kök sertifika yetkilisinden (CA)* X. 509.440 sertifikaları satın alın. Bu yöntem, üretim ortamları için önerilir.

* [OpenSSL](https://www.openssl.org/)gibi üçüncü taraf bir araç kullanarak kendi X. 509.440 sertifikalarınızı oluşturun. Bu teknik, test ve geliştirme amacıyla uygundur. PowerShell veya Bash kullanarak test CA sertifikaları oluşturma hakkında bilgi için bkz. [örnek ve öğreticiler için test CA sertifikalarını yönetme](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) . Bu öğreticinin geri kalanı, [örnek ve öğreticiler için test CA sertifikalarını yönetme](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)bölümündeki yönergeleri izleyerek oluşturulan test CA sertifikalarını kullanır.

* Var olan bir kök CA sertifikası tarafından imzalanmış bir [X. 509.440 ara CA sertifikası](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) oluşturun ve hub 'a yükleyin. Ara sertifika karşıya yüklenip doğrulandıktan sonra, aşağıda belirtildiği gibi, aşağıda bahsedilen bir kök CA sertifikası yerine kullanılabilir. OpenSSL ([OpenSSL REQ](https://www.openssl.org/docs/man1.1.0/man1/req.html) ve [OpenSSL CA](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) gibi araçlar, bir ara CA sertifikası oluşturmak ve imzalamak için kullanılabilir.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>X. 509.440 CA sertifikalarını IoT Hub 'ınıza kaydetme

Bu adımlarda, Portal üzerinden IoT Hub 'ınıza yeni bir sertifika yetkilisini nasıl ekleyeceğiniz gösterilir.

1. Azure portal, IoT Hub 'ınıza gidin ve hub için **ayarlar** > **Sertifikalar** ' ı seçin.

1. Yeni bir sertifika eklemek için **Ekle** ' yi seçin.

1. **Sertifika adı**alanına kolay bir görünen ad girin ve bilgisayarınızdan önceki bölümde oluşturduğunuz sertifika dosyasını seçin.

1. Sertifikanızın başarıyla karşıya yüklendiğini belirten bir bildirim aldıktan sonra **Kaydet**' i seçin.

    ![Sertifikayı karşıya yükleme](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Sertifikanız, sertifika listesinde **doğrulanmamış**durumuyla görüntülenir.

1. **Sertifika ayrıntılarını**göstermek için yeni eklediğiniz sertifikayı seçin ve ardından **doğrulama kodu oluştur**' u seçin.

   ![Sertifikayı doğrula](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. **Doğrulama kodunu** panoya kopyalayın. Sertifika sahipliğini doğrulamak için bunu kullanırsınız.

1. [Örnekler ve öğreticiler için test CA sertifikalarını yönetme](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)içindeki 3. adımı izleyin.  Bu işlem, bir imza oluşturan X. 509.440 CA sertifikanız ile ilişkili özel anahtarla doğrulama kodunuzu imzalar. Bu imzalama işlemini gerçekleştirmek için kullanabileceğiniz araçlar vardır, örneğin, OpenSSL. Bu işlem, [elinde bulunan kanıtı](https://tools.ietf.org/html/rfc5280#section-3.1)olarak bilinir.

1. **Sertifika ayrıntıları**' nda, **doğrulama sertifikası. pek veya. cer dosyası**' nın altında, imza dosyasını bulup açın. Ardından **Doğrula**' yı seçin.

   Sertifikanızın durumu **doğrulandı**olarak değişir. Sertifika otomatik olarak güncelleştirmezse **Yenile** ' yi seçin.

## <a name="create-an-x509-device-for-your-iot-hub"></a>IoT Hub 'ınız için bir X. 509.952 cihazı oluşturma

1. Azure portal IoT Hub 'ınıza gidin ve ardından **IoT cihazları** > **araştırıcılar** ' nı seçin.

1. Yeni bir cihaz eklemek için **Yeni** ' yi seçin.

1. **CIHAZ kimliği**' nde kolay bir görünen ad girin. **Kimlik doğrulama türü**Için, **X. 509.440 CA imzalanmış**' ı seçin ve ardından **Kaydet**' i seçin.

   ![Portalda X. 509.952 cihazı oluşturma](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>X. 509.952 cihazlarınızın X. 509.440 sertifikalarıyla kimlik doğrulamasını yapın

X. 509.952 cihazınızın kimliğini doğrulamak için önce cihazı CA sertifikasıyla imzalamanız gerekir. Yaprak cihazların imzalanması normalde üretim tesisnde yapılır, üretim araçları da buna göre etkindir. Cihaz bir üreticiden diğerine geçtiğinde, her bir üreticinin imzalama eylemi, zincirde bir ara sertifika olarak yakalanır. Sonuç CA sertifikasından cihazın yaprak sertifikasına olan bir sertifika zinciridir. [Örnek ve öğreticiler için test CA sertifikalarını yönetme](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) adım 4 ' te bir cihaz sertifikası oluşturulur.

Ardından, IoT Hub 'ınız için kaydedilen X. 509.440 C# cihazının benzetimini yapmak üzere bir uygulama oluşturmayı göstereceğiz. Sanal cihazdan hub 'ınıza sıcaklık ve nem değerleri göndereceğiz. Bu öğreticide yalnızca cihaz uygulaması oluşturacağız. Bu sanal cihaz tarafından gönderilen olaylara yanıt gönderecek IoT Hub hizmet uygulaması oluşturmak için okuyucuların bir alıştırma olarak kalır. Uygulama C# , [örnek ve ÖĞRETICILER için test CA sertifikalarını yönetme](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)bölümündeki adımları izlediğinizi varsayar.

1. Visual Studio 'yu açın, **Yeni proje oluştur**' u seçin ve ardından **konsol uygulaması (.NET Framework)** proje şablonunu seçin. **İleri**’yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde projeyi *SimulateX509Device*olarak adlandırın ve ardından **Oluştur**' u seçin.

   ![Visual Studio 'da X. 509.952 cihaz projesi oluşturma](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Çözüm Gezgini, **SimulateX509Device** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paket Yöneticisi**'Nde, **Araştır** ' ı seçin ve arama yapın ve **Microsoft. Azure. Devices. Client**' ı seçin. **Yükle**’yi seçin.

   ![Visual Studio 'da cihaz SDK 'Sı NuGet paketi ekleme](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Bu adım, Azure IoT cihaz SDK 'Sı NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin:

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Yukarıdaki bölümde _< your_device_id >_ yerine kullandığınız kolay cihaz adını kullanın.

1. Sıcaklık ve nem için rastgele sayılar oluşturmak ve bu değerleri hub 'a göndermek için aşağıdaki işlevi ekleyin:

    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. Son olarak, aşağıdaki kod satırlarını **ana** işleve ekleyerek, kurulum için gereken _cihaz-kimliği_, _IoT-Hub-adı_ve mutlak ve-------------,-,- _Dosya_ ve--------dosyanızın yer tutucuları

    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Bu kod, X. 509.952 cihazınız için bağlantı dizesi oluşturarak IoT Hub 'ınıza bağlanır. Başarıyla bağlandıktan sonra, merkeze sıcaklık ve nem olayları gönderir ve yanıtını bekler.

1. Uygulamayı çalıştırın. Bu uygulama bir *. pfx* dosyasına eriştiği için bu uygulamayı yönetici olarak çalıştırmanız gerekebilir.

   1. Visual Studio çözümünü derleyin.

   1. **Yönetici olarak çalıştır**'ı kullanarak yeni bir komut istemi penceresi açın.  

   1. Çözümünüzü içeren klasöre gidin ve çözüm klasörü içindeki *bin/Debug* yoluna gidin.

   1. Komut isteminden **SimulateX509Device. exe** uygulamasını çalıştırın.

   Cihazınızın hub 'a başarıyla bağlanmasını ve olayları göndermesini görmeniz gerekir.

   ![Cihaz uygulamasını Çalıştır](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Sonraki adımlar

IoT çözümünüzün güvenliğini sağlama hakkında daha fazla bilgi edinmek için bkz.:

* [IoT güvenlik En Iyi uygulamaları](../iot-fundamentals/iot-security-best-practices.md)

* [IoT güvenlik mimarisi](../iot-fundamentals/iot-security-architecture.md)

* [IoT dağıtımınızın güvenliğini sağlama](../iot-fundamentals/iot-security-deployment.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [Azure IoT Edge ile uç cihazlara AI dağıtma](../iot-edge/tutorial-simulate-device-linux.md)

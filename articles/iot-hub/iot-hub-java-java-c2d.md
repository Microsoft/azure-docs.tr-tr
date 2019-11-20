---
title: Azure IoT Hub ile buluttan cihaza iletiler (Java) | Microsoft Docs
description: Java için Azure IoT SDK 'larını kullanarak Azure IoT Hub 'ından bir cihaza buluttan cihaza ileti gönderme. Bir sanal cihaz uygulamasını buluttan cihaza iletiler alacak şekilde değiştirirsiniz ve bir arka uç uygulamasını, buluttan cihaza iletileri gönderecek şekilde değiştirirsiniz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 4754d7c2182de79d583dce4982b33395bf037479
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161884"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>IoT Hub ile buluttan cihaza iletileri gönderme (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub, milyonlarca cihaz ile bir çözüm arka ucu arasında güvenilir ve güvenli çift yönlü iletişimin sağlanmasına yardımcı olan, tam olarak yönetilen bir hizmettir. [Bir cihazdan IoT Hub 'ına yönelik Telemetriyi, bir](quickstart-send-telemetry-java.md) IoT Hub 'ı oluşturmayı, bu kodda bir cihaz kimliği sağlamayı ve cihazdan buluta iletiler gönderen bir sanal cihaz uygulamasını nasıl kodlayacağınızı gösterir.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu öğretici, [bir cihazdan IoT Hub 'ına telemetri gönderme hakkında bir](quickstart-send-telemetry-java.md)yapı oluşturur. Aşağıdakileri nasıl yapılacağını gösterir:

* Çözüm arka ucundan, IoT Hub aracılığıyla buluttan cihaza iletileri tek bir cihaza gönderin.

* Bir cihazda buluttan cihaza iletiler alın.

* Çözüm arka uçta, IoT Hub bir cihaza gönderilen iletiler için teslim bildirimi (*geri bildirim*) isteyin.

[IoT Hub geliştirici kılavuzunda buluttan cihaza iletiler](iot-hub-devguide-messaging.md)hakkında daha fazla bilgi edinebilirsiniz.

Bu öğreticinin sonunda iki Java konsol uygulaması çalıştırırsınız:

* **benzetimli-Device**, bir cihazdan telemetri göndererek IoT Hub 'ınıza bağlanan ve buluttan cihaza iletiler alan bir [IoT Hub 'ına bir cihazdan telemetri gönderme](quickstart-send-telemetry-java.md).

* IoT Hub aracılığıyla sanal cihaz uygulamasına buluttan cihaza ileti gönderen ve sonra teslim onayını alan **Send-C2D-messages**.

> [!NOTE]
> IoT Hub, Azure IoT cihaz SDK 'Ları aracılığıyla birçok cihaz platformu ve dili (C, Java, Python ve JavaScript dahil) için SDK desteğine sahiptir. Cihazınızı Bu öğreticinin koduna bağlama ve genellikle Azure IoT Hub 'e yönelik adım adım yönergeler için bkz. [Azure IoT Geliştirici Merkezi](https://azure.microsoft.com/develop/iot).

## <a name="prerequisites"></a>Önkoşullar

* [Bir cihazdan IoT Hub 'ına](quickstart-send-telemetry-java.md) veya [IoT Hub öğreticisiyle ileti yönlendirmeyi yapılandırma ile](tutorial-routing.md) Telemetriyi gönderme işleminin tamamen bir çalışma sürümü.

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). JDK 8 için karşıdan yüklemeye ulaşmak üzere **uzun süreli destek** altında **Java 8** ' i seçtiğinizden emin olun.

* [Maven 3](https://maven.apache.org/download.cgi)

* Etkin bir Azure hesabı. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

## <a name="receive-messages-in-the-simulated-device-app"></a>Sanal cihaz uygulamasında ileti alma

Bu bölümde, IoT Hub 'ından buluttan cihaza iletileri almak için [bir cihazdan Telemetriyi bir cihazdan IoT Hub 'ına](quickstart-send-telemetry-java.md) oluşturduğunuz sanal cihaz uygulamasını değiştirirsiniz.

1. Bir metin düzenleyicisi kullanarak simulated-device\src\main\java\com\mycompany\app\App.java dosyasını açın.

2. Aşağıdaki **Messagecallback** sınıfını, **uygulama** sınıfının içine bir iç içe sınıf olarak ekleyin. **Yürütme** yöntemi, cihaz IoT Hub bir ileti aldığında çağrılır. Bu örnekte cihaz her zaman IoT Hub 'ına iletiyi tamamladığını bildirir:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. **Appmessagecallback** örneği oluşturmak için **Main** metodunu değiştirin ve istemciyi şu şekilde açmadan önce **setmessagecallback** metodunu çağırın:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Taşıma olarak MQTT veya AMQP yerine HTTPS kullanırsanız, **Deviceclient** örneği IoT Hub seyrek 'tan (her 25 dakikada bir daha az) ileti olup olmadığını denetler. MQTT, AMQP ve HTTPS desteği arasındaki farklar ve IoT Hub azaltma hakkında daha fazla bilgi için, [IoT Hub geliştirici kılavuzunun mesajlaşma bölümüne](iot-hub-devguide-messaging.md)bakın.

4. Maven kullanarak **simulated-device** uygulamasını oluşturmak için, simulated-device klasöründeki komut isteminde aşağıdaki komutu yürütün:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-java.md)bölümünde oluşturduğunuz IoT Hub 'ı aracılığıyla buluttan cihaza iletileri göndermek için bir arka uç hizmeti oluşturursunuz. Buluttan cihaza iletiler göndermek için hizmetinize **hizmet bağlantısı** izni verilmesi gerekir. Varsayılan olarak, her IoT Hub, bu izni veren **hizmet** adlı paylaşılan bir erişim ilkesiyle oluşturulur.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Buluttan cihaza ileti gönderme

Bu bölümde, sanal cihaz uygulamasına buluttan cihaza iletiler gönderen bir Java konsol uygulaması oluşturursunuz. [Bir cihazdan IoT Hub 'ına bir cihazdan Telemetriyi gönder](quickstart-send-telemetry-java.md) hızlı başlangıç bölümünde eklediğiniz CIHAZıN cihaz kimliği gereklidir. [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde daha önce kopyaladığınız IoT Hub bağlantı dizesine da ihtiyacınız vardır.

1. Komut istemindeki aşağıdaki komutu kullanarak **Send-C2D-messages** adlı bir Maven projesi oluşturun. Bu komutun tek bir uzun komut olduğunu aklınızda:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Komut istemindeki yeni Send-C2D-messages klasörüne gidin.

3. Bir metin düzenleyicisi kullanarak, Send-C2D-messages klasöründeki POTM. xml dosyasını açın ve aşağıdaki bağımlılığı **Bağımlılıklar** düğümüne ekleyin. Bağımlılığı eklemek, IoT Hub hizmetinize iletişim kurmak için uygulamanızdaki **ıothub-Java-Service-Client** paketini kullanmanıza olanak sağlar:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven aramasını](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)kullanarak **IoT-Service-Client** ' ın en son sürümünü kontrol edebilirsiniz.

4. pom.xml dosyasını kaydedin ve kapatın.

5. Bir metin düzenleyicisi kullanarak send-c2d-messages\src\main\java\com\mycompany\app\App.java dosyasını açın.

6. Aşağıdaki **içeri aktarma** deyimlerini dosyaya ekleyin:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Aşağıdaki sınıf düzeyi değişkenleri **uygulama** sınıfına ekleyerek **{yourhubconnectionstring}** ve **{yourdeviceıd}** değerlerini daha önce not ettiğiniz değerlerle değiştirin:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. **Main** yöntemini aşağıdaki kodla değiştirin. Bu kod, IoT Hub 'ınıza bağlanır, cihazınıza bir ileti gönderir ve ardından cihazın aldığı ve iletiyi işlediği bir bildirim bekler:

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Kolaylık olması için, bu öğretici herhangi bir yeniden deneme ilkesi uygulamaz. Üretim kodunda, [geçici hata işleme](/azure/architecture/best-practices/transient-faults)makalesinde önerildiği gibi yeniden deneme ilkelerini (üstel geri alma gibi) uygulamanız gerekir.

9. Maven kullanarak **simulated-device** uygulamasını oluşturmak için, simulated-device klasöründeki komut isteminde aşağıdaki komutu yürütün:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

1. Sanal cihaz klasöründeki bir komut isteminde, IoT Hub 'ınıza telemetri göndermeye başlamak ve hub 'ınızdan gönderilen buluttan cihaza iletileri dinlemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Sanal cihaz uygulamasını çalıştırma](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Send-C2D-messages klasöründeki bir komut isteminde, buluttan cihaza ileti göndermek ve geri bildirim bildirimi için beklemek üzere aşağıdaki komutu çalıştırın:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Buluttan cihaza ileti göndermek için komutunu çalıştırın](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, buluttan cihaza iletileri gönderme ve alma hakkında daha fazla öğrendiniz.

IoT Hub kullanan uçtan uca çözümlerin tam örneklerini görmek için bkz. [Azure IoT Çözüm Hızlandırıcıları](https://azure.microsoft.com/documentation/suites/iot-suite/).

IoT Hub çözümleri geliştirme hakkında daha fazla bilgi edinmek için [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)' na bakın.

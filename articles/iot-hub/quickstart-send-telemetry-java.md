---
title: 'Hızlı başlangıç: Java ile Azure IoT Hub telemetri gönderme'
description: Bu hızlı başlangıçta bir IoT hub’a sanal telemetri göndermek ve bulutta işlemek üzere IoT hub’dan gelen telemetriyi okumak için iki örnek Java uygulaması çalıştırırsınız.
author: wesmc7777
manager: lizross
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 01/27/2021
ms.openlocfilehash: bc5a6583cc6f47a986379cc071a056751dede59c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066968"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Hızlı başlangıç: Azure IoT Hub 'ına telemetri gönderin ve Java uygulamasıyla okuyun

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Bu hızlı başlangıçta, Azure IoT Hub telemetri gönderirsiniz ve Java uygulamasıyla okuyabilirsiniz. IoT Hub, IoT cihazlarınızdan buluta depolama veya işleme amacıyla yüksek hacimlerde telemetri almanızı sağlayan bir Azure hizmetidir. Bu hızlı başlangıç, önceden yazılmış iki Java uygulaması kullanır: bir tane, Telemetriyi ve bir hub 'dan Telemetriyi okumak üzere bir tane. Bu iki uygulamayı çalıştırmadan önce bir IoT hub oluşturur ve hub’a bir cihaz kaydedersiniz.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Java SE Development Kit 8. [Azure ve Azure Stack Için Java uzun süreli destek](/java/azure/jdk/), **uzun süreli destek** altında **Java 8**' i seçin.

    Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli Java sürümünü doğrulayabilirsiniz:

    ```cmd/sh
    java -version
    ```

* [Apache Maven 3](https://maven.apache.org/download.cgi).

    Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli Maven sürümünü doğrulayabilirsiniz:

    ```cmd/sh
    mvn --version
    ```

* Azure-IoT-Samples- [Java deposu sayfasındaki](https://github.com/Azure-Samples/azure-iot-samples-java) **kod** düğmesini kullanarak Azure-IoT-Samples-Java deposunu indirin veya kopyalayın. 

    Bu makalede, depodaki [sanal cihaz](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/simulated-device) ve [Read-D2C-messages](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/read-d2c-messages) örnekleri kullanılmaktadır.

* Bağlantı noktası 8883 güvenlik duvarınızda açık. Bu hızlı başlangıçta bulunan cihaz örneği, 8883 bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Myjavadevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterildiği gibi **Myjavadevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı bu makalede da kullanmanız ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

    **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Aşağıdakine benzeyen cihaz bağlantı dizenizi not alın:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı başlangıçta kullanacaksınız.

3. Ayrıca, arka uç uygulamasının IoT Hub 'ınıza bağlanmasını ve iletileri almanızı sağlamak için IoT Hub 'ınızdaki _Event Hubs uyumlu uç nokta_, _Event Hubs uyumlu yol_ ve _hizmet birincil anahtarı_ gerekir. Aşağıdaki komutlar, IoT hub’ınız için şu değerleri alır:

     **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Bu üç değeri bir yere, daha sonra hızlı başlangıçta kullanacaksınız.

## <a name="send-simulated-telemetry"></a>Simülasyon telemetrisi gönderme

Simülasyon cihazı uygulaması, IoT hub’ınız üzerindeki cihaza özgü bir uç noktaya bağlanır ve sanal sıcaklık ve nem telemetrisi gönderir.

1. Yerel terminal penceresinde, örnek Java projesinin kök klasörüne gidin. Daha sonra **iot-hub\Quickstarts\simulated-device** klasörüne gidin.

2. **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** dosyasını istediğiniz bir metin düzenleyicide açın.

    Değişkenin değerini, `connString` daha önce bir değişiklik yaptığınız cihaz bağlantı dizesiyle değiştirin. Ardından **SimulatedDevice. Java**' da yaptığınız değişiklikleri kaydedin.

    ```java
    public class SimulatedDevice {
      // The device connection string to authenticate the device with your IoT hub.
      // Using the Azure CLI:
      // az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table

      //private static String connString = "{Your device connection string here}";    
      private static String connString = "HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}";    
     ```

3. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak gerekli kitaplıkları yükleyin ve simülasyon cihazı uygulamasını derleyin:

    ```cmd/sh
    mvn clean package
    ```

4. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak simülasyon cihazı uygulamasını çalıştırın:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    Aşağıdaki ekran görüntüsünde, simülasyon cihazı uygulaması, IoT hub’ınıza telemetri gönderdiğinde oluşan çıktı gösterilmektedir:

    ![Cihazın IoT Hub 'ınıza gönderdiği telemetriden çıkış](media/quickstart-send-telemetry-java/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub’ınızdan telemetri okuma

Arka uç uygulaması, IoT Hub’ınızdaki bir hizmet tarafı **Olaylar** uç noktasına bağlanır. Uygulama, simülasyon cihazınızdan gönderilen cihazdan buluta iletileri alır. IoT Hub arka uç uygulaması genellikle cihazdan buluta iletileri alıp işlemek için bulutta çalışır.

1. Başka bir yerel terminal penceresinde, örnek Java projesinin kök klasörüne gidin. Daha sonra **iot-hub\Quickstarts\read-d2c-messages** klasörüne gidin.

2. **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** dosyasını istediğiniz bir metin düzenleyicide açın. Aşağıdaki değişkenleri güncelleştirin ve yaptığınız değişiklikleri dosyaya kaydedin.

    | Değişken | Değer |
    | -------- | ----------- |
    | `EVENT_HUBS_COMPATIBLE_ENDPOINT` | Değişkenin değerini, daha önce bir değişiklik yaptığınız Event Hubs uyumlu uç nokta ile değiştirin. |
    | `EVENT_HUBS_COMPATIBLE_PATH`     | Değişkenin değerini, daha önce bir değişiklik yaptığınız Event Hubs uyumlu yol ile değiştirin. |
    | `IOT_HUB_SAS_KEY`                | Değişkenin değerini, daha önce bir değişiklik yaptığınız hizmet birincil anahtarıyla değiştirin. |

    ```java
    public class ReadDeviceToCloudMessages {
    
      private static final String EH_COMPATIBLE_CONNECTION_STRING_FORMAT = "Endpoint=%s/;EntityPath=%s;"
          + "SharedAccessKeyName=%s;SharedAccessKey=%s";
    
      // az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_ENDPOINT = "{your Event Hubs compatible endpoint}";
    
      // az iot hub show --query properties.eventHubEndpoints.events.path --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_PATH = "{your Event Hubs compatible name}";
    
      // az iot hub policy show --name service --query primaryKey --hub-name {your IoT Hub name}
      private static final String IOT_HUB_SAS_KEY = "{your service primary key}";    
    ```


3. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak gerekli kitaplıkları yükleyin ve arka uç uygulamasını derleyin:

    ```cmd/sh
    mvn clean package
    ```

4. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak arka uç uygulamasını çalıştırın:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    Aşağıdaki ekran görüntüsünde, arka uç uygulaması, simülasyon cihazı tarafından hub’a gönderilen telemetriyi aldığında oluşan çıktı gösterilmektedir:

    ![Arka uç uygulaması olarak çıktı, IoT Hub 'ınıza gönderilen telemetri alır](media/quickstart-send-telemetry-java/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, bir Java uygulaması kullanarak hub 'a sanal telemetri gönderdiniz ve basit bir arka uç uygulaması kullanarak hub 'ın Telemetriyi okuyaöğreneceksiniz.

Bir arka uç uygulamasından simülasyon cihazınızı denetlemeyi öğrenmek için sonraki hızlı başlangıçla devam edin.

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: IoT hub’a bağlı bir cihazı denetleme](quickstart-control-device-java.md)

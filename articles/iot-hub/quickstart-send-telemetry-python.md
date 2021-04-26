---
title: Hızlı başlangıç-Azure IoT Hub hızlı başlangıç 'a telemetri gönderme (Python) | Microsoft Docs
description: Bu hızlı başlangıçta, bir IoT hub’a sanal telemetri göndermek için örnek bir Python uygulaması çalıştırır ve IoT hub’dan telemetri okumak için bir yardımcı program kullanırsınız.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/16/2020
ms.openlocfilehash: 18163c3e5cdf83d736cdb52a3de028bf8890e728
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568582"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderme ve arka uç uygulamasıyla (Python) okuma

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Bu hızlı başlangıçta, sanal bir cihaz uygulamasından Azure IoT Hub aracılığıyla, işleme için bir arka uç uygulamasına telemetri gönderirsiniz. IoT Hub, IoT cihazlarınızdan buluta depolama veya işleme amacıyla yüksek hacimlerde telemetri almanızı sağlayan bir Azure hizmetidir. Bu hızlı başlangıç, önceden yazılmış iki Python uygulaması kullanır: tek bir telemetri göndermek için bir tane ve hub 'dan Telemetriyi okumak için. Bu iki uygulamayı çalıştırmadan önce bir IoT hub oluşturur ve hub’a bir cihaz kaydedersiniz.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7 +](https://www.python.org/downloads/). Desteklenen Python 'un diğer sürümleri için bkz. [Azure IoT cihaz özellikleri](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* GitHub 'dan [örnek bir Python projesi](https://github.com/Azure-Samples/azure-iot-samples-python/) . GitHub deposundaki **kod** düğmesini kullanarak örnekleri indirin veya kopyalayın.

* Bağlantı noktası 8883 güvenlik duvarınızda açık. Bu hızlı başlangıçta bulunan cihaz örneği, 8883 bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

    **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    **Mypythondevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterilen şekilde **Mypyıthondevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı bu makalede da kullanmanız ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

    **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Aşağıdakine benzeyen cihaz bağlantı dizenizi not alın:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı başlangıçta kullanacaksınız.

1. Ayrıca, arka uç uygulamasının IoT Hub 'ınıza bağlanmasını ve iletileri almanızı sağlamak için IoT Hub 'ınızdaki _Event Hubs uyumlu uç nokta_, _Event Hubs uyumlu yol_ ve _hizmet birincil anahtarı_ gerekir. Aşağıdaki komutlar, IoT hub’ınız için şu değerleri alır:

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Bu üç değeri bir yere, daha sonra hızlı başlangıçta kullanacaksınız.

## <a name="send-simulated-telemetry"></a>Simülasyon telemetrisi gönderme

Simülasyon cihazı uygulaması, IoT hub’ınız üzerindeki cihaza özgü bir uç noktaya bağlanır ve sanal sıcaklık ve nem telemetrisi gönderir.

1. Azure-IoT-Samples-Python [deposu sayfasındaki](https://github.com/Azure-Samples/azure-iot-samples-python/) **kod** düğmesini kullanarak Azure-IoT-Samples-Python deposunu indirin veya kopyalayın.

1. Yerel terminal penceresinde, örnek Python projesinin kök klasörüne gidin. Daha sonra **iot-hub\Quickstarts\simulated-device** klasörüne gidin.

1. **SimulatedDevice.py** dosyasını, istediğiniz bir metin düzenleyicide açın.

    Değişkenin değerini, `CONNECTION_STRING` daha önce bir değişiklik yaptığınız cihaz bağlantı dizesiyle değiştirin. Sonra **SimulatedDevice.py**' ye yaptığınız değişiklikleri kaydedin.

1. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak simülasyon cihazı uygulaması için gerekli kitaplıkları yükleyin:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak simülasyon cihazı uygulamasını çalıştırın:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Aşağıdaki ekran görüntüsünde, simülasyon cihazı uygulaması, IoT hub’ınıza telemetri gönderdiğinde oluşan çıktı gösterilmektedir:

    ![Simülasyon cihazını çalıştırma](media/quickstart-send-telemetry-python/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub’ınızdan telemetri okuma

Arka uç uygulaması, IoT Hub’ınızdaki bir hizmet tarafı **Olaylar** uç noktasına bağlanır. Uygulama, simülasyon cihazınızdan gönderilen cihazdan buluta iletileri alır. IoT Hub arka uç uygulaması genellikle cihazdan buluta iletileri alıp işlemek için bulutta çalışır.

> [!NOTE]
> Aşağıdaki adımlar, **read_device_to_cloud_messages_sync. Kopyala** zaman uyumlu örneğini kullanır. Aynı adımları zaman uyumsuz örnekle, **read_device_to_cloud_messages_async. Kopyala** ile gerçekleştirebilirsiniz.

1. Başka bir yerel terminal penceresinde, örnek Python projesinin kök klasörüne gidin. Daha sonra **iot-hub\Quickstarts\read-d2c-messages** klasörüne gidin.

2. **Read_device_to_cloud_messages_sync. Kopyala** dosyasını istediğiniz bir metin düzenleyicisinde açın. Aşağıdaki değişkenleri güncelleştirin ve yaptığınız değişiklikleri dosyaya kaydedin.

    | Değişken | Değer |
    | -------- | ----------- |
    | `EVENTHUB_COMPATIBLE_ENDPOINT` | Değişkenin değerini, daha önce bir değişiklik yaptığınız Event Hubs uyumlu uç nokta ile değiştirin. |
    | `EVENTHUB_COMPATIBLE_PATH`     | Değişkenin değerini, daha önce bir değişiklik yaptığınız Event Hubs uyumlu yol ile değiştirin. |
    | `IOTHUB_SAS_KEY`                | Değişkenin değerini, daha önce bir değişiklik yaptığınız hizmet birincil anahtarıyla değiştirin. |

3. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak arka uç uygulaması için gerekli kitaplıkları yükleyin:

    ```cmd/sh
    pip install azure-eventhub
    ```

4. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak arka uç uygulamasını derleyip çalıştırın:

    ```cmd/sh
    python read_device_to_cloud_messages_sync.py
    ```

    Aşağıdaki ekran görüntüsünde, arka uç uygulaması, simülasyon cihazı tarafından hub’a gönderilen telemetriyi aldığında oluşan çıktı gösterilmektedir:

    ![Arka uç uygulamasını çalıştırma](media/quickstart-send-telemetry-python/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, bir Python uygulaması kullanarak hub 'a sanal telemetri gönderdiniz ve basit bir arka uç uygulaması kullanarak hub 'ın Telemetriyi okuyaöğreneceksiniz.

Bir arka uç uygulamasından simülasyon cihazınızı denetlemeyi öğrenmek için sonraki hızlı başlangıçla devam edin.

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: IoT hub’a bağlı bir cihazı denetleme](quickstart-control-device-python.md)

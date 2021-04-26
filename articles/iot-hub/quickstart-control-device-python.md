---
title: Hızlı başlangıç-bir cihazı Azure IoT Hub hızlı başlangıç (Python) ile denetleme | Microsoft Docs
description: Bu hızlı başlangıçta iki örnek Python uygulaması çalıştırırsınız. Bir uygulama, hub’ınıza bağlı cihazları uzaktan denetleyebilen bir arka uç uygulamasıdır. Diğer uygulama, uzaktan denetlenebilen hub’ınıza bağlanan bir cihazın simülasyonunu yapar.
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
- devx-track-azurecli
ms.date: 09/14/2020
ms.openlocfilehash: 2ecc1ceb624f2f98bca47d4d1331f67c52fa54b4
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566920"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Hızlı Başlangıç: IoT hub’a bağlı bir cihazı denetleme (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Bu hızlı başlangıçta, Azure IoT Hub bağlı sanal cihazı denetlemek için doğrudan bir yöntem kullanırsınız. IoT Hub, IoT cihazlarınızı buluttan yönetmenize ve depolama ya da işleme için yüksek hacimli cihaz telemetrisine sahip olan bir Azure hizmetidir. IoT hub’ınıza bağlı bir cihazın davranışını uzaktan değiştirmek için doğrudan yöntemler kullanabilirsiniz. Bu hızlı başlangıçta iki Python uygulaması kullanılmaktadır: arka uç uygulamasından çağrılan doğrudan yöntemlere ve sanal cihazdaki doğrudan yöntemleri çağıran bir arka uç uygulamasına yanıt veren bir sanal cihaz uygulaması.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7 +](https://www.python.org/downloads/). Desteklenen Python 'un diğer sürümleri için bkz. [Azure IoT cihaz özellikleri](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* GitHub 'dan [örnek bir Python projesi](https://github.com/Azure-Samples/azure-iot-samples-python/) . GitHub deposundaki **kod** düğmesini kullanarak örnekleri indirin veya kopyalayın.

* Bağlantı noktası 8883 güvenlik duvarınızda açık. Bu hızlı başlangıçta bulunan cihaz örneği, 8883 bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Önceki [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-python.md) öğreticisini tamamladıysanız bu adımı atlayabilirsiniz.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Önceki [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-python.md) öğreticisini tamamladıysanız bu adımı atlayabilirsiniz.

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

    **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    **Mypythondevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterilen şekilde **Mypyıthondevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı da bu makalede kullanmanız gerekir ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirin.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Yeni kaydettiğiniz cihazın _cihaz bağlantı dizesini_ almak için aşağıdaki komutları Azure Cloud Shell'de çalıştırın:

    **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Aşağıdakine benzeyen cihaz bağlantı dizenizi not alın:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız.

3. Arka uç uygulamasının IoT hub’ınıza bağlanmasına ve iletileri almasına olanak sağlamak için bir _hizmet bağlantı dizesi_ de gerekir. Aşağıdaki komut, IoT hub'ınız için hizmeti bağlantı dizesini alır:

    **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub connection-string show \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Şu ifadeye benzer şekilde görünen hizmet bağlantı dizesini not edin:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız. Bu hizmet bağlantı dizesi, önceki adımda not ettiğiniz cihaz bağlantı dizesinden farklıdır.

## <a name="listen-for-direct-method-calls"></a>Doğrudan yöntem çağrılarını dinleme

Simülasyon cihazı, IoT hub’ınızdaki cihaza özgü bir uç noktaya bağlanır, sanal telemetri gönderir ve hub’ınızdan gelen doğrudan yöntem çağrılarını dinler. Bu hızlı başlangıçta, hub’dan gelen doğrudan yöntem çağrısı, telemetri gönderme aralığını değiştirmesini cihaza bildirir. Sanal cihaz, doğrudan yöntemini yürütmeden sonra hub 'ınıza bir bildirim gönderir.

1. Yerel terminal penceresinde, örnek Python projesinin kök klasörüne gidin. Daha sonra **iot-hub\Quickstarts\simulated-device-2** klasörüne gidin.

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

    ![Simülasyon cihazını çalıştırma](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>Doğrudan yöntem çağırma

Arka uç uygulaması, IoT Hub’ınızdaki bir hizmet tarafı uç noktasına bağlanır. Uygulama, IoT Hub 'ınız aracılığıyla bir cihaza doğrudan Yöntem çağrıları yapar ve bu bildirimleri dinler. IoT Hub arka uç uygulaması genellikle bulutta çalışır.

1. Başka bir yerel terminal penceresinde, örnek Python projesinin kök klasörüne gidin. Daha sonra **iot-hub\Quickstarts\back-end-application** klasörüne gidin.

1. **BackEndApplication.py** dosyasını, istediğiniz bir metin düzenleyicide açın.

    Değişkenin değerini, `CONNECTION_STRING` daha önce bir değişiklik yaptığınız hizmet bağlantı dizesiyle değiştirin. Sonra **BackEndApplication.py**' ye yaptığınız değişiklikleri kaydedin.

1. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak simülasyon cihazı uygulaması için gerekli kitaplıkları yükleyin:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak arka uç uygulamasını çalıştırın:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Aşağıdaki ekran görüntüsünde, uygulamanın cihaza doğrudan yöntem çağrısı yaptığı ve bir bildirim aldığı için çıkış gösterilmektedir:

    ![Arka uç uygulamasını çalıştırma](./media/quickstart-control-device-python/backend-application.png)

    Arka uç uygulamasını çalıştırdıktan sonra, simülasyon cihazını çalıştıran konsol penceresinde bir ileti ve ileti değişikliklerini gönderdiği hızı görürsünüz:

    ![Sanal istemcide değişiklik](./media/quickstart-control-device-python/simulated-device-2.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir arka uç uygulamasındaki cihazda doğrudan yöntem çağırdınız ve simülasyon cihazı uygulamasında doğrudan yöntem çağrısına yanıt verdiniz.

Cihazdan buluta iletileri, buluttaki farklı hedeflere yönlendirmeyi öğrenmek için sonraki öğreticiyle devam edin.

> [!div class="nextstepaction"]
> [Öğretici: Telemetriyi işlenmek üzere farklı uç noktalara yönlendirme](tutorial-routing.md)

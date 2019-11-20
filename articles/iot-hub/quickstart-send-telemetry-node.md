---
title: "Hızlı başlangıç: Azure IoT 'ye telemetri gönderme (node. js)"
description: Bu hızlı başlangıçta, bir IoT Hub 'ına sanal telemetri göndermek ve bulutta işlenmek üzere IoT Hub 'ından Telemetriyi okumak için iki örnek Node. js uygulaması çalıştırırsınız.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 92d6af41e55429f1b788de68940bc9b033c51ad6
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167025"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderme ve arka uç uygulamasıyla okuma (node. js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub, IoT cihazlarınızdan depolama ya da işleme için buluta kadar yüksek miktarda telemetri almanıza olanak sağlayan bir Azure hizmetidir. Bu hızlı başlangıçta, IoT Hub aracılığıyla sanal bir cihaz uygulamasından, işleme için bir arka uç uygulamasına telemetri gönderirsiniz.

Hızlı başlangıç, bir tane önceden yazılmış iki Node. js uygulaması, bir Telemetriyi ve bir hub 'dan Telemetriyi okuyacak bir tane kullanır. Bu iki uygulamayı çalıştırmadan önce bir IoT Hub oluşturun ve bir cihazı hub 'a kaydedersiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıçta çalıştırdığınız iki örnek uygulama Node. js ' de yazılmıştır. Geliştirme makinenizde Node. js ile v10 arasındaki. x. x veya üzeri gerekir.

[NodeJS.org](https://nodejs.org)adresinden birden çok platform için Node. js ' ye indirebilirsiniz.

Aşağıdaki komutu kullanarak geçerli Node. js sürümünü geliştirme makinenizde doğrulayabilirsiniz:

```cmd/sh
node --version
```

Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özel komutlar ekler.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

@No__t-0 ' dan örnek Node. js projesini indirin ve ZIP arşivini ayıklayın.

## <a name="create-an-iot-hub"></a>IoT Hub 'ı oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Bir cihazı kaydetme

Bir cihazın bağlanabilmesi için IoT Hub 'ınız ile kayıtlı olması gerekir. Bu hızlı başlangıçta, sanal cihazı kaydetmek için Azure Cloud Shell kullanırsınız.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Mynodedevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterildiği gibi **Mynodedevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı bu makalede da kullanmanız ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Cihaz bağlantı dizesini bir yere göz önünde bir şekilde görünür:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı başlangıçta kullanacaksınız.

1. Ayrıca, arka uç uygulamasının IoT Hub 'ınıza bağlanmasını ve iletileri almanızı sağlamak için bir _hizmet bağlantı dizesi_ gerekir. Aşağıdaki komut, IoT Hub 'ınız için hizmet bağlantı dizesini alır:

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --policy-name service --output table
    ```

    Aşağıdaki gibi görünen hizmet bağlantı dizesini bir yere göz önünde yapın:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı başlangıçta kullanacaksınız. Bu hizmet bağlantı dizesi, önceki adımda not ettiğiniz cihaz bağlantı dizesinden farklıdır.

## <a name="send-simulated-telemetry"></a>Sanal telemetri gönder

Sanal cihaz uygulaması, IoT Hub 'ınızdaki cihaza özgü bir uç noktaya bağlanır ve sanal sıcaklık ve nem telemetrisini gönderir.

1. Yerel Terminal pencerenizi açın, örnek Node. js projesinin kök klasörüne gidin. Ardından **iot-hub\Quickstarts\simulated-Device** klasörüne gidin.

1. **SimulatedDevice. js** dosyasını istediğiniz bir metin düzenleyicisinde açın.

    @No__t-0 değişkeninin değerini, daha önce bir değişiklik yaptığınız cihaz bağlantı dizesiyle değiştirin. Ardından **SimulatedDevice. js**' ye yaptığınız değişiklikleri kaydedin.

1. Yerel Terminal penceresinde, gerekli kitaplıkları yüklemek ve sanal cihaz uygulamasını çalıştırmak için aşağıdaki komutları çalıştırın:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    Aşağıdaki ekran görüntüsünde, sanal cihaz uygulamasının IoT Hub 'ınıza telemetri gönderdiği çıkış gösterilmektedir:

    ![Sanal cihazı Çalıştır](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub 'ınızdaki Telemetriyi okuyun

Arka uç uygulaması, IoT Hub hizmet tarafı **olayları** uç noktasına bağlanır. Uygulama, sanal cihazınızdan gönderilen cihazdan buluta iletileri alır. Bir IoT Hub arka uç uygulaması, cihazdan buluta iletileri almak ve işlemek için genellikle bulutta çalışır.

1. Başka bir yerel Terminal penceresi açın, örnek Node. js projesinin kök klasörüne gidin. Ardından **iot-hub\Quickstarts\read-D2C-messages** klasörüne gidin.

1. **Readdevicetocloudmessages. js** dosyasını istediğiniz bir metin düzenleyicisinde açın.

    @No__t-0 değişkeninin değerini, daha önce bir değişiklik yaptığınız hizmet bağlantı dizesiyle değiştirin. Sonra değişikliklerinizi **Readdevicetocloudmessages. js**' ye kaydedin.

1. Yerel Terminal penceresinde, gerekli kitaplıkları yüklemek ve arka uç uygulamasını çalıştırmak için aşağıdaki komutları çalıştırın:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    Aşağıdaki ekran görüntüsünde, arka uç uygulamasının hub 'a sanal cihaz tarafından gönderilen Telemetriyi aldığından, çıkış gösterilmektedir:

    ![Arka uç uygulamasını çalıştırma](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, bir Node. js uygulaması kullanarak hub 'a sanal telemetri gönderdiniz ve basit bir arka uç uygulaması kullanarak hub 'ın Telemetriyi okuyaöğreneceksiniz.

Bir arka uç uygulamasından sanal cihazınızı nasıl denetleyeceğinizi öğrenmek için sonraki hızlı başlangıca geçin.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: IoT Hub 'ına bağlı bir cihazı denetleme](quickstart-control-device-node.md)

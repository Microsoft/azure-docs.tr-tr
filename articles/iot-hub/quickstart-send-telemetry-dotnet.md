---
title: Hızlı başlangıç-Azure IoT Hub hızlı başlangıç 'a telemetri gönderme (C#) | Microsoft Docs
description: Bu hızlı başlangıçta bir IoT hub’a sanal telemetri göndermek ve bulutta işlemek üzere IoT hub’dan gelen telemetriyi okumak için iki örnek C# uygulaması çalıştırırsınız.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: ec26e4a42cdeb7a693c8078c0f065241f32da949
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864001"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderin ve hizmet uygulamasıyla okuyun (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub, IoT cihazlarınızdan buluta depolama veya işleme amacıyla yüksek hacimlerde telemetri almanızı sağlayan bir Azure hizmetidir. Bu hızlı başlangıçta, IoT Hub aracılığıyla bir sanal cihaz uygulamasından, işlenmek üzere bir hizmet uygulamasına telemetri gönderirsiniz.

Hızlı başlangıçta, biri telemetriyi göndermek için, diğeri de hub’dan telemetriyi okumak için olmak üzere önceden yazılmış iki C# uygulaması kullanılır. Bu iki uygulamayı çalıştırmadan önce bir IoT hub oluşturur ve hub’a bir cihaz kaydedersiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bu hızlı başlangıçta çalıştırdığınız iki örnek uygulama, C# kullanılarak yazılır. Geliştirme makinenizde .NET Core SDK 3,1 veya üzeri bir sürümü gerekir.

    [.NET](https://dotnet.microsoft.com/download)’ten birden fazla platform için .NET Core SDK’sını indirebilirsiniz.

    Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli C# sürümünü doğrulayabilirsiniz:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > Bu hızlı başlangıçta Telemetriyi okumak için kullanılan Event Hubs hizmet kodunu derlemek için .NET Core SDK 3,1 veya üzeri önerilir.


* Azure IoT C# örneklerini indirin [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) ve ZIP arşivini ayıklayın.

* Güvenlik duvarınızdaki 8883 numaralı bağlantı noktasını açık olduğundan emin olun. Bu hızlı başlangıçta bulunan cihaz örneği, 8883 bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **MyDotnetDevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterildiği gibi **MyDotnetDevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı bu makalede da kullanmanız ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Aşağıdakine benzeyen cihaz bağlantı dizenizi not alın:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı başlangıçta kullanacaksınız.

3. Ayrıca, hizmet uygulamasının IoT Hub 'ınıza bağlanmasını ve iletileri almanızı sağlamak için IoT Hub 'ınızdaki _Event Hubs uyumlu uç nokta_, _Event Hubs uyumlu yol_ ve _hizmet birincil anahtarı_ gerekir. Aşağıdaki komutlar, IoT hub’ınız için şu değerleri alır:

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Bu üç değeri bir yere, daha sonra hızlı başlangıçta kullanacaksınız.

## <a name="send-simulated-telemetry"></a>Simülasyon telemetrisi gönderme

Simülasyon cihazı uygulaması, IoT hub’ınız üzerindeki cihaza özgü bir uç noktaya bağlanır ve sanal sıcaklık ve nem telemetrisi gönderir.

1. Yerel terminal penceresinde, örnek C# projesinin kök klasörüne gidin. Ardından **iot-hub\Quickstarts\SimulatedDevice** klasörüne gidin.

2. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak simülasyon cihazı uygulaması için gerekli paketleri yükleyin:

    ```cmd/sh
    dotnet restore
    ```

3. Yerel Terminal penceresinde, cihaz bağlantı dizesiyle sanal cihaz uygulamasını derlemek ve çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Aşağıdaki ekran görüntüsünde, simülasyon cihazı uygulaması, IoT hub’ınıza telemetri gönderdiğinde oluşan çıktı gösterilmektedir:

    ![Simülasyon cihazını çalıştırma](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub’ınızdan telemetri okuma

Hizmet uygulaması, IoT Hub hizmet tarafı **olayları** uç noktasına bağlanır. Uygulama, simülasyon cihazınızdan gönderilen cihazdan buluta iletileri alır. Bir IoT Hub hizmet uygulaması, cihazdan buluta iletileri almak ve işlemek için genellikle bulutta çalışır.

1. Başka bir yerel terminal penceresinde, örnek C# projesinin kök klasörüne gidin. Ardından **iot-hub\Quickstarts\ReadD2cMessages** klasörüne gidin.

2. Uygulama için gerekli kitaplıkları yüklemek üzere yerel Terminal penceresinde aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dotnet restore
    ```

3. Parametre seçeneklerini görmek için yerel Terminal penceresinde aşağıdaki komutu çalıştırın.

    ```cmd/sh
    dotnet run
    ```

4. Yerel Terminal penceresinde, uygulamayı derlemek ve çalıştırmak için aşağıdaki komuttan birini çalıştırın:

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    veya

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    Aşağıdaki ekran görüntüsünde, hizmet uygulamasının sanal cihaz tarafından hub 'a gönderdiği Telemetriyi aldığından, çıkış gösterilmektedir:

    ![Hizmet uygulamasını çalıştırma](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, bir C# uygulaması kullanarak hub 'a sanal telemetri gönderdiniz ve basit bir hizmet uygulaması kullanarak hub 'ın Telemetriyi okuyaöğreneceksiniz.

Sanal cihazınızı bir hizmet uygulamasından nasıl denetleyeceğinizi öğrenmek için sonraki hızlı başlangıca geçin.

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: IoT hub’a bağlı bir cihazı denetleme](quickstart-control-device-dotnet.md)

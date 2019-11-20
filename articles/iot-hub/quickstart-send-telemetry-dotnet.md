---
title: Azure IoT Hub hızlı başlangıç 'a telemetri göndermeC#() | Microsoft Docs
description: Bu hızlı başlangıçta, bir IoT Hub 'ına C# sanal telemetri göndermek ve bulutta Işlenmek üzere IoT Hub 'ından Telemetriyi okumak için iki örnek uygulama çalıştırırsınız.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 9f9e84570c7e7a4a2049c9f357d001c3316a4106
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166338"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderme ve arka uç uygulamasıyla okuma (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub, IoT cihazlarınızdan depolama ya da işleme için buluta kadar yüksek miktarda telemetri almanıza olanak sağlayan bir Azure hizmetidir. Bu hızlı başlangıçta, IoT Hub aracılığıyla sanal bir cihaz uygulamasından, işleme için bir arka uç uygulamasına telemetri gönderirsiniz.

Hızlı başlangıç, bir tane önceden yazılmış C# iki uygulama kullanır, bunlardan biri Telemetriyi ve bir hub 'dan Telemetriyi okumak üzere bir tane. Bu iki uygulamayı çalıştırmadan önce bir IoT Hub oluşturun ve bir cihazı hub 'a kaydedersiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıçta çalıştırdığınız iki örnek uygulama kullanılarak C#yazılmıştır. Geliştirme makinenizde .NET Core SDK 2.1.0 veya üzeri bir sürümü gerekir.

.NET Core SDK [.net](https://www.microsoft.com/net/download/all)'ten birden çok platform için indirebilirsiniz.

Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli sürümünü C# doğrulayabilirsiniz:

```cmd/sh
dotnet --version
```

Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özel komutlar ekler.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Örnek C# projeyi https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip ' den INDIRIN ve ZIP arşivini ayıklayın.

## <a name="create-an-iot-hub"></a>IoT Hub 'ı oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Bir cihazı kaydetme

Bir cihazın bağlanabilmesi için IoT Hub 'ınız ile kayıtlı olması gerekir. Bu hızlı başlangıçta, sanal cihazı kaydetmek için Azure Cloud Shell kullanırsınız.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **MyDotnetDevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterildiği gibi **MyDotnetDevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı bu makalede da kullanmanız ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Cihaz bağlantı dizesini bir yere göz önünde bir şekilde görünür:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı başlangıçta kullanacaksınız.

3. Ayrıca, arka uç uygulamasının IoT Hub 'ınıza bağlanmasını ve iletileri almanızı sağlamak için IoT Hub 'ınızdaki _Event Hubs uyumlu uç nokta_, _Event Hubs uyumlu yol_ve _hizmet birincil anahtarı_ gerekir. Aşağıdaki komutlar IoT Hub 'ınız için bu değerleri alır:

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Bu üç değeri bir yere, daha sonra hızlı başlangıçta kullanacaksınız.

## <a name="send-simulated-telemetry"></a>Sanal telemetri gönder

Sanal cihaz uygulaması, IoT Hub 'ınızdaki cihaza özgü bir uç noktaya bağlanır ve sanal sıcaklık ve nem telemetrisini gönderir.

1. Yerel bir Terminal penceresinde, örnek C# projenin kök klasörüne gidin. Ardından **iot-hub\Quickstarts\simulated-Device** klasörüne gidin.

2. **SimulatedDevice.cs** dosyasını istediğiniz bir metin düzenleyicisinde açın.

    @No__t-0 değişkeninin değerini, daha önce bir değişiklik yaptığınız cihaz bağlantı dizesiyle değiştirin. Sonra **SimulatedDevice.cs**' ye yaptığınız değişiklikleri kaydedin.

3. Yerel Terminal penceresinde, sanal cihaz uygulaması için gerekli paketleri yüklemek üzere aşağıdaki komutları çalıştırın:

    ```cmd/sh
    dotnet restore
    ```

4. Yerel Terminal penceresinde, sanal cihaz uygulamasını derlemek ve çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dotnet run
    ```

    Aşağıdaki ekran görüntüsünde, sanal cihaz uygulamasının IoT Hub 'ınıza telemetri gönderdiği çıkış gösterilmektedir:

    ![Sanal cihazı Çalıştır](media/quickstart-send-telemetry-dotnet/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub 'ınızdaki Telemetriyi okuyun

Arka uç uygulaması, IoT Hub hizmet tarafı **olayları** uç noktasına bağlanır. Uygulama, sanal cihazınızdan gönderilen cihazdan buluta iletileri alır. Bir IoT Hub arka uç uygulaması, cihazdan buluta iletileri almak ve işlemek için genellikle bulutta çalışır.

1. Başka bir yerel Terminal penceresinde, örnek C# projenin kök klasörüne gidin. Ardından **iot-hub\Quickstarts\read-D2C-messages** klasörüne gidin.

2. **ReadDeviceToCloudMessages.cs** dosyasını istediğiniz bir metin düzenleyicisinde açın. Aşağıdaki değişkenleri güncelleştirin ve değişiklikleri dosyaya kaydedin.

    | Değişken | Değer |
    | -------- | ----------- |
    | `s_eventHubsCompatibleEndpoint` | Değişkenin değerini, daha önce bir değişiklik yaptığınız Event Hubs uyumlu uç nokta ile değiştirin. |
    | `s_eventHubsCompatiblePath`     | Değişkenin değerini, daha önce bir değişiklik yaptığınız Event Hubs uyumlu yol ile değiştirin. |
    | `s_iotHubSasKey`                | Değişkenin değerini, daha önce bir değişiklik yaptığınız hizmet birincil anahtarıyla değiştirin. |

3. Yerel Terminal penceresinde, arka uç uygulaması için gerekli kitaplıkları yüklemek üzere aşağıdaki komutları çalıştırın:

    ```cmd/sh
    dotnet restore
    ```

4. Yerel Terminal penceresinde, arka uç uygulamasını derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:

    ```cmd/sh
    dotnet run
    ```

    Aşağıdaki ekran görüntüsünde, arka uç uygulamasının hub 'a sanal cihaz tarafından gönderilen Telemetriyi aldığından, çıkış gösterilmektedir:

    ![Arka uç uygulamasını çalıştırma](media/quickstart-send-telemetry-dotnet/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, bir C# uygulamayı kullanarak hub 'a sanal telemetri gönderdiniz ve basit bir arka uç uygulaması kullanarak hub 'ın Telemetriyi okuyaöğreneceksiniz.

Bir arka uç uygulamasından sanal cihazınızı nasıl denetleyeceğinizi öğrenmek için sonraki hızlı başlangıca geçin.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: IoT Hub 'ına bağlı bir cihazı denetleme](quickstart-control-device-dotnet.md)
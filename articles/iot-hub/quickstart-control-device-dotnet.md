---
title: Hızlı başlangıç-bir cihazı Azure IoT Hub hızlı başlangıç (.NET) ile denetleme | Microsoft Docs
description: Bu hızlı başlangıçta iki örnek C# uygulaması çalıştırırsınız. Tek bir uygulama, hub 'ınıza bağlı cihazları uzaktan denetleyebileceği bir hizmet uygulamasıdır. Diğer uygulama, uzaktan denetlenebilen hub’ınıza bağlanan bir cihazın simülasyonunu yapar.
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
ms.date: 03/04/2020
ms.openlocfilehash: 92f27c16b5dd45352a3a6cb65dd4bf78e88188f0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868582"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Hızlı Başlangıç: IoT hub’a bağlı bir cihazı denetleme (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub, IoT cihazlarınızı buluttan yönetmenizi ve depolama ya da işleme için buluta yönelik yüksek hacimli cihaz telemetrisini gerçekleştirmenizi sağlayan bir Azure hizmetidir. Bu hızlı başlangıçta, IoT hub’ınıza bağlı bir simülasyon cihazını denetlemek için *doğrudan yöntem* kullanırsınız. IoT hub’ınıza bağlı bir cihazın davranışını uzaktan değiştirmek için doğrudan yöntemler kullanabilirsiniz.

Hızlı başlangıçta, önceden yazılmış iki .NET uygulaması kullanılır:

* Bir hizmet uygulamasından çağrılan doğrudan yöntemlere yanıt veren bir sanal cihaz uygulaması. Doğrudan yöntem çağrıları almak için bu uygulama, IoT hub’ınızda aygıta özgü bir uç noktaya bağlanır.

* Sanal cihazdaki doğrudan yöntemleri çağıran bir hizmet uygulaması. Bir cihazda doğrudan yöntem çağırmak için bu uygulama, IoT hub’ınızda sunucu tarafı uç noktasına bağlanır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bu hızlı başlangıçta çalıştırdığınız iki örnek uygulama, C# kullanılarak yazılır. Geliştirme makinenizde .NET Core SDK 3,1 veya üzeri bir sürümü gerekir.

    [.NET](https://dotnet.microsoft.com/download)’ten birden fazla platform için .NET Core SDK’sını indirebilirsiniz.

    Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli C# sürümünü doğrulayabilirsiniz:

    ```cmd/sh
    dotnet --version
    ```
* Henüz yapmadıysanız, Azure IoT C# örneklerini adresinden indirin https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip ve ZIP arşivini ayıklayın.

* Güvenlik duvarınızdaki 8883 numaralı bağlantı noktasını açık olduğundan emin olun. Bu hızlı başlangıçta bulunan cihaz örneği, 8883 bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Önceki [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-dotnet.md) öğreticisini tamamladıysanız bu adımı atlayabilirsiniz.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Önceki [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-dotnet.md) öğreticisini tamamladıysanız bu adımı atlayabilirsiniz.

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **MyDotnetDevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterildiği gibi **MyDotnetDevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı da bu makalede kullanmanız gerekir ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirin.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Yeni kaydettiğiniz cihazın _cihaz bağlantı dizesini_ almak için aşağıdaki komutları Azure Cloud Shell'de çalıştırın:

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Aşağıdakine benzeyen cihaz bağlantı dizenizi not alın:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız.

## <a name="retrieve-the-service-connection-string"></a>Hizmet bağlantı dizesini alma

Ayrıca, hizmet uygulamasının hub 'a bağlanmasını ve iletileri almanızı sağlamak için IoT Hub _hizmeti bağlantı dizeniz_ gerekir. Aşağıdaki komut, IoT hub'ınız için hizmeti bağlantı dizesini alır:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Şu ifadeye benzer şekilde görünen hizmet bağlantı dizesini not edin:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Bu değeri hızlı başlangıcın ilerleyen bölümlerinde kullanacaksınız. Bu hizmet bağlantı dizesi, önceki adımda not ettiğiniz cihaz bağlantı dizesinden farklıdır.

## <a name="listen-for-direct-method-calls"></a>Doğrudan yöntem çağrılarını dinleme

Simülasyon cihazı, IoT hub’ınızdaki cihaza özgü bir uç noktaya bağlanır, sanal telemetri gönderir ve hub’ınızdan gelen doğrudan yöntem çağrılarını dinler. Bu hızlı başlangıçta, hub’dan gelen doğrudan yöntem çağrısı, telemetri gönderme aralığını değiştirmesini cihaza bildirir. Sanal cihaz, doğrudan yöntemini yürütmeden sonra hub 'ınıza bir bildirim gönderir.

1. Yerel terminal penceresinde, örnek C# projesinin kök klasörüne gidin. Ardından **iot-hub\Quickstarts\SimulatedDeviceWithCommand** klasörüne gidin.

2. Yerel terminal penceresinde, aşağıdaki komutları çalıştırarak simülasyon cihazı uygulaması için gerekli paketleri yükleyin:

    ```cmd/sh
    dotnet restore
    ```

3. Yerel Terminal penceresinde, `{DeviceConnectionString}` daha önce not ettiğiniz cihaz bağlantı dizesiyle değiştirerek, sanal cihaz uygulamasını derlemek ve çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    Aşağıdaki ekran görüntüsünde, simülasyon cihazı uygulaması, IoT hub’ınıza telemetri gönderdiğinde oluşan çıktı gösterilmektedir:

    ![Simülasyon cihazını çalıştırma](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Doğrudan yöntem çağırma

Hizmet uygulaması, IoT Hub bir hizmet tarafı uç noktasına bağlanır. Uygulama, IoT Hub 'ınız aracılığıyla bir cihaza doğrudan Yöntem çağrıları yapar ve bu bildirimleri dinler. Bir IoT Hub hizmet uygulaması genellikle bulutta çalışır.

1. Başka bir yerel terminal penceresinde, örnek C# projesinin kök klasörüne gidin. Ardından **iot-hub\Quickstarts\InvokeDeviceMethod** klasörüne gidin.

2. Yerel Terminal penceresinde, hizmet uygulaması için gerekli kitaplıkları yüklemek üzere aşağıdaki komutları çalıştırın:

    ```cmd/sh
    dotnet restore
    ```

3. Yerel Terminal penceresinde, `{ServiceConnectionString}` daha önce not ettiğiniz hizmet bağlantı dizesiyle değiştirerek hizmet uygulamasını derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    Aşağıdaki ekran görüntüsünde, uygulamanın cihaza doğrudan yöntem çağrısı yaptığı ve bir bildirim aldığı için çıkış gösterilmektedir:

    ![Hizmet uygulamasını çalıştırma](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Hizmet uygulamasını çalıştırdıktan sonra, sanal cihaz çalıştıran konsol penceresinde bir ileti görürsünüz ve ileti değişiklikleri gönderme hızıdır:

    ![Sanal istemcide değişiklik](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir hizmet uygulamasından bir cihazda doğrudan yöntem çağırılır ve sanal cihaz uygulamasındaki doğrudan yöntem çağrısına yanıt vermiş olursunuz.

Cihazdan buluta iletileri, buluttaki farklı hedeflere yönlendirmeyi öğrenmek için sonraki öğreticiyle devam edin.

> [!div class="nextstepaction"]
> [Öğretici: Telemetriyi işlenmek üzere farklı uç noktalara yönlendirme](tutorial-routing.md)

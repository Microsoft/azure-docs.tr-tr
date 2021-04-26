---
title: Hızlı başlangıç-Azure IoT Hub hızlı başlangıç 'a telemetri gönderme (C) | Microsoft Docs
description: Bu hızlı başlangıçta bir IoT hub’a sanal telemetri göndermek ve bulutta işlemek üzere IoT hub’dan gelen telemetriyi okumak için iki örnek C uygulaması çalıştırırsınız.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: a9478948cd5232c863014631acb884cd637a2f7d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864028"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderme ve arka uç uygulamasıyla okuma (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub, IoT cihazlarınızdan buluta depolama veya işleme amacıyla yüksek hacimlerde telemetri almanızı sağlayan bir Azure hizmetidir. Bu hızlı başlangıçta, bir simülasyon cihazı uygulamasından bir arka uç uygulamasına işlenmek üzere IoT Hub aracılığıyla telemetri gönderirsiniz.

Bu hızlı başlangıçta, IoT hub’ına telemetri verileri göndermek için [C için Azure IoT cihaz SDK](iot-hub-device-sdk-c-intro.md)’sındaki bir C örnek uygulaması kullanılmaktadır. Azure IoT cihaz SDK’ları, taşınabilirlik ve geniş platform uyumluluğu için [ANSI C (C99)](https://wikipedia.org/wiki/C99) ile yazılır. Örnek kodu çalıştırmadan önce bir IoT hub’ı oluşturur ve simülasyon cihazını o hub’a kaydedersiniz.

Bu makale Windows için yazılmıştır, ancak bu hızlı başlangıcı Linux üzerinde de tamamlayabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [' C++ Ile masaüstü geliştirme '](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) iş yükünün etkin olduğu [Visual Studio 2019](https://www.visualstudio.com/vs/) ' i yükler.

* En son [Git](https://git-scm.com/download/) sürümünü yükleyin.

* Güvenlik duvarınızdaki 8883 numaralı bağlantı noktasını açık olduğundan emin olun. Bu hızlı başlangıçta bulunan cihaz örneği, 8883 bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

Bu hızlı başlangıç için, [C Için Azure IoT cihaz SDK 'sını](iot-hub-device-sdk-c-intro.md)kullanacaksınız. 

Aşağıdaki ortamlar için, bu paketleri ve kitaplıkları yükleyerek SDK 'Yı kullanabilirsiniz:

* **Linux**: apt-get PAKETLERI aşağıdaki CPU mimarilerini kullanarak Ubuntu 16,04 ve 18,04 için kullanılabilir: AMD64, arm64, armhf ve i386. Daha fazla bilgi için bkz. [Ubuntu’da C cihaz istemcisi oluşturmak için apt-get kullanma](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: mbed platformda cihaz uygulamaları oluşturan geliştiriciler için Azure IoT Hub ile dakikalar içinde çalışmaya başlamanızı sağlayacak bir kitaplık ve örnek yayımladık. Daha fazla bilgi için bkz. [Mbed kitaplığını kullanma](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Arduino üzerinde geliştirme yapıyorsanız, ARDUINO IDE kitaplık yöneticisinde bulunan Azure IoT kitaplığı 'ndan yararlanabilirsiniz. Daha fazla bilgi için bkz. [Arduino için Azure IoT Hub kitaplığı](https://github.com/azure/azure-iot-arduino).

* **iOS**: IoT Hub Cihaz SDK’sı, Mac ve iOS cihaz geliştirmesi için CocoaPods olarak kullanılabilir. Daha fazla bilgi için bkz. [Microsoft Azure IoT için iOS Örnekleri](https://cocoapods.org/pods/AzureIoTHubClient).

Bununla birlikte, bu hızlı başlangıçta GitHub 'dan [Azure IoT C SDK 'sını](https://github.com/Azure/azure-iot-sdk-c) klonlamak ve derlemek için kullanılan bir geliştirme ortamı hazırlarsınız. GitHub üzerindeki SDK, bu hızlı başlangıçta yer alan örnek kodu içerir.

1. [CMake derleme sistemini](https://cmake.org/download/)indirin.

    `CMake` yüklemesine başlamadan **önce** makinenizde Visual Studio önkoşullarının (Visual Studio ve "C++ ile masaüstü geliştirme" iş yükü) yüklenmiş olması önemlidir. Önkoşullar sağlandıktan ve indirme doğrulandıktan sonra, CMake derleme sistemini yükleyin.

2. SDK 'nın [en son sürümü](https://github.com/Azure/azure-iot-sdk-c/releases/latest) için etiket adını bulun.

3. Komut istemini veya Git Bash kabuğunu açın. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunun en son sürümünü kopyalamak için aşağıdaki komutları çalıştırın. Önceki adımda bulunan etiketini parametre değeri olarak kullanın `-b` :

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

4. Git deposunun kök dizininde bir `cmake` alt dizini oluşturun ve o klasöre gidin. Dizininden aşağıdaki komutları çalıştırın `azure-iot-sdk-c` :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. SDK 'nın geliştirme istemci platformunuza özgü bir sürümünü oluşturmak için aşağıdaki komutu çalıştırın. `cmake` dizininde simülasyon cihazı için bir Visual Studio çözümü de oluşturulur.

    ```cmd
    cmake ..
    ```

    `cmake`C++ derleyicisini bulamazsa, yukarıdaki komutu çalıştırırken derleme hataları alabilirsiniz. Bu durumda bu komutu [Visual Studio komut isteminde](/dotnet/framework/tools/developer-command-prompt-for-vs) çalıştırmayı deneyin. 

    Derleme başarılı olduktan sonra, son birkaç çıkış satırı aşağıdaki çıkışa benzer olacaktır:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu bölümde, sanal bir cihazı kaydetmek için [IoT uzantısıyla](/cli/azure/iot) birlikte Azure Cloud Shell kullanacaksınız.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Mycdevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterildiği gibi **Mycdevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı bu makalede da kullanmanız ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Aşağıdakine benzeyen cihaz bağlantı dizenizi not alın:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı başlangıçta kullanacaksınız.

## <a name="send-simulated-telemetry"></a>Simülasyon telemetrisi gönderme

Simülasyon cihazı uygulaması, IoT hub’ınız üzerindeki cihaza özgü bir uç noktaya bağlanır ve sanal telemetri olarak bir dize gönderir.

1. Bir metin düzenleyicisi kullanarak iothub_convenience_sample.c kaynak dosyasını açın ve telemetri verileri göndermek için örnek kodu gözden geçirin. Dosya, Azure IoT C SDK 'sını kopyaladığınız çalışma dizini altında aşağıdaki konumda bulunur:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. `connectionString` sabitinin bildirimini bulun:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    `connectionString`Sabitin değerini, daha önce bir değişiklik yaptığınız cihaz bağlantı dizesiyle değiştirin. Ardından **iothub_convenience_sample.c** üzerindeki değişikliklerinizi kaydedin.

3. Yerel terminal penceresinde, Azure IoT C SDK’sında oluşturduğunuz CMake dizininde yer alan *iothub_convenience_sample* proje dizinine gidin. Çalışma dizininizden aşağıdaki komutu girin:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. CMake öğesini yerel terminal pencerenizde çalıştırarak örneği güncelleştirilmiş `connectionString` değeriyle derleyin:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Yerel Terminal pencerenizde, sanal cihaz uygulamasını çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Aşağıdaki ekran görüntüsünde, simülasyon cihazı uygulaması, IoT hub’ınıza telemetri verilerini gönderdiğinde oluşan çıktı gösterilmektedir:

    ![Simülasyon cihazını çalıştırma](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub’ınızdan telemetri okuma

Bu bölümde, sanal cihaz tarafından gönderilen cihaz iletilerini izlemek için [IoT uzantısıyla](/cli/azure/iot) birlikte Azure Cloud Shell kullanacaksınız.

1. Azure Cloud Shell'i kullanarak, IoT hub’ınızdan gelen iletilere bağlanmak ve bu iletileri okumak için aşağıdaki komutu çalıştırın:

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Azure CLI kullanarak cihaz iletilerini okuma](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, bir C uygulaması kullanarak hub 'a sanal telemetri gönderdiniz ve Azure Cloud Shell kullanarak hub 'ın Telemetriyi okuyaöğreneceksiniz.

Azure IoT Hub C SDK’sı ile geliştirme hakkında daha fazla bilgi edinmek için aşağıdaki Nasıl yapılır kılavuzuyla devam edin:

> [!div class="nextstepaction"]
> [Azure IoT Hub C SDK’sı kullanarak geliştirme](iot-hub-devguide-develop-for-constrained-devices.md)

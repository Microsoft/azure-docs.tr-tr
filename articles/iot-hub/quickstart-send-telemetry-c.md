---
title: Azure IoT Hub hızlı başlangıç 'a telemetri gönderme (C) | Microsoft Docs
description: Bu hızlı başlangıçta, bir IoT Hub 'ına sanal telemetri göndermek ve bulutta işlenmek üzere IoT Hub 'ından Telemetriyi okumak için iki örnek C uygulaması çalıştırırsınız.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 4ccfa45c56a7e59024ce0639f218861054e32395
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166952"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderme ve arka uç uygulamasıyla okuma (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub, IoT cihazlarınızdan depolama ya da işleme için buluta kadar yüksek miktarda telemetri almanıza olanak sağlayan bir Azure hizmetidir. Bu hızlı başlangıçta, IoT Hub aracılığıyla sanal bir cihaz uygulamasından, işleme için bir arka uç uygulamasına telemetri gönderirsiniz.

Hızlı başlangıç, bir IoT Hub 'ına telemetri göndermek için [c Için Azure IoT cihaz SDK 'Sının](iot-hub-device-sdk-c-intro.md) c örnek uygulamasını kullanır. Azure IoT cihaz SDK 'Ları, taşınabilirlik ve geniş platform uyumluluğu için [ANSI C (C99)](https://wikipedia.org/wiki/C99) dilinde yazılmıştır. Örnek kodu çalıştırmadan önce bir IoT Hub 'ı oluşturacak ve sanal cihazı bu hub 'a kaydedecaksınız.

Bu makale Windows için yazılmıştır, ancak bu hızlı başlangıcı Linux üzerinde de tamamlayabilirsiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* [Visual Studio 2019](https://www.visualstudio.com/vs/) [' i ' masaüstü geliştirme C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) iş yükü etkinleştirilmiş olarak yükler.
* [Git](https://git-scm.com/download/)'in en son sürümünü yükler.
* Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özel komutlar ekler.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

Bu hızlı başlangıç için, [C Için Azure IoT cihaz SDK 'sını](iot-hub-device-sdk-c-intro.md)kullanacaksınız. 

Aşağıdaki ortamlar için, bu paketleri ve kitaplıkları yükleyerek SDK 'Yı kullanabilirsiniz:

* **Linux**: apt-get PAKETLERI aşağıdaki CPU mimarilerini kullanarak Ubuntu 16,04 ve 18,04 için kullanılabilir: AMD64, arm64, armhf ve i386. Daha fazla bilgi için bkz. [Ubuntu 'Da C cihaz istemci projesi oluşturmak için apt-get kullanma](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: mbed platformda cihaz uygulamaları oluşturan geliştiriciler için Azure IoT Hub ile dakikalar içinde çalışmaya başlamanızı sağlayacak bir kitaplık ve örnek yayımladık. Daha fazla bilgi için bkz. [mbed Kitaplığı kullanma](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Arduino üzerinde geliştirme yapıyorsanız, ARDUINO IDE kitaplık yöneticisinde bulunan Azure IoT kitaplığı 'ndan yararlanabilirsiniz. Daha fazla bilgi için bkz. [Arduino Için Azure IoT Hub kitaplığı](https://github.com/azure/azure-iot-arduino).

* **iOS**: IoT Hub cihaz SDK 'Sı, Mac ve iOS cihaz geliştirmesi için Cocoapods olarak sunulmaktadır. Daha fazla bilgi için bkz. [Microsoft Azure IoT Için IOS örnekleri](https://cocoapods.org/pods/AzureIoTHubClient).

Bununla birlikte, bu hızlı başlangıçta GitHub 'dan [Azure IoT C SDK 'sını](https://github.com/Azure/azure-iot-sdk-c) klonlamak ve derlemek için kullanılan bir geliştirme ortamı hazırlarsınız. GitHub 'daki SDK, bu hızlı başlangıçta kullanılan örnek kodu içerir.

1. [CMake derleme sistemini](https://cmake.org/download/)indirin.

    @No__t-2 yüklemesine **başlamadan önce** Visual Studio önkoşullarının (Visual Studio ve ' iş yükünün C++bulunduğu masaüstü geliştirme) makinenizde yüklü olması önemlidir. Önkoşullar olduktan sonra, indirme doğrulandıktan sonra CMake derleme sistemini yükleyin.

2. Bir komut istemi veya git bash kabuğu açın ve Azure IoT C SDK 'sını kopyalamak istediğiniz çalışma dizinine gidin. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu kopyalamak için aşağıdaki komutu yürütün:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Bu işlemin tamamlanması birkaç dakika sürer.

3. Git deposunun kök dizininde bir `cmake` alt dizini oluşturun ve bu klasöre gidin. Çalışma dizininizden aşağıdaki komutları girin:

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. SDK 'nın geliştirme istemci platformunuza özgü bir sürümünü oluşturmak için aşağıdaki komutu çalıştırın. @No__t-0 dizininde sanal cihaz için bir Visual Studio çözümü oluşturulacaktır.

    ```cmd
    cmake ..
    ```

    @No__t-0 C++ derleyicisini bulamazsa yukarıdaki komutu çalıştırırken derleme hataları alabilirsiniz. Bu durumda, [Visual Studio komut isteminde](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)bu komutu çalıştırmayı deneyin. 

    Derleme başarılı olduktan sonra, son birkaç çıktı satırı aşağıdaki çıktıya benzer olacaktır:

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

## <a name="create-an-iot-hub"></a>IoT Hub 'ı oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Bir cihazı kaydetme

Bir cihazın bağlanabilmesi için IoT Hub 'ınız ile kayıtlı olması gerekir. Bu bölümde, sanal bir cihazı kaydetmek için [IoT uzantısıyla](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) birlikte Azure Cloud Shell kullanacaksınız.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Mycdevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Gösterildiği gibi **Mycdevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı bu makalede da kullanmanız ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirmeniz gerekir.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Yeni kaydettiğiniz cihazın _Cihaz bağlantı dizesini_ almak için Azure Cloud Shell ' de aşağıdaki komutu çalıştırın:

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Cihaz bağlantı dizesini bir yere göz önünde bir şekilde görünür:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı başlangıçta kullanacaksınız.

## <a name="send-simulated-telemetry"></a>Sanal telemetri gönder

Sanal cihaz uygulaması, IoT Hub 'ınızdaki cihaza özgü bir uç noktaya bağlanır ve sanal telemetri olarak bir dize gönderir.

1. Bir metin düzenleyicisi kullanarak iothub_convenience_sample. c kaynak dosyasını açın ve telemetri göndermek için örnek kodu gözden geçirin. Dosya, Azure IoT C SDK 'sını kopyaladığınız çalışma dizini altında aşağıdaki konumda bulunur:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. @No__t-0 sabitinin bildirimini bulun:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    @No__t-0 sabitinin değerini, daha önce bir değişiklik yaptığınız cihaz bağlantı dizesiyle değiştirin. Ardından **iothub_convenience_sample. c**' ye yaptığınız değişiklikleri kaydedin.

3. Yerel bir Terminal penceresinde, Azure IoT C SDK 'sında oluşturduğunuz CMake dizinindeki *iothub_convenience_sample* proje dizinine gidin. Çalışma dizininizden aşağıdaki komutu girin:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Güncelleştirilmiş `connectionString` değeri ile örneği oluşturmak için yerel Terminal pencerenizde CMake 'i çalıştırın:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Yerel Terminal pencerenizde, sanal cihaz uygulamasını çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Aşağıdaki ekran görüntüsünde, sanal cihaz uygulamasının IoT Hub 'ına telemetri gönderdiği çıkış gösterilmektedir:

    ![Sanal cihazı Çalıştır](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub 'ınızdaki Telemetriyi okuyun

Bu bölümde, sanal cihaz tarafından gönderilen cihaz iletilerini izlemek için [IoT uzantısıyla](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) birlikte Azure Cloud Shell kullanacaksınız.

1. Azure Cloud Shell kullanarak, IoT Hub 'ınızdaki iletileri bağlamak ve okumak için aşağıdaki komutu çalıştırın:

   **Youriothubname**: Bu yer tutucuyu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Azure CLı kullanarak cihaz iletilerini okuma](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, bir C uygulaması kullanarak hub 'a sanal telemetri gönderdiniz ve Azure Cloud Shell kullanarak hub 'ın Telemetriyi okuyaöğreneceksiniz.

Azure IoT Hub C SDK ile geliştirme hakkında daha fazla bilgi edinmek için aşağıdaki nasıl yapılır kılavuzuna ilerleyin:

> [!div class="nextstepaction"]
> [Azure IoT Hub C SDK kullanarak geliştirme](iot-hub-devguide-develop-for-constrained-devices.md)
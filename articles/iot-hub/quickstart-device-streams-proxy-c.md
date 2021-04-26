---
title: Hızlı başlangıç-SSH ve RDP için Azure IoT Hub cihaz akışları C hızlı başlangıcı
description: Bu hızlı başlangıçta, IoT Hub cihaz akışları üzerinde SSH ve RDP senaryolarını etkinleştirmek için proxy görevi gören örnek bir C uygulaması çalıştırırsınız.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 8a74b5e463045e92e703ef6932f92b84d4709f36
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864151"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Hızlı başlangıç: C proxy uygulaması (Önizleme) kullanarak IoT Hub cihaz akışı üzerinden SSH ve RDP 'yi etkinleştirme

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub Şu anda cihaz akışlarını [Önizleme özelliği](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)olarak desteklemektedir.

[Cihaz akışları IoT Hub](./iot-hub-device-streams-overview.md) hizmet ve cihaz uygulamalarının güvenli ve güvenlik duvarı kolay bir şekilde iletişim kurmasına olanak tanır. Kuruluma genel bakış için bkz. [yerel proxy örnek sayfası](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Bu hızlı başlangıçta, cihaz akışları aracılığıyla tünel Secure Shell (SSH) trafiğinin (22 numaralı bağlantı noktasını kullanarak) kurulumu açıklanmaktadır. Uzak Masaüstü Protokolü (RDP) trafiği kurulumu benzerdir ve basit bir yapılandırma değişikliği gerektirir. Cihaz akışları uygulama ve protokol belirsiz olduğundan, bu hızlı başlangıcı diğer uygulama trafiği türlerine uyacak şekilde değiştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Cihaz akışlarının önizlemesi Şu anda yalnızca şu bölgelerde oluşturulan IoT Hub 'lar için desteklenmektedir:

  * Central US
  * EUAP Orta ABD
  * Kuzey Avrupa
  * Güneydoğu Asya

* C++ iş yükü etkinken [Masaüstü geliştirmeyle](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) [Visual Studio 2019](https://www.visualstudio.com/vs/) ' i yükler.
* En son [Git](https://git-scm.com/download/) sürümünü yükleyin.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Nasıl çalışır?

Aşağıdaki şekilde, cihaz ve hizmet yerel proxy programlarının SSH istemcisi ile SSH Daemon işlemleriyle uçtan uca bağlantıyı nasıl etkinleştireceğinizi gösterilmektedir. Genel Önizleme sırasında, C SDK 'Sı cihaz akışlarını yalnızca cihaz tarafında destekler. Sonuç olarak, bu hızlı başlangıçta yalnızca cihaz yerel proxy uygulamasını çalıştırmaya yönelik yönergeler ele alınmaktadır. Eşlik eden hizmet tarafı uygulamasını derlemek ve çalıştırmak için aşağıdaki hızlı başlangıçlardan birindeki yönergeleri izleyin:

* [C# proxy kullanarak IoT Hub cihaz akışları üzerinden SSH/RDP](./quickstart-device-streams-proxy-csharp.md)
* [NodeJS proxy kullanan IoT Hub cihaz akışları üzerinden SSH/RDP](./quickstart-device-streams-proxy-nodejs.md).

![Yerel ara sunucu kurulumu](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. Hizmet yerel proxy, IoT Hub 'ına bağlanır ve bir cihaz akışını hedef cihaza başlatır.

2. Cihaz yerel proxy, akış başlatma el sıkışması işlemini tamamlar ve IoT Hub 'ın akış uç noktası aracılığıyla hizmet tarafına uçtan uca bir akış tüneli oluşturur.

3. Cihaz yerel proxy, cihazda 22 numaralı bağlantı noktasında dinleme yapan SSH Daemon 'a bağlanır. Bu ayar, "cihaz-yerel proxy uygulaması çalıştırma" bölümünde açıklandığı gibi yapılandırılabilir.

4. Hizmet yerel proxy, bir kullanıcıdan belirli bir bağlantı noktasını dinleyerek, bu durumda 2222 numaralı bağlantı noktası olan yeni SSH bağlantıları bekler. Bu ayar, "cihaz-yerel proxy uygulaması çalıştırma" bölümünde açıklandığı gibi yapılandırılabilir. Kullanıcı SSH istemcisi aracılığıyla bağlanıyorsa, tünel SSH uygulaması trafiğinin SSH istemcisi ile sunucu programları arasında aktarılmasını sağlar.

> [!NOTE]
> Bir cihaz akışı üzerinden gönderilen SSH trafiği, doğrudan hizmet ve cihaz arasında gönderilmek yerine IoT Hub 'ının akış uç noktası aracılığıyla tünel oluşturulur. Daha fazla bilgi için bkz. [IoT Hub cihaz akışlarını kullanmanın avantajları](iot-hub-device-streams-overview.md#benefits). Ayrıca, şekil, cihaz yerel ara sunucusu ile aynı cihazda (veya makinede) çalışan SSH cinini gösterir. Bu hızlı başlangıçta, SSH Daemon IP adresini sağlamak, cihazın yerel ara sunucusunun ve arka plan programının farklı makinelerde da çalışmasına izin verir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

Bu hızlı başlangıç için, [C Için Azure IoT cihaz SDK 'sını](iot-hub-device-sdk-c-intro.md)kullanırsınız. GitHub 'dan [Azure IoT C SDK 'sını](https://github.com/Azure/azure-iot-sdk-c) klonlamak ve derlemek için kullanılan bir geliştirme ortamı hazırlarsınız. GitHub 'daki SDK, bu hızlı başlangıçta kullanılan örnek kodu içerir.

1. [CMake derleme sistemini](https://cmake.org/download/)indirin.

    CMake yüklemesine *başlamadan önce* Visual Studio önkoşullarının (Visual Studio ve *C++ Iş yükü ile masaüstü geliştirme* ) makinenizde yüklü olması önemlidir. Önkoşullar olduktan sonra, indirme doğrulandıktan sonra CMake yapı sistemini yükleyebilirsiniz.

1. Komut istemini veya Git Bash kabuğunu açın. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu kopyalamak için aşağıdaki komutları çalıştırın:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Bu işlem birkaç dakika sürer.

1. Git deposunun kök dizininde bir *CMake* alt dizini oluşturun ve bu klasöre gidin. *Azure-IoT-SDK-c* dizininden aşağıdaki komutları çalıştırın:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Geliştirme istemci platformunuza özgü bir SDK sürümü oluşturmak için *CMake* dizininden aşağıdaki komutları çalıştırın.

   * Linux 'ta:

      ```bash
      cmake ..
      make -j
      ```

   * Windows 'da, Visual Studio 2015 veya 2017 için Geliştirici Komut İstemi ' de aşağıdaki komutları çalıştırın. Sanal cihaz için bir Visual Studio çözümü, *CMake* dizininde oluşturulacaktır.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu bölümde, sanal bir cihazı kaydetmek için [IoT uzantısıyla](/cli/azure/iot) birlikte Azure Cloud Shell kullanırsınız.

1. Cihaz kimliğini oluşturmak için Cloud Shell ' de aşağıdaki komutu çalıştırın:

   > [!NOTE]
   > * *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.
   > * Kaydolduğunuz aygıtın adı için *mydevice* ' ın gösterildiği gibi kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz bu adı bu makale boyunca kullanın ve uygulamayı çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirin.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Yeni kaydettiğiniz cihazın *Cihaz bağlantı dizesini* almak için Cloud Shell ' de aşağıdaki komutları çalıştırın:

   > [!NOTE]
   > *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Bu hızlı başlangıçta kullanılmak üzere döndürülen cihaz bağlantı dizesini aklınızda edin. Aşağıdaki örneğe benzer şekilde görünür:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>Cihaz akışları aracılığıyla bir cihaza SSH

Bu bölümde, SSH trafiğini tünele bir uçtan uca akış kurarsınız.

### <a name="run-the-device-local-proxy-application"></a>Cihaz yerel proxy uygulamasını çalıştırma

1. Klasöründeki **iothub_client_c2d_streaming_proxy_sample. c** kaynak dosyasını düzenleyin `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` ve cihaz Bağlantı dizenizi, hedef cihazın IP/ana BILGISAYAR adını ve SSH bağlantı noktasını 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Örneği derle:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Derlenmiş programı cihazda çalıştırın:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Hizmet yerel proxy uygulamasını çalıştırma

"Nasıl çalıştığı" bölümünde açıklandığı gibi, SSH trafiğini tünele gösteren bir uçtan uca akış oluşturmak için her uçta bir yerel ara sunucu gerekir (hem hizmette hem de cihaz tarafında). Genel Önizleme sırasında, IoT Hub C SDK 'Sı yalnızca cihaz tarafında cihaz akışlarını destekler. Hizmet yerel ara sunucusunu derlemek ve çalıştırmak için aşağıdaki hızlı başlangıçlardan birindeki yönergeleri izleyin:

   * [C# proxy uygulamaları kullanarak IoT Hub cihaz akışları üzerinden SSH/RDP](./quickstart-device-streams-proxy-csharp.md)
   * [Node.js proxy uygulamaları kullanarak IoT Hub cihaz akışları üzerinden SSH/RDP](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>SSH oturumu oluşturma

Hem cihaz hem de hizmet yerel proxy 'leri çalıştıktan sonra SSH istemci programınızı kullanın ve bağlantı noktası 2222 üzerindeki hizmet yerel proxy 'sine bağlanın (doğrudan SSH Daemon yerine).

```cmd/sh
ssh {username}@localhost -p 2222
```

Bu noktada, SSH oturum açma penceresi, kimlik bilgilerinizi girmenizi ister.

Aşağıdaki görüntüde, şu adreste SSH Daemon 'a bağlanan cihaz yerel ara sunucusunda konsol çıktısı gösterilmektedir `IP_address:22` :

![Cihaz-yerel proxy çıkışı](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Aşağıdaki görüntüde SSH istemci programının konsol çıktısı gösterilmektedir. SSH istemcisi, hizmet yerel proxy 'nin dinlediği bağlantı noktası 22 ' ye bağlanarak SSH daemon ile iletişim kurar:

![SSH istemci çıktısı](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, IoT Hub aracılığıyla bir cihaz akışı oluşturmak için bir IoT Hub 'ı ve bir cihaz kaydettiniz, bir cihaz ve hizmet yerel proxy programı dağıttı ve SSH trafiğini tünele dağıtmak için proxy 'leri kullandınız.

Cihaz akışları hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Cihaz akışlarına genel bakış](./iot-hub-device-streams-overview.md)

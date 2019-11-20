---
title: Azure IoT Hub cihaz akışları ile C 'de cihaz uygulamasıyla iletişim kurma
description: Bu hızlı başlangıçta, bir cihaz akışı aracılığıyla IoT cihazıyla iletişim kuran bir C cihaz tarafı uygulaması çalıştırırsınız.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: d53b36beeca2c5e0456965cf5af19e5324b31ad9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084206"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Hızlı başlangıç: IoT Hub cihaz akışları aracılığıyla C 'deki bir cihaz uygulamasıyla Iletişim kurma (Önizleme)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub Şu anda cihaz akışlarını [Önizleme özelliği](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)olarak desteklemektedir.

[Cihaz akışları IoT Hub](iot-hub-device-streams-overview.md) hizmet ve cihaz uygulamalarının güvenli ve güvenlik duvarı kolay bir şekilde iletişim kurmasına olanak tanır. Genel Önizleme sırasında, C SDK 'Sı cihaz akışlarını yalnızca cihaz tarafında destekler. Sonuç olarak, bu hızlı başlangıçta yalnızca cihaz tarafı uygulamasını çalıştırmaya yönelik yönergeler ele alınmaktadır. Karşılık gelen bir hizmet tarafı uygulamasını çalıştırmak için şu makalelere bakın:

* [IoT Hub cihaz akışları C# aracılığıyla cihaz uygulamalarıyla iletişim kurma](./quickstart-device-streams-echo-csharp.md)

* [IoT Hub cihaz akışları aracılığıyla Node. js ' de cihaz uygulamalarıyla iletişim kurma](./quickstart-device-streams-echo-nodejs.md)

Bu hızlı başlangıçta cihaz tarafı C uygulaması aşağıdaki işlevlere sahiptir:

* IoT cihazında bir cihaz akışı oluşturun.

* Hizmet tarafı uygulamasından gönderilen verileri alır ve geri yankılanır.

Kod, bir cihaz akışının başlatma sürecini ve veri göndermek ve almak için nasıl kullanılacağını gösterir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulların olması gerekir:

* [Visual Studio 2019](https://www.visualstudio.com/vs/) ' i, iş yükünün etkin  **C++ olduğu masaüstü geliştirmeyle** birlikte yükler.

* En son [Git](https://git-scm.com/download/) sürümünü yükleyin.

* Azure CLı için Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IOT uzantısı, Azure CLı için IoT Hub, IoT Edge ve IoT cihazı sağlama hizmeti 'ne (DPS) özgü komutlar ekler.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

Cihaz akışlarının önizlemesi Şu anda yalnızca şu bölgelerde oluşturulan IoT Hub 'lar için desteklenmektedir:

  * Orta ABD
  * EUAP Orta ABD
  * Kuzey Avrupa
  * Güneydoğu Asya

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama

Bu hızlı başlangıç için, [C Için Azure IoT cihaz SDK 'sını](iot-hub-device-sdk-c-intro.md)kullanırsınız. GitHub 'dan [Azure IoT C SDK 'sını](https://github.com/Azure/azure-iot-sdk-c) klonlamak ve derlemek için kullanılan bir geliştirme ortamı hazırlarsınız. GitHub 'daki SDK, bu hızlı başlangıçta kullanılan örnek kodu içerir.

   > [!NOTE]
   > Bu yordama başlamadan önce, Visual Studio 'nun iş yükü  **C++ ile masaüstü geliştirmeyle** birlikte yüklü olduğundan emin olun.

1. Yükleme sayfasında açıklandığı gibi [CMake derleme sistemini](https://cmake.org/download/) yükleyin.

1. Komut istemini veya Git Bash kabuğunu açın. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu kopyalamak için aşağıdaki komutu çalıştırın:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Bu işlem birkaç dakika sürer.

1. Git deposunun kök dizininde aşağıdaki komutta gösterildiği gibi bir *CMake* dizini oluşturun ve ardından bu klasöre gidin.

    ```cmd
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Geliştirme istemci platformunuza özgü bir SDK sürümü oluşturmak için *CMake* dizininden aşağıdaki komutları çalıştırın.

   * Linux 'ta:

      ```bash
      cmake ..
      make -j
      ```

   * Windows 'ta, [Visual Studio için bir geliştirici komut istemi](/dotnet/framework/tools/developer-command-prompt-for-vs)açın. Visual Studio sürümünüz için komutunu çalıştırın. Bu hızlı başlangıçta Visual Studio 2019 kullanılır. Bu komutlar, *CMake* dizinindeki sanal cihaz Için bir Visual Studio çözümü oluşturur.

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

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bağlanabilmesi için IoT Hub 'ınız ile bir cihaz kaydetmeniz gerekir. Bu bölümde, sanal bir cihazı kaydetmek için [IoT uzantısıyla](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) birlikte Azure Cloud Shell kullanırsınız.

1. Cihaz kimliğini oluşturmak için Cloud Shell ' de aşağıdaki komutu çalıştırın:

   > [!NOTE]
   > * *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.
   > * Kaydolduğunuz aygıtın adı için *mydevice* ' ın gösterildiği gibi kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz bu adı bu makale boyunca kullanın ve uygulamayı çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirin.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Yeni kaydettiğiniz cihazın *Cihaz bağlantı dizesini* almak için Cloud Shell ' de aşağıdaki komutu çalıştırın:

   > [!NOTE]
   > *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Bu hızlı başlangıçta kullanılmak üzere döndürülen cihaz bağlantı dizesini aklınızda edin. Aşağıdaki örneğe benzer şekilde görünür:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Cihaz akışları aracılığıyla cihaz ve hizmet arasında iletişim kurma

Bu bölümde hem cihaz tarafı uygulamasını hem de hizmet tarafı uygulamasını çalıştırırsınız ve iki arasında iletişim kurabilirsiniz.

### <a name="run-the-device-side-application"></a>Cihaz tarafı uygulamayı çalıştırma

Cihaz tarafı uygulamasını çalıştırmak için aşağıdaki adımları izleyin:

1. `iothub_client/samples/iothub_client_c2d_streaming_sample` klasöründeki **iothub_client_c2d_streaming_sample. c** kaynak dosyasını düzenleyerek ve cihaz Bağlantı dizenizi ekleyerek cihaz kimlik bilgilerinizi sağlayın.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Aşağıdaki komutlarla kodu derleyin:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Derlenen programı çalıştırın:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Hizmet tarafı uygulamasını çalıştırma

Daha önce belirtildiği gibi, IoT Hub C SDK 'Sı yalnızca cihaz tarafında cihaz akışlarını destekler. Eşlik eden hizmet tarafı uygulamasını derlemek ve çalıştırmak için aşağıdaki hızlı başlangıçlardan birindeki yönergeleri izleyin:

* [IoT Hub cihaz akışları C# aracılığıyla bir cihaz uygulamasıyla iletişim kurma](./quickstart-device-streams-echo-csharp.md)

* [IoT Hub cihaz akışları aracılığıyla Node. js ' de bir cihaz uygulamasıyla iletişim kurma](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz, cihazdaki bir C uygulaması ve hizmet tarafındaki başka bir uygulama arasında bir cihaz akışı kurdu ve bu akışı, verileri uygulamalar arasında ileri ve geri göndermek için kullandınız.

Cihaz akışları hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Cihaz akışlarına genel bakış](./iot-hub-device-streams-overview.md)

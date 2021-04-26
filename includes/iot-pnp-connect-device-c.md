---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: f626d6058374d52b5a1fd07bfdb229cfb715ca45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104611795"
---
Bu hızlı başlangıçta örnek bir IoT Tak ve Kullan cihaz uygulaması oluşturma, IoT Hub 'ınıza bağlama ve Azure IoT Explorer aracını kullanarak gönderdiği Telemetriyi görüntüleme gösterilmektedir. Örnek uygulama C dilinde yazılır ve C için Azure IoT cihaz SDK 'sına dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu hızlı başlangıcı Linux veya Windows üzerinde çalıştırabilirsiniz. Bu hızlı başlangıçta kabuk komutları ' ' yol ayırıcıları için Linux kuralına uyar `/` . Windows üzerinde takip ediyorsanız, bu ayırıcıları ' ' için değiştirmeyi unutmayın `\` .

Önkoşullar işletim sistemine göre farklılık gösterir:

### <a name="linux"></a>Linux

Bu hızlı başlangıç, Ubuntu Linux kullandığınızı varsayar. Bu hızlı başlangıçtaki adımlar Ubuntu 18,04 kullanılarak test edilmiştir.

Bu hızlı başlangıcı Linux üzerinde gerçekleştirmek için, yerel Linux ortamınıza aşağıdaki yazılımı yüklersiniz:

Komutunu kullanarak **GCC**, **Git**, **CMake** ve tüm gerekli bağımlılıkları yükler `apt-get` :

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Öğesinin sürümünün `cmake` **2.8.12** üzerinde olduğunu ve **GCC** 'nin sürümünün **4.4.7**'in üzerinde olduğunu doğrulayın.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Bu hızlı başlangıcı Windows üzerinde gerçekleştirmek için, yerel Windows ortamınıza aşağıdaki yazılımı yüklersiniz:

* [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/) -Visual Studio 'Yu [yüklerken](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) C++ Iş yüküyle **Masaüstü geliştirmeyi** eklediğinizden emin olun.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>Kodu indirme

Bu hızlı başlangıçta, Azure IoT Hub cihaz C SDK 'sını klonlamak ve derlemek için kullanabileceğiniz bir geliştirme ortamı hazırlarsınız.

Seçtiğiniz dizinde bir komut istemi açın. [Azure IoT C SDK 'ları ve kitaplıkları](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu bu konuma kopyalamak için aşağıdaki komutu yürütün:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Bu işlemin tamamlanması birkaç dakika sürer.

## <a name="build-the-code"></a>Kodu oluşturma

Dahil edilen örnek kodu oluşturmak için cihaz SDK 'sını kullanın:

1. Cihaz SDK 'sının kök klasöründe bir _CMake_ alt dizini oluşturun ve bu klasöre gidin:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. SDK 'Yı ve örnekleri derlemek için aşağıdaki komutları çalıştırın:

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> Windows 'da, `cmake` Visual Studio 2019 ' de komut tarafından oluşturulan çözümü açabilirsiniz. _CMake_ dizinindeki *azure_iot_sdks. sln* proje dosyasını açın ve **pnp_simple_thermostat** projesini çözümdeki başlangıç projesi olarak ayarlayın. Artık Visual Studio 'da örneği oluşturabilir ve hata ayıklama modunda çalıştırabilirsiniz.

## <a name="run-the-device-sample"></a>Cihaz örneğini çalıştırma

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Örnek yapılandırma hakkında daha fazla bilgi edinmek için bkz. [örnek Readme](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

IoT Hub 'ınıza telemetri gönderen bir IoT Tak ve Kullan cihazının benzetimini yapan SDK 'da örnek uygulamayı çalıştırmak için:

_CMake_ klasöründen, yürütülebilir dosyayı içeren klasöre gidin ve çalıştırın:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> Windows üzerinde Visual Studio 'da kod yürütmeyi izlemek için, `main` _pnp_simple_thermostat. c_ dosyasındaki işleve bir kesme noktası ekleyin.

Cihaz artık komutları ve özellik güncelleştirmelerini almaya hazır ve hub 'a telemetri verileri göndermeye başladı. Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT Gezginini Kullanma

Cihaz istemcisi örneği başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu örnek, basit bir IoT Tak ve Kullan termostat cihazı uygular. Bu örneğin uyguladığı model IoT Tak ve Kullan [bileşenleri](../articles/iot-pnp/concepts-modeling-guide.md)kullanmaz. [Termostat cihazının Dtdl model dosyası](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) , cihazın uyguladığı telemetri, özellik ve komutları tanımlar.

Cihaz kodu, IoT Hub 'ınıza bağlanmak için standart işlevi kullanır:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Cihaz, bağlantı isteğinde uyguladığı DTDL modelinin model KIMLIĞINI gönderir. Model KIMLIĞI gönderen bir cihaz IoT Tak ve Kullan cihazdır:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

Özellikleri güncelleştiren, komutları işleyen ve telemetri gönderen kod, IoT Tak ve Kullan kurallarını kullanmayan bir cihazın koduyla aynıdır.

Kod, IoT Hub 'ınızdan gönderilen yüklerden JSON nesnelerini ayrıştırmak için Parson kitaplığını kullanır:

```c
// JSON parser
#include "parson.h"
```

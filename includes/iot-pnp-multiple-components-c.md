---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 5a8d270ffdef1f9ae68814fa023284c68216d3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612147"
---
Bu öğreticide, bileşenleriyle bir örnek IoT Tak ve Kullan cihaz uygulaması oluşturma, bunları IoT Hub 'ınıza bağlama ve Azure IoT Gezgini aracını kullanarak hub 'a gönderdiği bilgileri görüntüleme hakkında bilgi verilmektedir. Örnek uygulama C dilinde yazılır ve C için Azure IoT cihaz SDK 'sına dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Örnek kodu indirin.
> * Örnek kodu oluşturun.
> * Örnek cihaz uygulamasını çalıştırın ve IoT Hub 'ınıza bağlandığını doğrulayın.
> * Kaynak kodunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu öğreticiyi Linux veya Windows üzerinde tamamlayabilirsiniz. Bu öğreticideki kabuk komutları ' ' yol ayırıcıları için Linux kuralını izler `/` . Windows üzerinde takip ediyorsanız, bu ayırıcıları ' ' için değiştirmeyi unutmayın `\` .

Önkoşullar işletim sistemine göre farklılık gösterir:

### <a name="linux"></a>Linux

Bu öğretici Ubuntu Linux kullandığınızı varsayar. Bu öğreticideki adımlar Ubuntu 18,04 kullanılarak test edilmiştir.

Linux 'ta bu öğreticiyi tamamlayabilmeniz için, yerel Linux ortamınıza aşağıdaki yazılımı yüklersiniz:

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

Bu öğreticiyi Windows üzerinde gerçekleştirmek için, yerel Windows ortamınıza aşağıdaki yazılımları yüklersiniz:

* [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/) -Visual Studio 'Yu [yüklerken](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) C++ Iş yüküyle **Masaüstü geliştirmeyi** eklediğinizden emin olun.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>Kodu indirme

Hızlı başlangıç: daha önce kodu indirdiğiniz [Linux veya IoT Hub Windows üzerinde çalışan örnek bir ıot Tak ve kullan cihaz uygulaması bağlayın](../articles/iot-pnp/quickstart-connect-device.md) .

Bu öğreticide, Azure IoT Hub cihaz C SDK 'sını klonlamak ve derlemek için kullanabileceğiniz bir geliştirme ortamı hazırlarsınız.

Seçtiğiniz bir klasörde bir komut istemi açın. [Azure IoT C SDK 'ları ve kitaplıkları](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu bu konuma kopyalamak için aşağıdaki komutu yürütün:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

## <a name="build-and-run-the-code"></a>Kodu derleyin ve çalıştırın

Visual Studio 'Yu kullanarak veya komut satırında kodu oluşturabilir ve çalıştırabilirsiniz `cmake` .

### <a name="use-visual-studio"></a>Visual Studio kullanma

1. Kopyalanmış deponun kök klasörünü açın. Birkaç saniye sonra, Visual Studio 'da **CMake** desteği, projeyi çalıştırmak ve hatalarını ayıklamak için ihtiyacınız olan tümünü oluşturur.
1. Visual Studio hazırlanıyor, **Çözüm Gezgini** *iothub_client/Samples/PNP/pnp_temperature_controller/* örneğine gidin.
1. *Pnp_temperature_controller. c* dosyasına sağ tıklayın ve **hata ayıklama yapılandırması Ekle**' yi seçin. **Varsayılan**' ı seçin.
1. Visual Studio dosyada *launch.vs.js* açar. Gerekli ortam değişkenlerini ayarlamak için bu dosyayı aşağıdaki kod parçacığında gösterildiği gibi düzenleyin. [Iot Tak ve kullan hızlı başlangıç ve öğreticiler için ortamınızı ayarlamayı](../articles/iot-pnp/set-up-environment.md)tamamladıktan sonra kapsam kimliği ve kayıt birincil anahtarını bir yere görürsünüz:

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. *Pnp_temperature_controller. c* dosyasına sağ tıklayın ve **Başlangıç öğesi olarak ayarla**' yı seçin.
1. Visual Studio 'da kod yürütmeyi izlemek için, `main` *pnp_temperature_controller. c* dosyasındaki işleve bir kesme noktası ekleyin.
1. Artık **hata ayıklama** menüsünden örneği çalıştırabilir ve hata ayıklaması yapabilirsiniz.

Cihaz artık komutları ve özellik güncelleştirmelerini almaya hazır ve hub 'a telemetri verileri göndermeye başladı. Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

### <a name="use-cmake-at-the-command-line"></a>`cmake`Komut satırında kullan

Örneği oluşturmak için:

1. Kopyalanmış cihaz SDK 'sının kök klasöründe bir _CMake_ alt klasörü oluşturun ve bu klasöre gidin:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. SDK ve örnekler için proje dosyalarını oluşturmak ve derlemek için aşağıdaki komutları çalıştırın:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Örnek yapılandırma hakkında daha fazla bilgi edinmek için bkz. [örnek Readme](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Örneği çalıştırmak için:

1. _CMake_ klasöründen, yürütülebilir dosyayı içeren klasöre gidin ve çalıştırın:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

Cihaz artık komutları ve özellik güncelleştirmelerini almaya hazır ve hub 'a telemetri verileri göndermeye başladı. Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT gezginini kullanın

Cihaz istemcisi örneği başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu örnek, IoT Tak ve Kullan ısı denetleyicisi cihazı uygular. Bu örnek, [birden çok bileşeni](../articles/iot-pnp/concepts-modeling-guide.md)olan bir model uygular. [Sıcaklık cihazının dijital TWINS tanım dili (DTDL) modeli dosyası](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) , cihazın uyguladığı telemetri, Özellikler ve komutları tanımlar.

### <a name="iot-plug-and-play-helper-functions"></a>IoT Tak ve Kullan yardımcı işlevleri

Bu örnek için, kod *sık karşılaşılan* klasöründen bazı yardımcı işlevleri kullanır:

*pnp_device_client_ll* , IoT Tak ve Kullan için `model-id` bir parametre olarak içerilen Connect metodunu içerir: `PnP_CreateDeviceClientLLHandle` .

*pnp_protocol*: IoT Tak ve kullan yardımcı işlevlerini içerir:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Bu yardımcı işlevler, kendi projenizde kullanmak için yeterince geneldir. Bu örnek, bunları modeldeki her bileşene karşılık gelen üç dosyada kullanır:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

Örneğin, *pnp_deviceinfo_component* dosyasında `SendReportedPropertyForDeviceInformation` işlev yardımcı işlevlerden ikisini kullanır:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

Örnekteki her bileşen bu düzene uyar.

### <a name="code-flow"></a>Kod akışı

`main`İşlevi bağlantıyı başlatır ve model kimliğini gönderir:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

Kod, `PnP_CreateDeviceClientLLHandle` IoT Hub 'ına bağlanmak, `modelId` bir seçenek olarak ayarlamak ve doğrudan Yöntemler ve cihaz ikizi güncelleştirmeleri için cihaz yöntemi ve cihaz ikizi geri çağırma işleyicileri ayarlamak için kullanır:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` Ayrıca bağlantı bilgilerini içerir. Ortam değişkeni **IOTHUB_DEVICE_SECURITY_TYPE** , örneğin IoT Hub 'ına bağlanmak için bir bağlantı dizesi veya cihaz sağlama hizmeti kullanıp kullanmadığını belirler.

Cihaz bir model KIMLIĞI gönderdiğinde, bir IoT Tak ve Kullan cihazı haline gelir.

Geri çağırma işleyicileriyle birlikte cihaz, ikizi Updates ve doğrudan yöntem çağrılarına yeniden davranır:

* Device ikizi geri çağırması için, `PnP_TempControlComponent_DeviceTwinCallback` `PnP_ProcessTwinData` verileri işlemek için işlevini çağırır. `PnP_ProcessTwinData` , JSON 'u ayrıştırmak için *ziyaretçi düzenlerini* kullanır ve her bir öğeyi çağırarak her bir özelliği ziyaret edebilir `PnP_TempControlComponent_ApplicationPropertyCallback` .

* Komutlar geri çağırması için, `PnP_TempControlComponent_DeviceMethodCallback` işlev komut ve bileşen adlarını ayrıştırmak için yardımcı işlevini kullanır:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    `PnP_TempControlComponent_DeviceMethodCallback`İşlev sonra bileşen üzerinde komutunu çağırır:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

`main`İşlevi, IoT Hub 'ına gönderilen salt okunurdur özellikleri başlatır:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

`main`İşlevi, her bileşen için olay ve telemetri verilerini güncelleştirmek üzere bir döngü girer:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

`PnP_ThermostatComponent_SendTelemetry`İşlevi, yapısını nasıl kullanacağınızı gösterir `PNP_THERMOSTAT_COMPONENT` . Örnek, sıcaklık denetleyicisindeki iki termostats hakkındaki bilgileri depolamak için bu yapıyı kullanır. Kod, `PnP_CreateTelemetryMessageHandle` iletiyi hazırlamak ve göndermek için işlevini kullanır:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

`main`İşlev son olarak farklı bileşenleri yok eder ve hub bağlantısını kapatır.

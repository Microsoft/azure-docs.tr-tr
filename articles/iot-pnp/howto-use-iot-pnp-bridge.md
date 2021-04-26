---
title: Linux veya Windows üzerinde çalışan bir IoT Tak ve Kullan köprüsü örneğini bir IoT Hub 'ına bağlama | Microsoft Docs
description: IoT Hub 'ına bağlanan Linux veya Windows üzerinde IoT Tak ve Kullan köprüsü oluşturun ve çalıştırın. Cihaz tarafından hub 'a gönderilen bilgileri görüntülemek için Azure IoT gezgin aracını kullanın.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 28feaf266e2d5bed14077a686e6eb936e430a695
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066764"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Linux veya Windows üzerinde çalışan bir IoT Tak ve Kullan köprüsü örneğini IoT Hub 'a bağlama

Bu makalede IoT Tak ve Kullan köprüsü 'nün örnek çevresel bağdaştırıcısının nasıl oluşturulacağı, IoT Hub 'ınıza nasıl bağlanacağı ve gönderdiği Telemetriyi görüntülemek için Azure IoT gezgin aracının nasıl kullanılacağı gösterilmektedir. IoT Tak ve Kullan köprüsü C dilinde yazılır ve C için Azure IoT cihaz SDK 'sını içerir. Bu öğreticinin sonuna kadar IoT Tak ve Kullan köprüsünü çalıştırabiliyor ve Azure IoT Explorer 'da BT rapor telemetrisine bakabilirsiniz:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="IoT Tak ve Kullan köprüden bildirilen telemetri (nem, sıcaklık) tablosu ile Azure IoT Gezginini gösteren ekran görüntüsü.":::

## <a name="prerequisites"></a>Önkoşullar

Örneği Windows veya Linux makalesinde çalıştırabilirsiniz. Bu nasıl yapılır kılavuzundaki kabuk komutları ' ' yol ayırıcıları için Windows kuralına uyar `\` , Linux üzerinde takip ediyorsanız, bu ayırıcıları ' ' için değiştirmeyi unutmayın `/` .

### <a name="azure-iot-explorer"></a>Azure IoT Gezgini

Bu makalenin ikinci bölümünde örnek cihazla etkileşime geçmek için **Azure IoT gezgin** aracını kullanın. İşletim sisteminiz için [Azure IoT Explorer 'ın en son sürümünü indirin ve yükleyin](./howto-use-iot-explorer.md) .

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Hub 'ınız için _IoT Hub bağlantı dizesini_ almak için aşağıdaki komutu çalıştırın. Bu bağlantı dizesini bir yere getirin ve bu makalenin ilerleyen kısımlarında kullanın:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Hub 'a eklediğiniz cihazın _Cihaz bağlantı dizesini_ almak için aşağıdaki komutu çalıştırın. Bu bağlantı dizesini bir yere getirin ve bu makalenin ilerleyen kısımlarında kullanın:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>Köprüyü indirme ve çalıştırma

Bu makalede, Köprüsü çalıştırmak için iki seçeneğiniz vardır. Seçenekleriniz şunlardır:

- Önceden oluşturulmuş bir yürütülebiliri indirin ve bu bölümde açıklandığı gibi çalıştırın.
- Kaynak kodunu indirin ve ardından aşağıdaki bölümde açıklandığı gibi [köprüyü derleyin ve çalıştırın](#build-and-run-the-bridge) .

Köprüyü indirmek ve çalıştırmak için:

1. IoT Tak ve Kullan [yayınları sayfasına](https://github.com/Azure/iot-plug-and-play-bridge/releases)gidin.
1. İşletim sisteminiz için önceden oluşturulmuş yürütülebilir dosyayı indirin: Windows için **pnpbridge_bin.exe** veya Linux için **pnpbridge_bin** .
1. Ortam algılayıcı örneği için yapılandırma dosyasında örnek [config.js](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) indirin. Yapılandırma dosyasının yürütülebilir dosya ile aynı klasörde olduğundan emin olun.
1. Dosyadaki *config.js* düzenleyin:

    - `connection-string`Daha önce bir nota yaptığınız _Cihaz bağlantı dizesi_ değerini ekleyin.
    - `symmetric_key` _Cihaz bağlantı dizesinden_ paylaşılan erişim anahtarı değeri değerini ekleyin.
    - `root_interface_model_id`Değeri ile değiştirin `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

    Dosyadaki *config.js* ilk bölümü şu kod parçacığına benzer şekilde görünür:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Yürütülebilir dosyayı komut satırı ortamınızda çalıştırın. Köprü aşağıdakine benzer bir çıktı oluşturur:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>Köprü oluşturma ve çalıştırma

Yürütülebilir dosyayı kendiniz oluşturmayı tercih ediyorsanız, kaynak kodunu indirebilir ve betikleri oluşturun.

Seçtiğiniz bir klasörde bir komut istemi açın. [Iot Tak ve Kullan köprüsü](https://github.com/Azure/iot-plug-and-play-bridge) GitHub deposunu bu konuma kopyalamak için aşağıdaki komutu çalıştırın:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Depoyu kopyaladıktan sonra alt modülleri güncelleştirin. Alt modüller, C için Azure IoT SDK 'sını içerir:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Bu işlemin tamamlanması birkaç dakika sürer.

> [!TIP]
> Git kopyası alt modülü güncelleştirmesi başarısız olduğunda sorunlarla karşılaşırsanız, Windows dosya yollarında bu bilinen bir sorundur. Sorunu çözmek için aşağıdaki komutu deneyebilirsiniz: `git config --system core.longpaths true`

Köprü oluşturmaya yönelik önkoşullar işletim sistemine göre farklılık gösterir:

### <a name="windows"></a>Windows

Windows üzerinde IoT Tak ve Kullan köprüsü oluşturmak için aşağıdaki yazılımı yüklemelisiniz:

* [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/) -Visual Studio 'Yu [yüklerken](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) C++ Iş yüküyle **Masaüstü geliştirmeyi** eklediğinizden emin olun.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="linux"></a>Linux

Bu makale Ubuntu Linux kullandığınızı varsayar. Bu makaledeki adımlar Ubuntu 18,04 kullanılarak test edilmiştir.

Linux üzerinde IoT Tak ve Kullan köprüsü oluşturmak için, komutunu kullanarak **GCC**, **Git**, **CMake** ve tüm gerekli bağımlılıkları yüklemelisiniz `apt-get` :

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Öğesinin sürümünün `cmake` **2.8.12** üzerinde olduğunu ve **GCC** 'nin sürümünün **4.4.7**'in üzerinde olduğunu doğrulayın.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>IoT Tak ve Kullan köprüsü oluşturma

Depo dizinindeki *pnpbridge* klasörüne gidin.

Windows için, [Visual Studio için geliştirici komut istemi](/dotnet/framework/tools/developer-command-prompt-for-vs)aşağıdakileri çalıştırın:

```cmd
cd scripts\windows
build.cmd
```

Linux için benzer şekilde aşağıdakileri çalıştırın:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>Windows 'da, Visual Studio 2019 ' de CMake komutu tarafından oluşturulan çözümü açabilirsiniz. CMake dizinindeki *azure_iot_pnp_bridge. sln* proje dosyasını açın ve *pnpbridge_bin* projesini çözümdeki başlangıç projesi olarak ayarlayın. Artık Visual Studio 'da örneği oluşturabilir ve hata ayıklama modunda çalıştırabilirsiniz.

### <a name="edit-the-configuration-file"></a>Yapılandırma dosyasını düzenleme

[Iot Tak ve kullan köprü kavramları belgesinde](concepts-iot-pnp-bridge.md)yapılandırma dosyaları hakkında daha fazla bilgi edinebilirsiniz.

*iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.js* dosyayı bir metin düzenleyicisinde açın.

- `connection-string`Daha önce bir nota yaptığınız _Cihaz bağlantı dizesi_ değerini ekleyin.
- `symmetric_key` _Cihaz bağlantı dizesinden_ paylaşılan erişim anahtarı değeri değerini ekleyin.
- `root_interface_model_id`Değeri ile değiştirin `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

Dosyadaki *config.js* ilk bölümü şu kod parçacığına benzer şekilde görünür:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>IoT Tak ve Kullan köprüsünü çalıştırma

IoT Tak ve Kullan köprüsü çevresel algılayıcı örneğini başlatın. Parametresi, `config.json` önceki bölümde düzenlediğiniz dosyanın yoludur:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

Köprü aşağıdakine benzer bir çıktı oluşturur:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Köprüyü Linux üzerinde çalıştırmak için aşağıdaki komutları kullanın:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>Model dosyalarını indirin

Azure IoT Gezginini daha sonra kullanarak, IoT Hub 'ınıza bağlanırken cihazı görüntüleyebilirsiniz. Azure IoT Explorer 'ın, cihazın gönderdiği **model kimliğiyle** eşleşen bir model dosyasının yerel bir kopyasının olması gerekir. Model dosyası, IoT Gezgini 'nin cihazınızın uyguladığı telemetri, Özellikler ve komutları görüntülemesini sağlar.

Azure IoT Explorer modellerini indirmek için:

1. Yerel makinenizde *modeller* adlı bir klasör oluşturun.
1. [EnvironmentalSensor.js](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) , önceki adımda oluşturduğunuz *modeller* klasörüne kaydedin.
1. Bu model dosyasını bir metin düzenleyicisinde açarsanız, modelin KIMLIĞI olarak bir bileşeni tanımladığını görebilirsiniz `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` . Bu, dosyasında *config.js* KULLANDıĞıNıZ model kimliğiyle aynıdır.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT Gezginini Kullanma

Köprü başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın. Modelde tanımlanan telemetri, Özellikler ve komutları görebilirsiniz `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede IoT Tak ve Kullan cihazını IoT Hub 'ına bağlamayı öğrendiniz. IoT Tak ve Kullan cihazlarınızla etkileşim kuran bir çözüm oluşturma hakkında daha fazla bilgi edinmek için bkz.:

* [IoT Tak ve Kullan köprüsü nedir?](./concepts-iot-pnp-bridge.md)
* [IoT Tak ve Kullan köprüsü oluşturma, dağıtma ve genişletme](howto-build-deploy-extend-pnp-bridge.md)
* [GitHub 'da IoT Tak ve Kullan Köprüsü](https://github.com/Azure/iot-plug-and-play-bridge)

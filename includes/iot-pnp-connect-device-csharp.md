---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: b7b0cfa20257ad07d8418c39af68724d613adf41
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107821028"
---
Bu hızlı başlangıçta örnek bir IoT Tak ve Kullan cihaz uygulaması oluşturma, IoT Hub 'ınıza bağlama ve Azure IoT Explorer aracını kullanarak gönderdiği Telemetriyi görüntüleme gösterilmektedir. Örnek uygulama C# dilinde yazılır ve c# için Azure IoT örneklerine dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

[![Koda göz atın](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples/Thermostat)

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu hızlı başlangıcı Windows üzerinde tamamlayabilmeniz için geliştirme makinenizde aşağıdaki yazılımların yüklü olması gerekir:

* [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Kodu indirme

Bu hızlı başlangıçta, C# deposu için Azure IoT örneklerini klonlamak ve derlemek için kullanabileceğiniz bir geliştirme ortamı hazırlarsınız.

Seçtiğiniz bir klasörde bir komut istemi açın. [C# (.net) GitHub deposunu Microsoft Azure IoT örneklerini](https://github.com/Azure-Samples/azure-iot-samples-csharp) bu konuma kopyalamak için aşağıdaki komutu çalıştırın:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Kodu oluşturma

Artık Visual Studio 'da örneği oluşturabilir ve hata ayıklama modunda çalıştırabilirsiniz.

1. *Azure-iot-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* proje dosyasını Visual Studio 2019 ' de açın.

1. Visual Studio 'da, **hata ayıklama > Project > termostat özellikleri**' ne gidin. Ardından projeye aşağıdaki ortam değişkenlerini ekleyin:

    | Name | Değer |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | [Ortamınızı ayarlamayı](../articles/iot-pnp/set-up-environment.md) tamamladıktan sonra bir değişiklik yapmış olduğunuz değer |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | My-PNP-cihazım |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | [Ortamınızı ayarlamayı](../articles/iot-pnp/set-up-environment.md) tamamladıktan sonra bir değişiklik yapmış olduğunuz değer |

Artık Visual Studio 'da örneği oluşturabilir ve hata ayıklama modunda çalıştırabilirsiniz.

## <a name="run-the-device-sample"></a>Cihaz örneğini çalıştırma

Windows üzerinde Visual Studio 'da kod yürütmeyi izlemek için, `main` program. cs dosyasındaki işleve bir kesme noktası ekleyin.

Cihaz artık komutları ve özellik güncelleştirmelerini almaya hazır ve hub 'a telemetri verileri göndermeye başladı. Sonraki adımları tamamladıktan sonra örneği çalışır durumda tutun.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT Gezginini Kullanma

Cihaz istemcisi örneği başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu örnek, basit bir IoT Tak ve Kullan termostat cihazı uygular. Bu örneğin uyguladığı model IoT Tak ve Kullan [bileşenleri](../articles/iot-pnp/concepts-modeling-guide.md)kullanmaz. [Termostat cihazının dijital TWINS tanım dili (DTDL) modeli dosyası](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) , cihazın uyguladığı telemetri, özellik ve komutları tanımlar.

Cihaz kodu, standart yöntemi kullanarak IoT Hub 'ınıza bağlanır `CreateFromConnectionString` . Cihaz, bağlantı isteğinde uyguladığı DTDL modelinin model KIMLIĞINI gönderir. Model KIMLIĞI gönderen bir cihaz IoT Tak ve Kullan cihazdır:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

Model KIMLIĞI, aşağıdaki kod parçacığında gösterildiği gibi kodda depolanır:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Özellikleri güncelleştiren, komutları işleyen ve telemetri gönderen kod, IoT Tak ve Kullan kurallarını kullanmayan bir cihazın koduyla aynıdır.

Örnek, IoT Hub 'ınızdan gönderilen yüklerden JSON nesnelerini ayrıştırmak için bir JSON kitaplığı kullanır:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```

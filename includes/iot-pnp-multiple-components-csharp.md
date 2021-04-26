---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: cd87978f9ec34e103ede869360858c5633a8c6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612702"
---
Bu öğreticide, bileşenleriyle bir örnek IoT Tak ve Kullan cihaz uygulaması oluşturma, bunları IoT Hub 'ınıza bağlama ve Azure IoT Gezgini aracını kullanarak hub 'a gönderdiği bilgileri görüntüleme hakkında bilgi verilmektedir. Örnek uygulama C# dilinde yazılır ve c# için Azure IoT cihaz SDK 'sına dahildir. Bir çözüm Oluşturucusu, herhangi bir cihaz kodunu görüntülemeye gerek olmadan IoT Tak ve Kullan cihazının yeteneklerini anlamak için Azure IoT gezgin aracını kullanabilir.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Örnek kodu indirin.
> * Örnek kodu oluşturun.
> * Örnek cihaz uygulamasını çalıştırın ve IoT Hub 'ınıza bağlandığını doğrulayın.
> * Kaynak kodunu gözden geçirin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu öğreticiyi Windows üzerinde gerçekleştirmek için, yerel Windows ortamınıza aşağıdaki yazılımları yüklersiniz:

* [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Örnek kodla SDK deposunu kopyalayın

[Hızlı başlangıç: Windows üzerinde çalışan bir örnek ıot Tak ve kullan cihaz uygulamasını IoT Hub 'A bağlama (C#)](../articles/iot-pnp/quickstart-connect-device.md), depoyu zaten Klonladığınız.

C# GitHub deposu için Azure IoT örneklerinden örnekleri kopyalayın. Seçtiğiniz bir klasörde bir komut istemi açın. .NET GitHub deposu [için Microsoft Azure IoT örneklerini](https://github.com/Azure-Samples/azure-iot-samples-csharp) kopyalamak üzere aşağıdaki komutu çalıştırın:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Örnek cihazı çalıştırma

Bu hızlı başlangıçta, IoT Tak ve Kullan cihazı olarak C# dilinde yazılmış bir örnek sıcaklık denetleyicisi cihazı kullanırsınız. Örnek cihazı çalıştırmak için:

1. *Azure-iot-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* proje dosyasını Visual Studio 2019 ' de açın.

1. Visual Studio 'da, **hata ayıklama > Project > TemperatureController özellikleri**' ne gidin. Ardından projeye aşağıdaki ortam değişkenlerini ekleyin:

    | Name | Değer |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | [Ortamınızı ayarlamayı](../articles/iot-pnp/set-up-environment.md) tamamladıktan sonra bir değişiklik yapmış olduğunuz değer |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | My-PNP-cihazım |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | [Ortamınızı ayarlamayı](../articles/iot-pnp/set-up-environment.md) tamamladıktan sonra bir değişiklik yapmış olduğunuz değer |


1. Artık Visual Studio 'da örneği oluşturabilir ve hata ayıklama modunda çalıştırabilirsiniz.

1. Cihazın bazı bilgileri gönderdiğini ve onun çevrimiçi olduğunu söyleyen iletiler görürsünüz. Bu iletiler, cihazın hub 'a telemetri verileri göndermeyi başladığını ve artık komut ve özellik güncelleştirmelerini almaya hazır olduğunu gösterir. Bu Visual Studio örneğini kapatmayın, hizmet örneğinin çalıştığını onaylamanız gerekir.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Kodu doğrulamak için Azure IoT Gezginini Kullanma

Cihaz istemcisi örneği başladıktan sonra, çalıştığını doğrulamak için Azure IoT gezgin aracını kullanın.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu örnek, IoT Tak ve Kullan ısı denetleyicisi cihazı uygular. Bu örneğin uyguladığı model [birden çok bileşen](../articles/iot-pnp/concepts-modeling-guide.md)kullanır. [Sıcaklık cihazının dijital TWINS tanım dili (DTDL) modeli dosyası](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) , cihazın uyguladığı telemetri, Özellikler ve komutları tanımlar.

Cihaz kodu, standart yöntemi kullanarak IoT Hub 'ınıza bağlanır `CreateFromConnectionString` . Cihaz, bağlantı isteğinde uyguladığı DTDL modelinin model KIMLIĞINI gönderir. Model KIMLIĞI gönderen bir cihaz IoT Tak ve Kullan cihazdır:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

Model KIMLIĞI, aşağıdaki kod parçacığında gösterildiği gibi kodda depolanır:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

Cihaz IoT Hub 'ınıza bağlandıktan sonra kod, komut işleyicilerini kaydeder. `reboot`Komut varsayılan bileşende tanımlanmıştır. Bu `getMaxMinReport` komut, iki ıterstat bileşeninin her birinde tanımlanmıştır:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

İki termostat bileşenlerinde istenen özellik güncelleştirmelerine yönelik ayrı işleyiciler vardır:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

Örnek kod, her bir termostat bileşeninden telemetri gönderir:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

`SendTemperatureTelemetryAsync`Yöntemi, `PnpHhelper` her bileşen için ileti oluşturmak üzere sınıfını kullanır:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper`Sınıfı, birden çok bileşen modeliyle kullanabileceğiniz diğer örnek yöntemleri içerir.

İki termostat bileşeninden telemetri ve özellikleri görüntülemek için Azure IoT gezgin aracını kullanın:

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Azure IoT Gezgini 'nde birden çok bileşen cihazı":::

Ayrıca, iki termostat bileşeninden ya da varsayılan bileşende komutları çağırmak için Azure IoT gezgin aracını da kullanabilirsiniz.

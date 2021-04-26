---
title: Öğretici-genel bir Azure IoT Tak ve Kullan modülünü bağlama | Microsoft Docs
description: Öğretici-genel modüldeki örnek C# IoT Tak ve Kullan cihaz kodu kullanın.
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b981352dcfc5d3d2de2d773e2936feb27b6ba3d4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064707"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Öğretici: IoT Tak ve Kullan modülünü bağlama (C#)

Bu öğreticide, bir genel IoT Tak ve Kullan [modülünün](../iot-hub/iot-hub-devguide-module-twins.md)nasıl bağlanacağı gösterilmektedir.

Bir cihaz, bir IoT Hub 'ına bağlanırken model KIMLIĞINI yayımladığında bir IoT Tak ve Kullan aygıtıdır ve model KIMLIĞI tarafından tanımlanan dijital TWINS tanım dili (DTDL) modelinde açıklanan özellikleri ve yöntemleri uygular. Cihazların DTDL ve model KIMLIĞINI kullanma hakkında daha fazla bilgi edinmek için bkz. [ıot Tak ve kullan Geliştirici Kılavuzu](./concepts-developer-guide-device.md). Modüller model kimliklerini ve DTDL modellerini aynı şekilde kullanır.

IoT Tak ve Kullan modülünün nasıl uygulanacağını göstermek için, bu öğreticide nasıl yapılacağını gösterilmektedir:

> [!div class="checklist"]
> * IoT Hub 'ınıza modül içeren bir cihaz ekleyin.
> * Termostat C# cihaz örneğini genel bir modüle dönüştürün.
> * Modülle etkileşim kurmak için hizmet SDK 'sını kullanın.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Bu öğreticiyi Windows üzerinde gerçekleştirmek için, yerel Windows ortamınıza aşağıdaki yazılımları yüklersiniz:

* [Visual Studio (Community, Professional veya Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

IoT Hub 'ınıza **My-Module-Device** adlı yeni bir cihaz eklemek Için Azure IoT gezgin aracını kullanın.

My **-module-Module adlı bir** modül ekleyin **:**

1. Azure IoT Gezgini aracında, **My-Module-Device** cihazına gidin.

1. **Modül kimliği**' ni seçin ve **+ Ekle**' yi seçin.

1. Modül kimlik adı olarak **My-Module** ' i girin ve **Kaydet**' i seçin.

1. Modül kimlikleri listesinde **My-Module**' i seçin. Ardından birincil bağlantı dizesini kopyalayın. Bu modülün Bu bağlantı dizesini daha sonra bu öğreticide kullanacaksınız.

1. **Module ikizi** sekmesini seçin ve istenen veya bildirilen özelliklerden olmadığına dikkat edin:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>Kodu indirme

Daha önce yapmadıysanız Azure IoT Hub Device C# SDK GitHub deposunu yerel makinenize kopyalayın:

Seçtiğiniz bir klasörde bir komut istemi açın. [Azure IoT C# örnekleri](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub deposunu bu konuma kopyalamak için aşağıdaki komutu kullanın:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Projeyi hazırlama

Örnek projeyi açmak ve hazırlamak için:

1. *Azure-iot-SDK-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* proje dosyasını Visual Studio 2019 ' de açın.

1. Visual Studio 'da, **hata ayıklama > Project > termostat özellikleri**' ne gidin. Ardından projeye aşağıdaki ortam değişkenlerini ekleyin:

    | Name | Değer |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | Dizisi |
    | IOTHUB_MODULE_CONNECTION_STRING | Daha önce bir nota yaptığınız modül bağlantı dizesi |

    Örnek yapılandırma hakkında daha fazla bilgi edinmek için bkz. [örnek Readme](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md).

## <a name="modify-the-code"></a>Kodu değiştirir

Kodu bir cihaz yerine modül olarak çalışacak şekilde değiştirmek için:

1. Visual Studio 'da, *Parameter. cs* ' yi açın ve **primaryconnectionstring** değişkenini ayarlayan satırı aşağıdaki gibi değiştirin:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. Visual Studio 'da *program. cs* ' yi açın ve sınıfının yedi örneğini sınıfıyla değiştirin `DeviceClient` `ModuleClient` .

    > [!TIP]
    > Visual Studio Search ve Replace özelliğini **eşleştirme** durumuyla birlikte kullanın ve ile değiştirmek için etkinleştirilen **tüm kelimeyi eşleştirin** `DeviceClient` `ModuleClient` .

1. Visual Studio 'da, *termostat. cs* ' yi açın ve sınıfının her iki örneğini `DeviceClient` sınıfıyla birlikte `ModuleClient` aşağıdaki gibi değiştirin.

1. Değişiklikleri değiştirdiğiniz dosyalara kaydedin.

Kodu çalıştırır ve ardından ikizi güncelleştirilmiş modülünü görüntülemek için Azure IoT Gezginini kullanıyorsanız, güncelleştirilmiş cihaz ikizi ' yi model KIMLIĞI ve modül bildirilen özelliği ile görürsünüz:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Cihaz modülüyle etkileşim kurma

Hizmet SDK 'Ları, bağlı IoT Tak ve Kullan cihazların ve modüllerinin model KIMLIĞINI almanızı sağlar. Yazılabilir Özellikler ve çağrı komutları ayarlamak için hizmet SDK 'larını kullanabilirsiniz:

1. Visual Studio 'nun başka bir örneğinde, *Azure-iot-SDK-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* projesini açın.

1. Visual Studio 'da, **hata ayıklama > Project > termostat özellikleri**' ne gidin. Ardından projeye aşağıdaki ortam değişkenlerini ekleyin:

    | Name | Değer |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | My-Module-cihazım |
    | IOTHUB_CONNECTION_STRING | [Ortamınızı ayarlamayı](set-up-environment.md) tamamladıktan sonra bir değişiklik yapmış olduğunuz değer |

    > [!TIP]
    > IoT Hub bağlantı dizenizi Azure IoT gezgin aracında da bulabilirsiniz.

1. *Program. cs* dosyasını açın ve komut çağıran satırı aşağıdaki gibi değiştirin:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. *Program. cs* dosyasında, cihaz ikizi alan satırı aşağıdaki gibi değiştirin:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Modül istemci örneğinin hala çalıştığından emin olun ve ardından bu hizmet örneğini çalıştırın. Hizmet örneğindeki çıkış, cihaz ikizi ve komut çağrısının model KIMLIĞINI gösterir:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    Modül istemcisinden alınan çıkış komut işleyicisinin yanıtını gösterir:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>IoT Edge modüle Dönüştür

Bu örneği bir IoT Tak ve Kullan IoT Edge modülü olarak çalışacak şekilde dönüştürmek için, uygulamayı kapsayımalısınız. Daha fazla kod değişikliği yapmanız gerekmez. Bağlantı dizesi ortam değişkeni, başlangıçta IoT Edge çalışma zamanı tarafından eklenir. Daha fazla bilgi için bkz. [Azure IoT Edge için modülleri geliştirmek ve hatalarını ayıklamak Için Visual Studio 2019 kullanma](../iot-edge/how-to-visual-studio-develop-module.md).

Kapsayıcılı modülünüzü dağıtmayı öğrenmek için bkz.:

* [Ubuntu sanal makinelerinde Azure IoT Edge çalıştırın](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [Azure IoT Edge çalışma zamanını, detem tabanlı Linux sistemlerine yükler](../iot-edge/how-to-install-iot-edge.md).

Azure IoT gezgin aracını kullanarak şunları görebilirsiniz:

* İkizi modülündeki IoT Edge cihazınızın model KIMLIĞI.
* IoT Edge cihazdan telemetri.
* IoT Edge Module ikizi özellik güncelleştirmeleri IoT Tak ve Kullan bildirimlerini tetikledi.
* IoT Edge modülü, IoT Tak ve Kullan komutlarınıza tepki verir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, IoT Hub 'a modüllerle bir IoT Tak ve Kullan cihazını nasıl bağlayadığınızı öğrendiniz. IoT Tak ve Kullan cihaz modelleri hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [IoT Tak ve Kullan modelleme Geliştirici Kılavuzu](./concepts-developer-guide-device.md)
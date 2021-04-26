---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95510639"
---
IoT Tak ve Kullan, arka plandaki cihaz uygulamasıyla ilgili bilgi sahibi olmadan bir cihazın özellikleri ile etkileşim kurmanızı sağlayarak IoT 'yi basitleştirir. Bu hızlı başlangıçta, çözümünüze bağlı bir IoT Tak ve Kullan cihazına bağlanmak ve bunları denetlemek için Node.js nasıl kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Bu hızlı başlangıcı tamamlayabilmeniz için geliştirme makinenizde Node.js gerekir. [NodeJS.org](https://nodejs.org)adresinden birden çok platform için önerilen en son sürümü indirebilirsiniz.

Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli Node.js sürümünü doğrulayabilirsiniz:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Örnek kodla SDK deposunu kopyalayın

[Düğüm SDK deposundaki](https://github.com/Azure/azure-iot-sdk-node)örnekleri kopyalayın. Seçtiğiniz bir klasörde bir Terminal penceresi açın. Node.jsGitHub deposu [için Microsoft Azure ıOT SDK ](https://github.com/Azure/azure-iot-sdk-node) 'yı kopyalamak için aşağıdaki komutu çalıştırın:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Örnek cihazı çalıştırma

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Örnek yapılandırma hakkında daha fazla bilgi edinmek için bkz. [örnek Readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

Bu hızlı başlangıçta, IoT Tak ve Kullan cihazı olarak Node.js yazılmış örnek bir termostat cihazı kullanırsınız. Örnek cihazı çalıştırmak için:

1. Bir Terminal penceresi açın ve GitHub 'dan kopyaladığınız Node.js deposu için Microsoft Azure IoT SDK 'sını içeren yerel klasöre gidin.

1. Bu terminal penceresi, **cihaz** terminali olarak kullanılır. Klonlanmış deponuzdaki klasöre gidin ve */Azure-iot-SDK-node/Device/Samples/PNP* klasörüne gidin. Aşağıdaki komutu çalıştırarak tüm bağımlılıkları yükler:

    ```cmd/sh
    npm install
    ```

1. Aşağıdaki komutla örnek termostat cihazını çalıştırın:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Cihazın bazı bilgileri gönderdiğini ve onun çevrimiçi olduğunu söyleyen iletiler görürsünüz. Bu iletiler, cihazın hub 'a telemetri verileri göndermeyi başladığını ve artık komut ve özellik güncelleştirmelerini almaya hazır olduğunu gösterir. Bu terminali kapatmayın, hizmet örneğinin çalıştığını onaylamanız gerekir.

## <a name="run-the-sample-solution"></a>Örnek çözümü çalıştırma

Ortamınızı IoT Hub 'ınıza ve cihazınıza bağlanacak şekilde yapılandırmak için, [ıot Tak ve kullan hızlı başlangıçlarını ve öğreticilerini ayarlama](../articles/iot-pnp/set-up-environment.md) bölümünde iki ortam değişkeni oluşturdunuz:

* **IOTHUB_CONNECTION_STRING**: IoT Hub bağlantı dizesi daha önce bir nota yaptınız.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Bu hızlı başlangıçta, yeni ayarladığınız örnek cihazla etkileşim kurmak için Node.js bir örnek IoT çözümü kullanırsınız.

1. **Hizmet** terminali olarak kullanmak için başka bir Terminal penceresi açın.

1. Kopyalanmış düğüm SDK deposunda */Azure-iot-SDK-node/Service/Samples/JavaScript* klasörüne gidin. Aşağıdaki komutu çalıştırarak tüm bağımlılıkları yükler:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Bir özelliği okuyun

1. **Cihaz** terminalinde örnek termostat cihazını çalıştırdığınızda, çevrimiçi durumunu belirten aşağıdaki iletileri gördünüz:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. **Hizmet** terminaline gidin ve cihaz bilgilerini okumak üzere örneği çalıştırmak için aşağıdaki komutu kullanın:

    ```cmd/sh
    node twin.js
    ```

1. **Hizmet** terminali çıkışında, cihaz ikizi yanıtını görürsünüz. Cihazın model KIMLIĞINI ve bildirilen ilişkili özellikleri görürsünüz:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
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
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. Aşağıdaki kod parçacığında, Device ikizi 'in model KIMLIĞINI alan *twin.js* kodu gösterilmektedir:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

Bu senaryoda, çıkış çıkışları `Model Id: dtmi:com:example:Thermostat;1` .

> [!NOTE]
> Bu hizmet örnekleri, **IoT Hub hizmeti Istemcisinden** **kayıt defteri** sınıfını kullanır. Dijital TWINS API 'SI de dahil olmak üzere API 'Ler hakkında daha fazla bilgi edinmek için bkz. [hizmet Geliştirici Kılavuzu](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="update-a-writable-property"></a>Yazılabilir bir özelliği güncelleştirme

1. Dosya *twin.js* bir kod düzenleyicisinde açın.

1. Örnek kodu gözden geçirin, Device ikizi 'ı güncelleştirmenin iki yolunu gösterir. İlk yolu kullanmak için, `twinPatch` değişkeni aşağıdaki gibi değiştirin:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    `targetTemperature`Özelliği, termostat cihaz modelinde yazılabilir bir özellik olarak tanımlanır.

1. **Hizmet** terminalinde, özelliği güncelleştirme örneğini çalıştırmak için aşağıdaki komutu kullanın:

    ```cmd/sh
    node twin.js
    ```

1. **Cihaz terminalinizde** , cihazın güncelleştirmeyi aldığını görürsünüz:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. **Hizmet** terminalinizde, özelliğin güncelleştirildiğini onaylamak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    node twin.js
    ```

1. **Hizmet** terminali çıkışında, `reported` Özellikler bölümünde raporlanan hedef sıcaklığın olduğunu görürsünüz. Cihazın güncelleştirmeyi tamamlaması biraz zaman alabilir. Cihaz özellik güncelleştirmesini işleyene kadar bu adımı yineleyin:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Komut çağırma

1. *device_method.js* dosyasını açın ve kodu gözden geçirin.

1. **Hizmet** terminali ' ne gidin. Komutu çağırmak için örneği çalıştırmak için aşağıdaki komutu kullanın:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. **Hizmet** terminalinde çıktı aşağıdaki onayı gösterir:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. **Cihaz** terminalinde, komutun kabul edilen olduğunu görürsünüz:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

---
title: Azure IoT çözümünüze bağlı IoT Tak ve Kullan önizleme cihazından etkileşime geçin | Microsoft Docs
description: Node. js kullanarak Azure IoT çözümünüze bağlı IoT Tak ve Kullan önizleme cihazına bağlanın ve bunlarla etkileşime geçin.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3275c01059a61e4eb8591948695656f4e4b722ed
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152116"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Hızlı başlangıç: çözümünüze bağlı olan IoT Tak ve Kullan önizleme cihazından etkileşim kurma (node. js)

IoT Tak ve Kullan önizlemesi, temeldeki cihaz uygulamasıyla ilgili bilgi sahibi olmadan bir cihazın özellikleri ile etkileşim kurmanızı sağlayarak IoT 'yi basitleştirir. Bu hızlı başlangıçta, Node. js ' nin çözümünüze bağlı bir IoT Tak ve Kullan cihazına bağlanmak ve bunları denetlemek için nasıl kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlayabilmeniz için geliştirme makinenizde Node. js gerekir. [NodeJS.org](https://nodejs.org)adresinden birden çok platform için önerilen en son sürümü indirebilirsiniz.

Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli Node.js sürümünü doğrulayabilirsiniz:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="connect-your-device"></a>Cihazınızı bağlama

Bu hızlı başlangıçta, IoT Tak ve Kullan cihazı olarak Node. js ' de yazılmış bir örnek ortam algılayıcısı kullanırsınız. Aşağıdaki yönergelerde, cihazın nasıl yükleneceği ve çalıştırılacağı gösterilmektedir:

1. Seçtiğiniz dizinde bir Terminal penceresi açın. [Node. js GitHub deposu Için Azure IoT örneklerini](https://github.com/azure-samples/azure-iot-samples-node) bu konuma kopyalamak için aşağıdaki komutu yürütün:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Bu terminal penceresi artık _cihaz_ terminalinize göre kullanılacaktır. Kopyalanmış deponuza gidin ve **/Azure-iot-Samples-node/Digital-Twins/QuickStarts/Device** klasörüne gidin. Aşağıdaki komutu çalıştırarak tüm bağımlılıkları yükler:

    ```cmd/sh
    npm install
    ```

1. _Cihaz bağlantı dizesini_yapılandırın:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Aşağıdaki komutla örneği çalıştırın:

    ```cmd/sh
    node sample_device.js
    ```

1. Cihazın bazı bilgileri gönderdiğini ve onun çevrimiçi olduğunu söyleyen iletiler görürsünüz. Bu, cihazın hub 'a telemetri verileri göndermeyi başladığını ve artık komutları ve özellik güncelleştirmelerini almaya hazır olduğunu gösterir. Bu terminali kapatmayın, daha sonra Ayrıca, hizmet örneklerinin da çalıştığını onaylamanız gerekir.

## <a name="build-the-solution"></a>Çözümü derleme

Bu hızlı başlangıçta, örnek cihazla etkileşim kurmak için Node. js ' de örnek bir IoT çözümü kullanırsınız.

1. Başka bir Terminal penceresi açın (Bu _hizmet_ terminalinize eklenecektir). Klonlanmış deponuzdaki klasöre gidin ve **/Azure-iot-Samples-node/Digital-Twins/QuickStarts/Service** klasörüne gidin. Aşağıdaki komutu çalıştırarak tüm bağımlılıkları yükler:

    ```cmd/sh
    npm install
    ```

1. Hizmetin bağlanmasına izin vermek için _IoT Hub bağlantı dizesini_ yapılandırın:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Bir özelliği okuyun

1. _Cihazı_ terminalde bağladığınızda, çevrimiçi durumunu belirten aşağıdaki iletiyi gördünüz:

    ```cmd/sh
    reported state property as online
    ```

1. **/Azure-iot-Samples-node/Digital-Twins/QuickStarts/Service** klasöründe **get_digital_twin. js**dosyasını açın. `<DEVICE_ID_GOES_HERE>` yer tutucusunu cihaz KIMLIĞINIZLE değiştirin ve dosyayı kaydedin.

1. _Hizmet_ terminaline gidin ve cihaz bilgilerini okumak üzere örneği çalıştırmak için aşağıdaki komutu kullanın:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. _Hizmet_ terminali çıkışında `environmentalSensor` bileşenine gidin. `state` özelliğinin _çevrimiçi_olarak raporlanmışsa görürsünüz:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Yazılabilir bir özelliği güncelleştirme

1. **Update_digital_twin_property. js**dosyasını açın.

1. Dosyanın başlangıcında, büyük yertutucuları ile tanımlanmış sabitler kümesi vardır. `<DEVICE_ID_GOES_HERE>` yer tutucusunu gerçek cihaz KIMLIĞINIZLE değiştirin, kalan sabitleri aşağıdaki değerlerle güncelleştirin ve dosyayı kaydedin:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. _Hizmet_ terminali ' ne gidin ve özelliği güncelleştirmek üzere örneği çalıştırmak için aşağıdaki komutu kullanın:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. _Hizmet_ terminali çıkışı, güncelleştirilmiş cihaz bilgilerini gösterir. 42 'nin yeni parlaklık değerini görmek için `environmentalSensor` bileşenine gidin.

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. _Cihaz terminalinize_ gidin, cihazın güncelleştirmeyi aldığını görürsünüz:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. _Hizmet_ terminalinize geri dönüp, özelliğin güncelleştirildiğini onaylamak için cihaz bilgilerini yeniden almak üzere aşağıdaki komutu çalıştırın.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. _Hizmet_ terminali çıkışında, `environmentalSensor` bileşeni altında, güncelleştirilmiş parlaklık değerinin rapor edilmiş olduğunu görürsünüz. Note: cihazın güncelleştirmeyi tamamlaması biraz zaman alabilir. Cihaz özellik güncelleştirmesini gerçekten işleyene kadar bu adımı tekrarlayabilirsiniz.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Komut çağırma

1. **İnvoke_command. js**dosyasını açın.

1. Dosyanın başlangıcında, `<DEVICE_ID_GOES_HERE>` yer tutucusunu gerçek cihaz KIMLIĞINIZLE değiştirin. Kalan sabitleri aşağıdaki değerlerle güncelleştirin ve sonra dosyayı kaydedin:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. _Hizmet_ terminali ' ne gidin. Komutu çağırmak için örneği çalıştırmak için aşağıdaki komutu kullanın:

    ```cmd/sh
    node invoke_command.js
    ```

1. _Hizmet_ terminalinde çıktı aşağıdaki onayı göstermelidir:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. _Cihaz_ terminali ' ne gidin, komutun kabul edilmiş olduğunu görürsünüz:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta IoT Tak ve Kullan cihazını IoT çözümüne bağlamayı öğrendiniz. IoT Tak ve Kullan cihazlarınızla etkileşim kuran bir çözüm oluşturma hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Nasıl yapılır: bir cihaza bağlanma ve cihazla etkileşim kurma](howto-develop-solution.md)

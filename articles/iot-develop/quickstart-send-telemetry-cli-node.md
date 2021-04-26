---
title: Azure IoT Hub hızlı başlangıç 'a cihaz telemetrisi gönderme (Node.js)
description: Bu hızlı başlangıçta, bir cihazdan IoT Hub 'ına telemetri göndermek için Node.js Azure IoT Hub cihaz SDK 'sını kullanırsınız.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 0e1c99124228da9490abaa17ecc41b931631d9fb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876985"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderme (Node.js)

Uygulama **hedefi**: [cihaz uygulaması geliştirme](about-iot-develop.md#device-application-development)

Bu hızlı başlangıçta, temel bir IoT cihaz uygulama geliştirme iş akışı öğrenirsiniz. Azure CLı 'yı kullanarak Azure IoT Hub 'ı ve sanal cihazı oluşturun, ardından cihaza erişmek ve hub 'a telemetri göndermek için Azure IoT Node.js SDK 'sını kullanırsınız.

## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
- Azure CLI. Bu hızlı başlangıçta, tarayıcınızda çalışan etkileşimli bir CLı kabuğu olan Azure Cloud Shell kullanarak tüm komutları çalıştırabilirsiniz. Cloud Shell kullanıyorsanız, herhangi bir şey yüklemeniz gerekmez. CLı 'yi yerel olarak kullanmayı tercih ediyorsanız bu hızlı başlangıç, Azure CLı sürüm 2.0.76 veya üstünü gerektirir. Sürümü bulmak için az --version komutunu çalıştırın. Yüklemek veya yükseltmek için bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).
- [Node.js 10 +](https://nodejs.org). Azure Cloud Shell kullanıyorsanız, Node.js yüklü sürümünü güncelleştirmeyin. Azure Cloud Shell en son Node.js sürümüne zaten sahip.

    Aşağıdaki komutu kullanarak, geliştirme makinenizde geçerli Node.js sürümünü doğrulayın:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>İleti göndermek için Node.js SDK 'sını kullanma
Bu bölümde, sanal cihazınızdan IoT Hub 'ınıza ileti göndermek için Node.js SDK 'sını kullanacaksınız. 

1. Yeni bir terminal penceresi açın. Bu terminali Node.js SDK 'Yı yüklemek ve Node.js örnek kodla çalışmak için kullanacaksınız. Şu anda iki terminalin açık olması gerekir: Node.js ile çalışmak için açtığınız bir tane ve önceki bölümlerde Azure CLı komutları girmek için kullandığınız CLı kabuğu.

1. [Azure ıot Node.js SDK cihaz örneklerini](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) yerel makinenize kopyalayın:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. *Azure-IoT-SDK-node/Device/Samples/PNP* dizinine gidin:

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Azure IoT Node.js SDK 'sını ve gerekli bağımlılıkları yükler:

    ```console
    npm install
    ```

    Bu komut, aygıt örnekleri dizinindeki *package.js* dosyasında belirtilen şekilde doğru bağımlılıkları kurar.

1. Sanal cihazınızın Azure IoT 'ye bağlanmasını sağlamak için aşağıdaki ortam değişkenlerinin her ikisini de ayarlayın.
    * Adlı bir ortam değişkeni ayarlayın `IOTHUB_DEVICE_CONNECTION_STRING` . Değişken değeri için, önceki bölümde kaydettiğiniz cihaz bağlantı dizesini kullanın.
    * Adlı bir ortam değişkeni ayarlayın `IOTHUB_DEVICE_SECURITY_TYPE` . Değişkeni için, sabit dize değerini kullanın `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Windows CMD için her değişken için dize değerlerini çevreleyen tırnak işareti yoktur.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux veya Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. Açık CLı kabuğunuzun içinde, sanal IoT cihazınızdan olayları izlemeye başlamak için [az IoT Hub Monitor-Events](/cli/azure/iot/hub#az_iot_hub_monitor_events) komutunu çalıştırın.  Olay iletileri, geldikçe terminalde yazdırılacaktır.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Node.js terminalinizde, yüklü örnek dosya *simple_thermostat.js* için kodu çalıştırın. Bu kod, sanal IoT cihazına erişir ve IoT Hub 'ına bir ileti gönderir.

    Terminalden Node.js örneğini çalıştırmak için:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Bu kod örneği, Azure IoT Tak ve Kullan kullanır ve bu, akıllı cihazları el ile herhangi bir yapılandırma olmadan çözümlerinizle tümleştirmenize imkan tanır.  Varsayılan olarak, bu belgelerde birçok örnek IoT Tak ve Kullan kullanır. IoT PnP 'nin avantajları ve bunu kullanma ya da kullanmayan durumlar hakkında daha fazla bilgi edinmek için bkz. [IoT nedir Tak ve kullan?](../iot-pnp/overview-iot-plug-and-play.md)

Node.js kodu, cihazınızdan IoT Hub 'ına sanal bir telemetri iletisi gönderdiğinden, bu ileti, olayları izleyen CLı kabuğinizde görünür:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
```

Cihazınız artık güvenli bir şekilde bağlı ve Azure IoT Hub telemetri gönderiyor.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu hızlı başlangıçta oluşturulan Azure kaynaklarına artık ihtiyacınız yoksa, Azure CLı 'yi kullanarak bunları silebilirsiniz.

> [!IMPORTANT]
> Silinen kaynak grupları geri alınamaz. Kaynak grubu ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. 

Bir kaynak grubunu adıyla silmek için:
1. [Az Group Delete](/cli/azure/group#az_group_delete) komutunu çalıştırın. Bu komut, kaynak grubunu, IoT Hub ve oluşturduğunuz cihaz kaydını kaldırır.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Kaynak grubunun silindiğini onaylamak için [az Group List](/cli/azure/group#az_group_list) komutunu çalıştırın.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir cihazı buluta güvenli bir şekilde bağlamak ve cihazdan buluta telemetri göndermek için temel bir Azure IoT uygulama iş akışı öğrendiniz. Azure CLı 'yi bir IoT Hub 'ı ve sanal cihaz oluşturmak için kullandınız, ardından cihaza erişmek ve hub 'a telemetri göndermek için Azure IoT Node.js SDK 'sını kullandınız. 

Sonraki adım olarak, uygulama örnekleri aracılığıyla Azure IoT Node.js SDK 'sını keşfedebilirsiniz.

- [Daha fazla Node.js örnek](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): Bu dizin, IoT Hub senaryoları göstermek IÇIN Node.js SDK deposundan daha fazla örnek içerir.
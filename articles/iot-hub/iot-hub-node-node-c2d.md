---
title: Azure IoT Hub ile buluttan cihaza iletiler (node) | Microsoft Docs
description: Node. js için Azure IoT SDK 'larını kullanarak Azure IoT Hub 'ından bir cihaza buluttan cihaza ileti gönderme. Bir sanal cihaz uygulamasını buluttan cihaza iletiler alacak şekilde değiştirirsiniz ve bir arka uç uygulamasını, buluttan cihaza iletileri gönderecek şekilde değiştirirsiniz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: ba14a6bb9e234a5eae34232fc617f8b04284cd4f
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147469"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>IoT Hub ile buluttan cihaza iletileri gönderme (node. js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub, milyonlarca cihaz ile bir çözüm arka ucu arasında güvenilir ve güvenli çift yönlü iletişimin sağlanmasına yardımcı olan, tam olarak yönetilen bir hizmettir. [Bir cihazdan IoT Hub 'ına yönelik Telemetriyi, bir](quickstart-send-telemetry-node.md) IoT Hub 'ı oluşturmayı, bu kodda bir cihaz kimliği sağlamayı ve cihazdan buluta iletiler gönderen bir sanal cihaz uygulamasını nasıl kodlayacağınızı gösterir.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu öğretici, [bir cihazdan IoT Hub 'ına telemetri gönderme hakkında bir](quickstart-send-telemetry-node.md)yapı oluşturur. Şunu gösterir:

* Çözüm arka ucundan, IoT Hub aracılığıyla buluttan cihaza iletileri tek bir cihaza gönderin.
* Bir cihazda buluttan cihaza iletiler alın.
* Çözüm arka uçta, IoT Hub bir cihaza gönderilen iletiler için teslim bildirimi (*geri bildirim*) isteyin.

[IoT Hub geliştirici kılavuzunda](iot-hub-devguide-messaging.md)buluttan cihaza iletiler hakkında daha fazla bilgi edinebilirsiniz.

Bu öğreticinin sonunda iki Node. js konsol uygulaması çalıştırırsınız:

* **SimulatedDevice**, bir cihazdan Telemetriyi, IoT Hub 'ınıza bağlanan ve buluttan cihaza iletileri alan bir [IoT Hub 'ına bir cihaz aracılığıyla göndererek](quickstart-send-telemetry-node.md)oluşturulan bir uygulama sürümüdür.

* IoT Hub aracılığıyla sanal cihaz uygulamasına buluttan cihaza ileti gönderen **Sendcloudtodevicemessage**ve sonra teslim onayını alır.

> [!NOTE]
> IoT Hub, Azure IoT cihaz SDK 'Ları aracılığıyla birçok cihaz platformu ve dili (C, Java, Python ve JavaScript dahil) için SDK desteğine sahiptir. Cihazınızı Bu öğreticinin koduna bağlama ve genellikle Azure IoT Hub 'e yönelik adım adım yönergeler için bkz. [Azure IoT Geliştirici Merkezi](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Önkoşullar

* Node. js sürüm 10.0. x veya üzeri. [Geliştirme ortamınızı hazırlama](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) Windows veya Linux 'ta Bu öğretici için Node. js ' nin nasıl yükleneceğini açıklar.

* Etkin bir Azure hesabı. (Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) oluşturabilirsiniz.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Sanal cihaz uygulamasında ileti alma

Bu bölümde, IoT Hub 'ından buluttan cihaza iletileri almak için [bir cihazdan Telemetriyi bir cihazdan IoT Hub 'ına](quickstart-send-telemetry-node.md) oluşturduğunuz sanal cihaz uygulamasını değiştirirsiniz.

1. Bir metin düzenleyicisi kullanarak **SimulatedDevice. js** dosyasını açın. Bu dosya, [bir cihazdan IoT Hub 'ına bir cihazdan Telemetriyi gönder](quickstart-send-telemetry-node.md) hızlı başlangıç bölümünde indirdiğiniz Node. js örnek kodunun kök klasöründe yer alan **iot-hub\Quickstarts\simulated-Device** klasöründe bulunur.

2. IoT Hub gönderilen iletileri almak için cihaz istemcisiyle bir işleyici kaydettirin. Aşağıdaki kod parçacığında, `client.on` cihaz istemcisini oluşturan satırın hemen sonrasına çağrı ekleyin:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    Bu örnekte, cihaz iletiyi işlediğinden IoT Hub bildirmek için **tamamlandı** işlevini çağırır. MQTT taşıması kullanıyorsanız, **tamamlanacak** çağrı gerekli değildir ve atlanabilir. HTTPS ve AMQP için gereklidir.
  
   > [!NOTE]
   > Taşıma olarak MQTT veya AMQP yerine HTTPS kullanırsanız, **Deviceclient** örneği IoT Hub seyrek 'tan (her 25 dakikada bir daha az) ileti olup olmadığını denetler. MQTT, AMQP ve HTTPS desteği arasındaki farklar ve IoT Hub azaltma hakkında daha fazla bilgi için bkz. [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-node.md)bölümünde oluşturduğunuz IoT Hub 'ı aracılığıyla buluttan cihaza iletileri göndermek için bir arka uç hizmeti oluşturursunuz. Buluttan cihaza iletiler göndermek için hizmetinize **hizmet bağlantısı** izni verilmesi gerekir. Varsayılan olarak, her IoT Hub, bu izni veren **hizmet** adlı paylaşılan bir erişim ilkesiyle oluşturulur.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Buluttan cihaza ileti gönderme

Bu bölümde, sanal cihaz uygulamasına buluttan cihaza iletiler gönderen bir Node. js konsol uygulaması oluşturacaksınız. [Bir cihazdan IoT Hub 'ına bir cihazdan Telemetriyi gönder](quickstart-send-telemetry-node.md) hızlı başlangıç bölümünde eklediğiniz CIHAZıN cihaz kimliği gereklidir. [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde daha önce kopyaladığınız IoT Hub bağlantı dizesine de ihtiyacınız vardır.

1. **Sendcloudtodevicemessage**adlı boş bir klasör oluşturun. Komut isteminizde aşağıdaki komutu kullanarak **sendcloudtodevicemessage** klasöründe bir Package. JSON dosyası oluşturun. Tüm varsayılanları kabul edin:

    ```shell
    npm init
    ```

2. Komut isteminizde **sendcloudtodevicemessage** klasöründe, **Azure-ıothub** paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```shell
    npm install azure-iothub --save
    ```

3. Bir metin düzenleyicisi kullanarak, **sendcloudtodevicemessage** klasöründe **sendcloudtodevicemessage. js** dosyası oluşturun.

4. `require` **Sendcloudtodevicemessage. js** dosyasının başlangıcına aşağıdaki deyimlerini ekleyin:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Aşağıdaki kodu **Sendcloudtodevicemessage. js** dosyasına ekleyin. "{IoT Hub bağlantı dizesi}" ve "{Device ID}" yer tutucu değerlerini daha önce not ettiğiniz IoT Hub bağlantı dizesi ve cihaz KIMLIĞI ile değiştirin:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. İşlem sonuçlarını konsola yazdırmak için aşağıdaki işlevi ekleyin:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Dağıtım geri bildirim iletilerini konsola yazdırmak için aşağıdaki işlevi ekleyin:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Cihazınıza bir ileti göndermek ve cihaz buluttan cihaza iletisini geldiğinde geri bildirim iletisini işlemek için aşağıdaki kodu ekleyin:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. **Sendcloudtodevicemessage. js** dosyasını kaydedin ve kapatın.

## <a name="run-the-applications"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

1. **Sanal cihaz** klasöründeki komut isteminde, IoT Hub telemetri göndermek ve buluttan cihaza iletileri dinlemek için aşağıdaki komutu çalıştırın:

    ```shell
    node SimulatedDevice.js
    ```

    ![Sanal cihaz uygulamasını çalıştırma](./media/iot-hub-node-node-c2d/receivec2d.png)

2. **Sendcloudtodevicemessage** klasöründeki bir komut isteminde, buluttan cihaza bir ileti göndermek ve bildirim geri bildirimi için beklemek üzere aşağıdaki komutu çalıştırın:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Buluttan cihaza komutunu göndermek için uygulamayı çalıştırın](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Kolaylık olması için, bu öğretici herhangi bir yeniden deneme ilkesi uygulamaz. Üretim kodunda, [geçici hata işleme](/azure/architecture/best-practices/transient-faults)makalesinde önerildiği gibi yeniden deneme ilkelerini (üstel geri alma gibi) uygulamanız gerekir.
   >

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, buluttan cihaza iletileri gönderme ve alma hakkında daha fazla öğrendiniz.

IoT Hub kullanan uçtan uca çözümlerin tam örneklerini görmek için bkz. [Azure IoT uzaktan izleme Çözüm Hızlandırıcısı](https://azure.microsoft.com/documentation/suites/iot-suite/).

IoT Hub çözümleri geliştirme hakkında daha fazla bilgi edinmek için [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)' na bakın.

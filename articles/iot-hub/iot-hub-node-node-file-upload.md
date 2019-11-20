---
title: Dosyaları cihazlardan Azure IoT Hub 'e node ile yükleme | Microsoft Docs
description: Node. js için Azure IoT cihaz SDK 'sını kullanarak bir cihazdan buluta dosya yükleme. Karşıya yüklenen dosyalar bir Azure Storage blob kapsayıcısında depolanır.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 8747111921df494b8d5618dc8d6ece99fa821e47
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147640"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>IoT Hub (node. js) ile cihazınızdan buluta dosya yükleme

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Bu öğretici, [Azure Blob depolamaya](../storage/index.yml)bir dosyayı karşıya yüklemek için [IoT Hub dosya karşıya yükleme yeteneklerini](iot-hub-devguide-file-upload.md) nasıl kullanacağınızı göstermek için [IoT Hub öğreticisiyle buluttan cihaza ileti gönderme](iot-hub-node-node-c2d.md) ' deki kodu oluşturur. Öğretici şunların nasıl yapıldığını göstermektedir:

* Bir dosyayı karşıya yüklemek için Azure Blob URI 'SI ile güvenli bir şekilde cihaz sağlayın.

* Uygulama arka uçtaki dosyayı işlemeyi tetiklemek için karşıya dosya yükleme bildirimlerini IoT Hub kullanın.

[Bir cihazdan IoT Hub 'ına](quickstart-send-telemetry-node.md) hızlı başlangıç telemetriyi, IoT Hub temel cihazdan buluta mesajlaşma işlevlerini gösterir. Ancak, bazı senaryolarda, cihazlarınızın IoT Hub kabul ettiği görece küçük cihazdan buluta iletileri içine gönderdikleri verileri kolayca eşleyemezsiniz. Örneğin:

* Görüntü içeren büyük dosyalar
* Videolar
* Yüksek frekansta örneklenmiş veri titreşimi
* Önceden işlenmiş verilerin bir biçimi.

Bu dosyalar genellikle [Azure Data Factory](../data-factory/introduction.md) veya [Hadoop](../hdinsight/index.yml) yığını gibi araçları kullanarak bulutta toplu olarak işlenir. Dosyaları bir cihazdan kapladığınızda, IoT Hub güvenlik ve güvenilirliğini kullanmaya devam edebilirsiniz.

Bu öğreticinin sonunda iki Node. js konsol uygulaması çalıştırırsınız:

* IoT Hub 'ınız tarafından belirtilen SAS URI 'sini kullanarak bir dosyayı depolamaya yükleyen **SimulatedDevice. js**.

* IoT Hub 'ınızdaki karşıya dosya yükleme bildirimleri alan **Readfileuploadnotification. js**.

> [!NOTE]
> IoT Hub, Azure IoT cihaz SDK 'Ları aracılığıyla birçok cihaz platformunu ve dilini (C, .NET, JavaScript, Python ve Java dahil) destekler. Cihazınızı Azure IoT Hub bağlama hakkında adım adım yönergeler için [Azure IoT Geliştirici Merkezi] bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

* Node. js sürüm 10.0. x veya üzeri. [Geliştirme ortamınızı hazırlama](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) Windows veya Linux 'ta Bu öğretici için Node. js ' nin nasıl yükleneceğini açıklar.

* Etkin bir Azure hesabı. (Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Bir cihaz uygulamasından bir dosya yükleme

Bu bölümde, IoT Hub 'a bir dosya yüklemek üzere cihaz uygulaması oluşturacaksınız.

1. ```simulateddevice``` adlı bir boş klasör oluşturun.  Komut isteminizde aşağıdaki komutu kullanarak ```simulateddevice``` klasöründe bir package.json dosyası oluşturun.  Tüm varsayılanları kabul edin:

    ```cmd/sh
    npm init
    ```

2. Komut isteminizde ```simulateddevice``` klasöründe **azure-iot-device** Cihaz SDK paketini ve **azure-iot-device-mqtt** paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Bir metin düzenleyicisi kullanarak ```simulateddevice``` klasöründe bir **SimulatedDevice.js** dosyası oluşturun.

4. Aşağıdaki ```require``` deyimlerini **SimulatedDevice.js** dosyasının başlangıcına ekleyin:

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Bir `deviceconnectionstring` değişkeni ekleyin ve bir **İstemci** örneği oluşturmak için bunu kullanın.  `{deviceconnectionstring}` *IoT Hub oluşturma* bölümünde oluşturduğunuz cihazın adıyla değiştirin:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Kolaylık sağlaması için bağlantı dizesi koda dahildir: Bu önerilen bir uygulama değildir ve kullanım durumunuza ve mimarisine bağlı olarak, bu gizliliği depolamanın daha güvenli yollarını düşünmek isteyebilirsiniz.

6. İstemcisini bağlamak için aşağıdaki kodu ekleyin:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Bir geri çağırma oluşturun ve dosyayı karşıya yüklemek için **Uploadtoblob** işlevini kullanın.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. **SimulatedDevice.js** dosyasını kaydedin ve kapatın.

9. Bir resim dosyasını `simulateddevice` klasöre kopyalayın ve `myimage.png`yeniden adlandırın.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-node.md)bölümünde oluşturduğunuz IoT Hub 'ından dosya yükleme bildirim iletilerini almak için bir arka uç hizmeti oluşturursunuz. Karşıya dosya yükleme bildirim iletilerini almak için hizmetinizin **hizmet bağlantısı** izni olması gerekir. Varsayılan olarak, her IoT Hub, bu izni veren **hizmet** adlı paylaşılan bir erişim ilkesiyle oluşturulur.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Karşıya dosya yükleme bildirimi alma

Bu bölümde, IoT Hub 'ten karşıya dosya yükleme bildirimi iletileri alan bir Node. js konsol uygulaması oluşturacaksınız.

Bu bölümü tamamlayabilmeniz için IoT Hub **iothubowner** bağlantı dizesini kullanabilirsiniz. Bağlantı dizesini **paylaşılan erişim ilkesi** dikey penceresinde [Azure Portal](https://portal.azure.com/) bulacaksınız.

1. ```fileuploadnotification``` adlı bir boş klasör oluşturun.  Komut isteminizde aşağıdaki komutu kullanarak ```fileuploadnotification``` klasöründe bir package.json dosyası oluşturun.  Tüm varsayılanları kabul edin:

    ```cmd/sh
    npm init
    ```

2. Komut istemindeki ```fileuploadnotification``` klasörde, **Azure-ıothub** SDK paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Bir metin düzenleyicisi kullanarak `fileuploadnotification` klasöründe bir **fileuploadnotification. js** dosyası oluşturun.

4. `require` **Fileuploadnotification. js** dosyasının başlangıcında aşağıdaki deyimleri ekleyin:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Bir `iothubconnectionstring` değişkeni ekleyin ve bir **İstemci** örneği oluşturmak için bunu kullanın.  Yer tutucu `{iothubconnectionstring}` değerini, [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde daha önce kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin:

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Kolaylık sağlaması için bağlantı dizesi koda dahildir: Bu önerilen bir uygulama değildir ve kullanım durumunuza ve mimarisine bağlı olarak, bu gizliliği depolamanın daha güvenli yollarını düşünmek isteyebilirsiniz.

6. İstemcisini bağlamak için aşağıdaki kodu ekleyin:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. İstemcisini açın ve durum güncelleştirmelerini almak için **Getfilenotificationahize** işlevini kullanın.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. **Fileuploadnotification. js** dosyasını kaydedin ve kapatın.

## <a name="run-the-applications"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

`fileuploadnotification` Klasöründeki bir komut isteminde aşağıdaki komutu çalıştırın:

```cmd/sh
node FileUploadNotification.js
```

`simulateddevice` Klasöründeki bir komut isteminde aşağıdaki komutu çalıştırın:

```cmd/sh
node SimulatedDevice.js
```

Aşağıdaki ekran görüntüsünde **SimulatedDevice** uygulamasının çıktısı gösterilmektedir:

![Benzetimli cihaz uygulamasından çıkış](./media/iot-hub-node-node-file-upload/simulated-device.png)

Aşağıdaki ekran görüntüsünde, **Fileuploadnotification** uygulamasındaki çıkış gösterilmektedir:

![Okuma-dosya-karşıya yükleme-bildirim uygulamasının çıkışı](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Yapılandırdığınız depolama kapsayıcısında karşıya yüklenen dosyayı görüntülemek için portalını kullanabilirsiniz:

![Karşıya yüklenen dosya](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, cihazların dosya karşıya yüklemelerini basitleştirmek için IoT Hub dosya yükleme yeteneklerini nasıl kullanacağınızı öğrendiniz. Aşağıdaki makalelerle IoT Hub özelliklerini ve senaryolarını keşfetmeye devam edebilirsiniz:

* [Programlama yoluyla IoT Hub 'ı oluşturma](iot-hub-rm-template-powershell.md)

* [C SDK 'ya giriş](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK’ları](iot-hub-devguide-sdks.md)

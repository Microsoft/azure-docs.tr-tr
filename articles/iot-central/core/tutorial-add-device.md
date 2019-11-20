---
title: 'Öğretici: Azure IoT Central uygulamasına gerçek bir cihaz ekleme'
description: 'Öğretici: Azure IoT Central uygulamanıza gerçek bir cihaz ekleme ve yapılandırma hakkında bilgi edinin. Ayrıca, gerçek cihazınızdaki kod hakkında bilgi edinebilirsiniz.'
author: sandeeppujar
ms.author: sandeepu
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5234c371cc4a7238148915f4dcca1faac564f608
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108964"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Öğretici: Azure IoT Central uygulamanıza gerçek bir cihaz ekleme

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu öğreticide, Microsoft Azure IoT Central uygulamanıza *gerçek bir cihaz* ekleme ve yapılandırma açıklanmaktadır. Bu öğreticide, Node. js ' yi kullanarak gerçek cihazı kodlayın ve kodu bir masaüstü makinesinde çalıştırın. Bu öğreticiyi tamamlayabilmeniz için Raspberry PI veya Mxyonga IoT DevKit cihazı gibi ayrı bir IoT cihazına gerek yoktur.

Bu öğretici iki bölümden oluşur:

* İlk olarak, bir operatör olarak Azure IoT Central uygulamanıza gerçek bir cihaz eklemeyi ve yapılandırmayı öğrenirsiniz. Bu bölümün sonunda, ikinci bölümde kullanmak için bir bağlantı dizesi alırsınız.
* Daha sonra, cihaz geliştirici olarak, gerçek cihazınızdaki kod hakkında bilgi edinirsiniz. Örnek kodun ilk bölümünden bağlantı dizesini eklersiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni gerçek cihaz ekleme
> * Gerçek cihazı yapılandırma
> * Gerçek cihaz için uygulamadan bağlantı dizesi alma
> * İstemci kodunun uygulamaya nasıl eşlendiğini anlama
> * Gerçek cihaz için istemci kodu yapılandırma

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, oluşturucunun Azure IoT Central uygulamasını oluşturmak için en azından ilk Oluşturucu öğreticisini tamamlaması gerekir: [Yeni bir cihaz türü tanımlama](tutorial-define-device-type.md) (gerekli)

Geliştirme makinenize [Node. js](https://nodejs.org/) sürüm 8.0.0 veya üstünü yükler. Sürümünüzü denetlemek için komut satırında `node --version` çalıştırabilirsiniz. Node.js çeşitli işletim sistemleri için kullanılabilir.

## <a name="add-a-real-device"></a>Gerçek cihaz ekleme

Uygulamanıza gerçek bir cihaz eklemek için, **Yeni bir cihaz türü belirleme** öğreticisinde oluşturduğunuz [Bağlı Klima](tutorial-define-device-type.md) cihaz şablonunu kullanırsınız.

1. Yeni bir cihazı operatör olarak eklemek için sol bölmedeki **cihazlar** ' ı seçin:

   ![Bağlı klimayı gösteren Device Explorer sayfası](media/tutorial-add-device/explorer.png)

   **Device Explorer** , **bağlantılı AIR conditioner** cihaz şablonunu ve sanal cihazı gösterir. Bir cihaz şablonu oluşturduğunuzda, IoT Central otomatik olarak sanal bir cihaz oluşturur.

2. **Bağlı hava layıcı** cihaz şablonunun **Device Explorer**seçili olduğunu unutmayın. Bu şablonu kullanan gerçek bir AIR klimaları cihazını bağlamaya başlamak için **+** ve ardından **Gerçek**' ı seçin:

   ![Yeni, gerçek bir klima cihazı eklemeye başlama](media/tutorial-add-device/newreal.png)

3. Kendi **CIHAZ kimliğinizi** girin (küçük harfle yazılmalıdır) veya önerilen değeri kullanın. Ayrıca, yeni cihazınız için bir **Cihaz adı** girip **Oluştur**' a tıklayın.

   ![Cihazı yeniden adlandırma](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Gerçek bir cihaz yapılandırma

Gerçek cihaz **Bağlı Klima** cihaz şablonundan oluşturulur. Cihazınızı yapılandırmak ve cihazınız hakkında bilgileri kaydetmek üzere özellik değerlerini ayarlamak için **Ayarlar**’ı kullanabilirsiniz.

1. **Ayarlar** sayfasında, **Sıcaklık Ayarla** ayar durumunun **güncelleştirme yok** olduğuna dikkat edin. Gerçek cihaz uygulamaya bağlanıp bu ayar üzerine işlem gerçekleştirdiğini tanıyana kadar bu durumda kalır.

    ![Ayarları gösterme eşitleniyor](media/tutorial-add-device/settingssyncing.png)

2. Yeni, gerçek cihazınızın **Özellikler** sayfasında, hizmet konumu ve son hizmet tarihi düzenlenebilir özelliklerdir. Cihaz uygulamaya bağlanana kadar seri numarası ve üretici yazılımı sürümü alanları boş kalır. Bu salt yazılır değerler cihazdan gönderilir ve düzenlenemez.

    ![Gerçek cihazın Cihaz Özellikleri](media/tutorial-add-device/setproperties1.png)

3. Gerçek cihazınız için **Ölçüler**, **Kurallar** ve **Pano** sayfalarını görüntüleyebilirsiniz.

## <a name="prepare-the-client-code"></a>İstemci kodu hazırlama

Bu makaledeki örnek kod [Node. js](https://nodejs.org/) ' de yazılmıştır ve bir cihazın şunları yapmak için yeterli kodu gösterir:

* Azure IoT Central uygulamanıza bağlanın.
* Bağlı bir klima cihazı olarak sıcaklık telemetrisi gönderin.
* Azure IoT Central uygulamanıza cihaz özelliklerini gönderin.
* **Sıcaklık Ayarla** ayarını kullana bir işleci yanıtlayın.
* Azure IoT Central uygulamanızdan Echo komutunu işleyin.

[Sonraki adımlar](#next-steps) bölümünde listelenen makaleler, daha fazla örnek içerir ve diğer programlama dillerini gösterir. Cihazların Azure IoT Central’a bağlanması hakkında daha fazla bilgi için [Cihaz bağlantısı](concepts-connectivity.md) adlı makaleye bakın.

Aşağıdaki adımlar [Node.js](https://nodejs.org/) örneğinin nasıl hazırlanacağını gösterir:

### <a name="get-the-device-connection-information"></a>Cihaz bağlantı bilgilerini al

1. Uygulamanızdaki bir cihaz örneğinin bağlantı dizesi, IoT Central tarafından sağlanan cihaz bilgilerine göre oluşturulur.

   Gerçek bağlı klima cihazınızın ekranında **Bağlan**'ı seçin.

   ![Bağlantı bilgilerini görüntüleme bağlantısını gösteren cihaz sayfası](media/tutorial-add-device/connectionlink.png)

1. **Cihaz bağlantısı** sayfasında, **Kapsam KIMLIĞI**, **cihaz kimliği**ve **birincil anahtar** değerlerini bir yere unutmayın. Bu değerleri daha sonra bu öğreticide kullanacaksınız.

   ![Bağlantı ayrıntıları](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Node. js projesini hazırlama

1. Geliştirme makinenizde `connectedairconditioner` adlı bir klasör oluşturun.

1. Komut satırı ortamınızda, oluşturduğunuz `connectedairconditioner` klasörüne gidin.

1. Node.js projenizi başlatmak için, tüm varsayılanları kabul ederek aşağıdaki komutu çalıştırın:

    ```cmd/sh
    npm init
      ```

1. Gerekli paketleri yüklemek için, aşağıdaki komutu çalıştırın:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Bir metin düzenleyicisi kullanarak, **klasöründe**ConnectedAirConditioner.js`connectedairconditioner` adlı bir dosya oluşturun.

1. Aşağıdaki `require` deyimlerini **ConnectedAirConditioner.js** dosyasının başlangıcına ekleyin:

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Aşağıdaki değişken bildirimlerini dosyaya ekleyin. `{your Scope ID}`, `{your Device ID}`ve `{your Primary Key}` yer tutucuları, daha önce not ettiğiniz cihaz bağlantı bilgileriyle değiştirin:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    ```

1. Şu ana kadar yaptığınız değişiklikleri kaydedin ancak dosyayı açık tutun.

## <a name="review-client-code"></a>İstemci kodunu gözden geçir

Önceki bölümde, Azure IoT Central uygulamanıza bağlanan bir cihaz uygulaması için bir iskelet Node. js projesi oluşturdunuz. Sonraki adım, içine kod eklemektir:

* Azure IoT Central uygulamanıza bağlanın.
* Azure IoT Central uygulamanıza telemetri gönderin.
* Azure IoT Central uygulamanıza cihaz özelliklerini gönderin.
* Azure IoT Central uygulamanızdan ayarları alın.
* Azure IoT Central uygulamanızdan Echo komutunu işleyin.

1. Azure IoT Central uygulamanıza sıcaklık telemetrisi göndermek için, aşağıdaki kodu **ConnectedAirConditioner.js** dosyasına ekleyin:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Gönderdiğiniz JSON’deki alanın adı cihaz şablonunuzdaki sıcaklık telemetrisi için belirttiğiniz alanın adıyla eşleşmelidir. Bu örnekte, alanın adı **sıcaklıktır**.

1. **firmwareVersion** ve **serialNumber** gibi cihaz özelliklerini göndermek için şu tanımı ekleyin:

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) =>
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. Cihazınızın desteklediği **setTemperature** gibi ayarları tanımlamak için, aşağıdaki tanımı ekleyin:

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Azure IoT Central’dan gönderilen ayarları işlemek için, uygun cihaz kodunu bulan ve yürüten aşağıdaki işlevi ekleyin:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

    Bu işlev:

    * İstenen bir özellik gönderen Azure IoT Central’ı izler.
    * Ayar değişikliğini işlemek için çağrılacak uygun işlevi bulur.
    * Azure IoT Central uygulamanıza geri bildirim gönderir.

1. Azure IoT Central uygulamanızdan gelen **echo** gibi bir komuta yanıt vermek için şu tanımı ekleyin:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
    }
    ```

1. Azure IoT Central bağlantısını tamamlamak ve istemci kodundaki işlevleri bağlamak için aşağıdaki kodu ekleyin:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        hubClient.onDeviceMethod('echo', onCommandEcho);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };
    ```

1. Cihazı kaydedin ve IoT Central uygulamanıza bağlayın:

    ```javascript
    // Start the device (connect it to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.assignedHub);
        console.log('deviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

1. Yaptığınız değişiklikleri kaydedin.

## <a name="run-the-client-code"></a>İstemci kodunu çalıştırma

Artık istemci kodunu çalıştırabilir ve IoT Central uygulamanızla nasıl etkileşime gireceğini görebilirsiniz:

1. Örneği çalıştırmak için, komut satırı ortamınıza aşağıdaki komutu girin:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Bu komutu çalıştırdığınızda `connectedairconditioner` klasöründe olduğunuzdan emin olun.

1. Uygulama çıkışı konsola yazdırır:

   ![İstemci uygulaması çıkışı](media/tutorial-add-device/output.png)

1. 30 saniyeden sonra, cihaz **Ölçüm** sayfasında telemetriyi görürsünüz:

   ![Gerçek telemetri](media/tutorial-add-device/realtelemetry.png)

1. **Ayarlar** sayfasında, ayarın şimdi eşitlenmiş olduğunu görebilirsiniz. Cihaz ilk bağlandığında, ayar değerini aldı ve değişikliği onayladı:

   ![Ayarlar eşitlendi](media/tutorial-add-device/settingsynced.png)

1. **Ayarlar** sayfasında, cihaz sıcaklığını **95** olarak ayarlayıp **Cihazı güncelleştir**’i seçin. Örnek uygulamanız bu değişikliği alır ve işler:

   ![Ayarı alma ve işleme](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > İki “ayar güncelleştirmesi” iletisi vardır. `pending` durumu gönderildiğinde bir tane ve `completed` durumu gönderildiğinde bir tane.

1. **Ölçümler** sayfasında cihazın daha yüksek sıcaklık değerleri gönderdiğini görebilirsiniz:

    ![Sıcaklık telemetrisi şimdi daha yüksek](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="nextstepaction"]
> * Yeni gerçek cihaz ekleme
> * Yeni cihazı yapılandırma
> * Gerçek cihaz için uygulamadan bağlantı dizesi alma
> * İstemci kodunun uygulamaya nasıl eşlendiğini anlama
> * Gerçek cihaz için istemci kodu yapılandırma

Azure IoT Central uygulamanıza gerçek bir cihaz bağladığınıza göre, sonraki adımlarda önerilen bazı adımları bulabilirsiniz.

Bir operatör olarak, aşağıdakileri yapmayı öğrenebilirsiniz:

* [Cihazlarınızı yönetme](howto-manage-devices.md)
* [Cihaz kümelerini kullanma](howto-use-device-sets.md)
* [Özel analiz oluşturma](howto-use-device-sets.md)

Cihaz geliştiricisi olarak, şunları yapmayı öğrenebilirsiniz:

* [Bir DevKit cihazını hazırlama ve bağlama (C)](howto-connect-devkit.md)
* [Raspberry Pi (Python) hazırlama ve bağlama](howto-connect-raspberry-pi-python.md)
* [Raspberry Pi (C#) hazırlama ve bağlama](howto-connect-raspberry-pi-csharp.md)
* [Windows 10 IoT çekirdek cihazını hazırlama ve bağlama (C#)](howto-connect-windowsiotcore.md)
* [Genel bir Node.js istemcisini Azure IoT Central uygulamanıza bağlama](howto-connect-nodejs.md)

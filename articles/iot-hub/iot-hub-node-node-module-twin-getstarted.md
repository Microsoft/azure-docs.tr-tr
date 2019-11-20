---
title: Azure IoT Hub modül kimliği & Module ikizi (node. js) ile başlama
description: Node. js için IoT SDK 'larını kullanarak modül kimliği oluşturma ve modül ikizi güncelleştirme hakkında bilgi edinin.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: bf80925a2dc5c6d06ba14fe1c0d72a2fc1f88d51
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953864"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>IoT Hub Module kimliği ve Module ikizi (node. js) ile çalışmaya başlama

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modül kimlikleri ve modül ikizleri](iot-hub-devguide-module-twins.md), Azure IoT Hub cihaz kimliğine ve cihaz ikizine benzer, ancak daha hassas ayrıntı düzeyi sağlar. Azure IoT Hub cihaz kimliği ve cihaz ikizi, arka uç uygulamasının bir cihaz yapılandırmasına imkan tanıyıp cihazın koşullarına yönelik görünürlük sağlarken, modül kimliği ve modül ikizi de bir cihazın tek tek bileşenleri için bu özellikleri sağlar. İşletim sistemi tabanlı cihazlar veya üretici yazılımı cihazları gibi, birden fazla bileşen içeren ve bu özelliklere sahip cihazlarda her bir bileşen için yalıtılmış yapılandırma ve koşullara olanak sağlar.

Bu öğreticinin sonunda iki Node. js uygulamanız vardır:

* Cihaz ve modül istemcilerinizi bağlamak için bir cihaz kimliği, bir modül kimliği ve ilişkili güvenlik anahtarı oluşturan **CreateIdentities**.

* Güncelleştirilmiş modül ikizi tarafından raporlanan özelliklerini IoT Hub’ınıza gönderen **UpdateModuleTwinReportedProperties**.

> [!NOTE]
> Cihazlarda çalıştırılacak uygulamaları ve çözüm arka ucu oluşturmak için kullanabileceğiniz Azure IoT SDK 'Ları hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Önkoşullar

* Node. js sürüm 10.0. x veya üzeri. [Geliştirme ortamınızı hazırlama](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) Windows veya Linux 'ta Bu öğretici için Node. js ' nin nasıl yükleneceğini açıklar.

* Etkin bir Azure hesabı. (Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.)

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub bir cihaz kimliği ve modül kimliği oluşturma

Bu bölümde, IoT Hub 'ınızdaki kimlik kayıt defterinde bir cihaz kimliği ve bir modül kimliği oluşturan bir Node. js uygulaması oluşturacaksınız. Kimlik kayıt defterinde girişi olmayan bir cihaz veya modül, IoT hub'ına bağlanamaz. Daha fazla bilgi için, [IoT Hub geliştirici kılavuzunun](iot-hub-devguide-identity-registry.md)"kimlik kayıt defteri" bölümüne bakın. Bu konsol uygulamasını çalıştırdığınızda, hem cihaz hem de modül için benzersiz bir kimlik ve anahtar oluşturur. Cihazınız ve modülünüz, IoT Hub’ına cihazdan buluta iletileri gönderdiğinde kendisini tanımlamak için bu değerleri kullanır. Kimlikler büyük/küçük harfe duyarlıdır.

1. Kodunuzu barındıracak bir dizin oluşturun.

2. Bu dizinin içinde ilk olarak **NPM init-y** ' yi çalıştırarak boş bir Package. json ' ı varsayılan olarak oluşturun. Bu, kodunuzun proje dosyasıdır.

3. Hizmet SDK 'sını **node_modules** alt dizininde yüklemek için **NPM Install-S azure-ıothub\@modüller-Preview** ' i çalıştırın.

    > [!NOTE]
    > Node_modules alt dizin adı, "bir düğüm kitaplığı" demek için Word modülünü kullanır. Buradaki terim IoT Hub modüllerle hiçbir şey yapmaz.

4. Dizininizde aşağıdaki. js dosyasını oluşturun. **Add. js**olarak çağırın. Hub bağlantı dizenizi ve hub 'ınızın adını kopyalayıp yapıştırın.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Bu uygulama, **MYFIRSTDEVICE** kimliği ile bir cihaz kimliği ve myfirstdevice **adlı cihaz**altında **myfirstmodule** kimliğiyle bir modül kimliği oluşturur. (Bu modül KIMLIĞI kimlik kayıt defterinde zaten varsa, kod yalnızca var olan modül bilgilerini alır.) Uygulama daha sonra bu kimliğin birincil anahtarını görüntüler. IoT hub'ınıza bağlanmak için sanal modül uygulamasında bu anahtarı kullanırsınız.

Node Add. js kullanarak bunu çalıştırın. Bu, size cihaz kimliğiniz için bir bağlantı dizesi ve modül Kimliğiniz için bir tane sağlar.

> [!NOTE]
> IoT Hub kimlik kayıt defteri yalnızca IoT hub'ına güvenli erişim sağlamak amacıyla cihaz ve modül kimliklerini depolar. Kimlik kayıt defteri, cihaz kimliklerini ve anahtarlarını güvenlik kimlik bilgileri olarak kullanmak için depolar. Kimlik kayıt defterinin her cihaz için depoladığı etkin/devre dışı bayrağını kullanarak, ilgili cihaza erişimi devre dışı bırakabilirsiniz. Uygulamanızın cihaza özgü diğer meta verileri depolaması gerekiyorsa uygulamaya özgü bir depo kullanması gerekir. Modül kimlikleri için etkin/devre dışı bayrağı yoktur. Daha fazla bilgi için bkz. [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Node. js cihaz SDK 'sını kullanarak modül ikizi güncelleştirme

Bu bölümde, sanal cihazınızda modül ikizi bildirilen özelliklerini güncelleştiren bir Node. js uygulaması oluşturacaksınız.

1. **Modül Bağlantı dizenizi alın** - [Azure Portal](https://portal.azure.com/)oturum açın. IoT Hub’ınıza gidin ve IoT Cihazları’na tıklayın. MyFirstDevice ' ı bulun, açın ve myFirstModule başarıyla oluşturulduğunu görürsünüz. Modül bağlantı dizesini kopyalayın. Sonraki adımda gerekecektir.

   ![Azure portalı modül ayrıntısı](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Yukarıdaki adımda yaptığınız gibi, cihaz kodunuz için bir dizin oluşturun ve NPM kullanarak aygıtı başlatın ve cihaz SDK 'sını (**NPM Install-S Azure-IoT-Device-AMQP\@modüller-önizleme**) yükleyebilirsiniz.

   > [!NOTE]
   > NPM install komutu yavaş olabilir. Hasta olun, paket deposundan birçok kod çekiliyor.

   > [!NOTE]
   > NPM hatası belirten bir hata görürseniz! JSON ayrıştırılırken kayıt defteri hatası, bu, yok sayılacak güvenlidir. NPM hatası belirten bir hata görürseniz! JSON ayrıştırılırken kayıt defteri hatası, bu, yok sayılacak güvenlidir.

3. İkizi. js adlı bir dosya oluşturun. Modül kimlik dizenizi kopyalayıp yapıştırın.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Şimdi, **ikizi. js komut düğümünü**kullanarak bunu çalıştırın.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Bundan sonra şunları göreceksiniz:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub’ı kullanmaya başlamak ve diğer IoT senaryolarını keşfetmek için bkz:

* [Cihaz yönetimini kullanmaya başlama](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge kullanmaya başlama](../iot-edge/tutorial-simulate-device-linux.md)
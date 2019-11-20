---
title: Azure IoT Hub cihaz TWINS (node) ile çalışmaya başlama | Microsoft Docs
description: Azure IoT Hub cihaz ikimlerini kullanarak etiketler ekleyin ve ardından bir IoT Hub sorgusu kullanın. Node. js için Azure IoT SDK 'larını kullanarak, etiketleri ekleyen ve IoT Hub sorguyu çalıştıran bir hizmet uygulamasının benzetimini gerçekleştirin.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.openlocfilehash: 02ff65b27e03db9e9a48910e23d8ebf46de905a5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060719"
---
# <a name="get-started-with-device-twins-nodejs"></a>Cihaz ikizlerini kullanmaya başlama (node. js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Bu öğreticinin sonunda iki Node. js konsol uygulamanız olacaktır:

* Etiket ve sorgu cihaz TWINS 'i ekleyen bir Node. js arka uç uygulaması olan **Addtagsandquery. js**.

* Daha önce oluşturulan cihaz kimliğiyle IoT Hub 'ınıza bağlanan bir cihazın benzetimini yapan ve bağlantı koşulunu raporlayan bir Node. js uygulaması olan **TwinSimulatedDevice. js**.

> [!NOTE]
> [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md) makalesi, hem cihaz hem de arka uç uygulamaları oluşturmak Için kullanabileceğiniz Azure IoT SDK 'ları hakkında bilgi sağlar.
>

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Node. js sürüm 10.0. x veya üzeri.

* Etkin bir Azure hesabı. (Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.)

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Hizmet uygulaması oluşturma

Bu bölümde, **Mydeviceıd**ile ilişkili cihaz ikizi konum meta verilerini ekleyen bir Node. js konsol uygulaması oluşturacaksınız. Daha sonra, IoT Hub 'ında depolanan cihaz TWINS 'sini, ABD 'de bulunan cihazları ve ardından hücresel bağlantı bildirdikleri sorgular.

1. **Addtagsandqueryapp**adlı yeni bir boş klasör oluşturun. Komut istemindeki aşağıdaki komutu kullanarak **addtagsandqueryapp** klasöründe yeni bir Package. JSON dosyası oluşturun. `--yes` Parametresi tüm varsayılanları kabul eder.

    ```cmd/sh
    npm init --yes
    ```

2. Komut istemindeki **addtagsandqueryapp** klasöründe, **Azure-ıothub** paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Bir metin düzenleyicisi kullanarak **addtagsandqueryapp** klasöründe yeni bir **Addtagsandquery. js** dosyası oluşturun.

4. Aşağıdaki kodu **Addtagsandquery. js** dosyasına ekleyin. `{iot hub connection string}` [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin.

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    **Kayıt defteri** nesnesi, hizmetten cihaz ikikiyle etkileşimde bulunmak için gereken tüm yöntemleri kullanıma sunar. Önceki kod, **kayıt defteri** nesnesini ilk kez başlatır, sonra **mydeviceıd**için Device ikizi alır ve son olarak etiketlerini istenen konum bilgileriyle güncelleştirir.

    Etiketler güncelleştirildikten sonra **Querytwins** işlevini çağırır.

5. **Queryxtwins** işlevini uygulamak Için **Addtagsandquery. js** sonuna aşağıdaki kodu ekleyin:

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    Önceki kod iki sorgu yürütür: İlki yalnızca **Redmond43** tesisinde bulunan cihazların cihaz ikelerini seçer ve ikincisi ise yalnızca hücresel ağ üzerinden de bağlı olan cihazları seçecek şekilde sorguyu iyileştirir.

    Kod **sorgu** nesnesini oluşturduğunda, ikinci parametrede döndürülen en fazla belge sayısını belirtir. **Sorgu** nesnesi, tüm sonuçları almak Için **Nextastwin** yöntemlerini birden çok kez çağırmak için kullanabileceğiniz bir **hasMoreResults** Boolean özelliği içerir. **Daha sonra** adlı bir yöntem, cihaz ikikileri, örneğin toplama sorgularının sonuçları gibi, cihaz WINS olmayan sonuçlar için kullanılabilir.

6. Uygulamayı ile çalıştırın:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   **Redmond43** ' de bulunan tüm cihazları isteyen sorgunun sonuçlarında bir cihaz görmeniz gerekir ve sonuçları hücresel ağ kullanan cihazlara kısıtlayan sorgu için yok.

   ![Sorgu sonuçlarındaki bir cihaza bakın](media/iot-hub-node-node-twin-getstarted/service1.png)

Sonraki bölümde, bağlantı bilgilerini raporlayan ve önceki bölümde sorgunun sonucunu değiştiren bir cihaz uygulaması oluşturacaksınız.

## <a name="create-the-device-app"></a>Cihaz uygulaması oluşturma

Bu bölümde, hub 'ınıza **Mydeviceıd**olarak bağlanan bir Node. js konsol uygulaması oluşturursunuz ve ardından Device ikizi 'ın bildirilen özelliklerini bir hücresel ağ kullanılarak bağlı olduğu bilgileri içerecek şekilde güncelleştirir.

1. **Reportconnectivity**adlı yeni bir boş klasör oluşturun. **Reportconnectivity** klasöründe, komut istemindeki aşağıdaki komutu kullanarak yeni bir Package. JSON dosyası oluşturun. `--yes` Parametresi tüm varsayılanları kabul eder.

    ```cmd/sh
    npm init --yes
    ```

2. **Reportconnectivity** klasöründe komut istemindeki **Azure-IoT-Device**ve **Azure-IoT-Device-MQTT** paketlerini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Bir metin düzenleyicisi kullanarak, **reportconnectivity** klasöründe yeni bir **reportconnectivity. js** dosyası oluşturun.

4. **Reportconnectivity. js** dosyasına aşağıdaki kodu ekleyin. `{device connection string}` [IoT Hub 'ına yeni bir cihaz kaydet](#register-a-new-device-in-the-iot-hub)' de **mydeviceıd** cihaz kimliğini oluştururken kopyaladığınız cihaz bağlantı dizesiyle değiştirin.

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    **İstemci** nesnesi, cihazdan cihaz ikikiyle etkileşimde bulunmak için gereken tüm yöntemleri kullanıma sunar. Önceki kod, **istemci** nesnesini başlattıktan sonra **mydeviceıd** için Device ikizi 'ı alır ve bildirilen özelliğini bağlantı bilgileriyle güncelleştirir.

5. Cihaz uygulamasını çalıştırma

    ```cmd/sh
        node ReportConnectivity.js
    ```

    İletiyi `twin state reported`görmeniz gerekir.

6. Cihaz bağlantı bilgilerini raporladığı için, her iki sorgu da görünmelidir. **Addtagsandqueryapp** klasörüne dönün ve sorguları yeniden çalıştırın:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    **Mydeviceıd** bu kez sorgu sonuçlarında görünmelidir.

    ![Mydeviceıd 'yi her iki sorgu sonucunda göster](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure portalında yeni bir IoT hub'ı yapılandırdınız ve ardından IoT hub'ının kimlik kayıt defterinde bir cihaz kimliği oluşturdunuz. Cihaz meta verilerini bir arka uç uygulamasından Etiketler olarak eklediniz ve cihaz ikizi cihaz bağlantı bilgilerini raporlamak için bir sanal cihaz uygulaması yazmış olursunuz. Ayrıca, bu bilgilerin SQL benzeri IoT Hub sorgu dilini kullanarak nasıl sorgulanalınacağını da öğrendiniz.

Aşağıdakilerin nasıl yapılacağını öğrenmek için aşağıdaki kaynakları kullanın:

* [IoT Hub ile çalışmaya](quickstart-send-telemetry-node.md) başlayın öğreticisi sayesinde cihazlardan telemetri gönderin

* [cihazları yapılandırmak için istenen özellikleri kullan](tutorial-device-twins.md) öğreticisiyle cihaz ikizi 'nin istenen özelliklerini kullanarak cihazları yapılandırın öğreticisi

* cihazları etkileşimli olarak (kullanıcı denetimli bir uygulamadan bir fanı açmak gibi) [doğrudan Yöntemler](quickstart-control-device-node.md) öğreticisini kullanarak kontrol edin.

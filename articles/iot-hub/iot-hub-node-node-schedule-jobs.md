---
title: İşleri Azure IoT Hub zamanlayın (node) | Microsoft Docs
description: Birden çok cihazda doğrudan yöntem çağırmak için bir Azure IoT Hub işi zamanlama. İşi çalıştırmak üzere sanal cihaz uygulamalarını ve bir hizmet uygulamasını uygulamak için Node. js için Azure IoT SDK 'larını kullanın.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.openlocfilehash: 124af71e458e103392c554a9c86d679f691df5b9
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147653"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>İşleri zamanlama ve yayınlama (node. js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub, bir arka uç uygulamasının milyonlarca cihazı zamanlamayı ve güncelleştirmeyi sağlayan işleri oluşturmasına ve izlemesine olanak tanıyan, tam olarak yönetilen bir hizmettir.  İşler aşağıdaki eylemler için kullanılabilir:

* İstenen özellikleri güncelleştirme
* Etiketleri Güncelleştir
* Doğrudan metotları çağır

Kavramsal olarak, bir iş bu eylemlerden birini sarmalar ve yürütmenin ilerlemesini bir cihaz ikizi sorgusu tarafından tanımlanan bir cihaz kümesiyle izler.  Örneğin, bir arka uç uygulaması, bir cihaz ikizi sorgusu tarafından belirtilen ve gelecekteki bir zamanda zamanlanan 10.000 cihazlarda yeniden başlatma yöntemini çağırmak için bir iş kullanabilir. Bu uygulama daha sonra bu cihazların her biri tarafından yeniden başlatma yöntemini alıp yürütebileceği ilerlemeyi izleyebilir.

Bu makalelerdeki her bir özellik hakkında daha fazla bilgi edinin:

* Cihaz ikizi ve özellikleri: [Cihaz](iot-hub-node-node-twin-getstarted.md) ikizlerini ve [öğreticisini kullanmaya başlayın: Cihaz ikizi özelliklerini kullanma](tutorial-device-twins.md)

* Doğrudan Yöntemler: [IoT Hub Geliştirici Kılavuzu-doğrudan Yöntemler](iot-hub-devguide-direct-methods.md) ve [öğretici: doğrudan Yöntemler](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu öğretici şunların nasıl yapıldığını gösterir:

* Çözüm arka ucu tarafından çağrılabilen **Lockkapısı**sağlayan doğrudan yöntemine sahip bir Node. js sanal cihaz uygulaması oluşturun.

* Bir iş kullanarak sanal cihaz uygulamasındaki **Lockkapısı** doğrudan yöntemini çağıran bir Node. js konsol uygulaması oluşturun ve bir cihaz işi kullanarak istenen özellikleri günceller.

Bu öğreticinin sonunda iki Node. js uygulamanız vardır:

* IoT Hub 'ınıza cihaz kimliğiyle bağlanan ve bir **Lockkapısı** doğrudan yöntemi alan **simdevice. js**.

* sanal cihaz uygulamasında doğrudan bir yöntemi çağıran ve Device ikizi 'ın istenen özelliklerini bir iş kullanarak güncelleştiren **Schedulejobservice. js**.

## <a name="prerequisites"></a>Önkoşullar

* Node. js sürüm 10.0. x veya üzeri. [Geliştirme ortamınızı hazırlama](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) Windows veya Linux 'ta Bu öğretici için Node. js ' nin nasıl yükleneceğini açıklar.

* Etkin bir Azure hesabı. (Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.)

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Sanal cihaz uygulaması oluşturma

Bu bölümde, bulut tarafından çağrılan doğrudan bir yönteme yanıt veren bir Node. js konsol uygulaması oluşturacaksınız ve bu da sanal bir **Lockkapısı** metodunu tetikler.

1. **Simdevice**adlı yeni bir boş klasör oluşturun.  **Simdevice** klasöründe komut istemindeki aşağıdaki komutu kullanarak bir Package. JSON dosyası oluşturun.  Tüm varsayılanları kabul edin:

   ```console
   npm init
   ```

2. **Simdevice** klasöründe komut istemindeki **Azure-IoT-Device** cihaz SDK paketini ve **Azure-IoT-Device-MQTT** paketini yüklemek için aşağıdaki komutu çalıştırın:

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Bir metin düzenleyicisi kullanarak, **simdevice** klasöründe yeni bir **simdevice. js** dosyası oluşturun.

4. **Simdevice. js** dosyasının başlangıcında aşağıdaki ' gerektir ' deyimlerini ekleyin:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Bir **connectionString** değişkeni ekleyin ve bir **İstemci** örneği oluşturmak için bunu kullanın. Yer tutucu `{yourDeviceConnectionString}` değerini, daha önce kopyaladığınız cihaz bağlantı dizesiyle değiştirin.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. **Lockkapısı** metodunu işlemek için aşağıdaki işlevi ekleyin.

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. **Kilit kapısı** yöntemi için işleyiciyi kaydetmek üzere aşağıdaki kodu ekleyin.

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. **Simdevice. js** dosyasını kaydedin ve kapatın.

> [!NOTE]
> Sade ve basit bir anlatım gözetildiği için bu öğretici herhangi bir yeniden deneme ilkesi uygulamaz. Üretim kodunda, [geçici hata işleme](/azure/architecture/best-practices/transient-faults)makalesinde önerildiği gibi yeniden deneme ilkelerini (üstel geri alma gibi) uygulamanız gerekir.
>

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Bir doğrudan yöntem çağırmak ve bir cihaz ikizi özelliklerini güncelleştirmek için işleri zamanlama

Bu bölümde, doğrudan bir yöntemi kullanarak bir cihazda uzak **Lockkapısı** Başlatan bir Node. js konsol uygulaması oluşturursunuz ve Device ikizi 'in özelliklerini güncelleştirebilirsiniz.

1. **Schedulejobservice**adlı yeni bir boş klasör oluşturun.  Komut istemindeki aşağıdaki komutu kullanarak **Schedulejobservice** klasöründe bir Package. JSON dosyası oluşturun.  Tüm varsayılanları kabul edin:

    ```console
    npm init
    ```

2. Komut istemindeki **Schedulejobservice** klasöründe, **Azure-ıothub** cihaz SDK paketini ve **Azure-IoT-Device-MQTT** paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Bir metin düzenleyicisi kullanarak, **schedulejobservice** klasöründe yeni bir **schedulejobservice. js** dosyası oluşturun.

4. Aşağıdaki ' gerektir ' deyimlerini **Schedulejobservice. js** dosyasının başlangıcına ekleyin:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Aşağıdaki değişken bildirimlerini ekleyin. Yer tutucu değerini, [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)içinde kopyaladığınız değerle değiştirin. `{iothubconnectionstring}` **Mydeviceıd**'den farklı bir cihaz kaydettiniz, bunu sorgu koşulunda değiştirdiğinizden emin olun.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. İşin yürütülmesini izlemek için kullanılan aşağıdaki işlevi ekleyin:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Cihaz yöntemini çağıran işi zamanlamak için aşağıdaki kodu ekleyin:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. İşi cihaz ikizi güncelleştirmek üzere zamanlamak için aşağıdaki kodu ekleyin:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. **Schedulejobservice. js** dosyasını kaydedin ve kapatın.

## <a name="run-the-applications"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

1. **Simdevice** klasöründeki komut isteminde, önyükleme doğrudan metodunu dinlemeye başlamak için aşağıdaki komutu çalıştırın.

    ```console
    node simDevice.js
    ```

2. **Schedulejobservice** klasöründeki komut isteminde, kapıyı kilitlemek ve ikizi güncelleştirmek için işleri tetiklemek üzere aşağıdaki komutu çalıştırın.

    ```console
    node scheduleJobService.js
    ```

3. Doğrudan yönteme ve konsolundaki iş durumuna cihaz yanıtını görürsünüz.

   Aşağıdaki, doğrudan yöntemine yönelik cihaz yanıtını göstermektedir:

   ![Sanal cihaz uygulama çıkışı](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Aşağıda doğrudan yöntem ve cihaz ikizi güncelleştirmesi için hizmet zamanlama işleri ve tamamlanmayı çalıştıran işler gösterilmektedir:

   ![Sanal cihaz uygulamasını çalıştırma](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir cihaza doğrudan yöntem zamanlamak ve Device ikizi 'in özelliklerinin güncelleştirilmesi için bir iş kullandınız.

AIR üretici yazılımı güncelleştirmesi üzerinden uzak gibi IoT Hub ve cihaz yönetim desenleriyle çalışmaya devam etmek için bkz [. Öğretici: Üretici yazılımı güncelleştirmesi](tutorial-firmware-update.md)nasıl yapılır?

IoT Hub kullanmaya başlarken devam etmek için bkz. [Azure IoT Edge kullanmaya](../iot-edge/tutorial-simulate-device-linux.md)başlama.

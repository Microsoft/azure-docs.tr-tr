---
title: Komut satırından modül dağıtma-Azure IoT Edge | Microsoft Docs
description: Bir IoT Edge cihazına modül dağıtmak için Azure CLı için IoT uzantısını kullanma
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8cd6e2d13e252f9a7560b55eca58341e791db5a
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964934"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Azure CLı ile Azure IoT Edge modülleri dağıtma

İş mantığınızla IoT Edge modüller oluşturduktan sonra, bunları kenarda çalıştırmak için cihazlarınıza dağıtmak istersiniz. Verileri toplamak ve işlemek için birlikte çalışan birden fazla modülünüz varsa, bunları tek seferde dağıtabilir ve bunları bağlayan yönlendirme kurallarını bildirebilirsiniz.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) , IoT Edge gibi Azure kaynaklarını yönetmeye yönelik açık kaynaklı bir platformlar arası komut satırı aracıdır. Azure IoT Hub kaynaklarını, cihaz sağlama hizmeti örneklerini ve bağlı hub 'ları kutudan dışarı yönetmenizi sağlar. Yeni IoT uzantısı, Azure CLı 'yi cihaz yönetimi ve tam IoT Edge özelliği gibi özelliklerle zenginleştirir.

Bu makalede, bir JSON dağıtım bildiriminin nasıl oluşturulacağı ve dağıtım IoT Edge bir cihaza gönderimi için bu dosyanın nasıl kullanılacağı gösterilir. Paylaşılan etiketlerine göre birden çok cihazı hedefleyen bir dağıtım oluşturma hakkında bilgi için bkz. [IoT Edge modüllerini ölçeklendirerek dağıtma ve izleme](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-using-cli.md) .
* IoT Edge çalışma zamanı yüklü [IoT Edge bir cihaz](how-to-register-device.md#register-with-the-azure-cli) .
* Ortamınızdaki [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) . Azure CLı sürümünüz en azından 2.0.24 veya üzeri olmalıdır. Doğrulamak için `az --version` kullanın. Bu sürüm, az uzantı komutlarını destekler ve Knack komut çerçevesini kullanıma sunar.
* [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Dağıtım bildirimi yapılandırma

Dağıtım bildirimi, hangi modüllerin dağıtılacağını, modüller arasında verilerin nasıl akacağını ve modül TWINS 'in istenen özelliklerini tanımlayan bir JSON belgesidir. Dağıtım bildirimlerinin nasıl çalıştığı ve nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [IoT Edge modüllerinin nasıl kullanılabileceğini, yapılandırılacağını ve yeniden kullanıldığını anlayın](module-composition.md).

Azure CLı kullanarak modüller dağıtmak için dağıtım bildirimini yerel olarak bir. JSON dosyası olarak kaydedin. Yapılandırmayı cihazınıza uygulamak için komutunu çalıştırdığınızda sonraki bölümde bulunan dosya yolunu kullanacaksınız.

Örnek olarak bir modülle birlikte temel bir dağıtım bildirimi aşağıda verilmiştir:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="deploy-to-your-device"></a>Cihazınıza dağıtma

Modül bilgileriyle yapılandırdığınız dağıtım bildirimini uygulayarak modülleri cihazınıza dağıtırsınız.

Dizinleri dağıtım bildirimin kaydedildiği klasöre değiştirin. VS Code IoT Edge şablonlarından birini kullandıysanız, `deployment.template.json` dosyasına değil çözüm dizininizin **config** klasöründeki `deployment.json` dosyasını kullanın.

Yapılandırmayı IoT Edge bir cihaza uygulamak için aşağıdaki komutu kullanın:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Cihaz KIMLIĞI parametresi, büyük/küçük harfe duyarlıdır. İçerik parametresi, kaydettiğiniz dağıtım bildirimi dosyasını işaret eder.

   ![az IoT Edge set-modules çıktısı](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Cihazınızdaki modülleri görüntüleme

Modülleri cihazınıza dağıttıktan sonra, aşağıdaki komutla bunların tümünü görüntüleyebilirsiniz:

IoT Edge cihazınızda modülleri görüntüleme:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Cihaz KIMLIĞI parametresi, büyük/küçük harfe duyarlıdır.

   ![az IoT Hub modülü-kimlik listesi çıkışı](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge modüllerini ölçekli olarak dağıtmayı ve izlemeyi](how-to-deploy-monitor.md) öğrenin

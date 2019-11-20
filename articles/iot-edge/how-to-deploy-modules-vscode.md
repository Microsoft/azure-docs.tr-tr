---
title: Visual Studio Code modüller dağıtma-Azure IoT Edge | Microsoft Docs
description: IoT Edge cihazına modül dağıtmak için Visual Studio Code kullanma
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c3d721427075736138ba73fda51a4fd515125f1c
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964870"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Visual Studio Code Azure IoT Edge modüllerini dağıtma

İş mantığınızla IoT Edge modüller oluşturduktan sonra, bunları kenarda çalıştırmak için cihazlarınıza dağıtmak istersiniz. Verileri toplamak ve işlemek için birlikte çalışan birden fazla modülünüz varsa, bunları tek seferde dağıtabilir ve bunları bağlayan yönlendirme kurallarını bildirebilirsiniz.

Bu makalede, bir JSON dağıtım bildiriminin nasıl oluşturulacağı ve dağıtım IoT Edge bir cihaza gönderimi için bu dosyanın nasıl kullanılacağı gösterilir. Paylaşılan etiketlerine göre birden çok cihazı hedefleyen bir dağıtım oluşturma hakkında bilgi için bkz. [IoT Edge modüllerini ölçeklendirerek dağıtma ve izleme](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .
* IoT Edge çalışma zamanı yüklü [IoT Edge bir cihaz](how-to-register-device.md#register-with-visual-studio-code) .
* [Visual Studio Code](https://code.visualstudio.com/).
* Visual Studio Code için [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) .

## <a name="configure-a-deployment-manifest"></a>Dağıtım bildirimi yapılandırma

Dağıtım bildirimi, hangi modüllerin dağıtılacağını, modüller arasında verilerin nasıl akacağını ve modül TWINS 'in istenen özelliklerini tanımlayan bir JSON belgesidir. Dağıtım bildirimlerinin nasıl çalıştığı ve nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [IoT Edge modüllerinin nasıl kullanılabileceğini, yapılandırılacağını ve yeniden kullanıldığını anlayın](module-composition.md).

Visual Studio Code kullanarak modüller dağıtmak için, dağıtım bildirimini yerel olarak bir olarak kaydedin. JSON dosyası. Yapılandırmayı cihazınıza uygulamak için komutunu çalıştırdığınızda sonraki bölümde bulunan dosya yolunu kullanacaksınız.

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

## <a name="sign-in-to-access-your-iot-hub"></a>IoT Hub 'ınıza erişmek için oturum açın

IoT Hub 'ınız ile işlemler gerçekleştirmek için Visual Studio Code için Azure IoT uzantılarını kullanabilirsiniz. Bu işlemlerin çalışması için, Azure hesabınızda oturum açmanız ve üzerinde çalıştığınız IoT Hub 'ını seçmeniz gerekir.

1. Visual Studio Code ' de **Gezgin** görünümünü açın.

1. Gezgin 'in alt kısmındaki **Azure IoT Hub cihazlar** bölümünü genişletin.

   ![Azure IoT Hub cihazları bölümünü Genişlet](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. **Azure IoT Hub Devices** bölüm üstbilgisindeki **..** . öğesine tıklayın. Üç noktayı görmüyorsanız üstbilginin üzerine gelin.

1. **IoT Hub Seç ' i**seçin.

1. Azure hesabınızda oturum açmadıysanız, bunu yapmak için istemleri izleyin.

1. Azure aboneliğinizi seçin.

1. IoT Hub 'ınızı seçin.

## <a name="deploy-to-your-device"></a>Cihazınıza dağıtma

Modül bilgileriyle yapılandırdığınız dağıtım bildirimini uygulayarak modülleri cihazınıza dağıtırsınız.

1. Visual Studio Code Gezgini görünümünde, **Azure IoT Hub cihazları** bölümünü genişletin.

1. Dağıtım bildirimiyle yapılandırmak istediğiniz IoT Edge cihaza sağ tıklayın.

    > [!TIP]
    > Seçtiğiniz cihazın IoT Edge bir cihaz olduğunu doğrulamak için, modül listesini genişletmek ve **$edgeHub** ve **$edgeAgent**varlığını doğrulamak için seçin. Her IoT Edge cihaz bu iki modülü içerir.

1. **Tek cihaz Için dağıtım oluştur**' u seçin.

1. Kullanmak istediğiniz dağıtım bildirimi JSON dosyasına gidin ve **kenar dağıtım bildirimini Seç**' e tıklayın.

   ![Kenar dağıtım bildirimini seçin](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Dağıtımınızın sonuçları VS Code çıktısında yazdırılır. Başarılı dağıtımlar, hedef cihaz çalışıyorsa ve internet 'e bağlıysa birkaç dakika içinde uygulanır.

## <a name="view-modules-on-your-device"></a>Cihazınızdaki modülleri görüntüleme

Modülleri cihazınıza dağıttıktan sonra, bunların tümünü **Azure IoT Hub cihazları** bölümünde görüntüleyebilirsiniz. IoT Edge cihazınızın yanındaki oku seçerek genişletin. Çalışmakta olan tüm modüller görüntülenir.

Yeni modülleri bir cihaza yakın zamanda dağıttıysanız, **Azure IoT Hub cihazlar** bölüm üst bilgisinin üzerine gelin ve Yenile simgesini seçerek görünümü güncelleştirin.

Modülün adına sağ tıklayıp ikizi modülünü görüntüleyin ve düzenleyin.

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge modüllerini ölçekli olarak dağıtmayı ve izlemeyi](how-to-deploy-monitor.md) öğrenin

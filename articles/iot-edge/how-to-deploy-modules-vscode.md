---
title: Visual Studio Code Azure IoT Edge 'dan modül dağıtma
description: IoT Hub bir dağıtım bildirimi tarafından yapılandırıldığı gibi, IoT Edge cihazınıza IoT Edge bir modül göndermek için Azure IoT araçlarıyla Visual Studio Code kullanın.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b4840b36d5dadc14bbd664b844e3bcce6f6fec4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201683"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Visual Studio Code Azure IoT Edge modüllerini dağıtma

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

İş mantığınızla IoT Edge modüller oluşturduktan sonra, bunları kenarda çalıştırmak için cihazlarınıza dağıtmak istersiniz. Verileri toplamak ve işlemek için birlikte çalışan birden fazla modülünüz varsa, bunları tek seferde dağıtabilir ve bunları bağlayan yönlendirme kurallarını bildirebilirsiniz.

Bu makalede, bir JSON dağıtım bildiriminin nasıl oluşturulacağı ve dağıtım IoT Edge bir cihaza gönderimi için bu dosyanın nasıl kullanılacağı gösterilir. Paylaşılan etiketlerine göre birden çok cihazı hedefleyen bir dağıtım oluşturma hakkında bilgi için, bkz. [Visual Studio Code kullanarak ölçekte IoT Edge modülleri dağıtma](how-to-deploy-vscode-at-scale.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .
* IoT Edge cihaz

  Ayarlanmış bir IoT Edge cihazınız yoksa bir Azure sanal makinesinde bir tane oluşturabilirsiniz. [Bir sanal Linux cihazı oluşturmak](quickstart-linux.md) veya [bir sanal Windows cihazı oluşturmak](quickstart.md)için hızlı başlangıç makalelerinden birindeki adımları izleyin.

* [Visual Studio Code](https://code.visualstudio.com/).
* Visual Studio Code için [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) .

## <a name="configure-a-deployment-manifest"></a>Dağıtım bildirimi yapılandırma

Dağıtım bildirimi, hangi modüllerin dağıtılacağını, modüller arasında verilerin nasıl akacağını ve modül TWINS 'in istenen özelliklerini tanımlayan bir JSON belgesidir. Dağıtım bildirimlerinin nasıl çalıştığı ve nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [IoT Edge modüllerinin nasıl kullanılabileceğini, yapılandırılacağını ve yeniden kullanıldığını anlayın](module-composition.md).

Visual Studio Code kullanarak modüller dağıtmak için, dağıtım bildirimini yerel olarak bir olarak kaydedin. JSON dosyası. Yapılandırmayı cihazınıza uygulamak için komutunu çalıştırdığınızda sonraki bölümde bulunan dosya yolunu kullanacaksınız.

Örnek olarak bir modülle birlikte temel bir dağıtım bildirimi aşağıda verilmiştir:

>[!NOTE]
>Bu örnek dağıtım bildirimi, IoT Edge Aracısı ve hub için şema sürüm 1,1 ' u kullanır. Şema sürümü 1,1, IoT Edge Version 1.0.10 ile birlikte yayımlanmıştır ve modül başlangıç sırası ve rota önceliği belirleme gibi özellikleri sunar.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.1",
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
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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
           "schemaVersion": "1.1",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
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

1. Gezgin 'in alt kısmındaki **Azure IoT Hub** bölümünü genişletin.

   ![Azure IoT Hub bölümünü Genişlet](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. **Azure IoT Hub** bölüm üstbilgisindeki **..** . öğesine tıklayın. Üç noktayı görmüyorsanız üstbilginin üzerine gelin.

1. **IoT Hub Seç ' i** seçin.

1. Azure hesabınızda oturum açmadıysanız, bunu yapmak için istemleri izleyin.

1. Azure aboneliğinizi seçin.

1. IoT Hub 'ınızı seçin.

## <a name="deploy-to-your-device"></a>Cihazınıza dağıtma

Modül bilgileriyle yapılandırdığınız dağıtım bildirimini uygulayarak modülleri cihazınıza dağıtırsınız.

1. Visual Studio Code Gezgini görünümünde, **Azure IoT Hub** bölümünü genişletin ve ardından **cihazlar** düğümünü genişletin.

1. Dağıtım bildirimiyle yapılandırmak istediğiniz IoT Edge cihaza sağ tıklayın.

    > [!TIP]
    > Seçtiğiniz cihazın IoT Edge bir cihaz olduğunu doğrulamak için, modül listesini genişletmek ve **$edgeHub** ve **$edgeAgent** varlığını doğrulamak için seçin. Her IoT Edge cihaz bu iki modülü içerir.

1. **Tek cihaz Için dağıtım oluştur**' u seçin.

1. Kullanmak istediğiniz dağıtım bildirimi JSON dosyasına gidin ve **kenar dağıtım bildirimini Seç**' e tıklayın.

   ![Kenar dağıtım bildirimini seçin](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Dağıtımınızın sonuçları VS Code çıktısında yazdırılır. Başarılı dağıtımlar, hedef cihaz çalışıyorsa ve internet 'e bağlıysa birkaç dakika içinde uygulanır.

## <a name="view-modules-on-your-device"></a>Cihazınızdaki modülleri görüntüleme

Modülleri cihazınıza dağıttıktan sonra, bunların tümünü **Azure IoT Hub** bölümünde görüntüleyebilirsiniz. IoT Edge cihazınızın yanındaki oku seçerek genişletin. Çalışmakta olan tüm modüller görüntülenir.

Yeni modülleri bir cihaza yakın zamanda dağıttıysanız, **Azure IoT Hub cihazlar** bölüm üst bilgisinin üzerine gelin ve Yenile simgesini seçerek görünümü güncelleştirin.

Modülün adına sağ tıklayıp ikizi modülünü görüntüleyin ve düzenleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Visual Studio Code kullanarak ölçekli IoT Edge modüllerini dağıtmayı ve izlemeyi](how-to-deploy-at-scale.md) öğrenin

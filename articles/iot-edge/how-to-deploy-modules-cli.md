---
title: Azure CLı komut satırından modüller dağıtma-Azure IoT Edge
description: IoT Hub bir IoT Edge modülünü bir dağıtım bildirimiyle yapılandırıldığı şekilde IoT Edge cihazınıza iletmek için Azure IoT uzantısıyla Azure CLı 'yi kullanın.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: cc4308cf69ecb99fccb09a6668825397675983cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201139"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Azure CLı ile Azure IoT Edge modülleri dağıtma

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

İş mantığınızla IoT Edge modüller oluşturduktan sonra, bunları kenarda çalıştırmak için cihazlarınıza dağıtmak istersiniz. Verileri toplamak ve işlemek için birlikte çalışan birden fazla modülünüz varsa, bunları tek seferde dağıtabilir ve bunları bağlayan yönlendirme kurallarını bildirebilirsiniz.

[Azure CLI](/cli/azure) , IoT Edge gibi Azure kaynaklarını yönetmeye yönelik açık kaynaklı bir platformlar arası komut satırı aracıdır. Azure IoT Hub kaynaklarını, cihaz sağlama hizmeti örneklerini ve bağlı hub 'ları kutudan dışarı yönetmenizi sağlar. Yeni IoT uzantısı, Azure CLı 'yi cihaz yönetimi ve tam IoT Edge özelliği gibi özelliklerle zenginleştirir.

Bu makalede, bir JSON dağıtım bildiriminin nasıl oluşturulacağı ve dağıtım IoT Edge bir cihaza gönderimi için bu dosyanın nasıl kullanılacağı gösterilir. Paylaşılan etiketlerine göre birden çok cihazı hedefleyen bir dağıtım oluşturma hakkında bilgi için bkz. [IoT Edge modüllerini ölçeklendirerek dağıtma ve izleme](how-to-deploy-cli-at-scale.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-using-cli.md) .
* IoT Edge cihaz

  Ayarlanmış bir IoT Edge cihazınız yoksa bir Azure sanal makinesinde bir tane oluşturabilirsiniz. [Bir sanal Linux cihazı oluşturmak](quickstart-linux.md) veya [bir sanal Windows cihazı oluşturmak](quickstart.md)için hızlı başlangıç makalelerinden birindeki adımları izleyin.

* Ortamınızdaki [Azure CLI](/cli/azure/install-azure-cli) . Azure CLı sürümünüz en azından 2.0.70 veya üzeri olmalıdır. Doğrulamak için `az --version` kullanın. Bu sürüm, az uzantı komutlarını destekler ve Knack komut çerçevesini kullanıma sunar.
* [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Dağıtım bildirimi yapılandırma

Dağıtım bildirimi, hangi modüllerin dağıtılacağını, modüller arasında verilerin nasıl akacağını ve modül TWINS 'in istenen özelliklerini tanımlayan bir JSON belgesidir. Dağıtım bildirimlerinin nasıl çalıştığı ve nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [IoT Edge modüllerinin nasıl kullanılabileceğini, yapılandırılacağını ve yeniden kullanıldığını anlayın](module-composition.md).

Azure CLı kullanarak modüller dağıtmak için dağıtım bildirimini yerel olarak bir. JSON dosyası olarak kaydedin. Yapılandırmayı cihazınıza uygulamak için komutunu çalıştırdığınızda sonraki bölümde bulunan dosya yolunu kullanacaksınız.

Örnek olarak bir modülle birlikte temel bir dağıtım bildirimi aşağıda verilmiştir:

>[!NOTE]
>Bu örnek dağıtım bildirimi, IoT Edge Aracısı ve hub için şema sürüm 1,1 ' u kullanır. Şema sürümü 1,1, IoT Edge Version 1.0.10 ile birlikte yayımlanmıştır ve modül başlangıç sırası ve rota önceliği belirleme gibi özellikleri sunar.

```json
{
  "content": {
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>Cihazınıza dağıtma

Modül bilgileriyle yapılandırdığınız dağıtım bildirimini uygulayarak modülleri cihazınıza dağıtırsınız.

Dizinleri dağıtım bildirimin kaydedildiği klasöre değiştirin. VS Code IoT Edge şablonlarından birini kullandıysanız dosyayı `deployment.json` değil çözüm dizininizin **config** klasöründeki dosyasını kullanın `deployment.template.json` .

Yapılandırmayı IoT Edge bir cihaza uygulamak için aşağıdaki komutu kullanın:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Cihaz KIMLIĞI parametresi, büyük/küçük harfe duyarlıdır. İçerik parametresi, kaydettiğiniz dağıtım bildirimi dosyasını işaret eder.

   ![az IoT Edge set-modules çıktısı](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Cihazınızdaki modülleri görüntüleme

Modülleri cihazınıza dağıttıktan sonra, aşağıdaki komutla bunların tümünü görüntüleyebilirsiniz:

IoT Edge cihazınızda modülleri görüntüleme:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Cihaz KIMLIĞI parametresi, büyük/küçük harfe duyarlıdır.

   ![az IoT Hub modülü-kimlik listesi çıkışı](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge modüllerini ölçekli olarak dağıtmayı ve izlemeyi](how-to-deploy-at-scale.md) öğrenin
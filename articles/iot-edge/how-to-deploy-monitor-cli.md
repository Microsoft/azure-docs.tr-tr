---
title: Komut satırından otomatik dağıtımlar oluştur-Azure IoT Edge | Microsoft Docs
description: IoT Edge cihaz grupları için otomatik dağıtımlar oluşturmak üzere Azure CLı için IoT uzantısını kullanma
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7ba38e1aa7196263b0ac64a6c92984cc3e7416a6
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964806"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Azure CLı kullanarak IoT Edge modüllerini ölçeklendirerek dağıtma ve izleme

Tek seferde birçok cihaza yönelik devam eden dağıtımları yönetmek için Azure komut satırı arabirimini kullanarak **IoT Edge otomatik dağıtım** oluşturun. IoT Edge için otomatik dağıtımlar IoT Hub [otomatik cihaz yönetimi](/azure/iot-hub/iot-hub-automatic-device-management) özelliğinin bir parçasıdır. Dağıtımlar birden çok modülü birden çok cihaza dağıtmanızı, modüllerin durumunu ve durumunu izlemenizi ve gerektiğinde değişiklik yapmayı sağlayan dinamik işlemlerdir. 

Daha fazla bilgi için bkz. [tek cihazlarda veya ölçekte IoT Edge otomatik dağıtımları anlama](module-deployment-monitoring.md).

Bu makalede, Azure CLı ve IoT uzantısını ayarlarsınız. Daha sonra, bir IoT Edge cihaz kümesine modül dağıtmayı ve kullanılabilir CLı komutlarını kullanarak ilerlemeyi izlemeyi öğreneceksiniz.

## <a name="cli-prerequisites"></a>CLı önkoşulları

* Azure aboneliğinizdeki bir [IoT Hub 'ı](../iot-hub/iot-hub-create-using-cli.md) . 
* IoT Edge çalışma zamanının yüklü olduğu [cihazlar IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) .
* Ortamınızdaki [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) . Azure CLı sürümünüz en azından 2.0.24 veya üzeri olmalıdır. Doğrulamak için `az --version` kullanın. Bu sürüm, az uzantı komutlarını destekler ve Knack komut çerçevesini kullanıma sunar. 
* [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Dağıtım bildirimi yapılandırma

Dağıtım bildirimi, hangi modüllerin dağıtılacağını, modüller arasında verilerin nasıl akacağını ve modül TWINS 'in istenen özelliklerini tanımlayan bir JSON belgesidir. Daha fazla bilgi için bkz. [IoT Edge modül dağıtmayı ve yollar oluşturmayı öğrenin](module-composition.md).

Azure CLı kullanarak modüller dağıtmak için dağıtım bildirimini yerel olarak bir. txt dosyası olarak kaydedin. Yapılandırmayı cihazınıza uygulamak için komutunu çalıştırdığınızda sonraki bölümde dosya yolunu kullanın. 

Örnek olarak bir modülle birlikte temel bir dağıtım bildirimi aşağıda verilmiştir:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
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
}
```

## <a name="identify-devices-using-tags"></a>Etiketleri kullanarak cihazları tanımla

Bir dağıtım oluşturabilmeniz için önce hangi cihazların etkilenmesini istediğinizi belirleyebilmelisiniz. Azure IoT Edge cihaz ikizi **etiketleri** kullanarak cihazları tanımlar. Her cihazda, çözümünüz için anlamlı olacak şekilde tanımladığınız birden fazla etiket olabilir. Örneğin, akıllı binaları bir kampüs yönetiyorsanız, bir cihaza aşağıdaki etiketleri ekleyebilirsiniz:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Cihaz ikgörüti ve etiketleri hakkında daha fazla bilgi için bkz. [IoT Hub 'da cihaz TWINS 'ı anlama ve kullanma](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Dağıtım oluşturma

Dağıtım bildiriminin yanı sıra diğer parametrelerden oluşan bir dağıtım oluşturarak modülleri hedef cihazlarınıza dağıtırsınız. 

Dağıtım oluşturmak için [az IoT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) komutunu kullanın:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Dağıtım oluşturma komutu aşağıdaki parametreleri alır: 

* **--Deployment-ID** -IoT Hub 'ında oluşturulacak dağıtımın adı. Dağıtımınıza 128 küçük harflerden oluşan benzersiz bir ad verin. Boşluklardan ve şu geçersiz karakterlerden kaçının: `& ^ [ ] { } \ | " < > /`.
* **--hub-adı** -dağıtımın oluşturulacağı IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Geçerli aboneliğinizi `az account set -s [subscription name]` komutuyla değiştirin.
* **--Content** -FilePath öğesine DAĞıTıM bildirimi JSON 'ı. 
* **--labels** -dağıtımlarınızın izlenmesine yardımcı olmak için Etiketler ekleyin. Etiketler, dağıtımınızı tanımlayan ad ve değer çiftleridir. Etiketler, adlar ve değerler için JSON biçimlendirmesi alır. Örneğin, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-Condition** -Bu dağıtıma hangi cihazların hedeflenceğini belirleyen bir hedef koşul girin. Bu koşul, Device ikizi etiketlerine veya Device ikizi bildirilen özelliklerine dayalıdır ve ifade biçimiyle eşleşmelidir. Örneğin, `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--Priority** -pozitif bir tamsayı. İki veya daha fazla dağıtımın aynı cihaza hedeflenme durumunda, öncelik için en yüksek sayısal değere sahip dağıtım uygulanır.

## <a name="monitor-a-deployment"></a>Bir dağıtımı izleme

Tek bir dağıtımın ayrıntılarını görüntülemek için [az IoT Edge Deployment Show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) komutunu kullanın:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Dağıtım göster komutu aşağıdaki parametreleri alır:
* **--Deployment-ID** -IoT Hub 'ında bulunan dağıtımın adı.
* **--hub-adı** -dağıtımın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komut `az account set -s [subscription name]` istenen aboneliğe geçiş yapın

Komut penceresinde dağıtımı inceleyin. **Ölçümler** özelliği her bir hub tarafından değerlendirilen her ölçüm için bir sayı listeler:

* **Targetedcount** -hedefleme koşuluyla eşleşen IoT Hub bir cihaz TWINS sayısını belirten bir sistem ölçümü.
* **appliedCount** -bir sistem ölçümü, dağıtım içeriğinin IoT Hub ' de modül TWINS 'lerine uyguladığı cihazların sayısını belirtir.
* **Reportedbaşarıyla Fulcount** -IoT Edge istemci çalışma zamanının başarılı olarak bildirdiği IoT Edge cihazların sayısını belirten bir cihaz ölçümü.
* **Reportedfailedcount** -IoT Edge istemci çalışma zamanından dağıtım raporlama hatası içindeki IoT Edge cihazlarının sayısını belirten bir cihaz ölçümü.

[Az IoT Edge Deployment Show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) komutunu kullanarak her ölçüm Için cihaz kimliklerinin veya nesnelerinin bir listesini gösterebilirsiniz:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

Dağıtım Show-Metric komutu aşağıdaki parametreleri alır: 
* **--Deployment-ID** -IoT Hub 'ında bulunan dağıtımın adı.
* **--Metric-ID** -cihaz kimliklerinin listesini görmek istediğiniz ölçümün adı, örneğin `reportedFailedCount`
* **--hub-adı** -dağıtımın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komut `az account set -s [subscription name]` istenen aboneliğe geçiş yapın

## <a name="modify-a-deployment"></a>Bir dağıtımı değiştirme

Bir dağıtımı değiştirdiğinizde, değişiklikler hedeflenen tüm cihazlara hemen çoğaltılır. 

Hedef koşulu güncelleştirirseniz, aşağıdaki güncelleştirmeler gerçekleşir:

* Bir cihaz eski hedef koşulunu karşılamıyorsa, ancak yeni hedef koşulunu karşılıyorsa ve bu dağıtım bu cihaz için en yüksek önceliktir, bu dağıtım cihaza uygulanır. 
* Şu anda bu dağıtımı çalıştıran bir cihaz artık hedef koşulu karşılamıyorsa, bu dağıtımı kaldırır ve sonraki en yüksek öncelikli dağıtımı alır. 
* Şu anda bu dağıtımı çalıştıran bir cihaz artık hedef koşulunu karşılamıyorsa ve diğer dağıtımların hedef koşulunu karşılamıyorsa, cihazda hiçbir değişiklik gerçekleşmez. Cihaz geçerli durumunda mevcut modüllerini çalıştırmaya devam eder, ancak artık bu dağıtımın bir parçası olarak yönetilmez. Başka bir dağıtımın hedef koşulunu karşılıyorsa, bu dağıtımı kaldırır ve yenisini alır. 

Bir dağıtımı güncelleştirmek için [az IoT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) komutunu kullanın:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Dağıtım güncelleştirme komutu aşağıdaki parametreleri alır:
* **--Deployment-ID** -IoT Hub 'ında bulunan dağıtımın adı.
* **--hub-adı** -dağıtımın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komut `az account set -s [subscription name]` istenen aboneliğe geçiş yapın
* **--set** -dağıtımda bir özelliği güncelleştirin. Aşağıdaki özellikleri güncelleştirebilirsiniz:
  * targetCondition-örneğin `targetCondition=tags.location.state='Oregon'`
  * etikete 
  * Priority


## <a name="delete-a-deployment"></a>Bir dağıtımı silme

Bir dağıtımı sildiğinizde, tüm cihazlar bir sonraki en yüksek öncelikli dağıtımı alırlar. Cihazlarınız başka bir dağıtımın hedef koşulunu karşılamıyorsa, dağıtım silindiğinde modüller kaldırılmaz. 

Bir dağıtımı silmek için [az IoT Edge Deployment Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) komutunu kullanın:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

Dağıtım silme komutu aşağıdaki parametreleri alır: 
* **--Deployment-ID** -IoT Hub 'ında bulunan dağıtımın adı.
* **--hub-adı** -dağıtımın bulunduğu IoT Hub 'ının adı. Hub geçerli abonelikte olmalıdır. Komut `az account set -s [subscription name]` istenen aboneliğe geçiş yapın

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge cihazlara modül dağıtma](module-deployment-monitoring.md)hakkında daha fazla bilgi edinin.

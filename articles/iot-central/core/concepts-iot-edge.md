---
title: Azure IoT Edge ve Azure IoT Central | Microsoft Docs
description: Azure IoT Edge IoT Central bir uygulamayla nasıl kullanacağınızı anlayın.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: e0f3464420c5cb429f780999bf5983b2ab142567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608640"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Edge cihazlarını Azure IoT Central uygulamasına bağlama

*Bu makale, çözüm oluşturucular ve cihaz geliştiricileri için geçerlidir.*

Azure IoT Edge, bulut analizlerini ve özel iş mantığını cihazlara taşırken, kuruluşunuzun veri yönetimi yerine iş öngörülerine odaklanmasını sağlar. İş mantığınızı standart kapsayıcılara paketleyerek IoT çözümünüzü ölçeklendirin, bu kapsayıcıları cihazlarınıza dağıtın ve buluttan izleyin.

Bu makalede şunları açıklanmaktadır:

* IoT Edge cihazların bir IoT Central uygulamasına bağlanması.
* IoT Edge cihazlarınızı yönetmek için IoT Central kullanma.

IoT Edge hakkında daha fazla bilgi edinmek için bkz. [Azure IoT Edge nedir?](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

IoT Edge üç bileşenden oluşur:

* *IoT Edge modüller* , Azure Hizmetleri, iş ortağı hizmetleri veya kendi kodunuzla çalışan kapsayıcılardır. Modüller IoT Edge cihazlara dağıtılır ve bu cihazlarda yerel olarak çalıştırılır. Daha fazla bilgi için bkz. [Azure IoT Edge modüllerini anlama](../../iot-edge/iot-edge-modules.md).
* *IoT Edge çalışma zamanı* her bir IoT Edge cihazında çalışır ve her cihaza dağıtılan modülleri yönetir. Çalışma zamanı iki IoT Edge modülünden oluşur: *IoT Edge Aracısı* ve *IoT Edge hub 'ı*. Daha fazla bilgi edinmek için bkz. [Azure IoT Edge çalışma zamanını ve mimarisini anlayın](../../iot-edge/iot-edge-runtime.md).
* *Bulut tabanlı bir arabirim* , IoT Edge cihazları uzaktan izlemenize ve yönetmenize olanak sağlar. IoT Central, bulut arabirimine bir örnektir.

IoT Edge bir cihaz şu olabilir:

* Modüllerden oluşan tek başına bir cihaz.
* Bir *ağ geçidi cihazı* ile bağlantı aşağı akış cihazları.

## <a name="iot-edge-as-a-gateway"></a>Ağ geçidi olarak IoT Edge

IoT Edge cihaz, ağdaki diğer aşağı akış cihazları ve IoT Central uygulamanız arasında bağlantı sağlayan bir ağ geçidi olarak çalışabilir.

İki ağ geçidi deseni vardır:

* *Saydam ağ geçidi* modelinde IoT Edge hub modülü IoT Central gibi davranır ve IoT Central kayıtlı cihazlardan gelen bağlantıları işler. İletiler, aralarında ağ geçidi olmadığı gibi, aşağı akış cihazlarından IoT Central geçer.

* *Çeviri ağ geçidi* modelinde, kendi başına IoT Central bağlanamadıkları cihazlar bunun yerine özel bir IoT Edge modülüne bağlanır. IoT Edge cihazdaki modül gelen aşağı akış cihaz iletilerini işler ve sonra bunları IoT Central iletir.

Saydam ve çeviri ağ geçidi desenleri birbirini dışlanmamış. Tek bir IoT Edge cihaz hem saydam bir ağ geçidi hem de çeviri ağ geçidi olarak çalışabilir.

IoT Edge ağ geçidi desenleri hakkında daha fazla bilgi için bkz. [bir IoT Edge cihazının ağ geçidi olarak nasıl kullanılabileceği](../../iot-edge/iot-edge-as-gateway.md).

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Ağ geçidi ve modüllerle aşağı akış cihaz ilişkileri

Aşağı akış cihazları, *IoT Edge hub*  modülü aracılığıyla bir IoT Edge Gateway cihazına bağlanabilir. Bu senaryoda, IoT Edge cihaz saydam bir ağ geçididir:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Saydam ağ geçidi diyagramı" border="false":::

Aşağı akış cihazları, özel bir modül aracılığıyla bir IoT Edge Gateway cihazına da bağlanabilir. Aşağıdaki senaryoda, aşağı akış cihazları bir *Modbus* özel modülü aracılığıyla bağlanır. Bu senaryoda, IoT Edge cihaz bir çeviri ağ geçididir:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Özel modül bağlantısının diyagramı" border="false":::

Aşağıdaki diyagramda her iki tür modül aracılığıyla bir IoT Edge ağ geçidi cihazına yönelik bağlantılar gösterilmektedir. Bu senaryoda IoT Edge cihaz hem saydam hem de çeviri ağ geçididir:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Her iki bağlantı modülünü kullanarak bağlanma diyagramı" border="false":::

Aşağı akış cihazları, birden çok özel modülle bir IoT Edge ağ geçidi cihazına bağlanabilir. Aşağıdaki diyagramda, bir Modbus özel modülü, uyumlu olmayan özel bir modül ve *IoT Edge hub*  modülü aracılığıyla bağlanan aşağı akış cihazları gösterilmektedir:

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Birden çok özel modül kullanılarak bağlantı diyagramı" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge cihazlar ve IoT Central

IoT Edge cihazlar, IoT Central kimlik doğrulamak için *paylaşılan erişim imza* belirteçlerini veya X. 509.952 sertifikalarını kullanabilir. IoT Edge cihazlarınızı ilk kez bağlanmadan önce IoT Central el ile kaydedebilir veya kayıt işlemini işlemek için cihaz sağlama hizmetini kullanabilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure IoT Central 'ye bağlanma](concepts-get-connected.md).

IoT Central cihaz [şablonlarını](concepts-device-templates.md) kullanarak IoT Central bir cihazla nasıl etkileşime gireceğini tanımlar. Örneğin, bir cihaz şablonu şunları belirtir:

* Bir cihazın gönderdiği telemetri ve özellik türleri, IoT Central onları yorumlayabilmesi ve görselleştirmeler oluşturabilmesini sağlayacak.
* Bir cihazın yanıt verdiği komutlar, IoT Central komutları çağırmak için kullanılacak bir operatör için Kullanıcı arabirimi gösterebilir.

IoT Edge cihaz telemetri gönderebilir, özellik değerlerini eşitleyebilir ve komutlara standart bir cihazla aynı şekilde yanıt verebilir. Bu nedenle, bir IoT Edge cihazının IoT Central bir cihaz şablonuna ihtiyacı vardır.

### <a name="iot-edge-device-templates"></a>Cihaz şablonlarını IoT Edge

Cihaz şablonları IoT Central cihazların yeteneklerini anlatmak için modeller kullanır. Aşağıdaki diyagramda bir IoT Edge cihazının modelinin yapısı gösterilmektedir:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="IoT Central bağlı IoT Edge cihaz modelinin yapısı" border="false":::

IoT Edge bir cihazı aşağıdaki şekilde modeller IoT Central:

* Her IoT Edge cihaz şablonunda bir yetenek modeli vardır.
* Dağıtım bildiriminde listelenen her özel modül için bir modül yetenek modeli oluşturulur.
* Her modül yetenek modeli ve bir cihaz modeli arasında bir ilişki oluşturulur.
* Modül yetenek modeli bir veya daha fazla modül arabirimini uygular.
* Her modül arabirimi telemetri, Özellikler ve komutlar içerir.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge dağıtım bildirimleri ve IoT Central cihaz şablonları

IoT Edge, iş mantığını modül biçiminde dağıtır ve yönetirsiniz. IoT Edge modüller, IoT Edge tarafından yönetilen en küçük hesaplama birimidir ve Azure Stream Analytics gibi Azure hizmetlerini ya da çözümünüze özgü kodu içerebilir.

IoT Edge *dağıtım bildiriminde* , cihaza dağıtılacak IoT Edge modülleri ve bunların nasıl yapılandırılacağı listelenmektedir. Daha fazla bilgi edinmek için bkz. [IoT Edge modül dağıtmayı ve yolları oluşturmayı öğrenin](../../iot-edge/module-composition.md).

Azure IoT Central 'de, IoT Edge cihaz için bir cihaz şablonu oluşturmak üzere bir dağıtım bildirimi içeri aktarırsınız.

Aşağıdaki kod parçacığı bir örnek IoT Edge dağıtım bildirimi gösterir:

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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
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
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

Önceki kod parçacığında şunları görebilirsiniz:

* Üç modül vardır. *IoT Edge Aracısı* ve *IoT Edge hub* sistem modülleri her dağıtım bildiriminde bulunur. Özel **SimulatedTemperatureSensor** modülü.
* Ortak modül görüntüleri, bağlanmak için herhangi bir kimlik bilgisi gerektirmeyen bir Azure Container Registry deposundan çekilir. Özel modül görüntüleri için, `registryCredentials` *IoT Edge Aracısı* modülü ayarında kullanılacak kapsayıcı kayıt defteri kimlik bilgilerini ayarlayın.
* Özel **SimulatedTemperatureSensor** modülünün iki özelliği `"SendData": true` ve `"SendInterval": 10` .

Bu dağıtım bildirimini bir IoT Central uygulamasına aktardığınızda, aşağıdaki cihaz şablonunu oluşturur:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Dağıtım bildiriminden oluşturulan cihaz şablonunu gösteren ekran görüntüsü.":::

Önceki ekran görüntüsünde şunları görebilirsiniz:

* **SimulatedTemperatureSensor** adlı bir modül. *IoT Edge Aracısı* ve *IoT Edge hub* sistem modülleri şablonda görünmüyor.
* **SendData** ve **sendınterval** adlı iki yazılabilir özelliği içeren **Yönetim** adlı bir arabirim.

Dağıtım bildirimi, **SimulatedTemperatureSensor** modülünün gönderdiği Telemetriyi veya yanıt verdiği komutları içermez. Bu tanımları yayımlamadan önce cihaz şablonuna el ile ekleyin.

Daha fazla bilgi edinmek için bkz. [öğretici: Azure IoT Central uygulamanıza Azure IoT Edge bir cihaz ekleme](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Dağıtım bildirimini güncelleştirme

Cihaz şablonunun yeni bir [sürümünü](howto-version-device-template.md) oluşturursanız dağıtım bildirimini yeni bir sürümle değiştirebilirsiniz:

Dağıtım bildirimini değiştirdiğinizde, tüm bağlı IoT Edge cihazları yeni bildirimi indirir ve modüllerini güncelleştirir. Ancak IoT Central, cihaz şablonundaki arabirimleri modül yapılandırmasındaki değişikliklerle güncelleştirmez. Örneğin, önceki kod parçacığında gösterilen bildirimi aşağıdaki bildirimle değiştirirseniz, cihaz şablonundaki **Yönetim** arabirimindeki **sendunits** özelliğini otomatik olarak görmezsiniz. Yeni özelliği, IoT Central tanımak üzere **Yönetim** arabirimine el ile ekleyin:

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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
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
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>IoT Edge çalışma zamanını dağıtma

IoT Edge çalışma zamanını nerede çalıştırabileceğinizi öğrenmek için bkz. [Azure IoT Edge desteklenen sistemler](../../iot-edge/support.md).

IoT Edge çalışma zamanını aşağıdaki ortamlara da yükleyebilirsiniz:

* [Linux için Azure IoT Edge yükleme veya kaldırma](../../iot-edge/how-to-install-iot-edge.md)
* [Windows cihazı üzerinde Linux için Azure IoT Edge'i yükleme ve sağlama (Önizleme)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Azure 'da Ubuntu sanal makinelerinde Azure IoT Edge çalıştırma](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>IoT Edge ağ geçidi cihazları

Bir IoT Edge cihazını ağ geçidi cihazı olacak şekilde seçtiyseniz, ağ geçidi cihazına bağlanmak istediğiniz cihazların cihaz modellerine aşağı akış ilişkileri ekleyebilirsiniz.

Daha fazla bilgi edinmek için bkz. [IoT Edge saydam bir ağ geçidi aracılığıyla cihazları bağlama](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="next-steps"></a>Sonraki adımlar

Bir cihaz geliştiricisiyseniz, önerilen bir sonraki adım [kendi IoT Edge modüllerinizi geliştirmeyi](../../iot-edge/module-development.md)öğrenmektir.

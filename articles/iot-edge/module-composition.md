---
title: Dağıtım bildirimleriyle modül & yolları dağıtma-Azure IoT Edge
description: Dağıtım bildiriminin hangi modüllerin dağıtılacağını, nasıl dağıtılacağını ve aralarında ileti yolları nasıl oluşturulacağını öğrenin.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2e3f1891a786751365a0bea58097e03bd41f85bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489938"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>IoT Edge'de modülleri dağıtmayı ve yolları oluşturmayı öğrenin

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Her IoT Edge cihaz en az iki modülle çalışır: IoT Edge çalışma zamanının parçası olan $edgeAgent ve $edgeHub. IoT Edge cihaz, herhangi bir sayıda işlem için birden fazla ek modül çalıştırabilir. Cihaza hangi modüllerin yükleneceğini ve birlikte çalışmak üzere nasıl yapılandırılacağını bildirmek için bir dağıtım bildirimi kullanın.

*Dağıtım bildirimi* şunları açıklayan bir JSON belgesidir:

* Üç bileşen içeren **IoT Edge aracı** modülü ikizi:
  * Cihazda çalışan her modülün kapsayıcı görüntüsü.
  * Modül görüntülerini içeren özel kapsayıcı kayıt defterlerine erişim için kimlik bilgileri.
  * Her modülün oluşturulması ve yönetilmesi için yönergeler.
* İletilerin modüller arasında nasıl akabildiğini ve IoT Hub için **IoT Edge hub** modülü ikizi.
* Ek modül TWINS 'in istenen özellikleri (isteğe bağlı).

Tüm IoT Edge cihazların bir dağıtım bildirimiyle yapılandırılması gerekir. Yeni yüklenen IoT Edge çalışma zamanı, geçerli bir bildirimle yapılandırılana kadar bir hata kodu bildirir.

Azure IoT Edge öğreticilerde, Azure IoT Edge portalındaki bir sihirbaza giderek bir dağıtım bildirimi oluşturacaksınız. Ayrıca, REST veya IoT Hub hizmeti SDK 'sını kullanarak bir dağıtım bildirimini programlı bir şekilde uygulayabilirsiniz. Daha fazla bilgi için bkz. [IoT Edge dağıtımlarını anlama](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Dağıtım bildirimi oluşturma

Yüksek düzeyde bir dağıtım bildirimi, istenen özellikleriyle yapılandırılmış bir modül TWINS listesidir. Dağıtım bildirimi, hangi modüllerin yükleneceğini ve bunların nasıl yapılandırılacağını IoT Edge bir cihaza (veya bir cihaz grubuna) bildirir. Dağıtım bildirimleri, her modül için *istenen özellikleri* içerir ikizi. IoT Edge cihazlar her bir modülün *bildirilen özelliklerini* geri bildirir.

Her dağıtım bildiriminde iki modül gereklidir: `$edgeAgent` , ve `$edgeHub` . Bu modüller, IoT Edge cihazını ve üzerinde çalışan modülleri yöneten IoT Edge çalışma zamanının bir parçasıdır. Bu modüller hakkında daha fazla bilgi için bkz. [IoT Edge çalışma zamanını ve mimarisini anlayın](iot-edge-runtime.md).

İki çalışma zamanı modülüne ek olarak, bir IoT Edge cihazında çalıştırmak için kendi kendinize ait 50 modül ekleyebilirsiniz.

Yalnızca IoT Edge çalışma zamanını (edgeAgent ve edgeHub) içeren bir dağıtım bildirimi geçerlidir.

Dağıtım bildirimleri bu yapıyı izler:

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
        // includes the routing information between modules, and to IoT Hub
      }
    },
    "module1": {  // optional
      "properties.desired": {
        // desired properties of module1
      }
    },
    "module2": {  // optional
      "properties.desired": {
        // desired properties of module2
      }
    }
  }
}
```

## <a name="configure-modules"></a>Modülleri yapılandırma

IoT Edge çalışma zamanının dağıtımınıza modülleri nasıl yüklediğini tanımlayın. IoT Edge Aracısı, bir IoT Edge cihazının yükleme, güncelleştirme ve durum raporlamasını yöneten çalışma zamanı bileşenidir. Bu nedenle, ikizi Module $edgeAgent tüm modüller için yapılandırma ve yönetim bilgilerini içerir. Bu bilgiler IoT Edge aracısının kendisi için yapılandırma parametrelerini içerir.

$EdgeAgent Özellikler bu yapıyı izler:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            // configuration and management details
          },
          "edgeHub": {
            // configuration and management details
          }
        },
        "modules": {
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge Agent şema sürümü 1,1, IoT Edge Version 1.0.10 ile birlikte yayımlanmıştır ve modül başlangıç sırasını sunar. 1,1 şema sürümü, sürüm 1.0.10 veya üstünü çalıştıran tüm IoT Edge dağıtımlar için önerilir.

### <a name="module-configuration-and-management"></a>Modül yapılandırması ve yönetimi

IoT Edge Agent istenen özellikler listesi, hangi modüllerin bir IoT Edge cihazına dağıtıldığını ve bunların nasıl yapılandırılması ve yönetilmesi gerektiğini tanımladığınız yerdir.

Dahil edilen veya dahil olması gereken özelliklerin tamamı listesi için bkz. [IoT Edge Aracısı ve IoT Edge hub özellikleri](module-edgeagent-edgehub.md).

Örnek:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Her modülün modül **görüntüsünü**, bir kapsayıcı kayıt defterinde kapsayıcı görüntüsünün adresini ve başlangıçta görüntüyü yapılandırmak için herhangi bir **createOptions** içeren bir **Ayarlar** özelliği vardır. Daha fazla bilgi için bkz. [IoT Edge modüller için kapsayıcı oluşturma seçeneklerini yapılandırma](how-to-use-create-options.md).

EdgeHub modülü ve özel modüller Ayrıca IoT Edge aracısına nasıl yönetileceğini söyleyen üç özelliğe sahiptir:

* **Durum**: ilk dağıtıldığında modülün çalışıyor veya durdurulmuş olması gerekip gerekmediğini belirtir. Gereklidir.
* **RestartPolicy**: IoT Edge aracısının durması durumunda modülü yeniden başlatması gerekir. Gereklidir.
* **Startuporder**: *IoT Edge Version 1.0.10 içinde tanıtılmıştır.* İlk dağıtıldığında IoT Edge aracısının modülleri başlatması gereken sıralama. Sıra, başlangıç değeri 0 olan bir modülün ilk önce başlatıldığı ve ardından daha yüksek sayıların izlediği tamsayılar ile birlikte bildirilmiştir. EdgeAgent modülünün başlangıç değeri yok çünkü her zaman ilk olarak başlar. İsteğe bağlı.

  IoT Edge Aracısı, modülleri başlangıç değeri sırasıyla başlatır, ancak bir sonraki birine geçmeden önce her modülün bitmesini beklemez.

  Bazı modüller diğerlerine bağımlıysa, başlangıç sırası yararlı olur. Örneğin, diğer modüller başlatıldığında iletileri yönlendirmeye başlamaya başlamak için edgeHub modülünün önce başlamasını isteyebilirsiniz. Ya da veri gönderen modüllerden önce bir depolama modülü başlatmak isteyebilirsiniz. Ancak, diğer modüllerin başarısızlıklarını işlemek için her zaman modüllerinizi tasarlamanız gerekir. Bu, istedikleri zaman ve herhangi bir sayıda, durdurdukları ve yeniden başlatabilecekleri kapsayıcıların doğası olur.

## <a name="declare-routes"></a>Yolları bildir

IoT Edge hub, modüller, IoT Hub ve herhangi bir yaprak cihaz arasındaki iletişimi yönetir. Bu nedenle, $edgeHub Module ikizi, iletilerin bir dağıtım içinde nasıl geçtiğini bildiren *yollar* adlı istenen bir özelliği içerir. Aynı dağıtımda birden çok yol olabilir.

Yollar **$edgeHub** istenen özelliklerde aşağıdaki sözdizimi ile bildiriliyor:

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge hub şeması sürüm 1,1 IoT Edge sürüm 1.0.10 ile birlikte yayımlanmıştır ve yol önceliklendirmesini ve yaşam süresini sağlar. 1,1 şema sürümü, sürüm 1.0.10 veya üstünü çalıştıran tüm IoT Edge dağıtımlar için önerilir.

Her yol, iletilerin geldiği bir *kaynağa* ve iletilerin gideceği bir *havuza* ihtiyaç duyuyor. Bu *koşul* , iletileri filtrelemek için kullanabileceğiniz isteğe bağlı bir parçadır.

Öncelikle iletilerinin işlemesini sağlamak istediğiniz yollara *Öncelik* atayabilirsiniz. Bu özellik, yukarı akış bağlantısının zayıf veya sınırlı olduğu senaryolarda ve standart telemetri iletileri üzerinden öncelik verilmelidir önemli verileriniz olduğunda yararlıdır.

### <a name="source"></a>Kaynak

Kaynak, iletilerin nereden geldiği yeri belirtir. IoT Edge, modüller veya yaprak cihazlardan iletileri yönlendirebilir.

Modüller, IoT SDK 'larını kullanarak, kendi iletileri için ModuleClient sınıfını kullanarak belirli çıktı kuyrukları bildirebilir. Çıktı kuyrukları gerekli değildir, ancak birden çok yolu yönetmek için yararlıdır. Yaprak cihazlar, IoT Edge ağ geçidi cihazlarına ileti göndermek için IoT SDK 'larının DeviceClient sınıfını IoT Hub ileti gönderecek şekilde kullanabilir. Daha fazla bilgi için bkz. [Azure IoT Hub SDK 'Larını anlama ve kullanma](../iot-hub/iot-hub-devguide-sdks.md).

Kaynak özelliği aşağıdaki değerlerden herhangi biri olabilir:

| Kaynak | Açıklama |
| ------ | ----------- |
| `/*` | Tüm modülden veya yaprak cihazlardan gelen cihazdan buluta iletiler veya ikizi değişiklik bildirimleri |
| `/twinChangeNotifications` | Herhangi bir modülden veya yaprak cihazdan gelen herhangi bir ikizi değişikliği (bildirilen Özellikler) |
| `/messages/*` | Bir modül tarafından bir veya çıkış olmadan veya bir yaprak cihaz tarafından gönderilen cihazdan buluta ileti |
| `/messages/modules/*` | Bir modül tarafından bir veya çıkış olmadan gönderilen cihazdan buluta ileti |
| `/messages/modules/<moduleId>/*` | Bir veya daha fazla çıkış olmadan belirli bir modül tarafından gönderilen cihazdan buluta ileti |
| `/messages/modules/<moduleId>/outputs/*` | Bazı çıktılar aracılığıyla belirli bir modül tarafından gönderilen cihazdan buluta ileti |
| `/messages/modules/<moduleId>/outputs/<output>` | Belirli bir çıktı aracılığıyla belirli bir modül tarafından gönderilen cihazdan buluta ileti |

### <a name="condition"></a>Koşul

Koşul, bir yol bildiriminde isteğe bağlıdır. Kaynaktaki tüm iletileri havuza geçirmek istiyorsanız **WHERE** yan tümcesini tamamen bırakmalısınız. Ya da, koşulu karşılayan belirli iletileri veya ileti türlerini filtrelemek için [IoT Hub sorgu dilini](../iot-hub/iot-hub-devguide-routing-query-syntax.md) kullanabilirsiniz. IoT Edge yollar, ikizi etiketlerine veya özelliklerine göre iletileri filtrelemeyi desteklemez.

IoT Edge modüller arasında geçen iletiler, cihazlarınızla Azure IoT Hub arasında geçen iletilerle aynı şekilde biçimlendirilir. Tüm iletiler JSON olarak biçimlendirilir ve **SystemProperties**, **appproperties** ve **Body** parametreleri vardır.

Aşağıdaki söz dizimine sahip üç parametrenin herhangi biri etrafında sorgular oluşturabilirsiniz:

* Sistem Özellikleri: `$<propertyName>` veya `{$<propertyName>}`
* Uygulama özellikleri: `<propertyName>`
* Gövde özellikleri: `$body.<propertyName>`

İleti özellikleri için sorgu oluşturma hakkında örnekler için bkz. [cihazdan buluta ileti yolları sorgu ifadeleri](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

IoT Edge özgü bir örnek, bir yaprak cihazdan bir ağ geçidi cihazına ulaşan iletileri filtrelemek istediğinizde olur. Modüllerden gelen iletiler, **Connectionmoduleıd** adlı bir sistem özelliği içerir. Bu nedenle, yaprak cihazlardan iletileri doğrudan IoT Hub yönlendirmek istiyorsanız modül iletilerini dışlamak için aşağıdaki yolu kullanın:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Havuz

Havuz, iletilerin nereye gönderileceğini tanımlar. Yalnızca modüller ve IoT Hub ileti alabilir. İletiler diğer cihazlara yönlendirilemez. Havuz özelliğinde joker karakter seçeneği yok.

Sink özelliği aşağıdaki değerlerden herhangi biri olabilir:

| Havuz | Description |
| ---- | ----------- |
| `$upstream` | İletiyi IoT Hub gönder |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | İletiyi belirli bir modülün belirli bir girdisine gönder |

IoT Edge en az bir kez garanti sağlar. IoT Edge hub, bir yolun iletiyi havuza teslim etmesi durumunda iletileri yerel olarak depolar. Örneğin, IoT Edge hub IoT Hub bağlanamıyorsa veya hedef modül bağlı değilse.

IoT Edge hub, `storeAndForwardConfiguration.timeToLiveSecs` [IoT Edge hub istenen özelliklerinin](module-edgeagent-edgehub.md)özelliğinde belirtilen zamana kadar olan iletileri depolar.

### <a name="priority-and-time-to-live"></a>Öncelik ve yaşam süresi

Yollar yalnızca yolu tanımlayan bir dizeyle veya bir yol dizesi, bir öncelik tamsayı ve bir yaşam süresi tamsayı alan bir nesne olarak bildirilemez.

1. Seçenek:

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

Seçenek 2, IoT Edge hub şeması sürüm 1,1 ile IoT Edge Version 1.0.10 ile kullanıma sunulmuştur:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

**Öncelik** değerleri 0-9, dahil olabilir, burada 0 en yüksek önceliktir. İletiler, uç noktalarına göre sıralanır. Belirli bir uç noktayı hedefleyen tüm öncelik 0 iletileri, aynı uç noktayı hedefleyen herhangi bir öncelik 1 ileti işlenmeden ve satırın altına alınmadan önce işlenir. Aynı uç nokta için birden çok yol aynı önceliğe sahip ise, iletileri ilk kez sunulan bir temelde işlenir. Hiçbir öncelik belirtilmemişse, rota en düşük önceliğe atanır.

**Timetolivesecs** özelliği açıkça ayarlanmamışsa değeri IoT Edge hub 'ın **Storeandforwardconfiguration** öğesinden devralır. Değer herhangi bir pozitif tamsayı olabilir.

Öncelik sıralarının nasıl yönetildiği hakkında ayrıntılı bilgi için, [Rota önceliği ve yaşam süresi](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md)için başvuru sayfasına bakın.

## <a name="define-or-update-desired-properties"></a>İstenen özellikleri tanımlama veya güncelleştirme

Dağıtım bildirimi, IoT Edge cihazına dağıtılan her bir modül için istenen özellikleri belirtir. Dağıtım bildiriminde istenen özellikler, ikizi modülünde bulunan istenen özelliklerin üzerine yazar.

Dağıtım bildiriminde modülün ikizi istenen özelliklerini belirtmezseniz, IoT Hub modül ikizi herhangi bir şekilde değiştirmez. Bunun yerine, istenen özellikleri programlı bir şekilde ayarlayabilirsiniz.

Modül TWINS 'i değiştirmek için cihaz ikizlerini değiştirmenize olanak tanıyan mekanizmalar de kullanılır. Daha fazla bilgi için bkz. [module ikizi Geliştirici Kılavuzu](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Dağıtım bildirimi örneği

Aşağıdaki örnekte, geçerli bir dağıtım bildirimi belgesinin nasıl görünebilecekleri gösterilmektedir.

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
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
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
            "startupOrder": 2,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
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
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* $EdgeAgent ve $edgeHub dahil olması gereken özelliklerin tamamen listesi için bkz. [IoT Edge aracısının ve IoT Edge hub 'ının özellikleri](module-edgeagent-edgehub.md).

* IoT Edge modüllerinin nasıl kullanıldığını öğrendiğinize göre, [IoT Edge modülleri geliştirmeye yönelik gereksinimleri ve araçları anlayın](module-development.md).

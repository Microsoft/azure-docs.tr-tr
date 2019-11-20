---
title: Modüller ve rotalar dağıtım bildirimleri - Azure IOT Edge ile bildirmek | Microsoft Docs
description: Bir dağıtım bildirimi dağıtmak için hangi modülü nasıl bildirir, nasıl dağıtacağınızı ve bunlar arasında ileti yollarını nasıl oluşturulacağını öğrenin.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f275cca664733f19d3f3c5b52d168ffad01cadad
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839620"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>IOT Edge'de yollar oluşturmak ve modülleri dağıtma hakkında bilgi edinin

Her IOT Edge cihazı en az iki modüllerini çalıştırır: $edgeAgent hangilerinin $edgeHub, IOT Edge çalışma zamanı'nın bir parçasıdır. IoT Edge cihaz, herhangi bir sayıda işlem için birden fazla ek modül çalıştırabilir. Cihaza hangi modüllerin yükleneceğini ve birlikte çalışmak üzere nasıl yapılandırılacağını bildirmek için bir dağıtım bildirimi kullanın. 

*Dağıtım bildirimi* açıklayan bir JSON belgesidir:

* Üç bileşen içeren **IoT Edge aracı** modülü ikizi. 
  * Cihazda çalışan her modülün kapsayıcı görüntüsü.
  * Modül görüntülerini içeren özel kapsayıcı kayıt defterlerine erişim için kimlik bilgileri.
  * Her modülün oluşturulması ve yönetilmesi için yönergeler.
* **IOT Edge hub'ı** modüller arasında ve sonunda IOT hub'ına iletileri nasıl gerçekleştiğini içeren modül ikizi.
* İsteğe bağlı olarak, istenen özellikleri herhangi bir ek modül ikizlerini.

Tüm IOT Edge cihazları, bir dağıtım bildirimi ile yapılandırılması gerekir. Yeni yüklenen bir IOT Edge çalışma zamanı, geçerli bir bildirim ile yapılandırılana kadar bir hata kodu bildirir. 

Azure IOT Edge öğreticilerde, Azure IOT Edge Portalı'nda bir Sihirbazı aracılığıyla giderek bir dağıtım bildirimi oluşturun. Ayrıca, REST veya IOT Hub hizmeti SDK'sını kullanarak program aracılığıyla bir dağıtım bildirimi de uygulayabilirsiniz. Daha fazla bilgi için [IOT Edge dağıtımlarını anlama](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Bir dağıtım bildirimi oluşturma

Yüksek düzeyde, bir dağıtım bildirimi, istenen özellikleri ile yapılandırılan modül ikizlerini bir listesidir. Bir dağıtım bildirimini bir IOT Edge cihazı (veya cihaz grubunuz,) yüklemek için hangi modülü ve nasıl yapılandırılacakları söyler. Dağıtım bildirimleri içeren *istenen özellikleri* her modül ikizi için. IOT Edge cihazları rapor geri *bildirilen özellikler* her bir modül için. 

İki modül, her dağıtım bildiriminde gereklidir: `$edgeAgent`, ve `$edgeHub`. Bu modüller IOT Edge cihazı ve üzerini çalıştıran modüllerini yönetir IOT Edge çalışma zamanı'nın bir parçasıdır. Bu modül hakkında daha fazla bilgi için bkz. [IOT Edge çalışma zamanı ve mimarisini anlama](iot-edge-runtime.md).

İki çalışma zamanı modülü ek olarak, bir IOT Edge Cihazınızda çalıştırmak için kendi 20 modülleri kadar ekleyebilirsiniz. 

Yalnızca IOT Edge çalışma zamanı (edgeAgent ve edgeHub) içeren bir dağıtım bildirimi geçerli değil.

Dağıtım bildirimleri şu yapıyı izler:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
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
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Modülleri Yapılandır

IOT Edge çalışma zamanı modülleri, dağıtımınızdaki nasıl yükler tanımlayın. IOT Edge Aracısı'nı yükleme, güncelleştirmeleri ve durumu raporlama için bir IOT Edge cihazı yöneten çalışma zamanı bileşendir. Bu nedenle $edgeAgent modül ikizi tüm modüller için yapılandırma ve yönetim bilgilerini gerektirir. Bu bilgiler IoT Edge aracısının kendisi için yapılandırma parametrelerini içerir. 

Dahil edilebilir veya dahil olması gereken özelliklerin tamamen listesi için bkz. [IoT Edge Aracısı ve IoT Edge hub 'ının özellikleri](module-edgeagent-edgehub.md).

$EdgeAgent özellikleri şu yapıyı izler:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
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
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Yollar bildirme

IOT Edge hub'ı, modüller, IOT hub'ı ve tüm yaprak cihazlar arasındaki iletişimi yönetir. Bu nedenle, $edgeHub modül ikizi adlı istenen bir özellik içeren *yollar* iletileri dağıtımında nasıl geçirilir bildirir. Aynı dağıtım içinde birden çok yol olabilir.

İçinde tanımlanmış rotalar **$edgeHub** istenen özellikleri aşağıdaki sözdizimine sahip:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Her yol bir kaynak ve havuz gerekiyor ancak iletileri filtrelemek için kullanabileceğiniz isteğe bağlı bir durumdur. 


### <a name="source"></a>Kaynak

Kaynak iletileri nereden geldiğini belirtir. IoT Edge, modüller veya yaprak cihazlardan iletileri yönlendirebilir. 

Modüller, IoT SDK 'larını kullanarak, kendi iletileri için ModuleClient sınıfını kullanarak belirli çıktı kuyrukları bildirebilir. Çıktı kuyrukları gerekli değildir, ancak birden çok yolu yönetmek için yararlıdır. Yaprak cihazlar, IoT Edge ağ geçidi cihazlarına ileti göndermek için IoT SDK 'larının DeviceClient sınıfını IoT Hub ileti gönderecek şekilde kullanabilir. Daha fazla bilgi için bkz. [Azure IoT Hub SDK 'Larını anlama ve kullanma](../iot-hub/iot-hub-devguide-sdks.md).

Source özelliği aşağıdaki değerlerden biri olabilir:

| Kaynak | Açıklama |
| ------ | ----------- |
| `/*` | Tüm cihaz bulut iletilerini veya ikiz değişiklik bildirimlerini herhangi bir modül veya yaprak CİHAZDAN |
| `/twinChangeNotifications` | Herhangi bir modül veya yaprak CİHAZDAN gelen herhangi bir ikiz değişiklik (bildirilen Özellikler) |
| `/messages/*` | Bir modül tarafından bir veya çıkış olmadan veya bir yaprak cihaz tarafından gönderilen cihazdan buluta ileti |
| `/messages/modules/*` | Bazı veya hiç çıkış aracılığıyla bir modül tarafından gönderilen herhangi bir CİHAZDAN buluta ileti |
| `/messages/modules/<moduleId>/*` | Bazı veya hiç çıkış aracılığıyla belirli bir modül tarafından gönderilen herhangi bir CİHAZDAN buluta ileti |
| `/messages/modules/<moduleId>/outputs/*` | Bazı çıkış aracılığıyla belirli bir modül tarafından gönderilen herhangi bir CİHAZDAN buluta ileti |
| `/messages/modules/<moduleId>/outputs/<output>` | Belirli bir çıktısına aracılığıyla belirli bir modül tarafından gönderilen herhangi bir CİHAZDAN buluta ileti |

### <a name="condition"></a>Koşul
Koşul, bir rota bildiriminde isteğe bağlıdır. Kaynaktaki tüm iletileri havuza geçirmek istiyorsanız **WHERE** yan tümcesini tamamen bırakmalısınız. Ya da [IOT Hub sorgu dili](../iot-hub/iot-hub-devguide-routing-query-syntax.md) belirli iletileri veya koşulu karşılayan ileti türlerini filtrelemek için. IOT Edge yollar ikizi etiketleri veya özellikleri göre filtreleme iletilerini desteklemez. 

IOT edge'deki modüller arasında iletileri aynı şekilde Azure IOT Hub ve cihazlar arasında iletileri biçimlendirilir. Tüm iletileri JSON biçimindedir ve sahip **systemProperties**, **appProperties**, ve **gövdesi** parametreleri. 

Herhangi bir üç parametre aşağıdaki söz dizimi ile geçici sorgular oluşturabilirsiniz: 

* Sistem Özellikleri: `$<propertyName>` veya `{$<propertyName>}`
* Uygulama özellikleri: `<propertyName>`
* Gövde özellikleri: `$body.<propertyName>` 

İleti özellikleri için sorguları oluşturma hakkında daha fazla örnek için bkz: [yollar sorgu ifadelerinde CİHAZDAN buluta ileti](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Bir ağ geçidi cihazı bir yaprak CİHAZDAN gelen iletiler için filtreleme yapmak istediğinizde, IOT Edge için belirli bir örnektir. Modüllerden gelen iletileri içer adlı bir sistem özelliği **connectionModuleId**. Bu nedenle iletileri yönlendirmek için yaprak cihazlarıyla IOT Hub'ına doğrudan istiyorsanız, aşağıdaki yol modülü iletileri hariç tutmak için kullanın:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Havuz
Havuz iletilerinin nereye gönderildiğini tanımlar. Yalnızca modüller ve IOT Hub ileti alabilir. İletileri diğer cihazlara yönlendirilemiyor. Herhangi bir havuz özelliği joker seçeneği vardır. 

Havuz özelliği aşağıdaki değerlerden biri olabilir:

| Havuz | Açıklama |
| ---- | ----------- |
| `$upstream` | IOT Hub'ına ileti gönderin |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Belirli bir modül, belirli bir giriş iletisi gönder |

IOT Edge, en az bir kez garantileri sağlar. IoT Edge hub, bir yolun iletiyi havuza teslim etmesi durumunda iletileri yerel olarak depolar. Örneğin, IoT Edge hub IoT Hub bağlanamıyorsa veya hedef modül bağlı değilse.

IoT Edge hub, `storeAndForwardConfiguration.timeToLiveSecs` [IoT Edge hub istenen özelliklerinin](module-edgeagent-edgehub.md)özelliğinde belirtilen zamana kadar olan iletileri depolar.

## <a name="define-or-update-desired-properties"></a>İstenen özellikleri güncelleştirme veya tanımlama 

İstenen özellikleri IOT Edge cihazına dağıtılan her bir modül için dağıtım bildirimini belirtir. Dağıtım bildirimi içinde istenen özellikleri tüm istenen özellikleri şu anda modül ikizi üzerine yazın.

Modül ikizinin istenen özellikleri dağıtım bildiriminde belirtmezseniz, IOT hub'ı modül ikizi hiçbir şekilde değiştirmez. Bunun yerine, istenen özellikleri programlı olarak ayarlayabilirsiniz.

Cihaz ikizlerini değiştirmenize olanak tanıyan mekanizmalarının aynısını, modül ikizlerini değiştirmek için kullanılır. Daha fazla bilgi için [modül ikizi Geliştirici Kılavuzu](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Dağıtım bildirimi örneği

Aşağıdaki örnek, geçerli bir dağıtım bildirimi belge neye benzediğini gösterir.

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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
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
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
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
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* $EdgeAgent ve $edgeHub dahil olması gereken özelliklerin tamamen listesi için bkz. [IoT Edge aracısının ve IoT Edge hub 'ının özellikleri](module-edgeagent-edgehub.md).

* IOT Edge modüllerinin nasıl kullanıldığı, öğrendiğinize göre [IOT Edge modülleri geliştirmek için Araçlar ve gereksinimleri anlamak](module-development.md).

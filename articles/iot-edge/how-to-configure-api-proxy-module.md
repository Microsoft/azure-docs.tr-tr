---
title: API ara sunucu modülünü Yapılandırma-Azure IoT Edge | Microsoft Docs
description: IoT Edge ağ geçidi hiyerarşileri için API proxy modülünü özelleştirmeyi öğrenin.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: f55c3a1f699f8a087eb97eaba347a3f21c124cc9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307325"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Ağ Geçidi hiyerarşi senaryonuz için API proxy modülünü yapılandırma (Önizleme)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

API proxy modülü IoT Edge cihazların, bulut hizmetlerine doğrudan bağlantı yapmak yerine ağ geçitleri aracılığıyla HTTP istekleri göndermesini sağlar. Bu makalede, modülü ağ geçidi hiyerarşi gereksinimlerinizi destekleyecek şekilde özelleştirebilmeniz için yapılandırma seçeneklerini adım adım açıklar.

>[!NOTE]
>Bu özellik, Linux kapsayıcıları çalıştıran, genel önizlemede olan IoT Edge sürüm 1,2 gerektirir.

Bazı ağ mimarilerinde IoT Edge, ağ geçitlerinin arkasındaki cihazların buluta doğrudan erişimi olmaz. Bulut hizmetlerine bağlanmayı deneyen tüm modüller başarısız olur. API proxy modülü, modül bağlantılarını yeniden yönlendirerek, bunun yerine bir ağ geçidi hiyerarşisinin katmanları arasında gezinmek için bu yapılandırmadaki aşağı akış IoT Edge cihazları destekler. İstemcilerin tünel olmadan HTTPS üzerinden birden çok hizmet ile iletişim kurması için güvenli bir yol sağlar, ancak her katmandaki bağlantıları sonlandırarak. API proxy modülü, üst katmanda IoT Edge cihaza ulaşıncaya kadar bir ağ geçidi hiyerarşisindeki IoT Edge cihazları arasında bir proxy modülü işlevi görür. Bu noktada, üst katman IoT Edge cihazda çalışan hizmetler bu istekleri işler ve API proxy modülü, yerel hizmetlerden gelen tüm HTTP trafiğini tek bir bağlantı noktası aracılığıyla buluta sağlar.

API proxy modülü, alt katman cihazların kapsayıcı görüntülerini çekmesini veya depolama 'ya gönderme bloblarını sağlamak da dahil olmak üzere ağ geçidi hiyerarşileri için birçok senaryoyu etkinleştirebilir. Proxy kuralları yapılandırması, verilerin nasıl yönlendirildiğini tanımlar. Bu makalede, yaygın olarak kullanılan birkaç yapılandırma seçeneği ele alınmaktadır.

## <a name="deploy-the-proxy-module"></a>Proxy modülünü dağıtma

API proxy modülü Microsoft Container Registry (MCR) tarafından kullanılabilir: `mcr.microsoft.com/azureiotedge-api-proxy:1.0` .

API proxy modülünü doğrudan Azure Marketi 'nden de dağıtabilirsiniz: [IoT Edge API proxy 'si](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Proxy modülünü anlayın

API proxy modülü, verileri ağ katmanları aracılığıyla yönlendirmek için bir NGINX ters proxy kullanır. Bir ara sunucu modüle katıştırılır, bu, modül görüntüsünün ara sunucu yapılandırmasını desteklemesi gerektiği anlamına gelir. Örneğin, ara sunucu belirli bir bağlantı noktasını dinliyorsa modülün Bu bağlantı noktasının açık olması gerekir.

Ara sunucu, modüle gömülü bir varsayılan yapılandırma dosyası ile başlar. [İkizi modülünü](../iot-hub/iot-hub-devguide-module-twins.md)kullanarak buluttan modüle yeni bir yapılandırma geçirebilirsiniz. Ayrıca, dağıtım zamanında yapılandırma ayarlarını açmak veya kapatmak için ortam değişkenlerini kullanabilirsiniz.

Bu makalede, ilk olarak varsayılan yapılandırma dosyasında ve ortam değişkenlerinin ayarlarını etkinleştirmek için nasıl kullanılacağı ele alınmaktadır. Ardından, sonunda yapılandırma dosyasını özelleştirmeyi tartıştık.

### <a name="default-configuration"></a>Varsayılan yapılandırma

API proxy modülü, yaygın senaryoları destekleyen ve özelleştirmeye izin veren bir varsayılan yapılandırmayla birlikte gelir. Modülün ortam değişkenleri aracılığıyla varsayılan yapılandırmayı kontrol edebilirsiniz.

Şu anda varsayılan ortam değişkenleri şunlardır:

| Ortam değişkeni | Açıklama |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Güncelleştirmek istediğiniz tüm değişkenleri virgülle ayrılmış bir listede listeleyin. Bu adım yanlışlıkla yanlış yapılandırma ayarlarını değiştirmeyi engeller.
| `NGINX_DEFAULT_PORT` | NGINX proxy 'sinin dinlediği bağlantı noktasını değiştirir. Bu ortam değişkenini güncelleştirirseniz, seçtiğiniz bağlantı noktasının de modül dockerfile modülünde açık olduğundan ve dağıtım bildiriminde bir bağlantı noktası bağlaması olarak bildiriminin bulunduğundan emin olun.<br><br>Varsayılan değer 443 ' dir.<br><br>Azure Marketi 'nden dağıtıldığında, edgeHub modülüyle çakışmaları engellemek için varsayılan bağlantı noktası 8000 olarak güncelleştirilir. Daha fazla bilgi için bkz. [açık bağlantı noktalarını küçültün](#minimize-open-ports). |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Docker isteklerinin yönlendirileceği adres. En üst katman cihazında bu değişkeni, kayıt defteri modülünü işaret etmek üzere değiştirin.<br><br>Varsayılan ana ana bilgisayar adıdır. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob kayıt defteri isteklerinin yönlendirileceği adres. Üst katman cihazında bu değişkeni, BLOB depolama modülünü işaret etmek üzere değiştirin.<br><br>Varsayılan ana ana bilgisayar adıdır. |

## <a name="minimize-open-ports"></a>Açık bağlantı noktalarını Küçült

Açık bağlantı noktası sayısını en aza indirmek için, API proxy modülünün, edgeHub modülünü hedefleyen trafik dahil tüm HTTPS trafiğini (bağlantı noktası 443) aktarması gerekir. API proxy modülü, bağlantı noktası 443 ' deki tüm edgeHub trafiğini yeniden yönlendirmek için varsayılan olarak yapılandırılır.

Açık bağlantı noktalarını en aza indirmek üzere dağıtımınızı yapılandırmak için aşağıdaki adımları kullanın:

1. EdgeHub modülü ayarlarını 443 numaralı bağlantı noktasında bağlanmayacak şekilde güncelleştirin, aksi takdirde bağlantı noktası bağlama çakışmaları olur. Varsayılan olarak, edgeHub modülü 443, 5671 ve 8883 bağlantı noktalarına bağlanır. Bağlantı noktası 443 bağlamasını silin ve diğer iki yerde bırakın:

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. API proxy modülünü 443 numaralı bağlantı noktasına bağlanacak şekilde yapılandırın.

   1. **NGINX_DEFAULT_PORT** ortam değişkeninin değerini olarak ayarlayın `443` .
   1. 443 numaralı bağlantı noktasına bağlanacak kapsayıcı oluşturma seçeneklerini güncelleştirin.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Açık bağlantı noktalarını en aza indirmenize gerek yoksa, edgeHub modülünün bağlantı noktası 443 kullanmasına izin verebilir ve API proxy modülünü başka bir bağlantı noktasını dinlemek üzere yapılandırabilir. Örneğin, API proxy modülü, **NGINX_DEFAULT_PORT** ortam değişkeninin değerini olarak ayarlayıp bağlantı noktası `8000` 8000 için bir bağlantı noktası bağlaması oluşturarak 8000 numaralı bağlantı noktasını dinleyebilir.

## <a name="enable-container-image-download"></a>Kapsayıcı görüntüsü indirmeyi etkinleştir

API proxy modülü için yaygın olarak kullanılan bir kullanım örneği, daha düşük katmanlardaki IoT Edge cihazların kapsayıcı görüntülerini çekmesini etkinleştirmektir. Bu senaryo, buluttan kapsayıcı görüntülerini almak ve bunları üst katmanda önbelleğe almak için [Docker kayıt defteri modülünü](https://hub.docker.com/_/registry) kullanır. API proxy, tüm HTTPS isteklerini, en üst katmanda kayıt modülü tarafından sunulmanız için alt katmanlardan bir kapsayıcı görüntüsünü indirmek üzere geçirir.

Bu senaryo, bir görüntünün kapsayıcı kayıt defteri yerine, aşağı akış IoT Edge cihazların etki alanı adını ve `$upstream` ardından API proxy modülü bağlantı noktası numarasını işaret eder. Örneğin: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Bu kullanım örneği, [ağ geçitlerini kullanarak IoT Edge cihazları hiyerarşisi oluşturma öğreticisinde](tutorial-nested-iot-edge.md)gösterilmiştir.

**Üst katmanda** aşağıdaki modülleri yapılandırın:

* Docker kayıt defteri modülü
  * Modülü *kayıt defteri* gibi hatırlayabileceğiniz bir adla yapılandırın ve istekleri almak için modüldeki bir bağlantı noktası kullanıma sunun.
  * Modülü kapsayıcı kayıt defteriyle eşlenecek şekilde yapılandırın.
* API proxy modülü
  * Aşağıdaki ortam değişkenlerini yapılandırın:

    | Name | Değer |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | Kayıt defteri modül adı ve açık bağlantı noktası. Örneğin, `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | NGINX proxy 'sinin, aşağı akış cihazlarındaki istekler için dinlediği bağlantı noktası. Örneğin, `8000`. |

  * Aşağıdaki createOptions 'ı yapılandırın:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Aşağıdaki modülü bu senaryo için **daha düşük bir katmanda** yapılandırın:

* API proxy modülü
  * Aşağıdaki ortam değişkenlerini yapılandırın:

    | Name | Değer |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | NGINX proxy 'sinin, aşağı akış cihazlarındaki istekler için dinlediği bağlantı noktası. Örneğin, `8000`. |

  * Aşağıdaki createOptions 'ı yapılandırın:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Blob yüklemeyi etkinleştir

API proxy modülü için başka bir kullanım örneği, daha düşük katmanlardaki IoT Edge cihazların blob 'ları karşıya yüklemesine olanak tanır. Bu kullanım örneği, modül günlüklerini karşıya yükleme veya destek paketini karşıya yükleme gibi alt katman cihazlarda sorun giderme işlevselliği sunar.

Bu senaryo, blob oluşturmayı ve karşıya yüklemeyi işlemek için üst katmanda [IoT Edge modülündeki Azure Blob depolama alanını](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) kullanır.

**Üst katmanda** aşağıdaki modülleri yapılandırın:

* IoT Edge modülündeki bir Azure Blob depolama alanı.
* API proxy modülü
  * Aşağıdaki ortam değişkenlerini yapılandırın:

    | Name | Değer |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob Storage modül adı ve açık bağlantı noktası. Örneğin, `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | NGINX proxy 'sinin, aşağı akış cihazlarındaki istekler için dinlediği bağlantı noktası. Örneğin, `8000`. |

  * Aşağıdaki createOptions 'ı yapılandırın:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Aşağıdaki modülü bu senaryo için **daha düşük bir katmanda** yapılandırın:

* API proxy modülü
  * Aşağıdaki ortam değişkenlerini yapılandırın:

    | Name | Değer |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | NGINX proxy 'sinin, aşağı akış cihazlarındaki istekler için dinlediği bağlantı noktası. Örneğin, `8000`. |

  * Aşağıdaki createOptions 'ı yapılandırın:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Destek paketini veya günlük dosyasını üst katmanda bulunan BLOB depolama modülüne yüklemek için aşağıdaki adımları kullanın:

1. Azure Depolama Gezgini veya REST API 'Lerini kullanarak bir blob kapsayıcısı oluşturun. Daha fazla bilgi için bkz. [IoT Edge Azure Blob Storage ile verileri kenarda depolama](how-to-store-data-blob.md).
1. [IoT Edge dağıtımlarından günlükleri alma](how-to-retrieve-iot-edge-logs.md)bölümündeki adımlara göre bir günlük veya destek paketi karşıya yüklemesi isteyin, ancak `$upstream` BLOB depolama modülü adresinin yerine etki alanı adını ve açık ara sunucu bağlantı noktasını kullanın. Örnek:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>Ara sunucu yapılandırmasını düzenleme

Varsayılan yapılandırma dosyası, API proxy modülüne katıştırılır, ancak ikizi modülünü kullanarak buluta yeni bir yapılandırma geçirebilirsiniz.

Kendi yapılandırmanızı yazdığınızda, dağıtım başına ayarları ayarlamak için ortamı kullanmaya devam edebilirsiniz. Aşağıdaki sözdizimini kullanın:

* `${MY_ENVIRONMENT_VARIABLE}`Bir ortam değişkeninin değerini almak için kullanın.
* Bir ortam değişkeninin değerine göre ayarları açmak veya devre dışı bırakmak için koşullu deyimler kullanın:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

API proxy modülü bir ara sunucu yapılandırmasını ayrıştırdığında, önce içinde listelenen tüm ortam değişkenlerini `PROXY_CONFIG_ENV_VAR_LIST` değiştirme kullanılarak kendi belirtilen değerleriyle değiştirir. Ardından, ve çifti arasındaki her şey `#if_tag` `#endif_tag` değiştirilmiştir. Modül daha sonra, NGINX ters proxy 'sine ayrıştırılmış yapılandırma sağlar.

Ara sunucu yapılandırmasını dinamik olarak güncelleştirmek için aşağıdaki adımları kullanın:

1. Yapılandırma dosyanızı yazın. Bu varsayılan şablonu, başvuru olarak kullanabilirsiniz: [nginx_default_config. conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Yapılandırma dosyasının metnini kopyalayın ve Base64 'e dönüştürün.
1. Kodlanmış yapılandırma dosyasını `proxy_config` module ikizi istenen özelliğinin değeri olarak yapıştırın.

   ![Kodlanmış yapılandırma dosyasını proxy_config özelliğinin değeri olarak Yapıştır](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Sonraki adımlar

Bir [aşağı akış IoT Edge cihazını bir Azure IoT Edge ağ geçidine bağlamak](how-to-connect-downstream-iot-edge-device.md)için API proxy modülünü kullanın.
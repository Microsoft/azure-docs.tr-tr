---
title: Microsoft bağlı önbelleği, giden kimliği doğrulanmamış ara sunucu içeren iki düzey iç içe Azure IoT Edge Ağ | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft bağlı önbellek iki düzey iç içe geçmiş Azure IoT Edge ağ geçidi, giden kimliği doğrulanmamış proxy öğreticisi
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811895"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Microsoft bağlı önbellek önizleme dağıtım senaryosu örneği: giden kimliği doğrulanmamış proxy ile Iki düzey iç içe Azure IoT Edge Ağ Geçidi

Aşağıdaki diyagramda, bir Azure IoT Edge ağ geçidinin CDN kaynaklarına doğrudan erişimi olan ve başka bir Azure IoT Edge ağ geçidi için üst öğe olarak davrandığı senaryo açıklanmaktadır. Alt IoT Edge ağ geçidi, Raspberry Pi gibi bir Azure IoT yaprak cihazının üst öğesi olarak hareket eder. Hem Azure IoT Edge alt hem de Azure IoT cihazı Internet yalıtılmış. Aşağıdaki örnekte, Azure IoT Edge ağ geçitlerinin iki düzeyi için yapılandırma gösterilmektedir, ancak Microsoft bağlı önbelleğinin destekleyeceği yukarı akış ana bilgisayarlarının derinliğine yönelik bir sınır yoktur. Microsoft bağlı önbellek kapsayıcısı, önceki örneklerden oluşturma seçeneklerinde fark yoktur.

Azure IoT Edge ağ geçitlerinin katmanlı dağıtımlarını yapılandırma hakkında daha fazla bilgi için bkz. Şirket [yönündeki bağlantı IoT Edge cihazları-Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) . Ayrıca Azure IoT Edge, Microsoft bağlı önbelleği ve özel modüller dağıtıldığında tüm modüllerin aynı kapsayıcı kayıt defterinde bulunması gerektiğini unutmayın.

>[!Note]
>Azure IoT Edge, Microsoft bağlı önbelleği ve özel modüller dağıtıldığında, tüm modüller aynı kapsayıcı kayıt defterinde bulunmalıdır.

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft bağlı önbellek Iç Içe" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Üst ağ geçidi yapılandırması
1. Microsoft bağlı önbellek modülünü Azure IoT Hub 'de Azure IoT Edge Gateway cihaz dağıtımınıza ekleyin (modülün nasıl alınacağı hakkında daha fazla bilgi için, bkz. [bağlantı kesilen cihazlar Için destek](connected-cache-disconnected-device-update.md) ).
2. Dağıtım için ortam değişkenlerini ekleyin. Aşağıda ortam değişkenlerinin bir örneği verilmiştir.

    **Ortam değişkenleri**

    | Name                          | Değer                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Bkz. [ortam değişkeni](connected-cache-configure.md) açıklamaları |
    | CUSTOMER_ID                   | Bkz. [ortam değişkeni](connected-cache-configure.md) açıklamaları |
    | CUSTOMER_KEY                  | Bkz. [ortam değişkeni](connected-cache-configure.md) açıklamaları |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |

3. Dağıtım için kapsayıcı oluşturma seçeneklerini ekleyin. Yukarıdaki örnekteki MCC kapsayıcısı oluşturma seçeneklerinde fark yoktur. Kapsayıcı oluşturma seçeneklerine bir örnek aşağıda verilmiştir.

### <a name="container-create-options"></a>Kapsayıcı oluşturma seçenekleri

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="child-gateway-configuration"></a>Alt ağ geçidi yapılandırması

>[!Note]
>Kendi özel kayıt defterinizde yapılandırmanızda kullanılan kapsayıcıları çoğaltdıysanız, modül dağıtımınızdaki config. TOML ayarları ve çalışma zamanı ayarları üzerinde bir değişiklik olması gerekir. Daha fazla bilgi için, daha fazla ayrıntı için [aşağı akış IoT Edge cihazlara bağlanma-Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices) konusuna bakın.


1. Edge aracısının görüntü yolunu aşağıdaki örnekte gösterildiği gibi değiştirin:

    ```markdown
    [agent]
    name = "edgeAgent"
    type = "docker"
    env = {}
    [agent.config]
    image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
    auth = {}
    ```
2. Azure IoT Edge dağıtımında Edge hub ve Edge Aracısı çalışma zamanı ayarlarını şu örnekte gösterildiği gibi değiştirin:
    
    * Edge hub 'ında görüntü alanına şunu girin ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Edge Aracısı altında, görüntü alanına şunu girin ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Microsoft bağlı önbellek modülünü Azure IoT Hub Azure IoT Edge Gateway cihaz dağıtımınıza ekleyin.

   * Modülünüzün adını seçin: ```ConnectedCache```
   * Görüntü URI 'sini değiştirin: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Ana dağıtımda kullanılan aynı ortam değişkenleri kümesini ve kapsayıcı oluşturma seçeneklerini ekleyin.
>[!Note]
>CACHE_NODE_ID benzersiz olmalıdır.  CUSTOMER_ID ve CUSTOMER_KEY değerleri üst öğeyle aynı olacaktır. (bkz. [Microsoft bağlı önbelleğini yapılandırma](connected-cache-configure.md)

Microsoft bağlı önbelleğinin düzgün şekilde çalıştığını doğrulamak için, modülü barındıran IoT Edge cihazın terminalinde aşağıdaki komutu yürütün veya ağdaki herhangi bir cihazı çalıştırın. \<Azure IoT Edge Gateway IP\>IoT Edge ağ geçidinizin IP adresi veya ana bilgisayar adı ile değiştirin. (Bu raporun görünürlüğü hakkında bilgi için ortam değişkeni ayrıntılarına bakın).

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
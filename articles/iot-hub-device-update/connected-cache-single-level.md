---
title: Microsoft bağlı önbellek önizleme dağıtım senaryosu örnekleri | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft bağlı önbellek önizleme dağıtım senaryosu örnek öğreticileri
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811733"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Microsoft bağlı önbellek önizleme dağıtım senaryosu örnekleri

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Tek düzey Azure IoT Edge ağ geçidi ara sunucu yok

Aşağıdaki diyagramda, CDN kaynaklarına doğrudan erişimi olan bir Azure IoT Edge ağ geçidinin bulunduğu senaryo ve Azure IoT Edge ağ geçidinin Internet yalıtılmış alt cihazları olan bir Raspberry PI gibi bir Azure IoT yaprak cihazının bulunduğu senaryo açıklanmaktadır. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Microsoft bağlı önbellek bağlantısı kesildi cihaz güncelleştirmesi" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft bağlı önbellek modülünü Azure IoT Hub 'de Azure IoT Edge Gateway cihaz dağıtımınıza ekleyin (modülün nasıl alınacağı hakkında daha fazla bilgi için, bkz. [bağlantı kesilen cihazlar Için destek](connected-cache-disconnected-device-update.md) ).
2. Dağıtım için ortam değişkenlerini ekleyin. Aşağıda ortam değişkenlerinin bir örneği verilmiştir.

    **Ortam değişkenleri**
    
    | Name                          | Değer                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Bkz. [ortam değişkeni](connected-cache-configure.md) açıklamaları |
    | CUSTOMER_ID                   | Bkz. [ortam değişkeni](connected-cache-configure.md) açıklamaları |
    | CUSTOMER_KEY                  | Bkz. [ortam değişkeni](connected-cache-configure.md) açıklamaları |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. Dağıtım için kapsayıcı oluşturma seçeneklerini ekleyin. Kapsayıcı oluşturma seçeneklerine bir örnek aşağıda verilmiştir.

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

Microsoft bağlı önbelleğinin düzgün şekilde çalıştığını doğrulamak için, modülü barındıran IoT Edge cihazın terminalinde aşağıdaki komutu yürütün veya ağdaki herhangi bir cihazı çalıştırın. \<Azure IoT Edge Gateway IP\>IoT Edge ağ geçidinizin IP adresi veya ana bilgisayar adı ile değiştirin. (Bu raporun görünürlüğü hakkında bilgi için ortam değişkeni ayrıntılarına bakın).

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Giden kimliği doğrulanmamış ara sunucu ile Tek düzey Azure IoT Edge Ağ Geçidi

Bu senaryoda, giden kimliği doğrulanmamış bir ara sunucu aracılığıyla CDN kaynaklarına erişimi olan bir Azure IoT Edge ağ geçidi vardır. Microsoft bağlı önbelleği, içeriği özel bir depodan önbelleğe almak için Yapılandırılıyor ve Özet raporu ağdaki herkese görünür hale getirilir. Aşağıda, ayarlanacak MCC ortam değişkenlerinin bir örneği verilmiştir.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft bağlı önbellek tek düzeyli ara sunucu" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Microsoft bağlı önbellek modülünü Azure IoT Hub Azure IoT Edge Gateway cihaz dağıtımınıza ekleyin.
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
    | UPSTREAM_PROXY                | Proxy sunucunuz IP 'niz veya FQDN                                          |

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

Microsoft bağlı önbelleğinin düzgün şekilde çalıştığını doğrulamak için, modülü barındıran Azure IoT Edge cihazın terminalinde aşağıdaki komutu yürütün veya ağdaki herhangi bir cihazı çalıştırın. \<Azure IoT Edge Gateway IP\>IoT Edge ağ geçidinizin IP adresi veya ana bilgisayar adı ile değiştirin. (Bu raporun görünürlüğü hakkında bilgi için ortam değişkeni ayrıntılarına bakın).

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```

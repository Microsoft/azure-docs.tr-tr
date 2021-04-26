---
title: Endüstriyel IoT yapılandırması için bir Azure IoT Edge içindeki Microsoft bağlı önbelleği | Microsoft Docs
description: Endüstriyel IoT yapılandırma öğreticisi için bir Azure IoT Edge içindeki Microsoft bağlı önbelleği
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 083c7bf6edc7da1fd617487e91b0a3848fb401fe
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811842"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Microsoft bağlı önbellek önizleme dağıtım senaryosu örneği: Endüstriyel IoT yapılandırması için bir Azure IoT Edge içindeki Microsoft bağlı önbelleği

Üretim ağları genellikle, önemli bir [ağ modeli](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) ( [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) ve [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) standartlarına dahil) takip eden hiyerarşik katmanlarda düzenlenir. Bu ağlarda yalnızca üst katmanın buluta bağlantısı vardır ve hiyerarşideki alt katmanlar yalnızca komşu Kuzey ve Güney katmanlarla iletişim kurabilir.

[Endüstriyel IoT için Azure IoT Edge](https://github.com/Azure-Samples/iot-edge-for-iiot)bu GitHub örneği şunları dağıtır:

* Azure 'da sanal ağ nedeniyle benzetim
* Endüstriyel varlıklar 
* Azure IoT Edge ağ geçitleri hiyerarşisi
  
Bu bileşenler, endüstriyel verileri elde etmek ve ağın güvenliğini tehlikeye atmadan buluta güvenli bir şekilde yüklemek için kullanılacaktır. Microsoft bağlı önbelleği, ISA 95 uyumlu ağ içindeki tüm düzeylerde içerik indirilmesini desteklemek üzere dağıtılabilir.

Microsoft bağlı önbellek dağıtımlarını bir ISA 95 uyumlu ağ içinde yapılandırmanın anahtarı, hem OT ara sunucusunu *hem* de yukarı akış konağını L3 IoT Edge ağ geçidinde yapılandırıyor.

1. Two-Level Iç Içe IoT Edge ağ geçidi örneğinde açıklandığı gibi, Microsoft bağlı önbellek dağıtımlarını L5 ve L4 düzeylerinde yapılandırın 
2. L3 IoT Edge ağ geçidinde dağıtım şunları belirtmelidir:
   
   * UPSTREAM_HOST-L3 Microsoft bağlı önbelleğinin içerik isteyeceğini belirten, L4 IoT Edge ağ geçidinin IP/FQDN 'si.
   * UPSTREAM_PROXY-IP/FQDN: OT proxy sunucusunun bağlantı noktası.

3. OT proxy 'si, allowlist öğesine L4 MCC FQDN/IP adresini eklemesi gerekir.

Microsoft bağlı önbelleğinin düzgün çalıştığını doğrulamak için, IoT Edge cihazın terminalinde aşağıdaki komutu yürütün, modülü veya ağdaki herhangi bir cihazı barındırın. \<Azure IoT Edge Gateway IP\>IoT Edge ağ geçidinizin IP adresi veya ana bilgisayar adı ile değiştirin. (Bu raporun görünürlüğü hakkında bilgi için ortam değişkeni ayrıntılarına bakın).

```bash
    wget http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
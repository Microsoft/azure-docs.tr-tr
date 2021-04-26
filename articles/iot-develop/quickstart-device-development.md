---
title: Azure IoT Embedded cihaz geliştirme hızlı başlangıç
description: Azure RTOS ve Azure IoT kullanılarak eklenmiş cihaz geliştirmenin nasıl yapılacağını gösteren bir hızlı başlangıç kılavuzu.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 536b9092a7bddd353b58874e7aa97291839bafcc
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589542"
---
# <a name="getting-started-with-azure-iot-embedded-device-development"></a>Azure IoT Embedded cihaz geliştirmeyle çalışmaya başlama

**Uygulama hedefi**: [katıştırılmış cihaz geliştirme](about-iot-develop.md#embedded-device-development)

Bu başlangıç kılavuzu, katıştırılmış cihazlarla ve Azure IoT ile çalışmaya başlama hakkında sizi gösteren bir dizi hızlı başlangıç içerir. 

Her hızlı başlangıçta aşağıdaki temel görevleri tamamlayabilirsiniz:
* C 'de belirli bir cihazı programlamaya yönelik bir yerleşik geliştirme araçları kümesi yükler
* Azure RTOS bileşenlerini ve örneklerini içeren bir görüntü oluşturun ve ardından bir cihazı Flash yapın
* Bir cihazı Azure IoT 'ye güvenle bağlama
* Cihaz telemetrisini görüntüleyin, özellikleri görüntüleyin ve buluttan cihaza Yöntemleri çağırın

## <a name="quickstarts"></a>Hızlı Başlangıçlar
Aşağıdaki öğreticiler Başlangıç Kılavuzu 'na eklenmiştir:

|Hızlı Başlangıç|Cihaz|
|---------------|-----|
|[ST mikro elektronik B-L475E-IOT01 Discovery Kit ile çalışmaya başlama](https://go.microsoft.com/fwlink/p/?linkid=2129536) |[ST mikro elektronik B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)|
|[ST mikro elektronik B-L4S5I-IOT01 Discovery Kit ile çalışmaya başlama](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+) |[ST mikro elektronik B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html)|
|[NXP MIMXRT1060-EVK değerlendirme kiti ile çalışmaya başlama](https://go.microsoft.com/fwlink/p/?linkid=2129821) |[NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)|
|[NXP MIMXRT1050-EVKB değerlendirme kiti ile çalışmaya başlama](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1050-EVKB) |[NXP MIMXRT1050-EVKB](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/i-mx-rt1050-evaluation-kit:MIMXRT1050-EVK)|
|[Mikro yonga ATSAME54-XPRO değerlendirme seti ile çalışmaya başlama](https://go.microsoft.com/fwlink/p/?linkid=2129537) |[Mikro yonga ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)|
|[MxyongaAZ3166 IoT DevKit ile çalışmaya başlama](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166) |[MxyongaAZ3166 IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)|
|[RX65N-2MB için Renesas Starter Kit + ile çalışmaya başlama](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RSK_RX65N_2MB) |[RX65N-2MB için Renesas Starter Kit +](https://www.renesas.com/us/en/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-2mb-starter-kit-plus-renesas-starter-kit-rx65n-2mb)|

## <a name="next-steps"></a>Sonraki adımlar
Bu kılavuzda cihaza özgü hızlı başlangıç adımlarını tamamladıktan sonra, Azure RTOS Başlarken deposunda bulunan cihaza özgü diğer makaleleri ve örnekleri göz atın:
* [Azure RTOS ve Azure IoT ile çalışmaya başlama](https://github.com/azure-rtos/getting-started#getting-started-with-azure-rtos-and-azure-iot)
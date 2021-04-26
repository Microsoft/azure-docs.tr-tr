---
title: Kısıtlanmış cihazlarda IoT Tak ve Kullan kullanma | Microsoft Docs
description: Katıştırılmış C veya Azure RTOS için SDK kullanarak, kısıtlı cihazlarda IoT Tak ve Kullan nasıl uygulayabileceğinizi öğrenin.
author: EliotSeattle
ms.author: eliotgra
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6c792fbbb44b7dc769e7cdc56850684bd69ea40b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864146"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Kısıtlanmış cihazlarda IoT Tak ve Kullan uygulama

*Kısıtlanmış cihazlar* Için geliştiriyorsanız IoT Tak ve kullan 'Yi [Embedded C IoT Istemci kitaplıkları](https://aka.ms/embeddedcsdk) veya [Azure RTOS](/azure/rtos/overview-rtos)ile birlikte Azure SDK ile kullanabilirsiniz. Bu makalede, bu kısıtlanmış senaryolar için bağlantılar ve kaynaklar bulunur.

## <a name="use-the-sdk-for-embedded-c"></a>Embedded C için SDK 'Yı kullanma

Katıştırılmış C için SDK, [ıot Tak ve kullan kurallarını](concepts-convention.md)kullanma dahil, kısıtlı cihazları Azure IoT hizmetlerine bağlamaya yönelik hafif bir çözüm sunar. Aşağıdaki bağlantılar gerçek cihaza yönelik örnekleri ve eğitim ve hata ayıklama amaçlarını içerir.

### <a name="use-a-real-device"></a>Gerçek cihaz kullanma

Katıştırılmış C, cihaz sağlama hizmeti ve IoT Tak ve Kullan için SDK kullanan tam uçtan uca öğretici için, [IoT Hub cihaz sağlama hizmeti aracılığıyla Azure 'A bağlanmak için bkz. yeniden amaçlandırın PIC-IoT WX geliştirme panosu](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Tanıtım örnekleri

Katıştırılmış C deposu için SDK, IoT Tak ve Kullan kullanmayı gösteren [birkaç örnek](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) içerir:

> [!NOTE]
> Bu örnekler, eğitim ve hata ayıklama amacıyla Windows ve Linux üzerinde çalışan gösterilmiştir. Bir üretim senaryosunda, örnekler yalnızca kısıtlı cihazlara yöneliktir.

- [Gömülü C için SDK ile terstat örneği](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Gömülü C için SDK ile sıcaklık denetleyicisi örneği](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Azure RTOS 'ı kullanma

Azure RTOS, Azure IoT bulut hizmetlerine yerel bağlantı ekleyen hafif bir katman içerir. Bu katman, Azure RTOS 'ın gelişmiş özelliklerini kullanırken kısıtlı cihazları Azure IoT 'ye bağlamaya yönelik basit bir mekanizma sağlar. Daha fazla bilgi edinmek için bkz. [Microsoft Azure RTOS](/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Araç zincirlerinden

Azure RTOS örnekleri, farklı türlerde IDE ve araç zinciri bileşimleri ile sağlanır, örneğin:

- IAR: ıAR 'ın [Embedded çalışma ekranı](https://www.iar.com/iar-embedded-workbench/) IDE 'si
- GCC/CMake: açık kaynaklı [CMake](https://cmake.org/) derleme sisteminin ve [GNU ARM Embedded araç zincirinin](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)üzerine derleyin.
- Mcuifade so: NXP 'nin [Mcuxpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: Stmikro elektronik 's [STM32CUBE IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: mikro yonga 'nin [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide) 'si

### <a name="samples"></a>Örnekler

Azure RTOS ve IoT Tak ve Kullan ile farklı cihazlarda çalışmaya başlamanızı gösteren örnekler için aşağıdaki tabloya bakın:

Üretici | Cihaz | Örnekler |
| --- | --- | --- |
| Mikro yonga | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [iar](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [iar](https://aka.ms/azrtos-sample/rt1060-iar) • [mcuxpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| Stmikro elektronik | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| Stmikro elektronik | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [iar](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| Stmikro elektronik | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [iar](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Tak ve Kullan kısıtlı cihazlarda uygulama seçenekleri hakkında bilgi edindiğinize göre, bir sonraki adım [ıot Tak ve kullan kuralları](concepts-convention.md)hakkında bilgi almak için önerilir.
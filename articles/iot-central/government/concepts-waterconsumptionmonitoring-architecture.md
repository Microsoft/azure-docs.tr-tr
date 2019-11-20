---
title: Azure IoT Central ile oluşturulmuş su tüketimi izleme çözümü için başvuru mimarisi | Microsoft Docs
description: Azure IoT Central ile oluşturulmuş su tüketim izleme çözümü kavramlarını öğrenin.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d979c4210c7de4588338c8e3c30b179056102d0d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498795"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Su tüketim izleme başvuru mimarisi 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Su tüketim izleme çözümleri, **Azure IoT Central uygulama şablonuyla** bir başlangıç olarak başlangıç IoT uygulaması olarak oluşturulabilir. Bu makale uçtan uca çözüm oluşturma konusunda üst düzey bir başvuru mimarisi kılavuzu sağlar. 

![Su tüketim izleme mimarisi](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Kavramlar:

1. Cihazlar ve bağlantı  
1. IoT Central 
2. Genişletilebilirlik ve tümleştirmeler
3. İş uygulamaları

Genellikle su tüketim izleme çözümünde bir parçayı oynayan önemli bileşenlere göz atalım.

## <a name="devices-and-connectivity"></a>Cihazlar ve bağlantı 
Bu bölümde, genellikle akıllı su cihazları olarak su kalitesinde izleme veya su tüketim izleme gibi akıllı su çözümleri için kullanılan cihazlara başvuracağız. Akıllı su cihazları Flow ölçümleri, su kalitesi izleyicileri, akıllı vanalar, sızıntı algılayıcıları vb. olabilir.

Akıllı su çözümlerinde kullanılan cihazlar genellikle düşük güç Wide alan ağları (LPWAN) aracılığıyla bir üçüncü taraf ağ operatörü aracılığıyla bağlanır. Bu tür cihazlarda, cihaz verilerinizi Azure IoT Central IoT uygulamanıza göndermek için [azure IoT Central cihaz köprüsünden](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) yararlanabilirsiniz. Alternatif olarak, IP özellikli cihaz ağ geçitleriniz olabilir ve IoT Central doğrudan bağlanabilir.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central, IoT çözümünüzde hızla çalışmaya başlamanızı sağlayan bir IoT uygulama platformudur. Çözümünüzü, üçüncü taraf hizmetlerle markalayabilmeniz, özelleştirebilir ve tümleştirebilirsiniz.
Akıllı su cihazlarınızı IoT Central 'e bağladığınızda, cihaz komutu ve denetim, izleme ve uyarma, yerleşik RBAC, yapılandırılabilir Öngörüler panoları ve genişletilebilirlik seçenekleriyle kullanıcı arabirimi elde edersiniz. 


## <a name="extensibility-and-integrations"></a>Genişletilebilirlik ve tümleştirmeler 
IoT uygulamanızı IoT Central ve isteğe bağlı olarak genişletebilirsiniz:
* IoT Central uygulamadan sürekli veri dışarı aktarma aracılığıyla eğitim makinesi öğrenimi modelleri gibi gelişmiş analizler için IoT verilerinizi dönüştürün ve tümleştirin
* IoT Central uygulamadan Microsoft Flow veya Web kancaları aracılığıyla eylemleri tetikleyerek diğer sistemlerdeki iş akışlarını otomatikleştirin
* Program aracılığıyla API 'Leri aracılığıyla IoT Central IoT uygulamanıza erişin IoT Central

## <a name="business-applications"></a>İş Uygulamaları 
IoT verileri, bir su yardımcı programı dahilinde çeşitli iş uygulamalarını desteklemek için kullanılabilir. IoT Central su tüketim izleme uygulamanızı alan hizmetleriyle bağlamayı öğrenmek için, [Dynamics 365 alan hizmetleriyle tümleştirme](./how-to-configure-connected-field-services.md) hakkında öğreticiyi izleyin. 


## <a name="next-steps"></a>Sonraki adımlar
* Uygulama IoT Central [su tüketimi oluşturmayı](./tutorial-water-consumption-monitoring.md) öğrenin
* [IoT Central kamu şablonları](./overview-iot-central-government.md) hakkında daha fazla bilgi edinin
* IoT Central hakkında daha fazla bilgi edinmek için bkz. [IoT Central genel bakış](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

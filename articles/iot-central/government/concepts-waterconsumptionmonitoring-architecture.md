---
title: Azure IoT Central ile oluşturulmuş su tüketimi izleme çözümü için başvuru mimarisi | Microsoft Docs
description: Azure IoT Central ile oluşturulmuş su tüketim izleme çözümü kavramlarını öğrenin.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da1eafee719919d7f3086a059d8d2c70ef8d8cb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831663"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Su tüketimi izleme referans mimarisi 

Su tüketim izleme çözümleri, **Azure IoT Central uygulama şablonuyla** bir başlangıç olarak başlangıç IoT uygulaması olarak oluşturulabilir. Bu makale uçtan uca çözüm oluşturma konusunda üst düzey bir başvuru mimarisi kılavuzu sağlar. 

![Su tüketim izleme mimarisi](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Kavramlar:

1. Cihazlar ve bağlantı  
1. IoT Central 
1. Genişletilebilirlik ve tümleştirmeler
1. İş uygulamaları

Genellikle su tüketim izleme çözümünde bir parçayı oynayan önemli bileşenlere göz atalım.

## <a name="devices-and-connectivity"></a>Cihazlar ve bağlantı 
Bu bölüm, genellikle akıllı su cihazları olarak su kalitesinde izleme veya su tüketim izleme gibi akıllı su çözümleri için kullanılan cihazları ifade eder. Akıllı su cihazları Flow ölçümleri, su kalitesi izleyicileri, akıllı vanalar, sızıntı algılayıcıları vb. olabilir.

Akıllı su çözümlerinde kullanılan cihazlar genellikle düşük güç Wide alan ağları (LPWAN) üzerinden bir üçüncü taraf ağ operatörü aracılığıyla bağlanır. Bu tür cihazlarda, cihaz verilerinizi Azure IoT Central IoT uygulamanıza göndermek için [azure IoT Central cihaz Köprüsü](../core/howto-build-iotc-device-bridge.md) ' ne yararlanabilirsiniz. Alternatif olarak, IP özellikli cihaz ağ geçitleriniz olabilir ve IoT Central doğrudan bağlanabilir.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central, IoT çözümünüzde hızla çalışmaya başlamanızı sağlayan bir IoT uygulama platformudur. Çözümünüzü, üçüncü taraf hizmetlerle markalayabilmeniz, özelleştirebilir ve tümleştirebilirsiniz.
Akıllı su cihazlarınızı IoT Central 'e bağladığınızda, cihaz komutu ve denetim, izleme ve uyarma, yerleşik RBAC, yapılandırılabilir Öngörüler panoları ve genişletilebilirlik seçenekleriyle kullanıcı arabirimi elde edersiniz. 


## <a name="extensibility-and-integrations"></a>Genişletilebilirlik ve tümleştirmeler
IoT uygulamanızı IoT Central ve isteğe bağlı olarak genişletebilirsiniz:
* IoT Central uygulamadan sürekli veri dışarı aktarma aracılığıyla eğitim makinesi öğrenimi modelleri gibi gelişmiş analizler için IoT verilerinizi dönüştürün ve tümleştirin
* IoT Central uygulamadan Power otomatikleştir veya Web kancaları kullanarak eylemleri tetikleyerek diğer sistemlerdeki iş akışlarını otomatikleştirin
* Program aracılığıyla API 'Leri aracılığıyla IoT Central IoT uygulamanıza erişin IoT Central

## <a name="business-applications"></a>İş Uygulamaları 
IoT verileri, su yardımcı programı dahilinde farklı türlerde iş uygulamalarının gücüne yönelik olarak kullanılabilir. IoT Central su tüketim izleme uygulamanızı alan hizmetleriyle bağlamayı öğrenmek için, [Dynamics 365 alan hizmetleriyle tümleştirme](./how-to-configure-connected-field-services.md) hakkında öğreticiyi izleyin. 


## <a name="next-steps"></a>Sonraki adımlar
* Uygulama IoT Central [su tüketimi oluşturmayı](./tutorial-water-consumption-monitoring.md) öğrenin
* [IoT Central kamu şablonları](./overview-iot-central-government.md) hakkında daha fazla bilgi edinin
* IoT Central hakkında daha fazla bilgi edinmek için bkz. [IoT Central genel bakış](../core/overview-iot-central.md)

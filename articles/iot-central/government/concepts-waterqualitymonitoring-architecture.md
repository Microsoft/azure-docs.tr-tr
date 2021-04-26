---
title: Azure IoT Central ile oluşturulmuş su kalitesi izleme çözümü için başvuru mimarisi | Microsoft Docs
description: Azure IoT Central ile oluşturulmuş bir su kalite izleme çözümü için kavramlar öğrenin.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3dad4aea56586444bd54ac47c0462232913e173f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831612"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Su kalitesi izleme referans mimarisi 
Su kalitesinde izleme çözümleri, **Azure IoT Central uygulama şablonuyla** birlikte bir başlangıç IoT uygulaması olarak oluşturulabilir. Bu makale uçtan uca çözüm oluşturma konusunda üst düzey bir başvuru mimarisi kılavuzu sağlar. 

![Su kalitesi izleme mimarisi](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Kavramlar:

1. Cihazlar ve bağlantı  
1. IoT Central 
1. Genişletilebilirlik ve tümleştirmeler
1. İş uygulamaları

Genellikle su kalite izleme çözümünde bir bölümü oynatacak anahtar bileşenlere göz atalım.

## <a name="devices-and-connectivity"></a>Cihazlar ve bağlantı 
Bu bölümde, genellikle akıllı su cihazları olarak su kalitesinde izleme veya su tüketim izleme 'de kullanılan cihazlara başvuracağız. Akıllı su cihazları Flow ölçümleri, su kalitesi izleyicileri, akıllı vanalar, sızıntı algılayıcıları vb. olabilir.

Akıllı su çözümlerinde kullanılan cihazlar genellikle düşük güç Wide alan ağları (LPWAN) üzerinden bir üçüncü taraf ağ operatörü aracılığıyla bağlanır. Bu tür cihazlarda, cihaz verilerinizi Azure IoT Central IoT uygulamanıza göndermek için [azure IoT Central cihaz köprüsünden](../core/howto-build-iotc-device-bridge.md) yararlanabilirsiniz. Alternatif olarak, IP özellikli cihaz ağ geçitleriniz olabilir ve IoT Central doğrudan bağlanabilir.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central, IoT çözümünüzde hızla çalışmaya başlamanızı sağlayan bir IoT uygulama platformudur. Çözümünüzü, üçüncü taraf hizmetlerle markalayabilmeniz, özelleştirebilir ve tümleştirebilirsiniz.
Akıllı su cihazlarınızı IoT Central 'e bağladığınızda, cihaz komutu ve denetim, izleme ve uyarma, yerleşik RBAC, yapılandırılabilir Öngörüler panoları ve genişletilebilirlik seçenekleriyle kullanıcı arabirimi elde edersiniz. 

## <a name="extensibility-and-integrations"></a>Genişletilebilirlik ve tümleştirmeler
IoT uygulamanızı IoT Central ve isteğe bağlı olarak genişletebilirsiniz:
* IoT Central uygulamadan sürekli veri dışarı aktarma aracılığıyla eğitim makinesi öğrenimi modelleri gibi gelişmiş analizler için IoT verilerinizi dönüştürün ve tümleştirin
* IoT Central uygulamadan Power otomatikleştir veya Web kancaları kullanarak eylemleri tetikleyerek diğer sistemlerdeki iş akışlarını otomatikleştirin
* Program aracılığıyla API 'Leri aracılığıyla IoT Central IoT uygulamanıza erişin IoT Central

## <a name="business-applications"></a>İş uygulamaları 
IoT verileri, bir su yardımcı programı dahilinde çeşitli iş uygulamalarını desteklemek için kullanılabilir. IoT Central su kalite izleme uygulamanızı alan hizmetleriyle bağlamayı öğrenmek için, [Dynamics 365 alan hizmetleriyle tümleştirme](./how-to-configure-connected-field-services.md)hakkında makaleyi izleyin. 


## <a name="next-steps"></a>Sonraki adımlar
* [Su kalitesi izleme](./tutorial-water-quality-monitoring.md) IoT Central uygulaması oluşturmayı öğrenin
* [IoT Central kamu şablonları](./overview-iot-central-government.md) hakkında daha fazla bilgi edinin
* IoT Central hakkında daha fazla bilgi edinmek için bkz. [IoT Central genel bakış](../core/overview-iot-central.md)

---
title: IoT Central enerji çözümleri oluşturun | Microsoft Docs
description: Azure IoT Central uygulama şablonlarını kullanarak enerji çözümü oluşturmayı öğrenin.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8c7d2481fe3ef361386bacc652d87744c1c790bb
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581496"
---
# <a name="build-energy-solutions-with-iot-central"></a>IoT Central ile enerji çözümleri oluşturun 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]


Akıllı ölçümler ve Solar bölmeleri enerji sektörü dönüştürmesinin önemli bir rolünü yürütüyor. Akıllı ölçümler, enerji tüketimlerini hakkında daha fazla denetim ve gerçek zamanlı içgörüler sunar. Akıllı ölçer ve güneş paneli izleme uygulamaları, çeşitli özellikleri göstermek için örnek şablonlardır. İş ortakları, belirli ihtiyaçları için IoT Central enerji çözümleri oluşturmak üzere bu şablonlardan yararlanabilir. Yeni kodlama yoktur ve bu uygulamaları dağıtmak ve kullanmak için ek maliyet gerekmez. Enerji uygulama şablonları ve özellikleri hakkında daha fazla bilgi edinin.


## <a name="what-is-the-smart-meter-monitoring-application"></a>Akıllı ölçüm izleme uygulaması nedir?
 Akıllı ölçümler yalnızca otomatik faturalandırmayı etkinleştirmez, aynı zamanda gerçek zamanlı okumalar ve iki yönlü iletişim gibi gelişmiş ölçüm kullanım durumlarını da karşılamıyor. Akıllı ölçüm uygulama şablonu, yardımcı programların ve iş ortaklarının akıllı ölçüm durumunu ve verilerini izlemelerine, alarmlar ve bildirimler tanımlamasına olanak sağlar. Kesme, ölçüm ve güncelleştirme yazılımı gibi örnek komutlar sağlar. Ölçüm verileri diğer iş uygulamalarına çıkış olarak ayarlanabilir ve özel çözümler geliştirebilir. 

Uygulamanın temel işlevleri: 

* Ölçüm örneği cihaz modeli 
* Ölçüm bilgisi ve canlı durumu 
* Enerji, güç ve voltages gibi ölçüm okumaları
* Ölçüm komut örnekleri 
* Yerleşik görselleştirme ve panolar
* Özel çözüm geliştirmesi için genişletilebilirlik

[Akıllı ölçüm izleme uygulamasını](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) , bir Azure aboneliği ve herhangi bir taahhütsiz ücretsiz olarak ücretsiz olarak deneyebilirsiniz.


Uygulamayı dağıttıktan sonra, aşağıdaki şekilde gösterildiği gibi, Pano üzerinde sanal ölçer verilerini görürsünüz. Bu şablon, belirli kullanım durumlarınız için kolayca genişletebilen ve özelleştirebileceğiniz örnek bir uygulamadır.

> [!div class="mx-imgBorder"]
> Akıllı ölçüm uygulama panosu ![](media/overview-iot-central-energy/smart-meter-app-dashboard.png)


## <a name="what-is-the-solar-panel-monitoring-application"></a>Solar paneli izleme uygulaması nedir?
Solar paneli izleme uygulaması, yardımcı programlar ve iş ortaklarının, enerji üretimi ve bağlantı durumu gibi güneş panellerini neredeyse gerçek zamanlı olarak izlemesini sağlar. Tanımlı eşik ölçütlerine göre bildirimler gönderebilir. Güncelleştirme bellenimi ve diğer özellikleri gibi örnek komutlar sağlar. Güneş paneli verileri diğer iş uygulamalarına çıkış olarak ayarlanabilir ve özel çözümler geliştirebilir. 

Uygulamanın temel işlevleri: 

* Güneş paneli örnek cihaz modeli 
* Güneş paneli bilgileri ve canlı durumu
* Güneş enerji üretimi ve diğer okumalar
* Komut ve denetim örnekleri
* Yerleşik görselleştirme ve panolar
* Özel çözüm geliştirmesi için genişletilebilirlik

Bağımsız [panel izleme uygulamasını](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) bir Azure aboneliği ve taahhütler olmadan ücretsiz olarak deneyebilirsiniz.

Uygulamayı dağıttıktan sonra, aşağıdaki panoda gösterildiği gibi, sanal bilgisayar panel verilerini 1-2 dakika içinde görürsünüz. Bu şablon, belirli kullanım durumlarınız için kolayca genişletebilen ve özelleştirebileceğiniz örnek bir uygulamadır. 

> [!div class="mx-imgBorder"]
> ![Solar paneli uygulama panosu](media/overview-iot-central-energy/solar-panel-app-dashboard.png)


## <a name="next-steps"></a>Sonraki adımlar
Enerji çözümü oluşturmaya başlamak için:
* Ücretsiz uygulama şablonları oluşturun: [Akıllı Ölçüm uygulaması](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring), [güneş paneli uygulaması](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* [Akıllı ölçüm izleme uygulaması kavramları](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app) hakkında bilgi edinin
* [Güneş paneli izleme uygulaması kavramları](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app) hakkında bilgi edinin
* [IoT Central platformu](https://docs.microsoft.com/azure/iot-central/) hakkında bilgi edinin

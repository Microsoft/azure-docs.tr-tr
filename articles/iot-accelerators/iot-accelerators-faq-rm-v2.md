---
title: Uzaktan Izleme Çözüm Hızlandırıcısı SSS-Azure | Microsoft Docs
description: Bu makalede, uzaktan Izleme çözüm hızlandırıcılarına yönelik sık sorulan sorular yanıtlanmaktadır.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826244"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Uzaktan Izleme Çözüm Hızlandırıcısı için sık sorulan sorular

Ayrıca bkz. genel [SSS](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Yeni uzaktan Izleme çözümünü sağlama maliyeti ne kadar sürer?

Yeni Çözüm Hızlandırıcısı iki dağıtım seçeneği sunar:

* Tanıtım veya kavram kanıtı oluşturmak isteyen geliştiricilere daha düşük geliştirme maliyeti veya müşterileri arayan geliştiriciler için tasarlanan *temel* bir seçenek.
* Üretime yönelik bir altyapı dağıtmak isteyen kuruluşlar için tasarlanan *Standart* bir seçenek.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Çözümmi geliştirdiğimde maliyetlerimi bir daha tutdum nasıl emin olabilirim?

İki farklı dağıtım sağlamaya ek olarak, yeni uzaktan Izleme çözümünün isteğe bağlı tüm sanal cihazları etkinleştirme veya devre dışı bırakma ayarı vardır. Benzetimi devre dışı bırakmak, çözümde alınan verileri azaltır ve bu nedenle genel maliyettir.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Temel ve standart dağıtım seçenekleri arasındaki fark nedir? İki dağıtım seçeneği arasında Nasıl yaparım? karar veriyor musunuz?

Her dağıtım seçeneği farklı gereksinimlere yanıt verir. Temel dağıtım, başlamak ve PoC ve küçük Pilots geliştirmek için tasarlanmıştır. En düşük gerekli kaynaklarla ve daha düşük bir maliyetle daha kolay bir mimari sağlar. Standart dağıtım, üretime Ready bir çözüm oluşturup özelleştirmek için tasarlanmıştır ve bunu gerçekleştirmek için gerekli öğelerle bir dağıtım sağlar. Güvenilirlik ve ölçeklendirme için, uygulama mikro hizmetleri Docker Kapsayıcıları olarak oluşturulur ve Orchestrator kullanılarak dağıtılır (varsayılan olarak Kubernetes). Düzenleyici, uygulamanın dağıtımından, ölçeklenmesinden ve yönetiminden sorumludur. Geçerli gereksinimlerinize göre bir seçenek seçmeniz gerekir. Proje aşamaınıza bağlı olarak, birini, diğerini veya bir bileşimini kullanabilirsiniz.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Panoda dinamik bir harita mi Nasıl yaparım??

Daha fazla bilgi için bkz. [dinamik bir haritadaki cihazları görmek için eşleme anahtarını yükseltme](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Uzaktan Izleme çözümünün önceki sürümüyle ilgili bilgileri nereden bulabilirim?

Uzaktan Izleme çözüm hızlandırıcısının önceki sürümü IoT Suite uzaktan Izleme çözümü olarak bilinir. Arşivlenmiş belgeleri [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)' de bulabilirsiniz.

### <a name="next-steps"></a>Sonraki adımlar

IoT çözüm hızlandırıcılarının diğer özellik ve yeteneklerinden bazılarını da keşfedebilirsiniz:

* [Uzaktan Izleme çözüm hızlandırıcısının yeteneklerini keşfet](quickstart-remote-monitoring-deploy.md)
* [Tahmine Dayalı Bakım çözüm hızlandırıcısına genel bakış](iot-accelerators-predictive-overview.md)
* [Bağlı fabrika çözüm Hızlandırıcısını dağıtma](quickstart-connected-factory-deploy.md)
* [Sıfırdan IoT güvenliği](/azure/iot-fundamentals/iot-security-ground-up)

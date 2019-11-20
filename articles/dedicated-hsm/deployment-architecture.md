---
title: Dağıtım mimarisi-Azure ayrılmış HSM | Microsoft Docs
description: Uygulama mimarisinin bir parçası olarak Azure ayrılmış HSM kullanırken temel tasarım konuları
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: ff86c25de006495e3536f2ff907e1cf40a216f8e
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927856"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Azure Ayrılmış HSM dağıtım mimarisi

Azure adanmış HSM, Azure 'da şifreleme anahtar depolaması sağlar. Sıkı güvenlik gereksinimlerini karşılar. Müşteriler, şu durumlarda Azure ayrılmış HSM 'yi kullanmaya yarar olacaktır:

* FIPS 140-2 düzey 3 sertifikalarıyla Buluşmalıdır
* HSM 'ye özel erişime sahip olmaları gerekir
* cihazların tamamen denetimine sahip olması gerekir

HSM 'ler, Microsoft 'un veri merkezlerinde dağıtılır ve yüksek oranda kullanılabilir bir çözümün temeli olarak kolayca bir cihaz çifti olarak sağlanabilir. Ayrıca, olağanüstü durum dayanıklı bir çözüm için bölgeler arasında da dağıtılabilir. Ayrılmış HSM 'ye sahip bölgeler Şu anda kullanılabilir:

* Doğu ABD
* Doğu ABD 2
* Batı ABD
* Batı ABD 2
* Orta Güney ABD
* Güneydoğu Asya
* Doğu Asya
* Kuzey Avrupa
* Batı Avrupa
* Birleşik Krallık Güney
* Birleşik Krallık Batı
* Orta Kanada
* Doğu Kanada
* Avustralya Doğu
* Avustralya Güneydoğu

Bu bölgelerin her birinde, iki bağımsız veri merkezinde veya en az iki bağımsız kullanılabilirlik bölgesinde dağıtılan HSM rafları vardır. Güney Doğu Asya üç kullanılabilirlik bölgesine sahiptir ve Doğu ABD 2 iki tane vardır. Avrupa, Asya ve ABD 'de adanmış HSM hizmeti sunan toplam sekiz bölge vardır. Azure bölgeleri hakkında daha fazla bilgi için bkz. resmi [Azure bölgeleri bilgileri](https://azure.microsoft.com/global-infrastructure/regions/).
Tüm özel HSM tabanlı çözümler için bazı tasarım faktörleri konum/gecikme, yüksek kullanılabilirlik ve diğer dağıtılmış uygulamalar için destek.

## <a name="device-location"></a>Cihaz konumu

En iyi HSM cihaz konumu, şifreleme işlemleri gerçekleştiren uygulamalara en yakın yakındır. Bölge içi gecikme süresinin tek basamaklı milisaniye olması beklenir. Çapraz bölge gecikmesi bundan daha yüksek 5 ila 10 kat olabilir.

## <a name="high-availability"></a>Yüksek kullanılabilirlik

Yüksek kullanılabilirlik elde etmek için bir müşterinin yüksek kullanılabilirlik çifti olarak Gemalto Software kullanılarak yapılandırılmış bir bölgede iki HSM cihazı kullanması gerekir. Bu dağıtım türü, tek bir cihaz, anahtar işlemlerini işlemesini önlemek için bir sorun yaşadığında anahtarların kullanılabilirliğini sağlar. Ayrıca güç kaynağı değişikliği gibi onarım/çözme bakım işlemi gerçekleştirirken riski önemli ölçüde azaltır. Her türlü bölgesel düzey hata için bir tasarımın hesaba göre olması önemlidir. Bölgesel düzey arızalar, acericanes, floods veya deprem gibi doğal felaketler olduğunda meydana gelebilir. Bu tür olaylar, başka bir bölgedeki HSM cihazları sağlanarak azaltılmalıdır. Başka bir bölgede dağıtılan cihazlar, Gemalto yazılım yapılandırması aracılığıyla birlikte eşleştirilebilir. Bu, yüksek oranda kullanılabilir ve olağanüstü durum dayanıklı bir çözüm için en düşük dağıtımın iki bölgede dört HSM cihazı olduğu anlamına gelir. Bölgeler arasında yerel artıklığı ve artıklık, gecikme süresi, kapasiteyi desteklemek veya uygulamaya özel diğer gereksinimleri karşılamak için başka bir HSM cihaz dağıtımı eklemek için temel olarak kullanılabilir.

## <a name="distributed-application-support"></a>Dağıtılmış uygulama desteği

Adanmış HSM cihazları genellikle anahtar depolama ve anahtar alma işlemleri gerçekleştirmesi gereken uygulamalar desteğiyle dağıtılır. Adanmış HSM cihazlarının bağımsız uygulama desteği için 10 bölümü vardır. Cihaz konumu, hizmeti kullanması gereken tüm uygulamaların bütünsel görünümünü temel almalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Dağıtım mimarisi belirlendikten sonra, bu mimariyi uygulamak için gereken yapılandırma etkinliklerinin çoğu, Gemalto tarafından sağlanacaktır. Buna cihaz yapılandırması ve uygulama tümleştirme senaryoları da dahildir. Daha fazla bilgi için, [Gemalto müşteri destek](https://supportportal.gemalto.com/csm/) portalını kullanın ve yönetim ve yapılandırma kılavuzlarını indirin. Microsoft iş ortağı sitesinin çeşitli Tümleştirme kılavuzu vardır.
Hizmetin yüksek kullanılabilirlik ve güvenlik gibi tüm temel kavramlarının cihaz sağlama veya uygulama tasarımı ve dağıtımdan önce iyi anlaşıldığından emin olmanız önerilir.
Daha fazla kavram düzeyi konuları:

* [Yüksek kullanılabilirlik](high-availability.md)
* [Fiziksel güvenlik](physical-security.md)
* [Ağ](networking.md)
* [Desteklenebilirlik](supportability.md)
* [İzleme](monitoring.md)

---
title: Azure Service Fabric kafes uygulamalarının ölçeklenebilirliği
description: Uygulamaları Service Fabric kafese dağıtmanın avantajlarından biri, el ile veya otomatik ölçeklendirme ilkeleriyle hizmetlerinizi kolayca ölçeklendirebilme olanağıdır.
author: georgewallace
ms.author: gwallace
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 3f0e115e596925878bf9fdd43b7074cefdba47b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626867"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Service Fabric kafes uygulamalarını ölçeklendirme

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Service Fabric kafese uygulama dağıtmanın başlıca avantajlarından biri, hizmetlerinizi kolayca ölçeklendirebilme olanağı sağlar. Bu, hizmetinizdeki farklı yük miktarını işlemek veya kullanılabilirliği iyileştirmek için kullanılmalıdır. Hizmetlerinizi el ile ölçeklendirebilir veya otomatik ölçeklendirme ilkeleri ayarlayabilirsiniz.

## <a name="manual-scaling-instances"></a>El ile ölçeklendirme örnekleri

Uygulama kaynağı için dağıtım şablonunda her hizmet, hizmetin dağıtılmasını istediğiniz sayıyı ayarlamak için kullanılabilen bir *replicaCount* özelliğine sahiptir. Bir uygulama, birden çok hizmetten oluşabilir; her hizmet, birlikte dağıtılan ve yönetilen benzersiz bir *replicaCount* sayısına sahiptir. Hizmet çoğaltmaları sayısını ölçeklendirmek için, dağıtım şablonunda ölçeklendirmek istediğiniz her bir hizmet için *replicaCount* değerini veya parametreler dosyasını değiştirin. Ardından uygulamayı yükseltin.

Hizmet örneklerinin el ile ölçeklendirilmesi örnekleri için bkz. [hizmetlerinizi el ile ölçeklendirin veya out](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Otomatik Ölçeklendirme hizmeti örnekleri
Otomatik ölçeklendirme, hizmet örneklerinizin sayısını (yatay ölçeklendirme) dinamik olarak ölçeklendirmek için Service Fabric ek bir yetenektir. Otomatik ölçeklendirme, büyük ölçüde esneklik sağlar ve CPU veya bellek kullanımına göre hizmet örneklerinin sağlanması veya kaldırılmasına olanak sağlar.  Otomatik ölçeklendirme, iş yükünüz için doğru hizmet örneği sayısını çalıştırmanıza ve maliyeti iyileştirmek için optimize etmenize olanak sağlar.

Hizmet kaynak dosyasında hizmet başına otomatik ölçeklendirme ilkesi tanımlandı. Her ölçeklendirme ilkesi iki bölümden oluşur:

- Hizmet ölçeklendirmesinin ne zaman yapılacağını açıklayan bir ölçeklendirme tetikleyicisi. Hizmetin ne zaman ölçekleneceğini tespit eden üç etken vardır. *Alt yük eşiği* , hizmetin ne zaman ölçeklendirileceğini belirleyen bir değerdir. Bölümlerin tüm örneklerinin ortalama yükü bu değerden düşükse, hizmet içinde ölçeklendirilecektir. *Büyük yük eşiği* , hizmetin ne zaman gösterileceğini belirleyen bir değerdir. Bölümün tüm örneklerinin ortalama yükü bu değerden yüksekse, hizmet dışa ayarlanır. *Ölçeklendirme aralığı* , tetikleyicinin ne sıklıkta denetleneceğini (saniye cinsinden) belirler. Tetikleyici denetlendikten sonra, ölçeklendirilmesi gerekiyorsa mekanizmanın uygulanması gerekir. Ölçeklendirme gerekmiyorsa, hiçbir eylem yapılmaz. Her iki durumda da, ölçekleme aralığı sona ermeden önce tetikleyici yeniden denetlenmez.

- Bir ölçeklendirme mekanizması, tetiklendikten sonra ölçeklendirmeyi nasıl gerçekleştirileceğini açıklar. *Ölçek artışı* , mekanizma tetiklendiğinde kaç örnek ekleneceğini veya kaldırılacağını belirler. *En fazla örnek sayısı* ölçekleme için üst sınırı tanımlar. Örnek sayısı bu sınıra ulaşırsa, yük ne olursa olsun hizmet tarafından ölçeklenmez. *Minimum örnek sayısı* ölçekleme için alt sınırı tanımlar. Bölüm örneklerinin sayısı bu sınıra ulaşırsa, yük ne olursa olsun hizmet bu sınıra göre ölçeklenmez.

Hizmetiniz için bir otomatik ölçeklendirme ilkesi ayarlamayı öğrenmek için [Otomatik ölçeklendirme hizmetlerini](service-fabric-mesh-howto-auto-scale-services.md)okuyun.

## <a name="next-steps"></a>Sonraki adımlar

Uygulama modeli hakkında daha fazla bilgi için bkz. [Service Fabric Resources](service-fabric-mesh-service-fabric-resources.md)

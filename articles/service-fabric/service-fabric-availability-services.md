---
title: Service Fabric hizmetlerinin kullanılabilirliği
description: Bir Azure Service Fabric uygulamasında bir hizmetin hata algılama, yük devretme ve kurtarma işlemini açıklar.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018911"
---
# <a name="availability-of-service-fabric-services"></a>Service Fabric hizmetlerinin kullanılabilirliği
Bu makalede, Azure Service Fabric 'nin bir hizmetin kullanılabilirliğini nasıl koruduğu konusunda genel bir bakış sunulmaktadır.

## <a name="availability-of-service-fabric-stateless-services"></a>Service Fabric durum bilgisi olmayan Hizmetlerin kullanılabilirliği
Service Fabric Hizmetleri durum bilgisiz veya durumsuz olabilir. Durum bilgisi olmayan bir hizmet, yüksek oranda kullanılabilir veya güvenilir olması gereken [yerel bir durumu](service-fabric-concepts-state.md) olmayan bir uygulama hizmetidir.

Durum bilgisi olmayan bir hizmet oluşturmak için tanımlama gerekir `InstanceCount` . Örnek sayısı, kümede çalışması gereken durum bilgisi olmayan hizmetin uygulama mantığının örnek sayısını tanımlar. Örnek sayısının artırılması, durum bilgisi olmayan bir hizmetin ölçeğini genişletmek için önerilen yoldur.

Durum bilgisi olmayan adlandırılmış hizmetin bir örneği başarısız olursa, kümedeki uygun bir düğümde yeni bir örnek oluşturulur. Örneğin, durum bilgisi olmayan bir hizmet örneği Düğüm1 üzerinde başarısız olabilir ve Düğüm5 üzerinde yeniden oluşturulabilir.

## <a name="availability-of-service-fabric-stateful-services"></a>Service Fabric durum bilgisi olan hizmetlerin kullanılabilirliği
Durum bilgisi olan bir hizmetin kendisiyle ilişkilendirilmiş bir durumu vardır. Service Fabric, durum bilgisi olan bir hizmet bir çoğaltmalar kümesi olarak modellenir. Her çoğaltma, hizmetin kodunun çalışan bir örneğidir. Çoğaltma Ayrıca bu hizmetin durumunun bir kopyasına sahiptir. Okuma ve yazma işlemleri, *birincil* olarak adlandırılan tek bir çoğaltmada gerçekleştirilir. Yazma işlemlerinden gelen değişiklikler, *Etkin ikincil* adı verilen ve uygulanan çoğaltma kümesindeki diğer yinelemelere *çoğaltılır* . 

Yalnızca bir birincil çoğaltma olabilir, ancak birden çok etkin Ikincil çoğaltma olabilir. Etkin Ikincil çoğaltmaların sayısı yapılandırılabilir ve daha yüksek sayıda çoğaltma daha fazla sayıda eşzamanlı yazılım ve donanım hatasını kabul edebilir.

Birincil çoğaltma kapalıysa Service Fabric etkin Ikincil çoğaltmalardan birini yeni birincil çoğaltma yapar. Bu etkin Ikincil çoğaltma, *çoğaltma* aracılığıyla durumun güncelleştirilmiş sürümüne zaten sahip ve daha fazla okuma/yazma işlemi işlemeye devam edebilir. Bu işlem yeniden *yapılandırma olarak bilinir ve yeniden* [yapılandırma](service-fabric-concepts-reconfiguration.md) makalesinde daha ayrıntılı olarak açıklanmıştır.

Bir çoğaltma kavramı birincil ya da etkin bir Ikincil değer olan *çoğaltma rolü* olarak bilinir. Bu çoğaltmalar [çoğaltmalar ve örnekler](service-fabric-concepts-replica-lifecycle.md) makalesinde daha ayrıntılı olarak açıklanmıştır. 

## <a name="next-steps"></a>Sonraki adımlar
Service Fabric kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Service Fabric hizmetlerini ölçeklendirme](service-fabric-concepts-scalability.md)
- [Service Fabric Hizmetleri bölümlendirme](service-fabric-concepts-partitioning.md)
- [Durum tanımlama ve yönetme](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)


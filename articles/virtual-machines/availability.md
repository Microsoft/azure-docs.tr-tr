---
title: Azure sanal makineleri için kullanılabilirlik seçenekleri
description: Azure 'da sanal makine çalıştırmaya yönelik kullanılabilirlik seçenekleri hakkında bilgi edinin
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 821c50b5cb48bddb586c3589326a7f31c7bafded
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604541"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Azure sanal makineleri için kullanılabilirlik seçenekleri
Bu makalede, Azure sanal makineleri (VM 'Ler) için kullanılabilirlik seçeneklerine genel bakış sunulmaktadır.

## <a name="availability-zones"></a>Kullanılabilirlik alanları

[Kullanılabilirlik alanları](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) , sanal makinelerinizdeki uygulamaların ve verilerin kullanılabilirliğini korumak için sahip olduğunuz denetim düzeyini genişletir. Bir kullanılabilirlik alanı, bir Azure bölgesi içinde fiziksel olarak ayrı bir bölgedir. Desteklenen Azure bölgesi başına üç Kullanılabilirlik Alanları vardır. 

Her Kullanılabilirlik Alanı farklı bir güç kaynağı, ağ ve soğutma sistemine sahiptir. Bölgelerinizi, bölgelerde çoğaltılan VM 'Leri kullanmak üzere tasarlayarak, uygulamalarınızı ve verilerinizi bir veri merkezi kaybından koruyabilirsiniz. Bir bölge tehlikeye girerse, çoğaltılan uygulamalar ve veriler başka bir bölgede anında kullanılabilir. 

## <a name="availability-sets"></a>Kullanılabilirlik kümeleri
[Kullanılabilirlik kümesi](availability-set-overview.md) , Azure 'un uygulamanızın artıklık ve kullanılabilirlik sağlamak üzere nasıl oluşturulduğunu anlamasına olanak tanıyan mantıksal bir sanal makine gruplandırmasıdır. Yüksek oranda kullanılabilir bir uygulama sağlamak ve [% 99,95 Azure SLA 'sını](https://azure.microsoft.com/support/legal/sla/virtual-machines/)karşılamak için bir kullanılabilirlik kümesi içinde iki veya daha fazla sanal makine oluşturulmasını öneririz. Kullanılabilirlik kümesinin kendisi için herhangi bir maliyet yoktur, yalnızca oluşturduğunuz her sanal makine örneği için ödeme yaparsınız.


## <a name="virtual-machines-scale-sets"></a>Sanal Makine Ölçek Kümeleri 

[Azure sanal makine ölçek kümeleri](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) , yük dengeli bir VM grubu oluşturmanızı ve yönetmenizi sağlar. Tanımlı bir zamanlamaya veya talebe yanıt olarak sanal makine örneği sayısı otomatik olarak artabilir ya da azalabilir. Ölçek Kümeleri, uygulamalarınız için yüksek kullanılabilirlik sağlar ve birçok VM 'yi merkezi olarak yönetmenize, yapılandırmanıza ve güncelleştirmenize olanak tanır. Yüksek oranda kullanılabilir bir uygulama sağlamak ve [% 99,95 Azure SLA 'sını](https://azure.microsoft.com/support/legal/sla/virtual-machines/)karşılamak için ölçek kümesi içinde iki veya daha fazla sanal makine oluşturulmasını öneririz. Ölçek kümesinin kendisi için herhangi bir maliyet yoktur, yalnızca oluşturduğunuz her sanal makine örneği için ödeme yaparsınız.

Ölçek kümesindeki sanal makineler aynı zamanda tek bir kullanılabilirlik bölgesine veya bölgeye göre dağıtılabilir. Kullanılabilirlik alanı dağıtım seçenekleri, [düzenleme moduna](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context)göre farklılık gösterebilir.

## <a name="load-balancer"></a>Yük dengeleyici
En fazla uygulama esnekliğini almak için [Azure Load Balancer](../load-balancer/load-balancer-overview.md) bir kullanılabilirlik bölgesi veya kullanılabilirlik kümesiyle birleştirin. Azure Load Balancer, birden fazla sanal makine arasında trafiği dağıtır. Standart katman sanal makinelerimize Azure Load Balancer dahildir. Tüm sanal makine katmanları Azure Load Balancer hizmetini içermez. Sanal makinelerinizin Yük Dengelemesi hakkında daha fazla bilgi için bkz. [Linux](linux/tutorial-load-balancer.md) veya [Windows](windows/tutorial-load-balancer.md)Için **sanal makinelerin yükünü dengeleme** .


## <a name="azure-storage-redundancy"></a>Azure Depolama yedekliliği
Azure depolama, geçici donanım arızaları, ağ veya güç kesintileri ve çok büyük doğal felaketler de dahil olmak üzere planlı ve planlanmamış olaylardan korunabilmesi için verilerinizin birden çok kopyasını her zaman depolar. Artıklık, depolama hesabınızın, başarısızlık durumunda bile kullanılabilirlik ve dayanıklılık hedeflerini karşıladığından emin olmanızı sağlar.

Senaryonuz için en uygun artıklık seçeneğinin hangisi olduğuna karar verirken, düşük maliyetler ve daha yüksek kullanılabilirlik arasındaki avantajları göz önünde bulundurun. Hangi artıklık seçeneğini belirlemenizi gerektiğine yardımcı olan faktörler şunlardır:
- Verileriniz birincil bölgede nasıl çoğaltılır
- Verilerinizin birincil bölgeye coğrafi olarak uzaktaki ikinci bir bölgeye çoğaltılıp çoğaltılmayacağı, bölgesel olağanüstü durumlara karşı koruma
- Birincil bölge herhangi bir nedenle kullanılamaz duruma gelirse, uygulamanızın ikincil bölgedeki çoğaltılan verilere okuma erişimi gerektirip gerektirmediğini belirtir

Daha fazla bilgi için bkz. [Azure Storage yedekliği](../storage/common/storage-redundancy.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Bir kuruluş olarak, planlı ve plansız kesintiler gerçekleştiğinde verilerinizi güvende ve uygulamalarınız ile iş yüklerinizi çevrimiçi tutan bir iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejisi benimsemeniz gerekir.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) , iş uygulamaları ve iş yüklerini kesintiler sırasında çalışır durumda tutarak iş sürekliliği sağlamaya yardımcı olur. Site Recovery, fiziksel ve sanal makineler (VM) üzerinde çalışan iş yüklerini birincil siteden ikincil bir konuma çoğaltır. Birincil sitenizde bir kesinti oluştuğunda ikinci konuma yük devredersiniz ve uygulamalara oradan erişirsiniz. Birincil konum tekrar çalışmaya başladıktan sonra o konuma geri dönebilirsiniz.

Site Recovery, şunlar için çoğaltmayı yönetebilir:
- Azure bölgeleri arasında çoğaltılan Azure VM’leri.
- Şirket içi VM 'Ler, Azure Stack VM 'Ler ve fiziksel sunucular.

## <a name="next-steps"></a>Sonraki adımlar
- [Kullanılabilirlik bölgesinde bir sanal makine oluşturma](/azure/virtual-machines/linux/create-cli-availability-zone)
- [Kullanılabilirlik kümesinde sanal makine oluşturma](/azure/virtual-machines/linux/tutorial-availability-sets)
- [Sanal makine ölçek kümesi oluşturma](../virtual-machine-scale-sets/quick-create-portal.md)
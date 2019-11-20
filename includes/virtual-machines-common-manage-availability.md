---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7c884d3c7102fc47f6efad86d9fe3704afd0edcf
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73591488"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>VM Yeniden Başlatma İşlemlerini Anlama - bakım ve kapalı kalma süresi
Azure 'da sanal makineye etkilenmesine neden olan üç senaryo vardır: planlanmamış donanım bakımı, beklenmedik kapalı kalma süresi ve planlı bakım.

* **Plansız Donanım Bakımı Olayı**, Azure platformu donanımın veya fiziksel makineyle ilişkili herhangi bir platform bileşeninin arıza yapmak üzere olduğunu tahmin ettiğinde gerçekleşir. Platform bir arıza öngördüğünde, donanımda barındırılan sanal makineler üzerindeki etkiyi azaltmak amacıyla plansız donanım bakımı olayı düzenler. Azure, sanal makineleri başarısız olan donanımdan sağlıklı fiziksel bir makineye geçirmek için [dinamik geçiş](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) teknolojisini kullanır. Dinamik Geçiş, Sanal Makineyi yalnızca kısa bir süre için duraklatan bir VM koruma işlemidir. Bellek, açık dosyalar ve ağ bağlantıları korunur, ancak olaydan önce ve/veya sonra performans azalabilir. Dinamik Geçişin kullanılamadığı durumlarda VM, aşağıda açıklanan Beklenmeyen Kapalı Kalma Süresi yaşar.


* **Beklenmedik kapalı kalma süresi** , sanal makinenin donanımının veya fiziksel altyapısının beklenmedik bir şekilde başarısız olmasına neden olur. Bu, yerel ağ arızalarını, yerel disk başarısızlıklarını veya diğer raf düzeyi başarısızlıklarını içerebilir. Algılandığında, Azure platformu sanal makinenizi aynı veri merkezinde sağlıklı bir fiziksel makineye otomatik olarak geçirir (toplar). İyileştirme yordamı sırasında sanal makineler kapalı kalır (yeniden başlatma) ve bazı durumlarda geçici sürücü kaybı yaşar. Bağlı işletim sistemi ve veri diskleri her zaman korunur.

  Sanal makineler Ayrıca, tüm veri merkezini veya tüm bölgeyi etkileyen bir kesinti veya olağanüstü durum durumunda kapalı kalma süresi yaşar. Azure, bu senaryolar için [kullanılabilirlik alanları](../articles/availability-zones/az-overview.md) ve [eşleştirilmiş bölgeler](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)dahil olmak üzere koruma seçenekleri sunar.

* **Planlı Bakım olayları**, Microsoft tarafından sanal makinelerinizin çalıştığı platforma ait genel güvenilirlik, performans ve güvenliği artırmak amacıyla temel alınan Azure platformunda yapılan periyodik güncelleştirmelerdir. Bu güncelleştirmelerin çoğu Sanal Makine veya Bulut Hizmetlerinizi etkilemeden gerçekleştirilir (bkz. [VM Koruyucu Bakım](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Azure platformu mümkün olan tüm durumlarda VM Koruyucu Bakımı kullanmaya çalışsa da, gerekli güncelleştirmelerin temel alınan altyapıya uygulanması için bu güncelleştirmelerin sanal makineyi yeniden başlatmayı gerektirdiği nadir örnekler vardır. Bu durumda, uygun zaman penceresi içinde VM’lere yönelik bakımı başlatarak Maintenance-Redeploy işlemi ile Azure Planlı Bakımını gerçekleştirebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler için Planlı Bakım](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Bu olayların bir veya daha fazlası nedeniyle kapalı kalma süresinin etkisini azaltmak için, sanal makinelerinizde aşağıdaki yüksek kullanılabilirlik en iyi uygulamalarının kullanılması önerilir:

* [Bir kullanılabilirlik kümesindeki birden fazla sanal makineyi yedeklilik için yapılandırma]
* [Bir kullanılabilirlik kümesindeki VM’ler için yönetilen diskleri kullanma]
* [VM etkileyen olaylara önceden yanıt vermek için zamanlanmış olayları kullanma](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Her uygulama katmanını ayrı kullanılabilirlik kümeleri halinde yapılandırma]
* [Yük Dengeleyiciyi kullanılabilirlik kümeleri ile birleştirme]
* [Veri merkezi düzeyindeki hatalardan korumak için kullanılabilirlik bölgelerini kullanma]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Veri merkezi düzeyindeki hatalardan korumak için kullanılabilirlik bölgelerini kullanma

[Kullanılabilirlik alanları](../articles/availability-zones/az-overview.md) , sanal makinelerinizdeki uygulamaların ve verilerin kullanılabilirliğini korumak için sahip olduğunuz denetim düzeyini genişletir. Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılık sağlamak için, tüm etkin bölgelerde en az üç ayrı bölge vardır. Bir bölgedeki Kullanılabilirlik Alanları fiziksel ayrımı, uygulamaları ve verileri veri merkezi hatalarından korur. Bölgesel olarak yedekli hizmetler, uygulamalarınızı ve verilerinizi Kullanılabilirlik Alanları arasında çoğaltarak hata noktalarından koruyun.

Bir Azure bölgesindeki kullanılabilirlik bölgesi bir **hata etki alanının** ve bir **güncelleştirme etki alanının**birleşimidir. Örneğin, bir Azure bölgesindeki üç bölgede üç veya daha fazla VM oluşturursanız, VM 'niz üç hata etki alanına ve üç güncelleştirme etki alanına etkili bir şekilde dağıtılır. Azure platformu, farklı bölgelerdeki VM 'Lerin aynı anda güncelleştirildiğinden emin olmak için bu dağıtımı güncelleştirme etki alanları genelinde tanır.

Azure, Kullanılabilirlik Alanları sayesinde sektörün en iyi% 99,99 VM çalışma süresi SLA 'sını sunmaktadır. Bölgelerde çoğaltılan VM 'Leri kullanma çözümlerinizi tasarlayarak, uygulamalarınızı ve verilerinizi bir veri merkezi kaybından koruyabilirsiniz. Bir bölge tehlikeye girerse, çoğaltılan uygulamalar ve veriler başka bir bölgede anında kullanılabilir.

![Kullanılabilirlik alanları](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Bir [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) veya [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM 'yi bir kullanılabilirlik alanına dağıtma hakkında daha fazla bilgi edinin.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Bir kullanılabilirlik kümesindeki birden fazla sanal makineyi yedeklilik için yapılandırma
Kullanılabilirlik kümeleri, VM artıklığı ve kullanılabilirliği sağlayan başka bir veri merkezi yapılandırması. Bir veri merkezi içindeki bu yapılandırma, planlı veya plansız bir bakım olayı sırasında en az bir sanal makinenin kullanılabilir olmasını ve% 99,95 Azure SLA 'sını karşılamasını sağlar. Daha fazla bilgi için bkz. [Sanal Makineler için SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Tek örnekli bir sanal makineyi bir kullanılabilirlik kümesinde tek başına bırakmaktan kaçının. Bu yapılandırmadaki VM 'Ler, tek bir VM 'nin [Azure Premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)'leri kullanıyor olması dışında, Azure planlı bakım OLAYLARı sırasında SLA garantisi ve yüz kesintilerine uygun değildir. Premium SSD 'ler kullanan tek VM 'Ler için Azure SLA 'Sı geçerlidir.

Kullanılabilirlik kümenizdeki her sanal makineye, temel alınan Azure platformu tarafından bir **güncelleme etki alanı** ve bir **hata etki alanı** atanır. Belirli bir kullanılabilirlik kümesi için, aynı anda yeniden başlatılabilecek sanal makine gruplarını ve temel alınan fiziksel donanımları göstermek üzere, kullanıcı tarafından yapılandırılabilen beş güncelleme etki alanı varsayılan olarak atanır (Resource Manager dağıtımları daha sonra en fazla 20 güncelleme etki alanı sağlayacak şekilde artırılabilir). Tek bir kullanılabilirlik kümesinde beşten fazla sanal makine yapılandırıldığında, altıncı sanal makine birinci sanal makine ile, yedinci sanal makine ikinci sanal makine ile aynı güncelleme etki alanına yerleştirilir ve yerleştirme işlemi bu düzende devam eder. Yeniden başlatılmakta olan güncelleme etki alanlarının sırası, planlanan bakım sırasında sıralı olarak uygulanmayabilir, ancak aynı anda yalnızca bir güncelleme etki alanı yeniden başlatılır. Yeniden başlatılmış bir güncelleme etki alanının kurtarılması için, farklı bir güncelleme etki alanında bakım başlatılmadan önce 30 dakika beklenir.

Hata etki alanları ortak bir güç kaynağı ve ağ anahtarını paylaşan sanal makine grubunu tanımlar. Varsayılan olarak, kullanılabilirlik kümenizde yapılandırılmış olan sanal makineler, Resource Manager dağıtımları için en fazla üç hata etki alanı (Klasik için iki etki alanı) arasında ayrılır. Sanal makinelerinizin bir kullanılabilirlik kümesine yerleştirilmesi uygulamanızı işletim sistemine veya uygulamaya özel hatalardan korumasa da, olası fiziksel donanım hatalarının, ağ kesintilerinin veya güç kesintilerinin etkilerini sınırlar.

<!--Image reference-->
   güncelleştirme etki alanı ve hata etki alanı yapılandırma](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png) kavramsal çizimini ![

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Bir kullanılabilirlik kümesindeki VM’ler için yönetilen diskleri kullanma
Şu anda yönetilmeyen disklere sahip VM’ler kullanıyorsanız, [Kullanılabilirlik Kümesindeki VM’leri Yönetilen Diskleri kullanacak şekilde dönüştürmeniz](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) önemle tavsiye edilir.

[Yönetilen diskler](../articles/virtual-machines/windows/managed-disks-overview.md), bir Kullanılabilirlik Kümesindeki VM disklerinin tek arıza noktalarından kaçınmak üzere birbirinden yeterince ayrılmasını sağlayarak Kullanılabilirlik Kümeleri için daha fazla güvenilirlik sunar. Bu, diskleri farklı depolama hata etki alanlarına (depolama kümeleri) otomatik olarak yerleştirerek ve bunları VM hata etki alanıyla hizalayarak yapar. Bir depolama hatası etki alanı, donanım veya yazılım arızası nedeniyle başarısız olursa, yalnızca depolama hatası etki alanındaki disklere sahip VM örneği başarısız olur.
![yönetilen diskler FDs](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Yönetilen kullanılabilirlik kümelerine yönelik arıza etki alanlarının sayısı bölgeye göre farklılık gösterir (bölge başına iki veya üç). Aşağıdaki tabloda bölge başına sayı gösterilmektedir

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

> Note: belirli koşullar altında, aynı Kullanılabilirlik alanının 2 VM 'nin bir parçası aynı FaultDomain ' ı paylaşmasından kaynaklanabilir. Bu, kullanılabilirlik kümesine gidip "hata etki alanı" sütununu denetleyerek onaylanır.
> Bu davranış, VM 'Ler dağıtıldığında aşağıdaki sıra meydana geldiğinde gözlemlenebilir:
> - 1\. VM 'yi dağıtma
> - 1\. VM 'yi durdur/serbest bırak
> - 2\. VM 'yi bu koşullarda dağıtın, 2. VM 'nin işletim sistemi diski, 1. VM ile aynı hata etki alanında oluşturulabilir ve bu nedenle 2. VM aynı FaultDomain etki alanına da eklenecektir. 
> Bu sorundan kaçınmak için, dağıtımları arasında VM 'nin durdurulması/serbest olmaması önerilir.

VM 'Leri yönetilmeyen disklerle kullanmayı planlıyorsanız, VM 'lerin sanal sabit disklerinin (VHD) [sayfa Blobları](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)olarak depolandığı depolama hesapları için aşağıdaki en iyi yöntemleri izleyin.

1. **Bir VM ile ilişkili tüm diskleri (işletim sistemi ve veri) aynı depolama hesabında tutma**
2. Bir depolama hesabına daha fazla VHD eklemeden önce **Depolama hesabındaki yönetilmeyen disk sayısına ilişkin [limitleri](../articles/storage/common/storage-scalability-targets.md) gözden geçirin**
3. **Bir Kullanılabilirlik Kümesindeki her VM için ayrı depolama hesabı kullanın.** Depolama hesaplarını aynı Kullanılabilirlik Kümesinde birden fazla VM ile paylaşmayın. Yukarıdaki en iyi uygulamalardan ![, yönetilmeyen diskler](./media/virtual-machines-common-manage-availability/umd-updated.png) ardından, depolama hesaplarını paylaşmak için farklı kullanılabilirlik kümelerindeki VM 'Ler için kabul edilebilir.

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>VM etkileyen olayları önceden yanıtlamak için zamanlanmış olayları kullanma

[Zamanlanan olaylara](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)abone olduğunuzda, VM 'NIZ, VM 'nizi etkileyebilecek yaklaşan bakım olayları hakkında bilgilendirilir. Zamanlanan olaylar etkinleştirildiğinde, bakım etkinliği gerçekleştirilmeden önce sanal makinenize en az bir süre verilir. Örneğin, VM 'nizi etkileyebilecek ana bilgisayar işletim sistemi güncelleştirmeleri, etkiyi belirten olaylar olarak, aynı zamanda hiçbir işlem gerçekleştirilmediği zaman bakımın gerçekleştirileceği bir süre kadar sıraya alınır. Zamanlama olayları, Azure, VM 'nizi etkileyebilecek mini donanım hatası algıladığında de sıraya alınır ve bu durum, iyileştirmenin ne zaman gerçekleştirilmesi gerektiğine karar vermenize olanak tanır. Müşteriler, durumu kaydetme, ikinciye yük devretme, vb. gibi bakım öncesinde görev gerçekleştirmek için olayını kullanabilir. Bakım olayını düzgün bir şekilde işleme için mantığınızı tamamladıktan sonra, platformun bakım ile devam etmesini sağlamak için bekleyen zamanlanmış olayını onaylayabilirsiniz.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Her uygulama katmanını ayrı kullanılabilirlik bölgeleri veya kullanılabilirlik kümelerine göre yapılandırma
Sanal makineleriniz neredeyse aynıysa ve uygulamanız için aynı amaca sahipseniz, uygulamanızın her katmanı için bir kullanılabilirlik alanı veya kullanılabilirlik kümesi yapılandırmanız önerilir.  Aynı Kullanılabilirlik bölgesine veya kümesine iki farklı katman yerleştirirseniz, aynı uygulama katmanındaki tüm sanal makineler aynı anda yeniden başlatılabilir. Bir kullanılabilirlik alanında en az iki sanal makineyi yapılandırarak veya her katman için ayarlanmış olarak, her katmanda en az bir sanal makinenin kullanılabilir olduğundan emin olursunuz.

Örneğin, tüm sanal makineleri IIS, Apache ve NGINX çalıştıran uygulamanızın ön ucuna tek bir kullanılabilirlik alanına veya kümesine yerleştirebilirsiniz. Yalnızca ön uç sanal makinelerin aynı Kullanılabilirlik alanına yerleştirildiğinden veya ayarlanmış olduğundan emin olun. Benzer şekilde, çoğaltılan SQL Server sanal makineleriniz ya da MySQL sanal makineleriniz gibi yalnızca veri katmanı sanal makinelerinin kendi kullanılabilirlik bölgesine veya kümesine yerleştirildiğinden emin olun.

<!--Image reference-->
   Uygulama katmanlarını ![](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Bir yük dengeleyiciyi kullanılabilirlik alanları veya kümeleriyle birleştirme
[Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) bir kullanılabilirlik bölgesi ile birleştirin veya en fazla uygulama dayanıklılığı sağlamak için ayarlayın. Azure Load Balancer, birden fazla sanal makine arasında trafiği dağıtır. Standart katman sanal makinelerimize Azure Load Balancer dahildir. Tüm sanal makine katmanları Azure Load Balancer hizmetini içermez. Sanal makinelerinizde yük dengeleme hakkında daha fazla bilgi için bkz. [Sanal makinelerde yük dengeleme](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Yük dengeleyici birden fazla sanal makine arasında trafiği dengeleyecek şekilde yapılandırılmamışsa, planlı bakım olayları yalnızca trafik sunan sanal makineyi etkiler ve uygulama katmanınızda kesintiye neden olur. Aynı yük dengeleyici ve kullanılabilirlik kümesi altına aynı katmandaki birden fazla sanal makinenin yerleştirilmesi, trafiğin en az bir örnek tarafından sürekli olarak sunulmasını sağlar.

Kullanılabilirlik alanları arasında yük dengelemeye yönelik bir öğretici için bkz. [Azure CLI kullanarak VM 'leri tüm kullanılabilirlik bölgelerinde Yük Dengeleme](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md).


<!-- Link references -->
[Bir kullanılabilirlik kümesindeki birden fazla sanal makineyi yedeklilik için yapılandırma]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Her uygulama katmanını ayrı kullanılabilirlik kümeleri halinde yapılandırma]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Yük Dengeleyiciyi kullanılabilirlik kümeleri ile birleştirme]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Bir kullanılabilirlik kümesindeki VM’ler için yönetilen diskleri kullanma]: #use-managed-disks-for-vms-in-an-availability-set
[Veri merkezi düzeyindeki hatalardan korumak için kullanılabilirlik bölgelerini kullanma]: #use-availability-zones-to-protect-from-datacenter-level-failures

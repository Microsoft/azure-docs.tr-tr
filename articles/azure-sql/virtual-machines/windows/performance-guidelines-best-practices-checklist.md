---
title: 'Denetim listesi: performans en iyi yöntemleri & yönergeleri'
description: Azure sanal makinesinde (VM) SQL Server performansını iyileştirmek için en iyi uygulamalarınızı ve yönergeleri gözden geçirmek üzere hızlı bir denetim listesi sağlar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 84f2f4f679de80cd9b5fc986d40e084bae8a4cad
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313768"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Denetim listesi: Azure VM 'lerinde SQL Server için en iyi performans uygulamaları
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, Azure sanal makinelerinde (VM 'Ler) SQL Server performansını iyileştirmek için en iyi uygulamalar ve yönergeler dizisi olarak hızlı bir denetim listesi sunulmaktadır. 

Kapsamlı Ayrıntılar için, bu serinin diğer makalelerine bakın: [VM boyutu](performance-guidelines-best-practices-vm-size.md), [depolama](performance-guidelines-best-practices-storage.md), [temel toplama](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="overview"></a>Genel Bakış

Azure sanal makinelerinde SQL Server çalıştırılırken, şirket içi sunucu ortamlarında SQL Server için geçerli olan veritabanı performansı ayarlama seçeneklerini kullanmaya devam edin. Ancak, bir genel buluttaki ilişkisel veritabanının performansı, bir sanal makinenin boyutu ve veri disklerinin yapılandırması gibi birçok faktöre bağlıdır.

Genellikle maliyetleri iyileştirmek ve performansı iyileştirmek arasında bir denge vardır. Bu performans en iyi yöntemler serisi, Azure sanal makinelerinde SQL Server için *en iyi* performansı elde etmek üzere odaklanır. İş yükünüz daha az güç alıyorsa, önerilen en iyi duruma getirme gerektirmeyebilirsiniz. Bu önerileri değerlendirdiğiniz için performans ihtiyaçlarınızı, maliyetlerinizi ve iş yükü desenlerini göz önünde bulundurun.

## <a name="vm-size"></a>VM Boyutu

Aşağıda, Azure VM 'de SQL Server çalıştırmak için en iyi VM boyutu uygulamalarının hızlı bir denetim listesi verilmiştir: 

- [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)veya [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ya da üzeri gibi 4 veya daha fazla vCPU ile VM boyutlarını kullanın. 
- SQL Server iş yüklerinin en iyi performansı için [bellek için iyileştirilmiş](../../../virtual-machines/sizes-memory.md) sanal makine boyutlarını kullanın. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) serisi, [d-](../../../virtual-machines/m-series.md)ve [Mv2](../../../virtual-machines/mv2-series.md) serisi, OLTP Iş yükleri için gereken en iyi bellekten Vcore oranını sunmaktadır. Her iki e serisi sanal makine, görev açısından kritik iş yükleri için gereken en yüksek bellek-vCore oranını sağlar ve ayrıca veri ambarı iş yükleri için de idealdir. 
- Görev açısından kritik ve veri ambarı iş yükleri için daha yüksek bir bellek-sanal çekirdek oranı düşünün. 
- SQL Server ayarları ve depolama seçenekleri en iyi SQL Server performans için yapılandırıldığı için Azure sanal makine marketi görüntülerini kullanın. 
- Hedef iş yükünün performans özelliklerini toplayın ve işletmeniz için uygun VM boyutunu tespit etmek üzere bunları kullanın.

Daha fazla bilgi edinmek için bkz. kapsamlı [VM boyutu en iyi uygulamaları](performance-guidelines-best-practices-vm-size.md). 

## <a name="storage"></a>Depolama

Aşağıda, Azure VM 'de SQL Server çalıştırmaya yönelik depolama yapılandırması için en iyi uygulamalar hakkında hızlı bir denetim listesi verilmiştir: 

- Disk türünü seçmeden önce, uygulamayı izleyin ve SQL Server Data, log ve tempdb dosyaları için [depolama bant genişliği ve gecikme süresi gereksinimlerini saptayın](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) . 
- Depolama performansını iyileştirmek için, kullanılabilir önbelleğe alınmamış en yüksek ıOPS 'yi planlayın ve [sanal makine ve disklerin azalmasını/azaltmasını](../../../virtual-machines/premium-storage-performance.md#throttling)önleyerek veri okuma işlemleri için performans özelliği olarak veri önbelleğe alma özelliğini kullanın.
- Verileri, günlüğü ve tempdb dosyalarını ayrı sürücülere yerleştirin.
    - Veri sürücüsü için, önbellek desteğinin kullanılabilirliğini sağlamak üzere yalnızca [Premium P30 ve P40 disklerini](../../../virtual-machines/disks-types.md#premium-ssd) kullanın
    - Kapasite ve test performansı için günlük sürücüsü planı, [Premium P30-P80 disklerini](../../../virtual-machines/disks-types.md#premium-ssd)değerlendirirken maliyet ile karşılaştırıldığında.
      - Alt milisaniyelik depolama gecikmesi gerekliyse, işlem günlüğü için [Azure Ultra diskler](../../../virtual-machines/disks-types.md#ultra-disk) ' i kullanın. 
      - D serisi sanal makine dağıtımları için, Azure Ultra disklerini kullanarak [yazma Hızlandırıcısı](../../../virtual-machines/how-to-enable-write-accelerator.md) göz önünde bulundurun.
    - En [](/sql/relational-databases/databases/tempdb-database) `D:\` iyi VM boyutunu seçtikten sonra, çoğu SQL Server iş yükü için tempdb 'yi yerel kısa ömürlü SSD sürücüsüne yerleştirin. 
      - Yerel sürücünün kapasitesi tempdb için yeterli değilse, VM 'yi boyutlandırmayı göz önünde bulundurun. Daha fazla bilgi için bkz. [veri dosyası önbelleğe alma ilkeleri](performance-guidelines-best-practices-storage.md#data-file-caching-policies) .
- Hedef sanal makinenin ıOPS ve aktarım hızı sınırlarına kadar g/ç bant genişliğini artırmak için [depolama alanlarını](/windows-server/storage/storage-spaces/overview) kullanarak birden çok Azure veri diski Stripe.
- Veri dosyası diskleri için [konak önbelleğini](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) salt okunurdur olarak ayarlayın.
- Günlük dosyası diskleri için [konak önbelleğe almayı](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) hiçbiri olarak ayarlayın.
    - SQL Server dosyaları içeren disklerde okuma/yazma önbelleğini etkinleştirmeyin. 
    - Diskinizin önbellek ayarlarını değiştirmeden önce SQL Server hizmetini her zaman durdurun.
- Geliştirme ve test iş yükleri için standart depolama kullanmayı düşünün. Üretim iş yükleri için Standart HDD/SDD kullanılması önerilmez.
- [Kredi tabanlı disk](../../../virtual-machines/disk-bursting.md#credit-based-bursting) patlaması (P1-P20) yalnızca daha küçük geliştirme/test iş yükleri ve departman sistemleri için düşünülmelidir.
- Depolama hesabını SQL Server VM aynı bölgede sağlayın. 
- Azure coğrafi olarak yedekli depolamayı (coğrafi çoğaltma) devre dışı bırakın ve depolama hesabında LRS (yerel yedekli depolama) kullanın.
- Veri diskinizi, geçici `D:\` sürücü dışında (varsayılan 4 KB 'ye sahiptir) bir sürücüye yerleştirilmiş tüm veri dosyaları için 64 KB ayırma birimi boyutu kullanacak şekilde biçimlendirin. Azure Marketi aracılığıyla dağıtılan SQL Server VM 'Ler, depolama havuzu boyutu 64 KB olarak ayarlanan ayırma birimi boyutu ve ayırma ile biçimlendirilen veri disklerinde gelir. 

Daha fazla bilgi için bkz. kapsamlı [depolama en iyi uygulamaları](performance-guidelines-best-practices-storage.md). 


## <a name="azure--sql-feature-specific"></a>Azure & SQL özelliğine özgü

Aşağıda, Azure VM 'de SQL Server çalıştırırken SQL Server ve Azure 'a özgü yapılandırmalara yönelik en iyi yöntemler için hızlı bir denetim listesi verilmiştir: 

- Birçok [özellik avantajlarının](sql-server-iaas-agent-extension-automate-management.md#feature-benefits)kilidini açmak Için [SQL IaaS Aracısı uzantısına](sql-agent-extension-manually-register-single-vm.md) kaydolun. 
- Veritabanı sayfa sıkıştırmasını etkinleştirin.
- Veri dosyaları için anlık dosya başlatmayı etkinleştirin.
- Veritabanının otomatik büyümesini sınırlayın.
- Veritabanının bir kısmını daraltmaya devre dışı bırakın.
- Tüm veritabanlarını sistem veritabanları dahil olmak üzere veri disklerine taşıyın.
- SQL Server hata günlüğünü ve izleme dosyası dizinlerini veri disklerine taşıyın.
- Varsayılan yedekleme ve veritabanı dosya konumlarını yapılandırın.
- [Bellekte kilitli sayfaları etkinleştirin](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- SQL Server yüklü sürümü için [en son toplu güncelleştirmeleri](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) değerlendirin ve uygulayın.
- Doğrudan Azure Blob Storage 'a yedekleyin.
- Birden çok [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) dosyası, çekirdek başına 1 dosya, en fazla 8 dosya kullanın.



## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için bu serideki diğer makalelere göz atın:
- [VM boyutu](performance-guidelines-best-practices-vm-size.md)
- [Depolama](performance-guidelines-best-practices-storage.md)
- [Taban çizgisini topla](performance-guidelines-best-practices-collect-baseline.md)

En iyi güvenlik uygulamaları için bkz. [Azure sanal makinelerinde SQL Server Için güvenlik konuları](security-considerations-best-practices.md).

[Azure sanal makinelerine genel bakış ' a SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)diğer SQL Server sanal makine makalelerini inceleyin. SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](frequently-asked-questions-faq.md) bölümüne bakın.

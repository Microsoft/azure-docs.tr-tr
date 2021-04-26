---
title: 'VM boyutu: performans en iyi yöntemleri & yönergeleri'
description: ', Azure sanal makinesinde (VM) SQL Server performansını iyileştirmek için VM boyut yönergelerini ve en iyi yöntemleri sağlar.'
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 88adef7ea50744f913780d99594ce3baadade84b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600905"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM boyutu: Azure VM 'lerinde SQL Server için en iyi performans uygulamaları
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, Azure sanal makinelerinde (VM) SQL Server performansını en iyi hale getirmeye yönelik bir dizi en iyi uygulama ve yönergeler sunan VM boyut Kılavuzu sunulmaktadır.

Genellikle maliyetleri iyileştirmek ve performansı iyileştirmek arasında bir denge vardır. Bu performans en iyi yöntemler serisi, Azure sanal makinelerinde SQL Server için *en iyi* performansı elde etmek üzere odaklanır. İş yükünüz daha az güç alıyorsa, önerilen en iyi duruma getirme gerektirmeyebilirsiniz. Bu önerileri değerlendirdiğiniz için performans ihtiyaçlarınızı, maliyetlerinizi ve iş yükü desenlerini göz önünde bulundurun.


## <a name="checklist"></a>Denetim Listesi

Makalenin geri kalanının daha ayrıntılı bir şekilde ele aldığı VM boyutu en iyi uygulamalarına kısa bir genel bakış için aşağıdaki denetim listesini gözden geçirin: 

- [Standard_M8-4ms](/azure/virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)veya [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ya da üzeri gibi 4 veya daha fazla vCPU ile VM boyutlarını kullanın. 
- SQL Server iş yüklerinin en iyi performansı için [bellek için iyileştirilmiş](../../../virtual-machines/sizes-memory.md) sanal makine boyutlarını kullanın. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) serisi, [d-](/azure/virtual-machines/m-series)ve [Mv2](../../../virtual-machines/mv2-series.md) serisi, OLTP Iş yükleri için gereken en iyi bellekten Vcore oranını sunmaktadır. Her iki e serisi sanal makine, görev açısından kritik iş yükleri için gereken en yüksek bellek-vCore oranını sağlar ve ayrıca veri ambarı iş yükleri için de idealdir. 
- Görev açısından kritik ve veri ambarı iş yükleri için daha yüksek bir bellek-sanal çekirdek oranı düşünün. 
- SQL Server ayarları ve depolama seçenekleri en iyi SQL Server performans için yapılandırıldığı için Azure sanal makine marketi görüntülerinden yararlanın. 
- Hedef iş yükünün performans özelliklerini toplayın ve işletmeniz için uygun VM boyutunu tespit etmek üzere bunları kullanın.

VM boyutu denetim listesini başkalarıyla karşılaştırmak için, kapsamlı [performans en iyi yöntemleri denetim listelerine](performance-guidelines-best-practices-checklist.md)bakın. 

## <a name="overview"></a>Genel Bakış

Azure VM 'de bir SQL Server oluştururken, gereken iş yükünün türünü dikkatle göz önünde bulundurun. Mevcut bir ortamı geçiriyorsanız, Azure VM gereksinimlerini SQL Server için [bir performans temeli toplayın](performance-guidelines-best-practices-collect-baseline.md) . Bu yeni bir sanal makine ise, satıcı gereksinimlerinize göre yeni SQL Server VM oluşturun. 

Bulut için oluşturulmuş yeni bir uygulamayla yeni bir SQL Server VM oluşturuyorsanız, verileriniz ve kullanım gereksinimleriniz geliştikçe SQL Server VM kolayca boyut getirebilirsiniz.
Düşük katmanlı D serisi, B serisi veya AV2 serisi ile geliştirme ortamlarını başlatın ve ortamınızı zaman içinde büyütün. 

Portalda depolama yapılandırmasıyla SQL Server VM Market görüntülerini kullanın. Bu, iş yükleriniz için gereken boyut, ıOPS ve aktarım hızını almak için gereken depolama havuzlarını doğru şekilde oluşturmayı kolaylaştırır. Premium Depolama ve Premium Depolama önbelleğini destekleyen SQL Server VM 'Leri seçmek önemlidir. Daha fazla bilgi edinmek için [depolama](performance-guidelines-best-practices-storage.md) makalesine bakın. 

Üretim OLTP ortamı için önerilen minimum değer 4 sanal çekirdek, 32 GB bellek ve 8 ' in bellekten sanal çekirdek oranına sahiptir. Yeni ortamlar için 4 sanal çekirdek makinelerle başlayın ve veri ve işlem gereksinimleriniz değiştiğinde 8, 16, 32 sanal çekirdek veya daha fazlasını ölçeklendirin. OLTP üretilen iş için, her vCore için 5000 ıOPS 'ye sahip VM 'Leri hedef SQL Server. 

SQL Server veri ambarı ve görev açısından kritik ortamların genellikle 8 bellekten sanal çekirdek oranının ötesine ölçeklendirilmesi gerekir. Orta ortamlar için, daha büyük veri ambarı ortamları için bir 16 bellekten sanal çekirdek oranı ve 32 bellekten sanal çekirdek oranı seçmek isteyebilirsiniz. 

SQL Server veri ambarı ortamları, genellikle daha büyük makinelerin paralel işlenmesinden faydalanır. Bu nedenle, d serisi ve Mv2 serisi daha büyük veri ambarı ortamları için güçlü seçeneklerdir.

Geçerli bir şirket içi SQL Server veritabanını Azure VM 'lerinde SQL Server geçirmek için temel olarak kaynak makinenizden vCPU ve bellek yapılandırmasını kullanın. [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) avantajlarından yararlanmak ve SQL Server lisanslama maliyetlerine kaydetmek için çekirdek lisansınızı Azure 'a taşıyın.

## <a name="memory-optimized"></a>Bellek için iyileştirilmiş

[Bellek için iyileştirilmiş sanal makine boyutları](../../../virtual-machines/sizes-memory.md) SQL Server VM 'ler için birincil hedeftir ve Microsoft tarafından önerilen seçenektir. Bellek için iyileştirilmiş sanal makineler, daha güçlü bellek-CPU oranları ve orta-büyük önbellek seçenekleri sunar. 

### <a name="m-mv2-and-mdsv2-series"></a>D, Mv2 ve Mdsv2 serisi

[B serisi](/azure/virtual-machines/m-series) , en büyük SQL Server iş yükleri Için sanal çekirdek sayısı ve belleği sunar.  

[Mv2 serisi](../../../virtual-machines/mv2-series.md) , en yüksek sanal çekirdek sayısına ve belleğe sahiptir ve görev açısından kritik ve veri ambarı iş yükleri için önerilir. Mv2 serisi örnekler, büyük bellek içi veritabanlarını ve iş yüklerini, ilişkisel veritabanı sunucuları, büyük önbellekler ve bellek içi analizler için ideal olan yüksek bellekle CPU oranıyla desteklemek üzere benzersiz işlem performansı sağlayan bellek için iyileştirilmiş VM boyutlarıdır.

[Standard_M64ms](/azure/virtual-machines/m-series) , örneğin 28 bellekten sanal çekirdek oranına sahiptir.

[Mdsv2 orta bellek serisi](../../..//virtual-machines/msv2-mdsv2-series.md) , şu anda [Önizleme](https://aka.ms/Mv2MedMemoryPreview) aşamasında olan ve orta düzey bir bellek teklifiyle bir dizi d serisi Azure sanal makinesi sunan yeni bir d serisi. Bu makineler en az 30 ' a kadar bellekten sanal çekirdek desteğine sahip SQL Server iş yükleri için en uygun seçenektir.

D ve Mv2 serisi özelliklerinin bazıları SQL Server performansa yönelik olarak, [Premium Depolama](../../../virtual-machines/premium-storage-performance.md) ve [Premium depolama önbelleğe alma](../../../virtual-machines/premium-storage-performance.md#disk-caching) desteği, [Ultra disk](../../../virtual-machines/disks-enable-ultra-ssd.md) desteği ve [yazma hızlandırmasını](../../../virtual-machines/how-to-enable-write-accelerator.md)kapsar.

### <a name="edsv4-series"></a>Edsv4 serisi

[Edsv4 serisi](../../../virtual-machines/edv4-edsv4-series.md) , bellek yoğun uygulamalar için tasarlanmıştır. Bu VM 'Lerde büyük bir yerel depolama SSD kapasitesi, güçlü yerel disk ıOPS ve 504 GiB 'a kadar RAM vardır. Bu sanal makinelerin çoğunda, standart SQL Server iş yükleri için ideal olan her bir vCore için neredeyse tutarlı bir 8 GiB bellek vardır. 

Bu grupta, 80 sanal çekirdek, 504 GB bellek sunan ve 6 ' nın bellekten vCore oranıyla birlikte [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) yeni bir sanal makine vardır. Bu sanal makine, [yalıtılmış](../../../virtual-machines/isolation.md) olduğundan, konakta çalışan tek sanal makine olduğu garanti edildiği ve bu nedenle diğer müşteri iş yüklerinden yalıtılmış olduğu için bir önemli. Bu, SQL Server için önerilenden daha düşük olan bellekten vCore oranına sahiptir, bu nedenle yalnızca yalıtımın gerekliyse kullanılması gerekir.

Edsv4 serisi sanal makineler [Premium depolamayı](../../../virtual-machines/premium-storage-performance.md)ve [Premium depolama önbelleği](../../../virtual-machines/premium-storage-performance.md#disk-caching)'ni destekler.

### <a name="dsv2-series-11-15"></a>DSv2 serisi 11-15

[DSv2 serisi 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) , önceki D serisi ile aynı bellek ve disk yapılandırmalarına sahiptir. Bu seride, tüm sanal makinelerde 7 ' nin tutarlı, bellekten CPU oranı vardır. Bu, bellek için iyileştirilmiş serinin en küçüğü ve giriş düzeyi SQL Server iş yükleri için iyi bir düşük maliyetli bir seçenektir.

[DSv2 serisi 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) , [Premium Depolama](../../../virtual-machines/premium-storage-performance.md) ve [Premium Depolama önbelleğini](../../../virtual-machines/premium-storage-performance.md#disk-caching)destekler; bu, en iyi performans için önerilir.

## <a name="general-purpose"></a>Genel amaçlı

[Genel amaçlı sanal makine boyutları](../../../virtual-machines/sizes-general.md) , geliştirme ve test, Web sunucuları ve daha küçük veritabanı sunucuları gibi daha küçük giriş düzeyi iş yükleri için dengeli bellekten sanal çekirdek oranları sağlamak üzere tasarlanmıştır. 

Genel amaçlı sanal makinelerle daha küçük olan bellekten sanal çekirdek oranları nedeniyle, SQL Server bellek tabanlı performans sayaçlarını dikkatle izlemek önemlidir, çünkü bu, gereken arabellek önbelleği belleğini. Daha fazla bilgi için bkz. [bellek performansı temeli](performance-guidelines-best-practices-collect-baseline.md#memory) . 

Üretim iş yükleri için başlangıç önerisi 8 ' in bellekten sanal çekirdek oranı olduğundan, SQL Server çalıştıran bir genel amaçlı VM için önerilen en düşük yapılandırma 4 vCPU ve 32 GB bellek olur. 

### <a name="ddsv4-series"></a>Ddsv4 serisi

[Ddsv4-Series](../../../virtual-machines/ddv4-ddsv4-series.md) , vCPU, bellek ve geçici disk için, ancak daha küçük bellekten sanal çekirdek desteğiyle bir bileşim sunar. 

Ddsv4 VM 'Leri, daha düşük gecikme süresi ve daha yüksek hızlı yerel depolama içerir.

Bu makineler, geçici depolama ve departman ilişkisel veritabanlarına hızlı erişim gerektiren yan yana SQL ve uygulama dağıtımları için idealdir. Bu serideki tüm sanal makineler arasında standart bir bellek-sanal çekirdek oranı vardır. 

Bu nedenle, bu serideki, 8 sanal çekirdekler ve 32 GB bellek içeren başlangıç sanal makinesi olarak D8ds_v4 kullanmanız önerilir. En büyük makine, 64 sanal çekirdek ve 256 GB bellek içeren D64ds_v4.

[Ddsv4 serisi](../../../virtual-machines/ddv4-ddsv4-series.md) sanal makineler, [Premium Depolama](../../../virtual-machines/premium-storage-performance.md) ve [Premium Depolama önbelleğini](../../../virtual-machines/premium-storage-performance.md#disk-caching)destekler.

> [!NOTE]
> [Ddsv4 serisi](../../../virtual-machines/ddv4-ddsv4-series.md) , SQL Server iş yükleri için önerilen 8 ' ın bellekten sanal çekirdek oranına sahip değildir. Bu nedenle, bu sanal makinelerin yalnızca daha küçük uygulama ve geliştirme iş yükleri için kullanılması düşünülmektedir.

### <a name="b-series"></a>B serisi

[Burstable B serisi](../../../virtual-machines/sizes-b-series-burstable.md) sanal makine boyutları, kavram kanıtı ve çok küçük uygulama ve geliştirme sunucuları gibi tutarlı performansa gerek olmayan iş yükleri için idealdir. 

[Burstable B serisi](../../../virtual-machines/sizes-b-series-burstable.md) sanal makine boyutlarının çoğu, 4 ' ün bellekten Vcore oranına sahiptir. Bu makinelerin en büyüğü 20 sanal çekirdek ve 80 GB bellek içeren [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) .

Bu seri, uygulamalar makine boyutuna bağlı olarak değişen Burstable kredilerle iş saatlerinde **veri bloğu** yapabilme özelliğine sahip olduğu için benzersizdir. 

Krediler tükendiğinde VM, taban çizgisi makine performansına geri döner.

B serisinin avantajı, özellikle de işlem gücü için gün boyunca gerekli olan diğer VM boyutlarına göre elde edebileceğiniz işlem tasarruflarıdır.

Bu seri [Premium depolamayı](../../../virtual-machines/premium-storage-performance.md)destekler, ancak  [Premium Depolama önbelleğini](../../../virtual-machines/premium-storage-performance.md#disk-caching)desteklemez.

> [!NOTE] 
> [Burstable B serisi](../../../virtual-machines/sizes-b-series-burstable.md) , SQL Server iş yükleri için önerilen 8 ' ın bellekten sanal çekirdek oranına sahip değildir. Bu nedenle, bu sanal makineleri yalnızca daha küçük uygulamalar, Web sunucuları ve geliştirme iş yükleri için kullanmayı göz önünde bulundurun.

### <a name="av2-series"></a>Av2 Serisi

[AV2 serisi](../../../virtual-machines/av2-series.md) VM 'ler, geliştirme ve test, düşük trafikli web sunucuları, küçük ve orta ölçekli uygulama veritabanları ve kavram kanıtı gibi giriş düzeyi iş yükleri için idealdir.

Yalnızca [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 sanal çekirdek ve 16GB bellek), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 sanal çekirdek ve 32gb bellek) ve [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 sanal çekirdek ve 64 GB bellek), bu en önemli üç sanal makine Için 8 ' in bellek-Vcore oranına sahiptir. 

Bu sanal makineler, daha küçük geliştirme ve test SQL Server makinelere yönelik iyi seçeneklerdir. 

8 sanal çekirdek [Standard_A8m_v2](../../../virtual-machines/av2-series.md) , küçük uygulama ve Web sunucuları için de iyi bir seçenek olabilir.

> [!NOTE] 
> AV2 serisi, Premium depolamayı desteklemez ve bu nedenle, bellekten sanal çekirdek oranına sahip sanal makinelerle birlikte üretim SQL Server iş yükleri için önerilmez.

## <a name="storage-optimized"></a>Depolama için iyileştirilmiş

[Depolama için IYILEŞTIRILMIŞ VM boyutları](../../../virtual-machines/sizes-storage.md) belirli kullanım örneklerine yöneliktir. Bu sanal makineler özellikle iyileştirilmiş disk aktarım hızı ve GÇ ile tasarlanmıştır. 

### <a name="lsv2-series"></a>Lsv2 serisi

[Lsv2 serisi](../../../virtual-machines/lsv2-series.md) , yüksek aktarım hızı, düşük gecikme süresi ve yerel NVMe depolama özellikleri. Lsv2 serisi VM 'Ler, dayanıklı veri disklerini kullanmak yerine doğrudan VM 'ye eklenen düğümdeki yerel diski kullanacak şekilde iyileştirilmiştir. 

Bu sanal makineler, büyük veri, veri ambarı, raporlama ve ETL iş yükleri için güçlü seçeneklerdir. Yerel NVMe depolamanın yüksek aktarım hızı ve ıOPS, veritabanınıza yüklenecek dosyaları ve verilerin kaynak sistemden ya da Azure Blob depolama veya Azure Data Lake gibi diğer depolardan yeniden oluşturulabilen diğer senaryolara yönelik olarak işlenmesi için iyi bir kullanım durumdur. [Lsv2 serisi](../../../virtual-machines/lsv2-series.md) VM 'Ler aynı anda en fazla 30 dakika boyunca disk performansını da aşırı alabilir.

Bu sanal makineler 1,92, her bir vCPU için 8 GiB bellek ve her 8 vCPU için 8 TB 'lık NVMe SSD ile 80 vCPU arasında olur. Bu, [L80s_v2](../../../virtual-machines/lsv2-series.md), bu serinin en büyük VM 'si için, 10 gb 'Lık 80 sanal cpu ve 640 Bib belleği, 10 x 1.92 TB olan NVMe depolaması ile aynıdır.  Bu sanal makinelerin tümünde 8 ' e kadar tutarlı bir bellek-vCore oranı vardır.

NVMe depolaması, sanal makinenizi serbest bırakırsanız veya hizmet düzeltme için farklı bir konağa taşınırsa bu disklerde verilerin kaybedilmesi anlamına gelir.

Lsv2 ve LS Serisi [Premium](../../../virtual-machines/premium-storage-performance.md)depolamayı destekler, ancak Premium depolama önbelleğe alma işlemini desteklemez. IOPS 'yi artırmak için yerel bir önbelleğin oluşturulması desteklenmez. 

> [!WARNING]
> Veri dosyalarınızı kısa ömürlü NVMe depolamada depolamak, VM serbest bırakıldığında veri kaybına neden olabilir. 

## <a name="constrained-vcores"></a>Kısıtlanmış sanal çekirdekler

Yüksek performanslı SQL Server iş yükleri genellikle daha yüksek miktarda bellek, g/ç ve aktarım hızı daha yüksektir. 

Birçok OLTP iş yükü, çok sayıda küçük işlem tarafından yönetilen uygulama veritabanlarıdır. OLTP iş yükleri sayesinde, verilerin yalnızca küçük bir miktarı okunmakta veya değiştirilir, ancak kullanıcı sayılarına göre çalışan işlem birimleri çok daha yüksektir. SQL Server belleğin önbelleğe almak için kullanılabilir olması, en son erişilen verileri performans için depolaması ve fiziksel okumaların belleğe hızla okunabileceği emin olmanız önemlidir. 

Bu OLTP ortamlarında daha yüksek miktarda bellek, hızlı depolama ve en iyi şekilde gerçekleştirmek için gereken g/ç bant genişliği gerekir. 

Daha yüksek SQL Server lisanslama maliyetleri olmadan bu performans düzeyini korumak için Azure, [Kısıtlanmış vCPU sayısı](../../../virtual-machines/constrained-vcpu.md)ile VM boyutları sunar. 

Bu, üst sanal makinenin aynı bellek, depolama ve g/ç bant genişliğini koruyarak, kullanılabilir sanal çekirdekleri azaltarak lisanslama maliyetlerini denetlemeye yardımcı olur.

VCPU sayısı, özgün VM boyutunun dörtte birini bir buçuk ile kısıtlanıyor olabilir. Sanal makine için kullanılabilir olan sanal çekirdekleri azaltmak, daha yüksek bellek-vCore oranlarına sahip olur, ancak işlem maliyeti aynı kalacaktır.

Bu yeni VM boyutları, daha kolay tanımlanabilmesi için etkin vCPU sayısını belirten bir soneke sahiptir. 

Örneğin, [M64-32ms](../../../virtual-machines/constrained-vcpu.md) 32 yalnızca bellek, g/ç ve [M64ms](/azure/virtual-machines/m-series) aktarım hızı ile [M64-16Ms](../../../virtual-machines/constrained-vcpu.md) yalnızca 16 sanal çekirdekleri için lisanslama gerektirir SQL Server yalnızca lisanslama gerektirir.  [M64-16ms](../../../virtual-machines/constrained-vcpu.md) 'nin M64ms lisans maliyetine ait bir SQL Server çeyreği olsa da, sanal makinenin işlem maliyeti aynı olacaktır.

> [!NOTE] 
> - Orta ila büyük veri ambarı iş yükleri, [kısıtlı vCore sanal](../../../virtual-machines/constrained-vcpu.md)makinelerinden yine de faydalanabilir, ancak veri ambarı iş yükleri genellikle daha az Kullanıcı ve paralel olarak çalışan sorgu planları aracılığıyla daha büyük miktarlarda veri adresleyen süreçler tarafından belirlenir. 
> - İşletim sistemi lisansını içeren işlem maliyeti, üst sanal makineyle aynı kalacaktır. 



## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için bu serideki diğer makalelere göz atın:
- [Hızlı denetim listesi](performance-guidelines-best-practices-checklist.md)
- [Depolama](performance-guidelines-best-practices-storage.md)
- [Taban çizgisini topla](performance-guidelines-best-practices-collect-baseline.md)

En iyi güvenlik uygulamaları için bkz. [Azure sanal makinelerinde SQL Server Için güvenlik konuları](security-considerations-best-practices.md).

[Azure sanal makinelerine genel bakış ' a SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)diğer SQL Server sanal makine makalelerini inceleyin. SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](frequently-asked-questions-faq.md) bölümüne bakın. 

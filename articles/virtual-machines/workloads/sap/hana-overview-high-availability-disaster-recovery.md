---
title: SAP HANA Azure 'da yüksek kullanılabilirlik ve olağanüstü durum kurtarma (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA olağanüstü durum kurtarma için yüksek kullanılabilirlik ve plan oluşturma (büyük örnekler)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d0150aeace3960d075bbf61c1dd0bba4865aaf2b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099717"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure 'da yüksek kullanılabilirlik ve olağanüstü durum kurtarma SAP HANA Büyük Örnekleri 

>[!IMPORTANT]
>Bu belge, SAP HANA yönetimi belgelerinin veya SAP notlarının yerini almaz. Okuyucunun, özellikle yedekleme, geri yükleme, yüksek kullanılabilirlik ve olağanüstü durum kurtarma konularıyla birlikte SAP HANA yönetim ve işlemlerde katı bir şekilde anlaşılması beklenmektedir.

Ortamınızda ve HANA sürümlerinizle ve yayınlarınız ile alınan adımları ve işlemleri uygulamanız önemlidir. Bu belgelerde açıklanan bazı işlemler daha iyi bir genel bilgi için basitleştirilmiştir ve nihai işlem el kitapları için ayrıntılı adımlar olarak kullanılmamalıdır. Yapılandırmanız için el kitaplarını oluşturmak istiyorsanız işlemlerinizi test etmeniz ve uygulamanız ve özel konfigürasyonlarınızla ilgili işlemleri belgeetmeniz gerekir. 


Yüksek kullanılabilirlik ve olağanüstü durum kurtarma (DR), iş açısından kritik SAP HANA Azure (büyük örnekler) sunucusunda çalıştırmanın önemli yönlerinden birisidir. En yüksek kullanılabilirlik ve olağanüstü durum kurtarma stratejilerini doğru şekilde mimarilebilmeleri ve uygulamak için SAP, sistem tümleştiricisi veya Microsoft ile çalışmanız önemlidir. Ayrıca, ortamınıza özgü kurtarma noktası hedefini (RPO) ve kurtarma süresi hedefini göz önünde bulundurmanız önemlidir.

Microsoft, HANA büyük örneklerle bazı SAP HANA yüksek kullanılabilirlik özelliklerini destekler. Bu yetenekler şunlardır:

- **Depolama çoğaltması**: Depolama sisteminin, tüm verileri başka bir Azure bölgesindeki başka bir HANA büyük örnek damgasına çoğaltma özelliği. SAP HANA Bu yöntemden bağımsız olarak çalışır. Bu işlevsellik, HANA büyük örnekleri için sunulan varsayılan olağanüstü durum kurtarma mekanizmasıdır.
- **Hana sistem çoğaltması**: [SAP HANA içindeki tüm verilerin](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) ayrı bir SAP HANA sistemine çoğaltılması. Kurtarma süresi hedefi, düzenli aralıklarla veri çoğaltma aracılığıyla küçültülür. SAP HANA zaman uyumsuz, eşzamanlı bellek içi ve zaman uyumlu modlarını destekler. Zaman uyumlu modu yalnızca aynı veri merkezinde bulunan veya 100 km 'tan az olan SAP HANA sistemler için kullanılır. HANA büyük örnek damgalarının geçerli tasarımıyla, HANA sistem çoğaltması yalnızca bir bölgede yüksek kullanılabilirlik için kullanılabilir. HANA sistem çoğaltması, başka bir Azure bölgesine olağanüstü durum kurtarma yapılandırmalarına yönelik bir üçüncü taraf ters proxy veya yönlendirme bileşeni gerektirir. 
- **Konak otomatik yük devretme**: HANA sistem çoğaltması için bir alternatif olan SAP HANA için yerel bir hata kurtarma çözümü. Ana düğüm kullanılamaz hale gelirse, bir veya daha fazla bekleme SAP HANA düğümünü genişleme modunda yapılandırın ve SAP HANA otomatik olarak bir bekleme düğümüne yük devreder.

Azure 'daki SAP HANA (büyük örnekler) dört geopolitik bölgede (ABD, Avustralya, Avrupa ve Japonya) iki Azure bölgesinde sunulur. HANA büyük örnek damgalarının barındırıldığı bir geopolitik alanı içindeki iki bölge ayrı adanmış ağ devrelerine bağlıdır. Bunlar, olağanüstü durum kurtarma yöntemleri sağlamak üzere depolama anlık görüntülerini çoğaltmak için kullanılır. Çoğaltma varsayılan olarak kurulmaz, ancak olağanüstü durum kurtarma işlevlerini sipariş eden müşteriler için ayarlanır. Depolama çoğaltması, HANA büyük örnekleri için depolama anlık görüntülerinin kullanımına bağımlıdır. Farklı bir geopolitik alanda bulunan bir DR bölgesi olarak bir Azure bölgesi seçmek mümkün değildir. 

Aşağıdaki tabloda, şu anda desteklenen yüksek kullanılabilirlik ve olağanüstü durum kurtarma yöntemleri ve birleşimleri gösterilmektedir:

| HANA büyük örneklerde desteklenen senaryo | Yüksek kullanılabilirlik seçeneği | Olağanüstü durum kurtarma seçeneği | Açıklamalar |
| --- | --- | --- | --- |
| Tek düğüm | Kullanılamıyor. | Adanmış DR kurulumu.<br /> Çok amaçlı DR kurulumu. | |
| Konak otomatik yük devretme: Genişleme (bekleme ile veya olmadan)<br /> 1 + 1 dahil | Etkin rolü almaya yönelik bekleme ile mümkün.<br /> HANA, rol anahtarını denetler. | Adanmış DR kurulumu.<br /> Çok amaçlı DR kurulumu.<br /> Depolama çoğaltması kullanarak DR eşitlemesi. | HANA birim kümeleri tüm düğümlere iliştirilir.<br /> DR sitesinin aynı sayıda düğüm olması gerekir. |
| HANA sistem çoğaltması | Birincil veya ikincil kurulumla mümkün.<br /> İkincil, bir yük devretme çalışmasında birincil role gider.<br /> HANA sistem çoğaltması ve işletim sistemi denetimi yük devretmesi. | Adanmış DR kurulumu.<br /> Çok amaçlı DR kurulumu.<br /> Depolama çoğaltması kullanarak DR eşitlemesi.<br /> HANA sistem çoğaltması kullanılarak DR, üçüncü taraf bileşenleri olmadan henüz mümkün değildir. | Her düğüme ayrı bir disk birimi kümesi eklenir.<br /> Yalnızca üretim sitesindeki ikincil çoğaltmanın disk birimleri DR konumuna çoğaltılır.<br /> DR sitesinde bir birim kümesi gereklidir. | 

Özel bir DR kurulumu, DR sitesindeki HANA büyük örnek biriminin diğer iş yükünü veya üretim dışı sistemleri çalıştırmak için kullanılmadığı yerdir. Birim pasif ve yalnızca bir olağanüstü durum yük devretmesi yürütüldüğünde dağıtılır. Ancak, bu kurulum birçok müşteri için tercih edilen bir seçenek değildir.

Mimariniz için depolama düzeni ve Ethernet ayrıntıları öğrenmek üzere [hLi tarafından desteklenen senaryolar](hana-supported-scenario.md) bölümüne bakın.

> [!NOTE]
> [SAP HANA MCOD dağıtımları](https://launchpad.support.sap.com/#/notes/1681092) (bir birimde birden çok HANA örneği), aşırı yerleştirme senaryoları olarak tabloda listelenen HA ve DR yöntemleriyle çalışır. Özel durum, pacemaker tabanlı bir otomatik yük devretme kümesiyle HANA sistem çoğaltmasının kullanılması. Böyle bir durum, birim başına yalnızca bir HANA örneğini destekler. [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) dağıtımları için, birden fazla kiracı dağıtılırsa yalnızca depolama tabanlı olmayan ha ve Dr yöntemleri çalışır. Bir kiracı dağıtıldıktan sonra, listelenen tüm yöntemler geçerlidir.  

Çok amaçlı bir DR kurulumu, DR sitesindeki HANA büyük örnek biriminin üretim dışı iş yükünü çalıştırdığı yerdir. Olağanüstü durum durumunda, üretim dışı sistemi kapatın, depolama çoğaltılan (ek) birim kümelerini takın ve üretim HANA örneğini başlatın. HANA büyük örnek olağanüstü durum kurtarma işlevini kullanan müşterilerin çoğu bu yapılandırmayı kullanır. 


Aşağıdaki SAP makalelerinde SAP HANA yüksek kullanılabilirlik hakkında daha fazla bilgi edinebilirsiniz: 

- [SAP HANA yüksek kullanılabilirlik teknik Incelemesi](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA Yönetim Kılavuzu](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA sistem çoğaltmasıyla SAP HANA akademik videolar](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP destek notunun #1999880 – SAP HANA sistem çoğaltmasıyla ilgili SSS](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP destek notuna #2165547 – sistem çoğaltma ortamı SAP HANA SAP HANA yedekleme ve geri yükleme](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP destek notunun #1984882: en düşük/sıfır kapalı kalma süresiyle donanım alışverişi için SAP HANA sistem çoğaltmasını kullanma](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>HANA büyük örneklerle olağanüstü durum kurtarma için ağ değerlendirmeleri

HANA büyük örneklerin olağanüstü durum kurtarma işlevselliğinden faydalanmak için, iki Azure bölgesine ağ bağlantısı tasarlamanız gerekir. Ana Azure bölgenizdeki Şirket içinden bir Azure ExpressRoute bağlantı hattı bağlantısı ve şirket içi Şirket içinden olağanüstü durum kurtarma bölgenize başka bir bağlantı gerekir. Bu ölçü, bir Microsoft kurumsal sınır yönlendiricisi (MSEE) konumu dahil olmak üzere Azure bölgesinde bir sorun olduğu bir durumu ele alır.

İkinci bir ölçü olarak, Azure 'daki SAP HANA bağlanan tüm Azure sanal ağlarını (büyük örnekler) bir bölgedeki bir ExpressRoute devresine, diğer bölgedeki HANA büyük örneklerini bağlayan bir ExpressRoute devresine bağlayabilirsiniz. Bu *çapraz bağlantı*Ile, bölge 1 ' de bir Azure sanal ağı üzerinde çalışan hizmetler, bölge 2 ' deki Hana büyük örnek birimlerine ve diğer bir yönteme bağlanabilir. Bu ölçü, Azure ile şirket içi konumunuza bağlanan MSEE konumlarından yalnızca birinin çevrimdışı olduğu bir durumu ele alır.

Aşağıdaki grafikte olağanüstü durum kurtarma durumları için dayanıklı bir yapılandırma gösterilmektedir:

![Olağanüstü durum kurtarma için en iyi yapılandırma](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Çok sayıda HANA büyük örneğe sahip diğer gereksinimler olağanüstü durum kurtarma için depolama çoğaltma

HANA büyük örneklerle bir olağanüstü durum kurtarma kurulumu için önceki gereksinimlere ek olarak, şunları yapmanız gerekir:

- Azure (büyük örnekler) SKU 'Larında, üretim SKU 'larınız ile aynı boyutta olan ve bunları olağanüstü durum kurtarma bölgesinde dağıtan SAP HANA sıralayın. Geçerli müşteri dağıtımlarında, bu örnekler üretim dışı HANA örneklerini çalıştırmak için kullanılır. Bu yapılandırmalara *çok AMAÇLı Dr kurulumları*denir.   
- Olağanüstü durum kurtarma sitesinde kurtarmak istediğiniz Azure (büyük örnekler) SKU 'Larında SAP HANA her biri için DR sitesinde ek depolama alanı sıralayın. Ek depolama alanı satın alma, depolama birimlerini ayırmanıza olanak tanır. Depolama alanı çoğaltmasının hedefi olan birimleri, üretim Azure bölgesindeki olağanüstü durum kurtarma Azure bölgesine ayırabilirsiniz.
- Birincil üzerinde HSR kurulumunu yaptığınız ve DR sitesine depolama tabanlı çoğaltma kurmanızdan sonra, birincil ve ikincil düğümlerin verilerinin DR sitesine çoğaltılması için DR sitesinde ek depolama alanı satın almanız gerekir.

  **Sonraki adımlar**
- [Yedekleme ve geri yükleme](hana-backup-restore.md)bölümüne bakın.














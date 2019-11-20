---
title: Azure Site Recovery ile ExpressRoute kullanma hakkında
description: Olağanüstü durum kurtarma ve geçiş için Azure Site Recovery hizmetiyle Azure ExpressRoute 'un nasıl kullanılacağını açıklar.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: dcc5105fcf2ad7b6a9f0695b3086dc2956a76a50
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954063"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure Site Recovery ile Azure ExpressRoute

Microsoft Azure ExpressRoute, bağlantı sağlayıcı tarafından kolaylaştırılan özel bağlantı üzerinden şirket içi ağlarınızı Microsoft bulutuna genişletmenizi sağlar. ExpressRoute'u kullanarak Microsoft Azure, Office 365 ve Dynamics 365 gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz.

Bu makalede, olağanüstü durum kurtarma ve geçiş için Azure Site Recovery Azure ExpressRoute ile nasıl kullanabileceğiniz açıklanır.

## <a name="expressroute-circuits"></a>ExpressRoute devreleri

Bir ExpressRoute bağlantı hattı, şirket içi altyapınızı ve bağlantı sağlayıcı üzerinden Microsoft bulut hizmetleri arasında mantıksal bağlantıyı temsil eder. Birden çok ExpressRoute bağlantı hattına sipariş edebilirsiniz. Her bağlantı hattı aynı veya farklı bölgelerde olabilir ve farklı bağlantı sağlayıcıları aracılığıyla şirket içinde bağlanabilir. ExpressRoute devreleri hakkında [buradan](../expressroute/expressroute-circuit-peerings.md)daha fazla bilgi edinin.

ExpressRoute bağlantı hattının ilişkili birden çok yönlendirme etki alanı vardır. Daha fazla bilgi edinin ve ExpressRoute yönlendirme etki alanlarını [burada](../expressroute/expressroute-circuit-peerings.md#peeringcompare)karşılaştırın.

## <a name="on-premises-to-azure-replication-with-expressroute"></a>ExpressRoute ile Şirket içinden Azure 'a çoğaltma

Azure Site Recovery, şirket içi [Hyper-V sanal makineleri](hyper-v-azure-architecture.md), [VMware sanal makineleri](vmware-azure-architecture.md)ve [fiziksel sunucular](physical-azure-architecture.md)için olağanüstü durum kurtarma ve Azure 'a geçiş sağlar. Tüm şirket içi Azure senaryolarında çoğaltma verileri bir Azure depolama hesabına gönderilir ve depolanır. Çoğaltma sırasında, herhangi bir sanal makine ücreti ödemezsiniz. Azure 'a yük devretme işlemi çalıştırdığınızda, Site Recovery otomatik olarak Azure IaaS sanal makineleri oluşturur.

Site Recovery, verileri bir Azure depolama hesabına veya hedef Azure bölgesindeki çoğaltma yönetilen diskine ortak bir uç nokta üzerinden çoğaltır. Site Recovery çoğaltma trafiği için ExpressRoute 'u kullanmak üzere [Microsoft eşlemesini](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) veya var olan bir [genel eşlemeyi](../expressroute/expressroute-circuit-peerings.md#publicpeering) (yeni oluşturma işlemleri için kullanım dışı) kullanabilirsiniz. Microsoft eşleme, çoğaltma için önerilen yönlendirme etki alanıdır. Çoğaltmanın özel eşleme üzerinden desteklenmediğini unutmayın.

Yapılandırma sunucusu için [ağ gereksinimlerinin](vmware-azure-configuration-server-requirements.md#network-requirements) de karşılandığından emin olun. Site Recovery çoğaltmanın düzenlenmesi için yapılandırma sunucusu için belirli URL 'lere bağlantı gerekir. ExpressRoute Bu bağlantı için kullanılamaz. 

Şirket içinde ara sunucu kullanıyorsanız ve çoğaltma trafiği için ExpressRoute 'u kullanmak istiyorsanız, yapılandırma sunucusu ve Işlem sunucularında proxy atlama listesini yapılandırmanız gerekir. Aşağıdaki adımları izleyin:

- Sistem Kullanıcı bağlamına erişmek için PsExec aracını [buradan](https://aka.ms/PsExec) indirin.
- Aşağıdaki komut satırı PsExec-s-i "%ProgramFiles%\Internet Explorer\iexplore.exe" komut satırını çalıştırarak Internet Explorer 'ı sistem kullanıcı bağlamında açın
- IE 'de proxy ayarları ekleme
- Atlama listesinde, Azure depolama URL 'SI *. blob.core.windows.net ' yi ekleyin

Bu, iletişim proxy üzerinden ilerleirken yalnızca çoğaltma trafiğinin ExpressRoute üzerinden akmasını güvence altına alınır.

Sanal makineler veya sunucular bir Azure sanal ağına yük devreden sonra, [özel eşleme](../expressroute/expressroute-circuit-peerings.md#privatepeering)kullanarak bunlara erişebilirsiniz. 

Birleşik senaryo şu diyagramda temsil edilir: ExpressRoute ile Şirket içinden Azure 'a ![](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>ExpressRoute ile Azure 'dan Azure 'a çoğaltma

Azure Site Recovery, [Azure sanal makinelerinin](azure-to-azure-architecture.md)olağanüstü durum kurtarmaya izin verebilir. Azure sanal makinelerinizde [Azure yönetilen diskleri](../virtual-machines/windows/managed-disks-overview.md)kullanıp kullanmadığını bağlı olarak, çoğaltma verileri hedef Azure bölgesindeki bir Azure depolama hesabına veya çoğaltma yönetilen diskine gönderilir. Çoğaltma uç noktaları genel olsa da, Azure VM çoğaltma için çoğaltma trafiği varsayılan olarak, kaynak sanal ağın bulunduğu Azure bölgesinden bağımsız olarak Internet 'te geçiş yapmaz. [Özel bir rota](../virtual-network/virtual-networks-udr-overview.md#custom-routes) ile 0.0.0.0/0 adres ön eki için Azure 'un varsayılan sistem yolunu geçersiz KıLABILIR ve VM trafiğini şirket içi ağ sanal gerecine (NVA) yönlendirebilirsiniz, ancak bu yapılandırma Site Recovery çoğaltma için önerilmez. Özel yollar kullanıyorsanız, çoğaltma trafiğinin Azure sınırından ayrılmaması için sanal ağınızda "depolama" için [bir sanal ağ hizmet uç noktası oluşturmanız](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) gerekir.

Azure VM olağanüstü durum kurtarma için varsayılan olarak ExpressRoute çoğaltma için gerekli değildir. Sanal makinelerin hedef Azure bölgesine yük devri yapıldıktan sonra, [özel eşleme](../expressroute/expressroute-circuit-peerings.md#privatepeering)kullanarak bunlara erişebilirsiniz. Veri aktarımı fiyatlarının, Azure bölgeleri arasında veri çoğaltma modundan bağımsız olarak uygulanacağını unutmayın.

Şirket içi veri merkezinizden kaynak bölgedeki Azure VM 'lerine bağlanmak için zaten ExpressRoute kullanıyorsanız, yük devretme hedef bölgesinde ExpressRoute bağlantısını yeniden oluşturmayı planlayabilirsiniz. Aynı ExpressRoute bağlantı hattını kullanarak hedef bölgeye yeni bir sanal ağ bağlantısı aracılığıyla bağlanabilir veya olağanüstü durum kurtarma için ayrı bir ExpressRoute bağlantı hattı ve bağlantısı kullanabilirsiniz. Olası farklı senaryolar [burada](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)açıklanmıştır.

[Burada](../site-recovery/azure-to-azure-support-matrix.md#region-support)açıklandığı gibi, Azure sanal makinelerini aynı coğrafi küme içindeki herhangi bir Azure bölgesine çoğaltabilirsiniz. Seçilen hedef Azure bölgesi kaynakla aynı geopolitik bölgesi içinde değilse, ExpressRoute Premium 'u etkinleştirmeniz gerekebilir. Daha fazla ayrıntı için [ExpressRoute konumlarını](../expressroute/expressroute-locations.md) ve [ExpressRoute fiyatlandırmasını](https://azure.microsoft.com/pricing/details/expressroute/)kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar
- [ExpressRoute devreleri](../expressroute/expressroute-circuit-peerings.md)hakkında daha fazla bilgi edinin.
- [ExpressRoute yönlendirme etki alanları](../expressroute/expressroute-circuit-peerings.md#peeringcompare)hakkında daha fazla bilgi edinin.
- [ExpressRoute konumları](../expressroute/expressroute-locations.md)hakkında daha fazla bilgi edinin.
- [ExpressRoute Ile Azure sanal makinelerinin](azure-vm-disaster-recovery-with-expressroute.md)olağanüstü durum kurtarması hakkında daha fazla bilgi edinin.

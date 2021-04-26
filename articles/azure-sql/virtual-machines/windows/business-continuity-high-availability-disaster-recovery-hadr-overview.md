---
title: Yüksek kullanılabilirlik, olağanüstü durum kurtarma, iş sürekliliği
description: Azure VM 'lerinde, her zaman açık kullanılabilirlik grupları, yük devretme kümesi örneği, veritabanı yansıtma, günlük aktarma ve yedekleme & Azure depolama 'ya geri yükleme gibi yüksek kullanılabilirlik, olağanüstü durum kurtarma (HADR) ve iş sürekliliği SQL Server seçenekleri hakkında bilgi edinin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: 31d22be5ee5480878633b9742837e3f5d6119043
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078953"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Azure sanal makineler 'de SQL Server için iş sürekliliği ve HADR
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

İş sürekliliği, bir olağanüstü durum durumunda işletmenizin devam etmesini, kurtarmanın planlanmasını ve verilerinizin yüksek oranda kullanılabilir olmasını sağlamaya yönelik anlamına gelir. Azure sanal makinelerinde SQL Server, yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR) veritabanı çözümünün maliyetini düşürmenize yardımcı olabilir. 

SQL Server HADR çözümlerinin çoğu, hem Azure hem de Karma çözümler olarak sanal makinelerde (VM) desteklenir. Yalnızca bir Azure çözümünde, tüm HADR sistemleri Azure 'da çalışır. Karma yapılandırmasında, çözümün bir parçası Azure 'da çalışır ve diğer bölüm, kuruluşunuzda şirket içinde çalışır. Azure ortamının esnekliği, SQL Server veritabanı sistemlerinizin bütçesini ve HADR gereksinimlerini karşılamak üzere Azure 'a kısmen veya tamamen geçiş yapmanızı sağlar.

Bu makalede, Azure VM 'lerinde SQL Server için kullanılabilen iş sürekliliği çözümleri karşılaştırılmaktadır ve karşıtlıkları karşılaştırılır. 

## <a name="overview"></a>Genel Bakış

Veritabanı sisteminizin, hizmet düzeyi sözleşmesinin (SLA) gerektirdiği HADR özelliklerine sahip olduğundan emin olmanız gerekir. Azure 'un bulut hizmetleri için hizmet iyileştirme ve sanal makineler için hata kurtarma algılaması gibi yüksek kullanılabilirlik mekanizmaları sağladığı bir olgu, SLA 'yı karşılayabileceğinizi garanti etmez. Bu mekanizmalar sanal makinenin yüksek kullanılabilirlik düzeyini korumaya yardımcı olmakla birlikte, VM içinde çalışan SQL Server kullanılabilirliğini korumaz. 

VM çevrimiçi ve sağlıklı olduğunda SQL Server örneğinin başarısız olması mümkündür. Azure tarafından sunulan yüksek kullanılabilirlik mekanizmaları, yazılım veya donanım hatalarından ve işletim sistemi yükseltmelerinden kurtarma gibi olaylar nedeniyle VM 'lerin kapalı kalma süresine izin verir.

Azure 'daki coğrafi olarak yedekli depolama (GRS), coğrafi çoğaltma adlı bir özellik ile uygulanır. GRS, veritabanlarınız için yeterli bir olağanüstü durum kurtarma çözümü olmayabilir. Coğrafi çoğaltma verileri zaman uyumsuz olarak gönderdiğinden, son güncelleştirmeler bir olağanüstü durumda kaybolabilir. Coğrafi çoğaltma sınırlamaları hakkında daha fazla bilgi, [coğrafi çoğaltma desteği](#geo-replication-support) bölümünde ele alınmıştır.

## <a name="deployment-architectures"></a>Dağıtım mimarileri
Azure, iş sürekliliği için bu SQL Server teknolojilerini destekler:

* [Always on kullanılabilirlik grupları](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Her zaman yük devretme kümesi örneklerinde (Fcsıs)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Günlük aktarma](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [Azure Blob depolama ile yedekleme ve geri yükleme SQL Server](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Veritabanı yansıtma](/sql/database-engine/database-mirroring/database-mirroring-sql-server) -SQL Server 2016 ' de kullanımdan kaldırılmıştır

Hem yüksek kullanılabilirlik hem de olağanüstü durum kurtarma özelliklerine sahip bir SQL Server çözümü uygulamak için teknolojileri birleştirebilirsiniz. Kullandığınız teknolojiye bağlı olarak, karma bir dağıtım için Azure sanal ağı ile bir VPN tüneli gerekebilir. Aşağıdaki bölümlerde bazı örnek dağıtım mimarileri gösterilmektedir.

## <a name="azure-only-high-availability-solutions"></a>Yalnızca Azure: yüksek kullanılabilirliğe sahip çözümler

Her zaman açık kullanılabilirlik gruplarıyla veritabanı düzeyinde SQL Server için yüksek kullanılabilirliğe sahip bir çözümünüz olabilir. Ayrıca, her zaman yük devretme kümesi örneklerinde bir örnek düzeyinde yüksek kullanılabilirliğe sahip bir çözüm oluşturabilirsiniz. Ek koruma için, yük devretme kümesi örneklerinde kullanılabilirlik grupları oluşturarak her iki düzeyde artıklık oluşturabilirsiniz. 

| Teknoloji | Örnek mimarilerin |
| --- | --- |
| **Kullanılabilirlik grupları** |Aynı bölgedeki Azure VM 'lerinde çalışan kullanılabilirlik çoğaltmaları, yüksek kullanılabilirlik sağlar. Windows Yük Devretme Kümelemesi bir Active Directory etki alanı gerektirdiğinden bir etki alanı denetleyicisi VM 'si yapılandırmanız gerekir.<br/><br/> Daha yüksek artıklık ve kullanılabilirlik için, Azure VM 'Leri [kullanılabilirlik grubuna genel bakış](availability-group-overview.md)bölümünde belgelendiği gibi farklı [kullanılabilirlik bölgelerinde](../../../availability-zones/az-overview.md) dağıtılabilir. Bir kullanılabilirlik grubundaki SQL Server VM 'Ler Kullanılabilirlik alanlarına dağıtılmışsa, [Azure SQL VM CLI](./availability-group-az-commandline-configure.md) ve [Azure hızlı başlangıç şablonları](availability-group-quickstart-template-configure.md) makalelerinde belgelendiği gibi, dinleyici için [Azure Standart Load Balancer](../../../load-balancer/load-balancer-overview.md) kullanın.<br/> !["Birincil çoğaltma", "Ikincil çoğaltma" ve "dosya paylaşma tanığı" tarafından oluşturulan "WSFC kümesi" üzerinde "etki alanı denetleyicisi" ni gösteren diyagram.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Daha fazla bilgi için bkz. [Azure 'da kullanılabilirlik grupları yapılandırma (GUI)](./availability-group-quickstart-template-configure.md). |
| **Yük devretme kümesi örnekleri** |Yük devretme kümesi örnekleri SQL Server VM 'lerde desteklenir. FCı özelliği paylaşılan depolama gerektirdiğinden, Azure VM 'lerinde SQL Server beş çözüm çalışır: <br/><br/> -Windows Server 2019 için [Azure paylaşılan diskler](failover-cluster-instance-azure-shared-disks-manually-configure.md) kullanma. Paylaşılan yönetilen diskler aynı anda birden çok sanal makineye yönetilen bir disk eklemeye imkan tanıyan bir Azure ürünüdür. Kümedeki VM 'Ler, kümelenmiş uygulama tarafından seçilen ayırmayı temel alarak, SCSI kalıcı ayırmaları (SCSI PR) aracılığıyla bağlı diskinize okuyabilir veya yazabilir. SCSI PR, şirket içi bir depolama alanı ağı (SAN) üzerinde çalışan uygulamalar tarafından kullanılan sektör standardı bir depolama çözümüdür. Yönetilen bir diskte SCSI PR 'nin etkinleştirilmesi, bu uygulamaları olduğu gibi Azure 'a geçirmenize olanak sağlar. <br/><br/>-Windows Server 2016 ve üzeri için yazılım tabanlı bir sanal SAN sağlamak üzere [depolama alanları doğrudan \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) kullanma.<br/><br/>-Windows Server 2012 ve üzeri için [Premium dosya paylaşımının](failover-cluster-instance-premium-file-share-manually-configure.md) kullanılması. Premium dosya paylaşımları SSD destekli, sürekli düşük gecikme süresine sahip ve FCı ile kullanım için tam olarak desteklenmektedir.<br/><br/>-Kümeleme için bir iş ortağı çözümü tarafından desteklenen depolamayı kullanma. SIOS veri Man kullanan belirli bir örnek için bkz. blog girdisi [Yük Devretme Kümelemesi ve SIOS veri Man](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>-Azure ExpressRoute aracılığıyla uzak bir Iscsı hedefi için paylaşılan blok depolama kullanılıyor. Örneğin, NetApp özel depolama (NPS), Azure VM 'lerine Equinx ile ExpressRoute aracılığıyla bir Iscsı hedefi sunar.<br/><br/>Microsoft iş ortaklarının paylaşılan depolama ve veri çoğaltma çözümleri için, yük devretmeyle ilgili verilere erişme hakkında herhangi bir sorun için satıcıya başvurun.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Yalnızca Azure: olağanüstü durum kurtarma çözümleri
Azure 'daki SQL Server veritabanlarına yönelik bir olağanüstü durum kurtarma çözümüne, kullanılabilirlik grupları, veritabanı yansıtma veya depolama Blobları ile yedekleme ve geri yükleme aracılığıyla erişebilirsiniz.

| Teknoloji | Örnek mimarilerin |
| --- | --- |
| **Kullanılabilirlik grupları** |Olağanüstü durum kurtarma için Azure VM 'lerinde birden çok veri merkezinde çalışan kullanılabilirlik çoğaltmaları. Bu çapraz bölge çözümü, tam bir site kesintisine karşı korunmaya yardımcı olur. <br/> !["Zaman uyumsuz tamamlama" ile bağlı "birincil çoğaltma" ve "Ikincil çoğaltma" ile iki bölge gösteren diyagram.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>Bir bölge içinde tüm çoğaltmalar aynı bulut hizmeti ve aynı sanal ağ içinde olmalıdır. Her bölgenin ayrı bir sanal ağı olacağı için, bu çözümler ağdan ağa bağlantı gerektirir. Daha fazla bilgi için, bkz. [Azure Portal kullanarak ağdan ağa bağlantı yapılandırma](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Ayrıntılı yönergeler için bkz. [farklı Azure bölgelerinde SQL Server Always on kullanılabilirlik grubu yapılandırma](availability-group-manually-configure-multiple-regions.md).|
| **Veritabanı yansıtma** |Olağanüstü durum kurtarma için farklı veri merkezlerinde çalışan asıl ve yansıtma ve sunucular. Bunları sunucu sertifikaları kullanarak dağıtmanız gerekir. SQL Server veritabanı yansıtma, bir Azure VM üzerinde SQL Server 2008 veya SQL Server 2008 R2 için desteklenmez. <br/>!["Yüksek performanslı" başka bir bölgedeki yansıtmaya bağlı bir bölgedeki "Principal" öğesini gösteren diyagram.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Azure Blob depolama ile yedekleme ve geri yükleme** |Üretim veritabanları, olağanüstü durum kurtarma için farklı bir veri merkezinde blob depolamaya doğrudan yedeklenir.<br/>![Başka bir bölgedeki "blob depolama" için bir bölgedeki "veritabanı" öğesini gösteren diyagram.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Daha fazla bilgi için bkz. [Azure VM 'lerde SQL Server Için Yedekleme ve geri yükleme](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **SQL Server Azure Site Recovery Azure 'a çoğaltma ve yük devretme** |Tek bir Azure veri merkezinde üretim SQL Server örneği, olağanüstü durum kurtarma için farklı bir Azure veri merkezinde doğrudan Azure Storage 'a çoğaltılır.<br/>![Başka bir veri merkezinde olağanüstü durum kurtarma için "ASR çoğaltması" kullanarak bir Azure veri merkezinde "veritabanı" gösteren diyagram. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Daha fazla bilgi için bkz. [SQL Server olağanüstü durum kurtarma ve Azure Site Recovery kullanarak SQL Server koruma](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hibrit BT: olağanüstü durum kurtarma çözümleri
Azure Blob depolama ile kullanılabilirlik grupları, veritabanı yansıtma, günlük aktarma ve yedekleme ve geri yükleme kullanarak karma BT ortamındaki SQL Server veritabanlarına yönelik bir olağanüstü durum kurtarma çözümüne sahip olabilirsiniz.

| Teknoloji | Örnek mimarilerin |
| --- | --- |
| **Kullanılabilirlik grupları** |Azure VM 'lerinde çalışan bazı kullanılabilirlik çoğaltmaları ve siteler arası olağanüstü durum kurtarma için şirket içi çalışan diğer çoğaltmalar. Üretim sitesi şirket içinde ya da bir Azure veri merkezinde olabilir.<br/>![Kullanılabilirlik grupları](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Tüm kullanılabilirlik çoğaltmaları aynı yük devretme kümesinde olması gerektiğinden, küme hem ağları (çok alt ağ yük devretme kümesi) kapsamalıdır. Bu yapılandırma, Azure ile şirket içi ağ arasında bir VPN bağlantısı gerektirir.<br/><br/>Veritabanlarının olağanüstü durum kurtarma işleminin başarılı olması için, olağanüstü durum kurtarma sitesinde de bir çoğaltma etki alanı denetleyicisi yüklemelisiniz.|
| **Veritabanı yansıtma** |Sunucu sertifikaları kullanarak siteler arası olağanüstü durum kurtarma için bir Azure VM 'de ve diğer şirket içi çalışan bir iş ortağı. İş ortaklarının aynı Active Directory etki alanında olması gerekmez ve hiçbir VPN bağlantısı gerekli değildir.<br/>![Veritabanı yansıtma](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Başka bir veritabanı yansıtma senaryosu, siteler arası olağanüstü durum kurtarma için aynı Active Directory etki alanında bir Azure VM 'de çalışan diğeri de şirket içinde çalışan bir iş ortağı içerir. [Azure sanal ağı ile şirket içi ağ arasında bir VPN bağlantısı](../../../vpn-gateway/tutorial-site-to-site-portal.md) gereklidir.<br/><br/>Veritabanlarının olağanüstü durum kurtarma işleminin başarılı olması için, olağanüstü durum kurtarma sitesinde de bir çoğaltma etki alanı denetleyicisi yüklemelisiniz. SQL Server veritabanı yansıtma, bir Azure VM üzerinde SQL Server 2008 veya SQL Server 2008 R2 için desteklenmez. |
| **Günlük aktarma** |Azure VM 'de çalışan ve siteler arası olağanüstü durum kurtarma için şirket içi çalışan bir sunucu. Günlük aktarma Windows dosya paylaşımına bağlıdır, bu nedenle Azure sanal ağı ile şirket içi ağ arasında bir VPN bağlantısı gerekir.<br/>![Günlük aktarma](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Veritabanlarının olağanüstü durum kurtarma işleminin başarılı olması için, olağanüstü durum kurtarma sitesinde de bir çoğaltma etki alanı denetleyicisi yüklemelisiniz. |
| **Azure Blob depolama ile yedekleme ve geri yükleme** |Şirket içi üretim veritabanları, olağanüstü durum kurtarma için doğrudan Azure Blob Storage 'a yedeklenir.<br/>![Yedekleme ve geri yükleme](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **SQL Server Azure Site Recovery Azure 'a çoğaltma ve yük devretme** |Şirket içi üretim SQL Server örneği, olağanüstü durum kurtarma için doğrudan Azure Storage 'a çoğaltılır.<br/>![Azure Site Recovery kullanarak çoğaltma](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Daha fazla bilgi için bkz. [SQL Server olağanüstü durum kurtarma ve Azure Site Recovery kullanarak SQL Server koruma](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Azure 'da ücretsiz DR çoğaltması

[Yazılım güvencesi](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)varsa, pasif olağanüstü durum kurtarma örneği için ek lisans maliyetleri yapmadan SQL Server ile karma olağanüstü durum kurtarma (Dr) planları uygulayabilirsiniz.

Örneğin, her üç çoğaltma da Azure 'da barındırıldığı zaman iki adet ücretsiz pasif ikincil sahip olabilir: 

![Azure 'daki her şey olduğunda iki ücretsiz pasta](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

Ya da bir karma yük devretme ortamını, lisanslı bir birincil şirket içi, HA için bir ücretsiz pasif, ister şirket içi bir ücretsiz pasif, hem de Azure 'da DR için ücretsiz bir pasif ile yapılandırabilirsiniz:

![Ortam bir adet birincil şirket içi çoğaltmayla karma olduğunda üç ücretsiz pasa](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

Daha fazla bilgi için [ürün lisanslama koşullarına](https://www.microsoft.com/licensing/product-licensing/products)bakın. 

Bu avantajı etkinleştirmek için [SQL Server sanal makine kaynağına](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)gidin. **Ayarlar** altında **Yapılandır** ' ı seçin ve ardından **SQL Server Lisansı** altında **olağanüstü durum kurtarma** seçeneğini belirleyin. Bu SQL Server VM pasif bir çoğaltma olarak kullanılacağını onaylamak için onay kutusunu işaretleyin ve ardından ayarlarınızı kaydetmek için **Uygula** ' yı seçin. 

![Azure 'da olağanüstü durum kurtarma çoğaltması yapılandırma](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Azure 'da SQL Server HADR için önemli noktalar
Azure VM 'Leri, depolama ve ağ, şirket içi, sanallaştırılmamış BT altyapısından farklı işlemsel özelliklere sahiptir. Azure 'da bir HADR SQL Server çözümünün başarılı bir şekilde uygulanması, bu farklılıkları anladığınızı ve bunları kapsayacak şekilde tasarlamanızı gerektirir.

### <a name="high-availability-nodes-in-an-availability-set"></a>Kullanılabilirlik kümesindeki yüksek kullanılabilirlik düğümleri
Azure 'daki kullanılabilirlik kümeleri, yüksek kullanılabilirliğe sahip düğümleri ayrı hata etki alanlarına ve güncelleştirme etki alanlarına yerleştirmenizi sağlar. Azure platformu, kullanılabilirlik kümesindeki her bir sanal makineye bir güncelleştirme etki alanı ve bir hata etki alanı atar. Bir veri merkezi içindeki bu yapılandırma, planlı veya plansız bir bakım olayı sırasında en az bir sanal makinenin kullanılabilir olmasını sağlar ve yüzde 99,95 Azure SLA 'sını karşılar. 

Yüksek kullanılabilirliğe sahip bir kurulum yapılandırmak için, bakım olayı sırasında uygulama veya veri kaybını önlemek üzere tüm katılan SQL Server sanal makinelerini aynı Kullanılabilirlik kümesine yerleştirin. Aynı Kullanılabilirlik kümesine yalnızca aynı bulut hizmetindeki düğümler katılabilir. Daha fazla bilgi için bkz. [sanal makinelerin kullanılabilirliğini yönetme](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Kullanılabilirlik bölgesindeki yüksek kullanılabilirlik düğümleri
Kullanılabilirlik alanları, bir Azure bölgesi içinde benzersiz fiziksel konumlardır. Her bölge, bağımsız güç, soğutma ve ağ ile donatılmış bir veya daha fazla veri merkezinden oluşur. Bölge içindeki kullanılabilirlik bölgelerinin fiziksel ayrımı, en az bir sanal makinenin kullanılabilir olduğundan ve yüzde 99,99 Azure SLA 'Sı karşıladığından emin olarak veri merkezi hatalarından uygulama ve verilerin korunmasına yardımcı olur. 

Yüksek kullanılabilirliği yapılandırmak için, katılan SQL Server sanal makinelerin bölgedeki kullanılabilirlik bölgelerine yayılmasını sağlar. Kullanılabilirlik alanları arasında ağdan ağa aktarımlar için ek ücretler uygulanır. Daha fazla bilgi için bkz. [kullanılabilirlik alanları](../../../availability-zones/az-overview.md). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Azure ağlarında yük devretme kümesi davranışı
Azure 'daki RFC uyumlu olmayan DHCP hizmeti, belirli yük devretme kümesi yapılandırmalarının oluşturulmasına neden olabilir. Bu hata, küme ağ adına küme düğümlerinden biriyle aynı IP adresi gibi yinelenen bir IP adresi atandığından oluşur. Bu, Windows Yük devretme kümesi özelliğine bağlı olan kullanılabilirlik gruplarını kullandığınızda bir sorundur.

İki düğümlü bir küme oluşturulup çevrimiçi duruma getirildiğinde senaryoyu göz önünde bulundurun:

1. Küme çevrimiçi duruma gelir ve ardından DÜĞÜM1, küme ağ adı için dinamik olarak atanan bir IP adresi ister.
2. DHCP hizmeti, isteğin DÜĞÜM1 kendisinden geldiğini tanıdığından, DHCP hizmeti NODE1's kendi IP adresi dışında bir IP adresi vermez.
3. Windows, hem DÜĞÜM1 hem de yük devretme kümesinin ağ adına yinelenen bir adresin atandığını algılar ve varsayılan küme grubu çevrimiçi duruma gelmeyecektir.
4. Varsayılan küme grubu DÜĞÜM2 ' e gider. DÜĞÜM2, NODE1's IP adresini küme IP adresi olarak değerlendirir ve varsayılan küme grubunu çevrimiçi duruma getirir.
5. DÜĞÜM2, DÜĞÜM1 ile bağlantı kurmaya çalıştığında, NODE1's IP adresini kendisine çözümlediği için DÜĞÜM1 'e yöneltilen paketler hiçbir zaman DÜĞÜM2 bırakmayın. DÜĞÜM2, DÜĞÜM1 ile bağlantı kuramıyor ve ardından çekirdeği kaybeder ve kümeyi kapatır.
6. DÜĞÜM1 DÜĞÜM2 'e paket gönderebilir, ancak DÜĞÜM2 yanıt verebilir. DÜĞÜM1, çekirdeği kaybeder ve kümeyi kapatır.

Küme ağ adını çevrimiçi duruma getirmek için, küme ağ adına kullanılmayan bir statik IP adresi atayarak bu senaryodan kaçınabilirsiniz. Örneğin, 169.254.1.1 gibi bir bağlantı yerel IP adresi kullanabilirsiniz. Bu işlemi basitleştirmek için bkz. [Azure 'Da Windows Yük devretme kümesini kullanılabilirlik grupları Için yapılandırma](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Daha fazla bilgi için bkz. [Azure 'da kullanılabilirlik grupları yapılandırma (GUI)](./availability-group-quickstart-template-configure.md).

### <a name="support-for-availability-group-listeners"></a>Kullanılabilirlik grubu dinleyicileri desteği
Kullanılabilirlik grubu dinleyicileri, Windows Server 2012 ve üstünü çalıştıran Azure VM 'lerinde desteklenir. Bu destek, kullanılabilirlik grubu düğümleri olan Azure VM 'lerde etkin olan yük dengeli uç noktaların kullanımı ile mümkün hale getirilir. Dinleyicilerinin hem Azure 'da hem de şirket içinde çalışan istemci uygulamaları için çalışması için özel yapılandırma adımlarını izlemeniz gerekir.

Dinleyicinizi ayarlamaya yönelik iki ana seçenek vardır: dış (ortak) veya dahili. Dış (genel) dinleyicisi internet 'e yönelik bir yük dengeleyici kullanır ve internet üzerinden erişilebilen bir ortak sanal IP ile ilişkilendirilir. İç dinleyici, iç yük dengeleyiciyi kullanır ve yalnızca aynı sanal ağ içindeki istemcileri destekler. Her iki yük dengeleyici türü için doğrudan sunucu döndürmeyi etkinleştirmeniz gerekir. 

Kullanılabilirlik grubu birden çok Azure alt ağına yayılmışsa (örneğin, Azure bölgelerini kesen bir dağıtım gibi), istemci bağlantı dizesinin içermesi gerekir `MultisubnetFailover=True` . Bu durum, farklı alt ağlardaki çoğaltmalar için paralel bağlantı denemelerinde oluşur. Bir dinleyici ayarlamaya ilişkin yönergeler için bkz. [Azure 'da kullanılabilirlik grupları için ILB dinleyicisi yapılandırma](availability-group-listener-powershell-configure.md).


Yalnızca hizmet örneğine doğrudan bağlanarak her bir kullanılabilirlik çoğaltmasına ayrı olarak bağlanabilirsiniz. Ayrıca, kullanılabilirlik grupları veritabanı yansıtma istemcileriyle geriye dönük olarak uyumlu olduğundan, çoğaltmalar veritabanı yansıtmaya benzer şekilde yapılandırıldığı sürece veritabanı yansıtma ortakları gibi kullanılabilirlik çoğaltmalarına bağlanabilirsiniz:

* Bir birincil çoğaltma ve bir ikincil çoğaltma bulunur.
* İkincil çoğaltma okunabilir olmayan (**okunabilir ikincil** seçenek **Hayır** olarak ayarlanır) olarak yapılandırılmıştır.

ADO.NET veya SQL Server Native Client kullanarak bu veritabanı yansıtma benzeri yapılandırmaya karşılık gelen bir örnek istemci bağlantı dizesi aşağıda verilmiştir:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

İstemci bağlantısı hakkında daha fazla bilgi için bkz.

* [SQL Server Native Client ile bağlantı dizesi anahtar sözcükleri kullanma](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Istemcileri bir veritabanı yansıtma oturumuna bağlama (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Karma BT 'de kullanılabilirlik grubu dinleyicisine bağlanma](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Kullanılabilirlik grubu dinleyicileri, Istemci bağlantısı ve uygulama yük devretmesi (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Kullanılabilirlik gruplarıyla Database-Mirroring bağlantı dizelerini kullanma](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>Karma BT 'de ağ gecikmesi
Şirket içi ağınız ve Azure arasında yüksek ağ gecikme süresi olabileceğini varsayımıyla HADR çözümünüzü dağıtın. Çoğaltmaları Azure 'a dağıttığınızda eşitleme modu için zaman uyumlu tamamlama yerine zaman uyumsuz yürütmeyi kullanın. Hem şirket içinde hem de Azure 'da veritabanı yansıtma sunucuları dağıttığınızda, yüksek performanslı mod yerine yüksek performanslı modu kullanın.

### <a name="geo-replication-support"></a>Coğrafi çoğaltma desteği
Azure disklerinde coğrafi çoğaltma, ayrı disklerde depolanacak aynı veritabanının veri dosyasını ve günlük dosyasını desteklemez. GRS, her bir diskteki değişiklikleri bağımsız olarak ve zaman uyumsuz olarak çoğaltır. Bu mekanizma, birden çok diskin coğrafi çoğaltılan kopyalarında değil, coğrafi olarak çoğaltılan kopyada tek bir disk içindeki yazma sırasını garanti eder. Veri dosyasını ve günlük dosyasını ayrı disklerde depolamak üzere bir veritabanını yapılandırırsanız, bir olağanüstü durum sonrasında kurtarılan diskler, veri dosyasının günlük dosyasından daha güncel bir kopyasını içerebilir ve bu, işlemlerin SQL Server ön ek günlüğünü ve ACID özelliklerini (kararlılık, tutarlılık, yalıtım ve dayanıklılık) ayırır. 

Depolama hesabında Coğrafi çoğaltmayı devre dışı bırakma seçeneğiniz yoksa, bir veritabanının tüm verilerini ve günlük dosyalarını aynı diskte saklayın. Veritabanının boyutu nedeniyle birden fazla disk kullanmanız gerekiyorsa, daha önce listelenen olağanüstü durum kurtarma çözümlerinden birini dağıtarak veri yedekliliği sağlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bir [kullanılabilirlik grubunun](availability-group-overview.md) veya [Yük devretme kümesi örneğinin](failover-cluster-instance-overview.md) işletmeniz için en iyi iş sürekliliği çözümü olup olmadığına karar verin. Daha sonra ortamınızı yüksek kullanılabilirlik ve olağanüstü durum kurtarma için yapılandırmaya yönelik [en iyi uygulamaları](hadr-cluster-best-practices.md) gözden geçirin.

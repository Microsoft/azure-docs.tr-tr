---
title: SQL Server için yüksek kullanılabilirlik ve olağanüstü durum kurtarma | Microsoft Docs
description: Azure sanal makinelerinde çalışan SQL Server için çeşitli HADR stratejileri hakkında bir tartışma.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 5f5fc4ecc0949f2f224c1d6a05742900a751ef45
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756248"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Azure Sanal Makineler’de SQL Server için yüksek kullanılabilirlik ve olağanüstü durum kurtarma

SQL Server ile sanal makineler (VM) Microsoft Azure yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR) veritabanı çözümünün maliyetini düşürmenize yardımcı olabilir. SQL Server HADR çözümlerinin çoğu Azure sanal makinelerinde, hem Azure hem de hibrit çözümler olarak desteklenir. Yalnızca bir Azure çözümünde, tüm HADR sistemleri Azure 'da çalışır. Karma yapılandırmasında, çözümün bir parçası Azure 'da çalışır ve diğer bölüm, kuruluşunuzda şirket içinde çalışır. Azure ortamının esnekliği, SQL Server veritabanı sistemlerinizin bütçesini ve HADR gereksinimlerini karşılamak üzere Azure 'a kısmen veya tamamen geçiş yapmanızı sağlar.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Bir HADR çözümü gereksinimini anlama
Veritabanı sisteminizin, hizmet düzeyi sözleşmesinin (SLA) gerektirdiği HADR özelliklerine sahip olduğundan emin olun. Azure 'un bulut hizmetleri için hizmet iyileştirme ve sanal makineler için hata kurtarma algılaması gibi yüksek kullanılabilirlik mekanizmaları sağladığı aslında, istenen SLA 'yı karşılayabileceğinizi garanti etmez. Bu mekanizmalar sanal makinelerin yüksek kullanılabilirliğini korur ancak VM 'Lerde çalışan SQL Server yüksek kullanılabilirliğe sahip değildir. VM çevrimiçi ve sağlıklı olduğunda SQL Server örneğinin başarısız olması mümkündür. Üstelik, Azure tarafından sunulan yüksek kullanılabilirlik mekanizmaları, yazılım veya donanım hatalarından ve işletim sistemi yükseltmelerinden kurtarma gibi olaylar nedeniyle VM 'lerin kapalı kalma süresine izin verir.

Ayrıca, coğrafi çoğaltma adlı bir özellik ile uygulanan Azure 'daki coğrafi olarak yedekli depolama (GRS), veritabanlarınız için yeterli bir olağanüstü durum kurtarma çözümü olmayabilir. Coğrafi çoğaltma verileri zaman uyumsuz olarak gönderdiğinden, olağanüstü durum durumunda son güncelleştirmeler kaybedilebilir. Coğrafi çoğaltma sınırlamalarıyla ilgili daha fazla bilgi, [coğrafi çoğaltmanın farklı diskler üzerinde veri ve günlük dosyaları için desteklenmez](#geo-replication-support) bölümünde ele alınmıştır.

## <a name="hadr-deployment-architectures"></a>HADR dağıtım mimarileri
Azure 'da desteklenen SQL Server HADR teknolojileri şunları içerir:

* [Always on kullanılabilirlik grupları](https://technet.microsoft.com/library/hh510230.aspx)
* [Her zaman yük devretme kümesi örneklerinde](https://technet.microsoft.com/library/ms189134.aspx)
* [Günlük aktarma](https://technet.microsoft.com/library/ms187103.aspx)
* [Azure Blob depolama hizmeti ile yedekleme ve geri yükleme SQL Server](https://msdn.microsoft.com/library/jj919148.aspx)
* [Veritabanı yansıtma](https://technet.microsoft.com/library/ms189852.aspx) -SQL Server 2016 ' de kullanımdan kaldırılmıştır

Hem yüksek kullanılabilirlik hem de olağanüstü durum kurtarma özelliklerine sahip bir SQL Server çözümü uygulamak için teknolojileri birlikte birleştirmek mümkündür. Kullandığınız teknolojiye bağlı olarak, karma bir dağıtım için Azure sanal ağı ile bir VPN tüneli gerekebilir. Aşağıdaki bölümlerde örnek dağıtım mimarilerinden bazıları gösterilmektedir.

## <a name="azure-only-high-availability-solutions"></a>Yalnızca Azure: yüksek kullanılabilirlik çözümleri

Her zaman açık kullanılabilirlik grupları adlı kullanılabilirlik grupları ile veritabanı düzeyinde SQL Server için yüksek kullanılabilirlik çözümüne sahip olabilirsiniz. Ayrıca, her zaman yük devretme kümesi örnekleri-yük devretme kümesi örnekleri ile bir örnek düzeyinde yüksek kullanılabilirlik çözümü de oluşturabilirsiniz. Ek artıklık için, yük devretme kümesi örneklerinde kullanılabilirlik grupları oluşturarak her iki düzeyde artıklık oluşturabilirsiniz. 

| Teknoloji | Örnek mimarilerin |
| --- | --- |
| **Kullanılabilirlik grupları** |Aynı bölgedeki Azure VM 'lerinde çalışan kullanılabilirlik çoğaltmaları, yüksek kullanılabilirlik sağlar.  Windows Yük Devretme Kümelemesi bir Active Directory etki alanı gerektirdiğinden bir etki alanı denetleyicisi VM 'si yapılandırmanız gerekir.<br/><br/> Daha yüksek artıklık ve kullanılabilirlik için Azure VM 'Leri, [kullanılabilirlik grubuna genel bakış](virtual-machines-windows-portal-sql-availability-group-overview.md)bölümünde belgelendiği gibi farklı [kullanılabilirlik bölgelerinde](../../../availability-zones/az-overview.md) dağıtılabilir. Bir kullanılabilirlik grubundaki SQL Server VM 'Ler kullanılabilirlik alanlarında dağıtılmışsa, bu makalelerde belgelendiği gibi dinleyici için [Standart yük dengeleyici](../../../load-balancer/load-balancer-standard-overview.md) kullanın- [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) & [Azure hızlı başlangıç şablonları](virtual-machines-windows-sql-availability-group-quickstart-template.md).<br/> Grupları ![Availability ](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Daha fazla bilgi için bkz. [Azure 'Da kullanılabilirlik grupları yapılandırma (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Yük devretme kümesi örnekleri** |Paylaşılan depolama gerektiren yük devretme kümesi örnekleri (FCı) 4 farklı şekilde oluşturulabilir.<br/><br/>1. Azure VM 'lerde çalışan ve [Windows Server 2016 Depolama Alanları Doğrudan \(S2D \)](virtual-machines-windows-portal-sql-create-failover-cluster.md) yazılım tabanlı BIR sanal San sağlamak için bağlı depolama içeren iki düğümlü bir yük devretme kümesi.<br/><br/> 2. [Premium dosya paylaşımının](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)kullanıldığı Azure VM 'lerinde çalışan iki düğümlü bir yük devretme kümesi. Premium dosya paylaşımları, yük devretme kümesi örneği ile kullanım için tam olarak desteklenen, SSD destekli, tutarlı ve düşük gecikmeli dosya paylaşımlardır.<br/><br/>3. Azure VM 'lerde çalışan, bir üçüncü taraf kümeleme çözümü tarafından desteklenen depolama ile iki düğümlü bir yük devretme kümesi. SIOS veri Man kullanan belirli bir örnek için bkz. [Yük Devretme Kümelemesi ve 3. taraf yazılım SIOS veri kullanımı kullanılarak bir dosya paylaşımının yüksek kullanılabilirliği](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. ExpressRoute aracılığıyla uzak Iscsı hedefi paylaşılan blok depolama ile Azure VM 'lerinde çalışan iki düğümlü bir yük devretme kümesi. Örneğin, NetApp özel depolama (NPS), Azure VM 'lerine Equinx ile ExpressRoute aracılığıyla bir Iscsı hedefi sunar.<br/><br/>Üçüncü taraf paylaşılan depolama ve veri çoğaltma çözümleri için, yük devretmede veriye erişme ile ilgili herhangi bir sorun için satıcıyla iletişim kurmanız gerekir.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Yalnızca Azure: olağanüstü durum kurtarma çözümleri
Azure 'da kullanılabilirlik grupları, veritabanı yansıtma veya yedekleme ve geri yükleme ile depolama Blobları aracılığıyla SQL Server veritabanları için bir olağanüstü durum kurtarma çözümüne sahip olabilirsiniz.

| Teknoloji | Örnek mimarilerin |
| --- | --- |
| **Kullanılabilirlik grupları** |Olağanüstü durum kurtarma için Azure VM 'lerinde birden çok veri merkezinde çalışan kullanılabilirlik çoğaltmaları. Bu çapraz bölge çözümü, tam site kesintiine karşı koruma sağlar. <br/> Grupları ![Availability ](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>Bir bölge içinde tüm çoğaltmaların aynı bulut hizmeti ve aynı VNet içinde olması gerekir. Her bölge ayrı bir sanal ağa sahip olacağı için bu çözümler VNET 'in VNet bağlantısı olmasını gerektirir. Daha fazla bilgi için, [Azure Portal kullanarak VNET 'Ten VNET 'e bağlantı yapılandırma](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)konusuna bakın. Ayrıntılı yönergeler için bkz. [farklı bölgelerdeki Azure sanal makinelerinde SQL Server kullanılabilirlik grubu yapılandırma](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Veritabanı yansıtma** |Olağanüstü durum kurtarma için farklı veri merkezlerinde çalışan asıl ve yansıtma ve sunucular. Sunucu sertifikaları kullanarak dağıtmanız gerekir. SQL Server veritabanı yansıtma, bir Azure VM üzerinde SQL Server 2008 veya SQL Server 2008 R2 için desteklenmez. <br/>![Veritabanı yansıtma](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Azure Blob depolama hizmeti ile yedekleme ve geri yükleme** |Üretim veritabanları, olağanüstü durum kurtarma için farklı bir veri merkezinde blob depolamaya doğrudan yedeklenir.<br/>![Yedekleme ve Geri Yükleme](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](virtual-machines-windows-sql-backup-recovery.md). |
| **Azure Site Recovery ile Azure 'a çoğaltma ve yük devretme SQL Server** |Tek bir Azure veri merkezinin üretim SQL Server olağanüstü durum kurtarma için farklı Azure veri merkezi 'nin Azure depolama 'ya doğrudan çoğaltılır.<br/>Azure Site Recovery kullanarak ![Replicate ](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Daha fazla bilgi için bkz. [SQL Server olağanüstü durum kurtarma ve Azure Site Recovery kullanarak SQL Server koruma](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hibrit BT: olağanüstü durum kurtarma çözümleri
Kullanılabilirlik grupları, veritabanı yansıtma, günlük aktarma ve Azure blog depolama ile yedekleme ve geri yükleme kullanarak karma BT ortamındaki SQL Server veritabanlarına yönelik bir olağanüstü durum kurtarma çözümüne sahip olabilirsiniz.

| Teknoloji | Örnek mimarilerin |
| --- | --- |
| **Kullanılabilirlik grupları** |Azure VM 'lerinde çalışan bazı kullanılabilirlik çoğaltmaları ve siteler arası olağanüstü durum kurtarma için şirket içi çalışan diğer çoğaltmalar. Üretim sitesi şirket içinde ya da bir Azure veri merkezinde olabilir.<br/>![Kullanılabilirlik grupları](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Tüm kullanılabilirlik çoğaltmaları aynı yük devretme kümesinde olması gerektiğinden, küme hem ağları (çok alt ağ yük devretme kümesi) kapsamalıdır. Bu yapılandırma, Azure ile şirket içi ağ arasında bir VPN bağlantısı gerektirir.<br/><br/>Veritabanlarının olağanüstü durum kurtarma işleminin başarılı olması için, olağanüstü durum kurtarma sitesinde de bir çoğaltma etki alanı denetleyicisi yüklemelisiniz.<br/><br/>Var olan her zaman açık kullanılabilirlik grubuna bir Azure çoğaltması eklemek için SSMS içindeki çoğaltma ekleme Sihirbazı 'Nı kullanmak mümkündür. Daha fazla bilgi için bkz. Öğretici: Always on kullanılabilirlik grubunuzu Azure 'a genişletme. |
| **Veritabanı yansıtma** |Sunucu sertifikaları kullanarak siteler arası olağanüstü durum kurtarma için bir Azure VM 'de ve diğer şirket içi çalışan bir iş ortağı. İş ortaklarının aynı Active Directory etki alanında olması gerekmez ve hiçbir VPN bağlantısı gerekli değildir.<br/>![Database yansıtma ](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Başka bir veritabanı yansıtma senaryosu, siteler arası olağanüstü durum kurtarma için aynı Active Directory etki alanında bir Azure VM 'de çalışan diğeri de şirket içinde çalışan bir iş ortağı içerir. [Azure sanal ağı ile şirket içi ağ arasında bir VPN bağlantısı](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) gereklidir.<br/><br/>Veritabanlarının olağanüstü durum kurtarma işleminin başarılı olması için, olağanüstü durum kurtarma sitesinde de bir çoğaltma etki alanı denetleyicisi yüklemelisiniz. SQL Server veritabanı yansıtma, bir Azure VM üzerinde SQL Server 2008 veya SQL Server 2008 R2 için desteklenmez. |
| **Günlük aktarma** |Azure VM 'de çalışan ve siteler arası olağanüstü durum kurtarma için şirket içi çalışan bir sunucu. Günlük aktarma Windows dosya paylaşımına bağlıdır, bu nedenle Azure sanal ağı ile şirket içi ağ arasında bir VPN bağlantısı gerekir.<br/>![Günlük aktarma](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Veritabanlarının olağanüstü durum kurtarma işleminin başarılı olması için, olağanüstü durum kurtarma sitesinde de bir çoğaltma etki alanı denetleyicisi yüklemelisiniz. |
| **Azure Blob depolama hizmeti ile yedekleme ve geri yükleme** |Şirket içi üretim veritabanları, olağanüstü durum kurtarma için doğrudan Azure Blob Storage 'a yedeklenir.<br/>![Yedekleme ve Geri Yükleme](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](virtual-machines-windows-sql-backup-recovery.md). |
| **Azure Site Recovery ile Azure 'a çoğaltma ve yük devretme SQL Server** |Şirket içi üretim SQL Server olağanüstü durum kurtarma için doğrudan Azure Storage 'a çoğaltılır.<br/>Azure Site Recovery kullanarak ![Replicate ](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Daha fazla bilgi için bkz. [SQL Server olağanüstü durum kurtarma ve Azure Site Recovery kullanarak SQL Server koruma](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Azure 'da SQL Server HADR için önemli noktalar
Azure VM 'Leri, depolama ve ağ, şirket içi, sanallaştırılmamış BT altyapısından farklı işlemsel özelliklere sahiptir. Azure 'da bir HADR SQL Server çözümünün başarılı bir şekilde uygulanması, bu farklılıkları anladığınızı ve bunları kapsayacak şekilde tasarlamanızı gerektirir.

### <a name="high-availability-nodes-in-an-availability-set"></a>Bir kullanılabilirlik kümesindeki yüksek kullanılabilirlik düğümleri
Azure 'daki kullanılabilirlik kümeleri, yüksek kullanılabilirlik düğümlerini ayrı hata etki alanlarına (FDs) ve güncelleştirme etki alanlarına (UDs) yerleştirmenizi sağlar. Kullanılabilirlik kümesindeki her bir sanal makineye, temel alınan Azure platformu tarafından bir güncelleştirme etki alanı ve bir hata etki alanı atanır. Bir veri merkezi içindeki bu yapılandırma, planlı veya plansız bir bakım olayı sırasında en az bir sanal makinenin kullanılabilir olmasını ve% 99,95 Azure SLA 'sını karşılamasını sağlar. Yüksek kullanılabilirlik kurulumunu yapılandırmak için, bir bakım olayı sırasında uygulama veya veri kaybını önlemek için tüm katılan SQL sanal makinelerini aynı Kullanılabilirlik kümesine yerleştirin. Aynı Kullanılabilirlik kümesine yalnızca aynı bulut hizmetindeki düğümler katılabilir. Daha fazla bilgi için bkz. [Sanal Makinelerin Kullanılabilirliğini Yönetme](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Kullanılabilirlik bölgesindeki yüksek kullanılabilirlik düğümleri
Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Bir bölgedeki Kullanılabilirlik Alanları fiziksel ayrımı, en az bir sanal makinenin kullanılabilir olduğundan ve% 99,99 Azure SLA ile buluşmasını sağlayarak, veri merkezi hatalarından uygulama ve verileri korur. Yüksek kullanılabilirliği yapılandırmak için, katılan SQL sanal makinelerini bölgede kullanılabilir Kullanılabilirlik Alanları yayılmış yere yerleştirin. Ek kullanılabilirlik alanı VM 'leri-VM veri aktarımı ücretleri olacaktır. Daha fazla bilgi için bkz. [kullanılabilirlik alanları](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Azure ağlarında yük devretme kümesi davranışı
Azure 'daki RFC uyumlu olmayan DHCP hizmeti belirli yük devretme kümesi yapılandırmalarının oluşturulmasına neden olabilir. bu nedenle, küme ağ adı küme düğümlerinden biriyle aynı IP adresi gibi yinelenen bir IP adresine atanmaktadır. Bu, Windows Yük devretme kümesi özelliğine bağlı olan kullanılabilirlik gruplarını uyguladığınızda bir sorundur.

İki düğümlü bir küme oluşturulup çevrimiçi duruma getirildiğinde senaryoyu göz önünde bulundurun:

1. Küme çevrimiçi duruma gelir, sonra DÜĞÜM1 küme ağı adı için dinamik olarak atanan bir IP adresi ister.
2. DHCP hizmeti, isteğin DÜĞÜM1 kendisinden geldiğini tanıdığından, DHCP hizmeti tarafından NODE1's kendi IP adresi dışında bir IP adresi verilmez.
3. Windows, hem DÜĞÜM1 hem de yük devretme kümesi ağ adına yinelenen bir adresin atandığını algılar ve varsayılan küme grubu çevrimiçi olarak gerçekleşmeyecektir.
4. Varsayılan küme grubu, NODE1's IP adresini küme IP adresi olarak ele getiren ve varsayılan küme grubunu çevrimiçi hale getiren DÜĞÜM2 adresine gider.
5. DÜĞÜM2, DÜĞÜM1 ile bağlantı kurmaya çalıştığında, NODE1's IP adresini kendisine çözümlediği için DÜĞÜM1 'e yöneltilen paketler hiçbir zaman DÜĞÜM2 bırakmayın. DÜĞÜM2, DÜĞÜM1 ile bağlantı kuramıyor, ardından çekirdeği kaybeder ve kümeyi kapatır.
6. Bu sırada, DÜĞÜM1 DÜĞÜM2 'e paket gönderebilir, ancak DÜĞÜM2 yanıt vermez. DÜĞÜM1, çekirdeği kaybeder ve kümeyi kapatır.

Bu senaryoya, küme ağ adını çevrimiçi duruma getirmek için 169.254.1.1 gibi bir bağlantı yerel IP adresi gibi kullanılmayan bir statik IP adresi (örneğin, küme ağ adı) atanarak kaçınılabilir. Bu işlemi basitleştirmek için bkz. [Azure 'Da Windows Yük devretme kümesini kullanılabilirlik grupları Için yapılandırma](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Daha fazla bilgi için bkz. [Azure 'da kullanılabilirlik grupları yapılandırma (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Kullanılabilirlik grubu dinleyicisi desteği
Kullanılabilirlik grubu dinleyicileri, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 ve Windows Server 2016 çalıştıran Azure VM 'lerinde desteklenir. Bu destek, kullanılabilirlik grubu düğümleri olan Azure VM 'lerde etkin olan yük dengeli uç noktaların kullanımı ile mümkün hale getirilir. Dinleyicilerinin hem Azure 'da çalışan hem de şirket içinde çalışan istemci uygulamaları için çalışması için özel yapılandırma adımlarını izlemeniz gerekir.

Dinleyicinizi ayarlamaya yönelik iki ana seçenek vardır: dış (ortak) veya dahili. Dış (genel) dinleyicisi internet 'e yönelik yük dengeleyiciyi kullanır ve internet üzerinden erişilebilen bir ortak sanal IP (VIP) ile ilişkilendirilir. İç dinleyici, iç yük dengeleyiciyi kullanır ve yalnızca aynı sanal ağ içindeki istemcileri destekler. Her iki yük dengeleyici türü için doğrudan sunucu döndürmeyi etkinleştirmeniz gerekir. 

Kullanılabilirlik grubu birden çok Azure alt ağına yayılmışsa (örneğin, Azure bölgelerini kesen bir dağıtım gibi), istemci bağlantı dizesinin "**MultiSubnetFailover = true**" içermesi gerekir. Bu durum, farklı alt ağlardaki çoğaltmalar için paralel bağlantı denemelerinde oluşur. Dinleyici ayarlama hakkında yönergeler için bkz.

* [Azure 'da kullanılabilirlik grupları için BIR ıLB dinleyicisi yapılandırın](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Azure 'da kullanılabilirlik grupları için dış dinleyici yapılandırın](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Yalnızca hizmet örneğine doğrudan bağlanarak her bir kullanılabilirlik çoğaltmasına ayrı olarak bağlanabilirsiniz. Ayrıca, kullanılabilirlik grupları veritabanı yansıtma istemcileriyle geriye dönük olarak uyumlu olduğundan, çoğaltmalar veritabanı yansıtmaya benzer şekilde yapılandırıldığı sürece veritabanı yansıtma ortakları gibi kullanılabilirlik çoğaltmalarına bağlanabilirsiniz:

* Bir birincil çoğaltma ve bir ikincil çoğaltma
* İkincil çoğaltma okunabilir olmayan şekilde yapılandırıldı (**okunabilir ikincil** seçenek **Hayır**olarak ayarlanır)

ADO.NET veya SQL Server Native Client kullanarak bu veritabanı yansıtma benzeri yapılandırmaya karşılık gelen bir örnek istemci bağlantı dizesi aşağıda verilmiştir:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

İstemci bağlantısı hakkında daha fazla bilgi için bkz.

* [SQL Server Native Client ile bağlantı dizesi anahtar sözcükleri kullanma](https://msdn.microsoft.com/library/ms130822.aspx)
* [Istemcileri bir veritabanı yansıtma oturumuna bağlama (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Karma BT 'de kullanılabilirlik grubu dinleyicisine bağlanma](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Kullanılabilirlik grubu dinleyicileri, Istemci bağlantısı ve uygulama yük devretmesi (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Kullanılabilirlik gruplarıyla veritabanı yansıtma bağlantı dizelerini kullanma](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Karma BT 'de ağ gecikmesi
HADR çözümünüzü, şirket içi ağınız ve Azure arasında yüksek ağ gecikme süresine sahip olabilecek bir süre olabileceğini varsayımıyla dağıtmanız gerekir. Çoğaltmaları Azure 'a dağıttığınızda eşitleme modu için zaman uyumlu tamamlama yerine zaman uyumsuz yürütmeyi kullanmanız gerekir. Hem şirket içinde hem de Azure 'da veritabanı yansıtma sunucularını dağıttığınızda, yüksek performanslı mod yerine yüksek performanslı modu kullanın.

### <a name="geo-replication-support"></a>Coğrafi çoğaltma desteği
Azure disklerinde coğrafi çoğaltma, ayrı disklerde depolanacak aynı veritabanının veri dosyasını ve günlük dosyasını desteklemez. GRS, her bir diskteki değişiklikleri bağımsız olarak ve zaman uyumsuz olarak çoğaltır. Bu mekanizma, birden çok diskin coğrafi çoğaltılan kopyalarında değil, coğrafi olarak çoğaltılan kopyada tek bir disk içindeki yazma sırasını garanti eder. Bir veritabanını, veri dosyasını ve günlük dosyasını ayrı disklere depolayacak şekilde yapılandırırsanız, bir olağanüstü durum sonrasında kurtarılan diskler, veri dosyasının günlük dosyasından daha güncel bir kopyasını içerebilir ve bu, SQL Server ön belleği ve döküm 'nin ACID özelliklerini keser. alanda. Depolama hesabında Coğrafi çoğaltmayı devre dışı bırakma seçeneğiniz yoksa, belirli bir veritabanı için tüm verileri ve günlük dosyalarını aynı diskte tutmanız gerekir. Veritabanı boyutu nedeniyle birden fazla disk kullanmanız gerekiyorsa, verilerin artıklığı sağlamak için yukarıda listelenen olağanüstü durum kurtarma çözümlerinden birini dağıtmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
SQL Server ile bir Azure sanal makinesi oluşturmanız gerekiyorsa bkz. [Azure 'da SQL Server sanal makinesi sağlama](virtual-machines-windows-portal-sql-server-provision.md).

Azure VM 'de çalışan SQL Server en iyi performansı elde etmek için, [Azure sanal makineler 'de SQL Server yönelik performans En Iyi uygulamalarında](virtual-machines-windows-sql-performance.md)yer alan kılavuza bakın.

Azure VM 'lerinde SQL Server çalıştırmaya ilişkin diğer konular için bkz. [Azure sanal makinelerinde SQL Server](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Diğer kaynaklar
* [Azure 'da yeni bir Active Directory ormanı yüklemesi](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Azure VM 'de kullanılabilirlik grupları için yük devretme kümesi oluşturma](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)


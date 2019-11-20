---
title: Azure Site Recovery SQL Server için olağanüstü durum kurtarma ayarlama
description: Bu makalede SQL Server ve Azure Site Recovery kullanarak SQL Server için olağanüstü durum kurtarma ayarlama açıklanmaktadır.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084751"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>SQL Server için olağanüstü durum kurtarmayı ayarlama

Bu makalede, bir uygulamanın SQL Server arka ucunun korunmasına nasıl yardımcı olduğu açıklanır. Bunu, SQL Server iş sürekliliği ve olağanüstü durum kurtarma (BCDR) teknolojilerinin ve [Azure Site Recovery](site-recovery-overview.md)bir birleşimini kullanarak yapabilirsiniz.

Başlamadan önce SQL Server olağanüstü durum kurtarma özelliklerini anladığınızdan emin olun. Bu yetenekler şunlardır:

* Yük Devretme Kümelemesi
* Always on kullanılabilirlik grupları
* Veritabanı yansıtma
* Günlük aktarma
* Etkin coğrafi çoğaltma
* Otomatik yük devretme grupları

## <a name="combining-bcdr-technologies-with-site-recovery"></a>BCDR teknolojilerini Site Recovery birleştirme

SQL Server örnekleri kurtarmak için BCDR teknolojisini tercih ettiğiniz, aşağıdaki tabloda açıklandığı gibi kurtarma zamanı hedefi (RTO) ve kurtarma noktası hedefi (RPO) gereksinimlerine göre olması gerekir. Tüm uygulamanızı yönetmek için Site Recovery seçtiğiniz teknolojinin yük devretme işlemiyle birleştirin.

Dağıtım türü | BCDR teknolojisi | SQL Server için RTO bekleniyor | SQL Server için RPO bekleniyor |
--- | --- | --- | ---
Bir Azure hizmet olarak altyapı (IaaS) sanal makinesi (VM) veya şirket içinde SQL Server.| [AlwaysOn kullanılabilirlik grubu](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | İkincil çoğaltmayı birincil olarak yapmak için geçen süre. | İkincil çoğaltmaya çoğaltma zaman uyumsuz olduğundan, bazı veri kaybı vardır.
Azure IaaS sanal makinesinde veya şirket içinde SQL Server.| [Yük Devretme Kümelemesi (Always on FCı)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | Düğümler arasında yük devretmek için geçen süre. | Her zaman FCı üzerinde paylaşılan depolama kullandığından, yük devretme sırasında depolama örneğinin aynı görünümü kullanılabilir.
Azure IaaS sanal makinesinde veya şirket içinde SQL Server.| [Veritabanı yansıtma (yüksek performanslı mod)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | Bir yarı bekleyen sunucu olarak yansıtma sunucusunu kullanan hizmeti zorlamak için geçen süre. | Çoğaltma zaman uyumsuzdur. Yansıtma veritabanı, asıl veritabanının arkasında biraz gecikme alabilir. Gecikme genellikle küçüktür. Ancak asıl veya yansıtma sunucusunun sistemi ağır bir yükün altındaysa büyük hale gelebilir.<br/><br/>Günlük aktarma, veritabanı yansıtma için bir ek olabilir. Zaman uyumsuz veritabanı yansıtma için iyi bir alternatiftir.
Azure 'da hizmet olarak platform (PaaS) olarak SQL.<br/><br/>Bu dağıtım türü, elastik havuzlar ve Azure SQL veritabanı sunucularını içerir. | Etkin coğrafi çoğaltma | Yük devretme işlemi tetiklendiğinde 30 saniye.<br/><br/>İkincil veritabanlarından biri için yük devretme etkinleştirildiğinde, diğer tüm ikincil öğeler yeni birincil ile otomatik olarak bağlanır. | Beş saniyelik RPO 'SU.<br/><br/>Etkin coğrafi çoğaltma SQL Server her zaman açık teknolojisini kullanır. Anlık görüntü yalıtımı kullanarak birincil veritabanında yürütülen işlemleri ikincil veritabanına zaman uyumsuz olarak çoğaltır.<br/><br/>İkincil verilere hiçbir şekilde kısmi işlem olmadığı garanti edilir.
Azure 'da etkin coğrafi çoğaltma ile SQL as PaaS yapılandırıldı.<br/><br/>Bu dağıtım türü bir SQL veritabanı yönetilen örneği, elastik havuzlar ve SQL veritabanı sunucuları içerir. | Otomatik yük devretme grupları | RTO bir saat. | Beş saniyelik RPO 'SU.<br/><br/>Otomatik yük devretme grupları, etkin coğrafi çoğaltmanın en üstünde grup semantiğini sağlar. Ancak aynı zaman uyumsuz çoğaltma mekanizması kullanılır.
Azure IaaS sanal makinesinde veya şirket içinde SQL Server.| Azure Site Recovery ile çoğaltma | RTO, genellikle 15 dakikadan azdır. Daha fazla bilgi edinmek için [Site Recovery tarafından sunulan RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)makalesini okuyun. | Uygulama tutarlılığı için bir saat ve kilitlenme tutarlılığı için beş dakika. Daha düşük bir RPO arıyorsanız, diğer BCDR teknolojilerini kullanın.

> [!NOTE]
> Site Recovery ile SQL iş yüklerini korumaya yardımcı olduğunuzda dikkat etmeniz gereken birkaç önemli nokta:
> * Site Recovery uygulamanın belirsiz olması. Site Recovery, desteklenen bir işletim sisteminde dağıtılan SQL Server herhangi bir sürümünü korumaya yardımcı olabilir. Daha fazla bilgi edinmek için bkz. çoğaltılan makinelerin [kurtarılması için destek matrisi](vmware-physical-azure-support-matrix.md#replicated-machines) .
> * Azure, Hyper-V, VMware veya fiziksel altyapıda herhangi bir dağıtım için Site Recovery kullanmayı tercih edebilirsiniz. Lütfen [bir SQL Server kümesini Site Recovery ile korumanıza yardımcı olmak için](#how-to-help-protect-a-sql-server-cluster) Bu makalenin sonundaki yönergeleri izleyin.
> * Makinede gözlenen veri değişim hızının [Site Recovery limitlerin](vmware-physical-azure-support-matrix.md#churn-limits)içinde olduğundan emin olun. Değişiklik hızı, saniye başına yazılan bayt cinsinden ölçülür. Windows çalıştıran makineler için, Görev Yöneticisi 'nde **performans** sekmesini seçerek bu değişiklik oranını görüntüleyebilirsiniz. Her disk için yazma hızını gözlemleyin.
> * Site Recovery, Depolama Alanları Doğrudan yük devretme kümesi örneklerinin çoğaltılmasını destekler. Daha fazla bilgi için bkz. [depolama alanları doğrudan çoğaltmayı etkinleştirme](azure-to-azure-how-to-enable-replication-s2d-vms.md).

## <a name="disaster-recovery-of-an-application"></a>Uygulamanın olağanüstü durum kurtarması

Site Recovery, kurtarma planlarının yardımıyla tüm uygulamanızın yük devretmesini ve yük devretmesini düzenler.

Kurtarma planınızın gereksinimlerinize göre tamamen özelleştirildiğinden emin olmak için bazı Önkoşullar vardır. Herhangi bir SQL Server dağıtımı genellikle bir Active Directory dağıtımına ihtiyaç duyuyor. Ayrıca uygulama katmanınız için bağlantı gerekir.

### <a name="step-1-set-up-active-directory"></a>1\. Adım: Active Directory ayarlama

SQL Server düzgün çalışması için ikincil kurtarma sitesinde Active Directory ayarlayın.

* **Küçük kurumsal**: şirket içi site için az sayıda uygulama ve tek bir etki alanı denetleyiciniz vardır. Tüm sitenin yükünü devretmek istiyorsanız Site Recovery çoğaltma kullanın. Bu hizmet, etki alanı denetleyicisini ikincil veri merkezine veya Azure 'a çoğaltır.
* **Orta ila büyük kurumsal**: ek etki alanı denetleyicileri ayarlamanız gerekebilir.
  - Çok sayıda uygulamanız varsa, bir Active Directory ormanına sahipseniz ve uygulamanın veya iş yükünün yükünü devretmek istiyorsanız, ikincil veri merkezinde veya Azure 'da başka bir etki alanı denetleyicisi ayarlayın.
  -  Uzak bir siteye kurtarmak için Always on kullanılabilirlik grupları kullanıyorsanız, ikincil sitede veya Azure 'da başka bir etki alanı denetleyicisi ayarlayın. Bu etki alanı denetleyicisi kurtarılmış SQL Server örneği için kullanılır.

Bu makaledeki yönergeler, bir etki alanı denetleyicisinin ikincil konumda kullanılabilir olduğunu varsayar. Daha fazla bilgi edinmek için [Site Recovery Active Directory korumaya yardımcı olma](site-recovery-active-directory.md)yordamlarına bakın.

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>2\. Adım: diğer katmanlardan bağlantı sağlayın

Hedef Azure bölgesinde veritabanı katmanı çalıştıktan sonra uygulama ve Web katmanlarıyla bağlantınız olduğundan emin olun. Sınama yük devretmesi ile bağlantıyı doğrulamak için gerekli adımları önceden yapın.

Bağlantı hususları için uygulamaları nasıl tasarlayabileceğinizi anlamak için aşağıdaki örneklere bakın:

* [Bulut olağanüstü durum kurtarma için uygulama tasarlama](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Elastik havuz olağanüstü durum kurtarma stratejileri](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>3\. Adım: her zaman açık, etkin coğrafi çoğaltma ve otomatik yük devretme gruplarıyla birlikte çalışma

BCDR teknolojileri her zaman açık, etkin coğrafi çoğaltma ve otomatik yük devretme gruplarında, hedef Azure bölgesinde çalışan SQL Server ikincil çoğaltmaları vardır. Uygulamanızın yük devretmesinin ilk adımı, bu çoğaltmayı birincil olarak belirtmektir. Bu adım, ikincil üzerinde zaten bir etki alanı denetleyiciniz olduğunu varsayar. Otomatik yük devretmeyi tercih ederseniz adım gerekli olmayabilir. Web ve uygulama katmanlarınızın yükünü yalnızca veritabanı yük devretme işlemi tamamlandıktan sonra devreder.

> [!NOTE]
> SQL makinelerini Site Recovery korumayla ilgili yardım ediyorsanız, bu makinelerin bir kurtarma grubunu oluşturmanız ve yük devretmesini kurtarma planına eklemeniz yeterlidir.

Uygulama ve Web katmanı sanal makineleriyle [bir kurtarma planı oluşturun](site-recovery-create-recovery-plans.md) . Aşağıdaki adımlar veritabanı katmanının yük devretmesini nasıl ekleneceğini göstermektedir:

1. Hem [Kaynak Yöneticisi sanal makinede](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) hem de [klasık sanal makinede](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)SQL kullanılabilirlik grubu 'nun yükünü devretmek için betikleri içeri aktarın. Betikleri Azure Otomasyonu hesabınıza aktarın.

    ["Azure 'a dağıt" logosunun ![görüntüsü](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. ASR-SQL-FailoverAG betiğini kurtarma planının ilk grubunun ön eylemi olarak ekleyin.

1. Otomasyon değişkeni oluşturmak için betikte bulunan yönergeleri izleyin. Bu değişken, kullanılabilirlik gruplarının adını sağlar.

### <a name="step-4-conduct-a-test-failover"></a>4\. Adım: yük devretme testi yürütme

SQL her zaman açık gibi bazı BCDR teknolojileri, yük devretme testini yerel olarak desteklemez. *Yalnızca bu tür teknolojiler kullanılırken*aşağıdaki yaklaşımı öneririz.

1. Azure 'da kullanılabilirlik grubu çoğaltmasını barındıran VM 'de [Azure Backup](../backup/backup-azure-arm-vms.md) ayarlayın.

1. Kurtarma planının yük devretmesini test tetiklemeden önce, önceki adımda alınan yedekten VM 'yi kurtarın.

    ![Azure Backup bir yapılandırmayı geri yükleme penceresini gösteren ekran görüntüsü](./media/site-recovery-sql/restore-from-backup.png)

1. Yedekten geri yüklenen VM 'de [bir çekirdeğe zorlayın](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) .

1. Dinleyicinin IP adresini, test yük devretmesi ağında kullanılabilir bir adres olacak şekilde güncelleştirin.

    ![Kural penceresi ve IP adresi özellikleri iletişim kutusunun ekran görüntüsü](./media/site-recovery-sql/update-listener-ip.png)

1. Dinleyiciyi çevrimiçi duruma getirin.

    ![Sunucu adlarını ve durumlarını gösteren Content_AG etiketli pencerenin ekran görüntüsü](./media/site-recovery-sql/bring-listener-online.png)

1. Yük devretme ağındaki yük dengeleyicinin bir IP adresine, her bir kullanılabilirlik grubu dinleyicisine karşılık gelen ön uç IP adresi havuzundan ve arka uç havuzundaki SQL Server VM sahip olduğundan emin olun.

     !["SQL-AlwaysOn-LB-ön uç IP havuzu" başlıklı pencerenin ekran görüntüsü](./media/site-recovery-sql/create-load-balancer1.png)

    !["SQL-AlwaysOn-LB-arka uç IP havuzu" başlıklı pencerenin ekran görüntüsü](./media/site-recovery-sql/create-load-balancer2.png)

1. Daha sonraki kurtarma gruplarında, uygulama katmanınızın yük devretmesini ve ardından bu kurtarma planına ait web katmanınızı ekleyin.

1. Uygulamanızın uçtan uca yük devretmesini test etmek için kurtarma planının yük devretmesini test edin.

## <a name="steps-to-do-a-failover"></a>Yük devretme işlemi oluşturma adımları

Adım 3 ' te betiği ekledikten ve adım 4 ' te doğruladıktan sonra, adım 3 ' te oluşturulan kurtarma planının yük devretmesini gerçekleştirebilirsiniz.

Uygulama ve Web katmanları için yük devretme adımları, hem test yük devretmesi hem de yük devretme kurtarma planlarında aynı olmalıdır.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>SQL Server kümesini korumaya nasıl yardımcı olabilirsiniz

SQL Server Standard Edition veya SQL Server 2008 R2 çalıştıran bir küme için, SQL Server korumaya yardımcı olmak üzere Site Recovery çoğaltmasını kullanmanızı öneririz.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 'dan Azure 'a ve Şirket içinden Azure 'a

Site Recovery, bir Azure bölgesine çoğaltma yaparken Konuk küme desteği sağlamaz. SQL Server Standard sürüm, düşük maliyetli bir olağanüstü durum kurtarma çözümü de sağlamaz. Bu senaryoda, SQL Server kümesini birincil konumdaki tek başına SQL Server örneğine korumanızı ve ikincil konuma kurtarmayı öneririz.

1. Birincil Azure bölgesinde veya şirket içi sitede ek bir tek başına SQL Server örneği yapılandırın.

1. Koruma sağlamak istediğiniz veritabanları için bir yansıtma olarak kullanılacak örneği yapılandırın. İzlemeyi yüksek güvenlik modunda yapılandırın.

1. [Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md)veya [VMware VM 'leri ve fiziksel sunucular](site-recovery-vmware-to-azure-classic.md)için birincil sitede Site Recovery yapılandırın.

1. Yeni SQL Server örneğini ikincil siteye çoğaltmak için Site Recovery çoğaltma kullanın. Yüksek güvenlik yansıtma kopyası olduğundan, birincil kümeyle eşitlenir, ancak Site Recovery çoğaltma kullanılarak çoğaltılır.

   ![Birincil site, Site Recovery ve Azure arasındaki ilişkiyi ve akışı gösteren standart bir kümenin görüntüsü](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Yeniden çalışma konuları

SQL Server Standard kümeler için, planlanmamış bir yük devretmeden sonra yeniden çalışma için SQL Server Yedekleme ve geri yükleme gerekir. Bu işlem yansıtma örneğinden orijinal kümeye, yansıtmanın yeniden kurulmasıyla yapılır.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>SQL Server Site Recovery kullanıldığında nasıl lisanslanır?

SQL Server için Site Recovery çoğaltma, yazılım güvencesi olağanüstü durum kurtarma avantajı kapsamında ele alınmıştır. Bu kapsam tüm Site Recovery senaryolar için geçerlidir: Şirket içinden Azure 'a olağanüstü durum kurtarma ve bölgeler arası Azure IaaS olağanüstü durum kurtarma. Daha fazla bilgi için bkz. [Azure Site Recovery fiyatlandırması](https://azure.microsoft.com/pricing/details/site-recovery/) .

### <a name="will-site-recovery-support-my-sql-server-version"></a>SQL Server sürümm Site Recovery destekleyecektir.

Site Recovery uygulamanın belirsiz olması. Site Recovery, desteklenen bir işletim sisteminde dağıtılan SQL Server herhangi bir sürümünü korumaya yardımcı olabilir. Daha fazla bilgi için bkz. çoğaltılan makinelerin [kurtarılması için destek matrisi](vmware-physical-azure-support-matrix.md#replicated-machines) .

## <a name="next-steps"></a>Sonraki adımlar

* [Site Recovery mimari](site-recovery-components.md)hakkında daha fazla bilgi edinin.
* Azure 'da SQL Server için, ikincil bir Azure bölgesinde kurtarmaya yönelik [yüksek kullanılabilirlik çözümleri](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) hakkında daha fazla bilgi edinin.
* SQL veritabanı için, ikincil bir Azure bölgesinde kurtarmaya yönelik [iş sürekliliği](../sql-database/sql-database-business-continuity.md) ve [yüksek kullanılabilirlik](../sql-database/sql-database-high-availability.md) seçenekleri hakkında daha fazla bilgi edinin.
* Şirket içi SQL Server makineler için, Azure sanal makinelerinde kurtarmaya yönelik [yüksek kullanılabilirlik seçenekleri](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) hakkında daha fazla bilgi edinin.

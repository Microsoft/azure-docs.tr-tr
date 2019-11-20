---
title: Azure Site Recovery hizmetiyle ilgili genel sorular
description: Bu makalede Azure Site Recovery hakkındaki popüler genel sorular ele alınmaktadır.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: fb88d28ea47495dcbdb0844901a03ee7efa4e4eb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078532"
---
# <a name="general-questions-about-azure-site-recovery"></a>Azure Site Recovery ilgili genel sorular

Bu makalede Azure Site Recovery hakkında sık sorulan sorular özetlenmektedir. Belirli senaryolar için bu makaleleri gözden geçirin

- [Azure 'da Azure VM olağanüstü durum kurtarma ile ilgili sorular](azure-to-azure-common-questions.md)
- [Azure 'a VMware VM olağanüstü durum kurtarma ile ilgili sorular](vmware-azure-common-questions.md)
- [Azure 'da Hyper-V VM olağanüstü durum kurtarma ile ilgili sorular](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Genel

### <a name="what-does-site-recovery-do"></a>Site Recovery ne işe yarar?
Site Recovery, bölgeler, şirket içi sanal makineler ve fiziksel sunucular ile Azure arasında Azure VM 'Leri ve şirket içi makineleri bir araya getirerek ve otomatikleştirerek iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur. ikincil veri merkezi. [Daha fazla bilgi edinin](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Docker diskine sahip bir sanal makineyi koruyabilir miyim?

Hayır, bu desteklenmeyen bir senaryodur.

## <a name="service-providers"></a>Hizmet sağlayıcıları

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Ben bir hizmet sağlayıcısıyım. Adanmış ve paylaşılan altyapı modelleriyle Site Recovery çalışıyor mu?
Evet, Site Recovery hem adanmış hem de paylaşılan altyapı modellerini destekler.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Bir hizmet sağlayıcısı için, kiracının Site Recovery hizmetiyle paylaştığı kimlik mi?
Hayır. Kiracı kimliği anonim olarak kalır. Kiracılarınızın Site Recovery portalına erişmesi gerekmez. Yalnızca hizmet sağlayıcı yöneticisi portal ile etkileşim sağlar.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Kiracı uygulama verileri Azure 'a gitmez mi?
Hizmet sağlayıcının sahip olduğu siteler arasında çoğaltma yapılırken uygulama verileri asla Azure'a gitmez. Veriler aktarım sırasında şifrelenir ve doğrudan hizmet sağlayıcısı siteleri arasında çoğaltılır.

Azure'da çoğaltma yaparken, uygulama verileri Azure depolama alanına gönderilir ancak Site Recovery hizmetine gönderilmez. Veriler aktarım sırasında şifrelenir ve Azure 'da şifreli olarak kalır.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Kiracılarıma herhangi bir Azure hizmeti için fatura gönderilir mi?
Hayır. Azure'ın faturalama bağlantısı doğrudan hizmet sağlayıcı ile kurulur. Hizmet sağlayıcılar, kiracıları için belirli faturaları oluşturmaktan sorumludur.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Azure'a çoğaltma işlemi gerçekleştirirken sanal makinelerin Azure'da sürekli olarak çalışması gerekir mi?
Hayır, veriler aboneliğinizdeki Azure depolama alanına çoğaltılır. Yük devretme testi (DR detayı) veya gerçek bir yük devretme gerçekleştirdiğinizde, Site Recovery otomatik olarak aboneliğinizde sanal makineler oluşturur.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Azure'a çoğaltma yaptığımda kiracı düzeyinde yalıtım sağlanır mı?
Evet.

### <a name="what-platforms-do-you-currently-support"></a>Şu anda hangi platformları destekliyorsanız?
Azure Pack, bulut platformu sistemi ve System Center tabanlı (2012 ve üzeri) dağıtımları destekliyoruz. Azure paketi ve Site Recovery tümleştirme hakkında [daha fazla bilgi edinin](https://technet.microsoft.com/library/dn850370.aspx) .

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Tek bir Azure Paketi'ni ve tek bir VMM sunucusu dağıtımını destekliyor musunuz?
Evet, Hyper-V sanal makinelerini Azure 'a veya hizmet sağlayıcı siteleri arasında çoğaltabilirsiniz.  Hizmet sağlayıcı siteleri arasında çoğaltma yaparsanız Azure runbook tümleştirmesi 'nin kullanılamaz olduğunu unutmayın.

## <a name="pricing"></a>Fiyatlandırma

### <a name="where-can-i-find-pricing-information"></a>Fiyatlandırma bilgilerini nerede bulabilirim?
[Site Recovery fiyatlandırma](https://azure.microsoft.com/pricing/details/site-recovery/) ayrıntılarını gözden geçirin.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Site Recovery kullanımı sırasında yaklaşık ücretleri nasıl hesaplayabilirim?

Site Recovery kullanırken maliyetleri tahmin etmek için [Fiyatlandırma hesaplayıcısı](https://aka.ms/asr_pricing_calculator) ' nı kullanabilirsiniz.

Maliyetlerde ayrıntılı tahmin için, [VMware](https://aka.ms/siterecovery_deployment_planner) veya [Hyper-V](https://aka.ms/asr-deployment-planner)için dağıtım planlayıcısı aracını çalıştırın ve [maliyet tahmini raporunu](https://aka.ms/asr_DP_costreport)kullanın.


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Yönetilen diskler artık VMware VM 'lerini ve fiziksel sunucuları çoğaltmak için kullanılır. Yönetilen diskler ile önbellek depolama hesabı için ek ücret ödemem gerekiyor mu?

Hayır, önbellek için ek ücret alınmaz. Standart depolama hesabına çoğaltma yaptığınızda, bu önbellek depolaması aynı hedef depolama hesabının bir parçasıdır.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Bir aydan uzun süredir Azure Site Recovery kullanıcısıyım. Yine de korunan her örnek için ilk 31 gün ücretsiz mi olacak?

Evet. Korunan örneklerden ilk 31 gün boyunca Azure Site Recovery ücreti alınmaz. Örneğin, son 6 ay boyunca 10 örnek koruyorsanız ve Azure Site Recovery bir 11 örneği bağladığınızda, ilk 31 gün boyunca 11. örnek için ücret alınmaz. İlk 10 örnek 31 günden uzun süredir korunduğundan bu yana Azure Site Recovery ücret almaya devam eder.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>İlk 31 gün boyunca başka herhangi bir Azure hizmeti için ücretlendirilir miyim?

Evet, Site Recovery korunan bir örneğin ilk 31 günü boyunca ücretsiz olsa da Azure depolama, depolama işlemleri ve veri aktarımı için ücret ödemeniz istenebilir. Korunan bir sanal makine için de Azure işlem ücretleri alınabilir.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Olağanüstü durum kurtarma detaylarının/test yük devretmesi gerçekleştirmeye ilişkin bir maliyet var mı?

DR detayına yönelik ayrı bir maliyet yoktur. Yük devretme testi sonrasında VM oluşturulduktan sonra işlem ücretleri olacaktır.



## <a name="security"></a>Güvenlik

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Çoğaltılan veriler Site Recovery hizmetine gönderilir mi?
Hayır, Site Recovery çoğaltılan verileri engellemez ve sanal makinelerinizde veya fiziksel sunucularınızda çalışan hakkında hiçbir bilgi içermez.
Çoğaltma verileri şirket içi Hyper-V ana bilgisayarları, VMware hiper yöneticileri veya fiziksel sunucular ile Azure depolama alanı ya da ikincil siteniz arasında değiştirilir. Site Recovery bu verilere müdahale edemez. Yalnızca çoğaltma ve yük devretme işlemlerini düzenlemek için gereken meta veriler Site Recovery hizmetine gönderilir.  

Site Recovery ISO 27001:2013, 27018, HIPAA, DPA sertifikalı ve SOC2 ve Fedrajab değerlendirmesi sürecinde.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Uyumluluk nedenleriyle, şirket içi meta verilerimizin aynı coğrafi bölge içinde kalması gerekir. Bize Site Recovery yardımcı olabilir misiniz?
Evet. Bir bölgede Site Recovery kasa oluşturduğunuzda, çoğaltma ve yük devretme işlemlerini etkinleştirmek ve düzenlemek için ihtiyaç duyduğumuz tüm meta verilerin bu bölgenin coğrafi sınırında kalmasını sağlamaktır.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery çoğaltma işlemini şifreleyebilir mi?
Sanal makineler ve fiziksel sunucular için, şirket içi siteler arasında çoğaltma geçişi desteklenir. Azure 'a çoğaltılan sanal makineler ve fiziksel sunucular için hem çapraz geçiş hem de [bekleyen şifreleme (Azure 'da)](https://docs.microsoft.com/azure/storage/storage-service-encryption) desteklenir.




## <a name="disaster-recovery"></a>Olağanüstü durum kurtarma

### <a name="what-can-site-recovery-protect"></a>Site Recovery neleri koruyabilirim?
* **Azure VM 'leri**: Site Recovery, desteklenen BIR Azure VM üzerinde çalışan herhangi bir iş yükünü çoğaltabilir
* **Hyper-v sanal makineleri**: Site Recovery, Hyper-v VM üzerinde çalışan herhangi bir iş yükünü koruyabilir.
* **Fiziksel sunucular**: Site Recovery, Windows veya Linux çalıştıran fiziksel sunucuları koruyabilir.
* **VMware sanal makineleri**: Site Recovery, VMware VM 'de çalışan herhangi bir iş yükünü koruyabilir.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Site Recovery ile hangi iş yüklerini koruyabilirim?
Desteklenen bir VM 'de veya fiziksel sunucuda çalışan çoğu iş yükünü korumak için Site Recovery kullanabilirsiniz. Site Recovery uygulamalar akıllı bir duruma kurtarılabilmesi için uygulamayla uyumlu çoğaltma desteği sağlar. SharePoint, Exchange, Dynamics, SQL Server ve Active Directory gibi Microsoft uygulamalarıyla tümleştirilir ve Oracle, SAP, IBM ve Red hat gibi önde gelen satıcılarla yakından çalışır. İş yükü koruması hakkında [daha fazla bilgi edinin](site-recovery-workload.md).

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Site Recovery ile şubelerim için olağanüstü durum kurtarma işlemini yönetebilir miyim?
Evet. Şube ofislerinizde çoğaltma ve yük devretme işlemlerini düzenlemek için Site Recovery kullandığınızda, tüm şube ofisi iş yüklerinizi merkezi bir konumda birleştirilmiş bir düzenleme ve görüntüleme işlemi alacaksınız. Şubelerinizi ziyaret etmenize gerek kalmadan kolaylıkla tüm şubelerinizin olağanüstü durum kurtarma işlemlerini yönetebilir ve yük devretmeler çalıştırabilirsiniz.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Azure VM 'Leri için olağanüstü durum kurtarma destekleniyor mu?

Evet, Azure bölgeleri arasında Azure VM 'Leri için olağanüstü durum desteği Site Recovery. Azure VM olağanüstü durum kurtarma ile ilgili [sık sorulan soruları gözden geçirin](azure-to-azure-common-questions.md) .

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>VMware VM 'Leri için olağanüstü durum kurtarma destekleniyor mu?

Evet, Site Recovery şirket içi VMware VM 'lerinin olağanüstü durum kurtarmasını destekler. VMware VM 'lerinin olağanüstü durum kurtarması için [sık sorulan soruları gözden geçirin](vmware-azure-common-questions.md) .

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Hyper-V VM 'Leri için olağanüstü durum kurtarma destekleniyor mu?
Evet, Site Recovery şirket içi Hyper-V VM 'lerinin olağanüstü durum kurtarmasını destekler. Hyper-V VM 'lerinin olağanüstü durum kurtarma için [genel soruları gözden geçirin](hyper-v-azure-common-questions.md) .

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Fiziksel sunucular için olağanüstü durum kurtarma destekleniyor mu?
Evet, Site Recovery Windows ve Linux çalıştıran şirket içi fiziksel sunucuların Azure 'a veya ikincil bir siteye olağanüstü durum kurtarmayı destekler. [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)'a olağanüstü durum kurtarma ve[ikincil bir siteye](vmware-physical-secondary-support-matrix.md#replicated-vm-support)yönelik gereksinimler hakkında bilgi edinin.
Fiziksel sunucuların yük devretmeden sonra Azure 'da VM olarak çalışacağını unutmayın. Azure 'dan şirket içi fiziksel sunucuya yeniden çalışma şu anda desteklenmiyor. Yalnızca bir VMware sanal makinesine geri dönebilirsiniz.





## <a name="replication"></a>Çoğaltma

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Azure 'a siteden siteye VPN üzerinden çoğaltma yapabilir miyim?
Azure Site Recovery, verileri bir Azure depolama hesabına veya yönetilen disklere ortak bir uç nokta üzerinden çoğaltır. Çoğaltma, siteden siteye VPN üzerinde değil. 

### <a name="why-cant-i-replicate-over-vpn"></a>Neden VPN üzerinden çoğaltılamam?

Azure 'a çoğaltma yaptığınızda, çoğaltma trafiği bir Azure depolama alanının genel uç noktalarına ulaşır. Bu nedenle, yalnızca ExpressRoute (Microsoft eşlemesi veya var olan bir genel eşleme) ile genel İnternet üzerinden çoğaltma yapabilirsiniz ve VPN çalışmaz.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Çoğaltma için Rivervisteelheads kullanabilir miyim?

İş ortağımızda, Azure Site Recovery ile çalışma hakkında ayrıntılı rehberlik sağlar. [Çözüm kılavuzunu](https://community.riverbed.com/s/article/DOC-4627)gözden geçirin.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Sanal makineleri Azure 'a çoğaltmak için ExpressRoute kullanabilir miyim?
Evet, [ExpressRoute](concepts-expressroute-with-site-recovery.md) , şirket içi sanal makineleri Azure 'a çoğaltmak için kullanılabilir.

- Azure Site Recovery, verileri bir Azure depolama alanına ortak bir uç nokta üzerinden çoğaltır. Site Recovery çoğaltma için ExpressRoute kullanmak üzere [Microsoft eşlemesini](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ayarlamanız veya mevcut bir [ortak eşlemeyi](../expressroute/expressroute-circuit-peerings.md#publicpeering) (yeni devreler için kullanım dışı) kullanmanız gerekir.
- Microsoft eşleme, çoğaltma için önerilen yönlendirme etki alanıdır.
- Çoğaltma, özel eşleme üzerinden desteklenmez.
- VMware makinelerini veya fiziksel makineleri koruyorsanız, yapılandırma sunucusu için [ağ gereksinimlerinin](vmware-azure-configuration-server-requirements.md#network-requirements) de karşılandığından emin olun. Site Recovery çoğaltmanın düzenlenmesi için yapılandırma sunucusu için belirli URL 'lere bağlantı gerekir. ExpressRoute Bu bağlantı için kullanılamaz.
- Sanal makineler bir Azure sanal ağına yük devretdikten sonra, Azure sanal ağı ile [özel eşleme](../expressroute/expressroute-circuit-peerings.md#privatepeering) kurulumunu kullanarak bunlara erişebilirsiniz.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Azure 'a çoğaltma yapıyorsanız, ne tür bir depolama hesabı veya yönetilen disk gereksinimim vardır?

LRS veya GRS depolamaya ihtiyacınız vardır. Bölgesel bir kesintinin meydana gelmesi veya birincil bölgenin kurtarılamaması durumunda verilerin korunması için GRS'yi tavsiye ederiz. Hesabın, Kurtarma Hizmetleri kasasıyla aynı bölgede olması gerekir. Azure portal Site Recovery dağıtırken, VMware VM, Hyper-V VM ve fiziksel sunucu çoğaltma için Premium Depolama desteklenir. Yönetilen diskler yalnızca LRS 'yi destekler.

### <a name="how-often-can-i-replicate-data"></a>Verileri ne sıklıkta çoğaltabilirim?
* **Hyper-V:** Hyper-V VM 'Leri, her 30 saniyede bir (Premium Depolama hariç), beş dakika veya 15 dakika olabilir.
* **Azure VM 'leri, VMware VM 'leri, fiziksel sunucular:** Çoğaltma sıklığı burada ilgili değildir. Çoğaltma sürekli.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Mevcut kurtarma sitesinden başka bir üçüncül sitesine çoğaltma genişletebilir miyim?
Genişletilmiş veya zincir çoğaltma desteklenmez. Bu özelliği [geri bildirim forumuna](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)iste.

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Azure'a ilk kez çoğaltma yaparken çevrimdışı bir çoğaltma gerçekleştirebilir miyim?
Bu özellik desteklenmez. Bu özelliği [geri bildirim forumuna](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)iste.

### <a name="can-i-exclude-specific-disks-from-replication"></a>Belirli diskleri çoğaltmanın dışında tutabilir miyim?
Bu, Azure portal kullanarak VMware VM 'Leri ve Hyper-V VM 'lerini Azure 'a çoğaltırken desteklenir.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Dinamik disklere sahip sanal makineleri çoğaltabilir miyim?
Hyper-V sanal makinelerini çoğaltırken ve VMware VM 'lerini ve fiziksel makineleri Azure 'a çoğaltarak dinamik diskler desteklenir. İşletim sistemi diski, temel bir disk olmalıdır.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Çoğaltma trafiği için ayrılan bant genişliğini kısıtlayabilir miyim?
Evet. Aşağıdaki makalelerde azaltma bant genişliği hakkında daha fazla bilgi edinebilirsiniz:

* [VMware VM 'lerini ve fiziksel sunucuları çoğaltmak için kapasite planlaması](site-recovery-plan-capacity-vmware.md)
* [Hyper-V VM 'lerini Azure 'a çoğaltmak için kapasite planlaması](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Yük devretme
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Azure 'a yük devretdiğimde yük devretmeden sonra Azure VM 'lerine nasıl erişebilirim?

Azure VM'lerine güvenli bir İnternet bağlantısı, siteden siteye VPN veya Azure ExpressRoute üzerinden erişebilirsiniz. Bağlanmak için bir dizi şey hazırlamanız gerekir. [Daha fazla bilgi edinin](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Azure 'a yük devrediğimde, Azure 'un verilerimi dayanıklı olduğundan emin olun mi?
Azure esneklik için tasarlanmıştır. Site Recovery, Azure SLA 'sına uygun olarak ikincil bir Azure veri merkezine yük devretme için zaten tasarlanmıştır. Bu durumda, meta veri ve kasalarınızın kasanız için seçtiğiniz coğrafi bölge içinde kalmasını sağlarız.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>İki veri merkezi arasında çoğaltma yapıyorsanız, birincil veri Merkezim beklenmedik bir kesinti yaşıyorsa ne olur?
İkincil siteden planlanmamış bir yük devretme tetikleyebilirsiniz. Bu yük devretme işlemini gerçekleştirmek için Site Recovery'nin birincil siteye bağlanması gerekmez.

### <a name="is-failover-automatic"></a>Yük devretme işlemi otomatik midir?
Yük devretme işlemi otomatik değildir. Portalda tek tıklamayla yük devretme işlemini başlatır veya [Site Recovery PowerShell](/powershell/module/az.recoveryservices) kullanarak yük devretmeyi tetikleyebilirsiniz. Geri dönme, Site Recovery portalında basit bir işlemdir.

Otomatik hale getirmek için, şirket içi Orchestrator veya Operations Manager kullanarak bir sanal makine başarısızlığını algılayabilir ve sonra SDK 'Yı kullanarak yük devretmeyi tetikleyebilirsiniz.

* Kurtarma planları hakkında [daha fazla bilgi edinin](site-recovery-create-recovery-plans.md) .
* Yük devretme hakkında [daha fazla bilgi edinin](site-recovery-failover.md) .
* VMware VM 'lerini ve fiziksel sunucuları geri dönme hakkında [daha fazla bilgi edinin](site-recovery-failback-azure-to-vmware.md)

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Şirket içi Ana Bilgisayarım yanıt vermiyorsa veya kilitlenirse farklı bir konağa yeniden oturum açabilir miyim?
Evet, Azure 'dan farklı bir konağa yeniden çalışma yapmak için alternatif konum kurtarma kullanabilirsiniz.

* [VMware sanal makineleri için](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V sanal makineleri için](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Otomasyon

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Site Recovery senaryolarını bir SDK ile otomatikleştirebilirim miyim?
Evet. Rest API'si, PowerShell veya Azure SDK kullanarak Site Recovery iş akışlarını otomatikleştirebilirsiniz. PowerShell kullanarak Site Recovery dağıtmaya yönelik Şu anda desteklenen senaryolar:

* [VMMs bulutlarındaki Hyper-V VM 'lerini Azure PowerShell Kaynak Yöneticisi çoğaltma](hyper-v-vmm-powershell-resource-manager.md)
* [VMM olmadan Hyper-V VM 'lerini Azure PowerShell Kaynak Yöneticisi çoğaltma](hyper-v-azure-powershell-resource-manager.md)
* [VMware 'yi PowerShell ile Azure 'a çoğaltma Kaynak Yöneticisi](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Bileşen/sağlayıcı yükseltmesi

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Site Recovery yükseltmelerin sürüm notlarını/güncelleştirme paketlerini nerede bulabilirim?

Yeni güncelleştirmeler hakkında [bilgi edinin](site-recovery-whats-new.md) ve [toplama bilgilerini alın](service-updates-how-to.md).

## <a name="next-steps"></a>Sonraki adımlar
* [Site Recovery'ye genel bakış](site-recovery-overview.md) başlığını okuyun.


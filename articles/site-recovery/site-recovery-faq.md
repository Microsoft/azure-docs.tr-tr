---
title: Azure Site Recovery hizmetiyle ilgili genel sorular
description: Bu makalede Azure Site Recovery hakkındaki popüler genel sorular ele alınmaktadır.
ms.topic: conceptual
ms.date: 7/14/2020
ms.openlocfilehash: 974a119efaa69440aed6e6f9c3ac26ba26d7da16
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581179"
---
# <a name="general-questions-about-azure-site-recovery"></a>Azure Site Recovery hakkındaki genel sorular

Bu makalede Azure Site Recovery hakkında sık sorulan sorular özetlenmektedir. Belirli senaryolar için bu makaleleri gözden geçirin

- [Azure'a Azure VM olağanüstü durum kurtarma hakkındaki sorular](azure-to-azure-common-questions.md)
- [Azure'a VMware VM olağanüstü durum kurtarma hakkındaki sorular](vmware-azure-common-questions.md)
- [Azure'a Hyper-V VM olağanüstü durum kurtarma hakkındaki sorular](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Genel

### <a name="what-does-site-recovery-do"></a>Site Recovery ne işe yarar?

Site Recovery bölgeler, şirket içi sanal makineler ve fiziksel sunucular ile Azure arasında Azure VM 'Leri, ikincil veri merkezine ise şirket içi makineleri düzenleyerek ve otomatikleştirerek iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur. [Daha fazla bilgi edinin](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Docker diskine sahip bir sanal makineyi koruyabilir miyim?

Hayır, bu desteklenmeyen bir senaryodur.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Veri bütünlüğünü sağlamak için ne Site Recovery?

Veri bütünlüğünü sağlamak için Site Recovery tarafından gerçekleştirilen çeşitli ölçümler vardır. HTTPS protokolü kullanılarak tüm hizmetler arasında güvenli bir bağlantı oluşturulur. Bu, herhangi bir kötü amaçlı yazılımın veya dış varlıkların verileri denetleyemiyorum emin olmanızı sağlar. Alınan başka bir ölçü, sağlama toplamlarını kullanmaktır. Kaynak ve hedef arasındaki veri aktarımı, aralarında veri sağlama toplamı hesaplanırken yürütülür. Bu, aktarılan verilerin tutarlı olmasını sağlar.

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
Azure Pack, bulut platformu sistemi ve System Center tabanlı (2012 ve üzeri) dağıtımları destekliyoruz. Azure paketi ve Site Recovery tümleştirme hakkında [daha fazla bilgi edinin](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) .

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Tek bir Azure Paketi'ni ve tek bir VMM sunucusu dağıtımını destekliyor musunuz?
Evet, Hyper-V sanal makinelerini Azure 'a veya hizmet sağlayıcı siteleri arasında çoğaltabilirsiniz.  Hizmet sağlayıcı siteleri arasında çoğaltma yaparsanız Azure runbook tümleştirmesi 'nin kullanılamaz olduğunu unutmayın.

## <a name="pricing"></a>Fiyatlandırma

### <a name="where-can-i-find-pricing-information"></a>Fiyatlandırma bilgilerini nerede bulabilirim?
[Site Recovery fiyatlandırma](https://azure.microsoft.com/pricing/details/site-recovery/) ayrıntılarını gözden geçirin.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Site Recovery kullanımı sırasında yaklaşık ücretleri nasıl hesaplayabilirim?

Site Recovery kullanırken maliyetleri tahmin etmek için [Fiyatlandırma hesaplayıcısı](https://aka.ms/asr_pricing_calculator) ' nı kullanabilirsiniz.

Maliyetlerde ayrıntılı tahmin için, [VMware](./site-recovery-deployment-planner.md) veya [Hyper-V](https://aka.ms/asr-deployment-planner)için dağıtım planlayıcısı aracını çalıştırın ve [maliyet tahmini raporunu](./site-recovery-vmware-deployment-planner-cost-estimation.md)kullanın.


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Yönetilen diskler artık VMware VM 'lerini ve fiziksel sunucuları çoğaltmak için kullanılır. Yönetilen diskler ile önbellek depolama hesabı için ek ücret ödemem gerekiyor mu?

Hayır, önbellek için ek ücret alınmaz. Standart depolama hesabına çoğaltma yaptığınızda, bu önbellek depolaması aynı hedef depolama hesabının bir parçasıdır.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Ayda bir Kullanıcı Azure Site Recovery. Her korumalı örnek için yine de ilk 31 gün ücretsiz almaya devam ediyorum mi?

Evet. Her korumalı örnek ilk 31 gün için Azure Site Recovery ücret vermez. Örneğin, son 6 ay boyunca 10 örnek koruyorsanız ve Azure Site Recovery bir 11 örneği bağladığınızda, ilk 31 gün boyunca 11. örnek için ücret alınmaz. İlk 10 örnek 31 günden uzun süredir korunduğundan bu yana Azure Site Recovery ücret almaya devam eder.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>İlk 31 gün boyunca diğer Azure ücretlerine tabi mıyım?

Evet, Site Recovery korunan bir örneğin ilk 31 günü boyunca ücretsiz olsa da Azure depolama, depolama işlemleri ve veri aktarımı için ücret ödemeniz istenebilir. Kurtarılan bir sanal makine de Azure işlem ücretlerine neden olabilir.


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
Sanal makineler ve fiziksel sunucular için, şirket içi siteler arasında çoğaltma geçişi desteklenir. Azure 'a çoğaltılan sanal makineler ve fiziksel sunucular için hem çapraz geçiş hem de [bekleyen şifreleme (Azure 'da)](../storage/common/storage-service-encryption.md) desteklenir.

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Azure 'dan Azure Site Recovery, Azure 'un mikro hizmetleri arasındaki tüm iletişimler için TLS 1,2 kullanır mi?
Evet, TLS 1,2 Protokolü, Azure 'dan Azure Site Recovery senaryosu için varsayılan olarak zorlanır. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>VMware 'den Azure 'a ve fiziksel sunucudan Azure Site Recovery senaryolara TLS 1,2 ' I nasıl zorlayabilirim?
Çoğaltılan öğeler üzerinde yüklü olan Mobility aracıları yalnızca TLS 1,2 üzerinde Işlem sunucusuyla iletişim kurar. Bununla birlikte, yapılandırma sunucusundan Azure 'a ve Işlem sunucusundan Azure 'a iletişim, TLS 1,1 veya 1,0 ' de olabilir. Lütfen tüm yapılandırma sunucularında ve sizin tarafınızdan ayarlanan Işlem sunucularında TLS 1,2 ' i zorlamak için [yönergeleri](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) izleyin.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>HyperV-Azure Site Recovery senaryolarında TLS 1,2 ' I nasıl zorlayabilirim?
Azure Site Recovery mikro hizmetleri arasındaki tüm iletişimler TLS 1,2 protokolünde olur. Site Recovery, sistemde (OS) yapılandırılan güvenlik sağlayıcılarını kullanır ve kullanılabilir en son TLS protokolünü kullanır. Bir tek yapmanız gereken, kayıt defterinde TLS 1,2 ' i açıkça etkinleştirmektir ve Site Recovery hizmetlerle iletişim kurmak için TLS 1,2 ' i kullanmaya başlayacaktır. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Çoğaltma verilerini okumak/yazmak için Site Recovery hizmeti tarafından erişilen depolama hesaplarıma kısıtlı erişimi nasıl zorlayabilirim?
*Kimlik* ayarına giderek, kurtarma hizmetleri kasasının yönetilen kimliği üzerinde geçiş yapabilirsiniz. Kasa Azure Active Directory kaydedildikten sonra depolama hesaplarınıza gidebilir ve kasaya aşağıdaki rol atamalarını verebilirsiniz:

- Kaynak Yöneticisi tabanlı depolama hesapları (Standart tür):
  - [Katkıda Bulunan](../role-based-access-control/built-in-roles.md#contributor)
  - [Depolama Blob Verileri Katkıda Bulunanı](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Kaynak Yöneticisi tabanlı depolama hesapları (Premium türü):
  - [Katkıda Bulunan](../role-based-access-control/built-in-roles.md#contributor)
  - [Depolama Blob Verileri Sahibi](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klasik depolama hesapları:
  - [Klasik depolama hesabı Katılımcısı](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Klasik depolama hesabı anahtar operatörü hizmet rolü](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

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

Evet, Azure bölgeleri arasında Azure VM 'Leri için olağanüstü durum desteği Site Recovery. Azure VM olağanüstü durum kurtarma ile ilgili [sık sorulan soruları gözden geçirin](azure-to-azure-common-questions.md) . Aynı kıta iki Azure bölgesi arasında çoğaltmak istiyorsanız lütfen Azure 'da Azure DR teklifini kullanın. Yapılandırma sunucusu/işlem sunucusu ve ExpressRoute bağlantıları ayarlama gereksinimi yoktur.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>VMware VM 'Leri için olağanüstü durum kurtarma destekleniyor mu?

Evet, Site Recovery şirket içi VMware VM 'lerinin olağanüstü durum kurtarmasını destekler. VMware VM 'lerinin olağanüstü durum kurtarması için [sık sorulan soruları gözden geçirin](vmware-azure-common-questions.md) .

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Hyper-V VM 'Leri için olağanüstü durum kurtarma destekleniyor mu?
Evet, Site Recovery şirket içi Hyper-V VM 'lerinin olağanüstü durum kurtarmasını destekler. Hyper-V VM 'lerinin olağanüstü durum kurtarma için [genel soruları gözden geçirin](hyper-v-azure-common-questions.md) .

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>Fiziksel sunucular için olağanüstü durum kurtarma destekleniyor mu?
Evet, Site Recovery Windows ve Linux çalıştıran şirket içi fiziksel sunucuların Azure 'a veya ikincil bir siteye olağanüstü durum kurtarmayı destekler. [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)'a olağanüstü durum kurtarma ve[ikincil bir siteye](vmware-physical-secondary-support-matrix.md#replicated-vm-support)yönelik gereksinimler hakkında bilgi edinin.
Fiziksel sunucuların yük devretmeden sonra Azure 'da VM olarak çalışacağını unutmayın. Azure 'dan şirket içi fiziksel sunucuya yeniden çalışma şu anda desteklenmiyor. Yalnızca bir VMware sanal makinesine geri dönebilirsiniz.





## <a name="replication"></a>Çoğaltma

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Azure 'a siteden siteye VPN üzerinden çoğaltma yapabilir miyim?
Azure Site Recovery, verileri bir Azure depolama hesabına veya yönetilen disklere ortak bir uç nokta üzerinden çoğaltır. Çoğaltma, siteden siteye VPN üzerinde değil. 

### <a name="why-cant-i-replicate-over-vpn"></a>Neden VPN üzerinden çoğaltılamam?

Azure 'a çoğaltma yaptığınızda, çoğaltma trafiği bir Azure depolama alanının genel uç noktalarına ulaşır. Bu nedenle, yalnızca genel İnternet üzerinden veya ExpressRoute aracılığıyla (Microsoft eşlemesi veya var olan bir genel eşleme) çoğaltma yapabilirsiniz.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Çoğaltma için Rivervisteelheads kullanabilir miyim?

İş ortağımızda, Azure Site Recovery ile çalışma hakkında ayrıntılı rehberlik sağlar. [Çözüm kılavuzunu](https://community.riverbed.com/s/article/DOC-4627)gözden geçirin.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Sanal makineleri Azure 'a çoğaltmak için ExpressRoute kullanabilir miyim?
Evet, [ExpressRoute](concepts-expressroute-with-site-recovery.md) , şirket içi sanal makineleri Azure 'a çoğaltmak için kullanılabilir.

- Azure Site Recovery, verileri bir Azure depolama alanına ortak bir uç nokta üzerinden çoğaltır. Site Recovery çoğaltma için ExpressRoute kullanmak üzere [Microsoft eşlemesini](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ayarlamanız veya mevcut bir [ortak eşlemeyi](../expressroute/about-public-peering.md) (yeni devreler için kullanım dışı) kullanmanız gerekir.
- Microsoft eşleme, çoğaltma için önerilen yönlendirme etki alanıdır.
- Çoğaltma, özel eşleme üzerinden desteklenmez.
- VMware makinelerini veya fiziksel makineleri koruyorsanız, yapılandırma sunucusu için [ağ gereksinimlerinin](vmware-azure-configuration-server-requirements.md#network-requirements) de karşılandığından emin olun. Site Recovery çoğaltmanın düzenlenmesi için yapılandırma sunucusu için belirli URL 'lere bağlantı gerekir. ExpressRoute Bu bağlantı için kullanılamaz.
- Sanal makineler bir Azure sanal ağına yük devretdikten sonra, Azure sanal ağı ile [özel eşleme](../expressroute/expressroute-circuit-peerings.md#privatepeering) kurulumunu kullanarak bunlara erişebilirsiniz.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Azure 'a çoğaltma yapıyorsanız, ne tür bir depolama hesabı veya yönetilen disk gereksinimim vardır?

Depolama hesaplarını hedef depolama olarak kullanmak Azure Site Recovery tarafından desteklenmez. Makineler için hedef depolama olarak yönetilen diskleri kullanmanız önerilir. Yönetilen diskler yalnızca veri dayanıklılığı için LRS türünü destekler.

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
* [Hyper-V VM 'lerini Azure 'a çoğaltmak için kapasite planlaması](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Linux sunucularında uygulama tutarlılığı ile çoğaltmayı etkinleştirebilir miyim? 
Evet. Linux Işlem sistemi için Azure Site Recovery, uygulama tutarlılığı için uygulama özel komut dosyalarını destekler. Ön ve son seçenekleri olan özel betik, uygulama tutarlılığı sırasında Azure Site Recovery Mobility Aracısı tarafından kullanılır. Aşağıda, etkinleştirme adımları verilmiştir.

1. Makinede kök olarak oturum açın.
2. Dizini Mobility Aracısı yüklemesi konumuna Azure Site Recovery değiştirin. Varsayılan değer "/usr/local/ASR"<br>
    `# cd /usr/local/ASR`
3. Dizini, install location altındaki "bir", "dosyası" olarak değiştirin<br>
    `# cd VX/scripts`
4. Kök kullanıcı için yürütme izinleriyle "customscript.sh" adlı bir bash Shell betiği oluşturun.<br>
    a. Betik "--Pre" ve "--Post" (çift kesik çizgi) komut satırı seçeneklerini desteklemelidir<br>
    b. Betik, ön seçenekle çağrıldığında, uygulama giriş/çıkışını dondurmalı ve son seçenek ile çağrıldığında, uygulama girişini/çıkışını çözülmelidir.<br>
    c. Örnek şablon-<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Uygulama tutarlılığı gerektiren uygulamalar için ön ve son adımlara yönelik dondurma ve çözme giriş/çıkış komutlarını ekleyin. Bunları belirten başka bir betik eklemeyi ve ön ve sonrası seçenekleri olan "customscript.sh" öğesinden çağırmayı seçebilirsiniz.

>[!Note]
>Özel betikleri desteklemek için Site Recovery Agent sürümü 9,24 veya üzeri olmalıdır.

## <a name="replication-policy"></a>Çoğaltma ilkesi

### <a name="what-is-a-replication-policy"></a>Çoğaltma ilkesi nedir?

Çoğaltma İlkesi, kurtarma noktalarının bekletme geçmişine yönelik ayarları tanımlar. İlke, uygulamayla tutarlı anlık görüntülerin sıklığını da tanımlar. Varsayılan olarak, Azure Site Recovery varsayılan ayarları ile yeni bir çoğaltma ilkesi oluşturur:

- Kurtarma noktalarının bekletme geçmişi için 24 saat.
- uygulamayla tutarlı anlık görüntülerin sıklığı için 4 saat.

### <a name="what-is-a-crash-consistent-recovery-point"></a>Kilitlenme ile tutarlı bir kurtarma noktası nedir?

Kilitlenme ile tutarlı bir kurtarma noktası, anlık görüntü sırasında sunucudan güç kablosunu çektiği gibi disk üzerindeki verilere sahiptir. Kilitlenme ile tutarlı kurtarma noktası, anlık görüntü çekilirken bellekte olan herhangi bir şeyi içermez.

Günümüzde, çoğu uygulama kilitlenme ile tutarlı anlık görüntülerden iyi bir şekilde kurtarabilir. Kilitlenme ile tutarlı bir kurtarma noktası, genellikle veritabanı olmayan işletim sistemleri ve dosya sunucuları, DHCP sunucuları ve yazdırma sunucuları gibi uygulamalar için yeterlidir.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Kilitlenme ile tutarlı kurtarma noktası oluşturma sıklığı nedir?

Site Recovery 5 dakikada bir çökme ile tutarlı bir kurtarma noktası oluşturur.

### <a name="what-is-an-application-consistent-recovery-point"></a>Uygulamayla tutarlı kurtarma noktası nedir?

Uygulamayla tutarlı kurtarma noktaları uygulamayla tutarlı anlık görüntülerden oluşturulur. Uygulamayla tutarlı kurtarma noktaları aynı verileri çökme ile tutarlı anlık görüntülerle yakalar, ayrıca bellekteki verileri ve işlemdeki tüm işlemleri de yakalarsınız.

Ek içerikleri nedeniyle, uygulamayla tutarlı anlık görüntüler en çok söz konusu ve en uzun sürer. Veritabanı işletim sistemleri ve SQL Server gibi uygulamalar için uygulamayla tutarlı kurtarma noktaları öneririz.

>[!Note]
>Uygulama ile tutarlı kurtarma noktalarının oluşturulması, 64 'den fazla birimi varsa Windows makinesinde başarısız olur.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Uygulamayla tutarlı kurtarma noktalarının uygulama performansı üzerindeki etkisi nedir?

Uygulamayla tutarlı kurtarma noktaları, bellekteki ve işlemdeki tüm verileri yakalar. Kurtarma noktaları bu verileri yakaladığı için, uygulamayı sessiz bir şekilde açmak için Windows üzerinde Birim Gölge Kopyası Hizmeti gibi bir çerçeve gerektirir. Yakalama işlemi sık sık ise, iş yükü zaten meşgul olduğunda performansı etkileyebilir. Veritabanı olmayan iş yükleri için uygulamayla tutarlı kurtarma noktaları için düşük bir sıklık kullanmanızı önermiyoruz. Veritabanı iş yükü için bile 1 saat yeterlidir.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Uygulamayla tutarlı kurtarma noktası oluşturma işlemi için en düşük sıklık nedir?

Site Recovery, en az 1 saatlik bir sıklıkta uygulamayla tutarlı bir kurtarma noktası oluşturabilir.

### <a name="how-are-recovery-points-generated-and-saved"></a>Kurtarma noktaları nasıl oluşturulup kaydedilir?

Site Recovery kurtarma noktaları oluşturma hakkında bilgi edinmek için bir çoğaltma ilkesi örneği görelim. Bu çoğaltma ilkesinde, 24 saatlik bir bekletme penceresi ve 1 saatlik uygulamayla tutarlı sıklık anlık görüntüsüne sahip bir kurtarma noktası bulunur.

Site Recovery 5 dakikada bir çökme ile tutarlı bir kurtarma noktası oluşturur. Bu sıklığı değiştiremezsiniz. Son bir saat için, 12 çökme ile tutarlı nokta ve 1 uygulamayla tutarlı nokta arasından seçim yapabilirsiniz. Zaman ilerledikçe Site Recovery, tüm kurtarma noktalarını son saatin ötesinde ayıklar ve yalnızca saat başına 1 kurtarma noktası kaydeder.

Aşağıdaki ekran görüntüsünde örnek gösterilmektedir. Ekran görüntüsünde:

- Son saat içinde, 5 dakikalık bir sıklıkta kurtarma noktaları vardır.
- Son saatin ötesinde Site Recovery yalnızca 1 kurtarma noktası tutar.

   ![Oluşturulan kurtarma noktalarının listesi](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Ne kadar geri kurtarabilirim?

Kullanabileceğiniz en eski kurtarma noktası 72 saattir.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>24 saat olan bir çoğaltma ilkem var. Bir sorun Site Recovery, 24 saatten uzun bir bir kurtarma noktası oluşturmasını engelliyorsa ne olur? Önceki kurtarma noktalarım kaybedilir mi?

Hayır, Site Recovery önceki tüm kurtarma noktalarınızı tutacaktır. Kurtarma noktalarının bekletme penceresine bağlı olarak, Site Recovery en eski noktayı yalnızca yeni noktaları oluşturursa değiştirir. Sorun nedeniyle, Site Recovery yeni kurtarma noktası üretemiyor. Yeni kurtarma noktaları olana kadar, bekletme penceresine erişduktan sonra tüm eski noktaların kalması gerekir.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>VM 'de çoğaltma etkinleştirildikten sonra, çoğaltma ilkesini nasıl değiştirebilirim?

  >  **Altyapı**  >  **çoğaltma ilkelerine** Site Recovery kasa Site Recovery gidin. Düzenlemek istediğiniz ilkeyi seçin ve değişiklikleri kaydedin. Tüm değişiklikler, mevcut tüm çoğaltmalar için de geçerli olacaktır.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Tüm kurtarma noktaları VM 'nin ya da bir Farklıdan oluşan tüm bir kopyası mı?

Oluşturulan ilk kurtarma noktasının tamamen kopyası vardır. Tüm ardışık kurtarma noktalarında delta değişiklikleri vardır.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Kurtarma noktalarının bekletme döneminin artırılması depolama maliyetini arttırır mi?

Evet, bekletme süresini 24 saat ile 72 saate artırırsanız Site Recovery, kurtarma noktalarını ek 48 saat boyunca kaydeder. Eklenen süre, depolama ücretlerine tabi olacaktır. Örneğin, tek bir kurtarma noktası, ayda $0,16 GB başına maliyet ile 10 GB 'lik Delta değişikliklere sahip olabilir. Ek ücretler ayda $1,60 × 48 olacaktır.


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
* VMware VM 'lerini ve fiziksel sunucuları geri dönme hakkında [daha fazla bilgi edinin](./vmware-azure-failback.md)

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Şirket içi Ana Bilgisayarım yanıt vermiyorsa veya kilitlenirse farklı bir konağa yeniden oturum açabilir miyim?
Evet, Azure 'dan farklı bir konağa yeniden çalışma yapmak için alternatif konum kurtarma kullanabilirsiniz.

* [VMware sanal makineleri için](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V sanal makineleri için](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

### <a name="what-is-the-difference-between-complete-migration-commit-and-disable-replication"></a>Geçişi tamamlama, çoğaltmayı tamamlama ve devre dışı bırakma arasındaki fark nedir?

Kaynak konumdan bir makine hedef konuma yük devrettikten sonra, aralarından seçim yapabileceğiniz üç seçenek bulunur. Üç farklı amaç sunar-

1.  **Geçişin tamamı** artık kaynak konumuna geri dönmeyeceğiniz anlamına gelir. Hedef bölgeye geçiş yaptıysanız ve şimdi tamamladınız. Geçişin tamamını tamamlamak ve sonra da çoğaltmayı devre dışı bırakmak için öğesini tıklatın. 
2.  **İşleme** , çoğaltma sürecinizin sona gerekmediği anlamına gelir. Tüm yapılandırma ile birlikte çoğaltma öğesi kalır ve makinelerinizin kaynak bölgeye çoğaltılmasını sağlamak için daha sonraki bir zaman noktasında **yeniden koruma** sağlayabilirsiniz. 
3.  **Çoğaltmayı devre dışı bırak** , çoğaltmayı devre dışı bırakır ve ilgili tüm yapılandırmaları kaldırır. Hedef bölgede zaten mevcut olan makineyi etkilemez.

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
---
title: Azure Site Recovery ile Hyper-V olağanüstü durum kurtarma için sık sorulan sorular
description: Bu makalede, şirket içi Hyper-V sanal makineleri için Azure Site Recovery sitesini kullanarak Azure 'a olağanüstü durum kurtarma ayarlama hakkında sık sorulan sorular özetlenmektedir.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 11/12/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 8f3a04c70b88987fc91dbed3c186d04826b75726
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954055"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Sık sorulan sorular-Hyper-V-Azure olağanüstü durum kurtarma

Bu makale, şirket içi Hyper-V VM 'lerini Azure 'a çoğaltarak görtiğimiz yaygın soruların yanıtlarını sağlar. 

## <a name="general"></a>Genel

### <a name="how-is-site-recovery-priced"></a>Site Recovery nasıl fiyatlandırılır?
[Azure Site Recovery fiyatlandırma](https://azure.microsoft.com/pricing/details/site-recovery/) ayrıntılarını gözden geçirin.

### <a name="how-do-i-pay-for-azure-vms"></a>Nasıl yaparım? Azure VM 'Leri için ödeme yapılsın mı?
Çoğaltma sırasında, veriler Azure depolama 'ya çoğaltılır ve herhangi bir VM değişikliğini ödemezsiniz. Azure 'a yük devretme işlemi çalıştırdığınızda, Site Recovery otomatik olarak Azure IaaS sanal makineleri oluşturur. Bundan sonra Azure 'da kullandığınız işlem kaynakları için faturalandırılırsınız.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Genel Amaçlı v2 depolama hesabına çoğaltılırken maliyette herhangi bir farklılık var mı?

Azure Site Recovery işlemler ağır olduğundan, genellikle GPv2 depolama hesaplarında tahakkuk eden işlem maliyetinde bir artış görürsünüz. Değişikliği tahmin etmek için [daha fazla bilgi edinin](../storage/common/storage-account-upgrade.md#pricing-and-billing) .

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Site Recovery ile çoğaltmayı düzenlemek için Hyper-V ' d e ne yapmam gerekir?

Hyper-V ana bilgisayar sunucusu için sahip olmanız gerekenler dağıtım senaryosuna bağlıdır. Aşağıdaki makalelerden Hyper-V önkoşullarını inceleyin:

* [Hyper-V VM 'lerini (VMM olmadan) Azure 'a çoğaltma](site-recovery-hyper-v-site-to-azure.md)
* [Hyper-V VM 'lerini (VMM ile) Azure 'a çoğaltma](site-recovery-vmm-to-azure.md)
* [Hyper-V sanal makinelerini ikincil veri merkezine çoğaltma](site-recovery-vmm-to-vmm.md)
* İkincil bir veri merkezine çoğaltma yapıyorsanız, [Hyper-V VM 'leri Için desteklenen konuk işletim sistemleri](https://technet.microsoft.com/library/mt126277.aspx)hakkında bilgi edinin.
* Azure 'a çoğaltma yapıyorsanız, Site Recovery [Azure tarafından desteklenen](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)tüm konuk işletim sistemlerini destekler.

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Hyper-V bir istemci işletim sisteminde çalışırken VM 'Leri koruyabilir miyim?
Hayır, VM'lerin desteklenen bir Windows sunucusu makinesinde çalışan Hyper-V ana bilgisayar sunucusunda bulunması gerekir. Bir istemci bilgisayarı korumanız gerekiyorsa, bunu [Azure](site-recovery-vmware-to-azure.md) 'a veya [ikincil veri merkezine](site-recovery-vmware-to-vmware.md)fiziksel bir makine olarak çoğaltabilirsiniz.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hyper-V konaklarının VMM bulutlarında olması gerekir mi?
İkincil bir veri merkezine çoğaltmak istiyorsanız, Hyper-V VM 'lerinin VMM bulutundaki Hyper-V konakları sunucularında olması gerekir. Azure 'a çoğaltmak istiyorsanız VM 'Leri VMM bulutları ile veya olmayan bir şekilde çoğaltabilirsiniz. Azure 'da Hyper-V çoğaltma hakkında [daha fazla bilgi edinin](tutorial-hyper-v-to-azure.md) .


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Hyper-V 2.nesil sanal makinelerini Azure'a çoğaltabilir miyim?
Evet. Site Recovery yük devretme sırasında 2. nesil 2. nesil 1 ' e dönüştürür. Yeniden çalışma sırasında makine 2. nesil 'e geri dönüştürüldü. [Daha fazlasını okuyun](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Tek bir VMM sunucusuna sahip olsam da Site Recovery'yi VMM ile dağıtabilir miyim?

Evet. VMM bulutundaki Hyper-V sunucularındaki VM 'Leri Azure 'a çoğaltabilir veya aynı sunucuda VMM bulutları arasında çoğaltma yapabilirsiniz. Şirket içi ile şirket içi çoğaltma için hem birincil hem de ikincil sitelerde VMM sunucusuna sahip olmanız önerilir. 

### <a name="what-do-i-need-in-azure"></a>Azure 'da neye ihtiyacım var?
Bir Azure aboneliğiniz, bir kurtarma hizmetleri Kasası, depolama hesabı ve sanal ağ gerekir. Kasa, depolama hesabı ve ağın aynı bölgede olması gerekir.

### <a name="what-azure-storage-account-do-i-need"></a>Hangi Azure depolama hesabına ihtiyacım var?
LRS veya GRS depolama hesabınız olması gerekir. Bölgesel bir kesintinin meydana gelmesi veya birincil bölgenin kurtarılamaması durumunda verilerin korunması için GRS'yi tavsiye ederiz. Premium Depolama desteklenir.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Azure hesabımın VM oluşturmak için izinleri olması gerekiyor mu?
Abonelik yöneticisiyseniz, ihtiyacınız olan çoğaltma izinlerine sahip olursunuz. Değilseniz, Site Recovery yapılandırırken belirttiğiniz sanal ağ ve kaynak grubunda bir Azure VM oluşturma ve seçili depolama hesabına yazma izinleri gerekir. [Daha fazla bilgi edinin](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Çoğaltma verileri Site Recovery gönderildi mi?
Hayır, Site Recovery çoğaltılan verileri engellemez ve sanal makinelerinizdeki neleri çalıştırdıklarınız hakkında bilgi içermez. Çoğaltma verileri, Hyper-V konakları ile Azure depolama arasında değiştirilir. Site Recovery bu verilere müdahale edemez. Yalnızca çoğaltma ve yük devretme işlemlerini düzenlemek için gereken meta veriler Site Recovery hizmetine gönderilir.  

Site Recovery ISO 27001:2013, 27018, HIPAA, DPA sertifikalı ve SOC2 ve Fedrajab değerlendirmesi sürecinde.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Coğrafi bölge dahilinde şirket içi meta verileri tutabilir mi?
Evet. Bir bölgede kasa oluşturduğunuzda, Site Recovery tarafından kullanılan tüm meta verilerin bu bölgenin coğrafi sınırında kalmasını sağlamaktır.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery çoğaltma işlemini şifreleyebilir mi?
Evet, hem [Azure 'da](https://docs.microsoft.com/azure/storage/storage-service-encryption) hem de aktarım sırasında şifreleme desteklenir.


## <a name="deployment"></a>Dağıtım

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Azure çoğaltma ile Hyper-V arasında ne yapabilirim?

- **Olağanüstü durum kurtarma**: tam olağanüstü durum kurtarma ayarlayabilirsiniz. Bu senaryoda, şirket içi Hyper-V VM 'lerini Azure depolama 'ya çoğaltmanız gerekir:
    - VM 'Leri Azure 'a çoğaltabilirsiniz. Şirket içi altyapınız kullanılamıyorsa Azure 'a yük devreolursunuz.
    - Yük devretmek için, çoğaltılan veriler kullanılarak Azure VM 'Leri oluşturulur. Azure VM 'lerinde uygulamalara ve iş yüklerine erişebilirsiniz.
    - Şirket içi veri merkeziniz yeniden kullanılabilir olduğunda, Azure 'dan şirket içi sitenize yeniden yük devredebilirler.
- **Geçiş**: şirket içi Hyper-V VM 'lerini Azure depolama 'ya geçirmek için Site Recovery kullanabilirsiniz. Ardından Şirket içinden Azure 'a yük devreolursunuz. Yük devretmeden sonra, uygulamalarınız ve iş yükleriniz Azure VM 'lerde kullanılabilir ve çalışır.


### <a name="what-do-i-need-on-premises"></a>Şirket içinde ne yapmam gerekir?

Bir veya daha fazla tek başına veya kümelenmiş Hyper-V konağında çalışan bir veya daha fazla VM 'ye ihtiyacınız vardır. Ayrıca, System Center Virtual Machine Manager (VMM) tarafından yönetilen konaklarda çalışan VM 'Leri çoğaltabilirsiniz.
- VMM çalıştırmıyorsanız, Site Recovery dağıtımı sırasında Hyper-V konakları ve kümeleri Hyper-V sitelerine toplamanız gerekir. Site Recovery aracılarını (Azure Site Recovery sağlayıcısı ve kurtarma hizmetleri Aracısı) her Hyper-V konağına yüklersiniz.
- Hyper-V konakları bir VMM bulutunda bulunuyorsa, çoğaltmayı VMM 'de düzenleyebilirsiniz. Site Recovery sağlayıcıyı VMM sunucusuna ve kurtarma hizmetleri aracısını her Hyper-V konağına yüklersiniz. VMM mantıksal/VM ağları ve Azure sanal ağları arasında eşleme yapabilirsiniz.
- Hyper-V ' d e Azure mimarisine [daha fazla bilgi edinin](hyper-v-azure-architecture.md) .

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Hyper-V kümesinde bulunan VM 'Leri çoğaltabilir miyim?

Evet, Site Recovery kümelenmiş Hyper-V konaklarının kullanılmasını destekler. Aşağıdakilere dikkat edin:

- Kümenin tüm düğümleri aynı kasaya kaydedilmelidir.
- VMM kullanmıyorsanız, kümedeki tüm Hyper-V konakları aynı Hyper-V sitesine eklenmelidir.
- Azure Site Recovery sağlayıcısı ve kurtarma hizmetleri aracısını kümedeki her Hyper-V konağına yüklersiniz ve her konağı bir Hyper-V sitesine eklersiniz.
- Kümede belirli bir adım gerçekleştirilmesi gerekmez.
- Hyper-V için Dağıtım Planlayıcısı aracı çalıştırırsanız, araç, çalıştıran ve VM 'nin çalıştığı düğümden profil verilerini toplar. Araç kapatılan bir düğümden herhangi bir veri toplayamıyor, ancak bu düğüm, bu düğümü izlerler. Düğüm çalışır duruma geçtikten sonra, araç, VM profil verilerini (VM profil VM listesinin bir parçasıysa ve düğüm üzerinde çalışıyorsa) buradan toplamaya başlar.
- Bir Site Recovery kasasındaki bir Hyper-V konağındaki VM, aynı kümedeki farklı bir Hyper-V konağına veya tek başına bir konağa geçerse, VM için çoğaltma etkilenmez. Hyper-V konağının [önkoşulları](hyper-v-azure-support-matrix.md#on-premises-servers)karşılaması ve bir Site Recovery kasasında yapılandırılması gerekir. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Hyper-V bir istemci işletim sisteminde çalışırken VM 'Leri koruyabilir miyim?
Hayır, VM'lerin desteklenen bir Windows sunucusu makinesinde çalışan Hyper-V ana bilgisayar sunucusunda bulunması gerekir. Bir istemci bilgisayarı korumanız gerekiyorsa, bunu Azure 'a [fiziksel bir makine olarak çoğaltabilirsiniz](physical-azure-disaster-recovery.md) .

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Hyper-V 2.nesil sanal makinelerini Azure'a çoğaltabilir miyim?
Evet. Site Recovery yük devretme sırasında 2. nesil 2. nesil 1 ' e dönüştürür. Yeniden çalışma sırasında makine 2. nesil 'e geri dönüştürüldü.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Site Recovery senaryolarını bir SDK ile otomatikleştirebilirim miyim?
Evet. Rest API'si, PowerShell veya Azure SDK kullanarak Site Recovery iş akışlarını otomatikleştirebilirsiniz. PowerShell kullanarak Hyper-V ' d i Azure 'a çoğaltmak için şu anda desteklenen senaryolar:

- [PowerShell kullanarak Hyper-V ' d i VMM olmadan çoğaltma](hyper-v-azure-powershell-resource-manager.md)
- [PowerShell kullanarak Hyper-V ' d i VMM ile çoğaltma](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Çoğaltma

### <a name="where-do-on-premises-vms-replicate-to"></a>Şirket içi VM 'Ler nerede çoğaltılır?
Veriler Azure Storage 'a çoğaltılır. Yük devretme çalıştırdığınızda Site Recovery, depolama hesabından otomatik olarak Azure VM 'Leri oluşturur.

### <a name="what-apps-can-i-replicate"></a>Hangi uygulamaları çoğaltabilirim?
[Çoğaltma gereksinimleriyle](hyper-v-azure-support-matrix.md#replicated-vms)uyumlu bir Hyper-V VM çalıştıran tüm uygulamaları veya iş yüklerini çoğaltabilirsiniz. Site Recovery, uygulamaların yük devretmesini ve akıllı bir duruma geri dönmesi için uygulamayla uyumlu çoğaltma desteği sağlar. Site Recovery, SharePoint, Exchange, Dynamics, SQL Server ve Active Directory gibi Microsoft uygulamalarıyla tümleşir ve Oracle, SAP, IBM ve Red hat gibi önde gelen satıcılarla yakından çalışır. İş yükü koruması hakkında [daha fazla bilgi edinin](site-recovery-workload.md).

### <a name="whats-the-replication-process"></a>Çoğaltma işlemi nedir?

1. İlk çoğaltma tetiklendiğinde, bir Hyper-V VM anlık görüntüsü alınır.
2. VM 'deki sanal sabit diskler, hepsi Azure 'a kopyalanana kadar tek tek çoğaltılır. Bu işlem, VM boyutuna ve ağ bant genişliğine bağlı olarak biraz zaman alabilir. Ağ bant genişliğini artırma hakkında bilgi edinin.
3. İlk çoğaltma devam ederken disk değişiklikleri oluşursa, Hyper-V çoğaltma çoğaltma Izleyicisi değişiklikleri Hyper-V çoğaltma günlükleri (. HRL) olarak izler. Bu günlük dosyaları, disklerle aynı klasörde bulunur. Her diskin, ikincil depolamaya gönderilen ilişkili bir. HRL dosyası vardır. İlk çoğaltma sırasında anlık görüntü ve günlük dosyaları disk kaynaklarını kullanır.
4. İlk çoğaltma tamamlandığında, VM anlık görüntüsü silinir.
5. Günlükteki tüm disk değişiklikleri eşitlenir ve üst diskle birleştirilir.
6. İlk çoğaltma tamamlandıktan sonra, sanal makine işinin korumasını Sonlandır işi çalışır. Ağ ve diğer çoğaltma sonrası ayarlarını yapılandırır, böylece VM korunur.
7. Bu aşamada, yük devretmeye uygun olduğundan emin olmak için VM ayarlarını kontrol edebilirsiniz. VM için bir olağanüstü durum kurtarma detayına (yük devretme testi), beklendiği gibi yük devretmesinin başarısız olduğunu kontrol etmek için çalıştırabilirsiniz.
8. İlk çoğaltmadan sonra Delta çoğaltma, çoğaltma ilkesine uygun olarak başlar.
9. Değişiklikler günlüğe kaydedilir. HRL dosyaları. Çoğaltma için yapılandırılmış her diskin ilişkili bir .hrl dosyası vardır.
10. Günlük, müşterinin depolama hesabına gönderilir. Bir günlük Azure 'a geçişte, birincil diskteki değişiklikler aynı klasördeki başka bir günlük dosyasında izlenir.
11. Hem ilk hem de değişim çoğaltması sırasında, VM 'yi Azure portal izleyebilirsiniz.

Çoğaltma işlemi hakkında [daha fazla bilgi edinin](hyper-v-azure-architecture.md#replication-process) .

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Siteden siteye VPN ile Azure 'a çoğaltma yapabilir miyim?

Site Recovery, Şirket içindeki verileri ortak bir uç nokta üzerinden Azure depolama 'ya veya ExpressRoute Microsoft eşlemesi 'ni kullanarak çoğaltır. Siteden siteye VPN ağı üzerinden çoğaltma desteklenmez.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>ExpressRoute ile Azure 'a çoğaltma yapabilir miyim?

Evet, ExpressRoute, VM 'Leri Azure 'a çoğaltmak için kullanılabilir. Site Recovery, verileri bir Azure depolama hesabına ortak bir uç nokta üzerinden çoğaltır ve Site Recovery çoğaltma için [Microsoft eşlemesi](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ayarlamanız gerekir. VM 'Ler bir Azure sanal ağına yük devredikten sonra, [özel eşleme](../expressroute/expressroute-circuit-peerings.md#privatepeering)kullanarak bunlara erişebilirsiniz.


### <a name="why-cant-i-replicate-over-vpn"></a>Neden VPN üzerinden çoğaltılamam?

Azure 'a çoğalttığınızda, çoğaltma trafiği bir Azure depolama hesabının genel uç noktalarına ulaşır. Bu nedenle, yalnızca ExpressRoute (Microsoft eşlemesi) ile genel İnternet üzerinden çoğaltma yapabilir ve VPN çalışmaz. 

### <a name="what-are-the-replicated-vm-requirements"></a>Çoğaltılan VM gereksinimleri nelerdir?

Çoğaltma için, Hyper-V sanal makinesi desteklenen bir işletim sistemi çalıştırıyor olmalıdır. Ayrıca, VM 'nin Azure VM gereksinimlerini karşılaması gerekir. Destek matrisinden [daha fazla bilgi edinin](hyper-v-azure-support-matrix.md#replicated-vms) .

### <a name="how-often-can-i-replicate-to-azure"></a>Azure 'a ne sıklıkta çoğaltma yapabilirim?

Hyper-V VM 'Leri, her 30 saniyede bir (Premium Depolama hariç), 5 dakika veya 15 dakika olabilir.

### <a name="can-i-extend-replication"></a>Çoğaltmayı genişletebilir miyim?
Genişletilmiş veya zincir çoğaltma desteklenmez. Bu özelliği [geri bildirim forumuna](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)iste.

### <a name="can-i-do-an-offline-initial-replication"></a>Çevrimdışı bir ilk çoğaltma yapabilir miyim?
Bu özellik desteklenmez. Bu özelliği [geri bildirim forumuna](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)iste.

### <a name="can-i-exclude-disks"></a>Diskleri hariç tutabilir miyim?
Evet, diskleri çoğaltmanın dışında bırakabilirsiniz. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>VM 'Leri Dinamik disklerle çoğaltabilir miyim?
Dinamik diskler çoğaltılabiliyor. İşletim sistemi diski, temel bir disk olmalıdır.



## <a name="security"></a>Güvenlik

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Hyper-V konaklarına Site Recovery erişim gereksinimi

Site Recovery seçtiğiniz VM 'Leri çoğaltmak için Hyper-V konaklarına erişmesi gerekir. Site Recovery, Hyper-V konaklarına aşağıdakileri yükleyerek:

- VMM 'yi çalıştırsanız, Azure Site Recovery sağlayıcısı ve kurtarma hizmetleri Aracısı her bir konağa yüklenir.
- VMM çalıştırıyorsanız, kurtarma hizmetleri Aracısı her bir konağa yüklenir. Sağlayıcı VMM sunucusunda çalışır.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Hyper-V VM 'lerine ne Site Recovery yüklenir?

Site Recovery, çoğaltma için etkin Hyper-V VM 'lerine açık bir şekilde hiçbir şey yüklemez.




## <a name="failover-and-failback"></a>Yük devretme ve yeniden çalışma


### <a name="how-do-i-fail-over-to-azure"></a>Nasıl yaparım? Azure 'a yük devredesin mi?

Şirket içi Hyper-V VM 'lerinden Azure 'a planlı veya planlanmamış bir yük devretme işlemi çalıştırabilirsiniz.
    - Planlı bir yük devretme çalıştırırsanız, veri kaybı olmaması için kaynak VM’ler kapatılır.
    - Birincil siteniz erişilebilir değilse, planlanmamış bir yük devretme gerçekleştirebilirsiniz.
    - Birden fazla makinenin yükünü yönetmek için tek bir makinenin yükünü devreder veya kurtarma planları oluşturabilirsiniz.
    - Yük devretme çalıştırırsınız. Yük devretme işlemi tamamlandıktan sonra, oluşturulan çoğaltma sanal makinelerini Azure 'da görmeniz gerekir. Gerekli olursa VM’ye genel bir IP adresi atayabilirsiniz. Daha sonra yük devretmeyi, çoğaltma Azure VM 'sinden iş yüküne erişmeye başlamak için yürütün.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Yük devretmeden sonra Azure VM 'lerine mi erişin Nasıl yaparım??
Yük devretmeden sonra, bir siteden siteye VPN veya Azure ExpressRoute üzerinden Azure VM 'lerine güvenli bir Internet bağlantısı üzerinden erişebilirsiniz. Bağlanmak için bir dizi şey hazırlamanız gerekir. [Daha fazla bilgi](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Veri devredildi.
Azure esneklik için tasarlanmıştır. Site Recovery, Azure SLA 'sına uygun olarak ikincil bir Azure veri merkezine yük devretme için tasarlanmıştır. Yük devretme gerçekleştiğinde, meta veri ve kasalarınızın kasanız için seçtiğiniz coğrafi bölge içinde kalmasını sağlarız.

### <a name="is-failover-automatic"></a>Yük devretme işlemi otomatik midir?
[Yük devretme](site-recovery-failover.md) otomatik değildir. Portalda tek tıklamayla yük devretme işlemini başlatır veya [PowerShell](/powershell/module/az.recoveryservices) 'i kullanarak yük devretmeyi tetikleyebilirsiniz.

### <a name="how-do-i-fail-back"></a>Nasıl yaparım? yeniden başarısız oldu mu?

Şirket içi altyapınız tekrar çalışır duruma geçtikten sonra yeniden çalıştırabilirsiniz. Yeniden çalışma üç aşamada gerçekleşir:

1. Azure 'dan şirket içi siteye yapılan planlı bir yük devretmeyi, birkaç farklı seçenek kullanarak başlatın:

    - Kapalı kalma süresini en aza indir: Bu Site Recovery seçeneği kullanırsanız, yük devretme işleminden önce verileri eşitler. Değiştirilen veri bloklarını denetler ve Azure VM çalışmaya devam ederken, kapalı kalma süresini en aza indirirken bunları şirket içi siteye indirir. Yük devretmenin tamamlanması gerektiğini el ile belirttiğinizde, Azure VM kapanır, son Delta değişikliği kopyalanır ve yük devretme başlatılır.
    - Tam indirme: Bu seçenek verileri, yük devretme sırasında eşitlenir. Bu seçenek tüm diski indirir. Bir sağlama toplamı hesaplanmadığından daha hızlıdır, ancak daha fazla kapalı kalma süresi vardır. Çoğaltma Azure VM 'lerini bir süre çalıştırıyorsanız veya şirket içi VM silinmişse bu seçeneği kullanın.

2. Aynı VM 'ye veya alternatif bir VM 'ye yeniden yük devredeseçebilirsiniz. Zaten mevcut değilse Site Recovery VM 'nin oluşturulmasını belirtebilirsiniz.
3. İlk eşitleme bittikten sonra, yük devretmeyi tamamlamayı seçersiniz. Tamamlandıktan sonra, her şeyin beklendiği gibi çalıştığını denetlemek için şirket içi VM 'de oturum açabilirsiniz. Azure portal, Azure VM 'lerinin durdurulmuş olduğunu görebilirsiniz.
4. Yük devretmeyi tamamlamak için yürütün ve şirket içi VM 'den iş yüküne yeniden erişmeye başlayın.
5. İş yükleri yeniden başlatıldıktan sonra, şirket içi VM 'Lerin tekrar Azure 'a çoğaltılması için çoğaltmayı tersine çevirmeyi etkinleştirmeniz gerekir.

### <a name="can-i-fail-back-to-a-different-location"></a>Farklı bir konuma yeniden hata verebilir miyim?
Evet, Azure 'a yük devretmek için, özgün bir konum yoksa, farklı bir konuma geri dönebilirsiniz. [Daha fazla bilgi edinin](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment).

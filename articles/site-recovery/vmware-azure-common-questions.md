---
title: Azure Site Recovery ile VMware olağanüstü durum kurtarma hakkında sık sorulan sorular
description: Azure Site Recovery kullanarak şirket içi VMware VM 'lerinin olağanüstü durum kurtarma hakkında sık sorulan soruların yanıtlarını alın.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 11/14/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 508fa7e33cd8572d70b7ebf261edba67fd40dd93
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084157"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>VMware 'den Azure 'a çoğaltma hakkında sık sorulan sorular

Bu makalede, şirket içi VMware sanal makineleri (VM 'Ler) ile Azure 'a olağanüstü durum kurtarma dağıtımı yaptığınızda karşılaşabileceğiniz yaygın sorular yanıtlanmaktadır.

## <a name="general"></a>Genel

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM olağanüstü durum kurtarma için ne yapmam gerekir?

VMware VM 'lerinin olağanüstü durum kurtarma ile ilgili [Bileşenler hakkında bilgi edinin](vmware-azure-architecture.md) .

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>VMware VM 'lerini Azure 'a geçirebilmeniz için Site Recovery kullanabilir miyim?

Evet. VMware VM 'Leri için tam olağanüstü durum kurtarma ayarlamak üzere Site Recovery kullanmanın yanı sıra, şirket içi VMware VM 'lerini Azure 'a geçirmek için de Site Recovery kullanabilirsiniz. Bu senaryoda, şirket içi VMware VM 'lerini Azure depolama 'ya çoğaltmanız gerekir. Ardından Şirket içinden Azure 'a yük devreolursunuz. Yük devretmeden sonra, uygulamalarınız ve iş yükleriniz Azure VM 'lerde kullanılabilir ve çalışır. İşlem, tam olağanüstü durum kurtarmayı ayarlamaya benzer, ancak bir geçişte Azure 'dan yeniden yük devredemezseniz.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Azure hesabımın VM oluşturmak için izinleri olması gerekiyor mu?

Abonelik yöneticisiyseniz, ihtiyacınız olan çoğaltma izinlerine sahip olursunuz. Yönetici değilseniz, bu eylemleri gerçekleştirmek için izinlerinizin olması gerekir:

- Site Recovery yapılandırırken belirttiğiniz kaynak grubunda ve sanal ağda bir Azure VM oluşturun.
- Yapılandırmanızı temel alarak seçili depolama hesabına veya yönetilen diske yazın.

Gerekli izinler hakkında [daha fazla bilgi edinin](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) .

### <a name="what-applications-can-i-replicate"></a>Hangi uygulamaları çoğaltabilirim?

[Çoğaltma gereksinimlerini](vmware-physical-azure-support-matrix.md#replicated-machines)karşılayan BIR VMware VM üzerinde çalışan tüm uygulamaları veya iş yüklerini çoğaltabilirsiniz.

- Site Recovery, uygulamaların yük devretmesini ve akıllı bir duruma geri dönmesi için uygulamayla uyumlu çoğaltmayı destekler.
- Site Recovery, SharePoint, Exchange, Dynamics, SQL Server ve Active Directory gibi Microsoft uygulamalarıyla tümleşir. Ayrıca, Oracle, SAP, IBM ve Red hat gibi önde gelen satıcılarla de birlikte da çalışmaktadır.

İş yükü koruması hakkında [daha fazla bilgi edinin](site-recovery-workload.md).

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Azure 'da Konuk işletim sistemi sunucu lisansı kullanabilir miyim?

Evet, Microsoft yazılım güvencesi müşterileri, Azure 'a geçirilen Windows Server makinelerinde lisanslama maliyetlerine kaydetmek veya olağanüstü durum kurtarma için Azure 'u kullanmak üzere [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) kullanabilir.

## <a name="security"></a>Güvenlik

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>VMware sunucularına hangi erişimi Site Recovery gerekir?

Site Recovery aşağıdakiler için VMware sunucularına erişmesi gerekir:

- Site Recovery yapılandırma sunucusunu çalıştıran bir VMware VM 'yi ayarlayın.
- Çoğaltma için VM 'Leri otomatik olarak keşfet.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>VMware VM 'lerine erişimi ne Site Recovery gerekir?

- Çoğaltmak için, bir VMware VM Site Recovery Mobility hizmetinin yüklü ve çalışıyor olması gerekir. Aracı el ile dağıtabilir veya bir sanal makine için çoğaltmayı etkinleştirdiğinizde hizmetin göndererek yüklemesini Site Recovery belirtebilirsiniz.
- Çoğaltma sırasında VM 'Ler Site Recovery ile aşağıdaki gibi iletişim kurar:
    - VM 'Ler, çoğaltma yönetimi için HTTPS bağlantı noktası 443 üzerindeki yapılandırma sunucusuyla iletişim kurar.
    - VM 'Ler, çoğaltma verilerini HTTPS bağlantı noktası 9443 üzerindeki işlem sunucusuna gönderir. (Bu ayar değiştirilebilir.)
    - Çoklu VM tutarlılığını etkinleştirirseniz VM 'Ler 20004 bağlantı noktası üzerinden birbirleriyle iletişim kurar.

### <a name="is-replication-data-sent-to-site-recovery"></a>Çoğaltma verileri Site Recovery gönderildi mi?

Hayır, Site Recovery çoğaltılan verileri engellemez ve sanal makinelerinizdeki neleri çalıştırdıklarınız hakkında bilgi içermez. VMware hiper yönetici ve Azure depolama arasında çoğaltma verileri alışverişi yapılır. Site Recovery bu verilere müdahale edemez. Yalnızca çoğaltma ve yük devretme işlemlerini düzenlemek için gereken meta veriler Site Recovery hizmetine gönderilir.  

Site Recovery ISO 27001:2013 ve 27018, HIPAA ve DPA için sertifikalıdır. Bu, SOC2 ve Fedrampa JAB değerlendirmelerinde yapılır.

## <a name="pricing"></a>Fiyatlandırma

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>VMware olağanüstü durum kurtarma için yaklaşık ücretleri hesaplamak Nasıl yaparım??

Site Recovery kullanırken maliyetleri tahmin etmek için [fiyatlandırma hesaplayıcısını](https://aka.ms/asr_pricing_calculator) kullanın.

Maliyetlerin ayrıntılı bir tahmini için, [VMware](https://aka.ms/siterecovery_deployment_planner) için dağıtım planlayıcısı aracını çalıştırın ve [maliyet tahmini raporunu](https://aka.ms/asr_DP_costreport)kullanın.

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Depolama alanı veya doğrudan yönetilen disklere çoğaltma arasında maliyet farkı var mı?

Yönetilen diskler, depolama hesaplarından biraz farklı şekilde ücretlendirilir. Yönetilen disk fiyatlandırması hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/managed-disks/) .

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Genel Amaçlı v2 depolama hesabına çoğaltılırken maliyette herhangi bir farklılık var mı?

Azure Site Recovery işlemler ağır olduğundan, genellikle GPv2 depolama hesaplarında tahakkuk eden işlem maliyetinde bir artış görürsünüz. Değişikliği tahmin etmek için [daha fazla bilgi edinin](../storage/common/storage-account-upgrade.md#pricing-and-billing) .

## <a name="mobility-service"></a>Mobility hizmeti

### <a name="where-can-i-find-the-mobility-service-installers"></a>Mobility hizmeti yükleyicilerini nereden bulabilirim?

Yükleyiciler yapılandırma sunucusundaki%ProgramData%\asr\home\svsystems\pushınstallsvc\repository klasöründedir.

## <a name="how-do-i-install-the-mobility-service"></a>Mobility hizmeti yüklensin mi Nasıl yaparım??

Çoğaltmak istediğiniz her VM 'de hizmeti birkaç yöntemden biriyle yükleyebilirsiniz:

- [Gönderim yükleme](vmware-physical-mobility-service-overview.md#push-installation)
- Kullanıcı arabiriminden veya PowerShell 'ten [el ile yükleme](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui)
- [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) gibi bir dağıtım aracı kullanarak dağıtım

## <a name="managed-disks"></a>Yönetilen diskler

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery verileri nerede çoğaltır?

Site Recovery, şirket içi VMware VM 'lerini ve fiziksel sunucuları Azure 'da yönetilen disklere çoğaltır.

- Site Recovery işlem sunucusu, çoğaltma günlüklerini hedef bölgedeki bir önbellek depolama hesabına yazar.
- Bu Günlükler, Azure tarafından yönetilen ve **asrseeddisk**öneki olan disklerde kurtarma noktaları oluşturmak için kullanılır.
- Yük devretme gerçekleştiğinde, seçtiğiniz kurtarma noktası yeni bir hedef yönetilen disk oluşturmak için kullanılır. Bu yönetilen disk, Azure 'da sanal makineye eklenir.
- Daha önce bir depolama hesabına (2019 Mart 'tan önce) çoğaltılan VM 'Ler etkilenmez.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Yeni makineleri depolama hesaplarına çoğaltabilir miyim?

Hayır. Azure portal Mart 2019 ' den başlayarak, yalnızca Azure yönetilen disklere çoğaltabilirsiniz.

Yeni VM 'lerin bir depolama hesabına çoğaltılması yalnızca PowerShell veya REST API (sürüm 2018-01-10 veya 2016-08-10) kullanılarak kullanılabilir.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Yönetilen disklere çoğaltılmasının avantajları nelerdir?

[Nasıl yapılacağını öğrenin](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery, yönetilen disklerle olağanüstü durum kurtarmayı basitleştirir.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Bir makine korunduktan sonra yönetilen disk türünü değiştirebilir miyim?

Evet, devam eden çoğaltmalar için [yönetilen disk türünü kolayca değiştirebilirsiniz](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) . Türü değiştirmeden önce, yönetilen diskte hiçbir paylaşılan erişim imzası URL 'SI üretilmediğinden emin olun:

1. Azure portal **yönetilen disk** kaynağına gidin ve **genel bakış** dikey penceresinde bir paylaşılan erişim imzası URL 'si başlığını içerip içermediğini kontrol edin.
1. Başlık mevcutsa, devam eden dışarı aktarma işlemini iptal etmek için bunu seçin.
1. Sonraki birkaç dakika içinde disk türünü değiştirin. Yönetilen disk türünü değiştirirseniz, yeni kurtarma noktalarının Azure Site Recovery tarafından oluşturulmasını bekleyin.
1. Gelecekte herhangi bir test yük devretmesi veya yük devretme için yeni kurtarma noktalarını kullanın.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Yönetilen disklerden yönetilmeyen disklere çoğaltma arasında geçiş yapabilir miyim?

Hayır. Yönetilmeyenden yönetilene geçiş desteklenmez.

## <a name="replication"></a>Çoğaltma

### <a name="what-are-the-replicated-vm-requirements"></a>Çoğaltılan VM gereksinimleri nelerdir?

VMware VM 'Leri ve fiziksel sunucular için destek gereksinimleri hakkında [daha fazla bilgi edinin](vmware-physical-azure-support-matrix.md#replicated-machines) .

### <a name="how-often-can-i-replicate-to-azure"></a>Azure 'a ne sıklıkta çoğaltma yapabilirim?

VMware VM 'lerini Azure 'a çoğaltırken çoğaltma işlemi sürekli olur.

### <a name="can-i-extend-replication"></a>Çoğaltmayı genişletebilir miyim?

Genişletilmiş veya zincir çoğaltma desteklenmez. Bu özelliği [geri bildirim forumuna](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)iste.

### <a name="can-i-do-an-offline-initial-replication"></a>Çevrimdışı bir ilk çoğaltma yapabilir miyim?

Çevrimdışı çoğaltma desteklenmez. Bu özelliği [geri bildirim forumuna](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)iste.

### <a name="what-is-asrseeddisk"></a>Asrseeddisk nedir?

Her kaynak disk için, veriler Azure 'da yönetilen bir diske çoğaltılır. Bu disk, **asrseeddisk**ön ekine sahiptir. Kaynak diskin kopyasını ve tüm kurtarma noktası anlık görüntülerini depolar.

### <a name="can-i-exclude-disks-from-replication"></a>Diskleri çoğaltmadan hariç tutabilir miyim?

Evet, diskleri dışarıda bırakabilirsiniz.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Dinamik disklere sahip olan VM 'Leri çoğaltabilir miyim?

Dinamik diskler çoğaltılabiliyor. İşletim sistemi diski, temel bir disk olmalıdır.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Çoklu VM tutarlılığı için çoğaltma grupları 'nı kullanırsam, var olan bir çoğaltma grubuna yeni bir VM ekleyebilir miyim?

Evet, çoğaltma için çoğaltmayı etkinleştirdiğinizde var olan bir çoğaltma grubuna yeni VM 'Ler ekleyebilirsiniz. Ancak

- Çoğaltma başladıktan sonra var olan bir çoğaltma grubuna VM ekleyemezsiniz.
- Mevcut VM 'Ler için çoğaltma grubu oluşturamazsınız.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Diskler ekleyerek veya yeniden boyutlandırarak çoğaltılan VM 'Leri değiştirebilir miyim?

Azure 'a VMware çoğaltması için disk boyutunu değiştirebilirsiniz. Yeni diskler eklemek istiyorsanız, diski eklemeniz ve sanal makine için korumayı yeniden etkinleştirmeniz gerekir.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Şirket içi makineleri, devam eden Çoğaltmayı etkilemeden yeni bir vCenter Server geçirebilir miyim?

Hayır. VMware vCenter veya geçişi değişikliği, devam eden çoğaltmayı etkiler. Yeni vCenter Server Site Recovery ayarlayın ve makineler için çoğaltmayı yeniden etkinleştirin.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Üzerinde yapılandırılmış bir sanal ağa (Azure Güvenlik duvarları olan) sahip bir önbellek veya hedef depolama hesabına çoğaltma yapabilir miyim?

Hayır, Site Recovery sanal ağlardaki Azure depolama 'ya çoğaltmayı desteklemez.

## <a name="component-upgrade"></a>Bileşen yükseltme

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Mobility Hizmetleri Aracısı veya yapılandırma sunucusu sürümm eski ve yükseltme başarısız oldu. Ne yapmalıyım?

Site Recovery, N-4 destek modelini izler. Çok eski sürümlerden yükseltme hakkında [daha fazla bilgi edinin](https://aka.ms/asr_support_statement) .

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Azure Site Recovery için sürüm notlarını ve güncelleştirme paketlerini nerede bulabilirim?

[Yeni güncelleştirmeler hakkında bilgi edinin](site-recovery-whats-new.md)ve [toplama bilgilerini alın](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Azure 'a olağanüstü durum kurtarma için yükseltme bilgilerini nerede bulabilirim?

[Yükseltme hakkında bilgi edinin](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Her yükseltme için kaynak makineleri yeniden başlatmem gerekir mi?

Yeniden başlatma önerilir, ancak her yükseltme için zorunlu değildir. [Daha fazla bilgi edinin](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Yapılandırma Sunucusu

### <a name="what-does-the-configuration-server-do"></a>Yapılandırma sunucusu ne yapar?

Yapılandırma sunucusu, aşağıdakiler dahil olmak üzere şirket içi Site Recovery bileşenleri çalıştırır:

- Yapılandırma sunucusu. Sunucu, şirket içi bileşenler ile Azure arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir.
- Çoğaltma ağ geçidi görevi gören işlem sunucusu. Bu sunucu:
    1. Çoğaltma verilerini alır.
    2. Önbelleğe alma, sıkıştırma ve şifreleme ile verileri iyileştirir.
    3. Verileri Azure depolama alanına gönderir.
  İşlem sunucusu Ayrıca VM 'lerde Mobility hizmetinin göndererek yüklemesini yapar ve şirket içi VMware VM 'lerinin otomatik olarak bulunmasını gerçekleştirir.
- Azure 'dan yeniden çalışma sırasında çoğaltma verilerini işleyen ana hedef sunucu.

Yapılandırma sunucusu bileşenleri ve süreçler hakkında [daha fazla bilgi edinin](vmware-azure-architecture.md) .

### <a name="where-do-i-set-up-the-configuration-server"></a>Yapılandırma sunucusunu nereden ayarlayabilirim?

Yapılandırma sunucusu için tek, yüksek oranda kullanılabilir, şirket içi bir VMware VM 'sine ihtiyacınız vardır. Fiziksel sunucu olağanüstü durum kurtarma için, yapılandırma sunucusunu bir fiziksel makineye yükler.

### <a name="what-do-i-need-for-the-configuration-server"></a>Yapılandırma sunucusu için ne yapmam gerekir?

[Önkoşulları](vmware-azure-deploy-configuration-server.md#prerequisites)gözden geçirin.

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Bir şablon kullanmak yerine yapılandırma sunucusunu el ile ayarlayabilir miyim?

[Yapılandırma sunucusu VM](vmware-azure-deploy-configuration-server.md) 'sini Open Virtualization Format (ovf) şablonunun en son sürümünü kullanarak oluşturmanız önerilir. Şablonu kullanmıyorsanız (örneğin, VMware sunucusuna erişiminiz yoksa), kurulum dosyasını portaldan [indirin](physical-azure-set-up-source.md) ve yapılandırma sunucusunu ayarlayın.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Bir yapılandırma sunucusu birden fazla bölgeye çoğaltılabilir mi?

Hayır. Birden fazla bölgeye çoğaltmak için, her bölgede bir yapılandırma sunucusuna ihtiyacınız vardır.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Azure 'da bir yapılandırma sunucusunu barındırabilirim miyim?

Mümkün olsa da, yapılandırma sunucusunu çalıştıran Azure VM 'nin şirket içi VMware altyapınız ve VM 'leriyle iletişim kurması gerekir. Bu iletişim gecikme süresi ekler ve devam eden çoğaltmayı etkiler.

### <a name="how-do-i-update-the-configuration-server"></a>Yapılandırma sunucusu Nasıl yaparım? güncelleştirilsin mi?

Yapılandırma sunucusunu güncelleştirmeyi [öğrenin](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) .

- En son güncelleştirme bilgilerini [Azure Updates sayfasında](https://azure.microsoft.com/updates/?product=site-recovery)bulabilirsiniz.
- Portaldan en son sürümü indirebilirsiniz. Ya da, yapılandırma sunucusunun en son sürümünü doğrudan [Microsoft Yükleme Merkezi](https://aka.ms/asrconfigurationserver)' nden indirebilirsiniz.
- Sürümünüz geçerli sürümden daha eski dört sürümden eskiyse, Yükseltme Kılavuzu için [destek bildirimine](https://aka.ms/asr_support_statement) bakın.

### <a name="should-i-back-up-the-configuration-server"></a>Yapılandırma sunucusunu yedeklemem gerekir mi?

Yapılandırma sunucusu için düzenli olarak zamanlanmış yedeklemeler yapmanızı öneririz.

- Başarılı bir yeniden çalışma için, geri yüklenmekte olan VM 'nin yapılandırma sunucusu veritabanında mevcut olması gerekir.
- Yapılandırma sunucusunun çalışıyor olması ve bağlı durumda olması gerekir.
- Ortak yapılandırma sunucusu yönetim görevleri hakkında [daha fazla bilgi edinin](vmware-azure-manage-configuration-server.md) .

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Yapılandırma sunucusunu ayarlıyorum, MySQL 'i el ile indirebilir ve yükleyebilir miyim?

Evet. MySQL indirin ve C:\Temp\ASRSetup klasörüne yerleştirin. Ardından, el ile yükleyebilirsiniz. Yapılandırma sunucusu VM 'sini ayarlayıp koşulları kabul ettiğinizde MySQL, **indirme ve yükleme**sırasında **zaten yüklü** olarak listelenecektir.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>MySQL 'in indirimü, ancak Site Recovery yüklemesine izin verebilir miyim?

Evet. MySQL yükleyicisini indirin ve C:\Temp\ASRSetup klasörüne yerleştirin. Yapılandırma sunucusu VM 'sini ayarlarken, koşulları kabul edin ve **indir ve yükle**' yi seçin. Portal, MySQL 'i yüklemek için eklediğiniz yükleyiciyi kullanacaktır.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Yapılandırma sunucusu VM 'sini başka bir şey için kullanabilir miyim?

Hayır. VM 'yi yalnızca yapılandırma sunucusu için kullanın.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Bir yapılandırma sunucusunu kopyalayabilir ve düzenleme için kullanabilir miyim?

Hayır. Kayıt sorunlarından kaçınmak için yeni bir yapılandırma sunucusu ayarlayın.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Yapılandırma sunucusunun kayıtlı olduğu kasayı değiştirebilir miyim?

Hayır. Bir kasa yapılandırma sunucusuyla ilişkilendirildikten sonra değiştirilemez. Yapılandırma sunucusunu farklı bir kasaya kaydetme hakkında [bilgi edinin](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) .

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Aynı yapılandırma sunucusunu hem VMware VM 'Leri hem de fiziksel sunucuları olağanüstü durum kurtarma için kullanabilir miyim?

Evet, ancak fiziksel makinenin yalnızca bir VMware sanal makinesine geri dönebilir olabileceğini unutmayın.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Yapılandırma sunucusu için parolayı nereden indirebilirim?

Parolayı indirmeyi [öğrenin](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) .

### <a name="where-can-i-download-vault-registration-keys"></a>Kasa kayıt anahtarlarını nereden indirebilirim?

Kurtarma Hizmetleri kasasında, **Site Recovery altyapı** > **Yönet**' de **yapılandırma sunucuları** ' nı seçin. Ardından, **sunucular**' da, kasa kimlik bilgileri dosyasını indirmek için **kayıt anahtarını indir** ' i seçin.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Birden çok vCenter örneğini korumak için tek bir yapılandırma sunucusu kullanılabilir mi?

Evet, tek bir yapılandırma sunucusu, VM 'Leri birden çok sanal merkezde koruyabilir.  Yapılandırma sunucusuna kaç tane vCenter örneği eklenebileceği, ancak tek bir yapılandırma sunucusunun kaç sanal makine tarafından korunabileceğine ilişkin limitler için sınır yoktur.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Tek bir yapılandırma sunucusu vCenter içindeki birden çok kümeyi koruyabilir mi?

Evet, Azure Site Recovery VM 'Leri farklı kümeler genelinde koruyabilir.

## <a name="process-server"></a>İşlem sunucusu

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Çoğaltmayı etkinleştirdiğimde neden işlem sunucusunu seçmem gerekiyor?

9,24 ve sonraki sürümlerde bulunan güncelleştirmeler artık [çoğaltmayı etkinleştirdiğinizde işlem sunucusunun sistem durumunu](vmware-azure-enable-replication.md#enable-replication)görüntüler. Bu özellik işlem sunucusu azaltmaktan kaçınmanıza ve sağlıksız işlem sunucularının kullanımını en aza indirmenize yardımcı olur.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Doğru sağlık bilgileri için işlem sunucusunu sürüm 9,24 veya sonraki bir sürüme güncelleştirmek Nasıl yaparım??

[Sürüm 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups)' den başlayarak, işlem sunucusunun sistem durumunu göstermek için daha fazla uyarı eklenmiştir. Tüm uyarıların oluşturulması [için Site Recovery bileşenlerinizi sürüm 9,24 veya sonraki bir sürüme güncelleştirin](service-updates-how-to.md#links-to-currently-supported-update-rollups) .

## <a name="failover-and-failback"></a>Yük devretme ve yeniden çalışma

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Şirket içi işlem sunucusunu yeniden çalışma için kullanabilir miyim?

Veri aktarımı gecikmelerini önlemek için Azure 'da yeniden çalışma amacıyla bir işlem sunucusu oluşturmanız önemle önerilir. Ayrıca, kaynak VM 'Leri ağı yapılandırma sunucusundaki Azure ile bağlantılı ağ ile ayırarak, yeniden çalışma için Azure 'da oluşturulan işlem sunucusunu kullanmak gereklidir.

### <a name="can-i-keep-the-ip-address-on-failover"></a>IP adresini yük devretmeye devam edebilir miyim?

Evet, IP adresini yük devretmeye devam edebilirsiniz. Yük devretmeden önce VM 'nin **işlem ve ağ** AYARLARıNDA hedef IP adresini belirttiğinizden emin olun. Ayrıca, yeniden çalışma sırasında IP adresi çakışmalarını önlemek için, yük devretme sırasında makineleri kapatın.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Yük devretmeden önce hedef VM boyutunu veya VM türünü değiştirebilir miyim?

Evet, yük devretmeden önce VM 'nin türünü veya boyutunu dilediğiniz zaman değiştirebilirsiniz. Portalda, çoğaltılan VM için **işlem ve ağ** ayarlarını kullanın.

### <a name="how-far-back-can-i-recover"></a>Ne kadar geri kurtarabilirim?

VMware 'den Azure 'a kadar, kullanabileceğiniz en eski kurtarma noktası 72 saattir.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Yük devretmeden sonra Azure VM 'lerine mi erişin Nasıl yaparım??

Yük devretmeden sonra, bir siteden siteye VPN veya Azure ExpressRoute üzerinden Azure VM 'lerine güvenli bir internet bağlantısı üzerinden erişebilirsiniz. Bağlanmak için birkaç şeyi hazırlamanız gerekir. [Daha fazla bilgi edinin](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Yük devredildi-veriler esnektir mi?

Azure esneklik için tasarlanmıştır. Site Recovery, Azure hizmet düzeyi sözleşmesi 'nin (SLA) gerektirdiği şekilde ikincil bir Azure veri merkezine yük devretme için tasarlanmıştır. Yük devretme gerçekleştiğinde, meta veri ve kasalarınızın kasanız için seçtiğiniz coğrafi bölgede kalmasını sağlarız.

### <a name="is-failover-automatic"></a>Yük devretme işlemi otomatik midir?

[Yük devretme](site-recovery-failover.md) otomatik değildir. Portalda tek bir seçim yaparak yük devretme başlatın veya [PowerShell](/powershell/module/az.recoveryservices) 'i kullanarak yük devretmeyi tetikleyebilirsiniz.

### <a name="can-i-fail-back-to-a-different-location"></a>Farklı bir konuma yeniden hata verebilir miyim?

Evet. Azure 'a yük devretmek için, özgün bir konum yoksa, farklı bir konuma geri dönebilirsiniz. [Daha fazla bilgi edinin](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Neden yeniden yük devretmek için özel eşleme ile bir VPN veya ExpressRoute gerekir mi?

Azure 'dan geri döndüğünüzde, Azure 'daki veriler şirket içi sanal makinenize geri kopyalanır ve özel erişim gerekir.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Yük devretmeden sonra Azure VM 'yi yeniden boyutlandırabilir miyim?

Hayır, yük devretmeden sonra hedef VM 'nin boyutunu veya türünü değiştiremezsiniz.

## <a name="automation-and-scripting"></a>Otomasyon ve betik oluşturma

### <a name="can-i-set-up-replication-with-scripting"></a>Komut dosyasıyla çoğaltmayı ayarlayabilir miyim?

Evet. REST API, PowerShell veya Azure SDK kullanarak Site Recovery iş akışlarını otomatikleştirebilirsiniz. [Daha fazla bilgi edinin](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Performans ve kapasite

### <a name="can-i-throttle-replication-bandwidth"></a>Çoğaltma bant genişliğini kısıtlama yapabilir miyim?

Evet. [Daha fazla bilgi edinin](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Sonraki adımlar

- Destek gereksinimlerini [gözden geçirin](vmware-physical-azure-support-matrix.md) .
- [Ayarla](vmware-azure-tutorial.md) VMware 'den Azure 'a çoğaltma.

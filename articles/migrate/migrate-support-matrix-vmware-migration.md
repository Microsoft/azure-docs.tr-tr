---
title: Azure geçişi 'nde VMware geçişi desteği
description: Azure geçişi 'nde VMware VM geçişi desteği hakkında bilgi edinin.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 4fb2ea534954ae6c64d0da2d992ce8b1c8a62c0c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557573"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware geçişi için destek matrisi

Bu makalede, Azure geçişi ile VMware VM 'Leri geçirmeye yönelik destek ayarları ve sınırlamaları özetlenmektedir [: sunucu geçişi](migrate-services-overview.md#azure-migrate-server-migration-tool) . Azure 'a geçiş için VMware VM 'lerini değerlendirme hakkında bilgi arıyorsanız, [değerlendirme desteği matrisini](migrate-support-matrix-vmware.md)gözden geçirin.


## <a name="migration-options"></a>Geçiş seçenekleri

VMware VM 'lerini birkaç yolla geçirebilirsiniz:

- **Aracısız geçiş kullanma**: VM 'leri bunlara hiçbir şey yüklemeye gerek kalmadan geçirin. Aracısız geçiş için [Azure geçişi](migrate-appliance.md) gereci dağıtırsınız.
- **Aracı tabanlı geçişi kullanma**: VM 'ye çoğaltma için bir aracı yükler. Aracı tabanlı geçiş için bir [çoğaltma](migrate-replication-appliance.md)gereci dağıtırsınız.

Kullanmak istediğiniz yöntemi öğrenmek için [Bu makaleyi](server-migrate-overview.md) gözden geçirin.

## <a name="agentless-migration"></a>Aracısız geçiş 

Bu bölümde Azure 'a aracısız VMware VM geçişi için gereksinimler özetlenmektedir.

### <a name="vmware-requirements-agentless"></a>VMware gereksinimleri (aracısız)

Tablo, VMware hiper yönetici gereksinimlerini özetler.

**VMware** | **Ayrıntılar**
--- | ---
**VMware vCenter Server** | Sürüm 5,5, 6,0, 6,5, 6,7, 7,0.
**VMware vSphere ESXI Konağı** | Sürüm 5,5, 6,0, 6,5, 6,7, 7,0.
**vCenter Server izinleri** | Aracısız geçiş, [geçiş](migrate-appliance.md)gereci kullanır. Gereç vCenter Server içinde bu izinlere ihtiyaç duyuyor:<br/><br/> - **DataStore. gözatma** (veri deposu-> veri deposuna gözatma): anlık görüntü oluşturma ve silme sorunlarını GIDERMEK için VM günlük dosyalarına göz atmaya izin verin.<br/><br/> - **DataStore. FileManagement** (datastore-> düşük düzey dosya işlemleri): anlık görüntü oluşturma ve silme sorunlarını gidermek için veri deposu tarayıcısında okuma/yazma/silme/yeniden adlandırma Işlemlerine izin verir.<br/><br/> - **VirtualMachine.Config. ChangeTracking** (sanal makine-> disk değişikliği izleme): VM disklerinin değişiklik izlemesini etkinleştirme veya devre dışı bırakma olanağı, anlık görüntüler arasında değişen veri bloklarını çekme.<br/><br/> - **VirtualMachine.Config. DiskLease** (sanal makine-> disk kirası): VMware vSphere sanal disk geliştirme seti 'ni (VDDK) kullanarak diski okumak için BIR VM 'ye yönelik disk Kiralama Işlemlerine Izin verin.<br/><br/> - **VirtualMachine. sağlama. DiskRandomRead** (sanal makine-> sağlama-> salt okuma özellikli disk erişimine izin ver): VDDK kullanarak diski okumak IÇIN bir VM 'de disk açmaya izin verin.<br/><br/> - **VirtualMachine. sağlama. DiskRandomAccess** (sanal makine-> sağlama-> disk erişimine izin ver): VDDK kullanarak diski okumak IÇIN bir VM 'de disk açmaya izin verin.<br/><br/> - **VirtualMachine. sağlama. GetVmFiles** (sanal makine-> sağlama-> sanal makine Indirmeye izin ver): bir VM ile ilişkili dosyalardaki okuma Işlemlerine izin verir, bu durumda günlükleri indirebilir ve hata oluşursa sorun giderin.<br/><br/> - **VirtualMachine. State. \* *_ (Sanal makine-> anlık görüntü yönetimi): çoğaltma IÇIN VM anlık görüntülerinin oluşturulmasına ve yönetimine Izin verin. <br/> <br/> -_* VirtualMachine. ınterbir. poweroff** (sanal makine-> etkileşimi-> gücü kapalı): Azure 'a GEÇIŞ sırasında VM 'Nin kapatılmasını sağlar.



### <a name="vm-requirements-agentless"></a>VM gereksinimleri (aracısız)

Tablo, VMware VM 'Leri için aracısız geçiş gereksinimlerini özetler.

**Destek** | **Ayrıntılar**
--- | ---
**Desteklenen işletim sistemleri** | Azure tarafından desteklenen [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) ve [Linux](../virtual-machines/linux/endorsed-distros.md) işletim sistemlerini geçirebilirsiniz.
**Azure 'da Windows VM 'Leri** | Geçişten önce VM 'lerde [bazı değişiklikler yapmanız](prepare-for-migration.md#verify-required-changes-before-migrating) gerekebilir. 
**Azure 'da Linux VM 'Leri** | Bazı VM 'Ler, Azure 'da çalışabilecek şekilde değişiklik gerektirebilir.<br/><br/> Linux için Azure geçişi, değişiklikleri bu işletim sistemleri için otomatik olarak yapar:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Sent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Detem 7, 8, 9 <br/> Oracle Linux 7,7, 7,7-CI<br/> Diğer işletim sistemleri için [gerekli değişiklikleri](prepare-for-migration.md#verify-required-changes-before-migrating) el ile yaparsınız.
**Linux önyüklemesi** | /Boot ayrılmış bir bölümse, işletim sistemi diskinde bulunmalı ve birden çok diske yayılmamalıdır.<br/> /Boot kök (/) bölümünün parçasıysa, '/' bölümünün işletim sistemi diskinde olması ve diğer disklere yayılmamamakta olması gerekir.
**UEFı önyüklemesi** | Destekleniyor. UEFı tabanlı VM 'Ler, Azure 2. nesil VM 'lerine geçirilir. 
**Disk boyutu** | 2 TB işletim sistemi diski;  veri diskleri için 32 TB.
**Disk sınırları** |  VM başına en fazla 60 disk.
**Şifrelenmiş diskler/birimler** | Şifrelenmiş disklere/birimlere sahip VM 'Ler geçiş için desteklenmez.
**Paylaşılan disk kümesi** | Desteklenmez.
**Bağımsız diskler** | Desteklenmez.
**RDM/geçiş diskleri** | VM 'Lerin RDM veya PASSTHROUGH diskleri varsa, bu diskler Azure 'a çoğaltılmaz.
**NFS** | VM 'Lere birim olarak bağlanmış NFS birimleri çoğaltılmaz.
**Iscsı hedefleri** | Iscsı hedefleri olan VM 'Ler aracısız geçiş için desteklenmez.
**Çok yollu GÇ** | Desteklenmez.
**Depolama vMotion** | Desteklenmez. VM, Depolama vMotion kullanıyorsa çoğaltma çalışmayacaktır.
**Ekip oluşturulmuş NIC 'ler** | Desteklenmez.
**IPv6** | Desteklenmez.
**Hedef disk** | VM 'Ler, yalnızca Azure 'da yönetilen disklere (Standart HDD, standart SSD, Premium SSD) geçirilebilir.
**Eşzamanlı çoğaltma** | 1 gereç ile vCenter Server başına VM 'Leri aynı anda çoğaltma. 300 Ek bir [genişleme](./how-to-scale-out-for-migration.md) gereci dağıtıldığında, vCenter Server başına VM 'leri aynı anda çoğaltmaya 500. 
**Azure VM Aracısı 'nın otomatik yüklemesi (Windows ve Linux Aracısı)** | Windows Server 2008 R2 sürümleri için desteklenir. <br/> RHEL6, RHEL7, CentOS7, Ubuntu 14,04, Ubuntu 16,04, Ubuntu 18.04 için desteklenir. Bu Linux işletim sistemleri için [gerekli paketlerin](../virtual-machines/extensions/agent-linux.md#requirements)listesini gözden geçirin.

> [!TIP]
>  Azure portal kullanarak, çoğaltmayı yapılandırmak için bir seferde en fazla 10 VM seçebilirsiniz. Daha fazla VM çoğaltmak için portalı kullanabilir ve birden çok 10 VM 'de çoğaltılacak VM 'Leri ekleyebilir ya da çoğaltmayı yapılandırmak için Azure geçişi PowerShell arabirimini kullanabilirsiniz. Eşzamanlı çoğaltmalar için desteklenen en fazla VM sayısından daha fazla eşzamanlı çoğaltma yapılandırmatığınızdan emin olun.

### <a name="appliance-requirements-agentless"></a>Gereç gereksinimleri (aracısız)

Aracısız geçiş, [Azure geçişi](migrate-appliance.md)gereci kullanır. Gereci bir OVA şablonu kullanarak bir VMware VM 'si olarak dağıtabilir, vCenter Server içeri aktarılabilir veya bir [PowerShell betiği](deploy-appliance-script.md)kullanabilirsiniz.

- VMware için [gereç gereksinimleri](migrate-appliance.md#appliance---vmware) hakkında bilgi edinin.
- Gereçlerin [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu](migrate-appliance.md#government-cloud-urls) bulutlarında erişmesi gereken URL 'ler hakkında bilgi edinin.
- Azure Kamu 'da, [betiği kullanarak](deploy-appliance-script-government.md)gereci dağıtmanız gerekir.

### <a name="port-requirements-agentless"></a>Bağlantı noktası gereksinimleri (aracısız)

**Cihaz** | **Bağlantı**
--- | ---
Elektrikli | 443 numaralı bağlantı noktası, çoğaltılan verileri Azure 'a yüklemek ve Azure geçiş Hizmetleri ile iletişim kurmak için çoğaltma ve geçiş işlemlerini düzenleyen giden bağlantılar.
vCenter server | Bağlantı noktası 443 ' deki gelen bağlantılar, gerecin çoğaltma düzenlemesine olanak tanımak için anlık görüntü oluşturma, veri kopyalama, anlık görüntüleri yayınlama
vSphere/ESXI Konağı | Gereç için TCP bağlantı noktası 902 ' den gelen, verileri anlık görüntülerden çoğaltma.

## <a name="agent-based-migration"></a>Aracı tabanlı geçiş 


Bu bölüm, aracı tabanlı geçişe yönelik gereksinimleri özetler.


### <a name="vmware-requirements-agent-based"></a>VMware gereksinimleri (aracı tabanlı)

Bu tablo, VMware sanallaştırma sunucuları için değerlendirme desteğini ve sınırlamalarını özetler.

**VMware gereksinimleri** | **Ayrıntılar**
--- | ---
**VMware vCenter Server** | Sürüm 5,5, 6,0, 6,5 veya 6,7.
**VMware vSphere ESXI Konağı** | Sürüm 5,5, 6,0, 6,5 veya 6,7.
**vCenter Server izinleri** | VCenter Server için salt tanımlı bir hesap.

### <a name="vm-requirements-agent-based"></a>VM gereksinimleri (aracı tabanlı)

Tablo, aracı tabanlı geçiş kullanarak geçirmek istediğiniz VMware VM 'Leri için VMware VM desteğini özetler.

**Destek** | **Ayrıntılar**
--- | ---
**Makine iş yükü** | Azure geçişi, desteklenen bir makinede çalışan herhangi bir iş yükünün (Active Directory, SQL Server vb.) geçirilmesini destekler.
**İşletim sistemleri** | En son bilgiler için Site Recovery [işletim sistemi desteğini](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) gözden geçirin. Azure geçişi, özdeş VM işletim sistemi desteği sağlar.
**Linux dosya sistemi/Konuk depolama** | En son bilgiler için Site Recovery [Linux dosya sistemi desteğini](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) gözden geçirin. Azure geçişi 'nin aynı Linux dosya sistemi desteği vardır.
**Ağ/depolama** | En son bilgiler için, Site Recovery için [ağ](../site-recovery/vmware-physical-azure-support-matrix.md#network) ve [depolama](../site-recovery/vmware-physical-azure-support-matrix.md#storage) önkoşullarını gözden geçirin. Azure geçişi, aynı ağ/depolama gereksinimlerini sağlar.
**Azure gereksinimleri** | En son bilgiler için Site Recovery [Azure ağ](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [depolama](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)ve [işlem](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) gereksinimlerini gözden geçirin. Azure geçişi, VMware geçişi için aynı gereksinimlere sahiptir.
**Mobility hizmeti** | Taşımak istediğiniz her VM 'ye Mobility hizmeti aracısının yüklenmesi gerekir.
**UEFı önyüklemesi** | Destekleniyor. UEFı tabanlı VM 'Ler, Azure 2. nesil VM 'lerine geçirilir. 
**UEFı-güvenli önyükleme**         | Geçiş için desteklenmez.
**Hedef disk** | VM 'Ler, yalnızca Azure 'da yönetilen disklere (Standart HDD, standart SSD, Premium SSD) geçirilebilir.
**Disk boyutu** | 2 TB işletim sistemi diski; veri diskleri için 32 TB.
**Disk sınırları** |  VM başına en fazla 63 disk.
**Şifrelenmiş diskler/birimler** | Şifrelenmiş disklere/birimlere sahip VM 'Ler geçiş için desteklenmez.
**Paylaşılan disk kümesi** | Desteklenmez.
**Bağımsız diskler** | Destekleniyor.
**Geçiş diskleri** | Destekleniyor.
**NFS** | VM 'Lere birim olarak bağlanmış NFS birimleri çoğaltılmaz.
**Iscsı hedefleri** | Destekleniyor.
**Çok yollu GÇ** | Desteklenmez.
**Depolama vMotion** | Desteklenir
**Ekip oluşturulmuş NIC 'ler** | Desteklenmez.
**IPv6** | Desteklenmez.




### <a name="appliance-requirements-agent-based"></a>Gereç gereksinimleri (aracı tabanlı)

Azure geçişi hub 'ında belirtilen OVA şablonunu kullanarak çoğaltma gerecini ayarladığınızda, Gereç Windows Server 2016 ' i çalıştırır ve destek gereksinimlerine uyar. Çoğaltma gerecini fiziksel bir sunucuda el ile ayarlarsanız, gereksinimlerle uyumlu olduğundan emin olun.

- VMware için [çoğaltma gereç gereksinimleri](migrate-replication-appliance.md#appliance-requirements) hakkında bilgi edinin.
- MySQL 'in gereç üzerinde yüklü olması gerekir. [Yükleme seçenekleri](migrate-replication-appliance.md#mysql-installation)hakkında bilgi edinin.
- Çoğaltma gerecinin [ortak](migrate-replication-appliance.md#url-access) ve [kamu](migrate-replication-appliance.md#azure-government-url-access) bulutlarında erişmesi gereken URL 'ler hakkında bilgi edinin.
- Çoğaltma gerecinin erişmesi için gereken [bağlantı noktalarını](migrate-replication-appliance.md#port-access) gözden geçirin.

### <a name="port-requirements-agent-based"></a>Bağlantı noktası gereksinimleri (aracı tabanlı)

**Cihaz** | **Bağlantı**
--- | ---
VM'ler | VM 'lerde çalışan Mobility hizmeti, çoğaltma yönetimi için HTTPS 443 gelen bağlantı noktasında şirket içi çoğaltma gereci (yapılandırma sunucusu) ile iletişim kurar.<br/><br/> VM 'Ler, çoğaltma verilerini işlem sunucusuna (yapılandırma sunucusu makinesinde çalışan), HTTPS 9443 gelen bağlantı noktası üzerinden gönderir. Bu bağlantı noktası değiştirilebilir.
Çoğaltma gereci | Çoğaltma gereci, HTTPS 443 giden bağlantı noktası üzerinden Azure ile çoğaltmayı düzenler.
İşlem sunucusu | İşlem sunucusu çoğaltma verilerini alır, bu verileri iyileştirir ve şifreler ve 443 giden bağlantı noktası üzerinden Azure depolama 'ya gönderir.<br/> Varsayılan olarak, işlem sunucusu çoğaltma gereci üzerinde çalışır.

## <a name="azure-vm-requirements"></a>Azure VM gereksinimleri

Azure 'a çoğaltılan tüm şirket içi VM 'Ler (aracısız veya aracı tabanlı geçişle birlikte), bu tabloda özetlenen Azure VM gereksinimlerini karşılamalıdır. 

**Bileşen** | **Gereksinimler** 
--- | --- | ---
Konuk işletim sistemi | Geçiş için desteklenen VMware VM işletim sistemlerini doğrular.<br/> Desteklenen bir işletim sistemi üzerinde çalışan herhangi bir iş yükünü geçirebilirsiniz. 
Konuk işletim sistemi mimarisi | 64 bit. 
İşletim sistemi disk boyutu | 2.048 GB 'a kadar. 
İşletim sistemi disk sayısı | 1 
Veri diski sayısı | 64 veya daha az. 
Veri diski boyutu | 32 TB 'a kadar
Ağ bağdaştırıcıları | Birden çok bağdaştırıcı desteklenir.
Paylaşılan VHD | Desteklenmez. 
FC diski | Desteklenmez. 
BitLocker | Desteklenmez.<br/><br/> Makineyi geçirmeden önce BitLocker devre dışı bırakılmalıdır.
VM adı | 1 ile 63 karakter arasında.<br/><br/> Harfler, sayılar ve kısa çizgilerden oluşabilir.<br/><br/> Makine adı bir harf veya sayıyla başlamalı ve bitmelidir. 
Geçişten sonra Bağlan-Windows | Geçişten sonra Windows çalıştıran Azure VM 'lerine bağlanmak için:<br/><br/> -Geçişten önce, şirket içi VM 'de RDP 'yi etkinleştirin.<br/><br/> TCP ve UDP kurallarının **Ortak** profil için eklendiğinden ve tüm profillerde **Windows Güvenlik Duvarı** > **İzin Verilen Uygulamalar** içinde RDP’ye izin verildiğinden emin olun.<br/><br/> Siteden siteye VPN erişimi için, RDP 'yi etkinleştirin ve   ->  **etki alanı ve özel** ağlar için Windows Güvenlik Duvarı **izin verilen uygulamalar ve Özellikler** ' de RDP 'ye izin verin.<br/><br/> Ayrıca, işletim sisteminin SAN ilkesinin **OnlineAll** olarak ayarlandığından emin olun. [Daha fazla bilgi edinin](prepare-for-migration.md).
Geçişten sonra Bağlan-Linux | SSH kullanarak geçişten sonra Azure VM 'lerine bağlanmak için:<br/><br/> Geçişten önce, şirket içi makinede, Secure Shell hizmetinin başlangıç olarak ayarlandığını ve Güvenlik Duvarı kurallarının bir SSH bağlantısına izin vermeyi kontrol edin.<br/><br/> Yük devretmenin ardından Azure VM 'de, yük devredilen VM 'deki ağ güvenlik grubu kuralları için SSH bağlantı noktasına gelen bağlantılara ve bağlı olduğu Azure alt ağına izin verin.<br/><br/> Ayrıca, VM için bir genel IP adresi ekleyin.  


## <a name="next-steps"></a>Sonraki adımlar

Bir VMware geçiş seçeneği [belirleyin](server-migrate-overview.md) .
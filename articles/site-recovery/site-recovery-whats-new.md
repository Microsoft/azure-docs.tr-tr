---
title: Azure Site Recovery yenilikleri
description: Azure Site Recovery hizmetindeki yeni özelliklerin ve en son güncelleştirmelerin özetini sağlar.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: af1b531ac1b38921a99284aad2108e4027d97f4c
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091713"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery'deki yenilikler

[Azure Site Recovery](site-recovery-overview.md) hizmeti sürekli olarak güncelleştirilir ve geliştirilmiştir. Güncel kalabilmeniz için, bu makalede en son yayınlar, yeni özellikler ve yeni içerikler hakkında bilgi verilmektedir. Bu sayfa düzenli olarak güncelleştirilir.

[Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) kanalında Site Recovery güncelleştirme bildirimlerini izleyip abone olabilirsiniz.



## <a name="supported-updates"></a>Desteklenen Güncelleştirmeler

Site Recovery bileşenleri için n-4 sürümlerini destekliyoruz, burada N en son yayınlanan sürümdür. Bunlar aşağıdaki tabloda özetlenmiştir.

**Güncelleştirme** |  **Birleşik kurulum** | **Yapılandırma sunucusu ova** | **Mobility hizmeti Aracısı** | **Site Recovery sağlayıcı** | **Kurtarma Hizmetleri Aracısı**
--- | --- | --- | --- | --- | ---
[Toplu 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Toplu 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Toplu 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0
[Toplu 39](https://support.microsoft.com/help/4517283/) | 9.27.5308.1 | 5.1.4600.0 | 9.27.5308.1 | 5.1.4600.0 | 2.0.9165.0
[Toplu 38](https://support.microsoft.com/help/4513507/) | 9.26.5269.1 | 5.1.4500.0 | 9.26.5269.1 | 5.1.4500.0 | 2.0.9165.0
        

Güncelleştirme yükleme ve destek hakkında [daha fazla bilgi edinin](service-updates-how-to.md) .


## <a name="updates-november-2019"></a>Güncelleştirmeler (Kasım 2019)

### <a name="update-rollup-42"></a>Güncelleştirme paketi 42

[Güncelleştirme paketi 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)


## <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma

Azure VM olağanüstü durum kurtarma için yeni özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**UEFı** | Site Recovery artık UEFı tabanlı önyükleme mimarisi olan Azure VM 'Leri için olağanüstü durum kurtarmayı desteklemektedir.
**Linux** | Site Recovery artık Azure disk şifrelemesi (ADE) ile Linux çalıştıran Azure sanal makinelerini destekler.
**2. nesil** | Olağanüstü durum kurtarma için tüm 2. nesil Azure sanal makineleri artık desteklenmektedir.
**Bölgeler** | Şimdi, Norveç coğrafi bölümünde Azure VM 'Leri için olağanüstü durum kurtarmayı etkinleştirebilirsiniz.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware'den Azure'a olağanüstü durum kurtarma

VMware 'den Azure 'a olağanüstü durum kurtarma için yeni özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**UEFı** | Site Recovery artık UEFı tabanlı önyükleme mimarisine sahip VMware VM 'Leri için olağanüstü durum kurtarmayı desteklemektedir.<br/><br/> Desteklenen işletim sistemleri Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8 ' i içerir.

## <a name="update-to-servicing-stack-updatesha-2"></a>Hizmet yığını güncelleştirmesine güncelleştirme/SHA-2

Azure sanal makinelerinin bir ikincil bölgeye veya şirket içi VMware VM 'lerine veya fiziksel sunuculara Azure 'da olağanüstü durum kurtarması için aşağıdakilere göz önünde yer verilmiştir:

- Mobility hizmeti uzantısının (Azure VM 'Ler için) ve Mobility hizmeti aracısının (VMware/fiziksel makineler için) sürümü 9.30.5407.1 'dan, bazı makine işletim sistemleri bakım yığını güncelleştirmesi ve SHA-2 ' yi çalıştırıyor olmalıdır. Ayrıntılar aşağıdaki tabloda gösterilmiştir.
- Güncelleştirmeyi ve SHA-2 ' i bağlı KB 'ye uygun olarak yükler. SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez.
- [SHA-2 yükseltmesi ve gereksinimleri](https://aka.ms/SHA-2KB) hakkında daha fazla bilgi edinin.

**İşletim sistemi** | **Azure VM** | **VMware VM/fiziksel makine**
--- | --- | ---
**Windows 2008 R2 SP1** | [Hizmet yığını güncelleştirmesi](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Hizmet yığını güncelleştirmesi](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Hizmet yığını güncelleştirmesi](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Hizmet yığını güncelleştirmesi](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Hizmet yığını güncelleştirmesi](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Hizmet yığını güncelleştirmesi](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Güncelleştirmeler (Ekim 2019)

### <a name="update-rollup-41"></a>Güncelleştirme paketi 41

[Güncelleştirme paketi 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)



### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma

Azure VM olağanüstü durum kurtarma için yeni özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Yük devretme testi ayarları** | Yük devretme testi kurarken, artık IP adresi, NSG, iç yük dengelemesi ve her makine NIC 'i için genel IP adresi dahil olmak üzere test yük devretmesi VM 'si ve ağ için ayarları yapılandırabilirsiniz. Bu ayarlar isteğe bağlıdır ve geçerli davranışı değiştirmez. Bu ayarları yapılandırmazsanız, yük devretme testi sırasında bir Azure VNet seçebilirsiniz. [Daha fazla bilgi edinin](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Kurtarma planları** | Yük devretme güvenilirliğini sağlamak için kurtarma planları artık 100 VM 'Lerle sınırlıdır.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware'den Azure'a olağanüstü durum kurtarma

VMware 'den Azure 'a olağanüstü durum kurtarma için yeni özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Kurtarma planları** | Yük devretme güvenilirliğini sağlamak için kurtarma planları artık 100 VM 'Lerle sınırlıdır.


## <a name="updates-september-2019"></a>Güncelleştirmeler (Eylül 2019)

### <a name="update-rollup-40"></a>Güncelleştirme paketi 40

[Güncelleştirme paketi 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)




### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma

Azure VM olağanüstü durum kurtarma için yeni özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Yeniden çalışma sonrasında temizle** | İkincil Azure 'a yük devreder ve sonra birincil bölgeye geri döndüğünüzde, Site Recovery ikincil bölgedeki makineleri otomatik olarak temizler. VM 'Leri ve NIC 'leri el ile silmeniz gerekmez.
**Sınama yük devretmesi IP adreslerini korur** | Artık bir olağanüstü durum kurtarma ayrıntısı sırasında kaynak VM 'nin IP adresini koruyabilir ve yük devretme testi için bir statik IP adresi seçebilirsiniz.

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fiziksel sunucu olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
Yeni işlem sunucusu uyarıları | Yeni işlem sunucusu uyarıları ekledik. [Daha fazla bilgi edinin](vmware-physical-azure-monitor-process-server.md). 

### <a name="hyper-v-disaster-recovery"></a>Hyper-V olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
Depolama hesabı | Site Recovery artık Hyper-V için Azure olağanüstü durum kurtarma için Güvenlik Duvarı etkinleştirilmiş depolama hesaplarının kullanımını desteklemektedir.  Güvenlik Duvarı etkinleştirilmiş depolama hesaplarını hedef hesap olarak veya önbellek depolaması için seçebilirsiniz. Güvenlik Duvarı etkinleştirilmiş hesabı kullanıyorsanız, güvenilen Microsoft hizmetlerine izin verme seçeneğini etkinleştirdiğinizden emin olun.<br/><br/> Bu, System Center VMM ile veya olmadan Hyper-V VM 'lerinde desteklenir.


## <a name="updates-august-2019"></a>Güncelleştirmeler (Ağustos 2019)

### <a name="update-rollup-39"></a>Güncelleştirme paketi 39

[Güncelleştirme paketi 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)


### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma

Azure VM olağanüstü durum kurtarma için yeni özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Azure AD olmadan şifreleme** | Azure AD uygulaması olmayan şifreleme artık Windows çalıştıran yönetilen disklere yönelik Azure VM çoğaltma için desteklenmektedir.
**Yük devretme için ağ kaynakları** | Başka bir bölgeye yük devrettikten sonra, ağ kaynağı ayarlarını (NSG 'ler, Yük Dengeleme, genel IP adresi) bir VM 'ye ekleyebilirsiniz. 

## <a name="updates-july-2019"></a>Güncelleştirmeler (Temmuz 2019)

### <a name="update-rollup-38"></a>Toplu güncelleştirme 38

[Güncelleştirme paketi 38](https://support.microsoft.com/help/4513507/) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)


### <a name="general"></a>Genel

Site Recovery artık önbellek depolama veya hedef depolama için genel amaçlı v2 depolama hesaplarının kullanımını desteklemektedir. Daha önce yalnızca v1 destekleniyor.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware'den Azure'a olağanüstü durum kurtarma

Artık, yönetilen disklerle bir Azure VM 'ye çoğaltma yaparken diskleri 8 TB 'ye kadar çoğaltabilirsiniz.


## <a name="updates-june-2019"></a>Güncelleştirmeler (Haziran 2019)

### <a name="update-rollup-37"></a>Güncelleştirme paketi 37

[Güncelleştirme paketi 37](https://support.microsoft.com/help/4508614/) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fiziksel sunucu olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**GPT bölümleri** | Güncelleştirme paketi 37 ve sonraki sürümlerde (Mobility hizmeti sürüm 9.25.5241.1), UEFı 'de beş adede kadar GPT bölümü desteklenir. Bu güncelleştirmeden önce dört destekleniyordu.



## <a name="updates-may-2019"></a>Güncelleştirmeler (Mayıs 2019)

### <a name="update-rollup-36"></a>Toplu güncelleştirme 36

[Güncelleştirme paketi 36](https://support.microsoft.com/help/4503156) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)

### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Eklenen diskleri Çoğalt** | Olağanüstü durum kurtarma için zaten etkinleştirilmiş bir Azure VM 'sine eklenen veri diskleri için çoğaltmayı etkinleştirin. [Daha fazla bilgi edinin](azure-to-azure-enable-replication-added-disk.md).
**Otomatik Güncelleştirmeler** | Olağanüstü durum kurtarma için etkin olan Azure VM 'lerinde çalışan Mobility hizmeti uzantısı için otomatik güncelleştirmeleri yapılandırırken, Site Recovery tarafından oluşturulan varsayılan hesabı kullanmak yerine, kullanmak üzere var olan bir Otomasyon hesabı seçebilirsiniz. [Daha fazla bilgi edinin](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fiziksel sunucu olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**İşlem sunucusu izleme** | Şirket içi VMware VM 'Leri ve fiziksel sunucular için olağanüstü durum kurtarma için, iyileştirilmiş sunucu sistem durumu raporlama ve uyarılarla işlem sunucusu sorunlarını izleyin ve sorun giderin. [Daha fazla bilgi edinin](vmware-physical-azure-monitor-process-server.md). 





## <a name="updates-march-2019"></a>Güncelleştirmeler (Mart 2019)

### <a name="update-rollup-35"></a>Güncelleştirme paketi 35

[Güncelleştirme paketi 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fiziksel sunucu olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Yönetilen diskler** | Şirket içi VMware VM 'lerinin ve fiziksel sunucuların çoğaltılması artık doğrudan Azure 'daki yönetilen disklere gönderilir. Şirket içi veriler Azure 'da bir önbellek depolama hesabına gönderilir ve kurtarma noktaları hedef konumdaki yönetilen disklerde oluşturulur. Bu, birden çok hedef depolama hesabını yönetmenize gerek kalmaz.
**Yapılandırma sunucusu** | Site Recovery artık birden çok NIC içeren yapılandırma sunucularını desteklemektedir. Yapılandırma sunucusunu kasaya kaydetmeden önce yapılandırma sunucusu VM 'sine ek bağdaştırıcılar ekleyin. Daha sonra eklerseniz, sunucuyu kasada yeniden kaydetmeniz gerekir.


## <a name="updates-february-2019"></a>Güncelleştirmeler (Şubat 2019)

### <a name="update-rollup-34"></a>Güncelleştirme paketi 34 

[Güncelleştirme paketi 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak).
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak).


### <a name="update-rollup-33"></a>Güncelleştirme paketi 33 

[Güncelleştirme paketi 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak).
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak).


### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma 
Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Ağ eşleme** | Azure VM olağanüstü durum kurtarma için artık çoğaltmayı etkinleştirdiğinizde kullanılabilir bir hedef ağı kullanabilirsiniz. 
**Standart SSD** | Artık [Standart SSD diskleri](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)kullanarak Azure VM 'leri için olağanüstü durum kurtarma ayarlayabilirsiniz.
**Depolama Alanları Doğrudan** | Azure VM uygulamalarında çalışan uygulamalar için, yüksek kullanılabilirlik için [depolama alanları doğrudan](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) kullanarak olağanüstü durum kurtarma ayarlayabilirsiniz.  Site Recovery ile birlikte Depolama Alanları Doğrudan (S2D) kullanılması, Azure VM iş yüklerinin kapsamlı bir şekilde korunmasını sağlar. S2D, Azure 'da bir konuk küme barındırmanızı sağlar. Bu özellikle, bir VM, SAP ASCS katmanı, SQL Server veya genişleme dosya sunucusu gibi kritik bir uygulama barındırıyorsa kullanışlıdır.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fiziksel sunucu olağanüstü durum kurtarma
Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Linux BRTFS dosya sistemi** | Site Recovery artık, BRTFS dosya sistemi ile VMware VM 'lerinin çoğaltılmasını desteklemektedir. Şu durumlarda çoğaltma desteklenmez:<br/><br/>-BTRFS dosya sistemi alt birimi, çoğaltma etkinleştirildikten sonra değiştirilir.<br/><br/>-Dosya sistemi birden çok diske yayılmış.<br/><br/>-BTRFS dosya sistemi RAID 'i destekler.
**Windows Server 2019** | Windows Server 2019 çalıştıran makineler için destek eklendi.


## <a name="updates-january-2019"></a>Güncelleştirmeler (Ocak 2019)


### <a name="accelerated-networking-azure-vms"></a>Hızlandırılmış ağ (Azure VM 'Leri)

Hızlandırılmış ağ, bir VM 'ye tek köklü g/ç Sanallaştırması (SR-ıOV) sağlar ve ağ performansını geliştirir. Azure VM için çoğaltmayı etkinleştirdiğinizde, Site Recovery hızlandırılmış ağın etkinleştirilip etkinleştirilmediğini algılar. Eğer ise, yük devretme Site Recovery sonrasında, hem [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) hem de [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)IÇIN hedef çoğaltma Azure VM 'sinde hızlandırılmış ağı otomatik olarak yapılandırır.

[Daha fazla bilgi edinin](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Güncelleştirme paketi 32 

[Güncelleştirme paketi 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak).
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak).

### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Linux desteği** | RedHat Workstation 6/7 için destek eklendi ve Ubuntu, de, ve SUSE için yeni çekirdek sürümleri eklendi.
**Depolama Alanları Doğrudan** | Site Recovery, Depolama Alanları Doğrudan (S2D) kullanarak Azure VM 'lerini destekler.

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VM 'Leri/fiziksel sunucular olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.
 
**Özellik** | **Ayrıntılar**
--- | ---
**Linux desteği** | RedHat Enterprise Linux 7,6, RedHat Workstation 6/7, Oracle Linux 6.10/7.6 ve Ubuntu, de, ve SUSE için yeni çekirdek sürümleri için destek eklenmiştir.


### <a name="update-rollup-31"></a>Güncelleştirme paketi 31 

[Güncelleştirme paketi 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak).
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak).

### <a name="vmware-vmsphysical-servers-replication"></a>VMware VM 'Leri/fiziksel sunucular çoğaltması 
Bu ay eklenen özellikler tabloda özetlenmiştir.
**Özellik** | **Ayrıntılar**
--- | ---
**Linux desteği** | Oracle Linux 6,8 ve 6.9/7.0 ve UEK5 çekirdeği için destek eklenmiştir.
**LVM** | LVM ve LVM2 birimlerine yönelik destek eklendi.<br/><br/> Bir disk bölümünde ve LVM birimlerinde/boot dizini artık desteklenmektedir.
**Dizinler** | Bu dizinler için, ayrı bölümler olarak ayarlanan veya aynı sistem diskinde olmayan dosya sistemleri için destek eklendi:<br/><br/> /(root),/Boot,/usr,/usr/local,/var,/etc.
**Windows Server 2008** | Dinamik disklere yönelik destek eklendi.
**Yük devretme** | Storvsc ve vsbus önyükleme sürücüleri olmadığı için VMware VM 'Leri için geliştirilmiş yük devretme süresi.
**UEFı desteği** | Azure VM 'Leri UEFı önyükleme türünü desteklemez. Artık, Site Recovery ile şirket içi fiziksel sunucuları UEFı ile Azure 'a geçirebilirsiniz. Site Recovery, geçiş işleminden önce önyükleme türünü BIOS 'a dönüştürerek sunucuyu geçirir. Site Recovery, yalnızca VM 'Ler için bu dönüştürmeyi daha önce destekliyordu. Windows Server 2012 veya üstünü çalıştıran fiziksel sunucular için destek sağlanır.

### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma
Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Linux desteği** | Oracle Linux 6,8 ve 6.9/7.0 için desteklenen eklendi; ve UEK5 çekirdeği için.
**Linux BRTFS dosya sistemi** | Azure VM 'Leri için desteklenir.
**Kullanılabilirlik bölgelerinde Azure VM 'Leri** | Kullanılabilirlik bölgelerinde dağıtılan Azure VM 'Leri için başka bir bölgeye çoğaltma etkinleştirebilirsiniz. Artık bir Azure VM için çoğaltmayı etkinleştirebilir ve yük devretme hedefini tek bir VM örneğine, bir kullanılabilirlik kümesindeki bir VM 'ye veya bir kullanılabilirlik bölgesindeki VM 'ye ayarlayabilirsiniz. Ayar çoğaltmayı etkilemez. Duyuruyu [okuyun](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) .
**Güvenlik Duvarı özellikli depolama (portal/PowerShell)** | [Güvenlik duvarı özellikli depolama hesapları](https://docs.microsoft.com/azure/storage/common/storage-network-security)için destek eklendi.<br/><br/> Azure VM 'lerini, güvenlik duvarı özellikli depolama hesaplarındaki yönetilmeyen disklerle, olağanüstü durum kurtarma için başka bir Azure bölgesine çoğaltabilirsiniz.<br/><br/> Yönetilmeyen diskler için hedef depolama hesapları olarak güvenlik duvarı özellikli depolama hesapları kullanabilirsiniz.<br/><br/> Portalda ve PowerShell kullanılarak desteklenir.

## <a name="updates-december-2018"></a>Güncelleştirmeler (Aralık 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Mobility hizmeti için otomatik güncelleştirmeler (Azure VM 'Leri)

Site Recovery Mobility hizmeti uzantısına otomatik güncelleştirmeler için bir seçenek ekledi. Mobility hizmeti uzantısı, Site Recovery tarafından çoğaltılan her bir Azure VM 'ye yüklenir. Çoğaltmayı etkinleştirdiğinizde, Site Recovery uzantıya güncelleştirmeleri yönetmesine izin verip vermeyeceğinizi seçersiniz.

Güncelleştirmeler VM yeniden başlatması gerektirmez ve çoğaltmayı etkilemez. [Daha fazla bilgi edinin](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma için Fiyatlandırma Hesaplayıcı

Azure VM 'lerinin olağanüstü durum kurtarması, VM lisanslama maliyetlerini ve ağ ve depolama maliyetlerini doğurur. Azure, bu maliyetleri bulmanıza yardımcı olmak için bir [Fiyatlandırma Hesaplayıcı](https://aka.ms/a2a-cost-estimator) sağlar. Site Recovery artık 12 Standart HDD Disk ve 6 Premium SSD disk içeren altı VM kullanarak üç katmanlı bir uygulamayı temel alan örnek bir dağıtım fiyatlarına [örnek bir fiyatlandırma tahmini](https://aka.ms/a2a-cost-estimator) sunmaktadır.

- Örnek, standart için günde 10 GB ve Premium için 20 GB veri değişikliğini kabul eden bir örnektir.
- Belirli dağıtımınız için değişkenleri, maliyetleri tahmin etmek üzere değiştirebilirsiniz.
- VM 'lerin sayısını, yönetilen disklerin sayısını ve türünü ve VM 'lerde beklenen toplam veri değişikliği oranını belirtebilirsiniz.
- Ayrıca, bant genişliği maliyetlerini tahmin etmek için bir sıkıştırma faktörü uygulayabilirsiniz.

Duyuruyu [okuyun](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) .


## <a name="updates-october-2018"></a>Güncelleştirmeler (Ekim 2018)

### <a name="update-rollup-30"></a>Güncelleştirme paketi 30 

[Güncelleştirme paketi 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak).
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak).

### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma
Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Bölge desteği** | Avustralya Orta 1 ve Avustralya Orta 2 için Site Recovery desteği eklendi.
**Disk şifrelemesi desteği** | Azure AD uygulaması ile Azure disk şifrelemesi (ADE) ile şifrelenen Azure VM 'lerinin olağanüstü durum kurtarması için destek eklendi. [Daha fazla bilgi edinin](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Disk dışlama** | Başlatılmamış diskler artık Azure VM çoğaltma sırasında otomatik olarak dışlanır.
**Güvenlik Duvarı özellikli depolama (PowerShell)** | [Güvenlik duvarı özellikli depolama hesapları](https://docs.microsoft.com/azure/storage/common/storage-network-security)için destek eklendi.<br/><br/> Azure VM 'lerini, güvenlik duvarı özellikli depolama hesaplarındaki yönetilmeyen disklerle, olağanüstü durum kurtarma için başka bir Azure bölgesine çoğaltabilirsiniz.<br/><br/> Yönetilmeyen diskler için hedef depolama hesapları olarak güvenlik duvarı özellikli depolama hesapları kullanabilirsiniz.<br/><br/> Yalnızca PowerShell kullanılarak desteklenir.


### <a name="update-rollup-29"></a>Güncelleştirme paketi 29 

[Güncelleştirme paketi 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak).
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak).


## <a name="updates-august-2018"></a>Güncelleştirmeler (Ağustos 2018)

### <a name="update-rollup-28"></a>Güncelleştirme paketi 28 

[Güncelleştirme paketi 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak).
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak).

### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma 
Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Linux desteği** | RedHat Enterprise Linux 6,10 için desteklenen eklendi; CentOS 6,10.<br/><br/>
**Bulut desteği** | Almanya bulutundaki Azure VM 'Leri için desteklenen olağanüstü durum kurtarma.
**Çapraz abonelik olağanüstü durum kurtarma** | Aynı Azure Active Directory kiracısında, bir bölgedeki Azure VM 'Leri farklı bir abonelikte yer alan başka bir bölgeye çoğaltma desteği. [Daha fazla bilgi edinin](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/fiziksel sunucu olağanüstü durum kurtarma 
Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Linux desteği** | RedHat Enterprise Linux 6,10, CentOS 6,10 için destek eklendi.<br/><br/> Eski BIOS uyumluluk modundaki GUID bölümleme tablosu (GPT) Bölüm stilini kullanan Linux tabanlı VM 'Ler artık desteklenmektedir. Daha fazla bilgi için [Azure VM hakkında SSS bölümüne](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) bakın. 
**Geçişten sonra VM 'Ler için olağanüstü durum kurtarma** | Çoğaltmayı etkinleştirmeden önce VM 'de Mobility hizmetini kaldırmaya gerek olmadan Azure 'a geçirilen şirket içi bir VMware sanal makinesi için bir ikincil bölgeye olağanüstü durum kurtarmayı etkinleştirme desteği.
**Windows Server 2008** | Windows Server 2008 R2/2008 64-bit ve 32 bit çalıştıran makinelerin geçirilmesi için destek.<br/><br/> Yalnızca geçiş (çoğaltma ve yük devretme). Yeniden çalışma desteklenmiyor.

## <a name="updates-july-2018"></a>Güncelleştirmeler (Temmuz 2018)

### <a name="update-rollup-27-july-2018"></a>Güncelleştirme paketi 27 (Temmuz 2018)

[Güncelleştirme paketi 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) , aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak).
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak).

### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma 

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Linux desteği** | Red Hat Enterprise Linux 7,5 için destek eklendi.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/fiziksel sunucu olağanüstü durum kurtarma 

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Linux desteği** | Red Hat Enterprise Linux 7,5 için destek eklendi SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Sonraki adımlar

[Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) sayfasında güncelleştirmelerimizle güncel tutun.

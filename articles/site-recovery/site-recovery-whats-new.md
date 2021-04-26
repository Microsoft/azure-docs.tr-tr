---
title: Azure Site Recovery yenilikleri
description: Azure Site Recovery hizmetindeki yeni özelliklerin ve en son güncelleştirmelerin özetini sağlar.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 423fbc563878d0cc2a63a5366d2226281a204e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454609"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery'deki yenilikler

[Azure Site Recovery](site-recovery-overview.md) hizmeti sürekli olarak güncelleştirilir ve geliştirilmiştir. Güncel kalabilmeniz için, bu makalede en son yayınlar, yeni özellikler ve yeni içerikler hakkında bilgi verilmektedir. Bu sayfa düzenli olarak güncelleştirilir.

[Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) kanalında Site Recovery güncelleştirme bildirimlerini izleyip abone olabilirsiniz.

## <a name="supported-updates"></a>Desteklenen Güncelleştirmeler

Site Recovery bileşenleri için n-4 sürümlerini destekliyoruz, burada N en son yayınlanan sürümdür. Bunlar aşağıdaki tabloda özetlenmiştir.

**Güncelleştirme** |  **Birleşik kurulum** | **Yapılandırma sunucusu ova** | **Mobility hizmeti Aracısı** | **Site Recovery sağlayıcı** | **Kurtarma Hizmetleri Aracısı**
--- | --- | --- | --- | --- | ---
[Toplu 54](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | 9.41.5888.1 | 5.1.6620.0 | 9.41.5888.1 | 5.1.6620.0  | 2.0.9202.0
[Toplu 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 9.40.5850.1 | 5.1.6537.0 | 9.40.5850.1 | 5.1.6537.0  | 2.0.9202.0
[Toplu 52](https://support.microsoft.com/help/4597409/)  | 9.39.5796.1 | 5.1.6458.0 | 9.39.5796.1 | 5.1.6458.0  | 2.0.9196.0
[Toplu 51](https://support.microsoft.com/help/4590304)  | 9.38.5761.1 | 5.1.6400.0 | 9.38.5761.1 | 5.1.6400.0  | 2.0.9193.0
[Toplu 50](https://support.microsoft.com/help/4582666/) | 9.37.5724.1 | 5.1.6347.0 | 9.37.5724.1 | 5.1.6347.0  | 2.0.9192.0
[Toplu 49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0


Güncelleştirme yükleme ve destek hakkında [daha fazla bilgi edinin](service-updates-how-to.md) .

## <a name="updates-february-2021"></a>Güncelleştirmeler (Şubat 2021)

### <a name="update-rollup-54"></a>Güncelleştirme paketi 54

[Güncelleştirme paketi 54](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Toplamada ayrıntılı olarak bir dizi düzeltmeler ve geliştirmeler. 
**Azure VM olağanüstü durum kurtarma** | Azure Site Recovery kullanarak bölgeye olağanüstü durum kurtarma, artık 4 daha fazla bölgede, Kuzey Avrupa, Doğu ABD, Orta ABD ve Batı ABD 2.<br/>
**Azure 'da VMware VM/fiziksel olağanüstü durum kurtarma** | Güncelleştirme, çoğaltmayı etkinleştirdikten sonra VMware/fiziksel makineler için yakınlık yerleşimi gruplarının seçilmesi için portal desteği içerir.<br/><br/> VMware makinelerini veri diski boyutu 32 TB 'a varan bir ISS 'ye kadar korumak artık desteklenmektedir.
**Azure 'da Hyper-V olağanüstü durum kurtarma** | Güncelleştirme, çoğaltmayı etkinleştirdikten sonra Hyper-V makineleri için yakınlık yerleşimi gruplarının seçilmesi için portal desteği içerir.


## <a name="updates-january-2021"></a>Güncelleştirmeler (Ocak 2021)

### <a name="update-rollup-53"></a>Güncelleştirme paketi 53

[Güncelleştirme paketi 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Toplamada ayrıntılı olarak bir dizi düzeltmeler ve geliştirmeler. 
**Azure VM olağanüstü durum kurtarma** | Etiketler için çoğaltma artık destekleniyor. Kaynak bölgedeki Azure VM 'lerine, disklere ve NIC 'lerine eklenen Etiketler hedef bölgedeki makinelere çoğaltılır.<br/><br/> Deler 10 çalıştıran Azure VM 'Leri artık çoğaltma için desteklenmektedir.
**Azure 'da VMware VM/fiziksel olağanüstü durum kurtarma** | Güncelleştirme, Azure 'a VMware VM çoğaltması için günlük geliştirmelerini ve geliştirilmiş hata iletilerini içerir.<br/><br/> VMware VM 'Leri ve deler 10 çalıştıran fiziksel makineler artık çoğaltma için desteklenmektedir.


## <a name="updates-november-2020"></a>Güncelleştirmeler (Kasım 2020)

### <a name="update-rollup-52"></a>Güncelleştirme paketi 52

[Güncelleştirme paketi 52](https://support.microsoft.com/help/4597409/update-rollup-52-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Mobility hizmeti için yeni Linux desteği de dahil olmak üzere, toplu olarak ayrıntılı bir dizi düzeltmeler ve geliştirmeler.
**Azure VM olağanüstü durum kurtarma** | Artık RHEL 8,3 ve Oracle Linux 7,9 çalıştıran VM 'Ler için destekleniyor
**Azure 'da VMware VM/fiziksel olağanüstü durum kurtarma** | Artık RHEL 8,3, Oracle Linux 7.9/8.3 çalıştıran VM 'Ler için desteklenir.

## <a name="updates-october-2020"></a>Güncelleştirmeler (Ekim 2020)

### <a name="update-rollup-51"></a>Güncelleştirme paketi 51

[Güncelleştirme paketi 51](https://support.microsoft.com/help/4590304/update-rollup-51-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Mobility hizmeti için yeni Linux desteği de dahil olmak üzere, toplu olarak ayrıntılı bir dizi düzeltmeler ve geliştirmeler.

## <a name="updates-september-2020"></a>Güncelleştirmeler (Eylül 2020)

### <a name="update-rollup-50"></a>Güncelleştirme paketi 50

[Güncelleştirme paketi 50](https://support.microsoft.com/help/4582666/update-rollup-50-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Toplamada ayrıntılı olarak bir dizi düzeltmeler ve geliştirmeler.

## <a name="updates-august-2020"></a>Güncelleştirmeler (Ağustos 2020)

### <a name="update-rollup-49"></a>Güncelleştirme paketi 49

[Güncelleştirme paketi 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Mobility hizmeti için yeni Linux desteği de dahil olmak üzere, toplu olarak ayrıntılı bir dizi düzeltmeler ve geliştirmeler.

## <a name="updates-july-2020"></a>Güncelleştirmeler (Temmuz 2020)

### <a name="update-rollup-48"></a>Güncelleştirme paketi 48

[Güncelleştirme paketi 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Toplamada ayrıntılı olarak bir dizi düzeltmeler ve geliştirmeler.

> [!NOTE]
> Güncelleştirme paketi 48, ADE kullanılarak şifrelenmiş Linux makineleri için çoğaltmayı etkinleştirme konusunda bilinen bir sorun içeriyor. [Daha fazla bilgi edinin](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137).

### <a name="update-rollup-47"></a>Güncelleştirme paketi 47

[Güncelleştirme paketi 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Toplamada ayrıntılı olarak bir dizi düzeltmeler ve geliştirmeler.

## <a name="updates-june-2020"></a>Güncelleştirmeler (Haziran 2020)

### <a name="update-rollup-46"></a>Güncelleştirme paketi 46

[Güncelleştirme paketi 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Toplamada ayrıntılı olarak bir dizi düzeltmeler ve geliştirmeler.

## <a name="updates-march-2020"></a>Güncelleştirmeler (Mart 2020)

### <a name="update-rollup-45"></a>Güncelleştirme paketi 45

[Güncelleştirme paketi 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar:

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları toplu olarak, toplamada ayrıntılı olarak güncelleştirir.
**Sorun düzeltmeleri/geliştirmeler** | Toplamada ayrıntılı olarak bir dizi düzeltmeler ve geliştirmeler.

## <a name="updates-january-2020"></a>Güncelleştirmeler (Ocak 2020)

### <a name="update-rollup-44"></a>Güncelleştirme paketi 44

[Güncelleştirme paketi 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery sağlayıcıları ve aracıları için güncelleştirme yoktu.
**Sorun düzeltmeleri/geliştirmeler** | Toplamada ayrıntılı olarak bir dizi düzeltmeler ve geliştirmeler.

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware olağanüstü durum kurtarma

Azure sanal makineleri artık, müşteri tarafından yönetilen anahtarlarla bekleyen şifreleme için VM 'Leri etkinleştirir. [Daha fazla bilgi edinin](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Güncelleştirme paketi 43

[Güncelleştirme paketi 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)


## <a name="updates-november-2019"></a>Güncelleştirmeler (Kasım 2019)

### <a name="update-rollup-42"></a>Güncelleştirme paketi 42

[Güncelleştirme paketi 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) aşağıdaki güncelleştirmeleri sağlar.

**Güncelleştirme** | **Ayrıntılar**
--- | ---
**Sağlayıcılar ve aracılar** | Site Recovery aracıları ve sağlayıcıları güncelleştirme (toplamada ayrıntılı olarak)
**Sorun düzeltmeleri/geliştirmeler** | Bir dizi düzeltme ve geliştirmeler (toplamada ayrıntılı olarak)


### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma

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
- [SHA-2 yükseltme ve gereksinimleri](https://aka.ms/SHA-2KB)hakkında daha fazla bilgi edinin.

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

### <a name="update-rollup-38"></a>Güncelleştirme paketi 38

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

### <a name="update-rollup-36"></a>Güncelleştirme paketi 36

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
**Otomatik güncelleştirmeler** | Olağanüstü durum kurtarma için etkin olan Azure VM 'lerinde çalışan Mobility hizmeti uzantısı için otomatik güncelleştirmeleri yapılandırırken, Site Recovery tarafından oluşturulan varsayılan hesabı kullanmak yerine, kullanmak üzere var olan bir Otomasyon hesabı seçebilirsiniz. [Daha fazla bilgi edinin](azure-to-azure-autoupdate.md).


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
**Yapılandırma Sunucusu** | Site Recovery artık birden çok NIC içeren yapılandırma sunucularını desteklemektedir. Yapılandırma sunucusunu kasaya kaydetmeden önce yapılandırma sunucusu VM 'sine ek bağdaştırıcılar ekleyin. Daha sonra eklerseniz, sunucuyu kasada yeniden kaydetmeniz gerekir.


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
**Ağ eşlemesi** | Azure VM olağanüstü durum kurtarma için artık çoğaltmayı etkinleştirdiğinizde kullanılabilir bir hedef ağı kullanabilirsiniz.
**Standart SSD** | Artık [Standart SSD diskleri](../virtual-machines/disks-types.md#standard-ssd)kullanarak Azure VM 'leri için olağanüstü durum kurtarma ayarlayabilirsiniz.
**Depolama Alanları Doğrudan** | Azure VM uygulamalarında çalışan uygulamalar için, yüksek kullanılabilirlik için [depolama alanları doğrudan](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) kullanarak olağanüstü durum kurtarma ayarlayabilirsiniz.  Site Recovery ile birlikte Depolama Alanları Doğrudan (S2D) kullanılması, Azure VM iş yüklerinin kapsamlı bir şekilde korunmasını sağlar. S2D, Azure 'da bir konuk küme barındırmanızı sağlar. Bu özellikle, bir VM, SAP ASCS katmanı, SQL Server veya genişleme dosya sunucusu gibi kritik bir uygulama barındırıyorsa kullanışlıdır.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fiziksel sunucu olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Linux BRTFS dosya sistemi** | Site Recovery artık, BRTFS dosya sistemi ile VMware VM 'lerinin çoğaltılmasını desteklemektedir. Şu durumlarda çoğaltma desteklenmez:<br/><br/>-BTRFS dosya sistemi alt birimi, çoğaltma etkinleştirildikten sonra değiştirilir.<br/><br/>-Dosya sistemi birden çok diske yayılmış.<br/><br/>-BTRFS dosya sistemi RAID 'i destekler.
**Windows Server 2019** | Windows Server 2019 çalıştıran makineler için destek eklendi.


## <a name="updates-january-2019"></a>Güncelleştirmeler (Ocak 2019)


### <a name="accelerated-networking-azure-vms"></a>Hızlandırılmış ağ (Azure VM 'Leri)

Hızlandırılmış ağ, bir VM 'ye tek köklü g/ç Sanallaştırması (SR-ıOV) sağlar ve ağ performansını geliştirir. Azure VM için çoğaltmayı etkinleştirdiğinizde, Site Recovery hızlandırılmış ağın etkinleştirilip etkinleştirilmediğini algılar. Eğer ise, yük devretme Site Recovery sonrasında, hem [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) hem de [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)IÇIN hedef çoğaltma Azure VM 'sinde hızlandırılmış ağı otomatik olarak yapılandırır.

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
**Linux desteği** | RedHat Enterprise Linux 7,6, RedHat Workstation 6/7, Oracle Linux 6,10 ve Oracle Linux 7,6 ve Ubuntu, de, ve SUSE için yeni çekirdek sürümleri için destek eklendi.


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
**Linux desteği** |  Oracle Linux 6,8, Oracle Linux 6,9 ve Oracle Linux 7,0 için Red Hat uyumlu çekirdek ile ve Ayırıcılanılabilir kurumsal çekirdek (UEK) sürüm 5 için destek eklenmiştir.
**LVM** | LVM ve LVM2 birimlerine yönelik destek eklendi.<br/><br/> Bir disk bölümünde ve LVM birimlerinde/boot dizini artık desteklenmektedir.
**Dizine** | Bu dizinler için, ayrı bölümler olarak ayarlanan veya aynı sistem diskinde olmayan dosya sistemleri için destek eklendi:<br/><br/> /(root),/Boot,/usr,/usr/local,/var,/etc.
**Windows Server 2008** | Dinamik disklere yönelik destek eklendi.
**Yük devretme** | Storvsc ve vsbus önyükleme sürücüleri olmadığı için VMware VM 'Leri için geliştirilmiş yük devretme süresi.
**UEFı desteği** | Azure VM 'Leri UEFı önyükleme türünü desteklemez. Artık, Site Recovery ile şirket içi fiziksel sunucuları UEFı ile Azure 'a geçirebilirsiniz. Site Recovery, geçiş işleminden önce önyükleme türünü BIOS 'a dönüştürerek sunucuyu geçirir. Site Recovery, yalnızca VM 'Ler için bu dönüştürmeyi daha önce destekliyordu. Windows Server 2012 veya üstünü çalıştıran fiziksel sunucular için destek sağlanır.

### <a name="azure-vm-disaster-recovery"></a>Azure VM olağanüstü durum kurtarma

Bu ay eklenen özellikler tabloda özetlenmiştir.

**Özellik** | **Ayrıntılar**
--- | ---
**Linux desteği** | Oracle Linux 6,8, Oracle Linux 6,9 ve Oracle Linux 7,0 için Red Hat uyumlu çekirdek ile ve Ayırıcılanılabilir kurumsal çekirdek (UEK) sürüm 5 için destek eklenmiştir.
**Linux BRTFS dosya sistemi** | Azure VM 'Leri için desteklenir.
**Kullanılabilirlik bölgelerinde Azure VM 'Leri** | Kullanılabilirlik bölgelerinde dağıtılan Azure VM 'Leri için başka bir bölgeye çoğaltma etkinleştirebilirsiniz. Artık bir Azure VM için çoğaltmayı etkinleştirebilir ve yük devretme hedefini tek bir VM örneğine, bir kullanılabilirlik kümesindeki bir VM 'ye veya bir kullanılabilirlik bölgesindeki VM 'ye ayarlayabilirsiniz. Ayar çoğaltmayı etkilemez. Duyuruyu [okuyun](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) .
**Güvenlik Duvarı özellikli depolama (portal/PowerShell)** | [Güvenlik duvarı özellikli depolama hesapları](../storage/common/storage-network-security.md)için destek eklendi.<br/><br/> Azure VM 'lerini, güvenlik duvarı özellikli depolama hesaplarındaki yönetilmeyen disklerle, olağanüstü durum kurtarma için başka bir Azure bölgesine çoğaltabilirsiniz.<br/><br/> Yönetilmeyen diskler için hedef depolama hesapları olarak güvenlik duvarı özellikli depolama hesapları kullanabilirsiniz.<br/><br/> Portalda ve PowerShell kullanılarak desteklenir.

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
**Güvenlik Duvarı özellikli depolama (PowerShell)** | [Güvenlik duvarı özellikli depolama hesapları](../storage/common/storage-network-security.md)için destek eklendi.<br/><br/> Azure VM 'lerini, güvenlik duvarı özellikli depolama hesaplarındaki yönetilmeyen disklerle, olağanüstü durum kurtarma için başka bir Azure bölgesine çoğaltabilirsiniz.<br/><br/> Yönetilmeyen diskler için hedef depolama hesapları olarak güvenlik duvarı özellikli depolama hesapları kullanabilirsiniz.<br/><br/> Yalnızca PowerShell kullanılarak desteklenir.


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
**Linux desteği** | RedHat Enterprise Linux 6,10, CentOS 6,10 için destek eklendi.<br/><br/> Eski BIOS uyumluluk modundaki GUID bölümleme tablosu (GPT) Bölüm stilini kullanan Linux tabanlı VM 'Ler artık desteklenmektedir. Daha fazla bilgi için [Azure VM hakkında SSS bölümüne](../virtual-machines/faq-for-disks.md) bakın.
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
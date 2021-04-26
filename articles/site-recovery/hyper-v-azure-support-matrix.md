---
title: Azure Site Recovery ile Azure 'da Hyper-V VM 'lerinin olağanüstü durum kurtarma desteği
description: Azure Site Recovery ile Azure 'da Hyper-V VM olağanüstü durum kurtarma için desteklenen bileşenleri ve gereksinimleri özetler
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
ms.openlocfilehash: 936950ef5dd2c1c7343e30536ab8836fcd198bd0
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580832"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Şirket içi Hyper-V VM 'lerinin Azure 'a olağanüstü durum kurtarması için destek matrisi

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak şirket içi Hyper-V VM 'lerinin olağanüstü durum kurtarma için desteklenen bileşenleri ve ayarları özetlenmektedir.

>[!NOTE]
> Site Recovery, müşteri verilerini, kaynak makineler için olağanüstü durum kurtarma için ayarlanan hedef bölgeden taşımaz veya depolamaz. Müşteriler, bir kurtarma hizmetleri kasasını tercih ettikleri farklı bir bölgeden seçebilirler. Kurtarma Hizmetleri Kasası meta veriler içeriyor ancak gerçek müşteri verisi yok.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

**Senaryo** | **Ayrıntılar**
--- | ---
Virtual Machine Manager ile Hyper-V <br> <br>| System Center Virtual Machine Manager dokusunda yönetilen Hyper-V konaklarında çalışan VM 'Ler için Azure 'da olağanüstü durum kurtarma gerçekleştirebilirsiniz.<br/><br/> Bu senaryoyu Azure portal veya PowerShell kullanarak dağıtabilirsiniz.<br/><br/> Hyper-V konakları Virtual Machine Manager tarafından yönetildiğinde, ikincil şirket içi bir siteye olağanüstü durum kurtarma işlemi de yapabilirsiniz. Bu senaryo hakkında daha fazla bilgi edinmek için [Bu öğreticiyi](hyper-v-vmm-disaster-recovery.md)okuyun.
Virtual Machine Manager olmadan Hyper-V | Virtual Machine Manager tarafından yönetilmeyen Hyper-V konaklarında çalışan VM 'Ler için Azure 'da olağanüstü durum kurtarma gerçekleştirebilirsiniz.<br/><br/> Bu senaryoyu Azure portal veya PowerShell kullanarak dağıtabilirsiniz.

## <a name="on-premises-servers"></a>Şirket içi sunucular

**Sunucu** | **Gereksinimler** | **Ayrıntılar**
--- | --- | ---
Hyper-V (Virtual Machine Manager olmadan çalışıyor) |  Windows Server 2019, Windows Server 2016, en son güncelleştirmeleri içeren Windows Server 2012 R2 <br/><br/> **Note:** Bu işletim sistemlerinin sunucu çekirdeği yüklemesi de desteklenir. | Azure Site Recovery ile Windows Server 2012 R2 'yi/veya SCVMM 2012 R2 'yi zaten yapılandırdıysanız ve işletim sistemini yükseltmeyi planlarsanız, lütfen rehberlik [belgelerini izleyin.](upgrade-2012R2-to-2016.md)
Hyper-V (Virtual Machine Manager ile çalışıyor) | Virtual Machine Manager 2019, Virtual Machine Manager 2016 Virtual Machine Manager 2012 R2 <br/><br/> **Note:** Bu işletim sistemlerinin sunucu çekirdeği yüklemesi de desteklenir.  | Virtual Machine Manager kullanılırsa, Windows Server 2019 Konakları Virtual Machine Manager 2019 ' de yönetilmelidir. Benzer şekilde, Windows Server 2016 Konakları Virtual Machine Manager 2016 ' de yönetilmelidir.

> [!NOTE]
> Şirket içi sunucuda .NET Framework 4.6.2 veya üzeri bulunduğundan emin olun.

## <a name="replicated-vms"></a>Çoğaltılan VM 'Ler


Aşağıdaki tabloda VM desteği özetlenmektedir. Site Recovery, desteklenen bir işletim sisteminde çalışan tüm iş yüklerini destekler.

 **Bileşen** | **Ayrıntılar**
--- | ---
VM yapılandırması | Azure 'a çoğaltılan VM 'Lerin [Azure gereksinimlerini](#azure-vm-requirements)karşılaması gerekir.
Konuk işletim sistemi | [Azure için desteklenen](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases)herhangi bir konuk işletim sistemi.<br/><br/> Windows Server 2016 nano sunucu desteklenmiyor.


## <a name="vmdisk-management"></a>VM/disk yönetimi

**Eylem** | **Ayrıntılar**
--- | ---
Çoğaltılan Hyper-V VM 'de diski yeniden boyutlandır | Desteklenmez. Çoğaltmayı devre dışı bırakın, değişikliği yapın ve ardından VM için çoğaltmayı yeniden etkinleştirin.
Çoğaltılan Hyper-V VM 'ye disk ekleme | Desteklenmez. Çoğaltmayı devre dışı bırakın, değişikliği yapın ve ardından VM için çoğaltmayı yeniden etkinleştirin.

## <a name="hyper-v-network-configuration"></a>Hyper-V ağ yapılandırması

**Bileşen** | **Virtual Machine Manager ile Hyper-V** | **Virtual Machine Manager olmadan Hyper-V**
--- | --- | ---
Konak ağı: NIC ekibi oluşturma | Yes | Yes
Konak ağı: VLAN | Yes | Yes
Konak ağı: IPv4 | Yes | Yes
Konak ağı: IPv6 | Hayır | Hayır
Konuk VM ağı: NIC ekibi oluşturma | Hayır | Hayır
Konuk VM ağı: IPv4 | Yes | Yes
Konuk VM ağı: IPv6 | Hayır | Yes
Konuk VM ağı: statik IP (Windows) | Yes | Yes
Konuk VM ağı: statik IP (Linux) | Hayır | Hayır
Konuk VM ağı: çoklu NIC | Yes | Yes
HTTPS proxy 'Si | Hayır | Hayır
Site Recovery hizmetine özel bağlantı erişimi | Evet. [Daha fazla bilgi edinin](hybrid-how-to-enable-replication-private-endpoints.md). | Evet. [Daha fazla bilgi edinin](hybrid-how-to-enable-replication-private-endpoints.md).




## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM ağ yapılandırması (yük devretmeden sonra)

**Bileşen** | **Virtual Machine Manager ile Hyper-V** | **Virtual Machine Manager olmadan Hyper-V**
--- | --- | ---
Azure ExpressRoute | Yes | Yes
ILB | Yes | Yes
ELB | Yes | Yes
Azure Traffic Manager | Yes | Yes
Çoklu NIC | Yes | Yes
Ayrılmış IP | Yes | Yes
IPv4 | Yes | Yes
Kaynak IP adresini sakla | Yes | Yes
Azure sanal ağ hizmet uç noktaları<br/> (Azure Storage güvenlik duvarları olmadan) | Yes | Yes
Hızlandırılmış Ağ | Hayır | Hayır


## <a name="hyper-v-host-storage"></a>Hyper-V konak depolaması

**Depolama** | **Virtual Machine Manager ile Hyper-V** | **Virtual Machine Manager olmadan Hyper-V**
--- | --- | --- 
NFS | NA | NA
SMB 3.0 | Yes | Yes
SAN (ISCSı) | Yes | Yes
Çoklu yol (MPIO). Test edilen:<br></br> Microsoft DSM, EMC PowerPath 5,7 SP4, Clariiyon için EMC PowerPath DSM | Yes | Yes

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM Konuk depolaması

**Depolama** | **Virtual Machine Manager ile Hyper-V** | **Virtual Machine Manager olmadan Hyper-V**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | Yes | Yes
2. Nesil VM | Yes | Yes
EFı/UEFı<br></br>Azure 'daki geçirilmiş VM otomatik olarak bir BIOS önyükleme VM 'sine dönüştürülür. VM yalnızca Windows Server 2012 ve üstünü çalıştırmalıdır. İşletim sistemi diski en fazla beş bölüm veya daha az olmalıdır ve işletim sistemi diskinin boyutu 300 GB 'tan az olmalıdır.| Yes | Yes
Paylaşılan küme diski | Hayır | Hayır
Şifrelenmiş disk | Hayır | Hayır
NFS | NA | NA
SMB 3.0 | Hayır | Hayır
RDM | NA | NA
Disk >1 TB | Evet, 4.095 GB 'a kadar | Evet, 4.095 GB 'a kadar
Disk: 4K mantıksal ve fiziksel kesim | Desteklenmez: Gen 1/Gen 2 | Desteklenmez: Gen 1/Gen 2
Disk: 4K mantıksal ve 512 bayt fiziksel kesim | Yes |  Yes
Mantıksal birim yönetimi (LVM). LVM yalnızca veri disklerinde desteklenir. Azure yalnızca tek bir işletim sistemi diski sağlar. | Yes | Yes
Dizili disk >1 TB olan birim | Yes | Yes
Depolama Alanları | Hayır | Hayır
Dinamik disk Ekle/Kaldır | Hayır | Hayır
Diski hariç tutma | Yes | Yes
Çoklu yol (MPIO) | Yes | Yes

## <a name="azure-storage"></a>Azure Depolama

**Bileşen** | **Virtual Machine Manager ile Hyper-V** | **Virtual Machine Manager olmadan Hyper-V**
--- | --- | ---
Yerel olarak yedekli depolama | Yes | Yes
Coğrafi olarak yedekli depolama | Yes | Yes
Okuma erişimli coğrafi olarak yedekli depolama | Yes | Yes
Alanlar arası yedekli depolama | Hayır | Hayır
Seyrek Erişimli Depolama | Hayır | Hayır
Sık erişimli depolama| Hayır | Hayır
Blok blobları | Hayır | Hayır
Bekleyen şifreleme (SSE)| Yes | Yes
Bekleyen şifreleme (CMK) <br></br> (Yalnızca yönetilen disklere yük devretme için)| Evet (PowerShell aracılığıyla az 3.3.0 Module onödüller) | Evet (PowerShell aracılığıyla az 3.3.0 Module onödüller)
Rest 'te Çift şifreleme <br></br> (Yalnızca yönetilen disklere yük devretme için) <br></br> [Windows](../virtual-machines/disk-encryption.md) ve [Linux](../virtual-machines/disk-encryption.md) için desteklenen bölgeler hakkında daha fazla bilgi edinin | Evet (PowerShell aracılığıyla az 3.3.0 Module onödüller) | Evet (PowerShell aracılığıyla az 3.3.0 Module onödüller)
Premium depolama | Yes | Yes
Standart depolama | Yes | Yes
İçeri/dışarı aktarma hizmeti | Hayır | Hayır
Güvenlik Duvarı etkin Azure depolama hesapları | Evet. Hedef depolama ve önbellek için. | Evet. Hedef depolama ve önbellek için.
Depolama hesabını değiştir | Hayır. Hedef Azure depolama hesabı, çoğaltma etkinleştirildikten sonra değiştirilemez. Değişiklik yapmak için, olağanüstü durum kurtarmayı devre dışı bırakıp yeniden etkinleştirin. | Hayır
Güvenli aktarım seçeneği | Yes | Yes


## <a name="azure-compute-features"></a>Azure işlem özellikleri

**Özellik** | **Virtual Machine Manager ile Hyper-V** | **Virtual Machine Manager olmadan Hyper-V**
--- | --- | ---
Kullanılabilirlik kümeleri | Yes | Yes
HUB | Yes | Yes  
Yönetilen diskler | Evet, yük devretme için.<br/><br/> Yönetilen disklerin yeniden çalışma işlemi desteklenmiyor. | Evet, yük devretme için.<br/><br/> Yönetilen disklerin yeniden çalışma işlemi desteklenmiyor.

## <a name="azure-vm-requirements"></a>Azure VM gereksinimleri

Azure 'a çoğaltılan şirket içi VM 'Lerin bu tabloda özetlenen Azure VM gereksinimlerini karşılaması gerekir.

**Bileşen** | **Gereksinimler** | **Ayrıntılar**
--- | --- | ---
Konuk işletim sistemi | Site Recovery, [Azure tarafından desteklenen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10))tüm işletim sistemlerini destekler.  | Desteklenmiyorsa önkoşul denetimi başarısız olur.
Konuk işletim sistemi mimarisi | 32-bit (Windows Server 2008)/64-bit | Desteklenmiyorsa önkoşul denetimi başarısız olur.
İşletim sistemi disk boyutu | 1. nesil VM 'Ler için 2.048 GB 'a kadar.<br/><br/> 2. nesil VM 'Ler için 300 GB 'a kadar.  | Desteklenmiyorsa önkoşul denetimi başarısız olur.
İşletim sistemi disk sayısı | 1 | Desteklenmiyorsa önkoşul denetimi başarısız olur.
Veri diski sayısı | 16 veya daha az  | Desteklenmiyorsa önkoşul denetimi başarısız olur.
Veri diski VHD boyutu | 4.095 GB 'a kadar | Desteklenmiyorsa önkoşul denetimi başarısız olur.
Ağ bağdaştırıcıları | Birden çok bağdaştırıcı desteklenir |
Paylaşılan VHD | Desteklenmez | Desteklenmiyorsa önkoşul denetimi başarısız olur.
FC diski | Desteklenmez | Desteklenmiyorsa önkoşul denetimi başarısız olur.
Sabit disk biçimi | VHD <br/><br/> IÇERMEDIĞI | Site Recovery Azure 'a yük devretmek için VHDX 'i otomatik olarak VHD 'ye dönüştürür. Şirket içinde yeniden oturum açtığınızda, sanal makineler VHDX biçimini kullanmaya devam eder.
BitLocker | Desteklenmez | Bir VM için çoğaltmayı etkinleştirmeden önce BitLocker devre dışı bırakılmalıdır.
VM adı | 1-63 karakter. Harfler, sayılar ve kısa çizgilerden oluşabilir. VM adı bir harf veya sayıyla başlamalı ve bitmelidir. | Site Recovery içindeki VM özelliklerindeki değeri güncelleştirin.
VM türü | 1. nesil<br/><br/> 2. nesil--Windows | Temel bir işletim sistemi disk türü (VHDX olarak biçimlendirilen bir veya iki veri birimi içerir) ve 300 GB 'den az disk alanının kullanıldığı 2. nesil VM 'Ler desteklenir.<br></br>Linux 2. nesil VM 'Ler desteklenmez. [Daha fazla bilgi edinin](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Kurtarma Hizmetleri Kasası eylemleri

**Eylem** |  **VMM ile Hyper-V** | **VMM olmadan Hyper-V**
--- | --- | ---
Kasayı kaynak grupları arasında taşıma<br/><br/> Abonelikler içinde ve genelinde | Hayır | Hayır
Depolama, ağ ve Azure VM 'lerini kaynak grupları arasında taşıma<br/><br/> Abonelikler içinde ve genelinde | Hayır | Hayır

> [!NOTE]
> Şirket içinden Azure 'a Hyper-VMs çoğaltma yaparken, Hyper-V sitesi veya Hyper-V ' d e belirli bir ortamdan yalnızca bir AD kiracıya uygun şekilde çoğaltma yapabilirsiniz.


## <a name="provider-and-agent"></a>Sağlayıcı ve aracı

Dağıtımınızın bu makaledeki ayarlarla uyumlu olduğundan emin olmak için en son sağlayıcı ve aracı sürümlerini çalıştırdığınızdan emin olun.

**Ad** | **Açıklama** | **Ayrıntılar**
--- | --- | --- 
Azure Site Recovery sağlayıcı | Şirket içi sunucular ile Azure arasındaki iletişimleri düzenler <br/><br/> Virtual Machine Manager ile Hyper-V: Virtual Machine Manager sunucularına yüklendi<br/><br/> Virtual Machine Manager olmadan Hyper-V: Hyper-V konaklarında yüklü| En son sürüm: 5.1.2700.1 (Azure portal kullanılabilir)<br/><br/> [En son özellikler ve düzeltmeler](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Kurtarma Hizmetleri aracısı | Hyper-V VM 'Leri ve Azure arasında çoğaltmayı düzenler<br/><br/> Şirket içi Hyper-V sunucularında yüklü (Virtual Machine Manager sahip veya olmayan) | Portalda kullanılabilir en son aracı






## <a name="next-steps"></a>Sonraki adımlar
Şirket içi Hyper-V VM 'lerinin olağanüstü durum kurtarması için Azure 'u nasıl [hazırlayacağınızı](tutorial-prepare-azure.md) öğrenin.
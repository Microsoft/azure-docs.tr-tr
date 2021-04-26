---
title: Azure Kaynak taşıyıcısı ile Azure VM 'lerini başka bir bölgeye taşımak için destek matrisi
description: Azure Kaynak taşıyıcısı ile bölgeler arasında Azure VM 'Leri taşıma desteğini gözden geçirin.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 3022b2d4954ffaef71e17ed28dd9b6f141d4da70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99980499"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Azure bölgeleri arasında Azure VM 'Leri taşıma desteği

Bu makalede, kaynak taşıyıcısı kullanarak Azure bölgelerindeki sanal makineleri ve ilgili ağ kaynaklarını taşırken destek ve Önkoşullar özetlenmektedir.

> [!IMPORTANT]
> Azure Kaynak taşıyıcısı Şu anda önizleme aşamasındadır.


## <a name="windows-vm-support"></a>Windows VM desteği

Kaynak taşıyıcısı, bu Windows işletim sistemlerini çalıştıran Azure sanal makinelerini destekler.

**İşletim sistemi** | **Ayrıntılar**
--- | ---
Windows Server 2019 | Sunucu çekirdeği için desteklenir, masaüstü deneyimi olan sunucu.
Windows Server 2016  | Desteklenen sunucu çekirdeği, masaüstü deneyimi ile sunucu.
Windows Server 2012 R2 | Destekleniyor.
Windows Server 2012 | Destekleniyor.
SP1/SP2 ile Windows Server 2008 R2 | Destekleniyor.<br/><br/> SP1/SP2 ile Windows Server 2008 R2 çalıştıran makinelerde, bir Windows [bakım yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4490628) ve [SHA-2 güncelleştirmesi](https://support.microsoft.com/help/4474419)yüklemeniz gerekir.  SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez. [SHA-2 yükseltme ve gereksinimleri](https://aka.ms/SHA-2KB)hakkında daha fazla bilgi edinin.
Windows 10 (x64) | Destekleniyor.
Windows 8.1 (x64) | Destekleniyor.
Windows 8 (x64) | Destekleniyor.
SP1 ile Windows 7 (x64) sürümleri | SP1 ile Windows 7 çalıştıran makinelerde bir Windows [bakım yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4490628) ve [SHA-2 güncelleştirmesi](https://support.microsoft.com/help/4474419) yükler.  SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse ' hazırla ' adımı başarılı olmayacaktır. [SHA-2 yükseltme ve gereksinimleri](https://aka.ms/SHA-2KB)hakkında daha fazla bilgi edinin.


## <a name="linux-vm-support"></a>Linux VM desteği

Kaynak taşıma, bu Linux işletim sistemlerini çalıştıran Azure sanal makinelerini destekler.

**İşletim sistemi** | **Ayrıntılar**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0, 8,1
Ubuntu 14,04 LTS sunucusu | [Desteklenen çekirdek sürümleri](#supported-ubuntu-kernel-versions)
Ubuntu 16,04 LTS sunucusu | [Desteklenen çekirdek sürümü](#supported-ubuntu-kernel-versions)<br/><br/> Parola tabanlı kimlik doğrulaması ve oturum açma kullanan Ubuntu sunucuları ve bulut VM 'Leri yapılandırmak için Cloud-init paketi, yük devretmede (Cloud-init yapılandırmasına bağlı olarak) parola tabanlı oturum açma devre dışı bırakılabilir. Parola tabanlı oturum açma, Azure portal > sorun giderme > ayarları menüsündeki (yük devredilen VM 'nin) parolasını sıfırlayarak sanal makinede yeniden etkinleştirilebilir.
Ubuntu 18,04 LTS sunucusu | [Desteklenen çekirdek sürümü](#supported-ubuntu-kernel-versions).
Deyi 7 | [Desteklenen çekirdek sürümleri](#supported-debian-kernel-versions).
Debian 8 | [Desteklenen çekirdek sürümleri](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [Desteklenen çekirdek sürümleri](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 ve 15 SP1. [(Desteklenen çekirdek sürümleri)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | MADAN
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Red Hat uyumlu çekirdek veya ayırıcı kurumsal çekirdek sürümü 3, 4 & 5 (UEK3, UEK4, UEK5) çalıştırılıyor


### <a name="supported-ubuntu-kernel-versions"></a>Desteklenen Ubuntu çekirdek sürümleri

**Sürüm** | **Çekirdek sürümü** 
--- | --- 
14,04 LTS |  3.13.0-24-genel-3.13.0-170-Generic,<br/>3.16.0-25-Genel-3.16.0-77-Generic,<br/>3.19.0-18-Generic ila 3.19.0-80-Generic,<br/>4.2.0-18-Generic ila 4.2.0-42-Generic,<br/>4.4.0-21-Generic-4.4.0-148-Generic,<br/>4.15.0-1023-Azure to 4.15.0-1045-Azure 
16,04 LTS |  4.4.0-21-Generic-4.4.0-171-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-74-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1066-Azure
18,04 LTS | 4.15.0-20-Genel-4.15.0-74-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-37-Generic </br> 5.3.0-19-Generic-5.3.0-24-genel </br> 4.15.0-1009-Azure-4.15.0-1037-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1028-Azure </br> 5.3.0-1007-Azure-5.3.0-1009-Azure


### <a name="supported-debian-kernel-versions"></a>Desteklenen de, çekirdek sürümleri 

**Sürüm** |  **Çekirdek sürümü** 
--- |  --- 
Deyi 7 |  3.2.0-4-AMD64-3.2.0-6-AMD64, 3.16.0 -0. BPO. 4-AMD64 
Debian 8 |  3.16.0-4-AMD64-3.16.0-10-AMD64, 4.9.0 -0. BPO. 4-AMD64 ila 4.9.0 -0. BPO. 11-AMD64 
Debian 8 |  3.16.0-4-AMD64-3.16.0-10-AMD64, 4.9.0 -0. BPO. 4-AMD64 ila 4.9.0 -0. BPO. 9-AMD64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>Desteklenen SUSE Linux Enterprise Server 12 çekirdek sürümü 

**Sürüm** | **Çekirdek sürümü** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  Tüm [hisse SENEDI SUSE 12 SP1, SP2, SP3, SP4 çekirdekleri](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.34-Azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>Desteklenen SUSE Linux Enterprise Server 15 çekirdek sürümü

**Sürüm** | **Çekirdek sürümü** |
--- |  --- |
SUSE Linux Enterprise Server 15 ve 15 SP1 |  Tüm hisse senedi SUSE 15 ve 15 çekirdekler desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-8.22-Azure |

## <a name="supported-linux-file-systemguest-storage"></a>Desteklenen Linux dosya sistemi/Konuk depolaması

* Dosya sistemleri: ext3, ext4, XFS, BTRFS
* Birim Yöneticisi: LVM2
* Çok yollu yazılım: cihaz Eşleyici


## <a name="supported-vm-compute-settings"></a>Desteklenen VM işlem ayarları

**Ayar** | **Destek** | **Ayrıntılar**
--- | --- | ---
Boyut | En az iki CPU çekirdeği ve 1 GB RAM içeren herhangi bir Azure VM boyutu | [Azure sanal makine boyutlarını](../virtual-machines/sizes-general.md)doğrulayın.
Kullanılabilirlik kümeleri | Desteklenir | Destekleniyor.
Kullanılabilirlik alanları | Desteklenir | Hedef bölge desteğine bağlı olarak desteklenir.
Azure galeri görüntüleri (Microsoft tarafından yayımlanan) | Desteklenir | VM desteklenen bir işletim sisteminde çalışıyorsa desteklenir.
Azure galeri görüntüleri (üçüncü taraf tarafından yayımlanan)  | Desteklenir | VM desteklenen bir işletim sisteminde çalışıyorsa desteklenir.
Özel görüntüler (üçüncü taraf tarafından yayımlanan)| Desteklenir | VM desteklenen bir işletim sisteminde çalışıyorsa desteklenir.
Site Recovery kullanan VM 'Ler | Desteklenmez | Arka uçta Site Recovery kullanarak kaynakları VM 'Ler arasında taşıyın. Zaten Site Recovery kullanıyorsanız, çoğaltmayı devre dışı bırakın ve ardından hazırlama işlemini başlatın.
Azure RBAC ilkeleri | Desteklenmez | VM 'lerde Azure rol tabanlı erişim denetimi (Azure RBAC) ilkeleri, hedef bölgedeki sanal makineye kopyalanmaz.
Uzantıları | Desteklenmez | Uzantılar hedef bölgedeki sanal makineye kopyalanmaz. Taşıma işlemi tamamlandıktan sonra bunları el ile yükleyebilirsiniz.


## <a name="supported-vm-storage-settings"></a>Desteklenen VM depolama ayarları

Bu tabloda, Azure VM işletim sistemi diski, veri diski ve geçici disk için destek özetlenmektedir. Performans sorunlarından kaçınmak için, [yönetilen DISKLERIN](../virtual-machines/disks-scalability-targets.md) VM disk sınırlarını ve hedeflerini gözlemlemek önemlidir.

> [!NOTE]
> Hedef VM boyutu, kaynak VM 'den büyük veya bu değere eşit olmalıdır. Doğrulama için kullanılan parametreler: veri diskleri sayısı, NIC sayısı, kullanılabilir CPU 'Lar, GB cinsinden bellek. Bir hata verilir.


**Bileşen** | **Destek** | **Ayrıntılar**
--- | --- | ---
İşletim sistemi diski en büyük boyutu | 2048 GB | VM diskleri hakkında [daha fazla bilgi edinin](../virtual-machines/managed-disks-overview.md) .
Geçici disk | Desteklenmez | Geçici disk her zaman hazırlama işleminden çıkarılır.<br/><br/> Kalıcı verileri geçici diskte depolamamayın. [Daha fazla bilgi edinin](../virtual-machines/managed-disks-overview.md#temporary-disk).
Veri diski en büyük boyutu | yönetilen diskler için 8192 GB
Veri diski en küçük boyutu |  yönetilen diskler için 2 GB |
Veri diski en fazla sayı | Belirli bir Azure VM boyutu için desteğe uygun olarak 64 'e kadar | VM boyutları hakkında [daha fazla bilgi edinin](../virtual-machines/sizes.md) .
Veri diski değişim oranı | Premium Depolama için disk başına en fazla 10 MBps. Standart depolama için disk başına en fazla 2 MBps. | Disk üzerindeki ortalama veri değişim oranı, en yüksek değerden sürekli yüksek ise hazırlık işlemi gerçekleştirilmez.<br/><br/>  Ancak, en yüksek değer, tek tek aşılırsa, hazırlık yakalayabilir, ancak biraz gecikmeli kurtarma noktası görebilirsiniz.
Veri diski (Standart depolama hesabı) | Desteklenmez. | Depolama türünü yönetilen disk olarak değiştirip VM 'yi taşımayı deneyin.
Veri diski (Premium depolama hesabı) | Desteklenmez | Depolama türünü yönetilen disk olarak değiştirip VM 'yi taşımayı deneyin.
Yönetilen disk (Standart) | Desteklenir  |
Yönetilen disk (Premium) | Desteklenir |
Standart SSD | Desteklenir |
2. nesil (UEFı önyüklemesi) | Desteklenir
Önyükleme tanılama depolama hesabı | Desteklenmez | VM 'yi hedef bölgeye taşıdıktan sonra yeniden etkinleştirin.
Azure Disk Şifrelemesi etkin VM 'Ler | Desteklenir | [Daha fazla bilgi edinin](tutorial-move-region-encrypted-virtual-machines.md)
Müşteri tarafından yönetilen anahtarla sunucu tarafı şifrelemeyi kullanan VM 'Ler | Desteklenir | [Daha fazla bilgi edinin](tutorial-move-region-encrypted-virtual-machines.md)

### <a name="limits-and-data-change-rates"></a>Sınırlar ve veri değişim oranları

Aşağıdaki tabloda, testlerimizi temel alan sınırlar özetlenmektedir. Bu, olası tüm uygulama g/ç birleşimlerini kapsamamaktadır. Gerçek sonuçlar, uygulamanızın g/ç karışımına göre farklılık gösterir. Göz önünde bulundurulması gereken iki sınır vardır, disk başına veri dalgalanması ve VM veri karmaşası başına.

**Depolama hedefi** | **Ortalama kaynak disk g/ç** |**Ortalama kaynak disk veri değişim sıklığı** | **Gün başına toplam kaynak disk veri değişim sıklığı**
---|---|---|---
Standart depolama | 8 KB    | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 8 KB    | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 16 KB | 4 MB/sn |    Disk başına 336 GB
Premium P10 veya P15 disk | 32 KB veya daha büyük | 8 MB/sn | Disk başına 672 GB
Premium P20 veya P30 veya P40 veya P50 disk | 8 KB    | 5 MB/sn | Disk başına 421 GB
Premium P20 veya P30 veya P40 veya P50 disk | 16 KB veya daha büyük |20 MB/sn | disk başına 1684 GB

## <a name="supported-vm-networking-settings"></a>Desteklenen VM ağ ayarları

**Ayar** | **Destek** | **Ayrıntılar**
--- | --- | ---
NIC | Desteklenir | Hedef bölgede var olan bir kaynağı belirtin veya hazırlama işlemi sırasında yeni bir kaynak oluşturun. 
İç yük dengeleyici | Desteklenir | Hedef bölgede var olan bir kaynağı belirtin veya hazırlama işlemi sırasında yeni bir kaynak oluşturun.  
Genel yük dengeleyici | Desteklenir | Hedef bölgede var olan bir kaynağı belirtin veya hazırlama işlemi sırasında yeni bir kaynak oluşturun.  
Genel IP adresi | Desteklenir | Hedef bölgede var olan bir kaynağı belirtin veya hazırlama işlemi sırasında yeni bir kaynak oluşturun.<br/><br/> Genel IP adresi bölgeye özeldir ve taşıma işleminden sonra hedef bölgede korunmaz. Hedef konumdaki ağ ayarlarını (Yük Dengeleme kuralları dahil) değiştirirken bunu aklınızda bulundurun.
Ağ güvenlik grubu | Desteklenir | Hedef bölgede var olan bir kaynağı belirtin veya hazırlama işlemi sırasında yeni bir kaynak oluşturun.  
Ayrılmış (statik) IP adresi | Desteklenir | Bunu şu anda yapılandıramazsınız. Değer varsayılan olarak kaynak değeridir. <br/><br/> Kaynak VM üzerindeki NIC 'nin statik bir IP adresi varsa ve hedef alt ağda aynı IP adresi varsa, hedef VM 'ye atanır.<br/><br/> Hedef alt ağda aynı IP adresi yoksa, sanal makine için taşıma işlemi başarısız olur.
Dinamik IP adresi | Desteklenir | Bunu şu anda yapılandıramazsınız. Değer varsayılan olarak kaynak değeridir.<br/><br/> Kaynaktaki NIC dinamik IP adreslemesini içeriyorsa, hedef VM üzerindeki NIC de varsayılan olarak dinamik olur.
IP yapılandırması | Desteklenir | Bunu şu anda yapılandıramazsınız. Değer varsayılan olarak kaynak değeridir.

## <a name="outbound-access-requirements"></a>Giden erişim gereksinimleri

Taşımak istediğiniz Azure VM 'lerinin giden erişimi olması gerekir.


### <a name="url-access"></a>URL erişimi

 Giden bağlantıyı denetlemek için URL tabanlı bir güvenlik duvarı proxy 'si kullanıyorsanız, bu URL 'Lere erişim izni verin:

**Ad** | **Azure genel bulutu** | **Ayrıntılar** 
--- | --- | --- 
Depolama | `*.blob.core.windows.net`  | Verilerin VM’den kaynak bölgedeki önbellek depolama hesabına yazılmasına izin verir. 
Azure Active Directory | `login.microsoftonline.com`  | Site Recovery hizmet URL’leri için yetkilendirme ve kimlik doğrulama özellikleri sağlar. 
Çoğaltma | `*.hypervrecoverymanager.windowsazure.com` | VM’nin Site Recovery hizmetiyle iletişim kurmasına izin verir. 
Service Bus | `*.servicebus.windows.net` | VM’nin Site Recovery izleme ve tanılama verilerini yazmasına izin verir. 

## <a name="nsg-rules"></a>NSG kuralları
Giden bağlantıyı denetlemek için bir ağ güvenlik grubu (NSG) kuralları kullanıyorsanız, bu [hizmet etiketi](../virtual-network/service-tags-overview.md) kurallarını oluşturun. Her kural HTTPS (443) üzerinde giden erişime izin verilmelidir.
- Kaynak bölge için bir depolama etiketi kuralı oluşturun.
- Herhangi bir bölgedeki Site Recovery hizmetine erişime izin vermek için bir *Azuresterecovery* etiketi kuralı oluşturun. Bu etikette bu etiketlerin bağımlılıkları bulunur, bu nedenle şunlar için kurallar oluşturmanız gerekir:
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- Test kurallarını üretim dışı bir ortamda test etmenizi öneririz. [Bazı örnekleri inceleyin](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags). 


## <a name="next-steps"></a>Sonraki adımlar

Kaynak taşıyıcısı ile bir [Azure VM](tutorial-move-region-virtual-machines.md) 'yi başka bir bölgeye taşımayı deneyin.

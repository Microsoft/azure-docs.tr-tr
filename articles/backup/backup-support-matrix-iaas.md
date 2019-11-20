---
title: Azure VM yedeklemesi için destek matrisi
description: Azure Backup hizmeti ile Azure VM 'lerini yedeklerken destek ayarlarının ve sınırlamaların özetini sağlar.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 8f84d7fefd2affc3a3c47227ab6f2a2d0b325f4e
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172082"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM yedeklemesi için destek matrisi

[Azure Backup hizmetini](backup-overview.md) şirket içi makineleri ve iş yüklerini ve Azure sanal makinelerini (VM) yedeklemek için kullanabilirsiniz. Bu makalede, Azure Backup ile Azure VM 'Leri yedeklerken destek ayarları ve sınırlamaları özetlenmektedir.

Diğer destek matrisleri:

- Azure Backup için [genel destek matrisi](backup-support-matrix.md)
- Azure Backup Server/System Center Data Protection Manager (DPM) yedeklemesi için [destek matrisi](backup-support-matrix-mabs-dpm.md)
- Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı ile yedekleme için [destek matrisi](backup-support-matrix-mars-agent.md)

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Azure Backup hizmeti ile Azure VM 'lerini yedekleme ve geri yükleme işlemlerinin nasıl yapılacağını aşağıda bulabilirsiniz.

**Senaryo** | **Backup** | **Aracı** |**Geri yükleme**
--- | --- | --- | ---
Azure VM 'lerinin doğrudan yedeklemesi  | Tüm VM 'yi yedekleyin.  | Azure VM 'de aracı gerekmez. Azure Backup, VM üzerinde çalışan [Azure VM aracısına](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) bir uzantı yükleyip kullanır. | Aşağıdaki şekilde geri yükleyin:<br/><br/> - **temel BIR VM oluşturun**. Bu, VM 'nin birden çok IP adresi gibi özel bir yapılandırması yoksa yararlıdır.<br/><br/> **VM diskini geri**- . Diski geri yükleyin. Daha sonra mevcut bir VM 'ye bağlayın veya PowerShell 'i kullanarak diskten yeni bir VM oluşturun.<br/><br/> **VM diski - değiştirin**. Bir VM varsa ve yönetilen diskler (şifrelenmemiş) kullanıyorsa, bir diski geri yükleyebilir ve sanal makinede var olan bir diski değiştirmek için kullanabilirsiniz.<br/><br/> **belirli dosyaları/klasörleri geri yüklemek**- . Dosyaları/klasörleri VM 'nin tamamı yerine bir VM 'den geri yükleyebilirsiniz.
Azure VM 'lerinin doğrudan yedeklemesi (yalnızca Windows)  | Belirli dosyaları/klasörleri/birimleri yedekleyin. | [Azure kurtarma hizmetleri Aracısı](backup-azure-file-folder-backup-faq.md)'nı yükler.<br/><br/> VM 'yi dosya/klasör düzeyinde yedeklemek için, Azure VM aracısının yedekleme uzantısı ile birlikte MARS aracısını çalıştırabilirsiniz. | Belirli klasörleri/dosyaları geri yükleyin.
Azure VM 'yi yedekleme sunucusuna yedekleme  | Dosya/klasör/birim yedekleme; sistem durumu/çıplak dosyalar; uygulama verilerini System Center DPM 'ye veya Microsoft Azure Backup sunucusuna (MABS).<br/><br/> DPM/MABS daha sonra yedekleme kasasına yedekler. | DPM/MABS koruma aracısını VM 'ye yükler. MARS Aracısı DPM/MABS üzerine yüklenir.| Dosyaları/klasörleri/birimleri geri yükleme sistem durumu/çıplak dosyalar; uygulama verileri.

[Yedekleme sunucusu](backup-architecture.md#architecture-back-up-to-dpmmabs) ve [destek gereksinimleri](backup-support-matrix-mabs-dpm.md)hakkında daha fazla bilgi edinin.

## <a name="supported-backup-actions"></a>Desteklenen yedekleme eylemleri

**Eylem** | **Destek**
--- | ---
Windows Azure VM oluştururken yedeklemeyi etkinleştirme | İçin desteklenir: <br/><br/> -Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> -Windows Server 2008 R2 (RTM ve SP1 standart)
Linux VM oluştururken yedeklemeyi etkinleştirme | İçin desteklenir:<br/><br/> -Ubuntu Server: 18,04, 17,10, 17,04, 16,04 (LTS), 14,04 (LTS)<br/><br/> -Red Hat: RHEL 6,7, 6,8, 6,9, 7,2, 7,3, 7,4<br/><br/> -SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> -Detem: 8, 9<br/><br/> -CentOS: 6,9, 7,3<br/><br/> -Oracle Linux: 6,7, 6,8, 6,9, 7,2, 7,3
Kapalı/çevrimdışı VM olan bir VM 'yi yedekleme | Destekleniyor.<br/><br/> Anlık görüntü yalnızca çökme ile tutarlı, uygulamayla tutarlı değildir.
Yönetilen disklere geçtikten sonra diskleri yedekleme | Destekleniyor.<br/><br/> Yedekleme çalışmaya devam edecektir. İşlem yapmanız gerekmez.
Kaynak grubu kilidini etkinleştirdikten sonra yönetilen diskleri yedekleme | Desteklenmiyor.<br/><br/> Azure Backup eski geri yükleme noktalarını silemiyor ve en fazla geri yükleme noktası sınırına ulaşıldığında yedeklemeler başarısız olur.
VM için yedekleme ilkesini değiştirme | Destekleniyor.<br/><br/> VM, yeni ilkedeki zamanlama ve bekletme ayarları kullanılarak yedeklenir. Bekletme ayarları genişletilmişse, var olan kurtarma noktaları işaretlenir ve saklanır. Azaldıysanız, var olan kurtarma noktaları sonraki temizleme işinde ayıklanır ve sonunda silinir.
Bir yedekleme işini iptal et| Anlık görüntü işlemi sırasında desteklenir.<br/><br/> Anlık görüntü kasaya aktarıldığında desteklenmez.
VM 'yi farklı bir bölgeye veya aboneliğe yedekleme |Desteklenmiyor.
Gün başına yedeklemeler (Azure VM Uzantısı aracılığıyla) | Gün başına bir zamanlanmış yedekleme.<br/><br/> Hizmet günde en fazla dokuz isteğe bağlı yedekleme olanağı sağlar ancak Microsoft yalnızca en fazla dört günlük isteğe bağlı yedeklemeyi önerir ve destekler.
Gün başına yedeklemeler (MARS Aracısı aracılığıyla) | Gün başına üç zamanlanmış yedekleme.
Gün başına yedeklemeler (DPM/MABS aracılığıyla) | Gün başına iki zamanlanmış yedekleme.
Aylık/yıllık yedekleme| Azure VM Uzantısı ile yedeklenirken desteklenmez. Yalnızca günlük ve haftalık desteklenir.<br/><br/> İlkeyi aylık/yıllık Bekletme dönemi için günlük/haftalık yedeklemeleri koruyacak şekilde ayarlayabilirsiniz.
Otomatik saat ayarlaması | Desteklenmiyor.<br/><br/> Azure Backup, bir VM yedeklenirken gün ışığından yararlanma saati değişikliklerini otomatik olarak ayarlamaz.<br/><br/>  İlkeyi gerektiği şekilde el ile değiştirin.
[Karma yedekleme için güvenlik özellikleri](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Güvenlik özelliklerini devre dışı bırakma desteklenmiyor.
Makine süresi değişmiş olan VM 'yi yedekleme | Desteklenmiyor.<br/><br/> Makine saati, bu VM için yedeklemeyi etkinleştirdikten sonra gelecek tarih-saat olarak değiştirilirse; Ancak, zaman değişikliği geri döndürülse bile başarılı yedekleme garanti edilmez.  

## <a name="operating-system-support-windows"></a>İşletim sistemi desteği (Windows)

Aşağıdaki tabloda, Windows Azure VM 'Leri yedeklenirken desteklenen işletim sistemleri özetlenmektedir.

**Senaryo** | **İşletim sistemi desteği**
--- | ---
Azure VM Aracısı Uzantısı ile yedekleme | -Windows 10 Istemcisi (yalnızca 64 bit) <br/><br/>-Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> -Windows Server 2008 R2 (RTM ve SP1 standart)
MARS Aracısı ile yedekleme | [Desteklenen](backup-support-matrix-mars-agent.md#support-for-direct-backups) işletim sistemleri.
DPM/MABS ile yedekleme | [Mabs](backup-mabs-protection-matrix.md) ve [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807)ile yedekleme için desteklenen işletim sistemleri.

Azure Backup, 32 bitlik işletim sistemlerini desteklemez.

## <a name="support-for-linux-backup"></a>Linux yedekleme desteği

Linux makinelerini yedeklemek istiyorsanız, bu desteklenir.

**Eylem** | **Destek**
--- | ---
Linux Azure VM aracısıyla Linux Azure VM 'lerini yedekleme | Dosya tutarlı yedekleme.<br/><br/> [Özel betikler](backup-azure-linux-app-consistent.md)kullanılarak uygulamayla tutarlı yedekleme.<br/><br/> Geri yükleme sırasında yeni bir sanal makine oluşturabilir, bir diski geri yükleyebilir ve bir VM oluşturmak veya bir diski geri yüklemek için kullanabilir ve bunu kullanarak var olan bir VM 'deki bir diski değiştirebilirsiniz. Tek tek dosyaları ve klasörleri de geri yükleyebilirsiniz.
MARS Aracısı ile Linux Azure VM 'lerini yedekleme | Desteklenmiyor.<br/><br/> MARS Aracısı yalnızca Windows makinelere yüklenebilir.
DPM/MABS ile Linux Azure VM 'lerini yedekleme | Desteklenmiyor.

## <a name="operating-system-support-linux"></a>İşletim sistemi desteği (Linux)

Azure VM Linux yedeklemeleri için Azure Backup, [Azure tarafından](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)onaylanan Linux dağıtımları listesini destekler. Şunlara dikkat edin:

- Azure Backup, çekirdek işletim sistemi Linux 'u desteklemez.
- Azure Backup, 32 bitlik işletim sistemlerini desteklemez.
- Diğer kendi Linux dağıtımları, [Linux Için Azure VM ARACıSı](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) VM 'de kullanılabildiği ve Python desteklendiği sürece çalışan Linux dağıtımlarınız çalışabilir.
- Azure Backup, Python sürüm 2,7 yüklü değilse proxy tarafından yapılandırılan bir Linux VM 'yi desteklemez.

## <a name="backup-frequency-and-retention"></a>Yedekleme sıklığı ve bekletme

**Ayar** | **Limitler**
--- | ---
Korumalı örnek başına en fazla kurtarma noktası (makine/iş yükü) | 9999.
Kurtarma noktası için en uzun süre sonu zamanı | Sınır yok.
Kasaya maksimum yedekleme sıklığı (Azure VM uzantısı) | Günde bir kez.
Maksimum yedekleme sıklığı (MARS Aracısı) | Gün başına üç yedekleme.
DPM/MABS için maksimum yedekleme sıklığı | SQL Server için her 15 dakikada bir.<br/><br/> Diğer iş yükleri için saatte bir.
Kurtarma noktası bekletme | Günlük, haftalık, aylık ve yıllık.
En uzun bekletme süresi | Yedekleme sıklığına bağlıdır.
DPM/MABS diskindeki kurtarma noktaları | dosya sunucuları için 64 ve uygulama sunucuları için 448.<br/><br/> Bant kurtarma noktaları, şirket içi DPM için sınırsızdır.

## <a name="supported-restore-methods"></a>Desteklenen geri yükleme yöntemleri

**Restore yöntemi** | **Ayrıntılar**
--- | ---
Yeni bir VM oluşturun | Geri yükleme işlemi sırasında bir VM oluşturabilirsiniz. <br/><br/> Bu seçenek, temel bir VM 'yi çalışır duruma getirir. VM adı, kaynak grubu, sanal ağ, alt ağ ve depolama alanını belirtebilirsiniz.  
Diski geri yükleme | Bir diski geri yükleyebilir ve bir VM oluşturmak için kullanabilirsiniz.<br/><br/> Bu seçeneği belirlediğinizde, verileri kasadan seçtiğiniz bir depolama hesabına kopyalar Azure Backup. Geri yükleme işi bir şablon oluşturur. Bu şablonu indirebilir, özel VM ayarlarını belirtmek ve bir VM oluşturmak için kullanabilirsiniz.<br/><br/> Bu seçenek, önceki bir VM oluşturma seçeneğine daha fazla ayar belirtmenize olanak tanır.<br/><br/>
Var olan bir diski değiştirme | Bir diski geri yükleyebilir ve ardından bir sanal makinede bulunan bir diski değiştirmek için geri yüklenen diski kullanabilirsiniz.
Dosyaları geri yükle | Dosyaları seçili bir kurtarma noktasından kurtarabilirsiniz. VM diskini kurtarma noktasından bağlamak için bir komut dosyası indirirler. Daha sonra, kurtarmak istediğiniz dosyaları/klasörleri bulmak ve işiniz bittiğinde diski çıkarmak için disk birimlerine göz atabilirsiniz.

## <a name="support-for-file-level-restore"></a>Dosya düzeyinde geri yükleme desteği

**Geri yükleme** | **Destekleniyor**
--- | ---
İşletim sistemleri arasında dosyaları geri yükleme | Yedeklenen VM ile aynı (veya uyumlu) işletim sistemine sahip herhangi bir makinedeki dosyaları geri yükleyebilirsiniz. Bkz. [uyumlu işletim sistemi tablosu](backup-azure-restore-files-from-vm.md#system-requirements).
Klasik VM 'lerde dosyaları geri yükleme | Desteklenmiyor.
Şifrelenmiş VM 'lerden dosyaları geri yükleme | Desteklenmiyor.
Ağ kısıtlı depolama hesaplarından dosyaları geri yükleme | Desteklenmiyor.
Windows depolama alanları 'nı kullanarak sanal makinelere dosya geri yükleme | Geri yükleme aynı VM 'de desteklenmiyor.<br/><br/> Bunun yerine, uyumlu bir sanal makinenin dosyalarını geri yükleyin.
LVM/RAID dizilerini kullanarak Linux sanal makinesinde dosyaları geri yükleme | Geri yükleme aynı VM 'de desteklenmiyor.<br/><br/> Uyumlu bir sanal makineye geri yükleme.
Dosyaları özel ağ ayarlarıyla geri yükleme | Geri yükleme aynı VM 'de desteklenmiyor. <br/><br/> Uyumlu bir sanal makineye geri yükleme.

## <a name="support-for-vm-management"></a>VM yönetimi desteği

Aşağıdaki tabloda VM diskleri ekleme veya değiştirme gibi VM yönetim görevleri sırasında yedekleme desteği özetlenmektedir.

**Geri yükleme** | **Destekleniyor**
--- | ---
Abonelik/bölge/bölge genelinde geri yükleme. | Desteklenmiyor.
Mevcut bir VM 'ye geri yükleme | Diski Değiştir seçeneğini kullanın.
Azure Depolama Hizmeti Şifrelemesi için etkinleştirilmiş depolama hesabı ile diski geri yükleme (SSE) | Desteklenmiyor.<br/><br/> SSE özellikli olmayan bir hesaba geri yükleyin.
Karma depolama hesaplarına geri yükleme |Desteklenmiyor.<br/><br/> Depolama hesabı türüne bağlı olarak, tüm geri yüklenen diskler Premium veya standart olur ve karışmaz.
VM 'yi doğrudan bir kullanılabilirlik kümesine geri yükleme | Yönetilen diskler için diski geri yükleyebilir ve şablondaki kullanılabilirlik kümesi seçeneğini kullanabilirsiniz.<br/><br/> Yönetilmeyen diskler için desteklenmez. Yönetilmeyen diskler için, diski geri yükleyin ve ardından kullanılabilirlik kümesinde bir VM oluşturun.
Yönetilen VM 'ye yükselttikten sonra yönetilmeyen VM 'lerin yedeklemesini geri yükleme| Destekleniyor.<br/><br/> Diskleri geri yükleyebilir ve ardından yönetilen bir VM oluşturabilirsiniz.
VM yönetilen disklere geçirilmeden önce VM 'yi geri yükleme noktasına geri yükleme | Destekleniyor.<br/><br/> Yönetilmeyen disklere geri yükleme (varsayılan), geri yüklenen diskleri yönetilen diske Dönüştür ve yönetilen disklerle bir VM oluşturun.
Silinen bir VM 'yi geri yükleyin. | Destekleniyor.<br/><br/> VM 'yi bir kurtarma noktasından geri yükleyebilirsiniz.
Portal aracılığıyla çok bloklu yapılandırmanın parçası olan bir etki alanı denetleyicisi (DC) VM 'sini geri yükleme | Bir VM 'yi geri yükledikten ve PowerShell 'i kullanarak bir VM oluşturduğunuzda desteklenir.
Farklı sanal ağdaki VM 'yi geri yükleme |Destekleniyor.<br/><br/> Sanal ağın aynı abonelikte ve bölgede olması gerekir.

## <a name="vm-compute-support"></a>VM işlem desteği

**İşlem** | **Destek**
--- | ---
VM boyutu |En az 2 CPU çekirdeği ve 1 GB RAM içeren herhangi bir Azure VM boyutu.<br/><br/> [Daha fazla bilgi edinin.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
[Kullanılabilirlik kümelerinde](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) VM 'leri yedekleme | Destekleniyor.<br/><br/> Hızlı bir şekilde VM oluşturma seçeneğini kullanarak kullanılabilir bir küme içindeki bir VM 'yi geri alamazsınız. Bunun yerine, VM 'yi geri yükledikten sonra diski geri yükler ve bir VM dağıtmak ya da bir diski geri yüklemek için kullanın ve var olan bir diski değiştirmek için kullanın.
[Karma kullanım avantajı (hub)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) Ile dağıtılan VM 'leri yedekleme | Destekleniyor.
[Ölçek kümesine](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) dağıtılan VM 'leri yedekleme |Desteklenmiyor.
[Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) 'Nden dağıtılan VM 'leri yedekleme<br/><br/> (Microsoft, üçüncü taraf tarafından yayımlandı) |Destekleniyor.<br/><br/> VM desteklenen bir işletim sistemi çalıştırıyor olmalıdır.<br/><br/> SANAL makinede dosyaları kurtarırken, yalnızca uyumlu bir işletim sistemine (önceki veya sonraki bir işletim sistemini değil) geri yükleyebilirsiniz. VM 'Ler olarak desteklenen Azure Market VM 'lerini geri yüklemeyin, çünkü bu gereksinimler yalnızca disk olarak satın alınabilir.
Özel görüntüden dağıtılan VM 'Leri yedekleme (üçüncü taraf) |Destekleniyor.<br/><br/> VM desteklenen bir işletim sistemi çalıştırıyor olmalıdır.<br/><br/> SANAL makinede dosyaları kurtarırken, yalnızca uyumlu bir işletim sistemine (önceki veya sonraki bir işletim sistemini değil) geri yükleyebilirsiniz.
Azure 'a geçirilen VM 'Leri yedekleme| Destekleniyor.<br/><br/> VM 'yi yedeklemek için, geçirilen makinede VM aracısının yüklü olması gerekir.
Çoklu VM tutarlılığını yedekleme | Azure Backup birden çok VM arasında veri ve uygulama tutarlılığı sağlamaz.
[Tanılama ayarlarıyla](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) yedekleme  | Desteklenen. <br/><br/> Azure VM 'yi tanılama ayarlarıyla geri yükleme [yeni seçenek oluştur](backup-azure-arm-restore-vms.md#create-a-vm) seçeneği kullanılarak tetikleniyorsa geri yükleme başarısız olur.
Bölge ile sabitlenmiş VM 'Leri geri yükleme | Desteklenir (Ocak 2019 ' den sonra yedeklenen VM ve [kullanılabilirlik bölgesi](https://azure.microsoft.com/global-infrastructure/availability-zones/) kullanılabilir).<br/><br/>Şu anda VM 'lerde sabitlenmiş aynı bölgeye geri yüklemeyi destekliyoruz. Ancak bölge kullanılamıyorsa, geri yükleme başarısız olur.
Gen2 VM 'Leri | Destekleniyor <br> Azure Backup, [Gen2 VM](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)'lerinin yedeklenmesini ve geri yüklenmesini destekler. Bu VM 'Ler kurtarma noktasından geri yüklendiğinde, [Gen2 VM](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)olarak geri yüklenir.

## <a name="vm-storage-support"></a>VM depolama desteği

**Bileşen** | **Destek**
--- | ---
Azure VM veri diskleri | 16 veya daha az veri diski olan bir VM 'yi yedekleyin.
Veri diski boyutu | Tek bir disk boyutu 32 TB 'a kadar ve bir VM 'deki tüm diskler için en fazla 256 TB olabilir.
Depolama türü | Standart HDD, Standart SSD Premium SSD.
Yönetilen diskler | Destekleniyor.
Şifrelenmiş diskler | Destekleniyor.<br/><br/> Azure disk şifrelemesi ile etkinleştirilen Azure VM 'Leri yedeklenebilir (Azure AD uygulaması ile veya olmadan).<br/><br/> Şifrelenmiş VM 'Ler dosya/klasör düzeyinde kurtarılamaz. Tüm VM 'yi kurtarmanız gerekir.<br/><br/> Azure Backup tarafından zaten korunan VM 'lerde şifrelemeyi etkinleştirebilirsiniz.
Yazma Hızlandırıcısı etkin olan diskler | Desteklenmiyor.<br/><br/> Azure Backup, yedekleme sırasında Yazma Hızlandırıcısı etkin olan diskleri otomatik olarak dışlar. Yedeklenmediğinden, bu diskleri sanal makinenin kurtarma noktalarından geri yükleyemezsiniz.
Yinelenen yinelenenleri kaldırma & VM 'Leri/diskleri geri yükleme | Azure Backup Yinelenenleri kaldırmayı desteklemez. Daha fazla bilgi için bu [makaleye](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) bakın <br/> <br/>  -Azure Backup kurtarma hizmetleri kasasındaki VM 'lerde yinelenmez <br/> <br/>  -Geri yükleme sırasında yinelenenleri kaldırma durumunda VM 'Ler varsa, kasa biçimi anlayamadığından dosyalar geri yüklenemez
Korumalı VM 'ye disk ekleme | Destekleniyor.
Korumalı VM 'de diski yeniden boyutlandır | Destekleniyor.
Paylaşılan depolama alanı| Küme Paylaşılan Birimi (CSV) veya Genişleme Dosya Sunucusu kullanarak VM 'Lerin yedeklenmesi önerilmez. Yedekleme sırasında CSV yazıcılarının başarısız olma olasılığı yüksektir. Geri yükleme sırasında CSV birimleri içeren diskler gelmeyebilir.

## <a name="vm-network-support"></a>VM ağı desteği

**Bileşen** | **Destek**
--- | ---
Ağ arabirimi sayısı (NIC) | Belirli bir Azure VM boyutu için desteklenen en fazla NIC sayısı.<br/><br/> NIC 'ler, geri yükleme işlemi sırasında VM oluşturulduğunda oluşturulur.<br/><br/> Geri yüklenen VM üzerindeki NIC sayısı, korumayı etkinleştirdiğinizde VM üzerindeki NIC 'lerin sayısını yansıtır. Korumayı etkinleştirdikten sonra NIC 'Lerin kaldırılması sayıyı etkilemez.
Dış/iç yük dengeleyici |Destekleniyor. <br/><br/> Özel ağ ayarlarıyla VM 'Leri geri yükleme hakkında [daha fazla bilgi edinin](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) .
Birden çok ayrılmış IP adresi |Destekleniyor. <br/><br/> Özel ağ ayarlarıyla VM 'Leri geri yükleme hakkında [daha fazla bilgi edinin](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) .
Birden çok ağ bağdaştırıcısı olan VM 'Ler| Destekleniyor. <br/><br/> Özel ağ ayarlarıyla VM 'Leri geri yükleme hakkında [daha fazla bilgi edinin](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) .
Genel IP adreslerine sahip VM 'Ler| Destekleniyor.<br/><br/> Mevcut bir genel IP adresini NIC ile ilişkilendirin veya bir adres oluşturun ve geri yükleme yapıldıktan sonra bu adresi NIC ile ilişkilendirin.
NIC/alt ağ üzerinde ağ güvenlik grubu (NSG). |Destekleniyor.
Statik IP adresi | Desteklenmiyor.<br/><br/> Bir geri yükleme noktasından oluşturulan yeni bir sanal makineye dinamik bir IP adresi atanır.<br/><br/> Klasik VM 'Ler için, ayrılmış bir IP adresi ve tanımlı uç nokta olmayan bir VM 'yi yedekleyemiyoruz.
Dinamik IP adresi |Destekleniyor.<br/><br/> Kaynak VM üzerindeki NIC dinamik IP adresleme kullanıyorsa, varsayılan olarak, geri yüklenen VM üzerindeki NIC de onu kullanır.
Azure Traffic Manager| Destekleniyor.<br/><br/>Yedeklenen VM Traffic Manager, geri yüklenen VM 'yi aynı Traffic Manager örneğine el ile ekleyin.
Azure DNS |Destekleniyor.
Özel DNS |Destekleniyor.
HTTP proxy aracılığıyla giden bağlantı | Destekleniyor.<br/><br/> Kimliği doğrulanmış bir ara sunucu desteklenmiyor.
Sanal ağ hizmet uç noktaları| Destekleniyor.<br/><br/> Güvenlik Duvarı ve sanal ağ depolama hesabı ayarları tüm ağlardan erişime izin verilmelidir.

## <a name="vm-security-and-encryption-support"></a>VM Güvenlik ve şifreleme desteği

Azure Backup, geçiş sırasında ve REST verilerinde şifrelemeyi destekler:

Azure 'a ağ trafiği:

- Sunuculardan kurtarma hizmetleri kasasına yedekleme trafiği Gelişmiş Şifreleme Standardı 256 kullanılarak şifrelenir.
- Yedekleme verileri güvenli bir HTTPS bağlantısı üzerinden gönderilir.
- Yedekleme verileri, kurtarma hizmetleri kasasında şifrelenmiş biçimde depolanır.
- Yalnızca bu verilerin kilidini açmak için bir parola vardır. Microsoft, herhangi bir noktada yedekleme verilerinin şifresini çözemez.

  > [!WARNING]
  > Kasayı ayarladıktan sonra yalnızca şifreleme anahtarına erişiminiz vardır. Microsoft hiçbir şekilde kopya korumaz ve anahtara erişemez. Anahtar yanlış olursa Microsoft, yedekleme verilerini kurtaramaz.

Veri güvenliği:

- Azure VM 'lerini yedeklerken, sanal makine *içinde* şifrelemeyi ayarlamanız gerekir.
- Azure Backup, Linux sanal makinelerinde Windows sanal makinelerinde BitLocker ve US **dm-crypt** kullanan Azure disk şifrelemeyi destekler.
- Azure Backup arka uçta bekleyen verileri koruyan [Azure Depolama Hizmeti şifrelemesini](../storage/common/storage-service-encryption.md) kullanır.

**Makin** | **Yoldaki** | **Bekleyen**
--- | --- | ---
DPM/MABS olmayan şirket içi Windows makineleri | ![Yes][green] | ![Yes][green]
Azure VM’leri | ![Yes][green] | ![Yes][green]
DPM ile şirket içi/Azure VM 'Leri | ![Yes][green] | ![Yes][green]
MABS ile şirket içi/Azure VM 'Leri | ![Yes][green] | ![Yes][green]

## <a name="vm-compression-support"></a>VM sıkıştırma desteği

Yedekleme, aşağıdaki tabloda özetlenen yedekleme trafiğinin sıkıştırmasını destekler. Şunlara dikkat edin:

- Azure VM 'Leri için VM uzantısı, verileri doğrudan depolama ağı üzerinden Azure Storage hesabından okur. Bu trafiği sıkıştırmak gerekli değildir.
- DPM veya MABS kullanıyorsanız, DPM/MABS 'e yedeklenmeden önce verileri sıkıştırarak bant genişliği tasarrufu yapabilirsiniz.

**Makin** | **MABS/DPM 'ye Sıkıştır (TCP)** | **Kasaya Sıkıştır (HTTPS)**
--- | --- | ---
DPM/MABS olmayan şirket içi Windows makineleri | NA | ![Yes][green]
Azure VM’leri | NA | NA
DPM ile şirket içi/Azure VM 'Leri | ![Yes][green] | ![Yes][green]
MABS ile şirket içi/Azure VM 'Leri | ![Yes][green] | ![Yes][green]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure VM 'Lerini yedekleyin](backup-azure-arm-vms-prepare.md).
- [Windows makinelerini](tutorial-backup-windows-server-to-azure.md), yedekleme sunucusu olmadan doğrudan yedekleyin.
- Azure 'a yedekleme için [MABS ayarlayın](backup-azure-microsoft-azure-backup.md) ve sonra iş yüklerini mabs 'a yedekleyin.
- Azure 'a yedekleme için [DPM 'Yi ayarlayın](backup-azure-dpm-introduction.md) ve sonra Iş yüklerini DPM 'ye yedekleyin.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

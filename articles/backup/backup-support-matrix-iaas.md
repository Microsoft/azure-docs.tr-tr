---
title: Azure VM yedekleme için destek matrisi
description: Azure Backup hizmeti ile Azure VM 'lerini yedeklerken destek ayarlarının ve sınırlamaların özetini sağlar.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 1f63d0c3ad448a8ab9b91764d4c369fefddea25d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516731"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM yedekleme için destek matrisi

[Azure Backup hizmetini](backup-overview.md) şirket içi makineleri ve iş yüklerini ve Azure sanal makinelerini (VM) yedeklemek için kullanabilirsiniz. Bu makalede, Azure Backup ile Azure VM 'Leri yedeklerken destek ayarları ve sınırlamaları özetlenmektedir.

Diğer destek matrisleri:

- Azure Backup için [genel destek matrisi](backup-support-matrix.md)
- Azure Backup Server/System Center Data Protection Manager (DPM) yedeklemesi için [destek matrisi](backup-support-matrix-mabs-dpm.md)
- Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı ile yedekleme için [destek matrisi](backup-support-matrix-mars-agent.md)

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Azure Backup hizmeti ile Azure VM 'lerini yedekleme ve geri yükleme işlemlerinin nasıl yapılacağını aşağıda bulabilirsiniz.

**Senaryo** | **Backup** | **Aracı** |**Geri yükleme**
--- | --- | --- | ---
Azure VM 'lerinin doğrudan yedeklemesi  | Tüm VM 'yi yedekleyin.  | Azure VM 'de ek bir aracı gerekmez. Azure Backup, VM üzerinde çalışan [Azure VM aracısına](../virtual-machines/extensions/agent-windows.md) bir uzantı yükleyip kullanır. | Aşağıdaki şekilde geri yükleyin:<br/><br/> - **Temel BIR VM oluşturun**. Bu, VM 'nin birden çok IP adresi gibi özel bir yapılandırması yoksa yararlıdır.<br/><br/> - **VM diskini geri yükleyin**. Diski geri yükleyin. Daha sonra mevcut bir VM 'ye bağlayın veya PowerShell 'i kullanarak diskten yeni bir VM oluşturun.<br/><br/> - **VM diskini değiştirin**. Bir VM varsa ve yönetilen diskler (şifrelenmemiş) kullanıyorsa, bir diski geri yükleyebilir ve sanal makinede var olan bir diski değiştirmek için kullanabilirsiniz.<br/><br/> - **Belirli dosyaları/klasörleri geri yükleyin**. Dosyaları/klasörleri VM 'nin tamamı yerine bir VM 'den geri yükleyebilirsiniz.
Azure VM 'lerinin doğrudan yedeklemesi (yalnızca Windows)  | Belirli dosyaları/klasörleri/birimleri yedekleyin. | [Azure kurtarma hizmetleri Aracısı](backup-azure-file-folder-backup-faq.yml)'nı yükler.<br/><br/> VM 'yi dosya/klasör düzeyinde yedeklemek için, Azure VM aracısının yedekleme uzantısı ile birlikte MARS aracısını çalıştırabilirsiniz. | Belirli klasörleri/dosyaları geri yükleyin.
Azure VM 'yi yedekleme sunucusuna yedekleme  | Dosya/klasör/birim yedekleme; sistem durumu/çıplak dosyalar; uygulama verilerini System Center DPM 'ye veya Microsoft Azure Backup sunucusuna (MABS).<br/><br/> DPM/MABS daha sonra yedekleme kasasına yedekler. | DPM/MABS koruma aracısını VM 'ye yükler. MARS Aracısı DPM/MABS üzerine yüklenir.| Dosyaları/klasörleri/birimleri geri yükleme sistem durumu/çıplak dosyalar; uygulama verileri.

[Yedekleme sunucusu](backup-architecture.md#architecture-back-up-to-dpmmabs) ve [destek gereksinimleri](backup-support-matrix-mabs-dpm.md)hakkında daha fazla bilgi edinin.

## <a name="supported-backup-actions"></a>Desteklenen yedekleme eylemleri

**Eylem** | **Destek**
--- | ---
Kapalı/çevrimdışı VM olan bir VM 'yi yedekleme | Destekleniyor.<br/><br/> Anlık görüntü yalnızca çökme ile tutarlı, uygulamayla tutarlı değildir.
Yönetilen disklere geçtikten sonra diskleri yedekleme | Destekleniyor.<br/><br/> Yedekleme çalışmaya devam edecektir. İşlem yapmanız gerekmez.
Kaynak grubu kilidini etkinleştirdikten sonra yönetilen diskleri yedekleme | Desteklenmez.<br/><br/> Azure Backup eski geri yükleme noktalarını silemiyor ve en fazla geri yükleme noktası sınırına ulaşıldığında yedeklemeler başarısız olur.
VM için yedekleme ilkesini değiştirme | Destekleniyor.<br/><br/> VM, yeni ilkedeki zamanlama ve bekletme ayarları kullanılarak yedeklenir. Bekletme ayarları genişletilmişse, var olan kurtarma noktaları işaretlenir ve saklanır. Azaldıysanız, var olan kurtarma noktaları sonraki temizleme işinde ayıklanır ve sonunda silinir.
Bir yedekleme işini iptal et| Anlık görüntü işlemi sırasında desteklenir.<br/><br/> Anlık görüntü kasaya aktarıldığında desteklenmez.
VM 'yi farklı bir bölgeye veya aboneliğe yedekleme |Desteklenmez.<br><br>Başarıyla yedeklemek için, sanal makinelerin Yedekleme Kasası ile aynı abonelikte olması gerekir.
Gün başına yedeklemeler (Azure VM Uzantısı aracılığıyla) | Gün başına bir zamanlanmış yedekleme.<br/><br/>Azure Backup hizmeti, günde en fazla üç isteğe bağlı yedeklemeyi ve zamanlanan bir ek yedeklemeyi destekler.
Gün başına yedeklemeler (MARS Aracısı aracılığıyla) | Gün başına üç zamanlanmış yedekleme.
Gün başına yedeklemeler (DPM/MABS aracılığıyla) | Gün başına iki zamanlanmış yedekleme.
Aylık/yıllık yedekleme| Azure VM Uzantısı ile yedeklenirken desteklenmez. Yalnızca günlük ve haftalık desteklenir.<br/><br/> İlkeyi aylık/yıllık Bekletme dönemi için günlük/haftalık yedeklemeleri koruyacak şekilde ayarlayabilirsiniz.
Otomatik saat ayarlaması | Desteklenmez.<br/><br/> Azure Backup, bir VM yedeklenirken gün ışığından yararlanma saati değişikliklerini otomatik olarak ayarlamaz.<br/><br/>  İlkeyi gerektiği şekilde el ile değiştirin.
[Karma yedekleme için güvenlik özellikleri](./backup-azure-security-feature.md) |Güvenlik özelliklerini devre dışı bırakma desteklenmiyor.
Makine süresi değişmiş olan VM 'yi yedekleme | Desteklenmez.<br/><br/> Makine saati, bu VM için yedeklemeyi etkinleştirdikten sonra gelecek bir tarih-saat olarak değiştirilirse, zaman değişikliği geri döndürülse bile, başarılı yedekleme garantisi garanti edilmez.

## <a name="operating-system-support-windows"></a>İşletim sistemi desteği (Windows)

Aşağıdaki tabloda, Windows Azure VM 'Leri yedeklenirken desteklenen işletim sistemleri özetlenmektedir.

**Senaryo** | **İşletim sistemi desteği**
--- | ---
Azure VM Aracısı Uzantısı ile yedekleme | -Windows 10 Istemcisi (yalnızca 64 bit) <br/><br/>-Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> -Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> -Windows Server 2012 (Datacenter/Standard) <br/><br/> -Windows Server 2008 R2 (RTM ve SP1 standart)  <br/><br/> -Windows Server 2008 (yalnızca 64 bit)
MARS Aracısı ile yedekleme | [Desteklenen](backup-support-matrix-mars-agent.md#supported-operating-systems) işletim sistemleri.
DPM/MABS ile yedekleme | [Mabs](backup-mabs-protection-matrix.md) ve [DPM](/system-center/dpm/dpm-protection-matrix)ile yedekleme için desteklenen işletim sistemleri.

Azure Backup 32 bit işletim sistemlerini desteklemez.

## <a name="support-for-linux-backup"></a>Linux yedekleme desteği

Linux makinelerini yedeklemek istiyorsanız, bu desteklenir.

**Eylem** | **Destek**
--- | ---
Linux Azure VM aracısıyla Linux Azure VM 'lerini yedekleme | Dosya tutarlı yedekleme.<br/><br/> [Özel betikler](backup-azure-linux-app-consistent.md)kullanılarak uygulamayla tutarlı yedekleme.<br/><br/> Geri yükleme sırasında yeni bir sanal makine oluşturabilir, bir diski geri yükleyebilir ve bir VM oluşturmak veya bir diski geri yüklemek için kullanabilir ve bunu kullanarak var olan bir VM 'deki bir diski değiştirebilirsiniz. Tek tek dosyaları ve klasörleri de geri yükleyebilirsiniz.
MARS Aracısı ile Linux Azure VM 'lerini yedekleme | Desteklenmez.<br/><br/> MARS Aracısı yalnızca Windows makinelere yüklenebilir.
DPM/MABS ile Linux Azure VM 'lerini yedekleme | Desteklenmez.
Docker bağlama noktalarıyla Linux Azure VM 'lerini yedekleme | Şu anda, Azure Backup Docker bağlama noktalarının dışlamasını her seferinde farklı yollarda bağlandığından desteklemez.

## <a name="operating-system-support-linux"></a>İşletim sistemi desteği (Linux)

Azure VM Linux yedeklemeleri için Azure Backup, [Azure tarafından](../virtual-machines/linux/endorsed-distros.md)onaylanan Linux dağıtımları listesini destekler. Şunlara dikkat edin:

- Azure Backup, çekirdek işletim sistemi Linux 'u desteklemez.
- Azure Backup 32 bit işletim sistemlerini desteklemez.
- Diğer kendi Linux dağıtımları, [Linux Için Azure VM ARACıSı](../virtual-machines/extensions/agent-linux.md) VM 'de kullanılabildiği ve Python desteklendiği sürece çalışan Linux dağıtımlarınız çalışabilir.
- Azure Backup, Python sürüm 2,7 yüklü değilse proxy tarafından yapılandırılan bir Linux VM 'yi desteklemez.
- Azure Backup, depolama alanından veya diğer herhangi bir NFS sunucusundan Linux veya Windows makinelerine bağlı NFS dosyalarını yedeklemeyi desteklemez. Yalnızca VM 'ye yerel olarak bağlı diskleri yedekler.

## <a name="backup-frequency-and-retention"></a>Yedekleme sıklığı ve bekletme

**Ayar** | **Sınırlar**
--- | ---
Korumalı örnek başına en fazla kurtarma noktası (makine/iş yükü) | 9999.
Kurtarma noktası için en uzun süre sonu zamanı | Sınır yok (99 yıl).
Kasaya maksimum yedekleme sıklığı (Azure VM uzantısı) | Günde bir kez.
Maksimum yedekleme sıklığı (MARS Aracısı) | Gün başına üç yedekleme.
DPM/MABS için maksimum yedekleme sıklığı | SQL Server için her 15 dakikada bir.<br/><br/> Diğer iş yükleri için saatte bir.
Kurtarma noktası bekletme | Günlük, haftalık, aylık ve yıllık.
En uzun bekletme süresi | Yedekleme sıklığına bağlıdır.
DPM/MABS diskindeki kurtarma noktaları | dosya sunucuları için 64 ve uygulama sunucuları için 448.<br/><br/> Bant kurtarma noktaları, şirket içi DPM için sınırsızdır.

## <a name="supported-restore-methods"></a>Desteklenen geri yükleme yöntemleri

**Geri yükleme seçeneği** | **Ayrıntılar**
--- | ---
**Yeni VM oluştur** | Geri yükleme noktasından hızlıca temel bir VM oluşturur ve çalıştırmaya başlar.<br/><br/> VM için bir ad belirtebilir, yerleştirileceği kaynak grubunu ve sanal ağı (VNet) seçin ve geri yüklenen VM için bir depolama hesabı belirtin. Yeni VM'nin kaynak VM ile aynı bölgede oluşturulması gerekir.
**Diski geri yükle** | Yeni bir VM oluşturmak üzere kullanılabilecek bir VM diskini geri yükler.<br/><br/> Azure Backup, VM oluşturmanıza ve özelleştirmenize yardımcı olacak bir şablon sağlar. <br/><br> Geri yükleme işi, özel VM ayarlarını belirtmek ve bir VM oluşturmak için indirebileceğiniz ve kullanabileceğiniz bir şablon oluşturur.<br/><br/> Diskler belirttiğiniz kaynak grubuna kopyalanır.<br/><br/> Alternatif olarak, diski mevcut bir sanal makineye iliştirebilir veya PowerShell kullanarak yeni bir VM oluşturabilirsiniz.<br/><br/> Bu seçenek VM'yi özelleştirmek, yedekleme sırasında mevcut olmayan yapılandırma ayarlarını eklemek veya şablon veya PowerShell kullanılarak yapılandırılması gereken ayarlar eklemek istediğinizde faydalı olur.
**Mevcut olanı değiştir** | Bir diski geri yükleyebilir ve mevcut VM 'deki bir diski değiştirmek için kullanabilirsiniz.<br/><br/> Geçerli VM'nin mevcut olması gerekir. Silinmesi durumunda bu seçenek kullanılamaz.<br/><br/> Azure Backup, diski değiştirmeden önce var olan sanal makinenin anlık görüntüsünü alır ve bunu belirttiğiniz hazırlama konumunda depolar. VM'ye bağlı olan mevcut diskler seçilen geri yükleme noktasıyla değiştirilir.<br/><br/> Anlık görüntü kasaya kopyalanır ve bekletme ilkesine göre saklanır. <br/><br/> Disk değiştirme işleminden sonra, özgün disk kaynak grubunda tutulur. Gerekli değilse, özgün diskleri el ile silmeyi tercih edebilirsiniz. <br/><br/>Mevcut olanı Değiştir, şifrelenmemiş yönetilen sanal makineler ve [özel görüntüler kullanılarak oluşturulan](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)VM 'ler için desteklenir. Yönetilmeyen diskler ve VM 'Ler, klasik VM 'ler ve [Genelleştirilmiş VM 'ler](../virtual-machines/windows/capture-image-resource.md)için desteklenmez.<br/><br/> Geri yükleme noktası geçerli VM 'den daha fazla veya daha az disk içeriyorsa, geri yükleme noktasındaki disk sayısı yalnızca VM yapılandırmasını yansıtacaktır.<br><br> Aynı zamanda, [Kullanıcı tarafından atanan yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md) ve [Key Vault](../key-vault/general/overview.md)gibi bağlantılı kaynaklarla sanal makineler için de desteklenir.
**Bölgeler Arası (ikincil bölge)** | Çapraz bölge geri yükleme, Azure [eşlenmiş bölgesi](../best-practices-availability-paired-regions.md#what-are-paired-regions)olan Ikincil bölgedeki Azure VM 'lerini geri yüklemek için kullanılabilir.<br><br> Yedekleme ikincil bölgede yapıldığında, seçilen kurtarma noktası için tüm Azure VM 'lerini geri yükleyebilirsiniz.<br><br> Bu özellik aşağıdaki seçenekler için kullanılabilir:<br> <li> [VM oluştur](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Diskleri geri yükle](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> [Mevcut diskleri Değiştir](./backup-azure-arm-restore-vms.md#replace-existing-disks) seçeneğini şu anda desteklemiyoruz.<br><br> İzinler<br> İkincil bölgedeki geri yükleme işlemi, Backup Admins ve App Admins tarafından gerçekleştirilebilir.

## <a name="support-for-file-level-restore"></a>Dosya düzeyinde geri yükleme desteği

**Geri yükleme** | **Desteklenir**
--- | ---
İşletim sistemleri arasında dosyaları geri yükleme | Yedeklenen VM ile aynı (veya uyumlu) işletim sistemine sahip herhangi bir makinedeki dosyaları geri yükleyebilirsiniz. Bkz. [uyumlu işletim sistemi tablosu](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).
Şifrelenmiş VM 'lerden dosyaları geri yükleme | Desteklenmez.
Ağ kısıtlı depolama hesaplarından dosyaları geri yükleme | Desteklenmez.
Windows depolama alanları 'nı kullanarak sanal makinelere dosya geri yükleme | Geri yükleme aynı VM 'de desteklenmiyor.<br/><br/> Bunun yerine, uyumlu bir sanal makinenin dosyalarını geri yükleyin.
LVM/RAID dizilerini kullanarak Linux sanal makinesinde dosyaları geri yükleme | Geri yükleme aynı VM 'de desteklenmiyor.<br/><br/> Uyumlu bir sanal makineye geri yükleme.
Dosyaları özel ağ ayarlarıyla geri yükleme | Geri yükleme aynı VM 'de desteklenmiyor. <br/><br/> Uyumlu bir sanal makineye geri yükleme.
Yazma Hızlandırıcısı etkinken paylaşılan disk, geçici sürücü, yinelenenleri kaldırılmış disk, Ultra disk ve diskten dosya geri yükleme | Geri yükleme desteklenmiyor, <br/><br/>bkz. [Azure VM depolama desteği](#vm-storage-support).

## <a name="support-for-vm-management"></a>VM yönetimi desteği

Aşağıdaki tabloda VM diskleri ekleme veya değiştirme gibi VM yönetim görevleri sırasında yedekleme desteği özetlenmektedir.

**Geri yükleme** | **Desteklenir**
--- | ---
Abonelik/bölge/bölge genelinde geri yükleme. | Desteklenmez.
Mevcut bir VM 'ye geri yükleme | Diski Değiştir seçeneğini kullanın.
Azure Depolama Hizmeti Şifrelemesi için etkinleştirilmiş depolama hesabı ile diski geri yükleme (SSE) | Desteklenmez.<br/><br/> SSE özellikli olmayan bir hesaba geri yükleyin.
Karma depolama hesaplarına geri yükleme |Desteklenmez.<br/><br/> Depolama hesabı türüne bağlı olarak, tüm geri yüklenen diskler Premium veya standart olur ve karışmaz.
VM 'yi doğrudan bir kullanılabilirlik kümesine geri yükleme | Yönetilen diskler için diski geri yükleyebilir ve şablondaki kullanılabilirlik kümesi seçeneğini kullanabilirsiniz.<br/><br/> Yönetilmeyen diskler için desteklenmez. Yönetilmeyen diskler için, diski geri yükleyin ve ardından kullanılabilirlik kümesinde bir VM oluşturun.
Yönetilen VM 'ye yükselttikten sonra yönetilmeyen VM 'lerin yedeklemesini geri yükleme| Destekleniyor.<br/><br/> Diskleri geri yükleyebilir ve ardından yönetilen bir VM oluşturabilirsiniz.
VM yönetilen disklere geçirilmeden önce VM 'yi geri yükleme noktasına geri yükleme | Destekleniyor.<br/><br/> Yönetilmeyen disklere geri yükleme (varsayılan), geri yüklenen diskleri yönetilen diske Dönüştür ve yönetilen disklerle bir VM oluşturun.
Silinen bir VM 'yi geri yükleyin. | Destekleniyor.<br/><br/> VM 'yi bir kurtarma noktasından geri yükleyebilirsiniz.
Bir etki alanı denetleyicisi VM 'sini geri yükleme  | Destekleniyor. Ayrıntılar için bkz. [etki alanı denetleyicisi VM 'Lerini geri yükleme](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
Farklı sanal ağdaki VM 'yi geri yükleme |Destekleniyor.<br/><br/> Sanal ağın aynı abonelikte ve bölgede olması gerekir.

## <a name="vm-compute-support"></a>VM işlem desteği

**İşlem** | **Destek**
--- | ---
VM boyutu |En az 2 CPU çekirdeği ve 1 GB RAM içeren herhangi bir Azure VM boyutu.<br/><br/> [Daha fazla bilgi edinin.](../virtual-machines/sizes.md)
[Kullanılabilirlik kümelerinde](../virtual-machines/availability.md#availability-sets) VM 'leri yedekleme | Destekleniyor.<br/><br/> Hızlı bir şekilde VM oluşturma seçeneğini kullanarak kullanılabilir bir küme içindeki bir VM 'yi geri alamazsınız. Bunun yerine, VM 'yi geri yükledikten sonra diski geri yükler ve bir VM dağıtmak ya da bir diski geri yüklemek için kullanın ve var olan bir diski değiştirmek için kullanın.
[Karma kullanım avantajı (hub)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) Ile dağıtılan VM 'leri yedekleme | Destekleniyor.
[Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) 'Nden dağıtılan VM 'leri yedekleme<br/><br/> (Microsoft, üçüncü taraf tarafından yayımlandı) |Destekleniyor.<br/><br/> VM desteklenen bir işletim sistemi çalıştırıyor olmalıdır.<br/><br/> SANAL makinede dosyaları kurtarırken, yalnızca uyumlu bir işletim sistemine (önceki veya sonraki bir işletim sistemini değil) geri yükleyebilirsiniz. VM olarak desteklenen Azure Market VM 'lerini, satın alma bilgileri gerekli olduğundan geri yüklemeyin. Bunlar yalnızca disk olarak geri yüklenir.
Özel görüntüden dağıtılan VM 'Leri yedekleme (üçüncü taraf) |Destekleniyor.<br/><br/> VM desteklenen bir işletim sistemi çalıştırıyor olmalıdır.<br/><br/> SANAL makinede dosyaları kurtarırken, yalnızca uyumlu bir işletim sistemine (önceki veya sonraki bir işletim sistemini değil) geri yükleyebilirsiniz.
Azure 'a geçirilen VM 'Leri yedekleme| Destekleniyor.<br/><br/> VM 'yi yedeklemek için, geçirilen makinede VM aracısının yüklü olması gerekir.
Çoklu VM tutarlılığını yedekleme | Azure Backup birden çok VM arasında veri ve uygulama tutarlılığı sağlamaz.
[Tanılama ayarlarıyla](../azure-monitor/essentials/platform-logs-overview.md) yedekleme  | Desteklenmez. <br/><br/> Azure VM 'yi tanılama ayarlarıyla geri yükleme [yeni seçenek oluştur](backup-azure-arm-restore-vms.md#create-a-vm) seçeneği kullanılarak tetikleniyorsa geri yükleme başarısız olur.
Bölge ile sabitlenmiş VM 'Leri geri yükleme | Desteklenir (Ocak 2019 ' den sonra yedeklenen ve [kullanılabilirlik bölgelerinin](https://azure.microsoft.com/global-infrastructure/availability-zones/) KULLANıLABILDIĞI bir VM için).<br/><br/>Şu anda VM 'lerde sabitlenmiş aynı bölgeye geri yüklemeyi destekliyoruz. Ancak, bölge bir kesinti nedeniyle kullanılamazsa geri yükleme başarısız olur.
Gen2 VM 'Leri | Desteklenir <br> Azure Backup, [Gen2 VM](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)'lerinin yedeklenmesini ve geri yüklenmesini destekler. Bu VM 'Ler kurtarma noktasından geri yüklendiğinde, [Gen2 VM](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)olarak geri yüklenir.
Kilitleri olan Azure VM 'lerinin yedeklenmesi | Yönetilmeyen VM 'Ler için desteklenmez. <br><br> Yönetilen VM 'Ler için desteklenir.
[Spot VM'ler](../virtual-machines/spot-vms.md) | Desteklenmez. Azure Backup, normal Azure VM 'Leri olarak spot VM 'Leri geri yükler.
[Azure Ayrılmış Konak](../virtual-machines/dedicated-hosts.md) | Desteklenir
Tek başına Azure VM 'lerinin Windows depolama alanları yapılandırması | Desteklenir
[Azure VM Ölçek Kümeleri](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md#scale-sets-with-flexible-orchestration) | Tek bir Azure VM 'yi yedeklemek ve geri yüklemek için hem Tekdüzen hem de esnek düzenleme modelleri için desteklenir.

## <a name="vm-storage-support"></a>VM depolama desteği

**Bileşen** | **Destek**
--- | ---
Azure VM veri diskleri | En fazla 32 diski olan Azure VM 'lerinin yedeklenmesi desteği.<br><br> Yönetilmeyen diskler veya klasik VM 'Ler ile Azure VM 'lerinin yedeklenmesi desteği yalnızca 16 diske kadar olur.
Veri diski boyutu | Tek bir disk boyutu 32 TB 'a kadar ve bir VM 'deki tüm diskler için en fazla 256 TB olabilir.
Depolama türü | Standart HDD, Standart SSD Premium SSD.
Yönetilen diskler | Destekleniyor.
Şifrelenmiş diskler | Destekleniyor.<br/><br/> Azure disk şifrelemesi ile etkinleştirilen Azure VM 'Leri yedeklenebilir (Azure AD uygulaması ile veya olmadan).<br/><br/> Şifrelenmiş VM 'Ler dosya/klasör düzeyinde kurtarılamaz. Tüm VM 'yi kurtarmanız gerekir.<br/><br/> Azure Backup tarafından zaten korunan VM 'lerde şifrelemeyi etkinleştirebilirsiniz.
Yazma Hızlandırıcısı etkin olan diskler | 23 Kasım 2020 itibariyle, sınırlı sayıda abonelik için yalnızca Kore Orta (KRC) ve Güney Afrika Kuzey (SAN) bölgelerinde desteklenir. Desteklenen bu abonelikler için Azure Backup, yedekleme sırasında hızlandırılmış yazma (WA) özellikli disklere sahip sanal makineleri yedeklenecektir.<br><br>Desteklenmeyen bölgelerde, sanal makinelerin WA etkin olduğu sanal makinelerin anlık görüntülerini almak için VM 'de internet bağlantısı gereklidir.<br><br> **Önemli Note**: Bu desteklenmeyen bölgelerde, WA diskleri olan sanal makinelerin başarılı bir yedekleme için internet bağlantısı olması gerekir (Bu diskler yedeklemeden dışlanıyor olsa da).
Yinelenen yinelenenleri kaldırma & VM 'Leri/diskleri geri yükleme | Azure Backup Yinelenenleri kaldırmayı desteklemez. Daha fazla bilgi için bu [makaleye](./backup-support-matrix.md#disk-deduplication-support) bakın <br/> <br/>  -Azure Backup kurtarma hizmetleri kasasındaki VM 'lerde yinelenmez <br/> <br/>  -Geri yükleme sırasında yinelenenleri kaldırma durumunda VM 'Ler varsa, kasa biçimi anlamadığından dosyalar geri yüklenemez. Ancak, tam VM geri yükleme işlemini başarıyla gerçekleştirebilirsiniz.
Korumalı VM 'ye disk ekleme | Destekleniyor.
Korumalı VM 'de diski yeniden boyutlandır | Destekleniyor.
Paylaşılan depolama alanı| Küme Paylaşılan Birimi (CSV) veya Scale-Out dosya sunucusu kullanarak VM 'Leri yedekleme desteklenmez. Yedekleme sırasında CSV yazıcılarının başarısız olma olasılığı yüksektir. Geri yükleme sırasında CSV birimleri içeren diskler gelmeyebilir.
[Paylaşılan diskler](../virtual-machines/disks-shared-enable.md) | Desteklenmez.
Ultra SSD diskler | Desteklenmez. Daha fazla bilgi için, bkz. bu [sınırlamalar](selective-disk-backup-restore.md#limitations).
[Geçici diskler](../virtual-machines/managed-disks-overview.md#temporary-disk) | Geçici diskler Azure Backup tarafından yedeklenmez.
NVMe/kısa ömürlü diskler | Desteklenmez.

## <a name="vm-network-support"></a>VM ağı desteği

**Bileşen** | **Destek**
--- | ---
Ağ arabirimi sayısı (NIC) | Belirli bir Azure VM boyutu için desteklenen en fazla NIC sayısı.<br/><br/> NIC 'ler, geri yükleme işlemi sırasında VM oluşturulduğunda oluşturulur.<br/><br/> Geri yüklenen VM üzerindeki NIC sayısı, korumayı etkinleştirdiğinizde VM üzerindeki NIC 'lerin sayısını yansıtır. Korumayı etkinleştirdikten sonra NIC 'Lerin kaldırılması sayıyı etkilemez.
Dış/iç yük dengeleyici |Destekleniyor. <br/><br/> Özel ağ ayarlarıyla VM 'Leri geri yükleme hakkında [daha fazla bilgi edinin](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) .
Birden çok ayrılmış IP adresi |Destekleniyor. <br/><br/> Özel ağ ayarlarıyla VM 'Leri geri yükleme hakkında [daha fazla bilgi edinin](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) .
Birden çok ağ bağdaştırıcısı olan VM 'Ler| Destekleniyor. <br/><br/> Özel ağ ayarlarıyla VM 'Leri geri yükleme hakkında [daha fazla bilgi edinin](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) .
Genel IP adreslerine sahip VM 'Ler| Destekleniyor.<br/><br/> Mevcut bir genel IP adresini NIC ile ilişkilendirin veya bir adres oluşturun ve geri yükleme yapıldıktan sonra bu adresi NIC ile ilişkilendirin.
NIC/alt ağ üzerinde ağ güvenlik grubu (NSG). |Destekleniyor.
Statik IP adresi | Desteklenmez.<br/><br/> Bir geri yükleme noktasından oluşturulan yeni bir sanal makineye dinamik bir IP adresi atanır.<br/><br/> Klasik VM 'Ler için, ayrılmış bir IP adresi ve tanımlı uç nokta olmayan bir VM 'yi yedekleyemiyoruz.
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
- Yalnızca bu verilerin kilidini açmak için şifreleme anahtarınız vardır. Microsoft, herhangi bir noktada yedekleme verilerinin şifresini çözemez.

  > [!WARNING]
  > Kasayı ayarladıktan sonra yalnızca şifreleme anahtarına erişiminiz vardır. Microsoft hiçbir şekilde kopya korumaz ve anahtara erişemez. Anahtar yanlış olursa Microsoft, yedekleme verilerini kurtaramaz.

Veri güvenliği:

- Azure VM 'lerini yedeklerken, sanal makine *içinde* şifrelemeyi ayarlamanız gerekir.
- Azure Backup, Linux sanal makinelerinde Windows sanal makinelerinde BitLocker ve US **dm-crypt** kullanan Azure disk şifrelemeyi destekler.
- Azure Backup arka uçta bekleyen verileri koruyan [Azure Depolama Hizmeti şifrelemesini](../storage/common/storage-service-encryption.md) kullanır.

**Makine** | **Yoldaki** | **Bekleyen**
--- | --- | ---
DPM/MABS olmayan şirket içi Windows makineleri | ![Yes][green] | ![Yes][green]
Azure VM’leri | ![Yes][green] | ![Yes][green]
DPM ile şirket içi/Azure VM 'Leri | ![Yes][green] | ![Yes][green]
MABS ile şirket içi/Azure VM 'Leri | ![Yes][green] | ![Yes][green]

## <a name="vm-compression-support"></a>VM sıkıştırma desteği

Yedekleme, aşağıdaki tabloda özetlenen yedekleme trafiğinin sıkıştırmasını destekler. Şunlara dikkat edin:

- Azure VM 'Leri için VM uzantısı, verileri doğrudan depolama ağı üzerinden Azure Storage hesabından okur. Bu trafiği sıkıştırmak gerekli değildir.
- DPM veya MABS kullanıyorsanız, DPM/MABS 'e yedeklenmeden önce verileri sıkıştırarak bant genişliği tasarrufu yapabilirsiniz.

**Makine** | **MABS/DPM 'ye Sıkıştır (TCP)** | **Kasaya Sıkıştır (HTTPS)**
--- | --- | ---
DPM/MABS olmayan şirket içi Windows makineleri | NA | ![Yes][green]
Azure VM’leri | NA | NA
DPM ile şirket içi/Azure VM 'Leri | ![Yes][green] | ![Yes][green]
MABS ile şirket içi/Azure VM 'Leri | ![Yes][green] | ![Evet][green]

## <a name="next-steps"></a>Sonraki adımlar

- [Azure VM 'Lerini yedekleyin](backup-azure-arm-vms-prepare.md).
- [Windows makinelerini](tutorial-backup-windows-server-to-azure.md), yedekleme sunucusu olmadan doğrudan yedekleyin.
- Azure 'a yedekleme için [MABS ayarlayın](backup-azure-microsoft-azure-backup.md) ve sonra iş yüklerini mabs 'a yedekleyin.
- Azure 'a yedekleme için [DPM 'Yi ayarlayın](backup-azure-dpm-introduction.md) ve sonra Iş yüklerini DPM 'ye yedekleyin.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
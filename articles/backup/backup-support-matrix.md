---
title: Azure Backup destek matrisi
description: Azure Backup hizmeti için destek ayarlarının ve sınırlamaların özetini sağlar.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5c74a34efe8075ab7a34fab4570d9513900b3f81
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517428"
---
# <a name="support-matrix-for-azure-backup"></a>Azure Backup için destek matrisi

[Azure Backup](backup-overview.md) , verileri Microsoft Azure bulut platformuna yedeklemek için kullanabilirsiniz. Bu makalede Azure Backup senaryolar ve dağıtımlar için genel destek ayarları ve sınırlamaları özetlenmektedir.

Diğer destek matrisleri mevcuttur:

- [Azure sanal makinesi (VM) yedeklemesi](backup-support-matrix-iaas.md) için destek matrisi
- [System Center Data Protection Manager (DPM)/Microsoft Azure Backup sunucusu (MABS)](backup-support-matrix-mabs-dpm.md) kullanarak yedekleme için destek matrisi
- [Microsoft Azure kurtarma hizmetleri (mars) Aracısı](backup-support-matrix-mars-agent.md) kullanılarak yedekleme için destek matrisi

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Kasa desteği

Azure Backup, aşağıdaki iş yükü türleri için yedeklemeleri düzenlemek ve yönetmek üzere Kurtarma Hizmetleri kasalarını kullanır. Azure VM 'leri, Azure VM 'lerinde SQL, Azure VM 'lerinde SAP HANA, Azure dosya paylaşımları ve şirket içi iş yükleri Azure Backup Aracı, Azure Backup Sunucusu ve System Center DPM. Ayrıca, bu iş yükleri için yedeklenmiş verileri depolamak üzere Kurtarma Hizmetleri kasalarını kullanır.

Aşağıdaki tabloda, kurtarma hizmetleri kasalarının özellikleri açıklanmaktadır:

**Özellik** | **Ayrıntılar**
--- | ---
**Abonelikte kasa** | Tek bir abonelikte en fazla 500 kurtarma hizmeti Kasası.
**Kasadaki makineler** | Tüm iş yükleri (Azure VM 'Leri, SQL Server VM, MABS sunucuları vb. gibi) üzerinde en fazla 2000 veri kaynağı tek bir kasada korunabilir.<br><br>Tek bir kasada en fazla 1.000 Azure VM.<br/><br/> 50 MABS sunucusuna kadar tek bir kasada kayıt yapılabilir.
**Veri kaynakları** | Tek bir [veri kaynağının](./backup-azure-backup-faq.yml#how-is-the-data-source-size-determined-) en büyük boyutu 54.400 GB 'dir. Bu sınır, Azure VM yedeklemeleri için geçerlidir. Kasaya yedekleyebileceğiniz toplam veri miktarı için sınır uygulanmaz.
**Kasaya yedeklemeler** | **Azure VM 'leri:** Günde bir kez.<br/><br/>**DPM/MABS tarafından korunan makineler:** Günde iki kez.<br/><br/> **Mars Aracısı kullanılarak doğrudan yedeklenen makineler:** Günde üç kez.
**Kasaların arasındaki yedeklemeler** | Yedekleme bir bölgede yer alan.<br/><br/> Yedeklemek istediğiniz VM 'Leri içeren her Azure bölgesinde bir kasaya ihtiyacınız vardır. Farklı bir bölgeye yedekleme yapamazsınız.
**Kasaları taşı** | Kasalarını abonelikler arasında veya aynı abonelikteki kaynak grupları arasında [taşıyabilirsiniz](./backup-azure-move-recovery-services-vault.md) . Ancak, kasaların bölgeler arasında taşınması desteklenmez.
**Kasaları arasında veri taşıma** | Yedeklenen verilerin kasaların arasına taşınması desteklenmez.
**Kasa depolama türünü değiştir** | Yedeklemeler depolanmadan önce bir kasa için depolama çoğaltma türünü (coğrafi olarak yedekli depolama veya yerel olarak yedekli depolama) değiştirebilirsiniz. Yedekler kasada başladıktan sonra çoğaltma türü değiştirilemez.
**Alanlar arası yedekli depolama (ZRS)** | UK Güney (UKS) ve Güney Doğu Asya (SEA) bölgelerinde kullanılabilir.
**Özel uç noktalar** | Bir kurtarma hizmeti Kasası için özel uç noktalar oluşturma gereksinimlerinin için [Bu bölüme](./private-endpoints.md#before-you-start) bakın.  

## <a name="on-premises-backup-support"></a>Şirket içi yedekleme desteği

Şirket içi makineleri yedeklemek istiyorsanız bu adım desteklenir:

**Makine** | **Yedeklenen Özellikler** | **Konum** | **Özellikler**
--- | --- | --- | ---
**Windows makinenin MARS aracısıyla doğrudan yedeklenmesi** | Dosyalar, klasörler, sistem durumu | Kurtarma Hizmetleri kasasına yedekleyin. | Günde üç kez yedekleme<br/><br/> Uygulamayla uyumlu yedekleme yok<br/><br/> Dosya, klasör, birim geri yükleme
**MARS Aracısı ile Linux makinenin doğrudan yedeklemesi** | Yedekleme desteklenmiyor
**DPM 'ye yedekleme** | Dosyalar, klasörler, birimler, sistem durumu, uygulama verileri | Yerel DPM depolamasına yedekleme. DPM daha sonra kasaya yedekler. | Uygulama kullanan anlık görüntüler<br/><br/> Yedekleme ve kurtarma için tam ayrıntı düzeyi<br/><br/> VM 'Ler için desteklenen Linux (Hyper-V/VMware)<br/><br/> Oracle desteklenmiyor
**MABS 'e yedekleme** | Dosyalar, klasörler, birimler, sistem durumu, uygulama verileri | MABS yerel depolama alanına yedekleme. MABS daha sonra kasaya yedekler. | Uygulama kullanan anlık görüntüler<br/><br/> Yedekleme ve kurtarma için tam ayrıntı düzeyi<br/><br/> VM 'Ler için desteklenen Linux (Hyper-V/VMware)<br/><br/> Oracle desteklenmiyor

## <a name="azure-vm-backup-support"></a>Azure VM yedekleme desteği

### <a name="azure-vm-limits"></a>Azure VM sınırları

**Sınır** | **Ayrıntılar**
--- | ---
**Azure VM veri diskleri** | Bkz. [Azure VM yedeklemesi için destek matrisi](./backup-support-matrix-iaas.md#vm-storage-support).
**Azure VM veri diski boyutu** | Tek bir disk boyutu 32 TB 'a kadar ve bir VM 'deki tüm diskler için en fazla 256 TB olabilir.

### <a name="azure-vm-backup-options"></a>Azure VM yedekleme seçenekleri

Azure VM 'lerini yedeklemek istiyorsanız bu destek aşağıda verilmiştir:

**Makine** | **Yedeklenen Özellikler** | **Konum** | **Özellikler**
--- | --- | --- | ---
**VM uzantısı kullanarak Azure VM yedeklemesi** | Tüm VM | Kasaya yedekleyin. | Bir VM için yedeklemeyi etkinleştirdiğinizde uzantı yüklendi.<br/><br/> Günde bir kez yedekleyin.<br/><br/> Windows VM 'Leri için uygulamayla uyumlu yedekleme; Linux VM 'Ler için dosya tutarlı yedekleme. Linux makineler için uygulama tutarlılığını, Özel betikler kullanarak yapılandırabilirsiniz.<br/><br/> VM 'yi veya diski geri yükleyin.<br/><br/>[Active Directory etki alanı denetleyicilerinin yedeklenmesi ve geri yüklenmesi](active-directory-backup-restore.md) desteklenir.<br><br> Bir Azure VM 'yi şirket içi bir konuma yedekleyemiyorum.
**MARS Aracısı kullanarak Azure VM yedeklemesi** | Dosyalar, klasörler, sistem durumu | Kasaya yedekleyin. | Günde üç kez yedekleyin.<br/><br/> Tüm VM yerine belirli dosya veya klasörleri yedeklemek istiyorsanız, MARS Aracısı VM uzantısıyla birlikte çalışabilir.
**DPM ile Azure VM** | Dosyalar, klasörler, birimler, sistem durumu, uygulama verileri | DPM çalıştıran Azure VM 'nin yerel depolama birimine yedekleme. DPM daha sonra kasaya yedekler. | Uygulama kullanan anlık görüntüler.<br/><br/> Yedekleme ve kurtarma için tam ayrıntı düzeyi.<br/><br/> VM 'Ler için desteklenen Linux (Hyper-V/VMware).<br/><br/> Oracle desteklenmiyor.
**MABS ile Azure VM** | Dosyalar, klasörler, birimler, sistem durumu, uygulama verileri | MABS çalıştıran Azure VM 'nin yerel depolama birimine yedekleme. MABS daha sonra kasaya yedekler. | Uygulama kullanan anlık görüntüler.<br/><br/> Yedekleme ve kurtarma için tam ayrıntı düzeyi.<br/><br/> VM 'Ler için desteklenen Linux (Hyper-V/VMware).<br/><br/> Oracle desteklenmiyor.

## <a name="linux-backup-support"></a>Linux yedekleme desteği

Linux makinelerini yedeklemek istiyorsanız, bu destek aşağıda verilmiştir:

**Yedekleme türü** | **Linux (Azure onaysed)**
--- | ---
**Linux çalıştıran şirket içi makinenin doğrudan yedeklemesi** | Desteklenmez. MARS Aracısı yalnızca Windows makinelerine yüklenebilir.
**Linux çalıştıran Azure VM 'yi yedeklemek için aracı uzantısı kullanma** | [Özel betikler](backup-azure-linux-app-consistent.md)kullanarak uygulamayla tutarlı yedekleme.<br/><br/> Dosya düzeyinde kurtarma.<br/><br/> Kurtarma noktasından veya diskten bir VM oluşturarak geri yükleyin.
**Linux çalıştıran şirket içi makineleri yedeklemek için DPM 'yi kullanma** | Hyper-V ve VMware üzerinde Linux konuk VM 'lerinin dosya ile tutarlı yedeklemesi.<br/><br/> Hyper-V ve VMware Linux konuk VM 'lerinin VM geri yüklemesi.
**Linux çalıştıran şirket içi makineleri yedeklemek için MABS kullanma** | Hyper-V ve VMware üzerinde Linux konuk VM 'lerinin dosya ile tutarlı yedeklemesi.<br/><br/> Hyper-V ve VMware Linux konuk VM 'lerinin VM geri yüklemesi.
**Linux Azure VM 'lerini yedeklemek için MABS veya DPM kullanma** | Desteklenmez.

## <a name="daylight-saving-time-support"></a>Gün ışığından yararlanma süresi desteği

Azure Backup, Azure VM yedeklemeleri için gün ışığından yararlanma saatine yönelik otomatik saat ayarlamasını desteklemez. Yedeklemenin saatini ileriye veya geriye doğru kaydıramaz. Yedeklemenin istenen zamanda çalıştığından emin olmak için yedekleme ilkelerini gerektiği şekilde el ile değiştirin.

## <a name="disk-deduplication-support"></a>Disk yinelenenleri kaldırma desteği

Disk yinelenenleri kaldırma desteği aşağıdaki gibidir:

- Windows çalıştıran Hyper-V VM 'lerini yedeklemek için DPM veya MABS kullandığınızda, diski kaldırma işlemi şirket içinde desteklenir. Windows Server, sanal sabit diskler (VHD) üzerinde, yedekleme depolama alanı olarak VM 'ye bağlı yinelenen verileri kaldırma (konak düzeyinde) gerçekleştirir.
- Yinelenenleri kaldırma, Azure 'da herhangi bir yedekleme bileşeni için desteklenmez. Azure 'da DPM ve MABS dağıtıldığında, VM 'ye bağlı depolama diskleri çoğaltılamaz.

## <a name="security-and-encryption-support"></a>Güvenlik ve şifreleme desteği

Azure Backup, geçiş sırasında ve bekleyen veriler için şifrelemeyi destekler.

### <a name="network-traffic-to-azure"></a>Azure 'a ağ trafiği

- Sunuculardan kurtarma hizmetleri kasasına yedekleme trafiği Gelişmiş Şifreleme Standardı 256 kullanılarak şifrelenir.
- Yedekleme verileri güvenli bir HTTPS bağlantısı üzerinden gönderilir.

### <a name="data-security"></a>Veri güvenliği

- Yedekleme verileri, kurtarma hizmetleri kasasında şifrelenmiş biçimde depolanır.
- MARS aracısına sahip şirket içi sunuculardan veri yedeklendiğinde, veriler Azure Backup yüklenmeden önce bir parola ile şifrelenir ve yalnızca Azure Backup indirildikten sonra şifresi çözülür.
- Azure VM 'lerini yedekliyorsanız, sanal makine *içinde* şifrelemeyi ayarlamanız gerekir.
- Azure Backup, Windows sanal makinelerde BitLocker, Linux sanal makinelerde ise **dm-crypt** kullanan Azure Disk Şifrelemesi özelliğini destekler.
- Arka uçta Azure Backup, bekleyen verileri koruyan [Azure depolama hizmeti şifrelemesi](../storage/common/storage-service-encryption.md)kullanır.

**Makine** | **Yoldaki** | **Bekleyen**
--- | --- | ---
**DPM/MABS olmayan şirket içi Windows makineleri** | ![Yes][green] | ![Yes][green]
**Azure VM’leri** | ![Yes][green] | ![Yes][green]
**DPM ile şirket içi Windows makineleri veya Azure VM 'Leri** | ![Yes][green] | ![Yes][green]
**Şirket içi Windows makineleri veya MABS ile Azure VM 'Leri** | ![Yes][green] | ![Yes][green]

## <a name="compression-support"></a>Sıkıştırma desteği

Yedekleme, aşağıdaki tabloda özetlenen yedekleme trafiğinin sıkıştırmasını destekler.

- Azure VM 'Leri için VM uzantısı, verileri doğrudan depolama ağı üzerinden Azure Storage hesabından okur, bu nedenle bu trafiği sıkıştırmak gerekli değildir.
- DPM veya MABS kullanıyorsanız, verileri yedeklenmeden önce sıkıştırarak bant genişliği tasarrufu yapabilirsiniz.

**Makine** | **MABS/DPM 'ye Sıkıştır (TCP)** | **Kasaya Sıkıştır (HTTPS)**
--- | --- | ---
**Şirket içi Windows makinelerini doğrudan yedekleme** | NA | ![Yes][green]
**VM uzantısı kullanarak Azure VM 'lerini yedekleme** | NA | NA
**MABS/DPM kullanarak şirket içi/Azure makinelerinde yedekleme** | ![Yes][green] | ![Yes][green]

## <a name="retention-limits"></a>Bekletme sınırları

**Ayar** | **Sınırlar**
--- | ---
**Korumalı örnek başına en fazla kurtarma noktası (makine veya iş yükü)** | 9.999
**Kurtarma noktası için en uzun süre sonu zamanı** | Sınır yok
**DPM/MABS için maksimum yedekleme sıklığı** | SQL Server için 15 dakikada bir<br/><br/> Diğer iş yükleri için saatte bir
**Kasadan maksimum yedekleme sıklığı** | Şirket **Içi Windows makineleri veya Mars çalıştıran Azure VM 'leri:** Günde üç<br/><br/> **DPM/MABS:** Günde iki<br/><br/> **Azure VM yedeklemesi:** Günde bir
**Kurtarma noktası bekletme** | Günlük, haftalık, aylık, yıllık
**En uzun bekletme süresi** | Yedekleme sıklığına bağlıdır
**DPM/MABS diskindeki kurtarma noktaları** | dosya sunucuları için 64; uygulama sunucuları için 448 <br/><br/>Şirket içi DPM için sınırsız bant kurtarma noktası

## <a name="cross-region-restore"></a>Çapraz bölge geri yükleme

Azure Backup, veri kullanılabilirliğini ve dayanıklılık özelliğini güçlendirin ve verileri ikincil bir bölgeye geri yüklemek için tam denetim sağlayan çapraz bölge geri yükleme özelliğini ekledi. Bu özelliği yapılandırmak için [çapraz bölge geri yüklemeyi ayarla makalesini](backup-create-rs-vault.md#set-cross-region-restore)ziyaret edin. Bu özellik aşağıdaki yönetim türleri için desteklenir:

| Yedekleme yönetimi türü | Desteklenir                                                    | Desteklenen bölgeler |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Hem yönetilen hem de yönetilmeyen diskler ile Azure VM 'Leri (şifreli Azure VM 'Leri dahil) için desteklenir. Klasik VM 'Ler için desteklenmez. | Fransa Orta, Avustralya Orta, Güney Afrika Kuzey, BAE Kuzey, İsviçre Kuzey, Almanya Orta Batı, Norveç Doğu, UG IOWA ve UG Virginia hariç tüm Azure genel bölgelerinde ve bağımsız bölgelerde kullanılabilir. <br>Bu bölgelerde kullanım hakkında daha fazla bilgi için, iletişim kurun [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| SQL/SAP HANA | Önizlemede                                                      | Fransa Orta, Avustralya Orta, Güney Afrika Kuzey, BAE Kuzey, İsviçre Kuzey, Almanya Orta Batı, Norveç Doğu, UG IOWA ve UG Virginia hariç tüm Azure genel bölgelerinde ve bağımsız bölgelerde kullanılabilir. <br>Bu bölgelerde kullanım hakkında daha fazla bilgi için, iletişim kurun [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| MARS Aracısı/şirket içi  | Hayır                                                           | Yok               |
| AFS (Azure dosya paylaşımları)                 | Hayır                                                           | Yok               |

## <a name="next-steps"></a>Sonraki adımlar

- Azure VM yedeklemesi için [destek matrisini gözden geçirin](backup-support-matrix-iaas.md) .

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
---
title: Azure Backup destek matrisi
description: Azure Backup hizmeti için destek ayarlarının ve sınırlamaların özetini sağlar.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: 2c33c71e579cc6fa5d01ba086fb1a9a4fc9c142c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172079"
---
# <a name="support-matrix-for-azure-backup"></a>Azure Backup için destek matrisi

[Azure Backup](backup-overview.md) , verileri Microsoft Azure bulut platformuna yedeklemek için kullanabilirsiniz. Bu makalede Azure Backup senaryolar ve dağıtımlar için genel destek ayarları ve sınırlamaları özetlenmektedir.

Diğer destek matrisleri mevcuttur:

- [Azure sanal makinesi (VM) yedeklemesi](backup-support-matrix-iaas.md) için destek matrisi
- [System Center Data Protection Manager (DPM)/Microsoft Azure Backup sunucusu (MABS)](backup-support-matrix-mabs-dpm.md) kullanarak yedekleme için destek matrisi
- [Microsoft Azure kurtarma hizmetleri (mars) Aracısı](backup-support-matrix-mars-agent.md) kullanılarak yedekleme için destek matrisi

## <a name="vault-support"></a>Kasa desteği

Azure Backup yedeklemeleri düzenlemek ve yönetmek için kurtarma hizmetleri kasalarını kullanır. Ayrıca, yedeklenen verileri depolamak için kasalarını kullanır.

Aşağıdaki tabloda, kurtarma hizmetleri kasalarının özellikleri açıklanmaktadır:

**Özelliği** | **Ayrıntılar**
--- | ---
**Abonelikte kasa** | Tek bir abonelikte en fazla 500 kurtarma hizmeti Kasası.
**Kasadaki makineler** | Tek bir kasada en fazla 1.000 Azure VM.<br/><br/> 50 MABS sunucusuna kadar tek bir kasada kayıt yapılabilir.
**Kasa depolamadaki veri kaynakları** | Maksimum 54.400 GB. Azure VM yedeklemeleri için sınır yoktur.
**Kasaya yedeklemeler** | **Azure VM 'leri:** Günde bir kez.<br/><br/>**DPM/MABS tarafından korunan makineler:** Günde iki kez.<br/><br/> **Mars Aracısı kullanılarak doğrudan yedeklenen makineler:** Günde üç kez.
**Kasaların arasındaki yedeklemeler** | Yedekleme bir bölgede yer alan.<br/><br/> Yedeklemek istediğiniz VM 'Leri içeren her Azure bölgesinde bir kasaya ihtiyacınız vardır. Farklı bir bölgeye yedekleme yapamazsınız.
**Kasaları taşı** | Kasalarını abonelikler arasında veya aynı abonelikteki kaynak grupları arasında [taşıyabilirsiniz](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) .
**Kasaları arasında veri taşıma** | Yedeklenen verilerin kasaların arasına taşınması desteklenmez.
**Kasa depolama türünü değiştir** | Yedeklemeler depolanmadan önce bir kasa için depolama çoğaltma türünü (coğrafi olarak yedekli depolama veya yerel olarak yedekli depolama) değiştirebilirsiniz. Yedekler kasada başladıktan sonra çoğaltma türü değiştirilemez.

## <a name="on-premises-backup-support"></a>Şirket içi yedekleme desteği

Şirket içi makineleri yedeklemek istiyorsanız bu adım desteklenir:

**Makin** | **Yedeklenen Özellikler** | **Konum** | **Özellikler**
--- | --- | --- | ---
**Windows makinenin MARS aracısıyla doğrudan yedeklenmesi** | Dosyalar, klasörler, sistem durumu | Kurtarma Hizmetleri kasasına yedekleyin. | Günde üç kez yedekleme<br/><br/> Uygulamayla uyumlu yedekleme yok<br/><br/> Dosya, klasör, birim geri yükleme
**MARS Aracısı ile Linux makinenin doğrudan yedeklemesi** | Yedekleme desteklenmiyor
**DPM 'ye yedekleme** | Dosyalar, klasörler, birimler, sistem durumu, uygulama verileri | Yerel DPM depolamasına yedekleme. DPM daha sonra kasaya yedekler. | Uygulama kullanan anlık görüntüler<br/><br/> Yedekleme ve kurtarma için tam ayrıntı düzeyi<br/><br/> VM 'Ler için desteklenen Linux (Hyper-V/VMware)<br/><br/> Oracle desteklenmiyor
**MABS 'e yedekleme** | Dosyalar, klasörler, birimler, sistem durumu, uygulama verileri | MABS yerel depolama alanına yedekleme. MABS daha sonra kasaya yedekler. | Uygulama kullanan anlık görüntüler<br/><br/> Yedekleme ve kurtarma için tam ayrıntı düzeyi<br/><br/> VM 'Ler için desteklenen Linux (Hyper-V/VMware)<br/><br/> Oracle desteklenmiyor

## <a name="azure-vm-backup-support"></a>Azure VM yedekleme desteği

### <a name="azure-vm-limits"></a>Azure VM sınırları

**Sınır** | **Ayrıntılar**
--- | ---
**Azure VM veri diskleri** | 16 sınırı
**Azure VM veri diski boyutu** | Tek bir disk boyutu 32 TB 'a kadar ve bir VM 'deki tüm diskler için en fazla 256 TB olabilir.

### <a name="azure-vm-backup-options"></a>Azure VM yedekleme seçenekleri

Azure VM 'lerini yedeklemek istiyorsanız bu destek aşağıda verilmiştir:

**Makin** | **Yedeklenen Özellikler** | **Konum** | **Özellikler**
--- | --- | --- | ---
**VM uzantısı kullanarak Azure VM yedeklemesi** | Tüm VM | Kasaya yedekleyin. | Bir VM için yedeklemeyi etkinleştirdiğinizde uzantı yüklendi.<br/><br/> Günde bir kez yedekleyin.<br/><br/> Windows VM 'Leri için uygulamayla uyumlu yedekleme; Linux VM 'Ler için dosya tutarlı yedekleme. Linux makineler için uygulama tutarlılığını, Özel betikler kullanarak yapılandırabilirsiniz.<br/><br/> VM 'yi veya diski geri yükleyin.<br/><br/> Bir Azure VM 'yi şirket içi bir konuma yedekleyemiyorum.
**MARS Aracısı kullanarak Azure VM yedeklemesi** | Dosyalar, klasörler, sistem durumu | Kasaya yedekleyin. | Günde üç kez yedekleyin.<br/><br/> Tüm VM yerine belirli dosya veya klasörleri yedeklemek istiyorsanız, MARS Aracısı VM uzantısıyla birlikte çalışabilir.
**DPM ile Azure VM** | Dosyalar, klasörler, birimler, sistem durumu, uygulama verileri | DPM çalıştıran Azure VM 'nin yerel depolama birimine yedekleme. DPM daha sonra kasaya yedekler. | Uygulama kullanan anlık görüntüler.<br/><br/> Yedekleme ve kurtarma için tam ayrıntı düzeyi.<br/><br/> VM 'Ler için desteklenen Linux (Hyper-V/VMware).<br/><br/> Oracle desteklenmiyor.
**MABS ile Azure VM** | Dosyalar, klasörler, birimler, sistem durumu, uygulama verileri | MABS çalıştıran Azure VM 'nin yerel depolama birimine yedekleme. MABS daha sonra kasaya yedekler. | Uygulama kullanan anlık görüntüler.<br/><br/> Yedekleme ve kurtarma için tam ayrıntı düzeyi.<br/><br/> VM 'Ler için desteklenen Linux (Hyper-V/VMware).<br/><br/> Oracle desteklenmiyor.

## <a name="linux-backup-support"></a>Linux yedekleme desteği

Linux makinelerini yedeklemek istiyorsanız, bu destek aşağıda verilmiştir:

**Yedekleme türü** | **Linux (Azure onaysed)**
--- | ---
**Linux çalıştıran şirket içi makinenin doğrudan yedeklemesi** | Desteklenmiyor. MARS Aracısı yalnızca Windows makinelerine yüklenebilir.
**Linux çalıştıran Azure VM 'yi yedeklemek için aracı uzantısı kullanma** | [Özel betikler](backup-azure-linux-app-consistent.md)kullanarak uygulamayla tutarlı yedekleme.<br/><br/> Dosya düzeyinde kurtarma.<br/><br/> Kurtarma noktasından veya diskten bir VM oluşturarak geri yükleyin.
**Linux çalıştıran şirket içi veya Azure VM 'yi yedeklemek için DPM 'yi kullanma** | Hyper-V ve VMWare üzerinde Linux konuk VM 'lerinin dosya ile tutarlı yedeklemesi.<br/><br/> Hyper-V ve VMWare Linux konuk VM 'lerinin VM geri yüklemesi.<br/><br/> Dosya tutarlı yedekleme, Azure VM için kullanılamaz.
**Linux çalıştıran şirket içi makineyi veya Azure VM 'yi yedeklemek için MABS kullanma** | Hyper-V ve VMWare üzerinde Linux konuk VM 'lerinin dosya ile tutarlı yedeklemesi.<br/><br/> Hyper-V ve VMWare Linux konuk VM 'lerinin VM geri yüklemesi.<br/><br/> Dosya tutarlı yedekleme, Azure VM 'Leri için kullanılamaz.

## <a name="daylight-saving-time-support"></a>Gün ışığından yararlanma süresi desteği

Azure Backup, Azure VM yedeklemeleri için gün ışığından yararlanma saatine yönelik otomatik saat ayarlamasını desteklemez. Yedekleme ilkelerini gerektiği şekilde el ile değiştirin.

## <a name="disk-deduplication-support"></a>Disk yinelenenleri kaldırma desteği

Disk yinelenenleri kaldırma desteği aşağıdaki gibidir:

- Windows çalıştıran Hyper-V VM 'lerini yedeklemek için DPM veya MABs kullandığınızda, diski kaldırma işlemi şirket içinde desteklenir. Windows Server, sanal sabit diskler (VHD) üzerinde, yedekleme depolama alanı olarak VM 'ye bağlı yinelenen verileri kaldırma (konak düzeyinde) gerçekleştirir.
- Yinelenenleri kaldırma, Azure 'da herhangi bir yedekleme bileşeni için desteklenmez. Azure 'da DPM ve MABS dağıtıldığında, VM 'ye bağlı depolama diskleri çoğaltılamaz.

## <a name="security-and-encryption-support"></a>Güvenlik ve şifreleme desteği

Azure Backup, geçiş sırasında ve bekleyen veriler için şifrelemeyi destekler.

### <a name="network-traffic-to-azure"></a>Azure 'a ağ trafiği

- Sunuculardan kurtarma hizmetleri kasasına yedekleme trafiği Gelişmiş Şifreleme Standardı 256 kullanılarak şifrelenir.
- Yedekleme verileri güvenli bir HTTPS bağlantısı üzerinden gönderilir.
- Yedekleme verileri, kurtarma hizmetleri kasasında şifrelenmiş biçimde depolanır.
- Yalnızca bu verilerin kilidini açmak için bir parola vardır. Microsoft, herhangi bir noktada yedekleme verilerinin şifresini çözemez.

    > [!WARNING]
    > Kasayı ayarladıktan sonra yalnızca şifreleme anahtarına erişiminiz vardır. Microsoft hiçbir şekilde kopya korumaz ve anahtara erişemez. Anahtar yanlış olursa Microsoft, yedekleme verilerini kurtaramaz.

### <a name="data-security"></a>Veri güvenliği

- Azure VM 'lerini yedekliyorsanız, sanal makine *içinde* şifrelemeyi ayarlamanız gerekir.
- Azure Backup, Windows sanal makinelerde BitLocker, Linux sanal makinelerde ise **dm-crypt** kullanan Azure Disk Şifrelemesi özelliğini destekler.
- Arka uçta Azure Backup, bekleyen verileri koruyan [Azure depolama hizmeti şifrelemesi](../storage/common/storage-service-encryption.md)kullanır.

**Makin** | **Yoldaki** | **Bekleyen**
--- | --- | ---
**DPM/MABS olmayan şirket içi Windows makineleri** | ![Yes][green] | ![Yes][green]
**Azure VM 'Leri** | ![Yes][green] | ![Yes][green]
**DPM ile şirket içi Windows makineleri veya Azure VM 'Leri** | ![Yes][green] | ![Yes][green]
**Şirket içi Windows makineleri veya MABS ile Azure VM 'Leri** | ![Yes][green] | ![Yes][green]

## <a name="compression-support"></a>Sıkıştırma desteği

Yedekleme, aşağıdaki tabloda özetlenen yedekleme trafiğinin sıkıştırmasını destekler.

- Azure VM 'Leri için VM uzantısı, verileri doğrudan depolama ağı üzerinden Azure Storage hesabından okur, bu nedenle bu trafiği sıkıştırmak gerekli değildir.
- DPM veya MABS kullanıyorsanız, verileri yedeklenmeden önce sıkıştırarak bant genişliği tasarrufu yapabilirsiniz.

**Makin** | **MABS/DPM 'ye Sıkıştır (TCP)** | **Kasaya Sıkıştır (HTTPS)**
--- | --- | ---
**Şirket içi Windows makinelerini doğrudan yedekleme** | NA | ![Yes][green]
**VM uzantısı kullanarak Azure VM 'lerini yedekleme** | NA | NA
**MABS/DPM kullanarak şirket içi/Azure makinelerinde yedekleme** | ![Yes][green] | ![Yes][green]

## <a name="retention-limits"></a>Bekletme sınırları

**Ayar** | **Limitler**
--- | ---
**Korumalı örnek başına en fazla kurtarma noktası (makine veya iş yükü)** | 9\.999
**Kurtarma noktası için en uzun süre sonu zamanı** | Sınırsız
**DPM/MABS için maksimum yedekleme sıklığı** | SQL Server için 15 dakikada bir<br/><br/> Diğer iş yükleri için saatte bir
**Kasadan maksimum yedekleme sıklığı** | Şirket **Içi Windows makineleri veya Mars çalıştıran Azure VM 'leri:** Günde üç<br/><br/> **DPM/MABS:** Günde iki<br/><br/> **Azure VM yedeklemesi:** Günde bir
**Kurtarma noktası bekletme** | Günlük, haftalık, aylık, yıllık
**Maksimum bekletme süresi** | Yedekleme sıklığına bağlıdır
**DPM/MABS diskindeki kurtarma noktaları** | dosya sunucuları için 64; uygulama sunucuları için 448 <br/><br/>Şirket içi DPM için sınırsız bant kurtarma noktası

## <a name="next-steps"></a>Sonraki adımlar

- Azure VM yedeklemesi için [destek matrisini gözden geçirin](backup-support-matrix-iaas.md) .

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

---
title: Mimariye genel bakış
description: Azure Backup hizmeti tarafından kullanılan mimariye, bileşenlere ve işlemlere genel bir bakış sağlar.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: ae7b0c2b81bd3d393b7e749e077a6f5fa0379562
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173516"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup mimarisi ve bileşenler

Microsoft Azure bulut platformuna veri yedeklemek için [Azure Backup hizmeti](backup-overview.md) kullanabilirsiniz. Bu makalede Azure Backup mimarisi, bileşenler ve süreçler özetlenmektedir.

## <a name="what-does-azure-backup-do"></a>Azure Backup ne yapar?

Azure Backup, şirket içi makinelerde ve Azure sanal makine (VM) örneklerinde çalışan verileri, makine durumunu ve iş yüklerini yedekler. Birçok Azure Backup senaryo vardır.

## <a name="how-does-azure-backup-work"></a>Azure Backup nasıl çalışır?

Makineleri ve verileri bir dizi yöntemi kullanarak yedekleyebilirsiniz:

- **Şirket içi makineleri yedekleme**:
  - Şirket içi Windows makinelerini, Azure Backup Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı 'nı kullanarak doğrudan Azure 'a yedekleyebilirsiniz. Linux makineleri desteklenmez.
  - Şirket içi makineleri, System Center Data Protection Manager (DPM) veya Microsoft Azure Backup sunucusu (MABS) olan bir yedekleme sunucusuna yedekleyebilirsiniz. Daha sonra yedekleme sunucusunu Azure 'da bir kurtarma hizmetleri kasasına yedekleyebilirsiniz.

- **Azure VM 'Lerini yedekleme**:
  - Azure VM 'lerini doğrudan yedekleyebilirsiniz. Azure Backup, VM üzerinde çalışan Azure VM aracısına bir yedekleme uzantısı yüklüyor. Bu uzantı tüm VM 'yi yedekler.
  - MARS Aracısı 'nı çalıştırarak, Azure VM 'de belirli dosya ve klasörleri yedekleyebilirsiniz.
  - Azure VM 'lerini Azure 'da çalışan MABS 'lere yedekleyebilir ve ardından MABS 'yi bir kurtarma hizmetleri kasasına yedekleyebilirsiniz.

[Neleri yedekleyebileceğiniz](backup-overview.md) ve [Desteklenen yedekleme senaryoları](backup-support-matrix.md)hakkında daha fazla bilgi edinin.

## <a name="where-is-data-backed-up"></a>Veriler nerede yedeklenir?

Azure Backup, bir kurtarma hizmetleri kasasında yedeklenen verileri depolar. Kasa, Azure 'da Yedekleme kopyaları, kurtarma noktaları ve yedekleme ilkeleri gibi verileri tutmak için kullanılan çevrimiçi bir depolama varlıktır.

Kurtarma Hizmetleri kasaları aşağıdaki özelliklere sahiptir:

- Kasaları, yedekleme verilerinizi düzenlemenizi kolaylaştırır, yönetim yükünü en aza indirir.
- Her bir Azure aboneliğinde en fazla 500 kasa oluşturabilirsiniz.
- Azure VM 'Leri ve şirket içi makineler dahil olmak üzere bir kasadaki yedeklenen öğeleri izleyebilirsiniz.
- Azure [rol tabanlı erişim denetimi (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)ile kasa erişimini yönetebilirsiniz.
- Kasadaki verilerin artıklık için nasıl çoğaltılacağı belirtirsiniz:
  - **Yerel olarak yedekli depolama (LRS)** : bir veri merkezindeki hataya karşı korumak için LRS kullanabilirsiniz. LRS, verileri bir depolama ölçek birimine çoğaltır. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **Coğrafi olarak yedekli depolama (GRS)** : bölge genelinde kesintilere karşı koruma sağlamak için GRS kullanabilirsiniz. GRS, verilerinizi ikincil bir bölgeye çoğaltır. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Varsayılan olarak, kurtarma hizmetleri kasaları GRS kullanır.

## <a name="backup-agents"></a>Yedekleme aracıları

Azure Backup, ne tür bir makinenin yedeklendiğine bağlı olarak farklı yedekleme aracıları sağlar:

**Aracı** | **Ayrıntılar**
--- | ---
**MARS Aracısı** | <ul><li>Dosyaları, klasörleri ve sistem durumunu yedeklemek için tek bir şirket içi Windows Server makinesi üzerinde çalışır.</li> <li>Dosyaları, klasörleri ve sistem durumunu yedeklemek için Azure VM 'lerde çalışır.</li> <li>DPM/mabs sunucularında DPM/MABS yerel depolama diskini Azure 'a yedeklemek için çalışır.</li></ul>
**Azure VM Uzantısı** | Azure VM 'lerde çalışarak bunları bir kasaya yedekler.

## <a name="backup-types"></a>Yedekleme türleri

Aşağıdaki tabloda farklı yedekleme türleri ve ne zaman kullanıldıkları açıklanmaktadır:

**Yedekleme türü** | **Ayrıntılar** | **Kullanım**
--- | --- | ---
**Tümünü** | Tam yedekleme, tüm veri kaynağını içerir. Değişiklik veya artımlı yedeklerden daha fazla ağ bant genişliği alır. | İlk yedekleme için kullanılır.
**Di** |  Değişiklik yedeklemesi, ilk tam yedeklemeden bu yana değiştirilen blokları depolar. Daha küçük bir ağ ve depolama alanı kullanır ve değiştirilmemiş verilerin gereksiz kopyalarını tutar.<br/><br/> Daha sonraki yedeklemeler arasında değişmeyen veri blokları aktarıldığından ve depolandığından, verimsiz. | Azure Backup tarafından kullanılmıyor.
**Kaydetmenin** | Artımlı yedekleme yalnızca önceki yedeklemeden bu yana değiştirilen veri bloklarını depolar. Yüksek depolama ve ağ verimliliği. <br/><br/> Artımlı yedekleme ile tam yedeklemeler ile ek bir şekilde erişmeniz gerekmez. | DPM/MABS tarafından Disk yedeklemeleri için kullanılır ve tüm Azure yedeklemelerinde kullanılır. SQL Server yedekleme için kullanılmıyor.

## <a name="sql-server-backup-types"></a>SQL Server yedekleme türleri

Aşağıdaki tabloda SQL Server veritabanları için kullanılan farklı yedekleme türleri ve ne sıklıkta kullanıldıkları açıklanmaktadır:

**Yedekleme türü** | **Ayrıntılar** | **Kullanım**
--- | --- | ---
**Tam yedekleme** | Tam bir veritabanı yedeklemesi tüm veritabanını yedekler. Belirli bir veritabanındaki veya bir dosya grubu ya da dosya kümesindeki tüm verileri içerir. Tam yedekleme Ayrıca bu verileri kurtarmak için yeterli günlük içerir. | En çok, günde bir tam yedekleme tetikleyebilirsiniz.<br/><br/> Günlük veya haftalık aralıkta tam yedekleme yapmayı tercih edebilirsiniz.
**Değişiklik yedekleme** | Değişiklik yedeklemesi, en son, önceki tam veri yedeklemesini temel alır.<br/><br/> Yalnızca tam yedeklemeden bu yana değiştirilen verileri yakalar. |  En çok, her gün bir değişiklik yedeklemesi tetikleyebilirsiniz.<br/><br/> Tam yedekleme ve değişiklik yedeklemesini aynı günde yapılandıramazsınız.
**İşlem günlüğü yedeklemesi** | Bir günlük yedeklemesi, belirli bir saniyeye kadar belirli bir noktaya geri yüklemeyi sağlar. | En çok, işlem günlüğü yedeklemelerini 15 dakikada bir yapılandırabilirsiniz.

### <a name="comparison-of-backup-types"></a>Yedekleme türlerini karşılaştırma

Depolama alanı tüketimi, kurtarma süresi hedefi (RTO) ve ağ tüketimi her bir yedekleme türü için farklılık gösterir. Aşağıdaki görüntüde yedekleme türleri karşılaştırması gösterilmektedir:

- A veri kaynağı, aylık olarak yedeklenen, a1-A10 olan 10 depolama blobundan oluşur.
- A2, A3, A4 ve A9 blokları ilk ayda, A5 bloku ise sonraki ayda değişmektedir.
- İkinci ayda değişiklik yedeklemeleri için a2, a3, A4 ve A9 blokları yedeklenir. Üçüncü ayda değiştirilen A5 blokuna ek olarak aynı bloklar tekrar yedeklenir. Sonraki tam yedeklemeye kadar değiştirilen bloklar yedeklenmeye devam eder.
- Artımlı yedeklemeler için, ikinci ayda, a2, a3, A4 ve A9 blokları değiştirilmiş ve aktarıldı olarak işaretlenir. Üçüncü ayda yalnızca değiştirilen A5 bloku işaretlenir ve aktarılır.

![Yedekleme yöntemlerinin karşılaştırmalarını gösteren resim](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Yedekleme Özellikleri

Aşağıdaki tabloda farklı yedekleme türleri için desteklenen özellikler özetlenmektedir:

**Özelliği** | **Dosya ve klasörlerin doğrudan yedeklemesi (MARS Aracısı 'nı kullanarak)** | **Azure VM yedeklemesi** | **DPM/MABS ile makineler veya uygulamalar**
--- | --- | --- | ---
Kasaya yedekleme | ![Yes][green] | ![Yes][green] | ![Yes][green]
DPM/MABS diskine ve ardından Azure 'a yedekleme | | | ![Yes][green]
Yedekleme için gönderilen verileri sıkıştır | ![Yes][green] | Veri aktarımı sırasında sıkıştırma kullanılmaz. Depolama biraz az, ancak geri yükleme daha hızlıdır.  | ![Yes][green]
Artımlı yedekleme Çalıştır |![Yes][green] |![Yes][green] |![Yes][green]
Yinelenenleri kaldırılmış diskleri yedekleme | | | ![Kısmi][yellow]<br/><br/> Yalnızca şirket içinde dağıtılan DPM/MABS sunucuları için.

![Tablo anahtarı](./media/backup-architecture/table-key.png)

## <a name="architecture-built-in-azure-vm-backup"></a>Mimari: yerleşik Azure VM yedeklemesi

1. Bir Azure VM için yedeklemeyi etkinleştirdiğinizde, belirttiğiniz zamanlamaya göre bir yedekleme çalışır.
1. İlk yedekleme sırasında VM çalışıyorsa VM 'ye bir yedekleme uzantısı yüklenir.
    - Windows VM 'Leri için VMSnapshot uzantısı yüklenir.
    - Linux VM 'Leri için VMSnapshot Linux uzantısı yüklenir.
1. Uzantı, depolama düzeyinde bir anlık görüntü alır.
    - Çalıştıran Windows VM 'Leri için, VM 'nin uygulamayla tutarlı bir anlık görüntüsünü almak üzere Windows Birim Gölge Kopyası Hizmeti (VSS) ile yedekleme koordinatları. Varsayılan olarak, yedekleme tam VSS yedeklemeleri alır. Yedekleme, uygulamayla tutarlı bir anlık görüntü alacağından, dosya ile tutarlı bir anlık görüntü alır.
    - Linux VM 'Leri için yedekleme, dosya ile tutarlı bir anlık görüntü alır. Uygulamayla tutarlı anlık görüntüler için, ön/son betikleri el ile özelleştirmeniz gerekir.
    - Yedekleme, her bir sanal makine diski paralel olarak yedeklenerek en iyi duruma getirilir. Yedeklenmekte olan her disk için Azure Backup disk üzerindeki blokları okur ve yalnızca değiştirilen verileri depolar.
1. Anlık görüntü alındıktan sonra veriler kasaya aktarılır.
    - Yalnızca son yedeklemeden bu yana değiştirilen veri blokları.
    - Veriler şifrelenmedi. Azure Backup, Azure disk şifrelemesi kullanılarak şifrelenmiş Azure VM 'Leri yedekleyebilir.
    - Anlık görüntü verileri hemen kasaya kopyalanmayabilir. Yoğun saatlerde yedekleme birkaç saat sürebilir. Bir VM için toplam Yedekleme süresi, günlük yedekleme ilkeleri için 24 saatten daha az olacaktır.
1. Veriler kasaya gönderildikten sonra bir kurtarma noktası oluşturulur. Varsayılan olarak, anlık görüntüler silinmeden önce iki gün boyunca tutulur. Bu özellik bu anlık görüntülerden geri yükleme işlemine izin verir ve bu sayede geri yükleme sürelerini kapatır. Verileri kasadan geri dönüştürmek ve kopyalamak için gereken süreyi azaltır. Bkz. [Azure Backup Instant Restore özelliği](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).

Azure VM 'lerinin denetim komutlarına internet erişimi olması gerekir. VM içindeki iş yüklerini yedekliyorsanız (örneğin, veritabanı yedeklemeleri SQL Server), arka uç verilerinde de internet erişimi gerekir.

![Azure VM 'lerinin yedeklenmesi](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Mimari: şirket içi Windows Server makinelerini veya Azure VM dosyalarını veya klasörlerini doğrudan yedekleyin

1. Senaryoyu ayarlamak için MARS aracısını makineye indirip yüklersiniz. Ardından, yedeklemelerin ne zaman çalışacağını ve Azure 'da ne kadar süreyle saklanacağını seçersiniz.
1. İlk yedekleme, yedekleme ayarlarınıza göre çalışır.
1. MARS Aracısı, yedekleme için seçilen birimlerin bir noktadan noktaya anlık görüntüsünü almak için VSS 'yi kullanır.
    - MARS Aracısı, anlık görüntüyü yakalamak için yalnızca Windows sistem yazma işlemini kullanır.
    - Aracı herhangi bir uygulama VSS yazıcısını kullanmadığından, uygulamayla tutarlı anlık görüntüleri yakalamaz.
1. VSS ile anlık görüntüyü aldıktan sonra, MARS Aracısı, yedeklemeyi yapılandırdığınız sırada belirttiğiniz önbellek klasöründe bir sanal sabit disk (VHD) oluşturur. Aracı Ayrıca her veri bloğu için sağlama toplamlarını depolar.
1. Artımlı yedeklemeler, isteğe bağlı yedekleme çalıştırmadığınız takdirde belirttiğiniz zamanlamaya göre çalışır.
1. Artımlı yedeklemelerde değiştirilen dosyalar tanımlanır ve yeni bir VHD oluşturulur. VHD sıkıştırılır ve şifrelenir ve ardından kasaya gönderilir.
1. Artımlı yedekleme bittikten sonra, yeni VHD ilk çoğaltmadan sonra oluşturulan VHD ile birleştirilir. Bu birleştirilmiş VHD, devam eden yedekleme için kullanılmak üzere en son durumu sağlar.

![MARS Aracısı ile şirket içi Windows Server makinelerini yedekleme](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Mimari: DPM/MABS 'e yedekleme

1. Korumak istediğiniz makinelere DPM veya MABS koruma aracısını yüklersiniz. Ardından makineleri bir DPM koruma grubuna eklersiniz.
    - Şirket içi makineleri korumak için DPM veya MABS sunucusu şirket içinde bulunmalıdır.
    - Azure VM 'lerini korumak için, MABS sunucusu Azure 'da, Azure VM olarak çalışıyor olmalıdır.
    - DPM/MABS ile yedekleme birimlerini, paylaşımları, dosyaları ve klasörleri koruyabilirsiniz. Ayrıca, bir makinenin sistem durumunu koruyabilir (çıplak) ve uygulama kullanan yedekleme ayarlarıyla belirli uygulamaları koruyabilirsiniz.
1. DPM/MABS içindeki bir makine ya da uygulama için koruma ayarlarken, kısa vadeli depolama için MABS/DPM yerel diskine ve çevrimiçi koruma için Azure 'a yedeklemeyi seçersiniz. Ayrıca yerel DPM/MABS depolaması için yedeklemenin ne zaman çalışacağını ve Azure 'a çevrimiçi yedeklemenin ne zaman çalışacağını de belirtirsiniz.
1. Korunan iş yükünün diski, belirttiğiniz zamanlamaya göre yerel MABS/DPM disklerine yedeklenir.
1. DPM/MABS diskleri, DPM/MABS sunucusunda çalışan MARS Aracısı tarafından kasaya yedeklenir.

![DPM veya MABS tarafından korunan makinelerin ve iş yüklerinin yedeklenmesi](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM depolaması

Azure VM 'Leri, işletim sistemlerini, uygulamalarını ve verilerini depolamak için diskleri kullanır. Her Azure sanal makinesinde en az iki disk vardır: işletim sistemi ve geçici disk için bir disk. Azure VM 'Leri, uygulama verileri için veri disklerine da sahip olabilir. Diskler VHD olarak depolanır.

- VHD 'ler, Azure 'da standart veya Premium Depolama hesaplarında sayfa Blobları olarak depolanır:
  - **Standart depolama:** Gecikme süresine duyarlı olmayan iş yüklerini çalıştıran VM 'Ler için güvenilir, düşük maliyetli disk desteği. Standart depolama, standart katı hal sürücüsü (SSD) disklerini veya standart sabit disk sürücüsü (HDD) disklerini kullanabilir.
  - **Premium Depolama:** Yüksek performanslı disk desteği. Premium SSD disklerini kullanır.
- Diskler için farklı performans katmanları vardır:
  - **Standart HDD Disk:** HDD 'Ler tarafından desteklenir ve uygun maliyetli depolama için kullanılır.
  - **Standart SSD disk:** Premium SSD disklerinin ve standart HDD disklerinin öğelerini birleştirir. HDD 'den daha tutarlı performans ve güvenilirlik sunar, ancak yine de ekonomik maliyetli olur.
  - **Premium SSD disk:** SSD 'ler tarafından desteklenir ve g/ç yoğunluklu iş yükleri çalıştıran VM 'Ler için yüksek performanslı ve düşük gecikme süresi sağlar.
- Diskler yönetilebilir veya yönetilmeyen olabilir:
  - **Yönetilmeyen diskler:** VM 'Ler tarafından kullanılan geleneksel disk türü. Bu diskler için kendi depolama hesabınızı oluşturur ve diski oluştururken belirtirsiniz. Daha sonra sanal makinelerinize yönelik depolama kaynaklarını en üst düzeye çıkarmayı bilmeniz gerekir.
  - **Yönetilen diskler:** Azure depolama hesaplarını sizin için oluşturur ve yönetir. Disk boyutunu ve performans katmanını belirtirsiniz ve Azure yönetilen diskleri sizin için oluşturur. Diskler eklerken ve VM 'Leri ölçeklendirerek, Azure depolama hesaplarını işler.

Disk depolama ve VM 'Ler için kullanılabilir disk türleri hakkında daha fazla bilgi için şu makalelere bakın:

- [Windows VM 'Leri için Azure yönetilen diskler](../virtual-machines/windows/managed-disks-overview.md)
- [Linux sanal makineleri için Azure yönetilen diskler](../virtual-machines/linux/managed-disks-overview.md)
- [VM 'Ler için kullanılabilir disk türleri](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Premium Depolama ile Azure VM 'lerini yedekleme ve geri yükleme

Azure Backup ile Premium depolamayı kullanarak Azure VM 'Leri yedekleyebilirsiniz:

- VM 'Leri Premium Depolama ile yedekleme işlemi sırasında, yedekleme hizmeti depolama hesabında *AzureBackup*adlı geçici bir hazırlama konumu oluşturur. Hazırlama konumunun boyutu, kurtarma noktası anlık görüntüsünün boyutuna eşittir.
- Premium depolama hesabının geçici hazırlama konumuna uyum sağlamak için yeterli boş alana sahip olduğundan emin olun. [Daha fazla bilgi edinin](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). Hazırlama konumunu değiştirmeyin.
- Yedekleme işi tamamlandıktan sonra, hazırlama konumu silinir.
- Hazırlama konumu için kullanılan depolama fiyatı, [Premium Depolama fiyatlandırmasıyla](../virtual-machines/windows/disks-types.md#billing)tutarlıdır.

Premium depolamayı kullanarak Azure VM 'lerini geri yüklediğinizde, bunları Premium veya standart depolamaya geri yükleyebilirsiniz. Genellikle bunları Premium depolamaya geri yüklemeniz gerekir. Ancak, VM 'den yalnızca bir dosya alt kümesine ihtiyacınız varsa, bunları standart depolamaya geri yüklemek uygun maliyetli olabilir.

### <a name="back-up-and-restore-managed-disks"></a>Yönetilen diskleri yedekleme ve geri yükleme

Yönetilen disklerle Azure VM 'Leri yedekleyebilirsiniz:

- Yönetilen disklere sahip VM 'Leri, diğer Azure sanal makinelerini yaptığınız şekilde yedekleyebilirsiniz. VM 'yi doğrudan sanal makine ayarlarından yedekleyebilir veya kurtarma hizmetleri kasasındaki VM 'Ler için yedeklemeyi etkinleştirebilirsiniz.
- Yönetilen diskler üzerindeki VM'ler, yönetilen diskler üzerinde oluşturulmuş RestorePoint koleksiyonları ile yedeklenebilir.
- Azure Backup Ayrıca, Azure disk şifrelemesi kullanılarak şifrelenmiş yönetilen disklerle VM 'Lerin yedeklenmesini destekler.

VM 'Leri yönetilen disklere geri yüklerken, yönetilen disklere veya bir depolama hesabına sahip sanal makineye geri yükleme yapabilirsiniz:

- Geri yükleme işlemi sırasında, Azure yönetilen diskleri işler. Depolama hesabı seçeneğini kullanıyorsanız, geri yükleme işlemi sırasında oluşturulan depolama hesabını yönetirsiniz.
- Şifrelenen bir yönetilen VM 'yi geri yüklerseniz, geri yükleme işlemine başlamadan önce VM 'nin anahtar kasasında ve gizli dizilerde bulunduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Yedekleme senaryolarında desteklenen özellikler ve sınırlamalar hakkında bilgi edinmek](backup-support-matrix.md)için destek matrisini gözden geçirin.
- Aşağıdaki senaryolardan biri için Yedeklemeyi ayarlayın:
  - [Azure VM 'Lerini yedekleyin](backup-azure-arm-vms-prepare.md).
  - [Windows makinelerini](tutorial-backup-windows-server-to-azure.md), yedekleme sunucusu olmadan doğrudan yedekleyin.
  - Azure 'a yedekleme için [MABS ayarlayın](backup-azure-microsoft-azure-backup.md) ve sonra iş yüklerini mabs 'a yedekleyin.
  - Azure 'a yedekleme için [DPM 'Yi ayarlayın](backup-azure-dpm-introduction.md) ve sonra Iş yüklerini DPM 'ye yedekleyin.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

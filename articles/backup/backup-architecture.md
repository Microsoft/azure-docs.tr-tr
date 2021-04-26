---
title: Mimariye Genel Bakış
description: Azure Backup hizmeti tarafından kullanılan mimariye, bileşenlere ve işlemlere genel bir bakış sağlar.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 8fca05f8718fc5e44da33b19447895f5daafc905
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716759"
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

Azure Backup, yedekleme ve kurtarma hizmetleri kasalarında ve yedeklemelerdeki yedeklenen verileri depolar. Kasa, Azure 'da Yedekleme kopyaları, kurtarma noktaları ve yedekleme ilkeleri gibi verileri tutmak için kullanılan çevrimiçi bir depolama varlıktır.

Kasaların aşağıdaki özellikleri vardır:

- Kasaları, yedekleme verilerinizi düzenlemenizi kolaylaştırır, yönetim yükünü en aza indirir.
- Azure VM 'Leri ve şirket içi makineler dahil olmak üzere bir kasadaki yedeklenen öğeleri izleyebilirsiniz.
- [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)ile kasa erişimini yönetebilirsiniz.
- Kasadaki verilerin artıklık için nasıl çoğaltılacağı belirtirsiniz:
  - **Yerel olarak yedekli depolama (LRS)**: bir veri merkezindeki hataya karşı korumak için LRS kullanabilirsiniz. LRS, verileri bir depolama ölçek birimine çoğaltır. [Daha fazla bilgi edinin](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Coğrafi olarak yedekli depolama (GRS)**: bölge genelinde kesintilere karşı koruma sağlamak için GRS kullanabilirsiniz. GRS, verilerinizi ikincil bir bölgeye çoğaltır. [Daha fazla bilgi edinin](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - Bölgesel olarak **yedekli depolama (ZRS)**: verileri [kullanılabilirlik bölgelerinde](../availability-zones/az-overview.md#availability-zones)çoğaltır, bu da verileri, aynı bölgedeki veri fazlalığını ve dayanıklılığı garanti altına alır. [Daha fazla bilgi edinin](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Varsayılan olarak, kurtarma hizmetleri kasaları GRS kullanır.

Kurtarma Hizmetleri kasaları aşağıdaki ek özelliklere sahiptir:

- Her bir Azure aboneliğinde en fazla 500 kasa oluşturabilirsiniz.

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
**Tam** | Tam yedekleme, tüm veri kaynağını içerir. Değişiklik veya artımlı yedeklerden daha fazla ağ bant genişliği alır. | İlk yedekleme için kullanılır.
**Di** |  Değişiklik yedeklemesi, ilk tam yedeklemeden bu yana değiştirilen blokları depolar. Daha küçük bir ağ ve depolama alanı kullanır ve değiştirilmemiş verilerin gereksiz kopyalarını tutar.<br/><br/> Daha sonraki yedeklemeler arasında değişmeyen veri blokları aktarıldığından ve depolandığından, verimsiz. | Azure Backup tarafından kullanılmıyor.
**Artımlı** | Artımlı yedekleme yalnızca önceki yedeklemeden bu yana değiştirilen veri bloklarını depolar. Yüksek depolama ve ağ verimliliği. <br/><br/> Artımlı yedekleme ile tam yedeklemeler ile ek bir şekilde erişmeniz gerekmez. | DPM/MABS tarafından Disk yedeklemeleri için kullanılır ve tüm Azure yedeklemelerinde kullanılır. SQL Server yedekleme için kullanılmıyor.

## <a name="sql-server-backup-types"></a>SQL Server yedekleme türleri

Aşağıdaki tabloda SQL Server veritabanları için kullanılan farklı yedekleme türleri ve ne sıklıkta kullanıldıkları açıklanmaktadır:

**Yedekleme türü** | **Ayrıntılar** | **Kullanım**
--- | --- | ---
**Tam yedekleme** | Tam bir veritabanı yedeklemesi tüm veritabanını yedekler. Belirli bir veritabanındaki veya bir dosya grubu ya da dosya kümesindeki tüm verileri içerir. Tam yedekleme Ayrıca bu verileri kurtarmak için yeterli günlük içerir. | En çok, günde bir tam yedekleme tetikleyebilirsiniz.<br/><br/> Günlük veya haftalık aralıkta tam yedekleme yapmayı tercih edebilirsiniz.
**Değişiklik yedeği** | Değişiklik yedeklemesi, en son, önceki tam veri yedeklemesini temel alır.<br/><br/> Yalnızca tam yedeklemeden bu yana değiştirilen verileri yakalar. |  En yüksek sıklıkta her gün bir değişiklik yedeği tetikleyebilirsiniz.<br/><br/> Tam yedekleme ve değişiklik yedeklemesini aynı günde yapılandıramazsınız.
**İşlem günlüğü yedeklemesi** | Bir günlük yedeklemesi, belirli bir saniyeye kadar belirli bir noktaya geri yüklemeyi sağlar. | En çok, işlem günlüğü yedeklemelerini 15 dakikada bir yapılandırabilirsiniz.

### <a name="comparison-of-backup-types"></a>Yedekleme türlerini karşılaştırma

Depolama alanı tüketimi, kurtarma süresi hedefi (RTO) ve ağ tüketimi her bir yedekleme türü için farklılık gösterir. Aşağıdaki görüntüde yedekleme türleri karşılaştırması gösterilmektedir:

- A veri kaynağı, aylık olarak yedeklenen, a1-A10 olan 10 depolama blobundan oluşur.
- A2, A3, A4 ve A9 blokları ilk ayda, A5 bloku ise sonraki ayda değişmektedir.
- Değişiklik yedeklemeleri için, ikinci ayda değiştirilen a2, a3, A4 ve A9 blokları yedeklenir. Üçüncü ayda değiştirilen A5 blokuna ek olarak aynı bloklar tekrar yedeklenir. Sonraki tam yedeklemeye kadar değiştirilen bloklar yedeklenmeye devam eder.
- Artımlı yedeklemeler için, ikinci ayın a2, a3, A4 ve A9 blokları değiştirildi ve aktarıldı olarak işaretlenir. Üçüncü ayda yalnızca değiştirilen A5 bloku işaretlenir ve aktarılır.

![Yedekleme yöntemlerinin karşılaştırmalarını gösteren resim](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Yedekleme Özellikleri

Aşağıdaki tabloda farklı yedekleme türleri için desteklenen özellikler özetlenmektedir:

**Özellik** | **Dosya ve klasörlerin doğrudan yedeklemesi (MARS Aracısı 'nı kullanarak)** | **Azure VM yedeklemesi** | **DPM/MABS ile makineler veya uygulamalar**
--- | --- | --- | ---
Kasaya yedekleme | ![Yes][green] | ![Yes][green] | ![Yes][green]
DPM/MABS diskine ve ardından Azure 'a yedekleme | | | ![Yes][green]
Yedekleme için gönderilen verileri sıkıştır | ![Yes][green] | Veri aktarımı sırasında sıkıştırma kullanılmaz. Depolama biraz az, ancak geri yükleme daha hızlıdır.  | ![Yes][green]
Artımlı yedekleme Çalıştır |![Yes][green] |![Yes][green] |![Yes][green]
Yinelenenleri kaldırılmış diskleri yedekleme | | | ![Kısmi][yellow]<br/><br/> Yalnızca şirket içinde dağıtılan DPM/MABS sunucuları için.

![Tablo anahtarı](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Yedekleme ilkesi temelleri

- Her kasa için bir yedekleme ilkesi oluşturulur.
- Aşağıdaki iş yüklerinin yedeklenmesi için bir yedekleme ilkesi oluşturulabilir: Azure VM 'leri, Azure VM 'lerinde SQL, Azure VM 'lerde ve Azure dosya paylaşımlarında SAP HANA. Mars Aracısı kullanılarak dosya ve klasör yedeklemesi ilkesi MARS konsolunda belirtilmiştir.
  - Azure Dosya Paylaşımı
- Bir ilke, birçok kaynağa atanabilir. Azure VM yedekleme ilkesi, birçok Azure VM 'yi korumak için kullanılabilir.
- Bir ilke iki bileşenden oluşur
  - Zamanlama: yedeklemenin ne zaman ele
  - Bekletme: her yedeklemenin ne kadar süreyle saklanması gerekir.
- Zamanlama, belirli bir zaman noktasıyla "günlük" veya "haftalık" olarak tanımlanabilir.
- Bekletme "günlük", "haftalık", "aylık", "yıllık" yedekleme noktaları için tanımlanabilir.
  - "haftalık" haftanın belirli bir gününde bir yedeklemeyi ifade eder
  - "Monthly", ayın belirli bir gününde bir yedekleme anlamına gelir
  - "yıllık" yılın belirli bir gününde bir yedeklemeyi ifade eder
- "Aylık", "yıllık" yedekleme noktaları için bekletme, uzun süreli saklama (LTR) olarak adlandırılır
- Bir kasa oluşturulduğunda, "DefaultPolicy" de oluşturulur ve kaynakları yedeklemek için kullanılabilir.
- Bir yedekleme ilkesinin bekletme döneminde yapılan tüm değişiklikler, yeni olanlardan başka tüm eski kurtarma noktalarına daha etkin bir şekilde uygulanır.

### <a name="impact-of-policy-change-on-recovery-points"></a>Kurtarma noktalarında ilke değişikliğinin etkisi

- **Saklama süresi artırıldı/azaltılır:** Saklama süresi değiştirildiğinde, yeni saklama süresi var olan kurtarma noktalarına da uygulanır. Sonuç olarak, bazı kurtarma noktaları temizlenir. Saklama süresi artdıysa, var olan kurtarma noktalarının da daha fazla bekletme elde edilir.
- **Günlük 'den haftalık olarak değiştirildi:** Zamanlanan yedeklemeler günlük olarak haftalık olarak değiştirildiğinde, mevcut günlük kurtarma noktaları temizlenir.
- **Haftalık olarak günlük olarak değiştirildi:** Mevcut haftalık yedeklemeler, geçerli bekletme ilkesine göre kalan gün sayısına göre tutulacaktır.

### <a name="additional-reference"></a>Ek başvurular

- Azure VM makinesi: ilke [oluşturma](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) ve [değiştirme](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) .
- Azure VM makinesindeki SQL Server veritabanı: ilke [oluşturma](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) ve [değiştirme](./manage-monitor-sql-database-backup.md#modify-policy) .
- Azure dosya paylaşma: ilke [oluşturma](./backup-afs.md) ve [değiştirme](./manage-afs-backup.md#modify-policy) .
- SAP HANA: ilke [oluşturma](./backup-azure-sap-hana-database.md#create-a-backup-policy) ve [değiştirme](./sap-hana-db-manage.md#change-policy) .
- MARS: ilke [oluşturma](./backup-windows-with-mars-agent.md#create-a-backup-policy) ve [değiştirme](./backup-azure-manage-mars.md#modify-a-backup-policy) .
- [İş yükü türüne bağlı olarak yedeklemeyi zamanlamaya yönelik herhangi bir sınırlama var mı?](./backup-azure-backup-faq.yml#are-there-limits-on-backup-scheduling-)
- [Saklama ilkesini değiştirirsem mevcut kurtarma noktalarına ne olur?](./backup-azure-backup-faq.yml#what-happens-when-i-change-my-backup-policy-)

## <a name="architecture-built-in-azure-vm-backup"></a>Mimari: yerleşik Azure VM yedeklemesi

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Mimari: şirket içi Windows Server makinelerini veya Azure VM dosyalarını veya klasörlerini doğrudan yedekleyin

1. Senaryoyu ayarlamak için MARS aracısını makineye indirip yüklersiniz. Ardından, yedeklemelerin ne zaman çalışacağını ve Azure 'da ne kadar süreyle saklanacağını seçersiniz.
1. İlk yedekleme, yedekleme ayarlarınıza göre çalışır.
1. MARS Aracısı, yedekleme için seçilen birimlerin bir noktadan noktaya anlık görüntüsünü almak için VSS 'yi kullanır.
    - MARS Aracısı, anlık görüntüyü yakalamak için yalnızca Windows sistem yazma işlemini kullanır.
    - Aracı herhangi bir uygulama VSS yazıcısını kullanmadığından, uygulamayla tutarlı anlık görüntüleri yakalamaz.
1. VSS ile anlık görüntüyü aldıktan sonra, MARS Aracısı, yedeklemeyi yapılandırdığınız sırada belirttiğiniz önbellek klasöründe bir sanal sabit disk (VHD) oluşturur. Aracı Ayrıca her veri bloğu için sağlama toplamlarını depolar. Bunlar daha sonra, sonraki artımlı yedeklemeler için değiştirilen blokları tespit etmek üzere kullanılır.
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

- [Linux sanal makineleri için Azure yönetilen diskler](../virtual-machines/managed-disks-overview.md)
- [VM 'Ler için kullanılabilir disk türleri](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Premium Depolama ile Azure VM 'lerini yedekleme ve geri yükleme

Azure Backup ile Premium depolamayı kullanarak Azure VM 'Leri yedekleyebilirsiniz:

- VM 'Leri Premium Depolama ile yedekleme işlemi sırasında, yedekleme hizmeti depolama hesabında *AzureBackup* adlı geçici bir hazırlama konumu oluşturur. Hazırlama konumunun boyutu, kurtarma noktası anlık görüntüsünün boyutuna eşittir.
- Premium depolama hesabının geçici hazırlama konumuna uyum sağlamak için yeterli boş alana sahip olduğundan emin olun. Daha fazla bilgi için bkz. [Premium sayfa BLOB depolama hesapları Için ölçeklenebilirlik hedefleri](../storage/blobs/scalability-targets-premium-page-blobs.md). Hazırlama konumunu değiştirmeyin.
- Yedekleme işi tamamlandıktan sonra, hazırlama konumu silinir.
- Hazırlama konumu için kullanılan depolama fiyatı, [Premium Depolama fiyatlandırmasıyla](../virtual-machines/disks-types.md#billing)tutarlıdır.

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

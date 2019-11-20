---
title: Azure VM yedeklemesi hakkında
description: Bu makalede, Azure Backup hizmetinin Azure sanal makinelerini nasıl yedeklediği ve en iyi yöntemleri nasıl izledikleri hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f1c89b9ac7aeb51f43ef84267b20f83b408fd56c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172487"
---
# <a name="an-overview-of-azure-vm-backup"></a>Azure VM yedeklemesine genel bakış

Bu makalede, [Azure Backup hizmetinin](backup-introduction-to-azure-backup.md) Azure sanal makinelerini (VM) nasıl yedeklediği açıklanır.

## <a name="backup-process"></a>Yedekleme işlemi

Azure Backup Azure VM 'Leri için bir yedeklemeyi nasıl tamamlarsa aşağıda verilmiştir:

1. Yedekleme için seçilen Azure VM 'Leri için Azure Backup, belirttiğiniz yedekleme zamanlaması doğrultusunda bir yedekleme işi başlatır.
1. İlk yedekleme sırasında VM çalışıyorsa VM 'ye bir yedekleme uzantısı yüklenir.
    - Windows VM 'Leri için _VMSnapshot_ uzantısı yüklenir.
    - Linux VM 'Leri için _VMSnapshotLinux_ uzantısı yüklenir.
1. Çalıştıran Windows VM 'Leri için, VM 'nin uygulamayla tutarlı bir anlık görüntüsünü almak üzere Windows Birim Gölge Kopyası Hizmeti (VSS) ile yedekleme koordinatları.
    - Varsayılan olarak, yedekleme tam VSS yedeklemeleri alır.
    - Yedekleme uygulamayla tutarlı bir anlık görüntü alalamazsa, temeldeki depolamanın dosya ile tutarlı bir anlık görüntüsünü alır (VM durdurulduğunda hiçbir uygulama yazma işlemi gerçekleşmediğinden).
1. Linux VM 'Leri için yedekleme, dosya ile tutarlı bir yedekleme gerçekleştirir. Uygulamayla tutarlı anlık görüntüler için, ön/son betikleri el ile özelleştirmeniz gerekir.
1. Yedekleme, anlık görüntüyü aldıktan sonra verileri kasaya aktarır.
    - Yedekleme, her bir sanal makine diski paralel olarak yedeklenerek en iyi duruma getirilir.
    - Yedeklenmekte olan her disk için, Azure Backup diskteki blokları okur ve yalnızca önceki yedeklemeden bu yana değiştirilen (Delta) veri bloklarını tanımlar ve aktarır.
    - Anlık görüntü verileri hemen kasaya kopyalanmayabilir. Yoğun zamanlarda birkaç saat sürebilir. Bir VM için toplam Yedekleme süresi, günlük yedekleme ilkeleri için 24 saatten daha az olacaktır.
1. Azure Backup etkinleştirildikten sonra bir Windows sanal makinesinde yapılan değişiklikler şunlardır:
    - Microsoft Visual C++ 2013 Redistributable (x64)-12.0.40660, VM 'ye yüklendi
    - Birim gölge kopyası hizmeti 'nin (VSS) başlangıç türü el ile otomatik olarak değiştirildi
    - Iaasvmprovider Windows hizmeti eklendi

1. Veri aktarımı tamamlandığında, anlık görüntü kaldırılır ve bir kurtarma noktası oluşturulur.

![Azure sanal makine yedekleme mimarisi](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Azure VM yedeklemeleri şifrelemesi

Azure Backup ile Azure VM 'Leri yedeklerken, sanal makineler Depolama Hizmeti Şifrelemesi (SSE) ile geri kalanıyla şifrelenir. Azure Backup, Azure disk şifrelemesi kullanılarak şifrelenmiş Azure VM 'Leri de yedekleyebilir.

**Şifreleme** | **Ayrıntılar** | **Destek**
--- | --- | ---
**Azure disk şifrelemesi** | Azure disk şifrelemesi, hem işletim sistemini hem de Azure VM 'Leri için veri disklerini şifreler.<br/><br/> Azure disk şifrelemesi, bir anahtar kasasında gizli dizi olarak korunmuş olan BitLocker şifreleme anahtarları (BEKs) ile tümleşir. Azure disk şifrelemesi Ayrıca Azure Key Vault anahtar şifreleme anahtarları (KEKs) ile tümleşir. | Azure Backup, yalnızca BEKs ile şifrelenen yönetilen ve yönetilmeyen Azure VM 'lerinin yedeklenmesini destekler, ya da KEKs ile birlikte BEKs.<br/><br/> Hem BEKs hem de KEKs yedeklenir ve şifrelenir.<br/><br/> KEKs ve BEKs yedeklendiği için, gerekli izinlere sahip kullanıcılar anahtarları ve gizli dizileri gerekirse anahtar kasasına geri yükleyebilir. Bu kullanıcılar şifreli VM 'yi de kurtarabilir.<br/><br/> Şifrelenmiş anahtarlar ve gizlilikler yetkisiz kullanıcılar veya Azure tarafından okunamaz.
**SSE** | SSE ile Azure Storage, verileri depolamadan önce otomatik olarak şifreleyerek, bekleyen şifreleme sağlar. Azure Storage, verileri almadan önce de verilerin şifresini çözer. | Azure Backup, Azure VM 'lerinin Rest şifrelemesi için SSE kullanır.

Yönetilen ve yönetilmeyen Azure VM 'Leri için yedekleme, yalnızca BEKs ile şifrelenen VM 'Leri veya KEKs ile birlikte BEKs ile şifrelenmiş VM 'Leri destekler.

Yedeklenen BEKs (gizlilikler) ve KEKs (anahtarlar) şifrelenir. Bunlar, yalnızca yetkili kullanıcılar tarafından anahtar kasasına geri yüklendiğinde okunabilir ve kullanılabilir. Yetkisiz kullanıcılar veya Azure, yedeklenen anahtarları veya parolaları okuyabilir veya kullanabilir.

BEKs da yedeklenir. Bu nedenle, BEKs kaybolursa yetkili kullanıcılar BEKs 'yi anahtar kasasına geri yükleyebilir ve şifrelenmiş VM 'Leri kurtarabilir. Yalnızca gerekli izin düzeyine sahip kullanıcılar şifrelenmiş VM 'Leri veya anahtarları ve gizli dizileri yedekleyebilir ve geri yükleyebilir.

## <a name="snapshot-creation"></a>Anlık görüntü oluşturma

Azure Backup, anlık görüntüleri yedekleme zamanlamalarına göre alır.

- **Windows VM 'leri:** Windows VM 'lerinde yedekleme hizmeti, VM disklerinin uygulamayla tutarlı bir anlık görüntüsünü almak için VSS ile eşgüdümünü sağlar.

  - Varsayılan olarak Azure Backup tam VSS yedeklemeleri alır. [Daha fazla bilgi edinin](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Azure Backup VSS kopya yedeklemeleri alması için ayarı değiştirmek için, aşağıdaki kayıt defteri anahtarını bir komut isteminden ayarlayın:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent"/v USEVSSCOPYBACKUP/t REG_SZ/d TRUE/f**

- **Linux VM 'leri:** Linux VM 'lerinin uygulamayla tutarlı anlık görüntülerini almak için, tutarlılık sağlamak üzere kendi özel betiklerinizi yazmak üzere Linux ön betik ve betik sonrası çerçevesini kullanın.

  - Azure Backup yalnızca sizin tarafınızdan yazılmış olan ön/son betikleri çağırır.
  - Betiklerin ve son betiklerin başarıyla yürütülmesi Azure Backup kurtarma noktasını uygulamayla tutarlı olarak işaretler. Ancak, Özel betikler kullanırken, son olarak uygulama tutarlılığından sorumlu olursunuz.
  - Betikleri yapılandırma hakkında [daha fazla bilgi edinin](backup-azure-linux-app-consistent.md) .

### <a name="snapshot-consistency"></a>Anlık görüntü tutarlılığı

Aşağıdaki tabloda farklı türde anlık görüntü tutarlılığı açıklanmaktadır:

**Görüntüye** | **Ayrıntılar** | **Kurtarma** | **Dikkate Alınacak Nokta**
--- | --- | --- | ---
**Uygulamayla tutarlı** | Uygulamayla tutarlı yedeklemeler bellek içeriğini ve bekleyen g/ç işlemlerini yakalar. Uygulamayla tutarlı anlık görüntüler, bir yedekleme gerçekleşmeden önce uygulama verilerinin tutarlılığını sağlamak için bir VSS yazıcısı (veya Linux için ön betikler öncesi) kullanır. | VM 'yi uygulamayla tutarlı bir anlık görüntüyle kurtarırken, VM önyüklenir. Veri bozulması veya kaybı yok. Uygulamalar tutarlı bir durumda başlar. | Windows: tüm VSS yazıcıları başarılı oldu<br/><br/> Linux: ön/son betik yapılandırma ve başarılı
**Dosya sistemiyle tutarlı** | Dosya sistemiyle tutarlı yedeklemeler, tüm dosyaların bir anlık görüntüsünü aynı anda alarak tutarlılık sağlar.<br/><br/> | Bir sanal makineyi dosya sistemiyle tutarlı bir anlık görüntüyle kurtarırken, VM önyüklenir. Veri bozulması veya kaybı yok. Geri yüklenen verilerin tutarlı olduğundan emin olmak için uygulamaların kendi "düzeltilmesi" mekanizmasını uygulaması gerekir. | Windows: bazı VSS yazıcıları başarısız oldu <br/><br/> Linux: varsayılan (ön/sonrası betikler yapılandırılmamışsa veya başarısız olursa)
**Kilitlenme ile tutarlı** | Kilitlenme tutarlı anlık görüntüler genellikle bir Azure VM yedekleme sırasında kapatılırsa oluşur. Yalnızca yedekleme sırasında diskte bulunan veriler yakalanır ve yedeklenir.<br/><br/> Kilitlenme ile tutarlı bir kurtarma noktası, işletim sistemi veya uygulama için veri tutarlılığını garanti etmez. | Garanti olmasa da VM genellikle önyüklenir ve ardından bozulma hatalarını gidermeye yönelik bir disk denetimi başlatır. Kilitlenme kaybolmadan önce diske aktarılmayan bellek içi veriler veya yazma işlemleri kaybedilir. Uygulamalar kendi veri doğrulamasını uygular. Örneğin, bir veritabanı uygulaması doğrulama için işlem günlüğünü kullanabilir. İşlem günlüğünde veritabanında bulunmayan girişler varsa, veritabanı yazılımı veriler tutarlı olana kadar işlemleri geri kaydeder. | VM, kapalı durumda

## <a name="backup-and-restore-considerations"></a>Yedekleme ve geri yükleme konuları

**Dikkate Alınacak Nokta** | **Ayrıntılar**
--- | ---
**Disk** | VM disklerinin yedeklenmesi paralel. Örneğin, bir VM 'nin dört diski varsa, yedekleme hizmeti dört diski paralel olarak yedeklemeye çalışır. Yedekleme artımlı (yalnızca değiştirilen veriler).
**Planlama** |  Yedekleme trafiğini azaltmak için günün farklı saatlerinde farklı VM 'Leri yedekleyin ve zamanların çakışmadığından emin olun. VM 'Lerin yedeklenmesi aynı anda trafik jams oluşmasına neden olur.
**Yedeklemeler hazırlanıyor** | Yedeklemenin hazırlanması için gereken süreyi göz önünde bulundurun. Hazırlık süresi, yedekleme uzantısını yüklemeyi veya güncelleştirmeyi, yedekleme çizelgesine göre bir anlık görüntüyü tetiklemeyi içerir.
**Veri aktarımı** | Önceki yedeklemeden artımlı değişiklikleri belirlemek için Azure Backup gereken süreyi göz önünde bulundurun.<br/><br/> Artımlı yedeklemede, Azure Backup bloğun sağlama toplamını hesaplayarak değişiklikleri belirler. Bir blok değiştirilirse, kasaya aktarım için işaretlenir. Hizmet, aktarılacak veri miktarını daha aza indirmeye çalışacak şekilde tanımlanan blokları analiz eder. Değiştirilen tüm blokları değerlendirdikten sonra Azure Backup değişiklikleri kasaya aktarır.<br/><br/> Anlık görüntü alma ve kasaya kopyalama arasında bir gecikme olabilir.<br/><br/> Yoğun saatlerde, yedeklemelerin işlenmesi sekiz saate kadar sürebilir. Bir VM için yedekleme zamanı, günlük yedekleme için 24 saatten daha az olacaktır.
**İlk yedekleme** | Artımlı yedeklemeler için toplam Yedekleme süresi 24 saatten daha az olsa da, ilk yedekleme için bu durum olmayabilir. İlk yedekleme için gereken süre, verilerin boyutuna ve yedeklemenin işlendiği zamana bağlı olarak değişir.
**Geri yükleme kuyruğu** | Azure Backup işlemler birden çok depolama hesabından aynı anda geri yüklenir ve geri yükleme isteklerini bir sıraya koyar.
**Kopyayı geri yükle** | Geri yükleme işlemi sırasında veriler kasadan depolama hesabına kopyalanır.<br/><br/> Toplam geri yükleme süresi, saniye başına g/ç işlemlerine (ıOPS) ve depolama hesabının aktarım hızına bağlıdır.<br/><br/> Kopyalama süresini azaltmak için, diğer uygulama yazmaları ve okumaları ile yüklenmeyen bir depolama hesabı seçin.

### <a name="backup-performance"></a>Yedekleme performansı

Bu yaygın senaryolar toplam yedekleme süresini etkileyebilir:

- **Korumalı bir Azure VM 'ye yeni bir disk ekleme:** Bir VM, artımlı yedekleme gerçekleştirçalışıyorsa ve yeni bir disk eklenirse, yedekleme süresi artacaktır. Toplam Yedekleme süresi, yeni diskin ilk çoğaltması nedeniyle, mevcut disklerin Delta çoğaltmasıyla birlikte en son 24 saatten fazla sürebilir.
- **Parçalanmış diskler:** Disk değişiklikleri bitişik olduğunda yedekleme işlemleri daha hızlıdır. Değişiklikler bir diskte yayıldığında ve parçalanmışsa, yedekleme daha yavaş olur.
- **Disk karmaşıklığı:** Artımlı yedekleme yapılmakta olan korumalı diskler 200 GB 'tan fazla günlük bir dalgalanma sahip olursa, yedekleme işleminin tamamlanması uzun zaman alabilir (sekiz saatten fazla).
- **Yedekleme sürümleri:** Yedeklemenin en son sürümü (anında geri yükleme sürümü olarak bilinir), değişiklikleri belirlemek için sağlama toplamı karşılaştırmasından daha iyileştirilmiş bir işlem kullanır. Ancak anlık geri yükleme kullanıyorsanız ve bir yedek anlık görüntüsünü sildiyseniz yedekleme, sağlama toplamı karşılaştırmasına geçer. Bu durumda, yedekleme işlemi 24 saati (veya başarısız) aşacaktır.

## <a name="best-practices"></a>En iyi uygulamalar

VM yedeklemelerini yapılandırırken, aşağıdaki yöntemleri takip ederiz:

- Bir ilkede ayarlanan varsayılan zamanlama sürelerini değiştirin. Örneğin, ilkedeki varsayılan süre 12:00 ise, kaynakların en iyi şekilde kullanılması için zamanlamayı birkaç dakikaya kadar artırın.
- VM 'Leri tek bir kasadan geri yüklüyorsanız, hedef depolama hesabının kısıtlanmasını sağlamak için farklı [genel amaçlı v2 depolama hesapları](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) kullanmanızı önemle tavsiye ederiz. Örneğin, her sanal makinenin farklı bir depolama hesabı olmalıdır. Örneğin, 10 VM geri yüklenirse, 10 farklı depolama hesabı kullanın.
- Hızlı geri yükleme ile Premium depolama kullanan VM 'lerin yedeklenmesi için, **yalnızca** ilk yedekleme için gerekli olan toplam ayrılan depolama alanının *%50* boş alanını ayırmayı öneririz. İlk yedekleme tamamlandıktan sonra %50 boş alan yedeklemeler için bir gereksinim değildir
- Anlık görüntü aynı depolama hesabında olduğundan, genel amaçlı v1 depolama katmanından (anlık görüntü) geri yüklemeler dakikalar içinde tamamlanır. Genel amaçlı v2 depolama katmanından (kasadan) geri yüklemeler saat sürebilir. Verilerin genel amaçlı v1 depolamada kullanılabildiği durumlarda, daha hızlı [geri yükleme Için anında geri yükleme](backup-instant-restore-capability.md) özelliğini kullanmanızı öneririz. (Verilerin bir kasadan geri yüklenmesi gerekiyorsa daha fazla zaman alır.)
- Depolama hesabı başına disk sayısı sınırı, diskler hizmet olarak altyapı (IaaS) VM 'sinde çalışan uygulamalar tarafından ne kadar yoğun erişildiğine bağlıdır. Genel bir uygulama olarak, tek bir depolama hesabında 5 ila 10 disk veya daha fazlası varsa, bazı diskleri ayrı depolama hesaplarına taşıyarak yükü dengeleyin.

## <a name="backup-costs"></a>Yedekleme maliyetleri

Azure Backup ile yedeklenen Azure VM 'Leri [Azure Backup fiyatlandırmaya](https://azure.microsoft.com/pricing/details/backup/)tabidir.

İlk başarılı yedekleme bitene kadar faturalandırma başlamaz. Bu noktada, hem depolama hem de korumalı VM 'Ler için faturalandırma başlar. Faturalama, VM 'nin tüm yedekleme verileri bir kasada depolandığı sürece devam eder. Bir VM için korumayı durdurursanız, ancak VM için yedekleme verileri bir kasada mevcutsa faturalandırma devam eder.

Belirtilen bir VM için faturalandırma yalnızca koruma durdurulmuşsa ve tüm yedekleme verileri silinirse durdurulur. Koruma durdurulduğunda ve etkin yedekleme işi olmadığında, son başarılı VM yedeklemesinin boyutu aylık fatura için kullanılan korumalı örnek boyutu olur.

Korumalı örnek boyutu hesaplaması, sanal makinenin *gerçek* boyutuna göre belirlenir. VM 'nin boyutu, geçici depolama hariç olmak üzere VM 'deki tüm verilerin toplamıdır. Fiyatlandırma, sanal makineye bağlı her bir veri diski için desteklenen en büyük boyuta göre değil, veri disklerinde depolanan gerçek verileri temel alır.

Benzer şekilde, yedekleme depolama alanı faturanız, her kurtarma noktasındaki gerçek verilerin toplamı olan Azure Backup depolanan veri miktarına bağlıdır.

Örneğin, her biri en fazla 4 TB boyutunda iki ek veri diskine sahip olan a2 standart boyutlu bir VM alın. Aşağıdaki tabloda, bu disklerin her birinde depolanan gerçek veriler gösterilmektedir:

**Disk** | **En büyük boyut** | **Gerçek veriler var**
--- | --- | ---
İşletim sistemi diski | 4095 GB | 17 GB
Yerel/geçici disk | 135 GB | 5 GB (yedeklemeye dahil değil)
Veri diski 1 | 4095 GB | 30 GB
Veri diski 2 | 4095 GB | 0 GB

Bu durumda sanal makinenin gerçek boyutu 17 GB + 30 GB + 0 GB = 47 GB 'dir. Bu korumalı örnek boyutu (47 GB) aylık faturanın temelini oluşturur. VM 'deki veri miktarı büyüdükçe, faturalandırma değişiklikleri için kullanılan korumalı örnek boyutu.

<a name="limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb"></a>

## <a name="public-preview-backup-of-vm-with-disk-sizes-up-to-30-tb"></a>Genel Önizleme: 30 TB 'a kadar disk boyutlarına sahip VM yedeklemesi

Azure Backup artık boyutu 30 TB 'a kadar olan daha büyük ve daha güçlü [Azure yönetilen disklerinin](https://azure.microsoft.com/blog/larger-more-powerful-managed-disks-for-azure-virtual-machines/) genel önizlemesini desteklemektedir. Bu önizleme, yönetilen sanal makineler için üretim düzeyi desteği sağlar.

Her disk boyutu 30 TB 'a varan sanal makineleriniz için yedeklemeler ve bir VM 'deki tüm diskler için en fazla 256 TB, var olan yedeklemelerinizi etkilemeden sorunsuz bir şekilde çalışır. Sanal makine zaten Azure Backup ile yapılandırılmışsa, büyük ölçekli diskler için çalışan yedeklemeleri almak için Kullanıcı eylemi gerekmez.

Yedekleme yapılandırılmış büyük disklere sahip tüm Azure sanal makineleri başarıyla yedeklenmelidir.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi [Azure VM yedeklemesi için hazırlanma](backup-azure-arm-vms-prepare.md).

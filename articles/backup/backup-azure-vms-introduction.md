---
title: Azure VM yedeklemesi hakkında
description: Bu makalede, Azure Backup hizmetinin Azure sanal makinelerini nasıl yedeklediği ve en iyi yöntemleri nasıl izledikleri hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 5ce76f64093bab362d62afcc3f94d07f7ee7883d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718458"
---
# <a name="an-overview-of-azure-vm-backup"></a>Azure VM yedeklemesine genel bakış

Bu makalede, [Azure Backup hizmetinin](./backup-overview.md) Azure sanal makinelerini (VM) nasıl yedeklediği açıklanır.

Azure Backup, VM'lerinizdeki verilerin istenmeden yok edilmesine karşı koruma sağlayan bağımsız ve yalıtılmış yedekler sunar. Yedekler, yerleşik kurtarma noktası yönetim özelliklerine sahip Kurtarma Hizmetleri kasasında depolanır. Yapılandırma ve ölçeklendirme süreçleri kolaydır, yedekler iyileştirilir ve ihtiyaç duyduğunuz anda kolayca geri yükleme gerçekleştirmeniz sağlanır.

Yedekleme işleminin bir parçası olarak, bir [anlık görüntü alınır](#snapshot-creation)ve veriler, üretim iş yükleri üzerinde hiçbir etkilenmeden kurtarma hizmetleri kasasına aktarılır. Anlık görüntü, [burada](#snapshot-consistency)açıklandığı gibi farklı tutarlılık düzeyleri sağlar.

Azure Backup Ayrıca, iş yükü algılayan, 15 dakikalık RPO (kurtarma noktası hedefi) sunan ve ayrı veritabanlarının yedeklenme ve geri yüklenmesine izin veren [SQL Server](backup-azure-sql-database.md) ve [SAP HANA](sap-hana-db-about.md) gibi veritabanı iş yükleri için özelleştirilmiş tekliflerdir.

## <a name="backup-process"></a>Yedekleme işlemi

Azure Backup, Azure VM'lerini şu şekilde yedekler:

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="encryption-of-azure-vm-backups"></a>Azure VM yedeklemeleri şifrelemesi

Azure Backup ile Azure VM 'Leri yedeklerken, sanal makineler Depolama Hizmeti Şifrelemesi (SSE) ile geri kalanıyla şifrelenir. Azure Backup, Azure disk şifrelemesi kullanılarak şifrelenmiş Azure VM 'Leri de yedekleyebilir.

**Şifreleme** | **Ayrıntılar** | **Destek**
--- | --- | ---
**SSE** | SSE ile Azure Storage, verileri depolamadan önce otomatik olarak şifreleyerek, bekleyen şifreleme sağlar. Azure Storage, verileri almadan önce de verilerin şifresini çözer. Azure Backup iki tür Depolama Hizmeti Şifrelemesi sahip VM 'lerin yedeklerini destekler:<li> **Anahtar, platform tarafından yönetilen anahtarlarla SSE**: Bu şifreleme, sanal makinelerinizdeki tüm diskler için varsayılan olarak kullanılır. Daha fazla bilgi için [buraya](../virtual-machines/disk-encryption.md#platform-managed-keys)bakın.<li> **Müşteri tarafından yönetilen anahtarlarla SSE**. CMK ile diskleri şifrelemek için kullanılan anahtarları yönetirsiniz. Daha fazla bilgi için [buraya](../virtual-machines/disk-encryption.md#customer-managed-keys)bakın. | Azure Backup, Azure VM 'lerinin Rest şifrelemesi için SSE kullanır.
**Azure Disk Şifrelemesi** | Azure disk şifrelemesi, hem işletim sistemini hem de Azure VM 'Leri için veri disklerini şifreler.<br/><br/> Azure disk şifrelemesi, bir anahtar kasasında gizli dizi olarak korunmuş olan BitLocker şifreleme anahtarları (BEKs) ile tümleşir. Azure disk şifrelemesi Ayrıca Azure Key Vault anahtar şifreleme anahtarları (KEKs) ile tümleşir. | Azure Backup, yalnızca BEKs ile şifrelenen yönetilen ve yönetilmeyen Azure VM 'lerinin yedeklenmesini destekler, ya da KEKs ile birlikte BEKs.<br/><br/> Hem BEKs hem de KEKs yedeklenir ve şifrelenir.<br/><br/> KEKs ve BEKs yedeklendiği için, gerekli izinlere sahip kullanıcılar anahtarları ve gizli dizileri gerekirse anahtar kasasına geri yükleyebilir. Bu kullanıcılar şifreli VM 'yi de kurtarabilir.<br/><br/> Şifrelenmiş anahtarlar ve gizlilikler yetkisiz kullanıcılar veya Azure tarafından okunamaz.

Yönetilen ve yönetilmeyen Azure VM 'Leri için yedekleme, yalnızca BEKs ile şifrelenen VM 'Leri veya KEKs ile birlikte BEKs ile şifrelenmiş VM 'Leri destekler.

Yedeklenen BEKs (gizlilikler) ve KEKs (anahtarlar) şifrelenir. Bunlar, yalnızca yetkili kullanıcılar tarafından anahtar kasasına geri yüklendiğinde okunabilir ve kullanılabilir. Yetkisiz kullanıcılar veya Azure, yedeklenen anahtarları veya parolaları okuyabilir veya kullanabilir.

BEKs da yedeklenir. Bu nedenle, BEKs kaybolursa yetkili kullanıcılar BEKs 'yi anahtar kasasına geri yükleyebilir ve şifrelenmiş VM 'Leri kurtarabilir. Yalnızca gerekli izin düzeyine sahip kullanıcılar şifrelenmiş VM 'Leri veya anahtarları ve gizli dizileri yedekleyebilir ve geri yükleyebilir.

## <a name="snapshot-creation"></a>Anlık görüntü oluşturma

Azure Backup, anlık görüntüleri yedekleme zamanlamalarına göre alır.

- **Windows VM 'leri:** Windows VM 'lerinde yedekleme hizmeti, VM disklerinin uygulamayla tutarlı bir anlık görüntüsünü almak için VSS ile eşgüdümünü sağlar.  Varsayılan olarak, Azure Backup tam bir VSS yedeklemesi alır (uygulama düzeyi tutarlı yedeklemeyi almak için yedekleme sırasında SQL Server gibi uygulamanın günlüklerini keser).  Azure VM yedeklemesi ' nde bir SQL Server veritabanı kullanıyorsanız, bir VSS kopya yedeklemesi (günlükleri korumak için) almak için ayarı değiştirebilirsiniz. Daha fazla bilgi için [bu makaleye](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues) bakın.

- **Linux VM 'leri:** Linux VM 'lerinin uygulamayla tutarlı anlık görüntülerini almak için, tutarlılık sağlamak üzere kendi özel betiklerinizi yazmak üzere Linux ön betik ve betik sonrası çerçevesini kullanın.

  - Azure Backup yalnızca sizin tarafınızdan yazılmış olan ön/son betikleri çağırır.
  - Betiklerin ve son betiklerin başarıyla yürütülmesi Azure Backup kurtarma noktasını uygulamayla tutarlı olarak işaretler. Ancak, Özel betikler kullanırken, son olarak uygulama tutarlılığından sorumlu olursunuz.
  - Betikleri yapılandırma hakkında [daha fazla bilgi edinin](backup-azure-linux-app-consistent.md) .

## <a name="snapshot-consistency"></a>Anlık görüntü tutarlılığı

Aşağıdaki tabloda farklı türde anlık görüntü tutarlılığı açıklanmaktadır:

**Anlık Görüntü** | **Ayrıntılar** | **Kurtarma** | **Değerlendirme**
--- | --- | --- | ---
**Uygulamayla tutarlı** | Uygulamayla tutarlı yedeklemeler bellek içeriğini ve bekleyen g/ç işlemlerini yakalar. Uygulamayla tutarlı anlık görüntüler, bir yedekleme gerçekleşmeden önce uygulama verilerinin tutarlılığını sağlamak için bir VSS yazıcısı (veya Linux için ön betikler öncesi) kullanır. | VM 'yi uygulamayla tutarlı bir anlık görüntüyle kurtarırken, VM önyüklenir. Veri bozulması veya kaybı yok. Uygulamalar tutarlı bir durumda başlar. | Windows: tüm VSS yazıcıları başarılı oldu<br/><br/> Linux: ön/son betik yapılandırma ve başarılı
**Dosya sistemiyle tutarlı** | Dosya sistemiyle tutarlı yedeklemeler, tüm dosyaların bir anlık görüntüsünü aynı anda alarak tutarlılık sağlar.<br/><br/> | Bir sanal makineyi dosya sistemiyle tutarlı bir anlık görüntüyle kurtarırken, VM önyüklenir. Veri bozulması veya kaybı yok. Geri yüklenen verilerin tutarlı olduğundan emin olmak için uygulamaların kendi "düzeltilmesi" mekanizmasını uygulaması gerekir. | Windows: bazı VSS yazıcıları başarısız oldu <br/><br/> Linux: varsayılan (ön/sonrası betikler yapılandırılmamışsa veya başarısız olursa)
**Kilitlenme ile tutarlı** | Kilitlenme tutarlı anlık görüntüler genellikle bir Azure VM yedekleme sırasında kapatılırsa oluşur. Yalnızca yedekleme sırasında diskte bulunan veriler yakalanır ve yedeklenir. | , Bozulma hatalarını gidermeye yönelik bir disk denetimi tarafından sanal makine önyükleme işlemiyle başlar. Kilitlenme kaybolmadan önce diske aktarılmayan bellek içi veriler veya yazma işlemleri kaybedilir. Uygulamalar kendi veri doğrulamasını uygular. Örneğin, bir veritabanı uygulaması doğrulama için işlem günlüğünü kullanabilir. İşlem günlüğünde veritabanında bulunmayan girişler varsa, veritabanı yazılımı veriler tutarlı olana kadar işlemleri geri kaydeder. | VM kapalı (durdurulmuş/serbest bırakıldı) durumunda.

>[!NOTE]
> Sağlama durumu **başarılı** olursa, Azure Backup dosya sistemine tutarlı yedeklemeler alır. Sağlama durumu **kullanılamıyorsa** veya **başarısız** olursa, kilitlenmeyle tutarlı yedeklemeler alınır. Sağlama durumu **oluşturuyor** veya **silinirse**, Azure Backup işlemleri yeniden deniyor demektir.

## <a name="backup-and-restore-considerations"></a>Yedekleme ve geri yükleme konusunda dikkat edilmesi gerekenler

**Değerlendirme** | **Ayrıntılar**
--- | ---
**Disk** | VM disklerinin yedeklenmesi paralel. Örneğin, bir VM 'nin dört diski varsa, yedekleme hizmeti dört diski paralel olarak yedeklemeye çalışır. Yedekleme artımlı (yalnızca değiştirilen veriler).
**Zamanlama** |  Yedekleme trafiğini azaltmak için günün farklı saatlerinde farklı VM 'Leri yedekleyin ve zamanların çakışmadığından emin olun. VM'lerin aynı anda yedeklenmesi trafik yoğunluğuna neden olur.
**Yedeklemeler hazırlanıyor** | Yedeklemenin hazırlanması için gereken süreyi göz önünde bulundurun. Hazırlık süresi, yedekleme uzantısının yüklenmesi veya güncelleştirilmesi ve yedekleme zamanlamasına göre bir anlık görüntünün tetiklenmesi adımlarını kapsar.
**Veri aktarımı** | Önceki yedeklemeden artımlı değişiklikleri belirlemek için Azure Backup gereken süreyi göz önünde bulundurun.<br/><br/> Azure Backup, artımlı yedekleme sırasında değişiklikleri belirlemek için bloğun sağlama toplamını hesaplar. Değiştirilen bloklar kasaya aktarılmak üzere işaretlenir. Hizmet, aktarılacak veri miktarını en aza indirmek için belirlenen blokları analiz eder. Azure Backup, değiştirilen tüm blokları değerlendirdikten sonra değişiklikleri kasaya aktarır.<br/><br/> Anlık görüntünün alınması ile kasaya kopyalanması arasında bir gecikme olabilir. Yoğun zamanlarda, anlık görüntülerin kasaya aktarılması sekiz saate kadar sürebilir. Günlük yedeklemeler için bir VM'nin yedeklenme süresi 24 saatten kısa olacaktır.
**İlk yedekleme** | Artımlı yedeklemeler için toplam yedekleme süresi 24 saatten daha az olsa da aynı durum ilk yedekleme için geçerli olmayabilir. İlk yedekleme için gereken süre, verilerin boyutuna ve yedekleme işleminin gerçekleştirildiği zamana bağlıdır.
**Geri yükleme kuyruğu** | Azure Backup işlemler birden çok depolama hesabından aynı anda geri yüklenir ve geri yükleme isteklerini bir sıraya koyar.
**Kopyayı geri yükle** | Geri yükleme işlemi sırasında veriler kasadan depolama hesabına kopyalanır.<br/><br/> Toplam geri yükleme süresi, saniye başına g/ç işlemlerine (ıOPS) ve depolama hesabının aktarım hızına bağlıdır.<br/><br/> Kopyalama süresini azaltmak için, diğer uygulama yazmaları ve okumaları ile yüklenmeyen bir depolama hesabı seçin.

### <a name="backup-performance"></a>Yedekleme performansı

Bu yaygın senaryolar toplam yedekleme süresini etkileyebilir:

- **Korumalı bir Azure VM 'ye yeni bir disk ekleme:** Bir VM, artımlı yedekleme gerçekleştirçalışıyorsa ve yeni bir disk eklenirse, yedekleme süresi artacaktır. Toplam Yedekleme süresi, yeni diskin ilk çoğaltması nedeniyle, mevcut disklerin Delta çoğaltmasıyla birlikte en son 24 saatten fazla sürebilir.
- **Parçalanmış diskler:** Disk değişiklikleri bitişik olduğunda yedekleme işlemleri daha hızlıdır. Değişiklikler bir diskte yayıldığında ve parçalanmışsa, yedekleme daha yavaş olur.
- **Disk karmaşıklığı:** Artımlı yedekleme yapılmakta olan korumalı diskler 200 GB 'tan fazla günlük bir dalgalanma sahip olursa, yedekleme işleminin tamamlanması uzun zaman alabilir (sekiz saatten fazla).
- **Yedekleme sürümleri:** Yedeklemenin en son sürümü (anında geri yükleme sürümü olarak bilinir), değişiklikleri belirlemek için sağlama toplamı karşılaştırmasından daha iyileştirilmiş bir işlem kullanır. Ancak anlık geri yükleme kullanıyorsanız ve bir yedek anlık görüntüsünü sildiyseniz yedekleme, sağlama toplamı karşılaştırmasına geçer. Bu durumda, yedekleme işlemi 24 saati (veya başarısız) aşacaktır.

### <a name="restore-performance"></a>Performansı geri yükleme

Bu yaygın senaryolar toplam geri yükleme süresini etkileyebilir:

- Toplam geri yükleme süresi, saniye başına giriş/çıkış işlemlerine (ıOPS) ve depolama hesabının aktarım hızına bağlıdır.
- Hedef depolama hesabı diğer uygulama okuma ve yazma işlemleriyle birlikte yüklenirse toplam geri yükleme süresi etkilenebilir. Geri yükleme işlemini geliştirmek için, diğer uygulama verileriyle yüklenmeyen bir depolama hesabı seçin.

## <a name="best-practices"></a>En iyi uygulamalar

VM yedekleme yapılandırması sırasında aşağıdaki yöntemleri uygulamanız önerilir:

- Bir ilkede bulunan varsayılan zamanları değiştirin. Örneğin ilkedeki varsayılan saat 00:00 ise kaynakların en uygun şekilde kullanılması için birkaç dakika ileri atın.
- VM 'Leri tek bir kasadan geri yüklüyorsanız, hedef depolama hesabının kısıtlanmasını sağlamak için farklı [genel amaçlı v2 depolama hesapları](../storage/common/storage-account-upgrade.md) kullanmanızı önemle tavsiye ederiz. Örneğin, her sanal makinenin farklı bir depolama hesabı olmalıdır. Örneğin, 10 VM geri yüklenirse, 10 farklı depolama hesabı kullanın.
- Anlık geri yükleme ile Premium depolama kullanan VM 'lerin yedeklenmesi için, **yalnızca** ilk yedekleme için gerekli olan toplam ayrılan depolama alanının *%50* boş alanını ayırmayı öneririz. İlk yedekleme tamamlandıktan sonra %50 boş alan yedeklemeler için bir gereksinim değildir
- Depolama hesabı başına disk sayısı sınırı, disklere hizmet olarak altyapı (IaaS) VM üzerinde çalışan uygulamalar tarafından hangi düzeyde erişim sağlandığına göre değişir. Genellikle tek bir depolama hesabında 5-10 arası veya daha fazla disk varsa bazı diskleri ayrı depolama hesaplarına taşıyarak yükü dengelemeniz önerilir.
- PowerShell kullanarak VM 'Leri yönetilen disklere geri yüklemek için, yönetilen disklerin geri yükleneceği kaynak grubunu belirtmek üzere ***Targetresourcegroupname*** ek parametresini sağlayın, [daha fazla bilgi edinin](./backup-azure-vms-automation.md#restore-managed-disks).

## <a name="backup-costs"></a>Yedekleme maliyetleri

Azure Backup ile yedeklenen Azure VM 'Leri [Azure Backup fiyatlandırmaya](https://azure.microsoft.com/pricing/details/backup/)tabidir.

İlk başarılı yedekleme bitene kadar faturalandırma başlamaz. Bu noktada, hem depolama hem de korumalı VM 'Ler için faturalandırma başlar. Faturalama, VM 'nin tüm yedekleme verileri bir kasada depolandığı sürece devam eder. Bir VM için korumayı durdurursanız, ancak VM için yedekleme verileri bir kasada mevcutsa faturalandırma devam eder.

Belirtilen bir VM için faturalandırma yalnızca koruma durdurulmuşsa ve tüm yedekleme verileri silinirse durdurulur. Koruma durdurulduğunda ve etkin yedekleme işi olmadığında, son başarılı VM yedeklemesinin boyutu aylık fatura için kullanılan korumalı örnek boyutu olur.

Korumalı örnek boyutu hesaplaması, sanal makinenin *gerçek* boyutuna göre belirlenir. VM 'nin boyutu, geçici depolama hariç olmak üzere VM 'deki tüm verilerin toplamıdır. Fiyatlandırma, sanal makineye bağlı her bir veri diski için desteklenen en büyük boyuta göre değil, veri disklerinde depolanan gerçek verileri temel alır.

Benzer şekilde, yedekleme depolama alanı faturanız, her kurtarma noktasındaki gerçek verilerin toplamı olan Azure Backup depolanan veri miktarına bağlıdır.

Örneğin, her biri en fazla 32 TB boyutunda iki ek veri diskine sahip olan a2 standart boyutlu bir VM alın. Aşağıdaki tabloda, bu disklerin her birinde depolanan gerçek veriler gösterilmektedir:

**Disk** | **En büyük boyut** | **Gerçek veriler var**
--- | --- | ---
İşletim sistemi diski | 32 TB | 17 GB
Yerel/geçici disk | 135 GB | 5 GB (yedeklemeye dahil değil)
Veri diski 1 | 32 TB| 30 GB
Veri diski 2 | 32 TB | 0 GB

Bu durumda sanal makinenin gerçek boyutu 17 GB + 30 GB + 0 GB = 47 GB 'dir. Bu korumalı örnek boyutu (47 GB) aylık faturanın temelini oluşturur. VM 'deki veri miktarı büyüdükçe, faturalandırma değişiklikleri için kullanılan korumalı örnek boyutu.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure VM yedeklemesi Için hazırlanma](backup-azure-arm-vms-prepare.md).
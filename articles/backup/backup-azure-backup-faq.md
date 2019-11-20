---
title: Yaygın soruların yanıtları
description: 'Kurtarma Hizmetleri kasaları, neleri yedekleyebilir, nasıl çalışır, şifreleme ve limitlerin dahil olduğu Azure Backup özellikleriyle ilgili yaygın soruların yanıtları. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: f95a4e7e9084c304838b01bdfaceee261d8151cf
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173221"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup-sık sorulan sorular

Bu makalede Azure Backup hizmetiyle ilgili sık sorulan sorular yanıtlanmaktadır.

## <a name="recovery-services-vault"></a>Kurtarma hizmetleri kasası

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Her bir Azure aboneliği için oluşturulan kasaların sayısına yönelik herhangi bir sınır var mıdır?

Evet. Her abonelik için desteklenen Azure Backup bölge başına en fazla 500 kurtarma hizmeti Kasası oluşturabilirsiniz. Daha fazla kasaya ihtiyacınız varsa başka bir abonelik oluşturun.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Her bir kasa için kaydedilebilen sunucu/makine sayısına yönelik sınırlar var mıdır?

Kasa başına en fazla 1000 Azure sanal makinesi kaydedebilirsiniz. Microsoft Azure Backup aracısını kullanıyorsanız, kasa başına 50 MAB aracısına kadar kaydolabilirsiniz. Bir kasaya 50 MAB sunucuları/DPM sunucuları kaydedebilirsiniz.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Kasada kaç veri kaynağı/öğe korunabilecek?

Bir kasadaki tüm iş yükleri (IaaS VM, SQL, AFS, vb.) üzerinde en fazla 2000 veri kaynağı/öğe koruyabilirsiniz.
Örneğin, kasadaki 500 VM 'Leri ve 400 Azure dosya paylaşımlarını zaten koruduysanız, yalnızca bu anda en fazla 1100 SQL veritabanı koruyabilirsiniz.

### <a name="how-many-policies-can-i-create-per-vault"></a>Her kasa için kaç ilke oluşturabilirim?

Her kasa için yalnızca 200 ilke olabilir.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Kuruluşumun tek bir Kasası varsa, verileri geri yüklerken kasadaki farklı sunuculardan verileri nasıl ayırabilirim?

Birlikte kurtarmak istediğiniz sunucu verileri, yedeklemeyi ayarlarken aynı parolayı kullanmalıdır. Kurtarmayı belirli bir sunucu veya sunuculara yalıtmak istiyorsanız, yalnızca o sunucu veya sunucular için bir parola kullanın. Örneğin, insan kaynakları sunucuları bir şifreleme parolası kullanırken, muhasebe sunucuları ve depolama sunucuları farklı birer şifreleme parolası kullanabilir.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Kasayımı abonelikler arasında taşıyabilir miyim?

Evet. Kurtarma Hizmetleri kasasını taşımak için, bu [makaleye](backup-azure-move-recovery-services-vault.md) bakın

### <a name="can-i-move-backup-data-to-another-vault"></a>Yedekleme verilerini başka bir kasaya taşıyabilir miyim?

Hayır. Bir kasada depolanan yedekleme verileri farklı bir kasaya taşınamaz.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Bir yedeklemeden sonra GRS 'den LRS 'ye değişebilir miyim?

Hayır. Kurtarma Hizmetleri Kasası, yalnızca herhangi bir yedekleme depolanmadan önce depolama seçeneklerini değiştirebilir.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasasına yedeklenen VM 'Ler için bir öğe düzeyinde geri yükleme (ıLR) yapabilir miyim?

- ILR, Azure VM yedeklemesi tarafından yedeklenen Azure VM 'Leri için desteklenir. Daha fazla bilgi için bkz. [Makale](backup-azure-restore-files-from-vm.md)
- ILR, Azure Backup sunucusu ya da System Center DPM tarafından yedeklenen şirket içi VM 'lerin çevrimiçi kurtarma noktaları için desteklenmez.

## <a name="azure-backup-agent"></a>Azure Backup aracısı

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Azure VM yedeklemesi için Azure Backup Aracısı hakkında sık sorulan soruları nereden bulabilirim?

- Azure VM 'lerinde çalışan aracı için bu [SSS](backup-azure-vm-backup-faq.md)makalesini okuyun.
- Azure dosya klasörlerini yedeklemek için kullanılan aracı için bu [SSS](backup-azure-file-folder-backup-faq.md)makalesini okuyun.

## <a name="general-backup"></a>Genel yedekleme

### <a name="are-there-limits-on-backup-scheduling"></a>Yedekleme zamanlaması için sınırlamalar var mı?

Evet.

- Windows Server veya Windows makinelerini, günde üç kez yedekleyebilirsiniz. Zamanlama ilkesini günlük veya Haftalık zamanlamalar olarak ayarlayabilirsiniz.
- DPM 'yi günde iki kez yedekleyebilirsiniz. Zamanlama ilkesini günlük, haftalık, aylık ve yıllık olarak ayarlayabilirsiniz.
- Azure VM 'lerini günde bir kez yedekledikten sonra.

### <a name="what-operating-systems-are-supported-for-backup"></a>Yedekleme için hangi işletim sistemleri destekleniyor?

Azure Backup dosya ve klasörleri ve Azure Backup Sunucusu ve DPM tarafından korunan uygulamaları yedeklemek için bu işletim sistemlerini destekler.

**OS** | **SKU** | **Ayrıntılar**
--- | --- | ---
Unuzu | |
Windows 10 64 bit | Enterprise, Pro, Home | Makinelerin en son hizmet paketlerini ve güncelleştirmelerini çalıştırması gerekir.
Windows 8.1 64 bit | Enterprise, Pro | Makinelerin en son hizmet paketlerini ve güncelleştirmelerini çalıştırması gerekir.
Windows 8 64 bit | Enterprise, Pro | Makinelerin en son hizmet paketlerini ve güncelleştirmelerini çalıştırması gerekir.
Windows 7 64 bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Makinelerin en son hizmet paketlerini ve güncelleştirmelerini çalıştırması gerekir.
Sunucu | |
Windows Server 2019 64 bit | Standard, Datacenter, Essentials | En son hizmet paketleri/güncelleştirmeleriyle.
Windows Server 2016 64 bit | Standard, Datacenter, Essentials | En son hizmet paketleri/güncelleştirmeleriyle.
Windows Server 2012 R2 64 bit | Standard, Datacenter, Foundation | En son hizmet paketleri/güncelleştirmeleriyle.
Windows Server 2012 64 bit | Datacenter, Foundation, Standard | En son hizmet paketleri/güncelleştirmeleriyle.
Windows Storage Server 2016 64 bit | Standard, Workgroup | En son hizmet paketleri/güncelleştirmeleriyle.
Windows Storage Server 2012 R2 64 bit | Standart, çalışma grubu, temel | En son hizmet paketleri/güncelleştirmeleriyle.
Windows Storage Server 2012 64 bit | Standard, Workgroup | En son hizmet paketleri/güncelleştirmeleriyle.
Windows Server 2008 R2 SP1 64 bit | Standard, Enterprise, Datacenter, Foundation | En son güncelleştirmelerle.
Windows Server 2008 64 bit | Standard, Enterprise, Datacenter | En son güncelleştirmelerle.

Azure Backup, 32 bitlik işletim sistemlerini desteklemez.

Azure VM Linux yedeklemeleri için Azure Backup, çekirdek IŞLETIM sistemi Linux ve 32-bit işletim sistemi dışında [Azure tarafından onaylanan dağıtımların listesini](../virtual-machines/linux/endorsed-distros.md)destekler. Diğer kendi Linux dağıtımlarınız VM üzerinde kullanılabilir olduğu ve Python desteği bulunan sürece çalışır.

### <a name="are-there-size-limits-for-data-backup"></a>Veri yedeklemesi için boyut sınırları var mı?

Boyut sınırları aşağıdaki gibidir:

İşletim sistemi/makine | Veri kaynağının Boyut sınırı
--- | ---
Windows 8 veya üzeri | 54.400 GB
Windows 7 |1700 GB
Windows Server 2012 veya üzeri | 54.400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Azure VM | 16 veri diski<br/><br/> 4095 GB 'a kadar veri diski

### <a name="how-is-the-data-source-size-determined"></a>Veri kaynağı boyutu nasıl belirlenir?

Aşağıdaki tabloda, her bir veri kaynağı boyutunun nasıl belirlendiği açıklanmaktadır.

**Veri kaynağı** | **Ayrıntılar**
--- | ---
Birim |Yedeklenen tek bir birim VM 'den yedeklenen veri miktarı.
SQL Server veritabanı |Yedeklenen tek SQL veritabanı boyutunun boyutu.
SharePoint | Yedeklenmekte olan bir SharePoint grubu içindeki içerik ve yapılandırma veritabanlarının toplamı.
Exchange |Yedeklenmekte olan bir Exchange sunucusundaki tüm Exchange veritabanlarının toplamı.
BMR/sistem durumu |Yedeklenmekte olan makinenin her bir BMR veya sistem durumunun tek bir kopyası.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Kurtarma Hizmetleri Kasası kullanılarak yedeklenen veri miktarı için bir sınır var mı?

Kurtarma Hizmetleri kasasını kullanarak yedekleyebileceğiniz veri miktarı için bir sınır yoktur.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Kurtarma Hizmetleri kasasına aktarılan verilerin boyutu neden yedekleme için seçilen verilerden daha küçük?

Azure Backup aracılarından, DPM 'den ve Azure Backup Sunucusu yedeklenen veriler, aktarılmadan önce sıkıştırılır ve şifrelenir. Sıkıştırma ve şifreleme uygulandığında, kasadaki veriler% 30-40 daha küçüktür.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Kasadaki bir kurtarma noktasından tek tek dosyaları silebilir miyim?

Hayır, Azure Backup depolanan yedeklemelerdeki tek tek öğeleri silme veya temizleme işlemlerini desteklemez.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Bir yedekleme işini başladıktan sonra iptal ediyorum, aktarılan yedekleme verileri silinir mi?

Hayır. Yedekleme işi iptal edilmeden önce kasaya aktarılan tüm veriler kasada kalır.

- Azure Backup, yedekleme işlemi sırasında yedekleme verilerine zaman zaman denetim noktaları eklemek üzere bir denetim noktası mekanizması kullanır.
- Yedekleme verilerinde denetim noktaları bulunduğundan, sonraki yedekleme işlemi dosyaların bütünlüğünü doğrulayabilir.
- Bir sonraki yedekleme işi, daha önce yedeklenen verilerin üzerine artımlı olarak gerçekleşir. Artımlı yedekleme işlemlerinin yalnızca yeni veya değiştirilmiş verileri aktarması, bant genişliğinin daha iyi kullanılması anlamına gelir.

Bir Azure VM’ye yönelik bir yedekleme işini iptal ederseniz aktarılan tüm veriler yoksayılır. Bir sonraki yedekleme işi, son başarılı yedekleme işinden artımlı verileri aktarır.

## <a name="retention-and-recovery"></a>Bekletme ve kurtarma

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>DPM ve Windows makineler için bekletme ilkeleri aynı mı?

Evet, her ikisi de günlük, haftalık, aylık ve yıllık bekletme ilkelerine sahiptir.

### <a name="can-i-customize-retention-policies"></a>Bekletme ilkelerini özelleştirebilir miyim?

Evet, ilkeleri özelleştirin. Örneğin, haftalık ve aylık saklama gereksinimlerini yapılandırabilirsiniz, ancak yıllık ve aylık olarak kullanamazsınız.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Yedekleme zamanlaması ve bekletme ilkeleri için farklı zamanlar kullanabilir miyim?

Hayır. Bekletme ilkeleri, yalnızca yedekleme noktalarında uygulanabilir. Örneğin, bu görüntüde, saat 12:00:00 ve 6pm 'de gerçekleştirilen yedeklemeler için bir bekletme ilkesi gösterilmektedir.

![Yedeklemeyi ve Bekletmeyi Zamanlama](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Yedekleme uzun bir süre tutuluyorsa, eski bir veri noktasını kurtarmak daha fazla zaman alabilir mi?

Hayır. En eski veya en yeni noktanın kurtarılması için geçen süre aynıdır. Her kurtarma noktası bir tam nokta gibi davranır.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Her kurtarma noktası bir tam nokta gibiyse bu durum toplam faturalanabilir yedekleme alanını etkiler mi?

Genel uzun vadeli bekletme noktası ürünleri, yedekleme verilerini tam noktalar olarak depolar.

- Tam noktalar depolama açısından *verimsizdir* ancak daha kolay ve hızlı şekilde geri yüklenir.
- Artımlı kopyalar depolama *etkilidir* , ancak bir veri zincirini geri yüklemeniz gerekir, bu da kurtarma süresini etkiler

Azure Backup alanı mimarisi, hızlı geri yükleme özelliğiyle optimum veri depolama olanağı sunarken aynı zamanda düşük depolama maliyetleri oluşturarak iki açıdan da avantaj sağlar. Bu, giriş ve çıkış bant genişliğinizin verimli bir şekilde kullanılmasını sağlar. Veri depolama miktarı ve verileri kurtarmak için gereken süre en az bir olarak tutulur. [Artımlı yedeklemeler](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/)hakkında daha fazla bilgi edinin.

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Oluşturulabilecek kurtarma noktalarının sayısına yönelik bir sınır var mıdır?

Korumalı bir örnek için en çok 9999 kurtarma noktası oluşturabilirsiniz. Korumalı örnek, Azure 'a yedekleyen bir bilgisayar, sunucu (fiziksel veya sanal) veya iş yüküdür.

- [Yedekleme ve bekletme](./backup-overview.md#backup-and-retention)hakkında daha fazla bilgi edinin.

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Azure 'a yedeklenen verileri kaç kez kurtarabilirim?

Azure Backup ile gerçekleştirilen kurtarma işlemlerinin sayısına yönelik bir sınır yoktur.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Verileri geri yüklerken Azure'dan çıkış trafiği için ödeme yapacak mıyım?

Hayır. Kurtarma ücretsizdir ve çıkış trafiği için ücret ödemeniz gerekmez.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Yedekleme İlkemi değiştirdiğimde ne olur?

Yeni bir ilke uygulandığında, yeni ilkenin zamanlaması ve bekletme işlemi izlenir.

- Bekletme süresi uzatıldıysa, yeni ilkeye göre tutulması için mevcut kurtarma noktaları işaretlenir.
- Bekletme süresi kısaltıldıysa, bunlar sonraki temizleme işleminde kesilmek üzere işaretlenir ve sonra silinir.

## <a name="encryption"></a>Şifreleme

### <a name="is-the-data-sent-to-azure-encrypted"></a>Veriler Azure'a şifreli olarak mı gönderilir?

Evet. Veriler, AES256 kullanarak şirket içi makinede şifrelenir. Veriler güvenli bir HTTPS bağlantısı üzerinden gönderilir. Bulutta aktarılan veriler yalnızca depolama ve kurtarma hizmeti arasında HTTPS bağlantısı ile korunur. Iscsı protokolü, kurtarma hizmeti ve Kullanıcı makinesi arasında aktarılan verilerin güvenliğini sağlar. Güvenli Tünelleme, Iscsı kanalını korumak için kullanılır.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Azure üzerindeki yedekleme verileri de şifreli midir?

Evet. Azure 'daki veriler, Rest 'ten şifrelenir.

- Şirket içi yedekleme için, Azure 'a yedeklerken sağladığınız parola kullanılarak bekleyen şifreleme sağlanır.
- Azure VM 'Leri için, veriler Depolama Hizmeti Şifrelemesi (SSE) kullanılarak şifrelenir.

Microsoft herhangi bir noktada yedekleme verilerinin şifresini çözmez.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Yedekleme verilerini şifrelemek için kullanılan anahtarın şifreleme uzunluğu alt sınırı nedir?

Azure Backup aracısını kullanırken şifreleme anahtarı en az 16 karakter olmalıdır. Azure VM'lerde, Azure KeyVault tarafından kullanılan anahtarlar için boyut sınırlaması yoktur.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Şifreleme anahtarını kaybedersem ne olur? Verileri kurtarabilir miyim? Microsoft verileri kurtarabilir mi?

Yedekleme verilerini şifrelemek için kullanılan anahtar yalnızca sitenizde bulunur. Microsoft, Azure üzerinde anahtarın bir kopyasını tutmaz ve anahtara erişim sahibi değildir. Anahtarı yanlış yerleştirirseniz Microsoft, yedekleme verilerini kurtaramaz.

## <a name="next-steps"></a>Sonraki adımlar

Diğer SSS 'leri okuyun:

- Azure VM yedeklemeleri hakkında [sık sorulan sorular](backup-azure-vm-backup-faq.md) .
- Azure Backup aracısıyla ilgili [sık sorulan sorular](backup-azure-file-folder-backup-faq.md)

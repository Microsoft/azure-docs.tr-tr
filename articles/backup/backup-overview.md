---
title: Azure Backup nedir?
description: Azure Backup hizmetine genel bir bakış sağlar ve iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur.
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 49ef90c874e920ef92dd5206447081a6aa4f7988
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172190"
---
# <a name="what-is-the-azure-backup-service"></a>Azure Backup hizmeti nedir?

Azure Backup hizmeti, verileri Microsoft Azure buluta yedekler. Şirket içi makineleri ve iş yüklerini ve Azure sanal makinelerini (VM) yedekleyebilirsiniz.

## <a name="why-use-azure-backup"></a>Azure Backup'ı neden kullanmalısınız?

Azure Backup, bu önemli avantajları sunar:

- **Şirket içi yedeklemeyi boşaltma**: Azure Backup şirket içi kaynaklarınızı buluta yedeklemeye yönelik basit bir çözüm sunar. Karmaşık şirket içi yedekleme çözümlerini dağıtmaya gerek olmadan kısa ve uzun süreli yedekleme yapın.
- **Azure IaaS VM 'Lerini yedekleme**: Azure Backup özgün verilerin yanlışlıkla yok edilmesiyle karşı koruma sağlamak için bağımsız ve yalıtılmış yedeklemeler sağlar. Yedeklemeler, kurtarma noktaları yerleşik olarak yönetilen bir kurtarma hizmetleri kasasında depolanır. Yapılandırma ve ölçeklenebilirlik basittir, yedeklemeler en iyi duruma getirilir ve gerektiğinde kolayca geri yükleyebilirsiniz.
- **Kolayca ölçeklendirin** Azure Backup, bakım veya izleme ek yükü olmadan yüksek kullanılabilirlik sağlamak için Azure bulutunun temel alınan gücünü ve sınırsız ölçeğini kullanır.
- **Sınırsız veri aktarımı alın**: Azure Backup, aktardığınız gelen veya giden verilerin miktarını sınırlamaz veya aktarılan veriler için ücret ödemez.
  - Giden veriler, geri yükleme işlemi sırasında bir Kurtarma Hizmetleri kasasından aktarılan verileri tanımlar.
  - Azure Içeri/dışarı aktarma hizmetini kullanarak büyük miktarda veriyi içeri aktarmaya yönelik çevrimdışı bir ilk yedekleme gerçekleştirirseniz, gelen verilerle ilişkili bir maliyet vardır.  [Daha fazla bilgi edinin](backup-azure-backup-import-export.md).
- **Verileri güvende tutun**: Azure Backup yoldaki ve bekleyen verilerin güvenliğini sağlamak için çözümler sağlar.
- **Uygulamayla tutarlı yedeklemeler al**: uygulamayla tutarlı bir yedekleme, bir kurtarma noktasının yedek kopyayı geri yüklemek için gerekli tüm verilere sahip olduğu anlamına gelir. Azure Backup, verileri geri yüklerken ek düzeltmelere gerek kalmaması için uygulamayla tutarlı yedeklemeler yapılmasını sağlar. Uygulamayla tutarlı verilerin geri yüklenmesi, geri yükleme süresini azaltarak hizmetlerinizin kısa süre içinde çalışır hale gelmesini sağlar.
- **Kısa ve uzun süreli verileri koruyun**: kısa süreli ve uzun süreli veri saklama Için kurtarma hizmetleri kasalarını kullanabilirsiniz. Azure, bir Kurtarma Hizmetleri kasasında verileri saklama süresini kısıtlamaz. Dilediğiniz sürece bu uygulamayı koruyabilirsiniz. Azure Backup, korunan her örnek için 9999 kurtarma noktası sınırına sahiptir.
- **Otomatik depolama yönetimi** - Karma ortamlar genelde heterojen depolamaya (bazıları şirket içi, bazıları ise bulutta olan) ihtiyaç duyar. Azure Backup çözümünde, şirket içi depolama cihazlarının kullanımıyla ilişkili maliyetler yoktur. Azure Backup, yedekleme depolama alanını otomatik olarak ayırır ve yönetir ve yalnızca kullandığınız depolama alanı için ödeme yapmak üzere bir Kullandıkça Öde modeli kullanır. Fiyatlandırma hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/backup) .
- **Birden çok depolama seçeneği** Azure Backup depolama/verilerinizi yüksek oranda kullanılabilir tutmak için iki tür çoğaltma sunar.
  - [Yerel olarak yedekli depolama (LRS)](../storage/common/storage-redundancy-lrs.md) verilerinizi üç kez çoğaltır (verilerinizin üç kopyasını oluşturur) bir veri merkezinde bulunan bir depolama ölçek birimi. Verilerin tüm kopyaları aynı bölgenin içinde yer alır. LRS, verilerinizi yerel donanım hatalarına karşı korumak için düşük maliyetli bir seçenektir.
  - [Coğrafi olarak yedekli depolama (GRS)](../storage/common/storage-redundancy-grs.md) , varsayılan ve önerilen çoğaltma seçeneğidir. GRS, verilerinizi ikincil bir bölgeye (kaynak verilerin birincil konumundan yüzlerce kilometre uzakta) kopyalar. GRS’nin maliyeti LRS’den yüksektir ancak bölgesel kesintiler olsa bile daha yüksek veri dayanıklılık düzeyi sunar.

## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Azure Backup ve Azure Site Recovery arasındaki fark nedir?

Hem Azure Backup hem de Azure Site Recovery Hizmetleri işinizde bir iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejisine katkıda bulunur. BCDR iki geniş aims oluşur:

- Kesintiler gerçekleştiğinde iş verilerinizi güvende tutun ve kurtarılabilir.
- Planlanmış ve planlanmamış saatlerde uygulamalarınızı ve iş yüklerinizi çalışır durumda tutun.

Her iki hizmet de tamamlayıcı ancak farklı işlevlere sahiptir.

- **Azure Site Recovery**: Site Recovery, şirket içi makineler ve Azure VM 'ler için bir olağanüstü durum kurtarma çözümü sağlar. Makineleri birincil bir konumdan ikincil konuma çoğaltmanız gerekir. Olağanüstü durum çalıştığında, makineleri ikincil konuma devreder ve oradan buradan erişebilirsiniz. Her şey normal olarak çalışır duruma geldiğinde, makineleri birincil sitede kurtarmak için yeniden başarısız olursunuz.
- **Azure Backup**: Azure Backup hizmeti, şirket içi makinelerden ve Azure VM 'lerinden verileri yedekler. Veriler, dosya yedeklemesi, klasörler, makine sistem durumu ve uygulama tarafından uyumlu veri yedeklemesi dahil olmak üzere ayrıntılı bir düzeyde yedeklenebilir ve kurtarılabilir. Azure Backup verileri Site Recovery daha ayrıntılı bir düzeyde işler. Örnek olarak, dizüstü bilgisayarınızdaki bir sunu bozulursa, sunuyu geri yüklemek için Azure Backup kullanabilirsiniz. VM yapılandırmasını ve verileri güvenli ve erişilebilir tutmak istiyorsanız Site Recovery kullanabilirsiniz.  

BCDR ihtiyaçlarınızı anlamak için tablo noktalarını kullanın.

**Hedefi** | **Ayrıntılar** | **Il**
--- | --- | ---
**Veri yedekleme/bekletme** | Yedekleme verileri, bir uyumluluk perspektifinden gerekirse gün, ay veya hatta yıllar için korunabilir ve depolanabilir. | Azure Backup gibi yedekleme çözümleri, yedeklemek istediğiniz verileri en iyi şekilde seçmenizi ve yedekleme ve bekletme ilkelerini ayarlamayı de ayarlamanıza olanak sağlar.<br/><br/> Site Recovery, aynı ince ayarlamaya izin vermez.
**Kurtarma noktası hedefi (RPO)** | Kurtarma işleminin gerekli olduğu durumlarda kabul edilebilir veri kaybı miktarı. | Yedekler için daha fazla değişken RPO vardır.<br/><br/> VM yedeklemeleri genellikle günde bir RPO 'ya sahiptir ve veritabanı yedeklemeleri, RPOs 'u 15 dakika kadar düşük olur.<br/><br/> Site Recovery, çoğaltma sürekli veya sık olduğundan, kaynak ve çoğaltma kopyası arasındaki Delta küçük olacak şekilde düşük bir RPO sağlar.
**Kurtarma süresi hedefi (RTO)** |Bir geri yükleme veya kurtarma işlemini tamamlamak için geçen süre. | Daha büyük RPO nedeniyle, bir yedekleme çözümünün işlemesi gereken veri miktarı genellikle çok daha yüksektir; bu da daha uzun RTO'lara yol açar. Örneğin, bandın şirket dışı bir konumdan taşınması için harcanan süreye bağlı olarak, bantlardan veri geri yükleme işlemi birkaç gün sürebilir.

## <a name="what-backup-scenarios-are-supported"></a>Hangi yedekleme senaryoları desteklenir?

Azure Backup hem şirket içi makineleri hem de Azure VM 'lerini yedekleyebilir.

**Makin** | **Yedekleme senaryosu**
--- | ---
**Şirket içi yedekleme** |  1) tek tek dosyaları ve sistem durumunu yedeklemek için şirket içi Windows makinelerde Azure Backup Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı 'nı çalıştırın. <br/><br/>2) Şirket içi makineleri bir yedekleme sunucusuna yedekleyin (System Center Data Protection Manager (DPM) veya Microsoft Azure Backup sunucusu (MABS)) ve ardından yedekleme sunucusunu Azure 'da bir Azure Backup kurtarma hizmetleri kasasına yedekleyecek şekilde yapılandırın.
**Azure VM 'Leri** | 1) bireysel Azure VM 'Leri için yedeklemeyi etkinleştirin. Yedeklemeyi etkinleştirdiğinizde, Azure Backup VM üzerinde çalışan Azure VM aracısına bir uzantı yüklenir. Aracı tüm VM 'yi yedekler.<br/><br/> 2) MARS aracısını bir Azure VM üzerinde çalıştırın. Bu, sanal makinede ayrı dosya ve klasörleri yedeklemek istiyorsanız yararlıdır.<br/><br/>

## <a name="why-use-a-backup-server"></a>Yedekleme sunucusu neden kullanılmalıdır?

Makineleri ve uygulamaları MABS/DPM depolamaya yedeklemenin avantajları ve ardından DPM/MABS depolamasını bir kasaya yedeklemek aşağıdaki gibidir:

- MABS/DPM 'ye yedekleme, dosya/klasör/birim yedeklemelerine ve makine durumu yedeklemelerine (çıplak, sistem durumu) ek olarak SQL Server, Exchange ve SharePoint gibi ortak uygulamalar için iyileştirilmiş uygulama açısından iyileştirilmiş yedeklemeler sağlar.
- Şirket içi makineler için, yedeklemek istediğiniz her makineye MARS aracısını yüklemeniz gerekmez. Her makine DPM/MABS koruma aracısını çalıştırır ve MARS Aracısı yalnızca MABS/DPM üzerinde çalışır.
- Yedeklemeleri çalıştırmaya yönelik daha fazla esneklik ve ayrıntılı zamanlama seçenekleriniz vardır.
- Tek bir konsolundaki koruma gruplarında topladığınız birden çok makine için yedeklemeleri yönetebilirsiniz. Bu, uygulamalar birden çok makine üzerinde katmanlandığında ve bunları birlikte yedeklemek istediğinizde yararlıdır.

Yedekleme Sunucusu kullanılırken [yedeklemenin nasıl çalıştığı](backup-architecture.md#architecture-back-up-to-dpmmabs) hakkında daha fazla bilgi edinin ve yedekleme sunucuları için [destek gereksinimleri](backup-support-matrix-mabs-dpm.md) .

## <a name="what-can-i-back-up"></a>Neleri yedekleyebilirim?

**Makin** | **Backup yöntemi** | **Yedekleme**
--- | --- | ---
**Şirket içi Windows VM 'Leri** | MARS Aracısı 'nı Çalıştır | Dosyaları, klasörleri, sistem durumunu yedekleyin.<br/><br/> Linux makineleri desteklenmez.
**Şirket içi makineler** | DPM/MABS 'ye yedekleme | Dosya/klasör/paylaşım/birim ve uygulamaya özgü veriler de dahil olmak üzere [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) veya [mabs](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs)tarafından korunan her şeyi yedekleyin.
**Azure VM 'Leri** | Azure VM Aracısı yedekleme uzantısını Çalıştır | Tüm VM 'leri yedekleme
**Azure VM 'Leri** | MARS Aracısı 'nı Çalıştır | Dosyaları, klasörleri, sistem durumunu yedekleyin.<br/><br/> Linux makineleri desteklenmez.
**Azure VM 'Leri** | Azure 'da çalışan MABS/DPM 'ye yedekleme | Dosya/klasör/paylaşım/birim ve uygulamaya özgü veriler de dahil olmak üzere [Mabs](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) veya [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) tarafından korunan her şeyi yedekleyin.

## <a name="what-backup-agents-do-i-need"></a>Hangi yedekleme aracılarına ihtiyacım var?

**Senaryo** | **Aracı**
--- | ---
**Azure VM'lerini yedekleme** | Aracı gerekmez. İlk Azure VM yedeklemesini çalıştırdığınızda, yedekleme için Azure VM uzantısı, Azure VM 'de yüklüdür.<br/><br/> Windows ve Linux desteği desteği.
**Şirket içi Windows makinelerini yedekleme** | MARS aracısını doğrudan makinede indirin, yükleyin ve çalıştırın.
**MARS Aracısı ile Azure VM 'lerini yedekleme** | MARS aracısını doğrudan makinede indirin, yükleyin ve çalıştırın. MARS Aracısı, yedekleme uzantısı ile birlikte çalışabilir.
**Şirket içi makineleri ve Azure VM 'lerini DPM/MABS 'e yedekleme** | DPM veya MABS koruma Aracısı, korumak istediğiniz makinelerde çalışır. MARS Aracısı, Azure 'a yedeklemek için DPM sunucusunda/MABS üzerinde çalışır.

## <a name="which-backup-agent-should-i-use"></a>Hangi yedekleme aracısını kullanmalıyım?

**Backup** | **Çözüm** | **Sınırlama**
--- | --- | ---
**Azure VM 'nin tamamını yedeklemek istiyorum** | VM için yedeklemeyi etkinleştirin. Yedekleme uzantısı Windows veya Linux Azure VM 'de otomatik olarak yapılandırılır. | Tüm VM yedeklenir <br/><br/> Windows VM 'Leri için yedekleme, uygulamayla tutarlıdır. Linux için yedekleme dosya tutarlıdır. Linux VM 'Ler için uygulama açısından uyumlu olması gerekiyorsa, bunu özel betiklerle yapılandırmanız gerekir.
**Azure VM 'de belirli dosya/klasörleri yedeklemek istiyorum** | SANAL makineye MARS aracısını dağıtın.
**Şirket içi Windows makinelerini doğrudan geri almak istiyorum** | MARS aracısını makineye yükler. | Dosyaları, klasörleri ve sistem durumunu Azure 'a yedekleyebilirsiniz. Yedeklemeler uygulama duyarlı değildir.
**Şirket içi Linux makinelerini doğrudan yedeklemek istiyorum** | Azure 'a yedeklemek için DPM veya MABS dağıtmanız gerekir. | Linux konağının yedeklenmesi desteklenmez, yalnızca Hyper-V veya VMWare üzerinde barındırılan Linux konuk makinesini yedekleyebilirsiniz.
**Şirket içinde çalışan uygulamaları yedeklemek istiyorum** | Uygulamayla uyumlu yedeklemeler için makineler DPM veya MABS tarafından korunmalıdır.
**Azure VM 'Ler için parçalı ve esnek yedekleme ve kurtarma ayarları istiyorum** | Yedekleme zamanlamasında ek esneklik sağlamak için Azure 'da çalışan MABS/DPM ile Azure VM 'lerini koruyun ve dosya, klasör, birim, uygulama ve sistem durumunu korumak ve geri yüklemek için tam esneklik elde edin.

## <a name="backup-and-retention"></a>Yedekleme ve bekletme

Azure Backup’ta, *korumalı örnek* başına 9999 kurtarma noktası (yedekleme kopyası veya anlık görüntü olarak da bilinir) sınırı vardır.

- Korumalı örnek, verileri Azure’a yedeklemek için yapılandırılmış bir bilgisayar, sunucu (fiziksel veya sanal) veya iş yüküdür. Verilerin yedek kopyası kaydedildiğinde örnek, korumalı hale gelir.
- Verilerin yedek kopyası, korumayı oluşturur. Kaynak veriler, kaybolmaları veya bozulmaları durumunda yedek kopya kullanılarak geri yüklenebilir.

Aşağıdaki tablo, her bileşen için en fazla yedekleme sıklığını gösterir. Kurtarma noktalarını hangi hızla tükettiğiniz, yedekleme ilkesi yapılandırmanız tarafından belirlenir. Örneğin, her gün bir kurtarma noktası oluşturursanız, çalıştırmadan önce kurtarma noktalarını 27 yıl boyunca koruyabilirsiniz. Aylık bir kurtarma noktası alırsanız, çalıştırmadan önce 833 yıllık kurtarma noktalarını koruyabilirsiniz. Yedekleme hizmeti bir kurtarma noktasında bir süre sonu süresi ayarı yapmaz.

|  | Azure Backup (MARS) aracısı| System Center DPM | Azure Backup Sunucusu | Azure IaaS VM Backup |
| --- | --- | --- | --- | --- |
| Yedekleme sıklığı<br/> (Kurtarma hizmetleri kasasına) |Günde üç yedekleme |Günde iki yedekleme |Günde iki yedekleme |Günde bir yedekleme |
| Yedekleme sıklığı<br/> (diske) |Uygulanamaz |SQL Server için 15 dakikada bir<br/><br/> Diğer iş yükleri için saatte bir |SQL Server için 15 dakikada bir<br/><br/> Diğer iş yükleri için saatte bir |Uygulanamaz |
| Bekletme seçenekleri |Günlük, haftalık, aylık, yıllık |Günlük, haftalık, aylık, yıllık |Günlük, haftalık, aylık, yıllık |Günlük, haftalık, aylık, yıllık |
| Korumalı örnek başına en fazla kurtarma noktası |9999|9999|9999|9999|
| En uzun bekletme süresi |Yedekleme sıklığına bağlıdır |Yedekleme sıklığına bağlıdır |Yedekleme sıklığına bağlıdır |Yedekleme sıklığına bağlıdır |
| Yerel diskteki kurtarma noktaları |Uygulanamaz | Dosya sunucuları için 64<br/><br/> Uygulama Sunucuları için 448 | Dosya sunucuları için 64<br/><br/> Uygulama Sunucuları için 448 |Uygulanamaz |
| Banttaki kurtarma noktaları |Uygulanamaz |Sınırsız |Uygulanamaz |Uygulanamaz |

## <a name="how-does-azure-backup-work-with-encryption"></a>Azure Backup şifrelemeyle nasıl çalışır?

**Şifreleme** | **Şirket içinde yedekleme** | **Azure VM'lerini yedekleme** | **Azure VM 'lerde SQL yedekleme**
--- | --- | --- | ---
Bekleme sırasında şifreleme<br/> (Kalıcı olduğu/depolandığı verileri şifreleme) | Müşteri tarafından belirtilen parola verileri şifrelemek için kullanılır | Azure [depolama hizmeti şifrelemesi (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) kasada depolanan verileri şifrelemek için kullanılır.<br/><br/> Yedekleme, verileri depolamadan önce otomatik olarak şifreler. Azure Storage, verileri almadan önce şifresini çözer. SSE için müşteri tarafından yönetilen anahtarların kullanımı Şu anda desteklenmiyor.<br/><br/> İşletim sistemi ve veri disklerini şifrelemek için [Azure disk şifrelemesi (ade)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) kullanan VM 'leri yedekleyebilirsiniz. Azure Backup, yalnızca BEK ile şifrelenen sanal makineleri ve hem BEK hem de [kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/)ile destekler. [Sınırlamaları](backup-azure-vms-encryption.md#encryption-support)gözden geçirin. | Azure Backup, [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) özellikli SQL Server veritabanlarının veya sunucunun yedeklenmesini destekler. Yedekleme, Azure tarafından yönetilen anahtarlarla veya müşteri tarafından yönetilen anahtarlarla (BYOK) birlikte TDE 'yı destekler.<br/><br/> Yedekleme, yedekleme sürecinin bir parçası olarak herhangi bir SQL şifrelemesi gerçekleştirmez.
Aktarım sırasında şifreleme<br/> (Bir konumdan diğerine taşınan verilerin şifrelenmesi) | Veriler AES256 kullanılarak şifrelenir ve HTTPS üzerinden Azure 'daki kasaya gönderilir | Azure 'da, Azure depolama ve kasa arasındaki veriler HTTPS tarafından korunur. Bu veriler, Azure omurga ağında kalır.<br/><br/> Dosya kurtarma için Iscsı, kasa ve Azure VM arasında aktarılan verilerin güvenliğini sağlar. Güvenli Tünelleme, Iscsı kanalını korur. | Azure 'da, Azure depolama ve kasa arasındaki veriler HTTPS tarafından korunur.<br/><br/> Dosya kurtarma SQL için uygun değil.

## <a name="next-steps"></a>Sonraki adımlar

- Farklı yedekleme senaryolarına yönelik mimari ve bileşenleri [gözden geçirin](backup-architecture.md) .
- Yedekleme ve [Azure VM yedeklemesi](backup-support-matrix-iaas.md)için destek gereksinimlerini ve sınırlamalarını [doğrulayın](backup-support-matrix.md) .

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

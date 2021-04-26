---
title: "Azure sanal makineler 'de SQL Server SQL Server: geçiş kılavuzu"
description: Bu kılavuzda, bireysel SQL Server veritabanlarınızı Azure sanal makinelerinde SQL Server nasıl geçirebileceğinizi öğreneceksiniz.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e7fc4bacd73cec0fdab3117ada190fb7964b4282
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550906"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>Geçiş Kılavuzu: Azure sanal makinelerinde SQL Server SQL Server

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Bu *kılavuzda, değerlendirme* için [Data Migration Yardımcısı](/sql/dma/dma-overview) kullanan yedekleme ve geri yükleme ve günlük aktarma kullanarak Kullanıcı veritabanlarınızı SQL Server Azure sanal makinelerinde SQL Server örneğine *bulmayı*, değerlendirmeyi ve *geçirmeyi* öğreneceksiniz.

Şirket içinde veya üzerinde çalışan SQL Server geçirebilirsiniz:

- Sanal makinelerde (VM) SQL Server.
- Amazon Web Services (AWS) EC2.
- Amazon Ilişkisel veritabanı hizmeti (AWS RDS).
- İşlem altyapısı (Google Cloud Platform [GCP]).

Ek geçiş stratejileri hakkında daha fazla bilgi için [SQL Server VM geçişe genel bakış](sql-server-to-sql-on-azure-vm-migration-overview.md)bölümüne bakın. Diğer geçiş kılavuzlarında bkz. [Azure veritabanı geçiş kılavuzu](https://docs.microsoft.com/data-migration).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Geçiş işlem akışını gösteren diyagram.":::

## <a name="prerequisites"></a>Önkoşullar

SQL Server Azure sanal makinelerinde geçiş yapmak için aşağıdaki kaynaklar gereklidir:

- [Data Migration Yardımcısı](https://www.microsoft.com/download/details.aspx?id=53595).
- Bir [Azure geçişi projesi](../../../migrate/create-manage-projects.md).
- SQL Server kaynağından aynı veya daha fazla sürümü olan [Azure sanal makineler](../../virtual-machines/windows/create-sql-vm-portal.md) örneğinde hazırlanan bir hedef SQL Server.
- [Azure ile şirket içi arasında bağlantı](/azure/architecture/reference-architectures/hybrid-networking).
- [Uygun bir geçiş stratejisi seçme](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Geçiş öncesi

Geçişinize başlamadan önce, SQL ortamınızın topolojisini bulmanız ve hedeflediğiniz geçişinizin uygunluğunu değerlendirmeniz gerekir.

### <a name="discover"></a>Bulma

Şirket içi bilgisayarlardan Azure geçişi değerlendirir geçiş uygunluğu, performans tabanlı boyutlandırma gerçekleştirir ve şirket içinde çalışmaya yönelik maliyet tahminleri sağlar. Geçişi planlamak için, [mevcut veri kaynaklarını ve SQL Server örneklerinizin kullanıldığı özelliklerle ilgili ayrıntıları belirlemek](../../../migrate/concepts-assessment-calculation.md) Için Azure geçişi ' ni kullanın. Bu işlem, kullanımdaki sürüm ve özelliklerle kuruluşunuzdaki tüm SQL Server örneklerinizi belirlemek üzere ağı taramayı içerir.

> [!IMPORTANT]
> SQL Server Örneğiniz için bir hedef Azure sanal makinesi seçtiğinizde, [Azure sanal makinelerinde SQL Server Için performans kılavuzunu](../../virtual-machines/windows/performance-guidelines-best-practices.md)göz önünde bulundurduğunuzdan emin olun.

Daha fazla bulma aracı için bkz. veri geçiş senaryoları için kullanılabilen [Hizmetler ve araçlar](../../../dms/dms-tools-matrix.md#business-justification-phase) .

### <a name="assess"></a>Değerlendirme

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Tüm veri kaynaklarını bulduktan sonra, kaynak ve hedef örnekler arasındaki boşlukları anlamak üzere Azure sanal makinelerinde bir SQL Server örneğine geçiş yapan şirket içi SQL Server örneklerini değerlendirmek için [Data Migration Yardımcısı](/sql/dma/dma-overview) kullanın.

> [!NOTE]
> SQL Server sürümünü _yükseltmiyorsanız_ , bu adımı atlayın ve [geçiş](#migrate) bölümüne geçin.

#### <a name="assess-user-databases"></a>Kullanıcı veritabanlarını değerlendirme

Data Migration Yardımcısı, yeni SQL Server sürümünüzde veritabanı işlevselliğini etkileyebilecek uyumluluk sorunlarını algılayarak modern bir veri platformuna geçişinize yardımcı olur. Data Migration Yardımcısı, hedef ortamınız için performans ve güvenilirlik iyileştirmeleri önerir ve ayrıca, şema, veri ve oturum açma nesnelerinizi kaynak sunucunuzdaki hedef sunucunuza taşımanızı sağlar.

Daha fazla bilgi için bkz. [değerlendirme](/sql/dma/dma-migrateonpremsql).

> [!IMPORTANT]
>Değerlendirme türüne göre, kaynak SQL Server gereken izinler farklı olabilir:
   > - *Özellik eşlik* Danışmanı için, kaynak SQL Server veritabanına bağlanmak için girilen kimlik bilgileri *sysadmin* sunucu rolünün bir üyesi olmalıdır.
   > - *Uyumluluk sorunları* Danışmanı için, belirtilen kimlik bilgilerinin en az `CONNECT SQL` , `VIEW SERVER STATE` ve izinlerine sahip olması gerekir `VIEW ANY DEFINITION` .
   > - Data Migration Yardımcısı, değerlendirmeyi çalıştırmadan önce, seçilen Advisor için gereken izinleri vurgulayacaktır.

#### <a name="assess-the-applications"></a>Uygulamaları değerlendirme

Genellikle bir uygulama katmanı, verileri kalıcı hale getirmek ve değiştirmek için Kullanıcı veritabanlarına erişir. Data Migration Yardımcısı, bir uygulamanın veri erişim katmanını iki şekilde değerlendirebilirler:

- Yakalanan [Genişletilmiş olayları](/sql/relational-databases/extended-events/extended-events) veya Kullanıcı veritabanlarınızın [SQL Server Profiler izlemelerini](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) kullanarak. Ayrıca, A/B testi için de kullanılabilecek bir izleme günlüğü oluşturmak için [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-capture-trace) de kullanabilirsiniz.
- Kod içinde SQL sorgularının bulunmasını ve değerlendirmesini sağlayan [veri erişimi geçiş araç seti 'ni (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)kullanarak, uygulama kaynak kodunu bir veritabanı platformundan diğerine geçirmek için kullanılır. Bu araç C#, Java, XML ve düz metin gibi popüler dosya türlerini destekler. Veri erişimi geçiş araç seti değerlendirmesi gerçekleştirme hakkında bir kılavuz için [Data Migration Yardımcısı](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) blog gönderisine bakın.

Kullanıcı veritabanlarının değerlendirmesi sırasında, yakalanan izleme dosyalarını veya veri erişimi geçiş araç seti dosyalarını [içeri aktarmak](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) için Data Migration Yardımcısı kullanın.

#### <a name="assessments-at-scale"></a>Ölçekte değerlendirmeler

Data Migration Yardımcısı değerlendirmesi gerektiren birden çok sunucunuz varsa, [komut satırı arabirimini](/sql/dma/dma-commandline)kullanarak işlemi otomatikleştirebilirsiniz. Arabirimini kullanarak, geçirme kapsamındaki her bir SQL Server örneği için değerlendirme komutlarını önceden hazırlayabilirsiniz.

Büyük Estates genelinde Özet raporlama için Data Migration Yardımcısı değerlendirmeleri artık [Azure geçişi](/sql/dma/dma-assess-sql-data-estate-to-sqldb)'nde birleştirilebilir.

#### <a name="refactor-databases-with-data-migration-assistant"></a>Data Migration Yardımcısı ile veritabanlarını yeniden düzenleme

Data Migration Yardımcısı değerlendirme sonuçlarına göre, Kullanıcı veritabanlarınızı geçişten sonra doğru şekilde gerçekleştirmesini ve çalışmasını sağlamak için bir dizi öneriniz olabilir. Data Migration Yardımcısı, etkilenen nesneler ve her bir sorunun nasıl çözüleceği hakkında ayrıntılı bilgi sağlar. Üretim geçiş işlemine başlamadan önce tüm kırılmaya karşı değişiklikleri ve davranış değişikliklerini çözdiğinizden emin olun.

Kullanım dışı bırakılan özellikler için, bu değişiklikleri yapmaktan kaçınmak ve geçişi hızlandırmak istiyorsanız, Kullanıcı veritabanlarınızı orijinal [Uyumluluk](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) modunda çalıştırmayı seçebilirsiniz. Bu eylem, kullanım dışı bırakılan öğeler çözümlenene kadar [veritabanı uyumluluğuna yükseltmeyi](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) engeller.

Tüm Data Migration Yardımcısı düzeltmelerini betikten sonra [geçiş sonrası](#post-migration) aşamasında hedef SQL Server veritabanına uygulamanız gerekir.

> [!CAUTION]
> Tüm SQL Server sürümleri tüm uyumluluk modlarını desteklemez. [Hedef SQL Server sürümünüzün](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) seçtiğiniz veritabanı uyumluluğunu desteklediğinden emin olun. Örneğin SQL Server 2019, düzey 90 uyumluluğu olan veritabanlarını desteklemez (SQL Server 2005). Bu veritabanları, en azından uyumluluk düzeyi 100 ' e yükseltme yapılmasını gerektirir.
>

## <a name="migrate"></a>Geçiş

Geçiş öncesi adımları tamamladıktan sonra, Kullanıcı veritabanlarını ve bileşenlerini geçirmeye hazırsınız demektir. Tercih ettiğiniz [geçiş yönteminizi](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)kullanarak veritabanlarınızı geçirin.

Aşağıdaki bölümlerde yedekleme ve geri yükleme kullanarak bir geçiş gerçekleştirme adımları ya da yedekleme ve geri yükleme kullanarak günlük aktarma ile birlikte en az kesinti süresi geçişi sağlanmaktadır.

### <a name="backup-and-restore"></a>Yedekleme ve geri yükleme

Yedekleme ve geri yükleme kullanarak standart bir geçiş gerçekleştirmek için:

1. Gereksinimlerinize göre Azure sanal makinelerinde SQL Server bağlantı kurun. Daha fazla bilgi için bkz. [Azure 'da SQL Server sanal makinesine bağlanma (Kaynak Yöneticisi)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Geçiş için tasarlanan veritabanlarını kullanan tüm uygulamaları duraklatın veya durdurun.
1. [Tek Kullanıcı modu](/sql/relational-databases/databases/set-a-database-to-single-user-mode)kullanılarak kullanıcı veritabanlarının etkin olmadığından emin olun.
1. Şirket içi bir konumda tam bir veritabanı yedeklemesi gerçekleştirin.
1. Şirket içi Yedekleme dosyalarınızı, Uzak Masaüstü, [Azure Veri Gezgini](/azure/data-explorer/data-explorer-overview)veya [AzCopy komut satırı YARDıMCı programını](../../../storage/common/storage-use-azcopy-v10.md)kullanarak sanal makinenize kopyalayın. (2 TB 'den büyük yedeklemeler önerilir.)
1. Tam veritabanı yedeklemelerini Azure sanal makinelerinde SQL Server geri yükleyin.

### <a name="log-shipping-minimize-downtime"></a>Günlük aktarma (kapalı kalma süresini en aza indir)

Yedekleme ve geri yükleme ve günlük aktarma kullanarak en az bir kesinti süresi geçişi gerçekleştirmek için:

1. Gereksinimlerinize göre Azure sanal makinelerinde SQL Server bağlantı kurun. Daha fazla bilgi için bkz. [Azure 'da SQL Server sanal makinesine bağlanma (Kaynak Yöneticisi)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Geçirilecek şirket içi kullanıcı veritabanlarının tam veya toplu olarak günlüğe kaydedilmiş kurtarma modelinde olduğundan emin olun.
1. Şirket içi bir konumda tam bir veritabanı yedeklemesi gerçekleştirin ve var olan tüm veritabanı yedeklemeleri işlerini, günlük zincirini korumak için [copy_only](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) anahtar sözcüğünü kullanacak şekilde değiştirin.
1. Şirket içi Yedekleme dosyalarınızı, Uzak Masaüstü, [Azure Veri Gezgini](/azure/data-explorer/data-explorer-overview)veya [AzCopy komut satırı YARDıMCı programını](../../../storage/common/storage-use-azcopy-v10.md)kullanarak sanal makinenize kopyalayın. (1 TB 'den büyük yedeklemeler önerilir.)
1. Azure sanal makinelerinde SQL Server tam veritabanı yedeklerini geri yükleyin.
1. Azure sanal makinelerinde şirket içi veritabanı ve SQL Server [günlük dağıtımını](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) ayarlayın. Bu görev önceki adımlarda zaten tamamlanmış olduğundan, veritabanlarını yeniden başlatmanız gerektiğinden emin olun.
1. Hedef sunucuya kesin.
   1. Geçirilecek veritabanlarını kullanarak uygulamaları duraklatın veya durdurun.
   1. [Tek Kullanıcı modu](/sql/relational-databases/databases/set-a-database-to-single-user-mode)kullanılarak kullanıcı veritabanlarının etkin olmadığından emin olun.
   1. Hazırsanız, Azure sanal makinelerinde SQL Server için şirket içi veritabanlarının bir günlük gönderimi [denetimli yük devretmesini](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) gerçekleştirin.

### <a name="migrate-objects-outside-user-databases"></a>Nesneleri kullanıcı veritabanlarının dışına geçirme

Kullanıcı veritabanlarınızın kesintisiz çalışması için daha fazla SQL Server nesne gerekli olabilir.

Aşağıdaki tabloda, Kullanıcı veritabanlarınızın geçişten önce veya sonra tamamlanabilmesi için bileşenlerin ve önerilen geçiş yöntemlerinin bir listesi verilmiştir.

| **Özellik** | **Bileşen** | **Geçiş yöntemleri** |
| --- | --- | --- |
| **Veritabanları** | Modelleme | SQL Server Management Studio betiği. |
|| 'Nin | En iyi performansı elde etmek için tempDB 'yi [Azure VM geçici diskine (SSD)](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)taşımayı planlayın. TempDB 'nize uyum sağlamak için yeterli yerel SSD 'ye sahip bir VM boyutu seçtiğinizden emin olun. |
|| FILESTREAM ile kullanıcı veritabanları | Geçiş için [yedekleme ve geri yükleme](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) yöntemlerini kullanın. Data Migration Yardımcısı FILESTREAM ile veritabanlarını desteklemez. |
| **Güvenlik** | SQL Server ve Windows oturumu açma | [Kullanıcı oturumlarını geçirmek](/sql/dma/dma-migrateserverlogins)için Data Migration Yardımcısı kullanın. |
|| SQL Server rolleri | SQL Server Management Studio betiği. |
|| Şifreleme sağlayıcıları | [Azure Key Vault kullanmak için dönüştürmeyi](../../virtual-machines/windows/azure-key-vault-integration-configure.md)öneririz. Bu yordam, [SQL VM kaynak sağlayıcısını](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)kullanır. |
| **Sunucu nesneleri** | Yedekleme cihazları | [Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md)kullanarak veritabanı yedeğiyle değiştirin veya [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) 'a yedeklemeler YAZıN (SQL Server 2012 SP1 CU2 uygulamazsanız +). Bu yordam, [SQL VM kaynak sağlayıcısını](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)kullanır.|
|| Bağlı sunucular | SQL Server Management Studio betiği. |
|| Sunucu Tetikleyicileri | SQL Server Management Studio betiği. |
| **Çoğaltma** | Yerel yayınlar | SQL Server Management Studio betiği. |
|| Yerel aboneler | SQL Server Management Studio betiği. |
| **PolyBase** | PolyBase | SQL Server Management Studio betiği. |
| **Yönetim** | Veritabanı posta | SQL Server Management Studio betiği. |
| **SQL Server Agent** | İşler | SQL Server Management Studio betiği. |
|| Uyarılar | SQL Server Management Studio betiği. |
|| İşleçler | SQL Server Management Studio betiği. |
|| Kullanıldığı | SQL Server Management Studio betiği. |
| **İşletim sistemi** | Dosyalar, dosya paylaşımları | SQL sunucularınız tarafından kullanılan ve Azure sanal makineler hedefinde çoğaltılan diğer dosya veya dosya paylaşımlarını da unutmayın. |

## <a name="post-migration"></a>Geçiş sonrası

Geçiş aşamasını başarılı bir şekilde tamamladıktan sonra, her şeyin olabildiğince sorunsuz ve etkili bir şekilde çalıştığından emin olmak için bir dizi geçiş sonrası görevi gerçekleştirmeniz gerekir.

### <a name="remediate-applications"></a>Uygulamaları düzelt

Veriler hedef ortama geçirildikten sonra, daha önce kaynağı tüketen tüm uygulamaların hedefi tüketmeye başlaması gerekir. Bu görevi yerine getirmeye yönelik bazı durumlarda uygulamalarda değişiklik yapılması gerekebilir.

Kullanıcı veritabanlarına Data Migration Yardımcısı tarafından önerilen düzeltmeleri uygulayın. Tutarlılık sağlamak ve otomasyon için izin vermek üzere bu düzeltmeleri betikten sonra yapmanız gerekir.

### <a name="perform-tests"></a>Testleri gerçekleştirme

Veritabanı geçişine test yaklaşımı aşağıdaki etkinliklerden oluşur:

1. **Doğrulama testleri geliştirme**: veritabanı geçişini test etmek için SQL sorguları kullanmanız gerekir. Hem kaynak hem de hedef veritabanlarında çalıştırılacak doğrulama sorguları oluşturun. Doğrulama sorgularınız tanımladığınız kapsamı kapsamalıdır.
1. **Test ortamı ayarlama**: test ortamı, kaynak veritabanının ve hedef veritabanının bir kopyasını içermelidir. Test ortamını yalıtdığınızdan emin olun.
1. **Doğrulama testlerini Çalıştır**: doğrulama testlerini kaynak ve hedefe göre çalıştırın ve sonra sonuçları çözümleyin.
1. **Performans testlerini çalıştırın**: kaynak ve hedefte performans testlerini çalıştırın ve ardından sonuçları çözümleyip karşılaştırın.

> [!TIP]
> Hedef SQL Server performansını değerlendirmeye yardımcı olması için [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-overview) kullanın.

### <a name="optimize"></a>İyileştirme

Geçiş sonrası aşaması, tüm veri doğruluğu sorunlarını mutabık kılma, performansı doğrulamak ve iş yüküyle ilgili olası performans sorunlarını ele almak için önemlidir.

Bu sorunlar ve bunları azaltmaya yönelik adımlar hakkında daha fazla bilgi için, bkz.:

- [Geçiş sonrası doğrulama ve iyileştirme Kılavuzu](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Azure SQL sanal makinelerinde performansı ayarlama](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Azure maliyet iyileştirme Merkezi](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server için uygulanan hizmetlerin kullanılabilirliğini denetlemek için bkz. [Azure genel altyapı merkezi](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).
- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerde size yardımcı olabilecek Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmetler ve araçlar](../../../dms/dms-tools-matrix.md).
- Azure SQL hakkında daha fazla bilgi için bkz.
   - [Dağıtım seçenekleri](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure Sanal Makineler'de SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure toplam sahip olma maliyeti (TCO) Hesaplayıcı](https://azure.microsoft.com/pricing/tco/calculator/)

- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.:
   - [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Azure 'a geçiş için iş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Lisanslama hakkında daha fazla bilgi için bkz.:
   - [Azure Hibrit Avantajı kendi lisansınızı getirin](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 ve SQL Server 2008 R2 için ücretsiz genişletilmiş destek alın](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Veri erişim katmanı için A/B testi gerçekleştirme hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı genel bakış](/sql/dea/database-experimentation-assistant-overview).

---
title: "Azure SQL yönetilen örneği 'ne SQL Server: geçiş kılavuzu"
description: Bu kılavuz, SQL Server veritabanlarınızı Azure SQL yönetilen örneği 'ne geçirmenize öğretir.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: a1dcb72c30268dd82052e29232e79a485d86f72d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025314"
---
# <a name="migration-guide-sql-server-to-azure-sql-managed-instance"></a>Geçiş Kılavuzu: Azure SQL yönetilen örneği 'ne SQL Server
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Bu kılavuz, SQL Server örneğinizi Azure SQL yönetilen örneği 'ne geçirmenize yardımcı olur. 

Şirket içinde veya üzerinde çalışan SQL Server geçirebilirsiniz: 

- Sanal Makinelerde SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon Ilişkisel veritabanı hizmeti (AWS RDS) 
- İşlem altyapısı (Google Cloud Platform-GCP)  
- SQL Server için bulut SQL (Google Cloud Platform – GCP) 

Daha fazla geçiş bilgisi için [geçişe genel bakış](sql-server-to-managed-instance-overview.md)bölümüne bakın. Diğer geçiş kılavuzlarında, bkz. [Veritabanı geçişi](https://docs.microsoft.com/data-migration). 

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Geçiş işlem akışı":::

## <a name="prerequisites"></a>Önkoşullar 

SQL Server Azure SQL yönetilen örneği 'ne geçirmek için aşağıdaki önkoşulların yerine geldiğinden emin olun: 

- Bir [geçiş yöntemi](sql-server-to-managed-instance-overview.md#compare-migration-options) ve seçilen yöntem için gereken ilgili araçları seçin
- Kaynak SQL Server bağlanabilecek bir makineye [Data Migration Yardımcısı (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) yüklemesi
- Hem kaynak hem de hedefe erişmek için bağlantı ve uygun izinler. 



## <a name="pre-migration"></a>Geçiş öncesi

Kaynak ortamınızın desteklendiğini doğruladıktan sonra, geçiş öncesi aşamasına başlayın. Var olan tüm veri kaynaklarını bulun, geçiş durumunu değerlendirin ve geçişinizi engelleyebilen engelleyici sorunları tespit edin.  

### <a name="discover"></a>Bulma

Bulma aşamasında, kuruluşunuz tarafından kullanılan tüm SQL Server örneklerini ve özellikleri belirlemek için ağı tarayın. 

[Azure geçişi](../../../migrate/migrate-services-overview.md) 'ni kullanarak şirket içi sunuculardan geçiş uygunluğu değerlendirir, performans tabanlı boyutlandırma gerçekleştirin ve bunları Azure 'da çalıştırmaya yönelik maliyet tahminleri sağlayın. 

Alternatif olarak, geçerli BT altyapınızı değerlendirmek için [Microsoft değerlendirme ve planlama araç seti 'ni ("harita araç seti")](https://www.microsoft.com/download/details.aspx?id=7826) kullanın. Araç seti, geçiş planlama işlemini basitleştirmek için güçlü bir envanter, değerlendirme ve raporlama aracı sağlar. 

Bulma aşamasında kullanılabilecek araçlar hakkında daha fazla bilgi için bkz. [veri geçiş senaryoları için kullanılabilen hizmetler ve araçlar](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Değerlendirme 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Veri kaynakları bulunana sonra, geçiş engelleyicileri veya uyumluluk sorunlarını belirlemek için Azure SQL yönetilen örneği 'ne geçirilebilecek tüm şirket içi SQL Server örneklerini değerlendirin. 

Şunları almak üzere veritabanlarını değerlendirmek için Data Migration Yardımcısı (sürüm 4,1 ve üzeri) kullanabilirsiniz: 

- [Azure hedef önerileri](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU önerileri](/sql/dma/dma-sku-recommend-sql-db)

Veritabanı geçiş değerlendirmesini kullanarak ortamınızı değerlendirmek için aşağıdaki adımları izleyin: 

1. [Data Migration Yardımcısı (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)öğesini açın. 
1. **Dosya** ' yı ve ardından **Yeni değerlendirme**' yı seçin. 
1. Bir proje adı belirtin, kaynak sunucu türü olarak SQL Server öğesini seçin ve ardından hedef sunucu türü olarak Azure SQL yönetilen örneği ' ni seçin. 
1. Oluşturmak istediğiniz değerlendirme raporlarının türlerini seçin. Örneğin, veritabanı uyumluluğu ve özellik eşliği. Değerlendirme türüne göre, kaynak SQL Server gereken izinler farklı olabilir.  DMA, değerlendirmeyi çalıştırmadan önce, seçilen Advisor için gereken izinleri vurgulayacaktır.
    - **Özellik eşlik** kategorisi, Azure 'da kullanılabilen kapsamlı bir öneriler kümesi sağlar ve geçiş projenizi planlamada size yardımcı olacak adımları ortadan bir şekilde sunar. (sysadmin izinleri gereklidir)
    - **Uyumluluk sorunları** kategorisi, geçişi engelleyebilen kısmen desteklenen veya desteklenmeyen özellik uyumluluğu sorunlarını ( `CONNECT SQL` , `VIEW SERVER STATE` ve `VIEW ANY DEFINITION` gerekli izinleri) belirler.
1. SQL Server için kaynak bağlantısı ayrıntılarını belirtin ve kaynak veritabanına bağlanın.
1. **Değerlendirmeyi Başlat**' ı seçin. 
1. İşlem tamamlandığında, geçiş engelleme ve özellik eşlik sorunları için değerlendirme raporlarını seçin ve gözden geçirin. Değerlendirme raporu, kuruluşunuzdaki diğer ekiplerle veya personelle paylaşılabilen bir dosyaya da aktarılabilir. 
1. Geçiş sonrası çabalarını en aza indiren veritabanı uyumluluk düzeyini belirleme.  
1. Şirket içi iş yükünüz için en iyi Azure SQL yönetilen örnek SKU 'sunu belirler. 

Daha fazla bilgi için bkz. [Data Migration Yardımcısı ile SQL Server geçiş değerlendirmesi gerçekleştirme](/sql/dma/dma-assesssqlonprem).

SQL yönetilen örneği iş yükünüz için uygun bir hedef değilse, Azure VM 'lerinde SQL Server işletmeniz için uygun bir alternatif hedef olabilir. 

#### <a name="scaled-assessments-and-analysis"></a>Ölçeklenmiş değerlendirmeler ve analiz

Data Migration Yardımcısı, analiz için değerlendirme raporlarının ölçeklendirilmesi ve birleştirilmesi işlemlerini destekler. Verilerin daha geniş bir görünümünü sağlamak üzere ölçek halinde değerlendirilip çözümlenmesi gereken birden fazla sunucunuz ve veritabanınız varsa, daha fazla bilgi edinmek için aşağıdaki bağlantılara tıklayın.

- [PowerShell kullanarak ölçeklenmiş değerlendirmeler gerçekleştirme](/sql/dma/dma-consolidatereports)
- [Power BI kullanarak değerlendirme raporlarını çözümleme](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>Birden çok veritabanı için ölçekte değerlendirme çalıştırmak, [DMA 'Nın komut satırı yardımcı programı](/sql/dma/dma-commandline) kullanılarak otomatik hale getirilebilir ve bu da daha fazla analiz ve hedef hazırlığı Için sonuçların [Azure geçişi](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) 'ne yüklenmesine de olanak tanır.

### <a name="create-a-performance-baseline"></a>Performans temeli oluşturma

Bir SQL yönetilen örneği üzerinde iş yükünüzün performansını SQL Server üzerinde çalışan özgün iş yükünüze göre karşılaştırmanız gerekiyorsa, karşılaştırma için kullanılacak bir performans temeli oluşturun. Daha fazla bilgi için bkz. [performans temeli](sql-server-to-managed-instance-performance-baseline.md) . 

### <a name="create-sql-managed-instance"></a>SQL Yönetilen Örnek oluşturma 

Bulma ve değerlendirme aşamasındaki bilgilere bağlı olarak, uygun boyutta bir hedef SQL yönetilen örneği oluşturun. [Azure Portal](../../managed-instance/instance-create-quickstart.md), [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)veya bir [Azure Resource Manager (ARM) şablonu](../../managed-instance/create-template-quickstart.md)kullanarak bunu yapabilirsiniz. 


## <a name="migrate"></a>Geçiş

Geçiş öncesi aşamalarla ilişkili görevleri tamamladıktan sonra şema ve veri geçişini gerçekleştirmeye hazırlanın. 

Seçtiğiniz [geçiş yönteminizi](sql-server-to-managed-instance-overview.md#compare-migration-options)kullanarak verilerinizi geçirin. 

Bu kılavuzda en popüler iki seçenek (Azure veritabanı geçiş hizmeti (DMS) ve yerel yedekleme ve geri yükleme açıklanır. 

### <a name="database-migration-service"></a>Veritabanı Geçiş Hizmeti

DMS kullanarak geçişler gerçekleştirmek için aşağıdaki adımları izleyin:

1. Bu işlemi ilk kez gerçekleştiriyorsanız, aboneliğinize **Microsoft. DataMigration** kaynak sağlayıcısını kaydedin.
1. Seçtiğiniz bir konumda (tercihen, hedef Azure SQL yönetilen örneğinizle aynı bölgede) bir Azure veritabanı geçiş hizmeti örneği oluşturun ve var olan bir sanal ağı seçin ya da DMS örneğinizi barındırmak için yeni bir tane oluşturun.
1. DMS örneğinizi oluşturduktan sonra yeni bir geçiş projesi oluşturun ve kaynak sunucu türünü **SQL Server** ve hedef sunucu türünü **Azure SQL veritabanı yönetilen örneği** olarak belirtin. Proje oluşturma dikey penceresinde etkinlik türünü seçin-çevrimiçi veya çevrimdışı veri geçişi. 
1.  Geçiş **kaynağı** ayrıntıları sayfasında kaynak SQL Server ayrıntılarını ve hedef Azure SQL yönetilen örnek ayrıntılarını **geçiş hedefi** ayrıntıları sayfasında belirtin. **İleri**’yi seçin.
1. Geçirmek istediğiniz veritabanını seçin. 
1. Veritabanı yedekleme dosyalarınızı içeren **SMB ağ paylaşımının** belirtilmesi için yapılandırma ayarları sağlayın. Ağ paylaşımıyla erişebilen DMS ile Windows Kullanıcı kimlik bilgilerini kullanın. **Azure depolama hesabınızın ayrıntılarını** sağlayın. 
1. Geçiş özetini gözden geçirin ve **geçişi Çalıştır**' ı seçin. Daha sonra geçiş etkinliğini izleyebilir ve veritabanı geçişinizin ilerlemesini denetleyebilirsiniz.
1. Veritabanı geri yüklendikten sonra **tam geçişi Başlat**' ı seçin. Geçiş işlemi, SMB ağ paylaşımında kullanılabilir duruma getirildikten sonra kuyruk günlüğü yedeklemesini kopyalar ve hedefe geri yükler. 
1. Kaynak veritabanınıza gelen tüm trafiği durdurun ve bağlantı dizesini yeni Azure SQL yönetilen örnek veritabanına güncelleştirin. 

Bu geçiş seçeneğinin ayrıntılı bir adım adım öğretici için bkz. [DMS kullanarak Azure SQL yönetilen örneği 'ne çevrimiçi SQL Server geçirme](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   


### <a name="backup-and-restore"></a>Yedekleme ve geri yükleme 

Hızlı ve kolay veritabanı geçişini etkinleştirmek için Azure SQL yönetilen örneği 'nin temel yetilerinden biri, `.bak` [Azure depolama](https://azure.microsoft.com/services/storage/)'da depolanan veritabanı yedekleme () dosyalarının yerel geri yükleme işlemi olur. Yedekleme ve geri yükleme, veritabanınızın boyutuna bağlı olarak zaman uyumsuz bir işlemdir. 

Aşağıdaki diyagramda, işleme ilişkin üst düzey bir genel bakış sunulmaktadır:

![Diyagramda, Azure depolama 'ya giden URL 'ye yedekleme/karşıya yükleme etiketli bir oka ve Azure depolama 'dan yönetilen bir SQL örneğine GERI yükleme etiketli ikinci bir oka sahip SQL Server gösterilmektedir.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> Yedeklemenin gerçekleştirileceği zaman, Azure depolama 'ya yükleyin ve Azure SQL yönetilen örneği için bir yerel geri yükleme işlemi gerçekleştirin, veritabanının boyutunu temel alır. Büyük veritabanları için işleme uyum sağlamak için yeterli kapalı kalma süresini çarpanlara ayırın. 


Yedekleme ve geri yükleme kullanarak geçiş yapmak için şu adımları izleyin: 

1. Veritabanınızı Azure Blob depolama alanına yedekleyin. Örneğin [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)içindeki [URL 'ye yedekleme](/sql/relational-databases/backup-restore/sql-server-backup-to-url) kullanın. SQL Server 2012 SP1 CU2 UYGULAMAZSANıZ ' den önceki veritabanlarını desteklemek için [Microsoft Azure aracını](https://go.microsoft.com/fwlink/?LinkID=324399) kullanın. 
1. SQL Server Management Studio kullanarak Azure SQL yönetilen örneğinizi bağlayın. 
1. Veritabanı yedeklemelerinizle Azure Blob depolama hesabınıza erişmek için paylaşılan erişim Imzasını kullanarak bir kimlik bilgisi oluşturun. Örnek:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Azure Storage blob kapsayıcısından yedeklemeyi geri yükleyin. Örnek: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Geri yükleme tamamlandıktan sonra, SQL Server Management Studio içindeki **Nesne Gezgini** veritabanını görüntüleyin. 

Bu geçiş seçeneği hakkında daha fazla bilgi edinmek için bkz. [SSMS ile bir veritabanını Azure SQL yönetilen örneği 'Ne geri yükleme](../../managed-instance/restore-sample-database-quickstart.md).

> [!NOTE]
> Bir veritabanı geri yükleme işlemi zaman uyumsuzdur ve yeniden denenebilir. Bağlantı kesmeleri veya zaman aşımı süresi dolarsa SQL Server Management Studio bir hata alabilirsiniz. Azure SQL veritabanı, arka planda veritabanını geri yüklemeye çalışmaya devam eder ve [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ve [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) görünümlerini kullanarak geri yüklemenin ilerlemesini izleyebilirsiniz.



## <a name="data-sync-and-cutover"></a>Veri eşitleme ve tam geçişi

Kaynaktan hedefe yapılan veri değişikliklerini sürekli olarak çoğaltacak/eşitleyen geçiş seçenekleri kullanıldığında, kaynak veriler ve şema hedeften itibaren değişebilir ve bu verileri açabilir. Veri eşitleme sırasında, kaynak üzerinde yapılan tüm değişikliklerin, geçiş işlemi sırasında yakalandığından ve hedefe uygulandığından emin olun. 

Verilerin hem kaynak hem de hedefte aynı olduğunu doğruladıktan sonra kaynaktan hedef ortama aktarabilirsiniz. Tam geçişi sırasında en az kesinti iş devamlılığını etkilemediğinden emin olmak için iş/uygulama ekipleriyle tam geçişi sürecini planlamak önemlidir. 

> [!IMPORTANT]
> DMS kullanarak geçişler kapsamında bir tam geçişi gerçekleştirme ile ilişkili belirli adımlarla ilgili ayrıntılı bilgi için bkz. [geçiş tam geçişi gerçekleştirme](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


## <a name="post-migration"></a>Geçiş sonrası

Geçiş aşamasını başarıyla tamamladıktan sonra her şeyin sorunsuz ve verimli bir şekilde çalıştığından emin olmak için bir dizi geçiş sonrası görevi izleyin. 

Geçiş sonrası aşaması, tüm veri doğruluğu sorunlarını mutabık kılma ve tamamlanmanın yanı sıra iş yüküyle ilgili performans sorunlarını ele almak için çok önemlidir. 

### <a name="remediate-applications"></a>Uygulamaları düzelt 

Veriler hedef ortama geçirildikten sonra, daha önce kaynağı tüketen tüm uygulamaların hedefi tüketmeye başlaması gerekir. Bu işlem, bazı durumlarda uygulamalarda değişiklik yapılmasını gerektirir.

### <a name="perform-tests"></a>Testleri gerçekleştirme

Veritabanı geçişi için test yaklaşımı aşağıdaki etkinliklerden oluşur:

1. **Doğrulama testleri geliştirme**: veritabanı geçişini test etmek için SQL sorguları kullanmanız gerekir. Kaynak ve hedef veritabanlarında çalıştırmak için doğrulama sorguları oluşturmanız gerekir. Doğrulama sorgularınız tanımladığınız kapsamı kapsamalıdır.
1. **Test ortamını ayarlama**: test ortamı, kaynak veritabanının ve hedef veritabanının bir kopyasını içermelidir. Test ortamını yalıtdığınızdan emin olun.
1. **Doğrulama testlerini Çalıştır**: doğrulama testlerini kaynak ve hedefe göre çalıştırın ve ardından sonuçları çözümleyin.
1. **Performans testlerini Çalıştır**: kaynak ve hedefte performans testi çalıştırın ve ardından sonuçları çözümleyip karşılaştırın.


## <a name="leverage-advanced-features"></a>Gelişmiş özelliklerden yararlanın 

[Yerleşik yüksek kullanılabilirlik](../../database/high-availability-sla.md), [tehdit algılama](../../database/azure-defender-for-sql.md)ve [iş yükünüzü Izleme ve ayarlama](../../database/monitor-tune-overview.md)gibi SQL yönetilen örneği tarafından sunulan gelişmiş bulut tabanlı özelliklerden faydalandığınızdan emin olun. 

[Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) , büyük bir yönetilen örnek kümesini merkezi bir şekilde izlemenize olanak sağlar.

Bazı SQL Server özellikleri yalnızca [veritabanı uyumluluk düzeyi](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) en son uyumluluk düzeyine (150) değiştirildiğinde kullanılabilir. 


## <a name="next-steps"></a>Sonraki adımlar

- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerin yanı sıra size yardımcı olmak için sunulan Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmet ve araçlar](../../../dms/dms-tools-matrix.md).

- Azure SQL yönetilen örneği hakkında daha fazla bilgi için bkz.:
   - [Azure SQL yönetilen örneği 'nde hizmet katmanları](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server ile Azure SQL yönetilen örneği arasındaki farklar](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/) 


- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [İş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler Azure 'a geçiş](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Veri erişim katmanını bir/B testi gerçekleştirme hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-overview).
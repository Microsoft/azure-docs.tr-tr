---
title: "Azure SQL veritabanı 'na SQL Server: geçiş kılavuzu"
description: SQL Server veritabanlarınızı Azure SQL veritabanı 'na geçirmek için bu kılavuzu izleyin.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 03/19/2021
ms.openlocfilehash: 6ce7de649876ed8ccff1254f25a7213b407dacae
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284094"
---
# <a name="migration-guide-sql-server-to-azure-sql-database"></a>Geçiş Kılavuzu: Azure SQL veritabanı 'na SQL Server
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Bu kılavuzda, SQL Server örneğinizi Azure SQL veritabanı 'na [geçirmeyi](https://azure.microsoft.com/migration/migration-journey) öğreneceksiniz. 

Şirket içinde veya üzerinde çalışan SQL Server geçirebilirsiniz: 

- Sanal Makinelerde SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon Ilişkisel veritabanı hizmeti (AWS RDS) 
- İşlem altyapısı (Google Cloud Platform-GCP)  
- SQL Server için bulut SQL (Google Cloud Platform – GCP) 

Daha fazla geçiş bilgisi için [geçişe genel bakış](sql-server-to-sql-database-overview.md)bölümüne bakın. Diğer geçiş kılavuzlarında, bkz. [Veritabanı geçişi](https://docs.microsoft.com/data-migration). 

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Geçiş işlem akışı":::

## <a name="prerequisites"></a>Önkoşullar 

Azure SQL veritabanı 'na [SQL Server geçişiniz](https://azure.microsoft.com/en-us/migration/sql-server/) için aşağıdaki önkoşullara sahip olduğunuzdan emin olun: 

- Seçilen bir [geçiş yöntemi](sql-server-to-sql-database-overview.md#compare-migration-options) ve ilgili araçlar.
- [Data Migration Yardımcısı (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , kaynak SQL Server bağlanabilecek bir makineye yüklendi.
- Hedef [Azure SQL veritabanı](../../database/single-database-create-quickstart.md). 
- Hem kaynak hem de hedefe erişmek için bağlantı ve uygun izinler. 



## <a name="pre-migration"></a>Geçiş öncesi

Kaynak ortamınızın desteklendiğini doğruladıktan sonra, geçiş öncesi aşamasına başlayın. Var olan tüm veri kaynaklarını bulun, geçiş durumunu değerlendirin ve [Azure bulut geçişinizi](https://azure.microsoft.com/migration)engelleyebilen tüm engelleyici sorunları bulun.

### <a name="discover"></a>Bulma

Bulma aşamasında, kuruluşunuz tarafından kullanılan tüm SQL Server örneklerini ve özellikleri belirlemek için ağı tarayın. 

Şirket içi sunucuların geçiş uygunluğunu değerlendirmek, performans tabanlı boyutlandırma gerçekleştirmek ve bunları Azure 'da çalıştırmaya yönelik maliyet tahminleri sağlamak için [Azure geçişi](../../../migrate/migrate-services-overview.md) ' ni kullanın. 

Alternatif olarak, geçerli BT altyapınızı değerlendirmek için [Microsoft değerlendirme ve planlama araç seti 'ni ("harita araç seti")](https://www.microsoft.com/download/details.aspx?id=7826) kullanın. Araç seti, geçiş planlama işlemini basitleştirmek için güçlü bir envanter, değerlendirme ve raporlama aracı sağlar. 

Bulma aşamasında kullanılabilecek araçlar hakkında daha fazla bilgi için bkz. [veri geçiş senaryoları için kullanılabilen hizmetler ve araçlar](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Değerlendirme 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Veri kaynakları bulunduktan sonra, geçiş engelleyicileri veya uyumluluk sorunlarını belirlemek için Azure SQL veritabanı 'na geçirilebilecek şirket içi SQL Server veritabanlarını değerlendirin. 

Şunları almak üzere veritabanlarını değerlendirmek için Data Migration Yardımcısı (sürüm 4,1 ve üzeri) kullanabilirsiniz: 

- [Azure hedef önerileri](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU önerileri](/sql/dma/dma-sku-recommend-sql-db)

Veritabanı geçiş değerlendirmesini kullanarak ortamınızı değerlendirmek için aşağıdaki adımları izleyin: 

1. [Data Migration Yardımcısı (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)öğesini açın. 
1. **Dosya** ' yı ve ardından **Yeni değerlendirme**' yı seçin. 
1. Bir proje adı belirtin, kaynak sunucu türü olarak SQL Server öğesini seçin ve ardından hedef sunucu türü olarak Azure SQL veritabanı ' nı seçin. 
1. Oluşturmak istediğiniz değerlendirme raporlarının türlerini seçin. Örneğin, veritabanı uyumluluğu ve özellik eşliği. Değerlendirme türüne göre, kaynak SQL Server gereken izinler farklı olabilir.  DMA, değerlendirmeyi çalıştırmadan önce, seçilen Advisor için gereken izinleri vurgulayacaktır.
    - **Özellik eşlik** kategorisi, Azure 'da kullanılabilen kapsamlı bir öneriler kümesi sağlar ve geçiş projenizi planlamada size yardımcı olacak adımları ortadan bir şekilde sunar. (sysadmin izinleri gereklidir)
    - **Uyumluluk sorunları** kategorisi, geçişi engelleyebilen kısmen desteklenen veya desteklenmeyen özellik uyumluluğu sorunlarını ( `CONNECT SQL` , `VIEW SERVER STATE` ve `VIEW ANY DEFINITION` gerekli izinleri) belirler.
1. SQL Server için kaynak bağlantısı ayrıntılarını belirtin ve kaynak veritabanına bağlanın.
1. **Değerlendirmeyi Başlat**' ı seçin. 
1. İşlem tamamlandıktan sonra, geçiş engelleme ve özellik eşlik sorunları için değerlendirme raporlarını seçin ve gözden geçirin. Değerlendirme raporu, kuruluşunuzdaki diğer ekiplerle veya personelle paylaşılabilen bir dosyaya da aktarılabilir. 
1. Geçiş sonrası çabalarını en aza indiren veritabanı uyumluluk düzeyini belirleme.  
1. Şirket içi iş yükünüz için en iyi Azure SQL veritabanı SKU 'sunu belirler. 

Daha fazla bilgi için bkz. [Data Migration Yardımcısı ile SQL Server geçiş değerlendirmesi gerçekleştirme](/sql/dma/dma-assesssqlonprem).

Değerlendirme, veritabanınızın bir Azure SQL veritabanı geçişi için hazırlanmadığından emin olmak için birden çok engelleyiciyle karşılaşırsa, alternatif olarak şunları göz önünde bulundurun:

- Birden çok örnek kapsamlı bağımlılık varsa [Azure SQL yönetilen örneği](../managed-instance/sql-server-to-managed-instance-overview.md)
- Hem SQL veritabanı hem de SQL yönetilen örneği uygun hedefleri gerçekleştiremediğinde [Azure sanal makinelerinde SQL Server](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) . 



#### <a name="scaled-assessments-and-analysis"></a>Ölçeklenmiş değerlendirmeler ve analiz
Data Migration Yardımcısı, analiz için değerlendirme raporlarının ölçeklendirilmesi ve birleştirilmesi işlemlerini destekler. 

Verilerin daha geniş bir görünümünü sağlamak üzere ölçek halinde değerlendirilip çözümlenmesi gereken birden fazla sunucunuz ve veritabanınız varsa, daha fazla bilgi edinmek için aşağıdaki bağlantılara bakın:

- [PowerShell kullanarak ölçeklenmiş değerlendirmeler gerçekleştirme](/sql/dma/dma-consolidatereports)
- [Power BI kullanarak değerlendirme raporlarını çözümleme](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> Birden çok veritabanı için ölçekte değerlendirme çalıştırmak, özellikle de büyük bir deyişle, [DMA komut satırı yardımcı programı](/sql/dma/dma-commandline) kullanılarak otomatik hale getirilebilir ve daha fazla analiz ve hedef hazırlığı Için [Azure geçişi](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) 'ne yüklenebilir.

## <a name="migrate"></a>Geçiş

Geçiş öncesi aşamalarla ilişkili görevleri tamamladıktan sonra şema ve veri geçişini gerçekleştirmeye hazırlanın. 

Seçtiğiniz [geçiş yönteminizi](sql-server-to-sql-database-overview.md#compare-migration-options)kullanarak verilerinizi geçirin. 

Bu kılavuzda en popüler iki seçenek (Data Migration Yardımcısı ve Azure veritabanı geçiş hizmeti) açıklanmaktadır. 

### <a name="data-migration-assistant-dma"></a>Data Migration Yardımcısı (DMA)

DMA kullanarak bir veritabanını Azure SQL veritabanı 'na SQL Server geçirmek için şu adımları izleyin: 

1. Veritabanını indirin ve yükleyin [Geçiş Yardımcısı](https://www.microsoft.com/download/details.aspx?id=53595).
1. Yeni bir proje oluşturun ve proje türü olarak **geçişi** seçin.
1. Kaynak sunucu türünü **SQL Server** ve hedef sunucu türünü **Azure SQL veritabanı** olarak ayarlayın, geçiş kapsamını **şema ve veri** olarak seçin ve **Oluştur**' u seçin.
1. Geçiş projesinde sunucu adı, sunucuya bağlanmak için kimlik bilgileri ve geçirilecek kaynak veritabanı gibi kaynak sunucu ayrıntılarını belirtin.
1. Hedef sunucu ayrıntılarında, Azure SQL veritabanı sunucu adını, sunucuya bağlanmak için kimlik bilgilerini ve geçirilecek hedef veritabanını belirtin.
1. Şema nesnelerini seçin ve hedef Azure SQL veritabanı 'na dağıtın.
1. Son olarak, **veri geçişini Başlat** ' ı seçin ve geçişin ilerlemesini izleyin.

Ayrıntılı bir öğretici için, [Data Migration Yardımcısı kullanarak şirket içi SQL Server veya Azure sanal makinelerinde SQL Server Azure SQL veritabanı 'Na geçirme](/sql/dma/dma-migrateonpremsqltosqldb)konusuna bakın. 


> [!NOTE]
> - Daha fazla kaynak sağlayarak içeri aktarma hızını en üst düzeye çıkarmak için, içeri aktarma işlemi sırasında veritabanınızı daha yüksek bir hizmet katmanına ve işlem boyutuna ölçeklendirin. İçeri aktarma işlemi başarılı olduktan sonra ölçeği azaltabilirsiniz.</br>
> - İçeri aktarılan veritabanının uyumluluk düzeyi, kaynak veritabanınızın uyumluluk düzeyine göre belirlenir. 


### <a name="azure-database-migration-service-dms"></a>Azure Veritabanı Geçiş Hizmeti (DMS)

DMS kullanarak SQL Server veritabanlarını Azure SQL veritabanı 'na geçirmek için aşağıdaki adımları izleyin:

1. Henüz yapmadıysanız, aboneliğinize **Microsoft. DataMigration** kaynak sağlayıcısını kaydedin. 
1. İstediğiniz konumda (tercihen hedef Azure SQL veritabanı ile aynı bölgede) bir Azure veritabanı geçiş hizmeti örneği oluşturun. DMS örneğinizi barındırmak için var olan bir sanal ağı seçin veya yeni bir tane oluşturun.
1. DMS örneğiniz oluşturulduktan sonra yeni bir geçiş projesi oluşturun ve kaynak sunucu türünü **SQL Server** ve hedef sunucu türünü **Azure SQL veritabanı** olarak belirtin. Geçiş projesi oluşturma dikey penceresinde etkinlik türü olarak **çevrimdışı veri geçişi** ' ni seçin.
1. Geçiş **kaynağı** ayrıntıları sayfasında kaynak SQL Server ayrıntılarını ve hedef Azure SQL veritabanı ayrıntılarını **geçiş hedefi** ayrıntıları sayfasında belirtin.
1. Geçiş için kaynak ve hedef veritabanlarını eşleyin ve ardından geçirmek istediğiniz tabloları seçin.
1. Geçiş özetini gözden geçirin ve **geçişi Çalıştır**' ı seçin. Daha sonra geçiş etkinliğini izleyebilir ve veritabanı geçişinizin ilerlemesini denetleyebilirsiniz.

Ayrıntılı bir öğretici için bkz. [DMS kullanarak Azure SQL veritabanına SQL Server geçirme](../../../dms/tutorial-sql-server-to-azure-sql.md). 

## <a name="data-sync-and-cutover"></a>Veri eşitleme ve tam geçişi

Kaynaktan hedefe yapılan veri değişikliklerini sürekli olarak çoğaltacak/eşitleyen geçiş seçenekleri kullanıldığında, kaynak veriler ve şema hedeften itibaren değişebilir ve bu verileri açabilir. Veri eşitleme sırasında, kaynak üzerinde yapılan tüm değişikliklerin, geçiş işlemi sırasında yakalandığından ve hedefe uygulandığından emin olun. 

Verilerin hem kaynak hem de hedefte aynı olduğunu doğruladıktan sonra kaynaktan hedef ortama aktarabilirsiniz. Tam geçişi sırasında en az kesinti iş devamlılığını etkilemediğinden emin olmak için iş/uygulama ekipleriyle tam geçişi sürecini planlamak önemlidir. 

> [!IMPORTANT]
> DMS kullanarak geçişler kapsamında bir tam geçişi gerçekleştirme ile ilişkili belirli adımlarla ilgili ayrıntılı bilgi için bkz. [geçiş tam geçişi gerçekleştirme](../../../dms/tutorial-sql-server-to-azure-sql.md).

## <a name="migration-recommendations"></a>Geçiş önerileri

Azure SQL veritabanı 'na geçişi hızlandırmak için aşağıdaki önerileri göz önünde bulundurmanız gerekir:

|  | Kaynak çakışması | Öneri |
|--|--|--|
| **Kaynak (genellikle şirket içi)** |Kaynakta geçiş sırasında birincil performans sorunu, dikkatle izlenmesi gereken veri dosyasında VERI g/ç ve gecikme süresine sahiptir.  |VERI GÇ ve VERI dosyası gecikmesini temel alarak, bir sanal makine veya fiziksel sunucu olmasına bağlı olarak, Depolama Yöneticisi 'ne ihtiyacınız ve performans sorunlarını hafifletmek için seçenekleri araştırmanıza gerek vardır. |
|**Hedef (Azure SQL veritabanı)**|En büyük sınırlandırma faktörü günlük oluşturma oranı ve günlük dosyasında gecikme süresi olur. Azure SQL veritabanı ile en fazla 96 MB/sn günlük oluşturma oranı edinebilirsiniz. | Geçiş hızını hızlandırmak için, hedef SQL DB İş Açısından Kritik 'yi, 96 MB/sn 'lik maksimum günlük oluşturma oranını elde etmek üzere 5. nesil 8 sanal Core ' a ölçeklendirin ve günlük dosyası için düşük gecikme süresine ulaşın. [Hiper ölçek](../../database/service-tier-hyperscale.md) hizmeti katmanı, seçilen hizmet düzeyinden bağımsız olarak 100 MB/sn günlük hızı sağlar |
|**Ağ** |Gereken ağ bant genişliği, en fazla günlük alma oranı 96 MB/s (768 MB/s) değerine eşit |Şirket içi veri merkezinizden Azure 'a ağ bağlantısına bağlı olarak, en fazla günlük alma hızına uyum sağlamak için ağ bant genişliğinizi (genellikle [Azure ExpressRoute](../../../expressroute/expressroute-introduction.md#bandwidth-options)) kontrol edin. |
|**Data Migration Yardımcısı için kullanılan sanal makine (DMA)** |CPU, DMA çalıştıran sanal makine için birincil darboğazdır |Kullanarak veri geçişini hızlandırmak için göz önünde bulundurmanız gerekenler </br>-Azure işlem yoğunluklu VM 'Ler </br>-DMA çalıştırmak için en az F8s_v2 (8 sanal çekirdek) VM kullanın </br>-VM 'nin hedefle aynı Azure bölgesinde çalıştığından emin olun |
|**Azure Veritabanı Geçiş Hizmeti (DMS)** |DMS için işlem kaynağı çekişmesi ve veritabanı nesneleri dikkate alınması |Premium 4 sanal çekirdek kullanın. DMS, yabancı anahtarlar, Tetikleyiciler, kısıtlamalar ve kümelenmemiş dizinler gibi veritabanı nesnelerinden otomatik olarak ilgilenmez ve el ile müdahale gerektirmez.  |


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

[Yerleşik yüksek kullanılabilirlik](../../database/high-availability-sla.md), [tehdit algılama](../../database/azure-defender-for-sql.md)ve [iş yükünüzü Izleme ve ayarlama](../../database/monitor-tune-overview.md)gibi SQL veritabanı tarafından sunulan gelişmiş bulut tabanlı özelliklerden faydalandığınızdan emin olun. 

Bazı SQL Server özellikleri yalnızca [veritabanı uyumluluk düzeyi](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) en son uyumluluk düzeyine (150) değiştirildiğinde kullanılabilir. 

Daha fazla bilgi için bkz. [geçişten sonra Azure SQL veritabanı 'nı yönetme](../../database/manage-data-after-migrating-to-database.md)


## <a name="next-steps"></a>Sonraki adımlar

- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerin yanı sıra size yardımcı olmak için sunulan Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmet ve araçlar](../../../dms/dms-tools-matrix.md).


- [Azure geçişi](https://azure.microsoft.com/services/azure-migrate) hakkında daha fazla bilgi için bkz.
   - [Azure Geçişi](../../../migrate/migrate-services-overview.md)

- SQL veritabanı hakkında daha fazla bilgi için bkz.
    - [Azure SQL veritabanı 'Na genel bakış](../../database/sql-database-paas-overview.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/) 


- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure 'a geçiş için iş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Buluta Geçiş kaynakları](https://azure.microsoft.com/migration/resources)

- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Veri erişim katmanını bir/B testi gerçekleştirme hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-overview).

---
title: "DB2 'den Azure SQL yönetilen örneği: geçiş kılavuzu"
description: Bu kılavuzda, DB2 için SQL Server Geçiş Yardımcısı kullanarak IBM DB2 veritabanlarınızı Azure SQL yönetilen örneği 'ne geçirmenize öğretilir.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 15d94e17bea6a49bb5bf6fdb6c48252365676042
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228172"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-managed-instance"></a>Geçiş Kılavuzu: IBM DB2 'den Azure SQL yönetilen örneği
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Bu kılavuzda, DB2 için SQL Server Geçiş Yardımcısı kullanarak IBM DB2 veritabanlarınızı Azure SQL yönetilen örneğine geçirmeniz öğretilir. 

Diğer geçiş kılavuzlarında bkz. [Azure veritabanı geçiş kılavuzu](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Önkoşullar 

DB2 veritabanınızı SQL yönetilen örneğine geçirmek için şunlar gerekir:

- [Kaynak ortamınızın desteklendiğini](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites)doğrulamak için.
- [DB2 için SQL Server Geçiş Yardımcısı (SSMA)](https://www.microsoft.com/download/details.aspx?id=54254)indirmek için.
- [Azure SQL yönetilen örneğinin](../../managed-instance/instance-create-quickstart.md)hedef örneği.
- Hem kaynak hem de hedefe erişmek için bağlantı ve yeterli izinler. 

## <a name="pre-migration"></a>Geçiş öncesi

Önkoşulları karşıladıktan sonra ortamınızın topolojisini bulmaya ve geçişinizin uygunluğunu değerlendirmeye hazırsınız demektir. 

### <a name="assess-and-convert"></a>Değerlendirin ve dönüştürün

SQL Server Geçiş Yardımcısı kullanarak bir değerlendirme oluşturun. 

Bir değerlendirme oluşturmak için aşağıdaki adımları izleyin:

1. [DB2 Için SSMA](https://www.microsoft.com/download/details.aspx?id=54254)'yı açın. 
1. **Dosya**  >  **Yeni proje**' yi seçin. 
1. Projenizi kaydetmek için bir proje adı ve konum sağlayın. Ardından, açılan listeden geçiş hedefi olarak Azure SQL yönetilen örneği ' ni seçin ve **Tamam**' ı seçin.

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Belirtmek için proje ayrıntılarını gösteren ekran görüntüsü.":::


1. **DB2 'ye bağlanın** sayfasında DB2 bağlantı ayrıntıları için değerler girin. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="DB2 örneğinize bağlanma seçeneklerini gösteren ekran görüntüsü.":::


1. Geçirmek istediğiniz DB2 şemasını sağ tıklatın ve ardından **rapor oluştur**' u seçin. Bu, bir HTML raporu oluşturur. Alternatif olarak, şemayı seçtikten sonra gezinti çubuğundan **rapor oluştur** ' u seçebilirsiniz.

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Raporun nasıl oluşturulacağını gösteren ekran görüntüsü.":::

1. Dönüştürme istatistiklerini ve hataları ya da uyarıları anlamak için HTML raporunu gözden geçirin. Ayrıca, DB2 nesnelerinin envanterini almak için raporu Excel 'de açabilir ve şema dönüştürmeleri gerçekleştirmek için gereken çaba da yapabilirsiniz. Rapor için varsayılan konum *Ssmaprojects* içindeki rapor klasöründedir.

   Örneğin: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Herhangi bir hata veya uyarı belirlemek için inceettiğiniz raporun ekran görüntüsü":::


### <a name="validate-data-types"></a>Veri türlerini doğrula

Varsayılan veri türü eşlemelerini doğrulayın ve gerekirse gereksinimlere göre değiştirin. Bunu yapmak için aşağıdaki adımları izleyin: 

1. Menüden **Araçlar** ' ı seçin. 
1. **Proje ayarları**' nı seçin. 
1. **Tür eşlemeleri** sekmesini seçin.

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Şema ve tür eşlemeyi seçmeyi gösteren ekran görüntüsü.":::

1. Her tablo için tür eşlemesini, **DB2 meta veri Gezgini**'nde tabloyu seçerek değiştirebilirsiniz. 

### <a name="convert-schema"></a>Şemayı Dönüştür 

Şemayı dönüştürmek için aşağıdaki adımları izleyin:

1. Seçim Deyimlere dinamik veya geçici sorgular ekleyin. Düğüme sağ tıklayın ve ardından **deyim Ekle**' yi seçin. 
1. **Azure SQL yönetilen örneği 'Ne Bağlan**' ı seçin. 
    1. Azure SQL yönetilen örneğine bağlanmak için bağlantı ayrıntılarını girin. 
    1. Açılan listeden hedef veritabanınızı seçin veya yeni bir ad sağlayın, bu durumda hedef sunucuda bir veritabanının oluşturulması gerekir. 
    1. Kimlik doğrulama ayrıntılarını sağlayın. 
    1. **Bağlan**’ı seçin.

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="SQL Server bağlanmak için gereken ayrıntıları gösteren ekran görüntüsü.":::


1. Şemaya sağ tıklayıp **Şemayı Dönüştür**' ü seçin. Alternatif olarak, şemanızı seçtikten sonra üst gezinti çubuğundan **Şemayı Dönüştür** ' i de seçebilirsiniz.

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Şemayı seçip dönüştürmeyi gösteren ekran görüntüsü.":::

1. Dönüştürme tamamlandıktan sonra olası sorunları belirlemek için şemanın yapısını karşılaştırın ve gözden geçirin. Önerilere dayalı olarak sorunları çözün.

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Olası sorunları belirlemek için şemanın yapısını karşılaştırmayı ve incelemeyi gösteren ekran görüntüsü.":::

1. **Çıkış** bölmesinde **sonuçları gözden geçir**' i seçin. **Hata listesi** bölmesinde hataları gözden geçirin. 
1. Çevrimdışı şema düzeltme alıştırması için projeyi yerel olarak kaydedin. **Dosya** menüsünde **projeyi kaydet**' i seçin. Bu sayede, kaynak ve hedef şemaları çevrimdışına almak ve şemayı SQL yönetilen örneği 'ne yayımlamadan önce düzeltme yapmanız için bir fırsat sunulmaktadır.

## <a name="migrate"></a>Geçiş

Veritabanlarınızı değerlendirmek ve tutarsızlıkları doğruladıktan sonra, bir sonraki adım geçiş işlemini yürütmeniz gerekir.

Şemanızı yayımlamak ve verilerinizi geçirmek için şu adımları izleyin:

1. Şemayı yayımlayın. **Azure SQL yönetilen örnek meta veri Gezgini**' nde, **veritabanları** düğümünden veritabanına sağ tıklayın. Ardından **veritabanıyla Synchronize**' ı seçin.

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Veritabanıyla eşitlemeye yönelik seçeneği gösteren ekran görüntüsü.":::

1. Verileri geçirin. **DB2 meta veri Gezgini**'nde geçirmek istediğiniz veritabanına veya nesneye sağ tıklayın ve **veri geçişi**' ni seçin. Alternatif olarak, gezinti çubuğundan **veri geçişini** seçebilirsiniz. Tüm bir veritabanının verilerini geçirmek için veritabanı adının yanındaki onay kutusunu işaretleyin. Ayrı tablolardan verileri geçirmek için veritabanını genişletin, **Tablolar**' ı genişletin ve ardından tablonun yanındaki onay kutusunu işaretleyin. Ayrı tablolardaki verileri atlamak için onay kutusunu temizleyin.

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Şemayı seçme ve veri geçirmeyi seçme seçeneklerini gösteren ekran görüntüsü.":::

1. Hem DB2 hem de SQL yönetilen örneği için bağlantı ayrıntılarını sağlayın. 
1. Geçiş tamamlandıktan sonra **veri geçişi raporunu** görüntüleyin.  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Veri geçiş raporunun nereye incelendiğinin gösterildiği ekran görüntüsü.":::

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)kullanarak Azure SQL yönetilen örneği örneğine bağlanın. Verileri ve şemayı inceleyerek geçişi doğrulayın:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="SQL Server Management Studio şemayı karşılaştırmayı gösteren ekran görüntüsü.":::

## <a name="post-migration"></a>Geçiş sonrası 

Geçiş işlemi tamamlandıktan sonra her şeyin olabildiğince sorunsuz ve etkili bir şekilde çalıştığından emin olmak için geçiş sonrası görevlerden birine gitmeniz gerekir.

### <a name="remediate-applications"></a>Uygulamaları düzelt 

Veriler hedef ortama geçirildikten sonra, daha önce kaynağı tüketen tüm uygulamaların hedefi tüketmeye başlaması gerekir. Bu işlem, bazı durumlarda uygulamalarda değişiklik yapılmasını gerektirir.


### <a name="perform-tests"></a>Testleri gerçekleştirme

Test aşağıdaki etkinliklerden oluşur:

1. **Doğrulama testleri geliştirme**: veritabanı geçişini test etmek için SQL sorguları kullanmanız gerekir. Kaynak ve hedef veritabanlarında çalıştırmak için doğrulama sorguları oluşturmanız gerekir. Doğrulama sorgularınız tanımladığınız kapsamı kapsamalıdır.
1. **Test ortamını ayarlama**: test ortamı, kaynak veritabanının ve hedef veritabanının bir kopyasını içermelidir. Test ortamını yalıtdığınızdan emin olun.
1. **Doğrulama testlerini Çalıştır**: doğrulama testlerini kaynak ve hedefe göre çalıştırın ve ardından sonuçları çözümleyin.
1. **Performans testlerini çalıştırın**: kaynak ve hedefte performans testlerini çalıştırın ve ardından sonuçları çözümleyip karşılaştırın.

## <a name="advanced-features"></a>Gelişmiş özellikler 

[Yerleşik yüksek kullanılabilirlik](../../database/high-availability-sla.md), [tehdit algılama](../../database/azure-defender-for-sql.md)ve [iş yükünüzü Izleme ve ayarlama](../../database/monitor-tune-overview.md)gibi Azure SQL yönetilen örneği tarafından sunulan gelişmiş bulut tabanlı özelliklerden faydalandığınızdan emin olun. 

Bazı SQL Server özellikleri yalnızca [veritabanı uyumluluk düzeyi](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) en son uyumluluk düzeyine değiştirildiğinde kullanılabilir. 

## <a name="migration-assets"></a>Geçiş varlıkları 

Ek Yardım için, gerçek dünyada geçiş projesi katılımı desteğiyle geliştirilen aşağıdaki kaynaklara bakın:

|Varlık  |Description  |
|---------|---------|
|[Veri iş yükü değerlendirmesi modeli ve aracı](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Bu araç, belirli bir iş yükü için önerilen "en uygun" hedef platformları, bulut hazırlığı ve uygulama/veritabanı düzeltme düzeyini sağlar. Basit ve tek tıklamayla bir hesaplama ve rapor oluşturma olanağı sunarak, ve otomatikleştirilmiş ve Tekdüzen hedef platformu karar süreci sağlayarak büyük Emlak değerlendirmelerini hızlandırmaya yardımcı olur.|
|[DB2 zOS veri varlıkları bulma ve değerlendirme paketi](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|SQL betiğini bir veritabanında çalıştırdıktan sonra, sonuçları dosya sistemindeki bir dosyaya dışarı aktarabilirsiniz. Bazı dosya biçimleri, *. csv dahil olmak üzere desteklenir, böylece sonuçları elektronik tablolar gibi dış araçlarla yakalayabilirsiniz. Bu yöntem, çalışma ekranı yüklü olmayan ekiplerle sonuçları kolayca paylaşmak istiyorsanız yararlı olabilir.|
|[IBM DB2 LUW envanter betikleri ve yapıtları](https://github.com/microsoft/DataMigrationTeam/blob/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Bu varlık IBM DB2 LUW sürüm 11,1 sistem tabloları ' nı ziyaret eden bir SQL sorgusu içerir ve şema ve nesne türüne göre nesnelerin sayısını, her şemada "ham veri" tahmini tahminini ve her şemadaki tabloların bir CSV biçiminde depolanarak boyutlandırılmasına neden olur.|
|[Azure 'da db2 LUW saf ölçeği-Kurulum Kılavuzu](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Bu kılavuz, bir DB2 uygulama planı için başlangıç noktası görevi görür. İş gereksinimleri farklılık gösterebilse de, aynı temel model geçerli olur. Bu mimari model, Azure 'daki OLAP uygulamaları için de kullanılabilir.|

Veri SQL Mühendisliği ekibi bu kaynakları geliştirdik. Bu takımın temel kurucu, veri platformu geçiş projelerini Microsoft 'un Azure veri platformu 'na yönelik karmaşık modernleştirmeyi engellemeyi ve hızlandırmanızı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- Çeşitli veritabanı ve veri geçişi senaryolarında size yardımcı olacak Microsoft ve üçüncü taraf hizmetler ve araçlar için bkz. [veri geçişi Için hizmet ve araçlar](../../../dms/dms-tools-matrix.md).

- Azure SQL yönetilen örneği hakkında daha fazla bilgi edinmek için bkz.:
   - [SQL yönetilen örneği 'ne genel bakış](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/) 


- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.:
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure'a geçirilen iş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Veri erişim katmanı A/B testi gerçekleştirme hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-overview).

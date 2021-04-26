---
title: "Azure VM 'de SQL Server için DB2: geçiş kılavuzu"
description: Bu kılavuz, SQL Server for DB2 SQL Server Geçiş Yardımcısı kullanarak IBM DB2 veritabanlarınızı Azure VM 'deki geçirmenize öğretir.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 43eff2bea6f6d95291e9ba9650ff42187e39fc70
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600174"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>Geçiş Kılavuzu: Azure VM 'de SQL Server için IBM DB2
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Bu kılavuzda, DB2 SQL Server Geçiş Yardımcısı for DB2 kullanarak IBM DB2 ' deki SQL Server Azure VM 'den, Kullanıcı veritabanlarınızı nasıl geçirebileceğiniz öğretilir. 

Diğer geçiş kılavuzlarında bkz. [Azure veritabanı geçiş kılavuzu](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Önkoşullar

DB2 veritabanınızı SQL Server geçirmek için şunlar gerekir:

- [Kaynak ortamınızın desteklendiğini](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites)doğrulamak için.
- [DB2 için SQL Server Geçiş Yardımcısı (SSMA)](https://www.microsoft.com/download/details.aspx?id=54254).
- Azure 'da kaynak ortamınız ile SQL Server VM arasındaki [bağlantı](../../virtual-machines/windows/ways-to-connect-to-sql.md) . 
- [Azure VM 'de](../../virtual-machines/windows/create-sql-vm-portal.md)bir hedef SQL Server. 

## <a name="pre-migration"></a>Geçiş öncesi

Önkoşulları karşıladıktan sonra ortamınızın topolojisini bulmaya ve geçişinizin uygunluğunu değerlendirmeye hazırsınız demektir. 

### <a name="assess"></a>Değerlendirme 

Veritabanı nesneleri ve verilerini gözden geçirmek ve geçiş için veritabanlarını değerlendirmek için SıMMA 'yı kullanın. 

Bir değerlendirme oluşturmak için aşağıdaki adımları izleyin:

1. [DB2 Için SSMA](https://www.microsoft.com/download/details.aspx?id=54254)'yı açın. 
1. **Dosya**  >  **Yeni proje**' yi seçin. 
1. Projenizi kaydetmek için bir proje adı ve konum sağlayın. Ardından aşağı açılan listeden bir SQL Server geçiş hedefi seçin ve **Tamam**' ı seçin.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Belirtmek için proje ayrıntılarını gösteren ekran görüntüsü.":::


1. **DB2 'ye bağlanın** sayfasında DB2 bağlantı ayrıntıları için değerler girin.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="DB2 örneğinize bağlanma seçeneklerini gösteren ekran görüntüsü.":::


1. Geçirmek istediğiniz DB2 şemasını sağ tıklatın ve ardından **rapor oluştur**' u seçin. Bu, bir HTML raporu oluşturur. Alternatif olarak, şemayı seçtikten sonra gezinti çubuğundan **rapor oluştur** ' u seçebilirsiniz.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Raporun nasıl oluşturulacağını gösteren ekran görüntüsü.":::

1. Dönüştürme istatistiklerini ve hataları ya da uyarıları anlamak için HTML raporunu gözden geçirin. Ayrıca, DB2 nesnelerinin envanterini almak için raporu Excel 'de açabilir ve şema dönüştürmeleri gerçekleştirmek için gereken çaba da yapabilirsiniz. Rapor için varsayılan konum *Ssmaprojects* içindeki rapor klasöründedir.

   Örneğin: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Herhangi bir hata veya uyarı belirlemek için inceettiğiniz raporun ekran görüntüsü.":::


### <a name="validate-data-types"></a>Veri türlerini doğrula

Varsayılan veri türü eşlemelerini doğrulayın ve gerekirse gereksinimlere göre değiştirin. Bunu yapmak için aşağıdaki adımları izleyin: 

1. Menüden **Araçlar** ' ı seçin. 
1. **Proje ayarları**' nı seçin. 
1. **Tür eşlemeleri** sekmesini seçin.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Şema ve tür eşlemeyi seçmeyi gösteren ekran görüntüsü.":::

1. Her tablo için tür eşlemesini, **DB2 meta veri Gezgini**'nde tabloyu seçerek değiştirebilirsiniz. 

### <a name="convert-schema"></a>Şemayı Dönüştür 

Şemayı dönüştürmek için aşağıdaki adımları izleyin:

1. Seçim Deyimlere dinamik veya geçici sorgular ekleyin. Düğüme sağ tıklayın ve ardından **deyim Ekle**' yi seçin. 
1. **SQL Server Bağlan**' ı seçin. 
    1. Azure VM 'nizin SQL Server örneğine bağlanmak için bağlantı ayrıntılarını girin. 
    1. Hedef sunucuda var olan bir veritabanına bağlanmayı seçin veya hedef sunucuda yeni bir veritabanı oluşturmak için yeni bir ad sağlayın. 
    1. Kimlik doğrulama ayrıntılarını sağlayın. 
    1. **Bağlan**’ı seçin.

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Azure VM 'de SQL Server bağlanmak için gereken ayrıntıları gösteren ekran görüntüsü.":::

1. Şemaya sağ tıklayıp **Şemayı Dönüştür**' ü seçin. Alternatif olarak, şemanızı seçtikten sonra üst gezinti çubuğundan **Şemayı Dönüştür** ' i de seçebilirsiniz.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Şemayı seçip dönüştürmeyi gösteren ekran görüntüsü.":::

1. Dönüştürme bittikten sonra olası sorunları belirlemek için şemanın yapısını karşılaştırın ve gözden geçirin. Önerilere dayalı olarak sorunları çözün. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Olası sorunları belirlemek için şemanın yapısını karşılaştırmayı ve incelemeyi gösteren ekran görüntüsü.":::

1. **Çıkış** bölmesinde **sonuçları gözden geçir**' i seçin. **Hata listesi** bölmesinde hataları gözden geçirin. 
1. Çevrimdışı şema düzeltme alıştırması için projeyi yerel olarak kaydedin. **Dosya** menüsünde **projeyi kaydet**' i seçin. Bu, kaynağı ve hedef şemaları çevrimdışı olarak değerlendirmek ve Azure VM 'de SQL Server için şemayı yayımlamadan önce düzeltme gerçekleştirmek için bir fırsat sağlar.

## <a name="migrate"></a>Geçiş

Veritabanlarınızı değerlendirmek ve tutarsızlıkları doğruladıktan sonra, bir sonraki adım geçiş işlemini yürütmeniz gerekir.

Şemanızı yayımlamak ve verilerinizi geçirmek için şu adımları izleyin:

1. Şemayı yayımlayın. **SQL Server meta veri Gezgini**' nde, **veritabanları** düğümünden veritabanına sağ tıklayın. Ardından **veritabanıyla Synchronize**' ı seçin.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Veritabanıyla eşitlemeye yönelik seçeneği gösteren ekran görüntüsü.":::

1. Verileri geçirin. **DB2 meta veri Gezgini**'nde geçirmek istediğiniz veritabanına veya nesneye sağ tıklayın ve **veri geçişi**' ni seçin. Alternatif olarak, gezinti çubuğundan **veri geçişini** seçebilirsiniz. Tüm bir veritabanının verilerini geçirmek için veritabanı adının yanındaki onay kutusunu işaretleyin. Ayrı tablolardan verileri geçirmek için veritabanını genişletin, **Tablolar**' ı genişletin ve ardından tablonun yanındaki onay kutusunu işaretleyin. Ayrı tablolardaki verileri atlamak için onay kutusunu temizleyin.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Şemayı seçme ve veri geçirmeyi seçme seçeneklerini gösteren ekran görüntüsü.":::

1. Hem DB2 hem de SQL Server örnekleri için bağlantı ayrıntılarını sağlayın. 
1. Geçiş bittikten sonra **veri geçiş raporunu** görüntüleyin:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Veri geçiş raporunun nereye incelendiğinin gösterildiği ekran görüntüsü.":::

1.  [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)kullanarak Azure VM 'de SQL Server örneğine bağlanın. Verileri ve şemayı inceleyerek geçişi doğrulayın.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="SQL Server Management Studio şemayı karşılaştırmayı gösteren ekran görüntüsü.":::

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

## <a name="migration-assets"></a>Geçiş varlıkları 

Ek Yardım için, gerçek dünyada geçiş projesi katılımı desteğiyle geliştirilen aşağıdaki kaynaklara bakın:

|Varlık  |Description  |
|---------|---------|
|[Veri iş yükü değerlendirmesi modeli ve aracı](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Bu araç, belirli bir iş yükü için önerilen "en uygun" hedef platformları, bulut hazırlığı ve uygulama/veritabanı düzeltme düzeyini sağlar. Basit ve tek tıklamayla bir hesaplama ve rapor oluşturma olanağı sunarak, ve otomatikleştirilmiş ve Tekdüzen hedef platformu karar süreci sağlayarak büyük Emlak değerlendirmelerini hızlandırmaya yardımcı olur.|
|[DB2 zOS veri varlıkları bulma ve değerlendirme paketi](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|SQL betiğini bir veritabanında çalıştırdıktan sonra, sonuçları dosya sistemindeki bir dosyaya dışarı aktarabilirsiniz. Bazı dosya biçimleri, *. csv dahil olmak üzere desteklenir, böylece sonuçları elektronik tablolar gibi dış araçlarla yakalayabilirsiniz. Bu yöntem, çalışma ekranı yüklü olmayan ekiplerle sonuçları kolayca paylaşmak istiyorsanız yararlı olabilir.|
|[IBM DB2 LUW envanter betikleri ve yapıtları](https://github.com/microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Bu varlık IBM DB2 LUW sürüm 11,1 sistem tabloları ' nı ziyaret eden bir SQL sorgusu içerir ve şema ve nesne türüne göre nesnelerin sayısını, her şemada "ham veri" tahmini tahminini ve her şemadaki tabloların bir CSV biçiminde depolanarak boyutlandırılmasına neden olur.|
|[Azure 'da db2 LUW saf ölçeği-Kurulum Kılavuzu](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Bu kılavuz, bir DB2 uygulama planı için başlangıç noktası görevi görür. İş gereksinimleri farklılık gösterebilse de, aynı temel model geçerli olur. Bu mimari model, Azure 'daki OLAP uygulamaları için de kullanılabilir.|

Veri SQL Mühendisliği ekibi bu kaynakları geliştirdik. Bu takımın temel kurucu, veri platformu geçiş projelerini Microsoft 'un Azure veri platformu 'na yönelik karmaşık modernleştirmeyi engellemeyi ve hızlandırmanızı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Geçişten sonra, [geçiş sonrası doğrulama ve iyileştirme kılavuzunu](/sql/relational-databases/post-migration-validation-and-optimization-guide)gözden geçirin. 

Çeşitli veritabanı ve veri geçişi senaryolarında size yardımcı olmak için kullanabileceğiniz Microsoft ve üçüncü taraf hizmetler ve araçlar için bkz. [veri geçiş Hizmetleri ve araçları](../../../dms/dms-tools-matrix.md).

Video içeriği için bkz. [geçiş yolculuğuna genel bakış](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).

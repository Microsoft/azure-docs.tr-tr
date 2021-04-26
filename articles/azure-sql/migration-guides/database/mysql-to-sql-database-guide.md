---
title: 'MySQL to Azure SQL veritabanı: geçiş kılavuzu'
description: Bu kılavuzda, MySQL için SQL Server Geçiş Yardımcısı (MySQL için SSMA) kullanarak MySQL veritabanlarınızı bir Azure SQL veritabanına nasıl geçirebileceğinizi öğreneceksiniz.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: d4510aa5cda61dac88102c89b3e03da231380bd6
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389460"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Geçiş Kılavuzu: MySQL to Azure SQL veritabanı
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Bu kılavuzda, MySQL için [SQL Server geçiş](https://azure.microsoft.com/en-us/migration/sql-server/) Yardımcısı (MySQL için SSMA) kullanarak MySQL veritabanınızı BIR Azure SQL veritabanına [nasıl geçirebileceğinizi](https://azure.microsoft.com/migration/migration-journey) öğreneceksiniz. 

Diğer geçiş kılavuzlarında [Azure veritabanı geçiş kılavuzu](https://docs.microsoft.com/data-migration)' na bakın. 

## <a name="prerequisites"></a>Önkoşullar

MySQL veritabanınızı bir SQL veritabanına geçirmeye başlamadan önce aşağıdakileri yapın:

- Kaynak ortamınızın desteklendiğini doğrulayın. Şu anda MySQL 5,6 ve 5,7 destekleniyor. 
- [MySQL için SQL Server Geçiş Yardımcısı](https://www.microsoft.com/download/details.aspx?id=54257)indirip yükleyin.
- Hem kaynağa hem de hedefe erişmek için bağlantı ve yeterli izinlere sahip olduğunuzdan emin olun.

## <a name="pre-migration"></a>Geçiş öncesi 

Önkoşulları karşıladıktan sonra ortamınızın topolojisini bulmaya ve [Azure bulut geçişinizin](https://azure.microsoft.com/migration)uygunluğunu değerlendirmeye hazırsınız demektir.

### <a name="assess"></a>Değerlendirme 

Veritabanı nesneleri ve verilerini gözden geçirmek ve geçiş için veritabanlarını değerlendirmek üzere MySQL için SQL Server Geçiş Yardımcısı (SSMA) kullanın. 

Bir değerlendirme oluşturmak için aşağıdakileri yapın:

1. [MySQL Için SSMA](https://www.microsoft.com/download/details.aspx?id=54257)'yı açın. 
1. **Dosya**' yı ve ardından **Yeni proje**' yi seçin. 
1. **Yeni proje** bölmesinde projeniz için bir ad ve konum girin ve ardından **geçir** açılan listesinde **Azure SQL veritabanı**' nı seçin. 
1. **Tamam**’ı seçin.

   ![Geçiş projenizin adını, konumunu ve hedefini girmeye yönelik "yeni proje" bölmesinin ekran görüntüsü.](./media/mysql-to-sql-database-guide/new-project.png)

1. **MySQL 'e Bağlan** sekmesini seçin ve ardından MySQL sunucunuzu bağlamak için ayrıntıları sağlayın. 

   ![Kaynakla bağlantı belirtmek için "MySQL 'e Bağlan" bölmesinin ekran görüntüsü.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. **MySQL meta verileri gezgin** bölmesinde MySQL şemasını sağ tıklatın ve ardından **rapor oluştur**' u seçin. Alternatif olarak, sağ üst köşedeki **rapor oluştur** sekmesini de seçebilirsiniz.

   ![MySQL için SSMA içindeki "rapor oluşturma" bağlantılarının ekran görüntüsü.](./media/mysql-to-sql-database-guide/create-report.png)

1. Dönüştürme istatistiklerini, hataları ve uyarıları anlamak için HTML raporunu gözden geçirin. Dönüştürme sorunlarını ve çözümlerini anlamak için bunu çözümleyin. 
   Ayrıca, bu raporu Excel 'de açarak MySQL nesnelerinin envanterini alabilir ve şema dönüştürmeleri gerçekleştirmek için gereken çabayı anlayabilirsiniz. Rapor için varsayılan konum SSMAProjects içindeki rapor klasöründedir. Örnek: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![SSMA 'da örnek dönüştürme raporunun ekran görüntüsü.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Veri türlerini doğrulama

Varsayılan veri türü eşlemelerini doğrulayın ve gerekirse gereksinimlere göre değiştirin. Bunun için: 

1. **Araçlar**' ı seçin ve ardından **proje ayarları**' nı seçin.  
1. **Tür eşlemeleri** sekmesini seçin. 

   ![MySQL için SSMA içindeki "tür eşleme" bölmesinin ekran görüntüsü.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Her tablo için tür eşlemesini, **MySQL meta verileri gezgin** bölmesindeki tablo adını seçerek değiştirebilirsiniz. 

### <a name="convert-the-schema"></a>Şemayı Dönüştür 

Şemayı dönüştürmek için aşağıdakileri yapın: 

1. Seçim Dinamik veya özelleştirilmiş sorguları dönüştürmek için, düğümüne sağ tıklayın ve ardından **Add deyimi** seçin. 

1. **Azure SQL veritabanı 'Na Bağlan** sekmesini seçin ve ardından aşağıdakileri yapın:

   a. SQL veritabanınıza bağlanma ayrıntılarını girin.  
   b. Açılan listede, hedef SQL veritabanınızı seçin. Ya da yeni bir ad sağlayabilirsiniz. Bu durumda hedef sunucuda bir veritabanının oluşturulması gerekir.  
   c. Kimlik doğrulama ayrıntılarını sağlayın.  
   d. **Bağlan**’ı seçin.

   ![MySQL için SSMA 'daki "Azure SQL veritabanı 'na bağlanma" bölmesinin ekran görüntüsü.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Üzerinde çalıştığınız şemaya sağ tıklayıp **Şemayı Dönüştür**' ü seçin. Alternatif olarak, sağ üst köşedeki **Şemayı Dönüştür** sekmesini de seçebilirsiniz.

   !["MySQL meta veri Gezgini" bölmesindeki "şemayı Dönüştür" komutunun ekran görüntüsü.](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Dönüştürme tamamlandıktan sonra, olası sorunları belirlemek ve önerilere göre onları ele almak için dönüştürülen nesneleri gözden geçirin ve özgün nesnelerle karşılaştırın. 

   ![Dönüştürülmüş nesnelerin orijinal nesnelere karşılaştırmasını gösteren ekran görüntüsü.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Dönüştürülmüş Transact-SQL metnini özgün kodla karşılaştırın ve önerileri gözden geçirin.

   ![Dönüştürülmüş sorguların kaynak koda karşılaştırmasını gösteren ekran görüntüsü.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. **Çıkış** bölmesinde **sonuçları gözden geçir**' i seçin ve ardından **hata listesi** bölmesindeki hataları gözden geçirin. 
1. Çevrimdışı şema düzeltme alıştırması için projeyi yerel olarak kaydedin. Bunu yapmak için **Dosya**  >  **projesini kaydet**' i seçin. Bu, şemayı SQL veritabanınıza yayımlamadan önce, kaynak ve hedef şemaları çevrimdışı olarak değerlendirmek ve düzeltme gerçekleştirmek için bir fırsat sağlar.

   Dönüştürülen yordamları, burada gösterildiği gibi özgün yordamlarla karşılaştırın: 

   ![Dönüştürülen yordamların özgün yordamlara karşılaştırmasını gösteren ekran görüntüsü.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Veritabanlarını geçirme 

Veritabanlarınızı değerlendirdikten ve tutarsızlıkları doğruladıktan sonra, geçiş işlemini çalıştırabilirsiniz. Geçiş iki adımdan oluşur: şemayı yayımlama ve verileri geçirme. 

Şemayı yayımlamak ve verileri geçirmek için aşağıdakileri yapın: 

1. Şemayı yayımlayın. **Azure SQL veritabanı meta verileri gezgin** bölmesinde, veritabanına sağ tıklayın ve ardından **veritabanıyla birlikte Synchronize**' ı seçin. Bu eylem MySQL şemasını SQL veritabanınıza yayınlar.

   ![Veritabanı eşlemesini gözden geçirmek için "veritabanıyla Synchronize" bölmesinin ekran görüntüsü.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Verileri geçirin. **MySQL meta verileri gezgin** bölmesinde, geçirmek istediğiniz MySQL şemasına sağ tıklayın ve sonra **veri geçişi**' ni seçin. Alternatif olarak, sağ üst köşedeki **verileri geçir** sekmesini de seçebilirsiniz.

   Tüm bir veritabanının verilerini geçirmek için veritabanı adının yanındaki onay kutusunu işaretleyin. Ayrı tablolardan verileri geçirmek için veritabanını genişletin, **Tablolar**' ı genişletin ve ardından tablonun yanındaki onay kutusunu işaretleyin. Ayrı tablolardaki verileri atlamak için onay kutusunu temizleyin.

   !["MySQL meta veri Gezgini" bölmesindeki "verileri geçir" komutunun ekran görüntüsü.](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Geçiş tamamlandıktan sonra, **veri geçiş raporunu** görüntüleyin.
   
   ![Veri geçiş raporunun ekran görüntüsü.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) kullanarak SQL veritabanınıza bağlanın ve verileri ve şemayı inceleyerek geçişi doğrulayın.

   ![SQL Server Management Studio ekran görüntüsü.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Geçiş sonrası 

*Geçiş* aşamasını başarılı bir şekilde tamamladıktan sonra, her şeyin olabildiğince sorunsuz ve etkili bir şekilde çalıştığından emin olmak için bir dizi geçiş sonrası görevi gerçekleştirmeniz gerekir.

### <a name="remediate-applications"></a>Uygulamaları düzelt

Veriler hedef ortama geçirildikten sonra, daha önce kaynağı tüketen tüm uygulamaların hedefi tüketmeye başlaması gerekir. Bu işlem, bazı durumlarda uygulamalarda değişiklik yapılmasını gerektirir.

### <a name="perform-tests"></a>Testleri gerçekleştirme

Veritabanı geçişine test yaklaşımı aşağıdaki etkinliklerden oluşur:

1. **Doğrulama testleri geliştirme**: veritabanı geçişini test etmek için SQL sorguları kullanmanız gerekir. Kaynak ve hedef veritabanlarında çalıştırmak için doğrulama sorguları oluşturmanız gerekir. Doğrulama sorgularınız tanımladığınız kapsamı kapsamalıdır.

1. **Test ortamı ayarlama**: test ortamı, kaynak veritabanının ve hedef veritabanının bir kopyasını içermelidir. Test ortamını yalıtdığınızdan emin olun.

1. **Doğrulama testlerini Çalıştır**: doğrulama testlerini kaynak ve hedefe göre çalıştırın ve sonra sonuçları çözümleyin.

1. **Performans testlerini çalıştırın**: kaynak ve hedefte performans testlerini çalıştırın ve ardından sonuçları çözümleyip karşılaştırın.

### <a name="optimize"></a>İyileştirme

Geçiş sonrası aşaması, veri doğruluğu sorunlarını mutabık kılma, performansı doğrulamak ve iş yüküyle ilgili performans sorunlarını gidermek için çok önemlidir.

Bu sorunlar ve bunları azaltmaya yönelik adımlar hakkında daha fazla bilgi için [geçiş sonrası doğrulama ve iyileştirme Kılavuzu](/sql/relational-databases/post-migration-validation-and-optimization-guide)' na bakın.

## <a name="migration-assets"></a>Geçiş varlıkları

Bu geçiş senaryosunu tamamlamaya yönelik daha fazla yardım için aşağıdaki kaynağa bakın. Gerçek bir geçiş projesi katılımı desteğiyle geliştirilmiştir.

| Başlık | Açıklama |
| --- | --- |
| [Veri iş yükü değerlendirmesi modeli ve aracı](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Belirtilen iş yükleri için önerilen "en uygun" hedef platformları, bulut hazırlığı ve uygulama/veritabanı düzeltme düzeylerini sağlar. Basit, tek tıklamayla hesaplama ve rapor oluşturma işlemleri sunarak, otomatik, Tekdüzen hedef platform karar süreci sağlayarak büyük Emlak değerlendirmelerini hızlandırmaya yardımcı olur. |

Veri SQL Mühendisliği ekibi bu kaynakları geliştirdik. Bu takımın temel kurucu, veri platformu geçiş projelerini Microsoft 'un Azure veri platformu 'na yönelik karmaşık modernleştirmeyi engellemeyi ve hızlandırmanızı sağlar.

## <a name="next-steps"></a>Sonraki adımlar 

- İş yüklerinizi Azure 'a geçirerek fark edebileceğiniz maliyet tasarruflarını tahmin etmeye yardımcı olmak için bkz. [Azure toplam sahiplik Hesaplayıcı maliyeti](https://aka.ms/azure-tco).

- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerde size yardımcı olabilecek Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmet ve araçlar](../../../dms/dms-tools-matrix.md).

- Diğer geçiş kılavuzlarında [Azure veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/)' na bakın. 

- Geçiş videoları için bkz. [geçiş yolculuğuna genel bakış ve önerilen geçiş ve değerlendirme araçları ve Hizmetleri](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).

- Daha fazla [bulut geçiş kaynağı](https://azure.microsoft.com/migration/resources/)için bkz. [bulut geçiş çözümleri](https://azure.microsoft.com/migration).


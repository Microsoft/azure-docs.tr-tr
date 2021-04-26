---
title: "Azure SQL veritabanı 'na erişim: geçiş kılavuzu"
description: Bu kılavuzda, erişim için SQL Server Geçiş Yardımcısı (erişim için SSMA) kullanarak Microsoft Access veritabanlarınızı bir Azure SQL veritabanına nasıl geçirebileceğinizi öğreneceksiniz.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 137adbb045a4c449193f9029b9c72f09ddc439b1
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388474"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Geçiş Kılavuzu: Azure SQL veritabanı 'na erişim

Bu kılavuzda, erişim için [SQL Server geçiş](https://azure.microsoft.com/en-us/migration/sql-server/) Yardımcısı 'Nı kullanarak Microsoft Access veritabanınızı BIR Azure SQL veritabanına [geçirmeyi](https://azure.microsoft.com/migration/migration-journey) öğrenirsiniz (erişim için SSMA).

Diğer geçiş kılavuzlarında [Azure veritabanı geçiş kılavuzu](https://docs.microsoft.com/data-migration)' na bakın. 

## <a name="prerequisites"></a>Önkoşullar

Access veritabanınızı bir SQL veritabanına geçirmeye başlamadan önce aşağıdakileri yapın:

- Kaynak ortamınızın desteklendiğini doğrulayın. 
- [SQL Server Geçiş Yardımcısı erişim için](https://www.microsoft.com/download/details.aspx?id=54255)indirin ve yükleyin.
- Hem kaynak hem de hedefe erişmek için bağlantı ve yeterli izinlere sahip olduğunuzdan emin olun.

## <a name="pre-migration"></a>Geçiş öncesi

Önkoşulları karşıladıktan sonra ortamınızın topolojisini bulmaya ve [Azure bulut geçişinizin](https://azure.microsoft.com/migration)uygunluğunu değerlendirmeye hazırsınız demektir.


### <a name="assess"></a>Değerlendirme 

Veritabanı nesnelerini ve verilerini gözden geçirmek ve geçiş için veritabanlarını değerlendirmek için SSMA 'yı kullanın. 

Bir değerlendirme oluşturmak için aşağıdakileri yapın: 

1. [Erişim Için SSMA](https://www.microsoft.com/download/details.aspx?id=54255)'yı açın. 
1. **Dosya**' yı ve ardından **Yeni proje**' yi seçin. 
1. Projeniz için bir proje adı ve konum sağlayın ve ardından açılan listede geçiş hedefi olarak **Azure SQL veritabanı** ' nı seçin. 
1. **Tamam**’ı seçin. 

   ![Geçiş projenizin adı ve konumunuzu girmeye yönelik "yeni proje" bölmesinin ekran görüntüsü.](./media/access-to-sql-database-guide/new-project.png)

1. **Veritabanı Ekle**' yi seçin ve ardından yeni projenize eklenecek veritabanlarını seçin. 

   ![Erişim için SSMA 'daki "veritabanı Ekle" sekmesinin ekran görüntüsü.](./media/access-to-sql-database-guide/add-databases.png)

1. **Access meta verileri Gezgini** bölmesinde bir veritabanına sağ tıklayın ve ardından **rapor oluştur**' u seçin. Alternatif olarak, sağ üst köşedeki **rapor oluştur** sekmesini de seçebilirsiniz.

   ![Access Metadata Explorer 'da "rapor oluştur" komutunun ekran görüntüsü.](./media/access-to-sql-database-guide/create-report.png)

1. Dönüştürme istatistiklerini ve hataları ya da uyarıları anlamak için HTML raporunu gözden geçirin. Ayrıca, Access nesnelerinin envanterini almak ve şema dönüştürmeleri gerçekleştirmek için gereken çabaları anlamak için raporu Excel 'de açabilirsiniz. Rapor için varsayılan konum SSMAProjects içindeki rapor klasöründedir. Örnek:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![SSMA 'da örnek bir veritabanı rapor değerlendirmesi ekran görüntüsü.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Veri türlerini doğrulama

Varsayılan veri türü eşlemelerini doğrulayın ve gerekirse gereksinimlerinize göre değiştirin. Bunun için:

1. Erişim için SSMA 'da **Araçlar**' ı seçin ve ardından **proje ayarları**' nı seçin. 
1. **Tür eşleme** sekmesini seçin. 

   ![Erişim için SSMA 'daki "tür eşleme" bölmesinin ekran görüntüsü.](./media/access-to-sql-database-guide/type-mappings.png)

1. Her tablo için tür eşlemesini, **Access meta verileri Gezgini** bölmesindeki tablo adını seçerek değiştirebilirsiniz.


### <a name="convert-the-schema"></a>Şemayı Dönüştür

Veritabanı nesnelerini dönüştürmek için aşağıdakileri yapın: 

1. **Azure SQL veritabanı 'Na Bağlan** sekmesini seçin ve ardından aşağıdakileri yapın:

   a. SQL veritabanınıza bağlanma ayrıntılarını girin.  
   b. Açılan listede, hedef SQL veritabanınızı seçin. Ya da yeni bir ad girebilirsiniz, bu durumda hedef sunucuda bir veritabanının oluşturulması gerekir.  
   c. Kimlik doğrulama ayrıntılarını sağlayın.   
   d. **Bağlan**’ı seçin.

   ![Bağlantı ayrıntılarını girmek için "Azure SQL veritabanı 'na bağlanma" bölmesinin ekran görüntüsü.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. **Access meta verileri Gezgini** bölmesinde, veritabanına sağ tıklayın ve ardından **Şemayı Dönüştür**' ü seçin. Alternatif olarak, veritabanınızı seçip **Şemayı Dönüştür** sekmesini de seçebilirsiniz.

   !["Access meta verileri Gezgini" bölmesindeki "şemayı Dönüştür" komutunun ekran görüntüsü.](./media/access-to-sql-database-guide/convert-schema.png)

1. Dönüştürme tamamlandıktan sonra, olası sorunları belirlemek için dönüştürülen nesneleri özgün nesnelerle karşılaştırın ve önerilere göre sorunları çözün.

   ![Dönüştürülmüş nesnelerin kaynak nesnelere karşılaştırmasını gösteren ekran görüntüsü.](./media/access-to-sql-database-guide/table-comparison.png)

    Dönüştürülmüş Transact-SQL metnini özgün kodla karşılaştırın ve önerileri gözden geçirin.

   ![Dönüştürülmüş sorguların kaynak koda karşılaştırmasını gösteren ekran görüntüsü.](./media/access-to-sql-database-guide/query-comparison.png) 

1. Seçim Tek bir nesneyi dönüştürmek için, nesnesine sağ tıklayın ve ardından **Şemayı Dönüştür**' ü seçin. Dönüştürülen nesneler **Access meta veri Gezgini**'nde kalın metinde görünür: 

   ![Access meta veri Gezgini 'ndeki nesnelerin dönüştürüldüğünü gösteren ekran görüntüsü.](./media/access-to-sql-database-guide/converted-items.png)
 
1. **Çıkış** bölmesinde **sonuçları gözden geçir** simgesini seçin ve **hata listesi** bölmesindeki hataları gözden geçirin. 
1. Çevrimdışı şema düzeltme alıştırması için projeyi yerel olarak kaydedin. Bunu yapmak için **Dosya**  >  **projesini kaydet**' i seçin. Bu, kaynak ve hedef şemaları çevrimdışı olarak değerlendirmek ve SQL veritabanınızda yayımlamadan önce düzeltme gerçekleştirmek için bir fırsat sağlar.

## <a name="migrate-the-databases"></a>Veritabanlarını geçirme

Veritabanlarınızı değerlendirdikten ve tutarsızlıkları doğruladıktan sonra, geçiş işlemini çalıştırabilirsiniz. Verilerin geçirilmesi, veri satırlarını işlemler içindeki bir Azure SQL veritabanına taşıtan toplu yükleme işlemidir. Her bir işlemde SQL veritabanınıza yüklenecek satır sayısı proje ayarlarında yapılandırılır.

Şemayı yayımlamak ve erişim için SSMA kullanarak verileri geçirmek için aşağıdakileri yapın: 

1. Daha önce yapmadıysanız **Azure SQL veritabanı 'Na Bağlan**' ı seçin ve bağlantı ayrıntılarını sağlayın. 

1. Şemayı yayımlayın. **Azure SQL veritabanı meta verileri gezgin** bölmesinde, çalıştığınız veritabanına sağ tıklayın ve ardından **veritabanıyla birlikte Synchronize**' ı seçin. Bu eylem MySQL şemasını SQL veritabanına yayınlar.

1. **Veritabanı Ile senkronize** et bölmesinde, kaynak projeniz ve hedef arasındaki eşlemeyi gözden geçirin:

   ![Veritabanıyla eşitlemeyi gözden geçirmek için "veritabanıyla Eşitle" bölmesinin ekran görüntüsü.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. **Access meta verileri Gezgini** bölmesinde, geçirmek istediğiniz öğelerin yanındaki onay kutularını seçin. Tüm veritabanını geçirmek için veritabanının yanındaki onay kutusunu işaretleyin. 

1. Verileri geçirin. Geçirmek istediğiniz veritabanına veya nesneye sağ tıklayın ve ardından **veri geçişi**' ni seçin. Alternatif olarak, sağ üst köşedeki **verileri geçir** sekmesini de seçebilirsiniz.  

   Tüm bir veritabanının verilerini geçirmek için veritabanı adının yanındaki onay kutusunu işaretleyin. Ayrı tablolardan verileri geçirmek için veritabanını genişletin, **Tablolar**' ı genişletin ve ardından tablonun yanındaki onay kutusunu işaretleyin. Ayrı tablolardaki verileri atlamak için onay kutusunu temizleyin.

    !["Access meta verileri Gezgini" bölmesindeki "verileri geçir" komutunun ekran görüntüsü.](./media/access-to-sql-database-guide/migrate-data.png)

1. Geçiş tamamlandıktan sonra **veri geçişi raporunu** görüntüleyin.  

    ![İnceleme için örnek bir rapor gösteren "veri raporu geçirme" bölmesinin ekran görüntüsü.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)kullanarak Azure SQL veritabanınıza bağlanın ve verileri ve şemayı inceleyerek geçişi doğrulayın.

   ![SSMA 'da geçişinizi doğrulamaya yönelik SQL Server Management Studio Nesne Gezgini ekran görüntüsü.](./media/access-to-sql-database-guide/validate-data.png)

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

- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerde size yardımcı olabilecek Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmet ve araçlar](../../../dms/dms-tools-matrix.md).

- Azure SQL veritabanı hakkında daha fazla bilgi için bkz.
   - [SQL veritabanı 'na genel bakış](../../database/sql-database-paas-overview.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/) 


- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.:
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure 'a geçiş için iş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Buluta Geçiş kaynakları](https://azure.microsoft.com/migration/resources)


- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Veri erişim katmanı A/B testi gerçekleştirme hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı genel bakış](/sql/dea/database-experimentation-assistant-overview).

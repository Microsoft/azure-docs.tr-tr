---
title: 'SAP Ade Azure SQL veritabanı: geçiş kılavuzu'
description: Bu kılavuzda, SAP ASE veritabanlarınızı SQL Server Geçiş Yardımcısı for SAP Adapter Server Enterprise ' i kullanarak bir Azure SQL veritabanına nasıl geçirebileceğinizi öğreneceksiniz.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 0538071ffb9d244fb8b3493d6b63b27c6b56a726
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388542"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Geçiş Kılavuzu: SAP aşırı Azure SQL veritabanı 'na

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Bu kılavuzda, SAP Adapter Server Enterprise (ASE) veritabanlarınızı bir Azure SQL veritabanına [nasıl geçireceğinizi](https://azure.microsoft.com/migration/migration-journey) , sap bağdaştırıcısı sunucu enterprise Için [SQL Server geçiş](https://azure.microsoft.com/en-us/migration/sql-server/) Yardımcısı ' nı kullanarak öğreneceksiniz.

Diğer geçiş kılavuzlarında [Azure veritabanı geçiş kılavuzu](https://docs.microsoft.com/data-migration)' na bakın. 

## <a name="prerequisites"></a>Önkoşullar 

SAP SE veritabanınızı SQL veritabanınıza geçirmeye başlamadan önce aşağıdakileri yapın:

- Kaynak ortamınızın desteklendiğini doğrulayın. 
- [SAP Uyarlamalı Server Enterprise (eski ADıYLA SAP Sybase ASE) için SQL Server Geçiş Yardımcısı](https://www.microsoft.com/en-us/download/details.aspx?id=54256)indirin ve yükleyin.
- Hem kaynak hem de hedefe erişmek için bağlantı ve yeterli izinlere sahip olduğunuzdan emin olun.

## <a name="pre-migration"></a>Geçiş öncesi

Önkoşulları karşıladıktan sonra ortamınızın topolojisini bulmaya ve [Azure bulut geçişinizin](https://azure.microsoft.com/migration)uygunluğunu değerlendirmeye hazırsınız demektir.

### <a name="assess"></a>Değerlendirme

[SAP Uyarlamalı Server Enterprise (RESMI SAP Sybase ASE) için SQL Server Geçiş Yardımcısı (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54256)kullanarak veritabanı nesnelerini ve verileri inceleyebilir, geçiş için veritabanlarını değerlendirebilir, Sybase VERITABANı nesnelerini SQL veritabanınıza geçirebilir ve sonra verileri SQL veritabanına geçirebilirsiniz. Daha fazla bilgi edinmek için bkz. [Sybase için SQL Server Geçiş Yardımcısı (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Bir değerlendirme oluşturmak için aşağıdakileri yapın: 

1. Sybase için SSMA 'yı açın. 
1. **Dosya**' yı ve ardından **Yeni proje**' yi seçin. 
1. **Yeni proje** bölmesinde projeniz için bir ad ve konum girin ve ardından **geçir** açılan listesinde **Azure SQL veritabanı**' nı seçin. 
1. **Tamam**’ı seçin.
1. **Sybase 'e Bağlan** bölmesine SAP bağlantı ayrıntılarını girin. 
1. Geçirmek istediğiniz SAP veritabanına sağ tıklayın ve ardından **rapor oluştur**' u seçin. Bu bir HTML raporu oluşturur. Alternatif olarak, sağ üst köşedeki **rapor oluştur** sekmesini de seçebilirsiniz.
1. Dönüştürme istatistiklerini ve hataları ya da uyarıları anlamak için HTML raporunu gözden geçirin. Ayrıca, bir SAP Ao nesnelerinin envanterini almak için raporu Excel 'de açabilir ve şema dönüştürmeleri gerçekleştirmek için gereken çabadan ulaşabilirsiniz. Rapor için varsayılan konum SSMAProjects içindeki rapor klasöründedir. Örnek:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Tür eşlemelerini doğrulama

Şema dönüştürmesi gerçekleştirmeden önce, varsayılan veri türü eşlemelerini doğrulayın veya gereksinimlere göre değiştirin. Bunu, **Araçlar**  >  **proje ayarları**' nı seçerek yapabilir veya **SAP aken meta veri Gezgini**' nde tabloyu seçerek her tablo için tür eşlemesini değiştirebilirsiniz.

### <a name="convert-the-schema"></a>Şemayı Dönüştür

Şemayı dönüştürmek için aşağıdakileri yapın:

1. Seçim Dinamik veya özelleştirilmiş sorguları dönüştürmek için, düğümüne sağ tıklayın ve ardından **Add deyimi** seçin. 
1. **Azure SQL veritabanı 'Na Bağlan** sekmesini seçin ve ardından SQL veritabanınızın ayrıntılarını girin. Mevcut bir veritabanına bağlanmayı veya yeni bir ad sağlamayı seçebilirsiniz; Bu durumda hedef sunucuda bir veritabanı oluşturulur.
1. **Sybase meta verileri gezgin** bölmesinde, ÇALıŞTıĞıNıZ SAP asa şemasını sağ tıklatın ve ardından **Şemayı Dönüştür**' ü seçin. 
1. Şema dönüştürüldükten sonra, dönüştürülen yapıyı özgün yapıya göre karşılaştırın ve gözden geçirin olası sorunları belirler. 

   Şema dönüşümünüzü tamamladıktan sonra, bu projeyi çevrimdışı bir şema düzeltme alıştırması için yerel olarak kaydedebilirsiniz. Bunu yapmak için **Dosya**  >  **projesini kaydet**' i seçin. Bu, şemayı SQL veritabanınıza yayımlamadan önce, kaynak ve hedef şemaları çevrimdışı olarak değerlendirmek ve düzeltme gerçekleştirmek için bir fırsat sağlar.

1. **Çıkış** bölmesinde **sonuçları gözden geçir**' i seçin ve **hata listesi** bölmesindeki hataları gözden geçirin. 
1. Çevrimdışı şema düzeltme alıştırması için projeyi yerel olarak kaydedin. Bunu yapmak için **Dosya**  >  **projesini kaydet**' i seçin. Bu, şemayı SQL veritabanınıza yayımlamadan önce, kaynak ve hedef şemaları çevrimdışı olarak değerlendirmek ve düzeltme gerçekleştirmek için bir fırsat sağlar.

## <a name="migrate-the-databases"></a>Veritabanlarını geçirme 

Gerekli önkoşullara sahip olduktan ve *geçiş öncesi* aşamalandırmayla ilişkili görevleri tamamladıktan sonra şema ve veri geçişini çalıştırmaya hazırsınız demektir.

Şemayı yayımlamak ve verileri geçirmek için aşağıdakileri yapın: 

1. Şemayı yayımlayın. **Azure SQL veritabanı meta verileri gezgin** bölmesinde, veritabanına sağ tıklayın ve ardından **veritabanıyla birlikte Synchronize**' ı seçin. Bu eylem, SAP Ao şemasını SQL veritabanınıza yayınlar.

1. Verileri geçirin. **SAP asa meta verileri gezgin** bölmesinde, GEÇIRMEK ISTEDIĞINIZ SAP asa veritabanına veya nesnesine sağ tıklayın ve ardından **veri geçişi**' ni seçin. Alternatif olarak, sağ üst köşedeki **verileri geçir** sekmesini de seçebilirsiniz. 

   Tüm bir veritabanının verilerini geçirmek için veritabanı adının yanındaki onay kutusunu işaretleyin. Ayrı tablolardan verileri geçirmek için veritabanını genişletin, **Tablolar**' ı genişletin ve ardından tablonun yanındaki onay kutusunu işaretleyin. Ayrı tablolardaki verileri atlamak için onay kutusunu temizleyin. 
1. Geçiş tamamlandıktan sonra, **veri geçiş raporunu** görüntüleyin. 
1. Verileri ve şemayı inceleyerek geçişi doğrulayın. Bunu yapmak için [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)kullanarak SQL veritabanınıza bağlanın.

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


## <a name="next-steps"></a>Sonraki adımlar

- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerde size yardımcı olabilecek Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmet ve araçlar](../../../dms/dms-tools-matrix.md).

- Azure SQL veritabanı hakkında daha fazla bilgi edinmek için bkz.:
   - [SQL veritabanı 'na genel bakış](../../database/sql-database-paas-overview.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/)  

- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.:
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure 'a geçiş için iş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Buluta Geçiş kaynakları](https://azure.microsoft.com/migration/resources)

- Uygulama erişim katmanını değerlendirmek için bkz. [veri erişimi geçiş araç seti (Önizleme)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Veri erişim katmanını bir/B testi gerçekleştirme hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı](/sql/dea/database-experimentation-assistant-overview).

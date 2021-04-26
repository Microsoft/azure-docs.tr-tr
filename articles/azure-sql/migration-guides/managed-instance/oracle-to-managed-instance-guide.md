---
title: "Oracle 'dan Azure SQL yönetilen örneği: geçiş kılavuzu"
description: Bu kılavuzda Oracle şemalarınızı Oracle için SQL Server Geçiş Yardımcısı kullanarak Azure SQL yönetilen örneğine nasıl geçirebileceğinizi öğreneceksiniz.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2bb019a692178c5b44c3589d401d3b2b34c3dccb
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553937"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Geçiş Kılavuzu: Oracle 'dan Azure SQL yönetilen örneği

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Bu kılavuzda Oracle şemalarınızı, Oracle için SQL Server Geçiş Yardımcısı (Oracle için SıMMA) kullanarak Azure SQL yönetilen örneğine nasıl geçirebileceğinizi öğreneceksiniz.

Diğer geçiş kılavuzlarında bkz. [Azure veritabanı geçiş kılavuzu](https://docs.microsoft.com/data-migration).

## <a name="prerequisites"></a>Önkoşullar

Oracle şemanızı SQL yönetilen örneğine geçirmeye başlamadan önce:

- Kaynak ortamınızın desteklendiğini doğrulayın.
- [Oracle Için SSMA](https://www.microsoft.com/en-us/download/details.aspx?id=54258)'yı indirin.
- [SQL yönetilen örnek](../../managed-instance/instance-create-quickstart.md) hedefi olmalıdır.
- Oracle ve [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql) [için sımma için gerekli izinleri](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) alın.
 
## <a name="pre-migration"></a>Geçiş öncesi

Önkoşulları karşıladıktan sonra ortamınızın topolojisini bulmaya ve geçişinizin uygunluğunu değerlendirmeye hazırsınız demektir. İşlemin bu bölümünde, geçirmeniz gereken veritabanlarının envanterini oluşturma, bu veritabanlarını olası geçiş sorunları veya engelleyiciler için değerlendirme ve ardından kapsanmayan tüm öğeleri çözme işlemleri yer alabilir.

### <a name="assess"></a>Değerlendirme

Oracle için SSMA 'yı kullanarak veritabanı nesnelerini ve verileri inceleyebilir, geçiş için veritabanlarını değerlendirebilir, veritabanı nesnelerini SQL yönetilen örneğine geçirebilir ve son olarak veritabanına veri geçirebilirsiniz.

Değerlendirme oluşturmak için:

1. [Oracle Için SSMA](https://www.microsoft.com/en-us/download/details.aspx?id=54258)'yı açın.
1. **Dosya**' yı ve ardından **Yeni proje**' yi seçin.
1. Projenizi kaydetmek için bir proje adı ve konum girin. Ardından aşağı açılan listeden geçiş hedefi olarak **Azure SQL yönetilen örneği** ' ni seçin ve **Tamam**' ı seçin.

   ![Yeni proje gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/new-project.png)

1. **Oracle 'A Bağlan**' ı seçin. Oracle **'A Bağlan** Iletişim kutusunda Oracle bağlantı ayrıntıları için değerler girin.

   ![Oracle 'a Bağlan ' ın gösterildiği ekran görüntüsü.](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

1. Geçirmek istediğiniz Oracle şemalarını seçin.

   ![Oracle şeması seçimini gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/select-schema.png)

1. **Oracle meta veri Gezgini**' nde, geçirmek istediğiniz Oracle şemasını sağ tıklatın ve ardından bir HTML raporu oluşturmak Için **rapor oluştur** ' u seçin. Alternatif olarak, bir veritabanı seçip **rapor oluştur** sekmesini seçebilirsiniz.

   ![Rapor Oluştur ' un gösterildiği ekran görüntüsü.](./media/oracle-to-managed-instance-guide/create-report.png)

1. Dönüştürme istatistiklerini ve hataları ya da uyarıları anlamak için HTML raporunu gözden geçirin. Ayrıca, Oracle nesnelerinin envanterini almak için raporu Excel 'de açabilir ve şema dönüştürmeleri gerçekleştirmek için gereken çaba da yapabilirsiniz. Rapor için varsayılan konum SSMAProjects içindeki rapor klasöründedir.

   Örnek için bkz. `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Bir değerlendirme raporu gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Veri türlerini doğrulama

Varsayılan veri türü eşlemelerini doğrulayın ve gerekirse gereksinimlere göre değiştirin. Bunu yapmak için aşağıdaki adımları izleyin:

1. Oracle için SSMA 'da **Araçlar**' ı seçin ve ardından **proje ayarları**' nı seçin.
1. **Tür eşleme** sekmesini seçin.

   ![Tür eşlemeyi gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. **Oracle meta veri Gezgini**' nde tabloyu seçerek her tablo için tür eşlemesini değiştirebilirsiniz.

### <a name="convert-the-schema"></a>Şemayı Dönüştür

Şemayı dönüştürmek için:

1. Seçim Deyimlere dinamik veya geçici sorgular ekleyin. Düğüme sağ tıklayın ve ardından **deyim Ekle**' yi seçin.
1. **Azure SQL yönetilen örneği 'Ne Bağlan** sekmesini seçin.
    1. Veritabanınızı **SQL veritabanı yönetilen örneği**'ne bağlamak için bağlantı ayrıntılarını girin.
    1. Açılan listeden hedef veritabanınızı seçin veya yeni bir ad girin, bu durumda hedef sunucuda bir veritabanının oluşturulması gerekir.
    1. Kimlik doğrulama ayrıntılarını girin ve **Bağlan**' ı seçin.

    ![Azure SQL yönetilen örneği 'ne bağlanmayı gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. **Oracle meta veri Gezgini**' nde Oracle şemasına sağ tıklayıp **Şemayı Dönüştür**' ü seçin. Alternatif olarak, şemanızı seçip **Şemayı Dönüştür** sekmesini seçebilirsiniz.

   ![Şemayı Dönüştür ' ü gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. Dönüştürme tamamlandıktan sonra, olası sorunları belirlemek ve önerilere göre bunları ele almak için dönüştürülen nesneleri özgün nesnelere karşılaştırın ve gözden geçirin.

   ![Tablo önerilerini karşılaştırmayı gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/table-comparison.png)

1. Dönüştürülmüş Transact-SQL metnini özgün kodla karşılaştırın ve önerileri gözden geçirin.

   ![Yordam önerilerini karşılaştıran gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. Çıkış bölmesinde **sonuçları gözden geçir** ' i seçin ve **hata listesi** bölmesinde hataları gözden geçirin.
1. Çevrimdışı şema düzeltme alıştırması için projeyi yerel olarak kaydedin. **Dosya** menüsünde **projeyi kaydet**' i seçin. Bu adım, şemayı SQL yönetilen örneği 'ne yayımlamadan önce, kaynak ve hedef şemaları çevrimdışı olarak değerlendirmek ve düzeltme gerçekleştirmek için bir fırsat sağlar.

## <a name="migrate"></a>Geçiş

Veritabanlarınızı değerlendirmek ve tutarsızlıkları doğruladıktan sonra, bir sonraki adım geçiş işlemini çalıştırmak olur. Geçiş iki adımdan oluşur: şemayı yayımlama ve verileri geçirme.

Şemanızı yayımlamak ve verilerinizi geçirmek için:
1. **Azure SQL yönetilen örnek meta veri Gezgini** ' nde **veritabanları** düğümünden veritabanına sağ tıklayıp **veritabanını eşitler**' ı seçerek şemayı yayımlayın.

   ![Veritabanıyla eşitlemeyi gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)
   

1. Kaynak projeniz ve Hedefinizdeki eşlemeyi gözden geçirin.

   ![Veritabanı incelemesinin eşitlemesini gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. **Oracle meta veri Gezgini** 'nde geçirmek istediğiniz şemaya veya nesneye sağ tıklayıp **verileri geçir**' i seçerek verileri geçirin. Alternatif olarak, **verileri geçir** sekmesini de seçebilirsiniz. Tüm bir veritabanının verilerini geçirmek için veritabanı adının yanındaki onay kutusunu işaretleyin. Ayrı tablolardan verileri geçirmek için veritabanını genişletin, **Tablolar**' ı genişletin ve ardından tabloların yanındaki onay kutularını seçin. Ayrı tablolardaki verileri atlamak için onay kutularını temizleyin.

   ![Veri geçirmeyi gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Hem Oracle hem de SQL yönetilen örneği için bağlantı ayrıntılarını girin.
1. Geçiş tamamlandıktan sonra, **veri geçiş raporunu** görüntüleyin.

   ![Veri geçiş raporunu gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)kullanarak SQL yönetilen örneği örneğine bağlanın ve verileri ve şemayı inceleyerek geçişi doğrulayın.

   ![Oracle için SSMA doğrulaması gösteren ekran görüntüsü.](./media/oracle-to-managed-instance-guide/validate-data.png)

Alternatif olarak, geçişi gerçekleştirmek için SQL Server Integration Services de kullanabilirsiniz. Daha fazla bilgi edinmek için şu makalelere bakın:

- [SQL Server Integration Services kullanmaya başlama](/sql/integration-services/sql-server-integration-services)
- [Azure ve hibrit veri hareketi için SQL Server Integration Services](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Geçiş sonrası

*Geçiş* aşamasını başarılı bir şekilde tamamladıktan sonra, her şeyin olabildiğince sorunsuz ve etkili bir şekilde çalıştığından emin olmak için bir dizi geçiş sonrası görevi gerçekleştirmeniz gerekir.

### <a name="remediate-applications"></a>Uygulamaları düzelt

Veriler hedef ortama geçirildikten sonra, daha önce kaynağı tüketen tüm uygulamaların hedefi tüketmeye başlaması gerekir. Bu adımın yapılması, bazı durumlarda uygulamalarda değişiklik yapılmasını gerektirecektir.

[Veri erişimi geçiş araç seti](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) , Java kaynak kodunuzu analiz etmenizi ve veri erişimi API 'si çağrılarını ve sorguları algılamayı sağlayan Visual Studio Code için bir uzantıdır. Araç seti size, yeni veritabanı arka ucunun desteklenmesi için nelerin kullanılması gerektiğine ilişkin tek bölmeli bir görünüm sağlar. Daha fazla bilgi edinmek için bkz. [Java uygulamamızı Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) blog gönderisine geçirme.

### <a name="perform-tests"></a>Testleri gerçekleştirme

Veritabanı geçişine test yaklaşımı aşağıdaki etkinliklerden oluşur:

1. **Doğrulama testleri geliştirme**: veritabanı geçişini test etmek için SQL sorguları kullanmanız gerekir. Kaynak ve hedef veritabanlarında çalıştırmak için doğrulama sorguları oluşturmanız gerekir. Doğrulama sorgularınız tanımladığınız kapsamı kapsamalıdır.
2. **Test ortamı ayarlama**: test ortamı, kaynak veritabanının ve hedef veritabanının bir kopyasını içermelidir. Test ortamını yalıtdığınızdan emin olun.
3. **Doğrulama testlerini Çalıştır**: doğrulama testlerini kaynak ve hedefe göre çalıştırın ve sonra sonuçları çözümleyin.
4. **Performans testlerini çalıştırın**: kaynak ve hedefte performans testlerini çalıştırın ve ardından sonuçları çözümleyip karşılaştırın.

### <a name="optimize"></a>İyileştirme

Geçiş sonrası aşaması, veri doğruluğu sorunlarını mutabık kılma, performansı doğrulamak ve iş yüküyle ilgili performans sorunlarını gidermek için çok önemlidir.

> [!NOTE]
> Bu sorunlar ve bunları azaltmaya yönelik adımlar hakkında daha fazla bilgi için [geçiş sonrası doğrulama ve iyileştirme Kılavuzu](/sql/relational-databases/post-migration-validation-and-optimization-guide)' na bakın.

## <a name="migration-assets"></a>Geçiş varlıkları

Bu geçiş senaryosunu tamamlamaya yönelik daha fazla yardım için aşağıdaki kaynaklara bakın. Bunlar, gerçek dünyada geçiş projesi katılımı desteğiyle geliştirilmiştir.

| **Başlık/bağlantı**                                                                                                                                          | **Açıklama**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Veri Iş yükü değerlendirmesi modeli ve aracı](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Bu araç, belirli bir iş yükü için önerilen "en uygun" hedef platformları, bulut hazırlığı ve uygulama ya da veritabanı düzeltme düzeyini sağlar. Otomatikleştirilmiş ve Tekdüzen hedef platformu karar süreci sağlayarak büyük Emlak değerlendirmelerine yardımcı olan basit, tek tıklamayla hesaplama ve rapor oluşturma işlemlerini sunar.                                                          |
| [Oracle envanter betiği yapıtları](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Bu varlık, Oracle sistem tabloları ' nı ziyaret eden bir PL/SQL sorgusu içerir ve şema türüne, nesne türüne ve duruma göre nesne sayısını sağlar. Ayrıca, her şemada ham verilerin kabaca bir tahminini ve her şemadaki tabloların, bir CSV biçiminde depolanmış sonuçlarla birlikte boyutlandırılmasını sağlar.                                                                                                               |
| [SSMA Oracle değerlendirmesi toplama & birleştirme işlemini otomatikleştirin](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Bu kaynak kümesi, konsol modunda bir SSMA değerlendirmesi çalıştırmak için gereken XML dosyalarını oluşturmak üzere bir. csv dosyasını girdi olarak (proje klasörlerinde sources.csv) kullanır. source.csv, müşteri tarafından mevcut Oracle örneklerinin envanterini temel alarak sağlanır. Çıktı dosyaları AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml ve VariableValueFile.xml.|
| [Oracle ortak hataları ve bunların nasıl düzeltileceğini gösteren SSMA](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle ile WHERE yan tümcesinde skalar olmayan bir koşul atayabilirsiniz. Ancak SQL Server bu tür bir koşulu desteklemez. Sonuç olarak, Oracle için SSMA, WHERE yan tümcesinde skalar olmayan bir koşula sahip sorguları dönüştürmez. Bunun yerine, O2SS0001 hatasını üretir. Bu Teknik İnceleme, sorun hakkında daha fazla ayrıntı ve sorunu çözmeye yönelik yolları sağlar.          |
| [SQL Server geçiş el kitabı için Oracle](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Bu belge, bir Oracle şemasını SQL Server veritabanının en son sürümüne geçirme ile ilişkili görevlere odaklanır. Geçiş, özelliklerde veya işlevlerde değişiklik gerektiriyorsa, veritabanını kullanan uygulamalardaki her bir değişikliğin olası etkisi dikkatle düşünülmelidir.                                                     |

Veri SQL Mühendisliği ekibi bu kaynakları geliştirdik. Bu takımın temel kurucu, veri platformu geçiş projelerini Microsoft 'un Azure veri platformu 'na yönelik karmaşık modernleştirmeyi engellemeyi ve hızlandırmanızı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerde size yardımcı olabilecek Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmetler ve araçlar](../../../dms/dms-tools-matrix.md).

- SQL yönetilen örneği hakkında daha fazla bilgi edinmek için bkz.:
  - [Azure SQL yönetilen örneği 'ne genel bakış](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure toplam sahip olma maliyeti (TCO) Hesaplayıcı](https://azure.microsoft.com/en-us/pricing/tco/calculator/)

- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.:
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure 'a geçiş için iş yüklerini maliyetlendirme ve boyutlandırma için en iyi yöntemler](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Video içeriği için bkz.:
    - [Değerlendirme ve geçiş gerçekleştirmek için önerilen geçiş yolculuğuna ve araç ve hizmetlere genel bakış](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)

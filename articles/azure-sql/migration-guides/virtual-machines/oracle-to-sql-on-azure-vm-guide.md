---
title: "Azure sanal makineler 'de SQL Server Oracle: geçiş kılavuzu"
description: Bu kılavuzda, Oracle için SQL Server Geçiş Yardımcısı kullanarak Oracle şemalarınızı Azure sanal makinelerinde SQL Server geçirmeniz öğretilir.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: d4fb33e8e904d12e242f7eeaf9c2dc50a02eff4d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961260"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Geçiş Kılavuzu: Azure sanal makinelerinde SQL Server için Oracle
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Bu kılavuzda, Oracle için SQL Server Geçiş Yardımcısı kullanarak Oracle şemalarınızı Azure sanal makinelerinde SQL Server geçirmeniz öğretilir. 

Diğer geçiş kılavuzlarında, bkz. [Veritabanı geçişi](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Önkoşullar 

Oracle şemanızı Azure sanal makinelerinde SQL Server geçirmek için şunlar gerekir:

- Desteklenen bir kaynak ortam.
- [Oracle için SQL Server Geçiş Yardımcısı (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Hedef [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- Oracle ve [sağlayıcı](/sql/ssma/oracle/connect-to-oracle-oracletosql) [için sımma için gerekli izinler](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) .
- Kaynak ve hedefe erişmek için bağlantı ve yeterli izinler. 


## <a name="pre-migration"></a>Geçiş öncesi

Buluta geçirmeye hazırlanmak için, kaynak ortamınızın desteklendiğini ve herhangi bir ön koşul olduğunu doğrulayın. Bunun yapılması, etkili ve başarılı bir geçiş sağlanmasına yardımcı olur.

İşlemin bu bölümü şunları içerir: 
- Geçirmeniz gereken veritabanlarının envanterini oluşturma.
- Olası geçiş sorunları veya engelleyiciler için bu veritabanları değerlendiriliyor. 
- Ortaya çıkan sorunları çözme. 

### <a name="discover"></a>Bulma

Mevcut veri kaynaklarını ve işletmenizin kullandığı özelliklerle ilgili ayrıntıları belirlemek için [harita araç setini](https://go.microsoft.com/fwlink/?LinkID=316883) kullanın. Bunun yapılması, geçiş konusunda daha iyi bir anlayışın ve bu işlemin planlanmasına yardımcı olur. Bu işlem, kuruluşunuzun Oracle örneklerini ve kullanmakta olduğunuz sürümleri ve özellikleri belirlemek için ağın taranmasını içerir.

Bir envanter taraması yapmak için harıta araç setini kullanmak için şu adımları izleyin: 


1. [Harita araç setini](https://go.microsoft.com/fwlink/?LinkID=316883)açın.


1. **Veritabanı oluştur/Seç**' i seçin:

   ![Veritabanı oluştur/Seç seçeneğini gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. **Envanter veritabanı oluştur**' u seçin. Oluşturmakta olduğunuz yeni envanter veritabanı için bir ad girin, kısa bir açıklama girin ve ardından **Tamam**' ı seçin: 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Envanter veritabanı oluşturma arabirimini gösteren ekran görüntüsü.":::

1. Envanter **ve değerlendirme Sihirbazı**'nı açmak için **Envanter verilerini topla** ' yı seçin:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Envanter verilerini topla bağlantısını gösteren ekran görüntüsü.":::


1. **Envanter ve değerlendirme sihirbazında** **Oracle**' ı seçin ve ardından **İleri**' yi seçin:

   ![Envanter ve değerlendirme sihirbazının envanter senaryoları sayfasını gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. İş gereksinimlerinize ve ortamınıza en uygun bilgisayar arama seçeneğini belirleyin ve ardından **İleri**' yi seçin: 

   ![Envanter ve değerlendirme sihirbazının bulma yöntemleri sayfasını gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Araştırmak istediğiniz sistemler için kimlik bilgilerini girin veya yeni kimlik bilgileri oluşturun ve ardından **İleri**' yi seçin:

    ![Envanter ve değerlendirme sihirbazının tüm bilgisayarlar kimlik bilgileri sayfasını gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Kimlik bilgilerinin sırasını ayarlayın ve ardından **İleri**' yi seçin: 

   ![Envanter ve değerlendirme sihirbazının kimlik bilgileri sırası sayfasını gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Keşif yapmak istediğiniz her bilgisayar için kimlik bilgilerini girin. Her bilgisayar/makine için benzersiz kimlik bilgileri kullanabilir veya tüm bilgisayarlar kimlik bilgileri listesini kullanabilirsiniz.  

   ![Envanter ve değerlendirme sihirbazının bilgisayarları ve kimlik bilgilerini belirt sayfasını gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Seçimlerinizi doğrulayın ve sonra **son**' u seçin:

   ![Envanter ve değerlendirme sihirbazının Özet sayfasını gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. Tarama bittikten sonra **veri toplama** özetini görüntüleyin. Tarama, veritabanı sayısına bağlı olarak birkaç dakika sürebilir. İşiniz bittiğinde **Kapat** ' ı seçin: 

   ![Veri toplama özetini gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Oracle değerlendirmesi ve veritabanı ayrıntıları hakkında bir rapor oluşturmak için **Seçenekler** ' i seçin. Raporu oluşturmak için her iki seçeneği de birer birer seçin.


### <a name="assess"></a>Değerlendirme

Veri kaynaklarını tanımladıktan sonra, Oracle örneklerinin SQL Server VM geçişini değerlendirmek için [Oracle için SQL Server Geçiş Yardımcısı](https://www.microsoft.com/en-us/download/details.aspx?id=54258) kullanın. Yardımcı, kaynak ve hedef veritabanları arasındaki boşlukları anlamanıza yardımcı olur. Veritabanı nesnelerini ve verileri gözden geçirebilir, geçiş için veritabanlarını değerlendirebilir, veritabanı nesnelerini SQL Server geçirebilir ve sonra verileri SQL Server geçirebilirsiniz.

Bir değerlendirme oluşturmak için aşağıdaki adımları izleyin: 


1. [Oracle için SQL Server Geçiş Yardımcısı](https://www.microsoft.com/en-us/download/details.aspx?id=54258)açın. 
1. **Dosya** menüsünde **Yeni proje**' yi seçin. 
1. Projeniz için bir proje adı ve konum sağlayın ve sonra listeden bir SQL Server geçiş hedefi seçin. **Tamam ' ı** seçin: 

   ![Yeni proje iletişim kutusunu gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. **Oracle 'A Bağlan**' ı seçin. Oracle **'A Bağlan** Iletişim kutusunda Oracle bağlantısı için değerler girin:

   ![Oracle 'a Bağlan iletişim kutusunu gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Geçirmek istediğiniz Oracle şemalarını seçin: 

   ![Geçirilebileceğini Oracle şemaları listesini gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. **Oracle meta veri Gezgini**' nde, geçirmek istediğiniz Oracle şemasına sağ tıklayın ve ardından **rapor oluştur**' u seçin. Bunun yapılması, bir HTML raporu oluşturur. Alternatif olarak, veritabanını seçip üstteki menüden **rapor oluştur** ' u seçebilirsiniz.

   ![Raporun nasıl oluşturulacağını gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Dönüştürme istatistikleri, hatalar ve uyarılar için HTML raporunu gözden geçirin. Dönüştürme sorunlarını ve çözümlerini anlamak için bunu çözümleyin.

    Ayrıca, Oracle nesnelerinin envanterini almak için raporu Excel 'de açabilir ve şema dönüştürmelerini tamamlamaya yönelik çabadan yararlanın. Rapor için varsayılan konum SSMAProjects içindeki rapor klasörüdür. 

   Örnek: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Bir dönüştürme raporu gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Veri türlerini doğrula

Varsayılan veri türü eşlemelerini doğrulayın ve gerekirse gereksinimlere göre değiştirin. Bunu yapmak için aşağıdaki adımları izleyin: 


1. **Araçlar** menüsünde **proje ayarları**' nı seçin. 
1. **Tür eşlemeleri** sekmesini seçin. 

   ![Tür eşlemeleri sekmesini gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. **Oracle meta veri Gezgini**' nde tabloyu seçerek her tablo için tür eşlemesini değiştirebilirsiniz. 

### <a name="convert-the-schema"></a>Şemayı Dönüştür

Şemayı dönüştürmek için aşağıdaki adımları izleyin: 

1. Seçim Dinamik veya geçici sorguları dönüştürmek için, düğümüne sağ tıklayın ve **Ekstre Ekle**' yi seçin.

1. Üst menüdeki **SQL Server Bağlan** ' ı seçin. 
     1. Azure VM 'de SQL Server bağlantı ayrıntılarını girin. 
     1. Listeden hedef veritabanınızı seçin veya yeni bir ad sağlayın. Yeni bir ad sağlarsanız, hedef sunucuda bir veritabanı oluşturulur. 
     1. Kimlik doğrulama ayrıntılarını sağlayın. 
     1. **Bağlan**’ı seçin. 


   ![SQL Server nasıl bağlanılacağını gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. **Oracle meta veri Gezgini** ' nde Oracle şemasına sağ tıklayıp **Şemayı Dönüştür**' ü seçin. Alternatif olarak, üst menüdeki **Şemayı Dönüştür** ' i de seçebilirsiniz:

   ![Şemanın nasıl dönüştürüleceğini gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. Şema dönüştürme işlemi tamamlandıktan sonra, dönüştürülen nesneleri gözden geçirin ve olası sorunları belirlemek için bunları özgün nesnelerle karşılaştırın. Her türlü sorunu gidermek için önerileri kullanın:

   ![İki şemaların karşılaştırmasını gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Dönüştürülmüş Transact-SQL metnini özgün Saklı yordamlarla karşılaştırın ve önerileri gözden geçirin: 

   ![Transact-SQL, saklı yordamlar ve bir uyarı gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Çevrimdışı şema düzeltme alıştırması için projeyi yerel olarak kaydedebilirsiniz. Bunu yapmak için **Dosya** menüsünde **projeyi kaydet** ' i seçin. Projeyi yerel olarak kaydetmek, kaynak ve hedef şemaları çevrimdışı olarak değerlendirmenizi ve şemayı SQL Server için yayımlamadan önce düzeltmeyi gerçekleştirmenizi sağlar.

1. **Çıkış** bölmesinde **sonuçları gözden geçir** ' i seçin ve ardından **hata listesi** bölmesindeki hataları gözden geçirin. 
1. Çevrimdışı şema düzeltme alıştırması için projeyi yerel olarak kaydedin. **Dosya** menüsünde **projeyi kaydet** ' i seçin. Bu, Azure sanal makinelerinde SQL Server için şemayı yayımlamadan önce, kaynak ve hedef şemaları çevrimdışı olarak değerlendirmek ve düzeltme gerçekleştirmek için bir fırsat sağlar.


## <a name="migrate"></a>Geçiş

Gerekli önkoşullara sahip olduktan ve geçiş öncesi aşamalandırmayla ilişkili görevleri tamamladıktan sonra şema ve veri geçişini başlatmaya hazırsınız demektir. Geçiş iki adımdan oluşur: şemayı yayımlama ve verileri geçirme. 


Şemanızı yayımlamak ve verileri geçirmek için şu adımları izleyin: 

1. Şemayı yayımlama: **SQL Server meta veri Gezgini** ' nde veritabanına sağ tıklayın ve **veritabanıyla Synchronize**' ı seçin. Bunun yapılması, Azure sanal makinelerinde SQL Server için Oracle şemasını yayımlar. 

   ![Veritabanıyla Synchronize komutunu gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Kaynak projeniz ve Hedefinizdeki eşlemeyi gözden geçirin:

   ![Eşitleme durumunu gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. Verileri geçirme: **Oracle meta veri Gezgini** 'nde geçirmek istediğiniz veritabanına veya nesneye sağ tıklayın ve **veri geçişi**' ni seçin. Alternatif olarak, üst menüdeki **verileri geçir** ' i de seçebilirsiniz.

   Tüm bir veritabanının verilerini geçirmek için veritabanı adının yanındaki onay kutusunu işaretleyin. Ayrı tablolardan verileri geçirmek için veritabanını genişletin, **Tablolar**' ı genişletin ve ardından tablonun yanındaki onay kutusunu işaretleyin. Ayrı tablolardaki verileri atlamak için onay kutularını temizleyin.

   ![Veri geçirme komutunu gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. İletişim kutusundaki Azure sanal makinelerinde Oracle ve SQL Server için bağlantı ayrıntılarını girin.
1. Geçiş bittikten sonra **veri geçiş raporunu** görüntüleyin:

    ![Veri geçiş raporunu gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)kullanarak SQL Server Azure sanal makineler örneğine bağlanın. Verileri ve şemayı inceleyerek geçişi doğrulayın:


   ![SSMA 'da SQL Server örneği gösteren ekran görüntüsü.](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

SSMA 'yı kullanmak yerine, verileri geçirmek için SQL Server Integration Services (SSIS) kullanabilirsiniz. Daha fazla bilgi edinmek için şu makalelere bakın: 
- Makale [SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- Teknik İnceleme, [Azure ve karma veri hareketi için](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).


## <a name="post-migration"></a>Geçiş sonrası 

Geçiş aşamasını tamamladıktan sonra, her şeyin olabildiğince sorunsuz ve etkili bir şekilde çalıştığından emin olmak için geçiş sonrası görevlerinin bir serisini doldurmanız gerekir.

### <a name="remediate-applications"></a>Uygulamaları düzelt

Veriler hedef ortama geçirildikten sonra, kaynağı daha önce tüketen tüm uygulamaların hedefi tüketmeye başlaması gerekir. Bu değişikliklerin yapılması uygulamalarda değişiklik yapılmasını gerektirebilir.

[Veri erişimi geçiş araç seti](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) , Visual Studio Code için bir uzantıdır. Java kaynak kodunuzu analiz etmenize ve veri erişimi API 'SI çağrılarını ve sorgularını tespit etmenizi sağlar. Araç seti, yeni veritabanı arka ucu desteklemek için nelerin kullanılması gerektiğine ilişkin tek bölmeli bir görünüm sağlar. Daha fazla bilgi için bkz. [Java uygulamanızı Oracle 'Dan geçirme](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 

### <a name="perform-tests"></a>Testleri gerçekleştirme

Veritabanı geçişinizi test etmek için Şu etkinlikleri doldurun:

1. **Doğrulama testlerini geliştirin**. Veritabanı geçişini test etmek için SQL sorguları kullanmanız gerekir. Hem kaynak hem de hedef veritabanlarında çalıştırılacak doğrulama sorguları oluşturun. Doğrulama sorgularınız tanımladığınız kapsamı kapsamalıdır.

2. **Bir sınama ortamı ayarlayın**. Test ortamı, kaynak veritabanının ve hedef veritabanının bir kopyasını içermelidir. Test ortamını yalıtdığınızdan emin olun.

3. **Doğrulama testlerini çalıştırın**. Doğrulama testlerini kaynak ve hedefe göre çalıştırın ve sonra sonuçları çözümleyin.

4. **Performans testlerini çalıştırın**. Kaynak ve hedefte performans testi çalıştırın ve ardından sonuçları çözümleyip karşılaştırın.

### <a name="optimize"></a>İyileştirme

Geçiş sonrası aşamaları, veri doğruluğu sorunlarını mutabık kılma ve tamamlanmanın doğrulanması açısından önemlidir. Ayrıca iş yüküyle ilgili performans sorunlarını çözmek için de önemlidir.

> [!Note]
> Bu sorunlar ve bu sorunları azaltmaya yönelik belirli adımlar hakkında daha fazla bilgi için [geçiş sonrası doğrulama ve iyileştirme Kılavuzu](/sql/relational-databases/post-migration-validation-and-optimization-guide)' na bakın.


## <a name="migration-resources"></a>Geçiş kaynakları 

Bu geçiş senaryosunu tamamlamaya yönelik daha fazla yardım için, gerçek dünyada geçiş projesini desteklemek üzere geliştirilen aşağıdaki kaynaklara bakın.

| **Başlık/bağlantı**                                                                                                                                          | **Açıklama**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Veri Iş yükü değerlendirmesi modeli ve aracı](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Bu araç, belirli bir iş yükü için önerilen en uygun hedef platformları, bulut hazırlığı ve uygulama/veritabanı düzeltme düzeylerini sağlar. Otomatikleştirilmiş ve Tekdüzen hedef platform karar süreci sağlayarak büyük Emlak değerlendirmelerine yardımcı olan basit bir tıklama hesaplama ve rapor oluşturma olanağı sunar.                                                          |
| [Oracle envanter betiği yapıtları](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Bu varlık Oracle sistem tablolarını hedefleyen bir PL/SQL sorgusu içerir ve şema türüne, nesne türüne ve duruma göre nesne sayısını sağlar. Ayrıca, her şemada ham verilerin kabaca bir tahminini ve her şemadaki tabloların, bir CSV biçiminde depolanmış sonuçlarla birlikte boyutlandırılmasını sağlar.                                                                                                               |
| [SSMA Oracle değerlendirmesi toplama & birleştirme işlemini otomatikleştirin](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Bu kaynak kümesi, konsol modunda bir SSMA değerlendirmesi çalıştırmanız gereken XML dosyalarını oluşturmak için bir. csv dosyasını girdi olarak (proje klasörlerinde sources.csv) kullanır. Mevcut Oracle örneklerinin envanterini alarak source.csv dosyasını sağlarsınız. Çıktı dosyaları AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml ve VariableValueFile.xml.|
| [Oracle veritabanlarını geçirirken SıMMA sorunları ve olası düzeltmeler](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle ile WHERE yan tümcesinde skalar olmayan bir koşul atayabilirsiniz. SQL Server bu tür bir koşulu desteklemez. Bu nedenle, Oracle için SıMMA, WHERE yan tümcesinde skalar olmayan bir koşula sahip sorguları dönüştürmez. Bunun yerine, bir hata oluşturur: O2SS0001. Bu Teknik İnceleme, sorun ve çözümü çözme yolları hakkında ayrıntılı bilgi sağlar.          |
| [SQL Server geçiş el kitabı için Oracle](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Bu belge, bir Oracle şemasını SQL Server en son sürümüne geçirme ile ilişkili görevlere odaklanır. Geçiş, özelliklerde/işlevlerde değişiklik gerektiriyorsa, her bir değişikliğin veritabanını kullanan uygulamalarda olası etkisini dikkatlice düşünmeniz gerekir.                                                     |


Veri SQL Mühendisliği ekibi bu kaynakları geliştirdik. Bu takımın temel kurucu, veri platformu geçiş projeleri için Microsoft Azure veri platformuna yönelik karmaşık modernleştirmeyi engellemeyi ve hızlandırmanızı sağlar.


## <a name="next-steps"></a>Sonraki adımlar

- SQL Server için geçerli hizmetlerin kullanılabilirliğini denetlemek için bkz. [Azure genel altyapı merkezi](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- Çeşitli veritabanı ve veri geçişi senaryolarında ve özel görevlerde size yardımcı olabilecek Microsoft ve üçüncü taraf hizmet ve araçların bir matrisi için bkz. [veri geçişi Için hizmetler ve araçlar](../../../dms/dms-tools-matrix.md).

- Azure SQL hakkında daha fazla bilgi için bkz.
   - [Dağıtım seçenekleri](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure Sanal Makineler'de SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure toplam sahiplik Hesaplayıcı maliyeti](https://azure.microsoft.com/pricing/tco/calculator/)


- Bulut geçişleri için çerçeve ve benimseme çevrimi hakkında daha fazla bilgi edinmek için bkz.:
   -  [Azure için Bulut Benimseme Çerçevesi](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure 'a geçirilen iş yükleri için maliyet ve boyut için en iyi uygulamalar](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Lisanslama hakkında daha fazla bilgi için bkz.:
   - [Azure Hibrit Avantajı kendi lisansınızı getirin](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 ve SQL Server 2008 R2 için ücretsiz genişletilmiş destek alın](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Uygulama erişim katmanını değerlendirmek için [veri erişimi geçiş araç seti önizlemesi](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)' ni kullanın.
- Veri erişim katmanı A/B testi yapma hakkında daha fazla bilgi için bkz. [veritabanı yükseltme deneyimi Yardımcısı genel bakış](/sql/dea/database-experimentation-assistant-overview).



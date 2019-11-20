---
title: Birden çok veritabanı genelinde raporlama sorguları
description: Dağıtılmış sorgular kullanan çapraz kiracı raporlaması.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
ms.date: 01/25/2019
ms.openlocfilehash: c863946934df9990c14e49ef1a0a82bbc55b27c6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822074"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Dağıtılmış sorguları kullanarak çapraz kiracı raporlaması

Bu öğreticide, raporlama için tüm Kiracı veritabanı kümesi genelinde dağıtılmış sorgular çalıştırırsınız. Bu sorgular, Wingtip bilet SaaS kiracılarının günlük işletimsel verilerinde bulunan öngörüleri ayıklayabilir. Bunu yapmak için, katalog sunucusuna ek bir raporlama veritabanı dağıtır ve dağıtılmış sorguları etkinleştirmek için elastik sorgu kullanırsınız.


Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> 
> * Raporlama veritabanını dağıtma
> * Tüm kiracı veritabanlarında dağıtılmış sorgular çalıştırma
> * Her bir veritabanındaki genel görünümlerin kiracılar genelinde verimli bir şekilde sorgulanmasını nasıl etkinleştirebilirim?


Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:


* Kiracı uygulaması başına Wingtip bilet SaaS veritabanı dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıtma ve araştırma](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell’i kullanmaya başlama](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) yüklenir. SSMS 'yi indirip yüklemek için bkz. [download SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Çapraz kiracı raporlama kalıbı

![çapraz kiracı dağıtılmış sorgu kalıbı](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

SaaS uygulamalarına sahip bir fırsat, uygulamanızın işlem ve kullanımıyla ilgili Öngörüler elde etmek için bulutta depolanan büyük miktarda kiracı verisi kullanmaktır. Bu Öngörüler, uygulama ve hizmetinizdeki Özellik geliştirmeyi, kullanılabilirlik geliştirmelerini ve diğer yatırımları yönlendirebilir.

Bu verilere tek bir çok kiracılı veritabanında erişim kolaydır, ancak binlerce veritabanına ölçekli olarak dağıtıldığında çok kolay değildir. Bir yaklaşım, ortak şemaya sahip dağıtılmış bir veritabanı kümesi genelinde sorgulama sağlayan [elastik sorgu](sql-database-elastic-query-overview.md)kullanmaktır. Bu veritabanları farklı kaynak grupları ve aboneliklerine dağıtılabilir, ancak ortak bir oturum açma paylaşılması gerekir. Elastik sorgu, dağıtılmış (kiracı) veritabanlarındaki tabloları veya görünümleri yansıtan dış tabloların tanımlandığı tek bir *baş* veritabanı kullanır. Bu baş veritabanına gönderilen sorgular, gerektiğinde kiracı veritabanlarına gönderilen sorgu kısımlarıyla birlikte dağıtılmış bir sorgu planı oluşturmak üzere derlenir. Elastik sorgu, tüm kiracı veritabanlarının konumunu öğrenmek için katalog veritabanındaki parça haritasını kullanır. Baş veritabanının kurulumu ve sorgusu, standart [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)' i kullanarak basittir ve Power BI ve Excel gibi araçlardan sorgu yapmayı destekler.

Esnek sorgu, kiracı veritabanlarına sorgu dağıtarak canlı üretim verileri hakkında anında öngörüler sağlar. Elastik sorgu olabilecek çok sayıda veritabanından veri çeker, ancak sorgu gecikmesi tek bir çok kiracılı veritabanına gönderilen eşdeğer sorgulardan daha yüksek olabilir. Baş veritabanına döndürülen verileri en aza indirmek için sorgular tasarlayın. Esnek sorgu genellikle sık kullanılan veya karmaşık analiz sorguları veya raporları oluşturma aksine, küçük miktarlarda gerçek zamanlı verileri sorgulamak için idealdir. Sorgular iyi gerçekleştirmezse, sorgunun hangi kısmının uzak veritabanına itiltiğini ve ne kadar veri döndürüldüğünü görmek için [yürütme planına](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) bakın. Karmaşık toplama veya analitik işlem gerektiren sorgular, kiracı verileri bir veritabanına veya analiz sorguları için iyileştirilmiş veri ambarına ayıklanarak daha iyi idare edebilir. Bu model, [kiracı Analizi öğreticisinde](saas-tenancy-tenant-analytics.md)açıklanmıştır. 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Kiracı uygulama betikleri başına Wingtip bilet SaaS veritabanını alın

Wingtip biletleri SaaS çok kiracılı veritabanı betikleri ve uygulama kaynak kodu [Wingtipbilet ssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub deposunda mevcuttur. Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.

## <a name="create-ticket-sales-data"></a>Bilet satış verileri oluşturma

Sorguları daha ilgi çekici bir veri kümesine karşı çalıştırmak için, Bilet oluşturma ' yı çalıştırarak bilet satış verileri oluşturun.

1. *PowerShell ISE*'de,...\\öğrenme modüllerini\\işletimsel analiz\\geçici raporlama\\*demo-AdhocReporting. ps1* komut dosyasını açın ve aşağıdaki değeri ayarlayın:
   * **$DemoScenario** = 1, **Tüm havalandırma olayları Için bilet satın alın**.
2. Betiği çalıştırmak ve bilet satışları oluşturmak için **F5** tuşuna basın. Betik çalışırken bu öğreticideki adımlara devam edin. Bilet verileri, *geçici olarak dağıtılan sorguları Çalıştır* bölümünde sorgulanır, bu nedenle bilet oluşturucunun tamamlanmasını bekleyin.

## <a name="explore-the-global-views"></a>Genel görünümleri keşfet

Her kiracının kiracı uygulaması başına Wingtip bilet SaaS veritabanında bir veritabanı verilir. Bu nedenle, veritabanı tablolarında bulunan veriler tek bir kiracının perspektifine göre kapsamlandırılır. Ancak, tüm veritabanlarına sorgulama yaparken, elastik sorgunun verileri kiracı tarafından oluşturulmuş tek bir mantıksal veritabanının parçası gibi işleyemesinin önemli olması önemlidir. 

Bu düzenin benzetimini yapmak için, genel olarak sorgulanan her bir tabloya kiracı KIMLIĞI oluşturan kiracı veritabanına bir ' genel ' görünüm kümesi eklenir. Örneğin, *venueevents* görünümü, *Olaylar* tablosundan yansıtılan sütunlara bir hesaplanan *venueıd* ekler. Benzer şekilde, *Venuebilet satın alımlarını* ve *venuebilet* görünümleri, ilgili tablolarından yansıtılan bir hesaplanan *venueıd* sütunu ekler. Bu görünümler, elastik sorgu tarafından paralel hale getirmek sorguları için kullanılır ve bir *Venueıd* sütunu bulunduğunda bunları uygun uzak kiracı veritabanına gönderir. Bu, döndürülen veri miktarını önemli ölçüde azaltır ve birçok sorgu için performans açısından önemli ölçüde artışa neden olur. Bu genel görünümler, tüm kiracı veritabanlarında önceden oluşturulmuştur.

1. SSMS 'yi açın ve [tenants1-&lt;USER&gt; sunucusuna bağlanın](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. **Veritabanları**' nı genişletin, _contosoconcerthall_' a sağ tıklayın ve **Yeni sorgu**' yı seçin.
1. Tek kiracılı tablolar ve genel görünümler arasındaki farkı araştırmak için aşağıdaki sorguları çalıştırın:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Bu görünümlerde, *Venueıd* , mekan adının karması olarak hesaplanır, ancak benzersiz bir değer tanıtmak için herhangi bir yaklaşım kullanılabilir. Bu yaklaşım, kiracı anahtarının katalogda kullanım açısından hesaplandığı yönteme benzer.

*Havalandırma* görünümü tanımını incelemek için:

1. **Nesne Gezgini**' de, **contosoconcerthall** > **Görünümler**' i genişletin:

   ![Görünümler](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Dbo öğesine sağ tıklayın **. Havalandırma**.
3. **Yeni sorgu Düzenleyicisi penceresi** >  > **Oluştur** **olarak betik görünümü** seçin

Diğer *mekan* görünümlerini, *venueıd*'nin nasıl ekleneceğini görmek için betiği yapın.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Dağıtılmış sorgular için kullanılan veritabanını dağıtma

Bu alıştırma, _adhocretaşıma_ veritabanını dağıtır. Bu, tüm kiracı veritabanlarında sorgulamak için kullanılan şemayı içeren baş veritabanıdır. Veritabanı, örnek uygulamadaki tüm yönetim ile ilgili veritabanları için kullanılan sunucu olan var olan katalog sunucusuna dağıtılır.

1. *PowerShell ISE*'de,...\\öğrenme modüllerini\\işletimsel analiz\\geçici raporlama\\*demo-AdhocReporting. ps1*. 

1. **$DemoScenario = 2**olarak ayarlayın, _geçici raporlama veritabanını dağıtın_.

1. Betiği çalıştırmak ve *adhocretaşıma* veritabanını oluşturmak için **F5** ' e basın.

Sonraki bölümde, dağıtılmış sorguları çalıştırmak için kullanılabilmesi üzere veritabanına şema eklersiniz.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Dağıtılmış sorguları çalıştırmak için ' Head ' veritabanını yapılandırın

Bu alıştırma, tüm kiracı veritabanlarında sorgu sağlamak için, şemayı (dış veri kaynağı ve dış tablo tanımları) _adhocretaşıma_ veritabanına ekler.

1. SQL Server Management Studio açın ve önceki adımda oluşturduğunuz geçici raporlama veritabanına bağlanın. Veritabanının adı *adhocretaşıma*' dır.
2. SSMS 'de. ..\Learning Modules\işletimsel Analtics\geçici raporlama \ _Initialize-AdhocReportingDB. SQL_ dosyasını açın.
3. SQL betiğini gözden geçirin ve aklınızda:

   Elastik sorgu, kiracı veritabanlarının her birine erişmek için veritabanı kapsamlı kimlik bilgilerini kullanır. Bu kimlik bilgisinin tüm veritabanlarında kullanılabilir olması gerekir ve normalde bu sorguları etkinleştirmek için gereken en düşük haklara sahip olmalıdır.

    ![kimlik bilgisi oluşturma](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Dış veri kaynağı olarak Katalog veritabanı ile, sorgular sorgu çalıştığı sırada katalogda kayıtlı tüm veritabanlarına dağıtılır. Sunucu adları her dağıtım için farklı olduğundan, bu betik geçerli sunucudan (@@servername), komut dosyasının yürütüldüğü Katalog veritabanının konumunu alır.

    ![dış veri kaynağı oluştur](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Önceki bölümde açıklanan genel görünümlere başvuran ve **DISTRIBUTION = SHARDED (Venueıd)** ile tanımlanan dış tablolar. Her *Venueıd* tek bir veritabanıyla eşlentiğinden, bu, sonraki bölümde gösterildiği gibi birçok senaryo için performansı geliştirir.

    ![Dış tablolar oluşturma](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Oluşturulan ve doldurulan yerel tablo _Venuetype_ . Bu başvuru verileri tablosu, tüm kiracı veritabanlarında ortaktır, bu nedenle burada yerel bir tablo olarak temsil edilebilir ve ortak verilerle doldurulabilir. Bazı sorgular için, bu tablonun baş veritabanında tanımlanması, baş veritabanına taşınması gereken veri miktarını azaltabilir.

    ![tablo oluştur](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Başvuru tablolarını bu şekilde eklerseniz, kiracı veritabanlarını her güncelleştirdiğinizde tablo şemasını ve verileri güncelleştirdiğinizden emin olun.

4. Betiği çalıştırmak için **F5** tuşuna basın ve *adhocretaşıma* veritabanını başlatın. 

Artık dağıtılmış sorguları çalıştırabilir ve tüm kiracılarda Öngörüler toplayabilirsiniz!

## <a name="run-distributed-queries"></a>Dağıtılmış sorguları Çalıştır

Artık *adhocretaşıma* veritabanı ayarlanmış olduğuna göre, devam edin ve bazı dağıtılmış sorguları çalıştırın. Sorgu işlemenin nerede olduğunu daha iyi anlamak için yürütme planını dahil edin. 

Yürütme planı incelenirken Ayrıntılar için plan simgelerinin üzerine gelin. 

Önemli bir deyişle, dış veri kaynağı tanımlandığında **dağıtım = parçalara ayrılmış (Venueıd)** ayarı çok sayıda senaryonun performansını geliştirir. Her *Venueıd* tek bir veritabanıyla eşleniyorsa, filtreleme kolayca uzaktan yapılır ve yalnızca gerekli verileri döndürür.

1. Açık...\\öğrenme modüllerini\\Işletimsel analizler\\SSMS 'de geçici raporlama\\*demo-AdhocReportingQueries. SQL* .
2. **Adhocretaşıma** veritabanına bağlı olduğunuzdan emin olun.
3. **Sorgu** menüsünü seçin ve **gerçek yürütme planını dahil et** ' e tıklayın.
4. *Şu anda kayıtlı olan havalandırma noktaları vurgulansın mı?* sorgusu yapın ve **F5**'e basın.

   Sorgu tüm kiracılar listesini döndürür, bu, hızlı ve tüm kiracılarda sorgu yapmak ve her kiracıdan veri döndürmek için ne kadar kolay olduğunu gösterir.

   Planı inceleyin ve tüm maliyetin uzak sorguda olduğunu görün. Her kiracı veritabanı sorguyu uzaktan yürütür ve kendi mekan bilgilerini baş veritabanına döndürür.

   ![Dbo 'DAN * öğesini SEÇIN. Mekanlardaki](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Sonraki sorguyu seçin ve **F5**tuşuna basın.

   Bu sorgu, kiracı veritabanlarından ve yerel *Venuetype* tablosundan (yerel olarak, *adhocretıı* veritabanındaki bir tablo olarak) verileri birleştirir.

   Planı inceleyin ve maliyetin çoğunluğunun uzak sorgu olduğunu görün. Her kiracı veritabanı, kendi mekan bilgilerini döndürür ve kolay adı göstermek için yerel *Venuetype* tablosuyla yerel bir JOIN uygular.

   ![Uzak ve yerel verilere ekleyin](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Şimdi *hangi günün en çok bilet satılmış?* sorgusunu seçin ve **F5**tuşuna basın.

   Bu sorgu biraz daha karmaşık katılım ve toplama yapar. İşlemin büyük bir bölümünü uzaktan meydana gelir.  Baş veritabanına her bir mekanın günlük bilet satışı sayısını içeren tek satırlar.

   ![sorgu](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunları öğrendiniz:

> [!div class="checklist"]
> 
> * Tüm kiracı veritabanlarında dağıtılmış sorguları çalıştırma
> * Bir raporlama veritabanı dağıtın ve dağıtılmış sorguları çalıştırmak için gereken şemayı tanımlayın.


Artık daha karmaşık analiz işlemleri için verileri ayrı bir analiz veritabanına ayıklamayı araştırmak üzere [kiracı Analizi öğreticisini](saas-tenancy-tenant-analytics.md) deneyin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kiracı uygulaması başına Wingtip bilet SaaS veritabanı üzerine inşa eden ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Esnek Sorgu](sql-database-elastic-query-overview.md)

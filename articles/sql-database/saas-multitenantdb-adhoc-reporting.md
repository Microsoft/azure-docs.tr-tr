---
title: Birden çok veritabanı genelinde geçici raporlama sorguları
description: Çok kiracılı bir uygulama örneğinde birden çok SQL veritabanı arasında geçici raporlama sorguları çalıştırın.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 10/30/2018
ms.openlocfilehash: c0d1829c52041446b4feb43d8af262265e2680fc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822182"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Birden çok Azure SQL veritabanı arasında geçici analiz sorguları çalıştırma

Bu öğreticide, geçici etkileşimli Raporlamayı etkinleştirmek için tüm Kiracı veritabanı kümesi genelinde dağıtılmış sorgular çalıştırırsınız. Bu sorgular, Wingtip bilet SaaS uygulamasının günlük işletimsel verilerinde bulunan öngörüleri ayıklayabilir. Bu dışlamaları yapmak için, katalog sunucusuna ek bir analiz veritabanı dağıtır ve dağıtılmış sorguları etkinleştirmek için elastik sorgu kullanırsınız.


Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> 
> * Geçici raporlama veritabanını dağıtma
> * Tüm kiracı veritabanlarında dağıtılmış sorgular çalıştırma


Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

* Wingtip bilet SaaS çok kiracılı veritabanı uygulaması dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. Wingtip biletleri SaaS çok kiracılı veritabanı uygulaması dağıtma ve araştırma](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell’i kullanmaya başlama](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) yüklenir. SSMS 'yi indirip yüklemek için bkz. [download SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Geçici raporlama deseninin

![geçici raporlama kalıbı](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS uygulamaları, kuruluşunuzda merkezi olarak depolanan büyük miktarda kiracı verilerini analiz edebilir. Çözümlemeler, uygulamanızın işleme ve kullanımına ilişkin öngörüleri açığa çıkarır. Bu Öngörüler, uygulama ve hizmetinizdeki Özellik geliştirmeyi, kullanılabilirlik geliştirmelerini ve diğer yatırımları yönlendirebilir.

Bu verilere tek bir çok kiracılı veritabanında erişim kolaydır, ancak binlerce veritabanına ölçekli olarak dağıtıldığında çok kolay değildir. Bir yaklaşım, ortak şemaya sahip dağıtılmış bir veritabanı kümesi genelinde sorgulama sağlayan [elastik sorgu](sql-database-elastic-query-overview.md)kullanmaktır. Bu veritabanları, farklı kaynak grupları ve abonelikler arasında dağıtılabilir. Ancak, bir ortak oturum açmanın tüm veritabanlarından veri ayıklamak için erişimi olmalıdır. Elastik sorgu, dağıtılmış (kiracı) veritabanlarındaki tabloları veya görünümleri yansıtan dış tabloların tanımlandığı tek bir *baş* veritabanı kullanır. Bu baş veritabanına gönderilen sorgular, gerektiğinde kiracı veritabanlarına gönderilen sorgu kısımlarıyla birlikte dağıtılmış bir sorgu planı oluşturmak üzere derlenir. Elastik sorgu, tüm kiracı veritabanlarının konumunu öğrenmek için katalog veritabanındaki parça haritasını kullanır. Setup ve Query standart [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)' i kullanarak basittir ve Power BI ve Excel gibi araçlardan geçici sorgulamayı destekler.

Esnek sorgu, kiracı veritabanlarına sorgu dağıtarak canlı üretim verileri hakkında anında öngörüler sağlar. Ancak, elastik sorgu potansiyel olarak çok sayıda veritabanından veri çeker, ancak sorgu gecikmesi bazen tek bir çok kiracılı veritabanına gönderilen eşdeğer sorgulardan daha yüksek olabilir. Döndürülen verileri en aza indirmek için sorguları tasarlamadığınızdan emin olun. Esnek sorgu genellikle sık kullanılan veya karmaşık analiz sorguları veya raporları oluşturma aksine, küçük miktarlarda gerçek zamanlı verileri sorgulamak için idealdir. Sorgular iyi gerçekleştirmiyor ise, sorgunun hangi kısmının uzak veritabanına itiltiğini görmek için [yürütme planına](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) bakın. Ve ne kadar veri döndürülmekte olduğunu değerlendirin. Karmaşık analitik işlem gerektiren sorgular, ayıklanan kiracı verileri analiz sorguları için iyileştirilmiş bir veritabanına kaydederek daha iyi bir şekilde sunulabilir. SQL veritabanı ve SQL veri ambarı bu tür analiz veritabanını barındırabilir.

Analiz için bu model, [kiracı Analizi öğreticisinde](saas-multitenantdb-tenant-analytics.md)açıklanmıştır.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip bilet SaaS çok kiracılı veritabanı uygulaması kaynak kodu ve betikleri alın

Wingtip biletleri SaaS çok kiracılı veritabanı betikleri ve uygulama kaynak kodu [Wingtipbilet ssaas-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub deposunda mevcuttur. Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.

## <a name="create-ticket-sales-data"></a>Bilet satış verileri oluşturma

Sorguları daha ilgi çekici bir veri kümesine karşı çalıştırmak için, Bilet oluşturma ' yı çalıştırarak bilet satış verileri oluşturun.

1. *PowerShell ISE*'de,...\\öğrenme modüllerini\\işletimsel analiz\\geçici raporlama\\*demo-AdhocReporting. ps1* komut dosyasını açın ve aşağıdaki değerleri ayarlayın:
   * **$DemoScenario** = 1, **Tüm havalandırma olayları Için bilet satın alın**.
2. Betiği çalıştırmak ve bilet satışları oluşturmak için **F5** tuşuna basın. Betik çalışırken bu öğreticideki adımlara devam edin. Bilet verileri, *geçici olarak dağıtılan sorguları Çalıştır* bölümünde sorgulanır, bu nedenle bilet oluşturucunun tamamlanmasını bekleyin.

## <a name="explore-the-tenant-tables"></a>Kiracı tablolarını keşfet 

Wingtip bilet SaaS çok kiracılı veritabanı uygulamasında kiracılar, kiracı verilerinin çok kiracılı bir veritabanında veya tek bir kiracı veritabanında depolandığı ve ikisi arasında taşınabileceği karma kiracı yönetim modelinde depolanır. Tüm kiracı veritabanlarında sorgulama yaparken, elastik sorgunun verileri kiracıya göre tek bir mantıksal veritabanının parçası gibi işleyemesinin önemli olması önemlidir. 

Bu düzene ulaşmak için, tüm kiracı tablolarında verilerin hangi kiracıya ait olduğunu belirleyen bir *Venueıd* sütunu bulunur. *Venueıd* , mekan adının karması olarak hesaplanır, ancak bu sütun için benzersiz bir değer tanıtmak üzere herhangi bir yaklaşım kullanılabilir. Bu yaklaşım, kiracı anahtarının katalogda kullanım açısından hesaplandığı yönteme benzer. *Venueıd* içeren tablolar, elastik sorgu tarafından paralel hale getirmek sorguları ve uygun uzak kiracı veritabanına göndermek için kullanılır. Bu, döndürülen veri miktarını önemli ölçüde azaltır ve verileri tek bir kiracı veritabanında depolanan birden çok kiracı olduğunda performans artışı elde edilir.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Geçici dağıtılmış sorgular için kullanılan veritabanını dağıtma

Bu alıştırma, *adhocretaşıma* veritabanını dağıtır. Bu, tüm kiracı veritabanlarında sorgulamak için kullanılan şemayı içeren baş veritabanıdır. Veritabanı, örnek uygulamadaki tüm yönetim ile ilgili veritabanları için kullanılan sunucu olan var olan katalog sunucusuna dağıtılır.

1. Açık...\\öğrenme modüllerini\\Işletimsel analizler, *PowerShell ISE* 'de *Demo-AdhocReporting. ps1*\\geçici raporlama\\ve aşağıdaki değerleri ayarlar:
   * **$DemoScenario** = 2, geçici **analiz veritabanını dağıtın**.

2. Betiği çalıştırmak ve *adhocretaşıma* veritabanını oluşturmak için **F5** ' e basın.

Sonraki bölümde, dağıtılmış sorguları çalıştırmak için kullanılabilmesi üzere veritabanına şema eklersiniz.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Dağıtılmış sorguları çalıştırmak için ' Head ' veritabanını yapılandırın

Bu alıştırma, tüm kiracı veritabanlarında sorgulama sağlayan geçici raporlama veritabanına şema (dış veri kaynağı ve dış tablo tanımları) ekler.

1. SQL Server Management Studio açın ve önceki adımda oluşturduğunuz geçici raporlama veritabanına bağlanın. Veritabanının adı *adhocretaşıma*' dır.
2. SSMS 'de. ..\Learning Modules\işletimsel Analtics\geçici raporlama \ *Initialize-AdhocReportingDB. SQL* dosyasını açın.
3. SQL betiğini gözden geçirin ve aşağıdakileri göz önünde edin:

   Elastik sorgu, kiracı veritabanlarının her birine erişmek için veritabanı kapsamlı kimlik bilgilerini kullanır. Bu kimlik bilgisinin tüm veritabanlarında kullanılabilir olması gerekir ve normalde bu geçici sorguları etkinleştirmek için gereken en düşük haklara sahip olmalıdır.

    ![kimlik bilgisi oluşturma](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Katalog veritabanını dış veri kaynağı olarak kullanarak, sorgu çalıştırıldığında katalogda kayıtlı tüm veritabanlarına sorgular dağıtılır. Her dağıtım için sunucu adları farklı olduğundan, bu başlatma betiği, komut dosyasının yürütüldüğü geçerli sunucuyu (@@servername) alarak Katalog veritabanının konumunu alır.

    ![dış veri kaynağı oluştur](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Kiracı tablolarına başvuran dış tablolar, **DISTRIBUTION = SHARDED (Venueıd)** ile tanımlanır. Bu, belirli bir *Venueıd* için bir sorguyu uygun veritabanına yönlendirir ve sonraki bölümde gösterildiği gibi birçok senaryo için performansı geliştirir.

    ![Dış tablolar oluşturma](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Oluşturulan ve doldurulan yerel tablo *Venuetype* . Bu başvuru verileri tablosu, tüm kiracı veritabanlarında ortaktır, bu nedenle burada yerel bir tablo olarak temsil edilebilir ve ortak verilerle doldurulabilir. Bazı sorgular için, bu, kiracı veritabanları ve *adhocretaşıma* veritabanı arasında taşınan veri miktarını azaltabilir.

    ![tablo oluştur](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Başvuru tablolarını bu şekilde eklerseniz, kiracı veritabanlarını her güncelleştirdiğinizde tablo şemasını ve verileri güncelleştirdiğinizden emin olun.

4. Betiği çalıştırmak için **F5** tuşuna basın ve *adhocretaşıma* veritabanını başlatın. 

Artık dağıtılmış sorguları çalıştırabilir ve tüm kiracılarda Öngörüler toplayabilirsiniz!

## <a name="run-ad-hoc-distributed-queries"></a>Geçici dağıtılmış sorguları çalıştırma

Artık *adhocretaşıma* veritabanı ayarlanmış olduğuna göre, devam edin ve bazı dağıtılmış sorguları çalıştırın. Sorgu işlemenin nerede olduğunu daha iyi anlamak için yürütme planını dahil edin. 

Yürütme planı incelenirken Ayrıntılar için plan simgelerinin üzerine gelin. 

1. *SSMS*'de,...\\öğrenme modüllerini\\işletimsel çözümlemeler\\geçici raporlama\\*demo-AdhocReportingQueries. SQL*.
2. **Adhocretaşıma** veritabanına bağlı olduğunuzdan emin olun.
3. **Sorgu** menüsünü seçin ve **gerçek yürütme planını dahil et** ' e tıklayın.
4. *Şu anda kayıtlı olan havalandırma noktaları vurgulansın mı?* sorgusu yapın ve **F5**'e basın.

   Sorgu tüm kiracılar genelinde sorgu yapmak ve her kiracıdan veri döndürmek için ne kadar hızlı ve kolay olduğunu gösteren tüm mekan listesini döndürür.

   Yalnızca her bir kiracı veritabanına gittiğinden ve mekan bilgilerini seçerken, planı inceleyin ve tüm maliyetin uzak sorgu olduğunu görün.

   ![Dbo 'DAN * öğesini SEÇIN. Mekanlardaki](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Sonraki sorguyu seçin ve **F5**tuşuna basın.

   Bu sorgu, kiracı veritabanlarından ve yerel *Venuetype* tablosundan (yerel olarak, *adhocretıı* veritabanındaki bir tablo olarak) verileri birleştirir.

   Her kiracının yer bilgilerini (dbo) sorgulıyoruz, planı inceleyin ve maliyetin çoğunluğunun uzak sorgu olduğunu görün. Havalandırma) ve ardından kolay adı göstermek için yerel *Venuetype* tablosuyla hızlı bir yerel katılmayı yapın.

   ![Uzak ve yerel verilere ekleyin](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Şimdi *hangi günün en çok bilet satılmış?* sorgusunu seçin ve **F5**tuşuna basın.

   Bu sorgu biraz daha karmaşık katılım ve toplama yapar. Dikkat edilmesi gereken önemli şeyler, işlemin büyük bir kısmının uzaktan yapıldığına ve bir kez daha sonra yalnızca ihtiyaç duyduğumuz satırları geri döndürmemiz, her bir mekanın günlük bilet satışı sayısı için yalnızca tek bir satır döndürüyor.

   ![sorgu](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunları öğrendiniz:

> [!div class="checklist"]
> 
> * Tüm kiracı veritabanlarında dağıtılmış sorguları çalıştırma
> * Dağıtılmış sorgular çalıştırmak için bir geçici raporlama veritabanı dağıtın ve buna şema ekleyin.

Artık daha karmaşık analiz işlemleri için verileri ayrı bir analiz veritabanına ayıklamayı araştırmak üzere [kiracı Analizi öğreticisini](saas-multitenantdb-tenant-analytics.md) deneyin.

## <a name="additional-resources"></a>Ek kaynaklar

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Esnek Sorgu](sql-database-elastic-query-overview.md)

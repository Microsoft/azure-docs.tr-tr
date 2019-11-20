---
title: İzleme ve performans ayarlama
description: Değerlendirme ve iyileştirme aracılığıyla Azure SQL veritabanı 'nda performans ayarlama ipuçları.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: e77af00dc3352af3265da90685e58b34c96bee81
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825151"
---
# <a name="monitoring-and-performance-tuning"></a>İzleme ve performans ayarlama

Azure SQL veritabanı, kullanımı kolayca izlemek, kaynak eklemek veya kaldırmak için kullanabileceğiniz araçlar ve yöntemler sağlar (CPU, bellek veya g/ç gibi), olası sorunları giderebilir ve bir veritabanının performansını geliştirmek için öneriler oluşturabilirsiniz. Azure SQL veritabanı 'ndaki özellikler, veritabanlarındaki sorunları otomatik olarak düzeltir. 

Otomatik ayarlama, bir veritabanının iş yüküne uyum sağlamasına ve performansı otomatik olarak iyileştirmesine olanak sağlar. Ancak bazı özel sorunlar sorun giderme gerektirebilir. Bu makalede, bazı en iyi yöntemler ve performans sorunlarını gidermek için kullanabileceğiniz bazı araçlar açıklanmaktadır.

Bir veritabanının sorunsuz çalıştığından emin olmak için şunları yapmanız gerekir:
- Veritabanına atanan kaynakların iş yükünü işleyebileceğini doğrulamak için [veritabanı performansını izleyin](#monitor-database-performance) . Veritabanı kaynak limitlerine ulaşarak şunları göz önünde bulundurun:
   - En üstteki kaynak kullanan sorguları tanımlama ve iyileştirme.
   - [Hizmet katmanını yükselterek](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)daha fazla kaynak ekleme.
- Olası bir sorunun neden oluştuğunu belirlemek ve sorunun asıl nedenini belirlemek için [performans sorunlarını giderin](#troubleshoot-performance-problems) . Kök nedenini tanımladıktan sonra, sorunu gidermek için gereken adımları uygulayın.

## <a name="monitor-database-performance"></a>Veritabanı performansını izleme

Azure 'da bir SQL veritabanının performansını izlemek için, seçtiğiniz veritabanı performansı düzeyine göre kullanılan kaynakları izleyerek başlayın. Aşağıdaki kaynakları izleyin:
 - **CPU kullanımı**: gelişmiş bir süre IÇIN veritabanının CPU kullanımının yüzde 100 ' una ulaşmasını denetleyin. Yüksek CPU kullanımı, en fazla işlem gücünü kullanan sorguları belirlemeniz ve ayarlamanız gerektiğini gösterebilir. Yüksek CPU kullanımı, veritabanının veya örneğin daha yüksek bir hizmet katmanına yükseltilmesi gerektiğini de gösterebilir. 
 - **Bekleme istatistikleri**: sorguların beklediği süreyi öğrenmek için [sys. dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) kullanın. Sorgular, kaynakların, kuyruğun beklediği veya dış bekleme için bekleniyor. 
 - **GÇ kullanımı**: veritabanının TEMELDEKI depolamanın GÇ sınırlarına ulaşmakta olup olmadığını denetleyin.
 - **Bellek kullanımı**: veritabanı veya örnek için kullanılabilir bellek miktarı, sanal çekirdek sayısıyla orantılıdır. Belleğin iş yükü için yeterli olduğundan emin olun. Sayfa ömrü erkeklerin sayfaların bellekten ne kadar hızlı bir şekilde kaldırıldığını gösterebilen parametrelerden biridir.

Azure SQL veritabanı hizmeti, olası performans sorunlarını gidermenize ve düzeltmenize yardımcı olacak araçlar ve kaynaklar içerir. [Performans ayarlama önerilerini](sql-database-advisor.md)inceleyerek, kaynakları değiştirmeden sorgu performansını iyileştirmek ve iyileştirmek için fırsatları tanımlayabilirsiniz. 

Veritabanı performansının düşük olmasına yol açan yaygın nedenler, dizinlerin eksik olması ve sorguların hatalı bir şekilde iyileştirilmesidir. İş yükünün performansını artırmak için ayarlama önerilerini uygulayabilirsiniz. Ayrıca, tüm tanımlanan önerileri uygulayarak Azure SQL veritabanı ['nın sorguların performansını otomatik olarak iyileştirmenize](sql-database-automatic-tuning.md) izin verebilirsiniz. Daha sonra önerilerin veritabanı performansını iyileştirdiğini doğrulayın.

> [!NOTE]
> Dizin oluşturma yalnızca tek veritabanı ve elastik havuzlarda kullanılabilir. Yönetilen bir örnekte dizin oluşturma kullanılamıyor.

Veritabanı performansını izlemek ve sorunlarını gidermek için aşağıdaki seçeneklerden birini belirleyin:

- [Azure Portal](https://portal.azure.com) **SQL veritabanları** ' nı seçin ve veritabanını seçin. **İzleme** grafiğinde, en yüksek kullanım özelliklerine yaklaştığı kaynaklar ' a bakın. DTU tüketimi varsayılan olarak gösterilir. Gösterilen zaman aralığını ve değerleri değiştirmek için **Düzenle** ' yi seçin.
- SQL Server Management Studio gibi araçlar, [performans panosu](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard)gibi birçok yararlı rapor sağlar. Kaynak kullanımını izlemek ve en üstteki kaynak kullanan sorguları belirlemek için bu raporları kullanın. Performansı gerileyen sorguları belirlemek için [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) ' da kullanabilirsiniz.
- [Azure Portal](https://portal.azure.com), en fazla kaynağı kullanan sorguları belirlemek için [sorgu performansı içgörüleri](sql-database-query-performance.md) kullanın. Bu özellik yalnızca tek veritabanı ve elastik havuzlarda kullanılabilir.
- Dizin oluşturup bırakma, sorguları Parametreleştirme ve şema sorunlarını çözme konularında yardımcı olacak önerileri görüntülemek için [SQL veritabanı Danışmanı](sql-database-advisor-portal.md) kullanın. Bu özellik yalnızca tek veritabanı ve elastik havuzlarda kullanılabilir.
- Veritabanı performansını otomatik olarak izlemek için [Azure SQL akıllı içgörüler](sql-database-intelligent-insights.md) kullanın. Bir performans sorunu algılandığında, bir tanılama günlüğü oluşturulur. Günlük Ayrıntılar ve sorunun bir kök neden analizi (RCA) sağlar. Mümkün olduğunda performans iyileştirme önerisi sunulmaktadır.
- Azure SQL veritabanı 'nın performans sorunlarını otomatik olarak düzeltmesine izin vermek için [otomatik ayarlamayı etkinleştirin](sql-database-automatic-tuning-enable.md) .
- Performans sorunlarında daha ayrıntılı sorun giderme konusunda yardım için [dinamik yönetim görünümlerini (DMVs)](sql-database-monitoring-with-dmvs.md), [Genişletilmiş olayları](sql-database-xevent-db-diff-from-svr.md)ve [sorgu deposunu](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) kullanın.

> [!TIP]
> Bir performans sorununu belirledikten sonra, Azure SQL veritabanı 'nın performansını artırmanın tekniklerini bulmak için [performans kılavuzumuzu](sql-database-performance-guidance.md) inceleyin.

## <a name="troubleshoot-performance-problems"></a>Performans sorunlarını giderme

Performans sorunlarını tanılamak ve çözmek için, her bir etkin sorgunun durumunu ve her bir iş yükü durumuyla ilgili performans sorunlarına neden olan koşulları bularak başlayın. Azure SQL veritabanı performansını geliştirmek için, uygulamadaki her etkin sorgu isteğinin çalışır durumda veya bekleme durumunda olduğunu anlamanız gerekir. Azure SQL veritabanında bir performans sorununu giderirken, aşağıdaki diyagramı aklınızda tutun.

![İş yükü durumları](./media/sql-database-monitor-tune-overview/workload-states.png)

Bir iş yükünde performans sorunu, CPU çekişmesinin ( *çalışırken ilgili* bir koşul) veya bir şeyi bekleyen tek tek sorgulardan ( *bekleme ile ilgili* bir koşul) kaynaklanabilir.

Çalıştırmaya ilişkin sorunların nedeni şunlar olabilir:
- **Derleme sorunları**: SQL sorgu iyileştiricisi, eski istatistikler, işlenecek satır sayısı için hatalı bir tahmin veya gerekli belleğin yanlış bir tahmini olması nedeniyle bir en iyi işlem planı üretebilir. Sorgunun geçmişte veya başka bir örnekte (yönetilen bir örnek ya da bir SQL Server örneği) daha hızlı yürütüldüğünü biliyorsanız, farklı olup olmadığını görmek için gerçek yürütme planlarını karşılaştırın. Daha iyi planı almak için sorgu ipuçları uygulamayı veya istatistikleri veya dizinleri yeniden oluşturmayı deneyin. Bu sorunları otomatik olarak azaltmak için Azure SQL veritabanı 'nda otomatik plan düzeltmesini etkinleştirin.
- **Yürütme sorunları**: sorgu planı en iyi durumda ise, muhtemelen günlük yazma aktarım hızı gibi veritabanının kaynak sınırlarına ulaşalım. Ya da yeniden oluşturulması gereken parçalanmış dizinleri kullanıyor olabilir. Aynı kaynaklara çok sayıda eşzamanlı sorgu gerektiğinde, yürütme sorunları da oluşabilir. Etkili bir şekilde yürütülmediği sorgular büyük olasılıkla bazı kaynakları beklerken, *bekleme ile ilgili* sorunlar genellikle yürütme sorunlarıyla ilgilidir.

Bekleme ile ilgili sorunlar şunlar olabilir:
- **Engelleme**: bir sorgu, diğerleri aynı nesnelere erişmeyi denediğinde veritabanındaki nesneler üzerindeki kilidi tutabilir. DMVs veya izleme araçlarını kullanarak engelleme sorgularını belirleyebilirsiniz.
- **GÇ sorunları**: sorgular sayfaların verilere veya günlük dosyalarına yazılmasını bekliyor olabilir. Bu durumda, DMV 'de `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`veya `PAGEIOLATCH_*` bekleme istatistiklerini kontrol edin.
- **Tempdb sorunları**: iş yükü geçici tablolar kullanıyorsa veya planlarda tempdb 'den varsa, sorgular tempdb aktarım hızı ile ilgili bir sorun olabilir. 
- **Bellekle ilgili sorunlar**: iş yükünün yeterli belleği yoksa, erkeklerin sayfa ömrü bırakabilir veya sorgular gerekenden daha az bellek alabilir. Bazı durumlarda, sorgu Iyileştiricinizdeki yerleşik zeka ile ilgili sorunları düzeltir.
 
Aşağıdaki bölümlerde bazı sorun türlerinin nasıl tanımlanacağına ve giderileceği açıklanmaktadır.

## <a name="performance-problems-related-to-running"></a>Çalıştırmayla ilgili performans sorunları

Genel bir kılavuz olarak, CPU kullanımı düzenli olarak yüzde 80 ' lik veya daha fazla olursa performans sorununuz çalışıyor demektir. Çalıştırmaya ilişkin bir sorun yetersiz CPU kaynaklarından kaynaklanıyor olabilir. Ya da aşağıdaki koşullardan biri ile ilişkili olabilir:

- Çok fazla çalışan sorgu
- Çok fazla derleme sorgusu
- Bir alt sorgu planı kullanan bir veya daha fazla çalıştırılan sorgu

Çalışırken ilgili bir performans sorunu bulursanız, amacınız bir veya daha fazla yöntem kullanarak kesin sorunu belirlemektir. Bu yöntemler, çalıştırmaya ilişkin sorunları belirlemek için en yaygın yollardır:

- CPU yüzdesi kullanımını izlemek için [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) kullanın.
- Aşağıdaki [DMVs](sql-database-monitoring-with-dmvs.md)'leri kullanın:

  - [Sys. dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV, bir SQL VERITABANı için CPU, g/ç ve bellek tüketimi döndürür. Veritabanında etkinlik olmasa bile, her 15 saniyelik Aralık için bir satır vardır. Geçmiş verileri bir saat boyunca tutulur.
  - [Sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV, Azure SQL VERITABANı için CPU kullanımı ve depolama verileri döndürür. Veriler, beş dakikalık aralıklarla toplanır ve toplanır.

> [!IMPORTANT]
> Sys. dm_db_resource_stats ve sys. resource_stats DMVs kullanan T-SQL sorguları için CPU kullanımı sorunlarını gidermek için bkz. [CPU performans sorunlarını tanımla](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>PSP sorunları olan sorgular

Bir parametre duyarlı plan (PSP) sorunu, sorgu iyileştiricisi yalnızca belirli bir parametre değeri (veya değerler kümesi) için en uygun olan bir sorgu yürütme planı oluşturduğunda ve önbelleğe alınmış plan, ardışık olarak kullanılan parametre değerleri için en uygun değer olmadığında oluşur yürütme. En iyi durumda olmayan planlar sorgu performans sorunlarına neden olabilir ve genel iş yükü verimini düşürebilir. 

Parametre algılaması ve sorgu işleme hakkında daha fazla bilgi için, bkz. [sorgu işleme mimarisi Kılavuzu](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Bazı geçici çözümler, PSP sorunlarını azaltır. Her geçici çözümün ilişkili avantajları ve dezavantajları vardır:

- Her sorgu yürütmesinde sorgu ipucunu yeniden [Derle](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) ' i kullanın. Bu geçici çözüm, daha iyi plan kalitesi için derleme süresini ve CPU 'YU artırabilir. `RECOMPILE` seçeneği genellikle yüksek aktarım hızı gerektiren iş yükleri için mümkün değildir.
- Gerçek parametre değerini, çoğu parametre değeri olasılıklarından yeterince iyi bir plan üreten tipik bir parametre değeri ile geçersiz kılmak için [seçeneğini kullanın (..](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) .........) sorgu ipucunu kullanın. Bu seçenek, en iyi parametre değerlerinin ve ilişkili plan özelliklerinin iyi bir şekilde anlaşılmasına gerek duyar.
- Gerçek parametre değerini geçersiz kılmak için [(BILINMEYEN IÇIN iyileştirin)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sorgu ipucunu kullanın ve bunun yerine yoğunluk vektörü ortalamasını kullanın. Bunu, yerel değişkenlerdeki gelen parametre değerlerini yakalayıp, sonra parametrelerinin kendilerini kullanmak yerine koşulların içindeki yerel değişkenleri kullanarak da yapabilirsiniz. Bu çözüm için, ortalama yoğunluğu *yeterince iyi*olmalıdır.
- [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sorgu ipucunu kullanarak tamamen parametre algılaması 'nı devre dışı bırakın.
- Önbellekte yeniden derleme yapılmasını engellemek için [KeepFixedPlan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sorgu ipucunu kullanın. Bu geçici çözüm, daha önce önbellekte olan uygun bir plan olduğunu varsayar. Ayrıca, iyi planın çıkartılacağı ve yeni bir hatalı planın derlenmesi olasılığını azaltmak için otomatik istatistik güncelleştirmelerini devre dışı bırakabilirsiniz.
- Sorguyu yeniden yazarak ve sorgu metnine ipucu ekleyerek planı [kullanma](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) sorgu ipucunu kullanarak planı kesin olarak zorlayın. Veya sorgu deposu kullanarak veya [otomatik ayarlamayı](sql-database-automatic-tuning.md)etkinleştirerek belirli bir planı ayarlayın.
- Tek yordamı, her biri koşullu Logic ve ilişkili parametre değerlerine göre kullanılabilecek, iç içe geçmiş bir yordamlar kümesiyle değiştirin.
- Statik yordam tanımına dinamik dize yürütme alternatifleri oluşturun.

PSP sorunlarını çözme hakkında daha fazla bilgi için şu blog gönderilerine bakın:

- [Bir parametre kokusu](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [Parametreli sorgular için Conor ve Dynamic SQL ve Procedures ile plan kalitesi karşılaştırması](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL Server 'de SQL sorgu iyileştirme teknikleri: parametre algılaması](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Yanlış Parametreleştirme nedeniyle oluşan etkinliğin derlenmesi

Bir sorguda değişmez değerler olduğunda, veritabanı altyapısı otomatik olarak ifadeyi ifade eder veya bir Kullanıcı, derleme sayısını azaltmak için ifadeyi açıkça parametreleştirir. Aynı kalıbı kullanan bir sorgu için yüksek sayıda derleme, ancak farklı değişmez değer değerleri yüksek CPU kullanımına neden olabilir. Benzer şekilde, yalnızca sabit değerli olmaya devam eden bir sorguyu kısmen parametreleştirebilirsiniz, veritabanı altyapısı sorguyu daha fazla parametrelemez.  

Kısmen parametreli sorguya bir örnek aşağıda verilmiştir:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Bu örnekte, `t1.c1` `@p1`alır, ancak `t2.c2` GUID 'yi sabit değer olarak alma devam eder. Bu durumda, `c2`değerini değiştirirseniz sorgu farklı bir sorgu olarak değerlendirilir ve yeni bir derleme gerçekleşecektir. Bu örnekteki derlemeleri azaltmak için GUID 'i de parametreleştirebilirsiniz.

Aşağıdaki sorgu, bir sorgunun doğru parametreli olup olmadığını anlamak için sorgu karmasında sorgu sayısını gösterir:

```sql
SELECT  TOP 10  
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p 
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs 
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>Sorgu planı değişikliklerini etkileyen faktörler

Bir sorgu yürütme planı yeniden derlemesi, önbelleğe alınmış orijinal plandan farklı olan üretilmiş bir sorgu planına neden olabilirler. Mevcut bir orijinal plan çeşitli nedenlerle otomatik olarak yeniden derlenir:
- Şemadaki değişikliklere sorgu tarafından başvuruluyor.
- Tablolardaki veri değişikliklerine sorgu tarafından başvurulur. 
- Sorgu bağlamı seçenekleri değiştirildi.

Derlenmiş bir plan, çeşitli nedenlerle önbellekten çıkarılabilir, örneğin:

- Örnek yeniden başlatmalar.
- Veritabanı kapsamlı yapılandırma değişiklikleri.
- Bellek baskısı.
- Önbelleği temizlemek için açık istekler.

Yeniden derleme ipucu kullanırsanız, bir plan önbelleğe alınmaz.

Yeniden derleme (veya önbellek çıkarılması sonrasında yeni derleme), orijinalle özdeş bir sorgu yürütme planının oluşturulmasına neden olabilir. Plan önceki veya orijinal plandan değiştiğinde, bu açıklamalar olasıdır:

- **Değiştirilen fiziksel tasarım**: Örneğin, yeni oluşturulan dizinler bir sorgunun gereksinimlerini daha etkin bir şekilde kapsar. Sorgu iyileştiricisi, yeni bir derlemede kullanılabilir ve bu yeni dizin kullanılarak sorgu yürütmenin ilk sürümü için başlangıçta seçilmiş olan veri yapısını kullanmaktan daha iyi bir hale gelebilir.  Başvurulan nesnelerde yapılan tüm fiziksel değişiklikler, derleme zamanında yeni bir plan seçimine neden olabilirler.

- **Sunucu kaynak farkları**: bir sistemdeki bir plan başka bir sistemdeki plandan farklıysa, kullanılabilir işlemcilerin sayısı gibi kaynak kullanılabilirliği, hangi planın oluşturulduğunu etkileyebilir.  Örneğin, bir sistemin daha fazla işlemcisi varsa, paralel bir plan seçilebilir. 

- **Farklı istatistikler**: başvurulan nesnelerle ilişkili istatistikler değişmiş olabilir veya özgün sistemin istatistikleriyle, bu durum önemli ölçüde farklı olabilir.  İstatistik değişikliği ve yeniden derleme gerçekleştiğinde, sorgu iyileştiricisi değiştiği sırada başlangıç istatistiklerini kullanır. Düzeltilen istatistiklerin veri dağıtımları ve frekansları, özgün derlemeden farklı bir farklılık gösterebilir.  Bu değişiklikler, kardinalite tahminleri oluşturmak için kullanılır. (*Kardinalite tahminleri* , mantıksal sorgu ağacı üzerinden akışı beklenen satır sayısıdır.) Kardinalite tahminlerinde yapılan değişiklikler, farklı fiziksel işleçler ve ilişkili işlem siparişleri seçmenize yol açabilir.  İstatistikte küçük değişiklikler bile değiştirilen bir sorgu yürütme planına yol açabilir.

- **Değiştirilen veritabanı uyumluluk düzeyi veya kardinalite tahmini sürümü**: veritabanı uyumluluk düzeyinde yapılan değişiklikler, farklı bir sorgu yürütme planına neden olabilecek yeni stratejiler ve özellikler sağlayabilir.  Veritabanı uyumluluk düzeyinin ötesinde, devre dışı veya etkin bir izleme bayrağı 4199 veya veritabanı kapsamlı yapılandırma QUERY_OPTIMIZER_HOTFIXES değiştirilen bir durum, derleme zamanında sorgu yürütme planı seçimlerini de etkileyebilir.  İzleme bayrakları 9481 (eski CE 'yi zorla) ve 2312 (varsayılan CE 'yi zorla) Ayrıca planı da etkiler. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Sorun sorgularını çözümleyin veya daha fazla kaynak sağlayın

Sorunu tanımladıktan sonra, sorun sorgularını ayarlayabilir veya işlem boyutunu ya da hizmet katmanını yükselterek SQL veritabanınızın kapasitesini artışlarını devralarak CPU gereksinimlerine artırabilirsiniz. 

Daha fazla bilgi için bkz. Azure SQL [veritabanı 'nda tek veritabanı kaynaklarını ölçeklendirme](sql-database-single-database-scale.md) ve [Azure SQL veritabanı 'nda elastik havuz kaynaklarını ölçeklendirme](sql-database-elastic-pool-scale.md). Yönetilen bir örneği ölçeklendirme hakkında daha fazla bilgi için bkz. [hizmet katmanı kaynak sınırları](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Artan iş yükü biriminden kaynaklanan performans sorunları

Uygulama trafiği ve iş yükü birimi artışının artması CPU kullanımına neden olabilir. Ancak bu sorunu doğru bir şekilde tanılamak için dikkatli olmanız gerekir. Yüksek CPU bir sorun gördüğünüzde, artmasının iş yükü birimindeki değişikliklerden kaynaklanıp kaynaklanmadığını öğrenmek için bu soruları yanıtlayın:

- Uygulamanın sorguları yüksek CPU sorununun nedeni mi?
- Tanımlayabilmeniz için en üstteki CPU kullanan sorgular için:

   - Aynı sorguyla ilişkili birden çok yürütme planı var mı? Öyleyse, neden?
   - Aynı yürütme planına sahip sorgularda yürütme süresi tutarlı mi? Yürütme sayısı artdı mı? Öyleyse, iş yükü artışı büyük olasılıkla performans sorunlarına neden olur.

Özet olarak, sorgu yürütme planı farklı şekilde yürütüolmadıysa ancak yürütme sayısı ile birlikte CPU kullanımı artdıysa, performans sorunu büyük olasılıkla iş yükü artışı ile ilgilidir.

Bir CPU sorunu sunan iş yükü birimi değişikliğini belirlemek her zaman kolay değildir. Şu faktörleri göz önünde bulundurun: 

- **Değiştirilen kaynak kullanımı**: ÖRNEĞIN, CPU kullanımının uzun bir süre için yüzde 80 ' luk arttığı bir senaryoyu düşünün.  Yalnızca CPU kullanımı, iş yükü biriminin değiştiği anlamına gelmez. Sorgu yürütme planındaki gerilemeler ve veri dağıtımındaki değişiklikler aynı zamanda uygulama aynı iş yükünü yürüttüğünde bile daha fazla kaynak kullanımına katkıda bulunabilir.

- **Yeni bir sorgunun görünümü**: bir uygulama, farklı zamanlarda yeni bir sorgu kümesini sürücü halinde kullanabilir.

- **İstek sayısında artış veya azaltma**: Bu senaryo, bir iş yükünün en belirgin ölçümüdür. Sorgu sayısı, her zaman daha fazla kaynak kullanımına karşılık gelmez. Ancak, bu ölçüm hala önemli bir sinyaldir ve diğer faktörler değiştirilmez.

## <a name="waiting-related-performance-problems"></a>Bekleme ile ilgili performans sorunları 

Performans sorununuzun yüksek CPU kullanımı veya çalışıyor ile ilgili olmadığından emin değilseniz, sorununuz beklenme ile ilgilidir. Yani, CPU kaynakları başka bir kaynakta beklediği için CPU kaynaklarınız etkili bir şekilde kullanılmıyor. Bu durumda, CPU kaynaklarınızın ne beklediğini belirler. 

Bu yöntemler genellikle bekleme türlerinin en üstteki kategorilerini göstermek için kullanılır:

- Zaman içinde her bir sorgunun bekleme istatistiklerini bulmak için [sorgu deposu](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) ' nu kullanın. Sorgu deposunda, bekleme türleri bekleme kategorilerine birleştirilir. Wait kategorilerinin, [sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)içindeki bekleme türlerine eşlemesini bulabilirsiniz.
- İşlem sırasında yürütülen iş parçacıklarının karşılaştığı tüm bekler hakkında bilgi döndürmek için [sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) kullanın. Azure SQL veritabanı ve ayrıca belirli sorgular ve toplu işlerle ilgili performans sorunlarını tanılamak için bu toplanmış görünümü kullanabilirsiniz.
- Bazı kaynakları bekleyen görev kuyruğu hakkında bilgi döndürmek için [sys. dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) kullanın.

Yüksek CPU senaryolarında sorgu deposu ve bekleme istatistikleri şu durumlarda CPU kullanımını yansıtmayabilir:

- Yüksek CPU kullanan sorgular hala yürütülüyor.
- Yük devretme sırasında yüksek CPU kullanan sorgular çalışıyor.

Dmv sorgu deposunu ve bekleme istatistiklerini izleyen yalnızca başarıyla tamamlanan ve zaman aşımına uğrayan sorgular için sonuçları gösterir. Deyimler tamamlanana kadar Şu anda yürütülmekte olan deyimler için veri göstermez. Şu anda yürütülmekte olan sorguları ve ilişkili çalışan saatini izlemek için, dinamik yönetim görünümü [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) kullanın.

Bu makalenin başındaki grafik en sık görülen süreyi gösterir:

- Kilitler (engelleme)
- G/Ç
- TempDB ile ilgili çekişme
- Bellek izni bekler

> [!IMPORTANT]
> İle ilgili sorunları gidermek için DMVs kullanan T-SQL sorguları kümesi için bkz.:
>
> - [G/ç performans sorunlarını tanımla](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Bellek verme süresi istemini tanımla](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox bekler ve Cadılar](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Daha fazla kaynakla veritabanı performansını iyileştirme

Uygun olmayan hiçbir öğe veritabanı performansını iyileştire, Azure SQL veritabanı 'nda kullanılabilir kaynak miktarını değiştirebilirsiniz. Tek bir veritabanının [DTU hizmeti katmanını](sql-database-service-tiers-dtu.md) değiştirerek daha fazla kaynak atayın. Ya da elastik havuzun eDTU sayısını dilediğiniz zaman artırabilirsiniz. Alternatif olarak, [sanal çekirdek tabanlı satın alma modelini](sql-database-service-tiers-vcore.md)kullanıyorsanız, hizmet katmanını değiştirin ya da veritabanınıza ayrılan kaynakları artırın.

Tek veritabanları için, veritabanı performansını artırmak üzere [Hizmet katmanlarını veya işlem kaynaklarını](sql-database-single-database-scale.md) isteğe bağlı olarak değiştirebilirsiniz. Birden çok veritabanı için, kaynakları otomatik olarak ölçeklendirmek için [Esnek havuzlar](sql-database-elastic-pool-guidance.md) kullanmayı düşünün.

## <a name="tune-and-refactor-application-or-database-code"></a>Uygulama veya veritabanı kodunu ayarlama ve yeniden düzenleme

Veritabanı için uygulama kodunu iyileştirebilirsiniz, dizinleri değiştirebilir, planları zorlayabilir veya veritabanını iş yükünüze el ile uyarlayabilmeniz için ipuçları kullanabilirsiniz. El ile ayarlama ve kodu yeniden yazma hakkında daha fazla bilgi için bkz. [performans ayarlama Kılavuzu](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL veritabanı 'nda otomatik ayarlamayı etkinleştirmek ve otomatik ayarlama özelliğinin iş yükünüzü tamamen yönetmesine izin vermek için bkz. [otomatik ayarlamayı etkinleştirme](sql-database-automatic-tuning-enable.md).
- El ile ayarlamayı kullanmak için [Azure Portal ayarlama önerilerini](sql-database-advisor-portal.md)gözden geçirin. Sorgularınızın performansını artıran önerileri el ile uygulayın.
- [Azure SQL veritabanı hizmet katmanlarını](sql-database-performance-guidance.md)değiştirerek veritabanınızda kullanılabilir kaynakları değiştirin.

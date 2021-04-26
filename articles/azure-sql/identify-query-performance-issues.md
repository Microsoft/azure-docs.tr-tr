---
title: Azure SQL veritabanı 'nda sorgu performans sorunları türleri
description: Bu makalede, Azure SQL veritabanı 'nda sorgu performans sorunlarının türleri hakkında bilgi edinin ve ayrıca bu sorunlarla birlikte sorguları belirlemeyi ve çözümlemeyi öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 039332a8728e5d7e5b605f51f4bb53e6dcbb6381
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109178"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL Veritabanı'nda algılanabilir sorgu performansı sorunu türleri
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Performans darboğazı çözümlemeye çalışırken, sorgu çalışır durumda veya bekleme durumunda olduğunda performans sorunu olup olmadığını belirleyerek başlayın. Farklı çözümler bu belirlenmeye göre geçerlidir. Çalışan ilgili bir sorun veya bekleme ile ilgili bir sorun oluşmasına neden olabilecek faktörleri anlamanıza yardımcı olması için aşağıdaki diyagramı kullanın. Her bir sorun türüyle ilgili sorunlar ve çözümler bu makalede ele alınmıştır.

Bu tür performans sorunlarını algılamak için Azure SQL veritabanı [akıllı içgörüler](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) veya [DMVs](database/monitoring-with-dmvs.md) SQL Server kullanabilirsiniz.

![İş yükü durumları](./media/identify-query-performance-issues/workload-states.png)

**Çalışma ile ilgili sorunlar**: çalışma ile ilgili sorunlar genellikle yetersiz veya aşırı kullanılan kaynaklarla ilgili bir alt sorgu planına veya yürütme sorunlarına neden olan derleme sorunlarıyla ilgilidir.
Beklenme **sorunları**: bekleyen ilgili sorunlar genellikle ile ilgilidir:

- Kilitler (engelleme)
- G/Ç
- TempDB kullanımı ile ilgili çekişme
- Bellek izni bekler

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Derleme sorunları, bir alt sorgu planına yol açar

SQL sorgu Iyileştiricisi tarafından oluşturulan bir alt plan planı, yavaş sorgu performansının nedeni olabilir. SQL sorgu Iyileştiricisi, eksik dizin, eski istatistikler, işlenecek satır sayısının yanlış bir tahmini veya gerekli belleğin yanlış bir tahminini nedeniyle bir En Iyi işlem planı oluşturabilir. Sorgunun geçmişte veya başka bir örnekte daha hızlı yürütüldüğünü biliyorsanız, gerçek yürütme planlarını farklı olup olmadığını görmek için karşılaştırın.

- Aşağıdaki yöntemlerden birini kullanarak eksik dizinleri tanımla:

  - [Akıllı içgörüler](database/intelligent-insights-troubleshoot-performance.md#missing-index)kullanın.
  - Tek ve havuza alınmış veritabanları için [veritabanı Danışmanı](database/database-advisor-implement-performance-recommendations.md) .
  - DMV 'leri. Bu örnek, eksik bir dizinin etkisini, DMVs 'yi kullanarak [eksik dizinleri](database/performance-guidance.md#identifying-and-adding-missing-indexes) algılamayı ve eksik dizin önerisini uygulama etkisini gösterir.
- Daha iyi planı almak için [sorgu ipuçları](/sql/t-sql/queries/hints-transact-sql-query)uygulamayı, [İstatistikleri güncelleştirmeyi](/sql/t-sql/statements/update-statistics-transact-sql)veya [dizinleri yeniden oluşturmayı](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) deneyin. Bu sorunları otomatik olarak azaltmak için Azure SQL veritabanı 'nda [Otomatik plan düzeltmesini](../azure-sql/database/automatic-tuning-overview.md) etkinleştirin.

  Bu [örnek](database/performance-guidance.md#query-tuning-and-hinting) , parametreli bir sorgu, bu koşulun algılanması ve bir sorgu ipucunun çözümlenmesi için nasıl kullanılacağı nedeniyle, bir alt sorgu planının etkisini gösterir.

- Veritabanı uyumluluk düzeyini değiştirmeyi ve akıllı sorgu işlemeyi uygulamayı deneyin. SQL sorgu Iyileştiricisi, veritabanınızın uyumluluk düzeyine bağlı olarak farklı bir sorgu planı oluşturabilir. Daha yüksek uyumluluk düzeyleri daha [akıllı sorgu işleme özellikleri](/sql/relational-databases/performance/intelligent-query-processing)sağlar.

  - Sorgu işleme hakkında daha fazla bilgi için bkz. [sorgu Işleme mimari Kılavuzu](/sql/relational-databases/query-processing-architecture-guide).
  - Veritabanı uyumluluk düzeylerini değiştirmek ve uyumluluk düzeyleri arasındaki farklar hakkında daha fazla bilgi edinmek için bkz. [alter database](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Kardinalite tahmini hakkında daha fazla bilgi için bkz. [kardinalite tahmini](/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Alt sorgu yürütme planlarıyla sorguları çözümleme

Aşağıdaki bölümlerde, alt sorgu yürütme planına sahip sorguların nasıl çözümleneceği ele alınmıştır.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> Parametreye duyarlı plan (PSP) sorunları olan sorgular

Bir parametre duyarlı plan (PSP) sorunu, sorgu iyileştiricisi yalnızca belirli bir parametre değeri (veya değerler kümesi) için ideal olan bir sorgu yürütme planı oluşturduğunda ve önbelleğe alınmış plan daha sonra ardışık yürütmeler içinde kullanılan parametre değerleri için en iyi durumda değilse oluşur. En iyi durumda olmayan planlar sorgu performans sorunlarına neden olabilir ve genel iş yükü verimini düşürebilir.

Parametre algılaması ve sorgu işleme hakkında daha fazla bilgi için, bkz. [sorgu işleme mimarisi Kılavuzu](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Bazı geçici çözümler, PSP sorunlarını azaltır. Her geçici çözümün ilişkili avantajları ve dezavantajları vardır:

- Her sorgu yürütmesinde sorgu ipucunu yeniden [Derle](/sql/t-sql/queries/hints-transact-sql-query) ' i kullanın. Bu geçici çözüm, daha iyi plan kalitesi için derleme süresini ve CPU 'YU artırabilir. `RECOMPILE`Yüksek aktarım hızı gerektiren iş yükleri için bu seçenek genellikle mümkün değildir.
- Gerçek parametre değerini, çoğu parametre değeri olasılıklarından yeterince iyi bir plan üreten tipik bir parametre değeri ile geçersiz kılmak için [seçeneğini kullanın (..](/sql/t-sql/queries/hints-transact-sql-query) .........) sorgu ipucunu kullanın. Bu seçenek, en iyi parametre değerlerinin ve ilişkili plan özelliklerinin iyi bir şekilde anlaşılmasına gerek duyar.
- Gerçek parametre değerini geçersiz kılmak için [(BILINMEYEN IÇIN iyileştirin)](/sql/t-sql/queries/hints-transact-sql-query) sorgu ipucunu kullanın ve bunun yerine yoğunluk vektörü ortalamasını kullanın. Bunu, yerel değişkenlerdeki gelen parametre değerlerini yakalayıp, sonra parametrelerinin kendilerini kullanmak yerine koşulların içindeki yerel değişkenleri kullanarak da yapabilirsiniz. Bu çözüm için, ortalama yoğunluğu *yeterince iyi* olmalıdır.
- [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) sorgu ipucunu kullanarak tamamen parametre algılaması 'nı devre dışı bırakın.
- Önbellekte yeniden derleme yapılmasını engellemek için [KeepFixedPlan](/sql/t-sql/queries/hints-transact-sql-query) sorgu ipucunu kullanın. Bu geçici çözüm, daha önce önbellekte olan uygun bir plan olduğunu varsayar. Ayrıca, iyi planın çıkartılacağı ve yeni bir hatalı planın derlenmesi olasılığını azaltmak için otomatik istatistik güncelleştirmelerini devre dışı bırakabilirsiniz.
- Sorguyu yeniden yazarak ve sorgu metnine ipucu ekleyerek planı [kullanma](/sql/t-sql/queries/hints-transact-sql-query) sorgu ipucunu kullanarak planı kesin olarak zorlayın. Veya sorgu deposu kullanarak veya [otomatik ayarlamayı](../azure-sql/database/automatic-tuning-overview.md)etkinleştirerek belirli bir planı ayarlayın.
- Tek yordamı, her biri koşullu Logic ve ilişkili parametre değerlerine göre kullanılabilecek, iç içe geçmiş bir yordamlar kümesiyle değiştirin.
- Statik yordam tanımına dinamik dize yürütme alternatifleri oluşturun.

PSP sorunlarını çözme hakkında daha fazla bilgi için şu blog gönderilerine bakın:

- [Bir parametre kokusu](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Parametreli sorgular için Conor ve Dynamic SQL ve Procedures ile plan kalitesi karşılaştırması](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [SQL Server 'de SQL sorgu iyileştirme teknikleri: parametre algılaması](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Yanlış Parametreleştirme nedeniyle oluşan etkinliğin derlenmesi

Bir sorguda değişmez değerler olduğunda, veritabanı altyapısı otomatik olarak ifadeyi ifade eder veya bir Kullanıcı, derleme sayısını azaltmak için ifadeyi açıkça parametreleştirir. Aynı kalıbı kullanan bir sorgu için yüksek sayıda derleme, ancak farklı değişmez değer değerleri yüksek CPU kullanımına neden olabilir. Benzer şekilde, yalnızca sabit değerli olmaya devam eden bir sorguyu kısmen parametreleştirebilirsiniz, veritabanı altyapısı sorguyu daha fazla parametrelemez.

Kısmen parametreli sorguya bir örnek aşağıda verilmiştir:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

Bu örnekte, `t1.c1` alır `@p1` , ancak `t2.c2` GUID 'yi değişmez değer olarak kullanmaya devam eder. Bu durumda, için değerini değiştirirseniz `c2` , sorgu farklı bir sorgu olarak değerlendirilir ve yeni bir derleme gerçekleşecektir. Bu örnekteki derlemeleri azaltmak için GUID 'i de parametreleştirebilirsiniz.

Aşağıdaki sorgu, bir sorgunun doğru parametreli olup olmadığını anlamak için sorgu karmasında sorgu sayısını gösterir:

```sql
SELECT TOP 10
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
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>Sorgu planı değişikliklerini etkileyen faktörler

Bir sorgu yürütme planı yeniden derlemesi, önbelleğe alınmış orijinal plandan farklı olan üretilmiş bir sorgu planına neden olabilirler. Mevcut bir orijinal plan çeşitli nedenlerle otomatik olarak yeniden derlenir:

- Şemadaki değişikliklere sorgu tarafından başvuruluyor
- Tablolardaki veri değişikliklerine sorgu tarafından başvurulur
- Sorgu bağlamı seçenekleri değiştirildi

Derlenmiş bir plan, çeşitli nedenlerle önbellekten çıkarılabilir, örneğin:

- Örnek yeniden başlatmalar
- Veritabanı kapsamlı yapılandırma değişiklikleri
- Bellek baskısı
- Önbelleği temizlemek için açık istekler

Yeniden derleme ipucu kullanırsanız, bir plan önbelleğe alınmaz.

Yeniden derleme (veya önbellek çıkarılması sonrasında yeni derleme), orijinalle özdeş bir sorgu yürütme planının oluşturulmasına neden olabilir. Plan önceki veya orijinal plandan değiştiğinde, bu açıklamalar olasıdır:

- **Değiştirilen fiziksel tasarım**: Örneğin, yeni oluşturulan dizinler bir sorgunun gereksinimlerini daha etkin bir şekilde kapsar. Sorgu iyileştiricisi, yeni bir derlemede kullanılabilir ve bu yeni dizin kullanılarak sorgu yürütmenin ilk sürümü için başlangıçta seçilmiş olan veri yapısını kullanmaktan daha iyi bir hale gelebilir. Başvurulan nesnelerde yapılan tüm fiziksel değişiklikler, derleme zamanında yeni bir plan seçimine neden olabilirler.

- **Sunucu kaynak farkları**: bir sistemdeki bir plan başka bir sistemdeki plandan farklıysa, kullanılabilir işlemcilerin sayısı gibi kaynak kullanılabilirliği, hangi planın oluşturulduğunu etkileyebilir. Örneğin, bir sistemin daha fazla işlemcisi varsa, paralel bir plan seçilebilir. Azure SQL veritabanı 'nda paralellik hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda en yüksek paralellik derecesi (MAXDOP) yapılandırma](database/configure-max-degree-of-parallelism.md).

- **Farklı istatistikler**: başvurulan nesnelerle ilişkili istatistikler değişmiş olabilir veya özgün sistemin istatistikleriyle, bu durum önemli ölçüde farklı olabilir. İstatistik değişikliği ve yeniden derleme gerçekleştiğinde, sorgu iyileştiricisi değiştiği sırada başlangıç istatistiklerini kullanır. Düzeltilen istatistiklerin veri dağıtımları ve frekansları, özgün derlemeden farklı bir farklılık gösterebilir. Bu değişiklikler, kardinalite tahminleri oluşturmak için kullanılır. (*Kardinalite tahminleri* , mantıksal sorgu ağacı üzerinden akışı beklenen satır sayısıdır.) Kardinalite tahminlerinde yapılan değişiklikler, farklı fiziksel işleçler ve ilişkili işlem siparişleri seçmenize yol açabilir. İstatistikte küçük değişiklikler bile değiştirilen bir sorgu yürütme planına yol açabilir.

- **Değiştirilen veritabanı uyumluluk düzeyi veya kardinalite tahmini sürümü**: veritabanı uyumluluk düzeyinde yapılan değişiklikler, farklı bir sorgu yürütme planına neden olabilecek yeni stratejiler ve özellikler sağlayabilir. Veritabanı uyumluluk düzeyinin ötesinde, devre dışı veya etkin bir izleme bayrağı 4199 veya veritabanı kapsamlı yapılandırma QUERY_OPTIMIZER_HOTFIXES değiştirilen bir durum, derleme zamanında sorgu yürütme planı seçimlerini de etkileyebilir. İzleme bayrakları 9481 (eski CE 'yi zorla) ve 2312 (varsayılan CE 'yi zorla) Ayrıca planı da etkiler.

## <a name="resource-limits-issues"></a>Kaynak sınırları sorunları

Yavaş sorgu performansı, alt ve olmayan sorgu planlarıyla ilgili değildir ve eksik dizinler genellikle yetersiz veya aşırı kullanılan kaynaklarla ilgilidir. Sorgu planı en iyi durumda ise sorgu (ve veritabanı) veritabanı, elastik havuz veya yönetilen örnek için kaynak sınırlarına ulaşmalıdır. Hizmet düzeyi için daha fazla günlük yazma aktarım hızı olabilir.

- Azure portal kullanarak kaynak sorunlarını algılama: kaynak limitlerinin sorun olup olmadığını görmek Için bkz. [SQL veritabanı kaynak izleme](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Tek veritabanları ve elastik havuzlar için bkz. [veritabanı Danışmanı performans önerileri](database/database-advisor-implement-performance-recommendations.md) ve [sorgu performansı öngörüleri](database/query-performance-insight-use.md).
- [Akıllı içgörüler](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) kullanarak kaynak sınırlarını algılama
- [DMVs](database/monitoring-with-dmvs.md)kullanarak kaynak sorunlarını algılama:

  - [Sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV, VERITABANı için CPU, g/ç ve bellek tüketimi döndürür. Veritabanında etkinlik olmasa bile, her 15 saniyelik Aralık için bir satır vardır. Geçmiş verileri bir saat boyunca tutulur.
  - [Sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV, Azure SQL VERITABANı için CPU kullanımı ve depolama verileri döndürür. Veriler, beş dakikalık aralıklarla toplanır ve toplanır.
  - [Yüksek CPU kullanan çok sayıda tekil sorgu](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Sorunu yetersiz kaynak olarak belirlerseniz, CPU gereksinimlerini artışlarını devralarak için veritabanınızın kapasitesini artırmak üzere kaynakları yükseltebilirsiniz. Daha fazla bilgi için bkz. Azure SQL [veritabanı 'nda tek veritabanı kaynaklarını ölçeklendirme](database/single-database-scale.md) ve [Azure SQL veritabanı 'nda elastik havuz kaynaklarını ölçeklendirme](database/elastic-pool-scale.md). Yönetilen bir örneği ölçeklendirme hakkında daha fazla bilgi için bkz. [hizmet katmanı kaynak sınırları](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Artan iş yükü biriminden kaynaklanan performans sorunları

Uygulama trafiği ve iş yükü birimi artışının artması CPU kullanımına neden olabilir. Ancak bu sorunu doğru bir şekilde tanılamak için dikkatli olmanız gerekir. Yüksek CPU bir sorun gördüğünüzde, artmasının iş yükü birimindeki değişikliklerden kaynaklanıp kaynaklanmadığını öğrenmek için bu soruları yanıtlayın:

- Uygulamanın sorguları yüksek CPU sorununun nedeni mi?
- [Tanımlayabilmeniz için en ÜSTTEKI CPU kullanan sorgular](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)için:

  - Aynı sorguyla ilişkili birden çok yürütme planı var mı? Öyleyse, neden?
  - Aynı yürütme planına sahip sorgularda yürütme süresi tutarlı mi? Yürütme sayısı artdı mı? Öyleyse, iş yükü artışı büyük olasılıkla performans sorunlarına neden olur.

Özet olarak, sorgu yürütme planı farklı şekilde yürütüolmadıysa ancak yürütme sayısı ile birlikte CPU kullanımı artdıysa, performans sorunu büyük olasılıkla iş yükü artışı ile ilgilidir.

Bir CPU sorunu sunan iş yükü birimi değişikliğini belirlemek her zaman kolay değildir. Şu faktörleri göz önünde bulundurun:

- **Değiştirilen kaynak kullanımı**: ÖRNEĞIN, CPU kullanımının uzun bir süre için yüzde 80 ' luk arttığı bir senaryoyu düşünün. Yalnızca CPU kullanımı, iş yükü biriminin değiştiği anlamına gelmez. Sorgu yürütme planındaki gerilemeler ve veri dağıtımındaki değişiklikler aynı zamanda uygulama aynı iş yükünü yürüttüğünde bile daha fazla kaynak kullanımına katkıda bulunabilir.

- **Yeni bir sorgunun görünümü**: bir uygulama, farklı zamanlarda yeni bir sorgu kümesini sürücü halinde kullanabilir.

- **İstek sayısında artış veya azaltma**: Bu senaryo, bir iş yükünün en belirgin ölçümüdür. Sorgu sayısı, her zaman daha fazla kaynak kullanımına karşılık gelmez. Ancak, bu ölçüm hala önemli bir sinyaldir ve diğer faktörler değiştirilmez.

[İş yükü artışsını](database/intelligent-insights-troubleshoot-performance.md#workload-increase) ve [gerileme planını](database/intelligent-insights-troubleshoot-performance.md#plan-regression)algılamak için akıllı içgörüler kullanın.

- **Paralellik**: aşırı paralellik, diğer CPU ve çalışan iş parçacığı kaynakları sorgularını gerçekleştirerek diğer eşzamanlı iş yükü performansına neden olabilir. Azure SQL veritabanı 'nda paralellik hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda en yüksek paralellik derecesi (MAXDOP) yapılandırma](database/configure-max-degree-of-parallelism.md).

## <a name="waiting-related-problems"></a>Bekleme ile ilgili sorunlar

Bir alt plan planını ve yürütme sorunlarıyla ilgili *bekleme* sorunlarını ortadan kaldırdıktan sonra, performans sorunu genellikle sorgular büyük olasılıkla bazı kaynakları bekliyor. Bekleme ile ilgili sorunlar şunlar olabilir:

- **Engelleme**:

  Bir sorgu, diğer kullanıcılar aynı nesnelere erişmeyi denediğinde veritabanındaki nesneleri kilitleyip tutabilirler. [DMVs](database/monitoring-with-dmvs.md#monitoring-blocked-queries) veya [akıllı içgörüler](database/intelligent-insights-troubleshoot-performance.md#locking)kullanarak engelleme sorgularını belirleyebilirsiniz. Daha fazla bilgi için bkz. [Azure SQL engelleme sorunlarını anlama ve çözme](database/understand-resolve-blocking.md).
- **GÇ sorunları**

  Sorgular, sayfaların verilere veya günlük dosyalarına yazılmasını bekliyor olabilir. Bu durumda, `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG` `PAGEIOLATCH_*` DMV 'de, veya bekleme istatistiklerini kontrol edin. [GÇ performans sorunlarını belirlemek](database/monitoring-with-dmvs.md#identify-io-performance-issues)için bkz. DMVs 'yi kullanma.
- **TempDB sorunları**

  İş yükü geçici tablolar kullanıyorsa veya planlarda TempDB 'ye taşdıysanız, sorgular TempDB aktarım hızı ile ilgili bir sorun olabilir. Bkz. DMVs 'yi [kimlik tempdb sorunlarını](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues)kullanma.
- **Bellekle ilgili sorunlar**

  İş yükü yeterli belleğe sahip değilse, erkeklerin sayfa ömrü bırakabilir veya sorgular gerekenden daha az bellek alabilir. Bazı durumlarda, sorgu Iyileştiricinizdeki yerleşik zeka ile ilgili sorunları düzeltir. Bkz. [bellek verme sorunlarını belirlemek](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)Için DMVs kullanma.

### <a name="methods-to-show-top-wait-categories"></a>En üstteki bekleme kategorilerini gösterme yöntemleri

Bu yöntemler genellikle bekleme türlerinin en üstteki kategorilerini göstermek için kullanılır:

- [Daha fazla bekleme](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) nedeniyle performans düşüşüne sahip sorguları tanımlamak için akıllı içgörüler kullanın
- Zaman içinde her bir sorgunun bekleme istatistiklerini bulmak için [sorgu deposu](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) ' nu kullanın. Sorgu deposunda, bekleme türleri bekleme kategorilerine birleştirilir. Bekleme kategorilerinin [sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)bekleyen türlere eşlemesini bulabilirsiniz.
- Bir sorgu işlemi sırasında yürütülen iş parçacıklarının karşılaştığı tüm bekleme bilgileri hakkında bilgi döndürmek için [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) kullanın. Azure SQL veritabanı ve ayrıca belirli sorgular ve toplu işlerle ilgili performans sorunlarını tanılamak için bu toplanmış görünümü kullanabilirsiniz. Sorgular, kaynakların, kuyruğun beklediği veya dış bekleme için bekleniyor.
- Bazı kaynakları bekleyen görev kuyruğu hakkında bilgi döndürmek için [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) kullanın.

Yüksek CPU senaryolarında sorgu deposu ve bekleme istatistikleri şu durumlarda CPU kullanımını yansıtmayabilir:

- Yüksek CPU kullanan sorgular hala yürütülüyor.
- Yük devretme sırasında yüksek CPU kullanan sorgular çalışıyor.

Dmv sorgu deposunu ve bekleme istatistiklerini izleyen yalnızca başarıyla tamamlanan ve zaman aşımına uğrayan sorgular için sonuçları gösterir. Deyimler tamamlanana kadar Şu anda yürütülmekte olan deyimler için veri göstermez. Şu anda yürütülmekte olan sorguları ve ilişkili çalışan saatini izlemek için dinamik yönetim görünümünü [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) kullanın.

> [!TIP]
> Ek araçlar:
>
> - [TigerToolbox bekler ve Cadılar](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="see-also"></a>Ayrıca bkz.

* [Azure SQL veritabanı 'nda maksimum paralellik derecesi (MAXDOP) yapılandırma](database/configure-max-degree-of-parallelism.md)
* [Azure SQL veritabanı 'nda Azure SQL veritabanı engelleme sorunlarını anlama ve çözme](database/understand-resolve-blocking.md)

## <a name="next-steps"></a>Sonraki adımlar

* [SQL veritabanı izleme ve ayarlamaya genel bakış](database/monitor-tune-overview.md)

---
title: DTU'dan Sanal Çekirdeğe geçiş
description: Azure SQL veritabanı 'ndaki bir veritabanını DTU modelinden vCore modeline geçirin. VCore 'a geçiş, standart ve Premium katmanları arasında yükseltme veya eski sürüme düşürme ile benzerdir.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/09/2021
ms.openlocfilehash: 332a2273a377268a425619a0cdaa5f4780b46e73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361664"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>DTU tabanlı modelden Azure SQL veritabanı 'nı sanal çekirdek tabanlı modele geçirme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Bu makalede, Azure SQL veritabanı 'ndaki veritabanınızı [DTU tabanlı satın alma modelinden](service-tiers-dtu.md) [sanal çekirdek tabanlı satın alma modeline](service-tiers-vcore.md)nasıl geçirebileceğiniz açıklanır. 

## <a name="migrate-a-database"></a>Veritabanını geçirme

DTU tabanlı satın alma modelinden bir veritabanını sanal çekirdek tabanlı satın alma modeline geçirmek, temel, standart ve Premium hizmet katmanlarındaki hizmet hedefleri arasında ölçeklendirilmesine benzer ve bu da geçiş işleminin sonunda benzer [süreli](single-database-scale.md#latency) ve [en az bir kesinti](scale-resources.md) yaşar. Sanal çekirdek tabanlı satın alma modeline geçirilmiş bir veritabanı, aynı şekilde, [hiper ölçek](service-tier-hyperscale.md) hizmeti katmanına geçirilmiş veritabanlarının dışında, DTU tabanlı satın alma modeline geri geçirilebilir. 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>Sanal çekirdek hizmet katmanını ve hizmet hedefini seçin

Çoğu DTU 'ya vCore geçiş senaryosunda, temel ve standart hizmet katmanlarında veritabanları ve elastik havuzlar [genel amaçlı](service-tier-general-purpose.md) hizmet katmanıyla eşlenir. Premium hizmet katmanındaki veritabanları ve elastik havuzlar [iş açısından kritik](service-tier-business-critical.md) hizmet katmanıyla eşlenir. Uygulama senaryosuna ve gereksinimlerine bağlı olarak, [hiper ölçek](service-tier-hyperscale.md) hizmet katmanı genellıkle tüm DTU hizmeti katmanlarında tek veritabanları için geçiş hedefi olarak kullanılabilir.

Sanal çekirdek modelindeki geçirilmiş veritabanı için hizmet hedefini veya işlem boyutunu seçmek üzere, basit ancak yaklaşık bir Thumb kuralı kullanabilirsiniz: temel veya standart katmanlardaki her 100 DTU, *en az* 1 sanal çekirdek gerektirir ve Premium katmandaki her 125 DTU en *az* 1 sanal çekirdek gerektirir. 

> [!TIP]
> Bu kural, DTU veritabanı veya elastik havuz için kullanılan donanım üretimini düşünmediği için yaklaşık bir değer. 

DTU modelinde, kullanılabilir [donanım oluşturma](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) , veritabanınız veya elastik havuzunuz için kullanılabilir. Ayrıca, daha yüksek veya düşük DTU veya eDTU değerlerini seçerek sanal çekirdekler (mantıksal CPU 'Lar) üzerinde yalnızca dolaylı denetime sahip olursunuz. 

VCore modeliyle, müşterilerin hem donanım oluşturma hem de sanal çekirdek sayısı (mantıksal CPU 'Lar) için açık bir seçim yapması gerekir. DTU modeli bu seçimleri sunmaz, ancak donanım oluşturma ve her veritabanı ve elastik havuz için kullanılan mantıksal CPU sayısı dinamik yönetim görünümleri aracılığıyla sunulur. Bu, eşleşen sanal çekirdek hizmeti hedefini daha hassas bir şekilde belirlemeyi olanaklı kılar. 

Aşağıdaki yaklaşım, sanal çekirdek modeline geçişten sonra benzer bir performans düzeyi elde etmek için, benzer bir kaynak ayırmaya sahip bir vCore hizmet hedefini belirlemede bu bilgileri kullanır.

### <a name="dtu-to-vcore-mapping"></a>DTU 'dan vCore eşleme

Aşağıdaki bir T-SQL sorgusu, geçirilecek bir DTU veritabanı bağlamında yürütüldüğünde, sanal çekirdek modelindeki her bir donanım üretiminde eşleşen (büyük olasılıkla kesirli) sayıda sanal çekirdek döndürülür. Bu sayıyı, sanal çekirdek modelinde her bir donanım oluşturma işlemi için [veritabanları](resource-limits-vcore-single-databases.md) ve [elastik havuzlar](resource-limits-vcore-elastic-pools.md) için kullanılabilen en yakın sayıda sanal çekirdekte YUVARLAYARAK, kullanıcılar DTU veritabanı veya elastik havuz için en yakın eşleşme olan Vcore hizmeti hedefini seçebilirler. 

Bu yaklaşımı kullanarak örnek geçiş senaryoları [örnekler](#dtu-to-vcore-migration-examples) bölümünde açıklanmıştır.

Bu sorguyu veritabanı yerine geçirilecek veritabanının bağlamında yürütün `master` . Elastik bir havuzu geçirirken, sorguyu havuzdaki herhangi bir veritabanı bağlamında yürütün.

```SQL
WITH dtu_vcore_map AS
(
SELECT rg.slo_name,
       DATABASEPROPERTYEX(DB_NAME(), 'Edition') AS dtu_service_tier,
       CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG7%' THEN 'Gen5'
       END AS dtu_hardware_gen,
       s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
       CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
      AND
      rg.database_id = DB_ID()
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       dtu_service_tier,
       CASE WHEN dtu_service_tier = 'Basic' THEN 'General Purpose'
            WHEN dtu_service_tier = 'Standard' THEN 'General Purpose or Hyperscale'
            WHEN dtu_service_tier = 'Premium' THEN 'Business Critical or Hyperscale'
       END AS vcore_service_tier,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Ek etmenler

Sanal çekirdekler (mantıksal CPU 'Lar) ve donanım oluşturma sayısının yanı sıra, diğer birçok etken de sanal çekirdek hizmeti hedefi seçimini etkileyebilir:

- Eşleme T-SQL sorgusu, DTU ve sanal çekirdek hizmet hedeflerini CPU kapasitesi bakımından eşleştirir, bu nedenle sonuçlar CPU ile bağlantılı iş yükleri için daha doğru olacaktır.
- Aynı donanım oluşturma ve sanal çekirdek veritabanları için aynı sayıda sanal çekirdek, ıOPS ve işlem günlüğü verimlilik kaynak sınırları, DTU veritabanlarının genellikle daha yüksektir. GÇ bağlantılı iş yükleri için, sanal çekirdek modelindeki sanal çekirdek sayısını, aynı performans düzeyine ulaşmak için düşürmek mümkün olabilir. Mutlak değerlerde DTU ve sanal çekirdek veritabanlarının kaynak sınırları [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) görünümünde gösterilir. Bu değerleri geçirilecek DTU veritabanı ile, yaklaşık olarak eşleşen bir hizmet hedefi kullanan bir vCore veritabanı arasında karşılaştırmak, sanal çekirdek hizmeti hedefini daha kesin bir şekilde seçmenize yardımcı olur.
- Eşleme sorgusu Ayrıca, geçirilecek DTU veritabanı veya elastik havuz için çekirdek başına bellek miktarını ve sanal çekirdek modelindeki her donanım oluşturma için de döndürür. Sanal çekirdeğe geçiş sonrasında, büyük bir bellek veri önbelleğinin veya sorgu işleme için büyük bellek izni gerektiren iş yüklerine ihtiyaç duymasını gerektiren iş yükleri için, vCore 'a geçişten sonra benzer veya daha yüksek toplam bellek sağlamak önemlidir. Gerçek performansa bağlı olarak, bu tür iş yükleri için, yeterli miktarda bellek almak üzere sanal çekirdek sayısını artırmak gerekebilir.
- DTU veritabanının [Geçmiş kaynak kullanımı](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , sanal çekirdek hizmeti hedefi seçerken göz önünde bulundurulmalıdır. Sürekli olarak kullanılan CPU kaynaklarıyla DTU veritabanlarının, eşleme sorgusunun döndürdüğü sayıdan daha az sayıda sanal çekirdeğe ihtiyacı olabilir. Buna karşılık, tutarlı olarak yüksek CPU kullanımının yetersiz iş yükü performansına neden olduğu DTU veritabanları, sorgu tarafından döndürülenden daha fazla sanal çekirdek gerektirebilir.
- Veritabanlarını aralıklı veya öngörülemeyen kullanım desenleriyle geçiriyorsanız [sunucusuz](serverless-tier-overview.md) işlem katmanının kullanımını göz önünde bulundurun. Sunucusuz 'ta en fazla eş zamanlı çalışan sayısı (istek), yapılandırılan en fazla sayıda sanal çekirdek için sağlanan işlem sınırının %75 olduğunu unutmayın. Ayrıca, sunucusuz 'ta bulunan en fazla bellek, yapılandırılan maksimum sanal çekirdek sayısına 3 GB olur; Örneğin, en fazla 40 maksimum sanal çekirdek yapılandırıldığında maksimum bellek 120 GB 'dir.   
- Sanal çekirdek modelinde, desteklenen en büyük veritabanı boyutu, donanım oluşturmaya bağlı olarak farklılık gösterebilir. Büyük veritabanları için, [tek veritabanları](resource-limits-vcore-single-databases.md) ve [elastik havuzlar](resource-limits-vcore-elastic-pools.md)için sanal çekirdek modelinde desteklenen en büyük boyutları denetleyin.
- Elastik havuzlar için [DTU](resource-limits-dtu-elastic-pools.md) ve [sanal çekirdek](resource-limits-vcore-elastic-pools.md) modellerinin havuz başına desteklenen en fazla veritabanı sayısına göre farkları vardır. Bu, çok sayıda veritabanına sahip elastik havuzlar geçirilirken göz önünde bulundurulmalıdır.
- Bazı donanım oluşturmaları, her bölgede kullanılamayabilir. [Donanım nesilleri](service-tiers-vcore.md#hardware-generations)altında kullanılabilirliği denetleyin.

> [!IMPORTANT]
> Yukarıdaki vCore boyutlandırma yönergeleri, hedef veritabanı hizmeti hedefinin ilk tahmininin yardımına yardımcı olmak için verilmiştir.
>
> Hedef veritabanının en iyi yapılandırması iş yüküne bağımlıdır. Bu nedenle, geçiş sonrasında en iyi fiyat/performans oranını elde etmek, sanal çekirdekler, [donanım oluşturma](service-tiers-vcore.md#hardware-generations), [hizmet](service-tiers-vcore.md#service-tiers) ve [işlem](service-tiers-vcore.md#compute-tiers) katmanlarının yanı sıra, [en yüksek paralellik derecesi](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)gibi diğer veritabanı yapılandırma parametrelerinin ayarlanmasını sağlamak için Vcore modelinin esnekliğinden yararlanmak isteyebilir.
> 

### <a name="dtu-to-vcore-migration-examples"></a>DTU-sanal çekirdek geçiş örnekleri

> [!NOTE]
> Aşağıdaki örneklerdeki değerler yalnızca çizim amaçlıdır. Açıklanan senaryolarda döndürülen gerçek değerler farklı olabilir.
> 

**Standart S9 veritabanını geçirme**

Eşleme sorgusu aşağıdaki sonucu döndürür (bazı sütunlar breçekimi için gösterilmez):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|5. nesil|5,40|16,800|7|24,000|5,05|

DTU veritabanının, sanal çekirdek başına 5,4 GB bellek ile (vCore), 5. nesil donanımı kullandığını görüyoruz. Doğrudan eşleşmesi, 5. nesil donanımında Genel Amaçlı 24 sanal çekirdek veritabanıdır, yani **GP_Gen5_24** Vcore hizmeti hedefi.

**Standart S0 veritabanını geçirme**

Eşleme sorgusu aşağıdaki sonucu döndürür (bazı sütunlar breçekimi için gösterilmez):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|4. nesil|0,42|0.250|7|0.425|5,05|

DTU veritabanının, vCore başına 0,42 GB bellek ile 0,25 mantıksal CPU 'ların (Vçekirdekler) eşdeğeri olduğunu ve 4. nesil Hardware kullandığını görüyoruz. 4. nesil ve 5. nesil donanım nesilleri içindeki en küçük sanal çekirdek hizmet amaçları, **GP_Gen4_1** ve **GP_Gen5_2**, Standart S0 veritabanından daha fazla işlem kaynağı sağlar, bu nedenle doğrudan eşleşme mümkün değildir. 4. nesil donanımı [kullanımdan](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)olmadığından **GP_Gen5_2** seçeneği tercih edilir. Ayrıca, iş yükü [sunucusuz](serverless-tier-overview.md) işlem katmanına uygun ise, daha yakından bir eşleşme **GP_S_Gen5_1** .

**Premium P15 veritabanını geçirme**

Eşleme sorgusu aşağıdaki sonucu döndürür (bazı sütunlar breçekimi için gösterilmez):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|5. nesil|4,86|29,400|7|42,000|5,05|

DTU veritabanının, vCore başına 4,86 GB bellek ile 42 mantıksal CPU (sanal çekirdek) olduğunu ve 5. nesil Hardware kullandığını görüyoruz. 42 çekirdeklere sahip bir sanal çekirdek hizmet hedefi olmasa da **BC_Gen5_40** hizmet hedefı hem CPU hem de bellek kapasitesi açısından çok yakındır ve iyi bir eşleşmedir.

**Temel 200 eDTU elastik havuzunu geçirme**

Eşleme sorgusu aşağıdaki sonucu döndürür (bazı sütunlar breçekimi için gösterilmez):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4.00|5. nesil|5,40|2,800|7|4,000|5,05|

DTU elastik havuzunda, her vCore için 5,4 GB bellek ve 5. nesil donanım kullandığını belirten 4 mantıksal CPU (sanal çekirdek) olduğunu görüyoruz. VCore modelindeki doğrudan eşleşme **GP_Gen5_4** elastik bir havuzudur. Ancak, bu hizmet hedefi havuz başına en fazla 200 veritabanını destekler, ancak temel 200 eDTU esnek havuzu en fazla 500 veritabanını destekler. Geçirilecek elastik havuzun 200 ' den fazla veritabanı varsa, eşleşen sanal çekirdek hizmeti hedefinin **GP_Gen5_6** olması gerekir ve bu, en fazla 500 veritabanını destekler.

## <a name="migrate-geo-replicated-databases"></a>Coğrafi olarak çoğaltılan veritabanlarını geçirme

DTU tabanlı modelden sanal çekirdek tabanlı satın alma modeline geçiş yapmak, standart ve Premium hizmet katmanlarındaki veritabanları arasında coğrafi çoğaltma ilişkilerini yükseltmekle veya eski sürüme düşürmeye benzer. Geçiş sırasında Coğrafi çoğaltmayı durdurmanız gerekmez, ancak şu sıralama kurallarını izlemeniz gerekir:

- Yükseltme sırasında öncelikle ikincil veritabanını yükseltmeniz ve ardından birincili yükseltmeniz gerekir.
- Eski sürüme düşürme sırasında sırayı tersine çevirin: önce birincil veritabanını indirgemeniz ve sonra ikincili indirgemeniz gerekir.

İki elastik havuz arasında coğrafi çoğaltma kullanırken, bir havuzu birincil ve diğeri, ikincil olarak atamanız önerilir. Bu durumda, elastik havuzları geçirirken aynı sıralama kılavuzunu kullanmanız gerekir. Ancak, hem birincil hem de ikincil veritabanları içeren elastik havuzlarınız varsa, havuzu birincil olarak daha yüksek kullanım ile değerlendirin ve sıralama kurallarını uygun şekilde izleyin.  

Aşağıdaki tablo, belirli geçiş senaryoları için rehberlik sağlar:

|Geçerli hizmet katmanı|Hedef hizmet katmanı|Geçiş türü|Kullanıcı eylemleri|
|---|---|---|---|
|Standart|Genel amaçlı|Geni|Herhangi bir sırada geçirebilir, ancak yukarıda açıklanan şekilde uygun sanal çekirdek boyutlandırmayı sağlamak için gerekir|
|Premium|İş açısından kritik|Geni|Herhangi bir sırada geçirebilir, ancak yukarıda açıklanan şekilde uygun sanal çekirdek boyutlandırmayı sağlamak için gerekir|
|Standart|İş açısından kritik|Yükseltme|Önce ikinciye geçirilmesi gerekiyor|
|İş açısından kritik|Standart|Eski sürüme düşür|Önce birincil önce geçirilmesi gerekir|
|Premium|Genel amaçlı|Eski sürüme düşür|Önce birincil önce geçirilmesi gerekir|
|Genel amaçlı|Premium|Yükseltme|Önce ikinciye geçirilmesi gerekiyor|
|İş açısından kritik|Genel amaçlı|Eski sürüme düşür|Önce birincil önce geçirilmesi gerekir|
|Genel amaçlı|İş açısından kritik|Yükseltme|Önce ikinciye geçirilmesi gerekiyor|
||||

## <a name="migrate-failover-groups"></a>Yük devretme gruplarını geçirme

Birden çok veritabanına sahip yük devretme gruplarının geçirilmesi, birincil ve ikincil veritabanlarının tek tek geçirilmesini gerektirir. Bu işlem sırasında, aynı noktalar ve sıralama kuralları da geçerlidir. Veritabanları sanal çekirdek tabanlı satın alma modeline dönüştürüldükten sonra, yük devretme grubu aynı ilke ayarları ile geçerli olmaya devam edecektir.

### <a name="create-a-geo-replication-secondary-database"></a>Coğrafi çoğaltma ikincil veritabanı oluşturma

Yalnızca birincil veritabanı için kullandığınız hizmet katmanını kullanarak coğrafi çoğaltma ikincil veritabanı (coğrafi-ikincil) oluşturabilirsiniz. Yüksek günlük oluşturma hızına sahip veritabanları için, birincil ile aynı işlem boyutuyla coğrafi ikincil oluşturmanız önerilir.

Tek bir birincil veritabanı için elastik havuzda bir coğrafi ikincil oluşturuyorsanız, `maxVCore` Havuz ayarının birincil veritabanının işlem boyutuyla eşleştiğinden emin olun. Başka bir elastik havuzda birincil için bir coğrafi bölge oluşturuyorsanız, havuzların aynı ayarlara sahip olması önerilir `maxVCore` .

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>DTU 'dan vCore 'a geçiş yapmak için veritabanı kopyasını kullanın

Hedef işlem boyutu, kaynak veritabanının en büyük veritabanı boyutunu desteklediği sürece, DTU tabanlı işlem boyutu olan bir veritabanını, kısıtlama olmadan veya özel sıralamaya sahip bir veritabanı olarak bir veritabanına kopyalayabilirsiniz. Veritabanı kopyalama işlemi, kopyalama işleminin başlangıç saatinden itibaren verilerin bir anlık görüntüsünü oluşturur ve kaynak ile hedef arasında veri eşitlenmez.

## <a name="next-steps"></a>Sonraki adımlar

- Tek veritabanları için kullanılabilen belirli işlem boyutları ve depolama boyutu seçimleri için bkz. [tek veritabanları Için SQL veritabanı sanal çekirdek tabanlı kaynak sınırları](resource-limits-vcore-single-databases.md).
- Elastik havuzlara yönelik belirli işlem boyutları ve depolama boyutu seçimleri için bkz. [elastik havuzlar Için SQL veritabanı sanal çekirdek tabanlı kaynak sınırları](resource-limits-vcore-elastic-pools.md).
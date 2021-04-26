---
title: Yoğun elastik havuzlardaki kaynak yönetimi
description: Azure SQL veritabanı elastik havuzlarındaki işlem kaynaklarını birçok veritabanı ile yönetin.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 40b6c5a86184860cf3e7a9840f980706485ae977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572245"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Yoğun elastik havuzlardaki kaynak yönetimi
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL veritabanı [elastik havuzları](./elastic-pool-overview.md) , farklı kaynak kullanımıyla birçok veritabanını yönetmeye yönelik ekonomik bir çözümdür. Elastik havuzdaki tüm veritabanları CPU, bellek, çalışan iş parçacıkları, depolama alanı, tempdb gibi kaynakların aynı ayırmasını paylaşır, bu da **havuzdaki veritabanlarının yalnızca bir alt kümesinin belirli bir zamanda işlem kaynaklarını kullanacağı** varsayımına dayanır. Bu varsayım, esnek havuzların düşük maliyetli olmasına olanak sağlar. Müşteriler, her veritabanı için potansiyel olarak gerek duyduğu tüm kaynaklar için ödeme yapmak yerine, havuzdaki tüm veritabanları arasında paylaşılan çok daha küçük bir kaynak kümesi için ödeme yapar.

## <a name="resource-governance"></a>Kaynak idaresi

Kaynak paylaşımı, yüksek kaynak tüketimine sahip bir veritabanının aynı elastik havuzdaki diğer veritabanlarını etkilediği "gürültülü komşu" efektini en aza indirmek için sistemin kaynak kullanımını dikkatle denetlemesini gerektirir. Aynı zamanda sistemin, güvenilir bir şekilde çalışması için yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR), yedekleme ve geri yükleme, izleme, sorgu deposu, otomatik ayarlama vb. özellikler için yeterli kaynak sağlaması gerekir.

Azure SQL veritabanı, işlem düzeyi kaynak yönetimine yönelik Windows [Iş nesneleri](/windows/win32/procthread/job-objects) , depolama kotası yönetimi Için Windows [dosya sunucusu Kaynak Yöneticisi (FSRM)](/windows-server/storage/fsrm/fsrm-overview) ve SQL veritabanı içinde kaynak yönetimini uygulamak için SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) değiştirilmiş ve genişletilmiş bir sürümü dahil olmak üzere birden çok kaynak idare mekanizması kullanarak bu hedeflere erişir.

Elastik havuzların birincil tasarım hedefi uygun maliyetli olmalıdır. Bu nedenle, sistem kasıtlı olarak müşterilerin _yoğun_ havuzlar oluşturmalarına olanak tanır. Bu, izin verilen en yüksek düzeyde veya en fazla bir işlem kaynakları ayırması ile gelen veritabanları sayısını içeren havuzlarıdır. Aynı nedenden dolayı, sistem iç işlemlerinde gerekli olabilecek tüm kaynakları ayırmaz, ancak iç süreçler ve Kullanıcı iş yükleri arasında kaynak paylaşımına izin verir.

Bu yaklaşım, müşterilerin yeterli performans ve büyük maliyet tasarrufları elde etmek için yoğun elastik havuzlar kullanmasına olanak sağlar. Ancak, yoğun bir havuzda çok sayıda veritabanına karşı iş yükü yeterince yoğun ise, kaynak çakışması önemli hale gelir. Kaynak Çekişmesi, Kullanıcı iş yükü performansını azaltır ve iç süreçlerini olumsuz yönde etkileyebilir.

> [!IMPORTANT]
> Çok sayıda etkin veritabanına sahip yoğun havuzlarda, havuzdaki veritabanlarının sayısını [DTU](resource-limits-dtu-elastic-pools.md) ve [sanal çekirdek](resource-limits-vcore-elastic-pools.md) elastik havuzlar için belgelenen en yüksek sayıya kadar artırmak mümkün olmayabilir.
>
> Kaynak çekişmesine ve performans sorunlarına yol açmadan yoğun havuzlara yerleştirilebilecek veritabanlarının sayısı, eşzamanlı etkin veritabanlarının sayısına ve her bir veritabanındaki kullanıcı iş yükleri tarafından kaynak tüketimine bağlıdır. Bu sayı, Kullanıcı iş yükleri değiştiğinde zaman içinde değişebilir.

Daha seyrek paketlenmiş bir havuzda kaynak çekişmesi gerçekleştiğinde, müşteriler bunu azaltmak için aşağıdaki eylemlerden birini veya daha fazlasını seçebilir:

- Kaynak tüketimini azaltmak için sorgu iş yükünü ayarlayın veya kaynak tüketimini zaman içinde birden çok veritabanına yayın.
- Bazı veritabanlarını başka bir havuza taşıyarak veya tek başına veritabanları yaparak havuz yoğunluğunu azaltın.
- Daha fazla kaynak almak için havuzu ölçeklendirin.

Son iki eylemin nasıl uygulanacağı hakkında öneriler için, bu makalenin ilerleyen kısımlarında bulunan [işletimsel öneriler](#operational-recommendations) bölümüne bakın. Kaynak çekişmelerinin her ikisi de Kullanıcı iş yüklerini ve iç süreçlerini azaltır ve sistemin beklenen hizmet düzeyini güvenilir bir şekilde korumasını sağlar.

## <a name="monitoring-resource-consumption"></a>İzleme kaynak tüketimi

Kaynak çakışması nedeniyle performans düşüşünü önlemek için yoğun elastik havuzlar kullanan müşterilerin kaynak tüketimini önceden izlemesi ve kaynak çekişmesinin artması iş yüklerini etkilemeye başlarsa işlem yapması gerekir. Kullanıcı iş yükünde yapılan değişiklikler, veri birimlerindeki değişiklikler, havuz yoğunluğu 'ndaki değişiklikler ve Azure SQL veritabanı hizmetindeki değişiklikler nedeniyle, bir havuzdaki kaynak kullanımı zaman içinde değiştiği için sürekli izleme önemlidir.

Azure SQL veritabanı, bu tür izlemelerle ilgili birkaç ölçüm sağlar. Her ölçüm için önerilen ortalama değeri aşmamak, havuzdaki kaynak çekişmesini gösterir ve daha önce bahsedilen eylemlerden biri kullanılarak değinilmesi gerekir.

|Ölçüm adı|Description|Önerilen ortalama değer|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Temel alınan işletim sistemiyle ölçülen bir elastik havuz ile ilişkili SQL işleminin CPU kullanımı. Her veritabanındaki [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) görünümünde ve veritabanındaki [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) görünümünde kullanılabilir `master` . Bu [ölçüm, Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) izleyici 'ye de yayılmaktadır ve burada `sqlserver_process_core_percent` Azure Portal görüntülenir. Bu değer, aynı elastik havuzdaki her veritabanı için aynıdır.|%70 altında. %90 ' e kadar zaman zaman kısa artışlar kabul edilebilir.|
|`max_worker_percent`|[Çalışan iş parçacığı]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) kullanımı. Havuzdaki her veritabanı için ve, havuzun kendisi için de sunulur. Veritabanı düzeyindeki çalışan iş parçacıklarının sayısında ve havuz düzeyinde farklı sınırlamalar vardır. bu nedenle, bu ölçümün her iki düzeyde de izlenmesi önerilir. Her veritabanındaki [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) görünümünde ve veritabanındaki [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) görünümünde kullanılabilir `master` . Bu [ölçüm, Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) izleyici 'ye de yayılmaktadır ve burada `workers_percent` Azure Portal görüntülenir.|%80 altında. %100 ' e kadar olan ani artışlar bağlantı girişimlerinin ve sorguların başarısız olmasına neden olur.|
|`avg_data_io_percent`|Okuma ve yazma fiziksel GÇ için ıOPS kullanımı. Havuzdaki her veritabanı için ve, havuzun kendisi için de sunulur. Veritabanı düzeyindeki ıOPS sayısı ve havuz düzeyinde farklı sınırlamalar vardır, bu nedenle bu ölçümün her iki düzeyde de izlenmesi önerilir. Her veritabanındaki [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) görünümünde ve veritabanındaki [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) görünümünde kullanılabilir `master` . Bu [ölçüm, Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) izleyici 'ye de yayılmaktadır ve burada `physical_data_read_percent` Azure Portal görüntülenir.|%80 altında. %100 ' e kadar zaman zaman kısa artışlar kabul edilebilir.|
|`avg_log_write_percent`|İşlem günlüğü yazma GÇ için üretilen iş yükü belirlemeleri. Havuzdaki her veritabanı için ve, havuzun kendisi için de sunulur. Veritabanı düzeyinde ve havuz düzeyinde günlük verimlilik üzerinde farklı sınırlar vardır, bu nedenle bu ölçümün her iki düzeyde de izlenmesi önerilir. Her veritabanındaki [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) görünümünde ve veritabanındaki [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) görünümünde kullanılabilir `master` . Bu [ölçüm, Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) izleyici 'ye de yayılmaktadır ve burada `log_write_percent` Azure Portal görüntülenir. Bu ölçüm %100 ' e yakın olduğunda, tüm veritabanı değişiklikleri (INSERT, UPDATE, DELETE, MERGE deyimleri, SELECT... INTO, BULK INSERT vb.) daha yavaş olacaktır.|%90 altında. %100 ' e kadar zaman zaman kısa artışlar kabul edilebilir.|
|`oom_per_second`|Bellek baskısı göstergesi olan elastik havuzda bellek dışı (OOM) hatalarının hızı. [Sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) görünümünde kullanılabilir. Bu ölçümü hesaplamak için örnek bir sorgu [örneklerine](#examples) bakın.|0|
|`avg_storage_percent`|Elastik havuz içindeki tüm veritabanlarındaki veriler tarafından kullanılan toplam depolama alanı. Veritabanı dosyalarında boş alan içermez. Veritabanındaki [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) görünümünde kullanılabilir `master` . Bu [ölçüm, Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) izleyici 'ye de yayılmaktadır ve burada `storage_percent` Azure Portal görüntülenir.|%80 altında. Veri büyümesi olmayan havuzlar için %100 yaklaşımda bulunabilir.|
|`avg_allocated_storage_percent`|Elastik havuzdaki tüm veritabanlarında veritabanı dosyaları tarafından kullanılan toplam depolama alanı. Veritabanı dosyalarında boş alan içerir. Veritabanındaki [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) görünümünde kullanılabilir `master` . Bu [ölçüm, Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) izleyici 'ye de yayılmaktadır ve burada `allocated_data_storage_percent` Azure Portal görüntülenir.|%90 altında. Veri büyümesi olmayan havuzlar için %100 yaklaşımda bulunabilir.|
|`tempdb_log_used_percent`|Veritabanındaki işlem günlüğü alanı kullanımı `tempdb` . Bir veritabanında oluşturulan geçici nesneler aynı elastik havuzdaki diğer veritabanlarında görünmese de, `tempdb` Aynı havuzdaki tüm veritabanları için paylaşılan bir kaynaktır. Havuzdaki bir veritabanından başlatılan uzun süre çalışan veya yalnız bırakılmış bir işlem, `tempdb` işlem günlüğünün büyük bir bölümünü kullanabilir ve aynı havuzdaki diğer veritabanlarındaki sorgular için hatalara neden olabilir. [Sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) ve [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) görünümlerinden türetilmiş. Bu ölçüm Ayrıca Azure Izleyici 'ye de yayılır ve Azure portal ' de görüntülenebilir. Bu ölçümün geçerli değerini döndürmek için örnek bir sorgu [örneklerine](#examples) bakın.|%50 altında. %80 ' e kadar zaman zaman artışlar kabul edilebilir.|
|||

Azure SQL veritabanı, bu ölçümlere ek olarak, gerçek kaynak idare sınırlarını döndüren bir görünüm ve kaynak havuzu düzeyinde kaynak kullanımı istatistikleri döndüren ek görünümler ve iş yükü grubu düzeyinde bir görünüm sağlar.

|Görünüm adı|Description|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Geçerli veritabanında veya elastik havuzda kaynak idare mekanizmaları tarafından kullanılan gerçek yapılandırma ve kapasite ayarlarını döndürür.|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Geçerli kaynak havuzu durumu, Kaynak havuzlarının geçerli yapılandırması ve toplu kaynak havuzu istatistikleri hakkındaki bilgileri döndürür.|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Toplu iş yükü grubu istatistiklerini ve iş yükü grubunun geçerli yapılandırmasını döndürür. Bu görünüm, `pool_id` kaynak havuzu bilgilerini almak için sütununda sys.dm_resource_governor_resource_pools katılabilir.|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Son 32 dakika boyunca kaynak havuzu kullanım istatistiklerini döndürür. Her satır 20 saniyelik bir aralığı temsil eder. `delta_`Sütunlar, Aralık sırasında her istatistikteki değişikliği döndürür.|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Son 32 dakika için iş yükü grubu kullanım istatistiklerini döndürür. Her satır 20 saniyelik bir aralığı temsil eder. `delta_`Sütunlar, Aralık sırasında her istatistikteki değişikliği döndürür.|
|||

Bu görünümler, kaynak kullanımını izlemek ve neredeyse gerçek zamanlı kaynak çekişmesini gidermek için kullanılabilir. Coğrafi çoğaltmalar dahil olmak üzere birincil ve okunabilir ikincil çoğaltmalarda Kullanıcı iş yükü, `SloSharedPool1` `UserPrimaryGroup.DBId[N]` `N` veritabanı kimliği değerini temsil eden kaynak havuzu ve iş yükü grubu olarak sınıflandırılmaktadır.

Güncel kaynak kullanımını izlemenin yanı sıra, yoğun havuzlar kullanan müşteriler geçmiş kaynak kullanımı verilerini ayrı bir veri deposunda koruyabilir. Bu veriler, kaynak kullanımını geçmiş ve dönemsel eğilimler temelinde proaktif olarak yönetmek için tahmine dayalı çözümlemede kullanılabilir.

## <a name="operational-recommendations"></a>İşletimsel öneriler

**Yeterli kaynak headodsını bırakın**. Kaynak Çekişmesi ve performans düşüşü gerçekleşirse, risk azaltma, bazı veritabanlarının etkilenen elastik havuzdan dışına taşınmasını veya daha önce belirtildiği gibi havuzu ölçeklendirmesini gerektirebilir. Ancak, bu eylemler için ek işlem kaynaklarının tamamlanmasını gerekir. Özellikle, Premium ve İş Açısından Kritik havuzlarında, bu eylemler, taşınan veritabanları için tüm verilerin veya havuzun ölçeği Azaltılsa, elastik havuzdaki tüm veritabanları için aktarılmasını gerektirir. Veri aktarımı, uzun süre çalışan ve Kaynak yoğunluklu bir işlemdir. Havuz zaten yüksek kaynak baskısı altındaysa, Azaltýcý işlem performansı daha da düşürebilir. Olağanüstü durumlarda, gerekli kaynaklar kullanılamadığından veritabanı taşıma veya havuz ölçeği aracılığıyla kaynak çekişmesini çözümlemek mümkün olmayabilir. Bu durumda, etkilenen elastik havuzdaki sorgu iş yükünü geçici olarak düşürmek tek çözüm olabilir.

Yoğun havuzlar kullanan müşteriler, daha önce açıklandığı gibi kaynak kullanımı eğilimlerini yakından izlemelidir ve ölçümler önerilen aralıklar dahilinde kaldığı ve elastik havuzda yeterli kaynak olduğundan, bu işlemleri azaltma işlemi gerçekleştirin.

Kaynak kullanımı, her bir veritabanı ve elastik havuz için zaman içinde değişen birden çok etkene bağlıdır. Yoğun havuzlardaki en iyi fiyat/performans oranının sağlanması sürekli izleme ve yeniden dengeleme gerektirir, bu da veritabanlarını daha fazla kullanılan havuzlardan daha az kullanılan havuzlara taşıyor ve artan iş yükünü karşılamak için gereken yeni havuzlar oluşturuyor.

**"Sık erişimli" veritabanlarını taşımayın**. Havuz düzeyindeki kaynak çekişmesinin birincil olarak az sayıda çok kullanılan veritabanı olması nedeniyle, bu veritabanlarını daha az kullanılan bir havuza taşımak veya tek başına veritabanları yapmak mümkündür. Ancak, bunun yapılması, bir veritabanı yüksek düzeyde kullanılabilir kaldığı sürece bunu yapmanız önerilmez, çünkü taşıma işlemi, hem veritabanı hem de tüm havuz için performansı düşürür. Bunun yerine, yüksek kullanım alt taraflarına kadar bekleyin veya havuz düzeyinde kaynak basıncını sağlamak yerine daha az kullanılan veritabanlarını taşıyın. Ancak, çok düşük kullanımı olan veritabanlarının taşınması, havuz düzeyinde kaynak kullanımını önemli ölçüde azalmadığından, bu durumda herhangi bir avantaj sağlamaz.

**"Karantina" havuzunda yeni veritabanları oluşturun**. Veritabanı başına kiracı modeli kullanan uygulamalar gibi, sıklıkla yeni veritabanlarının oluşturulduğu senaryolarda, mevcut bir elastik havuza yerleştirilmiş yeni bir veritabanının beklenmedik şekilde önemli kaynakları tüketmesi ve havuzdaki diğer veritabanlarını ve iç süreçlerini etkilemesi gerekir. Bu riski azaltmak için, kaynakların çok fazla ayrılması ile ayrı bir "Karantina" havuzu oluşturun. Henüz bilinmeyen kaynak tüketim desenleriyle bu havuzu yeni veritabanları için kullanın. Bir veritabanı, hafta veya ay gibi bir iş çevrimi için bu havuzda bir kez daha olduğunda ve kaynak tüketimi bilindiğinde, bu ek kaynak kullanımına uyum sağlamak için yeterli kapasiteye sahip bir havuza taşınabilir.

**Kullanılan ve ayrılan alanı izleyin**. Ayrılmış havuz alanı (bir havuzdaki tüm veritabanları için depolamada bulunan tüm veritabanı dosyalarının toplam boyutu) en yüksek havuz boyutuna ulaşırsa, boş alan hataları oluşabilir. Ayrılan alan eğilimleri yüksekse ve en büyük havuz boyutuna ulaşmak için izse, risk azaltma seçenekleri şunlardır:
- Toplam ayrılan alanı azaltmak için bazı veritabanlarını havuzdan dışarı taşıyın
- Dosyalardaki boş ayrılmış alanı azaltmak için veritabanı dosyalarını [küçültün](file-space-manage.md)
- Havuzun boyutunu daha büyük olan en büyük havuz boyutuyla bir hizmet hedefine ölçeklendirin

Kullanılan havuz alanı (bir havuzdaki tüm veritabanlarındaki toplam veri boyutu) eğilimleri yüksek ve en büyük havuz boyutuna ulaşmak için izlerse, azaltma seçenekleri şunlardır:
- Toplam kullanılan alanı azaltmak için bazı veritabanlarını havuza taşıyın
- Verileri veritabanının dışına taşı veya artık gerekli olmayan verileri Sil
- [Veri sıkıştırmayı](/sql/relational-databases/data-compression/data-compression) Uygula
- Havuzun boyutunu daha büyük olan en büyük havuz boyutuyla bir hizmet hedefine ölçeklendirin

**Aşırı yoğun sunuculardan kaçının**. Azure SQL veritabanı, sunucu başına en fazla 5000 veritabanını [destekler](./resource-limits-logical-server.md) . Binlerce veritabanı içeren elastik havuzlar kullanan müşteriler, tek bir sunucuya birden çok elastik havuz yerleştirmeyi göz önünde bulundurarak desteklenen sınıra kadar toplam veritabanı sayısına sahip olabilir. Ancak, binlerce veritabanına sahip sunucular işlemsel zorluk oluşturur. Bir sunucudaki tüm veritabanlarının (örneğin, portaldaki veritabanlarını görüntüleme) listelenmesi gereken işlemler daha yavaş olacaktır. Sunucu düzeyinde oturum açma işlemleri veya güvenlik duvarı kuralları için yanlış değişiklik gibi işletimsel hatalar, daha fazla sayıda veritabanını etkileyecektir. Sunucu yanlışlıkla silinmesinin, silinen sunucuda veritabanlarını kurtarmak için Microsoft Desteği 'den yardım gerekir ve etkilenen tüm veritabanları için uzun sürmemiş bir kesinti oluşmasına neden olur.

Sunucu başına veritabanı sayısını desteklenenden daha düşük bir sayıyla sınırlamanızı öneririz. Birçok senaryoda, sunucu başına en fazla 1000-2000 veritabanı kullanılması idealdir. Yanlışlıkla sunucu silme olasılığını azaltmak için sunucuda veya kaynak grubunda bir [silme kilidi](../../azure-resource-manager/management/lock-resources.md) yerleştirmenizi öneririz.

Geçmişte, veritabanlarının aynı sunucu üzerindeki elastik havuzlar arasında, dışarı veya dışarı taşınmasını kapsayan bazı senaryolar veritabanlarını sunucular arasında taşırken daha hızlıdır. Şu anda, kaynak ve hedef sunucudan bağımsız olarak tüm veritabanı aynı hızda yürütülür.

## <a name="examples"></a>Örnekler

### <a name="monitoring-memory-utilization"></a>Bellek kullanımını izleme

Bu sorgu `oom_per_second` , son 32 dakika içinde her kaynak havuzu için ölçüyü hesaplar. Bu sorgu, elastik havuzdaki herhangi bir veritabanında yürütülebilir.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>`tempdb`Günlük alanı kullanımını izleme

Bu sorgu `tempdb_log_used_percent` , `tempdb` işlem günlüğünün izin verilen en büyük boyutuna göre göreli kullanımını gösteren ölçümün geçerli değerini döndürür. Bu sorgu, elastik havuzdaki herhangi bir veritabanında yürütülebilir.

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>Sonraki adımlar

- Elastik havuzlara giriş için bkz. [elastik havuzlar Azure SQL veritabanı 'nda birden çok veritabanını yönetmenize ve ölçeklendirmenize yardımcı olur](./elastic-pool-overview.md).
- Kaynak kullanımını azaltmak üzere sorgu iş yüklerini ayarlama hakkında daha fazla bilgi için bkz. [izleme ve ayarlama]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index), [izleme ve performans ayarlama](./monitor-tune-overview.md).
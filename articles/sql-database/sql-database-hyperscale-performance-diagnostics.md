---
title: Hiper ölçekte Performans Tanılama
description: Bu makalede, Azure SQL veritabanı 'nda hiper ölçekli performans sorunlarının nasıl giderileceği açıklanmaktadır.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: a7c64284c958fa8b3ec89c2b27515fe167a04011
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811141"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL hiper ölçek performans sorunlarını giderme tanılaması


Hiper ölçekli bir veritabanında performans sorunlarını gidermek için, Azure SQL veritabanı işlem düğümündeki [genel performans ayarlama yöntemleri](sql-database-monitor-tune-overview.md) , performans araştırmasının başlangıç noktasıdır. Ancak, Hyperscale 'nin [Dağıtılmış mimarisi](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) verildiğinde yardım 'a ek Tanılamalar eklenmiştir. Bu makalede, hiper ölçeğe özgü Tanılama verileri açıklanmaktadır.


## <a name="log-rate-throttling-waits"></a>Günlük hızı azaltma bekler


Her Azure SQL veritabanı hizmet düzeyinde günlük oluşturma oranı sınırlamaları, [günlük hızı](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)yönetimi aracılığıyla uygulanır. Hiper ölçekte, günlük oluşturma sınırı hizmet düzeyinden bağımsız olarak şu anda 100 MB/sn olarak ayarlanmıştır. Ancak, birincil işlem çoğaltmasındaki günlük oluşturma hızının, kurtarılabilirlik sağlamak için kısıtlandığı durumlar vardır. Bu kısıtlama, bir [sayfa sunucusu veya başka bir işlem çoğaltması](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) , günlük hizmetinden yeni günlük kayıtlarının uygulanması önemli ölçüde geride olduğunda gerçekleşir.

Aşağıdaki bekleme türleri ( [sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)' de), günlük hızının birincil işlem çoğaltmasında nasıl kısıtlanamadığına ilişkin nedenleri anlatmaktadır:

|Bekleme türü    |Açıklama                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Bir hiper ölçek veritabanı birincil işlem düğümü günlük oluşturma hızı, sayfa sunucusunda gecikmeli günlük kullanımı nedeniyle kısıtlanmakta olduğunda gerçekleşir.         |
|RBIO_RG_DESTAGE        | Bir hiper ölçek veritabanı işlem düğüm günlüğü oluşturma hızı, uzun süreli günlük depolama tarafından Gecikmeli günlük tüketimine karşı kısıtlanmakta olduğunda gerçekleşir.         |
|RBIO_RG_REPLICA        | Bir hiper ölçek veritabanı işlem düğüm günlüğü oluşturma hızı, okunabilir ikincil çoğaltmalar tarafından Gecikmeli günlük tüketimine karşı kısıtlanmakta olduğunda gerçekleşir.         |
|RBIO_RG_LOCALDESTAGE   | Bir hiper ölçek veritabanı işlem düğüm günlüğü oluşturma hızı, günlük hizmeti tarafından Gecikmeli günlük tüketimine karşı kısıtlanmakta olduğunda gerçekleşir.         |


## <a name="page-server-reads"></a>Sayfa sunucusu okuma

İşlem çoğaltmaları veritabanının tam bir kopyasını yerel olarak önbelleğe vermez. İşlem çoğaltmasındaki yerel veriler, arabellek havuzunda (bellekte) ve veri sayfalarının kısmi (kapsayan olmayan) önbelleği olan yerel dayanıklı arabellek havuzu uzantısı (RBPEX) önbelleğinde depolanır. Bu yerel RBPEX önbelleği, işlem boyutuna orantılı olarak boyutlandırılır ve işlem katmanının belleği 3 zamandadır. RBPEX, en sık erişilen verileri içeren arabellek havuzuna benzerdir. Diğer taraftaki her sayfa sunucusunda, tuttuğu veritabanının bölümü için kapsayan bir RBPEX önbelleği vardır.
 
Bir işlem çoğaltmasında okuma yapıldığında, veriler arabellek havuzunda veya yerel RBPEX önbelleğinde yoksa bir getPage (PageId, LSN) işlev çağrısı verilir ve sayfa ilgili sayfa sunucusundan getirilir. Sayfa sunucularından gelen okumalar uzaktan okumalardır ve bu nedenle yerel RBPEX 'dan okumalarından daha yavaştır. GÇ ile ilgili performans sorunları giderirken, görece yavaş uzak sayfa sunucusu okumaları ile kaç tane IOs yapıldığını anladık.

Birçok DMVs ve genişletilmiş olay, bir sayfa sunucusundan gelen ve toplam okumaların karşılaştırılabileceği uzaktan okuma sayısını belirten sütunlara ve alanlara sahiptir. 

- Sayfa sunucusu okumalarını raporlamak için sütunlar, yürütme DMVs 'de mevcuttur, örneğin:
    - [sys. dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys. dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys. dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys. dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
- Sayfa sunucusu okuma aşağıdaki genişletilmiş olaylara eklenir:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - sorgu-store_execution_runtime_info
- Gerçek planlar için sorgu planı XML 'e Actualpageserverokumaları/ActualPageServerReadAheads eklenir. Örneğin:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Bu öznitelikleri SSMS 'de sorgu planı özellikleri penceresinde görüntülemek için SSMS 18,3 veya üzeri bir sürüme ihtiyacınız olacaktır.


## <a name="virtual-file-stats-and-io-accounting"></a>Sanal dosya Istatistikleri ve GÇ hesabı

Azure SQL veritabanı 'nda, [sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) dmf, SQL Server GÇ izlemenin birincil yoludur. Hiper ölçekte GÇ özellikleri, [Dağıtılmış mimarisi](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)nedeniyle farklıdır. Bu bölümde, bu DMF 'de görüldüğü gibi veri dosyalarına GÇ 'ye (okuma ve yazma) odaklanıyoruz. Hiper ölçekte, bu DMF 'de görünür olan her veri dosyası uzak bir sayfa sunucusuna karşılık gelir. Burada bahsedilen RBPEX önbelleği, işlem çoğaltmasındaki kapsayan olmayan bir önbellek olan yerel bir SSD tabanlı önbelleğidir.


### <a name="local-rbpex-cache-usage"></a>Yerel RBPEX önbelleği kullanımı

Yerel RBPEX önbelleği, yerel SSD depolamada işlem düğümünde mevcuttur. Bu nedenle, bu RBPEX önbelleğindeki GÇ, uzak sayfa sunucularındaki GÇ 'den daha hızlıdır. Şu anda, bir hiper ölçek veritabanında bulunan [sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) , işlem çoğaltmasındaki yerel RBPEX ÖNBELLEĞINDE gerçekleştirilen GÇ 'yi bildiren özel bir satıra sahiptir. Bu satırda hem `database_id` hem de `file_id` sütunları için 0 değeri bulunur. Örneğin, aşağıdaki sorgu, veritabanının başlamasından itibaren RBPEX kullanım istatistikleri döndürüyor.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Tüm diğer veri dosyalarında yapılan toplanmış okumaların, RBPEX üzerinde yapılan okumaların oranı, RBPEX isabetli önbellek okuması oranını sağlar.


### <a name="data-reads"></a>Veri okuma

- Okuma SQL Server altyapısı tarafından bir işlem çoğaltması üzerinde verildiğinde, bunlar yerel RBPEX önbelleği veya uzak sayfa sunucuları tarafından ya da birden çok sayfa okunıyorsa ikisinin bir birleşimi tarafından sunulabilir.
- İşlem çoğaltması belirli bir dosyadaki bazı sayfaları okuduğunda, örneğin file_id 1, bu veriler yalnızca yerel RBPEX önbelleğinde bulunuyorsa, bu okuma için tüm GÇ 'ler file_id 0 (RBPEX) ile hesaba katılmaz. Bu verilerin bir bölümü yerel RBPEX önbelleğinde ise ve bazı bölümleri uzak sayfa sunucusunda ise, bu durumda, RBPEX 'tan sunulan bölüm için GÇ file_id ve uzak sayfa sunucusundan sunulan Bölüm file_id 1 ' e doğru bir şekilde hesaba katılmaz. 
- Bir işlem çoğaltması bir sayfa sunucusundan belirli bir [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) 'nin bir sayfasını istediğinde, sayfa sunucusu istenen LSN 'ye bakmadığında, işlem çoğaltmasındaki okuma işlemi sayfa sunucusu işlem çoğaltmasına döndürülmeden önce yakalanana kadar bekler. İşlem çoğaltmasında bir sayfa sunucusundan okunan her türlü, bu GÇ üzerinde bekliyorsa, PAGEIOLATCH_ * bekleme türünü görürsünüz. Bu bekleme süresi, sayfa sunucusundaki istenen sayfayı gereken LSN 'ye ve sayfa sunucusundan bilgi işlem çoğaltmasına aktarmak için gereken süreyi de içerir.
- Okuma sonrası gibi büyük okumalar genellikle ["dağılım-toplama" okumaları](/sql/relational-databases/reading-pages/)kullanılarak yapılır. Bu, SQL Server altyapısında tek bir okuma olarak kabul edilen, her seferinde 4 MB 'a kadar sayfa okumalarını sağlar. Öte yandan, okunan veriler RBPEX olduğunda, arabellek havuzu ve RBPEX her zaman 8 KB sayfa kullandığından, bu okumalar birden çok bireysel 8 KB okuma olarak hesaba katılmaz. Sonuç olarak, RBPEX 'ye karşı görülen okuma IOs sayısı, altyapı tarafından gerçekleştirilen gerçek IOs sayısından daha büyük olabilir.


### <a name="data-writes"></a>Veri yazma

- Birincil işlem çoğaltması doğrudan sayfa sunucularına yazmaz. Bunun yerine, günlük hizmetindeki günlük kayıtları ilgili sayfa sunucularında yeniden yürütülür. 
- İşlem çoğaltmasında gerçekleşen yazma işlemleri, yerel RBPEX (file_id 0) ağırlıklı. 8 KB 'den büyük olan mantıksal dosyalardaki yazma işlemleri için, her yazma işlemi, bellek havuzu ve RBPEX her zaman 8 KB sayfa kullandığından, her yazma [işlemi,](/sql/relational-databases/writing-pages/)rbpex 'e birden fazla 8 KB 'a çevrilir. Sonuç olarak, RBPEX 'ye karşı görülen yazma IOs sayısı, altyapı tarafından gerçekleştirilen gerçek IOs sayısından daha büyük olabilir.
- RBPEX dosyaları veya sayfa sunucularına karşılık gelen file_id 0 dışında bir veri dosyası, aynı zamanda yazma işlemlerini gösterir. Hiper ölçek hizmet katmanında, işlem çoğaltmaları hiçbir şekilde doğrudan sayfa sunucularına yazmadığından bu yazma 'lar benzetilir. Yazma ıOPS ve aktarım hızı, işlem çoğaltmasında gerçekleştikleri şekilde hesaba katılmaz, ancak file_id 0 dışında bir veri dosyası gecikmesi sayfa sunucusu yazma işleminin gerçek gecikmesini yansıtmaz.

### <a name="log-writes"></a>Günlük yazma Işlemleri

- Birincil işlem sırasında, file_id 2 ' nin sys. dm_io_virtual_file_stats ' de bir günlük yazma işlemi hesaba katılmaz. Birincil işlem üzerine bir günlük yazma, günlük giriş bölgesine bir yazma işlemi olur.
- Günlük kayıtları, bir işlemede ikincil çoğaltmada sağlamlaştırılmış değildir. Hiper ölçekte, günlük xlog hizmeti tarafından uzak yinelemelere uygulanır. Günlük yazma işlemleri ikincil çoğaltmalarda gerçekten gerçekleşmediğinden, ikincil çoğaltmalarda tüm günlük GÇ işlemleri yalnızca izleme amaçlıdır.

## <a name="additional-resources"></a>Ek Kaynaklar

- Tek bir hiper ölçekli tek bir veritabanı için Vcore kaynak sınırları için bkz. [hiper ölçekli Service Tier Vcore limitleri](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- Azure SQL veritabanı performans ayarlaması için bkz. [Azure SQL veritabanı 'Nda sorgu performansı](sql-database-performance-guidance.md)
- Sorgu deposu kullanarak performans ayarlaması için bkz. [sorgu deposu kullanarak performans izleme](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- DMV İzleme betikleri için bkz. [dinamik yönetim görünümlerini kullanarak performansı Izleme Azure SQL veritabanı](sql-database-monitoring-with-dmvs.md)

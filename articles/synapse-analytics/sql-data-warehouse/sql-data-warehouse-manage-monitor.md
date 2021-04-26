---
title: DMVs kullanarak adanmış SQL havuzu iş yükünüzü izleme
description: Azure SYNAPSE Analytics adanmış SQL havuzu iş yükünüzü ve DMVs kullanarak sorgu yürütmeyi izlemeyi öğrenin.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 62064eaae6aa7fb3438845170497035473227d30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685217"
---
# <a name="monitor-your-azure-synapse-analytics-dedicated-sql-pool-workload-using-dmvs"></a>DMVs kullanarak Azure SYNAPSE Analytics adanmış SQL havuzu iş yükünüzü izleme

Bu makalede, SQL havuzunda sorgu yürütmeyi araştırma dahil olmak üzere iş yükünüzü izlemek için dinamik yönetim görünümlerinin (DMVs) nasıl kullanılacağı açıklanır.

## <a name="permissions"></a>İzinler

Bu makaledeki DMV 'leri sorgulamak için, **VERITABANı durumunu** veya **Denetim** iznini görüntüleme izninizin olması gerekir. Genellikle, **Görünüm VERITABANı durumunu** vermek, çok daha kısıtlayıcı olduğundan tercih edilen izindir.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Bağlantıları izleme

Veri ambarınızdaki tüm oturumlar [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)için günlüğe kaydedilir.  Bu DMV, son 10.000 oturum açma bilgilerini içerir.  Session_id birincil anahtardır ve her yeni oturum açma işlemi için ardışık olarak atanır.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Sorgu yürütmeyi izleme

SQL havuzunda yürütülen tüm sorgular [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)kaydedilir.  Bu DMV, yürütülen son 10.000 sorguyu içerir.  Request_id her sorguyu benzersiz bir şekilde tanımlar ve bu DMV için birincil anahtardır.  Request_id her yeni sorgu için ardışık olarak atanır ve sorgu KIMLIĞINI temsil eden QıD öneki vardır.  Belirli bir session_id için bu DMV sorgulaması, belirli bir oturum açma için tüm sorguları gösterir.

> [!NOTE]
> Saklı yordamlar birden çok Istek kimliği kullanır.  İstek kimlikleri sıralı sırayla atanır.

Belirli bir sorgu için sorgu yürütme planlarını ve saatlerini araştırmak üzere izlenecek adımlar aşağıda verilmiştir.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>1. Adım: araştırmak istediğiniz sorguyu tanımla

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

Yukarıdaki sorgu sonuçlarından, araştırmak istediğiniz sorgunun **Istek kimliğini unutmayın** .

**Askıya alınmış** durumdaki sorgular, çok sayıda etkin çalışan sorgu nedeniyle sıraya alınabilir. Bu sorgular, bir tür UserConcurrencyResourceType bekleyen sorgu [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) de görüntülenir. Eşzamanlılık limitleri hakkında daha fazla bilgi için bkz. [iş yükü yönetimi Için](resource-classes-for-workload-management.md) [bellek ve eşzamanlılık sınırları](memory-concurrency-limits.md) veya kaynak sınıfları. Sorgular, nesne kilitleri gibi diğer nedenleri de bekleyebilir.  Sorgunuz bir kaynağı bekliyorsa, bu makalede daha fazla kaynak [için bekleyen sorguları araştırma](#monitor-waiting-queries) bölümüne bakın.

[Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) tablosundaki bir sorgunun aramasını basitleştirmek için, sorgunuza bir açıklama atamak için [etiketi](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) kullanın ve bu, sys.dm_pdw_exec_requests görünümünde aranabilir.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>2. Adım: sorgu planını araştırın

Sorgunun dağıtılmış SQL (DSQL) planını [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) almak IÇIN istek kimliğini kullanın

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Bir DSQL planı beklenenden uzun sürüyorsa, nedeni çok sayıda DSQL adımı olan karmaşık bir plan veya uzun süren bir adım olabilir.  Plan birkaç taşıma işlemiyle ilgili birçok adım ise, veri hareketini azaltmak için tablo dağıtımlarınızı en iyi duruma getirme işlemini göz önünde bulundurun. [Tablo dağıtım](sql-data-warehouse-tables-distribute.md) makalesinde, bir sorguyu çözümlemek için verilerin neden taşınması gerektiği açıklanmaktadır. Makalede veri hareketini en aza indirmek için bazı dağıtım stratejileri de açıklanmaktadır.

Tek bir adım hakkında daha fazla ayrıntı araştırmak için, uzun süreli sorgu adımının *operation_type* sütunu ve **adım dizini**' ni aklınızda koyun:

* **SQL işlemleri** için adım 3 ile devam edin: Onoperation, Remoteoperation, ReturnOperation.
* **Veri taşıma işlemleri** için 4. adım ile devam edin: karıştırılmış Limoveoperation, Yayınmoveoperation, Kırmoveoperation, Partitionmoveoperation, Moveoperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>3. Adım: dağıtılmış veritabanlarında SQL 'i araştırın

Dağıtılmış veritabanlarının tümünde sorgu adımının yürütme bilgilerini içeren [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)ayrıntılarını almak IÇIN istek kimliği ve adım dizinini kullanın.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Sorgu adımı çalışırken, belirli bir dağıtım üzerinde çalışan adımın SQL Server plan önbelleğinden tahmini SQL Server planı almak için [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) kullanılabilir.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>4. Adım: dağıtılmış veritabanlarında veri hareketini araştırın

[Sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)'ten her bir dağıtım üzerinde çalışan bir veri taşıma adımı hakkında bilgi almak IÇIN istek kimliği ve adım dizinini kullanın.

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Belirli bir dağıtımın, veri taşıma için diğerlerinden önemli ölçüde uzun sürdüğünü görmek için *total_elapsed_time* sütununa bakın.
* Uzun süre çalışan dağıtım için, bu dağılıktan taşınan satır sayısının diğerlerinden önemli ölçüde daha büyük olup olmadığını görmek için *rows_processed* sütununu kontrol edin. Bu durumda, bu bulma, temel alınan verilerin eğriliğini gösterebilir. Veri eğimiyle ilgili bir nedeni, çok sayıda NULL değeri olan (satırları aynı dağıtım içinde olacak) bir sütuna dağıtılıyor. Bu tür sütunlarda dağıtımı önleyin veya mümkünse null değerleri ortadan kaldırmak için sorgunuzu filtreleyerek yavaş sorguları engelleyin. 

Sorgu çalışıyorsa, belirli bir dağıtım içinde çalışmakta olan SQL adımının SQL Server plan önbelleğinden tahmini SQL Server planı almak için [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) kullanabilirsiniz.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Bekleyen sorguları izle

Sorgunuzun bir kaynak beklediği için ilerleme göstermediği fark ederseniz, bir sorgunun beklediği tüm kaynakları gösteren bir sorgu aşağıda verilmiştir.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Sorgu başka bir sorgudan kaynak üzerinde etkin bir şekilde bekliyorsa, durum **AcquireResources** olur.  Sorgunun tüm gerekli kaynakları varsa, durum **verilecektir**.

## <a name="monitor-tempdb"></a>Tempdb 'yi izleme

Tempdb sorgu yürütülürken ara sonuçları tutmak için kullanılır. Tempdb veritabanının yüksek kullanımı yavaş sorgu performansına yol açabilir. Yapılandırılan her DW100c için 399 GB tempdb alanı ayrılır (DW1000c toplam tempdb alanı 3,99 TB olur).  Aşağıda, tempdb kullanımını izlemeye ve sorgularınızdaki tempdb kullanımını düşürmeye yönelik ipuçları verilmiştir.

### <a name="monitoring-tempdb-with-views"></a>Tempdb 'yi görünümlerle izleme

Tempdb kullanımını izlemek için önce [SQL havuzu Için Microsoft araç seti](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring)' nden [Microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) görünümünü yüklemeniz gerekir. Ardından, yürütülen tüm sorgular için düğüm başına tempdb kullanımını görmek üzere aşağıdaki sorguyu yürütebilirsiniz:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Büyük miktarda bellek kullanan veya tempdb 'nin ayrılmasına ilişkin bir hata mesajı almış bir sorgunuz varsa, bu, [Select (CTAS) olarak](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) çok büyük bir create table veya son veri taşıma işleminde başarısız olan [Select](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ifadesini çalıştıran bir hata nedeniyle olabilir. Bu, genellikle son ekleme seçmeden önce dağıtılmış sorgu planında bir karıştırılmış Lemove işlemi olarak tanımlanabilir.  Karışık taşıma işlemlerini izlemek için [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) kullanın.

En yaygın risk azaltma, CTAS 'larınızı bölmek veya SELECT deyimini birden çok yük ifadesine eklemek için veri hacmi, düğüm başına 1 TB 'lık sınırı aşmayacak. Ayrıca, her bir düğümdeki tempdb 'yi azaltan daha fazla düğüm üzerinde tempdb boyutunu yayabilecek daha büyük bir boyuta ölçeklendirebilirsiniz.

CTAS ve INSERT SELECT deyimlerinin yanı sıra, yetersiz bellekle çalışan karmaşık sorgular tempdb içine taşıtabilecek ve sorguların başarısız olmasına neden olabilir.  Tempdb 'ye taşmamak için daha büyük bir [kaynak sınıfıyla](resource-classes-for-workload-management.md) çalışmayı düşünün.

## <a name="monitor-memory"></a>Belleği izleme

Bellek, yavaş performans ve yetersiz bellek sorunları için kök neden olabilir. Sorgu yürütme sırasında sınırlarına ulaşarak SQL Server bellek kullanımı bulursanız veri Ambarınızı ölçeklendirin.

Aşağıdaki sorgu, düğüm başına SQL Server bellek kullanımı ve bellek baskısı döndürür:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>İşlem günlüğü boyutunu izle

Aşağıdaki sorgu her bir dağıtımında işlem günlüğü boyutunu döndürür. Günlük dosyalarından biri 160 GB 'a ulaşıldığında, örneğinizi ölçeklendirmenizi veya işlem boyutunuzu sınırlandırmanız gerekir.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>İzleme işlem günlüğü geri alma

Sorgularınız başarısız olursa veya devam etmek uzun sürerse, geri alma işlemleri varsa denetleyebilir ve izleyebilirsiniz.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>PolyBase yükünü izle

Aşağıdaki sorgu, yüklerinizin ilerleme durumunun yaklaşık bir tahminini sağlar. Sorgu yalnızca işlenmekte olan dosyaları gösterir.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Sonraki adımlar

DMVs hakkında daha fazla bilgi için bkz. [Sistem görünümleri](../sql/reference-tsql-system-views.md).

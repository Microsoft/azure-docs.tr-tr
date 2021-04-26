---
title: DMVs kullanarak performansı izleme
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Microsoft Azure SQL Veritabanı ve Azure SQL yönetilen örneğini izlemek için dinamik yönetim görünümlerini kullanarak sık karşılaşılan performans sorunlarını algılamayı ve tanılamayı öğrenin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/15/2021
ms.openlocfilehash: 5c0de2c1589bfa495ab6ad287b998c403041674c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592175"
---
# <a name="monitoring-microsoft-azure-sql-database-and-azure-sql-managed-instance-performance-using-dynamic-management-views"></a>Dinamik yönetim görünümlerini kullanarak Microsoft Azure SQL Veritabanı'nın ve Azure SQL Yönetilen Örneği'nin performansını izleme
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Microsoft Azure SQL Veritabanı ve Azure SQL yönetilen örneği, dinamik yönetim görünümlerinin bir alt kümesini, Engellenen veya uzun süreli sorgular, kaynak darboğazları, kötü sorgu planları vb. nedeniyle oluşan performans sorunlarını tanılamak için etkinleştirir. Bu makalede, dinamik yönetim görünümlerini kullanarak yaygın performans sorunlarının algılanması hakkında bilgi verilmektedir.

Microsoft Azure SQL Veritabanı ve Azure SQL yönetilen örneği, dinamik yönetim görünümlerinin üç kategorisini kısmen destekler:

- Veritabanıyla ilgili dinamik yönetim görünümleri.
- Yürütmeye ilişkin dinamik yönetim görünümleri.
- İşlemle ilgili dinamik yönetim görünümleri.

Dinamik yönetim görünümleri hakkında ayrıntılı bilgi için bkz. [dinamik yönetim görünümleri ve işlevleri (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

## <a name="monitor-with-sql-insights"></a>SQL Insights ile izleme

Azure [IZLEYICI SQL öngörüleri](../../azure-monitor/insights/sql-insights-overview.md) , Azure SQL VM 'lerinde Azure SQL yönetilen örnekleri, Azure SQL veritabanları ve SQL Server örneklerini izlemeye yönelik bir araçtır. Bu hizmet, dinamik yönetim görünümlerindeki (DMVs) verileri yakalamak ve verileri Azure Log Analytics yönlendirdiğinde, burada izlenebileceğiniz ve çözümlenebileceği bir uzak Aracı kullanır. Bu verileri, [Azure izleyici](../../azure-monitor/overview.md) 'den belirtilen görünümlerde görüntüleyebilir veya sorguları çalıştırmak ve eğilimleri çözümlemek için günlük verilerine doğrudan erişebilirsiniz. Azure Izleyici SQL öngörülerini kullanmaya başlamak için bkz. [SQL Insights 'ı etkinleştirme](../../azure-monitor/insights/sql-insights-enable.md).

## <a name="permissions"></a>İzinler

Azure SQL veritabanı 'nda, dinamik bir yönetim görünümünü sorgulamak için **VERITABANı durumunu görüntüle** izinleri gerekir. **VERITABANı durumunu görüntüle** izni, geçerli veritabanı içindeki tüm nesneler hakkında bilgi döndürür.
Belirli bir veritabanı kullanıcısına **VERITABANı durumunu görüntüle** iznini vermek için aşağıdaki sorguyu çalıştırın:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

Azure SQL yönetilen örneği 'nde, dinamik bir yönetim görünümünü sorgulamak için **sunucu durumu Izinlerinin görünüm** olması gerekir. Daha fazla bilgi için bkz. [sistem dinamik yönetim görünümleri](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views#required-permissions).

SQL Server örneğinde ve Azure SQL yönetilen örneği 'nde, dinamik yönetim görünümleri sunucu durum bilgilerini döndürür. Azure SQL veritabanı 'nda yalnızca geçerli mantıksal veritabanınızla ilgili bilgileri döndürür.

Bu makale, aşağıdaki sorgu performans sorunlarının türlerini algılamak için SQL Server Management Studio veya Azure Data Studio kullanarak yürütebilmeniz gereken DMV sorgularının bir koleksiyonunu içerir:

- [Aşırı CPU tüketimiyle ilgili sorguları tanımlama](#identify-cpu-performance-issues)
- [PAGELATCH_ * ve WRITE_LOG, GÇ sorunları ile ilgili olarak bekler](#identify-io-performance-issues)
- [PAGELATCH_ *, beklediği bir Ttempdb çekişmesine neden oldu](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE bellek verme nedeniyle oluşan sorunları bekliyor](#identify-memory-grant-wait-performance-issues)
- [Veritabanı ve nesne boyutlarını tanımlama](#calculating-database-and-objects-sizes)
- [Etkin oturumlar hakkında bilgi alma](#monitoring-connections)
- [Sistem genelinde ve veritabanı kaynağı kullanım bilgilerini alma](#monitor-resource-use)
- [Sorgu performans bilgileri alınıyor](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>CPU performansı sorunlarını belirleme

CPU tüketimi, uzun süre boyunca %80 üzerinde ise, aşağıdaki sorun giderme adımlarını göz önünde bulundurun:

### <a name="the-cpu-issue-is-occurring-now"></a>CPU sorunu şimdi gerçekleşiyor

Sorun şu anda gerçekleşirse, iki olası senaryo vardır:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Yüksek CPU kullanan çok sayıda tekil sorgu

En üstteki sorgu karmalarını belirlemek için aşağıdaki sorguyu kullanın:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>CPU kullanan uzun süre çalışan sorgular hala çalışıyor

Bu sorguları tanımlamak için aşağıdaki sorguyu kullanın:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>Geçmişte CPU sorunu oluştu

Sorun geçmişte oluştuysa ve kök neden çözümlemesi yapmak istiyorsanız [sorgu deposu](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)' nu kullanın. Veritabanı erişimi olan kullanıcılar, sorgu deposu verilerini sorgulamak için T-SQL kullanabilir. Sorgu deposu varsayılan yapılandırması 1 saat ayrıntı düzeyi kullanır. Yüksek CPU kullanan sorgulara yönelik etkinliklere bakmak için aşağıdaki sorguyu kullanın. Bu sorgu, en üstteki 15 CPU kullanan sorguları döndürür. Değiştirmeyi unutmayın `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()` :

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Sorunlu sorguları tanımladıktan sonra, CPU kullanımını azaltmak için bu sorguları ayarlamaya zaman atalım.  Sorguları ayarlamaya yönelik bir zaman yoksa, bu sorunu geçici olarak çözmek için veritabanının SLO 'yu yükseltmeyi de tercih edebilirsiniz.

## <a name="identify-io-performance-issues"></a>GÇ performans sorunlarını tanımla

GÇ performans sorunlarını belirlerken, GÇ sorunlarıyla ilişkilendirilmiş en önemli bekleme türleri şunlardır:

- `PAGEIOLATCH_*`

  Veri dosyası GÇ sorunları (, `PAGEIOLATCH_SH` `PAGEIOLATCH_EX` ,, dahil `PAGEIOLATCH_UP` ) için.  Bekleme türü adında **GÇ** varsa, bir GÇ sorununa işaret eder. Sayfa mandal bekleme adında **GÇ** yoksa, farklı bir sorun türüne (örneğin, tempdb çekişmesi) işaret eder.

- `WRITE_LOG`

  İşlem günlüğü GÇ sorunları için.

### <a name="if-the-io-issue-is-occurring-right-now"></a>GÇ sorunu şu anda gerçekleşirse

Ve ' i görmek için [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) veya [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) kullanın `wait_type` `wait_time` .

#### <a name="identify-data-and-log-io-usage"></a>Veri ve günlük GÇ kullanımını tanımla

Veri ve günlük GÇ kullanımını belirlemek için aşağıdaki sorguyu kullanın. Veri veya günlük GÇ %80 ' den fazla ise, kullanıcılar SQL veritabanı hizmet katmanı için kullanılabilir GÇ 'yi kullandık.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

GÇ sınırına ulaşıldığında, iki seçeneğiniz vardır:

- 1\. Seçenek: İşlem boyutunu veya hizmet katmanını yükseltin
- 2. seçenek: en fazla GÇ kullanan sorguları belirleyip ayarlayın.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Sorgu deposunu kullanarak arabelleğin ilgili GÇ görüntüleme

2. seçenek için, izlenen etkinliğin son iki saatini görüntülemek üzere arabelleğin ilgili GÇ için sorgu deposunda aşağıdaki sorguyu kullanabilirsiniz:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>WRITELOG Wait için toplam günlük GÇ görüntüleme

Bekleme türü ise `WRITELOG` , toplam log IO for bildirisini görüntülemek için aşağıdaki sorguyu kullanın:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>`tempdb`Performans sorunlarını tanımla

GÇ performans sorunlarını tanımlarken, sorunlarla ilişkili en üstteki bekleme türleri `tempdb` `PAGELATCH_*` (değil `PAGEIOLATCH_*` ) olur. Ancak, `PAGELATCH_*` beklemeleri her zaman çekişmeye sahip olduğunuz anlamına gelmez `tempdb` .  Bu bekleme aynı veri sayfasını hedefleyen eşzamanlı isteklerden dolayı kullanıcı-nesne veri sayfası çekişmesiyle karşılaştığınızı gösteriyor da olabilir. Çekişmeyi daha fazla onaylamak için `tempdb` [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) kullanarak wait_resource değerin, `2:x:y` 2 `tempdb` ' nin VERITABANı kimliği olduğu, `x` dosya kimliği ve `y` sayfa kimliği olduğunu doğrulamak için kullanın.  

Tempdb çekişmesi için ortak bir yöntem, ' i temel alan uygulama kodunu azaltmaktır veya yeniden yazmaktır `tempdb` .  Ortak `tempdb` kullanım alanlarında şunlar bulunur:

- Geçici tablolar
- Tablo değişkenleri
- Tablo değerli parametreler
- Sürüm deposu kullanımı (uzun süren işlemlerle ilişkili)
- Sıralama, karma ile birleştirme ve biriktirici kullanan sorgu planlarına sahip sorgular

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Tablo değişkenlerini ve geçici tabloları kullanan popüler sorgular

Tablo değişkenlerini ve geçici tabloları kullanan en iyi sorguları belirlemek için aşağıdaki sorguyu kullanın:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Uzun süre çalışan işlemleri tanımla

Uzun süre çalışan işlemleri tanımlamak için aşağıdaki sorguyu kullanın. Uzun süre çalışan işlemler, sürüm deposunu temizlemeyi önler.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Bellek verme bekleme performans sorunlarını tanımla

En iyi bekleme türü ise `RESOURCE_SEMAHPORE` ve yüksek CPU kullanım sorununuz yoksa, bekleyen bir bellek verme sorunu olabilir.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>`RESOURCE_SEMAHPORE`Bekleme en çok bekleme olup olmadığını belirleme

Bekleme en çok bekleme olup olmadığını anlamak için aşağıdaki sorguyu kullanın `RESOURCE_SEMAHPORE`

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identify-high-memory-consuming-statements"></a>Yüksek bellek kullanan deyimleri tanımla

Yüksek bellek kullanan deyimleri belirlemek için aşağıdaki sorguyu kullanın:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>İlk 10 etkin bellek verdiğini tanımla

En iyi 10 etkin belleği veren izinleri tanımlamak için aşağıdaki sorguyu kullanın:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Veritabanı ve nesne boyutlarını hesaplama

Aşağıdaki sorgu veritabanınızın boyutunu döndürür (megabayt cinsinden):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Aşağıdaki sorgu veritabanınızdaki tek tek nesnelerin boyutunu döndürür (megabayt cinsinden):

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>İzleme bağlantıları

Belirli bir sunucuya ve yönetilen örneğe ve her bir bağlantının ayrıntılarına kurulan bağlantılar hakkında bilgi almak için [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) görünümünü kullanabilirsiniz. Ayrıca, [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) görünümü tüm etkin kullanıcı bağlantıları ve iç görevler hakkında bilgi alırken yararlı olur.

Aşağıdaki sorgu geçerli bağlantı hakkındaki bilgileri alır:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> **Sys.dm_exec_requests** ve **sys.dm_exec_sessions görünümleri** YÜRÜTÜRKEN, veritabanında **veritabanı durumunu görüntüle** izniniz varsa, veritabanında yürütülen tüm oturumları görürsünüz; Aksi takdirde, yalnızca geçerli oturumu görürsünüz.

## <a name="monitor-resource-use"></a>Kaynak kullanımını izleme

[SQL veritabanı sorgu performansı içgörüleri](query-performance-insight-use.md)kullanarak Azure SQL veritabanı kaynak kullanımı 'nı izleyebilirsiniz. Azure SQL veritabanı ve Azure SQL yönetilen örneği için [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)'u kullanarak izleyebilirsiniz.

Ayrıca, bu görünümleri kullanarak kullanımı izleyebilirsiniz:

- Azure SQL veritabanı: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- Azure SQL yönetilen örneği: [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database)
- Hem Azure SQL veritabanı hem de Azure SQL yönetilen örneği: [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

Her veritabanında [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) görünümünü kullanabilirsiniz. **Sys.dm_db_resource_stats** görünümü, hizmet katmanına göre son kaynak kullanım verilerini gösterir. CPU, veri GÇ, günlük yazma ve bellek için Ortalama yüzdeler, 15 saniyede bir kaydedilir ve 1 saat boyunca sürdürülür.

Bu görünüm, kaynak kullanımına daha ayrıntılı bir bakış sağladığından, geçerli durum analizi veya sorun giderme için önce **sys.dm_db_resource_stats** kullanın. Örneğin, bu sorgu, son saat içindeki geçerli veritabanı için Ortalama ve en yüksek kaynak kullanımını gösterir:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Diğer sorgular için [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)örneklere bakın.

### <a name="sysserver_resource_stats"></a>sys.server_resource_stats

Azure SQL yönetilen örneği için CPU kullanımı, GÇ ve depolama verilerini döndürmek üzere [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database) kullanabilirsiniz. Veriler, beş dakikalık aralıklar içinde toplanır ve toplanır. 15 saniyelik her raporlama için bir satır vardır. Döndürülen veriler CPU kullanımı, depolama boyutu, GÇ kullanımı ve yönetilen örnek SKU 'SU içerir. Geçmiş verileri yaklaşık 14 gün boyunca tutulur.

```sql
DECLARE @s datetime;  
DECLARE @e datetime;  
SET @s= DateAdd(d,-7,GetUTCDate());  
SET @e= GETUTCDATE();  
SELECT resource_name, AVG(avg_cpu_percent) AS Average_Compute_Utilization
FROM sys.server_resource_stats
WHERE start_time BETWEEN @s AND @e  
GROUP BY resource_name  
HAVING AVG(avg_cpu_percent) >= 80;
```

### <a name="sysresource_stats"></a>sys.resource_stats

**Ana** veritabanındaki [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) görünümünde, veritabanınızın performansını belirli hizmet katmanında ve işlem boyutuyla izlemenize yardımcı olabilecek ek bilgiler bulunur. Veriler her 5 dakikada bir toplanır ve yaklaşık 14 gün boyunca korunur. Bu görünüm, veritabanınızın kaynakları nasıl kullandığına yönelik daha uzun vadeli geçmiş analizler için yararlıdır.

Aşağıdaki grafikte, bir haftada her saat için P2 işlem boyutuyla bir Premium veritabanı için CPU kaynak kullanımı gösterilmektedir. Bu grafik Pazartesi günü başlar, beş iş günü gösterir ve uygulamada çok daha az gerçekleştiğinde bir hafta sonu gösterir.

![Veritabanı kaynak kullanımı](./media/monitoring-with-dmvs/sql_db_resource_utilization.png)

Bu veritabanında Şu anda, P2 işlem boyutuna (Salı günü Orta gün) göre yüzde 50 ' luk CPU 'nun en yüksek CPU yükü vardır. CPU, uygulamanın kaynak profilinde baskın bir faktörse, iş yükünün her zaman uygun olmasını sağlamak için P2 doğru işlem boyutu olduğuna karar verebilirsiniz. Bir uygulamanın zaman içinde büyümesini bekleseniz, uygulamanın performans düzeyi sınırına ulaşmaması için ek bir kaynak arabelleğinin olması iyi bir fikirdir. İşlem boyutunu artırdıysanız, bir veritabanı istekleri etkili bir şekilde işlemek için yeterli güce sahip olmadığında, özellikle de gecikme süresine duyarlı ortamlarda oluşabilecek, müşteri tarafından görünen hatalardan kaçınmanıza yardımcı olabilirsiniz. Veritabanı çağrılarının sonuçlarına göre Web sayfalarını boyayan bir uygulamayı destekleyen bir veritabanıdır.

Diğer uygulama türleri aynı grafiği farklı şekilde yorumlayabilir. Örneğin, bir uygulama her gün Bordro verilerini işlemeye çalışırsa ve aynı grafiğe sahipse, bu tür "Batch işi" modeli P1 işlem boyutunda ince bir işlem gösterebilir. P1 işlem boyutunun, P2 işlem boyutundaki 200 DTU ile karşılaştırıldığında 100 DTU vardır. P1 işlem boyutu, P2 işlem boyutu performansının yarısını sağlar. Bu nedenle, P2 cinsinden CPU kullanımı yüzdesi ' nde 50, P1 ' de yüzde 100 CPU kullanımı. Uygulamanın zaman aşımları yoksa, bugün yapılacağından, bir işin 2 saat veya 2,5 saat sürer. Bu kategorideki bir uygulama büyük olasılıkla P1 işlem boyutunu kullanabilir. Kaynak kullanımı düşük olduğunda gün içinde zaman dilimi olduğunu düşündüğünde, "büyük tepe" nın, gün içinde Troughs daha sonraki bir arasında taşma süresi vardır. P1 işlem boyutu, işlerin her gün bitebilmesi koşuluyla, bu tür bir uygulama (ve tasarruf) için uygun olabilir.

Veritabanı altyapısı her bir etkin veritabanı için tüketilen kaynak bilgilerini her bir sunucuda **ana** veritabanının **sys.resource_stats** görünümünde gösterir. Tablodaki veriler 5 dakikalık aralıklar için toplanır. Temel, standart ve Premium hizmet katmanlarıyla, verilerin tabloda görünmesi 5 dakikadan uzun sürebilir, bu nedenle bu veriler, neredeyse gerçek zamanlı analizler yerine geçmiş analize yönelik daha kullanışlı olabilir. Bir veritabanının son geçmişini görmek ve seçtiğiniz rezervasyonun gerektiğinde istediğiniz performansı teslim edilip edilmeyeceğini doğrulamak için **sys.resource_stats** görünümünü sorgulayın.

> [!NOTE]
> Azure SQL veritabanı 'nda aşağıdaki örneklerde **sys.resource_stats** sorgulamak için **ana** veritabanına bağlı olmanız gerekir.

Bu örnek, bu görünümdeki verilerin nasıl sunulduğunu gösterir:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC;
```

![Sys.resource_stats Catalog görünümü](./media/monitoring-with-dmvs/sys_resource_stats.png)

Sonraki örnekte, veritabanınızın kaynakları nasıl kullandığı hakkında bilgi almak için **sys.resource_stats** katalog görünümünü kullanmanın farklı yolları gösterilmektedir:

1. Veritabanı userdb1 için geçen haftaki kaynak kullanımına bakmak için bu sorguyu çalıştırabilirsiniz:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. İş yükünüzün işlem boyutuna ne kadar iyi uyduğunu değerlendirmek için, kaynak ölçümlerinin her bir yönüyle ayrıntıya inmelisiniz: CPU, okuma, yazma, çalışan sayısı ve oturum sayısı. Bu kaynak ölçümlerinin ortalama ve en büyük değerlerini raporlamak için **sys.resource_stats** kullanan düzeltilmiş bir sorgu aşağıda verilmiştir:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Her kaynak ölçüsünün ortalama ve en yüksek değerleriyle ilgili bu bilgilerle, iş yükünüzün seçtiğiniz işlem boyutuna ne kadar iyi uyduğunu değerlendirebilirsiniz. Genellikle **sys.resource_stats** içindeki ortalama değerler, hedef boyutunda kullanmak için iyi bir taban çizgisi sağlar. Bu, birincil ölçüm çubuğu olmalıdır. Bir örnek için, S2 işlem boyutuyla standart hizmet katmanını kullanıyor olabilirsiniz. CPU ve GÇ okuma ve yazma işlemleri için Ortalama kullanım yüzdesi %40, ortalama çalışan sayısı 50 ' in altında ve ortalama oturum sayısı 200 ' in altında. İş yükünüz, S1 işlem boyutuna uygun hale gelebilir. Veritabanınızın çalışan ve oturum sınırlarına uygun olup olmadığını kolayca görebilirsiniz. Bir veritabanının CPU, okuma ve yazma işlemlerinde daha düşük bir işlem boyutuna uygun olup olmadığını görmek için, düşük işlem boyutunun DTU numarasını geçerli işlem boyutunuzu DTU numarası ile bölün ve sonucu 100 ile çarpın:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    Sonuç, yüzde cinsinden iki işlem boyutu arasındaki göreli performans farklılığı. Kaynak kullanımı bu miktarı aşmazsa, iş yükünüz daha düşük işlem boyutuna sığabilecek. Bununla birlikte, tüm kaynak kullanım değerleri aralıklarına bakmanız ve veritabanı iş yükünüzün daha düşük bilgi işlem boyutuna ne sıklıkta uyduğunu belirlemeniz gerekir. Aşağıdaki sorgu, bu örnekte hesaplandığımız %40 eşiğine bağlı olarak kaynak boyutu başına sığdırma yüzdesini verir:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Veritabanı hizmet katmanınıza göre, iş yükünüzün daha düşük işlem boyutuna uygun olup olmadığına karar verebilirsiniz. Veritabanı iş yükü hedefiniz yüzde 99,9 ise ve önceki sorgu üç kaynak boyutu için yüzde 99,9 ' den daha büyük değerler döndürürse, iş yükünüz büyük olasılıkla daha düşük bilgi işlem boyutuna uyum altına ar.

    Aynı yüzdeyi göz altına almak, amacınızı karşılamak için bir sonraki daha yüksek işlem boyutuna geçmeniz gerekip gerekmediğini de size fikir verir. Örneğin, userdb1, önceki hafta için aşağıdaki CPU kullanımını gösterir:

   | Ortalama CPU yüzdesi | En yüksek CPU yüzdesi |
   | --- | --- |
   | 24,5 |100,00 |

    Ortalama CPU, işlem boyutu sınırının bir çeyreği ile ilgilidir ve veritabanının işlem boyutuna da uyum sağlayacak. Ancak en büyük değer, veritabanının işlem boyutu sınırına ulaştığını gösterir. Sonraki daha yüksek işlem boyutuna geçmeniz gerekiyor mu? İş yükünüzün kaç kez yüzde 100 ' e ulaştığını inceleyin ve ardından veritabanı iş yükü hedefiniz ile karşılaştırın.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Bu sorgu, üç kaynak boyutundan herhangi biri için yüzde 99,9 ' dan düşük bir değer döndürürse, sonraki daha yüksek işlem boyutuna geçmeyi ya da veritabanı üzerindeki yükü azaltmak için uygulama ayarlama tekniklerini kullanmayı düşünün.

4. Bu alıştırma, gelecekte öngörülen iş yükünüzün artışını de dikkate alır.

Elastik havuzlar için bu bölümde açıklanan olan tekniklerle havuzda bulunan tek veritabanlarını izleyebilirsiniz. Ancak havuzu bir bütün olarak da izleyebilirsiniz. Bilgi için bkz. [Elastik havuz izleme ve yönetme](elastic-pool-overview.md).

### <a name="maximum-concurrent-requests"></a>Maksimum eşzamanlı istek sayısı

Eşzamanlı isteklerin sayısını görmek için, bu Transact-SQL sorgusunu veritabanınızda çalıştırın:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Bir SQL Server veritabanının iş yükünü çözümlemek için, bu sorguyu çözümlemek istediğiniz belirli veritabanına göre filtrelemek üzere değiştirin. Örneğin, MyDatabase adlı bir şirket içi veritabanınız varsa, bu Transact-SQL sorgusu söz konusu veritabanındaki eşzamanlı isteklerin sayısını döndürür:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Bu, tek bir noktadaki yalnızca bir anlık görüntüdür. İş yükünüzü ve eşzamanlı istek gereksinimlerinizi daha iyi anlamak için zaman içinde birçok örnek toplamanız gerekir.

### <a name="maximum-concurrent-logins"></a>Maksimum eşzamanlı oturum açma

Kullanıcı ve uygulama desenlerinizi çözümleyerek oturum açma sıklığının bir fikrini alabilirsiniz. Ayrıca, bu makalede tartıştığımız bu veya diğer sınırlara ulaştığınızdan emin olmak için bir test ortamında gerçek dünya yüklerini çalıştırabilirsiniz. Eşzamanlı oturum açma sayısını veya geçmişi gösterebilmeyen tek bir sorgu veya dinamik yönetim görünümü (DMV) yoktur.

Birden çok istemci aynı bağlantı dizesini kullanıyorsa, hizmet her oturum açmanın kimliğini doğrular. 10 Kullanıcı aynı anda bir veritabanına aynı Kullanıcı adı ve parola kullanarak bağlanırsa, 10 eşzamanlı oturum açma işlemleri olur. Bu sınır yalnızca oturum açma ve kimlik doğrulama süresi boyunca geçerlidir. Aynı 10 kullanıcının veritabanına sırayla bağlanması halinde, eşzamanlı oturum açma sayısı 1 ' den büyük olmaz.

> [!NOTE]
> Şu anda, bu sınır elastik havuzlardaki veritabanları için geçerli değildir.

### <a name="maximum-sessions"></a>En fazla oturum sayısı

Geçerli etkin oturumların sayısını görmek için, bu Transact-SQL sorgusunu veritabanınızda çalıştırın:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections;
```

Bir SQL Server iş yükünü analiz ediyorsanız, sorguyu belirli bir veritabanına odaklanmak üzere değiştirin. Bu sorgu, Azure 'a taşımayı düşünüyorsanız veritabanına yönelik olası oturum ihtiyaçlarını belirlemenize yardımcı olur.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase';
```

Yine, bu sorgular bir zaman noktası sayısı döndürür. Zaman içinde birden çok örnek topluyorsanız, oturum kullanımı en iyi şekilde öğrenirsiniz.

[Sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) görünümünü sorgulayarak ve **active_session_count** sütununu inceleyerek oturumlardaki geçmiş istatistiklerini alabilirsiniz.

## <a name="monitoring-query-performance"></a>Sorgu performansını izleme

Yavaş veya uzun süre çalışan sorgular önemli sistem kaynaklarını kullanabilir. Bu bölümde, yaygın olarak karşılaşılan bazı sorgu performans sorunlarını algılamak için dinamik yönetim görünümlerinin nasıl kullanılacağı gösterilmektedir.

### <a name="finding-top-n-queries"></a>İlk N sorguyu bulma

Aşağıdaki örnek, ortalama CPU zamanına göre derecelendirilen ilk beş sorgu hakkında bilgi döndürür. Bu örnek, sorguları kendi sorgu karmalarına göre toplar, böylece mantıksal olarak eşdeğer sorguların birikimli kaynak tüketimine göre gruplanmasıdır.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Engellenen sorguları izleme

Yavaş veya uzun süre çalışan sorgular aşırı kaynak tüketimine katkıda bulunabilir ve engellenen sorguların sonucu olabilir. Engellemenin nedeni kötü uygulama tasarımı, hatalı sorgu planları, faydalı dizinlerin bulunmaması vb. olabilir. Veritabanındaki geçerli kilitleme etkinliği hakkında bilgi almak için sys.dm_tran_locks görünümünü kullanabilirsiniz. Örnek kod için bkz. [sys.dm_tran_locks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql). Engelleme sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure SQL engelleme sorunlarını anlama ve çözme](understand-resolve-blocking.md).

### <a name="monitoring-query-plans"></a>Sorgu planlarını izleme

Verimsiz bir sorgu planı, CPU tüketimini de artırabilir. Aşağıdaki örnek, hangi sorgunun en birikimli CPU 'YU kullandığını belirleyen [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql) görünümünü kullanır.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Ayrıca bkz.

[Azure SQL veritabanı ve Azure SQL yönetilen örneği 'ne giriş](sql-database-paas-overview.md)
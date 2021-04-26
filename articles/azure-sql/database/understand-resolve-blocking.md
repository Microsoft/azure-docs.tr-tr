---
title: Azure SQL engelleme sorunlarını anlama ve çözme
titleSuffix: Azure SQL Database
description: Engelleme ve sorun giderme konusunda Azure SQL veritabanı 'na özgü konulara genel bakış.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 3/02/2021
ms.openlocfilehash: e176c0399b191c7a511ea1d26388219b2cef1df8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107155"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Azure SQL veritabanı engelleme sorunlarını anlama ve çözme
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Amaç

Makale, Azure SQL veritabanlarında engellemeyi açıklar ve engellemeyi nasıl giderebileceğinizi ve çözeceğinizi gösterir. 

Bu makalede, bağlantı terimi, veritabanının tek bir oturum açma oturumu anlamına gelir. Her bağlantı, bir oturum KIMLIĞI (SPID) veya birçok DMVs session_id olarak görüntülenir. Bu SPID 'lerin her biri genellikle işlem olarak adlandırılır, ancak her zamanki anlamda ayrı bir işlem bağlamı değildir. Bunun yerine, her SPID belirli bir istemciden tek bir bağlantının isteklerine hizmet vermek için gereken sunucu kaynaklarından ve veri yapılarından oluşur. Tek bir istemci uygulamasının bir veya daha fazla bağlantısı olabilir. Azure SQL veritabanı perspektifinden, tek bir istemci bilgisayardaki tek bir istemci uygulamasından gelen birden çok bağlantı ve birden çok istemci uygulamasından veya birden çok istemci bilgisayardan birden çok bağlantı arasında bir fark yoktur; Bunlar atomik. Bir bağlantı, kaynak istemciden bağımsız olarak başka bir bağlantıyı engelleyebilirler.

> [!NOTE]
> **Bu içerik Azure SQL veritabanı ' na odaklanılmıştır.** Azure SQL veritabanı, Microsoft SQL Server veritabanı altyapısının en son kararlı sürümünü temel alır. bu nedenle, sorun giderme seçenekleri ve araçları farklı olabilir. SQL Server engelleme hakkında daha fazla bilgi için bkz. [SQL Server engelleyen sorunları anlama ve çözme](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>Engellemeyi anlama 
 
Engelleme, kilit tabanlı eşzamanlılık ile herhangi bir ilişkisel veritabanı yönetim sisteminin (RDBMS), kaçınılmaz ve tasarım dışı bir özelliğidir. Daha önce belirtildiği gibi, SQL Server, bir oturum belirli bir kaynak üzerinde bir kilit tuttuğunda ve ikinci bir SPID aynı kaynakta çakışan bir kilit türü edinmeyi denediğinde meydana gelir. Genellikle, ilk SPID 'nin kaynağı kilitlediği zaman dilimi küçüktür. Sahip olan oturum kilidi serbest bıraktığında ikinci bağlantı, kaynak üzerinde kendi kilidini elde etmek ve işlemeye devam etmek için ücretsizdir. Bu normal davranıştır ve sistem performansı üzerinde belirgin bir etkiye sahip olmak üzere bir gün boyunca birçok kez gerçekleşmeyebilir.

Bir sorgunun süresi ve işlem bağlamı, kilitlerin ne kadar süreyle tutulacağını ve bu nedenle diğer sorgularda etkilerini tespit edilir. Sorgu bir işlem içinde yürütülemez (ve hiçbir kilit ipucu kullanılmazsa), SELECT deyimlerinin kilitleri, sorgu sırasında değil, gerçekten okunmakta olduğu sırada bir kaynakta tutulur. INSERT, UPDATE ve DELETE deyimleri için, kilitler sorgu sırasında, hem veri tutarlılığı hem de gerektiğinde sorgunun geri alınmasına izin vermek için tutulur.

Bir işlem içinde yürütülen sorgular için kilitlerin tutulduğu süre sorgu türü, işlem yalıtım düzeyi ve sorgu üzerinde kilit ipuçlarının kullanılıp kullanılmayacağını belirler. Kilitleme, kilit ipuçları ve işlem yalıtım düzeylerinin açıklaması için aşağıdaki makalelere bakın:

* [Veritabanı Altyapısında Kilitleme](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Kilitleme ve satır sürüm oluşturmayı özelleştirme](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Kilit modları](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Kilit uyumluluğu](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [İşlemler](/sql/t-sql/language-elements/transactions-transact-sql)

Kilitleme ve engelleme, sistem performansı üzerinde bir etkisi olduğu noktada devam ediyorsa, bunun nedeni aşağıdaki nedenlerden biridir:

* SPID, serbest bırakmadan önce uzun bir süre boyunca bir dizi kaynak üzerinde kilit barındırır. Bu tür bir engelleme, kendisini zaman içinde çözer ancak performansın düşmesine neden olabilir.

* Bir SPID bir kaynak kümesi üzerinde kilitler barındırır ve bu kaynakları hiçbir şekilde serbest bırakır. Bu tür bir engelleme kendisini çözmez ve etkilenen kaynaklara süresiz olarak erişimi engeller.

İlk senaryoda, farklı SPID 'ler zaman içinde farklı kaynaklarda engellemeye neden olan bir hareketli hedef oluşturarak durum oldukça akıcı olabilir. Bu durumlar, sorunu tek tek sorgulara daraltmak için [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) kullanarak sorun gidermeye zordur. Buna karşılık ikinci durum, tanılamaya daha kolay olabilecek tutarlı bir durum ile sonuçlanır.

## <a name="applications-and-blocking"></a>Uygulamalar ve engelleme

Engelleyici bir sorunla karşılaştığındaki sunucu tarafı ayarlama ve platform sorunlarına odaklanmanız gerekebilir. Bununla birlikte, yalnızca veritabanı için ödemeli bir uyarı, bir çözüme neden olmayabilir ve istemci uygulamasını ve gönderdiği sorguları inceleyerek, zaman ve enerji daha iyi bir şekilde artışlarını devralarak sağlayabilir. Uygulama, yapılan veritabanı çağrılarına ilişkin olarak hangi düzeyde görünürlük olduğuna bakılmaksızın, bir engelleyici sorun Nonetheless genellikle uygulama tarafından gönderilen tam SQL deyimlerinin incelemesinin ve uygulamanın sorgu iptali, bağlantı yönetimi, tüm sonuç satırları getirme gibi tam davranışı gerekir. Geliştirme aracı bağlantı yönetimi, sorgu iptali, sorgu zaman aşımı, sonuç getirme vb. üzerinde açık denetime izin vermezse, engelleme sorunları çözülebilir olmayabilir. Bu muhtemel, özellikle performans duyarlı OLTP ortamları için Azure SQL veritabanı için bir uygulama geliştirme aracı seçmeden önce yakından incelenmelidir. 

Veritabanı ve uygulamanın tasarım ve oluşturma aşamasında veritabanı performansına dikkat edin. Özellikle, kaynak tüketimi, yalıtım düzeyi ve işlem yolu uzunluğu her sorgu için değerlendirilmelidir. Her sorgu ve işlem mümkün olduğunca hafif olmalıdır. İyi bağlantı yönetimi disiplinler, bu olmadan uygulamanız gerekir, ancak uygulama düşük sayıda kullanıcı için kabul edilebilir olarak görünebilir, ancak kullanıcı sayısı yukarı doğru ölçeklendirilirken performans önemli ölçüde düşebilir. 

Uygun uygulama ve sorgu tasarımıyla Azure SQL veritabanı, çok sayıda eşzamanlı kullanıcıyı tek bir sunucuda destekleyerek çok az engellemeye sahiptir.

> [!Note]
> Daha fazla uygulama geliştirme Kılavuzu için bkz. [bağlantı sorunlarını giderme ve Azure SQL veritabanı ile diğer hatalar ve Azure SQL yönetilen örneği](troubleshoot-common-errors-issues.md) Ile [geçici hata işleme](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Engelleme sorunlarını giderme

Hangi engelleme durumunda yaptığımız olursa olsun, kilitleme sorunlarını giderme yöntemi aynıdır. Bu mantıksal ayırmalar, bu makalenin geri kalanını dikte edecektir. Bu kavram, baş engelleyicisini bulmasının yanı sıra sorgunun ne yaptığını ve neden engellediğini belirler. Sorunlu sorgu tanımlandıktan sonra (diğer bir deyişle, uzun süre boyunca kilitleri tutan), bir sonraki adım çözümlenme ve engellemenin neden meydana gelmediğini belirlemektir. Nedenini anladığımızda sorguyu ve işlemi yeniden tasarlayarak değişiklik yapabiliriz.

Sorun giderme adımları:

1. Ana engelleme oturumunu (baş engelleyici) tanımla

2. Engellemeye neden olan sorgu ve işlemi bulun (bir zaman aralığı için kilitleri tutan)

3. Uzun süren engellemenin neden oluştuğunu çözümleyin/anlayın

4. Sorguyu ve işlemi yeniden tasarlayarak engelleme sorununu çözün

Şimdi, ana engelleme oturumunun uygun bir veri yakalamayla nasıl ele alınacağını tartışalım.

## <a name="gather-blocking-information"></a>Engelleme bilgilerini topla

Engelleyici sorunları gidermeye yönelik zorluklar için bir veritabanı yöneticisi, Azure SQL veritabanında kilitleme ve engelleme durumunu sürekli olarak izleyen SQL betikleri kullanabilir. Bu verileri toplamak için temelde iki yöntem vardır. 

Birincisi, dinamik yönetim nesneleri 'ni (dmo 'lar) sorgulamak ve sonuçları zamana göre karşılaştırmak için depolar. Bu makalede başvurulan bazı nesneler dinamik yönetim görünümleridir (DMVs) ve bazıları dinamik yönetim işlevleridir (DMFs). İkinci yöntem, yürütülen öğeleri yakalamak için XEvents kullanmaktır. 


## <a name="gather-information-from-dmvs"></a>DMV 'lerden bilgi toplayın

Gider sorunlarını gidermek için, blok zincirinin ve SQL bildiriminin başındaki SPID 'yi (oturum KIMLIĞI) belirleme hedefi vardır. Engellenmekte olan kurbanı SPID 'yi arayın. Herhangi bir SPID başka bir SPID tarafından engelleniyorsa, kaynağın sahip olduğu SPID 'yi araştırın (engelleyen SPID). Bu sahip SPID aynı zamanda engellensin mi? Baş engelleyicisini bulmak için zincirine kılavuzluk edebilir, sonra kilidinin nerede olduğunu araştırabilirsiniz.

Bu betiklerin her birini hedef Azure SQL veritabanında çalıştırmayı unutmayın.

* Sp_who ve sp_who2 komutları tüm geçerli oturumları göstermek için eski komutlardır. DMV sys.dm_exec_sessions, sorgu ve filtre uygulama açısından daha kolay bir sonuç kümesinde daha fazla veri döndürür. Diğer sorguların çekirdeğnde sys.dm_exec_sessions bulacaksınız. 

* Zaten tanımlanan belirli bir oturumunuz varsa, `DBCC INPUTBUFFER(<session_id>)` bir oturum tarafından gönderilen son ifadeyi bulmak için ' i kullanabilirsiniz. Sorgu ve filtreleme daha kolay olan session_id ve request_id sağlayan bir sonuç kümesinde sys.dm_exec_input_buffer dinamik yönetim işlevi (DMF) ile benzer sonuçlar döndürülebilir. Örneğin, session_id 66 ve request_id 0 tarafından gönderilen en son sorguyu döndürmek için:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Sys.dm_exec_requests başvurun ve blocking_session_id sütununa başvurun. Blocking_session_id = 0 olduğunda bir oturum engellenmiyor. Sys.dm_exec_requests yalnızca şu anda yürütülmekte olan istekleri listelerken, herhangi bir bağlantı (etkin veya değil) sys.dm_exec_sessions listelenir. Sonraki sorgudaki sys.dm_exec_requests ve sys.dm_exec_sessions arasında bu ortak birleşime derleyin.

* [Sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) veya [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMVs kullanarak, etkin şekilde yürütülen sorguları ve geçerli SQL toplu iş metnini veya giriş arabelleği metnini bulmak için bu örnek sorguyu çalıştırın. Sys.dm_exec_sql_text alanı tarafından döndürülen veriler `text` null ise, sorgu şu anda yürütülmüyor. Bu durumda, `event_info` sys.dm_exec_input_buffer alanı SQL altyapısına geçirilen son komut dizesini içerir. Bu sorgu, session_id başına engellenen session_ids listesi de dahil olmak üzere diğer oturumları engelleyen oturumları belirlemek için de kullanılabilir. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Bir engelleme zincirinde yer alan oturumların sorgu metni de dahil olmak üzere birden çok oturum engelleme zincirinin başını belirlemek için, Microsoft Desteği tarafından sunulan bu daha ayrıntılı örnek sorguyu çalıştırın.

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* Uzun süre çalışan veya işlenmemiş işlemleri yakalamak için, [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)ve sys.dm_exec_sql_text dahil olmak üzere geçerli açık işlemleri görüntülemek için başka bir DMV kümesi kullanın. İzleme işlemleri ile ilişkili çeşitli DMVs 'ler vardır. buradaki işlemlere daha fazla [DMVs](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) . 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* SQL 'in iş parçacığı/görev katmanında başvuru [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) . Bu, isteğin Şu anda karşılaştığı SQL bekleme türü hakkında bilgi döndürür. Sys.dm_exec_requests gibi, yalnızca etkin istekler sys.dm_os_waiting_tasks döndürülür. 

> [!Note]
> Zaman içinde toplanmış bekleme istatistikleri dahil olmak üzere bekleme türleri hakkında daha fazla bilgi için bkz. DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database). Bu DMV yalnızca geçerli veritabanı için toplam bekleme istatistiklerini döndürür.

* Sorgular tarafından hangi kilitlerin yerleştirildiği hakkında daha ayrıntılı bilgi için [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV kullanın. Bu DMV, bir üretim SQL Server büyük miktarlarda veri döndürebilir ve şu anda hangi kilitlerin tutulmakta olduğunu tanılamak için yararlıdır. 

Sys.dm_os_waiting_tasks Iç BIRLEŞIM nedeniyle aşağıdaki sorgu, çıktıyı yalnızca şu anda engellenen isteklere, bekleme durumuna ve bunların kilitlerine sys.dm_tran_locks kısıtlar:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* DMVs ile sorgu sonuçlarının zaman içinde depolanması, kalıcı engelleme veya eğilimleri belirlemek için belirli bir zaman aralığı üzerinde engellemeyi incelemenizi sağlayacak veri noktaları sağlar. 

## <a name="gather-information-from-extended-events"></a>Genişletilmiş olaylardan bilgi toplayın

Önceki bilgilere ek olarak, Azure SQL veritabanı 'nda engelleyici bir sorunu ayrıntılı bir şekilde araştırmak için genellikle sunucudaki etkinliklerin izlenmesini yakalamak gereklidir. Örneğin, bir oturum bir işlem içinde birden çok deyim çalıştırırsa, yalnızca gönderilen son deyim temsil edilir. Ancak, önceki deyimlerden biri kilitlerin hala tutulmakta olmasının nedeni olabilir. Bir izleme, geçerli işlem içindeki bir oturum tarafından yürütülen tüm komutları görmenizi sağlar.

SQL Server izlemeleri yakalamaya yönelik iki yol vardır; Genişletilmiş olaylar (XEvents) ve profil oluşturucu Izlemeleri. Ancak, Azure SQL veritabanı için kullanım dışı olan [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) izleme teknolojisi desteklenmez. [Genişletilmiş olaylar](/sql/relational-databases/extended-events/extended-events) , gözlemlenen sisteme daha fazla yönlülük ve daha fazla etki sağlayan yeni izleme teknolojisidir ve arabirimi SQL Server Management Studio (SSMS) ile tümleşiktir. 

SSMS 'de [genişletilmiş olaylar yeni oturum Sihirbazı 'nın](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) nasıl kullanılacağını açıklayan belgeye başvurun. Ancak, Azure SQL veritabanları için, SSMS Nesne Gezgini içindeki her bir veritabanı altında genişletilmiş olaylar alt klasörü sağlar. Bu faydalı olayları yakalamak için genişletilmiş olaylar oturum Sihirbazı 'nı kullanın: 

-   Kategori hataları:
    -   Özellikle
    -   Error_reported  
    -   Execution_warning

-   Kategori uyarıları: 
    -   Missing_join_predicate

-   Kategori yürütme:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Kilitle
    -   Lock_deadlock

-   Oturum
    -   Existing_connection
    -   Oturum aç
    -   Oturumu Kapat

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Yaygın engelleme senaryolarını tanımla ve çözümle

Önceki bilgileri inceleyerek, çoğu engelleme sorununun nedenini belirleyebilirsiniz. Bu makalenin geri kalanında, bazı yaygın engelleme senaryolarını belirlemek ve çözmek için bu bilgilerin nasıl kullanılacağına ilişkin bir tartışmadır. Bu tartışma, engelleme SPID 'si hakkında bilgi yakalamak ve bir XEvent oturumu kullanarak yakalanan uygulama etkinliğine sahip olan engelleme betiklerini (daha önce başvuruluyor) kullandığınızı varsayar.

## <a name="analyze-blocking-data"></a>Engelleme verilerini çözümle 

* Blocking_these ve session_id kullanarak, engelleme zincirlerinin kafalarını öğrenmek için DMVs sys.dm_exec_requests ve sys.dm_exec_sessions çıkışını inceleyin. Bu, hangi isteklerin engelleneceğini ve hangilerinin engellediğini açıkça belirler. Engellenen ve engelleyen oturumlara daha fazla göz atın. Engelleme zincirinde ortak veya kök var mı? Muhtemelen ortak bir tabloyu paylaşıyor ve engelleme zincirinde yer alan bir veya daha fazla oturum yazma işlemi gerçekleştiriyor. 

* Blok zincirinin başındaki SPID hakkında bilgi edinmek için DMVs sys.dm_exec_requests ve sys.dm_exec_sessions çıktısını inceleyin. Aşağıdaki alanları arayın: 

    -    `sys.dm_exec_requests.status`  
    Bu sütunda belirli bir isteğin durumu gösterilmektedir. Genellikle uyuyan bir durum SPID 'nin yürütmeyi tamamladığını ve uygulamanın başka bir sorgu ya da toplu iş göndermesini beklediğini gösterir. Bir çalıştırılabilir veya çalışıyor durumu SPID 'nin şu anda bir sorgu yaptığını gösterir. Aşağıdaki tabloda çeşitli durum değerlerinin kısa açıklamaları verilmektedir.

    | Durum | Anlamı |
    |:-|:-|
    | Arka Plan | SPID, kilitlenme algılama, günlük yazıcı veya denetim noktası gibi bir arka plan görevi çalıştırıyor. |
    | Uykuya | SPID şu anda yürütülmüyor. Bu genellikle SPID 'nin uygulamadan bir komut beklediğini gösterir. |
    | Çalışma | SPID şu anda bir Zamanlayıcı üzerinde çalışıyor. |
    | Çalıştırılabilir | SPID, Scheduler 'ın çalıştırılabilir kuyruğuna ait ve Zamanlayıcı süresini almayı bekliyor. |
    | Askıya alındı | SPID, kilit veya mandal gibi bir kaynağı bekliyor. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    Bu alan size bu oturumdaki açık işlem sayısını söyler. Bu değer 0 ' dan büyükse, SPID açık bir işlem içindedir ve işlem içindeki herhangi bir ifadeye alınmış kilitleri tutan olabilir.

    -    `sys.dm_exec_requests.open_transaction_count`  
    Benzer şekilde, bu alan size bu istekteki açık işlem sayısını bildirir. Bu değer 0 ' dan büyükse, SPID açık bir işlem içindedir ve işlem içindeki herhangi bir ifadeye alınmış kilitleri tutan olabilir.

    -   `sys.dm_exec_requests.wait_type`, `wait_time` ve `last_wait_type`  
     `sys.dm_exec_requests.wait_type`   Null ise, istek şu anda hiçbir şey beklemiyor ve  `last_wait_type`   değer  `wait_type`   , isteğin karşılaştığı son değeri gösterir. Hakkında daha fazla bilgi  `sys.dm_os_wait_stats` ve en sık kullanılan bekleme türlerinin açıklaması için bkz. [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).  `wait_time`   Değer, isteğin ilerleme yapıp yapmadığını anlamak için kullanılabilir. Sys.dm_exec_requests tabloya karşı bir sorgu,  `wait_time`   önceki bir sys.dm_exec_requests sorgusundan gelen değerden daha az bir değer döndürürse  `wait_time`   , bu, önceki kilidin elde edilen ve serbest bırakıldığını ve artık yeni bir kilit (sıfır dışında) beklediğini gösterir `wait_time` . Bu, `wait_resource`   isteğin beklediği kaynağı görüntüleyen sys.dm_exec_requests çıkışı arasında karşılaştırılarak doğrulanabilir.

    -   `sys.dm_exec_requests.wait_resource` Bu alan, engellenen bir isteğin beklediği kaynağı gösterir. Aşağıdaki tabloda ortak  `wait_resource`   biçimler ve bunların anlamları listelenmektedir:

    | Kaynak | Biçimlendir | Örnek | Açıklama | 
    |:-|:-|:-|:-|
    | Tablo | DatabaseID: ObjectID: IndexId | SEKME: 5:261575970:1 | Bu durumda, veritabanı KIMLIĞI 5, pubs örnek veritabanıdır ve nesne KIMLIĞI 261575970, başlık tablosu ve 1 ise kümelenmiş dizindir. |
    | Sayfa | DatabaseID: FileId: PageId | SAYFA: 5:1:104 | Bu durumda, veritabanı KIMLIĞI 5 pubs, dosya KIMLIĞI 1 ise birincil veri dosyasıdır ve sayfa 104, başlıklar tablosuna ait olan bir sayfasıdır. Sayfanın ait olduğu object_id belirlemek için, ' den DatabaseID, FileId, PageId ' yi geçirerek [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql)dinamik yönetim işlevini kullanın `wait_resource` . | 
    | Anahtar | DatabaseID: Hobt_id (dizin anahtarı için karma değer) | ANAHTAR: 5:72057594044284928 (3300a4f361aa) | Bu durumda, veritabanı KIMLIĞI 5 pubs, Hobt_ID 72057594044284928 object_id 261575970 (başlıklar tablosu) için index_id 2 ' ye karşılık gelir. Hobt_id belirli bir index_id ve object_id ilişkilendirmek için sys. partitions katalog görünümünü kullanın. Dizin anahtarı karmasında belirli bir anahtar değerine karma değeri kaldırma yöntemi yoktur. |
    | Satır | DatabaseID: FileId: PageId: slot (satır) | RıD: 5:1:104:3 | Bu durumda, veritabanı KIMLIĞI 5, dosya KIMLIĞI 1, birincil veri dosyasıdır, sayfa 104 başlıklar tablosuna ait olan bir sayfa ve yuva 3 ise satırın sayfadaki konumunu gösterir. |
    | Se  | DatabaseID: FileId: PageId: slot (satır) | RıD: 5:1:104:3 | Bu durumda, veritabanı KIMLIĞI 5, dosya KIMLIĞI 1, birincil veri dosyasıdır, sayfa 104 başlıklar tablosuna ait olan bir sayfa ve yuva 3 ise satırın sayfadaki konumunu gösterir. |

    -    `sys.dm_tran_active_transactions`[Sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV, işleme veya geri alma bekleyen işlemlerin tamamen bir resmi Için diğer DMVs 'ye katılebilecek açık işlemler hakkında veri içerir. [Sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)dahil diğer DMVs 'ye katılmış açık işlemler hakkında bilgi döndürmek için aşağıdaki sorguyu kullanın. Bir işlemin geçerli durumunu `transaction_begin_time` ve bir engelleme kaynağı olup olmadığını değerlendirmek için diğer durumsal verilerini göz önünde bulundurun.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   Diğer sütunlar

        [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) ve [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) içindeki kalan sütunlar da bir sorunun köküne ilişkin öngörüler sağlayabilir. Yararlılığı, sorunun durumlarına bağlı olarak farklılık gösterir. Örneğin, sorunun yalnızca belirli istemcilerden (konak adı), belirli ağ kitaplıklarında (net_library), bir SPID tarafından gönderilen son toplu iş `last_request_start_time` sys.dm_exec_sessions, bir isteğin ne kadar süre çalıştığı `start_time` sys.dm_exec_requests ve bu şekilde devam ederek emin olabilirsiniz.


## <a name="common-blocking-scenarios"></a>Yaygın engelleme senaryoları

Aşağıdaki tabloda, yaygın belirtilerin olası nedenleri eşlenir.  

`wait_type`, `open_transaction_count` Ve `status` sütunları [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)tarafından döndürülen bilgilere başvurur, diğer sütunlar [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql)tarafından döndürülür. "Çözümleniyor mi?" sütun, engellemenin kendi kendine çözümlenip çözümlenmeyeceğini ya da oturum komut aracılığıyla sonlandırılmayacağını gösterir `KILL` . Daha fazla bilgi için bkz. [KILL (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Senaryo | Aldığı | Open_Tran | Durum | Çözümler? | Diğer belirtiler |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NULL DEĞIL | >= 0 | çalıştırılabilir | Evet, sorgu bittiğinde. | Sys.dm_exec_sessions, **okuma**, **cpu_time** ve/veya **memory_usage** sütunları zaman içinde artacaktır. Sorgu süresi tamamlandığında yüksek olacaktır. |
| 2 | NULL | \>0 | uykuya | Hayır, ancak SPID sonlandırılabilir. | Bu SPID 'nin genişletilmiş olay oturumunda bir uyarı sinyali görünebilir ve bu, bir sorgu zaman aşımı veya iptal hatası oluştuğunu belirtir. |
| 3 | NULL | \>= 0 | çalıştırılabilir | Hayır. İstemci tüm satırları getirene veya bağlantıyı kapatana kadar çözümlenmeyecektir. SPID sonlandırılabilir, ancak bu işlem 30 saniyeye kadar sürebilir. | Open_transaction_count = 0 ise ve işlem yalıtım düzeyi varsayılan olduğunda SPID kilitleri tutuyorsa (okuma işlemi kabul edilir), bu olası bir nedendir. |  
| 4 | Değişir | \>= 0 | çalıştırılabilir | Hayır. İstemci sorguları iptal edinceye veya bağlantıları kapatana kadar çözümlenmeyecektir. SPID 'ler sonlandırılabilir, ancak 30 saniyeye kadar sürebilir. | Engelleme zincirinin başındaki SPID 'nin sys.dm_exec_sessions **ana bilgisayar adı** sütunu, engellediği SPID 'den biriyle aynı olacaktır. |  
| 5 | NULL | \>0 | etkin | Evet. | Bu SPID 'nin genişletilmiş olaylar oturumunda bir uyarı sinyali görünebilir. Bu, bir sorgu zaman aşımı veya iptal hatası olduğunu veya yalnızca bir geri alma ifadesinin verildiğini gösterir. |  
| 6 | NULL | \>0 | uykuya | Sonra. Windows NT oturumu artık etkin olmadığını belirlediğinde, Azure SQL veritabanı bağlantısı bozulur. | `last_request_start_time`Sys.dm_exec_sessions değeri geçerli zamandan çok daha eski. |

## <a name="detailed-blocking-scenarios"></a>Ayrıntılı engelleme senaryoları

1.  Uzun yürütme süresi ile normal çalışan bir sorgunun neden olduğu engelleme

    **Çözüm**: Bu tür engelleme sorununa yönelik çözüm, sorguyu iyileştirmek için bir yol arayacaktır. Aslında, bu engelleme sorunu sınıfı yalnızca bir performans sorunu olabilir ve bu şekilde bu şekilde izlenmesi gerekir. Yavaş çalışan belirli bir sorgunun sorunlarını giderme hakkında bilgi için, bkz. [SQL Server yavaş çalışan sorgularda sorun giderme](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Daha fazla bilgi için bkz. [performans Için izleme ve ayarlama](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    SSMS 'deki [sorgu deposundan](/sql/relational-databases/performance/best-practice-with-the-query-store) alınan raporlar Ayrıca en pahalı sorguları, en yüksek düzeyde yürütme planlarını belirlemek için de önerilen ve değerli bir araçtır. Ayrıca, [sorgu performansı içgörüleri](query-performance-insight-use.md)dahil olmak üzere Azure SQL veritabanı Azure Portal 'Nin [akıllı performans](intelligent-insights-overview.md) bölümünü gözden geçirin.

    Diğer kullanıcıları engelleyen uzun süre çalışan bir sorgunuz varsa ve en iyi duruma getirilemiyor, [veritabanının zaman uyumlu bir salt okunabilir çoğaltması](read-scale-out.md)olan bir OLTP ortamından adanmış bir raporlama sistemine taşımayı düşünün.

1.  İşlenmemiş bir işlem olan uyuyan bir SPID nedeniyle oluşan engelleme

    Bu tür bir engelleme, genellikle, işlem iç içe geçme düzeyi ( `@@TRANCOUNT` `open_transaction_count` sys.dm_exec_requests) sıfırdan büyük olan bir SPID tarafından tanımlanabilir veya bir komut bekliyor olabilir. Bu durum, uygulama bir sorgu zaman aşımı yaşıyorsa ya da gerekli sayıda ROLLBACK ve/veya COMMıT deyimi vermeden bir iptal sorunu yaşdığında ortaya çıkabilir. Bir SPID bir sorgu zaman aşımı veya iptal işlemi aldığında, geçerli sorguyu ve toplu işi sonlandırır, ancak işlemi otomatik olarak geri almaz veya gerçekleştirmez. Azure SQL veritabanı, tek bir sorgunun iptal edilmesi nedeniyle tüm bir işlemin geri alınması gerektiğini varsaymadığı için uygulama bundan sorumludur. Sorgu zaman aşımı veya iptal etme, uzatılmış olay oturumunda SPID için bir uyarı sinyali olayı olarak görünür.

    İşlenmemiş bir açık işlemi göstermek için aşağıdaki sorguyu yayınlayın:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Ardından, bu sorguyu aynı pencerede yürütün:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    İkinci sorgunun çıktısı, işlem iç içe geçme düzeyinin bir olduğunu gösterir. İşlem sırasında elde edilen tüm kilitler, işlem kaydedilene veya geri alınana kadar devam eder. Uygulamalar, işlemleri açık bir şekilde açıp kaydetmedikçe, bir iletişim veya başka bir hata oturum ve işlem açık bir durumda kalabilir. 

    Bu makalenin önceki kısımlarında yer alan betiği, örnek genelinde kaydedilmemiş olan işlemleri tanımlamak için sys.dm_tran_active_transactions temel alır.

    **Çözümler**:

     -   Ayrıca, bu engelleme sorunu sınıfı da bir performans sorunu olabilir ve bu şekilde bu şekilde izlenmesi gerekir. Sorgu yürütme süresi azaldıysanız sorgu zaman aşımı veya iptal gerçekleşmez. Uygulamanın zaman aşımı veya iptal senaryosu olması önemlidir, ancak sorgunun performansını incelemeyi de avantajdan yararlanabilirsiniz). 
     
     -    Uygulamalar, işlem iç içe düzeylerini düzgün şekilde yönetmelidir veya sorgunun bu şekilde iptali sonrasında bir engelleyici soruna neden olabilir. Aşağıdaki topluluklara bir göz atın:  

            *    İstemci uygulamanın hata işleyicisinde, `IF @@TRANCOUNT > 0 ROLLBACK TRAN` istemci uygulaması bir işlem açık olduğuna inanmasa bile, aşağıdaki hataları yürütün. Batch sırasında çağrılan bir saklı yordam, istemci uygulamasının bilgisi olmadan bir işlem başlatabileceğinden açık işlemleri denetlemek gereklidir. Sorguyu iptal etme gibi belirli koşullar, yordamın geçerli deyimin ötesinde yürütülmesini önler, bu nedenle yordamda işlemi denetleme ve durdurma mantığı olsa da `IF @@ERROR <> 0` Bu geri alma kodu böyle durumlarda yürütülmeyecektir.  
            *    Bağlantıyı açan bir uygulamada bağlantı havuzu kullanılıyorsa, bağlantıyı bir Web tabanlı uygulama gibi havuza geri bırakmadan önce az sayıda sorgu çalıştırıyorsa, bağlantı havuzunu geçici olarak devre dışı bırakmak, istemci uygulaması hataları uygun şekilde işleyecek şekilde değiştirilene kadar sorunu gidermenize yardımcı olabilir. Bağlantı havuzunu devre dışı bırakarak, bağlantının bırakılması Azure SQL veritabanı bağlantısının fiziksel bağlantısının kesilmesine neden olur ve bu da sunucunun açık işlemleri geri almasını ister.  
            *    `SET XACT_ABORT ON`Bağlantı için veya işlem başlatma ve bir hata takip eden herhangi bir saklı yordamda kullanın. Çalışma zamanı hatası durumunda, bu ayar tüm açık işlemleri iptal eder ve denetimi istemciye döndürür. Daha fazla bilgi için bkz. [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql).

    > [!NOTE]
    > Bağlantı, bağlantı havuzundan yeniden yükleninceye kadar sıfırlanmaz, bu nedenle bir kullanıcının bir işlemi açıp bağlantı havuzu bağlantısını serbest bırakabilmesi olasıdır, ancak işlem açık kaldığı zaman birkaç saniye boyunca yeniden kullanılamaz. Bağlantı yeniden kullanılmazsa, bağlantı zaman aşımına uğrar ve bağlantı havuzundan kaldırıldığında işlem iptal edilir. Bu nedenle, istemci uygulamanın hata işleyicisindeki işlemleri iptal etmek veya `SET XACT_ABORT ON` Bu olası gecikmeyi önlemek için kullanması idealdir.

    > [!CAUTION]
    > Aşağıdaki `SET XACT_ABORT ON` , bir hata oluşmasına neden olan bir deyimi izleyen T-SQL deyimleri yürütülmeyecektir. Bu, varolan kodun amaçlanan akışını etkileyebilir.

1.  Karşılık gelen istemci uygulaması, tüm sonuç satırlarını tamamlamaya getirmeyen bir SPID tarafından neden olan engelleme

    Sunucuya bir sorgu gönderdikten sonra, tüm uygulamaların tüm sonuç satırlarını hemen tamamlamaya getirmesi gerekir. Bir uygulama tüm sonuç satırlarını getirmezse, kilitler tablolarda bırakılabilir ve diğer kullanıcıları engelliyor olabilir. Sunucuya saydam olarak SQL deyimleri gönderen bir uygulama kullanıyorsanız, uygulamanın tüm sonuç satırlarını getirmesi gerekir. Değilse (ve bunu yapmak için yapılandırılamıyor), engelleyici sorunu çözemeyebilirsiniz. Sorunu önlemek için, kötü davranmış uygulamaları, ana OLTP veritabanından ayrı bir raporlama veya karar destek veritabanıyla kısıtlayabilirsiniz.
    
    > [!NOTE]
    > Bkz. Azure SQL veritabanı 'na bağlanan uygulamalar için [yeniden deneme mantığı Kılavuzu](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) . 
    
    **Çözüm**: uygulamanın, sonucun tüm satırlarını tamamlamada getirmek için yeniden yazılması gerekir. Bu, sunucu tarafı sayfalama gerçekleştirmek için bir sorgunun [order by yan tümcesinde offset ve Fetch](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) kullanımını denetlemez.

1.  Geri alma durumundaki bir oturumun neden olduğu engelleme

    Sonlandırılan veya Kullanıcı tanımlı bir işlemin dışında iptal edilen bir veri değiştirme sorgusu geri alınacaktır. Bu durum, istemci ağ oturumunun bağlantısını kesme işleminin yan etkisi veya kilitlenme kurbanı olarak bir istek seçildiğinde da oluşabilir. Bu, genellikle GERI alma **komutunu** gösterebilen sys.dm_exec_requests çıkışı gözlemleyerek tanımlanabilir ve **Percent_complete sütunu** ilerlemeyi gösterebilir. 

    2019 ' de sunulan [hızlandırılmış veritabanı kurtarma özelliği](../accelerated-database-recovery.md) sayesinde, uzun geri alma nadir olmalıdır.
    
    **Çözüm**: SPID 'nin yapılan değişiklikleri geri alma işleminin bitmesini bekleyin. 

    Bu durumdan kaçınmak için, OLTP sistemlerinde meşgul saatlerde büyük toplu işlem yazma işlemleri veya dizin oluşturma veya bakım işlemleri gerçekleştirmeyin. Mümkünse, düşük etkinlik dönemlerinde bu işlemleri gerçekleştirin.

1.  Yalnız bırakılmış bağlantının neden olduğu engelleme

    İstemci uygulaması hataları yakalar veya istemci iş istasyonunu yeniden başlatılırsa, bazı koşullarda sunucuya ağ oturumu hemen iptal edilemez. Azure SQL veritabanı perspektifinden, istemci hala var gibi görünüyor ve elde edilen tüm kilitler yine de tutuluyor olabilir. Daha fazla bilgi için bkz. [SQL Server yalnız bırakılmış bağlantıların sorunlarını giderme](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Çözüm**: istemci uygulamanın, kaynaklarını uygun şekilde temizlemeden bağlantısı kesilirse, komutunu kullanarak SPID 'yi sonlandırabilirsiniz `KILL` . `KILL`Komut SPID değerini girdi olarak alır. Örneğin, SPID 99 ' i sonlandırmak için aşağıdaki komutu verin:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Ayrıca bkz.

* [Azure SQL Veritabanı ve Azure SQL Yönetilen Örneği'nde izleme ve performansı ayarlama](./monitor-tune-overview.md)
* [Sorgu deposunu kullanarak performansı izleme](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [İşlem Kilitleme ve Satır Sürümü Oluşturma Kılavuzu](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [IŞLEM YALıTıM DÜZEYINI AYARLA](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Hızlı Başlangıç: SQL Server’da genişletilmiş olaylar](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Veritabanı performansını izlemek ve sorunlarını gidermek için AI kullanarak Akıllı İçgörüler](intelligent-insights-overview.md)

## <a name="learn-more"></a>Daha fazla bilgi edinin

* [Azure SQL veritabanı: otomatik ayarlama ile performansı ayarlamayı artırma](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Otomatik ayarlama ile Azure SQL veritabanı performansını iyileştirme](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Azure SQL ile tutarlı performans sunma](/learn/modules/azure-sql-performance/)
* [Azure SQL veritabanı ve Azure SQL yönetilen örneği ile bağlantı sorunlarını ve diğer hataları giderme](troubleshoot-common-errors-issues.md)
* [Geçici hata Işleme](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
* [Azure SQL veritabanı 'nda maksimum paralellik derecesi (MAXDOP) yapılandırma](configure-max-degree-of-parallelism.md)

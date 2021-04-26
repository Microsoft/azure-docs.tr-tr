---
title: Tablolar üzerinde istatistikler oluşturma ve güncelleştirme
description: Adanmış SQL havuzundaki tablolarda sorgu iyileştirme istatistiklerini oluşturmaya ve güncelleştirmeye yönelik öneriler ve örnekler.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 42dd34f0fe4006411f413002eb7c351646e72390
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078970"
---
# <a name="table-statistics-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzu için tablo istatistikleri

Bu makalede, adanmış SQL havuzundaki tablolarda sorgu iyileştirme istatistiklerini oluşturmak ve güncelleştirmek için öneriler ve örnekler bulacaksınız.

## <a name="why-use-statistics"></a>İstatistikleri neden kullanılmalıdır?

Daha fazla adanmış SQL havuzu verilerinize göre daha hızlı sorgu yürütebilir. Verileri adanmış SQL havuzuna yükledikten sonra, verilerinizi iyileştirmek için kullanabileceğiniz en önemli işlemlerden biri, verileriniz üzerinde istatistik toplama işlemi yapmanız gerekir.

Adanmış SQL havuzu sorgu iyileştiricisi, maliyet tabanlı bir iyileştiricudur. Çeşitli sorgu planlarının maliyetini karşılaştırır ve en düşük maliyetli planı seçer. Çoğu durumda, en hızlı yürütecektir planı seçer.

Örneğin, iyileştirici sorgunun filtrelemesinin olduğu tarihin bir satır döndürür bir plan seçer. Seçili tarihin 1.000.000 satır döndüreceğini tahmin eder, farklı bir plan döndürür.

## <a name="automatic-creation-of-statistic"></a>Otomatik istatistiğin oluşturulması

Veritabanı AUTO_CREATE_STATISTICS seçeneği açık olduğunda, adanmış SQL havuzu eksik istatistik için gelen kullanıcı sorgularını analiz eder.

İstatistikler eksikse, sorgu iyileştiricisi sorgu koşulunda tek tek sütunlarda istatistikler oluşturur ve sorgu planının kardinalitesini tahmin etmek için JOIN koşulundaki

> [!NOTE]
> İstatistiklerin otomatik olarak oluşturulması Şu anda varsayılan olarak açıktır.

Aşağıdaki komutu çalıştırarak adanmış SQL havuzunuzun AUTO_CREATE_STATISTICS yapılandırılıp yapılandırılmadığını kontrol edebilirsiniz:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Adanmış SQL havuzunuzun yapılandırılmış AUTO_CREATE_STATISTICS yoksa, aşağıdaki komutu çalıştırarak bu özelliği etkinleştirmenizi öneririz:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Bu deyimler istatistiklerin otomatik olarak oluşturulmasını tetikler:

- SELECT
- ıNSERT-SELECT
- CTAS
- UPDATE
- DELETE
- Bir JOIN içerdiğinde veya bir koşulun varlığı algılandığında AÇıKLA

> [!NOTE]
> Otomatik istatistik oluşturma geçici veya dış tablolarda oluşturulmaz.

İstatistiklerin otomatik olarak oluşturulması zaman uyumlu olarak yapılır, böylece sütunlarınızın istatistikleri eksik olduğunda biraz düşürülmüş sorgu performansına neden olabilirsiniz. Tek bir sütun için istatistik oluşturma zamanı tablonun boyutuna bağlıdır.

Ölçülebilir performans düşüşünü önlemek için, sistemin profilini oluşturmadan önce kıyaslama iş yükünü yürüterek öncelikle istatistik oluşturulduğundan emin olmanız gerekir.

> [!NOTE]
> İstatistik oluşturma, farklı bir kullanıcı bağlamı altında [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) oturum açacaktır.

Otomatik istatistik oluşturulduğunda, şu biçimi alır: _WA_Sys_<8 basamaklı sütun kimliği onaltılık>_<8 basamaklı tablo kimliği onaltılık>. [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) komutu çalıştırılarak daha önce oluşturulmuş olan istatistikleri görüntüleyebilirsiniz:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name, görüntülenecek istatistikleri içeren tablonun adıdır. Bu tablo bir dış tablo olamaz. Hedef, İstatistik bilgilerinin görüntüleneceği hedef dizinin, istatistiklerin veya sütunun adıdır.

## <a name="update-statistics"></a>İstatistikleri Güncelleştir

En iyi yöntem, yeni tarihler eklendikçe her gün tarih sütunlarında istatistikleri güncelleştirmelerdir. Adanmış SQL havuzuna her yeni satır yüklendiğinde yeni yükleme tarihleri veya işlem tarihleri eklenir. Bu eklemeler, veri dağıtımını değiştirir ve istatistikleri güncel hale getirir.

Değerlerin dağıtılması genellikle değişmediğinden, müşteri tablosundaki bir ülke/bölge sütunundaki istatistiklerin hiçbir şekilde güncelleştirilmesine gerek kalmaz. Dağıtımın müşteriler arasında sabit olduğu varsayıldığında, tablo varyasyonuna yeni satırlar eklemek veri dağıtımını değiştirmez.

Ancak, adanmış SQL havuzunuz yalnızca bir ülke/bölge içeriyorsa ve yeni bir ülke/bölgeden veri getirdiyseniz, birden çok ülkede/bölgeden veri elde edersiniz. Bu durumda, ülke/bölge sütunundaki istatistikleri güncelleştirmeniz gerekir.

Öneriler güncelleştirme istatistikleri aşağıda verilmiştir:

| İstatistik özniteliği | Öneri|
|-|-|
| **İstatistik güncelleştirme sıklığı**  | Koruyucu: günlük </br> Verilerinizi yükledikten veya dönüştürdükten sonra |
| **Örnekleme** |  1.000.000.000 satırdan az olan varsayılan örnekleme (yüzde 20) kullanın. </br> 1.000.000.000 ' den fazla satır ile, iki yüzde örnekleme kullanın. |

Bir sorgunun sorunlarını giderirken ilk sorulardan biri, **"istatistiklerin güncel mi?"** olduğunu sorar.

Bu soru, verilerin yaşına göre yanıtlanacak bir değildir. Temel alınan verilerde malzeme değişikliği olmadığında güncel bir istatistik nesnesi eski olabilir. Satır sayısı önemli ölçüde değiştirildiğinde veya bir sütun için değerlerin dağıtımında bir malzeme değişikliği *varsa, istatistiklerin güncelleştirilmesi zaman olur.* 

Son istatistik güncelleştirildikten sonra tablodaki verilerin değişip değişmediğini tespit etmek için dinamik bir yönetim görünümü yoktur.  Aşağıdaki iki sorgu istatistiklerinizin eski olup olmadığını belirlemenize yardımcı olabilir.

**Sorgu 1:**  İstatistiklerdeki satır sayısı (**stats_row_count**) ve gerçek satır sayısı arasındaki farkı (**actual_row_count**) öğrenin. 

```sql
select 
objIdsWithStats.[object_id], 
actualRowCounts.[schema], 
actualRowCounts.logical_table_name, 
statsRowCounts.stats_row_count, 
actualRowCounts.actual_row_count,
row_count_difference = CASE
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN actualRowCounts.actual_row_count - statsRowCounts.stats_row_count
    ELSE statsRowCounts.stats_row_count - actualRowCounts.actual_row_count
END,
percent_deviation_from_actual = CASE
    WHEN actualRowCounts.actual_row_count = 0 THEN statsRowCounts.stats_row_count
    WHEN statsRowCounts.stats_row_count = 0 THEN actualRowCounts.actual_row_count
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (actualRowCounts.actual_row_count - statsRowCounts.stats_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
    ELSE CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (statsRowCounts.stats_row_count - actualRowCounts.actual_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
END
from
(
    select distinct object_id from sys.stats where stats_id > 1
) objIdsWithStats
left join
(
    select object_id, sum(rows) as stats_row_count from sys.partitions group by object_id
) statsRowCounts
on objIdsWithStats.object_id = statsRowCounts.object_id 
left join
(
    SELECT sm.name [schema] ,
    tb.name logical_table_name ,
    tb.object_id object_id ,
    SUM(rg.row_count) actual_row_count
    FROM sys.schemas sm
    INNER JOIN sys.tables tb ON sm.schema_id = tb.schema_id
    INNER JOIN sys.pdw_table_mappings mp ON tb.object_id = mp.object_id
    INNER JOIN sys.pdw_nodes_tables nt ON nt.name = mp.physical_name
    INNER JOIN sys.dm_pdw_nodes_db_partition_stats rg
    ON rg.object_id = nt.object_id
    AND rg.pdw_node_id = nt.pdw_node_id
    AND rg.distribution_id = nt.distribution_id
    WHERE 1 = 1
    GROUP BY sm.name, tb.name, tb.object_id
) actualRowCounts
on objIdsWithStats.object_id = actualRowCounts.object_id

```

**Sorgu 2:** İstatistiklerin her tabloda ne zaman güncelleştirildiği son zamanı denetleyerek istatistiklerinizin yaşını öğrenin. 

> [!NOTE]
> Bir sütunun değerlerinin dağıtımında bir malzeme değişikliği varsa, son güncelleştirilme zamanından bağımsız olarak istatistikleri güncelleştirmeniz gerekir.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Örneğin, adanmış bir SQL havuzundaki **Tarih sütunları** genellikle sık sık istatistik güncelleştirmelerine ihtiyaç duyar. Adanmış SQL havuzuna her yeni satır yüklendiğinde yeni yükleme tarihleri veya işlem tarihleri eklenir. Bu eklemeler, veri dağıtımını değiştirir ve istatistikleri güncel hale getirir.

Buna karşılık, müşteri tablosundaki cinsiyet sütunundaki istatistiklerin hiçbir şekilde güncellenmesi gerekmeyebilir. Dağıtımın müşteriler arasında sabit olduğu varsayıldığında, tablo varyasyonuna yeni satırlar eklemek veri dağıtımını değiştirmez.

Adanmış SQL havuzunuz yalnızca bir cinsiyet içeriyorsa ve yeni bir gereksinim birden çok gende sonuçlanırsa, cinsiyet sütunundaki istatistikleri güncelleştirmeniz gerekir.

Daha fazla bilgi için bkz. [İstatistikler](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)için genel kılavuz.

## <a name="implementing-statistics-management"></a>İstatistik yönetimi uygulama

Verilerin yükleme işleminin sonunda, eşzamanlı sorgular arasındaki engelleme veya kaynak çekişmesini önlemek/en aza indirmek için istatistiklerin güncelleştirildiğinden emin olmak için veri yükleme işleminizi uzatmak iyi bir fikirdir.  

Veri yükü, tabloların en sık boyutunu ve/veya değerlerinin dağılımını değiştirme olur. Veri yükleme, bazı yönetim süreçlerini uygulamak için mantıksal bir yerdir.

İstatistiklerinizi güncelleştirmek için aşağıdaki temel ilkeler verilmiştir:

- Yüklenen her tabloda en az bir istatistik nesnesinin güncelleştirildiğinden emin olun. Bu, istatistik güncelleştirmesinin bir parçası olarak tablo boyutunu (satır sayısı ve sayfa sayısı) güncelleştirir.
- JOIN, GROUP BY, ORDER BY ve DISTINCT yan tümcelerinde yer alan sütunlara odaklanın.
- Bu değerler istatistik histogramı içinde yer alacağından, işlem tarihleri daha sık olan "artan anahtar" sütunlarını güncelleştirmeyi göz önünde bulundurun.
- Statik dağıtım sütunlarını daha az sıklıkta güncelleştirmeyi göz önünde bulundurun.
- Her istatistik nesnesinin sırayla güncelleştirildiğini unutmayın. `UPDATE STATISTICS <TABLE_NAME>`Özellikle çok sayıda istatistik nesnesi olan geniş tablolar için her zaman ideal bir uygulama değildir.

Daha fazla bilgi için bkz. [kardinalite tahmini](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="examples-create-statistics"></a>Örnekler: istatistik oluşturma

Bu örneklerde, istatistik oluşturmak için çeşitli seçeneklerin nasıl kullanılacağı gösterilmektedir. Her bir sütun için kullandığınız seçenekler, verilerinizin özelliklerine ve sütun sorgularda nasıl kullanılacağını bağlıdır.

### <a name="create-single-column-statistics-with-default-options"></a>Varsayılan seçeneklerle tek sütunlu istatistikler oluşturma

Bir sütunda istatistik oluşturmak için, istatistik nesnesi ve sütunun adı için bir ad sağlayın.

Bu söz dizimi varsayılan seçeneklerin tümünü kullanır. Varsayılan olarak, istatistik oluşturulurken tablonun **yüzde 20** ' si örneklenir.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Örnek:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Her satırı inceleyerek tek sütunlu istatistikler oluşturma

Çoğu durum için yüzde 20 ' nin varsayılan örnekleme oranı yeterlidir. Ancak örnekleme hızını ayarlayabilirsiniz.

Tam tabloyu örneklemek için şu sözdizimini kullanın:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Örnek:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Örnek boyutunu belirterek tek sütunlu istatistikler oluşturma

Alternatif olarak, örnek boyutunu yüzde olarak belirtebilirsiniz:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Yalnızca bazı satırlarda tek sütunlu istatistikler oluşturma

Ayrıca, Tablonuzdaki satırların bir kısmında İstatistikler de oluşturabilirsiniz. Buna filtrelenmiş istatistik denir.

Örneğin, büyük bölümlendirilmiş bir tablonun belirli bir bölümünü sorgulamayı planlarken filtrelenmiş istatistikleri kullanabilirsiniz. Yalnızca bölüm değerlerinde istatistik oluşturarak istatistiklerin doğruluğu iyileştirecektir ve bu nedenle sorgu performansını geliştirir.

Bu örnekte, bir dizi değer üzerinde istatistikler oluşturulur. Değerler, bir bölümdeki değer aralığıyla eşleşecek şekilde kolayca tanımlanabilir.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Sorgu iyileştiricinin dağıtılmış sorgu planını seçtiği sırada filtrelenmiş istatistikleri kullanmayı düşündüğü için, sorgu istatistik nesnesinin tanımına sığması gerekir. Önceki örneği kullanarak, sorgunun WHERE yan tümcesinin 2000101 ile 20001231 arasında Sütun1 değerlerini belirtmesi gerekir.

### <a name="create-single-column-statistics-with-all-the-options"></a>Tüm seçeneklerle tek sütunlu istatistikler oluşturma

Ayrıca, seçenekleri birlikte birleştirebilirsiniz. Aşağıdaki örnek, özel bir örnek boyutuyla filtrelenmiş bir istatistik nesnesi oluşturur:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Tam başvuru için bkz. [Istatistik oluşturma](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="create-multi-column-statistics"></a>Çok sütunlu istatistikler oluşturma

Çok sütunlu bir istatistik nesnesi oluşturmak için önceki örnekleri kullanın, ancak daha fazla sütun belirtin.

> [!NOTE]
> Sorgu sonucundaki satır sayısını tahmin etmek için kullanılan histogramı yalnızca istatistik nesne tanımında listelenen ilk sütunda kullanılabilir.

Bu örnekte, histogram *ürün \_ kategorisinde* bulunur. Çapraz sütun istatistikleri *ürün \_ kategorisinde* ve *ürün \_ sub_category* hesaplanır:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

*Ürün \_ kategorisi* ile *ürün \_ alt \_ kategorisi* arasında bir bağıntı olduğundan, bu sütunlara aynı anda erişildiğinde çok sütunlu bir istatistik nesnesi yararlı olabilir.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Tablodaki tüm sütunlarda istatistik oluşturma

İstatistik oluşturmanın bir yolu, tablo oluşturduktan sonra CREATE STATıSTıCS komutları verilme yöntemidir:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-sql-pool"></a>Bir SQL havuzundaki tüm sütunlarda istatistik oluşturmak için saklı yordam kullanma

Adanmış SQL havuzunda SQL Server sp_create_stats eşdeğer bir sistem saklı yordamı yok. Bu saklı yordam, zaten istatistiği olmayan bir SQL havuzundaki her sütunda tek sütunlu bir istatistik nesnesi oluşturur.

Aşağıdaki örnek, SQL havuzu tasarımınıza başlamanıza yardımcı olur. Gereksinimlerinize uygun hale gelmekten çekinmeyin.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Varsayılan değerleri kullanarak tablodaki tüm sütunlarda istatistik oluşturmak için, saklı yordamı yürütün.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

FULLSCAN kullanarak tablodaki tüm sütunlarda istatistik oluşturmak için bu yordamı çağırın.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Tablodaki tüm sütunlarda örneklenmiş istatistikler oluşturmak için 3 ve örnek yüzdesini girin. Bu yordam yüzde 20 örnek hız kullanır.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Örnekler: güncelleştirme istatistikleri

İstatistikleri güncelleştirmek için şunları yapabilirsiniz:

- Tek bir istatistik nesnesini güncelleştirin. Güncelleştirmek istediğiniz istatistik nesnesinin adını belirtin.
- Tablodaki tüm istatistik nesnelerini güncelleştirin. Belirli bir istatistik nesnesi yerine tablonun adını belirtin.

### <a name="update-one-specific-statistics-object"></a>Belirli bir istatistik nesnesini güncelleştirme

Belirli bir istatistik nesnesini güncelleştirmek için aşağıdaki sözdizimini kullanın:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Örnek:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Belirli istatistik nesnelerini güncelleştirerek, istatistikleri yönetmek için gereken süre ve kaynakları en aza indirmenize olanak sağlar. Bunun yapılması, güncelleştirilecek en iyi istatistik nesnelerini seçmek için bazı düşünce gerektirir.

### <a name="update-all-statistics-on-a-table"></a>Tablodaki tüm istatistikleri güncelleştirme

Bir tablodaki tüm istatistik nesnelerini güncelleştirmek için basit bir yöntem şunlardır:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Örnek:

```sql
UPDATE STATISTICS dbo.table1;
```

GÜNCELLEŞTIRME ISTATISTIKLERI bildiriminin kullanımı kolaydır. Yalnızca tablodaki *Tüm* istatistikleri güncelleştirdiğinizden ve bu nedenle gerekenden daha fazla iş gerçekleştirebileceğini unutmayın. Performans bir sorun değilse, istatistiklerin güncel olup olmadığını güvence altına almak için en kolay ve en kapsamlı yoldur.

> [!NOTE]
> Bir tablodaki tüm İstatistikleri güncelleştirirken, adanmış SQL havuzu her istatistik nesnesi için tabloyu örneklemek üzere bir tarama yapar. Tablo büyükse ve çok sayıda sütun ve birçok istatistik içeriyorsa, her bir istatistiği ihtiyaya göre güncelleştirmek daha verimli olabilir.

Bir yordamın uygulanması için `UPDATE STATISTICS` bkz. [geçici tablolar](sql-data-warehouse-tables-temporary.md). Uygulama yöntemi önceki yordamdan biraz farklıdır `CREATE STATISTICS` , ancak sonuç aynıdır.

Tam sözdizimi için bkz. [güncelleştirme istatistikleri](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="statistics-metadata"></a>İstatistik meta verileri

İstatistikler hakkında bilgi edinmek için kullanabileceğiniz çeşitli sistem görünümleri ve işlevleri vardır. Örneğin, istatistiklerin en son ne zaman oluşturulduğunu veya güncelleştirildiğini görmek için, istatistik bir nesnenin güncel olup olmadığını görmek için stats-Date işlevini kullanabilirsiniz.

### <a name="catalog-views-for-statistics"></a>İstatistikler için katalog görünümleri

Bu sistem görünümleri istatistikler hakkında bilgi sağlar:

| Katalog görünümü | Description |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Her sütun için bir satır. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Veritabanındaki her nesne için bir satır. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Veritabanındaki her şema için bir satır. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Her istatistik nesnesi için bir satır. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |İstatistikler nesnesindeki her sütun için bir satır. Sys. Columns öğesine geri bağlantı. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Her tablo için bir satır (dış tabloları içerir). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Her veri türü için bir satır. |

### <a name="system-functions-for-statistics"></a>İstatistikler için sistem işlevleri

Bu sistem işlevleri, istatistiklerle çalışmak için faydalıdır:

| Sistem işlevi | Description |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |İstatistik nesnesinin son güncelleştirilme tarihi. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |İstatistik nesnesi tarafından anlaşıldıkça değerlerin dağılımı hakkında özet düzeyi ve ayrıntılı bilgiler. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>İstatistik sütunları ve işlevleri tek bir görünümde birleştirin

Bu görünüm, STATS_DATE () işlevinin birlikte istatistikleriyle ve sonuçlarıyla ilgili sütunları getirir.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS () örnekleri

DBCC SHOW_STATISTICS (), bir istatistik nesnesi içinde tutulan verileri gösterir. Bu veriler üç bölümden oluşur:

- Üst bilgi
- Yoğunluk vektörü
- Histogram

İstatistiklerle ilgili üst bilgi meta verileri. Histogram, değerlerin dağılımını istatistik nesnesinin ilk anahtar sütununda görüntüler. Yoğunluk vektörü, çapraz sütun bağıntısını ölçer.

> [!NOTE]
> Adanmış SQL havuzu, istatistik nesnesindeki verilerle kardinalite tahminleri hesaplar.

### <a name="show-header-density-and-histogram"></a>Üstbilgiyi, yoğunluğu ve histogramı göster

Bu basit örnek, bir istatistik nesnesinin üç parçasını gösterir:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Örnek:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>DBCC SHOW_STATISTICS () bir veya daha fazla parçasını göster

Yalnızca belirli parçaları görüntülemek istiyorsanız, `WITH` yan tümcesini kullanın ve hangi parçaları görmek istediğinizi belirtin:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Örnek:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS () farkları

DBCC SHOW_STATISTICS (), SQL Server karşılaştırıldığında adanmış SQL havuzunda daha net bir şekilde uygulanmıştır:

- Belgelenmemiş özellikler desteklenmez.
- Stats_stream kullanılamıyor.
- İstatistik verilerinin belirli alt kümeleri için sonuçlara katılamaz. Örneğin STAT_HEADER DENSITY_VECTOR BIRLEŞTIRIN.
- İleti gizleme için NO_INFOMSGS ayarlanamaz.
- İstatistik adları etrafında köşeli ayraç kullanılamaz.
- İstatistik nesnelerini tanımlamak için sütun adları kullanılamaz.
- Özel hata 2767 desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar

Sorgu performansını artırmak için bkz. [iş yükünüzü izleme](sql-data-warehouse-manage-monitor.md)

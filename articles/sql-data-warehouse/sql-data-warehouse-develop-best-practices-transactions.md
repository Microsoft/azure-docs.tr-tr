---
title: İşlemleri iyileştirme
description: Azure SQL veri ambarı 'nda işlem kodunuzun performansını en üst düzeye çıkarmak ve uzun geri göndermeler riskini en aza indirmek hakkında bilgi edinin.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b8b8be9467ade870e57355be91b0de329b0f6217
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692868"
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda işlemleri iyileştirme
Azure SQL veri ambarı 'nda işlem kodunuzun performansını en üst düzeye çıkarmak ve uzun geri göndermeler riskini en aza indirmek hakkında bilgi edinin.

## <a name="transactions-and-logging"></a>İşlemler ve günlüğe kaydetme
İşlemler, ilişkisel bir veritabanı altyapısının önemli bir bileşenidir. SQL veri ambarı, veri değişikliği sırasında işlemleri kullanır. Bu işlemler açık veya kapalı olabilir. Tek INSERT, UPDATE ve DELETE deyimleri örtülü işlemlere örnektir. Açık işlemler, BEGIN TRAN, COMMıT TRAN veya ROLLBACK TRAN kullanır. Açık işlemler genellikle birden çok değişiklik deyimlerinin tek bir atomik birimde birbirine bağlanması gerektiğinde kullanılır. 

Azure SQL veri ambarı, değişiklikleri veritabanına işlem günlükleri kullanarak kaydeder. Her dağıtımın kendi işlem günlüğü vardır. İşlem günlüğü yazmaları otomatik. Yapılandırma gerekli değildir. Ancak, bu süreci, sistemde bir ek yük ortaya çıkaracak şekilde yazmayı garanti eder. İşlemsel olarak verimli kod yazarak bu etkiyi en aza indirgeyin. İşlem açısından verimli kod, genel olarak iki kategoriye girer.

* Mümkün olduğunda en az sayıda günlük oluşturma kullanın
* Çok uzun süre çalışan işlemleri önlemek için kapsamlı toplu işleri kullanarak verileri işleyin
* Belirli bir bölümdeki büyük değişiklikler için bölüm değiştirme modelini benimseyin

## <a name="minimal-vs-full-logging"></a>En az ve tam günlük
Her satır değişikliğini izlemek için işlem günlüğünü kullanan tam olarak günlüğe kaydedilen işlemlerden farklı olarak, en düşük düzeyde günlüğe kaydedilmiş işlemler yalnızca kapsam ve meta veri değişikliklerinin izini tutar. Bu nedenle, en az günlük kaydı yalnızca bir hatadan sonra işlemi geri almak için gerekli olan bilgileri günlüğe kaydetmeyi veya açık bir istek (ROLLBACK TRAN) gerektirir. İşlem günlüğünde daha az bilgi izlendiğinden, en düşük düzeyde günlüğe kaydedilmiş bir işlem, benzer şekilde tam olarak kaydedilen bir işlemden daha iyi çalışır. Ayrıca, daha az yazma işlemleri işlem günlüğüne gittiğinden, çok daha küçük bir günlük verisi oluşturulur ve bu nedenle daha fazla g/ç etkilidir.

İşlem güvenliği sınırları yalnızca tam olarak günlüğe kaydedilmiş işlemler için geçerlidir.

> [!NOTE]
> En düşük düzeyde günlüğe kaydedilmiş işlemler, açık işlemlere katılabilir. Ayırma yapılarında yapılan tüm değişiklikler izlendiğinden, en az düzeyde günlüğe kaydedilmiş işlemleri geri almak mümkündür. 
> 
> 

## <a name="minimally-logged-operations"></a>En düşük düzeyde günlüğe kaydedilmiş işlemler
Aşağıdaki işlemler en düşük düzeyde günlüğe kaydedilebilir:

* SELECT olarak CREATE TABLE ([CTAS](sql-data-warehouse-develop-ctas.md))
* Ekle.. SEÇIN
* CREATE INDEX
* ALTER ıNDEX REBUıLD
* DROP INDEX
* TRUNCATE TABLE
* TABLOYU BıRAK
* ALTER TABLE SWıTCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> İç veri taşıma işlemleri (örneğin, yayın ve KARıŞTıRMA) işlem güvenliği sınırından etkilenmez.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Toplu yükleme ile en az günlük
CTAS ve INSERT... Her iki toplu yükleme işlemi de SEÇIN. Ancak, her ikisi de hedef tablo tanımıyla etkilenerek yükleme senaryosuna bağlıdır. Aşağıdaki tabloda, toplu işlemlerin tamamen veya en az günlüğe kaydedildiği zaman açıklanmaktadır:  

| Birincil dizin | Yükleme senaryosu | Günlüğe kaydetme modu |
| --- | --- | --- |
| Yığın |Herhangi biri |**En az** |
| Kümelenmiş dizin |Boş hedef tablo |**En az** |
| Kümelenmiş dizin |Yüklenen satırlar, hedefteki mevcut sayfalarla çakışmıyor |**En az** |
| Kümelenmiş dizin |Yüklü satırlar hedefteki mevcut sayfalarla örtüşüyor |Tam |
| Kümelenmiş columnstore dizini |Toplu iş boyutu > = 102.400 bölüm hizalı dağıtım başına |**En az** |
| Kümelenmiş columnstore dizini |Bölüm hizalı dağıtım başına 102.400 < toplu iş boyutu |Tam |

İkincil veya kümelenmemiş dizinleri güncelleştirmek için herhangi bir yazma işlemi her zaman tam olarak günlüğe kaydedilmiş işlemler olacağını belirtmekte bir değer.

> [!IMPORTANT]
> SQL veri ambarı 60 dağıtımlarına sahiptir. Bu nedenle, tüm satırların eşit olarak dağıtıldığı ve tek bir bölüme giriş olduğu varsayıldığında, bir kümelenmiş columnstore dizinine yazarken en az 6.144.000 satır veya daha büyük bir işlem olması gerekir. Tablo bölümlenmiş ise ve eklenen satırlar bölüm sınırlarına yayıldıysanız, veri dağıtımını bile varsayarak bölüm sınırı başına 6.144.000 satıra ihtiyacınız olacaktır. Her bir dağıtım içindeki her bölüm, dağıtıma en az düzeyde günlüğe kaydedilecek ekleme için 102.400 satır eşiğini bağımsız olarak aşmalıdır.
> 
> 

Verilerin kümelenmiş bir dizine sahip boş olmayan bir tabloya yüklenmesi genellikle tam olarak günlüğe kaydedilen ve en düşük düzeyde günlüğe kaydedilen satırlardan oluşan bir karışımı içerebilir. Kümelenmiş dizin, sayfaların dengeli bir ağacıdır (b-Tree). Yazılan sayfa zaten başka bir işlemden satırlar içeriyorsa, bu yazma işlemleri tam olarak günlüğe kaydedilir. Ancak, sayfa boşsa, bu sayfaya yazma işlemi en düşük düzeyde günlüğe kaydedilir.

## <a name="optimizing-deletes"></a>Silmeleri iyileştirme
SILME işlemi tam olarak günlüğe kaydedilir.  Tablodaki veya bir bölümdeki büyük miktarda veriyi silmeniz gerekiyorsa, genellikle korumak istediğiniz verileri `SELECT` daha anlamlı hale gelir ve bu, en düşük düzeyde günlüğe kaydedilmiş bir işlem olarak çalıştırılabilir.  Verileri seçmek için [CTAS](sql-data-warehouse-develop-ctas.md)ile yeni bir tablo oluşturun.  Oluşturulduktan sonra, eski tablonuzu yeni oluşturulan tabloyla değiştirmek için [Yeniden Adlandır](/sql/t-sql/statements/rename-transact-sql) ' ı kullanın.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Güncelleştirmeleri iyileştirme
GÜNCELLEŞTIRME, tam olarak günlüğe kaydedilmiş bir işlemdir.  Bir tabloda veya bölümde çok sayıda satırı güncelleştirmeniz gerekiyorsa, bu, genellikle [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) gibi en düşük düzeyde günlüğe kaydedilmiş bir işlemin kullanılması çok daha verimli olabilir.

Aşağıdaki örnekte, tam tablo güncelleştirme bir CTAS 'ye dönüştürüldü, böylece minimum günlük kaydı mümkün olur.

Bu durumda, tablodaki satışa geriye dönük olarak bir indirim miktarı ekliyoruz:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Büyük tabloları yeniden oluşturmak SQL veri ambarı iş yükü yönetimi özelliklerini kullanmaktan faydalanabilir. Daha fazla bilgi için bkz. [iş yükü yönetimi Için kaynak sınıfları](resource-classes-for-workload-management.md).
> 
> 

## <a name="optimizing-with-partition-switching"></a>Bölüm değiştirme ile iyileştirme
Bir [tablo bölümü](sql-data-warehouse-tables-partition.md)içinde büyük ölçekli değişikliklerle karşılaşırsanız, bölüm değiştirme deseninin mantıklı olması gerekir. Veri değişikliği önemli ise ve birden çok bölüme yayılıyorsa, bölümler üzerinde yineleme aynı sonuca ulaşır.

Bölüm anahtarı gerçekleştirme adımları aşağıdaki gibidir:

1. Boş bir bölüm oluşturma
2. ' Update ' i CTAS olarak gerçekleştir
3. Varolan verileri Out tablosuna geçir
4. Yeni verilerde geçiş yap
5. Verileri temizleme

Ancak, geçiş yapmak için bölümleri belirlemek üzere aşağıdaki yardımcı yordamı oluşturun.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Bu yordam, kod yeniden kullanımını en üst düzeye çıkarır ve bölüm anahtarlama örneğini daha küçük tutar.

Aşağıdaki kod, bir tam bölüm değiştirme yordamının elde edilebilmesi için daha önce bahsedilen adımları gösterir.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Küçük toplu işlerle günlüğü en aza indirme
Büyük veri değiştirme işlemleri için, iş biriminin kapsamını oluşturmak üzere işlemi parçalara ayırmak veya toplu işlemlere Bölmek mantıklı olabilir.

Aşağıdaki kod, çalışan bir örnektir. Toplu iş boyutu, tekniği vurgulamak için önemsiz bir sayı olarak ayarlanmıştır. Gerçekte, toplu iş boyutu önemli ölçüde daha büyük olacaktır. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Kılavuzu duraklatma ve ölçeklendirme
Azure SQL veri ambarı, veri Ambarınızı isteğe bağlı olarak [duraklatmanıza, sürdürmenize ve ölçeklendirmenize](sql-data-warehouse-manage-compute-overview.md) olanak tanır. SQL veri Ambarınızı duraklattığınızda veya ölçeklendirirseniz, tüm uçuş işlemleri hemen sonlandırıldığını anlamak önemlidir; açık işlemlerin geri alınmasına neden olur. İş yükünüz, duraklatma veya ölçeklendirme işleminden önce uzun süre çalışan ve tamamlanmamış veri değişikliği yaptıysanız, bu işin geri yüklenmesi gerekir. Bu geri alma, Azure SQL veri ambarı veritabanınızı duraklatmak veya ölçeklendirmek için gereken süreyi etkileyebilir. 

> [!IMPORTANT]
> Hem `UPDATE` hem de `DELETE` tamamen günlüğe kaydedilir ve bu geri alma/yineleme işlemleri, eşdeğer en düşük düzeyde günlüğe kaydedilmiş işlemden çok daha uzun sürebilir. 
> 
> 

En iyi senaryo, SQL veri ambarını duraklatmadan veya ölçeklendirmeden önce uçuş verileri değiştirme işlemlerinin tamamlanmasını sağlar. Ancak, bu senaryo her zaman pratik olmayabilir. Uzun geri alma riskini azaltmak için aşağıdaki seçeneklerden birini göz önünde bulundurun:

* [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) kullanarak uzun çalışan işlemleri yeniden yazma
* İşlemi parçalara bölün; satırların bir alt kümesi üzerinde çalışma

## <a name="next-steps"></a>Sonraki adımlar
Yalıtım düzeyleri ve işlem sınırları hakkında daha fazla bilgi edinmek için bkz. [SQL veri ambarı 'Nda işlemler](sql-data-warehouse-develop-transactions.md) .  Diğer En Iyi yöntemlere genel bakış için bkz. [SQL veri ambarı En Iyi uygulamaları](sql-data-warehouse-best-practices.md).


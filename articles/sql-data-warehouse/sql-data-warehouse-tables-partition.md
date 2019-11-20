---
title: Tabloları bölümleme
description: Azure SQL veri ambarı 'nda tablo bölümlerinin kullanılmasına yönelik öneriler ve örnekler.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7ec313094a9ebc05f966e0c49f44284909ca778f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685425"
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>SQL veri ambarı 'nda tabloları bölümleme
Azure SQL veri ambarı 'nda tablo bölümlerinin kullanılmasına yönelik öneriler ve örnekler.

## <a name="what-are-table-partitions"></a>Tablo bölümleri nelerdir?
Tablo bölümleri, verilerinizi daha küçük veri gruplarına bölmenizi sağlar. Çoğu durumda, tablo bölümleri bir tarih sütununda oluşturulur. Bölümlendirme tüm SQL veri ambarı tablo türlerinde desteklenir; kümelenmiş columnstore, kümelenmiş dizin ve yığın dahil. Hem karma hem de hepsini bir kez deneme dağıtımı dahil olmak üzere tüm dağıtım türlerinde bölümlendirme de desteklenir.  

Bölümlendirme, veri bakımı ve sorgu performansı avantajına sahip olabilir. Yalnızca bir sütundan yalnızca bir sütunda yapılabileceğinizden, verilerin nasıl yüklenedüğüne ve aynı sütunun her iki amaçla de kullanılıp kullanılamayacağını belirtir.

### <a name="benefits-to-loads"></a>Yüklerin avantajları
SQL veri ambarı 'nda bölümlemenin birincil avantajı, bölüm silme, değiştirme ve birleştirme kullanarak veri yükleme verimliliğini ve performansını artırmaktır. Çoğu durumda veriler, verilerin veritabanına yüklendiği sıraya yakın bir tarih sütununda bölümlenir. Bölüm kullanmanın en büyük avantajlarından biri, verileri işlem günlüğü 'nün engelleme. Yalnızca veri ekleme, güncelleştirme veya silme en kolay yaklaşım olabilir. Bu işlem, kısa bir süre sonra, yükleme işleminiz sırasında bölümlemenin kullanılması performansı önemli ölçüde iyileştirebilir.

Bölüm değiştirme, bir tablonun bir bölümünü hızlıca kaldırmak veya değiştirmek için kullanılabilir.  Örneğin, bir Sales olgu tablosu yalnızca son 36 aya ait verileri içerebilir. Her ayın sonunda, satış verilerinin en eski ayı tablodan silinir.  Bu veriler, en eski aya ilişkin verileri silmek için bir Delete ifadesiyle silinebilir. Ancak, bir Delete ifadesiyle büyük miktarda veri satırını satır içinde silmek çok fazla zaman alabilir ve bir şeyler yanlış olursa geri alınması uzun zaman alan büyük işlemler riskini oluşturabilir. En iyi yöntem, en eski veri bölümünü düşürülemedir. Tek tek satırları silmenin saat sürebileceği durumlarda, bölümün tamamının silinmesi saniye sürebilir.

### <a name="benefits-to-queries"></a>Sorguların avantajları
Sorgu performansını artırmak için bölümleme de kullanılabilir. Bölümlenmiş verilere filtre uygulayan bir sorgu, taramayı yalnızca uygun bölümlerle sınırlayabilir. Bu filtreleme yöntemi bir tam tablo taramasını önleyebilir ve yalnızca daha küçük bir veri alt kümesini tarayabilir. Kümelenmiş columnstore dizinlerinin tanıtılmasıyla, koşulun eleme performansı avantajları daha az faydalıdır, ancak bazı durumlarda sorgulara bir avantaj olabilir. Örneğin, Sales olgu tablosu satış tarihi alanı kullanılarak 36 ay içinde bölümleniyorsa, satış tarihini filtreleyen sorgular filtreyle eşleşmeyen bölümlerde aramayı atlayabilir.

## <a name="sizing-partitions"></a>Bölümleri boyutlandırma
Bölümleme performansı artırmak için, bazı senaryolarda **çok fazla** bölüm içeren bir tablo oluşturmak, bazı koşullarda performansı etkileyebilir.  Bu sorunlar özellikle kümelenmiş columnstore tabloları için geçerlidir. Bölümlemenin yararlı olması için bölümleme ve oluşturulacak bölüm sayısının ne zaman kullanılacağını anlamak önemlidir. Birçok bölüm çok fazla olan bir sabit hızlı kural yoktur, bu, verilerinize ve aynı anda kaç bölümden fazla yükleme yapadığınıza bağlıdır. Başarılı bir bölümlendirme şeması genellikle binlerce bölüm olan binlerce bölümden oluşur.

**Kümelenmiş columnstore** tablolarında bölümler oluştururken, her bölüme ait kaç satır olduğunu göz önünde bulundurmanız önemlidir. Kümelenmiş columnstore tablolarının en iyi sıkıştırması ve performansı için, dağıtım ve bölüm başına en az 1.000.000 satır gerekir. Bölümler oluşturulmadan önce SQL veri ambarı, her tabloyu zaten 60 dağıtılmış veritabanına böler. Bir tabloya eklenen herhangi bir bölümleme, arka planda oluşturulan dağıtımların yanı sıra. Bu örneği kullanarak, Sales olgu tablosu 36 aylık bölümler içeriyorsa ve SQL Data Warehouse 'ın 60 dağıtımları varsa, Sales olgu tablosu ayda 60.000.000 satır veya tüm aylar doldurulduğunda 2.100.000.000 satır içermelidir. Bir tablo, bölüm başına önerilen en az sayıda satır içeriyorsa, bölüm başına satır sayısını artırmak için daha az bölüm kullanmayı göz önünde bulundurun. Daha fazla bilgi için, küme columnstore dizinlerinin kalitesini değerlendirebilen sorgular içeren [Dizin oluşturma](sql-data-warehouse-tables-index.md) makalesine bakın.

## <a name="syntax-differences-from-sql-server"></a>SQL Server söz dizimi farklılıkları
SQL veri ambarı, SQL Server daha basit olan bölümleri tanımlamak için bir yol sunar. Bölümlendirme işlevleri ve şemaları, SQL Server olduklarından SQL veri ambarı 'nda kullanılmaz. Bunun yerine, tek yapmanız gereken bölümlenmiş sütunu ve sınır noktalarını belirlemektir. Bölümleme sözdizimi SQL Server biraz farklı olabilir, ancak temel kavramlar aynıdır. SQL Server ve SQL veri ambarı, her tablo için bir bölüm sütununu destekler ve bu bölüm, sıralaştırılmış bölümdür. Bölümlendirme hakkında daha fazla bilgi için bkz. [bölümlenmiş tablolar ve dizinler](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

Aşağıdaki örnek, OrderDateKey sütunundaki FactInternetSales tablosunu bölümlemek için [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) ifadesini kullanır:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>SQL Server bölümleme geçiriliyor
SQL Server bölüm tanımlarını SQL Data Warehouse 'a geçirmek için:

- SQL Server [bölüm düzenini](/sql/t-sql/statements/create-partition-scheme-transact-sql)kaldırın.
- [Bölüm işlev](/sql/t-sql/statements/create-partition-function-transact-sql) tanımını Create Table ekleyin.

Bölümlenmiş bir tabloyu SQL Server örneğinden geçiriyorsanız, aşağıdaki SQL her bölümde bulunan satır sayısını bulmanıza yardımcı olabilir. SQL veri ambarı 'nda aynı bölümleme ayrıntı düzeyi kullanılıyorsa, bölüm başına satır sayısı 60 faktörüyle azaldığını aklınızda bulundurun.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Bölüm değiştirme
SQL veri ambarı bölüm bölmeyi, birleştirmeyi ve değiştirmeyi destekler. Bu işlevlerin her biri [alter table](/sql/t-sql/statements/alter-table-transact-sql) ifadesi kullanılarak yürütülür.

Bölümleri iki tablo arasında değiştirmek için bölümlerin ilgili sınırlarına göre hizalanmasına ve tablo tanımlarının eşleştiğinden emin olmanız gerekir. Denetim kısıtlamaları tablodaki değer aralığını zorlamak için kullanılabilir olmadığından, kaynak tablo hedef tabloyla aynı bölüm sınırlarını içermelidir. Bölüm sınırları aynı değilse, Bölüm meta verileri eşitlenmediği için bölüm anahtarı başarısız olur.

### <a name="how-to-split-a-partition-that-contains-data"></a>Veri içeren bir bölümü bölme
Zaten veri içeren bir bölümü bölmek için en verimli yöntem `CTAS` bir ifade kullanmaktır. Bölümlenmiş tablo kümelenmiş bir columnstore ise, bölünebilmesi için tablo bölümünün boş olması gerekir.

Aşağıdaki örnek bölümlenmiş bir columnstore tablosu oluşturur. Her bölüme bir satır ekler:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

Aşağıdaki sorgu `sys.partitions` katalog görünümünü kullanarak satır sayısını bulur:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Aşağıdaki Split komutu bir hata iletisi alır:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Bölüm boş olmadığından, # 35346, düzey 15, durum 1, ALTER PARTITION ifadesinin Line 44 SPLIT yan tümcesi başarısız oldu. Tabloda bir columnstore dizini mevcut olduğunda yalnızca boş bölümler bölünebilir. ALTER PARTıTıON deyimini yürütmeden önce columnstore dizinini devre dışı bırakmayı, ardından ALTER PARTıTıON tamamlandıktan sonra columnstore dizinini yeniden oluşturmayı düşünün.

Ancak, verileri tutacak yeni bir tablo oluşturmak için `CTAS` kullanabilirsiniz.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Bölüm sınırları hizalandığı için bir anahtara izin verilir. Böylece kaynak tablo, daha sonra bölünebileceğiniz boş bir bölüm ile bırakılır.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Her şey, `CTAS`kullanarak verileri yeni bölüm sınırlarına hizalamak ve sonra verileri ana tabloya geri geçirmek için kullanılır.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Verilerin hareketini tamamladıktan sonra, hedef tablodaki istatistikleri yenilemek iyi bir fikirdir. İstatistiklerin güncelleştirilmesi, istatistiklerin ilgili bölümlerinde verilerin yeni dağıtımını doğru şekilde yansıtmasını sağlar.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Verileri tek bir adımda içeren bölümlere yeni verileri yükleme
Bölüm değiştirme ile verileri bölümlere yükleme, kullanıcıların yeni verilerde anahtara görünmeyen bir tabloda yeni verileri aşamalandırmaya uygun bir yoldur.  Bölüm geçişle ilişkili kilitleme çekişmesiyle uğraşmak için meşgul sistemler zor olabilir.  Bir bölümdeki mevcut verileri temizlemek için, verileri değiştirmek üzere kullanılan bir `ALTER TABLE`.  Yeni verilerde geçiş yapmak için başka bir `ALTER TABLE` gerekiyordu.  SQL veri ambarı 'nda `TRUNCATE_TARGET` seçeneği, `ALTER TABLE` komutunda desteklenir.  `TRUNCATE_TARGET` `ALTER TABLE` komutu, bölümdeki mevcut verilerin yeni verilerle üzerine yazar.  Aşağıda, var olan verileri içeren yeni bir tablo oluşturmak, yeni veriler eklemek ve ardından tüm verileri yeniden hedef tabloya dönüştürmek, mevcut verilerin üzerine yazmak için `CTAS` kullanan bir örnek verilmiştir.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Tablo bölümleme kaynak denetimi
Kaynak denetim sisteminizde tablo tanımınızın bir engel olmasını önlemek için aşağıdaki yaklaşımı göz önünde bulundurmanız gerekebilir:

1. Tabloyu bölümlenmiş tablo olarak oluştur, ancak bölüm değeri yok

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. dağıtım işleminin bir parçası olarak tabloyu `SPLIT`:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Bu yaklaşımla kaynak denetimindeki kod statik kalır ve bölümleme sınırı değerlerinin dinamik olmasına izin verilir; zaman içinde ambarla gelişiyor.

## <a name="next-steps"></a>Sonraki adımlar
Tablo geliştirme hakkında daha fazla bilgi için bkz. makaleye [genel bakış](sql-data-warehouse-tables-overview.md).


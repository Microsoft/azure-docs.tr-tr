---
title: SELECT OLARAK CREATE TABLE (CTAS)
description: Çözüm geliştirmeye yönelik Azure SQL veri ambarı 'nda SELECT AS SELECT (CTAS) bildiriminin açıklaması ve CREATE TABLE örnekleri.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 4992bb00fa5397ef6a4e055e08b445d35f5ed77a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685855"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda SELECT (CTAS) olarak CREATE TABLE

Bu makalede, çözümlerin geliştirilmesi için Azure SQL veri ambarı 'nda SELECT (CTAS) T-SQL ifadesiyle CREATE TABLE açıklanmaktadır. Makale Ayrıca kod örnekleri de sağlar.

## <a name="create-table-as-select"></a>SEÇIM OLARAK CREATE TABLE

[Select as Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) bildirisi, kullanılabilen en önemli T-SQL özelliklerinden biridir. Create Table CTAS, SELECT ifadesinin çıktısına göre yeni bir tablo oluşturan paralel bir işlemdir. CTAS, tek bir komutla bir tabloya veri oluşturup eklemenin en basit ve en hızlı yoludur.

## <a name="selectinto-vs-ctas"></a>Seç... Vs. CTAS 'A

CTAS, Select... öğesinin daha özelleştirilebilir bir sürümüdür [. INTO](/sql/t-sql/queries/select-into-clause-transact-sql) bildirisi.

Aşağıda basit bir seçme örneği verilmiştir... BIRLEŞTIRIN

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Seç... ' De, dağıtım yöntemini veya dizin türünü işlemin bir parçası olarak değiştirmenize izin vermez. Varsayılan ROUND_ROBIN dağıtım türünü ve KÜMELENMIŞ COLUMNSTORE DIZINININ varsayılan tablo yapısını kullanarak `[dbo].[FactInternetSales_new]` oluşturursunuz.

CTAS ile, diğer yandan tablo verilerinin hem dağıtımını hem de tablo yapısı türünü belirtebilirsiniz. Önceki örneği CTAS 'ya dönüştürmek için:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Yalnızca CTAS işleminde dizini değiştirmeye çalışıyorsanız ve kaynak tablo karma olarak dağıtılmışsa, aynı dağıtım sütununu ve veri türünü koruyun. Bu, işlem sırasında çapraz dağıtım veri hareketini önler ve daha etkilidir.

## <a name="use-ctas-to-copy-a-table"></a>Bir tabloyu kopyalamak için CTAS kullanma

Belki de en yaygın CTAS kullanımları, DDL 'yi değiştirmek için bir tablonun bir kopyasını oluşturuyor. İlk olarak tablonuzu `ROUND_ROBIN`olarak oluşturduğunuzu ve şimdi bir sütunda dağıtılan bir tabloyla değiştirmek istediğinizi varsayalım. CTAS, dağıtım sütununu değiştirme. Ayrıca, CTAS 'yi bölümleme, dizin oluşturma veya sütun türlerini değiştirmek için de kullanabilirsiniz.

Bu tabloyu, `CREATE TABLE`dağıtım sütunu belirtmeksizin `ROUND_ROBIN`varsayılan dağıtım türünü kullanarak oluşturduğunuzu varsayalım.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Artık bu tablonun yeni bir kopyasını oluşturmak için `Clustered Columnstore Index`, kümelenmiş columnstore tablolarının performansının avantajlarından yararlanabilirsiniz. Ayrıca, bu sütunda benimsemeyi bekleme birleşimler olduğu ve `ProductKey`birleştirme sırasında veri hareketini önlemek istediğiniz için bu tabloyu `ProductKey`dağıtmak istiyorsunuz. Son olarak, eski bölümleri bırakarak eski verileri hızlıca silebilmeniz için `OrderDateKey`bölümlemeyi de eklemek istersiniz. Eski tablonuzu yeni bir tabloya kopyalayan CTAS deyimleri aşağıda verilmiştir.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Son olarak, yeni tablonuzu değiştirmek için tablolarınızı yeniden adlandırabilir ve sonra eski tablonuzu bırakabilirsiniz.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Desteklenmeyen özellikleri geçici olarak çözmek için CTAS kullanın

CTAS 'yi, aşağıda listelenen desteklenmeyen özelliklerin bir sayısını çözmek için de kullanabilirsiniz. Yalnızca kodunuzun uyumlu olması, ancak SQL Data Warehouse üzerinde daha hızlı çalışması için bu yöntem genellikle yararlı olabilir. Bu performans, tam paralelleştirilmiş tasarımın bir sonucudur. Senaryolar şunlardır:

* Güncelleştirmeler üzerinde ANSI BIRLEŞTIRMELERI
* Silmeler üzerinde ANSI birleştirmeleri
* MERGE ekstresi

> [!TIP]
> Önce "CTAS" öğesini düşünmek deneyin. Sonuç olarak daha fazla veri yazıyorsanız bile CTAS kullanarak bir sorunun çözülmesi genellikle iyi bir yaklaşımdır.

## <a name="ansi-join-replacement-for-update-statements"></a>Update deyimleri için ANSI JOIN değiştirme

Karmaşık bir güncelleştirmeniz olduğunu fark edebilirsiniz. Güncelleştirme, GÜNCELLEŞTIRMEYI veya SILMEYI gerçekleştirmek için ANSI JOIN söz dizimini kullanarak ikiden fazla tabloyu birleştirir.

Bu tabloyu güncelleştirmek zorunda kaldığınızı düşünün:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

Özgün sorgu Şu örneğe benzer bir şey bakmış olabilir:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

SQL veri ambarı, bir `UPDATE` deyimin `FROM` yan tümcesinde ANSI birleştirmeleri desteklemez, bu nedenle önceki örneği değiştirmeden kullanamazsınız.

Önceki örneği değiştirmek için CTAS ve örtük bir birleşimin birleşimini kullanabilirsiniz:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Delete deyimleri için ANSI JOIN değiştirme

Bazen, verileri silmenin en iyi yaklaşımı, özellikle ANSI JOIN söz dizimini kullanan `DELETE` deyimleri için CTAS kullanmaktır. Bunun nedeni, SQL veri ambarının bir `DELETE` bildiriminin `FROM` yan tümcesinde ANSI birleştirmelerini desteklemezler. Verileri silmek yerine, tutmak istediğiniz verileri seçin.

Dönüştürülmüş `DELETE` bildirimine bir örnek aşağıda verilmiştir:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Merge deyimlerini Değiştir

Birleştirme deyimlerini CTAS kullanarak en az kısmen bir şekilde değiştirebilirsiniz. `INSERT` ve `UPDATE` tek bir deyime birleştirebilirsiniz. Tüm silinen kayıtlar `SELECT` deyimden, sonuçlardan atlamak için kısıtlanmalıdır.

Aşağıdaki örnek bir `UPSERT`içindir:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Açık durum veri türü ve Nullable çıkış

Kodu geçirirken, bu tür bir kodlama düzeniyle çalıştırıldığını fark edebilirsiniz:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Bu kodu CTAS 'ye geçirmeniz gerektiğini ve doğru olduğunu düşünebilirsiniz. Ancak burada gizli bir sorun var.

Aşağıdaki kod aynı sonucu vermez:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

"Result" sütununun, ifadenin veri türünü ve null değer alabilme değerlerini iletdiğine dikkat edin. Veri türü iletme, dikkatli değilseniz değerlerde hafif sapmaya yol açabilir.

Şu örneği deneyin:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Sonuç için depolanan değer farklı. Sonuç sütunundaki kalıcı değer diğer ifadelerde kullanıldığında, hata daha da önemli olur.

![CTAS sonuçlarının ekran görüntüsü](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Bu, veri geçişleri için önemlidir. İkinci sorgu daha doğru bir şekilde daha doğru olsa da bir sorun oluştu. Veriler, kaynak sistemle karşılaştırıldığında farklılık gösterir ve bu, geçiş sırasında bütünlük sorularına yol açar. Bu, "yanlış" yanıtın gerçekten doğru bir şekilde olduğu nadir durumlardan biridir!

İki sonuç arasında bir eşlik görtiğimiz neden örtük tür atama nedeniyle oluşur. İlk örnekte tablo, sütun tanımını tanımlar. Satır eklendiğinde, örtük bir tür dönüştürmesi oluşur. İkinci örnekte, ifadesi sütunun veri türünü tanımladığından örtük bir tür dönüştürmesi yoktur.

Ayrıca, ikinci örnekteki sütunun null yapılabilir bir sütun olarak tanımlandığından, ancak ilk örnekte olmadığına dikkat edin. Tablo ilk örnekte oluşturulduğunda, null değer alabilirlik açık bir şekilde tanımlanmıştır. İkinci örnekte, ifadeye ayrılmıştı ve varsayılan olarak NULL tanımına neden olur.

Bu sorunları çözmek için CTAS bildiriminin SELECT bölümünde tür dönüştürme ve null değer alabilirlik ' i açıkça ayarlamanız gerekir. Bu özellikleri ' CREATE TABLE ' içinde ayarlayamazsınız.
Aşağıdaki örnek, kodun nasıl düzeltileceğini göstermektedir:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Şunlara dikkat edin:

* CAST veya CONVERT kullanabilirsiniz.
* Null değer verilebilirliği zorlamak için ıSNULL, BIRLEŞIM değil ' i kullanın. Aşağıdaki nota bakın.
* ISNULL, en dıştaki işlevdir.
* ISNULL 'nin ikinci bölümü bir sabittir, 0.

> [!NOTE]
> Null değer, doğru şekilde ayarlanverilebilmesi için, ıSNULL ve BIRLEŞIM değil kullanılması çok önemlidir. BIRLEŞIM belirleyici bir işlev değildir ve bu nedenle, ifadenin sonucu her zaman null yapılabilir olur. ISNULL farklı. Belirleyici. Bu nedenle, ıSNULL işlevinin ikinci bölümü bir sabit veya değişmez değer olduğunda, sonuç değeri NULL olmaz.

Hesaplamalarınızın bütünlüğünden emin olmak tablo bölümü değiştirme için de önemlidir. Bu tablonun olgu tablosu olarak tanımlandığını düşünün:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Ancak, tutar alanı hesaplanmış bir ifadedir. Kaynak verilerin bir parçası değildir.

Bölümlenmiş veri kümenizi oluşturmak için aşağıdaki kodu kullanmak isteyebilirsiniz:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Sorgu sorunsuz bir şekilde çalışır. Bu sorun, Bölüm anahtarını yapmayı denediğinizde gönderilir. Tablo tanımları eşleşmiyor. Tablo tanımlarının eşleşmesini sağlamak için CTAS 'yi, sütunun null olabilme özniteliğini korumak üzere bir `ISNULL` işlevi ekleyecek şekilde değiştirin.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]   
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Tür tutarlılığı ve bir CTAS üzerinde null değer alabilme özelliklerinin sürdürülmesi, mühendisliğin en iyi yöntemidir. Hesaplamalarınızda tutarlılığın sağlanmasına yardımcı olur ve ayrıca bölüm geçişinin mümkün olmasını sağlar.

CTAS, SQL Data Warehouse 'daki en önemli ifadelerden biridir. Kapsamlı olarak anladığınızdan emin olun. Bkz. [CTAS belgeleri](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md)bölümüne bakın.


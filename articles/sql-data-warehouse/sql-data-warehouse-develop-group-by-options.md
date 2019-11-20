---
title: Gruplandırma ölçütü seçeneklerini kullanma
description: Çözümleri geliştirmek için Azure SQL veri ambarı 'nda grup seçeneklerini uygulamaya yönelik ipuçları.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8d5d0f28fa210a56460ced25323b96a68f245895
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685841"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>SQL veri ambarı 'nda gruplandırma ölçütü seçenekleri
Çözümleri geliştirmek için Azure SQL veri ambarı 'nda grup seçeneklerini uygulamaya yönelik ipuçları.

## <a name="what-does-group-by-do"></a>GROUP BY ne yapar?

[Group By](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL yan tümcesi, verileri bir Özet satır kümesine toplar. GROUP BY, SQL Data Warehouse tarafından desteklenmeyen bazı seçeneklere sahiptir. Bu seçeneklerde geçici çözümler vardır.

Bu seçenekler şunlardır

* TOPLAMASıYLA gruplandırma ölçütü
* GRUPLANDıRMA KÜMELERI
* KÜP ile gruplandırma ölçütü

## <a name="rollup-and-grouping-sets-options"></a>Toplama ve gruplandırma kümeleri seçenekleri
Buradaki en basit seçenek, açık sözdizimine güvenmek yerine, toplamayı gerçekleştirmek yerine UNıON ALL kullanmaktır. Sonuç tamamen aynıdır

Aşağıdaki örnek, toplama seçeneği ile GROUP BY ifadesini kullanıyor:
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Önceki örnek, ROLLUP kullanarak aşağıdaki toplamaları ister:

* Ülke ve bölge
* Ülke
* Genel Toplam

TOPLAMAYı değiştirmek ve aynı sonuçları döndürmek için UNıON ALL ' ı kullanabilir ve gerekli toplamaları açıkça belirtebilirsiniz:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Gruplandırma KÜMELERINI değiştirmek için, örnek ilke uygulanır. Yalnızca görmek istediğiniz toplama düzeyleri için UNıON ALL bölümlerini oluşturmanız yeterlidir.

## <a name="cube-options"></a>Küp seçenekleri
UNıON ALL yaklaşımını kullanarak KÜPLE bir grup oluşturmak mümkündür. Bu sorun, kodun hızlı bir şekilde hızla ve farkında hale gelmesine neden olabilir. Bunu azaltmak için daha gelişmiş bir yaklaşım kullanabilirsiniz.

Yukarıdaki örneği kullanalım.

İlk adım, oluşturmak istediğimiz tüm toplama düzeylerini tanımlayan ' Cube ' öğesini tanımlamaktır. İki türetilmiş tablonun çapraz BIRLEŞTIRMESINI göz önünde bulundurulmak önemlidir. Bu, bizim için tüm düzeyleri oluşturur. Kodun geri kalanı gerçekten biçimlendirme içindir.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Aşağıda CTAS 'ın sonuçları gösterilmektedir:

![Küpe göre Gruplandır](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

İkinci adım, geçici sonuçları depolamak için bir hedef tablo belirtmektir:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Üçüncü adım, toplama işlemi gerçekleştiren sütun küpümüzden fazla döngüdür. Sorgu #Cube geçici tablodaki her satır için bir kez çalışır ve sonuçları #Results geçici tabloda depolar

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Son olarak, yalnızca #Results geçici tablodan okurken sonuçları döndürebilirsiniz

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Kodu bölümlere ayırarak ve döngü yapısı oluşturarak, kod daha yönetilebilir ve sürdürülebilir hale gelir.

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).


---
title: Gruplandırma ölçütü seçeneklerini kullanma
description: Azure SYNAPSE Analytics 'te adanmış SQL havuzları için Grup seçeneklerini uygulamaya yönelik ipuçları.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3f0879aa9b6f9e084d0c51f0bb371740d333c1b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683265"
---
# <a name="group-by-options-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzları için gruplandırma ölçütü seçenekleri

Bu makalede, adanmış SQL havuzlarında grup seçeneklerini uygulamaya yönelik ipuçları bulacaksınız.

## <a name="what-does-group-by-do"></a>GROUP BY ne yapar?

[Group By](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL yan tümcesi, verileri bir Özet satır kümesine toplar. GROUP BY, adanmış SQL havuzunun desteklemediği bazı seçeneklere sahiptir. Bu seçeneklerde aşağıdaki gibi geçici çözümler bulunur:

* TOPLAMASıYLA gruplandırma ölçütü
* GRUPLANDıRMA KÜMELERI
* KÜP ile gruplandırma ölçütü

## <a name="rollup-and-grouping-sets-options"></a>Toplama ve gruplandırma kümeleri seçenekleri

Buradaki en basit seçenek, açık sözdizimine güvenmek yerine, toplamayı gerçekleştirmek için UNıON ALL kullanmaktır. Sonuç tamamen aynıdır.

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

UNıON ALL yaklaşımını kullanarak KÜPLE bir grup oluşturmak mümkündür. Bu sorun, kodun hızlı bir şekilde hızla ve farkında hale gelmesine neden olabilir. Bu sorunu azaltmak için daha gelişmiş bir yaklaşım kullanabilirsiniz.

Önceki örneği kullanarak, ilk adım oluşturmak istediğimiz tüm toplama düzeylerini tanımlayan ' Cube ' öğesini tanımlamaktır.

Bu iki türetilmiş tablonun çapraz BIRLEŞTIRMESINI göz önünde bulunun çünkü bu, bizim için tüm düzeyleri oluşturur. Kodun geri kalanı biçimlendirme için mevcuttur:

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

Aşağıdaki görüntüde CTAS sonuçları gösterilmektedir:

![Küpe göre Gruplandır](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

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

Üçüncü adım, toplama işlemi gerçekleştiren sütun küpümüzden fazla döngüdür. Sorgu #Cube geçici tablodaki her satır için bir kez çalışır. Sonuçlar #Results geçici tabloda depolanır:

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

Son olarak, #Results geçici tablodan okurken sonuçları döndürebilirsiniz:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Kodu bölümlere ayırarak ve bir döngü yapısı oluşturarak, kod daha yönetilebilir ve sürdürülebilir hale gelir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

---
title: SYNAPSE SQL 'de geçici tablolar kullanma
description: SYNAPSE SQL 'de geçici tabloları kullanmaya yönelik temel kılavuz.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8513df83196b3521a749515c6bb22caad7d255b7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816053"
---
# <a name="temporary-tables-in-synapse-sql"></a>SYNAPSE SQL 'de geçici tablolar

Bu makale, geçici tabloları kullanmaya yönelik temel bir kılavuz içerir ve SYNAPSE SQL içindeki oturum düzeyi geçici tablolarının ilkelerini vurgular. 

Hem adanmış SQL havuzu hem de sunucusuz SQL havuzu kaynakları geçici tabloları kullanabilir. Sunucusuz SQL havuzunda, bu makalenin sonunda ele alınan sınırlamalar vardır. 

## <a name="temporary-tables"></a>Geçici tablolar

Geçici tablolar, özellikle de ara sonuçların geçici olduğu dönüştürme sırasında verileri işlerken yararlı olur. SYNAPSE SQL ile, geçici tablolar oturum düzeyinde bulunur.  Bunlar yalnızca oluşturuldukları oturum tarafından görülebilir. Bu nedenle, bu oturum kapattığında otomatik olarak bırakılır. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Adanmış SQL havuzundaki geçici tablolar

Adanmış SQL havuzu kaynağında, sonuçları uzak depolama yerine yerel olarak yazıldığı için geçici tablolar bir performans avantajı sunar.

### <a name="create-a-temporary-table"></a>Geçici tablo oluşturma

Geçici tablolar, tablo adınızın bir ile önek olarak eklenerek oluşturulur `#` .  Örnek:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Geçici tablolar `CTAS` , tam olarak aynı yaklaşımla birlikte kullanılarak da oluşturulabilir:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` güçlü bir komuttur ve işlem günlüğü alanı kullanımıyla ilgili verimli bir avantajdır. 
> 
> 

### <a name="drop-temporary-tables"></a>Geçici tabloları bırak

Yeni bir oturum oluşturulduğunda geçici tabloların olmaması gerekir.  Bununla birlikte, aynı ada sahip bir geçici oluşturan aynı saklı yordamı arıyorsanız, deyimlerinizin başarılı olduğundan emin olmak için, `CREATE TABLE` ile basit bir ön varlık denetimi kullanın  `DROP` : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Kodlama tutarlılığı için, bu düzenin hem tablolar hem de geçici tablolar için kullanılması iyi bir uygulamadır.  Ayrıca `DROP TABLE` , ile işiniz bittiğinde geçici tabloları kaldırmak için kullanmak iyi bir fikirdir.  

Saklı yordam geliştirmede, bu nesnelerin temizlendiğinden emin olmak için bir yordamın sonunda bırakma komutlarının birlikte paketlenmiştir.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Kodu modüle leştir

Geçici tablolar, kullanıcı oturumunda herhangi bir yerde kullanılabilir. Bu özellik daha sonra uygulama kodunuzu modüler hale getirmenize yardımcı olmak için kullanılabilir.  Göstermek için aşağıdaki saklı yordam DDL 'yi, veritabanındaki tüm istatistikleri istatistik adına göre güncelleştirmek üzere oluşturur:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    #stats_ddl
;
GO
```

Bu aşamada, oluşan tek eylem, #stats_ddl geçici tablo üreten bir saklı yordamın oluşturulması olur.  Saklı yordam zaten varsa #stats_ddl bırakır. Bu bırakma, bir oturum içinde birden fazla kez çalıştırıldığında başarısız olmamasını sağlar.  

`DROP TABLE`Saklı yordamın sonunda, saklı yordam tamamlandığında, oluşturulan tablo kalır ve saklı yordamın dışında okunabilir.  

Diğer SQL Server veritabanlarının aksine, SYNAPSE SQL, geçici tabloyu onu oluşturan yordamın dışında kullanmanıza olanak sağlar.  Adanmış SQL havuzu aracılığıyla oluşturulan geçici tablolar, oturum içinde **herhangi bir yerde** kullanılabilir. Sonuç olarak, aşağıdaki örnekte gösterildiği gibi daha modüler ve yönetilebilir kod elde edersiniz:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

### <a name="temporary-table-limitations"></a>Geçici tablo sınırlamaları

Adanmış SQL havuzu geçici tablolar için birkaç uygulama kısıtlamalarına sahiptir:

- Yalnızca oturum kapsamlı geçici tablolar desteklenir.  Genel geçici tablolar desteklenmez.
- Görünümler geçici tablolarda oluşturulamaz.
- Geçici tablolar yalnızca karma veya hepsini bir kez deneme dağıtımı ile oluşturulabilir.  Çoğaltılan geçici tablo dağıtımı desteklenmiyor. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Sunucusuz SQL havuzundaki geçici tablolar

Sunucusuz SQL havuzundaki geçici tablolar desteklenir, ancak kullanımları sınırlıdır. Dosyaları hedef olan sorgularda kullanılamaz. 

Örneğin, depolama alanındaki dosyalardaki verilerle geçici bir tabloya katılamaz. Geçici tablo sayısı 100 ile sınırlıdır ve toplam boyutu 100 MB ile sınırlıdır.

## <a name="next-steps"></a>Sonraki adımlar

Tablo geliştirme hakkında daha fazla bilgi edinmek için [SYNAPSE SQL kaynaklarını kullanarak tabloları tasarlama](develop-tables-overview.md) makalesini inceleyin.


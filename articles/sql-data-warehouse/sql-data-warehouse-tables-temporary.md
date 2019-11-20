---
title: Geçici tablolar
description: Azure SQL veri ambarı 'nda geçici tabloları kullanmaya yönelik temel kılavuz, oturum düzeyi geçici tablolarının ilkelerini vurgulamalıdır.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 23a5825a32c602f70aff1d9f577ce13d3e9f2260
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685439"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>SQL Data Warehouse 'da geçici tablolar
Bu makale, geçici tabloları kullanmaya yönelik temel kılavuz içerir ve oturum düzeyi geçici tablolarının ilkelerini vurgular. Bu makaledeki bilgilerin kullanılması, kodunuzun kodlarınızın hem yeniden kullanılabilirliğini hem de bakımının kolayca bakımını iyileştirmeye yardımcı olabilir.

## <a name="what-are-temporary-tables"></a>Geçici tablolar nelerdir?
Geçici tablolar, özellikle ara sonuçların geçici olduğu dönüştürme sırasında verileri işlerken yararlı olur. SQL veri ambarı 'nda, geçici tablolar oturum düzeyinde bulunur.  Bunlar yalnızca oluşturuldukları oturum için görünür ve oturum kapattığında otomatik olarak bırakılır.  Geçici tablolar, sonuçları uzak depolama yerine yerel olarak yazıldığı için bir performans avantajı sunar.

## <a name="create-a-temporary-table"></a>Geçici tablo oluşturma
Geçici tablolar, tablo adınızı bir `#`önek olarak ekleyerek oluşturulur.  Örneğin:

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

Geçici tablolar, tam olarak aynı yaklaşım kullanılarak `CTAS` de oluşturulabilir:

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

## <a name="dropping-temporary-tables"></a>Geçici tabloları bırakma
Yeni bir oturum oluşturulduğunda geçici tabloların olmaması gerekir.  Ancak, aynı ada sahip bir geçici oluşturan aynı saklı yordamı arıyorsanız, `CREATE TABLE` deyimlerinizin başarılı olduğundan emin olmak için, aşağıdaki örnekte olduğu gibi, bir `DROP` basit bir ön varlık denetimi kullanılabilir:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Kodlama tutarlılığı için, bu düzenin hem tablolar hem de geçici tablolar için kullanılması iyi bir uygulamadır.  Kodunuzda bitirdiğinizde geçici tabloları kaldırmak için `DROP TABLE` kullanmak da iyi bir fikirdir.  Saklı yordam geliştirme bölümünde, bu nesnelerin temizlendiğinden emin olmak için bir yordamın sonunda bırakma komutlarının birlikte paketlenmiştir.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Kodu modüle etme
Geçici tablolar bir kullanıcı oturumunda herhangi bir yerde görünebileceğinizden, bu, uygulama kodunuzu modüler hale getirmenize yardımcı olmak için kullanılabilir.  Örneğin, aşağıdaki saklı yordam veritabanındaki tüm istatistikleri istatistik adına göre güncelleştirmek için DDL 'yi oluşturur.

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
FROM    t1
;
GO
```

Bu aşamada, oluşan tek eylem, DDL deyimleriyle #stats_ddl geçici bir tablo oluşturan bir saklı yordamın oluşturulmasına neden olur.  Bu saklı yordam, bir oturum içinde birden fazla kez çalıştırıldığında başarısız olmamasını sağlamak için zaten mevcutsa #stats_ddl bırakır.  Ancak, saklı yordamın sonunda `DROP TABLE` olmadığından, saklı yordam tamamlandığında, saklı yordamın dışında okunabilmesi için oluşturulan tabloyu bırakır.  SQL veri ambarı ' nda, diğer SQL Server veritabanlarının aksine, geçici tabloyu onu oluşturan yordamın dışında kullanmak mümkündür.  SQL veri ambarı geçici tabloları, oturum içinde **herhangi bir yerde** kullanılabilir. Bu, aşağıdaki örnekte olduğu gibi daha modüler ve yönetilebilir kod oluşmasına neden olabilir:

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

## <a name="temporary-table-limitations"></a>Geçici tablo sınırlamaları
SQL veri ambarı, geçici tablolar uygularken birkaç sınırlama getirir.  Şu anda yalnızca oturum kapsamlı geçici tablolar desteklenir.  Genel geçici tablolar desteklenmez.  Ayrıca, görünümler geçici tablolarda oluşturulamaz.  Geçici tablolar yalnızca karma veya hepsini bir kez deneme dağıtımı ile oluşturulabilir.  Çoğaltılan geçici tablo dağıtımı desteklenmiyor. 

## <a name="next-steps"></a>Sonraki adımlar
Tablo geliştirme hakkında daha fazla bilgi için bkz. [tabloya genel bakış](sql-data-warehouse-tables-overview.md).


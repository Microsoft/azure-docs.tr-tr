---
title: Geçici tablolar
description: Geçici tabloları adanmış SQL havuzunda kullanmak için, oturum düzeyi geçici tablolarının ilkelerini vurgulayan temel kılavuz.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 077782099d6d61982052dc1690d545e58e928d8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93310672"
---
# <a name="temporary-tables-in-dedicated-sql-pool"></a>Adanmış SQL havuzundaki geçici tablolar

Bu makale, geçici tabloları kullanmaya yönelik temel kılavuz içerir ve oturum düzeyi geçici tablolarının ilkelerini vurgular. 

Bu makaledeki bilgilerin kullanılması, kodunuzun kodlarınlığı ve bakım kolaylığını iyileştirmeye yardımcı olabilir.

## <a name="what-are-temporary-tables"></a>Geçici tablolar nelerdir?

Geçici tablolar, özellikle de ara sonuçların geçici olduğu dönüştürme sırasında verileri işlerken yararlı olur. Adanmış SQL havuzunda, geçici tablolar oturum düzeyinde mevcuttur.  

Geçici tablolar yalnızca oluşturuldukları oturum için görünür ve bu oturum kapattığında otomatik olarak bırakılır.  

Geçici tablolar, sonuçları uzak depolama yerine yerel olarak yazıldığı için bir performans avantajı sunar.

Geçici tablolar, özellikle de ara sonuçların geçici olduğu dönüştürme sırasında verileri işlerken yararlı olur. Adanmış SQL havuzu ile, geçici tablolar oturum düzeyinde bulunur.  Bunlar yalnızca oluşturuldukları oturum tarafından görülebilir. Bu nedenle, bu oturum kapattığında otomatik olarak bırakılır. 

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

## <a name="dropping-temporary-tables"></a>Geçici tabloları bırakma
Yeni bir oturum oluşturulduğunda geçici tabloların olmaması gerekir.  

Aynı adı taşıyan bir geçici oluşturan aynı saklı yordamı arıyorsanız, deyimlerinizin başarılı olmasını sağlamak için, `CREATE TABLE` Aşağıdaki örnekte olduğu gibi basit bir ön varlık öncesi denetim `DROP` kullanılabilir:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Kodlama tutarlılığı için, bu düzenin hem tablolar hem de geçici tablolar için kullanılması iyi bir uygulamadır.  Ayrıca `DROP TABLE` , kodunuzda bu dosyalarla işiniz bittiğinde geçici tabloları kaldırmak için kullanmanız iyi bir fikirdir.  

Saklı yordam geliştirmede, bu nesnelerin temizlendiğinden emin olmak için bir yordamın sonunda bırakma komutlarının birlikte paketlenmiştir.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Kodu modüle etme
Geçici tablolar bir kullanıcı oturumunda herhangi bir yerde görünebileceğinizden, bu özellik uygulama kodunuzu modüler hale getirmenize yardımcı olmak için yararlanılabilir olabilir.  

Örneğin, aşağıdaki saklı yordam veritabanındaki tüm istatistikleri istatistik adına göre güncelleştirmek için DDL 'yi oluşturur:

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

Bu aşamada, oluşan tek eylem, DDL deyimleriyle #stats_ddl geçici bir tablo oluşturan bir saklı yordamın oluşturulmasına neden olur.  

Bu saklı yordam, bir oturum içinde birden çok kez çalıştırıldığında başarısız olmamasını sağlamak için mevcut bir #stats_ddl bırakır.  

Bununla birlikte, saklı yordamın `DROP TABLE` sonunda, saklı yordam tamamlandığında, saklı yordamın dışında okunabilmesi için oluşturulan tabloyu bırakır.  

Adanmış SQL havuzunda, diğer SQL Server veritabanlarının aksine, geçici tabloyu onu oluşturan yordamın dışında kullanmak mümkündür.  Adanmış SQL havuzu geçici tabloları, oturum içinde **herhangi bir yerde** kullanılabilir. Bu özellik aşağıdaki örnekte olduğu gibi daha modüler ve yönetilebilir koda yol açabilir:

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
Adanmış SQL havuzu, geçici tablolar uygularken birkaç sınırlama uygular.  Şu anda yalnızca oturum kapsamlı geçici tablolar desteklenir.  Genel geçici tablolar desteklenmez.  

Ayrıca, görünümler geçici tablolarda oluşturulamaz.  Geçici tablolar yalnızca karma veya hepsini bir kez deneme dağıtımı ile oluşturulabilir.  Çoğaltılan geçici tablo dağıtımı desteklenmiyor. 

## <a name="next-steps"></a>Sonraki adımlar

Tablo geliştirme hakkında daha fazla bilgi edinmek için [ADANMıŞ SQL havuzu kullanarak tabloları tasarlama](sql-data-warehouse-tables-overview.md) makalesini inceleyin.


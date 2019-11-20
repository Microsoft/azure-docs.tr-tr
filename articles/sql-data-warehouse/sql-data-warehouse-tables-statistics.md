---
title: İstatistikleri oluşturma, güncelleştirme
description: Azure SQL veri ambarı 'nda tablolar üzerinde sorgu iyileştirme istatistiklerini oluşturmaya ve güncelleştirmeye yönelik öneriler ve örnekler.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c995358fc0135a1f9b504b57b23ecb3f6b41d6da
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692404"
---
# <a name="table-statistics-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda tablo istatistikleri

Azure SQL veri ambarı 'nda tablolar üzerinde sorgu iyileştirme istatistiklerini oluşturmaya ve güncelleştirmeye yönelik öneriler ve örnekler.

## <a name="why-use-statistics"></a>İstatistikleri neden kullanılmalıdır?

Daha fazla Azure SQL veri ambarı verilerinizi öğrendiğinde, daha hızlı sorgu yürütebilir. Verileri SQL veri ambarı 'na yükledikten sonra verileriniz üzerinde istatistikler toplanması, sorgularınızı iyileştirmek için yapabileceğiniz en önemli işlemlerden biridir. SQL Veri Ambarı sorgu iyileştiricisi maliyet tabanlı bir iyileştiricidir. Çeşitli sorgu planlarının maliyetini karşılaştırır ve en düşük maliyetli planı seçer. Çoğu durumda, en hızlı yürütecektir planı seçer. Örneğin, iyileştirici sorgunun filtrelemesinin olduğu tarihin bir satır döndürür bir plan seçer. Seçili tarihin 1.000.000 satır döndüreceğini tahmin eder, farklı bir plan döndürür.

## <a name="automatic-creation-of-statistic"></a>Otomatik istatistiğin oluşturulması

DATABASE AUTO_CREATE_STATISTICS seçeneği açık olduğunda SQL veri ambarı eksik istatistik için gelen kullanıcı sorgularını analiz eder. İstatistikler eksikse, sorgu iyileştiricisi sorgu koşulunda tek tek sütunlarda istatistikler oluşturur ve sorgu planının kardinalitesini tahmin etmek için JOIN koşulundaki İstatistiklerin otomatik olarak oluşturulması Şu anda varsayılan olarak açıktır.

Aşağıdaki komutu çalıştırarak veri ambarınızın AUTO_CREATE_STATISTICS yapılandırılıp yapılandırılmadığını kontrol edebilirsiniz:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Veri Ambarınızda AUTO_CREATE_STATISTICS yapılandırılmamışsa, aşağıdaki komutu çalıştırarak bu özelliği etkinleştirmenizi öneririz:

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

İstatistiklerin otomatik olarak oluşturulması zaman uyumlu olarak yapılır, böylece sütunlarınızın istatistikleri eksik olduğunda biraz düşürülmüş sorgu performansına neden olabilirsiniz. Tek bir sütun için istatistik oluşturma zamanı tablonun boyutuna bağlıdır. Ölçülebilir performans düşüşünü önlemek için, özellikle de performans değerlendirmesi ' nde, sistemin profilini oluşturmadan önce kıyaslama iş yükünü yürüterek önce istatistik oluşturulduğundan emin olmanız gerekir.

> [!NOTE]
> İstatistik oluşturma, farklı bir kullanıcı bağlamı altında [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 'e kaydedilir.

Otomatik istatistikler oluşturulduğunda, şu biçimde olur: _WA_Sys_< 8 basamaklı sütun kimliği onaltılık > _ < 8 basamaklı tablo kimliği onaltılık >. [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest) komutu çalıştırılarak daha önce oluşturulmuş olan istatistikleri görüntüleyebilirsiniz:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name, görüntülenecek istatistikleri içeren tablonun adıdır. Bu bir dış tablo olamaz. Hedef, İstatistik bilgilerinin görüntüleneceği hedef dizinin, istatistiklerin veya sütunun adıdır.

## <a name="updating-statistics"></a>İstatistikleri güncelleştirme

En iyi yöntem, yeni tarihler eklendikçe her gün tarih sütunlarında istatistikleri güncelleştirmelerdir. Veri ambarına her yeni satır yüklendiğinde, yeni yükleme tarihleri veya işlem tarihleri eklenir. Bu, veri dağıtımını değiştirir ve istatistikleri güncel hale getirir. Bunun aksine, bir müşteri tablosundaki ülke/bölge sütunundaki istatistiklerin hiçbir şekilde güncellenmesi gerekmez, çünkü değerlerin dağıtılması genellikle değişmez. Dağıtımın müşteriler arasında sabit olduğu varsayıldığında, tablo varyasyonuna yeni satırlar eklemek veri dağıtımını değiştirmez. Ancak, veri ambarınız yalnızca bir ülke/bölge içeriyorsa ve yeni bir ülke/bölgeden veri getirdiyseniz, birden çok ülkede/bölgeden veri elde etmeniz durumunda, ülke/bölge sütunundaki istatistikleri güncelleştirmeniz gerekir.

Öneriler güncelleştirme istatistikleri aşağıda verilmiştir:

|||
|-|-|
| **İstatistik güncelleştirme sıklığı**  | Koruyucu: günlük </br> Verilerinizi yükledikten veya dönüştürdükten sonra |
| **Örnekleme** |  1\.000.000.000 satırdan az olan varsayılan örnekleme (yüzde 20) kullanın. </br> 1\.000.000.000 ' den fazla satır ile, iki yüzde örnekleme kullanın. |

Bir sorgunun sorunlarını giderirken ilk sorulardan biri, **"istatistiklerin güncel mi?"** olduğunu sorar.

Bu soru, verilerin yaşına göre yanıtlanacak bir değildir. Temel alınan verilerde malzeme değişikliği olmadığında güncel bir istatistik nesnesi eski olabilir. Satır sayısı önemli ölçüde değiştirildiğinde veya bir sütun için değerlerin dağıtımında bir malzeme değişikliği *varsa, istatistiklerin güncelleştirilmesi zaman olur.*

Son istatistik güncelleştirildikten sonra tablodaki verilerin değişip değişmediğini tespit etmek için dinamik bir yönetim görünümü yoktur. İstatistiklerinizin yaşınızı bilmeniz, resmin bir parçasını sağlayabilir. Her tabloda istatistiklerin güncelleştirildiği son zamanı anlamak için aşağıdaki sorguyu kullanabilirsiniz.

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

Örneğin, bir veri ambarındaki **Tarih sütunları** genellikle sık sık istatistik güncelleştirmelerine ihtiyaç duyar. Veri ambarına her yeni satır yüklendiğinde, yeni yükleme tarihleri veya işlem tarihleri eklenir. Bu, veri dağıtımını değiştirir ve istatistikleri güncel hale getirir. Buna karşılık, müşteri tablosundaki cinsiyet sütunundaki istatistiklerin hiçbir şekilde güncellenmesi gerekmeyebilir. Dağıtımın müşteriler arasında sabit olduğu varsayıldığında, tablo varyasyonuna yeni satırlar eklemek veri dağıtımını değiştirmez. Ancak, veri ambarınız yalnızca bir cinsiyet içeriyorsa ve yeni bir gereksinim birden çok gende sonuçlanırsa, cinsiyet sütunundaki istatistikleri güncelleştirmeniz gerekir.

Daha fazla bilgi için bkz. [İstatistikler](/sql/relational-databases/statistics/statistics)için genel kılavuz.

## <a name="implementing-statistics-management"></a>İstatistik yönetimi uygulama

Yük sonunda istatistiklerin güncelleştirildiğinden emin olmak için veri yükleme işleminizi genişletmek iyi bir fikirdir. Veri yükü, tabloların en sık boyutunu ve/veya değerlerinin dağılımını değiştirme olur. Bu nedenle, bazı yönetim süreçlerini uygulamak için bu mantıksal bir yerdir.

Aşağıdaki temel ilkeler, yükleme işlemi sırasında istatistiklerinizi güncelleştirmek için verilmiştir:

* Yüklenen her tabloda en az bir istatistik nesnesinin güncelleştirildiğinden emin olun. Bu, istatistik güncelleştirmesinin bir parçası olarak tablo boyutunu (satır sayısı ve sayfa sayısı) güncelleştirir.
* JOIN, GROUP BY, ORDER BY ve DISTINCT yan tümcelerinde yer alan sütunlara odaklanın.
* Bu değerler istatistik histogramı içinde yer alacağından, işlem tarihleri daha sık olan "artan anahtar" sütunlarını güncelleştirmeyi göz önünde bulundurun.
* Statik dağıtım sütunlarını daha az sıklıkta güncelleştirmeyi göz önünde bulundurun.
* Her istatistik nesnesinin sırayla güncelleştirildiğini unutmayın. Yalnızca çok sayıda istatistik nesnesi olan geniş tablolar için `UPDATE STATISTICS <TABLE_NAME>` uygulamak her zaman ideal değildir.

Daha fazla bilgi için bkz. [kardinalite tahmini](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Örnekler: istatistik oluşturma

Bu örneklerde, istatistik oluşturmak için çeşitli seçeneklerin nasıl kullanılacağı gösterilmektedir. Her bir sütun için kullandığınız seçenekler, verilerinizin özelliklerine ve sütun sorgularda nasıl kullanılacağını bağlıdır.

### <a name="create-single-column-statistics-with-default-options"></a>Varsayılan seçeneklerle tek sütunlu istatistikler oluşturma

Bir sütunda istatistik oluşturmak için, istatistik nesnesi ve sütunun adı için bir ad belirtmeniz yeterlidir.

Bu söz dizimi varsayılan seçeneklerin tümünü kullanır. Varsayılan olarak SQL veri ambarı, istatistik oluşturduğunda tablonun **yüzde 20 ' sini** örnekler.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Örneğin:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Her satırı inceleyerek tek sütunlu istatistikler oluşturma

Çoğu durum için yüzde 20 ' nin varsayılan örnekleme oranı yeterlidir. Ancak örnekleme hızını ayarlayabilirsiniz.

Tam tabloyu örneklemek için şu sözdizimini kullanın:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Örneğin:

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

Tam başvuru için bkz. [Istatistik oluşturma](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Çok sütunlu istatistikler oluşturma

Çok sütunlu bir istatistik nesnesi oluşturmak için yalnızca önceki örnekleri kullanın, ancak daha fazla sütun belirtin.

> [!NOTE]
> Sorgu sonucundaki satır sayısını tahmin etmek için kullanılan histogramı yalnızca istatistik nesne tanımında listelenen ilk sütunda kullanılabilir.

Bu örnekte, histogram *ürün\_kategorisinde*bulunur. Çapraz sütun istatistikleri *ürün\_kategorisi* ve *ürün\_sub_category*göre hesaplanır:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

*Ürün\_kategorisi* ve *ürün\_Sub\_kategorisi*arasında bir bağıntı olduğundan, bu sütunlara aynı anda erişildiğinde çok sütunlu bir istatistik nesnesi yararlı olabilir.

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Bir veritabanındaki tüm sütunlarda istatistik oluşturmak için saklı yordam kullanma

SQL veri ambarı 'nın SQL Server içinde sp_create_stats ile eşdeğer bir sistem saklı yordamı yoktur. Bu saklı yordam, veritabanının zaten istatistiği olmayan her sütununda tek sütunlu bir istatistik nesnesi oluşturur.

Aşağıdaki örnek, veritabanı tasarımınızı kullanmaya başlamanıza yardımcı olur. Gereksinimlerinize uygun hale gelmekten çekinmeyin:

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

FULLSCAN kullanarak tablodaki tüm sütunlarda istatistik oluşturmak için aşağıdaki yordamı çağırın:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Tablodaki tüm sütunlarda örneklenmiş istatistikler oluşturmak için 3 ve örnek yüzdesini girin. Bu yordamlar yüzde 20 örnek oranını kullanır.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

Tüm sütunlarda örneklenmiş istatistik oluşturmak için

## <a name="examples-update-statistics"></a>Örnekler: güncelleştirme istatistikleri

İstatistikleri güncelleştirmek için şunları yapabilirsiniz:

- Tek bir istatistik nesnesini güncelleştirin. Güncelleştirmek istediğiniz istatistik nesnesinin adını belirtin.
- Tablodaki tüm istatistik nesnelerini güncelleştirin. Belirli bir istatistik nesnesi yerine tablonun adını belirtin.

### <a name="update-one-specific-statistics-object"></a>Belirli bir istatistik nesnesini güncelleştirme

Belirli bir istatistik nesnesini güncelleştirmek için aşağıdaki sözdizimini kullanın:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Örneğin:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Belirli istatistik nesnelerini güncelleştirerek, istatistikleri yönetmek için gereken süre ve kaynakları en aza indirmenize olanak sağlar. Bu, güncelleştirilecek en iyi istatistik nesnelerini seçmek için bazı düşünce gerektirir.

### <a name="update-all-statistics-on-a-table"></a>Tablodaki tüm istatistikleri güncelleştirme

Bir tablodaki tüm istatistik nesnelerini güncelleştirmek için basit bir yöntem şunlardır:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Örneğin:

```sql
UPDATE STATISTICS dbo.table1;
```

GÜNCELLEŞTIRME ISTATISTIKLERI bildiriminin kullanımı kolaydır. Yalnızca tablodaki *Tüm* istatistikleri güncelleştirdiğinizden ve bu nedenle gerekenden daha fazla iş gerçekleştirebileceğini unutmayın. Performans bir sorun değilse, istatistiklerin güncel olup olmadığını güvence altına almak için en kolay ve en kapsamlı yoldur.

> [!NOTE]
> Bir tablodaki tüm İstatistikleri güncelleştirirken, SQL veri ambarı her istatistik nesnesi için tabloyu örneklemek üzere bir tarama yapar. Tablo büyükse ve çok sayıda sütun ve birçok istatistik içeriyorsa, her bir istatistiği ihtiyaya göre güncelleştirmek daha verimli olabilir.

`UPDATE STATISTICS` yordamının uygulanması için bkz. [geçici tablolar](sql-data-warehouse-tables-temporary.md). Uygulama yöntemi, önceki `CREATE STATISTICS` yordamından biraz farklıdır, ancak sonuç aynıdır.

Tam sözdizimi için bkz. [güncelleştirme istatistikleri](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>İstatistik meta verileri

İstatistikler hakkında bilgi edinmek için kullanabileceğiniz çeşitli sistem görünümleri ve işlevleri vardır. Örneğin, istatistiklerin en son ne zaman oluşturulduğunu veya güncelleştirildiğini görmek için, istatistik bir nesnenin güncel olup olmadığını görmek için stats-Date işlevini kullanabilirsiniz.

### <a name="catalog-views-for-statistics"></a>İstatistikler için katalog görünümleri

Bu sistem görünümleri istatistikler hakkında bilgi sağlar:

| Katalog görünümü | Açıklama |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Her sütun için bir satır. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Veritabanındaki her nesne için bir satır. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Veritabanındaki her şema için bir satır. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Her istatistik nesnesi için bir satır. |
| [sys. stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |İstatistikler nesnesindeki her sütun için bir satır. Sys. Columns öğesine geri bağlantı. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Her tablo için bir satır (dış tabloları içerir). |
| [sys. table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Her veri türü için bir satır. |

### <a name="system-functions-for-statistics"></a>İstatistikler için sistem işlevleri

Bu sistem işlevleri, istatistiklerle çalışmak için faydalıdır:

| Sistem işlevi | Açıklama |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |İstatistik nesnesinin son güncelleştirilme tarihi. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |İstatistik nesnesi tarafından anlaşıldıkça değerlerin dağılımı hakkında özet düzeyi ve ayrıntılı bilgiler. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>İstatistik sütunları ve işlevleri tek bir görünümde birleştirin

Bu görünüm, STATS_DATE () işlevinin istatistikleriyle ve sonuçlarıyla ilgili sütunları getirir.

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

İstatistiklerle ilgili üst bilgi meta verileri. Histogram, değerlerin dağılımını istatistik nesnesinin ilk anahtar sütununda görüntüler. Yoğunluk vektörü, çapraz sütun bağıntısını ölçer. SQL veri ambarı, istatistik nesnesindeki verilerle kardinalite tahminleri hesaplar.

### <a name="show-header-density-and-histogram"></a>Üstbilgiyi, yoğunluğu ve histogramı göster

Bu basit örnek, bir istatistik nesnesinin üç parçasını gösterir:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Örneğin:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>DBCC SHOW_STATISTICS () ' in bir veya daha fazla parçasını göster

Yalnızca belirli parçaları görüntülemeyi ilgileniyorsanız, `WITH` yan tümcesini kullanın ve hangi parçaları görmek istediğinizi belirtin:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Örneğin:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS () farkları

DBCC SHOW_STATISTICS (), SQL veri ambarı 'nda SQL Server kıyasla daha net bir şekilde uygulanmıştır:

- Belgelenmemiş özellikler desteklenmez.
- Stats_stream kullanılamaz.
- İstatistik verilerinin belirli alt kümeleri için sonuçlara katılamaz. Örneğin, STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS ileti gizleme için ayarlanamıyor.
- İstatistik adları etrafında köşeli ayraç kullanılamaz.
- İstatistik nesnelerini tanımlamak için sütun adları kullanılamaz.
- Özel hata 2767 desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar

Sorgu performansını artırmak için bkz. [iş yükünüzü izleme](sql-data-warehouse-manage-monitor.md)

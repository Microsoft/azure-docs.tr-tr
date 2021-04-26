---
title: Azure SYNAPSE SQL kaynaklarını kullanarak istatistikleri oluşturma ve güncelleştirme
description: SYNAPSE SQL 'de sorgu iyileştirme istatistikleri oluşturma ve güncelleştirme önerileri ve örnekleri.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 450a089c6cc1c77ac26cb0aa339277d5c49b41c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594793"
---
# <a name="statistics-in-synapse-sql"></a>SYNAPSE SQL istatistikleri

Bu makalede sunulan öneriler ve SYNAPSE SQL kaynaklarını kullanarak sorgu iyileştirmesi istatistiklerini oluşturma ve güncelleştirme örnekleri: adanmış SQL havuzu ve sunucusuz SQL Havuzu.

## <a name="statistics-in-dedicated-sql-pool"></a>Adanmış SQL havuzundaki istatistikler

### <a name="why-use-statistics"></a>İstatistikleri neden kullanılmalıdır?

Daha fazla adanmış SQL havuzu verileriniz hakkında bilgi sahibi olduğunda sorguları yürütebilir daha hızlı olur. Verileri adanmış bir SQL havuzuna yükledikten sonra, verilerinize ilişkin istatistikleri toplamak, sorgu iyileştirmesi için yapabileceğiniz en önemli işlemlerden biridir.  

Adanmış SQL havuzu sorgu iyileştiricisi, maliyet tabanlı bir iyileştiricudur. Çeşitli sorgu planlarının maliyetini karşılaştırır ve en düşük maliyetli planı seçer. Çoğu durumda, en hızlı yürütecektir planı seçer.

Örneğin, iyileştirici sorgunun filtrelemesinin olduğu tarihin bir satır döndürdüğü tahmini bir plan seçer. Seçili tarihin 1.000.000 satır döndüreceğini tahmin eder, farklı bir plan döndürür.

### <a name="automatic-creation-of-statistics"></a>İstatistiklerin otomatik olarak oluşturulması

Adanmış SQL havuzu altyapısı, veritabanı AUTO_CREATE_STATISTICS seçeneği olarak ayarlandığında eksik istatistik için gelen kullanıcı sorgularını analiz eder `ON` .  İstatistikler eksikse, sorgu iyileştiricisi sorgu koşulunda veya JOIN koşulunda tek tek sütunlarda istatistikler oluşturur. 

Bu işlev, sorgu planına yönelik kardinalite tahminlerini geliştirmek için kullanılır.

> [!IMPORTANT]
> İstatistiklerin otomatik olarak oluşturulması Şu anda varsayılan olarak açıktır.

Aşağıdaki komutu çalıştırarak veri ambarınızın AUTO_CREATE_STATISTICS yapılandırılıp yapılandırılmadığını kontrol edebilirsiniz:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Veri Ambarınızda AUTO_CREATE_STATISTICS etkinleştirilmemişse, aşağıdaki komutu çalıştırarak bu özelliği etkinleştirmenizi öneririz:

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

İstatistiklerin otomatik olarak oluşturulması zaman uyumlu olarak yapılır. Bu nedenle, sütunlarınızın istatistikleri eksik olduğunda biraz daha düşük bir sorgu performansına neden olabilirsiniz. Tek bir sütun için istatistik oluşturma zamanı tablonun boyutuna bağlıdır.

Ölçülebilir performans düşüşünü önlemek için, sistemin profilini oluşturmadan önce kıyaslama iş yükünü yürüterek öncelikle istatistik oluşturulduğundan emin olmanız gerekir.

> [!NOTE]
> Farklı bir kullanıcı bağlamı altında [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) , istatistik oluşturma işlemi günlüğe kaydedilir.

Otomatik istatistikler oluşturulduğunda şu biçimi alır: _WA_Sys_<8 basamaklı sütun kimliği onaltılık>_<8 basamaklı tablo kimliği onaltılık>. Zaten oluşturulmuş istatistikleri [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) komutunu çalıştırarak görüntüleyebilirsiniz:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name, görüntülenecek istatistikleri içeren tablonun adıdır ve bu, dış tablo olamaz. Hedef, İstatistik bilgilerinin görüntüleneceği hedef dizinin, istatistiklerin veya sütunun adıdır.

### <a name="update-statistics"></a>İstatistikleri Güncelleştir

En iyi yöntem, yeni tarihler eklendikçe her gün tarih sütunlarında istatistikleri güncelleştirmelerdir. Veri ambarına her yeni satır yüklendiğinde, yeni yükleme tarihleri veya işlem tarihleri eklenir. Bu eklemeler, veri dağıtımını değiştirir ve istatistikleri güncel hale getirir.

Değerlerin dağıtılması genellikle değişmediğinden, müşteri tablosundaki bir ülke veya bölge sütunundaki istatistiklerin hiçbir şekilde güncelleştirilmesine gerek kalmaz. Dağıtımın müşteriler arasında sabit olduğu varsayıldığında, tablo varyasyonuna yeni satırlar eklemek veri dağıtımını değiştirmez.

Ancak, veri ambarınız yalnızca bir ülke veya bölge içerdiğinde ve yeni bir ülke veya bölgeden veri getirdiyseniz, ülke veya bölge sütununda istatistikleri güncelleştirmeniz gerekir.

İstatistikleri güncelleştirme önerileri aşağıda verilmiştir:

|Tür|Öneri|
|-|-|
| **İstatistik güncelleştirme sıklığı**  | Koruyucu: günlük </br> Verilerinizi yükledikten veya dönüştürdükten sonra |
| **Örnekleme** |  1.000.000.000 satırdan az olan varsayılan örnekleme (yüzde 20) kullanın. </br> 1.000.000.000 ' den fazla satır ile, iki yüzde örnekleme kullanın. |

### <a name="determine-last-statistics-update"></a>Son istatistik güncelleştirmesini belirleme

Bir sorgunun sorunlarını giderirken ilk sorulardan biri, **"istatistiklerin güncel mi?"** olduğunu sorar.

Bu soru, verilerin yaşına göre yanıtlanacak bir değildir. Temel alınan verilerde malzeme değişikliği olmadığında güncel bir istatistik nesnesi eski olabilir. Satır sayısı önemli ölçüde değiştirildiğinde veya bir sütun için değerlerin dağıtımında bir malzeme değişikliği meydana *gelirse, istatistikleri güncelleştirme zamanı geldi.*

En son istatistik güncelleştirildikten sonra tablodaki verilerin değişip değişmediğini tespit etmek için kullanılabilir dinamik bir yönetim görünümü yoktur. İstatistiklerinizin yaşınızı bilmeniz, resmin bir parçasını sağlayabilir. 

Her tabloda istatistiklerin güncelleştirildiği son zamanı anlamak için aşağıdaki sorguyu kullanabilirsiniz.

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

Örneğin, bir veri ambarındaki **Tarih sütunları** genellikle sık sık istatistik güncelleştirmelerine ihtiyaç duyar. Veri ambarına her yeni satır yüklendiğinde, yeni yükleme tarihleri veya işlem tarihleri eklenir. Bu eklemeler, veri dağıtımını değiştirir ve istatistikleri güncel hale getirir.

Müşteri tablosundaki cinsiyet sütunundaki istatistiklerin hiçbir şekilde güncellenmesi gerekmez. Dağıtımın müşteriler arasında sabit olduğu varsayıldığında, tablo varyasyonuna yeni satırlar eklemek veri dağıtımını değiştirmez.

Ancak, veri ambarınız tek bir cinsiyet içeriyorsa ve yeni bir gereksinim birden çok gende sonuçlanırsa, cinsiyet sütunundaki istatistikleri güncelleştirmeniz gerekir. 

Daha fazla bilgi için, [istatistik](/sql/relational-databases/statistics/statistics) makaleyi gözden geçirin.

### <a name="implement-statistics-management"></a>İstatistik yönetimi Uygula

Yük sonunda istatistiklerin güncelleştirildiğinden emin olmak için veri yükleme işleminizi genişletmek iyi bir fikirdir. Veri yükü, tabloların boyutunu en sık değiştiren, değerlerin dağıtılması veya her ikisi de olur. Bu nedenle, yükleme işlemi bazı yönetim işlemlerini uygulamak için mantıksal bir yerdir.

Aşağıdaki temel ilkeler, yükleme işlemi sırasında istatistiklerinizi güncelleştirmek için verilmiştir:

- Yüklenen her tabloda en az bir istatistik nesnesinin güncelleştirildiğinden emin olun. Bu işlem, istatistik güncelleştirmesinin bir parçası olarak tablo boyutunu (satır sayısı ve sayfa sayısı) güncelleştirir.
- JOIN, GROUP BY, ORDER BY ve DISTINCT yan tümcelerinde yer alan sütunlara odaklanın.
- Bu değerler istatistik histogramı 'ne dahil edilmeyeceği için, işlem tarihleri gibi "artan anahtar" sütunları güncellemeyi daha sık düşünün.
- Statik dağıtım sütunlarını daha az sıklıkta güncelleştirmeyi göz önünde bulundurun.
- Her istatistik nesnesinin sırayla güncelleştirildiğini unutmayın. `UPDATE STATISTICS <TABLE_NAME>`Özellikle çok sayıda istatistik nesnesi olan geniş tablolar için her zaman ideal bir uygulama değildir.

Daha fazla bilgi için bkz. [kardinalite tahmini](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Örnekler: istatistik oluşturma

Bu örneklerde, istatistik oluşturmak için çeşitli seçeneklerin nasıl kullanılacağı gösterilmektedir. Her bir sütun için kullandığınız seçenekler, verilerinizin özelliklerine ve sütun sorgularda nasıl kullanılacağını bağlıdır.

#### <a name="create-single-column-statistics-with-default-options"></a>Varsayılan seçeneklerle tek sütunlu istatistikler oluşturma

Bir sütunda istatistik oluşturmak için, istatistik nesnesi ve sütunun adı için bir ad sağlayın.
Bu söz dizimi varsayılan seçeneklerin tümünü kullanır. Varsayılan olarak, adanmış SQL havuzu, istatistik oluşturduğunda tablonun **yüzde 20 ' sini** örnekler.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Örnek:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Her satırı inceleyerek tek sütunlu istatistikler oluşturma

Çoğu durum için yüzde 20 ' nin varsayılan örnekleme oranı yeterlidir. Ancak örnekleme hızını ayarlayabilirsiniz. Tam tabloyu örneklemek için şu sözdizimini kullanın:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Örnek:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Örnek boyutunu belirterek tek sütunlu istatistikler oluşturma

Diğer bir seçenek de örnek boyutunu yüzde olarak belirtmektir:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Yalnızca bazı satırlarda tek sütunlu istatistikler oluşturma

Ayrıca, tablonuzda bulunan ve filtrelenmiş istatistik olarak adlandırılan satırların bir kısmında İstatistikler de oluşturabilirsiniz.

Örneğin, büyük bölümlendirilmiş bir tablonun belirli bir bölümünü sorgulamayı planlarken filtrelenmiş istatistikleri kullanabilirsiniz. Yalnızca bölüm değerlerinde istatistik oluşturarak istatistiklerin doğruluğu iyileştirecektir. Sorgu performansı için de bir geliştirme yaşarsınız.

Bu örnekte, bir dizi değer üzerinde istatistikler oluşturulur. Değerler, bir bölümdeki değer aralığıyla eşleşecek şekilde kolayca tanımlanabilir.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Sorgu iyileştiricinin dağıtılmış sorgu planını seçtiği sırada filtrelenmiş istatistikleri kullanmayı düşündüğü için, sorgu istatistik nesnesinin tanımına sığması gerekir. Önceki örneği kullanarak, sorgunun WHERE yan tümcesinin 2000101 ile 20001231 arasında Sütun1 değerlerini belirtmesi gerekir.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Tüm seçeneklerle tek sütunlu istatistikler oluşturma

Ayrıca, seçenekleri birlikte birleştirebilirsiniz. Aşağıdaki örnek, özel bir örnek boyutuyla filtrelenmiş bir istatistik nesnesi oluşturur:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Tam başvuru için bkz. [Istatistik oluşturma](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

#### <a name="create-multi-column-statistics"></a>Çok sütunlu istatistikler oluşturma

Çok sütunlu bir istatistik nesnesi oluşturmak için önceki örnekleri kullanın, ancak daha fazla sütun belirtin.

> [!NOTE]
> Sorgu sonucundaki satır sayısını tahmin etmek için kullanılan histogramı yalnızca istatistik nesne tanımında listelenen ilk sütunda kullanılabilir.

Bu örnekte, histogram *ürün \_ kategorisinde* bulunur. Çapraz sütun istatistikleri *ürün \_ kategorisinde* ve *ürün \_ sub_category* hesaplanır:

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

*Ürün \_ kategorisi* ile *ürün \_ alt \_ kategorisi* arasında bir bağıntı bulunduğundan, bu sütunlara aynı anda erişildiğinde çok sütunlu bir istatistik nesnesi yararlı olabilir.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Tablodaki tüm sütunlarda istatistik oluşturma

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Bir veritabanındaki tüm sütunlarda istatistik oluşturmak için saklı yordam kullanma

SQL havuzunda SQL Server sp_create_stats eşdeğer bir sistem saklı yordamı yoktur. Bu saklı yordam, veritabanının zaten istatistiği olmayan her sütununda tek sütunlu bir istatistik nesnesi oluşturur.

Aşağıdaki örnek, veritabanı tasarımınızı kullanmaya başlamanıza yardımcı olur. Gereksinimlerinize uygun hale gelmekten çekinmeyin:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
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

Tablodaki tüm sütunlarda örneklenmiş istatistikler oluşturmak için 3 ve örnek yüzdesini girin. Aşağıdaki yordam yüzde 20 örnek hız kullanır.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Örnekler: güncelleştirme istatistikleri

İstatistikleri güncelleştirmek için şunları yapabilirsiniz:

- Tek bir istatistik nesnesini güncelleştirin. Güncelleştirmek istediğiniz istatistik nesnesinin adını belirtin.
- Tablodaki tüm istatistik nesnelerini güncelleştirin. Belirli bir istatistik nesnesi yerine tablonun adını belirtin.

#### <a name="update-one-specific-statistics-object"></a>Belirli bir istatistik nesnesini güncelleştirme

Belirli bir istatistik nesnesini güncelleştirmek için aşağıdaki sözdizimini kullanın:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Örnek:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Belirli istatistik nesnelerini güncelleştirerek, istatistikleri yönetmek için gereken süre ve kaynakları en aza indirmenize olanak sağlar. Bu eylem, güncelleştirilecek en iyi istatistik nesnelerinin seçilmesi için bazı düşünce gerektirir.

#### <a name="update-all-statistics-on-a-table"></a>Tablodaki tüm istatistikleri güncelleştirme

Bir tablodaki tüm istatistik nesnelerini güncelleştirmek için basit bir yöntem şunlardır:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Örnek:

```sql
UPDATE STATISTICS dbo.table1;
```

GÜNCELLEŞTIRME ISTATISTIKLERI bildiriminin kullanımı kolaydır. Yalnızca tablodaki *Tüm* istatistikleri güncelleştirdiğinden, daha fazla çalışma istemek gerektiğini unutmayın. 

Performans bir sorun değilse, bu yöntem istatistiklerin güncel olup olmadığını güvence altına almak için en kolay ve en kapsamlı yoldur.

> [!NOTE]
> Bir tablodaki tüm İstatistikleri güncelleştirirken, adanmış SQL havuzu her istatistik nesnesi için tabloyu örneklemek üzere bir tarama yapar. Tablo büyükse ve çok sayıda sütun ve birçok istatistik içeriyorsa, her bir istatistiği ihtiyaya göre güncelleştirmek daha verimli olabilir.

Bir yordamın uygulanması için `UPDATE STATISTICS` bkz. [geçici tablolar](develop-tables-temporary.md). Uygulama yöntemi önceki yordamdan biraz farklıdır `CREATE STATISTICS` , ancak sonuç aynıdır.
Tam sözdizimi için bkz. [güncelleştirme istatistikleri](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="statistics-metadata"></a>İstatistik meta verileri

İstatistikler hakkında bilgi edinmek için kullanabileceğiniz çeşitli sistem görünümleri ve işlevleri vardır. Örneğin, STATS_DATE () işlevini kullanarak bir istatistik nesnesinin güncel olup olmadığını görebilirsiniz. STATS_DATE (), istatistiklerin en son ne zaman oluşturulduğunu veya güncelleştirildiğini görmenizi sağlar.

#### <a name="catalog-views-for-statistics"></a>İstatistikler için katalog görünümleri

Bu sistem görünümleri istatistikler hakkında bilgi sağlar:

| Katalog görünümü | Description |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Her sütun için bir satır. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Veritabanındaki her nesne için bir satır. |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Veritabanındaki her şema için bir satır. |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Her istatistik nesnesi için bir satır. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |İstatistikler nesnesindeki her sütun için bir satır. Sys. Columns öğesine geri bağlantı. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Her tablo için bir satır (dış tabloları içerir). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Her veri türü için bir satır. |

#### <a name="system-functions-for-statistics"></a>İstatistikler için sistem işlevleri

Bu sistem işlevleri, istatistiklerle çalışmak için faydalıdır:

| Sistem işlevi | Description |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) |İstatistik nesnesinin son güncelleştirilme tarihi. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) |İstatistik nesnesi tarafından anlaşıldıkça değerlerin dağılımı hakkında özet düzeyi ve ayrıntılı bilgiler. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>İstatistik sütunları ve işlevleri tek bir görünümde birleştirin

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS () örnekleri

DBCC SHOW_STATISTICS (), bir istatistik nesnesi içinde tutulan verileri gösterir. Bu veriler üç bölümden oluşur:

- Üst bilgi
- Yoğunluk vektörü
- Histogram

Üst bilgi, istatistiklerle ilgili meta verilerdir. Histogram, değerlerin dağılımını istatistik nesnesinin ilk anahtar sütununda görüntüler. 

Yoğunluk vektörü, çapraz sütun bağıntısını ölçer. Adanmış SQL havuzu, istatistik nesnesindeki verilerle kardinalite tahminleri hesaplar.

#### <a name="show-header-density-and-histogram"></a>Üstbilgiyi, yoğunluğu ve histogramı göster

Bu basit örnek, bir istatistik nesnesinin üç parçasını gösterir:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Örnek:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>DBCC SHOW_STATISTICS () bir veya daha fazla parçasını göster

Yalnızca belirli parçaları görüntülemek istiyorsanız, `WITH` yan tümcesini kullanın ve hangi parçaları görmek istediğinizi belirtin:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Örnek:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS () farkları

`DBCC SHOW_STATISTICS()` , SQL Server karşılaştırıldığında adanmış SQL havuzunda daha net bir şekilde uygulanmıştır:

- Belgelenmemiş özellikler desteklenmez.
- Stats_stream kullanılamıyor.
- İstatistik verilerinin belirli alt kümeleri için sonuçlara katılamaz. Örneğin STAT_HEADER DENSITY_VECTOR BIRLEŞTIRIN.
- İleti gizleme için NO_INFOMSGS ayarlanamaz.
- İstatistik adları etrafında köşeli ayraç kullanılamaz.
- İstatistik nesnelerini tanımlamak için sütun adları kullanılamaz.
- Özel hata 2767 desteklenmiyor.


## <a name="statistics-in-serverless-sql-pool"></a>Sunucusuz SQL havuzundaki istatistikler

İstatistikler, belirli bir veri kümesi (depolama yolu) için belirli bir sütun başına oluşturulur.

> [!NOTE]
> LOB sütunları için istatistikler oluşturulamıyor.

### <a name="why-use-statistics"></a>İstatistikleri neden kullanılmalıdır?

Daha az sunucusuz SQL havuzu verilerinize göre daha hızlı bir şekilde sorgu yürütebilir. Verilerinize ilişkin istatistikleri toplamak, sorgularınızı iyileştirmek için yapabileceğiniz en önemli işlemlerden biridir. 

Sunucusuz SQL havuzu sorgu iyileştiricisi, maliyet tabanlı bir iyileştiricudur. Çeşitli sorgu planlarının maliyetini karşılaştırır ve en düşük maliyetli planı seçer. Çoğu durumda, en hızlı yürütecektir planı seçer. 

Örneğin, iyileştirici sorgunun filtrelemesinin olduğu tarihin bir satır döndürür bir plan seçer. Seçili tarihin 1.000.000 satır döndüreceğini tahmin eder, farklı bir plan döndürür.

### <a name="automatic-creation-of-statistics"></a>İstatistiklerin otomatik olarak oluşturulması

Sunucusuz SQL havuzu, eksik istatistik için gelen kullanıcı sorgularını analiz eder. İstatistikler eksikse, sorgu iyileştiricisi sorgu koşulunda tek tek sütunlarda istatistikler oluşturur ve sorgu planının kardinalitesini tahmin etmek için JOIN koşulundaki

SELECT deyimleri otomatik olarak istatistik oluşturmayı tetikler.

> [!NOTE]
> Parquet dosyaları için otomatik istatistiklerin oluşturulması açıktır. CSV dosyaları için, CSV dosyaları istatistiklerinin otomatik olarak oluşturulması desteklenene kadar el ile istatistik oluşturmanız gerekir.

İstatistiklerin otomatik olarak oluşturulması zaman uyumlu olarak yapılır, böylece sütunlarınızın istatistikleri eksik olduğunda biraz düşürülmüş sorgu performansına neden olabilirsiniz. Tek bir sütun için istatistik oluşturma zamanı, hedeflenen dosyaların boyutuna bağlıdır.

### <a name="manual-creation-of-statistics"></a>İstatistiklerin el ile oluşturulması

Sunucusuz SQL havuzu, istatistikleri el ile oluşturmanızı sağlar. CSV dosyaları için otomatik istatistik oluşturma özelliği açık olmadığından, CSV dosyaları için istatistikleri el ile oluşturmanız gerekir. 

İstatistiklerin el ile nasıl oluşturulacağı hakkında yönergeler için aşağıdaki örneklere bakın.

### <a name="update-statistics"></a>İstatistikleri Güncelleştir

Dosyalardaki verilerde yapılan değişiklikler, silme ve dosya ekleme, veri dağıtım değişikliklerine neden olur ve istatistik güncel değildir. Bu durumda, istatistiklerin güncelleştirilmesi gerekir.

Veriler önemli ölçüde değiştirilmişse sunucusuz SQL havuzu otomatik olarak istatistikleri yeniden oluşturur. İstatistikler otomatik olarak oluşturulduğunda, veri kümesinin geçerli durumu da kaydedilir: dosya yolları, Boyutlar, son değiştirilme tarihleri.

İstatistikler eskidiğinde, yenilerini oluşturulur. Algoritma verilerin içinden geçer ve veri kümesinin geçerli durumuyla karşılaştırılır. Değişikliklerin boyutu belirli eşikten büyükse, eski istatistikler silinir ve yeni veri kümesi üzerinden yeniden oluşturulur.

El ile istatistikler hiçbir şekilde eski olarak bildirilmez.

> [!NOTE]
> Parquet dosyaları için istatistiklerin otomatik olarak yeniden oluşturma özelliği açıktır. CSV dosyaları için, otomatik olarak CSV dosyaları oluşturulması desteklenene kadar el ile istatistikleri oluşturmanız ve oluşturmanız gerekir. İstatistik bırakma ve oluşturma hakkında aşağıdaki örnekleri kontrol edin.

Bir sorgunun sorunlarını giderirken ilk sorulardan biri, **"istatistiklerin güncel mi?"** olduğunu sorar.

Satır sayısı önemli ölçüde değiştirildiğinde veya bir sütun için değerlerin dağıtımında bir malzeme değişikliği *varsa, istatistiklerin* güncelleştirilmesi zaman olur.

> [!NOTE]
> Bir sütunun değerlerinin dağıtımında bir malzeme değişikliği varsa, son güncelleştirilme zamanından bağımsız olarak istatistikleri güncelleştirmeniz gerekir.

### <a name="implement-statistics-management"></a>İstatistik yönetimi Uygula

Veriler ekleme, silme veya değişiklik aracılığıyla önemli ölçüde değiştirildiğinde istatistiklerin güncelleştirilmesini sağlamak için veri işlem hattınızı genişletmek isteyebilirsiniz.

İstatistiklerinizi güncelleştirmek için aşağıdaki temel ilkeler verilmiştir:

- Veri kümesinde en az bir istatistik nesnesinin güncelleştirildiğinden emin olun. Bu güncelleştirme, istatistik güncelleştirmesinin bir parçası olarak boyut (satır sayısı ve sayfa sayısı) bilgileri.
- WHERE, JOIN, GROUP BY, ORDER BY ve DISTINCT yan tümcelerinde yer alan sütunlara odaklanın.
- Bu değerler istatistik histogramı ' ne dahil edilmeyeceği için işlem tarihleri gibi "artan anahtar" sütunlarını güncelleştirin.
- Statik dağıtım sütunlarını daha az sıklıkta güncelleştirin.

Daha fazla bilgi için bkz. [kardinalite tahmini](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Örnekler: OPENROWSET yolunda sütun için istatistikler oluşturma

Aşağıdaki örneklerde, istatistik oluşturmak için çeşitli seçeneklerin nasıl kullanılacağı gösterilmektedir. Her bir sütun için kullandığınız seçenekler, verilerinizin özelliklerine ve sütun sorgularda nasıl kullanılacağını bağlıdır.

> [!NOTE]
> Yalnızca şu an için tek sütunlu istatistikler oluşturabilirsiniz.
>
> Sp_create_openrowset_statistics ve sp_drop_openrowset_statistics yürütmek için aşağıdaki izinler gerekir: Toplu IŞLEMLERI yönetme veya VERITABANı toplu IŞLEMLERINI yönetme.

Aşağıdaki saklı yordam istatistik oluşturmak için kullanılır:

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

Bağımsız değişkenler: [ @stmt =] N ' statement_text '-istatistikler için kullanılacak sütun değerlerini döndürecek bir Transact-SQL Ifadesini belirtir. Kullanılacak veri örneklerini belirtmek için, "can" kullanabilirsiniz. Bu belirtilmemişse, FULLSCAN kullanılacaktır.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> CSV örneklemesi Şu anda çalışmıyor, CSV için yalnızca FULLSCAN desteklenir.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Her satırı inceleyerek tek sütunlu istatistikler oluşturma

Bir sütunda istatistik oluşturmak için, istatistikleri gereken sütunu döndüren bir sorgu sağlayın.

Varsayılan olarak, aksi takdirde, sunucusuz SQL havuzu, istatistik oluşturduğunda veri kümesinde belirtilen verilerin %100 'ini kullanır.

Örneğin, population.csv dosyasına göre veri kümesinin yıl sütunu için varsayılan seçeneklerle (FULLSCAN) istatistik oluşturmak için:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Örnek boyutunu belirterek tek sütunlu istatistikler oluşturma

Örnek boyutunu yüzde olarak belirtebilirsiniz:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Örnekler: güncelleştirme istatistikleri

İstatistikleri güncelleştirmek için, istatistikleri bırakıp, oluşturmanız gerekir. İstatistikleri bırakmak için aşağıdaki saklı yordam kullanılır:

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> Sp_create_openrowset_statistics ve sp_drop_openrowset_statistics yürütmek için aşağıdaki izinler gerekir: Toplu IŞLEMLERI yönetme veya VERITABANı toplu IŞLEMLERINI yönetme.

Bağımsız değişkenler: [ @stmt =] N ' statement_text '-istatistikler oluşturulduğunda kullanılan Transact-SQL Ifadesini belirtir.

population.csv dosyasını temel alan veri kümesindeki yıl sütununun istatistiklerini güncelleştirmek için, istatistikleri bırakıp oluşturmanız gerekir:

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Örnekler: dış tablo sütunu için istatistik oluşturma

Aşağıdaki örneklerde, istatistik oluşturmak için çeşitli seçeneklerin nasıl kullanılacağı gösterilmektedir. Her bir sütun için kullandığınız seçenekler, verilerinizin özelliklerine ve sütun sorgularda nasıl kullanılacağını bağlıdır.

> [!NOTE]
> Yalnızca şu an için tek sütunlu istatistikler oluşturabilirsiniz.

Bir sütunda istatistik oluşturmak için, istatistik nesnesi ve sütunun adı için bir ad sağlayın.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Bağımsız değişkenler: external_table istatistiklerin oluşturulması gereken dış tabloyu belirtir.

FULLSCAN tüm satırları tarayarak Işlem istatistiklerini tarar. FULLSCAN ve örnek %100 aynı sonuçlara sahiptir. FULLSCAN örnek seçeneğiyle kullanılamaz.

YÜZDE örnek numarası, istatistik oluşturduğunda kullanılacak sorgu iyileştiricisi için tablodaki veya dizinli görünümdeki satırların yaklaşık yüzdesini veya sayısını belirtir. Sayı 0 ile 100 arasında olabilir.

ÖRNEK, FULLSCAN seçeneğiyle birlikte kullanılamaz.

> [!NOTE]
> CSV örneklemesi Şu anda çalışmıyor, CSV için yalnızca FULLSCAN desteklenir.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Her satırı inceleyerek tek sütunlu istatistikler oluşturma

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Örnek boyutunu belirterek tek sütunlu istatistikler oluşturma

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Örnekler: güncelleştirme istatistikleri

İstatistikleri güncelleştirmek için, istatistikleri bırakıp, oluşturmanız gerekir. Önce istatistikleri bırakın:

```sql
DROP STATISTICS census_external_table.sState
```

Ve istatistik oluştur:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

### <a name="statistics-metadata"></a>İstatistik meta verileri

İstatistikler hakkında bilgi edinmek için kullanabileceğiniz çeşitli sistem görünümleri ve işlevleri vardır. Örneğin, STATS_DATE () işlevini kullanarak bir istatistik nesnesinin güncel olup olmadığını görebilirsiniz. STATS_DATE (), istatistiklerin en son ne zaman oluşturulduğunu veya güncelleştirildiğini görmenizi sağlar.

> [!NOTE]
> İstatistik meta verileri yalnızca dış tablo sütunları için kullanılabilir. İstatistik meta verileri OPENROWSET sütunları için kullanılamaz.

#### <a name="catalog-views-for-statistics"></a>İstatistikler için katalog görünümleri

Bu sistem görünümleri istatistikler hakkında bilgi sağlar:

| Katalog görünümü                                                 | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Her sütun için bir satır.                                     |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Veritabanındaki her nesne için bir satır.                     |
| [sys. schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Veritabanındaki her şema için bir satır.                     |
| [sys. stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Her istatistik nesnesi için bir satır.                          |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | İstatistikler nesnesindeki her sütun için bir satır. Sys. Columns öğesine geri bağlantı. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Her tablo için bir satır (dış tabloları içerir).           |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Her veri türü için bir satır.                                  |

#### <a name="system-functions-for-statistics"></a>İstatistikler için sistem işlevleri

Bu sistem işlevleri, istatistiklerle çalışmak için faydalıdır:

| Sistem işlevi                                              | Description                                  |
| :----------------------------------------------------------- | :------------------------------------------- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) | İstatistik nesnesinin son güncelleştirilme tarihi. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>İstatistik sütunları ve işlevleri tek bir görünümde birleştirin

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   st.[user_created] = 1
;
```

## <a name="next-steps"></a>Sonraki adımlar

Adanmış SQL havuzunun sorgu performansını daha da geliştirmek için bkz. [Özel SQL havuzu için](best-practices-dedicated-sql-pool.md#maintain-statistics) [iş yükünüzü](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ve en iyi uygulamalarınızı izleme.

Sunucusuz SQL havuzunun sorgu performansını daha da geliştirmek için bkz. [sunucusuz SQL havuzu Için en iyi yöntemler](best-practices-serverless-sql-pool.md)

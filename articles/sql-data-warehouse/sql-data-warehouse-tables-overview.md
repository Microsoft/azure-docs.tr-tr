---
title: Tabloları tasarlama
description: Azure SQL veri ambarı 'nda tablo tasarlamaya giriş.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9220d3adb31005551b6358034207f1071065b1a7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692389"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda tablo tasarlama

Azure SQL veri ambarı 'nda tablo tasarlamaya yönelik temel kavramları öğrenin. 

## <a name="determine-table-category"></a>Tablo kategorisini belirleme 

Bir [yıldız şeması](https://en.wikipedia.org/wiki/Star_schema) , verileri olgu ve boyut tablolarına düzenler. Bazı tablolar, bir olgu veya boyut tablosuna geçmeden önce tümleştirme veya hazırlama verileri için kullanılır. Bir tablo tasarlarken tablo verilerinin bir olgu, boyut veya tümleştirme tablosunda yer alıyor olup olmadığına karar verin. Bu karar, uygun tablo yapısına ve dağıtımına bildirir. 

- **Olgu tabloları** , genellikle bir işlem sisteminde oluşturulan ve veri ambarına yüklenen nicel verilerini içerir. Örneğin, bir perakende iş her gün satış işlemleri oluşturur ve ardından verileri analiz için bir veri ambarı olgu tablosuna yükler.

- **Boyut tabloları** , değişebilir ancak genellikle seyrek olarak değişen öznitelik verilerini içerir. Örneğin, bir müşterinin adı ve adresi bir Boyut tablosunda depolanır ve yalnızca müşterinin profili değiştiğinde güncelleştirilir. Büyük olgu tablosunun boyutunu en aza indirmek için müşterinin adının ve adresinin bir olgu tablosunun her satırında olması gerekmez. Bunun yerine, olgu tablosu ve boyut tablosu bir müşteri KIMLIĞINI paylaşabilir. Bir sorgu, müşterinin profilini ve işlemlerini ilişkilendirmek için iki tabloya katılabilir. 

- **Tümleştirme tabloları** , verileri tümleştirmek veya hazırlamak için bir yer sağlar. Bir tümleştirme tablosunu normal tablo, dış tablo veya geçici bir tablo olarak oluşturabilirsiniz. Örneğin, hazırlama tablosuna veri yükleyebilir, hazırlama sırasında veriler üzerinde dönüşümler gerçekleştirebilir ve ardından verileri bir üretim tablosuna ekleyebilirsiniz.

## <a name="schema-and-table-names"></a>Şema ve tablo adları
Şemaları, benzer bir biçimde, birlikte kullanılan tabloları gruplamak için iyi bir yoldur.  Şirket içi bir çözümden birden çok veritabanını SQL veri ambarı 'na geçiriyorsanız, tüm olgu, boyut ve tümleştirme tablolarını SQL veri ambarı 'nda tek bir şemaya geçirmek en iyi şekilde çalışmaktadır. Örneğin, tüm tabloları, wwi adlı bir şema içindeki [Wideworldimportersdw](/sql/sample/world-wide-importers/database-catalog-wwi-olap) örnek veri ambarında saklayabilirsiniz. Aşağıdaki kod, wwi adlı [Kullanıcı tanımlı bir şema](/sql/t-sql/statements/create-schema-transact-sql) oluşturur.

```sql
CREATE SCHEMA wwi;
```

SQL veri ambarı 'nda tabloların organizasyonunu göstermek için, tablo adlarına önek olarak olgu, Dim ve Int kullanabilirsiniz. Aşağıdaki tabloda, WideWorldImportersDW için şema ve tablo adlarından bazıları gösterilmektedir.  

| WideWorldImportersDW tablosu  | Tablo türü | SQL Veri Ambarı |
|:-----|:-----|:------|:-----|
| Şehir | Boyut | wwi. DimCity |
| Sipariş verme | Halde | wwi. FactOrder |


## <a name="table-persistence"></a>Tablo kalıcılığı 

Tablolar, verileri kalıcı olarak Azure Storage 'da, Azure depolama 'da veya veri ambarına harici bir veri deposunda depolar.

### <a name="regular-table"></a>Normal tablo

Normal bir tablo, verileri veri ambarının bir parçası olarak Azure Storage 'da depolar. Bir oturumun açık olup olmamasına bakılmaksızın tablo ve veriler korunur.  Bu örnek, iki sütunlu bir normal tablo oluşturur. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Geçici tablo
Geçici bir tablo yalnızca oturum süresince bulunur. Diğer kullanıcıların geçici sonuçları görmesini ve ayrıca Temizleme gereksinimini azaltmasını engellemek için geçici bir tablo kullanabilirsiniz.  Geçici tablolar, hızlı performans sunmak için yerel depolamayı kullanır.  Daha fazla bilgi için bkz. [geçici tablolar](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Dış tablo
Dış tablo, Azure Depolama Blobu veya Azure Data Lake Store bulunan verilere işaret eder. CREATE TABLE SELECT ifadesiyle birlikte kullanıldığında, dış bir tablodan seçim yapmak verileri SQL Data Warehouse 'a aktarır. Bu nedenle, dış tablolar veri yükleme için yararlıdır. Yükleme öğreticisi için bkz. [Azure Blob depolamadan veri yüklemek Için PolyBase kullanma](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Veri türleri
SQL veri ambarı en yaygın kullanılan veri türlerini destekler. Desteklenen veri türlerinin bir listesi için, CREATE TABLE deyimindeki [Create Table başvuru içindeki veri türleri](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) bölümüne bakın. Veri türlerini kullanma hakkında yönergeler için bkz. [veri türleri](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Dağıtılmış tablolar
SQL veri ambarı 'nın temel bir özelliği, [dağıtımların](massively-parallel-processing-mpp-architecture.md#distributions)tamamında tablo üzerinde depolama ve çalışma yöntemidir.  SQL veri ambarı, verileri dağıtmaya yönelik üç yöntemi destekler, hepsini bir kez deneme (varsayılan), karma ve yinelenmiş.

### <a name="hash-distributed-tables"></a>Karma Dağıtılmış tablolar
Karma olarak dağıtılan bir tablo, satırları dağıtım sütunundaki değere göre dağıtır. Karma Dağıtılmış bir tablo, büyük tablolardaki sorgular için yüksek performans elde etmek üzere tasarlanmıştır. Bir dağıtım sütunu seçerken göz önünde bulundurmanız gereken birkaç etken vardır. 

Daha fazla bilgi için bkz. [Dağıtılmış tablolar Için tasarım kılavuzu](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Çoğaltılmış tablolar
Çoğaltılan bir tablo, her Işlem düğümünde kullanılabilir olan tablonun tam kopyasına sahiptir. Çoğaltılan tablolardaki birleşimler veri hareketi gerektirmediğinden, sorgular çoğaltılan tablolarda hızlı çalışır. Çoğaltma, daha fazla depolama alanı gerektirir, ancak büyük tablolar için pratik değildir. 

Daha fazla bilgi için bkz. [çoğaltılan tablolar Için tasarım kılavuzu](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Hepsini bir kez deneme tabloları
Hepsini bir kez deneme tablosu, tablo satırlarını tüm dağıtımların arasına eşit dağıtır. Satırlar rasgele dağıtılır. Hepsini bir kez deneme tablosuna veri yüklemek hızlıdır.  Ancak sorgular, diğer dağıtım yöntemlerinden daha fazla veri taşıması gerektirebilir. 

Daha fazla bilgi için bkz. [Dağıtılmış tablolar Için tasarım kılavuzu](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Tablolar için ortak dağıtım yöntemleri
Tablo kategorisi genellikle tabloyu dağıtmak için hangi seçeneğin tercih verileceğini belirler. 

| Tablo kategorisi | Önerilen dağıtım seçeneği |
|:---------------|:--------------------|
| Halde           | Kümelenmiş columnstore diziniyle karma dağıtım kullanın. İki karma tablo aynı dağıtım sütununa katıldığında performans artar. |
| Boyut      | Daha küçük tablolar için çoğaltılan kullanın. Tablolar her bir Işlem düğümünde depolamaya çok büyükse, karma dağıtılmış kullanın. |
| Staging        | Hazırlama tablosu için hepsini bir kez deneme kullanın. CTAS ile yük hızlıdır. Veriler hazırlama tablosundan olduktan sonra Insert... öğesini kullanın. Verileri üretim tablolarına taşımak için SEÇIN. |

## <a name="table-partitions"></a>Tablo bölümleri
Bölümlenmiş bir tablo, veri aralıklarına göre tablo satırlarında işlem depolar ve gerçekleştirir. Örneğin, bir tablo güne, aya veya yıla göre bölümlenebilir. Bölüm içindeki verilerle bir sorgu taramasını sınırlayan, Bölüm eliminasyon aracılığıyla sorgu performansını artırabilirsiniz. Ayrıca, verileri bölüm değiştirme aracılığıyla da koruyabilirsiniz. SQL veri ambarı 'ndaki veriler zaten dağıtıldığından, çok fazla bölüm sorgu performansını yavaşlatabilir. Daha fazla bilgi için bkz. [bölümleme kılavuzu](sql-data-warehouse-tables-partition.md).  Bölüm boş olmayan tablo bölümlerine geçiş yaparken, var olan veriler kesilmişse [alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) DEYIMINIZDE TRUNCATE_TARGET seçeneğini kullanmayı düşünün. Aşağıdaki kod, dönüştürülmüş günlük verilerde, mevcut verilerin üzerine yazarak Satışolgusuna geçiş yapar. 

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Columnstore dizinleri
Varsayılan olarak, SQL veri ambarı bir tabloyu kümelenmiş bir columnstore dizini olarak depolar. Bu veri depolama alanı, büyük tablolardaki yüksek veri sıkıştırma ve sorgu performansına erişir.  Kümelenmiş columnstore dizini genellikle en iyi seçenektir, ancak bazı durumlarda kümelenmiş bir dizin veya yığın uygun depolama yapısıdır.  Yığın tablosu, son tabloya dönüştürülen hazırlama tablosu gibi geçici verileri yüklemek için özellikle kullanışlı olabilir.

Columnstore özelliklerinin bir listesi için bkz. [columnstore dizinleri yenilikleri](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Columnstore dizin performansını geliştirmek için bkz. [columnstore dizinleri için satır grubu kalitesini en üst düzeye çıkarma](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>İstatistikler
Sorgu iyileştiricisi, bir sorgu yürütmek için plan oluşturduğunda sütun düzeyi istatistikleri kullanır. Sorgu performansını artırmak için, özel sütunlarda, özellikle de sorgu birleşimlerinde kullanılan sütunlarda istatistik olması önemlidir. [Istatistiklerin oluşturulması](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic) otomatik olarak gerçekleşir.  Ancak, istatistiklerin güncelleştirilmesi otomatik olarak gerçekleşmez. Önemli sayıda satır eklendikten veya değiştirildikten sonra istatistikleri güncelleştirin. Örneğin, bir yüklemeden sonra istatistikleri güncelleştirin. Daha fazla bilgi için bkz. [istatistik Kılavuzu](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Birincil anahtar ve benzersiz anahtar
BIRINCIL anahtar yalnızca KÜMELENMEMIŞ ve zorunlu KıLıNMAYAN her ikisi de kullanıldığında desteklenir.  UNIQUE kısıtlaması yalnızca ZORLANMAMıŞ ile desteklenir.  [SQL veri ambarı tablo kısıtlamalarını](sql-data-warehouse-table-constraints.md)denetleyin.

## <a name="commands-for-creating-tables"></a>Tablo oluşturma komutları
Yeni bir boş tablo olarak tablo oluşturabilirsiniz. Ayrıca bir SELECT ifadesinin sonuçlarıyla bir tablo oluşturup doldurabilirsiniz. Aşağıda tablo oluşturmak için T-SQL komutları verilmiştir.

| T-SQL ekstresi | Açıklama |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Tüm tablo sütunlarını ve seçeneklerini tanımlayarak boş bir tablo oluşturur. |
| [DıŞ TABLO OLUŞTUR](/sql/t-sql/statements/create-external-table-transact-sql) | Dış tablo oluşturur. Tablonun tanımı SQL veri ambarı 'nda depolanır. Tablo verileri Azure Blob depolamada veya Azure Data Lake Store depolanır. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Bir SELECT ifadesinin sonuçlarıyla yeni bir tablo doldurur. Tablo sütunları ve veri türleri SELECT ifadesinin sonuçlarını temel alır. Bu ifade, verileri içeri aktarmak için bir dış tablodan seçim yapabilir. |
| [DıŞ TABLOYU SEÇ OLARAK OLUŞTUR](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Bir SELECT ifadesinin sonuçlarını dış konuma aktararak yeni bir dış tablo oluşturur.  Konum, Azure Blob depolama veya Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Veri ambarıyla kaynak verileri hizalama

Veri ambarı tabloları, başka bir veri kaynağından veri yükleyerek doldurulur. Başarılı bir yük gerçekleştirmek için, kaynak verilerdeki sütunların sayısı ve veri türleri, veri ambarındaki tablo tanımıyla hizalanmalıdır. Hizalanacak verilerin alınması, tablolarınızın tasarlanmasına ait olabilir. 

Veriler birden fazla veri deposundan geliyorsa verileri veri ambarına getirip bir tümleştirme tablosunda saklayabilirsiniz. Veriler tümleştirme tablosundan olduktan sonra, dönüştürme işlemlerini gerçekleştirmek için SQL veri ambarı 'nın gücünden yararlanabilirsiniz. Veriler hazırlandıktan sonra, bunu üretim tablolarına ekleyebilirsiniz.

## <a name="unsupported-table-features"></a>Desteklenmeyen tablo özellikleri
SQL veri ambarı, diğer veritabanları tarafından sunulan tablo özelliklerinin çoğunu, ancak hepsini destekler.  Aşağıdaki listede SQL veri ambarı 'nda desteklenmeyen bazı tablo özellikleri gösterilmektedir.

- Yabancı anahtar, Denetim [tablosu kısıtlamaları](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)
- [Hesaplanan sütunlar](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Dizinli görünümler](/sql/relational-databases/views/create-indexed-views)
- [Sırasına](/sql/t-sql/statements/create-sequence-transact-sql)
- [Seyrek sütunlar](/sql/relational-databases/tables/use-sparse-columns)
- Vekil anahtarlar. [Kimlik](sql-data-warehouse-tables-identity.md)ile uygulayın.
- [İmler](/sql/t-sql/statements/create-synonym-transact-sql)
- [Tetikleyiciler](/sql/t-sql/statements/create-trigger-transact-sql)
- [Benzersiz dizinler](/sql/t-sql/statements/create-index-transact-sql)
- [Kullanıcı tanımlı türler](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Tablo boyutu sorguları
60 dağıtımların her birindeki bir tablo tarafından tüketilen boşluk ve satırları belirlemenin basit bir yolu, [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql)kullanmaktır.

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Ancak, DBCC komutlarının kullanılması oldukça kısıtlabilmektedir.  Dinamik yönetim görünümleri (DMVs), DBCC komutlarından daha fazla ayrıntı gösterir. Bu görünümü oluşturarak başlayın.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Tablo alanı Özeti

Bu sorgu, tabloya göre satırları ve boşluğu döndürür.  Hangi tabloların en büyük tablolarınızı olduğunu ve bunların hepsini bir kez deneme, çoğaltma veya karma olarak dağıtılıp dağıtılmadığını görmenizi sağlar.  Karma Dağıtılmış tablolar için, sorgu dağıtım sütununu gösterir.  

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Dağıtım türüne göre tablo alanı

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Dizin türüne göre tablo alanı

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Dağıtım alanı Özeti

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Sonraki adımlar
Veri Ambarınızla ilgili tabloları oluşturduktan sonra, bir sonraki adım tabloya veri yüklemek olur.  Yükleme öğreticisi için bkz. [SQL veri ambarı 'na veri yükleme](load-data-wideworldimportersdw.md).

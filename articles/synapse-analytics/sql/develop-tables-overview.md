---
title: SYNAPSE SQL kullanarak tabloları tasarlama
description: SYNAPSE SQL 'de tablo tasarlamaya giriş.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 27cc53c3eef1bb2a9962d2c21ae80db3c8b0383d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585443"
---
# <a name="design-tables-using-synapse-sql-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te SYNAPSE SQL kullanarak tablo tasarlama

Bu belgede adanmış SQL havuzu ve sunucusuz SQL havuzu ile tablo tasarlamaya yönelik temel kavramlar yer almaktadır.  

[Sunucusuz SQL havuzu](on-demand-workspace-overview.md) , Data Lake 'unuzdaki veriler üzerinde bir sorgu hizmetidir. Veri alımı için yerel depolama alanı yoktur. [ADANMıŞ SQL havuzu](best-practices-dedicated-sql-pool.md) , SYNAPSE SQL kullanılırken sağlanmakta olan analitik kaynakların koleksiyonunu temsil eder. Adanmış bir SQL havuzunun boyutu, veri ambarı birimleri (DWU) tarafından belirlenir.

Aşağıdaki tabloda adanmış SQL havuzu ve sunucusuz SQL havuzu ile ilgili konular listelenmektedir:

| Konu                                                        | adanmış SQL havuzu | sunucusuz SQL havuzu |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Tablo kategorisini belirleme](#determine-table-category)        | Yes                | Hayır                      |
| [Şema adları](#schema-names)                                | Yes                | Yes                     |
| [Tablo adları](#table-names)                                  | Yes                | Hayır                      |
| [Tablo kalıcılığı](#table-persistence)                      | Yes                | Hayır                      |
| [Normal tablo](#regular-table)                              | Yes                | Hayır                      |
| [Geçici tablo](#temporary-table)                          | Yes                | Yes                     |
| [Dış tablo](#external-table)                            | Yes                | Yes                     |
| [Veri türleri](#data-types)                                    | Yes                | Yes                     |
| [Dağıtılmış tablolar](#distributed-tables)                    | Yes                | Hayır                      |
| [Karma dağıtılmış tablolar](#hash-distributed-tables)          | Yes                | Hayır                      |
| [Çoğaltılmış tablolar](#replicated-tables)                      | Yes                | Hayır                      |
| [Hepsini bir kez deneme tabloları](#round-robin-tables)                    | Yes                | Hayır                      |
| [Tablolar için ortak dağıtım yöntemleri](#common-distribution-methods-for-tables) | Yes                | Hayır                      |
| [Bölüme](#partitions)                                    | Yes                | Yes                     |
| [Columnstore dizinleri](#columnstore-indexes)                  | Yes                | Hayır                      |
| [İstatistikler](#statistics)                                    | Yes                | Yes                     |
| [Birincil anahtar ve benzersiz anahtar](#primary-key-and-unique-key)    | Yes                | Hayır                      |
| [Tablo oluşturma komutları](#commands-for-creating-tables) | Yes                | Hayır                      |
| [Veri ambarıyla kaynak verileri hizalama](#align-source-data-with-the-data-warehouse) | Yes                | Hayır                      |
| [Desteklenmeyen tablo özellikleri](#unsupported-table-features)    | Yes                | Hayır                      |
| [Tablo boyutu sorguları](#table-size-queries)                    | Yes                | Hayır                      |

## <a name="determine-table-category"></a>Tablo kategorisini belirleme

Bir [yıldız şeması](https://en.wikipedia.org/wiki/Star_schema) , verileri olgu ve boyut tablolarına düzenler. Bazı tablolar, bir olgu veya boyut tablosuna geçmeden önce tümleştirme veya hazırlama verileri için kullanılır. Bir tablo tasarlarken tablo verilerinin bir olgu, boyut veya tümleştirme tablosunda yer alıyor olup olmadığına karar verin. Bu karar, uygun tablo yapısına ve dağıtımına bildirir.

- **Olgu tabloları** , genellikle bir işlem sisteminde oluşturulan ve veri ambarına yüklenen nicel verilerini içerir. Örneğin, bir perakende iş her gün satış işlemleri oluşturur ve ardından verileri analiz için bir veri ambarı olgu tablosuna yükler.

- **Boyut tabloları** , değişebilir ancak genellikle seyrek olarak değişen öznitelik verilerini içerir. Örneğin, bir müşterinin adı ve adresi bir Boyut tablosunda depolanır ve yalnızca müşterinin profili değiştiğinde güncelleştirilir. Büyük olgu tablosunun boyutunu en aza indirmek için müşterinin adının ve adresinin bir olgu tablosunun her satırında olması gerekmez. Bunun yerine, olgu tablosu ve boyut tablosu bir müşteri KIMLIĞINI paylaşabilir. Bir sorgu, müşterinin profilini ve işlemlerini ilişkilendirmek için iki tabloya katılabilir.

- **Tümleştirme tabloları** , verileri tümleştirmek veya hazırlamak için bir yer sağlar. Bir tümleştirme tablosunu normal tablo, dış tablo veya geçici bir tablo olarak oluşturabilirsiniz. Örneğin, hazırlama tablosuna veri yükleyebilir, hazırlama sırasında veriler üzerinde dönüşümler gerçekleştirebilir ve ardından verileri bir üretim tablosuna ekleyebilirsiniz.

## <a name="schema-names"></a>Şema adları

Şemalar, benzer bir biçimde kullanılan nesneleri gruplamak için iyi bir yoldur. Aşağıdaki kod, wwi adlı [Kullanıcı tanımlı bir şema](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) oluşturur.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Tablo adları

Şirket içi bir çözümden birden çok veritabanını adanmış SQL havuzuna geçiriyorsanız, en iyi yöntem olgu, boyut ve tümleştirme tablolarının tümünü bir SQL havuzu şemasına geçirmesidir. Örneğin, tüm tabloları, wwi adlı bir şema içindeki [Wideworldimportersdw](/sql/samples/wide-world-importers-dw-database-catalog?view=azure-sqldw-latest&preserve-view=true) örnek veri ambarında saklayabilirsiniz.

Özel SQL havuzundaki tabloların organizasyonunu göstermek için, tablo adlarına önek olarak olgu, Dim ve Int kullanabilirsiniz. Aşağıdaki tabloda, WideWorldImportersDW için şema ve tablo adlarından bazıları gösterilmektedir.  

| WideWorldImportersDW tablosu  | Tablo türü | adanmış SQL havuzu |
|:-----|:-----|:------|:-----|
| Şehir | Boyut | wwi. DimCity |
| Sipariş | Fact | wwi. FactOrder |

## <a name="table-persistence"></a>Tablo kalıcılığı

Tablolar, verileri kalıcı olarak Azure Storage 'da, Azure depolama 'da veya veri ambarına harici bir veri deposunda depolar.

### <a name="regular-table"></a>Normal tablo

Normal bir tablo, verileri veri ambarının bir parçası olarak Azure Storage 'da depolar. Bir oturumun açık olup olmadığı, tablo ve veriler korunur.  Aşağıdaki örnek, iki sütunlu bir normal tablo oluşturur.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Geçici tablo

Geçici bir tablo yalnızca oturum süresince bulunur. Diğer kullanıcıların geçici sonuçları görmesini engellemek için geçici bir tablo kullanabilirsiniz. Geçici tabloların kullanılması, temizleme gereksinimini de azaltır.  Geçici tablolar yerel depolamayı kullanır ve adanmış SQL havuzlarında daha hızlı performans sağlayabilir.  

Sunucusuz SQL havuzu geçici tabloları destekler. Ancak geçici bir tablodan seçim yapabilirsiniz ancak depolama alanındaki dosyalarla birleştiremezsiniz.

Daha fazla bilgi için bkz.  [geçici tablolar](develop-tables-temporary.md).

### <a name="external-table"></a>Dış tablo

[Dış tablolar](develop-tables-external-tables.md) , Azure depolama blobu veya Azure Data Lake Storage bulunan verilere işaret ediyor.

Dış tablolardaki verileri, [Create Table Select](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ifadesini kullanarak adanmış SQL havuzlarına aktarın. Yükleme öğreticisi için bkz. [Azure Blob depolamadan veri yüklemek Için PolyBase kullanma](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Sunucusuz SQL havuzu için, [Cetas](develop-tables-cetas.md) kullanarak sorgu sonucunu Azure Storage 'daki bir dış tabloya kaydedebilirsiniz.

## <a name="data-types"></a>Veri türleri

Adanmış SQL havuzu en yaygın olarak kullanılan veri türlerini destekler. Desteklenen veri türlerinin bir listesi için, CREATE TABLE deyimindeki [Create Table başvuru içindeki veri türleri](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest#DataTypes&preserve-view=true) bölümüne bakın. Veri türlerini kullanma hakkında daha fazla bilgi için bkz. [veri türleri](../sql/develop-tables-data-types.md).

## <a name="distributed-tables"></a>Dağıtılmış tablolar

Adanmış SQL havuzunun temel bir özelliği, [dağıtımların](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)tamamında tablo üzerinde depolama ve çalışma yöntemidir.  Adanmış SQL havuzu, veri dağıtmak için üç yöntemi destekler:

- Hepsini bir kez deneme (varsayılan)
- Karma
- Çoğaltılmış

### <a name="hash-distributed-tables"></a>Karma dağıtılmış tablolar

Karma olarak dağıtılan bir tablo, satırları dağıtım sütunundaki değere göre dağıtır. Karma Dağıtılmış bir tablo, büyük tablolardaki sorgular için yüksek performans elde etmek üzere tasarlanmıştır. Bir dağıtım sütunu seçerken göz önünde bulundurmanız gereken birkaç etken vardır.

Daha fazla bilgi için bkz. [Dağıtılmış tablolar Için tasarım kılavuzu](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="replicated-tables"></a>Çoğaltılmış tablolar

Çoğaltılan bir tablo, her Işlem düğümünde kullanılabilir olan tablonun tam kopyasına sahiptir. Çoğaltılan tablolardaki birleşimler veri hareketi gerektirmediğinden, sorgular çoğaltılan tablolarda hızlı çalışır. Çoğaltma, daha fazla depolama alanı gerektirir, ancak büyük tablolar için pratik değildir.

Daha fazla bilgi için bkz. [çoğaltılan tablolar Için tasarım kılavuzu](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="round-robin-tables"></a>Hepsini bir kez deneme tabloları

Hepsini bir kez deneme tablosu, tablo satırlarını tüm dağıtımların arasına eşit dağıtır. Satırlar rasgele dağıtılır. Hepsini bir kez deneme tablosuna veri yüklemek hızlıdır.  Ancak sorgular diğer dağıtım yöntemlerinden daha fazla veri hareketi gerektirebilir.

Daha fazla bilgi için bkz. [Dağıtılmış tablolar Için tasarım kılavuzu](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="common-distribution-methods-for-tables"></a>Tablolar için ortak dağıtım yöntemleri

Tablo kategorisi genellikle tablo dağıtımı için en iyi seçeneği belirler.

| Tablo kategorisi | Önerilen dağıtım seçeneği |
|:---------------|:--------------------|
| Fact           | Kümelenmiş columnstore diziniyle karma dağıtım kullanın. İki karma tablo aynı dağıtım sütununa katıldığında performans artar. |
| Boyut      | Daha küçük tablolar için çoğaltılan kullanın. Tablolar her bir Işlem düğümünde depolamaya çok büyükse, karma dağıtılmış kullanın. |
| Hazırlama        | Hazırlama tablosu için hepsini bir kez deneme kullanın. CTAS ile yük hızlıdır. Veriler hazırlama tablosundan olduktan sonra Insert... öğesini kullanın. Verileri üretim tablolarına taşımak için SEÇIN. |

## <a name="partitions"></a>Bölümler

Adanmış SQL havuzlarında, bölümlenmiş bir tablo, veri aralıklarına göre tablo satırlarında işlemleri depolar ve yürütür. Örneğin, bir tablo güne, aya veya yıla göre bölümlenebilir. Bölüm içindeki verilerle bir sorgu taramasını sınırlayan, Bölüm eliminasyon aracılığıyla sorgu performansını artırabilirsiniz.

Ayrıca, verileri bölüm değiştirme aracılığıyla da koruyabilirsiniz. Adanmış bir SQL havuzundaki veriler zaten dağıtıldığından, çok fazla bölüm sorgu performansını yavaşlatabilir. Daha fazla bilgi için bkz. [bölümleme kılavuzu](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

> [!TIP]
> Bölüm boş olmayan tablo bölümlerine geçiş yaparken, var olan veriler kesilmişse [alter table](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) deyiminizdeki TRUNCATE_TARGET seçeneğini kullanmayı düşünün.

Aşağıdaki kod, dönüştürülmüş günlük verileri bir Salesolgu bölümüne geçirir ve mevcut verilerin üzerine yazar.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

Sunucusuz SQL havuzunda, sorgunuz tarafından okunacak dosyaları/klasörleri (bölümleri) sınırlayabilirsiniz. Yola göre bölümlendirme, [depolama dosyalarını sorgulama](develop-storage-files-overview.md)bölümünde açıklanan FilePath ve FileInfo işlevleri kullanılarak desteklenir. Aşağıdaki örnek, 2017 yılında verileri içeren bir klasörü okur:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>Columnstore dizinleri

Adanmış SQL havuzu, varsayılan olarak bir tabloyu kümelenmiş bir columnstore dizini olarak depolar. Bu veri depolama alanı, büyük tablolardaki yüksek veri sıkıştırma ve sorgu performansına erişir.  Kümelenmiş columnstore dizini genellikle en iyi seçenektir, ancak bazı durumlarda kümelenmiş bir dizin veya yığın uygun depolama yapısıdır.  

> [!TIP]
> Yığın tablosu, son tabloya dönüştürülen hazırlama tablosu gibi geçici verileri yüklemek için özellikle kullanışlı olabilir.

Columnstore özelliklerinin bir listesi için bkz. [columnstore dizinleri yenilikleri](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?view=azure-sqldw-latest&preserve-view=true). Columnstore dizin performansını geliştirmek için bkz. [columnstore dizinleri için satır grubu kalitesini en üst düzeye çıkarma](../sql/data-load-columnstore-compression.md).

## <a name="statistics"></a>İstatistikler

Sorgu iyileştiricisi, bir sorgu yürütmek için plan oluşturduğunda sütun düzeyi istatistikleri kullanır. Sorgu performansını artırmak için, özel sütunlarda, özellikle de sorgu birleşimlerinde kullanılan sütunlarda istatistik olması önemlidir. SYNAPSE SQL, otomatik istatistik oluşturmayı destekler. 

İstatistiksel güncelleştirme otomatik olarak gerçekleşmez. Önemli sayıda satır eklendikten veya değiştirildikten sonra istatistikleri güncelleştirin. Örneğin, bir yüklemeden sonra istatistikleri güncelleştirin. Ek bilgiler, [istatistik Kılavuzu](develop-tables-statistics.md) makalesinde sunulmaktadır.

## <a name="primary-key-and-unique-key"></a>Birincil anahtar ve benzersiz anahtar

Adanmış SQL havuzunda, BIRINCIL anahtar yalnızca KÜMELENMEMIŞ ve ZORLANMAZ durumunda desteklenir.  UNIQUE kısıtlaması yalnızca ZORLANMAMıŞ kullanıldığında desteklenir.  Daha fazla bilgi için bkz. [SQL havuzu tablo kısıtlamaları](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) makalesi.

## <a name="commands-for-creating-tables"></a>Tablo oluşturma komutları

Adanmış SQL havuzu için yeni bir boş tablo olarak tablo oluşturabilirsiniz. Ayrıca bir SELECT ifadesinin sonuçlarıyla bir tablo oluşturup doldurabilirsiniz. Aşağıda tablo oluşturmak için T-SQL komutları verilmiştir.

| T-SQL ekstresi | Description |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Tüm tablo sütunlarını ve seçeneklerini tanımlayarak boş bir tablo oluşturur. |
| [DıŞ TABLO OLUŞTUR](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Dış tablo oluşturur. Tablonun tanımı adanmış SQL havuzunda depolanır. Tablo verileri Azure Blob depolamada veya Azure Data Lake Storage depolanır. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Bir SELECT ifadesinin sonuçlarıyla yeni bir tablo doldurur. Tablo sütunları ve veri türleri SELECT ifadesinin sonuçlarını temel alır. Bu ifade, verileri içeri aktarmak için bir dış tablodan seçim yapabilir. |
| [DıŞ TABLOYU SEÇ OLARAK OLUŞTUR](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Bir SELECT ifadesinin sonuçlarını dış konuma aktararak yeni bir dış tablo oluşturur.  Konum, Azure Blob depolama veya Azure Data Lake Storage. |

## <a name="align-source-data-with-the-data-warehouse"></a>Kaynak verileri veri ambarına hizalayın

Ayrılmış SQL havuzu tabloları, başka bir veri kaynağından veri yükleyerek doldurulur. Başarılı bir yük elde etmek için, kaynak verilerdeki sütunların sayısı ve veri türleri, veri ambarındaki tablo tanımıyla hizalanmalıdır.

> [!NOTE]
> Hizalanacak verilerin alınması, tablolarınızın tasarlanmasına ait olabilir.

Veriler birden fazla veri deposundan geliyorsa, verilerin veri ambarına bağlantı noktası oluşturabilir ve bunu bir tümleştirme tablosunda saklayabilirsiniz. Veriler tümleştirme tablosundan olduktan sonra, dönüştürme işlemlerini uygulamak için adanmış SQL havuzunun gücünden yararlanabilirsiniz. Veriler hazırlandıktan sonra, bunu üretim tablolarına ekleyebilirsiniz.

## <a name="unsupported-table-features"></a>Desteklenmeyen tablo özellikleri

Adanmış SQL havuzu, diğer veritabanları tarafından sunulan tablo özelliklerinin çoğunu destekler, ancak tümünü desteklememektedir.  Aşağıdaki listede, adanmış SQL havuzunda desteklenmeyen bazı tablo özellikleri gösterilmektedir.

- Yabancı anahtar, Denetim [tablosu kısıtlamaları](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Hesaplanan Sütunlar](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Dizinli görünümler](/sql/relational-databases/views/create-indexed-views?view=azure-sqldw-latest&preserve-view=true)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Seyrek sütunlar](/sql/relational-databases/tables/use-sparse-columns?view=azure-sqldw-latest&preserve-view=true)
- Vekil anahtarlar, [kimlik](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ile uygulama
- [Eş Anlamlı Sözcükler](/sql/t-sql/statements/create-synonym-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Tetikleyiciler](/sql/t-sql/statements/create-trigger-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Benzersiz dizinler](/sql/t-sql/statements/create-index-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [Kullanıcı tanımlı türler](/sql/relational-databases/native-client/features/using-user-defined-types?view=azure-sqldw-latest&preserve-view=true)

## <a name="table-size-queries"></a>Tablo boyutu sorguları

Adanmış SQL havuzunda, 60 dağıtımların her birindeki bir tablo tarafından tüketilen alanı ve satırları belirlemenin basit bir yolu, [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?view=azure-sqldw-latest&preserve-view=true)kullanmaktır.

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

DBCC komutlarının kullanılması tam olarak sınırlandırabileceolabileceğini unutmayın.  Dinamik yönetim görünümleri (DMVs), DBCC komutlarından daha fazla ayrıntı gösterir. Aşağıdaki görünümü oluşturarak başlayın.

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
WHERE pn.[type] = 'COMPUTE'
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

Bu sorgu, tabloya göre satırları ve boşluğu döndürür.  Tablo alanı Özeti, en büyük tablo olan tabloları görmenizi sağlar. Ayrıca, hepsini bir kez deneme, çoğaltma veya karma dağıtılmış olup olmadığını da görürsünüz.  Karma Dağıtılmış tablolar için, sorgu dağıtım sütununu gösterir.  

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

Veri Ambarınızla ilgili tabloları oluşturduktan sonra, bir sonraki adım tabloya veri yüklemek olur.  Yükleme öğreticisi için bkz. [verileri ADANMıŞ SQL havuzuna yükleme](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool).
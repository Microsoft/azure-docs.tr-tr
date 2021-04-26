---
title: Columnstore dizin performansını iyileştirme
description: Bir columnstore dizininin her rowgroup 'a sıkıştıran satır sayısını en üst düzeye çıkarmak için bellek gereksinimlerini azaltın veya kullanılabilir belleği artırın.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a452808b1c349e2aec91759675e269e3680f0598
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567970"
---
# <a name="maximize-rowgroup-quality-for-columnstore-index-performance"></a>Columnstore dizini performansı için satır grubu kalitesini en üst düzeye çıkarın

Rowgroup kalitesi bir rowgroup 'taki satır sayısına göre belirlenir. Kullanılabilir belleğin artırılması, bir columnstore dizininin her rowgroup 'ta sıkıştıran satır sayısını en üst düzeye çıkarabilir.  Bu yöntemleri, columnstore dizinleri için sıkıştırma oranlarını ve sorgu performansını geliştirmek için kullanın.

## <a name="why-the-rowgroup-size-matters"></a>Satır grubu boyutunun neden önemli

Bir columnstore dizini, tek tek grupların sütun segmentlerini tarayarak bir tabloyu taradığından, her satır grubu 'taki satır sayısının en üst düzeye çıkarmak sorgu performansını geliştirir. RowGroups çok sayıda satıra sahip olduğunda veri sıkıştırma, diskten okunmaları daha az veri olduğu anlamına gelir.

RowGroups hakkında daha fazla bilgi için bkz. [columnstore dizinleri Kılavuzu](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true).

## <a name="target-size-for-rowgroups"></a>RowGroups için hedef boyut

En iyi sorgu performansı için, hedef, bir columnstore dizininde satır grubu başına satır sayısını en üst düzeye çıkarmaktır. Satır grubu 'un en fazla 1.048.576 satırı olabilir. Rowgroup başına en fazla satır sayısına sahip olmak normaldir. RowGroups en az 100.000 satır olduğunda, columnstore dizinleri iyi performans elde etmenizi ister.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>RowGroups sıkıştırma sırasında kırpılabilecek

Toplu yükleme veya columnstore dizini yeniden oluşturma sırasında, her bir rowgroup için belirlenmiş tüm satırları sıkıştırmak için yeterli kullanılabilir bellek yok. Bellek baskısı olduğunda, columnstore dizinleri satır grubu boyutlarını kırpabilir ve bu sayede columnstore için sıkıştırma başarılı olabilir.

Her rowgroup 'ta en az 10.000 satırı sıkıştırmak için yeterli bellek olmadığında bir hata oluşturulur.

Toplu yükleme hakkında daha fazla bilgi için bkz. [kümelenmiş bir columnstore dizinine toplu yükleme](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?view=azure-sqldw-latest#bulk&preserve-view=true).

## <a name="how-to-monitor-rowgroup-quality"></a>Satır grubu kalitesini izleme

DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.DM_DB_COLUMN_STORE_ROW_GROUP_PHYSICAL_STATS](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) SQL DB ile eşleşen görünüm tanımını içerir) RowGroups içindeki satır sayısı ve kırpma durumunda kırpma nedeni gibi yararlı bilgiler sunan. Satır grubu kırpması hakkında bilgi almak için bu DMV sorgusunun kolay bir yolu olarak aşağıdaki görünümü oluşturabilirsiniz.

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

Trim_reason_desc, satır grubu 'un kırpıldığını söyler (trim_reason_desc = NO_TRIM kırpma olmadığını ve satır grubunu en iyi kaliteden olduğunu gösterir). Aşağıdaki kırpma nedenleri rowgroup 'un zamanından önce kırpılmasını gösterir:

- BULKLOAD: Bu kırpma nedeni, yük için gelen satır toplu işi 1.000.000 satırdan az olduğunda kullanılır. Altyapı, eklenen 100.000 'den fazla satır (Delta deposuna ekleme aksine) varsa sıkıştırılmış satır grupları oluşturur, ancak kırpma nedenini BULKLOAD olarak ayarlar. Bu senaryoda, toplu iş yüklerinizi daha fazla satır içerecek şekilde artırmayı düşünün. Ayrıca, satır grupları bölüm sınırlarına yayamayacağı için bölümleme şemanızın yeniden değerlendirildiğinden çok ayrıntılı olmamasını sağlayın.
- MEMORY_LIMITATION: 1.000.000 satırları olan satır grupları oluşturmak Için altyapının belirli bir çalışma belleği miktarı gereklidir. Yükleme oturumunun kullanılabilir belleği gereken çalışma belleğinden daha az olduğunda, satır grupları beklenenden önce kırpılmakta olur. Aşağıdaki bölümlerde, gereken belleği tahmin etme ve daha fazla bellek ayırma açıklanmaktadır.
- DICTIONARY_SIZE: Bu kırpma nedeni, geniş ve/veya yüksek kardinalite dizeleri olan en az bir dize sütunu olduğundan satır grubu kırpmasının oluştuğunu gösterir. Sözlük boyutu bellekte 16 MB ile sınırlıdır ve bu sınıra ulaşıldığında satır grubu sıkıştırılır. Bu durumla karşılaşırsanız sorunlu sütununu ayrı bir tabloda yalıtmak için göz önünde bulundurun.

## <a name="how-to-estimate-memory-requirements"></a>Bellek gereksinimlerini tahmin etme

Bir satır grubu 'u sıkıştırmak için gereken en yüksek bellek, yaklaşık olarak aşağıdaki gibidir:

- 72 MB +
- \#satır \* \# sütunları \* 8 bayt +
- \#satırlar \* \# kısa dize-sütun \* 32 bayt +
- \#\*sıkıştırma sözlüğü için uzun dize sütunları 16 MB

> [!NOTE]
> Kısa dize sütunlarındaki <= 32 baytlık dize veri türlerini kullanır ve uzun dize sütunları, > 32 baytlık dize veri türlerini kullanır.

Uzun dizeler, metin sıkıştırmak için tasarlanan bir sıkıştırma yöntemiyle sıkıştırılır. Bu sıkıştırma yöntemi metin desenleri depolamak için bir *Sözlük* kullanır. Bir sözlüğün en büyük boyutu 16 MB 'tır. Rowgroup 'taki her Long dize sütunu için yalnızca bir sözlük bulunur.

Columnstore bellek gereksinimleriyle ilgili ayrıntılı bir tartışma için bkz. video [SYNAPSE SQL ölçeklendirme: yapılandırma ve kılavuz](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Bellek gereksinimlerini azaltmanın yolları

RowGroups 'un columnstore dizinlerine sıkıştırılmasına yönelik bellek gereksinimlerini azaltmak için aşağıdaki teknikleri kullanın.

### <a name="use-fewer-columns"></a>Daha az sütun kullan

Mümkünse, tabloyu daha az sütun ile tasarlayın. Bir satır grubu, columnstore dosyasına sıkıştırıldığında, columnstore dizini her bir sütun kesimini ayrı ayrı sıkıştırır. Bu nedenle, sütun sayısı arttıkça bir satır grubu 'un sıkıştırılması için bellek gereksinimleri artar.

### <a name="use-fewer-string-columns"></a>Daha az dize sütunu kullan

Dize veri türleri sütunları, sayısal ve Tarih veri türlerinden daha fazla bellek gerektirir. Bellek gereksinimlerini azaltmak için, olgu tablolarından dize sütunlarını kaldırmayı ve bunları daha küçük boyut tablolarına yerleştirmeyi göz önünde bulundurun.

Dize sıkıştırması için ek bellek gereksinimleri:

- 32 karaktere kadar olan dize veri türleri, 32 değer başına ek bayt gerektirir.
- 32 karakterden daha fazla karakter içeren dize veri türleri, sözlük yöntemleri kullanılarak sıkıştırılır.  Satır grubu 'taki her sütun, sözlük oluşturmak için 16 MB 'a kadar ek olabilir.

### <a name="avoid-over-partitioning"></a>Aşırı Bölümlendirmeyi önleyin

Columnstore dizinleri bölüm başına bir veya daha fazla RowGroups oluşturur. Azure SYNAPSE Analytics 'teki veri ambarı için, veriler dağıtıldığından ve her bir dağıtım bölümlenmiş olduğundan bölüm sayısı hızla artar. Tabloda çok fazla bölüm varsa, RowGroups doldurmanız için yeterli sayıda satır olmayabilir. Satır eksikliği sıkıştırma sırasında bellek baskısı oluşturmaz, ancak en iyi columnstore sorgu performansına ulaşmayan RowGroups 'a yol açar.

Bölümlemenin aşırı oluşmaması için bir diğer neden, bölümlenmiş bir tablodaki bir columnstore dizinine satır yüklemeye yönelik bir bellek ek yükü vardır. Yük sırasında, birçok bölüm, her bölümde Sıkıştırılacak yeterli satıra sahip olana kadar bellekte tutulan gelen satırları alabilir. Çok fazla bölüm olması ek bellek baskısı oluşturur.

### <a name="simplify-the-load-query"></a>Yükleme sorgusunu basitleştirme

Veritabanı, sorgudaki tüm işleçler arasında bir sorgu için bellek iznini paylaşır. Bir Load sorgusunda karmaşık sıralamalar ve birleşimler olduğunda, sıkıştırma için kullanılabilir bellek azalır.

Sorgu yükleme üzerine odaklanmak için yükleme sorgusunu tasarlayın. Veriler üzerinde dönüşümler çalıştırmanız gerekiyorsa, bunları yükleme sorgusundan ayrı olarak çalıştırın. Örneğin, verileri bir yığın tablosunda aşamalandırın, dönüşümleri çalıştırın ve ardından hazırlama tablosunu columnstore dizinine yükleyin. 

### <a name="adjust-maxdop"></a>MAXDOP 'yi ayarla

Her dağıtım, dağıtım başına birden fazla CPU çekirdeği kullanılabilir olduğunda RowGroups öğesini paralel olarak bir şekilde sıkıştırır. Paralellik, bellek baskısı ve satır grubu bölünmesi ile sonuçlanabilme ek bellek kaynakları gerektirir.

Bellek basıncını azaltmak için, her bir dağıtım içinde yükleme işlemini seri modda çalışacak şekilde zorlamak için MAXDOP sorgu ipucunu kullanabilirsiniz.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Daha fazla bellek ayırma yolları

DWU boyutu ve Kullanıcı kaynak sınıfı, bir Kullanıcı sorgusu için kullanılabilir bellek miktarını tespit edin. Bir yük sorgusuna yönelik bellek iznini artırmak için, DWU sayısını artırabilir veya kaynak sınıfını artırabilirsiniz.

- DWU 'ları yükseltmek için bkz. [nasıl yaparım? ölçek performansı](../sql-data-warehouse/quickstart-scale-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .
- Bir sorgunun kaynak sınıfını değiştirmek için, bkz. [Kullanıcı kaynak sınıfı örneğini değiştirme](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#change-a-users-resource-class).

## <a name="next-steps"></a>Sonraki adımlar

SYNAPSE SQL 'de performansı artırmanın daha fazla yolunu öğrenmek için bkz. [performansa genel bakış](../overview-terminology.md).


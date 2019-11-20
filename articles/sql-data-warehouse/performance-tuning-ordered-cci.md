---
title: Sıralı kümelenmiş columnstore diziniyle performans ayarı
description: Sorgu performansınızı geliştirmek için sıralı kümelenmiş columnstore dizini kullanırken bilmeniz gereken öneriler ve önemli noktalar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 3cc2f140eeed0a4667a01aa8c5ccbad7e4411521
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685996"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Sıralı kümelenmiş columnstore diziniyle performans ayarı  

Kullanıcılar Azure SQL veri ambarı 'nda bir columnstore tablosunu sorgudığında, iyileştirici her kesimde depolanan en düşük ve en yüksek değerleri denetler.  Sorgu koşulunun sınırları dışında kalan segmentler diskten belleğe okunamaz.  Okunan parçaların sayısı ve toplam boyutu küçük olduğunda sorgu daha hızlı bir performans alabilir.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Sıralı ve sıralı olmayan kümelenmiş columnstore dizini 
Varsayılan olarak, bir dizin seçeneği olmadan oluşturulan her Azure veri ambarı tablosu için, bir iç bileşen (Dizin Oluşturucu), üzerinde sıralı olmayan bir kümelenmiş columnstore dizini (CCı) oluşturur.  Her sütundaki veriler ayrı bir CCI satır grubu segmentinde sıkıştırılır.  Her bir segmentin değer aralığında meta veriler bulunur, bu nedenle sorgu koşulunun sınırları dışında kalan segmentler sorgu yürütme sırasında diskten okunmazlar.  CCı, en yüksek düzeyde veri sıkıştırması sağlar ve sorguların daha hızlı çalışabilmesi için okunacak parçaların boyutunu azaltır. Ancak, Dizin Oluşturucu verileri segmentlere sıkıştırmadan önce sıralamadığından, çakışan değer aralıklarına sahip kesimler meydana gelebilir ve sorguların diskten daha fazla kesim okumasına ve daha uzun sürmesine neden olabilir.  

Sıralı bir CCı oluştururken, Dizin Oluşturucu onları Dizin kesimlerine sıkıştırmadan önce, Azure SQL veri ambarı altyapısı bellekteki mevcut verileri sıra anahtarları ile sıralar.  Sıralanmış verilerle, çakışan bölüm, sorguların daha verimli bir kesim yok etme ve bu nedenle diskten okunan segmentlerin sayısı daha az olduğundan daha hızlı performans sağlar.  Tüm veriler bellekte aynı anda sıralanmışsa, çakışan segmentden kaçınılabilir.  Veri ambarı tablolarında verilerin büyük boyutu verildiğinde, bu senaryo genellikle gerçekleşmez.  

Bir sütunun kesim aralıklarını denetlemek için şu komutu tablo adınızla ve sütun adınızla çalıştırın:

```sql
SELECT o.name, pnp.index_id, cls.row_count, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table_Name>' and cols.name = '<Column_Name>' 
ORDER BY o.name, pnp.distribution_id, cls.min_data_id

```

> [!NOTE] 
> Sıralı bir CCı tablosunda, aynı DML veya veri yükleme işlemlerinden kaynaklanan yeni veriler, bu toplu iş içinde sıralanır ve tablodaki tüm verilerde Genel sıralama yapılmaz.  Kullanıcılar tablodaki tüm verileri sıralamak için sıralı CCı 'yı YENIDEN oluşturabilir.  Azure SQL veri ambarı 'nda, columnstore dizini yeniden oluşturma, çevrimdışı bir işlemdir.  Bölümlenmiş bir tablo için, yeniden oluşturma tek seferde bir bölüm olarak gerçekleştirilir.  Yeniden oluşturulmakta olan bölümdeki veriler "çevrimdışı" ve bu bölüm için yeniden oluşturma tamamlanana kadar kullanılamaz. 

## <a name="query-performance"></a>Sorgu performansı

Sorgunun düzenli bir CCı 'den performans kazancı, sorgu desenlerine, verilerin boyutuna, verilerin ne kadar iyi sıralandığına, parçaların fiziksel yapısına ve sorgu yürütmesi için seçilen DWU ve kaynak sınıfına bağlıdır.  Kullanıcılar, sıralı bir CCı tablosu tasarlarken sütunları sıralamayı seçmeden önce tüm bu faktörleri incelemelidir.

Tüm bu desenleri içeren sorgular genellikle sıralı CCı ile daha hızlı çalışır.  
1. Sorgular eşitlik, eşitsizlik veya Aralık koşullarına sahiptir
1. Koşul sütunları ve sıralı CCı sütunları aynı.  
1. Koşul sütunları, sıralı CCı sütunlarının sütun sırası ile aynı sırada kullanılır.  
 
Bu örnekte, T1 tablosunun Col_C, Col_B ve Col_A dizisinde sıralanmış bir kümelenmiş columnstore dizini vardır.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Sorgu 1 ' in performansı, sıralı CCı 'dan diğer üç sorgudan daha fazla avantaj sağlayabilir. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Veri yükleme performansı

Sıralı bir CCı tablosuna yükleme verilerinin performansı bölümlenmiş bir tabloyla benzerdir.  Verilerin sıralı bir CCı tablosuna yüklenmesi, veri sıralama işlemi nedeniyle sıralı olmayan bir CCı tablosundan daha uzun sürebilir, ancak sorgular daha sonra sıralı CCı ile daha hızlı çalışabilir.  

Aşağıda, verileri farklı şemalara sahip tablolara yüklemenin bir örnek performans karşılaştırması verilmiştir.

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


CCı ve sıralı CCı arasında örnek bir sorgu performansı karşılaştırması aşağıda verilmiştir.

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Çakışan kesimi azalt

Çakışan parçaların sayısı, sıralanan CCı oluşturma sırasında sıralanacak verilerin boyutuna, kullanılabilir belleğe ve en yüksek paralellik (MAXDOP) ayarına bağlıdır. Aşağıda, sıralı CCı oluşturulurken çakışan parçayı azaltma seçenekleri verilmiştir.

- Dizin Oluşturucu verileri kesimlere sıkıştırmadan önce veri sıralamasına daha fazla bellek sağlamak için xlargerc kaynak sınıfını daha yüksek bir DWU üzerinde kullanın.  Dizin segmentinde, verilerin fiziksel konumu değiştirilemez.  Bir kesim içinde veya kesimlerde veri sıralaması yoktur.  

- MAXDOP = 1 ile sıralı CCı oluştur.  Sıralı CCı oluşturma için kullanılan her iş parçacığı verilerin bir alt kümesinde çalışarak yerel olarak sıralar.  Farklı iş parçacıkları tarafından sıralanan veriler arasında genel sıralama yoktur.  Paralel iş parçacıklarının kullanılması, sıralı bir CCı oluşturma süresini azaltabilir, ancak tek bir iş parçacığı kullanmaktan daha fazla çakışan kesim oluşturur.  Şu anda, MAXDOP seçeneği yalnızca SELECT komutu olarak CREATE TABLE kullanılarak sıralı bir CCı tablosu oluşturulurken desteklenir.  CREATE INDEX veya CREATE TABLE komutları aracılığıyla sıralı bir CCı oluşturma, MAXDOP seçeneğini desteklemez. Örneğin,

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```
- Verileri Azure SQL veri ambarı tablolarına yüklemeden önce sıralama anahtarına göre önceden sıralayın.


Aşağıda, Yukarıdaki önerilerden sonra çakışan sıfır kesimine sahip sıralı bir CCı tablo dağıtımına örnek verilmiştir. Sıralı CCı tablosu, MAXTAS ile bir DWU1000c veritabanında, MAXDOP 1 ve xlargerc kullanılarak 20 GB yığın tablosundan oluşturulur.  CCı, yinelenen olmayan bir BIGINT sütununda sıralanır.  

![Segment_No_Overlapping](media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Büyük tablolarda sıralı CCı oluşturma
Sıralı bir CCı oluşturma, çevrimdışı bir işlemdir.  Bölüm içermeyen tablolar için, sıralı CCı oluşturma işlemi tamamlanana kadar verilere kullanıcılar erişemez.   Bölümlenmiş tablolar için, altyapı sıralı CCı bölümünü bölüm tarafından oluşturduğundan, kullanıcılar sıralı CCı oluşturma işleminin işlem içinde olmadığı bölümlerdeki verilere erişmeye devam edebilir.   Büyük tablolarda sıralı CCı oluşturma sırasında kapalı kalma süresini en aza indirmek için bu seçeneği kullanabilirsiniz: 

1.  Hedef büyük tabloda (Table_A olarak adlandırılır) bölümler oluşturun.
2.  Tablo A ile aynı tablo ve bölüm şemasına sahip boş bir sıralı CCı tablosu (Table_B adlı) oluşturun.
3.  A tablosundan tablo B 'ye bir bölüm geçirin.
4.  Anahtarlı bölümü yeniden derlemek için tablo B üzerinde < Table_B > yeniden oluşturma < bölümünde ALTER INDEX < Ordered_CCI_Index > çalıştırın.  
5.  Table_A içindeki her bölüm için 3. ve 4. adımı yineleyin.
6.  Tüm bölümler Table_A ' den Table_B ' ye geçtikten ve yeniden derlendikten sonra Table_A ' i bırakın ve Table_A Table_B olarak yeniden adlandırın. 

## <a name="examples"></a>Örnekler

**A. sıralı sütunları ve sıra sıra sayısını denetlemek Için:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. sütun sırasını değiştirmek Için, sipariş listesinden sütun ekleyin veya kaldırın ya da CCı 'dan sıralı CCı 'ya geçiş yapın:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Sonraki adımlar
Geliştirme ile ilgili daha fazla ipucu için bkz. [SQL Data Warehouse geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

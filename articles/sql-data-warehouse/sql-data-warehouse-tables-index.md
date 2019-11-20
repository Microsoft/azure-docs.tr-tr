---
title: Tabloları dizinleme
description: Azure SQL veri ambarı 'nda tabloları dizine alma önerileri ve örnekleri.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 079891824bf71caf1ebfa575833de650a55ed5be
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685460"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>SQL veri ambarı 'nda tabloları dizinleme

Azure SQL veri ambarı 'nda tabloları dizine alma önerileri ve örnekleri.

## <a name="index-types"></a>Dizin türleri

SQL veri ambarı [kümelenmiş columnstore dizinleri](/sql/relational-databases/indexes/columnstore-indexes-overview), [kümelenmiş dizinler ve kümelenmemiş dizinler](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)gibi çeşitli dizin oluşturma seçenekleri ve [yığın](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes)olarak da bilinen Dizin olmayan bir seçenek sunar.  

Dizin içeren bir tablo oluşturmak için [Create Table (Azure SQL veri ambarı)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) belgelerine bakın.

## <a name="clustered-columnstore-indexes"></a>Kümelenmiş columnstore dizinleri

Varsayılan olarak, bir tabloda dizin seçeneği belirtilmediğinde SQL veri ambarı kümelenmiş bir columnstore dizini oluşturur. Kümelenmiş columnstore tabloları, hem en yüksek düzeyde veri sıkıştırması hem de en iyi genel sorgu performansını sunar.  Kümelenmiş columnstore tabloları, genellikle kümelenmiş dizin veya yığın tablolarını gerçekleştirecek ve genellikle büyük tablolar için en iyi seçenektir.  Bu nedenlerden dolayı, tablonuzun dizin oluşturma konusunda emin olduğunuzda, kümelenmiş columnstore en iyi başlangıç yerdir.  

Kümelenmiş bir columnstore tablosu oluşturmak için WıTH yan tümcesinde KÜMELENMIŞ COLUMNSTORE DIZININI belirtmeniz veya WıTH yan tümcesini devre dışı bırakmanız yeterlidir:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Kümelenmiş columnstore 'nın iyi bir seçenek olabileceği birkaç senaryo vardır:

- Columnstore tabloları varchar (max), nvarchar (max) ve varbinary (max) desteklemez. Bunun yerine yığın veya kümelenmiş dizini düşünün.
- Columnstore tabloları geçici veriler için daha verimli olabilir. Yığın ve belki de geçici tabloları göz önünde bulundurun.
- 60.000.000 ' den az satırı olan küçük tablolar. Yığın tablolarını göz önünde bulundurun.

## <a name="heap-tables"></a>Yığın tabloları

SQL veri ambarı 'nda geçici olarak veri sahanken, yığın tablosu kullanmanın genel işlemi daha hızlı hale getiriyor olduğunu fark edebilirsiniz. Bunun nedeni, Heap yükleri Dizin tablolarından daha hızlıdır ve bazı durumlarda sonraki okuma önbellekten yapılabilir.  Verileri yalnızca daha fazla dönüşüm çalıştırmadan önce hazırlamak için yüklüyorsanız, tabloyu yığın tablosuna yüklemek verileri kümelenmiş bir columnstore tablosuna yüklemeden çok daha hızlıdır. Ayrıca, verileri [geçici bir tabloya](sql-data-warehouse-tables-temporary.md) yüklemek, bir tabloyu kalıcı depolamaya yüklemeden daha hızlı yüklenir.  

Küçük arama tablolarında 60.000.000 satırdan az, genellikle yığın tabloları anlamlı hale getirir.  Küme columnstore tabloları, 60.000.000 ' den fazla satır olduğunda en iyi sıkıştırmayı elde etmek için başlar.

Yığın tablosu oluşturmak için WıTH yan tümcesinde yığın belirtmeniz yeterlidir:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Kümelenmiş ve kümelenmemiş dizinler

Kümelenmiş dizinler, tek bir satırın hızlıca alınması gerektiğinde kümelenmiş columnstore tablolarını gerçekleştirebilir. Yüksek hızda performans için tek veya çok az bir satır aramasının gerekli olduğu sorgularda, küme dizinini veya kümelenmemiş ikincil dizini düşünün. Kümelenmiş dizin kullanmanın dezavantajı, yalnızca kümelenmiş dizin sütununda yüksek düzeyde seçmeli bir filtre kullanan tek bir sorgu olduğunu belirttiklerdir. Diğer sütunlarda filtre 'yi geliştirmek için, diğer sütunlara kümelenmemiş bir dizin eklenebilir. Ancak, bir tabloya eklenen her dizin, yüklenecek alanı ve işleme süresini ekler.

Kümelenmiş dizin tablosu oluşturmak için, WıTH yan tümcesinde KÜMELENMIŞ DIZINI belirtmeniz yeterlidir:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Bir tabloya kümelenmemiş bir dizin eklemek için aşağıdaki sözdizimini kullanın:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Kümelenmiş columnstore dizinlerini iyileştirme

Kümelenmiş columnstore tabloları veriler halinde kesimlerde düzenlenir.  Bir columnstore tablosunda en iyi sorgu performansını elde etmek için yüksek kesimli bir kalite olması önemlidir.  Segment kalitesi, sıkıştırılmış bir satır grubundaki satır sayısıyla ölçülebilir.  Segment kalitesi en iyi, sıkıştırılan satır grubu başına en az 100K satır olduğu ve bir satır grubunun içerebileceği satır grubu yaklaşımının 1.048.576 satırı kadar olan satır sayısı kadar performans kazanmasının en iyiyiyiydir.

Aşağıdaki görünüm, satır grubu başına ortalama satırları hesaplamak ve en uygun alt küme columnstore dizinlerini tanımlamak için sisteminizde oluşturulabilir ve kullanılabilir.  Bu görünümdeki son sütun, dizinlerinizi yeniden derlemek için kullanılabilecek bir SQL ifadesini oluşturur.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Görünümü oluşturduğunuza göre, 100 ' den az satırı olan satır gruplarıyla tabloları tanımlamak için bu sorguyu çalıştırın. Kuşkusuz, daha iyi segment kalitesi arıyorsanız, 100K eşiğini artırmak isteyebilirsiniz.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Sorguyu çalıştırdıktan sonra verileri aramaya başlayabilir ve sonuçlarınızı çözümleyebilirsiniz. Bu tabloda, satır grubu analizinizdeki arama işlemleri açıklanmaktadır.

| Sütun | Bu verileri kullanma |
| --- | --- |
| [table_partition_count] |Tablo bölümlendiğinde, daha fazla açık satır grubu sayısını görmeyi bekleyebilir. Dağıtımındaki her bölüm teorik olarak onunla ilişkili bir açık satır grubuna sahiptir. Bunu Analize göre çarpanın. Bölümlenmiş küçük bir tablo, bölümleme tamamen kaldırılarak, sıkıştırmayı iyileştirecek şekilde iyileştirilebilir. |
| [row_count_total] |Tablo için toplam satır sayısı. Örneğin, sıkıştırılmış durumdaki satır yüzdesini hesaplamak için bu değeri kullanabilirsiniz. |
| [row_count_per_distribution_MAX] |Tüm satırlar eşit olarak dağıtılırsa, bu değer dağıtım başına hedef satır sayısı olacaktır. Bu değeri compressed_rowgroup_count ile karşılaştırın. |
| [COMPRESSED_rowgroup_rows] |Tablo için columnstore biçimindeki toplam satır sayısı. |
| [COMPRESSED_rowgroup_rows_AVG] |Ortalama satır sayısı bir satır grubu için en fazla satır sayısı kadar düşükse, verileri yeniden sıkıştırmak için CTAS veya ALTER INDEX REBUILD kullanmayı düşünün |
| [COMPRESSED_rowgroup_count] |Columnstore biçimindeki satır gruplarının sayısı. Bu sayı tabloyla ilişkili olarak çok yüksek ise, columnstore yoğunluğu düşük olan bir göstergedir. |
| [COMPRESSED_rowgroup_rows_DELETED] |Satırlar, columnstore biçiminde mantıksal olarak silinir. Sayı tablo boyutuna yüksekse, bölümü yeniden oluşturmayı veya dizini yeniden oluşturmayı düşünün. Bu, fiziksel olarak kaldırır. |
| [COMPRESSED_rowgroup_rows_MIN] |Bu değeri, columnstore 'inizdeki satır grupları için değer aralığını anlamak üzere ortalama ve en fazla sütunları ile birlikte kullanın. Yük eşiği üzerinden düşük bir sayı (Bölüm hizalı dağıtım başına 102.400), veri yükünde iyileştirmelerin kullanılabilir olduğunu önerir |
| [COMPRESSED_rowgroup_rows_MAX] |Yukarıdaki gibi |
| [OPEN_rowgroup_count] |Açık satır grupları normaldir. Her biri, her tablo dağıtımı için bir açık satır grubu (60) tahmin etmek için makul bir Aşırı sayılar, bölümler arasında veri yükleme önerir. Bir ses olduğundan emin olmak için bölümleme stratejisini çift işaretleyin |
| [OPEN_rowgroup_rows] |Her satır grubu, en fazla 1.048.576 satıra sahip olabilir. Açık satır gruplarının tam olarak şu anda nasıl olduğunu görmek için bu değeri kullanın |
| [OPEN_rowgroup_rows_MIN] |Açık gruplar, verilerin tabloya yüklendiğini veya önceki yükün bu satır grubuna kalan satırların geri kırpıldığını gösterir. AÇıK satır gruplarında ne kadar veri Sat olduğunu görmek için MIN, MAX, AVG sütunlarını kullanın. Küçük tablolar için tüm verilerin %100 ' i olabilir! Bu durumda, verileri columnstore 'e zorlamak için ALTER INDEX REBUıLD. |
| [OPEN_rowgroup_rows_MAX] |Yukarıdaki gibi |
| [OPEN_rowgroup_rows_AVG] |Yukarıdaki gibi |
| [CLOSED_rowgroup_rows] |Kapalı satır grubu satırlarına sağlamlık denetimi olarak bakın. |
| [CLOSED_rowgroup_count] |Kapalı satır gruplarının sayısı, herhangi bir varsa, herhangi bir görüldüğünde düşük olmalıdır. Kapalı satır grupları ALTER INDEX kullanılarak sıkıştırılmış satır gruplarına dönüştürülebilir... Komutu yeniden DÜZENLEYIN. Ancak bu genellikle gerekli değildir. Kapalı gruplar, arka plan "demet taşıyıcısı" işlemi tarafından otomatik olarak columnstore satır gruplarına dönüştürülür. |
| [CLOSED_rowgroup_rows_MIN] |Kapalı satır grupları çok yüksek bir doldur hızına sahip olmalıdır. Kapalı bir satır grubu için doldur oranı düşükse, daha sonra columnstore 'in daha fazla analizini yapmanız gerekir. |
| [CLOSED_rowgroup_rows_MAX] |Yukarıdaki gibi |
| [CLOSED_rowgroup_rows_AVG] |Yukarıdaki gibi |
| [Rebuild_Index_SQL] |Bir tablo için columnstore dizinini yeniden derlemek için SQL |

## <a name="causes-of-poor-columnstore-index-quality"></a>Yanlış columnstore dizin kalitesinin nedenleri

Bölüm kalitesi düşük olan tablolar belirlediyseniz, kök nedenini belirlemek istersiniz.  Aşağıda, kötü segment kalitesinin bazı yaygın nedenleri verilmiştir:

1. Dizin oluşturulduğunda bellek baskısı
2. DML işlemlerinin yüksek hacmi
3. Küçük veya Trickle yükleme işlemleri
4. Çok fazla bölüm

Bu faktörler, bir columnstore dizininin satır grubu başına en uygun 1.000.000 satırdan önemli ölçüde daha az olmasına neden olabilir. Ayrıca, satırların sıkıştırılmış bir satır grubu yerine Delta satır grubuna geçmesine neden olabilirler.

### <a name="memory-pressure-when-index-was-built"></a>Dizin oluşturulduğunda bellek baskısı

Sıkıştırılan satır grubu başına satır sayısı, satırın genişliği ve satır grubunu işlemek için kullanılabilir bellek miktarı ile doğrudan ilgilidir.  Satırlar columnstore tablolarına bellek baskısı altında yazıldığında, segment kalitesi düşebilir.  Bu nedenle en iyi yöntem, columnstore dizin tablolarınızı yazan oturumun mümkün olduğunca çok belleğe erişmesini vermektir.  Bellek ve eşzamanlılık arasında bir denge olduğundan, doğru bellek ayırma Kılavuzu, tablonuzun her satırındaki verilere, sisteminize ayrılan veri ambarı birimlerine ve oturuma verebileceğiniz eşzamanlılık yuvaları sayısına bağlıdır. , tablonuza veri yazıyor.

### <a name="high-volume-of-dml-operations"></a>DML işlemlerinin yüksek hacmi

Satırları güncelleştiren ve silen bir dizi DML işlemi, columnstore 'e inefficiency getirebilir. Bu, özellikle bir satır grubundaki satırların çoğunluğu değiştirildiğinde geçerlidir.

- Bir satırı sıkıştırılmış satır grubundan silme, satırı yalnızca mantıksal olarak silindi olarak işaretler. Bölüm veya tablo yeniden oluşturulana kadar satır sıkıştırılmış satır grubunda kalır.
- Satır eklemek, satırı değişim satırı grubu adlı bir iç rowstore tablosuna ekler. Ekleme satırı, Delta satırı grubu dolu olana ve kapalı olarak işaretlendiğinden columnstore 'e dönüştürülmez. Satır grupları, 1.048.576 satırlık maksimum kapasiteye ulaştığında kapalıdır.
- Columnstore biçimindeki bir satırın güncelleştirilmesi, mantıksal bir Delete ve sonra bir INSERT olarak işlenir. Insertedrow, Delta deposunda depolanabilir.

Bölüm hizalı dağıtım başına 102.400 satırlık toplu eşiği aşan toplu güncelleştirme ve ekleme işlemleri doğrudan columnstore biçimine gider. Ancak, eşit bir dağıtım olduğu varsayıldığında, bunun gerçekleşmesi için tek bir işlemde 6.144.000 ' den fazla satırı değiştirmeniz gerekir. Belirli bir bölüm hizalanmış dağıtım için satır sayısı 102.400 ' den küçükse, satırlar Delta deposuna gider ve satır grubunu kapatmak için yeterli satır eklenene veya değiştirilinceye kadar veya dizin yeniden oluşturulduktan sonra orada kalır.

### <a name="small-or-trickle-load-operations"></a>Küçük veya Trickle yükleme işlemleri

SQL Data Warehouse 'a akan küçük yükler bazen de Trickle yükleri olarak bilinir. Genellikle sistem tarafından alınan verilerin neredeyse sabit akışını temsil eder. Ancak, bu akış sürekli yakında olduğu için satır hacmi özellikle büyük değildir. Veriler genellikle columnstore biçimine doğrudan yük için gereken eşiğin altında önemli ölçüde düşüktür.

Bu durumlarda, verileri Azure Blob Storage 'da ilk kez almak ve yüklemeden önce birikmesini sağlamak daha iyi bir seçenektir. Bu teknik genellikle *mikro işleme*olarak bilinir.

### <a name="too-many-partitions"></a>Çok fazla bölüm

Göz önünde bulundurulması gereken bir şey, kümelenmiş columnstore Tablolarınızda bölümlemenin etkisinin olmasıdır.  Bölümlendirmadan önce, SQL veri ambarı verilerinizi zaten 60 veritabanlarına böler.  Bölümleme, verilerinizi daha fazla böler.  Verilerinizi bölümleyip, **her** bölümün bir kümelenmiş columnstore dizininden faydalanmak için en az 1.000.000 satıra ihtiyacı olduğunu düşünün.  Tablonuzu 100 bölüm olarak bölümleyebilirsiniz, kümelenmiş bir columnstore dizininden (60 dağıtımları *100 bölüm* 1.000.000 satırları) faydalanmak için tablonuzun en az 6.000.000.000 satıra ihtiyacı vardır. 100 bölümlü tablonuzda 6.000.000.000 satır yoksa, bölüm sayısını azaltın veya bunun yerine bir yığın tablosu kullanmayı deneyin.

Tablolarınız bazı verilerle yüklendikten sonra, alt optimum kümelenmiş columnstore dizinleri ile tabloları tanımlamak ve yeniden oluşturmak için aşağıdaki adımları izleyin.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Bölüm kalitesini artırmak için dizinleri yeniden oluşturma

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>1\. Adım: doğru kaynak sınıfını kullanan Kullanıcı tanımla veya oluştur

Segment kalitesini hemen artırmanın bir hızlı yolu dizini yeniden dermaktır.  Yukarıdaki görünümün döndürdüğü SQL, dizinlerinizi yeniden derlemek için kullanılabilen bir ALTER INDEX REBUıLD ifadesi döndürür. Dizinlerinizi yeniden oluştururken, dizininizin dizinini oluşturan oturuma yeterli bellek ayırdığınızdan emin olun.  Bunu yapmak için, bu tablodaki dizini önerilen en düşük düzeyde yeniden oluşturma izinlerine sahip olan bir kullanıcının kaynak sınıfını arttırın.

Aşağıda, kaynak sınıflarını artırarak bir kullanıcıya daha fazla bellek ayırmayı gösteren bir örnek verilmiştir. Kaynak sınıflarıyla çalışmak için bkz. [iş yükü yönetimi Için kaynak sınıfları](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>2\. Adım: kümelenmiş columnstore dizinlerini daha yüksek kaynak sınıfı kullanıcıyla yeniden oluşturma

1\. adımdaki (örn. LoadUser) Kullanıcı olarak oturum açın, bu, artık daha yüksek bir kaynak sınıfı kullanıyor ve ALTER INDEX deyimlerini yürütür. Bu kullanıcının, dizinin yeniden oluşturulduğu tablolarda ALTER iznine sahip olduğundan emin olun. Bu örneklerde, tüm columnstore dizininin nasıl yeniden oluşturulduğu veya tek bir bölümün nasıl yeniden oluşturulacağı gösterilmektedir. Büyük tablolarda, dizinleri tek bir bölüme yeniden oluşturmak daha pratik bir hale gelir.

Alternatif olarak, dizini yeniden oluşturmak yerine, [CTAS kullanarak](sql-data-warehouse-develop-ctas.md)tabloyu yeni bir tabloya kopyalayabilirsiniz. Hangi şekilde iyidir? Büyük hacimde veri için CTAS genellikle [alter dizininden](/sql/t-sql/statements/alter-index-transact-sql)daha hızlıdır. Daha küçük birimlerde veri, ALTER INDEX kullanımı daha kolaydır ve tabloyu takas etmeniz gerekmez.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

SQL veri ambarı 'nda bir dizinin yeniden oluşturulması, çevrimdışı bir işlemdir.  Dizinleri yeniden oluşturma hakkında daha fazla bilgi için, [columnstore dizinleri birleştirme](/sql/relational-databases/indexes/columnstore-indexes-defragmentation)ve [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql)'teki alter INDEX REBUILD bölümüne bakın.

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>3\. Adım: kümelenmiş columnstore segmentinin kalitesinin iyileştirdiğini doğrulama

Tabloyu kötü segment kalitesiyle tanımlayan sorguyu yeniden çalıştırın ve segment kalitesini doğrulayın.  Segment kalitesi geliştirmediği takdirde, tablonuzda bulunan satırlar çok geniş olabilir.  Dizinlerinizi yeniden oluştururken daha yüksek bir kaynak sınıfı veya DWU kullanmayı deneyin.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>CTAS ve bölüm değiştirme ile dizinleri yeniden oluşturma

Bu örnek [Select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ifadesini ve bölüm değiştirme olarak CREATE TABLE tablo bölümünü yeniden derlemek için kullanır.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

CTAS kullanarak bölümleri yeniden oluşturma hakkında daha fazla ayrıntı için bkz. [SQL veri ambarı 'nda bölümleri kullanma](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Sonraki adımlar

Tablo geliştirme hakkında daha fazla bilgi için bkz. [tablo geliştirme](sql-data-warehouse-tables-overview.md).

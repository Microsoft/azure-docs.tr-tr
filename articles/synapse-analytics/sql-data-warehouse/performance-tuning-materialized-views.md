---
title: Gerçekleştirilmiş görünümlerle performans ayarı
description: Sorgu performansınızı geliştirmek için gerçekleştirilmiş görünümleri kullanırken bilmeniz gereken öneriler ve noktalar hakkında bilgi edinin.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick; azure-synapse
ms.openlocfilehash: e137611809e2d2beefecfeaea11b4295bf6ba141
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678500"
---
# <a name="performance-tune-with-materialized-views"></a>Gerçekleştirilmiş görünümlerle performans ayarı

Azure SYNAPSE SQL havuzundaki gerçekleştirilmiş görünümler, herhangi bir sorgu değişikliği yapmadan hızlı performans sağlamak üzere karmaşık analitik sorgular için düşük bakım yöntemi sağlar. Bu makalede gerçekleştirilmiş görünümleri kullanma hakkında genel yönergeler ele alınmaktadır.

## <a name="materialized-views-vs-standard-views"></a>Gerçekleştirilmiş görünümler ve standart görünümler karşılaştırması

Azure SYNAPSE 'deki SQL havuzu standart ve gerçekleştirilmiş görünümleri destekler.  Her ikisi de SELECT ifadelerle oluşturulmuş ve sorguları Mantıksal tablolar olarak sunulan sanal tablolardır.  Görünümler ortak veri hesaplamasının karmaşıklığını yalıtır ve değişiklikleri hesaplama için bir soyutlama katmanı ekler. böylece sorguları yeniden yazmaya gerek kalmaz.  

Standart Görünüm, görünümün her seferinde verilerini hesaplar.  Diskte depolanan veri yok. İnsanlar genellikle standart görünümleri bir SQL havuzundaki mantıksal nesneleri ve sorguları düzenlemeye yardımcı olan bir araç olarak kullanır.  Standart bir görünüm kullanmak için bir sorgunun kendisine doğrudan başvuru yapması gerekir.

Gerçekleştirilmiş bir görünüm, verileri SQL havuzunda tıpkı bir tablo gibi önceden hesaplar, depolar ve korur.  Gerçekleştirilmiş bir görünüm kullanıldığında her seferinde yeniden hesaplama gerekmez.  Bu, gerçekleştirilmiş görünümlerde verilerin tümünü veya alt kümesini kullanan sorguların neden daha hızlı bir performans alabilir.  Daha da iyisi, sorgular kendisine doğrudan başvuru yapmadan gerçekleştirilmiş bir görünüm kullanabilir, bu nedenle uygulama kodunu değiştirmeniz gerekmez.  

Standart görünümde gereksinimlerin çoğu, gerçekleştirilmiş bir görünüm için de geçerlidir. Gerçekleştirilmiş görünüm sözdizimi ve diğer gereksinimler hakkında daha fazla bilgi için bkz. [Select olarak GERÇEKLEŞTIRILMIŞ görünüm oluşturma](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

| Karşılaştırma                     | Görünüm                                         | Gerçekleştirilmiş Görünüm
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Tanımı görüntüleme                 | SQL havuzunda depolanır.              | SQL havuzunda depolanır.
|İçeriği görüntüleme                    | Görünüm her kullanıldığında oluşturulur.   | Görünüm oluşturma sırasında SQL havuzunda ön işleme ve depolama. Temel tablolara veri eklendikçe güncelleştirildi.
|Veri yenileme                    | Her zaman güncelleştiriliyor                               | Her zaman güncelleştiriliyor
|Karmaşık sorgulardan Görünüm verilerini alma hızı     | Yavaş                                         | Hızlı  
|Ek depolama                   | Hayır                                           | Yes
|Syntax                          | CREATE VIEW                                  | GERÇEKLEŞTIRILMIŞ GÖRÜNÜMÜ SEÇ

## <a name="benefits-of-using-materialized-views"></a>Gerçekleştirilmiş görünümleri kullanmanın avantajları

Düzgün şekilde tasarlanan gerçekleştirilmiş bir görünüm aşağıdaki avantajları sağlar:

- Birleştirmelere ve toplama işlevlerine sahip karmaşık sorgular için yürütme süresini azaltın. Sorgu ne kadar karmaşık olursa, yürütme zamanı kaydetme olasılığı o kadar yüksektir. En avantaja, bir sorgunun hesaplama maliyeti yüksekse ve elde edilen veri kümesi küçük olduğunda kazanılabilir.  
- SQL havuzundaki iyileştirici, sorgu yürütme planlarını geliştirmek için dağıtılan gerçekleştirilmiş görünümleri otomatik olarak kullanabilir.  Bu işlem, daha hızlı sorgu performansı sağlayan kullanıcılar tarafından saydamdır ve gerçekleştirilmiş görünümlere doğrudan başvuru yapmak için sorgular gerektirmez.
- Görünümlerde düşük bakım gerektir.  Taban tablolardaki tüm artımlı veri değişiklikleri, gerçekleştirilmiş görünümlere zaman uyumlu şekilde otomatik olarak eklenir.  Bu tasarım, gerçekleştirilmiş görünümlerin doğrudan temel tabloları sorgulamak için aynı verileri döndürmesini sağlar.
- Gerçekleştirilmiş bir görünümdeki veriler, temel tablolardan farklı şekilde dağıtılabilir.  
- Gerçekleştirilmiş görünümlerde veri, normal tablolardaki verilerle aynı yüksek kullanılabilirlik ve dayanıklılık avantajlarını alır.  

SQL havuzunda uygulanan gerçekleştirilmiş görünümler de aşağıdaki ek avantajları sağlar:

Diğer veri ambarı sağlayıcılarıyla karşılaştırıldığında, Azure SYNAPSE Analytics 'te uygulanan gerçekleştirilmiş görünümler de aşağıdaki ek avantajları sağlar:

- Taban tablolardaki veri değişiklikleriyle otomatik ve zaman uyumlu veri yenileme. Kullanıcı eylemi gerekli değildir.
- Geniş kapsamlı toplama işlevi desteği. Bkz. [Select (Transact-SQL) olarak GERÇEKLEŞTIRILMIŞ görünüm oluşturma](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- Sorguya özgü gerçekleştirilmiş görünüm önerisi için destek.  Bkz. [açıkla (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="common-scenarios"></a>Genel senaryolar  

Gerçekleştirilmiş görünümler genellikle aşağıdaki senaryolarda kullanılır:

**Büyük verilere karşı karmaşık analitik sorguların performansını artırmanın gerekli olması gerekir**

Karmaşık analitik sorgular genellikle daha fazla toplama işlevleri ve tablo birleştirmeleri kullanır, bu da karışık ve sorgu yürütmesindeki birleşimler gibi daha fazla işlem ağır işleme neden olur.  Bu sorguların özel olarak büyük tablolar üzerinde tamamlanması daha uzun sürer.  

Kullanıcılar, sorguların ortak hesaplamalarından döndürülen veriler için gerçekleştirilmiş görünümler oluşturabilir, bu nedenle sorgular için bu verilere ihtiyaç duyduğunda, daha düşük işlem maliyetine ve daha hızlı sorgu yanıtına izin veren bir yeniden hesaplama gerekmez.

**Hiçbir veya en düşük sorgu değişikliği ile daha hızlı performans gerekiyor**

SQL havuzlarındaki şema ve sorgu değişiklikleri, normal ETL işlemlerini ve raporlamayı desteklemek için genellikle en düşük düzeyde tutulur.  Görünümler, görünümler tarafından tahakkuk eden maliyet sorgu performansından kazanımdan kayılarak, sorgu performansı ayarlama için gerçekleştirilmiş görünümleri kullanabilir.

Ölçeklendirme ve istatistik yönetimi gibi diğer ayarlama seçeneklerine kıyasla, gerçekleştirilmiş bir görünüm oluşturup sürdürmek için çok daha az bir üretim değişikliği ve potansiyel performans kazancı da daha yüksektir.

- Gerçekleştirilmiş görünümlerin oluşturulması veya sürdürülmesi, temel tablolara karşı çalışan sorguları etkilemez.
- Sorgu iyileştiricisi, bir sorgudaki doğrudan görünüm başvurusu olmadan dağıtılmış gerçekleştirilmiş görünümleri otomatik olarak kullanabilir. Bu yetenek, performans ayarlamasındaki sorgu değişikliği gereksinimini azaltır.

**Daha hızlı sorgu performansı için farklı veri dağıtımı stratejisi gerekir**

Azure SYNAPSE Analytics, dağıtılmış bir sorgu işleme sistemidir.  SQL tablosundaki veriler, üç [dağıtım stratejisinden](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (karma, round_robin veya çoğaltılan) biri kullanılarak 60 düğüm arasında dağıtılır.   

Veri dağıtımı tablo oluşturma zamanında belirtilir ve tablo bırakılana kadar değişmeden kalır. Gerçekleştirilmiş görünüm, karma ve round_robin veri dağıtımlarını destekler.  Kullanıcılar, temel tablolardan farklı olan, ancak en çok görünümleri kullanan sorguların performansı için en uygun olan veri dağıtımını seçebilirler.  

## <a name="design-guidance"></a>Tasarım Kılavuzu

Sorgu performansını artırmak için gerçekleştirilmiş görünümleri kullanmaya yönelik genel rehberlik aşağıda verilmiştir:

**İş yükünüz için tasarım**

Gerçekleştirilmiş görünümler oluşturmaya başlamadan önce, iş yükünüzü sorgu desenleri, önem derecesi, sıklık ve elde edilen verilerin boyutu açısından derinlemesine bir şekilde anlamak önemlidir.  

Kullanıcılar, sorgu iyileştiricisi tarafından önerilen gerçekleştirilmiş görünümler için> AÇıKLA WITH_RECOMMENDATIONS <SQL_statement çalıştırabilir.  Bu öneriler sorguya özgü olduğundan, tek bir sorgunun avantajlarından faydalanmış olan gerçekleştirilmiş bir görünüm aynı iş yükünde diğer sorgular için en iyi durumda olmayabilir.  

İş yükünüz gereksinimlerinize göre bu önerileri değerlendirin.  İdeal gerçekleştirilmiş görünümler iş yükünün performansına faydalanabilir.  

**Daha hızlı sorgular ve maliyet arasındaki zorunluluğunu getirir farkında olun**

Gerçekleştirilmiş her bir görünüm için bir veri depolama maliyeti ve görünümün sürdürülmesi için bir maliyet vardır.  Temel tablolarda veri değişiklikleri yapıldığında, gerçekleştirilmiş görünümün boyutu artar ve fiziksel yapısı da değişir.  Sorgu performansı düşüşünü önlemek için, gerçekleştirilmiş her görünüm SQL Analytics altyapısı tarafından ayrı tutulur.  

Gerçekleştirilmiş görünümler ve temel tablo değişikliklerinin sayısı arttıkça bakım iş yükü artar.   Kullanıcılar, gerçekleştirilmiş tüm görünümlerden tahakkuk eden maliyetin sorgu performans kazancı tarafından kaydırılarak yer olup olmadığını denetlemelidir.  

Bu sorguyu, bir SQL havuzundaki gerçekleştirilmiş görünüm listesi için çalıştırabilirsiniz:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Gerçekleştirilmiş görünümlerin sayısını azaltma seçenekleri:

- İş yükünüzün karmaşık sorguların sıklıkla kullandığı ortak veri kümelerini belirler.  İyileştiricinin bunları yürütme planları oluştururken yapı taşları olarak kullanabilmesi için, bu veri kümelerini depolamak üzere gerçekleştirilmiş görünümler oluşturun.  

- Az kullanım veya artık gerekli olmayan gerçekleştirilmiş görünümleri bırakın.  Devre dışı gerçekleştirilmiş bir görünüm korunmaz, ancak depolama maliyeti hala buna neden olur.  

- Verileri örtüşmese de aynı veya benzer temel tablolarda oluşturulan gerçekleştirilmiş görünümleri birleştirin.  Gerçekleştirilmiş görünümleri birleştirmek, farklı görünümlerin toplamından daha büyük bir görünüm oluşmasına neden olabilir, ancak görünüm bakım maliyeti azaltılmalıdır.  Örnek:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Tüm performans ayarları sorgu değişikliğini gerektirmez**

SQL Analytics iyileştirici, sorgu performansını artırmak için dağıtılmış gerçekleştirilmiş görünümleri otomatik olarak kullanabilir.  Bu destek, gerçekleştirilmiş görünümler oluşturmada desteklenmeyen toplamalar kullanan, görünümlere ve sorgulara başvurmayan sorgulara şeffaf bir şekilde uygulanır.  Sorgu değişikliğine gerek yoktur. Gerçekleştirilmiş bir görünümün kullanıldığını onaylamak için bir sorgunun tahmini yürütme planını kontrol edebilirsiniz.  

**Gerçekleştirilmiş görünümleri izle**

Gerçekleştirilmiş bir görünüm SQL havuzunda, kümelenmiş columnstore dizini (CCı) içeren bir tabloda olduğu gibi depolanır.  Gerçekleştirilmiş görünümden veri okuma, CCı Dizin segmentlerini taramayı ve temel tablolardan artımlı değişiklikler uygulamayı içerir. Artımlı değişikliklerin sayısı çok yüksekse, gerçekleştirilmiş bir görünümden bir sorgunun çözümlenmesi doğrudan temel tabloları sorgulamadan daha uzun sürebilir.  

Sorgu performansı düşüşünü önlemek için, görünümün overhead_ratio (total_rows/Max (1, base_view_row)) izlemek için [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) çalıştırmak iyi bir uygulamadır.  Overhead_ratio çok yüksekse, kullanıcılar gerçekleştirilmiş görünümü yeniden derlemelidir.

**Gerçekleştirilmiş görünüm ve sonuç kümesi önbelleğe alma**

Bu iki özellik, SQL Analytics 'te sorgu performansı ayarlama için aynı anda tanıtılmıştır.  Sonuç kümesi önbelleğe alma, yinelenen sorgulardan statik verilere karşı yüksek eşzamanlılık ve hızlı yanıt almak için kullanılır.  

Önbelleğe alınan sonucu kullanmak için, sorgu isteyen önbelleğin formu, önbelleği üreten sorguyla eşleşmelidir.  Ayrıca, önbelleğe alınan sonuç tüm sorguya uygulanır.  

Gerçekleştirilmiş görünümler, temel tablolardaki veri değişikliklerine izin verir.  Gerçekleştirilmiş görünümlerde bulunan veriler, bir sorgu parçasına uygulanabilir.  Bu destek, daha hızlı performans için bazı hesaplamayı paylaşan farklı sorgular tarafından aynı gerçekleştirilmiş görünümlerin kullanılmasına izin verir.

## <a name="example"></a>Örnek

Bu örnek, katalogdan daha fazla ücret harcamış müşterileri bulan TPCDS benzeri bir sorgu kullanır, tercih edilen müşterileri ve kaynak ülke/bölge bölgelerini belirler.   Sorgu SUM () ve GROUP BY içeren üç alt SELECT deyimi BIRLEŞIMDEN Ilk 100 kaydı seçmeyi içerir.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Sorgunun tahmini yürütme planını denetleyin.  18 karıştırılmış Les ve 17 birleştirme işlemi vardır ve bu işlemler yürütülmesi daha fazla zaman alır. Şimdi üç alt SELECT deyimlerinin her biri için bir gerçekleştirilmiş görünüm oluşturalım.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Özgün sorgunun yürütme planını yeniden denetleyin.  Artık 17 ' den 5 ' e kadar olan birleştirme sayısı ve artık karışmıyor.  Plandaki filtre işlemi simgesine tıklayın, çıkış listesi verileri temel tablolar yerine gerçekleştirilmiş görünümlerden okuduğunuzu gösterir.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

Gerçekleştirilmiş görünümler sayesinde, aynı sorgu herhangi bir kod değişikliği olmadan çok daha hızlı çalışır.  

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [SYNAPSE SQL Pool geliştirmesine genel bakış](sql-data-warehouse-overview-develop.md).

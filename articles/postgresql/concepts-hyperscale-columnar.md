---
title: Sütunlu tablo depolama önizleme-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı
description: Sütunlu depolamayı kullanarak verileri sıkıştırma (Önizleme)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024003"
---
# <a name="columnar-table-storage-preview"></a>Sütunlu tablo depolama (Önizleme)

> [!IMPORTANT]
> Hiperscale 'de (Citus) sütun tablosu depolaması Şu anda önizleme aşamasındadır. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Diğer yeni özelliklerin bir listesini, [hiper ölçek için Önizleme özellikleri (Citus)](hyperscale-preview-features.md)sayfasına bakabilirsiniz.

PostgreSQL için Azure veritabanı-Hyperscale (Citus), analitik ve veri ambarı iş yükleri için yalnızca Append sütunlu tablo depolamayı destekler. Sütunlarda (satırlar yerine) bitişik olarak depolandığında, veriler daha erişilebilir hale gelir ve sorgular bir sütun alt kümesini daha hızlı bir şekilde talep edebilir.

Sütunlu depolamayı kullanmak için, `USING columnar` bir tablo oluştururken belirtin:

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

Hiper ölçek (Citus) ekleme sırasında satırları "şeritler" içinde sütunlu depolamaya dönüştürür. Her bir Stripe, verilerin veya 150000 satırın bir işlem olduğu, hangisi daha az olduğunu tutar.  (Bir sütunlu tablonun Şerit boyutu ve diğer parametreleri [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) işleviyle değiştirilebilir.)

Örneğin, aşağıdaki ifade tüm beş satırı aynı Stripe içine koyar, çünkü tüm değerler tek bir işlemde eklenir:

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

Hiper ölçek (Citus), sütunlu verileri her Stripe için ayrı olarak sıkıştırdığından, mümkün olduğunda büyük şeritler yapmak en iyisidir. Kullanarak sütun tablolarımızla ilgili, sıkıştırma oranı, şerit sayısı ve her Stripe için Ortalama satır hakkında olguları görebiliriz `VACUUM VERBOSE` :

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

Çıktı, 1.31 x veri sıkıştırma elde etmek için zstd sıkıştırma algoritmasını kullanan hiper ölçeğin (Citus) kullanıldığını gösterir. Sıkıştırma oranı bir), eklenen verilerin boyutunu b 'ye karşılık bellekte hazırlandığından, bu verilerin nihai Stripe içinde sıkıştırıldığı şekilde karşılaştırır.

Nasıl ölçüldüğü için, sıkıştırma oranı tablo için satır ve sütunlu depolama arasındaki boyut farklılığı olabilir veya eşleşmeyebilir. Gerçekten bu farkı bulmanın tek yolu, aynı verileri içeren bir satır ve sütunlu tablo oluşturmak ve karşılaştırmaktır:

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

Küçük tablolarımız için, sütunlu depolama aslında daha fazla alan kullanır, ancak veriler büyüdükçe sıkıştırma kazanacaktır.

## <a name="example"></a>Örnek

Sütunlu depolama, tablo bölümleme ile iyi sonuç verir. Bir örnek için bkz. Citus Engine Community documentation, [sütunlu depolamayla arşivleme](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage).

## <a name="gotchas"></a>Tuzakları

* Sütunlu depolama, dizi başına sıkıştırır. Çizgiler her işlem için oluşturulur, bu nedenle işlem başına bir satır eklemek, tek satırları kendi şeritlerinde yerleştirir. Tek satır şeritlerini sıkıştırma ve performans, bir satır tablosundan daha kötüleşmelidir. Her zaman sütunlu bir tabloya her zaman toplu olarak ekleyin.
* Çok küçük şeritler oluşturduysanız ve columnarize sahipseniz, takılmış olursunuz.
  Tek çözüm, yeni bir sütunlu tablo oluşturmak ve verileri bir işlem içinde orijinalden kopyalamak içindir:
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* Temel olarak sıkıştırılabilir olmayan veriler bir sorun olabilir, ancak sütunlu depolama hala belirli sütunları seçerken yararlı olur. Diğer sütunları belleğe yüklemesi gerekmez.
* Satır ve sütun bölümlerinin karışımı olan bölümlenmiş bir tabloda, güncelleştirmelerin dikkatle hedeflenmesi gerekir. Yalnızca satır bölümlerine isabet etmek için bunları filtreleyin.
   * İşlem belirli bir satır bölümüne (örneğin,) hedefleniyorsa `UPDATE p2 SET i = i + 1` , başarılı olur; belirtilen bir sütunlu bölümde (örneğin,) hedefleniyorsa `UPDATE p1 SET i = i + 1` , başarısız olur.
   * İşlem bölümlenmiş tabloya hedeflenmiş ve tüm sütunlu bölümleri (örneğin) dışlayan bir WHERE yan tümcesi içeriyorsa `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'` , başarılı olur.
   * İşlem bölümlenmiş tabloya hedeflenmiş olmasına karşın bölüm anahtarı sütunlarında filtre uygulamaz, başarısız olur. Yalnızca sütunlu bölümlerdeki satırlarla eşleşen yan tümceler olsa bile, bu yeterli değildir; bölüm anahtarı da filtrelenmesi gerekir.

## <a name="limitations"></a>Sınırlamalar

Bu özellik hala bazı önemli sınırlamalara sahiptir. Bkz. [Hyperscale (Citus) sınırları ve sınırlamaları](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Sonraki adımlar

* Bir Citus [zaman serisi öğreticisinde](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (dış bağlantı) sütunlu depolama örneği örneğine bakın.

---
title: 'Öğretici: çalışan düğümlerinde parça verileri-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı'
description: Bu öğreticide, PostgreSQL için Azure veritabanı hiper ölçek (Citus) ile dağıtılmış tablolar oluşturma ve veri dağıtımını görselleştirme işlemlerinin nasıl yapılacağı gösterilmektedir.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/16/2020
ms.openlocfilehash: 7d93002af866aa653972182a13ea37d37e912ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630318"
---
# <a name="tutorial-shard-data-on-worker-nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Öğretici: PostgreSQL için Azure veritabanı 'nda çalışan düğümlerinde verileri parçalara atma – hiper ölçek (Citus)

Bu öğreticide, şu şekilde nasıl yapılacağını öğrenmek için PostgreSQL için Azure veritabanı-hiper ölçek (Citus) kullanacaksınız:

> [!div class="checklist"]
> * Karma Dağıtılmış parçalar oluşturma
> * Tablo parçaları 'nın nereye yerleştirileceğini görün
> * Eğilmiş dağılımı tanımla
> * Dağıtılmış tablolarda kısıtlamalar oluşturma
> * Dağıtılmış verilerde sorgu çalıştırma

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici, iki çalışan düğümü olan çalışan bir hiper ölçek (Citus) sunucu grubu gerektirir. Çalışan bir sunucu grubunuz yoksa, [sunucu grubu oluştur](tutorial-hyperscale-server-group.md) öğreticisini izleyin ve ardından buna geri dönün.

## <a name="hash-distributed-data"></a>Karma dağıtılan veriler

Tablo satırlarını birden çok PostgreSQL sunucusu arasında dağıtmak, hiper ölçekte ölçeklenebilir sorgular için önemli bir tekniktir (Citus). Birlikte, birden çok düğüm geleneksel bir veritabanından daha fazla veri tutabilir ve birçok durumda sorguları yürütmek için çalışan CPU 'Ları paralel olarak kullanabilir.

Önkoşullar bölümünde iki çalışan düğümü olan bir hiper ölçek (Citus) sunucu grubu oluşturduk.

![Koordinatör ve iki çalışan](tutorial-hyperscale-shard/nodes.png)

Düzenleyici düğümünün meta veri tabloları çalışanları ve dağıtılmış verileri izler. [Pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) tablosundaki etkin çalışanları kontrol edebilirsiniz.

```sql
select nodeid from pg_dist_node where isactive;
```
```
 nodeid | nodename
--------+-----------
      1 | 10.0.0.21
      2 | 10.0.0.23
```

> [!NOTE]
> Nodenames on Hyperscale (Citus), bir sanal ağ içindeki iç IP adresleridir ve gördüğünüz gerçek adresler farklı olabilir.

### <a name="rows-shards-and-placements"></a>Satırlar, parçalar ve yerleştirme

Çalışan düğümlerinin CPU ve depolama kaynaklarını kullanmak için tablo verilerini sunucu grubu genelinde dağıttık.  Tablo dağıtmak her satırı parça adlı bir mantıksal gruba atar *.* Bir tablo oluşturup dağıtalım:

```sql
-- create a table on the coordinator
create table users ( email text primary key, bday date not null );

-- distribute it into shards on workers
select create_distributed_table('users', 'email');
```

Hiper ölçek (Citus) her bir satırı, *dağıtım sütununun* değerine göre bir parçaya atar, bu durumda, bizim örneğimizde olduğu gibi belirttik `email` . Her satır tam olarak bir parça içinde olur ve her parça birden çok satır içerebilir.

![parçalara işaret eden satırlar içeren kullanıcılar tablosu](tutorial-hyperscale-shard/table.png)

Varsayılan olarak `create_distributed_table()` , [pg_dist_shard](reference-hyperscale-metadata.md#shard-table)meta veri tablosunda sayarak görebilmemiz için 32 parça oluşturur:

```sql
select logicalrelid, count(shardid)
  from pg_dist_shard
 group by logicalrelid;
```
```
 logicalrelid | count
--------------+-------
 users        |    32
```

Hiper ölçek (Citus), `pg_dist_shard` dağıtım sütunundaki değer karmasını temel alarak parçalara satır atamak için tabloyu kullanır. Bu öğretici için karma ayrıntıları önemli değildir. Hangi değerlerin hangi parça kimliklerine eşlendiğini görmek için Sorgulayabileceğiniz önemli şeyler:

```sql
-- Where would a row containing hi@test.com be stored?
-- (The value doesn't have to actually be present in users, the mapping
-- is a mathematical operation consulting pg_dist_shard.)
select get_shard_id_for_distribution_column('users', 'hi@test.com');
```
```
 get_shard_id_for_distribution_column
--------------------------------------
                               102008
```

Satırların parçalara eşlenmesi yalnızca mantıksal olarak belirlenir. Parçalara ayırma, depolama için belirli çalışan düğümlerine atanmalıdır (Hiperscale (Citus) parça *yerleşimini* çağırır.

![Çalışanlara atanan parçalar](tutorial-hyperscale-shard/shard-placement.png)

[Pg_dist_placement](reference-hyperscale-metadata.md#shard-placement-table)parça yerleşimi ' ne göz atabiliriz.
Görtiğimiz diğer meta veri tabloları ile birleştirme, her parçanın nerede olduğunu gösterir.

```sql
-- limit the output to the first five placements

select
    shard.logicalrelid as table,
    placement.shardid as shard,
    node.nodename as host
from
    pg_dist_placement placement,
    pg_dist_node node,
    pg_dist_shard shard
where placement.groupid = node.groupid
  and shard.shardid = placement.shardid
order by shard
limit 5;
```
```
 table | shard  |    host
-------+--------+------------
 users | 102008 | 10.0.0.21
 users | 102009 | 10.0.0.23
 users | 102010 | 10.0.0.21
 users | 102011 | 10.0.0.23
 users | 102012 | 10.0.0.21
```

### <a name="data-skew"></a>Veri eğriltme

Çalışan düğümlerine verileri eşit olarak yerleştirdiğinizde ve ilgili verileri aynı çalışanlara yerleştirdiğinizde bir sunucu grubu en verimli şekilde çalışır. Bu bölümde, ilk parçaya, yerleştirmede bulunan parçalı olarak odaklanacağız.

Göstermek için tablomızda örnek veri oluşturalım `users` :

```sql
-- load sample data
insert into users
select
    md5(random()::text) || '@test.com',
    date_trunc('day', now() - random()*'100 years'::interval)
from generate_series(1, 1000);
```

Parça boyutlarını görmek için parçalar üzerinde [tablo boyutu işlevleri](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) çalıştırabiliriz.

```sql
-- sizes of the first five shards
select *
from
    run_command_on_shards('users', $cmd$
      select pg_size_pretty(pg_table_size('%1$s'));
    $cmd$)
order by shardid
limit 5;
```
```
 shardid | success | result
---------+---------+--------
  102008 | t       | 16 kB
  102009 | t       | 16 kB
  102010 | t       | 16 kB
  102011 | t       | 16 kB
  102012 | t       | 16 kB
```

Parçaların eşit boyutta olduğunu görebiliriz. Bu yerlerin çalışanlar arasında eşit bir şekilde dağıtıldığını gördük, böylece çalışan düğümlerinin kabaca eşit sayıda satır tutabilmesini sağlayabilirsiniz.

Örneğimizde bulunan satırlar `users` , dağıtım sütununun özellikleri nedeniyle eşit olarak dağıtılır `email` .

1. E-posta adresi sayısı, parça sayısına eşit veya ondan büyük.
2. Her e-posta adresi başına satır sayısı benzerdir (bizim örneğimizde, her adres için tam olarak bir satır) ve e-posta bir anahtar bildirdik.

Her iki özelliğin de başarısız olduğu herhangi bir tablo ve dağıtım sütunu seçimi, çalışanlar üzerinde düzensiz veri boyutuyla, yani *veri eğimiyle* sona alınacaktır.

### <a name="add-constraints-to-distributed-data"></a>Dağıtılmış verilere kısıtlamalar ekleme

Hiper ölçek (Citus) kullanımı, [veritabanı kısıtlamaları](https://www.postgresql.org/docs/current/ddl-constraints.html)da dahil olmak üzere ilişkisel bir veritabanının güvenliğini kullanmaktan devam etmenize olanak tanır.
Ancak, bir sınırlama vardır. Dağıtılmış sistemlerin doğası nedeniyle, hiper ölçekte (Citus) çapraz başvuru, benzersizlik kısıtlamaları veya çalışan düğümleri arasında bilgi tutarlılığı yoktur.

`users`Tablo örneğimizi ilgili tabloyla birlikte ele alalım.

```sql
-- books that users own
create table books (
    owner_email text references users (email),
    isbn text not null,
    title text not null
);

-- distribute it
select create_distributed_table('books', 'owner_email');
```

Verimlilik için, `books` `users` sahip e-posta adresi ile aynı şekilde dağıyoruz. Benzer sütun değerlerine göre dağıtmak birlikte bulundurma olarak [adlandırılır.](concepts-hyperscale-colocation.md)

Anahtar bir dağıtım sütununda olduğundan, kullanıcılara yabancı anahtarla kitap dağıtırken sorun yoktu. Ancak, bir anahtar oluştururken sorun yaşıyoruz `isbn` :

```sql
-- will not work
alter table books add constraint books_isbn unique (isbn);
```
```
ERROR:  cannot create constraint on "books"
DETAIL: Distributed relations cannot have UNIQUE, EXCLUDE, or
        PRIMARY KEY constraints that do not include the partition column
        (with an equality operator if EXCLUDE).
```

Bir dağıtılmış tabloda en iyi şekilde yapabiliriz: sütunları, dağıtım sütunu için benzersiz bir mod haline getirme:

```sql
-- a weaker constraint is allowed
alter table books add constraint books_isbn unique (owner_email, isbn);
```

Yukarıdaki kısıtlama yalnızca ISBN Kullanıcı başına benzersiz hale getirir. Diğer bir seçenek ise, kitapları dağıtılmış tablo yerine bir [Başvuru tablosu](howto-hyperscale-modify-distributed-tables.md#reference-tables) yapmak ve kitapları kullanıcılarla ilişkilendiren ayrı bir dağıtılmış tablo oluşturmaktır.

## <a name="query-distributed-tables"></a>Dağıtılmış tabloları sorgula

Önceki bölümlerde, dağıtılmış tablo satırlarının çalışan düğümlerine parçalar halinde yerleştirildiğini gördük. Çoğu zaman verilerin bir sunucu grubunda nasıl veya nerede depolandığını bilmeniz gerekmez. Hyperscale (Citus), düzenli SQL sorgularını otomatik olarak ayıran bir dağıtılmış sorgu yürütücüsü içerir. Bu, verileri kapalı çalışan düğümleri üzerinde paralel olarak çalıştırır.

Örneğin, kullanıcıların ortalama yaşını bulmak için bir sorgu çalıştırabiliriz ve bu, dağıtılmış `users` tabloyu düzenleyicinin bir normal tablosu gibi kabul edebilir.

```sql
select avg(current_date - bday) as avg_days_old from users;
```
```
    avg_days_old
--------------------
 17926.348000000000
```

![Koordinatör aracılığıyla parçalara göre sorgu](tutorial-hyperscale-shard/query-fragments.png)

Arka planda, Hyperscale (Citus) yürütücüsü her parça için ayrı bir sorgu oluşturur, bunları çalışanlar üzerinde çalıştırır ve sonucu birleştirir. PostgreSQL AÇıKLA komutunu kullanırsanız bunu görebilirsiniz:

```sql
explain select avg(current_date - bday) from users;
```
```
                                  QUERY PLAN
----------------------------------------------------------------------------------
 Aggregate  (cost=500.00..500.02 rows=1 width=32)
   ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=100000 width=16)
     Task Count: 32
     Tasks Shown: One of 32
     ->  Task
       Node: host=10.0.0.21 port=5432 dbname=citus
       ->  Aggregate  (cost=41.75..41.76 rows=1 width=16)
         ->  Seq Scan on users_102040 users  (cost=0.00..22.70 rows=1270 width=4)
```

Çıktı, parça 102040 üzerinde çalışan bir *sorgu parçasının* yürütme planına bir örnek gösterir ( `users_102040` Worker 10.0.0.21 tablosu). Benzer oldukları için diğer parçalar gösterilmez. Çalışan düğümünün parça tablolarını taramakta olduğunu ve toplamı uygulayacağını görebiliriz. Düzenleyici düğümü, nihai sonucun toplamlarını birleştirir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, dağıtılmış bir tablo oluşturduk ve bunların parçaları ve yerleşimi hakkında bilgi edindik. Benzersizlik ve yabancı anahtar kısıtlamaları kullanmanın bir sınamasını gördük ve son olarak Dağıtılmış sorguların yüksek düzeyde nasıl çalıştığını gördünüz.

* Hiperscale (Citus) [tablo türleri](concepts-hyperscale-nodes.md) hakkında daha fazla bilgi edinin
* [Dağıtım sütunu seçme](concepts-hyperscale-choose-distribution-column.md) hakkında daha fazla ipucu alın
* [Tablo](concepts-hyperscale-colocation.md) bulundurma avantajları hakkında bilgi edinin

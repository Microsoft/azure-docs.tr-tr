---
title: Faydalı tanılama sorguları-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı
description: Dağıtılmış veriler ve daha fazlası hakkında bilgi edinmek için sorgular
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 4858f650aca1b704ac79482e0158fd83fc0264b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165250"
---
# <a name="useful-diagnostic-queries"></a>Faydalı tanılama sorguları

## <a name="finding-which-node-contains-data-for-a-specific-tenant"></a>Belirli bir kiracı için veri içeren düğümü bulma

Çok kiracılı kullanım durumunda, hangi çalışan düğümünün belirli bir kiracıya ait satırları içerdiğini tespit edebilirsiniz.  Hiper ölçek (Citus), dağıtılmış tablo satırlarını parçalar halinde gruplandırır ve her bir parçayı sunucu grubundaki bir çalışan düğümüne koyar. 

Uygulamanızın kiracılarının depoladığını ve hangi çalışan düğümünün depolama KIMLIĞI = 4 için verileri bulundurduğunu öğrenmek istiyoruz.  Diğer bir deyişle, dağıtım sütunu 4 değerine sahip olan satırlar içeren parça için yerleşimi bulmak istiyoruz:

``` postgresql
SELECT shardid, shardstate, shardlength, nodename, nodeport, placementid
  FROM pg_dist_placement AS placement,
       pg_dist_node AS node
 WHERE placement.groupid = node.groupid
   AND node.noderole = 'primary'
   AND shardid = (
     SELECT get_shard_id_for_distribution_column('stores', 4)
   );
```

Çıktı, çalışan veritabanının ana bilgisayar ve bağlantı noktasını içerir.

```
┌─────────┬────────────┬─────────────┬───────────┬──────────┬─────────────┐
│ shardid │ shardstate │ shardlength │ nodename  │ nodeport │ placementid │
├─────────┼────────────┼─────────────┼───────────┼──────────┼─────────────┤
│  102009 │          1 │           0 │ 10.0.0.16 │     5432 │           2 │
└─────────┴────────────┴─────────────┴───────────┴──────────┴─────────────┘
```

## <a name="finding-the-distribution-column-for-a-table"></a>Tablo için dağıtım sütununu bulma

Hiperscale (Citus) içindeki her dağıtılmış tablo bir "dağıtım sütunu" içerir. (Daha fazla bilgi için bkz. [Dağıtılmış veri modelleme](concepts-hyperscale-choose-distribution-column.md).) Hangi sütunu olduğunu bilmemiz önemli olabilir. Örneğin, tabloları birleştirirken veya filtrelerken, "dağıtım sütununa bir filtre Ekle" gibi ipuçları içeren hata iletileri görebilirsiniz.

`pg_dist_*`Düzenleyici düğümündeki tablolar, dağıtılmış veritabanıyla ilgili farklı meta veriler içerir. Özellikle `pg_dist_partition` , her tablo için dağıtım sütunuyla ilgili bilgiler içerir. Meta verilerdeki alt düzey ayrıntılardan dağıtım sütunu adını aramak için uygun bir yardımcı program işlevi kullanabilirsiniz. Aşağıda bir örnek ve çıktısı verilmiştir:

``` postgresql
-- create example table

CREATE TABLE products (
  store_id bigint,
  product_id bigint,
  name text,
  price money,

  CONSTRAINT products_pkey PRIMARY KEY (store_id, product_id)
);

-- pick store_id as distribution column

SELECT create_distributed_table('products', 'store_id');

-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Örnek çıktı:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ store_id      │
└───────────────┘
```

## <a name="detecting-locks"></a>Kilitleri algılama

Bu sorgu tüm çalışan düğümlerinde çalışır ve kilitleri, ne kadar açık olduğunu ve sorunlu sorguları belirler:

``` postgresql
SELECT run_command_on_workers($cmd$
  SELECT array_agg(
    blocked_statement || ' $ ' || cur_stmt_blocking_proc
    || ' $ ' || cnt::text || ' $ ' || age
  )
  FROM (
    SELECT blocked_activity.query    AS blocked_statement,
           blocking_activity.query   AS cur_stmt_blocking_proc,
           count(*)                  AS cnt,
           age(now(), min(blocked_activity.query_start)) AS "age"
    FROM pg_catalog.pg_locks         blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity
      ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks         blocking_locks
      ON blocking_locks.locktype = blocked_locks.locktype
     AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
     AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
     AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
     AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
     AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
     AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
     AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
     AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
     AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
     AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED
     AND blocking_locks.GRANTED
    GROUP BY blocked_activity.query,
             blocking_activity.query
    ORDER BY 4
  ) a
$cmd$);
```

Örnek çıktı:

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                               run_command_on_workers                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│ (10.0.0.16,5432,t,"")                                                             │
│ (10.0.0.20,5432,t,"{""update ads_102277 set name = 'new name' where id = 1; $ sel…│
│…ect * from ads_102277 where id = 1 for update; $ 1 $ 00:00:03.729519""}")         │
└───────────────────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-your-shards"></a>Parçalarınızın boyutunu sorgulama

Bu sorgu, size verilen bir dağıtılmış tablonun her parçanın boyutunu size sağlar `my_distributed_table` :

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT json_build_object(
    'shard_name', '%1$s',
    'size',       pg_size_pretty(pg_table_size('%1$s'))
  );
$cmd$);
```

Örnek çıktı:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                                result                                 │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │ {"shard_name" : "my_distributed_table_102008", "size" : "2416 kB"}    │
│  102009 │ t       │ {"shard_name" : "my_distributed_table_102009", "size" : "3960 kB"}    │
│  102010 │ t       │ {"shard_name" : "my_distributed_table_102010", "size" : "1624 kB"}    │
│  102011 │ t       │ {"shard_name" : "my_distributed_table_102011", "size" : "4792 kB"}    │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-all-distributed-tables"></a>Dağıtılan tüm tabloların boyutunu sorgulama

Bu sorgu, dağıtılmış her tablo için boyutların listesini ve bunların dizinlerinin boyutunu alır.

``` postgresql
SELECT
  tablename,
  pg_size_pretty(
    citus_total_relation_size(tablename::text)
  ) AS total_size
FROM pg_tables pt
JOIN pg_dist_partition pp
  ON pt.tablename = pp.logicalrelid::text
WHERE schemaname = 'public';
```

Örnek çıktı:

```
┌───────────────┬────────────┐
│   tablename   │ total_size │
├───────────────┼────────────┤
│ github_users  │ 39 MB      │
│ github_events │ 98 MB      │
└───────────────┴────────────┘
```

Dağıtılmış tablo boyutunu sorgulamak için başka bir Hyperscale (Citus) işlevi olduğuna göz atın. [tablo boyutunu belirleme](howto-hyperscale-table-size.md)konusuna bakın.

## <a name="identifying-unused-indices"></a>Kullanılmayan dizinleri tanımlama

Aşağıdaki sorgu, belirli bir dağıtılmış tablo () için çalışan düğümlerinde kullanılmayan dizinleri tanımlacaktır `my_distributed_table`

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT array_agg(a) as infos
  FROM (
    SELECT (
      schemaname || '.' || relname || '##' || indexrelname || '##'
                 || pg_size_pretty(pg_relation_size(i.indexrelid))::text
                 || '##' || idx_scan::text
    ) AS a
    FROM  pg_stat_user_indexes ui
    JOIN  pg_index i
    ON    ui.indexrelid = i.indexrelid
    WHERE NOT indisunique
    AND   idx_scan < 50
    AND   pg_relation_size(relid) > 5 * 8192
    AND   (schemaname || '.' || relname)::regclass = '%s'::regclass
    ORDER BY
      pg_relation_size(i.indexrelid) / NULLIF(idx_scan, 0) DESC nulls first,
      pg_relation_size(i.indexrelid) DESC
  ) sub
$cmd$);
```

Örnek çıktı:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                            result                                     │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │                                                                       │
│  102009 │ t       │ {"public.my_distributed_table_102009##some_index_102009##28 MB##0"}   │
│  102010 │ t       │                                                                       │
│  102011 │ t       │                                                                       │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="monitoring-client-connection-count"></a>İstemci bağlantı sayısını izleme

Aşağıdaki sorgu, düzenleyicinin üzerinde açık olan bağlantıları sayar ve bunları türlerine göre gruplandırır.

``` sql
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;
```

Örnek çıktı:

```
┌────────┬───────┐
│ state  │ count │
├────────┼───────┤
│ active │     3 │
│ idle   │     3 │
│ ∅      │     6 │
└────────┴───────┘
```

## <a name="index-hit-rate"></a>Dizin isabet oranı

Bu sorgu size tüm düğümlerde Dizin isabet oranını sağlar. Dizin isabet oranı, sorgulanırken dizinlerin ne sıklıkta kullanıldığını belirlemek için faydalıdır:

``` postgresql
SELECT nodename, result as index_hit_rate
FROM run_command_on_workers($cmd$
  SELECT CASE sum(idx_blks_hit)
    WHEN 0 THEN 'NaN'::numeric
    ELSE to_char((sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit + idx_blks_read), '99.99')::numeric
    END AS ratio
  FROM pg_statio_user_indexes
$cmd$);
```

Örnek çıktı:

```
┌───────────┬────────────────┐
│ nodename  │ index_hit_rate │
├───────────┼────────────────┤
│ 10.0.0.16 │ 0.88           │
│ 10.0.0.20 │ 0.89           │
└───────────┴────────────────┘
```

## <a name="cache-hit-rate"></a>İsabetli önbellek okuması oranı

Çoğu uygulama genellikle Toplam verilerinin küçük bir kesirine aynı anda erişir. PostgreSQL, diskten yavaş okumalarını önlemek için sık erişilen verileri bellekte tutar. [Pg_statio_user_tables](https://www.postgresql.org/docs/current/monitoring-stats.html#MONITORING-PG-STATIO-ALL-TABLES-VIEW) görünümünde onunla ilgili istatistikleri görebilirsiniz.

Önemli bir ölçüm, veri yüzdesinin, iş yükünüzün disk ile bellek önbelleğinden geldiği bir şeydir:

``` postgresql
SELECT
  sum(heap_blks_read) AS heap_read,
  sum(heap_blks_hit)  AS heap_hit,
  sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS ratio
FROM
  pg_statio_user_tables;
```

Örnek çıktı:

```
 heap_read | heap_hit |         ratio
-----------+----------+------------------------
         1 |      132 | 0.99248120300751879699
```

Kendinizi %99 ' den büyük ölçüde daha düşük bir oranda fark ediyorsanız, büyük olasılıkla önbelleğin veritabanınızın kullanımına açık olmasını düşünmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Tanılama için yararlı olan diğer [sistem tabloları](reference-hyperscale-metadata.md) hakkında bilgi edinin

---
title: Sistem tabloları – Hyperscale (Citus)-PostgreSQL için Azure veritabanı
description: Dağıtılmış sorgu yürütme meta verileri
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652866"
---
# <a name="system-tables-and-views"></a>Sistem tabloları ve görünümleri

Hiper ölçek (Citus), sunucu grubundaki dağıtılmış veriler hakkında bilgi içeren özel tablolar oluşturur ve korur. Düzenleyici düğümü, çalışan düğümleri genelinde sorguların nasıl çalıştırılacağını planlarken bu tabloları çalıştırır.

## <a name="coordinator-metadata"></a>Düzenleyici meta verileri

Hiper ölçek (Citus), dağıtılan her tabloyu dağıtım sütununa göre birden çok mantıksal parçalara böler. Daha sonra düzenleyici, istatistikleri ve bu parçaların sistem durumu ve konumuyla ilgili bilgileri izlemek için meta veri tablolarını korur.

Bu bölümde, bu meta veri tablolarının ve bunların şemasının her birini açıklıyoruz.
Bu tabloları, düzenleyici düğümünde oturum açtıktan sonra SQL kullanarak görüntüleyebilir ve sorgulayabilirsiniz.

> [!NOTE]
>
> Citus altyapısının eski sürümlerini çalıştıran hyperscale (Citus) sunucu grupları aşağıda listelenen tüm tabloları sunmayabilir.

### <a name="partition-table"></a>Bölüm tablosu

PG \_ Dist \_ bölüm tablosu, veritabanındaki tabloların dağıtıldığı meta verileri depolar. Dağıtılmış her tablo için dağıtım yöntemiyle ilgili bilgileri ve dağıtım sütunuyla ilgili ayrıntılı bilgileri de depolar.

| Ad         | Tür     | Description                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | Bu satırın karşılık geldiği dağıtılmış tablo. Bu değer, pg_class Sistem kataloğu tablosundaki relfilenode sütununa başvurur.                                                                                                                   |
| partmethod   | char     | Bölümlendirme/dağıtım için kullanılan yöntem. Bu sütunun farklı dağıtım yöntemlerine karşılık gelen değerleri ekleme: ' a ', karma: ' h ', başvuru tablosu: ' n '                                                                          |
| partkey      | metin     | Dağıtım sütunuyla ilgili sütun numarası, türü ve diğer ilgili bilgileri içeren ayrıntılı bilgiler.                                                                                                                                      |
| birlikte bulunan cationıd | tamsayı  | Bu tablonun ait olduğu birlikte bulundurma grubu. Aynı gruptaki tablolar, diğer iyileştirmeler arasında birlikte bulunan birleştirmeler ve dağıtılmış toplamalar sağlar. Bu değer pg_dist_colocation tablosundaki birlikte bulundurma kimliği sütununa başvurur.                      |
| repmodel     | char     | Veri çoğaltma için kullanılan yöntem. Bu sütunun farklı çoğaltma yöntemlerine karşılık gelen değerleri şunlardır: Citus deyimli tabanlı çoğaltma: ' c ', PostgreSQL akış çoğaltma: ', iki aşamalı tamamlama (başvuru tabloları için): 't ' |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Parça tablosu

PG \_ Dist \_ parça tablosu, bir tablonun bireysel parçaları hakkındaki meta verileri depolar. Pg_dist_shard, hangi dağıtılmış tablo parçaları ve parçalara ait dağıtım sütunuyla ilgili istatistikler hakkında bilgiler içerir.
Bu istatistikler, ekleme dağıtılan tablolar için dağıtım sütununun Min/Max değerlerine karşılık gelir. Karma Dağıtılmış tablolar için, bu parçaya atanan karma belirteç aralıkları vardır. Bu istatistikler, SELECT sorguları sırasında ilişkisiz parçaları ayıklamak için kullanılır.

| Ad          | Tür     | Description                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | Bu satırın karşılık geldiği dağıtılmış tablo. Bu değer, pg_class Sistem kataloğu tablosundaki relfilenode sütununa başvurur.                                                          |
| parça       | bigint   | Bu parçaya atanan genel benzersiz tanımlayıcı.                                                                                                                                           |
| shardstorage  | char     | Bu parça için kullanılan depolama türü. Farklı depolama türleri aşağıdaki tabloda ele alınmıştır.                                                                                               |
| shardminvalue | metin     | Dağıtılmış tablo ekleme için, bu parçadaki (dahil) dağıtım sütununun en küçük değeri. Karma Dağıtılmış tablolar için, bu parçaya atanan minimum karma belirteç değeri (dahil). |
| shardmaxvalue | metin     | Dağıtılmış tablo ekleme için, bu parçadaki (dahil) dağıtım sütununun maksimum değeri. Karma Dağıtılmış tablolar için, bu parçaya atanan maksimum karma belirteç değeri (dahil). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Parça depolama türleri

PG ldakıard içindeki shardstorage sütunu, parça \_ \_ için kullanılan depolama türünü gösterir. Farklı parça depolama türlerine ve temsiline ilişkin kısa bir genel bakış aşağıda verilmiştir.

| Depolama Türü | Shardstorage değeri | Description                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABLOSUNDAN        | görüntülenemeyen                | Parçanın, düzenli olarak dağıtılan bir tabloya ait verileri depoladığı anlamına gelir.         |
| SÜTUNLU     | Değiştir                | Parçanın sütunlu verileri depolayıp depoladığını gösterir. (Dağıtılmış cstore_fdw tabloları tarafından kullanılır) |
| YABANCI      | Oynat                | Parçanın yabancı verileri depolayıp depoladığını gösterir. (Dağıtılmış file_fdw tabloları tarafından kullanılır)    |

### <a name="shard-placement-table"></a>Parça yerleştirme tablosu

PG \_ Dist \_ yerleştirme tablosu, çalışan düğümlerinde parça çoğaltmalarının konumunu izler. Belirli bir düğüme atanan bir parça kopyasının her çoğaltması parça yerleşimi olarak adlandırılır. Bu tablo, her parça yerleşiminin sistem durumu ve konumuyla ilgili bilgileri depolar.

| Ad        | Tür   | Description                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| parça     | bigint | Bu yerleştirme ile ilişkili parça tanımlayıcısı. Bu değer, pg_dist_shard Catalog tablosundaki parçalama sütununa başvurur.             |
| kıardstate  | int    | Bu yerleştirme durumunu açıklar. Aşağıdaki bölümde farklı parça durumları ele alınmıştır.                                         |
| kıarduzunluğu | bigint | Dağıtılmış tablo ekleme için, parça olarak çalışan düğümünde parça yerleştirme boyutu. Karma Dağıtılmış tablolar için sıfır.            |
| placementId | bigint | Her tek yerleşim için benzersiz bir otomatik oluşturulan tanımlayıcı.                                                                           |
| ID     | int    | Akış çoğaltma modeli kullanıldığında bir birincil sunucu grubunu ve sıfır veya daha fazla ikincil sunucu olduğunu gösterir. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Parça yerleştirme durumları

Hiper ölçek (Citus), parça sistem durumunu yerleştirme temelinde yönetir. Bir yerleştirme sistemi tutarsız bir duruma geçirir, Citus otomatik olarak kullanılamaz olarak işaretler. Yerleştirme durumu, pg_dist_shard_placement tablosuna, shardstate sütununun içinde kaydedilir. Aşağıda, farklı parça yerleştirme durumlarına ilişkin kısa bir genel bakış verilmiştir:

| Durum adı | Shardstate değeri | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| KARAR  | 1                | Yeni parçaların oluşturulduğu durum içinde oluşturulur. Bu durumdaki parça yerleşimi güncel kabul edilir ve sorgu planlama ve yürütme işlemlerinde kullanılır.                                                                                                                                                                                                                                                                                 |
| OLMADAN   | 3                | Bu durumdaki parça yerleşimi, aynı parçanın diğer çoğaltmalarıyla eşitlenmeyeceği için etkin değil olarak kabul edilir. Bu yerleştirme için bir Append, değiştirme (INSERT, UPDATE, DELETE) veya DDL işlemi başarısız olduğunda durum oluşabilir. Sorgu planlayıcısı, planlama ve yürütme sırasında bu durumda yer alacak yerleşimi yoksayacak. Kullanıcılar bu parçadaki verileri, bir arka plan etkinliği olarak son bir çoğaltmayla eşitleyebilir. |
| TO_DELETE  | 4                | Citus bir parça yerleştirmesini master_apply_delete_command çağrısına yanıt verecek şekilde bırakmaya çalışırsa, yerleştirme bu duruma taşınır. Kullanıcılar daha sonra bu parçaları sonraki bir arka plan etkinliği olarak silebilir.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Çalışan düğümü tablosu

PG \_ Dist \_ düğüm tablosu, kümedeki çalışan düğümleri hakkında bilgiler içerir.

| Ad             | Tür    | Description                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NodeId           | int     | Tek bir düğüm için otomatik olarak tanımlayıcı tanımlayıcı.                                                                                                                                          |
| ID          | int     | Akış çoğaltma modeli kullanıldığında bir birincil sunucu grubunu ve sıfır veya daha fazla ikincil sunucuyu belirtmek için kullanılan tanımlayıcı. Varsayılan olarak NodeId ile aynıdır.         |
| nodename         | metin    | PostgreSQL çalışan düğümünün ana bilgisayar adı veya IP adresi.                                                                                                                                     |
| NodePort         | int     | PostgreSQL çalışan düğümünün dinlediği bağlantı noktası numarası.                                                                                                                              |
| noderack         | metin    | Seçim Çalışan düğümü için raf yerleştirme bilgileri.                                                                                                                                 |
| HasMetadata      | boolean | Dahili kullanım için ayrılmıştır.                                                                                                                                                                 |
| IsActive         | boolean | Düğüm, parça yerleştirmelerinin kabul edilmesi için etkin olup olmadığı.                                                                                                                                     |
| noderole         | metin    | Düğümün birincil veya ikincil olduğunu belirtir                                                                                                                                                 |
| nodecluster      | metin    | Bu düğümü içeren kümenin adı                                                                                                                                               |
| shouldhaveshards | boolean | Yanlış ise, parçalar yeniden dengeleme sırasında düğüm dışına taşınır (drenaj), ya da parçalarla birlikte yerleştirilmedikleri takdirde yeni dağıtılmış tablolardaki parçalar düğüm üzerinde yer alır. |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Dağıtılmış nesne tablosu

Citus.pg \_ Dist \_ nesne tablosu, düzenleyici düğümünde oluşturulmuş ve çalışan düğümlerine yayılan türler ve işlevler gibi nesnelerin bir listesini içerir. Bir yönetici kümeye yeni çalışan düğümleri eklediğinde, hiper ölçek (Citus) otomatik olarak dağıtılmış nesnelerin kopyalarını yeni düğümlerde oluşturur (nesne bağımlılıklarını karşılamak için doğru sırada).

| Ad                        | Tür    | Description                                          |
|-----------------------------|---------|------------------------------------------------------|
| ClassID                     | id     | Dağıtılmış nesnenin sınıfı                      |
| ObjId                       | id     | Dağıtılmış nesnenin nesne KIMLIĞI                  |
| objsubıd                    | tamsayı | Dağıtılmış nesnenin nesne alt KIMLIĞI, örneğin, attnum |
| tür                        | metin    | PG yükseltmeleri sırasında kullanılan kararlı adresin bir parçası   |
| object_names                | metin []  | PG yükseltmeleri sırasında kullanılan kararlı adresin bir parçası   |
| object_args                 | metin []  | PG yükseltmeleri sırasında kullanılan kararlı adresin bir parçası   |
| distribution_argument_index | tamsayı | Yalnızca dağıtılmış işlevler/yordamlar için geçerlidir      |
| birlikte bulunan cationıd                | tamsayı | Yalnızca dağıtılmış işlevler/yordamlar için geçerlidir      |

\"Kararlı adresler \" nesneleri belirli bir sunucudan bağımsız olarak benzersiz şekilde tanımlar. Hyperscale (Citus), sygresql yükseltmesi sırasında, [pg using \_ \_ nesnesi \_ \_ Address ()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) işlevi ile oluşturulan kararlı adresler kullanarak nesneleri izler.

Buraya \' girdileri tabloya ekleme hakkında bir örnek aşağıdadır `create_distributed_function()` `citus.pg_dist_object` :

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Birlikte bulundurma Grubu tablosu

PG \_ Dist \_ birlikte bulundurma tablosu, parçaların hangi tabloları \' birlikte yerleştirilebileceğini veya birlikte yerleşebileceği hakkında [](concepts-hyperscale-colocation.md)bilgiler içerir.
Aynı parçalı grup içinde iki tablo olduğunda, hiper ölçek (Citus) aynı bölüm değerlerine sahip parçaların aynı çalışan düğümlerine yerleştirilmelerini sağlar.
Birlikte bulundurma, JOIN iyileştirmeleri, belirli dağıtılmış toplamalar ve yabancı anahtar desteği sunar. Parça sayıları, çoğaltma faktörleri ve bölüm sütun türleri iki tablo arasında eşleşme olduğunda parça birlikte bulundurma algılanır; Ancak, isterseniz, dağıtılmış tablo oluşturulurken özel bir birlikte bulundurma grubu belirtilebilir.

| Ad                   | Tür | Description                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| birlikte bulunan cationıd           | int  | Bu satırın karşılık geldiği birlikte bulundurma grubu için benzersiz tanımlayıcı.          |
| shardcount             | int  | Bu birlikte bulundurma grubundaki tüm tablolar için parça sayısı                          |
| replicationfactor      | int  | Bu birlikte bulundurma grubundaki tüm tablolar için çoğaltma faktörü.                  |
| distributioncolumntype | id  | Bu birlikte bulundurma grubundaki tüm tablolar için dağıtım sütununun türü. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Redengeleyici strateji tablosu

Bu tablo, [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) parçaların nereye taşınacağını belirlemede kullanabileceği stratejileri tanımlar.

| Ad                           | Tür    | Description                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Rebalance_table_shards varsayılan olarak bu stratejiyi seçip seçmemelidir. Bu sütunu güncelleştirmek için citus_set_default_rebalance_strategy kullanın             |
| shard_cost_function            | regproc | Büyük/büyük tamsayı olarak bir parça olması gereken bir maliyet işlevi için tanımlayıcı, gerçek tür olarak bir maliyet kavramı döndürmelidir                                |
| node_capacity_function         | regproc | Bir kapasite işlevinin tanımlayıcı olarak bir NodeId alıp, düğüm kapasitesini gerçek zamanlı olarak döndürmesi gereken tanımlayıcı                          |
| shard_allowed_on_node_function | regproc | Parçalara ayrılmış büyük/veya nodeidarg int 'e ait tanımlayıcı, Citus 'in düğüm üzerinde parça depolayıp depolayamayacağı için Boole değeri döndürür |
| default_threshold              | float4  | Bir düğümün çok dolu veya çok boş olması için eşik, rebalance_table_shards parçalara parçalar taşımaya ne zaman çalışacağını belirler                    |
| minimum_threshold              | float4  | Rebalance_table_shards () öğesinin eşik bağımsız değişkeninin çok düşük bir şekilde ayarlanmasını önlemek için bir koruma                                                  |

Bir Hyperscale (Citus) yüklemesi, tablodaki bu stratejilerle birlikte gelir:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

Varsayılan strateji, `by_shard_count` her parçayı aynı maliyetle atar. Etkisi, düğümler arasında parça sayısını eşleyebilme. Diğer önceden tanımlı strateji, `by_disk_size` disk boyutuyla eşleşen her parçaya bir maliyet atar ve bu, onunla birlikte bulunan parçaları bayt cinsinden verir. Disk boyutu kullanılarak hesaplanır `pg_total_relation_size` , bu nedenle dizinler bulunur. Bu strateji, her düğümde aynı disk alanını elde etme girişiminde bulunur. 0,1 eşiğini aklınızda bırakın; disk alanındaki önemli farklılıklardan kaynaklanan gereksiz parça hareketini engeller.

#### <a name="creating-custom-rebalancer-strategies"></a>Özel redengeleyici stratejileri oluşturma

İşte, yeni parça yeniden dengeleyici stratejileri içinde kullanılabilecek ve [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) işleviyle [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) kayıtlı işlevlere örnekler aşağıda verilmiştir.

-   Ana bilgisayar adı düzenine göre düğüm kapasitesi özel durumu ayarlanıyor:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   [Citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)ile ölçülen bir parçaya gidecek sorgu sayısına göre yeniden dengeleyin:

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Bir düğümde (Adres 10.0.0.1) belirli bir parçayı yalıtma (10000) \' \' :

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Sorgu istatistikleri tablosu

Hiper ölçek (Citus) `citus_stat_statements` , sorguların nasıl yürütüldüğü ve kim için çalıştığı hakkında istatistikler sağlar. Bu \' , (ve ile birleştirilebilir), PostgreSQL içindeki [pg \_ stat \_ deyimleri](https://www.postgresql.org/docs/current/static/pgstatstatements.html) görünümü, sorgu hızı hakkında istatistikleri izler.

Bu görünüm, kiracı yalıtımının ne zaman karar vermeyeceğine yardımcı olan çok kiracılı bir uygulamadaki sorguları kaynak kiracılarına izleyebilir.

| Ad          | Tür   | Description                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| QueryId       | bigint | tanımlayıcı (pg_stat_statements birleştirmeler için iyi)                                   |
| UserID        | id    | sorguyu çalıştıran Kullanıcı                                                           |
| DBID          | id    | düzenleyicinin veritabanı örneği                                                 |
| sorgu         | metin   | anonimleştirilmiş sorgu dizesi                                                          |
| Yürütücü      | metin   | Citus yürütücü kullanıldı: Uyarlamalı, gerçek zamanlı, görev-izleyici, yönlendirici veya INSERT-SELECT |
| partition_key | metin   | yönlendirici tarafından yürütülen sorguların dağıtım sütununun değeri, değilse NULL               |
| aramalarda         | bigint | sorgunun kaç kez çalıştırıldığı                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Sonuçlar:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Uyarılar

-   İstatistik verileri çoğaltılmaz ve \' veritabanı kilitlenmelerini veya yük devretmeyi kazandı
-   Guc tarafından ayarlanan sınırlı sayıda sorguyu izler `pg_stat_statements.max` (varsayılan 5000)
-   Tabloyu kesmek için, `citus_stat_statements_reset()` işlevini kullanın

### <a name="distributed-query-activity"></a>Dağıtılmış sorgu etkinliği

Hiper ölçek (Citus), Dağıtılmış sorguların sonuçlarını oluşturmak için dahili olarak kullanılan ve yerleşik sorgular dahil olmak üzere küme genelinde sorguları ve kilitleri izlemek için özel görünümler sağlar.

-   **citus \_ Dist \_ stat \_ etkinliği**: tüm düğümlerde yürütülen dağıtılmış sorguları gösterir. , İkincisini nerede olursa olsun, öğesinin bir üst kümesi `pg_stat_activity` .
-   **citus \_ Worker \_ stat \_ etkinliği**: ayrı parçalara yönelik parça sorguları dahil olmak üzere çalışanlar üzerindeki sorguları gösterir.
-   **citus \_ lock \_ wait**: küme genelinde engellenen sorgular.

İlk iki görünüm, [pg \_ stat \_ etkinliğinin](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) tüm sütunlarını ve sorguyu Başlatan çalışan konak/bağlantı noktasını ve kümenin düzenleyici düğümünün ana bilgisayar/bağlantı noktasını içerir.

Örneğin, dağıtılmış bir tablodaki satırları saymayı göz önünde bulundurun:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

Sorgunun şu şekilde göründüğünü görebiliriz `citus_dist_stat_activity` :

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

Bu sorgu tüm parçalardan bilgi gerektirir. Bazı bilgiler parça içinde `users_table_102038` depolanmaktadır `localhost:9700` . Görünüme bakarak parçalara erişen bir sorgu görebiliriz `citus_worker_stat_activity` :

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

`query`Alan, sayımda dışarı aktarılmakta olan verileri gösterir.

> [!NOTE]
> Bir yönlendirici sorgusu (ör. çok kiracılı bir uygulamadaki tek kiracı), ' Seç
> * Tenant_id = X ') bir işlem bloğu olmadan yürütüldüğü tablodan, \_ \_ \_ citus Worker stat etkinliğinde ana sorgu ana bilgisayar adı ve ana \_ sorgu \_ ana bilgisayar \_ bağlantı noktası sütunları null olur \_ \_ \_ .

Nasıl çalıştığını görmek için `citus_lock_waits` el ile kilitleme durumu üretebiliriz. İlk olarak \' düzenleyiciden bir sınama tablosu ayarladık:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Daha sonra, düzenleyici üzerinde iki oturum kullanarak bu deyim dizisini çalıştırabiliriz:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

`citus_lock_waits`Görünüm durumu gösterir.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

Bu örnekte, sorgular düzenleyiciden kaynaklardır, ancak görünüm aynı zamanda çalışanlar kaynaklı sorgular arasındaki kilitleri da listeleyebilir (örnek için Hiperscale (Citus) MX ile yürütülür).

## <a name="next-steps"></a>Sonraki adımlar

* Bazı [Hyperscale (Citus) işlevlerinin](reference-hyperscale-functions.md) sistem tablolarını değiştirme şeklini öğrenin
* [Düğümlerin ve tabloların](concepts-hyperscale-nodes.md) kavramlarını gözden geçirme

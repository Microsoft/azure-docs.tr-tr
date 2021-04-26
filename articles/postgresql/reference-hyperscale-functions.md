---
title: SQL işlevleri – Hyperscale (Citus)-PostgreSQL için Azure veritabanı
description: Hyperscale (Citus) SQL API 'sindeki işlevler
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 04/07/2021
ms.openlocfilehash: b0aa9d5dec25d8d600ecbcde59a57e67917c6411
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011161"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Hyperscale (Citus) SQL API 'sindeki işlevler

Bu bölüm, Hyperscale (Citus) tarafından sunulan Kullanıcı tanımlı işlevlere ilişkin başvuru bilgilerini içerir. Bu işlevler, Hyperscale 'e (Citus) dağıtılmış işlevsellik sağlamaya yardımcı olur.

> [!NOTE]
>
> Citus altyapısının eski sürümlerini çalıştıran hyperscale (Citus) sunucu grupları aşağıda listelenen tüm işlevleri sunmayabilir.

## <a name="table-and-shard-ddl"></a>Tablo ve parça DDL

### <a name="create_distributed_table"></a>\_Dağıtılmış \_ tablo oluştur

Dağıtılmış tablo \_ oluşturma \_ () işlevi, karma olarak dağıtılan bir tablo ise, dağıtılmış bir tabloyu tanımlamak ve parçaları oluşturmak için kullanılır. Bu işlev bir tablo adı, dağıtım sütunu ve isteğe bağlı bir dağıtım yöntemi alır ve tabloyu dağıtılmış olarak işaretlemek için uygun meta verileri ekler. Hiçbir dağıtım yöntemi belirtilmemişse, işlev varsayılan olarak ' hash ' dağılımını alır. Tablo karma dağıtılırsa, işlev parça sayısı ve parça çoğaltma faktörü yapılandırma değerlerini temel alarak çalışan parçaları da oluşturur. Tablo herhangi bir satır içeriyorsa, bunlar otomatik olarak çalışan düğümlerine dağıtılır.

Bu işlev, ana \_ \_ Dağıtılmış \_ tablo oluşturma () ve ardından ana \_ \_ çalışan parça oluşturma \_ () kullanımını değiştirir.

#### <a name="arguments"></a>Bağımsız değişkenler

**tablo \_ adı:** dağıtılması gereken tablonun adı.

**dağıtım \_ sütunu:** tablonun dağıtılacağı sütun.

**dağıtım \_ türü:** (isteğe bağlı) tabloya göre dağıtılacak yöntem. İzin verilen değerler, varsayılan değeri ' hash ' olan ekleme veya karma değerlerdir.

**birlikte bulundurma \_ :** (isteğe bağlı) başka bir tablonun ekleme grubundaki geçerli tabloyu içerir. Varsayılan olarak tablolar aynı türde sütunlar tarafından dağıtıldığında, aynı parça sayısına sahip ve aynı çoğaltma faktörüne sahip olduğunda birlikte bulunur. İçin olası değerler `colocate_with` `default` , `none` Yeni bir birlikte bulundurma grubu başlatmak veya bu tabloyla birlikte kullanılacak başka bir tablonun adı.  (Bkz. [tablo](concepts-hyperscale-colocation.md)bulundurma.)

Varsayılan değerinin örtük bir şekilde birlikte tutulup tutulmayacağını unutmayın `colocate_with` . Tablolar ilişkili olduğunda veya katılacaksa, birlikte bulundurma harika bir [şey olabilir.](concepts-hyperscale-colocation.md)  Ancak, iki tablo ilgisiz olduğunda, ancak dağıtım sütunları için aynı veri türünü kullanması durumunda, verileri yanlışlıkla eklemek parça [yeniden dengeleme](howto-hyperscale-scale-rebalance.md)sırasında performansı düşürebilir.  Tablo parçaları, Cascade içinde gereksiz bir şekilde hareket eder \" .\"

Yeni bir dağıtılmış tablo diğer tablolarla ilgili değilse, bunu belirtmek en iyisidir `colocate_with => 'none'` .

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

Bu örnekte, \_ Depo kimliği sütununda GitHub olayları tablosunun karma tarafından dağıtılması gerektiğini veritabanına bildirir \_ .

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>\_başvuru \_ tablosu oluştur

\_Başvuru tablosu oluştur \_ () işlevi, küçük bir başvuru veya boyut tablosu tanımlamak için kullanılır. Bu işlev bir tablo adı alır ve her çalışan düğümüne çoğaltılan tek bir parça ile dağıtılmış bir tablo oluşturur.

#### <a name="arguments"></a>Bağımsız değişkenler

**tablo \_ adı:** dağıtılması gereken küçük boyutun veya başvuru tablosunun adı.

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

Bu örnek, veritabanını, ulus tablosunun bir başvuru tablosu olarak tanımlanması gerektiğini bildirir

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>\_ \_ başvuru \_ tablosuna yükselt

\_ \_ Başvuru \_ tablosuna yükselt () işlevi, parça sayısı bir olan mevcut bir dağıtılmış tabloyu alır ve bunu tanınan bir başvuru tablosu olarak yükseltir. Bu işlev çağrıldıktan sonra tablo, [create_reference_table](#create_reference_table)oluşturulmuş gibi olacaktır.

#### <a name="arguments"></a>Bağımsız değişkenler

**tablo \_ adı:** bir başvuru tablosu olarak dağıtılacak olan dağıtılmış tablonun adı (parça sayısı = 1).

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

Bu örnek, veritabanını, ulus tablosunun bir başvuru tablosu olarak tanımlanması gerektiğini bildirir

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>tabloları birlikte işaretle \_ \_

İşaretleme \_ tabloları \_ birlikte bulunan () işlevi, dağıtılmış bir tablo (kaynak) ve diğerlerinin bir listesini (hedefler) alır ve hedefleri kaynakla aynı birlikte bulundurma grubuna koyar. Kaynak henüz bir grupta değilse, bu işlev bir oluşturur ve kaynak ve hedefleri buna atar.

Tablo dağıtım zamanında `colocate_with` , [create_distributed_table](#create_distributed_table)parametresi aracılığıyla tabloları birlikte `mark_tables_colocated` bulundurmamak, ancak gerekirse daha sonra bu işlemi gerçekleştirebilir.

#### <a name="arguments"></a>Bağımsız değişkenler

**kaynak \_ tablo \_ adı:** birlikte bulundurma grubunu hedefe atanacak şekilde, dağıtılmış tablonun adı.

**hedef \_ tablo \_ adları:** dağıtılmış hedef tabloların ad dizisi boş olmamalıdır. Bu dağıtılmış tabloların içindeki kaynak tabloyla eşleşmesi gerekir:

> -   dağıtım yöntemi
> -   Dağıtım sütunu türü
> -   çoğaltma türü
> -   parça sayısı

Yukarıdakilerden hiçbiri uygulanmadığından, Hyperscale (Citus) bir hata oluşturacak. Örneğin, tabloları birlikte bulundurma `apples` ve `oranges` dağıtım sütun türleri farklı sonuçlar:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

Bu örnek `products` , ile `line_items` aynı birlikte bulundurma grubuna ve yerleştirir `stores` . Örnek, bu tabloların tümünün, büyük olasılıkla bir depo kimliği olan eşleşen türe sahip bir sütuna dağıtıldığı varsayılır \" .\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>\_Dağıtılmış \_ işlev oluştur

Bir işlevi düzenleyici düğümünden çalışanlara yayar ve dağıtılmış yürütme için işaretler. Düzenleyici üzerinde bir dağıtılmış işlev çağrıldığında, hiper ölçek (Citus) \" \" işlevi çalıştırmak için bir çalışan düğümü seçmek üzere dağıtım bağımsız değişkeninin değerini kullanır. Çalışan işlevin yürütülmesi paralellik artırır ve daha düşük gecikme süresi için kodu parçalara parçalar halinde verileri daha yakınına getirebilir.

Postgres arama yolu, dağıtılmış işlev yürütmesi sırasında düzenleyiciyle çalışanlara yayılmaz, bu nedenle dağıtılmış işlev kodu, veritabanı nesnelerinin adlarını tamamen nitelemelidir. Ayrıca, işlevler tarafından yayılan bildirimler kullanıcıya gösterilmez.

#### <a name="arguments"></a>Bağımsız değişkenler

**işlev \_ adı:** dağıtılacak işlevin adı. Birden çok işlev PostgreSQL içinde aynı ada sahip olabileceğinden, adın parantez içindeki parametre türleri içermesi gerekir. Örneğin, `'foo(int)'` öğesinden farklıdır `'foo(int, text)'` .

**dağıtım \_ bağımsız değişkeni \_ adı:** (isteğe bağlı) dağıtılacak bağımsız değişken adı. Kolaylık sağlaması için (veya işlev bağımsız değişkenlerinin adları yoksa), gibi bir Konumsal yer tutucuya izin verilir `'$1'` . Bu parametre belirtilmemişse, tarafından adlı işlev `function_name` yalnızca çalışanlar üzerinde oluşturulur. Gelecekte çalışan düğümleri eklenirse, işlev otomatik olarak bu şekilde oluşturulur.

**\_ ile birlikte bulundurma:** (isteğe bağlı) dağıtılmış işlev dağıtılmış bir tabloya okur veya yazar (ya da daha genel, birlikte bulundurma grubu), bu tabloyu parametresini kullanarak girdiğinizden emin olun `colocate_with` . Ardından, işlevin her çağrılması ilgili parçaları içeren çalışan düğümünde çalışır.

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

### <a name="alter_columnar_table_set"></a>alter_columnar_table_set

Alter_columnar_table_set () işlevi [sütunlu tablodaki](concepts-hyperscale-columnar.md)ayarları değiştirir. Bu işlevi sütunlu olmayan bir tabloda çağırmak bir hata verir. Tablo adı hariç tüm bağımsız değişkenler isteğe bağlıdır.

Tüm sütunlu tablolar için geçerli seçenekleri görüntülemek üzere şu tabloya başvurun:

```postgresql
SELECT * FROM columnar.options;
```

Yeni oluşturulan tablolar için sütunlu ayarların varsayılan değerleri bu GUCs ile geçersiz kılınabilir:

* sütunlu. sıkıştırma
* columnar.compression_level
* columnar.stripe_row_count
* columnar.chunk_row_count

#### <a name="arguments"></a>Bağımsız değişkenler

**table_name:** Sütunlu tablonun adı.

**chunk_row_count:** (isteğe bağlı) yeni Ekli veriler için öbek başına en fazla satır sayısı. Var olan veri öbekleri değiştirilmeyecektir ve bu en büyük değerden daha fazla satıra sahip olabilir. Varsayılan değer 10000 ' dir.

**stripe_row_count:** (isteğe bağlı) yeni girilen veriler için her Stripe için en fazla satır sayısı. Varolan veri şeritleri değiştirilmeyecektir ve bu en büyük değerden daha fazla satıra sahip olabilir. Varsayılan değer 150000 ' dir.

**sıkıştırma:** (isteğe bağlı) `[none|pglz|zstd|lz4|lz4hc]` Yeni Ekli veriler için sıkıştırma türü. Mevcut veriler yeniden sıkıştırılmaz veya sıkıştırması açılır. Varsayılan ve önerilen değer zstd ' dir (Eğer desteği ' de derlendiyse).

**compression_level:** (isteğe bağlı) geçerli ayarlar 1 ile 19 arasında geçerlidir. Sıkıştırma yöntemi seçilen düzeyi desteklemiyorsa, bunun yerine en yakın düzey seçilir.

#### <a name="return-value"></a>Döndürülen değer

Yok

#### <a name="example"></a>Örnek

```postgresql
SELECT alter_columnar_table_set(
  'my_columnar_table',
  compression => 'none',
  stripe_row_count => 10000);
```

## <a name="metadata--configuration-information"></a>Meta veri/yapılandırma bilgileri

### <a name="master_get_table_metadata"></a>Ana \_ \_ tablo Al \_ meta verileri

Ana \_ Get \_ Table \_ Metadata () işlevi, dağıtılmış bir tablo için dağıtımla ilgili meta verileri döndürmek üzere kullanılabilir. Bu meta veriler, tabloya ilişkin ilişki KIMLIĞI, depolama türü, dağıtım yöntemi, dağıtım sütunu, çoğaltma sayısı, en büyük parça boyutu ve parça yerleştirme ilkesini içerir. Bu işlev, kapsamadan sonra, gerekli bilgileri almak için hiper ölçek (Citus) meta veri tablolarını sorgular ve onu kullanıcıya döndürmeden önce bir tanımlama grubu içine birleştirir.

#### <a name="arguments"></a>Bağımsız değişkenler

**tablo \_ adı:** meta verileri getirmek istediğiniz dağıtılmış tablonun adı.

#### <a name="return-value"></a>Dönüş Değeri

Aşağıdaki bilgileri içeren bir kayıt düzeni:

**mantıksal \_ relid:** dağıtılmış tablonun OID 'si. PG Class Sistem kataloğu tablosundaki relfilenode sütununa başvurur \_ .

**bölüm \_ depolama \_ türü:** tablo için kullanılan depolamanın türü. 'T ' (Standart tablo), ' f ' (yabancı tablo) veya ' c ' (sütunlu tablo) olabilir.

**bölüm \_ yöntemi:** tablo için kullanılan dağıtım yöntemi. ' A ' (Append) veya ' h ' (karma) olabilir.

**bölüm \_ anahtarı:** tablo için dağıtım sütunu.

**bölüm \_ çoğaltma \_ sayısı: geçerli parça** çoğaltma sayısı.

**bölüm \_ en büyük \_ boyutu:** bayt cinsinden geçerli en büyük parça boyutu.

**bölüm \_ yerleştirme \_ ilkesi: tablonun parçalarını** yerleştirmek için kullanılan parça yerleştirme ilkesi. 1 (yerel-düğüm-ilk) veya 2 (hepsini bir kez deneme) olabilir.

#### <a name="example"></a>Örnek

Aşağıdaki örnek, GitHub olayları tablosu için tablo meta verilerini getirir ve görüntüler \_ .

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>\_ \_ \_ \_ dağıtım sütunu için parça kimliği \_ Al

Hiper ölçek (Citus), dağıtılmış bir tablonun her satırını, satırın dağıtım sütununun değerine ve tablonun dağıtım yöntemine göre bir parçaya atar. Çoğu durumda, kesin eşleme, veritabanı yöneticisinin yoksaydığı alt düzey bir ayrıntıdır. Bununla birlikte, el ile veritabanı bakım görevleri için ya da yalnızca öngörüye karşılamak üzere bir satırın parça belirlenmesi yararlı olabilir. `get_shard_id_for_distribution_column`İşlevi, karma dağıtılan, Aralık ile dağıtılan ve başvuru tabloları için bu bilgileri sağlar. Ekleme dağıtımı için çalışmaz.

#### <a name="arguments"></a>Bağımsız değişkenler

**tablo \_ adı:** dağıtılmış tablo.

**dağıtım \_ değeri:** dağıtım sütununun değeri.

#### <a name="return-value"></a>Dönüş Değeri

Parça KIMLIĞI hiper ölçek (Citus), verilen tablo için dağıtım sütunu değeri ile ilişkilendirir.

#### <a name="example"></a>Örnek

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>sütun \_ \_ adı sütunu \_

`partkey`Sütununu `pg_dist_partition` metin sütunu adına çevirir. Çeviri, dağıtılmış bir tablonun dağıtım sütununu tespit etmek için kullanışlıdır.

Daha ayrıntılı bir tartışma için bkz. [dağıtım sütunu seçme](concepts-hyperscale-choose-distribution-column.md).

#### <a name="arguments"></a>Bağımsız değişkenler

**tablo \_ adı:** dağıtılmış tablo.

**sütun \_ var \_ metni:** `partkey` `pg_dist_partition` tablodaki değeri.

#### <a name="return-value"></a>Dönüş Değeri

`table_name`Dağıtım sütununun adı.

#### <a name="example"></a>Örnek

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Çıkış:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus \_ ilişki \_ boyutu

Belirtilen dağıtılmış tablonun tüm parçaları tarafından kullanılan disk alanını al.
Disk alanı, \" ana çatalın boyutunu içerir, ancak parçalar \" için görünürlük eşlemesini ve boş alan haritasını dışlar.

#### <a name="arguments"></a>Bağımsız değişkenler

**logicalrelid:** dağıtılmış bir tablonun adı.

#### <a name="return-value"></a>Dönüş Değeri

Bayt cinsinden bir bigint olarak boyut.

#### <a name="example"></a>Örnek

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus \_ tablo \_ boyutu

Dizinler hariç olmak üzere, belirtilen dağıtılmış tablonun tüm parçaları tarafından kullanılan disk alanını alır (BILDIRIM, boş alan haritası ve görünürlük Haritası dahil).

#### <a name="arguments"></a>Bağımsız değişkenler

**logicalrelid:** dağıtılmış bir tablonun adı.

#### <a name="return-value"></a>Dönüş Değeri

Bayt cinsinden bir bigint olarak boyut.

#### <a name="example"></a>Örnek

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus \_ Toplam \_ ilişki \_ boyutu

Tüm dizinler ve BILDIRIM verileri dahil olmak üzere, belirtilen dağıtılmış tablonun tüm parçaları tarafından kullanılan toplam disk alanını alır.

#### <a name="arguments"></a>Bağımsız değişkenler

**logicalrelid:** dağıtılmış bir tablonun adı.

#### <a name="return-value"></a>Dönüş Değeri

Bayt cinsinden bir bigint olarak boyut.

#### <a name="example"></a>Örnek

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>citus \_ stat \_ deyimlerini \_ sıfırlama

[Citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)tüm satırları kaldırır.
Bu işlev, öğesinden bağımsız olarak çalışır `pg_stat_statements_reset()` . Tüm istatistikleri sıfırlamak için her iki işlevi de çağırın.

#### <a name="arguments"></a>Bağımsız değişkenler

Yok

#### <a name="return-value"></a>Dönüş Değeri

Yok

## <a name="server-group-management-and-repair"></a>Sunucu grubu yönetimi ve onarımı

### <a name="master_copy_shard_placement"></a>Ana \_ kopya parça \_ \_ yerleşimi

Bir parça yerleşimi, bir değiştirme komutu veya bir DDL işlemi sırasında güncelleştirilemezse, etkin değil olarak işaretlenir. Ana \_ kopya parça \_ \_ yerleştirme işlevi daha sonra, sağlıklı bir yerleştirmede verileri kullanarak etkin olmayan parça yerleşimini onarmak için çağrılabilir.

Bir parçayı onarmak için, işlev önce sağlıksız parça yerleşimini bırakır ve düzenleyiciden şemayı kullanarak yeniden oluşturur. Parça yerleşimi oluşturulduktan sonra, işlev sağlıklı yerleşimden verileri kopyalar ve yeni parça yerleştirmesini sağlıklı olarak işaretlemek için meta verileri güncelleştirir. Bu işlev, onarım sırasında parçanın eşzamanlı değişikliklerden korunmasını sağlar.

#### <a name="arguments"></a>Bağımsız değişkenler

parça **\_ kimliği:** onarılacak parça kimliği.

**kaynak \_ düğümü \_ adı:** sağlıklı parça yerleşiminin bulunduğu düğümün DNS adı ( \" kaynak \" düğüm).

**kaynak \_ düğümü \_ bağlantı noktası:** veritabanı sunucusunun dinlediği kaynak çalışan düğümündeki bağlantı noktası.

**hedef \_ düğüm \_ adı:** geçersiz parça yerleşimi bulunan düğümün DNS adı ( \" hedef \" düğüm).

**hedef \_ düğüm \_ bağlantı noktası:** veritabanı sunucusunun dinlediği hedef çalışan düğümündeki bağlantı noktası.

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

Aşağıdaki örnekte, \_ bağlantı noktası 5432 ' de ' hatalı ana bilgisayar ' üzerinde çalışan veritabanı sunucusunda bulunan parça 12345 ' nin etkin olmayan bir yerleşimini onaracaktır. Bunu onarmak için, \_ bağlantı noktasında ' iyi ana bilgisayar ' üzerinde çalışan sunucuda, sağlıklı bir parça yerleşimden verileri kullanır
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>Ana \_ taşıma parça \_ \_ yerleşimi

Bu işlev, belirli bir parçayı (ve birlikte bulunan parçaları onunla birlikte) bir düğümden diğerine taşılar. Genellikle bir veritabanı yöneticisi tarafından çağrılması yerine, parça yeniden dengeleme sırasında dolaylı olarak kullanılır.

Verileri taşımanın iki yolu vardır: engelleme veya engelleme. Engelleyici yaklaşım, parça üzerinde yapılan tüm değişiklikleri taşıma sırasında duraklatıldığı anlamına gelir.
Parça yazma engellemeyi önleyen ikinci yöntem, Postgres 10 mantıksal çoğaltmasını kullanır.

Başarılı bir taşıma işleminden sonra kaynak düğümdeki parçalar silinir. Taşıma herhangi bir noktada başarısız olursa, bu işlev bir hata oluşturur ve kaynak ve hedef düğümleri değişmeden bırakır.

#### <a name="arguments"></a>Bağımsız değişkenler

parça **\_ kimliği:** taşınacak parça kimliği.

**kaynak \_ düğümü \_ adı:** sağlıklı parça yerleşiminin bulunduğu düğümün DNS adı ( \" kaynak \" düğüm).

**kaynak \_ düğümü \_ bağlantı noktası:** veritabanı sunucusunun dinlediği kaynak çalışan düğümündeki bağlantı noktası.

**hedef \_ düğüm \_ adı:** geçersiz parça yerleşimi bulunan düğümün DNS adı ( \" hedef \" düğüm).

**hedef \_ düğüm \_ bağlantı noktası:** veritabanı sunucusunun dinlediği hedef çalışan düğümündeki bağlantı noktası.

parça **\_ aktarma \_ modu:** (Isteğe bağlı) PostgreSQL mantıksal çoğaltma veya bir çapraz çalışan kopyalama komutu kullanılıp kullanılmayacağını belirtir. Olası değerler şunlardır:

> -   `auto`: Mantıksal çoğaltma mümkünse çoğaltma kimliği gerektir, aksi takdirde eski davranışı kullanın (örneğin, parça onarımı için PostgreSQL 9,6). Varsayılan değer budur.
> -   `force_logical`: Tablonun bir çoğaltma kimliği olmasa bile mantıksal çoğaltma kullanın. Tablodaki tüm eş zamanlı güncelleştirme/silme deyimleri çoğaltma sırasında başarısız olur.
> -   `block_writes`: Birincil anahtar veya çoğaltma kimliği olmayan tablolar için kopyalama (yazmaları engelleme) kullanın.

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>Tablo parçaları yeniden dengeleme \_ \_

Yeniden dengeleme \_ tablo parçaları \_ () işlevi, çalışanlar arasında eşit bir şekilde dağıtılmasını sağlamak için verilen tablonun parçalarını taşımaktadır. İşlev ilk olarak, sunucu grubunun verilen eşik dahilinde dengelenmesi için olması gereken taşımaların listesini hesaplar. Daha sonra, parça yerleştirmelerinin birini kaynak düğümden hedef düğüme taşır ve ilgili parça meta verilerini taşımayı yansıtacak şekilde günceller.

Parçaların \" eşit olarak dağıtılıp dağıtılmadığını belirlerken her parçaya bir ücret atanır. \" Varsayılan olarak, her parça aynı maliyetle (1 değeri) sahiptir, bu nedenle, çalışanların maliyetlerini eşit hale getirmek için dağıtım, her birinde parçalama sayısını equalde aynıdır. Sabit maliyet stratejisi parça \" sayısı tarafından çağrılır \_ \_ \" ve varsayılan yeniden dengeleme stratejisidir.

Varsayılan strateji şu koşullarda uygundur:

*  Parçalar kabaca aynı boyutta
*  Parçalar kabaca aynı miktarda trafik alır
*  Çalışan düğümlerinin hepsi aynı boyutta/türde
*  Parçalar belirli çalışanlara sabitlenmemiş

Bu varsayımlar varsa, varsayılan yeniden dengeleme hatalı bir plana neden olabilir. Bu durumda, parametresini kullanarak stratejiyi özelleştirebilirsiniz `rebalance_strategy` .

[](#get_rebalance_table_shards_plan) \_ \_ Gerçekleştirilecek eylemleri görmek ve doğrulamak için yeniden dengeleme tablo parçaları çalıştırılmadan önce get_rebalance_table_shards_plan çağrısı yapmanız önerilir.

#### <a name="arguments"></a>Bağımsız değişkenler

**tablo \_ adı:** (isteğe bağlı) parçaları yeniden dengelenmesi gereken tablo adı. NULL ise, tüm mevcut birlikte bulundurma gruplarını yeniden dengeleyin.

**eşik:** (isteğe bağlı) ortalama kullanımdan düğüm kullanımının en yüksek fark oranını gösteren 0,0 ile 1,0 arasında bir float numarası. Örneğin, 0,1 belirtildiğinde parça yeniden dengeleyicisi, tüm düğümlerin aynı sayıda parça olan ± 10 ' ı tutacak şekilde dengelemeye çalışmasına neden olur.
Özellikle, parça yeniden dengeleyici, tüm çalışan düğümlerinin kullanımını (1 eşiğin) ortalama kullanıma yakınsamasını dener \* \_ \. . (1
+ Threshold) \* Ortalama \_ kullanım aralığı.

en büyük parça taşıma **\_ \_ :** (isteğe bağlı) taşınacak en fazla parça sayısı.

**Dışlanan parça \_ \_ listesi:** (isteğe bağlı) yeniden dengeleme işlemi sırasında taşınmaması gereken parçalara yönelik tanımlayıcılar.

parça **\_ aktarma \_ modu:** (Isteğe bağlı) PostgreSQL mantıksal çoğaltma veya bir çapraz çalışan kopyalama komutu kullanılıp kullanılmayacağını belirtir. Olası değerler şunlardır:

> -   `auto`: Mantıksal çoğaltma mümkünse çoğaltma kimliği gerektir, aksi takdirde eski davranışı kullanın (örneğin, parça onarımı için PostgreSQL 9,6). Varsayılan değer budur.
> -   `force_logical`: Tablonun bir çoğaltma kimliği olmasa bile mantıksal çoğaltma kullanın. Tablodaki tüm eş zamanlı güncelleştirme/silme deyimleri çoğaltma sırasında başarısız olur.
> -   `block_writes`: Birincil anahtar veya çoğaltma kimliği olmayan tablolar için kopyalama (yazmaları engelleme) kullanın.

**yalnızca drenajı \_ :** (isteğe bağlı) doğru olduğunda, pg_dist_node alt düğümleri devre dışı olarak ayarlamış olan çalışan düğümlerini taşıyın `shouldhaveshards` ; başka parçalar kalmaz. [](reference-hyperscale-metadata.md#worker-node-table)

yeniden **Dengeleme \_ stratejisi:** (isteğe bağlı) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)bir stratejinin adı.
Bu bağımsız değişken atlanırsa, işlev tabloda gösterildiği gibi varsayılan stratejiyi seçer.

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

Aşağıdaki örnekte, \_ varsayılan eşik içindeki GitHub olayları tablosunun parçaları yeniden dengelenmesi denenecek.

```postgresql
SELECT rebalance_table_shards('github_events');
```

Bu örnek kullanım, \_ No. ve 2 ile parçaları taşımadan GitHub olayları tablosunu yeniden dengeetmeye çalışır.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>yeniden \_ Dengeleme \_ tablosu \_ parça \_ planını al

[Rebalance_table_shards](#rebalance_table_shards) planlı parça hareketlerine çıkış yapmadan çıktıyı ayırın.
Büyük olasılıkla, yeniden \_ Dengeleme tablosu parçalama \_ \_ \_ planı \_ \_ , aynı bağımsız değişkenlerle bir yeniden dengeleme tablosu parçalama çağrısının yapabileceğinden biraz farklı bir plana çıkış yapabilir. Aynı anda çalıştırılmazlar, bu nedenle sunucu grubuyla ilgili olgular; \- Örneğin, disk alanı, \- çağrılar arasında farklılık gösterebilir.

#### <a name="arguments"></a>Bağımsız değişkenler

Yeniden dengelenir tablo parçaları ile aynı bağımsız değişkenler \_ \_ : ilişki, eşik, en büyük parça \_ \_ taşımaları, dışlanan parça \_ \_ listesi ve yalnızca boşaltma \_ . Bağımsız değişkenlerin anlamı için ilgili işlevin belgelerine bakın.

#### <a name="return-value"></a>Dönüş Değeri

Şu sütunları içeren diziler:

-   **tablo \_ adı**: parçalar taşınacak olan tablo
-   parçalara ayırma: söz konusu **parça**
-   parça **\_ boyutu**: bayt cinsinden boyut
-   **SourceName**: kaynak düğümün ana bilgisayar adı
-   **sourceport**: kaynak düğümün bağlantı noktası
-   **hedefadı**: hedef düğümün ana bilgisayar adı
-   **TARGETPORT**: hedef düğümün bağlantı noktası

### <a name="get_rebalance_progress"></a>yeniden \_ Dengeleme \_ ilerlemesini al

Parça yeniden dengelemesinin başlaması durumunda, `get_rebalance_progress()` işlev ilgili her parçanın ilerlemesini listeler. Planlı ve tarafından yürütülen taşımaları izler `rebalance_table_shards()` .

#### <a name="arguments"></a>Bağımsız değişkenler

Yok

#### <a name="return-value"></a>Dönüş Değeri

Şu sütunları içeren diziler:

-   **SessionID**: yeniden dengeleme izleyicisinin Postgres PID 'si
-   **tablo \_ adı**: parçalar hareketli olan tablo
-   parçalara ayırma: söz konusu **parça**
-   parça **\_ boyutu**: bayt cinsinden boyut
-   **SourceName**: kaynak düğümün ana bilgisayar adı
-   **sourceport**: kaynak düğümün bağlantı noktası
-   **hedefadı**: hedef düğümün ana bilgisayar adı
-   **TARGETPORT**: hedef düğümün bağlantı noktası
-   **ilerleme**: 0 = taşınması bekleniyor; 1 = taşınıyor; 2 = Tamam

#### <a name="example"></a>Örnek

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus \_ yeniden \_ Dengeleme \_ stratejisi Ekle

[Pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) bir satır ekleyin.

#### <a name="arguments"></a>Bağımsız değişkenler

Bu bağımsız değişkenler hakkında daha fazla bilgi için içindeki karşılık gelen sütun değerlerine bakın `pg_dist_rebalance_strategy` .

**ad:** yeni strateji için tanımlayıcı

parça **\_ maliyeti \_ işlevi:** \" \" her parçanın maliyetini belirlemede kullanılan işlevi tanımlar

**düğüm \_ kapasitesi \_ işlevi:** düğüm kapasitesini ölçmek için işlevi tanımlar

**\_ \_ \_ düğüm \_ işlevinde parçalara izin verildi:** hangi parçaların hangi düğümlere yerleştirilebileceğini belirleyen işlevi tanımlar

**varsayılan \_ eşik:** toplam parça maliyetinin düğümler arasında ne kadar dengelenmesi gerektiğini belirten bir kayan nokta eşiği

**Minimum \_ eşik:** (isteğe bağlı) \_ tablo parçaları () için eşik bağımsız değişkeni için izin verilen en düşük değeri tutan bir koruma sütunu \_ . Varsayılan değer 0 ' dır

#### <a name="return-value"></a>Dönüş Değeri

Yok

### <a name="citus_set_default_rebalance_strategy"></a>citus \_ \_ varsayılan yeniden \_ Dengeleme \_ stratejisi ayarla

[Pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) tablosunu güncelleştirin ve bağımsız değişkeni tarafından adlandırılan stratejiyi, parçaları yeniden dengeleme sırasında seçilen varsayılan değer olacak şekilde değiştirerek.

#### <a name="arguments"></a>Bağımsız değişkenler

**ad:** pg \_ Dist yeniden \_ Dengeleme \_ stratejisindeki stratejinin adı

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus \_ uzak \_ bağlantı \_ İstatistikleri

Citus \_ Remote \_ Connection \_ stats () işlevi, her bir uzak düğüme yönelik etkin bağlantı sayısını gösterir.

#### <a name="arguments"></a>Bağımsız değişkenler

Yok

#### <a name="example"></a>Örnek

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>Ana \_ boşaltma \_ düğümü

Master \_ boşalt \_ node () işlevi, parçaları belirlenen düğüme ve `shouldhaveshards` [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)' de true olarak ayarlanmış diğer düğümlere taşılar. Sunucu grubundan bir düğümü kaldırmadan ve düğümün fiziksel sunucusunu kapatmadan önce işlevi çağırın.

#### <a name="arguments"></a>Bağımsız değişkenler

**DüğümAdı:** Boşaltılır olan düğümün ana bilgisayar adı.

**NodePort:** Drenaj olacak düğümün bağlantı noktası numarası.

parça **\_ aktarma \_ modu:** (Isteğe bağlı) PostgreSQL mantıksal çoğaltma veya bir çapraz çalışan kopyalama komutu kullanılıp kullanılmayacağını belirtir. Olası değerler şunlardır:

> -   `auto`: Mantıksal çoğaltma mümkünse çoğaltma kimliği gerektir, aksi takdirde eski davranışı kullanın (örneğin, parça onarımı için PostgreSQL 9,6). Varsayılan değer budur.
> -   `force_logical`: Tablonun bir çoğaltma kimliği olmasa bile mantıksal çoğaltma kullanın. Tablodaki tüm eş zamanlı güncelleştirme/silme deyimleri çoğaltma sırasında başarısız olur.
> -   `block_writes`: Birincil anahtar veya çoğaltma kimliği olmayan tablolar için kopyalama (yazmaları engelleme) kullanın.

yeniden **Dengeleme \_ stratejisi:** (isteğe bağlı) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)bir stratejinin adı.
Bu bağımsız değişken atlanırsa, işlev tabloda gösterildiği gibi varsayılan stratejiyi seçer.

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="example"></a>Örnek

Tek bir düğümü kaldırma (örneğin, standart bir PostgreSQL bağlantı noktasında ' 10.0.0.1 ') için tipik adımlar aşağıda verilmiştir:

1.  Düğümü boşaltabilir.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Komut bitene kadar bekle

3.  Düğümü Kaldır

Birden çok düğüm boşaltıyor olduğunda, bunun yerine [rebalance_table_shards](#rebalance_table_shards) kullanılması önerilir. Bunun yapılması, Hiperscale (Citus) uygulamasının önceden planlanmasını ve parçaların en az sayıda kez taşınmasını sağlar.

1.  Kaldırmak istediğiniz her düğüm için öğesini çalıştırın:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  [Rebalance_table_shards](#rebalance_table_shards) ile tümünü bir kez boşaltma

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Boşaltma yeniden dengelenmesi bitene kadar bekleyin

4.  Düğümleri kaldır

### <a name="replicate_table_shards"></a>Tablo parçaları çoğaltma \_ \_

Çoğaltılan \_ tablo parçaları \_ () işlevi, verilen tablonun alt çoğaltılan parçalarını çoğaltır. İşlev ilk olarak, çoğaltma için getiribilecekleri, çoğaltılan parçaların ve konumların listesini hesaplar. Sonra işlevi bu parçaların üzerine kopyalar ve ilgili parça meta verilerini kopyayı yansıtacak şekilde güncelleştirir.

#### <a name="arguments"></a>Bağımsız değişkenler

**tablo \_ adı:** parçalar çoğaltılmakta olan tablonun adı.

parça **\_ çoğaltma \_ faktörü:** (isteğe bağlı) her parçaya ulaşmak için istenen çoğaltma faktörü.

**en büyük parça \_ \_ kopyaları:** (isteğe bağlı) istenen çoğaltma faktörüne ulaşmak Için kopyalamak üzere en fazla parça sayısı.

**Dışlanan parça \_ \_ listesi:** (isteğe bağlı) çoğaltma işlemi sırasında kopyalanmaması gereken parçalara yönelik tanımlayıcılar.

#### <a name="return-value"></a>Dönüş Değeri

Yok

#### <a name="examples"></a>Örnekler

Aşağıdaki örnek, GitHub olayları tablosunun parçalarını parça çoğaltma faktörüyle çoğaltmaya çalışır \_ \_ \_ .

```postgresql
SELECT replicate_table_shards('github_events');
```

Bu örnek, GitHub olayları tablosunun parçalarını, \_ en fazla 10 parçalı kopyaya sahip istenen çoğaltma faktörüyle getirmeye çalışacaktır. Redengeleyici, istenen çoğaltma faktörüne ulaşmak için girişimine en fazla 10 parça kopyalayacaktır.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>\_kiracıyı \_ \_ Yeni \_ parçalara ayır

Bu işlev, dağıtım sütununda belirli bir tek değere sahip satırları tutmak için yeni bir parça oluşturur. Büyük bir kiracının kendi parça ve son olarak kendi fiziksel düğümüne yerleştirilebileceği çok kiracılı hiper ölçek (Citus) kullanım örneği için özellikle kullanışlıdır.

#### <a name="arguments"></a>Bağımsız değişkenler

**tablo \_ adı:** yeni bir parça alınacak tablonun adı.

**kiracı \_ kimliği:** yeni parçaya atanacak olan dağıtım sütununun değeri.

**Cascade \_ seçeneği:** (isteğe bağlı) olarak \" ayarlandığında \" Aynı zamanda, geçerli tablonun birlikte bulundurma [grubundaki](concepts-hyperscale-colocation.md)tüm tablolardan bir parça ayırır.

#### <a name="return-value"></a>Dönüş Değeri

parça **\_ kimliği:** işlev, yeni oluşturulan parçaya atanan benzersiz kimliği döndürür.

#### <a name="examples"></a>Örnekler

Kiracı 135 için satır öğelerini tutmak üzere yeni bir parça oluşturun:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Sonraki adımlar

* Bu makaledeki işlevlerin birçoğu sistem [meta veri tablolarını](reference-hyperscale-metadata.md) değiştirir
* [Sunucu parametreleri](reference-hyperscale-parameters.md) bazı işlevlerin davranışını özelleştirme

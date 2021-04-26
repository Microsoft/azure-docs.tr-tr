---
title: Sunucu parametreleri – Hyperscale (Citus)-PostgreSQL için Azure veritabanı
description: Hyperscale (Citus) SQL API 'sindeki parametreler
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336250"
---
# <a name="server-parameters"></a>Sunucu parametreleri

Hem standart PostgreSQL hem de Hyperscale 'e (Citus) özgü Hyperscale (Citus) davranışını etkileyen çeşitli sunucu parametreleri vardır.
Bu parametreler, bir hiper ölçek (Citus) sunucu grubu için Azure portal ayarlanabilir. **Ayarlar** kategorisi altında **çalışan düğümü parametreleri** veya **Düzenleyici düğüm parametreleri**' ni seçin. Bu sayfalar, tüm çalışan düğümleri için veya yalnızca Düzenleyici düğümü için parametreleri ayarlamanıza olanak sağlar.

## <a name="hyperscale-citus-parameters"></a>Hyperscale (Citus) parametreleri

> [!NOTE]
>
> Citus altyapısının eski sürümlerini çalıştıran hyperscale (Citus) sunucu grupları aşağıda listelenen tüm parametreleri sunmayabilir.

### <a name="general-configuration"></a>Genel yapılandırma

#### <a name="citususe_secondary_nodes-enum"></a>citus. \_ İkincil \_ düğümleri kullan (enum)

Seçme sorguları için düğüm seçerken kullanılacak ilkeyi ayarlar. ' Always ' olarak ayarlanırsa, Planner yalnızca [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)' ikincil ' noderole olarak işaretlenen düğümleri sorgular.

Bu sabit listesi için desteklenen değerler şunlardır:

-   **asla:** (varsayılan) birincil düğümlerde tüm okumalar gerçekleşir.
-   **her zaman:** Bunun yerine ikincil düğümlere karşı çalıştırılan okuma ve INSERT/update deyimleri devre dışı bırakıldı.

#### <a name="cituscluster_name-text"></a>citus. Cluster \_ adı (metin)

Koordinatör düğümü planlayıcısı 'nda hangi kümenin koordinatının olduğunu bildirir. Küme adı ayarlandıktan sonra \_ , Planner yalnızca o kümedeki çalışan düğümlerini sorgular.

#### <a name="citusenable_version_checks-boolean"></a>citus. \_ Sürüm \_ denetimlerini etkinleştir (Boole)

Hiper ölçek (Citus) sürümünü yükseltmek için sunucu yeniden başlatması (yeni paylaşılan kitaplığı seçmek için) ve ardından ALTER EXTENSION UPDATE komutu gerekir.  İki adımı yürütme hatası muhtemelen hatalara veya kilitlenmelere neden olabilir.
Hiper ölçek (Citus), bu nedenle kodun sürümünü ve uzantı eşleşmesini doğrular ve hata vermez \' .

Bu değer varsayılan olarak true 'dur ve düzenleyici üzerinde etkilidir. Nadir durumlarda, karmaşık yükseltme işlemlerinde bu parametrenin false olarak ayarlanması, dolayısıyla denetimi devre dışı bırakılması gerekebilir.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus. log \_ Dağıtılmış \_ kilitlenme \_ algılaması (Boolean)

Dağıtılmış kilitlenme algılamayla ilgili işlemenin sunucu günlüğünde günlüğe kaydedilip edilmeyeceğini belirtir. Varsayılan olarak false değerini alır.

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus. dağıtılmış \_ kilitlenme \_ algılama \_ faktörü (kayan nokta)

Dağıtılmış kilitlenmeleri denetlemeden önce beklenecek süreyi ayarlar. Bu değer özellikle bekleme süresi, PostgreSQL \' s [kilitlenme \_ zaman aşımı](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) ayarıyla çarpılacak. `2` varsayılan değerdir. Değeri, `-1` Dağıtılmış kilitlenme algılamasını devre dışı bırakır.

#### <a name="citusnode_connection_timeout-integer"></a>citus. Node \_ bağlantısı \_ zaman aşımı (tamsayı)

`citus.node_connection_timeout`Guc, bağlantı kurulması için beklenecek en uzun süreyi (milisaniye cinsinden) ayarlar. Hiper ölçek (Citus), en az bir çalışan bağlantısı oluşturulmadan önce zaman aşımı geçtiğinde bir hata oluşturur. Bu GUC, düzenleyicilere ve çalışanlara olan bağlantıları etkiler.

-   Varsayılan: beş saniye
-   En az: 10 milisaniye
-   Maksimum: bir saat

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>Sorgu Istatistikleri

#### <a name="citusstat_statements_purge_interval-integer"></a>citus. stat \_ deyimlerini \_ Temizleme \_ aralığı (tamsayı)

Bakım arka plan programının ' de eşleşmeyen [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) kayıtları kaldırabileceği sıklığı ayarlar `pg_stat_statements` . Bu yapılandırma değeri, varsayılan değer olan 10 ' a kadar saniye cinsinden, zaman aralığını ayarlar. 0 değeri, temizler öğesini devre dışı bırakır.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Bu parametre düzenleyici üzerinde etkilidir ve çalışma zamanında değiştirilebilir.

### <a name="data-loading"></a>Veri yükleme

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. Multi \_ kıard \_ COMMIT \_ Protokolü (enum)

Karma Dağıtılmış bir tabloda kopyalama gerçekleştirirken kullanılacak tamamlama protokolünü ayarlar. Her bir parça yerleşimi üzerinde, kopyalama sırasında bir hata oluşursa hiçbir veri kullanılmadığından emin olmak için kopya bir işlem bloğunda gerçekleştirilir. Ancak, KOPYALAMANıN tüm yerleştirmede başarılı olduğu belirli bir hata durumu vardır ancak tüm işlemler kaydedilmeden önce bir (donanım) arızası oluşur. Bu parametre, aşağıdaki tamamlama protokolleri arasında seçim yaparak bu durumda veri kaybını engellemek için kullanılabilir:

-   **2PC:** (varsayılan) parça yerleşimi ÜZERINDE yapılan kopyanın Ilk kez PostgreSQL \' s [iki aşamalı işleme](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) kullanılarak hazırlanmakta ve sonra kararlıdır. Başarısız işlemeler, sırasıyla yürütme veya GERI alma PREPARED kullanılarak el ile kurtarılabilir veya iptal edilebilir.
    2PC kullanılırken, genellikle en fazla [ \_ hazırlanan \_ işlem](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) , en fazla bağlantı ile aynı değere sahip tüm çalışanlar üzerinde artmalıdır \_ .
-   **1pc:** Parça yerleştirmelerinin üzerinde gerçekleştirileceği işlemler tek bir turda kaydedilir. Tüm yerleştirmelerin (nadir) kopyalama başarılı olduktan sonra bir işlem başarısız olursa, veriler kaybolabilir.

#### <a name="citusshard_replication_factor-integer"></a>citus. Shard \_ çoğaltma \_ faktörü (tamsayı)

Parçalar için çoğaltma faktörünü, parçaların yerleştirileceği düğüm sayısını ve varsayılan değer olan 1 ' i ayarlar. Bu parametre çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkilidir. Bu parametre için ideal değer kümenin boyutuna ve düğüm hatası hızına bağlıdır.  Örneğin, büyük kümeler çalıştırırsanız ve düğüm başarısızlıklarını daha sık bir sıklıkta gözlemlerseniz, bu çoğaltma faktörünü artırmak isteyebilirsiniz.

#### <a name="citusshard_count-integer"></a>citus. Shard \_ sayısı (tamsayı)

Karma bölümlenmiş tablolar için parça sayısını ve varsayılan değer olarak 32 ' i belirler.  Bu değer, karma bölümlenmiş tablolar oluşturulurken [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF tarafından kullanılır. Bu parametre çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkilidir.

#### <a name="citusshard_max_size-integer"></a>citus. Shard \_ en büyük \_ boyutu (tamsayı)

Parçalanmadan önce bir parçanın büyüeceği en büyük boyutu ayarlar ve varsayılan olarak 1 GB olur. Bir parça için kaynak dosyası \' s boyutu (hazırlama için kullanılan) Bu yapılandırma değerini aşarsa, veritabanı yeni bir parçanın oluşturulmasını sağlar. Bu parametre çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkilidir.

### <a name="planner-configuration"></a>Planner yapılandırması

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus. limit \_ yan tümce \_ satırı \_ getirme \_ sayısı (tamsayı)

Limit yan tümcesi iyileştirmesi için görev başına getirilecek satır sayısını ayarlar.
Bazı durumlarda, sınır yan tümceleri olan sorguların seçim yapması, her bir görevden sonuçlar oluşturmak için tüm satırları getirmesi gerekebilir. Bu durumlarda ve yaklaşık anlamlı sonuçlar üretmesi durumunda, bu yapılandırma değeri her bir parçadan getirilecek satır sayısını ayarlar. Sınır sayısı varsayılan olarak devre dışıdır ve bu parametre-1 ' e ayarlanır. Bu değer, çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkilidir.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>citus. Count \_ ayrı \_ hata \_ oranı (kayan nokta)

Hiper ölçek (Citus), PostgreSQL-hll uzantısını kullanarak sayıyı (benzersiz) tahmin edebilir. Bu yapılandırma girişi, Count (benzersiz) hesaplanırken istenen hata oranını ayarlar. Varsayılan değer olan 0,0, sayı (benzersiz) için yaklaşık olarak devre dışı bırakır; ve 1,0 sonuçların doğruluğu hakkında garanti vermez. En iyi sonuçlar için bu parametreyi 0,005 olarak ayarlamayı öneririz. Bu değer, çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkilidir.

#### <a name="citustask_assignment_policy-enum"></a>citus. Task \_ atama \_ ilkesi (enum)

> [!NOTE]
> Bu GUC yalnızca [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) bir veya daha büyük olduğunda veya [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables)sorguları için geçerlidir.

Çalışanlara görev atarken kullanılacak ilkeyi ayarlar. Düzenleyici, işleri parça konumlarına göre çalışanlara atar. Bu yapılandırma değeri, bu atamalar yapılırken kullanılacak ilkeyi belirtir.
Şu anda kullanılabilecek üç olası görev atama ilkesi vardır.

-   **Greedy:** Doyumsuz ilkesi, çalışanlar arasında görevleri eşit bir şekilde dağıtmak için varsayılan ve amaçlar 'dir.
-   Hepsini bir **kez deneme:** Hepsini bir kez deneme ilkesi, işleri farklı çoğaltmalar arasında değişen bir şekilde bir kez daha kez çalışan bir biçimde atar. Bu ilke, bir tablonun parça sayısı, çalışan sayısına göre düşük olduğunda daha iyi küme kullanımını sağlar.
-   **ilk çoğaltma:** İlk çoğaltma ilkesi, parçaları için yerleştirme sırası (çoğaltmalar) temelinde görevler atar. Diğer bir deyişle, bir parça için parça sorgusu, bu parçanın ilk çoğaltması olan çalışana atanır.
    Bu yöntem, hangi parçalar için hangi parçaların kullanılacağı (diğer bir deyişle, daha güçlü bellek daha fazla garanti güvence altına alınır) için güçlü garanti sağlar.

Bu parametre çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkilidir.

### <a name="intermediate-data-transfer"></a>Ara Veri Aktarımı

#### <a name="citusbinary_worker_copy_format-boolean"></a>citus. Binary \_ çalışan \_ kopya \_ biçimi (Boolean)

Çalışanlar arasında ara verileri aktarmak için ikili kopya biçimini kullanın.
Büyük tablo birleşimleri sırasında, hiper ölçek (Citus) farklı çalışanlar arasında verileri dinamik olarak yeniden bölümlemek ve karıştırmak zorunda kalabilir. Varsayılan olarak, bu veriler metin biçiminde aktarılır. Bu parametrenin etkinleştirilmesi, veritabanının bu verileri aktarmak için PostgreSQL 'in ikili serileştirme biçimini kullanmasını söyler. Bu parametre, çalışanlar üzerinde etkilidir ve PostgreSQL. conf dosyasında değiştirilmesi gerekir. Yapılandırma dosyasını düzenledikten sonra, kullanıcılar bu değişikliğin etkili olması için bir veya daha fazla sinyal gönderebilir veya sunucuyu yeniden başlatabilir.

#### <a name="citusbinary_master_copy_format-boolean"></a>citus. Binary \_ ana \_ kopya \_ biçimi (Boolean)

Koordinatör ve çalışanlar arasında veri aktarmak için ikili kopya biçimini kullanın. Dağıtılmış sorgular çalıştırılırken, çalışanlar ara sonuçları son toplama için düzenleyiciye aktarır. Varsayılan olarak, bu veriler metin biçiminde aktarılır. Bu parametrenin etkinleştirilmesi, veritabanının bu verileri aktarmak için PostgreSQL 'in ikili serileştirme biçimini kullanmasını söyler.
Bu parametre, çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkin olabilir.

#### <a name="citusmax_intermediate_result_size-integer"></a>citus. Max \_ Ara \_ sonuç \_ boyutu (tamsayı)

Yürütme için çalışan düğümlerine itilmeyecek ve karmaşık alt sorgularda, CTEs için ara sonuçların KB cinsinden en büyük boyutu. Varsayılan 1 GB 'tır ve-1 değeri sınır yoktur.
Sınırı aşan sorgular iptal edilecek ve bir hata mesajı oluşturacak.

### <a name="ddl"></a>DDL

#### <a name="citusenable_ddl_propagation-boolean"></a>citus. \_ DDL \_ yaymayı etkinleştir (Boolean)

DDL değişikliklerinin düzenleyicilere otomatik olarak tüm çalışanlara yayıp yaymayacağını belirtir. Varsayılan değer true şeklindedir. Bazı şema değişiklikleri tablolar üzerinde özel bir erişim kilidi gerektirdiğinden ve otomatik yayma tüm çalışanlar için ardışık olarak yapıldığından, hiper ölçek (Citus) kümesini geçici olarak daha az yanıt verebilir hale getirebilirsiniz. Bu ayarı devre dışı bırakmayı ve değişiklikleri el ile yaymaya yönelik seçim yapabilirsiniz.

### <a name="executor-configuration"></a>Yürütücü yapılandırması

#### <a name="general"></a>Genel

##### <a name="citusall_modifications_commutative"></a>citus. tüm \_ değişiklikler \_ yürütülüyor

Hiper ölçek (Citus), çalışma kuralları uygular ve davranışın doğruluğundan emin olmak için değiştirme işlemleri için uygun kilitleri alır. Örneğin, bir INSERT ifadesinin başka bir INSERT ifadesiyle ilgili olduğunu varsayar, ancak bir UPDATE veya DELETE ifadesiyle değildir. Benzer şekilde, bir UPDATE veya DELETE ifadesinin başka bir UPDATE veya DELETE ifadesiyle ilgili olduğunu varsaymaktadır. Bu önlem, güncelleştirmelerin ve silinmenin daha güçlü kilitler elde etmek için hiper ölçek (Citus) gerektirmesidir.

Eklemelerinizin veya diğer güncelleştirmelerinizin bulunduğu GÜNCELLEŞTIRME deyimlerine sahipseniz, bu parametreyi true olarak ayarlayarak bu ilgili bu varsayımları hafifleyerek rahatlığınızı yapabilirsiniz. Bu parametre true olarak ayarlandığında, tüm komutlar işlem için kabul edilir ve paylaşılan bir kilit talep edilir ve bu da genel üretilen işi geliştirebilirler. Bu parametre, çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkin olabilir.

##### <a name="citusremote_task_check_interval-integer"></a>citus. uzak \_ görev \_ Denetim \_ aralığı (tamsayı)

, Hiper ölçeğin (Citus), görev izleyici yürütücüsü tarafından yönetilen işlerin durumlarını denetlediği sıklığı ayarlar. Varsayılan olarak 10 MS olur. Düzenleyici çalışanları çalışanlara atar ve sonra her bir görev ilerlemesi hakkında düzenli olarak kontrol eder \' . Bu yapılandırma değeri iki arada bir denetim arasındaki zaman aralığını ayarlar. Bu parametre düzenleyici üzerinde etkilidir ve çalışma zamanında ayarlanabilir.

##### <a name="citustask_executor_type-enum"></a>citus. Task \_ yürütücü \_ türü (enum)

Hiper ölçekte (Citus) dağıtılmış SELECT sorguları çalıştırmak için üç yürütücü türü vardır.  İstenen yürütücü bu yapılandırma parametresi ayarlanarak seçilebilir. Bu parametre için kabul edilen değerler şunlardır:

-   **Uyarlamalı:** Varsayılan değer. Birden çok parça arasında yayılan toplamaları ve birlikte bulunan birleştirmeleri içeren sorgulara hızlı yanıt almak için idealdir.
-   **görev-İzleyici:** Görev izleyici yürütücüsü, çalışan düğümlere ve verimli kaynak yönetimine kadar veri karıştırma gerektiren uzun süre çalışan, karmaşık sorgular için idealdir.
-   **gerçek zamanlı:** (kullanım dışı) Uyarlamalı yürütücü olarak benzer bir amaca hizmet eder, ancak daha az esnektir ve çalışan düğümlerinde daha fazla bağlantı baskısı oluşmasına neden olabilir.

Bu parametre çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkilidir.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus. Multi \_ görevi \_ sorgu \_ günlüğü \_ düzeyi (enum) {#multi_task_logging}

Birden fazla görev üreten herhangi bir sorgu için günlük düzeyi ayarlar (yani, birden fazla parça ziyaret eden). Birden çok kiracılı uygulama geçişi sırasında günlüğe kaydetme yararlı olur. bu şekilde, bu tür sorgular için hata veya uyarma yapabilir, bunları bulabilir ve bunlara bir kiracı \_ kimliği filtresi ekleyebilirsiniz. Bu parametre, çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkin olabilir. Bu parametre için varsayılan değer \' kapalıdır \' .

Bu sabit listesi için desteklenen değerler şunlardır:

-   **Kapalı:** Birden çok görev üreten tüm sorguları günlüğe kaydetmeyi devre dışı bırakma (yani birden çok parçaya yayın)
-   **Hata Ayıkla:** Hata ayıklama önem düzeyinde logs deyimleri.
-   **günlüğe kaydet:** Günlük önem düzeyinde LOG ifadesini kaydeder. Günlük satırına çalıştırılan SQL sorgusu dahil edilir.
-   **Uyarı:** Günlük bildirimini BILDIRIM önem düzeyine göre günlüğe kaydeder.
-   **Uyarı:** Uyarı önem derecesine sahip deyimleri günlüğe kaydeder.
-   **hata:** HATA önem derecesine sahip deyimleri günlüğe kaydeder.

`error`Geliştirme testi sırasında ve `log` gerçek üretim dağıtımı sırasında olduğu gibi daha düşük bir günlük düzeyinde kullanılması faydalı olabilir.
Seçim `log` yapmak, çok görev sorgularının sorgu tarafından gösterilen sorgu ile veritabanı günlüklerinde görünmesine neden olur \" .\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus. yeniden \_ bölümleme \_ katılımları etkinleştir (Boolean)

Normalde, uyarlamalı yürütücü ile yeniden bölümleme birleştirmeleri gerçekleştirmeye çalışmak bir hata iletisiyle başarısız olur.  Bununla birlikte, `citus.enable_repartition_joins` doğru olarak ayarlanması, hiper ölçeklendirmeyi (Citus) geçici olarak görev izleyici Yürütücüsü ' ne, katılmayı gerçekleştirecek şekilde değiştirmesine izin verir.  Varsayılan değer false'tur.

#### <a name="task-tracker-executor-configuration"></a>Görev izleyici yürütücü yapılandırması

##### <a name="citustask_tracker_delay-integer"></a>citus. Task \_ Tracker \_ gecikmesi (tamsayı)

Bu parametre, görev yönetimi ve varsayılan değer 200 ms olarak görev izleyici uyku süresini ayarlar. Görev izleyici işlemi düzenli olarak uyandırır, kendisine atanan tüm görevleri açıklar ve bu görevleri zamanlar ve yürütür.  Daha sonra görev izleyici, bu görevleri yeniden yürüten önce bir süre boyunca uyku moduna geçer.
Bu yapılandırma değeri, bu uyku döneminin uzunluğunu belirler. Bu parametre, çalışanlar üzerinde etkilidir ve PostgreSQL. conf dosyasında değiştirilmesi gerekir. Yapılandırma dosyasını düzenledikten sonra, kullanıcılar değişikliğin etkili olması için bir işlem sinyali gönderebilir veya sunucuyu yeniden başlatabilir.

Bu parametre, yönetim 'in her ne kadar zaman aralığı azaltıldığında, görev izleyici yürütücüsü nedeniyle oluşan gecikmeyi kırpmak için azaltılabilir.
Gecikmeyi azaltmak, parça sorgularının kısa olduğu durumlarda ve bu nedenle durumlarını düzenli olarak güncelleştirmede yararlıdır.

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus. en \_ fazla \_ görev ata \_ toplu iş \_ boyutu (tamsayı)

Düzenleyicinin görev izleyici yürütücüsü, çalışanlara zaman uyumlu olarak görevler halinde görev atar. Bu parametre, tek bir toplu işte atanacak en fazla görev sayısını ayarlar. Daha büyük bir toplu iş boyutu seçilmesi daha hızlı görev atamasına izin verir. Ancak, çalışan sayısı büyükse, tüm çalışanların görevleri alması daha uzun sürebilir.  Bu parametre, çalışma zamanında ayarlanabilir ve düzenleyici üzerinde etkin olabilir.

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus. \_ düğüm başına en fazla çalışan \_ görev \_ \_ (tamsayı)

Görev izleyici işlemi, uygun şekilde kendisine atanan görevleri zamanlar ve yürütür. Bu yapılandırma değeri, belirli bir zamanda bir düğümde aynı anda yürütülecek en fazla görev sayısını ayarlar ve varsayılan değer 8 ' dir.

Sınır, \' diskte aynı anda çok sayıda görev vurmasını ve disk g/ç çekişmesini önlemeye yardımcı olur. Sorgularınız bellekten veya SSDs 'den sunuluyorsa, \_ \_ çok önemli olmayan, düğüm başına en fazla çalışan görevi artırabilirsiniz \_ \_ .

##### <a name="cituspartition_buffer_size-integer"></a>citus. Partition \_ arabellek \_ boyutu (tamsayı)

Bölüm işlemleri için kullanılacak arabellek boyutunu ve varsayılan olarak 8 MB 'ı belirler.
Hiper ölçek (Citus), iki büyük tablo birleştirilirken tablo verilerinin birden çok dosyaya yeniden bölümlenmesi için izin verir. Bu bölüm arabelleği doldurulduktan sonra, yeniden bölümlenen veriler diskteki dosyalara temizlenir.  Bu yapılandırma girişi, çalışma zamanında ayarlanabilir ve çalışanlar üzerinde etkilidir.

#### <a name="explain-output"></a>Çıktıyı açıkla

##### <a name="citusexplain_all_tasks-boolean"></a>citus. \_ tüm \_ görevleri açıkla (Boolean)

Varsayılan olarak, hiper ölçek (Citus), dağıtılmış bir sorguda [açıkla](http://www.postgresql.org/docs/current/static/sql-explain.html) çalışırken tek ve rastgele bir görevin çıkışını gösterir. Çoğu durumda, açıklama çıktısı görevler arasında benzerdir. Bazen bazı görevler farklı planlanacaktır veya çok daha yüksek yürütme zamanına sahip olur. Bu gibi durumlarda, bu parametreyi etkinleştirmek yararlı olabilir ve bu parametrenin ardından, açıklama çıkışının tüm görevleri içermesi gerekir. Tüm görevlerin açıklanması, anlayarak daha uzun sürmesine neden olabilir.

## <a name="postgresql-parameters"></a>PostgreSQL parametreleri

* [Dadtestyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) -tarih ve saat değerleri için görüntüleme biçimini ayarlar
* [Interinterval stili](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) -Aralık değerleri için görüntüleme biçimini ayarlar
* [Saat dilimi](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) -zaman damgalarını görüntülemek ve yorumlamak için saat dilimini ayarlar
* [Application_Name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) -istatistikler ve günlüklerde bildirilecek uygulama adını ayarlar
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) -dizilerde null öğelerin girişini etkinleştirilir
* [oto vakum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) -oto vakum alt öğesini başlatır
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) -kayıt düzeni sayısı, relbaşlıkların bir bölümü olarak çözümlenmesinden önce ekleme, güncelleştirme veya silme
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) -analiz etmeden önce en az kayıt kümesi ekleme, güncelleştirme veya silme sayısı
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) , oto vakum çalıştırmaları arasında uykuya geçme süresi
* Oto için [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) vakum maliyet gecikme süresi (saniye)
* , Oto için, kmadan önce kullanılabilir [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) vakum maliyeti miktarı
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) -kayıt kümesi güncelleştirmelerinin sayısı veya bir relın bir bölümü olarak vakum 'tan önce silme
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) -en düşük demet güncelleştirme sayısı veya vakum 'tan önce siler
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) -her bir oto çalışan işlemi tarafından kullanılacak maksimum belleği ayarlar
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) -daha önce gerçekleştirilen yazma işlemleri diske boşaltıldıktan sonra sayfa sayısı
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) - \' dize değişmez değerlerinde "" kullanımına izin verilip verilmeyeceğini ayarlar
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) -arka plan yazıcı uyku süresi
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) -daha önce gerçekleştirilen yazma işlemleri diske boşaltıldıktan sonra sayfa sayısı
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) -arka plan yazıcısı, her yuvarlak temizlenecek en fazla LRU sayfa sayısı
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) -her yuvarlak ücretsiz olarak ortalama arabellek kullanımının katı
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) -bytea için çıkış biçimini ayarlar
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) -oluşturma işlevi sırasında işlev gövdelerini denetler
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) -kontrol noktası aralığının kesri olarak denetim noktası sırasında kirli arabellekleri temizlemeye harcanan süre
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) -otomatik Wal kontrol noktaları arasındaki en uzun süreyi ayarlar
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) -denetim noktası kesimleri bundan daha sık doldurulmuşsa, uyarıları etkinleştirilir
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) -istemcinin karakter kümesi kodlamasını ayarlar
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) -istemciye gönderilen ileti düzeylerini ayarlar
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) -işlem işleme ve diski diske reçeteye göre boşaltma arasındaki gecikmeyi mikrosaniye cinsinden ayarlar
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) -commit_delay gerçekleştirmeden önce en az eşzamanlı açık işlemleri ayarlar
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) -Ajanda 'nın sorguları iyileştirmek için kısıtlamalar kullanmasına olanak sağlar
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) -bir dizin taraması sırasında her bir dizin girişini işleme maliyetinin planlayıcının tahminini belirler
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) -her bir operatör veya işlev çağrısını işleme maliyetinin planlayıcının tahminini ayarlar
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) -planlayıcının her bir tanımlama grubu (satır) işleme maliyetinin tahminini belirler
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) -Planner 'ın, alınacak bir imlecin satır kesiri için olan tahminini belirler
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) -kilitlenmeyi denetlemeden önce bir kilit için beklenecek süreyi milisaniye olarak ayarlar
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) -girintiler ayrıştırma ve plan ağacı ekranları
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -her sorgunun ayrıştırma ağacını günlüğe kaydeder
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -her sorgunun yürütme planını günlüğe kaydeder
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -her sorgunun yeniden yazan ayrıştırma ağacını günlüğe kaydeder
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) -varsayılan istatistik hedefini ayarlar
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) -' de tablolar ve dizinler oluşturmak için varsayılan tablo alanını ayarlar
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) -varsayılan metin arama yapılandırmasını ayarlar
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) -yeni işlemlerin varsayılan ertelik durumunu ayarlar
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) -her yeni işlemin işlem yalıtım düzeyini ayarlar
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) -yeni işlemlerin varsayılan salt okunurdur durumunu ayarlar
* default_with_oids-varsayılan olarak OID 'ler içeren yeni tablolar oluşturur
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) -Planner 'ın disk önbelleğinin boyutu hakkında varsayımını belirler
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) -, planlayıcının bit eşlem tarama planları kullanımını mümkün
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) -planlayıcının birleştirme planlarını toplama kullanımını mümkün
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) -planlayıcının karma toplama planlarını kullanmasını mümkün hale
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) -Planner 'ın karma JOIN planları kullanımını mümkün hale
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) -Planlayıcı 'nın yalnızca dizin tarama planlarını kullanmasını mümkün bir şekilde sunar
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) -Planner 'ın Dizin tarama planlarının kullanımını mümkün
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) -Planner 'ın gerçekleştirmesi kullanımını mümkün hale getirme
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) -planlayıcının birleştirme birleştirme planlarını kullanmasına izin vermez
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) -planlayıcının iç içe geçmiş döngülü JOIN planlarına kullanımını etkinleştirilir
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) -planlayıcının sıralı tarama planları kullanımını mümkün
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) -planlayıcının açık sıralama adımlarının kullanımını mümkün
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) -PLANNER 'ın TID tarama planlarının kullanımını mümkün
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) -normal dize değişmez değerlerinde ters eğik çizgi çıkışları hakkında uyarır
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) -oturumu herhangi bir hata üzerinde sonlandırır
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) -kayan nokta değerleri için görünen basamak sayısını ayarlar
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) -paralel sorgu tesislerinin kullanımını zorlar
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) -listede alt sorgular daraltılamayacak olan listeden gelen liste boyutunu ayarlar
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) -genesel sorgu iyileştirmeyi etkinleştirilir
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo: efor, DIĞER geqo parametreleri için varsayılan değeri ayarlamak için kullanılır
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) -geqo: algoritmanın yineleme sayısı
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo: popülasyondaki kişilerin sayısı
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo: rastgele yol seçimi için çekirdek
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo: popülasyon içinde Seçmeli basınç
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) -GEQO 'nun kullanıldığı öğelerin eşiğini ayarlar
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) -Gin tarafından tam arama için izin verilen en yüksek sonucu ayarlar
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) -gın dizini için bekleyen listenin en büyük boyutunu ayarlar
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) -her bir işlem için izin verilen maksimum süreyi ayarlar
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) -, JOIN yapılarının düzleştirilmemiş olan listeden gelen liste boyutunu ayarlar
* [LC_MONETARY](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) -parasal tutarları biçimlendirmek için yerel ayarı ayarlar
* [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) -yerel ayarı biçimlendirme numaraları için ayarlar
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) -büyük nesneler üzerinde ayrıcalık denetimleri için geriye dönük uyumluluk modunu etkinleştirilir
* [lock_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) -bir kilit için bekleyen izin verilen süre üst sınırını (milisaniye cinsinden) ayarlar. 0 bunu kapatır
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) -en düşük yürütme süresini ayarlar ve bu da, oto dışı eylemlerin günlüğe kaydedileceği
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) -her denetim noktasını günlüğe kaydeder
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) -her başarılı bağlantıyı günlüğe kaydeder
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) -sunucu günlüğü çıkışı için hedefi ayarlar
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) -süresi de dahil olmak üzere bir oturumun sonuna kadar günlüğe kaydedilir
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) -tamamlanan her SQL deyimin süresini günlüğe kaydeder
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) -günlüğe kaydedilen iletilerin ayrıntı düzeyini ayarlar
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) -günlük uzun kilit bekler
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) -yukarıdaki deyimlerin kaydedileceği en düşük yürütme süresini (milisaniye cinsinden) ayarlar. -1 günlük deyimleri sürelerini devre dışı bırakır
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) -bu düzeyin üzerinde veya üzerinde bu düzeyin üzerinde hata üreten tüm deyimlerin günlüğe kaydedilmesini sağlar
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) -günlüğe kaydedilen ileti düzeylerini ayarlar
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) -her çoğaltma komutunu günlüğe kaydeder
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) -günlüğe kaydedilen deyimlerin türünü ayarlar
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) -her sorgu için, toplu performans istatistiklerini sunucu günlüğüne yazar
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) -bu kilobayt sayısından daha büyük geçici dosya kullanımını günlüğe kaydeder
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) -bakım işlemleri için kullanılacak maksimum belleği ayarlar
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) -tek seferde etkin olabilecek en fazla paralel çalışan sayısını ayarlar
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) -yürütücü düğümü başına en fazla paralel işlem sayısını ayarlar
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) -sayfa başına en fazla koşul kilitli tanımlama grubu sayısını ayarlar
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) -her ilişki için izin verilen en fazla küme kilitli sayfa sayısını ve tanımlama gruplarını ayarlar
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) , etkin bir hazır bekleyen sunucu arşivlenmiş Wal verilerini işlerken sorguları iptal etmeden önce en fazla gecikme sayısını ayarlar
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) -etkin bir bekleyen sunucu akan Wal verilerini işlerken sorguları iptal etmeden önce maksimum gecikme sayısını ayarlar
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) -abonelik başına en fazla tablo eşitleme çalışanı sayısı
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) -bir kontrol noktasını tetikleyen Wal boyutunu ayarlar
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) -paralel tarama için en az dizin verisi miktarını ayarlar
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) -Wal 'yi daraltmak için en küçük boyutu ayarlar
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) -PostgreSQL 9,4 ' den bu yana anlamı değişen yapılar için bir uyarı yayar
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) -, paralel sorgu için çalışan işlemlerinin başlama maliyetini planlayıcının tahminini belirler
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) -planlayıcının her bir tanımlama grubunu (satır) çalışana ana arka uca geçirme maliyetinin tahminini belirler
* [pg_stat_statements. Save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -pg_stat_statements istatistiklerini sunucu kapatmaları arasında kaydeder
* [pg_stat_statements. Track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -hangi deyimlerin pg_stat_statements tarafından izleneceğini seçer
* [pg_stat_statements. track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -yardımcı program komutlarının pg_stat_statements tarafından izlenip izlenmeyeceğini belirler
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) -SQL parçaları oluştururken tüm tanımlayıcıları tırnak içine alın
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) , planlayıcının ardışık olmayan bir şekilde getirilen disk sayfasının maliyetinin tahminini belirler
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) -satır güvenliğini etkinleştirilir
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) -şema arama sırasını şemaya uygun olmayan adlarla belirler
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) -bir sıralı olarak getirilen disk sayfasının maliyetinin tahminini belirler
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) -oturum davranışını Tetikleyiciler ve yeniden yazma kuralları için ayarlar
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) -neden '... ' ters eğik çizgileri işleyecek dizeler
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) -herhangi bir deyimin izin verilen en fazla süreyi (milisaniye cinsinden) ayarlar. 0 bunu kapatır
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) -eşitlenmiş sıralı taramaları etkinleştirilir
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) -geçerli işlemin eşitleme düzeyini ayarlar
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) -en fazla TCP KeepAlive yeniden iletim sayısı
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) -TCP keepcanlı tutma arasında zaman aşımı
* TCP KeepAlive yeniden aktarımlar arasında [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) zamanı
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) -her veritabanı oturumu tarafından kullanılan en fazla geçici arabellek sayısını ayarlar
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) -geçici tablolar ve sıralama dosyaları için kullanılacak tablo alanları 'nı ayarlar
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) -komutları yürütme hakkında bilgi toplar
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) -veritabanı etkinliğinde istatistikleri toplar
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) -veritabanı etkinliğinde işlev düzeyi istatistikleri toplar
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) -veritabanı g/ç etkinliği için zamanlama istatistiklerini toplar
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) -"Expr null" olarak "EXPR = null" olarak davranır
* milisaniye cinsinden [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) vakum maliyet gecikmesi
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) vakum maliyet miktarı, kapatmadan önce kullanılabilir
* Vakum 'e göre bir sayfa için [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) vakum maliyeti
* arabellek önbelleğinde bulunan bir sayfa için [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) vakum maliyeti
* arabellek önbelleğinde bulunmayan bir sayfa için [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) vakum maliyeti
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) -varsa, vakum ve dinamik Temizleme işleminin ertelenmesi gereken işlem sayısı
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) -vakum 'nin bir tablo satırını dondurması gereken minimum yaş
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) : vakum, başlıkları dondurmak için tüm tabloyu taraması gereken Age
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) -bir tablo satırındaki vakum 'nin birden çok xactid 'yi dondurması gereken minimum yaş
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) -multixyasası, çalışma gruplarını dondurmak için vakum tüm tabloyu taraymalıdır
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) -Wal alıcı durum raporları arasındaki en büyük aralığı birincil olarak ayarlar
* WAL yazıcısında gerçekleştirilen WAL temizlemeleri arasında [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) zamanı
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) -Temizleme tetikleyen Wal yazıcı tarafından yazılan Wal miktarı
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) -geçici disk dosyalarına yazmadan önce iç sıralama işlemleri ve karma tabloları tarafından kullanılacak bellek miktarını ayarlar
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) -IKILI değerlerin XML 'de nasıl kodlandığını ayarlar
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) -örtük ayrıştırma ve SERILEŞTIRME işlemlerinde XML verilerinin, belge veya içerik parçaları olarak değerlendirilip değerlendirilmeyeceğini ayarlar

## <a name="next-steps"></a>Sonraki adımlar

* Sunucu parametrelerinin yanı sıra bir hiper ölçek (Citus) sunucu grubundaki kaynak [yapılandırma seçenekleri](concepts-hyperscale-configuration-options.md) de farklı bir yapılandırma biçimidir.
* Temel alınan PostgreSQL veri tabanında [yapılandırma parametreleri](http://www.postgresql.org/docs/current/static/runtime-config.html)de vardır.

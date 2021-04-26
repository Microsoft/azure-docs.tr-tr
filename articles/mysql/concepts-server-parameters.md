---
title: Sunucu parametreleri-MySQL için Azure veritabanı
description: Bu konuda, MySQL için Azure veritabanı 'nda sunucu parametrelerini yapılandırmaya yönelik yönergeler sağlanmaktadır.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/26/2021
ms.openlocfilehash: 756337ce20c827d0c6549181c20fd843fa60c020
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720962"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'nda sunucu parametreleri

Bu makalede, MySQL için Azure veritabanı 'nda sunucu parametrelerini yapılandırmaya yönelik konular ve yönergeler sağlanmaktadır.

## <a name="what-are-server-parameters"></a>Sunucu parametreleri nelerdir? 

MySQL altyapısı, altyapının davranışını yapılandırmak ve ayarlamak için kullanılabilecek birçok farklı sunucu değişkeni/parametresi sağlar. Bazı parametreler, çalışma zamanında dinamik olarak ayarlanabilir, diğerleri ise "statik" olarak ayarlanabilir, bu da bir sunucu yeniden başlatılmasını gerektirir.

MySQL için Azure Veritabanı [Azure portal](./howto-server-parameters.md), [Azure CLI](./howto-configure-server-parameters-using-cli.md) ve [PowerShell](./howto-configure-server-parameters-using-powershell.md) kullanarak çeşitli MySQL sunucu parametrelerini iş yükünüzün gereksinimlerine uyacak şekilde değiştirme olanağı sunar.

## <a name="configurable-server-parameters"></a>Yapılandırılabilir sunucu parametreleri

Desteklenen sunucu parametrelerinin listesi sürekli olarak büyüyordur. Tam listeyi görüntülemek ve sunucu parametreleri değerlerini yapılandırmak için Azure portal sunucu parametreleri sekmesini kullanın.

Yaygın olarak güncellenen çeşitli sunucu parametrelerinin sınırları hakkında daha fazla bilgi edinmek için aşağıdaki bölümlere bakın. Sınırlar, sunucunun fiyatlandırma katmanı ve sanal çekirdekleri tarafından belirlenir.

### <a name="thread-pools"></a>İş parçacığı havuzları

MySQL, her istemci bağlantısı için geleneksel olarak bir iş parçacığı atar. Eşzamanlı kullanıcıların sayısı arttıkça, buna karşılık gelen bir bir bırakma performansı vardır. Birçok etkin iş parçacığı, artan bağlam değiştirme, iş parçacığı çekişmesi ve CPU önbellekleri için bozuk konum nedeniyle performansı önemli ölçüde etkileyebilir.

Sunucu tarafı özelliği olan ve bağlantı havuzundan farklı olan iş parçacığı havuzları, sunucuda çalışan etkin iş parçacıklarının sayısını sınırlandırmak ve iş parçacığı karmaşıklığını en aza indirmek için kullanılabilen bir dizi çalışan iş parçacığının dinamik bir havuzunu sunarak performansı en üst düzeye çıkarır. Bu, bir bağlantı patlaması sunucunun yetersiz bellek hatası ile kaynakların kaynak veya kilitlenme olmasına neden olmamasını sağlamaya yardımcı olur. İş parçacığı havuzları kısa sorgular ve CPU yoğunluklu iş yükleri için en verimli şekilde OLTP iş yükleri gibi etkindir.

İş parçacığı havuzları hakkında daha fazla bilgi için bkz. [MySQL Için Azure veritabanı 'nda iş parçacığı havuzlarına giriş](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)

> [!NOTE]
> İş parçacığı havuzu özelliği MySQL 5,6 sürümü için desteklenmiyor. 

### <a name="configuring-the-thread-pool"></a>İş parçacığı havuzunu yapılandırma
İş parçacığı havuzunu etkinleştirmek için `thread_handling` sunucu parametresini "iş parçacıkları havuzu" olarak güncelleştirin. Varsayılan olarak, bu parametre olarak ayarlanır, bu da `one-thread-per-connection` MySQL her yeni bağlantı için yeni bir iş parçacığı oluşturur. Bunun statik bir parametre olduğunu ve uygulamak için sunucunun yeniden başlatılmasını gerektirdiğini lütfen unutmayın.

Aşağıdaki sunucu parametrelerini ayarlayarak havuzdaki en yüksek ve en düşük iş parçacığı sayısını da yapılandırabilirsiniz: 
- `thread_pool_max_threads`: Bu değer, havuzdaki bu sayıda iş parçacığından daha fazla olmamasını sağlar.
- `thread_pool_min_threads`: Bu değer, bağlantılar kapatıldıktan sonra bile ayrılacak iş parçacığı sayısını ayarlar.

İş parçacığı havuzundaki kısa sorguların performans sorunlarını artırmak için, MySQL için Azure veritabanı, bir Sorgu yürütüldükten hemen sonra iş parçacığı havuzuna geri dönmek yerine toplu yürütmeyi etkinleştirmenizi sağlar, iş parçacıkları Bu bağlantı aracılığıyla bir sonraki sorguyu beklemek için kısa bir süre etkin kalır. İş parçacığı daha sonra sorguyu hızlı bir şekilde yürütür ve tamamlandıktan sonra, bu işlemin genel zaman tüketimi bir eşiği aşana kadar bir sonraki için bekler. Toplu yürütme davranışı aşağıdaki sunucu parametreleri kullanılarak belirlenir:  

-  `thread_pool_batch_wait_timeout`: Bu değer bir iş parçacığının başka bir sorgunun işlemesini bekleyeceği süreyi belirtir.
- `thread_pool_batch_max_time`: Bu değer, bir iş parçacığının sorgu yürütme döngüsünü yineleneceği ve sonraki sorgu için beklerken en uzun süreyi belirler.

> [!IMPORTANT]
> Lütfen üretim ortamında açmadan önce iş parçacığı havuzunu test edin. 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

MySQL için Azure veritabanı 'nda, ikili Günlükler her zaman etkindir (yani `log_bin` Açık olarak ayarlanır). Tetikleyicileri kullanmak istiyorsanız, *süper ayrıcalığa sahip değilsiniz ve ikili günlüğe kaydetme özelliğinin etkin olduğu bir hata alırsınız (daha az güvenli bir değişken kullanmak isteyebilirsiniz `log_bin_trust_function_creators` )*. 

İkili günlük biçimi her zaman **satırdır** ve sunucuya yapılan tüm bağlantılar **her zaman** satır tabanlı ikili günlük kullanır. Satır tabanlı ikili günlüğe kaydetme ile güvenlik sorunları yoktur ve ikili günlüğe alma, güvenli şekilde [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) **doğru** olarak ayarlanabilir.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Bu parametre hakkında daha fazla bilgi edinmek için [MySQL belgelerini](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) gözden geçirin.

#### <a name="servers-supporting-up-to-4-tb-storage"></a>4 TB 'a kadar depolamayı destekleyen sunucular

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Varsayılan değer (bayt)**|**En az değer (bayt)**|**En büyük değer (bayt)**|
|---|---|---|---|---|
|Temel|1|872415232|134217728|872415232|
|Temel|2|2684354560|134217728|2684354560|
|Genel Amaçlı|2|3758096384|134217728|3758096384|
|Genel Amaçlı|4|8053063680|134217728|8053063680|
|Genel Amaçlı|8|16106127360|134217728|16106127360|
|Genel Amaçlı|16|32749125632|134217728|32749125632|
|Genel Amaçlı|32|66035122176|134217728|66035122176|
|Genel Amaçlı|64|132070244352|134217728|132070244352|
|Bellek İçin İyileştirilmiş|2|7516192768|134217728|7516192768|
|Bellek İçin İyileştirilmiş|4|16106127360|134217728|16106127360|
|Bellek İçin İyileştirilmiş|8|32212254720|134217728|32212254720|
|Bellek İçin İyileştirilmiş|16|65498251264|134217728|65498251264|
|Bellek İçin İyileştirilmiş|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>Sunucular 16 TB 'a kadar depolamayı destekler

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Varsayılan değer (bayt)**|**En az değer (bayt)**|**En büyük değer (bayt)**|
|---|---|---|---|---|
|Temel|1|872415232|134217728|872415232|
|Temel|2|2684354560|134217728|2684354560|
|Genel Amaçlı|2|7516192768|134217728|7516192768|
|Genel Amaçlı|4|16106127360|134217728|16106127360|
|Genel Amaçlı|8|32212254720|134217728|32212254720|
|Genel Amaçlı|16|65498251264|134217728|65498251264|
|Genel Amaçlı|32|132070244352|134217728|132070244352|
|Genel Amaçlı|64|264140488704|134217728|264140488704|
|Bellek İçin İyileştirilmiş|2|15032385536|134217728|15032385536|
|Bellek İçin İyileştirilmiş|4|32212254720|134217728|32212254720|
|Bellek İçin İyileştirilmiş|8|64424509440|134217728|64424509440|
|Bellek İçin İyileştirilmiş|16|130996502528|134217728|130996502528|
|Bellek İçin İyileştirilmiş|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında güncelleştirilebilen bir şekilde yapılandırılabilir.

MySQL, InnoDB tablosunu tablo oluşturma sırasında verdiğiniz yapılandırmaya göre farklı Tablespaces halinde depolar. [Sistem tablo](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) alanı, InnoDB veri sözlüğü için depolama alanıdır. [Tablo başına dosya tablosu](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) , tek bir InnoDB tablosunun verilerini ve dizinlerini içerir ve dosya sisteminde kendi veri dosyasında depolanır. Bu davranış, `innodb_file_per_table` sunucu parametresi tarafından denetlenir. `innodb_file_per_table`İçin ayarı `OFF` , InnoDB 'in, sistem tablo tablosu 'nda tablo oluşturmasına neden olur. Aksi halde, InnoDB tablo başına tabloalanları içinde tablo oluşturur.

MySQL için Azure veritabanı, tek bir veri dosyasında en büyük, **4 TB**'yi destekler. Veritabanınızın boyutu 4 TB 'den büyükse, tabloyu [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tablo alanında oluşturmanız gerekir. 4 TB 'tan büyük tek bir tablo boyutunuz varsa, bölüm tablosunu kullanmanız gerekir.

### <a name="join_buffer_size"></a>join_buffer_size

Bu parametre hakkında daha fazla bilgi edinmek için [MySQL belgelerini](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) gözden geçirin.

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Varsayılan değer (bayt)**|**En az değer (bayt)**|**En büyük değer (bayt)**|
|---|---|---|---|---|
|Temel|1|Temel katmanda yapılandırılamaz|Yok|Yok|
|Temel|2|Temel katmanda yapılandırılamaz|Yok|Yok|
|Genel Amaçlı|2|262144|128|268435455|
|Genel Amaçlı|4|262144|128|536870912|
|Genel Amaçlı|8|262144|128|1073741824|
|Genel Amaçlı|16|262144|128|2147483648|
|Genel Amaçlı|32|262144|128|4294967295|
|Genel Amaçlı|64|262144|128|4294967295|
|Bellek İçin İyileştirilmiş|2|262144|128|536870912|
|Bellek İçin İyileştirilmiş|4|262144|128|1073741824|
|Bellek İçin İyileştirilmiş|8|262144|128|2147483648|
|Bellek İçin İyileştirilmiş|16|262144|128|4294967295|
|Bellek İçin İyileştirilmiş|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Varsayılan değer**|**En düşük değer**|**En yüksek değer**|
|---|---|---|---|---|
|Temel|1|50|10|50|
|Temel|2|100|10|100|
|Genel Amaçlı|2|300|10|600|
|Genel Amaçlı|4|625|10|1250|
|Genel Amaçlı|8|1250|10|2500|
|Genel Amaçlı|16|2500|10|5000|
|Genel Amaçlı|32|5000|10|10000|
|Genel Amaçlı|64|10000|10|20000|
|Bellek İçin İyileştirilmiş|2|625|10|1250|
|Bellek İçin İyileştirilmiş|4|1250|10|2500|
|Bellek İçin İyileştirilmiş|8|2500|10|5000|
|Bellek İçin İyileştirilmiş|16|5000|10|10000|
|Bellek İçin İyileştirilmiş|32|10000|10|20000|

Bağlantılar sınırı aştığında, şu hatayı alabilirsiniz:
> HATA 1040 (08004): çok fazla bağlantı

> [!IMPORTANT]
> En iyi deneyim için, bağlantıları verimli bir şekilde yönetmek üzere ProxySQL gibi bir bağlantı havuzlayıcı kullanmanızı öneririz.

MySQL 'e yeni istemci bağlantıları oluşturma zaman alır ve bir kez kurulduktan sonra bile bu bağlantılar veritabanı kaynaklarını kaplar. Çoğu uygulama, bu durumu çözer birçok kısa süreli bağlantı ister. Sonuç olarak gerçek iş yükünüz için daha az kaynak kullanılabilir ve performansı azaltıldı. Boştaki bağlantıları azaltan ve var olan bağlantıları yeniden kullanan bir bağlantı havuzlayıcı bunun önlenmesine yardımcı olur. ProxySQL 'i ayarlama hakkında bilgi edinmek için [Blog gönderimizi](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)ziyaret edin.

>[!Note]
>ProxySQL açık kaynaklı bir topluluk aracıdır. En iyi çaba temelinde Microsoft tarafından desteklenir. Yetkili kılavuzlarla üretime yönelik destek almak için, [Proxysql ürün desteğini](https://proxysql.com/services/support/)değerlendirebilir ve bunlarla iletişime ulaşabilirsiniz.

### <a name="max_heap_table_size"></a>max_heap_table_size

Bu parametre hakkında daha fazla bilgi edinmek için [MySQL belgelerini](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) gözden geçirin.

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Varsayılan değer (bayt)**|**En az değer (bayt)**|**En büyük değer (bayt)**|
|---|---|---|---|---|
|Temel|1|Temel katmanda yapılandırılamaz|Yok|Yok|
|Temel|2|Temel katmanda yapılandırılamaz|Yok|Yok|
|Genel Amaçlı|2|16777216|16384|268435455|
|Genel Amaçlı|4|16777216|16384|536870912|
|Genel Amaçlı|8|16777216|16384|1073741824|
|Genel Amaçlı|16|16777216|16384|2147483648|
|Genel Amaçlı|32|16777216|16384|4294967295|
|Genel Amaçlı|64|16777216|16384|4294967295|
|Bellek İçin İyileştirilmiş|2|16777216|16384|536870912|
|Bellek İçin İyileştirilmiş|4|16777216|16384|1073741824|
|Bellek İçin İyileştirilmiş|8|16777216|16384|2147483648|
|Bellek İçin İyileştirilmiş|16|16777216|16384|4294967295|
|Bellek İçin İyileştirilmiş|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

Sorgu önbelleği varsayılan olarak kapalıdır. Sorgu önbelleğini etkinleştirmek için `query_cache_type` parametresini yapılandırın. 

Bu parametre hakkında daha fazla bilgi edinmek için [MySQL belgelerini](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) gözden geçirin.

> [!NOTE]
> Sorgu önbelleği MySQL 5.7.20 yükle itibariyle kullanımdan kaldırılmıştır ve MySQL 8,0 ' de kaldırılmıştır

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Varsayılan değer (bayt)**|**En az değer (bayt)**|* * En büyük değer * *|
|---|---|---|---|---|
|Temel|1|Temel katmanda yapılandırılamaz|Yok|Yok|
|Temel|2|Temel katmanda yapılandırılamaz|Yok|Yok|
|Genel Amaçlı|2|0|0|16777216|
|Genel Amaçlı|4|0|0|33554432|
|Genel Amaçlı|8|0|0|67108864|
|Genel Amaçlı|16|0|0|134217728|
|Genel Amaçlı|32|0|0|134217728|
|Genel Amaçlı|64|0|0|134217728|
|Bellek İçin İyileştirilmiş|2|0|0|33554432|
|Bellek İçin İyileştirilmiş|4|0|0|67108864|
|Bellek İçin İyileştirilmiş|8|0|0|134217728|
|Bellek İçin İyileştirilmiş|16|0|0|134217728|
|Bellek İçin İyileştirilmiş|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

Lower_case_table_name varsayılan olarak 1 ' e ayarlanır ve MySQL 5,6 ve MySQL 5,7 ' de bu parametreyi güncelleştirebilirsiniz

Bu parametre hakkında daha fazla bilgi edinmek için [MySQL belgelerini](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names) gözden geçirin.

> [!NOTE]
> MySQL 8,0 ' de lower_case_table_name varsayılan olarak 1 ' e ayarlanır ve bunu değiştiremezsiniz.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

"Satır boyutu çok büyük (> 8126) şuna benzer bir hata alırsanız, **innodb_strict_mode** parametresini kapatmak isteyebilirsiniz. Satır veri boyutu 8k ' den daha büyükse, verilerin sunucu düzeyinde genel olarak değiştirilmesine izin verilmez, çünkü bu, veri kaybına neden olan bir hata olmadan veriler kesilir. **innodb_strict_mode** Şemayı sayfa boyutu sınırına uyacak şekilde değiştirmenizi öneririz. 

Bu parametre, kullanılarak bir oturum düzeyinde ayarlanabilir `init_connect` . **İnnodb_strict_mode** , oturum düzeyinde ayarlamak için, [listede bulunmayan ayar parametresi](./howto-server-parameters.md#setting-parameters-not-listed)' ne bakın.

> [!NOTE]
> Bir okuma Çoğaltma sunucunuz varsa, bir kaynak sunucuda oturum düzeyinde **innodb_strict_mode** olarak ayarlama, çoğaltmayı bozacaktır. Okuma çoğaltmalarınızı varsa parametre kümesinin kapalı kalmasını öneririz.

### <a name="sort_buffer_size"></a>sort_buffer_size

Bu parametre hakkında daha fazla bilgi edinmek için [MySQL belgelerini](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) gözden geçirin.

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Varsayılan değer (bayt)**|**En az değer (bayt)**|**En büyük değer (bayt)**|
|---|---|---|---|---|
|Temel|1|Temel katmanda yapılandırılamaz|Yok|Yok|
|Temel|2|Temel katmanda yapılandırılamaz|Yok|Yok|
|Genel Amaçlı|2|524288|32768|4194304|
|Genel Amaçlı|4|524288|32768|8388608|
|Genel Amaçlı|8|524288|32768|16777216|
|Genel Amaçlı|16|524288|32768|33554432|
|Genel Amaçlı|32|524288|32768|33554432|
|Genel Amaçlı|64|524288|32768|33554432|
|Bellek İçin İyileştirilmiş|2|524288|32768|8388608|
|Bellek İçin İyileştirilmiş|4|524288|32768|16777216|
|Bellek İçin İyileştirilmiş|8|524288|32768|33554432|
|Bellek İçin İyileştirilmiş|16|524288|32768|33554432|
|Bellek İçin İyileştirilmiş|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

Bu parametre hakkında daha fazla bilgi edinmek için [MySQL belgelerini](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) gözden geçirin.

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Varsayılan değer (bayt)**|**En az değer (bayt)**|**En büyük değer (bayt)**|
|---|---|---|---|---|
|Temel|1|Temel katmanda yapılandırılamaz|Yok|Yok|
|Temel|2|Temel katmanda yapılandırılamaz|Yok|Yok|
|Genel Amaçlı|2|16777216|1024|67108864|
|Genel Amaçlı|4|16777216|1024|134217728|
|Genel Amaçlı|8|16777216|1024|268435456|
|Genel Amaçlı|16|16777216|1024|536870912|
|Genel Amaçlı|32|16777216|1024|1073741824|
|Genel Amaçlı|64|16777216|1024|1073741824|
|Bellek İçin İyileştirilmiş|2|16777216|1024|134217728|
|Bellek İçin İyileştirilmiş|4|16777216|1024|268435456|
|Bellek İçin İyileştirilmiş|8|16777216|1024|536870912|
|Bellek İçin İyileştirilmiş|16|16777216|1024|1073741824|
|Bellek İçin İyileştirilmiş|32|16777216|1024|1073741824|

### <a name="innodb-buffer-pool-warmup"></a>InnoDB arabellek havuzu Warmup
MySQL sunucusu için Azure veritabanı 'nı yeniden başlattıktan sonra, tablolar sorgulandığında diskte bulunan veri sayfaları yüklenir. Bu, daha fazla gecikme süresi ve sorguların ilk yürütülmesi için daha yavaş performans sağlar. Bu, gecikme süresine duyarlı iş yükleri için kabul edilebilir olmayabilir. InnoDB arabellek havuzunun kullanıldığı ısınma, bir süre önce arabellek havuzunda bulunan disk sayfalarını DML veya belirli satırlara erişmek için işlem işlemleri yerine yeniden başlatmadan önce yeniden başlatarak ısınma süresini kısaltır.

[InnoDB arabellek havuzu sunucu parametrelerini](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)yapılandırarak bir performans avantajını temsil eden MySQL için Azure veritabanı sunucunuzu yeniden başlattıktan sonra ısınma süresini azaltabilirsiniz. InnoDB, sunucu kapatılmadan her bir arabellek havuzu için en son kullanılan sayfaların yüzdesini kaydeder ve bu sayfaları sunucu başlangıcında geri yükler.

Ayrıca, gelişmiş performansın sunucu için daha uzun başlangıç zamanı harcamasıyla birlikte geldiğini unutmayın. Bu parametre etkinleştirildiğinde sunucu başlatma ve yeniden başlatma süresinin, sunucuda sağlanan ıOPS 'ye bağlı olarak artması beklenir. Bu süre boyunca sunucu kullanılamadığından başlatma/yeniden başlatma performansının kabul edilebilir olmasını sağlamak için yeniden başlatma süresini test etmenizi ve izlemenizi öneririz. Sağlanan ıOPS 1000 ıOPS 'den (veya başka bir deyişle, sağlanan depolama alanı 335GB 'den az olduğunda) Bu parametrenin kullanılması önerilmez.

Sunucu kapatılmadan önce ayarlanan sunucu parametresi için arabellek havuzunun durumunu kaydetmek için `innodb_buffer_pool_dump_at_shutdown` `ON` . Benzer şekilde, sunucu `innodb_buffer_pool_load_at_startup` `ON` başlatma sırasında arabellek havuzu durumunu geri yüklemek için sunucu parametresini olarak ayarlayın. Başlangıç/yeniden başlatma üzerindeki etkiyi, sunucu parametresinin değerini indirerek ve ince ayar yaparak, `innodb_buffer_pool_dump_pct` Varsayılan olarak, bu parametrenin olarak ayarlandığını kontrol edebilirsiniz `25` .

> [!Note]
> InnoDB arabellek havuzu ısınma parametreleri yalnızca 16 TB 'a kadar depolama alanı bulunan genel amaçlı depolama sunucularında desteklenir. [MySQL Için Azure veritabanı depolama seçenekleri](./concepts-pricing-tiers.md#storage)hakkında daha fazla bilgi edinin.

### <a name="time_zone"></a>time_zone

İlk dağıtımdan sonra, MySQL için Azure Server, saat dilimi bilgileri için sistem tabloları içerir, ancak bu tablolar doldurulmaz. Saat dilimi tabloları, `mysql.az_load_timezone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan saklı yordam çağırarak doldurulabilirler. Saklı yordamı çağırma ve küresel veya oturum düzeyi saat dilimlerini ayarlama hakkında [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) veya [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) makalelerine bakın.

## <a name="non-configurable-server-parameters"></a>Yapılandırılamayan sunucu parametreleri

Aşağıdaki sunucu parametreleri hizmette yapılandırılamaz:

|**Parametre**|**Sabit değer**|
| :------------------------ | :-------- |
|Temel katmanda innodb_file_per_table|KAPALI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|6/E|
|innodb_log_files_in_group|2|

Burada listelenmeyen diğer değişkenler, varsayılan MySQL 'in hazır değerlere ayarlanır. Varsayılan değerler için [8,0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5,7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)ve [5,6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) sürümleri için MySQL belgelerine bakın. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal kullanarak sunucu parametrelerini yapılandırmayı](./howto-server-parameters.md) öğrenin
- [Azure CLI kullanarak sunucu parametrelerini yapılandırmayı](./howto-configure-server-parameters-using-cli.md) öğrenin
- [PowerShell kullanarak sunucu parametrelerini yapılandırmayı](./howto-configure-server-parameters-using-powershell.md) öğrenin
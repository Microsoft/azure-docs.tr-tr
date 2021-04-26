---
title: Sunucu parametreleri-MySQL için Azure veritabanı-esnek sunucu
description: Bu konu, MySQL için Azure veritabanı-esnek sunucu 'da sunucu parametrelerini yapılandırmaya ilişkin yönergeler sağlar.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: d64dc4f3c034279aee7401503bbb60883c9ed4e7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492248"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>MySQL için Azure veritabanı 'nda sunucu parametreleri-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu makalede, MySQL için Azure veritabanı esnek sunucusu 'nda sunucu parametrelerini yapılandırmaya yönelik konular ve yönergeler sağlanmaktadır.

## <a name="what-are-server-variables"></a>Sunucu değişkenleri nelerdir? 

MySQL altyapısı, altyapının davranışını yapılandırmak ve ayarlamak için kullanılabilecek birçok farklı [sunucu değişkeni/parametresi](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) sağlar. Bazı parametreler, çalışma zamanında dinamik olarak ayarlanabilir, diğerleri ise "statik" olarak ayarlanabilir, bu da bir sunucu yeniden başlatılmasını gerektirir.

MySQL için Azure veritabanı esnek sunucusu, iş yükünüzün ihtiyaçlarına uygun [Azure Portal](./how-to-configure-server-parameters-portal.md) ve [Azure CLI](./how-to-configure-server-parameters-cli.md) kullanarak çeşitli MySQL Server parametrelerinin değerini değiştirme özelliğini kullanıma sunar.

## <a name="configurable-server-parameters"></a>Yapılandırılabilir sunucu parametreleri

Sunucu parametrelerini kullanarak MySQL için Azure veritabanı esnek sunucu yapılandırması ' nı yönetebilirsiniz. Sunucuyu oluştururken sunucu parametreleri varsayılan ve önerilen değer ile yapılandırılır. Azure portal sunucu parametresi dikey penceresinde hem değiştirilebilir hem de değiştirilemeyen sunucu parametreleri gösterilmektedir. Değiştirilemeyen sunucu parametreleri gri değildir.

Desteklenen sunucu parametrelerinin listesi sürekli olarak büyüyordur. Tam listeyi görüntülemek ve sunucu parametreleri değerlerini yapılandırmak için Azure portal sunucu parametreleri sekmesini kullanın.

Yaygın olarak güncellenen çeşitli sunucu parametrelerinin sınırları hakkında daha fazla bilgi edinmek için aşağıdaki bölümlere bakın. Sınırlar, sunucunun işlem katmanına ve boyutuna (sanal çekirdekler) göre belirlenir.

> [!NOTE]
> Değiştirilemeyen ve ortamınız için değiştirilebilir olarak görmek istediğiniz bir sunucu parametresini değiştirmek istiyorsanız, lütfen bir UserVoice öğesi açın veya geri bildirim zaten mevcutsa, önceliklendirmemize yardımcı olabilecek bir [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) öğesi açın veya oylayın.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

MySQL için Azure veritabanı esnek sunucusu 'nda ikili Günlükler her zaman etkindir (yani, açık olarak `log_bin` ayarlanır). log_bin_trust_function_creators, esnek sunuculardaki varsayılan olarak açık olarak ayarlanır. 

İkili günlük biçimi her zaman **satırdır** ve sunucuya yapılan tüm bağlantılar **her zaman** satır tabanlı ikili günlük kullanır. Satır tabanlı ikili günlüğe kaydetme ile güvenlik sorunları yoktur ve ikili günlüğe kaydetme kesintiye uğramaz, bu sayede [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) **Açık** kalmasına izin verebilirsiniz.

[ `log_bin_trust_function_creators` ] Off olarak ayarlandıysa, Tetikleyiciler oluşturmaya çalışırsanız, *süper ayrıcalığa sahip değilsiniz ve ikili günlüğe kaydetme etkin olan bir hata alabilirsiniz (daha az güvenli bir değişken kullanmak isteyebilirsiniz `log_bin_trust_function_creators` )*. 

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Bu parametre hakkında daha fazla bilgi edinmek için [MySQL belgelerini](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) gözden geçirin.

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Bellek boyutu (GiB)**|**Varsayılan değer (bayt)**|**En az değer (bayt)**|**En büyük değer (bayt)**|
|---|---|---|---|---|---|
|Burstable (B1S)|1|1|134217728|33554432|134217728|
|Burstable (B1ms)|1|2|536870912|134217728|536870912|
|Burstable|2|4|2147483648|134217728|2147483648|
|Genel Amaçlı|2|8|6442450944|134217728|6442450944|
|Genel Amaçlı|4|16|12884901888|134217728|12884901888|
|Genel Amaçlı|8|32|25769803776|134217728|25769803776|
|Genel Amaçlı|16|64|51539607552|134217728|51539607552|
|Genel Amaçlı|32|128|103079215104|134217728|103079215104|
|Genel Amaçlı|48|192|154618822656|134217728|154618822656|
|Genel Amaçlı|64|256|206158430208|134217728|206158430208|
|Bellek İçin İyileştirilmiş|2|16|12884901888|134217728|12884901888|
|Bellek İçin İyileştirilmiş|4|32|25769803776|134217728|25769803776|
|Bellek İçin İyileştirilmiş|8|64|51539607552|134217728|51539607552|
|Bellek İçin İyileştirilmiş|16|128|103079215104|134217728|103079215104|
|Bellek İçin İyileştirilmiş|32|256|206158430208|134217728|206158430208|
|Bellek İçin İyileştirilmiş|48|384|309237645312|134217728|309237645312|
|Bellek İçin İyileştirilmiş|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL, InnoDB tablosunu tablo oluşturma sırasında verdiğiniz yapılandırmaya göre farklı Tablespaces halinde depolar. [Sistem tablo](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) alanı, InnoDB veri sözlüğü için depolama alanıdır. [Tablo başına dosya tablosu](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) , tek bir InnoDB tablosunun verilerini ve dizinlerini içerir ve dosya sisteminde kendi veri dosyasında depolanır. Bu davranış, `innodb_file_per_table` sunucu parametresi tarafından denetlenir. `innodb_file_per_table`İçin ayarı `OFF` , InnoDB 'in, sistem tablo tablosu 'nda tablo oluşturmasına neden olur. Aksi halde, InnoDB tablo başına tabloalanları içinde tablo oluşturur.

MySQL için Azure veritabanı esnek sunucu, tek bir veri dosyasında en büyük, **4 TB**'yi destekler. Veritabanınızın boyutu 4 TB 'den büyükse, tabloyu [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tablo alanında oluşturmanız gerekir. 4 TB 'tan büyük tek bir tablo boyutunuz varsa, bölüm tablosunu kullanmanız gerekir.

### <a name="max_connections"></a>max_connections

Max_connection değeri sunucunun bellek boyutuna göre belirlenir. 

|**Fiyatlandırma Katmanı**|**Sanal çekirdek**|**Bellek boyutu (GiB)**|**Varsayılan değer**|**En düşük değer**|**En yüksek değer**|
|---|---|---|---|---|---|
|Burstable (B1S)|1|1|85|10|171|
|Burstable (B1ms)|1|2|171|10|341|
|Burstable|2|4|341|10|683|
|Genel Amaçlı|2|8|683|10|1365|
|Genel Amaçlı|4|16|1365|10|2731|
|Genel Amaçlı|8|32|2731|10|5461|
|Genel Amaçlı|16|64|5461|10|10923|
|Genel Amaçlı|32|128|10923|10|21845|
|Genel Amaçlı|48|192|16384|10|32768|
|Genel Amaçlı|64|256|21845|10|43691|
|Bellek İçin İyileştirilmiş|2|16|1365|10|2731|
|Bellek İçin İyileştirilmiş|4|32|2731|10|5461|
|Bellek İçin İyileştirilmiş|8|64|5461|10|10923|
|Bellek İçin İyileştirilmiş|16|128|10923|10|21845|
|Bellek İçin İyileştirilmiş|32|256|21845|10|43691|
|Bellek İçin İyileştirilmiş|48|384|32768|10|65536|
|Bellek İçin İyileştirilmiş|64|504|43008|10|86016|

Bağlantılar sınırı aştığında, şu hatayı alabilirsiniz:
> HATA 1040 (08004): çok fazla bağlantı

> [!IMPORTANT]
> En iyi deneyim için, bağlantıları verimli bir şekilde yönetmek üzere ProxySQL gibi bir bağlantı havuzlayıcı kullanmanızı öneririz.

MySQL 'e yeni istemci bağlantıları oluşturma zaman alır ve bir kez kurulduktan sonra bile bu bağlantılar veritabanı kaynaklarını kaplar. Çoğu uygulama, bu durumu çözer birçok kısa süreli bağlantı ister. Sonuç olarak gerçek iş yükünüz için daha az kaynak kullanılabilir ve performansı azaltıldı. Boştaki bağlantıları azaltan ve var olan bağlantıları yeniden kullanan bir bağlantı havuzlayıcı bunun önlenmesine yardımcı olur. ProxySQL 'i ayarlama hakkında bilgi edinmek için [Blog gönderimizi](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)ziyaret edin.

>[!Note]
>ProxySQL açık kaynaklı bir topluluk aracıdır. En iyi çaba temelinde Microsoft tarafından desteklenir. Yetkili kılavuzlarla üretime yönelik destek almak için, [Proxysql ürün desteğini](https://proxysql.com/services/support/)değerlendirebilir ve bunlarla iletişime ulaşabilirsiniz.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

"Satır boyutu çok büyük (> 8126)" benzeri bir hata alırsanız, **innodb_strict_mode** parametresini kapatmak isteyebilirsiniz. **İnnodb_strict_mode** sunucu parametresi, satır veri boyutu 8k ' den büyükse, veriler hata olmadan kesilir, bu da olası veri kaybına yol açabilir. Şemayı sayfa boyutu sınırına uyacak şekilde değiştirmenizi öneririz. 

Bu parametre, kullanılarak bir oturum düzeyinde ayarlanabilir `init_connect` . **İnnodb_strict_mode** , oturum düzeyinde ayarlamak için, [listede bulunmayan ayar parametresi](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters)' ne bakın.

> [!NOTE]
> Bir okuma Çoğaltma sunucunuz varsa, bir kaynak sunucuda oturum düzeyinde **innodb_strict_mode** olarak ayarlama, çoğaltmayı bozacaktır. Okuma çoğaltmalarınızı varsa parametre kümesinin kapalı kalmasını öneririz.

### <a name="time_zone"></a>time_zone

İlk dağıtımdan sonra MySQL için Azure esnek sunucusu, saat dilimi bilgileri için sistem tabloları içerir, ancak bu tablolar doldurulmaz. Saat dilimi tabloları, `mysql.az_load_timezone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan saklı yordam çağırarak doldurulabilirler. Saklı yordamı çağırma ve küresel veya oturum düzeyi saat dilimlerini ayarlama hakkında [Azure Portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) veya [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) makalelerine bakın.

## <a name="non-modifiable-server-parameters"></a>Değiştirilemeyen sunucu parametreleri

Azure portal sunucu parametresi dikey penceresinde hem değiştirilebilir hem de değiştirilemeyen sunucu parametreleri gösterilmektedir. Değiştirilemeyen sunucu parametreleri gri değildir. Oturum düzeyinde değiştirilemeyen bir sunucu parametresi yapılandırmak istiyorsanız, kullanarak bağlantı düzeyindeki parametreyi ayarlamak için [Azure Portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) veya [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) makalesine başvurun `init_connect` .

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal 'da sunucu parametrelerini](./how-to-configure-server-parameters-portal.md) yapılandırma
- [Azure CLI 'da sunucu parametrelerini](./how-to-configure-server-parameters-cli.md) yapılandırma

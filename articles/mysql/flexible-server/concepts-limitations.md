---
title: Sınırlamalar-MySQL için Azure veritabanı-esnek sunucu
description: Bu makalede, MySQL için Azure veritabanı 'nda bağlantı ve depolama motoru seçeneklerinin sayısı gibi sınırlamalar açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 48aef337326d58b2a503dc48862571efde0d37ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034529"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>MySQL için Azure veritabanı 'nda sınırlamalar-esnek sunucu (Önizleme)

> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu makalede MySQL için Azure veritabanı esnek sunucu hizmeti kısıtlamaları açıklanmaktadır. MySQL veritabanı altyapısındaki [genel sınırlamalar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) da geçerlidir. Kaynak (işlem, bellek, depolama) katmanları hakkında bilgi edinmek istiyorsanız, bkz. [işlem ve depolama](concepts-compute-storage.md) makalesi.

## <a name="server-parameters"></a>Sunucu parametreleri

> [!NOTE]
> Ve gibi sunucu parametreleri için Min/Max değerlerini arıyorsanız `max_connections` `innodb_buffer_pool_size` , bu bilgiler sunucu parametreleri kavramlar [sunucu parametreleri](./concepts-server-parameters.md) makalesine taşınır.

MySQL için Azure veritabanı, sunucu parametrelerinin değerlerini ayarlamayı destekler. Bazı parametrelerin en küçük ve en büyük değeri (örn. `max_connections`, `join_buffer_size` , `query_cache_size` ) sunucunun işlem katmanına ve işlem boyutuna göre belirlenir. Bu sınırlar hakkında daha fazla bilgi için [sunucu parametrelerine](./concepts-server-parameters.md) bakın.

"Validate_password" ve "caching_sha2_password" gibi parola eklentileri hizmet tarafından desteklenmez.

## <a name="storage-engines"></a>Depolama motorları

MySQL birçok depolama altyapısını destekler. MySQL için Azure veritabanı esnek sunucusu 'nda aşağıdaki depolama motorları desteklenir ve desteklenmez:

### <a name="supported"></a>Desteklenir
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [BELLEK](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Desteklenmeyen
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [KARA DELIK](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARŞIVLIYORSANıZ](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [Federasyon](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Veri işleme desteğini & ayrıcalıklar

Çok sayıda sunucu parametresi ve ayarı, MySQL sunucusunun sunucu performansını veya Negate ACID özelliklerini yanlışlıkla düşürebilir. Hizmet bütünlüğünü ve SLA 'yı bir ürün düzeyinde sürdürmek için, bu hizmet birden çok rol sunmaz. 

MySQL hizmeti, temel alınan dosya sistemine doğrudan erişime izin vermez. Bazı veri işleme komutları desteklenmez. 

### <a name="unsupported"></a>Desteklenmeyen

Aşağıdakiler desteklenmez:
- DBA rolü: kısıtlı. Alternatif olarak, yönetici kullanıcıyı kullanabilirsiniz (yeni sunucu oluşturma sırasında oluşturulur), DDL ve DML deyimlerinin çoğunu gerçekleştirmenize olanak tanır. 
- Süper ayrıcalık: benzer şekilde, [süper ayrıcalık](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) kısıtlıdır.
- DEFINER: oluşturmak için süper ayrıcalıklar gerektirir ve kısıtlıdır. Bir yedekleme kullanarak veri içeri aktardıysanız, `CREATE DEFINER` komutları el ile veya `--skip-definer` bir mysqldump gerçekleştirirken komutunu kullanarak kaldırın.
- Sistem veritabanları: [MySQL sistem veritabanı](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) salt okunurdur ve çeşitli PaaS işlevlerini desteklemek için kullanılır. `mysql`Sistem veritabanında değişiklik yapamazsınız.
- `SELECT ... INTO OUTFILE`: Hizmette desteklenmiyor.

### <a name="supported"></a>Desteklenir
- `LOAD DATA INFILE` desteklenir, ancak `[LOCAL]` parametresi belirtilmelidir ve bır UNC yoluna (SMB üzerinden bağlanmış Azure Storage) yönlendirilmelidir.

## <a name="functional-limitations"></a>İşlevsel sınırlamalar

### <a name="zone-redundant-ha"></a>Bölge yedekli HA
- Bu yapılandırma, yalnızca sunucu oluşturma sırasında ayarlanabilir.
- Burstable işlem katmanında desteklenmez.

### <a name="networking"></a>Ağ
- Sunucu oluşturulduktan sonra bağlantı yöntemi değiştirilemez. Sunucu *özel erişim (VNET tümleştirmesi)* ile oluşturulduysa, oluşturma Işleminden sonra *ortak erişim (izin verilen IP adresleri)* olarak değiştirilemez ve bunun tersi de geçerlidir
- TLS/SSL varsayılan olarak etkindir ve devre dışı bırakılamaz.
- Sunucuda desteklenen minimum TLS sürümü TLS 1.2. Daha fazla bilgi edinmek için [TLS/SSL kullanarak bağlanma](./how-to-connect-tls-ssl.md) bölümüne bakın.

### <a name="stopstart-operation"></a>İşlemi Durdur/Başlat
- Bölge yedekli HA yapılandırmalarında (birincil ve bekleme) desteklenmez.
- Okuma çoğaltması yapılandırmalarında (kaynak ve çoğaltmalar) desteklenmez.

### <a name="scale-operations"></a>Ölçeklendirme işlemleri
- Sağlanan sunucu depolama alanı azaltma desteklenmiyor.

### <a name="read-replicas"></a>Okuma amaçlı çoğaltmalar
- Bölge yedekli HA yapılandırmalarında (birincil ve bekleme) desteklenmez.

### <a name="server-version-upgrades"></a>Sunucu sürümü yükseltmeleri
- Ana veritabanı altyapısı sürümleri arasında otomatik geçiş desteklenmez. Ana sürümü yükseltmek isterseniz, bir dökümünü alın ve yeni altyapı sürümüyle oluşturulmuş bir sunucuya [geri yükleyin](../concepts-migrate-dump-restore.md) .

### <a name="restoring-a-server"></a>Bir sunucuyu geri yükleme
- Noktadan itibaren geri yükleme ile, yeni sunucular, temel aldığı kaynak sunucuyla aynı işlem ve depolama yapılandırmasıyla oluşturulur. Yeni geri yüklenen sunucunun işlemi, sunucu oluşturulduktan sonra aşağı ölçeklendirilebilir.
- Silinen bir sunucunun geri yüklenmesi desteklenmez.

## <a name="features-available-in-single-server-but-not-yet-supported-in-flexible-server"></a>Tek bir sunucuda kullanılabilen ancak esnek sunucuda henüz desteklenmeyen özellikler 
MySQL için Azure veritabanı 'nda tüm özellikler mevcut değildir-tek sunucu, esnek sunucuda henüz kullanılabilir. Tek sunucu ve esnek sunucu arasındaki özellik karşılaştırmasının tüm listesi için, [Azure belgelerindeki doğru MySQL sunucusu seçeneğini belirleme](../select-right-deployment-type.md#comparing-the-mysql-deployment-options-in-azure) konusuna bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure belgelerindeki doğru MySQL sunucusu seçeneğini belirleyin](../select-right-deployment-type.md)
- [Esnek sunucu 'da işlem ve depolama seçeneklerinde nelerin kullanılabildiğini](concepts-compute-storage.md) anlayın
- [Desteklenen MySQL sürümleri](concepts-supported-versions.md) hakkında bilgi edinin
- Hızlı başlangıç: [MySQL Için Azure veritabanı esnek sunucusu oluşturmak için Azure Portal kullanma](quickstart-create-server-portal.md)

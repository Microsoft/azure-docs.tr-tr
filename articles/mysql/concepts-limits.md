---
title: Sınırlamalar-MySQL için Azure veritabanı
description: Bu makalede, MySQL için Azure veritabanı 'nda bağlantı ve depolama motoru seçeneklerinin sayısı gibi sınırlamalar açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 9b18b24686908ac92f97ea0cae892369919ae4d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721029"
---
# <a name="limitations-in-azure-database-for-mysql"></a>MySQL için Azure veritabanı sınırlamaları
Aşağıdaki bölümlerde kapasiteyi, depolama altyapısı desteğini, ayrıcalık desteğini, veri işleme ekstresi desteğini ve veritabanı hizmetindeki işlev sınırlarını anlatmaktadır. Ayrıca bkz. MySQL veritabanı altyapısı için geçerli olan [genel sınırlamalar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) .

## <a name="server-parameters"></a>Sunucu parametreleri

> [!NOTE]
> Ve gibi sunucu parametreleri için Min/Max değerlerini arıyorsanız `max_connections` `innodb_buffer_pool_size` , bu bilgiler **[sunucu parametreleri](./concepts-server-parameters.md)** makalesine taşınır.

MySQL için Azure veritabanı, sunucu parametrelerinin değerlerini ayarlamayı destekler. Bazı parametrelerin en küçük ve en büyük değeri (örn. `max_connections`, `join_buffer_size` , `query_cache_size` ), sunucunun fiyatlandırma katmanı ve sanal çekirdekleri tarafından belirlenir. Bu sınırlar hakkında daha fazla bilgi için [sunucu parametrelerine](./concepts-server-parameters.md) bakın.

İlk dağıtımdan sonra, MySQL için Azure Server, saat dilimi bilgileri için sistem tabloları içerir, ancak bu tablolar doldurulmaz. Saat dilimi tabloları, `mysql.az_load_timezone` MySQL komut satırı veya MySQL çalışma ekranı gibi bir araçtan saklı yordam çağırarak doldurulabilirler. Saklı yordamı çağırma ve küresel veya oturum düzeyi saat dilimlerini ayarlama hakkında [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) veya [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) makalelerine bakın.

"Validate_password" ve "caching_sha2_password" gibi parola eklentileri hizmet tarafından desteklenmez.

## <a name="storage-engines"></a>Depolama motorları

MySQL birçok depolama altyapısını destekler. MySQL için Azure veritabanı 'nda aşağıdaki depolama motorları desteklenir ve desteklenmez:

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
- `LOAD_FILE(file_name)`: Hizmette desteklenmiyor.

### <a name="supported"></a>Desteklenir
- `LOAD DATA INFILE` desteklenir, ancak `[LOCAL]` parametresi belirtilmelidir ve bır UNC yoluna (SMB üzerinden bağlanmış Azure Storage) yönlendirilmelidir.

## <a name="functional-limitations"></a>İşlevsel sınırlamalar

### <a name="scale-operations"></a>Ölçeklendirme işlemleri
- Temel fiyatlandırma katmanlarına ve bu katmanlara dinamik ölçeklendirme Şu anda desteklenmiyor.
- Sunucu depolama boyutunun düşürülmesi desteklenmiyor.

### <a name="server-version-upgrades"></a>Sunucu sürümü yükseltmeleri
- Ana veritabanı altyapısı sürümleri arasında otomatik geçiş Şu anda desteklenmiyor. Bir sonraki ana sürüme yükseltmek isterseniz, bir dökümünü alın ve yeni altyapı sürümüyle oluşturulmuş bir sunucuya [geri yükleyin](./concepts-migrate-dump-restore.md) .

### <a name="point-in-time-restore"></a>belirli bir noktaya geri yükleme
- INR özelliği kullanılırken yeni sunucu, temel aldığı sunucuyla aynı yapılandırmalara sahip olarak oluşturulur.
- Silinen bir sunucunun geri yüklenmesi desteklenmez.

### <a name="vnet-service-endpoints"></a>Sanal ağ hizmet uç noktaları
- VNet hizmet uç noktaları için destek yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş sunucular içindir.

### <a name="storage-size"></a>Depolama boyutu
- Fiyatlandırma Katmanı başına depolama boyutu sınırları için lütfen [fiyatlandırma katmanlarına](concepts-pricing-tiers.md) bakın.

## <a name="current-known-issues"></a>Bilinen geçerli sorunlar
- MySQL Server örneği bağlantı kurulduktan sonra yanlış sunucu sürümünü görüntülüyor. Doğru sunucu örneği altyapısı sürümünü almak için `select version();` komutunu kullanın.

## <a name="next-steps"></a>Sonraki adımlar
- [Her hizmet katmanında kullanılabilen özellikler](concepts-pricing-tiers.md)
- [Desteklenen MySQL veritabanı sürümleri](concepts-supported-versions.md)

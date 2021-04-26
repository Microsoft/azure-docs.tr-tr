---
title: Gelen Verileri Çoğaltma-MySQL için Azure veritabanı
description: Bir dış sunucudan MySQL için Azure veritabanı hizmetine eşitlenmek üzere Gelen Verileri Çoğaltma kullanma hakkında bilgi edinin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 83ad2d4f392afb6bb33c99a5449b9bc8ceeaa058
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312816"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'na veri çoğaltma

Gelen Verileri Çoğaltma, bir dış MySQL sunucusundan verileri MySQL için Azure veritabanı hizmetine eşitlemenize olanak tanır. Dış sunucu şirket içinde, sanal makinelerde veya diğer bulut sağlayıcıları tarafından barındırılan bir veritabanı hizmeti olabilir. Gelen Verileri Çoğaltma ikili günlük (binlog) dosya konumu tabanlı veya GTıD tabanlı çoğaltmayı MySQL 'e göre temel alır. Binlog çoğaltma hakkında daha fazla bilgi edinmek için [MySQL binlog çoğaltmasına genel bakış](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)bölümüne bakın.

## <a name="when-to-use-data-in-replication"></a>Ne zaman kullanılacağı Gelen Verileri Çoğaltma

Gelen Verileri Çoğaltma kullanmayı göz önünde bulundurmanız gereken temel senaryolar şunlardır:

- **Karma veri eşitleme:** Gelen Verileri Çoğaltma ile, şirket içi sunucularınız ve MySQL için Azure veritabanı arasında verileri eşitlenmiş halde tutabilirsiniz. Bu eşitleme, karma uygulamalar oluşturmak için kullanışlıdır. Bu yöntem, var olan bir yerel veritabanı sunucunuz olduğunda ancak verileri son kullanıcılara yakın bir bölgeye taşımak istediğinizde çarpıcı olur.
- **Çoklu bulut eşitlemesi:** Karmaşık bulut çözümleri için, MySQL için Azure veritabanı ve bu bulutlarda barındırılan sanal makineler ve veritabanı hizmetleri dahil farklı bulut sağlayıcıları arasında veri eşitlemesi için Gelen Verileri Çoğaltma kullanın.

Geçiş senaryoları için, [Azure veritabanı geçiş hizmeti](https://azure.microsoft.com/services/database-migration/)'ni (DMS) kullanın.

## <a name="limitations-and-considerations"></a>Sınırlamalar ve önemli noktalar

### <a name="data-not-replicated"></a>Çoğaltılan veriler

Kaynak sunucudaki [*MySQL sistem veritabanı*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) çoğaltılmaz. Ayrıca, kaynak sunucudaki hesaplar ve izinler üzerinde yapılan değişiklikler çoğaltılmaz. Kaynak sunucuda bir hesap oluşturursanız ve bu hesabın çoğaltma sunucusuna erişmesi gerekiyorsa, Çoğaltma sunucusunda aynı hesabı el ile oluşturun. Hangi tabloların sistem veritabanına dahil olduğunu anlamak için [MySQL kılavuzuna](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)bakın.

### <a name="filtering"></a>Filtreleme

Kaynak sunucunuzdaki (Şirket içi, sanal makinelerde barındırılan veya diğer bulut sağlayıcıları tarafından barındırılan bir veritabanı hizmetindeki) tabloları çoğaltmayı atlamak için, `replicate_wild_ignore_table` parametresi desteklenir. İsteğe bağlı olarak, [Azure Portal](howto-server-parameters.md) veya [Azure CLI](howto-configure-server-parameters-using-cli.md)kullanarak Azure 'da barındırılan Çoğaltma sunucusunda bu parametreyi güncelleştirin.

Bu parametre hakkında daha fazla bilgi edinmek için [MySQL belgelerini](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table)gözden geçirin.

## <a name="supported-in-general-purpose-or-memory-optimized-tier-only"></a>Yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş katmanda desteklenir

Gelen Verileri Çoğaltma yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında desteklenir.

### <a name="requirements"></a>Gereksinimler

- Kaynak sunucu sürümü en az MySQL sürüm 5,6 olmalıdır.
- Kaynak ve çoğaltma sunucusu sürümleri aynı olmalıdır. Örneğin, her ikisi de MySQL sürüm 5,6 olmalıdır veya her ikisi de MySQL sürüm 5,7 olmalıdır.
- Her tablo bir birincil anahtara sahip olmalıdır.
- Kaynak sunucu MySQL InnoDB altyapısını kullanmalıdır.
- Kullanıcının, ikili günlüğü yapılandırma ve kaynak sunucuda yeni kullanıcılar oluşturma izinlerine sahip olması gerekir.
- Kaynak sunucuda SSL etkinse, etki alanı için sağlanan SSL CA sertifikasının `mysql.az_replication_change_master` veya saklı yordama eklendiğinden emin olun `mysql.az_replication_change_master_with_gtid` . Aşağıdaki [örneklere](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) ve `master_ssl_ca` parametresine bakın.
- Kaynak sunucunun IP adresinin MySQL Çoğaltma sunucusunun güvenlik duvarı kuralları için Azure veritabanı 'na eklendiğinden emin olun. [Azure portalını](./howto-manage-firewall-using-portal.md) veya [Azure CLI](./howto-manage-firewall-using-cli.md)’yı kullanarak güvenlik duvarı kurallarını güncelleştirin.
- Kaynak sunucuyu barındıran makinenin 3306 numaralı bağlantı noktasında hem gelen hem de giden trafiğe izin verdiğinden emin olun.
- Kaynak sunucunun **Genel BIR IP adresine** sahip olduğundan, DNS 'nin genel olarak erişilebilir olduğundan veya kaynak sunucunun tam etki alanı adı (FQDN) olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Verileri çoğaltmayı ayarlamayı](howto-data-in-replication.md) öğrenin
- [Azure 'da okuma çoğaltmalarıyla çoğaltma](concepts-read-replicas.md) hakkında bilgi edinin
- [DMS kullanarak verileri en az kapalı kalma süresiyle geçirme](howto-migrate-online.md) hakkında bilgi edinin

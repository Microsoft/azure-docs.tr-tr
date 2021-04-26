---
title: Veri çoğaltma-MariaDB için Azure veritabanı
description: Bir dış sunucudan MariaDB hizmeti için Azure veritabanı ile eşitleme yapmak üzere verileri çoğaltma hakkında bilgi edinin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662636"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'na veri çoğaltma

Gelen Verileri Çoğaltma, şirket içinde, sanal makinelerde veya diğer bulut sağlayıcıları tarafından barındırılan veritabanı hizmetlerinde çalışan bir MariaDB sunucusundan, MariaDB için Azure Veritabanı hizmetine verileri eşitlemenize olanak sağlar. Gelen Verileri Çoğaltma, MariaDB’de yerel olan ikili günlük (binlog) dosya konumuna dayalı çoğaltmayı temel alır. Binlog çoğaltma hakkında daha fazla bilgi edinmek için bkz. [binlog Çoğaltmaya genel bakış](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Ne zaman kullanılacağı Gelen Verileri Çoğaltma
Gelen Verileri Çoğaltma kullanmayı göz önünde bulundurmanız gereken temel senaryolar şunlardır:

- **Karma veri eşitleme:** Gelen Verileri Çoğaltma ile, verileri şirket içi sunucularınız ve MariaDB için Azure veritabanı arasında eşitlenmiş durumda tutabilirsiniz. Bu eşitleme, karma uygulamalar oluşturmak için kullanışlıdır. Bu yöntem, var olan bir yerel veritabanı sunucusuna sahip olduğunuzda ve verileri son kullanıcılara yakın bir bölgeye taşımak istediğinizde çarpıcı olur.
- **Çoklu bulut eşitlemesi:** Karmaşık bulut çözümleri için, bu bulutlarda barındırılan sanal makineler ve veritabanı hizmetleri dahil olmak üzere MariaDB için Azure veritabanı ve farklı bulut sağlayıcıları arasında veri eşitlemesi için Gelen Verileri Çoğaltma kullanın.

## <a name="limitations-and-considerations"></a>Sınırlamalar ve önemli noktalar

### <a name="data-not-replicated"></a>Çoğaltılan veriler
Kaynak sunucudaki [*MySQL sistem veritabanı*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) çoğaltılmaz. Kaynak sunucudaki hesaplar ve izinler üzerinde yapılan değişiklikler çoğaltılmaz. Kaynak sunucuda bir hesap oluşturursanız ve bu hesabın çoğaltma sunucusuna erişmesi gerekiyorsa, çoğaltma sunucusu tarafında el ile aynı hesabı oluşturun. Hangi tabloların sistem veritabanına dahil olduğunu anlamak için, [MariaDB belgelerine](https://mariadb.com/kb/en/library/the-mysql-database-tables/)bakın.

### <a name="requirements"></a>Gereksinimler
- Kaynak sunucu sürümü en azından MariaDB sürüm 10,2 olmalıdır.
- Kaynak ve çoğaltma sunucusu sürümleri aynı olmalıdır. Örneğin, her ikisi de MariaDB sürüm 10,2 olmalıdır.
- Her tablo bir birincil anahtara sahip olmalıdır.
- Kaynak sunucu InnoDB altyapısını kullanmalıdır.
- Kullanıcının, ikili günlüğü yapılandırma ve kaynak sunucuda yeni kullanıcılar oluşturma izinlerine sahip olması gerekir.
- Kaynak sunucuda SSL etkinse, etki alanı için sağlanan SSL CA sertifikasının saklı yordama eklendiğinden emin olun `mariadb.az_replication_change_master` . Aşağıdaki [örneklere](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) ve `master_ssl_ca` parametresine bakın.
- Kaynak sunucunun IP adresinin, MariaDB çoğaltma sunucusunun güvenlik duvarı kuralları için Azure veritabanı 'na eklendiğinden emin olun. [Azure portalını](howto-manage-firewall-portal.md) veya [Azure CLI](howto-manage-firewall-cli.md)’yı kullanarak güvenlik duvarı kurallarını güncelleştirin.
- Kaynak sunucuyu barındıran makinenin 3306 numaralı bağlantı noktasında hem gelen hem de giden trafiğe izin verdiğinden emin olun.
- Kaynak sunucunun **Genel BIR IP adresi** olduğundan, DNS 'nin genel olarak erişilebilir olduğundan veya tam etki alanı adı (FQDN) olduğundan emin olun.

### <a name="other"></a>Diğer
- Veri içi çoğaltma yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında desteklenir.

## <a name="next-steps"></a>Sonraki adımlar
- [Verileri çoğaltmanın nasıl ayarlanacağını](howto-data-in-replication.md)öğrenin.

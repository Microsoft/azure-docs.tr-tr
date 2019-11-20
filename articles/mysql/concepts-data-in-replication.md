---
title: MySQL için Azure veritabanı 'na veri çoğaltma
description: Bir dış sunucudan MySQL için Azure veritabanı hizmetine eşitleme yapmak üzere verileri çoğaltma ile ilgili bilgi edinin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: b501a1f1ea54aff5617932dc5085d6d19f86976c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970363"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'na veri çoğaltma

Gelen Verileri Çoğaltma, bir dış MySQL sunucusundan verileri MySQL için Azure veritabanı hizmetine eşitlemenize olanak tanır. Dış sunucu şirket içinde, sanal makinelerde veya diğer bulut sağlayıcıları tarafından barındırılan bir veritabanı hizmeti olabilir. Gelen Verileri Çoğaltma, MySQL’de yerel olan ikili günlük (binlog) dosya konumuna dayalı çoğaltmayı temel alır. Binlog çoğaltma hakkında daha fazla bilgi edinmek için [MySQL binlog çoğaltmasına genel bakış](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)bölümüne bakın. 

## <a name="when-to-use-data-in-replication"></a>Ne zaman kullanılacağı Gelen Verileri Çoğaltma
Gelen Verileri Çoğaltma kullanmayı göz önünde bulundurmanız gereken temel senaryolar şunlardır:

- **Karma veri eşitleme:** Gelen Verileri Çoğaltma ile, şirket içi sunucularınız ve MySQL için Azure veritabanı arasında verileri eşitlenmiş halde tutabilirsiniz. Bu eşitleme, karma uygulamalar oluşturmak için kullanışlıdır. Bu yöntem, var olan bir yerel veritabanı sunucunuz olduğunda ancak verileri son kullanıcılara yakın bir bölgeye taşımak istediğinizde çarpıcı olur.
- **Çoklu bulut eşitlemesi:** Karmaşık bulut çözümleri için, MySQL için Azure veritabanı ve bu bulutlarda barındırılan sanal makineler ve veritabanı hizmetleri dahil farklı bulut sağlayıcıları arasında veri eşitlemesi için Gelen Verileri Çoğaltma kullanın.
 
Geçiş senaryoları için, [Azure veritabanı geçiş hizmeti](https://azure.microsoft.com/services/database-migration/)'ni (DMS) kullanın.

## <a name="limitations-and-considerations"></a>Sınırlamalar ve önemli noktalar

### <a name="data-not-replicated"></a>Çoğaltılan veriler
Ana sunucudaki [*MySQL sistem veritabanı*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) çoğaltılmaz. Ana sunucudaki hesaplar ve izinler üzerinde yapılan değişiklikler çoğaltılmaz. Ana sunucuda bir hesap oluşturursanız ve bu hesabın çoğaltma sunucusuna erişmesi gerekiyorsa, çoğaltma sunucusu tarafında el ile aynı hesabı oluşturun. Hangi tabloların sistem veritabanına dahil olduğunu anlamak için [MySQL kılavuzuna](https://dev.mysql.com/doc/refman/5.7/en/system-database.html)bakın.

### <a name="requirements"></a>Gereksinimler
- Ana sunucu sürümü en az MySQL sürüm 5,6 olmalıdır. 
- Ana sunucu ve çoğaltma sunucusu sürümleri aynı olmalıdır. Örneğin, her ikisi de MySQL sürüm 5,6 olmalıdır veya her ikisi de MySQL sürüm 5,7 olmalıdır.
- Her tablo bir birincil anahtara sahip olmalıdır.
- Ana sunucu MySQL InnoDB altyapısını kullanmalıdır.
- Kullanıcının, ikili günlüğü yapılandırma ve ana sunucuda yeni kullanıcılar oluşturma izinlerine sahip olması gerekir.
- Ana sunucuda SSL etkinse, etki alanı için sağlanan SSL CA sertifikasının `mysql.az_replication_change_master` saklı yordamına eklendiğinden emin olun. Aşağıdaki [örneklere](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) ve `master_ssl_ca` parametresine bakın.
- Ana sunucunun IP adresinin MySQL için Azure Veritabanı çoğaltma sunucusunun güvenlik duvarı kurallarına eklendiğinden emin olun. [Azure portalını](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) veya [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli)’yı kullanarak güvenlik duvarı kurallarını güncelleştirin.
- Ana sunucuyu barındıran makinenin, 3306 numaralı bağlantı noktasında hem gelen hem de giden trafiğe izin verdiğinden emin olun.
- Ana sunucunun **Genel BIR IP adresi**olduğundan, DNS 'nin genel olarak erişilebilir olduğundan veya tam etki alanı adı (FQDN) olduğundan emin olun.

### <a name="other"></a>Diğer
- Veri içi çoğaltma yalnızca Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında desteklenir.
- Genel işlem tanımlayıcıları (GTıD) desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar
- [Verileri çoğaltmayı ayarlamayı](howto-data-in-replication.md) öğrenin
- [Azure 'da okuma çoğaltmalarıyla çoğaltma](concepts-read-replicas.md) hakkında bilgi edinin
- [DMS kullanarak verileri en az kapalı kalma süresiyle geçirme](howto-migrate-online.md) hakkında bilgi edinin
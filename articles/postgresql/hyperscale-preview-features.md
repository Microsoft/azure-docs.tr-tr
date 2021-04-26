---
title: PostgreSQL için Azure veritabanı 'nda Önizleme özellikleri-hiper ölçek (Citus)
description: Şu anda önizleme aşamasında olan özelliklerin güncelleştirilmiş listesi
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 5f0552e09464a6b571a665cfe3895f48c3aa8c41
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258461"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>PostgreSQL için Önizleme özellikleri-hiper ölçek (Citus)

PostgreSQL için Azure veritabanı-Hyperscale (Citus), yayımlanmayan özellikler için önizleme sağlar. Önizleme sürümleri, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.  Daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="features-currently-in-preview"></a>Şu anda önizlemede olan özellikler

Şu anda önizleme için kullanılabilen özellikler şunlardır:

* **[Temel katman](concepts-hyperscale-tiers.md)**. Yalnızca bir düzenleyici düğümü ve çalışan düğümü kullanarak bir sunucu grubu çalıştırın. İlk test ve geliştirme yapmanın ekonomik bir yolu ve küçük üretim iş yüklerini işleyin.
* **[PostgreSQL 12 ve 13](concepts-hyperscale-versions.md)**.
  Sunucu grubunuzda en son veritabanı sürümünü kullanın.
* **[Citus 10](concepts-hyperscale-versions.md#citus-and-other-extension-versions)**.
  PostgreSQL 13 çalıştıran sunucu gruplarına otomatik olarak yüklenir.
* **[Sütunlu depolama](concepts-hyperscale-columnar.md)**.
  Seçili tabloların sütunları (satırlar yerine satırları değil) bitişik olarak diskte depola. Disk üzerinde sıkıştırmayı destekler. Analitik ve veri ambarı iş yükleri için uygundur.
* **[Çoğaltmaları oku](howto-hyperscale-read-replicas-portal.md)** (Şu anda yalnızca aynı bölge). Birincil sunucu grubunda gerçekleşen tüm değişiklikler, çoğaltmasına yansıtılır ve çoğaltmaya karşı sorgular orijinalde ek yük gerektirmez.
  Çoğaltmalar, salt okunurdur iş yüklerinin performansını artırmak için yararlı bir araçtır.
* **[Yönetilen PgBouncer](concepts-hyperscale-limits.md#managed-pgbouncer-preview)**.
  Birçok istemcinin aynı anda sunucu grubuna bağlanmasına izin veren bağlantı havuzlayıcı, etkin bağlantı sayısını sınırlarken. Düzenleyici düğümü sorunsuz şekilde çalışır durumda tutarken bağlantı isteklerini karşılar.
* **[Pgaudit](concepts-hyperscale-audit.md)**. Standart PostgreSQL günlük tesisi aracılığıyla ayrıntılı oturum ve nesne denetim günlüğü sağlar. Belirli kamu, finans veya ISO sertifika denetimlerini geçirmek için gereken denetim günlüklerini üretir.

### <a name="available-regions-for-preview-features"></a>Önizleme özellikleri için kullanılabilir bölgeler

PgAudit uzantısı, [Hyperscale tarafından desteklenen tüm bölgelerde kullanılabilir (Citus)](concepts-hyperscale-configuration-options.md#regions).
Diğer Önizleme özellikleri yalnızca **Doğu ABD** kullanılabilir.

## <a name="does-my-server-group-have-access-to-preview-features"></a>Sunucu grubumun önizleme özelliklerine erişimi var mı?

Hiperscale (Citus) sunucu grubunuzun Önizleme özellikleri etkin olup olmadığını anlamak için Azure portal sunucu grubunun **genel bakış** sayfasına gidin.
Özellik **katmanı: temel (Önizleme)** veya **Katman: Standart (Önizleme)** görürseniz, sunucu grubunuzun önizleme özelliklerine erişimi vardır.

### <a name="how-to-get-access"></a>Erişim alma

Yeni bir hiper ölçek (Citus) sunucu grubu oluştururken **Önizleme özelliklerini etkinleştir** kutusunu işaretleyin.

## <a name="contact-us"></a>Bizimle iletişim kurun

[PostgreSQL Için Azure DB](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)'ye e-posta göndererek Önizleme özelliklerini kullanma deneyiminizle ilgili bize bilgi verin.
(Bu e-posta adresi bir teknik destek kanalı değildir. Teknik sorunlar için bir [destek isteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)açın.)

---
title: Yedekleme ve geri yükleme – Hyperscale (Citus)-PostgreSQL için Azure veritabanı
description: Verileri yanlışlıkla bozulma veya silme işleminden koruma
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520182"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda yedekleme ve geri yükleme-hiper ölçek (Citus)

PostgreSQL için Azure veritabanı – Hyperscale (Citus), her bir düğümün yedeklemesini otomatik olarak oluşturur ve yerel olarak yedekli depolamada depolar. Yedeklemeler, hiper ölçek (Citus) sunucu grubunuzu belirli bir zamana geri yüklemek için kullanılabilir.
Yedekleme ve geri yükleme her iş sürekliliği stratejisinin temel parçalarıdır çünkü bunlar verilerinizi yanlışlıkla bozulmalara veya silmelere karşı korur.

## <a name="backups"></a>Yedeklemeler

Günde en az bir kez, PostgreSQL için Azure veritabanı veri dosyalarının anlık görüntü yedeklemelerini ve veritabanı işlem günlüğünü alır. Yedeklemeler, bir sunucuyu Bekletme dönemi içinde herhangi bir zaman noktasına geri yüklemenize olanak tanır. (Bekletme dönemi Şu anda tüm sunucu grupları için 35 gündür.) Tüm yedeklemeler AES 256 bit şifreleme kullanılarak şifrelenir.

Kullanılabilirlik alanlarını destekleyen Azure bölgelerinde, yedekleme anlık görüntüleri üç kullanılabilirlik alanında depolanır. En az bir kullanılabilirlik bölgesi çevrimiçi olduğu sürece, hiper ölçek (Citus) sunucu grubu geri yüklenir.

Yedekleme dosyaları verilemez. Yalnızca PostgreSQL için Azure veritabanı 'nda geri yükleme işlemleri için kullanılabilir.

### <a name="backup-storage-cost"></a>Yedekleme depolama maliyeti

Geçerli yedekleme depolama fiyatlandırması için bkz. PostgreSQL için Azure veritabanı-hiper ölçek (Citus) [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

## <a name="restore"></a>Geri Yükleme

Hiper ölçek (Citus) sunucu grubunu, son 35 gün içinde herhangi bir zaman noktasına geri yükleyebilirsiniz.  Bir noktadan noktaya geri yükleme, birden çok senaryoda faydalıdır. Örneğin kullanıcı yanlışlıkla verileri silebilir, önemli bir tabloyu veya veritabanını bırakabilir ya da bir uygulama yanlışlıkla iyi verilerin üzerine hatalı verileri yazabilir.

> [!IMPORTANT]
> Hiper ölçek silindi (Citus) sunucu grupları geri yüklenemez. Sunucu grubunu silerseniz, sunucu grubuna ait olan tüm düğümler silinir ve kurtarılamaz. Sunucu grubu kaynaklarını korumak için dağıtım sonrası, yanlışlıkla silme veya beklenmeyen değişikliklerden, Yöneticiler [Yönetim kilitlerinin](../azure-resource-manager/management/lock-resources.md)faydalanabilir.

Geri yükleme işlemi, özgün olarak aynı Azure bölgesinde, abonelikte ve kaynak grubunda yeni bir sunucu grubu oluşturur. Sunucu grubu özgün yapılandırmasına sahiptir: aynı düğüm sayısı, sanal çekirdek sayısı, depolama boyutu, Kullanıcı rolleri, PostgreSQL sürümü ve Citus uzantısının sürümü.

Güvenlik Duvarı ayarları ve PostgreSQL sunucu parametreleri özgün sunucu grubundan korunmaz, varsayılan değerlere sıfırlanır. Güvenlik Duvarı tüm bağlantıları engeller. Geri yükleme sonrasında bu ayarları el ile ayarlamanız gerekir. Genel olarak, önerilen [geri yükleme sonrası görevleri](howto-hyperscale-restore-portal.md#post-restore-tasks)listemize bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Azure portal [bir sunucu grubunu geri yükleme](howto-hyperscale-restore-portal.md) adımlarına bakın.
*  [Azure kullanılabilirlik alanları](../availability-zones/az-overview.md)hakkında bilgi edinin.

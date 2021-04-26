---
title: Bağlantı sorunlarını giderme-MySQL için Azure veritabanı
description: Yeniden denemeler, güvenlik duvarı sorunları ve kesintiler gerektiren geçici hatalar da dahil olmak üzere MySQL için Azure veritabanı 'nda bağlantı sorunlarını nasıl giderebileceğinizi öğrenin.
keywords: MySQL bağlantısı, bağlantı dizesi, bağlantı sorunları, geçici hata, bağlantı hatası
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: c2a2fba0d23ef9102374979300fe850732137c31
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109922"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql"></a>MySQL için Azure Veritabanı bağlantı sorunlarını giderme
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Bağlantı sorunlarına aşağıdakiler de dahil olmak üzere çeşitli öğeler neden olmuş olabilir:

* Güvenlik duvarı ayarları
* Bağlantı zaman aşımı
* Hatalı oturum açma bilgileri
* MySQL için Azure veritabanı kaynaklarında maksimum sınıra ulaşıldı
* Hizmetin altyapısıyla ilgili sorunlar
* Hizmette gerçekleştirilen bakım
* Sunucunun işlem ayırması, sanal çekirdek sayısı ölçeklenerek veya farklı bir hizmet katmanına taşınarak değiştirilir

Genellikle, MySQL için Azure veritabanı 'na yönelik bağlantı sorunları şu şekilde sınıflandırılabilirler:

* Geçici hatalar (kısa ömürlü veya aralıklı)
* Kalıcı veya geçici olmayan hatalar (düzenli olarak yinelenen hatalar)

## <a name="troubleshoot-transient-errors"></a>Geçici hataları giderme

Bakım gerçekleştirildiğinde geçici hatalar meydana gelir, sistem donanım veya yazılımla ilgili bir hatayla karşılaştığında veya sunucunuzun sanal çekirdeklerini veya hizmet katmanını değiştirirsiniz. MySQL için Azure veritabanı hizmeti, yerleşik yüksek kullanılabilirliğe sahiptir ve bu tür sorunları otomatik olarak azaltmak için tasarlanmıştır. Bununla birlikte, uygulamanız, genellikle 60 saniyeden kısa bir süre boyunca sunucu bağlantısını kaybeder. Büyük bir işlemin uzun süre çalışan bir kurtarmaya neden olduğu gibi bazı olayların hafifletmek daha uzun sürebilir.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Geçici bağlantı sorunlarını giderme adımları

1. Uygulama tarafından hataların bildirildiği zaman içinde oluşan bilinen kesintiler için [Microsoft Azure hizmet panosunu](https://azure.microsoft.com/status) denetleyin.
2. MySQL için Azure veritabanı gibi bir bulut hizmetine bağlanan uygulamalar, geçici hatalar beklemelisiniz ve bunları kullanıcılara uygulama hataları olarak eklemek yerine bu hataları işlemek için yeniden deneme mantığını uygulamalıdır. En iyi uygulamalar ve geçici hataları işlemeye yönelik tasarım yönergeleri için, [MySQL Için Azure veritabanı için geçici bağlantı hatalarının işlenmesini](concepts-connectivity.md) gözden geçirin.
3. Sunucu kaynak sınırlarına yaklaşıyorsa, hatalar geçici bağlantı sorunu olabilir. [MySQL Için Azure veritabanı 'Ndaki sınırlamalara](concepts-limits.md)bakın.
4. Bağlantı sorunları devam ederse veya uygulamanızın hatayla karşılaştığı süre 60 saniye değerini aşarsa veya hatanın belirli bir gün içinde birden çok kez yinelendiğini görürseniz, [Azure](https://azure.microsoft.com/support/options) destek sitesinde **Destek Al** ' ı seçerek bir Azure destek isteği dosyası sağlayın.

## <a name="troubleshoot-persistent-errors"></a>Kalıcı hataları giderme

Uygulama, MySQL için Azure veritabanı 'na kalıcı olarak bağlanamazsa, genellikle aşağıdakilerden biriyle ilgili bir sorun olduğunu gösterir:

* Sunucu güvenlik duvarı yapılandırması: MySQL Server güvenlik duvarı için Azure veritabanı 'nın, proxy sunucuları ve ağ geçitleri dahil olmak üzere istemcinizden gelen bağlantılara izin verecek şekilde yapılandırıldığından emin olun.
* İstemci güvenlik duvarı yapılandırması: istemcinizdeki güvenlik duvarı, veritabanı sunucunuza yönelik bağlantılara izin vermelidir. Bazı güvenlik duvarlarındaki MySQL gibi uygulama adlarının yanı sıra, izin verilmeyen sunucunun IP adreslerine ve bağlantı noktalarına izin verilmesi gerekir.
* Kullanıcı hatası: bağlantı dizesindeki sunucu adı veya Kullanıcı adında eksik *\@ ServerName* son eki gibi yanlış bağlantı parametreleri olabilir.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kalıcı bağlantı sorunlarını giderme adımları

1. İstemci IP adresine izin vermek için [güvenlik duvarı kuralları](howto-manage-firewall-using-portal.md) ayarlayın. Yalnızca geçici test amacıyla, başlangıç IP adresi olarak 0.0.0.0 kullanarak bir güvenlik duvarı kuralı ayarlayın ve bitiş IP adresi olarak 255.255.255.255 kullanın. Bu, sunucuyu tüm IP adreslerine açar. Bu, bağlantı sorununuzu giderirse, bu kuralı kaldırın ve uygun şekilde sınırlı bir IP adresi veya adres aralığı için bir güvenlik duvarı kuralı oluşturun.
2. İstemci ile İnternet arasındaki tüm güvenlik duvarlarında, bağlantı noktası 3306 ' ın giden bağlantılar için açık olduğundan emin olun.
3. Bağlantı dizenizi ve diğer bağlantı ayarlarını doğrulayın. [Uygulamaları MySQL Için Azure veritabanı 'na bağlamayı](howto-connection-string.md)inceleyin.
4. Panodaki hizmet durumunu denetleyin. Bölgesel bir kesinti olduğunu düşünüyorsanız, yeni bir bölgeye kurtarma adımları için bkz. [MySQL Için Azure veritabanı ile iş sürekliliği 'Ne genel bakış](concepts-business-continuity.md) .

## <a name="next-steps"></a>Sonraki adımlar

* [MySQL için Azure veritabanı geçici bağlantı hatalarını işleme](concepts-connectivity.md)

---
title: Bağlantı sorunlarını giderme-MariaDB için Azure veritabanı
description: Denemeler, güvenlik duvarı sorunları ve kesintiler gerektiren geçici hatalar da dahil olmak üzere MariaDB için Azure veritabanı bağlantı sorunlarını nasıl giderebileceğinizi öğrenin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: b9a670472529dccd51cbfa71e385151bc63cff9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664741"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>MariaDB için Azure Veritabanı bağlantı sorunlarını giderme

Bağlantı sorunlarına aşağıdakiler de dahil olmak üzere çeşitli öğeler neden olmuş olabilir:

* Güvenlik duvarı ayarları
* Bağlantı zaman aşımı
* Hatalı oturum açma bilgileri
* MariaDB kaynakları için Azure veritabanı 'nda maksimum sınıra ulaşıldı
* Hizmetin altyapısıyla ilgili sorunlar
* Hizmette gerçekleştirilen bakım
* Sunucunun işlem ayırması, sanal çekirdek sayısı ölçeklenerek veya farklı bir hizmet katmanına taşınarak değiştirilir

Genellikle, MariaDB için Azure veritabanı bağlantı sorunları şu şekilde sınıflandırılabilir:

* Geçici hatalar (kısa ömürlü veya aralıklı)
* Kalıcı veya geçici olmayan hatalar (düzenli olarak yinelenen hatalar)

## <a name="troubleshoot-transient-errors"></a>Geçici hataları giderme

Bakım gerçekleştirildiğinde geçici hatalar meydana gelir, sistem donanım veya yazılımla ilgili bir hatayla karşılaştığında veya sunucunuzun sanal çekirdeklerini veya hizmet katmanını değiştirirsiniz. MariaDB hizmeti için Azure veritabanı 'nın yerleşik yüksek kullanılabilirliği vardır ve bu tür sorunların otomatik olarak azaltılmasına yönelik olarak tasarlanmıştır. Bununla birlikte, uygulamanız, genellikle 60 saniyeden kısa bir süre boyunca sunucu bağlantısını kaybeder. Büyük bir işlemin uzun süre çalışan bir kurtarmaya neden olduğu gibi bazı olayların hafifletmek daha uzun sürebilir.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Geçici bağlantı sorunlarını giderme adımları

1. Uygulama tarafından hataların bildirildiği zaman içinde oluşan bilinen kesintiler için [Microsoft Azure hizmet panosunu](https://azure.microsoft.com/status) denetleyin.
2. MariaDB için Azure veritabanı gibi bir bulut hizmetine bağlanan uygulamalar, geçici hatalar beklemelisiniz ve bunları kullanıcılara uygulama hataları olarak eklemek yerine bu hataları işlemek için yeniden deneme mantığını uygulamalıdır. En iyi uygulamalar ve geçici hataları işlemeye yönelik tasarım yönergeleri için, [MariaDB Için Azure veritabanı geçici bağlantı hatalarının işlenmesini](concepts-connectivity.md) gözden geçirin.
3. Sunucu kaynak sınırlarına yaklaşıyorsa, hatalar geçici bağlantı sorunu olabilir. [MariaDB Için Azure veritabanı 'Ndaki sınırlamalara](concepts-limits.md)bakın.
4. Bağlantı sorunları devam ederse veya uygulamanızın hatayla karşılaştığı süre 60 saniye değerini aşarsa veya hatanın belirli bir gün içinde birden çok kez yinelendiğini görürseniz, [Azure](https://azure.microsoft.com/support/options) destek sitesinde **Destek Al** ' ı seçerek bir Azure destek isteği dosyası sağlayın.

## <a name="troubleshoot-persistent-errors"></a>Kalıcı hataları giderme

Uygulama, MariaDB için Azure veritabanı 'na kalıcı olarak bağlanamıyorsa, genellikle aşağıdakilerden biriyle ilgili bir sorun olduğunu gösterir:

* Güvenlik duvarı yapılandırması: MariaDB sunucusu veya istemci tarafı güvenlik duvarı için Azure veritabanı bağlantıları engelliyor.
* İstemci tarafında ağ yeniden yapılandırması: yeni bir IP adresi veya bir ara sunucu eklendi.
* Kullanıcı hatası: Örneğin, bağlantı dizesindeki sunucu adı veya Kullanıcı adında eksik *\@ ServerName* son eki gibi yanlış bağlantı parametreleri olabilir.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kalıcı bağlantı sorunlarını giderme adımları

1. İstemci IP adresine izin vermek için [güvenlik duvarı kuralları](howto-manage-firewall-portal.md) ayarlayın. Yalnızca geçici test amacıyla, başlangıç IP adresi olarak 0.0.0.0 kullanarak bir güvenlik duvarı kuralı ayarlayın ve bitiş IP adresi olarak 255.255.255.255 kullanın. Bu, sunucuyu tüm IP adreslerine açar. Bu, bağlantı sorununuzu giderirse, bu kuralı kaldırın ve uygun şekilde sınırlı bir IP adresi veya adres aralığı için bir güvenlik duvarı kuralı oluşturun.
2. İstemci ile İnternet arasındaki tüm güvenlik duvarlarında, bağlantı noktası 3306 ' ın giden bağlantılar için açık olduğundan emin olun.
3. Bağlantı dizenizi ve diğer bağlantı ayarlarını doğrulayın. [MariaDB Için Azure veritabanı 'na uygulama bağlamayı](howto-connection-string.md)inceleyin.
4. Panodaki hizmet durumunu denetleyin. Bölgesel bir kesinti olduğunu düşünüyorsanız, yeni bir bölgeye kurtarma adımları için bkz. [MariaDB Için Azure veritabanı ile iş sürekliliği 'Ne genel bakış](concepts-business-continuity.md) .

## <a name="next-steps"></a>Sonraki adımlar

* [MariaDB için Azure veritabanı geçici bağlantı hatalarını işleme](concepts-connectivity.md)

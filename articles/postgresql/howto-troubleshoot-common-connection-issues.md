---
title: Bağlantı sorunlarını giderme-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı-tek sunucu ile bağlantı sorunlarını giderme hakkında bilgi edinin.
keywords: PostgreSQL bağlantısı, bağlantı dizesi, bağlantı sorunları, geçici hata, bağlantı hatası
author: sunilagarwal
ms.author: sunila
ms.reviewer: ''
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 7fe8c4b751be174a91a0e2e94991bc63b4b1e5c7
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504252"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı ile ilgili bağlantı sorunlarını giderme-tek sunucu

Bağlantı sorunlarına aşağıdakiler dahil çeşitli şeyler neden olmuş olabilir:

* Güvenlik duvarı ayarları
* Bağlantı zaman aşımı
* Hatalı oturum açma bilgileri
* Bazı PostgreSQL kaynakları için Azure veritabanı kaynaklarına maksimum sınıra ulaşıldı
* Hizmetin altyapısıyla ilgili sorunlar
* Hizmette gerçekleştirilen bakım
* Sunucunun işlem ayırması, sanal çekirdek sayısı ölçeklenerek veya farklı bir hizmet katmanına taşınarak değiştirilir

Genellikle, PostgreSQL için Azure veritabanı bağlantı sorunları şu şekilde sınıflandırılabilir:

* Geçici hatalar (kısa ömürlü veya aralıklı)
* Kalıcı veya geçici olmayan hatalar (düzenli olarak yinelenen hatalar)

## <a name="troubleshoot-transient-errors"></a>Geçici hataları giderme

Bakım gerçekleştirildiğinde geçici hatalar meydana gelir, sistem donanım veya yazılımla ilgili bir hatayla karşılaştığında veya sunucunuzun sanal çekirdeklerini veya hizmet katmanını değiştirirsiniz. PostgreSQL için Azure veritabanı hizmeti yerleşik yüksek kullanılabilirliğe sahiptir ve bu tür sorunları otomatik olarak azaltmak için tasarlanmıştır. Bununla birlikte, uygulamanız, genellikle 60 saniyeden kısa bir süre boyunca sunucu bağlantısını kaybeder. Büyük bir işlemin uzun süre çalışan bir kurtarmaya neden olduğu gibi bazı olayların hafifletmek daha uzun sürebilir.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Geçici bağlantı sorunlarını giderme adımları

1. Uygulama tarafından hataların bildirildiği zaman içinde oluşan bilinen kesintiler için [Microsoft Azure hizmet panosunu](https://azure.microsoft.com/status) denetleyin.
2. PostgreSQL için Azure veritabanı gibi bir bulut hizmetine bağlanan uygulamalar, bunları kullanıcılara uygulama hataları olarak eklemek yerine bu hataları işlemek için geçici hataları ve yeniden deneme mantığını uygulamalıdır. [PostgreSQL Için Azure veritabanı için geçici bağlantı hatalarının işlenmesini](concepts-connectivity.md) , en iyi uygulamalar ve geçici hataları işlemeye yönelik tasarım yönergeleri için gözden geçirin.
3. Sunucu kaynak sınırlarına yaklaşıyorsa, hatalar geçici bağlantı sorunu olabilir. [PostgreSQL Için Azure veritabanı 'Ndaki sınırlamalara](concepts-limits.md)bakın.
4. Bağlantı sorunları devam ederse veya uygulamanızın hatayla karşılaştığı süre 60 saniye değerini aşarsa veya hatanın belirli bir gün içinde birden çok kez yinelendiğini görürseniz, [Azure](https://azure.microsoft.com/support/options) destek sitesinde **Destek Al** ' ı seçerek bir Azure destek isteği dosyası sağlayın.

## <a name="troubleshoot-persistent-errors"></a>Kalıcı hataları giderme

Uygulama, PostgreSQL için Azure veritabanı 'na kalıcı olarak bağlanamazsa, genellikle aşağıdakilerden biriyle ilgili bir sorun olduğunu gösterir:

* Sunucu güvenlik duvarı yapılandırması: PostgreSQL için Azure veritabanı sunucu güvenlik duvarının, proxy sunucuları ve ağ geçitleri dahil olmak üzere istemcinizden gelen bağlantılara izin verecek şekilde yapılandırıldığından emin olun.
* İstemci güvenlik duvarı yapılandırması: istemcinizdeki güvenlik duvarı, veritabanı sunucunuza yönelik bağlantılara izin vermelidir. Bağlanamadaki sunucunun IP adreslerine ve bağlantı noktalarına, bazı güvenlik duvarlarındaki PostgreSQL gibi uygulama adlarına izin verilmelidir.
* Kullanıcı hatası: bağlantı dizesindeki sunucu adı veya Kullanıcı adında eksik *\@ ServerName* son eki gibi yanlış bağlantı parametreleri olabilir.
* _Sunucu, IPv6 bağlantılarına izin verecek şekilde yapılandırılmadığını_ görürseniz, temel katmanın sanal ağ hizmet uç noktalarını desteklemediğini unutmayın. Temel sunucuya bağlanmaya çalışan alt ağdan Microsoft. SQL uç noktasını kaldırmanız gerekir.
* _SSL desteği hatada derlenmediği zaman "* * *"_ bağlantı hatasını görürseniz, bu, PostgreSQL istemciniz SSL 'yi desteklemediğinden anlamına gelir. Büyük olasılıkla, istemci tarafı libpq "--with-OpenSSL" bayrağıyla derlenmemiştir. SSL desteği olan bir PostgreSQL istemcisiyle bağlanmayı deneyin. 

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kalıcı bağlantı sorunlarını giderme adımları

1. İstemci IP adresine izin vermek için [güvenlik duvarı kuralları](howto-manage-firewall-using-portal.md) ayarlayın. Yalnızca geçici test amacıyla, başlangıç IP adresi olarak 0.0.0.0 kullanarak bir güvenlik duvarı kuralı ayarlayın ve bitiş IP adresi olarak 255.255.255.255 kullanın. Bu, sunucuyu tüm IP adreslerine açar. Bu, bağlantı sorununuzu giderirse, bu kuralı kaldırın ve uygun şekilde sınırlı bir IP adresi veya adres aralığı için bir güvenlik duvarı kuralı oluşturun.
2. İstemci ile İnternet arasındaki tüm güvenlik duvarlarında, bağlantı noktası 5432 ' ın giden bağlantılar için açık olduğundan emin olun.
3. Bağlantı dizenizi ve diğer bağlantı ayarlarını doğrulayın.
4. Panodaki hizmet durumunu denetleyin. Bölgesel bir kesinti olduğunu düşünüyorsanız, yeni bir bölgeye kurtarma adımları için [PostgreSQL Için Azure veritabanı ile iş sürekliliği 'Ne genel bakış](concepts-business-continuity.md) konusuna bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [PostgreSQL için Azure veritabanı geçici bağlantı hatalarının işlenmesi](concepts-connectivity.md)

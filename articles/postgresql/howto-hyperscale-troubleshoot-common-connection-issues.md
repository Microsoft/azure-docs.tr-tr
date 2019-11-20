---
title: PostgreSQL için Azure veritabanı 'nda bağlantı sorunlarını giderme-hiper ölçek (Citus)
description: PostgreSQL için Azure veritabanı 'nda bağlantı sorunlarını giderme-hiper ölçek (Citus) hakkında bilgi edinin
keywords: PostgreSQL bağlantısı, bağlantı dizesi, bağlantı sorunları, geçici hata, bağlantı hatası
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: b812b730cebba4dbf0735f49f544e53bf7f8787c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952153"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda bağlantı sorunlarını giderme-hiper ölçek (Citus)

Bağlantı sorunlarına birkaç şey neden olabilir, örneğin:

* Güvenlik duvarı ayarları
* Bağlantı zaman aşımı
* Hatalı oturum açma bilgileri
* Sunucu grubu için bağlantı sınırına ulaşıldı
* Hizmetin altyapısıyla ilgili sorunlar
* Hizmet bakımı
* Düzenleyici düğümü, yeni donanıma yük devretiyor

Genellikle, hiper ölçeğe yönelik bağlantı sorunları şu şekilde sınıflandırılabilir:

* Geçici hatalar (kısa ömürlü veya aralıklı)
* Kalıcı veya geçici olmayan hatalar (düzenli olarak yinelenen hatalar)

## <a name="troubleshoot-transient-errors"></a>Geçici hatalarda sorun giderme

Geçici hatalar, birkaç nedenden dolayı oluşur. En yaygın sistem bakımı, donanım veya yazılımla ilgili hata ve düğüm vCore yükseltmelerini düzenleyici.

Hiper ölçek sunucu grubu düğümleri için yüksek kullanılabilirliği etkinleştirmek, bu tür sorunları otomatik olarak hafifletdirebilir. Bununla birlikte, uygulamanız yine de bağlantısını kaybetmek için hazırlanmalıdır. Ayrıca, büyük bir işlemin uzun süre çalışan bir kurtarmaya neden olduğu gibi diğer olayların hafifletmek daha uzun sürebilir.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Geçici bağlantı sorunlarını giderme adımları

1. Uygulamanın hata bildirdiği zaman sırasında oluşan bilinen kesintiler için [Microsoft Azure hizmeti panosunu](https://azure.microsoft.com/status) denetleyin.
2. Hiperscale (Citus) gibi bir bulut hizmetine bağlanan uygulamalar geçici hatalar ve düzgün şekilde tepki sağlamalıdır. Örneğin, uygulamalar, kullanıcılara uygulama hataları olarak sunulmak yerine bu hataları işlemek için yeniden deneme mantığı uygulamalıdır.
3. Sunucu grubu kaynak sınırlarına yaklaşırsa, hatalar geçici bağlantı sorunları gibi görünebilir. Düğüm RAM 'i artırma veya çalışan düğümleri ekleme ve verileri yeniden dengeleme yardımcı olabilir.
4. Bağlantı sorunları devam ederse ya da 60 saniyeden daha uzun bir süre veya günde birden çok kez gerçekleşiyorsa [Azure](https://azure.microsoft.com/support/options) destek sitesinde **Destek Al** ' ı seçerek bir Azure destek isteği yapın.

## <a name="troubleshoot-persistent-errors"></a>Kalıcı hatalarda sorun giderme

Uygulama, hiper ölçeğe (Citus) bağlanamazsa, en yaygın nedenler güvenlik duvarının yanlış yapılandırılması veya Kullanıcı hatasıdır.

* Düzenleyici düğüm güvenlik duvarı yapılandırması: hiper ölçek sunucusu güvenlik duvarının, proxy sunucuları ve ağ geçitleri dahil olmak üzere istemcinizden gelen bağlantılara izin verecek şekilde yapılandırıldığından emin olun.
* İstemci güvenlik duvarı yapılandırması: istemcinizdeki güvenlik duvarı, veritabanı sunucunuza yönelik bağlantılara izin vermelidir. Bazı güvenlik duvarları yalnızca ada göre uygulama yapılmasına izin vermeyi gerektirir, ancak sunucunun IP adreslerine ve bağlantı noktalarına izin verir.
* Kullanıcı hatası: bağlantı dizesini çift kontrol edin. Sunucu adı gibi yanlış parametre veya Kullanıcı adında *\@ServerName* sonekini unuttum olabilir.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kalıcı bağlantı sorunlarını giderme adımları

1. İstemci IP adresine izin vermek için [güvenlik duvarı kuralları](howto-hyperscale-manage-firewall-using-portal.md) ayarlayın. Yalnızca geçici test amacıyla, başlangıç IP adresi olarak 0.0.0.0 kullanarak bir güvenlik duvarı kuralı ayarlayın ve bitiş IP adresi olarak 255.255.255.255 kullanın. Bu kural, sunucuyu tüm IP adreslerine açar. Kural, bağlantı sorununuzu giderirse, bunu kaldırın ve uygun şekilde sınırlı bir IP adresi veya adres aralığı için bir güvenlik duvarı kuralı oluşturun.
2. İstemci ile İnternet arasındaki tüm güvenlik duvarlarında, bağlantı noktası 5432 ' ın giden bağlantılar için açık olduğundan emin olun.
3. Bağlantı dizenizi ve diğer bağlantı ayarlarını doğrulayın.
4. Panodaki hizmet durumunu denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [PostgreSQL Için Azure veritabanı 'Nda güvenlik duvarı kuralları kavramlarını öğrenme-hiper ölçek (Citus)](concepts-hyperscale-firewall-rules.md)
* Bkz. [PostgreSQL Için Azure veritabanı için güvenlik duvarı kurallarını yönetme-hiper ölçek (Citus)](howto-hyperscale-manage-firewall-using-portal.md)

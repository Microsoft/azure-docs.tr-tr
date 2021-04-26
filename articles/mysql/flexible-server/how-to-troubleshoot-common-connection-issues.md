---
title: Bağlantı sorunlarını giderme-MySQL için Azure veritabanı-esnek sunucu
description: MySQL için Azure veritabanı esnek sunucusu 'nda bağlantı sorunlarını giderme hakkında bilgi edinin.
keywords: MySQL bağlantısı, bağlantı dizesi, bağlantı sorunları, kalıcı hata, bağlantı hatası
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: 6fc8e08757ee067e0616cc701a3037a9dbf4c6fd
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491755"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>MySQL için Azure veritabanı 'na yönelik bağlantı sorunlarını giderme-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bağlantı sorunlarına aşağıdakiler de dahil olmak üzere çeşitli öğeler neden olmuş olabilir:

* Güvenlik duvarı ayarları
* Bağlantı zaman aşımı
* Hatalı oturum açma bilgileri
* MySQL için Azure veritabanı esnek sunucu kaynakları için en fazla sınıra ulaşıldı

Bu makalede, bu hataları çözmek için bazı yaygın hatalardan ve adımlardan oluşan sorunları nasıl giderebileceğiniz ele alınacaktır.

## <a name="troubleshoot-common-errors"></a>Sık karşılaşılan hataları giderme

Uygulama, MySQL esnek sunucusu için Azure veritabanı 'na kalıcı olarak bağlanamıyorsa, genellikle aşağıdakilerden biriyle ilgili bir sorun olduğunu gösterir:

* TLS/SSL kullanılarak şifrelenmiş bağlantı: esnek sunucu, Aktarım Katmanı Güvenliği (TLS 1,2) kullanılarak şifrelenmiş bağlantıları destekler ve **tls 1,0 ve tls 1,1 tüm gelen bağlantıları varsayılan olarak reddedilir**. Şifrelenmiş bağlantıların uygulanmasını devre dışı bırakabilir veya TLS sürümünü değiştirebilirsiniz. [MySQL Için Azure veritabanı-esnek sunucu 'Da Aktarım Katmanı Güvenliği (TLS 1,2) kullanılarak şifrelenmiş bağlantı](./how-to-connect-tls-ssl.md)hakkında daha fazla bilgi edinin.
- *Özel erişimde esnek sunucu (VNET tümleştirmesi)*: esnek sunucu ile aynı sanal ağ içinden bağlanmakta olduğunuzdan emin olun. [MySQL için Azure veritabanı esnek sunucusu 'nda sanal ağ] bölümüne bakın.<!--(./concepts-networking-virtual-network.md)-->
- Ortak erişime sahip esnek sunucu *(izin VERILEN IP adresleri)*, güvenlik duvarının istemcinizden gelen bağlantılara izin verecek şekilde yapılandırıldığından emin olun. [Azure Portal kullanarak esnek sunucu güvenlik duvarı kuralları oluşturma ve yönetme](./how-to-manage-firewall-portal.md)bölümüne bakın.
* İstemci güvenlik duvarı yapılandırması: istemcinizdeki güvenlik duvarı, veritabanı sunucunuza yönelik bağlantılara izin vermelidir. Bazı güvenlik duvarlarındaki MySQL gibi uygulama adlarının yanı sıra, izin verilmeyen sunucunun IP adreslerine ve bağlantı noktalarına izin verilmesi gerekir.
* Kullanıcı hatası: bağlantı dizesindeki sunucu adı gibi yanlış bağlantı parametrelerine sahip olabilirsiniz.

### <a name="resolve-connectivity-issues"></a>Bağlantı sorunlarını çözme

* Şifrelenmiş bağlantılar hakkında daha fazla bilgi edinmek için [, MySQL Için Azure veritabanı-esnek sunucu--> Aktarım Katmanı Güvenliği (TLS 1,2) kullanılarak şifrelenmiş bağlantı](./how-to-connect-tls-ssl.md) bölümüne bakın.
* **Ortak erişim (izin VERILEN IP adresleri)** kullanıyorsanız, istemci IP adresine izin vermek için [güvenlik duvarı kuralları](./how-to-manage-firewall-portal.md) ayarlayın. Yalnızca geçici test amacıyla, başlangıç IP adresi olarak 0.0.0.0 kullanarak bir güvenlik duvarı kuralı ayarlayın ve bitiş IP adresi olarak 255.255.255.255 kullanın. Bu, sunucuyu tüm IP adreslerine açar. Bu, bağlantı sorununuzu giderirse, bu kuralı kaldırın ve uygun şekilde sınırlı bir IP adresi veya adres aralığı için bir güvenlik duvarı kuralı oluşturun.
* İstemci ile İnternet arasındaki tüm güvenlik duvarlarında, bağlantı noktası 3306 ' ın giden bağlantılar için açık olduğundan emin olun.
* Bağlantı dizenizi ve diğer bağlantı ayarlarını doğrulayın. Ortak diller için Azure portal sunucunuzdaki **bağlantı dizeleri** sayfasında önceden tanımlı bağlantı dizelerine bakın.

## <a name="next-steps"></a>Sonraki adımlar
- MySQL [Için Azure veritabanı esnek sunucusu 'na bağlanmak ve veri sorgulamak Için MySQL çalışma sunucusunu kullanın](./connect-workbench.md).
- [MySQL Için Azure veritabanı esnek sunucusu 'na bağlanmak ve verileri sorgulamak IÇIN php 'Yi kullanın](./connect-php.md).
- [MySQL Için Azure veritabanı esnek sunucusu 'na bağlanmak ve verileri sorgulamak Için Python kullanın](./connect-python.md).

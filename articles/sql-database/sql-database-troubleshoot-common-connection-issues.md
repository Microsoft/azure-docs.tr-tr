---
title: Sık karşılaşılan bağlantı sorunlarını giderme
description: Azure SQL veritabanı için genel bağlantı hatalarını belirlemek ve çözümlemek için gereken adımlar.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 11/14/2019
ms.openlocfilehash: ffbe52bfcef3f32a12e97d37c39a2199cefe72ce
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082453"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Azure SQL veritabanı ile ilgili bağlantı sorunlarını giderme

Azure SQL veritabanı bağlantısı başarısız olduğunda [hata iletileri](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)alırsınız. Bu makale, Azure SQL veritabanı bağlantı sorunlarını gidermenize yardımcı olacak merkezi bir konudur. Bağlantı sorunlarının [yaygın nedenlerini](#cause) tanıtır, sorun için kimlik bulmanıza yardımcı olan [bir sorun giderme aracı](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) önerir ve [geçici hataları](#troubleshoot-transient-errors) ve [kalıcı veya geçici olmayan hataları çözmek için sorun giderme adımları sağlar ](#troubleshoot-persistent-errors).

Bağlantı sorunlarıyla karşılaşırsanız, bu makalede açıklanan sorun giderme adımlarını deneyin.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Nedeni

Bağlantı sorunlarına aşağıdakilerden biri neden olmuş olabilir:

* Uygulama tasarım sürecinde en iyi yöntemler ve tasarım yönergeleri uygulama başarısız oldu.  Başlamak için bkz. [SQL veritabanı geliştirmeye genel bakış](sql-database-develop-overview.md) .
* Azure SQL veritabanı yeniden yapılandırması
* Güvenlik duvarı ayarları
* Bağlantı zaman aşımı
* Hatalı oturum açma bilgileri
* Bazı Azure SQL veritabanı kaynaklarında maksimum sınıra ulaşıldı

Genellikle, Azure SQL veritabanı 'na yönelik bağlantı sorunları şu şekilde sınıflandırılabilirler:

* [Geçici hatalar (kısa ömürlü veya aralıklı)](#troubleshoot-transient-errors)
* [Kalıcı veya geçici olmayan hatalar (düzenli olarak yinelenen hatalar)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Azure SQL veritabanı bağlantı sorunlarıyla ilgili sorun gidericiyi deneyin

Belirli bir bağlantı hatasıyla karşılaşırsanız, [Bu aracı](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)deneyin; bu, sorununuzu hızlı bir şekilde tanımanıza ve çözmenize yardımcı olur.

## <a name="troubleshoot-transient-errors"></a>Geçici hatalarda sorun giderme

Bir uygulama bir Azure SQL veritabanına bağlanırsa, aşağıdaki hata iletisini alırsınız:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Bu hata iletisi genellikle geçicidir (kısa süreli).

Bu hata, veritabanı taşınırken (veya yeniden yapılandırdığınızda) ve uygulamanız veritabanına bağlantısını kaybettiğinde oluşur. Planlanmış bir olay (örneğin, yazılım yükseltmesi) veya plansız bir olay (örneğin, bir işlem kilitlenmesi veya yük dengeleme) nedeniyle veritabanı yeniden yapılandırma olayları meydana gelir. En yeniden yapılandırma olaylarının çoğu genellikle kısa süreli olur ve en çok 60 saniyeden kısa bir süre içinde tamamlanmalıdır. Ancak, büyük bir işlemin uzun süre çalışan bir kurtarmaya neden olduğu gibi, bu olayların zaman zaman tamamlanması daha uzun sürebilir.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Geçici bağlantı sorunlarını giderme adımları

1. Uygulama tarafından hataların bildirildiği süre boyunca oluşan bilinen kesintiler için [Microsoft Azure hizmet panosunu](https://azure.microsoft.com/status) denetleyin.
2. Azure SQL veritabanı gibi bir bulut hizmetine bağlanan uygulamalar, düzenli olarak yeniden yapılandırma olaylarını beklemeli ve bu hataları, kullanıcılara uygulama hataları olarak ortaya çıkacak şekilde işlemek için yeniden deneme mantığı uygulamalıdır. Daha fazla bilgi ve genel yeniden deneme stratejileri için [SQL veritabanı geliştirmeye genel bakış](sql-database-develop-overview.md) 'da [geçici hatalar](sql-database-connectivity-issues.md) bölümünü ve en iyi uygulamaları ve tasarım kılavuzunu gözden geçirin. Ardından bkz. [SQL veritabanı Için bağlantı kitaplıklarında](sql-database-libraries.md) kod örnekleri ve ayrıntılar için SQL Server.
3. Bir veritabanı kaynak sınırlarına yaklaşıyorsa, geçici bir bağlantı sorunu olabilir. Bkz. [kaynak sınırları](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Bağlantı sorunları devam ederse veya uygulamanızın hatayla karşılaştığı süre 60 saniyeyi aşarsa veya hatanın belirli bir gün içinde birden çok kez yinelendiğini görürseniz, Azure 'da **Destek Al** ' ı seçerek bir Azure destek Isteği dosyası sağlayın [ Destek](https://azure.microsoft.com/support/options) sitesi.

## <a name="troubleshoot-persistent-errors"></a>Kalıcı hatalarda sorun giderme

Uygulama, Azure SQL veritabanı 'na kalıcı olarak bağlanamazsa, genellikle aşağıdakilerden biriyle ilgili bir sorun olduğunu gösterir:

* Güvenlik duvarı yapılandırması. Azure SQL veritabanı veya istemci tarafı güvenlik duvarı Azure SQL veritabanı bağlantılarını engelliyor.
* İstemci tarafında ağ yeniden yapılandırması: Örneğin, yeni bir IP adresi veya bir ara sunucu.
* Kullanıcı hatası: Örneğin, bağlantı dizesindeki sunucu adı gibi yanlış bağlantı parametreleri.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kalıcı bağlantı sorunlarını giderme adımları

1. İstemci IP adresine izin vermek için [güvenlik duvarı kuralları](sql-database-configure-firewall-settings.md) ayarlayın. Geçici test amacıyla, başlangıç IP adresi aralığı olarak 0.0.0.0 kullanarak ve bitiş IP adresi aralığı olarak 255.255.255.255 kullanarak bir güvenlik duvarı kuralı ayarlayın. Bu, sunucuyu tüm IP adreslerine açar. Bu, bağlantı sorununuzu giderirse, bu kuralı kaldırın ve uygun şekilde sınırlı bir IP adresi veya adres aralığı için bir güvenlik duvarı kuralı oluşturun.
2. İstemci ile Internet arasındaki tüm güvenlik duvarlarında, bağlantı noktası 1433 ' ın giden bağlantılar için açık olduğundan emin olun. [Windows Güvenlik Duvarı 'nı,](https://msdn.microsoft.com/library/cc646023.aspx) Azure Active Directory kimlik doğrulaması için açmanız gereken ek bağlantı noktalarıyla ilgili ek işaretçiler Için SQL Server erişim ve [karma kimlik Için gerekli bağlantı noktaları ve protokoller](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) sağlamak üzere yapılandırın.
3. Bağlantı dizenizi ve diğer bağlantı ayarlarını doğrulayın. Bağlantı [sorunları konusunun](sql-database-connectivity-issues.md#connections-to-sql-database)bağlantı dizesi bölümüne bakın.
4. Panodaki hizmet durumunu denetleyin. Bölgesel bir kesinti olduğunu düşünüyorsanız, yeni bir bölgeye kurtarma adımları için bkz. [bir kesinti Ile kurtarma](sql-database-disaster-recovery.md) .

## <a name="next-steps"></a>Sonraki adımlar

* [SQL veritabanı geliştirmeye genel bakış](sql-database-develop-overview.md)
* [Genel geçici hata işleme Kılavuzu](../best-practices-retry-general.md)
* [SQL veritabanı ve SQL Server için bağlantı kitaplıkları](sql-database-libraries.md)

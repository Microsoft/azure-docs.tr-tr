---
title: Zamanlanan Bakım-PostgreSQL için Azure veritabanı-hiper ölçek (Citus)
description: Bu makalede PostgreSQL için Azure veritabanı-hiper ölçek (Citus) içinde zamanlanmış bakım özelliği açıklanır.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106794"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda zamanlanmış bakım – hiper ölçek (Citus)

PostgreSQL için Azure veritabanı-Hyperscale (Citus), yönetilen veritabanınızı güvenli, kararlı ve güncel tutmak için düzenli aralıklarla bakım yapar.  Bakım sırasında, sunucu grubundaki tüm düğümler yeni özellikler, güncelleştirmeler ve düzeltme ekleri alır.

Hiper ölçek için zamanlanmış bakımın (Citus) temel özellikleri şunlardır:

* Güncelleştirmeler sunucu grubundaki tüm düğümlerde aynı anda uygulanır
* Yaklaşan bakımla ilgili bildirimler, Azure hizmet durumu 'na beş gün önceden gönderilir
* Genellikle, bir sunucu grubu için başarılı bakım olayları arasında en az 30 gün vardır
* Bakım başlangıcı için o gün içindeki haftanın ve zaman penceresinin tercih edilen günü, her bir sunucu grubu için ayrı ayrı tanımlanabilir

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>Bakım penceresi seçme ve yaklaşan bakım hakkında bildirim

Belirli bir gün boyunca ve o gün içinde bir zaman penceresi için bakım zamanlayabilirsiniz. Ya da sistemin sizin yerinize otomatik olarak bir gün ve zaman penceresi seçmesini sağlayabilirsiniz. Her iki durumda da, sistem herhangi bir bakım çalıştırılmadan önce beş gün sonra uyarır. Sistem ayrıca bakım başladığında ve başarıyla tamamlandığında size bilgi verir.

Yaklaşan zamanlanmış bakımla ilgili bildirimler Azure hizmet durumu 'na gönderilir ve şu olabilir:

* Belirli bir adrese e-postayla
* Azure Resource Manager rolüne e-postayla
* Mobil cihazlara kısa mesajda (SMS) gönderilir
* Azure uygulamasına bildirim olarak gönderilerek
* Sesli mesaj olarak teslim edilerek

Bakım zamanlamasıyla ilgili tercihleri belirtirken haftanın gününü ve zaman aralığını belirleyebilirsiniz. Belirtmezseniz, sistem, sunucu grubunuzun bölge saatinde 11 PM ile 7 saat arasında bir süre seçer. Azure aboneliğinizdeki her bir hiper ölçek (Citus) sunucu grubu için farklı zamanlamalar tanımlayabilirsiniz.

> [!IMPORTANT]
> Normal olarak, bir sunucu grubu için başarılı zamanlanmış bakım olayları arasında en az 30 gün vardır.
>
> Ancak ciddi bir güvenlik açığı gibi kritik öneme sahip acil bir güncelleştirme söz konusu olduğunda beş günden daha kısa bir süre öncesinde bildirim yapılabilir. Son 30 gün içinde başarılı bir zamanlanmış bakım gerçekleştirilmiş olsa dahi sunucunuza kritik güncelleştirme uygulanabilir.

Zamanlama ayarlarını dilediğiniz zaman güncelleştirebilirsiniz. Hiperscale (Citus) sunucu grubunuz için zamanlanmış bakım varsa ve zamanlamayı güncelleştirirseniz, mevcut olaylar başlangıçta zamanlandığı gibi devam edecektir. Ayarlar değişikliği, var olan olayların başarılı bir şekilde tamamlanmasından sonra devreye girer.

Bakım başarısız olursa veya iptal edilirse sistem bir bildirim oluşturacaktır.
Geçerli zamanlama ayarlarına göre bakım yapmayı yeniden dener ve sonraki bakım olayından beş gün önce bildirim gönderir.

## <a name="next-steps"></a>Sonraki adımlar

* [Bakım zamanlamasını değiştirme](howto-hyperscale-maintenance.md) hakkında bilgi edinin
* Azure hizmet durumunu kullanarak [gelecek bakımla ilgili bildirim alma](../service-health/service-notifications.md) hakkında bilgi edinin
* [Yaklaşan zamanlanmış bakım olayları hakkında uyarı ayarlamayı](../service-health/resource-health-alert-monitor-guide.md) öğrenin

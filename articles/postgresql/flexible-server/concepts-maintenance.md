---
title: Zamanlanan Bakım-PostgreSQL için Azure veritabanı-esnek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nda zamanlanmış bakım özelliği (esnek sunucu) açıklanmaktadır.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 50ef040f1cb7d8c533ec5ee31e9bffa2e6dca2f5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478021"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>MySQL için Azure Veritabanı - Esnek Sunucuda zamanlanmış bakım
 
PostgreSQL için Azure veritabanı-esnek sunucu, yönetilen veritabanınızı güvenli, kararlı ve güncel tutmak için düzenli bakım gerçekleştirir. Bakım sırasında sunucu yeni özellikler, güncelleştirmeler ve düzeltme ekleri alır.
 
> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir
 
## <a name="selecting-a-maintenance-window"></a>Bakım penceresi seçme
 
Belirli bir gün boyunca ve o gün içinde bir zaman penceresi için bakım zamanlayabilirsiniz. Ya da sistemin sizin yerinize otomatik olarak bir gün ve zaman penceresi seçmesini sağlayabilirsiniz. Her iki durumda da, sistem herhangi bir bakım çalıştırılmadan önce beş gün sonra uyarır. Sistem ayrıca bakım başladığında ve başarıyla tamamlandığında size bilgi verir.
 
Yaklaşan zamanlanmış bakım bildirimleri şu olabilir:
 
* Belirli bir adrese e-postayla
* Azure Resource Manager rolüne e-postayla
* Mobil cihazlara kısa mesajda (SMS) gönderilir
* Azure uygulamasına bildirim olarak gönderilerek
* Sesli mesaj olarak teslim edilerek
 
Bakım zamanlamasıyla ilgili tercihleri belirtirken haftanın gününü ve zaman aralığını belirleyebilirsiniz. Herhangi bir değer belirtmezseniz sistem, sunucunuzun bulunduğu bölgenin saatine göre akşam 11 ile sabah 7 arasında bir zaman seçer. Azure aboneliğinizdeki her esnek sunucu için farklı zamanlamalar tanımlayabilirsiniz. 
 
> [!IMPORTANT]
> Normalde bir sunucuda yapılacak başarılı zamanlanmış bakım olayları en az 30 gün önceden bildirilir.
>
> Ancak ciddi bir güvenlik açığı gibi kritik öneme sahip acil bir güncelleştirme söz konusu olduğunda beş günden daha kısa bir süre öncesinde bildirim yapılabilir. Son 30 gün içinde başarılı bir zamanlanmış bakım gerçekleştirilmiş olsa dahi sunucunuza kritik güncelleştirme uygulanabilir.

Zamanlama ayarlarını dilediğiniz zaman güncelleştirebilirsiniz. Esnek sunucunuz için zamanlanan bir bakım varsa ve zamanlama tercihlerini güncelleştirirseniz, geçerli dağıtım zamanlandığı şekilde devam eder ve zamanlama ayarları değişikliği, zamanlanan bir sonraki bakımın başarıyla tamamlanmasıyla etkili olur.

Azure aboneliğinizdeki her esnek sunucu için sistem tarafından yönetilen zamanlama veya özel zamanlama tanımlayabilirsiniz.  
* Özel zamanlamaya göre, haftanın gününü ve bir saatlik zaman penceresini seçerek sunucu için bakım pencerenizi belirtebilirsiniz.  
* Sistem tarafından yönetilen zamanlamaya göre sistem, sunucunuzun bölge saatinde 23.00 aralığındaki değerler ile 7 arasında bir saatlik pencere seçer.  

Değişiklikleri kullanıma almanın bir parçası olarak, önce sistem tarafından yönetilen zamanlamalarla yapılandırılan sunuculara, ardından belirli bir bölgede en az 7 günlük bir süre sonra özel zamanlamaya sahip sunuculara uygulanan güncelleştirmeleri uyguladık. Geliştirme ve test ortamı sunucularında daha erken güncelleştirmeler almayı düşünüyorsanız, geliştirme ve test ortamında kullanılan sunucular için sistem tarafından yönetilen zamanlamayı yapılandırmanızı öneririz. Bu, doğrulama için test ve değerlendirme için geliştirme ve test ortamınızda en son güncelleştirmeyi almanızı sağlar. Herhangi bir davranışla veya son değişiklikle karşılaşırsanız, aynı güncelleştirme, özel olarak yönetilen zamanlamalarla üretim sunucularına gönderilmeden önce bunları ele almanız gerekir. Güncelleştirme, 7 gün sonra özel zamanlamaya göre esnek sunucuları kullanıma açmaya başlar ve tanımlı bakım penceresinde sunucunuza uygulanır. Şu anda, bildirim gönderildikten sonra güncelleştirmeyi erteleme seçeneği yoktur. Özel-zamanlama yalnızca üretim ortamları için önerilir. 

Nadir durumlarda, bakım olayı sistem tarafından iptal edilebilir veya başarıyla tamamlanmayabilir. Güncelleştirme başarısız olursa, güncelleştirme geri döndürülür ve ikili dosyaların önceki sürümü geri yüklenir. Bu tür başarısız güncelleştirme senaryolarında bakım penceresi sırasında sunucunun yeniden başlatılmasına devam edebilirsiniz. Güncelleştirme iptal edilirse veya başarısız olduysa, sistem sizi bilgilendirirken, iptal edilen veya başarısız bakım olayı hakkında bir bildirim oluşturacaktır. Bakım gerçekleştirme girişimi, geçerli zamanlama ayarlarınıza göre zamanlanır ve bu işlem için beş gün daha önceden bildirim alırsınız. 

 
## <a name="next-steps"></a>Sonraki adımlar
 
* [Bakım zamanlamasını değiştirme](how-to-maintenance-portal.md) hakkında bilgi edinin
* Azure hizmet durumunu kullanarak [gelecek bakımla ilgili bildirim alma](../../service-health/service-notifications.md) hakkında bilgi edinin
* [Yaklaşan zamanlanmış bakım olayları hakkında uyarı ayarlamayı](../../service-health/resource-health-alert-monitor-guide.md) öğrenin

---
title: Planlı bakım bildirimi-MySQL için Azure veritabanı-tek sunucu
description: Bu makalede, MySQL için Azure veritabanı-tek sunucu 'da planlı bakım bildirimi özelliği açıklanmaktadır
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: c6d1bfbf1592da3a5e632eb875221225630aed3f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108681"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>MySQL için Azure veritabanı 'nda planlı bakım bildirimi-tek sunucu

MySQL için Azure veritabanı 'nda planlı bakım olaylarına hazırlanma hakkında bilgi edinin.

## <a name="what-is-a-planned-maintenance"></a>Planlı bakım nedir?

MySQL için Azure veritabanı hizmeti, temel alınan donanım, işletim sistemi ve veritabanı altyapısının otomatik düzeltme eki uygulamayı gerçekleştirir. Düzeltme Eki, yeni hizmet özellikleri, güvenlik ve yazılım güncelleştirmeleri içerir. MySQL altyapısı için, ikincil sürüm yükseltmeleri otomatik ve düzeltme eki uygulama döngüsünün bir parçası olarak dahil edilmiştir. Düzeltme için gereken kullanıcı eylemi veya yapılandırma ayarları yoktur. Düzeltme Eki, kapsamlı olarak test edilir ve güvenli dağıtım uygulamaları kullanılarak alınır.

Planlı bakım, bu hizmet güncelleştirmeleri belirli bir Azure bölgesindeki sunuculara dağıtıldığında bakım penceresidir. Planlı bakım sırasında, sunucularını barındıran Azure bölgesinde hizmet güncelleştirmesinin ne zaman dağıtılacağını müşteriye bildirmek için bir bildirim olayı oluşturulur. İki planlı bakım arasındaki en az süre 30 gündür. Sonraki bakım penceresi için 72 saat önce bildirim alırsınız.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Planlı bakım - süre ve müşteri etkisi

Belirli bir Azure bölgesi için planlı bakımın 15 saat çalıştırılması beklenir. Bu pencere, gerekirse bir geri alma planının yürütülmesi için arabellek süresini de içerir. Planlı bakım sırasında, veritabanı sunucusu yeniden başlatmaları veya yük devretme işlemleri, son kullanıcılar için veritabanı sunucularının kısa bir kullanım dışı kalmasına yol açabilir. MySQL için Azure veritabanı sunucuları, veritabanı sunucusu yeniden başlatmaları genellikle hızlı, genellikle 60-120 saniye içinde tamamlanmak üzere kapsayıcılarda çalışmaktadır. Her sunucu yeniden başlatmalarının dahil olduğu tüm planlı bakım olayı, mühendislik ekibi tarafından dikkatle izlenir. Sunucu yük devretmesi süresi, veritabanı kurtarma zamanına bağlıdır. Bu, sunucuda yük devretme sırasında ağır işlem etkinliğinizi varsa veritabanının daha uzun sürmesine neden olabilir. Yeniden başlatma zamanından daha uzun bir süre önlemek için planlı bakım olayları sırasında çalışan uzun işlemleri (toplu yükleme) önlemek önerilir.

Özet olarak, planlanan bakım olayı 15 saat boyunca çalışırken, tek tek sunucu etkisi, sunucudaki hareketsel etkinliğe bağlı olarak genellikle 60 saniye sürer. Planlı bakım başlamadan önce bir bildirim 72 takvim saati, belirli bir bölge için bakım devam ederken ise başka bir uyarı gönderilir.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Planlı bakımla ilgili nasıl bildirim alabilirim?

Planlı bakım bildirimleri özelliğini, yaklaşan bir planlı bakım olayına yönelik uyarılar almak için kullanabilirsiniz. Gelecekteki bakım 72 takvim saatleriyle ilgili bildirim, belirli bir bölge için bakım devam ederken olay ve başka bir durum hakkında bildirim alacaksınız.

### <a name="planned-maintenance-notification"></a>Planlı bakım bildirimi

> [!IMPORTANT]
> Planlı bakım bildirimleri şu anda tüm bölgelerde önizleme aşamasında mevcuttur Orta Batı ABD **hariç**

**Planlı bakım bildirimleri** , gelecek planlı bakım olayına yönelik uyarıları MySQL Için Azure veritabanınıza almanızı sağlar. Bu bildirimler [hizmet durumunun](../service-health/overview.md) planlanmış bakımıyla tümleşiktir ve abonelikleriniz için tüm zamanlanmış bakımı tek bir yerden görüntülemenize olanak sağlar. Ayrıca, farklı kaynaklardan sorumlu farklı kişileriniz olabileceğinden farklı kaynak grupları için bildirimin doğru kitlelere ölçeklendirilmesine de yardımcı olur. Olaydan önce yaklaşan bakım 72 takvim saatleriyle ilgili bildirimi alacaksınız.

Tüm olaylar için **Planlı bakım bildirimi** 72 saat bildirimi sağlamak için her girişimde bulunacağız. Bununla birlikte, kritik veya güvenlik düzeltme ekleri durumlarında, bildirim olaya yakın bir şekilde gönderilebilir veya atlanamaz.

Azure portal planlı bakım bildirimini denetleyebilir ya da uyarıları bildirim alacak şekilde yapılandırabilirsiniz. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Azure portal planlı bakım bildirimini denetle

1. [Azure Portal](https://portal.azure.com) **hizmet durumu**' nu seçin.
2. **Planlı bakım** sekmesini seçin
3. Planlı bakım bildirimini denetlemek istediğiniz **abonelik**, **bölge** ve **hizmeti** seçin. 
   
### <a name="to-receive-planned-maintenance-notification"></a>Planlı bakım bildirimi almak için

1. [Portalda](https://portal.azure.com) **hizmet durumu**' nu seçin.
2. **Uyarılar** bölümünde **sistem durumu uyarıları**' nı seçin.
3. **+ Hizmet sistem durumu uyarısı Ekle** ' yi seçin ve alanları girin.
4. Gerekli alanları doldurun. 
5. **Olay türünü** seçin, **Planlı bakım** ' ı seçin veya **tümünü seçin**
6. **Eylem gruplarında** , uyarıyı nasıl almak istediğinizi tanımlar (bir e-posta alın, mantıksal uygulama tetiklemesi vb.)  
7. Oluşturma sonrasında kuralın etkinleştir ' in Evet olarak ayarlandığından emin olun.
8. Uyarınızı gerçekleştirmek için **Uyarı kuralı oluştur** ' u seçin

**Hizmet durumu uyarıları** oluşturma hakkında ayrıntılı adımlar için, [hizmet bildirimlerinde etkinlik günlüğü uyarıları oluşturma](../service-health/alerts-activity-log-service-notifications-portal.md)konusuna bakın.

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>Planlı bakımı iptal edebilir veya erteleyebilir miyim?

Sunucunuzu güvenli, kararlı ve güncel tutmak için bakım yapmanız gerekir. Planlı bakım olayı iptal edilemez veya ertelenemez. Bildirim, belirli bir Azure bölgesine gönderildikten sonra, bu bölgedeki herhangi bir sunucu için düzeltme eki uygulama değişiklikleri yapılamaz. Düzeltme Eki, tüm bölge için aynı anda kullanıma alınır. MySQL için Azure veritabanı-tek sunucu hizmeti, hizmet için ayrıntılı denetim veya özelleştirme gerektirmeyen bulut Yerel uygulaması için tasarlanmıştır. Sunucularınız için bakım zamanlanmasını istiyorsanız [Esnek sunucuları](./flexible-server/overview.md)düşünmeniz önerilir.

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Tüm Azure bölgelerine düzeltme eki aynı zamanda mı uygulanır?

Hayır, dağıtım Wise pencere zamanlamaları sırasında tüm Azure bölgelerinin düzeltme eki uygulanır. Dağıtım Wise penceresi genellikle belirli bir Azure bölgesinde 5 PM-8 ile yerel saat olan bir sonraki günden uzatılır. Coğrafi eşlenmiş Azure bölgeleri, farklı günlerde düzeltme eki uygulanmış. Veritabanı sunucularının yüksek kullanılabilirlik ve iş sürekliliği için, [çapraz bölge okuma Çoğaltmalarından](./concepts-read-replicas.md#cross-region-replication) yararlanmak önerilir.

## <a name="retry-logic"></a>Yeniden deneme mantığı

Geçici arıza olarak da bilinen geçici bir hata, kendisini çözecek bir hatadır. Bakım sırasında [geçici hatalar](./concepts-connectivity.md#transient-errors) meydana gelebilir. Bu olayların çoğu, sistem tarafından 60 saniyeden daha az bir süre içinde otomatik olarak azalır. Geçici hatalar, [yeniden deneme mantığı](./concepts-connectivity.md#handling-transient-errors)kullanılarak işlenmelidir.


## <a name="next-steps"></a>Sonraki adımlar

- MySQL için Azure veritabanı ile çalışmaya yönelik herhangi bir soru veya öneri için, adresinden MySQL için Azure veritabanı ekibine bir e-posta gönderin. AskAzureDBforMySQL@service.microsoft.com
- Bir ölçüm üzerinde uyarı oluşturma konusunda rehberlik için [uyarıları ayarlama](howto-alert-on-metric.md) bölümüne bakın.
- [MySQL için Azure veritabanı 'na yönelik bağlantı sorunlarını giderme-tek sunucu](howto-troubleshoot-common-connection-issues.md)
- [MySQL için Azure veritabanı-tek sunucu için geçici hataları işleyin ve verimli bir şekilde bağlanın](concepts-connectivity.md)

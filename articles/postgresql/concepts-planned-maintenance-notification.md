---
title: Planlı bakım bildirimi-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nda planlı bakım bildirimi özelliği (tek sunucu) açıklanmaktadır
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 250ddf9f92559acd810aaed65d50ae45f2d7adc9
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227476"
---
# <a name="planned-maintenance-notification-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure Veritabanı - Tek Sunucu'da planlı bakım bildirimi

PostgreSQL için Azure veritabanı 'nda planlı bakım olaylarına hazırlanma hakkında bilgi edinin.

## <a name="what-is-a-planned-maintenance"></a>Planlı bakım nedir?

PostgreSQL için Azure veritabanı hizmeti, temel alınan donanım, işletim sistemi ve veritabanı altyapısının otomatik düzeltme eki uygular. Düzeltme Eki, yeni hizmet özellikleri, güvenlik ve yazılım güncelleştirmeleri içerir. PostgreSQL altyapısı için, ikincil sürüm yükseltmeleri otomatik ve düzeltme eki uygulama döngüsünün bir parçası olarak dahil edilmiştir. Düzeltme için gereken kullanıcı eylemi veya yapılandırma ayarları yoktur. Düzeltme Eki, kapsamlı olarak test edilir ve güvenli dağıtım uygulamaları kullanılarak alınır.

Planlı bakım, bu hizmet güncelleştirmeleri belirli bir Azure bölgesindeki sunuculara dağıtıldığında bakım penceresidir. Planlı bakım sırasında, sunucularını barındıran Azure bölgesinde hizmet güncelleştirmesinin ne zaman dağıtılacağını müşteriye bildirmek için bir bildirim olayı oluşturulur. İki planlı bakım arasındaki en az süre 30 gündür. Sonraki bakım penceresi için 72 saat önce bildirim alırsınız.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Planlı bakım - süre ve müşteri etkisi

Belirli bir Azure bölgesinin planlı bir bakımının genellikle 15 saat içinde tamamlandığı beklenmektedir. Bu zaman penceresinde, gerektiğinde geri alma planının yürütülmesi için arabellek süresi de bulunur. PostgreSQL için Azure veritabanı sunucuları, veritabanı sunucusunun yeniden başlatılması genellikle 60-120 saniye sürer, ancak bu 15 saat içinde bu süre içinde bilmeniz gereken belirleyici bir yol olmasa da sunucunuz etkilenecek. Her sunucu yeniden başlatmalarının dahil olduğu tüm planlı bakım olayı, mühendislik ekibi tarafından dikkatle izlenir. Sunucu yük devretme süresi, veritabanı kurtarmaya bağımlıdır ve bu, yük devretme sırasında sunucuda ağır işlem etkinliğinizi varsa veritabanının daha fazla çevrimiçi olmasına neden olabilir. Yeniden başlatma zamanından daha uzun bir süre önlemek için planlı bakım olayları sırasında çalışan uzun işlemleri (toplu yükleme) önlemek önerilir.

Özet olarak, planlanan bakım olayı 15 saat boyunca çalışırken, tek tek sunucu etkisi, sunucudaki hareketsel etkinliğe bağlı olarak genellikle 60 saniye sürer. Planlı bakım başlamadan önce bir bildirim 72 takvim saati, belirli bir bölge için bakım devam ederken ise başka bir uyarı gönderilir.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Planlı bakımla ilgili nasıl bildirim alabilirim?

Planlı bakım bildirimleri özelliğini, yaklaşan bir planlı bakım olayına yönelik uyarılar almak için kullanabilirsiniz. Gelecekteki bakım 72 takvim saatleriyle ilgili bildirim, belirli bir bölge için bakım devam ederken olay ve başka bir durum hakkında bildirim alacaksınız.

### <a name="planned-maintenance-notification"></a>Planlı bakım bildirimi

> [!IMPORTANT]
> Planlı bakım bildirimleri şu anda tüm bölgelerde önizleme aşamasında mevcuttur Orta Batı ABD **hariç**

**Planlı bakım bildirimleri** PostgreSQL Için Azure veritabanı 'na yaklaşan planlı bakım olayı için uyarı almanızı sağlar. Bu bildirimler [hizmet durumunun](../service-health/overview.md) planlanmış bakımıyla tümleşiktir ve abonelikleriniz için tüm zamanlanmış bakımı tek bir yerden görüntülemenize olanak sağlar. Ayrıca, farklı kaynaklardan sorumlu farklı kişileriniz olabileceğinden farklı kaynak grupları için bildirimin doğru kitlelere ölçeklendirilmesine de yardımcı olur. Olaydan önce yaklaşan bakım 72 takvim saatleriyle ilgili bildirimi alacaksınız.

Tüm olaylar için **Planlı bakım bildirimi** 72 saat bildirimi sağlamak için her girişimde bulunacağız. Bununla birlikte, kritik veya güvenlik düzeltme ekleri durumlarında, bildirim olaya yakın bir şekilde gönderilebilir veya atlanamaz.

Azure portal planlı bakım bildirimini denetleyebilir ya da uyarıları bildirim alacak şekilde yapılandırabilirsiniz. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Azure portal planlı bakım bildirimini denetle

1. [Azure Portal](https://portal.azure.com) **hizmet durumu**' nu seçin.
2. **Planlı bakım** sekmesini seçin
3. Planlı bakım bildirimini denetlemek istediğiniz **aboneliği**, * * bölgeyi ve **hizmeti** seçin. 
   
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

Sunucunuzu güvenli, kararlı ve güncel tutmak için bakım yapmanız gerekir. Planlı bakım olayı iptal edilemez veya ertelenemez. Bildirim, belirli bir Azure bölgesine gönderildikten sonra, bu bölgedeki herhangi bir sunucu için düzeltme eki uygulama değişiklikleri yapılamaz. Düzeltme Eki, tüm bölge için aynı anda kullanıma alınır. PostgreSQL için Azure veritabanı-tek sunuculu hizmet, hizmet için ayrıntılı denetim veya özelleştirme gerektirmeyen bulut Yerel uygulaması için tasarlanmıştır. Sunucularınız için bakım zamanlanmasını istiyorsanız [Esnek sunucuları](./flexible-server/overview.md)düşünmeniz önerilir.

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Tüm Azure bölgelerine düzeltme eki aynı zamanda mı uygulanır?

Hayır, dağıtım Wise pencere zamanlamaları sırasında tüm Azure bölgelerinin düzeltme eki uygulanır. Dağıtım Wise penceresi genellikle belirli bir Azure bölgesinde 5 PM-8 ile yerel saat olan bir sonraki günden uzatılır. Coğrafi eşlenmiş Azure bölgeleri, farklı günlerde düzeltme eki uygulanmış. Veritabanı sunucularının yüksek kullanılabilirlik ve iş sürekliliği için, [çapraz bölge okuma Çoğaltmalarından](./concepts-read-replicas.md#cross-region-replication) yararlanmak önerilir.

## <a name="retry-logic"></a>Yeniden deneme mantığı

Geçici arıza olarak da bilinen geçici bir hata, kendisini çözecek bir hatadır. Bakım sırasında [geçici hatalar](./concepts-connectivity.md#transient-errors) meydana gelebilir. Bu olayların çoğu, sistem tarafından 60 saniyeden daha az bir süre içinde otomatik olarak azalır. Geçici hatalar, [yeniden deneme mantığı](./concepts-connectivity.md#handling-transient-errors)kullanılarak işlenmelidir.


## <a name="next-steps"></a>Sonraki adımlar

- PostgreSQL için Azure veritabanı ile çalışmaya yönelik herhangi bir soru veya öneri için, adresinden PostgreSQL için Azure veritabanı ekibine bir e-posta gönderin. AskAzureDBforPostgreSQL@service.microsoft.com
- Bir ölçüm üzerinde uyarı oluşturma konusunda rehberlik için [uyarıları ayarlama](howto-alert-on-metric.md) bölümüne bakın.
- [PostgreSQL için Azure veritabanı ile ilgili bağlantı sorunlarını giderme-tek sunucu](howto-troubleshoot-common-connection-issues.md)
- [Geçici hataları işleyin ve PostgreSQL için Azure veritabanı 'na verimli bir şekilde bağlanın-tek sunucu](concepts-connectivity.md)

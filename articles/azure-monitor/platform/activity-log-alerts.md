---
title: Azure Izleyici 'de etkinlik günlüğü uyarıları
description: Etkinlik günlüğünde belirli olaylar meydana geldiğinde SMS, Web kancası, SMS, e-posta ve daha fazlası aracılığıyla bilgilendirilirsiniz.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/17/2018
ms.openlocfilehash: d3cb075d5ec0607453ca21f2574df7def02a4453
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553714"
---
# <a name="alerts-on-activity-log"></a>Etkinlik günlüğündeki uyarılar 

## <a name="overview"></a>Genel Bakış
Etkinlik günlüğü uyarıları, uyarıda belirtilen koşullara uyan yeni bir [etkinlik günlüğü olayı](activity-log-schema.md) gerçekleştiğinde etkinleştiği uyarılardır. [Azure etkinlik günlüğüne](activity-logs-overview.md)kaydedilen olayların sırasına ve hacmine bağlı olarak, uyarı kuralı başlatılır. Etkinlik günlüğü uyarı kuralları, Azure kaynaklarıdır, bu nedenle bir Azure Resource Manager şablonu kullanılarak oluşturulabilirler. Ayrıca, Azure portal oluşturulabilir, güncelleştirilemeyebilir veya silinebilirler. Bu makalede, etkinlik günlüğü uyarılarının arkasındaki kavramlar tanıtılmaktadır. Etkinlik günlüğü uyarı kurallarının oluşturulması veya kullanımı hakkında daha fazla bilgi için bkz. [etkinlik günlüğü uyarıları oluşturma ve yönetme](alerts-activity-log.md).

> [!NOTE]
> Etkinlik günlüğü uyarı kategorisinde olaylar **için uyarılar oluşturulamıyor** .

Genellikle, şu durumlarda bildirim almak için etkinlik günlüğü uyarıları oluşturursunuz:

* Azure aboneliğinizdeki kaynaklarda, genellikle belirli kaynak grupları veya kaynaklar kapsamındaki belirli işlemler meydana gelir. Örneğin, Myüretim Resourcegroup içindeki herhangi bir sanal makine silindiğinde bildirim almak isteyebilirsiniz. Ya da aboneliğinizdeki bir kullanıcıya herhangi bir yeni rol atanmışsa bildirim almak isteyebilirsiniz.
* Hizmet durumu olayı oluşur. Hizmet durumu olayları, aboneliğinizdeki kaynaklar için uygulanan olayların ve bakım olaylarının bildirimini içerir.

Etkinlik günlüğünde hangi uyarı kurallarının oluşturulabileceği koşullarını anlamak için basit bir benzerleme vurguladı, [Azure Portal Etkinlik günlüğü](activity-log-view.md#azure-portal)aracılığıyla olayları keşfetmeye veya filtrelemeye yöneliktir. Azure Izleyici-etkinlik günlüğünde, bir tane gerekli olayı filtreleyebilir veya bulabilir ve sonra **etkinlik günlüğü uyarısı Ekle** düğmesini kullanarak bir uyarı oluşturabilir.

Her iki durumda da, bir etkinlik günlüğü uyarısı yalnızca, uyarının oluşturulduğu abonelikteki olaylar için izler.

Etkinlik günlüğü olayı için JSON nesnesindeki herhangi bir üst düzey özelliğe göre etkinlik günlüğü uyarısı yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure etkinlik günlüğü 'Ne genel bakış](./activity-logs-overview.md#categories-in-the-activity-log). Hizmet durumu olayları hakkında daha fazla bilgi edinmek için bkz. [hizmet bildirimlerinde etkinlik günlüğü uyarıları alma](./alerts-activity-log-service-notifications.md). 

Etkinlik günlüğü uyarıları bazı yaygın seçeneklere sahiptir:

- **Kategori**: yönetim, hizmet durumu, otomatik ölçeklendirme, güvenlik, Ilke ve öneri. 
- **Kapsam**: etkinlik günlüğündeki uyarının tanımlandığı tek kaynak veya kaynak kümesi. Etkinlik günlüğü uyarısı kapsamı, çeşitli düzeylerde tanımlanabilir:
    - Kaynak düzeyi: Örneğin, belirli bir sanal makine için
    - Kaynak grubu düzeyi: Örneğin, belirli bir kaynak grubundaki tüm sanal makineler
    - Abonelik düzeyi: Örneğin, bir abonelikteki tüm sanal makineler (veya) bir abonelikteki tüm kaynaklar
- **Kaynak grubu**: varsayılan olarak, uyarı kuralı kapsamda tanımlanan hedefle aynı kaynak grubuna kaydedilir. Kullanıcı, uyarı kuralının saklanacağı kaynak grubunu da tanımlayabilir.
- **Kaynak türü**: Kaynak Yöneticisi uyarının hedefi için tanımlı ad alanı.
- **İşlem adı**: rol tabanlı Access Control için kullanılan [Azure Resource Manager işlem](../../role-based-access-control/resource-provider-operations.md) adı. Azure Resource Manager kayıtlı olmayan işlemler, etkinlik günlüğü uyarı kuralında kullanılamaz.
- **Düzey**: etkinliğin önem derecesi (ayrıntılı, bilgilendirici, uyarı, hata veya kritik).
- **Durum**: etkinliğin durumu, genellikle başlatıldı, başarısız veya başarılı.
- **Olay tarafından başlatılan**: "arayan" olarak da bilinir. İşlemi gerçekleştiren kullanıcının e-posta adresi veya Azure Active Directory tanımlayıcısı.

> [!NOTE]
> Bir abonelikte 100 ' e kadar uyarı kuralı, tek bir kaynak, kaynak grubundaki tüm kaynaklar (veya) tüm abonelik düzeyinde bir kapsam etkinliği için oluşturulabilir.

Etkinlik günlüğü uyarısı etkinleştirildiğinde, eylemler veya bildirimler oluşturmak için bir eylem grubu kullanır. Eylem grubu, e-posta adresleri, Web kancası URL 'Leri veya SMS telefon numaraları gibi yeniden kullanılabilir bir bildirim alıcıları kümesidir. Alıcıların bildirim kanallarınızı merkezileştirmek ve gruplamak için birden çok uyarıdan başvurulabilirler. Etkinlik günlüğü uyarısını tanımlarken iki seçeneğiniz vardır. Yapabilecekleriniz:

* Etkinlik günlüğü uyarısında mevcut bir eylem grubunu kullanın.
* Yeni bir eylem grubu oluşturun.

Eylem grupları hakkında daha fazla bilgi edinmek için [Azure Portal eylem grupları oluşturma ve yönetme](action-groups.md)konusuna bakın.


## <a name="next-steps"></a>Sonraki adımlar

- [Uyarılara genel bakış](alerts-overview.md)alın.
- [Etkinlik günlüğü uyarıları oluşturma ve değiştirme](alerts-activity-log.md)hakkında bilgi edinin.
- [Etkinlik günlüğü uyarısı Web kancası şemasını](activity-log-alerts-webhook.md)gözden geçirin.
- [Hizmet durumu bildirimleri](service-notifications.md)hakkında bilgi edinin.
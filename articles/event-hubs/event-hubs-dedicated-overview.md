---
title: Adanmış Olay Hub 'larına genel bakış-Azure Event Hubs | Microsoft Docs
description: Bu makalede, özel Azure Event Hubs, Olay Hub 'larının tek kiracılı dağıtımlarını sunan bir genel bakış sunulmaktadır.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: fe63b074bfdd01191d81dfb7024af5532a87a12f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310776"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Ayrılmış genel bakış

*Event Hubs kümeler* en zorlu akış gereksinimlerine sahip müşteriler için tek kiracılı dağıtımlar sağlar. Bu tek kiracılı teklifin% 99,99 bir SLA 'Sı vardır ve yalnızca adanmış fiyatlandırma katmanımızda kullanılabilir. Bir Event Hubs kümesi, garantili kapasiteye ve alt saniyelik gecikmeye göre saniyede milyonlarca olayı alabilir. Adanmış küme içinde oluşturulan ad alanları ve Olay Hub 'ları, standart teklifin tüm özelliklerini ve daha fazlasını içerir, ancak herhangi bir giriş sınırı olmadan. Ayrıca, ek ücret ödemeden popüler [Event Hubs yakalama](event-hubs-capture-overview.md) özelliğini de içerir. Bu özellik, Azure depolama 'ya veya Azure Data Lake veri akışlarını otomatik olarak toplu olarak günlüğe kaydetmenize olanak tanır. 

Kümeler, önceden ayrılmış bir CPU ve bellek kaynakları miktarı olan **Kapasite birimleri (cu düzeyinde kapsanır)** tarafından sağlanır ve faturalandırılır. Her küme için 1, 2, 4, 8, 12, 16 veya 20 cu düzeyinde kapsanır satın alabilirsiniz. Her CU için alma ve akış alma işlemi, aşağıdakiler gibi çeşitli etkenlere bağlıdır: 

- Üreticileri ve tüketici sayısı
- Yük şekli
- Çıkış oranı

> [!NOTE]
> Tüm Event Hubs kümeleri varsayılan olarak Kafka etkindir ve var olan Kafka tabanlı uygulamalarınız tarafından kullanılabilecek Kafka uç noktalarını destekler. Kümenizde Kafka etkin olma, Kafka olmayan kullanım örneklerini etkilemez; bir küme üzerinde Kafka devre dışı bırakılması gerekmez veya bu bir seçenek yoktur.

## <a name="why-dedicated"></a>Neden ayrılmış?

Adanmış Event Hubs, kurumsal düzeyde kapasiteye ihtiyaç duyan müşteriler için üç etkileyici avantaj sunar:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Tek kiracı, daha iyi performans için kapasiteyi garanti eder

Adanmış bir küme, tam ölçekte kapasiteyi garanti eder. Tam dayanıklı depolama ve trafikte herhangi bir veri bloğu için alt saniye gecikme süresine sahip gigabayt 'tan fazla akış verisi alabilir. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Özelliklere dahil ve özel erişim 
Adanmış teklif, hiçbir ek ücret ödemeden yakalama gibi özellikleri ve Kendi Anahtarını Getir (BYOK) gibi yakında sunulacak özelliklere özel erişimi içerir. Hizmet Ayrıca yük dengelemeyi, işletim sistemi güncelleştirmelerini, güvenlik düzeltme eklerini ve bölümleme işlemlerini yönetir. Bu nedenle, altyapı bakımı üzerinde daha az zaman harcayabilir ve istemci tarafı özellikleri oluşturmaya daha fazla zaman ayırabilirsiniz.  

#### <a name="cost-savings"></a>Maliyet tasarrufları
Yüksek giriş birimlerinde (>100 üretilen iş birimi), standart sunumdaki karşılaştırılabilir üretilen iş birimi miktarı satın almadan bir küme maliyeti saat başına önemli ölçüde küçüktür.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Ayrılmış kotaları ve limitleri

Event Hubs Ayrılmış teklif, en az 4 saatlik kullanım ile sabit bir aylık fiyatla faturalandırılır. Adanmış katman, standart planın tüm özelliklerini sunar, ancak yoğun iş yükleri olan müşterilere yönelik kurumsal ölçekli kapasite ve sınırlara sahiptir. 

| Özellik | Standart | Ayrılmış |
| --- |:---|:---|
| Bant genişliği | 20 s (40 'e kadar) | 20 cu düzeyinde kapsanır |
| Ad alanları |  1 | CU başına 50 |
| Event Hubs |  ad alanı başına 10 | ad alanı başına 1000 |
| Giriş olayları | Milyon olay başına ödeme | Dahil |
| İleti boyutu | 1.000.000 bayt | 1.000.000 bayt |
| Bölümler | Olay Hub 'ı başına 32 | Olay Hub 'ı başına 1024<br/>CU başına 2000 |
| Tüketici grupları | Olay Hub 'ı başına 20 | Her saniyedeki limit yok, Olay Hub 'ı başına 1000 |
| Aracılı bağlantılar | 1.000 dahil, en fazla 5.000 | 100 K dahil ve maks |
| [Olay saklama](event-hubs-features.md#event-retention) | 7 gün, TU başına 84 GB dahil | 90 gün, CU başına 10 TB dahildir |
| Yakalama | Saat başına ödeme | Dahil |

Daha fazla kota ve sınır için bkz. [Event Hubs kotalar ve sınırlar](event-hubs-quotas.md)

## <a name="how-to-onboard"></a>Ekleme

[Azure Portal](https://aka.ms/eventhubsclusterquickstart) aracılığıyla [bir Event Hubs kümesi oluşturmaya](event-hubs-dedicated-cluster-create-portal.md) yönelik self servis deneyimi artık önizlemededir. Event Hubs Ayrılmış ekleme konusunda sorularınız varsa veya yardıma ihtiyacınız varsa [Event Hubs ekibine](mailto:askeventhubs@microsoft.com)başvurun.

## <a name="faqs"></a>SSS

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Sonraki adımlar

Event Hubs Ayrılmış hakkında daha fazla bilgi edinmek için Microsoft satış temsilcinizle veya Microsoft Desteği başvurun. Ayrıca, aşağıdaki bağlantıları ziyaret ederek bir küme oluşturabilir veya Event Hubs fiyatlandırma katmanları hakkında daha fazla bilgi edinebilirsiniz:

- [Azure portal aracılığıyla Event Hubs kümesi oluşturma](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs ayrılmış fiyatlandırması](https://azure.microsoft.com/pricing/details/event-hubs/). Ayrıca, Event Hubs Ayrılmış kapasitesi hakkında ek ayrıntılar almak için Microsoft satış temsilcinizle veya Microsoft Desteği başvurabilirsiniz.
- [Event HUBS SSS](event-hubs-faq.yml) , fiyatlandırma bilgilerini içerir ve Event Hubs hakkında sık sorulan bazı sorulara yanıt verir.

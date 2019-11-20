---
title: Adanmış Olay Hub 'larına genel bakış-Azure Event Hubs | Microsoft Docs
description: Bu makalede, özel Azure Event Hubs, Olay Hub 'larının tek kiracılı dağıtımlarını sunan bir genel bakış sunulmaktadır.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516753"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Ayrılmış genel bakış

*Event Hubs kümeler* en zorlu akış gereksinimlerine sahip müşteriler için tek kiracılı dağıtımlar sağlar. %99,99 SLA garantisi veren bu tek kiracılı teklif sadece Ayrılmış fiyatlandırma katmanı için sunulur. Bir Event Hubs kümesi, garantili kapasite ve alt saniyelik gecikme süresiyle saniyede milyonlarca olayı alabilir. Adanmış küme içinde oluşturulan ad alanları ve Olay Hub 'ları, standart teklifin tüm özelliklerini ve daha fazlasını içerir, ancak herhangi bir giriş sınırı olmadan. Ayrıca, ek bir ücret ödemeden popüler [Event Hubs yakalama](event-hubs-capture-overview.md) özelliğini de içerir, böylece veri akışlarını otomatik olarak Azure depolama 'ya veya Azure Data Lake günlüğe kaydedebilirsiniz. 

Kümeler, önceden ayrılmış bir CPU ve bellek kaynakları miktarı olan **Kapasite birimleri (cu düzeyinde kapsanır)** tarafından sağlanır ve faturalandırılır. Her küme için 1, 2, 4, 8, 12, 16 veya 20 cu düzeyinde kapsanır satın alabilirsiniz. Her CU için alma ve akış alma işlemlerinin sayısı, üreticileri ve tüketiciler, yük şekli, çıkış oranı (daha fazla ayrıntı için aşağıdaki kıyaslama sonuçlarına bakın) gibi çeşitli faktörlere bağlıdır. 

> [!NOTE]
> Tüm Event Hubs kümeleri varsayılan olarak Kafka etkindir ve var olan Kafka tabanlı uygulamalarınız tarafından kullanılabilecek Kafka uç noktalarını destekler. Kümenizde Kafka etkin olma, Kafka olmayan kullanım örneklerini etkilemez; bir küme üzerinde Kafka devre dışı bırakılması gerekmez veya bu bir seçenek yoktur.

## <a name="why-dedicated"></a>Neden ayrılmış?

Adanmış Event Hubs, kurumsal düzeyde kapasiteye ihtiyaç duyan müşteriler için üç etkileyici avantaj sunar:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Tek kiracı, daha iyi performans için kapasiteyi garanti eder

Adanmış bir küme, tam ölçekte kapasiteyi garanti eder ve trafikte herhangi bir patlama sağlamak için tam olarak dayanıklı depolama ve alt saniyelik gecikme süresiyle çok sayıda akış verisi alabilir. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Özelliklere dahil ve özel erişim 
Adanmış teklif, ek bir ücret ödemeden yakalama gibi özellikleri ve Kendi Anahtarını Getir (BYOK) gibi yakında sunulacak özelliklere özel erişimi de içerir. Hizmet Ayrıca, altyapı bakımı üzerinde daha az zaman harcayabilmeniz ve istemci tarafı Özellikler oluştururken daha fazla zaman harcamanız için yük dengelemeyi, işletim sistemi güncelleştirmelerini, güvenlik düzeltme eklerini ve Bölümlendirmeyi yönetir.  

#### <a name="cost-savings"></a>Maliyet tasarrufları
Yüksek giriş birimlerinde (> 100 lik), standart sunumdaki karşılaştırılabilir üretilen iş birimi miktarı satın almadan bir küme maliyeti saat başına önemli ölçüde küçüktür.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Ayrılmış kotaları ve limitleri

Event Hubs Ayrılmış teklif, en az 4 saatlik kullanım ile sabit bir aylık fiyatla faturalandırılır. Adanmış katman, standart planın tüm özelliklerini sunar, ancak yoğun iş yükleri olan müşterilere yönelik kurumsal ölçekli kapasite ve sınırlara sahiptir. 

| Özellik | Standart | Adanmış |
| --- |:---:|:---:|
| Bant Genişliği | 20 s (40 'e kadar) | 20 cu düzeyinde kapsanır |
| Ad Alanları |  1 | CU başına 50 |
| Event Hubs |  ad alanı başına 10 | ad alanı başına 1000 |
| Giriş olayları | Milyon olay başına ödeme | Var |
| İleti Boyutu | 1\.000.000 bayt | 1\.000.000 bayt |
| Bölümler | Olay Hub 'ı başına 32 | Olay Hub 'ı başına 1024 |
| Tüketici grupları | Olay Hub 'ı başına 20 | Her saniyedeki limit yok, Olay Hub 'ı başına 1000 |
| Aracılı bağlantılar | 1\.000 dahil, en fazla 5.000 | 100 K dahil ve maks |
| İleti Saklama | 7 gün, TU başına 84 GB dahil | 90 gün, CU başına 10 TB dahildir |
| Yakalama | Saat başına ödeme | Var |

## <a name="how-to-onboard"></a>Ekleme

[Azure portalı](https://aka.ms/eventhubsclusterquickstart) aracılığıyla [bir Event Hubs kümesi oluşturmaya](event-hubs-dedicated-cluster-create-portal.md) yönelik self servis deneyimi artık önizlemededir. Sorularınız varsa veya Event Hubs Ayrılmış için yardıma ihtiyacınız varsa, lütfen [Event Hubs ekibine](mailto:askeventhubs@microsoft.com)başvurun.

## <a name="faqs"></a>SSS

#### <a name="what-can-i-achieve-with-a-cluster"></a>Bir küme ile neler elde edebilirim?

Bir Event Hubs kümesi için, alma ve akış alma işlemleri, üreticileri, Tüketicileriniz, geri alma ve işleme alma ücreti ve çok daha fazlası gibi çeşitli faktörlere bağlıdır. 

Aşağıdaki tabloda, sınamamız sırasında elde ettiğimiz kıyaslama sonuçları gösterilmektedir:

| Yük şekli | Alıcıların | Giriş bant genişliği| Giriş iletileri | Çıkış bant genişliği | Çıkış iletileri | Toplam DTU | CU başına DTU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB toplu işleri | 2 | 400 MB/sn | 400k İleti/sn | 800 MB/sn | 800k İleti/sn | 400 tüs | 100 tüs | 
| 10x10KB toplu işlem | 2 | 666 MB/sn | 66.6 k iletisi/sn | 1,33 GB/sn | 133k İleti/sn | 666 tüs | 166 tüs |
| 6x32KB toplu işleri | 1 | 1,05 GB/sn | 34k İleti/sn | 1,05 GB/sn | 34k İleti/sn | 1000 tüs | 250 tüs |

Sınamada, aşağıdaki ölçütler kullanılmıştır:

- Dört Kapasite birimi (cu düzeyinde kapsanır) içeren bir adanmış katman Event Hubs kümesi kullanıldı. 
- Alma işlemi için kullanılan olay hub 'ının 200 bölümü vardı. 
- Alınan veriler, tüm bölümlerden alınan iki alıcı uygulaması tarafından alındı.

#### <a name="can-i-scale-updown-my-cluster"></a>Kümemin ölçeğini artırma/azaltma yapabilir miyim?

Oluşturulduktan sonra, kümeler en az 4 saatlik kullanım için faturalandırılır. Self Servis deneyiminin önizleme sürümünde, Event Hubs ekibine Teknik > kota > bir [destek talebi](https://ms.portal.azure.com/#create/Microsoft.Support) göndererek, kümenizin ölçeğini yukarı veya aşağı ölçeklendirmek üzere *ayrılmış kümeyi ölçeğini büyütme veya küçültme isteğinde* bulunabilir. Kümenizi ölçeklendirmek için isteğin tamamlanması 7 güne kadar sürebilir. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Kümemdeki coğrafi ve DR nasıl çalışır?

Ayrılmış katman kümesi altında başka bir ad alanı ile ayrılmış katman kümesi altında bir ad alanını coğrafi olarak eşleştirin. Aktarım hızı sınırı uyumsuz olacağı için standart Teklifimizde bir ad alanı ile adanmış katman ad alanını eşleştirmeyi öneririz. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Standart ad alanlarımı adanmış katman kümesine ait olacak şekilde geçirebilir miyim?
Şu anda, Olay Hub 'larınızın verilerini standart bir ad alanından adanmış bir şekilde geçirmek için otomatik bir geçiş işlemini desteklemiyoruz. 

## <a name="next-steps"></a>Sonraki adımlar

Event Hubs Ayrılmış hakkında daha fazla bilgi edinmek için Microsoft satış temsilcinizle veya Microsoft Desteği başvurun. Ayrıca, aşağıdaki bağlantıları ziyaret ederek bir küme oluşturabilir veya Event Hubs fiyatlandırma katmanları hakkında daha fazla bilgi edinebilirsiniz:

- [Azure portalı aracılığıyla Event Hubs kümesi oluşturma](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs ayrılmış fiyatlandırması](https://azure.microsoft.com/pricing/details/event-hubs/). Ayrıca, Event Hubs Ayrılmış kapasitesi hakkında ek ayrıntılar almak için Microsoft satış temsilcinizle veya Microsoft Desteği başvurabilirsiniz.
- [Event HUBS SSS](event-hubs-faq.md) , fiyatlandırma bilgilerini içerir ve Event Hubs hakkında sık sorulan bazı sorulara yanıt verir.

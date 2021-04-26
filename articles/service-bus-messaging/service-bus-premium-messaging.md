---
title: Azure Service Bus Premium ve Standart katmanlar
description: Bu makalede, Azure Service Bus standart ve Premium katmanları açıklanmaktadır. Bu katmanları karşılaştırır ve teknik farklılıklar sağlar.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: b7117da6a959181704dd136c6d5be5ab62edef55
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389494"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus Premium ve Standart Mesajlaşma katmanları

Kuyruklar ve konu başlıkları gibi varlıkları içeren Service Bus Mesajlaşma, kuruluşun mesajlaşma işlevlerini bulut ölçeğinde zengin yayımla-abone ol semantiği ile birleştirir. Service Bus Mesajlaşması birçok gelişmiş bulut çözümü için iletişimin temel öğesi olarak kullanılır.

Service Bus mesajlaşma 'nın *Premium* katmanı, görev açısından kritik uygulamalar için ölçek, performans ve kullanılabilirlik bakımından ortak müşteri isteklerine yöneliktir. Üretim senaryoları için Premium katman önerilir. Özellikler kümeleri neredeyse aynı olsa da, Service Bus Mesajlaşma hizmetinin bu iki katmanı farklı kullanım durumlarına göre tasarlanmıştır.

Aşağıdaki tabloda bazı üst düzey farklılıklar vurgulanmıştır.

| Premium | Standart |
| --- | --- |
| Yüksek aktarım hızı |Değişken işleme |
| Tahmin edilebilir performans |Değişken gecikme süresi |
| Sabit fiyatlandırma |Kullandıkça Öde değişken fiyatlandırması |
| İş yükünün ölçeğini artırma veya azaltma |Yok |
| İleti boyutu 1 MB 'a kadar. Bu sınır gelecekte oluşturulabilir. Hizmette en son önemli güncelleştirmeler için bkz. [Azure blogda mesajlaşma](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog). |İleti boyutu 256 KB’a kadar |

**Service Bus Premium Mesajlaşma Hizmeti**, CPU'da ve bellek düzeyinde kaynak yalıtımına olanak sağladığından her müşterinin iş yükü yalıtımlı şekilde çalışır. Bu kaynak kapsayıcısı *mesajlaşma birimi* olarak adlandırılır. Her premium ad alanı, en az bir mesajlaşma birimi için ayrılmıştır. Her Service Bus Premium ad alanı için 1, 2, 4, 8 veya 16 mesajlaşma birimi satın alabilirsiniz. Tek bir iş yükü veya varlık birden çok mesajlaşma birimini kapsayabilir ve mesaj birimlerinin sayısı her zaman değiştirilebilir. Sonuç olarak, Service Bus tabanlı çözümünüz için tahmin edilebilir ve tekrarlanabilir bir performans elde edersiniz.

Daha tahmin edilebilir ve kullanılabilir olmasının yanı sıra bu performans, daha hızlıdır. Premium Mesajlaşma sayesinde, en yüksek performans Standart katmanda olduğundan daha hızlıdır.

> [!NOTE]
> Premium mesajlaşma için toplu iş boyutu sınırı 1 MB 'tır.

## <a name="premium-messaging-technical-differences"></a>Premium Mesajlaşmanın teknik farklılıkları

Aşağıdaki bölümlerde Premium ve Standart mesajlaşma katmanları arasındaki bazı farklar ele alınmaktadır.

### <a name="partitioned-queues-and-topics"></a>Bölümlenmiş kuyruklar ve konular

Bölümlenmiş kuyruklar ve konular Premium mesajlaşmada desteklenmez. Bölümleme hakkında daha fazla bilgi için bkz. [Bölümlenmiş kuyruklar ve konular](service-bus-partitioning.md).

### <a name="express-entities"></a>İfade varlıkları

Premium mesajlaşma, yalıtılmış bir çalışma zamanı ortamında çalıştığından, Premium ad alanlarında Express varlıkları desteklenmez. Bir Express varlığı kalıcı depolamaya yazmadan önce bellekte geçici bir ileti tutar. Standart Mesajlaşma altında çalışan kodunuz varsa ve bunu Premium katmanına bağlamak istiyorsanız, Express varlık özelliğinin devre dışı olduğundan emin olun.

## <a name="premium-messaging-resource-usage"></a>Premium mesajlaşma kaynak kullanımı
Genel olarak, bir varlıktaki herhangi bir işlem CPU ve bellek kullanımına neden olabilir. Bu işlemlerden bazıları şunlardır: 

- Kuyruklar, konular ve aboneliklerde CRUD (oluşturma, alma, güncelleştirme ve silme) işlemleri gibi yönetim işlemleri.
- Çalışma zamanı işlemleri (ileti gönderme ve alma)
- İşlemleri ve Uyarıları izleme

Ek CPU ve bellek kullanımı da buna ek olarak fiyatlandırılmıyor. Premium mesajlaşma katmanı için, ileti birimi için tek bir fiyat vardır.

CPU ve bellek kullanımı izlenir ve aşağıdaki nedenlerden dolayı size görüntülenir: 

- Sistem iç işlevleri için saydamlık sağlama
- Satın alınan kaynakların kapasitesini anlayın.
- Ölçeği artırma/azaltma kararı vermenize yardımcı olan kapasite planlaması.

## <a name="messaging-unit---how-many-are-needed"></a>Mesajlaşma Birimi-kaç tane gerekir?

Azure Service Bus Premium ad alanı sağlanırken, ayrılan mesajlaşma birimlerinin sayısı belirtilmelidir. Bu mesajlaşma birimleri, ad alanına ayrılan ayrılmış kaynaklardır.

Service Bus Premium ad alanına ayrılan mesajlaşma birimi sayısı, iş yüklerindeki değişikliğin (artış veya azaldıkça) **dinamik olarak** çarpılabilecek şekilde ayarlanabilir.

Mimariniz için Mesajlaşma Birimi sayısına karar verirken dikkate alınması gereken birkaç etken vardır:

- Ad alanınız için ayrılmış ***1 veya 2 mesajlaşma birimi*** ile başlayın.
- Ad alanınız için [kaynak kullanım ölçümleri](service-bus-metrics-azure-monitor.md#resource-usage-metrics) içindeki CPU kullanım ölçümlerini inceleyin.
    - CPU kullanımı ***%20**' nin altında ise, ad alanınız için ayrılan ileti birimi sayısı olan _ *_ölçeği_* azaltabilirsiniz.
    - CPU kullanımı **70% _**' den fazla olursa, uygulamanız _ *_ölçeklendirmeyi yukarı_**, ad alanınız için ayrılan mesajlaşma birimi sayısı kadar yararlı olacaktır.

Bir Service Bus ad alanını otomatik olarak ölçeklendirmek (ileti birimlerini artırma veya azaltma) için yapılandırma hakkında bilgi edinmek için bkz. [mesajlaşma birimlerini otomatik olarak güncelleştirme](automate-update-messaging-units.md).

> [!NOTE]
> Ad alanına ayrılan kaynakların **ölçeklendirilmesi** preemptive veya reaktif olabilir.
>
>  * **Preemptive**: ek iş yükü bekleniyorsa (mevsimsellik veya eğilimleri nedeniyle), iş yüklerinden önce ad alanına daha fazla mesajlaşma birimi ayırmaya devam edebilirsiniz.
>
>  * **Reaktif**: kaynak kullanım ölçümlerini sağlayarak ek iş yükleri tanımlanmışsa, artan talebi birleştirmek için ad alanına ek kaynaklar ayrılabilir.
>
> Service Bus için faturalandırma ölçüleri her saat için faturalandırılır. Ölçeği artırma durumunda, yalnızca bunların kullanıldığı saatlere ait Ek kaynaklar için ödeme yaparsınız.
>

## <a name="get-started-with-premium-messaging"></a>Premium Mesajlaşmayı kullanmaya başlama

Premium Mesajlaşma ile çalışmaya başlamak kolaydır ve süreç Standart Mesajlaşma ile benzerlik gösterir. [Azure Portal](https://portal.azure.com)'da [ad alanı oluşturarak](service-bus-create-namespace-portal.md) başlayın. **Fiyatlandırma katmanı** altında **Premium** ' u seçtiğinizden emin olun. Her bir katman hakkında daha fazla bilgi almak için **Fiyatlandırma ayrıntılarının tamamını görüntüle**'ye tıklayın.

![create-premium-namespace][create-premium-namespace]

Ayrıca [Azure Resource Manager şablonlarını kullanarak premium ad alanları](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/) oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus Mesajlaşma hizmeti hakkında daha fazla bilgi edinmek için aşağıdaki bağlantılara bakın:

- [Mesajlaşma birimlerini otomatik olarak güncelleştir](automate-update-messaging-units.md).
- [Azure Service Bus Premium mesajlaşma tanıtımı (blog gönderisi)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Azure Service Bus Premium mesajlaşma tanıtımı (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png

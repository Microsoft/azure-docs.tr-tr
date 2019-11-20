---
title: Azure Service Bus azaltmasına genel bakış | Microsoft Docs
description: Service Bus azaltmasına genel bakış-standart ve Premium katmanları.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: 21a3bfd09e83571e489e15e9351e12220a99e563
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301262"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Azure Service Bus azaltma işlemleri

Cloud Native Solutions, iş yüklerinizle ölçeklenebilen sınırsız kaynak kavramı sağlar. Bu kavram, bulutta şirket içi sistemlerle olduğundan daha doğru olsa da, bulutta mevcut olan sınırlamalar vardır.

Bu sınırlamalar, bu makalede anlatıldığı gibi standart ve Premium katmanlardaki istemci uygulama isteklerinin azaltılmasına neden olabilir. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Azure Service Bus Standart katmanda daraltma

Azure Service Bus standart katmanı, Kullandıkça Öde fiyatlandırma modeliyle çok kiracılı bir kurulum olarak çalışır. Aynı kümedeki birden çok ad alanı ayrılan kaynakları paylaşır. Standart katman, düşük işleme üretim sistemleriyle birlikte geliştirici, test ve QA ortamları için önerilen seçenektir.

Geçmişte Azure Service Bus, kaynak kullanımına kesinlikle bağlı olarak, büyük bir azaltma sınırlarına sahipti. Ancak, daraltma mantığını iyileştirmek ve bu kaynakları paylaşan tüm ad alanlarına öngörülebilir azaltma davranışı sağlamak için bir fırsat vardır.

Aynı kaynakları kullanan tüm Azure Service Bus standart ad alanları genelinde kaynak kullanımını ve bunların dağıtımını sağlamak için, azaltma mantığı kredi tabanlı olacak şekilde değiştirilmiştir.

> [!NOTE]
> Daraltma Azure Service Bus veya herhangi bir bulut Yerel hizmeti için ***Yeni*** bir değer olmadığına dikkat edin.
>
> Kredi tabanlı azaltma, çeşitli ad alanlarının çok kiracılı bir standart katman ortamında kaynakları paylaştığı ve bu sayede kaynakları paylaşan tüm ad alanları için dengeli kullanımı etkinleştiren şekilde iyileştirilmiştir.

### <a name="what-is-credit-based-throttling"></a>Kredi tabanlı azaltma nedir?

Kredi tabanlı azaltma, belirli bir dönemde belirli bir ad alanında gerçekleştirilebilecek işlem sayısını sınırlar. 

Kredi tabanlı azaltma için iş akışı aşağıda verilmiştir. 

  * Her dönemin başlangıcında, her bir ad alanı için belirli sayıda kredi sağlıyoruz.
  * Gönderen veya alıcı istemci uygulamaları tarafından gerçekleştirilen tüm işlemler, bu kredilerle (ve kullanılabilir kredilerden çıkarılan) hesaplanır.
  * Krediler tükendiğinde, sonraki işlemler bir sonraki dönemin başlangıcına kadar kısıtlanacak.
  * Krediler bir sonraki dönemin başlangıcında replenished.

### <a name="what-are-the-credit-limits"></a>Kredi limitleri nelerdir?

Kredi limitleri Şu anda her saniyede ' 1000 ' kredilerine ayarlanmıştır (ad alanı başına).

Tüm işlemler eşit olarak oluşturulmaz. İşlemlerin her biri için kredi maliyetleri aşağıda verilmiştir. 

| İşlem | Kredi maliyeti|
|-----------|-----------|
| Veri işlemleri (Send, Sendadsync, Receive, ReceiveAsync, Peek) |ileti başına 1 kredi |
| Yönetim işlemleri (oluşturma, okuma, güncelleştirme, kuyruklarda silme, konular, abonelikler, filtreler) | 10 kredi |

### <a name="how-will-i-know-that-im-being-throttled"></a>Kısıtlandığımı nasıl anlarım?

İstemci uygulama istekleri kısıtlanmakta olduğunda, uygulamanız tarafından aşağıdaki sunucu yanıtı alınır ve günlüğe kaydedilir.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Kısıtlanacak şekilde nasıl kaçınabilirim?

Paylaşılan kaynaklarla, bu kaynakları paylaşan çeşitli Service Bus ad alanları genelinde bazı bir tür kullanım uygulamak önemlidir. Kısıtlama, tek bir iş yükünde bulunan tüm ani değişikliklerin aynı kaynaklardaki diğer iş yüklerinin kısıtlanmasına neden olmamasını sağlar.

Makalede daha sonra bahsedildiği gibi, istemci SDK 'larının (ve diğer Azure PaaS tekliflerinin), bunlara yerleşik olarak bulunan varsayılan yeniden deneme ilkesi bulunduğundan, kısıtlanmakta olan risk yoktur. Tüm kısıtlanmış istekler üstel geri alma ile yeniden denenir ve krediler replenished olduğunda devam eder.

Daha anlaşılır, bazı uygulamaların kısıtlanacak şekilde hassas olması gerekebilir. Bu durumda, [geçerli Service Bus standart ad alanınızı Premium 'a geçirmeniz](service-bus-migrate-standard-premium.md)önerilir. 

Geçiş sırasında, Service Bus ad alanına adanmış kaynaklar ayırabilir ve iş yükünüzün bir ani artış varsa ve kısıtlanması olasılığını azaltabilmeniz durumunda kaynakları uygun şekilde ölçeklendirebilirsiniz. Ek olarak, iş yükünüz normal düzeylerine göre azaltıyorsa, ad alanınız için ayrılan kaynakları ölçeklendirebilirsiniz.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Azure Service Bus Premium katmanında daraltma

[Azure Service Bus Premium](service-bus-premium-messaging.md) katmanı, müşteri tarafından her bir ad alanı kurulumuna, mesajlaşma birimleri bakımından adanmış kaynakları ayırır. Bu adanmış kaynaklar, öngörülebilir aktarım hızı ve gecikme süresi sağlar ve yüksek aktarım hızı veya hassas üretim sınıfı sistemleri için önerilir.

Ayrıca, Premium katman müşterilerin iş yükünde ani artışlar yaştıklarında verimlilik kapasitesini ölçeklendirmelerini de sağlar.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Kısıtlama Service Bus Premium 'da nasıl çalışır?

Service Bus Premium için özel kaynak ayırma sayesinde, kısıtlama, ad alanına ayrılan kaynakların sınırlamaları ile tamamen çalıştırılır.

İstek sayısı geçerli kaynaklardan daha büyükse, istekler kısıtlanacak.

### <a name="how-will-i-know-that-im-being-throttled"></a>Kısıtlandığımı nasıl anlarım?

Azure Service Bus Premium 'da azaltmayı tanımlamaya yönelik çeşitli yollar vardır. 
  * **Kısıtlanmış istekler** , kaç tane istek olduğunu belirlemek Için [Azure izleyici isteği ölçümlerinde](service-bus-metrics-azure-monitor.md#request-metrics) gösterilir.
  * Yüksek **CPU kullanımı** , geçerli kaynak ayırmanın yüksek olduğunu ve geçerli iş yükü azalmıyorsa isteklerin kısıtlanamayacağını gösterir.
  * Yüksek **bellek kullanımı** , geçerli kaynak ayırmanın yüksek olduğunu ve geçerli iş yükü azalmıyorsa isteklerin kısıtlanamayacağını gösterir.

### <a name="how-can-i-avoid-being-throttled"></a>Kısıtlanacak şekilde nasıl kaçınabilirim?

Service Bus Premium ad alanı zaten adanmış kaynaklara sahip olduğundan, iş yükündeki bir ani bir depoda (veya olasılığına) ad alanına ayrılan mesajlaşma birimi sayısını ölçeklendirerek, azalmanın olasılığını azaltabilirsiniz.

Ölçeği artırma/azaltma, yukarıdaki ölçümlerde bulunan değişikliklerle tetiklenebilecek [runbook 'lar](../automation/automation-create-alert-triggered-runbook.md) oluşturularak elde edilebilir.

## <a name="faqs"></a>SSS

### <a name="how-does-throttling-affect-my-application"></a>Daraltma uygulamamı nasıl etkiler?

Bir istek kısıtlandığı zaman, kaynağın izin verenden daha fazla istek olduğu için hizmetin meşgul olduğunu gösterir. Aynı işlem birkaç dakika sonra yeniden denense, hizmet geçerli iş yükü aracılığıyla çalıştıktan sonra istek kabul edilebilir.

Daraltma, herhangi bir bulut Yerel hizmetinin beklenen davranışı olduğundan, yeniden deneme mantığını Azure Service Bus SDK 'sının kendine yapılandırdık. Her seferinde aynı isteğin kısıtlanıldığımızda emin olmak için, varsayılan değer üstel geri alarak otomatik yeniden denemeye ayarlanmıştır.

Varsayılan yeniden deneme mantığı her işlem için geçerlidir.

### <a name="does-throttling-result-in-data-loss"></a>Azaltma, veri kaybına neden olabilir mi?

Azure Service Bus, kalıcılık için en iyi duruma getirilmiştir, hizmet isteğin başarısını bildirmeden önce Service Bus gönderilen tüm verilerin depolama alanına yapıldığından emin olmanızı sağlar.

İstek başarılı bir şekilde ' ACK ' (onaylandı) Service Bus tarafından başarılı olduktan sonra, Service Bus isteği başarıyla işleyeceği anlamına gelir. Service Bus bir ' NACK ' (hata) döndürürse, Service Bus isteği işleyebilmediğinden ve istemci uygulamanın isteği yeniden denemesi gerektiğini belirtir.

Ancak, bir istek kısıtlandığı zaman hizmet, kaynak sınırlamaları nedeniyle isteği şu anda kabul edip işleyemediği konusunda bir işlemdir. Bu, Service Bus bir veri kaybı olduğunu göstermez çünkü bu, isteği yalnızca **bakmamıştır.** Bu durumda, Service Bus SDK 'nın varsayılan yeniden deneme ilkesine bağlı olarak isteğin sonunda işlenmesi sağlanır.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşma kullanma hakkında daha fazla bilgi ve örnekler için aşağıdaki gelişmiş konulara bakın:

* [Service Bus mesajlaşma hizmetine genel bakış](service-bus-messaging-overview.md)
* [Hızlı başlangıç: Azure portal ve .NET kullanarak ileti gönderme ve alma](service-bus-quickstart-portal.md)
* [Öğretici: Azure portal ve konuları/abonelikleri kullanarak envanteri güncelleştirme](service-bus-tutorial-topics-subscriptions-portal.md)


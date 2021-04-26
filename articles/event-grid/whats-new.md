---
title: Yenilikler Sürüm notları-Azure Event Grid
description: En son sürüm notları, bilinen sorunlar, hata düzeltmeleri, kullanım dışı işlevler ve yaklaşan değişiklikler gibi Azure Event Grid yenilikleri öğrenin.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: f135b25f28002c037dd24fa0cb3bb7476a06309f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389596"
---
# <a name="whats-new-in-azure-event-grid"></a>Azure Event Grid yenilikler nelerdir?

>Bu URL 'YI kopyalayarak ve yapıştırarak bu sayfanın ne zaman yeniden ziyaret ettikleridir hakkında bildirim alın: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` ![ RSS akışı okuyucu simgesi ](./media/whats-new/feed-icon-16x16.png) akış okuyucusuna.

Azure Event Grid iyileştirmeleri sürekli olarak alır. En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

- En son yayınlar
- Bilinen sorunlar
- Hata düzeltmeleri
- Kullanım dışı işlevsellik
- Değişiklik planları

## <a name="610-preview-2020-10"></a>6.1.0-Önizleme (2020-10)
- [Sistem için Yönetilen kimlikler konuları](enable-identity-system-topics.md)
- [Özel teslim özellikleri](delivery-properties.md)
- [Depolama kuyruğu-ileti yaşam süresi (TTL)](delivery-properties.md#configure-time-to-live-on-outgoing-events-to-azure-storage-queues)
- [Gelişmiş filtreleme geliştirmeleri](event-filtering.md#advanced-filtering)
    - Gelen olaylardaki dizi verilerinde filtrelemeyi destekleme
    - CloudEvents uzantıları bağlam özniteliklerinde filtrelemeye izin ver
    - Yeni işleçler
        - StringNotContains
        - StringNotBeginsWith
        - StringNotEndsWith
        - Numberınrange
        - NumberNotInRange
        - IsNull
        - IsNotNull
- [Özel konular ve etki alanları için Event Grid şemasının CloudEvents 1,0 şema dönüşümlerine izin ver](cloudevents-schema.md#configure-event-grid-for-cloudevents)
        

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Yeni genel kullanıma açık (GA) hizmet API 'SI sürüm 2020-06-01 ' e destek ekleyin.
- GA haline gelen yeni özellikler:
    - [Giriş eşlemeleri](input-mappings.md)
    - [Özel giriş şeması](input-mappings.md)
    - [Bulut olayı Ile v10 arasındaki şeması](cloud-event-schema.md)
    - [Service Bus konuyu hedef olarak](handler-service-bus.md)
    - [Hedef olarak Azure işlevi](handler-functions.md)
    - [Web kancası toplu işleme](./edge/delivery-output-batching.md)
    - [Güvenli Web kancası (Azure Active Directory destek)](secure-webhook-delivery.md)
    - [IP filtreleme](configure-firewall.md)
    - [Özel bağlantı hizmeti desteği](configure-private-endpoints.md)
    - [Olay teslimi şeması](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-Önizleme (2020-05)
- Bu sürüm, kaliteyi iyileştirmek için ek hata düzeltmeleri içerir.
- Sürüm 5.3.1-Preview olarak, bu sürüm aşağıdaki yeni işlevleri içeren 2020-04-01-Preview API sürümüne karşılık gelir: 
    - [Etki alanları ve konular için olayları yayımlarken IP filtreleme desteği](configure-firewall.md)
    - [İş ortağı konuları](./partner-events-overview.md)
    - [Azure portal 'da izlenen kaynaklar olarak sistem konuları](system-topics.md)
    - [Yönetilen hizmet kimliğiyle olay teslimi](managed-service-identity.md) 
    - [Özel bağlantı hizmeti desteği](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-Önizleme (2020-04)
- Bu sürüm, kaliteyi artırmak için çeşitli hata düzeltmeleri içerir.
- Sürüm 5.3.0-Preview olarak, bu sürüm aşağıdaki yeni işlevleri içeren 2020-04-01-Preview API sürümüne karşılık gelir: 
    - [Etki alanları ve konular için olayları yayımlarken IP filtreleme desteği](configure-firewall.md)
    - [İş ortağı konuları](./partner-events-overview.md)
    - [Azure portal 'da izlenen kaynaklar olarak sistem konuları](system-topics.md)
    - [Yönetilen hizmet kimliğiyle olay teslimi](managed-service-identity.md) 
    - [Özel bağlantı hizmeti desteği](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-Önizleme (2020-03)
- Sürüm 5.2.0-Preview ' da önceden eklenmiş özelliklerin üzerine yeni özellikler sunuyoruz. 
- Sürüm 5.2.0-Preview olarak, bu sürüm 2020-04-01-Preview API sürümüne karşılık gelir.
- Aşağıdaki yeni işlevlere destekler: 
    - [Etki alanları ve konular için olayları yayımlarken IP filtreleme desteği](configure-firewall.md)
    - [İş ortağı konuları](./partner-events-overview.md)
    - [Azure portal 'da izlenen kaynaklar olarak sistem konuları](system-topics.md)
    - [Yönetilen hizmet kimliğiyle olay teslimi](managed-service-identity.md) 
    - [Özel bağlantı hizmeti desteği](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-Önizleme (2020-01)
- Bu sürüm 2020-04-01-Preview API sürümüne karşılık gelir.
- Aşağıdaki yeni işlevlere destekler:
    - [Etki alanları ve konular için olayları yayımlarken IP filtreleme desteği](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Bu sürüm, `2019-06-01` API sürümüne karşılık gelir.
- Aşağıdaki yeni işlevlere destek ekler:
    * [Etki Alanları](event-domains.md)
    * Kaynak listeleme işlemleri için sayfalandırma ve arama filtresi. Bir örnek için bkz. [konulara göre liste listesi](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription).
    * [Kuyruğu hedef olarak Service Bus](handler-service-bus.md)
    * [Gelişmiş filtreleme](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-Önizleme (2019-03)
- Bu sürüm 2019-02-01-Preview API sürümüne karşılık gelir.
- Aşağıdaki yeni işlevlere destek ekler:
    * Kaynak listeleme işlemleri için sayfalandırma ve arama filtresi. Bir örnek için bkz. [konulara göre liste listesi](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription).
    * [Etki alanını el ile oluşturma/silme konuları](how-to-event-domains.md)
    * [Kuyruğu hedef olarak Service Bus](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Bu sürüm, `2019-01-01` KARARLı API sürümüne karşılık gelir.
- Olay abonelikleriyle ilgili aşağıdaki işlevlerin genel kullanılabilirliğini (GA) destekler:
    * [Atılacak mektup hedefi](manage-event-delivery.md)
    * [Hedef olarak Azure depolama kuyruğu](handler-storage-queues.md)
    * [Hedef olarak Azure Relay karma bağlantı](handler-relay-hybrid-connections.md)
    * [El sıkışma doğrulaması](webhook-event-delivery.md)
    * [Yeniden deneme ilkeleri desteği](delivery-and-retry.md)
- Hala önizlemede olan Özellikler ( [Event Grid etki alanları](event-domains.md) veya [Gelişmiş FILTRELER desteği](event-filtering.md#advanced-filtering) için SDK 'nın 3.0.1-Preview sürümü kullanılarak erişilebilir. "

## <a name="301-preview-2018-10"></a>3.0.1-Önizleme (2018-10)
- [Newtonsoft NuGet paketinin 10.0.3 sürümüne](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3)bağımlılık alınıyor.

## <a name="300-preview-2018-10"></a>3.0.0-Önizleme (2018-10)
- Bu sürüm, 2018-09-15-Preview API sürümünde tanıtılan yeni özellikler için bir önizleme SDK 'sına sahiptir. -Bu sürüm şunları destekler:
    - [Etki alanı ve etki alanı konuları](event-domains.md)
    - [Olay aboneliği için sona erme tarihi](concepts.md#event-subscription-expiration) tanıtımı
    - Olay abonelikleri için [Gelişmiş filtreleme](event-filtering.md#advanced-filtering) tanıtımı
    - API sürümünü hedefleyen SDK 'nın kararlı sürümü `2018-01-01` 1.3.0 sürümü olarak mevcut olmaya devam eder

## <a name="next-steps"></a>Sonraki adımlar
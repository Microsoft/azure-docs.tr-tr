---
title: İzleme Media Services
description: Media Services nasıl izleneceğini öğrenmek için Buradan başlayın
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 90ca92dc19c588d0b19adf009301cf844e0cdbde
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609059"
---
# <a name="monitor-media-services"></a>İzleyici Media Services

Azure kaynaklarına bağlı kritik Uygulamalarınız ve iş süreçleriniz olduğunda, bu kaynakları kullanılabilirlik, performans ve işlem için izlemek istersiniz. Bu makalede, Media Services tarafından oluşturulan izleme verileri ve bu verileri çözümlemek ve uyarmak için Azure Izleyici 'nin özelliklerini nasıl kullanabileceğiniz açıklanmaktadır.

## <a name="metrics-are-useful"></a>Ölçümler faydalıdır

İzleme Media Services ölçümlerinin uygulamalarınızın nasıl çalıştığını anlamanıza yardımcı olabilecek örnekler aşağıda verilmiştir. Media Services ölçümleriyle giderilebililecek bazı sorular şunlardır:

- Sınırları aşdığımda emin olmak için standart akış uç noktası mi Nasıl yaparım??
- Nasıl yaparım? en fazla Premium akış uç noktası ölçek birimi olup olmadığı hakkında bilgi sahibi misiniz?
- Akış uç noktalarımı ne zaman ölçeklendirebileceğinizi öğrenmek için bir uyarı ayarlayabilirim?
- Nasıl yaparım?, hesapta yapılandırılan en fazla çıkış ne zaman ulaşılırsa emin olmak için bir uyarı ayarladı musunuz?
- Başarısız olan isteklerin dökümünü nasıl görebilirim ve hataya neden olmuş olabilir?
- Paketleyiciyi kaç tane HLS veya DASH isteğinin çekmekte olduğunu nasıl görebilirim?
- Nasıl yaparım?, başarısız isteklerin eşik değerine ne zaman ulaştıysa haberdar olacak bir uyarı ayarladı musunuz?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Azure İzleyici nedir?

Media Services, Azure 'da, diğer bulutlardaki ve Şirket içindeki kaynaklara ek olarak Azure kaynaklarınızı izlemeye yönelik eksiksiz bir özellik kümesi sunan Azure [izleyici](../../../azure-monitor/overview.md)'yi kullanarak izleme verileri oluşturur.

Azure [izleme Ile Azure kaynaklarını](../../../azure-monitor/essentials/monitor-azure-resource.md)izleyen aşağıdaki kavramları açıklayan makaleyi okumaya başlayın:

- Azure İzleyici nedir?
- İzleme ile ilişkili maliyetler
- Azure 'da toplanan verileri izleme
- Veri toplamayı yapılandırma
- İzleme verilerini analiz etmek ve uyarı vermek için Azure 'da standart araçlar

## <a name="monitoring-data"></a>Verileri izleme

Media Services, [Azure kaynaklarından gelen verileri izleme](../../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)bölümünde açıklanan diğer Azure kaynaklarıyla aynı türde izleme verilerini toplar.

Azure İzleyici tarafından toplanan tüm veriler, ölçüm ve günlükler şeklindeki iki temel türden birine uyar. Bu iki tür ile şunları yapabilirsiniz:

- Ölçüm Gezgini 'ni kullanarak ölçüm verilerini görselleştirin ve çözümleyin.
- Media Services tanılama günlüklerini izleyin ve bunlar için uyarılar ve bildirimler oluşturun.
- Günlüklerle şunları yapabilirsiniz:
  - Azure depolama 'ya gönder
  - Azure Event Hubs akış
  - Log Analytics dışarı aktarın
  - Üçüncü taraf hizmetleri kullanma

Media Services tarafından oluşturulan ölçümler ve günlük ölçümleri hakkında ayrıntılı bilgi için [izleme Media Services veri başvurusunu izleme](monitor-media-services-data-reference.md) makalesine bakın.

## <a name="collection-and-routing"></a>Toplama ve yönlendirme

*Platform ölçümleri* ve *etkinlik günlüğü* otomatik olarak toplanır ve depolanır, ancak bir tanılama ayarı kullanılarak başka konumlara yönlendirilebilir.  

*Kaynak günlükleri* , bir tanılama ayarı oluşturup bunları bir veya daha fazla konuma yönlendirene **kadar toplanmaz ve** depolanmaz.

Azure portal, CLı veya PowerShell kullanarak bir tanılama ayarı oluşturmanın ayrıntılı süreci için [Azure 'da platform günlüklerini ve ölçümlerini toplamak üzere tanılama ayarı oluşturma](../../../azure-monitor/essentials/diagnostic-settings.md) makalesine bakın.

Bir tanılama ayarı oluşturduğunuzda hangi günlük kategorilerinin toplanacağını belirlersiniz. Media Services kategorileri [Media Services izleme veri başvurusunda](monitor-media-services-data-reference.md)listelenmiştir.

## <a name="analyzing-metrics"></a>Ölçümler çözümleniyor

**Azure izleyici** menüsünden **ölçümler** ' i açarak Ölçüm Gezgini 'ni kullanarak diğer Azure hizmetlerinden ölçümlerle Media Services için ölçümleri çözümleyebilirsiniz. Bu aracı kullanma hakkında ayrıntılı bilgi için bkz. [Azure Ölçüm Gezgini](../../../azure-monitor/essentials/metrics-getting-started.md) kullanmaya başlama.

Media Services için toplanan ölçümlerin bir listesi için bkz. [Media Services veri başvurusunu izleme](monitor-media-services-data-reference.md).

## <a name="analyzing-logs"></a>Günlükler çözümleniyor

Azure Izleyici günlüklerindeki veriler, her tablonun kendine ait benzersiz özellikler kümesine sahip olduğu tablolarda depolanır.  

Azure Izleyici 'deki tüm kaynak günlüklerine aynı alanlar ve hizmete özgü alanlar gelir. Ortak şema, [Azure izleyici kaynak günlüğü şemasında](../../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)özetlenmiştir.

Media Services kaynak günlükleri şeması, [izleme Media Services veri başvurusunda](monitor-media-services-data-reference.md)bulunur.

Etkinlik günlüğü, Azure 'da abonelik düzeyindeki olaylara ilişkin Öngörüler sağlayan bir platform [Günlüğliğidir](../../../azure-monitor/essentials/activity-log.md) . Bunu bağımsız olarak görüntüleyebilir veya Azure Izleyici günlüklerine yönlendirebilirsiniz, burada Log Analytics kullanarak çok daha karmaşık sorgular yapabilirsiniz.

Media Services için toplanan kaynak günlüklerinin türlerinin listesi için bkz. [izleme Media Services veri başvurusu](monitor-media-services-data-reference.md).

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Tanılama günlüklerini neden kullanmak istiyorum?

Tanılama günlükleri ile inceleyebileceğiniz bazı şeyler şunlardır:

- DRM türü tarafından sunulan lisansların sayısı.
- İlke tarafından teslim edilen lisansların sayısı.
- DRM veya ilke türüne göre hatalar.
- İstemcilerden gelen yetkisiz lisans isteği sayısı.

## <a name="alerts"></a>Uyarılar

İzleme verilerinizde önemli koşullar bulunduğunda Azure Izleyici uyarıları size önceden bildirimde bulunur. Bunlar, müşterilerinizin sorunları fark etmeden önce sisteminizdeki sorunları tanımlamanızı ve ele belirlemenizi sağlar. [Ölçümler](../../../azure-monitor/alerts/alerts-metric-overview.md), [Günlükler](../../../azure-monitor/alerts/alerts-unified-log.md)ve [etkinlik günlüğü](../../../azure-monitor/alerts/activity-log-alerts.md)hakkında uyarı ayarlayabilirsiniz.

Media Services ölçümler, değerin değişip değişmediğini düzenli aralıklarla toplanır. Bunlar, sık örneklenebilir olmaları nedeniyle uyarı için faydalıdır. Bir uyarı görece basit mantık ile hızlı bir şekilde tetiklenebilir.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]

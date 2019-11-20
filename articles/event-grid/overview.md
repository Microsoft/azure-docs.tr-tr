---
title: Azure Event Grid nedir?
description: Olay verileri bir kaynaktan işleyicisi Azure Event Grid ile gönderin. Olay tabanlı uygulamalar oluşturmanıza ve Azure Hizmetleri ile tümleştirme.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: 9c79a6ba1a6b7b5582c6430c6de7a4a309bf4920
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169840"
---
# <a name="what-is-azure-event-grid"></a>Azure Event Grid nedir?

Azure Event Grid, olay temelli mimarilerle kolayca uygulamalar derlemenize olanak tanır. İlk olarak, abone olun ve ardından olay işleyicisi veya olay göndermek için Web kancası uç noktası vermek istediğiniz Azure kaynağını seçin. Event Grid, depolama blobları ve kaynak grupları gibi Azure hizmetlerinden gelen olaylar için yerleşik destek sunar. Event Grid, özel konular kullanarak kendi olaylarınızı oluşturmanızı da destekler. 

Belirli olayları farklı uç noktalara yönlendirmek, birden fazla uç noktaya yayın yapmak ve olaylarınızın güvenilir bir şekilde teslim edildiğinden emin olmak üzere filtreleri kullanabilirsiniz.

Azure Event Grid, her bölgedeki birden çok hata etki alanına ve kullanılabilirlik bölgelerine (bunları destekleyen bölgelerde) yerel olarak yayılarak kullanılabilirliği en üst düzeye çıkarmak üzere dağıtılır. Event Grid tarafından desteklenen bölgelerin listesi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Bu makalede Azure Event Grid’e genel bir bakış sağlanmıştır. Event Grid kullanmaya başlamak istiyorsanız bkz. [Azure Event Grid ile özel olaylar oluşturma ve yönlendirme](custom-event-quickstart.md). 

![Olay ızgarası modeli kaynakları ve işleyicileri](./media/overview/functional-model.png)

Bu görüntü nasıl Event Grid kaynakları ve işleyicilerini bağlar ve desteklenen tümleştirmeler kapsamlı bir listesi değildir gösterir.

## <a name="event-sources"></a>Olay kaynakları

Kaynağın özellikleriyle ilgili tüm ayrıntılar ve ilgili makaleler için bkz. [olay kaynakları](event-sources.md). Şu anda Event Grid’e olay gönderme özelliği aşağıdaki Azure hizmetleri tarafından desteklenmektedir:

* [Azure abonelikleri (yönetim işlemleri)](event-sources.md#azure-subscriptions)
* [Container Registry](event-sources.md#container-registry)
* [Özel Konular](event-sources.md#custom-topics)
* [Event Hubs](event-sources.md#event-hubs)
* [IoT Hub’ı](event-sources.md#iot-hub)
* [Media Services](event-sources.md#media-services)
* [Kaynak grupları (yönetim işlemleri)](event-sources.md#resource-groups)
* [Service Bus](event-sources.md#service-bus)
* [Depolama Blobu](event-sources.md#storage)
* [Azure Haritalar](event-sources.md#maps)

## <a name="event-handlers"></a>Olay işleyicileri

İşleyicinin özellikleriyle ilgili tüm ayrıntılar ve ilgili makaleler için bkz. [olay işleyicileri](event-handlers.md). Şu anda Event Grid’den olay işleme özelliği aşağıdaki Azure hizmetleri tarafından desteklenmektedir: 

* [Azure Otomasyonu](event-handlers.md#azure-automation)
* [Azure İşlevleri](event-handlers.md#azure-functions)
* [Event Hubs](event-handlers.md#event-hubs)
* [Karma Bağlantılar](event-handlers.md#hybrid-connections)
* [Logic Apps](event-handlers.md#logic-apps)
* [Güç otomatikleştirme (eskiden Microsoft Flow olarak bilinirdi)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Kuyruk depolama](event-handlers.md#queue-storage)
* [Service Bus](event-handlers.md#service-bus)
* [Web kancaları](event-handlers.md#webhooks)

## <a name="concepts"></a>Kavramlar

Azure Event Grid’de başlangıç yapmanızı sağlayan beş kavram vardır:

* **Olaylar** - Ne olduğu.
* **Olay kaynakları** - Olayın gerçekleştiği yer.
* **Konu Başlıkları** - Yayımcıların olayları gönderdiği uç nokta.
* **Olay abonelikleri** -olayları yönlendirmek için uç nokta veya yerleşik mekanizma, bazen birden fazla işleyiciye. Abonelikler ayrıca işleyiciler tarafından gelen olayları akıllıca filtrelemek için de kullanılır.
* **Olay işleyicileri** - Olaya tepki veren uygulama ya da hizmet.

Bu kavramlar hakkında daha fazla bilgi için bkz. [Azure Event Grid’de Kavramlar](concepts.md).

## <a name="capabilities"></a>Özellikler

Azure Event Grid’in önemli özelliklerinden bazıları şunlardır:

* **Basitlik** - Azure kaynağınızdan herhangi bir olay işleyici ya da uç noktaya olayları hedeflemek için üzerine gelip tıklayın.
* **Gelişmiş filtreleme** -filtre olay türü veya olay, olay işleyicileri, yalnızca ilgili olayları aldığınızdan emin olmak için yol yayımlama.
* **Yayma** -birkaç uç noktayı gerektiğinde sayıda basamağa olay kopyasını göndermek için aynı olaya abone olun.
* **Güvenilirlik** -olayları teslim edilir emin olmak için bir üstel geri alma ile 24 saat yeniden deneyin.
* **Olay başına ödeme** - Yalnızca Event Grid’i kullandığınız miktar için ödeme yapın.
* **Yüksek aktarım hızı** - Saniyede milyonlarca olay desteği ile Event Grid’de yüksek hacimli iş yükleri oluşturun.
* **Yerleşik Olaylar** - Kaynak tarafından tanımlanan yerleşik olaylarla hızlıca çalışmaya başlayın.
* **Özel Olaylar** - Event Grid’i kullanarak uygulamanızda özel olayları yönlendirin, filtreleyin ve güvenilir bir şekilde teslim edin.

Event Grid, Event Hubs ve Service Bus hizmetlerinin bir karşılaştırması için bkz. [İleti teslim eden Azure hizmetleri arasında seçim yapma](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Event Grid ile ne yapabilirim?

Azure Event Grid sunucusuz, Ops otomasyonu ve [tümleştirme](https://azure.com/integration) çalışmasını büyük ölçüde çeşitli özellikler sağlar: 

### <a name="serverless-application-architectures"></a>Sunucusuz uygulama mimarileri

![Sunucusuz uygulama mimarisi](./media/overview/serverless_web_app.png)

Event Grid, veri kaynaklarını ve olay işleyicilerini bağlar. Örneğin, bir blob depolama kapsayıcısına eklenen görüntülerini inceler sunucusuz bir işlev tetiklemek için Event grid'i kullanın. 

### <a name="ops-automation"></a>İşlem Otomasyonu

![Operasyon otomasyonu](./media/overview/Ops_automation.png)

Event Grid, otomasyonu hızlandırmanızı ve ilke uygulamayı basitleştirmenizi sağlar. Örneğin, bir sanal makine veya SQL veritabanı oluşturulduğunda Azure Otomasyonu bildirmek için Event grid'i kullanın. Meta veri, operasyon araçlarına, sanal makineleri etiketlemek ve iş öğelerini yerleştirin otomatik olarak hizmet yapılandırmalarının uyumlu olup olmadığını denetlemek için olayları kullanın.

### <a name="application-integration"></a>Uygulama tümleştirme

![Azure ile uygulama tümleştirme](./media/overview/app_integration.png)

Event Grid, uygulamanızı diğer hizmetlere bağlar. Örneğin, uygulamanızın olay verilerini Event Grid’e göndermek için özel bir konu başlığı oluşturup Azure ile doğrudan tümleştirme, güvenilir teslim ve gelişmiş yönlendirme özelliklerinden yararlanabilirsiniz. Ya da kod yazmadan her yerden, işlem veri Event grid'i Logic Apps ile kullanabilirsiniz. 

## <a name="how-much-does-event-grid-cost"></a>Event Grid’in maliyeti ne kadardır?

Azure Event Grid, olay başına ödeme fiyatlandırma modeli kullanır, bu nedenle yalnızca kullandığınız kadar ödeme yaparsınız. Her ay ilk 100.000 işlem ücretsizdir. İşlemler olay girişi, abonelik teslim denemeleri, yönetim çağrıları ve konu sonekine göre filtreleme olarak tanımlanır. Ayrıntılar için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Sonraki adımlar

* [Depolama Blobu olaylarını yönlendirme](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Event Grid kullanarak depolama blobu olaylarına yanıt verin.
* [Özel olay oluşturma ve özel olaylara abone olma](custom-event-quickstart.md)  
  Azure Event Grid hızlı başlangıcını kullanarak hemen giriş yapın ve kendi özel olaylarınızı herhangi bir uç noktaya göndermeye başlayın.
* [Olay İşleyicisi olarak Logic Apps kullanma](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Event Grid tarafından gönderilen olaylara yanıt vermek üzere Logic Apps kullanarak uygulama derleme öğreticisi.
* [Veri ambarına büyük veri akışı yapma](event-grid-event-hubs-integration.md)  
  Azure İşlevleri’ni kullanarak Event Hubs’dan SQL Veri Ambarı’na veri akışı yapan öğretici.
* [Event Grid REST API başvurusu](/rest/api/eventgrid)  
  Olay Aboneliklerini yönetmek için başvuru içeriği sağlar Yönlendirme ve filtreleme.

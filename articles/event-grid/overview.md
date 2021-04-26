---
title: Azure Event Grid nedir?
description: Azure Event Grid olan bir kaynaktan işleyicilere olay verileri gönderin. Olay tabanlı uygulamalar oluşturun ve Azure hizmetleriyle tümleştirin.
ms.topic: overview
ms.date: 01/28/2021
ms.openlocfilehash: a410f56da9e28403fb98d92332e6d67c064bac4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934877"
---
# <a name="what-is-azure-event-grid"></a>Azure Event Grid nedir?

Azure Event Grid, olay temelli mimarilerle kolayca uygulamalar derlemenize olanak tanır. İlk olarak, abone olmak istediğiniz Azure kaynağını seçin ve olay işleyicisine veya Web kancası uç noktasına olayı göndermek için verin. Event Grid, depolama blobları ve kaynak grupları gibi Azure hizmetlerinden gelen olaylar için yerleşik destek sunar. Event Grid, özel konular kullanarak kendi olaylarınızı oluşturmanızı da destekler. 

Belirli olayları farklı uç noktalara yönlendirmek, birden çok uç noktaya yayın yapmak ve olaylarınızın güvenilir bir şekilde teslim edildiğinden emin olmak için filtreleri kullanabilirsiniz.

Azure Event Grid, her bölgedeki birden çok hata etki alanına ve kullanılabilirlik bölgelerine (bunları destekleyen bölgelerde) yerel olarak yayılarak kullanılabilirliği en üst düzeye çıkarmak üzere dağıtılır. Event Grid tarafından desteklenen bölgelerin listesi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Bu makalede Azure Event Grid’e genel bir bakış sağlanmıştır. Event Grid kullanmaya başlamak istiyorsanız bkz. [Azure Event Grid ile özel olaylar oluşturma ve yönlendirme](custom-event-quickstart.md). 

:::image type="content" source="./media/overview/functional-model.png" alt-text="Kaynak ve işleyicilerle Event Grid modeli" lightbox="./media/overview/functional-model-big.png":::

> [!NOTE]
> Bu görüntü, Event Grid kaynakları ve işleyicileri nasıl bağladığını gösterir ve desteklenen tümleştirmelerin kapsamlı bir listesi değildir. Desteklenen tüm olay kaynaklarının listesi için aşağıdaki bölüme bakın. 

## <a name="event-sources"></a>Olay kaynakları

Şu anda aşağıdaki Azure hizmetleri Event Grid ' ye olay göndermeyi destekler. Listedeki bir kaynak hakkında daha fazla bilgi için bağlantıyı seçin.

- [Azure Uygulama Yapılandırması](event-schema-app-configuration.md)
- [Azure Blob Depolama](event-schema-blob-storage.md)
- [Azure İletişim Hizmetleri](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Haritalar](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure İlkesi](./event-schema-policy.md)
- [Azure kaynak grupları](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure abonelikleri](event-schema-subscriptions.md)
- [Redis için Azure Önbelleği](event-schema-azure-cache.md)

## <a name="event-handlers"></a>Olay işleyicileri

İşleyicinin özellikleriyle ilgili tüm ayrıntılar ve ilgili makaleler için bkz. [olay işleyicileri](event-handlers.md). Şu anda Event Grid’den olay işleme özelliği aşağıdaki Azure hizmetleri tarafından desteklenmektedir: 

* [Azure Otomasyonu](handler-webhooks.md#azure-automation)
* [Azure İşlevleri](handler-functions.md)
* [Event Hubs](handler-event-hubs.md)
* [Geçiş Karma Bağlantılar](handler-relay-hybrid-connections.md)
* [Logic Apps](handler-webhooks.md#logic-apps)
* [Güç otomatikleştirme (eskiden Microsoft Flow olarak bilinirdi)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](handler-service-bus.md)
* [Kuyruk Depolama](handler-storage-queues.md)
* [Web kancaları](handler-webhooks.md)

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
* **Gelişmiş filtreleme** -olay işleyicilerinin yalnızca ilgili olayları aldığından emin olmak için olay türü veya olay yayımlama yolunu filtreleyin.
* **Fanı-Out** -etkinliğin kopyalarını gereken sayıda yere göndermek için aynı olaya birkaç uç noktayı abone yapın.
* **Güvenilirlik** -24 saat üstel geri alma ile yeniden dener ve olayların teslim edildiğinden emin olun.
* **Olay başına ödeme** - Yalnızca Event Grid’i kullandığınız miktar için ödeme yapın.
* **Yüksek verimlilik** -Event Grid yüksek hacimli iş yükleri oluşturun.
* **Yerleşik Olaylar** - Kaynak tarafından tanımlanan yerleşik olaylarla hızlıca çalışmaya başlayın.
* **Özel olaylar** -uygulamanızdaki özel olayları yönlendirmek, filtrelemek ve güvenilir bir şekilde teslim etmek için Event Grid kullanın.

Event Grid, Event Hubs ve Service Bus hizmetlerinin bir karşılaştırması için bkz. [İleti teslim eden Azure hizmetleri arasında seçim yapma](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Event Grid ile ne yapabilirim?

Azure Event Grid sunucusuz, Ops otomasyonu ve [tümleştirme](https://azure.com/integration) çalışmasını büyük ölçüde çeşitli özellikler sağlar: 

### <a name="serverless-application-architectures"></a>Sunucusuz uygulama mimarileri

![Sunucusuz uygulama mimarisi](./media/overview/serverless_web_app.png)

Event Grid, veri kaynaklarını ve olay işleyicilerini bağlar. Örneğin, bir BLOB depolama kapsayıcısına eklendiğinde görüntüleri çözümleyen sunucusuz bir işlev tetiklemek için Event Grid kullanın. 

### <a name="ops-automation"></a>İşlem Otomasyonu

![Operasyon otomasyonu](./media/overview/Ops_automation.png)

Event Grid, otomasyonu hızlandırmanızı ve ilke uygulamayı basitleştirmenizi sağlar. Örneğin, Azure SQL 'de bir sanal makine veya veritabanı oluşturulduğunda Azure Otomasyonu 'na bildirmek için Event Grid kullanın. Hizmet yapılandırmalarının uyumlu olup olmadığını otomatik olarak denetlemek, işletim araçlarına meta veriler koymak, sanal makineleri etiketlemek veya dosya iş öğeleri için olayları kullanın.

### <a name="application-integration"></a>Uygulama tümleştirme

![Azure ile uygulama tümleştirmesi](./media/overview/app_integration.png)

Event Grid, uygulamanızı diğer hizmetlere bağlar. Örneğin, uygulamanızın olay verilerini Event Grid’e göndermek için özel bir konu başlığı oluşturup Azure ile doğrudan tümleştirme, güvenilir teslim ve gelişmiş yönlendirme özelliklerinden yararlanabilirsiniz. Ya da, kod yazmadan verileri her yerde işlemek için Logic Apps Event Grid kullanabilirsiniz. 

## <a name="how-much-does-event-grid-cost"></a>Event Grid’in maliyeti ne kadardır?

Azure Event Grid, olay başına ödeme fiyatlandırma modeli kullanır, bu nedenle yalnızca kullandığınız kadar ödeme yaparsınız. Her ay ilk 100.000 işlem ücretsizdir. İşlemler olay girişi, abonelik teslim denemeleri, yönetim çağrıları ve konu sonekine göre filtreleme olarak tanımlanır. Ayrıntılar için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Sonraki adımlar

* [Depolama Blobu olaylarını yönlendirme](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Event Grid kullanarak depolama blobu olaylarına yanıt verin.
* [Özel olay oluşturma ve özel olaylara abone olma](custom-event-quickstart.md)  
  Azure Event Grid hızlı başlangıcını kullanarak hemen giriş yapın ve kendi özel olaylarınızı herhangi bir uç noktaya göndermeye başlayın.
* [Olay İşleyicisi olarak Logic Apps kullanma](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Event Grid tarafından gönderilen olaylara yanıt vermek üzere Logic Apps kullanarak uygulama derleme öğreticisi.
* [Büyük verileri bir veri ambarına akışla aktarma](event-grid-event-hubs-integration.md)  
  Azure Işlevleri 'ni kullanarak Event Hubs Azure SYNAPSE Analytics 'e veri akışı sağlayan bir öğretici.
* [Event Grid REST API başvurusu](/rest/api/eventgrid)  
  Olay abonelikleri, Yönlendirme ve filtrelemeyi yönetmek için başvuru içeriği sağlar.
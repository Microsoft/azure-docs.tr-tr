---
title: Olayları ve iletileri yönlendirme-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS ile hizmet uç noktalarına olayları ve iletileri yönlendirmeye genel bakış
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: f2479d9f3e278d23d62275b667f78d1fd70dd151
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889700"
---
# <a name="routing-events-and-messages"></a>Yönlendirme olayları ve iletileri

IoT çözümleri genellikle depolama, analiz ve daha fazlasını içeren birkaç güçlü hizmeti kapsar. Bu makalede, Azure dijital TWINS uygulamalarının Azure Analytics, AI ve depolama hizmetlerine bağlanarak daha ayrıntılı Öngörüler ve işlevler vermesini açıklar.

## <a name="route-types"></a>Rota türleri  

Azure dijital TWINS, IoT olaylarını diğer Azure hizmetleriyle veya iş uygulamalarıyla bağlamak için iki yol sunar:

* **Azure dijital TWINS olaylarını yönlendirme**: uzamsal grafikteki değişiklik, alınan telemetri verileri veya önceden tanımlanmış koşullara dayalı bir bildirim oluşturan kullanıcı tanımlı bir Işlev Azure dijital TWINS olaylarını tetikleyebilirler. Kullanıcılar bu olayları daha fazla işleme için [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure Service Bus konularına](https://azure.microsoft.com/services/service-bus/)veya [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) gönderebilir.

* **Yönlendirme cihaz telemetrisi**: olayları yönlendirmeye ek olarak Azure dijital TWINS, daha ayrıntılı bilgiler ve analizler için ham cihaz telemetri iletilerini de Event Hubs yönlendirebilir. Bu tür iletiler Azure dijital TWINS tarafından işlenmez. Yalnızca Olay Hub 'ına iletilir.

Kullanıcılar, olayları göndermek veya iletileri iletmek için bir veya daha fazla çıkış uç noktası belirtebilir. Olaylar ve iletiler, bu önceden tanımlanmış yönlendirme tercihlerine göre uç noktalara gönderilir. Diğer bir deyişle, kullanıcılar, Graph işlem olaylarını almak için başka bir uç nokta belirtebilir, diğer bir deyişle cihaz telemetri olaylarını alabilir ve bu şekilde devam edebilir.

[Azure dijital TWINS olayları yönlendirme ![](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Event Hubs yönlendirme, telemetri iletilerinin gönderilme sırasını korur. Bu nedenle, son olarak alındıkları sırada uç noktaya ulaşır. 

Event Grid ve Service Bus uç noktaların olayları gerçekleşdikleri sırada almasını garanti etmez. Ancak olay şeması, olaylar uç noktaya ulaştığında sırayı belirlemek için kullanılabilecek bir zaman damgası içerir.

## <a name="route-implementation"></a>Yol uygulama

Azure dijital TWINS hizmeti şu anda aşağıdaki **Endpointtypes türlerini**desteklemektedir:

* **EventHub** Event Hubs bağlantı dizesi uç noktasıdır.
* **ServiceBus** , Service Bus bağlantı dizesi uç noktasıdır.
* **Eventgrid** , Event Grid bağlantı dizesi uç noktasıdır.

Azure dijital TWINS Şu anda seçili uç noktaya gönderilecek olan aşağıdaki **eventTypes** 'ı desteklemektedir:

* **Devicemessages** , kullanıcıların cihazlarından gönderilen ve sistem tarafından iletilen telemetri iletilerdir.
* **Topologyoperation** , grafiğin grafiğini veya meta verilerini değiştiren bir işlemdir. Örneğin boşluk gibi bir varlık ekleme veya silme.
* **Spacechange** , bir alanın hesaplanan değerindeki bir cihaz telemetri iletisinden kaynaklanan bir değişiklikten oluşur.
* **Sensorchange** , bir algılayıcının hesaplanan değerindeki bir cihaz telemetri iletisinden kaynaklanan bir değişiklikten oluşur.
* **Udfcustom** , Kullanıcı tanımlı bir işlevden özel bir bildirimdir.

> [!IMPORTANT]  
> Tüm **Endpointtypes** tüm **eventTypes**'ı desteklemez.
> Her bir **EndpointType**için Izin verilen **eventTypes** için aşağıdaki tabloya bakın.

|             | DeviceMessages Iletileri | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Uç noktaların oluşturulması ve olayların şeması örnekleri hakkında daha fazla bilgi için bkz. [Çıkış ve uç noktalar](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure dijital TWINS önizleme sınırları hakkında bilgi edinmek için bkz. [Genel Önizleme hizmeti sınırları](concepts-service-limits.md).

- Bir Azure dijital TWINS örneği denemek için, [kullanılabilir odaları bulmak için hızlı](quickstart-view-occupancy-dotnet.md)başlangıca bakın.
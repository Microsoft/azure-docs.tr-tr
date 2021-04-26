---
title: Event Grid kullanarak Azure Maps olaylarına tepki verme
description: Bölge sınırlaması içeren Azure haritalar olaylarına tepki verme hakkında bilgi edinin. Olayları eşlemek için dinleme yapma ve olayları olay işleyicilerine yeniden yönlendirme için Event Grid kullanma konusuna bakın.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 44b2cab814d4e51c4eb0c3ce3322b898f5b26414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92889775"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Event Grid kullanarak Azure Maps olaylarına tepki verme

Azure Maps, kullanıcıların diğer hizmetlere olay bildirimleri gönderebilmesi ve aşağı akış süreçlerini tetikleyebilmeleri için Azure Event Grid ile tümleşir. Bu makalenin amacı, Azure Maps olaylarını dinlemek için iş uygulamalarınızı yapılandırmanıza yardımcı olur. Bu, kullanıcıların kritik olaylara güvenilir, ölçeklenebilir ve güvenli bir şekilde tepki vermesini sağlar. Örneğin, kullanıcılar bir cihaz bölge alanı her girdiğinde bir veritabanını güncelleştirmek, Bilet oluşturmak ve bir e-posta bildirimi göndermek için bir uygulama oluşturabilir.

Azure Event Grid, yayımlama-abonelik modeli kullanan, tam olarak yönetilen bir olay yönlendirme hizmetidir. Event Grid [Azure işlevleri](../azure-functions/functions-overview.md) ve [Azure Logic Apps](../azure-functions/functions-overview.md)gibi Azure hizmetleri için yerleşik desteğe sahiptir. Web kancalarını kullanarak Azure olmayan hizmetlere olay uyarıları sunabilir. Event Grid desteklediği olay işleyicilerinin tüm listesi için bkz. [Azure Event Grid giriş](../event-grid/overview.md).


![Azure Event Grid işlevsel modeli](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure haritalar olay türleri

Olay Kılavuzu, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../event-grid/concepts.md#event-subscriptions) kullanır. Azure haritalar hesabı aşağıdaki olay türlerini yayar: 

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. maps. Geofencegirildi | Alınan Koordinatlar, belirli bir bölge dışından, içindeki ' dan ' a taşındığında tetiklenir |
| Microsoft. maps. Geofenceçıkıldı | Alınan koordinatlar belirli bir bölge içinden dışarıya taşındığında tetiklenir |
| Microsoft. maps. GeofenceResult | Bölge sınırlama sorgusu, durumdan bağımsız olarak bir sonuç döndürdüğünde tetiklenir |

## <a name="event-schema"></a>Olay şeması

Aşağıdaki örnek, GeofenceResult için şemayı gösterir:

```JSON
{
    "id":"451675de-a67d-4929-876c-5c2bf0b2c000",
    "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}",
    "subject":"/spatial/geofence/udid/{udid}/id/{eventId}",
    "data":{
        "geometries":[
            {
                "deviceId":"device_1",
                "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169",
                "geometryId":"1",
                "distance":999.0,
                "nearestLat":47.609833,
                "nearestLon":-122.148274
            }
        ],
        "expiredGeofenceGeometryId":[
        ],
        "invalidPeriodGeofenceGeometryId":[
        ]
    },
    "eventType":"Microsoft.Maps.GeofenceResult",
    "eventTime":"2018-11-08T00:52:08.0954283Z",
    "metadataVersion":"1",
    "dataVersion":"1.0"
}

```

## <a name="tips-for-consuming-events"></a>Olayları kullanma ipuçları

Azure haritalar bölge olaylarını işleyen uygulamalar, önerilen birkaç uygulamayı izlemelidir:

* Olayları aynı olay işleyicisine yönlendirmek için birden çok abonelik yapılandırın. Olayların belirli bir kaynaktan olduğunu varsaymamak önemlidir. İletinin, beklediği kaynaktan geldiğinden emin olmak için her zaman ileti konusunu denetleyin.
* `X-Correlation-id`Nesneler hakkındaki bilgilerinizin güncel olup olmadığını anlamak için yanıt üstbilgisindeki alanı kullanın. İletiler karışık sırayla veya bir gecikmeden sonra gelebilir.
* Bölge sınırı API 'sindeki bir get veya post isteği olarak ayarlanan mod parametresi ile çağrıldığında `EnterAndExit` , durumun önceki bölge API çağrısından değiştiği bölge için her bir geometri için bir Enter veya Exit olayı oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

Bir oluşturma sitesindeki işlemleri denetlemek için bölge sınırlaması kullanma hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"] 
> [Azure haritalar 'ı kullanarak bölge kümesi oluşturma](tutorial-geofence.md)
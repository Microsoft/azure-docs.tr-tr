---
title: Azure haritalar Event Grid kaynak olarak
description: Azure Event Grid ile Azure Maps olayları için belirtilen özellikleri ve şemayı açıklar
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 88cf0c8274d685a45862bc7b7884b5e4a686c22d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363703"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Azure haritalar Event Grid kaynak olarak

Bu makalede, Azure Maps olayları için özellikler ve şema sağlanmaktadır. Olay şemalarına giriş için bkz. [Azure Event Grid olay şeması](./event-schema.md). Ayrıca, Azure haritalar 'ı bir olay kaynağı olarak kullanmak için hızlı başlayan ve öğreticilerin bir listesini sağlar.

## <a name="available-event-types"></a>Kullanılabilir olay türleri

Azure haritalar hesabı aşağıdaki olay türlerini yayar:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. maps. Geofencegirildi | Alınan Koordinatlar, belirli bir bölge sınırı 'ın içinden içine arasında taşındığında tetiklenir |
| Microsoft. maps. Geofenceçıkıldı | Alınan koordinatlar belirli bir bölge içinden dışarıya taşındığında tetiklenir |
| Microsoft. maps. GeofenceResult | Bölge sınırlama sorgusu, durumdan bağımsız olarak bir sonuç döndürdüğünde tetiklenir |

## <a name="example-events"></a>Örnek olaylar

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)
Aşağıdaki örnek, **Geofencegirildi** olayının şemasını gösterir

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

Aşağıdaki örnek **GeofenceResult** için şemayı gösterir 

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
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
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

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)
Aşağıdaki örnek, **Geofencegirildi** olayının şemasını gösterir

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceEntered", 
   "time":"2018-11-08T00:54:17.6408601Z", 
   "specversion":"1.0" 
}
```

Aşağıdaki örnek **GeofenceResult** için şemayı gösterir 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
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
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "type":"Microsoft.Maps.GeofenceResult", 
   "time":"2018-11-08T00:52:08.0954283Z", 
   "specversion":"1.0" 
}
```
---

## <a name="event-properties"></a>Olay özellikleri

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)
Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `topic` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `eventType` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `eventTime` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Bölge sınırlaması olay verileri. |
| `dataVersion` | string | Veri nesnesinin şema sürümü. Şema sürümünü yayımcı tanımlar. |
| `metadataVersion` | string | Olay meta verilerinin şema sürümü. Event Grid en üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)
Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `source` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `type` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `time` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Bölge sınırlaması olay verileri. |
| `specversion` | string | CloudEvents şema belirtimi sürümü. |

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `apiCategory` | dize | Etkinliğin API kategorisi. |
| `apiName` | string | Etkinliğin API adı. |
| `issues` | object | İşlem sırasında oluşan sorunları listeler. Herhangi bir sorun döndürülürse, yanıtla birlikte hiçbir geometriler döndürülmeyecektir. |
| `responseCode` | sayı | HTTP yanıt kodu |
| `geometries` | object | Koordinat konumunu içeren sınır geometrileri listeler veya konumun çevresindeki searchBuffer ile çakışıyor. |

Maps API 'sinde bir hata oluştuğunda hata nesnesi döndürülür. Hata nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `error` | ErrorDetails |Bu nesne, Haritalar API 'sinde bir hata oluştuğunda döndürülür  |

Maps API 'sinde bir hata oluştuğunda ErrorDetails nesnesi döndürülür. ErrorDetails veya nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `code` | dize | HTTP durum kodu. |
| `message` | string | Kullanılabiliyorsa, hatanın okunabilir bir açıklaması. |
| `innererror` | Innererror | Varsa, hatayla ilgili hizmete özel bilgileri içeren bir nesne. |

Innererror, hatayla ilgili hizmete özel bilgileri içeren bir nesnedir. Innererror nesnesi aşağıdaki özelliklere sahiptir: 

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `code` | dize | Hata iletisi. |

Geometriler nesnesi, istekteki Kullanıcı saatine göre süresi geçen bölge sayısının geometri kimliklerini listeler. Geometriler nesnesi, aşağıdaki özelliklere sahip geometri öğelerine sahiptir: 

| Özellik | Tür | Description |
|:-------- |:---- |:----------- |
| `deviceid` | dize | Cihazın KIMLIĞI. |
| `distance` | string | <p>Koordinatlardan bölge alanının en yakın kenarlığına olan uzaklık. Pozitif, koordinat bölge sayısının dışında olduğu anlamına gelir. Koordinat Bölge sınırının dışında, ancak en yakın bölge genişliğinden daha fazla searchBuffer değeri varsa, değer 999 ' dir. Negatif, koordinat bölge sayısının içinde olduğu anlamına gelir. Koordinat Çokgen içindeyse, ancak en yakın bölge sınırlaması kenarından daha fazla searchBuffer değerinden fazla olursa değer-999 olur. 999 değeri, koordinat bölge alanının dışında iyi bir güvenilirlik olduğu anlamına gelir. -999 değeri, koordinatın bölge kapsamında iyi olduğu anlamına gelir.<p> |
| `geometryid` |string | Benzersiz kimlik, bölge sınırı geometrisini tanımlar. |
| `nearestlat` | sayı | En yakın geometri noktası enlem. |
| `nearestlon` | sayı | Geometri 'nın en yakın noktasının boylam. |
| `udId` | string | Bölge, karşıya yüklenirken Kullanıcı karşıya yükleme hizmetinden döndürülen benzersiz KIMLIK. Bölge sınırlaması Post API 'sine dahil değildir. |

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `expiredGeofenceGeometryId` | String [] | İstekteki Kullanıcı saatine göre süresi dolmak üzere olan bölge sınırı 'ın geometri kimliği listesi. |
| `geometries` | geometriler [] |Koordinat konumunu içeren sınır geometrileri listeler veya konumun çevresindeki searchBuffer ile çakışıyor. |
| `invalidPeriodGeofenceGeometryId` | String []  | İstekteki Kullanıcı saatine göre geçersiz süre içinde olan bölge sınırı 'ın geometri kimliği listesi. |
| `isEventPublished` | boolean | Azure Maps olay abonesi 'nda en az bir olay yayımlandıysa true, Azure Maps olay abonesi 'nda hiçbir olay yayımlanmamışsa false. |

## <a name="tutorials-and-how-tos"></a>Öğreticiler ve nasıl yapılır kılavuzları
|Başlık  |Açıklama  |
|---------|---------|
| [Event Grid kullanarak Azure Maps olaylarına tepki verme](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid ile Azure haritalarını tümleştirmeyle ilgili genel bakış. |
| [Öğretici: bölge kümesi ayarlama](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bu öğretici, Azure haritalar 'ı kullanarak bölge sınırlaması ayarlama konusunda size kılavuzluk eder. Bölge sonuçlarının akışını yapmak ve bölge sonuçlarına dayalı bir bildirim ayarlamak için Azure Event Grid kullanırsınız. |

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. [Event Grid nedir?](overview.md)
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).

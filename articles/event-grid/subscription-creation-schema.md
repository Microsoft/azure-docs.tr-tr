---
title: Azure Event Grid abonelik şeması
description: Azure Event Grid ile bir olaya abone olmak için özellikleri tanımlar.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 6129c7f498ce6c52fce4266f693c6a304642f8c3
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845463"
---
# <a name="event-grid-subscription-schema"></a>Event Grid abonelik şeması

Event Grid aboneliği oluşturmak için oluşturma olay Abonelik işlem için bir istek gönderin. Aşağıdaki biçimi kullanın:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Örneğin, adlı bir depolama hesabı için bir olay aboneliği oluşturmak için `examplestorage` adlı bir kaynak grubu içinde `examplegroup`, aşağıdaki biçimi kullanın:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Olay aboneliği adı 3-64 karakter uzunluğunda olmalıdır ve yalnızca a-z, A-Z, 0-9, içerebilir ve "-". Bu makalede, özellikleri ve istek gövdesi için şema açıklanır.
 
## <a name="event-subscription-properties"></a>Olay aboneliği özellikleri

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| destination | object | Uç nokta tanımlayan nesne. |
| filter | object | Olay türlerini filtreleme için isteğe bağlı alan. |

### <a name="destination-object"></a>hedef nesne

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| endpointType | string | (Web kancası/HTTP, olay hub'ı veya kuyruk) abonelik için uç nokta türü. | 
| endpointUrl | string | Bu olay aboneliğine olaylar için hedef URL. | 

### <a name="filter-object"></a>filtre nesnesi

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| includedEventTypes | array | Olay türü olay iletisi olduğunda bu olay türü adları birine tam bir eşleşme eşleşmedir. Olay adı, olay kaynağı için kayıtlı olay türü adları eşleşmediğinde bir hata oluşturur. Varsayılan, tüm olay türleri eşleşir. |
| subjectBeginsWith | string | Ön ek eşleştirmesi için konu alanında olay iletisi filtreleyin. Varsayılan ya da boş dize tüm eşleşir. | 
| subjectEndsWith | string | Bir sonek eşleşme Konu alanına olay iletisi filtreleyin. Varsayılan ya da boş dize tüm eşleşir. |
| isSubjectCaseSensitive | string | Denetimler için filtreler eşleştirme büyük küçük harfe duyarlı. |


## <a name="example-subscription-schema"></a>Örnek abonelik şeması

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* Event grid'e giriş için bkz [Event Grid nedir?](overview.md)

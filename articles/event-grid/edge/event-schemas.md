---
title: Olay şemaları — Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge üzerinde Event Grid olay şemaları.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242473"
---
# <a name="event-schemas"></a>Olay şemaları

Event Grid modülü olayları JSON biçiminde kabul eder ve sunar. Şu anda Event Grid tarafından desteklenen üç şema var:-

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

Bir yayımcının konu oluşturma sırasında uyması gereken şemayı yapılandırabilirsiniz. Belirtilmemişse, varsayılan olarak **Eventgridschema**kullanılır. Beklenen şemayla uyumlu olmayan olaylar reddedilir.

Aboneler, olayların teslim edilmesini istedikleri şemayı de yapılandırabilir. Belirtilmemişse, varsayılan konunun şemadır.
Şu anda abone teslim şeması, konusunun giriş şemasıyla eşleşmelidir. 

## <a name="eventgrid-schema"></a>EventGrid şeması

EventGrid şeması, bir yayımlama varlığının uyması gereken özellikler kümesinden oluşur. Her yayımcının en üst düzey alanları doldurması vardır.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>EventGrid şema özellikleri

Tüm olaylar aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Gereklidir | Açıklama |
| -------- | ---- | ----------- |-----------
| konu başlığı | string | Hayır | Yayımlandığı konuyla eşleşmelidir. Event Grid,, belirtilmemişse yayımlandığı konunun adı ile doldurulur. |
| Konu | string | Yes | Olay konusunun yayımcı tanımlı yolu. |
| Türü | string | Yes | Bu olay kaynağı için olay türü, örneğin BlobCreated. |
| eventTime | string | Yes | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| Kimlik | string | Hayır | Etkinliğin benzersiz tanımlayıcısı. |
| Verileri | object | Hayır | Yayımlama varlığına özgü olay verilerini yakalamak için kullanılır. |
| Veri sürümü | string | Yes | Veri nesnesinin şema sürümü. Yayımcı, şema sürümünü tanımlar. |
| metadataVersion | string | Hayır | Olay meta verilerinin şema sürümü. Event Grid üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

### <a name="example--eventgrid-schema-event"></a>Örnek — EventGrid şema olayı

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent şeması

Özel şemada, EventGrid şeması gibi zorlanan zorunlu özellikler yoktur. Yayımlama varlığı olay şemasını tamamen denetleyebilir. En yüksek esnekliği sağlar ve zaten bir olay tabanlı sisteminizin olduğu ve var olan olayları yeniden kullanmak ve/veya belirli bir şemaya bağlı olmasını istemediğiniz senaryolara olanak tanır.

### <a name="custom-schema-properties"></a>Özel şema özellikleri

Zorunlu özellik yok. Bu, yükü belirlemede yayımlama varlığına kadar yapılır.

### <a name="example--custom-schema-event"></a>Örnek — özel şema olayı

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent şeması

Yukarıdaki şemalara ek olarak, Event Grid [Cloudevents JSON şemasındaki](https://github.com/cloudevents/spec/blob/master/json-format.md)olayları yerel olarak destekler. CloudEvents, olay verilerini tanımlamaya yönelik açık bir belirtimdir. Yayımlama için ortak bir olay şeması sağlayarak ve olayları tüketen birlikte çalışabilirliği basitleştirir. Bu, [Cncf](https://www.cncf.io/) 'in bir parçasıdır ve şu anda kullanılabilir olan sürüm 1,0-RC1 ' dir.

### <a name="cloudevent-schema-properties"></a>CloudEvent şema özellikleri

Zorunlu zarf özelliklerindeki [Cloudevents belirtimine](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) bakın.

### <a name="example--cloud-event"></a>Örnek — bulut olayı
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```

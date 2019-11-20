---
title: Azure Event Grid olay şeması
description: Azure Event Grid olan olaylar için sunulan özellikleri açıklar
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/20/2019
ms.author: babanisa
ms.openlocfilehash: 44cc611a9a7d71a3ac4ac7b0d779b18778d0aacd
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607600"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid olay şeması

Bu makalede, tüm olaylar için mevcut olan özellikler ve şema açıklanmaktadır. Olaylar, beş gerekli dize özelliği ve gerekli bir veri nesnesi kümesinden oluşur. Özellikler, herhangi bir yayımcının tüm olayları için ortaktır. Veri nesnesi, her yayımcıya özgü özelliklere sahiptir. Sistem konuları için, bu özellikler kaynak sağlayıcısına (örneğin, Azure depolama veya Azure Event Hubs) özgüdür.

Olay kaynakları, çeşitli olay nesnelerine sahip olabilen bir dizide Azure Event Grid olayları gönderir. Olayları bir Event Grid konusuna naklederken, dizi toplam boyutu 1 MB olabilir. Dizideki her olay 64 KB (genel kullanılabilirlik) veya 1 MB (Önizleme) ile sınırlıdır. Bir olay veya dizi boyut limitinden büyükse, yanıt **413 yükünü çok büyük**olarak alırsınız.

> [!NOTE]
> 64 KB 'a kadar olan bir olay, genel kullanım (GA) Hizmet Düzeyi Sözleşmesi (SLA) ile ele alınmıştır. 1 MB 'a kadar olan bir olay desteği şu anda önizleme aşamasındadır. 64 KB üzerindeki olaylar 64 KB 'lik artışlarla ücretlendirilir. 

Event Grid, olayları tek bir olaya sahip olan bir dizide abonelere gönderir. Bu davranış daha sonra değişebilir.

Event Grid olayı ve her bir Azure yayımcısının veri yükünün [olay şeması deposunda](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)JSON şemasını bulabilirsiniz.

## <a name="event-schema"></a>Olay şeması

Aşağıdaki örnek, tüm olay yayımcıları tarafından kullanılan özellikleri gösterir:

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

Örneğin, bir Azure Blob depolama olayı için yayımlanan şema:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Olay özellikleri

Tüm olaylar en üst düzey verilere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| konu başlığı | string | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| Konu | string | Olay konusunun yayımcı tanımlı yolu. |
| Türü | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| eventTime | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| id | string | Etkinliğin benzersiz tanımlayıcısı. |
| Verileri | object | Kaynak sağlayıcısına özel olay verileri. |
| dataVersion | string | Veri nesnesinin şema sürümü. Yayımcı, şema sürümünü tanımlar. |
| metadataVersion | string | Olay meta verilerinin şema sürümü. Event Grid üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

Veri nesnesindeki özellikler hakkında bilgi edinmek için bkz. olay kaynağı:

* [Azure abonelikleri (yönetim işlemleri)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob depolama](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub’ı](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Kaynak grupları (yönetim işlemleri)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Özel konularda, olay yayımcısı veri nesnesini belirler. En üst düzey veriler standart kaynak tanımlı olaylarla aynı alanlara sahip olmalıdır.

Olayları özel konulara yayımlarken, etkinliklerinizin etkinlikleri oluşturun ve bu da abonelerin olayla ilgilenip ilgilenmediğini bilmesini kolaylaştırır. Aboneler olayları filtrelemek ve yönlendirmek için konuyu kullanır. Örneğin, abonelerin bu yolun segmentlerine göre filtreleyebilmesi için olayın gerçekleştiği yolu sağlamayı düşünün. Yol, abonelerin olayları en dar veya genel olarak filtrelemesine olanak sağlar. Örneğin, konu içinde `/A/B/C` gibi üç segment yolu sağlarsanız, aboneler çok sayıda olay almak için `/A` ilk kesime göre filtreleyebilirsiniz. Bu aboneler `/A/B/C` veya `/A/D/E`gibi konularla olayları alır. Diğer aboneler, daha dar bir olay kümesini almak için `/A/B` göre filtreleyebilirler.

Bazen konusu ne olduğu hakkında daha fazla ayrıntı istiyor. Örneğin, bir kapsayıcıya dosya eklendiğinde **depolama hesapları** yayımcısı konu `/blobServices/default/containers/<container-name>/blobs/<file>` sağlar. Bir abone, bu kapsayıcının tüm olaylarını almak için `/blobServices/default/containers/testcontainer` yol tarafından filtreleyip depolama hesabındaki diğer kapsayıcıları alamaz. Abone Ayrıca yalnızca metin dosyalarıyla çalışmak üzere sonek `.txt` filtreleyebilir veya yönlendirebilir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. [Event Grid nedir?](overview.md)
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).

---
title: Azure Event Grid olay şeması
description: Tüm olaylar için mevcut olan özellikleri ve şemayı açıklar. Olaylar, beş gerekli dize özelliği ve gerekli bir veri nesnesi kümesinden oluşur.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: 5ff7b41a45cf467074e18a50be398b2a3c9866a4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732168"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid olay şeması

Bu makalede, tüm olaylar için mevcut olan özellikler ve şema açıklanmaktadır. Olaylar, beş gerekli dize özelliği ve gerekli bir veri nesnesi kümesinden oluşur. Özellikler, herhangi bir yayımcının tüm olayları için ortaktır. Veri nesnesi, her yayımcıya özgü özelliklere sahiptir. Sistem konuları için, bu özellikler kaynak sağlayıcısına (örneğin, Azure depolama veya Azure Event Hubs) özgüdür.

Olay kaynakları, çeşitli olay nesnelerine sahip olabilen bir dizide Azure Event Grid olayları gönderir. Olayları bir Event Grid konusuna naklederken, dizi toplam boyutu 1 MB olabilir. Dizideki her olay 1 MB ile sınırlıdır. Bir olay veya dizi boyut limitinden büyükse, yanıt **413 yükünü çok büyük** olarak alırsınız. İşlemler, 64 KB 'lik artışlarla ücretlendirilir. Bu nedenle, 64 KB üzerindeki olaylar birden çok olaymış gibi işlemler ücretlerine neden olur. Örneğin, 130 KB olan bir olay 3 ayrı olay gibi işlemler için işlem yapmış olur.

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

| Özellik | Tür | Gerekli | Açıklama |
| -------- | ---- | -------- | ----------- |
| konu başlığı | string | Hayır, ancak dahil ediliyorsa, KIMLIK Azure Resource Manager Event Grid konusuyla aynı olmalıdır. Dahil edilmediğinden, Event Grid olayın üzerine damgası eklenir. | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| subject | string | Yes | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| eventType | string | Yes | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| eventTime | string | Yes | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| kimlik | string | Yes | Etkinliğin benzersiz tanımlayıcısı. |
| veriler | object | No | Kaynak sağlayıcısına özel olay verileri. |
| dataVersion | string | Hayır, ancak boş bir değerle damgalı olacak. | Veri nesnesinin şema sürümü. Şema sürümünü yayımcı tanımlar. |
| metadataVersion | string | Gerekli değildir, ancak dahil ise Event Grid şeması ile `metadataVersion` tam olarak eşleşmelidir (Şu anda yalnızca `1` ). Dahil edilmediğinden, Event Grid olayın üzerine damgası eklenir. | Olay meta verilerinin şema sürümü. Event Grid en üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

Veri nesnesindeki özellikler hakkında bilgi edinmek için bkz. olay kaynağı:

* [Azure İlkesi](./event-schema-policy.md)
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

Olayları özel konulara yayımlarken, etkinliklerinizin etkinlikleri oluşturun ve bu da abonelerin olayla ilgilenip ilgilenmediğini bilmesini kolaylaştırır. Aboneler olayları filtrelemek ve yönlendirmek için konuyu kullanır. Örneğin, abonelerin bu yolun segmentlerine göre filtreleyebilmesi için olayın gerçekleştiği yolu sağlamayı düşünün. Yol, abonelerin olayları en dar veya genel olarak filtrelemesine olanak sağlar. Örneğin, konuyla ilgili üç segment yolu sağlarsanız `/A/B/C` , aboneler `/A` çok sayıda olayı almak için ilk kesime göre filtreleyebilirsiniz. Bu aboneler, veya gibi konularla olayları `/A/B/C` alır `/A/D/E` . Diğer aboneler, daha `/A/B` dar bir olay kümesi almak için tarafından filtreleyebilirler.

Bazen konusu ne olduğu hakkında daha fazla ayrıntı istiyor. Örneğin, **depolama hesapları** yayımcısı `/blobServices/default/containers/<container-name>/blobs/<file>` bir kapsayıcıya bir dosya eklendiğinde konuyu sağlar. Bir abone, `/blobServices/default/containers/testcontainer` Bu kapsayıcının tüm olaylarını almak için yola göre filtreleyip depolama hesabındaki diğer kapsayıcıları değil. Abone Ayrıca `.txt` yalnızca metin dosyalarıyla çalışacak şekilde sonek tarafından filtreleyip yönlendirebilir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. [Event Grid nedir?](overview.md)
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).

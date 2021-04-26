---
title: Azure Uygulama yapılandırması anahtar-değer olaylarına yeniden davranıyor
description: Uygulamaların, önemli bir koda gerek kalmadan anahtar değerlerindeki değişikliklere tepki vermesini sağlayan uygulama yapılandırma olaylarına abone olmak için Azure Event Grid kullanın.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 12a62bbd06cf9adf3b5978bb061e1d014599b44c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550751"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Azure uygulama yapılandırma olaylarına yeniden davranıma

Azure uygulama yapılandırma olayları, uygulamaların anahtar değerlerinde değişikliklere tepki vermesini sağlar. Bu, karmaşık kod veya pahalı ve verimsiz yoklama Hizmetleri gerekmeden yapılır. Bunun yerine, olaylar [Azure işlevleri](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)gibi abonelere veya kendi özel http dinleyicinize de [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) gönderilir. Kritik ölçüde, yalnızca kullandığınız kadar ödersiniz.

Azure uygulama yapılandırma olayları, zengin yeniden deneme ilkeleri ve atılacak mektup teslimi aracılığıyla uygulamalarınıza güvenilir teslim hizmetleri sağlayan Azure Event Grid gönderilir. Daha fazla bilgi edinmek için bkz. [ileti teslimi Event Grid ve yeniden deneyin](../event-grid/delivery-and-retry.md).

Ortak uygulama yapılandırma olay senaryoları, uygulama yapılandırmasını yenilemeyi, dağıtımları tetiklemeyi veya herhangi bir yapılandırmaya dayalı iş akışını içerir. Değişiklikler seyrek olduğunda, ancak senaryonuz anında yanıt vermeyi gerektirdiğinde, olay tabanlı mimari özellikle etkili olabilir.

Hızlı bir örnek için [veri değişikliği bildirimleri için Event Grid kullanma](./howto-app-configuration-event.md) bölümüne göz atın. 

![Event Grid modeli](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Kullanılabilir Azure uygulama yapılandırma olayları
Olay Kılavuzu, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../event-grid/concepts.md#event-subscriptions) kullanır. Azure uygulama yapılandırma olay abonelikleri iki tür olay içerebilir:  

> |Olay Adı|Description|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Anahtar-değer oluşturulduğunda veya değiştirildiğinde tetiklenir|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Anahtar-değer silindiğinde harekete geçirilir|

## <a name="event-schema"></a>Olay şeması
Azure uygulama yapılandırma olayları, verilerdeki değişikliklere yanıt vermek için gereken tüm bilgileri içerir. EventType özelliği "Microsoft. AppConfiguration" ile başladığı için bir uygulama yapılandırma olayını tanımlayabilirsiniz. Event Grid olay özelliklerinin kullanımı hakkında ek bilgiler [Event Grid olay şeması](../event-grid/event-schema.md)'nda belgelenmiştir.  

> |Özellik|Tür|Description|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |konu başlığı|string|Olayı gösteren uygulama yapılandırmasının tam Azure Resource Manager kimliği.|
> |subject|string|Olayın konusu olan anahtar-değer URI 'SI.|
> |eventTime|string|Olayın oluşturulduğu tarih/saat, ISO 8601 biçiminde.|
> |eventType|string|"Microsoft. AppConfiguration. KeyValueModified" veya "Microsoft. AppConfiguration. KeyValueDeleted".|
> |Id|string|Bu olayın benzersiz tanımlayıcısı.|
> |dataVersion|string|Veri nesnesinin şema sürümü.|
> |metadataVersion|string|Üst düzey özelliklerin şema sürümü.|
> |veriler|object|Azure Uygulama yapılandırmasına özgü olay verileri koleksiyonu|
> |Data. Key|string|Değiştirilen veya silinen anahtar-değer anahtarı.|
> |Data. Label|string|Değiştirilen veya silinen anahtar-değer etiketi.|
> |Data. ETag|string|`KeyValueModified`Yeni anahtar-değer ETag için. `KeyValueDeleted`Silinen anahtar değerinin ETag 'i için.|

KeyValueModified olayına bir örnek aşağıda verilmiştir:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Daha fazla bilgi için bkz. [Azure uygulama yapılandırma olayları şeması](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Olayları tüketen uygulamalar
Uygulama yapılandırma olaylarını işleyen uygulamalar aşağıdaki önerilen yöntemleri izlemelidir:
> [!div class="checklist"]
> * Birden çok abonelik olayları aynı olay işleyicisine yönlendirmek üzere yapılandırılabilir, bu nedenle olayların belirli bir kaynaktan olduğunu varsaymayın. Bunun yerine, olayı gönderen uygulama yapılandırma örneğinin olduğundan emin olmak için iletinin konusunu kontrol edin.
> * EventType olduğunu kontrol edin ve aldığınız tüm olayların istediğiniz tür olacağını varsaymayın.
> * Nesneler hakkındaki bilgilerinizin hala güncel olup olmadığını anlamak için ETag alanlarını kullanın.  
> * Belirli bir nesne üzerindeki olayların sırasını anlamak için sıralayıcı alanlarını kullanın.
> * Değiştirilen anahtar değere erişmek için konu alanını kullanın.


## <a name="next-steps"></a>Sonraki adımlar

Event Grid hakkında daha fazla bilgi edinin ve Azure uygulama yapılandırma olaylarına bir deneme hakkı verin:

- [Event Grid Hakkında](../event-grid/overview.md)
- [Veri değişikliği bildirimleri için Event Grid kullanma](./howto-app-configuration-event.md)
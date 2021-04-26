---
title: Azure Event Grid-teslim edilen olaylarda özel üst bilgileri ayarla
description: Teslim edilen olaylar üzerinde nasıl özel üstbilgiler (veya teslim özellikleri) ayarlayabileceğinizi açıklar.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: fb6f0de7919ed7cf9072c0fa35e8f9be5cb5e7db
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278295"
---
# <a name="custom-delivery-properties"></a>Özel teslim özellikleri
Olay abonelikleri, teslim edilen olaylara dahil edilen HTTP üstbilgilerini ayarlamanıza olanak sağlar. Bu özellik, bir hedef için gereken özel üstbilgileri ayarlamanıza olanak sağlar. Bir olay aboneliği oluştururken en fazla 10 üst bilgi ayarlayabilirsiniz. Her üst bilgi değeri 4.096 (4K) bayttan büyük olmamalıdır.

Aşağıdaki hedeflere teslim edilen olaylar üzerinde özel üstbilgiler belirleyebilirsiniz:

- Web Kancaları
- Azure Service Bus konuları ve kuyrukları
- Azure Event Hubs
- Geçiş Karma Bağlantılar

Azure portal bir olay aboneliği oluştururken, özel HTTP üst bilgilerini ayarlamak için **teslim özellikleri** sekmesini kullanabilirsiniz. Bu sayfa, sabit ve dinamik üstbilgi değerlerini ayarlamanıza olanak sağlar.

## <a name="setting-static-header-values"></a>Statik üst bilgi değerlerini ayarlama
Üst bilgileri sabit bir değere ayarlamak için üstbilginin adını ve değerini karşılık gelen alanlara girin:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Teslim özellikleri-statik":::

Hassas veriler sağlanırken denetim **gizli mi?** Hassas veriler Azure portal gösterilmez. 

## <a name="setting-dynamic-header-values"></a>Dinamik üstbilgi değerlerini ayarlama
Bir üstbilginin değerini gelen bir olaydaki bir özelliğe göre ayarlayabilirsiniz. Giden isteklerde bir üst bilgi için değer olarak kullanılacak gelen olayın özellik değerine başvurmak için JsonPath söz dizimini kullanın. Örneğin, olay verilerinde gelen olay özelliği **sisteminin** değerini kullanarak **Kanal** adlı bir üstbilginin değerini ayarlamak için olay aboneliğinizi aşağıdaki şekilde yapılandırın:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Teslim özellikleri-dinamik":::

## <a name="examples"></a>Örnekler
Bu bölüm, teslim özelliklerini kullanmanın birkaç örneğini sağlar.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Yetkilendirme üst bilgisini bir taşıyıcı belirteçle ayarlama (norksel olmayan örnek)

Web kancası işleyicinizdeki isteği tanımlamak için bir yetkilendirme üst bilgisine bir değer ayarlayın. [Web kancasını Azure Active Directory ile korumadıysanız](secure-webhook-delivery.md)bir yetkilendirme üst bilgisi ayarlanabilir.

| Üst bilgi adı   | Üst bilgi türü | Üst bilgi değeri |
| :--           | :--         | :--            |
|`Authorization` | Statik | `BEARER SlAV32hkKG...`|

Giden istekler artık olay aboneliğinde ayarlanan üstbilgiyi içermelidir:

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Yetkilendirmenin bir Web kancası olması durumunda yetkilendirme üst bilgilerinin tanımlanması senerişilebilir bir seçenektir. [Bir kaynak kimliği](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs ve Karma Bağlantılar, Event Grid ile kullanıldığında kendi kimlik doğrulama düzenlerini destekledikleri için kullanılmamalıdır.

### <a name="service-bus-example"></a>Service Bus örneği
Azure Service Bus, tek iletiler gönderilirken ileti özelliklerini tanımlamak için bir [Brokerproperties http üst bilgisi](/rest/api/servicebus/message-headers-and-properties#message-headers) kullanımını destekler. `BrokerProperties`Üstbilginin DEĞERI JSON biçiminde sağlanmalıdır. Örneğin, Service Bus için tek bir ileti gönderirken ileti özelliklerini ayarlamanız gerekiyorsa, üstbilgiyi aşağıdaki şekilde ayarlayın:

| Üst bilgi adı | Üst bilgi türü | Üst bilgi değeri |
| :-- | :-- | :-- |
|`BrokerProperties` | Statik     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Event Hubs örneği

Olayları bir olay hub 'ında belirli bir bölüme yayımlamanız gerekiyorsa, hedef olay hub 'ı bölümünü tanımlayan bölüm anahtarını belirtmek için olay aboneliğinizde bir [Brokerproperties http üstbilgisi](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) tanımlayın.

| Üst bilgi adı | Üst bilgi türü | Üst bilgi değeri                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | Statik | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Azure Depolama kuyruklarına giden olaylarda yaşam süresi yapılandırma
Azure depolama kuyrukları hedefi için, Azure depolama kuyruğuna teslim edildiğinde giden iletinin yalnızca yaşam süresini yapılandırabilirsiniz. Zaman sağlanmazsa, iletinin varsayılan yaşam süresi 7 gündür. Ayrıca, olayı süresiz olarak ayarlayabilirsiniz.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Teslim özellikleri-depolama kuyruğu":::

## <a name="next-steps"></a>Sonraki adımlar
Olay teslimi hakkında daha fazla bilgi için aşağıdaki makaleye bakın:

- [Teslim ve yeniden deneme](delivery-and-retry.md)
- [Web kancası olay teslimi](webhook-event-delivery.md)
- [Olay filtreleme](event-filtering.md)

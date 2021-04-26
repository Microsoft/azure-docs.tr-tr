---
title: include dosyası
description: include dosyası
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/21/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 44afd8ea4ef2ab06ec31b7528e9776faebc3b4dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98689997"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Güvenlik duvarında hangi bağlantı noktalarını açmalıyım? 
Olayları göndermek ve almak için Azure Event Hubs ile aşağıdaki protokolleri kullanabilirsiniz:

- Gelişmiş İleti Sıraya Alma Protokolü 1,0 (AMQP)
- TLS ile Köprü Metni Aktarım Protokolü 1,1 (HTTPS)
- Apache Kafka

Azure Event Hubs ile iletişim kurmak için bu protokolleri kullanmak üzere açmanız gereken giden bağlantı noktaları için aşağıdaki tabloya bakın. 

| Protokol | Bağlantı noktaları | Ayrıntılar | 
| -------- | ----- | ------- | 
| AMQP | 5671 ve 5672 | Bkz. [AMQP protokol Kılavuzu](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Bu bağlantı noktası HTTP/REST API ve AMQP-Over-WebSockets için kullanılır. |
| Kafka | 9093 | Bkz. [Kafka uygulamalardan Event Hubs kullanma](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

İstemci SDK 'Ları tarafından gerçekleştirilen çeşitli yönetim işlemleri ve Azure Active Directory (kullanıldığında) ' dan belirteçleri alma HTTPS üzerinden çalıştırıldığında, giden iletişim için HTTPS bağlantı noktası 5671 bağlantı noktası üzerinden de gereklidir. 

Resmi Azure SDK 'Ları genellikle Event Hubs olayları göndermek ve almak için AMQP protokolünü kullanır. AMQP-Over-WebSockets protokol seçeneği, HTTP API gibi bağlantı noktası TCP 443 üzerinden çalışır, ancak başka bir şekilde düz AMQP ile aynı şekilde çalışır. Bu seçenek, ek el sıkışma gidiş dönüşleri ve HTTPS bağlantı noktasını paylaşmak için zorunluluğunu getirir kadar çok daha fazla ek yük nedeniyle ilk bağlantı gecikmesini daha yüksektir. Bu mod seçilirse, TCP bağlantı noktası 443 iletişim için yeterlidir. Aşağıdaki seçenekler, düz AMQP veya AMQP WebSockets modunun seçilmesine izin verir:

| Dil | Seçenek   |
| -------- | ----- |
| .NET     | [Eventhubstransporttype. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) veya [Eventhubstransporttype. AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) Ile [Eventhubconnectionoptions. TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype) özelliği |
| Java     | [com. Microsoft. Azure. eventhubs. EventProcessorClientBuilder. TransportType](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype) , [Amqptransporttype. amqp](/java/api/com.azure.core.amqp.amqptransporttype) veya [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Düğüm  | [Eventhubconsumerclientoptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions) bir `webSocketOptions` özelliğe sahip. |
| Python | [TransportType. AMQP](/python/api/azure-eventhub/azure.eventhub.transporttype) veya [TransportType. AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype) ile [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Hangi IP adreslerine izin vermem gerekir?
Azure ile çalışırken, bazı durumlarda kurumsal güvenlik duvarınızdaki veya ara ortamınızdaki belirli IP adresi aralıklarına veya URL 'Lerine, kullanmakta olduğunuz veya kullanmaya çalıştığınız tüm Azure hizmetlerine erişim izni vermeniz gerekir. Event Hubs tarafından kullanılan IP adreslerinde trafiğe izin verildiğini doğrulayın. Azure Event Hubs tarafından kullanılan IP adresleri için: bkz. [Azure IP aralıkları ve hizmet etiketleri-genel bulut](https://www.microsoft.com/download/details.aspx?id=56519).

Ayrıca, ad alanınız için IP adresine izin verildiğini doğrulayın. Bağlantılarınız için izin verilecek doğru IP adreslerini bulmak için şu adımları izleyin:

1. Komut isteminden aşağıdaki komutu çalıştırın: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. ' De döndürülen IP adresini aklınızda edin `Non-authoritative answer` . 

Ad alanınız için **bölge yedekliliği** kullanıyorsanız birkaç ek adım yapmanız gerekir: 

1. İlk olarak, ad alanında Nslookup ' ı çalıştırırsınız.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. **Yetkili olmayan yanıt** bölümündeki adı aşağıdaki biçimlerden birinde olan bir yere göz önünde edin: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Her biri için S1, S2 ve S3 sonekleri ile her biri için Nslookup ' ı çalıştırarak üç kullanılabilirlik alanında çalışan üç örnek için IP adreslerini alın. 

    > [!NOTE]
    > Komut tarafından döndürülen IP adresi `nslookup` statik BIR IP adresi değil. Ancak, temeldeki dağıtım silinene veya farklı bir kümeye taşınana kadar sabit kalır.

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>Hangi istemci IP 'Leri ad alanım 'dan olay gönderiyor veya olayları alıyor?
İlk olarak, ad alanında [IP filtrelemeyi](../articles/event-hubs/event-hubs-ip-filtering.md) etkinleştirin. 

Ardından [tanılama günlüklerini etkinleştirme](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs)' deki yönergeleri izleyerek [Event Hubs sanal ağ bağlantı olayları](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) için tanılama günlüklerini etkinleştirin. Bağlantının reddedildiği IP adresini görürsünüz.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> Sanal ağ günlükleri yalnızca ad alanı **belırlı IP adreslerinden** (IP filtre kuralları) erişime izin veriyorsa oluşturulur. Bu özellikleri kullanarak ad alanınızı erişimi kısıtlamak ve yine de Event Hubs ad alanına bağlanan istemcilerin IP adreslerini izlemek için sanal ağ günlükleri almak istiyorsanız, aşağıdaki geçici çözümü kullanabilirsiniz: IP filtrelemeyi etkinleştirebilir ve toplam adreslenebilir IPv4 aralığını (1.0.0.0/1-255.0.0.0/1) ekleyebilirsiniz. Event Hubs IPv6 adres aralıklarını desteklemez. 

> [!NOTE]
> Şu anda, tek bir iletinin veya olayın kaynak IP 'sini tespit etmek mümkün değildir. 
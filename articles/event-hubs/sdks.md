---
title: Azure Event Hubs-Istemci SDK 'Ları | Microsoft Docs
description: Bu makalede, Azure Event Hubs yönelik istemci SDK 'Ları hakkında bilgi sağlanır.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e4af837c4d62098f3efe4098c6734ecd8972e972
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310011"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs-Istemci SDK 'Ları
Bu makalede, Azure Event Hubs tarafından desteklenen SDK 'lar için aşağıdaki bilgiler sağlanmaktadır: 

- Uygulamalarınızda kullanabileceğiniz paketin konumu 
- Kaynak kodu, örnekleri, Benioku, değişiklik günlüğü, bildirilen sorunları bulabileceğiniz ve ayrıca yeni sorunlar oluşturabileceğiniz GitHub konumu 
- Hızlı başlangıç öğreticilerine bağlantılar 

## <a name="client-sdks"></a>İstemci SDK'ları
Aşağıdaki tabloda, şu anda kullanılabilir olan tüm Azure Event Hubs çalışma zamanı istemcileri açıklanmaktadır. Bu kitaplıkların bazıları da sınırlı yönetim işlevleri de içerirken, yönetim işlemlerine adanmış belirli kitaplıklar da vardır. Bu kitaplıkların temel odağı, bir olay hub 'ından **ileti göndermek ve almak** için kullanılır.

| Dil | Paket | Başvuru | 
| -------- | ------- | --------------- | 
| . NET Standard (**en son** ve hem .NET Core hem de .NET Framework destekler) | [Azure. Messaging. EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Öğretici](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure. Messaging. EventHubs. Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Öğretici](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Standard (**eski** ve hem .NET Core hem de .NET Framework destekler) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Öğretici](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Öğretici](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework (**eski**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[Öğretici](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [Azure-mesajlaşma-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Öğretici](event-hubs-java-get-started-send.md)</li></ul> |
|      | [Azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(eski)** | <ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Öğretici](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [Azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Öğretici](event-hubs-python-get-started-send.md)</li></ul> |
|        | [Azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Öğretici](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [Azure/Olay-Hub 'lar](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Öğretici](event-hubs-node-get-started-send.md)</li></ul> |
|            | [Azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Öğretici](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [Azure-Event-hub 'lar-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-event-hubs-go)</li><li>[Öğretici](event-hubs-go-get-started-send.md)</li></ul> |
| C | [Azure-Event-hub 'lar-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub konumu](https://github.com/Azure/azure-event-hubs-c)</li><li>[Öğretici](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>Yönetim SDK 'Ları
Aşağıdaki tabloda, mevcut olan yönetime özgü tüm kitaplıklar listelenmiştir. Bu kitaplıkların hiçbiri çalışma zamanı işlemleri içermez ve **Event Hubs varlıklarını yönetmek** tek amaç içindir.

| Dil | Paket | Başvuru | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[GitHub konumu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[Öğretici](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Event Hubs genel bakış](./event-hubs-about.md)
* [Olay Hub'ı oluşturma](event-hubs-create.md)
* [Event Hubs ile ilgili SSS](event-hubs-faq.yml)

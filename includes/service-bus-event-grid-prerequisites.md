---
title: include dosyası
description: include dosyası
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96509278"
---
## <a name="prerequisites"></a>Önkoşullar
[Azure aboneliğiniz](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

## <a name="create-a-service-bus-namespace"></a>Service Bus ad alanı oluşturma
Bu öğreticideki yönergeleri izleyin: [hızlı başlangıç: Azure Portal kullanarak aşağıdaki görevleri yapmak için konuya bir Service Bus konu ve abonelik oluşturun](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) :

- **Premium** Service Bus ad alanı oluşturun. 
- Bağlantı dizesini alın. 
- Service Bus konu başlığı oluşturun.
- Konuya abonelik oluşturun. Bu öğreticide yalnızca bir aboneliğiniz olması gerekir, bu nedenle S2 ve S3 abonelikleri oluşturmaya gerek yoktur. 

## <a name="send-messages-to-the-service-bus-topic"></a>Service Bus konuya ileti gönderin
Bu adımda, önceki adımda oluşturduğunuz Service Bus konuya ileti göndermek için örnek bir uygulama kullanırsınız. 

1. [GitHub Azure-Service-Bus deposunu](https://github.com/Azure/azure-service-bus/)kopyalayın.
2. Visual Studio 'da *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* klasörüne gidin ve *sbeventgridıntegration. sln* dosyasını açın.
3. Çözüm Gezgini penceresinde, **Iletileyici** projesini genişletin ve **program. cs**' yi seçin.
4. `<SERVICE BUS NAMESPACE - CONNECTION STRING>`Service Bus ad alanınız ve konunun adı ile bağlantı dizesiyle değiştirin `<TOPIC NAME>` . 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Service Bus konusuna 5 test iletisi () göndermek için programı derleyin ve çalıştırın `const int numberOfMessages = 5;` . 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Konsol uygulaması çıkışı":::

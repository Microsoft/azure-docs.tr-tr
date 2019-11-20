---
title: Apache Kafka etkin olay hub 'ı oluşturma-Azure Event Hubs | Microsoft Docs
description: Bu makalede Azure portal kullanarak Apache Kafka etkin bir Azure Event Hubs ad alanı oluşturmaya yönelik bir yol sunulmaktadır.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 9ce0f74ec6d4e536bfb3fe827ae6f8ae143b640e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555804"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Apache Kafka etkin olay hub 'ları oluşturma

Azure Event Hubs, saniyede milyonlarca olayı gösteren (PaaS) büyük bir veri akışı platformudur ve gerçek zamanlı analiz ve görselleştirme için düşük gecikme süresi ve yüksek performans sağlar.

Azure Event Hubs, size bir Kafka uç noktası sağlar. Bu uç nokta, Event Hubs ad alanının [Apache Kafka](https://kafka.apache.org/intro) ileti protokolünü ve API 'leri yerel olarak anlamasına olanak sağlar. Bu özellik sayesinde, protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Kafka konularda yaptığınız gibi Olay Hub 'lerinizle iletişim kurabilirsiniz. Event Hubs, [1,0 ve üzeri sürümleri Apache Kafka](https://kafka.apache.org/10/documentation.html) destekler.

Bu makalede, bir Event Hubs ad alanı oluşturma ve Kafka uygulamalarını Kafka etkin olay hub 'larına bağlamak için gereken bağlantı dizesinin nasıl alınacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka etkin Event Hubs ad alanı oluşturma

1. [Azure Portal][Azure portal]oturum açın ve ekranın sol üst kısmında bulunan **kaynak oluştur ' a** tıklayın.

2. Event Hubs araması yapın ve burada gösterilen seçenekleri belirleyin:
    
    ![Portalda Event Hubs arama](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Benzersiz bir ad belirtin ve ad alanında Kafka'yı etkinleştirin. **Oluştur**’a tıklayın.
    
    ![Ad alanı oluşturma](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Ad alanı oluşturulduktan sonra **Ayarlar** sekmesinde **Paylaşılan erişim ilkeleri**'ne tıklayarak bağlantı dizesini alın.

    ![Paylaşılan erişim ilkeleri’ne tıklayın.](./media/event-hubs-create/create-event-hub7.png)

5. Varsayılan **RootManageSharedAccessKey** ilkesini seçebilir veya yeni bir ilke ekleyebilirsiniz. İlke adına tıklayın ve bağlantı dizesini kopyalayın. 
    
    ![İlke seçme](./media/event-hubs-create/create-event-hub8.png)
 
6. Bu bağlantı dizesini Kafka uygulaması yapılandırmanıza ekleyin.

Artık Kafka protokolünü kullanan uygulamalarınızdaki olayların akışını Event Hubs'a yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Event Hubs hakkında daha fazla bilgi edinmek için şu bağlantıları ziyaret edin:

* [Kafka uygulamalarınızdan Event Hubs'a akış yapma](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Kafka için Event Hubs hakkında bilgi edinin](event-hubs-for-kafka-ecosystem-overview.md)
* [Event Hubs hakkında bilgi edinin](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/

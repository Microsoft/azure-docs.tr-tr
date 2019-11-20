---
title: 'Hızlı başlangıç: Python ile Azure Service Bus kuyruklarını kullanma'
description: Python ile Azure Service Bus kuyruklarını kullanmayı öğrenin.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: d0f579fcd82860380f1aaa651a61c0259d075a0d
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748535"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Hızlı başlangıç: Python ile Azure Service Bus kuyruklarını kullanma

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Bu makalede, Azure Service Bus kuyruklarından ileti oluşturmak, göndermek ve almak için Python 'un nasıl kullanılacağı gösterilmektedir. 

Python Azure Service Bus kitaplıkları hakkında daha fazla bilgi için bkz. [Python için Service Bus kitaplıkları](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Ön koşullar
- Azure aboneliği. [Visual Studio veya MSDN abonesi avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- [Hızlı başlangıç: bir Service Bus konusu ve abonelikleri oluşturmak için Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md)kullanarak oluşturulan bir Service Bus ad alanı. Bu makalede daha sonra kullanmak için, **paylaşılan erişim ilkeleri** ekranından birincil bağlantı dizesini kopyalayın. 
- Python [Azure Service Bus][Python Azure Service Bus package] paketi yüklüyken Python 3.4 x veya üzeri. Daha fazla bilgi için bkz. [Python Yükleme Kılavuzu](/azure/python/python-sdk-azure-install). 

## <a name="create-a-queue"></a>Kuyruk oluşturma

**Servicebusclient** nesnesi kuyruklarla çalışmanıza olanak sağlar. Service Bus programlı olarak erişmek için, Python dosyanızın en üstüne aşağıdaki satırı ekleyin:

```python
from azure.servicebus import ServiceBusClient
```

**Servicebusclient** nesnesi oluşturmak için aşağıdaki kodu ekleyin. `<connectionstring>`, Service Bus birincil bağlantı dizesi değeri ile değiştirin. Bu değeri, [Azure portal][Azure portal]Service Bus ad alanında **paylaşılan erişim ilkeleri** altında bulabilirsiniz.

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

Aşağıdaki kod, varsayılan ayarlarla `taskqueue` adlı bir sıra oluşturmak için **Servicebusclient** 'ın `create_queue` yöntemini kullanır:

```python
sb_client.create_queue("taskqueue")
```

İleti yaşam süresi (TTL) veya en büyük konu boyutu gibi varsayılan sıra ayarlarını geçersiz kılmak için seçeneklerini kullanabilirsiniz. Aşağıdaki kod, en fazla 5 GB sıra boyutu ve 1 dakikalık TTL değeri ile `taskqueue` adlı bir kuyruk oluşturur:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Kuyruğa ileti gönderme

Bir Service Bus kuyruğuna ileti göndermek için, bir uygulama **Servicebusclient** nesnesinde `send` yöntemini çağırır. Aşağıdaki kod örneği bir kuyruk istemcisi oluşturur ve `taskqueue` kuyruğuna bir test iletisi gönderir. `<connectionstring>`, Service Bus birincil bağlantı dizesi değeri ile değiştirin. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>İleti boyutu sınırları ve kotaları

Service Bus kuyrukları, [Standart katmanda](service-bus-premium-messaging.md) maksimum 256 KB ve [Premium katmanda](service-bus-premium-messaging.md) maksimum 1 MB ileti boyutunu destekler. Standart ve özel uygulama özelliklerini içeren üst bilginin maksimum dosya boyutu 64 KB olabilir. Kuyruğun tutatabilecek ileti sayısı için bir sınır yoktur, ancak sıranın tuttuğu mesajların toplam boyutunun bir üst sınırı vardır. Sıra boyutunu oluşturma sırasında, 5 GB üst sınırı ile tanımlayabilirsiniz. 

Kotalar hakkında daha fazla bilgi için bkz. [Service Bus kotaları][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Kuyruktan ileti alma

Queue Client, **Servicebusclient** nesnesinde `get_receiver` yöntemini kullanarak bir kuyruktan ileti alır. Aşağıdaki kod örneği bir kuyruk istemcisi oluşturur ve `taskqueue` sırasından bir ileti alır. `<connectionstring>`, Service Bus birincil bağlantı dizesi değeri ile değiştirin. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Peek_lock parametresini kullanma

`get_receiver` isteğe bağlı `peek_lock` parametresi, Service Bus iletileri okudukları sırada silip silmeyeceğini belirler. İleti alma için varsayılan mod *PeekLock*' dır veya `peek_lock` **true**olarak ayarlanır ve bu, iletileri kuyruktan silmeden onları okur (göz atar) ve kilitler. Her ileti daha sonra kuyruktan kaldırılacak şekilde açıkça tamamlanmalıdır.

Sıradaki iletileri okurken silmek için, `get_receiver` `peek_lock` parametresini **false**olarak ayarlayabilirsiniz. Alma işleminin bir parçası olarak ileti silme en basit modeldir, ancak yalnızca bir hata oluşursa uygulamanın eksik iletileri kabul edebildiği durumlarda işe yarar. Bu davranışı anlamak için, tüketicinin işlemeden önce bir alma isteği yaptığı ve sonra çöktüğü bir senaryo düşünün. İleti alındığı sırada silinirse, uygulama yeniden başlatıldığında ve iletileri yeniden kullanmaya başladığında, kilitlenmeden önce aldığı iletiyi kaçırmıştır.

Uygulamanız eksik iletileri kabul edemiyorum, alma iki aşamalı bir işlemdir. PeekLock, tüketilen bir sonraki iletiyi bulur, diğer tüketicilerin bunu almasını engellemek için onu kilitler ve uygulamaya döndürür. İleti işlendikten veya depolandıktan sonra uygulama, **ileti** nesnesine `complete` yöntemini çağırarak alma işleminin ikinci aşamasını tamamlar.  `complete` yöntemi iletiyi tüketildiği gibi işaretler ve sıradan kaldırır.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Uygulama kilitlenmelerini ve okunamaz iletileri işle

Service Bus, uygulamanızda gerçekleşen hataları veya ileti işlenirken oluşan zorlukları rahat bir şekilde ortadan kaldırmanıza yardımcı olmak için işlevsellik sağlar. Alıcı uygulama bir nedenden dolayı bir iletiyi işleyemeyeceği takdirde **ileti** nesnesinde `unlock` yöntemi çağırabilir. Service Bus kuyruktaki iletinin kilidini açar ve aynı ya da başka bir tüketen uygulama tarafından yeniden alınmak üzere kullanılabilir hale gelir.

Sıra içinde kilitlenen iletiler için de bir zaman aşımı vardır. Bir uygulama, kilit zaman aşımı dolmadan önce bir iletiyi işleyemezse, örneğin, uygulama kilitlenirse, Service Bus otomatik olarak iletinin kilidini açar ve tekrar alınabilmesini sağlar.

Bir uygulama bir ileti işlendikten sonra, ancak `complete` yöntemi çağrılmadan önce kilitlenirse, ileti yeniden başlatıldığında uygulamaya yeniden gönderilir. Bu davranış genellikle *en az bir kez işleme*olarak adlandırılır. Her ileti en az bir kez işlenir, ancak bazı durumlarda aynı ileti yeniden teslim edilebilir. Senaryonuz yinelenen işleme kabul edememesi durumunda, yinelenen ileti teslimini işlemek için teslim denemelerinde sabit olarak kalan ileti **MessageID** özelliğini kullanabilirsiniz. 

> [!TIP]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, bir Service Bus ad alanına bağlanmanızı ve mesajlaşma varlıklarını kolayca yönetmenizi sağlar. Araç içeri/dışarı aktarma işlevselliği ve konuları, kuyrukları, abonelikleri, geçiş Hizmetleri, Bildirim Hub 'larını ve Olay Hub 'larını test etme özelliği gibi gelişmiş özellikler sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus kuyrukların temellerini öğrendiğinize göre, daha fazla bilgi edinmek için [Kuyruklar, konular ve abonelikler][Queues, topics, and subscriptions] konusuna bakın.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

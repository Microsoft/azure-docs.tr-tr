---
title: 'Hızlı başlangıç: Python ile Azure Service Bus konuları ve abonelikleri kullanma'
description: Python 'dan Azure Service Bus konuları ve abonelikleri kullanmayı öğrenin.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 94a49b31139947c6323ab391b78ecd03ee911e0a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748495"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Hızlı başlangıç: Python ile Service Bus konuları ve abonelikleri kullanma

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Bu makalede, Python 'un Azure Service Bus konuları ve abonelikleri ile nasıl kullanılacağı açıklanır. Örnekler için [Azure Python SDK][Azure Python package] paketini kullanın: 

- Konularda konular ve abonelikler oluşturun
- Abonelik filtreleri ve kuralları oluşturma
- Konulara ileti gönder 
- Aboneliklerden ileti alma
- Konu başlıklarını ve abonelikleri silme

## <a name="prerequisites"></a>Ön koşullar
- Azure aboneliği. [Visual Studio veya MSDN abonesi avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- [Hızlı başlangıç: bir Service Bus konusu ve abonelikleri oluşturmak için Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md)kullanarak oluşturulan bir Service Bus ad alanı. Bu hızlı başlangıçta kullanmak üzere **paylaşılan erişim ilkeleri** ekranından ad alanı adını, paylaşılan erişim anahtarı adını ve birincil anahtar değerini kopyalayın. 
- [Azure Python SDK][Azure Python package] paketi yüklüyken Python 3.4 x veya üzeri. Daha fazla bilgi için bkz. [Python Yükleme Kılavuzu](/azure/python/python-sdk-azure-install).

## <a name="create-a-servicebusservice-object"></a>ServiceBusService nesnesi oluşturma

**Servicebusservice** nesnesi konularda konular ve abonelikler ile çalışmanıza olanak sağlar. Service Bus programlı olarak erişmek için, Python dosyanızın en üstüne aşağıdaki satırı ekleyin:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

**Servicebusservice** nesnesi oluşturmak için aşağıdaki kodu ekleyin. `<namespace>`, `<sharedaccesskeyname>`ve `<sharedaccesskeyvalue>` Service Bus ad alanı adınızla, paylaşılan erişim Imzası (SAS) anahtar adı ve birincil anahtar değeri ile değiştirin. Bu değerleri, [Azure portal][Azure portal]Service Bus ad uzayındaki **paylaşılan erişim ilkeleri** altında bulabilirsiniz.

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Konu başlığı oluşturma

Aşağıdaki kod, varsayılan ayarlarla `mytopic`adlı Service Bus bir konu oluşturmak için `create_topic` yöntemini kullanır:

```python
bus_service.create_topic('mytopic')
```

İleti yaşam süresi (TTL) veya en büyük konu boyutu gibi varsayılan konu ayarlarını geçersiz kılmak için konu seçeneklerini kullanabilirsiniz. Aşağıdaki örnek, en fazla 5 GB konu boyutu ve bir dakikalık varsayılan ileti TTL 'SI `mytopic` adlı bir konu oluşturur:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Abonelik oluşturma

Ayrıca, konularla ilgili abonelikler oluşturmak için **Servicebusservice** nesnesini de kullanabilirsiniz. Abonelik, sanal kuyruğuna teslim edilen ileti kümesini kısıtlamak için bir filtreye sahip olabilir. Filtre belirtmezseniz yeni abonelikler, konuya yayınlanan tüm iletileri aboneliğin sanal kuyruğuna yerleştiren varsayılan **Matchall** filtresini kullanır. Aşağıdaki örnek, **Matchall** filtresini kullanan `mytopic` adlı `AllMessages` için bir abonelik oluşturur:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Abonelikler ile filtreleri kullanma

Bir abonelikte görünen iletileri filtrelemek için **Servicebusservice** nesnesinin `create_rule` yöntemini kullanın. Abonelik oluştururken kuralları belirtebilir veya mevcut aboneliklere kurallar ekleyebilirsiniz.

En esnek filtre türü, bir SQL-92 alt kümesini kullanan bir **Sqlfilter**. SQL filtreleri, konusunda yayınlanan iletilerin özelliklerine göre çalışır. Bir SQL filtresiyle kullanabileceğiniz ifadeler hakkında daha fazla bilgi için, bkz. [sqlfilter. SqlExpression][SqlFilter.SqlExpression] sözdizimi.

Tüm yeni abonelikler için **Matchall** varsayılan filtresi otomatik olarak uygulandığı için, filtre uygulamak istediğiniz aboneliklerden kaldırmanız gerekir ya da **Matchall** , belirttiğiniz diğer filtreleri geçersiz kılar. **Servicebusservice** nesnesinin `delete_rule` yöntemini kullanarak varsayılan kuralı kaldırabilirsiniz.

Aşağıdaki örnek, `HighMessageFilter`adlı bir **Sqlfilter** kuralıyla `mytopic` adlı `HighMessages`bir abonelik oluşturur. `HighMessageFilter` kuralı yalnızca, 3 ' ten büyük özel bir `messageposition` özelliğine sahip iletileri seçer:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Aşağıdaki örnek, `LowMessageFilter`adlı bir **Sqlfilter** kuralıyla `mytopic` adlı `LowMessages`bir abonelik oluşturur. `LowMessageFilter` kuralı yalnızca, 3 ' ten küçük veya buna eşit bir `messageposition` özelliğine sahip iletileri seçer:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

`AllMessages`, `HighMessages`ve `LowMessages` tüm etkiyle, `mytopic` gönderilen iletiler her zaman `AllMessages` aboneliğinin alıcılarına dağıtılır. İletiler, iletinin `messageposition` özellik değerine bağlı olarak `HighMessages` veya `LowMessages` aboneliğine de seçmeli olarak dağıtılır. 

## <a name="send-messages-to-a-topic"></a>Konu başlığına ileti gönderme

Uygulamalar, bir Service Bus konusuna ileti göndermek için **Servicebusservice** nesnesinin `send_topic_message` yöntemini kullanır.

Aşağıdaki örnek `mytopic` konusuna beş test iletisi gönderir. Özel `messageposition` özelliği değeri, döngünün yinelemesine bağlıdır ve iletileri hangi aboneliklerin alacağını belirler. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>İleti boyutu sınırları ve kotaları

Service Bus konu başlıkları, [Standart katmanda](service-bus-premium-messaging.md) maksimum 256 KB ve [Premium katmanda](service-bus-premium-messaging.md) maksimum 1 MB ileti boyutunu destekler. Standart ve özel uygulama özelliklerini içeren üst bilginin maksimum dosya boyutu 64 KB olabilir. Konunun tutamadıkları ileti sayısında bir sınır yoktur, ancak konunun tuttuğu mesajların toplam boyutunun bir üst sınırı vardır. Konu boyutunu oluşturma sırasında, 5 GB üst sınırı ile tanımlayabilirsiniz. 

Kotalar hakkında daha fazla bilgi için bkz. [Service Bus kotaları][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Bir abonelikten ileti alma

Uygulamalar, bir abonelikten ileti almak için **Servicebusservice** nesnesinde `receive_subscription_message` yöntemini kullanır. Aşağıdaki örnek `LowMessages` aboneliğinden gelen iletileri alır ve bunları okuduklarında siler:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

`receive_subscription_message` isteğe bağlı `peek_lock` parametresi, Service Bus iletileri okunduklarında, aboneliğin silip silmediğini belirler. İleti alma için varsayılan mod *PeekLock*' dır veya `peek_lock` **true**olarak ayarlanır. Bu, iletileri bir abonelikten silmeksizin okur (göz atar) ve kilitler. Bu durumda, her bir ileti aboneliğin kaldırılması için açıkça tamamlanmış olmalıdır.

Abonelikten gelen iletileri okurken silmek için, önceki örnekte olduğu gibi `peek_lock` parametresini **false**olarak ayarlayabilirsiniz. İletileri alma işleminin bir parçası olarak silmek en basit modeldir ve bir hata oluşursa uygulamanın eksik iletileri kabul edebildiği durumlarda iyi sonuç verir. Bu davranışı anlamak için, uygulamanın bir alma isteği yaptığı ve işlemeden önce çöktüğü bir senaryo düşünün. İleti alındığı sırada silinirse, uygulama yeniden başlatıldığında ve iletileri yeniden kullanmaya başladığında, kilitlenmeden önce aldığı iletiyi kaçırmıştır.

Uygulamanız eksik iletileri kabul edemediği takdirde, alma iki aşamalı bir işlem haline gelir. PeekLock, tüketilen bir sonraki iletiyi bulur, diğer tüketicilerin bunu almasını engellemek için onu kilitler ve uygulamaya döndürür. İleti işlendikten veya depolandıktan sonra uygulama, **ileti** nesnesine `complete` yöntemini çağırarak alma işleminin ikinci aşamasını tamamlar.  `complete` yöntemi iletiyi tüketildiği gibi işaretler ve abonelikten kaldırır.

Aşağıdaki örnek, bir Peek kilit senaryosunu göstermektedir:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Uygulama kilitlenmelerini ve okunamaz iletileri işle

Service Bus, uygulamanızda gerçekleşen hataları veya ileti işlenirken oluşan zorlukları rahat bir şekilde ortadan kaldırmanıza yardımcı olmak için işlevsellik sağlar. Alıcı uygulama bir nedenden dolayı bir iletiyi işleyemeyeceği takdirde **ileti** nesnesinde `unlock` yöntemi çağırabilir. Service Bus aboneliğin içindeki iletinin kilidini açar ve aynı ya da başka bir tüketen uygulama tarafından yeniden alınmak üzere kullanılabilir hale gelir.

Ayrıca, abonelik içinde kilitlenen iletiler için bir zaman aşımı vardır. Bir uygulama, kilit zaman aşımı dolmadan önce bir iletiyi işleyemezse, örneğin, uygulama kilitlenirse, Service Bus otomatik olarak iletinin kilidini açar ve tekrar alınabilmesini sağlar.

Bir uygulama bir ileti işlendikten sonra, ancak `complete` yöntemi çağrılmadan önce kilitlenirse, ileti yeniden başlatıldığında uygulamaya yeniden teslim edilir. Bu davranış genellikle *en az bir kez işleme*olarak adlandırılır. Her ileti en az bir kez işlenir, ancak bazı durumlarda aynı ileti yeniden teslim edilebilir. Senaryonuz yinelenen işleme kabul edememesi durumunda, yinelenen ileti teslimini işlemek için teslim denemelerinde sabit olarak kalan ileti **MessageID** özelliğini kullanabilirsiniz. 

## <a name="delete-topics-and-subscriptions"></a>Konu başlıklarını ve abonelikleri silme

Konuları ve abonelikleri silmek için [Azure Portal][Azure portal] veya `delete_topic` metodunu kullanın. Aşağıdaki kod `mytopic`adlı konuyu siler:

```python
bus_service.delete_topic('mytopic')
```

Konu başlığı silindiğinde, konu başlığı altındaki tüm abonelikler silinir. Ayrıca, abonelikleri bağımsız olarak da silebilirsiniz. Aşağıdaki kod, `mytopic` konusunun `HighMessages` adlı aboneliği siler:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Varsayılan olarak, konular ve abonelikler kalıcı ve siz silene kadar mevcut. Belirli bir süre geçtikten sonra abonelikleri otomatik olarak silmek için, abonelikte [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) parametresini ayarlayabilirsiniz. 

> [!TIP]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, bir Service Bus ad alanına bağlanmanızı ve mesajlaşma varlıklarını kolayca yönetmenizi sağlar. Araç içeri/dışarı aktarma işlevselliği ve konuları, kuyrukları, abonelikleri, geçiş Hizmetleri, Bildirim Hub 'larını ve Olay Hub 'larını test etme özelliği gibi gelişmiş özellikler sağlar. 

## <a name="next-steps"></a>Sonraki adımlar

Service Bus konuların temellerini öğrendiğinize göre, daha fazla bilgi edinmek için aşağıdaki bağlantıları izleyin:

* [Kuyruklar, konu başlıkları ve abonelikler][Queues, topics, and subscriptions]
* [Sqlfilter. SqlExpression][SqlFilter.SqlExpression] başvurusu

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 

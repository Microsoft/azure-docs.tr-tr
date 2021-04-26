---
title: Azure Service Bus mesajlaşma-Java ileti hizmeti varlıkları
description: Bu makalede Java Message Service API 'SI aracılığıyla erişilebilen Azure Service Bus mesajlaşma varlıklarına genel bakış sunulmaktadır.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652590"
---
# <a name="java-message-service-jms-20-entities"></a>Java ileti hizmeti (JMS) 2,0 varlıkları

Azure Service Bus Premium 'a bağlanan ve [JMS kitaplığı Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) kullanarak istemci uygulamaları aşağıdaki varlıkları kullanabilir.

## <a name="queues"></a>Kuyruklar

JMS 'deki kuyruklar, geleneksel [Service Bus kuyruklarında](service-bus-queues-topics-subscriptions.md#queues)anlam ile karşılaştırılabilir.

Bir sıra oluşturmak için, sınıfında aşağıdaki yöntemleri kullanın `JMSContext` -

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Konu başlıkları

JMS 'deki konular, geleneksel [Service Bus konularıyla](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)anlam ile karşılaştırılabilir.

Bir konu oluşturmak için, sınıfında aşağıdaki yöntemleri kullanın `JMSContext` -

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Geçici kuyruklar

Bir istemci uygulaması, uygulamanın ömrü için mevcut olan geçici bir varlık gerektiriyorsa geçici kuyrukları kullanabilir. Bu varlıklar, [istek-yanıt](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) modelinde kullanılır.

Geçici bir sıra oluşturmak için, sınıfında aşağıdaki yöntemleri kullanın `JMSContext` -

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Geçici konular

Geçici kuyruklarda olduğu gibi, uygulama ömrü için mevcut olan geçici bir varlık aracılığıyla yayımla/abone ol ' u etkinleştirmek için geçici konular vardır.

Geçici bir konu oluşturmak için, sınıfında aşağıdaki yöntemleri kullanın `JMSContext` -

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Java ileti hizmeti (JMS) abonelikleri

Bunlar, [aboneliklerle](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) benzerdir (yani bir konu üzerinde mevcuttur ve yayımlama/abone olma semantiğini etkinleştirir), Java ileti hizmeti özelliği, belirli bir abonelik için **paylaşılan**, **paylaşılmayan**, * * dayanıklı ve **dayanıklı olmayan** özniteliklerin kavramlarını tanıtır.

> [!NOTE]
> Aşağıdaki abonelikler [Azure Service Bus JMS kitaplığı](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)kullanarak Azure Service Bus bağlanan istemci uygulamaları Için Azure Service Bus Premium katmanında kullanılabilir.
>
> Azure portal kullanılarak yalnızca dayanıklı abonelikler oluşturulabilir.
>

### <a name="shared-durable-subscriptions"></a>Paylaşılan dayanıklı abonelikler

Paylaşılan dayanıklı bir abonelik, bir konu üzerinde yayınlanan tüm iletiler, uygulamanın abonelikten her zaman etkin bir şekilde kullanılıp kullanılmadığına bakılmaksızın bir uygulama tarafından alınıp işleneceğinden kullanılır.

Service Bus ' den alma kimliği doğrulanmış herhangi bir uygulama, paylaşılan dayanıklı abonelikten alabilir.

Paylaşılan dayanıklı bir abonelik oluşturmak için sınıfında aşağıdaki yöntemleri kullanın `JMSContext` -

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

Paylaşılan dayanıklı abonelik, sınıfında yöntemi kullanılarak silinmedikleri takdirde mevcut olmaya devam eder `unsubscribe` `JMSContext` .

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Paylaşılmayan dayanıklı abonelikler

Paylaşılan dayanıklı bir abonelik gibi, bir konu üzerinde yayınlanan tüm iletiler, uygulamanın etkin bir şekilde abonelikle kullanılıp kullanılmadığına bakılmaksızın bir uygulama tarafından alınıp işleneceğinden, paylaşılmayan dayanıklı bir abonelik kullanılır.

Ancak, bu paylaşılmayan bir abonelik olduğundan, yalnızca aboneliği oluşturan uygulama bundan alabilir.

Paylaşılmayan dayanıklı bir abonelik oluşturmak için, sınıfından aşağıdaki yöntemleri kullanın `JMSContext` - 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`Özellik şu anda desteklenmiyor ve yoksayıldı.
>

Paylaşılmayan dayanıklı abonelik, sınıfında yöntemi kullanılarak silinmedikleri takdirde mevcut olmaya devam eder `unsubscribe` `JMSContext` .

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Paylaşılan, dayanıklı olmayan abonelikler

Birden çok istemci uygulamanın iletileri tek bir abonelikten alması ve işlemesi gerektiğinde, yalnızca bu bilgisayardan etkin bir şekilde tüketerek/alınana kadar, paylaşılan, dayanıklı olmayan bir abonelik kullanılır.

Abonelik dayanıklı olmadığından kalıcı değil. Üzerinde etkin bir tüketici olmadığında bu abonelik tarafından ileti alınmaz.

Paylaşılan, dayanıklı olmayan bir abonelik oluşturmak için, `JmsConsumer` sınıfında aşağıdaki yöntemlerle gösterildiği gibi bir oluşturun `JMSContext` -

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

Paylaşılan dayanıklı olmayan abonelik, bundan alınan etkin tüketiciler olana kadar mevcut olmaya devam eder.

### <a name="unshared-non-durable-subscriptions"></a>Paylaşılmayan kalıcı olmayan abonelikler

Yönetici olmayan bir abonelik, istemci uygulamanın iletiyi bir abonelikten alması ve işlemesi gerektiğinde, yalnızca onu etkin bir şekilde tüketene kadar kullanılır. Bu abonelikte yalnızca bir tüketici bulunabilir, diğer bir deyişle, aboneliği oluşturan istemcidir.

Abonelik dayanıklı olmadığından kalıcı değil. Üzerinde etkin bir tüketici olmadığında bu abonelik tarafından ileti alınmaz.

Paylaşılmayan dayanıklı olmayan bir abonelik oluşturmak için, `JMSConsumer` sınıfından aşağıdaki yöntemlerle gösterildiği gibi bir oluşturun `JMSContext` -

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`Özellik şu anda desteklenmiyor ve yoksayıldı.
>

Paylaşılmayan dayanıklı olmayan abonelik, bundan sonra gelen etkin bir tüketici olana kadar mevcut olmaya devam eder.

### <a name="message-selectors"></a>İleti seçicileri

Normal Service Bus abonelikler için **filtreler ve eylemler** gıbı, JMS abonelikleri Için de **ileti seçicileri** vardır.

İleti seçicileri her JMS aboneliğine ayarlanabilir ve ileti üst bilgisi özelliklerinde bir filtre koşulu olarak mevcuttur. Yalnızca ileti seçici ifadesiyle eşleşen üst bilgi özelliklerine sahip iletiler sunulur. Null veya boş bir dize değeri, JMS aboneliği/tüketicisi için bir ileti seçici olmadığını gösterir.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Java Ileti hizmeti (JMS) 2,0 abonelikleri için ek kavramlar

### <a name="client-scoping"></a>İstemci kapsamı

Java Ileti hizmeti (JMS) 2,0 API 'sinde belirtilen abonelikler, *belirli istemci uygulama/sn* (uygun ile tanımlanır) kapsamında olabilir veya bu şekilde kapsama sahip olmayabilir `clientId` .

Abonelik kapsamlandırıldıktan sonra, yalnızca aynı istemci kimliğine sahip istemci uygulamalarından **erişilebilir** . 

Başka bir istemci kimliğine sahip bir uygulamadan belirli bir istemci KIMLIĞINE (clientId2 clientId1) kapsamlı bir aboneliğe erişim girişimleri, diğer istemci KIMLIĞINE (clientId2) kapsamlı başka bir abonelik oluşturulmasına yol açacaktır.

> [!NOTE]
> İstemci KIMLIĞI null veya boş olabilir, ancak JMS istemci uygulamasında ayarlanan istemci KIMLIĞIYLE eşleşmesi gerekir. Azure Service Bus perspektifinden, null istemci KIMLIĞI ve boş bir istemci kimliği aynı davranışa sahiptir.
>
> İstemci KIMLIĞI null veya boş olarak ayarlandıysa, yalnızca istemci KIMLIĞI null ya da boş olarak ayarlanan istemci uygulamaları için erişilebilir.
>

### <a name="shareability"></a>Paylaşılabilirlik

**Paylaşılan** abonelikler birden çok istemci/tüketiciye (diğer bir deyişle, JMSConsumer nesneleri) onlardan ileti almasına izin verir.

>[!NOTE]
> Belirli bir istemci KIMLIĞI kapsamındaki paylaşılan abonelikler, hala birden çok istemci/tüketici (yani JMSConsumer nesneleri) tarafından erişilebilir, ancak istemci uygulamalarının her biri aynı istemci KIMLIĞINE sahip olmalıdır.
>
 

**Paylaşılmayan** abonelikler, yalnızca tek bir istemci/tüketiciye (yani JMSConsumer nesnesi), onlardan ileti almasına izin verir. Bir `JMSConsumer` paylaşılmayan abonelikte `JMSConsumer` , üzerinde ileti zaten etkin bir şekilde dinlemeden oluşturulduysa, bir oluşturulur `JMSException` .


### <a name="durability"></a>Dayanıklılık

**Dayanıklı** abonelikler kalıcı hale getirilir ve bir uygulamanın ( `JMSConsumer` ) bundan ileti tükettiği bağımsız olarak konudan ileti toplamaya devam eder.

**Dayanıklı olmayan** abonelikler kalıcı değildir ve bir uygulama ( `JMSConsumer` ) tarafından iletileri tükettiği sürece konudan ileti toplar. 

## <a name="representation-of-client-scoped-subscriptions"></a>İstemci kapsamlı abonelikler temsili

İstemci kapsamlı (JMS) aboneliklerinin var olan [aboneliklerde](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)birlikte bulunması gerekir, çünkü istemci kapsamlı (JMS) aboneliklerinin temsil ettiği şekilde aşağıdaki biçimi izleyin.

   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**D** (dayanıklı abonelikler için)
   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**ND** (dayanıklı olmayan abonelikler için)

Burada, **$** sınırlayıcıdır.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşma kullanma hakkında daha fazla bilgi ve örnekler için aşağıdaki gelişmiş konulara bakın:

* [Service Bus mesajlaşma hizmetine genel bakış](service-bus-messaging-overview.md)
* [Azure Service Bus Premium ile Java Ileti hizmeti 2,0 API 'sini kullanma](how-to-use-java-message-service-20.md)




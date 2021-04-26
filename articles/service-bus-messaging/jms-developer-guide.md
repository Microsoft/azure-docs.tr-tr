---
title: Azure Service Bus JMS 2,0 Geliştirici Kılavuzu
description: Azure Service Bus ile iletişim kurmak için Java Ileti hizmeti (JMS) 2,0 API 'sini kullanma
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726963"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Azure Service Bus JMS 2,0 Geliştirici Kılavuzu

Bu kılavuz, Java Ileti hizmeti (JMS) 2,0 API 'sini kullanarak Azure Service Bus ile iletişim kurarken başarılı olmanıza yardımcı olacak ayrıntılı bilgiler içerir.

Bir Java geliştiricisi olarak, Azure Service Bus yeni iseniz, lütfen aşağıdaki makaleleri okumayı göz önünde bulundurun.

| Kullanmaya başlama | Kavramlar |
|----------------|-------|
| <ul> <li> [Azure Service Bus nedir?](service-bus-messaging-overview.md) </li> <li> [Kuyruklar, konular ve abonelikler](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure Service Bus-Premium katmanı](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Java Ileti hizmeti (JMS) programlama modeli

Java Ileti hizmeti API programlama modeli aşağıda gösterildiği gibidir. 

> [!NOTE]
>
>**Azure Service Bus Premium katmanı** jms 1,1 ve jms 2,0 ' ü destekler.
> <br> <br>
> **Azure Service Bus-standart** katman, sınırlı jms 1,1 işlevlerini destekler. Daha fazla ayrıntı için lütfen bu [belgeye](service-bus-java-how-to-use-jms-api-amqp.md)başvurun.
>

# <a name="jms-20-programming-model"></a>[JMS 2,0 programlama modeli](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="JMS 2,0 programlama modelini gösteren diyagram." border="false":::

# <a name="jms-11-programming-model"></a>[JMS 1,1 programlama modeli](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="JMS 1,1 programlama modelini gösteren diyagram." border="false":::

---

## <a name="jms---building-blocks"></a>JMS-yapı taşları

Aşağıdaki yapı taşları JMS uygulamasıyla iletişim kurmak için kullanılabilir.

> [!NOTE]
> Aşağıdaki kılavuz, [Java Message Service (JMS) Için Oracle Java EE 6 öğreticisiyle](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html) uyarlanmıştır
>
> Java Ileti hizmeti 'nin (JMS) daha iyi anlamak için Bu öğreticiye başvurma önerilir.
>

### <a name="connection-factory"></a>Bağlantı fabrikası
Bağlantı fabrikası nesnesi, istemci tarafından JMS sağlayıcısına bağlanmak için kullanılır. Bağlantı fabrikası, yönetici tarafından tanımlanan bir bağlantı yapılandırma parametreleri kümesini kapsar.

Her bağlantı Fabrikası `ConnectionFactory` , `QueueConnectionFactory` veya arayüzün bir örneğidir `TopicConnectionFactory` .

Azure Service Bus ile bağlanmayı basitleştirmek için bu arabirimler `ServiceBusJmsConnectionFactory` , `ServiceBusJmsQueueConnectionFactory` ve `ServiceBusJmsTopicConnectionFactory` sırasıyla uygulanır. Bağlantı fabrikası aşağıdaki parametrelerle oluşturulabilir- 
   * Bağlantı dizesi-Azure Service Bus Premium katmanı ad alanı için bağlantı dizesi.
   * ServiceBusJmsConnectionFactorySettings özellik paketi şunu içerir
      * Connectionıdletimeoutms-boşta bağlantı zaman aşımı (milisaniye).
      * traceFrames-hata ayıklama için AMQP izleme çerçevelerini toplamak üzere Boole bayrağı.
      * *diğer yapılandırma parametreleri*

Fabrika aşağıdaki gibi oluşturulabilir. Bağlantı dizesi gerekli bir parametredir, ancak ek özellikler isteğe bağlıdır.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> JMS 2,0 API kullanan Java uygulamaları yalnızca bağlantı dizesini kullanarak Azure Service Bus bağlanmalıdır. Şu anda JMS istemcileri için kimlik doğrulaması yalnızca bağlantı dizesi kullanılarak desteklenir.
>
> Azure Active Directory (AAD) destekli kimlik doğrulaması şu anda desteklenmiyor.
>

### <a name="jms-destination"></a>JMS hedefi

Hedef, istemcinin oluşturduğu iletilerin hedefini ve tükettiği iletilerin kaynağını belirtmek için kullandığı nesnedir.

Hedefler Azure Service Bus-kuyruklarındaki varlıklara eşlenir (nokta senaryolarında) ve konular (Yayın-alt senaryolarında).

### <a name="connections"></a>Bağlantılar

Bir bağlantı, bir JMS sağlayıcısıyla sanal bağlantı kapsüller. Azure Service Bus, bu, uygulama ile AMQP üzerinden Azure Service Bus arasında durum bilgisi olan bir bağlantıyı temsil eder.

Bağlantı fabrikasından aşağıda gösterildiği gibi bir bağlantı oluşturulur.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Oturumlar

Oturum, ileti üretmek ve kullanmak için tek iş parçacıklı bir bağlamdır. İleti, ileti üreticileri ve tüketiciler oluşturmak için kullanılabilir, ancak aynı zamanda, bir atomik iş birimine gönderme ve alma gruplandırması sağlamak için de bir işlem bağlamı sağlar.

Bağlantı nesnesinden aşağıda gösterildiği gibi bir oturum oluşturulabilir.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Oturum modları

Aşağıdaki modlardan herhangi biriyle bir oturum oluşturulabilir.

| Oturum modları | Davranış |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | Oturum, bir kullanıcının alma çağrısından başarıyla döndürüldüğü veya ileti dinleyicisi tarafından başarıyla döndürülen iletiyi işlemek üzere çağrılmışsa, oturum otomatik olarak istemcinin bir ileti alındığını onaylar.|
|**Session.CLIENT_ACKNOWLEDGE** |İstemci, iletinin bildirim yöntemini çağırarak tüketilen bir iletiyi kabul etmiştir.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Bu bildirim modu, oturuma ileti teslimini bildirmek için geç bildirir.| 
|**Session.SESSION_TRANSACTED**|Bu değer, oturumun yerel bir işlem kullanması gerektiğini belirtmek için bağlantı nesnesindeki createSession (int sessionMode) yöntemine bağımsız değişken olarak geçirilebilir.| 

Oturum modu belirtilmediğinde, **Session.AUTO_ACKNOWLEDGE** varsayılan olarak çekilir.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext, JMS 2,0 belirtiminin bir parçası olarak tanımlanmıştır.
>

JMSContext, bağlantı ve oturum nesnesi tarafından sunulan işlevleri birleştirir. Bağlantı fabrikası nesnesinden oluşturulabilir.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext modları

Tıpkı **oturum** nesnesi gibi, JMSContext, [oturum modlarında](#session-modes)bahsedilen aynı bildirim modlarıyla oluşturulabilir.

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

Mod belirtilmediğinde, **JMSContext.AUTO_ACKNOWLEDGE** varsayılan olarak çekilir.

### <a name="jms-message-producers"></a>JMS ileti üreticileri

İleti üreticisi, bir JMSContext veya bir oturum kullanılarak oluşturulan ve bir hedefe ileti göndermek için kullanılan bir nesnedir.

Bu, aşağıdaki gibi tek başına bir nesne olarak oluşturulabilir. 

```java
JMSProducer producer = context.createProducer();
```

veya gönderilmek üzere bir ileti gerektiğinde çalışma zamanında oluşturulur.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>JMS ileti tüketicileri

İleti tüketicisi, bir JMSContext veya oturum tarafından oluşturulan ve bir hedefe gönderilen iletileri almak için kullanılan bir nesnedir. Bu, aşağıda gösterildiği gibi oluşturulabilir.

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Receive () yöntemi aracılığıyla zaman uyumlu alma

İleti tüketicisi, yöntemi aracılığıyla hedefteki iletileri almak için zaman uyumlu bir yol sağlar `receive()` .

Bir bağımsız değişken/zaman aşımı belirtilmemişse veya ' 0 ' zaman aşımı belirtilmişse, ileti kesilmediği veya bağlantı kopuk (hangisi daha önce olduğunda), tüketici süresiz olarak engellenir.

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

Sıfır olmayan pozitif bir bağımsız değişken sağlandığında, tüketici bu Zamanlayıcının süresi dolana kadar engeller.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>JMS ileti dinleyicileri ile zaman uyumsuz olarak alır

İleti dinleyicisi, bir hedefteki iletilerin zaman uyumsuz işlemesi için kullanılan bir nesnedir. `MessageListener` `onMessage` Belirli bir iş mantığının canlı olması gereken yöntemi içeren arabirimini uygular.

Bir ileti dinleyicisi nesnesi, yöntemi kullanılarak belirli bir ileti tüketicisine göre örneklenmeli ve kaydedilmelidir `setMessageListener` .

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Konularda kullanma

[JMS Ileti tüketicileri](#jms-message-consumers) , bir kuyruk veya konu olabilecek bir [hedefe](#jms-destination) göre oluşturulur.

Kuyruklarda bulunan tüketiciler, istemci uygulaması ve Azure Service Bus arasındaki oturum (ve bağlantı) bağlamında yalnızca istemci tarafı nesnelerdir.

Ancak konularda tüketiciler, 2 bölümden oluşur- 
   * Oturum (veya JMSContext) bağlamında bulunan bir **istemci tarafı nesnesi** ve,
   * Azure Service Bus bir varlık olan bir **abonelik** .

Abonelikler [burada](java-message-service-20-entities.md#java-message-service-jms-subscriptions) belgelenmiştir ve aşağıdakilerden biri olabilir 
   * Paylaşılan dayanıklı abonelikler
   * Paylaşılan, dayanıklı olmayan abonelikler
   * Paylaşılmayan dayanıklı abonelikler
   * Paylaşılmayan kalıcı olmayan abonelikler

### <a name="jms-queue-browsers"></a>JMS kuyruk tarayıcıları

JMS API 'SI, `QueueBrowser` uygulamanın kuyruktaki iletilere gözatmasını ve her ileti için üst bilgi değerlerini görüntülemesini sağlayan bir nesne sağlar.

Aşağıdaki gibi JMSContext kullanılarak bir kuyruk tarayıcısı oluşturulabilir.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> JMS API, bir konuya gözatan bir API sağlamaz.
>
> Bunun nedeni, konunun kendisini depolamadığından oluşur. İleti konuya gönderildiğinde, ilgili aboneliklerle iletilir.
>

### <a name="jms-message-selectors"></a>JMS Ileti seçicileri

İleti seçicileri, alınan iletileri filtrelemek için uygulamalar alarak kullanılabilir. İleti seçicileri sayesinde, alıcı uygulama, bu sorumluluğun kendisini almak yerine, iletileri (Bu durumda Azure Service Bus), bu sorumluluğu kendi kendine almak yerine, bir JMS sağlayıcısına (Bu örnekte,)

Aşağıdaki tüketicilerden herhangi birini oluştururken seçiciler kullanılabilir- 
   * Paylaşılan dayanıklı abonelik
   * Paylaşılmayan dayanıklı abonelik
   * Paylaşılan, dayanıklı olmayan abonelik
   * Paylaşılmayan, dayanıklı olmayan abonelik
   * Kuyruk tarayıcısı


## <a name="summary"></a>Özet

Bu Geliştirici Kılavuzu, Java Ileti hizmeti (JMS) kullanan Java istemci uygulamalarının Azure Service Bus nasıl bağlanabildiğini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Java Ileti hizmeti (JMS) varlıklarıyla ilgili Azure Service Bus ve ayrıntılar hakkında daha fazla bilgi için aşağıdaki bağlantılara göz atın- 
* [Service Bus-kuyruklar, konular ve abonelikler](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java Ileti hizmeti varlıkları](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure Service Bus 'da AMQP 1,0 desteği](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0 Geliştirici Kılavuzu](service-bus-amqp-dotnet.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Java Ileti hizmeti API 'SI (dış Oracle belgesi)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [ActiveMQ 'dan Service Bus geçirmeyi öğrenin](migrate-jms-activemq-to-servicebus.md)

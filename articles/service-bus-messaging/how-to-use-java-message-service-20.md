---
title: Azure Service Bus Premium ile Java Ileti hizmeti 2,0 API 'sini kullanma
description: Azure Service Bus ile Java Ileti hizmeti 'ni (JMS) kullanma
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 894821444f74248b73578595df943cb3a0025360
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698408"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>Azure Service Bus Premium ile Java Ileti hizmeti 2,0 API 'sini kullanma

Bu makalede, gelişmiş Ileti sıraya alma Protokolü (AMQP 1,0) protokolü üzerinden Azure Service Bus etkileşimde bulunmak için popüler **Java Ileti hizmeti (JMS) 2,0** API 'sinin nasıl kullanılacağı açıklanmaktadır.

> [!NOTE]
> Java Ileti hizmeti (JMS) 2,0 API desteği yalnızca **Azure Service Bus Premium katmanında** kullanılabilir.
>

## <a name="pre-requisites"></a>Ön koşullar

### <a name="get-started-with-service-bus"></a>Service Bus’ı kullanmaya başlama

Bu kılavuzda zaten bir Service Bus ad alanı olduğunu varsayılmaktadır. Bunu yapmazsanız, [Azure Portal](https://portal.azure.com)kullanarak [ad alanını ve kuyruğu oluşturabilirsiniz](service-bus-create-namespace-portal.md) . 

Service Bus ad alanları ve kuyrukları oluşturma hakkında daha fazla bilgi için, bkz. [Azure Portal ile Service Bus kuyrukları kullanmaya başlama](service-bus-quickstart-portal.md).

### <a name="set-up-a-java-development-environment"></a>Java geliştirme ortamı ayarlama

Java uygulamaları geliştirmek için, uygun geliştirme ortamını ayarlamanız gerekir- 
   * JDK (Java geliştirme seti) ya da JRE (Java Runtime Environment) yüklü.
   * JDK veya JRE derleme yoluna ve uygun sistem değişkenlerine eklenir.
   * JDK veya JRE 'yi kullanmak için bir Java IDE yüklenir. Örneğin, tutulma veya IntelliJ.

Geliştirici ortamınızı Azure 'da Java için hazırlama hakkında daha fazla bilgi edinmek için [Bu kılavuzu](/azure/developer/java/fundamentals/)kullanın.

## <a name="what-jms-features-are-supported"></a>Hangi JMS özellikleri destekleniyor?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Java Ileti hizmeti (JMS) istemci kitaplığı indiriliyor

Azure Service Bus Premium katmanında bulunan tüm özellikleri kullanmak için, aşağıdaki kitaplığın projenin yapı yoluna eklenmesi gerekir.

[Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> [Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) 'yi yapı yoluna eklemek Için [Maven](https://maven.apache.org/) veya [Gradle](https://gradle.org/)gibi projeniz için tercih edilen bağımlılık yönetimi aracını kullanın.
>

## <a name="coding-java-applications"></a>Java uygulamalarını kodlama

Bağımlılıklar içeri aktarıldıktan sonra, Java uygulamaları bir JMS sağlayıcısı tarafından belirsiz şekilde yazılabilir.

### <a name="connecting-to-azure-service-bus-using-jms"></a>JMS kullanarak Azure Service Bus bağlanma

JMS istemcilerini kullanarak Azure Service Bus ile bağlantı kurmak için, **birincil bağlantı dizesi** altındaki [Azure Portal](https://portal.azure.com) ' paylaşılan erişim ilkeleri ' içinde bulunan **bağlantı dizesine** ihtiyacınız vardır.

1. Şunu oluşturun `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. `ServiceBusJmsConnectionFactory`Uygun şekilde örneğini oluşturun `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Şunu `ConnectionFactory` oluşturmak için `Connection` öğesini kullanın ve ardından bir `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    veya a `JMSContext` (JMS 2,0 istemcileri için)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > Benzer şekilde adlandırılmış, bir JMS ' Session ' ve Service Bus ' Session ' tamamen birbirinden bağımsızdır.
    >
    > JMS 1,1 ' de oturum, API 'nin MessageProducer, MessageConsumer ve Iletinin kendisini oluşturmaya izin veren önemli bir yapı taşıdır. Daha ayrıntılı bilgi için [JMS API programlama modelini](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html) gözden geçirin
    >
    > Service Bus, oturumlarda ve aboneliklerde FıFO işlemeyi etkinleştirmek için [Oturumlar](message-sessions.md) hizmet ve istemci tarafı yapısıdır.
    >

### <a name="write-the-jms-application"></a>JMS uygulamasını yazma

`Session`Veya örneği oluşturulduktan sonra `JMSContext` uygulamanız, hem yönetim hem de veri işlemlerini gerçekleştirmek için tanıdık JMS API 'lerini kullanabilir.

Hangi API 'Lerin desteklendiğini görmek için [desteklenen JMS özellikleri](how-to-use-java-message-service-20.md#what-jms-features-are-supported) listesine bakın.

Aşağıda, JMS ile çalışmaya başlamak için bazı örnek kod parçacıkları verilmiştir.

#### <a name="sending-messages-to-a-queue-and-topic"></a>Bir kuyruğa ve konuya ileti gönderme

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Kuyruktan ileti alma

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Bir konuda paylaşılan dayanıklı bir abonelikten ileti alma

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Özet

Bu kılavuz, AMQP 1,0 üzerinden Java Ileti hizmeti (JMS) kullanan Java istemci uygulamalarının Azure Service Bus nasıl etkileşime gireceğini gösterir.

.NET, C, Python ve PHP gibi diğer dillerden AMQP 1,0 Service Bus de kullanabilirsiniz. Bu farklı diller kullanılarak oluşturulan bileşenler, Service Bus içinde AMQP 1,0 desteği kullanılarak güvenilir bir şekilde ve tam uygunlukta iletiler değiş tokuş edebilir.

## <a name="next-steps"></a>Sonraki adımlar

Java Ileti hizmeti (JMS) varlıklarıyla ilgili Azure Service Bus ve ayrıntılar hakkında daha fazla bilgi için aşağıdaki bağlantılara göz atın- 
* [Service Bus-kuyruklar, konular ve abonelikler](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java Ileti hizmeti varlıkları](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure Service Bus 'da AMQP 1,0 desteği](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0 Geliştirici Kılavuzu](service-bus-amqp-dotnet.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Java Ileti hizmeti API 'SI (dış Oracle belgesi)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [ActiveMQ 'dan Service Bus geçirmeyi öğrenin](migrate-jms-activemq-to-servicebus.md)
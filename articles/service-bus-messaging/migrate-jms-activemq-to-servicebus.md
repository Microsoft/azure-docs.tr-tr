---
title: Java Ileti hizmeti (JMS) uygulamalarını Apache ActiveMQ ' den Azure Service Bus | geçir | Microsoft Docs
description: Bu makalede, Azure Service Bus etkileşimde bulunmak için Apache ActiveMQ ile etkileşime geçen mevcut JMS uygulamalarının nasıl geçirileceği açıklanır.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: 4160a9ab4edbac8584eab2d4e5b9bf1ba11a9aec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568792"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Mevcut Java Ileti hizmeti (JMS) 2,0 uygulamalarını Apache ActiveMQ ' den Azure Service Bus geçirin

Bu makalede, bir JMS aracısıyla etkileşim kuran mevcut bir Java Ileti hizmeti (JMS) 2,0 uygulamasının, Azure Service Bus etkileşimde bulunmak için nasıl değiştirileceği açıklanır. Özellikle, makalede Apache ActiveMQ veya Amazon MQ ' den geçiş ele alınmaktadır.

Azure Service Bus, gelişmiş Ileti sıraya alma Protokolü (AMQP) üzerinden JMS 2,0 API kullanan Java 2 platformunu, Enterprise Edition ve yay iş yüklerini destekler.

## <a name="before-you-start"></a>Başlamadan önce

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure Service Bus ile Apache ActiveMQ arasındaki farklar

Azure Service Bus ve Apache ActiveMQ her ikisi de ileti aracılarıdır, istemci uygulamaları için JMS sağlayıcıları olarak çalışan ve iletileri ileti gönderme ve alma. Bunlar her ikisi de kuyruklarla noktadan noktaya semantiğini etkinleştirir ve konular ve abonelikler ile yayımlama-abonelik semantiğini etkinleştirir. 

Bu nedenle, aşağıdaki tabloda gösterildiği gibi, iki arasında bazı farklılıklar vardır:

| Kategori | ActiveMQ | Azure Service Bus |
| --- | --- | --- |
| Uygulama katmanlama | Kümelenmiş mimariden | İki katmanlı <br> (ağ geçidi + arka uç) |
| Protokol desteği | <ul> <li>AMQP</li> <li> RASTLAYABILIR </li> <li> Opentel </li> </ul> | AMQP |
| Sağlama modu | <ul> <li> Hizmet olarak altyapı (IaaS), şirket içi </li> <li> Amazon MQ (hizmet olarak yönetilen Platform) </li> | Hizmet olarak platform (PaaS) |
| İleti boyutu | Müşteri yapılandırılabilir | 1 MB (Premium katman) |
| Yüksek kullanılabilirlik | Müşteri tarafından yönetilen | Platform yönetiliyor |
| Olağanüstü durum kurtarma | Müşteri tarafından yönetilen | Platform yönetiliyor | 

### <a name="current-supported-and-unsupported-features"></a>Desteklenen ve desteklenmeyen geçerli özellikler

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Dikkat edilmesi gerekenler

Azure Service Bus iki katmanlı doğası, çeşitli iş sürekliliği özelliklerine sahiptir (yüksek kullanılabilirlik ve olağanüstü durum kurtarma). Ancak, JMS özelliklerini kullanırken bazı önemli noktalar vardır.

#### <a name="service-upgrades"></a>Hizmet yükseltmeleri

Service Bus yükseltmeleri ve yeniden başlatmalar söz konusu olduğunda geçici kuyruklar veya konular silinir. Uygulamanız geçici kuyruklarda veya konularda veri kaybına karşı duyarlıysa geçici kuyrukları veya konuları kullanmayın. Bunun yerine dayanıklı kuyruklar, konular ve abonelikler kullanın.

#### <a name="data-migration"></a>Veri geçişi

İstemci uygulamalarınızı Azure Service Bus etkileşimde bulunmak üzere geçirme ve değiştirme işleminin bir parçası olarak, ActiveMQ ' de tutulan veriler Service Bus öğesine geçirilmez. ActiveMQ kuyruklarını, konularını ve aboneliklerini boşaltmak için özel bir uygulamaya ihtiyacınız vardır ve ardından iletileri Service Bus kuyruklara, konularına ve aboneliklerine yeniden çalabilirsiniz.

#### <a name="authentication-and-authorization"></a>Kimlik doğrulaması ve yetkilendirme

Azure Active Directory tarafından desteklenen Azure rol tabanlı erişim denetimi (Azure RBAC), Service Bus için tercih edilen kimlik doğrulama mekanizmasıdır. Azure RBAC veya talep tabanlı kimlik doğrulaması şu anda Apache QPID JMS tarafından desteklenmediğinden, kimlik doğrulaması için SAS anahtarlarını kullanmanız gerekir.

## <a name="pre-migration"></a>Geçiş öncesi

### <a name="version-check"></a>Sürüm denetimi

JMS uygulamalarını yazarken aşağıdaki bileşenleri ve sürümleri kullanırsınız: 

| Bileşen | Sürüm |
|---|---|
| Java Ileti hizmeti (JMS) API | 1,1 veya üzeri |
| AMQP Protokolü | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>AMQP bağlantı noktalarının açık olduğundan emin olun

Service Bus AMQP protokolü üzerinden iletişimi destekler. Bu amaçla 5671 (AMQP) ve 443 (TCP) bağlantı noktaları üzerinden iletişimi etkinleştirin. İstemci uygulamalarının nerede barındırıldığına bağlı olarak, bu bağlantı noktaları üzerinden iletişime izin vermek için bir destek bileti gerekebilir.

> [!IMPORTANT]
> Service Bus yalnızca AMQP 1,0 protokolünü destekler.

### <a name="set-up-enterprise-configurations"></a>Kurumsal yapılandırmaların kurulumu

Service Bus, çeşitli kurumsal güvenlik ve yüksek kullanılabilirlik özelliklerine izin verebilir. Daha fazla bilgi için bkz. 

  * [Sanal ağ hizmet uç noktaları](service-bus-service-endpoints.md)
  * [Güvenlik duvarı](service-bus-ip-filtering.md)
  * [Müşterinin yönettiği anahtarla hizmet tarafı şifreleme (BYOK)](configure-customer-managed-key.md)
  * [Özel uç noktalar](private-link-service.md)
  * [Kimlik doğrulaması ve yetkilendirme](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>İzleme, uyarılar ve izleme

Her bir Service Bus ad alanı için ölçümleri Azure Izleyici üzerinde yayımlayın. Bu ölçümleri, ad alanına ayrılan kaynakların uyarı ve dinamik ölçeklendirilmesi için kullanabilirsiniz.

Farklı ölçümler ve bunların üzerinde Uyarılar ayarlama hakkında daha fazla bilgi için bkz. [Azure izleyici 'de Service Bus ölçümleri](service-bus-metrics-azure-monitor.md). Ayrıca, [veri işlemleri için istemci tarafı izleme](service-bus-end-to-end-tracing.md) ve [yönetim işlemleri için işletimsel/tanılama günlüğü](service-bus-diagnostic-logs.md)hakkında daha fazla bilgi edinebilirsiniz.

### <a name="metrics---new-relic"></a>Ölçümler-yeni relik

ActiveMQ haritalarından hangi ölçümlerin Azure Service Bus ' deki ölçümlerle ilişkilendirip ilişkilendirebelirtebilirsiniz. Yeni relik Web sitesinden aşağıdakilere bakın:

  * [ActiveMQ/Amazon MQ yeni relik ölçümleri](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Yeni relik ölçümleri Azure Service Bus](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Şu anda, yeni relik, ActiveMQ ile doğrudan, sorunsuz bir şekilde tümleştirilmesine sahip değildir, ancak Amazon MQ için kullanılabilir ölçümleri vardır. Amazon MQ, ActiveMQ ' den türetildiğinden aşağıdaki tablo, Amazon MQ ' dan Azure Service Bus yeni relik ölçümlerini eşler.
>

|Ölçüm gruplandırması| Amazon MQ/ActiveMQ ölçümü | Azure Service Bus ölçümü |
|------------|---------------------------|--------------------------|
|'Ndan|`CpuUtilization`|`CPUXNS`|
|'Ndan|`MemoryUsage`|`WSXNS`|
|'Ndan|`CurrentConnectionsCount`|`activeConnections`|
|'Ndan|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|'Ndan|`InactiveDurableTopicSubscribersCount`|Abonelik ölçümlerini kullanma|
|'Ndan|`TotalMessageCount`|Kuyruk/konu/abonelik düzeyi kullan `activeMessages`|
|Kuyruk/konu|`EnqueueCount`|`incomingMessages`|
|Kuyruk/konu|`DequeueCount`|`outgoingMessages`|
|Kuyruk|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Geçiş

Mevcut JMS 2,0 uygulamanızı Service Bus etkileşimde bulunmak üzere geçirmek için, sonraki birkaç bölümde bulunan adımları izleyin.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>ActiveMQ ' den topolojiyi dışarı aktarın ve Service Bus varlıkları oluşturun (isteğe bağlı)

İstemci uygulamalarının Service Bus sorunsuzca bağlanmasına emin olmak için, topolojiyi (kuyruklar, konular ve abonelikler dahil) Apache ActiveMQ ' dan Service Bus geçirin.

> [!NOTE]
> JMS uygulamaları için, çalışma zamanı işlemi olarak kuyruklar, konu başlıkları ve abonelikler oluşturursunuz. Çoğu JMS sağlayıcısı (ileti aracıları), çalışma zamanında bunları oluşturma olanağı sağlar. Bu, bu dışarı aktarma adımının isteğe bağlı olarak kabul edilmesine neden olur. Uygulamanızın çalışma zamanında topoloji oluşturma izinlerine sahip olduğundan emin olmak için, SAS izinleriyle bağlantı dizesini kullanın `Manage` .

Bunu yapmak için:

1. Topolojiyi dışarı aktarmak için [ActiveMQ komut satırı araçlarını](https://activemq.apache.org/activemq-command-line-tools-reference) kullanın.
1. [Azure Resource Manager şablonu](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)kullanarak aynı topolojiyi yeniden oluşturun.
1. Azure Resource Manager şablonunu çalıştırın.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>JMS uygulamasının Service Bus Maven bağımlılığını içeri aktarma

Service Bus ile sorunsuz bağlantı sağlamak için, `azure-servicebus-jms` paketi Maven dosyasına bir bağımlılık olarak `pom.xml` aşağıdaki gibi ekleyin:

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Uygulama sunucusu yapılandırma değişiklikleri

Bu bölüm, ActiveMQ ' a bağlanan istemci uygulamalarınızı barındıran uygulama sunucusuna özelleştirilir.

#### <a name="spring-applications"></a>Yay uygulamaları

##### <a name="update-the-applicationproperties-file"></a>Dosyayı güncelleştirme `application.properties`

ActiveMQ 'e bağlanmak için bir Spring Boot uygulaması kullanıyorsanız, ActiveMQ 'e özgü özellikleri dosyadan kaldırmak istersiniz `application.properties` .

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Sonra, Service Bus özgü özellikleri `application.properties` dosyaya ekleyin.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Değiştir `ActiveMQConnectionFactory``ServiceBusJmsConnectionFactory`

Bir sonraki adım, öğesinin örneğini `ActiveMQConnectionFactory` ile değiştirmek `ServiceBusJmsConnectionFactory` .

> [!NOTE] 
> Gerçek kod değişiklikleri uygulamaya özeldir ve bağımlılıkların yönetilmesi, ancak aşağıdaki örnek, nelerin değiştirilmesi gerektiğine ilişkin rehberlik sağlar.
>

Daha önce, bir nesnesini şu şekilde örnekleniyor olabilirsiniz `ActiveMQConnectionFactory` :

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Şimdi bunu bir nesnesinin örneğini oluşturmak için şu `ServiceBusJmsConnectionFactory` şekilde değiştiriyorsunuz:

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Geçiş sonrası

Artık uygulamayı Service Bus iletileri göndermeye ve almaya başlamak üzere değiştirdiğine göre, bunun beklendiği gibi çalıştığını doğrulamanız gerekir. Bu tamamlandığında, uygulama yığınınızı daha da belirginleştirmek ve modernleştirin için devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Service Bus ile sorunsuz tümleştirme için [JMS Azure Service Bus Için Spring Boot Starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) 'ı kullanın.

Service Bus mesajlaşma ve JMS hakkında daha fazla bilgi edinmek için bkz.:

* [JMS Service Bus](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)

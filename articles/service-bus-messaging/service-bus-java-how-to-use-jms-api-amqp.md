---
title: Java Message Service API & AMQP kullanın Azure Service Bus
description: Java Ileti hizmeti 'ni (JMS) Azure Service Bus ve Gelişmiş İleti Sıraya Alma Protokolü (AMQP) 1,0 ile kullanma.
services: service-bus-messaging
documentationcenter: java
author: axisc
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: c0c7e8b6066626966e2a72d474306bae4ead14c2
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027216"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Azure Service Bus ve AMQP 1,0 ile Java Ileti hizmeti 'ni (JMS) kullanma
Bu makalede, popüler Java Ileti hizmeti (JMS) API standardı kullanılarak Java uygulamalarından Azure Service Bus mesajlaşma özelliklerinin (kuyruklar ve yayımlama/abone olma konuları) nasıl kullanılacağı açıklanmaktadır. Azure Service Bus .NET API 'SI ile nasıl yapılacağını açıklayan bir [yardımcı makale](service-bus-amqp-dotnet.md) vardır. AMQP 1,0 kullanarak platformlar arası mesajlaşma hakkında bilgi edinmek için bu iki Kılavuzu birlikte kullanabilirsiniz.

Gelişmiş İleti Sıraya Alma Protokolü (AMQP) 1,0, güçlü, platformlar arası mesajlaşma uygulamaları oluşturmak için kullanabileceğiniz verimli, güvenilir, hat düzeyinde bir mesajlaşma protokolüdür.

Azure Service Bus ' de AMQP 1,0 desteği, verimli bir ikili protokol kullanarak bir dizi platformda sıraya alma ve yayımlama/Aracılı mesajlaşma özelliklerini de kullanabileceğiniz anlamına gelir. Ayrıca, bir dil, çerçeve ve işletim sistemi karışımı kullanılarak oluşturulan bileşenlerden oluşan uygulamalar oluşturabilirsiniz.

## <a name="get-started-with-service-bus"></a>Service Bus’ı kullanmaya başlama
Bu kılavuzda, **basicqueue**adlı bir sırayı içeren bir Service Bus ad alanınız zaten var. Bunu yapmazsanız, [Azure Portal](https://portal.azure.com)kullanarak [ad alanını ve kuyruğu oluşturabilirsiniz](service-bus-create-namespace-portal.md) . Service Bus ad alanları ve kuyrukları oluşturma hakkında daha fazla bilgi için, bkz. [Service Bus kuyrukları kullanmaya başlama](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Bölümlenmiş kuyruklar ve konular AMQP 'yi de destekler. Daha fazla bilgi için bkz. bölümlenmiş [mesajlaşma varlıkları](service-bus-partitioning.md) ve [Service Bus bölümlenmiş kuyruklar ve konular için AMQP 1,0 desteği](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>AMQP 1,0 JMS istemci kitaplığı indiriliyor
Apache Qpid JMS AMQP 1,0 istemci kitaplığı 'nın en son sürümünü indirecek hakkında daha fazla bilgi için [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)ziyaret edin.

Aşağıdaki dört JAR dosyasını Apache Qpid JMS AMQP 1,0 dağıtım arşivinden, Service Bus JMS uygulamaları oluşturup çalıştırırken Java SıNıFYOLUNA eklemeniz gerekir:

* GERONIMO-JMS\_1,1\_spec-1.0. jar
* qpid-JMS-Client-[sürüm]. jar

> [!NOTE]
> JMS JAR adları ve sürümleri değişmiş olabilir. Ayrıntılar için bkz. [Qpid JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Java uygulamalarını kodlama
### <a name="java-naming-and-directory-interface-jndi"></a>Java adlandırma ve Dizin arabirimi (JNDI)
JMS, mantıksal adlar ve fiziksel adlar arasında bir ayrım oluşturmak için Java adlandırma ve Dizin arabirimini (JNDı) kullanır. JNDı: ConnectionFactory ve Destination kullanılarak iki tür JMS nesnesi çözümlenir. JNDı, ad çözümlemesi görevlerini işlemek üzere farklı Dizin Hizmetleri ekleyebileceğiniz bir sağlayıcı modeli kullanır. Apache Qpid JMS AMQP 1,0 kitaplığı, aşağıdaki biçimdeki bir özellikler dosyası kullanılarak yapılandırılmış basit bir özellikler dosya tabanlı JNDı sağlayıcısı ile birlikte gelir:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>JNDı bağlamını kurma ve ConnectionFactory 'yi yapılandırma

**Birincil bağlantı dizesi** altındaki [Azure portalındaki](https://portal.azure.com) ' paylaşılan erişim ilkeleri ' içinde kullanılabilir olan **ConnectionString**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Üretici ve tüketici hedef sıralarını yapılandırma
JNDı sağlayıcısında Qpid Özellikler dosyasındaki bir hedefi tanımlamak için kullanılan giriş aşağıdaki biçimdedir:

Üretici için hedef kuyruğu oluşturmak için- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Tüketici için bir hedef sıra oluşturmak için 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>JMS uygulamasını yazma
JMS Service Bus ile kullanılırken hiçbir özel API veya seçenek gerekli değildir. Ancak, daha sonra ele alınacaktır bazı kısıtlamalar vardır. Her JMS uygulamasında olduğu gibi, gereken ilk şey JNDı ortamının yapılandırması ve bir **Connectionfactory** ve hedefleri çözebilmelidir.

#### <a name="configure-the-jndi-initialcontext"></a>JNDı InitialContext 'i yapılandırma
JNDı ortamı, bir yapılandırma bilgileri Hashtable 'ı javax. Naming. InitialContext sınıfının oluşturucusuna geçirerek yapılandırılır. Hashtable 'daki iki gerekli öğe, Ilk bağlam fabrikasının sınıf adı ve sağlayıcı URL 'sidir. Aşağıdaki kod, JNDı ortamının, **ServiceBus. Properties**adlı bir özellikler dosyası ile Qndı sağlayıcısı 'NıN Qndı sağlayıcısını kullanmak için nasıl yapılandırılacağını gösterir.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Service Bus kuyruğu kullanan basit bir JMS uygulaması
Aşağıdaki örnek program, JNDı mantıksal adına sahip bir Service Bus kuyruğuna JMS TextMessages gönderir ve iletileri geri alır.

Tüm kaynak kodu ve yapılandırma bilgilerine, [JMS kuyruğundan Azure Service Bus örnekleri hızlı başlangıç](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart) erişebilirsiniz

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Uygulamayı çalıştırma
Uygulamayı çalıştırmak için, paylaşılan erişim Ilkelerinden **bağlantı dizesini** geçirin.
Aşağıda, uygulamayı çalıştırarak formun çıktısı verilmiştir:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP değerlendirmesi ve Service Bus işlem eşleme
Bir AMQP değerlendirmesi Service Bus bir işleme çevrilir:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS konuları ile Service Bus konuları
Java Ileti hizmeti (JMS) API 'SI aracılığıyla Azure Service Bus konuları ve abonelikleri kullanmak, temel gönderme ve alma özellikleri sağlar. JMS uyumlu API 'Ler ile diğer ileti aracılarından uygulamalar taşırken kullanışlı bir seçenektir. Service Bus konular, JMS konularından farklı olsa da birkaç ayarlama gerektirir. 

Azure Service Bus, iletileri Azure Kaynak yönetimi arabirimi, Azure komut satırı araçları veya Azure portal aracılığıyla yönetilen adlandırılmış, paylaşılan ve dayanıklı aboneliklere yönlendirir. Her abonelik, her biri bir filtre koşuluna sahip olabilecek ve SQL filtreleri için de bir meta veri dönüştürme eylemi olan 2000 adede kadar seçim kuralına izin verir. Her filtre koşulu eşleşmesi, aboneliğe kopyalanacak giriş iletisini seçer.  

Aboneliklerden ileti alma, kuyruklardan alınan iletileri aynı alıyor. Her aboneliğin ilişkili bir atılacak ileti sırası ve iletileri otomatik olarak başka bir kuyruğa veya konuya iletme yeteneği vardır. 

JMS konuları, istemcilerin isteğe bağlı olarak ileti seçiciyle ileti filtrelemesine izin veren dayanıklı ve dayanıklı aboneler dinamik olarak oluşturmalarına olanak tanır. Bu paylaşılmayan varlıklar Service Bus tarafından desteklenmez. Service Bus için SQL filtre kuralı sözdizimi, ancak JMS tarafından desteklenen ileti seçici sözdizimine benzer. 

JMS konu yayımcı tarafı, bu örnekte gösterildiği gibi Service Bus uyumludur, ancak dinamik aboneler değildir. Aşağıdaki topolojiyle ilgili JMS API 'Leri Service Bus desteklenmez. 

## <a name="unsupported-features-and-restrictions"></a>Desteklenmeyen özellikler ve kısıtlamalar
Aşağıdaki kısıtlamalar, Service Bus ile AMQP 1,0 üzerinden JMS kullanılırken mevcuttur, yani:

* **Oturum**başına yalnızca bir **Messageproducer** veya **messageconsumer** 'a izin verilir. Bir uygulamada birden çok **MessageProducers** veya **messagetüketiciler** oluşturmanız gerekiyorsa, bunların her biri için özel bir **oturum** oluşturun.
* Geçici konu abonelikleri şu anda desteklenmiyor.
* **Messageseçiciler** Şu anda desteklenmiyor.
* İşlem temelli oturumlar ve dağıtılmış işlemler desteklenmez.

Ayrıca, Azure Service Bus denetim düzlemi 'ni veri düzleminden ayırır ve bu nedenle JMS 'nin dinamik topoloji işlevlerinin birkaçını desteklemez:

| Desteklenmeyen Yöntem          | Şununla değiştir                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| Createdurableabonesi     | ileti seçiciyi taşıma konu aboneliği oluşturma                                 |
| createDurableConsumer       | ileti seçiciyi taşıma konu aboneliği oluşturma                                 |
| createSharedConsumer        | Service Bus konular her zaman paylaşılabilir, yukarıya bakın                                       |
| createSharedDurableConsumer | Service Bus konular her zaman paylaşılabilir, yukarıya bakın                                       |
| createTemporaryTopic        | bir sona erme dönemi için, *oto Deleteonıdle* kümesine sahıp yönetim API/araçlar/Portal aracılığıyla bir konu oluşturun |
| createTopic                 | Yönetim API 'SI/araçlar/Portal aracılığıyla bir konu oluşturun                                           |
| kaldırmak                 | Konu yönetimi API/araçlar/Portal 'ı silme                                             |
| createBrowser               | Desteklenen. Service Bus API 'sinin Peek () işlevini kullanma                         |
| createQueue                 | Yönetim API 'SI/araçlar/Portal aracılığıyla kuyruk oluşturma                                           | 
| createTemporaryQueue        | bir sona erme dönemi için, *oto Deleteonıdle* ayarlanmış olan yönetim API/araçlar/Portal aracılığıyla kuyruk oluşturma |
| receiveNoWait               | Service Bus SDK tarafından sunulan Receive () yöntemini kullanın ve çok düşük veya sıfır zaman aşımı belirtin |

## <a name="summary"></a>Özet
Bu nasıl yapılır kılavuzunda, popüler JMS API ve AMQP 1,0 kullanılarak Java 'dan Service Bus Aracılı mesajlaşma özelliklerinin (kuyruklar ve yayımlama/abone olma konuları) nasıl kullanılacağı gösterildi.

.NET, C, Python ve PHP gibi diğer dillerden AMQP 1,0 Service Bus de kullanabilirsiniz. Bu farklı diller kullanılarak oluşturulan bileşenler, Service Bus içinde AMQP 1,0 desteği kullanılarak güvenilir bir şekilde ve tam uygunlukta iletiler değiş tokuş edebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Service Bus 'da AMQP 1,0 desteği](service-bus-amqp-overview.md)
* [Service Bus .NET API ile AMQP 1,0 kullanma](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1,0 Geliştirici Kılavuzu](service-bus-amqp-dotnet.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Java Geliştirici Merkezi](https://azure.microsoft.com/develop/java/)


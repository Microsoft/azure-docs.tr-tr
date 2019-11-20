---
title: Apache Kafka için Akka akışlarını kullanma-Azure Event Hubs | Microsoft Docs
description: Bu makalede, Akka akışlarını Apache Kafka etkin bir Azure Olay Hub 'ına bağlama hakkında bilgi sağlanır.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ba81ce88bcdf039d020dcd945e45a11cf603c114
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555760"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Apache Kafka için Event Hubs ile Akka akışlarını kullanma
Bu öğreticide, protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Akka akışlarını Kafka özellikli Olay Hub 'larına nasıl bağlayabilmeniz gösterilmektedir. Kafka için Azure Event Hubs [Apache Kafka 1,0 sürümünü destekler.](https://kafka.apache.org/10/documentation.html)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Örnek projeyi kopyalama
> * Akka Streams üreticisi çalıştırma 
> * Akka akışları tüketicisini Çalıştır

> [!NOTE]
> Bu örnek [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)'da sağlanır

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullara sahip olduğunuzdan emin olun:

* [Apache Kafka için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) makalesini okuyun. 
* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * Ubuntu’da JDK’yi yüklemek için `apt-get install default-jdk` komutunu çalıştırın.
    * JAVA_HOME ortam değişkenini JDK’nin yüklü olduğu klasöre işaret edecek şekilde ayarladığınızdan emin olun.
* Maven ikili Arşivi [indirme](https://maven.apache.org/download.cgi) ve [yükleme](https://maven.apache.org/install.html)
    * Ubuntu’da Maven’i yüklemek için `apt-get install maven` komutunu çalıştırabilirsiniz.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu’da Git’i yüklemek için `sudo apt-get install git` komutunu çalıştırabilirsiniz.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs ad alanı oluşturma

Herhangi bir Event Hubs hizmetinden göndermek veya almak için bir Event Hubs ad alanı gerekir. Event Hubs Kafka uç noktası alma hakkında bilgi için bkz. [Create Kafka Enabled Event Hubs](event-hubs-create-kafka-enabled.md) . Event Hubs bağlantı dizesini daha sonra kullanmak üzere kopyalamadığınızdan emin olun.

## <a name="clone-the-example-project"></a>Örnek projeyi kopyalama

Artık Kafka özellikli Event Hubs bağlantı dizesine sahip olduğunuza göre, Azure Event Hubs for Kafka Repository 'yi klonlayın ve `akka` alt klasörüne gidin:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Akka Streams üreticisi çalıştırma

Sunulan Akka Streams üreticisi örneğini kullanarak, Event Hubs hizmetine ileti gönderin.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka uç noktası sağlama

#### <a name="producer-applicationconf"></a>Producer Application. conf

Üreticiyi doğru kimlik doğrulamasıyla Event Hubs Kafka uç noktasına yönlendirmek için `producer/src/main/resources/application.conf` `bootstrap.servers` ve `sasl.jaas.config` değerlerini güncelleştirin.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Üretici 'yi komut satırından Çalıştır

Producer 'ı komut satırından çalıştırmak için, JAR 'yi oluşturun ve Maven içinden çalıştırın (veya Maven kullanarak JAR 'yi oluşturun ve ardından, gerekli Kafka JAR 'leri, Sınıfyoluna ekleyerek Java 'da çalıştırın):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Üretici, `test` konu başlığı altında Kafka etkin olay hub 'ına olay göndermeye başlar ve STDOUT 'a olayları yazdırır.

## <a name="run-akka-streams-consumer"></a>Akka akışları tüketicisini Çalıştır

Sağlanan tüketici örneğini kullanarak, Kafka etkin olay hub 'larından iletiler alın.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka uç noktası sağlama

#### <a name="consumer-applicationconf"></a>Tüketici uygulaması. conf

@No__t_0 ve `sasl.jaas.config` `consumer/src/main/resources/application.conf` değerlerini, tüketiciye doğru kimlik doğrulama ile Event Hubs Kafka uç noktasına yönlendirmek için güncelleştirin.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Komut satırından tüketici çalıştırma

Tüketici 'yi komut satırından çalıştırmak için, JAR 'yi oluşturun ve Maven içinden çalıştırın (veya Maven kullanarak JAR 'yi oluşturun ve ardından, gerekli Kafka JAR 'leri, Sınıfyoluna ekleyerek Java 'da çalıştırın):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Kafka etkin olay hub 'ının olayları varsa (örneğin, üreticisi de çalışıyorsa), tüketici `test` konudan olay almaya başlar. 

Akka akışları hakkında daha ayrıntılı bilgi için [Akka Streams Kafka kılavuzuna](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) göz atın.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Akka akışlarını Kafka özellikli Olay Hub 'larına bağlamayı öğrendiniz. Kafka için Azure Event Hubs [Apache Kafka 1,0 sürümünü destekler.](https://kafka.apache.org/10/documentation.html) Bu öğreticinin bir parçası olarak aşağıdaki eylemleri yaptınız: 

> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Örnek projeyi kopyalama
> * Akka Streams üreticisi çalıştırma 
> * Akka akışları tüketicisini Çalıştır

Event Hubs ve Kafka için Event Hubs hakkında daha fazla bilgi edinmek için aşağıdaki konuya bakın:  

- [Event Hubs hakkında bilgi edinin](event-hubs-what-is-event-hubs.md)
- [Apache Kafka için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka özellikli Event Hubs oluşturma](event-hubs-create-kafka-enabled.md)
- [Kafka uygulamalarınızdan Event Hubs'a akış yapma](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka özellikli bir olay hub'ında Kafka aracısını yansıtma](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark'ı Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink'i Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect'i Kafka özellikli olay hub'ıyla tümleştirme](event-hubs-kafka-connect-tutorial.md)
- [GitHub'ımızdaki örnekleri inceleme](https://github.com/Azure/azure-event-hubs-for-kafka)

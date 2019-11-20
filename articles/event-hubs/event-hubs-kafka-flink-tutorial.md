---
title: Apache Kafka için Apache flink kullanın-Azure Event Hubs | Microsoft Docs
description: Bu makalede, Apache flink Apache Kafka etkinleştirilmiş bir Azure Olay Hub 'ına nasıl bağlanabileceğiniz hakkında bilgi verilmektedir.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 881546a97b01bef993cc24c6b868ec97ddf5ac36
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555725"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Apache Kafka için Azure Event Hubs ile Apache flink kullanın
Bu öğreticide, protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Apache flink 'i Kafka özellikli Olay Hub 'larına nasıl bağlayabileceğiniz gösterilmektedir. Azure Event Hubs [Apache Kafka 1,0 sürümünü destekliyor.](https://kafka.apache.org/10/documentation.html).

Apache Kafka kullanmanın önemli avantajlarından biri, bağlanamaların ekosistemidir. Kafka Enabled Event Hubs, Azure ekosisteminin ölçeklenebilirlik, tutarlılık ve desteğiyle birlikte Kafka esnekliğini birleştirir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Örnek projeyi kopyalama
> * Flink üreticisi Çalıştır 
> * Flink tüketicisi Çalıştır

> [!NOTE]
> Bu örnek [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)'da sağlanır

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullara sahip olduğunuzdan emin olun:

* [Apache Kafka için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) makalesini okuyun. 
* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Ubuntu’da JDK’yi yüklemek için `apt-get install default-jdk` komutunu çalıştırın.
    * JAVA_HOME ortam değişkenini JDK’nin yüklü olduğu klasöre işaret edecek şekilde ayarladığınızdan emin olun.
* Maven ikili Arşivi [indirme](https://maven.apache.org/download.cgi) ve [yükleme](https://maven.apache.org/install.html)
    * Ubuntu’da Maven’i yüklemek için `apt-get install maven` komutunu çalıştırabilirsiniz.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu’da Git’i yüklemek için `sudo apt-get install git` komutunu çalıştırabilirsiniz.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs ad alanı oluşturma

Herhangi bir Event Hubs hizmetinden göndermek veya almak için bir Event Hubs ad alanı gerekir. Event Hubs Kafka uç noktası alma hakkında bilgi için bkz. [Create Kafka Enabled Event Hubs](event-hubs-create-kafka-enabled.md) . Event Hubs bağlantı dizesini daha sonra kullanmak üzere kopyalamadığınızdan emin olun.

## <a name="clone-the-example-project"></a>Örnek projeyi kopyalama

Artık Kafka özellikli Event Hubs bağlantı dizesine sahip olduğunuza göre, Azure Event Hubs for Kafka Repository 'yi klonlayın ve `flink` alt klasörüne gidin:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Flink üreticisi Çalıştır

Belirtilen flink üreticisi örneğini kullanarak, Event Hubs hizmetine ileti gönderin.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka uç noktası sağlama

#### <a name="producerconfig"></a>üretici. config

Üreticiyi doğru kimlik doğrulamasıyla Event Hubs Kafka uç noktasına yönlendirmek için `producer/src/main/resources/producer.config` `bootstrap.servers` ve `sasl.jaas.config` değerlerini güncelleştirin.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Üretici 'yi komut satırından Çalıştır

Producer 'ı komut satırından çalıştırmak için, JAR 'yi oluşturun ve Maven içinden çalıştırın (veya Maven kullanarak JAR 'yi oluşturun ve ardından, gerekli Kafka JAR 'leri, Sınıfyoluna ekleyerek Java 'da çalıştırın):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Üretici artık `test` konu başlığı altında Kafka etkin olay hub 'ına olay göndermeye başlayacak ve olayları stdout 'a yazdıracaktır.

## <a name="run-flink-consumer"></a>Flink tüketicisi Çalıştır

Sağlanan tüketici örneğini kullanarak, Kafka etkin Event Hubs iletiler alın.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka uç noktası sağlama

#### <a name="consumerconfig"></a>Consumer. config

@No__t_0 ve `sasl.jaas.config` `consumer/src/main/resources/consumer.config` değerlerini, tüketiciye doğru kimlik doğrulama ile Event Hubs Kafka uç noktasına yönlendirmek için güncelleştirin.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Komut satırından tüketici çalıştırma

Tüketici 'yi komut satırından çalıştırmak için, JAR 'yi oluşturun ve Maven içinden çalıştırın (veya Maven kullanarak JAR 'yi oluşturun ve ardından, gerekli Kafka JAR 'leri, Sınıfyoluna ekleyerek Java 'da çalıştırın):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Kafka etkin olay hub 'ının olayları varsa (örneğin, üreticisi de çalışıyorsa), tüketici artık `test` konudan olay almaya başlar.

Flink 'in Kafka 'e bağlanması hakkında daha ayrıntılı bilgi için [flink 'In Kafka bağlayıcı kılavuzuna](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) göz atın.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, protokol istemcilerinizi değiştirmeden veya kendi kümelerinizi çalıştırmadan Apache flink 'i Kafka özellikli Olay Hub 'larına bağlamayı öğrendiniz. Bu öğreticinin bir parçası olarak aşağıdaki adımları gerçekleştirdiniz: 

> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Örnek projeyi kopyalama
> * Flink üreticisi Çalıştır 
> * Flink tüketicisi Çalıştır

Event Hubs ve Kafka için Event Hubs hakkında daha fazla bilgi edinmek için aşağıdaki konuya bakın:  

- [Event Hubs hakkında bilgi edinin](event-hubs-what-is-event-hubs.md)
- [Apache Kafka için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka özellikli Event Hubs oluşturma](event-hubs-create-kafka-enabled.md)
- [Kafka uygulamalarınızdan Event Hubs'a akış yapma](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka özellikli bir olay hub'ında Kafka aracısını yansıtma](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark'ı Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-spark-tutorial.md)
- [Kafka Connect'i Kafka özellikli olay hub'ıyla tümleştirme](event-hubs-kafka-connect-tutorial.md)
- [Akka Streams’i Kafka özellikli olay hub'ına bağlama](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub'ımızdaki örnekleri inceleme](https://github.com/Azure/azure-event-hubs-for-kafka)

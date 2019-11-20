---
title: Apache Spark App ile bağlantı kurmak-Azure Event Hubs | Microsoft Docs
description: Bu makalede, Kafka için Azure Event Hubs ile Apache Spark kullanma hakkında bilgi sağlanır.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 5bd25604844ce0521b6bac2d637a2d48a6ef089b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555742"
---
# <a name="connect-your-apache-spark-application-with-kafka-enabled-azure-event-hubs"></a>Apache Spark uygulamanızı Kafka özellikli Azure Event Hubs'a bağlama
Bu öğreticide, gerçek zamanlı akış için Spark uygulamanızı Kafka özellikli Event Hubs'a bağlama işleminde size yol gösterilir. Bu tümleştirme, protokol istemcilerinizi değiştirmek ya da kendi Kafka veya Zookeeper kümelerinizi çalıştırmak zorunda kalmadan akış yapmanıza olanak tanır. Bu öğretici için Apache Spark v2.4+ ve Apache Kafka v2.0+ gerekir.

> [!NOTE]
> Bu örnek [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)'da sağlanır

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Örnek projeyi kopyalama
> * Spark'ı çalıştırma
> * Kafka için Event Hubs'dan okuma
> * Kafka için Event Hubs'a yazma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce şunlara sahip olduğunuzdan emin olun:
-   Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> Spark-Kafka bağdaştırıcısı Spark v2.4'ten itibaren Kafka v2.0'ı destekleyecek şekilde güncelleştirildi. Spark'ın önceki sürümlerinde, bağdaştırıcı Kafka v0.10 ve üstünü destekliyordu ama özel olarak Kafka v0.10 API'lerine dayanıyordu. Kafka için Event Hubs Kafka v0.10'u desteklemediğinden, Spark'ın v2.4'ten önceki sürümlerinden Spark-Kafka bağdaştırıcıları Kafka için Event Hubs Ekosistemlerinde desteklenmez.


## <a name="create-an-event-hubs-namespace"></a>Event Hubs ad alanı oluşturma
Herhangi bir Event Hubs hizmetinden göndermek ve almak için Event Hubs ad alanı gereklidir. Event Hubs Kafka uç noktası alma yönergeleri için bkz. [Kafka özellikli Event Hubs oluşturma](event-hubs-create.md). Daha sonra kullanmak üzere Event Hubs bağlantı dizesini ve tam etki alanı adını (FQDN) alın. Yönergeler için bkz. [Event Hubs bağlantı dizesi alma](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Örnek projeyi kopyalama
Azure Event Hubs deposunu kopyalayın ve `tutorials/spark` alt klasörüne gidin:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Kafka için Event Hubs'dan okuma
Birkaç yapılandırma değişikliğiyle, Kafka için Event Hubs'dan okumaya başlayabilirsiniz. **BOOTSTRAP_SERVERS** ve **EH_SASL** öğelerini ad alanınızdan gelen ayrıntılarla güncelleştirin. Bundan sonra aynı Kafka'yla yaptığınız gibi Event Hubs ile akışı başlatabilirsiniz. Örnek kodun tamamı için GitHub'da sparkConsumer.scala dosyasına bakın. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "false")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Kafka için Event Hubs'a yazma
Ayrıca, Kafka 'e yazdığınız şekilde Event Hubs de yazabilirsiniz. Yapılandırmanızı güncelleştirip **BOOTSTRAP_SERVERS** ve **EH_SASL** öğelerini Event Hubs ad alanınızdan gelen bilgilerle değiştirmeyi unutmayın.  Örnek kodun tamamı için GitHub'da sparkProducer.scala dosyasına bakın. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Spark-Kafka bağlayıcısını ve Kafka için Event Hubs'ı kullanarak akış yapmayı öğrendiniz. Aşağıdaki adımları gerçekleştirdiniz: 

> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Örnek projeyi kopyalama
> * Spark'ı çalıştırma
> * Kafka için Event Hubs'dan okuma
> * Kafka için Event Hubs'a yazma

Event Hubs ve Kafka için Event Hubs hakkında daha fazla bilgi edinmek için aşağıdaki konuya bakın:  

- [Event Hubs hakkında bilgi edinin](event-hubs-what-is-event-hubs.md)
- [Apache Kafka için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka özellikli Event Hubs oluşturma](event-hubs-create-kafka-enabled.md)
- [Kafka uygulamalarınızdan Event Hubs'a akış yapma](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka özellikli bir olay hub'ında Kafka aracısını yansıtma](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Flink'i Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect'i Kafka özellikli olay hub'ıyla tümleştirme](event-hubs-kafka-connect-tutorial.md)
- [Akka Streams’i Kafka özellikli olay hub'ına bağlama](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub'ımızdaki örnekleri inceleme](https://github.com/Azure/azure-event-hubs-for-kafka)

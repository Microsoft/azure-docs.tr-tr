---
title: Apache Kafka MirrorMaker 'ı kullanın-Azure Event Hubs | Microsoft Docs
description: Bu makalede, AzureEvent hub 'Larda bir Kafka kümesini yansıtmak için Kafka MirrorMaker 'ın nasıl kullanılacağı hakkında bilgi verilmektedir.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6d1596cf0a50ed5dcb896896282178b6fc12c1a1
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555115"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Apache Kafka için Event Hubs ile Kafka MirrorMaker 'ı kullanma

Bu öğreticide, Kafka MirrorMaker kullanılarak Kafka etkin bir olay hub 'ında Kafka aracısının nasıl yansıtılması gösterilmektedir.

   ![Event Hubs ile Kafka MirrorMaker](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Bu örnek [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)'da sağlanır


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Örnek projeyi kopyalama
> * Kafka kümesi ayarlama
> * Kafka MirrorMaker 'ı yapılandırma
> * Kafka MirrorMaker 'ı Çalıştır

## <a name="introduction"></a>Tanıtım
Modern bulut ölçekli uygulamalar için önemli bir göz önünde bulundurun, hizmet kesintiye uğramadan altyapıyı güncelleştirebilir, geliştirebilirsiniz ve değiştirebilirsiniz. Bu öğreticide, Kafka etkin bir olay hub 'ı ve Kafka MirrorMaker 'ın mevcut bir Kafka işlem hattını, Event Hubs hizmetindeki Kafka giriş akışını "yansıtarak" bir Azure ile nasıl tümleştirebileceğinizi gösterir. 

Azure Event Hubs Kafka uç noktası, Azure Event Hubs 'a Kafka protokolünü (yani, Kafka istemcileri) kullanarak bağlanmanızı sağlar. Bir Kafka uygulamasında en az değişiklik yaparak Azure Event Hubs bağlanabilir ve Azure ekosisteminin avantajlarından yararlanabilirsiniz. Kafka Enabled Event Hubs Şu anda Kafka sürümleri 1,0 ve üstünü desteklemektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için şunlar sahip olduğunuzdan emin olun:

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

Herhangi bir Event Hubs hizmetinden göndermek ve almak için Event Hubs ad alanı gereklidir. Event Hubs Kafka uç noktası alma yönergeleri için bkz. [Kafka özellikli Event Hubs oluşturma](event-hubs-create.md). Event Hubs bağlantı dizesini daha sonra kullanmak üzere kopyalamadığınızdan emin olun.

## <a name="clone-the-example-project"></a>Örnek projeyi kopyalama

Artık bir Kafka etkin Event Hubs bağlantı dizesine sahip olduğunuza göre, Azure Event Hubs for Kafka Repository 'yi klonlayın ve `mirror-maker` alt klasörüne gidin:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka kümesi ayarlama

İstenen ayarlarla bir küme ayarlamak için [Kafka hızlı başlangıç kılavuzunu](https://kafka.apache.org/quickstart) kullanın (veya var olan bir Kafka kümesini kullanın).

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker 'ı yapılandırma

Kafka MirrorMaker bir akışın "yansıtmasını" sunar. Kaynak ve hedef Kafka kümeleri verildiğinde, MirrorMaker kaynak kümeye gönderilen tüm iletilerin hem kaynak hem de hedef kümeler tarafından alınmasını sağlar. Bu örnekte, hedef Kafka etkin bir olay hub 'ı ile bir kaynak Kafka kümesinin nasıl yansıtılması gösterilmektedir. Bu senaryo, verilerin akışını kesintiye uğramadan mevcut bir Kafka işlem hattından Event Hubs veri göndermek için kullanılabilir. 

Kafka MirrorMaker hakkında daha ayrıntılı bilgi için bkz. [Kafka yansıtma/MirrorMaker Kılavuzu](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Kafka MirrorMaker 'ı yapılandırmak için BT 'nin BT/kaynak olarak bir Kafka kümesi ve üreticisi/hedefi olarak Kafka özellikli bir olay hub 'ı sağlayın.

#### <a name="consumer-configuration"></a>Tüketici yapılandırması

@No__t_0 müşteri yapılandırma dosyasını güncelleştirin, bu, MirrorMaker kaynak Kafka kümesinin özelliklerine söyler.

##### <a name="source-kafkaconfig"></a>Kaynak-Kafka. config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Üretici yapılandırması

Şimdi, MirrorMaker 'ın yinelenen (veya "yansıtmalı") verileri Event Hubs hizmetine göndermesini bildiren `mirror-eventhub.config` üretici yapılandırma dosyasını güncelleştirin. Özellikle, `bootstrap.servers` ve `sasl.jaas.config` Event Hubs Kafka uç noktanızı işaret etmek üzere değiştirin. Event Hubs hizmeti, aşağıdaki yapılandırmadaki son üç özellik ayarlanarak elde edilen güvenli (SASL) iletişim gerektirir: 

##### <a name="mirror-eventhubconfig"></a>yansıtma-eventhub. config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker 'ı Çalıştır

Yeni güncelleştirilmiş yapılandırma dosyalarını kullanarak kök Kafka dizininden Kafka MirrorMaker betiğini çalıştırın. Yapılandırma dosyalarını kök Kafka dizinine kopyalamaya ya da aşağıdaki komutta yollarını güncelleştirdiğinizden emin olun.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Olayların Kafka etkin olay hub 'ına ulaşmakta olduğunu doğrulamak için [Azure Portal](https://azure.microsoft.com/features/azure-portal/)giriş istatistiklerine bakın veya Olay Hub 'ına karşı bir tüketici çalıştırın.

MirrorMaker çalıştıran, kaynak Kafka kümesine gönderilen tüm olaylar hem Kafka kümesi hem de yansıtmalı Kafka özellikli Olay Hub 'ı tarafından alınır. MirrorMaker ve bir Event Hubs Kafka uç noktası kullanarak, var olan kümeyi değiştirmeden veya devam eden herhangi bir veri akışını kesintiye uğratmadan mevcut bir Kafka işlem hattını yönetilen Azure Event Hubs hizmetine geçirebilirsiniz.

## <a name="samples"></a>Örnekler
GitHub 'da aşağıdaki örneklere bakın:

- [GitHub 'da Bu öğretici için örnek kod](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Container Instance üzerinde çalışan Azure Event Hubs Kafka MirrorMaker](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Örnek projeyi kopyalama
> * Kafka kümesi ayarlama
> * Kafka MirrorMaker 'ı yapılandırma
> * Kafka MirrorMaker 'ı Çalıştır

Event Hubs ve Kafka için Event Hubs hakkında daha fazla bilgi edinmek için aşağıdaki konuya bakın:  

- [Event Hubs hakkında bilgi edinin](event-hubs-what-is-event-hubs.md)
- [Apache Kafka için Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka özellikli Event Hubs oluşturma](event-hubs-create-kafka-enabled.md)
- [Kafka uygulamalarınızdan Event Hubs'a akış yapma](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Apache Spark'ı Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink'i Kafka özellikli bir olay hub'ına bağlama](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect'i Kafka özellikli olay hub'ıyla tümleştirme](event-hubs-kafka-connect-tutorial.md)
- [Akka Streams’i Kafka özellikli olay hub'ına bağlama](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub'ımızdaki örnekleri inceleme](https://github.com/Azure/azure-event-hubs-for-kafka)

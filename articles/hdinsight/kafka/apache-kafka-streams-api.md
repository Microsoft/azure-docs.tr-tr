---
title: 'Öğretici: Apache Kafka Akışları API’sini kullanma - Azure HDInsight '
description: Öğretici-Apache Kafka Streams API 'sini HDInsight üzerinde Kafka ile nasıl kullanacağınızı öğrenin. Bu API, Kafka’daki konular arasında akış işleme gerçekleştirmenize olanak sağlar.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 10/08/2019
ms.openlocfilehash: f256adfd1fc970512cad5fb93ec235fc27a50373
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817738"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Öğretici: Azure HDInsight 'ta Apache Kafka akışları API 'SI kullanma

Apache Kafka Streams API 'sini kullanan bir uygulama oluşturmayı ve HDInsight üzerinde Kafka ile çalıştırmayı öğrenin.

Bu öğreticide kullanılan uygulama, akışa alma sözcük sayısıdır. Bir Kafka konusundan metin verilerini okur, tek tek sözcükleri ayıklar ve sonra sözcüğü ve sayıyı başka bir Kafka konusunda depolar.

Kafka Stream işleme genellikle Apache Spark veya Apache Storm kullanılarak yapılır. Kafka Version 1.1.0 (HDInsight 3,5 ve 3,6 ' de) Kafka Streams API 'sini kullanıma sunmuştur. Bu API, girdi ve çıktı konuları arasında veri akışlarını dönüştürmenize olanak sağlar. Bazı durumlarda bu, bir Spark veya Storm akış çözümü oluşturulmasına alternatif olabilir.

Kafka Akışları hakkında daha fazla bilgi için, Apache.org adresindeki [Akışların Tanıtımı](https://kafka.apache.org/10/documentation/streams/) belgelerine bakın.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Kodu anlama
> * Uygulama derleme ve dağıtma
> * Kafka konuları yapılandırma
> * Kodu çalıştırma

## <a name="prerequisites"></a>Önkoşullar

* HDInsight 3.6 kümesi üzerinde bir Kafka. HDInsight kümesinde Kafka oluşturma hakkında bilgi edinmek için bkz. [HDInsight 'ta Apache Kafka kullanmaya başlama](apache-kafka-get-started.md) belgesi.

* [Apache Kafka tüketicisi ve ÜRETICISI API](apache-kafka-producer-consumer-api.md) belgesindeki adımları uygulayın. Bu belgede yer alan adımlarda, bu öğreticide oluşturulan örnek uygulama ve konular kullanılmaktadır.

* [Java geliştirici seti (JDK) sürüm 8](https://aka.ms/azure-jdks) veya OpenJDK gibi bir eşdeğer.

* Apache [Maven](https://maven.apache.org/download.cgi) , Apache 'e göre düzgün şekilde [yüklendi](https://maven.apache.org/install.html) .  Maven, Java projeleri için bir proje derleme sistemidir.

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Kodu anlama

Örnek uygulama, [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) konumunda `Streaming` alt dizininde bulunur. Uygulama iki dosyadan oluşur:

* `pom.xml`: Bu dosya, proje bağımlılıklarını, Java sürümünü ve paketleme yöntemlerini tanımlar.
* `Stream.java`: Bu dosya, akış mantığını uygular.

### <a name="pomxml"></a>Pom.xml

`pom.xml` dosyasında aşağıdaki önemli şeyler anlaşılır:

* Bağımlılıklar: Bu proje, `kafka-clients` paketi tarafından sağlanan Kafka Akışları API’sini kullanır. Aşağıdaki XML kodu, bu bağımlılığı tanımlar:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    `${kafka.version}` girişi, `pom.xml` dosyasının `<properties>..</properties>` bölümünde bildirilir ve HDInsight kümesinin Kafka sürümüne yapılandırılır.

* Eklentiler: Maven eklentileri çeşitli özellikler sağlar. Bu projede aşağıdaki eklentiler kullanılır:

    * `maven-compiler-plugin`: Proje tarafından kullanılan Java sürümünü 8 olarak ayarlamak için kullanılır. HDInsight 3.6 için Java 8 gerekir.
    * `maven-shade-plugin`: Bu uygulamayı ve tüm bağımlılıkları içeren bir uber jar oluşturmak için kullanılır. Ayrıca, ana sınıfı belirtmek zorunda kalmadan jar dosyasını doğrudan çalıştırabilmeniz için uygulamanın giriş noktasını ayarlamak için de kullanılır.

### <a name="streamjava"></a>Stream.java

[Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) dosyası, sözcük sayısı uygulamasını çalıştırmak için Akışlar API’sini kullanır. `test` adlı bir Kafka konusundan verileri okur ve `wordcounts` adlı bir konuya sözcük sayılarını yazar.

Aşağıdaki kod, sözcük sayısı uygulamasını tanımlar:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```

## <a name="build-and-deploy-the-example"></a>Örnek derleme ve dağıtma

Projeyi derlemek ve HDInsight kümesi üzerinde Kafka’nıza dağıtmak için aşağıdaki adımları kullanın:

1. Geçerli dizininizi `hdinsight-kafka-java-get-started-master\Streaming` dizininin konumuna ayarlayın ve ardından aşağıdaki komutu kullanarak bir jar paketi oluşturun:

    ```cmd
    mvn clean package
    ```

    Bu komut, `target/kafka-streaming-1.0-SNAPSHOT.jar` konumunda paketi oluşturur.

2. `sshuser` değerini, kümenizin SSH kullanıcısı ile, `clustername` değerini kümenizin adıyla değiştirin. `kafka-streaming-1.0-SNAPSHOT.jar` dosyasını HDInsight kümenize kopyalamak için aşağıdaki komutu kullanın. İstendiğinde, SSH kullanıcı hesabının parolasını girin.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Apache Kafka konuları oluşturma

1. `sshuser` değerini, kümenizin SSH kullanıcısı ile, `CLUSTERNAME` değerini kümenizin adıyla değiştirin. Aşağıdaki komutu girerek kümeye bir SSH bağlantısı açın. İstendiğinde, SSH kullanıcı hesabının parolasını girin.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Komut satırı JSON işlemcisi olan [JQ](https://stedolan.github.io/jq/)'yi yükler. Açık SSH bağlantısından `jq`yüklemek için aşağıdaki komutu girin:

    ```bash
    sudo apt -y install jq
    ```

3. Parola değişkenini ayarlayın. `PASSWORD`, küme oturum açma parolasıyla değiştirin, ardından şu komutu girin:

    ```bash
    export password='PASSWORD'
    ```

4. Doğru şekilde oluşturulmuş küme adını ayıklayın. Kümenin nasıl oluşturulduğuna bağlı olarak, küme adının gerçek büyük küçük harfleri beklediğinizden farklı olabilir. Bu komut gerçek büyük küçük harf elde eder ve bir değişkende depolar. Aşağıdaki komutu girin:
    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Bu işlemi küme dışından yapıyorsanız, küme adını depolamak için farklı bir yordam vardır. Azure portal küme adını küçük harf olarak alın. Ardından, aşağıdaki komutta `<clustername>` için küme adını değiştirin ve çalıştırın: `export clusterName='<clustername>'`.  

5. Kafka Broker konakları ve Apache Zookeeper Konakları almak için aşağıdaki komutları kullanın. İstendiğinde, küme oturum açma (yönetici) hesabı için parolayı girin. İki defa parolanız istenir.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

> [!Note]  
> Bu komutlar, ambarı erişimi gerektirir. Kümeniz bir NSG 'nin arkasındaysa, bu komutları ambarı erişebilen bir makineden çalıştırın. 

6. Akış işlemi tarafından kullanılan konular oluşturmak için aşağıdaki komutları kullanın:

    > [!NOTE]  
    > `test` konusunun zaten mevcut olduğuna dair bir hata alabilirsiniz. Üretici ve Tüketici API’si öğreticisinde oluşturulmuş olabileceğinden bu sorun değildir.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Konular aşağıdaki amaçlar için kullanılır:

   * `test`: Bu konu, kayıtların alındığı konudur. Akış uygulaması buradan okur.
   * `wordcounts`: Bu konu, akış uygulamasının çıktısını depoladığı konudur.
   * `RekeyedIntermediateTopic`: Bu konu, `countByKey` işleci tarafından sayı güncelleştirilirken verileri yeniden bölümlemek için kullanılır.
   * `wordcount-example-Counts-changelog`: Bu konu, `countByKey` işlemi tarafından kullanılan durum deposudur

    HDInsight üzerinde Kafka otomatik olarak konular oluşturmak için de yapılandırılabilir. Daha fazla bilgi için [Otomatik konu oluşturmayı yapılandırma](apache-kafka-auto-create-topics.md) belgesine bakın.

## <a name="run-the-code"></a>Kodu çalıştırma

1. Arka plan işlemi olarak akış uygulamasını başlatmak için aşağıdaki komutu kullanın:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    Apache Log4J hakkında bir uyarı alabilirsiniz. Bunu yoksayabilirsiniz.

2. `test` konusuna kayıtlar göndermek için aşağıdaki komutu kullanarak üretici uygulamasını başlatın:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Üretici tamamlandıktan sonra `wordcounts` konusunda depolanan bilgileri görüntülemek için aşağıdaki komutu kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    `--property` parametreleri, konsol tüketicisine sayı (değer) ile birlikte anahtarı (sözcük) yazdırmasını bildirir. Bu parametre, Kafka’dan bu değerler okunurken kullanılacak seri kaldırıcıyı da yapılandırır.

    Çıktı aşağıdaki metne benzer:

        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    `--from-beginning` parametresi, tüketiciyi konuda depolanan kayıtların başında başlayacak şekilde yapılandırır. Her sözcükle karşılaşıldığında sayı artar, bu nedenle konu, artan sayıyla birlikte her sözcük için birden fazla giriş içerir.

4. Üreticiden çıkış yapmak için __Ctrl + C__ tuşlarını kullanın. Uygulamadan ve tüketiciden çıkış yapmak için __Ctrl + C__ tuşlarını kullanmaya devam edin.

5. Akış işlemi tarafından kullanılan konuları silmek için aşağıdaki komutları kullanın:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici ile oluşturulan kaynakları temizlemek için kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, ilişkili HDInsight kümesini ve kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

Azure portalını kullanarak kaynak grubunu kaldırmak için:

1. Azure portalında sol taraftaki menüyü genişleterek hizmet menüsünü açın ve sonra __Kaynak Grupları__'nı seçerek kaynak gruplarınızın listesini görüntüleyin.
2. Silinecek kaynak grubunu bulun ve sonra listenin sağ tarafındaki __Daha fazla__ düğmesine (...) sağ tıklayın.
3. __Kaynak grubunu sil__'i seçip onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, HDInsight üzerinde Kafka ile Apache Kafka Streams API 'sini kullanmayı öğrendiniz. Kafka ile çalışma hakkında daha fazla bilgi edinmek için aşağıdakileri kullanın.

> [!div class="nextstepaction"]
> [Apache Kafka günlüklerini çözümle](apache-kafka-log-analytics-operations-management.md)

---
title: "Öğretici: Apache Kafka üreticisi & tüketici API 'Leri-Azure HDInsight"
description: HDInsight’ta Apache Kafka Üretici ve Tüketici API’lerini kullanmayı öğrenin. Bu öğreticide, bu API’leri HDInsight üzerinde Kafka ile kullanmayı öğreneceksiniz.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: e5a635a8837aadaf423c6f3a0925dbac4080e60f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945173"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Öğretici: Apache Kafka Üretici ve Tüketici API’lerini kullanma

HDInsight’ta Apache Kafka Üretici ve Tüketici API’lerini kullanmayı öğrenin.

Kafka Üretici API’si, uygulamaların Kafka kümesine veri akışları göndermesine olanak tanır. Kafka Tüketici API’si, uygulamaların kümeden veri akışları okumasına olanak tanır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ön koşullar
> * Kodu anlama
> * Uygulama derleme ve dağıtma
> * Uygulamayı küme üzerinde çalıştırma

API’ler hakkında daha fazla bilgi için [Üretici API’si](https://kafka.apache.org/documentation/#producerapi) ve [Tüketici API’si](https://kafka.apache.org/documentation/#consumerapi) hakkında Apache belgelerine bakın.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight kümesinde Apache Kafka. Küme oluşturma hakkında bilgi edinmek için bkz. [HDInsight 'ta Apache Kafka kullanmaya başlama](apache-kafka-get-started.md).
* [Java geliştirici seti (JDK) sürüm 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) veya OpenJDK gibi bir eşdeğer.
* Apache [Maven](https://maven.apache.org/download.cgi) , Apache 'e göre düzgün şekilde [yüklendi](https://maven.apache.org/install.html) .  Maven, Java projeleri için bir proje derleme sistemidir.
* PuTTY gibi bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Kodu anlama

Örnek uygulama, dizininde konumunda bulunur [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) `Producer-Consumer` . **Kurumsal güvenlik paketi (ESP)** etkin Kafka kümesi kullanıyorsanız, alt dizinde bulunan uygulama sürümünü kullanmanız gerekir `DomainJoined-Producer-Consumer` .

Uygulama öncelikli olarak dört dosyadan oluşur:
* `pom.xml`: Bu dosya, proje bağımlılıklarını, Java sürümünü ve paketleme yöntemlerini tanımlar.
* `Producer.java`: Bu dosya, üretici API’sini kullanarak Kafka’ya rastgele tümceler gönderir.
* `Consumer.java`: Bu dosya, tüketici API’sini kullanarak Kafka’dan verileri okur ve STDOUT’a yayar.
* `AdminClientWrapper.java`: Bu dosya, Kafka konularını oluşturmak, anlatmak ve silmek için yönetim API 'sini kullanır.
* `Run.java`: Üretici ve tüketici kodunu çalıştırmak için kullanılan komut satırı arabirimi.

### <a name="pomxml"></a>Pom.xml

`pom.xml` dosyasında aşağıdaki önemli şeyler anlaşılır:

* Bağımlılıklar: Bu proje, `kafka-clients` paketi tarafından sağlanan Kafka üretici ve tüketici API’lerini kullanır. Aşağıdaki XML kodu, bu bağımlılığı tanımlar:

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

    * `maven-compiler-plugin`: Proje tarafından kullanılan Java sürümünü 8 olarak ayarlamak için kullanılır. HDInsight 3.6 tarafından kullanılan Java sürümüdür.
    * `maven-shade-plugin`: Bu uygulamayı ve tüm bağımlılıkları içeren bir uber jar oluşturmak için kullanılır. Ana sınıfı belirtmek zorunda olmadan doğrudan Jar dosyasını çalıştırabilmeniz için uygulamanın giriş noktasını ayarlamak için de kullanılır.

### <a name="producerjava"></a>Producer.java

Üretici, Kafka aracı konakları (çalışan düğümleri) ile iletişim kurar ve verileri bir Kafka konusuna gönderir. Aşağıdaki kod parçacığı, [GitHub deposundan](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) [Producer. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) dosyasından bulunur ve üretici özelliklerinin nasıl ayarlanacağını gösterir. Kurumsal güvenlik etkin kümeler için ek bir özellik eklenmelidir "Properties. setProperty (CommonClientConfigs.SECURITY_PROTOCOL_CONFIG," SASL_PLAINTEXT ");"

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Tüketici, Kafka aracı konakları (çalışan düğümleri) ile iletişim kurar ve bir döngüdeki kayıtları okur. [Tüketici. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) dosyasındaki aşağıdaki kod parçacığı, tüketici özelliklerini ayarlar. Kurumsal güvenlik etkin kümeler için ek bir özellik eklenmelidir "Properties. setProperty (CommonClientConfigs.SECURITY_PROTOCOL_CONFIG," SASL_PLAINTEXT ");"

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

Bu kodda tüketici, konu başlangıcından okumak üzere yapılandırılmıştır (`auto.offset.reset` değeri `earliest` olarak ayarlanır.)

### <a name="runjava"></a>Run.java

[Run. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) dosyası, üretici veya tüketici kodu çalıştıran bir komut satırı arabirimi sağlar. Kafka aracı konağı bilgilerini bir parametre olarak sağlamanız gerekir. İsteğe bağlı olarak, tüketici işlemi tarafından kullanılan bir grup KIMLIĞI değeri ekleyebilirsiniz. Aynı grup KIMLIĞINI kullanarak birden çok tüketici örneği oluşturursanız, bu, konudan okuma dengeyi yükler.

## <a name="build-and-deploy-the-example"></a>Örnek derleme ve dağıtma

### <a name="use-pre-built-jar-files"></a>Önceden oluşturulmuş JAR dosyalarını kullan

[Kafka Başlarken Azure örneğinden](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars)jar dosyaları dışındaki 'ı indirin. Kümeniz **Kurumsal güvenlik paketi (ESP)** etkinse, Kafka-Producer-Consumer-ESP. jar kullanın. Jar dosyaları dışındaki 'ı kümenize kopyalamak için aşağıdaki komutu kullanın.

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>Koddan JAR dosyalarını oluşturma

Bu adımı atlamak isterseniz, önceden oluşturulmuş jar dosyaları dışındaki alt dizininden indirilebilir `Prebuilt-Jars` . Kafka-Producer-Consumer. jar dosyasını indirin. Kümeniz **Kurumsal güvenlik paketi (ESP)** etkinse, Kafka-Producer-Consumer-ESP. jar kullanın. Jar dosyasını HDInsight kümenize kopyalamak için adım 3 ' ü yürütün.

1. İçindeki örnekleri indirip ayıklayın [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) .

2. Geçerli dizininizi dizinin konumuna ayarlayın `hdinsight-kafka-java-get-started\Producer-Consumer` . **Kurumsal güvenlik paketi (ESP)** etkin Kafka kümesi kullanıyorsanız, konumu alt dizin olarak ayarlamanız gerekir `DomainJoined-Producer-Consumer` . Uygulamayı derlemek için aşağıdaki komutu kullanın:

    ```cmd
    mvn clean package
    ```

    Bu komut, `kafka-producer-consumer-1.0-SNAPSHOT.jar` adlı dosyayı içeren `target` adlı bir dizin oluşturur. ESP kümeleri için dosya, `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar`

3. `sshuser` değerini, kümenizin SSH kullanıcısı ile, `CLUSTERNAME` değerini kümenizin adıyla değiştirin. Dosyayı HDInsight kümenize kopyalamak için aşağıdaki komutu girin `kafka-producer-consumer-1.0-SNAPSHOT.jar` . İstendiğinde, SSH kullanıcısının parolasını girin.

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a> Örneği çalıştırma

1. `sshuser` değerini, kümenizin SSH kullanıcısı ile, `CLUSTERNAME` değerini kümenizin adıyla değiştirin. Aşağıdaki komutu girerek kümeye bir SSH bağlantısı açın. İstendiğinde, SSH kullanıcı hesabının parolasını girin.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kafka Broker Konakları almak için, `<clustername>` ve değerlerini `<password>` aşağıdaki komutta yerine yürütün ve yürütün. Azure portal gösterildiği gibi büyük küçük harf kullanın `<clustername>` . `<password>`Küme oturum açma parolasıyla değiştirin, ardından şunu yürütün:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Bu komut, ambarı erişimi gerektirir. Kümeniz bir NSG 'nin arkasındaysa, bu komutu ambarı erişebilen bir makineden çalıştırın.

1. `myTest`Aşağıdaki komutu girerek Kafka konusu oluşturun:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Üreticiyi çalıştırmak ve konuya veri yazmak için aşağıdaki komutu kullanın:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Üretici tamamlandıktan sonra, konu başlığından okumak için aşağıdaki komutu kullanın:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    Okunan kayıtlar, kayıt sayısıyla birlikte gösterilir.

1. Tüketiciden çıkış yapmak için __Ctrl + C__ tuşlarını kullanın.

### <a name="multiple-consumers"></a>Birden çok tüketici

Kafka tüketicileri kayıtları okurken bir tüketici grubu kullanır. Birden çok tüketiciyle aynı grubun kullanılması, konu başlığından yük dengeli okuma yapılmasına neden olur. Gruptaki her bir tüketici, kayıtların bir kısmını alır.

Tüketici uygulaması, grup kimliği olarak kullanılan bir parametre kabul eder Örneğin, aşağıdaki komut bir `myGroup` grup kimliği kullanarak tüketiciyi başlatır:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Tüketiciden çıkış yapmak için __Ctrl + C__ tuşlarını kullanın.

Bu işlemi uygulamada görmek için aşağıdaki komutu kullanın:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Bu komut, terminali iki sütuna bölmek için `tmux` kullanır. Her sütunda aynı grup kimliği değerine sahip bir tüketici başlatılır. Tüketici okumayı tamamladıktan sonra her birinin yalnızca kayıtların bir bölümünü okuduğuna dikkat edin. Çıkmak için __CTRL + C__ tuşlarına iki kez kullanın `tmux` .

Aynı gruptaki istemcilerin tüketimi, konu başlığının bölümleri aracılığıyla işlenir. Bu kod örneğinde, daha önce oluşturulan `test` konusunda sekiz bölüm vardır. Sekiz tüketici başlatırsanız, her tüketici konunun tek bir bölümünden kayıtları okur.

> [!IMPORTANT]  
> Bir tüketici grubunda bölümden daha fazla tüketici örneği olamaz. Bu örnekte, konu başlığındaki bölüm sayısı sekiz olduğu için bir tüketici grubu en fazla bu sayıda tüketici içerebilir. Ya da her biri en fazla sekiz tüketici içeren birden fazla tüketici grubunuz olabilir.

Kafka içinde depolanan kayıtlar bir bölüm içinde alındıkları sırada depolanır. *Bir bölüm* içindeki kayıtlar için sıralı teslim sağlamak üzere, tüketici örneklerinin bölüm sayısıyla eşleştiği bir tüketici grubu oluşturun. *Konu başlığı içindeki* kayıtların sıralı teslim edilmesini sağlayabilmek için, yalnızca bir tüketici örneği içeren bir tüketici grubu oluşturun.

## <a name="common-issues-faced"></a>Karşılaştığı yaygın sorunlar

1. **Konu oluşturma başarısız** Kümeniz kurumsal güvenlik paketi etkinse, [üretici ve tüketici için önceden oluşturulmuş jar dosyalarını](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar)kullanın. ESP jar, [ `DomainJoined-Producer-Consumer` alt dizindeki](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)koddan oluşturulabilir. Üretici ve tüketici özellikleri, `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG` ESP etkin kümeler için ek bir özelliğe sahiptir.

2. **ESP etkin kümelerde hata**: oluşturma ve kullanma işlemleri başarısız olursa ve bir ESP etkin küme kullanıyorsanız, kullanıcının `kafka` Tüm Ranger ilkelerinde mevcut olup olmadığını denetleyin. Mevcut değilse, tüm Ranger ilkelerine ekleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici ile oluşturulan kaynakları temizlemek için kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, ilişkili HDInsight kümesini ve kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

Azure portalını kullanarak kaynak grubunu kaldırmak için:

1. Azure portalında sol taraftaki menüyü genişleterek hizmet menüsünü açın ve sonra __Kaynak Grupları__'nı seçerek kaynak gruplarınızın listesini görüntüleyin.
2. Silinecek kaynak grubunu bulun ve sonra listenin sağ tarafındaki __Daha fazla__ düğmesine (...) sağ tıklayın.
3. __Kaynak grubunu sil__'i seçip onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, HDInsight üzerinde Kafka ile Apache Kafka üreticisi ve tüketici API 'sini kullanmayı öğrendiniz. Kafka ile çalışma hakkında daha fazla bilgi için aşağıdakileri kullanın:

* [Kafka REST proxy kullan](rest-proxy.md)
* [Apache Kafka günlüklerini çözümleme](apache-kafka-log-analytics-operations-management.md)
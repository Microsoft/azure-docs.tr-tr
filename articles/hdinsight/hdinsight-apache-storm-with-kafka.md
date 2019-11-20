---
title: 'Öğretici: Apache Kafka ile Apache Storm-Azure HDInsight'
description: HDInsight üzerinde Apache Storm ve Apache Kafka kullanarak akış işlem hattı oluşturmayı öğrenin. Bu öğreticide, Kafka'dan veri akışı yapmak için KafkaBolt ve KafkaSpout bileşenlerini kullanırsınız.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: eac9bee6992520492b846e3b579d8a05c327e749
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494358"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>Öğretici: HDInsight üzerinde Apache Kafka ile Apache Storm kullanma

Bu öğreticide, HDInsight üzerinde [Apache Kafka](https://kafka.apache.org/) ile verileri okumak ve yazmak için [Apache Storm](https://storm.apache.org/) topolojisinin nasıl kullanılacağı gösterilmektedir. Bu öğretici Ayrıca verileri, fırtınası kümesindeki [Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) , uyumlu depolamada nasıl kalıcı hale getirebileceğinizi gösterir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Storm ve Kafka
> * Kodu anlama
> * Kafka ve Storm kümeleri oluşturma
> * Topoloji oluşturma
> * Topolojiyi yapılandırma
> * Kafka konusu oluşturma
> * Topolojileri başlatma
> * Topolojileri durdurma
> * Kaynakları temizleme

## <a name="prerequisites"></a>Önkoşullar

* Kafka konuları oluşturmayı bilme. Daha fazla bilgi için [HDInsight üzerinde Kafka hızlı başlangıcı](./kafka/apache-kafka-get-started.md) belgesine bakın.

* Storm çözümleri (topolojileri) oluşturmayı ve dağıtmayı bilme. Özellikle, [Apache Storm Flox](https://storm.apache.org/releases/current/flux.html) Framework kullanan topolojiler. Daha fazla bilgi için bkz. [Java 'da Apache Storm topolojisi oluşturma](./storm/apache-storm-develop-java-topology.md) .

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) veya üstü. HDInsight 3.5 veya üstü için Java 8 gerekir.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH istemcisi (`ssh` ve `scp` komutları gerekir) - Bilgi için bkz. [HDInsight ile SSH'yi kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

Dağıtım iş istasyonunuza Java ve JDK yüklerken aşağıdaki ortam değişkenleri ayarlanabilir. Ancak, bunların mevcut olup olmadığını ve sisteminiz için doğru değerleri içerip içermediğini denetlemeniz gerekir.

* `JAVA_HOME` - JDK’nın yüklendiği dizine işaret etmelidir.
* `PATH` - aşağıdaki yolları içermelidir:
  
    * `JAVA_HOME` (veya eşdeğer yol).
    * `JAVA_HOME\bin` (veya eşdeğer yol).
    * Maven'ın yüklendiği dizin.

> [!IMPORTANT]  
> Bu belgede yer alan adımlar hem HDInsight üzerinde Storm hem de HDInsight kümesi üzerinde Kafka içeren bir Azure kaynak grubu gerektirir. Bu kümelerin her ikisi de Storm kümesinin Kafka kümesiyle doğrudan iletişim kurmasına olanak tanıyan bir Azure Sanal Ağı içinde bulunur.
> 
> Size kolaylık sağlamak için bu belgede, tüm gerekli Azure kaynaklarını oluşturabilecek bir şablonun bağlantıları sağlanır. 
>
> Bir sanal ağda HDInsight kullanma hakkında daha fazla bilgi için bkz. [HDInsight için sanal ağ planı](hdinsight-plan-virtual-network-deployment.md) belgesi.

## <a name="storm-and-kafka"></a>Storm ve Kafka

Apache Storm, Apache Kafka çalışmak için birkaç bileşen sağlar. Bu öğreticide aşağıdaki bileşenler kullanılır:

* `org.apache.storm.kafka.KafkaSpout`: Bu bileşen Kafka'dan verileri okur. Bu bileşen, aşağıdaki bileşenlere dayanır:

    * `org.apache.storm.kafka.SpoutConfig`: Spout bileşeni için yapılandırma sağlar.

    * `org.apache.storm.spout.SchemeAsMultiScheme` ve `org.apache.storm.kafka.StringScheme`: Kafka'dan alınan verilerin Storm tanımlama grubuna nasıl dönüştürüldüğü.

* `org.apache.storm.kafka.bolt.KafkaBolt`: Bu bileşen Kafka'ya verileri yazar. Bu bileşen, aşağıdaki bileşenlere dayanır:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: Yazılan konuyu açıklar.

    * `org.apache.kafka.common.serialization.StringSerializer`: Verileri bir dize değeri olarak seri hale getirmek için bolt yapılandırır.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: Storm topolojisi içinde kullanılan tanımlama grubu veri yapısını Kafka'da depolanan alanlarla eşler.

Bu bileşenler `org.apache.storm : storm-kafka` paketinde sağlanır. Storm sürümüyle eşleşen paket sürümünü kullanın. HDInsight 3.6 için, Storm sürümü 1.1.0'dır.
Ayrıca, ek Kafka bileşenlerini içeren `org.apache.kafka : kafka_2.10` paketi de gereklidir. Kafka sürümüyle eşleşen paket sürümünü kullanın. HDInsight 3,6 için Kafka sürümü 1.1.1.

Aşağıdaki XML [Apache Maven](https://maven.apache.org/) projesi için `pom.xml` bağımlılık bildirimidir:

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>1.1.1</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>Kodu anlama

Bu belgede kullanılan kod [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) adresinde sağlanır.

Bu öğreticide iki topoloji sağlanmaktadır:

* Kafka-yazıcı: Rastgele tümceler oluşturur ve bunları Kafka'da depolar.

* Kafka-okuyucu: Kafka'dan verileri okur ve bunları Storm kümesi için HDFS uyumlu bir dosya deposunda depolar.

    > [!WARNING]  
    > Storm'un HDInsight tarafından kullanılan HDFS uyumlu depolamada çalışmasını sağlamak için, bir betik eylemi gerekir. Betik, Storm için çeşitli jar dosyalarını `extlib` yoluna yükler. Bu öğreticideki şablon, küme oluşturma sırasında betiği otomatik olarak kullanır.
    >
    > Storm kümesini oluşturmak için bu belgedeki şablonu kullanmazsanız, betik eylemini kümenize el ile uygulamanız gerekir.
    >
    > Betik eylemi [https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh) konumunda bulunur ve bu, fırtınası kümesinin gözetmen ve Nimbus düğümlerine uygulanır. Betik eylemlerini kullanma hakkında daha fazla bilgi için, [Betik eylemlerini kullanarak HDInsight'ı özelleştirme](hdinsight-hadoop-customize-cluster-linux.md) belgesine bakın.

Topolojiler [Flux](https://storm.apache.org/releases/current/flux.html) kullanılarak tanımlanır. Flux Storm 0.10.x sürümünde kullanıma sunulmuştur ve topoloji yapılandırmasını koddan ayırmanıza olanak tanır. Flux çerçevesini kullanan Topolojiler için, topoloji YAML dosyasında tanımlanır. YAML dosyası topolojinin bir parçası olarak eklenebilir. Ayrıca, topolojiyi gönderirken kullandığınız tek başına bir dosya da olabilir. Flux, bu örnekte kullanılan çalışma zamanında değişken değiştirme özelliğini de destekler.

Aşağıdaki parametreler, bu topolojiler için çalışma zamanında ayarlanır:

* `${kafka.topic}`: Topolojilerin okuduğu/yazdığı Kafka konusunu adı.

* `${kafka.broker.hosts}`: Kafka aracılarının üzerinde çalıştırıldığı konaklar. Aracı bilgisi, KafkaBolt tarafından Kafka'ya yazarken kullanılır.

* `${kafka.zookeeper.hosts}`: Kafka kümesinde Zookeeper'ın üzerinde çalıştırıldığı konaklar.

* `${hdfs.url}`: HDFSBolt bileşeni için dosya sistemi URL'si. Verilerin bir Azure depolama hesabına mı yoksa Azure Data Lake Storage mi yazıldığını belirtir.

* `${hdfs.write.dir}`: Verilerin yazıldığı dizin.

Flux topolojileriyle ilgili daha fazla bilgi için bkz. [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html).

### <a name="kafka-writer"></a>Kafka-yazıcı

Kafka-yazıcı topolojisinde, Kafka bolt bileşeni iki dize değerini parametre olarak alır. Bu parametreler bolt'un __anahtar__ ve __ileti__ değerleri olarak Kafka'ya hangi tanımlama grubu alanlarını gönderdiğini gösterir. Anahtar, Kafka'da verileri bölümlemek için kullanılır. İleti, depolanmakta olan verilerdir.

Bu örnekte, `com.microsoft.example.SentenceSpout` bileşeni iki alan (`key` ve `message`) içeren bir tanımlama grubu gösterir. Kafka bolt bu alanları ayıklar ve içlerindeki verileri Kafka'ya gönderir.

Alanların `key` ve `message`adlarını kullanması zorunlu değildir. Bu projede bu adların kullanılmasının nedeni eşlemenin daha kolay anlaşılmasını sağlamaktır.

Aşağıdaki YAML, Kafka-yazıcı bileşeninin tanımıdır:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-okuyucu

Kafka-okuyucu topolojisinde, spout bileşeni Kafka'dan verileri dize değerleri olarak okur. Ardından veriler günlük bileşeni tarafından Storm günlüğüne ve HDFS bolt bileşeni tarafından Storm kümesi için HDFS uyumlu dosya sistemine yazılır.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Özellik değişimleri

Proje, topolojilerin kullandığı parametreleri geçirmek için kullanılan `dev.properties` adlı bir dosya içerir. Bu dosya şu özellikleri tanımlar:

| dev.properties dosyası | Açıklama |
| --- | --- |
| `kafka.zookeeper.hosts` | [Apache ZooKeeper](https://zookeeper.apache.org/) , Kafka kümesi için barındırır. |
| `kafka.broker.hosts` | Kafka aracısı konakları (çalışan düğümleri). |
| `kafka.topic` | Topolojileri kullanan Kafka konusu. |
| `hdfs.write.dir` | Kafka-okuyucu topolojisinin yazdığı dizin. |
| `hdfs.url` | Storm kümesi tarafından kullanılan dosya sistemi. Azure Depolama hesapları için `wasb://` değerini kullanın. Azure Data Lake Storage 2. için `abfs://`değerini kullanın. Azure Data Lake Storage 1. için `adl://`değerini kullanın. |

## <a name="create-the-clusters"></a>Kümeleri oluşturma

HDInsight üzerinde Apache Kafka, genel internet üzerinden Kafka aracılarına erişim sağlamaz. Kafka kullanan her özellik aynı Azure sanal ağı içinde olmalıdır. Bu öğreticide hem Kafka hem de Storm kümeleri aynı Azure sanal ağı içinde yer alır. 

Aşağıdaki diyagramda Storm ile Kafka arasındaki iletişimin nasıl aktığı gösterilmektedir:

![Bir Azure sanal ağında Storm ve Kafka kümeleri diyagramı](./media/hdinsight-apache-storm-with-kafka/apache-storm-kafka-vnet.png)

> [!NOTE]  
> Kümedeki SSH ve [Apache ambarı](https://ambari.apache.org/) gibi diğer hizmetlere internet üzerinden erişilebilir. HDInsight üzerinde kullanılabilir olan genel bağlantı noktaları hakkında daha fazla bilgi için bkz. [HDInsight Tarafından Kullanılan Bağlantı Noktaları ve URI’ler](hdinsight-hadoop-port-settings-for-services.md).

Bir Azure Sanal Ağı oluşturmak ve sonra bunun içinde Kafka ve Storm kümeleri oluşturmak için aşağıdaki adımları kullanın:

1. Aşağıdaki düğmeyi kullanarak Azure'da oturum açın ve şablonu Azure portalında açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager şablonu **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json** sayfasında bulunur. Aşağıdaki kaynakları oluşturur:

    * Azure kaynak grubu
    * Azure Sanal Ağ
    * Azure Depolama hesabınızın
    * HDInsight sürüm 3.6 üzerinde Kafka (üç çalışan düğümü)
    * HDInsight sürüm 3.6 üzerinde Storm (üç çalışan düğümü)

   > [!WARNING]  
   > HDInsight üzerinde Kafka'yı kullanabilmeniz için kümenizin en az üç çalışan düğümü içermesi gerekir. Bu şablon, üç çalışan düğümü içeren bir Kafka kümesi oluşturur.

2. **Özel dağıtım** bölümündeki girdileri doldurmak için aşağıdaki yönergeleri kullanın:

   1. **Özelleştirilmiş şablon** bölümündeki girişleri doldurmak için aşağıdaki bilgileri kullanın:

      | Ayar | Değer |
      | --- | --- |
      | Abonelik | Azure aboneliğiniz |
      | Kaynak grubu | Kaynakları içeren kaynak grubu. |
      | Konum | İçinde kaynakların oluşturulduğu Azure bölgesi. |
      | Kafka Kümesi Adı | Kafka kümesinin adı. |
      | Storm Kümesi Adı | Storm kümesinin adı. |
      | Küme Oturum Açma Kullanıcı Adı | Kümeler için yönetici kullanıcı adı. |
      | Küme Oturum Açma Parolası | Kümeler için yönetici kullanıcı parolası. |
      | SSH Kullanıcı Adı | Kümeler için oluşturulacak SSH kullanıcısı. |
      | SSH Parolası | SSH kullanıcısı için parola. |
   
      ![Şablon parametrelerinin resmi](./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png)

3. **Hüküm ve Koşullar**’ı okuyun ve ardından **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**’u seçin.

4. Son olarak, **Panoya sabitle**’yi işaretleyin ve **Satın Al**’ı seçin.

> [!NOTE]  
> Kümelerin oluşturulması 20 dakikaya kadar sürebilir.

## <a name="build-the-topology"></a>Topoloji oluşturma

1. Geliştirme ortamında [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) adresinden projeyi indirin, komut satırı açın ve dizinleri projeyi indirdiğiniz konumla değiştirin.

2. **hdinsight-storm-java-kafka** dizininde, aşağıdaki komutu kullanarak projeyi derleyin ve dağıtım için paket oluşturun:

   ```bash
   mvn clean package
   ```

    Paket işlemi, `KafkaTopology-1.0-SNAPSHOT.jar` dizininde `target` adlı bir dosya oluşturur.

3. Paketi HDInsight kümesindeki Storm'a kopyalamak için aşağıdaki komutları kullanın. `sshuser` değerini kümenin SSH kullanıcı adıyla değiştirin. `stormclustername` değerini __Storm__ kümesinin adıyla değiştirin.

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    İstendiğinde, kümeleri oluştururken kullandığınız parolayı girin.

## <a name="configure-the-topology"></a>Topolojiyi yapılandırma

1. Aşağıdaki yöntemlerden birini kullanarak HDInsight kümesinde **Kafka** için Kafka aracı konaklarını bulun:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > Aşağıdaki Bash örneğinde `$CLUSTERNAME` öğesinin __Kafka__ küme adını içerdiği varsayılır. Ayrıca, [jq](https://stedolan.github.io/jq/) sürüm 1.5 veya üstünün yüklü olduğu da varsayılır. İstendiğinde, küme oturum açma hesabı için parolayı girin.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    Döndürülen değer aşağıdaki metne benzer:

    ```output
    wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
     ```

    > [!IMPORTANT]  
    > Kümeniz için ikiden fazla aracı konağı olabilir, ama istemcilere tüm konakların listesini sağlamanız gerekmez. Bir veya iki tanesi yeterlidir.

2. Aşağıdaki yöntemlerden birini kullanarak HDInsight kümesinde __Kafka__ için Zookeeper konaklarını bulun:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > Aşağıdaki Bash örneğinde `$CLUSTERNAME` öğesinin __Kafka__ kümesinin adını içerdiği varsayılır. Ayrıca, [jq](https://stedolan.github.io/jq/)'nun yüklü olduğu da varsayılır. İstendiğinde, küme oturum açma hesabı için parolayı girin.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    Döndürülen değer aşağıdaki metne benzer:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]  
    > İkiden fazla Zookeeper düğümü olsa da, istemcilere tüm konakların listesini sağlamanız gerekmez. Bir veya iki tanesi yeterlidir.

    Bu değeri kaydedin çünkü daha sonra kullanılacaktır.

3. Proje kökündeki `dev.properties` dosyasını düzenleyin. Bu dosyadaki ilgili satırlara __Kafka__ kümesi için Aracı ve Zookeeper konaklarının bilgilerini ekleyin. Aşağıdaki örnek, önceki adımlardan alınan örnek değerler kullanılarak yapılandırılır:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

    > [!IMPORTANT]  
    > `hdfs.url` girdisi, Azure Depolama hesabı kullanan bir küme için yapılandırılır. Bu topolojiyi Data Lake Storage kullanan bir fırtınası kümesiyle birlikte kullanmak için, bu değeri `wasb` ' dan `adl`' e değiştirin.

4. `dev.properties` dosyasını kaydedin ve ardından aşağıdaki komutu kullanarak bu dosyayı **Storm** kümesine yükleyin:

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    **USERNAME** değerini kümenin SSH kullanıcı adıyla değiştirin. **BASENAME** değerini kümeyi oluştururken kullandığınız temel adıyla değiştirin.

## <a name="create-the-kafka-topic"></a>Kafka konusu oluşturma

Kafka, verileri bir _konu_ içinde depolar. Storm topolojilerini başlatmadan önce konuyu oluşturmanız gerekir. Topolojiyi oluşturmak için aşağıdaki adımları kullanın:

1. Aşağıdaki komutu kullanarak SSH üzerinden __Kafka__ kümesine bağlanın. `sshuser` değerini kümeyi oluştururken kullanılan SSH kullanıcı adıyla değiştirin. `kafkaclustername` değerini, Kafka kümenizin adıyla değiştirin:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    İstendiğinde, kümeleri oluştururken kullandığınız parolayı girin.
   
    Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Kafka konusunu oluşturmak için aşağıdaki komutu kullanın. `$KAFKAZKHOSTS` değerini topolojiyi yapılandırırken kullandığınız Zookeeper konak bilgisiyle değiştirin:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Bu komut Kafka kümesi için Zookeeper'a bağlanır ve `stormtopic` adlı yeni bir konu oluşturur. Bu konu Storm topolojileri tarafından kullanılır.

## <a name="start-the-writer"></a>Yazıcıyı başlatma

1. SSH kullanarak **Storm** kümesine bağlanmak için aşağıdakini kullanın. `sshuser` değerini kümeyi oluştururken kullanılan SSH kullanıcı adıyla değiştirin. `stormclustername` değerini Storm kümesinin adıyla değiştirin:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    İstendiğinde, kümeleri oluştururken kullandığınız parolayı girin.
   
    Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

2. SSH bağlantısından Storm kümesine, yazıcı topolojisini başlatmak için aşağıdaki komutu kullanın:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Bu komutla kullanılan parametreler şunlardır:

    * `org.apache.storm.flux.Flux`: Bu topolojiyi yapılandırmak ve çalıştırmak için Flux kullanın.

    * `--remote`: Topolojiyi Nimbus'a gönderin. Topoloji, kümedeki çalışan düğümlerine dağıtılır.

    * `-R /writer.yaml`: Topolojiyi yapılandırmak için `writer.yaml` dosyasını kullanın. `-R`, bu kaynağın jar dosyası içinde yer aldığını gösterir. Bu, jar dosyasının kökünde yer aldığından yolu `/writer.yaml` şeklindedir.

    * `--filter`: `writer.yaml` dosyasındaki değerleri kullanarak `dev.properties` topolojisindeki girdileri doldurun. Örneğin, dosyadaki `kafka.topic` girdisinin değeri topoloji tanımındaki `${kafka.topic}` girdisi yerine kullanılır.

## <a name="start-the-reader"></a>Okuyucuyu başlatma

1. SSH oturumundan Storm kümesine, okuyucu topolojisini başlatmak için aşağıdaki komutu kullanın:

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. Bir dakika bekleyin ve ardından okuyucu topolojisi tarafından oluşturulan dosyaları görüntülemek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    Çıktı aşağıdaki metne benzer:

        Found 173 items
        -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
        -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
        -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
        ...

3. Dosyanın içeriğini görüntülemek için aşağıdaki komutu kullanın. `filename.txt` değerini dosyanın adıyla değiştirin:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    Aşağıdaki metin, örnek bir dosya içeriğidir:

        four score and seven years ago
        snow white and the seven dwarfs
        i am at two with nature
        snow white and the seven dwarfs
        i am at two with nature
        four score and seven years ago
        an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Topolojileri durdurma

SSH oturumundan Storm kümesine, Storm topolojilerini durdurmak için aşağıdaki komutları kullanın:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici ile oluşturulan kaynakları temizlemek için kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, ilişkili HDInsight kümesini ve kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

Azure portalını kullanarak kaynak grubunu kaldırmak için:

1. Azure portalında sol taraftaki menüyü genişleterek hizmet menüsünü açın ve sonra __Kaynak Grupları__'nı seçerek kaynak gruplarınızın listesini görüntüleyin.
2. Silinecek kaynak grubunu bulun ve sonra listenin sağ tarafındaki __Daha fazla__ düğmesine (...) sağ tıklayın.
3. __Kaynak grubunu sil__'i seçip onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, HDInsight üzerinde [Apache Kafka](https://kafka.apache.org/) yazmak ve okumak için [Apache Storm](https://storm.apache.org/) topolojisini nasıl kullanacağınızı öğrendiniz. Ayrıca HDInsight tarafından kullanılan [Apache Hadoop](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) , uyumlu depolama alanına veri depolamayı öğrendiniz.

> [!div class="nextstepaction"]
> [Apache Kafka Producer ve tüketici API 'SI kullanma](kafka/apache-kafka-producer-consumer-api.md)

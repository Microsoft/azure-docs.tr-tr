---
title: Apache Storm örnek Java topolojisi-Azure HDInsight
description: Örnek bir sözcük sayısı topolojisi oluşturarak Java 'da Apache Storm topolojileri oluşturmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 75100b47ddf8f36ed9a22ff3073c439f8ad9040b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083290"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Java 'da Apache Storm topolojisi oluşturma

[Apache Storm](https://storm.apache.org/)için Java tabanlı topoloji oluşturmayı öğrenin. Burada, bir sözcük sayısı uygulaması uygulayan bir fırtınası topolojisi oluşturursunuz. Projeyi derlemek ve paketlemek için [Apache Maven](https://maven.apache.org/) 'yi kullanırsınız. Daha sonra, [Apache Storm Flox](https://storm.apache.org/releases/2.0.0/flux.html) çerçevesini kullanarak topolojiyi nasıl tanımlayacağınızı öğreneceksiniz.

Bu belgedeki adımları tamamladıktan sonra, HDInsight üzerinde Apache Storm için topolojiyi dağıtabilirsiniz.

> [!NOTE]  
> Bu belgede oluşturulan bir fırtınası topolojisi örneklerinin tamamlanmış bir sürümü [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)' de kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

* [Java geliştirici seti (JDK) sürüm 8](https://aka.ms/azure-jdks)

* Apache [Maven](https://maven.apache.org/download.cgi) , Apache 'e göre düzgün şekilde [yüklendi](https://maven.apache.org/install.html) .  Maven, Java projeleri için bir proje derleme sistemidir.

## <a name="test-environment"></a>Test ortamı

Bu makale için kullanılan ortam, Windows 10 çalıştıran bir bilgisayardır.  Komutlar bir komut isteminde yürütülürler ve çeşitli dosyalar Notepad ile düzenlendi.

Bir komut isteminden, çalışan bir ortam oluşturmak için aşağıdaki komutları girin:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven projesi oluşturma

**WORDCOUNT**adlı bir Maven projesi oluşturmak için aşağıdaki komutu girin:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Bu komut, temel bir Maven projesi içeren geçerli konumda `WordCount` adlı bir dizin oluşturur. İkinci komut, mevcut çalışma dizinini `WordCount`olarak değiştirir. Üçüncü komut daha sonra kullanılacak yeni bir dizin `resources`oluşturur.  `WordCount` dizin aşağıdaki öğeleri içerir:

* `pom.xml`: Maven projesinin ayarlarını Içerir.
* `src\main\java\com\microsoft\example`: uygulama kodunuzu Içerir.
* `src\test\java\com\microsoft\example`: uygulamanız için testler Içerir.  

### <a name="remove-the-generated-example-code"></a>Oluşturulan örnek kodu kaldırın

Oluşturulan test ve uygulama dosyalarını `AppTest.java`silin ve aşağıdaki komutları girerek `App.java`:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Maven depoları ekleme

HDInsight, Hortonçalışmalar veri platformunu (HDP) temel alır, bu nedenle Apache Storm projeleriniz için bağımlılıkları indirmek üzere Hortonlıs deposunu kullanmanızı öneririz.  

Aşağıdaki komutu girerek `pom.xml` açın:

```cmd
notepad pom.xml
```

Sonra `<url> https://maven.apache.org</url>` satırdan sonra aşağıdaki XML 'i ekleyin:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Özellik Ekle

Maven, özellikler olarak adlandırılan proje düzeyi değerlerini tanımlamanızı sağlar. `pom.xml`, `</repositories>` satırından sonra aşağıdaki metni ekleyin:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Artık bu değeri `pom.xml`diğer bölümlerinde kullanabilirsiniz. Örneğin, fırtınası bileşenleri sürümünü belirtirken, bir değeri sabit bir şekilde kodlamak yerine `${storm.version}` kullanabilirsiniz.

## <a name="add-dependencies"></a>Bağımlılık Ekle

Fırtınası bileşenleri için bir bağımlılık ekleyin. `pom.xml`, `<dependencies>` bölümüne aşağıdaki metni ekleyin:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Derleme zamanında Maven, Maven deposunda `storm-core` aramak için bu bilgileri kullanır. İlk olarak yerel bilgisayarınızdaki depoya bakar. Dosyalar orada yoksa Maven bunları ortak Maven deposundan indirir ve bunları yerel depoda depolar.

> [!NOTE]  
> Bu bölümdeki `<scope>provided</scope>` satırına dikkat edin. Bu ayar, Maven 'yi, sistem tarafından sağlandığı için oluşturulan tüm JAR dosyalarından **fırtınası çekirdeğini** hariç tutmasını söyler.

## <a name="build-configuration"></a>Derleme yapılandırması

Maven eklentileri projenin derleme aşamalarını özelleştirmenizi sağlar. Örneğin, projenin nasıl derlendiğini veya bir JAR dosyasına nasıl paketleneceğini öğrenin. `pom.xml`, aşağıdaki metni doğrudan `</project>` satırının üstüne ekleyin.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Bu bölüm eklenti, kaynak ve diğer derleme yapılandırma seçeneklerini eklemek için kullanılır. `pom.xml` dosyanın tam bir başvurusu için bkz. [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Eklentiler ekleme

* **Exec Maven eklentisi**

    Java 'da uygulanan Apache Storm topolojileri için, geliştirme ortamınızda yerel olarak topolojiyi kolayca çalıştırmanıza olanak sağladığından, [Exec Maven eklentisi](https://www.mojohaus.org/exec-maven-plugin/) faydalıdır. Exec Maven eklentisini dahil etmek için `pom.xml` dosyasının `<plugins>` bölümüne aşağıdakini ekleyin:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Apache Maven derleyicisi eklentisi**

    Diğer bir faydalı eklenti, derleme seçeneklerini değiştirmek için kullanılan [Apache Maven derleyicisi](https://maven.apache.org/plugins/maven-compiler-plugin/)eklentisidir. Maven 'nin uygulamanızın kaynağı ve hedefi için kullandığı Java sürümünü değiştirin.

  * HDInsight __3,4 veya önceki sürümlerde__, kaynak ve hedef Java sürümünü __1,7__olarak ayarlayın.

  * HDInsight __3,5__için, kaynak ve hedef Java sürümünü __1,8__olarak ayarlayın.

  Apache Maven derleyicisi eklentisini dahil etmek için `pom.xml` dosyasının `<plugins>` bölümüne aşağıdaki metni ekleyin. Bu örnek 1,8 belirtir, bu nedenle hedef HDInsight sürümü 3,5 ' dir.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Kaynakları yapılandırma

Kaynaklar bölümü, topolojideki bileşenlere gereken yapılandırma dosyaları gibi kod olmayan kaynakları dahil etmenize olanak tanır. Bu örnek için, `pom.xml` dosyasının `<resources>` bölümüne aşağıdaki metni ekleyin. Sonra dosyayı kaydedip kapatın.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Bu örnek, proje kökündeki kaynak dizinini (`${basedir}`) kaynakları içeren bir konum olarak ekler ve `log4j2.xml`adlı dosyayı içerir. Bu dosya, topoloji tarafından hangi bilgilerin günlüğe kaydedileceğini yapılandırmak için kullanılır.

## <a name="create-the-topology"></a>Topolojiyi oluşturma

Java tabanlı Apache Storm topolojisi, bir bağımlılık olarak yazmak (veya başvuru yapmanız gereken üç bileşenden oluşur).

* **Spout**: dış kaynaklardaki verileri okur ve veri akışlarını topolojiye yayar.

* **Cıvatalar**: spolar veya diğer cıvatları tarafından yayılan akışlar üzerinde işleme gerçekleştirir ve bir veya daha fazla akış yayar.

* **Topoloji**: Spout ve cıvatların nasıl düzenlendiğini tanımlar ve topoloji için giriş noktası sağlar.

### <a name="create-the-spout"></a>Spout oluşturma

Dış veri kaynaklarını ayarlamaya yönelik gereksinimleri azaltmak için aşağıdaki Spout rastgele cümleler yayar. Bu, [fırtınası-Starter örnekleri](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)ile birlikte sunulan bir Spout 'ın değiştirilmiş sürümüdür.  Bu topoloji yalnızca bir Spout kullanıyor olsa da, diğerleri farklı kaynaklardan alınan çeşitli akış verilerine topolojiye sahip olabilir.

`RandomSentenceSpout.java`yeni bir dosya oluşturmak ve açmak için aşağıdaki komutu girin:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Ardından aşağıdaki Java kodunu kopyalayıp yeni dosyaya yapıştırın.  Sonra dosyayı kapatın.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Dış veri kaynağından okuyan bir Spout örneği için aşağıdaki örneklerden birine bakın:
>
> * [Dallı örnek Spout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Twitter 'dan okuyan bir örnek Spout.
> * [Fırtınası-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Kafka 'dan okuyan bir Spout.

### <a name="create-the-bolts"></a>Cıvatları oluşturma

Cıvatalar, veri işlemeyi işler. Cıvatalar, örneğin hesaplama, kalıcılık veya dış bileşenlere konuşuyor gibi her şeyi gerçekleştirebilir. Bu topoloji iki cıvatları kullanır:

* **Splitcümlesini**: **Rasgelesentencespout** tarafından tek tek sözcüklere yayılan cümleler böler.

* **WORDCOUNT**: her bir sözcüğün kaç kez oluştuğunu sayar.

#### <a name="splitsentence"></a>Splitcümlesi

`SplitSentence.java`yeni bir dosya oluşturmak ve açmak için aşağıdaki komutu girin:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Ardından aşağıdaki Java kodunu kopyalayıp yeni dosyaya yapıştırın.  Sonra dosyayı kapatın.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

`WordCount.java`yeni bir dosya oluşturmak ve açmak için aşağıdaki komutu girin:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Ardından aşağıdaki Java kodunu kopyalayıp yeni dosyaya yapıştırın.  Sonra dosyayı kapatın.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Topolojiyi tanımlama

Topoloji, biriktirmelerin ve cıvatları, verilerin bileşenler arasında nasıl akacağını tanımlayan bir grafikte birleştirir. Ayrıca, bir küme içindeki bileşenlerin örneklerini oluştururken, fırtınası tarafından kullanılan paralellik ipuçları da sağlar.

Aşağıdaki görüntü, bu topolojinin bileşen grafiğinin temel bir diyagramıdır.

![Spout ve cıvatları düzenlemeyi gösteren diyagram](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Topolojiyi uygulamak için, aşağıdaki komutu girerek yeni bir dosya oluşturun `WordCountTopology.java`açın:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Ardından aşağıdaki Java kodunu kopyalayıp yeni dosyaya yapıştırın.  Sonra dosyayı kapatın.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma

Fırtınası, bilgileri günlüğe kaydetmek için [Apache Log4J 2](https://logging.apache.org/log4j/2.x/) kullanır. Günlüğe kaydetmeyi yapılandırmazsanız, topoloji tanılama bilgilerini yayar. Günlüğe nelerin kaydedildiğini denetlemek için, aşağıdaki komutu girerek `resources` dizininde `log4j2.xml` adlı bir dosya oluşturun:

```cmd
notepad resources\log4j2.xml
```

Sonra aşağıdaki XML metnini kopyalayıp yeni dosyaya yapıştırın.  Sonra dosyayı kapatın.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Bu XML, bu örnek topolojideki bileşenleri içeren `com.microsoft.example` sınıfı için yeni bir günlükçü yapılandırır. Bu, bu topolojideki bileşenler tarafından yayılan günlüğe kaydetme bilgilerini yakalayan bu günlükçü için izleme olarak ayarlanır.

`<Root level="error">` bölümü, günlük kaydetme düzeyini (`com.microsoft.example`bulunmayan her şey) yalnızca hata bilgilerini günlüğe kaydetmek üzere yapılandırır.

Log4J 2 için günlüğü yapılandırma hakkında daha fazla bilgi için bkz. [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]  
> Fırtınası sürümü 0.10.0 ve üzeri Log4J 2. x kullanır. Daha eski fırtınası sürümleri, günlük yapılandırması için farklı bir biçim kullanan 1. x Log4J kullandı. Eski yapılandırma hakkında daha fazla bilgi için bkz. [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Topolojiyi yerel olarak test etme

Dosyaları kaydettikten sonra, topolojiyi yerel olarak test etmek için aşağıdaki komutu kullanın.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Çalıştırıldığında, topoloji başlangıç bilgilerini görüntüler. Aşağıdaki metin, sözcük sayısı çıkışının bir örneğidir:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Bu örnek günlük, ' ve ' kelimesinin 113 katı olduğunu gösterir. Spout sürekli aynı cümleleri yaydığı için, sayı topoloji çalıştığı sürece çalışmaya devam eder.

Sözcükler ve sayımlar arasında 5 saniyelik bir Aralık vardır. **WORDCOUNT** bileşeni yalnızca bir değer tanımlama grubu geldiğinde bilgileri göstermek üzere yapılandırılmıştır. Değer tanımlama gruplarının yalnızca beş saniyede bir teslim edildiğini ister.

## <a name="convert-the-topology-to-flux"></a>Topolojiyi Flox 'e Dönüştür

[Flox](https://storm.apache.org/releases/2.0.0/flux.html) , 0.10.0 ve üzeri bir sürümü kullanarak, yapılandırmayı uygulamadan ayırmanızı sağlayan yeni bir çerçevedir. Bileşenleriniz hala Java 'da tanımlanmıştır, ancak topoloji bir YAML dosyası kullanılarak tanımlanır. Projeniz ile varsayılan bir topoloji tanımını paketleyebilir veya topolojiyi gönderirken tek başına bir dosya kullanabilirsiniz. Topolojiyi fırtınası 'ya gönderirken, YAML topolojisi tanımındaki değerleri doldurmak için ortam değişkenlerini veya yapılandırma dosyalarını kullanabilirsiniz.

YAML dosyası, topoloji ve aralarında veri akışı için kullanılacak bileşenleri tanımlar. Jar dosyasının bir parçası olarak bir YAML dosyası ekleyebilirsiniz veya bir dış YAML dosyası kullanabilirsiniz.

Flox hakkında daha fazla bilgi için bkz. [Flox Framework (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> Bir hata nedeniyle [(](https://issues.apache.org/jira/browse/STORM-2055) fırtınası 1.0.1 ile https://issues.apache.org/jira/browse/STORM-2055), Flox topolojilerini yerel olarak çalıştırmak Için bir [fırtınası geliştirme ortamı](https://storm.apache.org/releases/current/Setting-up-development-environment.html) yüklemeniz gerekebilir.

1. Daha önce `WordCountTopology.java` topoloji tanımladı, ancak Flox ile gerekli değildir. Aşağıdaki komutla dosyayı silin:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. `topology.yaml`yeni bir dosya oluşturmak ve açmak için aşağıdaki komutu girin:

    ```cmd
    notepad resources\topology.yaml
    ```

    Ardından aşağıdaki metni kopyalayıp yeni dosyaya yapıştırın.  Sonra dosyayı kapatın.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Aşağıda açıklanan düzeltmeleri yapmak üzere `pom.xml` açmak için aşağıdaki komutu girin:

    ```cmd
    notepad pom.xml
    ```

   1. `<dependencies>` bölümüne aşağıdaki yeni bağımlılığı ekleyin:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. `<plugins>` bölümüne aşağıdaki eklentiyi ekleyin. Bu eklenti, proje için bir paket (jar dosyası) oluşturmayı işler ve paketi oluştururken Flox 'e özgü bazı dönüştürmeleri uygular.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   1. Exec Maven eklentisi bölümünde `<configuration>` > `<mainClass>` gidin ve `${storm.topology}` `org.apache.storm.flux.Flux`olarak değiştirin. Bu ayar, akıcı x 'in, geliştirme sırasında topolojiyi yerel olarak işlemesini sağlar.

   1. `<resources>` bölümünde, `<includes>`için aşağıdakini ekleyin. Bu XML, projenin bir parçası olarak topolojiyi tanımlayan YAML dosyasını içerir.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Flox topolojisini yerel olarak test etme

1. Maven kullanarak Flox topolojisini derlemek ve yürütmek için aşağıdaki komutu girin:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Topolojiniz fırtınası 1.0.1 bitleri kullanıyorsa, bu komut başarısız olur. Bu hata neden [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055). Bunun yerine, [geliştirme ortamınıza fırtınası 'yı yükledikten](https://storm.apache.org/releases/current/Setting-up-development-environment.html) sonra aşağıdaki adımları kullanın:
    >
    > [Geliştirme ortamınıza fırtınası yüklediyseniz](https://storm.apache.org/releases/current/Setting-up-development-environment.html), bunun yerine aşağıdaki komutları kullanabilirsiniz:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local` parametresi, topolojiyi geliştirme ortamınızda yerel modda çalıştırır. `-R /topology.yaml` parametresi, topolojiyi tanımlamak için jar dosyasındaki `topology.yaml` dosya kaynağını kullanır.

    Çalıştırıldığında, topoloji başlangıç bilgilerini görüntüler. Aşağıdaki metin, çıktının bir örneğidir:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Günlüğe kaydedilen bilgilerin toplu işleri arasında 10 saniyelik bir gecikme vardır.

2. Projeden yeni bir topoloji YAML oluşturun.

    1. `topology.xml`açmak için aşağıdaki komutu girin:

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Aşağıdaki bölümü bulun ve `10` değerini `5`olarak değiştirin. Bu değişiklik, sözcük sayısı, 10 saniyeden 5 ' e kadar olan yayma toplu işleri arasındaki aralığı değiştirir.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Dosyayı `newtopology.yaml`olarak kaydedin.

3. Topolojiyi çalıştırmak için aşağıdaki komutu girin:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Ya da, geliştirme ortamınızda bir fırtınası varsa:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Bu komut, `newtopology.yaml` topoloji tanımı olarak kullanır. `compile` parametresini içermedik, Maven önceki adımlarda oluşturulan projenin sürümünü kullanıyor.

    Topoloji başladıktan sonra, yayılan toplu işler arasındaki sürenin `newtopology.yaml`değeri yansıtacak şekilde değiştiğini fark etmelisiniz. Bu nedenle, topolojiyi yeniden derlemek zorunda kalmadan bir YAML dosyası aracılığıyla yapılandırmanızı değiştirebilmenizi sağlayabilirsiniz.

Bu ve akışkan x çerçevesinin diğer özellikleri hakkında daha fazla bilgi için bkz. [Flox (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Çatal

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) , fırtınası tarafından sunulan üst düzey bir soyutlamadır. Durum bilgisi olan işlemeyi destekler. Trident 'nin birincil avantajı, topolojiye giren her iletinin yalnızca bir kez işlendiğini garanti edebileceğinizin güvencesidir. Trident kullanılmadan, topolojiniz yalnızca iletilerin en az bir kez işlenmesini garanti edebilir. Ayrıca, cıvatları oluşturmak yerine kullanılabilecek yerleşik bileşenler gibi başka farklılıklar da vardır. Aslında, cıvatalar, filtreler, tahminler ve işlevler gibi daha az genel bileşenlerle değiştirilmiştir.

Trident uygulamaları Maven projeleri kullanılarak oluşturulabilir. Bu makalede daha önce sunulan temel adımların aynısını kullanırsınız; yalnızca kod farklıdır. Trident, Flox çerçevesiyle de (Şu anda) kullanılamaz.

Trident hakkında daha fazla bilgi için bkz. [Trident API 'Sine genel bakış](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Sonraki Adımlar

Java kullanarak Apache Storm topolojisi oluşturmayı öğrendiniz. Şimdi şunları yapmayı öğrenirsiniz:

* [HDInsight 'ta Apache Storm topolojileri dağıtma ve yönetme](apache-storm-deploy-monitor-topology-linux.md)

* [Visual C# Studio kullanarak hdınsight 'ta Apache Storm için topolojiler geliştirin](apache-storm-develop-csharp-visual-studio-topology.md)

[HDInsight üzerinde Apache Storm Için örnek topolojilerini](apache-storm-example-topology.md)ziyaret ederek daha fazla örnek Apache Storm topolojiden ulaşabilirsiniz.

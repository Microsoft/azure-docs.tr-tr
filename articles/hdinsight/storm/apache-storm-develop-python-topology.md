---
title: Python bileşenleriyle Apache Storm-Azure HDInsight
description: Azure HDInsight 'ta Python bileşenleri kullanan bir Apache Storm topolojisi oluşturma hakkında bilgi edinin
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-python
ms.date: 12/16/2019
ms.openlocfilehash: e28d21ed71cf5f485165c639a8bd519b3a2736e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928991"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>HDInsight 'ta Python kullanarak Apache Storm topolojileri geliştirme

Python bileşenleri kullanan bir [Apache Storm](https://storm.apache.org/) topolojisi oluşturmayı öğrenin. Apache Storm birden çok dili destekler, hatta bileşenleri tek bir topolojide çeşitli dillerden birleştirmeniz sağlanır. [Flox](https://storm.apache.org/releases/current/flux.html) çerçevesi (fırtınası 0.10.0 ile sunulan), Python bileşenleri kullanan çözümleri kolayca oluşturmanızı sağlar.

> [!IMPORTANT]  
> Bu belgedeki bilgiler, HDInsight 3,6 ' de fırtınası kullanılarak test edilmiştir.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Storm kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) ve **küme türü** için **fırtınası** seçme.

* Yerel bir fırtınası geliştirme ortamı (Isteğe bağlı). Yerel bir fırtınası ortamı yalnızca, topolojiyi yerel olarak çalıştırmak istiyorsanız gereklidir. Daha fazla bilgi için bkz. [geliştirme ortamı kurma](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2,7 veya üzeri](https://www.python.org/downloads/).

* [Java geliştirici seti (JDK) sürüm 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Apache [Maven](https://maven.apache.org/download.cgi) , Apache 'e göre düzgün şekilde [yüklendi](https://maven.apache.org/install.html) .  Maven, Java projeleri için bir proje derleme sistemidir.

## <a name="storm-multi-language-support"></a>Fırtınası çok dil desteği

Apache Storm, herhangi bir programlama dili kullanılarak yazılmış bileşenlerle çalışmak üzere tasarlanmıştır. Bileşenler, fırtınası için Thrift tanımıyla nasıl çalışacağınızı anlamalıdır. Python için, bir modül Apache Storm projenin bir parçası olarak sağlanır ve bu da, fırtınası ile kolayca arabirim oluşturmanızı sağlar. Bu modülü adresinde bulabilirsiniz [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) .

Fırtınası Java Sanal Makinesi (JVM) üzerinde çalışan bir Java işlemidir. Diğer dillerde yazılan bileşenler alt işlem olarak yürütülür. Fırtınası, stdin/stdout üzerinden gönderilen JSON iletilerini kullanarak bu alt işlemlerle iletişim kurar. Bileşenler arasındaki iletişimle ilgili daha fazla ayrıntı, [Multi-Lang Protocol](https://storm.apache.org/releases/current/Multilang-protocol.html) belgelerinde bulunabilir.

## <a name="python-with-the-flux-framework"></a>Flox çerçevesiyle Python

Flox Framework, her bir bileşenden ayrı olarak fırtınası tanımlamanızı sağlar. Flox çerçevesi, fırtınası topolojisini tanımlamak için YAML 'yi kullanır. Aşağıdaki metin, YAML belgesinde bir Python bileşenine nasıl başvurulacağını gösteren bir örnektir:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

Sınıfı `FluxShellSpout` `sentencespout.py` , Spout 'yi uygulayan betiği başlatmak için kullanılır.

Flox, Python betikleri 'nin `/resources` topolojiyi içeren jar dosyasının içindeki dizininde olmasını bekler. Bu nedenle bu örnek, Python betiklerini dizinde depolar `/multilang/resources` . , `pom.xml` AŞAĞıDAKI XML 'i kullanarak bu dosyayı içerir:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Daha önce belirtildiği gibi, `storm.py` fırtınası Için Thrift tanımını uygulayan bir dosya vardır. Akıcı x Framework, `storm.py` Proje yapılandırıldığında otomatik olarak içerir, bu nedenle dahil olmak üzere endişelenmeniz gerekmez.

## <a name="build-the-project"></a>Projeyi derleme

1. Projeyi konumundan indirin [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) .

1. Bir komut istemi açın ve proje köküne gidin: `hdinsight-python-storm-wordcount-master` . Aşağıdaki komutu girin:

    ```cmd
    mvn clean compile package
    ```

    Bu komut `target/WordCount-1.0-SNAPSHOT.jar` , derlenmiş topolojiyi içeren bir dosya oluşturur.

## <a name="run-the-storm-topology-on-hdinsight"></a>HDInsight 'ta fırtınası topolojisini çalıştırma

1. Dosyayı HDInsight kümenizdeki fırtınası kümenize kopyalamak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın `WordCount-1.0-SNAPSHOT.jar` . CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Dosya karşıya yüklendikten sonra SSH kullanarak kümeye bağlanın:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH oturumunda, kümede topolojiyi başlatmak için aşağıdaki komutu kullanın:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Başlatıldıktan sonra, bir fırtınası topolojisi durduruluncaya kadar çalışır.

1. Kümedeki topolojiyi görüntülemek için fırtınası Kullanıcı arabirimini kullanın. Fırtınası Kullanıcı arabirimi konumunda bulunur `https://CLUSTERNAME.azurehdinsight.net/stormui` . `CLUSTERNAME`Küme adınızla değiştirin.

1. Fırtınası topolojisini durdurun. Kümedeki topolojiyi durdurmak için aşağıdaki komutu kullanın:

    ```bash
    storm kill wordcount
    ```

    Alternatif olarak, fırtınası Kullanıcı arabirimini de kullanabilirsiniz. Topoloji için **topoloji eylemleri** altında **Sonlandır**' ı seçin.

## <a name="run-the-topology-locally"></a>Topolojiyi yerel olarak çalıştırma

Topolojiyi yerel olarak çalıştırmak için aşağıdaki komutu kullanın:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Bu komut için yerel bir fırtınası geliştirme ortamı gerekir. Daha fazla bilgi için bkz. [geliştirme ortamı kurma](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

Topoloji başladıktan sonra, yerel konsola aşağıdaki metne benzer bilgiler yayar:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Topolojiyi durdurmak için __CTRL + C__ kullanın.

## <a name="next-steps"></a>Sonraki adımlar

HDInsight ile Python kullanmanın diğer yolları için aşağıdaki belgelere bakın: [Apache Pig ve Apache Hive Python Kullanıcı tanımlı işlevleri (UDF) kullanma](../hadoop/python-udf-hdinsight.md).
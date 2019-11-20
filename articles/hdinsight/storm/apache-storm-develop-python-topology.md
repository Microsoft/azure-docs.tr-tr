---
title: Python bileşenleriyle Apache Storm-Azure HDInsight
description: Azure HDInsight 'ta Python bileşenleri kullanan bir Apache Storm topolojisi oluşturma hakkında bilgi edinin
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache fırtınası Python
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: a15506632e90edae235c3d1889603ca4997a3398
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813884"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>HDInsight 'ta Python kullanarak Apache Storm topolojileri geliştirme

Python bileşenleri kullanan bir [Apache Storm](https://storm.apache.org/) topolojisi oluşturmayı öğrenin. Apache Storm birden çok dili destekler, hatta bileşenleri tek bir topolojide çeşitli dillerden birleştirmeniz sağlanır. [Flox](https://storm.apache.org/releases/current/flux.html) çerçevesi (fırtınası 0.10.0 ile sunulan), Python bileşenleri kullanan çözümleri kolayca oluşturmanızı sağlar.

> [!IMPORTANT]  
> Bu belgedeki bilgiler, HDInsight 3,6 ' de fırtınası kullanılarak test edilmiştir. 

Bu proje için kod, adresinde [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)bulunabilir.

## <a name="prerequisites"></a>Önkoşullar

* Python 2,7 veya üzeri

* Java JDK 1,8 veya üzeri

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* Seçim Yerel bir fırtınası geliştirme ortamı. Yerel bir fırtınası ortamı yalnızca, topolojiyi yerel olarak çalıştırmak istiyorsanız gereklidir. Daha fazla bilgi için bkz. [geliştirme ortamı kurma](http://storm.apache.org/releases/current/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Fırtınası çok dil desteği

Apache Storm, herhangi bir programlama dili kullanılarak yazılmış bileşenlerle çalışmak üzere tasarlanmıştır. Bileşenler, [fırtınası Için Thrift tanımıyla](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)nasıl çalışacağınızı anlamalıdır. Python için, bir modül Apache Storm projenin bir parçası olarak sağlanır ve bu da, fırtınası ile kolayca arabirim oluşturmanızı sağlar. Bu modülü adresinde [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)bulabilirsiniz.

Fırtınası Java Sanal Makinesi (JVM) üzerinde çalışan bir Java işlemidir. Diğer dillerde yazılan bileşenler alt işlem olarak yürütülür. Fırtınası, stdin/stdout üzerinden gönderilen JSON iletilerini kullanarak bu alt işlemlerle iletişim kurar. Bileşenler arasındaki iletişimle ilgili daha fazla ayrıntı, [Multi-Lang Protocol](https://storm.apache.org/documentation/Multilang-protocol.html) belgelerinde bulunabilir.

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

Sınıfı `FluxShellSpout` , Spout 'yi uygulayan `sentencespout.py` betiği başlatmak için kullanılır.

Flox, Python betikleri 'nin topolojiyi içeren jar dosyasının `/resources` içindeki dizininde olmasını bekler. Bu nedenle bu örnek, Python betiklerini `/multilang/resources` dizinde depolar. , `pom.xml` Aşağıdaki XML 'i kullanarak bu dosyayı içerir:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Daha önce belirtildiği gibi, fırtınası için `storm.py` Thrift tanımını uygulayan bir dosya vardır. Akıcı x Framework, proje `storm.py` yapılandırıldığında otomatik olarak içerir, bu nedenle dahil olmak üzere endişelenmeniz gerekmez.

## <a name="build-the-project"></a>Proje derleme

Projenin kökünden aşağıdaki komutu kullanın:

```bash
mvn clean compile package
```

Bu komut, derlenmiş `target/WordCount-1.0-SNAPSHOT.jar` topolojiyi içeren bir dosya oluşturur.

## <a name="run-the-topology-locally"></a>Topolojiyi yerel olarak çalıştırma

Topolojiyi yerel olarak çalıştırmak için aşağıdaki komutu kullanın:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Bu komut için yerel bir fırtınası geliştirme ortamı gerekir. Daha fazla bilgi için bkz. [geliştirme ortamı ayarlama](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

Topoloji başladıktan sonra, yerel konsola aşağıdaki metne benzer bilgiler yayar:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Topolojiyi durdurmak için __CTRL + C__kullanın.

## <a name="run-the-storm-topology-on-hdinsight"></a>HDInsight 'ta fırtınası topolojisini çalıştırma

1. `WordCount-1.0-SNAPSHOT.jar` Dosyayı HDInsight kümenizdeki fırtınası kümenize kopyalamak için aşağıdaki komutu kullanın:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Kümenizin `sshuser` SSH kullanıcısı ile değiştirin. Küme `mycluster` adıyla değiştirin. SSH kullanıcısının parolasını girmeniz istenebilir.

    SSH ve SCP kullanma hakkında daha fazla bilgi için bkz. [HDInsight Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Dosya karşıya yüklendikten sonra SSH kullanarak kümeye bağlanın:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. SSH oturumunda, kümede topolojiyi başlatmak için aşağıdaki komutu kullanın:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Küme üzerindeki topolojiyi görüntülemek için fırtınası Kullanıcı arabirimini kullanabilirsiniz. Fırtınası Kullanıcı arabirimi konumunda https://mycluster.azurehdinsight.net/stormui bulunur. Küme `mycluster` adınızla değiştirin.

> [!NOTE]  
> Başlatıldıktan sonra, bir fırtınası topolojisi durduruluncaya kadar çalışır. Topolojiyi durdurmak için aşağıdaki yöntemlerden birini kullanın:
>
> * Komut `storm kill TOPOLOGYNAME` satırından komut
> * Fırtınası Kullanıcı arabirimindeki **KILL** düğmesi.


## <a name="next-steps"></a>Sonraki adımlar

HDInsight ile Python kullanmanın diğer yolları için aşağıdaki belgelere bakın:

* [Apache Pig ve Apache Hive 'da Python Kullanıcı tanımlı Işlevleri (UDF) kullanma](../hadoop/python-udf-hdinsight.md)

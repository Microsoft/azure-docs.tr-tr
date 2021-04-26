---
title: 'Öğretici: HDInsight Apache Storm depolama-Azure/Data Lake'
description: Öğretici-Azure HDInsight için, Apache Storm ile uyumlu depolamaya yazmak üzere kullanmayı öğrenin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 4e648c57be699620e669ce7db0845dad2b876095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932549"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Öğretici: Azure HDInsight 'ta Apache Storm Apache Hadoop ini yazma

Bu öğreticide, HDInsight üzerinde Apache Storm tarafından kullanılan, Apache Storm ile uyumlu depolamaya veri yazmak için nasıl kullanılacağı gösterilmektedir. HDInsight hem Azure Storage hem de Azure Data Lake Storage ile uyumlu depolama olarak kullanabilir. Fırtınası,, [](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) verileri, Bu belge, Hdfscıvata her iki depolama türüne yazma hakkında bilgi sağlar.

Bu belgede kullanılan örnek topoloji, HDInsight üzerinde fırtınası ile birlikte gelen bileşenlere bağımlıdır. Diğer Apache Storm kümeleriyle birlikte kullanıldığında, değişikliğin Azure Data Lake Storage çalışmasını gerektirebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Kümeyi betik eylemiyle yapılandırma
> * Topolojiyi derleyin ve paketleyin
> * Topolojiyi dağıtma ve çalıştırma
> * Çıktı verilerini görüntüle
> * Topolojiyi durdurma

## <a name="prerequisites"></a>Önkoşullar

* [Java geliştirici seti (JDK) sürüm 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Apache [Maven](https://maven.apache.org/download.cgi) , Apache 'e göre düzgün şekilde [yüklendi](https://maven.apache.org/install.html) .  Maven, Java projeleri için bir proje derleme sistemidir.

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Kümelerinizin birincil depolama alanı için [URI şeması](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Bu `wasb://` , `abfs://` Azure Data Lake Storage 2. veya Azure Data Lake Storage 1. Için Azure depolama için olacaktır `adl://` . Azure depolama için güvenli aktarım etkinse URI olur `wasbs://` .  Ayrıca bkz. [Güvenli aktarım](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Örnek yapılandırma

Aşağıdaki YAML, `resources/writetohdfs.yaml` örnekte yer alan dosyanın bir alıntısıdır. Bu dosya, Apache Storm için [Flox](https://storm.apache.org/releases/current/flux.html) çerçevesini kullanarak fırtınası topolojisini tanımlar.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
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
```

Bu YAML aşağıdaki öğeleri tanımlar:

* `syncPolicy`: Dosyaların dosya sistemine ne zaman senkronize/boşaltımı olduğunu tanımlar. Bu örnekte, her 1000 tanımlama grubu.
* `fileNameFormat`: Dosyalar yazılırken kullanılacak yolu ve dosya adı modelini tanımlar. Bu örnekte, yol çalışma zamanında bir filtre kullanılarak sağlanır ve dosya uzantısı `.txt` .
* `recordFormat`: Yazılan dosyaların dahili biçimini tanımlar. Bu örnekte, alanlar karakter ile sınırlandırılmıştır `|` .
* `rotationPolicy`: Dosyaların ne zaman döndürüleceğini tanımlar. Bu örnekte, hiçbir döndürme gerçekleştirilmez.
* `hdfs-bolt`: Önceki bileşenleri sınıf için yapılandırma parametreleri olarak kullanır `HdfsBolt` .

Flox çerçevesi hakkında daha fazla bilgi için bkz [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) ..

## <a name="configure-the-cluster"></a>Kümeyi yapılandırma

Varsayılan olarak, HDInsight üzerindeki fırtınası, `HdfsBolt` Azure depolama ile iletişim kurmak için kullanılan bileşenleri veya fırtınası sınıfınData Lake Storage. Bu bileşenleri kümenizdeki fırtınası dizinine eklemek için aşağıdaki betik eylemini kullanın `extlib` :

| Özellik | Değer |
|---|---|
|Betik türü |-Özel|
|Bash betiği URI 'SI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Düğüm türleri |Nimbus, Gözetmen|
|Parametreler |Hiçbiri|

Bu betiği kümenizle birlikte kullanma hakkında bilgi için bkz. [betik eylemleri kullanarak HDInsight kümelerini özelleştirme](./../hdinsight-hadoop-customize-cluster-linux.md) belgesi.

## <a name="build-and-package-the-topology"></a>Topolojiyi derleyin ve paketleyin

1. Örnek projeyi öğesinden [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) geliştirme ortamınıza indirin.

2. Bir komut istemi, Terminal veya kabuk oturumundan, dizinleri indirilen projenin köküne değiştirin. Topolojiyi derlemek ve paketlemek için şu komutu kullanın:

    ```cmd
    mvn compile package
    ```

    Derleme ve paketleme tamamlandığında adlı bir dosya içeren adlı yeni bir dizin vardır `target` `StormToHdfs-1.0-SNAPSHOT.jar` . Bu dosya derlenen topolojiyi içerir.

## <a name="deploy-and-run-the-topology"></a>Topolojiyi dağıtma ve çalıştırma

1. Topolojiyi HDInsight kümesine kopyalamak için aşağıdaki komutu kullanın. `CLUSTERNAME`Kümenin adıyla değiştirin.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Karşıya yükleme tamamlandıktan sonra, SSH kullanarak HDInsight kümesine bağlanmak için aşağıdakileri kullanın. `CLUSTERNAME`Kümenin adıyla değiştirin.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Bağlandıktan sonra aşağıdaki komutu kullanarak adlı bir dosya oluşturun `dev.properties` :

    ```bash
    nano dev.properties
    ```

1. Dosyanın içeriği olarak aşağıdaki metni kullanın `dev.properties` . [URI şemanız](../hdinsight-hadoop-linux-information.md#URI-and-scheme)temelinde gereken şekilde gözden geçirin.

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Dosyayı kaydetmek için __CTRL + X__, sonra __Y__ ve son olarak __ENTER__ tuşlarını kullanın. Bu dosyadaki değerler, depolama URL 'sini ve verilerin yazıldığı dizin adını ayarlar.

1. Topolojiyi başlatmak için aşağıdaki komutu kullanın:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Bu komut, bu komutu kümenin Nimbus düğümüne göndererek, Flox çerçevesini kullanarak topolojiyi başlatır. Topoloji, `writetohdfs.yaml` jar 'e dahil edilen dosya tarafından tanımlanır. `dev.properties`Dosya bir filtre olarak geçirilir ve dosyada bulunan değerler topoloji tarafından okunabilir.

## <a name="view-output-data"></a>Çıktı verilerini görüntüle

Verileri görüntülemek için aşağıdaki komutu kullanın:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Bu topoloji tarafından oluşturulan dosyaların bir listesi görüntülenir. Aşağıdaki liste, önceki komutlar tarafından döndürülen verilerin bir örneğidir:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Topolojiyi durdurma

Fırtınası topolojileri durduruluncaya kadar veya küme silindikten sonra çalışır. Topolojiyi durdurmak için aşağıdaki komutu kullanın:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici ile oluşturulan kaynakları temizlemek için kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, ilişkili HDInsight kümesini ve kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

Azure portalını kullanarak kaynak grubunu kaldırmak için:

1. Azure portalında sol taraftaki menüyü genişleterek hizmet menüsünü açın ve sonra __Kaynak Grupları__'nı seçerek kaynak gruplarınızın listesini görüntüleyin.
2. Silinecek kaynak grubunu bulun ve sonra listenin sağ tarafındaki __Daha fazla__ düğmesine (...) sağ tıklayın.
3. __Kaynak grubunu sil__'i seçip onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, HDInsight üzerinde Apache Storm tarafından kullanılan, Apache Storm ile uyumlu depolama alanına veri yazmak için nasıl kullanacağınızı öğrendiniz.

> [!div class="nextstepaction"]
> [HDInsight için diğer Apache Storm örnekleri](apache-storm-example-topology.md) bulma
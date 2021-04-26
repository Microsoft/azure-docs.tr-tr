---
title: Azure HDInsight 'ta etkileşimli Spark kabuğu kullanma
description: Etkileşimli bir Spark kabuğu, Spark komutlarının tek seferde çalıştırılması ve sonuçları görmek için bir okuma-yürütme-yazdırma işlemi sağlar.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: 324852a967b5de015a9b1e9b465d4b4703e573cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929685"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Spark kabuğu 'ndan Apache Spark çalıştırma

Etkileşimli bir [Apache Spark](https://spark.apache.org/) kabuğu Spark komutlarının tek seferde çalıştırılması ve sonuçları görmesi IÇIN bir REPL (okuma-yürütme-yazdırma döngüsü) ortamı sağlar. Bu işlem, geliştirme ve hata ayıklama için kullanışlıdır. Spark, desteklenen dillerin her biri için bir kabuk sağlar: Scala, Python ve R.

## <a name="run-an-apache-spark-shell"></a>Apache Spark kabuğu çalıştırma

1. Kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark, Scala (Spark-Shell) ve Python (pyspark) için kabuklar sağlar. SSH oturumunda aşağıdaki komutlardan *birini* girin:

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    Herhangi bir isteğe bağlı yapılandırma kullanmayı düşünüyorsanız, [Apache Spark Için OutOfMemoryError özel durumunu](./apache-spark-troubleshoot-outofmemory.md)gözden geçirdiğinizden emin olun.

1. Birkaç temel örnek komut. İlgili dili seçin:

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. Bir CSV dosyasını sorgulayın. Aşağıdaki dilin ve için çalışıp çalışmadığını aklınızda bulabilirsiniz `spark-shell` `pyspark` .

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Bir CSV dosyası sorgulama ve sonuçları şu değişkende depola:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Sonuçları görüntüle:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Çıkış

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>Mini oturum ve mini bağlam örnekleri

Spark kabuğunu çalıştırdığınızda varsayılan olarak, sizin için otomatik olarak Mini oturum ve parlak bağlam örnekleri oluşturulur.

Mini erişimli oturum örneğine erişmek için girin `spark` . Mini bağlam örneğine erişmek için girin `sc` .

## <a name="important-shell-parameters"></a>Önemli kabuk parametreleri

Spark Shell komutu ( `spark-shell` veya `pyspark` ) birçok komut satırı parametresini destekler. Parametrelerin tam listesini görmek için Spark kabuğunu anahtarla başlatın `--help` . Bu parametrelerden bazıları yalnızca `spark-submit` Spark kabuğu 'nun sarmaladığı için geçerlidir.

| switch | açıklama | örnek |
| --- | --- | --- |
| --Master MASTER_URL | Ana URL 'YI belirtir. HDInsight 'ta bu değer her zaman olur `yarn` . | `--master yarn`|
| --jars JAR_LIST | Sürücü ve yürütücü Sınıfyoluna dahil etmek için yerel jar dosyaları dışındaki 'ın virgülle ayrılmış listesi. HDInsight 'ta, bu liste Azure Storage veya Data Lake Storage varsayılan dosya sistemine yönelik yollardan oluşur. | `--jars /path/to/examples.jar` |
| --paketler MAVEN_COORDS | Sürücü ve yürütücü Sınıfyoluna dahil etmek için jar dosyaları dışındaki 'ın Maven koordinatlarının virgülle ayrılmış listesi. Yerel Maven depolarında, Maven orta, ve ile belirtilen diğer uzak depolarda arama yapar `--repositories` . Koordinatlar için biçim *GroupID*:*ArtifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --Kopyala-Dosyalar LISTESI | Yalnızca Python için, PYTHONPATH üzerine yerleştirilecek. zip,. yumurg veya. Kopyala dosyalarının virgülle ayrılmış bir listesi. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure HDInsight 'ta Apache Spark giriş](apache-spark-overview.md) .
- Spark kümeleri ve parlak SQL ile çalışmak için bkz. [Azure HDInsight 'ta Apache Spark kümesi oluşturma](apache-spark-jupyter-spark-sql.md) .
- Spark ile akış verilerini işleyen uygulamalar yazmak için bkz. [Apache Spark yapısal akışı nedir?](apache-spark-streaming-overview.md) .

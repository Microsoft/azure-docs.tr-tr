---
title: Apache Hadoop-Azure HDInsight ile MapReduce ve SSH bağlantısı
description: HDInsight üzerinde Apache Hadoop kullanarak MapReduce işlerini çalıştırmak için SSH 'yi nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 991b91d1feee185d17bbf01266e0392f347e1a66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939647"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>HDInsight üzerinde Apache Hadoop MapReduce 'yi SSH ile kullanma

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

MapReduce işlerinin bir Secure Shell (SSH) bağlantısından HDInsight 'a nasıl göndereceğini öğrenin.

> [!NOTE]
> Linux tabanlı Apache Hadoop sunucuları kullanmayı zaten biliyorsanız, ancak HDInsight 'a yeni bir adım daha sahipseniz bkz. [Linux tabanlı HDInsight ipuçları](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Önkoşullar

HDInsight üzerinde bir Apache Hadoop kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Hadoop komutlarını kullanma

1. Kümenize bağlanmak için [SSH komutunu](../hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. HDInsight kümesine bağlandıktan sonra, bir MapReduce işi başlatmak için aşağıdaki komutu kullanın:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Bu komut `wordcount` , dosyasında bulunan sınıfını başlatır `hadoop-mapreduce-examples.jar` . `/example/data/gutenberg/davinci.txt`Belgeyi girdi olarak kullanır ve çıkış konumunda depolanır `/example/data/WordCountOutput` .

    > [!NOTE]
    > Bu MapReduce işi ve örnek veriler hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop MapReduce kullanma](hdinsight-use-mapreduce.md).

    İş, işleme yaptığı sırada ayrıntıları yayar ve iş tamamlandığında aşağıdaki metne benzer bilgiler döndürür:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. İş tamamlandığında, çıktı dosyalarını listelemek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Bu komut, ve olarak iki dosya görüntüler `_SUCCESS` `part-r-00000` . `part-r-00000`Dosya bu iş için çıktıyı içerir.

    > [!NOTE]  
    > Bazı MapReduce işleri, sonuçları birden çok **Part-r-#** # # # # dosyasında bölebilir. Bu durumda, dosyaların sırasını göstermek için # # # # # sonekini kullanın.

1. Çıktıyı görüntülemek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Bu komut, **wasbs://example/data/gutenberg/davinci.txt** dosyasında yer alan sözcüklerin listesini ve her sözcüğün kaç kez oluştuğunu görüntüler. Aşağıdaki metin, dosyasında yer alan verilerin bir örneğidir:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Sonraki adımlar

Görebileceğiniz gibi, Hadoop komutları bir HDInsight kümesinde MapReduce işlerini çalıştırmanın kolay bir yolunu sağlar ve sonra iş çıktısını görüntüler. HDInsight 'ta Hadoop ile birlikte çalışmak için kullanabileceğiniz diğer yollar hakkında daha fazla bilgi için:

* [HDInsight Hadoop üzerinde MapReduce kullanma](hdinsight-use-mapreduce.md)
* [HDInsight üzerinde Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md)

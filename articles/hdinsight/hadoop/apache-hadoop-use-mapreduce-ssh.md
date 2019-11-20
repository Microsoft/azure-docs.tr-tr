---
title: Apache Hadoop-Azure HDInsight ile MapReduce ve SSH bağlantısı
description: HDInsight üzerinde Apache Hadoop kullanarak MapReduce işlerini çalıştırmak için SSH 'yi nasıl kullanacağınızı öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: b4075de1a184896d598c11d09ae2b2bda5e257ed
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044518"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>HDInsight üzerinde Apache Hadoop MapReduce 'yi SSH ile kullanma

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

MapReduce işlerinin bir Secure Shell (SSH) bağlantısından HDInsight 'a nasıl göndereceğini öğrenin.

> [!NOTE]
> Linux tabanlı Apache Hadoop sunucuları kullanmayı zaten biliyorsanız, ancak HDInsight 'a yeni bir adım daha sahipseniz bkz. [Linux tabanlı HDInsight ipuçları](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Önkoşullar

* Linux tabanlı HDInsight (HDInsight üzerinde Hadoop) kümesi

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [HDInsight Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>SSH ile bağlanma

SSH kullanarak kümeye bağlanın. Örneğin, aşağıdaki komut, **sshuser** hesabı olarak **myhdınsight** adlı bir kümeye bağlanır:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**SSH kimlik doğrulaması için bir sertifika anahtarı kullanırsanız**, istemci sisteminizdeki özel anahtarın konumunu belirtmeniz gerekebilir, örneğin:

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**SSH kimlik doğrulaması için bir parola kullanırsanız**, istendiğinde parolayı sağlamanız gerekir.

HDInsight ile SSH kullanma hakkında daha fazla bilgi için bkz. [HDInsight Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Hadoop komutlarını kullanma

1. HDInsight kümesine bağlandıktan sonra, bir MapReduce işi başlatmak için aşağıdaki komutu kullanın:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Bu komut, `hadoop-mapreduce-examples.jar` dosyasında bulunan `wordcount` sınıfını başlatır. Giriş olarak `/example/data/gutenberg/davinci.txt` belgeyi kullanır ve çıkış `/example/data/WordCountOutput`kaydedilir.

    > [!NOTE]
    > Bu MapReduce işi ve örnek veriler hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop MapReduce kullanma](hdinsight-use-mapreduce.md).

2. İş, işleme yaptığı sırada ayrıntıları yayar ve iş tamamlandığında aşağıdaki metne benzer bilgiler döndürür:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. İş tamamlandığında, çıktı dosyalarını listelemek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Bu komut, `_SUCCESS` ve `part-r-00000`iki dosya görüntüler. `part-r-00000` dosyası bu iş için çıktıyı içerir.

    > [!NOTE]  
    > Bazı MapReduce işleri, sonuçları birden çok **Part-r-#** # # # # dosyasında bölebilir. Bu durumda, dosyaların sırasını göstermek için # # # # # sonekini kullanın.

4. Çıktıyı görüntülemek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Bu komut, **wasb://example/Data/Gutenberg/DaVinci.txt** dosyasında yer alan sözcüklerin listesini ve her sözcüğün kaç kez oluştuğunu görüntüler. Aşağıdaki metin, dosyasında yer alan verilerin bir örneğidir:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Özet

Görebileceğiniz gibi, Hadoop komutları bir HDInsight kümesinde MapReduce işlerini çalıştırmanın kolay bir yolunu sağlar ve sonra iş çıktısını görüntüler.

## <a id="nextsteps"></a>Sonraki adımlar

HDInsight 'ta MapReduce işleri hakkında genel bilgi için:

* [HDInsight Hadoop üzerinde MapReduce kullanma](hdinsight-use-mapreduce.md)

HDInsight 'ta Hadoop ile birlikte çalışmak için kullanabileceğiniz diğer yollar hakkında daha fazla bilgi için:

* [HDInsight üzerinde Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md)
* [HDInsight üzerinde Apache Hadoop Apache Pig kullanma](hdinsight-use-pig.md)

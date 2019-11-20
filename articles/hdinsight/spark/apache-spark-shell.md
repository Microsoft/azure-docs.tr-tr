---
title: Azure HDInsight 'ta etkileşimli Spark kabuğu kullanma
description: Etkileşimli bir Spark kabuğu, Spark komutlarının tek seferde çalıştırılması ve sonuçları görmek için bir okuma-yürütme-yazdırma işlemi sağlar.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 7aac2812787a7c14d99377754a4f85e699ef3f09
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441886"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Spark kabuğu 'ndan Apache Spark çalıştırma

Etkileşimli bir [Apache Spark](https://spark.apache.org/) kabuğu Spark komutlarının tek seferde çalıştırılması ve sonuçları görmesi IÇIN bir REPL (okuma-yürütme-yazdırma döngüsü) ortamı sağlar. Bu işlem, geliştirme ve hata ayıklama için kullanışlıdır. Spark, desteklenen dillerin her biri için bir kabuk sağlar: Scala, Python ve R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>SSH ile Apache Spark Shell 'e edinme

SSH kullanarak kümenin birincil baş düğümüne bağlanarak HDInsight 'ta Apache Spark Shell 'e erişin:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Azure portal kümeniz için tüm SSH komutunu edinebilirsiniz:

1. [Azure Portal](https://portal.azure.com)’da oturum açın.
2. HDInsight Spark kümenizin bölmesine gidin.
3. Secure Shell (SSH) öğesini seçin.

    ![Azure portal HDInsight bölmesi](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Görüntülenmiş SSH komutunu kopyalayın ve terminalinizde çalıştırın.

    ![Azure portal HDInsight SSH bölmesi](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

HDInsight 'a bağlanmak için SSH kullanma hakkında ayrıntılı bilgi için bkz. [HDInsight Ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Apache Spark kabuğu çalıştırma

Spark, Scala (Spark-Shell), Python (pyspark) ve R (mini r) için kabuklar sağlar. HDInsight kümenizin baş düğümündeki SSH oturumunda aşağıdaki komutlardan birini girin:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Artık Spark komutlarını uygun dilde girebilirsiniz.

## <a name="sparksession-and-sparkcontext-instances"></a>Mini oturum ve mini bağlam örnekleri

Spark kabuğunu çalıştırdığınızda varsayılan olarak, sizin için otomatik olarak Mini oturum ve parlak bağlam örnekleri oluşturulur.

Mini erişimli oturum örneğine erişmek için girin `spark`. Mini bağlam örneğine erişmek için girin `sc`.

## <a name="important-shell-parameters"></a>Önemli kabuk parametreleri

Spark Shell komutu (`spark-shell`, `pyspark`veya `sparkR`) birçok komut satırı parametresini destekler. Parametrelerin tam listesini görmek için Spark kabuğunu anahtarla `--help`başlatın. Bu parametrelerin bazılarının yalnızca Spark kabuğu 'nun sarmaladığı için `spark-submit`uygulanabilir olabileceğini unutmayın.

| anahtarı | description | Örnek |
| --- | --- | --- |
| --Master MASTER_URL | Ana URL 'YI belirtir. HDInsight 'ta bu değer her zaman `yarn`olur. | `--master yarn`|
| --jars JAR_LIST | Sürücü ve yürütücü Sınıfyoluna dahil etmek için yerel jar dosyaları dışındaki 'ın virgülle ayrılmış listesi. HDInsight 'ta, bu liste Azure Storage veya Data Lake Storage varsayılan dosya sistemine yönelik yollardan oluşur. | `--jars /path/to/examples.jar` |
| --Packages MAVEN_COORDS | Sürücü ve yürütücü Sınıfyoluna dahil etmek için jar dosyaları dışındaki 'ın Maven koordinatlarının virgülle ayrılmış listesi. Yerel Maven depolarında, Maven orta, ve ile `--repositories`belirtilen diğer uzak depolarda arama yapar. Koordinatlar için biçim *GroupID*:*ArtifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --Kopyala-Dosyalar LISTESI | Yalnızca Python için, PYTHONPATH üzerine yerleştirilecek. zip,. yumurg veya. Kopyala dosyalarının virgülle ayrılmış bir listesi. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure HDInsight 'ta Apache Spark giriş](apache-spark-overview.md) .
- Spark kümeleri ve parlak SQL ile çalışmak için bkz. [Azure HDInsight 'ta Apache Spark kümesi oluşturma](apache-spark-jupyter-spark-sql.md) .
- Spark ile akış verilerini işleyen uygulamalar yazmak için bkz. [Apache Spark yapısal akışı nedir?](apache-spark-streaming-overview.md) .

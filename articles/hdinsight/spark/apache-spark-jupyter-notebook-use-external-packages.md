---
title: Spark-Azure HDInsight 'ta Jupyıter ile özel Maven paketleri kullanma
description: Özel Maven paketleri kullanmak için HDInsight Spark kümeleri ile kullanılabilen jupi not defterlerini yapılandırma hakkında adım adım yönergeler.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 3ddfdfdfe10d5b6ea7c2d5cd99d325564163c0dd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866021"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>HDInsight 'ta Apache Spark kümelerinde jupi Not defterleri ile dış paketleri kullanma

HDInsight 'ta Apache Spark kümesindeki bir [Jupyter Notebook](https://jupyter.org/) , kümede kullanıma hazır olmayan, topluluk tarafından katkıda bulunulan Apache **Maven** paketlerini kullanmak üzere nasıl yapılandıracağınızı öğrenin.

[Maven deposunda](https://search.maven.org/) kullanılabilen paketlerin tüm listesini arayabilirsiniz. Ayrıca, diğer kaynaklardan kullanılabilir paketlerin bir listesini alabilirsiniz. Örneğin, topluluk tarafından katkıda bulunulan paketlerin tam bir listesi [Spark paketlerinde](https://spark-packages.org/)bulunabilir.

Bu makalede, [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketini Jupyter Notebook ile kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

* HDInsight üzerinde Spark ile Jupyter Notebook kullanma bilgisi. Daha fazla bilgi için bkz. [HDInsight üzerinde verileri yükleme ve sorguları çalıştırma Apache Spark](./apache-spark-load-data-run-query.md).

* Kümelerinizin birincil depolama alanı için [URI şeması](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Bu `wasb://` , `abfs://` Azure Data Lake Storage 2. veya Azure Data Lake Storage 1. Için Azure depolama için olacaktır `adl://` . Azure depolama veya Data Lake Storage 2. için güvenli aktarım etkinleştirilirse, URI `wasbs://` veya `abfss://` Ayrıca, [Güvenli aktarım](../../storage/common/storage-require-secure-transfer.md)' ı da görürsünüz.

## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyıter Not defterleri ile dış paketleri kullanma

1. `https://CLUSTERNAME.azurehdinsight.net/jupyter` `CLUSTERNAME` Spark Kümenizin adı olduğu yere gidin.

1. Yeni bir not defteri oluşturun. **Yeni**' yi ve ardından **Spark**' ı seçin.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png " alt-text="Yeni bir Spark Jupyter Notebook oluşturma" border="true":::

1. Yeni bir not defteri oluşturulur ve Untitled.pynb adı ile açılır. Üstteki Not defteri adını seçin ve kolay bir ad girin.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png " alt-text="Not defteri adını belirtme" border="true":::

1. `%%configure`Not defterini harici bir paket kullanacak şekilde yapılandırmak için Magic 'i kullanacaksınız. Dış paketleri kullanan not defterlerinde, `%%configure` ilk kod hücresinde Magic ' i çağırdığınızdan emin olun. Bu, çekirdeğin oturum başlamadan önce paketi kullanacak şekilde yapılandırılmasını sağlar.

    >[!IMPORTANT]  
    >İlk hücrede çekirdeği yapılandırmayı unutursanız, `%%configure` `-f` parametresini parametresiyle kullanabilirsiniz, ancak bu işlem oturumu yeniden başlatır ve tüm ilerleme kaybedilir.

    | HDInsight sürümü | Komut |
    |-------------------|---------|
    | HDInsight 3,5 ve HDInsight için 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |HDInsight 3,3 ve HDInsight için 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. Yukarıdaki kod parçacığı, Maven merkezi deposundaki Dış paketin Maven koordinatlarını bekler. Bu kod parçacığında `com.databricks:spark-csv_2.11:1.5.0` **Spark-CSV** paketi için Maven koordinatı. Bir paket için koordinatları nasıl oluşturabileceğiniz aşağıda açıklanmaktadır.

    a. Maven deposundaki paketi bulun. Bu makalede [Spark-CSV](https://mvnrepository.com/artifact/com.databricks/spark-csv)kullanırız.

    b. Deposundan **GroupID**, **ArtifactId** ve **Version** değerlerini toplayın. Toplamakta olduğunuz değerlerin kümenize eşleştiğinden emin olun. Bu durumda, bir Scala 2,11 ve Spark 1.5.0 paketi kullanıyoruz, ancak kümenizdeki uygun Scala veya Spark sürümü için farklı sürümler seçmeniz gerekebilir. `scala.util.Properties.versionString`Spark Jupyıter çekirdeği üzerinde veya Spark göndermesi üzerinde çalıştırarak, kümenizde Scala sürümünü bulabilirsiniz. `sc.version`Jupi Not defterleri üzerinde çalıştırarak Spark sürümünü kümenizde bulabilirsiniz.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png " alt-text="Jupyter Notebook ile dış paketleri kullanma" border="true":::

    c. İki nokta üst üste (**:**) ayırarak üç değeri birleştirir.

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. Kod hücresini Magic ile çalıştırın `%%configure` . Bu, temel alınan Livy oturumu verdiğiniz paketi kullanacak şekilde yapılandırır. Not defterindeki sonraki hücrelerde, artık paketini aşağıda gösterildiği gibi kullanabilirsiniz.

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    HDInsight 3,4 ve sonraki bir için aşağıdaki kod parçacığını kullanmanız gerekir.

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Daha sonra, önceki adımda oluşturduğunuz veri çerçevesindeki verileri görüntülemek için aşağıda gösterildiği gibi parçacıkları çalıştırabilirsiniz.

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>Ayrıca bkz.

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>Senaryolar

* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Uygulamaları oluşturma ve çalıştırma

* [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Araçlar ve uzantılar

* [HDInsight Linux 'ta Apache Spark kümelerinde Jupyıter Not defterleri ile dış Python paketleri kullanma](apache-spark-python-package-installation.md)
* [Spark Scala uygulamaları oluşturmak ve göndermek amacıyla IntelliJ IDEA için HDInsight Araçları Eklentisini kullanma](apache-spark-intellij-tool-plugin.md)
* [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesinde Jupyter Notebook için kullanılabilir olan kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

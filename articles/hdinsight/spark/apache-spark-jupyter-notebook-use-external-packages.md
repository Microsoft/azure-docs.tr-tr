---
title: Spark-Azure HDInsight 'ta Jupyıter ile özel Maven paketleri kullanma
description: Özel Maven paketleri kullanmak için HDInsight Spark kümeleri ile kullanılabilen jupi not defterlerini yapılandırma hakkında adım adım yönergeler.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 0cdd962e5d027b5576a0556ca5decb976af45ff1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494554"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>HDInsight 'ta Apache Spark kümelerinde jupi Not defterleri ile dış paketleri kullanma

> [!div class="op_single_selector"]
> * [Hücre Magic 'i kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Betik eylemini kullanma](apache-spark-python-package-installation.md)

HDInsight 'ta Apache Spark kümesindeki bir [Jupyter Notebook](https://jupyter.org/) , kümede kullanıma hazır olmayan, topluluk tarafından katkıda bulunulan Apache **Maven** paketlerini kullanmak üzere nasıl yapılandıracağınızı öğrenin. 

[Maven deposunda](https://search.maven.org/) kullanılabilen paketlerin tüm listesini arayabilirsiniz. Ayrıca, diğer kaynaklardan kullanılabilir paketlerin bir listesini alabilirsiniz. Örneğin, topluluk tarafından katkıda bulunulan paketlerin tam bir listesi [Spark paketlerinde](https://spark-packages.org/)bulunabilir.

Bu makalede, Jupyter Not defteri ile [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketini nasıl kullanacağınızı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdakilere sahip olmanız gerekir:

* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter not defterleri ile dış paketleri kullanma

1. [Azure Portal](https://portal.azure.com/)’daki başlangıç panosunda Spark kümenizin kutucuğuna tıklayın (başlangıç panosuna sabitlediyseniz). Ayrıca **Tüm** > **HDInsight Kümelerine Gözat** altından kümenize gidebilirsiniz.

1. Spark kümesi dikey penceresinden **Hızlı Bağlantılar**’a ve sonra **Küme Panosu** dikey penceresinden **Jupyter Not Defteri**’ne tıklayın. İstenirse, küme için yönetici kimlik bilgilerini girin.

    > [!NOTE]  
    > Aşağıdaki URL’yi tarayıcınızda açarak da Jupyter Notebook’a ulaşabilirsiniz. **CLUSTERNAME** değerini kümenizin adıyla değiştirin:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Yeni bir not defteri oluşturun. **Yeni**' ye ve ardından **Spark**' a tıklayın.
   
    ![Yeni bir Spark Jupyter Not defteri oluşturun](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Yeni bir Jupyter not defteri oluşturma")

1. Yeni bir not defteri oluşturulur ve Untitled.pynb adı ile açılır. Üstteki not defteri adına tıklayın ve kolay bir ad girin.
   
    ![Not defteri için bir ad girin](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Not defteri adını belirtme")

1. Not defterini harici bir paket kullanacak şekilde yapılandırmak için `%%configure` Magic kullanacaksınız. Dış paketleri kullanan not defterlerinde, ilk kod hücresinde `%%configure` Magic ' i çağırdığınızdan emin olun. Bu, çekirdeğin oturum başlamadan önce paketi kullanacak şekilde yapılandırılmasını sağlar.

    >[!IMPORTANT]  
    >İlk hücrede çekirdeği yapılandırmayı unutursanız, `%%configure` `-f` parametresiyle kullanabilirsiniz, ancak bu oturum oturumunu yeniden başlatır ve tüm ilerleme kaybedilir.

    | HDInsight sürümü | Komut |
    |-------------------|---------|
    |HDInsight 3,3 ve HDInsight için 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | HDInsight 3,5 ve HDInsight için 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. Yukarıdaki kod parçacığı, Maven merkezi deposundaki Dış paketin Maven koordinatlarını bekler. Bu kod parçacığında, `com.databricks:spark-csv_2.10:1.4.0` **Spark-CSV** paketi için Maven koordinatı ' dir. Bir paket için koordinatları nasıl oluşturabileceğiniz aşağıda açıklanmaktadır.
   
    a. Maven deposundaki paketi bulun. Bu makalede [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)kullanırız.
   
    b. Deposundan **GroupID**, **ArtifactId**ve **Version**değerlerini toplayın. Toplamakta olduğunuz değerlerin kümenize eşleştiğinden emin olun. Bu durumda, bir Scala 2,10 ve Spark 1.4.0 paketi kullanıyoruz, ancak kümenizdeki uygun Scala veya Spark sürümü için farklı sürümler seçmeniz gerekebilir. Spark Jupyıter çekirdeğinde veya Spark gönderme üzerinde `scala.util.Properties.versionString` çalıştırarak, kümeinizdeki Scala sürümünü bulabilirsiniz. Jupyıter Not defterlerindeki `sc.version` çalıştırarak, kümelerinizde Spark sürümünü bulabilirsiniz.
   
    ![Jupyter Not defteri ile dış paketleri kullanma](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Jupyter Not defteri ile dış paketleri kullanma")
   
    c. İki nokta üst üste ( **:** ) ayırarak üç değeri birleştirir.
   
        com.databricks:spark-csv_2.10:1.4.0

1. Kod hücresini `%%configure` Magic ile çalıştırın. Bu, temel alınan Livy oturumu verdiğiniz paketi kullanacak şekilde yapılandırır. Not defterindeki sonraki hücrelerde, artık paketini aşağıda gösterildiği gibi kullanabilirsiniz.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    HDInsight 3,6 için aşağıdaki kod parçacığını kullanmanız gerekir.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Daha sonra, önceki adımda oluşturduğunuz veri çerçevesindeki verileri görüntülemek için aşağıda gösterildiği gibi parçacıkları çalıştırabilirsiniz.

        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Ayrıca bkz.

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
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

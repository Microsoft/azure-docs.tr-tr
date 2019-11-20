---
title: Azure HDInsight üzerinde jupi ile Python paketleri için betik eylemi
description: Betik eyleminin nasıl kullanılacağına ilişkin adım adım yönergeler, HDInsight Spark kümeleri ile birlikte sunulan jupi not defterlerini dış Python paketleri kullanacak şekilde yapılandırma.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: e344035f05e192de1779a60fc99a7e0144566654
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682196"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>HDInsight üzerinde Apache Spark jupi Not defterleri için dış Python paketleri yüklemeye yönelik betik eylemi

> [!div class="op_single_selector"]
> * [Hücre Magic 'i kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Betik eylemini kullanma](apache-spark-python-package-installation.md)

HDInsight 'ta bir [Apache Spark](https://spark.apache.org/) kümesini, kümedeki kullanıma hazır olmayan, topluluk tarafından katkıda bulunulan **Python** paketlerini kullanacak şekilde yapılandırmak için betik eylemlerinin nasıl kullanılacağını öğrenin.

> [!NOTE]  
> Ayrıca, dış paketleri kullanmak için `%%configure` Magic kullanarak bir Jupyter Not defteri yapılandırabilirsiniz. Yönergeler için bkz. [HDInsight 'ta Apache Spark kümelerinde Jupyıter Not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md).

Kullanılabilir paketlerin tüm listesi için [paket dizininde](https://pypi.python.org/pypi) arama yapabilirsiniz. Ayrıca, diğer kaynaklardan kullanılabilir paketlerin bir listesini alabilirsiniz. Örneğin, [Conda-Forge](https://conda-forge.org/feedstocks/)aracılığıyla kullanıma sunulan paketleri yükleyebilirsiniz.

Bu makalede, kümenizin betik eylemini kullanarak [TensorFlow](https://www.tensorflow.org/) paketini yüklemeyi ve örnek olarak Jupyter Not defteri aracılığıyla kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > HDInsight Linux üzerinde henüz bir Spark kümeniz yoksa, küme oluşturma sırasında betik eylemleri çalıştırabilirsiniz. [Özel Betik eylemlerinin kullanımı](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)hakkındaki belgeleri ziyaret edin.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight kümelerinde kullanılan açık kaynaklı yazılım desteği

Microsoft Azure HDInsight hizmeti Apache Hadoop etrafında oluşturulmuş açık kaynaklı teknolojilerin ekosistemini kullanır. Microsoft Azure, açık kaynaklı teknolojiler için genel bir destek düzeyi sağlar. Daha fazla bilgi için bkz. [Azure DESTEĞI SSS Web sitesi](https://azure.microsoft.com/support/faq/). HDInsight hizmeti, yerleşik bileşenler için ek bir destek düzeyi sağlar.

HDInsight hizmetinde bulunan iki tür açık kaynaklı bileşen vardır:

* **Yerleşik bileşenler** -bu bileşenler HDInsight kümelerinde önceden yüklenir ve kümenin temel işlevlerini sağlar. Örneğin, Apache Hadoop YARN ResourceManager, Apache Hive sorgu dili (HiveQL) ve Mahout kitaplığı bu kategoriye aittir. Tüm küme bileşenleri listesi, [HDInsight tarafından sağlanan Apache Hadoop kümesi sürümlerindeki yenilikler](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)bölümünde bulunur.
* **Özel bileşenler** -kümenin bir kullanıcısı olarak, kuruluşunuzda bulunan veya sizin tarafınızdan oluşturulan herhangi bir bileşeni iş yükünüze yükleyebilir veya kullanabilirsiniz.

> [!IMPORTANT]
> HDInsight kümesiyle birlikte sunulan bileşenler tam olarak desteklenmektedir. Microsoft Desteği, bu bileşenlerle ilgili sorunları yalıtmaya ve çözmeye yardımcı olur.
>
> Özel bileşenler, sorunu gidermeye yardımcı olmak için ticari açıdan makul destek alır. Microsoft desteği sorunu çözebiliyor olabilir veya bu teknoloji için derin uzmanlığın bulunduğu açık kaynaklı teknolojiler için kullanılabilir kanalları ister. Örneğin, şu şekilde kullanılabilecek birçok topluluk sitesi vardır: [HDInsight Için MSDN Forumu](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Ayrıca Apache projelerinin [https://apache.org](https://apache.org)proje siteleri vardır, örneğin: [Hadoop](https://hadoop.apache.org/).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter not defterleri ile dış paketleri kullanma

1. [Azure Portal](https://portal.azure.com/)kümenize gidin.  

2. Kümeniz seçiliyken sol bölmeden **Ayarlar**altında **betik eylemleri**' ni seçin.

3. **+ Yeni Gönder**' i seçin.

4. **Betiği gönder eylem** penceresi için aşağıdaki değerleri girin:  

    |Parametre | Değer |
    |---|---|
    |Betik türü | Açılan listeden **özel** ' i seçin.|
    |Ad |Metin kutusuna `tensorflow` girin.|
    |Bash betiği URI 'SI |Metin kutusuna `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` girin. |
    |Düğüm türleri | **Baş**ve **çalışan** onay kutularını seçin. |

    `tensorflowinstall.sh` aşağıdaki komutları içerir:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install -c conda-forge tensorflow
    ```

5. **Oluştur**'u seçin.  [Özel Betik eylemlerinin kullanımı](../hdinsight-hadoop-customize-cluster-linux.md)hakkındaki belgeleri ziyaret edin.

6. Betiğin tamamlanmasını bekleyin.  Komut dosyası **eylemleri** bölmesi, komut dosyası yürütülürken **geçerli küme Işlemi bittikten sonra yeni betik eylemleri gönderilebilir** .  Bir ilerleme çubuğu, ambarı Kullanıcı arabirimi **arka plan işlemleri** penceresinden görüntülenebilir.

7. PySpark Jupyter Not defterini açın.  Adımlar için bkz. [Spark HDInsight 'Ta Jupyter Not defteri oluşturma](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) .

    ![Yeni Jupyter Not defteri oluşturma](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Yeni bir Jupyter not defteri oluşturma")

8. Şimdi bir Merhaba Dünya örneğini `import tensorflow` ve çalıştıracaksınız. Aşağıdaki kodu girin:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Sonuç şöyle görünür:
    
    ![TensorFlow kod yürütme](./media/apache-spark-python-package-installation/tensorflow-execution.png "TensorFlow kodunu yürütme")

> [!NOTE]  
> Kümede iki Python yüklemesi vardır. Spark, `/usr/bin/anaconda/bin` konumunda bulunan Anaconda Python yüklemesini kullanır ve Python 2,7 ortamında varsayılan olarak kullanılır. Python 3. x kullanmak ve PySpark3 çekirdeğine paket yüklemek için, bu ortam için `conda` yürütülebilir dosyasının yolunu kullanın ve ortamı belirtmek için `-n` parametresini kullanın. Örneğin, komut `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`, `conda-forge` kanalını kullanarak Python 3,5 ortamına `ggplot` paketini yüklerse.

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

* [HDInsight 'ta Apache Spark kümelerinde jupi Not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Spark Scala uygulamaları oluşturmak ve göndermek amacıyla IntelliJ IDEA için HDInsight Araçları Eklentisini kullanma](apache-spark-intellij-tool-plugin.md)
* [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

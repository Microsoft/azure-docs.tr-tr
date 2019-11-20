---
title: Azure HDInsight üzerinde çalışan hata ayıklama Apache Spark işleri
description: Azure HDInsight 'ta bir Spark kümesinde çalışan işleri izlemek ve hatalarını ayıklamak için YARN UI, Spark UI ve Spark geçmiş sunucusunu kullanın
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 0e80aa44652efbc58f8259944058aabe59ca5d1a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338474"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Azure HDInsight üzerinde çalışan hata ayıklama Apache Spark işleri

Bu makalede, HDInsight kümelerinde çalışan [Apache Spark](https://spark.apache.org/) işlerin [Apache Hadoop Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI, Spark Kullanıcı arabirimi ve Spark geçmiş sunucusunu kullanarak nasıl izleneceğini ve hata ayıklacağınızı öğreneceksiniz. Spark kümesi ile kullanılabilen bir not defteri kullanarak Spark işi başlatın, **makine öğrenimi: MLLib**kullanarak yiyecek İnceleme verilerinde tahmine dayalı analiz. Diğer herhangi bir yaklaşımı kullanarak gönderdiğiniz bir uygulamayı (örneğin **Spark-gönder**) izlemek için aşağıdaki adımları kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdakilere sahip olmanız gerekir:

* Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).
* Machine Learning ile  **[Not defterini çalıştırmaya başlamalısınız: ](apache-spark-machine-learning-mllib-ipython.md)Mllib**kullanarak yiyecek İnceleme verilerinde tahmine dayalı analiz. Bu Not defterini çalıştırmaya ilişkin yönergeler için bağlantıyı izleyin.  

## <a name="track-an-application-in-the-yarn-ui"></a>YARN Kullanıcı arabiriminde uygulama izleme

1. YARN Kullanıcı arabirimini başlatın. **Küme panoları**altında **Yarn** ' ye tıklayın.

    ![Azure portal YARN Kullanıcı arabirimini başlatın](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Alternatif olarak, Ayrıca, ambarı kullanıcı arabiriminden YARN Kullanıcı arabirimini de başlatabilirsiniz. Ambarı Kullanıcı arabirimini başlatmak için, **küme panoları**altında, **ambarı giriş** ' e tıklayın. Ambarı kullanıcı arabiriminden, **Yarn**' ye tıklayın, **hızlı bağlantılar**' a tıklayın, etkin kaynak yöneticisi ve ardından **Kaynak Yöneticisi Kullanıcı arabirimi**' ne tıklayın.

2. Jupiter not defterlerini kullanarak Spark işini başlattığınız için, uygulama **remoteparlak mıknatıcs** adına sahiptir (Bu, not defterlerinden başlatılan tüm uygulamaların adıdır). İş hakkında daha fazla bilgi edinmek için uygulama adına karşı uygulama KIMLIĞI ' ne tıklayın. Bu, uygulama görünümünü başlatır.

    ![Spark geçmiş sunucusu Spark uygulama KIMLIĞINI bul](./media/apache-spark-job-debugging/find-application-id1.png)

    Jupyter not defterlerinden başlatılan uygulamalar için, Not defterinizden çıkana kadar durum her zaman **çalışır** .
3. Uygulama görünümünden, uygulamayla ve günlüklerle (stdout/stderr) ilişkili kapsayıcıları bulmak için daha fazla ayrıntıya gidebilirsiniz. Spark Kullanıcı arabirimini aşağıda gösterildiği gibi **Izleme URL**'sine karşılık gelen bağlamaya tıklayarak da başlatabilirsiniz.

    ![Spark geçmiş sunucusu kapsayıcı günlüklerini indirme](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Spark Kullanıcı arabirimindeki bir uygulamayı izleme

Spark Kullanıcı arabiriminde, daha önce başlattığınız uygulama tarafından oluşturulan Spark işlerinin detayına gidebilirsiniz.

1. Spark Kullanıcı arabirimini başlatmak için, uygulama görünümünden, yukarıdaki ekran yakalama bölümünde gösterildiği gibi **izleme URL 'sine**karşı bağlantıya tıklayın. Jupyter not defterinde çalışan uygulama tarafından başlatılan Spark işlerinin tümünü görebilirsiniz.

    ![Spark geçmiş sunucusu işleri sekmesi](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Her bir yürütücü için işleme ve depolama bilgilerini görmek üzere **Yürüticileri** sekmesine tıklayın. Ayrıca, **Iş parçacığı dökümü** bağlantısına tıklayarak çağrı yığınını da alabilirsiniz.

    ![Spark geçmiş sunucu yürüticileri sekmesi](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Uygulamayla ilişkili aşamaları görmek için **aşamalar** sekmesine tıklayın.

    ![Spark geçmiş sunucusu aşamaları sekmesi](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Spark aşamalarını görüntüle")

    Her aşamada, aşağıda gösterildiği gibi, yürütme istatistiklerini görüntüleyebileceğiniz birden fazla görev olabilir.

    ![Spark geçmiş sunucusu aşamaları sekme ayrıntıları](./media/apache-spark-job-debugging/view-spark-stages-details.png "Görünüm Spark aşamaları ayrıntıları")

4. Aşama ayrıntıları sayfasında, DAG görselleştirmesini başlatabilirsiniz. Aşağıda gösterildiği gibi sayfanın üst kısmındaki **dag görselleştirme** bağlantısını genişletin.

    ![Spark aşamaları DAG görselleştirmesini görüntüle](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG veya Direct Aclyic Graph, uygulamadaki farklı aşamaları temsil eder. Grafikteki her mavi kutu, uygulamadan çağrılan bir Spark işlemini temsil eder.

5. Aşama ayrıntıları sayfasında, uygulama zaman çizelgesi görünümünü de başlatabilirsiniz. Sayfanın üst kısmındaki **olay zaman çizelgesi** bağlantısını aşağıda gösterildiği gibi genişletin.

    ![Spark aşamaları olay zaman çizelgesini görüntüle](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Bu, Spark olaylarını bir zaman çizelgesi biçiminde görüntüler. Zaman çizelgesi görünümü, işler arasında, iş içinde ve bir aşamada olmak üzere üç düzeyde kullanılabilir. Yukarıdaki görüntü, belirli bir aşama için zaman çizelgesi görünümünü yakalar.

   > [!TIP]  
   > **Yakınlaştırmayı Etkinleştir** onay kutusunu seçerseniz, zaman çizelgesi görünümünde sola ve sağa kaydırma yapabilirsiniz.

6. Spark Kullanıcı arabirimindeki diğer sekmeler Spark örneği hakkında da yararlı bilgiler sağlar.

   * Depolama sekmesi-uygulamanız bir RDDs oluşturursa, bunlar hakkında, depolama alanı sekmesinden bilgi edinebilirsiniz.
   * Ortam sekmesi-Bu sekme, Spark örneğiniz hakkında çok yararlı bilgiler sağlar:
     * Scala sürümü
     * Kümeyle ilişkili olay günlüğü dizini
     * Uygulamanın yürütücü çekirdekleri sayısı
     * Etc.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Spark geçmiş sunucusunu kullanarak tamamlanan işler hakkında bilgi bulma

Bir iş tamamlandıktan sonra, işle ilgili bilgiler Spark geçmiş sunucusunda kalıcıdır.

1. Spark geçmiş sunucusunu başlatmak için genel bakış dikey penceresinde, **küme panoları**altında **Spark geçmiş sunucusu** ' na tıklayın.

    ![Spark geçmişi sunucusunu başlatmak Azure Portal](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spark geçmişini Başlat Sunucu1")

   > [!TIP]  
   > Alternatif olarak, aynı zamanda, ambarı kullanıcı arabiriminden Spark geçmiş sunucusu kullanıcı arabirimini de başlatabilirsiniz. Ambarı Kullanıcı arabirimini başlatmak için genel bakış dikey penceresinden, **küme panoları**altında, **ambarı giriş** ' e tıklayın. Ambarı kullanıcı arabiriminden **Spark**' a, **hızlı bağlantılar**' a ve ardından **Spark geçmiş sunucusu kullanıcı arabirimi**' ne tıklayın.

2. Listelenen tüm tamamlanmış uygulamaları görürsünüz. Daha fazla bilgi için bir uygulamanın ayrıntılarına gitmek üzere bir uygulama KIMLIĞINE tıklayın.

    ![Spark geçmiş sunucusu tamamlanan uygulamalar](./media/apache-spark-job-debugging/view-completed-applications.png "Spark geçmişini Başlat Sunucu2")

## <a name="see-also"></a>Ayrıca bkz.

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [Genişletilmiş Spark geçmiş sunucusunu kullanarak Apache Spark Işlerinde hata ayıklama](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Veri analistleri için

* [Machine Learning Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: Yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight 'ta Apache Spark kullanarak Application Insight telemetri veri analizi](apache-spark-analyze-application-insight-logs.md)
* [Dağıtılmış derin öğrenme için Azure HDInsight Spark Caffe kullanma](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Spark geliştiricileri için

* [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)
* [Spark Scala uygulamaları oluşturmak ve göndermek amacıyla IntelliJ IDEA için HDInsight Araçları Eklentisini kullanma](apache-spark-intellij-tool-plugin.md)
* [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

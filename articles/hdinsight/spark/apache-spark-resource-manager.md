---
title: Azure HDInsight 'ta Apache Spark kümesi için kaynakları yönetme
description: Daha iyi performans için Azure HDInsight 'ta Spark kümeleri için kaynakları yönetme ' nin nasıl kullanılacağını öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: ac0109ff8c5dd7f6013acefbe5ee08a13494cb77
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71001718"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight 'ta Apache Spark kümesi için kaynakları yönetme 

[Apache ambarı](https://ambari.apache.org/) Kullanıcı arabirimi, [Apache Hadoop Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Kullanıcı arabirimi ve [Apache Spark](https://spark.apache.org/) kümeniz ile ilişkili Spark geçmiş sunucusu gibi arabirimlere erişmeyi ve en iyi performans için küme yapılandırmasını ayarlamayı öğrenin.

**Ön koşullar:**

* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Ambarı Web Kullanıcı arabirimini açın

Apache ambarı, kümeyi izlemek ve yapılandırma değişiklikleri yapmak için kullanılır. Daha fazla bilgi için bkz [. HDInsight 'ta Apache Hadoop kümelerini yönetme Azure Portal kullanarak](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Spark geçmiş sunucusunu açın

Spark geçmiş sunucusu, tamamlanan ve Spark uygulamalarının çalıştırıldığı Web Kullanıcı arabirimi. Spark 'ın Web Kullanıcı arabiriminin bir uzantısıdır.

**Spark geçmiş sunucusu Web Kullanıcı arabirimini açmak için**

1. [Azure Portal](https://portal.azure.com/)Spark kümesini açın. Daha fazla bilgi için bkz. [kümeleri listeleme ve gösterme](../hdinsight-administer-use-portal-linux.md#showClusters).
2. **Hızlı bağlantılardan** **küme panosu**' na ve ardından **Spark geçmiş sunucusu** ' na tıklayın.

    ![Spark geçmiş sunucusu](./media/apache-spark-resource-manager/launch-history-server.png "Spark geçmiş sunucusu")

    İstendiğinde Spark kümesi için yönetici kimlik bilgilerini girin. Spark geçmiş sunucusunu aşağıdaki URL 'ye göz atarak da açabilirsiniz:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    @No__t_0 Spark kümeniz adınızla değiştirin.

Spark geçmiş sunucusu Web Kullanıcı arabirimi şöyle görünür:

![HDInsight Spark geçmiş sunucusu](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Yarn Kullanıcı arabirimini açın

Şu anda Spark kümesinde çalışmakta olan uygulamaları izlemek için YARN Kullanıcı arabirimini kullanabilirsiniz.

1. [Azure Portal](https://portal.azure.com/)Spark kümesini açın. Daha fazla bilgi için bkz. [kümeleri listeleme ve gösterme](../hdinsight-administer-use-portal-linux.md#showClusters).
2. **Hızlı bağlantılardan** **küme panosu**' na ve ardından **Yarn**' ye tıklayın.

    ![YARN Kullanıcı arabirimini Başlat](./media/apache-spark-resource-manager/hdi-launch-apache-yarn.png)

   > [!TIP]  
   > Alternatif olarak, Ayrıca, ambarı kullanıcı arabiriminden YARN Kullanıcı arabirimini de başlatabilirsiniz. Ambarı Kullanıcı arabirimini başlatmak için **küme panosu**' na ve ardından **HDInsight küme panosu**' na tıklayın. Ambarı kullanıcı arabiriminden, **Yarn**' ye tıklayın, **hızlı bağlantılar**' a tıklayın, etkin kaynak yöneticisi ve ardından **Kaynak Yöneticisi Kullanıcı arabirimi**' ne tıklayın.

## <a name="optimize-clusters-for-spark-applications"></a>Spark uygulamaları için kümeleri iyileştirme

Uygulama gereksinimlerine bağlı olarak Spark yapılandırması için kullanılabilecek üç temel parametre `spark.executor.instances`, `spark.executor.cores` ve `spark.executor.memory`. Yürütücü, Spark uygulaması için başlatılan bir işlemdir. Çalışan düğümünde çalışır ve uygulama için görevleri yürütmekten sorumludur. Her kümenin varsayılan yürütmelerin sayısı ve yürütücü boyutları, çalışan düğümlerinin sayısı ve çalışan düğüm boyutu temel alınarak hesaplanır. Bu bilgiler, küme baş düğümlerinde `spark-defaults.conf` depolanır.

Üç yapılandırma parametresi küme düzeyinde yapılandırılabilir (küme üzerinde çalışan tüm uygulamalar için) veya her bir uygulama için de belirlenebilir.

### <a name="change-the-parameters-using-ambari-ui"></a>Ambarı Kullanıcı arabirimini kullanarak parametreleri değiştirme
1. Ambarı kullanıcı arabiriminden **Spark**' a tıklayın, **configs**' ye tıklayın ve ardından **özel Spark-varsayılanlar**' ı genişletin.

    ![Ambarı özel kullanarak parametreleri ayarlama](./media/apache-spark-resource-manager/set-parameters-using-ambari.png "Ambarı özel kullanarak parametreleri ayarlama")
2. Varsayılan değerler, küme üzerinde aynı anda dört Spark uygulamasının çalışması için uygundur. Aşağıdaki ekran görüntüsünde gösterildiği gibi kullanıcı arabiriminden bu değerleri değiştirebilirsiniz:

    ![Ambarı kullanarak parametreleri ayarlama](./media/apache-spark-resource-manager/set-executor-parameters.png "Ambarı kullanarak parametreleri ayarlama")

3. Yapılandırma değişikliklerini kaydetmek için **Kaydet** ' e tıklayın. Sayfanın üst kısmında, etkilenen tüm hizmetleri yeniden başlatmanız istenir. **Yeniden Başlat**'a tıklayın.

    ![Hizmetleri yeniden Başlat](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter Not defteri 'nde çalışan bir uygulamanın parametrelerini değiştirme
Jupyter not defterinde çalışan uygulamalar için, yapılandırma değişikliğini yapmak üzere `%%configure` Magic ' i kullanabilirsiniz. İdeal olarak, ilk kod hücresini çalıştırmadan önce bu değişiklikleri uygulamanın başlangıcında yapmanız gerekir. Bunun yapılması, yapılandırmanın, oluşturulduğu zaman, uygun bir oturuma uygulanmasını sağlar. Uygulamada sonraki bir aşamada yapılandırmayı değiştirmek istiyorsanız `-f` parametresini kullanmanız gerekir. Ancak bunu yaparak uygulamadaki tüm ilerleme durumu kaybedilir.

Aşağıdaki kod parçacığında, Jupyıter 'da çalışan bir uygulama için yapılandırmanın nasıl değiştirileceği gösterilmektedir.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Yapılandırma parametrelerinin, örnek sütununda gösterildiği gibi, bir JSON dizesi olarak geçirilmesi ve Magic 'in sonraki satırında olması gerekir.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Spark-gönder kullanılarak gönderilen bir uygulama için parametreleri değiştirme
Aşağıdaki komut, `spark-submit` kullanılarak gönderilen bir Batch uygulamasının yapılandırma parametrelerinin nasıl değiştirileceği hakkında bir örnektir.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Kıvrımlı kullanılarak gönderilen bir uygulama için parametreleri değiştirme
Aşağıdaki komut, kıvrımlı kullanılarak gönderilen toplu uygulama için yapılandırma parametrelerinin nasıl değiştirileceği hakkında bir örnektir.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Spark Thrift sunucusunda bu parametreleri değiştirme
Spark Thrift Server, Spark kümesine JDBC/ODBC erişimi sağlar ve Spark SQL sorgularına hizmet vermek için kullanılır. Power BI, Tableau vb. gibi araçlar Spark SQL sorgularını Spark uygulaması olarak yürütmek üzere Spark Thrift sunucusuyla iletişim kurmak için ODBC protokolünü kullanın. Bir Spark kümesi oluşturulduğunda, her bir baş düğümde bir tane olmak üzere Spark Thrift sunucusu 'nun iki örneği başlatılır. Her Spark Thrift sunucusu, YARN Kullanıcı arabiriminde Spark uygulaması olarak görülebilir.

Spark Thrift sunucusu Spark dinamik yürütücü ayırmayı kullanır ve bu nedenle `spark.executor.instances` kullanılmaz. Bunun yerine, Spark Thrift sunucusu `spark.dynamicAllocation.minExecutors` ve `spark.dynamicAllocation.maxExecutors` kullanarak yürütücü sayısını belirtir. @No__t_0 ve `spark.executor.memory` yapılandırma parametreleri, yürütücü boyutunu değiştirmek için kullanılır. Aşağıdaki adımlarda gösterildiği gibi bu parametreleri değiştirebilirsiniz:

* @No__t_1, `spark.dynamicAllocation.maxExecutors` ve `spark.executor.memory` parametreleri güncelleştirmek için **Advanced Spark-Thrift-parlak conf** kategorisini genişletin.

    ![Spark Thrift sunucusunu yapılandırma](./media/apache-spark-resource-manager/spark-thrift-server-1.png "Spark Thrift sunucusunu yapılandırma")
* @No__t_1 parametresini güncelleştirmek için **Custom Spark-Thrift-parlak conf** kategorisini genişletin.

    ![Spark Thrift sunucu parametresini yapılandırma](./media/apache-spark-resource-manager/spark-thrift-server-2.png "Spark Thrift sunucu parametresini yapılandırma")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Spark Thrift sunucusunun sürücü belleğini değiştirme
Spark Thrift sunucu sürücüsü belleği, baş düğümün Toplam RAM boyutu 14 GB 'den büyük olduğundan, baş düğüm RAM boyutunun %25 ' i olarak yapılandırılır. Aşağıdaki ekran görüntüsünde gösterildiği gibi, sürücü belleği yapılandırmasını değiştirmek için, ambarı Kullanıcı arabirimini kullanabilirsiniz:

* Ambarı kullanıcı arabiriminden **Spark**' a tıklayın, **configs**' ye tıklayın, **Advanced Spark-env**' i genişletin ve ardından **spark_thrift_cmd_opts**değerini girin.

    ![Spark Thrift Server RAM 'ini yapılandırma](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Spark küme kaynaklarını geri kazanma
Spark dinamik ayırma nedeniyle, yalnızca Thrift sunucusu tarafından tüketilen kaynaklar iki uygulama ana öğesinin kaynaklarıdır. Bu kaynakları geri kazanmak için küme üzerinde çalışan Thrift sunucu hizmetlerini durdurmanız gerekir.

1. Ambarı kullanıcı arabiriminden, sol bölmeden **Spark**' a tıklayın.
2. Sonraki sayfada **Spark Thrift sunucuları**' na tıklayın.

    ![Yeniden başlatma Sunucu1](./media/apache-spark-resource-manager/restart-thrift-server-1.png "Yeniden başlatma Sunucu1")
3. Spark Thrift sunucusunun çalıştığı iki headnode görmeniz gerekir. Yayın düğümlerinden birine tıklayın.

    ![Yeniden başlatma, Sunucu2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Yeniden başlatma, Sunucu2")
4. Sonraki sayfada, o headnode üzerinde çalışan tüm hizmetler listelenir. Listeden Spark Thrift Server ' ın yanındaki açılan düğmeye tıklayın ve ardından **Durdur**' a tıklayın.

    ![Yeniden başlatma Thrift Server3](./media/apache-spark-resource-manager/restart-thrift-server-3.png "Yeniden başlatma Thrift Server3")
5. Bu adımları diğer baş düğümüne üzerinde de yineleyin.

## <a name="restart-the-jupyter-service"></a>Jupyıter hizmetini yeniden başlatma
Makalenin başlangıcında gösterildiği gibi, ambarı Web Kullanıcı arabirimini başlatın. Sol gezinti bölmesinden **jupi**' ye tıklayın, **hizmet eylemleri**' ne tıklayın ve ardından **Tümünü Yeniden Başlat**' a tıklayın. Bu, Jupyıter hizmetini tüm yayın düğümlerinde başlatır.

![Jupyıter 'ı yeniden Başlat](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Jupyter’i yeniden başlatın")

## <a name="monitor-resources"></a>Kaynakları izleme
Makalenin başlangıcında gösterildiği gibi Yarn Kullanıcı arabirimini başlatın. Ekranın üstündeki küme ölçümleri tablosunda, **kullanılan bellek** ve **bellek toplam** sütunları değerlerini denetleyin. İki değer yakınsa, bir sonraki uygulamayı başlatmak için yeterli kaynak bulunmayabilir. Aynı, **kullanılan sanal çekirdekler** ve **sanal çekirdekler** için de geçerlidir. Ayrıca, ana görünümde, **kabul edilmiş** durumda olan ve **çalışan** ya da **başarısız** durumuna geçmemiş bir uygulama varsa, bu, başlamak için yeterli kaynak bulunmadığını de ifade eder.

![Kaynak sınırı](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Kaynak sınırı")

## <a name="kill-running-applications"></a>Çalışan uygulamaları Sonlandır
1. Yarn Kullanıcı arabiriminde sol bölmede, **çalışıyor**' a tıklayın. Çalışan uygulamalar listesinden, sonlandırılacak uygulamayı belirleme ve **kimliğe**tıklama.

    ![KILL APP1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "KILL APP1")

2. Sağ üst köşedeki **Uygulamayı Sonlandır** ' a ve ardından **Tamam**' a tıklayın.

    ![KILL app2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "KILL app2")

## <a name="see-also"></a>Ayrıca bkz.
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Veri analistleri için

* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight 'ta Apache Spark kullanarak Application Insight telemetri veri analizi](apache-spark-analyze-application-insight-logs.md)
* [Dağıtılmış derin öğrenme için Azure HDInsight Spark Caffe kullanma](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Apache Spark geliştiricileri için

* [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)
* [Spark Scala uygulamaları oluşturmak ve göndermek amacıyla IntelliJ IDEA için HDInsight Araçları Eklentisini kullanma](apache-spark-intellij-tool-plugin.md)
* [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

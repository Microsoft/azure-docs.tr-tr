---
title: Azure HDInsight 'ta Apache Spark kümesi için kaynakları yönetme
description: Daha iyi performans için Azure HDInsight 'ta Spark kümelerine yönelik kaynakları yönetmeyi öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/12/2021
ms.openlocfilehash: 7027e4af9c11db628990d42430c6877340de3534
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868452"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight 'ta Apache Spark kümesi için kaynakları yönetme

[Apache ambarı](https://ambari.apache.org/) Kullanıcı arabirimi, [Apache Hadoop Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Kullanıcı arabirimi ve [Apache Spark](https://spark.apache.org/) kümeniz ile ilişkili [Spark geçmiş sunucusu](./apache-azure-spark-history-server.md) gibi arabirimlere erişmeyi ve en iyi performans için küme yapılandırmasını ayarlamayı öğrenin.

## <a name="open-the-spark-history-server"></a>Spark geçmiş sunucusunu açın

Spark geçmiş sunucusu, tamamlanan ve Spark uygulamalarının çalıştırıldığı Web Kullanıcı arabirimi. Spark 'ın Web Kullanıcı arabiriminin bir uzantısıdır. Tüm bilgiler için bkz. [Spark geçmiş sunucusu](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Yarn Kullanıcı arabirimini açın

Şu anda Spark kümesinde çalışmakta olan uygulamaları izlemek için YARN Kullanıcı arabirimini kullanabilirsiniz.

1. [Azure Portal](https://portal.azure.com/)Spark kümesini açın. Daha fazla bilgi için bkz. [kümeleri listeleme ve gösterme](../hdinsight-administer-use-portal-linux.md#showClusters).

2. **Küme panolarından** **Yarn**' yi seçin. İstendiğinde Spark kümesi için yönetici kimlik bilgilerini girin.

    :::image type="content" source="./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png" alt-text="YARN Kullanıcı arabirimini Başlat" border="true":::

   > [!TIP]  
   > Alternatif olarak, Ayrıca, ambarı kullanıcı arabiriminden YARN Kullanıcı arabirimini de başlatabilirsiniz. Ambarı kullanıcı arabiriminden, **Yarn**  >  **hızlı bağlantılar**  >  **etkin**  >  **Kaynak Yöneticisi Kullanıcı arabirimine** gidin.

## <a name="optimize-clusters-for-spark-applications"></a>Spark uygulamaları için kümeleri iyileştirme

Uygulama gereksinimlerine bağlı olarak Spark yapılandırması için kullanılabilecek üç temel parametre `spark.executor.instances` , ve ' dir `spark.executor.cores` `spark.executor.memory` . Yürütücü, Spark uygulaması için başlatılan bir işlemdir. Çalışan düğümünde çalışır ve uygulama için görevleri yürütmekten sorumludur. Her kümenin varsayılan yürütmelerin sayısı ve yürütücü boyutları, çalışan düğümlerinin sayısı ve çalışan düğüm boyutu temel alınarak hesaplanır. Bu bilgiler, ' de `spark-defaults.conf` küme baş düğümlerinde depolanır.

Üç yapılandırma parametresi küme düzeyinde yapılandırılabilir (küme üzerinde çalışan tüm uygulamalar için) veya her bir uygulama için de belirlenebilir.

### <a name="change-the-parameters-using-ambari-ui"></a>Ambarı Kullanıcı arabirimini kullanarak parametreleri değiştirme

1. Ambarı kullanıcı arabiriminden **Spark2**  >  **configs**  >  **Custom Spark2-Defaults** adresine gidin.

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png " alt-text="Ambarı özel kullanarak parametreleri ayarlama" border="true":::

1. Varsayılan değerler, küme üzerinde aynı anda dört Spark uygulamasının çalışması için uygundur. Aşağıdaki ekran görüntüsünde gösterildiği gibi kullanıcı arabiriminden bu değerleri değiştirebilirsiniz:

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png " alt-text="Ambarı kullanarak parametreleri ayarlama" border="true":::

1. Yapılandırma değişikliklerini kaydetmek için **Kaydet** ' i seçin. Sayfanın üst kısmında, etkilenen tüm hizmetleri yeniden başlatmanız istenir. **Yeniden Başlat**' ı seçin.

    :::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-restart-services.png" alt-text="Hizmetleri yeniden Başlat" border="true":::

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter Notebook çalıştıran bir uygulamanın parametrelerini değiştirme

Jupyter Notebook çalışan uygulamalar için, `%%configure` yapılandırma değişikliklerini yapmak için Magic ' i kullanabilirsiniz. İdeal olarak, ilk kod hücresini çalıştırmadan önce bu değişiklikleri uygulamanın başlangıcında yapmanız gerekir. Bunun yapılması, yapılandırmanın, oluşturulduğu zaman, uygun bir oturuma uygulanmasını sağlar. Uygulamada sonraki bir aşamada yapılandırmayı değiştirmek istiyorsanız parametresini kullanmanız gerekir `-f` . Ancak bunu yaparak uygulamadaki tüm ilerleme durumu kaybedilir.

Aşağıdaki kod parçacığında, Jupyıter 'da çalışan bir uygulama için yapılandırmanın nasıl değiştirileceği gösterilmektedir.

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

Yapılandırma parametrelerinin, örnek sütununda gösterildiği gibi, bir JSON dizesi olarak geçirilmesi ve Magic 'in sonraki satırında olması gerekir.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Spark-gönder kullanılarak gönderilen bir uygulama için parametreleri değiştirme

Aşağıdaki komut kullanılarak gönderilen toplu uygulama için yapılandırma parametrelerinin nasıl değiştirileceği hakkında bir örnektir `spark-submit` .

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Kıvrımlı kullanılarak gönderilen bir uygulama için parametreleri değiştirme

Aşağıdaki komut, kıvrımlı kullanılarak gönderilen toplu uygulama için yapılandırma parametrelerinin nasıl değiştirileceği hakkında bir örnektir.

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

> [!Note]
> JAR dosyasını küme depolama hesabınıza kopyalayın. JAR dosyasını doğrudan baş düğüme kopyalamayın.

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Spark Thrift sunucusunda bu parametreleri değiştirme

Spark Thrift Server, Spark kümesine JDBC/ODBC erişimi sağlar ve Spark SQL sorgularına hizmet vermek için kullanılır. Power BI, Tableau vb. gibi araçlar, Spark SQL sorgularını Spark uygulaması olarak yürütmek üzere Spark Thrift sunucusuyla iletişim kurmak için ODBC protokolünü kullanın. Bir Spark kümesi oluşturulduğunda, her bir baş düğümde bir tane olmak üzere Spark Thrift sunucusu 'nun iki örneği başlatılır. Her Spark Thrift sunucusu, YARN Kullanıcı arabiriminde Spark uygulaması olarak görülebilir.

Spark Thrift sunucusu Spark dinamik yürütücü ayırmayı kullanır ve bu nedenle `spark.executor.instances` kullanılmaz. Bunun yerine, Spark Thrift sunucusu `spark.dynamicAllocation.maxExecutors` , `spark.dynamicAllocation.minExecutors` yürütücü sayısını belirtmek için ve kullanır. Yapılandırma parametreleri `spark.executor.cores` ve `spark.executor.memory` yürütücü boyutunu değiştirmek için kullanılır. Aşağıdaki adımlarda gösterildiği gibi bu parametreleri değiştirebilirsiniz:

* Parametreleri güncelleştirmek için **Advanced spark2-Thrift-parlak conf** kategorisini genişletin `spark.dynamicAllocation.maxExecutors` `spark.dynamicAllocation.minExecutors` .

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png " alt-text="Spark Thrift sunucusunu yapılandırma" border="true":::

* Parametreleri güncelleştirmek için **Custom spark2-Thrift-parlak conf** kategorisini genişletin `spark.executor.cores` `spark.executor.memory` .

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png " alt-text="Spark Thrift sunucu parametresini yapılandırma" border="true":::

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Spark Thrift sunucusunun sürücü belleğini değiştirme

Spark Thrift sunucu sürücüsü belleği, baş düğümün Toplam RAM boyutu 14 GB 'den büyük olduğundan, baş düğüm RAM boyutunun %25 ' i olarak yapılandırılır. Aşağıdaki ekran görüntüsünde gösterildiği gibi, sürücü belleği yapılandırmasını değiştirmek için, ambarı Kullanıcı arabirimini kullanabilirsiniz:

Ambarı kullanıcı arabiriminden **Spark2**  >  **configs**  >  **Advanced Spark2-env** dizinine gidin. Sonra **spark_thrift_cmd_opts** için değeri sağlayın.

## <a name="reclaim-spark-cluster-resources"></a>Spark küme kaynaklarını geri kazanma

Spark dinamik ayırma nedeniyle, yalnızca Thrift sunucusu tarafından tüketilen kaynaklar iki uygulama ana öğesinin kaynaklarıdır. Bu kaynakları geri kazanmak için küme üzerinde çalışan Thrift sunucu hizmetlerini durdurmanız gerekir.

1. Ambarı kullanıcı arabiriminden sol bölmeden **Spark2**' yi seçin.

2. Sonraki sayfada **Spark2 Thrift sunucuları**' nı seçin.

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png " alt-text="Yeniden başlatma Sunucu1" border="true":::

3. Spark2 Thrift sunucusunun çalıştığı iki headnode görmeniz gerekir. Yayın düğümlerinden birini seçin.

    :::image type="content" source="./media/apache-spark-resource-manager/restart-thrift-server-2.png " alt-text="Yeniden başlatma, Sunucu2" border="true":::

4. Sonraki sayfada, o headnode üzerinde çalışan tüm hizmetler listelenir. Listeden Spark2 Thrift Server ' ın yanındaki açılan düğmeyi seçin ve ardından **Durdur**' u seçin.

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png " alt-text="Yeniden başlatma Thrift Server3" border="true":::
5. Bu adımları diğer baş düğümüne üzerinde de yineleyin.

## <a name="restart-the-jupyter-service"></a>Jupyıter hizmetini yeniden başlatma

Makalenin başlangıcında gösterildiği gibi, ambarı Web Kullanıcı arabirimini başlatın. Sol gezinti bölmesinden **Jupo**' ı seçin, **hizmet eylemleri**' ni seçin ve ardından **Tümünü Yeniden Başlat**' ı seçin. Bu, Jupyıter hizmetini tüm yayın düğümlerinde başlatır.

:::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png " alt-text="Jupyter’i yeniden başlatın" border="true":::

## <a name="monitor-resources"></a>Kaynakları izleme

Makalenin başlangıcında gösterildiği gibi Yarn Kullanıcı arabirimini başlatın. Ekranın üstündeki küme ölçümleri tablosunda, **kullanılan bellek** ve **bellek toplam** sütunları değerlerini denetleyin. İki değer yakınsa, bir sonraki uygulamayı başlatmak için yeterli kaynak bulunmayabilir. Aynı, **kullanılan sanal çekirdekler** ve **sanal çekirdekler** için de geçerlidir. Ayrıca, ana görünümde, **kabul edilmiş** durumda olan ve **çalışan** ya da **başarısız** durumuna geçmemiş bir uygulama varsa, bu, başlamak için yeterli kaynak bulunmadığını de ifade eder.

:::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-resource-limit.png " alt-text="Kaynak sınırı" border="true":::

## <a name="kill-running-applications"></a>Çalışan uygulamaları Sonlandır

1. Yarn Kullanıcı arabiriminde sol panelinden **çalışıyor**' u seçin. Çalışan uygulamalar listesinden, sonlandırılacak uygulamayı belirleyin ve **kimliği** seçin.

    :::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-kill-app1.png " alt-text="KILL APP1" border="true":::

2. Sağ üst köşedeki **Uygulamayı Sonlandır** ' ı seçin ve ardından **Tamam**' ı seçin.

    :::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-kill-app2.png " alt-text="KILL app2" border="true":::

## <a name="see-also"></a>Ayrıca bkz.

* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Veri analistleri için

* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight 'ta Apache Spark kullanarak Application Insight telemetri veri analizi](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Apache Spark geliştiricileri için

* [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)
* [Spark Scala uygulamaları oluşturmak ve göndermek amacıyla IntelliJ IDEA için HDInsight Araçları Eklentisini kullanma](apache-spark-intellij-tool-plugin.md)
* [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesinde Jupyter Notebook için kullanılabilir olan kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyıter Not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

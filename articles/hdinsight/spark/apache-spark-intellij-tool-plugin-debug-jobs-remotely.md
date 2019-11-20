---
title: 'Azure araç seti: uzaktan hata ayıklama Apache Spark uygulamalar-Azure HDInsight'
description: VPN aracılığıyla HDInsight kümelerinde çalışan Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ HDInsight araçlarını kullanmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: ac8ef620948048ae26ef6f408b4bc86b2a2bfbdc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494581"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>VPN aracılığıyla HDInsight 'ta Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ kullanma

[Apache Spark](https://spark.apache.org/) uygulamalarının SSH aracılığıyla uzaktan hata ayıklamasını öneririz. Yönergeler için bkz. [SSH aracılığıyla Azure Toolkit for IntelliJ bir HDInsight kümesindeki Apache Spark uygulamalarında uzaktan hata ayıklama](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Bu makalede, HDInsight Spark kümesinde bir Spark işi göndermek ve sonra masaüstü bilgisayarınızdan uzaktan hata ayıklamak için Azure Toolkit for IntelliJ 'de HDInsight araçlarının nasıl kullanılacağına ilişkin adım adım yönergeler sağlanmaktadır. Bu görevleri tamamlamak için aşağıdaki üst düzey adımları gerçekleştirmeniz gerekir:

1. Siteden siteye veya Noktadan siteye Azure sanal ağı oluşturun. Bu belgedeki adımlarda, siteden siteye ağ kullandığınızı varsayalım.
1. HDInsight 'ta siteden siteye sanal ağın bir parçası olan bir Spark kümesi oluşturun.
1. Küme baş düğümü ve masaüstünüz arasındaki bağlantıyı doğrulayın.
1. IntelliJ FIKRTE bir Scala uygulaması oluşturun ve sonra uzaktan hata ayıklama için yapılandırın.
1. Uygulamayı çalıştırın ve hata ayıklayın.

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Daha fazla bilgi için bkz. [ücretsiz Azure deneme](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)sürümü.
* **HDInsight 'Ta bir Apache Spark kümesi**. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).
* **Oracle Java geliştirme seti**. [Oracle Web sitesinden](https://aka.ms/azure-jdks)yükleyebilirsiniz.
* **IntelliJ fikri**. Bu makalede 2017,1 sürümü kullanılmaktadır. Bunu, [JetBrains Web sitesinden](https://www.jetbrains.com/idea/download/)yükleyebilirsiniz.
* **Azure Toolkit for IntelliJ 'de HDInsight araçları**. IntelliJ için HDInsight araçları Azure Toolkit for IntelliJ bir parçası olarak kullanılabilir. Azure araç seti 'nin nasıl yükleneceğine ilişkin yönergeler için bkz. [ınstall Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **IntelliJ fikrinden Azure aboneliğinizde oturum açın**. [HDInsight kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for IntelliJ kullanın](apache-spark-intellij-tool-plugin.md)' daki yönergeleri izleyin.
* **Özel durum geçici çözümü**. Windows bilgisayarda uzaktan hata ayıklama için Spark Scala uygulamasını çalıştırırken bir özel durum alabilirsiniz. Bu özel durum [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356) ' de açıklanmaktadır ve Windows 'ta eksik bir winutils. exe dosyası nedeniyle oluşur. Bu hatayı geçici olarak çözmek için [yürütülebilir dosyayı](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) **C:\Win, \ bin**gibi bir konuma indirmeniz gerekir. Bir **HADOOP_HOME** ortam değişkeni ekleyin ve sonra değişkenin değerini **C\winutils**olarak ayarlayın.

## <a name="step-1-create-an-azure-virtual-network"></a>1\. Adım: Azure sanal ağı oluşturma

Aşağıdaki bağlantılardan gelen yönergeleri izleyerek bir Azure sanal ağı oluşturun ve ardından Masaüstü bilgisayarınızla sanal ağ arasındaki bağlantıyı doğrulayın:

* [Azure portal kullanarak siteden siteye VPN bağlantısı ile VNet oluşturma](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [PowerShell kullanarak siteden siteye VPN bağlantısı ile VNet oluşturma](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [PowerShell kullanarak bir sanal ağa Noktadan siteye bağlantı yapılandırma](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>2\. Adım: HDInsight Spark kümesi oluşturma

Azure HDInsight 'ta oluşturduğunuz Azure sanal ağının bir parçası olan bir Apache Spark kümesi de oluşturmanızı öneririz. [HDInsight 'Ta Linux tabanlı kümeler oluşturma](../hdinsight-hadoop-provision-linux-clusters.md)bölümünde bulunan bilgileri kullanın. İsteğe bağlı yapılandırmanın bir parçası olarak, önceki adımda oluşturduğunuz Azure sanal ağını seçin.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>3\. Adım: küme baş düğümü ve masaüstünüz arasındaki bağlantıyı doğrulama

1. Baş düğümün IP adresini alın. Küme için ambarı Kullanıcı arabirimini açın. Küme dikey penceresinde **Pano**' yı seçin.

    ![Apache ambarı 'nda Pano seçme](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. Ambarı kullanıcı arabiriminden, **konaklar**' ı seçin.

    ![Apache ambarı 'nda Konakları seçin](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Baş düğümlerin, çalışan düğümlerinin ve Zookeeper düğümlerin bir listesini görürsünüz. Baş düğümlerin bir **hn*** öneki vardır. İlk baş düğümü seçin.

    ![Apache ambarı 'nda baş düğümü bulma](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. Açılan sayfanın alt kısmındaki **Özet** bölmesinden baş düğümün **IP adresini** ve **ana bilgisayar**adını kopyalayın.

    ![Apache ambarı 'nda IP adresini bulma](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Ana düğümün IP adresini ve ana bilgisayar adını, çalıştırmak istediğiniz bilgisayardaki **konaklar** dosyasına ekleyin ve Spark işinde uzaktan hata ayıklayın. Bu, ana bilgisayar adının yanı sıra IP adresini kullanarak baş düğümle iletişim kurmanızı sağlar.

   a. Yükseltilmiş izinlere sahip bir not defteri dosyası açın. **Dosya** menüsünde **Aç**' ı seçin ve ardından Hosts dosyasının konumunu bulun. Bir Windows bilgisayarında, konum **C:\Windows\system32\drivers\etc\hosts**şeklindedir.

   b. Aşağıdaki bilgileri **Hosts** dosyasına ekleyin:

    ```
    # For headnode0
    192.xxx.xx.xx hn0-nitinp
    192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx hn1-nitinp
    192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. HDInsight kümesi tarafından kullanılan Azure sanal ağına bağladığınız bilgisayardan, ana bilgisayar adının yanı sıra IP adresini ve ana bilgisayar adını kullanarak baş düğümlere ping atabildiğinizi doğrulayın.

1. [SSH kullanarak bir HDInsight kümesine bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md)bölümündeki yönergeleri izleyerek küme baş düğümüne bağlanmak için SSH kullanın. Küme baş düğümünden masaüstü bilgisayarın IP adresine ping gönderin. Bilgisayara atanan her iki IP adresiyle bağlantıyı test edin:

    - Biri ağ bağlantısı için
    - Azure sanal ağı için bir tane

1. Diğer baş düğüm için adımları tekrarlayın.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>4\. Adım: Azure Toolkit for IntelliJ HDInsight araçlarını kullanarak Apache Spark Scala uygulaması oluşturma ve uzaktan hata ayıklama için yapılandırma

1. IntelliJ FIKRINI açın ve yeni bir proje oluşturun. **Yeni Proje** iletişim kutusunda aşağıdakileri yapın:

    ![IntelliJ FIKRNDE yeni proje şablonunu seçin](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. **HDInsight** > **HDInsight’ta Spark (Scala)** seçeneğini belirleyin.

    b. **İleri**’yi seçin.
1. Sonraki **Yeni proje** iletişim kutusunda, aşağıdakileri yapın ve ardından **son**' u seçin:

    - Bir proje adı ve konum girin.

    - **Proje SDK’sı** açılır listesinde, Spark 2.x kümesi için **Java 1.8**’i seçin veya Spark 1.x kümesi için **Java 1.7**’yi seçin.

    - **Spark sürümü** açılan listesinde, Scala proje oluşturma SIHIRBAZı Spark SDK ve Scala SDK için uygun sürümü tümleştirir. Spark kümesi sürümü 2.0’dan eskiyse **Spark 1.x** seçeneğini belirleyin. Aksi takdirde, **Spark2.x** seçeneğini belirleyin. Bu örnek, **Spark 2.0.2 (Scala 2.11.8)** kullanır.
  
   ![Proje SDK 'sını ve Spark sürümünü seçin](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Spark projesi sizin için otomatik olarak bir yapı oluşturur. Yapıtı görüntülemek için aşağıdakileri yapın:

    a. **Dosya** menüsünden **Proje Yapısı**’nı seçin.

    b. **Proje yapısı** iletişim kutusunda, oluşturulan varsayılan yapıtı görüntülemek Için **yapıtlar** ' ı seçin. Ayrıca, artı işaretini ( **+** ) seçerek kendi yapıtlarınızı oluşturabilirsiniz.

   ![IntelliJ fıkır yapıları jar oluştur](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Projenize kitaplık ekleyin. Bir kitaplık eklemek için aşağıdakileri yapın:

    a. Proje ağacındaki proje adına sağ tıklayın ve ardından **Modül ayarlarını aç**' ı seçin.

    b. **Proje yapısı** Iletişim kutusunda **Kitaplıklar**' ı seçin, ( **+** ) simgesini seçin ve ardından **Maven**' ı seçin.

    ![IntelliJ fıkır yükleme kitaplığı](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. **Maven deposundan kitaplığı indir** iletişim kutusunda aşağıdaki kitaplıkları arayın ve ekleyin:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. `yarn-site.xml` ve `core-site.xml` küme baş düğümünden kopyalayın ve projeye ekleyin. Dosyaları kopyalamak için aşağıdaki komutları kullanın. Dosyaları küme baş düğümlerinden kopyalamak için aşağıdaki `scp` komutlarını çalıştırmak üzere [Cygwin](https://cygwin.com/install.html) kullanabilirsiniz:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Konak dosyası için küme baş düğüm IP adresini ve ana bilgisayar adlarını masaüstünde zaten ekledik, `scp` komutlarını aşağıdaki şekilde kullanabilirsiniz:

    ```bash
    scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Bu dosyaları projenize eklemek için, bunları proje ağacınızdaki **/src** klasörü altına kopyalayın, örneğin `<your project directory>\src`.

1. `core-site.xml` dosyasını aşağıdaki değişiklikleri yapmak için güncelleştirin:

   a. Şifrelenen anahtarı değiştirin. `core-site.xml` dosyası, kümeyle ilişkili depolama hesabının şifreli anahtarını içerir. Projeye eklediğiniz `core-site.xml` dosyasında, şifrelenen anahtarı varsayılan depolama hesabıyla ilişkili gerçek depolama anahtarıyla değiştirin. Daha fazla bilgi için bkz. [depolama erişim anahtarlarınızı yönetme](../../storage/common/storage-account-manage.md#access-keys).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. `core-site.xml`aşağıdaki girişleri kaldırın:

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Dosyayı kaydedin.

1. Uygulamanız için ana sınıf ekleyin. **Proje Gezgini**'nde **src**öğesine sağ tıklayın, **Yeni**' nin üzerine gelin ve ardından **Scala sınıfı**' nı seçin.

    ![IntelliJ fıkır ana sınıfı seçin](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. **Yeni Scala sınıfı oluştur** iletişim kutusunda bir ad belirtin, **tür** kutusundan **nesne** ' yi seçin ve ardından **Tamam**' ı seçin.

    ![IntelliJ fıkır yeni Scala sınıfı oluştur](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. `MyClusterAppMain.scala` dosyasında aşağıdaki kodu yapıştırın. Bu kod Spark bağlamını oluşturur ve `SparkSample` nesnesinden bir `executeJob` yöntemi açar.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
        ```

1. Repeat steps 8 and 9 to add a new Scala object called `*SparkSample`. Add the following code to this class. This code reads the data from the HVAC.csv (available in all HDInsight Spark clusters). It retrieves the rows that only have one digit in the seventh column in the CSV file, and then writes the output to **/HVACOut** under the default storage container for the cluster.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. `RemoteClusterDebugging`adlı yeni bir sınıf eklemek için 8 ve 9. adımları yineleyin. Bu sınıf, uygulamalarda hata ayıklamak için kullanılan Spark test çerçevesini uygular. `RemoteClusterDebugging` sınıfına aşağıdaki kodu ekleyin:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Dikkat etmeniz gereken birkaç önemli nokta vardır:

      * `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`için, Spark derlemesi JAR 'in belirtilen yoldaki küme depolama alanı üzerinde kullanılabilir olduğundan emin olun.
      * `setJars`için, yapıt JAR 'in oluşturulduğu konumu belirtin. Genellikle `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.

1. `*RemoteClusterDebugging` sınıfında, `test` anahtar sözcüğünü sağ tıklatın ve ardından **RemoteClusterDebugging yapılandırması oluştur**' u seçin.

    ![IntelliJ fıkır uzak yapılandırma oluşturma](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. **RemoteClusterDebugging yapılandırması oluştur** iletişim kutusunda yapılandırma için bir ad girin **ve test** **türü olarak test türü** ' nü seçin. Diğer tüm değerleri varsayılan ayarlar olarak bırakın. **Uygula**’yı ve sonra **Tamam**’ı seçin.

    ![RemoteClusterDebugging yapılandırması oluştur](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Artık menü çubuğunda bir **Uzaktan çalıştırma** yapılandırması açılır listesini görmeniz gerekir.

    ![Uzaktan çalıştırma açılan listesini IntelliJ](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>5\. Adım: uygulamayı hata ayıklama modunda çalıştırma

1. IntelliJ fıkır projenizde, `SparkSample.scala` açın ve `val rdd1`' nin yanında bir kesme noktası oluşturun. Açılır menü **Için kesme noktası oluştur** menüsünde, **executejob işlevinde satır**' ı seçin.

    ![IntelliJ fıkır kesme noktası ekleme](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Uygulamayı çalıştırmak için **Uzaktan çalıştırma** yapılandırması açılan listesinin yanındaki **Hata Ayıkla Çalıştır** düğmesini seçin.

    ![IntelliJ fıkır hata ayıklama çalıştırması düğmesini seçin](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Program yürütme kesme noktasına ulaştığında, alt bölmede bir **hata ayıklayıcı** sekmesi görürsünüz.

    ![IntelliJ fıkır hata ayıklayıcı sekmesini görüntüleme](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Bir izleme eklemek için ( **+** ) simgesini seçin.

    ![IntelliJ hata ayıklama-Add-Watch-değişken](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Bu örnekte, uygulama `rdd1`, değişken oluşturulmadan önce bir sorun oluştu. Bu izlemeyi kullanarak, `rdd`değişkende ilk beş satırı görebiliriz. **Enter** tuşunu seçin.

    ![IntelliJ programı hata ayıklama modunda çalıştır](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Önceki görüntüde gördükleriniz, çalışma zamanında, terabaytlarca veri sorgulayabilir ve uygulamanızın ilerlediği şekilde hata ayıklaması yapabilirsiniz. Örneğin, önceki görüntüde gösterilen çıktıda çıktının ilk satırının bir üst bilgi olduğunu görebilirsiniz. Bu çıktıyı temel alarak, gerekirse üst bilgi satırını atlamak için uygulama kodunuzu değiştirebilirsiniz.

1. Artık uygulama çalıştırmaya devam etmek için **programı sürdürme** simgesini seçebilirsiniz.

    ![IntelliJ fıkır programı özgeçmişi seçin](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Uygulama başarıyla tamamlanerdiğinde, aşağıdakine benzer bir çıktı görmeniz gerekir:

    ![IntelliJ fıkır hata ayıklayıcı konsol çıktısı](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="seealso"></a>Sonraki adımlar

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>Tanıtım

* Scala projesi oluşturma (video): [Apache Spark Scala uygulamaları oluşturma](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Uzaktan hata ayıklama (video): [HDInsight kümesinde Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ kullanma](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Senaryolar

* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak derleme sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Uygulamaları oluşturma ve çalıştırma

* [Scala kullanarak tek başına uygulama oluşturma](../hdinsight-apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Araçlar ve uzantılar

* [HDInsight kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for IntelliJ kullanma](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ kullanarak Apache Spark uygulamalarda SSH aracılığıyla uzaktan hata ayıklayın](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Apache Spark uygulamalar oluşturmak için Azure Toolkit for Eclipse HDInsight araçlarını kullanma](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesinde Jupyter Not defteri için kullanılabilir çekirdekler](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight 'ta Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

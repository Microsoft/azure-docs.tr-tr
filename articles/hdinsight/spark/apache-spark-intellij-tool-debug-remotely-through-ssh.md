---
title: 'Azure Toolkit for IntelliJ: SSH ile Spark uygulamalarında hata ayıklama-HDInsight'
description: HDInsight kümelerini SSH aracılığıyla uzaktan hata ayıklama için Azure Toolkit for IntelliJ 'de HDInsight araçları 'nı kullanma hakkında adım adım yönergeler
keywords: Uzaktan IntelliJ, uzaktan hata ayıklama IntelliJ, SSH, IntelliJ, HDInsight, hata ayıklama IntelliJ, hata ayıklama
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 82aaead87fad0ed9fc7b715baf3dc5ebbd4941be
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494607"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Bir HDInsight kümesindeki Apache Spark uygulamalarda SSH aracılığıyla Azure Toolkit for IntelliJ hata ayıklama

Bu makalede, HDInsight kümesindeki uygulamalarda hata ayıklamak için [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) 'de HDInsight araçlarının nasıl kullanılacağına ilişkin adım adım yönergeler sağlanmaktadır. Projenizde hata ayıklamak için, [Azure Toolkit for IntelliJ video Ile hata ayıklama HDInsight Spark uygulamalarını](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) da görüntüleyebilirsiniz.

**Önkoşullar**
* **Azure Toolkit for IntelliJ 'de HDInsight araçları**. Bu araç Azure Toolkit for IntelliJ bir parçasıdır. Daha fazla bilgi için bkz. [ınstall Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). Ve **Azure Toolkit for IntelliJ**. HDInsight kümesi için Apache Spark uygulamalar oluşturmak için bu araç takımını kullanın. Daha fazla bilgi için bkz. [Use Azure Toolkit for IntelliJ, HDInsight kümesi için Apache Spark uygulamalar oluşturma](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **HDıNSIGHT SSH Service Kullanıcı adı ve parola yönetimi**. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) ve ayrıca [, ambarı Web Kullanıcı arabirimi, Jobhistory, süs Code, Apache Oozie ve diğer Web UG erişimi Için SSH tüneli kullanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Yerel çalıştırma ve hata ayıklamayı gerçekleştirmeyi öğrenin
### <a name="scenario-1-create-a-spark-scala-application"></a>Senaryo 1: Spark Scala uygulaması oluşturma 

1. IntelliJ IDEA’yı başlatın ve sonra bir proje oluşturun. **Yeni Proje** iletişim kutusunda aşağıdakileri yapın:

   a. **Azure Spark/HDInsight**' ı seçin. 

   b. Tercihinize göre bir Java veya Scala şablonu seçin. Aşağıdaki seçenekler arasından seçim yapın:

   - **Spark projesi (Java)**

   - **Spark projesi (Scala)**

   - **Örneklere sahip Spark projesi (Scala)**

   - **Hatalı görev hata ayıklama örnekleri (Önizleme) ile Spark projesi (Scala)**

     Bu örnek **, Samples (Scala) şablonuyla Spark projesi** kullanır.

   c. **Derleme aracı** listesinde, gereksiniminize göre aşağıdakilerden birini seçin:

   - **Maven**, Scala proje oluşturma Sihirbazı desteği için.

   - **SBT**, bağımlılıkları yönetmek ve Scala projesi için oluşturmak için.

     ![IntelliJ yeni proje Spark oluştur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. **İleri**’yi seçin.

1. Sonraki **Yeni proje** penceresinde şunları yapın:

   ![IntelliJ yeni proje Spark sürümü Seç](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Bir proje adı ve proje konumu girin.

   b. **Proje SDK 'sı** açılan listesinde **Spark 2. x** kümesi için **Java 1,8** ' i seçin veya **spark 1. x** kümesi için **Java 1,7** ' yi seçin.

   c. **Spark sürümü** açılan listesinde, Scala proje oluşturma SIHIRBAZı Spark SDK ve Scala SDK için doğru sürümü tümleştirir. Spark küme sürümü 2,0 ' den daha eski ise **Spark 1. x**' i seçin. Aksi halde **Spark 2. x** öğesini seçin. Bu örnek, **Spark 2.0.2 (Scala 2.11.8)** kullanır.

   d. **Son**’u seçin.

1. Kodunuzu projede açmak için **src** > **Main** > **Scala** ' yı seçin. Bu örnek, **SparkCore_wasbloTest** betiğini kullanır.

### <a name="prerequisite-for-windows"></a>Windows için önkoşul
Yerel Spark Scala uygulamasını bir Windows bilgisayarında çalıştırırken [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)' de açıklandığı gibi bir özel durum alabilirsiniz. Windows 'da WinUtils. exe eksik olduğu için özel durum oluşur.

Bu hatayı çözmek için [yürütülebilir dosyayı](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) **C:\Win, \ bin**gibi bir konuma indirin. Ardından, **HADOOP_HOME**ortam değişkenini ekleyin ve değişkenin değerini **c:\winutils**olarak ayarlayın.

### <a name="scenario-2-perform-local-run"></a>Senaryo 2: Yerel çalıştırma gerçekleştirme

1. **SparkCore_wasbloTest** betiğini açın, betik Düzenleyicisi ' ne sağ tıklayın ve ardından yerel çalıştırma gerçekleştirmek için **' [Spark Job] xxx ' Çalıştır** seçeneğini belirleyin.

1. Yerel çalıştırma tamamlandığında, çıkış dosyasını geçerli Proje Gezgini **verilerinize** kaydet **__varsayılan__**  > görebilirsiniz.

    ![IntelliJ projesi yerel çalıştırma sonucu](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Yerel çalıştırma ve yerel hata ayıklama gerçekleştirirken araçlarımız varsayılan yerel çalıştırma yapılandırmasını otomatik olarak ayarladı. Sağ üst köşedeki [ **Spark on HDInsight] xxx** yapılandırmasını açın, **HDInsight üzerinde Apache Spark**' de zaten oluşturulmuş olan **[HDInsight on HDInsight] xxx** 'yi görebilirsiniz. **Yerel olarak çalıştır** sekmesine geçin.

    ![IntelliJ hata ayıklama yapılandırmalarının yerel çalıştırma Çalıştır](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Ortam değişkenleri](#prerequisite-for-windows): **HADOOP_HOME** sistem ortamı değişkenini zaten **c:\winutils**olarak ayarladıysanız, el ile ekleme gereksinimi olmadığını otomatik olarak algılayabilir.
    - [Winutils. exe konumu](#prerequisite-for-windows): sistem ortam değişkenini görmüyorsanız, konumunu düğmesine tıklayarak bulabilirsiniz.
    - Yalnızca iki seçenekten birini seçmeniz yeterlidir ve bunlar MacOS ve Linux 'ta gerekli değildir.

1. Yerel çalıştırma ve yerel hata ayıklama gerçekleştirmeden önce yapılandırmayı el ile de ayarlayabilirsiniz. Önceki ekran görüntüsünde, artı işaretini ( **+** ) seçin. Sonra **HDInsight üzerinde Apache Spark** seçeneğini belirleyin. Kaydedilecek **ad**, **ana sınıf adı** bilgilerini girin ve ardından yerel çalışma düğmesine tıklayın.

### <a name="scenario-3-perform-local-debugging"></a>Senaryo 3: yerel hata ayıklamayı gerçekleştirme
1. **SparkCore_wasbloTest** betiğini açın, kesme noktaları ayarlayın.
1. Betik düzenleyicisine sağ tıklayın ve ardından yerel hata ayıklamayı gerçekleştirmek için **' [Spark on HDInsight] xxx '** seçeneğini belirleyin.

## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Uzaktan çalıştırma ve hata ayıklamayı gerçekleştirmeyi öğrenin
### <a name="scenario-1-perform-remote-run"></a>Senaryo 1: Uzaktan çalıştırma gerçekleştirme

1. **Konfigürasyonları Düzenle** menüsüne erişmek için sağ üst köşedeki simgeyi seçin. Bu menüden, uzaktan hata ayıklama için yapılandırma oluşturabilir veya düzenleyebilirsiniz.

   ![HDI IntelliJ yapılandırma yapılandırması](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png)

1. **Çalıştır/hata ayıkla yapılandırma** iletişim kutusunda artı işaretini ( **+** ) seçin. Sonra **HDInsight üzerinde Apache Spark** seçeneğini belirleyin.

   ![IntelliJ yeni yapılandırma Ekle](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Küme sekmesinde **Uzaktan Çalıştır** ' a geçiş yapın. **ad**, **Spark kümesi**ve **ana sınıf adı**bilgilerini girin. Ardından **Gelişmiş yapılandırma (uzaktan hata ayıklama)** seçeneğine tıklayın. Araçlarımız **Yürüticiler**ile hata ayıklamayı destekler. **Numexectors**, varsayılan değer 5 ' tir. 3 ' ten daha yüksek bir ayarlama yapmanız daha iyidir.

   ![IntelliJ hata ayıklama yapılandırmasını Çalıştır](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. **Gelişmiş yapılandırma (uzaktan hata ayıklama)** bölümünde **Spark uzaktan hata ayıklamayı etkinleştir**' i seçin. SSH kullanıcı adını girin ve bir parola girin veya bir özel anahtar dosyası kullanın. Uzaktan hata ayıklama gerçekleştirmek istiyorsanız, onu ayarlamanız gerekir. Yalnızca uzaktan çalıştırma kullanmak istiyorsanız, bu ayarı ayarlamaya gerek yoktur.

   ![IntelliJ gelişmiş yapılandırma Spark uzaktan hata ayıklamayı etkinleştir](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Yapılandırma artık verdiğiniz adla birlikte kaydedilir. Yapılandırma ayrıntılarını görüntülemek için yapılandırma adını seçin. Değişiklik yapmak için, **konfigürasyonları Düzenle**' yi seçin.

1. Yapılandırma ayarlarını tamamladıktan sonra, projeyi uzak kümede çalıştırabilir veya uzaktan hata ayıklama işlemi yapabilirsiniz.

   ![IntelliJ hata ayıklama uzak Spark Işi uzaktan çalıştırma düğmesi](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Gönderim günlüklerinin sol bölmede görünmediğinden **bağlantıyı kes** düğmesine tıklayın. Ancak, arka uçta hala çalışır.

   ![IntelliJ hata ayıklama uzak Spark Işi uzak çalıştırma sonucu](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Senaryo 2: uzaktan hata ayıklamayı gerçekleştirme
1. Kesme noktaları ayarlayın ve ardından **Uzaktan hata ayıklama** simgesine tıklayın. Uzaktan gönderim farkı, SSH Kullanıcı adı/parolasının yapılandırılması gerektiğidir.

   ![IntelliJ hata ayıklama uzak Spark Işi hata ayıklama simgesi](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Program yürütme, kesme noktasına ulaştığında, **hata ayıklayıcı** bölmesinde bir **sürücü** sekmesi ve iki **yürütücü** sekmesi görürsünüz. Kodu çalıştırmaya devam etmek için **programı Sürdür** simgesini seçin, daha sonra bir sonraki kesme noktasına ulaşır. Hata ayıklama için hedef yürütücüsü bulmak için doğru **yürütücü** sekmesine geçmeniz gerekir. Yürütme günlüklerini ilgili **konsol** sekmesinden görüntüleyebilirsiniz.

   ![IntelliJ hata ayıklama uzak Spark Işi hata ayıklama sekmesi](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Senaryo 3: uzaktan hata ayıklamayı ve hata düzeltmeyi gerçekleştirme

1. İki kesme noktası ayarlayın ve ardından **hata ayıklama** simgesini seçerek uzaktan hata ayıklama işlemini başlatın.

1. Kod, ilk kesme noktasında duraklar ve parametre ve değişken bilgileri **değişkenler** bölmesinde gösterilir.

1. Devam etmek için **programı Sürdür** simgesini seçin. Kod, ikinci noktada duraklar. Özel durum beklendiği gibi yakalandı.

   ![IntelliJ hata ayıklama uzak Spark Işi hata ayıklama hatası](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Programı yeniden **başlatma** simgesini seçin. **HDInsight Spark gönderimi** penceresinde "iş çalıştırma başarısız oldu" hatası görüntülenir.

   ![IntelliJ hata ayıklama uzak Spark Işi hata gönderimi](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. IntelliJ hata ayıklama özelliğini kullanarak değişken değerini dinamik olarak güncelleştirmek için, **Hata Ayıkla** ' yı seçin. **Değişkenler** bölmesi tekrar görünür.

1. **Hata Ayıkla** sekmesinde hedefi sağ tıklatın ve ardından **değeri ayarla**' yı seçin. Sonra, değişken için yeni bir değer girin. Sonra değeri kaydetmek için **ENTER** ' u seçin.

   ![IntelliJ hata ayıklama uzak Spark Iş kümesi değeri](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Programı çalıştırmaya devam etmek için **programı Sürdür** simgesini seçin. Bu kez, hiçbir özel durum yakalanmaz. Projenin özel durum olmadan başarıyla çalıştığını görebilirsiniz.

   ![IntelliJ hata ayıklama uzak Spark Işi özel durum olmadan](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

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
* [VPN aracılığıyla Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ kullanma](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark uygulamalar oluşturmak için Azure Toolkit for Eclipse HDInsight araçlarını kullanma](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme
* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

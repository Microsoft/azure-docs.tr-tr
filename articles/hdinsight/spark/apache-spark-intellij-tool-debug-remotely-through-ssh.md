---
title: 'Azure Toolkit for IntelliJ: SSH ile Spark uygulamalarında hata ayıklama-HDInsight'
description: HDInsight kümelerini SSH aracılığıyla uzaktan hata ayıklama için Azure Toolkit for IntelliJ 'de HDInsight araçları 'nı kullanma hakkında adım adım yönergeler
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: de838e094c8a37d375aa6c7649ee5717705ad33c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866361"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Bir HDInsight kümesindeki Apache Spark uygulamalarda SSH aracılığıyla Azure Toolkit for IntelliJ hata ayıklama

Bu makalede, HDInsight kümesindeki uygulamalarda hata ayıklamak için [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) 'de HDInsight araçlarının nasıl kullanılacağına ilişkin adım adım yönergeler sağlanmaktadır. Projenizde hata ayıklamak için, [Azure Toolkit for IntelliJ video Ile hata ayıklama HDInsight Spark uygulamalarını](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) da görüntüleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Spark kümesi. Bkz. [Apache Spark kümesi oluşturma](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Windows kullanıcıları için: yerel Spark Scala uygulamasını bir Windows bilgisayarında çalıştırırken [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)bölümünde açıklandığı gibi bir özel durum alabilirsiniz. Windows 'da WinUtils.exe eksik olduğu için özel durum oluşur.

    Bu hatayı çözmek için [Winutils.exe](https://github.com/steveloughran/winutils) **C:\Win, \ bin** gibi bir konuma indirin. Ardından **HADOOP_HOME** ortam değişkenini ekleyin ve değişkenin değerini **c:\winutils** olarak ayarlayın.

* [IntelliJ fikir](https://www.jetbrains.com/idea/download/#section=windows) (Community sürümü ücretsizdir.).

* [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [IntelliJ Için Scala eklentisi](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Spark Scala uygulaması oluşturma

1. IntelliJ FIKRINI başlatın ve yeni proje **Oluştur** ' u seçerek **Yeni proje** penceresini açın.

1. Sol bölmeden **Apache Spark/HDInsight** ' ı seçin.

1. Ana penceredeki **Spark projesi örneklerle (Scala)** öğesini seçin.

1. **Yapı aracı** açılan listesinden aşağıdakilerden birini seçin:

    * Scala projesi oluşturma Sihirbazı desteği için **Maven** .
    * Scala projesi için bağımlılıkları ve oluşturmayı yönetmek üzere **SBT** .

     :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png" alt-text="IntelliJ yeni proje Spark oluştur" border="true":::

1. **İleri**’yi seçin.

1. Sonraki **Yeni proje** penceresinde, aşağıdaki bilgileri sağlayın:

    |Özellik |Açıklama |
    |---|---|
    |Proje adı|Bir ad girin. Bu, kullanımları gözden geçir `myApp` .|
    |Proje konumu|Projenizin kaydedileceği istenen konumu girin.|
    |Proje SDK 'Sı|Boşsa, **yeni...** öğesini seçin ve JDK 'nize gidin.|
    |Spark sürümü|Oluşturma Sihirbazı Spark SDK ve Scala SDK için doğru sürümü tümleştirir. Spark kümesi sürümü 2.0’dan eskiyse **Spark 1.x** seçeneğini belirleyin. Aksi halde **Spark 2. x** öğesini seçin. Bu örnek **Spark 2.3.0 (Scala 2.11.8)** kullanır.|

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png" alt-text="IntelliJ yeni proje Spark sürümü Seç" border="true":::

1. **Son**'u seçin. Projenin kullanılabilir hale gelmesi birkaç dakika sürebilir. İlerleme için sağ alt köşeyi izleyin.

1. Projenizi genişletin ve **src**  >  **Main**  >  **Scala**  >  **örneğine** gidin. **SparkCore_WasbIOTest** çift tıklayın.

## <a name="perform-local-run"></a>Yerel çalıştırma gerçekleştir

1. **SparkCore_WasbIOTest** betikten, betik düzenleyicisine sağ tıklayın ve sonra yerel çalıştırma işlemini gerçekleştirmek için **' SparkCore_WasbIOTest ' Çalıştır** seçeneğini belirleyin.

1. Yerel çalıştırma tamamlandığında, çıkış dosyasını geçerli Proje Gezgini **veri**  >  **__varsayılanındaki__** kaydetme dosyasına bakabilirsiniz.

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png" alt-text="IntelliJ projesi yerel çalıştırma sonucu" border="true":::

1. Yerel çalıştırma ve yerel hata ayıklama gerçekleştirirken araçlarımız varsayılan yerel çalıştırma yapılandırmasını otomatik olarak ayarladı. Sağ üst köşedeki [ **Spark on HDInsight] xxx** yapılandırmasını açın, **HDInsight üzerinde Apache Spark**' de zaten oluşturulmuş olan **[HDInsight on HDInsight] xxx** 'yi görebilirsiniz. **Yerel olarak çalıştır** sekmesine geçin.

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png" alt-text="IntelliJ hata ayıklama yapılandırmalarının yerel çalıştırma Çalıştır" border="true":::

    - [Ortam değişkenleri](#prerequisites): **HADOOP_HOME** sistem ortam değişkenini zaten **c:\winutils** olarak ayarladıysanız, el ile ekleme gereksinimi olmadığını otomatik olarak tespit edebilir.
    - [WinUtils.exe konumu](#prerequisites): sistem ortam değişkenini görmüyorsanız, konumunu düğmesine tıklayarak bulabilirsiniz.
    - Yalnızca iki seçenekten birini seçmeniz yeterlidir ve bunlar MacOS ve Linux 'ta gerekli değildir.

1. Yerel çalıştırma ve yerel hata ayıklama gerçekleştirmeden önce yapılandırmayı el ile de ayarlayabilirsiniz. Önceki ekran görüntüsünde, artı işaretini ( **+** ) seçin. Sonra **HDInsight üzerinde Apache Spark** seçeneğini belirleyin. Kaydedilecek **ad**, **ana sınıf adı** bilgilerini girin ve ardından yerel çalışma düğmesine tıklayın.

## <a name="perform-local-debugging"></a>Yerel hata ayıklamayı gerçekleştir

1. **SparkCore_wasbloTest** betiğini açın, kesme noktalarını ayarlayın.

1. Betik düzenleyicisine sağ tıklayın ve ardından yerel hata ayıklamayı gerçekleştirmek için **' [Spark on HDInsight] xxx '** seçeneğini belirleyin.

## <a name="perform-remote-run"></a>Uzaktan çalıştırma gerçekleştir

1.   >  **Yapılandırma yapılandırması Çalıştır...**' a gidin. Bu menüden, uzaktan hata ayıklama için yapılandırma oluşturabilir veya düzenleyebilirsiniz.

1. **Çalıştır/hata ayıkla yapılandırma** iletişim kutusunda artı işaretini ( **+** ) seçin. Sonra **HDInsight üzerinde Apache Spark** seçeneğini belirleyin.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png" alt-text="IntelliJ yeni yapılandırma Ekle" border="true":::

1. Küme sekmesinde **Uzaktan Çalıştır '** a geçiş yapın. **Ad**, **Spark kümesi** ve **ana sınıf adı** bilgilerini girin. Ardından **Gelişmiş yapılandırma (uzaktan hata ayıklama)** seçeneğine tıklayın. Araçlarımız **Yürüticiler** ile hata ayıklamayı destekler. **Numexectors**, varsayılan değer 5 ' tir. 3 ' ten daha yüksek bir ayarlama yapmanız daha iyidir.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png" alt-text="IntelliJ hata ayıklama yapılandırmasını Çalıştır" border="true":::

1. **Gelişmiş yapılandırma (uzaktan hata ayıklama)** bölümünde **Spark uzaktan hata ayıklamayı etkinleştir**' i seçin. SSH kullanıcı adını girin ve bir parola girin veya bir özel anahtar dosyası kullanın. Uzaktan hata ayıklama gerçekleştirmek istiyorsanız, onu ayarlamanız gerekir. Yalnızca uzaktan çalıştırma kullanmak istiyorsanız, bu ayarı ayarlamaya gerek yoktur.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png" alt-text="IntelliJ gelişmiş yapılandırma Spark uzaktan hata ayıklamayı etkinleştir" border="true":::

1. Yapılandırma artık verdiğiniz adla birlikte kaydedilir. Yapılandırma ayrıntılarını görüntülemek için yapılandırma adını seçin. Değişiklik yapmak için, **konfigürasyonları Düzenle**' yi seçin.

1. Yapılandırma ayarlarını tamamladıktan sonra, projeyi uzak kümede çalıştırabilir veya uzaktan hata ayıklama işlemi yapabilirsiniz.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png" alt-text="IntelliJ hata ayıklama uzak Spark Işi uzaktan çalıştırma düğmesi" border="true":::

1. Gönderim günlüklerinin sol bölmede görünmediğinden **bağlantıyı kes** düğmesine tıklayın. Ancak, arka uçta hala çalışır.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png" alt-text="IntelliJ hata ayıklama uzak Spark Işi uzak çalıştırma sonucu" border="true":::

## <a name="perform-remote-debugging"></a>Uzaktan hata ayıklama gerçekleştir

1. Kesme noktaları ayarlayın ve ardından **Uzaktan hata ayıklama** simgesine tıklayın. Uzaktan gönderim farkı, SSH Kullanıcı adı/parolasının yapılandırılması gerektiğidir.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png" alt-text="IntelliJ hata ayıklama uzak Spark Işi hata ayıklama simgesi" border="true":::

1. Program yürütme, kesme noktasına ulaştığında, **hata ayıklayıcı** bölmesinde bir **sürücü** sekmesi ve iki **yürütücü** sekmesi görürsünüz. Kodu çalıştırmaya devam etmek için **programı Sürdür** simgesini seçin, daha sonra bir sonraki kesme noktasına ulaşır. Hata ayıklama için hedef yürütücüsü bulmak için doğru **yürütücü** sekmesine geçmeniz gerekir. Yürütme günlüklerini ilgili **konsol** sekmesinden görüntüleyebilirsiniz.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png" alt-text="IntelliJ hata ayıklama uzak Spark Işi hata ayıklama sekmesi" border="true":::

### <a name="perform-remote-debugging-and-bug-fixing"></a>Uzaktan hata ayıklama ve hata düzeltmeyi gerçekleştirme

1. İki kesme noktası ayarlayın ve ardından **hata ayıklama** simgesini seçerek uzaktan hata ayıklama işlemini başlatın.

1. Kod, ilk kesme noktasında duraklar ve parametre ve değişken bilgileri **değişkenler** bölmesinde gösterilir.

1. Devam etmek için **programı Sürdür** simgesini seçin. Kod, ikinci noktada duraklar. Özel durum beklendiği gibi yakalandı.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png" alt-text="IntelliJ hata ayıklama uzak Spark Işi hata ayıklama hatası" border="true":::

1. Programı yeniden **başlatma** simgesini seçin. **HDInsight Spark gönderimi** penceresinde "iş çalıştırma başarısız oldu" hatası görüntülenir.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png" alt-text="IntelliJ hata ayıklama uzak Spark Işi hata gönderimi" border="true":::

1. IntelliJ hata ayıklama özelliğini kullanarak değişken değerini dinamik olarak güncelleştirmek için, **Hata Ayıkla** ' yı seçin. **Değişkenler** bölmesi tekrar görünür.

1. **Hata Ayıkla** sekmesinde hedefi sağ tıklatın ve ardından **değeri ayarla**' yı seçin. Sonra, değişken için yeni bir değer girin. Sonra değeri kaydetmek için **ENTER** ' u seçin.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png" alt-text="IntelliJ hata ayıklama uzak Spark Iş kümesi değeri" border="true":::

1. Programı çalıştırmaya devam etmek için **programı Sürdür** simgesini seçin. Bu kez, hiçbir özel durum yakalanmaz. Projenin özel durum olmadan başarıyla çalıştığını görebilirsiniz.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png" alt-text="IntelliJ hata ayıklama uzak Spark Işi özel durum olmadan" border="true":::

## <a name="next-steps"></a>Sonraki adımlar

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>Tanıtım

* Scala projesi oluşturma (video): [Apache Spark Scala uygulamaları oluşturma](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Uzaktan hata ayıklama (video): [HDInsight kümesinde Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ kullanma](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Senaryolar

* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak derleme sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Uygulamaları oluşturma ve çalıştırma

* [Scala kullanarak tek başına uygulama oluşturma](./apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Araçlar ve uzantılar

* [HDInsight kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for IntelliJ kullanma](apache-spark-intellij-tool-plugin.md)
* [VPN aracılığıyla Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ kullanma](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Spark uygulamalar oluşturmak için Azure Toolkit for Eclipse HDInsight araçlarını kullanma](./apache-spark-eclipse-tool-plugin.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesinde Jupyter Notebook için kullanılabilir olan kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyıter Not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)
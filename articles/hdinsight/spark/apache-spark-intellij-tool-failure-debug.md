---
title: IntelliJ Azure Toolkit (Önizleme) ile Spark işinde hata ayıklama-HDInsight
description: Uygulamalarda hata ayıklamak için Azure Toolkit for IntelliJ HDInsight araçlarını kullanma kılavuzu
keywords: Uzaktan IntelliJ, uzaktan hata ayıklama IntelliJ, SSH, IntelliJ, HDInsight, hata ayıklama IntelliJ, hata ayıklama
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494597"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Azure Toolkit for IntelliJ Spark iş hata ayıklaması başarısız oldu (Önizleme)

Bu makalede **Spark hata ayıklama** uygulamalarını çalıştırmak için [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) 'de HDInsight araçlarının nasıl kullanılacağına ilişkin adım adım yönergeler sunulmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* [Oracle Java geliştirme seti](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Bu öğretici, Java sürüm 8.0.202 kullanır.
  
* IntelliJ fıkrı. Bu makalede [IntelliJ fikir Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows)kullanılmaktadır.
  
* Azure Toolkit for IntelliJ. Bkz. [Azure Toolkit for IntelliJ yükleme](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* HDInsight kümenize bağlanın. Bkz. [HDInsight kümenize bağlanma](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Depolama Gezgini. Bkz. [indirme Microsoft Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Hata ayıklama şablonuyla proje oluşturma

Hata ayıklamaya devam etmek için bir Spark 2.3.2 projesi oluşturun, bu belgede hata ayıklama örnek dosyası alın.

1. IntelliJ IDEA’yı açın. **Yeni proje** penceresini açın.

   a. Sol bölmeden **Azure Spark/HDInsight** ' ı seçin.

   b. Ana pencereden **hata hata ayıklama örneği (Önizleme) (Scala) Ile Spark projesi** öğesini seçin.

     ![IntelliJ hata ayıklama projesi oluşturma](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. **İleri**’yi seçin.

2. **Yeni proje** penceresinde aşağıdaki adımları uygulayın:

   ![IntelliJ yeni proje Spark sürümü Seç](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Bir proje adı ve proje konumu girin.

   b. **Proje SDK 'sı** açılan listesinde **Spark 2.3.2** kümesi için **Java 1,8** ' ı seçin.

   c. **Spark sürümü** açılan listesinde **Spark 2.3.2 (Scala 2.11.8)** öğesini seçin.

   d. **Son**’u seçin.

3. Kodunuzu projede açmak için **src** > **Main** > **Scala** ' yı seçin. Bu örnek, **AgeMean_Div ()** betiğini kullanır.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>HDInsight kümesinde Spark Scala/Java uygulaması çalıştırma

Bir Spark Scala/Java uygulaması oluşturun ve ardından aşağıdaki adımları uygulayarak uygulamayı bir Spark kümesinde çalıştırın:

1. **Yapılandırma Ekle** ' ye tıklayarak **Çalıştır/hata ayıklama yapılandırmaları** penceresini açın.

   ![HDI IntelliJ yapılandırma Ekle](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. **Çalıştır/hata ayıkla yapılandırma** iletişim kutusunda artı işaretini ( **+** ) seçin. Sonra **HDInsight üzerinde Apache Spark** seçeneğini belirleyin.

   ![IntelliJ yeni yapılandırma Ekle](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Küme sekmesinde **Uzaktan Çalıştır** ' a geçiş yapın. **ad**, **Spark kümesi**ve **ana sınıf adı**bilgilerini girin. Araçlarımız **Yürüticiler**ile hata ayıklamayı destekler. **Numexectors**, varsayılan değer 5 ' tir ve 3 ' ten daha yüksek bir değere ayarlanmamalıdır. Çalışma süresini azaltmak için **spark. yarn. maxAppAttempts** 'Yi **iş yapılandırmalarına** ekleyebilir ve değeri 1 olarak ayarlayabilirsiniz. Yapılandırmayı kaydetmek için **Tamam** düğmesine tıklayın.

   ![IntelliJ hata ayıklama yapılandırmasını yeni Çalıştır](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Yapılandırma artık verdiğiniz adla birlikte kaydedilir. Yapılandırma ayrıntılarını görüntülemek için yapılandırma adını seçin. Değişiklik yapmak için, **konfigürasyonları Düzenle**' yi seçin.

5. Yapılandırma ayarlarını tamamladıktan sonra, projeyi uzak kümede çalıştırabilirsiniz.

   ![IntelliJ hata ayıklama uzak Spark Işi uzaktan çalıştırma düğmesi](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Çıkış penceresinden uygulama KIMLIĞI ' ni kontrol edebilirsiniz.

   ![IntelliJ hata ayıklama uzak Spark Işi uzak çalıştırma sonucu](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Başarısız iş profilini indir

İş gönderimi başarısız olursa, daha fazla hata ayıklama için başarısız iş profilini yerel makineye indirebilirsiniz.

1. **Microsoft Azure Depolama Gezgini**açın, başarısız iş Için kümenin HDInsight hesabını bulun, ilgili konumdan başarısız iş kaynaklarını indirin: **\hdp\parlak 2-Events\\. spark-hatalarıyla\\\<uygulama KIMLIĞI** yerel bir klasöre >. **Etkinlik** penceresinde indirme ilerleme durumu gösterilir.

   ![İndirme hatası Azure Depolama Gezgini](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![İndirme başarılı Azure Depolama Gezgini](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Yerel hata ayıklama ortamını yapılandırma ve hatada hata ayıklama

1. Orijinal projeyi açın veya yeni bir proje oluşturun ve özgün kaynak kodla ilişkilendirin. Şu anda hata ayıklama hatası için yalnızca Spark 2.3.2 sürümü destekleniyor.

1. IntelliJ ıDEA **Spark hata ayıklama** yapılandırma dosyası oluşturun, **Spark iş hatası bağlam konumu** alanı için önceden indirilen başarısız iş kaynaklarından FTD dosyasını seçin.

   ![Crete hata yapılandırması](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Araç çubuğundaki yerel çalıştırma düğmesine tıklayın, hata Çalıştır penceresinde görüntülenir.

   ![çalışma-hata-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![çalışma-hata-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Günlük gösterdiği için kesme noktası ayarlayın, sonra da IntelliJ 'deki normal Scala/Java projeleriniz gibi yerel hata ayıklama yapmak için yerel hata ayıklama düğmesine tıklayın.

1. Hata ayıkladıktan sonra, proje başarıyla tamamlanırsa, başarısız işi HDInsight kümesinde Spark uygulamanıza yeniden gönderebilirsiniz.

## <a name="seealso"></a>Sonraki adımlar

* [Genel Bakış: Apache Spark uygulamalarda hata ayıkla](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

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
* [Hortonçalışmalar korumalı alanı ile IntelliJ için HDInsight araçlarını kullanma](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Apache Spark uygulamalar oluşturmak için Azure Toolkit for Eclipse HDInsight araçlarını kullanma](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

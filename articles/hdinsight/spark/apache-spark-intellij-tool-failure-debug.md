---
title: IntelliJ Azure Toolkit (Önizleme) ile Spark işinde hata ayıklama-HDInsight
description: Uygulamalarda hata ayıklamak için Azure Toolkit for IntelliJ HDInsight araçlarını kullanma kılavuzu
keywords: Uzaktan IntelliJ, uzaktan hata ayıklama IntelliJ, SSH, IntelliJ, HDInsight, hata ayıklama IntelliJ, hata ayıklama
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5422fe324ca1f3ef5bb2d14fb04664c8fb03fe3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866242"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Azure Toolkit for IntelliJ Spark iş hata ayıklaması başarısız oldu (Önizleme)

Bu makalede **Spark hata ayıklama** uygulamalarını çalıştırmak için [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) 'de HDInsight araçlarının nasıl kullanılacağına ilişkin adım adım yönergeler sunulmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* [Oracle Java geliştirme seti](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Bu öğretici, Java sürüm 8.0.202 kullanır.
  
* IntelliJ fıkrı. Bu makalede [IntelliJ fikir Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows)kullanılmaktadır.
  
* Azure Toolkit for IntelliJ. Bkz. [Azure Toolkit for IntelliJ yükleme](/azure/developer/java/toolkit-for-intellij/installation).

* HDInsight kümenize bağlanın. Bkz. [HDInsight kümenize bağlanma](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Depolama Gezgini. Bkz. [indirme Microsoft Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Hata ayıklama şablonuyla proje oluşturma

Hata ayıklamaya devam etmek için bir Spark 2.3.2 projesi oluşturun, bu belgede hata ayıklama örnek dosyası alın.

1. IntelliJ IDEA’yı açın. **Yeni proje** penceresini açın.

   a. Sol bölmeden **Azure Spark/HDInsight** ' ı seçin.

   b. Ana pencereden **hata hata ayıklama örneği (Önizleme) (Scala) Ile Spark projesi** öğesini seçin.

     :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png" alt-text="IntelliJ hata ayıklama projesi oluşturma" border="true":::

   c. **İleri**’yi seçin.

2. **Yeni proje** penceresinde aşağıdaki adımları uygulayın:

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png" alt-text="IntelliJ yeni proje Spark sürümü Seç" border="true":::

   a. Bir proje adı ve proje konumu girin.

   b. **Proje SDK 'sı** açılan listesinde **Spark 2.3.2** kümesi için **Java 1,8** ' ı seçin.

   c. **Spark sürümü** açılan listesinde **Spark 2.3.2 (Scala 2.11.8)** öğesini seçin.

   d. **Son**'u seçin.

3.   >    >  Kodunuzu projede açmak için src Main **Scala** ' yı seçin. Bu örnek **AgeMean_Div ()** betiğini kullanır.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>HDInsight kümesinde Spark Scala/Java uygulaması çalıştırma

Bir Spark Scala/Java uygulaması oluşturun ve ardından aşağıdaki adımları uygulayarak uygulamayı bir Spark kümesinde çalıştırın:

1. **Yapılandırma Ekle** ' ye tıklayarak **Çalıştır/hata ayıklama yapılandırmaları** penceresini açın.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png" alt-text="HDI IntelliJ yapılandırma Ekle" border="true":::

2. **Çalıştır/hata ayıkla yapılandırma** iletişim kutusunda artı işaretini ( **+** ) seçin. Sonra **HDInsight üzerinde Apache Spark** seçeneğini belirleyin.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png" alt-text="IntelliJ yeni yapılandırma Ekle" border="true":::

3. Küme sekmesinde **Uzaktan Çalıştır '** a geçiş yapın. **Ad**, **Spark kümesi** ve **ana sınıf adı** bilgilerini girin. Araçlarımız **Yürüticiler** ile hata ayıklamayı destekler. **Numexectors**, varsayılan değer 5 ' tir ve 3 ' ten daha yüksek bir değere ayarlanmamalıdır. Çalışma süresini azaltmak için **spark. yarn. maxAppAttempts** 'Yi **iş yapılandırmalarına** ekleyebilir ve değeri 1 olarak ayarlayabilirsiniz. Yapılandırmayı kaydetmek için **Tamam** düğmesine tıklayın.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png" alt-text="IntelliJ hata ayıklama yapılandırmasını yeni Çalıştır" border="true":::

4. Yapılandırma artık verdiğiniz adla birlikte kaydedilir. Yapılandırma ayrıntılarını görüntülemek için yapılandırma adını seçin. Değişiklik yapmak için, **konfigürasyonları Düzenle**' yi seçin.

5. Yapılandırma ayarlarını tamamladıktan sonra, projeyi uzak kümede çalıştırabilirsiniz.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png" alt-text="IntelliJ hata ayıklama uzak Spark Işi uzaktan çalıştırma düğmesi" border="true":::

6. Çıkış penceresinden uygulama KIMLIĞI ' ni kontrol edebilirsiniz.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png" alt-text="IntelliJ hata ayıklama uzak Spark Işi uzak çalıştırma sonucu" border="true":::

## <a name="download-failed-job-profile"></a>Başarısız iş profilini indir

İş gönderimi başarısız olursa, daha fazla hata ayıklama için başarısız iş profilini yerel makineye indirebilirsiniz.

1. **Microsoft Azure Depolama Gezgini** açın, başarısız iş için kümenin HDInsight hesabını bulun, ilgili konumdan başarısız iş kaynaklarını indirin: **\hdp\parlak 2-Events \\ . Spark \\ \<application ID> -başarısızlıklarını** yerel bir klasöre yükleyin. **Etkinlik** penceresinde indirme ilerleme durumu gösterilir.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png" alt-text="İndirme hatası Azure Depolama Gezgini" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png" alt-text="İndirme başarılı Azure Depolama Gezgini" border="true":::

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Yerel hata ayıklama ortamını yapılandırma ve hatada hata ayıklama

1. Orijinal projeyi açın veya yeni bir proje oluşturun ve özgün kaynak kodla ilişkilendirin. Şu anda hata ayıklama hatası için yalnızca Spark 2.3.2 sürümü destekleniyor.

1. IntelliJ ıDEA **Spark hata ayıklama** yapılandırma dosyası oluşturun, **Spark iş hatası bağlam konumu** alanı için önceden indirilen başarısız iş kaynaklarından FTD dosyasını seçin.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png" alt-text="Crete hata yapılandırması" border="true":::

1. Araç çubuğundaki yerel çalıştırma düğmesine tıklayın, hata Çalıştır penceresinde görüntülenir.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png" alt-text="çalışma-hata-configuration1" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png" alt-text="çalışma-hata-configuration2" border="true":::

1. Günlük gösterdiği için kesme noktası ayarlayın, sonra da IntelliJ 'deki normal Scala/Java projeleriniz gibi yerel hata ayıklama yapmak için yerel hata ayıklama düğmesine tıklayın.

1. Hata ayıkladıktan sonra, proje başarıyla tamamlanırsa, başarısız işi HDInsight kümesinde Spark uygulamanıza yeniden gönderebilirsiniz.

## <a name="next-steps"></a><a name="seealso"></a>Sonraki adımlar

* [Genel Bakış: Apache Spark uygulamalarda hata ayıkla](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

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
* [Hortonçalışmalar korumalı alanı ile IntelliJ için HDInsight araçlarını kullanma](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Apache Spark uygulamalar oluşturmak için Azure Toolkit for Eclipse HDInsight araçlarını kullanma](./apache-spark-eclipse-tool-plugin.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesinde Jupyter Notebook için kullanılabilir olan kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyıter Not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)
---
title: Zeppelin Not defterleri & Apache Spark kümesi-Azure HDInsight
description: Azure HDInsight 'ta Apache Spark kümeleriyle Zeppelin not defterlerini kullanma hakkında adım adım yönergeler.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: 75811382867b93c778641ece42971018eff39949
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664598"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma

HDInsight Spark kümeleri, [Apache Spark](https://spark.apache.org/) işleri çalıştırmak Için kullanabileceğiniz [Apache Zeppelin](https://zeppelin.apache.org/) not defterlerini içerir. Bu makalede, bir HDInsight kümesinde Zeppelin Not defterini kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).
* Kümelerinizin birincil depolama alanı için URI şeması. Bu, Azure Blob depolama için `wasb://`, Azure Data Lake Storage 1. için Azure Data Lake Storage 2. veya `adl://` `abfs://`. BLOB depolama için güvenli aktarım etkinse, URI `wasbs://`olur.  Daha fazla bilgi için Ayrıca bkz. [Azure Storage 'da güvenli aktarım gerektir](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Apache Zeppelin Not defteri başlatma

1. Spark kümesine **genel bakış**bölümünde, **küme panolarından** **Zeppelin Not defteri** ' ni seçin. Küme için yönetici kimlik bilgilerini girin.  

   > [!NOTE]  
   > Aşağıdaki URL 'YI tarayıcınızda açarak kümeniz için Zeppelin not defterine de ulaşabilirsiniz. **CLUSTERNAME** değerini kümenizin adıyla değiştirin:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Yeni bir not defteri oluşturun. Üstbilgi bölmesinden **Not defteri** ' ne gidin > **Yeni Not oluştur**' u izleyin.

    ![Yeni bir Zeppelin Not defteri oluşturun](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Yeni bir Zeppelin Not defteri oluşturun")

    Not defteri için bir ad girin ve **Not oluştur**' u seçin.

3. Not defteri üstbilgisinin bağlı bir durum belirttiğinden emin olun. Sağ üst köşedeki yeşil noktayla gösterilir.

    ![Zeppelin Not defteri durumu](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Not defteri durumu")

4. Örnek verilerini geçici bir tabloya yükleyin. HDInsight 'ta bir Spark kümesi oluşturduğunuzda, örnek veri dosyası `hvac.csv`, `\HdiSamples\SensorSampleData\hvac`altındaki ilişkili depolama hesabına kopyalanır.

    Yeni not defterinde varsayılan olarak oluşturulan boş paragrafta aşağıdaki kod parçacığını yapıştırın.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Kod parçacığını çalıştırmak için **SHIFT + enter** tuşlarına basın veya paragrafın **oynat** düğmesini seçin. Paragrafın sağ köşesindeki durum, TAMAMLANDı, beklıyor, çalışıyor olarak çalışır hale gelmelidir. Çıktı, aynı paragrafın en altında görüntülenir. Ekran görüntüsü aşağıdakine benzer:

    ![Ham verilerden geçici bir tablo oluşturma](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Ham verilerden geçici bir tablo oluşturma")

    Ayrıca her paragrafa bir başlık sağlayabilirsiniz. Paragrafın sağ köşesinden, **Ayarlar** simgesini (Sprocket) seçin ve ardından **Başlığı göster**' i seçin.  

    > [!NOTE]  
    > % spark2 yorumlayıcı tüm HDInsight sürümlerindeki Zeppelin not defterlerinde desteklenmez ve% sh yorumlayıcı HDInsight 4,0 ' den itibaren desteklenmeyecektir.

5. Artık `hvac` tabloda Spark SQL deyimlerini çalıştırabilirsiniz. Aşağıdaki sorguyu yeni bir paragrafa yapıştırın. Sorgu, belirli bir tarihteki her bir bina için hedef ve gerçek sıcaklıklar arasındaki bina KIMLIĞINI ve farkı alır. **SHIFT + enter**tuşlarına basın.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    Başındaki **% SQL** deyimleri, not defterine Livy Scala yorumlayıcısını kullanmasını söyler.

6. Ekranı değiştirmek için **çubuk grafik** simgesini seçin.  **çubuk grafiği**seçtikten sonra görüntülenen **Ayarlar**, **anahtarlar**ve **değerler**seçmenize olanak sağlar.  Aşağıdaki ekran görüntüsünde çıkış gösterilmektedir.

    ![Notebook1 kullanarak Spark SQL ifadesini çalıştırma](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Notebook1 kullanarak Spark SQL ifadesini çalıştırma")

7. Spark SQL deyimlerini, sorgudaki değişkenleri kullanarak da çalıştırabilirsiniz. Sonraki kod parçacığında, sorgulamak istediğiniz olası değerlerle birlikte sorgusunda bir değişkenin `Temp`nasıl tanımlanacağı gösterilmektedir. Sorguyu ilk kez çalıştırdığınızda, bir açılan liste, değişken için belirttiğiniz değerlerle otomatik olarak doldurulur.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Bu kod parçacığını yeni bir paragrafa yapıştırın ve **SHIFT + enter**tuşlarına basın. Ardından **geçici** açılan listeden **65** ' ı seçin.

8. Ekranı değiştirmek için **çubuk grafik** simgesini seçin.  Ardından **Ayarlar** ' ı seçin ve aşağıdaki değişiklikleri yapın:

   * **Gruplar:**  **Targettemp**ekleyin.  
   * **Değerler:** 1. **Tarihi**kaldırın.  2. **Temp_diff**ekleyin.  3.  Toplayıcıyı **Sum** 'dan **Ortalama**olarak değiştirin.  

     Aşağıdaki ekran görüntüsünde çıkış gösterilmektedir.

     ![Notebook2 kullanarak Spark SQL ifadesini çalıştırma](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Notebook2 kullanarak Spark SQL ifadesini çalıştırma")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Nasıl yaparım? Not defteri ile harici paketler mi kullanıyorsunuz?

HDInsight üzerinde Apache Spark kümesinde Zeppelin Not defterini, kümede bulunmayan harici, topluluk tarafından katkıda bulunulan paketleri kullanacak şekilde yapılandırabilirsiniz. [Maven deposunda](https://search.maven.org/) kullanılabilen paketlerin tüm listesini arayabilirsiniz. Ayrıca, diğer kaynaklardan kullanılabilir paketlerin bir listesini alabilirsiniz. Örneğin, topluluk tarafından katkıda bulunulan paketlerin tam bir listesi [Spark paketlerinde](https://spark-packages.org/)bulunabilir.

Bu makalede, Jupyter Not defteri ile [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketini nasıl kullanacağınızı göreceksiniz.

1. Yorumlayıcı ayarlarını açın. Sağ üst köşeden oturum açmış kullanıcı adını seçip **yorumlayıcı**' yı seçin.

    ![Yorumlayıcı 'yı Başlat](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive çıkışı")

2. **Livy2**' ye kaydırın ve **Düzenle**' yi seçin.

    ![Yorumlayıcı Ayarları1 değiştirme](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Yorumlayıcı Ayarları1 değiştirme")

3. Anahtar `livy.spark.jars.packages`gidin ve değerini `group:id:version`biçimde ayarlayın. Bu nedenle, [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketini kullanmak istiyorsanız, anahtarın değerini `com.databricks:spark-csv_2.10:1.4.0`olarak ayarlamanız gerekir.

    ![Yorumlayıcı settings2 değiştirme](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Yorumlayıcı settings2 değiştirme")

    **Kaydet** ' i ve ardından **Tamam** ' ı seçerek Livy yorumlayıcısını yeniden başlatın.

4. Yukarıda girilen anahtarın değerine nasıl ulaşılacağı hakkında bilgi edinmek istiyorsanız, aşağıdaki adımları uygulayın.

    a. Maven deposundaki paketi bulun. Bu makalede [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)kullandık.

    b. Deposundan **GroupID**, **ArtifactId**ve **Version**değerlerini toplayın.

    ![Jupyter Not defteri ile dış paketleri kullanma](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Jupyter Not defteri ile dış paketleri kullanma")

    c. İki nokta üst üste ( **:** ) ayırarak üç değeri birleştirir.

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Not defterleri nerede kaydedilir?

Zeppelin Not defterleri küme yayın düğümlerine kaydedilir. Bu nedenle, kümeyi silerseniz Not defterleri de silinir. Not defterlerinizi daha sonra diğer kümeler üzerinde kullanılmak üzere korumak istiyorsanız, işleri çalıştırmayı bitirdikten sonra bunları dışarı aktarmanız gerekir. Bir not defterini dışarı aktarmak için, aşağıdaki görüntüde gösterildiği gibi **dışa aktarma** simgesini seçin.

![Not defteri indir](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Not defterini indir")

Bu, Not defterini indirme konumunuza bir JSON dosyası olarak kaydeder.

## <a name="livy-session-management"></a>Livy oturum yönetimi

Zeppelin Not defterinizde ilk kod paragrafı çalıştırdığınızda, HDInsight Spark kümenizde yeni bir Livy oturumu oluşturulur. Bu oturum, daha sonra oluşturduğunuz tüm Zeppelin Not defterleri genelinde paylaşılır. Herhangi bir nedenle, Livy oturumunun sonlandırısı (küme yeniden başlatma vb.) varsa, Zeppelin Not defterinden iş çalıştıramazsınız.

Böyle bir durumda, bir Zeppelin Not defterinden iş çalıştırmaya başlayabilmeniz için aşağıdaki adımları gerçekleştirmeniz gerekir.  

1. Zeppelin Not defterinden Livy yorumlayıcısını yeniden başlatın. Bunu yapmak için sağ üst köşedeki oturum açmış kullanıcı adını **seçip yorumlayıcı ' yı seçin.**

    ![Yorumlayıcı 'yı Başlat](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive çıkışı")

2. **Livy2**' e gidin ve **Yeniden Başlat**' ı seçin.

    ![Livy yorumlayıcısını yeniden başlatın](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Zeppelin yorumlayıcı 'yı yeniden başlatma")

3. Mevcut bir Zeppelin Not defterinden kod hücresi çalıştırın. Bu, HDInsight kümesinde yeni bir Livy oturumu oluşturur.

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

* [Apache Spark Scala uygulamaları oluşturmak ve göndermek için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin.md)
* [Apache Spark uygulamalarında uzaktan hata ayıklama için IntelliJ fıkır için HDInsight Araçları eklentisini kullanın](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

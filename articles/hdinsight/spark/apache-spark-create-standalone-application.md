---
title: 'Öğretici: Spark için Scala Maven uygulaması & IntelliJ-Azure HDInsight'
description: Öğretici-IntelliJ fıkrı sağlayan Scala için yapı sistemi ve var olan Maven arşiv ETYPE ile Scala 'da yazılmış bir Spark uygulaması oluşturun.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 06/26/2019
ms.openlocfilehash: 156892a4785bf1644d29b82e98c3b2ae202c5a49
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494696"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Öğretici: IntelliJ kullanarak HDInsight 'ta Apache Spark için bir Scala Maven uygulaması oluşturma

Bu öğreticide, IntelliJ ıDEA ile [Apache Maven](https://maven.apache.org/) kullanarak [Scala](https://www.scala-lang.org/) 'da yazılan [Apache Spark](https://spark.apache.org/) bir uygulama oluşturmayı öğreneceksiniz. Makale, derleme sistemi olarak Apache Maven kullanmakta ve Scala için IntelliJ IDEA tarafından sağlanan mevcut bir Maven mimari türü ile başlamaktadır.  IntelliJ IDEA’da Scala uygulaması oluşturma işlemi aşağıdaki adımları içerir:

* Derleme sistemi olarak Maven kullanma.
* Spark modülü bağımlılıklarını çözümlemek için Proje Nesne Modeli (POM) dosyasını güncelleştirme.
* Uygulamanızı Scala’da yazma.
* HDInsight Spark kümelerine gönderilebilen bir jar dosyası oluşturma.
* Livy kullanarak uygulamayı Spark kümesi üzerinde çalıştırma.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * IntelliJ IDEA için Scala eklentisini yükleme
> * IntelliJ kullanarak bir Scala Maven uygulaması geliştirme
> * Tek başına Scala projesi oluşturma

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

* [Oracle Java geliştirme seti](https://www.azul.com/downloads/azure-only/zulu/).  Bu öğretici, Java sürüm 8.0.202 kullanır.

* Bir Java IDE. Bu makalede [IntelliJ fikir topluluk ver kullanılmaktadır.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Bkz. [Azure Toolkit for IntelliJ yükleme](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA için Scala eklentisini yükleme

Scala eklentisini yüklemek için aşağıdaki adımları gerçekleştirin:

1. IntelliJ IDEA’yı açın.

2. Giriş ekranında, **Eklentiler** penceresini açmak Için > **eklentileri** **yapılandırma** ' ya gidin.

    ![IntelliJ fıkır Scala eklentisini etkinleştir](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Yeni pencerede tanıtılan Scala eklentisine yönelik **Install** öğesini seçin.  

    ![IntelliJ fıkır Scala eklentisini yükler](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Eklenti başarıyla yüklendikten sonra IDE’yi yeniden başlatmanız gerekir.

## <a name="use-intellij-to-create-application"></a>Uygulama oluşturmak için IntelliJ kullanma

1. IntelliJ FIKRINI başlatın ve yeni proje **Oluştur** ' u seçerek **Yeni proje** penceresini açın.

2. Sol bölmeden **Azure Spark/HDInsight** ' ı seçin.

3. Ana pencereden **Spark projesi (Scala)** öğesini seçin.

4. **Yapı aracı** açılan listesinden aşağıdakilerden birini seçin:
      * Scala projesi oluşturma Sihirbazı desteği için **Maven** .
      * Scala projesi için bağımlılıkları ve oluşturmayı yönetmek üzere **SBT** .

   ![Yeni proje iletişim kutusunu IntelliJ](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. **İleri**’yi seçin.

6. **Yeni proje** penceresinde, aşağıdaki bilgileri sağlayın:  

  	|  Özellik   | Açıklama   |  
  	| ----- | ----- |  
  	|Proje adı| Bir ad girin.|  
  	|Proje&nbsp;konumu| Projenizin kaydedileceği istenen konumu girin.|
  	|Proje SDK 'Sı| Bu, ilk fıkrın kullanımı üzerinde boştur.  **Yeni...** öğesini seçin ve JDK 'nize gidin.|
  	|Spark sürümü|Oluşturma Sihirbazı Spark SDK ve Scala SDK için doğru sürümü tümleştirir. Spark kümesi sürümü 2.0’dan eskiyse **Spark 1.x** seçeneğini belirleyin. Aksi takdirde, **Spark2.x** seçeneğini belirleyin. Bu örnek **Spark 2.3.0 (Scala 2.11.8)** kullanır.|

    ![Spark SDK 'sını seçen IntelliJ fıkır](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. **Son**’u seçin.

## <a name="create-a-standalone-scala-project"></a>Tek başına Scala projesi oluşturma

1. IntelliJ FIKRINI başlatın ve yeni proje **Oluştur** ' u seçerek **Yeni proje** penceresini açın.

2. Sol bölmeden **Maven** ' ı seçin.

3. Bir **Proje SDK’sı** belirtin. Boşsa, **yeni...** öğesini seçin ve Java yükleme dizinine gidin.

4. Arşiv **ETYPE oluştur** onay kutusunu seçin.  

5. Mimari türleri listesinden **org.scala-tools.archetypes:scala-archetype-simple** öğesini seçin. Bu arşiv ETYPE doğru dizin yapısını oluşturur ve Scala programını yazmak için gerekli varsayılan bağımlılıkları indirir.

    ![IntelliJ fıkır Maven projesi oluşturma](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. **İleri**’yi seçin.

7. **GroupId**, **ArtifactId** ve **Version** için ilgili değerleri sağlayın. Bu öğreticide aşağıdaki değerler kullanılır:

    - **GroupID:** com. Microsoft. spark. example
    - **ArtifactId:** Mini Simpleapp

8. **İleri**’yi seçin.

9. Ayarları doğrulayıp **İleri**’yi seçin.

10. Proje adını ve konumunu doğrulayıp **Son**’u seçin.  Projenin içeri aktarılması birkaç dakika sürer.

11. Proje içe aktarıldıktan sonra sol bölmeden, > **src** > **Test** > **Scala** > **com** > **Microsoft** > **Spark** ' **a gidin @no__t_13** _ **örneği**. >   **Myspec**öğesine sağ tıklayın ve ardından **Sil...** öğesini seçin. Uygulama için bu dosyaya ihtiyacınız yoktur.  İletişim kutusunda **Tamam ' ı** seçin.
  
12. Sonraki adımlarda, Spark Scala uygulamasının bağımlılıklarını tanımlamak için **Pod. xml** ' yi güncelleştirdik. Bu bağımlılıkların otomatik olarak indirilip çözümlenmesi için Maven’i uygun şekilde yapılandırmanız gerekir.

13. **Ayarlar penceresini açmak** için **Dosya** menüsünden **Ayarlar** ' ı seçin.

14. **Ayarlar** penceresinde, **derleme, yürütme, dağıtım** > **derleme araçları** > **Maven** > **içeri aktarma**' ya gidin.

15. **Maven projelerini otomatik olarak Içeri aktar** onay kutusunu seçin.

16. **Uygula**’yı ve sonra **Tamam**’ı seçin.  Ardından proje penceresine döndürülecektir.

    ![Maven’i otomatik yüklemeler için yapılandırma](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. Sol bölmeden **src** > **Main** > **Scala** > **com. Microsoft. spark. example**sayfasına gidin ve ardından App. Scala 'yı açmak için **uygulama** ' ya çift tıklayın.

18. Var olan örnek kodu aşağıdaki kodla değiştirin ve değişiklikleri kaydedin. Bu kod, HVAC.csv dosyasından (tüm HDInsight Spark kümelerinde mevcuttur) verileri okur, altıncı sütunda yalnızca bir basamağı olan satırları alır ve çıktıyı kümenin varsayılan depolama kapsayıcısı altındaki **/HVACOut** dosyasına yazar.

        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
19. Sol bölmede **pom.xml** dosyasına çift tıklayın.  

20. `<project>\<properties>` içinde aşağıdaki segmentleri ekleyin:

          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. `<project>\<dependencies>` içinde aşağıdaki segmentleri ekleyin:

           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>

    pom.xml dosyasında yapılan değişiklikleri kaydedin.

22. .jar dosyasını oluşturun. IntelliJ IDEA, proje yapıtı olarak JAR dosyası oluşturmayı sağlar. Aşağıdaki adımları uygulayın.

    1. **Dosya** menüsünde **proje yapısı...** öğesini seçin.

    2. **Proje yapısı** penceresinde **yapılar** ' a gidin > **ek sembol +**  > **jar** > **Bağımlılıklar içeren modüllerden.** ...

        ![IntelliJ fıkır proje yapısı jar Ekle](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. **MODÜLLERDEN jar oluştur** penceresinde, **ana sınıf** metin kutusunda klasör simgesini seçin.

    4. **Ana Sınıf Seç** penceresinde, varsayılan olarak görüntülenen sınıfı seçin ve ardından **Tamam**' ı seçin.

        ![IntelliJ fıkır proje yapısı Sınıf Seç](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. **MODÜLLERDEN jar oluştur** penceresinde, **hedef jar 'e Ayıkla** seçeneğinin seçili olduğundan emin olun ve ardından **Tamam**' ı seçin.  Bu ayar, tüm bağımlılıklarla tek bir JAR oluşturur.

        ![IntelliJ fıkır modülünden proje yapısı jar](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. **Çıkış düzeni** sekmesi, Maven projesinin bir parçası olarak dahil olan tüm jar dosyaları dışındaki türlerini listeler. Scala uygulamasının doğrudan bağımlılığı olmayan jar dosyalarını seçip silebilirsiniz. Burada oluşturduğunuz uygulama için sonuncu (**SparkSimpleApp derleme çıktısı**) hariç tüm jar dosyalarını kaldırabilirsiniz. Silinecek jar dosyaları dışındaki ' ı seçin ve ardından **-** negatif sembolünü seçin.

        ![IntelliJ fıkır proje yapısı çıkışı Sil](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Proje **derlemesinde Ekle** onay kutusunun seçili olduğundan emin olun. Bu, her proje oluşturulduğunda veya güncelleştirilirken jar 'in oluşturulmasını sağlar. **Uygula** ' yı ve ardından **Tamam**' ı seçin.

    7. Jar 'yi oluşturmak için **derleme > yapı** **yapıtları** > derleme ' ya **gidin.** Projenin yaklaşık 30 saniye içinde derlenmesi gerekir.  Çıktı jar dosyası, **\out\artifacts** altında oluşturulur.

        ![IntelliJ fıkır proje yapısı çıkışı](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Apache Spark kümesinde uygulamayı çalıştırma

Uygulamayı kümede çalıştırmak için aşağıdaki yaklaşımları kullanabilirsiniz:

* **Uygulama jar dosyasını kümeyle ilişkili olan Azure depolama blobuna kopyalayın**. Bunu yapmak için, bir komut satırı yardımcı programı olan [**AzCopy**](../../storage/common/storage-use-azcopy.md)’yi kullanabilirsiniz. Verileri karşıya yüklemek için kullanabileceğiniz çok sayıda başka istemci de mevcuttur. [HDInsight 'ta Apache Hadoop işleri Için karşıya yükleme verilerinde](../hdinsight-upload-data.md)daha fazla bilgi bulabilirsiniz.

* **Bir uygulama Işini Spark kümesine uzaktan göndermek Için Apache Livy kullanın** . HDInsight üzerinde Spark kümeleri, Spark işlerini uzaktan göndermek için REST uç noktalarını kullanıma sunan Livy’yi içerir. Daha fazla bilgi için bkz. [HDInsight 'Ta Spark kümeleriyle Apache Livy kullanarak Apache Spark işleri uzaktan gönderme](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, oluşturduğunuz kümeyi aşağıdaki adımlarla silin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Üstteki **arama** kutusuna **HDInsight**yazın.

1. **Hizmetler**altında **HDInsight kümeleri** ' ni seçin.

1. Görüntülenen HDInsight kümeleri listesinde, bu öğretici için oluşturduğunuz kümenin yanındaki **...** seçeneğini belirleyin.

1. **Sil**’i seçin. **Evet**' i seçin.

![HDInsight Azure portalı kümeyi silme](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "HDInsight kümesini Sil")

## <a name="next-step"></a>Sonraki adım

Bu makalede, Apache Spark Scala uygulaması oluşturmayı öğrendiniz. Bu uygulamayı Livy kullanarak bir HDInsight Spark kümesinde çalıştırmayı öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
>[Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](./apache-spark-livy-rest-interface.md)

---
title: 'Öğretici: Spark için Scala Maven uygulaması & IntelliJ-Azure HDInsight'
description: Öğretici-yapı sistemi olarak Apache Maven ile Scala 'da yazılmış bir Spark uygulaması oluşturun. Ve IntelliJ fıkrı sağlayan Scala için mevcut bir Maven arşiv ETYPE.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: 54738ebe45792bea70067383aaeeca99667f753f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068328"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Öğretici: IntelliJ kullanarak HDInsight 'ta Apache Spark için bir Scala Maven uygulaması oluşturma

Bu öğreticide, IntelliJ ıDEA ile Apache Maven kullanarak Scala 'da yazılan Apache Spark bir uygulama oluşturmayı öğreneceksiniz. Makale, derleme sistemi olarak Apache Maven 'i kullanır. Ve IntelliJ fıkrı tarafından sunulan Scala için mevcut bir Maven arşiv ETYPE ile başlar.  IntelliJ IDEA’da Scala uygulaması oluşturma işlemi aşağıdaki adımları içerir:

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

* Azure Toolkit for IntelliJ.  Bkz. [Azure Toolkit for IntelliJ yükleme](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA için Scala eklentisini yükleme

Scala eklentisini yüklemek için aşağıdaki adımları uygulayın:

1. IntelliJ IDEA’yı açın.

2. Giriş ekranında , eklentiler  >  penceresini açmak için **eklentileri** Yapılandır ' a gidin  .

    :::image type="content" source="./media/apache-spark-create-standalone-application/enable-scala-plugin1.png" alt-text="' IntelliJ fıkır Scala pluginenable '" border="true":::

3. Yeni pencerede tanıtılan Scala eklentisine yönelik **Install** öğesini seçin.  

    :::image type="content" source="./media/apache-spark-create-standalone-application/install-scala-plugin.png" alt-text="' IntelliJ fıkır Install Scala plugin'" border="true":::

4. Eklenti başarıyla yüklendikten sonra IDE’yi yeniden başlatmanız gerekir.

## <a name="use-intellij-to-create-application"></a>Uygulama oluşturmak için IntelliJ kullanma

1. IntelliJ FIKRINI başlatın ve yeni proje **Oluştur** ' u seçerek **Yeni proje** penceresini açın.

2. Sol bölmeden **Apache Spark/HDInsight** ' ı seçin.

3. Ana pencereden **Spark projesi (Scala)** öğesini seçin.

4. **Yapı aracı** açılan listesinden aşağıdaki değerlerden birini seçin:
      * Scala projesi oluşturma Sihirbazı desteği için **Maven** .
      * Scala projesi için bağımlılıkları ve oluşturmayı yönetmek üzere **SBT** .

   :::image type="content" source="./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png" alt-text="Yeni proje iletişim kutusunu IntelliJ" border="true":::

5. **İleri**’yi seçin.

6. **Yeni proje** penceresinde, aşağıdaki bilgileri sağlayın:  

  	|  Özellik   | Açıklama   |  
  	| ----- | ----- |  
  	|Proje adı| Bir ad girin.|  
  	|Proje &nbsp; konumu| Projenizin kaydedileceği konumu girin.|
  	|Proje SDK 'Sı| Bu alan, ilk fıkrın kullanımı üzerinde boştur.  **Yeni...** öğesini seçin ve JDK 'nize gidin.|
  	|Spark sürümü|Oluşturma Sihirbazı Spark SDK ve Scala SDK için doğru sürümü tümleştirir. Spark kümesi sürümü 2.0’dan eskiyse **Spark 1.x** seçeneğini belirleyin. Aksi takdirde, **Spark2.x** seçeneğini belirleyin. Bu örnek **Spark 2.3.0 (Scala 2.11.8)** kullanır.|

    :::image type="content" source="./media/apache-spark-create-standalone-application/hdi-scala-new-project.png" alt-text="Spark SDK 'sını seçen IntelliJ fıkır" border="true":::

7. **Son**'u seçin.

## <a name="create-a-standalone-scala-project"></a>Tek başına Scala projesi oluşturma

1. IntelliJ FIKRINI başlatın ve yeni proje **Oluştur** ' u seçerek **Yeni proje** penceresini açın.

2. Sol bölmeden **Maven** ' ı seçin.

3. Bir **Proje SDK’sı** belirtin. Boşsa, **yeni...** öğesini seçin ve Java yükleme dizinine gidin.

4. Arşiv **ETYPE oluştur** onay kutusunu seçin.  

5. Arşiv türleri listesinden öğesini seçin **`org.scala-tools.archetypes:scala-archetype-simple`** . Bu arşiv ETYPE doğru dizin yapısını oluşturur ve Scala programını yazmak için gerekli varsayılan bağımlılıkları indirir.

    :::image type="content" source="./media/apache-spark-create-standalone-application/intellij-project-create-maven.png" alt-text="Ekran görüntüsü yeni proje penceresinde seçili bir arşiv ETYPE gösterir." border="true":::

6. **İleri**’yi seçin.

7. **Yapıt koordinatlarını** genişlet. **GroupID** ve **ArtifactId** için ilgili değerleri sağlayın. **Ad** ve **konum** , yeniden doldurulur. Bu öğreticide aşağıdaki değerler kullanılır:

    - **GroupID:** com. Microsoft. spark. example
    - **ArtifactId:** Mini Simpleapp

    :::image type="content" source="./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png" alt-text="Ekran görüntüsü yeni proje penceresindeki yapıt koordinatları seçeneğini gösterir." border="true":::

8. **İleri**’yi seçin.

9. Ayarları doğrulayıp **İleri**’yi seçin.

10. Proje adını ve konumunu doğrulayıp **Son**’u seçin.  Projenin içeri aktarılması birkaç dakika sürer.

11. Proje içe aktarıldıktan sonra sol bölmeden **mini simpleapp**  >  **src**  >  **Test**  >  **Scala**  >  **com**  >  **Microsoft**  >  **Spark**  >  **örneğine** gidin.  **Myspec** öğesine sağ tıklayın ve ardından **Sil...** öğesini seçin. Uygulama için bu dosyaya ihtiyacınız yoktur.  İletişim kutusunda **Tamam ' ı** seçin.
  
12. Sonraki adımlarda, Spark Scala uygulamasının bağımlılıklarını tanımlamak için **pom.xml** güncelleştirin. Bu bağımlılıkların otomatik olarak indirilip çözümlenmesi için Maven 'yi yapılandırmanız gerekir.

13. **Ayarlar penceresini açmak** için **Dosya** menüsünden **Ayarlar** ' ı seçin.

14. **Ayarlar** penceresinde, **derleme, yürütme, dağıtım**  >  **derleme araçları**  >  **Maven**  >  **içeri aktarma**' ya gidin.

15. **Maven projelerini otomatik olarak Içeri aktar** onay kutusunu seçin.

16. **Uygula**’yı ve sonra **Tamam**’ı seçin.  Ardından proje penceresine geri dönersiniz.

    :::image type="content" source="./media/apache-spark-create-standalone-application/configure-maven-download.png" alt-text="Maven’i otomatik yüklemeler için yapılandırma" border="true":::

17. Sol bölmeden **src**  >  **Main**  >  **Scala**  >  **com. Microsoft. spark. example**' a gidin ve App. Scala 'yı açmak için **uygulama** ' ya çift tıklayın.

18. Var olan örnek kodu aşağıdaki kodla değiştirin ve değişiklikleri kaydedin. Bu kod, HVAC.csv verileri okur (tüm HDInsight Spark kümelerinde kullanılabilir). Altıncı sütunda yalnızca bir basamak olan satırları alır. Ve çıktıyı, kümenin varsayılan depolama kapsayıcısının altına **/Hvacout** dosyasına yazar.

    ```scala
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
    ```

19. Sol bölmede **pom.xml** dosyasına çift tıklayın.  

20. `<project>\<properties>` içinde aşağıdaki segmentleri ekleyin:

    ```xml
    <scala.version>2.11.8</scala.version>
    <scala.compat.version>2.11.8</scala.compat.version>
    <scala.binary.version>2.11</scala.binary.version>
    ```

21. `<project>\<dependencies>` içinde aşağıdaki segmentleri ekleyin:

    ```xml
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_${scala.binary.version}</artifactId>
        <version>2.3.0</version>
    </dependency>
    ```

    pom.xml dosyasında yapılan değişiklikleri kaydedin.

22. .jar dosyasını oluşturun. IntelliJ IDEA, proje yapıtı olarak JAR dosyası oluşturmayı sağlar. Aşağıdaki adımları uygulayın.

    1. **Dosya** menüsünde **proje yapısı...** öğesini seçin.

    2. **Proje yapısı** penceresinde,   >    >    >  **bağımlılıklara sahip modüllerden** artı sembol + jar öğesine gidin...

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png" alt-text="' IntelliJ fıkır proje yapısı jar Ekle '" border="true":::

    3. **MODÜLLERDEN jar oluştur** penceresinde, **ana sınıf** metin kutusunda klasör simgesini seçin.

    4. **Ana Sınıf Seç** penceresinde, varsayılan olarak görüntülenen sınıfı seçin ve ardından **Tamam**' ı seçin.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png" alt-text="' IntelliJ fıkır proje yapısı Sınıf Seç '" border="true":::

    5. **MODÜLLERDEN jar oluştur** penceresinde, **hedef jar 'e Ayıkla** seçeneğinin seçili olduğundan emin olun ve ardından **Tamam**' ı seçin.  Bu ayar, tüm bağımlılıklarla tek bir JAR oluşturur.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png" alt-text="IntelliJ fıkır modülünden proje yapısı jar" border="true":::

    6. **Çıkış düzeni** sekmesi, Maven projesinin bir parçası olarak dahil olan tüm jar dosyaları dışındaki türlerini listeler. Scala uygulamasının doğrudan bağımlılığı olmayan jar dosyalarını seçip silebilirsiniz. Uygulama için, burada oluşturduğunuz son bir tane (**basit Simpleapp derleme çıkışı**) ekleyebilirsiniz. Silinecek jar dosyaları dışındaki ' ı seçin ve ardından negatif sembolü seçin **-** .

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png" alt-text="' IntelliJ fıkır proje yapısı çıktıyı Sil '" border="true":::

        **Proje derlemesi 'Ne dahil et** onay kutusunun işaretli olduğundan emin olun. Bu seçenek, proje oluşturulduğu veya güncelleştirildiği her seferinde jar 'in oluşturulmasını sağlar. **Uygula** ' yı ve ardından **Tamam**' ı seçin.

    7. Jar 'yi oluşturmak **için derleme**  >  **Yapı yapıtları**  >  **derlemesi**' ne gidin. Projenin yaklaşık 30 saniye içinde derlenmesi gerekir.  Çıktı jar dosyası, **\out\artifacts** altında oluşturulur.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png" alt-text="IntelliJ fıkır proje yapısı çıkışı" border="true":::

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Apache Spark kümesinde uygulamayı çalıştırma

Uygulamayı kümede çalıştırmak için aşağıdaki yaklaşımları kullanabilirsiniz:

* **Uygulama jar dosyasını kümeyle Ilişkili Azure Storage blob 'Una kopyalayın** . Bunu yapmak için, bir komut satırı yardımcı programı olan **AzCopy**’yi kullanabilirsiniz. Verileri karşıya yüklemek için kullanabileceğiniz çok sayıda başka istemci de mevcuttur. [HDInsight 'ta Apache Hadoop işleri Için karşıya yükleme verilerinde](../hdinsight-upload-data.md)daha fazla bilgi bulabilirsiniz.

* **Bir uygulama Işini Spark kümesine uzaktan göndermek Için Apache Livy kullanın** . HDInsight üzerinde Spark kümeleri, Spark işlerini uzaktan göndermek için REST uç noktalarını kullanıma sunan Livy’yi içerir. Daha fazla bilgi için bkz. [HDInsight 'Ta Spark kümeleriyle Apache Livy kullanarak Apache Spark işleri uzaktan gönderme](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, oluşturduğunuz kümeyi aşağıdaki adımlarla silin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Üstteki **arama** kutusuna **HDInsight** yazın.

1. **Hizmetler** altında **HDInsight kümeleri** ' ni seçin.

1. Görüntülenen HDInsight kümeleri listesinde, bu öğretici için oluşturduğunuz kümenin yanındaki **...** seçeneğini belirleyin.

1. **Sil**’i seçin. **Evet**’i seçin.

:::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png " alt-text="' HDInsight Azure Portal ' kümesini silme ' kümesini Sil" border="true":::' "Border =" true ":::

## <a name="next-step"></a>Sonraki adım

Bu makalede, Apache Spark Scala uygulaması oluşturmayı öğrendiniz. Bu uygulamayı Livy kullanarak bir HDInsight Spark kümesinde çalıştırmayı öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
>[Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](./apache-spark-livy-rest-interface.md)
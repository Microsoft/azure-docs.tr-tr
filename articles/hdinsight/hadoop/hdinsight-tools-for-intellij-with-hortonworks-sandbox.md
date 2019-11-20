---
title: Hortonçalışmakorumalı alanı ile Azure Toolkit for IntelliJ kullanma
description: Azure Toolkit for IntelliJ 'de, Hortonçalışmalar korumalı alanı ile birlikte HDInsight araçlarını kullanmayı öğrenin.
keywords: Hadoop araçları, Hive sorgusu, IntelliJ, hortonçalışmalar korumalı alanı, IntelliJ için Azure araç seti
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 783d7fc8bc26ce2c715c774e63ecf60c5b75a439
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076269"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Hortonçalışmalar korumalı alanı ile IntelliJ için HDInsight araçlarını kullanma

Apache Scala uygulamaları geliştirmek için IntelliJ için HDInsight araçları 'nı kullanmayı ve sonra da bilgisayarınızda çalışan [Hortonrunning korumalı alanı](https://hortonworks.com/products/sandbox/) üzerinde uygulamaları test yapmayı öğrenin. 

[IntelliJ fikri](https://www.jetbrains.com/idea/) , bilgisayar yazılımı geliştirmeye yönelik bir Java tümleşik geliştirme ORTAMıDıR (IDE). Verilerinizi Hortonçalışmalar korumalı alanı üzerinde geliştirip test ettikten sonra, uygulamaları [Azure HDInsight](apache-hadoop-introduction.md)'a taşıyabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleye başlamadan önce aşağıdaki öğelere sahip olmanız gerekir:

- Hortonrunning Data platform (HDP) 2,4, yerel bilgisayarınızda çalışan Hortonrunning korumalı alan. HDP 'yi ayarlamak için, bkz. [sanal bir makinedeki Hadoop korumalı alanı ile Apache Hadoop ekosistemine Başlarken](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > IntelliJ için HDInsight araçları yalnızca HDP 2,4 ile test edilmiştir. HDP 2,4 'yi almak için [Hortonçalışmalar korumalı alanı İndirmeleri sitesinde](https://hortonworks.com/downloads/#sandbox) **Hortonçalışmasandbox Arşivi** ' ni genişletin.

- [Java geliştirici seti (JDK) sürüm 1,8 veya üzeri](https://aka.ms/azure-jdks). Azure Toolkit for IntelliJ JDK gerektirir.

- [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) eklentisi Ile [IntelliJ fikir community Edition](https://www.jetbrains.com/idea/download) ve [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) eklentisi. IntelliJ için HDInsight araçları Azure Toolkit for IntelliJ bir parçası olarak kullanılabilir. 

Eklentileri yüklemek için:

  1. IntelliJ IDEA’yı açın.
  2. **Hoş geldiniz** sayfasında **Yapılandır**' ı seçin ve ardından **Eklentiler**' i seçin.
  3. Sol alt köşede **JetBrains eklentisini Install**' ı seçin.
  4. **Scala**aramak için Search işlevini kullanın ve ardından **Install**' ı seçin.
  5. Yüklemeyi gerçekleştirmek için **IntelliJ FIKRINI yeniden Başlat**' ı seçin.
  6. **Azure Toolkit for IntelliJ**yüklemek için 4. ve 5. adımları tekrarlayın. Daha fazla bilgi için bkz. [ınstall Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Apache Spark Scala uygulaması oluşturma

Bu bölümde, IntelliJ FIKRINI kullanarak örnek bir Scala projesi oluşturacaksınız. Bir sonraki bölümde, projeyi göndermeden önce IntelliJ FIKRINI Hortonçalışmalar Sandbox (öykünücü) ile bağlantılandırın.

1. Bilgisayarınızda IntelliJ FIKRINI açın. **Yeni proje** iletişim kutusunda şu adımları uygulayın:

   1. **HDInsight** > **HDInsight’ta Spark (Scala)** seçeneğini belirleyin.
   2. **Yapı aracı** listesinde, senaryonuza göre aşağıdakilerden birini seçin:

      * **Maven**: Scala proje oluşturma Sihirbazı desteği için.
      * **SBT**: , Scala projesi için bağımlılıkları ve oluşturmayı yönetmek için.

   ![IntelliJ yeni Scala projesi oluştur](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. **İleri**’yi seçin.
3. Sonraki **Yeni proje** iletişim kutusunda aşağıdaki adımları izleyin:

   1. **Proje adı** kutusuna bir proje adı girin.
   2. **Proje konumu** kutusuna bir proje konumu girin.
   3. **Proje SDK 'sı** açılan listesinin yanındaki **Yeni**' yi seçin, **JDK**' i seçin ve ardından Java JDK sürüm 1,7 veya üzeri için klasörü belirtin. Spark 2. x kümesi için **Java 1,8** ' ı seçin. Spark 1. x kümesi için **Java 1,7** ' ı seçin. Varsayılan konum C:\Program Files\java\jdk1.8.x_xxxşeklindedir.
   4. **Spark sürümü** açılan listesinde, Scala proje oluşturma SIHIRBAZı Spark SDK ve Scala SDK için doğru sürümü tümleştirir. Spark kümesi sürümü 2.0’dan eskiyse **Spark 1.x** seçeneğini belirleyin. Aksi takdirde, **Spark2.x** seçeneğini belirleyin. Bu örnek Spark 1.6.2 (Scala 2.10.5) kullanır. **Scala 2.10. x**olarak işaretlenmiş depoyu kullandığınızdan emin olun. Scala 2.11. x olarak işaretlenmiş depoyu kullanmayın.
    
      ![IntelliJ Scala proje özellikleri oluştur](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. **Son**’u seçin.
5. **Proje** görünümü zaten açık değilse, açmak için **alt + 1** tuşlarına basın.
6. **Proje Gezgini**' nde projeyi genişletin ve ardından **src**' yi seçin.
7. **Src**öğesine sağ tıklayın, **Yeni**' nin üzerine gelin ve ardından **Scala sınıfı**' nı seçin.
8. **Ad** kutusuna bir ad girin. **Tür** kutusunda, **nesne**' yi seçin. Sonra **Tamam**’ı seçin.

    ![Yeni Scala sınıfı oluştur iletişim kutusu](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. . Scala dosyasında, aşağıdaki kodu yapıştırın:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. **Yapı** menüsünde **proje oluştur**' u seçin. Derlemenin başarılı bir şekilde tamamlandığından emin olun.


## <a name="link-to-the-hortonworks-sandbox"></a>Hortonçalışmalar korumalı alanına bağlantı

Hortonbir çalışma alanı 'na (öykünücü) bağlanmadan önce, mevcut bir IntelliJ uygulamasına sahip olmanız gerekir.

Öykünücüyü bağlamak için:

1. Projeyi IntelliJ içinde açın.
2. **Görünüm** menüsünde, **Araçlar Windows**' u seçin ve ardından **Azure Gezgini**' ni seçin.
3. **Azure**' ı genişletin, **HDInsight**' a sağ tıklayın ve ardından **bir öykünücü bağla**' yı seçin.
4. **Yeni bir öykünücü bağla** iletişim kutusunda, Hortonçalışmalar korumalı alanının kök hesabı için ayarladığınız parolayı girin. Ardından, aşağıdaki ekran görüntüsünde kullanılanlarla benzer değerleri girin. Sonra **Tamam**’ı seçin. 

   ![Yeni bir öykünücü bağla iletişim kutusu](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Öykünücüyü yapılandırmak için **Evet**' i seçin.

Öykünücü başarıyla bağlandığında, öykünücü (Hortonçalışmalar korumalı alanı) HDInsight düğümünde listelenir.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Spark Scala uygulamasını Hortonçalışmalar korumalı alanına gönderme

Öykünücüyü IntelliJ FIKRINI bağladıktan sonra projenizi gönderebilirsiniz.

Bir öykünücüyü bir proje göndermek için:

1. **Proje Gezgini**'nde projeye sağ tıklayın ve ardından **Spark uygulamasını HDInsight 'a gönder**' i seçin.
2. Aşağıdaki adımları tamamlayın:

    1. **Spark kümesi (yalnızca Linux)** aşağı açılan listesinde, yerel Hortonçalışmalarınızın korumalı alanını seçin.
    2. **Ana sınıf adı** kutusunda, ana sınıf adını seçin veya girin. Bu makale için ad **Groupbytest**' dir.

3. Seçin **gönderme**. İş gönderme günlükleri Spark gönderim aracı penceresinde gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

- [HDInsight Spark Linux kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for IntelliJ 'de HDInsight araçlarını kullanmayı](../spark/apache-spark-intellij-tool-plugin.md)öğrenin.

- IntelliJ için HDInsight araçları hakkında bir video için, bkz. [Apache Spark geliştirme Için IntelliJ Için HDInsight araçları 'Nı tanıtma](https://www.youtube.com/watch?v=YTZzYVgut6c).

- [Bir HDInsight kümesindeki Apache Spark UYGULAMALARıNDA SSH aracılığıyla Azure Toolkit for IntelliJ uzaktan hata ayıklamayı](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)öğrenin.

- [HDInsight Spark Linux kümesinde Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ HDInsight araçlarını kullanmayı](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)öğrenin.

- [Apache Spark uygulamalar oluşturmak için Azure Toolkit for Eclipse HDInsight araçlarını kullanmayı](../spark/apache-spark-eclipse-tool-plugin.md)öğrenin.

- Çakışan Küreler HDInsight araçları hakkında bir video için bkz. [Spark uygulamaları oluşturmak Için HDInsight araçları kullanma](https://mix.office.com/watch/1rau2mopb6fha).

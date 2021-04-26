---
title: 'Azure Toolkit for Eclipse: HDInsight Spark için Scala uygulamaları oluşturma'
description: Scala 'da yazılmış Spark uygulamaları geliştirmek ve bunları doğrudan tutulma IDE 'sinden bir HDInsight Spark kümesine göndermek için Azure Toolkit for Eclipse 'de HDInsight araçlarını kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: e4f19afc0edf0f43b64548ae1c93ed5ff8dfa8a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866582"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>HDInsight kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for Eclipse kullanma

Azure Toolkit 'teki HDInsight araçlarını kullanarak, [Scala](https://www.scala-lang.org/) 'da yazılmış [Apache Spark](https://spark.apache.org/) uygulamalar geliştirin ve bunları [doğrudan tutulma IDE](https://www.eclipse.org/) 'sinden bir Azure HDInsight Spark kümesine gönderebilirsiniz. HDInsight Araçları eklentisini birkaç farklı şekilde kullanabilirsiniz:

* Bir HDInsight Spark kümesinde bir Scala Spark uygulaması geliştirmek ve göndermek için.
* Azure HDInsight Spark kümesi kaynaklarınıza erişin.
* Bir Scala Spark uygulamasını yerel olarak geliştirmek ve çalıştırmak için.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

* [Java geliştirici seti (JDK) sürüm 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Tutulma IDE](https://www.eclipse.org/downloads/). Bu makale, Java geliştiricileri için tutulma IDE 'yi kullanır.

## <a name="install-required-plug-ins"></a>Gerekli eklentileri yükler

### <a name="install-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse'i yükleme

Yükleme yönergeleri için bkz. [yükleme Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>Scala eklentisini kurma

Çakışan Küreler ' ı açtığınızda, HDInsight araçları, Scala eklentisini yükleyip yüklemeip yüklemeyeceğinizi otomatik olarak algılar. Devam etmek için **Tamam** ' ı seçin ve ardından, çakışan küreler marketi 'nden eklentiyi yüklemek için yönergeleri izleyin. Yükleme tamamlandıktan sonra IDE 'yi yeniden başlatın.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png" alt-text="Scala eklentisinin otomatik yüklemesi" border="true":::

### <a name="confirm-plug-ins"></a>Eklentileri Onayla

1. Yardım eden   >  **küreler Market 'e git...**.

1. **Yüklü öğeler** sekmesini seçin.

1. En azından şunu görmeniz gerekir:
    * Azure Toolkit for Eclipse \<version> .
    * Scala IDE \<version> .

## <a name="sign-in-to-your-azure-subscription"></a>Azure aboneliğinizde oturum açın

1. Tutulma IDE 'yi başlatın.

1. Pencereye git   >   diğer **görünümü göster**  >  **...**  >  **Oturum aç..**.

1. **Görünümü göster** iletişim kutusunda **Azure**  >  **Azure Gezgini**' ne gidin ve **Aç**' ı seçin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png" alt-text="Apache Spark tutulma görünümü göster" border="true":::

1. **Azure Gezgini**'nden **Azure** düğümüne sağ tıklayın ve ardından **oturum aç**' ı seçin.

1. **Azure oturum açma** iletişim kutusunda kimlik doğrulama yöntemini seçin, **oturum aç**' ı seçin ve oturum açma işlemini doldurun.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png" alt-text="Apache Spark tutulma Azure Işareti" border="true":::

1. Oturum açtıktan sonra, **abonelikleriniz** iletişim kutusunda kimlik bilgileriyle Ilişkili tüm Azure abonelikleri listelenir. İletişim kutusunu kapatmak için **Seç** ' e basın.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png" alt-text="Abonelikler Seç iletişim kutusu" border="true":::

1. **Azure Explorer**'da,   >   aboneliğinizin altındaki HDInsight Spark kümelerini görmek için Azure **HDInsight** ' a gidin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png" alt-text="Azure 'da HDInsight Spark kümeleri Explorer3" border="true":::

1. Kümeyle ilişkili kaynakları (örneğin, depolama hesapları) görmek için bir küme adı düğümünü daha da genişletebilirsiniz.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png" alt-text="Kaynakları görmek için küme adını genişletme" border="true":::

## <a name="link-a-cluster"></a>Bir kümeyi bağlama

Bir normal kümeyi, ambarı yönetilen Kullanıcı adı kullanarak bağlayabilirsiniz. Benzer şekilde, etki alanına katılmış bir HDInsight kümesi için, etki alanı ve Kullanıcı adı (gibi) kullanarak bağlantı oluşturabilirsiniz `user1@contoso.com` .

1. **Azure Gezgini**'nden **HDInsight** öğesine sağ tıklayın ve **küme bağla**' yı seçin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png" alt-text="Azure Explorer bağlantı kümesi menüsü" border="true":::

1. **Küme adını**, **Kullanıcı adını** ve **parolayı** girip **Tamam**' ı seçin. İsteğe bağlı olarak, depolama hesabı, depolama anahtarı girin ve ardından Depolama Gezgini 'nin sol ağaç görünümünde çalışması için depolama kapsayıcısını seçin

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png" alt-text="Yeni HDInsight kümesi bağlantısı iletişim kutusu" border="true":::

   > [!NOTE]  
   > Küme hem Azure abonelikte hem de bir kümeye bağlandığında bağlı depolama anahtarını, Kullanıcı adını ve parolayı kullanırız.
   > :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png" alt-text="Azure Gezgini depolama hesapları" border="true":::
   >
   > Yalnızca klavye kullanıcısı için, geçerli odak **depolama anahtaryken**, iletişim kutusunda bir sonraki alana odaklanmak için **CTRL + TAB** tuşlarını kullanmanız gerekir.

1. Bağlı kümeyi **HDInsight** altında görebilirsiniz. Artık, bu bağlantılı kümeye bir uygulama gönderebilirsiniz.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png" alt-text="Azure Explorer HDI bağlantılı kümesi" border="true":::

1. Ayrıca bir kümenin bağlantısını **Azure Explorer**'da kaldırabilirsiniz.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png" alt-text="Azure Explorer bağlantısı olmayan küme" border="true":::

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>HDInsight Spark kümesi için Spark Scala projesi ayarlama

1. Çakışan IDE çalışma alanından **Dosya**  >  **Yeni**  >  **proje...** öğesini seçin.

1. **Yeni proje** sihirbazında   >  **HDInsight (Scala) üzerinde** HDInsight proje Spark ' ı seçin. Sonra **İleri**’yi seçin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png" alt-text="HDInsight üzerinde Spark (Scala) Projesi seçme" border="true":::

1. **Yeni HDInsight Scala projesi** iletişim kutusunda aşağıdaki değerleri belirtin ve ardından **İleri**' yi seçin:
   * Proje için bir ad girin.
   * **JRE** alanında, **bir yürütme ortamı kullanın JRE** 'nin **javaı-1,7** veya üzeri olarak ayarlandığından emin olun.
   * **Spark kitaplığı** alanında, **Spark SDK 'Sını yapılandırmak için Maven 'i kullanın** seçeneğini belirleyebilirsiniz.  Aracımız Spark SDK ve Scala SDK için uygun sürümü tümleştirir. Spark SDK 'yı **El Ile Ekle** , Indir ve Spark SDK 'yi el ile Ekle seçeneğini de belirleyebilirsiniz.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png" alt-text="Yeni HDInsight Scala projesi iletişim kutusu" border="true":::

1. Sonraki iletişim kutusunda ayrıntıları gözden geçirin ve ardından **son**' u seçin.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark kümesi için bir Scala uygulaması oluşturma

1. **Paket Gezgini**' nden, daha önce oluşturduğunuz projeyi genişletin. **Src** öğesine sağ tıklayın, **Yeni**  >  **diğer...** seçeneğini belirleyin.

1. **Sihirbaz Seç** iletişim kutusunda **Scala sihirbazları**  >  **Scala nesnesi**' ni seçin. Sonra **İleri**’yi seçin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png" alt-text="Bir Scala nesnesi oluşturma Sihirbazı seçin" border="true":::

1. **Yeni dosya oluştur** iletişim kutusunda, nesne için bir ad girin ve ardından **son**' u seçin. Bir metin Düzenleyicisi açılır.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png" alt-text="Yeni dosya Sihirbazı yeni dosya oluştur" border="true":::

1. Metin düzenleyicisinde, geçerli içeriği aşağıdaki kodla değiştirin:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Uygulamayı bir HDInsight Spark kümesinde çalıştırın:

   a. Paket Gezgini ' nden proje adına sağ tıklayın ve ardından **Spark uygulamasını HDInsight 'A gönder**' i seçin.

   b. **Spark gönderimi** iletişim kutusunda aşağıdaki değerleri sağlayın ve ardından **Gönder**' i seçin:

   * **Küme adı** için, uygulamanızı çalıştırmak Istediğiniz HDInsight Spark kümesini seçin.
   * Çakışan Küreler projesinden bir yapıt seçin ya da bir sabit sürücüden birini seçin. Varsayılan değer, Paket Gezgini ' nde sağ tıkladığınıza göre değişir.
   * **Ana sınıf adı** açılır listesinde, gönderim Sihirbazı projenizdeki tüm nesne adlarını görüntüler. Çalıştırmak istediğiniz bir tane seçin veya girin. Bir sabit sürücüden yapıt seçtiyseniz, ana sınıf adını el ile girmeniz gerekir. 
   * Bu örnekteki uygulama kodu herhangi bir komut satırı bağımsız değişkeni ya da JARs veya dosya başvurusu gerektirmediğinden, kalan metin kutularını boş bırakabilirsiniz.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png" alt-text="Apache Spark gönderimi iletişim kutusu" border="true":::

1. **Spark gönderim** sekmesi ilerlemeyi görüntülemeye başlamalıdır. **Spark gönderim** penceresindeki kırmızı düğmeyi seçerek uygulamayı durdurabilirsiniz. Ayrıca, bu belirli uygulama için günlükleri görüntülemek için dünya simgesini seçin (görüntüdeki mavi kutusuyla gösterilir).

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png" alt-text="Apache Spark gönderme penceresi" border="true":::

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse 'de HDInsight araçlarını kullanarak HDInsight Spark kümelerine erişin ve yönetin

İş çıktısına erişme dahil olmak üzere HDInsight araçlarını kullanarak çeşitli işlemler gerçekleştirebilirsiniz.

### <a name="access-the-job-view"></a>İş görünümüne erişin

1. **Azure Explorer**'da **HDInsight**' ı ve Spark küme adı ' nı genişletin ve ardından **işler**' i seçin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png" alt-text="Azure Gezgini tutulma iş görünümü düğümü" border="true":::

1. **İşler** düğümünü seçin. Java sürümü **1,8**'den düşükse, HDInsight araçları otomatik olarak **E (FX) clipu** eklentisini yüklersiniz. Devam etmek için **Tamam** ' ı seçin ve ardından, çakışan küreler marketi 'nden yüklemek için Sihirbazı izleyin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png" alt-text="Eksik eklenti E (FX) panoları 'nı yükler" border="true":::

1. **İşler** düğümünden iş görünümünü açın. Sağ bölmede **Spark Iş görünümü** sekmesi kümede çalıştırılan tüm uygulamaları görüntüler. Daha fazla ayrıntı görmek istediğiniz uygulamanın adını seçin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png" alt-text="Apache tutulma görünümü iş günlüğü ayrıntıları" border="true":::

   Daha sonra bu eylemlerden herhangi birini gerçekleştirebilirsiniz:

   * İş grafiğinde üzerine gelin. Çalışan iş hakkında temel bilgileri görüntüler. İş grafiğini seçin ve her işin ürettiği aşamaları ve bilgileri görebilirsiniz.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png" alt-text="Apache Spark iş grafiği aşama bilgisi" border="true":::

   * **Sürücü stderr**, **sürücü stdout** ve **Dizin bilgileri** de dahil olmak üzere sık kullanılan günlükleri görüntülemek için **günlük** sekmesini seçin.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png" alt-text="Apache Spark tutulma iş günlüğü bilgileri" border="true":::

   * Pencerenin üst kısmındaki köprüleri seçerek Spark geçmişi Kullanıcı arabirimini ve Apache Hadoop YARN Kullanıcı arabirimini (uygulama düzeyinde) açın.

### <a name="access-the-storage-container-for-the-cluster"></a>Küme için depolama kapsayıcısına erişin

1. Azure Explorer 'da, kullanılabilir HDInsight Spark kümelerinin bir listesini görmek için **HDInsight** kök düğümünü genişletin.

1. Depolama hesabını ve kümenin varsayılan depolama kapsayıcısını görmek için küme adını genişletin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png" alt-text="Depolama hesabı ve varsayılan depolama kapsayıcısı" border="true":::

1. Kümeyle ilişkili depolama kapsayıcısı adını seçin. Sağ bölmede **Hvacout** klasörünü çift tıklatın. Uygulamanın çıkışını görmek için **bölüm** dosyalarından birini açın.

### <a name="access-the-spark-history-server"></a>Spark geçmiş sunucusuna erişin

1. Azure Explorer 'da Spark kümeniz adına sağ tıklayın ve ardından **Spark geçmişi Kullanıcı arabirimini aç**' ı seçin. İstendiğinde, kümenin yönetici kimlik bilgilerini girin. Kümeyi sağlarken bunları belirtmelisiniz.

1. Spark geçmiş sunucusu panosunda, az önce çalıştırmayı tamamlamış uygulamayı aramak için uygulama adını kullanırsınız. Yukarıdaki kodda, kullanarak uygulama adını ayarlarsınız `val conf = new SparkConf().setAppName("MyClusterApp")` . Bu nedenle, Spark uygulamanızın adı **Myclusterapp** idi.

### <a name="start-the-apache-ambari-portal"></a>Apache ambarı portalını başlatma

1. Azure Explorer 'da Spark kümeniz adına sağ tıklayın ve ardından **küme yönetim portalı aç (ambarı)** öğesini seçin.

1. İstendiğinde, kümenin yönetici kimlik bilgilerini girin. Kümeyi sağlarken bunları belirtmelisiniz.

### <a name="manage-azure-subscriptions"></a>Azure aboneliklerini yönetme

Varsayılan olarak, Azure Toolkit for Eclipse HDInsight aracında tüm Azure aboneliklerinizden Spark kümeleri listelenir. Gerekirse, kümeye erişmek istediğiniz abonelikleri belirtebilirsiniz.

1. Azure Gezgini ' nde **Azure** kök düğümüne sağ tıklayın ve ardından **abonelikleri Yönet**' i seçin.

1. İletişim kutusunda, erişmek istemediğiniz aboneliğin onay kutularını temizleyin ve ardından **Kapat**' ı seçin. Azure aboneliğinizden oturumunuzu kapatmak istiyorsanız **oturumu** Kapat ' ı da seçebilirsiniz.

## <a name="run-a-spark-scala-application-locally"></a>Spark Scala uygulamasını yerel olarak çalıştırma

Spark Scala uygulamalarını iş istasyonunuzda yerel olarak çalıştırmak için, Azure Toolkit for Eclipse 'de HDInsight araçlarını kullanabilirsiniz. Genellikle, bu uygulamaların depolama kapsayıcısı gibi küme kaynaklarına erişmesi gerekmez ve bunları yerel olarak çalıştırıp test edebilirsiniz.

### <a name="prerequisite"></a>Önkoşul

Yerel Spark Scala uygulamasını bir Windows bilgisayarında çalıştırırken [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)' de anlatıldığı gibi bir özel durum alabilirsiniz. Bu özel durum, Windows 'ta **WinUtils.exe** eksik olduğu için oluşur.

Bu hatayı çözmek için **C:\wintıı S\bin** gibi bir konuma [Winutils.exe](https://github.com/steveloughran/winutils) gerekir ve ardından ortam değişkenini **HADOOP_HOME** ekleyip değişkenin değerini **c\winutils** olarak ayarlamalısınız.

### <a name="run-a-local-spark-scala-application"></a>Yerel Spark Scala uygulaması çalıştırma

1. Çakışan Küreler başlatın ve bir proje oluşturun. **Yeni proje** iletişim kutusunda aşağıdaki seçimleri yapın ve ardından **İleri**' yi seçin.

1. **Yeni proje** sihirbazında,   >  **HDInsight yerel çalıştırma örneği ' nde (Scala)** HDInsight proje Spark ' ı seçin. Sonra **İleri**’yi seçin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png" alt-text="Yeni proje bir sihirbaz iletişim kutusu seçer" border="true":::

1. Proje ayrıntılarını sağlamak için, önceki bölümde bulunan 3 ile 6 arasındaki adımları izleyerek [HDInsight Spark kümesi için bir Spark Scala projesi kurun](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Şablon, bilgisayarınızda yerel olarak çalıştırabileceğiniz **src** klasörünün altına bir örnek kod (**logquery**) ekler.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/local-scala-application.png" alt-text="LogQuery yerel Scala uygulamasının konumu" border="true":::

1. **Logquery. Scala** öğesine sağ tıklayın ve 1. **Farklı Çalıştır**  >  **uygulaması**' nı seçin. Aşağıdakine benzer bir çıktı, **konsol** sekmesinde görünür:

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png" alt-text="Spark uygulaması yerel çalıştırma sonucu" border="true":::

## <a name="reader-only-role"></a>Yalnızca okuyucu rolü

Kullanıcılar işi yalnızca okuyucu rolü izni olan bir kümeye gönderdiğinde, ambarı kimlik bilgileri gereklidir.

### <a name="link-cluster-from-context-menu"></a>Bağlam menüsünden küme bağlama

1. Yalnızca okuyucu rol hesabıyla oturum açın.

2. **Azure Explorer**'da, aboneliğinizdeki HDInsight kümelerini görüntülemek için **HDInsight** ' ı genişletin. **"Role: Reader"** olarak işaretlenen kümelerin yalnızca yalnızca okuyucu rolü izni vardır.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png" alt-text="Azure Gezgini rol okuyucudaki HDInsight Spark kümeleri" border="true":::

3. Yalnızca okuyucu rolü izniyle kümeye sağ tıklayın. **Bu kümeyi** bağlama menüsünden Bağla ' yı seçin. Ambarı Kullanıcı adı ve parolasını girin.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png" alt-text="Azure Explorer 'da HDInsight Spark kümeleri bağlantısı" border="true":::

4. Küme başarıyla bağlanmışsa, HDInsight yenilenir.
   Kümenin aşaması bağlantılı hale gelir.
  
    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png" alt-text="Azure Explorer 'da HDInsight Spark kümeleri bağlı" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Işler düğümünü genişleterek küme bağlama

1. **İşler** düğümü ' ne tıklayın, **küme işi erişimi reddedildi** penceresi açılır.

2. **Bu kümeyi** bağlantı kümesine bağla ' ya tıklayın.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png" alt-text="Azure 'da HDInsight Spark kümeleri Explorer9" border="true":::

### <a name="link-cluster-from-spark-submission-window"></a>Kümeyi Spark gönderim penceresinden bağla

1. HDInsight projesi oluşturun.

2. Pakete sağ tıklayın. Ardından **Spark uygulamasını HDInsight 'A gönder**' i seçin.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png" alt-text="Azure Explorer 'da HDInsight Spark kümeleri gönderme" border="true":::

3. **Küme adı** için yalnızca okuyucu rolü iznine sahip olan bir küme seçin. Uyarı iletisi görüntülenir. **Bu kümeyi** bağlantı kümesine bağla ' ya tıklayabilirsiniz.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png" alt-text="Azure Explorer 'da HDInsight Spark kümeleri bunu bağla" border="true":::

### <a name="view-storage-accounts"></a>Depolama hesaplarını görüntüleme

* Yalnızca okuyucu rolü iznine sahip kümeler için **depolama hesapları** düğümü ' ne tıklayın, **depolama erişimi reddedildi** penceresi açılır.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png" alt-text="Azure Explorer depolamada HDInsight Spark kümeleri" border="true":::

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png" alt-text="Azure Explorer 'da HDInsight Spark kümeleri reddedildi" border="true":::

* Bağlantılı kümeler için **depolama hesapları** düğümü ' ne tıklayın, **depolama erişimi reddedildi** penceresi açılır.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png" alt-text="Azure Explorer 'da HDInsight Spark kümeleri denied2" border="true":::

## <a name="known-problems"></a>Bilinen sorunlar

**Bir küme bağlantısı** kullanırken, depolama alanı kimlik bilgisi sağlamanızı öneriyorum.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png" alt-text="küme, depolama kimlik bilgisi eclipses ile bağlantı" border="true":::

İşleri göndermek için iki mod vardır. Depolama kimlik bilgileri sağlanmışsa, işi göndermek için Batch modu kullanılacaktır. Aksi takdirde etkileşimli mod kullanılacaktır. Küme meşgulse, aşağıdaki hatayı alabilirsiniz.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png " alt-text="Çakışan tutulma, küme meşgul olduğunda hata alır" border="true":::

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png " alt-text="küme meşgul olduğunda tutulma hatası al Yarn" border="true":::

## <a name="see-also"></a>Ayrıca bkz.

* [Genel Bakış: Azure HDInsight’ta Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>Senaryolar

* [BI ile Apache Spark: bı araçlarıyla HDInsight 'ta Spark kullanarak etkileşimli veri çözümlemesi gerçekleştirme](apache-spark-use-bi-tools.md)
* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 'ta Apache Spark kullanarak Web sitesi günlüğü Analizi](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Uygulamaları oluşturma ve çalıştırma

* [Scala kullanarak tek başına uygulama oluşturma](apache-spark-create-standalone-application.md)
* [Apache Livy kullanarak Apache Spark kümesinde işleri uzaktan çalıştırma](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Araçlar ve uzantılar

* [Spark Scala uygulamaları oluşturmak ve göndermek için Azure Toolkit for IntelliJ kullanma](apache-spark-intellij-tool-plugin.md)
* [VPN aracılığıyla Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ kullanma](./apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ kullanarak Apache Spark uygulamalarda SSH aracılığıyla uzaktan hata ayıklayın](./apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesinde Jupyter Notebook için kullanılabilir olan kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyıter Not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)
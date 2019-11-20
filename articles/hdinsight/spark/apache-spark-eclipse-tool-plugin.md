---
title: 'Azure Toolkit for Eclipse: HDInsight Spark için Scala uygulamaları oluşturma'
description: Scala 'da yazılmış Spark uygulamaları geliştirmek ve bunları doğrudan tutulma IDE 'sinden bir HDInsight Spark kümesine göndermek için Azure Toolkit for Eclipse 'de HDInsight araçlarını kullanın.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 7e320b4a0166ffad3ba62c80ad7667c626acb136
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494679"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>HDInsight kümesi için Apache Spark uygulamalar oluşturmak üzere Azure Toolkit for Eclipse kullanma

Azure Toolkit 'teki HDInsight araçlarını kullanarak, [Scala](https://www.scala-lang.org/) 'da yazılmış [Apache Spark](https://spark.apache.org/) uygulamalar geliştirin ve bunları [doğrudan tutulma IDE](https://www.eclipse.org/) 'sinden bir Azure HDInsight Spark kümesine gönderebilirsiniz. HDInsight Araçları eklentisini birkaç farklı şekilde kullanabilirsiniz:

* Bir HDInsight Spark kümesinde bir Scala Spark uygulaması geliştirmek ve göndermek için.
* Azure HDInsight Spark kümesi kaynaklarınıza erişin.
* Bir Scala Spark uygulamasını yerel olarak geliştirmek ve çalıştırmak için.

> [!IMPORTANT]  
> Bu aracı yalnızca Linux üzerinde bir HDInsight Spark kümesi için uygulama oluşturmak ve göndermek üzere kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).
* Çakışan Küreler IDE çalışma zamanı için kullanılan Oracle Java Geliştirme Seti sürüm 8. [Oracle Web sitesinden](https://aka.ms/azure-jdks)indirebilirsiniz.
* Tutulma IDE. Bu makale, Neon tutulma kullanır. Bunu, [tutulma Web sitesinden](https://www.eclipse.org/downloads/)yükleyebilirsiniz.

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Azure Toolkit for Eclipse ve Scala eklentisinde HDInsight araçları 'nı yükler

### <a name="install-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse yüklensin

Azure Toolkit for Eclipse bir parçası olarak, tutulma için HDInsight araçları mevcuttur. Yükleme yönergeleri için bkz. [yükleme Azure Toolkit for Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Scala eklentisini kurma

Çakışan Küreler ' ı açtığınızda, HDInsight Aracı, Scala eklentisini yükleyip yüklemeip yüklemeyeceğinizi otomatik olarak algılar. Devam etmek için **Tamam** ' ı seçin ve ardından, çakışan küreler marketi 'nden eklentiyi yüklemek için yönergeleri izleyin.

![Scala eklentisinin otomatik yüklemesi](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

Kullanıcı [Azure aboneliğinde oturum](#sign-in-to-your-azure-subscription)açabilir veya [bir HDInsight kümesini](#link-a-cluster) , ambarı Kullanıcı adı/parola veya etki alanına katılmış kimlik bilgilerini kullanarak bağlantı altına alabilir.

## <a name="sign-in-to-your-azure-subscription"></a>Azure aboneliğinizde oturum açın

1. Çakışan Küreler IDE 'yi başlatın ve Azure Explorer 'ı açın. **Pencere** menüsünde **Görünüm göster**' i seçin ve ardından **diğer**' i seçin. Açılan iletişim kutusunda **Azure**' ı genişletin, **Azure Gezgini**' ni seçin ve ardından **Tamam**' ı seçin.

   ![Apache Spark Eclispse görünümü göster](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. **Azure** düğümüne sağ tıklayın ve ardından **oturum aç**' ı seçin.
1. **Azure oturum açma** iletişim kutusunda kimlik doğrulama yöntemini seçin, **oturum aç**' ı seçin ve Azure kimlik bilgilerinizi girin.

   ![Apache Spark Eclispse Azure Işareti](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Oturum açtıktan sonra, **abonelikler Seç** iletişim kutusunda kimlik bilgileriyle Ilişkili tüm Azure abonelikleri listelenir. İletişim kutusunu kapatmak için **Seç** ' e tıklayın.

   ![Abonelikler Seç iletişim kutusu](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. **Azure Explorer** sekmesinde, aboneliğinizdeki HDInsight Spark kümelerini görmek için **HDInsight** ' ı genişletin.

   ![Azure 'da HDInsight Spark kümeleri Explorer3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. Kümeyle ilişkili kaynakları (örneğin, depolama hesapları) görmek için bir küme adı düğümünü daha da genişletebilirsiniz.

   ![Kaynakları görmek için küme adını genişletme](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Bir kümeyi bağlama

Bir normal kümeyi, ambarı yönetilen Kullanıcı adı kullanarak bağlayabilirsiniz. Benzer şekilde, etki alanına katılmış bir HDInsight kümesi için, `user1@contoso.com`gibi etki alanı ve Kullanıcı adı kullanarak bağlantı oluşturabilirsiniz.

1. **Azure Explorer**'dan **bir küme bağla** ' yı seçin.

   ![Azure Explorer bağlantı kümesi menüsü](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Küme **adı**, **Kullanıcı adı** ve **parola**girin, ardından kümeyi bağlamak için Tamam düğmesine tıklayın. İsteğe bağlı olarak, depolama hesabı, depolama anahtarı girin ve ardından Depolama Gezgini 'nin sol ağaç görünümünde çalışması için depolama kapsayıcısını seçin

   ![Yeni HDInsight kümesi bağlantısı iletişim kutusu](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > Küme hem Azure abonelikte hem de bir kümeye bağlandığında bağlı depolama anahtarını, Kullanıcı adını ve parolayı kullanırız.
   > Azure Explorer depolama hesaplarını ![](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Giriş bilgileri doğru ise, Tamam düğmesine tıkladıktan sonra **HDInsight** düğümünde bağlantılı bir küme görebilirsiniz. Artık, bu bağlantılı kümeye bir uygulama gönderebilirsiniz.

   ![Azure Explorer HDI bağlantılı kümesi](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. Ayrıca bir kümenin bağlantısını **Azure Explorer**'da kaldırabilirsiniz.

   ![Azure Explorer bağlantısı olmayan küme](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>HDInsight Spark kümesi için Spark Scala projesi ayarlama

1. Tutulma IDE çalışma alanında **Dosya**' yı seçin, **Yeni**' yi seçin ve ardından **Proje**' yi seçin.

1. Yeni proje sihirbazında **HDInsight**' ı genişletin, **HDInsight 'Ta Spark (Scala)** öğesini seçin ve ardından **İleri**' yi seçin.

   ![HDInsight üzerinde Spark (Scala) Projesi seçme](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. Scala proje oluşturma Sihirbazı, Scala eklentisini yükleyip yüklemeip yüklemeyeceğinizi otomatik olarak algılar. Scala eklentisini indirmeye devam etmek için **Tamam** ' ı seçin ve ardından, tutulma 'yi yeniden başlatmak için yönergeleri izleyin.

   ![Eksik eklenti Scala denetimini yükler](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala2.png)

1. **Yeni HDInsight Scala projesi** iletişim kutusunda aşağıdaki değerleri belirtin ve ardından **İleri**' yi seçin:
   * Proje için bir ad girin.
   * **JRE** alanında, **bir yürütme ortamı kullanın JRE** 'nin **javaı-1,7** veya üzeri olarak ayarlandığından emin olun.
   * **Spark kitaplığı** alanında, **Spark SDK 'Sını yapılandırmak için Maven 'i kullanın** seçeneğini belirleyebilirsiniz.  Aracımız Spark SDK ve Scala SDK için uygun sürümü tümleştirir. Spark SDK 'yı **El Ile Ekle** seçeneğini de seçebilirsiniz, El Ile Spark SDK 'yı indirebilir ve ekleyebilirsiniz.

   ![Yeni HDInsight Scala projesi iletişim kutusu](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. Sonraki iletişim kutusunda **son**' u seçin.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark kümesi için bir Scala uygulaması oluşturma

1. Çakışan Küreler IDE ' de, Paket Gezgini ' nde, daha önce oluşturduğunuz projeyi genişletin, **src**' a sağ tıklayın, **Yeni**' nin üzerine gelin ve ardından **diğer**' i seçin.

1. **Sihirbaz Seç** iletişim kutusunda, **Scala sihirbazları**' nı genişletin, **Scala nesnesi**' ni seçin ve ardından **İleri**' yi seçin.

   ![Bir Scala nesnesi oluşturma Sihirbazı seçin](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)
1. **Yeni dosya oluştur** iletişim kutusunda, nesne için bir ad girin ve ardından **son**' u seçin.

   ![Yeni dosya Sihirbazı yeni dosya oluştur](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)
1. Aşağıdaki kodu metin düzenleyicisine yapıştırın:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasb:///HVACOut")
        }        
    }
    ```

1. Uygulamayı bir HDInsight Spark kümesinde çalıştırın:

   a. Paket Gezgini ' nden proje adına sağ tıklayın ve ardından **Spark uygulamasını HDInsight 'A gönder**' i seçin.

   b. **Spark gönderimi** iletişim kutusunda aşağıdaki değerleri sağlayın ve ardından **Gönder**' i seçin:

   * **Küme adı**için, uygulamanızı çalıştırmak Istediğiniz HDInsight Spark kümesini seçin.
   * Çakışan Küreler projesinden bir yapıt seçin ya da bir sabit sürücüden birini seçin. Varsayılan değer, Paket Gezgini ' nde sağ tıkladığınıza göre değişir.
   * **Ana sınıf adı** açılır listesinde, gönderim Sihirbazı projenizdeki tüm nesne adlarını görüntüler. Çalıştırmak istediğiniz bir tane seçin veya girin. Bir sabit sürücüden yapıt seçtiyseniz, ana sınıf adını el ile girmeniz gerekir. 
   * Bu örnekteki uygulama kodu herhangi bir komut satırı bağımsız değişkeni ya da JARs veya dosya başvurusu gerektirmediğinden, kalan metin kutularını boş bırakabilirsiniz.

     ![Apache Spark gönderimi iletişim kutusu](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. **Spark gönderim** sekmesi ilerlemeyi görüntülemeye başlamalıdır. **Spark gönderim** penceresindeki kırmızı düğmeyi seçerek uygulamayı durdurabilirsiniz. Ayrıca, bu belirli uygulama için günlükleri görüntülemek için dünya simgesini seçin (görüntüdeki mavi kutusuyla gösterilir).

   ![Apache Spark gönderme penceresi](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse 'de HDInsight araçlarını kullanarak HDInsight Spark kümelerine erişin ve yönetin

İş çıktısına erişme dahil olmak üzere HDInsight araçlarını kullanarak çeşitli işlemler gerçekleştirebilirsiniz.

### <a name="access-the-job-view"></a>İş görünümüne erişin

1. Azure Explorer 'da **HDInsight**' ı genişletin, Spark küme adını genişletin ve ardından **işler**' i seçin.

   ![Azure Gezgini tutulma iş görünümü düğümü](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. **İşler** düğümünü seçin. Java sürümü **1,8**'den düşükse, HDInsight araçları otomatik olarak **E (FX) clipu** eklentisini yüklersiniz. Devam etmek için **Tamam** ' ı seçin ve ardından, çakışan küreler marketi 'nden yüklemek için Sihirbazı izleyin.

   ![Eksik eklenti E (FX) panoları 'nı yükler](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. **İşler** düğümünden iş görünümünü açın. Sağ bölmede **Spark Iş görünümü** sekmesi kümede çalıştırılan tüm uygulamaları görüntüler. Daha fazla ayrıntı görmek istediğiniz uygulamanın adını seçin.

   ![Apache tutulma görünümü iş günlüğü ayrıntıları](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Daha sonra bu eylemlerden herhangi birini gerçekleştirebilirsiniz:

   * İş grafiğinde üzerine gelin. Çalışan iş hakkında temel bilgileri görüntüler. İş grafiğini seçin ve her işin ürettiği aşamaları ve bilgileri görebilirsiniz.

     ![Apache Spark iş grafiği aşama bilgisi](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * **Sürücü stderr**, **sürücü stdout**ve **Dizin bilgileri**de dahil olmak üzere sık kullanılan günlükleri görüntülemek için **günlük** sekmesini seçin.

     ![Apache Spark tutulma iş günlüğü bilgileri](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Pencerenin üst kısmındaki köprüleri seçerek Spark geçmişi Kullanıcı arabirimini ve Apache Hadoop YARN Kullanıcı arabirimini (uygulama düzeyinde) açın.

### <a name="access-the-storage-container-for-the-cluster"></a>Küme için depolama kapsayıcısına erişin

1. Azure Explorer 'da, kullanılabilir HDInsight Spark kümelerinin bir listesini görmek için **HDInsight** kök düğümünü genişletin.

1. Depolama hesabını ve kümenin varsayılan depolama kapsayıcısını görmek için küme adını genişletin.

   ![Depolama hesabı ve varsayılan depolama kapsayıcısı](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Kümeyle ilişkili depolama kapsayıcısı adını seçin. Sağ bölmede **Hvacout** klasörünü çift tıklatın. Uygulamanın çıkışını görmek için **bölüm** dosyalarından birini açın.

### <a name="access-the-spark-history-server"></a>Spark geçmiş sunucusuna erişin

1. Azure Explorer 'da Spark kümeniz adına sağ tıklayın ve ardından **Spark geçmişi Kullanıcı arabirimini aç**' ı seçin. İstendiğinde, kümenin yönetici kimlik bilgilerini girin. Kümeyi sağlarken bunları belirtmelisiniz.

1. Spark geçmiş sunucusu panosunda, az önce çalıştırmayı tamamlamış uygulamayı aramak için uygulama adını kullanırsınız. Yukarıdaki kodda `val conf = new SparkConf().setAppName("MyClusterApp")`kullanarak uygulama adını ayarlarsınız. Bu nedenle, Spark uygulamanızın adı **Myclusterapp**idi.

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

Yerel Spark Scala uygulamasını bir Windows bilgisayarında çalıştırırken [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)' de anlatıldığı gibi bir özel durum alabilirsiniz. Bu özel durum, Windows 'ta **Winutils. exe** eksik olduğu için oluşur.

Bu hatayı çözmek için [yürütülebilir dosyayı](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) **C:\wintıı s\bin**gibi bir konuma indirmeniz ve sonra **HADOOP_HOME** ortam değişkenini ekleyip değişkenin değerini **c\winutils**olarak ayarlamanız gerekir.

### <a name="run-a-local-spark-scala-application"></a>Yerel Spark Scala uygulaması çalıştırma

1. Çakışan Küreler başlatın ve bir proje oluşturun. **Yeni proje** iletişim kutusunda aşağıdaki seçimleri yapın ve ardından **İleri**' yi seçin.

   * Sol bölmede **HDInsight**’ı seçin.
   * Sağ bölmede, **HDInsight yerel çalıştırma örneği ' nde (Scala) Spark**' ı seçin.

   ![Yeni proje sihirbaz iletişim kutusu seçme](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Proje ayrıntılarını sağlamak için, önceki bölümde bulunan 3 ile 6 arasındaki adımları izleyerek [HDInsight Spark kümesi için bir Spark Scala projesi kurun](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Şablon, bilgisayarınızda yerel olarak çalıştırabileceğiniz **src** klasörünün altına bir örnek kod (**logquery**) ekler.

   ![LogQuery yerel Scala uygulamasının konumu](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. **Logquery** uygulamasına sağ tıklayın, **Farklı Çalıştır**' ın üzerine gelin ve ardından **1 Scala uygulaması**' nı seçin. Aşağıdakine benzer bir çıktı, **konsol** sekmesinde görünür:

   ![Spark uygulaması yerel çalıştırma sonucu](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Yalnızca okuyucu rolü

Kullanıcılar işi yalnızca okuyucu rolü izni olan bir kümeye gönderdiğinde, ambarı kimlik bilgileri gereklidir.

### <a name="link-cluster-from-context-menu"></a>Bağlam menüsünden küme bağlama

1. Yalnızca okuyucu rol hesabıyla oturum açın.

2. **Azure Explorer**'da, aboneliğinizdeki HDInsight kümelerini görüntülemek için **HDInsight** ' ı genişletin. **"Role: Reader"** olarak işaretlenen kümelerin yalnızca yalnızca okuyucu rolü izni vardır.

    ![Azure Gezgini rol okuyucudaki HDInsight Spark kümeleri](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Yalnızca okuyucu rolü izniyle kümeye sağ tıklayın. **Bu kümeyi** bağlama menüsünden Bağla ' yı seçin. Ambarı Kullanıcı adı ve parolasını girin.

    ![Azure Explorer 'da HDInsight Spark kümeleri bağlantısı](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Küme başarıyla bağlanmışsa, HDInsight yenilenir.
   Kümenin aşaması bağlantılı hale gelir.
  
    ![Azure Explorer 'da HDInsight Spark kümeleri bağlı](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Işler düğümünü genişleterek küme bağlama

1. **İşler** düğümü ' ne tıklayın, **küme işi erişimi reddedildi** penceresi açılır.

2. **Bu kümeyi** bağlantı kümesine bağla ' ya tıklayın.

    ![Azure 'da HDInsight Spark kümeleri Explorer9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Kümeyi Spark gönderim penceresinden bağla

1. HDInsight projesi oluşturun.

2. Pakete sağ tıklayın. Ardından **Spark uygulamasını HDInsight 'A gönder**' i seçin.

   ![Azure Explorer 'da HDInsight Spark kümeleri gönderme](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. **Küme adı**için yalnızca okuyucu rolü iznine sahip olan bir küme seçin. Uyarı iletisi görüntülenir. **Bu kümeyi** bağlantı kümesine bağla ' ya tıklayabilirsiniz.

   ![Azure Explorer 'da HDInsight Spark kümeleri bunu bağla](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Depolama hesaplarını görüntüleme

* Yalnızca okuyucu rolü iznine sahip kümeler için **depolama hesapları** düğümü ' ne tıklayın, **depolama erişimi reddedildi** penceresi açılır.

   ![Azure Explorer depolamada HDInsight Spark kümeleri](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![Azure Explorer 'da HDInsight Spark kümeleri reddedildi](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* Bağlantılı kümeler için **depolama hesapları** düğümü ' ne tıklayın, **depolama erişimi reddedildi** penceresi açılır.

   ![Azure Explorer 'da HDInsight Spark kümeleri denied2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Bilinen sorunlar

Bir kümeyi bağladığınızda, depolama kimlik bilgilerini sağlamanızı öneriyorum.

![küme, depolama kimlik bilgilerinin tutulma ile bağlantı](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

İşleri göndermek için iki mod vardır. Depolama kimlik bilgileri sağlanmışsa, işi göndermek için Batch modu kullanılacaktır. Aksi takdirde etkileşimli mod kullanılacaktır. Küme meşgulse, aşağıdaki hatayı alabilirsiniz.

![Çakışan tutulma, küme meşgul olduğunda hata alır](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "Çakışan tutulma, küme meşgul olduğunda hata alır")

![küme meşgul olduğunda tutulma hatası al Yarn](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "küme meşgul olduğunda tutulma hatası al Yarn")

## <a name="seealso"></a>Ayrıca bkz.

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
* [VPN aracılığıyla Apache Spark uygulamalarında uzaktan hata ayıklamak için Azure Toolkit for IntelliJ kullanma](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ kullanarak Apache Spark uygulamalarda SSH aracılığıyla uzaktan hata ayıklayın](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight 'ta Apache Spark kümesiyle Apache Zeppelin not defterlerini kullanma](apache-spark-zeppelin-notebook.md)
* [HDInsight için Apache Spark kümesindeki Jupyter Not defteri için kullanılabilir kernels](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter not defterleri ile dış paketleri kullanma](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter’i bilgisayarınıza yükleme ve bir HDInsight Spark kümesine bağlanma](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Kaynakları yönetme

* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)

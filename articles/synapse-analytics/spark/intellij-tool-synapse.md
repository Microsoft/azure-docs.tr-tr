---
title: Öğretici-Azure Toolkit for IntelliJ (Spark uygulaması)
description: Öğretici-Scala 'da yazılmış Spark uygulamaları geliştirmek ve bunları sunucusuz Apache Spark havuzuna göndermek için Azure Toolkit for IntelliJ kullanın.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: a4512a8f75d59f617058a50846d7578a90b9d062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675814"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Öğretici: SYNAPSE çalışma alanı kullanarak IntelliJ ile Apache Spark uygulaması oluşturma

Bu öğreticide, Azure Toolkit for IntelliJ eklentisinin [Scala](https://www.scala-lang.org/)'da yazılmış Apache Spark uygulamalar geliştirme ve sonra doğrudan IntelliJ tümleşik geliştirme ORTAMıNDAN (IDE) daha az bir Apache Spark havuzuna göndermesi gösterilmektedir. Eklentiyi birkaç şekilde kullanabilirsiniz:

- Spark havuzunda bir Scala Spark uygulaması geliştirin ve gönderebilirsiniz.
- Spark havuzlarınızın kaynaklarına erişin.
- Yerel olarak bir Scala Spark uygulaması geliştirin ve çalıştırın.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> - Azure Toolkit for IntelliJ eklentisini kullanma
> - Apache Spark uygulamaları geliştirme
> - Uygulamayı Spark havuzlarına gönder

## <a name="prerequisites"></a>Önkoşullar

- [IntelliJ fikir topluluk sürümü](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Azure araç seti eklentisi 3.27.0-2019.2 – [IntelliJ eklenti deposundan](/java/azure/intellij/azure-toolkit-for-intellij-installation) Install
- [JDK (sürüm 1,8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala eklentisi – [IntelliJ eklenti deposundan](../../hdinsight/spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea)Install.
- Aşağıdaki önkoşul yalnızca Windows kullanıcılarına yöneliktir:

  Yerel Spark Scala uygulamasını bir Windows bilgisayarında çalıştırırken [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)' de açıklandığı gibi bir özel durum alabilirsiniz. Windows 'da WinUtils.exe eksik olduğu için özel durum oluşur.
  Bu hatayı çözmek için [Winutils yürütülebilirini](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) **C:\Win, \ bin** gibi bir konuma indirin. Ardından **HADOOP_HOME** ortam değişkenini ekleyin ve değişkenin değerini **c:\winutils** olarak ayarlayın.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Spark havuzu için Spark Scala uygulaması oluşturma

1. IntelliJ FIKRINI başlatın ve yeni proje **Oluştur** ' u seçerek **Yeni proje** penceresini açın.
2. Sol bölmeden **Apache Spark/HDInsight** ' ı seçin.
3. Ana penceredeki **Spark projesi örneklerle (Scala)** öğesini seçin.
4. **Yapı aracı** açılan listesinden aşağıdaki türlerden birini seçin:

   - Scala projesi oluşturma Sihirbazı desteği için **Maven** .
   - Scala projesi için bağımlılıkları ve oluşturmayı yönetmek üzere **SBT** .

    ![IntelliJ fıkır yeni proje iletişim kutusu](./media/intellij-tool-synapse/create-synapse-application01.png)

5. **İleri**’yi seçin.
6. **Yeni proje** penceresinde, aşağıdaki bilgileri sağlayın:

    | Özellik | Açıklama |
    | ----- | ----- |
    |Proje adı| Bir ad girin. Bu öğreticide `myApp` kullanılır.|
    |Proje &nbsp; konumu| Projenizi kaydetmek için istenen konumu girin.|
    |Proje SDK 'Sı| Bu, ilk fıkrın kullanımı üzerinde boş olabilir. **Yeni...** öğesini seçin ve JDK 'nize gidin.|
    |Spark sürümü|Oluşturma Sihirbazı Spark SDK ve Scala SDK için doğru sürümü tümleştirir. SYNAPSE yalnızca **Spark 2.4.0** destekler.|
    |||

    ![Apache Spark SDK 'sını seçme](./media/intellij-tool-synapse/create-synapse-application02.png)

7. **Son**'u seçin. Projenin kullanılabilir hale gelmesi birkaç dakika sürebilir.
8. Spark projesi sizin için otomatik olarak bir yapı oluşturur. Yapıtı görüntülemek için aşağıdaki işletim sistemlerini yapın:

   a. Menü çubuğundan **Dosya**  >  **projesi yapısına git...**.

   b. **Proje yapısı** penceresinde **yapıtlar**' ı seçin.

   c. Yapıtı görüntüledikten sonra **iptal** ' i seçin.

    ![İletişim kutusunda yapıt bilgisi](./media/intellij-tool-synapse/default-artifact-dialog.png)

9.  **MyApp**  >  **src**  >  **Main**  >  **Scala** >  **örnek** >  **logquery**'den logquery bulun. Bu öğretici, çalıştırmak için **Logquery** kullanır.

   ![Projeden bir Scala sınıfı oluşturma komutları](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Spark havuzlarınız ile bağlantı kurmak

Spark havuzlarınız ile bağlantı kurmak için Azure aboneliğinde oturum açın.

### <a name="sign-in-to-your-azure-subscription"></a>Azure aboneliğinizde oturum açın

1. Menü çubuğunda, **Görünüm**  >  **Aracı Windows**  >  **Azure Gezgini**' ne gidin.

   ![IntelliJ fıkır Azure Gezginini gösterir](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Azure Gezgini 'nden **Azure** düğümüne sağ tıklayın ve ardından **oturum aç**' ı seçin.

   ![IntelliJ fıkır Gezgini, Azure 'a sağ tıklayın](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. **Azure oturum açma** Iletişim kutusunda **cihaz oturumu açma**' yı ve ardından **oturum aç**' ı seçin.

    ![IntelliJ fıkır Azure oturum açma](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. **Azure cihaz oturum açma** Iletişim kutusunda **Kopyala&aç**' ı seçin.

   ![IntelliJ fıkır Azure cihaz oturum açma](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Tarayıcı arabiriminde kodu yapıştırın ve sonra **İleri**' yi seçin.

   ![Microsoft HDI için kod iletişim kutusu giriyor](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Azure kimlik bilgilerinizi girin ve ardından tarayıcıyı kapatın.

   ![Microsoft HDI için e-posta iletişim kutusu giriyor](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Oturum açtıktan sonra, **abonelikler Seç** iletişim kutusunda kimlik bilgileriyle Ilişkili tüm Azure abonelikleri listelenir. Aboneliğinizi seçin ve ardından **Seç**' i seçin.

    ![Abonelik Seç iletişim kutusu](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. **Azure Gezgini**'nden aboneliklerinizde bulunan çalışma alanlarını görüntülemek için **SYNAPSE üzerindeki Apache Spark** ' ı genişletin.

    ![IntelliJ fıkır Azure Explorer ana görünümü](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Spark havuzlarını görüntülemek için, bir çalışma alanını daha da genişletebilirsiniz.

    ![Azure Gezgini depolama hesapları](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Spark havuzunda uzak bir Spark Scala uygulaması çalıştırma

Bir Scala uygulaması oluşturduktan sonra, uzaktan çalıştırabilirsiniz.

1. Simgeyi seçerek **çalıştırma/hata ayıklama yapılandırması** penceresini açın.

    ![Spark uygulamasını HDInsight 'a Gönder komutu 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. **Çalıştır/hata ayıkla yapılandırma** iletişim penceresinde, öğesini seçin **+** ve sonra **SYNAPSE üzerinde Apache Spark**' yi seçin.

    ![Spark uygulamasını HDInsight 'a Gönder komutu 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. **Çalıştır/hata ayıkla yapılandırma** penceresinde, aşağıdaki değerleri girin ve ardından **Tamam**' ı seçin:

    |Özellik |Değer |
    |----|----|
    |Spark havuzları|Uygulamanızı çalıştırmak istediğiniz Spark havuzlarını seçin.|
    |Göndermek için bir yapıt seçin|Varsayılan ayarı bırakın.|
    |Ana sınıf adı|Varsayılan değer, seçili dosyadaki ana sınıftır. Üç nokta (**...**) simgesini seçip başka bir sınıf seçerek sınıfı değiştirebilirsiniz.|
    |İş yapılandırması|Varsayılan anahtar ve değerleri değiştirebilirsiniz. Daha fazla bilgi için bkz. [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Komut satırı bağımsız değişkenleri|Gerekirse ana sınıf için boşlukla ayrılmış bağımsız değişkenler girebilirsiniz.|
    |Başvurulan Jliler ve başvurulan dosyalar|Varsa, başvurulan jars ve dosyalar için yollar girebilirsiniz. Yalnızca ADLS 2. kümesini destekleyen Azure sanal dosya sistemindeki dosyalara da gidebilirsiniz. Daha fazla bilgi için: [Apache Spark Configuration] https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) ve [kaynakları kümeye yükleme](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |İş yükleme depolaması|Ek seçenekleri açığa çıkarmak için genişletin.|
    |Depolama Türü|Karşıya yüklemek için **Azure Blob 'U kullan** ' ı seçin veya açılan listeden **karşıya yüklemek üzere küme varsayılan depolama hesabını kullanın** .|
    |Depolama Hesabı|Depolama hesabınızı girin.|
    |Depolama anahtarı|Depolama anahtarınızı girin.|
    |Depolama kapsayıcısı|**Depolama hesabı** ve **depolama anahtarı** girildikten sonra, açılan listeden depolama kapsayıcınızı seçin.|

    ![Spark gönderimi iletişim kutusu 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Projenizi seçili Spark havuzuna göndermek için **mini** veri simgesini seçin. **Küme Içindeki uzak Spark işi** , en altta iş yürütme ilerleme durumunu görüntüler. Kırmızı düğmeyi seçerek uygulamayı durdurabilirsiniz.

    ![Apache Spark gönderme penceresi](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Spark gönderimi iletişim kutusu 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Yerel çalıştırma/hata ayıklama Apache Spark uygulamaları

Apache Spark işiniz için yerel çalıştırmayı ve yerel hata ayıklamayı ayarlamak üzere aşağıdaki yönergeleri izleyebilirsiniz.

### <a name="scenario-1-do-local-run"></a>Senaryo 1: Yerel çalıştırma

1. **Çalıştır/hata ayıkla yapılandırma** iletişim kutusunu açın, artı işaretini ( **+** ) seçin. Ardından **Apache Spark on SYNAPSE** seçeneğini belirleyin. Kaydedilecek **ad**, **ana sınıf adı** bilgilerini girin.

    ![IntelliJ çalıştırılan hata ayıklama yapılandırması yerel çalıştırma 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - Ortam değişkenleri ve WinUtils.exe konumu yalnızca Windows kullanıcılarına yöneliktir.
    - Ortam değişkenleri: sistem ortam değişkeni, daha önce ayarladıysanız ve el ile eklemesi gerekmiyorsa otomatik olarak algılanabilir.
    - [WinUtils.exe konumu](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): sağdaki klasör simgesini seçerek winutils konumunu belirtebilirsiniz.

2. Sonra yerel Oynat düğmesini seçin.

    ![IntelliJ çalıştırma hata ayıklama yapılandırması yerel çalıştırma 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Yerel çalıştırma tamamlandığında, komut dosyası çıkış içeriyorsa, çıkış dosyasını **veri**  >  **__varsayılanındaki__** kontrol edebilirsiniz.

    ![IntelliJ projesi yerel çalıştırma sonucu 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Senaryo 2: yerel hata ayıklamayı do

1. **Logquery** betiğini açın, kesme noktaları ayarlayın.
2. Yerel hata ayıklamayı yapmak için **yerel hata ayıklama** simgesini seçin.

    ![IntelliJ projesi yerel çalıştırma sonucu 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>SYNAPSE çalışma alanına erişin ve yönetin

Azure Toolkit for IntelliJ içinde Azure Explorer 'da farklı işlemler gerçekleştirebilirsiniz. Menü çubuğunda, **Görünüm**  >  **Aracı Windows**  >  **Azure Gezgini**' ne gidin.

### <a name="launch-workspace"></a>Çalışma alanını Başlat

1. Azure Gezgini 'nden **SYNAPSE üzerinde Apache Spark** gidin ve ardından genişletin.

    ![IntelliJ fıkır Azure Explorer ana görünümü](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Bir çalışma alanına sağ tıklayın ve sonra **çalışma alanını Başlat**' ı seçin, Web sitesi açılır.

    ![Spark Iş görünümü uygulama ayrıntıları 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Spark Iş görünümü uygulama ayrıntıları 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark konsolu

Spark yerel konsolu 'Nu (Scala) çalıştırabilir veya Spark Livy etkileşimli oturum konsolunu (Scala) çalıştırabilirsiniz.

### <a name="spark-local-console-scala"></a>Spark yerel Konsolu (Scala)

WINUTILS.EXE önkoşulu karşılatığınızdan emin olun.

1. Menü çubuğundan **Çalıştır**  >  **yapılandırma yapılandırma...**' ya gidin.
2. **Çalıştır/hata ayıkla yapılandırma** penceresinde, sol bölmede **SYNAPSE**  >  **[Spark on SYNAPSE] MyApp**' de Apache Spark ' a gidin.
3. Ana pencereden **yerel olarak çalıştır** sekmesini seçin.
4. Aşağıdaki değerleri girip **Tamam**' ı seçin:

    |Özellik |Değer |
    |----|----|
    |Ortam değişkenleri|HADOOP_HOME değerinin doğru olduğundan emin olun.|
    |WINUTILS.exe konumu|Yolun doğru olduğundan emin olun.|

    ![Yerel konsol kümesi yapılandırması](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Projeden **MyApp**  >  **src**  >  **Main**  >  **Scala**  >  **MyApp** sayfasına gidin.
6. Menü çubuğundan **Araçlar**  >  **Spark konsolu**  >  **Spark yerel konsoluna (Scala)** gidin.
7. Ardından, bağımlılıkları otomatik olarak onarmak isteyip istemediğinizi sormak için iki iletişim kutusu görüntülenebilir. Öyleyse **otomatik çözüm**' i seçin.

    ![IntelliJ fıkır Spark otomatik çözüm Dialog1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ fıkır Spark otomatik çözüm dialog2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. Konsol aşağıdaki resme benzer görünmelidir. Konsol penceresi yazın `sc.appName` ve ardından CTRL + ENTER tuşlarına basın. Sonuç görüntülenecektir. Yerel konsolu kırmızı düğme ' i seçerek durdurabilirsiniz.

    ![IntelliJ fıkır yerel konsol sonucu](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark Livy etkileşimli oturum Konsolu (Scala)

Yalnızca IntelliJ 2018,2 ve 2018,3 ' de desteklenir.

1. Menü çubuğundan **Çalıştır**  >  **yapılandırma yapılandırma...**' ya gidin.

2. **Çalıştır/hata ayıkla yapılandırma** penceresinde, sol bölmede **SYNAPSE**  >  **[Spark on SYNAPSE] MyApp**' de Apache Spark ' a gidin.

3. Ana pencereden, **küme Içinde uzaktan Çalıştır** sekmesini seçin.

4. Aşağıdaki değerleri girip **Tamam**' ı seçin:

    |Özellik |Değer |
    |----|----|
    |Ana sınıf adı| Ana sınıf adını seçin.| 
    |Spark havuzları|Uygulamanızı çalıştırmak istediğiniz Spark havuzlarını seçin.|
    ||

    ![Etkileşimli konsol ayarlama yapılandırması](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Projeden **MyApp**  >  **src**  >  **Main**  >  **Scala**  >  **MyApp** sayfasına gidin.

6. Menü çubuğundan **Araçlar**  >  **Spark konsolu**,  >  **Spark Livy etkileşimli oturum konsolu 'nu (Scala) çalıştırın**.
7. Konsol aşağıdaki resme benzer görünmelidir. Konsol penceresi yazın `sc.appName` ve ardından CTRL + ENTER tuşlarına basın. Sonuç görüntülenecektir. Yerel konsolu kırmızı düğme ' i seçerek durdurabilirsiniz.

    ![IntelliJ fıkır etkileşimli konsol sonucu](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Seçimi Spark konsoluna gönder

Yerel konsola veya Livy etkileşimli oturum konsoluna (Scala) bazı kodlar göndererek betik sonucunu görmek isteyebilirsiniz. Bunu yapmak için, Scala dosyasındaki bazı kodları vurgulayabilir ve sonra **seçimi Spark konsoluna Gönder ' e** sağ tıklayabilirsiniz. Seçili kod konsola gönderilir ve bu işlem yapılır. Sonuç, konsolundaki koddan sonra görüntülenecektir. Konsol var olan hataları kontrol eder.

   ![Seçimi Spark konsoluna gönder](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Synapse Analytics](../overview-what-is.md)
- [Azure SYNAPSE Analytics çalışma alanı için yeni bir Apache Spark havuzu oluşturma](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
---
title: Apache Hadoop & Visual Studio Data Lake araçları-Azure HDInsight
description: Visual Studio için Data Lake araçları 'nı yüklemeyi ve kullanmayı ve Azure HDInsight 'ta Apache Hadoop kümelerine bağlanmayı ve ardından Hive sorguları çalıştırmayı öğrenin.
keywords: hadoop araçları, hive sorgusu, visual studio, visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825072"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Visual Studio için Data Lake araçları 'nı kullanarak Azure HDInsight 'a bağlanma ve Apache Hive sorguları çalıştırma

[Azure HDInsight 'ta Apache Hadoop kümelerine](apache-hadoop-introduction.md) bağlanmak ve Hive sorguları göndermek üzere Visual Studio için Microsoft Azure Data Lake ve Stream Analytics araçları 'nı (Data Lake araçları olarak da bilinir) nasıl kullanacağınızı öğrenin.  

HDInsight kullanma hakkında daha fazla bilgi için bkz. [HDInsight kullanmaya başlama](apache-hadoop-linux-tutorial-get-started.md).  

Apache Storm kümesine bağlanma hakkında daha fazla bilgi için bkz. [Visual Studio C# için Data Lake araçları 'nı kullanarak Apache Storm için topoloji geliştirme](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Visual Studio için Data Lake Araçlarını hem Azure Data Lake Analytics’e hem de HDInsight’a erişmek için kullanabilirsiniz. Data Lake Araçları hakkında bilgi için bkz. [Visual Studio için Data Lake Araçları'nı kullanarak U-SQL betikleri geliştirme](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Ön koşullar

Bu makaleyi tamamlayıp Visual Studio için Data Lake araçları 'nı kullanmak için aşağıdaki öğeler gereklidir:

* Bir Azure HDInsight kümesi. HDInsight kümesi oluşturmak için bkz. [Azure HDInsight 'ta Apache Hadoop kullanmaya başlama](apache-hadoop-linux-tutorial-get-started.md). Etkileşimli Apache Hive sorguları çalıştırmak için [HDInsight etkileşimli sorgu](../interactive-query/apache-interactive-query-get-started.md) kümesine ihtiyacınız vardır.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). [Visual Studio Community sürümü](https://visualstudio.microsoft.com/vs/community/) ücretsizdir. Burada gösterilen yönergeler [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)içindir.

## <a name="install-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake Araçları’nı yükleme.  

Visual Studio sürümünüze yönelik Data Lake araçları yüklemek için uygun yönergeleri izleyin:

- Visual Studio 2017 veya Visual Studio 2019 için:

    Visual Studio yüklemesi sırasında, **Azure geliştirme** iş yükü veya **veri depolama ve işleme** iş yükünü dahil ettiğinizden emin olun.  

    Mevcut Visual Studio yüklemeleri için IDE menü çubuğuna gidin **ve Araçlar ' ı seçerek Visual Studio yükleyicisi** açmak üzere Araçlar **ve Özellikler alın** > . **Iş yükleri** sekmesinde, en az **Azure geliştirme** iş yükünü ( **Web & bulutu**altında) veya **veri depolama ve işleme** iş yükünü ( **diğer araç kümeleri**altında) seçin.

  ![İş yükü seçimi, Visual Studio Yükleyicisi](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- Visual Studio 2015 için:

    [Data Lake araçlarını indirin](https://www.microsoft.com/download/details.aspx?id=49504). Visual Studio sürümünüzle eşleşen Data Lake Araçları sürümünü seçin.

## <a name="update-data-lake-tools-for-visual-studio"></a>Visual Studio için güncelleştirme Data Lake araçları  

Daha sonra, Data Lake araçlarını en son sürüme güncelleştirdiğinizden emin olun.

1. Visual Studio’yu açın.

2. **Başlangıç** penceresinde, **kod olmadan devam et**' i seçin.

3. Visual Studio IDE menü çubuğunda, uzantıları **yönet** > **Uzantılar** ' ı seçin.

4. **Uzantıları Yönet** Iletişim kutusunda **güncelleştirmeler** düğümünü genişletin.

5. Kullanılabilir güncelleştirmelerin listesi **Azure Data Lake ve Stream analitik araçları**içeriyorsa, bu seçeneği seçin. Ardından **güncelleştirme** düğmesini seçin. **İndir ve yükle** iletişim kutusu görüntülendikten ve kaybolduktan sonra, Visual Studio **Azure Data Lake ve Stream analitik araçları** uzantısını güncelleştirme zamanlamaya ekler.

6. Tüm Visual Studio pencerelerini kapatın. **VSIX yükleyicisi** iletişim kutusu görüntülenir.

7. Lisans koşullarını okumak için **Lisans** ' ı seçin ve ardından **VSIX yükleyicisi** Iletişim kutusuna dönmek için **Kapat** ' ı seçin.

8. **Değiştir**'i seçin. Uzantı güncelleştirmesi yüklemesi başlar. Bir süre sonra, iletişim kutusu değişiklik yapma işlemi yapıldığını gösterecek şekilde değişir. **Kapat**' ı seçin ve ardından Visual Studio 'yu yeniden başlatarak yüklemeyi doldurun.

> [!NOTE]  
> Etkileşimli Sorgu kümelerine bağlanmak ve etkileşimli Hive sorguları çalıştırmak için yalnızca Data Lake Araçları sürüm 2.3.0.0 veya üzerini kullanabilirsiniz.

## <a name="connect-to-azure-subscriptions"></a>Azure aboneliklerine bağlanma

Visual Studio için Data Lake araçları 'nı kullanarak HDInsight kümelerinize bağlanabilir, bazı temel yönetim işlemlerini gerçekleştirebilir ve Hive sorguları çalıştırabilirsiniz.

> [!NOTE]  
> Genel bir Hadoop kümesine bağlanma hakkında daha fazla bilgi için bkz. [Visual Studio kullanarak Hive sorguları yazma ve gönderme](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Bir Azure aboneliğine Bağlanma

Azure aboneliğinize bağlanmak için:

1. Visual Studio’yu açın.

2. **Başlangıç** penceresinde, **kod olmadan devam et**' i seçin.

3. IDE menü çubuğunda **görüntüle** > **Sunucu Gezgini**öğesini seçin.

4. **Sunucu Gezgini**' de **Azure**' a sağ tıklayın, **Microsoft Azure aboneliğine Bağlan**' ı seçin ve kimlik doğrulama işlemini doldurun. **Sunucu Gezgini**, var olan HDInsight kümelerinin bir listesini görüntülemek için **Azure** > **HDInsight** ' ı genişletin.

5. Herhangi bir kümeniz yoksa, Azure portal, Azure PowerShell veya HDInsight SDK kullanarak bir tane oluşturun. Daha fazla bilgi için bkz. [HDInsight 'ta kümeleri ayarlama](../hdinsight-hadoop-provision-linux-clusters.md).

   ![HDInsight küme listesi, Sunucu Gezgini, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. HDInsight kümesini genişletin. Küme, **Hive veritabanları**, varsayılan depolama hesabı, diğer bağlı depolama hesapları ve **Hadoop hizmeti günlüğü**için düğümler içerir. Varlıkları daha da genişletebilirsiniz.

Azure aboneliğinize bağlandıktan sonra aşağıdaki görevleri gerçekleştirebilirsiniz.

### <a name="connect-to-azure-from-visual-studio"></a>Visual Studio 'dan Azure 'a bağlanma

Visual Studio'dan Azure portalına bağlanmak için:

1. **Sunucu Gezgini**' de **Azure** > **HDInsight** ' ı genişletin ve kümenizi seçin.

2. Bir HDInsight kümesine sağ tıklayın ve **Azure Portal kümeyi Yönet**' i seçin.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Visual Studio 'dan sorularınızı ve geri bildirimleri sunun

Soru sormak ve/veya Visual Studio 'dan geri bildirim sağlamak için:

1. Sunucu Gezgini **Azure** > **HDInsight**' ı seçin.

2. **HDInsight** ' a sağ tıklayın ve soru sormak Için **MSDN Forumu** ' nu seçin ya da geri bildirimde bulunmak için **geri bildirimde** bulunun.

## <a name="link-to-or-edit-a-cluster"></a>Bir kümeyi bağlama veya düzenleme

> [!NOTE]
> Şu anda bağlantı için kullanabileceğiniz tek HDInsight kümesi türü bir Hive türüdür.

Bir HDInsight kümesini bağlamak için:

1. HDInsight ' **a** **sağ tıklayın ve**ardından HDInsight kümesi bağla iletişim kutusunu göstermek Için **bir HDInsight kümesi bağla** ' yı seçin.

2. *Https\://\<küme&nbsp;name >. azurehdinsight. net*biçiminde bir **bağlantı URL 'si** girin. Başka bir alana gittiğinizde **küme adı** URL 'nizin küme adı bölümüyle otomatik olarak doldurulur. Ardından bir **Kullanıcı adı** ve **parola**girin ve **İleri**' yi seçin.

    ![Bir kümeyi bağlama, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. **Son**’u seçin. Küme bağlama başarılı olursa, küme **HDInsight** düğümü altında listelenir.

Bağlı bir kümeyi güncelleştirmek için kümeye sağ tıklayın ve **Düzenle**' yi seçin. Daha sonra küme bilgilerini güncelleştirebilirsiniz.

![Bağlı kümeyi düzenleme, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Bağlantılı kaynakları araştırma
Sunucu Gezgini'nde, varsayılan depolama hesabını ve bağlı tüm depolama hesaplarını görebilirsiniz. Varsayılan depolama hesabını genişletirseniz, depolama hesabında kapsayıcıları görebilirsiniz. Varsayılan depolama hesabı ve varsayılan kapsayıcı işaretlenmiştir.

![Sunucu Gezgini içindeki Visual Studio bağlantılı kaynaklarına yönelik Data Lake araçları](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Kapsayıcıya sağ tıklayın ve kapsayıcının içeriğini görüntülemek için **kapsayıcıyı görüntüle** ' yi seçin. Bir kapsayıcıyı açtıktan sonra, araç çubuğu düğmelerini kullanarak içerik listesini **yenileyebilir** , **blobu yükleyebilir**, **Seçili Blobları silebilir**, **BLOB 'u açabilir**ve seçili blob 'ları indirebilirsiniz (**farklı kaydet**).

![Kapsayıcı listesi ve BLOB işlemleri, HDInsight kümesi, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Etkileşimli Apache Hive sorguları çalıştırma
[Apache Hive](https://hive.apache.org), Hadoop üzerinde oluşturulmuş bir veri ambarı altyapısıdır. Hive veri özetleme, sorgular ve analiz için kullanılır. Visual Studio’dan Hive sorguları çalıştırmak üzere Visual Studio için Data Lake Araçları’nı kullanabilirsiniz. Hive hakkında daha fazla bilgi için bkz. [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](hdinsight-use-hive.md).

[Azure HDInsight 'Ta etkileşimli sorgu](../interactive-query/apache-interactive-query-get-started.md) Apache Hive 2,1 ' de [LLAP üzerinde Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP) kullanır. Etkileşimli sorgu, büyük, depolanmış veri kümelerinde karmaşık, veri ambarı stili sorgulara etkileşim getirir. Etkileşimli sorgu üzerinde Hive sorgularının çalıştırılması, geleneksel Hive toplu iş işlemlerinden çok daha hızlıdır. 

> [!NOTE]  
> Etkileşimli Hive sorgularını yalnızca bir [HDInsight Etkileşimli Sorgu](../interactive-query/apache-interactive-query-get-started.md) kümesine bağlandığınızda çalıştırabilirsiniz.

Ayrıca, Visual Studio için Data Lake Araçları’nı kullanarak bir Hive işinin içeriğini görebilirsiniz. Visual Studio için Data Lake Araçları bazı Hive işlerinin Yarn günlüklerini toplar ve yüzeye çıkarır.

**Sunucu Gezgini** **Azure** > **HDInsight** ' ı seçin ve kümenizi seçin.  Bu düğüm, Bölüm izlemek için **Sunucu Gezgini** başlangıç noktasıdır.

### <a name="view-hivesampletable"></a>Hivesampletable 'ı görüntüle

Tüm HDInsight kümeleri `hivesampletable`adlı varsayılan bir örnek Hive tablosuna sahiptir.  

Kümeinizden **Hive veritabanları** > **varsayılan** > **hivesampletable**' ı seçin.

- `hivesampletable` şemasını görüntülemek için:

    **Hivesampletable**öğesini genişletin. `hivesampletable` sütunlarının adları ve veri türleri gösterilir.

- `hivesampletable` verilerini görüntülemek için:

    **Hivesampletable**öğesine sağ tıklayın ve **Ilk 100 satırı görüntüle**' yi seçin. 100 sonuçlarının listesi **Hive tablosu: hivesampletable** penceresinde görünür. Bu eylem, Hive ODBC sürücüsünü kullanarak aşağıdaki Hive sorgusunu çalıştırmaya eşdeğerdir:

    `SELECT * FROM hivesampletable LIMIT 100`

    Satır sayısını, satır **sayısını**değiştirerek özelleştirebilirsiniz. aşağı açılan listeden 50, 100, 200 veya 1000 satırları seçebilirsiniz.

### <a name="create-hive-tables"></a>Hive tabloları oluşturma
Bir Hive tablosu oluşturmak için GUI’yi ya da Hive sorgularını kullanabilirsiniz. Hive sorguları kullanma hakkında daha fazla bilgi için bkz. [Hive sorguları oluşturma ve çalıştırma](#create-and-run-hive-queries).

1. Kümeinizden **varsayılan** > **Hive veritabanları** ' nı seçin.

2. **Varsayılan**' a sağ tıklayın ve **tablo oluştur**' u seçin.

3. Tabloyu yapılandırın.

4. Yeni Hive tablosunu oluşturan işi göndermek için **tablo oluştur** düğmesini seçin.

    ![Tablo penceresi, Hive, HDInsight kümesi, Visual Studio oluştur](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Hive sorguları oluşturma ve çalıştırma
Hive sorguları oluşturmak ve çalıştırmak için iki seçeneğiniz vardır:

* Geçici sorgular oluşturma
* Hive uygulaması oluşturma

#### <a name="create-an-ad-hoc-query"></a>Geçici sorgu oluşturma

Geçici sorgu oluşturmak ve çalıştırmak için:

1. Sorguyu çalıştırmak istediğiniz kümeye sağ tıklayın ve **Hive sorgusu yaz**' ı seçin.  

2. Hive sorgusu girin.

    Hive düzenleyicisi IntelliSense’i destekler. Visual Studio için Data Lake Araçları, Hive betiğinizi düzenlerken uzak meta verilerin yüklenmesini destekler. Örneğin, `SELECT * FROM`yazarsanız, IntelliSense önerilen tüm tablo adlarını listeler. Bir tablo adı belirtildiğinde, IntelliSense sütun adlarını listeler. Araçlar çoğu Hive DML deyimlerini, alt sorguları ve yerleşik UDF'leri destekler.

    ![IntelliSense örnek 1, Hive geçici sorgu, HDInsight kümesi, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense örnek 2, Hive geçici sorgu, HDInsight kümesi, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense yalnızca HDInsight araç çubuğunda seçilen kümelerin meta verilerini önerir.

    Kullanabileceğiniz örnek bir sorgu aşağıda verilmiştir:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Yürütme modunu seçin:

    * **Etkileşimli**  

        İlk açılan listede **etkileşimli**' i seçin ve ardından **Yürüt**' ü seçin.

        ![Etkileşimli mod, Hive geçici sorgu, HDInsight kümesi, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        İlk açılan listede **toplu işlem**' i seçin ve ardından **Gönder** ' i seçin (veya **Gönder** ' ın yanındaki açılan simgeyi seçin ve **Gelişmiş**' i seçin).

        ![Batch modu, Hive geçici sorgu, HDInsight kümesi, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Gelişmiş gönder seçeneğini belirlerseniz, **betiği gönder** iletişim kutusu görüntülenir. Betik için **Iş adı**, **bağımsız değişkenler**, **ek yapılandırma**ve **durum dizini** yapılandırın.

        ![Betik Gönder iletişim kutusu, Hive geçici sorgu, HDInsight kümesi, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Etkileşimli sorgu kümelerine toplu işler gönderemezsiniz.  Etkileşimli mod kullanmanız gerekir.

#### <a name="create-a-hive-application"></a>Hive uygulaması oluşturma

Hive çözümü oluşturmak ve çalıştırmak için:

1. Menü çubuğundan **dosya** > **Yeni** > **Proje**' yi seçin.

2. **Yeni proje oluştur** penceresinde, arama kutusunu seçin ve **Hive**yazın. Ardından **Hive uygulaması** ' nı seçin ve **İleri**' yi seçin.

3. **Yeni projeyi yapılandırın** penceresinde bir **Proje adı**girin, proje **konumunu**seçin veya oluşturun ve ardından **Oluştur**' u seçin.

    ![Yeni Hive uygulaması, yeni proje pencerenizi yapılandırma, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. Betiği açmak için **Çözüm Gezgini**’nde **Script.hql** öğesine çift tıklayın.

### <a name="view-job-summary-and-output"></a>İş özetini ve çıktıyı görüntüleme

İş Özeti, **Batch** ve **etkileşimli** mod arasında biraz farklılık gösterir.

![Hive iş Özeti pencereleri, toplu iş ve etkileşimli mod, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

İş durumu **tamamlandı**olarak değiştirilene kadar durumu güncelleştirmek için **Yenile** simgesini kullanın.  

* **Batch** modundan iş ayrıntıları Için, **iş sorgusunu**, **iş çıkışını**veya **Iş günlüğünü**görmek ya da **Yarn günlüklerini görüntülemek**için alttaki bağlantıları seçin.

* **Etkileşimli** moddan iş ayrıntıları için **Çıkış** ve **HiveServer2 çıkış** bölmeleri bölümüne bakın.

    ![Hive etkileşimli iş çıktısı, HDInsight kümesi, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>İş grafiğini görüntüle

Şu anda iş grafikleri yalnızca, yürütme altyapısı olarak tez kullanan Hive işleri için gösterilir.  Tez 'yi etkinleştirme hakkında bilgi için bkz. [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](hdinsight-use-hive.md).  Ayrıca bkz. [harita azaltma yerine Apache tez kullanın](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Köşe içindeki tüm işleçleri görüntülemek için, iş grafiğinin köşelerine çift tıklayın. Ayrıca, işleç hakkında daha fazla ayrıntı görmek için belirli bir işleci işaret edebilirsiniz.

Tez yürütme altyapısı olarak belirtilmiş olsa bile, hiçbir tez uygulaması başlatılmasa bile iş grafiği görünmeyebilir.  Bu durum, iş DML deyimlerini içermediğinden veya DML deyimleri tez uygulaması başlatmadan döndürebileceğinden bu durum oluşabilir. Örneğin, `SELECT * FROM table1` tez uygulamasını başlatamaz.

![Apache Hive iş grafiği, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Görev yürütme ayrıntılarını görüntüleme

İş Grafında, Hive işleri için yapılandırılmış ve görselleştirilmiş bilgileri almak üzere **görev yürütme ayrıntısı** ' nı seçebilirsiniz. Daha fazla iş ayrıntısı da alabilirsiniz. Performans sorunları oluşursa, sorun hakkında daha fazla bilgi almak için bu görünümü kullanabilirsiniz. Örneğin, her bir görevin nasıl çalıştığı ve her görevle ilgili ayrıntılı bilgilerin yanı sıra (veri okuma/yazma, zamanlama/başlangıç/bitiş zamanı ve daha fazlası) hakkında bilgi alabilirsiniz. İş yapılandırmalarını veya sistem mimarisini görselleştirilmiş bilgilere göre ayarlamak için bilgileri kullanın.

![Görev yürütme görünümü penceresi, Data Lake Visual Studio Araçları](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Hive İşlerini Görüntüleme

Hive işleri için iş sorguları, iş çıktısı, iş günlükleri ve Yarn günlüklerini görüntüleyebilirsiniz.

Araçların en son sürümünde Yarn günlüklerini toplayarak ve görünmesini sağlayarak Hive işlerinizin içeriğini görebilirsiniz. Yarn günlüğü, performans sorunlarını araştırmanıza yardımcı olabilir. HDInsight 'ın Yarn günlüklerini nasıl topladığı hakkında daha fazla bilgi için bkz. [erişim Apache HADOOP YARN uygulama günlükleri](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Hive işlerini görüntülemek için:

1. Bir HDInsight kümesine sağ tıklayın ve **Işleri görüntüle**' yi seçin.

    ![Işleri görüntüleme, Apache Hive, HDInsight kümesi, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Küme üzerinde çalıştırılan Hive işlerinin listesi görüntülenir.  

2. Bir iş seçin. **Hive Iş Özeti** penceresinde, aşağıdaki bağlantılardan birini seçin:
    - **İş Sorgusu**
    - **İş Çıktısı**
    - **İş Günlüğü**  
    - **Yarn günlüğü**

## <a name="run-apache-pig-scripts"></a>Apache Pig betiklerini çalıştır

1. Menü çubuğundan **dosya** > **Yeni** > **Proje**' yi seçin.

2. **Başlangıç** penceresinde arama kutusunu seçin ve **Pig**girin. Sonra **Pig uygulamasını** seçin ve **İleri**' yi seçin.

3. **Yeni projenizi yapılandırın** penceresinde bir **Proje adı**girin ve proje için bir **konum** seçin veya oluşturun. Ardından **Oluştur**’u seçin.

4. IDE **Çözüm Gezgini** bölmesinde, Script **. Pig** öğesine çift tıklayarak betiği açın.

## <a name="feedback-and-known-issues"></a>Geri bildirim ve bilinen sorunlar

* Null değerlerle başlatılan sonuçların gösterilmediği bir sorun düzeltilmiştir. Bu sorun sizi engelliyorsa destek ekibine başvurun.

* Visual Studio tarafından oluşturulan HQL betiği kullanıcının yerel bölge ayarlarına bağlı olarak kodlanır. Betiği bir kümeye ikili dosya olarak yüklerseniz betik doğru şekilde yürütülmez.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede Visual Studio’dan HDInsight kümelerine bağlanmak üzere Visual Studio için Data Lake Araçları paketini kullanmayı öğrendiniz. Ayrıca bir Hive sorgusu çalıştırmayı öğrendiniz. Daha fazla bilgi için şu makalelere bakın:

* [Visual Studio için Data Lake araçlarını kullanarak Apache Hive sorgularını çalıştırma](apache-hadoop-use-hive-visual-studio.md)
* [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](hdinsight-use-hive.md)
* [Apache Hadoop kümesi oluşturma-şablon](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight 'ta Apache Hadoop işleri gönderme](submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight üzerinde Apache Hive ve Apache Hadoop kullanarak Twitter verilerini çözümleme](../hdinsight-analyze-twitter-data-linux.md)

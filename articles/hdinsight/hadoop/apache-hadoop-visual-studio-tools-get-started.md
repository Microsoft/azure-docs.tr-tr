---
title: Apache Hadoop & Visual Studio Data Lake araçları-Azure HDInsight
description: Visual Studio için Data Lake araçları 'nı yüklemeyi ve kullanmayı öğrenin. Aracı kullanarak Azure HDInsight 'ta Apache Hadoop kümelerine bağlanın ve ardından Hive sorguları çalıştırın.
keywords: hadoop araçları, hive sorgusu, visual studio, visual studio hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: c6969c3108fb1465a705d8e61b3a7bdf2083736a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865664"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Visual Studio için Data Lake araçları 'nı kullanarak Azure HDInsight 'a bağlanma ve Apache Hive sorguları çalıştırma

Visual Studio için Microsoft Azure Data Lake ve Stream Analytics araçları 'nı (Data Lake araçları) nasıl kullanacağınızı öğrenin. Aracı kullanarak [Azure HDInsight 'ta Apache Hadoop kümelerine](apache-hadoop-introduction.md) bağlanın ve Hive sorguları gönderebilirsiniz.  

HDInsight kullanma hakkında daha fazla bilgi için bkz. [HDInsight kullanmaya başlama](apache-hadoop-linux-tutorial-get-started.md).  

Apache Storm bağlanma hakkında daha fazla bilgi için bkz. [Data Lake araçlarını kullanarak Apache Storm Için C# topolojileri geliştirme](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Visual Studio için Data Lake Araçlarını hem Azure Data Lake Analytics’e hem de HDInsight’a erişmek için kullanabilirsiniz. Data Lake Araçları hakkında bilgi için bkz. [Visual Studio için Data Lake Araçları'nı kullanarak U-SQL betikleri geliştirme](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayıp Visual Studio için Data Lake araçları 'nı kullanmak için aşağıdaki öğeler gereklidir:

* Bir Azure HDInsight kümesi. HDInsight kümesi oluşturmak için bkz. [Azure HDInsight 'ta Apache Hadoop kullanmaya başlama](apache-hadoop-linux-tutorial-get-started.md). Etkileşimli Apache Hive sorguları çalıştırmak için [HDInsight etkileşimli sorgu](../interactive-query/apache-interactive-query-get-started.md) kümesine ihtiyacınız vardır.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). [Visual Studio Community sürümü](https://visualstudio.microsoft.com/vs/community/) ücretsizdir. Burada gösterilen yönergeler [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)içindir.

## <a name="install-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake Araçları’nı yükleme.  

Visual Studio sürümünüze yönelik Data Lake araçları yüklemek için uygun yönergeleri izleyin:

* Visual Studio 2017 veya Visual Studio 2019 için:

    Visual Studio yüklemesi sırasında, **Azure geliştirme** iş yükü veya **veri depolama ve işleme** iş yükünü dahil ettiğinizden emin olun.  

    Mevcut Visual Studio yüklemeleri için IDE menü çubuğuna gidin ve   >  Visual Studio yükleyicisi açmak için Araçlar **ve Özellikler al** ' ı seçin. **Iş yükleri** sekmesinde en az **Azure geliştirme** iş yükü ( **Web & bulutu** altında) seçeneğini belirleyin. Veya **veri depolama ve işleme** iş yükünü ( **diğer araç kümeleri** altında) seçin.

  :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png" alt-text="İş yükü seçimi, Visual Studio Yükleyicisi" border="true":::

* Visual Studio 2015 için:

    [Data Lake araçlarını indirin](https://www.microsoft.com/download/details.aspx?id=49504). Visual Studio sürümünüzle eşleşen Data Lake Araçları sürümünü seçin.

## <a name="update-data-lake-tools-for-visual-studio"></a>Visual Studio için güncelleştirme Data Lake araçları  

Daha sonra, Data Lake araçlarını en son sürüme güncelleştirdiğinizden emin olun.

1. Visual Studio'yu açın.

2. **Başlangıç** penceresinde, **kod olmadan devam et**' i seçin.

3. Visual Studio IDE menü çubuğunda **Uzantılar**  >  **Yönet uzantılar**' ı seçin.

4. **Uzantıları Yönet** Iletişim kutusunda **güncelleştirmeler** düğümünü genişletin.

5. Kullanılabilir güncelleştirmelerin listesi **Azure Data Lake ve Stream analitik araçları** içeriyorsa, bu seçeneği seçin. Ardından **güncelleştirme** düğmesini seçin. **İndir ve yükle** iletişim kutusu görüntülendikten ve kaybolduktan sonra, Visual Studio **Azure Data Lake ve Stream analitik araçları** uzantısını güncelleştirme zamanlamaya ekler.

6. Tüm Visual Studio pencerelerini kapatın. **VSIX yükleyicisi** iletişim kutusu görüntülenir.

7. Lisans koşullarını okumak için **Lisans** ' ı seçin ve ardından **VSIX yükleyicisi** Iletişim kutusuna dönmek için **Kapat** ' ı seçin.

8. **Değiştir**'i seçin. Uzantı güncelleştirmesi yüklemesi başlar. Bir süre sonra, iletişim kutusu değişiklik yapma işlemi yapıldığını gösterecek şekilde değişir. **Kapat**' ı seçin ve ardından Visual Studio 'yu yeniden başlatarak yüklemeyi doldurun.

> [!NOTE]  
> Etkileşimli Sorgu kümelerine bağlanmak ve etkileşimli Hive sorguları çalıştırmak için yalnızca Data Lake Araçları sürüm 2.3.0.0 veya üzerini kullanabilirsiniz.

## <a name="connect-to-azure-subscriptions"></a>Azure aboneliklerine bağlanma

Visual Studio için Data Lake araçları 'nı kullanarak HDInsight kümelerinize bağlanabilir, bazı temel yönetim işlemlerini gerçekleştirebilir ve Hive sorguları çalıştırabilirsiniz.

> [!NOTE]  
> Genel bir Hadoop kümesine bağlanma hakkında daha fazla bilgi için bkz. [Visual Studio kullanarak Hive sorguları yazma ve gönderme](/archive/blogs/xiaoyong/how-to-write-and-submit-hive-queries-using-visual-studio).

### <a name="connect-to-an-azure-subscription"></a>Bir Azure aboneliğine Bağlanma

Azure aboneliğinize bağlanmak için:

1. Visual Studio'yu açın.

2. **Başlangıç** penceresinde, **kod olmadan devam et**' i seçin.

3. IDE menü çubuğunda Sunucu Gezgini **görüntüle**' yi seçin  >  .

4. **Sunucu Gezgini**' de **Azure**' a sağ tıklayın, **Microsoft Azure aboneliğine Bağlan**' ı seçin ve kimlik doğrulama işlemini doldurun. **Sunucu Gezgini**,   >  var olan HDInsight kümelerinin bir listesini görüntülemek için Azure **HDInsight** ' ı genişletin.

5. Herhangi bir kümeniz yoksa, Azure portal, Azure PowerShell veya HDInsight SDK kullanarak bir tane oluşturun. Daha fazla bilgi için bkz. [HDInsight 'ta kümeleri ayarlama](../hdinsight-hadoop-provision-linux-clusters.md).

   :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png" alt-text="HDInsight küme listesi, Sunucu Gezgini, Visual Studio" border="true":::

6. HDInsight kümesini genişletin. Küme, **Hive veritabanları** için düğümler içerir. Ayrıca, varsayılan depolama hesabı, ek bağlı depolama hesapları ve **Hadoop hizmeti günlüğü**. Varlıkları daha da genişletebilirsiniz.

Azure aboneliğinize bağlandıktan sonra aşağıdaki görevleri gerçekleştirebilirsiniz.

### <a name="connect-to-azure-from-visual-studio"></a>Visual Studio 'dan Azure 'a bağlanma

Visual Studio'dan Azure portalına bağlanmak için:

1. **Sunucu Gezgini**' de, **Azure**  >  **HDInsight** ' ı genişletin ve kümenizi seçin.

2. Bir HDInsight kümesine sağ tıklayın ve **Azure Portal kümeyi Yönet**' i seçin.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Visual Studio 'dan sorularınızı ve geri bildirimleri sunun

Visual Studio 'dan soru sormak ve geri bildirim sağlamak için:

1. Sunucu Gezgini **Azure**  >  **HDInsight**' ı seçin.

2. **HDInsight** ' a sağ tıklayın ve soru sormak Için **MSDN Forumu** ' nu seçin ya da geri bildirimde bulunmak için **geri bildirimde** bulunun.

## <a name="link-to-or-edit-a-cluster"></a>Bir kümeyi bağlama veya düzenleme

> [!NOTE]
> Şu anda bağlantı için kullanabileceğiniz tek HDInsight kümesi türü bir Hive türüdür.

Bir HDInsight kümesini bağlamak için:

1. HDInsight ' **a** **sağ tıklayın ve** ardından HDInsight kümesi bağla iletişim kutusunu göstermek Için **bir HDInsight kümesi bağla** ' yı seçin.

2. Forma bir **bağlantı URL 'si** girin `https://CLUSTERNAME.azurehdinsight.net` . Başka bir alana gittiğinizde **küme adı** URL 'nizin küme adı bölümüyle otomatik olarak doldurulur. Ardından bir **Kullanıcı adı** ve **parola** girin ve **İleri**' yi seçin.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png" alt-text="Bir kümeyi bağlama, HDInsight, Visual Studio" border="true":::

3. **Son**'u seçin. Küme bağlama başarılı olursa, küme **HDInsight** düğümü altında listelenir.

Bağlı bir kümeyi güncelleştirmek için kümeye sağ tıklayın ve **Düzenle**' yi seçin. Daha sonra küme bilgilerini güncelleştirebilirsiniz.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png" alt-text="Bağlı kümeyi düzenleme, HDInsight, Visual Studio" border="true":::

## <a name="explore-linked-resources"></a>Bağlantılı kaynakları araştırma

Sunucu Gezgini'nde, varsayılan depolama hesabını ve bağlı tüm depolama hesaplarını görebilirsiniz. Varsayılan depolama hesabını genişletirseniz, depolama hesabında kapsayıcıları görebilirsiniz. Varsayılan depolama hesabı ve varsayılan kapsayıcı işaretlenmiştir.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png" alt-text="Sunucu Gezgini içindeki Visual Studio bağlantılı kaynaklarına yönelik Data Lake araçları" border="true":::

Kapsayıcıya sağ tıklayın ve kapsayıcının içeriğini görüntülemek için **kapsayıcıyı görüntüle** ' yi seçin. Bir kapsayıcıyı açtıktan sonra, araç çubuğu düğmelerini kullanarak içerik listesini **yenileyebilir** , **blobu yükleyebilir**, **Seçili Blobları silebilir**, **BLOB 'u açabilir** ve seçili blob 'ları indirebilirsiniz (**farklı kaydet**).

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png" alt-text="Kapsayıcı listesi ve BLOB işlemleri, HDInsight kümesi, Visual Studio" border="true":::

## <a name="run-interactive-apache-hive-queries"></a>Etkileşimli Apache Hive sorguları çalıştırma

[Apache Hive](https://hive.apache.org), Hadoop üzerinde oluşturulmuş bir veri ambarı altyapısıdır. Hive veri özetleme, sorgular ve analiz için kullanılır. Visual Studio’dan Hive sorguları çalıştırmak üzere Visual Studio için Data Lake Araçları’nı kullanabilirsiniz. Hive hakkında daha fazla bilgi için bkz. [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](hdinsight-use-hive.md).

[Azure HDInsight 'Ta etkileşimli sorgu](../interactive-query/apache-interactive-query-get-started.md) Apache Hive 2,1 ' de [LLAP üzerinde Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP) kullanır. Etkileşimli sorgu, büyük, depolanmış veri kümelerinde karmaşık, veri ambarı stili sorgulara etkileşim getirir. Etkileşimli sorgu üzerinde Hive sorgularının çalıştırılması, geleneksel Hive toplu iş işlemlerinden çok daha hızlıdır. 

> [!NOTE]  
> Etkileşimli Hive sorgularını yalnızca bir [HDInsight Etkileşimli Sorgu](../interactive-query/apache-interactive-query-get-started.md) kümesine bağlandığınızda çalıştırabilirsiniz.

Ayrıca, bir Hive işinin içinde neler olduğunu görmek için Visual Studio için Data Lake araçları 'nı kullanabilirsiniz. Visual Studio için Data Lake Araçları bazı Hive işlerinin Yarn günlüklerini toplar ve yüzeye çıkarır.

**Sunucu Gezgini** **Azure**  >  **HDInsight** ' ı seçin ve kümenizi seçin.  Bu düğüm, Bölüm izlemek için **Sunucu Gezgini** başlangıç noktasıdır.

### <a name="view-hivesampletable"></a>hivesampletable öğesini görüntüleme

Tüm HDInsight kümelerinin varsayılan bir örnek Hive tablosu adı vardır `hivesampletable` .  

Kümenizde **Hive veritabanları**  >  **varsayılan**  >  **hivesampletable** öğesini seçin.

* Şemayı görüntülemek için `hivesampletable` :

    **Hivesampletable** öğesini genişletin. Sütunların adları ve veri türleri `hivesampletable` gösterilir.

* Verileri görüntülemek için `hivesampletable` :

    **Hivesampletable** öğesine sağ tıklayın ve **Ilk 100 satırı görüntüle**' yi seçin. 100 sonuçlarının listesi **Hive tablosu: hivesampletable** penceresinde görünür. Bu eylem, Hive ODBC sürücüsünü kullanarak aşağıdaki Hive sorgusunu çalıştırmaya eşdeğerdir:

    `SELECT * FROM hivesampletable LIMIT 100`

    Satır sayısını, satır **sayısını** değiştirerek özelleştirebilirsiniz. aşağı açılan listeden 50, 100, 200 veya 1000 satırları seçebilirsiniz.

### <a name="create-hive-tables"></a>Hive tabloları oluşturma

Bir Hive tablosu oluşturmak için GUI’yi ya da Hive sorgularını kullanabilirsiniz. Hive sorguları kullanma hakkında daha fazla bilgi için bkz. [Hive sorguları oluşturma ve çalıştırma](#create-and-run-hive-queries).

1. Kümeinizden **Hive veritabanı**  >  **Varsayılanı**' nı seçin.

2. **Varsayılan**' a sağ tıklayın ve **tablo oluştur**' u seçin.

3. Tabloyu yapılandırın.

4. Yeni Hive tablosunu oluşturan işi göndermek için **tablo oluştur** düğmesini seçin.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png" alt-text="Tablo penceresi, Hive, HDInsight kümesi, Visual Studio oluştur" border="true":::

### <a name="create-and-run-hive-queries"></a>Hive sorguları oluşturma ve çalıştırma

Hive sorguları oluşturmak ve çalıştırmak için iki seçeneğiniz vardır:

* Geçici sorgular oluşturma
* Hive uygulaması oluşturma

#### <a name="create-an-ad-hoc-query"></a>Geçici sorgu oluşturma

Geçici sorgu oluşturmak ve çalıştırmak için:

1. Sorguyu çalıştırmak istediğiniz kümeye sağ tıklayın ve **Hive sorgusu yaz**' ı seçin.  

2. Hive sorgusu girin.

    Hive düzenleyicisi IntelliSense’i destekler. Visual Studio için Data Lake Araçları, Hive betiğinizi düzenlerken uzak meta verilerin yüklenmesini destekler. Örneğin, yazarsanız `SELECT * FROM` , IntelliSense önerilen tüm tablo adlarını listeler. Bir tablo adı belirtildiğinde, IntelliSense sütun adlarını listeler. Araçlar çoğu Hive DML deyimlerini, alt sorguları ve yerleşik UDF'leri destekler.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png" alt-text="IntelliSense örnek 1, Hive geçici sorgu, HDInsight kümesi, Visual Studio" border="true":::

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png" alt-text="IntelliSense örnek 2, Hive geçici sorgu, HDInsight kümesi, Visual Studio" border="true":::

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

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png" alt-text="Etkileşimli mod, Hive geçici sorgu, HDInsight kümesi, Visual Studio" border="true":::  

    * **Batch**  

        İlk açılan listede **toplu işlem**' i seçin ve ardından **Gönder**' i seçin. Ya da **Gönder** ' in yanındaki açılan simgeyi seçin ve **Gelişmiş**' i seçin.

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png" alt-text="Batch modu, Hive geçici sorgu, HDInsight kümesi, Visual Studio" border="true":::

        Gelişmiş gönder seçeneğini belirlerseniz, **betiği gönder** iletişim kutusu görüntülenir. Betik için **Iş adı**, **bağımsız değişkenler**, **ek yapılandırma** ve **durum dizini** yapılandırın.

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png" alt-text="Betik Gönder iletişim kutusu, Hive geçici sorgu, HDInsight kümesi, Visual Studio" border="true":::

      > [!NOTE]  
      > Etkileşimli sorgu kümelerine toplu işler gönderemezsiniz.  Etkileşimli mod kullanmanız gerekir.

#### <a name="create-a-hive-application"></a>Hive uygulaması oluşturma

Hive çözümü oluşturmak ve çalıştırmak için:

1. Menü çubuğundan **Dosya**  >  **Yeni**  >  **Proje**' yi seçin.

2. **Yeni proje oluştur** penceresinde, arama kutusunu seçin ve **Hive** yazın. Ardından **Hive uygulaması** ' nı seçin ve **İleri**' yi seçin.

3. **Yeni projeyi yapılandırın** penceresinde bir **Proje adı** girin, proje **konumunu** seçin veya oluşturun ve ardından **Oluştur**' u seçin.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png" alt-text="Yeni Hive uygulaması, yeni proje pencerenizi yapılandırma, HDInsight Visual Studio" border="true":::

4. Betiği açmak için **Çözüm Gezgini**’nde **Script.hql** öğesine çift tıklayın.

### <a name="view-job-summary-and-output"></a>İş özetini ve çıktıyı görüntüleme

İş Özeti, **Batch** ve **etkileşimli** mod arasında biraz farklılık gösterir.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png" alt-text="Hive iş Özeti pencereleri, toplu iş ve etkileşimli mod, Visual Studio" border="true":::

İş durumu **tamamlandı** olarak değiştirilene kadar durumu güncelleştirmek için **Yenile** simgesini kullanın.  

* **Batch** modundan iş ayrıntıları Için, **iş sorgusunu**, **iş çıkışını** veya **Iş günlüğünü** görmek ya da **Yarn günlüklerini görüntülemek** için alttaki bağlantıları seçin.

* **Etkileşimli** moddan iş ayrıntıları için **Çıkış** ve **HiveServer2 çıkış** bölmeleri bölümüne bakın.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png" alt-text="Hive etkileşimli iş çıktısı, HDInsight kümesi, Visual Studio" border="true":::

### <a name="view-job-graph"></a>İş grafiğini görüntüle

Şu anda iş grafikleri yalnızca, yürütme altyapısı olarak tez kullanan Hive işleri için gösterilir.  Tez 'yi etkinleştirme hakkında bilgi için bkz. [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](hdinsight-use-hive.md).  Ayrıca bkz. [harita azaltma yerine Apache tez kullanın](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Köşe içindeki tüm işleçleri görüntülemek için, iş grafiğinin köşelerine çift tıklayın. Ayrıca, işleç hakkında daha fazla ayrıntı görmek için belirli bir işleci işaret edebilirsiniz.

Tez yürütme altyapısı olarak belirtilmiş olsa bile, hiçbir tez uygulaması başlatılmasa bile iş grafiği görünmeyebilir.  İş DML deyimlerini içermediği için bu durum oluşabilir. Ya da DML deyimleri tez uygulaması başlatmadan dönebildiğinden. Örneğin, `SELECT * FROM table1` tez uygulamasını başlatılmaz.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png" alt-text="Apache Hive iş grafiği, Visual Studio" border="true":::

### <a name="view-task-execution-detail"></a>Görev yürütme ayrıntılarını görüntüleme

İş Grafında, Hive işleri için yapılandırılmış ve görselleştirilmiş bilgileri almak üzere **görev yürütme ayrıntısı** ' nı seçebilirsiniz. Daha fazla iş ayrıntısı da alabilirsiniz. Performans sorunları oluşursa, sorun hakkında daha fazla bilgi almak için bu görünümü kullanabilirsiniz. Örneğin, her bir görevin nasıl çalıştığı ve her görevle ilgili ayrıntılı bilgilerin yanı sıra (veri okuma/yazma, zamanlama/başlangıç/bitiş zamanı ve daha fazlası) hakkında bilgi alabilirsiniz. İş yapılandırmalarını veya sistem mimarisini görselleştirilmiş bilgilere göre ayarlamak için bilgileri kullanın.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png" alt-text="Görev yürütme görünümü penceresi, Data Lake Visual Studio Araçları" border="true":::

### <a name="view-hive-jobs"></a>Hive İşlerini Görüntüleme

Hive işleri için iş sorguları, iş çıktısı, iş günlükleri ve Yarn günlüklerini görüntüleyebilirsiniz.

Araçların en son sürümünde, Yarn günlüklerini toplayıp ortaya koyarak Hive işleriniz içinde neler olduğunu görebilirsiniz. Yarn günlüğü, performans sorunlarını araştırmanıza yardımcı olabilir. HDInsight 'ın Yarn günlüklerini nasıl topladığı hakkında daha fazla bilgi için bkz. [erişim Apache HADOOP YARN uygulama günlükleri](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Hive işlerini görüntülemek için:

1. Bir HDInsight kümesine sağ tıklayın ve **Işleri görüntüle**' yi seçin.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png" alt-text="Işleri görüntüleme, Apache Hive, HDInsight kümesi, Visual Studio" border="true":::

    Küme üzerinde çalıştırılan Hive işlerinin listesi görüntülenir.  

2. Bir iş seçin. **Hive Iş Özeti** penceresinde, aşağıdaki bağlantılardan birini seçin:
    - **İş Sorgusu**
    - **İş çıkışı**
    - **İş Günlüğü**  
    - **Yarn günlüğü**

## <a name="run-apache-pig-scripts"></a>Apache Pig betiklerini çalıştır

1. Menü çubuğundan **Dosya**  >  **Yeni**  >  **Proje**' yi seçin.

2. **Başlangıç** penceresinde arama kutusunu seçin ve **Pig** girin. Sonra **Pig uygulamasını** seçin ve **İleri**' yi seçin.

3. **Yeni projenizi yapılandırın** penceresinde bir **Proje adı** girin ve proje için bir **konum** seçin veya oluşturun. Ardından **Oluştur**’u seçin.

4. IDE **Çözüm Gezgini** bölmesinde, Script **. Pig** öğesine çift tıklayarak betiği açın.

## <a name="feedback-and-known-issues"></a>Geri bildirim ve bilinen sorunlar

* Null değerlerle başlatılan sonuçların gösterilmediği bir sorun düzeltilmiştir. Bu sorun sizi engelliyorsa destek ekibine başvurun.

* Visual Studio 'Nun oluşturduğu HQL betiği kullanıcının yerel bölge ayarına bağlı olarak kodlanır. Betiği bir kümeye ikili dosya olarak yüklerseniz betik doğru şekilde yürütülmez.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede Visual Studio’dan HDInsight kümelerine bağlanmak üzere Visual Studio için Data Lake Araçları paketini kullanmayı öğrendiniz. Ayrıca bir Hive sorgusu çalıştırmayı öğrendiniz. 

* [Visual Studio için Data Lake araçlarını kullanarak Apache Hive sorgularını çalıştırma](apache-hadoop-use-hive-visual-studio.md)
* [Azure HDInsight 'ta Apache Hive ve HiveQL nedir?](hdinsight-use-hive.md)
* [Apache Hadoop kümesi oluşturma - Şablon](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight 'ta Apache Hadoop işleri gönderme](submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight üzerinde Apache Hive ve Apache Hadoop kullanarak Twitter verilerini çözümleme](../hdinsight-analyze-twitter-data-linux.md)
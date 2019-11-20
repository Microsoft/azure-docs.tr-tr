---
title: 'Öğretici: Power BI ile Azure HDInsight Apache Spark verileri çözümleme'
description: Öğretici-Microsoft Power BI kullanarak HDInsight kümelerini Apache Spark depolanan verileri görselleştirme
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: c2d6a5acba304d7421b000cab2ee5cee5b85e5ce
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241363"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Öğretici: HDInsight 'ta Power BI kullanarak Apache Spark verileri çözümleme

Bu öğreticide, [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)'ta bir Apache Spark kümesindeki verileri görselleştirmek için [Microsoft Power BI](https://powerbi.microsoft.com/) kullanmayı öğreneceksiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Power BI kullanarak Spark verilerini görselleştirme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Makale [öğreticisini doldurun: Azure HDInsight 'ta Apache Spark kümesinde veri yükleme ve sorgu çalıştırma](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* İsteğe bağlı: [deneme aboneliği Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Verileri doğrulama

[Önceki öğreticide](apache-spark-load-data-run-query.md) oluşturduğunuz [Jupyter Notebook](https://jupyter.org/) , bir `hvac` tablosu oluşturmak için kod içerir. Bu tablo, `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv` ' da tüm HDInsight Spark kümelerinde kullanılabilen CSV dosyasını temel alır. Verileri doğrulamak için aşağıdaki yordamı kullanın.

1. Jupyter not defterinden aşağıdaki kodu yapıştırın ve sonra **SHIFT + ENTER** tuşuna basın. Kod, tabloların varlığını doğrular.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Çıkış aşağıdakine benzer olacaktır:

    ![Spark’ta tabloları gösterme](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Bu öğreticiye başlamadan önce not defterini kapattıysanız, `hvactemptable` silinir ve bu nedenle çıktıya eklenmez.  Yalnızca meta veri deposunda depolanmış Hive tablolarına (**isTemporary** sütunu altında **False** ile gösterilir) BI araçlarından erişilebilir. Bu öğreticide, oluşturduğunuz **hvac** tablosuna bağlanacaksınız.

2. Aşağıdaki kodu boş bir hücreye yapıştırın ve sonra **SHIFT + ENTER** tuşlarına basın. Kod, tablodaki verileri doğrular.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Çıkış aşağıdakine benzer olacaktır:

    ![Spark içinde hvac tablosunun satırlarını gösterme](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. Not defterindeki **Dosya** menüsünden **Kapat ve Durdur**’u seçin. Kaynakları serbest bırakmak için not defterini kapatın.

## <a name="visualize-the-data"></a>Verileri görselleştirme

Bu bölümde Power BI kullanarak, Spark kümesi verilerinden görselleştirme, rapor ve panolar oluşturacaksınız.

### <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop'ta rapor oluşturma

Spark ile çalışmanın ilk adımları, Power BI Desktop’ta kümeye bağlanmak, kümeden veri yüklemek ve bu verileri temel alarak basit bir görselleştirme oluşturmaktır.

> [!NOTE]  
> Bu makalede gösterilen bağlayıcı şu anda önizleme aşamasındadır. Geri bildirimlerinizi [Power BI Topluluğu](https://community.powerbi.com/) sitesi ya da [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) sayfasından gönderin.

1. Power BI Desktop’ı açın. Açılırsa başlangıç giriş ekranını kapatın.

2. **Giriş** sekmesinde **veri al** > **daha fazla..** . sayfasına gidin.

    ![HDInsight 'tan Power BI Desktop veri alın Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Apache Spark bı 'dan Power BI veri al")

3. Arama kutusuna `Spark` girin, **Azure HDInsight Spark**' i seçin ve ardından **Bağlan**' ı seçin.

    ![Apache Spark bı 'dan Power BI veri al](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Apache Spark bı 'dan Power BI veri al")

4. **Sunucu** metin kutusuna küme URL 'nizi (`mysparkcluster.azurehdinsight.net` biçiminde) girin.

5. **Veri bağlantısı modu**altında **DirectQuery**' yi seçin. Sonra **Tamam**’ı seçin.

    Spark ile herhangi bir veri bağlantısı modunu kullanabilirsiniz. DirectQuery kullanırsanız, değişiklikler tüm veri kümesi yenilenmeden raporlara yansıtılır. Verileri içeri aktarırsanız, değişiklikleri görmek için veri kümesini yenilemeniz gerekir. DirectQuery’nin nasıl ve ne zaman kullanılacağı hakkında daha fazla bilgi için bkz. [Power BI’da DirectQuery kullanma](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. HDInsight oturum açma hesabı bilgilerini girin ve **Bağlan**' ı seçin. Varsayılan hesap adı *admin*’dir.

7. `hvac` tablosunu seçin, verilerin önizlemesini görmek için bekleyin ve sonra **Yükle**' yi seçin.

    ![Spark kümesi Kullanıcı adı ve parolası](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark kümesi Kullanıcı adı ve parolası")

    Power BI Desktop, Spark kümesine bağlanmak ve `hvac` tablosundan verileri yüklemek için gereken bilgilere sahiptir. Tablo ve sütunları, **Alanlar** bölmesinde gösterilir.

8. Her bina için hedef sıcaklık ile gerçek sıcaklık arasındaki farkı görselleştirin:

    1. **GÖRSELLEŞTİRMELER** bölmesinde **Alan Grafiği**’ni seçin.

    2. **BuildingID** alanını **Eksen**’e, **ActualTemp** ve **TargetTemp** alanlarını ise **Değer**’e sürükleyin.

        ![değer sütunları ekle](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "değer sütunları ekle")

        Diyagram şuna benzer:

        ![alan grafiği toplamı](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "alan grafiği toplamı")

        Varsayılan olarak görselleştirme, **ActualTemp** ve **TargetTemp** değerlerinin toplamını gösterir. Görsel öğeler bölmesindeki **actualtemp** ve **tragettemp** ' in yanındaki aşağı oku seçerek **Sum** ' ın seçili olduğunu görebilirsiniz.

    3. Görsel öğeler bölmesindeki **Actualtemp** ve **tragettemp** ' in yanındaki aşağı okları seçin, her bina için Ortalama gerçek ve hedef sıcaklıklar almak üzere **Ortalama** ' ı seçin.

        ![değerlerin ortalaması](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "değerlerin ortalaması")

        Veri görselleştirmeniz, ekran görüntüsünde gösterilene benzer olmalıdır. İlgili verilere ilişkin araç ipuçları almak üzere imlecinizi görselleştirmenin üzerine getirin.

        ![alan grafiği](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "alan grafiği")

9. **Dosya** > **Kaydet**' e gidin, dosya için `BuildingTemperature` adını girin ve **Kaydet**' i seçin.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Raporu Power BI Hizmetinde yayımlama (isteğe bağlı)

Power BI hizmeti, raporları ve panoları kuruluşunuzda paylaşmanıza olanak tanır. Bu bölümde, ilk olarak veri kümesini ve raporu yayımlayacaksınız. Ardından, raporu bir panoya sabitleyeceksiniz. Panolar genellikle bir rapordaki verilerin bir alt kümesine odaklanmak için kullanılır. Raporunuzda yalnızca bir görselleştirmeniz var, ancak bu adımlar arasında gezinmek hala faydalı.

1. Power BI Desktop’ı açın.

1. **Giriş** sekmesinden **Yayımla**’ya tıklayın.

    ![Power BI Desktop'tan yayımlama](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Power BI Desktop'tan yayımlama")

1. Veri kümenizin ve raporunuzun yayımlanacağı çalışma alanını seçin ve **Seç**’e tıklayın. Aşağıdaki görüntüde varsayılan **Çalışma Alanım** seçilidir.

    ![Veri kümesini ve raporu yayımlamak için çalışma alanı seçin](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Veri kümesini ve raporu yayımlamak için çalışma alanı seçin") 

1. Yayımlama başarılı olduktan sonra **'BuildingTemperature.pbix' dosyasını Power BI’da aç** öğesine tıklayın.

    ![Yayımlama başarılı, kimlik bilgilerini girmek için tıklayın](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Yayımlama başarılı, kimlik bilgilerini girmek için tıklayın") 

1. Power BI hizmetinde **Kimlik bilgilerini girin**’e tıklayın.

    ![Power BI hizmeti kimlik bilgilerini girin](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Power BI hizmeti kimlik bilgilerini girin")

1. **Kimlik bilgilerini düzenle**’ye tıklayın.

    ![Power BI hizmeti kimlik bilgilerini düzenleme](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Power BI hizmeti kimlik bilgilerini düzenleme")

1. HDInsight oturum açma hesabı bilgilerini girin, ardından **Oturum Aç**’a tıklayın. Varsayılan hesap adı *admin*’dir.

    ![Spark kümesinde oturum açma](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Spark kümesinde oturum açma")

1. Sol bölmede, **Çalışma Alanları** > **Çalışma Alanım** > **RAPORLAR**’a gidin, ardından **BinaSıcaklığı**’na tıklayın.

    ![Sol bölmedeki raporlar altında listelenen rapor](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Sol bölmedeki raporlar altında listelenen rapor")

    **BinaSıcaklığı** değerinin sol bölmedeki **VERİ KÜMELERİ** altında da listelendiğini göreceksiniz.

    Power BI Desktop’ta oluşturduğunuz görsel artık Power BI hizmetinde kullanılabilir. 

1. İmlecinizi görselleştirmenin üzerine getirin ve ardından sağ üst köşedeki iğne simgesine tıklayın.

    ![Power BI hizmeti raporla](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Power BI hizmeti raporla")

1. "Yeni pano" öğesini seçin, `Building temperature` adını girin, ardından **Sabitle**’ye tıklayın.

    ![Yeni panoya sabitle](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Yeni panoya sabitle")

1. Raporda **Panoya git**’e tıklayın.

Görseliniz panoya sabitlenir. Rapora başka görseller ekleyebilir ve bu görselleri aynı panoya sabitleyebilirsiniz. Raporlar ve panolar hakkında daha fazla bilgi için, [Power BI içindeki](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)Power BI ve [panolarda bulunan raporlara](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) bakın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiyi tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmasa bile bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar.

Bir kümeyi silmek için bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)'ta bir Apache Spark kümesindeki verileri görselleştirmek için [Microsoft Power BI](https://powerbi.microsoft.com/) nasıl kullanacağınızı öğrendiniz. Spark’a kaydettiğiniz verilerin Power BI gibi bir BI analiz aracına nasıl çekilebileceğini görmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Apache Spark akış işi çalıştırma](apache-spark-eventhub-streaming.md)
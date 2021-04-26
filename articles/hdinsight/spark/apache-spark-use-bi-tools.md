---
title: 'Öğretici: Power BI ile Azure HDInsight Apache Spark verileri çözümleme'
description: Öğretici-Microsoft Power BI kullanarak HDInsight kümelerini Apache Spark depolanan verileri görselleştirme
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 3e0632b2ad1ac237d8899e9d3bdc7f1d3350fa76
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057941"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Öğretici: HDInsight 'ta Power BI kullanarak Apache Spark verileri çözümleme

Bu öğreticide, Azure HDInsight 'ta bir Apache Spark kümesindeki verileri görselleştirmek için Microsoft Power BI kullanmayı öğreneceksiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Power BI kullanarak Spark verilerini görselleştirme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* [Öğretici: Azure HDInsight içindeki bir Apache Spark kümesinde veri yükleme ve sorgu çalıştırma](./apache-spark-load-data-run-query.md) makalesini tamamlayın.

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* İsteğe bağlı: [deneme aboneliği Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Verileri doğrulama

[Önceki öğreticide](apache-spark-load-data-run-query.md) oluşturduğunuz [Jupyter Notebook](https://jupyter.org/) tablo oluşturmak için kod içerir `hvac` . Bu tablo, konumundaki tüm HDInsight Spark kümelerinde kullanılabilen CSV dosyasını temel alır `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv` . Verileri doğrulamak için aşağıdaki yordamı kullanın.

1. Jupyter Notebook, aşağıdaki kodu yapıştırın ve ardından **SHIFT + enter** tuşlarına basın. Kod, tabloların varlığını doğrular.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Çıktı şuna benzer:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-show-tables.png" alt-text="Spark’ta tabloları gösterme" border="true":::

    Bu öğreticiye başlamadan önce not defterini kapattıysanız, `hvactemptable` silinir ve bu nedenle çıktıya eklenmez.  Yalnızca meta veri deposunda depolanmış Hive tablolarına (**isTemporary** sütunu altında **False** ile gösterilir) BI araçlarından erişilebilir. Bu öğreticide, oluşturduğunuz **hvac** tablosuna bağlanacaksınız.

2. Aşağıdaki kodu boş bir hücreye yapıştırın ve sonra **SHIFT + ENTER** tuşlarına basın. Kod, tablodaki verileri doğrular.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Çıktı şuna benzer:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-select-limit.png" alt-text="Spark içinde hvac tablosunun satırlarını gösterme" border="true":::

3. Not defterindeki **Dosya** menüsünden **Kapat ve Durdur**’u seçin. Kaynakları serbest bırakmak için not defterini kapatın.

## <a name="visualize-the-data"></a>Verileri görselleştirme

Bu bölümde Power BI kullanarak, Spark kümesi verilerinden görselleştirme, rapor ve panolar oluşturacaksınız.

### <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop’ta rapor oluşturma

Spark ile çalışmanın ilk adımları, Power BI Desktop’ta kümeye bağlanmak, kümeden veri yüklemek ve bu verileri temel alarak basit bir görselleştirme oluşturmaktır.

1. Power BI Desktop'ı açın. Açılırsa başlangıç giriş ekranını kapatın.

2. **Giriş** sekmesinde, **verileri**  >  **daha fazla al..**. ' a gidin.

    :::image type="content" source="./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png " alt-text="HDInsight 'tan Power BI Desktop veri al Apache Spark" border="true":::er = "true":::

3. `Spark`Arama kutusuna girin, **Azure HDInsight Spark** öğesini seçin ve ardından **Bağlan**' ı seçin.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png " alt-text="Apache Spark bı 'dan Power BI veri al" border="true":::= "true":::

4. `mysparkcluster.azurehdinsight.net` **Sunucu** metın kutusuna küme URL 'nizi (biçimde) girin.

5. **Veri bağlantısı modu** altında **DirectQuery**' yi seçin. Ardından **Tamam**’ı seçin.

    Spark ile herhangi bir veri bağlantısı modunu kullanabilirsiniz. DirectQuery kullanırsanız, değişiklikler tüm veri kümesi yenilenmeden raporlara yansıtılır. Verileri içeri aktarırsanız, değişiklikleri görmek için veri kümesini yenilemeniz gerekir. DirectQuery’nin nasıl ve ne zaman kullanılacağı hakkında daha fazla bilgi için bkz. [Power BI’da DirectQuery kullanma](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. HDInsight oturum açma hesabı bilgilerini girin ve **Bağlan**' ı seçin. Varsayılan hesap adı *admin*’dir.

7. Tabloyu seçin `hvac` , verilerin önizlemesini görmek için bekleyin ve sonra **Yükle**' yi seçin.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png " alt-text="Spark kümesi Kullanıcı adı ve parolası" border="true":::d "Border =" true ":::

    Power BI Desktop, Spark kümesine bağlanmak ve `hvac` tablosundan verileri yüklemek için gereken bilgilere sahiptir. Tablo ve sütunları, **Alanlar** bölmesinde gösterilir.

8. Her bina için hedef sıcaklık ile gerçek sıcaklık arasındaki farkı görselleştirin:

    1. **GÖRSELLEŞTİRMELER** bölmesinde **Alan Grafiği**’ni seçin.

    2. **BuildingID** alanını **Eksen**’e, **ActualTemp** ve **TargetTemp** alanlarını ise **Değer**’e sürükleyin.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png " alt-text="değer sütunları ekle" border="true":::t = "değer sütunları ekle" Border = "true":::

        Diyagram şuna benzer:

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png " alt-text="alan grafı toplamı" border="true":::lt-Text = "alan grafiği toplamı" Border = "true":::

        Varsayılan olarak görselleştirme, **ActualTemp** ve **TargetTemp** değerlerinin toplamını gösterir. Görsel öğeler bölmesindeki **actualtemp** ve **tragettemp** ' in yanındaki aşağı oku seçerek **Sum** ' ın seçili olduğunu görebilirsiniz.

    3. Görsel öğeler bölmesindeki **Actualtemp** ve **tragettemp** ' in yanındaki aşağı okları seçin, her bina için Ortalama gerçek ve hedef sıcaklıklar almak üzere **Ortalama** ' ı seçin.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png " alt-text="değer ortalaması" border="true":::t = "ortalama değer" Border = "true":::

        Veri görselleştirmeniz, ekran görüntüsünde gösterilene benzer olmalıdır. İlgili verilere ilişkin araç ipuçları almak üzere imlecinizi görselleştirmenin üzerine getirin.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png " alt-text="Area Graph" border="true":::. png "alt-metin =" alan grafiği "Border =" true ":::

9. **Dosya**  >  **Kaydet**' e gidin, dosyanın adını girin ve `BuildingTemperature` ardından **Kaydet**' i seçin.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Raporu Power BI Hizmetinde yayımlama (isteğe bağlı)

Power BI hizmeti, raporları ve panoları kuruluşunuzda paylaşmanıza olanak tanır. Bu bölümde, ilk olarak veri kümesini ve raporu yayımlayacaksınız. Ardından, raporu bir panoya sabitleyeceksiniz. Panolar genellikle bir rapordaki verilerin bir alt kümesine odaklanmak için kullanılır. Raporunuzda yalnızca bir görselleştirmeniz var, ancak bu adımlar arasında gezinmek hala faydalı.

1. Power BI Desktop'ı açın.

1. **Giriş** sekmesinde **Yayımla**' yı seçin.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png " alt-text="Power BI Desktop yayımlama" border="true"::: Masaüstü "Border =" true ":::

1. Veri kümenizi ve raporunuzu yayımlamak için bir çalışma alanı seçin ve ardından **Seç**' i seçin. Aşağıdaki görüntüde varsayılan **Çalışma Alanım** seçilidir.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png " alt-text="Veri kümesini yayımlamak için çalışma alanı seçin ve" border="true":::UE ":::

1. Yayımlama başarılı olduktan sonra, **Power BI içinde ' Buildingsıcaklık. pbix ' Aç '** ı seçin.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png " alt-text="Yayımlama başarılı, kimlik bilgilerini girmek için tıklayın" border="true":::er = "true":::

1. Power BI hizmeti, **kimlik bilgilerini girin**' i seçin.

    Power BI hizmeti "Border =" true " :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png " alt-text="olarak kimlik bilgilerini girin" border="true"::::::

1. **Kimlik bilgilerini düzenle**' yi seçin.

    Power BI hizmeti e "Border =" true " :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png " alt-text="içinde kimlik bilgilerini düzenle" border="true"::::::

1. HDInsight oturum açma hesabı bilgilerini girin ve **oturum aç**' ı seçin. Varsayılan hesap adı *admin*’dir.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png " alt-text="Spark kümesinde oturum açma" border="true"::: Spark kümesi "Border =" true ":::

1. Sol **bölmede çalışma**  >  **alanı**  >  **Raporlarım**' e gidin ve ardından **buildingsıcaklık**' ı seçin.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png " alt-text="Rapor, sol bölme" border="true":::sırası = "true" olarak listelenmektedir:::

    **BinaSıcaklığı** değerinin sol bölmedeki **VERİ KÜMELERİ** altında da listelendiğini göreceksiniz.

    Power BI Desktop’ta oluşturduğunuz görsel artık Power BI hizmetinde kullanılabilir.

1. İmlecinizi görselleştirme üzerine getirin ve sağ üst köşedeki sabitleme simgesini seçin.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png " alt-text="Power BI hizmeti hizmetindeki rapor" border="true":::"Border =" true ":::

1. "Yeni Pano" yı seçin, adı girin `Building temperature` ve **sabitle**' yi seçin.

    Yeni panoya yeni panoya :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png " alt-text="sabitle" border="true"::: "Border =" true ":::

1. Raporda **panoya git**' i seçin.

Görseliniz panoya sabitlenir. Rapora başka görseller ekleyebilir ve bu görselleri aynı panoya sabitleyebilirsiniz. Raporlar ve panolar hakkında daha fazla bilgi için, [Power BI içindeki](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)Power BI ve [panolarda bulunan raporlara](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) bakın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiyi tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmasa bile bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar.

Bir kümeyi silmek için bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure HDInsight 'ta bir Apache Spark kümesindeki verileri görselleştirmek için Microsoft Power BI nasıl kullanacağınızı öğrendiniz. Bir makine öğrenimi uygulaması oluşturabileceğiniz makaleyi görmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Machine Learning uygulaması oluşturma](./apache-spark-ipython-notebook-machine-learning.md)
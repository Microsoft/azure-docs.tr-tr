---
title: 'Öğretici: verileri yükleme & sorguları Apache Spark ile çalıştırma-Azure HDInsight'
description: Öğretici-Azure HDInsight 'ta verileri yükleme ve Spark kümelerinde etkileşimli sorgular çalıştırma hakkında bilgi edinin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: 8c5fe1970857a04c7b237a101ac228dea024815b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494496"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Öğretici: Azure HDInsight içindeki bir Apache Spark kümesinde veri yükleme ve sorgular çalıştırma

Bu öğreticide, bir CSV dosyasından bir veri çerçevesi oluşturmayı ve etkileşimli Spark SQL sorgularını Azure HDInsight 'ta bir [Apache Spark](https://spark.apache.org/) kümesinde çalıştırmayı öğreneceksiniz. Spark’ta dataframe, adlandırılmış sütunlar halinde düzenlenmiş, dağıtılmış bir veri koleksiyonudur. Dataframe kavramsal olarak, ilişkisel bir veritabanındaki tabloya veya R/Python’daki veri çerçevesine eşdeğerdir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Bir csv dosyasından dataframe oluşturma
> * Dataframe üzerinde sorgular çalıştırma

## <a name="prerequisites"></a>Önkoşullar

HDInsight üzerinde bir Apache Spark kümesi. Bkz. [Apache Spark kümesi oluşturma](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter not defteri oluşturma

Jupyter Notebook, çeşitli programlama dillerini destekleyen etkileşimli bir not defteri ortamıdır. Not defteri, verilerle etkileşim kurmanıza, kodu markdown metniyle birleştirmenize ve basit görselleştirmeler gerçekleştirmenize olanak sağlar.

1. `SPARKCLUSTER` Spark kümenizin adıyla değiştirerek URL 'YI düzenleyin `https://SPARKCLUSTER.azurehdinsight.net/jupyter`. Ardından, düzenlenen URL 'YI bir Web tarayıcısına girin. İstendiğinde, küme için küme oturum açma kimlik bilgilerini girin.

2. Jupyter Web sayfasından **yeni** > **pyspark** ' ı seçerek bir not defteri oluşturun.

   ![Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook oluşturma](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook oluşturma")

   Yeni bir not defteri oluşturulur ve adsız (`Untitled.ipynb`) adıyla açılır.

    > [!NOTE]  
    > PySpark çekirdeği kullanılarak not defteri oluşturmak için, ilk kod hücresini çalıştırdığınızda sizin için otomatik olarak `spark` oturumu oluşturulur. Belirtik şekilde bir oturum oluşturmanız gerekmez.

## <a name="create-a-dataframe-from-a-csv-file"></a>Bir csv dosyasından dataframe oluşturma

Uygulamalar, Azure depolama veya Azure Data Lake Storage gibi uzak depolamada bulunan dosyalardan veya klasörlerden doğrudan veri çerçeveleri oluşturabilir. Hive tablosundan; ya da Spark tarafından desteklenen Cosmos DB, Azure SQL DB, DW gibi diğer veri kaynaklarından. Aşağıdaki ekran görüntüsünde, bu öğreticide kullanılan HVAC.csv dosyasının bir anlık görüntü gösterilmektedir. Csv dosyası, tüm HDInsight Spark kümeleriyle birlikte gelir. Veriler, bazı binaların sıcaklık varyasyonlarını yakalar.

![Etkileşimli Spark SQL sorgusu için veri anlık görüntüsü](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Etkileşimli Spark SQL sorgusu için veri anlık görüntüsü")

1. Aşağıdaki kodu Jupyter Not defterinin boş bir hücresine yapıştırın ve ardından kodu çalıştırmak için **SHIFT + enter** tuşlarına basın. Kod, bu senaryo için gerekli olan türleri içeri aktarır:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Jupyter’de etkileşimli bir sorgu çalıştırılırken web tarayıcısı penceresinde veya sekme açıklamalı alt yazısında not defteri başlığıyla birlikte **(Meşgul)** durumu gösterilir. Ayrıca sağ üst köşedeki **PySpark** metninin yanında içi dolu bir daire görürsünüz. İş tamamlandıktan sonra bu simge boş bir daireye dönüşür.

    ![Etkileşimli Spark SQL sorgusunun durumu](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Etkileşimli Spark SQL sorgusunun durumu")

2. Aşağıdaki kodu çalıştırarak bir dataframe ve geçici bir tablo (**hvac**) oluşturun.

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Dataframe üzerinde sorgular çalıştırma

Tablo oluşturulduktan sonra veriler üzerinde etkileşimli bir sorgu çalıştırabilirsiniz.

1. Not defterinin boş bir hücresinde aşağıdaki kodu çalıştırın:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Aşağıdaki tablo çıktısı görüntülenir.

     ![Etkileşimli Spark Sorgu sonucunun tablo çıktısı](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Etkileşimli Spark Sorgu sonucunun tablo çıktısı")

2. Sonuçları diğer görselleştirmelerde de görebilirsiniz. Aynı çıktı için bir alan grafiği görmek için **Alan**’ı seçin ve sonra gösterildiği gibi diğer değerleri ayarlayın.

    ![Etkileşimli Spark Sorgu sonucunun alan grafiği](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Etkileşimli Spark Sorgu sonucunun alan grafiği")

3. Not defteri menü çubuğundan **dosya** > **Kaydet ve denetim noktası**' na gidin.

4. [Sonraki öğreticiyi](apache-spark-use-bi-tools.md) şimdi başlatıyorsanız, not defterini açık bırakın. Aksi takdirde, küme kaynaklarını serbest bırakmak için Not defterini kapatın: Not defteri menü çubuğundan **dosya** >  **Kapat ve Durdur**' a gidin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

HDInsight ile, veri ve jupi Not defterleriniz Azure Storage 'da veya Azure Data Lake Storage depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmadığı halde bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar. Sonraki öğretici üzerinde hemen çalışmayı planlıyorsanız, kümeyi tutmak isteyebilirsiniz.

Azure portalında kümeyi açıp **Sil**’i seçin.

![HDInsight kümesini Sil](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight kümesini Sil")

Kaynak grubu adını seçerek de kaynak grubu sayfasını açabilir ve sonra **Kaynak grubunu sil**’i seçebilirsiniz. Kaynak grubunu silerek hem HDInsight Spark kümesini hem de varsayılan depolama hesabını silersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir CSV dosyasından bir veri çerçevesi oluşturmayı ve etkileşimli Spark SQL sorgularını Azure HDInsight 'ta bir Apache Spark kümesine karşı çalıştırmayı öğrendiniz. Apache Spark ' de kaydettiğiniz verilerin Power BI gibi bir bı Analytics aracına nasıl çekkullanılabileceğini görmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [BI araçlarını kullanarak verileri analiz etme](apache-spark-use-bi-tools.md)

---
title: 'Öğretici: verileri yükleme & sorguları Apache Spark ile çalıştırma-Azure HDInsight'
description: Öğretici-Azure HDInsight 'ta verileri yükleme ve Spark kümelerinde etkileşimli sorgular çalıştırma hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: eee93d338a106414235b04380e5a67c772951188
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062752"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Öğretici: Azure HDInsight içindeki bir Apache Spark kümesinde veri yükleme ve sorgular çalıştırma

Bu öğreticide, bir CSV dosyasından bir veri çerçevesi oluşturmayı ve etkileşimli Spark SQL sorgularını Azure HDInsight 'ta bir [Apache Spark](https://spark.apache.org/) kümesinde çalıştırmayı öğreneceksiniz. Spark’ta dataframe, adlandırılmış sütunlar halinde düzenlenmiş, dağıtılmış bir veri koleksiyonudur. Dataframe kavramsal olarak, ilişkisel bir veritabanındaki tabloya veya R/Python’daki veri çerçevesine eşdeğerdir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Bir csv dosyasından dataframe oluşturma
> * Dataframe üzerinde sorgular çalıştırma

## <a name="prerequisites"></a>Önkoşullar

HDInsight üzerinde bir Apache Spark kümesi. Bkz. [Apache Spark kümesi oluşturma](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook oluşturma

Jupyter Notebook, çeşitli programlama dillerini destekleyen etkileşimli bir not defteri ortamıdır. Not defteri, verilerle etkileşim kurmanıza, kodu markdown metniyle birleştirmenize ve basit görselleştirmeler gerçekleştirmenize olanak sağlar.

1. `https://SPARKCLUSTER.azurehdinsight.net/jupyter`Spark kümenizin adıyla değiştirerek URL 'yi düzenleyin `SPARKCLUSTER` . Ardından, düzenlenen URL 'YI bir Web tarayıcısına girin. İstendiğinde, küme için küme oturum açma kimlik bilgilerini girin.

2. Jupyter Web sayfasından **Yeni**  >  **pyspark** ' ı seçerek bir not defteri oluşturun.

   :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Etkileşimli Spark SQL sorgusu çalıştırmak için Jupyter Notebook oluşturma" border="true":::

   Yeni bir not defteri oluşturulur ve başlıksız () adıyla açılır `Untitled.ipynb` .

    > [!NOTE]  
    > PySpark çekirdeği kullanılarak not defteri oluşturmak için, ilk kod hücresini çalıştırdığınızda sizin için otomatik olarak `spark` oturumu oluşturulur. Belirtik şekilde bir oturum oluşturmanız gerekmez.

## <a name="create-a-dataframe-from-a-csv-file"></a>Bir csv dosyasından dataframe oluşturma

Uygulamalar, Azure depolama veya Azure Data Lake Storage gibi uzak depolamada bulunan dosyalardan veya klasörlerden doğrudan veri çerçeveleri oluşturabilir. Hive tablosundan; ya da Spark tarafından desteklenen Cosmos DB, Azure SQL DB, DW gibi diğer veri kaynaklarından. Aşağıdaki ekran görüntüsünde, bu öğreticide kullanılan HVAC.csv dosyasının bir anlık görüntü gösterilmektedir. Csv dosyası, tüm HDInsight Spark kümeleriyle birlikte gelir. Veriler, bazı binaların sıcaklık varyasyonlarını yakalar.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png " alt-text="Etkileşimli Spark SQL sorgusu için veri anlık görüntüsü" border="true":::

1. Aşağıdaki kodu Jupyter Notebook boş bir hücresine yapıştırın ve sonra kodu çalıştırmak için **SHIFT + enter** tuşlarına basın. Kod, bu senaryo için gerekli olan türleri içeri aktarır:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Jupyter’de etkileşimli bir sorgu çalıştırılırken web tarayıcısı penceresinde veya sekme açıklamalı alt yazısında not defteri başlığıyla birlikte **(Meşgul)** durumu gösterilir. Ayrıca sağ üst köşedeki **PySpark** metninin yanında içi dolu bir daire görürsünüz. İş tamamlandıktan sonra bu simge boş bir daireye dönüşür.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png " alt-text="Etkileşimli Spark SQL sorgusunun durumu" border="true":::

1. Döndürülen oturum kimliği ' ni aklınızda yapın. Yukarıdaki resimden oturum kimliği 0 ' dır. İsterseniz, burada CLUSTERNAME adlı konuma giderek oturum ayrıntılarını alabilirsiniz; `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` burada clustername, Spark Kümenizin adı, kimlik ise oturum kimliği numarasıdır.

1. Aşağıdaki kodu çalıştırarak bir dataframe ve geçici bir tablo (**hvac**) oluşturun.

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

     :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png " alt-text="Etkileşimli Spark Sorgu sonucunun tablo çıktısı" border="true":::

2. Sonuçları diğer görselleştirmelerde de görebilirsiniz. Aynı çıktı için bir alan grafiği görmek için **Alan**’ı seçin ve sonra gösterildiği gibi diğer değerleri ayarlayın.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png " alt-text="Etkileşimli Spark Sorgu sonucunun alan grafiği" border="true":::

3. Not defteri menü çubuğundan **Dosya**  >  **kaydetme ve denetim noktası**' na gidin.

4. [Sonraki öğreticiyi](apache-spark-use-bi-tools.md) şimdi başlatıyorsanız, not defterini açık bırakın. Aksi takdirde, küme kaynaklarını serbest bırakmak için Not defterini kapatın: Not defteri menü çubuğundan **Dosya**  >   **Kapat ve Durdur**' a gidin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

HDInsight ile, veri ve jupi Not defterleriniz Azure Storage 'da veya Azure Data Lake Storage depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmadığı halde bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar. Sonraki öğretici üzerinde hemen çalışmayı planlıyorsanız, kümeyi tutmak isteyebilirsiniz.

Azure portalında kümeyi açıp **Sil**’i seçin.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png " alt-text="HDInsight kümesini Sil" border="true":::

Kaynak grubu adını seçerek de kaynak grubu sayfasını açabilir ve sonra **Kaynak grubunu sil**’i seçebilirsiniz. Kaynak grubunu silerek hem HDInsight Spark kümesini hem de varsayılan depolama hesabını silersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir CSV dosyasından bir veri çerçevesi oluşturmayı ve etkileşimli Spark SQL sorgularını Azure HDInsight 'ta bir Apache Spark kümesine karşı çalıştırmayı öğrendiniz. Apache Spark ' de kaydettiğiniz verilerin Power BI gibi bir bı Analytics aracına nasıl çekkullanılabileceğini görmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [BI araçlarını kullanarak verileri analiz etme](apache-spark-use-bi-tools.md)

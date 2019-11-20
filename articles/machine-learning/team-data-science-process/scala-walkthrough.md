---
title: Azure - Team Data Science Process Scala ve Spark kullanan veri bilimi
description: Scala Spark ölçeklenebilir MLlib ve Spark ML paketleri ile bir Azure HDInsight Spark kümesi üzerinde denetimli makine öğrenimi görevlerini kullanmak nasıl.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b22d461d327e595908ea8cc18dd0d507fdc83ecd
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907706"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Azure üzerinde Scala ve Spark kullanan Veri Bilimi
Bu makalede, Scala Spark ölçeklenebilir MLlib ve Spark ML paketleri ile bir Azure HDInsight Spark kümesi üzerinde denetimli makine öğrenimi görevlerini kullanmak nasıl gösterir. Oluşturan görevlerinde size yol gösterir [veri bilimi işlemi](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): veri alımı ve keşfi, görselleştirme, özellik Mühendisliği, modelleme ve model tüketim. Makaleyi modellerinde Lojistik ve doğrusal regresyon, rastgele ormanları ve gradyan boosted ağaçları (GBTs) ek olarak iki ortak denetimli makine öğrenimi görevlerini içerir:

* Gerileme sorunu: Bir TAXI yolculuğu için tip tutarının Tahmini ($)
* İkili sınıflandırma: Bir taksi yolculuğu için tıp tahmini veya ipucu (1/0)

Modelleme işlemi, eğitim ve değerlendirme sınama veri kümesi ve ilgili doğruluğu ölçümleri gerektirir. Bu makalede, bu modeller Azure Blob Depolama alanında depolayın ve puan ve Tahmine dayalı performanslarını değerlendirmek bilgi edinebilirsiniz. Bu makalede, çapraz doğrulama ve hiper parametreli Süpürme'ı kullanarak modelleri iyileştirmek nasıl daha gelişmiş konular da kapsar. Kullanılan veri kümesinin 2013 NYC taksi seyahat ve taksi verileri Github'da bulunan bir örnektir.

[Scala](https://www.scala-lang.org/), Java sanal makineye dayalı bir dili, nesne yönelimli ve işlevsel dil kavramları tümleştirir. Bu, bulutta dağıtılan işleme için uygundur ve Azure Spark kümelerinde çalıştırılır ölçeklenebilir bir dildir.

[Spark](https://spark.apache.org/) olduğundan büyük veri analizi uygulamalarının performansını artırmak üzere bellek içi işlemeyi destekleyen açık kaynaklı paralel işleme altyapısıdır. Spark işleme altyapısı hız, kullanım kolaylığı ve Gelişmiş analiz için oluşturulmuştur. Spark'ın dağıtılmış bellek içi hesaplama özellikleri makine öğrenimi ve grafik hesaplamalarında yinelemeli algoritmalar için iyi bir seçim yapın. [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) paket Tekdüzen bir yardımcı olabilecek bir çerçeve oluşturun ve pratik machine learning işlem hatlarını ayarlama veri üzerinde oluşturulan üst düzey API kümesi sağlar. [MLlib](https://spark.apache.org/mllib/) Spark'ın ölçeklenebilir makine öğrenimi kitaplığı dağıtılmış bu ortama modelleme özellikleri sunar.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) açık kaynaklı Spark'ın Azure'da barındırılan teklifidir. Ayrıca Spark kümesinde Jupyter Scala not defterleri için destek içerir ve dönüştürme, filtreleme ve Azure Blob depolamada depolanan verileri görselleştirmek için Spark SQL etkileşimli sorguları çalıştırabilirsiniz. Jupyter not defterlerini Spark kümelerinde yüklü çözümler sağlayın ve verileri görselleştirmek için ilgili çizimleri Göster Scala kod parçacıkları bu makaledeki çalıştırın. Bu konu başlıklarındaki modelleme adımları, eğitme, değerlendirmek, kaydetme ve her bir türü modelin kullanma işlemi gösterilmektedir koda sahip.

Kurulum adımları ve kod bu makalede Azure HDInsight 3.4 Spark 1.6 için var. Ancak, bu makalede hem de kodu [Scala Jupyter not defteri](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) geneldir ve tüm Spark kümesinde çalışması gerekir. Küme Kurulum ve yönetim adımları HDInsight Spark kullanmıyorsanız ne bu makalede gösterilenden biraz farklı olabilir.

> [!NOTE]
> Scala yerine Python uçtan uca veri bilimi işlemi için görevleri tamamlamak için nasıl kullanılacağını gösteren bir konu için bkz: [Azure HDInsight üzerinde Spark'ı kullanarak veri bilimi](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Önkoşullar
* Bir Azure aboneliğiniz olmalıdır. Zaten bir tamamlamadıysanız [Azure ücretsiz deneme sürümü edinin](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Aşağıdaki yordamları tamamlamak için bir Azure HDInsight 3.4 Spark 1.6 kümesine ihtiyacınız vardır. Bir küme oluşturmak için Başlarken ' deki [yönergelere bakın: Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)üzerinde Apache Spark oluşturun. Küme türü ve sürümü ayarlamak **küme türü seçin** menüsü.

![HDInsight küme türü yapılandırması](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Spark kümesinde Jupyter not defteri gelen kodu çalıştırmak yönergeler ve NYC taksi seyahat veri açıklaması için ilgili bölümlere bakın [genel bakış, verilerin Azure HDInsight üzerinde Spark'ı kullanarak bilimi](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spark kümesinde Jupyter not defteri gelen Scala kod yürütün
Jupyter not defteri Azure portalından başlatabilirsiniz. Panonuzda Spark kümesini bulun ve kümeniz için Yönetim sayfasında girmek için tıklatın. Ardından, **küme panoları**ve ardından **Jupyter not defteri** Spark kümesiyle ilişkili not defterini açın.

![Küme panosu ve Jupyter Not Defterleri](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Jupyter not defterleri https:// sırasında da erişebilirsiniz&lt;clustername&gt;.azurehdinsight.net/jupyter. Değiştirin *clustername* değerini kümenizin adıyla. Jupyter not defterlerini erişmek, yönetici hesabı için parola gerekir.

![Jupyter not defterleri için küme adını kullanarak Git](./media/scala-walkthrough/spark-jupyter-notebook.png)

Seçin **Scala** PySpark API kullanan önceden paketlenmiş not defterlerini birkaç örnek sahip bir dizini görmek için. Araştırma model ve puanlama kodu içeren Scala.ipynb Not Defteri kullanarak Spark konular bu paketi üzerinde kullanılabilir örnekleri [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Jupyter not defteri sunucusuna doğrudan github'dan bir Not Defteri kullanarak Spark kümenizde karşıya yükleyebilirsiniz. Jupyter giriş sayfanızda tıklayın **karşıya** düğmesi. Dosya Gezgini'nde, Scala not defterinin GitHub (ham içeriği) URL'yi yapıştırın ve ardından **açık**. Scala not defterine aşağıdaki URL'de kullanılabilir:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Yapısı Spark ve Hive bağlamlarını, Spark mıknatıc ve Spark kitaplıklarını önceden ayarlayın
### <a name="preset-spark-and-hive-contexts"></a>Önceden oluşturulmuş Spark ve Hive bağlamları
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Jupyter not defterleri ile sağlanan Spark çekirdekler bağlamları önceden. Uygulama ile çalışmaya başlamadan önce Hive bağlamları geliştirme ya da açıkça Spark ayarlamanız gerekmez. Önceden ayarlanmış Bağlamlar şunlardır:

* `sc` zamanda sparkcontext öğesini için
* `sqlContext` HiveContext için

### <a name="spark-magics"></a>Spark işlevlerini
Bazı önceden tanımlanmış "işlevlerini" ile çağırabileceğiniz özel komutlar olduğu Spark çekirdek sağlar `%%`. Bu komutların ikisi, aşağıdaki kod örnekleri kullanılır.

* `%%local` sonraki satırların kodu yerel olarak yürütülecek belirtir. Kod, geçerli Scala kodu olmalıdır.
* `%%sql -o <variable name>` bir Hive sorgusu yürütür `sqlContext`. Varsa `-o` parametresi geçirilir, sorgu sonucu kalıcı hale getirilir `%%local` Scala bağlamı bir Spark veri çerçevesine olarak.

İle çağrı, Jupyter not defterleri ve kendi önceden tanımlanmış çekirdekler hakkında daha fazla bilgi "magics için" `%%` (örneğin, `%%local`), bkz: [için Jupyter not defterlerinde kullanılabilen çekirdekler HDInsight Spark Linux kümeleri üzerinde HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Kitaplıkları içeri aktarma
Spark MLlib ve aşağıdaki kodu kullanarak ihtiyacınız olan diğer kitaplıklarla içeri aktarın.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Veri alımı
Veri bilimi işlemi ilk adımı, analiz etmek istediğiniz veri alma sağlamaktır. Verilerin dış kaynaklardan depolamadan sistemler yer aldığı veri keşfi ve modelleme ortamınıza taşıyın. Bu makalede, alma, verileri bir birleştirilmiş %0,1 taksi seyahat ve taksi (.tsv dosyası olarak depolanır) dosyası örneğidir. Veri keşfi ve modelleme Spark ortamıdır. Bu bölüm, aşağıdaki görev dizisini tamamlamak için kodu içerir:

1. Depolama veri ve model için dizin yolları ayarlayın.
2. Giriş veri kümesinde (.tsv dosyası olarak depolanır) okuyun.
3. Veriler için bir şema tanımlayabilir ve yalnızca verileri temizledik.
4. Temizlenen veri çerçevesi oluşturun ve bellekte önbelleğe alır.
5. Veri SQLContext geçici tablo olarak kaydedin.
6. Tablo sorgulama ve sonuçları bir veri çerçevesine içeri aktarın.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Azure Blob Depolama alanında depolama konumları için dizin yolları ayarlayın
Spark okuyup Azure Blob depolamaya yazabilir. Tüm mevcut verilerinizi işlemek için Spark'ı kullanın ve sonra sonuçları Blob storage'da depolamak.

Modelleri veya dosyaları Blob depolama alanına kaydetmek için doğru yolunu belirtmeniz gerekir. İle başlayan bir yol kullanarak Spark kümesine eklenen varsayılan kapsayıcı başvuru `wasb:///`. Diğer yerlerden başvuruda `wasb://`.

Aşağıdaki kod örneği, girdi verilerini okumak için ve yolun Spark kümesine eklenmiş olan Blob depolama alanına model kaydedileceği konumu belirtir.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Veri alma, bir RDD oluşturmak ve şemaya göre bir veri çerçevesi tanımlayın
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Çıkış:**

Hücrenin çalıştırılacağı süre: 8 saniye.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Tablo sorgulama ve sonuçları bir veri çerçevesinde İçeri Aktar
Ardından, taksi, yolcular ve ipucu verileri için bir tabloyu sorgulamak; bozuk ve harici verileri filtrelemek; ve birkaç satır yazdırın.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Çıkış:**

| fare_amount | passenger_count | tip_amount | Eğimli |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Veri keşfi ve görselleştirme
Spark'a verileri getirdikten sonra veri bilimi işlemi sonraki adımda, veri keşfi ve görselleştirme üzerinde daha derin bir anlayış kazanmak sağlamaktır. Bu bölümde, taksi verileri SQL sorgularını kullanarak inceleyin. Ardından, bir veri çerçevesi Jupyter otomatik görselleştirme özelliğini kullanarak görsel denetim için olası özellikleri ve hedef değişkenler çizmek için sonuçları alın.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Veri çizimi için yerel ve SQL Sihri kullanın
Varsayılan olarak, bir Jupyter not defteri çalıştırmak herhangi bir kod parçacığı çıkışı çalışan düğümlerine kalıcı oturum bağlamında kullanılabilir. Her hesaplama için çalışan düğümüne bir seyahat kaydetmek istiyorsanız ve, hesaplama için ihtiyacınız olan tüm verileri (aynı baş düğüm) yerel olarak Jupyter sunucu düğümünde mevcut ise, kullanabileceğiniz `%%local` magic üzerinde Jupyter kod parçacığını çalıştırın Sunucu.

* **SQL Sihri** (`%%sql`). HDInsight Spark çekirdek kolay satır içi HiveQL SQLContext sorguları destekler. (`-o VARIABLE_NAME`) Bağımsız değişken Jupyter sunucuda Pandas bir veri çerçevesi olarak SQL sorgusunun çıktısını sürdürür. Başka bir deyişle, yerel modda kullanıma sunulacak.
* `%%local` **Magic**. `%%local` Magic çalışan kodu yerel olarak HDInsight kümesinin baş düğüm Jupyter sunucusu üzerinde. Genellikle, kullandığınız `%%local` birlikte Sihirli `%%sql` ile Sihirli `-o` parametresi. `-o` Parametresi yerel olarak SQL sorgusunun çıktısını kalıcı ve ardından `%%local` Sihirli yerel olarak karşı ve yerel olarak kalıcı çıkış SQL sorguları çalıştırmak için kod parçacığı bir sonraki kümesini tetikleme.

### <a name="query-the-data-by-using-sql"></a>SQL kullanarak verileri Sorgulama
Bu sorgu, taksi gelişlerin taksi tutar, yolcular sayısı ve ipucu miktarını alır.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

Aşağıdaki kodda, `%%local` Sihirli bir yerel veri çerçevesi sqlResults oluşturur. SqlResults matplotlib kullanarak çizmek için kullanabilirsiniz.

> [!TIP]
> Yerel Sihirli bu makalede birden çok kez kullanıldı. Veri kümeniz büyükse, yerel belleğe sığması bir veri çerçevesi oluşturmak için lütfen örnek.
> 
> 

### <a name="plot-the-data"></a>Veri çizimi
Yerel içeriği Pandas veri çerçevesi olarak veri çerçevesidir sonra Python kodu kullanarak çizebilirsiniz.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Kod çalıştırdıktan sonra Spark çekirdek (HiveQL) SQL sorguları çıktısını otomatik olarak görselleştirir. Birkaç görselleştirme türleri arasında seçim yapabilirsiniz:

* Tablo
* Pasta
* Çizgi
* Alan
* Çubuk

Veri çizimi için kod aşağıdaki gibidir:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Çıkış:**

![İpucu tutarı çubuk grafik](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![İpucu miktarda yolcular sayısı](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Taksi miktar tutarı İpucu](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Özellikleri oluşturup özellikleri dönüştürmek ve ardından veri modelleme işlevleri içine girişi için hazırlama
Spark ML ve MLlib ağaç tabanlı modelleme işlevleri için çeşitli gruplama, dizin oluşturma, sık erişimli bir kodlama ve vektörleştirme gibi teknikler kullanılarak, hedef ve Özellikler hazırlamanız gerekir. Bu bölümde izlemek için yordamlar şunlardır:

1. Yeni bir özellik tarafından oluşturma **gruplama** trafiği saatlerine zaman demetleri.
2. Uygulama **dizin oluşturma ve sık erişimli bir kodlama** kategorik özellikleri.
3. **Örnek ve veri kümesi bölme** içine eğitim ve test faturalandırılmayacak.
4. **Eğitim değişkeni ve Özellikler belirtmek**, ardından eğitim kodlanmış dizinli veya bir seyrek oluşturmak ve test etiketli giriş noktası dayanıklı Dağıtılmış veri kümeleri (Rdd) veya veri çerçevelerini.
5. Otomatik olarak **kategorilere ayırmak ve özellikler ve hedefler vektör hale getirmeye** girdi olarak makine öğrenimi modelleri için kullanılacak.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Yeni bir özellik olarak gruplama saat trafiği zaman demetlerin içine oluşturun.
Bu kod, yeni bir özellik olarak gruplama saat trafiği zaman demetlerin içine oluşturma ve sonuçta elde edilen veri çerçevesi bellekte önbelleğe alınacağını gösterir. Rdd ve veri çerçevelerini tekrar tekrar kullanıldığı geliştirilmiş yürütme sürelerini müşteri adaylarını önbelleğe alma. Buna, Rdd ve veri çerçevelerini aşağıdaki yordamları çeşitli aşamalarında önbelleğe alınır.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Dizin oluşturma ve sık erişimli bir kategorik özelliklerinin kodlama
Modelleme ve tahmin MLlib işlevlerini dizine veya kullanılmadan önce kodlanmış için kategorik girdi verilerini özelliklerle gerektirir. Bu bölümde dizin veya kodlama giriş modelleme işlevleri için kategorik özellikleri gösterilmektedir.

Dizin veya modeline bağlı olarak, farklı şekillerde Modellerinizi kodlamanız gerekir. Örneğin, mantıksal ve doğrusal regresyon modellerini bir seyrek kodlama gerektirir. Örneğin, üç kategoriye sahip bir özellik, üç özellik sütunlara genişletilebilir. Her sütun, 0 veya 1 gözlemi kategorisine bağlı olarak içerecektir. MLlib sağlar [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) sık erişimli bir kodlama için işlevi. Bu Kodlayıcı etiket dizinleri içeren bir sütun ikili vektörleri en fazla bir değerle tek bir-bir sütunu eşlenir. Bu kodlama ile Lojistik regresyon gibi sayısal değerli özellikler beklediğiniz algoritmaları kategorik özelliklerine uygulanabilir.

Burada, karakter dizeleri olan örnekler göstermek için yalnızca dört değişken dönüştürün. Ayrıca, kategorik değişkenleri olarak sayısal değerler tarafından temsil edilen diğer gibi değişkenleri haftanın günü, dizine ekleyebilir.

Dizin oluşturma için kullanmak `StringIndexer()`ve sık erişimli bir kodlama için `OneHotEncoder()` MLlib işlevleri. Dizin ve kategorik özellikleri kodlamak için kod aşağıdaki gibidir:

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Çıkış:**

Hücrenin çalıştırılacağı süre: 4 saniye.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Örnek ve bölme eğitim ve test parçalar halinde veri kümesi
Bu kod, rastgele bir örnekleme verilerin (Bu örnekte, %25) oluşturur. Örnekleme, bu örnekte veri kümesinin boyutu nedeniyle gerekli olmamasına karşın, bu makalede, böylece gerektiğinde kendi sorunları kullanma bilirsiniz nasıl, örnek oluşturabilirsiniz gösterilir. Örnekleri büyük olduğunda, modelleri eğitme sırada bu önemli zamandan tasarruf edebilirsiniz. Ardından, örnek bir eğitim bölümü (Bu örnekte, %75) ve test bölümü (Bu örnekte, %25) sınıflandırma ve regresyon modelleme bölün.

Rastgele bir sayı (0 ve 1 arasında) çapraz doğrulama hatları eğitim sırasında seçmek için kullanılan her bir satır ("rand" sütununda) ekleyin.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Çıkış:**

Hücrenin çalıştırılacağı süre: 2 saniye.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Eğitim değişkeni ve özellikleri belirtin ve ardından eğitim ve giriş noktası Rdd ya da veri çerçevelerini etiketli testi kodlanmış dizinli veya bir seyrek oluşturun
Bu bölüm, kategorik metin veri noktası etiketli veri türü olarak dizin ve eğitme ve test MLlib Lojistik regresyon ve diğer sınıflandırma modellerini üzere kullanabilmeniz için kodlamak gösteren kod içerir. Etiketli noktası, girdi verisi olarak makine öğrenimi algoritması MLlib içinde çoğu için gerekli olan bir şekilde biçimlendirilmiş Rdd nesneleridir. A [noktası etiketli](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) yerel bir vektör, yoğun ya da seyrek, etiket/yanıt ile ilişkilidir.

Bu kodda, hedef (bağımlı) değişkeni ve modelleri eğitmek için kullanmak için özellikleri belirtin. Ardından, eğitim ve giriş noktası Rdd ya da veri çerçevelerini etiketli testi kodlanmış dizinli veya bir seyrek oluşturun.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Çıkış:**

Hücrenin çalıştırılacağı süre: 4 saniye.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Otomatik olarak kategorilere ayırmak ve özellikler ve hedefler için makine öğrenimi modellerini girdi olarak kullanmak için vektör hale getirmeye
Spark ML, ağaç tabanlı modelleme işlevleri kullanmak için özellikler ve hedef kategorilere ayırmak için kullanın. Kod, iki görevleri gerçekleştirir:

* 0 veya 1 değerini 0 ile 1 arasında her bir veri noktasına 0,5 eşik değerini kullanarak atayarak sınıflandırma için ikili bir hedef oluşturur.
* Otomatik olarak özellikleri kategorilere ayırır. Bu özellik, herhangi bir özellik için farklı sayısal değerleri sayısı 32'den az ise, kategorilere ayrılmıştır.

Burada, bu iki görevleri için kodu verilmiştir.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>İkili sınıflandırma modeli: Bir ipucunun ödenip ödenmeyeceğini tahmin etme
Bu bölümde, üç türleri ipucu Ücretli olup olmadığını tahmin etmek için ikili sınıflandırma modellerini oluşturun:

* A **Lojistik regresyon modelini** Spark ML kullanarak `LogisticRegression()` işlevi
* A **rastgele orman sınıflandırma modeli** Spark ML kullanarak `RandomForestClassifier()` işlevi
* A **gradyan artırırken ağacı sınıflandırma modeli** MLlib kullanarak `GradientBoostedTrees()` işlevi

### <a name="create-a-logistic-regression-model"></a>Bir Lojistik regresyon modeli oluşturun
Ardından, Spark ML kullanarak bir Lojistik regresyon modeli oluşturun `LogisticRegression()` işlevi. Bir dizi adım kod oluşturma modeli oluşturun:

1. **Modeli eğitme** bir parametre kümesi ile verileri.
2. **Modeli değerlendirme** ölçümlerle test veri kümesinde.
3. **Modeli kaydedin** gelecekteki kullanım için Blob Depolama.
4. **Modeli Puanlama** karşı test verilerinin.
5. **Sonuçları çizim** alıcı çalıştırma özellikleri (ROC) eğrileri ile.

Bu yordamlar için kod aşağıdaki gibidir:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Yük, Puanlama ve sonuçları kaydedin.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Çıkış:**

Test veri çubuğunda ROC 0.9827381497557599 =

Python yerel Pandas veri karelerden ROC eğrisi çizmek için kullanın.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**Çıkış:**

![İpucu veya ipucu ROC eğrisi çizimleri yok](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Bir rastgele orman sınıflandırma modeli oluşturma
Ardından, Spark ML kullanarak rastgele orman sınıflandırma modeli oluşturma `RandomForestClassifier()` çalışması ve test veri modelinde değerlendirebilirsiniz.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Çıkış:**

Test veri çubuğunda ROC 0.9847103571552683 =

### <a name="create-a-gbt-classification-model"></a>GBT sınıflandırma modeli oluşturma
Ardından, MLlib'ın kullanarak GBT sınıflandırma modeli oluşturma `GradientBoostedTrees()` çalışması ve test veri modelinde değerlendirebilirsiniz.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Çıkış:**

ROC eğrisi altındaki alan: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regresyon modeli: İpucu tutarını tahmin etme
Bu bölümde, iki türleri ipucu miktarı tahmin etmek için regresyon modeli oluşturun:

* A **regularized doğrusal regresyon modeli** Spark ML kullanarak `LinearRegression()` işlevi. Modeli kaydedin ve modeli test veri çubuğunda değerlendirme.
* A **gradyan yükseltmeli ağaç regresyon modeli** Spark ML kullanarak `GBTRegressor()` işlevi.

### <a name="create-a-regularized-linear-regression-model"></a>Bir regularized doğrusal regresyon modeli oluşturun
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Çıkış:**

Hücrenin çalıştırılacağı süre: 13 saniye.

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Çıkış:**

Test verileri üzerindeki R sqr 0.5960320470835743 =

Ardından, bir veri çerçevesi test sonuçlarını sorgulama ve bunları görselleştirmek için AutoVizWidget ve matplotlib kullanın.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Kod bir yerel veri çerçevesine sorgu çıkışı oluşturur ve verileri çizer. `%%local` Sihirli bir yerel veri çerçevesine oluşturur `sqlResults`, hangi ile matplotlib çizmek için kullanabilirsiniz.

> [!NOTE]
> Bu Spark Sihirli bu makalede birden çok kez kullanıldı. Veri miktarı büyükse, yerel belleğe sığması bir veri çerçevesi oluşturmak için örnek.
> 
> 

Çizimler Python matplotlib kullanarak oluşturun.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Çıkış:**

![İpucu miktarı: Gerçek ve tahmin edilen](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>GBT regresyon modeli oluşturun
Spark ML kullanarak bir GBT regresyon modeli oluşturun `GBTRegressor()` çalışması ve test veri modelinde değerlendirebilirsiniz.

[Gradyan boosted ağaçları](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) olan Kümelemeler karar ağaçları (GBTs). GBTs çalıştırmalarınızı kaybı işlevi en aza indirmek için karar ağaçları eğitin. Sınıflandırma ve regresyon GBTs kullanabilirsiniz. Bunlar kategorik özellikleri işleyebilir, özellik ölçeklendirme gerektirmez ve nonlinearities ve özellik etkileşimleri yakalayabilirsiniz. Ayrıca bunları bir sınıflandırma veya çoklu sınıflar ayarında kullanabilirsiniz.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Çıkış:**

Test R-SQR: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>İyileştirme için Gelişmiş modelleme yardımcı programları
Bu bölümde, geliştiricilerin model iyileştirme için sık kullandığınız machine learning yardımcı programlarını kullanın. Özellikle, parametre Süpürme ve çapraz doğrulama kullanarak makine öğrenimi modelleri üç farklı yolla iyileştirebilirsiniz:

* Verileri eğitme ve doğrulama kümeleri halinde bölmek, eğitim kümesinde hiper parametreli Süpürme kullanarak model iyileştirin ve doğrulama kümesinde (doğrusal regresyon) değerlendir
* Çapraz doğrulama ve hyper-Spark ML'ın CrossValidator işlevi (ikili sınıflandırma) kullanarak üst düzey parametre kullanarak model en iyi duruma getirme
* Herhangi bir makine öğrenme işlevi ve parametre kümesi (doğrusal regresyon) kullanmak için özel çapraz doğrulama ve parametre Süpürme kodu kullanarak model en iyi duruma getirme

**Çapraz doğrulama** ne kadar iyi bilinen bir veri kümesini eğitilmiş bir modeli veri kümeleri üzerinde bu eğitilmedi özelliklerinin tahmin etmek için generalize değerlendirir bir tekniktir. Bu tekniğin arkasındaki genel fikir, bir model, bilinen verileri bir veri kümesinde çalıştırılır ve ardından kendi tahmin doğruluğunu bağımsız bir veri kümesi karşı test edilmiştir ' dir. Bir veri kümesine bölmek için ortak bir uygulama olan *k*-katlayan ve ardından ettirirsiniz hatları biri dışındaki tüm modelde eğitin.

**Hyper-parametre iyileştirme** hyper-parametreleriyle bir öğrenme algoritması için bir dizi genellikle bir ölçü bağımsız bir veri kümesi üzerinde algoritması'nın performansını en iyi duruma getirme amacıyla seçme sorunudur. Parametre hyper dışında modeli eğitimi yordamı belirtmeniz gereken bir değerdir. Hiper parametre değerleri ile ilgili varsayım, esneklik ve model doğruluğunu etkileyebilir. Karar ağaçları leaves ağacında sayısı ve istenen derinliği gibi hyper-parametreleriyle, örneğin, sahip. Misclassification cezası terimi destekli vektör makinesi (SVM) için ayarlamanız gerekir.

Hiper parametreli iyileştirme gerçekleştirmek için yaygın bir yolu olarak da adlandırılan bir kılavuz arama kullanmaktır bir **parametre tarama**. Bir kılavuz aramada dizinin ayrıntılı aramasını değerleri belirtilen bir alt kümesine bir öğrenme algoritması için hiper parametreli alanı aracılığıyla gerçekleştirilir. Çapraz doğrulama, en iyi sonuçlar kılavuz arama algoritma tarafından üretilen çıkış sıralamak için bir performans ölçümü sağlayabilirsiniz. Çapraz doğrulama hiper parametreli Süpürme kullanırsanız, bir eğitim veri modeline overfitting gibi sınırı sorunları yardımcı olabilir. Bu şekilde, modeli genel içinden eğitim verileri ayıklandı veri kümesine uygulanacak kapasite korur.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Bir doğrusal regresyon modeli hiper parametreli Süpürme ile en iyi duruma getirme
Ardından, veri eğitme ve doğrulama kümeleri, kullanım hyper-model iyileştirin ve doğrulama kümesinde (doğrusal regresyon) değerlendirmek için Eğitim kümesi üzerinde Süpürme parametresi bölün.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Çıkış:**

Test R-SQR: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Çapraz doğrulama ve hiper parametreli Süpürme kullanarak ikili sınıflandırma modelinde en iyi duruma getirme
Bu bölümde, çapraz doğrulama ve hiper parametreli Süpürme kullanarak ikili sınıflandırma modelinde en iyi duruma getirme gösterir. Bu, Spark ML kullanır `CrossValidator` işlevi.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Çıkış:**

Hücrenin çalıştırılacağı süre: 33 saniye.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Çapraz doğrulama ve parametre Süpürme özel kod kullanarak doğrusal regresyon modelinin en iyi duruma getirme
Ardından, özel kod kullanarak model iyileştirin ve en yüksek doğruluğa ölçütünü kullanarak en iyi modeli parametreleri tanımlayın. Daha sonra son modelin oluşturma, modeli test veri çubuğunda değerlendirme ve Blob Depolama alanında modeli kaydedin. Son olarak, yük modeli, test verilerini puanlamak ve doğruluğu değerlendirin.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Çıkış:**

Hücrenin çalıştırılacağı süre: 61 saniye.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Spark'a yerleşik machine learning modellerini Scala ile otomatik olarak kullanma
Azure veri bilimi işlemi oluşturan görevler rehberlik konuları genel bakış için bkz. [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

[Team Data Science Process Kılavuzu](walkthroughs.md) Team Data Science Process belirli senaryolar için adımları gösteren diğer uçtan uca izlenecek yolları açıklanmaktadır. İzlenecek yollar, ayrıca bir iş akışı veya işlem hattı akıllı bir uygulama oluşturmak için bulut ve şirket içi araçları ve Hizmetleri birleştirme işlemini göstermektedir.

[Spark'a yerleşik makine öğrenimi modellerini puanlamanıza](spark-model-consumption.md) Scala kod otomatik olarak yükleyin ve yeni veri kümeleri ile Spark, oluşturulan ve Azure Blob Depolama'ya machine learning modellerini Puanlama için nasıl kullanılacağını gösterir. Burada sağlanan yönergeleri izleyin ve yalnızca Scala kod otomatik tüketim için bu makalede Python kodunu değiştirin.


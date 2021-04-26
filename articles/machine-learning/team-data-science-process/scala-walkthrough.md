---
title: Azure 'da Scala ve Spark kullanan veri bilimi-ekip veri bilimi Işlemi
description: Azure HDInsight Spark kümesinde Spark ölçeklenebilir MLlib ve Spark ML paketleri ile denetimli makine öğrenimi görevleri için Scala 'yı kullanma.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9ae4549fe343422bbf60275a97768ca407f2dc7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321381"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Azure üzerinde Scala ve Spark kullanan Veri Bilimi
Bu makalede, bir Azure HDInsight Spark kümesinde Spark ölçeklenebilir MLlib ve Spark ML paketleri ile denetimli makine öğrenimi görevleri için Scala 'nın nasıl kullanılacağı gösterilmektedir. [Veri bilimi işlemini](./index.yml)ve araştırma, görselleştirme, özellik Mühendisliği, modelleme ve model tüketimini oluşturan görevlerde size kılavuzluk eder. Makaledeki modeller, lojistik ve doğrusal regresyon, rastgele ormanlar ve gradyan-artırılmış ağaçlar (GBTs), yaygın olarak denetlenen, denetlenen makine öğrenimi görevlerinin yanı sıra şunları içerir:

* Regresyon sorunu: bir TAXI yolculuğu için tip tutarının ($) tahmini
* İkili sınıflandırma: bir TAXI yolculuğu için ipucu veya ipucu (1/0) tahmin

Modelleme işlemi bir test veri kümesi ve ilgili doğruluk ölçümleri üzerinde eğitim ve değerlendirme gerektirir. Bu makalede, Azure Blob depolama alanında bu modelleri nasıl depolayacağınızı ve tahmine dayalı performanslarını nasıl değerlendirileceğini ve değerlendirebileceğinizi öğrenebilirsiniz. Bu makalede ayrıca çapraz doğrulama ve hiper parametre kullanımı kullanılarak modellerin nasıl iyileştirileceği hakkında daha gelişmiş konular ele alınmaktadır. Kullanılan veriler, GitHub 'da kullanılabilen 2013 NYC TAXI seyahat ve tarifeli havayolu veri kümesinin bir örneğidir.

Java sanal makinesini temel alan bir dil olan [Scala](https://www.scala-lang.org/), nesne yönelimli ve işlevsel dil kavramlarını tümleştirir. Bulutta dağıtılmış işleme uygun olan ölçeklenebilir bir dildir ve Azure Spark kümelerinde çalışır.

[Spark](https://spark.apache.org/) , büyük veri analizi uygulamalarının performansını artırmak üzere bellek içi işlemeyi destekleyen açık kaynaklı bir paralel işleme çerçevesidir. Spark işleme altyapısı hız, kullanım kolaylığı ve gelişmiş analiz için oluşturulmuştur. Spark 'ın bellek içi dağıtılmış hesaplama özellikleri, makine öğrenimi ve grafik hesaplamaları ' nda yinelemeli algoritmalar için iyi bir seçim yapar. [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) paketi, pratik makine öğrenimi işlem hatlarını oluşturmanıza ve ayarlamanıza yardımcı olabilecek veri çerçevelerinin üzerine inşa edilen tek düzeyli bir üst düzey API kümesi sağlar. [Mllib](https://spark.apache.org/mllib/) , bu dağıtılmış ortama modelleme özellikleri getiren Spark 'ın ölçeklenebilir makine öğrenme kitaplığıdır.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) , açık kaynaklı Spark 'ın Azure tarafından barındırılan sunumudur. Ayrıca Spark kümesinde jupi Scala için destek içerir ve Azure Blob depolama alanında depolanan verileri dönüştürmek, filtrelemek ve görselleştirmek için Spark SQL etkileşimli sorgularını çalıştırabilir. Bu makaledeki, çözümleri sağlayan ve Spark kümelerine yüklenen jupi not defterlerinde çalışan verileri görselleştirmek için ilgili çizimleri gösteren Scala kod parçacıkları. Bu konulardaki modelleme adımlarında, her model türünü eğitme, değerlendirme, kaydetme ve kullanma hakkında sizi gösteren kod vardır.

Bu makaledeki kurulum adımları ve kodu, Azure HDInsight 3,4 Spark 1,6 içindir. Ancak, bu makaledeki ve [Scala Jupyter Notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) kod geneldir ve tüm Spark kümeleri üzerinde çalışmalıdır. HDInsight Spark kullanmıyorsanız, küme kurulumu ve yönetim adımları Bu makalede gösterilenden biraz farklı olabilir.

> [!NOTE]
> Uçtan uca bir veri bilimi işleminin görevlerini gerçekleştirmek için Scala yerine Python 'u nasıl kullanacağınızı gösteren bir konu için bkz. [Azure HDInsight 'Ta Spark kullanarak veri bilimi](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Önkoşullar
* Bir Azure aboneliğiniz olmalıdır. Henüz bir tane yoksa [Azure Ücretsiz deneme sürümü alın](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Aşağıdaki yordamları tamamlayabilmeniz için bir Azure HDInsight 3,4 Spark 1,6 kümesine ihtiyacınız vardır. Bir küme oluşturmak için bkz. [Başlarken: Azure HDInsight üzerinde Apache Spark oluşturma](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Küme türünü **seçin** menüsünde küme türünü ve sürümünü ayarlayın.

![HDInsight kümesi tür yapılandırması](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

NYC TAXI seyahat verilerinin açıklaması ve Spark kümesindeki bir Jupyter Not defterinden kod yürütme yönergeleri için bkz. [Azure HDInsight 'Ta Spark kullanarak veri bilimine genel bakış](spark-overview.md)konusundaki ilgili bölümler.  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spark kümesindeki bir Jupyter Not defterinden Scala kodu yürütün
Azure portal bir Jupyter Not defteri başlatabilirsiniz. Panonuzda Spark kümesini bulun ve ardından kümenize ait yönetim sayfasını girmek için tıklayın. Ardından, **küme panoları**' na tıklayın ve ardından **Jupyter Notebook** ' a tıklayarak Spark kümesiyle ilişkili Not defterini açın.

![Küme panosu ve Jupyıter Not defterleri](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Ayrıca, https:// &lt; clustername. azurehdinsight.net/Jupyter adresinden jupi not defterlerine erişebilirsiniz &gt; . *Clustername* değerini kümenizin adıyla değiştirin. Jupyıter not defterlerine erişmek için yönetici hesabınız için parola gerekir.

![Küme adını kullanarak Jupyıter not defterlerine git](./media/scala-walkthrough/spark-jupyter-notebook.png)

PySpark API kullanan önceden paketlenmiş not defterlerine örnek bir dizin görmek için **Scala** ' yı seçin. Bu Spark konuları paketine yönelik kod örneklerini içeren Scala. ipynb Not defterini kullanan keşif modelleme ve Puanlama, [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)' da kullanılabilir.

Not defterini doğrudan GitHub 'dan Spark kümenizdeki Jupyter Notebook sunucusuna yükleyebilirsiniz. Jupyıter giriş sayfanızda **karşıya yükle** düğmesine tıklayın. Dosya Gezgini 'nde, Scala Not defterinin GitHub (ham içerik) URL 'sini yapıştırın ve **Aç**' a tıklayın. Scala Not defteri Şu URL 'de kullanılabilir:

[Araştırma-modelleme ve Puanlama-kullanma-Scala. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Kurulum: önceden ayarlanmış Spark ve Hive bağlamları, Spark mıknatıc ve Spark kitaplıklarını
### <a name="preset-spark-and-hive-contexts"></a>Önceden oluşturulmuş Spark ve Hive bağlamları

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

Jupyter Not defterleri ile birlikte sunulan Spark çekirdekler 'in önceden ayarlanmış bağlamları vardır. Geliştirmekte olduğunuz uygulamayla çalışmaya başlamadan önce Spark veya Hive bağlamlarını açıkça ayarlamanız gerekmez. Önceden ayarlanmış bağlamlar şunlardır:

* `sc` Mini bağlam için
* `sqlContext` HiveContext için

### <a name="spark-magics"></a>Spark mıknatıcs
Spark çekirdeği, ile çağırabilmeniz için bazı önceden tanımlanmış "mıknatıcs" sağlar `%%` . Aşağıdaki kod örneklerinde bu komutlardan ikisi kullanılır.

* `%%local` sonraki satırlardaki kodun yerel olarak yürütüleceğini belirtir. Kod geçerli bir Scala kodu olmalıdır.
* `%%sql -o <variable name>` için bir Hive sorgusu yürütür `sqlContext` . `-o`Parametresi geçirilirse, sorgunun sonucu, `%%local` bir Spark veri çerçevesi olarak Scala bağlamında kalıcı hale getirilir.

Jupyter Not defterleri ve ile çağırdığınız önceden tanımlanmış "mıknatık" için çekirdekler hakkında daha fazla bilgi için `%%` `%%local` bkz. [HDInsight 'ta HDInsight Spark Linux kümeleri ile Jupyter Not defterleri için sunulan çekirdekler](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Kitaplıkları içeri aktarma
Aşağıdaki kodu kullanarak, gereken Spark, MLlib ve diğer kitaplıkları içeri aktarın.

```scala
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
```

## <a name="data-ingestion"></a>Veri alımı
Veri bilimi işlemindeki ilk adım, analiz etmek istediğiniz verileri almak için kullanılır. Verileri, veri araştırmasına ve modelleme ortamınıza bulunduğu dış kaynaklardan veya sistemlerden siz getirin. Bu makalede, aldığınız veriler, TAXI seyahat ve tarifeli havayolu dosyasının (. tsv dosyası olarak depolanır)% 0,1 Birleşik bir örneğidir. Veri araştırması ve modelleme ortamı spark. Bu bölüm aşağıdaki görev serisini tamamlayacak kodu içerir:

1. Veri ve model depolaması için dizin yolları ayarlayın.
2. Giriş veri kümesinde okuma (. tsv dosyası olarak depolanır).
3. Veriler için bir şema tanımlayın ve verileri temizleyin.
4. Temizlenen bir veri çerçevesi oluşturun ve bellekte önbelleğe koyun.
5. Verileri SQLContext 'e geçici bir tablo olarak kaydedin.
6. Tabloyu sorgulama ve sonuçları bir veri çerçevesine aktarma.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Azure Blob depolamada depolama konumları için dizin yolları ayarlama
Spark, Azure Blob depolama alanını okuyup yazabilir. Spark kullanarak mevcut verilerinizi işleyebilir ve sonra sonuçları blob depolamada yeniden saklayabilirsiniz.

Blob depolamada model veya dosya kaydetmek için yolu doğru bir şekilde belirtmeniz gerekir. İle başlayan bir yol kullanarak Spark kümesine eklenen varsayılan kapsayıcıya başvurun `wasb:///` . Kullanarak diğer konumlara başvurun `wasb://` .

Aşağıdaki kod örneği, okunacak giriş verilerinin konumunu ve modelin kaydedileceği Spark kümesine eklenen BLOB depolama alanının yolunu belirtir.

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Verileri içeri aktarın, bir RDD oluşturun ve şemaya göre bir veri çerçevesi tanımlayın

```scala
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
```

**Çıktıların**

Hücrenin çalıştırılacağı süre: 8 saniye.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Tabloyu sorgulama ve sonuçları bir veri çerçevesinde içeri aktarma
Daha sonra, tarifeli havayolu, Passenger ve tıp verileri için tabloyu sorgulayın; bozuk ve harici verileri filtreleme; ve birkaç satır yazdırın.

```scala
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
```

**Çıktıların**

| fare_amount | passenger_count | tip_amount | eğik |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16,0 |2.0 |3.4 |1.0 |
|        10,5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Veri araştırması ve görselleştirme
Verileri Spark 'a geçirdikten sonra, veri bilimi sürecinin bir sonraki adımı, keşif ve görselleştirme aracılığıyla verilerin daha derin bir şekilde anlaşılmasıdır. Bu bölümde, SQL sorgularını kullanarak TAXI verilerini inceleyeceksiniz. Ardından, otomatik görselleştirme jupi özelliğini kullanarak hedef değişkenleri ve görsel inceleme için olası özellikleri çizmek üzere sonuçları bir veri çerçevesine aktarın.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Verileri çizmek için yerel ve SQL Magic kullanın
Varsayılan olarak, bir Jupyter Not defterinden çalıştırdığınız kod parçacığının çıktısı, çalışan düğümlerinde kalıcı olan oturum bağlamında kullanılabilir. Her bir hesaplama için çalışan düğümlerine bir seyahat kaydetmek istiyorsanız ve hesaplayıcınız için ihtiyaç duyduğunuz tüm veriler jupi sunucu düğümünde (baş düğümüdür) yerel olarak kullanılabiliyorsa, `%%local` sihirli bir kod parçacığını jupi sunucusu üzerinde çalıştırmak için kullanabilirsiniz.

* **SQL Magic** ( `%%sql` ). HDInsight Spark Kernel, SQLContext 'e karşı kolay satır içi HiveQL sorgularını destekler. ( `-o VARIABLE_NAME` ) Bağımsız değişkeni, SQL sorgusunun çıkışını jupi sunucusunda bir Pandas veri çerçevesi olarak devam ettirir. Bu ayar, çıktının yerel modda kullanılabilir olacağı anlamına gelir.
* `%%local`**Magic**. `%%local`MAGIC, kodu HDInsight kümesinin baş düğümü olan Jupyıter sunucusunda yerel olarak çalıştırır. Genellikle, `%%local` parametresini parametresiyle birlikte sihirli ile birlikte kullanırsınız `%%sql` `-o` . `-o`Parametresi, SQL sorgusunun çıkışını yerel olarak kalıcı hale getirecağından, daha sonra `%%local` Magic, yerel olarak kalıcı olan SQL sorgularının çıktısına karşı yerel olarak çalışacak bir sonraki kod parçacığı kümesini tetikler.

### <a name="query-the-data-by-using-sql"></a>SQL kullanarak verileri sorgulama
Bu sorgu, tarifeli havayolu tutarına, yolcular sayısına ve tıp miktarına göre vergilenme dönüşlerini alır.

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

Aşağıdaki kodda, `%%local` Magic bir yerel veri çerçevesi oluşturur, sqlResults. Matplotlib kullanarak çizmek için sqlResults kullanabilirsiniz.

> [!TIP]
> Yerel sihirli Bu makalede birden çok kez kullanılır. Veri kümesi büyükse, lütfen yerel belleğe sığan bir veri çerçevesi oluşturmak için örnek ' i girin.
> 
> 

### <a name="plot-the-data"></a>Verileri çiz
Veri çerçevesinin bir Pandas veri çerçevesi olarak yerel bağlamdan sonra Python kodunu kullanarak çizim yapabilirsiniz.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 Spark çekirdeği, kodu çalıştırdıktan sonra SQL (HiveQL) sorgularının çıkışını otomatik olarak görselleştirir. Çeşitli görselleştirme türleri arasından seçim yapabilirsiniz:

* Tablo
* Pasta
* Çizgi
* Alan
* Çubuk

Verilerin çizme kodu aşağıda verilmiştir:

```scala
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
```

**Çıktıların**

![İpucu tutarı histogramı](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Yolcular sayısına göre tıp miktarı](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tarifeli havayolu tutara göre tıp tutarı](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Özellikler oluşturun ve özellikleri dönüştürün ve sonra verileri modelleme işlevlerine göre hazırlayın
Spark ML ve MLlib 'den ağaç tabanlı modelleme işlevleri için, çözümleme, dizin oluşturma, tek yönlü kodlama ve vektörleştirme gibi çeşitli teknikler kullanarak hedef ve özellikleri hazırlamanız gerekir. Bu bölümde izlenecek yordamlar aşağıda verilmiştir:

1. Saat demetlerini, trafik zaman demetlerine **ekleyerek yeni** bir özellik oluşturun.
2. Kategorik özelliklerine **Dizin oluşturma ve tek yönlü kodlama** uygulayın.
3. Veri kümesini eğitim ve test kesirleri olarak **örnekle ve böler** .
4. **Eğitim değişkeni ve özelliklerini belirtin**, ardından dizinlenmiş veya tek yönlü kodlanmış eğitim ve işaret eden dayanıklı Dağıtılmış veri kümeleri (rdds) veya veri çerçeveleri etiketli test girişi oluşturun.
5. Makine öğrenimi modelleriyle ilgili giriş olarak kullanılacak **özellikleri ve hedefleri otomatik olarak kategorilere ayırın ve vektörleştirme** .

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Saatleri trafik zaman demetlerine ekleyerek yeni bir özellik oluşturun
Bu kod, trafik zaman demetlerine saat ekleyerek yeni bir özellik oluşturmayı ve elde edilen veri çerçevesinin bellekte nasıl önbelleğe alınacağını gösterir. RDDs ve veri çerçevelerinin sürekli olarak kullanıldığı, önbelleğe alma işlemi, geliştirilmiş yürütme sürelerine yol açar. Buna uygun olarak, aşağıdaki yordamlarda, birkaç aşamada RDDs ve veri çerçevelerini önbelleğe alacaksınız.

```scala
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
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Kategorik özelliklerin dizinini oluşturma ve tek yönlü kodlama
MLlib 'in modelleme ve tahmin etme işlevleri, ' nin kullanılmadan önce dizinlenmesi veya kodlanması gereken kategorik giriş verilerini içeren özellikler gerektirir. Bu bölümde, modelleme işlevlerine giriş için kategorik özelliklerinin nasıl dizinlenmesi veya kodlanması gösterilmektedir.

Modele bağlı olarak modellerinizi farklı yollarla dizinleyebilir veya kodlamanız gerekir. Örneğin, lojistik ve doğrusal regresyon modelleri, tek yönlü bir kodlama gerektirir. Örneğin, üç kategoriye sahip bir özellik üç özellik sütununa genişletilebilir. Her sütun, bir gözlemin kategorisine bağlı olarak 0 veya 1 ' i içerir. MLlib, tek başına kodlama için [Onehotencoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) işlevini sağlar. Bu kodlayıcı, etiket dizinlerinin bir sütununu en çok tek bir değer ile ikili vektörler sütunuyla eşler. Bu kodlamayla, lojistik regresyon gibi sayısal değerli özellikler bekleyen algoritmalar kategorik özelliklere uygulanabilir.

Burada, karakter dizeleri olan örnekleri göstermek üzere yalnızca dört değişken dönüştürülürler. Ayrıca, sayısal değerlerle temsil edilen iş günü gibi diğer değişkenleri kategorik değişkenler olarak da dizinlenebilir.

Dizin oluşturma, kullanma `StringIndexer()` ve tek yönlü kodlama için, `OneHotEncoder()` mllib içindeki işlevleri kullanın. Kategorik özelliklerinin dizinme ve kodlama kodu aşağıda verilmiştir:

```scala
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
```

**Çıktıların**

Hücrenin çalıştırılacağı süre: 4 saniye.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Veri kümesini eğitim ve test kesirleri olarak örnekleme ve bölme
Bu kod, verilerin rastgele bir örneklemesini (Bu örnekte %25) oluşturur. Veri kümesinin boyutu nedeniyle bu örnek için örnekleme gerekli olmamasına rağmen, makalede gerektiğinde kendi sorunlarınız için nasıl kullanacağınızı bilmeniz için nasıl örnekleyebilirsiniz. Örnekler büyükse bu, modelleri eğitirken önemli ölçüde tasarruf edebilir. Daha sonra, sınıflandırma ve regresyon modellemesinde kullanmak üzere örneği bir eğitim bölümüne (Bu örnekte %75) ve bir test bölümüne (Bu örnekte %25) ayırın.

Eğitim sırasında çapraz doğrulama katlarını seçmek için kullanılabilecek, her satıra (0 ve 1 arasında) rastgele bir sayı (bir "S_SAYI_ÜRET" sütununda) ekleyin.

```scala
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
```

**Çıktıların**

Hücrenin çalıştırılacağı süre: 2 saniye.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Eğitim değişkenini ve özelliklerini belirtin ve ardından dizinlenmiş veya tek yönlü kodlanmış bir eğitim oluşturun ve giriş noktası veya veri çerçeveleri etiketli bir test girişi oluşturun
Bu bölüm, kategorik metin verilerinin etiketlenmiş bir nokta veri türü olarak nasıl dizinlendirilirsiniz ve bunu kullanarak MLlib Lojistik gerileme ve diğer sınıflandırma modellerini eğitmeniz ve test edebilirsiniz. Etiketli nokta nesneleri, MLlib içindeki makine öğrenimi algoritmalarından büyük bir şekilde, giriş verileri için gereken şekilde biçimlendirilen RDD 'dir. [Etiketli nokta](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) , bir etiket/Yanıtla ilişkili olan, yoğun veya seyrek olan yerel bir vektörüdür.

Bu kodda hedef (bağımlı) değişkeni ve modelleri eğiteiçin kullanılacak özellikleri belirtirsiniz. Daha sonra, Dizin oluşturulmuş veya tek yönlü kodlanmış bir eğitim ve işaret RDDs veya veri çerçeveleri etiketli test girişi oluşturursunuz.

```scala
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
```

**Çıktıların**

Hücrenin çalıştırılacağı süre: 4 saniye.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Makine öğrenimi modelleriyle ilgili giriş olarak kullanılacak özellikleri ve hedefleri otomatik olarak kategorilere ayırın ve vektörleştirme
Ağaç tabanlı modelleme işlevlerinde kullanılacak hedefi ve özellikleri sınıflandırmak için Spark ML 'yi kullanın. Kod iki görevi tamamlar:

* 0,5 eşik değerini kullanarak 0 ile 1 arasında her bir veri noktasına 0 veya 1 değeri atayarak sınıflandırma için bir ikili hedef oluşturur.
* Özellikleri otomatik olarak kategorilere ayırır. Herhangi bir özelliğin farklı sayısal değer sayısı 32 ' den küçükse, bu özellik kategorilere ayrılır.

Bu iki görevin kodu aşağıda verilmiştir.

```scala
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
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>İkili sınıflandırma modeli: bir ipucunun ödenip ödenmeyeceğini tahmin etme
Bu bölümde, tıp 'nin ödenip ödenmeyeceğini tahmin etmek için üç tür ikili sınıflandırma modeli oluşturursunuz:

* Spark ML işlevini kullanarak bir **lojistik regresyon modeli** `LogisticRegression()`
* Spark ML işlevini kullanarak **rastgele bir orman sınıflandırma modeli** `RandomForestClassifier()`
* MLlib işlevini kullanarak **gradyan arttırma ağacı sınıflandırma modeli** `GradientBoostedTrees()`

### <a name="create-a-logistic-regression-model"></a>Lojistik regresyon modeli oluşturma
Ardından, Spark ML işlevini kullanarak bir lojistik regresyon modeli oluşturun `LogisticRegression()` . Model oluşturma kodunu bir dizi adımda oluşturursunuz:

1. Model verilerini bir parametre kümesiyle **eğitme** .
2. Ölçümle bir test veri kümesindeki **modeli değerlendirin** .
3. Daha sonraki tüketim için modeli BLOB depolama alanına **kaydedin** .
4. Test verileriyle **modeli puan** edin.
5. **Sonuçları** alıcı işletim ÖZELLIĞI (ROC) eğrilerle çizdirme.

Bu yordamların kodu aşağıda verilmiştir:

```scala
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
```

Sonuçları yükleyin, puanı yapın ve kaydedin.

```scala
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
```

**Çıktıların**

Test verilerinde ROC = 0.9827381497557599

ROC eğrisini çizmek için yerel Pandas veri çerçeveleri üzerinde Python kullanın.

```scala
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
```

**Çıktıların**

![İpucu veya Tıp ROC eğrisi yok](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Rastgele bir orman sınıflandırma modeli oluşturma
Ardından, Spark ML işlevini kullanarak rastgele bir orman sınıflandırma modeli oluşturun `RandomForestClassifier()` ve ardından test verilerinde modeli değerlendirin.

```scala
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
```

**Çıktıların**

Test verilerinde ROC = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>GBT sınıflandırma modeli oluşturma
Ardından, MLlib 'in işlevini kullanarak bir GBT sınıflandırma modeli oluşturun `GradientBoostedTrees()` ve ardından test verilerinde modeli değerlendirin.

```scala
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
```

**Çıktıların**

ROC eğrisi altındaki alan: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regresyon modeli: tahmin ipucu tutarı
Bu bölümde, ipucu miktarını tahmin etmek için iki tür regresyon modeli oluşturursunuz:

* Spark ML işlevini kullanarak **regularized doğrusal regresyon modeli** `LinearRegression()` . Modeli kaydeder ve test verilerinde modeli değerlendirirsiniz.
* Spark ML işlevini kullanarak bir **gradyan arttırma ağacı regresyon modeli** `GBTRegressor()` .

### <a name="create-a-regularized-linear-regression-model"></a>Regularized doğrusal regresyon modeli oluşturma

```scala
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
```


**Çıktıların**

Hücrenin çalıştırılacağı süre: 13 saniye.

```scala
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
```

**Çıktıların**

Test verilerinde R-SQR = 0.5960320470835743

Sonra, test sonuçlarını bir veri çerçevesi olarak sorgulayın ve AutoVizWidget ve Matplotlib kullanarak görselleştirin.

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

Kod, sorgu çıktısından bir yerel veri çerçevesi oluşturur ve verilerin grafiğini çizer. `%%local`Magic, `sqlResults` Matplotlib ile çizmek için kullanabileceğiniz bir yerel veri çerçevesi oluşturur.

> [!NOTE]
> Bu Spark Magic, bu makalede birden çok kez kullanılır. Veri miktarı büyükse, yerel belleğe sığan bir veri çerçevesi oluşturmak için örnek yapmanız gerekir.
> 
> 

Python Matplotlib kullanarak çizimler oluşturun.

```scala
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
```

**Çıktıların**

![İpucu miktarı: gerçek ve tahmin karşılaştırması](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>GBT regresyon modeli oluşturma
Spark ML işlevini kullanarak bir GBT regresyon modeli oluşturun `GBTRegressor()` ve ardından test verilerinde modeli değerlendirin.

[Gradyan-artırılmış ağaçlar](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS), karar ağaçları Kümelemeler. GB bir kayıp işlevini en aza indirmek için GBTS hatlarınızın karar ağaçları. Gerileme ve sınıflandırma için GB 'LAR kullanabilirsiniz. Kategorik özellikleri işleyebilir, özellik ölçeklendirmesini gerektirmez ve ilişkisel olmayan ve özellik etkileşimlerini yakalayabilir. Bunları, birden çok Lass sınıflandırma ayarında da kullanabilirsiniz.

```scala
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
```

**Çıktıların**

Test R-SQR: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>İyileştirme için gelişmiş modelleme yardımcı programları
Bu bölümde, geliştiricilerin model iyileştirmesi için sık kullanılan makine öğrenimi yardımcı programlarını kullanırsınız. Özellikle, parametre sweve çapraz doğrulamayı kullanarak makine öğrenimi modellerini üç farklı şekilde iyileştirebilirsiniz:

* Verileri eğitme ve doğrulama kümelerine ayırın, bir eğitim kümesindeki hiper parametre kullanımını kullanarak modeli iyileştirin ve bir doğrulama kümesi üzerinde değerlendirin (doğrusal regresyon)
* Spark ML 'nin CrossValidator işlevini (ikili sınıflandırma) kullanarak çapraz doğrulama ve hiper parametre kullanımı kullanarak modeli iyileştirin
* Herhangi bir makine öğrenimi işlevini ve parametre kümesini (doğrusal regresyon) kullanmak için özel çapraz doğrulama ve parametre katlama kodu kullanarak modeli iyileştirin

**Çapraz doğrulama** , bilinen bir veri kümesi üzerinde eğitilen bir modelin, eğitilen veri kümelerinin özelliklerini tahmin etmek için genelleştirdiğini değerlendirir bir tekniktir. Bu tekniğin arkasındaki genel fikir, bir modelin bilinen verilerin veri kümesi üzerinde eğitilmesinin ve tahmin edilebillerinin doğruluğu bağımsız bir veri kümesine karşı test edilmektir. Yaygın bir uygulama, bir veri kümesini *k* katlara bölmek ve sonra da katların biri olan her türlü bir yandan modeli hepsini bir kez deneme halinde eğmektir.

**Hyper-parametre iyileştirmesi** , genellikle bir öğrenme algoritması için bir dizi Hyper-parametresi seçerken, bir bağımsız veri kümesindeki algoritmanın performansının bir ölçüsünü en iyi duruma getirme amacını içeren bir sorundur. Hyper-parametresi, model eğitimi yordamının dışında belirtmeniz gereken bir değerdir. Hyper-parametre değerleri hakkındaki varsayımlar, modelin esnekliğini ve doğruluğunu etkileyebilir. Karar ağaçlarında, örneğin, ağaçta istenen derinlik ve sayıda yaprakları gibi Hyper-parametreleri vardır. Bir destek vektör makinesi (SVM) için bir yanlış sınıflandırma ceza terimi ayarlamanız gerekir.

Hyper-parametre iyileştirmesinin gerçekleştirilemesinin yaygın bir yolu da, **parametre tarama** adı verilen bir kılavuz araması kullanmaktır. Bir kılavuz aramasında, bir öğrenme algoritması için belirtilen Hyper-parametre alanının bir alt kümesinin değerleriyle kapsamlı bir arama yapılır. Çapraz doğrulama, kılavuz arama algoritması tarafından üretilen en iyi sonuçları sıralamak için bir performans ölçümü sağlayabilir. Çapraz doğrulama hiper parametre kullanımını kullanıyorsanız, bir modeli eğitim verilerine fazla ekleme gibi sorunları sınırlamaya yardımcı olabilirsiniz. Bu şekilde model, eğitim verilerinin ayıklandığı genel veri kümesine uygulanacak kapasiteyi korur.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Bir doğrusal regresyon modelini hiper parametre ile iyileştirme
Ardından, verileri eğitme ve doğrulama kümelerine ayırın, modeli iyileştirmek için bir eğitim kümesindeki hiper parametre kullanımını kullanın ve bir doğrulama kümesi üzerinde (doğrusal regresyon) değerlendirin.

```scala
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
```

**Çıktıların**

Test R-SQR: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Çapraz doğrulama ve hiper parametre kullanımını kullanarak ikili sınıflandırma modelini iyileştirin
Bu bölümde, çapraz doğrulama ve hiper parametre kullanımını kullanarak bir ikili sınıflandırma modelinin nasıl iyileştirileceği gösterilmektedir. Bu, Spark ML `CrossValidator` işlevini kullanır.

```scala
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
```

**Çıktıların**

Hücrenin çalıştırılacağı süre: 33 saniye.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Özel çapraz doğrulama ve parametre katlama kodu kullanarak doğrusal regresyon modelini iyileştirin
Ardından, özel kod kullanarak modeli iyileştirin ve en yüksek doğruluk ölçütünü kullanarak en iyi model parametrelerini belirleyebilirsiniz. Ardından, son modeli oluşturun, test verilerinde modeli değerlendirin ve modeli BLOB depolama alanına kaydedin. Son olarak, modeli yükleyin, test verilerini puanlandırın ve doğruluğu değerlendirin.

```scala
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
```

**Çıktıların**

Hücrenin çalıştırılacağı süre: 61 saniye.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Spark ile oluşturulmuş makine öğrenimi modellerini Scala ile otomatik olarak kullanma
Azure 'daki veri bilimi sürecini oluşturan görevlerde size kılavuzluk eden konulara genel bakış için bkz. [Team Data Science Process](./index.yml).

[Ekip veri bilimi işlem talimatları](walkthroughs.md) , belirli senaryolar Için ekip veri bilimi işlemindeki adımları gösteren diğer uçtan uca izlenecek yolları açıklar. İzlenecek yollar Ayrıca akıllı bir uygulama oluşturmak için bulut ve şirket içi araçların ve hizmetlerin bir iş akışında veya işlem hattına nasıl birleştirileceğini gösterir.

[Spark tarafından oluşturulan makine öğrenimi modelleri](spark-model-consumption.md) , Spark ve Azure Blob depolama alanına kaydedilmiş makine öğrenimi modelleriyle yeni veri kümelerini otomatik olarak yüklemek ve Puanlama yapmak Için Scala kodunu nasıl kullanacağınızı gösterir. Burada belirtilen yönergeleri izleyebilir ve otomatik tüketim için Python kodunu bu makaledeki Scala kodu ile değiştirmeniz yeterlidir.
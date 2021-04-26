---
title: Operationlıme Spark-derlenmiş makine öğrenimi modelleri-Team Data Science Işlemi
description: Python ile Azure Blob depolama (elde eden) içinde depolanan öğrenme modellerini yükleme ve Puanlama.
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
ms.openlocfilehash: 4a8d46b5ff10fdaa62f7747aac654e0b1ca78c42
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259158"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Spark tarafından oluşturulan Machine Learning modellerini çalıştırma

Bu konuda, HDInsight Spark kümelerinde Python kullanılarak kaydedilmiş bir makine öğrenimi modelinin (ML) nasıl yapılacağı gösterilmektedir. Spark MLlib kullanılarak derlenen ve Azure Blob depolama (elde edilen) ' de depolanan makine öğrenimi modellerinin nasıl yükleneceğini ve ayrıca, It b ' de depolanan veri kümeleriyle nasıl puan alınacağını açıklar. Giriş verilerinin nasıl önceden işlenmesi, MLlib araç setinde dizin oluşturma ve kodlama işlevleri kullanılarak özelliklerin nasıl değiştirileceği ve ML modelleriyle Puanlama için girdi olarak kullanılabilecek etiketli bir nokta veri nesnesinin nasıl oluşturulacağı gösterilmektedir. Puanlama için kullanılan modeller, doğrusal regresyon, lojistik regresyon, rastgele orman modelleri ve gradyan yükseltme ağacı modellerini içerir.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark kümeleri ve Jupyıter Not defterleri
Bir ML modelini işlemek için kurulum adımları ve kodu, bir HDInsight Spark 1,6 kümesinin yanı sıra bir Spark 2,0 kümesi kullanmaya yönelik bu kılavuzda sunulmaktadır. Bu yordamların kodu, Jupyıter not defterlerinde da sağlanır.

### <a name="notebook-for-spark-16"></a>Spark 1,6 için Not defteri
[Pyspark-Machine-Learning-Data-Science-Spark-model-tüketim. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter Not defteri, HDInsight kümelerinde Python kullanarak kaydedilmiş bir modeli nasıl kullanıma sunmayı göstermektedir. 

### <a name="notebook-for-spark-20"></a>Spark 2,0 için Not defteri
Spark 1,6 için Jupyter Not defterini HDInsight Spark 2,0 kümesiyle birlikte kullanmak üzere değiştirmek için Python kod dosyasını [Bu dosyayla](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py)değiştirin. Bu kod Spark 2,0 ' de oluşturulan modellerin nasıl kullanıldığını gösterir.


## <a name="prerequisites"></a>Önkoşullar

1. Bu izlenecek yolu tamamlamak için bir Azure hesabı ve Spark 1,6 (veya Spark 2,0) HDInsight kümesine ihtiyacınız vardır. Bu gereksinimleri nasıl karşılacağınız hakkında yönergeler için bkz. [Azure HDInsight 'Ta Spark kullanarak veri bilimine genel bakış](spark-overview.md) . Bu konuda Ayrıca, burada kullanılan NYC 2013 TAXI verilerinin açıklaması ve Spark kümesindeki bir Jupyter Not defterinden kod yürütme yönergeleri de yer alır. 
2. Spark 1,6 kümesi veya Spark 2,0 Not defterleri için veri araştırması [ve Spark ile modelleme](spark-data-exploration-modeling.md) konularında çalışarak, burada puanlanması gereken makine öğrenimi modellerini oluşturun. 
3. Spark 2,0 Not defterleri, 2011 ve 2012 ' den iyi bilinen Airline 'ın veri kümesi olan sınıflandırma görevi için ek bir veri kümesi kullanır. Not defterlerinin ve bunlara bağlantıların açıklaması, bunları içeren GitHub deposu için [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) içinde verilmiştir. Üstelik, burada ve bağlantılı Not defterlerindeki kod geneldir ve tüm Spark kümeleri üzerinde çalışmalıdır. HDInsight Spark kullanmıyorsanız, küme kurulumu ve yönetim adımları burada gösterilenden biraz farklı olabilir. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Kurulum: depolama konumları, kitaplıklar ve önceden ayarlanmış Spark bağlamı
Spark bir Azure Depolama Blobu okuyup yazabilir (ile). Bu nedenle, var olan tüm verileriniz Spark kullanılarak işlenebilir ve sonuçlar elde edilebilir.

Model veya dosyaları te. 1. & lt; 1} içinde kaydetmek için yolun doğru şekilde belirtilmesi gerekir. Spark kümesine eklenen varsayılan kapsayıcıya, *"sunb///"* ile başlayan bir yol kullanılarak başvurulabilir. Aşağıdaki kod örneği, okunacak verilerin konumunu ve model çıktısının kaydedildiği model depolama dizini için yolu belirtir. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>ILB 'de depolama konumları için dizin yolları ayarlama
Modeller şu şekilde kaydedilir: "wasb:///user/remoteuser/NYCTaxi/Models". Bu yol düzgün ayarlanmamışsa, modeller Puanlama için yüklenmez.

Puanlanmış sonuçlar şu şekilde kaydedildi: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Klasörün yolu yanlışsa, sonuçlar bu klasöre kaydedilmez.   

> [!NOTE]
> Dosya yolu konumları, **Machine-Learning-Data-Science-Spark-Data-araştırması-modelleme. ipynb** Not defterinin son hücresinin çıktısından Bu koddaki yer tutuculara kopyalanabilir ve yapıştırılabilir.   
> 
> 

Dizin yollarını ayarlamaya yönelik kod aşağıda verilmiştir: 

```python
# LOCATION OF DATA TO BE SCORED (TEST DATA)
taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

# SET SCORDED RESULT DIRECTORY PATH
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

# FILE LOCATIONS FOR THE MODELS TO BE SCORED
logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

# RECORD START TIME
import datetime
datetime.datetime.now()
```

**ÇıKTıLARıN**

DateTime. DateTime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Kitaplıkları içeri aktarma
Spark bağlamını ayarla ve gerekli kitaplıkları aşağıdaki kodla içeri aktar

```python
#IMPORT LIBRARIES
import pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import SQLContext
import matplotlib
import matplotlib.pyplot as plt
from pyspark.sql import Row
from pyspark.sql.functions import UserDefinedFunction
from pyspark.sql.types import *
import atexit
from numpy import array
import numpy as np
import datetime
```

### <a name="preset-spark-context-and-pyspark-magics"></a>Önceden ayarlanmış Spark bağlamı ve PySpark mıknatıcs
Jupyter Not defterleri ile birlikte sunulan pyspark çekirdekler 'in önceden ayarlanmış bir bağlamı vardır. Bu nedenle, geliştirmekte olduğunuz uygulamayla çalışmaya başlamadan önce Spark veya Hive bağlamlarını açıkça ayarlamanız gerekmez. Bu bağlamlar varsayılan olarak kullanılabilir:

* SC-Spark için 
* Hive için sqlContext

PySpark çekirdeği,%% ile çağırabilmeniz için özel komutlar olan önceden tanımlanmış bazı "mıknatıcs" sağlar. Bu kod örneklerinde kullanılan iki komut vardır.

* **%% Yerel** Sonraki satırlardaki kodun yerel olarak yürütüldüğü belirtildi. Kod geçerli bir Python kodu olmalıdır.
* **%% SQL-o \<variable name>** 
* SqlContext 'e karşı bir Hive sorgusu yürütür. -O parametresi geçirilirse, sorgunun sonucu%% yerel Python bağlamında Pandas dataframe olarak kalıcı hale getirilir.

Jupyter Not defterleri ve sağladıkları önceden tanımlanmış "mıknatık" hakkında daha fazla bilgi için bkz. [HDInsight 'ta HDInsight Spark Linux kümeleri içeren Jupyter Not defterleri için sunulan çekirdekler](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Verileri alma ve temizlenen bir veri çerçevesi oluşturma
Bu bölüm, puanlanalınacak verileri almak için gereken bir dizi görevle ilgili kodu içerir. Taxı seyahat ve tarifeli havayolu dosyasının (. tsv dosyası olarak depolanır) birleştirilmiş% 0,1 bir örneğinde okuyun, verileri biçimlendirin ve sonra temiz bir veri çerçevesi oluşturur.

Taxı seyahat ve tarifeli havayolu dosyaları, şu yordamda belirtilen yordama göre birleştirildi: bkz. [Team Data Science Process in, HDInsight Hadoop kümelerini kullanma](hive-walkthrough.md) konusu.

```python
# INGEST DATA AND CREATE A CLEANED DATA FRAME

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_test_file = sc.textFile(taxi_test_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_test_file.first()
fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
fields[7].dataType = IntegerType() #Pickup hour
fields[8].dataType = IntegerType() # Pickup week
fields[9].dataType = IntegerType() # Weekday
fields[10].dataType = IntegerType() # Passenger count
fields[11].dataType = FloatType() # Trip time in secs
fields[12].dataType = FloatType() # Trip distance
fields[19].dataType = FloatType() # Fare amount
fields[20].dataType = FloatType() # Surcharge
fields[21].dataType = FloatType() # Mta_tax
fields[22].dataType = FloatType() # Tip amount
fields[23].dataType = FloatType() # Tolls amount
fields[24].dataType = FloatType() # Total amount
fields[25].dataType = IntegerType() # Tipped or not
fields[26].dataType = IntegerType() # Tip class
taxi_schema = StructType(fields)

# CREATE DATA FRAME
taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_cleaned.cache()
taxi_df_test_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_test_cleaned.registerTempTable("taxi_test")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 46,37 saniye

## <a name="prepare-data-for-scoring-in-spark"></a>Spark 'da Puanlama için verileri hazırlama
Bu bölümde, sınıflandırma ve gerileme için MLlib denetimli öğrenme algoritmalarında kullanılmak üzere kategorik özelliklerinin nasıl dizinlenmesi, kodlanması ve ölçeklenmesi gösterilmektedir.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Özellik dönüşümü: Puanlama için, giriş modellerine giriş için kategorik özellikleri dizin ve kodla
Bu bölümde `StringIndexer` , modellerdeki verilerle bir ve kodlama özellikleri kullanılarak kategorik verilerin nasıl dizininin oluşturulduğu gösterilmektedir `OneHotEncoder` .

[Stringındexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) , etiketlerin dize sütununu etiket dizinlerinin bir sütununa kodluyor. Dizinler, etiket sıklıklara göre sıralanır. 

[Onehotencoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) , etiket dizinlerinin bir sütununu en çok tek bir değer ile ikili vektörler sütunuyla eşler. Bu kodlama, lojistik regresyon gibi sürekli değerli özelliklerin kategorik özelliklere uygulanmasını bekleyen algoritmalara izin verir.

```python
#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_test 
"""
taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_with_newFeatures.cache()
taxi_df_test_with_newFeatures.count()

# INDEX AND ONE-HOT ENCODING
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_test_with_newFeatures)
encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
model = stringIndexer.fit(encoded1)
indexed = model.transform(encoded1)
encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
model = stringIndexer.fit(encoded2)
indexed = model.transform(encoded2)
encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
encoded3 = encoder.transform(indexed)

# INDEX AND ENCODE TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 5,37 saniye

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Modellerdeki giriş için özellik dizileri ile RDD nesneleri oluşturma
Bu bölüm, bir RDD nesnesi olarak kategorik metin verilerinin nasıl dizininin oluşturulacağını ve tek yönlü bir kodlama olduğunu ve bu sayede MLlib Lojistik gerileme ve ağaç tabanlı modelleri eğitmek ve test etmek için kullanılabilecek kodu içerir. Dizinli veriler dayanıklı [Dağıtılmış veri kümesi (RDD)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) nesnelerinde depolanır. RDDs Spark 'ta temel soyutlamadır. RDD nesnesi, Spark ile paralel olarak üzerinde işletilebilir, sabit, bölümlenmiş bir öğe koleksiyonunu temsil eder.

Ayrıca, `StandardScalar` çok çeşitli makine öğrenimi modellerini eğitmek için popüler bir algoritma olan Stochastic gradyanı (SGD) ile doğrusal regresyonla kullanılmak üzere MLlib tarafından sağlanarak verilerin nasıl ölçeklenmesi gerektiğini gösteren kodu içerir. [Standartscaler](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.tree.RandomForest.html#pyspark.mllib.tree.RandomForest
) , özellikleri birim varyansı ölçeklendirmek için kullanılır. Veri normalleştirmesi olarak da bilinen özellik ölçeklendirme, yöntem, yaygın olarak kullanılan değerlere sahip özellikler, amaç işlevinde aşırı ağırlık olarak verilmez. 

```python
# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

# CACHE RDDS IN MEMORY
indexedTESTbinary.cache();
oneHotTESTbinary.cache();
indexedTESTreg.cache();
oneHotTESTreg.cache();
oneHotTESTregScaled.cache();

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 11,72 saniye

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Lojistik regresyon modeliyle puan edin ve çıktıyı blob 'a kaydedin
Bu bölümdeki kod, Azure Blob depolama alanına kaydedilmiş bir lojistik regresyon modelinin nasıl yükleneceğini gösterir ve bir tıp 'nin bir vergilenme yolculuğuna mi ödendiğini tahmin etmek, standart sınıflandırma ölçümleriyle elde etmek ve sonuçları blob depolamaya eklemek ve bunlara göre çizmek için onu kullanır. Puanlanmış sonuçlar RDD nesnelerinde depolanır. 

```python
# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

## LOAD SAVED MODEL
savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

## SAVE SCORED RESULTS (RDD) TO BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
dirfilename = scoredResultDir + logisticregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 19,22 saniye

## <a name="score-a-linear-regression-model"></a>Doğrusal regresyon modeli puanı
En iyi duruma getirme işlemi, ücretli ipucu miktarını tahmin etmek için Stochastic gradyan (SGD) kullanarak bir doğrusal regresyon modeli eğitmek için, bir doğrusal regresyon [modelini kullandık](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html
) . 

Bu bölümdeki kod, Azure Blob depolama alanından bir doğrusal regresyon modelinin nasıl yükleneceğini, ölçeklendirilen değişkenleri kullanarak nasıl puan alınacağını ve sonra sonuçları blob 'a geri kaydetmenizi gösterir.

```python
#SCORE LINEAR REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

#LOAD LIBRARIES
from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

# LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = scoredResultDir + linearregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 16,63 saniye

## <a name="score-classification-and-regression-random-forest-models"></a>Sınıflandırmayı ve gerileme rastgele orman modellerini puan edin
Bu bölümdeki kod, Azure Blob depolama alanına kaydedilen kaydedilmiş sınıflandırma ve regresyon rastgele orman modellerinin nasıl yükleneceğini, standart sınıflandırıcı ve gerileme ölçüleriyle performanslarını nasıl gösterdiğini gösterir ve ardından sonuçları blob depolamaya geri kaydeder.

[Rastgele ormanlar](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) karar ağaçlarının Kümelemeler.  Çok sayıda karar ağacının, fazla ekleme riskini azaltmak için bunları birleştirirler. Rastgele ormanlar kategorik özellikleri işleyebilir, birden çok Lass sınıflandırma ayarına genişletebilir, özellik Ölçeklendirmesi gerektirmez ve bu özellik etkileşimini yakalayabilir. Rastgele ormanlar, sınıflandırma ve gerileme için en başarılı makine öğrenimi modellerinden biridir.

[spark. mllib](https://spark.apache.org/mllib/) ikili ve birden çok sınıf sınıflandırması için rastgele ormanları destekler ve hem sürekli hem de kategorik özellikler kullanılarak gerileme için. 

```python
# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES    
from pyspark.mllib.tree import RandomForest, RandomForestModel


# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 31,07 saniye

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Sınıflandırma ve gerileme gradyan arttırma ağaç modellerini Puanlama
Bu bölümdeki kod, Azure Blob depolama alanından sınıflandırma ve regresyon gradyanı yükseltme ağacı modellerinin nasıl yükleneceğini, standart sınıflandırıcı ve gerileme ölçüleriyle performanslarını nasıl gösterdiğini gösterir ve ardından sonuçları blob depolamaya geri kaydeder. 

**spark. mllib** , hem sürekli hem de kategorik özellikler kullanarak ikili sınıflandırma ve gerileme için GBTS 'yi destekler. 

[Gradyan arttırma ağaçları](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS), karar ağaçları Kümelemeler. GBTS, bir kayıp işlevini en aza indirmek için karar ağaçlarını tekrarlayarak eğitme. GBTS, kategorik özellikleri işleyebilir, özellik ölçeklendirmesini gerektirmez ve özyinelemesiz ve özellik etkileşimlerini yakalayabilir. Bu algoritma, birden çok Lass sınıflandırma ayarında da kullanılabilir.

```python
# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

#LOAD AND SCORE THE MODEL
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + btclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

# LOAD AND SCORE MODEL 
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + btregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 14,6 saniye

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Nesneleri bellekten Temizleme ve puanlanmış dosya konumlarını yazdırma

```python
# UNPERSIST OBJECTS CACHED IN MEMORY
taxi_df_test_cleaned.unpersist()
indexedTESTbinary.unpersist();
oneHotTESTbinary.unpersist();
indexedTESTreg.unpersist();
oneHotTESTreg.unpersist();
oneHotTESTregScaled.unpersist();


# PRINT OUT PATH TO SCORED OUTPUT FILES
print "logisticRegFileLoc: " + logisticregressionfilename;
print "linearRegFileLoc: " + linearregressionfilename;
print "randomForestClassificationFileLoc: " + rfclassificationfilename;
print "randomForestRegFileLoc: " + rfregressionfilename;
print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
print "BoostedTreeRegressionFileLoc: " + btregressionfilename;
```

**ÇıKTıLARıN**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

Doğrregfileloc: LinearRegressionWithSGD_2016-05 -0317 _22_ 58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

Rasgeleforestregfileloc: RandomForestRegression_2016-05-0317_23_31.459140.txt

Boostedtreeclassıficationfileloc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Spark modellerini bir Web arabirimi aracılığıyla kullanma
Spark, toplu işleri veya etkileşimli sorguları, Livy adlı bir bileşen ile REST arabirimi aracılığıyla uzaktan göndermeye yönelik bir mekanizma sağlar. Livy, HDInsight Spark kümenizde varsayılan olarak etkindir. Livy hakkında daha fazla bilgi için bkz.: [Livy kullanarak Spark işlerini uzaktan gönderme](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Bir Azure blobuna depolanan bir dosyayı toplu olarak belirten bir işi uzaktan göndermek için Livy kullanabilir ve sonra sonuçları başka bir bloba yazar. Bunu yapmak için Python betiğini şuradan karşıya yüklersiniz  
Spark kümesinin blobuna [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) . Betiği küme blobuna kopyalamak için **Microsoft Azure Depolama Gezgini** veya **AzCopy** gibi bir araç kullanabilirsiniz. Bizim örneğimizde, betiği **_wasb:///example/Python/ConsumeGBNYCReg.py_** dosyasına yükledik.   

> [!NOTE]
> İhtiyaç duyduğunuz erişim anahtarları Spark kümesiyle ilişkili depolama hesabı için portalda bulunabilir. 
> 
> 

Bu konuma yüklendikten sonra, bu betik Spark kümesi içinde dağıtılmış bir bağlamda çalışır. Modeli yükler ve modele göre giriş dosyalarında tahminleri çalıştırır.  

Livy üzerinde basit bir HTTPS/REST isteği yaparak bu betiği uzaktan çağırabilirsiniz.  Python betiğini uzaktan çağırmak için HTTP isteği oluşturmak üzere bir kıvrımlı komut aşağıda verilmiştir. CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME değerlerini Spark kümeniz için uygun değerlerle değiştirin.

```console
# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches
```

Temel kimlik doğrulaması ile basit bir HTTPS çağrısı yaparak, Spark işini Livy aracılığıyla çağırmak için uzak sistemdeki herhangi bir dili kullanabilirsiniz.   

> [!NOTE]
> Bu HTTP çağrısını yaparken Python Istekleri kitaplığını kullanmak kullanışlı olacaktır, ancak şu anda Azure Işlevlerinde varsayılan olarak yüklenmez. Bunun yerine eski HTTP kitaplıkları kullanılır.   
> 
> 

HTTP çağrısının Python kodu aşağıda verilmiştir:

```python
#MAKE AN HTTPS CALL ON LIVY. 

import os

# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
import httplib, urllib, base64

# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
host = '<spark cluster name>.azurehdinsight.net:443'
username='<username>'
password='<password>'

#AUTHORIZATION
conn = httplib.HTTPSConnection(host)
auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
response = conn.getresponse().read()
print(response)
conn.close()
```

Blob 'u Zamanlayıcı, oluşturma veya güncelleştirme gibi çeşitli olaylara dayalı olarak bir blob 'a puan veren bir Spark iş gönderimi tetikleyebilmeniz için bu python kodunu [Azure işlevlerine](https://azure.microsoft.com/documentation/services/functions/) de ekleyebilirsiniz. 

Kod ücretsiz istemci deneyimini tercih ediyorsanız, **Logic Apps tasarımcısında** bir http eylemi tanımlayarak ve parametrelerini ayarlayarak Spark Batch Puanlama çağırmak için [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) kullanın. 

* Azure Portal, **+ Yeni**  ->  **Web ve mobil**  ->  **Logic App**' i seçerek yeni bir mantıksal uygulama oluşturun. 
* **Logic Apps tasarımcısını** getirmek Için mantıksal uygulamanın adını ve App Service planı girin.
* Bir HTTP eylemi seçin ve aşağıdaki şekilde gösterilen parametreleri girin:

![Logic Apps Tasarımcısı](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Sırada ne var?
**Çapraz doğrulama ve hiper parametre** kullanımı: modellerin çapraz doğrulama ve hiper parametre Tatkileri kullanılarak eğitilme hakkında [Spark ile gelişmiş veri araştırması ve modelleme](spark-advanced-data-exploration-modeling.md) konusuna bakın.


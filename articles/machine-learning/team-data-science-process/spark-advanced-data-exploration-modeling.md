---
title: Spark-Team Data Science süreci ile gelişmiş veri araştırması ve modelleme
description: Çapraz doğrulama ve hiper parametre iyileştirmesi kullanarak, veri araştırması ve ikili sınıflandırma ve regresyon modellerini eğitme için HDInsight Spark kullanın.
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
ms.openlocfilehash: 37f0b48190bcbad7ab190da1d690a1a0485501a1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259328"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Spark ile gelişmiş veri keşfi ve modelleme

Bu izlenecek yol, NYC TAXI seyahat ve tarifeli havayolu 2013 veri kümesinin bir örneği üzerinde çapraz doğrulama ve hiper parametre iyileştirmesi kullanarak, veri araştırması yapmak ve ikili sınıflandırma ve regresyon modellerini eğitmek için HDInsight Spark kullanır. Veri [bilimi işlemi](./index.yml), Işleme ve Azure Blob 'ları için verileri ve modelleri depolamak üzere bir HDInsight Spark kümesi kullanarak uçtan uca adım adım yol gösterir. İşlem, bir Azure Depolama Blobu getirilen verileri araştırır ve görselleştirir ve sonra tahmine dayalı modeller oluşturmak için verileri hazırlar. Python, çözümü kodlayın ve ilgili çizimleri göstermek için kullanılır. Bu modeller, ikili sınıflandırma ve regresyon modelleme görevleri yapmak için Spark MLlib araç seti kullanılarak oluşturulur. 

* **İkili sınıflandırma** görevi, seyahat için bir tıp ödenip ödenmediğini tahmin etmeye yöneliktir. 
* **Gerileme** görevi, diğer ipucu özelliklerine göre ipucu miktarını tahmin etmek için kullanılır. 

Modelleme adımları Ayrıca her bir model türünün nasıl eğileceğini, değerlendirileceğini ve kaydedileceğini gösteren kodu içerir. Bu konu, Spark konusuyla veri araştırmasına [ve modelleme ile](spark-data-exploration-modeling.md) aynı yerden bir kısmını ele alır. Ancak, en iyi şekilde doğru sınıflandırma ve regresyon modellerini eğitmek için hiper parametre tathasyon ile çapraz doğrulamayı de kullandığından daha fazla "Gelişmiş". 

**Çapraz doğrulama (CV)** , bilinen bir veri kümesi üzerinde eğitilen bir modelin, eğitilen veri kümelerinin özelliklerini tahmin etmek için nasıl iyi olduğunu bilmenin bir tekniğidir.  Burada kullanılan yaygın bir uygulama, bir veri kümesini K katlara bölmek ve ardından modeli katların hepsi hariç tüm ve bir kez daha uyumlu olarak eğitmektir. Modelin, modeli eğitme için kullanılmayan bağımsız veri kümesine karşı test edildiğinde doğru tahmin etme yeteneği.

**Hiper parametre iyileştirmesi** , genellikle bir öğrenme algoritması için hiper parametre kümesi seçme sorununa, genellikle bir bağımsız veri kümesindeki algoritmanın performansının bir ölçüsünü en iyi duruma getirme amacını içeren bir sorundur. **Hiper parametreler** , model eğitimi yordamının dışında belirtilmesi gereken değerlerdir. Bu değerler hakkındaki varsayımlar, modellerin esnekliğini ve doğruluğunu etkileyebilir. Karar ağaçları, örneğin, ağaçta istenen derinlik ve sayıda yaprakları gibi hiper parametrelere sahiptir. Destek vektör makineleri (SVMs), yanlış sınıflandırma ceza dönemi ayarlamayı gerektirir. 

Burada kullanılan hiper parametre iyileştirmesini gerçekleştirmenin yaygın bir yolu, bir ızgara araması veya bir **parametre tarama** yöntemidir. Bu arama, bir öğrenme algoritması için hiper parametre alanının bir alt kümesinden geçer. Çapraz doğrulama, kılavuz arama algoritması tarafından üretilen en iyi sonuçları sıralamak için bir performans ölçümü sağlayabilir. Hiperparametre sweile kullanılan CV, modelin eğitim verilerinin ayıklandığı genel veri kümesine uygulanmasını sağlamak için bir modelin verileri eğitimine kadar olan sorunları sınırlamaya yardımcı olur.

Kullandığımız modeller şunlardır: lojistik ve doğrusal regresyon, rastgele ormanlar ve gradyan tarafından artırılmış ağaçlar:

* [SGD Ile doğrusal regresyon](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html#pyspark.mllib.regression.LinearRegressionWithSGD
) , bir Stochastik gradyan (SGD) yöntemi kullanan ve en iyi duruma getirme ve özellik ölçekleme için, ücretli ipucu tutarlarını tahmin eden bir doğrusal regresyon modelidir. 
* [LBFGS](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.classification.LogisticRegressionWithLBFGS.html
) veya "logıt" regresyon ile lojistik regresyon, bağımlı değişken veri sınıflandırması yapmak için kategorik olduğunda kullanılabilecek bir gerileme modelidir. LBFGS, sınırlı miktarda bilgisayar belleği kullanarak ve makine öğreniminde yaygın olarak kullanılan, Brohden – Fletma – Goldfarb – shanno (BFGS) algoritmasını yaklaştırın, bir dörde ve çok büyük bir en iyi duruma getirme algoritmasıdır.
* [Rastgele ormanlar](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) karar ağaçlarının Kümelemeler.  Çok sayıda karar ağacının, fazla ekleme riskini azaltmak için bunları birleştirirler. Rastgele ormanlar gerileme ve sınıflandırma için kullanılır ve kategorik özellikleri işleyebilir ve çok Lass sınıflandırma ayarına genişletilebilir. Bunlar, özellik Ölçeklendirmesi gerektirmez ve doğrmalsuz ve özellik etkileşimlerini yakalayabilir. Rastgele ormanlar, sınıflandırma ve gerileme için en başarılı makine öğrenimi modellerinden biridir.
* [Degrade artırılmış ağaçlar](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS), karar ağaçları Kümelemeler. GBTS, bir kayıp işlevini en aza indirmek için karar ağaçlarını tekrarlayarak eğitme. GBTS, gerileme ve sınıflandırma için kullanılır ve kategorik özellikleri işleyebilir, özellik ölçeklendirmesini gerektirmez ve bu, özyinelemesiz ve özellik etkileşimlerini yakalayabilir. Ayrıca, birden çok Lass sınıflandırma ayarında de kullanılabilirler.

İkili sınıflandırma sorunu için CV ve hiper parametre süpürme kullanan modelleme örnekleri gösterilir. Daha basit örnekler (parametre sweeps olmadan), regresyon görevleri için ana konu başlığında sunulur. Bununla birlikte, ek olarak, doğrusal regresyon ve CV için esnek ağ kullanılarak doğrulama, rastgele orman gerileme için kullanılarak parametre süpürme için de sunulur. **Elastik net** , L1 ve L2 ölçümlerini [kement](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) ve [Ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization) yöntemlerinin yaptırımlarıyla birleştiren doğrusal regresyon modellerini sığdırmanın bir regularized regresyon yöntemidir.   

<!-- -->

> [!NOTE]
> Spark MLlib araç seti büyük veri kümelerinde çalışmak üzere tasarlanmış olsa da, görece küçük bir örnek (170K satırları kullanılarak yaklaşık 30 MB, özgün NYC veri kümesinin% 0,1 ' i) burada kolaylık sağlaması için kullanılmıştır. Burada verilen alıştırma 2 çalışan düğümü olan bir HDInsight kümesinde verimli bir şekilde (yaklaşık 10 dakika) çalışır. Küçük değişikliklerle aynı kod, bellekteki verileri önbelleğe almak ve küme boyutunu değiştirmek için uygun değişikliklerle birlikte daha büyük veri kümelerini işlemek üzere kullanılabilir.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Kurulum: Spark kümeleri ve Not defterleri
Bu izlenecek yolda, HDInsight Spark 1,6 kullanımı için kurulum adımları ve kodu sunulmaktadır. Ancak, hem HDInsight Spark 1,6 hem de Spark 2,0 kümelerinde Jupyıter Not defterleri sağlanır. Not defterlerinin ve bunlara bağlantıların açıklaması, bunları içeren GitHub deposu için [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) içinde verilmiştir. Üstelik, burada ve bağlantılı Not defterlerindeki kod geneldir ve tüm Spark kümeleri üzerinde çalışmalıdır. HDInsight Spark kullanmıyorsanız, küme kurulumu ve yönetim adımları burada gösterilenden biraz farklı olabilir. Kolaylık sağlaması için, Jupyter Notebook sunucusunun pyspark çekirdeğinde çalıştırılmak üzere Spark 1,6 ve 2,0 için Jupyter Not defterlerinin bağlantıları aşağıda verilmiştir:

### <a name="spark-16-notebooks"></a>Spark 1,6 Not defterleri

[Pyspark-Machine-Learning-Data-Science-Spark-gelişmiş-veri-araştırma-modelleme. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): not defteri #1 konuları ve hiper parametre ayarlama ve çapraz doğrulama kullanarak model geliştirmeyi içerir.

### <a name="spark-20-notebooks"></a>Spark 2,0 Not defterleri

[Spark 2.0-pySpark3-Machine-Learning-Data-Science-Spark-Advanced-Data-araştırmayı-Modellendirme. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Bu dosya Spark 2,0 kümelerinde veri keşif, modelleme ve Puanlama gerçekleştirme hakkında bilgi sağlar.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Kurulum: depolama konumları, kitaplıklar ve önceden ayarlanmış Spark bağlamı
Spark, Azure Depolama Blobu okuyabilir ve yazabilir (Ayrıca, .% t olarak da bilinir). Bu nedenle, var olan tüm verileriniz Spark kullanılarak işlenebilir ve sonuçlar elde edilebilir.

Model veya dosyaları te. 1. & lt; 1} içinde kaydetmek için yolun doğru şekilde belirtilmesi gerekir. Spark kümesine eklenen varsayılan kapsayıcıya, ile başlayan bir yol kullanılarak başvurulabilir: "wasb:///". Diğer konumlara "wasb://" tarafından başvurulur.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>ILB 'de depolama konumları için dizin yolları ayarlama
Aşağıdaki kod örneği, okunacak verilerin konumunu ve model çıktısının kaydedildiği model depolama dizininin yolunu belirtir:

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";


# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

# PRINT START TIME
import datetime
datetime.datetime.now()
```

**ÇıKTıLARıN**

DateTime. DateTime (2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Kitaplıkları içeri aktarma
Gerekli kitaplıkları aşağıdaki kodla içeri aktarın:

```python
# LOAD PYSPARK LIBRARIES
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
Jupyter Not defterleri ile birlikte sunulan pyspark çekirdekler 'in önceden ayarlanmış bir bağlamı vardır. Bu nedenle, geliştirmekte olduğunuz uygulamayla çalışmaya başlamadan önce Spark veya Hive bağlamlarını açıkça ayarlamanız gerekmez. Bu bağlamlar varsayılan olarak sizin için kullanılabilir. Bu bağlamlar şunlardır:

* SC-Spark için 
* Hive için sqlContext

PySpark çekirdeği,%% ile çağırabilmeniz için özel komutlar olan önceden tanımlanmış bazı "mıknatıcs" sağlar. Bu kod örneklerinde kullanılan iki komut vardır.

* **%% Yerel** Sonraki satırlardaki kodun yerel olarak yürütüleceğini belirtir. Kod geçerli bir Python kodu olmalıdır.
* **%% SQL-o \<variable name>** SqlContext 'e karşı bir Hive sorgusu yürütür. -O parametresi geçirilirse, sorgunun sonucu%% yerel Python bağlamında Pandas DataFrame olarak kalıcı hale getirilir.

Jupyter Not defterleri ve sağladıkları önceden tanımlanmış "mıknatık" hakkında daha fazla bilgi için bkz. [HDInsight 'ta HDInsight Spark Linux kümeleri içeren Jupyter Not defterleri için sunulan çekirdekler](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Ortak bloba veri alımı:
Veri bilimi sürecinin ilk adımı, veri keşif ve modelleme ortamınızda bulunduğu kaynaklardan analiz edilecek verileri almak için kullanılır. Bu ortam, bu kılavuzda spark. Bu bölüm, bir dizi görevi tamamlamaya yönelik kodu içerir:

* modellenen veri örneğini alma
* giriş veri kümesinde okuma (. tsv dosyası olarak depolanır)
* verileri biçimlendirme ve Temizleme
* bellekte (RDDs veya veri çerçeveleri) nesneler oluşturma ve önbelleğe alma
* SQL-Context ' te geçici tablo olarak kaydedin.

Veri alma kodu aşağıda verilmiştir.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
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

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

# CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 276,62 saniye

## <a name="data-exploration--visualization"></a>Görselleştirme & veri araştırması
Veriler Spark 'a alındıktan sonra, veri bilimi sürecinin bir sonraki adımı, keşif ve görselleştirme aracılığıyla verilerin daha derin bir şekilde anlaşılmasıdır. Bu bölümde, SQL sorgularını kullanarak TAXI verilerini inceleyeceğiz ve görsel inceleme için hedef değişkenleri ve olası özellikleri çiziyoruz. Özellikle, taxg seyahatlerinde yolcular sayısı, tıp tutarının sıklığı ve ipuçlarının ödeme tutarına ve tipine göre nasıl değişeceğini de çiztik.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Taksi dönüşlerin örneğinde yolcular sayısı sıklıklarının histogramını çiz
Bu kod ve sonraki parçacıklar, verileri çizmek için örneği ve yerel Magic 'i sorgulamak için SQL Magic kullanır.

* **SQL Magic ( `%%sql` )** HDInsight Pyspark çekirdeği, SqlContext 'e karşı kolay satır Içi hiveql sorgularını destekler. (-O VARIABLE_NAME) bağımsız değişkeni, SQL sorgusunun çıkışını jupi sunucusunda bir Pandas DataFrame olarak devam ettirir. Bu, yerel modda kullanılabildiği anlamına gelir.
* **`%%local` MAGIC** , HDInsight kümesinin baş düğümüne olan jupyıter sunucusunda yerel olarak kod çalıştırmak için kullanılır. Genellikle, `%%local` `%%sql -o` bir sorgu çalıştırmak için Magic kullanıldıktan sonra sihirli ' i kullanırsınız. -O parametresi, SQL sorgusunun çıkışını yerel olarak kalıcı hale getirebilecek. Sonra `%%local` Magic, yerel olarak kalıcı olan SQL sorgularının çıktısına karşı yerel olarak çalıştırılacak bir sonraki kod parçacığı kümesini tetikler. Kodu çalıştırdıktan sonra çıkış otomatik olarak görselleştirilebilir.

Bu sorgu, döngüleri pasur sayısına göre alır. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# SQL QUERY
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count
```

Bu kod, sorgu çıktısından yerel bir veri çerçevesi oluşturur ve verilerin grafiğini çizer. `%%local`Magic, `sqlResults` Matplotlib ile çizim için kullanılabilecek bir yerel veri çerçevesi oluşturur. 

<!-- -->

> [!NOTE]
> Bu PySpark Magic, bu kılavuzda birden çok kez kullanılır. Veri miktarı büyükse, yerel belleğe uyatabilecek bir veri çerçevesi oluşturmak için örnekleyebilirsiniz.

<!-- -->

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Bu, döngüleri pasger sayılarına göre çizdirmek için kod

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT PASSENGER NUMBER VS TRIP COUNTS
x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**ÇıKTıLARıN**

![Yolcular sayısına göre gidiş frekansı](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Not defterindeki **tür** menü düğmelerini kullanarak birkaç farklı görselleştirme türü (tablo, pasta, satır, alan veya çubuk) arasından seçim yapabilirsiniz. Çubuk çizimi burada gösterilmiştir.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tıp tutarlarının histogramını ve tıp tutarının yolcular sayısı ve tarifeli havayolu tutarlarına göre nasıl değiştiğini gösteren bir histogram çizirsiniz.
Verileri örneklemek için bir SQL sorgusu kullanın..

```sql
# SQL SQUERY
%%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7
    AND fare_amount > 0 
    AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 
    AND tip_amount < 25
```

Bu kod hücresi, verileri üç adet çizim halinde oluşturmak için SQL sorgusunu kullanır.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline

# TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount ($) by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
ax.set_title('Tip amount by Fare amount ($)')
ax.set_xlabel('Fare Amount')
ax.set_ylabel('Tip Amount')
plt.axis([-2, 120, -2, 30])
plt.show()
```

**ÇıKTıLARıN** 

![Tıp miktarı dağılımı](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Yolcular sayısına göre tıp miktarı](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tarifeli havayolu tutara göre tıp tutarı](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Modelleme için özellik Mühendisliği, dönüştürme ve veri hazırlığı
Bu bölümde, ML modellemesinde kullanılmak üzere verileri hazırlamak için kullanılan yordamların kodu açıklanmıştır. Aşağıdaki görevlerin nasıl yapılacağını gösterir:

* Saatleri trafik zaman gözlerine bölümleyerek yeni bir özellik oluşturun
* Dizin ve sık erişimli kodlama kategorik özellikleri
* ML işlevlerine giriş için etiketli nokta nesneleri oluşturma
* Verilerin rastgele bir alt örneklemesi oluşturun ve bunu eğitim ve test kümelerine ayırın
* Özellik ölçeklendirme
* Bellekteki nesneleri önbelleğe al

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Trafiği zaman gözlerine bölümlere ayırarak yeni bir özellik oluşturun
Bu kod, trafik sürelerini depo gözlerine bölümleyerek yeni bir özelliğin nasıl oluşturulduğunu ve sonra elde edilen veri çerçevesinin bellekte nasıl önbelleğe alınacağını gösterir. Önbelleğe alma, dayanıklı Dağıtılmış veri kümeleri (RDDs) ve veri çerçevelerinin tekrar tekrar kullanıldığı, geliştirilmiş yürütme zamanına yol açar. Bu nedenle, Bu izlenecek yolda bulunan birkaç aşamada RDDs ve veri çerçevelerini önbelleğe aldık.

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
```

```python
# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**ÇıKTıLARıN**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Dizin ve tek sıcak kodlama kategorik özellikleri
Bu bölümde, modelleme işlevlerine giriş için kategorik özelliklerinin nasıl indekslenmesi veya kodlanması gösterilmektedir. MLlib 'in modelleme ve tahmin etme işlevleri, kategorik giriş verileri olan özelliklerin kullanılmadan önce dizinlenmesi veya kodlanması gerekir. 

Modele bağlı olarak, bunları farklı yollarla dizinleyebilir veya kodlamanız gerekir. Örneğin, lojistik ve doğrusal regresyon modelleri, örneğin üç kategoriye sahip bir özellik, gözlemleyen kategoriye bağlı olarak her biri 0 veya 1 içeren üç özellik sütununa genişletilebilen tek yönlü bir kodlama gerektirir. MLlib, tek yönlü kodlama yapmak için [Onehotencoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) işlevi sağlar. Bu kodlayıcı, etiket dizinlerinin bir sütununu, en çok tek bir değer ile ikili vektör sütunuyla eşler. Bu kodlama, lojistik regresyon gibi sayısal değerli özelliklerin kategorik özelliklere uygulanmasını bekleyen algoritmalara izin verir.

Kategorik özelliklerinin dizinme ve kodlama kodu aşağıda verilmiştir:

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
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

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 3,14 saniye

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>ML işlevlerine giriş için etiketli nokta nesneleri oluşturma
Bu bölüm, kategorik metin verilerinin etiketlenmiş bir nokta veri türü olarak nasıl dizin oluşturulacağını ve nasıl kodlanacağını gösteren kodu içerir. Bu dönüşüm, MLlib Lojistik gerileme ve diğer sınıflandırma modellerini eğitmek ve test etmek için kullanılacak metin verilerini hazırlar. Etiketli nokta nesneleri, MLlib 'teki ML algoritmalarından büyük bir kısmının giriş verileri için gereken şekilde biçimlendirilen dayanıklı Dağıtılmış veri kümeleridir (RDD). [Etiketli nokta](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) , bir etiket/Yanıtla ilişkili olan, yoğun veya seyrek olan yerel bir vektörüdür.

İkili sınıflandırma için metin özelliklerini dizinlemek ve kodlamak için kod aşağıda verilmiştir.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

Doğrusal regresyon analizi için kategorik metin özelliklerini kodlamak ve dizinlemek için kod aşağıda verilmiştir.

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Verilerin rastgele bir alt örneklemesi oluşturun ve bunu eğitim ve test kümelerine ayırın
Bu kod, verilerin rastgele bir örneklemesini oluşturur (burada %25 kullanılır). Veri kümesinin boyutu nedeniyle bu örnek için gerekli olmamasına rağmen, verileri burada nasıl örneklebileceğinizi gösteririz. Daha sonra gerekirse kendi sorununuz için nasıl kullanacağınızı öğrenirsiniz. Örnekler büyükse, örnekleme eğitim modelleriyle önemli ölçüde tasarruf edebilir. Daha sonra örneği, sınıflandırma ve regresyon modellemesinde kullanılacak bir eğitim bölümüne (burada %75) ve bir test bölümüne (burada %25) böleceğiz.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
from pyspark.sql.functions import rand

samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# CACHE TRAIN AND TEST DATA
trainData.cache()
testData.cache()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 0,31 saniye

### <a name="feature-scaling"></a>Özellik ölçeklendirme
Veri normalleştirmesi olarak da bilinen özellik ölçeklendirme, yöntem, yaygın olarak kullanılan değerlere sahip özellikler, amaç işlevinde aşırı ağırlık olarak verilmez. Özellik ölçeklendirme kodu, özellikleri birim varyansı ölçeklendirmek için [Standardscaler](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html
) kullanır. Bu, Stochastic gradyan (SGD) ile doğrusal regresyonda kullanılmak üzere MLlib tarafından sağlanır. SGD, regularized gerilemeler veya destek vektör makineleri (SVM) gibi çok çeşitli makine öğrenimi modellerini eğitmek için popüler bir algoritmadır.   

> [!TIP]
> Özellik ölçeklendirilmesine duyarlı olmak için, bir Doğritsionwithsgd algoritmasını bulduk.   
> 
> 

Regularized doğrusal SGD algoritmasıyla kullanılacak değişkenleri ölçeklendirmek için kod aşağıda verilmiştir.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 11,67 saniye

### <a name="cache-objects-in-memory"></a>Bellekteki nesneleri önbelleğe al
ML algoritmalarının eğitimine ve test edilmesine yönelik süre, sınıflandırma, gerileme ve ölçeklendirilen özellikler için kullanılan giriş veri çerçevesi nesneleri önbelleğe alınarak azaltılabilir.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN** 

Hücrenin yürütülmesi için geçen süre: 0,13 saniye

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Bir tıp 'nin ikili sınıflandırma modelleriyle ödenip ödenmediğini tahmin etme
Bu bölümde, bir vergilenme için bir tıp ödenip ödenmediğini tahmin eden ikili sınıflandırma görevi için üç modelin nasıl kullanılacağı gösterilmektedir. Sunulan modeller şunlardır:

* Lojistik regresyon 
* Rastgele orman
* Gradyan arttırma ağaçları

Her model derleme kodu bölümü, adımlara ayrılır: 

1. Tek bir parametre kümesiyle **eğitim verilerini modelleyin**
2. Ölçümlerle bir test veri kümesindeki **model değerlendirmesi**
3. Gelecekteki tüketim için modeli blob 'a **kaydetme**

İki şekilde parametre sweile çapraz doğrulamanın (CV) nasıl yapılacağını göstereceğiz:

1. MLlib 'teki herhangi bir algoritmaya ve bir algoritmadaki herhangi bir parametre kümesine uygulanabilen **genel** özel kod kullanma. 
2. **Pyspark CrossValidator işlem hattı işlevini** kullanma. Çapraz Doğrulayıcı Spark 1.5.0 için bazı sınırlamalara sahiptir: 
   
   * Ardışık düzen modelleri gelecekteki tüketim için kaydedilemez veya kalıcı hale getirilir.
   * Bir modeldeki her parametre için kullanılamaz.
   * Her MLlib algoritması için kullanılamaz.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>İkili sınıflandırma için lojistik regresyon algoritmasıyla genel çapraz doğrulama ve hiper parametre birlikte kullanılması
Bu bölümdeki kod, NYC taksi seyahat ve tarifeli havayolu veri kümesindeki bir yolculuğa yönelik bir tıp ödenip ödenmediğini tahmin eden [lbfgs](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) ile bir lojistik regresyon modelinin nasıl eğeceği, değerlendirilmesi ve kaydedileceği gösterilmektedir. Model, MLlib 'teki öğrenme algoritmalarından herhangi birine uygulanabilen, çapraz doğrulama (CV) ve hiper parametre swekullanılarak eğitilir.   

<!-- -->

> [!NOTE]
> Bu özel CV kodunun yürütülmesi birkaç dakika sürebilir.

<!-- -->

**CV ve hiper parametre kullanımını kullanarak lojistik regresyon modelini eğitme**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from pyspark.mllib.evaluation import BinaryClassificationMetrics

# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
from sklearn.grid_search import ParameterGrid
grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
paramGrid = list(ParameterGrid(grid))
numModels = len(paramGrid)

# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
nFolds = 3;
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

# BEGIN CV WITH PARAMETER SWEEP
for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create LabeledPoints from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowOneHotBinary)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        regt = paramGrid[j]['regType']
        regp = paramGrid[j]['regParam']
        iters = paramGrid[j]['iterations']
        tol = paramGrid[j]['tolerance']
        # Train logistic regression model with hypermarameter set
        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                  regParam=regp, tolerance = tol, intercept=True)
        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
        # Use ROC-AUC as accuracy metrics
        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
        metric = validMetrics.areaUnderROC
        metricSum[j] += metric

avgAcc = metricSum / nFolds;
bestParam = paramGrid[np.argmax(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                              iterations=bestParam['iterations'], 
                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                              intercept=True)


# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitBest.weights))
print("Intercept: " + str(logitBest.intercept))

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Katsayılar: [0.0082065285375,-0.0223675576104,-0.0183812028036,-3.48124578069 e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1,00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Kesme noktası:-0.0111216486893

Hücrenin yürütülmesi için geçen süre: 14,43 saniye

**Standart ölçümler ile ikili sınıflandırma modelini değerlendirin**

Bu bölümdeki kod, ROC eğrisinin bir çizimi dahil olmak üzere bir test veri kümesine karşı bir lojistik regresyon modelinin nasıl değerlendirileceğini gösterir.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT LIBRARIES
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

PR = 0.985336538462 altındaki alan

ROC = 0.983383274312 altındaki alan

Özet Istatistikleri

Precision = 0.984174341679

Hatırlayın = 0.984174341679

F1 puanı = 0.984174341679

Hücrenin yürütülmesi için geçen süre: 2,67 saniye

**ROC eğrisini çiz.**

*PredictionAndLabelsDF* , önceki hücrede *tmp_results* tablo olarak kaydedilir. *tmp_results* , çizim Için SQLResults veri çerçevesinde sorgu ve çıkış sonuçları yapmak için kullanılabilir. Kod şöyledir.

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Tahmine dayalı hale getirmek ve ROC eğrisini çizmek için kod aşağıda verilmiştir.

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

#PREDICTIONS
predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVES
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

**ÇıKTıLARıN**

![Genel yaklaşım için lojistik regresyon ROC eğrisi](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Gelecekteki tüketim için modeli bir bloba kalıcı yap**

Bu bölümdeki kod, tüketim için lojistik regresyon modelinin nasıl kaydedileceğini gösterir.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

# PERSIST MODEL
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;

logitBest.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 34,57 saniye

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Lojistik regresyon (elastik regresyon) modeliyle MLlib 'in CrossValidator işlem hattı işlevini kullanma
Bu bölümdeki kod, NYC taksi seyahat ve tarifeli havayolu veri kümesindeki bir yolculuğa yönelik bir tıp ödenip ödenmediğini tahmin eden [lbfgs](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) ile bir lojistik regresyon modelinin nasıl eğeceği, değerlendirilmesi ve kaydedileceği gösterilmektedir. Model, parametre süpürme ile CV için MLlib CrossValidator işlem hattı işleviyle birlikte, çapraz doğrulama (CV) ve hiper parametre swekullanılarak eğitilir.   

<!-- -->

> [!NOTE]
> Bu MLlib CV kodunun yürütülmesi birkaç dakika sürebilir.

<!-- -->

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.classification import LogisticRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
from sklearn.metrics import roc_curve,auc

# DEFINE ALGORITHM / MODEL
lr = LogisticRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build()

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=BinaryClassificationEvaluator(),
                    numFolds=3)

# CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


## PREDICT AND EVALUATE ON TEST DATA-SET

# USE TEST DATASET FOR PREDICTION
testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
test_predictions = cv_model.transform(testDataFrame)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
``` 

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 107,98 saniye

**ROC eğrisini çiz.**

*PredictionAndLabelsDF* , önceki hücrede *tmp_results* tablo olarak kaydedilir. *tmp_results* , çizim Için SQLResults veri çerçevesinde sorgu ve çıkış sonuçları yapmak için kullanılabilir. Kod şöyledir.

```python
# QUERY RESULTS
%%sql -q -o sqlResults
SELECT label, prediction, probability from tmp_results
```

ROC eğrisinin çizme kodu aşağıda verilmiştir.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
%%local
from sklearn.metrics import roc_curve,auc

# ROC CURVE
prob = [x["values"][1] for x in sqlResults["probability"]]
fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

#PLOT
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

**ÇıKTıLARıN**

![MLlib 'in CrossValidator kullanan lojistik regresyon ROC eğrisi](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Rastgele orman sınıflandırması
Bu bölümdeki kod, NYC taksi seyahat ve tarifeli havayolu veri kümesindeki bir yolculuğa yönelik bir tıp ödenip ödenmediğini tahmin eden bir rastgele orman gerilemesini nasıl eğiteleceğini, değerlendirmenizi ve kaydetmenizi gösterir.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

ROC = 0.985336538462 altındaki alan

Hücrenin yürütülmesi için geçen süre: 26,72 saniye

### <a name="gradient-boosting-trees-classification"></a>Gradyan arttırma ağaçları sınıflandırması
Bu bölümdeki kod, bir tıp 'nin NYC taksi seyahat ve tarifeli havayolu veri kümesinde bir seyahat için ödenip ödenmediğini tahmin eden bir gradyan artırma ağacı modelinin nasıl eğeceği, değerlendirilmesi ve kaydedileceği gösterilmektedir.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                numIterations=10)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# Area under ROC curve
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

ROC = 0.985336538462 altındaki alan

Hücrenin yürütülmesi için geçen süre: 28,13 saniye

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Regresyon modelleriyle tıp tutarını tahmin etme (CV kullanma)
Bu bölümde, regresyon görevi için üç modelin nasıl kullanılacağı gösterilmektedir: diğer tıp özelliklerine göre bir TAXI yolculuğuna ödenen ipucu miktarını tahmin edin. Sunulan modeller şunlardır:

* Regularized doğrusal regresyon
* Rastgele orman
* Gradyan arttırma ağaçları

Bu modeller giriş bölümünde açıklanmıştı. Her model derleme kodu bölümü, adımlara ayrılır: 

1. Tek bir parametre kümesiyle **eğitim verilerini modelleyin**
2. Ölçümlerle bir test veri kümesindeki **model değerlendirmesi**
3. Gelecekteki tüketim için modeli blob 'a **kaydetme**   

<!-- -->

> [!NOTE] 
> Çapraz doğrulama, bu bölümdeki üç regresyon modeliyle birlikte kullanılmaz, çünkü bu, lojistik regresyon modelleriyle ilgili ayrıntılı olarak gösterilmiştir. Bu konunun ekinde, CV 'nin doğrusal regresyon için elastik net ile nasıl kullanılacağını gösteren bir örnek verilmiştir.

<!-- -->

<!-- -->

> [!NOTE] 
> Deneyimimizde, bir, geçerli bir model elde etmek için, bu, bir diğer deyişle, doğru bir model almak için parametrelerin değiştirilmesi/iyileştirilmesi gerekir. Değişkenlerin ölçeklendirilmesi, yakınsama konusunda önemli ölçüde yardımcı olur. Bu konuya ek olarak gösterilen elastik net gerileme, Mergerileme Sionwithsgd yerine de kullanılabilir.

<!-- -->

### <a name="linear-regression-with-sgd"></a>SGD ile doğrusal regresyon
Bu bölümdeki kod, en iyi duruma getirme için Stokastik gradyan (SGD) kullanan doğrusal bir gerileme ve Azure Blob depolama (vstob) ' de modeli Puanlama, değerlendirme ve kaydetme işlemlerinin nasıl yapıldığını gösterir.

> [!TIP]
> Deneyimimizde, bir, geçerli bir model elde etmek için, bir,,, bir,,, bir, doğru şekilde değiştirilmelidir/en iyi duruma getirilmesi gerekir. Değişkenlerin ölçeklendirilmesi, yakınsama konusunda önemli ölçüde yardımcı olur.
> 
> 

```python
# LINEAR REGRESSION WITH SGD 

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Katsayılar: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Kesme noktası: 0.854507624459

RMO = 1.23485131376

R-SQR = 0.597963951127

Hücrenin yürütülmesi için geçen süre: 38,62 saniye

### <a name="random-forest-regression"></a>Rastgele orman gerileme
Bu bölümdeki kod, NYC TAXI seyahat verileri için ipucu miktarını tahmin eden bir rastgele orman modelini eğitme, değerlendirme ve kaydetme işlemlerinin nasıl yapılacağını gösterir.   

<!-- -->

> [!NOTE]
> Özel kod kullanarak parametre tathasyon ile çapraz doğrulama ek olarak sağlanır.

<!-- -->

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
# UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

RMO = 0.931981967875

R-SQR = 0.733445485802

Hücrenin yürütülmesi için geçen süre: 25,98 saniye

### <a name="gradient-boosting-trees-regression"></a>Gradyan arttırma ağaçları gerileme
Bu bölümdeki kod, NYC TAXI seyahat verileri için ipucu miktarını tahmin eden bir gradyan artırma ağaçları modelinin nasıl eğiteleceğini, değerlendirileceğini ve kaydedileceğini gösterir.

**Eğitme ve değerlendirme**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

# EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
test_predictions= sqlContext.createDataFrame(predictionAndLabels)
test_predictions_pddf = test_predictions.toPandas()

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

RMO = 0.928172197114

R-SQR = 0.732680354389

Hücrenin yürütülmesi için geçen süre: 20,9 saniye

**ZF**

*tmp_results* , önceki hücrede Hive tablosu olarak kaydedilir. Tablodaki sonuçlar, çizim için *SQLResults* veri çerçevesinin çıktılarından oluşur. Kod şöyledir

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Jupyıter sunucusunu kullanarak verileri çizme kodu aşağıda verilmiştir.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import numpy as np

# PLOT
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 15])
plt.show(ax)
```

![Gerçek-ve tahmini-ipucu-tutarlar](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Ek: parametre süpürmeleri ile çapraz doğrulamayı kullanan ek gerileme görevleri
Bu ek, doğrusal regresyon için elastik net kullanarak CV 'nin nasıl yapılacağını ve rastgele orman gerileme için özel kod kullanarak CV 'nin nasıl yapılacağını gösteren bir kod içerir.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Doğrusal regresyon için elastik net kullanarak çapraz doğrulama
Bu bölümdeki kod, doğrusal regresyon için elastik net kullanarak çapraz doğrulamanın nasıl yapılacağını ve test verilerine karşı modelin nasıl değerlendirileceğini gösterir.

```python
###  CV USING ELASTIC NET FOR LINEAR REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.regression import LinearRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import RegressionEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

# DEFINE ALGORITHM/MODEL
lr = LinearRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build() 

# DEFINE PIPELINE 
# SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
pipeline = Pipeline(stages=[lr])

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=RegressionEvaluator(),
                    numFolds=3)

# CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


# EVALUATE MODEL ON TEST SET
testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

# MAKE PREDICTIONS ON TEST DOCUMENTS
# cvModel uses the best model found (lrModel).
predictionAndLabels = cv_model.transform(testDataFrame)

# CONVERT TO DF AND SAVE REGISTER DF AS TABLE
predictionAndLabels.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

Hücrenin yürütülmesi için geçen süre: 161,21 saniye

**R-SQR ölçümü ile değerlendir**

*tmp_results* , önceki hücrede Hive tablosu olarak kaydedilir. Tablodaki sonuçlar, çizim için *SQLResults* veri çerçevesinin çıktılarından oluşur. Kod şöyledir

```python
# SELECT RESULTS
%%sql -q -o sqlResults
SELECT label,prediction from tmp_results
```

R-SQR 'yi hesaplamak için kod aşağıda verilmiştir.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
from scipy import stats

#R-SQR TEST METRIC
corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
r2 = (corstats[2]*corstats[2])
print("R-sqr = %s" % r2)
```

**ÇıKTıLARıN**

R-SQR = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Rastgele orman gerileme için özel kod kullanarak parametre süpürme ile çapraz doğrulama
Bu bölümdeki kod, rastgele orman gerileme için özel kod kullanarak parametre süpürme ile çapraz doğrulamanın nasıl yapılacağını ve test verilerine karşı modelin nasıl değerlendirileceğini gösterir.

```python
# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics
from sklearn.grid_search import ParameterGrid

## CREATE PARAMETER GRID
grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
paramGrid = list(ParameterGrid(grid))

## SPECIFY LEVELS OF CATEGORICAL VARIBLES
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
nFolds = 3;
numModels = len(paramGrid)
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create labeled points from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowIndexingRegression)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        maxD = paramGrid[j]['maxDepth']
        numT = paramGrid[j]['numTrees']
        # Train logistic regression model with hypermarameter set
        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=numT, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=maxD, maxBins=32)
        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
        # Use ROC-AUC as accuracy metrics
        validMetrics = RegressionMetrics(predictionAndLabels)
        metric = validMetrics.rootMeanSquaredError
        metricSum[j] += metric

avgAcc = metricSum/nFolds;
bestParam = paramGrid[np.argmin(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

## TRAIN FINAL MODL WIHT BEST PARAMETERS
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

# EVALUATE MODEL ON TEST DATA
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

#PRINT TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ÇıKTıLARıN**

RMO = 0.906972198262

R-SQR = 0.740751197012

Hücrenin yürütülmesi için geçen süre: 69,17 saniye

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Nesneleri bellekten Temizleme ve yazdırma modeli konumları
`unpersist()`Bellekte önbelleğe alınan nesneleri silmek için kullanın.

```python
# UNPERSIST OBJECTS CACHED IN MEMORY

# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()
trainData.unpersist()
trainData.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

**ÇıKTıLARıN**

PythonRDD. Scala 'da RDD 'de PythonRDD [122.368]: 43

* * Tüketim not defterinde kullanılacak model dosyalarının çıkış yolu. * * Bağımsız bir veri kümesini tüketmek ve skor etmek için, bu dosya adlarını "tüketim Not defteri" nde kopyalamanız ve yapıştırmanız gerekir.

```python
# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**ÇıKTıLARıN**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0316 _47_ 30.096528"

Doğrregfileloc = modelDir + "LinearRegressionWithSGD_2016-05 -0316 _51_ 28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0316 _50_ 17.454440"

Rasgeleforestregfileloc = modelDir + "RandomForestRegression_2016-05 -0316 _51_ 57.331730"

Boostedtreeclassıficationfileloc = modelDir + "GradientBoostingTreeClassification_2016-05 -0316 _50_ 40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0316 _52_ 18.827237"

## <a name="whats-next"></a>Sırada ne var?
Spark MlLib ile gerileme ve sınıflandırma modelleri oluşturduğunuza göre, bu modellerin nasıl puan alabileceğinizi ve değerlendirileceğini öğrenmek için hazırsınız demektir.

**Model tüketimi:** Bu konuda oluşturulan sınıflandırma ve regresyon modellerini Puanlama ve değerlendirme hakkında bilgi edinmek için bkz. [Spark tarafından oluşturulan Machine Learning modellerini Puanlama ve değerlendirme](spark-model-consumption.md).

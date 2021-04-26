---
title: 'Öğretici: Apache Spark MLlib ile makine öğrenimi uygulaması derleme'
description: Lojistik gerileme aracılığıyla sınıflandırmayı kullanarak bir veri kümesini analiz eden bir makine öğrenimi uygulaması oluşturmak için MLlib Apache Spark kullanma hakkında bir öğretici.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 5caa41b852bf55a11489db6c0bab871b20720e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670669"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Öğretici: Apache Spark MLlib ve Azure SYNAPSE Analytics ile Machine Learning uygulaması derleme

Bu makalede, Azure açık veri kümesinde basit bir tahmine dayalı analiz yapan bir makine öğrenimi uygulaması oluşturmak için Apache Spark [Mllib](https://spark.apache.org/mllib/) kullanmayı öğreneceksiniz. Spark, yerleşik makine öğrenimi kitaplıklarını sağlar. Bu örnek Lojistik gerileme aracılığıyla *Sınıflandırmayı* kullanır.

Mini bilgi ve MLlib, için uygun olan yardımcı programlar dahil olmak üzere makine öğrenimi görevleri için yararlı olan çok sayıda yardımcı program sağlayan temel Spark kitaplıklarıdır:

- Sınıflandırma
- Regresyon
- Kümeleme
- Konu modelleme
- Tekil değer ayrıştırma (SVD) ve asıl bileşen analizi (PCA)
- Örnek istatistiklerini test etme ve hesaplama

## <a name="understand-classification-and-logistic-regression"></a>Sınıflandırmayı ve lojistik regresyonunu anlayın

Popüler bir makine öğrenimi görevi *sınıflandırması*, giriş verilerini kategorilere sıralama işlemidir. Bu, sağladığınız giriş verilerine nasıl etiket atanacağını anlamak için bir sınıflandırma algoritmasının *işleridir* . Örneğin, stok bilgilerini girdi olarak kabul eden bir makine öğrenimi algoritmasını düşünebilirsiniz ve stoku iki kategoriye ayırabilirsiniz: satış yapmanız gereken stoklar ve tutmanız gereken hisse senetleri.

*Lojistik regresyon* , sınıflandırma için kullanabileceğiniz bir algoritmadır. Spark 'un lojistik regresyon API 'SI, *ikili sınıflandırmada* veya giriş verilerinin iki gruptan birinde sınıflandırılmasına yardımcı olur. Lojistik regresyon hakkında daha fazla bilgi için bkz. [Vikipedi](https://en.wikipedia.org/wiki/Logistic_regression).

Özet olarak, lojistik regresyon süreci, bir giriş vektörünün bir grupta ya da diğeri ait olma olasılığını tahmin etmek için kullanabileceğiniz bir *lojistik işlevi* üretir.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>NYC TAXI verilerinde tahmine dayalı analiz örneği

Bu örnekte, New York 'dan alınan TAXI-Seyahat ipucu verilerinde bazı tahmine dayalı analiz gerçekleştirmek için Spark 'ı kullanırsınız. Veriler [Azure açık veri kümeleri](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)aracılığıyla kullanılabilir. Veri kümesinin bu alt kümesi, her seyahat hakkında bilgiler, başlangıç ve bitiş saati ve konumları, maliyet ve diğer ilgi çekici öznitelikler dahil olmak üzere sarı TAXI seyahatleriyle ilgili bilgiler içerir.

> [!IMPORTANT]
> Bu verilerin depolama konumundan çekilerek ilgili ek ücretler de olabilir.

Aşağıdaki adımlarda, belirli bir yolculuğa bir tıp içerip içermediğini tahmin etmek için bir model geliştirirsiniz.

## <a name="create-an-apache-spark-machine-learning-model"></a>Apache Spark Machine Learning modeli oluşturma

1. PySpark çekirdeğini kullanarak bir not defteri oluşturun. Yönergeler için bkz. [Not defteri oluşturma](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Bu uygulama için gereken türleri içeri aktarın. Aşağıdaki kodu kopyalayıp boş bir hücreye yapıştırın ve ardından SHIFT + enter tuşlarına basın. Veya kodun solundaki mavi yürütme simgesini kullanarak hücreyi çalıştırın.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    PySpark çekirdeği nedeniyle, açıkça herhangi bir bağlam oluşturmanız gerekmez. İlk kod hücresini çalıştırdığınızda Spark bağlamı sizin için otomatik olarak oluşturulur.

## <a name="construct-the-input-dataframe"></a>Giriş veri çerçevesini oluşturun

Ham veriler bir Parquet biçiminde olduğundan, dosyayı doğrudan veri çerçevesi olarak belleğe çekmek için Spark bağlamını kullanabilirsiniz. Aşağıdaki adımlarda bulunan kod varsayılan seçenekleri kullandığından, gerekirse veri türlerinin ve diğer şema özniteliklerinin eşlenmesine zorlamak mümkündür.

1. Kodu yeni bir hücreye yapıştırarak Spark DataFrame oluşturmak için aşağıdaki satırları çalıştırın. Bu adım, verileri açık veri kümeleri API 'SI aracılığıyla alır. Bu verilerin tümünün çekilerek 1.500.000.000 satır hakkında bilgi oluşturulur. 

   Sunucusuz Apache Spark havuzunuzun boyutuna bağlı olarak, ham veriler çok büyük olabilir veya üzerinde çalışmak için çok fazla zaman alabilir. Bu verileri daha küçük bir değere filtreleyerek azaltabilirsiniz. Aşağıdaki kod örneği, `start_date` `end_date` verilerin tek bir ayı döndüren bir filtre uygulamak için ve kullanır.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Daha basit filtrelemeye yönelik aşağı, istatistiksel bir perspektiften verilere yönelik sapma gösterebilir. Diğer bir yaklaşım, Spark içinde yerleşik olarak bulunan örneklemeyi kullanmaktır. 

   Aşağıdaki kod, yukarıdaki koddan sonra uygulanırsa veri kümesini yaklaşık 2.000 satır için azaltır. Bu örnekleme adımını basit filtre yerine veya basit filtreyle birlikte kullanabilirsiniz.

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Artık okunmuş olduğunu görmek için verilere bakmak mümkündür. Veri kümesinin boyutuna bağlı olarak, verileri tam küme yerine bir alt kümeyle gözden geçirmek genellikle daha iyidir. 

   Aşağıdaki kod, verileri görüntülemenin iki yolunu sunar. İlk yöntem temel ' dir. İkinci şekilde, verileri grafik halinde görselleştirme özelliği ile birlikte daha zengin bir kılavuz deneyimi sağlanır.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. Oluşturulan veri kümesinin boyutuna ve Not defterini pek çok kez denemenize veya çalıştırmaya ihtiyaç duyuyorsanız, veri kümesini çalışma alanında yerel olarak önbelleğe almak isteyebilirsiniz. Açık önbelleğe alma gerçekleştirmek için üç yol vardır:

   - Veri çerçevesini yerel olarak bir dosya olarak kaydedin.
   - Veri çerçevesini geçici bir tablo veya görünüm olarak kaydedin.
   - Veri çerçevesini kalıcı bir tablo olarak kaydedin.

Bu yaklaşımların ilk ikisi aşağıdaki kod örneklerine dahil edilmiştir.

Geçici bir tablo veya görünüm oluşturmak verilere farklı erişim yolları sağlar, ancak yalnızca Spark örnek oturumunun süresi boyunca sürer.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Verileri hazırlama

Ham biçimindeki veriler genellikle doğrudan bir modele geçirilmesi için uygun değildir. Veri üzerinde, modelin tüketebileceği bir duruma getirmek için bir dizi eylem gerçekleştirmeniz gerekir.

Aşağıdaki kodda dört işlem sınıfı gerçekleştirirsiniz:

- Filtreleme yoluyla aykırı değerleri veya hatalı değerleri kaldırma.
- Gerekli olmayan sütunların kaldırılması.
- Modelin daha verimli bir şekilde çalışmasını sağlamak için ham verilerden türetilmiş yeni sütunların oluşturulması. Bu işlem bazen korturlama olarak adlandırılır.
- Kapatma. İkili sınıflandırma az olduğu için (belirli bir yolculuğa sahip bir ipucu olacaktır), ipucu miktarını 0 veya 1 değerine dönüştürmeniz gerekir.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Ardından son özellikleri eklemek için verilerin üzerine ikinci bir geçiş yaparsınız.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Lojistik regresyon modeli oluşturma

Son görev, etiketli verileri Lojistik gerileme aracılığıyla çözümlenebilecek bir biçime dönüştürmelidir. Lojistik regresyon algoritmasının girişi, *özellik vektörünün* giriş noktasını temsil eden bir sayı vektörü olduğu bir *etiket/özellik vektör çiftleri* kümesi olmalıdır. 

Bu nedenle, kategorik sütunları sayılara dönüştürmeniz gerekir. Özellikle, `trafficTimeBins` ve `weekdayString` sütunlarını tamsayı temsillerine dönüştürmeniz gerekir. Dönüştürmeyi gerçekleştirmeye yönelik birden çok yaklaşım vardır. Aşağıdaki örnek `OneHotEncoder` , yaygın olarak kullanılan yaklaşımı kullanır.

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Bu eylem, bir modeli eğitebilmeniz için tüm sütunları doğru biçimde içeren yeni bir veri çerçevesine neden olur.

## <a name="train-a-logistic-regression-model"></a>Lojistik regresyon modeli eğitme

İlk görev, veri kümesini bir eğitim kümesine ve test ya da doğrulama kümesine bölemedir. Burada bölme isteğe bağlı olarak belirlenir. Modeli etkilediklerini görmek için farklı bölünmüş ayarlarla denemeler yapın.

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Artık iki veri çerçevesi olduğuna göre, sonraki görev model formülünü oluşturmak ve eğitim veri çerçevesinde çalıştırmak içindir. Daha sonra test veri çerçevesine göre doğrulayabilirsiniz. Farklı birleşimlerin etkilerini görmek için model formülünün farklı sürümleriyle denemeler yapın.

> [!Note]
> Modeli kaydetmek için *Depolama Blobu verileri katılımcısı* Azure rolüne ihtiyacınız olacaktır. Depolama hesabınız altında **Access Control (IAM)** bölümüne gidin ve **rol ataması Ekle**' yi seçin. Depolama Blobu veri katılımcısı rolünü Azure SQL veritabanı sunucunuza atayın. Yalnızca sahip ayrıcalıklarına sahip Üyeler bu adımı gerçekleştirebilir. 
>
>Çeşitli Azure yerleşik rolleri için [bu kılavuza](../../role-based-access-control/built-in-roles.md)bakın.

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

Bu hücrenin çıktısı:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Tahmine ilişkin görsel bir temsili oluşturun

Artık bu testin sonuçları hakkında neden olacak bir son görselleştirme oluşturabilirsiniz. [ROC eğrisi](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) , sonucu gözden geçirmek için bir yoldur.

```python
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![İpucu modelinde Lojistik gerileme için ROC eğrisini gösteren grafik.](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Spark örneğini kapatma

Uygulamayı çalıştırmayı bitirdikten sonra, sekmeyi kapatarak, kaynakları serbest bırakmak için Not defterini kapatın. Veya Not defteri 'nin altındaki durum panelinden **oturumu sonlandır** ' ı seçin.

## <a name="see-also"></a>Ayrıca bkz.

- [Genel Bakış: Azure SYNAPSE Analytics 'te Apache Spark](apache-spark-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Apache Spark belgeleri için .NET](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
- [Resmi belgeleri Apache Spark](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Bazı resmi Apache Spark belgeleri, Azure SYNAPSE Analytics 'te Apache Spark bulunmayan Spark konsolunun kullanılmasına bağımlıdır. Bunun yerine [Not defteri](../quickstart-apache-spark-notebook.md) veya [IntelliJ](../spark/intellij-tool-synapse.md) deneyimlerini kullanın.
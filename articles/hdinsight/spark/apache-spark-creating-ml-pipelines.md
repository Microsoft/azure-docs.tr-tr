---
title: Apache Spark Machine Learning işlem hattı oluşturma-Azure HDInsight
description: Azure HDInsight 'ta veri işlem hatları oluşturmak için Apache Spark Machine Learning kitaplığını kullanın.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: b0de9103fd022dc74e7c75017a602eb6701686fe
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494665"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Apache Spark makine öğrenimi işlem hattı oluşturma

Apache Spark ölçeklenebilir makine öğrenme kitaplığı (MLlib), modelleme yeteneklerini dağıtılmış bir ortama getirir. Spark paketi [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) , veri çerçeveleri üzerinde oluşturulmuş üst düzey API 'lerin bir kümesidir. Bu API 'Ler, pratik makine öğrenimi işlem hatlarını oluşturmanıza ve ayarlamanıza yardımcı olur.  *Spark Machine Learning* , eskı RDD tabanlı Işlem hattı API 'sini değil, bu Mllib DATAFRAME tabanlı API 'ye başvurur.

Machine Learning (ML) işlem hattı, birden çok makine öğrenimi algoritmasını birlikte birleştiren bir iş akışıdır. Bir dizi algoritmanın yanı sıra verileri işlemek ve öğrenmek için gereken birçok adım olabilir. İşlem hatları, bir makine öğrenimi sürecinin aşamalarını ve sıralamasını tanımlar. MLlib içinde, bir işlem hattının aşamaları, her biri bir transformatör ve bir Estimator her bir görev gerçekleştirirken belirli bir PipelineStages dizisi tarafından temsil edilir.

Transformatör, `transform()` yöntemini kullanarak bir veri çerçevesini diğerine dönüştüren algoritmadır. Örneğin, bir özellik transformatörü bir veri çerçevesinin bir sütununu okuyabilir, bunu başka bir sütunla eşleyebilir ve eşlenmiş sütunuyla birlikte yeni bir veri çerçevesinin çıktısını alabilir.

Bir Estimator, öğrenme algoritmalarının soyutlamasıdır ve bir dönüştürücü oluşturmak için bir veri kümesine ekleme veya eğitim yapmaktan sorumludur. Bir Estimator, bir veri çerçevesini kabul eden ve bir transformatör olan veri çerçevesini üreten `fit()`adlı bir yöntemi uygular.

Bir transformatör veya bir Estimator 'ın durum bilgisi olan her örneği, parametreleri belirtirken kullanılan kendi benzersiz tanımlayıcısına sahiptir. Her ikisi de bu parametreleri belirtmek için Tekdüzen API kullanır.

## <a name="pipeline-example"></a>İşlem hattı örneği

ML işlem hattının pratik bir kullanımını göstermek için bu örnek, HDInsight kümeniz için Azure Storage veya Data Lake Storage varsayılan depolama alanında önceden yüklenmiş olarak gelen örnek `HVAC.csv` veri dosyasını kullanır. Dosyanın içeriğini görüntülemek için `/HdiSamples/HdiSamples/SensorSampleData/hvac` dizinine gidin. `HVAC.csv`, çeşitli binalarda HVAC (*Isıtma, havalandırma ve hava*) sistemleri için hem hedef hem de gerçek sıcaklıklar içeren bir zaman kümesi içerir. Amaç, verileri veri üzerinde eğmektir ve belirli bir bina için bir tahmin sıcaklığını üretir.

Aşağıdaki kod:

1. `BuildingID`, `SystemInfo` (sistemin tanımlayıcısını ve yaşını `label`) depolayan bir `LabeledDocument`tanımlar (bina çok fazla çalışırken 0,0, aksi takdirde 1,0).
2. Bir satır (satır) veri alan ve binasının hedef sıcaklığın gerçek sıcaklığa göre "etkin" olup olmadığını belirleyen özel bir Ayrıştırıcı işlevi oluşturur `parseDocument`.
3. Kaynak verileri ayıklarken ayrıştırıcısı uygular.
4. Eğitim verileri oluşturur.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument


def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)


# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Bu örnek işlem hattının üç aşaması vardır: `Tokenizer` ve `HashingTF` (her iki dönüştürücüler) ve `Logistic Regression` (bir Estimator).  `training` veri çerçevesindeki ayıklanan ve ayrıştırılmış veriler `pipeline.fit(training)` çağrıldığında işlem hattı üzerinden akar.

1. İlk aşama `Tokenizer`, `SystemInfo` giriş sütununu (sistem tanımlayıcısı ve yaş değerlerinden oluşan) `words` çıkış sütununa böler. Bu yeni `words` sütunu DataFrame 'e eklenir. 
2. İkinci aşama `HashingTF`, yeni `words` sütununu Özellik vektörlerine dönüştürür. Bu yeni `features` sütunu DataFrame 'e eklenir. Bu ilk iki aşama dönüştürücüler ' dir. 
3. `LogisticRegression`, üçüncü aşama bir tahmin değildir ve bu nedenle işlem hattı bir `LogisticRegressionModel`üretmek için `LogisticRegression.fit()` yöntemini çağırır. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

`Tokenizer` ve `HashingTF` dönüştürücüler tarafından eklenen yeni `words` ve `features` sütunlarını ve `LogisticRegression` Estimator 'un bir örneğini görmek için özgün veri çerçevesinde bir `PipelineModel.transform()` yöntemi çalıştırın. Üretim kodunda, bir sonraki adım, eğitimi doğrulamak için bir test veri çerçevesini geçirmek olacaktır.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

`model` nesnesi artık tahminleri yapmak için kullanılabilir. Bu makine öğrenimi uygulamasının tam örneği ve bunu çalıştırmaya yönelik adım adım yönergeler için bkz. [Azure HDInsight 'ta Machine Learning uygulamaları oluşturma Apache Spark](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Ayrıca bkz.

* [Azure 'da Scala ve Apache Spark kullanan veri bilimi](../../machine-learning/team-data-science-process/scala-walkthrough.md)

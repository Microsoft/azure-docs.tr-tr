---
title: 'Öğretici: otomatik makine öğrenimi ile gerileme'
titleSuffix: Azure Machine Learning
description: Sizin için bir gerileme modeli üreten otomatik makine öğrenimi denemesi oluşturmak için Python SDK ile kod yazın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 08/14/2020
ms.custom: devx-track-python, automl
ms.openlocfilehash: 85129cf282e39b4f4932cc5e9f7cfd72d1e445b0
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210644"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Öğretici: Taksi ücretlerini tahmin etmek için otomatik makine öğrenmesini kullanma

Bu öğreticide, NYC TAXI tarifeli havayolu fiyatlarını tahmin etmek üzere bir [gerileme modeli](concept-automated-ml.md#regression) oluşturmak için Azure Machine Learning SDK 'sında otomatik makine öğrenimini kullanırsınız. Bu işlem, eğitim verilerini ve yapılandırma ayarlarını kabul eder ve en iyi modele ulaşmak için farklı özellik normalleştirme/standartlaştırma yöntemlerinin, modellerinin ve hiper parametre ayarlarının birleşimleri aracılığıyla otomatik olarak yinelenir.

![Akış diyagramı](./media/tutorial-auto-train-models/flow2.png)

Bu öğreticide Python SDK 'sını kullanarak kod yazacaksınız.  Aşağıdaki görevleri öğreneceksiniz:

> [!div class="checklist"]
> * Azure açık veri kümelerini kullanarak verileri indirme, dönüştürme ve Temizleme
> * Otomatik makine öğrenimi regresyon modelini eğitme
> * Model doğruluğunu hesapla

Ayrıca, bu diğer model türleri için otomatik makine öğrenimini deneyin: 

* Öğretici: Azure Machine Learning-kodu olmayan bir örnek için [OTOMATIK ml ile bir sınıflandırma modeli oluşturun](tutorial-first-experiment-automated-ml.md) .
* [Öğretici: otomatik makine öğrenimi ile talep tahmini](tutorial-automated-ml-forecast.md) -kod içermeyen bir örnek.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. Azure Machine Learning [ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

* Zaten bir Azure Machine Learning çalışma alanınız veya Not defteri sanal makineniz yoksa [Kurulum öğreticisini](tutorial-1st-experiment-sdk-setup.md) doldurun.
* Kurulum öğreticisini tamamladıktan sonra, aynı not defteri sunucusunu kullanarak *öğreticiler/Regression-automl-NYC-Taxi-Data/Regression-Automated-ml. ipynb* Not defterini açın.

Bu öğretici, kendi [Yerel ortamınızda](how-to-configure-environment.md#local)çalıştırmak istiyorsanız [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 'da da kullanılabilir. Gerekli paketleri almak için, 
* [Tam `automl` istemciyi yükler](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment).
* `pip install azureml-opendatasets azureml-widgets`Gerekli paketleri almak için öğesini çalıştırın.

## <a name="download-and-prepare-data"></a>Verileri indirme ve hazırlama

Gerekli paketleri içeri aktarın. Açık veri kümeleri paketi, `NycTlcGreen` indirilmeden önce tarih parametrelerini kolayca filtrelemek için her bir veri kaynağını temsil eden bir sınıf içerir (örneğin).

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Taxı verilerini tutmak için bir veri çerçevesi oluşturarak başlayın. Spark olmayan bir ortamda çalışırken, açık veri kümeleri, büyük veri kümelerinde kaçınmak için belirli sınıflarla tek seferde bir aydan verilerin indirilmesini sağlar `MemoryError` .

Taxı verilerini indirmek için, bir kerede bir ayda bir kez ve `green_taxi_df` veri çerçevesini bloktan kaçınmak için bu dosyayı her ayın rastgele örnek 2.000 kayıtlarına eklemeden önce bir ay olarak yeniden koyun. Sonra verileri önizleyin.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

|Konağında VendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| Üçlü mesafe|   Pulocationıd|   Dolocationıd|   Pickupboylam|    Pickupenlem| Açılan Boylam    |...|   paymentType |fareAmount |ına| Mtavergisi| improvementSurcharge|   tipAmount|  tollsAmount|    Ehailücret|   totalAmount|    Üçlü tür|
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|Yok|Yok|-73,88|40,84|-73,94|...|2|15,00|0.50|0.50|0.3|0.00|0.00|nBir|16,30|
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|Yok|Yok|-73,96|40,81|-73,96|...|2|4,50|1,00|0.50|0.3|0.00|0.00|nBir|6,30|
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Yok|Yok|-73,92|40,76|-73,91|...|2|4.00|0,00|0.50|0.3|0.00|0.00|nBir|4,80|
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|Yok|Yok|-73,81|40,70|-73,82|...|2|12,50|0.50|0.50|0.3|0.00|0.00|nBir|13,80|
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0.50|Yok|Yok|-73,92|40,76|-73,92|...|2|4.00|0.50|0.50|0|0.00|0.00|nBir|5.00|
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Yok|Yok|-73,96|40,72|-73,95|...|2|6,50|0.50|0.50|0.3|0.00|0.00|nBir|7.80|
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|Yok|Yok|-73,88|40,76|-73,87|...|2|6,00|0,00|0.50|0.3|0.00|0.00|nBir|6,80|
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|Yok|Yok|-73,96|40,72|-73,91|...|2|12,50|0.50|0.50|0.3|0.00|0.00|nBir|13,80|
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|Yok|Yok|-73,94|40,71|-73,95|...|1|7,00|0,00|0.50|0.3|1,75|0,00|nBir|9,55|
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0,65|Yok|Yok|-73,94|40,71|-73,94|...|2|5.00|0.50|0.50|0.3|0.00|0.00|nBir|6,30|

Artık ilk veriler yüklendikten sonra, toplama tarih/saati alanından çeşitli zamana dayalı özellikler oluşturmak için bir işlev tanımlayın. Bu işlem, ay numarası, ay günü, haftanın günü ve günün saati için yeni alanlar oluşturur ve modelin zaman tabanlı mevsimsellik açısından çarpaya izin verir. İşlevi, `apply()` `build_time_features()` TAXI verilerinde her satıra yinelemeli olarak uygulamak için dataframe üzerindeki işlevini kullanın.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

|Konağında VendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| Üçlü mesafe|   Pulocationıd|   Dolocationıd|   Pickupboylam|    Pickupenlem| Açılan Boylam    |...|   paymentType|fareAmount  |ına| Mtavergisi| improvementSurcharge|   tipAmount|  tollsAmount|    Ehailücret|   totalAmount|Üçlü tür|month_num|day_of_month|day_of_week|hour_of_day
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|----|----|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|Yok|Yok|-73,88|40,84|-73,94|...|2|15,00|0.50|0.50|0.3|0.00|0.00|nBir|16,30|1,00|1|11|6|
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|Yok|Yok|-73,96|40,81|-73,96|...|2|4,50|1,00|0.50|0.3|0.00|0.00|nBir|6,30|1,00|1|20|1|
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Yok|Yok|-73,92|40,76|-73,91|...|2|4.00|0,00|0.50|0.3|0.00|0.00|nBir|4,80|1,00|1|1|3|
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|Yok|Yok|-73,81|40,70|-73,82|...|2|12,50|0.50|0.50|0.3|0.00|0.00|nBir|13,80|1,00|1|17|5|
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0.50|Yok|Yok|-73,92|40,76|-73,92|...|2|4.00|0.50|0.50|0|0.00|0.00|nBir|5.00|1,00|1|1|3|
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Yok|Yok|-73,96|40,72|-73,95|...|2|6,50|0.50|0.50|0.3|0.00|0.00|nBir|7.80|1,00|1|4|6|
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|Yok|Yok|-73,88|40,76|-73,87|...|2|6,00|0,00|0.50|0.3|0.00|0.00|nBir|6,80|1,00|1|3|5|
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|Yok|Yok|-73,96|40,72|-73,91|...|2|12,50|0.50|0.50|0.3|0.00|0.00|nBir|13,80|1,00|1|9|4|
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|Yok|Yok|-73,94|40,71|-73,95|...|1|7,00|0,00|0.50|0.3|1,75|0,00|nBir|9,55|1,00|1|11|6|
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0,65|Yok|Yok|-73,94|40,71|-73,94|...|2|5.00|0.50|0.50|0.3|0.00|0.00|nBir|6,30|1,00|1|22|3|

Eğitim veya ek özellik oluşturma için ihtiyaç duymayabilmeniz gereken bazı sütunları kaldırın.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Verileri temizleme

Her bir `describe()` alanın Özet istatistiklerini görmek için bu işlevi yeni dataframe üzerinde çalıştırın.

```python
green_taxi_df.describe()
```

|Konağında VendorID|passengerCount|Üçlü mesafe|Pickupboylam|Pickupenlem|Açılan Boylam|Açılan Enlem|  totalAmount|month_num day_of_month|day_of_week|hour_of_day
|----|----|---|---|----|---|---|---|---|---|---|
|count|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|
|ortalama|1,78|1.37|2.87|-73,83|40,69|-73,84|40,70|14,75|6,50|15,13|
|std|0,41|1,04|2,93|2,76|1,52|2,61|1,44|12,08|3,45|8,45|
|dk|1,00|0.00|0.00|-74,66|0,00|-74,66|0,00|-300,00|1,00|1,00|
|25%|2,00|1,00|1.06|-73,96|40,70|-73,97|40,70|7.80|3,75|8,00|
|50%|2,00|1,00|1,90|-73,94|40,75|-73,94|40,75|11,30|6,50|15,00|
|75%|2,00|1,00|3,60|-73,92|40,80|-73,91|40,79|17,80|9,25|22,00|
|max|2,00|9,00|97,57|0,00|41,93|0,00|41,94|450,00|12,00|30,00|


Özet istatistiklerinde, mantıksal veya daha fazla alan içeren birkaç alanın, model doğruluğunu azaltacak şekilde olduğunu görürsünüz. İlk olarak, Manhattan alanının sınırları dahilinde olacak şekilde Enlem/Long alanlarını filtreleyin. Bu, diğer özelliklerle ilişkisine göre, daha uzun bir süre içinde daha fazla vergilenme veya gezilerin filtreleneceği.

Ayrıca, `tripDistance` alanı sıfırdan büyük ancak 31 milden küçük (iki Enlem/uzun çiftleri arasındaki haversinüsü uzaklığı) olarak filtreleyin. Bu, tutarsız seyahat maliyeti olan uzun süreli döngüleri ortadan kaldırır.

Son olarak, `totalAmount` alanda taksi Fares için negatif değerler vardır. Bu, modelimizin bağlamında mantıklı değildir ve `passengerCount` alanda en düşük değerler sıfır olan hatalı veriler vardır.

Sorgu işlevlerini kullanarak bu anomali filtreleme yapın ve ardından eğitim için gereksiz son sütunu kaldırın.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

`describe()`Temizleme 'nin beklendiği gibi çalıştığından emin olmak için verileri yeniden çağırın. Artık makine öğrenimi model eğitimi için kullanılacak, hazırlanmış ve yeniden hazırlanan bir TAXI, tatil ve hava durumu verisi ayarlamış olursunuz.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Çalışma alanını yapılandırma

Mevcut çalışma alanından bir çalışma alanı nesnesi oluşturun. [Çalışma alanı](/python/api/azureml-core/azureml.core.workspace.workspace) , Azure aboneliğinizi ve kaynak bilgilerinizi kabul eden bir sınıftır. Ayrıca, modelinizi izlemek ve izlemek için bir bulut kaynağı oluşturur. `Workspace.from_config()` Dosya **config.js** okur ve kimlik doğrulama ayrıntılarını adlı bir nesneye yükler `ws` . Bu öğreticideki kodun kalanında `ws` kullanılır.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Verileri eğitme ve test kümelerine Böl

Kitaplığındaki işlevini kullanarak verileri eğitim ve test kümelerine ayırın `train_test_split` `scikit-learn` . Bu işlev, model eğitimi için verileri x (**Özellikler**) veri kümesine ayırır ve test için y (**tahmin edilecek değerler**) veri kümesini gösterir.

`test_size`Parametresi, teste ayrılacak verilerin yüzdesini belirler. `random_state`Parametresi, tren-test bölünmeleri belirleyici olacak şekilde rastgele oluşturucuya bir çekirdek ayarlar.

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

Bu adımın amacı, doğru doğruluğu ölçmek için modeli eğmekte kullanılmamış olan tamamlanmış modeli test etmek için veri noktalarına sahip değildir.

Diğer bir deyişle, iyi eğitilen bir modelin zaten görmemiş verilerden tahmine dayalı hale getirme yapabilmesi gerekir. Artık, bir makine öğrenimi modeline otomatik eğitim için hazırlanan veriler vardır.

## <a name="automatically-train-a-model"></a>Bir modeli otomatik olarak eğitme

Bir modeli otomatik olarak eğitmek için aşağıdaki adımları uygulayın:
1. Deneme çalıştırmasının ayarlarını tanımlayın. Eğitim verilerinizi yapılandırmaya ekleyin ve eğitim sürecini denetleyen ayarları değiştirin.
1. Model ayarlama için denemeyi gönder. Deneme gönderdikten sonra işlem, farklı makine öğrenimi algoritmalarını ve hiper parametre ayarlarını kullanarak, tanımlı kısıtlamalarınız için uygun şekilde yinelenir. Doğruluk ölçüsünü en iyi duruma getirerek en uygun modeli seçer.

### <a name="define-training-settings"></a>Eğitim ayarlarını tanımlama

Eğitim için deneme parametresi ve model ayarlarını tanımlayın. [Ayarların](how-to-configure-auto-train.md)tam listesini görüntüleyin. Denemeyi bu varsayılan ayarlarla göndermek yaklaşık 5-20 dakika sürer, ancak daha kısa bir çalışma süresi istiyorsanız `experiment_timeout_hours` parametreyi küçültün.

|Özellik| Bu öğreticideki değer |Description|
|----|----|---|
|**iteration_timeout_minutes**|10|Her yineleme için dakika cinsinden zaman sınırı. Her yineleme için daha fazla zaman gerektiren daha büyük veri kümeleri için bu değeri arttırın.|
|**experiment_timeout_hours**|0.3|Deneme sona ermeden önce tüm yinelemelerin birleştirilebilmesi için en fazla saat cinsinden süre.|
|**enable_early_stopping**|Doğru|Puan, kısa vadede iyileştirilmediğinden erken sonlandırmayı etkinleştirmek için bayrak.|
|**primary_metric**| spearman_correlation | İyileştirmek istediğiniz ölçüm. En uygun model bu ölçüme göre seçilecek.|
|**korturlama**| auto | **Otomatik**, deneme kullanarak giriş verilerini önceden işleyebilir (eksik verileri işleme, metni sayısal olarak dönüştürme vb.)|
|**ayrıntı**| logging.INFO | Günlüğe kaydetme düzeyini denetler.|
|**n_cross_validations**|5|Doğrulama verileri belirtilmediğinde gerçekleştirilecek çapraz doğrulama bölme sayısı.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 10,
    "experiment_timeout_hours": 0.3,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Tanımlı eğitim ayarlarınızı bir `**kwargs` nesne için parametre olarak kullanın `AutoMLConfig` . Ayrıca, bu durumda eğitim verilerinizi ve model türünü de belirtin `regression` .

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> Otomatik makine öğrenimi ön işleme adımları (özellik normalleştirme, eksik verileri işleme, metni sayısal olarak dönüştürme, vb.) temel modelin bir parçası haline gelir. Tahmin için model kullanılırken, eğitim sırasında uygulanan aynı ön işleme adımları, giriş verilerinize otomatik olarak uygulanır.

### <a name="train-the-automatic-regression-model"></a>Otomatik regresyon modelini eğitme

Çalışma alanınızda bir deneme nesnesi oluşturun. Deneme, bireysel çalışmalarınız için bir kapsayıcı olarak davranır. Tanımlanan `automl_config` nesneyi denemenize geçirin ve `True` çalıştırma sırasında ilerlemeyi görüntülemek için çıktıyı ayarlayın.

Denemeyi başlattıktan sonra, bu gösterilen çıkış, deney olarak canlı güncelleştirmeler çalışır. Her yineleme için model türünü, çalışma süresini ve eğitim doğruluğunu görürsünüz. Alan, `BEST` Ölçüm Türünüz temelinde en iyi çalışan eğitim Puanını izler.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "Tutorial-NYCTaxi")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
Running on local machine
Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
Current status: DatasetFeaturization. Beginning to featurize the dataset.
Current status: DatasetEvaluation. Gathering dataset statistics.
Current status: FeaturesGeneration. Generating features for the dataset.
Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
Current status: ModelSelection. Beginning model selection.

****************************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE: A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
****************************************************************************************************

 ITERATION   PIPELINE                                       DURATION      METRIC      BEST
         0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
         1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
         2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
         3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
         4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
         5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
         6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
         7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
         8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
         9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
        10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
        11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
        12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
        13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
        14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
        15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
        16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
        17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
        18   VotingEnsemble                                 0:00:23       0.9471    0.9471
        19   StackEnsemble                                  0:00:27       0.9463    0.9471
```

## <a name="explore-the-results"></a>Sonuçları inceleme

[Jupyıter pencere öğesiyle](/python/api/azureml-widgets/azureml.widgets)otomatik eğitime ilişkin sonuçları keşfet. Pencere öğesi, eğitim doğruluğu ölçümleri ve meta verileri ile birlikte tüm bireysel çalıştırma yinelemelerini bir grafik ve tablo görmenizi sağlar. Ayrıca, açılan seçiciyle, birincil ölçümünüzün farklı doğruluk ölçümlerine filtre uygulayabilirsiniz.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupi pencere öğesi çalıştırma ayrıntıları ](./media/tutorial-auto-train-models/automl-dash-output.png)
 ![ jupi pencere öğesi çizimi](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>En iyi modeli alma

Yinelemeinizden en iyi modeli seçin. İşlevi, en `get_output` iyi çalışmayı ve en son çağrıya yönelik olarak sığdırılmış modeli döndürür. Üzerinde aşırı yüklemeleri kullanarak `get_output` , herhangi bir günlüğe kaydedilmiş ölçüm veya belirli bir yineleme için en iyi çalıştırma ve bağlı modeli elde edebilirsiniz.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>En iyi model doğruluğunu test edin

Taksi Fares 'yi tahmin etmek için test verileri kümesindeki tahminleri çalıştırmak için en iyi modeli kullanın. İşlevi `predict` en iyi modeli kullanır ve veri kümesinden y, **seyahat maliyeti** değerlerini tahmin eder `x_test` . İlk 10 tahmini maliyet değerini ' den yazdırın `y_predict` .

```python
y_test = x_test.pop("totalAmount")

y_predict = fitted_model.predict(x_test)
print(y_predict[:10])
```

`root mean squared error`Sonuçların sayısını hesaplayın. Tahmin edilen `y_test` değerlerle karşılaştırmak için veri çerçevesini bir listeye dönüştürün. İşlevi `mean_squared_error` iki dizi değer alır ve aralarındaki ortalama kare içinde hata sayısını hesaplar. Sonucun kare kökünü almak, y **değişkeni ile aynı** birimlerde bir hata verir. EPI tarifeli havayolu tahminlerinin gerçek farlarından ne kadar olduğunu kabaca gösterir.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Tam `y_actual` ve veri kümelerini kullanarak ortalama mutlak yüzde hatasını (MAPE) hesaplamak için aşağıdaki kodu çalıştırın `y_predict` . Bu ölçüm, tahmin edilen ve gerçek değerler arasındaki mutlak bir farkı hesaplar ve tüm farkları toplar. Ardından, bu toplamı gerçek değerlerin toplamının yüzdesi olarak ifade eder.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


İki tahmin doğruluk ölçümlerinde, modelin, veri kümesinin özelliklerinden, genellikle +-$4,00 ' de ve yaklaşık %15 ' te vergilenme Fareli tahminlerinde oldukça iyi olduğunu görürsünüz.

Geleneksel makine öğrenimi modeli geliştirme süreci, yoğun kaynak yoğunluğu sağlar ve çok sayıda modelin sonuçlarını çalıştırmak ve karşılaştırmak için önemli etki alanı bilgisi ve zaman yatırımı gerektirir. Otomatik makine öğrenimi kullanmak, senaryonuza yönelik birçok farklı modeli hızlıca test etmenin harika bir yoludur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Azure Machine Learning öğreticileri çalıştırmayı planlıyorsanız, bu bölümü tamamlamayın.

### <a name="stop-the-compute-instance"></a>İşlem örneğini durdur

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Her şeyi sil

Oluşturduğunuz kaynakları kullanmayı planlamıyorsanız, herhangi bir ücret ödemezsiniz.

1. Azure portalının en sol tarafındaki **Kaynak gruplarını** seçin.
1. Listeden oluşturduğunuz kaynak grubunu seçin.
1. **Kaynak grubunu sil**'i seçin.
1. Kaynak grubu adını girin. Ardından **Sil**’i seçin.

Ayrıca, kaynak grubunu koruyabilir ancak tek bir çalışma alanını silebilirsiniz. Çalışma alanı özelliklerini görüntüleyin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu otomatik makine öğrenimi öğreticisinde aşağıdaki görevleri yaptınız:

> [!div class="checklist"]
> * Bir deneme için çalışma alanı ve hazırlanan veriler yapılandırıldı.
> * Özel parametrelerle yerel olarak otomatik regresyon modeli kullanılarak eğitilen.
> * Araştırılan ve gözden geçirilmiş eğitim sonuçları.

[Modelinizi](tutorial-deploy-models-with-aml.md) Azure Machine Learning ile dağıtın.

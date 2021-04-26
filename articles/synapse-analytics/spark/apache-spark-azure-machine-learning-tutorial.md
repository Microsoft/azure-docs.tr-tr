---
title: "Öğretici: otomatik makine öğrenimi ile Python 'da model eğitme"
description: Apache Spark ve otomatik makine öğrenimi kullanarak Python 'da makine öğrenimi modelini eğitme hakkında öğretici.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 89309cfe427183d594a5cc2f76332ae150d4f803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102498686"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>Öğretici: otomatik makine öğrenimi ile Python 'da model eğitme

Azure Machine Learning, makine öğrenimi modellerini eğmenize, dağıtmanıza, otomatikleştirmenize, yönetmenize ve izlemenize olanak tanıyan bulut tabanlı bir ortamdır. 

Bu öğreticide, TAXI tarifeli havayolu fiyatlarını tahmin etmek üzere bir gerileme modeli oluşturmak için Azure Machine Learning ' de [otomatik makine öğrenimini](../../machine-learning/concept-automated-ml.md) kullanırsınız. Bu işlem, eğitim verilerini ve yapılandırma ayarlarını kabul ederek ve farklı yöntemlerin, modellerin ve hiper parametre ayarlarının birleşimlerinde otomatik olarak yinelenerek en iyi modele ulaşır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
- Apache Spark ve Azure açık veri kümelerini kullanarak verileri indirin.
- Apache Spark Dataframe kullanarak verileri dönüştürün ve temizleyin.
- Otomatik makine öğrenimi 'nde regresyon modeli eğitme.
- Model doğruluğunu hesaplayın.

## <a name="before-you-begin"></a>Başlamadan önce

- [Sunucusuz Apache Spark havuzu oluşturma](../quickstart-create-apache-spark-pool-studio.md) hızlı başlangıcı ' nı izleyerek sunucusuz Apache Spark havuzu oluşturun.
- Mevcut bir Azure Machine Learning çalışma alanınız yoksa [Azure Machine Learning çalışma alanı kurulum](../../machine-learning/tutorial-1st-experiment-sdk-setup.md) öğreticisini doldurun. 

## <a name="understand-regression-models"></a>Gerileme modellerini anlama

*Regresyon modelleri* , bağımsız tahmine göre sayısal çıkış değerlerini tahmin eder. Gerileme ' de amaç, bir değişkenin diğerlerini nasıl etkilediğini tahmin ederek bu bağımsız tahmine dayalı değişkenler arasında ilişki kurmaya yardımcı olur.  

### <a name="example-based-on-new-york-city-taxi-data"></a>New York City TAXI verilerine dayalı örnek

Bu örnekte, New York şehrinden (NYC), TAXI gidiş dönüş ipucu verilerinde bazı analizler gerçekleştirmek için Spark 'ı kullanırsınız. Veriler [Azure açık veri kümeleri](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)aracılığıyla kullanılabilir. Veri kümesinin bu alt kümesi, her seyahat, başlangıç ve bitiş saati ve konumları ve maliyeti dahil olmak üzere sarı TAXI seyahatleriyle ilgili bilgiler içerir.

> [!IMPORTANT]
> Bu verilerin depolama konumundan çekilerek ilgili ek ücretler de olabilir. Aşağıdaki adımlarda, NYC TAXI tarifeli havayolu fiyatlarını tahmin etmek için bir model geliştirirsiniz. 

##  <a name="download-and-prepare-the-data"></a>Verileri indirin ve hazırlayın

Aşağıdaki adımları uygulayın:

1. PySpark çekirdeğini kullanarak bir not defteri oluşturun. Yönergeler için bkz. [Not defteri oluşturma](../quickstart-apache-spark-notebook.md#create-a-notebook).
   
    > [!Note]
    > PySpark çekirdeği nedeniyle, açıkça herhangi bir bağlam oluşturmanız gerekmez. İlk kod hücresini çalıştırdığınızda Spark bağlamı sizin için otomatik olarak oluşturulur.
  
2. Ham veriler bir Parquet biçiminde olduğundan, dosyayı doğrudan belleğe bir veri çerçevesi olarak çekmek için Spark bağlamını kullanabilirsiniz. Açık veri kümeleri API 'SI aracılığıyla verileri alarak Spark DataFrame oluşturun. Burada, `schema on read` veri türlerini ve şemayı çıkarması Için Spark DataFrame özelliklerini kullanırsınız. 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from the blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet; note that it won't load any data yet
    df = spark.read.parquet(wasbs_path)

    ```

3. Spark havuzunuzun boyutuna bağlı olarak, ham veriler çok büyük olabilir veya üzerinde çalışmak için çok fazla zaman alabilir. Ve filtrelerini kullanarak bu verileri bir ay gibi daha küçük bir değere filtreleyebilirsiniz ```start_date``` ```end_date``` . Bir veri çerçevesini filtreledikten sonra, ```describe()``` her bir alanın Özet istatistiklerini görmek için yeni veri çerçevesinde işlevi de çalıştırırsınız. 

   Özet istatistiklerine bağlı olarak, veride bazı dar bir düzeyi olduğunu görebilirsiniz. Örneğin, istatistik en düşük seyahat mesafesinin 0 ' dan küçük olduğunu gösterir. Bu düzensiz veri noktalarını filtrelemeniz gerekir.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Bir sütun kümesi seçip toplama alanından çeşitli zamana dayalı özellikler oluşturarak veri kümesinden Özellikler oluşturun `datetime` . Önceki adımdan tanımlanan aykırı değerleri filtreleyin ve daha sonra eğitim için gereksiz olduklarından son birkaç sütunu kaldırın.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                   , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                   , column('puMonth').alias('month_num') \
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                   ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                           .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                   & (sampled_taxi_df.tipAmount >= 0)\
                                   & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                   & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                   & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                   & (sampled_taxi_df.rateCodeId <= 5)\
                                   & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
   Görebileceğiniz gibi, bu, ayın günü için ek sütunları olan yeni bir veri çerçevesi oluşturacak ve toplam saat, hafta içi ve toplam seyahat süresi oluşturacaktır. 

   ![Taxı veri çerçevesinin resmi.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Test ve doğrulama veri kümeleri oluştur

Son veri kümeniz olduktan sonra Spark 'daki işlevini kullanarak verileri eğitim ve test kümelerine bölebilirsiniz ```random_ split ``` . Bu işlev, sunulan ağırlıkları kullanarak verileri model eğitimi için eğitim veri kümesine ve test için doğrulama veri kümesine rastgele ayırır.

```python
# Random split dataset using Spark; convert Spark to pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Bu adım, tamamlanmış modeli test etmek için kullanılan veri noktalarının modeli eğitmek için kullanılmamasını sağlar. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning çalışma alanına bağlanma
Azure Machine Learning, çalışma alanı, Azure aboneliğinizi ve kaynak bilgilerinizi kabul eden bir sınıftır. Ayrıca, modelinizi izlemek ve izlemek için bir bulut kaynağı oluşturur. Bu adımda, mevcut Azure Machine Learning çalışma alanından bir çalışma alanı nesnesi oluşturacaksınız.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Bir veri çerçevesini Azure Machine Learning veri kümesine dönüştürme
Uzak bir deneme göndermek için veri kümenizi bir Azure Machine Learning ```TabularDatset``` örneğine dönüştürün. [Tabulardataset](/python/api/azureml-core/azureml.data.tabulardataset) , belirtilen dosyaları ayrıştırarak verileri tablosal biçimde temsil eder.

Aşağıdaki kod, mevcut çalışma alanını ve varsayılan Azure Machine Learning veri deposunu alır. Daha sonra yeni bir örnek oluşturmak için veri deposunu ve dosya konumlarını yol parametresine geçirir ```TabularDataset``` . 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning default datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into an Azure Machine Learning tabular dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Karşıya yüklenen veri kümesinin resmi.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>Otomatikleştirilmiş bir deneme gönder

Aşağıdaki bölümler otomatik makine öğrenimi denemesi gönderme sürecinde size yol gösterir.

### <a name="define-training-settings"></a>Eğitim ayarlarını tanımlama
1. Bir deneme göndermek için, eğitim için deneme parametresi ve model ayarlarını tanımlamanız gerekir. Ayarların tam listesi için bkz. [Python 'da otomatik makine öğrenimi denemeleri yapılandırma](../../machine-learning/how-to-configure-auto-train.md).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

1. Tanımlı eğitim ayarlarını bir `kwargs` nesneye parametre olarak geçirin `AutoMLConfig` . Spark kullanıyor olduğunuzdan, değişken tarafından otomatik olarak erişilebilen Spark bağlamını de geçirmeniz gerekir ```sc``` . Ayrıca, eğitim verilerini ve model türünü belirtirsiniz ve bu durumda regresyon olur.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                                debug_log='automated_ml_errors.log',
                                training_data = dataset_training,
                                spark_context = sc,
                                model_explainability = False, 
                                label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
>Otomatik makine öğrenimi ön işleme adımları, temel alınan modelin bir parçası haline gelir. Bu adımlar, özellik normalleştirme, eksik verileri işleme ve metni sayısal olarak dönüştürme işlemlerini içerir. Tahmin için model kullanırken, eğitim sırasında uygulanan aynı ön işleme adımları, giriş verilerinize otomatik olarak uygulanır.

### <a name="train-the-automatic-regression-model"></a>Otomatik regresyon modelini eğitme 
Sonra, Azure Machine Learning çalışma alanınızda bir deneme nesnesi oluşturun. Deneme, bireysel çalışmalarınız için bir kapsayıcı olarak davranır. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
Deneme tamamlandığında, çıktı tamamlanan yinelemeler hakkındaki ayrıntıları döndürür. Her yineleme için model türünü, çalışma süresini ve eğitim doğruluğunu görürsünüz. `BEST`Alan, ölçüm türünüz temelinde en iyi çalışan eğitim Puanını izler.

![Model çıktısının ekran görüntüsü.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> Otomatik makine öğrenimi denemesini gönderdikten sonra, çeşitli yinelemeler ve model türleri çalışır. Bu çalıştırma genellikle 90 60 dakika sürer. 

### <a name="retrieve-the-best-model"></a>En iyi modeli alma
Yinelemeden en iyi modeli seçmek için, ```get_output``` işlevi kullanarak en iyi çalışma ve sığdırılmış modeli döndürün. Aşağıdaki kod, herhangi bir günlüğe kaydedilmiş ölçüm veya belirli bir yineleme için en iyi çalıştırma ve sığdırılmış modeli alır.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>Test modeli doğruluğu
1. Model doğruluğunu test etmek için, test veri kümesinde TAXI tarifeli havayolu öngörülerini çalıştırmak üzere en iyi modeli kullanın. ```predict```İşlevi en iyi modeli kullanır ve `y` doğrulama veri kümesinden (tarifeli havayolu amount) değerlerini tahmin eder. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. Kök-ortalama-kare hatası, bir model tarafından tahmin edilen örnek değerler ve gözlenen değerler arasındaki farklılıklara ilişkin sık kullanılan bir ölçüdür. Veri çerçevesini model tarafından tahmin edilen değerlerle karşılaştırarak sonuçların kök-ortalama-kare hatasını hesaplayabilirsiniz `y_test` . 

   İşlevi ```mean_squared_error``` iki dizi alır ve aralarındaki ortalama kare içinde hata hesaplar. Sonra sonucun kare kökünü alın. Bu ölçüm, TAXI tarifeli havayolu tahminlerinin gerçek tarifeli havayolu değerlerinden kabaca ne kadar olduğunu gösterir.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate root-mean-square error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   Kök-ortalama-kare hatası, modelin yanıtı ne kadar doğru tahmin ettirdiğini iyi bir ölçüdür. Sonuçlardan, genellikle $2,00 içinde, veri kümesinin özelliklerinden taksi Fareli tahmine dayalı olarak, modelin oldukça iyi olduğunu görürsünüz.

1. Ortalama-mutlak-yüzde hatasını hesaplamak için aşağıdaki kodu çalıştırın. Bu ölçüm, hatanın yüzdesi olarak doğruluğu ifade eder. Bu, tahmin edilen ve gerçek değerler arasındaki mutlak bir farkı hesaplayarak ve tüm farkları toplayarak yapar. Ardından, bu toplamı gerçek değerlerin toplamının yüzdesi olarak ifade eder.

   ```python
   # Calculate mean-absolute-percent error and model accuracy 
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

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   İki tahmin doğruluk ölçümlerinden, modelin, veri kümesinin özelliklerinden vergilenme Fareli tahminlerinde oldukça iyi olduğunu görürsünüz. 

1. Doğrusal regresyon modelini gönderdikten sonra, modelin verilere ne kadar iyi uyduğunu belirlemeniz gerekir. Bunu yapmak için gerçek tarifeli havayolu değerlerini tahmin edilen çıkışa göre çizdirebilirsiniz. Ayrıca, verilerin sığdırılmış regresyon satırına ne kadar yakın olduğunu anlamak için R-kare ölçüsünü hesaplayabilirsiniz.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score by using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the actual versus predicted fare amount values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amount R^2={}".format(r2))
   plt.show()

   ```
   ![Regresyon grafiğinin ekran görüntüsü.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Sonuçlardan, fark yüzdesinin yüzde 95 ' luk için R-kare ölçü hesaplarının olduğunu görebilirsiniz. Bu aynı zamanda, gözlemlenen çizim ile gerçek çizim tarafından da onaylanır. Regresyon modeli hesaplarının için ne kadar çok fark olursa, veri noktalarının daha yakın olması, sığdırılmış regresyon satırına girer.  

## <a name="register-the-model-to-azure-machine-learning"></a>Modeli Azure Machine Learning için kaydedin
En iyi modelinizi doğruladıktan sonra, Azure Machine Learning kaydedebilirsiniz. Ardından, kayıtlı modeli indirebilir veya dağıtabilir ve kaydettiğiniz tüm dosyaları alabilirsiniz.

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Sonuçları Azure Machine Learning görüntüle
Ayrıca, Azure Machine Learning çalışma alanınızdaki deneye giderek yinelemelerin sonuçlarına erişebilirsiniz. Burada, çalıştırma, deneme modellerinin ve diğer model ölçümlerinizin durumu hakkında daha fazla bilgi edinebilirsiniz. 

![Azure Machine Learning çalışma alanının ekran görüntüsü.](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Synapse Analytics](../index.yml)
- [Öğretici: Apache Spark MLlib ve Azure SYNAPSE Analytics ile Machine Learning uygulaması derleme](./apache-spark-machine-learning-mllib-notebook.md)
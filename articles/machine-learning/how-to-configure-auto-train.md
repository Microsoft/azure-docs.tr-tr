---
title: Otomatik ML denemeleri oluşturma
titleSuffix: Azure Machine Learning
description: Otomatik makine öğrenimi denemeleri için veri kaynakları, hesaplar ve yapılandırma ayarlarını tanımlama hakkında bilgi edinin.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: d0a15b16c04a28bcc67caeeceedfcbad485b7157
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861472"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python’da otomatik ML denemelerini yapılandırma


Bu kılavuzda, [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)ile otomatik makine öğrenimi denemeleri 'in çeşitli yapılandırma ayarlarını nasıl tanımlayacağınızı öğrenin. Otomatik makine öğrenimi, sizin için bir algoritma ve hiper parametreler seçer ve dağıtım için hazırlanın bir model oluşturur. Otomatik makine öğrenimi denemeleri yapılandırmak için kullanabileceğiniz çeşitli seçenekler vardır.

Otomatik makine öğrenimi denemesinin uçtan uca bir örneği için bkz. [öğretici: sınıflandırma modelini otomatik makine öğrenimiyle eğitme](tutorial-auto-train-models.md).

Otomatik makine öğreniminde kullanılabilen yapılandırma seçenekleri:

* Deneme türünü seçin: sınıflandırma, regresyon veya zaman serisi tahmin
* Veri kaynağı, biçimler ve veri getirme
* İşlem hedefini seçin: yerel veya uzak
* Otomatik makine öğrenimi deneme ayarları
* Otomatik makine öğrenmesi denemesi çalıştırma
* Model ölçümlerini keşfet
* Modeli kaydetme ve dağıtma

Kod deneyimini tercih ediyorsanız, [Azure Machine Learning Studio 'da otomatik makine öğrenimi denemeleri de oluşturabilirsiniz](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Önkoşullar

İhtiyacınız olan bu makalede, 
* Azure Machine Learning çalışma alanı. Çalışma alanını oluşturmak için, bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

* Azure Machine Learning Python SDK 'Sı yüklendi.
    SDK 'yı yüklemek için şunlardan birini yapabilirsiniz 
    * SDK 'Yı otomatik olarak yüklediği ve ML iş akışları için önceden yapılandırılmış bir işlem örneği oluşturun. Daha fazla bilgi için bkz. [Azure Machine Learning işlem örneği oluşturma ve yönetme](how-to-create-manage-compute-instance.md) . 

    * SDK 'nın [varsayılan yüklemesini](/python/api/overview/azure/ml/install#default-install) içeren [ `automl` paketi kendiniz yüklemeniz](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)gerekir.
    
    > [!WARNING]
    > Python 3,8 ile uyumlu değildir `automl` . 

## <a name="select-your-experiment-type"></a>Deneme türünüzü seçme

Denemenize başlamadan önce, çözmeyle ilgili makine öğrenimi sorunu türünü belirlemelisiniz. Otomatik makine öğrenimi,, ve görev türlerini destekler `classification` `regression` `forecasting` . [Görev türleri](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)hakkında daha fazla bilgi edinin.

Aşağıdaki kod, `task` `AutoMLConfig` deneme türünü olarak belirtmek için oluşturucuda parametresini kullanır `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Veri kaynağı ve biçimi

Otomatik makine öğrenmesi yerel masaüstünüzde veya Azure Blob Depolama gibi bulutta duran verilerinizi destekler. Veriler bir **Pandas DataFrame** veya **Azure Machine Learning TabularDataset** içinde okunabilir. [Veri kümeleri hakkında daha fazla bilgi edinin](how-to-create-register-datasets.md).

Machine Learning 'de Eğitim verileri için gereksinimler:
- Verilerin tablolu biçimde olması gerekir.
- Tahmin edilecek değer, hedef sütun, verilerde olmalıdır.

**Uzak denemeleri için** eğitim verilerine uzaktan işlem üzerinden erişilebilir olması gerekir. AutoML uzak işlem üzerinde çalışırken yalnızca [Azure Machine Learning TabularDatasets](/python/api/azureml-core/azureml.data.tabulardataset) kabul eder. 

Azure Machine Learning veri kümeleri şu işlevleri kullanıma sunar:

* Statik dosyalardan veya URL kaynaklarından verileri çalışma alanınıza kolayca aktarın.
* Verilerinizin bulut bilişim kaynaklarında çalıştırılan eğitim betikleri tarafından kullanılabilmesini sağlama. Verileri uzaktan işlem hedeflerinize bağlamak için sınıfını kullanma örneği için bkz. veri [kümeleriyle eğitme](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) `Dataset` .

Aşağıdaki kod, bir Web URL 'sinden TabularDataset oluşturur. Bkz. yerel dosyalar ve veri depoları gibi diğer kaynaklardan veri kümeleri oluşturma hakkında kod örnekleri için [Tabulardataset oluşturma](how-to-create-register-datasets.md#create-a-tabulardataset) .

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Yerel işlem denemeleri için**, daha hızlı işleme süreleri için Pandas dataframes önerilir.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Eğitim, doğrulama ve test verileri

Doğrudan oluşturucuda ayrı **eğitim verileri ve doğrulama veri kümeleri** belirtebilirsiniz `AutoMLConfig` . [Data bölmelerini yapılandırma ve](how-to-configure-cross-validation-data-splits.md) oto ml denemeleri için çapraz doğrulama hakkında daha fazla bilgi edinin. 

Açıkça bir `validation_data` veya `n_cross_validation` parametresi belirtmezseniz, otomatik ml doğrulamanın nasıl gerçekleştirileceğini belirlemek için varsayılan teknikler uygular. Bu belirleme, parametreye atanan veri kümesindeki satır sayısına bağlıdır `training_data` . 

|Eğitim &nbsp; veri &nbsp; boyutu| Doğrulama tekniği |
|---|-----|
|**&nbsp; &nbsp; 20.000 &nbsp; satırdan büyük**| Eğitme/doğrulama verileri bölünmesi uygulandı. Varsayılan değer, doğrulama kümesi olarak ilk eğitim verileri kümesinin %10 ' un sürme sayısıdır. Buna karşılık, bu doğrulama kümesi ölçüm hesaplamaları için kullanılır.
|**&nbsp; &nbsp; 20.000 &nbsp; satırdan küçük**| Çapraz doğrulama yaklaşımı uygulanır. Varsayılan katların sayısı satır sayısına bağlıdır. <br> **Veri kümesi 1.000 satırdan azsa**, 10 katlar kullanılır. <br> **Satırlar 1.000 ve 20.000 arasındaysa**, üç katlar kullanılır.

Şu anda, model değerlendirmesi için kendi **Test verilerinizi** sağlamanız gerekir. Model değerlendirmesi için kendi test verilerinizi getirme kodu örneği için, [Bu Jupyter Not defteri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)'nin **Test** bölümüne bakın.

## <a name="compute-to-run-experiment"></a>Deneme çalıştırmak için işlem

Ardından, modelin eğitilme yeri belirlenir. Otomatik makine öğrenmesi eğitim denemesi aşağıdaki işlem seçenekleriyle çalıştırılabilir. [Yerel ve uzak işlem seçeneklerinin avantajlarını ve dezavantajlarını](concept-automated-ml.md#local-remote) öğrenin. 

* Yerel Masaüstü veya dizüstü bilgisayar gibi **Yerel** makineniz, genellikle küçük bir veri kümeniz olduğunda ve araştırma aşamasında olmaya devam ediyorsa. Yerel işlem örneği için [bu not defterine](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) bakın. 
 
* Bulutta bulunan bir **uzak** makine – [Azure Machine Learning yönetilen işlem](concept-compute-target.md#amlcompute) , Azure sanal makinelerinin kümelerinde makine öğrenimi modellerini eğitme olanağı sağlayan bir yönetilen hizmettir. 

    Uzaktan Azure Machine Learning Yönetilen İşlem hizmetini kullanma örneği için [bu not defterine](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) bakın. 

* Azure aboneliğinizdeki bir **Azure Databricks kümesi** . [OTOMATIK ml için Azure Databricks kümesi ayarlama](how-to-configure-databricks-automl-environment.md)bölümünde daha fazla ayrıntı bulabilirsiniz. Azure Databricks bulunan not defteri örnekleri için bu [GitHub sitesine](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) bakın.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Deneme ayarlarınızı yapılandırın

Otomatik makine öğrenimi denemenizi yapılandırmak için kullanabileceğiniz çeşitli seçenekler vardır. Bu parametreler bir nesne örneği oluşturarak ayarlanır `AutoMLConfig` . Parametrelerin tam listesi için bkz. [oto Mlconfig sınıfı](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Bazı örnekler:

1. Deneme zaman aşımı dakikalarını 30 dakika ve 2 çapraz doğrulama katlarından oluşan birincil ölçüm olarak AUC ağırlıklı kullanarak yeniden sınıflandırın.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. Aşağıdaki örnek, beş doğrulama yük katlarından sonra 60 dakika sonra biten bir gerileme deneimiyle ayarlanmıştır.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Tahmin görevleri ek kurulum gerektirir, daha fazla ayrıntı için [zaman serisi tahmin modelini oto eğitme](how-to-auto-train-forecast.md) makalesine bakın. 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Desteklenen modeller

Otomatik makine öğrenimi, otomasyon ve ayarlama işlemi sırasında farklı modeller ve algoritmalar gerçekleştirmeye çalışır. Bir kullanıcı olarak, algoritmayı belirtmeniz gerekmez. 

Üç farklı `task` parametre değeri, uygulanacak algoritmaların veya modellerin listesini belirlenir. `allowed_models` `blocked_models` Dahil etmek veya hariç tutmak için kullanılabilir modellerle Yinelemeleri değiştirmek için veya parametrelerini kullanın. 

Aşağıdaki tabloda görev türüne göre desteklenen modeller özetlenmektedir. 

> [!NOTE]
> Auto ML tarafından oluşturulan modellerinizi bir [onnx modeline](concept-onnx.md)dışarı aktarmayı planlıyorsanız, yalnızca bir * ile gösterilen algoritmalar onnx biçimine dönüştürülebiliyor. [Modelleri ONNX 'e dönüştürme](concept-automated-ml.md#use-with-onnx)hakkında daha fazla bilgi edinin. <br> <br> Ayrıca, ONNX Şu anda yalnızca sınıflandırma ve gerileme görevlerini destekler. 

Sınıflandırma | Regresyon | Zaman Serileri Tahmini
|-- |-- |--
[Lojistik regresyon](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastik ağ](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastik Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Hafif GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Hafif GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Hafif GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradyan artırma](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Gradyan artırma](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradyan artırma](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Karar ağacı](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Karar ağacı](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Karar ağacı](https://scikit-learn.org/stable/modules/tree.html#regression)
[K en yakın komşuları](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K en yakın komşuları](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K en yakın komşuları](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Doğrusal SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[SÜTUNLAR kement](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Destek vektör sınıflandırması (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastic gradyan (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastic gradyan (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Rastgele orman](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Rastgele orman](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Rastgele Orman](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Son derece rastgele ağaçlar](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Son derece rastgele ağaçlar](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Son derece rastgele ağaçlar](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Ortalama Perceptron sınıflandırıcı](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Çevrimiçi gradyan açıklama gerileme](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Otomatik ARıMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Hızlı doğrusal gerileme sor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastic gradyan (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||Forekaletcn
|[Doğrusal SVM Sınıflandırıcısı](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Birincil ölçüm
`primary metric`Parametresi, iyileştirme için model eğitimi sırasında kullanılacak ölçümü belirler. Seçebileceğiniz kullanılabilir ölçümler, seçtiğiniz görev türüne göre belirlenir ve aşağıdaki tabloda her bir görev türü için geçerli birincil ölçümler gösterilmektedir.

Otomatik makine öğrenimi için en iyileştirmek üzere bir birincil ölçüm seçilmesi birçok faktöre bağlıdır. Birincil düşünmeniz, iş gereksinimlerinizi en iyi şekilde temsil eden bir ölçüm seçmeniz önerilir. Daha sonra ölçümün veri kümesi profiliniz için uygun olup olmadığını (veri boyutu, Aralık, sınıf dağıtımı vb.) göz önünde bulundurun.

[Otomatik makine öğrenimi sonuçlarını anlamak](how-to-understand-automated-ml.md)için bu ölçümlerin belirli tanımları hakkında bilgi edinin.

|Sınıflandırma | Regresyon | Zaman Serileri Tahmini
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Sınıflandırma senaryoları için birincil ölçümler 

,, Ve gibi eşik değerleri, `accuracy` `average_precision_score_weighted` `norm_macro_recall` `precision_score_weighted` çok büyük sınıf eğriliği (sınıf dengesizliği) olan veya beklenen ölçüm değeri 0,0 veya 1,0 ' e yakın olan veri kümelerinde de iyileştiremeyebilir. Bu durumlarda, `AUC_weighted` birincil ölçüm için daha iyi bir seçenek olabilir. Otomatik makine öğrenimini tamamladıktan sonra, iş gereksinimlerinize en uygun ölçüm temelinde kazanan modeli seçebilirsiniz.

| Metric | Örnek kullanım örneği |
| ------ | ------- |
| `accuracy` | Görüntü sınıflandırması, yaklaşım analizi, dalgalanma tahmini |
| `AUC_weighted` | Sahtekarlık algılama, görüntü sınıflandırması, anomali algılama/istenmeyen posta algılama |
| `average_precision_score_weighted` | Yaklaşım analizi |
| `norm_macro_recall` | Dalgalanma tahmini |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Gerileme senaryoları için birincil ölçümler

`r2_score`Ve `spearman_correlation` değerlerini tahmin edilecek şekilde ölçeklendirmek çok sayıda büyüklük halinde olduğunda, ve gibi ölçümler, modelin kalitesini daha iyi temsil edebilir. Örnek maaş tahmini için, birçok kişinin ücret $20K ile $100k arasında bir ücret ödemesine karşın, ölçek, $100M aralığındaki bazı ücretler ile çok daha yüksek bir konuma gider. 

`normalized_mean_absolute_error``normalized_root_mean_squared_error`Bu durumda, bir çalışan için $20K tahmin hatası, çalışan olarak $30k maaş olan bir çalışan için aynı şekilde davranır. Gerçekte, bir $20M maaşından yalnızca $20K ' ı tahmin etmek çok yakındır (küçük% 0,1 göreli bir fark), $30k 'den $20K kapalı değildir (büyük %67 göreli bir fark). `normalized_mean_absolute_error` ve `normalized_root_mean_squared_error` tahmin edilecek değerler benzer bir ölçekte olduğunda faydalıdır.

| Metric | Örnek kullanım örneği |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Fiyat tahmini (şirket/ürün/ipucu), gözden geçirme puan tahmini |
| `r2_score` | Airline gecikmesi, maaş tahmini, hata çözümleme süresi |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Zaman serisi tahmin senaryolarına yönelik birincil ölçümler

Yukarıdaki regresyon notlarına bakın.

| Metric | Örnek kullanım örneği |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Fiyat tahmini (tahmin), stok iyileştirmesi, talep tahmini |
| `r2_score` | Fiyat tahmini (tahmin), stok iyileştirmesi, talep tahmini |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Veri korturlama

Her otomatik makine öğrenimi denemesinde, verileriniz, farklı ölçeklerde bulunan özelliklerle hassas olan *belirli* algoritmalara yardımcı olacak şekilde otomatik olarak ölçeklendirilir ve normalleştirilir. Bu ölçeklendirme ve normalleştirme, korleştirme olarak adlandırılır. Daha ayrıntılı bilgi ve kod örnekleri için bkz. [oto ml 'de Fealeştirme](how-to-configure-auto-features.md#) . 

Denemeleri 'nizi yapılandırırken `AutoMLConfig` , ayarı etkinleştirebilir/devre dışı bırakabilirsiniz `featurization` . Aşağıdaki tabloda, [oto Mlconfig nesnesinde](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)fealeştirme için kabul edilen ayarlar gösterilmektedir. 

|Korleştirme yapılandırması | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| Ön işleme 'nin bir parçası olarak, [veri guardı ve korleştirme adımlarının](how-to-configure-auto-features.md#featurization) otomatik olarak gerçekleştirileceğini belirtir. **Varsayılan ayar**.|
|`"featurization": 'off'`| Korleştirme adımının otomatik olarak yapılmaması gerektiğini gösterir.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Özelleştirilmiş basamak kullanılması gerektiğini gösterir. [Korleştirme özelleştirmeyi öğrenin](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Otomatik makine öğrenimi adımları (özellik normalleştirme, eksik verileri işleme, metni sayısal olarak dönüştürme, vb.) temel modelin bir parçası haline gelir. Tahmin için model kullanılırken, eğitim sırasında uygulanan aynı özellik adımları, giriş verilerinize otomatik olarak uygulanır.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Sorgu yapılandırma

En son çalışan modeller varsayılan olarak etkindir ve bir oto ml çalıştırmasında son çalıştırma yinelemeleri olarak görünür. Şu anda **Votingensebir** ve **stackensebir** , desteklenir. 

Oylama, ağırlıklı ortalamaları kullanan geçici oylama uygular. Yığın uygulama, birinci katmanın oylama ile aynı modellere sahip olduğu iki katmanlı bir uygulama kullanır ve ikinci katman modeli, ilk katmandaki modellerin en iyi birleşimini bulmak için kullanılır. 

ONNX modellerini kullanıyorsanız **veya** model-explainability etkinse, yığınlama devre dışıdır ve yalnızca oylama kullanılır.

Ensemine yönelik eğitim, `enable_voting_ensemble` ve Boole parametreleri kullanılarak devre dışı bırakılabilir `enable_stack_ensemble` .

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Varsayılan ensebirlikte kaydetme davranışını değiştirmek için, `kwargs` bir nesnesinde olarak sağlanbir çoklu varsayılan bağımsız değişken vardır `AutoMLConfig` .

> [!IMPORTANT]
>  Aşağıdaki parametreler, oto Mlconfig sınıfının açık parametreleri değildir. 

* `ensemble_download_models_timeout_sec`: **Votingensebir** ve **stackensebir** model oluşturma sırasında, önceki alt çalıştırmaların birden çok monte edilen modeli indirilir. Bu hatayla karşılaşırsanız `AutoMLEnsembleException: Could not find any models for running ensembling` , modellerin indirilmesi için daha fazla zaman girmeniz gerekebilir. Bu modellerin paralel olarak indirilmesi için varsayılan değer 300 saniyedir ve en fazla zaman aşımı sınırı yoktur. Daha fazla zaman gerekliyse bu parametreyi 300 saniyeden daha yüksek bir değerle yapılandırın. 

  > [!NOTE]
  >  Zaman aşımına ulaşılırsa ve indirilen modeller varsa, ensembling indirildiği modellerle devam eder. Tüm modellerin bu zaman aşımı süresi içinde sona ermesini sağlamak zorunda değildir.

Aşağıdaki parametreler yalnızca **Stackensesıgrafik** modelleri için geçerlidir: 

* `stack_meta_learner_type`: meta-Learner, tek tek heterojen modellerin çıktısı üzerinde eğitilen bir modeldir. Varsayılan meta öğrenenler, `LogisticRegression` Sınıflandırma görevlerine (veya `LogisticRegressionCV` çapraz doğrulamanın etkin olması) ve `ElasticNet` gerileme/tahmin görevlerine (veya `ElasticNetCV` çapraz doğrulamanın etkin olması halinde) yöneliktir. Bu parametre şu dizelerden biri olabilir: `LogisticRegression` , `LogisticRegressionCV` ,, `LightGBMClassifier` `ElasticNet` , `ElasticNetCV` , `LightGBMRegressor` , veya `LinearRegression` .

* `stack_meta_learner_train_percentage`: meta-Learner eğitimi için ayrılacak olan eğitim kümesi oranını belirtir (eğitim ve doğrulama türünü seçerken). Varsayılan değer `0.2` olarak belirlenmiştir. 

* `stack_meta_learner_kwargs`: meta-Learner başlatıcısına geçirilecek isteğe bağlı parametreler. Bu parametreler ve parametre türleri, karşılık gelen model oluşturucusundan parametreleri ve parametre türlerini yansıtır ve model oluşturucusuna iletilir.

Aşağıdaki kod, bir nesnesinde özel bir ensebir davranışını belirtmenin bir örneğini gösterir `AutoMLConfig` .

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Çıkış kriterleri

Denemenizin sona erdirmek için, Cmlconfig ' de tanımlayabileceğiniz birkaç seçenek vardır.

|Ölçütler| açıklama
|----|----
&nbsp;Ölçüt yok | Herhangi bir çıkış parametresi tanımlamadıysanız, deneme, birincil ölçümünüzün başka bir işlem yapılmayacağı sürece devam eder.
&nbsp;Sürenin sonunda &nbsp; &nbsp; &nbsp;| `experiment_timeout_minutes`Dakika cinsinden, denemenizin ne kadar süreyle çalışmaya devam etmesi gerektiğini tanımlamak için ayarlarınızda kullanın. <br><br> Deneme zaman aşımı başarısızlıklarını önlemeye yardımcı olmak için en az 15 dakika, ya da sütunlarınızın sütun boyutu 10.000.000 ' i aşarsa 60 dakika olur.
Bir &nbsp; puana &nbsp; &nbsp; &nbsp; ulaşıldı| Kullanım `experiment_exit_score` , belirtilen birincil ölçüm puanına ulaşıldıktan sonra denemeyi tamamlar.

## <a name="run-experiment"></a>Deneme Çalıştır

Otomatik ML için, `Experiment` `Workspace` denemeleri çalıştırmak için kullanılan bir adlandırılmış nesne olan bir nesnesi oluşturursunuz.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Çalıştırmak ve model oluşturmak için denemeyi gönderin. `AutoMLConfig` `submit` Modeli oluşturmak için yöntemini geçirin.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Bağımlılıklar önce yeni bir makineye yüklenir.  Çıktının gösterilmesi 10 dakika kadar sürebilir.
>`show_output` `True` Konsolunda gösterilen çıkışın sonucu olarak ayarlanıyor.

### <a name="multiple-child-runs-on-clusters"></a>Kümeler üzerinde birden çok alt çalışma

Otomatik ML denemesi alt çalıştırmaları, zaten başka bir deneme çalıştıran bir kümede gerçekleştirilebilir. Ancak, zamanlama kümenin kaç düğüme sahip olduğuna ve bu düğümlerin farklı bir deneme çalıştırmak için kullanılabilir olup olmadığına bağlıdır.

Kümedeki her düğüm tek bir eğitim çalıştırmasını gerçekleştirebilen tek bir sanal makine (VM) görevi görür; Otomatik ML için bu bir alt çalışma anlamına gelir. Tüm düğümler meşgulse, yeni deneme sıraya alınır. Ancak, ücretsiz düğümler varsa, yeni deneme otomatik ML alt çalıştırmalarını kullanılabilir düğümlerde/VM 'lerde paralel olarak çalıştırır.

Alt çalıştırmaların yönetilmesine yardımcı olmak ve bunların gerçekleştirilebileceği durumlarda, deneme başına adanmış bir küme oluşturmanızı ve `max_concurrent_iterations` denemenizin sayısını kümedeki düğüm sayısına göre eşleşmeyi öneririz. Bu şekilde, tüm küme düğümlerini aynı anda istediğiniz eşzamanlı alt çalıştırma/yineleme sayısıyla birlikte kullanırsınız.

`max_concurrent_iterations`Nesneniz içinde yapılandırın `AutoMLConfig` . Yapılandırılmazsa, deneme başına yalnızca bir eşzamanlı alt çalışma/yinelemeye izin verilir.  

## <a name="explore-models-and-metrics"></a>Modelleri ve ölçümleri keşfet

Otomatikleştirilmiş ML, eğitim sonuçlarınızı izlemenize ve değerlendirmenize yönelik seçenekler sunar. 

* Bir not defteriniz varsa, eğitim sonuçlarınızı bir pencere öğesinde veya satır içi olarak görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [otomatik makine öğrenimi çalıştırmalarını izleme](#monitor) .

* Her çalıştırma için belirtilen performans grafiklerine ve ölçümlere ilişkin tanımlar ve örnekler için bkz. [otomatik makine öğrenme deneme sonuçlarını değerlendir](how-to-understand-automated-ml.md) . 

* Bir Özet Özeti almak ve belirli bir modele hangi özelliklerin eklendiğini anlamak için bkz. uygun [Saydamlık](how-to-configure-auto-features.md#featurization-transparency). 

Hiper parametreleri, ölçeklendirme ve normalleştirme tekniklerini ve belirli bir otomatik ML 'ye uygulanan algoritmayı aşağıdaki özel kod çözümüyle birlikte çalıştırabilirsiniz. 

Aşağıdaki, `print_model()` OTOMATIK ml eğitim işlem hattının her bir adımının hiper parametrelerini yazdıran özel yöntemini tanımlar.
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

Yeni gönderilen ve aynı deneme Not Defteri içinden eğitilen bir yerel veya uzak çalıştırma için, yöntemini kullanarak en iyi modeli geçirebilirsiniz `get_output()` . 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

Aşağıdaki çıkış şunları gösterir:
 
* Standartscalerwrapper tekniği, eğitimin öncesindeki verileri ölçeklendirmek ve normalleştirmek için kullanılmıştır.

* Xgboostsınıflandırıcı algoritması en iyi çalıştırma olarak tanımlanmıştır ve ayrıca hiper parametre değerlerini de gösterir. 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

Çalışma alanınızdaki farklı bir deneyden mevcut bir çalıştırma için, araştırmak istediğiniz belirli çalıştırma KIMLIĞINI edinin ve `print_model()` yönteme geçirin. 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```
> [!NOTE]
> Otomatik ML algoritmaları, önerilen bir modelin nihai ölçüm puanından, doğruluk gibi hafif çeşitçine neden olabilecek, rastgele bir açıklık elde ediyor. Otomatikleştirilmiş ML, gerektiğinde tren-test Split, tren-doğrulama bölme veya çapraz doğrulama gibi veriler üzerinde işlemler de gerçekleştirir. Bu nedenle, aynı yapılandırma ayarları ve birincil ölçüm ile bir denemeyi birden çok kez çalıştırırsanız, bu faktörlere bağlı olarak her bir denemeleri son ölçüm puanı için çeşitleme görürsünüz. 

## <a name="monitor-automated-machine-learning-runs"></a><a name="monitor"></a> Otomatik makine öğrenimi çalıştırmalarını izleyin

Otomatik makine öğrenimi çalıştırmaları için, önceki bir çalıştırmalardan grafiklere erişmek üzere `<<experiment_name>>` uygun deneme adıyla değiştirin:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Otomatik Machine Learning için Jupyter Not defteri pencere öğesi](./media/how-to-configure-auto-train/azure-machine-learning-auto-ml-widget.png)

## <a name="register-and-deploy-models"></a>Modelleri kaydetme ve dağıtma

Bir modeli kaydedebilirsiniz, böylece daha sonra kullanmak üzere buna geri dönebilirsiniz. 

Bir modeli otomatik ML çalıştırağından kaydetmek için [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) yöntemini kullanın. 

```Python

best_run, fitted_model = run.get_output()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = remote_run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


Bir dağıtım yapılandırması oluşturma ve bir Web hizmetine kayıtlı bir model dağıtma hakkında ayrıntılı bilgi için bkz. [bir modelin nasıl ve ne şekilde dağıtılacağı](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration).

> [!TIP]
> Kayıtlı modeller için, tek tıklamayla dağıtım [Azure Machine Learning Studio](https://ml.azure.com)aracılığıyla kullanılabilir. Bkz. [Studio 'dan kayıtlı modelleri dağıtma](how-to-use-automated-ml-for-ml-models.md#deploy-your-model). 
<a name="explain"></a>

## <a name="model-interpretability"></a>Model yorumlanabilirliği

Model yorumlamalar, modellerinizin tahmin yaptığını ve temel alınan özellik önem değerlerini anlamanıza olanak tanır. SDK, yerel ve dağıtılmış modeller için hem eğitim hem de çıkarım sırasında model yorumsıya özellikleri etkinleştirmek için çeşitli paketler içerir.

Özellikle otomatik makine öğrenimi ['nin içindeki](how-to-machine-learning-interpretability-automl.md) yorumlu özelliklerin nasıl etkinleştirileceği hakkında kod örneklerine bakın.

Model açıklamalarının ve özelliklerinin önem derecesine ilişkin genel bilgiler için otomatik makine öğrenimi dışında SDK 'nın diğer alanlarında nasıl etkinleştirilecekleri hakkında genel bilgi edinmek için bkz. yorumda bulunan [kavram](how-to-machine-learning-interpretability.md) makalesi.

> [!NOTE]
> Forekaletcn modeli şu anda açıklama Istemcisi tarafından desteklenmiyor. Bu model, en iyi model olarak döndürülürse bir açıklama panosu döndürmez ve isteğe bağlı açıklama çalıştırmalarını desteklemez.

## <a name="next-steps"></a>Sonraki adımlar

+ [Bir modelin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md)hakkında daha fazla bilgi edinin.

+ [Otomatik makine öğrenimi ile regresyon modelini eğitme](tutorial-auto-train-models.md)hakkında daha fazla bilgi edinin.

+ [Birçok model çözümü hızlandırıcısında](https://aka.ms/many-models), oto ml ile birden çok modeli eğitme hakkında bilgi edinin.

+ [OTOMATIK ml denemeleri sorunlarını giderin](how-to-troubleshoot-auto-ml.md). 

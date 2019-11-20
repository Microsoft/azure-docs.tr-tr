---
title: Otomatik ML denemeleri oluşturma
titleSuffix: Azure Machine Learning
description: Otomatik makine öğrenimi sizin için bir algoritma seçer ve dağıtım için hazırlanma bir model oluşturur. Otomatik makine öğrenimi denemeleri yapılandırmak için kullanabileceğiniz seçenekleri öğrenin.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: c70226ef58ed60a7be556b88366953796ed6fff1
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580573"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python 'da otomatik ML denemeleri yapılandırma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu kılavuzda, [Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ile otomatik makine öğrenimi denemeleri 'in çeşitli yapılandırma ayarlarını nasıl tanımlayacağınızı öğrenin. Otomatik makine öğrenimi, sizin için bir algoritma ve hiper parametreler seçer ve dağıtım için hazırlanın bir model oluşturur. Otomatik makine öğrenimi denemeleri yapılandırmak için kullanabileceğiniz çeşitli seçenekler vardır.

Otomatik makine öğrenimi denemeleri örneklerini görüntülemek için bkz. [öğretici: bir sınıflandırma modelini otomatik makine öğrenimi Ile eğitme](tutorial-auto-train-models.md) veya [bulutta otomatik makine öğrenimi ile modeller eğitme](how-to-auto-train-remote.md).

Otomatik makine öğreniminde kullanılabilen yapılandırma seçenekleri:

* Deneme türünü seçin: sınıflandırma, gerileme veya zaman serisi tahmin
* Veri kaynağı, biçimler ve veri getirme
* İşlem hedefini seçin: yerel veya uzak
* Otomatik makine öğrenimi deneme ayarları
* Otomatik makine öğrenimi denemesinin çalıştırın
* Model ölçümlerini keşfet
* Modeli kaydetme ve dağıtma

Kod deneyimini tercih ediyorsanız, [Azure Machine Learning Studio 'da otomatik makine öğrenimi denemeleri de oluşturabilirsiniz](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Deneme türünü seçin

Denemenize başlamadan önce, çözmeyle ilgili makine öğrenimi sorunu türünü belirlemelisiniz. Otomatik makine öğrenimi, sınıflandırma, gerileme ve tahmin görev türlerini destekler. [Görev türleri](how-to-define-task-type.md)hakkında daha fazla bilgi edinin.

Otomatik makine öğrenimi, otomasyon ve ayarlama işlemi sırasında aşağıdaki algoritmaları destekler. Bir kullanıcı olarak, algoritmayı belirtmeniz gerekmez.

Sınıflandırma | Regresyon | Zaman serisi tahmin
|-- |-- |--
[Lojistik regresyon](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastik ağ](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastik ağ](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Hafif GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Hafif GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Hafif GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradyan artırma](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradyan artırma](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradyan artırma](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Karar ağacı](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Karar ağacı](https://scikit-learn.org/stable/modules/tree.html#regression)|[Karar ağacı](https://scikit-learn.org/stable/modules/tree.html#regression)
[K en yakın komşuları](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K en yakın komşuları](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K en yakın komşuları](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Doğrusal SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[SÜTUNLAR kement](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[SÜTUNLAR kement](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Destek vektör sınıflandırması (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastic gradyan (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastic gradyan (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Rastgele orman](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Rastgele orman](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Rastgele orman](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Son derece rastgele ağaçlar](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Son derece rastgele ağaçlar](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Son derece rastgele ağaçlar](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN sınıflandırıcı](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN gerileme](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN gerileme](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN doğrusal sınıflandırıcı](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Doğrusal gerileme](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Doğrusal gerileme](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Hızlı doğrusal gerileme sor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Otomatik ARıMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stochastic gradyan (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Çevrimiçi gradyan açıklama gerileme](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Ortalama Perceptron sınıflandırıcı](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||Forekaletcn
|[Doğrusal SVM Sınıflandırıcısı](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Deneme türünü belirtmek için `AutoMLConfig` oluşturucusunda `task` parametresini kullanın.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Veri kaynağı ve biçimi

Otomatik makine öğrenimi, yerel masaüstünüzde veya Azure Blob depolama gibi bulutta bulunan verileri destekler. Veriler bir **Pandas DataFrame** veya **Azure Machine Learning TabularDataset**içinde okunabilir.  [Veri kümeleri hakkında daha fazla bilgi edinin](https://github.com/MicrosoftDocs/azure-docs-pr/pull/how-to-create-register-datasets.md).

Eğitim verileri için gereksinimler:
- Verilerin tablolu biçimde olması gerekir.
- Tahmin edilecek değer, hedef sütun, verilerde olmalıdır.

Aşağıdaki kod örnekleri, verileri bu biçimlerde nasıl depolayabileceğinizi göstermektedir.

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  
  tabular_dataset = Dataset.Tabular.from_delimited_files("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv")
  train_dataset, test_dataset = tabular_dataset.random_split(percentage = 0.1, seed = 42)
  label = "Label"
  ```

* Pandas dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    train_data, test_data = train_test_split(df, test_size = 0.1, random_state = 42)
    label = "Label"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Uzaktan işlem üzerinde deneme çalıştırmak için veri getirme

Uzaktan yürütmeler için eğitim verilerine uzaktan işlem üzerinden erişilebilir olması gerekir. SDK 'daki [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) sınıfı işlevleri sunar:

* statik dosyalardan veya URL kaynaklarından verileri çalışma alanınıza kolayca aktarın
* bulut bilgi işlem kaynaklarında çalışırken verilerinizi eğitim betiklerine kullanılabilir hale getirme

İşlem Hedefinizdeki verileri bağlamak için `Dataset` sınıfını kullanma örneği için bkz. [nasıl yapılır](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) .

## <a name="train-and-validation-data"></a>Eğitim ve doğrulama verileri

Doğrudan `AutoMLConfig` oluşturucuda ayrı tren ve doğrulama kümeleri belirtebilirsiniz.

### <a name="k-folds-cross-validation"></a>K-Fold çapraz doğrulama

Çapraz doğrulama sayısını belirtmek için `n_cross_validations` ayarını kullanın. Eğitim veri kümesi, eşit büyüklükte `n_cross_validations` katlara rastgele olarak bölünür. Her bir çapraz doğrulama işleminde, katların biri, kalan katlara eğitilen modelin doğrulanması için kullanılacaktır. Bu işlem, her katlama doğrulama kümesi olarak bir kez kullanılana kadar `n_cross_validations` yinelenir. Tüm `n_cross_validations` yuvarlamalar için Ortalama puanlar raporlanır ve ilgili model tüm eğitim verileri kümesine geri alınacaktır.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo çapraz doğrulama (yinelenen rastgele alt örnekleme)

Doğrulama için kullanılması gereken eğitim veri kümesinin yüzdesini belirtmek için `validation_size` kullanın ve çapraz doğrulama sayısını belirtmek için `n_cross_validations` kullanın. Her çapraz doğrulama işleminde, kalan veriler üzerinde eğitilen modelin doğrulanması için `validation_size` boyut alt kümesi rastgele seçilir. Son olarak, tüm `n_cross_validations` yuvarlamalar için Ortalama puanlar raporlanır ve ilgili model tüm eğitim verileri kümesinde geri alınacaktır. Monte Carlo, zaman serisi tahmin için desteklenmez.

### <a name="custom-validation-dataset"></a>Özel doğrulama veri kümesi

Rastgele bölme kabul edilebilir değilse, genellikle zaman serisi verileri veya imledengeli veriler için özel doğrulama veri kümesi kullanın. Kendi doğrulama veri kümenizi belirtebilirsiniz. Model, rastgele veri kümesi yerine belirtilen doğrulama veri kümesine göre değerlendirilir.

## <a name="compute-to-run-experiment"></a>Deneme çalıştırmak için işlem

Ardından, modelin eğitilme yeri belirlenir. Otomatik makine öğrenimi eğitim denemesi aşağıdaki işlem seçeneklerinde çalıştırılabilir:
*   Yerel Masaüstü veya dizüstü bilgisayar gibi yerel makineniz, genellikle küçük veri kümeniz olduğunda ve araştırma aşamasında olmaya devam ediyorsa.
*   Bulutta bulunan bir uzak makine – [Azure Machine Learning yönetilen işlem](concept-compute-target.md#amlcompute) , Azure sanal makinelerinin kümelerinde makine öğrenimi modellerini eğitme olanağı sağlayan bir yönetilen hizmettir.

Yerel ve uzak işlem hedeflerine sahip Not defterleri için [GitHub sitesine](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) bakın.

*   Azure aboneliğinizdeki bir Azure Databricks kümesi. Burada daha fazla ayrıntı bulabilirsiniz- [OTOMATIK ml için Azure Databricks kümesi ayarlayın](how-to-configure-environment.md#azure-databricks)

Azure Databricks örnek Not defterleri için [GitHub sitesine](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) bakın.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Deneme ayarlarınızı yapılandırın

Otomatik makine öğrenimi denemenizi yapılandırmak için kullanabileceğiniz çeşitli seçenekler vardır. Bu parametreler bir `AutoMLConfig` nesnesi örneği oluşturarak ayarlanır. Parametrelerin tam listesi için bkz. [oto Mlconfig sınıfı](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) .

Bazı örnekler:

1.  Deneme zaman aşımı dakikalarını 30 dakika ve 2 çapraz doğrulama katlarından oluşan birincil ölçüm olarak AUC ağırlıklı kullanarak yeniden sınıflandırın.

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  5 doğrulama çapraz katlarından 60 dakika sonra sona erdirmek için bir gerileme denemesi örneği aşağıda verilmiştir.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

Üç farklı `task` parametre değeri (üçüncü görev türü `forecasting`ve `regression` görevlerle aynı algoritma havuzunu kullanır) uygulanacak modellerin listesini belirleme. Dahil edilecek veya hariç tutulacak mevcut modellerle yinelemeleri daha fazla değiştirmek için `whitelist` veya `blacklist` parametrelerini kullanın. Desteklenen modellerin listesi [Supportedmodeller sınıfında](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedmodels?view=azure-ml-py)bulunabilir.

### <a name="primary-metric"></a>Birincil ölçüm
Birincil ölçüm, iyileştirme için model eğitimi sırasında kullanılacak ölçümü belirler. Seçebileceğiniz kullanılabilir ölçümler, seçtiğiniz görev türüne göre belirlenir ve aşağıdaki tabloda her bir görev türü için geçerli birincil ölçümler gösterilmektedir.

|Sınıflandırma | Regresyon | Zaman serisi tahmin
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

[Otomatik makine öğrenimi sonuçlarını anlamak](how-to-understand-automated-ml.md)için bunların belirli tanımları hakkında bilgi edinin.

### <a name="data-preprocessing--featurization"></a>Veri ön işlemesi &

Her otomatik makine öğrenimi denemesinde, verileriniz, farklı ölçeklerde bulunan özelliklerle hassas olan *belirli* algoritmalara yardımcı olacak şekilde [otomatik olarak ölçeklendirilir ve normalleştirilir](concept-automated-ml.md#preprocess) .  Ancak, eksik değerler imputation, kodlama ve dönüşümler gibi ek ön işleme/korleştirme de etkinleştirebilirsiniz. [Nelerin dahil olduğu hakkında daha fazla bilgi edinin](how-to-create-portal-experiments.md#preprocess).

Bu özelliği etkinleştirmek için [`AutoMLConfig` sınıfı](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)için `"preprocess": True` belirtin.

> [!NOTE]
> Otomatik makine öğrenimi ön işleme adımları (özellik normalleştirme, eksik verileri işleme, metni sayısal olarak dönüştürme, vb.) temel modelin bir parçası haline gelir. Tahmin için model kullanılırken, eğitim sırasında uygulanan aynı ön işleme adımları, giriş verilerinize otomatik olarak uygulanır.

### <a name="time-series-forecasting"></a>Zaman serisi tahmin
Zaman serisi `forecasting` görevi yapılandırma nesnesinde ek parametreler gerektirir:

1. `time_column_name`: eğitim verilerinizde geçerli bir zaman serisi içeren sütunun adını tanımlayan gerekli parametre.
1. `max_horizon`: eğitim verilerinin dönemselliği temelinde tahmin etmek istediğiniz sürenin uzunluğunu tanımlar. Örneğin, günlük saat grasınlar ile eğitim verileriniz varsa, modelin ne kadar süreyle eğmesini istediğinizi tanımlarsınız.
1. `grain_column_names`: eğitim verilerinizde bireysel zaman serisi verileri içeren sütunların adını tanımlar. Örneğin, belirli bir markaların mağazasının satışlarını tahmin ediyorsanız, bir mağaza ve marka sütunları gren sütunları olarak tanımlarsınız. Her bir Gren/gruplama için ayrı zaman serisi ve tahminler oluşturulacaktır. 

Aşağıda kullanılan ayarların örnekleri için bkz. [örnek Not defteri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
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

### <a name="ensemble"></a>Sorgu yapılandırma

Enseletirme modelleri varsayılan olarak etkindir ve otomatik makine öğrenimi çalıştırmasında son çalıştırma yinelemeleri olarak görünür. Şu anda desteklenen ensesıya yöntemleri oylama ve yığınlama. Oylama, ağırlıklı ortalamalar kullanılarak geçici oylama olarak uygulanır ve yığınlama uygulamasının, birinci katmanın oylama ile aynı modellere sahip olduğu ve ikinci katman modelinin en iyi birleşimini bulmak için kullanıldığı 2 katmanlı bir uygulama kullanıyor. ilk katmandan modeller. ONNX modellerini kullanıyorsanız **veya** model-explainability etkinse, yığınlama devre dışı bırakılır ve yalnızca oylama kullanılacaktır.

Varsayılan yığın zenginme davranışını değiştirmek için bir `AutoMLConfig` nesnesinde `kwargs` olarak sağlanbir çoklu varsayılan bağımsız değişken vardır.

* `stack_meta_learner_type`: meta-Learner, bireysel heterojen modellerinin çıktısı üzerinde eğitilen bir modeldir. Varsayılan meta öğrenenler, Sınıflandırma görevleri için `LogisticRegression` (veya çapraz doğrulama etkinse `LogisticRegressionCV`) ve gerileme/tahmin görevleri için `ElasticNet` (veya çapraz doğrulama etkinse `ElasticNetCV`). Bu parametre şu dizelerden biri olabilir: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`veya `LinearRegression`.
* `stack_meta_learner_train_percentage`: meta-Learner eğitimi için ayrılacak eğitim kümesinin oranını belirtir (eğitim ve doğrulama türünü seçerken). Varsayılan değer `0.2`.
* `stack_meta_learner_kwargs`: meta-Learner başlatıcısına geçirilecek isteğe bağlı parametreler. Bu parametreler ve parametre türleri, ilgili model oluşturucusundan bunları yansıtır ve model oluşturucusuna iletilir.

Aşağıdaki kod, bir `AutoMLConfig` nesnesinde özel ensebölümlü davranışı belirtmeye ilişkin bir örnek gösterir.

```python
ensemble_settings = {
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

Ensebölümlü eğitim varsayılan olarak etkindir, ancak `enable_voting_ensemble` ve `enable_stack_ensemble` Boole parametreleri kullanılarak devre dışı bırakılabilir.

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

## <a name="run-experiment"></a>Deneme Çalıştır

Otomatik ML için, denemeleri çalıştırmak için kullanılan bir `Workspace` adlandırılmış nesne olan bir `Experiment` nesnesi oluşturursunuz.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Çalıştırmayı denemek ve bir model oluşturmak için denemeyi iletin. Modeli oluşturmak için `AutoMLConfig` `submit` yöntemine geçirin.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Bağımlılıklar önce yeni bir makineye yüklenir.  Çıktının gösterilmesi 10 dakika kadar sürebilir.
>`show_output` `True` olarak ayarlama, konsolda gösterilmekte olan sonuçlara neden olur.

### <a name="exit-criteria"></a>Çıkış kriterleri
Denemenizin sona erdirmek için tanımlayabileceğiniz birkaç seçenek vardır.
1. Ölçüt yok: herhangi bir çıkış parametresi tanımlamadıysanız, birincil ölçümünde başka bir ilerleme yapılıncaya kadar deneme devam edecektir.
1. Süre dolduktan sonra çık: ayarlarınızda `experiment_timeout_minutes` kullanmak, bir deneyin çalıştırmada ne kadar süreyle devam etmesi gerektiğini tanımlamanızı sağlar.
1. Bir puana ulaşıldıktan sonra çık: `experiment_exit_score` kullanmak, bir birincil ölçüm puanına ulaşıldıktan sonra denemeyi tamamlayacak.

### <a name="explore-model-metrics"></a>Model ölçümlerini keşfet

Bir not defteriniz varsa, eğitim sonuçlarınızı bir pencere öğesinde veya satır içi olarak görüntüleyebilirsiniz. Daha fazla ayrıntı için bkz. [modelleri izleme ve değerlendirme](how-to-track-experiments.md#view-run-details) .

## <a name="understand-automated-ml-models"></a>Otomatik ML modellerini anlama

Otomatikleştirilmiş ML kullanılarak oluşturulan herhangi bir model aşağıdaki adımları içerir:
+ Otomatik Özellik Mühendisliği (Eğer preprocess = true ise)
+ Hiper parametre değerleriyle ölçekleme/normalleştirme ve algoritma

Otomatikleştirilmiş ML 'den gelen fitted_model çıktısından bu bilgileri almak için saydam hale gelir.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Otomatik Özellik Mühendisliği

Feauturization = Auto olduğunda gerçekleşen ön işleme ve [Otomatik Özellik Mühendisliği](concept-automated-ml.md#preprocess) listesine bakın.

Şu örneği göz önünde bulundurun:
+ 4 giriş özelliği vardır: A (sayısal), B (sayısal), C (sayısal), D (TarihSaat)
+ Sayısal Özellik C, tüm benzersiz değerleri olan bir ID sütunu olduğundan bırakıldı
+ A ve B sayısal özelliklerinde eksik değerler var ve bu nedenle ortalama
+ Tarih saat özelliği D, 11 farklı mühendislik uygulanmış özelliklere sahiptir

Daha fazlasını anlamak için, bu 2 API 'Leri, monte edilecek modelin ilk adımında kullanın.  [Bu örnek Not defteri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)' ne bakın.

+ API 1: `get_engineered_feature_names()`, uygulanan özellik adlarının bir listesini döndürür.

  Kullanım:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Bu liste, uygulanan tüm özellik adlarını içerir.

  >[!Note]
  >Görev = ' tahmin ' için ' timeseriestransformer ' kullanın, aksi takdirde ' gerileme ' veya ' sınıflandırma ' görevi için ' datatransformer ' kullanın.

+ API 2: `get_featurization_summary()` tüm giriş özellikleri için korleştirme Özeti döndürüyor.

  Kullanım:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Görev = ' tahmin ' için ' timeseriestransformer ' kullanın, aksi takdirde ' gerileme ' veya ' sınıflandırma ' görevi için ' datatransformer ' kullanın.

  Çıktı:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Konumlar:

   |Çıktı|Tanım|
   |----|--------|
   |RawFeatureName|Girilen veri kümesindeki giriş özelliği/sütun adı.|
   |Typedetesiyonu|Giriş özelliğinin veri türü algılandı.|
   |Bırakılmış|Giriş özelliğinin bırakılıp bırakılmadığını veya kullanıldığını gösterir.|
   |EngineeringFeatureCount|Otomatikleştirilmiş Özellik Mühendisliği dönüştürmeleri aracılığıyla oluşturulan özellik sayısı.|
   |Dönüşümler|Uygulanan özellikler oluşturmak için giriş özelliklerine uygulanan dönüşümlerin listesi.|
   
### <a name="customize-feature-engineering"></a>Özellik Mühendisliği 'nı özelleştirme
Özellik Mühendisliği 'nı özelleştirmek için `"feauturization":FeaturizationConfig`belirtin.

Desteklenen özelleştirme şunları içerir:

|Özelleştirme|Tanım|
|--|--|
|Sütun amacı güncelleştirmesi|Belirtilen sütun için özellik türünü geçersiz kıl.|
|Transformatör parametresi güncelleştirmesi |Belirtilen transformatör için parametreleri güncelleştirin. Şu anda ımputer ve HashOneHotEncoder 'ı desteklemektedir.|
|Bırakma sütunları |Korturdan bırakılacak sütunlar.|
|Blok dönüştürücüler| Korleştirme işleminde kullanılacak dönüştürücüler bloğunu engelleyin.|

API çağrılarını kullanarak FeaturizationConfig nesnesini oluşturun:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Hiper parametre değerleriyle ölçekleme/normalleştirme ve algoritma:

Bir işlem hattının ölçekleme/normalleştirme ve algoritma/hiper parametre değerlerini anlamak için fitted_model. Steps kullanın. [Ölçeklendirme/normalleştirme hakkında daha fazla bilgi edinin](concept-automated-ml.md#preprocess). Örnek çıktı aşağıdaki gibidir:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Daha fazla bilgi edinmek için [Bu örnek not defterinde](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)gösterilen bu yardımcı işlevi kullanın.

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

Aşağıda belirli bir algoritma (Bu örnekte RobustScalar ile LogisticRegression) kullanılarak bir işlem hattı için örnek çıktı verilmiştir.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

<a name="explain"></a>

## <a name="model-interpretability"></a>Model yorumlanabilirliği

Model yorumlamalar, modellerinizin tahmin yaptığını ve temel alınan özellik önem değerlerini anlamanıza olanak tanır. SDK, yerel ve dağıtılmış modeller için hem eğitim hem de çıkarım sırasında model yorumsıya özellikleri etkinleştirmek için çeşitli paketler içerir.

Özellikle otomatik makine öğrenimi ['nin içindeki](how-to-machine-learning-interpretability-automl.md) yorumlu özelliklerin nasıl etkinleştirileceği hakkında kod örneklerine bakın.

Model açıklamalarının ve özelliklerinin önem derecesine ilişkin genel bilgiler için otomatik makine öğrenimi dışında SDK 'nın diğer alanlarında nasıl etkinleştirilecekleri hakkında genel bilgi edinmek için bkz. yorumda bulunan [kavram](how-to-machine-learning-interpretability.md) makalesi.

## <a name="next-steps"></a>Sonraki adımlar

[Bir modelin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md)hakkında daha fazla bilgi edinin.

[Otomatik makine öğrenimi ile regresyon modelini eğitme](tutorial-auto-train-models.md) veya [uzak bir kaynakta otomatik makine öğrenimi kullanarak nasıl eğitme](how-to-auto-train-remote.md)yapılacağı hakkında daha fazla bilgi edinin.

---
title: Yerel ve uzak çalıştırmalar için model yorumlenebilirliği
titleSuffix: Azure Machine Learning
description: Machine Learning modelinizin Özellik önemini nasıl belirlediğini ve Azure Machine Learning SDK kullanırken tahmine dayalı hale getirmek için nasıl açıklamalar alabileceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: ffb9e0547c44ee47a43de00e51933ce7d0584759
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158716"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Yerel ve uzak çalıştırmalar için model yorumlenebilirliği

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, modelinizin tahmin yaptığını anlamak için Azure Machine Learning Python SDK 'sının yorumlenebilirliği paketini kullanmayı öğreneceksiniz. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

* Hem yerel olarak hem de uzaktan işlem kaynaklarında eğitilen makine öğrenimi modellerini yorumlayın.
* Azure çalışma geçmişinde yerel ve genel açıklamaları depolayın.
* [Azure Machine Learning Studio](https://ml.azure.com)'daki yorumlenebilirlik görselleştirmelerini görüntüleyin.
* Modelinize bir Puanlama açıklama dağıtın.

Daha fazla bilgi için [Azure Machine Learning hizmetinde model yorumlenebilirliği](how-to-machine-learning-interpretability.md)konusuna bakın.

## <a name="local-interpretability"></a>Yerel yorumlenebilirlik

Aşağıdaki örnek, Azure hizmetleriyle iletişim kurmadan yorumlenebilirlik paketinin yerel olarak nasıl kullanılacağını gösterir.

1. Gerekirse, yorumlenebilirlik paketini almak için `pip install azureml-interpret` kullanın.

1. Yerel bir Jupyter not defterinde örnek modeli eğitme.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

1. Açıklama yerel olarak çağırın.
   * Bir açıklama nesnesini başlatmak için modelinizi ve bazı eğitim verilerinizi açıklama oluşturucusuna geçirin.
   * Açıklamaları ve görselleştirmelerinizi daha bilgilendirici hale getirmek için sınıflandırma yaparsanız özellik adlarını ve çıkış sınıfı adlarını geçirmeye seçebilirsiniz.

   Aşağıdaki kod blokları, yerel olarak `TabularExplainer`, `MimicExplainer`ve `PFIExplainer` ile bir açıklama nesnesinin örneğini oluşturmayı gösterir.
   * `TabularExplainer` alttaki üç SHAP explainers birini (`TreeExplainer`, `DeepExplainer`veya `KernelExplainer`) çağırır.
   * `TabularExplainer`, kullanım durumu için en uygun olanı otomatik olarak seçer, ancak her üç temel explainers doğrudan çağırabilirsiniz.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    veya

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    veya

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Genel, genel özellik önem değerleri

Genel özellik önem değerlerini almanıza yardımcı olması için aşağıdaki örneğe bakın.

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Örnek düzeyi, yerel özellik önem değerleri

Tek bir örneğin veya örnek grubunun açıklamalarını çağırarak yerel özellik önem değerlerini alın.
> [!NOTE]
> `PFIExplainer` yerel açıklamaları desteklemez.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Uzaktan çalıştırmalar için yorumlenebilirlik

Aşağıdaki örnek, uzak çalıştırmalar için model yorumlenebilirliğini etkinleştirmek üzere `ExplanationClient` sınıfını nasıl kullanabileceğinizi gösterir. Bu, yerel işlem için kavramsal olarak benzerdir, ancak şunları yapabilirsiniz:

* Yorumlenebilirlik bağlamını karşıya yüklemek için uzak çalıştırmada `ExplanationClient` kullanın.
* Bağlamı daha sonra yerel bir ortamda indirin.

1. Gerekirse, gerekli paketi almak için `pip install azureml-contrib-interpret` kullanın.

1. Bir eğitim betiği yerel bir Jupyter not defteri oluşturun. Örneğin, `train_explain.py`.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. İşlem hedefi olarak bir Azure Machine Learning Işlem kurun ve eğitim çalıştırmanıza iletin. Yönergeler için bkz. [model eğitimi için işlem hedeflerini ayarlama](how-to-set-up-training-targets.md#amlcompute) . [Örnek Not defterlerinin](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) yararlı olduğunu da görebilirsiniz.

1. Yerel Jupyter Not defterinize açıklamayı indirin.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Ham Özellik dönüştürmeleri

Mühendislik uygulanmış özellikler yerine ham, dönüştürülmemiş özellikler açısından açıklamaları almayı tercih edebilirsiniz. Bu seçenek için, özellik dönüştürme işlem hattınızı `train_explain.py`içindeki açıklama geçirin. Aksi takdirde açıklama, mühendislik uygulanmış özellikler açısından açıklamaları sağlar.

Desteklenen dönüşümlerin biçimi, [sköğren-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)bölümünde açıklananla aynıdır. Genel olarak, Tüm dönüştürmeler tek bir sütunda çalıştıkları sürece desteklenir, böylece bir çok-çok olacak şekilde net bir şekilde çalışır.

`sklearn.compose.ColumnTransformer` veya bir birlikte bulunan transformatör tanımlama grupları listesi kullanarak ham özellikler için bir açıklama alın. Aşağıdaki örnek `sklearn.compose.ColumnTransformer`kullanır.

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Örneği, birlikte bulunan transformatör tanımlama grupları listesiyle çalıştırmak istiyorsanız aşağıdaki kodu kullanın:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Görsel öğeler

Yerel Jupyter Not defterinize açıklamaları indirdikten sonra, modelinizi anlamak ve yorumlamak için görselleştirme panosunu kullanabilirsiniz.

### <a name="global-visualizations"></a>Küresel görselleştirmeler

Aşağıdaki çizimler, eğitilen modelin, tahmine dayalı ve açıklamalarıyla birlikte küresel bir görünümünü sağlar.

|ZF|Açıklama|
|----|-----------|
|Veri araştırması| Tahmin değerleriyle birlikte veri kümesine genel bir bakış görüntüler.|
|Küresel önem derecesi|Üst K (yapılandırılabilir K) önemli özellikleri küresel olarak gösterir. Temel modelin genel davranışının anlaşılmasına yardımcı olur.|
|Açıklama araştırması|Bir özelliğin modelin tahmin değerlerinde bir değişikliği nasıl etkilediğini veya tahmin değerlerinin olasılığını gösterir. Özellik etkileşiminin etkisini gösterir.|
|Özet önem derecesi|Her bir özelliğin tahmin değerindeki etkisinin dağıtımını göstermek için tüm veri noktalarında yerel, özellik önem değerlerini kullanır.|

[![görselleştirme panosu genel](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Yerel görselleştirmeler

Her veri noktası için yerel, özellik önem düzeyini, çizmede tek bir veri noktasını seçerek yükleyebilirsiniz.

|ZF|Açıklama|
|----|-----------|
|Yerel önem derecesi|Genel olarak üst K (yapılandırılabilir K) önemli özellikleri gösterir. Belirli bir veri noktasındaki temeldeki modelin yerel davranışını göstermeye yardımcı olur.|
|Perturbation araştırması|Seçili veri noktasının özellik değerlerinde değişikliklere izin verir ve sonuç olarak tahmini değişiklikleri gözlemleyin.|
|Bireysel koşullu beklenti (buz)| Özellik değeri değişikliklerinin en küçük değerden en büyük değere değiştirilmesine izin verir. Bir özellik değiştiğinde veri noktasının öngörme şeklini göstermeye yardımcı olur.|

[![görselleştirme Panosu yerel özellik önemi](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![görselleştirme Pano özelliği Perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![görselleştirme Pano buz çizimleri](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

> [!NOTE]
> Jupyıter çekirdeği başlamadan önce görselleştirme panosu için pencere öğesi uzantılarını etkinleştirdiğinizden emin olun.

* Jupyter notebooks

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* Jupyıterlab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Görselleştirme panosunu yüklemek için aşağıdaki kodu kullanın:

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio 'da görselleştirme

[Uzaktan YORUMSİZ](#interpretability-for-remote-runs) adımları tamamlarınız için [Azure Machine Learning Studio](https://ml.azure.com)'da görselleştirme panosunu görüntüleyebilirsiniz. Bu Pano, yukarıda açıklanan görselleştirme panosunun daha basit bir sürümüdür. Yalnızca iki sekmeyi destekler:

|ZF|Açıklama|
|----|-----------|
|Küresel önem derecesi|Üst K (yapılandırılabilir K) önemli özellikleri küresel olarak gösterir. Temel modelin genel davranışının anlaşılmasına yardımcı olur.|
|Özet önem derecesi|Her bir özelliğin tahmin değerindeki etkisinin dağıtımını göstermek için tüm veri noktalarında yerel, özellik önem değerlerini kullanır.|

Hem genel hem de yerel açıklamalar varsa, veriler her iki sekmeden de doldurulur. Yalnızca genel bir açıklama varsa, Özet önem düzeyi sekmesi devre dışıdır.

Azure Machine Learning Studio 'daki görselleştirme panosuna erişmek için bu yollardan birini izleyin:

* **Denemeleri** bölmesi (Önizleme)
  1. Azure Machine Learning hizmeti 'nde çalıştırdığınız denemeleri listesini görmek için sol bölmedeki **denemeleri** öğesini seçin.
  1. Bu deneyteki tüm çalıştırmaları görüntülemek için belirli bir deneme seçin.
  1. Bir çalıştırma seçin ve ardından **açıklamalar** sekmesini açıklama görselleştirme panosu ' na tıklayın.

   [![görselleştirme Panosu yerel özellik önemi](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)

* **Modeller** bölmesi
  1. [Azure Machine Learning ile modelleri dağıtma](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)bölümündeki adımları izleyerek orijinal modelinizi kaydettiniz, görüntülemek için sol bölmedeki **modeller** ' ı seçebilirsiniz.
  1. Açıklama görselleştirme panosunu görüntülemek için bir model ve ardından **açıklamalar** sekmesini seçin.

## <a name="interpretability-at-inference-time"></a>Çıkarımı zamanında yorumlenebilirlik

Açıklama 'yi özgün modelle birlikte dağıtabilir ve yerel açıklama bilgilerini sağlamak için bunu çıkarımı zaman içinde kullanabilirsiniz. Ayrıca, çıkarım performansını artırmak için daha hafif Puanlama explainers sunuyoruz. Daha hafif bir Puanlama açıklama dağıtma işlemi, model dağıtmaya benzer ve aşağıdaki adımları içerir:

1. Bir açıklama nesnesi oluşturun. Örneğin, `TabularExplainer`kullanabilirsiniz:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Açıklama nesnesiyle bir Puanlama açıklama oluşturun.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Puanlama açıklama modelini kullanan bir görüntüyü yapılandırın ve kaydedin.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. İsteğe bağlı bir adım olarak, Puanlama açıklama buluttan alabilir ve açıklamaları test edebilirsiniz.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Aşağıdaki adımları izleyerek görüntüyü bir işlem hedefine dağıtın:

   1. Gerekirse, [modelleri Azure Machine Learning Ile dağıtma](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)' daki adımları izleyerek orijinal tahmin modelinizi kaydedin.

   1. Puanlama dosyası oluşturma.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Dağıtım Yapılandırması tanımlayın.

         Bu yapılandırma, modelinizin gereksinimlerine bağlıdır. Aşağıdaki örnekte, bir CPU çekirdeği ve bir GB bellek kullanan bir yapılandırma tanımlanmaktadır.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Ortam bağımlılıklarıyla bir dosya oluşturun.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. G + + yüklenmiş özel bir dockerfile oluşturun.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Oluşturulan görüntüyü dağıtın.
   
         Bu işlem yaklaşık beş dakika sürer.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Dağıtımı test edin.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Temizleyin.

   Dağıtılmış bir web hizmetini silmek için kullanın `service.delete()`.

## <a name="next-steps"></a>Sonraki adımlar

[Model yorumlenebilirliği hakkında daha fazla bilgi edinin](how-to-machine-learning-interpretability.md)
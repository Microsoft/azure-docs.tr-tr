---
title: Otomatik ML 'de Explainability (Önizleme)
titleSuffix: Azure Machine Learning
description: Otomatikleştirilmiş ML modelinizin Özellik önemini nasıl belirlediğini ve Azure Machine Learning SDK kullanırken tahmine dayalı hale getirmek için nasıl açıklamalar alabileceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 3258a1d53c4aa5010758bcd93ef32c53611f4684
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576474"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Yorumlanabilirlik: otomatik makine öğrenmesinde model açıklamaları (önizleme)


Bu makalede, Python SDK kullanarak Azure Machine Learning ' de otomatik makine öğrenimi (otomatik ML) açıklamalarını alma hakkında bilgi edinebilirsiniz. Otomatikleştirilmiş ML, oluşturulan modellerin Özellik önemini anlamanıza yardımcı olur. 

1.0.85 sonrasında tüm SDK sürümleri `model_explainability=True` Varsayılan olarak ayarlanır. SDK sürüm 1.0.85 ve önceki sürümlerinde, kullanıcıların `model_explainability=True` `AutoMLConfig` model yorumlenebilirliğini kullanabilmesi için nesnesine ayarlanması gerekir. 


Bu makalede şunları öğreneceksiniz:

- En iyi model veya herhangi bir model için eğitim sırasında yorumlenebilirlik gerçekleştirin.
- Veri ve açıklamaları desenlerdeki desenleri görmenizi sağlayacak görselleştirmeleri etkinleştirin.
- Çıkarım veya Puanlama sırasında yorumlenebilirlik uygulayın.

## <a name="prerequisites"></a>Önkoşullar

- Yorumlenebilirlik özellikleri. `pip install azureml-interpret`Gerekli paketi almak için ' i çalıştırın.
- Otomatik ML denemeleri oluşturma hakkında bilgi. Azure Machine Learning SDK 'sını kullanma hakkında daha fazla bilgi için, bu [regresyon modeli öğreticisini](tutorial-auto-train-models.md) doldurun veya [Otomatik ml denemeleri](how-to-configure-auto-train.md)'yi nasıl yapılandıracağınızı öğrenin.

## <a name="interpretability-during-training-for-the-best-model"></a>En iyi model için eğitim sırasında yorumlenebilirlik

`best_run`Hem ham hem de mühendislik uygulanmış özelliklerin açıklamalarını içeren öğesinden açıklamayı alın.

> [!NOTE]
> Yorumlenebilirlik, en iyi model açıklaması, aşağıdaki algoritmaların en iyi model olarak kullanılması için otomatik ML tahmin denemeleri için kullanılamaz: 
> * TCNForecaster
> * Oto ARIMA
> * Üs Alaltı yumuşatma
> * Prophet
> * Ortalama 
> * Naive
> * Mevsimlik ortalama 
> * Mevsimtif

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Mühendislik uygulanan özelliği en iyi çalıştırmada indirin

' `ExplanationClient` Nin yapıt deposundan, uygulanan özellik açıklamalarını indirmek için ' i kullanabilirsiniz `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>En iyi çalıştırmanın ham özelliklerini indirin

`ExplanationClient`Ham özellik açıklamalarını uygulamasının yapıt deposundan indirmek için ' i kullanabilirsiniz `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Herhangi bir model için eğitim sırasında yorumlenebilirlik 

Model açıklamalarını hesaplarken ve bunları görselleştirerek, bir oto modeli için mevcut bir model açıklaması ile sınırlı değilsiniz. Ayrıca, farklı test verileriyle modelinize yönelik bir açıklama alabilirsiniz. Bu bölümdeki adımlarda, test verilerinize göre uygulanan özellik önemini nasıl hesaplabileceğiniz ve görselleştirebileceğiniz gösterilmektedir.

### <a name="retrieve-any-other-automl-model-from-training"></a>Eğitimin diğer tüm oto modellerini al

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Model açıklamalarını ayarlama

`automl_setup_model_explanations`Mühendislik uygulanan ve ham açıklamaları almak için kullanın. , `fitted_model` Aşağıdaki öğeleri oluşturabilir:

- Eğitilen veya test örneklerinden öne çıkan veriler
- Uygulanan özellik adı listeleri
- Sınıflandırma senaryolarında etiketli sütunlarınızda findable sınıfları

, `automl_explainer_setup_obj` Yukarıdaki listeden tüm yapıları içerir.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Özellik önemi için benzeme açıklama 'ı başlatın

Otomatik ML modelleriyle ilgili bir açıklama oluşturmak için `MimicWrapper` sınıfını kullanın. Mmıısarmalayıcı 'yi şu parametrelerle başlatabilirsiniz:

- Açıklama Kurulum nesnesi
- Çalışma alanınız
- Otomatikleştirilmiş ML modelini açıklamak için bir yedek modeli `fitted_model`

Mımısarmalayıcı, uygulanan açıklamalarının `automl_run` karşıya yükleneceği nesneyi de alır.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Uygulanan özellik önemini hesaplamak ve görselleştirmek için Benzeaçıklama kullanma

`explain()`Oluşturulan mühendislik özelliklerine ilişkin özellik önemini elde etmek için, dönüştürülmüş test örnekleriyle birlikte Msarmalayıcı içindeki yöntemi çağırabilirsiniz. Ayrıca, üretilen mühendislik özelliklerinin Özellikler Pano görselleştirmesini otomatik ML özellikleri tarafından görüntülemek için [Azure Machine Learning Studio](https://ml.azure.com/) 'da da oturum açabilirsiniz.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```
Otomatikleştirilmiş ML ile eğitilen modeller için, `get_output()` yöntemi ve işlem açıklamalarını yerel olarak kullanarak en iyi modeli elde edebilirsiniz.  Açıklama sonuçlarını paketiyle görselleştirebilirsiniz `ExplanationDashboard` `interpret-community` .

```python
best_run, fitted_model = remote_run.get_output()

from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations
automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train,
                                                             X_test=X_test, y=y_train,
                                                             task='regression')

from interpret.ext.glassbox import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=best_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
                         
pip install interpret-community[visualization]

engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, datasetX=automl_explainer_setup_obj.X_test_transform)

 

raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(raw_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, datasetX=automl_explainer_setup_obj.X_test_raw)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Ham Özellik önemini hesaplamak ve görselleştirmek için Benzeaçıklama kullanma

`explain()`Ham özelliklerin özellik önem derecesini almak için, dönüştürülmüş test örnekleriyle birlikte MimicWrapper içindeki yöntemi çağırabilirsiniz. [Machine Learning Studio](https://ml.azure.com/)'da, ham özelliklerin özellik önem derecesi değerlerinin Pano görselleştirmesini görüntüleyebilirsiniz.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Çıkarım sırasında yorumlenebilirlik

Bu bölümde, önceki bölümde yer alan açıklamaları hesaplamak için kullanılan açıklama ile otomatik bir ML modeli oluşturmayı öğreneceksiniz.

### <a name="register-the-model-and-the-scoring-explainer"></a>Modeli ve Puanlama açıklama kaydetme

' İ kullanarak, `TreeScoringExplainer` uygulanan özellik önem değerlerini, çıkarım zamanında hesaplayacağımız Puanlama açıklama oluşturmak için kullanın. Puanlama Açıklama `feature_map` daha önce hesaplanan ile birlikte başlatılıyor. 

Puanlama Açıklama ' nı kaydedin ve sonra modeli ve Puanlama Açıklama ' ı Model Yönetimi hizmetiyle kaydedin. Aşağıdaki kodu çalıştırın:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Hizmeti ayarlamak için Conda bağımlılıklarını oluşturun

Ardından, dağıtılan model için kapsayıcıda gerekli ortam bağımlılıklarını oluşturun. Lütfen, modeli bir Web hizmeti olarak barındırmak için gereken işlevleri içerdiğinden, >= 1.0.45 sürümüne sahip olan azureml-varsayılan değer bir PIP bağımlılığı olarak listelenmelidir.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>Puanlama betiği oluşturma

Modelinizi yükleyen ve yeni bir veri grubu temelinde tahminler ve açıklamalar üreten bir betik yazın.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>Hizmeti dağıtma

Önceki adımlardan Conda dosyasını ve Puanlama dosyasını kullanarak hizmeti dağıtın.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Test verileriyle çıkarım

Şu anda yalnızca Azure Machine Learning SDK 'sında desteklenen, oto ml modelinden tahmin edilen değeri görmek için bazı test verileriyle çıkarım. Tahmin edilen bir değer için katkıda bulunan özelliği görüntüleyin. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

## <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Eğitim sırasında veri ve açıklamaları desenlerdeki desenleri keşfetmeyi görselleştirin

Özellik önem grafiğini [Azure Machine Learning Studio](https://ml.azure.com)'daki çalışma alanınızda görselleştirebilirsiniz. Oto ml çalıştırmanız tamamlandıktan sonra, belirli bir çalışmayı görüntülemek için **model ayrıntılarını görüntüle** ' yi seçin. Açıklama panosundaki görselleştirmeleri görmek için **açıklamalar** sekmesini seçin.

[![Machine Learning Yorumlenebilirlik mimarisi](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Açıklama panosu görselleştirmeleri ve belirli çizimler hakkında daha fazla bilgi için lütfen bkz. [nasıl yapılır belgesi yorumda](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Sonraki adımlar

Otomatik makine öğrenimi dışında Azure Machine Learning SDK 'sının alanlarında model açıklamalarını ve özellik önemini nasıl etkinleştirebilirim hakkında daha fazla bilgi için, bkz. [yorumda bulunan kavram makalesi](how-to-machine-learning-interpretability.md).

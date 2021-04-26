---
title: Mevcut modelleri kullanma ve dağıtma
titleSuffix: Azure Machine Learning
description: Azure 'un dışında eğitilen ML modellerini Azure Machine Learning ile Azure bulutuna getirme hakkında bilgi edinin. Ardından modeli bir Web hizmeti veya IoT modülü olarak dağıtın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 46dc9e48ba68189253885ae823457a62c3c4426e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877813"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Mevcut modelinize Azure Machine Learning dağıtın


Bu makalede, Azure Machine Learning dışında eğitilen bir makine öğrenimi modelini kaydetmeyi ve dağıtmayı öğreneceksiniz. Bir Web hizmeti veya IoT Edge bir cihaza dağıtabilirsiniz.  Dağıtıldıktan sonra modelinizi izleyebilir ve Azure Machine Learning veri kayması tespit edebilirsiniz. 

Bu makaledeki kavramlar ve terimler hakkında daha fazla bilgi için bkz. [Machine Learning modellerini yönetme, dağıtma ve izleme](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Önkoşullar

* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md)
  + Python örnekleri, `ws` değişkenin Azure Machine Learning çalışma alanınıza ayarlandığını varsayar. Çalışma alanına bağlanma hakkında daha fazla bilgi için lütfen [Python için Azure MACHINE LEARNING SDK belgelerine](/python/api/overview/azure/ml/#workspace)başvurun.
  
  + CLı örnekleri `myworkspace` , ve ' nin `myresourcegroup` , çalışma alanınızın adı ve onu içeren kaynak grubu ile değiştirmeniz gereken yer tutucuları kullanır.

* [Python SDK Azure Machine Learning](/python/api/overview/azure/ml/install).  

* [Azure CLI](/cli/azure/install-azure-cli) ve [Machine Learning CLI uzantısı](reference-azure-machine-learning-cli.md).

* Eğitilen bir model. Model, geliştirme ortamınızda bir veya daha fazla dosyada kalıcı olmalıdır. <br><br>Eğitilen bir modelin kaydedilmesini göstermek için bu makaledeki örnek kod, [Paolo Ripamonti 'ın Twitter yaklaşım Analizi projesinden](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)modelleri kullanır.

## <a name="register-the-models"></a>Model (ler) i Kaydet

Bir modeli kaydetmek, çalışma alanınızdaki modeller hakkında meta verileri depolamanıza, sürümüne ve izlemenize olanak sağlar. Aşağıdaki Python ve CLI örneklerinde, `models` Dizin `model.h5` ,, `model.w2v` `encoder.pkl` ve dosyalarını içerir `tokenizer.pkl` . Bu örnek, dizinde bulunan dosyaları `models` adlı yeni bir model kaydı olarak yükler `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Daha fazla bilgi için bkz. [model. Register ()](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) başvurusu.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Ayrıca, `tags` kayıtlı modele ekleme ve `properties` sözlük nesneleri de ayarlayabilirsiniz. Bu değerler, daha sonra belirli bir modeli belirlemesine yardımcı olmak için kullanılabilir. Örneğin, kullanılan çerçeve, eğitim parametreleri vb.

Daha fazla bilgi için, [az ml model kayıt](/cli/azure/ml/model#az_ml_model_register) başvurusuna bakın.


Model kaydı hakkında genel bilgi için bkz. [Machine Learning modellerini yönetme, dağıtma ve izleme](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Çıkarım yapılandırmasını tanımla

Çıkarım yapılandırması, dağıtılan modeli çalıştırmak için kullanılan ortamı tanımlar. Çıkarım yapılandırması, dağıtıldıktan sonra modeli çalıştırmak için kullanılan aşağıdaki varlıklara başvurur:

* Adlı bir giriş betiği, `score.py` dağıtılan hizmet başladığında modeli yükler. Bu betik, veri alma, modele geçirme ve sonra yanıt döndürme açısından de sorumludur.
* Azure Machine Learning [ortamı](how-to-use-environments.md). Bir ortam, model ve giriş betiğini çalıştırmak için gereken yazılım bağımlılıklarını tanımlar.

Aşağıdaki örnek, bir ortam oluşturmak için SDK 'nın nasıl kullanılacağını gösterir ve ardından bunu bir çıkarım yapılandırmasıyla kullanmaktır:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

+ [Ortamları kullanma](how-to-use-environments.md).
+ [Inenceconfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) başvurusu.


CLı, bir YAML dosyasından çıkarım yapılandırmasını yükler:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

CLı ile, Conda ortamı, `myenv.yml` çıkarım yapılandırması tarafından başvurulan dosyada tanımlanır. Aşağıdaki YAML bu dosyanın içeriğidir:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Çıkarım yapılandırması hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Giriş betiği (score.py)

Giriş betiği yalnızca iki zorunlu işleve sahiptir `init()` ve `run(data)` . Bu işlevler, başlatma sırasında hizmeti başlatmak ve bir istemci tarafından geçirilen istek verilerini kullanarak modeli çalıştırmak için kullanılır. Komut dosyasının geri kalanı, modeli yüklemeyi ve çalıştırmayı işler.

> [!IMPORTANT]
> Tüm modeller için kullanılan genel bir giriş betiği yok. Her zaman kullanılan modele özeldir. Modelin nasıl yükleneceğini, modelin beklediği veri biçimini ve modeli kullanarak verilerin nasıl puanlandıralınacağını anlamalıdır.

Aşağıdaki Python kodu bir örnek giriş betiğine ( `score.py` ) sahiptir:

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Giriş betikleri hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Dağıtımı tanımla

[WebService](/python/api/azureml-core/azureml.core.webservice) paketi, dağıtım için kullanılan sınıfları içerir. Kullandığınız sınıf, modelin dağıtıldığı yeri belirler. Örneğin, Azure Kubernetes hizmetinde bir Web hizmeti olarak dağıtmak için, dağıtım yapılandırmasını oluşturmak üzere [AksWebService.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) kullanın.

Aşağıdaki Python kodu bir yerel dağıtım için dağıtım yapılandırmasını tanımlar. Bu yapılandırma, modeli yerel bilgisayarınıza bir Web hizmeti olarak dağıtır.

> [!IMPORTANT]
> Yerel bir dağıtım için yerel bilgisayarınıza [Docker](https://www.docker.com/) çalışır şekilde bir yükleme gerekir:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Daha fazla bilgi için [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-) başvurusuna bakın.

CLı, dağıtım yapılandırmasını bir YAML dosyasından yükler:

```YAML
{
    "computeType": "LOCAL"
}
```

Azure bulutundaki Azure Kubernetes hizmeti gibi farklı bir işlem hedefine dağıtım, dağıtım yapılandırmasını değiştirmek kadar kolaydır. Daha fazla bilgi için bkz. [modellerin nasıl ve ne şekilde dağıtılacağı](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Modeli dağıtma

Aşağıdaki örnek, adlı kayıtlı modele bilgileri yükler `sentiment` ve ardından adlı bir hizmet olarak dağıtır `sentiment` . Dağıtım sırasında, hizmet ortamını oluşturmak ve yapılandırmak için çıkarım yapılandırma ve dağıtım yapılandırması kullanılır:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Daha fazla bilgi için bkz. [model. deploy ()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) başvurusu.

Modeli CLı 'dan dağıtmak için aşağıdaki komutu kullanın. Bu komut `sentiment:1` , ve dosyalarında depolanan çıkarım ve dağıtım yapılandırmasını kullanarak kayıtlı modelin () sürüm 1 ' i `inferenceConfig.json` dağıtır `deploymentConfig.json` :

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Daha fazla bilgi için, [az ml model dağıtım](/cli/azure/ml/model#az_ml_model_deploy) başvurusuna bakın.

Dağıtım hakkında daha fazla bilgi için bkz. [modellerin nasıl ve ne şekilde dağıtılacağı](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>İstek-yanıt tüketimi

Dağıtımdan sonra Puanlama URI 'SI görüntülenir. Bu URI, istemciler tarafından hizmete istek göndermek için kullanılabilir. Aşağıdaki örnek, hizmete veri gönderen ve yanıtı görüntüleyen basit bir Python istemcsahiptir:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Dağıtılan hizmeti kullanma hakkında daha fazla bilgi için bkz. [Istemci oluşturma](how-to-consume-web-service.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Application Insights Azure Machine Learning modellerinizi izleyin](how-to-enable-app-insights.md)
* [Üretimde modeller için veri toplama](how-to-enable-data-collection.md)
* [Dağıtılan bir model için istemci oluşturma](how-to-consume-web-service.md)
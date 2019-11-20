---
title: Mevcut modelleri kullanma ve dağıtma
titleSuffix: Azure Machine Learning
description: Hizmetin dışında eğitilen modellerle Azure Machine Learning nasıl kullanabileceğinizi öğrenin. Azure Machine Learning dışında oluşturulan modelleri kaydedebilir ve sonra bunları bir Web hizmeti veya Azure IoT Edge modülü olarak dağıtabilirsiniz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: b25189eb63961056c7966722dccb6857736d0c35
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123237"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Azure Machine Learning var olan bir modeli kullanın
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning ile mevcut makine öğrenimi modelini nasıl kullanacağınızı öğrenin.

Azure Machine Learning dışında eğitilen bir makine öğrenimi modelinize sahipseniz, modeli bir Web hizmeti olarak veya bir IoT Edge cihazına dağıtmak için hizmetini kullanmaya devam edebilirsiniz. 

> [!TIP]
> Bu makalede, var olan bir modeli kaydetme ve dağıtma hakkında temel bilgiler sağlanmaktadır. Dağıtıldıktan sonra, Azure Machine Learning modelinize yönelik izleme sağlar. Ayrıca, dağıtıma gönderilen giriş verilerini depolamanıza olanak tanır. Bu, veri DRI analizi veya modelin yeni sürümlerini eğitmek için kullanılabilir.
>
> Burada kullanılan kavramlar ve terimler hakkında daha fazla bilgi için bkz. [Machine Learning modellerini yönetme, dağıtma ve izleme](concept-model-management-and-deployment.md).
>
> Dağıtım işlemi hakkında genel bilgi için bkz. [modelleri Azure Machine Learning Ile dağıtma](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [çalışma alanı oluşturma](how-to-manage-workspace.md).

    > [!TIP]
    > Bu makaledeki Python örnekleri, `ws` değişkeninin Azure Machine Learning çalışma alanınıza ayarlandığını varsayar.
    >
    > CLı örnekleri `myworkspace` ve `myresourcegroup`yer tutucusunu kullanır. Bunları, çalışma alanınızın adı ve onu içeren kaynak grubu ile değiştirin.

* [Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ve [Machine Learning CLI uzantısı](reference-azure-machine-learning-cli.md).

* Eğitilen bir model. Model, geliştirme ortamınızda bir veya daha fazla dosyada kalıcı olmalıdır.

    > [!NOTE]
    > Azure Machine Learning dışında eğitilen bir modelin kaydedilmesini göstermek için, bu makaledeki örnek kod parçacıkları Paolo Ripamonti 'ın Twitter yaklaşım analiz projesi tarafından oluşturulan modelleri kullanır: [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Model (ler) i Kaydet

Bir modeli kaydetmek, çalışma alanınızdaki modeller hakkında meta verileri depolamanıza, sürümüne ve izlemenize olanak sağlar. Aşağıdaki Python ve CLı örneklerinde `models` Dizin `model.h5`, `model.w2v`, `encoder.pkl`ve `tokenizer.pkl` dosyalarını içerir. Bu örnek `models` dizininde bulunan dosyaları `sentiment`adlı yeni bir model kaydı olarak yükler:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Daha fazla bilgi için bkz. [model. Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) başvurusu.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Daha fazla bilgi için, [az ml model kayıt](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) başvurusuna bakın.


Model kaydı hakkında genel bilgi için bkz. [Machine Learning modellerini yönetme, dağıtma ve izleme](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Çıkarım yapılandırmasını tanımla

Çıkarım yapılandırması, dağıtılan modeli çalıştırmak için kullanılan ortamı tanımlar. Çıkarım yapılandırması, dağıtıldıktan sonra modeli çalıştırmak için kullanılan aşağıdaki varlıklara başvurur:

* Bir giriş betiği. Bu dosya (`score.py`adlı), dağıtılan hizmet başladığında modeli yükler. Verilerin alınması, modele geçirilmesi ve sonra bir yanıt döndürmesi da sorumludur.
* Azure Machine Learning [ortamı](how-to-use-environments.md). Bir ortam, model ve giriş betiğini çalıştırmak için gereken yazılım bağımlılıklarını tanımlar.

Aşağıdaki örnek, bir ortam oluşturmak için SDK 'nın nasıl kullanılacağını gösterir ve ardından bunu bir çıkarım yapılandırmasıyla kullanmaktır:

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
conda_dep.add_pip_package("keras")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Daha fazla bilgi için aşağıdaki makalelere bakın:

+ [Ortamları kullanma](how-to-use-environments.md).
+ [Inenceconfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) başvurusu.


CLı, bir YAML dosyasından çıkarım yapılandırmasını yükler:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

CLı ile Conda ortamı, çıkarım yapılandırması tarafından başvurulan `myenv.yml` dosyasında tanımlanır. Aşağıdaki YAML bu dosyanın içeriğidir:

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
```

Çıkarım yapılandırması hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Giriş betiği

Giriş betiği, `init()` ve `run(data)`yalnızca iki gerekli işleve sahiptir. Bu işlevler, başlatma sırasında hizmeti başlatmak ve bir istemci tarafından geçirilen istek verilerini kullanarak modeli çalıştırmak için kullanılır. Komut dosyasının geri kalanı, modeli yüklemeyi ve çalıştırmayı işler.

> [!IMPORTANT]
> Tüm modeller için kullanılan genel bir giriş betiği yok. Her zaman kullanılan modele özeldir. Modelin nasıl yükleneceğini, modelin beklediği veri biçimini ve modeli kullanarak verilerin nasıl puanlandıralınacağını anlamalıdır.

Aşağıdaki Python kodu bir örnek giriş betiğine (`score.py`) sahiptir:

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

[WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) paketi, dağıtım için kullanılan sınıfları içerir. Kullandığınız sınıf, modelin dağıtıldığı yeri belirler. Örneğin, Azure Kubernetes hizmetinde bir Web hizmeti olarak dağıtmak için, dağıtım yapılandırmasını oluşturmak üzere [Akswebservice. deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-) kullanın.

Aşağıdaki Python kodu bir yerel dağıtım için dağıtım yapılandırmasını tanımlar. Bu yapılandırma, modeli yerel bilgisayarınıza bir Web hizmeti olarak dağıtır.

> [!IMPORTANT]
> Yerel bir dağıtım için yerel bilgisayarınıza [Docker](https://www.docker.com/) çalışır şekilde bir yükleme gerekir:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Daha fazla bilgi için bkz. [Localwebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) başvurusu.

CLı, dağıtım yapılandırmasını bir YAML dosyasından yükler:

```YAML
{
    "computeType": "LOCAL"
}
```

Azure bulutundaki Azure Kubernetes hizmeti gibi farklı bir işlem hedefine dağıtım, dağıtım yapılandırmasını değiştirmek kadar kolaydır. Daha fazla bilgi için bkz. [modellerin nasıl ve ne şekilde dağıtılacağı](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Modeli dağıtma

Aşağıdaki örnek, `sentiment`adlı kayıtlı modele bilgi yükler ve ardından bunu `sentiment`adlı bir hizmet olarak dağıtır. Dağıtım sırasında, hizmet ortamını oluşturmak ve yapılandırmak için çıkarım yapılandırma ve dağıtım yapılandırması kullanılır:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Daha fazla bilgi için bkz. [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) başvurusu.

Modeli CLı 'dan dağıtmak için aşağıdaki komutu kullanın. Bu komut, `inferenceConfig.json` ve `deploymentConfig.json` dosyalarında depolanan çıkarım ve dağıtım yapılandırmasını kullanarak kayıtlı modelin 1. sürümünü (`sentiment:1`) dağıtır:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Daha fazla bilgi için, [az ml model dağıtım](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) başvurusuna bakın.

Dağıtım hakkında daha fazla bilgi için bkz. [modellerin nasıl ve ne şekilde dağıtılacağı](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>İstek-yanıt tüketimi

Dağıtımdan sonra Puanlama URI 'SI görüntülenir. Bu URI, istemciler tarafından hizmete istek göndermek için kullanılabilir. Aşağıdaki örnek, hizmete veri gönderen ve yanıtı görüntüleyen temel bir Python istemcisine sahiptir:

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
* [Modellerin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md)
* [Dağıtılan bir model için istemci oluşturma](how-to-consume-web-service.md)

---
title: GPU ile çıkarım için model dağıtma
titleSuffix: Azure Machine Learning
description: Bu makalede, GPU özellikli bir TensorFlow derin öğrenme modelini Web hizmeti olarak dağıtmak için Azure Machine Learning kullanma öğretilir. hizmet ve puan çıkarma istekleri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 6797c32ded5c12bbac3fafa1eabd1e6f74d28e07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519257"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU ile çıkarım için derin öğrenme modeli dağıtma


Bu makalede, GPU özellikli bir modeli bir Web hizmeti olarak dağıtmak için Azure Machine Learning kullanma öğretilir. Bu makaledeki bilgiler, Azure Kubernetes Service (AKS) üzerinde bir model dağıtmaya dayalıdır. AKS kümesi, çıkarım için model tarafından kullanılan bir GPU kaynağı sağlar.

Çıkarım veya model Puanlama, dağıtılan modelin tahminleri yapmak için kullanıldığı aşamadır. CPU yerine GPU 'Ları kullanmak, yüksek bir paralelleştirilebilir hesaplama üzerinde performans avantajları sunar.

> [!IMPORTANT]
> Web hizmeti dağıtımları için GPU çıkarımı yalnızca Azure Kubernetes hizmetinde desteklenir. __Makine öğrenimi ardışık düzeni__ kullanan çıkarım için, GPU 'lar yalnızca Azure Machine Learning işlem sırasında desteklenir. ML işlem hatlarını kullanma hakkında daha fazla bilgi için bkz. [öğretici: Batch Puanlama için Azure Machine Learning işlem hattı oluşturma](tutorial-pipeline-batch-scoring-classification.md). 

> [!TIP]
> Bu makaledeki kod parçacıkları bir TensorFlow modeli kullanmasına karşın, bilgileri GPU 'ları destekleyen herhangi bir Machine Learning çerçevesine uygulayabilirsiniz.

> [!NOTE]
> Bu makaledeki bilgiler, [Azure Kubernetes hizmetine dağıtma](how-to-deploy-azure-kubernetes-service.md) makalesindeki bilgileri oluşturur. Bu makalede genellikle AKS 'e dağıtımı ele alınmaktadır ve bu makalede GPU 'ya özgü dağıtım ele alınmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

* Azure Machine Learning SDK yüklü bir Python geliştirme ortamı. Daha fazla bilgi için bkz. [SDK Azure Machine Learning](/python/api/overview/azure/ml/install).  

* GPU kullanan kayıtlı bir model.

    * Modellerin nasıl kaydedileceği hakkında bilgi edinmek için bkz. [modelleri dağıtma](how-to-deploy-and-where.md#registermodel).

    * Bu belgeyi oluşturmak için kullanılan TensorFlow modelini oluşturmak ve kaydetmek için bkz. [bir TensorFlow modelini eğitme](how-to-train-tensorflow.md).

* [Modellerin nasıl ve nereye dağıtılacağını](how-to-deploy-and-where.md)gösteren genel bir anlama.

## <a name="connect-to-your-workspace"></a>Çalışma alanınıza bağlanma

Mevcut bir çalışma alanına bağlanmak için aşağıdaki kodu kullanın:

> [!IMPORTANT]
> Bu kod parçacığı, çalışma alanı yapılandırmasının geçerli dizine veya onun üst öğesine kaydedilmesini bekliyor. Çalışma alanı oluşturma hakkında daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanları oluşturma ve yönetme](how-to-manage-workspace.md).   Yapılandırmayı dosyaya kaydetme hakkında daha fazla bilgi için bkz. [çalışma alanı yapılandırma dosyası oluşturma](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>GPU 'Lar ile Kubernetes kümesi oluşturma

Azure Kubernetes hizmeti birçok farklı GPU seçeneği sunar. Model çıkarımı için bunlardan herhangi birini kullanabilirsiniz. Yeteneklerin ve maliyetlerin tam bir dökümü için [N serisi VM 'lerin listesine](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) bakın.

Aşağıdaki kod, çalışma alanınız için nasıl yeni bir AKS kümesi oluşturulacağını göstermektedir:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure, AKS kümesi mevcut olduğu sürece sizi faturalandıracaktır. İle işiniz bittiğinde AKS kümenizi sildiğinizden emin olun.

Azure Machine Learning ile AKS kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmetine dağıtım](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Giriş betiğini yazın

Giriş betiği Web hizmetine gönderilen verileri alır, modele geçirir ve Puanlama sonuçlarını döndürür. Aşağıdaki betik, başlangıçta TensorFlow modelini yükler ve ardından modeli kullanarak verileri puan alır.

> [!TIP]
> Giriş betiği, modelinize göre değişir. Örneğin, komut dosyası modelinizle, veri biçimlerinizin vb. ile kullanılacak çerçeveyi bilmelidir.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Bu dosya adı `score.py` . Giriş betikleri hakkında daha fazla bilgi için bkz. [nasıl ve nereye dağıtılacak](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Conda ortamını tanımlama

Conda ortamı dosyası hizmetin bağımlılıklarını belirtir. Hem model hem de giriş betiği için gereken bağımlılıkları içerir. Lütfen, modeli bir Web hizmeti olarak barındırmak için gereken işlevleri içerdiğinden, >= 1.0.45 = ile azureml-varsayılan değeri bir PIP bağımlılığı olarak belirtmeniz gerektiğini unutmayın. Aşağıdaki YAML, bir TensorFlow modeli için ortamı tanımlar. `tensorflow-gpu`Bu dağıtımda kullanılan GPU 'yu kullanacak şekilde belirtir:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

Bu örnekte, dosya olarak kaydedilir `myenv.yml` .

## <a name="define-the-deployment-configuration"></a>Dağıtım yapılandırmasını tanımlama

> [!IMPORTANT]
> AKS 'ler, nesnelerin GPU 'ları paylaşmasına izin vermediğinden, kümede GPU 'lar olduğu için GPU özellikli bir Web hizmetinin yalnızca birçok çoğaltmasını kullanabilirsiniz.

Dağıtım yapılandırması, Web hizmetini çalıştırmak için kullanılan Azure Kubernetes hizmet ortamını tanımlar:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Daha fazla bilgi için bkz. [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)için başvuru belgeleri.

## <a name="define-the-inference-configuration"></a>Çıkarım yapılandırmasını tanımlama

Çıkarım yapılandırması, giriş betiğine ve GPU desteğiyle bir Docker görüntüsü kullanan bir ortam nesnesine işaret eder. Lütfen ortam tanımı için kullanılan YAML dosyasının, modeli bir Web hizmeti olarak barındırmak için gereken işlevleri içerdiğinden, bir PIP bağımlılığı olarak >= 1.0.45 ile birlikte

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Ortamlar hakkında daha fazla bilgi için bkz. [eğitim ve dağıtım için ortamları oluşturma ve yönetme](how-to-use-environments.md).
Daha fazla bilgi için bkz. [ınenceconfig](/python/api/azureml-core/azureml.core.model.inferenceconfig)için başvuru belgeleri.

## <a name="deploy-the-model"></a>Modeli dağıtma

Modeli AKS kümenize dağıtın ve hizmetinizi oluşturmak için bekleyin.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

Daha fazla bilgi için bkz. [model](/python/api/azureml-core/azureml.core.model.model)için başvuru belgeleri.

## <a name="issue-a-sample-query-to-your-service"></a>Hizmetinize örnek bir sorgu verme

Dağıtılan modele bir test sorgusu gönderin. Modele bir JPEG görüntüsü gönderdiğinizde, görüntüde puan alır. Aşağıdaki kod örneği test verilerini indirir ve ardından hizmete göndermek için rastgele bir test görüntüsü seçer.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

İstemci uygulaması oluşturma hakkında daha fazla bilgi için bkz. [dağıtılan Web hizmetini kullanmak için Istemci oluşturma](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>Kaynakları temizleme

Bu örnek için AKS kümesini özel olarak oluşturduysanız, işiniz bittiğinde kaynakları silin.

> [!IMPORTANT]
> Azure, AKS kümesinin ne kadar süreyle dağıtıldığını temel alır. Bu işlemi tamamladıktan sonra temizlediğinizden emin olun.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Sonraki adımlar

* [FPGA 'da model dağıtma](how-to-deploy-fpga-web-service.md)
* [Modeli ONNX ile Dağıt](concept-onnx.md#deploy-onnx-models-in-azure)
* [TensorFlow DNN modellerini eğitme](how-to-train-tensorflow.md)
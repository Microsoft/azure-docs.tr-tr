---
title: GPU ile çıkarım için model dağıtma
titleSuffix: Azure Machine Learning
description: Bu makalede, GPU özellikli bir TensorFlow derin öğrenme modelini Web hizmeti olarak dağıtmak için Azure Machine Learning kullanma öğretilir. hizmet ve puan çıkarma istekleri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: 2e088557bf61141d3ea3cbeb25d53f711a71fd97
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496854"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU ile çıkarım için derin öğrenme modeli dağıtma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, GPU özellikli bir modeli bir Web hizmeti olarak dağıtmak için Azure Machine Learning kullanma öğretilir. Bu makaledeki bilgiler, Azure Kubernetes Service (AKS) üzerinde bir model dağıtmaya dayalıdır. AKS kümesi, çıkarım için model tarafından kullanılan bir GPU kaynağı sağlar.

Çıkarım veya model Puanlama, dağıtılan modelin tahminleri yapmak için kullanıldığı aşamadır. CPU yerine GPU 'Ları kullanmak, yüksek bir paralelleştirilebilir hesaplama üzerinde performans avantajları sunar.

> [!IMPORTANT]
> Web hizmeti dağıtımları için GPU çıkarımı yalnızca Azure Kubernetes hizmetinde desteklenir. __Makine öğrenimi ardışık düzeni__kullanan çıkarım için, GPU 'lar yalnızca Azure Machine Learning işlem sırasında desteklenir. ML işlem hatlarını kullanma hakkında daha fazla bilgi için bkz. [Batch tahminleri çalıştırma](how-to-run-batch-predictions.md). 

> [!TIP]
> Bu makaledeki kod parçacıkları bir TensorFlow modelini görebilse de, bilgileri GPU 'ları destekleyen herhangi bir Machine Learning çerçevesine uygulayabilirsiniz.

> [!NOTE]
> Bu makaledeki bilgiler, [Azure Kubernetes hizmetine dağıtma](how-to-deploy-azure-kubernetes-service.md) makalesindeki bilgileri oluşturur. Bu makalede genellikle AKS 'e dağıtımı ele alınmaktadır ve bu makalede GPU 'ya özgü dağıtım ele alınmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

* Azure Machine Learning SDK yüklü bir Python geliştirme ortamı. Daha fazla bilgi için bkz. [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* GPU kullanan kayıtlı bir model.

    * Modellerin nasıl kaydedileceği hakkında bilgi edinmek için bkz. [modelleri dağıtma](../service/how-to-deploy-and-where.md#registermodel).

    * Bu belgeyi oluşturmak için kullanılan TensorFlow modelini oluşturmak ve kaydetmek için bkz. [bir TensorFlow modelini eğitme](how-to-train-tensorflow.md).

* [Modellerin nasıl ve nereye dağıtılacağını](how-to-deploy-and-where.md)gösteren genel bir anlama.

## <a name="connect-to-your-workspace"></a>Çalışma alanınıza bağlanın

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
> Giriş betiği modelinize özeldir. Örneğin, komut dosyası modelinizle, veri biçimlerinizin vb. ile kullanılacak çerçeveyi bilmelidir.

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
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Bu dosya `score.py`olarak adlandırılmıştır. Giriş betikleri hakkında daha fazla bilgi için bkz. [nasıl ve nereye dağıtılacak](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Conda ortamını tanımlama

Conda ortamı dosyası hizmetin bağımlılıklarını belirtir. Hem model hem de giriş betiği için gereken bağımlılıkları içerir. Aşağıdaki YAML, bir TensorFlow modeli için ortamı tanımlar. Bu dağıtımda kullanılan GPU 'YU kullanacak olan `tensorflow-gpu`belirtir:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

Bu örnekte, dosya `myenv.yml`olarak kaydedilir.

## <a name="define-the-deployment-configuration"></a>Dağıtım yapılandırmasını tanımlama

Dağıtım yapılandırması, Web hizmetini çalıştırmak için kullanılan Azure Kubernetes hizmet ortamını tanımlar:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Daha fazla bilgi için, bkz. [Aksservice. deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)için başvuru belgeleri.

## <a name="define-the-inference-configuration"></a>Çıkarım yapılandırmasını tanımlama

Çıkarım yapılandırması, giriş betiğine ve Conda ortam dosyasına işaret eder. Ayrıca, Web hizmeti için oluşturulan Docker görüntüsüne CUDA yüklenen GPU desteğini de sunar:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml",
                                   enable_gpu=True)
```

Daha fazla bilgi için bkz. [ınenceconfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)için başvuru belgeleri.

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

> [!NOTE]
> `InferenceConfig` nesnesi `enable_gpu=True`varsa `deployment_target` parametresi GPU sağlayan bir kümeye başvurmalıdır. Aksi takdirde, dağıtım başarısız olur.

Daha fazla bilgi için bkz. [model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)için başvuru belgeleri.

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

## <a name="clean-up-the-resources"></a>Kaynakları Temizleme

Bu örnek için AKS kümesini özel olarak oluşturduysanız, işiniz bittiğinde kaynakları silin.

> [!IMPORTANT]
> Azure, AKS kümesinin ne kadar süreyle dağıtıldığını temel alır. Bu işlemi tamamladıktan sonra temizlediğinizden emin olun.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Sonraki adımlar

* [FPGA 'da model dağıtma](../service/how-to-deploy-fpga-web-service.md)
* [Modeli ONNX ile Dağıt](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [TensorFlow DNN modellerini eğitme](../service/how-to-train-tensorflow.md)

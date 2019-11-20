---
title: Derin öğrenme sinir ağını TensorFlow ile eğitme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning kullanarak, TensorFlow eğitim betiklerini nasıl bir ölçekte çalıştırmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 4a055e039e8d7629f3ff1c20c6ce9e4f1533b6b9
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931041"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Azure Machine Learning ile bir TensorFlow derin öğrenme modeli oluşturun
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning [TensorFlow tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) sınıfını kullanarak [TensorFlow](https://www.tensorflow.org/overview) eğitim betiklerinizi ölçeklendirerek nasıl çalıştırabileceğiniz gösterilmektedir. Bu örnek, derin sinir ağı (DNN) kullanarak el ile yapılan rakamları sınıflandırmak için bir TensorFlow modeli kaydeder ve kaydettirir.

Baştan sona akış modeli geliştirmenize veya [mevcut bir modeli](how-to-deploy-existing-model.md) buluta verdiğinize göre, üretim sınıfı modellerini derlemek, dağıtmak, sürüm ve izlemek üzere açık kaynaklı eğitim işlerini ölçeklendirmek için Azure Machine Learning kullanabilirsiniz .

[Derin öğrenme ve makine öğrenimi](concept-deep-learning-vs-machine-learning.md)hakkında daha fazla bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

Bu kodu şu ortamlardan birinde çalıştırın:

 - Azure Machine Learning Not defteri VM-indirme veya yükleme gerekli değil

     - Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md) .
    - Not defteri sunucusundaki örnekler derin öğrenimi klasöründe, bu dizine giderek tamamlanmış ve genişletilmiş bir not defteri bulabilirsiniz: **nasıl kullanılır-azureml > ml-çerçeveleri > TensorFlow > dağıtımı > Tren-hyperparameter-ayarla-dağıt-TensorFlow** klasörü. 
 
 - Kendi Jupyter Notebook sunucunuz

    - [Azure Machine Learning SDK 'Sını yükler](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).
    - [Örnek betik dosyalarını indirin](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` ve `utils.py`
     
    Ayrıca, bu kılavuzun tamamlanmış bir [Jupyter Notebook sürümünü](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) GitHub örnekleri sayfasından bulabilirsiniz. Not defteri, akıllı hiper parametre ayarlamayı, model dağıtımını ve Not defteri pencere öğelerini kapsayan genişletilmiş bölümler içerir.

## <a name="set-up-the-experiment"></a>Denemeyi ayarlama

Bu bölüm, gerekli Python paketlerini yükleyerek, bir çalışma alanı başlatarak, bir deneme oluşturarak ve eğitim verilerini ve eğitim betikleri karşıya yükleyerek Eğitim denemesini ayarlar.

### <a name="import-packages"></a>Paketleri içeri aktarma

İlk olarak, gerekli Python kitaplıklarını içeri aktarın.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Çalışma alanını başlatma

[Azure Machine Learning çalışma alanı](concept-workspace.md) , hizmet için en üst düzey kaynaktır. Oluşturduğunuz tüm yapıtlarla çalışmak için merkezi bir yer sağlar. Python SDK 'sında, [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) nesnesi oluşturarak çalışma alanı yapılarına erişebilirsiniz.

[Önkoşullar bölümünde](#prerequisites)oluşturulan `config.json` dosyasından bir çalışma alanı nesnesi oluşturun.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Derin öğrenme denemesi oluşturun

Eğitim betiklerinizi tutmak için bir deneme ve bir klasör oluşturun. Bu örnekte, "TF-mnist" adlı bir deneme oluşturun.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Dosya veri kümesi oluşturma

`FileDataset` nesne, çalışma alanı veri deposundaki veya genel URL 'lerdeki bir veya birden çok dosyaya başvurur. Dosyalar herhangi bir biçimde olabilir ve sınıfı, size dosyaları indirme veya işleme özelliğini sağlar. Bir `FileDataset`oluşturarak, veri kaynağı konumuna bir başvuru oluşturursunuz. Veri kümesine herhangi bir dönüştürme uyguladıysanız, bunlar veri kümesinde da depolanır. Veriler mevcut konumunda kalır, bu nedenle ek depolama maliyeti tahakkuk etmemesi gerekir. Daha fazla bilgi için `Dataset` paketindeki [nasıl yapılır](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets) kılavuzuna bakın.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Veri kümesini çalışma alanınıza kaydetmek için `register()` yöntemini kullanın, böylece diğer denemeleri arasında yeniden kullanılabilir ve eğitim betiğinizdeki ada göre başvuruda bulunulabilir.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>İşlem hedefi oluştur

Açık iş akışı işinizin üzerinde çalışacağı bir işlem hedefi oluşturun. Bu örnekte, GPU özellikli Azure Machine Learning işlem kümesi oluşturun.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

İşlem hedefleri hakkında daha fazla bilgi için bkz. [işlem hedefi nedir](concept-compute-target.md) makalesi.

## <a name="create-a-tensorflow-estimator"></a>TensorFlow tahmin aracı oluşturma

[TensorFlow tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) , bir işlem hedefinde bir TensorFlow eğitim işi başlatmanın basit bir yolunu sağlar.

TensorFlow tahmin aracı, herhangi bir çerçeveyi desteklemek için kullanılabilen genel [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) sınıfı aracılığıyla uygulanır. Genel tahmin aracı kullanan eğitim modelleri hakkında daha fazla bilgi için bkz. [tahmin aracı kullanarak Azure Machine Learning modelleri eğitme](how-to-train-ml-models.md)

Eğitim betiğinizin çalışması için ek PIP veya Conda paketleri gerekiyorsa, bu paketlerin adlarını `pip_packages` ve `conda_packages` bağımsız değişkenlerle geçirerek elde edilen Docker görüntüsüne yüklenmesini sağlayabilirsiniz.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

> [!TIP]
> **TensorFlow 2,0** için destek, TensorFlow tahmin aracı sınıfına eklenmiştir. Daha fazla bilgi için [blog gönderisine](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) bakın.

## <a name="submit-a-run"></a>Bir çalıştırma gönder

[Run nesnesi](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) , iş çalışırken ve tamamlandıktan sonra çalışma geçmişi için arabirim sağlar.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Çalıştırma yürütüldüğü için aşağıdaki aşamalardan geçer:

- **Hazırlama**: bir Docker görüntüsü, TensorFlow Estimator öğesine göre oluşturulur. Görüntü, çalışma alanının kapsayıcı kayıt defterine yüklenir ve daha sonra çalışacak şekilde önbelleğe alınır. Günlükler, çalıştırma geçmişine de kaydedilir ve ilerlemeyi izlemek için görüntülenebilir.

- **Ölçeklendirme**: Batch AI kümesi, çalışmayı yürütmek için daha fazla düğüm gerektiriyorsa, küme ölçeği büyütmeyi dener ve şu anda kullanılabilir.

- **Çalışıyor**: betik klasöründeki tüm betikler işlem hedefine yüklenir, veri depoları bağlanır veya kopyalanır ve entry_script yürütülür. Stdout ve./logs klasörü çıkışları, çalıştırma geçmişine akışla kaydedilir ve çalıştırmayı izlemek için kullanılabilir.

- **Işlem sonrası**: çalıştırmanın./çıktılar klasörü, çalışma geçmişine kopyalanır.

## <a name="register-or-download-a-model"></a>Bir modeli kaydetme veya indirme

Modeli eğittikten sonra, çalışma alanınıza kaydedebilirsiniz. Model kaydı, [model yönetimi ve dağıtımını](concept-model-management-and-deployment.md)basitleştirmek için modellerinizi çalışma alanınızda depolamanızı ve sürümlerini oluşturmanıza imkan tanır. `model_framework`, `model_framework_version`ve `resource_configuration`parametreleri belirterek, hiçbir kod modeli dağıtımı kullanılabilir hale gelir. Bu, modelinizi kayıtlı modelden doğrudan bir Web hizmeti olarak dağıtmanızı sağlar ve `ResourceConfiguration` nesnesi Web hizmeti için işlem kaynağını tanımlar.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Ayrıca, Çalıştır nesnesini kullanarak modelin yerel bir kopyasını indirebilirsiniz. Eğitim betiğinin `mnist-tf.py`, bir TensorFlow koruyucu nesnesi modeli yerel bir klasöre (yerel olarak işlem hedefine) devam ettirir. Bir kopyasını indirmek için Run nesnesini kullanabilirsiniz.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Dağıtılmış eğitim

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) tahmin aracı Ayrıca CPU ve GPU kümelerinde dağıtılmış eğitimi destekler. Dağıtılmış TensorFlow işlerini kolayca çalıştırabilirsiniz ve Azure Machine Learning düzenleme sizin için yönetecektir.

Azure Machine Learning, TensorFlow 'da iki dağıtılmış eğitim yöntemini destekler:

- [Horovod](https://github.com/uber/horovod) çerçevesini kullanarak [MPI tabanlı](https://www.open-mpi.org/) dağıtılmış eğitim
- Parameter Server metodunu kullanarak yerel [Dağıtılmış TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) , Uber tarafından geliştirilen dağıtılmış eğitime yönelik açık kaynaklı bir çerçevedir. Dağıtılmış GPU TensorFlow işlerinin kolay bir yolunu sunar.

Horovod 'yi kullanmak için, TensorFlow oluşturucusunda `distributed_training` parametresi için bir [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) nesnesi belirtin. Bu parametre, eğitim betiğinizdeki kullanabileceğiniz Horovod kitaplığının yüklenmesini sağlar.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Parametre sunucusu

Ayrıca, parametre sunucu modelini kullanan [Yerel dağıtılmış TensorFlow](https://www.tensorflow.org/deploy/distributed)' u de çalıştırabilirsiniz. Bu yöntemde, bir parametre sunucuları ve çalışanları kümesi üzerinde eğitebilirsiniz. Çalışanlar, eğitim sırasında degradeleri hesaplar, ancak parametre sunucuları degradeleri toplar.

Parameter Server metodunu kullanmak için, TensorFlow oluşturucusunda `distributed_training` parametresi için bir [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) nesnesi belirtin.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>' TF_CONFIG ' içinde küme belirtimlerini tanımlayın

Ayrıca, [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)kümenin ağ adresleri ve bağlantı noktaları da gerekir, Azure Machine Learning `TF_CONFIG` ortam değişkenini sizin için ayarlar.

`TF_CONFIG` ortam değişkeni bir JSON dizesidir. Bir parametre sunucusunun değişkenine bir örnek aşağıda verilmiştir:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

TensorFlow 'un yüksek düzey [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API 'si Için, tensorflow `TF_CONFIG` değişkenini ayrıştırır ve küme belirtimini sizin için oluşturur.

TensorFlow 'un eğitim için alt düzey çekirdek API 'Leri için `TF_CONFIG` değişkenini ayrıştırın ve eğitim kodunuzda `tf.train.ClusterSpec` oluşturun.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deployment"></a>Dağıtım

Yeni Kaydolmakta olduğunuz model, eğitim için kullandığınız tahmin etmeksizin, Azure Machine Learning ' deki diğer tüm kayıtlı modellerle tamamen aynı şekilde dağıtılabilir. Dağıtım nasıl yapılır, model kaydettirme hakkında bir bölüm içerir, ancak zaten kayıtlı bir modeliniz olduğundan, dağıtım için doğrudan [bir işlem hedefi oluşturmaya](how-to-deploy-and-where.md#choose-a-compute-target) geçebilirsiniz.

### <a name="preview-no-code-model-deployment"></a>Önizle Kod olmayan model dağıtımı

Geleneksel dağıtım yolu yerine, TensorFlow için kod içermeyen dağıtım özelliğini (Önizleme) de kullanabilirsiniz. Modelinizi yukarıda gösterildiği gibi `model_framework`, `model_framework_version`ve `resource_configuration` parametreleriyle kaydederek, modelinizi dağıtmak için `deploy()` static işlevini kullanmanız yeterlidir.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Daha ayrıntılı Azure Machine Learning dağıtımı [nasıl](how-to-deploy-and-where.md) ele alır.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir TensorFlow modeli eğitilmiş ve kaydettiniz ve dağıtım seçenekleri hakkında bilgi edindiniz. Azure Machine Learning hakkında daha fazla bilgi edinmek için bu makaleye bakın.

* [Eğitim sırasında çalıştırma ölçümlerini izleyin](how-to-track-experiments.md)
* [Hiper parametreleri ayarla](how-to-tune-hyperparameters.md)
* [Azure 'da dağıtılmış derin öğrenme eğitimi için başvuru mimarisi](/azure/architecture/reference-architectures/ai/training-deep-learning)

---
title: Bir TensorFlow modelini eğitme ve dağıtma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning, elastik bulut bilgi işlem kaynaklarını kullanarak bir TensorFlow eğitim işini ölçeklendirmenizi nasıl sağlayacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0eac999fa53679ef67c2a322bd8cc7841197d493
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819151"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>TensorFlow modellerini Azure Machine Learning ölçeklendirerek eğitme

Bu makalede, Azure Machine Learning kullanarak, [TensorFlow](https://www.tensorflow.org/overview) eğitim betiklerinizi nasıl bir ölçekte çalıştıracağınızı öğrenin.

Bu örnek, derin sinir ağı (DNN) kullanarak el ile yapılan rakamları sınıflandırmak için bir TensorFlow modeli kaydeder ve kaydettirir.

Baştan sona bir iş akışı modeli geliştirmenize veya [mevcut bir modeli](how-to-deploy-existing-model.md) buluta verdiğinize göre, üretim sınıfı modellerini derlemek, dağıtmak, sürüm ve izlemek üzere açık kaynaklı eğitim işlerini ölçeklendirmek için Azure Machine Learning kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu kodu şu ortamlardan birinde çalıştırın:

 - Azure Machine Learning işlem örneği-indirme veya yükleme gerekli değil

     - Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md) .
    - Not defteri sunucusundaki örnekler derin öğrenimi klasöründe, bu dizine giderek tamamlanmış ve genişletilmiş bir not defteri bulun: **nasıl kullanılır-azureml > ml-çerçeveleri > tensorflow > tren-hyperparameter-ayarla-dağıt-with-TensorFlow** klasörü. 
 
 - Kendi Jupyter Notebook sunucunuz

    - [Azure Machine Learning SDK 'sını](/python/api/overview/azure/ml/install) (>= 1.15.0) yükler.
    - [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).
    - [Örnek betik dosyalarını indirin](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` ' `utils.py`
     
    Ayrıca, bu kılavuzun tamamlanmış bir [Jupyter Notebook sürümünü](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) GitHub örnekleri sayfasından bulabilirsiniz. Not defteri, akıllı hiper parametre ayarlamayı, model dağıtımını ve Not defteri pencere öğelerini kapsayan genişletilmiş bölümler içerir.

## <a name="set-up-the-experiment"></a>Denemeyi ayarlama

Bu bölüm, gerekli Python paketlerini yükleyerek, bir çalışma alanı başlatarak, işlem hedefini oluşturarak ve eğitim ortamını tanımlayarak Eğitim denemesini ayarlar.

### <a name="import-packages"></a>Paketleri içeri aktarma

İlk olarak, gerekli Python kitaplıklarını içeri aktarın.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Çalışma alanını başlatma

[Azure Machine Learning çalışma alanı](concept-workspace.md) , hizmet için en üst düzey kaynaktır. Oluşturduğunuz tüm yapıtlarla çalışmak için merkezi bir yer sağlar. Python SDK 'sında bir nesne oluşturarak çalışma alanı yapıtlarına erişebilirsiniz [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) .

`config.json` [Önkoşullar bölümünde](#prerequisites)oluşturulan dosyadan bir çalışma alanı nesnesi oluşturun.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Dosya veri kümesi oluşturma

`FileDataset`Nesne, çalışma alanı veri deposundaki veya genel URL 'lerdeki bir veya birden çok dosyaya başvurur. Dosyalar herhangi bir biçimde olabilir ve sınıfı, size dosyaları indirme veya işleme özelliğini sağlar. Bir oluşturarak `FileDataset` , veri kaynağı konumuna bir başvuru oluşturursunuz. Veri kümesine herhangi bir dönüştürme uyguladıysanız, bunlar veri kümesinde da depolanır. Veriler mevcut konumunda kalır, bu nedenle ek depolama maliyeti tahakkuk etmemesi gerekir. [](./how-to-create-register-datasets.md) `Dataset` Daha fazla bilgi için bkz. paketteki nasıl yapılır Kılavuzu.

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

`register()`Veri kümesini çalışma alanınıza kaydetmek için yöntemini kullanın, böylece diğer denemeleri arasında yeniden kullanılabilir ve eğitim betiğinizdeki ada göre adlandırılır.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>İşlem hedefi oluştur

Açık iş akışı işinizin üzerinde çalışacağı bir işlem hedefi oluşturun. Bu örnekte, GPU özellikli Azure Machine Learning işlem kümesi oluşturun.

```Python
cluster_name = "gpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

İşlem hedefleri hakkında daha fazla bilgi için bkz. [işlem hedefi nedir](concept-compute-target.md) makalesi.

### <a name="define-your-environment"></a>Ortamınızı tanımlama

Eğitim betiğinin bağımlılıklarını kapsülleyen Azure ML [ortamını](concept-environments.md) tanımlamak için özel bir ortam tanımlayabilir veya BIR Azure ML seçkin ortamı kullanabilirsiniz.

#### <a name="use-a-curated-environment"></a>Seçkin bir ortam kullanma
Azure ML, kendi ortamınızı tanımlamak istemiyorsanız önceden oluşturulmuş, seçkin ortamlar sağlar. Azure ML 'nin, TensorFlow 'un farklı sürümlerine karşılık gelen TensorFlow için birkaç CPU ve GPU seçkin ortamı vardır. Daha fazla bilgi için [buraya](resource-curated-environments.md)bakın.

Seçkin bir ortam kullanmak istiyorsanız, bunun yerine aşağıdaki komutu çalıştırabilirsiniz:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Seçkin ortama dahil olan paketleri görmek için Conda bağımlılıklarını diske yazabilirsiniz:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Seçkin ortamın eğitim betiğinizin gerektirdiği tüm bağımlılıkları içerdiğinden emin olun. Aksi takdirde, ortamı eksik bağımlılıkları içerecek şekilde değiştirmeniz gerekir. Ortam değiştirilirse, ' AzureML ' öneki, seçkin ortamlar için ayrıldığından, buna yeni bir ad vermeniz gerekeceğini unutmayın. Conda Dependencies YAML dosyasını değiştirdiyseniz, yeni bir adla yeni bir ortam oluşturabilirsiniz, örneğin:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Bunun yerine, seçkin ortam nesnesini doğrudan değiştirdiyseniz, bu ortamı yeni bir adla klonlayabilirsiniz:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Özel ortam oluşturma

Ayrıca, eğitim betiğinin bağımlılıklarını kapsülleyen kendi Azure ML ortamınızı de oluşturabilirsiniz.

İlk olarak, Conda bağımlılıklarınızı bir YAML dosyasında tanımlayın; Bu örnekte, dosyanın adı `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Bu Conda ortam belirtiminden bir Azure ML ortamı oluşturun. Ortam, çalışma zamanında bir Docker kapsayıcısı olarak paketlenir.

Varsayılan olarak, bir temel görüntü belirtilmemişse Azure ML, temel görüntü olarak bir CPU görüntüsü kullanacaktır `azureml.core.environment.DEFAULT_CPU_IMAGE` . Bu örnek, bir GPU kümesinde eğitim yaptığından, gerekli GPU sürücüleri ve bağımlılıkları olan bir GPU temel görüntüsü belirtmeniz gerekecektir. Azure ML, Microsoft Container Registry (MCR) üzerinde yayınlanan bir temel görüntü kümesini saklar. daha fazla bilgi için bkz. [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) GitHub deposu.

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> İsteğe bağlı olarak, yalnızca tüm bağımlılıklarınızı doğrudan özel bir Docker görüntüsü veya Dockerfile içinde yakalayabilir ve ortamınızı bundan oluşturabilirsiniz. Daha fazla bilgi için bkz. [özel görüntüyle eğitme](how-to-train-with-custom-image.md).

Ortamları oluşturma ve kullanma hakkında daha fazla bilgi için, bkz. [Azure Machine Learning yazılım ortamları oluşturma ve kullanma](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Eğitim çalıştırmanızı yapılandırma ve gönderme

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig oluşturma

Eğitim betiğinizi, kullanılacak ortamı ve üzerinde çalıştırılacak işlem hedefini de içeren eğitim işinizin yapılandırma ayrıntılarını belirtmek için bir [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) nesnesi oluşturun. Eğitim betiğinizdeki bağımsız değişkenler, parametresinde belirtilmişse komut satırı aracılığıyla geçirilir `arguments` .

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning, tüm kaynak dizinini kopyalayarak eğitim betikleri çalıştırır. Karşıya yüklemek istemediğiniz gizli verileriniz varsa, bir [. Ignore dosyası](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) kullanın veya kaynak dizine eklemeyin. Bunun yerine, bir Azure ML [veri kümesi](how-to-train-with-datasets.md)kullanarak verilerinize erişin.

ScriptRunConfig ile işleri yapılandırma hakkında daha fazla bilgi için bkz. [eğitim çalıştırmalarını yapılandırma ve gönderme](how-to-set-up-training-targets.md).

> [!WARNING]
> Daha önce TensorFlow eğitim işlerinizi yapılandırmak için TensorFlow tahmin Aracı ' ı kullandıysanız, 1.19.0 SDK sürümü itibariyle estimators 'ın kullanım dışı olduğuna lütfen emin olun. Azure ML SDK ile >= 1.15.0, ScriptRunConfig, derinlemesine öğrenme çerçeveleri kullanan eğitim işlerini yapılandırmak için önerilen yoldur. Yaygın geçiş soruları için bkz. [Estimator to ScriptRunConfig Migration Guide](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-a-run"></a>Bir çalıştırma gönder

[Run nesnesi](/python/api/azureml-core/azureml.core.run%28class%29) , iş çalışırken ve tamamlandıktan sonra çalışma geçmişi için arabirim sağlar.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Çalıştırma yürütme sırasında ne olur?
Çalıştırma yürütüldüğü için aşağıdaki aşamalardan geçer:

- **Hazırlama**: bir Docker görüntüsü, tanımlanan ortama göre oluşturulur. Görüntü, çalışma alanının kapsayıcı kayıt defterine yüklenir ve daha sonra çalışacak şekilde önbelleğe alınır. Günlükler, çalıştırma geçmişine de kaydedilir ve ilerlemeyi izlemek için görüntülenebilir. Bunun yerine, seçkin bir ortam belirtilmişse, bu eklenen ortamı destekleyen önbelleğe alınmış görüntü kullanılacaktır.

- **Ölçeklendirme**: Batch AI kümesi, çalışmayı yürütmek için daha fazla düğüm gerektiriyorsa, küme ölçeği büyütmeyi dener ve şu anda kullanılabilir.

- **Çalışıyor**: betik klasöründeki tüm betikler işlem hedefine yüklenir, veri depoları bağlanır veya kopyalanır ve `script` yürütülür. Stdout ve **./logs** klasörü çıkışları, çalıştırma geçmişine akışla kaydedilir ve çalıştırmayı izlemek için kullanılabilir.

- **Işlem sonrası**: çalıştırmanın **./çıktılar** klasörü, çalışma geçmişine kopyalanır.

## <a name="register-or-download-a-model"></a>Bir modeli kaydetme veya indirme

Modeli eğittikten sonra, çalışma alanınıza kaydedebilirsiniz. Model kaydı, [model yönetimi ve dağıtımını](concept-model-management-and-deployment.md)basitleştirmek için modellerinizi çalışma alanınızda depolamanızı ve sürümlerini oluşturmanıza imkan tanır. İsteğe bağlı:, ve parametrelerini belirterek,, `model_framework` `model_framework_version` ve `resource_configuration` , No-Code Model dağıtımı kullanılabilir hale gelir. Bu, modelinizi kayıtlı modelden doğrudan bir Web hizmeti olarak dağıtmanızı sağlar ve `ResourceConfiguration` nesne, Web hizmeti için işlem kaynağını tanımlar.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Ayrıca, Çalıştır nesnesini kullanarak modelin yerel bir kopyasını indirebilirsiniz. Eğitim betiğinde `tf_mnist.py` , bir TensorFlow koruyucu nesnesi modeli yerel bir klasöre (yerel olarak işlem hedefine) devam ettirir. Bir kopyasını indirmek için Run nesnesini kullanabilirsiniz.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Dağıtılmış eğitim

Azure Machine Learning Ayrıca, eğitim iş yüklerinizi ölçeklendirebilmeniz için çok düğümlü dağıtılmış TensorFlow işlerini destekler. Dağıtılmış TensorFlow işlerini kolayca çalıştırabilirsiniz ve Azure ML, düzenleme işini sizin için yönetecektir.

Azure ML, hem Horovod hem de TensorFlow 'un yerleşik dağıtılmış eğitim API 'SI ile dağıtılmış TensorFlow işlerinin çalıştırılmasını destekler.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) , bir açık kaynak, hepsi Uber tarafından geliştirilen dağıtılmış eğitimin çerçevesini azaltır. Eğitim için dağıtılmış TensorFlow kodu yazmak için kolay bir yol sunar.

Eğitim kodunuzun, dağıtılmış eğitim için Horovod ile işaretlenmiş olması gerekir. TensorFlow ile Horovod kullanma hakkında daha fazla bilgi için Horovod belgelerine bakın:

TensorFlow ile Horovod kullanma hakkında daha fazla bilgi için Horovod belgelerine bakın:

* [TensorFlow ile horovod](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [TensorFlow 'un keras API 'SI ile horovod](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Ayrıca, eğitim ortamınızın **horovod** paketini içerdiğinden emin olun. Bir TensorFlow seçkin ortam kullanıyorsanız, horovod bağımlılıklardan biri olarak zaten dahil edilmiştir. Kendi ortamınızı kullanıyorsanız, horovod bağımlılığının eklendiğinden emin olun, örneğin:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Azure ML üzerinde MPı/Horovod kullanarak dağıtılmış bir işi yürütmek için, ScriptRunConfig oluşturucusunun parametresine bir [Mpicontısıdiation](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) belirtmeniz gerekir `distributed_job_config` . Aşağıdaki kod, düğüm başına bir işlem çalıştıran 2 düğümlü dağıtılmış bir işi yapılandırır. Düğüm başına birden çok işlem çalıştırmak istiyorsanız (örneğin, küme SKU 'sunda birden fazla GPU varsa), ek olarak, `process_count_per_node` Mpiconation (varsayılan değer) parametresinde parametreyi de belirtin `1` .

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Azure ML 'de Horovod ile dağıtılmış TensorFlow 'u çalıştırmaya ilişkin tam bir öğretici için bkz. [horovod Ile dağıtılmış TensorFlow](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>tf. dağıt

Eğitim kodunuzda [Yerel dağıtılmış TensorFlow](https://www.tensorflow.org/guide/distributed_training) kullanıyorsanız (örneğin, TensorFlow 2. x ' in API 'si), `tf.distribute.Strategy` Dağıtılmış işi Azure ML aracılığıyla da başlatabilirsiniz. 

Bunu yapmak için, ScriptRunConfig oluşturucusunun parametresine yönelik bir [Tensorflowconfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) belirtin `distributed_job_config` . Kullanıyorsanız, `tf.distribute.experimental.MultiWorkerMirroredStrategy` `worker_count` eğitim işinizin düğüm sayısına karşılık gelen TensorflowConfiguration içinde öğesini belirtin.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

TensorFlow 'da, `TF_CONFIG` ortam değişkeni birden çok makine üzerinde eğitim için gereklidir. Azure ML, `TF_CONFIG` eğitim betiğinizi yürütmeden önce değişkeni her çalışan için uygun şekilde yapılandırıp ayarlayacaktır. `TF_CONFIG`İle gerekirse eğitim betiğinizden erişebilirsiniz `os.environ['TF_CONFIG']` .

`TF_CONFIG`Bir baş çalışan düğümünde ayarlanan örnek yapı:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Eğitim betiğinizde dağıtılmış eğitim için parametre sunucusu stratejisi kullanılıyorsa (örneğin, eski TensorFlow 1. x için), işte kullanılacak parametre sunucusu sayısını da belirtmeniz gerekir, örn. `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>TensorFlow modeli dağıtma

Dağıtım nasıl yapılır, model kaydettirme hakkında bir bölüm içerir, ancak zaten kayıtlı bir modeliniz olduğundan, dağıtım için doğrudan [bir işlem hedefi oluşturmaya](how-to-deploy-and-where.md#choose-a-compute-target) geçebilirsiniz.

### <a name="preview-no-code-model-deployment"></a>Önizle Kod olmayan model dağıtımı

Geleneksel dağıtım yolu yerine, TensorFlow için kod içermeyen dağıtım özelliğini (Önizleme) de kullanabilirsiniz. Modelinizi yukarıda gösterildiği gibi `model_framework` ,, ve parametreleriyle kaydederek, `model_framework_version` `resource_configuration` `deploy()` modelinizi dağıtmak için yalnızca statik işlevi kullanabilirsiniz.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Daha ayrıntılı Azure Machine Learning dağıtımı [nasıl](how-to-deploy-and-where.md) ele alır.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir TensorFlow modeli eğitilmiş ve kaydettiniz ve dağıtım seçenekleri hakkında bilgi edindiniz. Azure Machine Learning hakkında daha fazla bilgi edinmek için bu makaleye bakın.

* [Eğitim sırasında çalıştırma ölçümlerini izleyin](how-to-log-view-metrics.md)
* [Hiperparametreleri ayarlama](how-to-tune-hyperparameters.md)
* [Azure 'da dağıtılmış derin öğrenme eğitimi için başvuru mimarisi](/azure/architecture/reference-architectures/ai/training-deep-learning)

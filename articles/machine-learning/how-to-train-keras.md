---
title: Derin öğrenme keras modellerini eğitme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning kullanarak TensorFlow 'da çalışan keras derin sinir ağ sınıflandırma modelini eğitme ve kaydetme hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 555ec90bbd73cee401f6f35aa04598792d2f24f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817155"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning ile keras modellerini ölçeklendirerek eğitme

Bu makalede, keras eğitim betiklerinizi Azure Machine Learning ile çalıştırmayı öğrenin.

Bu makaledeki örnek kod, Azure Machine Learning ile birlikte TensorFlow arka ucu kullanılarak oluşturulan keras sınıflandırma modelinin nasıl eğildiği ve kaydedileceği gösterilmektedir. Popüler [veri kümesini](http://yann.lecun.com/exdb/mnist/) kullanarak, [TensorFlow](https://www.tensorflow.org/overview)üzerinde çalışan [keras Python kitaplığı](https://keras.io) kullanılarak oluşturulan derin bir sinir ağı (DNN) kullanarak el yazısı rakamlarını sınıflandırın.

Keras, geliştirmeyi basitleştirmek için diğer popüler DNN çerçevelerinin üstünü çalıştırabilen üst düzey bir sinir ağ API 'sidir. Azure Machine Learning, esnek bulut işlem kaynaklarını kullanarak eğitim işlerini hızla ölçeklendirebilirsiniz. Ayrıca eğitim çalıştırmalarını, sürüm modellerinizi, modellerinizi dağıtmayı ve daha fazlasını izleyebilirsiniz.

Sıfırdan bir keras modeli geliştirirken veya var olan bir modeli buluta getiriyor Azure Machine Learning, üretime hazır modeller oluşturmanıza yardımcı olabilir.

> [!NOTE]
> Tek başına keras paketini değil TensorFlow 'da yerleşik olarak keras API **tf. keras** kullanıyorsanız, [TensorFlow modellerini eğitme](how-to-train-tensorflow.md)yerine bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu kodu şu ortamlardan birinde çalıştırın:

- Azure Machine Learning işlem örneği-indirme veya yükleme gerekli değil

     - Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md) .
    - Not defteri sunucusundaki örnekler klasöründe, bu dizine giderek tamamlanmış ve genişletilmiş bir not defteri bulun: **nasıl kullanılır-azureml > ml-çerçeveleri > keras > tren-hyperparameter-ayarla-dağıt-as-keras** klasörü.

 - Kendi Jupyter Notebook sunucunuz

    - [Azure Machine Learning SDK 'sını](/python/api/overview/azure/ml/install) (>= 1.15.0) yükler.
    - [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).
    - [Örnek betik dosyalarını indirin](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` ' `utils.py`

    Ayrıca, bu kılavuzun tamamlanmış bir [Jupyter Notebook sürümünü](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) GitHub örnekleri sayfasından bulabilirsiniz. Not defteri, akıllı hiper parametre ayarlamayı, model dağıtımını ve Not defteri pencere öğelerini kapsayan genişletilmiş bölümler içerir.

## <a name="set-up-the-experiment"></a>Denemeyi ayarlama

Bu bölüm, gerekli Python paketlerini yükleyerek, bir çalışma alanı başlatarak, giriş eğitim verileri için dosya veri kümesini oluşturarak, işlem hedefini oluşturarak ve eğitim ortamını tanımlayarak Eğitim denemesini ayarlar.

### <a name="import-packages"></a>Paketleri içeri aktarma

İlk olarak, gerekli Python kitaplıklarını içeri aktarın.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
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

`register()`Veri kümesini çalışma alanınıza kaydetmek için yöntemini kullanarak, diğer denemeleri arasında yeniden kullanılabilir ve eğitim betiğinizdeki ada göre başvuruda bulunulabilir.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>İşlem hedefi oluştur

Eğitim işinizin üzerinde çalışacağı bir işlem hedefi oluşturun. Bu örnekte, GPU özellikli Azure Machine Learning işlem kümesi oluşturun.

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

Eğitim betiğinin bağımlılıklarını kapsülleyen Azure ML [ortamını](concept-environments.md) tanımlayın.

İlk olarak, Conda bağımlılıklarınızı bir YAML dosyasında tanımlayın; Bu örnekte, dosyanın adı `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Bu Conda ortam belirtiminden bir Azure ML ortamı oluşturun. Ortam, çalışma zamanında bir Docker kapsayıcısı olarak paketlenir.

Varsayılan olarak, bir temel görüntü belirtilmemişse Azure ML, temel görüntü olarak bir CPU görüntüsü kullanacaktır `azureml.core.environment.DEFAULT_CPU_IMAGE` . Bu örnek, bir GPU kümesinde eğitim yaptığından, gerekli GPU sürücüleri ve bağımlılıkları olan bir GPU temel görüntüsü belirtmeniz gerekecektir. Azure ML, Microsoft Container Registry (MCR) üzerinde yayınlanan bir temel görüntü kümesini saklar. daha fazla bilgi için bkz. [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) GitHub deposu.

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Ortamları oluşturma ve kullanma hakkında daha fazla bilgi için, bkz. [Azure Machine Learning yazılım ortamları oluşturma ve kullanma](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Eğitim çalıştırmanızı yapılandırma ve gönderme

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig oluşturma
İlk olarak, sınıfını kullanarak çalışma alanı veri deposundan verileri alın `Dataset` .

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Eğitim betiğinizi, kullanılacak ortamı ve üzerinde çalıştırılacak işlem hedefini de içeren eğitim işinizin yapılandırma ayrıntılarını belirtmek için bir ScriptRunConfig nesnesi oluşturun.

Eğitim betiğinizdeki bağımsız değişkenler, parametresinde belirtilmişse komut satırı aracılığıyla geçirilir `arguments` . FileDataset için Datasettüketimptionconfig, bağımsız değişkeni olarak eğitim betiğine bir bağımsız değişken olarak geçirilir `--data-folder` . Azure ML, bu Datasettüketimptionconfig dosyasını, daha sonra eğitim betiğiyle erişilebilen, yedekleme veri deposunun bağlama noktasına çözirler.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

ScriptRunConfig ile işleri yapılandırma hakkında daha fazla bilgi için bkz. [eğitim çalıştırmalarını yapılandırma ve gönderme](how-to-set-up-training-targets.md).

> [!WARNING]
> Keras eğitim işlerinizi yapılandırmak için önceden TensorFlow tahmin aracı kullanıyorsanız, 1.19.0 SDK sürümü itibariyle estimators 'ın kullanım dışı olduğuna lütfen emin olun. Azure ML SDK ile >= 1.15.0, ScriptRunConfig, derinlemesine öğrenme çerçeveleri kullanan eğitim işlerini yapılandırmak için önerilen yoldur. Yaygın geçiş soruları için bkz. [Estimator to ScriptRunConfig Migration Guide](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-your-run"></a>Çalıştırmanızı gönder

[Run nesnesi](/python/api/azureml-core/azureml.core.run%28class%29) , iş çalışırken ve tamamlandıktan sonra çalışma geçmişi için arabirim sağlar.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Çalıştırma yürütme sırasında ne olur?
Çalıştırma yürütüldüğü için aşağıdaki aşamalardan geçer:

- **Hazırlama**: bir Docker görüntüsü, tanımlanan ortama göre oluşturulur. Görüntü, çalışma alanının kapsayıcı kayıt defterine yüklenir ve daha sonra çalışacak şekilde önbelleğe alınır. Günlükler, çalıştırma geçmişine de kaydedilir ve ilerlemeyi izlemek için görüntülenebilir. Bunun yerine, seçkin bir ortam belirtilmişse, bu eklenen ortamı destekleyen önbelleğe alınmış görüntü kullanılacaktır.

- **Ölçeklendirme**: Batch AI kümesi, çalışmayı yürütmek için daha fazla düğüm gerektiriyorsa, küme ölçeği büyütmeyi dener ve şu anda kullanılabilir.

- **Çalışıyor**: betik klasöründeki tüm betikler işlem hedefine yüklenir, veri depoları bağlanır veya kopyalanır ve `script` yürütülür. Stdout ve **./logs** klasörü çıkışları, çalıştırma geçmişine akışla kaydedilir ve çalıştırmayı izlemek için kullanılabilir.

- **Işlem sonrası**: çalıştırmanın **./çıktılar** klasörü, çalışma geçmişine kopyalanır.

## <a name="register-the-model"></a>Modeli Kaydet

Modeli eğittikten sonra, çalışma alanınıza kaydedebilirsiniz. Model kaydı, [model yönetimi ve dağıtımını](concept-model-management-and-deployment.md)basitleştirmek için modellerinizi çalışma alanınızda depolamanızı ve sürümlerini oluşturmanıza imkan tanır.

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> Dağıtım nasıl yapılır, model kaydettirme hakkında bir bölüm içerir, ancak zaten kayıtlı bir modeliniz olduğundan, dağıtım için doğrudan [bir işlem hedefi oluşturmaya](how-to-deploy-and-where.md#choose-a-compute-target) geçebilirsiniz.

Ayrıca modelin yerel bir kopyasını da indirebilirsiniz. Bu, ek model doğrulama işini yerel olarak gerçekleştirmek için yararlı olabilir. Eğitim betiğinde, `keras_mnist.py` bir TensorFlow koruyucu nesnesi modeli yerel bir klasöre (yerel olarak işlem hedefine) devam ettirir. Çalıştırma geçmişinden bir kopya indirmek için Run nesnesini kullanabilirsiniz.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Machine Learning bir keras modeli eğitildiniz ve kaydettiniz. Modeli dağıtmayı öğrenmek için model dağıtım makalemize devam edin.

* [Modellerin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md)
* [Eğitim sırasında çalıştırma ölçümlerini izleyin](how-to-log-view-metrics.md)
* [Hiperparametreleri ayarlama](how-to-tune-hyperparameters.md)
* [Eğitilen model dağıtma](how-to-deploy-and-where.md)
* [Azure 'da dağıtılmış derin öğrenme eğitimi için başvuru mimarisi](/azure/architecture/reference-architectures/ai/training-deep-learning)

---
title: Derin öğrenme keras modellerini eğitme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning kullanarak TensorFlow 'da çalışan keras derin sinir ağ sınıflandırma modelini eğitme ve kaydetme hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: a61b7e058e485121d87c5d3bb1050b4289e4ceed
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931063"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Azure Machine Learning ile keras sınıflandırma modelini eğitme ve kaydetme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning kullanarak TensorFlow 'da oluşturulan keras sınıflandırma modelinin nasıl eğildiği ve kaydedileceği gösterilmektedir. Popüler [veri kümesini](http://yann.lecun.com/exdb/mnist/) kullanarak, [TensorFlow](https://www.tensorflow.org/overview)üzerinde çalışan [keras Python kitaplığı](https://keras.io) kullanılarak oluşturulan derin bir sinir ağı (DNN) kullanarak el yazısı rakamlarını sınıflandırın.

Keras, geliştirmeyi basitleştirmek için diğer popüler DNN çerçevelerinin üstünü çalıştırabilen üst düzey bir sinir ağ API 'sidir. Azure Machine Learning, esnek bulut işlem kaynaklarını kullanarak eğitim işlerini hızla ölçeklendirebilirsiniz. Ayrıca eğitim çalıştırmalarını, sürüm modellerinizi, modellerinizi dağıtmayı ve daha fazlasını izleyebilirsiniz.

Sıfırdan bir keras modeli geliştirirken veya var olan bir modeli buluta getiriyor Azure Machine Learning, üretime hazır modeller oluşturmanıza yardımcı olabilir.

Machine Learning ve derin öğrenme arasındaki farklar hakkında bilgi edinmek için [kavramsal makaleye](concept-deep-learning-vs-machine-learning.md) bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu kodu şu ortamlardan birinde çalıştırın:

 - Azure Machine Learning Not defteri VM-indirme veya yükleme gerekli değil

     - Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md) .
    - Not defteri sunucusundaki örnekler klasöründe, bu dizine giderek tamamlanmış ve genişletilmiş bir not defteri bulun: **kullanımı nasıl kullanılır-azureml > eğitimi--derin öğrenme > tren-hyperparameter-ayarla-dağıt-i-keras** klasörü.

 - Kendi Jupyter Notebook sunucunuz

    - [Azure Machine Learning SDK 'Sını yükler](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).
    - [Örnek betik dosyalarını indirin](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` ve `utils.py`

    Ayrıca, bu kılavuzun tamamlanmış bir [Jupyter Notebook sürümünü](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) GitHub örnekleri sayfasından bulabilirsiniz. Not defteri, akıllı hiper parametre ayarlamayı, model dağıtımını ve Not defteri pencere öğelerini kapsayan genişletilmiş bölümler içerir.

## <a name="set-up-the-experiment"></a>Denemeyi ayarlama

Bu bölüm, gerekli Python paketlerini yükleyerek, bir çalışma alanı başlatarak, bir deneme oluşturarak ve eğitim verilerini ve eğitim betikleri karşıya yükleyerek Eğitim denemesini ayarlar.

### <a name="import-packages"></a>Paketleri içeri aktarma

İlk olarak, gerekli Python kitaplıklarını içeri aktarın.

```Python
import os
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

### <a name="create-an-experiment"></a>Deneme oluşturma

Çalışma alanınızda "keras-mnist" adlı bir deneme oluşturun.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>TensorFlow tahmin aracı oluşturma ve keras içeri aktarma

[TensorFlow tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) , işlem hedefinde TensorFlow eğitim işlerinin başlatılması için basit bir yol sağlar. Keras, TensorFlow üzerinde çalıştığından, TensorFlow tahmin Aracı ' ı kullanabilir ve `pip_packages` bağımsız değişkenini kullanarak keras kitaplığını içeri aktarabilirsiniz.

İlk olarak, `Dataset` sınıfını kullanarak çalışma alanı veri deposundan verileri alın.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow tahmin aracı, herhangi bir çerçeveyi desteklemek için kullanılabilen genel [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) sınıfı aracılığıyla uygulanır. Ayrıca, DNN hyperparameter ayarlarını içeren bir sözlük `script_params` oluşturun. Genel tahmin aracı kullanan eğitim modelleri hakkında daha fazla bilgi için bkz. [tahmin aracı kullanarak Azure Machine Learning modelleri eğitme](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

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

## <a name="register-the-model"></a>Modeli Kaydet

DNN modelini eğittikten sonra, çalışma alanınıza kaydedebilirsiniz. Model kaydı, [model yönetimi ve dağıtımını](concept-model-management-and-deployment.md)basitleştirmek için modellerinizi çalışma alanınızda depolamanızı ve sürümlerini oluşturmanıza imkan tanır.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Yeni kaydettiğiniz model, eğitim için kullandığınız tahmin aracı 'dan bağımsız olarak, Azure Machine Learning ' deki diğer tüm kayıtlı modellerle aynı şekilde dağıtılır. Dağıtım nasıl yapılır, model kaydettirme hakkında bir bölüm içerir, ancak zaten kayıtlı bir modeliniz olduğundan, dağıtım için doğrudan [bir işlem hedefi oluşturmaya](how-to-deploy-and-where.md#choose-a-compute-target) geçebilirsiniz.

Ayrıca modelin yerel bir kopyasını da indirebilirsiniz. Bu, ek model doğrulama işini yerel olarak gerçekleştirmek için yararlı olabilir. Eğitim betikindeki `mnist-keras.py`, bir TensorFlow koruyucu nesnesi modeli yerel bir klasöre (yerel olarak işlem hedefi) devam ettirir. Veri deposundan bir kopya indirmek için Run nesnesini kullanabilirsiniz.

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

> [!div class="nextstepaction"]
> [Modellerin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md)
* [Eğitim sırasında çalıştırma ölçümlerini izleyin](how-to-track-experiments.md)
* [Hiper parametreleri ayarla](how-to-tune-hyperparameters.md)
* [Eğitilen model dağıtma](how-to-deploy-and-where.md)
* [Azure 'da dağıtılmış derin öğrenme eğitimi için başvuru mimarisi](/azure/architecture/reference-architectures/ai/training-deep-learning)

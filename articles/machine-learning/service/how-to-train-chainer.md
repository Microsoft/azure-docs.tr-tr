---
title: Derin öğrenme Chainer modellerini eğitme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Chainer tahmin aracı sınıfını kullanarak pytorch eğitim betiklerinizi kurumsal ölçekte çalıştırmayı öğrenin.  Örnek betik, el yazısı y 'nin üstünde çalışan Chainer Python kitaplığını kullanarak derin bir öğrenme sinir ağı oluşturmak için el ile yazılmış rakam görüntülerini sınıflandırır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: a7c19486fe6787c4548a77dcdd93a92a92c97d8c
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931106"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning ile Chainer modellerini eğitme ve kaydetme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning [Chainer tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) sınıfını kullanarak [Chainer](https://chainer.org/) eğitim betiklerinizi kurumsal ölçekte çalıştırmayı öğrenin. Bu makaledeki örnek eğitim betiği, el yazısı [y](https://www.numpy.org/)'nin üstünde çalışan Chainer Python kitaplığı kullanılarak oluşturulan derin bir sinir ağı (DNN) kullanarak el yazısı rakamları sınıflandırmak için popüler [veri kümesini](http://yann.lecun.com/exdb/mnist/) kullanır.

Derin bir öğrenme Chainer modelini baştan sona eğiyor veya mevcut bir modeli buluta çıkardığınızı, elastik bulut işlem kaynaklarını kullanarak açık kaynaklı eğitim işlerini ölçeklendirmek için Azure Machine Learning kullanabilirsiniz. Azure Machine Learning ile üretim sınıfı modellerini oluşturabilir, dağıtabilir, sürüm ve izleyebilirsiniz. 

[Derin öğrenme ve makine öğrenimi](concept-deep-learning-vs-machine-learning.md)hakkında daha fazla bilgi edinin.

Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

## <a name="prerequisites"></a>Önkoşullar

Bu kodu şu ortamlardan birinde çalıştırın:

- Azure Machine Learning Not defteri VM-indirme veya yükleme gerekli değil

    - Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md) .
    - Not defteri sunucusundaki örnekler derin öğrenimi klasöründe, **nasıl yapılır-kullan-azureml > ml-çerçeveleri > chainer > deployment > tren-hyperparameter-ayarla-dağıt-Chainer klasörünü içeren** tamamlanmış bir not defteri ve dosya bulun.  Not defteri, akıllı hiper parametre ayarlamayı, model dağıtımını ve Not defteri pencere öğelerini kapsayan genişletilmiş bölümler içerir.

- Kendi Jupyter Notebook sunucunuz

    - [Azure Machine Learning SDK 'Sını yükler](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).
    - Örnek betik dosyası [chainer_mnist. Kopyala](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)' yı indirin.
     - GitHub örnekleri sayfasında bu kılavuzun tamamlanmış bir [Jupyter Notebook sürümünü](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) de bulabilirsiniz. Not defteri, akıllı hiper parametre ayarlamayı, model dağıtımını ve Not defteri pencere öğelerini kapsayan genişletilmiş bölümler içerir.

## <a name="set-up-the-experiment"></a>Denemeyi ayarlama

Bu bölüm, gerekli Python paketlerini yükleyerek, bir çalışma alanı başlatarak, bir deneme oluşturarak ve eğitim verilerini ve eğitim betikleri karşıya yükleyerek Eğitim denemesini ayarlar.

### <a name="import-packages"></a>Paketleri içeri aktarma

İlk olarak, azureml. Core Python kitaplığını içeri aktarın ve sürüm numarasını görüntüleyin.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Çalışma alanını başlatma

[Azure Machine Learning çalışma alanı](concept-workspace.md) , hizmet için en üst düzey kaynaktır. Oluşturduğunuz tüm yapıtlarla çalışmak için merkezi bir yer sağlar. Python SDK 'sında, [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) nesnesi oluşturarak çalışma alanı yapılarına erişebilirsiniz.

[Önkoşullar bölümünde](#prerequisites)oluşturulan `config.json` dosyasını okuyarak bir çalışma alanı nesnesi oluşturun:

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Proje dizini oluşturma
Yerel makinenizden uzak kaynak üzerinde erişmeniz gereken tüm gerekli kodu içerecek bir dizin oluşturun. Bu eğitim betiğini ve eğitim betiğinizin bağımlı olduğu diğer dosyaları içerir.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Eğitim betiğini hazırla

Bu öğreticide, sizin için eğitim betiği **chainer_mnist. Kopyala** zaten sağlanmış. Uygulamada, herhangi bir özel eğitim betiğini olduğu gibi götürebilmeniz ve kodunuzu değiştirmek zorunda kalmadan Azure ML ile çalıştırmanız gerekir.

Azure ML 'nin izleme ve ölçüm yeteneklerini kullanmak için eğitim betiğinizin içine küçük miktarda Azure ML kodu ekleyin.  Eğitim betiği **chainer_mnist. Kopyala** , bazı ÖLÇÜMLERIN Azure ML çalıştırmak için betik içinde `Run` nesnesini kullanarak nasıl günlüğe alınacağını gösterir.

Belirtilen eğitim betiği, Chainer `datasets.mnist.get_mnist` işlevindeki örnek verileri kullanır.  Kendi verileriniz için verileri eğitim sırasında kullanılabilir hale getirmek üzere veri [kümesini ve betikleri karşıya yükleme](how-to-train-keras.md#data-upload) gibi adımları kullanmanız gerekebilir.

Eğitim betiğini **chainer_mnist.** Kopyala öğesini proje dizininize kopyalayın.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Derin öğrenme denemesi oluşturun

Deneme oluşturma. Bu örnekte, "Chainer-mnist" adlı bir deneme oluşturun.

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>İşlem hedefi oluştur veya al

Modelinize eğitim için bir [işlem hedefine](concept-compute-target.md) ihtiyacınız vardır. Bu örnekte, uzaktan eğitim işlem kaynağınız için Azure ML yönetilen işlem (AmlCompute) kullanacaksınız.

**AmlCompute oluşturma işlemi yaklaşık 5 dakika sürer**. Bu ada sahip AmlCompute zaten çalışma alanınızda varsa, bu kod oluşturma işlemini atlar.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

İşlem hedefleri hakkında daha fazla bilgi için bkz. [işlem hedefi nedir](concept-compute-target.md) makalesi.

## <a name="create-a-chainer-estimator"></a>Chainer tahmin aracı oluşturma

[Chainer tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) , işlem Hedefinizdeki Chainer eğitim işlerini başlatmanın basit bir yolunu sağlar.

Chainer tahmin aracı, herhangi bir çerçeveyi desteklemek için kullanılabilen genel [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) sınıfı aracılığıyla uygulanır. Genel tahmin aracı kullanan eğitim modelleri hakkında daha fazla bilgi için bkz. [tahmin aracı kullanarak Azure Machine Learning modelleri eğitme](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Bir çalıştırma gönder

[Run nesnesi](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) , iş çalışırken ve tamamlandıktan sonra çalışma geçmişi için arabirim sağlar.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Çalıştırma yürütüldüğü için aşağıdaki aşamalardan geçer:

- **Hazırlama**: bir Docker görüntüsü, Chainer Estimator öğesine göre oluşturulur. Görüntü, çalışma alanının kapsayıcı kayıt defterine yüklenir ve daha sonra çalışacak şekilde önbelleğe alınır. Günlükler, çalıştırma geçmişine de kaydedilir ve ilerlemeyi izlemek için görüntülenebilir.

- **Ölçeklendirme**: Batch AI kümesi, çalışmayı yürütmek için daha fazla düğüm gerektiriyorsa, küme ölçeği büyütmeyi dener ve şu anda kullanılabilir.

- **Çalışıyor**: betik klasöründeki tüm betikler işlem hedefine yüklenir, veri depoları bağlanır veya kopyalanır ve entry_script yürütülür. Stdout ve./logs klasörü çıkışları, çalıştırma geçmişine akışla kaydedilir ve çalıştırmayı izlemek için kullanılabilir.

- **Işlem sonrası**: çalıştırmanın./çıktılar klasörü, çalışma geçmişine kopyalanır.

## <a name="save-and-register-the-model"></a>Modeli kaydetme ve kaydetme

Modeli eğittikten sonra, çalışma alanınıza kaydedebilir ve kaydedebilirsiniz. Model kaydı, [model yönetimi ve dağıtımını](concept-model-management-and-deployment.md)basitleştirmek için modellerinizi çalışma alanınızda depolamanızı ve sürümlerini oluşturmanıza imkan tanır.


Model eğitimi tamamlandıktan sonra, aşağıdaki kodla modeli çalışma alanınıza kaydedin.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Yeni kaydettiğiniz model, eğitim için kullandığınız tahmin aracı 'dan bağımsız olarak, Azure Machine Learning ' deki diğer tüm kayıtlı modellerle aynı şekilde dağıtılır. Dağıtım nasıl yapılır, model kaydettirme hakkında bir bölüm içerir, ancak zaten kayıtlı bir modeliniz olduğundan, dağıtım için doğrudan [bir işlem hedefi oluşturmaya](how-to-deploy-and-where.md#choose-a-compute-target) geçebilirsiniz.

Ayrıca modelin yerel bir kopyasını da indirebilirsiniz. Bu, ek model doğrulama işini yerel olarak gerçekleştirmek için yararlı olabilir. Eğitim betikindeki `chainer_mnist.py`, bir koruyucu nesnesi modeli yerel bir klasöre (yerel olarak işlem hedefine) devam ettirir. Veri deposundan bir kopya indirmek için Run nesnesini kullanabilirsiniz.

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

Bu makalede, Azure Machine Learning üzerinde Chainer kullanarak derin bir öğrenme, sinir ağı eğitildiniz ve kaydettiniz. Modeli dağıtmayı öğrenmek için [model dağıtım](how-to-deploy-and-where.md) makalemize devam edin.

* [Hiper parametreleri ayarla](how-to-tune-hyperparameters.md)

* [Eğitim sırasında çalıştırma ölçümlerini izleyin](how-to-track-experiments.md)

* [Azure 'da dağıtılmış derin öğrenme eğitimi için başvuru mimarimizi görüntüleyin](/azure/architecture/reference-architectures/ai/training-deep-learning)

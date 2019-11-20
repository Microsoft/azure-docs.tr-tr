---
title: Derin öğrenme PyTorch modellerini eğitme
titleSuffix: Azure Machine Learning
description: Pytorch eğitim betiklerinizi, Azure Machine Learning pytorch tahmin aracı sınıfını kullanarak kurumsal ölçekte çalıştırmayı öğrenin.  Örnek betikler,, PyTorch 'ın aktarım öğrenimi öğreticisini temel alarak derin bir öğrenme sinir ağı oluşturmak için tavuk ve Türkiye görüntülerini sınıflandırır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: a18949799b948b962a5b24ffdc7c735fc1380808
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931084"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Pytorch derin öğrenme modellerini Azure Machine Learning ölçeklendirirken eğitme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning [pytorch tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) sınıfını kullanarak [pytorch](https://pytorch.org/) eğitim betiklerinizi kurumsal ölçekte çalıştırmayı öğrenin.  

Bu makaledeki örnek betikler, PyTorch 'ın aktarım öğrenimi [öğreticisini](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)temel alan derin bir öğrenme sinir ağı oluşturmak için tavuk ve Türkiye görüntülerini sınıflandırmak üzere kullanılır. 

Derin bir öğrenme PyTorch modelini baştan sona eğiyor veya mevcut bir modeli buluta çıkarksanız, elastik bulut işlem kaynakları kullanarak açık kaynaklı eğitim işlerini ölçeklendirmek için Azure Machine Learning kullanabilirsiniz. Azure Machine Learning ile üretim sınıfı modellerini oluşturabilir, dağıtabilir, sürüm ve izleyebilirsiniz. 

[Derin öğrenme ve makine öğrenimi](concept-deep-learning-vs-machine-learning.md)hakkında daha fazla bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

Bu kodu şu ortamlardan birinde çalıştırın:

 - Azure Machine Learning Not defteri VM-indirme veya yükleme gerekli değil

    - Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md) .
    - Not defteri sunucusundaki örnekler derin öğrenimi klasöründe, bu dizine giderek tamamlanmış ve genişletilmiş bir not defteri bulun: **kullanımı nasıl kullanılır-azureml > eğitimi-derin öğrenme > tren-hyperparameter-ayarla-dağıt--pytorch** klasörü. 
 
 - Kendi Jupyter Notebook sunucunuz

    - [Azure Machine Learning SDK 'Sını yükler](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).
    - [Örnek betik dosyalarını indirin](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Ayrıca, bu kılavuzun tamamlanmış bir [Jupyter Notebook sürümünü](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) GitHub örnekleri sayfasından bulabilirsiniz. Not defteri, akıllı hiper parametre ayarlamayı, model dağıtımını ve Not defteri pencere öğelerini kapsayan genişletilmiş bölümler içerir.

## <a name="set-up-the-experiment"></a>Denemeyi ayarlama

Bu bölüm, gerekli Python paketlerini yükleyerek, bir çalışma alanı başlatarak, bir deneme oluşturarak ve eğitim verilerini ve eğitim betikleri karşıya yükleyerek Eğitim denemesini ayarlar.

### <a name="import-packages"></a>Paketleri içeri aktarma

İlk olarak, gerekli Python kitaplıklarını içeri aktarın.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Çalışma alanını başlatma

[Azure Machine Learning çalışma alanı](concept-workspace.md) , hizmet için en üst düzey kaynaktır. Oluşturduğunuz tüm yapıtlarla çalışmak için merkezi bir yer sağlar. Python SDK 'sında, [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) nesnesi oluşturarak çalışma alanı yapılarına erişebilirsiniz.

[Önkoşullar bölümünde](#prerequisites)oluşturulan `config.json` dosyasından bir çalışma alanı nesnesi oluşturun.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Derin öğrenme denemesi oluşturun

Eğitim betiklerinizi tutmak için bir deneme ve bir klasör oluşturun. Bu örnekte, "pytorch-kuşlar" adlı bir deneme oluşturun.

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Verileri alma

Veri kümesi, her bir sınıf için 100 doğrulama görüntüleriyle birlikte, her biri Turkey ve tavkens için 120 eğitim görüntülerinden oluşur. Veri kümesini, eğitim betiğimizin bir parçası olarak indirecek ve ayıklayacağız `pytorch_train.py`. Görüntüler, [Açık görüntüler v5 veri kümesinin](https://storage.googleapis.com/openimages/web/index.html)bir alt kümesidir.

### <a name="prepare-training-scripts"></a>Eğitim betikleri hazırlama

Bu öğreticide, `pytorch_train.py`eğitim betiği zaten sağlanmış. Uygulamada, tüm özel eğitim komut dosyalarını olduğu gibi alabilir ve Azure Machine Learning ile çalıştırabilirsiniz.

`pytorch_train.py`Pytorch eğitim betiğini yükleyin.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Ancak Azure Machine Learning izleme ve ölçüm yeteneklerini kullanmak istiyorsanız, eğitim betiğinizin içine küçük bir tutar kodu eklemeniz gerekir. Ölçüm izleme örnekleri, `pytorch_train.py`bulunabilir.

## <a name="create-a-compute-target"></a>İşlem hedefi oluştur

PyTorch işinizin üzerinde çalışacağı bir işlem hedefi oluşturun. Bu örnekte, GPU özellikli Azure Machine Learning işlem kümesi oluşturun.

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

## <a name="create-a-pytorch-estimator"></a>Pytorch tahmin aracı oluşturma

[Pytorch tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) , bir bilgi işlem hedefinde bir pytorch eğitim işi başlatmanın basit bir yolunu sağlar.

Pytorch tahmin aracı, herhangi bir çerçeveyi desteklemek için kullanılabilen genel [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) sınıfı aracılığıyla uygulanır. Genel tahmin aracı kullanan eğitim modelleri hakkında daha fazla bilgi için bkz. [tahmin aracı kullanarak Azure Machine Learning modelleri eğitme](how-to-train-ml-models.md)

Eğitim betiğinizin çalışması için ek PIP veya Conda paketleri gerekiyorsa, bu paketlerin adlarını `pip_packages` ve `conda_packages` bağımsız değişkenlerle geçirerek elde edilen Docker görüntüsüne yüklenmesini sağlayabilirsiniz.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Bir çalıştırma gönder

[Run nesnesi](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) , iş çalışırken ve tamamlandıktan sonra çalışma geçmişi için arabirim sağlar.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Çalıştırma yürütüldüğü için aşağıdaki aşamalardan geçer:

- **Hazırlama**: bir Docker görüntüsü PyTorch Estimator 'a göre oluşturulur. Görüntü, çalışma alanının kapsayıcı kayıt defterine yüklenir ve daha sonra çalışacak şekilde önbelleğe alınır. Günlükler, çalıştırma geçmişine de kaydedilir ve ilerlemeyi izlemek için görüntülenebilir.

- **Ölçeklendirme**: Batch AI kümesi, çalışmayı yürütmek için daha fazla düğüm gerektiriyorsa, küme ölçeği büyütmeyi dener ve şu anda kullanılabilir.

- **Çalışıyor**: betik klasöründeki tüm betikler işlem hedefine yüklenir, veri depoları bağlanır veya kopyalanır ve entry_script yürütülür. Stdout ve./logs klasörü çıkışları, çalıştırma geçmişine akışla kaydedilir ve çalıştırmayı izlemek için kullanılabilir.

- **Işlem sonrası**: çalıştırmanın./çıktılar klasörü, çalışma geçmişine kopyalanır.

## <a name="register-or-download-a-model"></a>Bir modeli kaydetme veya indirme

Modeli eğittikten sonra, çalışma alanınıza kaydedebilirsiniz. Model kaydı, [model yönetimi ve dağıtımını](concept-model-management-and-deployment.md)basitleştirmek için modellerinizi çalışma alanınızda depolamanızı ve sürümlerini oluşturmanıza imkan tanır.

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Yeni kaydettiğiniz model, eğitim için kullandığınız tahmin aracı 'dan bağımsız olarak, Azure Machine Learning ' deki diğer tüm kayıtlı modellerle aynı şekilde dağıtılır. Dağıtım nasıl yapılır, model kaydettirme hakkında bir bölüm içerir, ancak zaten kayıtlı bir modeliniz olduğundan, dağıtım için doğrudan [bir işlem hedefi oluşturmaya](how-to-deploy-and-where.md#choose-a-compute-target) geçebilirsiniz.

Ayrıca, Çalıştır nesnesini kullanarak modelin yerel bir kopyasını indirebilirsiniz. Eğitim betiğinde `pytorch_train.py`, PyTorch Save nesnesi modeli yerel bir klasöre (yerel olarak işlem hedefine) devam ettirir. Bir kopyasını indirmek için Run nesnesini kullanabilirsiniz.

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

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) tahmin aracı Ayrıca CPU ve GPU kümelerinde dağıtılmış eğitimi destekler. Dağıtılmış PyTorch işlerini kolayca çalıştırabilir ve Azure Machine Learning düzenleme sizin için yönetecektir.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) , bir açık kaynak, hepsi Uber tarafından geliştirilen dağıtılmış eğitimin çerçevesini azaltır. Dağıtılmış GPU PyTorch işlerinin kolay bir yolunu sunar.

Horovod 'yi kullanmak için, PyTorch oluşturucusunda `distributed_training` parametresi için bir [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) nesnesi belirtin. Bu parametre, eğitim betiğinizdeki kullanabileceğiniz Horovod kitaplığının yüklenmesini sağlar.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod ve bağımlılıkları sizin için yüklenecek, bu sayede eğitim betiğinizdeki `train.py` aşağıdaki gibi içeri aktarabilirsiniz:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>ONNX 'e aktar

[Onnx çalışma zamanıyla](concept-onnx.md)çıkarımı iyileştirmek için, eğitimli PyTorch MODELINIZI onnx biçimine dönüştürün. Çıkarım veya model Puanlama, dağıtılan modelin tahmin için en yaygın olarak üretim verilerinde kullanıldığı aşamadır. Örnek için [öğreticiye](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Machine Learning PyTorch kullanarak derin bir öğrenme, sinir ağı eğitildiniz ve kaydettiniz. Modeli dağıtmayı öğrenmek için model dağıtım makalemize devam edin.

> [!div class="nextstepaction"]
> [Modellerin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md)
* [Eğitim sırasında çalıştırma ölçümlerini izleyin](how-to-track-experiments.md)
* [Hiper parametreleri ayarla](how-to-tune-hyperparameters.md)
* [Eğitilen model dağıtma](how-to-deploy-and-where.md)
* [Azure 'da dağıtılmış derin öğrenme eğitimi için başvuru mimarisi](/azure/architecture/reference-architectures/ai/training-deep-learning)

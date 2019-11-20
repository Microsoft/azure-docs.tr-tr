---
title: Scikit 'i eğitme-makine öğrenimi modellerini öğrenin
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SKIT tahmin aracı sınıfını kullanarak scikit-kurumsal ölçekte eğitim komut dosyalarınızı nasıl çalıştıracağınızı öğrenin. Örnek betikler, ırikit-öğrenimi veri kümesini temel alan bir makine öğrenimi modeli oluşturmak için Iris çiçek görüntülerini sınıflandırır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6d71ea59b7094134cc70b9eeea6da89feacb3a14
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931050"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Derleme scikit-Azure Machine Learning uygun ölçekte modeller öğrenin
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning [SKIT tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) sınıfını kullanarak scikit-kurumsal ölçekte eğitim komut dosyalarınızı nasıl çalıştıracağınızı öğrenin. 

Bu makaledeki örnek betikler, ırikit-öğrenme için [Iris veri kümesini](https://archive.ics.uci.edu/ml/datasets/iris)temel alan bir makine öğrenimi modeli oluşturmak üzere Iris çiçek görüntülerini sınıflandırmak için kullanılır.

Bir makine öğrenimi scikit-bir modeli baştan sona öğreniyor olun veya var olan bir modeli buluta getiriyor, elastik bulut bilgi işlem kaynaklarını kullanarak açık kaynaklı eğitim işlerini ölçeklendirmek için Azure Machine Learning kullanabilirsiniz. Azure Machine Learning ile üretim sınıfı modellerini oluşturabilir, dağıtabilir, sürüm ve izleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu kodu şu ortamlardan birinde çalıştırın:
 - Azure Machine Learning Not defteri VM-indirme veya yükleme gerekli değil

    - Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md) .
    - Not defteri sunucusundaki örnekler eğitim klasöründe, bu dizine giderek tamamlanmış ve genişletilmiş bir not defteri bulun: **nasıl kullanılır-azureml > ml-çerçeveleri > scikit-> eğitim > tren-hyperparameter-ayarla-dağıt-ile-sköğren** klasörü.

 - Kendi Jupyter Notebook sunucunuz

    - [Azure Machine Learning SDK 'Sını yükler](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).
    - Veri kümesini ve örnek betik dosyasını indirin 
        - [Iris veri kümesi](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Ayrıca, bu kılavuzun tamamlanmış bir [Jupyter Notebook sürümünü](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) GitHub örnekleri sayfasından bulabilirsiniz. Not defteri, akıllı hiper parametre ayarlamayı kapsayan ve birincil ölçümler için en iyi modeli alan genişletilmiş bir bölüm içerir.

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

### <a name="create-a-machine-learning-experiment"></a>Machine Learning denemesi oluşturun

Eğitim betiklerinizi tutmak için bir deneme ve bir klasör oluşturun. Bu örnekte "sköğren-Iris" adlı bir deneme oluşturun.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Eğitim betiğini hazırla

Bu öğreticide, sizin için eğitim betiği **train_iris. Kopyala** zaten sağlanmış. Uygulamada, herhangi bir özel eğitim betiğini olduğu gibi götürebilmeniz ve kodunuzu değiştirmek zorunda kalmadan Azure ML ile çalıştırmanız gerekir.

Azure ML 'nin izleme ve ölçüm yeteneklerini kullanmak için eğitim betiğinizin içine küçük miktarda Azure ML kodu ekleyin.  Eğitim betiği **train_iris. Kopyala** , bazı ÖLÇÜMLERIN Azure ML çalıştırmak için betik içinde `Run` nesnesini kullanarak nasıl günlüğe alınacağını gösterir.

Belirtilen eğitim betiği `iris = datasets.load_iris()` işlevindeki örnek verileri kullanır.  Kendi verileriniz için verileri eğitim sırasında kullanılabilir hale getirmek üzere veri [kümesini ve betikleri karşıya yükleme](how-to-train-keras.md#data-upload) gibi adımları kullanmanız gerekebilir.

Eğitim betiğini **train_iris.** Kopyala öğesini proje dizininize kopyalayın.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>İşlem hedefi oluştur veya al

Scikit-üzerinde çalıştırılacak iş için bilgi işlem hedefi oluşturun. Scikit-yalnızca tek düğümlü, CPU bilgi işlem desteği.

Aşağıdaki kod, uzaktan eğitim işlem kaynağınız için Azure Machine Learning yönetilen bir işlem (AmlCompute) oluşturur. AmlCompute oluşturma işlemi yaklaşık 5 dakika sürer. Bu ada sahip AmlCompute zaten çalışma alanınızda varsa, bu kod oluşturma işlemini atlar.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

İşlem hedefleri hakkında daha fazla bilgi için bkz. [işlem hedefi nedir](concept-compute-target.md) makalesi.

## <a name="create-a-scikit-learn-estimator"></a>Bir scikit oluşturma-tahmin aracı

[Scikit-öğrenme tahmin aracı](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) , bir işlem hedefinde bir scikit-öğrenme eğitimi işi başlatmanın basit bir yolunu sunar. Tek düğümlü CPU eğitimini desteklemek için kullanılabilecek [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) sınıfı aracılığıyla uygulanır.

Eğitim betiğinizin çalışması için ek PIP veya Conda paketleri gerekiyorsa, bu paketlerin adlarını `pip_packages` ve `conda_packages` bağımsız değişkenlerle geçirerek elde edilen Docker görüntüsüne yüklenmesini sağlayabilirsiniz.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>Bir çalıştırma gönder

[Run nesnesi](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) , iş çalışırken ve tamamlandıktan sonra çalışma geçmişi için arabirim sağlar.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Çalıştırma yürütüldüğü için aşağıdaki aşamalardan geçer:

- **Hazırlama**: bir Docker görüntüsü, TensorFlow Estimator öğesine göre oluşturulur. Görüntü, çalışma alanının kapsayıcı kayıt defterine yüklenir ve daha sonra çalışacak şekilde önbelleğe alınır. Günlükler, çalıştırma geçmişine de kaydedilir ve ilerlemeyi izlemek için görüntülenebilir.

- **Ölçeklendirme**: Batch AI kümesi, çalışmayı yürütmek için daha fazla düğüm gerektiriyorsa, küme ölçeği büyütmeyi dener ve şu anda kullanılabilir.

- **Çalışıyor**: betik klasöründeki tüm betikler işlem hedefine yüklenir, veri depoları bağlanır veya kopyalanır ve entry_script yürütülür. Stdout ve./logs klasörü çıkışları, çalıştırma geçmişine akışla kaydedilir ve çalıştırmayı izlemek için kullanılabilir.

- **Işlem sonrası**: çalıştırmanın./çıktılar klasörü, çalışma geçmişine kopyalanır.

## <a name="save-and-register-the-model"></a>Modeli kaydetme ve kaydetme

Modeli eğittikten sonra, çalışma alanınıza kaydedebilir ve kaydedebilirsiniz. Model kaydı, [model yönetimi ve dağıtımını](concept-model-management-and-deployment.md)basitleştirmek için modellerinizi çalışma alanınızda depolamanızı ve sürümlerini oluşturmanıza imkan tanır.

Modeli kaydetmek için eğitim betiğe, train_iris. Kopyala ' ya aşağıdaki kodu ekleyin. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Aşağıdaki kodla modeli çalışma alanınıza kaydedin. `model_framework`, `model_framework_version`ve `resource_configuration`parametreleri belirterek, hiçbir kod modeli dağıtımı kullanılabilir hale gelir. Bu, modelinizi kayıtlı modelden doğrudan bir Web hizmeti olarak dağıtmanızı sağlar ve `ResourceConfiguration` nesnesi Web hizmeti için işlem kaynağını tanımlar.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Dağıtım

Yeni Kaydolmakta olduğunuz model, eğitim için kullandığınız tahmin etmeksizin, Azure Machine Learning ' deki diğer tüm kayıtlı modellerle tamamen aynı şekilde dağıtılabilir. Dağıtım nasıl yapılır, model kaydettirme hakkında bir bölüm içerir, ancak zaten kayıtlı bir modeliniz olduğundan, dağıtım için doğrudan [bir işlem hedefi oluşturmaya](how-to-deploy-and-where.md#choose-a-compute-target) geçebilirsiniz.

### <a name="preview-no-code-model-deployment"></a>Önizle Kod olmayan model dağıtımı

Geleneksel dağıtım yolu yerine, scikit-öğren için kod dışı dağıtım özelliğini (Önizleme) de kullanabilirsiniz. Hiçbir kod modeli dağıtımı, yerleşik tüm scikit-öğrenme model türleri için desteklenmez. Modelinizi yukarıda gösterildiği gibi `model_framework`, `model_framework_version`ve `resource_configuration` parametreleriyle kaydederek, modelinizi dağıtmak için `deploy()` static işlevini kullanmanız yeterlidir.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

NOTE: Bu bağımlılıklar önceden oluşturulmuş scikit-öğrenme çıkarımı kapsayıcısına dahil edilmiştir.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Daha ayrıntılı Azure Machine Learning dağıtımı [nasıl](how-to-deploy-and-where.md) ele alır.


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir scikit-öğren modeli eğitildiniz ve kaydettiniz ve dağıtım seçenekleri hakkında bilgi edindiniz. Azure Machine Learning hakkında daha fazla bilgi edinmek için bu makaleye bakın.

* [Eğitim sırasında çalıştırma ölçümlerini izleyin](how-to-track-experiments.md)
* [Hiper parametreleri ayarla](how-to-tune-hyperparameters.md)
* [Azure 'da dağıtılmış derin öğrenme eğitimi için başvuru mimarisi](/azure/architecture/reference-architectures/ai/training-deep-learning)

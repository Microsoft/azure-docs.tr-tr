---
title: Scikit 'i eğitme-makine öğrenimi modellerini öğrenin
titleSuffix: Azure Machine Learning
description: Azure Machine Learning, esnek bulut işlem kaynaklarını kullanarak bir scikit-eğitim işini nasıl ölçeklendirebileceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef64d94ed3e860895bcc81a1429008205a1a8acb
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817116"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit 'i eğitme-Azure Machine Learning modelleri uygun ölçekte öğrenme

Bu makalede, Azure Machine Learning ile scikit-eğitim komut dosyalarını nasıl çalıştıracağınızı öğrenin.

Bu makaledeki örnek betikler, ırikit-öğrenme için [Iris veri kümesini](https://archive.ics.uci.edu/ml/datasets/iris)temel alan bir makine öğrenimi modeli oluşturmak üzere Iris çiçek görüntülerini sınıflandırmak için kullanılır.

Bir makine öğrenimi scikit-bir modeli baştan sona öğreniyor olun veya var olan bir modeli buluta getiriyor, elastik bulut bilgi işlem kaynaklarını kullanarak açık kaynaklı eğitim işlerini ölçeklendirmek için Azure Machine Learning kullanabilirsiniz. Azure Machine Learning ile üretim sınıfı modellerini oluşturabilir, dağıtabilir, sürüm ve izleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu kodu şu ortamlardan birinde çalıştırın:
 - Azure Machine Learning işlem örneği-indirme veya yükleme gerekli değil

    - Öğreticiyi doldurun: SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanını kurma](tutorial-1st-experiment-sdk-setup.md)  .
    - Not defteri sunucusundaki örnekler eğitim klasöründe, şu dizine giderek tamamlanmış ve genişletilmiş bir not defteri bulun: **nasıl kullanılır-azureml > ml-çerçeveleri > scikit >---------dağıt--------------------**

 - Kendi Jupyter Notebook sunucunuz

    - [Azure Machine Learning SDK 'sını](/python/api/overview/azure/ml/install) (>= 1.13.0) yükler.
    - [Bir çalışma alanı yapılandırma dosyası oluşturun](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Denemeyi ayarlama

Bu bölüm, gerekli Python paketlerini yükleyerek, bir çalışma alanı başlatarak, eğitim ortamını tanımlayarak ve eğitim betiğinin hazırlanarak Eğitim denemesini ayarlar.

### <a name="initialize-a-workspace"></a>Çalışma alanını başlatma

[Azure Machine Learning çalışma alanı](concept-workspace.md) , hizmet için en üst düzey kaynaktır. Oluşturduğunuz tüm yapıtlarla çalışmak için merkezi bir yer sağlar. Python SDK 'sında bir nesne oluşturarak çalışma alanı yapıtlarına erişebilirsiniz [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) .

`config.json` [Önkoşullar bölümünde](#prerequisites)oluşturulan dosyadan bir çalışma alanı nesnesi oluşturun.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Betikleri hazırlama

Bu öğreticide, [burada](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)sizin için eğitim betiği **train_iris. Kopyala** zaten sağlanmış. Uygulamada, herhangi bir özel eğitim betiğini olduğu gibi götürebilmeniz ve kodunuzu değiştirmek zorunda kalmadan Azure ML ile çalıştırmanız gerekir.

Notlar:
- Belirtilen eğitim betiği, bazı ölçümlerin Azure ML çalıştırmak için betik içindeki nesnesini kullanarak nasıl günlüğe alınacağını gösterir `Run` .
- Belirtilen eğitim betiği, işlevden örnek verileri kullanır  `iris = datasets.load_iris()` .  Kendi verilerinizi kullanmak ve erişmek için bkz. eğitim sırasında verileri kullanılabilir hale getirmek için veri [kümeleriyle eğitme](how-to-train-with-datasets.md) .

### <a name="define-your-environment"></a>Ortamınızı tanımlama

Eğitim betiğinin bağımlılıklarını kapsülleyen Azure ML [ortamını](concept-environments.md) tanımlamak için, özel bir ortam tanımlayabilir veya Azure ML 'nin seçkin ortamını kullanabilirsiniz.

#### <a name="use-a-curated-environment"></a>Seçkin bir ortam kullanma
İsteğe bağlı olarak, Azure ML kendi ortamınızı tanımlamak istemiyorsanız önceden oluşturulmuş, seçkin ortamlar sağlar. Daha fazla bilgi için [buraya](resource-curated-environments.md)bakın.
Seçkin bir ortam kullanmak istiyorsanız, bunun yerine aşağıdaki komutu çalıştırabilirsiniz:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Özel ortam oluşturma

Kendi özel ortamınızı da oluşturabilirsiniz. Conda bağımlılıklarınızı bir YAML dosyasında tanımlayın; Bu örnekte, dosyanın adı `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Bu Conda ortam belirtiminden bir Azure ML ortamı oluşturun. Ortam, çalışma zamanında bir Docker kapsayıcısı olarak paketlenir.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Ortamları oluşturma ve kullanma hakkında daha fazla bilgi için, bkz. [Azure Machine Learning yazılım ortamları oluşturma ve kullanma](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Eğitim çalıştırmanızı yapılandırma ve gönderme

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig oluşturma
Eğitim betiğinizi, kullanılacak ortamı ve üzerinde çalıştırılacak işlem hedefini de içeren eğitim işinizin yapılandırma ayrıntılarını belirtmek için bir ScriptRunConfig nesnesi oluşturun.
Eğitim betiğinizdeki bağımsız değişkenler, parametresinde belirtilmişse komut satırı aracılığıyla geçirilir `arguments` .

Aşağıdaki kod, yerel makinenizde yürütmek üzere işinizi göndermek için bir ScriptRunConfig nesnesi yapılandıracaktır.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Bunun yerine, işinizi uzak bir kümede çalıştırmak istiyorsanız, istenen işlem hedefini `compute_target` ScriptRunConfig parametresi olarak belirtebilirsiniz.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Çalıştırmanızı gönder
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning, tüm kaynak dizinini kopyalayarak eğitim betikleri çalıştırır. Karşıya yüklemek istemediğiniz gizli verileriniz varsa, bir [. Ignore dosyası](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) kullanın veya kaynak dizine eklemeyin. Bunun yerine, bir Azure ML [veri kümesi](how-to-train-with-datasets.md)kullanarak verilerinize erişin.

### <a name="what-happens-during-run-execution"></a>Çalıştırma yürütme sırasında ne olur?
Çalıştırma yürütüldüğü için aşağıdaki aşamalardan geçer:

- **Hazırlama**: bir Docker görüntüsü, tanımlanan ortama göre oluşturulur. Görüntü, çalışma alanının kapsayıcı kayıt defterine yüklenir ve daha sonra çalışacak şekilde önbelleğe alınır. Günlükler, çalıştırma geçmişine de kaydedilir ve ilerlemeyi izlemek için görüntülenebilir. Bunun yerine, seçkin bir ortam belirtilmişse, bu eklenen ortamı destekleyen önbelleğe alınmış görüntü kullanılacaktır.

- **Ölçeklendirme**: Batch AI kümesi, çalışmayı yürütmek için daha fazla düğüm gerektiriyorsa, küme ölçeği büyütmeyi dener ve şu anda kullanılabilir.

- **Çalışıyor**: betik klasöründeki tüm betikler işlem hedefine yüklenir, veri depoları bağlanır veya kopyalanır ve `script` yürütülür. Stdout ve **./logs** klasörü çıkışları, çalıştırma geçmişine akışla kaydedilir ve çalıştırmayı izlemek için kullanılabilir.

- **Işlem sonrası**: çalıştırmanın **./çıktılar** klasörü, çalışma geçmişine kopyalanır.

## <a name="save-and-register-the-model"></a>Modeli kaydetme ve kaydetme

Modeli eğittikten sonra, çalışma alanınıza kaydedebilir ve kaydedebilirsiniz. Model kaydı, [model yönetimi ve dağıtımını](concept-model-management-and-deployment.md)basitleştirmek için modellerinizi çalışma alanınızda depolamanızı ve sürümlerini oluşturmanıza imkan tanır.

Modeli kaydetmek için eğitim betiğe, train_iris. Kopyala ' ya aşağıdaki kodu ekleyin. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Aşağıdaki kodla modeli çalışma alanınıza kaydedin. , Ve parametrelerini belirterek,, `model_framework` `model_framework_version` ve `resource_configuration` , No-Code Model dağıtımı kullanılabilir hale gelir. Kod modeli dağıtımı, modelinizi kayıtlı modelden doğrudan bir Web hizmeti olarak dağıtmanızı sağlar ve [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) nesne, Web hizmeti için işlem kaynağını tanımlar.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Dağıtım

Yeni kaydettiğiniz model, Azure ML 'deki diğer kayıtlı modelde tam olarak aynı şekilde dağıtılabilir. Dağıtım nasıl yapılır, model kaydettirme hakkında bir bölüm içerir, ancak zaten kayıtlı bir modeliniz olduğundan, dağıtım için doğrudan [bir işlem hedefi oluşturmaya](how-to-deploy-and-where.md#choose-a-compute-target) geçebilirsiniz.

### <a name="preview-no-code-model-deployment"></a>Önizle Kod olmayan model dağıtımı

Geleneksel dağıtım yolu yerine, scikit-öğren için kod dışı dağıtım özelliğini (Önizleme) de kullanabilirsiniz. Hiçbir kod modeli dağıtımı, yerleşik tüm scikit-öğrenme model türleri için desteklenmez. Modelinizi yukarıda gösterildiği gibi `model_framework` ,, ve parametreleriyle kaydederek, `model_framework_version` `resource_configuration` [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) modelinizi dağıtmak için yalnızca statik işlevi kullanabilirsiniz.

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

* [Eğitim sırasında çalıştırma ölçümlerini izleyin](how-to-log-view-metrics.md)
* [Hiperparametreleri ayarlama](how-to-tune-hyperparameters.md)

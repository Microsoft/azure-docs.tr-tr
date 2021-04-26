---
title: Özel bir Docker görüntüsü kullanarak bir modeli eğitme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning modelleri eğitebilmeniz için kendi Docker görüntülerinizi kullanmayı veya Microsoft 'tan nasıl seçeceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 953d43f93635e25da008515afd9baf9a9e9b7afa
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817080"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Özel bir Docker görüntüsü kullanarak bir modeli eğitme

Bu makalede, Azure Machine Learning ile eğitim modelleriniz olduğunda özel bir Docker görüntüsü kullanmayı öğrenin. Bu makaledeki örnek betikleri kullanarak, bir evsel sinir ağı oluşturarak Evcil hayvan görüntülerini sınıflandırın. 

Azure Machine Learning varsayılan bir Docker temel görüntüsü sağlar. Azure Machine Learning ortamlarını Ayrıca, korunan [Azure Machine Learning taban görüntülerinin](https://github.com/Azure/AzureML-Containers) veya kendi [özel yansımalarından](how-to-deploy-custom-docker-image.md#create-a-custom-base-image)biri gibi farklı bir temel görüntü belirtmek için de kullanabilirsiniz. Özel temel görüntüler, kendi bağımlılıklarınızı yakından yönetmenize ve eğitim işleri çalıştırırken bileşen sürümleri üzerinde daha sıkı bir denetim sürdürmenize imkan tanır.

## <a name="prerequisites"></a>Önkoşullar

Kodu şu ortamlardan birinde çalıştırın:

* Azure Machine Learning işlem örneği (İndirilenler veya yükleme gerekmez):
  * SDK ve örnek depoyla önceden yüklenmiş adanmış bir not defteri sunucusu oluşturmak için [ortamı ve çalışma alanı](tutorial-1st-experiment-sdk-setup.md) Oluşturma öğreticisini doldurun.
  * Azure Machine Learning [örnekleri deposunda](https://github.com/Azure/azureml-examples),   >  **fastai**  >  **eğitme-pets-resnet34. ipynb** dizinine giderek tamamlanan bir not defteri bulun. 
* Kendi Jupyter Notebook sunucunuz:
  * Bir [çalışma alanı yapılandırma dosyası](how-to-configure-environment.md#workspace)oluşturun.
  * [Azure Machine Learning SDK 'sını](/python/api/overview/azure/ml/install)yükler. 
  * İnternet 'te bulunan bir [Azure Container Registry](../container-registry/index.yml) veya diğer Docker kayıt defteri oluşturun.

## <a name="set-up-a-training-experiment"></a>Eğitim denemesi ayarlama

Bu bölümde, bir çalışma alanı başlatarak, ortamınızı tanımlayarak ve bir işlem hedefi yapılandırarak eğitim denemenizi ayarlarsınız.

### <a name="initialize-a-workspace"></a>Çalışma alanını başlatma

[Azure Machine Learning çalışma alanı](concept-workspace.md) , hizmet için en üst düzey kaynaktır. Sizin oluşturduğunuz tüm yapıtlarla çalışmak için merkezi bir yer sağlar. Python SDK 'sında bir nesne oluşturarak çalışma alanı yapıtlarına erişebilirsiniz [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) .

`Workspace`Bir [Önkoşul](#prerequisites)olarak oluşturduğunuz dosyadaki config.jsbir nesne oluşturun.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Ortamınızı tanımlama

Bir `Environment` nesne oluşturun ve Docker 'ı etkinleştirin.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Aşağıdaki kodda belirtilen temel görüntü, dağıtılmış derin öğrenme özelliklerine izin veren fast.ai kitaplığını destekler. Daha fazla bilgi için bkz. [Fast.ai Docker Hub deposu](https://hub.docker.com/u/fastdotai). 

Özel Docker görüntünüzü kullanırken, zaten Python ortamınız ayarlanmış olabilir. Bu durumda, `user_managed_dependencies` bayrağını `True` özel görüntünüzün yerleşik Python ortamını kullanacak şekilde ayarlayın. Varsayılan olarak, Azure Machine Learning belirttiğiniz bağımlılıklarla bir Conda ortamı oluşturur. Hizmet, temel görüntüde yüklü olan Python kitaplıklarını kullanmak yerine betiği bu ortamda çalıştırır.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Özel kapsayıcı kayıt defteri kullanın (isteğe bağlı)

Çalışma alanınızda olmayan özel bir kapsayıcı kayıt defterinden bir görüntü kullanmak için, `docker.base_image_registry` deponun adresini ve Kullanıcı adını ve parolayı belirtmek için kullanın:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Özel bir Dockerfile kullanın (isteğe bağlı)

Özel bir Dockerfile kullanmak da mümkündür. Python olmayan paketleri bağımlılık olarak yüklemeniz gerekiyorsa bu yaklaşımı kullanın. Taban görüntüsünü olarak ayarlamayı unutmayın `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure Machine Learning yalnızca aşağıdaki yazılımları sağlayan Docker görüntülerini destekler:
> * Ubuntu 16,04 veya üzeri.
> * Conda 4.5. # veya üzeri.
> * Python 3.6 +.

Azure Machine Learning ortamları oluşturma ve yönetme hakkında daha fazla bilgi için bkz. [yazılım ortamları oluşturma ve kullanma](how-to-use-environments.md). 

### <a name="create-or-attach-a-compute-target"></a>İşlem hedefi oluşturma veya iliştirme

Modelinize eğitim için bir [işlem hedefi](concept-azure-machine-learning-architecture.md#compute-targets) oluşturmanız gerekir. Bu öğreticide `AmlCompute` eğitim işlem kaynağınız olarak oluşturursunuz.

Oluşturma `AmlCompute` birkaç dakika sürer. `AmlCompute`Kaynak çalışma alanınızda zaten varsa, bu kod oluşturma işlemini atlar.

Diğer Azure hizmetlerinde olduğu gibi, Azure Machine Learning hizmetiyle ilişkili belirli kaynaklarda (örneğin,) sınırlar vardır `AmlCompute` . Daha fazla bilgi için bkz. [varsayılan sınırlar ve daha yüksek kota isteme](how-to-manage-quotas.md).

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Eğitim işinizi yapılandırma

Bu öğretici için [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py)'da *train.py* eğitim betiğini kullanın. Uygulamada, herhangi bir özel eğitim betiği alabilir ve Azure Machine Learning olduğu gibi çalıştırabilirsiniz.

`ScriptRunConfig`İşinizi istenen [işlem hedefinde](how-to-set-up-training-targets.md)çalıştırmak üzere yapılandırmak için bir kaynak oluşturun.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Eğitim işinizi gönderme

Bir nesne kullanarak bir eğitim çalıştırması gönderdiğinizde `ScriptRunConfig` , `submit` yöntemi türünde bir nesne döndürür `ScriptRun` . Döndürülen `ScriptRun` nesne, eğitim çalıştırmaları hakkında bilgi için programlı erişim sağlar. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning, tüm kaynak dizinini kopyalayarak eğitim betikleri çalıştırır. Karşıya yüklemek istemediğiniz gizli verileriniz varsa, bir [. Ignore dosyası](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) kullanın veya kaynak dizine eklemeyin. Bunun yerine, veri [deposu](/python/api/azureml-core/azureml.data)kullanarak verilerinize erişin.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, özel bir Docker görüntüsü kullanarak bir model eğitiliniz. Azure Machine Learning hakkında daha fazla bilgi edinmek için şu makalelere bakın:
* Eğitim sırasında [çalıştırma ölçümlerini izleyin](how-to-log-view-metrics.md) .
* Özel bir Docker görüntüsü kullanarak [bir model dağıtın](how-to-deploy-custom-docker-image.md) .

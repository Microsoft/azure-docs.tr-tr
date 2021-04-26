---
title: Yazılım ortamlarını kullanma
titleSuffix: Azure Machine Learning
description: Model eğitimi ve dağıtımı için ortamları oluşturun ve yönetin. Bu ortam için Python paketlerini ve diğer ayarları yönetin.
services: machine-learning
author: saachigopal
ms.author: sagopal
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 10491733d7473932a3eeb0e93dabe74a71d99fc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889051"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>Azure Machine Learning yazılım ortamları oluşturun & kullanın


Bu makalede Azure Machine Learning [ortamları](/python/api/azureml-core/azureml.core.environment.environment)oluşturma ve yönetme hakkında bilgi edinin. Ortamların yazılım bağımlılıklarını geliştikçe izlemek ve yeniden oluşturmak için ortamları kullanın.

Yazılım bağımlılığı yönetimi, geliştiriciler için ortak bir görevdir. Derlemelerin kapsamlı el ile yazılım yapılandırması olmadan tekrarlanabilir olmasını sağlamak istiyorsunuz. Azure Machine Learning `Environment` sınıfı, PIP ve Conda gibi yerel geliştirme çözümleri ve Docker özellikleri aracılığıyla dağıtılmış bulut geliştirme.

Bu makaledeki örneklerde nasıl yapılacağı gösterilmektedir:

* Bir ortam oluşturun ve paket bağımlılıklarını belirtin.
* Ortamları alın ve güncelleştirin.
* Eğitim için bir ortam kullanın.
* Web hizmeti dağıtımı için bir ortam kullanın.

Ortamların Azure Machine Learning nasıl çalıştığı hakkında üst düzey bir genel bakış için bkz. [ml ortamları nedir?](concept-environments.md) Geliştirme ortamlarını yapılandırma hakkında daha fazla bilgi için [buraya](how-to-configure-environment.md)bakın.

## <a name="prerequisites"></a>Önkoşullar

* [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/install) (>= 1.13.0)
* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Ortam oluşturma

Aşağıdaki bölümlerde, denemeleri için bir ortam oluşturabileceğiniz birçok yol araştırılamaz.

### <a name="instantiate-an-environment-object"></a>Ortam nesnesi örneği oluşturma

Bir ortamı el ile oluşturmak için `Environment` SDK 'dan sınıfını içeri aktarın. Ardından, bir ortam nesnesini başlatmak için aşağıdaki kodu kullanın.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-a-curated-environment"></a>Seçkin bir ortam kullanma

Seçkin ortamlar, Python paketlerinin koleksiyonlarını içerir ve çalışma alanınızda varsayılan olarak kullanılabilir. Bu ortamlar, çalışma hazırlığı maliyetini azaltan önbelleğe alınmış Docker görüntüleri tarafından desteklenir. Başlamak için şu popüler ortamlarından birini seçebilirsiniz: 

* _AzureML en düşük_ ortam, çalışma izlemeyi ve varlık yüklemeyi etkinleştirmek için en az bir paket kümesi içerir. Kendi ortamınız için bir başlangıç noktası olarak kullanabilirsiniz.

* _AzureML öğretici_ ortamı, ortak veri bilimi paketleri içerir. Bu paketler Scikit-öğren, Pandas, Matplotlib ve daha büyük bir azureml-SDK paketleri kümesi içerir.

Seçkin ortamların bir listesi için bkz. [seçkin ortamlar makalesi](resource-curated-environments.md).

Bu yöntemi kullanarak, `Environment.get` seçkin ortamların birini seçin:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Aşağıdaki kodu kullanarak, seçkin ortamları ve paketleri listeleyebilirsiniz:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  _AzureML_ ön eki ile kendi ortam adınızı başlatmayın. Bu ön ek, seçkin ortamlar için ayrılmıştır.

### <a name="use-conda-dependencies-or-pip-requirements-files"></a>Conda bağımlılıklarını veya PIP gereksinimleri dosyalarını kullanın

Bir Conda belirtimi veya bir zar gereksinimleri dosyasından bir ortam oluşturabilirsiniz. [`from_conda_specification()`](/python/api/azureml-core/azureml.core.environment.environment#from-conda-specification-name--file-path-)Yöntemini veya [`from_pip_requirements()`](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-) yöntemini kullanın. Yöntem bağımsız değişkeninde, ortam adınızı ve istediğiniz dosyanın dosya yolunu ekleyin. 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv",
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="enable-docker"></a>Docker 'ı etkinleştir

Docker 'ı etkinleştirdiğinizde, Azure Machine Learning bir Docker görüntüsü oluşturur ve bu kapsayıcı içinde belirtimlerinizi verilen bir Python ortamı oluşturur. Docker görüntüleri önbelleğe alınır ve yeniden kullanılır: yeni bir ortamdaki ilk çalıştırma genellikle görüntü derleme olduğu için daha uzun sürer.

[`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection)Azure Machine Learning `Environment` sınıfı, öğreticinizi çalıştırdığınız Konuk işletim sistemini en iyi şekilde özelleştirmenize ve denetlemenize olanak tanır. `arguments`Değişkeni, Docker Run komutuna geçirilecek ek bağımsız değişkenler belirtmek için kullanılabilir.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Varsayılan olarak, yeni oluşturulan Docker görüntüsü, çalışma alanıyla ilişkili kapsayıcı kayıt defterinde görüntülenir.  Depo adının *azureml/azureml_ \<uuid\>* biçimi vardır. Adın benzersiz tanımlayıcı (*UUID*) bölümü, ortam yapılandırmasından hesaplanan bir karmaya karşılık gelir. Bu yazışma, hizmetin, belirtilen ortam için bir görüntünün yeniden kullanım için zaten mevcut olup olmadığını belirlemesine izin verir.

#### <a name="use-a-prebuilt-docker-image"></a>Önceden oluşturulmuş bir Docker görüntüsü kullanma

Hizmet, varsayılan olarak, tarafından tanımlanan Ubuntu Linux tabanlı [temel görüntülerden](https://github.com/Azure/AzureML-Containers)birini otomatik olarak kullanır `azureml.core.environment.DEFAULT_CPU_IMAGE` . Ardından, belirtilen Azure ML ortamı tarafından tanımlanan tüm belirtilen Python paketlerini yüklenir. Diğer Azure ML CPU ve GPU temel görüntüleri kapsayıcı [deposunda](https://github.com/Azure/AzureML-Containers)mevcuttur. [Özel bir Docker temel görüntüsü](./how-to-deploy-custom-docker-image.md#create-a-custom-base-image)kullanmak da mümkündür.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> Azure Machine Learning yalnızca aşağıdaki yazılımları sağlayan Docker görüntülerini destekler:
> * Ubuntu 16,04 veya üzeri.
> * Conda 4.5. # veya üzeri.
> * Python 3.6 +.

#### <a name="use-your-own-dockerfile"></a>Kendi Dockerfile dosyanızı kullanın 

Ayrıca, özel bir Dockerfile belirtebilirsiniz. Docker komutunu kullanarak Azure Machine Learning temel görüntülerden birinden başlamak ```FROM``` ve kendi özel adımlarınızı eklemek en iyisidir. Python olmayan paketleri bağımlılık olarak yüklemeniz gerekiyorsa bu yaklaşımı kullanın. Temel görüntüyü None olarak ayarlamayı unutmayın.

Özel bir dockerfile 'ın Python 'un yüklü olması için Python 'un Azure Machine Learning ' de örtük bir bağımlılık olduğunu lütfen unutmayın.

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

Özel Docker görüntülerini kullanırken, reproducibility daha iyi güvence sağlamak için paket sürümlerini sabitlemenize önerilir.

#### <a name="specify-your-own-python-interpreter"></a>Kendi Python yorumlayıcılarınızı belirtin

Bazı durumlarda, özel taban görüntünüz kullanmak istediğiniz paketlere sahip bir Python ortamı zaten içerebilir.

Yüklü paketlerinizi kullanmak ve Conda 'yi devre dışı bırakmak için parametresini ayarlayın `Environment.python.user_managed_dependencies = True` . Temel görüntünün bir Python yorumlayıcı içerdiğinden ve eğitim betiğinizin gereksinimlerine yönelik paketlere sahip olduğundan emin olun.

Örneğin, 2. tuş paketinin yüklü olduğu bir temel Miniconda ortamında çalıştırmak için, önce paketi yüklemek için bir adımla birlikte bir Dockerfile belirtin. Ardından Kullanıcı tarafından yönetilen bağımlılıkları olarak ayarlayın `True` . 

Ayrıca, değişkenini ayarlayarak görüntünün içindeki belirli bir Python Yorumlayıcısına bir yol belirtebilirsiniz `Environment.python.interpreter_path` .

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

> [!WARNING]
> Docker yansımanıza bazı Python bağımlılıklarını yükler ve ayarlamayı unutursanız `user_managed_dependencies=True` , bu paketler yürütme ortamında mevcut olmayacaktır ve bu nedenle çalışma zamanı hatalara neden olur. Varsayılan olarak, Azure ML belirttiğiniz bağımlılıklara sahip bir Conda ortamı oluşturur ve temel görüntüde yüklü olan herhangi bir Python kitaplıklarını kullanmak yerine bu ortamda çalışmayı yürütür.

#### <a name="retrieve-image-details"></a>Görüntü ayrıntılarını al

Kayıtlı bir ortam için, `details` [Dockerımagedetails](/python/api/azureml-core/azureml.core.environment.dockerimagedetails) 'In (AzureML Python SDK >= 1,11) bir örneği olduğu ve dockerfile, kayıt defteri ve görüntü adı gibi ortam görüntüsüyle ilgili tüm bilgileri sağlayan aşağıdaki kodu kullanarak görüntü ayrıntılarını alabilirsiniz.

```python
details = environment.get_image_details(workspace=ws)
```

Bir çalıştırmanın yürütülmesinden otomatik olarak kaydedilen bir ortamdan görüntü ayrıntılarını almak için aşağıdaki kodu kullanın:

```python
details = run.get_environment().get_image_details(workspace=ws)
```

### <a name="use-existing-environments"></a>Mevcut ortamları kullanma

Yerel bilgisayarınızda var olan bir Conda ortamınız varsa, bu hizmeti kullanarak bir ortam nesnesi oluşturabilirsiniz. Bu stratejiyi kullanarak, uzak çalışmalardaki yerel etkileşimli ortamınızı yeniden kullanabilirsiniz.

Aşağıdaki kod, mevcut Conda ortamından bir ortam nesnesi oluşturur `mycondaenv` . [`from_existing_conda_environment()`](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-)Yöntemini kullanır.

``` python
myenv = Environment.from_existing_conda_environment(name="myenv",
                                                    conda_environment_name="mycondaenv")
```

Ortam tanımı, yöntemiyle kolayca düzenlenebilir bir biçimde bir dizine kaydedilebilir [`save_to_directory()`](/python/api/azureml-core/azureml.core.environment.environment#save-to-directory-path--overwrite-false-) . Değiştirildikten sonra, dizinden dosyalar yüklenirken yeni bir ortam oluşturulabilir.

```python
# save the enviroment
myenv.save_to_directory(path="path-to-destination-directory", overwrite=False)
# modify the environment definition
newenv = Environment.load_from_directory(path="path-to-source-directory")
```

### <a name="implicitly-use-the-default-environment"></a>Varsayılan ortamı örtük olarak kullan

Çalıştırmayı göndermeden önce betik çalıştırma yapılandırmanızda bir ortam belirtmezseniz, sizin için varsayılan bir ortam oluşturulur.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
src = ScriptRunConfig(source_directory=".", script="example.py", compute_target="local")

# Submit the run
run = myexp.submit(config=src)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

## <a name="add-packages-to-an-environment"></a>Bir ortama paket ekleme

Conda, PIP veya özel tekerlek dosyalarını kullanarak bir ortama paket ekleyin. Sınıfını kullanarak her bir paket bağımlılığını belirtin [`CondaDependency`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) . Ortama ekleyin `PythonSection` .

### <a name="conda-and-pip-packages"></a>Conda ve PIP paketleri

Bir Conda Package deposunda bir paket varsa, PIP yüklemesi yerine Conda yüklemesini kullanmanızı öneririz. Conda paketleri genellikle yüklemeyi daha güvenilir hale getirmek için önceden oluşturulmuş ikililer ile gelir.

Aşağıdaki örnek ortama ekler `myenv` . Sürüm 1.17.0 ekler `numpy` . Ayrıca paketi de ekler `pillow` . Örnek, [`add_conda_package()`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies#add-conda-package-conda-package-) sırasıyla yöntemini ve yöntemini kullanır [`add_pip_package()`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies#add-pip-package-pip-package-) .

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

Ayrıca ortamınıza ortam değişkenleri ekleyebilirsiniz. Bunlar daha sonra eğitim betiğinizdeki OS. Environ. Get kullanılarak kullanılabilir hale gelir.

```python
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

>[!IMPORTANT]
> Başka bir çalıştırma için aynı ortam tanımını kullanıyorsanız, Azure Machine Learning hizmeti ortamınızın önbelleğe alınmış görüntüsünü yeniden kullanır. Örneğin, ayrılmış paket bağımlılığı olan bir ortam oluşturursanız, ```numpy``` Bu ortam, _ortam oluşturma sırasında_ yüklenen paket sürümünü kullanmaya devam edecektir. Ayrıca, eşleşen tanımı olan gelecekteki tüm ortamlar eski sürümü kullanmaya devam eder. Daha fazla bilgi için bkz. [ortam oluşturma, önbelleğe alma ve yeniden kullanma](./concept-environments.md#environment-building-caching-and-reuse).

### <a name="private-python-packages"></a>Özel Python paketleri

Python paketlerini genel İnternet 'te kullanıma açmadan özel olarak ve güvenli bir şekilde kullanmak için, [özel Python paketlerini kullanma](how-to-use-private-python-packages.md)makalesine bakın.

## <a name="manage-environments"></a>Ortam yönetme

Ortamları, işlem hedefleri genelinde ve çalışma alanının diğer kullanıcılarıyla güncelleştirmek, izlemek ve yeniden kullanmak için yönetin.

### <a name="register-environments"></a>Ortamları kaydetme

Bir Web hizmeti çalıştırdığınızda veya dağıtırken ortam, çalışma alanınıza otomatik olarak kaydedilir. Ayrıca, yöntemini kullanarak ortamı el ile de kaydedebilirsiniz [`register()`](/python/api/azureml-core/azureml.core.environment%28class%29#register-workspace-) . Bu işlem, ortamı bulutta izlenen ve sürümlü bir varlığa yapar. Varlık, çalışma alanı kullanıcıları arasında paylaşılabilir.

Aşağıdaki kod `myenv` ortamı `ws` çalışma alanına kaydeder.

```python
myenv.register(workspace=ws)
```

Ortamı eğitim veya dağıtımda ilk kez kullandığınızda, çalışma alanına kaydedilir. Daha sonra işlem hedefine oluşturulup dağıtılır. Hizmet ortamları önbelleğe alır. Önbelleğe alınmış bir ortamı yeniden kullanmak yeni bir hizmet kullanmaktan veya güncelleştirilmiş bir hizmetin kullanılmasıyla çok daha az zaman alır.

### <a name="get-existing-environments"></a>Mevcut ortamları al

`Environment`Sınıfı, çalışma alanınızdaki mevcut ortamları almanıza izin veren yöntemler sunar. Ortamları ada, liste olarak veya belirli bir eğitim çalıştırmasına göre alabilirsiniz. Bu bilgiler, sorun giderme, denetim ve reproducibility için faydalıdır.

#### <a name="view-a-list-of-environments"></a>Ortamların listesini görüntüleyin

Sınıfını kullanarak çalışma alanınızdaki ortamları görüntüleyin [`Environment.list(workspace="workspace_name")`](/python/api/azureml-core/azureml.core.environment%28class%29#list-workspace-) . Sonra yeniden kullanmak için bir ortam seçin.

#### <a name="get-an-environment-by-name"></a>Ada göre ortam al

Ayrıca, belirli bir ortamı ada ve sürüme göre de alabilirsiniz. Aşağıdaki kod, [`get()`](/python/api/azureml-core/azureml.core.environment%28class%29#get-workspace--name--version-none-) `1` `myenv` çalışma alanındaki ortamın sürümünü almak için yöntemini kullanır `ws` .

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Çalıştırmaya özgü bir ortamı eğitme

Eğitim bittikten sonra belirli bir çalıştırma için kullanılan ortamı almak için, [`get_environment()`](/python/api/azureml-core/azureml.core.run.run#get-environment--) sınıfındaki yöntemini kullanın `Run` .

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Mevcut bir ortamı güncelleştirme

Var olan bir ortamı (örneğin, bir Python paketi ekleyerek) değiştirmenizi söyleyin. Bu işlem, bir çalıştırma gönderdiğinizde, bir model dağıttığınızda veya ortamı el ile kaydettiğinizde oluşturulur. Sürüm oluşturma, ortamın değişikliklerini zamana göre görüntülemenize olanak sağlar. 

Mevcut bir ortamda bir Python paketi sürümünü güncelleştirmek için, bu pakete ait sürüm numarasını belirtin. Tam sürüm numarasını kullanmazsanız Azure Machine Learning, özgün paket sürümleri ile mevcut ortamı yeniden kullanacaktır.

### <a name="debug-the-image-build"></a>Görüntü derlemesinde hata ayıklama

Aşağıdaki örnek, [`build()`](/python/api/azureml-core/azureml.core.environment%28class%29#build-workspace--image-build-compute-none-) bir ortamı Docker görüntüsü olarak el ile oluşturmak için yöntemini kullanır. Kullanarak görüntü derlemeden çıkış günlüklerini izler [`wait_for_completion()`](/python/api/azureml-core/azureml.core.image%28class%29#wait-for-creation-show-output-false-) . Sonra oluşturulan görüntü, çalışma alanının Azure Container Registry örneğinde görüntülenir. Bu bilgiler hata ayıklama için yararlıdır.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

İlk olarak, yöntemini kullanarak görüntüleri yerel olarak derlemek yararlı olur [`build_local()`](/python/api/azureml-core/azureml.core.environment.environment#build-local-workspace--platform-none----kwargs-) . Docker görüntüsü oluşturmak için isteğe bağlı parametresini ayarlayın `useDocker=True` . Elde edilen görüntüyü AzureML çalışma alanı kapsayıcısı kayıt defterine göndermek için, ayarlayın `pushImageToWorkspaceAcr=True` .

```python
build = env.build_local(workspace=ws, useDocker=True, pushImageToWorkspaceAcr=True)
```

> [!WARNING]
>  Bir ortamdaki bağımlılıkların veya kanalların sırasını değiştirmek yeni bir ortama neden olur ve yeni bir görüntü derlemesi gerektirir. Ayrıca, `build()` Yeni sürümler varsa, varolan bir görüntü için yöntemini çağırmak bağımlılıklarını güncelleştirir. 

## <a name="use-environments-for-training"></a>Eğitim için ortamları kullanma

Bir eğitim çalıştırması göndermek için ortamınızı, [işlem hedefini](concept-compute-target.md)ve eğitim Python betiğinizi çalıştırma yapılandırması olarak birleştirmeniz gerekir. Bu yapılandırma, çalıştırmaları göndermek için kullanılan bir sarmalayıcı nesnesidir.

Bir eğitim çalıştırması gönderdiğinizde, yeni bir ortam oluşturmak birkaç dakika sürebilir. Süre, gerekli bağımlılıkların boyutuna bağlıdır. Ortamlar hizmet tarafından önbelleğe alınır. Bu nedenle, ortam tanımı değişmeden kaldığı sürece, tam kurulum zamanına yalnızca bir kez tabi olursunuz.

Aşağıdaki yerel betik çalıştırma örneği, [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) sarmalayıcı nesneniz olarak kullanacağınız yeri gösterir.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Configure the ScriptRunConfig and specify the environment
src = ScriptRunConfig(source_directory=".", script="train.py", target="local", environment=myenv)

# Submit run 
run = exp.submit(src)
```

> [!NOTE]
> Çalıştırma geçmişini devre dışı bırakmak veya anlık görüntüleri çalıştırmak için altındaki ayarı kullanın `src.run_config.history` .

Çalışma yapılandırmanızda ortamı belirtmezseniz, hizmet, çalıştırmayı gönderdiğinizde varsayılan bir ortam oluşturur.

## <a name="use-environments-for-web-service-deployment"></a>Web hizmeti dağıtımı için ortamları kullanma

Modelinizi bir Web hizmeti olarak dağıtırken ortamları kullanabilirsiniz. Bu özellik, tekrarlanabilir, bağlantılı bir iş akışı sunar. Bu iş akışında, hem eğitim hesaplamanıza hem de çıkarım işlem ortamınızda aynı kitaplıkları kullanarak modelinizi eğitebilirsiniz, test edebilir ve dağıtabilirsiniz.


Web hizmeti dağıtımı için kendi ortamınızı tanımlıyorsanız, `azureml-defaults` >= 1.0.45 sürümünü bir PIP bağımlılığı olarak listemalısınız. Bu paket, modeli bir Web hizmeti olarak barındırmak için gereken işlevselliği içerir.

Bir Web hizmeti dağıtmak için ortam, çıkarım işlem, Puanlama betiği ve dağıtım nesnenizin kayıtlı modelini birleştirin [`deploy()`](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) . Daha fazla bilgi için bkz. [modellerin nasıl ve ne şekilde dağıtılacağı](how-to-deploy-and-where.md).

Bu örnekte, bir eğitim çalıştırmasını tamamladığınızı varsayalım. Artık bu modeli Azure Container Instances dağıtmak istiyorsunuz. Web hizmeti oluşturduğunuzda, model ve Puanlama dosyaları görüntüye bağlanır ve Azure Machine Learning çıkarım yığını görüntüye eklenir.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="notebooks"></a>Notebooks

Bu [makalede](./how-to-access-terminal.md#add-new-kernels) bir Conda ortamının bir not defterine çekirdek olarak nasıl yükleneceğine ilişkin bilgiler sağlanmaktadır.

[Özel bir Docker temel görüntüsünü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md) özel bir Docker temel görüntüsü kullanarak bir modelin nasıl dağıtılacağını gösterir.

Bu [örnek Not defteri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) , bir Spark modelinin Web hizmeti olarak nasıl dağıtılacağını göstermektedir.

## <a name="create-and-manage-environments-with-the-cli"></a>CLı ile ortam oluşturma ve yönetme

[Azure MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md) , Python SDK işlevlerinin çoğunu yansıtır. Ortamları oluşturmak ve yönetmek için bu uygulamayı kullanabilirsiniz. Bu bölümde tartıştığımız komutlar temel işlevselliği göstermektedir.

Aşağıdaki komut, belirtilen dizinde varsayılan bir ortam tanımı için dosyaları tanılar. Bu dosyalar JSON dosyalarıdır. SDK 'da karşılık gelen sınıf gibi çalışır. Dosyaları, özel ayarları olan yeni ortamlar oluşturmak için kullanabilirsiniz. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Belirtilen dizinden bir ortamı kaydetmek için aşağıdaki komutu çalıştırın.

```azurecli-interactive
az ml environment register -d myenvdir
```

Tüm kayıtlı ortamları listelemek için aşağıdaki komutu çalıştırın.

```azurecli-interactive
az ml environment list
```

Aşağıdaki komutu kullanarak kayıtlı bir ortamı indirin.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Sonraki adımlar

* Bir modeli eğitemek üzere yönetilen bir işlem hedefi kullanmak için bkz. [öğretici: model eğitme](tutorial-train-models-with-aml.md).
* Eğitilen bir modelden sonra [modellerin nasıl ve nereye dağıtılacağını](how-to-deploy-and-where.md)öğrenin.
* [ `Environment` Sınıf SDK başvurusunu](/python/api/azureml-core/azureml.core.environment%28class%29)görüntüleyin.

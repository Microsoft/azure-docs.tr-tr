---
title: Triton ile hizmet veren yüksek performanslı model (Önizleme)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ' de NVıDıA Triton çıkarım sunucusu ile modelinizi dağıtmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 9b7b8fe9c05d0de64dcd0cf7c6c324e0d03cb1ac
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874162"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton çıkarım sunucusuyla yüksek performanslı hizmet (Önizleme) 

Model çıkarımı için kullanılan Web hizmetinin performansını artırmak için [NVIDIA Triton çıkarım sunucusunu](https://aka.ms/nvidia-triton-docs) nasıl kullanacağınızı öğrenin.

Çıkarımı için model dağıtma yöntemlerinden biri Web hizmeti olarak belirlenir. Örneğin, Azure Kubernetes hizmetine veya Azure Container Instances bir dağıtım. Varsayılan olarak, Azure Machine Learning Web hizmeti dağıtımları için tek iş parçacıklı, *genel amaçlı* Web çerçevesi kullanır.

Triton, *çıkarım için iyileştirilmiş* bir çerçevedir. GPU 'ların daha iyi kullanımını ve düşük maliyetli çıkarımı sağlar. Sunucu tarafında, gelen istekleri işler ve bu toplu işlemleri çıkarımı için gönderir. Toplu işleme GPU kaynaklarını daha iyi kullanır ve önemli bir performans parçasıdır.

> [!IMPORTANT]
> Azure Machine Learning dağıtım için Triton kullanımı Şu anda __Önizleme__ aşamasındadır. Önizleme işlevselliği müşteri desteğinin kapsamına alınmayabilir. Daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Bu belgedeki kod parçacıkları tanım amaçlıdır ve bir çözümü tamamen gösteremeyebilir. Çalışan örnek kod için [Azure Machine Learning üç aylık dönemin uçtan uca örneklerine](https://aka.ms/triton-aml-sample)bakın.

> [!NOTE]
> [NVIDIA Triton çıkarım sunucusu](https://aka.ms/nvidia-triton-docs) , Azure Machine Learning ile tümleştirilmiş açık kaynaklı bir üçüncü taraf yazılımdır.

## <a name="prerequisites"></a>Önkoşullar

* Bir **Azure aboneliği**. Bir tane yoksa, [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.
* Azure Machine Learning [bir modelin nasıl ve nasıl dağıtılacağı hakkında](how-to-deploy-and-where.md) benzerlik.
* [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/) **veya** [Azure CLI](/cli/azure/) ve [Machine Learning uzantısı](reference-azure-machine-learning-cli.md).
* Yerel test için çalışan bir Docker yüklemesi. Docker 'ı yükleme ve doğrulama hakkında daha fazla bilgi için bkz. Docker belgelerindeki [Yönlendirme ve kurulum](https://docs.docker.com/get-started/) .

## <a name="architectural-overview"></a>Mimariye genel bakış

Kendi modeliniz için Triton kullanmayı denemeden önce, Azure Machine Learning nasıl çalıştığını ve varsayılan bir dağıtım ile nasıl Karşılaştırıldığı anlamak önemlidir.

**Triton olmadan varsayılan dağıtım**

* Birden çok [Gunic,](https://gunicorn.org/) gelen istekleri eşzamanlı olarak işleyecek şekilde başlatılır.
* Bu çalışanlar ön işleme, modeli çağırma ve işlem sonrası işlemleri işler. 
* İstemciler __Azure ML Puanlama URI__'sini kullanır. Örneğin, `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normal, üç aylık olmayan dağıtım mimarisi diyagramı":::

**Doğrudan Triton ile dağıtma**

* İstekler doğrudan Triton sunucusuna gider.
* Triton, GPU kullanımını en üst düzeye çıkarmak için istekleri toplu halde işler.
* İstemci, istek yapmak için __Triton URI__ 'sini kullanır. Örneğin, `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Yalnızca Triton ile ınenceconfig dağıtımı ve Python ara yazılımı yok":::

**Triton ile çıkarım yapılandırma dağıtımı**

* Birden çok [Gunic,](https://gunicorn.org/) gelen istekleri eşzamanlı olarak işleyecek şekilde başlatılır.
* İstekler, **Triton sunucusuna** iletilir. 
* Triton, GPU kullanımını en üst düzeye çıkarmak için istekleri toplu halde işler.
* İstemci, istek yapmak için __Azure ML Puanlama URI__ 'sini kullanır. Örneğin, `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Triton ve Python ara yazılımı ile dağıtım":::

Model dağıtımınız için Triton kullanmak üzere iş akışı:

1. Modelinize doğrudan bir araç sunar.
1. İstekleri, Triton dağıtılan modelinize gönderebildiğinizi doğrulayın.
1. Seçim Sunucu tarafı ön ve sonrası işlemleri için Python ara yazılımı katmanı oluşturma

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Python ön ve son işlem olmadan Triton dağıtma

İlk olarak, Triton çıkarımı sunucusunun modelinize hizmeti verebildiğini doğrulamak için aşağıdaki adımları izleyin.

### <a name="optional-define-a-model-config-file"></a>Seçim Model yapılandırma dosyası tanımlama

Model yapılandırma dosyası, kaç giriş olduğunu ve bu girişlerin hangi boyutlarda olacağını gösterir. Yapılandırma dosyasını oluşturma hakkında daha fazla bilgi için bkz. NVıDıA belgelerindeki [model yapılandırması](https://aka.ms/nvidia-triton-docs) .

> [!TIP]
> `--strict-model-config=false`Triton çıkarım sunucusunu başlatırken seçeneğini kullanıyoruz, bu da `config.pbtxt` Onnx veya TensorFlow modelleri için bir dosya sağlamanız gerekmediği anlamına gelir.
> 
> Bu seçenek hakkında daha fazla bilgi için bkz. NVıDıA belgelerinde [oluşturulan model yapılandırması](https://aka.ms/nvidia-triton-docs) .

### <a name="use-the-correct-directory-structure"></a>Doğru dizin yapısını kullanın

Azure Machine Learning bir modeli kaydederken, tek tek dosyaları veya dizin yapısını kaydedebilirsiniz. Triton kullanmak için model kaydı, adlı dizini içeren bir dizin yapısı için olmalıdır `triton` . Bu dizinin genel yapısı:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Bu dizin yapısı bir Triton model deposudur ve modelinlerinizin Triton ile çalışması için gereklidir. Daha fazla bilgi için bkz. NVıDıA belgelerindeki [Triton modeli depoları](https://aka.ms/nvidia-triton-docs) .

### <a name="register-your-triton-model"></a>Triton modelinizi kaydetme

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Hakkında daha fazla bilgi için `az ml model register` [başvuru belgelerine](/cli/azure/ml/model)bakın.

Model Azure Machine Learning ' de kaydedilirken, parametrenin değeri, `--model-path  -p` üç aylık dönemin üst klasörünün adı olmalıdır.  
Yukarıdaki örnekte,  `--model-path` ' modeller '.

`--name  -n`Örnekte, â € ̃ my_triton_modelâ €™ parametresinin değeri, Azure Machine Learning çalışma alanı bilinen model adı olacaktır. 

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
Daha fazla bilgi için [model sınıfına](/python/api/azureml-core/azureml.core.model.model)yönelik belgelere bakın.

---

### <a name="deploy-your-model"></a>Modelinizi dağıtın

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Azure Machine Learning aracılığıyla oluşturulan "aks-GPU" adlı GPU özellikli bir Azure Kubernetes hizmet kümeniz varsa, modelinizi dağıtmak için aşağıdaki komutu kullanabilirsiniz.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

[Modelleri dağıtma hakkında daha fazla bilgi için bu belgelere](how-to-deploy-and-where.md)bakın.

### <a name="call-into-your-deployed-model"></a>Dağıtılan modelinize çağrı

İlk olarak, Puanlama URI 'SI ve taşıyıcı belirteçlerinizi alın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Sonra, hizmetinizin çalıştığından emin olun: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Bu komut şuna benzer bilgiler döndürür. Göz önünde, `200 OK` Bu durum Web sunucusunun çalıştığı anlamına gelir.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

Bir sistem durumu denetimi gerçekleştirdikten sonra, çıkarım için Triton 'a veri göndermek üzere bir istemci oluşturabilirsiniz. İstemci oluşturma hakkında daha fazla bilgi için NVıDıA belgelerindeki [istemci örneklerine](https://aka.ms/nvidia-client-examples) bakın. Ayrıca [, Üçlü GitHub 'Da Python örnekleri](https://aka.ms/nvidia-triton-docs)de vardır.

Bu noktada, dağıtılan Web sunucunuza Python ön ve sonrası işlemleri eklemek istemiyorsanız, bu işlemi gerçekleştirebilirsiniz. Bu ön ve son işleme mantığını eklemek istiyorsanız, okumaya devam edin.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>Seçim Ön ve son işlemler için bir Python giriş betiği ile yeniden dağıtım

Triton 'nin modelinize hizmeti sağlayabilmesinin ardından, bir _giriş betiği_ tanımlayarak, ön işleme ve sonrası kod ekleyebilirsiniz. Bu dosya adı `score.py` . Giriş betikleri hakkında daha fazla bilgi için bkz. [bir giriş betiği tanımlama](how-to-deploy-and-where.md#define-an-entry-script).

İki ana adım, yönteinizde bir üç aylık HTTP istemcisini başlatmak `init()` ve işlevinizde bu istemciye çağırmak için kullanılır `run()` .

### <a name="initialize-the-triton-client"></a>Triton Istemcisini başlatma

Dosyanıza aşağıdaki örneğe benzer bir kod ekleyin `score.py` . Azure Machine Learning, localhost, bağlantı noktası 8000 ' de değinilmesi beklenir. Bu durumda, localhost yerel makinenizde bir bağlantı noktası değil, bu dağıtım için Docker görüntüsünün içindedir:

> [!TIP]
> `tritonhttpclient`PIP paketi, seçkin `AzureML-Triton` ortamda bulunur, bu nedenle bunu bir PIP bağımlılığı olarak belirtmeye gerek yoktur.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Üç aylık arama yapmak için Puanlama betiğinizi değiştirin

Aşağıdaki örnek, model için meta verilerin dinamik olarak nasıl isteneceğini gösterir:

> [!TIP]
> Triton istemcisinin yöntemini kullanarak, üç aylık olarak yüklenmiş modellerin meta verilerini dinamik olarak isteyebilirsiniz `.get_model_metadata` . Kullanım örneği için [örnek Not defteri](https://aka.ms/triton-aml-sample) ' ne bakın.

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Çıkarım yapılandırmasıyla yeniden dağıtın

Bir çıkarım yapılandırması, Python SDK veya Azure CLı kullanarak Azure Machine Learning dağıtım işleminin yanı sıra bir giriş betiği kullanmanıza olanak sağlar.

> [!IMPORTANT]
> `AzureML-Triton` [Seçkin ortamı](./resource-curated-environments.md)belirtmeniz gerekir.
>
> Python kod örneği `AzureML-Triton` adlı başka bir ortama klonlar `My-Triton` . Azure CLı kodu bu ortamı da kullanır. Ortam kopyalama hakkında daha fazla bilgi için [Environment. Clone ()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) başvurusuna bakın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> Çıkarım yapılandırması oluşturma hakkında daha fazla bilgi için bkz. [çıkarım yapılandırma şeması](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

Dağıtım tamamlandıktan sonra Puanlama URI 'SI görüntülenir. Bu yerel dağıtım için, olacaktır `http://localhost:6789/score` . Buluta dağıtırsanız, Puanlama URI 'sini almak için [az ml Service CLI göster](/cli/azure/ml/service#az_ml_service_show) komutunu kullanabilirsiniz.

Puanlama URI 'sine çıkarım istekleri gönderen bir istemci oluşturma hakkında daha fazla bilgi için bkz. [Web hizmeti olarak dağıtılan bir modeli](how-to-consume-web-service.md)kullanma.

### <a name="setting-the-number-of-workers"></a>Çalışan sayısını ayarlama

Dağıtımınızdaki çalışanların sayısını ayarlamak için ortam değişkenini ayarlayın `WORKER_COUNT` . Adlı bir [ortam](/python/api/azureml-core/azureml.core.environment.environment) nesneniz varsa `env` , şunları yapabilirsiniz:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Bu, Azure ML 'nin belirttiğiniz çalışan sayısını kullanmasını söyler.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure Machine Learning çalışma alanını kullanmaya devam etmek, ancak dağıtılan hizmetten kurtulabilmesi istiyorsanız, aşağıdaki seçeneklerden birini kullanın:


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---
## <a name="troubleshoot"></a>Sorun giderme

* [Başarısız bir dağıtımda sorun](how-to-troubleshoot-deployment.md)giderme, bir modeli dağıttığınızda karşılaşabileceğiniz sık karşılaşılan hataları giderme ve çözme veya geçici çözüm yapma hakkında bilgi edinin.

* Dağıtım günlükleri söz konusu **Tritonserver 'ın başlayamadığından**, lütfen [Nvidiaâ €™ s açık kaynak belgelerine bakın.](https://github.com/triton-inference-server/server)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Machine Learning üç aylık dönemin uçtan uca örneklerine bakın](https://aka.ms/aml-triton-sample)
* [Triton istemci örneklerine](https://aka.ms/nvidia-client-examples) göz atın
* [Üç aylık çıkarım sunucusu belgelerini](https://aka.ms/nvidia-triton-docs) okuyun
* [Azure Kubernetes Service’e dağıtma](how-to-deploy-azure-kubernetes-service.md)
* [Web hizmetini güncelleştirme](how-to-deploy-update-web-service.md)
* [Üretimde modeller için veri toplama](how-to-enable-data-collection.md)

---
title: Makine öğrenimi modellerini dağıtma
titleSuffix: Azure Machine Learning
description: Makine öğrenimi modellerinin nasıl ve nereye dağıtılacağını öğrenin. Azure Container Instances, Azure Kubernetes hizmeti, Azure IoT Edge ve FPGA 'e dağıtın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2
adobe-target: true
ms.openlocfilehash: f2128949090ce0ec2aa4ed66eb476384d662953a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872650"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Makine öğrenimi modellerini Azure 'a dağıtma

Machine Learning veya derin öğrenme modelinizi Azure bulutu 'nda bir Web hizmeti olarak dağıtmayı öğrenin. Ayrıca, Azure IoT Edge cihazlara dağıtabilirsiniz.

Modeli nerede dağıtırsanız dağıtın benzer bir iş akışı kullanılır:

1. Modeli kaydedin (isteğe bağlı, aşağıya bakın).
1. Bir çıkarım yapılandırması ( [kod dağıtımı](./how-to-deploy-no-code-deployment.md)kullanmadıkça) hazırlayın.
1. Bir giriş betiği hazırlayın ( [kod dağıtımı](./how-to-deploy-no-code-deployment.md)kullanmadıkça).
1. Bir işlem hedefi seçin.
1. Modeli işlem hedefine dağıtın.
1. Elde edilen Web hizmetini test edin.

Machine Learning dağıtım iş akışında yer alan kavramlar hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri yönetme, dağıtma ve izleme](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Önkoşullar

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).
- Bir model. Eğitilen bir modeliniz yoksa, [Bu öğreticide](https://aka.ms/azml-deploy-cloud)verilen model ve bağımlılık dosyalarını kullanabilirsiniz.
- [Machine Learning hizmeti Için Azure komut satırı arabirimi (CLI) uzantısı](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).
- Bir model. Eğitilen bir modeliniz yoksa, [Bu öğreticide](https://aka.ms/azml-deploy-cloud)verilen model ve bağımlılık dosyalarını kullanabilirsiniz.
- [Python için Azure Machine Learning yazılım geliştirme seti (SDK)](/python/api/overview/azure/ml/intro).

---

## <a name="connect-to-your-workspace"></a>Çalışma alanınıza bağlanma

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[Abonelik bağlamını ayarlamak](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)IÇIN Azure CLI belgelerindeki yönergeleri izleyin.

Ardından şunları yapın:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

erişiminiz olan çalışma alanlarını görmek için.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Bir çalışma alanına bağlanmak için SDK 'Yı kullanma hakkında daha fazla bilgi için bkz. [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro#workspace) belgeleri.


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Modelinizi kaydetme (isteğe bağlı)

Kayıtlı bir model, modelinizi oluşturan bir veya daha fazla dosya için mantıksal bir kapsayıcıdır. Örneğin, birden çok dosyada depolanan bir modeliniz varsa, bunları çalışma alanına tek bir model olarak kaydedebilirsiniz. Dosyaları kaydettikten sonra, kayıtlı modeli indirebilir veya dağıtabilir ve kaydettiğiniz tüm dosyaları alabilirsiniz.

> [!TIP] 
> Sürüm izleme için bir modelin kaydedilmesi önerilir, ancak gerekli değildir. Bir modeli kaydetmeden devam etmek istiyorsanız, [ınseenceconfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) veya [ üzerindeinferenceconfig.js](./reference-azure-machine-learning-cli.md#inference-configuration-schema) bir kaynak dizin belirtmeniz ve modelinizin bu kaynak dizinde yer aldığından emin olmanız gerekir.

> [!TIP]
> Bir modeli kaydettiğinizde, bir bulut konumunun (bir eğitim çalıştırmasında) ya da yerel bir dizinin yolunu sağlarsınız. Bu yol, kayıt işleminin bir parçası olarak karşıya yüklenecek dosyaları bulmak için yeterlidir. Giriş betiğinde kullanılan yol ile eşleşmesi gerekmez. Daha fazla bilgi için bkz. [giriş betiğinizdeki model dosyalarını bulma](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> `Tags`Azure Machine Learning Studio modeller sayfasında, `TagName : TagValue` müşteriler kullanmak yerine `TagName=TagValue` (boşluk olmadan) filtre ölçütü seçeneği kullanılırken

Aşağıdaki örneklerde bir modelin nasıl kaydedileceği gösterilmektedir.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML eğitim çalıştırmasında bir modeli kaydetme

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path`Parametresi, modelin bulut konumunu ifade eder. Bu örnekte, tek bir dosyanın yolu kullanılır. Model kaydına birden çok dosya eklemek için, `--asset-path` dosyaları içeren bir klasörün yoluna ayarlayın.

### <a name="register-a-model-from-a-local-file"></a>Yerel dosyadan model kaydetme

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Model kaydına birden çok dosya eklemek için, `-p` dosyaları içeren bir klasörün yoluna ayarlayın.

Hakkında daha fazla bilgi için `az ml model register` [başvuru belgelerine](/cli/azure/ml/model)bakın.

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML eğitim çalıştırmasında bir modeli kaydetme

  Bir modeli eğitebilmeniz için SDK 'yı kullandığınızda, modeli eğitidiğinize bağlı olarak bir [Run](/python/api/azureml-core/azureml.core.run.run) nesnesi ya da bir [oto mlrun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) nesnesi alabilirsiniz. Her nesne, bir deneme çalıştırması tarafından oluşturulan bir modeli kaydetmek için kullanılabilir.

  + Bir nesneden model kaydetme `azureml.core.Run` :
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Parametresi, modelin bulut konumunu ifade eder. Bu örnekte, tek bir dosyanın yolu kullanılır. Model kaydına birden çok dosya eklemek için, `model_path` dosyaları içeren bir klasörün yoluna ayarlayın. Daha fazla bilgi için [Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) belgelerine bakın.

  + Bir nesneden model kaydetme `azureml.train.automl.run.AutoMLRun` :

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Bu örnekte, `metric` ve `iteration` parametreleri belirtilmedi, bu nedenle en iyi birincil ölçüye sahip yineleme kaydedilir. `model_id`Bir model adı yerine çalıştırağından döndürülen değer kullanılır.

    Daha fazla bilgi için [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) belgelerine bakın.

    ' Dan kayıtlı bir model dağıtmak için `AutoMLRun` [Azure Machine Learning Studio 'da tek tıklamayla dağıt düğmesine tıklayarak](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)bunu yapmanızı öneririz. 
### <a name="register-a-model-from-a-local-file"></a>Yerel dosyadan model kaydetme

Modelin yerel yolunu sağlayarak bir modeli kaydedebilirsiniz. Bir klasörün ya da tek bir dosyanın yolunu sağlayabilirsiniz. Bu yöntemi, Azure Machine Learning eğitilen ve daha sonra indirilen modelleri kaydetmek için kullanabilirsiniz. Bu yöntemi, Azure Machine Learning dışında eğitilen modelleri kaydetmek için de kullanabilirsiniz.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **SDK ve ONNX kullanma**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Model kaydına birden çok dosya eklemek için, `model_path` dosyaları içeren bir klasörün yoluna ayarlayın.

Daha fazla bilgi için [model sınıfına](/python/api/azureml-core/azureml.core.model.model)yönelik belgelere bakın.

Azure Machine Learning dışında eğitilen modellerle çalışma hakkında daha fazla bilgi için bkz. [var olan bir modeli dağıtma](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Giriş betiği tanımlama

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Bir çıkarım yapılandırması tanımlama


Bir çıkarım yapılandırması, modelinizi içeren Web hizmeti ayarlamayı açıklar. Modeli dağıtırken daha sonra kullanılır.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

En az bir çıkarım yapılandırması şöyle yazılabilir:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir",
    "environment": {
    "docker": {
        "arguments": [],
        "baseDockerfile": null,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "enabled": false,
        "sharedVolumes": true,
        "shmSize": null
    },
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "name": "my-deploy-env",
    "python": {
        "baseCondaEnvironment": null,
        "condaDependencies": {
            "channels": [
                "conda-forge",
                "pytorch"
            ],
            "dependencies": [
                "python=3.6.2",
                "torchvision"
                {
                    "pip": [
                        "azureml-defaults",
                        "azureml-telemetry",
                        "scikit-learn==0.22.1",
                        "inference-schema[numpy-support]"
                    ]
                }
            ],
            "name": "project_environment"
        },
        "condaDependenciesFile": null,
        "interpreterPath": "python",
        "userManagedDependencies": false
    },
    "version": "1"
}
```

Bu, Machine Learning dağıtımının `score.py` `./working_dir` gelen istekleri işlemek için dizindeki dosyayı kullanacağı ve ortamda belirtilen Python paketleriyle Docker görüntüsünü kullanacağı belirtir `project_environment` .

Çıkarım yapılandırmalarının daha kapsamlı bir açıklaması için [Bu makaleye bakın](./reference-azure-machine-learning-cli.md#inference-configuration-schema) . 

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek şunları göstermektedir:

1. çalışma alanınızdan [seçkin bir ortamı](resource-curated-environments.md) yükleme
1. Ortamı kopyalama
1. `scikit-learn`Bağımlılık olarak belirtme.
1. Bir ınenceconfig oluşturmak için ortamı kullanma

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Ortamlar hakkında daha fazla bilgi için bkz. [eğitim ve dağıtım için ortamları oluşturma ve yönetme](how-to-use-environments.md).

Çıkarım yapılandırması hakkında daha fazla bilgi için bkz. [ınenceconfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) sınıfı belgeleri.

---

> [!TIP] 
> Bir çıkarım yapılandırmasıyla özel bir Docker görüntüsü kullanma hakkında daha fazla bilgi için bkz. [özel bir Docker görüntüsü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>İşlem hedefi seçin

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Dağıtım yapılandırması tanımlama

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Bir dağıtım yapılandırması için kullanılabilen seçenekler, seçtiğiniz işlem hedefine bağlı olarak farklılık gösterir.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Daha fazla bilgi için [Bu başvuruya](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)bakın.

# <a name="python"></a>[Python](#tab/python)

Modelinizi dağıtılmadan önce dağıtım yapılandırmasını tanımlamanız gerekir. *Dağıtım yapılandırması, Web hizmetini barındıracak işlem hedefine özgüdür.* Örneğin, bir modeli yerel olarak dağıttığınızda, hizmetin istekleri kabul ettiği bağlantı noktasını belirtmeniz gerekir. Dağıtım yapılandırması, giriş betiğinizin bir parçası değil. Model ve giriş betiğini barındıracak işlem hedefinin özelliklerini tanımlamak için kullanılır.

Ayrıca, çalışma alanınız ile ilişkili bir Azure Kubernetes Service (AKS) örneğiniz yoksa, işlem kaynağını oluşturmanız da gerekebilir.

Aşağıdaki tabloda her işlem hedefi için bir dağıtım yapılandırması oluşturma örneği verilmiştir:

| İşlem hedefi | Dağıtım yapılandırma örneği |
| ----- | ----- |
| Yerel | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Yerel, Azure Container Instances ve AKS Web Hizmetleri için sınıflar şu kaynaktan içeri aktarılabilir `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>Machine Learning modelinizi dağıtma

Artık modelinizi dağıtmaya hazırsınız. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Kayıtlı model kullanma

Modelinize Azure Machine Learning çalışma alanınızda kaydolduysanız, "MyModel: 1" değerini modelinizin adı ve sürüm numarası ile değiştirin.

```azurecli-interactive
az ml model deploy -n tutorial -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Yerel model kullanma

Modelinize kaydolmayı tercih ediyorsanız, modelinize sunulacak yerel bir dizin belirtmek için, üzerinde inferenceconfig.js"sourceDirectory" parametresini geçirebilirsiniz.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json --name my_deploy
```

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnekte yerel bir dağıtım gösterilmektedir. Sözdizimi, önceki adımda seçtiğiniz işlem hedefine bağlı olarak değişir.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Daha fazla bilgi için bkz. [Localwebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice), [model. deploy ()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)ve [WebService](/python/api/azureml-core/azureml.core.webservice.webservice)belgeleri.

---

### <a name="understanding-service-state"></a>Hizmet durumunu anlama

Model dağıtımı sırasında hizmet durumu değişikliğini tam olarak dağıttığında görebilirsiniz.

Aşağıdaki tabloda farklı hizmet durumları açıklanmaktadır:

| Web hizmeti durumu | Description | Son durum?
| ----- | ----- | ----- |
| Kta | Hizmet, dağıtım sürecinde. | No |
| Uygun Değil | Hizmet dağıtıldı, ancak şu anda ulaşılamaz durumda.  | No |
| Unschedulable | Kaynak eksikliği nedeniyle hizmet şu anda dağıtılamıyor. | No |
| Başarısız | Hizmet bir hata veya kilitlenme nedeniyle dağıtılamadı. | Yes |
| Sağlam | Hizmet sağlıklı ve uç nokta kullanılabilir. | Yes |

> [!TIP]
> Dağıtım sırasında, işlem hedefleri için Docker görüntüleri Azure Container Registry (ACR) ile oluşturulur ve yüklenir. Varsayılan olarak, Azure Machine Learning *temel* hizmet katmanını kullanan bir ACR oluşturur. Çalışma alanınızın ACR 'sini standart veya Premium katmana değiştirmek, görüntü oluşturma ve işlem hedeflerinize dağıtmak için geçen süreyi azaltabilir. Daha fazla bilgi için bkz. [Azure Container Registry hizmet katmanları](../container-registry/container-registry-skus.md).

> [!NOTE]
> Azure Kubernetes Service 'e (AKS) bir model dağıtıyorsanız, bu küme için [Azure izleyicisini](../azure-monitor/containers/container-insights-enable-existing-clusters.md) etkinleştirmenizi öneririz. Bu, genel küme durumunu ve kaynak kullanımını anlamanıza yardımcı olur. Aşağıdaki kaynakların yararlı olduğunu da görebilirsiniz:
>
> * [AKS kümenizi etkileyen Kaynak Durumu olaylarını denetleme](../aks/aks-resource-health.md)
> * [Azure Kubernetes hizmet tanılaması](../aks/concepts-diagnostics.md)
>
> Bir modeli sağlıksız veya aşırı yüklenmiş bir kümeye dağıtmaya çalışıyorsanız, sorun yaşanması beklenmektedir. AKS kümesi sorunlarını gidermek için yardıma ihtiyacınız varsa lütfen AKS desteğiyle iletişime geçin.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Toplu çıkarım
Azure Machine Learning Işlem hedefleri Azure Machine Learning tarafından oluşturulur ve yönetilir. Bunlar, Azure Machine Learning işlem hatlarından toplu tahmin için kullanılabilirler.

Azure Machine Learning Işlem ile Batch çıkarımı hakkında yönergeler için bkz. [Batch öngörülerini çalıştırma](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge çıkarımı
Kenara dağıtım desteği önizleme aşamasındadır. Daha fazla bilgi için bkz. [Azure Machine Learning IoT Edge modül olarak dağıtma](../iot-edge/tutorial-deploy-machine-learning.md).

## <a name="delete-resources"></a>Kaynakları silme

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Dağıtılan bir Web hizmetini silmek için kullanın `az ml service <name of webservice>` .

Çalışma alanınızdan kayıtlı bir modeli silmek için şunu kullanın `az ml model delete <model id>`

[Bir Web hizmetini silme](/cli/azure/ml/service#az_ml_service_delete) ve [bir modeli silme](/cli/azure/ml/model#az_ml_model_delete)hakkında daha fazla bilgi edinin.

# <a name="python"></a>[Python](#tab/python)

Dağıtılmış bir Web hizmetini silmek için kullanın `service.delete()` .
Kayıtlı bir modeli silmek için kullanın `model.delete()` .

Daha fazla bilgi için bkz. [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) ve [model. Delete ()](/python/api/azureml-core/azureml.core.model.model#delete--)belgeleri.

---

## <a name="next-steps"></a>Sonraki adımlar

* [Başarısız bir dağıtımın sorunlarını giderme](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service’e dağıtma](how-to-deploy-azure-kubernetes-service.md)
* [Web hizmetlerini kullanmak için istemci uygulamaları oluşturma](how-to-consume-web-service.md)
* [Web hizmetini güncelleştirme](how-to-deploy-update-web-service.md)
* [Özel bir Docker görüntüsü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md)
* [Azure Machine Learning Studio 'da otomatik ML çalıştırmaları için bir tıklama dağıtımı](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [TLS kullanarak Azure Machine Learning aracılığıyla web hizmetinin güvenliğini sağlama](how-to-secure-web-service.md)
* [Application Insights Azure Machine Learning modellerinizi izleyin](how-to-enable-app-insights.md)
* [Üretimde modeller için veri toplama](how-to-enable-data-collection.md)
* [Model dağıtımları için olay uyarıları ve Tetikleyicileri oluşturma](how-to-use-event-grid.md)
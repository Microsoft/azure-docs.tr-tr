---
title: ML denemeleri için MLflow Izleme
titleSuffix: Azure Machine Learning
description: Ölçümleri & yapıtları günlüğe kaydetmek ve Databricks, yerel ortamınız veya VM ortamınızdan modeller dağıtmak için Azure Machine Learning ile MLflow ayarlayın.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 946350af0c1a4e8140fbf7f926061aae250e9969
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716476"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>MLflow ve Azure Machine Learning modellerini izleme ve model dağıtma (Önizleme)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning ile mlflow [izleme](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)olarak bilinen MLFLOW izleme URI ve günlük API 'sinin nasıl etkinleştirileceği gösterilmektedir. Bunu yapmanız şunları yapmanızı sağlar:

+ [Azure Machine Learning çalışma](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces)alanınızdaki deneme ölçümlerini ve yapıtları izleyin ve günlüğe kaydedin. Denemeleri için MLflow Izlemeyi zaten kullanıyorsanız, çalışma alanı eğitim ölçümlerini ve modellerini depolamak için merkezi, güvenli ve ölçeklenebilir bir konum sağlar.

+ MLflow denemeleri 'nizi Azure Machine Learning Web hizmeti olarak dağıtın. Web hizmeti olarak dağıtarak, Azure Machine Learning izleme ve veri DRI algılama işlevlerini üretim modellerinize uygulayabilirsiniz. 

[Mlflow](https://www.mlflow.org) , Machine Learning denemeleri 'in yaşam döngüsünü yönetmeye yönelik açık kaynaklı bir kitaplıktır. MLFlow Izleme, bir MLflow bileşeni olan ve denemenizin ortam, uzak bir işlem hedefi üzerinde, bir sanal makine üzerinde yerel olarak bilgisayarınızda veya bir Azure Databricks kümesinde olduğu gibi, eğitim çalıştırma ölçümleri ve model yapıtlarını kaydeder ve izler.

Aşağıdaki diyagramda, MLflow Izlemenin yanı sıra, Azure Machine Learning çalışma alanınızda bir deneyin çalışma ölçümlerini ve mağaza modeli yapılarını izlersiniz.

![Azure Machine Learning diyagramı ile mlflow](media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Bu belgedeki bilgiler öncelikli olarak, model eğitimi sürecini izlemek isteyen veri bilimcileri ve geliştiricileri içindir. Kotalar, tamamlanan eğitim çalıştırmaları veya tamamlanmış model dağıtımları gibi Azure Machine Learning 'den kaynak kullanımını ve olayları izlemeyi ilgilenen bir yöneticiyseniz, bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow ve Azure Machine Learning istemcilerini karşılaştırın

 Aşağıdaki tabloda Azure Machine Learning kullanılabilecek farklı istemciler ve ilgili işlev özellikleri özetlenmektedir.

 MLflow Izleme yalnızca [Azure Machine Learning Python SDK 'sı](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)aracılığıyla kullanılabilen ölçüm günlüğü ve yapıt depolama işlevleri sunar.


| | MLflow Izleme & dağıtımı | Python SDK Azure Machine Learning |  Azure Machine Learning CLı | Azure Machine Learning Studio|
|---|---|---|---|---|
| Çalışma alanını yönet |   | ✓ | ✓ | ✓ |
| Veri depolarını kullanma  |   | ✓ | ✓ | |
| Günlük ölçümleri      | ✓ | ✓ |   | |
| Yapıtları karşıya yükle | ✓ | ✓ |   | |
| Ölçümleri görüntüle     | ✓ | ✓ | ✓ | ✓ |
| İşlemi yönetme   |   | ✓ | ✓ | ✓ |
| Modelleri dağıtma    | ✓ | ✓ | ✓ | ✓ |
|Model performansını izleme||✓|  |   |
| Veri değişikliklerini algılama |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Ön koşullar

* [MLflow 'ı yükler.](https://mlflow.org/docs/latest/quickstart.html)
* [Azure Machine Learning SDK 'sını](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) yerel BILGISAYARıNıZA yüklediğinizde SDK, çalışma alanınıza erişmek Için mlflow bağlantısını sağlar.
* [Azure Machine Learning çalışma alanı oluşturun](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Yerel çalıştırmaları izle

Azure Machine Learning ile MLflow Izleme, yerel çalıştırmanıza ait günlüğe kaydedilen ölçümleri ve yapıtları Azure Machine Learning çalışma alanınıza depolamanıza olanak sağlar.

Denemeleri yerel olarak bir Jupyter Notebook veya kod düzenleyicide çalıştırdığınız Azure Machine Learning ile MLflow Izlemeyi kullanmak için `azureml-contrib-run` paketini yükler.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Hizmetin geliştirilmesi için çalışdığımız için, azureml. contrib ad alanı sıklıkla değişir. Bu nedenle, bu ad alanındaki her şey Microsoft tarafından tam olarak desteklenmez ve önizleme olarak değerlendirilmelidir.

MLflow 'un izleme URI 'sine erişmek ve çalışma alanınızı yapılandırmak için `mlflow` ve [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) sınıfları içeri aktarın.

Aşağıdaki kodda `get_mlflow_tracking_uri()` yöntemi, çalışma alanına benzersiz bir izleme URI adresi atar, `ws`ve `set_tracking_uri()` MLflow izleme URI 'sini bu adrese yönlendirir.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>İzleme URI 'SI saat veya daha az bir saate kadar geçerlidir. Bir boşta kalma zamanından sonra betiğinizi yeniden başlatırsanız, yeni bir URI almak için get_mlflow_tracking_uri API 'sini kullanın.

MLflow deney adını `set_experiment()` olarak ayarlayın ve öğreticinizi `start_run()`ile başlatın. Ardından, MLflow günlüğü API 'sini etkinleştirmek ve eğitim çalıştırma ölçümlerinizi günlüğe kaydetmeye başlamak için `log_metric()` kullanın.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Uzak çalıştırmaları izle

Azure Machine Learning ile MLflow Izleme, uzak çalıştırmanıza ait günlüğe kaydedilen ölçümleri ve yapıtları Azure Machine Learning çalışma alanınıza depolamanıza olanak sağlar.

Uzak çalıştırmalar, modellerinizi GPU etkin sanal makineler veya Machine Learning İşlem kümeler gibi daha güçlü bir şekilde bir şekilde eğitmenizi sağlar. Farklı işlem seçenekleri hakkında bilgi edinmek için bkz. [model eğitimi için işlem hedeflerini ayarlama](how-to-set-up-training-targets.md) .

İşlem ve eğitim çalıştırma ortamınızı [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) sınıfıyla yapılandırın. Ortamın [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) bölümüne `mlflow` ve `azure-contrib-run` PIP paketleri ekleyin. Daha sonra, işlem hedefi olarak uzak hesaplamanıza [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) oluşturun.

```Python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Eğitim betiğinizdeki MLflow günlüğü API 'Lerini kullanmak için `mlflow` içeri aktarın ve çalıştırma ölçümlerinizi günlüğe kaydetmeye başlayın.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Bu işlem ve eğitim çalıştırma yapılandırmasıyla, bir çalıştırma göndermek için `Experiment.submit('train.py')` yöntemini kullanın. Bu yöntem, MLflow izleme URI 'sini otomatik olarak ayarlar ve MLflow oturumunu çalışma alanınıza yönlendirir.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure Databricks çalıştırmalarını izleyin

Azure Machine Learning ile MLflow Izleme, Databricks 'ınızdan günlüğe kaydedilen ölçümleri ve yapıtları Azure Machine Learning çalışma alanınızda depolamanıza olanak tanır.

Mlflow denemeleri Azure Databricks çalıştırmak için öncelikle bir [Azure Databricks çalışma alanı ve küme](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) oluşturmanız gerekir

Kümenizde, kümenizin gerekli işlevlere ve sınıflara erişebildiğinden emin olmak için, PyPI 'den *azureml-mlflow* kitaplığını yüklediğinizden emin olun.
Buradan, deneme not defterinizi içeri aktarın, kümenizi bu kümeye ekleyin ve denemenizi çalıştırın. 

### <a name="install-libraries"></a>Kitaplıkları yükler

Kümenizi kümenize yüklemek için **Kitaplıklar** sekmesine gidin ve **Yeni** ' ye tıklayın

 ![Azure Machine Learning diyagramı ile mlflow](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

**Paket** alanına, azureml-mlflow yazın ve ardından Install ' a tıklayın. Bu adımı, denemeniz için kümenize diğer ek paketleri yüklemek için gereken şekilde tekrarlayın.

 ![Azure Machine Learning diyagramı ile mlflow](media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Not defterinizi ve çalışma alanınızı ayarlama

Kümeniz kurulduktan sonra, deneme not defterinizi içeri aktarın, açın ve kümenizi ona ekleyin.

Aşağıdaki kod, deneme Not defterinizde olmalıdır. Bu kod, çalışma alanınızı oluşturmak için Azure aboneliğinizin ayrıntılarını alır. Bu kod, var olan bir kaynak grubuna ve Azure Machine Learning çalışma alanına sahip olduğunuzu varsayar, aksi takdirde [bunları oluşturabilirsiniz](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks ve Azure Machine Learning çalışma alanlarınızı bağlama

[Azure Portal](https://ms.portal.azure.com), Azure Databricks (ADB) çalışma alanınızı yeni veya mevcut bir Azure Machine Learning çalışma alanına bağlayabilirsiniz. Bunu yapmak için ADB çalışma alanınıza gidin ve sağ alt köşedeki **Azure Machine Learning çalışma alanını bağla** düğmesini seçin. Çalışma alanlarınızı bağlamak, deneme verilerinizi Azure Machine Learning çalışma alanında izlemenize olanak sağlar. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>MLflow izlemeyi çalışma alanınıza bağlama

Çalışma alanınızı örnekledikten sonra MLflow izleme URI 'sini ayarlayın. Bunu yaparak, MLflow izlemesini Azure Machine Learning çalışma alanına bağlarsınız. Bağlandıktan sonra, tüm denemeleri yönetilen Azure Machine Learning izleme hizmetine eklenecektir.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Not defterinizde MLflow Izlemeyi doğrudan ayarlama

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Eğitim betiğinizdeki mlflow günlüğü API 'Lerini kullanmak için mlflow 'u içeri aktarın ve çalıştırma ölçümlerinizi günlüğe kaydetmeye başlayın. Aşağıdaki örnek, dönem kayıp ölçümünü günlüğe kaydeder. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>MLflow Izleme ayarını otomatikleştirin

Kümelerinizde sonraki tüm denemeler Not defteri oturumunda izleme URI 'sini el ile ayarlamak yerine bu [Azure Machine Learning Izleme kümesi Init betiğini](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)kullanarak otomatik olarak bunu yapın.

Doğru yapılandırıldığında, MLflow izleme verilerinizi Azure Machine Learning REST API ve tüm istemcilerinde ve Azure Databricks MLflow Kullanıcı arabirimi aracılığıyla ya da MLflow istemcisini kullanarak görebileceksiniz.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Çalışma alanınızdaki ölçümleri ve yapıtları görüntüleme

MLflow günlüğü 'ndeki ölçümler ve yapıtlar çalışma alanınızda tutulur. Bunları dilediğiniz zaman görüntülemek için, çalışma alanınıza gidin ve [Azure Machine Learning Studio](https://ml.azure.com)'daki çalışma alanınızda ada göre deneyin ' i bulun.  Veya aşağıdaki kodu çalıştırın. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>MLflow modellerini Web hizmeti olarak dağıtma

MLflow denemeleri 'in Azure Machine Learning Web hizmeti olarak dağıtımı, Azure Machine Learning model yönetimi ve veri drime özelliklerinden yararlanmanıza ve bunları üretim modellerinize uygulamanıza olanak tanır.

Aşağıdaki diyagramda, MLflow dağıtım API 'SI ile, var olan MLflow modellerinizi bir Azure Machine Learning Web hizmeti olarak dağıtabileceğiniz, çerçeveler--PyTorch, TensorFlow, scikit-öğren, ONNX, vb. ve içindeki üretim modellerinizi yönetme çalışma alanınız.

![Azure Machine Learning diyagramı ile mlflow](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Modelinizi günlüğe kaydedin

Dağıtmadan önce, modelinize ve dağıtım için yol konumuna başvurabilmeniz için modelinizin kaydedildiğinden emin olun. Eğitim betiğinizdeki aşağıdaki [mlflow. sköğren. log _model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) yöntemine benzer bir kod olmalıdır. Bu, modelinizi belirtilen çıktılar dizinine kaydeder. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Bu modelin çalıştırılması gereken bağımlılıkların ve ortamın sözlük gösterimini geçirmek için `conda_env` parametresini ekleyin.

### <a name="retrieve-model-from-previous-run"></a>Önceki çalıştırınızdan model al

Çalıştırmayı almak için, çalışma KIMLIĞI ve modelin kaydedildiği yerde çalıştırma geçmişi içinde yol gereklidir. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Docker görüntüsü oluşturma

`mlflow.azureml.build_image()` işlevi, kaydedilmiş modelden çerçeve duyarlı bir şekilde bir Docker görüntüsü oluşturur. Otomatik olarak çerçeveye özgü ınsınlama sarmalayıcı kodunu oluşturur ve paket bağımlılıklarını sizin için belirtir. Model yolunu, çalışma alanınızı, çalıştırma KIMLIĞINI ve diğer parametreleri belirtin.

Aşağıdaki kod, bir Scikit-öğren denemesi için model_uri yolu olarak *:/< Run. ıd >/Model çalıştırmaları* kullanarak bir Docker görüntüsü oluşturur.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
Docker görüntüsünün oluşturulması birkaç dakika sürebilir. 

### <a name="deploy-the-docker-image"></a>Docker görüntüsünü dağıtma 

Görüntü oluşturulduktan sonra, görüntüyü bir Web hizmeti olarak dağıtmak için Azure Machine Learning SDK 'sını kullanın.

İlk olarak, dağıtım yapılandırmasını belirtin. Azure Container Instance (acı), Azure Kubernetes hizmeti (AKS), ölçeklenebilir üretim dağıtımları için uygun olan hızlı geliştirme ve test dağıtımı için uygun bir seçimdir.

#### <a name="deploy-to-aci"></a>ACI'ye dağıtma

[Deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) yöntemiyle dağıtım yapılandırmanızı ayarlayın. Web hizmetinizi izlemeye yardımcı olmak için Etiketler ve açıklamalar da ekleyebilirsiniz.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Ardından, Azure Machine Learning SDK 'nın [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) metodunu kullanarak görüntüyü dağıtın. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>AKS’ye dağıtma

AKS 'e dağıtmak için, önce bir AKS kümesi oluşturun ve dağıtmak istediğiniz Docker görüntüsünü getirin. Bu örnekte, ACI dağıtımından önceden oluşturulmuş görüntüyü getirin.

Önceki ACI dağıtımından görüntüyü almak için [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) sınıfını kullanın. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

[ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) yöntemini kullanarak bir aks kümesi oluşturun. Yeni bir küme oluşturmak 20-25 dakika sürebilir.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
[Deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) yöntemiyle dağıtım yapılandırmanızı ayarlayın. Web hizmetinizi izlemeye yardımcı olmak için Etiketler ve açıklamalar da ekleyebilirsiniz.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Ardından, Azure Machine Learning SDK 'nın [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) metodunu kullanarak görüntüyü dağıtın. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

Hizmet dağıtımı birkaç dakika sürebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Çalışma alanınızda günlüğe kaydedilen ölçümleri ve yapıtları kullanmayı planlamıyorsanız, bunları tek tek silme özelliği şu anda kullanılamaz. Bunun yerine, depolama hesabı ve çalışma alanını içeren kaynak grubunu silip herhangi bir ücret ödemeniz gerekmez:

1. Azure portalının en sol tarafındaki **Kaynak gruplarını** seçin.

   ![Azure portal Sil](media/how-to-use-mlflow/delete-resources.png)

1. Listeden oluşturduğunuz kaynak grubunu seçin.

1. **Kaynak grubunu sil**'i seçin.

1. Kaynak grubu adını girin. Ardından **Sil**’i seçin.


## <a name="example-notebooks"></a>Örnek Not defterleri

[Azure ML Not defterleri Ile Mlflow](https://aka.ms/azureml-mlflow-examples) , bu makalede sunulan kavramları gösterir ve genişletir.

## <a name="next-steps"></a>Sonraki adımlar
* [Modellerinizi yönetin](concept-model-management-and-deployment.md).
* [Veri kayması](how-to-monitor-data-drift.md)için üretim modellerinizi izleyin.

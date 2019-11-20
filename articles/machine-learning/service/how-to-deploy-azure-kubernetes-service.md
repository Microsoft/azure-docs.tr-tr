---
title: Azure Kubernetes hizmetine modeller dağıtma
titleSuffix: Azure Machine Learning
description: Azure Kubernetes hizmetini kullanarak Azure Machine Learning modellerinizi bir Web hizmeti olarak dağıtmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: 054e0154d5b300df40840bb409879ff879a6a3b0
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123272"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes hizmet kümesine model dağıtma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Kubernetes Service (AKS) üzerinde bir modeli Web hizmeti olarak dağıtmak için Azure Machine Learning kullanmayı öğrenin. Azure Kubernetes hizmeti, yüksek ölçekli üretim dağıtımları için uygundur. Aşağıdaki özelliklerde bir veya daha fazlasına ihtiyacınız varsa Azure Kubernetes hizmetini kullanın:

- __Hızlı yanıt süresi__.
- Dağıtılan hizmetin __Otomatik ölçeklendirilmesi__ .
- GPU ve alan-programlanabilir kapı dizileri (FPGA) gibi __donanım hızlandırma__ seçenekleri.

> [!IMPORTANT]
> Küme ölçeklendirme, Azure Machine Learning SDK aracılığıyla sağlanmaz. Bir AKS kümesindeki düğümleri ölçeklendirme hakkında daha fazla bilgi için bkz. [AKS kümesindeki düğüm sayısını ölçeklendirme](../../aks/scale-cluster.md).

Azure Kubernetes hizmetine dağıtırken, __çalışma alanınıza bağlı__bir aks kümesine dağıtırsınız. Bir AKS kümesini çalışma alanınıza bağlamak için iki yol vardır:

* Azure Machine Learning SDK, Machine Learning CLı veya [Azure Machine Learning Studio 'yu](https://ml.azure.com)kullanarak aks kümesini oluşturun. Bu işlem, kümeyi otomatik olarak çalışma alanına bağlar.
* Azure Machine Learning çalışma alanınıza mevcut bir AKS kümesi iliştirin. Bir küme Azure Machine Learning SDK, Machine Learning CLı veya Azure Machine Learning Studio kullanılarak iliştirilebilir.

> [!IMPORTANT]
> Oluşturma veya ekleme işlemi bir kerelik görevdir. Bir AKS kümesi çalışma alanına bağlandıktan sonra dağıtım için kullanabilirsiniz. Artık gerekmiyorsa AKS kümesini ayırabilirsiniz veya silebilirsiniz. Detaalı veya silindikten sonra, artık kümeye dağıtım yapamayacaktır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

- Bir Machine Learning modeli, çalışma alanınıza kaydedildi. Kayıtlı bir modeliniz yoksa, bkz. [modellerin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md).

- [Machine Learning hizmeti Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)veya [Azure Machine Learning Visual Studio Code uzantısı](how-to-vscode-tools.md).

- Bu makaledeki __Python__ kod parçacıkları aşağıdaki değişkenlerin ayarlandığı varsayılır:

    * `ws`-çalışma alanınıza ayarlanır.
    * `model`-kayıtlı modelinize ayarlanır.
    * `inference_config`-modelin çıkarım yapılandırmasına ayarlanır.

    Bu değişkenleri ayarlama hakkında daha fazla bilgi için bkz. [modellerin nasıl ve ne şekilde dağıtılacağı](how-to-deploy-and-where.md).

- Bu makaledeki __CLI__ kod parçacıkları `inferenceconfig.json` bir belge oluşturduğunuzu varsayar. Bu belgeyi oluşturma hakkında daha fazla bilgi için bkz. [modellerin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Yeni bir AKS kümesi oluşturma

**Tahmini Süre**: yaklaşık 20 dakika.

AKS kümesi oluşturma veya iliştirme, çalışma alanınız için tek seferlik bir işlemdir. Bu kümeye birden çok dağıtımlar için yeniden kullanabilirsiniz. Kümeyi veya onu içeren kaynak grubunu silerseniz, bir dahaki sefer dağıtmanız gerektiğinde yeni bir küme oluşturmanız gerekir. Çalışma alanınıza eklenmiş birden çok AKS kümeniz olabilir.

> [!TIP]
> Azure sanal ağını kullanarak AKS kümenizi güvenli hale getirmek istiyorsanız, önce sanal ağı oluşturmanız gerekir. Daha fazla bilgi için bkz. [Azure sanal ağ Ile güvenli deneme ve çıkarım](how-to-enable-virtual-network.md#aksvnet).

Üretim yerine __geliştirme__, __doğrulama__ve __Test__ için bir aks kümesi oluşturmak istiyorsanız, geliştirme __testi__için __küme amacını__ belirtebilirsiniz.

> [!WARNING]
> `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`ayarlarsanız, oluşturulan küme üretim düzeyi trafiğe uygun değildir ve çıkarım sürelerini artırabilir. Geliştirme ve test kümeleri de hata toleransı garantisi vermez. Geliştirme ve test kümeleri için en az 2 sanal CPU önerilir.

Aşağıdaki örneklerde SDK ve CLı kullanarak yeni bir AKS kümesinin nasıl oluşturulacağı gösterilmektedir:

**SDK 'Yı kullanma**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)için, `agent_count` ve `vm_size`için özel değerler seçerseniz ve `cluster_purpose` `DEV_TEST`değilse, `agent_count` çarpılarak 12 sanal CPU 'ya eşit veya daha büyük olduğundan emin olmanız gerekir.`vm_size` Örneğin, 4 sanal CPU içeren bir `vm_size` "Standard_D3_v2" kullanırsanız, 3 veya daha büyük bir `agent_count` seçmeniz gerekir.
>
> Azure Machine Learning SDK, bir AKS kümesini ölçeklendirmeye yönelik destek sağlamaz. Kümedeki düğümleri ölçeklendirmek için Azure Machine Learning Studio 'daki AKS kümeniz için Kullanıcı arabirimini kullanın. Kümenin VM boyutunu değil, yalnızca düğüm sayısını değiştirebilirsiniz.

Bu örnekte kullanılan sınıflar, Yöntemler ve parametreler hakkında daha fazla bilgi için, aşağıdaki başvuru belgelerine bakın:

* [AksCompute. Clusteramacını](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**CLı 'yi kullanma**

```azurecli
az ml computetarget create aks -n myaks
```

Daha fazla bilgi için bkz. [az ml computetarget Create ask](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

## <a name="attach-an-existing-aks-cluster"></a>Mevcut bir AKS kümesini iliştirme

**Tahmini süre:** Yaklaşık 5 dakika.

Azure aboneliğinizde zaten AKS kümeniz varsa ve sürüm 1,14 ' den düşükse, görüntünüzü dağıtmak için kullanabilirsiniz.

> [!TIP]
> Mevcut AKS kümesi, Azure Machine Learning çalışma alanınızdan bir Azure bölgesinde olabilir.
>
> Azure sanal ağını kullanarak AKS kümenizi güvenli hale getirmek istiyorsanız, önce sanal ağı oluşturmanız gerekir. Daha fazla bilgi için bkz. [Azure sanal ağ Ile güvenli deneme ve çıkarım](how-to-enable-virtual-network.md#aksvnet).

Bir çalışma alanına AKS kümesi eklerken, `cluster_purpose` parametresini ayarlayarak kümeyi nasıl kullanacağınızı tanımlayabilirsiniz.

`cluster_purpose` parametresini ayarlamayın veya `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`ayarlarsanız, kümede en az 12 sanal CPU kullanılabilir olmalıdır.

`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`ayarlarsanız, kümenin 12 sanal CPU 'ya sahip olması gerekmez. Geliştirme ve test için en az 2 sanal CPU önerilir. Ancak, geliştirme/test için yapılandırılan bir küme, üretim düzeyi trafiğe uygun değildir ve çıkarım sürelerini artırabilir. Geliştirme ve test kümeleri de hata toleransı garantisi vermez.

> [!WARNING]
> Çalışma alanınızdan aynı AKS kümesine birden çok, eşzamanlı ek oluşturmayın. Örneğin, iki farklı ad kullanarak bir AKS kümesini çalışma alanına ekleme. Her yeni ek önceki mevcut ekleri keser.
>
> Bir AKS kümesini yeniden eklemek istiyorsanız (örneğin, SSL 'yi veya başka bir küme yapılandırma ayarını değiştirmek için), önce [Akscompute. detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)kullanarak var olan eki kaldırmanız gerekir.

Azure CLı veya portalını kullanarak bir AKS kümesi oluşturma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [AKS kümesi oluşturma (CLı)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS kümesi oluşturma (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Aşağıdaki örneklerde, var olan bir AKS kümesini çalışma alanınıza nasıl ekleyebileceğiniz gösterilmektedir:

**SDK 'Yı kullanma**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Bu örnekte kullanılan sınıflar, Yöntemler ve parametreler hakkında daha fazla bilgi için, aşağıdaki başvuru belgelerine bakın:

* [AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute. Clusteramacını](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**CLı 'yi kullanma**

CLı kullanarak var olan bir kümeyi eklemek için, var olan kümenin kaynak KIMLIĞINI almanız gerekir. Bu değeri almak için aşağıdaki komutu kullanın. `myexistingcluster`, AKS kümenizin adıyla değiştirin. `myresourcegroup`, kümeyi içeren kaynak grubuyla değiştirin:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Bu komut aşağıdaki metne benzer bir değer döndürür:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Mevcut kümeyi çalışma alanınıza eklemek için aşağıdaki komutu kullanın. `aksresourceid`, önceki komutun döndürdüğü değerle değiştirin. `myresourcegroup`, çalışma alanınızı içeren kaynak grubuyla değiştirin. `myworkspace`, çalışma alanınızın adıyla değiştirin.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Daha fazla bilgi için, [az ml computetarget Attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) başvurusuna bakın.

## <a name="deploy-to-aks"></a>AKS’ye dağıtma

Azure Kubernetes hizmetine bir model dağıtmak için, gereken işlem kaynaklarını açıklayan bir __dağıtım yapılandırması__ oluşturun. Örneğin, çekirdek ve bellek sayısı. Ayrıca, modeli ve Web hizmetini barındırmak için gereken ortamı açıklayan bir __çıkarım yapılandırmasına__ihtiyacınız vardır. Çıkarım yapılandırmasını oluşturma hakkında daha fazla bilgi için bkz. [modellerin nasıl ve ne şekilde dağıtılacağı](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>SDK’yı kullanarak

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Bu örnekte kullanılan sınıflar, Yöntemler ve parametreler hakkında daha fazla bilgi için, aşağıdaki başvuru belgelerine bakın:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>CLı 'yi kullanma

CLı kullanarak dağıtmak için aşağıdaki komutu kullanın. `myaks`, AKS işlem hedefinin adıyla değiştirin. `mymodel:1`, kayıtlı modelin adı ve sürümü ile değiştirin. `myservice`, bu hizmete verilecek adla değiştirin:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

Daha fazla bilgi için, [az ml model dağıtım](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) başvurusuna bakın. 

### <a name="using-vs-code"></a>VS Code'u kullanma

VS Code kullanımı hakkında bilgi için bkz. [vs Code uzantısı aracılığıyla AKS 'e dağıtma](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> VS Code aracılığıyla dağıtmak, AKS kümesinin önceden oluşturulmasını veya çalışma alanınıza eklenmesini gerektirir.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Denetimli dağıtımı kullanarak AKS 'e model dağıtma (Önizleme)
Uç noktaları kullanarak, denetimli bir biçimde model sürümlerini çözümleyin ve yükseltin. Tek bir uç noktanın arkasında 6 ' a kadar sürüm dağıtın ve her dağıtılan sürüme yönelik Puanlama trafiği yüzdesini yapılandırın. Uç noktaların ve dağıtılan sürümlerin işletimsel ölçümlerini görüntülemek için App Insights 'ı etkinleştirebilirsiniz.

### <a name="create-an-endpoint"></a>Uç nokta oluşturma
Modellerinizi dağıtmaya hazırsanız, bir Puanlama uç noktası oluşturun ve ilk sürümünüzü dağıtın. Aşağıdaki adımda, son noktanın SDK kullanarak nasıl dağıtılacağı ve oluşturulacağı gösterilmektedir. İlk dağıtım varsayılan sürüm olarak tanımlanacak, yani tüm sürümlerde belirtilmeyen trafik yüzdebirlik değeri varsayılan sürüme gidecektir.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace 
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = true, 
                                                              tags = {'sckitlearn':'demo'},
                                                              decription = testing versions,
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Bir uç noktaya güncelleştirme ve sürüm ekleme

Uç noktanıza başka bir sürüm ekleyin ve Puanlama trafiği yüzdebirlik sürümünü sürüme göre yapılandırın. İki tür sürüm, bir denetim ve bir işleme sürümü vardır. Tek bir denetim sürümüne karşı karşılaştırmanıza yardımcı olmak için birden çok işleme sürümü olabilir. 

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb" 
endpoint.create_version(version_name = version_name_add, 
                        inference_config=inference_config,
                        models=[model], 
                        tags = {'modelVersion':'b'}, 
                        description = "my second version", 
                        traffic_percentile = 10)
```

Mevcut sürümleri güncelleştirin veya bir uç noktada silin. Sürümün varsayılan türünü, denetim türünü ve trafik Yüzdeliğini değiştirebilirsiniz. 
 
 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type 
endpoint.update_version(version_name=endpoint.versions["versionb"].name, 
                        description="my second version update", 
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint 
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Web hizmeti kimlik doğrulaması

Azure Kubernetes hizmetine dağıtım yaparken, __anahtar tabanlı__ kimlik doğrulaması varsayılan olarak etkindir. __Belirteç tabanlı__ kimlik doğrulamasını da etkinleştirebilirsiniz. Belirteç tabanlı kimlik doğrulaması, istemcilerin dağıtılan hizmette istek yapmak için kullanılan bir kimlik doğrulama belirteci istemek için bir Azure Active Directory hesabı kullanmasını gerektirir.

Kimlik doğrulamasını __devre dışı bırakmak__ için, dağıtım yapılandırmasını oluştururken `auth_enabled=False` parametresini ayarlayın. Aşağıdaki örnek, SDK 'Yı kullanarak kimlik doğrulamasını devre dışı bırakır:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

İstemci uygulamasından kimlik doğrulama hakkında daha fazla bilgi için, [Web hizmeti olarak dağıtılan Azure Machine Learning modelini](how-to-consume-web-service.md)kullanma konusuna bakın.

### <a name="authentication-with-keys"></a>Anahtarlar ile kimlik doğrulama

Anahtar kimlik doğrulaması etkinleştirilirse, birincil ve ikincil kimlik doğrulama anahtarını almak için `get_keys` yöntemini kullanabilirsiniz:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Bir anahtarı yeniden oluşturmanız gerekiyorsa [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) kullanın

### <a name="authentication-with-tokens"></a>Belirteçlerle kimlik doğrulama

Belirteç kimlik doğrulamasını etkinleştirmek için, bir dağıtım oluştururken veya güncelleştirirken `token_auth_enabled=True` parametresini ayarlayın. Aşağıdaki örnek SDK kullanarak belirteç kimlik doğrulamasını mümkün bir şekilde sunar:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Belirteç kimlik doğrulaması etkinleştirilirse, bir JWT belirteci almak için `get_token` yöntemini ve belirtecin süre sonu süresini kullanabilirsiniz:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Belirtecin `refresh_by` zamanından sonra yeni bir belirteç istemeniz gerekir.
>
> Microsoft, Azure Machine Learning çalışma alanınızı Azure Kubernetes hizmet kümeniz ile aynı bölgede oluşturmanızı kesinlikle önerir. Bir belirteçle kimlik doğrulaması yapmak için Web hizmeti, Azure Machine Learning çalışma alanınızın oluşturulduğu bölgeye bir çağrı yapar. Çalışma alanınızın bölgesi kullanılamıyorsa, kümeniz çalışma alanınızdan farklı bir bölgedeyse, Web hizmetiniz için de bir belirteç getirimeyeceksiniz. Bu, çalışma alanınızın bölgesi yeniden kullanılabilir olana kadar belirteç tabanlı kimlik doğrulamanın kullanılamamasına neden olur. Ayrıca, kümenizin bölgesi ve çalışma alanınızın bölgesi arasındaki mesafe ne kadar fazlaysa bir belirteci getirmek için bu daha uzun sürer.

## <a name="update-the-web-service"></a>Web hizmetini güncelleştirmek

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Bir sanal ağda güvenli deneme ve çıkarım](how-to-enable-virtual-network.md)
* [Özel bir Docker görüntüsü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md)
* [Dağıtım sorunlarını giderme](how-to-troubleshoot-deployment.md)
* [Azure Machine Learning web hizmetleri SSL ile güvenli hale getirme](how-to-secure-web-service.md)
* [Bir web hizmeti olarak ML modeli kullanma](how-to-consume-web-service.md)
* [Application Insights Azure Machine Learning modellerinizi izleyin](how-to-enable-app-insights.md)
* [Üretimde modeller için veri toplama](how-to-enable-data-collection.md)

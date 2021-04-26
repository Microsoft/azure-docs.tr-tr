---
title: Erişim denetimi için Yönetilen kimlikler kullanma (Önizleme)
titleSuffix: Azure Machine Learning
description: Yönetilen kimliklerin Azure Machine Learning çalışma alanından Azure kaynaklarına erişimi denetlemek için nasıl kullanılacağını öğrenin.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 3246f6668a653c05599001a2f8d8fc8d376936ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642415"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Azure Machine Learning ile yönetilen kimlikler kullanma (Önizleme)

[Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) , çalışma alanınızı, *kaynaklara erişmek için gereken en düşük izinlerle* yapılandırmanıza olanak tanır. 

Azure Machine Learning çalışma alanını güvenilir şekilde yapılandırırken, çalışma alanıyla ilişkili farklı hizmetlerin doğru erişim düzeyine sahip olduğundan emin olmak önemlidir. Örneğin, Machine Learning iş akışı sırasında, çalışma alanının Docker görüntüleri için Azure Container Registry (ACR) ve eğitim verilerine yönelik depolama hesapları için erişimi olması gerekir. 

Ayrıca, Yönetilen kimlikler izinler üzerinde ayrıntılı denetime izin verir, örneğin belirli işlem kaynaklarından belirli bir ACR 'ye erişimi verebilir veya iptal edebilirsiniz.

Bu makalede, yönetilen kimliklerin nasıl kullanılacağını şu şekilde öğreneceksiniz:

 * ACR 'ye Yönetici Kullanıcı erişimini etkinleştirmek zorunda kalmadan, Azure Machine Learning çalışma alanınız için ACR 'yi yapılandırın ve kullanın.
 * Eğitim veya çıkarım için temel görüntüleri çekmek üzere çalışma alanınıza özel bir ACR 'ye erişin.
 * İlişkili kaynaklara erişmek için Kullanıcı tarafından atanan yönetilen kimlikle çalışma alanı oluşturun.

> [!IMPORTANT]
> Azure Machine Learning ile kaynaklara erişimi denetlemek için yönetilen kimliklerin kullanılması şu anda önizleme aşamasındadır. Önizleme işlevselliği, destek veya hizmet düzeyi anlaşması garantisi olmadan "olduğu gibi" verilmiştir. Daha fazla bilgi için, [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)bölümüne bakın.
 
## <a name="prerequisites"></a>Önkoşullar

- Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).
- [Machine Learning hizmeti Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md)
- [Python SDK Azure Machine Learning](/python/api/overview/azure/ml/intro).
- Rol atamak için, Azure aboneliğiniz için oturum açma, [yönetilen kimlik operatörü](../role-based-access-control/built-in-roles.md#managed-identity-operator) rolüne veya gerekli eylemlere (örneğin, __sahip__) izin veren başka bir role sahip olmalıdır.
- [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)oluşturma ve bunlarla çalışma konusunda bilgi sahibi olmanız gerekir.

## <a name="configure-managed-identities"></a>Yönetilen kimlikleri yapılandırma

Bazı durumlarda, Azure Container Registry yönetici kullanıcı erişimine izin vermemek gerekir. Örneğin, ACR paylaşılabilir ve diğer kullanıcılar tarafından yönetici erişimine izin vermemelisiniz. Ya da, yönetici kullanıcı ile ACR 'nin oluşturulmasına abonelik düzeyindeki bir ilke tarafından izin verilmiyor.

> [!IMPORTANT]
> Azure Container Instance (acı) üzerinde çıkarım için Azure Machine Learning kullanırken ACR üzerinde yönetici kullanıcı erişimi __gereklidir__. Çıkarımı için modelleri acı 'ye dağıtmaya çalışırsanız bu ayarı devre dışı bırakın.

Yönetici Kullanıcı erişimini etkinleştirmeden ACR oluşturduğunuzda Yönetilen kimlikler, Docker görüntülerini derlemek ve çekmek için ACR 'ye erişmek üzere kullanılır.

Çalışma alanını oluştururken kendi ACR 'nizi yönetici kullanıcı devre dışı bırakabilirsiniz. Alternatif olarak, Azure Machine Learning çalışma alanı ACR oluşturma ve daha sonra yönetici kullanıcısını devre dışı bırakma.

### <a name="bring-your-own-acr"></a>Kendi ACR 'nizi getirin

ACR yönetici kullanıcısına abonelik ilkesi tarafından izin verilmedikçe, önce yönetici kullanıcı olmadan ACR oluşturmanız ve ardından çalışma alanıyla ilişkilendirmeniz gerekir. Ayrıca, yönetici kullanıcı ile var olan ACR kullanıyorsanız, çalışma alanına iliştirebilirsiniz.

Bağımsız değişken ayarlamadan [Azure CLI 'dan](../container-registry/container-registry-get-started-azure-cli.md) ```--admin-enabled``` veya yönetici kullanıcı etkinleştirilmeden Azure Portal 'Ten ACR oluşturun. Sonra, Azure Machine Learning çalışma alanı oluştururken ACR 'nin Azure Kaynak KIMLIĞINI belirtin. Aşağıdaki örnek, var olan bir ACR kullanan yeni bir Azure ML çalışma alanı oluşturmayı göstermektedir:

> [!TIP]
> Parametresinin değerini almak için `--container-registry` [az ACR Show](/cli/azure/acr#az_acr_show) komutunu kullanarak ACR 'nizin bilgilerini görüntüleyin. `id`Alanı ACR 'nizin kaynak kimliğini içerir.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Azure Machine Learning Service çalışma alanı ACR oluşturmasına izin ver

Kendi ACR 'nizi getirmeyin, tek yapmanız gereken bir işlem gerçekleştirdiğinizde Azure Machine Learning hizmeti sizin için bir tane oluşturur. Örneğin, Machine Learning İşlem için bir eğitim çalıştırın, bir ortam oluşturun veya bir Web hizmeti uç noktası dağıtın. Çalışma alanı tarafından oluşturulan ACR yönetici kullanıcısının etkinleştirilmesini sağlar ve yönetici kullanıcıyı el ile devre dışı bırakmanız gerekir.

1. Yeni çalışma alanı oluşturma

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. ACR gerektiren bir eylem gerçekleştirin. Örneğin, [bir modeli eğitmek için öğretici](tutorial-train-models-with-aml.md).

1. Küme tarafından oluşturulan ACR adını alın:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Bu komut, aşağıdaki metne benzer bir değer döndürür. Metnin yalnızca, ACR örnek adı olan son kısmını istersiniz:

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Yönetici kullanıcıyı devre dışı bırakmak için ACR 'yi güncelleştirin:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Eğitim için Docker görüntülerine erişmek üzere yönetilen kimliğe sahip işlem oluşturma

Çalışma alanı ACR 'ye erişmek için, sistem tarafından atanan yönetilen kimlik etkinken makine öğrenimi işlem kümesi oluşturun. İşlem oluştururken veya aşağıdaki kullanarak Azure CLı 'dan Azure portal veya Studio 'dan kimlik etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [yönetilen kimliği işlem kümeleriyle kullanma](how-to-create-attach-compute-cluster.md#managed-identity).

# <a name="python"></a>[Python](#tab/python)

[AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration)ile bir işlem kümesi oluştururken, `identity_type` yönetilen kimlik türünü ayarlamak için parametresini kullanın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Studio 'da bir işlem kümesi oluştururken yönetilen kimliği yapılandırma hakkında daha fazla bilgi için bkz. [yönetilen kimlik ayarlama](how-to-create-attach-compute-studio.md#managed-identity).

---

Yönetilen bir kimlik, eğitim için Docker görüntülerini çekmeyi etkinleştirmek üzere çalışma alanı ACR üzerinde otomatik olarak ACRPull rolüne sahiptir.

> [!NOTE]
> Önce işlem oluşturursanız, çalışma alanı ACR oluşturulduktan önce, ACRPull rolünü el ile atamanız gerekir.

## <a name="access-base-images-from-private-acr"></a>Özel ACR 'den temel görüntülere erişme

Varsayılan olarak Azure Machine Learning, Microsoft tarafından yönetilen bir genel depodan gelen Docker temel görüntülerini kullanır. Daha sonra bu görüntülerde eğitim veya çıkarım ortamınızı oluşturur. Daha fazla bilgi için bkz. [ml ortamları nedir?](concept-environments.md).

Kuruluşunuza iç özel bir temel görüntü kullanmak için özel ACR 'nize erişmek üzere yönetilen kimlikler kullanabilirsiniz. İki kullanım durumu vardır:

 * Eğitim için temel görüntüyü olduğu gibi kullanın.
 * Temel olarak özel görüntüyle Azure Machine Learning yönetilen görüntü oluşturun.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Eğitim için Docker temel görüntüsünü makine öğrenimi işlem kümesine çekme

Daha önce açıklandığı gibi, sistem tarafından atanan yönetilen kimlikle makine öğrenimi işlem kümesi oluşturun. Ardından, yönetilen kimliğin asıl KIMLIĞINI saptayın.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

İsteğe bağlı olarak, Kullanıcı tarafından atanan bir yönetilen kimlik atamak için işlem kümesini güncelleştirebilirsiniz:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

İşlem kümesinin temel görüntüleri çekmesine izin vermek için özel ACR üzerinde yönetilen hizmet kimliği ACRPull rolünü verin

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Son olarak, bir eğitim çalıştırması gönderdiğinizde, [ortam tanımında](how-to-use-environments.md#use-existing-environments)taban görüntünün konumunu belirtin.

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Taban görüntünün doğrudan işlem kaynağına çekildiğinden emin olmak için, ayarlayın `user_managed_dependencies = True` ve bir Dockerfile belirtmeyin. Aksi takdirde Azure Machine Learning hizmet yeni bir Docker görüntüsü oluşturmaya çalışır ve başarısız olur çünkü yalnızca bilgi işlem kümesi ACR 'den temel görüntüyü çekme erişimine sahiptir.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Eğitim veya çıkarım için özel ACR 'den temel görüntüde Azure Machine Learning yönetilen ortam oluşturun

Bu senaryoda Azure Machine Learning hizmeti, özel bir ACR 'den sağladığınız temel görüntünün üzerinde eğitim veya çıkarım ortamı oluşturur. Görüntü oluşturma görevi ACR görevlerini kullanarak çalışma alanında ACR görevlerini yaptığından, erişime izin vermek için daha fazla adım gerçekleştirmeniz gerekir.

1. __Kullanıcı tarafından atanan yönetilen kimlik__ oluşturun ve kimliği __özel ACR__'ye erişim izni verin.  
1. Çalışma alanına __sistem tarafından atanan yönetilen kimliğe__ , önceki adımdan __Kullanıcı tarafından atanan yönetilen__ kimlik Için yönetilen bir kimlik operatörü rolü verin. Bu rol, çalışma alanının yönetilen ortamı oluşturmak için Kullanıcı tarafından atanan yönetilen kimliği ACR görevine atamasını sağlar. 

    1. Çalışma alanı sistem tarafından atanan yönetilen kimliğin asıl KIMLIĞINI edinin:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Yönetilen kimlik operatörü rolüne izin ver:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        UAı kaynak KIMLIĞI, Kullanıcı tarafından atanan kimliğin Azure Kaynak KIMLIĞI biçimindedir `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. [Workspace.set_connection yöntemi](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-)kullanarak çalışma alanı bağlantılarında __Kullanıcı tarafından atanan yönetilen kimliğin__ dış ACR ve istemci kimliğini belirtin:

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Yapılandırma tamamlandıktan sonra, eğitim veya çıkarım için ortamlar oluştururken özel ACR 'den temel görüntüleri kullanabilirsiniz. Aşağıdaki kod parçacığı, bir ortam tanımında temel görüntü ACR ve görüntü adının nasıl belirtileceğini gösterir:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

İsteğe bağlı olarak, yönetilen kimlik kaynak URL 'sini ve istemci KIMLIĞINI [registryıdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity)kullanarak ortam tanımında belirtebilirsiniz. Kayıt defteri kimliğini açık olarak kullanırsanız, daha önce belirtilen tüm çalışma alanı bağlantılarını geçersiz kılar:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>Çıkarım için Docker görüntülerini kullanma

Daha önce açıklandığı gibi yönetici kullanıcı olmadan ACR 'yi yapılandırdıktan sonra, Azure Kubernetes hizmetinden (AKS) yönetici anahtarları olmadan Docker görüntülerine çıkarım için de erişebilirsiniz. Çalışma alanına AKS eklediğinizde veya bağladığınızda, kümenin hizmet sorumlusu çalışma alanı ACR 'ye otomatik olarak ACRPull erişimi atanır.

> [!NOTE]
> Kendi AKS kümenizi verirseniz, kümenin yönetilen kimlik yerine hizmet sorumlusu etkinleştirilmiş olması gerekir.

## <a name="create-workspace-with-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlikle çalışma alanı oluştur

Çalışma alanı oluştururken, ilişkili kaynaklara erişmek için kullanılacak kullanıcı tarafından atanan bir yönetilen kimlik belirtebilirsiniz: ACR, Keykasası, Storage ve App Insights.

Önce [Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli)ve YÖNETILEN kimliğin ARM kaynak kimliğini bir yere göz atın.

Sonra, çalışma alanını oluşturmak için Azure CLı veya Python SDK 'Yı kullanın. CLı kullanırken, parametresini kullanarak KIMLIĞI belirtin `--primary-user-assigned-identity` . SDK kullanırken kullanın `primary_user_assigned_identity` . Aşağıdaki parametreleri kullanarak yeni bir çalışma alanı oluşturmak için Azure CLı ve Python kullanmanın örnekleri aşağıda verilmiştir:

__Azure CLI__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

Kullanıcı tarafından atanan yönetilen kimliğe sahip bir çalışma alanı oluşturmak için [ARM şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) da kullanabilirsiniz.

> [!IMPORTANT]
> Kendi ilişkili kaynaklarınızı ayarlarsanız Azure Machine Learning hizmeti oluşturmak yerine bu kaynaklarda yönetilen kimlik rollerine izin vermeniz gerekir. Atamaları yapmak için [rol atama ARM şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) kullanın.

(Şifreleme için müşteri tarafından yönetilen anahtarlar) [] içeren bir çalışma alanı için https://docs.microsoft.com/azure/machine-learning/concept-data-encryption , depolamadan Key Vault kimlik doğrulaması yapmak için Kullanıcı tarafından atanan bir yönetilen kimlik geçirebilirsiniz. Yönetilen kimliği geçirmek için __Kullanıcı tarafından atanan-Identity-for-CMK-Encryption__ (CLI) veya __user_assigned_identity_for_cmk_encryption__ (SDK) bağımsız değişkenini kullanın. Bu yönetilen kimlik, çalışma alanı birincil kullanıcı tarafından atanan yönetilen kimliğe göre aynı veya farklı olabilir.

Mevcut bir çalışma alanınız varsa, ```az ml workspace update``` CLI komutunu veya Python SDK yöntemini kullanarak Kullanıcı tarafından atanan yönetilen kimliğe sistem tarafından atanmış olarak güncelleştirebilirsiniz ```Workspace.update``` .


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Machine Learning 'de kurumsal güvenlik](concept-enterprise-security.md)hakkında daha fazla bilgi edinin.

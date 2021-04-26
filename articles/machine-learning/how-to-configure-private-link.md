---
title: Özel uç nokta yapılandırma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning çalışma alanınıza sanal bir ağdan güvenli bir şekilde erişmek için Azure özel bağlantısı 'nı kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 02/09/2021
ms.openlocfilehash: 5e13c97427736d60f300d2d7b502c6f3e15fb481
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861454"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Azure Machine Learning çalışma alanı için Azure özel bağlantısını yapılandırma

Bu belgede, Azure özel bağlantısının Azure Machine Learning çalışma alanıyla nasıl kullanılacağını öğrenirsiniz. Azure Machine Learning için sanal ağ oluşturma hakkında daha fazla bilgi için bkz. [sanal ağ yalıtımı ve gizliliğe genel bakış](how-to-network-security-overview.md)

Azure özel bağlantısı, özel bir uç nokta kullanarak çalışma alanınıza bağlanmanızı sağlar. Özel uç nokta, sanal ağınız içindeki özel IP adresleri kümesidir. Daha sonra çalışma alanınıza erişimi yalnızca özel IP adresleri üzerinden oluşacak şekilde sınırlayabilirsiniz. Özel bağlantı, veri taşması riskini azaltmaya yardımcı olur. Özel uç noktalar hakkında daha fazla bilgi için bkz. [Azure özel bağlantı](../private-link/private-link-overview.md) makalesi.

> [!IMPORTANT]
> Azure özel bağlantısı, çalışma alanını silme veya işlem kaynaklarını yönetme gibi Azure denetim düzlemi 'ni (yönetim işlemleri) etkilemez. Örneğin, bir işlem hedefi oluşturma, güncelleştirme veya silme. Bu işlemler, genel Internet üzerinden normal olarak gerçekleştirilir. Azure Machine Learning Studio, API 'Ler (yayınlanan işlem hatları dahil) veya SDK özel uç noktasını kullanır gibi veri düzlemi işlemleri.
>
> Mozilla Firefox kullanıyorsanız, çalışma alanınızın özel uç noktasına erişmeye çalışırken sorunlarla karşılaşabilirsiniz. Bu sorun, Mozilla 'de HTTPS üzerinden DNS ile ilişkili olabilir. Geçici çözüm olarak Microsoft Edge veya Google Chrome kullanmanızı öneririz.

## <a name="prerequisites"></a>Önkoşullar

* Müşteri tarafından yönetilen bir anahtarla özel bağlantı etkin bir çalışma alanı kullanmayı planlıyorsanız, bu özelliği bir destek bileti kullanarak istemeniz gerekir. Daha fazla bilgi için bkz. [kotaları yönetme ve artırma](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

* İçinde özel uç noktasını oluşturmak için var olan bir sanal ağınız olmalıdır. Özel uç nokta eklemeden önce [Özel uç noktalar için ağ ilkelerini de devre dışı bırakmanız](../private-link/disable-private-endpoint-network-policy.md) gerekir.
## <a name="limitations"></a>Sınırlamalar

* Azure Kamu bölgelerinde özel bağlantıyla bir Azure Machine Learning çalışma alanı kullanılması kullanılamaz.
* Özel bağlantıyla güvenli hale getirilmiş bir çalışma alanı için genel erişimi etkinleştirir ve ortak internet üzerinden Azure Machine Learning Studio 'yu kullanıyorsanız, tasarımcı gibi bazı özellikler verilerinize erişemez. Bu sorun, veriler VNet 'in arkasında güvenliği sağlanmış bir hizmette depolandığında oluşur. Örneğin, bir Azure depolama hesabı.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Özel uç nokta kullanan bir çalışma alanı oluşturma

Özel bir uç nokta ile çalışma alanı oluşturmak için aşağıdaki yöntemlerden birini kullanın. Bu yöntemlerin her biri, __var olan bir sanal ağ gerektirir__:

> [!TIP]
> Aynı anda bir çalışma alanı, Özel uç nokta ve sanal ağ oluşturmak isterseniz, [Azure Machine Learning için bir çalışma alanı oluşturmak üzere Azure Resource Manager şablonu kullanma](how-to-create-workspace-template.md)konusuna bakın.

# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK 'Sı, özel bir uç nokta ile bir çalışma alanı oluşturmak için [çalışma alanı. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) Ile kullanılabilen [privateendpointconfig](/python/api/azureml-core/azureml.core.privateendpointconfig) sınıfını sağlar. Bu sınıf, var olan bir sanal ağ gerektirir.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine Learning Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md) [az ml çalışma alanı oluştur](/cli/azure/ml/workspace#az_ml_workspace_create) komutunu sağlar. Bu komut için aşağıdaki parametreler, özel bir ağla çalışma alanı oluşturmak için kullanılabilir, ancak var olan bir sanal ağ gerektirir:

* `--pe-name`: Oluşturulan özel uç noktanın adı.
* `--pe-auto-approval`: Çalışma alanına özel uç nokta bağlantılarının otomatik olarak onaylanıp onaylanmayacağı.
* `--pe-resource-group`: İçinde özel uç nokta oluşturulacak kaynak grubu. Sanal ağı içeren aynı grup olmalıdır.
* `--pe-vnet-name`: İçinde özel uç nokta oluşturmak için var olan sanal ağ.
* `--pe-subnet-name`: İçinde özel uç nokta oluşturulacak alt ağın adı. `default` varsayılan değerdir.

Bu parametreler Create komutu için gerekli diğer parametrelere ek niteliğindedir. Örneğin, aşağıdaki komut, var olan bir kaynak grubunu ve VNet 'i kullanarak Batı ABD bölgesinde yeni bir çalışma alanı oluşturur:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure Machine Learning Studio 'daki __ağ__ sekmesi özel bir uç nokta yapılandırmanıza olanak tanır. Ancak, var olan bir sanal ağ gerektirir. Daha fazla bilgi için bkz. [portalda çalışma alanları oluşturma](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Çalışma alanına özel uç nokta ekleme

Mevcut bir çalışma alanına özel bir uç nokta eklemek için aşağıdaki yöntemlerden birini kullanın:

> [!WARNING]
>
> Bu çalışma alanıyla ilişkili mevcut bir işlem hedefi varsa ve bunlar aynı sanal ağ Tha 'nin arkasında değilse, Özel uç nokta içinde oluşturulur ve çalışmaz.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Bu örnekte kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi için bkz. [Privateendpointconfig](/python/api/azureml-core/azureml.core.privateendpointconfig) ve [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine Learning Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md) [az ml Workspace Private-Endpoint Add](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_add) komutunu sağlar.

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Portaldaki Azure Machine Learning çalışma alanından __Özel uç nokta bağlantıları__ ' nı seçin ve __+ Özel uç nokta__' ı seçin. Yeni bir özel uç nokta oluşturmak için alanlarını kullanın.

* __Bölgeyi__ seçerken, sanal ağınızla aynı bölgeyi seçin. 
* __Kaynak türünü__ seçerken __Microsoft. MachineLearningServices/çalışma alanlarını__ kullanın. 
* __Kaynağı__ çalışma alanınızın adı olarak ayarlayın.

Son olarak, Özel uç noktayı oluşturmak için __Oluştur__ ' u seçin.

---

## <a name="remove-a-private-endpoint"></a>Özel uç noktayı kaldırma

Bir çalışma alanından özel bir uç noktayı kaldırmak için aşağıdaki yöntemlerden birini kullanın:

# <a name="python"></a>[Python](#tab/python)

Özel bir uç noktayı kaldırmak için [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) kullanın.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine Learning Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md) [az ml Workspace Private-Endpoint Delete](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete) komutunu sağlar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Portaldaki Azure Machine Learning çalışma alanından __Özel uç nokta bağlantıları__' nı seçin ve ardından kaldırmak istediğiniz uç noktayı seçin. Son olarak __Kaldır__' ı seçin.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Özel bir uç nokta üzerinde çalışma alanı kullanma

Çalışma alanına yönelik iletişime yalnızca sanal ağdan izin verildiğinden, çalışma alanını kullanan tüm geliştirme ortamları sanal ağın üyesi olmalıdır. Örneğin, sanal ağdaki bir sanal makine.

> [!IMPORTANT]
> Microsoft, bağlantının geçici kesintisini önlemek için özel bağlantı etkinleştirildikten sonra çalışma alanına bağlanan makinelerde DNS önbelleğini temizlemeye öneriyor. 

Azure sanal makineleri hakkında daha fazla bilgi için bkz. [sanal makineler belgeleri](../virtual-machines/index.yml).

## <a name="enable-public-access"></a>Genel erişimi etkinleştir

Bazı durumlarda, birisinin VNet yerine ortak bir uç nokta üzerinden güvenli çalışma alanınıza bağlanmasına izin vermek isteyebilirsiniz. Çalışma alanını özel bir uç noktayla yapılandırdıktan sonra, isteğe bağlı olarak çalışma alanına genel erişimi etkinleştirebilirsiniz. Bunu yaptığınızda özel uç nokta kaldırılmaz. VNet 'in arkasındaki bileşenler arasındaki tüm iletişimler hala güvenli hale getirilir. VNet üzerinden özel erişime ek olarak yalnızca çalışma alanına genel erişim sağlar.

> [!WARNING]
> Genel uç nokta üzerinden bağlanılırken, bazı Studio özellikleri verilerinize erişemez. Bu sorun, veriler VNet 'in arkasında güvenliği sağlanmış bir hizmette depolandığında oluşur. Örneğin, bir Azure depolama hesabı. Ayrıca, Jupiter/Jupiterlab/RStudio işlevinin işlem örneği ' ni not edin ve çalışan Not defterleri çalışmaz.

Özel bir bağlantı etkin çalışma alanına genel erişimi etkinleştirmek için aşağıdaki adımları kullanın:

# <a name="python"></a>[Python](#tab/python)

Özel bir uç noktayı kaldırmak için [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) kullanın.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine Learning Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md) [az ml Workspace Update](/cli/azure/ml/workspace#az_ml_workspace_update) komutunu sağlar. Çalışma alanına genel erişimi etkinleştirmek için parametresini ekleyin `--allow-public-access true` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Şu anda portal kullanılarak bu işlevselliği etkinleştirmenin bir yolu yoktur.

---


## <a name="next-steps"></a>Sonraki adımlar

* Azure Machine Learning çalışma alanınızı güvenli hale getirme hakkında daha fazla bilgi için [sanal ağ yalıtımı ve gizliliği genel bakış](how-to-network-security-overview.md) makalesine bakın.

* Sanal ağınızda özel bir DNS çözümü kullanmayı planlıyorsanız, bkz. [Özel BIR DNS sunucusu ile çalışma alanı kullanma](how-to-custom-dns.md).

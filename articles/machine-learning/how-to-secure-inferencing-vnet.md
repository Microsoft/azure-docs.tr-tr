---
title: Sanal ağlarla güvenli ınırel sınırlama ortamları
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Inde bir Azure sanal ağını kullanarak, sanal ağ oluşturma ortamınızı güvenli hale getirin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 610ab82bfc4665fbb30aa3d3bc0448fa9338689c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872560"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Sanal ağlarla Azure Machine Learning ınvenli bir ortamın güvenliğini sağlama

Bu makalede, Azure Machine Learning ' de sanal ağ ile ınırel korumalı ortamların güvenliğini nasıl sağlayacağınızı öğreneceksiniz.

Bu makale, bir Azure Machine Learning iş akışını güvenli hale getirmek için size kılavuzluk eden beş bölümlü bir serinin dördüncü kısmıdır. İlk olarak birinci mimariyi anlamak için [tek bir bölüm (VNet genel bakış](how-to-network-security-overview.md) ) okumanız önemle tavsiye ederiz. 

Bu serideki diğer makalelere göz atın:

[1. VNET genel bakış](how-to-network-security-overview.md)  >  [çalışma alanı](how-to-secure-workspace-vnet.md)3 ' ü güvenceye alın  >  [. Eğitim ortamının](how-to-secure-training-vnet.md)4 ' ü koruyun  >  **. Invenli ortam** 5 ' i güvenli hale getirin  >  [. Studio işlevselliğini etkinleştir](how-to-enable-studio-virtual-network.md)

Bu makalede, bir sanal ağda aşağıdaki ınırm kaynaklarını güvenli hale getirme hakkında bilgi edineceksiniz:
> [!div class="checklist"]
> - Varsayılan Azure Kubernetes hizmeti (AKS) kümesi
> - Özel AKS kümesi
> - Özel bağlantısı olan AKS kümesi
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>Önkoşullar

+ Genel sanal ağ senaryolarını ve genel sanal ağ mimarisini anlamak için [ağ güvenliğine genel bakış](how-to-network-security-overview.md) makalesini okuyun.

+ İşlem kaynaklarınızla kullanılacak mevcut bir sanal ağ ve alt ağ.

+ Bir sanal ağa veya alt ağa kaynak dağıtmak için, Kullanıcı hesabınızın Azure rol tabanlı erişim denetimi 'nde (Azure RBAC) aşağıdaki eylemler için izinleri olmalıdır:

    - Sanal ağ kaynağında "Microsoft. Network/virtualNetworks/JOIN/Action".
    - Alt ağ kaynağında "Microsoft. Network/virtualNetworks/subnet/JOIN/Action".

    Ağ ile Azure RBAC hakkında daha fazla bilgi için bkz. [ağ yerleşik rolleri](../role-based-access-control/built-in-roles.md#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Bir sanal ağda AKS kümesi kullanmak için aşağıdaki ağ gereksinimlerinin karşılanması gerekir:

> [!div class="checklist"]
> * [Azure Kubernetes hizmeti 'nde (AKS) Gelişmiş ağ yapılandırma](../aks/configure-azure-cni.md#prerequisites)' daki önkoşulları izleyin.
> * AKS örneği ve sanal ağ aynı bölgede olmalıdır. Bir sanal ağ içinde çalışma alanı tarafından kullanılan Azure depolama hesaplarını güvenli hale getirmek istiyorsanız, AKS örneğiyle aynı sanal ağda olmaları gerekir.

Bir sanal ağdaki AKS 'leri çalışma alanınıza eklemek için aşağıdaki adımları kullanın:

1. [Azure Machine Learning Studio](https://ml.azure.com/)' da oturum açın ve ardından aboneliğinizi ve çalışma alanınızı seçin.

1. Sol tarafta __işlem__ ' ı seçin.

1. Merkezden __çıkarım kümeleri__ ' ni seçin ve ardından öğesini seçin __+__ .

1. __Yeni çıkarım kümesi__ Iletişim kutusunda __ağ yapılandırması__ altında __Gelişmiş__ ' i seçin.

1. Bu işlem kaynağını bir sanal ağ kullanacak şekilde yapılandırmak için aşağıdaki eylemleri gerçekleştirin:

    1. __Kaynak grubu__ açılır listesinde, sanal ağı içeren kaynak grubunu seçin.
    1. __Sanal ağ__ açılan listesinde, alt ağı içeren sanal ağı seçin.
    1. __Alt ağ__ açılan listesinde, alt ağı seçin.
    1. __Kubernetes hizmeti adres aralığı__ kutusuna Kubernetes hizmeti adres aralığını girin. Bu adres aralığı, küme için kullanılabilir IP adreslerini tanımlamak üzere sınıfsız Inter-Domain yönlendirme (CıDR) gösterimi IP aralığını kullanır. Herhangi bir alt ağ IP aralığı ile çakışmamalıdır (örneğin, 10.0.0.0/16).
    1. __KUBERNETES DNS HIZMETI IP adresi__ kutusuna Kubernetes DNS hizmeti IP adresini girin. Bu IP adresi, Kubernetes DNS hizmetine atanır. Bu, Kubernetes hizmeti adres aralığı içinde olmalıdır (örneğin, 10.0.0.10).
    1. __Docker köprü adresi__ kutusuna Docker köprü adresini girin. Bu IP adresi Docker köprüsüne atandı. Herhangi bir alt ağ IP aralığında olmamalı veya Kubernetes hizmeti adres aralığı (örneğin, 172.17.0.1/16) olmalıdır.

   ![Azure Machine Learning: Machine Learning İşlem sanal ağ ayarları](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Bir modeli bir Web hizmeti olarak AKS 'e dağıttığınızda, ikinci dereceden sınırlama isteklerini işlemek için bir Puanlama uç noktası oluşturulur. Sanal ağı denetleyen NSG grubunun, sanal ağın dışından çağırmak istiyorsanız, Puanlama uç noktasının IP adresi için etkinleştirilmiş bir gelen güvenlik kuralına sahip olduğundan emin olun.

    Puanlama uç noktasının IP adresini bulmak için, dağıtılan hizmetin Puanlama URI 'sine bakın. Puanlama URI 'sini görüntüleme hakkında daha fazla bilgi için bkz. [Web hizmeti olarak dağıtılan bir modeli](how-to-consume-web-service.md#connection-information)kullanma.

   > [!IMPORTANT]
   > NSG için varsayılan giden kuralları saklayın. Daha fazla bilgi için bkz. [güvenlik gruplarında](../virtual-network/network-security-groups-overview.md#default-security-rules)varsayılan güvenlik kuralları.

   [![Bir gelen güvenlik kuralı](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > Puanlama uç noktası için görüntüde gösterilen IP adresi dağıtımlarınız için farklı olacaktır. Aynı IP bir AKS kümesine yönelik tüm dağıtımlar tarafından paylaşıldığında, her bir AKS kümesi farklı bir IP adresine sahip olur.

Azure Kubernetes hizmetini bir sanal ağa eklemek için Azure Machine Learning SDK 'sını de kullanabilirsiniz. Bir sanal ağda zaten bir AKS kümeniz varsa, bunları [aks 'e dağıtma](how-to-deploy-and-where.md)bölümünde açıklandığı gibi çalışma alanına ekleyin. Aşağıdaki kod, `default` adlı bir sanal ağın alt ağında yeni BIR AKS örneği oluşturur `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Oluşturma işlemi tamamlandığında, bir sanal ağın arkasındaki AKS kümesinde çıkarım veya model Puanlama çalıştırabilirsiniz. Daha fazla bilgi için bkz. [AKS 'e dağıtma](how-to-deploy-and-where.md).

Kubernetes ile Role-Based Access Control kullanma hakkında daha fazla bilgi için bkz. [Kubernetes yetkilendirmesi Için Azure RBAC kullanma](../aks/manage-azure-rbac.md).

## <a name="network-contributor-role"></a>Ağ katılımcısı rolü

> [!IMPORTANT]
> Daha önce oluşturduğunuz bir sanal ağ sağlayarak bir AKS kümesi oluşturur veya iliştirdiyseniz, AKS kümeniz için hizmet sorumlusu (SP) veya yönetilen kimliğe, sanal ağı içeren kaynak grubuna _ağ katılımcısı_ rolü vermelisiniz.
>
> Kimliği ağ katılımcısı olarak eklemek için aşağıdaki adımları kullanın:

1. AKS için hizmet sorumlusu veya yönetilen kimlik KIMLIĞINI bulmak için aşağıdaki Azure CLı komutlarını kullanın. `<aks-cluster-name>`Kümenin adıyla değiştirin. `<resource-group-name>` _, Aks kümesini içeren_ kaynak grubunun adıyla değiştirin:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Bu komut değerini döndürürse `msi` , yönetilen kimliğin asıl kimliğini tanımlamak için aşağıdaki komutu kullanın:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Sanal ağınızı içeren kaynak grubunun KIMLIĞINI bulmak için aşağıdaki komutu kullanın. `<resource-group-name>` _Sanal ağı içeren_ kaynak grubunun adıyla değiştirin:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Hizmet sorumlusu veya yönetilen kimliği bir ağ katılımcısı olarak eklemek için aşağıdaki komutu kullanın. `<SP-or-managed-identity>`Hizmet sorumlusu veya yönetilen kimlik için döndürülen kimlikle değiştirin. `<resource-group-id>`Sanal ağı içeren kaynak grubu için döndürülen kimlikle değiştirin:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
AKS ile iç yük dengeleyiciyi kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmeti ile iç yük dengeleyici kullanma](../aks/internal-lb.md).

## <a name="secure-vnet-traffic"></a>Güvenli VNet trafiği

AKS kümesinden ve sanal ağa giden trafiği yalıtmak için iki yaklaşım vardır:

* __Özel AKS kümesi__: Bu yaklaşım, dağıtım/yönetim işlemleri için kümeyle iletişimin güvenliğini sağlamak üzere Azure özel bağlantısını kullanır.
* __Iç aks yük dengeleyici__: Bu yaklaşım, sanal ağ içinde özel bir IP kullanmak üzere dağıtımlarınız için uç noktayı yapılandırır.

> [!WARNING]
> İç yük dengeleyici, kubenet kullanan bir AKS kümesiyle çalışmaz. Aynı anda bir iç yük dengeleyici ve özel bir AKS kümesi kullanmak istiyorsanız, özel AKS kümenizi Azure Container Networking Interface (CNı) ile yapılandırın. Daha fazla bilgi için bkz. Azure [Kubernetes hizmetinde Azure CNI ağını yapılandırma](../aks/configure-azure-cni.md).

### <a name="private-aks-cluster"></a>Özel AKS kümesi

Varsayılan olarak, AKS kümelerinin, genel IP adreslerine sahip bir denetim düzlemi veya API sunucusu vardır. Özel bir AKS kümesi oluşturarak, AKS 'i özel denetim düzlemi kullanacak şekilde yapılandırabilirsiniz. Daha fazla bilgi için bkz. [özel Azure Kubernetes hizmet kümesi oluşturma](../aks/private-clusters.md).

Özel AKS kümesi oluşturduktan sonra, Azure Machine Learning ile kullanmak üzere [kümeyi sanal ağa bağlayın](how-to-create-attach-kubernetes.md) .

> [!IMPORTANT]
> Azure Machine Learning ile özel bağlantı etkin bir AKS kümesi kullanmadan önce, bu işlevi etkinleştirmek için bir destek olayı açmanız gerekir. Daha fazla bilgi için bkz. [kotaları yönetme ve artırma](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

### <a name="internal-aks-load-balancer"></a>İç AKS yük dengeleyici

Varsayılan olarak, AKS dağıtımları [ortak yük dengeleyici](../aks/load-balancer-standard.md)kullanır. Bu bölümde, AKS 'in iç yük dengeleyiciyi kullanmak üzere nasıl yapılandırılacağını öğreneceksiniz. Ön uç olarak yalnızca özel IP 'Lere izin verilen bir iç (veya özel) yük dengeleyici kullanılır. İç yük dengeleyiciler, bir sanal ağ içindeki trafiğin yükünü dengelemek için kullanılır

Bir özel yük dengeleyici, AKS 'in _iç yük dengeleyiciyi_ kullanacak şekilde yapılandırılması ile etkinleştirilir. 

#### <a name="enable-private-load-balancer"></a>Özel yük dengeleyiciyi etkinleştir

> [!IMPORTANT]
> Azure Machine Learning Studio 'da Azure Kubernetes hizmet kümesini oluştururken özel IP 'yi etkinleştiremezsiniz. Machine Learning için Python SDK veya Azure CLı uzantısı kullanırken iç yük dengeleyiciye sahip bir tane oluşturabilirsiniz.

Aşağıdaki örneklerde, SDK ve CLı kullanarak __Özel BIR IP/iç yük dengeleyici ile yeni BIR AKS kümesinin nasıl oluşturulacağı__ gösterilmektedir:

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

> [!IMPORTANT]
> CLı kullanarak yalnızca iç yük dengeleyiciye sahip bir AKS kümesi oluşturabilirsiniz. Mevcut bir kümeyi iç yük dengeleyiciyi kullanacak şekilde yükseltmek için az ml komutu yoktur.

Daha fazla bilgi için, [az ml computetarget Create aks](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_aks) Reference bölümüne bakın.

---

__Var olan bir kümeyi çalışma alanınıza iliştirirken__ , yük dengeleyiciyi yapılandırmak için iliştirme işleminden sonra beklemeniz gerekir. Bir küme ekleme hakkında bilgi için bkz. [var olan AKS kümesi ekleme](how-to-create-attach-kubernetes.md).

Mevcut kümeyi iliştirdikten sonra, bir iç yük dengeleyici/özel IP kullanmak için kümeyi güncelleştirebilirsiniz:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Azure Container Instances etkinleştir (ACI)

Azure Container Instances, bir model dağıtıldığında dinamik olarak oluşturulur. Azure Machine Learning sanal ağ içinde ACI oluşturmak üzere etkinleştirmek için, dağıtım tarafından kullanılan alt ağ için __alt ağ temsilcisini__ etkinleştirmeniz gerekir.

> [!WARNING]
> Bir sanal ağda Azure Container Instances kullanırken, sanal ağın şu olması gerekir:
> * Azure Machine Learning çalışma alanınız ile aynı kaynak grubunda.
> * Çalışma alanınızın __özel bir uç noktası__ varsa Azure Container Instances için kullanılan sanal ağ, çalışma alanı özel uç noktası tarafından kullanılan bir ile aynı olmalıdır.
>
> Sanal ağ içinde Azure Container Instances kullanırken, çalışma alanınızın Azure Container Registry (ACR) Sanal ağda olamaz.

Çalışma alanınıza bir sanal ağda ACI 'yi kullanmak için aşağıdaki adımları kullanın:

1. Sanal ağınızda alt ağ temsilcisini etkinleştirmek için [alt ağ temsili ekleme veya kaldırma](../virtual-network/manage-subnet-delegation.md) makalesindeki bilgileri kullanın. Bir sanal ağ oluştururken temsilciyi etkinleştirebilir veya var olan bir ağa ekleyebilirsiniz.

    > [!IMPORTANT]
    > Temsilci seçme etkinleştirildiğinde, `Microsoft.ContainerInstance/containerGroups` __hizmet verme için alt ağ__ olarak kullanın.

2. [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)kullanarak modeli dağıtın, `vnet_name` ve `subnet_name` parametrelerini kullanın. Bu parametreleri, temsilciyi etkinleştirdiğiniz sanal ağ adı ve alt ağa ayarlayın.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>Sanal ağdan giden bağlantıyı sınırlama

Varsayılan giden kurallarını kullanmak istemiyorsanız ve sanal ağınızın giden erişimini sınırlandırmak istiyorsanız, Azure Container Registry erişimine izin vermeniz gerekir. Örneğin, ağ güvenlik gruplarınızı (NSG), ' {RegionName} öğesinin bir Azure bölgesinin adı olduğu __AzureContainerRegistry. RegionName__ hizmet etiketine erişime izin veren bir kural içerdiğinden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, beş bölümlü bir sanal ağ serisinin dördüncü kısmıdır. Bir sanal ağın güvenliğini nasıl sağlayacağınızı öğrenmek için makalelerin geri kalanına bakın:

* [1. kısım: sanal ağa genel bakış](how-to-network-security-overview.md)
* [2. Bölüm: çalışma alanı kaynaklarını güvenli hale getirme](how-to-secure-workspace-vnet.md)
* [3. kısım: eğitim ortamının güvenliğini sağlama](how-to-secure-training-vnet.md)
* [5. Bölüm: Studio işlevselliğini etkinleştirme](how-to-enable-studio-virtual-network.md)

Ayrıca, ad çözümlemesi için [özel DNS](how-to-custom-dns.md) kullanma makalesine bakın.
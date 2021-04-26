---
title: Azure CLı kullanarak sanal düğümler oluşturma
titleSuffix: Azure Kubernetes Service
description: Pods 'leri çalıştırmak için sanal düğümleri kullanan bir Azure Kubernetes hizmeti (AKS) kümesi oluşturmak üzere Azure CLı 'yı nasıl kullanacağınızı öğrenin.
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 86f1d8923eea961471883c44168c4fa848ac12d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769292"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Azure CLı kullanarak sanal düğümleri kullanmak için bir Azure Kubernetes hizmeti (AKS) kümesi oluşturma ve yapılandırma

Bu makalede, Azure CLı kullanarak sanal ağ kaynakları ve AKS kümesi oluşturup yapılandırma ve ardından sanal düğümleri etkinleştirme işlemlerinin nasıl yapılacağı gösterilir.


## <a name="before-you-begin"></a>Başlamadan önce

Sanal düğümler Azure Container Instances (aci) ve aks kümesinde çalışan Pod 'ler arasında ağ iletişimini sağlar. Bu iletişim sağlamak için bir sanal ağ alt ağı oluşturulur ve temsilci izinleri atanır. Sanal düğümler yalnızca *Gelişmiş* ağ (Azure CNI) kullanılarak oluşturulan aks kümeleriyle çalışır. Varsayılan olarak, AKS kümeleri *temel* ağ (kubenet) ile oluşturulur. Bu makalede, bir sanal ağ ve alt ağ oluşturma ve ardından Gelişmiş ağ kullanan bir AKS kümesi dağıtma işlemleri gösterilir.

> [!IMPORTANT]
> AKS ile sanal düğümleri kullanmadan önce, [AKS sanal düğümlerinin sınırlamalarını][virtual-nodes-aks] ve [acı 'nin sanal ağ sınırlamalarını][virtual-nodes-networking-aci]gözden geçirin. Bu sınırlamalar, hem AKS kümenizin hem de sanal düğümlerin konumunu, ağ yapılandırmasını ve diğer yapılandırma ayrıntılarını etkiler.

Daha önce ACI kullandıysanız, hizmet sağlayıcısını aboneliğiniz ile kaydedin. Aşağıdaki örnekte gösterildiği gibi [az Provider List][az-provider-list] komutunu kullanarak aci sağlayıcı kaydının durumunu denetleyebilirsiniz:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Aşağıdaki örnek çıktıda gösterildiği gibi, *Microsoft. Containerınstance* sağlayıcısı *kayıtlı* olarak rapor etmelidir:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Sağlayıcı *Notregistered* olarak gösteriyorsa, aşağıdaki örnekte gösterildiği gibi [az Provider Register][az-provider-register] kullanarak sağlayıcıyı kaydedin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır.

Cloud Shell açmak için, bir kod bloğunun sağ üst köşesinden **dene** ' yi seçin. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/bash](https://shell.azure.com/bash) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure CLı sürüm 2.0.49 veya üstünü gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği mantıksal bir gruptur. [az group create][az-group-create] komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek *westus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[Az Network VNET Create][az-network-vnet-create] komutunu kullanarak bir sanal ağ oluşturun. Aşağıdaki örnek, *10.0.0.0/8* adres ön *ekine sahip bir* sanal ağ adı ve *myakssubnet* adlı bir alt ağ oluşturur. Bu alt ağın adres ön eki varsayılan olarak *10.240.0.0/16*' dır:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Şimdi [az Network VNET subnet Create][az-network-vnet-subnet-create] komutunu kullanarak sanal düğümler için ek bir alt ağ oluşturun. Aşağıdaki örnek, *10.241.0.0/16* adres ön ekine sahip *myvirtualnodesubnet* adlı bir alt ağ oluşturur.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Hizmet sorumlusu oluşturma veya yönetilen kimlik kullanma

Bir AKS kümesinin diğer Azure kaynaklarıyla etkileşime geçmesini sağlamak için bir küme kimliği kullanılır. Bu küme kimliği Azure CLı veya portalı tarafından otomatik olarak oluşturulabilir veya bir tane önceden oluşturup daha fazla izin atayabilirsiniz. Varsayılan olarak, bu küme kimliği yönetilen bir kimliktir. Daha fazla bilgi için bkz. [yönetilen kimlikleri kullanma](use-managed-identity.md). Ayrıca, bir hizmet sorumlusunu küme kimliğiniz olarak kullanabilirsiniz. Aşağıdaki adımlarda hizmet sorumlusunu kümenize el ile oluşturma ve atama işlemleri gösterilmektedir.

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] komutunu kullanarak bir hizmet sorumlusu oluşturun. `--skip-assignment` parametresi, ek izinlerin atanmasını engeller.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Çıktı aşağıdaki örneğe benzer:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

*appId* ve *password* değerlerini not edin. Bu değerler aşağıdaki adımlarda kullanılacaktır.

## <a name="assign-permissions-to-the-virtual-network"></a>Sanal ağa izin atama

Kümenizin sanal ağı kullanmasına ve yönetmesine izin vermek için, AKS hizmet sorumlusuna ağ kaynaklarını kullanmak için doğru haklara sahip olmanız gerekir.

İlk olarak, [az Network VNET Show][az-network-vnet-show]kullanarak sanal ağ kaynak kimliğini alın:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Sanal ağı kullanmak için AKS kümesine doğru erişimi vermek üzere [az role atama Create][az-role-assignment-create] komutunu kullanarak bir rol ataması oluşturun. `<appId`> ve `<vnetId>` yerine önceki iki adımda topladığınız değerleri yazın.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

Bir AKS kümesini önceki bir adımda oluşturulan AKS alt ağına dağıtırsınız. [Az Network VNET subnet Show][az-network-vnet-subnet-show]kullanarak bu alt ağın kimliğini alın:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

AKS kümesi oluşturmak için [az aks create][az-aks-create] komutunu kullanın. Aşağıdaki örnekte, bir düğüm ile *myAKSCluster* adlı bir küme oluşturulmuştur. `<subnetId>`Önceki adımda elde EDILEN kimlikle ve daha sonra `<appId>` ve `<password>` önceki bölümde toplanan değerlerle değiştirin.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Birkaç dakika sonra komut tamamlanır ve küme hakkında JSON tarafından biçimlendirilmiş bilgiler gösterilir.

## <a name="enable-virtual-nodes-addon"></a>Sanal düğümlerin eklentisini etkinleştir

Sanal düğümleri etkinleştirmek için şimdi [az aks Enable-addons][az-aks-enable-addons] komutunu kullanın. Aşağıdaki örnek, önceki adımda oluşturulan *Myvirtualnodesubnet* adlı alt ağı kullanır:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

`kubectl` istemcisini Kubernetes kümenize bağlanacak şekilde yapılandırmak için [az aks get-credentials][az-aks-get-credentials] komutunu kullanın. Bu adım kimlik bilgilerini indirir ve Kubernetes CLI’yi bunları kullanacak şekilde yapılandırır.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Kümenize bağlantıyı doğrulamak için [kubectl get][kubectl-get] komutunu kullanarak küme düğümleri listesini alın.

```console
kubectl get nodes
```

Aşağıdaki örnek çıktıda, oluşturulan tek VM düğümü ve Linux için sanal düğüm, *sanal düğüm-aci-Linux* sanal düğümü gösterilmektedir:

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Örnek uygulama dağıtma

`virtual-node.yaml`Aşağıdaki YAML 'de adlı bir dosya oluşturun ve kopyalayın. Düğüm üzerinde kapsayıcıyı zamanlamak için bir [Nodeselector][node-selector] ve [toleranation][toleration] tanımlanmıştır.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Uygulamayı [kubectl Apply][kubectl-apply] komutuyla çalıştırın.

```console
kubectl apply -f virtual-node.yaml
```

[Kubectl Get Pod][kubectl-get] komutunu bağımsız değişkenle birlikte kullanarak `-o wide` bir pod ve zamanlanan düğüm listesini çıkış. `aci-helloworld`Pod 'ın düğüm üzerinde zamanlandığına dikkat edin `virtual-node-aci-linux` .

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod 'a sanal düğümlerle kullanılmak üzere atanan Azure sanal ağ alt ağından bir iç IP adresi atanır.

> [!NOTE]
> Azure Container Registry depolanan görüntüleri kullanıyorsanız, [bir Kubernetes gizli anahtarını yapılandırın ve kullanın][acr-aks-secrets]. Sanal düğümlerin geçerli sınırlaması, tümleşik Azure AD hizmet sorumlusu kimlik doğrulamasını kullanamıyoruz. Gizli anahtar kullanmıyorsanız, sanal düğümlerde zamanlanan Pod 'ler başlatılamaz ve hatayı bildirebilir `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Sanal düğüm Pod 'u test etme

Sanal düğümde çalışan Pod 'u test etmek için, bir web istemcisiyle tanıtım uygulamasına gidin. Pod 'a bir iç IP adresi atandığında, bu bağlantıyı AKS kümesindeki başka bir pod 'tan hızlıca test edebilirsiniz. Bir test Pod oluşturun ve buna bir terminal oturumu ekleyin:

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Şunu `curl` kullanarak Pod 'a yüklensin `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Artık, kullanarak Pod 'nizin adresine erişin `curl` *http://10.241.0.4* . Önceki komutta gösterilen kendi iç IP adresini belirtin `kubectl get pods` :

```console
curl -L http://10.241.0.4
```

Demo uygulaması aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi görüntülenir:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

İle test Pod 'unuzla Terminal oturumunu kapatın `exit` . Oturumunuz sona erdikten sonra Pod silinir.

## <a name="remove-virtual-nodes"></a>Sanal düğümleri kaldır

Artık sanal düğümleri kullanmak istemiyorsanız, [az aks Disable-addons][az aks disable-addons] komutunu kullanarak bunları devre dışı bırakabilirsiniz. 

Gerekirse, [https://shell.azure.com](https://shell.azure.com) tarayıcınızda Azure Cloud Shell açmak için bölümüne gidin.

İlk olarak, `aci-helloworld` sanal düğümde çalışan Pod 'yi silin:

```console
kubectl delete -f virtual-node.yaml
```

Aşağıdaki örnek komut Linux sanal düğümlerini devre dışı bırakır:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Şimdi, sanal ağ kaynaklarını ve kaynak grubunu kaldırın:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, sanal düğümde bir pod zamanlandı ve özel, dahili bir IP adresi atandı. Bunun yerine, bir yük dengeleyici veya giriş denetleyicisi aracılığıyla bir hizmet dağıtımı oluşturup Pod 'inize trafik yönlendirebilirsiniz. Daha fazla bilgi için bkz. [AKS 'de temel giriş denetleyicisi oluşturma][aks-basic-ingress].

Sanal düğümler genellikle AKS 'deki bir ölçeklendirme çözümünün bir bileşenidir. Çözümleri ölçeklendirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes yatay Pod otomatik Scaler 'ı kullanma][aks-hpa]
- [Kubernetes kümesi otomatik Scaler 'ı kullanma][aks-cluster-autoscaler]
- [Sanal düğümler için otomatik ölçeklendirme örneğine göz atın][virtual-node-autoscale]
- [Sanal Kubelet açık kaynak kitaplığı hakkında daha fazla bilgi edinin][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks disable-addons]: /cli/azure/aks#az_aks_disable_addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az_provider_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md
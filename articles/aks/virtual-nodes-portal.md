---
title: Azure Kubernetes hizmetlerinde (AKS) portalı kullanarak sanal düğümler oluşturma
description: Bir Azure Kubernetes hizmeti (AKS) kümesi oluşturmak için Azure portal kullanarak pods 'yi çalıştırmak için sanal düğümleri kullanan bir Azure
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: ab0aebf0b66ac01e19699795b14063df31cb9621
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263767"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Azure portal sanal düğümleri kullanmak için bir Azure Kubernetes hizmeti (AKS) kümesi oluşturma ve yapılandırma

Azure Kubernetes Service (AKS) kümesinde iş yüklerini hızlıca dağıtmak için sanal düğümleri kullanabilirsiniz. Sanal düğümlerde, yığınların hızlı bir şekilde sağlanması ve yürütme süresi boyunca yalnızca saniye başına ödeme yaparsınız. Bir ölçeklendirme senaryosunda, Kubernetes kümesi otomatik Scaler ' ı ek düğüm işlemlerini çalıştırmak için VM işlem düğümlerini dağıtmaya beklemeniz gerekmez. Sanal düğümler yalnızca Linux Pod ve düğümleri ile desteklenir.

Bu makalede, sanal düğümlerin etkin olduğu sanal ağ kaynaklarının ve AKS kümesinin nasıl oluşturulacağı ve yapılandırılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Sanal düğümler, aci ve aks kümesinde çalışan Pod 'ler arasındaki ağ iletişimini sağlar. Bu iletişim sağlamak için bir sanal ağ alt ağı oluşturulur ve temsilci izinleri atanır. Sanal düğümler yalnızca *Gelişmiş* ağ kullanılarak oluşturulan aks kümeleriyle çalışır. Varsayılan olarak, AKS kümeleri *temel* ağ ile oluşturulur. Bu makalede, bir sanal ağ ve alt ağ oluşturma ve ardından Gelişmiş ağ kullanan bir AKS kümesi dağıtma işlemleri gösterilir.

Daha önce ACI kullandıysanız, hizmet sağlayıcısını aboneliğiniz ile kaydedin. Aşağıdaki örnekte gösterildiği gibi [az Provider List][az-provider-list] komutunu kullanarak aci sağlayıcı kaydının durumunu denetleyebilirsiniz:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Aşağıdaki örnek çıktıda gösterildiği gibi, *Microsoft. Containerınstance* sağlayıcısı *kayıtlı*olarak rapor etmelidir:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Sağlayıcı *Notregistered*olarak gösteriyorsa, aşağıdaki örnekte gösterildiği gibi [az Provider Register] [az-Provider-Register] öğesini kullanarak sağlayıcıyı kaydedin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Sanal düğüm dağıtımları için aşağıdaki bölgeler desteklenir:

* Avustralya Doğu (australiaeast)
* Orta ABD (merkezde ABD)
* Doğu ABD (eastus)
* Doğu ABD 2 (eastus2)
* Japonya Doğu (japaneast)
* Kuzey Avrupa (northeurope)
* Güneydoğu Asya (Güneydoğu)
* Orta Batı ABD (westcentralus)
* Batı Avrupa (westeurope)
* Batı ABD (westus)
* Batı ABD 2 (westus2)

## <a name="known-limitations"></a>Bilinen sınırlamalar
Sanal düğümler işlevselliği, ACI 'nin özellik kümesine yoğun bir şekilde bağımlıdır. Aşağıdaki senaryolar henüz sanal düğümlerde desteklenmiyor

* ACR görüntülerini çekmek için hizmet sorumlusu kullanma. [Geçici çözüm](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) , [Kubernetes gizli](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line) dizileri kullanmaktır
* VNet eşlemesi, Kubernetes ağ ilkeleri ve ağ güvenlik gruplarıyla internet 'e giden trafik dahil [sanal ağ sınırlamaları](../container-instances/container-instances-vnet.md) .
* Init kapsayıcıları
* [Ana bilgisayar diğer adları](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* ACI 'da exec için [bağımsız değişkenler](../container-instances/container-instances-exec.md#restrictions)
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) , sanal düğüme Pod dağıtmayacak
* [Windows Server düğümleri (Şu anda AKS 'de önizlemededir)](windows-container-cli.md) sanal düğümlerde desteklenmez. Sanal düğümleri, bir AKS kümesinde Windows Server düğümlerine gerek kalmadan Windows Server kapsayıcıları zamanlamak için kullanabilirsiniz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

Azure portalının sol üst köşesinde bulunan **Create a resource** (Kaynak oluştur) > **Kubernetes Service** (Kubernetes Hizmeti) öğesini seçin.

**Temel bilgiler** sayfasında, aşağıdaki seçenekleri yapılandırın:

- *PROJE AYRINTILARI*: Bir Azure aboneliği seçtikten sonra bir Azure kaynak grubu seçin veya *myResourceGroup* adıyla yeni bir tane oluşturun. **Kubernetes kümesi adı** alanına *myAKSCluster* gibi bir ad girin.
- *KÜME AYRINTILARI*: AKS kümesi için bölge, Kubernetes sürümü ve DNS adı ön eki seçin.
- *BIRINCIL düğüm havuzu*: aks düğümleri IÇIN bir VM boyutu seçin. AKS kümesi dağıtıldıktan sonra, sanal makine boyutu **değiştirilemez**.
     - Kümeye dağıtılacak düğüm sayısını seçin. Bu makalede, **düğüm sayısını** *1*olarak ayarlayın. Küme dağıtıldıktan sonra düğüm sayısı **ayarlanabilir**.

**İleri: ölçek**öğesine tıklayın.

**Ölçek** sayfasında, **sanal düğümler**altında *etkin* ' i seçin.

![AKS kümesi oluşturma ve sanal düğümleri etkinleştirme](media/virtual-nodes-portal/enable-virtual-nodes.png)

Varsayılan olarak, bir Azure Active Directory hizmet sorumlusu oluşturulur. Bu hizmet sorumlusu, küme iletişimi ve diğer Azure hizmetleriyle tümleştirme için kullanılır.

Küme, Gelişmiş ağ için de yapılandırılır. Sanal düğümler kendi Azure sanal ağ alt ağını kullanacak şekilde yapılandırılır. Bu alt ağ, AKS kümesi arasında Azure kaynaklarını bağlama izinlerine temsilci seçti. Henüz temsilci alt ağınız yoksa, Azure portal Azure sanal ağını ve alt ağını sanal düğümlerle kullanılmak üzere oluşturur ve yapılandırır.

**İncele ve oluştur**’u seçin. Doğrulama tamamlandıktan sonra **Oluştur**' u seçin.

AKS kümesinin oluşturulması ve kullanıma hazır olması birkaç dakika sürer.

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bir Kubernetes kümesini yönetmek için Kubernetes komut satırı istemcisi [kubectl][kubectl]’i kullanın. `kubectl` istemcisi Azure Cloud Shell’de önceden yüklüdür.

Cloud Shell açmak için, bir kod bloğunun sağ üst köşesinden **dene** ' yi seçin. İsterseniz [https://shell.azure.com/bash](https://shell.azure.com/bash) adresine giderek Cloud Shell'i ayrı bir tarayıcı sekmesinde de başlatabilirsiniz. **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

Kubernetes kümenize bağlanmak için `kubectl` ' i yapılandırmak için [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanın. Aşağıdaki örnek *myResourceGroup* adlı kaynak grubu içindeki *myAKSCluster* adlı kümenin kimlik bilgilerini alır:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Kümenize bağlantıyı doğrulamak için [kubectl get][kubectl-get] komutunu kullanarak küme düğümleri listesini alın.

```azurecli-interactive
kubectl get nodes
```

Aşağıdaki örnek çıktıda, oluşturulan tek VM düğümü ve Linux için sanal düğüm, *sanal düğüm-aci-Linux*sanal düğümü gösterilmektedir:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Örnek uygulama dağıtma

Azure Cloud Shell, `virtual-node.yaml` adlı bir dosya oluşturun ve aşağıdaki YAML 'ye kopyalayın. Düğüm üzerinde kapsayıcıyı zamanlamak için bir [Nodeselector][node-selector] ve [toleranation][toleration] tanımlanmıştır. Bu ayarlar, Pod 'ın sanal düğümde zamanlanmasını sağlar ve özelliğin başarıyla etkinleştirildiğini doğrulayın.

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
        image: microsoft/aci-helloworld
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

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

[Kubectl Get Pod][kubectl-get] komutunu `-o wide` bağımsız değişkeniyle birlikte kullanarak Pod ve zamanlanan düğüm listesini alın. @No__t-0 Pod `virtual-node-linux` düğümünde zamanlandığına dikkat edin.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod 'a sanal düğümlerle kullanılmak üzere atanan Azure sanal ağ alt ağından bir iç IP adresi atanır.

> [!NOTE]
> Azure Container Registry depolanan görüntüleri kullanıyorsanız, [bir Kubernetes gizli anahtarını yapılandırın ve kullanın][acr-aks-secrets]. Sanal düğümlerin geçerli sınırlaması, tümleşik Azure AD hizmet sorumlusu kimlik doğrulamasını kullanamıyoruz. Gizli anahtar kullanmıyorsanız, sanal düğümlerde zamanlanan düğüm başlatılamaz ve `HTTP response status code 400 error code "InaccessibleImage"` hatası rapor edebilir.

## <a name="test-the-virtual-node-pod"></a>Sanal düğüm Pod 'u test etme

Sanal düğümde çalışan Pod 'u test etmek için, bir web istemcisiyle tanıtım uygulamasına gidin. Pod 'a bir iç IP adresi atandığında, bu bağlantıyı AKS kümesindeki başka bir pod 'tan hızlıca test edebilirsiniz. Bir test Pod oluşturun ve buna bir terminal oturumu ekleyin:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

@No__t-1 kullanarak Pod 'a `curl` ' yı yükler:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Artık *http://10.241.0.4* gibi `curl` kullanarak Pod 'nizin adresine erişin. Önceki `kubectl get pods` komutunda gösterilen kendi iç IP adresini belirtin:

```azurecli-interactive
curl -L http://10.241.0.4
```

Demo uygulaması aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi görüntülenir:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

@No__t-0 ile test Pod 'unuzla Terminal oturumunu kapatın. Oturumunuz sona erdikten sonra Pod silinir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, sanal düğümde bir pod zamanlandı ve özel, dahili bir IP adresi atandı. Bunun yerine, bir yük dengeleyici veya giriş denetleyicisi aracılığıyla bir hizmet dağıtımı oluşturup Pod 'inize trafik yönlendirebilirsiniz. Daha fazla bilgi için bkz. [AKS 'de temel giriş denetleyicisi oluşturma][aks-basic-ingress].

Sanal düğümler, AKS 'teki bir ölçeklendirme çözümünün bir bileşenidir. Çözümleri ölçeklendirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes yatay Pod otomatik Scaler 'ı kullanma][aks-hpa]
- [Kubernetes kümesi otomatik Scaler 'ı kullanma][aks-cluster-autoscaler]
- [Sanal düğümler için otomatik ölçeklendirme örneğine göz atın][virtual-node-autoscale]
- [Sanal Kubelet açık kaynak kitaplığı hakkında daha fazla bilgi edinin][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
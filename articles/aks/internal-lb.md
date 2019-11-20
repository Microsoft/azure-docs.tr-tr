---
title: Azure Kubernetes Service (AKS) içinde iç yük dengeleyici oluşturma
description: Azure Kubernetes Service (AKS) ile hizmetlerinizi kullanıma sunmak için bir iç yük dengeleyici oluşturma ve kullanma hakkında bilgi edinin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 5842003d43d4268d0f663e8a57e40562a480e252
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615145"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) ile iç yük dengeleyici kullanma

Azure Kubernetes Service (AKS) uygulamasındaki uygulamalarınıza erişimi kısıtlamak için bir iç yük dengeleyici oluşturup kullanabilirsiniz. İç yük dengeleyici, Kubernetes hizmetini yalnızca Kubernetes kümesiyle aynı sanal ağda çalışan uygulamalar için erişilebilir hale getirir. Bu makalede, Azure Kubernetes Service (AKS) ile iç yük dengeleyici oluşturma ve kullanma işlemlerinin nasıl yapılacağı gösterilir.

> [!NOTE]
> Azure Load Balancer, *temel* ve *Standart*olmak üzere iki SKU 'da kullanılabilir. Varsayılan olarak, AKS üzerinde bir yük dengeleyici oluşturmak için bir hizmet bildirimi kullanıldığında *temel* SKU kullanılır. Daha fazla bilgi için bkz. [Azure Yük DENGELEYICI SKU karşılaştırması][azure-lb-comparison].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü `az --version` bulmak için ' i çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

Mevcut bir alt ağ veya kaynak grubu kullanıyorsanız AKS kümesi hizmet sorumlusu ağ kaynaklarını yönetmek için izne ihtiyaç duyuyor. Genel olarak, temsilcili kaynaklar üzerindeki hizmet sorumlusu rolüne *ağ katılımcısı* rolünü atayın. İzinler hakkında daha fazla bilgi için bkz. [diğer Azure kaynaklarına AKS erişimi verme][aks-sp].

## <a name="create-an-internal-load-balancer"></a>İç yük dengeleyici oluşturma

Bir iç yük dengeleyici oluşturmak için aşağıdaki örnekte gösterildiği gibi, `internal-lb.yaml` *LoadBalancer* hizmet türü ve *Azure-Yük Dengeleyici-iç* ek açıklaması ile adlı bir hizmet bildirimi oluşturun:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

[Kubectl Apply][kubectl-apply] kullanarak iç yük dengeleyiciyi dağıtın ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f internal-lb.yaml
```

Düğüm kaynak grubunda bir Azure yük dengeleyici oluşturulur ve AKS kümesiyle aynı sanal ağa bağlanır.

Hizmet ayrıntılarını görüntülediğinizde, iç yük dengeleyicinin IP adresi *dış IP* sütununda gösterilir. Bu bağlamda, *dış* , yük dengeleyicinin dış arabirimiyle ilişkili olduğundan, genel, HARICI bir IP adresi alır. Aşağıdaki örnekte gösterildiği gibi, IP adresinin *\<bekliyor\>* durumundan gerçek bir iç IP adresine değiştirilmesi bir veya iki dakika sürebilir:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Bir IP adresi belirtin

İç yük dengeleyiciye belirli bir IP adresi kullanmak isterseniz, *Loadbalancerıp* özelliğini yük dengeleyici YAML bildirimine ekleyin. Belirtilen IP adresi AKS kümesiyle aynı alt ağda bulunmalı ve bir kaynağa zaten atanmamalıdır.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Dağıtılırken ve hizmet ayrıntılarını görüntülediğinizde, *dış IP* sütununda IP adresı belirtilen IP adresini yansıtır:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Özel ağları kullanma

AKS kümenizi oluştururken Gelişmiş ağ ayarları belirtebilirsiniz. Bu yaklaşım, kümeyi mevcut bir Azure sanal ağına ve alt ağlarına dağıtmanıza imkan tanır. Tek senaryo, AKS kümenizi şirket içi ortamınıza bağlı özel bir ağa dağıtmak ve yalnızca dahili olarak erişilebilen hizmetleri çalıştırmak içindir. Daha fazla bilgi için bkz. [Kubenet][use-kubenet] veya [Azure CNI][advanced-networking]ile kendi sanal ağ alt ağlarınızı yapılandırma.

Özel ağ kullanan bir AKS kümesinde iç yük dengeleyici dağıtmak için önceki adımlarda hiçbir değişiklik yapılması gerekmez. Yük dengeleyici, AKS kümeniz ile aynı kaynak grubunda oluşturulur, ancak aşağıdaki örnekte gösterildiği gibi özel sanal ağınıza ve alt ağınıza bağlanır:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> AKS kümeniz için hizmet sorumlusu ' nı, Azure sanal ağ kaynaklarınızın dağıtıldığı kaynak grubuna *ağ katılımcısı* rolü olarak vermeniz gerekebilir. Hizmet sorumlusunu, gibi [az aks Show][az-aks-show] `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`ile görüntüleyin. Rol ataması oluşturmak için [az role atama Create][az-role-assignment-create] komutunu kullanın.

## <a name="specify-a-different-subnet"></a>Farklı bir alt ağ belirtin

Yük dengeleyiciniz için bir alt ağ belirtmek üzere hizmetinize *Azure-Load-Dengeleyici-iç alt* ek açıklamasını ekleyin. Belirtilen alt ağ, AKS kümeniz ile aynı sanal ağda olmalıdır. Dağıtıldığında, yük dengeleyici *dış IP* adresi belirtilen alt ağın bir parçasıdır.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Yük dengeleyiciyi silme

İç yük dengeleyiciyi kullanan tüm hizmetler silindiğinde, yük dengeleyici de silinir.

Ayrıca, aynı zamanda temel alınan Azure Yük dengeleyiciyi de silen gibi herhangi bir Kubernetes kaynağı `kubectl delete service internal-app`ile bir hizmeti doğrudan silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Kubernetes Services [belgelerindeki][kubernetes-services]Kubernetes hizmetleri hakkında daha fazla bilgi edinin.

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources

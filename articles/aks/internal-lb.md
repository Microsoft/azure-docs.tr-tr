---
title: İç yük dengeleyici oluşturma
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ile hizmetlerinizi kullanıma sunmak için bir iç yük dengeleyici oluşturma ve kullanma hakkında bilgi edinin.
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: cbb898d05ecc1f0796f3609adb1368c3d77de2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779750"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) ile iç yük dengeleyici kullanma

Azure Kubernetes Service (AKS) uygulamasındaki uygulamalarınıza erişimi kısıtlamak için bir iç yük dengeleyici oluşturup kullanabilirsiniz. İç yük dengeleyici, Kubernetes hizmetini yalnızca Kubernetes kümesiyle aynı sanal ağda çalışan uygulamalar için erişilebilir hale getirir. Bu makalede, Azure Kubernetes Service (AKS) ile iç yük dengeleyici oluşturma ve kullanma işlemlerinin nasıl yapılacağı gösterilir.

> [!NOTE]
> Azure Load Balancer, *temel* ve *Standart* olmak üzere iki SKU 'da kullanılabilir. Varsayılan olarak, bir AKS kümesi oluşturduğunuzda standart SKU kullanılır.  Yük dengeleyici olarak türünde bir hizmet oluştururken, kümeyi sağladığınızda aynı LB türüne sahip olursunuz. Daha fazla bilgi için bkz. [Azure Yük DENGELEYICI SKU karşılaştırması][azure-lb-comparison].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

Var olan bir alt ağ veya kaynak grubu kullanıyorsanız AKS küme kümesi kimliğinin ağ kaynaklarını yönetmek için izni olması gerekir. Daha fazla bilgi için bkz. Azure [Kubernetes Service (aks) içindeki kendı IP adresi aralığınızla Kubernetes kullanan ağı kullanma][use-kubenet] veya Azure [Kubernetes hizmeti 'nde (aks) Azure CNI ağı 'nı yapılandırma][advanced-networking]. Yük dengeleyiciyi [farklı bir alt ağda bir IP adresi][different-subnet]kullanacak şekilde yapılandırıyorsanız, aks kümesi kimliğinin bu alt ağa de okuma erişiminin olduğundan emin olun.

İzinler hakkında daha fazla bilgi için bkz. [diğer Azure kaynaklarına AKS erişimi verme][aks-sp].

## <a name="create-an-internal-load-balancer"></a>İç yük dengeleyici oluşturma

Bir iç yük dengeleyici oluşturmak için `internal-lb.yaml` Aşağıdaki örnekte gösterildiği gibi, *LoadBalancer* hizmet türü ve *Azure-Yük Dengeleyici-iç* ek açıklaması ile adlı bir hizmet bildirimi oluşturun:

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

Hizmet ayrıntılarını görüntülediğinizde, iç yük dengeleyicinin IP adresi *dış IP* sütununda gösterilir. Bu bağlamda, *dış* , yük dengeleyicinin dış arabirimiyle ilişkili olduğundan, genel, HARICI bir IP adresi alır. *\<pending\>* Aşağıdaki örnekte gösterildiği gıbı IP adresinin gerçek bir Iç IP adresine değiştirilmesi bir veya iki dakika sürebilir:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Bir IP adresi belirtin

İç yük dengeleyiciye belirli bir IP adresi kullanmak isterseniz, *Loadbalancerıp* özelliğini yük dengeleyici YAML bildirimine ekleyin. Bu senaryoda, belirtilen IP adresi AKS kümesi ile aynı alt ağda bulunmalı ve bir kaynağa zaten atanmamalıdır. Örneğin, Kubernetes alt ağı için belirlenmiş aralıkta bir IP adresi kullanmamalısınız.

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

Yük dengeleyiciyi farklı bir alt ağda yapılandırma hakkında daha fazla bilgi için bkz. [farklı bir alt ağ belirtme][different-subnet]

## <a name="use-private-networks"></a>Özel ağları kullanma

AKS kümenizi oluştururken Gelişmiş ağ ayarları belirtebilirsiniz. Bu yaklaşım, kümeyi mevcut bir Azure sanal ağına ve alt ağlarına dağıtmanıza imkan tanır. Tek senaryo, AKS kümenizi şirket içi ortamınıza bağlı özel bir ağa dağıtmak ve yalnızca dahili olarak erişilebilen hizmetleri çalıştırmak içindir. Daha fazla bilgi için bkz. [Kubenet][use-kubenet] veya [Azure CNI][advanced-networking]ile kendi sanal ağ alt ağlarınızı yapılandırma.

Özel ağ kullanan bir AKS kümesinde iç yük dengeleyici dağıtmak için önceki adımlarda hiçbir değişiklik yapılması gerekmez. Yük dengeleyici, AKS kümeniz ile aynı kaynak grubunda oluşturulur, ancak aşağıdaki örnekte gösterildiği gibi özel sanal ağınıza ve alt ağınıza bağlanır:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> AKS kümenizin küme kimliğini, Azure sanal ağ kaynaklarınızın dağıtıldığı kaynak grubuna *ağ katılımcısı* rolü için vermeniz gerekebilir. Küme kimliğini, gibi [az aks Show][az-aks-show]ile görüntüleyin `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"` . Rol ataması oluşturmak için [az role atama Create][az-role-assignment-create] komutunu kullanın.

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

Ayrıca, aynı `kubectl delete service internal-app` zamanda temel alınan Azure Yük dengeleyiciyi de silen gibi herhangi bir Kubernetes kaynağı ile bir hizmeti doğrudan silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Kubernetes Services [belgelerindeki][kubernetes-services]Kubernetes hizmetleri hakkında daha fazla bilgi edinin.

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet
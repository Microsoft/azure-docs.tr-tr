---
title: Azure’da Kubernetes öğreticisi - Kümeyi yükseltme
description: Bu Azure Kubernetes Service (AKS) öğreticisinde var olan bir AKS kümesini en son Kubernetes sürümüne yükseltmeyi öğreneceksiniz.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 9fe02c9b563259abb51a1a768c7facdf1bf601f7
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898831"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Öğretici: Azure Kubernetes Service (AKS) ile Kubernetes 'i yükseltme

Uygulama ve küme yaşam döngüsünün bir parçası olarak Kubernetes'in son sürümüne yükselterek yeni özelliklerden faydalanmak isteyebilirsiniz. Azure Kubernetes Hizmeti (AKS) kümesi, Azure CLI kullanılarak yükseltilebilir.

Yedi parçalık bu öğreticinin yedinci kısmında, bir Kubernetes kümesi yükseltilir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Geçerli ve kullanılabilir Kubernetes sürümlerini tanımlama
> * Kubernetes düğümlerini yükseltme
> * Başarılı bir yükseltmeyi doğrulama

## <a name="before-you-begin"></a>Başlamadan önce

Önceki öğreticilerde, bir uygulama bir kapsayıcı görüntüsüne paketlendi. Bu görüntü, Azure Container Registry yüklendi ve bir AKS kümesi oluşturdunuz. Uygulama daha sonra AKS kümesine dağıtıldı. Bu adımları yapmadıysanız ve birlikte takip etmek istiyorsanız, [öğretici 1 – kapsayıcı görüntüleri oluşturma][aks-tutorial-prepare-app]ile başlayın.

Bu öğreticide, Azure CLı sürüm 2.0.53 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Kullanılabilir küme sürümlerini alma

Bir kümeyi yükseltmeden önce, [az aks get-upgrades][] komutunu kullanarak hangi Kubernetes sürümlerinin yükseltme için kullanılabilir olduğunu öğrenin:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

Aşağıdaki örnekte, geçerli sürüm *1.13.10*' dir ve kullanılabilir sürümler *yükseltmeler* sütununda gösterilir.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.13.10          1.13.10            1.14.5, 1.14.6
```

## <a name="upgrade-a-cluster"></a>Kümeyi yükseltme

Uygulama çalıştırma kesintisini en aza indirmek için AKS düğümleri dikkatle ve drenaj. Bu işlemde aşağıdaki adımlar gerçekleştirilir:

1. Kubernetes Zamanlayıcı, Yükseltilecek düğüm üzerinde ek yığınların zamanlanmasını önler.
1. Düğüm üzerinde pod çalıştırmak, kümedeki diğer düğümlere zamanlanır.
1. En son Kubernetes bileşenlerini çalıştıran bir düğüm oluşturulur.
1. Yeni düğüm hazır olduğunda ve kümeye katıldığında, Kubernetes Zamanlayıcı üzerinde pod çalıştırmaya başlar.
1. Eski düğüm silinir ve kümedeki bir sonraki düğüm Cordon ve boşalt işlemini başlatır.

AKS kümesini yükseltmek için [az aks upgrade][] komutunu kullanın. Aşağıdaki örnek, kümeyi Kubernetes sürüm *1.14.6*'ye yükseltir.

> [!NOTE]
> Aynı anda yalnızca bir ikincil sürüm yükseltmesi yapabilirsiniz. Örneğin, *1.12. x* ' den *1.13. x*' e yükseltebilirsiniz, ancak *1.12. x* ' ten doğrudan *1.14. x* ' e yükseltemezsiniz. *1.12. x* ' den *1.14. x*' e yükseltmek için, ilk olarak *1.12. x* ' ten *1.13. x*' e yükseltin, sonra *1.13. x* ' den *1.14.* x ' e yükseltme gerçekleştirin

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.14.6
```

Aşağıdaki sıkıştırılmış örnek çıktıda, *Kubernetesversion* artık Reports *1.14.6*raporları gösterilmektedir:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.14.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Yükseltmeyi doğrulama

[az aks show][] komutunu aşağıdaki şekilde kullanarak yükseltmenin başarılı olup olmadığını doğrulayabilirsiniz:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Aşağıdaki örnek çıktıda, AKS kümesi çalıştırıldığı *Kubernetesversion 1.14.6*gösterilmektedir:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.14.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Küme silme

Bu öğretici serinin son parçasıysa, AKS kümesini silmek isteyebilirsiniz. Kubernetes düğümleri Azure sanal makinelerinde (VM) çalıştığından kümeyi kullanmasanız dahi ücret tahsil edilmeye devam eder. Kaynak grubunu, kapsayıcı hizmetini ve ilgili tüm kaynakları kaldırmak için [az Group Delete][az-group-delete] komutunu kullanın.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Kümeyi sildiğinizde, AKS kümesi tarafından kullanılan Azure Active Directory hizmet sorumlusu kaldırılmaz. Hizmet sorumlusunu kaldırma adımları için bkz. [aks hizmet sorumlusu konuları ve silme][sp-delete].

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir AKS kümesinde Kubernetes’i yükselttiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Geçerli ve kullanılabilir Kubernetes sürümlerini tanımlama
> * Kubernetes düğümlerini yükseltme
> * Başarılı bir yükseltmeyi doğrulama

AKS hakkında daha fazla bilgi için bu bağlantıyı izleyin.

> [!div class="nextstepaction"]
> [AKS genel bakış][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations

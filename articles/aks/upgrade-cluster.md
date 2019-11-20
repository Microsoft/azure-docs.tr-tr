---
title: Azure Kubernetes hizmeti (AKS) kümesini yükseltme
description: Azure Kubernetes hizmeti (AKS) kümesini nasıl yükselteceğinizi öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 36bd05940644b62bc3e58b642bfe6c00238383fa
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472467"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes hizmeti (AKS) kümesini yükseltme

AKS kümesinin yaşam döngüsünün bir parçası olarak genellikle en son Kubernetes sürümüne yükseltmeniz gerekir. En son Kubernetes güvenlik sürümlerini uygulamanız veya en son özellikleri almak için yükseltmeniz önemlidir. Bu makalede, bir AKS kümesindeki ana bileşenlerin veya tek bir varsayılan düğüm havuzunun nasıl yükseltileceğini gösterir.

Birden çok düğüm havuzu veya Windows Server düğümü kullanan AKS kümeleri için (Şu anda AKS 'de önizlemededir), bkz. [aks 'de düğüm havuzunu yükseltme][nodepool-upgrade].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure CLı sürüm 2.0.65 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

> [!WARNING]
> AKS kümesi yükseltmesi, düğümlerinizin bir eş ve drenajı tetikler. Kullanılabilir düşük bir işlem kotası varsa, yükseltme başarısız olabilir.  Daha fazla bilgi için bkz. [kotaları artırma](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request?branch=pr-en-us-83289) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Kullanılabilir AKS kümesi yükseltmelerini denetle

Kümeniz için hangi Kubernetes sürümlerinin kullanılabilir olduğunu denetlemek için [az aks Get-yükseltmeler][az-aks-get-upgrades] komutunu kullanın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *Myakscluster* adlı kümede kullanılabilir yükseltmeleri denetler:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Bir AKS kümesini yükselttiğinizde Kubernetes ikincil sürümleri atlanamaz. Örneğin, *1.12. x* -> *1.13. x* veya *1.13. x* -> *1.14. x* arasında yapılan yükseltmelere izin verilir, ancak *1.12. x* -> *1.14. x* değildir.
>
> Yükseltmek için, *1.12. x* -> *1.14. x*sürümünden önce *1.12. x* -> *1.13. x*' ten yükseltme yapın, ardından *1.13. x* -> *1.14. x*' ten yükseltme yapın.

Aşağıdaki örnek çıktı, kümenin *1.13.9* ve *1.13.10*sürümlerine yükseltileceğini gösterir:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Kullanılabilir bir yükseltme yoksa şunları alacaksınız:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>AKS kümesini yükseltme

AKS kümeniz için kullanılabilir sürümlerin bir listesi ile yükseltmek için [az aks Upgrade][az-aks-upgrade] komutunu kullanın. Yükseltme işlemi sırasında, AKS, belirtilen Kubernetes sürümünü çalıştıran kümeye yeni bir düğüm ekler, ardından çalışan uygulamaların kesintiye uğramasını azaltmak için eski düğümlerden birini dikkatle indirin [ve][kubernetes-drain] kapatın. Yeni düğüm çalışan uygulama yığınlarını onayladığı zaman, eski düğüm silinir. Bu işlem, kümedeki tüm düğümler yükseltilene kadar yinelenir.

Aşağıdaki örnek, bir kümeyi *1.13.10*sürümüne yükseltir:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Kaç tane düğüme sahip olduğunuza bağlı olarak, kümeyi yükseltmek birkaç dakika sürer. 

> [!NOTE]
> Bir küme yükseltmesinin tamamlanabilmesi için bir toplam izin verilen süre vardır. Bu süre, `10 minutes * total number of nodes in the cluster`ürünü alınarak hesaplanır. Örneğin, 20 düğümlü bir kümede, yükseltme işlemleri 200 dakika içinde başarılı olmalıdır veya AKS kurtarılamayan bir küme durumundan kaçınmak için işlemi başarısız olur. Yükseltme hatasını kurtarmak için, zaman aşımı isabet alındıktan sonra yükseltme işlemini yeniden deneyin.

Yükseltmenin başarılı olduğunu doğrulamak için [az aks Show][az-aks-show] komutunu kullanın:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Aşağıdaki örnek çıktıda, kümenin artık *1.13.10*çalıştığını gösterilmektedir:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, mevcut bir AKS kümesini nasıl yükselteceğiniz açıklanır. AKS kümelerini dağıtma ve yönetme hakkında daha fazla bilgi için bkz. öğreticiler kümesi.

> [!div class="nextstepaction"]
> [AKS öğreticileri][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool

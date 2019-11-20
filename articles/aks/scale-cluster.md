---
title: Azure Kubernetes hizmeti (AKS) kümesini ölçeklendirme
description: Azure Kubernetes Service (AKS) kümesindeki düğümlerin sayısını ölçeklendirmeyi öğrenin.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 719f45aeeb5c7aa7e9b5e597ed461808c9d2b005
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472594"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) kümesindeki düğüm sayısını ölçeklendirme

Uygulamalarınızın kaynağı değişiklik içeriyorsa, bir AKS kümesini farklı sayıda düğüm çalıştıracak şekilde el ile ölçeklendirebilirsiniz. Ölçeği azaltmak için düğümler, çalışan uygulamaların kesintiye uğratılmasını en aza indirmek için dikkatle [ve drenapaştırlanır][kubernetes-drain] . Ölçeği artırma sırasında, AKS 'ler, düğümler üzerinde zamanlanmadan önce Kubernetes kümesi tarafından `Ready` işaretlenene kadar bekler.

## <a name="scale-the-cluster-nodes"></a>Küme düğümlerini ölçeklendirme

İlk olarak, [az aks Show][az-aks-show] komutunu kullanarak düğüm havuzunuzun *adını* alın. Aşağıdaki örnek, *Myresourcegroup* kaynak grubundaki *Myakscluster* adlı küme için düğüm havuzu adını alır:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Aşağıdaki örnek çıktı, *adının* *nodepool1*olduğunu gösterir:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Küme düğümlerini ölçeklendirmek için [az aks Scale][az-aks-scale] komutunu kullanın. Aşağıdaki örnek, *Myakscluster* adlı bir kümeyi tek bir düğüme ölçeklendirir. *Nodepool1*gibi önceki komuttan kendi *--nodepool-adınızı* sağlayın:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Aşağıdaki örnek çıktı, *Agentpoolprofiles* bölümünde gösterildiği gibi, kümenin tek bir düğüme başarıyla ölçeklendirildi olduğunu gösterir:

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, düğüm sayısını artırmak veya azaltmak için bir AKS kümesini el ile ölçeklendirmeniz gerekir. Kümenizi otomatik olarak ölçeklendirmek için [cluster otomatik Scaler öğesini][cluster-autoscaler] de kullanabilirsiniz.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md

---
title: Azure Kubernetes Service (AKS) kümesini ölçeklendirme
description: Azure Kubernetes Service (AKS) kümesindeki düğümlerin sayısını ölçeklendirmeyi öğrenin.
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: 1468f9a0a23935022ed14488dfb65d789828d310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782896"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) kümesindeki düğüm sayısını ölçeklendirme

Uygulamalarınızın kaynağı değişiklik içeriyorsa, bir AKS kümesini farklı sayıda düğüm çalıştıracak şekilde el ile ölçeklendirebilirsiniz. Ölçeği azaltmak için düğümler, çalışan uygulamaların kesintiye uğratılmasını en aza indirmek için dikkatle [ve drenapaştırlanır][kubernetes-drain] . Ölçeği artırma sırasında AKS 'ler, düğümler `Ready` üzerinde zamanlanmadan önce düğüm Kubernetes kümesi tarafından işaretlenene kadar bekler.

## <a name="scale-the-cluster-nodes"></a>Küme düğümlerini ölçeklendirme

İlk olarak, [az aks Show][az-aks-show] komutunu kullanarak düğüm havuzunuzun *adını* alın. Aşağıdaki örnek, *Myresourcegroup* kaynak grubundaki *Myakscluster* adlı küme için düğüm havuzu adını alır:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Aşağıdaki örnek çıktı, *adının* *nodepool1* olduğunu gösterir:

```output
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

Küme düğümlerini ölçeklendirmek için [az aks Scale][az-aks-scale] komutunu kullanın. Aşağıdaki örnek, *Myakscluster* adlı bir kümeyi tek bir düğüme ölçeklendirir. `--nodepool-name` *Nodepool1* gibi önceki komuttan kendinizinkini sağlayın:

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


## <a name="scale-user-node-pools-to-0"></a>`User`Düğüm havuzlarını 0 olarak Ölçeklendir

`System`Her zaman çalışan düğümler gerektiren düğüm havuzlarının aksine, `User` düğüm havuzları 0 ' a ölçeklenmenize izin verir. Sistem ve Kullanıcı düğümü havuzları arasındaki farklılıklar hakkında daha fazla bilgi edinmek için bkz. [sistem ve Kullanıcı düğümü havuzları](use-system-pools.md).

Bir Kullanıcı havuzunu 0 olarak ölçeklendirmek için yukarıdaki komuta alternatif olarak [az aks nodepool ölçeğini][az-aks-nodepool-scale] kullanabilir `az aks scale` ve düğüm sayımının 0 olarak ayarlanmasını sağlayabilirsiniz.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

Ayrıca `User` , `--min-count` [küme otomatik](cluster-autoscaler.md) ölçeklendirme parametresini 0 olarak ayarlayarak düğüm havuzlarını 0 düğümlere otomatik olarak dönüştürebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, düğüm sayısını artırmak veya azaltmak için bir AKS kümesini el ile ölçeklendirmeniz gerekir. Kümenizi otomatik olarak ölçeklendirmek için [cluster otomatik Scaler öğesini][cluster-autoscaler] de kullanabilirsiniz.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
---
title: Azure Kubernetes hizmeti (AKS) düğüm görüntülerini yükseltme
description: AKS küme düğümleri ve düğüm havuzlarındaki görüntüleri nasıl yükselteceğinizi öğrenin.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 4f6ac01c1d4df288c823142abbc93e981048d8db
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767538"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes hizmeti (AKS) düğümü görüntüsü yükseltme

AKS, en yeni işletim sistemi ve çalışma zamanı güncelleştirmeleriyle güncel olması için bir düğümdeki görüntülerin yükseltilmesini destekler. AKS, en son güncelleştirmelerle hafta başına yeni bir görüntü sağlar; bu nedenle, Linux veya Windows yamaları dahil olmak üzere en son özellikler için düğümlerinizin görüntülerini düzenli olarak yükseltmeniz yararlı olur. Bu makalede, AKS kümesi düğüm görüntülerini yükseltme ve Kubernetes sürümünü yükseltmeden düğüm havuzu görüntülerini güncelleştirme işlemlerinin nasıl yapılacağı gösterilir.

AKS tarafından sunulan en son görüntüler hakkında daha fazla bilgi için bkz. [aks sürüm notları](https://github.com/Azure/AKS/releases).

Kümeniz için Kubernetes sürümünü yükseltme hakkında daha fazla bilgi için bkz. [AKS kümesini yükseltme][upgrade-cluster].

> [!NOTE]
> AKS kümesinin düğümlerin sanal makine ölçek kümelerini kullanması gerekir.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Düğüm havuzunuzun en son düğüm görüntüsünde olup olmadığını denetleyin

Aşağıdaki komutla, düğüm havuzunuz için en son düğüm görüntüsü sürümünü nasıl kullanabileceğiniz görebilirsiniz: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

Çıktıda `latestNodeImageVersion` Aşağıdaki örnekte olduğu gibi görebilirsiniz:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

Bu nedenle `nodepool1` , kullanılabilir en son düğüm görüntüsü için `AKSUbuntu-1604-2020.10.28` . Şimdi, şunu çalıştırarak düğüm havuzunuzun kullandığı geçerli düğüm görüntüsü sürümüyle karşılaştırabilirsiniz:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Örnek bir çıktı şöyle olabilir:

```output
"AKSUbuntu-1604-2020.10.08"
```

Bu örnekte, geçerli `AKSUbuntu-1604-2020.10.08` görüntü sürümünden en son sürüme yükseltebilirsiniz `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Tüm düğüm havuzlarındaki tüm düğümleri yükselt

Düğüm görüntüsünü yükseltmek ile yapılır `az aks upgrade` . Düğüm görüntüsünü yükseltmek için aşağıdaki komutu kullanın:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Yükseltme sırasında, `kubectl` etiketleri almak ve geçerli düğüm görüntü bilgilerini filtrelemek için aşağıdaki komutla düğüm görüntülerinin durumunu kontrol edin:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Yükseltme tamamlandığında, `az aks show` güncelleştirilmiş düğüm havuzu ayrıntılarını almak için kullanın. Geçerli düğüm görüntüsü `nodeImageVersion` özellikte gösterilmektedir.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Belirli bir düğüm havuzunu yükseltme

Bir düğüm havuzundaki görüntünün yükseltilmesi, bir kümedeki görüntünün yükseltilme ile benzerdir.

Düğüm havuzunun işletim sistemi görüntüsünü bir Kubernetes kümesi yükseltmesi yapmadan güncelleştirmek için `--node-image-only` Aşağıdaki örnekteki seçeneğini kullanın:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Yükseltme sırasında, `kubectl` etiketleri almak ve geçerli düğüm görüntü bilgilerini filtrelemek için aşağıdaki komutla düğüm görüntülerinin durumunu kontrol edin:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Yükseltme tamamlandığında, `az aks nodepool show` güncelleştirilmiş düğüm havuzu ayrıntılarını almak için kullanın. Geçerli düğüm görüntüsü `nodeImageVersion` özellikte gösterilmektedir.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Düğüm dalgalanma ile düğüm görüntülerini yükseltme

Düğüm görüntüsü yükseltme işlemini hızlandırmak için, bir özelleştirilebilir düğüm dalgalanma değeri kullanarak düğüm görüntülerinizi yükseltebilirsiniz. Varsayılan olarak, AKS yükseltmeleri yapılandırmak için bir ek düğüm kullanır.

Yükseltme hızını artırmak isterseniz, `--max-surge` daha hızlı tamamlanabilmeleri için yükseltme için kullanılacak düğüm sayısını yapılandırmak için değerini kullanın. Çeşitli ayarların ticareti hakkında daha fazla bilgi edinmek için `--max-surge` bkz. [Özelleştirme düğümü aşırı yükseltmesi][max-surge].

Aşağıdaki komut, düğüm görüntüsü yükseltmesi gerçekleştirmek için en fazla dalgalanma değerini ayarlar:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Yükseltme sırasında, `kubectl` etiketleri almak ve geçerli düğüm görüntü bilgilerini filtrelemek için aşağıdaki komutla düğüm görüntülerinin durumunu kontrol edin:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

`az aks nodepool show`Güncelleştirilmiş düğüm havuzu ayrıntılarını almak için kullanın. Geçerli düğüm görüntüsü `nodeImageVersion` özellikte gösterilmektedir.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Sonraki adımlar

- En son düğüm görüntüleri hakkında bilgi için bkz. [aks sürüm notları](https://github.com/Azure/AKS/releases) .
- [AKS kümesini yükseltme][upgrade-cluster]Ile Kubernetes sürümünü nasıl yükselteceğinizi öğrenin.
- [GitHub eylemleri ile küme ve düğüm havuzu yükseltmelerini otomatik olarak uygulama][github-schedule]
- Birden çok düğüm havuzu ve [birden çok düğüm havuzu oluşturma ve yönetme][use-multiple-node-pools]ile düğüm havuzlarını yükseltme hakkında daha fazla bilgi edinin.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update

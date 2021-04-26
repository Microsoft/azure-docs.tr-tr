---
title: Azure Kubernetes Service (AKS) içindeki sistem düğüm havuzlarını kullanma
description: Azure Kubernetes hizmeti 'nde (AKS) sistem düğüm havuzları oluşturmayı ve yönetmeyi öğrenin
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 8b41b43c70f72ab327de2f1d59415cc1f49e5a5b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767412"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmetindeki (AKS) sistem düğüm havuzlarını yönetme

Azure Kubernetes hizmeti 'nde (AKS), aynı yapılandırmanın düğümleri *düğüm havuzlarında* birlikte gruplandırılır. Düğüm havuzları, uygulamalarınızı çalıştıran temel VM 'Leri içerir. Sistem düğüm havuzları ve Kullanıcı düğümü havuzları, AKS kümeleriniz için iki farklı düğüm havuzu modudur. Sistem düğüm havuzları, ve gibi kritik sistem yığınlarını barındırmanın birincil amacını sunar `CoreDNS` `metrics-server` . Kullanıcı düğümü havuzları, uygulama yığınlarınızı barındırmanın birincil amacını sunar. Ancak, aks kümenizde yalnızca bir havuza sahip olmak istiyorsanız, uygulama Pod 'leri sistem düğüm havuzlarında zamanlanabilir. Her bir AKS kümesi en az bir düğümü olan en az bir sistem düğüm havuzu içermelidir.

> [!Important]
> Bir üretim ortamında AKS kümeniz için tek bir sistem düğüm havuzu çalıştırırsanız, düğüm havuzu için en az üç düğüm kullanmanızı öneririz.

## <a name="before-you-begin"></a>Başlamadan önce

* Azure CLı sürüm 2.3.1 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="limitations"></a>Sınırlamalar

Sistem düğüm havuzlarını destekleyen AKS kümelerini oluştururken ve yönetirken aşağıdaki sınırlamalar geçerlidir.

* [Azure Kubernetes Service (AKS) Içindeki kotalar, sanal makine boyutu kısıtlamaları ve bölge kullanılabilirliği][quotas-skus-regions]konusuna bakın.
* AKS kümesi, sanal makine ölçek kümeleri ile VM türü ve *Standart* SKU yük dengeleyici olarak oluşturulmalıdır.
* Düğüm havuzunun adı yalnızca küçük harfli alfasayısal karakterler içerebilir ve küçük harfle başlamalıdır. Linux düğüm havuzları için uzunluk 1 ile 12 karakter arasında olmalıdır. Windows düğüm havuzları için uzunluk 1 ile 6 karakter arasında olmalıdır.
* Düğüm havuzu modunu ayarlamak için 2020-03-01 veya üzeri bir API sürümü kullanılmalıdır. 2020-03-01 ' den eski API sürümlerinde oluşturulan kümeler yalnızca Kullanıcı düğümü havuzlarını içerir, ancak [güncelleştirme havuzu modu adımlarını](#update-existing-cluster-system-and-user-node-pools)izleyerek Sistem düğüm havuzlarını içerecek şekilde geçirilebilirler.
* Düğüm havuzunun modu gerekli bir özelliktir ve ARM şablonları veya doğrudan API çağrıları kullanılırken açıkça ayarlanması gerekir.

## <a name="system-and-user-node-pools"></a>Sistem ve Kullanıcı düğümü havuzları

Bir sistem düğüm havuzu için AKS, **Kubernetes.Azure.com/Mode: System** etiketini otomatik olarak düğümlerine atar. Bu, AKS 'in bu etiketi içeren düğüm havuzlarında sistem kimliklerinin planlanmasını tercih etmesine neden olur. Bu etiket, sistem düğüm havuzlarında uygulama yığınlarını zamanınızdan engel olmaz. Bununla birlikte, yanlış yapılandırılmış veya standart dışı uygulama kimliklerinin sistem dizilerini yanlışlıkla sonlandırmasını engellemek için uygulama yığınlarından önemli sistem dizilerini yalıtmanızı öneririz. Özel bir sistem düğüm havuzu oluşturarak bu davranışı uygulayabilirsiniz. `CriticalAddonsOnly=true:NoSchedule`Uygulama kimliklerinin sistem düğüm havuzlarında zamanlanmasını engellemek için taınt kullanın.

Sistem düğüm havuzları aşağıdaki kısıtlamalara sahiptir:

* Sistem havuzları osType, Linux olmalıdır.
* Kullanıcı düğümü havuzları osType, Linux veya Windows olabilir.
* Sistem havuzlarının en az bir düğüm içermesi ve Kullanıcı düğüm havuzlarının sıfır veya daha fazla düğüm içermesi gerekir.
* Sistem düğüm havuzları, en az 2 vCPU ve 4 GB bellek için bir VM SKU 'SU gerektirir. Ancak Burstable-VM (B serisi) önerilmez.
* Özellikle büyük kümeler (örneğin, birden çok CoreDNS Pod çoğaltmaları, 3-4 + Eklentiler, vb.) için en az iki düğüm 4 vCPU önerilir (ör. Standard_DS4_v2).
* Sistem düğüm havuzlarının [En düşük ve en yüksek değer formülünde Pod][maximum-pods]tarafından açıklandığı gibi en az 30 tane olması gerekir.
* Spot düğüm havuzları, Kullanıcı düğümü havuzları gerektirir.
* Ek bir sistem düğüm havuzu ekleme veya hangi düğüm havuzunun bir sistem düğüm havuzu olarak değiştirilmesi, sistem yığınlarını otomatik olarak *taşımayacak* . System Pod, bir Kullanıcı düğümü havuzuna değiştirseniz bile aynı düğüm havuzunda çalışmaya devam edebilir. Daha önce bir sistem düğüm havuzudur sistem Pod çalıştıran bir düğüm havuzunu siler veya ölçeklendirirseniz, bu sistem havuzları yeni sistem düğümü havuzuna tercih edilen zamanlamaya göre yeniden dağıtılır.

Düğüm havuzlarıyla aşağıdaki işlemleri yapabilirsiniz:

* Ayrılmış bir sistem düğüm havuzu oluşturma (düğüm havuzlarının sistem havuzları planlamasını tercih et `mode:system` )
* Bir sistem düğüm havuzunu bir Kullanıcı düğümü havuzu olacak şekilde değiştirin. Bu, AKS kümesinde konumunu almak için başka bir sistem düğüm havuzunuz olması gerekir.
* Bir Kullanıcı düğümü havuzunu sistem düğüm havuzu olacak şekilde değiştirin.
* Kullanıcı düğümü havuzlarını silin.
* Sistem düğüm havuzlarını, kendi AKS kümesindeki yerini almak için başka bir sistem düğüm havuzunuz olması şartıyla silebilirsiniz.
* AKS kümesinde birden fazla sistem düğüm havuzu olabilir ve en az bir sistem düğüm havuzu gerekir.
* Mevcut düğüm havuzlarındaki çeşitli sabit ayarları değiştirmek istiyorsanız, bunları değiştirmek için yeni düğüm havuzları oluşturabilirsiniz. Bir örnek, yeni bir maxPods ayarı ile yeni bir düğüm havuzu eklemek ve eski düğüm havuzunu silmektir.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Sistem düğüm havuzu ile yeni bir AKS kümesi oluşturma

Yeni bir AKS kümesi oluşturduğunuzda, otomatik olarak tek bir düğüm içeren bir sistem düğüm havuzu oluşturursunuz. İlk düğüm havuzu, sistem türü bir mod olarak varsayılan olarak belirlenmiştir. İle yeni düğüm havuzları oluşturduğunuzda `az aks nodepool add` , mod parametresini açıkça belirtmediğiniz müddetçe, bu düğüm havuzları Kullanıcı düğüm havuzlarıdır.

Aşağıdaki örnek *eastus* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

AKS kümesi oluşturmak için [az aks create][az-aks-create] komutunu kullanın. Aşağıdaki örnek, bir düğüm içeren ayrılmış bir sistem havuzu ile *Myakscluster* adlı bir küme oluşturur. Üretim iş yükleriniz için en az üç düğüm ile sistem düğüm havuzlarını kullandığınızdan emin olun. Bu işlemin tamamlanması birkaç dakika sürebilir.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Mevcut bir AKS kümesine ayrılmış bir sistem düğüm havuzu ekleme

> [!Important]
> Düğüm havuzu oluşturulduktan sonra CLı aracılığıyla düğüm ilerledikten geçiş yapamazsınız.

Varolan AKS kümelerine bir veya daha fazla sistem düğüm havuzu ekleyebilirsiniz. Uygulama yığınlarınızı Kullanıcı düğümü havuzlarında zamanlamanız ve sistem düğüm havuzlarını yalnızca kritik sistem yığınlarına ayırmayı öneririz. Bu, uygulama kimliklerinin yanlışlıkla sistem yığınlarını sonlandırmasını önler. `CriticalAddonsOnly=true:NoSchedule`Sistem düğüm havuzlarınız için bu davranışı [taınt][aks-taints] ile zorlayın. 

Aşağıdaki komut, varsayılan sayıda üç düğüme sahip bir mod türü sisteminin ayrılmış düğüm havuzunu ekler.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Düğüm havuzunuzun ayrıntılarını gösterme

Aşağıdaki komutla, düğüm havuzunuzun ayrıntılarını denetleyebilirsiniz.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Sistem düğüm havuzları için **sistem** türü bir mod tanımlanır ve Kullanıcı düğüm havuzları için **Kullanıcı** türünde bir mod tanımlanır. Bir sistem havuzu için, taınt 'nin olarak ayarlandığını doğrulayın `CriticalAddonsOnly=true:NoSchedule` . Bu, uygulama yığınlarının bu düğüm havuzunda zamanlanmasını engeller.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Mevcut küme sistemini ve Kullanıcı düğümü havuzlarını Güncelleştir

> [!NOTE]
> Bir sistem düğüm havuzu modu ayarlamak için 2020-03-01 veya üzeri bir API sürümü kullanılmalıdır. 2020-03-01 'den eski API sürümlerinde oluşturulan kümeler, sonuç olarak yalnızca Kullanıcı düğümü havuzlarını içerir. Daha eski kümelerde sistem düğüm havuzu işlevselliği ve avantajları almak için, mevcut düğüm havuzlarının modunu en son Azure CLı sürümünde aşağıdaki komutlarla güncelleştirin.

Hem sistem hem de Kullanıcı düğüm havuzlarının modlarını değiştirebilirsiniz. Bir sistem düğüm havuzunu bir Kullanıcı havuzu için, yalnızca başka bir sistem düğüm havuzu zaten varsa, AKS kümesinde değiştirebilirsiniz.

Bu komut, bir sistem düğüm havuzunu bir Kullanıcı düğümü havuzuna değiştirir.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Bu komut, bir Kullanıcı düğümü havuzunu bir sistem düğümü havuzuna değiştirir.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Sistem düğüm havuzunu silme

> [!Note]
> API sürüm 2020-03-02 ' den önceki AKS kümelerinde sistem düğüm havuzlarını kullanmak için yeni bir sistem düğümü havuzu ekleyin, sonra özgün varsayılan düğüm havuzunu silin.

Bunlardan birini silebilmek için, AKS kümenizde en az iki sistem düğüm havuzunuz olmalıdır.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kümeyi silmek için, AKS kaynak grubunu silmek için [az Group Delete][az-group-delete] komutunu kullanın:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir AKS kümesinde sistem düğüm havuzlarını oluşturmayı ve yönetmeyi öğrendiniz. Birden çok düğüm havuzu kullanma hakkında daha fazla bilgi için bkz. [birden çok düğüm havuzu kullanma][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node

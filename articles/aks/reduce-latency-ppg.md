---
title: Azure Kubernetes hizmeti (AKS) kümelerinde gecikme süresini azaltmak için yakınlık yerleştirme gruplarını kullanın
description: AKS kümesi iş yüklerinizde gecikme süresini azaltmak için yakınlık yerleşimi gruplarını nasıl kullanacağınızı öğrenin.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fbcff37185b2cba71c405e917653d52397479007
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779606"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Yakınlık yerleştirme gruplarıyla gecikme süresini azaltma

> [!Note]
> AKS üzerinde yakınlık yerleşimi grupları kullanırken, birlikte bulundurma yalnızca aracı düğümleri için geçerlidir. Düğümden düğüme ve ilgili barındırılan Pod 'ın Pod gecikme süresine kadar geliştirildi. Birlikte bulundurma, kümenin denetim düzlemi yerleştirmesini etkilemez.

Uygulamanızı Azure 'da dağıttığınızda, sanal makine (VM) örneklerinin bölgeler veya kullanılabilirlik alanları arasında yayılması ağ gecikmesi oluşturur ve bu da uygulamanızın genel performansını etkileyebilir. Yakınlık yerleşimi grubu, Azure işlem kaynaklarının fiziksel olarak birbirlerine yakın bir yerde bulunduğundan emin olmak için kullanılan mantıksal bir gruplandırmadır. Oyun, mühendislik benzetimleri ve yüksek frekanslı ticaret (HFT) gibi bazı uygulamalar, düşük gecikme süresi ve hızla tamamlanan görevler gerektirir. Bunlar gibi yüksek performanslı bilgi işlem (HPC) senaryolarında, kümenizin düğüm havuzları için [yakınlık yerleşimi grupları](../virtual-machines/co-location.md#proximity-placement-groups) (PPG) kullanmayı düşünün.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure CLı sürüm 2,14 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

### <a name="limitations"></a>Sınırlamalar

* Yakınlık yerleşimi grubu en fazla bir kullanılabilirlik bölgesine eşlenir.
* Bir yakınlık yerleşimi grubunu ilişkilendirmek için bir düğüm havuzunun sanal makine ölçek kümelerini kullanması gerekir.
* Düğüm havuzu, yalnızca düğüm havuzu oluşturma zamanında bir yakınlık yerleşimi grubunu ilişkilendirebilir.

## <a name="node-pools-and-proximity-placement-groups"></a>Düğüm havuzları ve yakınlık yerleştirme grupları

Bir yakınlık yerleşimi grubuyla dağıttığınız ilk kaynak belirli bir veri merkezine bağlanır. Aynı yakınlık yerleşimi grubuyla dağıtılan ek kaynaklar aynı veri merkezinde birlikte bulunur. Yakınlık yerleşimi grubunu kullanan tüm kaynaklar durdurulduktan sonra (serbest bırakıldığında) veya silindikten sonra artık eklenmez.

* Birçok düğüm havuzu tek bir yakınlık yerleşimi grubuyla ilişkilendirilebilir.
* Düğüm havuzu yalnızca tek bir yakınlık yerleşimi grubuyla ilişkilendirilebilir.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Kullanılabilirlik alanları ile yakınlık yerleşimi gruplarını yapılandırma

> [!NOTE]
> Yakınlık yerleştirme grupları, en fazla bir kullanılabilirlik alanında kullanmak üzere bir düğüm havuzu gerektirdiğinde, tek bir bölgedeki VM 'Ler için [% 99,9 olan temel Azure VM SLA 'sı](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) hala geçerli olur.

Yakınlık yerleştirme grupları bir düğüm havuzu kavramıdır ve tek tek düğüm havuzıyla ilişkilendirilir. Bir PPG kaynağı kullanmanın AKS denetim düzlemi kullanılabilirliği üzerinde hiçbir etkisi yoktur. Bu, bir kümenin bölgelerle nasıl tasarlanmalıdır. Bir kümenin birden çok bölgeye yayılmasını sağlamak için aşağıdaki tasarımın kullanılması önerilir.

* 3 bölge kullanarak ilk sistem havuzu ile bir küme sağlayın ve hiçbir yakınlık yerleştirme grubu ilişkilendirilmez. Bu, sistem ayırımlarının birden çok bölgeye yayılan ayrılmış bir düğüm havuzunda yer almasını sağlar.
* Her havuz ile ilişkili benzersiz bir bölge ve yakınlık yerleşimi grubu olan ek Kullanıcı düğümü havuzları ekleyin. Nodepool1 bölge 1 ve PPG1, nodepool2 bölge 2 ve PPG2, nodepool3 bölge 3 ' te PPG3 bir örnektir. Bu, bir küme düzeyinde, düğümlerin birden çok bölgeye yayılmasını ve her bir düğüm havuzunun ayrılmış bir PPG kaynağı ile belirlenen bölgede birlikte yer almasını sağlar.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Yakınlık yerleştirme grubuyla yeni bir AKS kümesi oluşturma

Aşağıdaki örnek, *merkezde ABD* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturmak için [az Group Create][az-group-create] komutunu kullanır. *Myakscluster* adlı bir aks kümesi daha sonra [az aks Create][az-aks-create] komutu kullanılarak oluşturulur.

Hızlandırılmış ağ, sanal makinelerin ağ performansını önemli ölçüde geliştirir. İdeal olarak, hızlandırılmış ağ ile birlikte yakınlık yerleştirme gruplarını kullanın. Varsayılan olarak, AKS, en fazla iki veya daha fazla vCPU içeren Azure sanal makinesini içeren [desteklenen sanal makine örneklerinde](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)hızlandırılmış ağ kullanır.

İlk sistem düğüm havuzuyla ilişkili bir yakınlık yerleşimi grubu ile yeni bir AKS kümesi oluşturun:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Aşağıdaki komutu çalıştırın ve döndürülen KIMLIĞI depolayın:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Komut, yaklaşan CLı komutları için ihtiyaç duyduğunuz *kimlik* değerini içeren çıktıyı üretir:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Aşağıdaki komutta *Myppgresourceıd* değeri için yakınlık yerleşimi grubu kaynak kimliğini kullanın:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Var olan bir kümeye yakınlık yerleştirme grubu ekleme

Yeni bir düğüm havuzu oluşturarak var olan bir kümeye bir yakınlık yerleşimi grubu ekleyebilirsiniz. Daha sonra isteğe bağlı olarak, mevcut iş yüklerini yeni düğüm havuzuna geçirebilir ve sonra özgün düğüm havuzunu silebilirsiniz.

Daha önce oluşturduğunuz yakınlık yerleşimi grubunu kullanın ve bu, AKS kümenizdeki her iki düğüm havuzunda bulunan aracı düğümlerinin fiziksel olarak aynı veri merkezinde yer aldığından emin olur.

Daha önce oluşturduğunuz yakınlık yerleşimi grubundan kaynak KIMLIĞI ' ni kullanın ve komutla yeni bir düğüm havuzu ekleyin [`az aks nodepool add`][az-aks-nodepool-add] :

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Temizleme

Kümeyi silmek için, [`az group delete`][az-group-delete] AKS kaynak grubunu silmek için komutunu kullanın:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Sonraki adımlar

* [Yakınlık yerleşimi grupları][proximity-placement-groups]hakkında daha fazla bilgi edinin.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az_aks_create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
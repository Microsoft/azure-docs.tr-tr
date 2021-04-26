---
title: Çalışma süresi SLA 'Sı ile Azure Kubernetes hizmeti (AKS)
description: Azure Kubernetes hizmeti (AKS) API sunucusu için isteğe bağlı çalışma süresi SLA teklifi hakkında bilgi edinin.
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 846446b4c19c066afe789bf636d68ad37b20709e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779570"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes hizmeti (AKS) çalışma süresi SLA 'Sı

Çalışma süresi SLA 'Sı, bir küme için mali olarak desteklenen ve daha yüksek SLA 'yı etkinleştirmeye yönelik isteğe bağlı bir özelliktir Çalışma süresi SLA, Kullanılabilirlik Alanları kullanmayan kümeler için [kullanılabilirlik alanları][availability-zones] ve% 99,9 ' i kullanan kümeler Için Kubernetes API sunucu uç noktasının% 99,95 kullanılabilirliğini garanti eder. AKS, SLA gereksinimlerinin karşılanmasını sağlamak için güncelleştirme ve hata etki alanları genelinde ana düğüm çoğaltmaları kullanır.

Uyumluluk gereksinimlerini karşılamak için SLA 'ya ihtiyaç duyan veya son kullanıcıları için SLA genişletmeyi gerektiren müşterilerin bu özelliği etkinleştirmesi gerekir. Kritik iş yükleri olan müşteriler, daha yüksek bir çalışma süresi SLA 'Sı üzerinden faydalanabilir. Çalışma süresi SLA özelliğinin Kullanılabilirlik Alanları ile kullanılması, Kubernetes API sunucusunun çalışma süresi için daha yüksek bir kullanılabilirlik sağlar.  

Müşteriler, bir hizmet düzeyi hedefi (SLO 99,5) olan sınırsız sayıda ücretsiz küme oluşturabilir ve tercih edilen SLO veya SLA çalışma süresini gerektiği gibi kabul edebilir.

> [!Important]
> Çıkış kilidi olan kümeler için bkz. uygun bağlantı noktalarını açmak için [çıkış trafiğini sınırlayın](limit-egress-traffic.md) .

## <a name="region-availability"></a>Bölge kullanılabilirliği

* Çalışma süresi SLA 'Sı, [aks 'in desteklendiği](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)Genel bölgelerde ve Azure Kamu bölgelerinde kullanılabilir.
* Çalışma süresi SLA 'Sı, AKS 'nin desteklendiği tüm genel bölgelerde [özel AKS kümelerinde][private-clusters] kullanılabilir.

## <a name="sla-terms-and-conditions"></a>SLA hüküm ve koşulları

Çalışma süresi SLA 'Sı ücretli bir özelliktir ve küme başına etkindir. Çalışma süresi SLA fiyatlandırması, ayrı kümelerin boyutuna göre değil, farklı kümelerin sayısıyla belirlenir. Daha fazla bilgi için [çalışma SÜRESI SLA fiyatlandırma ayrıntılarını](https://azure.microsoft.com/pricing/details/kubernetes-service/) görüntüleyebilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

* [Azure CLI](/cli/azure/install-azure-cli) sürüm 2.8.0 veya üstünü yükler

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Çalışma süresi SLA 'Sı ile yeni bir küme oluşturma

Çalışma süresi SLA 'Sı ile yeni bir küme oluşturmak için Azure CLı 'yi kullanırsınız.

Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
[`az aks create`][az-aks-create]BIR AKS kümesi oluşturmak için komutunu kullanın. Aşağıdaki örnekte, bir düğüm ile *myAKSCluster* adlı bir küme oluşturulmuştur. Bu işlemin tamamlanabilmesi birkaç dakika sürer:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Birkaç dakika sonra komut tamamlanır ve küme hakkında JSON biçimli bilgileri döndürür. Aşağıdaki JSON kod parçacığında, kümenizin çalışma süresi SLA 'Sı ile etkinleştirildiğini belirten SKU 'nun ücretli katmanı gösterilmektedir:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Mevcut bir kümeyi çalışma süresi SLA 'sını kullanacak şekilde değiştirme

İsteğe bağlı olarak, mevcut kümelerinizi çalışma süresi SLA 'sını kullanacak şekilde güncelleştirebilirsiniz.

Önceki adımlarla bir AKS kümesi oluşturduysanız, kaynak grubunu silin:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Yeni bir kaynak grubu oluşturun:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Yeni bir küme oluşturun ve çalışma süresi SLA 'sını kullanmayın:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

[`az aks update`][az-aks-update]Mevcut kümeyi güncelleştirmek için komutunu kullanın:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Aşağıdaki JSON kod parçacığında, kümenizin çalışma süresi SLA 'Sı ile etkinleştirildiğini belirten SKU 'nun ücretli katmanı gösterilmektedir:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="opt-out-of-uptime-sla"></a>Çalışma süresi SLA 'Sı devre dışı

Kümenizi ücretsiz katmana değiştirecek şekilde güncelleştirebilir ve çalışma süresi SLA 'sını devre dışı bırakabilirsiniz.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
 ```

## <a name="clean-up"></a>Temizleme

Ücretlerden kaçınmak için oluşturduğunuz tüm kaynakları temizleyin. Kümeyi silmek için, [`az group delete`][az-group-delete] AKS kaynak grubunu silmek için komutunu kullanın:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Sonraki adımlar

AKS kümesi iş yüklerinizde yüksek kullanılabilirliği artırmak için [kullanılabilirlik alanları][availability-zones] kullanın.

Kümenizi [çıkış trafiğini sınırlayacak](limit-egress-traffic.md)şekilde yapılandırın.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md

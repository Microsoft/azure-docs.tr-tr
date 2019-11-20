---
title: Azure Kubernetes Service (AKS) içinde standart bir SKU yük dengeleyici kullanma
description: Azure Kubernetes Service (AKS) ile hizmetlerinizi kullanıma sunmak için standart SKU ile yük dengeleyiciyi nasıl kullanacağınızı öğrenin.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: ef826239bc916b4ccf25785f92397286017d00f7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171400"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde standart bir SKU yük dengeleyici kullanma

Azure Kubernetes Service (AKS) içinde `LoadBalancer` türündeki Kubernetes Hizmetleri aracılığıyla uygulamalara erişim sağlamak için bir Azure Load Balancer kullanabilirsiniz. AKS üzerinde çalışan bir yük dengeleyici, iç veya dış yük dengeleyici olarak kullanılabilir. İç yük dengeleyici, bir Kubernetes hizmetini yalnızca AKS kümesi ile aynı sanal ağda çalışan uygulamalar için erişilebilir hale getirir. Dış yük dengeleyici, giriş için bir veya daha fazla genel IP alır ve genel IP 'Leri kullanarak bir Kubernetes hizmetini dışarıdan erişilebilir hale getirir.

Azure Load Balancer, *temel* ve *Standart*olmak üzere iki SKU 'da kullanılabilir. Varsayılan olarak, bir AKS kümesi oluşturduğunuzda *Standart* SKU kullanılır. *Standart* SKU yük dengeleyici kullanmak, daha büyük bir arka uç havuzu boyutu ve kullanılabilirlik alanları gibi ek özellikler ve işlevler sağlar. Kullanmayı seçmeden önce *Standart* ve *temel* yük dengeleyiciler arasındaki farkları anlamanız önemlidir. Bir AKS kümesi oluşturduktan sonra, bu küme için yük dengeleyici SKU 'sunu değiştiremezsiniz. *Temel* ve *Standart* SKU 'lar hakkında daha fazla bilgi için bkz. [Azure yük dengeleyici SKU karşılaştırması][azure-lb-comparison].

Bu makalede, Kubernetes ve Azure Load Balancer kavramlarının temel bir şekilde anlaşıldığı varsayılır. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) Için Kubernetes temel kavramları][kubernetes-concepts] ve [Azure Load Balancer nedir?][azure-lb].

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure CLı sürüm 2.0.74 veya üstünü çalıştırıyor olmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][install-azure-cli].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, *Standart* SKU 'nun Azure Load Balancer bir aks kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

AKS küme hizmeti sorumlusu Ayrıca mevcut bir alt ağ veya kaynak grubu kullanıyorsanız ağ kaynaklarını yönetme iznine de sahip olmalıdır. Genel olarak, temsilcili kaynaklar üzerindeki hizmet sorumlusu rolüne *ağ katılımcısı* rolünü atayın. İzinler hakkında daha fazla bilgi için bkz. [diğer Azure kaynaklarına AKS erişimi verme][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Temel SKU Load Balancer standart SKU 'ya taşıma

Temel SKU 'nun Load Balancer var olan bir kümeniz varsa, standart SKU Load Balancer sahip bir küme kullanmaya geçiş yaparken dikkat etmeniz önemli davranış farklılıkları vardır.

Örneğin, kümeleri geçirmek için mavi/yeşil dağıtımlar yapmak, bir kümenin `load-balancer-sku` türü için yalnızca küme oluşturma zamanında tanımlandıkları yaygın bir uygulamadır. Ancak, *temel SKU* yük dengeleyiciler *Standart* SKU IP adreslerini gerektirdiğinden *Standart* SKU yük dengeleyiciler Ile uyumlu olmayan *temel SKU* IP adreslerini kullanır. Kümeleri Load Balancer SKU 'Larına geçirirken, uyumlu bir IP adresi SKU 'SU olan yeni bir IP adresi gerekir.

Kümeleri geçirme hakkında daha fazla bilgi için, geçiş yaparken göz önünde bulundurmanız gereken önemli konuların bir listesini görüntülemek için [geçiş konuları hakkındaki belgelerimizi](acs-aks-migration.md) ziyaret edin. Aşağıdaki sınırlamalar Ayrıca AKS 'de standart SKU yük dengeleyicileri kullanırken dikkat edilecek önemli davranış farklarıdır.

### <a name="limitations"></a>Sınırlamalar

*Standart* SKU ile yük dengeleyiciyi destekleyen aks kümelerini oluşturup yönetirken aşağıdaki sınırlamalar geçerlidir:

* AKS kümesinden giden trafiğe izin vermek için en az bir genel IP veya IP öneki gereklidir. Genel IP veya IP öneki Ayrıca, denetim düzlemi ve aracı düğümleri arasındaki bağlantıyı sürdürmek için ve önceki AKS sürümleriyle uyumluluğu sürdürmek için de gereklidir. *Standart* bir SKU yük dengeleyiciye sahip genel IP 'LERI veya IP öneklerini belirtmek için aşağıdaki seçenekleriniz vardır:
    * Kendi genel IP 'nizi sağlayın.
    * Kendi genel IP öneklerinizi sağlayın.
    * AKS kümesinin, genellikle başlangıçta *Mc_* olarak adlandırılan aks kümesi olarak oluşturulan aynı kaynak grubunda *Standart* SKU genel IP 'leri oluşturmasına izin vermek için en fazla 100 ' a kadar bir sayı belirtin. AKS, genel IP 'yi *Standart* SKU yük dengeleyicisine atar. Varsayılan olarak, genel IP, genel IP öneki veya IP sayısı belirtilmemişse, AKS kümesiyle aynı kaynak grubunda bir genel IP otomatik olarak oluşturulur. Ayrıca, genel adreslere izin vermeniz ve IP oluşturma ile ilgili herhangi bir Azure Ilkesinin oluşturulmasını önetmeniz gerekir.
* Yük Dengeleyici için *Standart* SKU kullanırken, Kubernetes sürüm *1,13 veya üstünü*kullanmanız gerekir.
* Yük dengeleyici SKU 'SU tanımlama yalnızca bir AKS kümesi oluşturduğunuzda yapılabilir. Bir AKS kümesi oluşturulduktan sonra yük dengeleyici SKU 'sunu değiştiremezsiniz.
* Tek bir kümede yalnızca bir tür yük dengeleyici SKU 'SU (temel veya standart) kullanabilirsiniz.
* *Standart* SKU yük dengeleyiciler yalnızca *Standart* SKU IP adreslerini destekler.

## <a name="configure-the-load-balancer-to-be-internal"></a>Yük dengeleyiciyi iç olarak yapılandırma

Ayrıca yük dengeleyiciyi iç olarak yapılandırabilir ve genel bir IP 'yi kullanıma sunmamalısınız. Yük dengeleyiciyi iç olarak yapılandırmak için, *LoadBalancer* hizmetine bir ek açıklama olarak `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` ekleyin. [Burada][internal-lb-yaml]bir YAML bildiriminin yanı sıra bir iç yük dengeleyici hakkında daha fazla ayrıntı görebilirsiniz.

## <a name="scale-the-number-of-managed-public-ips"></a>Yönetilen genel IP sayısını ölçeklendirme

Varsayılan olarak oluşturulan, yönetilen giden genel IP 'Ler ile *Standart* SKU yük dengeleyicisi kullanırken, *yük dengeleyici-yönetilen-IP-Count* parametresini kullanarak yönetilen giden genel IP sayısını ölçeklendirebilirsiniz.

Var olan bir kümeyi güncelleştirmek için aşağıdaki komutu çalıştırın. Bu parametre, birden çok yönetilen giden genel IP 'si olması için küme oluşturma zamanında de ayarlanabilir.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Yukarıdaki örnek, *Myresourcegroup*Içindeki *Myakscluster* kümesi Için yönetilen giden genel IP sayısını *2* ' ye ayarlar. 

Ayrıca, `--load-balancer-managed-outbound-ip-count` parametresini ekleyerek ve istediğiniz değere ayarlayarak, kümenizi oluştururken yönetilen giden genel IP 'lerin ilk sayısını ayarlamak için *Load-dengeleyiciyle yönetilen-IP-Count* parametresini de kullanabilirsiniz. Varsayılan yönetilen giden genel IP sayısı 1 ' dir.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Çıkış için kendi genel IP 'Leri veya ön eklerini sağlayın

*Standart* SKU yük dengeleyicisi kullanılırken aks kümesi, aks kümesi için oluşturulan aynı kaynak grubunda ortak IP 'yi otomatik olarak oluşturur ve genel IP 'yi *Standart* SKU yük dengeleyicisine atar. Alternatif olarak, küme oluşturma sırasında kendi genel IP 'nizi atayabilir veya var olan bir kümenin yük dengeleyici özelliklerini güncelleştirebilirsiniz.

Birden çok IP adresi veya ön ek sunarak, tek bir yük dengeleyici nesnesi arkasında IP adresini tanımlarken birden çok yedekleme hizmeti tanımlayabilirsiniz. Belirli düğümlerin çıkış uç noktası, ilişkili oldukları hizmete bağlı olarak değişir.

> [!IMPORTANT]
> *Standart* SKU 'su yük dengeleyiciniz ile çıkış için *Standart* SKU genel IP 'lerini kullanmanız gerekir. [Az Network public-ip show][az-network-public-ip-show] komutunu kullanarak genel IP 'nizin SKU 'sunda emin olabilirsiniz:
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Genel IP 'nizin kimliklerini listelemek için [az Network public-ip show][az-network-public-ip-show] komutunu kullanın.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Yukarıdaki komut, *Myresourcegroup* kaynak grubundaki *Mypublicıp* genel IP 'nin kimliğini gösterir.

Kümenizi genel IP 'larınız ile güncelleştirmek için, *Load-dengeleyici-giden-IP* parametresi ile *az aks Update* komutunu kullanın.

Aşağıdaki örnek, önceki komuttan gelen kimliklerle *yük dengeleyici-giden-IP* parametresini kullanır.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*Standart* SKU yük dengeleyicinizle çıkış IÇIN genel IP öneklerini de kullanabilirsiniz. Aşağıdaki örnek, genel IP öneklerinizin kimliklerini listelemek için [az Network public-ip önekini göster][az-network-public-ip-prefix-show] komutunu kullanır:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Yukarıdaki komut, *Myresourcegroup* kaynak grubundaki *Mypublicipprefix* genel IP ön ekinin kimliğini gösterir.

Aşağıdaki örnek, önceki komuttan gelen kimliklerle *yük dengeleyici-giden-IP-önekleri* parametresini kullanır.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Genel IP 'Ler ve IP önekleri, AKS kümeniz ile aynı aboneliğin aynı bölgede ve bir parçası olmalıdır. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Küme oluşturma zamanında kendi genel IP veya öneklerinizi tanımlama

Çıkış uç noktaları beyaz listeleme gibi senaryoları desteklemek için küme oluşturma sırasında kendi IP adreslerinizi veya IP öneklerinizi almak isteyebilirsiniz. Kümenin yaşam döngüsünün başlangıcında kendi genel IP 'Leri ve IP öneklerinizi tanımlamak için yukarıda gösterilen aynı parametreleri küme oluşturma adımınıza ekleyin.

Başlangıç aşamasında genel IP 'inizle yeni bir küme oluşturmak için, *Load-dengeleyici-giden-IP* parametresi ile *az aks Create* komutunu kullanın.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Başlangıçta genel IP öneklerinizi içeren yeni bir küme oluşturmak için, *Load-dengeleyici-giden-IP-önekleri* parametresiyle *az aks Create* komutunu kullanın.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="show-the-outbound-rule-for-your-load-balancer"></a>Yük dengeleyiciniz için giden kuralı gösterme

Yük dengeleyicide oluşturulan giden kuralı göstermek için [az Network lb Outbound-Rule listesini][az-network-lb-outbound-rule-list] kullanın ve aks kümenizin düğüm kaynak grubunu belirtin:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Önceki komutlarda yük dengeleyiciniz için giden kuralı listelenir, örneğin:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Örnek çıktıda *AllocatedOutboundPorts* 0 ' dır. *AllocatedOutboundPorts* DEĞERI, SNAT bağlantı noktası ayırmanın arka uç havuz boyutuna göre otomatik atamaya geri dönmesi anlamına gelir. Daha fazla bilgi için bkz. Azure 'da [giden kuralları][azure-lb-outbound-rules] ve [giden bağlantıları][azure-lb-outbound-connections] Load Balancer.

## <a name="next-steps"></a>Sonraki adımlar

Kubernetes Services [belgelerindeki][kubernetes-services]Kubernetes hizmetleri hakkında daha fazla bilgi edinin.

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

---
title: Azure Kubernetes Service 'te (AKS) Azure CNı ağını yapılandırma
description: Azure Kubernetes Service 'te (AKS) Azure CNı (Gelişmiş) ağını, bir AKS kümesini mevcut bir sanal ağa ve alt ağa dağıtma dahil olmak üzere nasıl yapılandıracağınızı öğrenin.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 839aa012cedaaa6f5bd3d1edad60e3ea7278133b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775898"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service 'te (AKS) Azure CNı ağını yapılandırma

Varsayılan olarak, aks kümeleri [Kubernetes kullanan][kubenet]kullanır ve sizin için bir sanal ağ ve alt ağ oluşturulur. *Kubernetes kullanan* ile, düğümler bir sanal ağ alt ağından bir IP adresi alır. Ağ adresi çevirisi (NAT) düğümler üzerinde yapılandırılır ve düğüm IP 'nin arkasında "gizli" IP adresini alır. Bu yaklaşım, Pod 'nin kullanabilmesi için ağ alanınızda ayırmanız gereken IP adresi sayısını azaltır.

[Azure Container ağ arabirimi (CNı)][cni-networking]ile her Pod, alt ağdan bir IP adresi alır ve doğrudan erişilebilir. Bu IP adresleri, ağ alanınızda benzersiz olmalı ve önceden planlanmalıdır. Her düğümün desteklediği en fazla sayıda düğüm için bir yapılandırma parametresi vardır. Düğüm başına düşen IP adresi sayısı, bu düğüm için önde ayrılır. Bu yaklaşım daha fazla planlama gerektirir ve genellikle, uygulamanızın beklentilerine göre daha büyük bir alt ağda küme yeniden oluşturma gereksinimiyle sonuçlanır.

Bu makalede, bir AKS kümesi için bir sanal ağ alt ağı oluşturmak ve kullanmak üzere *Azure CNI* ağı 'nın nasıl kullanılacağı gösterilmektedir. Ağ seçenekleri ve konuları hakkında daha fazla bilgi için bkz. [Kubernetes ve AKS Için ağ kavramları][aks-network-concepts].

## <a name="prerequisites"></a>Önkoşullar

* AKS kümesinin sanal ağı giden internet bağlantısına izin vermelidir.
* Aks kümeleri,,, `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` veya `192.0.2.0/24` Kubernetes hizmeti adres aralığı, Pod adres aralığı veya küme sanal ağ adresi aralığı için kullanılamıyor olabilir.
* AKS kümesi tarafından kullanılan küme kimliğinin, sanal ağınızdaki alt ağda en az [ağ katılımcısı](../role-based-access-control/built-in-roles.md#network-contributor) izinleri olması gerekir. Yerleşik ağ katılımcısı rolünü kullanmak yerine [özel bir rol](../role-based-access-control/custom-roles.md) tanımlamak istiyorsanız aşağıdaki izinler gereklidir:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* AKS düğüm havuzuna atanan alt ağ, [yetkilendirilmiş bir alt ağ](../virtual-network/subnet-delegation-overview.md)olamaz.

## <a name="plan-ip-addressing-for-your-cluster"></a>Kümeniz için IP adresi planlayın

Azure CNı ağı ile yapılandırılan kümeler için ek planlama yapılması gerekir. Sanal ağınızın ve alt ağının boyutu, çalıştırmayı planladığınız düğüm sayısını ve küme düğüm sayısını içermelidir.

Pod ve kümenin düğümlerine ait IP adresleri, sanal ağ içindeki belirtilen alt ağdan atanır. Her düğüm, birincil bir IP adresi ile yapılandırılır. Varsayılan olarak, 30 ek IP adresi, düğümde zamanlanan Pod 'ye atanan Azure CNI tarafından önceden yapılandırılmıştır. Kümenizin ölçeğini ölçeklendirirseniz, her düğüm benzer şekilde alt ağdan IP adresleriyle yapılandırılır. [Düğüm başına en fazla düğüm sayısını](#maximum-pods-per-node)da görüntüleyebilirsiniz.

> [!IMPORTANT]
> Gerekli IP adresi sayısı, yükseltme ve ölçeklendirme işlemlerine yönelik hususlar içermelidir. IP adresi aralığını yalnızca sabit sayıda düğümü destekleyecek şekilde ayarlarsanız, kümenizi yükseltemez veya ölçeklendirebilirsiniz.
>
> * AKS kümenizi **yükselttiğinizde** , kümeye yeni bir düğüm dağıtılır. Hizmetler ve iş yükleri yeni düğümde çalışmaya başlar ve eski bir düğüm kümeden kaldırılır. Bu sıralı yükseltme işlemi, en az bir IP adresi bloğunun kullanılabilir olmasını gerektirir. Düğüm sayıınız daha sonra `n + 1` .
>   * Windows Server düğüm havuzlarını kullandığınızda bu durum özellikle önemlidir. AKS 'deki Windows Server düğümleri, düğüm havuzunda bir yükseltme gerçekleştirmeniz yerine otomatik olarak Windows güncelleştirmelerini uygulamaz. Bu yükseltme, son Windows Server 2019 temel düğüm görüntüsü ve güvenlik düzeltme ekleriyle yeni düğümleri dağıtır. Bir Windows Server düğüm havuzunu yükseltme hakkında daha fazla bilgi için bkz. [AKS 'de düğüm havuzunu yükseltme][nodepool-upgrade].
>
> * Bir AKS kümesini **ölçeklendirirseniz** , kümeye yeni bir düğüm dağıtılır. Hizmetler ve iş yükleri yeni düğümde çalışmaya başlar. IP adresi aralığınızı, kümenizin destekleyebileceği düğüm sayısını ve sınırları nasıl ölçeklendirmek istediğinizi göz önünde bulundurmanız gerekir. Yükseltme işlemleri için bir ek düğüm da dahil edilmelidir. Düğüm sayıınız daha sonra `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Düğümlerinizin maksimum sayıda pods çalıştırmasını ve düzenli olarak dizin oluşturup dağıtımını bekleliyorsanız, düğüm başına bazı ek IP adreslerini de çarpanlara katmalısınız. Bu ek IP adresleri, bir hizmetin silinmesi ve yeni bir hizmetin dağıtılması ve adresi edinmeniz için birkaç saniye sürebilir.

AKS kümesi için IP adresi planı bir sanal ağ, düğümler ve düğüm için en az bir alt ağ ve bir Kubernetes hizmeti adres aralığı içerir.

| Adres aralığı/Azure kaynağı | Sınırlar ve boyutlandırma |
| --------- | ------------- |
| Sanal ağ | Azure sanal ağı,/8 ile büyük olabilir, ancak 65.536 yapılandırılmış IP adresleriyle sınırlıdır. Adres alanınızı yapılandırmadan önce, diğer sanal ağlardaki hizmetlerle iletişim kurmak dahil olmak üzere tüm ağ ihtiyaçlarınızı göz önünde bulundurun. Örneğin, bir adres alanının çok büyük bir bölümünü yapılandırırsanız ağınız içindeki diğer adres alanları ile ilgili sorunlar yaşayabilirsiniz.|
| Alt ağ | , Kümenizde sağlanmış olabilecek düğümlerin, yığınların ve tüm Kubernetes ve Azure kaynaklarına uyum sağlayacak kadar büyük olmalıdır. Örneğin, dahili bir Azure Load Balancer dağıtırsanız, ön uç IP 'Leri genel IP 'Ler değil, küme alt ağından ayrılır. Alt ağ boyutu ayrıca hesap yükseltme işlemlerini veya gelecekteki ölçekleme ihtiyaçlarını da almalıdır.<p />Yükseltme işlemleri için ek bir düğüm dahil *Minimum* alt ağ boyutunu hesaplamak için: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>50 düğüm kümesi örneği: `(51) + (51  * 30 (default)) = 1,581` (/21 veya daha büyük)<p/>Ek 10 düğümleri ölçeklendirmek için sağlama de içeren 50 düğümlü bir küme için örnek: `(61) + (61 * 30 (default)) = 1,891` (/21 veya daha büyük)<p>Kümenizi oluştururken düğüm başına en fazla sayıda Pod belirtmezseniz, düğüm başına en fazla düğüm sayısı *30* olarak ayarlanır. Gerekli olan en az IP adresi sayısı bu değere göre belirlenir. En az IP adresi gereksinimlerinizi farklı bir maksimum değer üzerinde hesaplarsanız, kümenizi dağıtırken bu değeri ayarlamak için [düğüm başına en fazla sayıda Pod 'yi yapılandırma](#configure-maximum---new-clusters) bölümüne bakın. |
| Kubernetes hizmeti adres aralığı | Bu Aralık, bu sanal ağ üzerinde herhangi bir ağ öğesi tarafından kullanılmamalıdır veya bu sanal ağa bağlı olmamalıdır. CıDR hizmet adresi/12 ' den küçük olmalıdır. Bu aralığı farklı AKS kümelerinde yeniden kullanabilirsiniz. |
| Kubernetes DNS hizmeti IP adresi | Küme hizmeti bulma tarafından kullanılacak Kubernetes hizmeti adres aralığı içindeki IP adresi. Adres aralığınızı. 1 gibi ilk IP adresini kullanmayın. Alt ağ aralığınızı ilk adres *Kubernetes. default. svc. Cluster. Local* adresi için kullanılır. |
| Docker köprüsü adresi | Docker köprüsü ağ adresi, tüm Docker yüklemelerinde bulunan varsayılan *docker0* köprüsü ağ adresini temsil eder. *Docker0* Bridge, aks kümeleri veya Pod tarafından kullanılmadığından, aks kümesi içindeki *Docker derlemesi* gibi senaryoları desteklemeye devam etmek için bu adresi ayarlamanız gerekir. Docker köprü ağ adresi için bir CıDR seçmeniz gerekir, aksi takdirde Docker otomatik olarak bir alt ağ seçip diğer Cıdrs ile çakışabilecek. Kümenin hizmet CıDR ve pod CıDR dahil olmak üzere ağlarınızdaki CIO 'nun geri kalanı ile çakışmayan bir adres alanı seçmelisiniz. Varsayılan değer 172.17.0.1/16. Bu aralığı farklı AKS kümelerinde yeniden kullanabilirsiniz. |

## <a name="maximum-pods-per-node"></a>Düğüm başına maksimum Pod

AKS kümesindeki düğüm başına en fazla düğüm sayısı 250 ' dir. Düğüm başına *varsayılan* en fazla düğüm sayısı, *Kubernetes kullanan* ve *Azure CNI* ağı ve Küme dağıtımı yöntemi arasında farklılık gösterir.

| Dağıtım yöntemi | Kubenet varsayılanı | Azure CNı varsayılan | Dağıtımda yapılandırılabilir |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Evet (250 'e kadar) |
| Resource Manager şablonu | 110 | 30 | Evet (250 'e kadar) |
| Portal | 110 | 110 (düğüm havuzları sekmesinde yapılandırılır) | No |

### <a name="configure-maximum---new-clusters"></a>En fazla yeni küme yapılandırma

Küme dağıtım sırasında düğüm başına en fazla düğüm sayısını veya yeni düğüm havuzları eklerken yapılandırabileceksiniz. Azure CLı ile veya bir Kaynak Yöneticisi şablonuyla dağıtırsanız, düğüm başına maksimum düğüm sayısı değerini 250 olarak belirleyebilirsiniz.

Yeni düğüm havuzları oluştururken maxPods belirtmezseniz, Azure CNı için varsayılan değer olan 30 değerini alırsınız.

Düğüm başına en fazla düğüm sayısı için en düşük değer, sistem Pod 'nin küme durumu için kritik alan garantisi sağlamak üzere zorlanır. Düğüm başına maksimum pod için ayarlanalabilen en düşük değer 10 ' dur ve yalnızca her düğüm havuzunun yapılandırmasında en az 30 pod için alan varsa. Örneğin, düğüm başına en fazla 10 düğüm için maksimum Pod 'nin ayarlanması, her bir düğüm havuzunun en az 3 düğüme sahip olmasını gerektirir. Bu gereksinim, oluşturulan her yeni düğüm havuzu için geçerlidir; bu nedenle, 10 düğüm başına en fazla sayıda düğüm, eklenen her düğüm havuzu için en az 3 düğüm olmalıdır.

| Ağ | Minimum | Maksimum |
| -- | :--: | :--: |
| Azure CNı | 10 | 250 |
| Kubernetes kullanan | 10 | 110 |

> [!NOTE]
> Yukarıdaki tablodaki minimum değer AKS hizmeti tarafından kesinlikle zorlanır. Bir maxPods değerini, bu şekilde gösterilen en düşük boyuttan düşük ayarlayamazsınız. Bu, kümenin başlamasını engelleyebilir.

* **Azure CLI**: `--max-pods` [az aks Create][az-aks-create] komutuyla bir küme dağıtırken bağımsız değişkeni belirtin. En büyük değer 250 ' dir.
* **Kaynak Yöneticisi şablonu**: `maxPods` bir kümeyi Kaynak Yöneticisi şablonuyla dağıtırken [Managedclusteragentpoolprofile] nesnesinde özelliğini belirtin. En büyük değer 250 ' dir.
* **Azure Portal**: bir kümeyi Azure Portal birlikte dağıtırken düğüm başına en fazla düğüm sayısını değiştiremezsiniz. Azure CNI ağ kümeleri, Azure Portal kullanarak dağıtırken düğüm başına 30 Pod ile sınırlıdır.

### <a name="configure-maximum---existing-clusters"></a>En fazla mevcut kümeleri yapılandırma

Düğüm başına maxPod ayarı, yeni bir düğüm havuzu oluşturduğunuzda tanımlanabilir. Var olan bir kümede düğüm başına maxPod ayarını artırmanız gerekiyorsa, istenen yeni maxPod sayımına sahip yeni bir düğüm havuzu ekleyin. Yığınlarınızı yeni havuza geçirdikten sonra eski havuzu silin. Bir kümedeki eski bir havuzu silmek için, düğüm havuzu modlarını [sistem düğüm havuzları belgesinde][system-node-pools]tanımlandığı şekilde ayarlamadığınıza emin olun.

## <a name="deployment-parameters"></a>Dağıtım parametreleri

Bir AKS kümesi oluşturduğunuzda, aşağıdaki parametreler Azure CNı ağı için yapılandırılabilir:

**Sanal ağ**: Kubernetes kümesini dağıtmak istediğiniz sanal ağ. Kümeniz için yeni bir sanal ağ oluşturmak isterseniz, *Yeni oluştur* ' u seçin ve *sanal ağ oluşturma* bölümündeki adımları izleyin. Bir Azure sanal ağı için sınırlamalar ve Kotalar hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Alt ağ**: kümeyi dağıtmak istediğiniz sanal ağ içindeki alt ağ. Kümeniz için sanal ağda yeni bir alt ağ oluşturmak isterseniz, *Yeni oluştur* ' u seçin ve *alt ağ oluşturma* bölümündeki adımları izleyin. Karma bağlantı için, adres aralığı ortamınızdaki diğer tüm sanal ağlarla çakışmamalıdır.

**Azure ağ eklentisi**: Azure ağ eklentisi kullanıldığında, aks kümesine ait olmayan clusterCIDR IÇINDEKI bir IP 'ye sahip VM 'Lerden "ExternalTrafficPolicy = Local" Iç yük dengeleyici hizmetine erişilemez.

**Kubernetes hizmeti adres aralığı**: Bu parametre, Kubernetes 'in kümenizdeki iç [hizmetlere][services] atadığı sanal IP 'ler kümesidir. Aşağıdaki gereksinimleri karşılayan herhangi bir özel adres aralığını kullanabilirsiniz:

* Kümenizin sanal ağ IP adresi aralığı içinde olmaması gerekir
* Küme sanal ağ eşlerinin bulunduğu diğer sanal ağlarla çakışmamalıdır
* Herhangi bir şirket içi IP ile çakışmamalıdır
* ,, Veya aralıkları dahilinde olmamalıdır `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16``192.0.2.0/24`

Bir hizmet adres aralığını kümeniz ile aynı sanal ağ içinde belirtmek Teknik olarak mümkün olsa da, bunun yapılması önerilmez. Çakışan IP aralıkları kullanılırsa öngörülemeyen davranış oluşabilir. Daha fazla bilgi için bu makalenin [SSS](#frequently-asked-questions) bölümüne bakın. Kubernetes hizmetleri hakkında daha fazla bilgi için bkz. Kubernetes belgelerindeki [Hizmetler][services] .

**Kubernetes DNS HIZMETI IP adresi**: kümenin DNS HIZMETI için IP adresi. Bu adresin *Kubernetes hizmeti adres aralığı* içinde olması gerekir. Adres aralığınızı. 1 gibi ilk IP adresini kullanmayın. Alt ağ aralığınızı ilk adres *Kubernetes. default. svc. Cluster. Local* adresi için kullanılır.

**Docker köprü adresi**: Docker köprü ağ adresi, tüm Docker yüklemelerinde bulunan varsayılan *docker0* Köprüsü ağ adresini temsil eder. *Docker0* Bridge, aks kümeleri veya Pod tarafından kullanılmadığından, aks kümesi içindeki *Docker derlemesi* gibi senaryoları desteklemeye devam etmek için bu adresi ayarlamanız gerekir. Docker köprü ağ adresi için bir CıDR seçmeniz gerekir, aksi takdirde Docker diğer Cıdrs ile çakışabilecek bir alt ağ seçer. Kümenin hizmet CıDR ve pod CıDR dahil olmak üzere ağlarınızdaki CIO 'nun geri kalanı ile çakışmayan bir adres alanı seçmelisiniz.

## <a name="configure-networking---cli"></a>Ağ iletişimini yapılandırma-CLı

Azure CLı ile bir AKS kümesi oluşturduğunuzda, Azure CNı ağını de yapılandırabilirsiniz. Azure CNı ağı etkin olan yeni bir AKS kümesi oluşturmak için aşağıdaki komutları kullanın.

İlk olarak, AKS kümesinin katılacağını varolan alt ağın alt ağ kaynak KIMLIĞINI alın:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Gelişmiş ağlarla bir küme oluşturmak için, bağımsız değişkeniyle [az aks Create][az-aks-create] komutunu kullanın `--network-plugin azure` . Değeri, `--vnet-subnet-id` önceki adımda toplanan alt ağ kimliğiyle güncelleştirin:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Ağı yapılandırma-Portal

Azure portal aşağıdaki ekran görüntüsünde, AKS kümesi oluşturma sırasında bu ayarları yapılandırmanın bir örneği gösterilmektedir:

! [Azure portal gelişmiş ağ yapılandırması] [Portal-01-ağ-Gelişmiş]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>IP 'lerin dinamik ayırması ve gelişmiş alt ağ desteği (Önizleme)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

> [!NOTE] 
> Bu önizleme özelliği şu anda aşağıdaki bölgelerde kullanılabilir:
>
> * Orta Batı ABD

Geleneksel CNı ile bir dezavantajı, AKS kümesi büyüdükçe Pod IP adreslerinin tükenmesi olduğundan, tüm kümeyi daha büyük bir alt ağda yeniden oluşturma gereksinimiyle sonuçlanır. Azure CNı 'deki yeni dinamik IP ayırma özelliği, AKS kümesini barındıran alt ağdan ayrı bir alt ağdan farklı pod IP 'Leri ayırarak bu sorunu çözer.  Aşağıdaki avantajları sunar:

* **Daha ıyı IP kullanımı: IP**'Ler, Pod alt ağından küme yığınlarından dinamik olarak ayrılır. Bu, kümedeki IP 'lerin geleneksel CNı çözümüne kıyasla, her düğüm için IP 'lerin statik olarak ayrılmasını sağlayan, daha iyi bir şekilde kullanımına yol açar.  

* **Ölçeklenebilir ve esnek**: düğüm ve pod alt ağları bağımsız olarak ölçeklendirilebilir. Tek bir pod alt ağı, bir kümenin birden çok düğüm havuzunda veya aynı VNet 'te dağıtılan birden çok AKS kümesi arasında paylaşılabilir. Ayrıca, bir düğüm havuzu için ayrı bir pod alt ağı da yapılandırabilirsiniz.  

* **Yüksek performans**: Pod sanal ağ IP 'leri atandığından, sanal ağ üzerindeki diğer küme Pod ve kaynaklarına doğrudan bağlantı sağlar. Çözüm, performansı azalmadan çok büyük kümeleri destekler.

* **Pod için ayrı VNET ilkeleri**: bir alt ağa sahip olduğundan, düğüm ilkelerinden farklı olan ayrı VNET ilkeleri yapılandırabilirsiniz. Bu, yalnızca düğümler için internet bağlantısına izin verme, VNet ağ NAT kullanarak düğüm havuzunda pod için kaynak IP 'yi düzeltme ve düğüm havuzları arasındaki trafiği filtrelemek için NSG 'ler kullanma gibi birçok yararlı senaryoyu sağlar.  

* **Kubernetes ağ ilkeleri**: hem Azure ağ ilkeleri hem de Calıco bu yeni çözümle çalışır.  

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview`Azure CLI 'yı yükler

*Aks-Preview* Azure CLI uzantısına ihtiyacınız olacaktır. [Az Extension Add][az-extension-add] komutunu kullanarak *aks-Preview* Azure CLI uzantısını yükler. Veya [az Extension Update][az-extension-update] komutunu kullanarak kullanılabilir tüm güncelleştirmeleri yükler.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>`PodSubnetPreview`Önizleme özelliğini kaydetme

Özelliği kullanmak için `PodSubnetPreview` aboneliğinizdeki Özellik bayrağını da etkinleştirmeniz gerekir.

`PodSubnetPreview`Aşağıdaki örnekte gösterildiği gibi, [az Feature Register][az-feature-register] komutunu kullanarak özellik bayrağını kaydedin:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

Durumun *kayıtlı* gösterilmesi birkaç dakika sürer. [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu doğrulayın:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak *Microsoft. Containerservice* kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="additional-prerequisites"></a>Ek önkoşullar

Azure CNı için zaten listelenen Önkoşullar hala geçerlidir ancak bazı ek sınırlamalar vardır:

* Yalnızca Linux düğüm kümeleri ve düğüm havuzları desteklenir.
* AKS altyapısı ve DIY kümeleri desteklenmez.

### <a name="planning-ip-addressing"></a>IP adresini planlama

Bu özellik kullanılırken, planlama çok daha basittir. Düğümler ve düğüm birbirinden bağımsız olarak ölçeklendirildiğinden, adres alanları ayrı olarak da planlanabilecek. Pod alt ağları bir düğüm havuzunun ayrıntı düzeyine yapılandırılabileceğinizden, müşteriler düğüm havuzu eklerken her zaman yeni bir alt ağ ekleyebilirler. Bir küme/düğüm havuzundaki sistem IP 'Leri Pod alt ağından de IP alır, bu nedenle bu davranışın için de hesap oluşturulması gerekir.

K8S Hizmetleri ve Docker Köprüsü için IP planlaması değişmeden kalır.

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>IP 'lerin ve gelişmiş alt ağ desteğinin dinamik olarak ayrılması ile bir kümede düğüm başına maksimum Pod

IP 'lerin dinamik olarak ayrılması ile Azure CNı kullanılırken düğüm başına düşen düğüm değerleri geleneksel CNı davranışından biraz değişmiş:

|SEÇENEĞIYLE CNı kullanan|Dağıtım yöntemi|Varsayılan|Dağıtımda yapılandırılabilir|
|--|--| :--: |--|
|Geleneksel Azure CNı|Azure CLI|30|Evet (250 'e kadar)|
|IP 'lerin dinamik ayırması ile Azure CNı|Azure CLI|250|Evet (250 'e kadar)|

Pod başına en fazla düğüm yapılandırması ile ilgili diğer tüm rehberlik aynı kalır.

### <a name="additional-deployment-parameters"></a>Ek dağıtım parametreleri

Yukarıda açıklanan dağıtım parametreleri, tek bir özel durumla hala geçerlidir:

* **Alt ağ** parametresi artık kümenin düğümleriyle ilgili alt ağa başvurur.
* IP adresleri dinamik olarak pods 'ye ayrılabilen alt ağı belirtmek için, bir ek parametre **Pod alt ağı** kullanılır.

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Ağ iletişimi-CLı 'yı, IP 'lerin ve gelişmiş alt ağ desteğinin dinamik ayırması ile yapılandırma

Kümenizde IP 'lerin dinamik olarak ayrılması ve gelişmiş alt ağ desteğinin kullanılması, bir küme Azure CNı yapılandırma için varsayılan yönteme benzerdir. Aşağıdaki örnek, düğümler için bir alt ağ ile yeni bir sanal ağ oluşturma ve IP 'lerin dinamik olarak ayrılması ve gelişmiş alt ağ desteği içeren Azure CNı kullanan bir küme oluşturmak için İzlenecek yol gösterir. Değişkenlerini kendi değerlerinizle değiştirdiğinizden emin olun `$subscription` :

İlk olarak, sanal ağı iki alt ağ ile oluşturun:

```azurecli-interactive
$resourceGroup="myResourceGroup"
$vnet="myVirtualNetwork"

# Create our two subnet network 
az network vnet create -g $rg --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

Daha sonra, kullanarak düğüm alt ağına `--vnet-subnet-id` ve pod alt ağını kullanarak bir küme oluşturun `--pod-subnet-id` :

```azurecli-interactive
$clusterName="myAKSCluster"
$location="eastus"
$subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location --max-pods 250 --node-count 2 --network-plugin azure --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>Düğüm havuzu ekleniyor

Düğüm havuzu eklerken, kullanarak düğüm alt ağına `--vnet-subnet-id` ve pod alt ağını başvuru yapın `--pod-subnet-id` . Aşağıdaki örnek, yeni bir düğüm havuzunun oluşturulmasında başvurulan iki yeni alt ağ oluşturur:

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newNodepool --max-pods 250 --node-count 2 --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet --no-wait 
```

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

Aşağıdaki sorular ve yanıtlar **Azure CNI** ağ yapılandırması için geçerlidir.

* *Küme alt ağından VM 'Leri dağıtabilir miyim?*

  Evet.

* *Dış sistemler hangi kaynak IP olarak etkindir bir Azure CNı özellikli Pod 'da kaynaklı trafik için bkz?*

  AKS kümesiyle aynı sanal ağdaki sistemler, Pod 'un herhangi bir trafiği için kaynak adresi olarak Pod IP 'si ' ne bakın. AKS kümesi sanal ağı dışındaki sistemler, Pod 'un herhangi bir trafiği için kaynak adresi olarak düğüm IP 'sini inceleyin.

* *Pod başına ağ ilkelerini yapılandırabilir miyim?*

  Evet, Kubernetes ağ ilkesi AKS 'de kullanılabilir. Başlamak için bkz. [aks 'de ağ ilkelerini kullanarak Pod arasındaki trafiği güvenli hale getirme][network-policy].

* *Yapılandırılabilir bir düğüme dağıtılabilir maksimum düğüm sayısı.*

  Evet, Azure CLı veya Kaynak Yöneticisi şablonuyla bir küme dağıtırken. Bkz. [düğüm başına maksimum Pod](#maximum-pods-per-node).

  Mevcut bir kümede düğüm başına en fazla düğüm sayısını değiştiremezsiniz.

* *AKS kümesi oluşturma sırasında oluşturduğum alt ağ için ek özellikler yapılandırma Nasıl yaparım? misiniz? Örneğin, hizmet uç noktaları.*

  AKS kümesi oluşturma sırasında oluşturduğunuz sanal ağ ve alt ağların özelliklerinin tamamı listesi, Azure portal standart sanal ağ yapılandırması sayfasında yapılandırılabilir.

* **Kubernetes hizmeti adres aralığı** *için küme sanal ağı 'nda farklı bir alt ağ kullanabilir miyim* ?

  Önerilmez, ancak bu yapılandırma mümkündür. Hizmet adres aralığı, Kubernetes 'in kümenizdeki iç hizmetlere atadığı bir sanal IP (VIP) kümesidir. Azure ağ Iletişimi, Kubernetes kümesinin hizmet IP aralığında görünürlüğe sahip değildir. Kümenin hizmet adres aralığında görünürlük olmaması nedeniyle, küme sanal ağında hizmet adresi aralığıyla örtüşen yeni bir alt ağ oluşturmak mümkündür. Bu tür bir çakışma oluşursa Kubernetes, bir hizmet için alt ağdaki başka bir kaynak tarafından zaten kullanımda olan bir IP atayabilir ve öngörülemeyen davranışlara veya hatalara neden olur. Kümenin sanal ağı dışında bir adres aralığı kullanmanızı sağlayarak bu çakışma riskini önleyebilirsiniz.

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>IP adreslerinin dinamik ayırması ve gelişmiş alt ağ desteği SSS

Aşağıdaki sorular ve yanıtlar, **IP adreslerinin dinamik olarak ayrılması ve gelişmiş alt ağ desteğinin kullanılması sırasında Azure CNI ağ yapılandırması** için geçerlidir.

* *Bir küme/düğüm havuzuna birden çok Pod alt ağı atayabilir miyim?*

  Bir kümeye veya düğüm havuzuna yalnızca bir alt ağ atanabilir. Ancak, birden çok küme veya düğüm havuzu tek bir alt ağ paylaşabilir.

* *Farklı bir VNet 'ten Pod alt ağlarını tamamen atayabilir miyim?*

  Pod alt ağı, kümeyle aynı VNet 'ten olmalıdır.  

* *Bir kümedeki bazı düğüm havuzlarının geleneksel CNı kullanabilmesi, diğerleri ise yeni CNı kullanıyor mu?*

  Tüm küme yalnızca bir CNı türü kullanmalıdır.

## <a name="aks-engine"></a>AKS Engine

[Azure Kubernetes hizmet altyapısı (AKS motoru)][aks-engine] , Azure 'Da Kubernetes kümelerini dağıtmak için kullanabileceğiniz Azure Resource Manager şablonlar üreten bir açık kaynaklı projem projesidir.

Aks motoru ile oluşturulan Kubernetes kümeleri hem [Kubernetes kullanan][kubenet] hem de [Azure CNI][cni-networking] eklentilerini destekler. Bu nedenle, her iki ağ senaryosu da AKS altyapısı tarafından desteklenir.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde AKS 'de ağ oluşturma hakkında daha fazla bilgi edinin:

* [Azure Kubernetes Service (AKS) yük dengeleyicisiyle bir statik IP adresi kullanın](static-ip.md)
* [Azure Container Service (AKS) ile iç yük dengeleyici kullanma](internal-lb.md)

* [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
* [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
* [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
* [Dinamik bir genel IP ile giriş denetleyicisi oluşturun ve otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi yapılandırın][aks-ingress-tls]
* [Statik bir genel IP ile giriş denetleyicisi oluşturun ve otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi yapılandırın][aks-ingress-static-tls]
<!-- IMAGES -->
[Gelişmiş-ağ-diyagram-01]:./Media/Networking-Overview/advanced-networking-diagram-01.png [Portal-01-Networking-Advanced]:./Media/Networking-Overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md

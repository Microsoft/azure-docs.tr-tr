---
title: Azure Kubernetes hizmetinde (AKS) birden çok düğüm havuzu kullanma
description: Azure Kubernetes Service (AKS) ' de bir küme için birden çok düğüm havuzu oluşturma ve yönetme hakkında bilgi edinin
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: b7b54ccf6662e172ebfe95a84189df5e8e6e990f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832257"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ile bir küme için birden çok düğüm havuzu oluşturma ve yönetme

Azure Kubernetes hizmeti 'nde (AKS), aynı yapılandırmanın düğümleri *düğüm havuzlarında* birlikte gruplandırılır. Bu düğüm havuzları, uygulamalarınızı çalıştıran temel VM 'Leri içerir. Bir [sistem düğüm havuzu][use-system-pool]oluşturan bir aks kümesi oluşturduğunuzda, ilk düğüm sayısı ve boyutu (SKU) tanımlanmıştır. Farklı işlem veya depolama taleplerine sahip uygulamaları desteklemek için ek *Kullanıcı düğümü havuzları* oluşturabilirsiniz. Sistem düğüm havuzları, CoreDNS ve tunnelfront gibi kritik sistem yığınlarını barındırmanın birincil amacını sunar. Kullanıcı düğümü havuzları, uygulama yığınlarınızı barındırmanın birincil amacını sunar. Ancak, aks kümenizde yalnızca bir havuza sahip olmak istiyorsanız, uygulama Pod 'leri sistem düğüm havuzlarında zamanlanabilir. Kullanıcı düğümü havuzları, uygulamaya özgü yığınlarınızı yerleştirdiğiniz yerdir. Örneğin, işlem yoğunluklu uygulamalar için GPU veya yüksek performanslı SSD depolamaya erişim sağlamak için bu ek Kullanıcı düğümü havuzlarını kullanın.

> [!NOTE]
> Bu özellik, birden çok düğüm havuzunun oluşturulması ve yönetilmesi üzerinde daha yüksek denetim sağlar. Sonuç olarak, oluşturma/güncelleştirme/silme için ayrı komutlar gerekir. Daha önce `az aks create` `az aks update` , MANAGEDCLUSTER API 'yi aracılığıyla veya kullanarak işlem yapın ve denetim düzlemini ve tek bir düğüm havuzunu değiştirmek için tek seçenektir. Bu özellik, agentPool API 'SI aracılığıyla aracı havuzları için ayarlanan ayrı bir işlem sunar ve `az aks nodepool` tek bir düğüm havuzunda işlemleri yürütmek için komut kümesinin kullanılmasını gerektirir.

Bu makalede bir AKS kümesinde birden çok düğüm havuzu oluşturma ve yönetme konusu gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Azure CLı sürüm 2.2.0 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="limitations"></a>Sınırlamalar

Birden çok düğüm havuzunu destekleyen AKS kümelerini oluşturup yönetirken aşağıdaki sınırlamalar geçerlidir:

* [Azure Kubernetes Service (AKS) Içindeki kotalar, sanal makine boyutu kısıtlamaları ve bölge kullanılabilirliği][quotas-skus-regions]konusuna bakın.
* Sistem düğüm havuzlarını, kendi AKS kümesindeki yerini almak için başka bir sistem düğüm havuzunuz olması şartıyla silebilirsiniz.
* Sistem havuzlarının en az bir düğüm içermesi ve Kullanıcı düğüm havuzlarının sıfır veya daha fazla düğüm içermesi gerekir.
* AKS kümesi birden çok düğüm havuzu kullanmak için standart SKU yük dengeleyiciyi kullanmalıdır, özellik temel SKU yük dengeleyicilerle desteklenmez.
* AKS kümesinin düğümlerin sanal makine ölçek kümelerini kullanması gerekir.
* Düğüm havuzunun adı yalnızca küçük harfli alfasayısal karakterler içerebilir ve küçük harfle başlamalıdır. Linux düğüm havuzları için uzunluk 1 ile 12 karakter arasında olmalıdır, Windows düğüm havuzları için uzunluk 1 ile 6 karakter arasında olmalıdır.
* Tüm düğüm havuzları aynı sanal ağda bulunmalıdır.
* Küme oluşturma zamanında birden çok düğüm havuzu oluştururken, düğüm havuzları tarafından kullanılan tüm Kubernetes sürümlerinin denetim düzlemi için ayarlanan sürüm kümesiyle eşleşmesi gerekir. Bu, küme, düğüm başına havuz işlemleri kullanılarak sağlandıktan sonra güncelleştirilebilen olabilir.

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

> [!Important]
> Bir üretim ortamında AKS kümeniz için tek bir sistem düğüm havuzu çalıştırırsanız, düğüm havuzu için en az üç düğüm kullanmanızı öneririz.

Başlamak için, tek düğümlü havuz ile bir AKS kümesi oluşturun. Aşağıdaki örnek, *eastus* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturmak için [az Group Create][az-group-create] komutunu kullanır. *Myakscluster* adlı bir aks kümesi daha sonra [az aks Create][az-aks-create] komutu kullanılarak oluşturulur.

> [!NOTE]
> Birden çok düğüm havuzu kullanılırken *temel* yük dengeleyici SKU 'su **desteklenmez** . Varsayılan olarak, AKS kümeleri, Azure CLı ve Azure portal *Standart* yük dengeleyici SKU 'su ile oluşturulur.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

Kümenin oluşturulması birkaç dakika sürer.

> [!NOTE]
> Kümenizin güvenilir bir şekilde çalışmasını sağlamak için, bu düğüm havuzunda önemli sistem hizmetleri çalıştığı için varsayılan düğüm havuzunda en az 2 (iki) düğüm çalıştırmalısınız.

Küme kullanıma hazırsa, şu ile kullanılacak küme kimlik bilgilerini almak için [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanın `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Düğüm havuzu ekleme

Önceki adımda oluşturulan kümenin tek bir düğüm havuzu vardır. [Az aks nodepool Add][az-aks-nodepool-add] komutunu kullanarak ikinci bir düğüm havuzu ekleyelim. Aşağıdaki örnek *3* düğümleri çalıştıran *mynodepool* adlı bir düğüm havuzu oluşturur:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> Düğüm havuzunun adı küçük harfle başlamalı ve yalnızca alfasayısal karakterler içerebilir. Linux düğüm havuzları için uzunluk 1 ile 12 karakter arasında olmalıdır, Windows düğüm havuzları için uzunluk 1 ile 6 karakter arasında olmalıdır.

Düğüm havuzlarınızın durumunu görmek için [az aks node Pool List][az-aks-nodepool-list] komutunu kullanın ve kaynak grubunuzu ve küme adınızı belirtin:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Aşağıdaki örnek çıktı, düğüm havuzundaki üç düğüm ile *mynodepool* başarıyla oluşturulduğunu gösterir. Önceki adımda AKS kümesi oluşturulduğunda, düğüm sayısı *2* olan varsayılan bir *nodepool1* oluşturulmuştur.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Bir düğüm havuzu eklediğinizde hiçbir *VMSize* belirtilmemişse, varsayılan boyut Windows düğüm havuzları için *Standard_D2s_v3* ve Linux düğüm havuzları için *Standard_DS2_v2* . Bir *Orchestratorversion* belirtilmemişse, varsayılan olarak denetim düzlemi ile aynı sürüme ayarlanır.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Benzersiz alt ağa sahip bir düğüm havuzu ekleme (Önizleme)

Bir iş yükü, kümenin düğümlerini mantıksal yalıtım için ayrı havuzlara bölmeyi gerektirebilir. Bu yalıtım, kümedeki her düğüm havuzuna ayrılmış ayrı alt ağlarda desteklenebilir. Bu, düğüm havuzlarına bölünecek bitişik olmayan sanal ağ adres alanı gibi gereksinimleri ele alabilir.

#### <a name="limitations"></a>Sınırlamalar

* Nodepools öğesine atanan tüm alt ağlar aynı sanal ağa ait olmalıdır.
* DNS çözümlemesi ve tünel kubectl günlükleri/exec/bağlantı noktası-iletme proxy 'si gibi kritik işlevler sağlamak için, sistem kimliklerinin kümedeki tüm düğümlere/yığınlara erişimi olmalıdır.
* Kümeyi oluşturduktan sonra VNET 'i genişletirseniz, özgün CIDR dışında bir alt ağ eklemeden önce kümenizi güncelleştirmeniz gerekir (yönetilen clster işlemi gerçekleştirin ancak düğüm havuzu işlemleri sayılmaz). Bu durumda, ilk olarak izin verdiğimiz halde, aracı havuzunda hata dışarı çıkar. Küme dosyanızı bir destek bileti ile nasıl mutabık kılınabileceğinizi bilmiyorsanız. 
* Calıco ağ Ilkesi desteklenmiyor. 
* Azure ağ Ilkesi desteklenmiyor.
* Kuin-proxy, tek bir bitişik CIDR bekler ve bunu üç optmizations için kullanır. Bu [K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) bakın ve--daha fazla bilgi için [burada](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) CIDR. Azure CNI 'de, ilk düğüm havuzunuzun alt ağı Kube-proxy ' ye verilecek. 

Ayrılmış bir alt ağa sahip bir düğüm havuzu oluşturmak için, düğüm havuzu oluştururken alt ağ kaynak KIMLIĞINI ek bir parametre olarak geçirin.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Düğüm havuzunu yükseltme

> [!NOTE]
> Bir küme veya düğüm havuzundaki yükseltme ve ölçeklendirme işlemleri, bir hata döndürülürse, aynı anda gerçekleşemez. Bunun yerine, her işlem türünün aynı kaynaktaki bir sonraki istekten önce hedef kaynakta tamamlaması gerekir. [Sorun giderme kılavuzumuzdan](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)bu konuda daha fazla bilgi edinin.

Bu bölümdeki komutlar, tek bir belirli düğüm havuzunun nasıl yükseltileceğini açıklamaktadır. Denetim düzlemi ve düğüm havuzunun Kubernetes sürümünü yükseltme arasındaki ilişki [aşağıdaki bölümde](#upgrade-a-cluster-control-plane-with-multiple-node-pools)açıklanmıştır.

> [!NOTE]
> Düğüm havuzu işletim sistemi görüntüsü sürümü, kümenin Kubernetes sürümüne bağlıdır. Yalnızca bir küme yükseltmesini izleyerek işletim sistemi görüntüsü yükseltmelerini alacaksınız.

Bu örnekte iki düğüm havuzu olduğundan, bir düğüm havuzunu yükseltmek için [az aks nodepool Upgrade][az-aks-nodepool-upgrade] kullanmanız gerekir. Kullanılabilir yükseltmeleri görmek için [az aks Get-yükseltmeleri][az-aks-get-upgrades] kullanın

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

*Mynodepool*'yi yükseltelim. Aşağıdaki örnekte gösterildiği gibi, düğüm havuzunu yükseltmek için [az aks nodepool Upgrade][az-aks-nodepool-upgrade] komutunu kullanın:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

[Az aks düğüm havuzu listesi][az-aks-nodepool-list] komutunu kullanarak düğüm havuzlarınızın durumunu yeniden listeleyin. Aşağıdaki örnek, *mynodepool* *KUBERNETES_VERSION* için *yükseltme* durumunda olduğunu gösterir:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Düğümlerin belirtilen sürüme yükseltilmesi birkaç dakika sürer.

En iyi uygulama olarak, bir AKS kümesindeki tüm düğüm havuzlarını aynı Kubernetes sürümüne yükseltmeniz gerekir. Varsayılan davranışı, `az aks upgrade` Bu hizalamayı başarmak için tüm düğüm havuzlarını denetim düzlemesiyle birlikte yükseltmekte. Tek tek düğüm havuzlarını yükseltebilme özelliği, yukarıdaki kısıtlamalar dahilinde uygulama çalışma süresini korumak için sıralı yükseltme gerçekleştirmenize ve düğüm havuzları arasında dizin zamanlamauygulamanıza olanak tanır.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Birden çok düğümlü havuzlarla küme denetim düzlemi 'ni yükseltme

> [!NOTE]
> Kubernetes, standart [anlamsal sürüm](https://semver.org/) oluşturma düzenini kullanır. Sürüm numarası *x. y. z* olarak ifade edilir; burada *x* ana sürümdür, *y* ise ikincil sürümdür ve *z* , yama sürümüdür. Örneğin, sürüm *1.12.6*' de, 1 ana sürümdür, 12 ise ikincil sürümdür ve 6 Düzeltme Eki sürümüdür. Denetim düzlemi ve ilk düğüm havuzunun Kubernetes sürümü küme oluşturma sırasında ayarlanır. Tüm ek düğüm havuzlarının, kümeye eklendiğinde Kubernetes sürümü ayarlanmış olmalıdır. Kubernetes sürümleri, düğüm havuzlarının yanı sıra düğüm havuzu ile denetim düzlemi arasında farklılık gösterebilir.

AKS kümesi, Kubernetes sürümleriyle ilişkili iki küme kaynak nesnesine sahiptir.

1. Küme denetim düzlemi Kubernetes sürümü.
2. Kubernetes sürümüne sahip bir düğüm Havuzu.

Denetim düzlemi bir veya daha fazla düğüm havuzlarıyla eşlenir. Bir yükseltme işleminin davranışı, hangi Azure CLı komutunun kullanıldığına bağlıdır.

AKS denetim düzleminin yükseltilmesi için kullanılması gerekir `az aks upgrade` . Bu komut, denetim düzlemi sürümünü ve kümedeki tüm düğüm havuzlarını yükseltir.

`az aks upgrade`Komutun `--control-plane-only` bayrağıyla verilmesi yalnızca küme denetim düzlemini yükseltir. Kümedeki ilişkili düğüm havuzlarının hiçbiri değiştirilmez.

Tek tek düğüm havuzlarının yükseltilmesi için kullanılması gerekir `az aks nodepool upgrade` . Bu komut yalnızca hedef düğüm havuzunu belirtilen Kubernetes sürümüyle yükseltir

### <a name="validation-rules-for-upgrades"></a>Yükseltmeler için doğrulama kuralları

Bir kümenin denetim düzlemi ve düğüm havuzları için geçerli Kubernetes yükseltmeleri, aşağıdaki kural kümeleri tarafından onaylanır.

* Düğüm havuzlarını yükseltmek için geçerli sürümlerin kuralları:
   * Düğüm havuzu sürümü, denetim düzlemi ile aynı *ana* sürüme sahip olmalıdır.
   * Düğüm havuzu *İkincil* sürümü, denetim düzlemi *sürümünün iki alt sürümü içinde* olmalıdır.
   * Düğüm havuzu sürümü, denetim sürümünden daha büyük olamaz `major.minor.patch` .

* Yükseltme işlemi gönderme kuralları:
   * Denetim düzlemi veya düğüm havuzu Kubernetes sürümü indirgeyemezsiniz.
   * Düğüm havuzu Kubernetes sürümü belirtilmemişse, davranış kullanılan istemciye bağlıdır. Kaynak Yöneticisi şablonlarındaki bildirim, kullanılıyorsa düğüm havuzu için tanımlanan mevcut sürüme geri döner, yoksa, hiçbir değer ayarlanmamışsa denetim düzlemi sürümü kullanılır.
   * Belirli bir zamanda bir denetim düzlemi veya düğüm havuzu yükseltebilir ya da ölçeklendirebilirsiniz, tek bir denetim düzlemine veya düğüm havuzu kaynağına aynı anda birden çok işlem gönderemezsiniz.

## <a name="scale-a-node-pool-manually"></a>Düğüm havuzunu el ile ölçeklendirme

Uygulama iş yükünüz değiştikçe, bir düğüm havuzundaki düğüm sayısını ölçeklendirmeniz gerekebilir. Düğüm sayısı yukarı veya aşağı ölçeklendirilebilirler.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Düğüm havuzundaki düğüm sayısını ölçeklendirmek için [az aks node Pool Scale][az-aks-nodepool-scale] komutunu kullanın. Aşağıdaki örnek, *mynodepool* içindeki düğüm sayısını *5* olarak ölçeklendirir:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

[Az aks düğüm havuzu listesi][az-aks-nodepool-list] komutunu kullanarak düğüm havuzlarınızın durumunu yeniden listeleyin. Aşağıdaki örnek, *mynodepool* 'in yeni sayısı *5* düğüm olan *ölçekleme* durumunda olduğunu gösterir:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Ölçek işleminin tamamlanabilmesi birkaç dakika sürer.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Küme otomatik Scaler 'ı etkinleştirerek belirli bir düğüm havuzunu otomatik olarak ölçeklendirin

AKS, düğüm havuzlarını [küme](cluster-autoscaler.md)otomatik olarak ölçeklendirme adlı bir özellik ile otomatik olarak ölçeklendirmeye yönelik ayrı bir özellik sunar. Bu özellik, düğüm havuzu başına benzersiz ve en fazla ölçek sayısı olan düğüm havuzu başına etkinleştirilebilir. [Düğüm havuzu başına küme otomatik Scaler 'ı kullanmayı](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)öğrenin.

## <a name="delete-a-node-pool"></a>Düğüm havuzunu silme

Artık havuz gerekmiyorsa, onu silebilir ve temel alınan VM düğümlerini kaldırabilirsiniz. Bir düğüm havuzunu silmek için [az aks node Pool Delete][az-aks-nodepool-delete] komutunu kullanın ve düğüm havuzu adını belirtin. Aşağıdaki örnekte, önceki adımlarda oluşturulan *mynoodepool* silinir:

> [!CAUTION]
> Bir düğüm havuzunu sildiğinizde oluşabilecek veri kaybı için kurtarma seçeneği yoktur. Diğer düğüm havuzlarında Eğer Pod zamanlanamaz, bu uygulamalar kullanılamaz. Kullanımda olan uygulamalarda veri yedeklemeleri olmadığında veya kümenizdeki diğer düğüm havuzlarında çalıştırma yeteneğine sahip olmayan bir düğüm havuzunu silmemenizi unutmayın.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

[Az aks düğüm havuzu List][az-aks-nodepool-list] komutundan alınan aşağıdaki örnek çıktı, *mynodepool* 'in *silme* durumunda olduğunu gösterir:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Düğümlerin ve düğüm havuzunun silinmesi birkaç dakika sürer.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Düğüm havuzu için VM boyutu belirtme

Önceki örneklerde, bir düğüm havuzu oluşturmak için, kümede oluşturulan düğümler için varsayılan bir VM boyutu kullanıldı. Daha yaygın bir senaryo, farklı VM boyutları ve özellikleri olan düğüm havuzları oluşturmanıza yöneliktir. Örneğin, büyük miktarlarda CPU veya belleğe sahip düğümleri veya GPU desteği sağlayan bir düğüm havuzunu içeren düğüm havuzu oluşturabilirsiniz. Sonraki adımda, Kubernetes Scheduler 'a bu düğümlerde çalışabilecek düğüm erişimini nasıl sınırlayacağınızı bildirmek için [tatları ve toleransı kullanırsınız](#setting-nodepool-taints) .

Aşağıdaki örnekte, *Standard_NC6* VM boyutunu kullanan GPU tabanlı bir düğüm havuzu oluşturun. Bu VM 'Ler NVıDıA Tesla K80 kartı tarafından desteklenir. Kullanılabilir VM boyutları hakkında daha fazla bilgi için bkz. [Azure 'Da Linux sanal makineleri Için boyutlar][vm-sizes].

[Az aks düğüm havuzu Add][az-aks-nodepool-add] komutunu yeniden kullanarak bir düğüm havuzu oluşturun. Bu kez, *gpunodepool* adını belirtin ve `--node-vm-size` *Standard_NC6* boyutunu belirtmek için parametresini kullanın:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

[Az aks düğüm havuzu List][az-aks-nodepool-list] komutundan aşağıdaki örnek çıktı, *Gpunodepool* belirtilen *VMSize* sahip düğümleri *oluşturuyor* olduğunu gösterir:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

*Gpunodepool* 'nin başarıyla oluşturulması birkaç dakika sürer.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Düğüm havuzu için bir taınt, etiket veya etiket belirtme

Düğüm havuzu oluştururken, bu düğüm havuzuna litre, Etiketler veya Etiketler ekleyebilirsiniz. Bir taınt, etiket veya etiket eklediğinizde, bu düğüm havuzundaki tüm düğümler o taınt, etiket veya etiketi de alır.

> [!IMPORTANT]
> Düğümler, Etiketler ya da Etiketler, kullanarak tüm düğüm havuzu için yapılmalıdır `az aks nodepool` . Kullanarak düğüm havuzundaki tek tek düğümlere litre, lablels veya Etiketler uygulamak `kubectl` önerilmez.  

### <a name="setting-nodepool-taints"></a>Nodepool izleri ayarlama

Taint ile bir düğüm havuzu oluşturmak için [az aks nodepool Add][az-aks-nodepool-add]kullanın. *Container TNP* adını belirtin ve bu parametreyi, `--node-taints` Taint için *SKU = GPU: NoSchedule* belirtmek üzere kullanın.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Bir Taint, düğüm havuzu oluşturma sırasında yalnızca düğüm havuzları için ayarlanabilir.

[Az aks nodepool List][az-aks-nodepool-list] komutundan alınan aşağıdaki örnek çıktı, *Container TNP* *'nin belirtilen hatalarla* düğüm *oluşturmasını* göstermektedir:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

Taint bilgileri, düğümlerin zamanlama kurallarını işlemek için Kubernetes içinde görülebilir. Kubernetes Zamanlayıcı, düğümlerde hangi iş yüklerinin çalıştırılacağını kısıtlamak için tatları ve toleranları kullanabilir.

* Yalnızca belirli yığınların zamanlanabileceğini gösteren bir düğüme bir **taınt** uygulanır.
* Daha sonra bir **tolerans** , düğümün Taint *'e kabul* etmesine izin veren bir pod öğesine uygulanır.

Gelişmiş Kubernetes zamanlanmış özelliklerini kullanma hakkında daha fazla bilgi için bkz. [AKS 'de gelişmiş Zamanlayıcı özellikleri Için en iyi yöntemler][taints-tolerations]

Önceki adımda, düğüm havuzunuzu oluştururken *SKU = GPU: NoSchedule* Taint ' i uyguladınız. Aşağıdaki temel örnek YAML bildirimi, Kubernetes Scheduler ' ın bu düğüm havuzundaki bir düğümde NGıNX Pod çalıştırmasına izin vermek için bir toleranation kullanır.

Adlı bir dosya oluşturun `nginx-toleration.yaml` ve aşağıdaki örnekteki YAML 'yi kopyalayın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Şu komutu kullanarak Pod 'u zamanlayın `kubectl apply -f nginx-toleration.yaml` :

```console
kubectl apply -f nginx-toleration.yaml
```

Pod 'u zamanlamak ve NGıNX görüntüsünü çekmek birkaç saniye sürer. Pod durumunu görüntülemek için [kubectl betimleyen Pod][kubectl-describe] komutunu kullanın. Aşağıdaki sıkıştırılmış örnek çıktı *SKU = GPU: NoSchedule* toleranation ' ı gösterir. Olaylar bölümünde Zamanlayıcı, Pod 'yi *aks-Container TNP-28993262-vmss000000* düğümüne atamıştır:

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

Yalnızca bu toleranlama uygulanmış olan FID 'ler, *Container* of 'lar içindeki düğümlerde zamanlanabilir. Diğer Pod 'lar *nodepool1* düğüm havuzunda zamanlanır. Ek düğüm havuzları oluşturursanız, bu düğüm kaynakları üzerinde hangi yığınların zamanlanabileceği ile ilgili ek litre ve toleransyonlar kullanabilirsiniz.

### <a name="setting-nodepool-labels"></a>Nodepool etiketlerini ayarlama

Düğüm havuzu oluşturma sırasında düğüm havuzuna de etiket ekleyebilirsiniz. Düğüm havuzunda ayarlanan Etiketler düğüm havuzundaki her bir düğüme eklenir. Bu Etiketler, düğümlerin zamanlama kurallarını işlemek için [Kubernetes içinde görülebilir][kubernetes-labels] .

Etiketli bir düğüm havuzu oluşturmak için [az aks nodepool Add][az-aks-nodepool-add]kullanın. *Labelnp* adını belirtin ve `--labels` Bu parametreyi kullanarak *Bölüm = It* ve *costcenter = 9999* etiketlerini belirtin.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Etiket, düğüm havuzu oluşturma sırasında yalnızca düğüm havuzları için ayarlanabilir. Etiketler Ayrıca bir anahtar/değer çifti olmalıdır ve [geçerli bir sözdizimine][kubernetes-label-syntax]sahip olmalıdır.

[Az aks nodepool List][az-aks-nodepool-list] komutundan alınan aşağıdaki örnek çıktı, *labelnp* 'in belirtilen *nodelabels* düğümleri *oluşturmasını* göstermektedir:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Nodepool Azure etiketlerini ayarlama

AKS kümenizdeki düğüm havuzlarına bir Azure etiketi uygulayabilirsiniz. Düğüm havuzuna uygulanan etiketler, düğüm havuzu içindeki her bir düğüme uygulanır ve yükseltmeler aracılığıyla kalıcı hale getirilir. Etiketler, genişleme işlemleri sırasında düğüm havuzuna eklenen yeni düğümlere de uygulanır. Etiket eklemek, ilke izleme veya maliyet tahmini gibi görevlerle yardımcı olabilir.

Azure etiketlerinde, anahtar ararken bir etiketi alırken olduğu gibi işlemler için büyük/küçük harfe duyarsız olan anahtarlar vardır. Bu durumda, verilen anahtara sahip bir etiket, büyük/küçük harfe bakılmaksızın güncelleştirilir veya alınacaktır. Etiket değerleri büyük/küçük harfe duyarlıdır.

AKS 'de, birden fazla etiket aynı anahtarlarla, ancak büyük/küçük harflere sahip olarak ayarlandıysa, kullanılan etiket ilk alfabetik sırada kullanılır. Örneğin, `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` `Key1` ile sonuçlanır ve `val1` ayarlanır.

[Az aks nodepool Add][az-aks-nodepool-add]' i kullanarak bir düğüm havuzu oluşturun. *Tagnodepool* adını belirtin ve `--tag` Bu parametreyi kullanarak, Etiketler için *Bölüm = It* ve *costcenter = 9999* parametresini belirtin.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Ayrıca, `--tags` [az aks nodepool Update][az-aks-nodepool-update] komutunu kullanırken ve küme oluşturma sırasında parametresini de kullanabilirsiniz. Küme oluşturma sırasında parametresi, `--tags` kümeyle oluşturulan ilk düğüm havuzuna etiketi uygular. Tüm etiket adları, [Azure kaynaklarınızı düzenlemek Için kullanılan etiketlerle][tag-limitation]ilgili sınırlamalara uymalıdır. Bir düğüm havuzunu parametresiyle güncelleştirmek, `--tags` var olan tüm etiket değerlerini güncelleştirir ve yeni Etiketler ekler. Örneğin, düğüm havuzunuzun *Bölüm = It* ve *costcenter = 9999* etiketleri varsa ve bunu *Team = dev* ve *costcenter = 111* ile GÜNCELLEŞTIRDIYSENIZ, Etiketler için nodepool, *Bölüm = It*, *costcenter = 111* ve *Team = dev* olur.

[Az aks nodepool List][az-aks-nodepool-list] komutundan aşağıdaki örnek çıktı, *tagnodepool* belirtilen *etikete* sahip düğümleri *oluşturuyor* olduğunu gösterir:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak düğüm havuzlarını yönetme

Kaynakları oluşturmak ve yönetmek için bir Azure Resource Manager şablonu kullandığınızda, genellikle şablonunuzda ayarları güncelleştirebilir ve kaynağı güncelleştirmek için yeniden dağıtabilirsiniz. AKS içindeki düğüm havuzlarıyla, AKS kümesi oluşturulduktan sonra ilk düğüm havuzu profili güncelleştirilemiyor. Bu davranış, mevcut bir Kaynak Yöneticisi şablonunu güncelleştiremeyeceğiniz, düğüm havuzlarında değişiklik yapamayacağı ve yeniden dağımeyeceğiniz anlamına gelir. Bunun yerine, yalnızca var olan bir AKS kümesi için düğüm havuzlarını güncelleştiren ayrı bir Kaynak Yöneticisi şablonu oluşturmanız gerekir.

Gibi bir şablon oluşturun `aks-agentpools.json` ve aşağıdaki örnek bildirimi yapıştırın. Bu örnek şablon aşağıdaki ayarları yapılandırır:

* *Myagentpool* adlı *Linux* düğüm havuzunu üç düğüm çalıştıracak şekilde güncelleştirir.
* Düğüm havuzundaki düğümleri Kubernetes sürüm *1.15.7* çalıştıracak şekilde ayarlar.
* Düğüm boyutunu *Standard_DS2_v2* olarak tanımlar.

Gerektiğinde düğüm havuzlarını güncelleştirme, ekleme veya silme gereksinimi olarak bu değerleri düzenleyin:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Bu şablonu, aşağıdaki örnekte gösterildiği gibi [az Deployment Group Create][az-deployment-group-create] komutunu kullanarak dağıtın. Mevcut AKS kümesi adı ve konumu sorulur:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Aşağıdaki örnekte gösterildiği gibi, *Tag* özelliğini şablona ekleyerek düğüm havuzunuza bir etiket ekleyebilirsiniz.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Kaynak Yöneticisi şablonunuzda tanımladığınız düğüm havuzu ayarlarına ve işlemlerine bağlı olarak AKS kümenizin güncelleştirilmesi birkaç dakika sürebilir.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>Düğüm havuzlarınız için düğüm başına genel IP atama

AKS düğümleri iletişim için kendi genel IP adreslerini gerektirmez. Ancak senaryolar, düğüm havuzundaki düğümlerin kendi adanmış genel IP adreslerini almasını gerektirebilir. Yaygın bir senaryo, bir konsolun, atlamaları en aza indirmek için bir bulut sanal makinesine doğrudan bağlantı kurmak için ihtiyaç duyacağı oyun iş yükleri içindir. Bu senaryoya, düğüm genel IP 'si kullanılarak AKS üzerinde ulaşılabilir.

İlk olarak, yeni bir kaynak grubu oluşturun.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Yeni bir AKS kümesi oluşturun ve düğümleriniz için genel bir IP ekleyin. Düğüm havuzundaki düğümlerin her biri benzersiz bir genel IP alır. Bunu, sanal makine ölçek kümesi örneklerine bakarak doğrulayabilirsiniz.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Mevcut AKS kümelerinde Ayrıca yeni bir düğüm havuzu ekleyebilir ve düğümleriniz için genel bir IP ekleyebilirsiniz.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>Genel IP öneki kullanın

[Genel IP öneki kullanmanın çeşitli avantajları][public-ip-prefix-benefits]vardır. AKS, `node-public-ip-prefix` Yeni bir küme oluştururken veya düğüm havuzu eklerken, kaynak kimliğini bayrağıyla geçirerek, düğümleriniz için mevcut bir genel IP önekinden adresler kullanmayı destekler.

İlk olarak, [az Network PUBLIC-IP önekini][az-public-ip-prefix-create]kullanarak genel bir IP öneki oluşturun:

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

Çıktıyı görüntüleyin ve öneki için şunu göz önüne alın `id` :

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

Son olarak, yeni bir küme oluştururken veya yeni bir düğüm havuzu eklerken, bayrağı kullanın `node-public-ip-prefix` ve öneki kaynak kimliği ' ni geçirin:

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>Düğümler için genel IP 'Leri bulma

Düğümleriniz için genel IP 'Leri çeşitli yollarla bulabilirsiniz:

* [Az VMSS List-instance-public-IP][az-list-ips]Azure CLI komutunu kullanın.
* [PowerShell veya bash komutlarını][vmss-commands]kullanın. 
* Ayrıca, sanal makine ölçek kümesindeki örnekleri görüntüleyerek Azure portal genel IP 'Leri görüntüleyebilirsiniz.

> [!Important]
> [Düğüm kaynak grubu][node-resource-group] , düğümleri ve bunların genel IP 'lerini içerir. Düğümlerinizin genel IP 'lerini bulmak için komutları yürütürken düğüm kaynak grubunu kullanın.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede, GPU tabanlı düğümleri içeren bir AKS kümesi oluşturdunuz. Gereksiz maliyeti azaltmak için, *gpunodepool* veya tüm aks kümesini silmek isteyebilirsiniz.

GPU tabanlı düğüm havuzunu silmek için, aşağıdaki örnekte gösterildiği gibi [az aks nodepool Delete][az-aks-nodepool-delete] komutunu kullanın:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Kümeyi silmek için, AKS kaynak grubunu silmek için [az Group Delete][az-group-delete] komutunu kullanın:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

Ayrıca, düğüm havuzları için genel IP için oluşturduğunuz ek kümeyi silebilirsiniz.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Sonraki adımlar

[Sistem düğüm havuzları][use-system-pool]hakkında daha fazla bilgi edinin.

Bu makalede, bir AKS kümesinde birden çok düğüm havuzu oluşturmayı ve yönetmeyi öğrendiniz. Düğüm havuzlarının tamamında nasıl denetim yapılacağı hakkında daha fazla bilgi için bkz. [AKS 'de gelişmiş Zamanlayıcı özellikleri Için en iyi yöntemler][operator-best-practices-advanced-scheduler].

Windows Server kapsayıcısı düğüm havuzlarını oluşturmak ve kullanmak için bkz. [AKS 'de Windows Server kapsayıcısı oluşturma][aks-windows].

AKS uygulamalarınızın gecikmesini azaltmak için [yakınlık yerleştirme gruplarını][reduce-latency-ppg] kullanın.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_update
[az-group-create]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create
[az-group-delete]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete
[az-deployment-group-create]: /cli/azure/deployment/group?view=azure-cli-latest&preserve-view=true#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest&preserve-view=true#az_vmss_list_instance_public_ips
[reduce-latency-ppg]: reduce-latency-ppg.md
[public-ip-prefix-benefits]: ../virtual-network/public-ip-address-prefix.md#why-create-a-public-ip-address-prefix
[az-public-ip-prefix-create]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest&preserve-view=true#az_network_public_ip_prefix_create

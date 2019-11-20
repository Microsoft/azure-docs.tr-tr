---
title: Azure Kubernetes hizmetinde (AKS) birden çok düğüm havuzu kullanma
description: Azure Kubernetes Service (AKS) ' de bir küme için birden çok düğüm havuzu oluşturma ve yönetme hakkında bilgi edinin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: 9c8bae879c5e28914981eec34afb0759dd963004
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928987"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' de bir küme için birden çok düğüm havuzu oluşturma ve yönetme

Azure Kubernetes hizmeti 'nde (AKS), aynı yapılandırmanın düğümleri *düğüm havuzlarında*birlikte gruplandırılır. Bu düğüm havuzları, uygulamalarınızı çalıştıran temel VM 'Leri içerir. Bir *varsayılan düğüm havuzu*oluşturan bir aks kümesi oluşturduğunuzda, ilk düğüm sayısı ve boyutu (SKU) tanımlanmıştır. Farklı işlem veya depolama taleplerine sahip uygulamaları desteklemek için ek düğüm havuzları oluşturabilirsiniz. Örneğin, işlem yoğunluklu uygulamalar için GPU 'Lar sağlamak veya yüksek performanslı SSD depolamaya erişmek için bu ek düğüm havuzlarını kullanın.

> [!NOTE]
> Bu özellik, birden çok düğüm havuzunun oluşturulması ve yönetilmesi üzerinde daha yüksek denetim sağlar. Sonuç olarak, oluşturma/güncelleştirme/silme için ayrı komutlar gerekir. Daha önce `az aks create` veya `az aks update` aracılığıyla küme işlemleri managedCluster API 'sini kullandı ve denetim düzlemini ve tek bir düğüm havuzunu değiştirmek için tek seçenektir. Bu özellik, agentPool API aracılığıyla aracı havuzları için ayarlanan ayrı bir işlem sunar ve tek bir düğüm havuzunda işlemleri yürütmek için `az aks nodepool` komutunun kullanılmasını gerektirir.

Bu makalede bir AKS kümesinde birden çok düğüm havuzu oluşturma ve yönetme konusu gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Azure CLı sürüm 2.0.76 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][install-azure-cli].

## <a name="limitations"></a>Sınırlamalar

Birden çok düğüm havuzunu destekleyen AKS kümelerini oluşturup yönetirken aşağıdaki sınırlamalar geçerlidir:

* Varsayılan (ilk) düğüm havuzunu silemezsiniz.
* HTTP uygulama yönlendirme eklentisi kullanılamıyor.
* AKS kümesi birden çok düğüm havuzu kullanmak için standart SKU yük dengeleyiciyi kullanmalıdır, özellik temel SKU yük dengeleyicilerle desteklenmez.
* AKS kümesinin düğümlerin sanal makine ölçek kümelerini kullanması gerekir.
* Birçok işlem ile olduğu gibi mevcut bir Kaynak Yöneticisi şablonu kullanarak düğüm havuzları ekleyemez veya silemezsiniz. Bunun yerine, bir AKS kümesindeki düğüm havuzlarında değişiklik yapmak için [ayrı bir kaynak yöneticisi şablonu kullanın](#manage-node-pools-using-a-resource-manager-template) .
* Düğüm havuzunun adı yalnızca küçük harfli alfasayısal karakterler içerebilir ve küçük harfle başlamalıdır. Linux düğüm havuzları için uzunluk 1 ile 12 karakter arasında olmalıdır, Windows düğüm havuzları için uzunluk 1 ile 6 karakter arasında olmalıdır.
* AKS kümesinde en fazla sekiz düğüm havuzu olabilir.
* AKS kümesi, bu sekiz düğüm havuzunda en fazla 400 düğüme sahip olabilir.
* Tüm düğüm havuzları aynı alt ağda bulunmalıdır.

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

Başlamak için, tek düğümlü havuz ile bir AKS kümesi oluşturun. Aşağıdaki örnek, *eastus* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturmak için [az Group Create][az-group-create] komutunu kullanır. *Myakscluster* adlı bir aks kümesi daha sonra [az aks Create][az-aks-create] komutu kullanılarak oluşturulur. Bir *--Kubernetes-* *1.13.10* , aşağıdaki adımlarda bir düğüm havuzunun nasıl güncelleştiğine göstermek için kullanılır. [Desteklenen Kubernetes sürümünü][supported-versions]belirtebilirsiniz.

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
    --kubernetes-version 1.13.10 \
    --load-balancer-sku standard
```

Kümenin oluşturulması birkaç dakika sürer.

> [!NOTE]
> Kümenizin güvenilir bir şekilde çalışmasını sağlamak için, bu düğüm havuzunda önemli sistem hizmetleri çalıştığı için varsayılan düğüm havuzunda en az 2 (iki) düğüm çalıştırmalısınız.

Küme hazırlanıyor, `kubectl`ile kullanmak üzere küme kimlik bilgilerini almak için [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanın:

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
    --node-count 3 \
    --kubernetes-version 1.12.7
```

> [!NOTE]
> Düğüm havuzunun adı küçük harfle başlamalı ve yalnızca alfasayısal karakterler içerebilir. Linux düğüm havuzları için uzunluk 1 ile 12 karakter arasında olmalıdır, Windows düğüm havuzları için uzunluk 1 ile 6 karakter arasında olmalıdır.

Düğüm havuzlarınızın durumunu görmek için [az aks node Pool List][az-aks-nodepool-list] komutunu kullanın ve kaynak grubunuzu ve küme adınızı belirtin:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Aşağıdaki örnek çıktı, düğüm havuzundaki üç düğüm ile *mynodepool* başarıyla oluşturulduğunu gösterir. Önceki adımda AKS kümesi oluşturulduğunda, düğüm sayısı *2*olan varsayılan bir *nodepool1* oluşturulmuştur.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Bir düğüm havuzu eklediğinizde bir *Orchestratorversion* veya *VMSize* belirtilmemişse düğümler, aks kümesinin varsayılan değerleri temel alınarak oluşturulur. Bu örnekte, Kubernetes sürüm *1.13.10* ve *Standard_DS2_v2*düğüm boyutudur.

## <a name="upgrade-a-node-pool"></a>Düğüm havuzunu yükseltme
 
> [!NOTE]
> Bir küme veya düğüm havuzundaki yükseltme ve ölçeklendirme işlemleri, bir hata döndürülürse, aynı anda gerçekleşemez. Bunun yerine, her işlem türünün aynı kaynaktaki bir sonraki istekten önce hedef kaynakta tamamlaması gerekir. [Sorun giderme kılavuzumuzdan](https://aka.ms/aks-pending-upgrade)bu konuda daha fazla bilgi edinin.

AKS kümeniz ilk adımda ilk kez oluşturulduğunda, bir *1.13.10* `--kubernetes-version` belirtildi. Bu, Kubernetes sürümünü hem denetim düzlemi hem de varsayılan düğüm havuzu için ayarlar. Bu bölümdeki komutlar, tek bir belirli düğüm havuzunun nasıl yükseltileceğini açıklamaktadır.

Denetim düzlemi ve düğüm havuzunun Kubernetes sürümünü yükseltme arasındaki ilişki [aşağıdaki bölümde](#upgrade-a-cluster-control-plane-with-multiple-node-pools)açıklanmıştır.

> [!NOTE]
> Düğüm havuzu işletim sistemi görüntüsü sürümü, kümenin Kubernetes sürümüne bağlıdır. Yalnızca bir küme yükseltmesini izleyerek işletim sistemi görüntüsü yükseltmelerini alacaksınız.

Bu örnekte iki düğüm havuzu olduğundan, bir düğüm havuzunu yükseltmek için [az aks nodepool Upgrade][az-aks-nodepool-upgrade] kullanmanız gerekir. *Mynodepool* , Kubernetes *1.13.10*'e yükseltelim. Aşağıdaki örnekte gösterildiği gibi, düğüm havuzunu yükseltmek için [az aks nodepool Upgrade][az-aks-nodepool-upgrade] komutunu kullanın:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

[Az aks düğüm havuzu listesi][az-aks-nodepool-list] komutunu kullanarak düğüm havuzlarınızın durumunu yeniden listeleyin. Aşağıdaki örnek, *mynodepool* 'in *yükseltme* durumunda olduğunu *gösterir:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Düğümlerin belirtilen sürüme yükseltilmesi birkaç dakika sürer.

En iyi uygulama olarak, bir AKS kümesindeki tüm düğüm havuzlarını aynı Kubernetes sürümüne yükseltmeniz gerekir. `az aks upgrade` varsayılan davranışı, bu hizalamayı başarmak için tüm düğüm havuzlarını denetim düzlemesiyle birlikte yükseltmekte. Tek tek düğüm havuzlarını yükseltebilme özelliği, yukarıdaki kısıtlamalar dahilinde uygulama çalışma süresini korumak için sıralı yükseltme gerçekleştirmenize ve düğüm havuzları arasında dizin zamanlamauygulamanıza olanak tanır.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Birden çok düğümlü havuzlarla küme denetim düzlemi 'ni yükseltme

> [!NOTE]
> Kubernetes, standart [anlamsal sürüm](https://semver.org/) oluşturma düzenini kullanır. Sürüm numarası *x. y. z*olarak ifade edilir; burada *x* ana sürümdür, *y* ise ikincil sürümdür ve *z* , yama sürümüdür. Örneğin, sürüm *1.12.6*' de, 1 ana sürümdür, 12 ise ikincil sürümdür ve 6 Düzeltme Eki sürümüdür. Denetim düzlemi ve ilk düğüm havuzunun Kubernetes sürümü küme oluşturma sırasında ayarlanır. Tüm ek düğüm havuzlarının, kümeye eklendiğinde Kubernetes sürümü ayarlanmış olmalıdır. Kubernetes sürümleri, düğüm havuzlarının yanı sıra düğüm havuzu ile denetim düzlemi arasında farklılık gösterebilir.

AKS kümesi, Kubernetes sürümleriyle ilişkili iki küme kaynak nesnesine sahiptir.

1. Küme denetim düzlemi Kubernetes sürümü.
2. Kubernetes sürümüne sahip bir düğüm Havuzu.

Denetim düzlemi bir veya daha fazla düğüm havuzlarıyla eşlenir. Bir yükseltme işleminin davranışı, hangi Azure CLı komutunun kullanıldığına bağlıdır.

AKS denetim düzlemi 'nin yükseltilmesi için `az aks upgrade`kullanılması gerekir. Bu, denetim düzlemi sürümünü ve kümedeki tüm düğüm havuzlarını yükseltir. 

`--control-plane-only` bayrağıyla `az aks upgrade` komutu verilmesi yalnızca küme denetim düzlemini yükseltir. Kümedeki ilişkili düğüm havuzlarının hiçbiri değiştirilmez.

Tek tek düğüm havuzlarının yükseltilmesi için `az aks nodepool upgrade`kullanılması gerekir. Bu, yalnızca belirtilen Kubernetes sürümü ile hedef düğüm havuzunu yükseltir

### <a name="validation-rules-for-upgrades"></a>Yükseltmeler için doğrulama kuralları

Bir kümenin denetim düzlemi veya düğüm havuzları tarafından tutulan Kubernetes sürümlerinin geçerli yükseltmeleri, aşağıdaki kural kümeleri tarafından onaylanır.

* Yükseltilecek geçerli sürümlerin kuralları:
   * Düğüm havuzu sürümü, denetim düzlemi ile aynı *ana* sürüme sahip olmalıdır.
   * Düğüm havuzu sürümü, denetim düzlemi sürümünden daha *az iki alt sürüm olabilir* .
   * Düğüm havuzu sürümü, denetim düzlemi sürümünden daha az iki *yama* sürümü olabilir.

* Yükseltme işlemi gönderme kuralları:
   * Denetim düzlemi veya düğüm havuzu Kubernetes sürümü indirgeyemezsiniz.
   * Düğüm havuzu Kubernetes sürümü belirtilmemişse, davranış kullanılan istemciye bağlıdır. Kaynak Yöneticisi şablonlarındaki bildirim, kullanılıyorsa düğüm havuzu için tanımlanan mevcut sürüme geri döner, yoksa, hiçbir değer ayarlanmamışsa denetim düzlemi sürümü kullanılır.
   * Belirli bir zamanda bir denetim düzlemi veya düğüm havuzu yükseltebilir ya da ölçeklendirebilirsiniz, tek bir denetim düzlemine veya düğüm havuzu kaynağına aynı anda birden çok işlem gönderemezsiniz.

## <a name="scale-a-node-pool-manually"></a>Düğüm havuzunu el ile ölçeklendirme

Uygulama iş yükünüz değiştikçe, bir düğüm havuzundaki düğüm sayısını ölçeklendirmeniz gerekebilir. Düğüm sayısı yukarı veya aşağı ölçeklendirilebilirler.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Düğüm havuzundaki düğüm sayısını ölçeklendirmek için [az aks node Pool Scale][az-aks-nodepool-scale] komutunu kullanın. Aşağıdaki örnek, *mynodepool* içindeki düğüm sayısını *5*olarak ölçeklendirir:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

[Az aks düğüm havuzu listesi][az-aks-nodepool-list] komutunu kullanarak düğüm havuzlarınızın durumunu yeniden listeleyin. Aşağıdaki örnek, *mynodepool* 'in yeni sayısı *5* düğüm olan *ölçekleme* durumunda olduğunu gösterir:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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

Önceki örneklerde, bir düğüm havuzu oluşturmak için, kümede oluşturulan düğümler için varsayılan bir VM boyutu kullanıldı. Daha yaygın bir senaryo, farklı VM boyutları ve özellikleri olan düğüm havuzları oluşturmanıza yöneliktir. Örneğin, büyük miktarlarda CPU veya belleğe sahip düğümleri veya GPU desteği sağlayan bir düğüm havuzunu içeren düğüm havuzu oluşturabilirsiniz. Sonraki adımda, Kubernetes Scheduler 'a bu düğümlerde çalışabilecek düğüm erişimini nasıl sınırlayacağınızı bildirmek için [tatları ve toleransı kullanırsınız](#schedule-pods-using-taints-and-tolerations) .

Aşağıdaki örnekte, *Standard_NC6* VM boyutunu kullanan GPU tabanlı bir düğüm havuzu oluşturun. Bu VM 'Ler NVıDıA Tesla K80 kartı tarafından desteklenir. Kullanılabilir VM boyutları hakkında daha fazla bilgi için bkz. [Azure 'Da Linux sanal makineleri Için boyutlar][vm-sizes].

[Az aks düğüm havuzu Add][az-aks-nodepool-add] komutunu yeniden kullanarak bir düğüm havuzu oluşturun. Bu kez, *gpunodepool*adını belirtin ve *Standard_NC6* boyutunu belirtmek için `--node-vm-size` parametresini kullanın:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

[Az aks düğüm havuzu List][az-aks-nodepool-list] komutundan aşağıdaki örnek çıktı, *Gpunodepool* belirtilen *VMSize*sahip düğümleri *oluşturuyor* olduğunu gösterir:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

*Gpunodepool* 'nin başarıyla oluşturulması birkaç dakika sürer.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Talara ve toleranları kullanarak Pod zamanlama

Artık kümenizde iki düğüm havuzu vardır; başlangıçta oluşturulan varsayılan düğüm havuzu ve GPU tabanlı düğüm Havuzu. Kümenizdeki düğümleri görüntülemek için [kubectl Get Nodes][kubectl-get] komutunu kullanın. Aşağıdaki örnek çıktı, düğümleri göstermektedir:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.10
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.10
```

Kubernetes Zamanlayıcı, düğümlerde hangi iş yüklerinin çalıştırılacağını kısıtlamak için tatları ve toleranları kullanabilir.

* Yalnızca belirli yığınların zamanlanabileceğini gösteren bir düğüme bir **taınt** uygulanır.
* Daha sonra bir **tolerans** , düğümün Taint *'e kabul* etmesine izin veren bir pod öğesine uygulanır.

Gelişmiş Kubernetes zamanlanmış özelliklerini kullanma hakkında daha fazla bilgi için bkz. [AKS 'de gelişmiş Zamanlayıcı özellikleri Için en iyi yöntemler][taints-tolerations]

Bu örnekte, [kubectl taınt node][kubectl-taint] komutunu kullanarak GPU tabanlı düğümünüz için bir Taint uygulayın. Önceki `kubectl get nodes` komutunun çıktısından GPU tabanlı düğümünüz adını belirtin. Taınt bir anahtar olarak uygulanır *: değer* ve ardından zamanlama seçeneği. Aşağıdaki örnek *SKU = GPU* çiftini kullanır ve pod 'yi tanımlar, aksi takdirde *NoSchedule* özelliğine sahiptir:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Aşağıdaki temel örnek YAML bildirimi, Kubernetes Scheduler 'ın GPU tabanlı düğümde bir NGıNX Pod çalıştırmasına izin vermek için bir tolerans kullanır. Daha uygun olan, ancak veri kümesine karşı bir TensorFlow işi çalıştırmak için yoğun zaman tüketen bir örnek için bkz. [AKS üzerinde işlem yoğunluğu yoğun iş yükleri Için GPU 'Ları kullanma][gpu-cluster].

`gpu-toleration.yaml` adlı bir dosya oluşturun ve aşağıdaki örnekte bulunan YAML 'yi kopyalayın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
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

`kubectl apply -f gpu-toleration.yaml` komutunu kullanarak Pod 'u zamanlayın:

```console
kubectl apply -f gpu-toleration.yaml
```

Pod 'u zamanlamak ve NGıNX görüntüsünü çekmek birkaç saniye sürer. Pod durumunu görüntülemek için [kubectl betimleyen Pod][kubectl-describe] komutunu kullanın. Aşağıdaki sıkıştırılmış örnek çıktı *SKU = GPU: NoSchedule* toleranation ' ı gösterir. Olaylar bölümünde Zamanlayıcı, Pod 'yi *aks-gpunodepool-28993262-vmss000000* GPU tabanlı düğüme atamıştır:

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Yalnızca bu taınt uygulanmış olan bir düğüm, *gpunodepool*içindeki düğümlerde zamanlanabilir. Diğer Pod 'lar *nodepool1* düğüm havuzunda zamanlanır. Ek düğüm havuzları oluşturursanız, bu düğüm kaynakları üzerinde hangi yığınların zamanlanabileceği ile ilgili ek litre ve toleransyonlar kullanabilirsiniz.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak düğüm havuzlarını yönetme

Kaynakları oluşturmak ve yönetmek için bir Azure Resource Manager şablonu kullandığınızda, genellikle şablonunuzda ayarları güncelleştirebilir ve kaynağı güncelleştirmek için yeniden dağıtabilirsiniz. AKS içindeki düğüm havuzlarıyla, AKS kümesi oluşturulduktan sonra ilk düğüm havuzu profili güncelleştirilemiyor. Bu davranış, mevcut bir Kaynak Yöneticisi şablonunu güncelleştiremeyeceğiniz, düğüm havuzlarında değişiklik yapamayacağı ve yeniden dağımeyeceğiniz anlamına gelir. Bunun yerine, yalnızca var olan bir AKS kümesi için düğüm havuzlarını güncelleştiren ayrı bir Kaynak Yöneticisi şablonu oluşturmanız gerekir.

`aks-agentpools.json` gibi bir şablon oluşturun ve aşağıdaki örnek bildirimi yapıştırın. Bu örnek şablon aşağıdaki ayarları yapılandırır:

* *Myagentpool* adlı *Linux* düğüm havuzunu üç düğüm çalıştıracak şekilde güncelleştirir.
* Düğüm havuzundaki düğümleri Kubernetes sürüm *1.13.10*çalıştıracak şekilde ayarlar.
* Düğüm boyutunu *Standard_DS2_v2*olarak tanımlar.

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
            "aks": "2019-04-01"
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
            "apiVersion": "2019-04-01",
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
                "orchestratorVersion": "1.13.10"
            }
        }
    ]
}
```

Bu şablonu, aşağıdaki örnekte gösterildiği gibi [az Group Deployment Create][az-group-deployment-create] komutunu kullanarak dağıtın. Mevcut AKS kümesi adı ve konumu sorulur:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Kaynak Yöneticisi şablonunuzda tanımladığınız düğüm havuzu ayarlarına ve işlemlerine bağlı olarak AKS kümenizin güncelleştirilmesi birkaç dakika sürebilir.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Düğüm havuzunda düğüm başına genel IP atama

> [!WARNING]
> Düğüm başına genel IP atama önizlemesi sırasında, sanal makine sağlama ile çakışan olası yük dengeleyici kuralları nedeniyle *AKS 'de standart Load Balancer SKU 'su* ile kullanılamaz. Önizleme aşamasında, düğüm başına genel IP atamanız gerekiyorsa *temel Load Balancer SKU* 'sunu kullanmanız gerekir.

AKS düğümleri iletişim için kendi genel IP adreslerini gerektirmez. Ancak bazı senaryolar, düğüm havuzundaki düğümlerin kendi genel IP adreslerine sahip olmasını gerektirebilir. Örneğin, bir konsolun, atlamaları en aza indirmek için bir bulut sanal makinesine doğrudan bağlantı kurmak için gereken oyun. Bu, ayrı bir önizleme özelliği olan düğüm genel IP (Önizleme) için kaydolarak elde edilebilir.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Kayıt başarılı olduktan sonra, [Yukarıdaki](#manage-node-pools-using-a-resource-manager-template) şekilde aynı yönergelerden sonra bir Azure Resource Manager şablonu dağıtın ve `enableNodePublicIP`, agentPoolProfiles öğesine Boole değer özelliği ekleyin. Değeri varsayılan olarak `true` olarak ayarlayın, belirtilmemişse `false` olarak ayarlanır. Bu yalnızca bir oluşturma zamanı özelliğidir ve en düşük API sürümü olan 2019-06-01 gerektirir. Bu, hem Linux hem de Windows düğüm havuzlarına uygulanabilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede, GPU tabanlı düğümleri içeren bir AKS kümesi oluşturdunuz. Gereksiz maliyeti azaltmak için, *gpunodepool*veya tüm aks kümesini silmek isteyebilirsiniz.

GPU tabanlı düğüm havuzunu silmek için, aşağıdaki örnekte gösterildiği gibi [az aks nodepool Delete][az-aks-nodepool-delete] komutunu kullanın:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Kümeyi silmek için, AKS kaynak grubunu silmek için [az Group Delete][az-group-delete] komutunu kullanın:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir AKS kümesinde birden çok düğüm havuzu oluşturmayı ve yönetmeyi öğrendiniz. Düğüm havuzlarının tamamında nasıl denetim yapılacağı hakkında daha fazla bilgi için bkz. [AKS 'de gelişmiş Zamanlayıcı özellikleri Için en iyi yöntemler][operator-best-practices-advanced-scheduler].

Windows Server kapsayıcısı düğüm havuzlarını oluşturmak ve kullanmak için bkz. [AKS 'de Windows Server kapsayıcısı oluşturma][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

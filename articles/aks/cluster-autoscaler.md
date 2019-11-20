---
title: Azure Kubernetes Service (AKS) içindeki küme otomatik Scaler 'ı kullanma
description: Bir Azure Kubernetes Service (AKS) kümesindeki uygulama taleplerini karşılamak üzere kümenizi otomatik olarak ölçeklendirmek için küme otomatik olarak nasıl kullanacağınızı öğrenin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 8ce5d2965d0127eec01620c702d7d83bd0b39416
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885772"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) üzerinde uygulama taleplerini karşılamak için bir kümeyi otomatik olarak ölçeklendirme

Azure Kubernetes Service 'te (AKS) uygulama taleplerine devam etmek için, iş yüklerinizi çalıştıran düğümlerin sayısını ayarlamanız gerekebilir. Küme otomatik Scaler bileşeni, kümenizde kaynak kısıtlamaları nedeniyle zamanlanabilecek Pod 'leri izleyebilir. Sorunlar algılandığında, bir düğüm havuzundaki düğümlerin sayısı uygulama talebini karşılayacak şekilde artmıştır. Düğümler, düğüm sayısıyla daha sonra gerektiği şekilde azaldıkça, bir yük eksikliği olmaması için düzenli olarak kontrol edilir. AKS kümenizdeki düğüm sayısını otomatik olarak ölçeklendirme veya azaltma yeteneği, verimli ve ekonomik bir küme çalıştırmanızı sağlar.

Bu makalede, bir AKS kümesinde Küme otomatik olarak nasıl etkinleştirileceği ve yönetileceği gösterilmektedir. 

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure CLı sürüm 2.0.76 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

## <a name="limitations"></a>Sınırlamalar

Küme otomatik Scaler 'ı kullanan AKS kümelerini oluşturup yönetirken aşağıdaki sınırlamalar geçerlidir:

* HTTP uygulama yönlendirme eklentisi kullanılamıyor.

## <a name="about-the-cluster-autoscaler"></a>Küme otomatik yüklemesi hakkında

Workday ve akşam ya da bir hafta sonu arasındaki uygulama taleplerini değiştirmek üzere ayarlamak için kümeler genellikle otomatik olarak ölçeklendirilmesi için bir yönteme ihtiyaç duyar. AKS kümeleri, iki şekilde ölçeklendirebilir:

* **Küme otomatik yüklemesi** , kaynak kısıtlamaları nedeniyle düğümlerde zamanlanabilecek düğüm sayısını izler. Küme daha sonra düğümlerin sayısını otomatik olarak arttırır.
* **Yatay Pod otomatik Scaler** , Kubernetes kümesinde ölçüm sunucusunu kullanarak pods 'nin kaynak talebini izler. Bir uygulamanın daha fazla kaynağa ihtiyacı varsa, yığınların sayısı talebi karşılamak üzere otomatik olarak artar.

![Küme otomatik olarak ve yatay Pod otomatik Scaler, gerekli uygulama taleplerini desteklemek için genellikle birlikte çalışır](media/autoscaler/cluster-autoscaler.png)

Hem yatay Pod otomatik Scaler hem de Cluster otomatik Scaler, gereken düğüm sayısını ve düğümleri de azaltabilir. Küme otomatik yüklemesi, bir süre için kullanılmayan kapasiteden fazla düğüm sayısını düşürür. Küme otomatik olarak kaldırılacak bir düğümdeki düğüm, kümede başka bir yerde güvenle zamanlanır. Küme otomatik olarak, aşağıdaki durumlarda olduğu gibi, Pod taşınmadığı takdirde ölçeği ölçeklendiremeyebilir:

* Doğrudan oluşturulup oluşturulan bir pod, bir dağıtım veya çoğaltma kümesi gibi bir denetleyici nesnesi tarafından yedeklenmez.
* Pod kesinti bütçesi (pdb) çok kısıtlayıcıdır ve pod sayısının belirli bir eşiğin altına düşmeye izin vermez.
* Pod, farklı bir düğümde zamanlanırsa, düğüm seçicileri veya benzeşim önleme kullanır.

Küme otomatik olarak ölçeklendirilmesine nasıl ölçeklenebileceğinize ilişkin daha fazla bilgi için bkz [. küme otomatik Scaler 'ın bir düğümü kaldırmasını engelleyebilir][autoscaler-scaledown]

Küme otomatik yüklemesi, ölçek olayları ve kaynak eşikleri arasındaki zaman aralıkları gibi şeyler için başlangıç parametrelerini kullanır. Bu parametreler Azure platformu tarafından tanımlanır ve şu anda ayarlamanız için sunulmamaktadır. Kümenin otomatik olarak kullandığı parametreler hakkında daha fazla bilgi için bkz. [küme otomatik Scaler parametreleri nedir?][autoscaler-parameters].

Küme ve yatay Pod otomatik scalers birlikte çalışabilir ve genellikle bir kümede dağıtılır. Birleştirildiğinde, yatay Pod otomatik Scaler, uygulama talebini karşılamak için gereken sayıda Pod çalıştırmaya odaklanılmıştır. Küme otomatik yüklemesi, zamanlanmış pods 'yi desteklemek için gereken düğüm sayısını çalıştırmaya odaklanır.

> [!NOTE]
> Küme otomatik ölçeklendirme kullandığınızda el ile ölçekleme devre dışıdır. Küme otomatik olarak gerekli düğüm sayısını belirlemesine izin verin. Kümenizi el ile ölçeklendirmek isterseniz, [küme otomatik Scaler ' ı devre dışı bırakın](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS kümesi oluşturma ve küme otomatik Scaler 'ı etkinleştirme

AKS kümesi oluşturmanız gerekiyorsa, [az aks Create][az-aks-create] komutunu kullanın. Küme için düğüm havuzunda küme otomatik listesini etkinleştirmek ve yapılandırmak için *--Enable-Cluster-otomatik Scaler* parametresini kullanın ve bir düğüm belirtin *--Min-Count* ve *--Max-Count*.

> [!IMPORTANT]
> Küme otomatik yüklemesi, bir Kubernetes bileşenidir. AKS kümesi düğümler için bir sanal makine ölçek kümesi kullansa da, Azure portal ölçek kümesi otomatik ölçek ayarlarını el ile etkinleştirmeyin veya Azure CLı kullanarak ayarları düzenleyin. Kubernetes kümesi otomatik olarak gerekli ölçek ayarlarını yönetmesine izin verin. Daha fazla bilgi için bkz. [düğüm kaynak grubundaki AKS kaynaklarını değiştirebilir miyim?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

Aşağıdaki örnek, bir sanal makine ölçek kümesi tarafından desteklenen tek düğümlü havuz içeren bir AKS kümesi oluşturur. Ayrıca küme için düğüm havuzunda küme otomatik Scaler 'ı ve en az *1* ve en fazla *3* düğüm ayarlar:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Kümeyi oluşturmak ve küme otomatik Scaler ayarlarını yapılandırmak birkaç dakika sürer.

## <a name="change-the-cluster-autoscaler-settings"></a>Küme otomatik Scaler ayarlarını değiştirme

> [!IMPORTANT]
> AKS kümenizde birden fazla düğüm havuzunuz varsa, [birden çok aracı havuzu ile otomatik ölçeklendirmeyi](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)atlayın. Birden çok aracı havuzu içeren kümeler, `az aks`yerine düğüm havuzuna özgü özellikleri değiştirmek için `az aks nodepool` komut kümesinin kullanılmasını gerektirir.

Önceki adımda, bir AKS kümesi oluşturmak veya var olan bir düğüm havuzunu güncelleştirmek için, küme otomatik algılama en düşük düğüm sayısı *1*olarak ayarlanmıştır ve en fazla düğüm sayısı *3*olarak ayarlanmıştır. Uygulamanız değiştikçe değişiklik yaparken, küme otomatik Scaler düğüm sayısını ayarlamanız gerekebilir.

Düğüm sayısını değiştirmek için [az aks Update][az-aks-update] komutunu kullanın.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Yukarıdaki örnek, *Myakscluster* içindeki tek düğümlü havuzda küme otomatik Scaler ' nı en az *1* ve en fazla *5* düğüm olarak güncelleştirir.

> [!NOTE]
> Düğüm havuzu için şu anda ayarlanmış olandan daha yüksek bir düğüm sayısı ayarlayamazsınız. Örneğin, şu anda en az *1*olarak ayarlandıysa, en az sayıyı *3*olarak güncelleştiremezsiniz.

Uygulamalarınızın ve hizmetlerinizin performansını izleyin ve küme otomatik Scaler düğüm sayılarını gerekli performansla eşleşecek şekilde ayarlayın.

## <a name="disable-the-cluster-autoscaler"></a>Küme otomatik Scaler 'ı devre dışı bırakma

Artık küme otomatik özelliğini kullanmak istemiyorsanız, *--Disable-Cluster-otomatik Scaler* parametresini belirterek [az aks Update][az-aks-update] komutunu kullanarak devre dışı bırakabilirsiniz. Küme otomatik yüklemesi devre dışı bırakıldığında düğümler kaldırılmaz.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

[Az aks Scale][az-aks-scale] komutunu kullanarak küme otomatik Scaler 'ı devre dışı bıraktıktan sonra kümenizi el ile ölçekleyebilirsiniz. Yatay Pod otomatik Scaler ' ı kullanırsanız, bu özellik küme otomatik olarak devre dışı bırakılmış olarak çalışmaya devam eder, ancak tüm düğüm kaynakları kullanımda ise, Pod çalışmayabilir.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Devre dışı bırakılan bir kümeyi otomatik olarak yeniden etkinleştirin

Küme otomatik olarak var olan bir kümede yeniden etkinleştirmek istiyorsanız, [az aks Update][az-aks-update] komutunu kullanarak, *--Enable-Cluster-otomatik Scaler*, *--Min-Count*ve *--Max-Count* parametrelerini belirterek yeniden etkinleştirebilirsiniz.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Küme otomatik gizleme günlüklerini ve durumunu alma

Otomatik Scaler olaylarını tanılamak ve hatalarını ayıklamak için, Günlükler ve durum otomatik Scaler eklentisi 'nden alınabilir.

AKS, küme otomatik denetimini sizin adınıza yönetir ve yönetilen denetim düzlemine çalıştırır. Ana düğüm günlüklerinin bir sonuç olarak görüntülenmek üzere yapılandırılması gerekir.

Günlüklerin küme otomatik olarak gönderildiği bir şekilde yapılandırılması için Log Analytics aşağıdaki adımları izleyin.

1. Küme-otomatik Scaler günlüklerini Log Analytics 'e göndermek için tanılama günlükleri için bir kural ayarlayın. [Yönergeler burada ayrıntılı olarak verilmiştir](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs), "Logs" seçeneklerini seçerken `cluster-autoscaler` kutusunu kontrol edin.
1. Azure portal aracılığıyla kümenizdeki "Günlükler" bölümüne tıklayın.
1. Aşağıdaki örnek sorguyu Log Analytics olarak girin:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Alınacak Günlükler olduğu sürece, aşağıdakine benzer Günlükler görmeniz gerekir.

![Log Analytics günlükleri](media/autoscaler/autoscaler-logs.png)

Küme otomatik olarak, `cluster-autoscaler-status`adlı bir configmap 'e sistem durumu da yazar. Bu günlükleri almak için aşağıdaki `kubectl` komutunu yürütün. Küme otomatik olarak yapılandırılmış her düğüm havuzu için bir sistem durumu bildirilir.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Otomatik olarak kaydedilen öğeler hakkında daha fazla bilgi edinmek için [Kubernetes/otomatik Scaler GitHub PROJESINDE](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why)SSS makalesini okuyun.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Birden çok düğüm havuzu etkin olan küme otomatik Scaler 'ı kullanma

Küme otomatik Scaler, [birden çok düğüm havuzu](use-multiple-node-pools.md) etkinleştirilmiş olarak birlikte kullanılabilir. Birden çok düğüm havuzunun nasıl etkinleştirileceğini ve var olan bir kümeye ek düğüm havuzları nasıl ekleneceğini öğrenmek için bu belgeyi izleyin. Her iki özelliği birlikte kullanırken kümedeki her bir düğüm havuzunda küme otomatik Scaler ' ı etkinleştirir ve her birine benzersiz otomatik ölçeklendirme kuralları geçirebilir.

Aşağıdaki komut, bu belgede daha önce [ilk yönergeleri](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) izlediğinizi ve mevcut düğüm havuzunun en büyük sayısını *3* ' ten *5*' e kadar güncelleştirmek istediğinizi varsayar. Mevcut bir düğüm havuzunun ayarlarını güncelleştirmek için [az aks nodepool Update][az-aks-nodepool-update] komutunu kullanın.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Küme otomatik Scaler, [az aks nodepool Update][az-aks-nodepool-update] ile devre dışı bırakılabilir ve `--disable-cluster-autoscaler` parametresi geçirilerek.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Küme otomatik olarak var olan bir kümede yeniden etkinleştirmek istiyorsanız, [az aks nodepool Update][az-aks-nodepool-update] komutunu kullanarak, *--Enable-Cluster-otomatik Scaler*, *--Min-Count*ve *--Max-Count* parametrelerini belirterek yeniden etkinleştirebilirsiniz .

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS düğümlerinin sayısını otomatik olarak ölçeklendirirsiniz. Ayrıca, uygulamanızı çalıştıran Pod sayısını otomatik olarak ayarlamak için yatay Pod otomatik Scaler ' yı da kullanabilirsiniz. Yatay Pod otomatik Scaler 'ı kullanma adımları için bkz. [aks 'de Uygulamaları ölçeklendirme][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca

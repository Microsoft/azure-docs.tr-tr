---
title: Azure Kubernetes Service (AKS) kümesine spot düğüm havuzu ekleme
description: Azure Kubernetes Service (AKS) kümesine bir spot düğüm havuzu eklemeyi öğrenin.
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: f46a421ae2ad1a4d9c590c7e0b47784760ebcb9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782810"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) kümesine spot düğüm havuzu ekleme

Spot düğüm havuzu, bir [spot sanal makine ölçek kümesi][vmss-spot]tarafından desteklenen bir düğüm havuzudur. AKS kümeniz olan düğümler için spot VM 'Lerin kullanılması, Azure 'daki unutilized kapasitesinden yararlanarak önemli bir maliyet tasarrufu elde etmenizi sağlar. Kullanılabilir unutilized kapasitesi, düğüm boyutu, bölge ve günün saati dahil olmak üzere birçok etkene göre değişiklik gösterecektir.

Bir spot düğüm havuzu dağıtıldığında, kullanılabilir kapasite varsa Azure spot düğümleri ayırır. Ancak, nokta düğümleri için SLA yoktur. Spot düğüm havuzunu yedekleyen bir spot ölçek kümesi, tek bir hata etki alanında dağıtılır ve yüksek kullanılabilirlik garantisi vermez. Azure 'un kapasiteyi yeniden yapması gerektiğinde, Azure altyapısı spot düğümleri çıkarır.

Spot düğümler, kesintileri, erken sonlandırmaları veya çıkarmaları işleyebilen iş yükleri için idealdir. Örneğin, toplu işleme işleri, geliştirme ve test ortamları ve büyük işlem iş yükleri gibi iş yükleri, bir spot düğüm havuzunda zamanlanabilecek iyi adaylar olabilir.

Bu makalede, var olan bir Azure Kubernetes Service (AKS) kümesine ikincil bir spot düğüm havuzu eklersiniz.

Bu makalede, Kubernetes ve Azure Load Balancer kavramlarının temel bir şekilde anlaşıldığı varsayılır. Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti (AKS) Için Kubernetes temel kavramları][kubernetes-concepts].

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

Bir spot düğüm havuzu kullanmak için bir küme oluşturduğunuzda, bu küme düğüm havuzları ve *Standart* SKU yük dengeleyici Için sanal makine ölçek kümelerini de kullanmalıdır. Bir spot düğüm havuzu kullanmak için kümenizi oluşturduktan sonra ek bir düğüm havuzu da eklemeniz gerekir. Ek düğüm havuzu eklemek sonraki bir adımda ele alınmıştır.

Bu makalede, Azure CLı sürüm 2,14 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

### <a name="limitations"></a>Sınırlamalar

Bir spot düğüm havuzuyla AKS kümeleri oluşturup yönetirken aşağıdaki sınırlamalar geçerlidir:

* Bir spot düğüm havuzu kümenin varsayılan düğüm havuzu olamaz. Bir spot düğüm havuzu yalnızca ikincil havuz için kullanılabilir.
* Spot düğüm havuzları Cordon ve drenajı garanti edemediği için bir spot düğüm havuzunu yükseltemezsiniz. Kubernetes sürümünü yükseltme gibi işlemleri yapmak için mevcut spot düğüm havuzunuzu yeni bir tane ile değiştirmeniz gerekir. Bir spot düğüm havuzunu değiştirmek için, farklı bir Kubernetes sürümü ile yeni bir spot düğüm havuzu oluşturun, durumu *hazır* olana kadar bekleyin, sonra eski düğüm havuzunu kaldırın.
* Denetim düzlemi ve düğüm havuzları aynı anda yükseltilemez. Bunları ayrı olarak yükseltmeniz veya denetim düzlemi ile kalan düğüm havuzlarını aynı anda yükseltmek için spot düğüm havuzunu kaldırmanız gerekir.
* Bir spot düğüm havuzunun sanal makine ölçek kümelerini kullanması gerekir.
* Oluşturmadan sonra ScaleSetPriority veya SpotMaxPrice ' i değiştiremezsiniz.
* SpotMaxPrice ayarlanırken değer-1 veya en fazla beş ondalık basamak içeren pozitif bir değer olmalıdır.
* Bir spot düğüm havuzunda *Kubernetes.Azure.com/scalesetpriority:spot* etiketi, taint *Kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* ve sistem yığınlarının benzeşim düzeyi olur.
* Bir spot düğüm havuzunda iş yüklerini zamanlamak için [karşılık gelen bir tolerans][spot-toleration] eklemeniz gerekir.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>AKS kümesine spot düğüm havuzu ekleme

Birden çok düğüm havuzu etkin olan mevcut bir kümeye bir spot düğüm havuzu eklemeniz gerekir. Birden çok düğümlü havuzlarla bir AKS kümesi oluşturma hakkında daha fazla ayrıntı [burada][use-multiple-node-pools]bulunabilir.

[Az aks nodepool Add][az-aks-nodepool-add]' i kullanarak bir düğüm havuzu oluşturun.
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Varsayılan olarak, çok düğümlü havuzlarla bir küme oluşturduğunuzda AKS kümenizde *düzenli* *önceliğe* sahip bir düğüm havuzu oluşturursunuz. Yukarıdaki komut, var olan bir AKS kümesine bir *nokta* *önceliği* olan bir yardımcı düğüm havuzu ekler. Noktanın *önceliği* ,  düğüm havuzunun bir spot düğüm havuzu olmasını sağlar. *Çıkarma-ilke* parametresi, yukarıdaki örnekte varsayılan değer olan, *silinmek* üzere ayarlanır. [Çıkarma Ilkesini][eviction-policy] *silinmek* üzere ayarladığınızda, düğüm havuzunun temeldeki ölçek kümesindeki düğümler çıkarıldıklarında silinir. Çıkarma ilkesini *serbest bırakma* olarak da ayarlayabilirsiniz. Çıkarma ilkesini *serbest bırakma* olarak ayarladığınızda, temel alınan ölçek kümesindeki düğümler, çıkarma sırasında durdurulmuş, serbest bırakılmış duruma ayarlanır. İşlem kotasından dolayı durdurulmuş serbest bırakılmış durum sayısı içindeki düğümler, küme ölçeklendirme veya yükseltme ile ilgili sorunlara neden olabilir. *Öncelik* ve *çıkarma ilkesi* değerleri yalnızca düğüm havuzu oluşturma sırasında ayarlanabilir. Bu değerler daha sonra güncellenemiyor.

Komut Ayrıca, küme düğüm havuzlarıyla kullanılması önerilen [küme otomatik Scaler][cluster-autoscaler]öğesini de sağlar. Kümenizde çalışan iş yüklerine bağlı olarak, otomatik ölçeklendirme kümesi, düğüm havuzundaki düğüm sayısını ölçeklendirir ve ölçeklendirir. Spot düğüm havuzları için, ek düğümlere ihtiyaç duyulduktan sonra, küme otomatik olarak bir çıkarma sonrasında düğüm sayısını ölçeklendirecektir. Düğüm havuzunun sahip olduğu en fazla düğüm sayısını değiştirirseniz, `maxCount` küme otomatik Scaler ile ilişkili değeri de ayarlamanız gerekir. Bir küme otomatik yüklemesi kullanmazsanız, çıkarma sonrasında, nokta havuzu sonunda sıfır olarak azalır ve ek spot düğümleri almak için el ile bir işlem gerekir.

> [!Important]
> Yalnızca yığın işleme işleri ve test ortamları gibi kesintileri işleyebilen spot düğüm havuzlarındaki iş yüklerini zamanlayın. Yalnızca düğüm çıkarmaları işleyebilen iş yüklerinin bir spot düğüm havuzunda zamanlanmasını sağlamak için spot düğüm havuzunuzdaki [Talara ve toleransyonlar][taints-tolerations] ayarlamanız önerilir. Örneğin, yukarıdaki komut, varsayılan olarak bir Taint *Kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* , bu durumda yalnızca karşılık gelen bir toleransı olan Pod 'ler zamanlanır.

## <a name="verify-the-spot-node-pool"></a>Spot düğüm havuzunu doğrulama

Düğüm havuzunuzun bir spot düğüm havuzu olarak eklendiğini doğrulamak için:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

*Scalesetpriority* 'nin *nokta* olduğunu onaylayın.

Bir pod 'yi bir spot düğümünde çalışacak şekilde zamanlamak için, nokta düğümünüz uygulanmış olan taınt öğesine karşılık gelen bir tolerans ekleyin. Aşağıdaki örnek, bir YAML dosyasının, önceki adımda kullanılan bir *Kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* taınt öğesine karşılık gelen bir toleransı tanımlayan bir bölümünü gösterir.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Bu toleranya sahip bir pod dağıtıldığında Kubernetes, Taint uygulanmış olan düğümlerde Pod 'u başarıyla zamanlayabilir.

## <a name="max-price-for-a-spot-pool"></a>Bir spot havuz için en fazla fiyat
[Spot örnekleri Için fiyatlandırma][pricing-spot], bölgeye ve SKU 'ya göre değişkendir. Daha fazla bilgi için bkz. [Linux][pricing-linux] ve [Windows][pricing-windows]fiyatlandırması.

Değişken fiyatlandırmayla, en fazla 5 ondalık basamak kullanarak ABD Doları (USD) cinsinden maksimum fiyat ayarlama seçeneğiniz vardır. Örneğin *0,98765* değeri, saat başına en fazla $0,98765 ABD doları olur. Maksimum fiyatı *-1* olarak ayarlarsanız, örnek fiyata göre çıkarılmaz. Örneğin fiyatı, kapasite ve kota kullanılabilir olduğu sürece, bir standart örnek için geçerli fiyat veya fiyat fiyatı olacaktır.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir AKS kümesine spot düğüm havuzu eklemeyi öğrendiniz. Düğüm havuzlarının tamamında nasıl denetim yapılacağı hakkında daha fazla bilgi için bkz. [AKS 'de gelişmiş Zamanlayıcı özellikleri Için en iyi yöntemler][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md
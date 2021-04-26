---
title: Zamanlayıcı özellikleri için en iyi yöntemler
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ile ilgili teknoloji ve tolerans, düğüm seçicileri, benzeşim veya POD olmayan benzeşim ve benzeşim gibi gelişmiş Zamanlayıcı özelliklerini kullanmaya yönelik küme işletmeni en iyi yöntemlerini öğrenin
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 971916c3fc903ff5d69db2e0f82fd884acf807b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831591"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) gelişmiş zamanlayıcı özellikleri için en iyi yöntemler

Azure Kubernetes Service (AKS) içindeki kümeleri yönetirken, genellikle takımları ve iş yüklerini yalıtmanız gerekir. Kubernetes Scheduler tarafından sunulan gelişmiş özellikler şunları denetlemenize izin verir:
* Belirli düğümlerde hangi yığınların planlanabileceği.
* Çoklu Pod uygulamalarının küme genelinde uygun şekilde dağıtılması. 

Bu en iyi yöntemler makalesi, küme işleçleri için gelişmiş Kubernetes zamanlama özelliklerine odaklanır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Düğümlerde hangi yığınların zamanlanabileceği sınırlandırmalar için Talara ve toleranlarını kullanın.
> * Düğüm seçicileri veya düğüm benzeşimi ile belirli düğümlerde çalıştırılacak bir tercihe izin verin.
> * Pod 'lar arası benzeşim veya benzeşim olmadan parçalama veya grup ayırma.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Talitre ve tolerans kullanarak adanmış düğümler sağlama

> **En iyi Yöntem Kılavuzu:** 
>
> Giriş denetleyicileri gibi kaynak yoğunluklu uygulamalar için erişimi belirli düğümlere sınırlayın. Düğüm kaynaklarını, bunları gerektiren iş yükleri için kullanılabilir tutun ve düğümlerdeki diğer iş yüklerinin zamanlamaya izin vermez.

AKS kümenizi oluştururken, GPU desteğiyle veya çok sayıda güçlü CPU ile düğümleri dağıtabilirsiniz. Machine Learning (ML) veya yapay zeka (AI) gibi büyük veri işleme iş yükleri için bu düğümleri kullanabilirsiniz. 

Bu düğüm kaynak donanımının dağıtımı genellikle pahalı olduğundan, bu düğümlerde zamanlanabilecek iş yüklerini sınırlayın. Bunun yerine, giriş hizmetlerini çalıştırmak ve diğer iş yüklerini engellemek için kümedeki bazı düğümleri ayırabilirsiniz.

Farklı düğümler için bu destek, birden çok düğüm havuzu kullanılarak sağlanır. AKS kümesi bir veya daha fazla düğüm havuzu sağlar.

Kubernetes Scheduler, düğümlerde hangi iş yüklerinin çalıştırılacağını kısıtlamak için tatları ve toleransı kullanır.

* Yalnızca belirli yığınların zamanlanabilecek olduğunu göstermek için bir düğüme **taınt** uygulayın.
* Daha sonra bir pod 'a **tolerans** uygulayıp, bir düğümün Taint 'i kabul *edebilmesine* izin verir.

Bir aks kümesine Pod dağıttığınızda, Kubernetes yalnızca taınt toleransı ile hizalanan düğümlerde Pod 'yi zamanlar. Örneğin, GPU desteği olan düğümler için AKS kümenize bir düğüm havuzu eklediğinizi varsayalım. *GPU* gibi bir ad, sonra da zamanlama için bir değer tanımlarsınız. Bu değerin *NoSchedule* olarak ayarlanması, Kubernetes Scheduler 'ın düğüm üzerinde tanımsız toleransıyla planlamasını planladığını kısıtlar.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

Düğüm havuzundaki düğümlere uygulanan bir Taint ile,, düğümlerde zamanlamaya izin veren Pod belirtiminde bir tolerans tanımlayacaksınız. Aşağıdaki örnek, `sku: gpu` `effect: NoSchedule` önceki adımda düğüm havuzuna uygulanmış olan taınt 'in tolerans için ve ' i tanımlar:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Bu Pod kullanılarak dağıtıldığında `kubectl apply -f gpu-toleration.yaml` Kubernetes, Taint uygulanmış olan düğümlerde Pod 'u başarıyla zamanlayabilir. Bu mantıksal yalıtım, bir küme içindeki kaynaklara erişimi denetlemenize olanak tanır.

Taşı uyguladığınızda, uygulama geliştiricileriniz ve sahipleriyle birlikte çalışarak dağıtımlarındaki gerekli toleranları tanımlamasına izin verin.

AKS 'de birden çok düğüm havuzu kullanma hakkında daha fazla bilgi için bkz. [AKS 'deki bir küme için birden çok düğüm havuzu oluşturma ve yönetme][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>AKS 'teki litre ve tolerantalara yönelik davranış

AKS 'deki bir düğüm havuzunu yükselttiğinizde, litre ve tolerans, yeni düğümlere uygulandıkları sırada bir küme düzeniyle uyar:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>VM Ölçek kümelerini kullanan varsayılan kümeler
Yeni ölçeklendirilen düğümlerin API tarafından belirtilen düğüm konik almasını sağlamak için AKS API 'sinden [bir düğüm havuzu][taint-node-pool] oluşturabilirsiniz.

Şunları varsayalım:
1. İki düğümlü bir küme ile başlarlar: *Düğüm1* ve *Düğüm2*. 
1. Düğüm havuzunu yükseltirsiniz.
1. İki ek düğüm oluşturulur: *Düğüm3* ve *Düğüm4*. 
1. Litre sırasıyla geçirilir.
1. Özgün *Düğüm1* ve *Düğüm2* silinir.

#### <a name="clusters-without-vm-scale-set-support"></a>VM Ölçek kümesi desteği olmayan kümeler

Yine de şunları varsayalım:
1. İki düğümlü bir kümeniz vardır: *Düğüm1* ve *Düğüm2*. 
1. Düğüm havuzunu yükseltirsiniz.
1. Ek bir düğüm oluşturulur: *Düğüm3*.
1. *Düğüm1* 'in talar *Düğüm3*'e uygulanır.
1. *Düğüm1* silindi.
1. Özgün *Düğüm1* değiştirilecek yeni bir *Düğüm1* oluşturulur.
1. *Düğüm2* talar yeni *Düğüm1*'e uygulanır. 
1. *Düğüm2* silindi.

*Düğüm1* ' de *Düğüm3* olur ve *Düğüm2* yeni *Düğüm1* olur.

AKS 'deki bir düğüm havuzunu ölçeklendirdiğiniz zaman, litre ve tolerans, tasarıma göre yerine geçer.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Düğüm seçicileri ve benzeşimi kullanarak Pod zamanlamasını denetleme

> **En iyi yöntemler kılavuzu** 
> 
> Düğüm seçicileri, düğüm benzeşimi veya Pod tabanlı benzeşim kullanarak düğümlerdeki düğüm zamanlamasını denetleyin. Bu ayarlar, Kubernetes Scheduler 'ın, düğümdeki donanımlar gibi iş yüklerini mantıksal olarak yalıtmak için izin verir.

Litre ve toleransyonlar, kaynakları sabit bir kesme ile mantıksal olarak yalıtır. Pod bir düğümün Taint 'i kabul etmez, düğüm üzerinde zamanlanmamış.

Alternatif olarak, düğüm seçicileri de kullanabilirsiniz. Örneğin, düğümleri yerel olarak bağlı SSD depolamayı veya büyük miktarda belleği belirtecek şekilde etiketleyebilir ve sonra Pod belirtiminde bir düğüm Seçicisi tanımlayın. Kubernetes, bu düğümleri eşleşen bir düğümde zamanlar.

Toleranların aksine, eşleşen düğüm seçici olmayan yığınların etiketli düğümlerde zamanlanması devam edebilir. Bu davranış, düğümlerde kullanılmayan kaynakların kullanılmasına izin verir, ancak eşleşen düğüm seçiciyi tanımlayan düğüm önceliklerini önceliklendirir.

Yüksek miktarda bellekle bir düğüm örneğine göz atalım. Bu düğümler, yüksek miktarda bellek isteyen düğüm önceliklerini önceliklendirin. Kaynakların boşta olmadığından emin olmak için diğer yığınların çalışmasına de izin verir. Örnek izle komutu, *Myresourcegroup* Içindeki *Myakscluster* etiketli bir Node *= highmem* etiketine sahip bir düğüm havuzu ekler. Bu düğüm havuzundaki tüm düğümler bu etikete sahip olacaktır.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels hardware=highmem \
    --no-wait
```

Pod belirtimi, `nodeSelector` bir düğüm üzerinde ayarlanan etiketle eşleşen bir düğüm seçici tanımlamak için özelliğini ekler:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

Bu Zamanlayıcı seçeneklerini kullandığınızda, kendi Pod özelliklerini doğru bir şekilde tanımlamasına olanak tanımak için uygulama geliştiricileriniz ve sahipleriyle birlikte çalışın.

Düğüm seçicileri kullanma hakkında daha fazla bilgi için bkz. [düğümlere pods atama][k8s-node-selector].

### <a name="node-affinity"></a>Düğüm benzeşimi

Düğüm seçici, belirli bir düğüme Pod atamak için temel bir çözümdür. *Düğüm benzeşimi* daha fazla esneklik sağlar ve pod, bir düğümle eşleştirilemezse ne olacağını tanımlamanızı sağlar. Seçenekleriniz şunlardır: 
* Kubernetes Scheduler 'ın etiketli ana bilgisayar ile bir pod ile eşleşmesini *gerektir* . Veya
* Bir eşleşme *tercih eder* , ancak kullanılabilir bir eşleşme yoksa Pod 'ın farklı bir konakta zamanlanmasını sağlar.

Aşağıdaki örnek, Düğüm benzeşimini *requiredDuringSchedulingIgnoredDuringExecution* olarak ayarlar. Bu benzeşim, eşleşen etikete sahip bir düğüm kullanmak için Kubernetes zamanlamasını gerektirir. Kullanılabilir düğüm yoksa Pod 'ın zamanlamanın devam etmesini beklemesi gerekir. Pod 'un farklı bir düğümde zamanlanmasını sağlamak için, bu değeri ***Yeğlenen** DuringSchedulingIgnoreDuringExecution * olarak ayarlayabilirsiniz:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

Ayarın *IgnoredDuringExecution* bölümü, düğüm etiketlerinin değiştirilmesi durumunda Pod 'un düğümden çıkarılmayacağını belirtir. Kubernetes Scheduler yalnızca, düğümlerde zaten planlanmış olan yeni Pod 'ler için güncelleştirilmiş düğüm etiketlerini kullanır.

Daha fazla bilgi için bkz. [benzeşim ve benzeşim önleme][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Pod 'lar arası benzeşim ve benzeşim

İş yüklerini mantıksal olarak yalıtmak için Kubernetes Scheduler 'a yönelik bir son yaklaşım, Pod 'lar arası benzeşim veya benzeşim kullanımı kullanmaktır. Bu ayarlar, var olan bir pod 'un bulunduğu bir düğüm üzerinde olmayan *ya da zamanlanması* *gereken* bu ayarları tanımlar. Varsayılan olarak, Kubernetes Zamanlayıcı, düğümler arasında bir çoğaltma kümesinde birden çok düğüm zamanlamaya çalışır. Bu davranış etrafında daha özel kurallar tanımlayabilirsiniz.

Örneğin, redde için Azure önbelleği kullanan bir Web uygulamanız vardır. 
1. Kubernetes Scheduler 'ın çoğaltmaları düğümler arasında dağıtmasını istemek için Pod, çoklu seçim önleme kurallarını kullanırsınız. 
1. Her Web uygulaması bileşeninin karşılık gelen bir önbellek ile aynı ana bilgisayarda zamanlandığından emin olmak için benzeşim kurallarını kullanın. 

Düğümler arasında Pod dağıtımı aşağıdaki örneğe benzer şekilde görünür:

| **Düğüm 1** | **Düğüm 2** | **Düğüm 3** |
|------------|------------|------------|
| WEBAPP-1   | WEBAPP-2   | WEBAPP-3   |
| Önbellek-1    | önbellek-2    | önbellek-3    |

Pod 'lar arası benzeşim ve benzeşim önleme, düğüm seçicilerini veya düğüm benzeşimine göre daha karmaşık bir dağıtım sağlar. Dağıtım ile kaynakları mantıksal olarak yalıtın ve Kubernetes 'in düğümlerde düğüm zamanlamalarını kontrol edersiniz. 

Redin örneği için Azure Cache ile bu Web uygulamasının tam bir örneği için bkz. [aynı düğümdeki aynı düğüm üzerinde yer alan ortak bulma][k8s-pod-affinity].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, gelişmiş Kubernetes Zamanlayıcı özelliklerine odaklanılmıştır. AKS 'deki küme işlemleri hakkında daha fazla bilgi için aşağıdaki en iyi yöntemlere bakın:

* [Çok kiracılılık ve küme yalıtımı][aks-best-practices-scheduler]
* [Temel Kubernetes Zamanlayıcı özellikleri][aks-best-practices-scheduler]
* [Kimlik doğrulaması ve yetkilendirme][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool

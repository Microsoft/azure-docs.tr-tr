---
title: Operatör en iyi uygulamaları-Azure Kubernetes hizmetlerindeki (AKS) gelişmiş Zamanlayıcı özellikleri
description: Azure Kubernetes Service (AKS) ile ilgili teknoloji ve tolerans, düğüm seçicileri, benzeşim veya POD olmayan benzeşim ve benzeşim gibi gelişmiş Zamanlayıcı özelliklerini kullanmaya yönelik küme işletmeni en iyi yöntemlerini öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 798c368edb4a738124fce965f8990e6805fbdeba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472606"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki gelişmiş Zamanlayıcı özellikleri için en iyi yöntemler

Azure Kubernetes Service (AKS) içindeki kümeleri yönetirken, genellikle takımları ve iş yüklerini yalıtmanız gerekir. Kubernetes Scheduler, belirli düğümlerde hangi yığınların planlanabileceği veya birden çok Pod uygulamasının kümeye uygun şekilde nasıl dağılamayacağını denetlemenize olanak sağlayan gelişmiş özellikler sağlar. 

Bu en iyi yöntemler makalesi, küme işleçleri için gelişmiş Kubernetes zamanlama özelliklerine odaklanır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Düğümlerde hangi yığınların zamanlanabileceği sınırlandırmalar için Talara ve toleransyonlar kullanın
> * Düğüm seçicileri veya düğüm benzeşimi ile belirli düğümlerde çalıştırılacak olan tercihi verme
> * Pod 'lar arası benzeşim veya benzeşim önleme ile bir arada parçalama veya gruplama

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Talitre ve tolerans kullanarak adanmış düğümler sağlama

Giriş denetleyicileri gibi kaynak kullanımı yoğun uygulamalar için belirli düğümlere **en iyi yöntem kılavuzlarını** sınırlayın. Düğüm kaynaklarını, bunları gerektiren iş yükleri için kullanılabilir tutun ve düğümlerdeki diğer iş yüklerinin zamanlamaya izin vermez.

AKS kümenizi oluştururken, GPU desteğiyle veya çok sayıda güçlü CPU ile düğümleri dağıtabilirsiniz. Bu düğümler genellikle Machine Learning (ML) veya yapay zeka (AI) gibi büyük veri işleme iş yükleri için kullanılır. Bu tür donanımlar genellikle dağıtılacak maliyetli bir düğüm kaynağı olduğundan, bu düğümlerde zamanlanabilecek iş yüklerini sınırlayın. Bunun yerine, giriş hizmetlerini çalıştırmak ve diğer iş yüklerini engellemek için kümedeki bazı düğümleri ayırmak isteyebilirsiniz.

Farklı düğümler için bu destek, birden çok düğüm havuzu kullanılarak sağlanır. AKS kümesi bir veya daha fazla düğüm havuzu sağlar.

Kubernetes Zamanlayıcı, düğümlerde hangi iş yüklerinin çalıştırılacağını kısıtlamak için tatları ve toleranları kullanabilir.

* Yalnızca belirli yığınların zamanlanabileceğini gösteren bir düğüme bir **taınt** uygulanır.
* Daha sonra bir **tolerans** , düğümün Taint *'e kabul* etmesine izin veren bir pod öğesine uygulanır.

Bir aks kümesine Pod dağıttığınızda, Kubernetes yalnızca bir toleranation 'ın Taint ile hizalandığı düğümlerde Pod 'yi zamanlar. Örnek olarak, GPU desteği olan düğümler için AKS kümenizdeki bir düğüm havuzunuzun olduğunu varsayalım. *GPU*gibi bir ad, sonra da zamanlama için bir değer tanımlarsınız. Bu değeri *NoSchedule*olarak ayarlarsanız, Pod uygun toleransı tanımlamıyorsa Kubernetes Scheduler düğüm üzerinde pod zamanlayamaz.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Düğümlere bir taınt uygulandıktan sonra, düğümlerde zamanlamaya izin veren Pod belirtiminde bir tolerans tanımlarsınız. Aşağıdaki örnek, önceki adımda düğümüne uygulanan Taint 'e tolerans sağlamak için `sku: gpu` ve `effect: NoSchedule` tanımlar:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
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

Bu Pod dağıtıldığında, `kubectl apply -f gpu-toleration.yaml`kullanımı gibi Kubernetes, bir Taint uygulanmış olan düğümlerde Pod 'u başarıyla zamanlayabilir. Bu mantıksal yalıtım, bir küme içindeki kaynaklara erişimi denetlemenize olanak tanır.

Taşı uyguladığınızda, uygulama geliştiricileriniz ve sahipleriyle birlikte çalışarak dağıtımlarındaki gerekli toleranları tanımlamasına izin verin.

Litre ve toleransyonlar hakkında daha fazla bilgi için bkz. [litre ve tolerans uygulama][k8s-taints-tolerations].

AKS 'de birden çok düğüm havuzu kullanma hakkında daha fazla bilgi için bkz. [AKS 'deki bir küme için birden çok düğüm havuzu oluşturma ve yönetme][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>AKS 'teki litre ve tolerantalara yönelik davranış

AKS 'deki bir düğüm havuzunu yükselttiğinizde, litre ve tolerans, yeni düğümlere uygulandıkları sırada bir küme düzeniyle uyar:

- **Sanal makine ölçek kümeleri kullanan varsayılan kümeler**
  - İki düğümlü bir kümeniz olduğunu varsayalım- *Düğüm1* ve *Düğüm2*. Düğüm havuzunu yükseltirsiniz.
  - İki ek düğüm oluşturulur, *Düğüm3* ve *Düğüm4*ve litre sırasıyla geçirilir.
  - Özgün *Düğüm1* ve *Düğüm2* silinir.

- **Sanal makine ölçek kümesi desteği olmayan kümeler**
  - Yine de iki düğümlü bir kümeniz olduğunu varsayalım- *Düğüm1* ve *Düğüm2*. Yükselttiğinizde, ek bir düğüm (*Düğüm3*) oluşturulur.
  - *Düğüm1* 'in talar *Düğüm3*'e uygulanır, sonra *Düğüm1* silinir.
  - Başka bir yeni düğüm oluşturulur (önceki *Düğüm1* silindiği için *Düğüm1*adında) ve *Düğüm2* talitre yeni *Düğüm1*uygulanır. Sonra, *Düğüm2* silinir.
  - *Düğüm1* ' de *Düğüm3*olur ve *Düğüm2* *Düğüm1*olur.

AKS 'deki bir düğüm havuzunu ölçeklendirdiğiniz zaman, litre ve tolerans, tasarıma göre yerine geçer.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Düğüm seçicileri ve benzeşimi kullanarak Pod zamanlamasını denetleme

**En iyi Yöntem Kılavuzu** -düğüm seçicileri, düğüm benzeşimi veya Pod tabanlı benzeşim kullanarak düğümlerde düğüm zamanlamayı kontrol edin. Bu ayarlar, Kubernetes Scheduler 'ın, düğümdeki donanımlar gibi iş yüklerini mantıksal olarak yalıtmak için izin verir.

Litre ve toleransyonlar, kaynakları sabit bir şekilde yalıtmak için kullanılır-Pod bir düğümün Taint 'i kabul etmez, düğüm üzerinde zamanlanmamış. Alternatif yaklaşım, düğüm seçicileri kullanmaktır. Yerel olarak bağlı SSD depolamayı veya büyük miktarda belleği göstermek için gibi düğümleri etiketleyebilir ve sonra Pod belirtiminde bir düğüm seçici içinde tanımlayabilirsiniz. Kubernetes daha sonra bu düğüm 'leri eşleşen bir düğümde zamanlar. Toleranların aksine, eşleşen düğüm seçici olmayan düğüm etiketli düğümlerde zamanlanabilir. Bu davranış, düğümlerdeki kullanılmayan kaynakların kullanılmasına izin verir, ancak eşleşen düğüm seçiciyi tanımlayan Pod 'ye öncelik verir.

Yüksek miktarda bellekle bir düğüm örneğine göz atalım. Bu düğümler, büyük miktarda bellek isteyen Pod 'ye tercih verebilir. Kaynakların boşta olmadığından emin olmak için diğer yığınların çalışmasına de izin verir.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Pod belirtimi daha sonra, düğüm üzerinde ayarlanan etiketle eşleşen bir düğüm seçici tanımlamak için `nodeSelector` özelliğini ekler:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
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

Düğüm seçici, belirli bir düğüme Pod atamak için temel bir yoldur. *Düğüm benzeşimi*kullanılarak daha fazla esneklik sağlanır. Düğüm benzeşimi ile pod 'un bir düğümle eşleştirilemezse ne olacağını tanımlarsınız. Kubernetes Scheduler 'ın etiketli bir ana bilgisayar ile bir pod ile eşleşmesini *zorunlu* kılabilirsiniz. Ya da bir eşleşme *tercih* edebilir, ancak aynı eşleşme yoksa Pod 'ın farklı bir konakta zamanlanmasını sağlayabilirsiniz.

Aşağıdaki örnek, Düğüm benzeşimini *requiredDuringSchedulingIgnoredDuringExecution*olarak ayarlar. Bu benzeşim, eşleşen etikete sahip bir düğüm kullanmak için Kubernetes zamanlamasını gerektirir. Kullanılabilir düğüm yoksa Pod 'ın zamanlamanın devam etmesini beklemesi gerekir. Pod 'un farklı bir düğümde zamanlanmasını sağlamak için, bunun yerine değeri *preferredDuringScheduledIgnoreDuringExecution*olarak ayarlayabilirsiniz:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
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

Ayarın *IgnoredDuringExecution* bölümü, düğüm etiketlerinin değiştiğinde Pod 'un düğümden çıkarılmaması gerektiğini gösterir. Kubernetes Scheduler yalnızca, düğümlerde zaten planlanmış olan yeni Pod 'ler için güncelleştirilmiş düğüm etiketlerini kullanır.

Daha fazla bilgi için bkz. [benzeşim ve benzeşim önleme][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Pod 'lar arası benzeşim ve benzeşim

İş yüklerini mantıksal olarak yalıtmak için Kubernetes Scheduler 'a yönelik bir son yaklaşım, Pod 'lar arası benzeşim veya benzeşim kullanımı kullanmaktır. Ayarlar, var olan bir pod 'a sahip olan ya da zamanlanmaları *gereken* bir düğüm üzerinde, *Pod 'nin zamanlanmaması* gerektiğini tanımlar. Varsayılan olarak, Kubernetes Zamanlayıcı, düğümler arasında bir çoğaltma kümesinde birden çok düğüm zamanlamaya çalışır. Bu davranış etrafında daha özel kurallar tanımlayabilirsiniz.

İyi bir örnek, redde için Azure önbelleği kullanan bir Web uygulamasıdır. Kubernetes Scheduler 'ın çoğaltmaları düğümler arasında dağıtmasını istemek için Pod, çoklu seçim önleme kurallarını kullanabilirsiniz. Ardından, her bir Web uygulaması bileşeninin karşılık gelen bir önbellek ile aynı ana bilgisayarda zamanlandığından emin olmak için benzeşim kurallarını kullanabilirsiniz. Düğümler arasında Pod dağıtımı aşağıdaki örneğe benzer şekilde görünür:

| **Düğüm 1** | **Düğüm 2** | **Düğüm 3** |
|------------|------------|------------|
| WEBAPP-1   | WEBAPP-2   | WEBAPP-3   |
| Önbellek-1    | önbellek-2    | önbellek-3    |

Bu örnek, düğüm seçicileri veya düğüm benzeşimi kullanmaktan daha karmaşık bir dağıtımdır. Dağıtım, Kubernetes 'in düğümlerde düğüm zamanlamalarına ilişkin denetim sağlar ve kaynakları mantıksal olarak yalıtabilir. Redin örneği için Azure Cache ile bu Web uygulamasının tam bir örneği için bkz. [aynı düğümdeki aynı düğüm üzerinde yer alan ortak bulma][k8s-pod-affinity].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, gelişmiş Kubernetes Zamanlayıcı özelliklerine odaklanılmıştır. AKS 'deki küme işlemleri hakkında daha fazla bilgi için aşağıdaki en iyi yöntemlere bakın:

* [Çok kiracılı ve küme yalıtımı][aks-best-practices-scheduler]
* [Temel Kubernetes Zamanlayıcı özellikleri][aks-best-practices-scheduler]
* [Kimlik doğrulama ve yetkilendirme][aks-best-practices-identity]

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

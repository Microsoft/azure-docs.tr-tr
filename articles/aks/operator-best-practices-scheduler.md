---
title: İşleç en iyi uygulamalar - Azure Kubernetes Hizmetleri (AKS) temel Zamanlayıcı Özellikleri
description: Kaynak kotaları gibi temel Zamanlayıcı özelliklerini kullanmak için küme işleci en iyi adımları öğrenin ve pod kesintisi bütçelerini Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 3ce59784b2c7c1d145d99786b10927c230146c8b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614612"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Temel Zamanlayıcı Özellikleri Azure Kubernetes Service (AKS) için en iyi uygulamalar

Azure Kubernetes Service (AKS) kümelerini yönetirken, genellikle takımlar ve iş yüklerini yalıtmak gerekir. Kubernetes Zamanlayıcı işlem kaynaklarının dağıtımını denetlemek veya bakım etkinliklerinin etkisini sınırlamak olanak tanıyan özellikler sunar.

Bu en iyi yöntemler makalesi temel Kubernetes küme operatörleri için zamanlama özellikleri ele alınmaktadır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Kaynak kotaları kaynakları ekipleri ve iş yükleri için sabit miktarda sağlamak için kullanın
> * Zamanlanan bakım pod kesintisi bütçelerini kullanarak etkisini sınırlamak
> * Eksik pod kaynak isteklerini ve sınırları kullanarak onay `kube-advisor` aracı

## <a name="enforce-resource-quotas"></a>Kaynak kotaları

**En iyi uygulama kılavuzunu** -planı ve kaynak kotalar ad alanı düzeyinde uygulanır. Pod'ları kaynak isteklerini ve sınırları tanımlamazsanız, dağıtımı reddedebilir. Kaynak kullanımını izlemek ve kotalar gerektiği gibi ayarlayın.

Kaynak isteklerini ve sınırları pod belirtiminde yerleştirilir. Bu sınırlar Kubernetes Zamanlayıcı tarafından bir kullanılabilir düğüm kümedeki bulmak için dağıtım sırasında kullanılır. Bu sınırlar ve istekleri tek tek pod düzeyinde çalışır. Bu değerler tanımlama hakkında daha fazla bilgi için bkz. [tanımla pod kaynak isteklerini ve sınırlar][resource-limits]

Ayırabilir ve geliştirme takım veya proje kaynakları sınırlandırmak için bir yol sağlamak üzere kullanmalısınız *kaynak kotaları*. Bu kotalar ad alanı üzerinde tanımlanan ve aşağıdaki temelinde kota ayarlamak için kullanılabilir:

* **İşlem kaynaklarını**CPU ve bellek veya GPU'ları gibi.
* **Depolama kaynaklarını**, birimlerin toplam sayısı veya belirli bir depolama sınıfı için disk alanı miktarını içerir.
* **Nesne sayısı**, gizli dizileri maksimum sayısı gibi hizmetleri veya işleri oluşturulabilir.

Kubernetes kaynakları fazla kullanma değil. Atanan kota kaynak istekler veya sınırları toplam başarılı olduğunda, başka hiçbir dağıtımı başarılı.

Kaynak kotaları tanımladığınızda, ad alanında oluşturulan tüm pod sınırları veya kendi pod özellikleri istekleri sağlamanız gerekir. Bu değerleri sağlamazsanız dağıtımı reddedebilir. Bunun yerine, [varsayılan istekleri ve bir ad alanı için sınırları yapılandırın][configure-default-quotas].

Aşağıdaki örnek YAML bildirimi adlı *uygulama takım quotas.yaml geliştirme* toplam sabit bir sınır ayarlar *10* CPU'lar, *20 GI* bellek ve *10*pod'ları:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Bu kaynak kotası gibi bir ad belirterek uygulanabilir *geliştirme uygulamaları*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Kendi gereksinimlerini anlamak ve ilgili kaynak kotaları, uygulama geliştiriciler ve sahipleri çalışır.

Kullanılabilir kaynak nesneleri, kapsamları ve öncelikleri hakkında daha fazla bilgi için bkz: [Kubernetes kaynak kotaları][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Pod kesintisi bütçelerini kullanarak kullanılabilirlik planlama

**En iyi uygulama kılavuzunu** - uygulamaların kullanılabilirliğini sürdürmek için Pod kesintisi en az bir pod'ların sayısını kümedeki kullanılabilir olduğundan emin olmak için bütçelerini (PDB) tanımlayın.

Pod'ların kaldırılmasına neden iki aksatıcı olayları vardır:

* *Gönülsüz kesintileri* olaylar tipik kontrolü küme işleci veya uygulama sahibi.
  * Fiziksel makine, bir çekirdek Panik veya bir düğüm VM silinmesini bir donanım hatası Bu gönülsüz kesintileri içerir
* *Gönüllü kesintileri* küme işleci tarafından istenen bir olay veya uygulama sahibi.
  * Küme yükseltme, güncelleştirilmiş bir dağıtım şablonu veya bir pod yanlışlıkla silinmesi bu gönüllü kesintileri içerir.

Gönülsüz kesintileri, bir dağıtımdaki pod birden çok kopyasını kullanılarak azaltılabilir. Birden çok düğüm AKS kümesinde çalışan da bu gönülsüz kesintileri ile yardımcı olur. Gönüllü kesintileri için Kubernetes sağlar *kesintisi bütçelerini pod* en düşük veya en fazla kullanılabilir kullanılabilir kaynak sayısı tanımlamak küme işleci olanak tanır. Bu pod kesintisi bütçeleri gönüllü kesintisi olay meydana geldiğinde nasıl dağıtımları veya çoğaltma kümeleri yanıt için planlamanıza olanak sağlar.

Gönüllü bozulma olayları devam etmeden önce yükseltilecek bir küme ise veya bir dağıtım şablonu güncelleştirildi, Kubernetes Zamanlayıcı emin ek yapar pod'ların diğer düğümlerde zamanlanır. Zamanlayıcı, bir düğüm kümedeki diğer düğümlere tanımlanmış pod'ların sayısını başarıyla zamanlanan kadar yeniden bekler.

NGINX çalıştıran beş pod'ları ile ayarlanmış bir çoğaltmanın bir örneğe bakalım. Çoğaltma pod ' ların kümesi etiketi atanmış olarak `app: nginx-frontend`. Bir küme yükseltmesi gibi bir gönüllü kesintisi olayı sırasında en az üç pod'ların çalışmaya devam emin olmanız gerekir. Aşağıdaki YAML bildirim için bir *PodDisruptionBudget* nesne bu gereksinimleri tanımlar:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

Yüzde gibi tanımlayabilirsiniz *% 60*, çoğaltma için otomatik olarak dengelemek sağlayan ayarlamak pod'ların sayısı.

Çoğaltma kümesi içinde kullanılamayan örnekler en fazla sayısını tanımlayabilirsiniz. Yeniden yüzdesi için en fazla kullanılabilir pod'ları da tanımlanabilir. En fazla iki çoğaltma pod'ları, pod kesintisi bütçe YAML bildirimi tanımlar kümesi kullanılamaz durumda olabilir:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Pod kesintisi bütçenizi tanımlandıktan sonra AKS kümenizin nesnesiyle herhangi diğer Kubernetes gibi oluşturduğunuz:

```console
kubectl apply -f nginx-pdb.yaml
```

Kendi gereksinimlerini anlamak ve uygun pod kesintisi bütçeleri uygulamak için uygulama geliştiriciler ve sahipleri çalışır.

Pod kesintisi bütçelerini kullanma hakkında daha fazla bilgi için bkz. [uygulamanız için kesintiye bütçe belirtin][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Küme sorunlarını kube-Danışmanı ile düzenli aralıklarla denetleyin

**En iyi uygulama kılavuzunu** -düzenli olarak en son sürümünü çalıştıran `kube-advisor` kümenizdeki sorunları algılamak için açık kaynak aracı. Mevcut bir AKS kümesinde kaynak kotaları çalıştırırsanız `kube-advisor` ilk kaynak isteklerini ve sınırları tanımlanmış olmayan pod'ların bulunamıyor.

[Kube-advisor][kube-advisor] , bir Kubernetes kümesi tarar ve bulduğu sorunları ilişkili AKS açık kaynaklı proje bir araçtır. Yararlı bir onay kaynak isteklerini ve sınırları limitiniz olmadığı pod'ların belirlemektir.

Kaynak isteği ve Linux uygulamaları yanı sıra PodSpecs için Windows uygulamaları eksik sınırları kube-Danışman aracı bildirebilirsiniz, ancak kube-Danışman aracı, bir Linux pod zamanlanmalıdır. Bir pod bir düğüm havuzunu kullanarak belirli bir işletim sistemi ile çalışacak şekilde zamanlayabilirsiniz bir [düğüm Seçicisi][k8s-node-selector] pod'ın yapılandırması.

Birden çok geliştirme ekipleri ve uygulamaları barındıran bir AKS kümesinde bu kaynağın ister ve kümesi sınırlar olmadan pod'ların izlenmesi zor olabilir. En iyi uygulama, düzenli olarak çalıştırılan `kube-advisor` özellikle ad alanları için kaynak kotaları atamazsanız, AKS kümeleri üzerinde.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, temel Kubernetes Zamanlayıcı özelliklere odaklanan. AKS kümesi işlemleri hakkında daha fazla bilgi için aşağıdaki en iyi bakın:

* [Çok kiracılılık ve küme ayırma][aks-best-practices-cluster-isolation]
* [Gelişmiş Kubernetes Zamanlayıcı Özellikleri][aks-best-practices-advanced-scheduler]
* [Kimlik doğrulama ve yetkilendirme][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors

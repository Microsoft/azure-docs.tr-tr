---
title: Operatör en iyi yöntemleri-Azure Kubernetes hizmetlerinde küme yalıtımı (AKS)
description: Azure Kubernetes Service (AKS) ' de yalıtım için küme işletmeni en iyi uygulamalarını öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: e9f7a10f19ed23e4f3b4fefa38fbb2d1912f2ac0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348792"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki küme yalıtımı için en iyi yöntemler

Azure Kubernetes Service (AKS) içindeki kümeleri yönetirken, genellikle takımları ve iş yüklerini yalıtmanız gerekir. AKS, çok kiracılı kümelerin nasıl çalıştırılabilme ve kaynakları yalıtmak için esneklik sağlar. Kubernetes 'te yatırımınızı en üst düzeye çıkarmak için, bu çok kiracılı ve yalıtım özelliklerinin anlaşılması ve uygulanması gerekir.

Bu en iyi yöntemler makalesi, küme işleçleri yalıtımına odaklanır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Çok kiracılı kümeler ve kaynak ayrımı için plan yapın
> * AKS kümelerinizde mantıksal veya fiziksel yalıtım kullanın

## <a name="design-clusters-for-multi-tenancy"></a>Çoklu kiracı için küme tasarlama

Kubernetes, takımları ve iş yüklerini aynı kümede mantıksal olarak yalıtmanızı sağlayan özellikler sağlar. Amaç, her ekibin ihtiyacı olan kaynaklara göre en az sayıda ayrıcalıkların sağlanması gerekir. Kubernetes içindeki bir [ad alanı][k8s-namespaces] , mantıksal bir yalıtım sınırı oluşturur. Yalıtım ve çoklu kiracı için ek Kubernetes özellikleri ve konuları aşağıdaki alanlarda yer alır:

* **Zamanlama** , kaynak kotaları ve pod kesintisi bütçeleri gibi temel özelliklerin kullanımını içerir. Bu özellikler hakkında daha fazla bilgi için bkz. [AKS 'deki temel Zamanlayıcı özellikleri Için en iyi uygulamalar][aks-best-practices-scheduler].
  * Daha gelişmiş Zamanlayıcı özellikleri, litre ve tolerans, düğüm seçicileri, düğüm ve pod benzeşimi veya benzeşim toleransı içerir. Bu özellikler hakkında daha fazla bilgi için bkz. [AKS 'de gelişmiş Zamanlayıcı özellikleri Için en iyi uygulamalar][aks-best-practices-advanced-scheduler].
* **Ağ** , dışarıda ve dışı trafik akışını denetlemek için ağ ilkelerinin kullanımını içerir.
* **Kimlik doğrulama ve yetkilendirme** , rol tabanlı erişim denetımı (RBAC) ve Azure ACTIVE DIRECTORY (ad) tümleştirmesi, Pod kimlikleri ve Azure Key Vault gizli dizi kullanıcısını içerir. Bu özellikler hakkında daha fazla bilgi için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][aks-best-practices-identity].
* **Kapsayıcılar** , güvenlik açıkları için pod güvenlik ilkelerini, Pod güvenlik bağlamlarını, görüntüleri taramayı ve çalışma zamanlarını içerir. Ayrıca, temel alınan düğüme kapsayıcı erişimini kısıtlamak için App koruma sağlamak veya Seccomp (güvenli bilgi Işlem) kullanmayı da içerir.

## <a name="logically-isolate-clusters"></a>Kümeleri mantıksal olarak yalıtma

**En iyi Yöntem Kılavuzu** -takımları ve projeleri ayırmak için mantıksal yalıtım kullanın. Takımları veya uygulamaları yalıtmak için dağıttığınız fiziksel AKS kümelerinin sayısını en aza indirmeye çalışın.

Mantıksal yalıtımda, tek bir AKS kümesi çoklu iş yükleri, takımlar veya ortamlar için kullanılabilir. Kubernetes [ad alanları][k8s-namespaces] , iş yükleri ve kaynaklar için mantıksal yalıtım sınırı oluşturur.

![AKS 'teki bir Kubernetes kümesinin mantıksal yalıtımı](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Kümelerin mantıksal ayrımı genellikle fiziksel olarak yalıtılmış kümelerden daha yüksek bir pod yoğunluğu sağlar. Kümede boşta kalan daha fazla bilgi işlem kapasitesi vardır. Kubernetes kümesi otomatik olarak birleştirildiğinde, talepleri karşılamak için düğüm sayısını yukarı veya aşağı ölçeklendirebilirsiniz. Otomatik ölçeklendirmeye yönelik bu en iyi yöntem, yalnızca gerekli olan düğüm sayısını ve maliyetleri en aza indirecek şekilde çalışır.

Kubernetes ortamları, AKS veya başka bir yerde, çok kiracılı Kullanıcı kullanımı için tamamen güvenli değildir. Çok kiracılı bir ortamda birden çok kiracı ortak, paylaşılan bir altyapı üzerinde çalışıyor. Sonuç olarak, tüm kiracıların güvenilemediği durumlarda, bir kiracının güvenlik ve hizmet tarafından etkilenmesini önlemek için ek planlama yapmanız gerekir. Tüm düğümler için *Pod güvenlik ilkesi* ve daha ayrıntılı rol tabanlı erişim DENETIMLERI (RBAC) gibi ek güvenlik özellikleri, güvenli hale getirme daha zordur. Ancak, çok kiracılı çoklu kiracı iş yüklerini çalıştırırken doğru güvenlik için bir hiper yönetici, güvenmeniz gereken tek güvenlik düzeyidir. Kubernetes güvenlik etki alanı, tek bir düğüm değil, tüm küme haline gelir. Bu tür çok kiracılı iş yükleri için, fiziksel olarak yalıtılmış kümeler kullanmanız gerekir.

## <a name="physically-isolate-clusters"></a>Kümeleri fiziksel olarak yalıtma

**En iyi Yöntem Kılavuzu** -her ayrı takım veya uygulama dağıtımı için fiziksel yalıtımın kullanımını en aza indirin. Bunun yerine, önceki bölümde anlatıldığı gibi *mantıksal* yalıtım kullanın.

Küme yalıtımına yönelik yaygın bir yaklaşım, fiziksel olarak ayrı AKS kümelerini kullanmaktır. Bu yalıtım modelinde, takımlara veya iş yüklerine kendi AKS kümesi atanır. Bu yaklaşım genellikle iş yüklerini veya takımları yalıtmak için en kolay yol gibi görünür, ancak ek yönetim ve mali ek yük ekler. Artık bu birden çok kümeyi korumanız gerekir ve tek tek erişim ve izin atama izinleri sağlamanız gerekir. Tek tek tüm düğümler için de faturalandırılırsınız.

![AKS 'de tek bir Kubernetes kümesi fiziksel yalıtımı](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fiziksel olarak ayrı kümelerin genellikle düşük Pod yoğunluğu vardır. Her bir ekip veya iş yükünün kendi AKS kümesi olduğundan, küme genellikle işlem kaynaklarıyla sağlanır. Genellikle, bu düğümlerde az sayıda düğüm zamanlanır. Düğümlerde kullanılmayan kapasite, diğer takımlar tarafından geliştirilmekte olan uygulamalar veya hizmetler için kullanılamaz. Bu fazlalık kaynaklar, fiziksel olarak ayrı kümelerdeki ek maliyetlere katkıda bulunur.

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, küme yalıtımına odaklanılmıştır. AKS 'deki küme işlemleri hakkında daha fazla bilgi için aşağıdaki en iyi yöntemlere bakın:

* [Temel Kubernetes Zamanlayıcı özellikleri][aks-best-practices-scheduler]
* [Gelişmiş Kubernetes Zamanlayıcı özellikleri][aks-best-practices-advanced-scheduler]
* [Kimlik doğrulama ve yetkilendirme][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md

---
title: Küme yalıtımı için en iyi deneyimler
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ' de yalıtım için küme işletmeni en iyi uygulamalarını öğrenin
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105179"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki küme yalıtımı için en iyi yöntemler

Azure Kubernetes Service (AKS) içindeki kümeleri yönetirken, genellikle takımları ve iş yüklerini yalıtmanız gerekir. AKS, çok kiracılı kümelerin nasıl çalıştırılabilme ve kaynakları yalıtmak için esneklik sağlar. Kubernetes 'te yatırımınızı en üst düzeye çıkarmak için, önce AKS çok kiracılı ve yalıtım özelliklerini anlayın ve uygulayın.

Bu en iyi yöntemler makalesi, küme işleçleri yalıtımına odaklanır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Çok kiracılı kümeler ve kaynak ayrımı için plan yapın
> * AKS kümelerinizde mantıksal veya fiziksel yalıtım kullanın

## <a name="design-clusters-for-multi-tenancy"></a>Çoklu kiracı için küme tasarlama

Kubernetes, takımları ve iş yüklerini aynı kümede mantıksal olarak yalıtmanızı sağlar. Amaç, her ekibin ihtiyaç duyacağı kaynaklara göre en az sayıda ayrıcalıkların sağlanması sağlamaktır. Bir Kubernetes [ad alanı][k8s-namespaces] mantıksal yalıtım sınırı oluşturur. Yalıtım ve çoklu kiracı için ek Kubernetes özellikleri ve konuları aşağıdaki alanlarda yer alır:

### <a name="scheduling"></a>Zamanlama

*Zamanlama* , kaynak kotaları ve pod kesintisi bütçeleri gibi temel özellikleri kullanır. Bu özellikler hakkında daha fazla bilgi için bkz. [AKS 'deki temel Zamanlayıcı özellikleri Için en iyi uygulamalar][aks-best-practices-scheduler].

Daha gelişmiş Zamanlayıcı özellikleri şunlardır:
* Litre ve tolerantalar
* Düğüm seçicileri
* Node ve pod benzeşimi veya benzeşim karşıtlığı. 

Bu özellikler hakkında daha fazla bilgi için bkz. [AKS 'de gelişmiş Zamanlayıcı özellikleri Için en iyi uygulamalar][aks-best-practices-advanced-scheduler].

### <a name="networking"></a>Ağ

*Ağ* , dışarıda ve dışı trafik akışını denetlemek için ağ ilkelerini kullanır.

### <a name="authentication-and-authorization"></a>Kimlik doğrulaması ve yetkilendirme

*Kimlik doğrulama ve yetkilendirme* şunları kullanır:
* Rol tabanlı erişim denetimi (RBAC)
* Azure Active Directory (AD) Tümleştirmesi
* Pod kimlikleri
* Azure Key Vault gizli dizileri 

Bu özellikler hakkında daha fazla bilgi için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][aks-best-practices-identity].

### <a name="containers"></a>Kapsayıcılar
*Kapsayıcılar* şunları içerir:
* AKS için Azure Ilke eklentisi Pod güvenliği zorlayacak.
* Pod güvenlik bağlamlarının kullanımı.
* Hem görüntüler hem de çalışma zamanı, güvenlik açıkları için taranıyor. 
* İlgili düğüme kapsayıcı erişimini kısıtlamak için App koruma sağlamak veya Seccomp (güvenli bilgi Işlem) kullanma.

## <a name="logically-isolate-clusters"></a>Kümeleri mantıksal olarak yalıtma

> **En iyi yöntemler kılavuzu**
>
> *Mantıksal yalıtım* kullanarak takımları ve projeleri ayırın. Takımları veya uygulamaları yalıtmak için dağıttığınız fiziksel AKS kümelerinin sayısını en aza indirin.

Mantıksal yalıtımda, tek bir AKS kümesi çoklu iş yükleri, takımlar veya ortamlar için kullanılabilir. Kubernetes [ad alanları][k8s-namespaces] , iş yükleri ve kaynaklar için mantıksal yalıtım sınırı oluşturur.

![AKS 'teki bir Kubernetes kümesinin mantıksal yalıtımı](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Kümelerin mantıksal ayrımı genellikle fiziksel olarak yalıtılmış kümelerden daha yüksek bir pod yoğunluğu sağlar ve daha az fazla işlem kapasitesi kümede kalır. Kubernetes kümesi otomatik olarak birleştirildiğinde, talepleri karşılamak için düğüm sayısını yukarı veya aşağı ölçeklendirebilirsiniz. Otomatik ölçeklendirmeye yönelik bu en iyi yöntem, yalnızca gerekli düğüm sayısını çalıştırarak maliyetleri en aza indirir.

Şu anda, Kubernetes ortamları, çok kiracılı Kullanıcı kullanımı için tamamen güvenli değildir. Çok kiracılı bir ortamda, ortak, paylaşılan bir altyapıda birden çok kiracı çalışmaktadır. Tüm kiracıların güvenilemediği durumlarda, kiracının başkalarının güvenlik ve hizmetini etkilemesini engellemek için ek planlamaya ihtiyacınız olacaktır.

Düğümler için *Pod güvenlik ilkeleri* veya Kubernetes RBAC gibi ek güvenlik özellikleri, etkin bir şekilde blok yararlanma. Saldırgan çok kiracılı iş yüklerini çalıştırırken doğru güvenlik için yalnızca bir hiper yöneticiye güvenmelisiniz. Kubernetes güvenlik etki alanı, tek bir düğüm değil, tüm küme haline gelir. 

Bu tür çok kiracılı iş yükleri için, fiziksel olarak yalıtılmış kümeler kullanmanız gerekir.

## <a name="physically-isolate-clusters"></a>Kümeleri fiziksel olarak yalıtma

> **En iyi yöntemler kılavuzu** 
>
> Her ayrı takım veya uygulama dağıtımı için fiziksel yalıtımın kullanımını en aza indirin. Bunun yerine, önceki bölümde anlatıldığı gibi *mantıksal* yalıtım kullanın.

AKS kümelerini fiziksel olarak ayırmak, küme yalıtımına yönelik yaygın bir yaklaşımdır. Bu yalıtım modelinde, takımlara veya iş yüklerine kendi AKS kümesi atanır. Fiziksel yalıtım, iş yüklerini veya takımları yalıtmak için en kolay yol gibi görünebilir ancak yönetim ve mali ek yük ekler. Şimdi, bu birden çok kümeyi korumanız ve ayrı ayrı erişim ve atama izinleri sağlamanız gerekir. Her tek düğüm için de faturalandırılırsınız.

![AKS 'de tek bir Kubernetes kümesi fiziksel yalıtımı](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fiziksel olarak ayrı kümelerin genellikle düşük Pod yoğunluğu vardır. Her ekip veya iş yükünün kendi AKS kümesi olduğundan, küme genellikle işlem kaynaklarıyla sağlanır. Genellikle, bu düğümlerde az sayıda düğüm zamanlanır. Talep edilmeyen düğüm kapasitesi, diğer takımlar tarafından geliştirilmesinde uygulamalar veya hizmetler için kullanılamaz. Bu fazlalık kaynaklar, fiziksel olarak ayrı kümelerdeki ek maliyetlere katkıda bulunur.

## <a name="next-steps"></a>Sonraki adımlar

Bu makale, küme yalıtımına odaklanılmıştır. AKS 'deki küme işlemleri hakkında daha fazla bilgi için aşağıdaki en iyi yöntemlere bakın:

* [Temel Kubernetes Zamanlayıcı özellikleri][aks-best-practices-scheduler]
* [Gelişmiş Kubernetes Zamanlayıcı özellikleri][aks-best-practices-advanced-scheduler]
* [Kimlik doğrulaması ve yetkilendirme][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md

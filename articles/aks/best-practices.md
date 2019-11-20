---
title: Azure Kubernetes Service (AKS) için en iyi uygulamalar
description: Azure Kubernetes Service (AKS) uygulama oluşturmak ve yönetmek için küme işleci ve geliştirici en iyi koleksiyonu
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/07/2018
ms.author: mlearned
ms.openlocfilehash: 7127894b364ac8f0fe1d87e13150d5522f5473e2
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615954"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Küme işleci ve geliştirici en iyi uygulamalar Azure Kubernetes Service'teki (AKS) oluşturmak ve yönetmek için uygulamalar

Derleme ve Azure Kubernetes Service (AKS) uygulamaları başarılı bir şekilde çalıştırılması için anlaşılması ve uygulanması bazı önemli noktalar vardır. Bu alanlar, çok kiracılı ve Zamanlayıcı özellikler, küme ve pod güvenlik veya iş sürekliliği ve olağanüstü durum kurtarma içerir. Küme operatörleri yardımcı olması için aşağıdaki en iyi gruplandırılır ve geliştiriciler için bu alanların her biri için yapılacak değerlendirmeleri anlamaktır ve uygun özellikler uygular.

Bu en iyi yöntemler ve kavramsal makaleler, mühendislik ekipleri ve genel siyah bantları (GBBs) dahil olmak üzere alan takımlar AKS ürün grubu, birlikte yazılmıştır.

## <a name="cluster-operator-best-practices"></a>Küme işleci en iyi uygulamalar

Bir küme işleci olarak, kendi gereksinimlerini anlamak için uygulama sahipleri ve geliştiriciler ile birlikte çalışır. Ardından, AKS kümelerinizi gerektiği şekilde yapılandırmak için aşağıdaki en iyi kullanabilirsiniz.

**Çok kiracılı**

* [Küme yalıtımı için en iyi deneyimler](operator-best-practices-cluster-isolation.md)
    * Çok kiracılı modeli çekirdek bileşenleri ve mantıksal yalıtımı ile ad alanlarını içerir.
* [Temel zamanlayıcı özellikleri için en iyi deneyimler](operator-best-practices-scheduler.md)
    * Kaynak kotaları ve pod kesintisi bütçelerini kullanarak içerir.
* [Gelişmiş zamanlayıcı özellikleri için en iyi deneyimler](operator-best-practices-advanced-scheduler.md)
    * Taints ve tolerations, düğüm seçicileri ve benzeşim ve arası pod benzeşimi ve benzeşim karşıtlığını kullanarak içerir.
* [Kimlik doğrulaması ve yetkilendirme için en iyi yöntemler](operator-best-practices-identity.md)
    * Azure rol tabanlı erişim denetimlerine (RBAC) ve pod kimlikler kullanarak Active Directory ile tümleştirmeyi içerir.

**Güvenlik**

* [Küme güvenliği ve yükseltmeler için en iyi yöntemler](operator-best-practices-cluster-security.md)
    * Kapsayıcı erişimini sınırlandırma ve yükseltmeleri ve düğümü yeniden başlatma işlemlerini yönetme API sunucusuna erişimi güvenli hale getirme içerir.
* [Kapsayıcı görüntü yönetimi ve güvenliği için en iyi uygulamalar](operator-best-practices-container-image-management.md)
    * Görüntü ve çalışma zamanları ve otomatik yapılara temel görüntü güncelleştirmeleri güvenli hale getirme içerir.
* [Pod güvenlik için en iyi uygulamalar](developer-best-practices-pod-security.md)
    * Kaynaklara erişimi güvenli hale getirme, kimlik bilgisi ifşa sınırlandırma ve pod kimlikler ve dijital anahtar kasalarını kullanarak içerir.

**Ağ ve depolama**

* [Ağ bağlantısı için en iyi uygulamalar](operator-best-practices-network.md)
    * Giriş ve web uygulaması güvenlik duvarları (WAF) kullanarak ve düğüm SSH erişimini güvenli hale getirme farklı ağ modellerini içerir.
* [Depolama ve yedekleme için en iyi yöntemler](operator-best-practices-storage.md)
    * Dinamik olarak sağlama birimleri ve yedekleri uygun depolama türü ve düğüm boyutunu seçme içerir.

**Kurumsal kullanıma hazır iş yüklerini çalıştırma**

* [İş sürekliliği ve olağanüstü durum kurtarma için en iyi uygulamalar](operator-best-practices-multi-region.md)
    * Bölge çiftlerinin, birden fazla küme Azure Traffic Manager ve coğrafi çoğaltma, kapsayıcı görüntüleri kullanarak içerir.

## <a name="developer-best-practices"></a>Geliştirici en iyi uygulamalar

Bir geliştirici olarak veya uygulama sahibi, geliştirme deneyiminizi kolaylaştırmak ve tanımlama performans gereksinimlerine göre gerektirir.

* [Uygulama geliştiriciler için kaynak yönetimine yönelik en iyi yöntemler](developer-best-practices-resource-management.md)
    * Pod kaynak isteklerini ve geliştirme araçlarını yapılandırma ve uygulama sorunları için iade etme sınırları, tanımlama içerir.
* [Pod güvenlik için en iyi uygulamalar](developer-best-practices-pod-security.md)
    * Kaynaklara erişimi güvenli hale getirme, kimlik bilgisi ifşa sınırlandırma ve pod kimlikler ve dijital anahtar kasalarını kullanarak içerir.

## <a name="kubernetes--aks-concepts"></a>Kubernetes / AKS kavramları

Özelliklerinden bazıları ve bu en iyi uygulamaları bileşenlerinin anlamanıza yardımcı olmak için aşağıdaki kavramsal makaleleri kümeleri Azure Kubernetes Service (AKS) için de görebilirsiniz:

* [Kubernetes kavramları](concepts-clusters-workloads.md)
* [Erişim ve kimlik](concepts-identity.md)
* [Güvenlik kavramları](concepts-security.md)
* [Ağ kavramları](concepts-network.md)
* [Depolama seçenekleri](concepts-storage.md)
* [Ölçeklendirme seçenekleri](concepts-scale.md)

## <a name="next-steps"></a>Sonraki adımlar

AKS ile kullanmaya başlamak ihtiyacınız varsa, bir Azure Kubernetes Service (AKS) kullanarak küme dağıtmak için hızlı başlangıçları birini izleyin [Azure CLI](kubernetes-walkthrough.md) veya [Azure portalında](kubernetes-walkthrough-portal.md).

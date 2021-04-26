---
title: Azure Kubernetes hizmeti (AKS) düğümlerini otomatik olarak onarma
description: Düğüm otomatik onarma işlevselliği ve AKS 'in kopuk çalışan düğümlerini düzeltme hakkında bilgi edinin.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 341aef394a3784edbc0acd91dad396c9794da3d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105213"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes hizmeti (AKS) düğümü otomatik onarma

AKS, çalışan düğümlerinin sistem durumunu sürekli olarak izler ve sağlıksız hale gelirse otomatik düğüm onarımı gerçekleştirir. Azure sanal makinesi (VM) platformu, sorunlar yaşayan [VM 'lerde bakım gerçekleştirir][vm-updates] . 

AKS ve Azure VM 'Leri, kümelerdeki hizmet kesintilerini en aza indirmek için birlikte çalışır.

Bu belgede, otomatik düğüm onarma işlevinin hem Windows hem de Linux düğümleri için nasıl davranacağını öğreneceksiniz. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS, sağlıksız düğümleri denetler

AKS, bir düğümün kötü durumda olup olmadığını ve onarılması gerekip gerekmediğini anlamak için aşağıdaki kuralları kullanır: 
* Düğüm, 10 dakikalık bir zaman çerçevesi içinde ardışık denetimler üzerinde **NotReady** durumunu raporlar.
* Düğüm, 10 dakika içinde herhangi bir durum raporlamaz.

Kubectl ile düğümlerinizin sistem durumunu el ile kontrol edebilirsiniz.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Otomatik onarımın nasıl çalıştığı

> [!Note]
> AKS, Kullanıcı hesabı **aks-düzeltici** ile onarım işlemlerini başlatır.

AKS, 10 dakika boyunca sağlıksız bir durumda kalan sağlıksız bir düğümü tanımlarsa AKS aşağıdaki eylemleri gerçekleştirir:

1. Düğümü yeniden başlatın.
1. Yeniden başlatma başarısız olursa, düğümü yeniden görüntü yapın.
1. Yeniden görüntü başarısız olursa yeni bir düğüm oluşturup yeniden görüntü oluşturun.

Alternatif düzeltmeler, otomatik onarma başarısız olursa AKS mühendisleri tarafından araştırılır. 

AKS bir sistem durumu denetimi sırasında birden çok sağlıksız düğüm bulursa, başka bir onarım başlamadan önce her düğüm tek tek onarılır.

## <a name="next-steps"></a>Sonraki adımlar

AKS kümesi iş yüklerinizde yüksek kullanılabilirliği artırmak için [kullanılabilirlik alanları][availability-zones] kullanın.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md

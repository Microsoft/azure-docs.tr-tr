---
title: Kotalar, SKU'ları ve bölge kullanılabilirliği Azure Kubernetes Service (AKS)
description: Varsayılan kotaları, kısıtlı düğümü VM SKU boyutları ve bölge kullanılabilirliği Azure Kubernetes Service (AKS) hakkında bilgi edinin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: 318846cddecdf020e2e751d3a0b9e05fc83bba73
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614543"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Bölge kullanılabilirliği Azure Kubernetes Service (AKS) kotaları ve sanal makine boyutu sınırlamaları

Tüm Azure Hizmetleri varsayılan limitler ve kotalar kaynakları ve özelliklerini ayarlayın. Belirli sanal makine (VM) SKU'ları, ayrıca kullanmak için kısıtlanır.

Bu makalede, Azure Kubernetes Service (AKS) kaynakları ve Azure bölgelerindeki AKS kullanılabilirliği için varsayılan kaynak limitlerinin ayrıntıları.

## <a name="service-quotas-and-limits"></a>Hizmet kotaları ve limitleri

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Sağlanan altyapı

Tüm diğer ağ, işlem ve depolama sınırları, sağlanan altyapıya uygulanır. İlgili sınırlar için bkz. [Azure aboneliği ve hizmet sınırlamaları](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> Bir AKS kümesi yükselttiğinizde, ek kaynaklar geçici olarak kullanılır. Bu kaynaklar, bir sanal ağ alt ağı veya sanal makine vCPU kotası kullanılabilir IP adreslerini içerir. Windows Server kapsayıcıları (şu anda önizlemede aks'deki) kullanıyorsanız, en son güncelleştirmeleri düğümlerine uygulamak için yalnızca desteklenen bir yükseltme işlemi gerçekleştirmek için yaklaşımdır. Başarısız Küme yükseltme işlemi, bu geçici kaynakları işlemek için kullanılabilir IP adresi alanı veya vCPU kotası yoksa gösterebilir. Windows Server düğümün yükseltme işlemi hakkında daha fazla bilgi için bkz. [aks'deki bir düğüm havuzunu yükseltme][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Kısıtlı VM boyutları

Bir AKS kümesindeki her düğüm, sabit bir vCPU ve bellek gibi kaynakların miktarını içerir. Bir AKS düğümü yeterli işlem kaynakları içeriyorsa, pod'ların düzgün çalışması başarısız olabilir. Gerekli emin olmak için *kube-system* pod'ların ve uygulamalarınızın güvenilir bir şekilde zamanlanabilir, AKS içinde aşağıdaki VM SKU'ları kullanmayın:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

VM türleri ve işlem kaynaklarını hakkında daha fazla bilgi için bkz. [azure'da sanal makine boyutları][vm-skus].

## <a name="region-availability"></a>Bölge kullanılabilirliği

Where en son listesi için dağıtma ve kümeleri çalıştırın, bkz: [AKS bölge kullanılabilirliği][region-availability].

## <a name="next-steps"></a>Sonraki adımlar

Bazı varsayılan limitler ve kotalar artırılabilir. Kaynağınızı artışı destekliyorsa, aracılığıyla artırma isteği bir [Azure destek isteği][azure-support] (için **sorun türü**seçin **kota**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool

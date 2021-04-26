---
title: Kaynaklar, SKU 'Lar, bölgeler için sınırlar
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes hizmeti 'nin (AKS) varsayılan kotaları, sınırlı düğüm sanal makine SKU boyutları ve bölge kullanılabilirliği hakkında bilgi edinin.
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 3e1e74834153584525d2093d2a1bb8ba8e991e5a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011473"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde kotalar, sanal makine boyutu kısıtlamaları ve bölge kullanılabilirliği

Tüm Azure Hizmetleri, belirli sanal makine (VM) SKU 'Larının kullanım kısıtlamaları da dahil olmak üzere kaynak ve özellikler için varsayılan sınırları ve kotaları ayarlar.

Bu makalede, Azure Kubernetes hizmeti (AKS) kaynakları için varsayılan kaynak sınırları ve Azure bölgelerindeki AKS kullanılabilirliği ayrıntılı olarak açıklanır.

## <a name="service-quotas-and-limits"></a>Hizmet kotaları ve limitleri

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Sağlanan altyapı

Tüm diğer ağ, işlem ve depolama sınırları, sağlanan altyapıya uygulanır. İlgili sınırlar için bkz. [Azure aboneliği ve hizmet sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Bir AKS kümesini yükselttiğinizde, ek kaynaklar geçici olarak tüketilecektir. Bu kaynaklar, bir sanal ağ alt ağı veya sanal makine vCPU kotası içindeki kullanılabilir IP adreslerini içerir. 
>
> Windows Server kapsayıcıları için, en son düğüm güncelleştirmelerini uygulamak üzere bir yükseltme işlemi gerçekleştirebilirsiniz. Bu geçici kaynakları işlemek için kullanılabilir IP adresi alanı veya vCPU kotası yoksa, küme yükseltme işlemi başarısız olur. Windows Server düğüm yükseltme işlemi hakkında daha fazla bilgi için bkz. [AKS 'de düğüm havuzunu yükseltme][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Kısıtlanmış VM boyutları

Bir AKS kümesindeki her düğüm, vCPU ve bellek gibi sabit bir işlem kaynakları içerir. Aks düğümü yetersiz işlem kaynakları içeriyorsa, Pod düzgün çalışmayabilir. Gerekli *Kuto-sistem* yığınlarını ve uygulamalarınızın güvenilir bir şekilde zamanlanabilmesi IÇIN **aks 'de aşağıdaki VM SKU 'larını kullanmayın**:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

VM türleri ve bunların işlem kaynakları hakkında daha fazla bilgi için bkz. [Azure 'da sanal makineler Için boyutlar][vm-skus].

## <a name="region-availability"></a>Bölge kullanılabilirliği

Kümeleri dağıtmak ve çalıştırmak için kullanabileceğiniz en son liste için bkz. [aks bölge kullanılabilirliği][region-availability].

## <a name="next-steps"></a>Sonraki adımlar

Bazı varsayılan limitlerin ve kotaların artmasını sağlayabilirsiniz. Kaynağınız bir artışı destekliyorsa, bir [Azure destek isteği][azure-support] üzerinden artış Isteyin ( **sorun türü** için **Kota**' i seçin).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool

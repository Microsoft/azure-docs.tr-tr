---
title: include dosyası
description: include dosyası
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: f85605610727ef2c1e1987b7ef93a41ce2417a25
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "69626413"
---
| Resource | Varsayılan limit | Üst sınır |
| --- | --- | --- |
| [Abonelik](../articles/billing-buy-sign-up-azure-subscription.md) başına VM |Bölge başına 25.000<sup>1</sup> . |Bölge başına 25.000. |
| [Abonelik](../articles/billing-buy-sign-up-azure-subscription.md) başına toplam VM çekirdeği sayısı |Bölge başına 20<sup>1</sup> . | Desteğe başvurun. |
| Abonelik başına dv2 ve F gibi sanal makine, [abonelik](../articles/billing-buy-sign-up-azure-subscription.md) başına çekirdek |Bölge başına 20<sup>1</sup> . | Desteğe başvurun. |
| Abonelik başına [Coadministrators](../articles/billing-add-change-azure-subscription-administrator.md) |Sayısız. |Sayısız. |
| Abonelik başına bölge başına [depolama hesapları](../articles/storage/common/storage-quickstart-create-account.md) |250 |250 |
| Abonelik başına [kaynak grupları](../articles/azure-resource-manager/resource-group-overview.md) |980 |980 |
| Abonelik başına [kullanılabilirlik kümesi](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) sayısı |Bölge başına 2.000. |Bölge başına 2.000. |
| Azure Resource Manager API isteği boyutu |4\.194.304 bayt. |4\.194.304 bayt. |
| Abonelik başına Etiketler<sup>2</sup> |Sayısız. |Sayısız. |
| Abonelik başına benzersiz etiket hesaplamaları<sup>2</sup> | 10,000 | 10,000 |
| Abonelik başına [bulut hizmeti](../articles/cloud-services/cloud-services-choose-me.md) sayısı |YOK<sup>3</sup> |YOK<sup>3</sup> |
| Abonelik başına [benzeşim grubu](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) sayısı |YOK<sup>3</sup> |YOK<sup>3</sup> |
| Konum başına [abonelik düzeyi dağıtımlar](../articles/azure-resource-manager/deploy-to-subscription.md) | 800<sup>4</sup> | 800 |

<sup>1</sup> Varsayılan sınırlar, ücretsiz deneme, Kullandıkça öde ve dv2, F ve G gibi seriler gibi teklif kategorisi türüne göre farklılık gösterir. Örneğin, Kurumsal Anlaşma abonelikler için varsayılan değer 350 ' dir.

<sup>2</sup> Abonelik başına sınırsız sayıda etiket uygulayabilirsiniz. Kaynak veya kaynak grubu başına etiket sayısı 50 ile sınırlıdır. Kaynak Yöneticisi, yalnızca etiket sayısı 10.000 veya daha az olduğunda, abonelikteki [benzersiz etiket adı ve değerlerinin listesini](/rest/api/resources/tags) döndürür. Sayı 10.000 ' i aştığında yine de etikete göre bir kaynak bulabilirsiniz.  

<sup>3</sup> Bu özellikler artık Azure Kaynak grupları ve Kaynak Yöneticisi için gerekli değildir.

<sup>4</sup> 800 dağıtım sınırına ulaşırsanız, artık gerekli olmayan geçmişten dağıtımları silin. Abonelik düzeyi dağıtımlarını silmek için [Remove-azdeployment](/powershell/module/az.resources/Remove-AzDeployment) veya [az Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)komutunu kullanın.

> [!NOTE]
> Sanal makine çekirdekleri bölgesel toplam sınıra sahiptir. Ayrıca, Dv2 ve F gibi bölge başına bir seri için sınır vardır. Bu sınırlar ayrı olarak zorlanır. Örneğin, Doğu ABD toplam VM çekirdek limiti 30, A serisi çekirdek limiti 30 ve D serisi çekirdek limiti 30 olan bir abonelik düşünün. Bu abonelik 30 a1 VM 'Leri ya da 30 D1 VM 'yi ya da ikisinin bir bileşimini bir toplam 30 çekirdeği aşmayacak şekilde dağıtabilirler. Bir bileşim örneği 10 a1 VM ve 20 D1 VM 'dir.  
> <!-- -->
> 
> 


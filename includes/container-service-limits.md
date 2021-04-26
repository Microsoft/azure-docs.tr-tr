---
title: include dosyası
description: include dosyası
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: da22991b9a1c4b69d3a3d6eb6f76b0925a6ad3d4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800119"
---
| Kaynak                                                                                                           | Sınır                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Abonelik başına en fazla küme                                                                                  | 5000                                                                                                                                                                                                        |
| Sanal makine kullanılabilirlik kümeleri ve temel Load Balancer SKU 'SU ile küme başına en fazla düğüm                       | 100                                                                                                                                                                                                         |
| Sanal Makine Ölçek Kümeleri ve [Standart Load Balancer SKU 'su][standard-load-balancer] olan küme başına en fazla düğüm | 1000 (tüm [düğüm havuzları][node-pool]genelinde)                                            |
| Küme başına en fazla düğüm havuzu                                                                                     | 100                                                                                  |
| Düğüm başına maksimum Pod: kubenet ile [temel ağ][basic-networking]                                           | Maksimum: 250 <br /> Azure CLı varsayılan: 110 <br /> Azure Resource Manager şablonu varsayılanı: 110 <br /> Azure portal dağıtım varsayılanı: 30          |
| Düğüm başına en fazla düğüm sayısı: Azure Container ağ arabirimi ile [Gelişmiş ağ][advanced-networking]        | Maksimum: 250 <br /> Varsayılan: 30                                                      |
| Açık hizmet ağı (OSDK) AKS eklentisi önizlemesi                                                                          | Kubernetes kümesi sürümü: 1.19 +<sup>1</sup><br />Küme başına OSD denetleyicileri: 1<sup>1</sup><br />OSD denetleyicisi başına pods: 500<sup>1</sup><br />OSD tarafından yönetilen Kubernetes hizmet hesapları: 50<sup>1</sup> |

<sup>1</sup> AKS için OSA eklentisi önizleme durumundadır ve genel kullanılabilirlik (GA) öncesinde daha fazla geliştirmeler yapılacaktır. Önizleme aşamasında, gösterilen limitlerin aşılmadığından önerilir.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

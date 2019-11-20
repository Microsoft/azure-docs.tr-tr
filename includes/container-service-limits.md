---
title: include dosyası
description: include dosyası
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67188734"
---
| Resource | Varsayılan limit |
| --- | :--- |
| Abonelik başına en fazla küme | 100 |
| Küme başına en fazla düğüm | 100 |
| Düğüm başına maksimum Pod: Kubenet ile [temel ağ][basic-networking] | 110 |
| Düğüm başına maksimum Pod: Azure Container ağ arabirimi ile [Gelişmiş ağ oluşturma][advanced-networking] | Azure CLı dağıtımı: 30<sup>1</sup><br />Azure Resource Manager şablonu: 30<sup>1</sup><br />Portal dağıtımı: 30 |

<sup>1</sup> Azure CLI veya Kaynak Yöneticisi şablonuyla bir Azure Kubernetes hizmeti (aks) kümesi dağıttığınızda, bu değer düğüm başına 250 Pod 'ye yapılandırılabilir. Zaten bir aks kümesi dağıttıktan sonra veya Azure Portal kullanarak bir küme dağıtırsanız, düğüm başına maksimum Pod yapılandıramazsınız.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

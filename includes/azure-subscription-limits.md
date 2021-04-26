---
title: include dosyası
description: include dosyası
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559274"
---
| Kaynak | Varsayılan limit | Üst sınır |
| --- | --- | --- |
| [abonelik](https://azure.microsoft.com/pricing/)başına vCPU sayısı<sup>1</sup> |20 |10,000 |
| Abonelik başına [Coadministrators](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |200 |200 |
| Abonelik başına [depolama hesapları](../articles/storage/common/storage-account-create.md) <sup>2</sup> |100 |100 |
| Abonelik başına [bulut hizmeti](../articles/cloud-services/cloud-services-choose-me.md) sayısı |20 |200 |
| Abonelik başına [yerel ağlar](/previous-versions/azure/reference/jj157100(v=azure.100)) |10 |500 |
| Abonelik başına DNS sunucuları |9 |100 |
| Abonelik başına ayrılmış IP 'Ler |20 |100 |
| Abonelik başına [benzeşim grubu](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) sayısı |256 |256 |
| Abonelik adı uzunluğu (karakter) | 64 | 64 |

<sup>1</sup> Kısmi bir CPU çekirdeği kullanılmasına karşın, çok küçük örnekler vCPU sınırına doğru bir vCPU olarak sayılır.

<sup>2</sup> Depolama hesabı sınırı hem standart hem de Premium Depolama hesaplarını içerir.
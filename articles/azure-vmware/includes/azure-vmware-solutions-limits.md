---
title: Azure VMware Çözüm sınırları
description: Azure VMware Çözüm sınırlamaları.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582742"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Aşağıdaki tabloda, Azure VMware çözümü için en fazla sınır açıklanmaktadır.

| **Kaynak** | **Sınırlı** |
| :-- | :-- |
| Özel bulut başına kümeler | 12 |
| Küme başına en az düğüm sayısı | 3 |
| Küme başına en fazla düğüm sayısı | 16 |
| Özel bulut başına düğümler | 96 |
| özel bulut başına vCenter | 1  |
| HCX site eşleştirmeleri | 3 Advanced Edition ile 10 Enterprise Edition |
| AVS ExpressRoute maksimum bağlı SDDC | 4 |
| AVS ExpressRoute portspeed | 10 Gbps<br />Kullanılan sanal ağ geçidi, gerçek bant genişliğini belirler. Daha ayrıntılı bilgi için bkz. [ExpressRoute sanal ağ geçitleri hakkında](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| VWAN aracılığıyla kullanıma sunulan genel IP 'Ler | 100 |
| vSAN kapasite sınırları | Toplam kullanılabilir %75 (SLA için %25 oranında kullanılabilir)  |

Diğer VMware 'e özgü sınırlar için lütfen [VMware yapılandırması en yüksek aracı](https://configmax.vmware.com/)'nı kullanın!.

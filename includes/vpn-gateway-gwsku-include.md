---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085282"
---
Bir sanal ağ geçidi oluşturduğunuzda, kullanmak istediğiniz ağ geçidi SKU’sunu belirtmeniz gerekir. İş yükü, aktarım hızı, özellik ve SLA türlerine bağlı olarak gereksinimlerinize uyan SKU’ları seçin. Azure Kullanılabilirlik Alanları sanal ağ geçidi SKU 'Ları için, bkz. [Azure kullanılabilirlik alanları ağ geçidi SKU 'ları](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="benchmark"></a>Tünele, bağlantıya ve performansa göre Ağ Geçidi SKU’ları

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKU 'Ları (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 ve VpnGw5AZ) yalnızca Kaynak Yöneticisi dağıtım modeli için desteklenir. Klasik sanal ağlar eski (eski) SKU 'Ları kullanmaya devam etmelidir.
>  * Eski ağ geçidi SKU 'Ları (temel, standart ve HighPerformance) ile çalışma hakkında daha fazla bilgi için bkz. [VPN Gateway SKU 'ları (eski SKU 'lar) Ile çalışma](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * ExpressRoute ağ geçidi SKU 'Ları için bkz. [ExpressRoute Için sanal ağ geçitleri](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="feature"></a>Özellik kümesine göre ağ geçidi SKU 'Ları

Yeni VPN Gateway SKU 'Ları, ağ geçitleri üzerinde sunulan özellik kümelerini kolaylaştırır:

| **SKU**| **Özellikler**|
| ---    | ---         |
|**Temel** (\*\*)   | **Rota tabanlı VPN**: S2S/bağlantılar için 10 tüneli; P2S için RADIUS kimlik doğrulaması yok; P2S için Ikev2 yok<br>**İlke tabanlı VPN**: (IKEv1): 1 S2S/bağlantı tüneli; P2S yok|
| **Temel hariç tüm Generation1 ve Generation2 SKU 'Ları** | **Rota tabanlı VPN**: en fazla 30 tünelle (*), P2S, BGP, etkin-etkin, özel ıPSEC/Ike Ilkesi, ExpressRoute/VPN birlikte kullanımı |
|        |             |

(*) Yol tabanlı bir VPN ağ geçidini birden çok şirket içi ilke tabanlı güvenlik duvarı cihazına bağlamak için "PolicyBasedTrafficSelectors" yapılandırabilirsiniz. Ayrıntılı bilgi için bkz. [PowerShell kullanarak VPN ağ geçitlerini şirket içi ilke tabanlı birden fazla VPN cihazına bağlama](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

(\*\*) Temel SKU, eski SKU olarak kabul edilir. Temel SKU 'nun belirli özellik sınırlamaları vardır. Temel SKU kullanan bir ağ geçidini yeni ağ geçidi SKU 'larından birine yeniden boyutlandıramazsınız, bunun yerine VPN ağ geçidinizi silmeyi ve yeniden oluşturmayı içeren yeni bir SKU 'ya geçiş yapmanız gerekir.

###  <a name="workloads"></a>Ağ Geçidi SKU 'Ları-üretim ve geliştirme ve test Iş yükleri

SLA 'Lar ve özellik kümelerindeki farklılıklar nedeniyle, üretim ve geliştirme ve test için aşağıdaki SKU 'Ların kullanılması önerilir:

| **İş yükü**                       | **SKU'lar**               |
| ---                                | ---                    |
| **Üretim, kritik iş yükleri** | Temel hariç tüm Generation1 ve Generation2 SKU 'Ları |
| **Geliştirme-test veya kavram kanıtı**   | Temel (\*\*)                 |
|                                    |                        |

(\*\*) Temel SKU 'su eski SKU olarak değerlendirilir ve özellik sınırlamaları vardır. Temel SKU 'YU kullanmadan önce ihtiyacınız olan özelliğin desteklendiğinden emin olun.

Eski SKU 'Ları (eski) kullanıyorsanız, üretim SKU 'SU önerileri standart ve yüksek Performansdır. Eski SKU 'Lar hakkında bilgi ve yönergeler için bkz. [ağ geçidi SKU 'ları (eski)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).

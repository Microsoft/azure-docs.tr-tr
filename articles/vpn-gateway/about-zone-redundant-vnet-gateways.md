---
title: Azure Kullanılabilirlik Alanları 'de bölge yedekli sanal ağ geçitleri hakkında
description: Sanal ağ geçitleri için dayanıklılık, ölçeklenebilirlik ve daha yüksek kullanılabilirlik sağlamak üzere Azure Kullanılabilirlik Alanları ' de VPN ve ExpressRoute ağ geçitleri dağıtın.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2d0e6464e81c0b0d04b9a0f483bcd14f075fa399
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065171"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Azure Kullanılabilirlik Alanları 'de bölge yedekli sanal ağ geçitleri hakkında

VPN ve ExpressRoute ağ geçitlerini [Azure kullanılabilirlik alanları](../availability-zones/az-overview.md)dağıtabilirsiniz. Bu seçenek, sanal ağ geçitlerine dayanıklılık, ölçeklenebilirlik ve daha yüksek kullanılabilirlik getirir. Ağ geçitlerini Azure Kullanılabilirlik Alanları içinde dağıtmak, bir bölge içindeki ağ geçitlerini fiziksel ve mantıksal olarak birbirinden ayırırken, Azure ile şirket içi ağ bağlantınızı alan düzeyindeki hatalardan korur.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Alanlar arası yedekli ağ geçitleri

Sanal ağ geçitlerini kullanılabilirlik alanları arasında otomatik olarak dağıtmak için, bölgesel olarak yedekli sanal ağ geçitleri kullanabilirsiniz. Bölgesel olarak yedekli ağ geçitleri sayesinde, Azure 'da görev açısından kritik, ölçeklenebilir hizmetlerinize erişmek için bölge esnekliği avantajlarından yararlanabilirsiniz.

<br>
<br>

![bölgesel olarak yedekli ağ geçitleri grafiği](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Zonal ağ geçitleri

Belirli bir bölgedeki ağ geçitlerini dağıtmak için, bölgesel ağ geçitlerini kullanabilirsiniz. Bir bölgesel ağ geçidi dağıttığınızda, ağ geçidinin tüm örnekleri aynı Kullanılabilirlik bölgesine dağıtılır.

<br>
<br>

![Bölgesel ağ geçitleri grafiği](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Ağ Geçidi SKU’ları

Bölgesel olarak yedekli ve bölgesel ağ geçitleri, yeni ağ geçidi SKU 'ları olarak kullanılabilir. Azure AZ bölgelere yeni sanal ağ geçidi SKU 'Ları ekledik. Bu SKU 'lar ExpressRoute ve VPN Gateway için ilgili mevcut SKU 'lara benzer ve bunlar, bölgesel olarak yedekli ve bölgesel ağ geçitlerine özgü olmaları dışında. Bu SKU 'Ları, SKU adında "AZ" ile tanımlayabilirsiniz.

Ağ Geçidi SKU 'Ları hakkında bilgi için bkz. [VPN Gateway SKU 'ları](vpn-gateway-about-vpngateways.md#gwsku) ve [ExpressRoute ağ geçidi SKU 'ları](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>Genel IP SKU 'Ları

Bölgesel olarak yedekli ağ geçitleri ve bölgesel ağ geçitleri, Azure genel IP kaynak *standardı* SKU 'sunu kullanır. Azure genel IP kaynağının yapılandırması, dağıttığınız ağ geçidinin bölge yedekli veya zonal olup olmadığını belirler. *Temel* SKU ile genel bir IP kaynağı oluşturursanız ağ geçidinde hiçbir bölge artıklığı olmaz ve ağ geçidi kaynakları bölgesel olur.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Alanlar arası yedekli ağ geçitleri

Bir bölge belirtmeden **Standart** genel IP SKU 'su kullanılarak genel bir IP adresi oluşturduğunuzda, bu davranış, ağ GEÇIDININ bir VPN ağ geçidi veya ExpressRoute ağ geçidi olmasına bağlı olarak farklılık gösterir. 

* Bir VPN ağ geçidi için, iki ağ geçidi örneği, bölge artıklığı sağlamak üzere bu üç bölgeden herhangi bir 2 ' ye dağıtılır. 
* ExpressRoute ağ geçidi için, iki taneden fazla örnek olduğundan, ağ geçidi üç bölgenin tamamına yayılabilir.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Zonal ağ geçitleri

**Standart** genel IP SKU 'sunu kullanarak genel bir IP adresi oluşturduğunuzda ve bölgeyi (1, 2 veya 3) belirttiğinizde, tüm ağ geçidi örnekleri aynı bölgeye dağıtılır.

### <a name="regional-gateways"></a><a name="piprg"></a>Bölgesel ağ geçitleri

**Temel** genel IP SKU 'su kullanılarak genel bir IP adresi oluşturduğunuzda, ağ geçidi bölgesel ağ geçidi olarak dağıtılır ve ağ geçidine yerleştirilmiş bölge yedekliliği yoktur.

## <a name="faq"></a><a name="faq"></a>SSS

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Bu yeni SKU 'Ları dağıtırken ne değişecektir?

Perspektifinden, ağ geçitlerini bölge yedekliliği ile dağıtabilirsiniz. Bu, ağ geçitlerinin tüm örneklerinin Azure Kullanılabilirlik Alanları üzerinden dağıtılacağı ve her kullanılabilirlik bölgesinin farklı bir hata ve güncelleştirme etki alanı olması anlamına gelir. Bu, ağ geçitlerinizin bölge hatalarıyla daha güvenilir, kullanılabilir ve dayanıklı olmasını sağlar.

### <a name="can-i-use-the-azure-portal"></a>Azure portal kullanabilir miyim?

Evet, yeni SKU 'Ları dağıtmak için Azure portal kullanabilirsiniz. Ancak, bu yeni SKU 'Ları yalnızca Azure Kullanılabilirlik Alanları olan Azure bölgelerinde görürsünüz.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Yeni SKU 'Ları kullanamadığı için hangi bölgeler kullanılabilir?

Yeni SKU 'Lar Azure Kullanılabilirlik Alanları-Orta ABD, Fransa Orta, Kuzey Avrupa, Batı Avrupa ve Batı ABD 2 bölgelere, Doğu ABD, Doğu ABD 2, Güneydoğu Asya, Japonya Doğu, UK Güney bulunan Azure bölgelerinde kullanılabilir. Bu işlem, bölgesel olarak yedekli ağ geçitlerini diğer Azure ortak bölgelerinde sizin için kullanılabilir hale yapacağız.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Var olan sanal ağ geçitlerini, bölgesel olarak yedekli veya bölgesel ağ geçitlerine değiştirebilir/geçirebilir/yükseltebilir miyim?

Mevcut sanal ağ geçitlerinizi bölgesel olarak yedekli veya bölgesel ağ geçitlerine geçirme işlemi şu anda desteklenmiyor. Bununla birlikte, mevcut ağ geçidinizi silebilir ve bölgesel olarak yedekli veya bölgesel ağ geçidini yeniden oluşturabilirsiniz.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Aynı sanal ağda hem VPN hem de hızlı rota ağ geçitlerini dağıtabilir miyim?

Aynı sanal ağ üzerinde hem VPN hem de hızlı rota ağ geçitlerinin birlikte bulunması desteklenir. Ancak, ağ geçidi alt ağı için bir/27 IP adresi aralığı ayırmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[Alanlar arası yedekli sanal ağ geçidi oluşturma](create-zone-redundant-vnet-gateway.md)

---
title: Azure Kullanılabilirlik Alanları 'de bölge yedekli sanal ağ geçitleri hakkında | Microsoft Docs
description: Kullanılabilirlik Alanları VPN Gateway ve ExpressRoute ağ geçitleri hakkında bilgi edinin.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: d076e2b0057f0ba666fa47ffd0b3d7d1fcc14631
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725592"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Azure Kullanılabilirlik Alanları 'de bölge yedekli sanal ağ geçitleri hakkında

VPN ve ExpressRoute ağ geçitlerini [Azure kullanılabilirlik alanları](../availability-zones/az-overview.md)dağıtabilirsiniz. Bu, sanal ağ geçitleri için esneklik, ölçeklenebilirlik ve daha yüksek kullanılabilirlik sağlar. Ağ geçitlerini Azure Kullanılabilirlik Alanları fiziksel olarak dağıtmak ve bölge düzeyindeki hatalardan Azure ile şirket içi ağ bağlanabilirliğini korurken bir bölgedeki ağ geçitlerini mantıksal olarak ayırır.

### <a name="zrgw"></a>Bölgesel olarak yedekli ağ geçitleri

Sanal ağ geçitlerini kullanılabilirlik alanları arasında otomatik olarak dağıtmak için, bölgesel olarak yedekli sanal ağ geçitleri kullanabilirsiniz. Bölgesel olarak yedekli ağ geçitleri sayesinde, Azure 'da görev açısından kritik, ölçeklenebilir hizmetlerinize erişmek için bölge esnekliği avantajlarından yararlanabilirsiniz.

<br>
<br>

![bölgesel olarak yedekli ağ geçitleri grafiği](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Zonal ağ geçitleri

Belirli bir bölgedeki ağ geçitlerini dağıtmak için, bölgesel ağ geçitlerini kullanabilirsiniz. Bir bölgesel ağ geçidi dağıttığınızda, ağ geçidinin tüm örnekleri aynı Kullanılabilirlik bölgesine dağıtılır.

<br>
<br>

![Bölgesel ağ geçitleri grafiği](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Ağ Geçidi SKU'ları

Bölgesel olarak yedekli ve bölgesel ağ geçitleri, yeni ağ geçidi SKU 'ları olarak kullanılabilir. Azure AZ bölgelere yeni sanal ağ geçidi SKU 'Ları ekledik. Bu SKU 'lar ExpressRoute ve VPN Gateway için ilgili mevcut SKU 'lara benzer ve bunlar, bölgesel olarak yedekli ve bölgesel ağ geçitlerine özgü olmaları dışında.

Yeni ağ geçidi SKU 'Ları şunlardır:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Genel IP SKU 'Ları

Bölgesel olarak yedekli ağ geçitleri ve bölgesel ağ geçitleri, Azure genel IP kaynak *standardı* SKU 'sunu kullanır. Azure genel IP kaynağının yapılandırması, dağıttığınız ağ geçidinin bölge yedekli veya zonal olup olmadığını belirler. *Temel* SKU ile genel bir IP kaynağı oluşturursanız ağ geçidinde hiçbir bölge artıklığı olmaz ve ağ geçidi kaynakları bölgesel olur.

### <a name="pipzrg"></a>Bölgesel olarak yedekli ağ geçitleri

Bir bölge belirtmeden **Standart** genel IP SKU 'su kullanılarak genel bir IP adresi oluşturduğunuzda, bu davranış, ağ GEÇIDININ bir VPN ağ geçidi veya ExpressRoute ağ geçidi olmasına bağlı olarak farklılık gösterir. 

* Bir VPN ağ geçidi için, iki ağ geçidi örneği, bölge artıklığı sağlamak üzere bu üç bölgeden herhangi bir 2 ' ye dağıtılır. 
* ExpressRoute ağ geçidi için, iki taneden fazla örnek olduğundan, ağ geçidi üç bölgenin tamamına yayılabilir.

### <a name="pipzg"></a>Zonal ağ geçitleri

**Standart** genel IP SKU 'sunu kullanarak genel bir IP adresi oluşturduğunuzda ve bölgeyi (1, 2 veya 3) belirttiğinizde, tüm ağ geçidi örnekleri aynı bölgeye dağıtılır.

### <a name="piprg"></a>Bölgesel ağ geçitleri

**Temel** genel IP SKU 'su kullanılarak genel bir IP adresi oluşturduğunuzda, ağ geçidi bölgesel ağ geçidi olarak dağıtılır ve ağ geçidine yerleştirilmiş bölge yedekliliği yoktur.

## <a name="faq"></a>SSS

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

## <a name="next-steps"></a>Sonraki Adımlar

[Alanlar arası yedekli sanal ağ geçidi oluşturma](create-zone-redundant-vnet-gateway.md)

---
title: Azure Sanal Ağ
description: Adres alanı, alt ağlar, bölgeler ve abonelikler dahil olmak üzere Azure sanal ağ kavramları ve özellikleri hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 927ff80a544ef8fbff348aeb8781f8a55c84e109
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061902"
---
# <a name="what-is-azure-virtual-network"></a>Azure Sanal Ağı nedir?

Azure sanal ağı (VNet), Azure 'daki özel ağınız için temel yapı taşdır. VNet, Azure sanal makineler (VM) gibi birçok Azure Kaynak türünün birbirleriyle güvenli bir şekilde iletişim kurmasına olanak sağlar, internet ve şirket içi ağlar. VNet, kendi veri merkezinizde işlemenizi sağlayan geleneksel bir ağa benzer, ancak Azure 'un ölçek, kullanılabilirlik ve yalıtım gibi ek avantajlarından yararlanıyor.

## <a name="why-use-an-azure-virtual-network"></a>Azure sanal ağını neden kullanmalısınız?
Azure sanal ağ, Azure kaynaklarının birbirleriyle, internet ve şirket içi ağlarla güvenli bir şekilde iletişim kurmasına olanak sağlar. Bir sanal ağ ile gerçekleştirebileceğiniz önemli senaryolar; internet ile Azure kaynaklarıyla iletişim, Azure kaynakları arasında iletişim, şirket içi kaynaklarla iletişim, ağ trafiğini filtreleme, ağ trafiği yönlendirme ve Azure hizmetleriyle tümleştirme.

### <a name="communicate-with-the-internet"></a>İnternet ile iletişim kurma

Bir sanal ağ içindeki tüm kaynaklar varsayılan olarak Internet ile giden iletişim kurabilir. Bir kaynağa genel IP adresi veya genel Load Balancer atayarak o kaynağa gelen yönde iletişim kurabilirsiniz. Giden bağlantılarınızı yönetmek için genel IP adresi veya genel Load Balancer da kullanabilirsiniz.  Azure'daki giden bağlantılar hakkında daha fazla bilgi edinmek için bkz. [Giden bağlantılar](../load-balancer/load-balancer-outbound-connections.md), [Genel IP adresleri](virtual-network-public-ip-address.md) ve [Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Yalnızca sistem içi [Standart Load Balancer](../load-balancer/load-balancer-overview.md) kullanıldığında [giden bağlantıların](../load-balancer/load-balancer-outbound-connections.md) örnek düzeyinde genel IP veya genel Load Balancer ile nasıl çalışacağını tanımlamadığınız sürece giden bağlantı kullanılamaz.

### <a name="communicate-between-azure-resources"></a>Azure kaynakları arasında iletişim kurma

Azure kaynakları, aşağıdaki yöntemlerden birini uygulayarak birbiriyle güvenli şekilde iletişim kurar:

- **Bir sanal ağ üzerinden**: Azure App Service Ortamları, Azure Kubernetes Service (AKS) ve Azure Sanal Makine Ölçek Kümeleri gibi sanal makineleri ve diğer birçok türde Azure kaynağını bir sanal ağa dağıtabilirsiniz. Bir sanal ağa dağıtabileceğiniz Azure kaynaklarının tam listesini görüntülemek için bkz. [Sanal ağ hizmeti tümleştirmesi](virtual-network-for-azure-services.md).
- **Bir sanal ağ hizmeti uç noktası aracılığıyla**: sanal ağınızın özel adres alanınızı ve sanal ağınızın kimliğini Azure depolama hesapları ve Azure SQL veritabanı gibi Azure hizmet kaynaklarına, doğrudan bir bağlantı üzerinden genişletin. Hizmet uç noktaları, kritik Azure hizmeti kaynaklarınızı yalnızca bir sanal ağla sınırlayarak güvenliğini sağlamanıza imkan verir. Daha fazla bilgi için bkz. [Sanal ağ hizmet uç noktalarına genel bakış](virtual-network-service-endpoints-overview.md).
- **VNET eşlemesi aracılığıyla**: sanal ağları birbirlerine bağlanarak sanal ağ eşlemesi kullanarak sanal ağların birbirleriyle iletişim kurmasına olanak sağlayabilirsiniz. Bağlandığınız sanal ağlar aynı veya farklı Azure bölgelerinde bulunabilir. Daha fazla bilgi edinmek için bkz. [Sanal ağ eşlemesi](virtual-network-peering-overview.md).

### <a name="communicate-with-on-premises-resources"></a>Şirket içi kaynaklarla iletişim kurma

Aşağıdaki seçenekleri bir arada kullanarak şirket içi bilgisayarlarınızı ve ağlarınızı bir sanal ağa bağlayabilirsiniz:

- **Noktadan siteye sanal özel ağ (VPN):** Ağınızdaki tek bir bilgisayar ile sanal ağ arasında oluşur. Bir sanal ağ ile bağlantı kurmak isteyen her bilgisayar, bağlantısını yapılandırmalıdır. Azure’ı kullanmaya yeni başladıysanız bu bağlantı türü mükemmeldir. Mevcut ağınız üzerinde çok az bir değişiklik gerektirdiğinden veya hiç değişiklik gerektirmediğinden geliştiriciler için de mükemmeldir. Bilgisayarınız ile bir sanal ağ arasındaki iletişim, İnternet üzerinden şifrelenmiş bir tünel aracılığıyla gönderilir. Daha fazla bilgi için bkz. [Noktadan siteye VPN](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#).
- **Siteden siteye VPN:** Şirket içi VPN cihazınız ile bir sanal ağda dağıtılan Azure VPN Gateway arasında oluşur. Bu bağlantı türü, yetkilendirdiğiniz şirket içi kaynakların bir sanal ağa erişmesini sağlar. Şirket içi VPN cihazınız ile Azure VPN ağ geçidi arasındaki iletişim, İnternet üzerinden şifrelenmiş bir tünel aracılığıyla gönderilir. Daha fazla bilgi için bkz. [Siteden siteye VPN](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** Bir ExpressRoute iş ortağı aracılığıyla ağınız ile Azure arasında oluşur. Bu bağlantı özeldir. Trafik, İnternet üzerinden geçmez. Daha fazla bilgi edinmek için bkz. [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="filter-network-traffic"></a>Ağ trafiğini filtreleme

Aşağıdaki seçeneklerden birini veya her ikisini de kullanarak alt ağlar arasındaki ağ trafiğini filtreleyebilirsiniz:

- **Ağ güvenlik grupları:** Ağ güvenlik grupları ve uygulama güvenlik grupları, kaynak ve hedef IP adresi, bağlantı noktası ve protokole göre kaynaklardan gelen ve giden trafiği filtrelemenizi sağlayan birden çok gelen ve giden güvenlik kuralına sahip olabilir. Daha fazla bilgi için bkz. [ağ güvenlik grupları](./network-security-groups-overview.md#network-security-groups) veya [uygulama güvenlik grupları](./network-security-groups-overview.md#application-security-groups).
- **Ağ sanal gereçleri:** Ağ sanal gereci; güvenlik duvarı, WAN iyileştirmesi veya diğer ağ işlevi gibi ağ işlevlerini gerçekleştiren bir sanal makinedir. Bir sanal ağda dağıtabileceğiniz kullanılabilir ağ sanal gereçleri listesini görüntülemek için bkz. [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

### <a name="route-network-traffic"></a>Ağ trafiğini yönlendirme

Azure varsayılan olarak alt ağlar, bağlı sanal ağlar, şirket içi ağlar ve İnternet arasındaki trafiği yönlendirir. Azure’ın oluşturduğu varsayılan rotaları geçersiz kılmak için aşağıdaki seçeneklerden birini veya her ikisini uygulayabilirsiniz:

- **Rota tabloları:** Her bir alt ağ için trafiğin nereye yönlendirileceğini denetleyen rotalarla özel rota tabloları oluşturabilirsiniz. [Rota tabloları](virtual-networks-udr-overview.md#user-defined) hakkında daha fazla bilgi edinin.
- **Sınır ağ geçidi protokolü (BGP) rotaları:** Azure VPN Gateway veya ExpressRoute bağlantısı kullanarak sanal ağınızı şirket içi ağınıza bağlarsanız, şirket içi BGP rotalarınızı sanal ağlarınıza yayabilirsiniz. [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) ile BGP’yi kullanma hakkında daha fazla bilgi edinin.

### <a name="virtual-network-integration-for-azure-services"></a>Azure hizmetleri için sanal ağ tümleştirmesi

Azure hizmetleri 'ni bir Azure sanal ağı ile tümleştirmek, sanal makinelerden hizmete özel erişim sağlar veya sanal ağdaki işlem kaynaklarını sağlar.
Sanal ağınızdaki Azure hizmetlerini aşağıdaki seçeneklerle tümleştirebilirsiniz:
- [Hizmetin adanmış örneklerini](virtual-network-for-azure-services.md) bir sanal ağa dağıtma. Daha sonra hizmetlere sanal ağ ve şirket içi ağlardan özel olarak erişilebilir.
- [Özel bağlantıyı](../private-link/private-link-overview.md) kullanarak sanal ağınızdan ve şirket içi ağlardan gelen hizmetin belirli bir örneğine özel olarak erişin.
- Hizmet [uç noktaları](virtual-network-service-endpoints-overview.md)aracılığıyla bir sanal ağı hizmete genişleterek genel uç noktaları kullanarak hizmete erişebilirsiniz. Hizmet uç noktaları, hizmet kaynaklarının sanal ağla güvenliğini sağlar.
 

## <a name="azure-vnet-limits"></a>Azure VNet sınırları

Dağıtabileceğiniz Azure kaynakları sayısında belirli sınırlar vardır. Azure ağ sınırlarının çoğu en büyük değer olan değerlerdir. Ancak, [VNET sınırları sayfasında](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)belirtilen [bazı ağ sınırlarını artırabilirsiniz](../azure-portal/supportability/networking-quota-requests.md) . 

## <a name="pricing"></a>Fiyatlandırma

Azure VNet kullanımı ücretsizdir, ücretsiz olarak ücretlendirilir. Standart ücretler, sanal makineler (VM 'Ler) ve diğer ürünler gibi kaynaklar için geçerlidir. Daha fazla bilgi için bkz. [VNET fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-network/) ve Azure [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/).

## <a name="next-steps"></a>Sonraki adımlar
 - [Azure sanal ağ kavramları ve en iyi uygulamalar](concepts-and-best-practices.md) hakkında bilgi edinin.
 - Bir sanal ağı kullanmaya başlamak için bir sanal ağ oluşturun, bu sanal ağa birkaç sanal makine dağıtın ve sanal makineler arasında iletişim kurun. Nasıl olduğunu öğrenmek için [Sanal ağ oluşturma](quick-create-portal.md) başlıklı hızlı başlangıca bakın.

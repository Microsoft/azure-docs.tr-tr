---
title: Azure ağ hizmetlerine genel bakış
description: Bağlantı, uygulama koruması, uygulama teslimi ve ağ izleme hizmetleri dahil olmak üzere Azure 'daki ağ hizmetleri hakkında bilgi edinin.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/07/2021
ms.author: kumud
ms.openlocfilehash: 34e31bacbb276feb5b11d1b955096877376e989b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107712"
---
# <a name="azure-networking-services-overview"></a>Azure ağ hizmetlerine genel bakış

Azure 'daki ağ hizmetleri, birlikte veya ayrı olarak kullanılabilecek çeşitli ağ özellikleri sağlar. Bunlarla ilgili daha fazla bilgi edinmek için aşağıdaki önemli yeteneklerden birine tıklayın:
- [**Bağlantı Hizmetleri**](#connect): Azure sanal ağ (VNet), sanal WAN, expressroute, VPN Gateway, sanal ağ NAT ağ geçidi, Azure DNS, eşleme hizmeti ve Azure savunma 'da bu ağ hizmetlerinin herhangi birini veya birleşimini kullanarak Azure kaynaklarını ve şirket içi kaynakları bağlayın.
- [**Uygulama koruma hizmetleri**](#protect): Azure-Load Balancer, özel bağlantı, DDoS koruması, güvenlik duvarı, ağ güvenlik grupları, Web uygulaması güvenlik duvarı ve sanal ağ uç noktalarında bu ağ hizmetlerinin herhangi birini veya birleşimini kullanarak uygulamalarınızı koruyun.
- [**Uygulama Teslim Hizmetleri**](#deliver): azure-Content DELIVERY Network (CDN), Azure ön kapı hizmeti, Traffic Manager, Application Gateway, Internet çözümleyicisi ve Load Balancer bu ağ hizmetlerinin herhangi birini veya birleşimini kullanarak Azure ağı 'nda uygulamalar sunun.
- [**Ağ izleme**](#monitor): Azure-ağ Izleyicisi, ExpressRoute Izleyicisi, Azure Izleyici veya VNET Terminal erişim noktası (TAP) ' de bu ağ hizmetlerinin herhangi birini veya birleşimini kullanarak ağ kaynaklarınızı izleyin.

## <a name="connectivity-services"></a><a name="connect"></a>Bağlantı hizmetleri
 
Bu bölümde, Azure kaynakları, şirket içi ağdan Azure kaynaklarına bağlantı ve Azure sanal ağ (VNet), ExpressRoute, VPN Gateway, sanal WAN, sanal ağ NAT ağ geçidi, Azure DNS, Azure eşleme hizmeti ve Azure 'da şube bağlantısı arasında bağlantı sağlayan hizmetler açıklanmaktadır.


### <a name="virtual-network"></a><a name="vnet"></a>Sanal ağ

Azure sanal ağı (VNet), Azure 'daki özel ağınız için temel yapı taşdır. Bir sanal ağları kullanarak şunları yapabilirsiniz:
- **Azure kaynakları arasında Iletişim kurma**: VM 'leri ve diğer birçok Azure kaynağı türünü, Azure App Service ortamları, Azure Kubernetes hizmeti (aks) ve Azure sanal makine ölçek kümeleri gibi sanal bir ağa dağıtabilirsiniz. Bir sanal ağa dağıtabileceğiniz Azure kaynaklarının tam listesini görüntülemek için bkz. [Sanal ağ hizmeti tümleştirmesi](../../virtual-network/virtual-network-for-azure-services.md).
- Birbirleriyle **Iletişim kurma**: sanal ağları birbirlerine bağlanarak sanal ağ eşleme kullanarak her iki sanal ağdaki kaynakların birbirleriyle iletişim kurmasına olanak sağlayabilirsiniz. Bağlandığınız sanal ağlar aynı veya farklı Azure bölgelerinde bulunabilir. Daha fazla bilgi için bkz. [sanal ağ eşlemesi](../../virtual-network/virtual-network-peering-overview.md).
- **İnternet Ile Iletişim kurma**: bir sanal ağ içindeki tüm kaynaklar varsayılan olarak Internet ile giden iletişim kurabilir. Bir kaynağa genel IP adresi veya genel Load Balancer atayarak o kaynağa gelen yönde iletişim kurabilirsiniz. Giden bağlantılarınızı yönetmek için [genel IP adresleri](../../virtual-network/virtual-network-public-ip-address.md) veya genel [Load Balancer](../../load-balancer/load-balancer-overview.md) de kullanabilirsiniz.
- Şirket **içi ağlarla Iletişim kurma**: [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../../expressroute/expressroute-introduction.md)kullanarak şirket içi bilgisayarlarınızı ve ağlarınızı sanal bir ağa bağlayabilirsiniz.

Daha fazla bilgi için bkz. [Azure sanal ağı nedir?](../../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute, şirket içi ağlarınızı bir bağlantı sağlayıcısı tarafından kolaylaştırarak özel bir bağlantı üzerinden Microsoft bulutuna genişletmenizi sağlar. Bu bağlantı özeldir. Trafik, İnternet üzerinden geçmez. ExpressRoute'u kullanarak Microsoft Azure, Microsoft 365 ve Dynamics 365 gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz.  Daha fazla bilgi için bkz. [ExpressRoute nedir?](../../expressroute/expressroute-introduction.md).

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway, şirket içi konumlardan sanal ağınıza yönelik şifrelenmiş şirketler arası bağlantılar oluşturmanıza veya VNET 'ler arasında şifrelenmiş bağlantılar oluşturmanıza yardımcı olur. Siteden siteye, Noktadan siteye veya VNet 'ten VNet 'e kadar VPN Gateway bağlantı için kullanılabilir farklı yapılandırma vardır.
Aşağıdaki diyagramda aynı sanal ağa ait birden çok siteden siteye VPN bağlantısı gösterilmektedir.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Siteden siteye Azure VPN Gateway bağlantıları":::

Farklı türlerde VPN bağlantıları hakkında daha fazla bilgi için bkz. [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Sanal WAN
Azure sanal WAN, Azure ile ve arasında iyileştirilmiş ve otomatik şube bağlantısı sağlayan bir ağ hizmetidir. Azure bölgeleri, Dallarınızı bağlamak için seçebileceğiniz hub olarak görev yapar. Aynı zamanda dalları bağlamak ve şube-VNet bağlantısının avantajlarından yararlanmak için Azure omurgasına sahip olabilirsiniz. Azure sanal WAN, tek bir işletim arabirimine siteden siteye VPN, ExpressRoute, Noktadan siteye kullanıcı VPN gibi birçok Azure bulut bağlantısı hizmeti sunar. Azure VNet bağlantısı, sanal ağ bağlantıları kullanılarak oluşturulur. Daha fazla bilgi için bkz. [Azure sanal WAN nedir?](../../virtual-wan/virtual-wan-about.md).

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Sanal WAN diyagramı":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS, Microsoft Azure altyapısını kullanarak ad çözümlemesi sağlayan DNS etki alanları için bir barındırma hizmetidir. Etki alanlarınızı Azure'da barındırarak DNS kayıtlarınızı diğer Azure hizmetlerinde kullandığınız kimlik bilgileri, API’ler, araçlar ve faturalarla yönetebilirsiniz. Daha fazla bilgi için bkz. [Azure DNS nedir?](../../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure savunma hizmeti, sanal ağınızın içinde sağladığınız yeni, platform tarafından yönetilen yeni bir PaaS hizmetidir. Doğrudan TLS üzerinden Azure portal sanal makinelerinize güvenli ve sorunsuz RDP/SSH bağlantısı sağlar. Azure Bastion aracılığıyla bağlandığınızda, sanal makinelerinizin bir genel IP adresi olması gerekmez. Daha fazla bilgi için bkz. Azure savunma nedir [?](../../bastion/bastion-overview.md).

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure savunma mimarisi":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Sanal ağ NAT ağ geçidi
Sanal ağ NAT (ağ adresi çevirisi), sanal ağlar için yalnızca giden Internet bağlantısını basitleştirir. Bir alt ağda yapılandırıldığında, tüm giden bağlantılar belirttiğiniz statik genel IP adreslerini kullanır. Doğrudan sanal makinelere bağlı yük dengeleyici veya genel IP adresleri olmadan giden bağlantı kurulabilir. Daha fazla bilgi için bkz. [sanal ağ NAT ağ geçidi nedir?](../../virtual-network/nat-overview.md).

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Sanal ağ NAT ağ geçidi":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure eşleme hizmeti
Azure eşleme hizmeti Microsoft 365, Dynamics 365, hizmet olarak yazılım (SaaS) Hizmetleri, Azure veya genel İnternet üzerinden erişilebilen herhangi bir Microsoft hizmeti gibi Microsoft bulut hizmetleriyle müşteri bağlantısını geliştirir. Daha fazla bilgi için bkz. [Azure eşleme hizmeti nedir?](../../peering-service/about.md).

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zones

Azure Edge bölgesi, kullanıcıya veri işlemeyi kapatma imkanı sağlayan Microsoft Azure bir teklif ailesidir. Uygulamaların düşük gecikme süresi ve yüksek performans gereksinimlerini karşılamak için VM 'Leri, kapsayıcıları ve diğer seçili Azure hizmetlerini uç bölgelere dağıtabilirsiniz.

### <a name="azure-orbital"></a><a name="orbital"></a>Azure Orbisi

Azure Orbte, Spacecraft veya uydu yarışmaları ile iletişim kurmanıza, veri bağlama ve yukarı akış, verileri bulutta işleme, Azure hizmetleri ile farklı senaryolarda zincir oluşturma ve müşterileriniz için ürün üretmenizi sağlayan bir hizmet olarak tam olarak yönetilen bulut tabanlı bir taban istasyondur. Bu sistem, Azure genel altyapısının ve düşük gecikmeli küresel fiber ağın üzerine kurulmuştur.

## <a name="application-protection-services"></a><a name="protect"></a>Uygulama koruma hizmetleri

Bu bölümde, Azure 'da ağ kaynaklarınızı korumaya yardımcı olan ağ hizmetleri açıklanmaktadır-Azure-DDoS koruması, özel bağlantı, güvenlik duvarı, Web uygulaması güvenlik duvarı, ağ güvenlik grupları ve sanal ağ hizmeti uç noktalarında bu ağ hizmetlerinin herhangi birini veya birleşimini kullanarak uygulamalarınızı koruyun.

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS koruması 
[Azure DDoS koruması](../../ddos-protection/manage-ddos-protection.md) , en gelişmiş DDoS tehditlerine karşı karşı önlemler sağlar. Hizmet, uygulamanız ve sanal ağlarınızda dağıtılan kaynaklarınız için gelişmiş DDoS azaltma özellikleri sağlar. Ayrıca, Azure DDoS koruması kullanan müşterilerin, etkin bir saldırı sırasında DDoS uzmanlarını sağlamak için DDoS hızlı yanıt desteğine erişimi vardır.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="DDoS koruması":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure özel bağlantısı
[Azure özel bağlantısı](../../private-link/private-link-overview.md) , Azure PaaS hizmetlerine (örneğin, Azure depolama ve SQL veritabanı) ve Azure 'da barındırılan, müşteriye ait/iş ortağı hizmetlerine sanal ağınızdaki özel bir uç nokta üzerinden erişmenizi sağlar.
Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağını de dolaşır. Hizmetinizi genel İnternet 'e sunma artık gerekli değildir. Kendi özel bağlantı hizmetinizi sanal ağınızda oluşturabilir ve müşterilerinize iletebilirsiniz.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Özel uç noktaya genel bakış":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure Güvenlik Duvarı
Azure Güvenlik Duvarı, Azure Sanal Ağ kaynaklarınızı koruyan yönetilen, bulut tabanlı bir güvenlik hizmetidir. Azure Güvenlik Duvarı 'nı kullanarak, abonelikler ve sanal ağlar arasında merkezi olarak uygulama ve ağ bağlantısı ilkeleri oluşturabilir, uygulayabilir ve bunları günlüğe kaydedebilirsiniz. Azure Güvenlik Duvarı, dış güvenlik duvarlarının sanal ağınızdan kaynaklanan trafiği tanımlamasına olanak tanımak amacıyla sanal ağ kaynaklarınız için statik genel bir IP adresi kullanır. 

Azure Güvenlik Duvarı hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı belgeleri](../../firewall/overview.md).

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Güvenlik duvarına genel bakış":::

### <a name="web-application-firewall"></a><a name="waf"></a>Web Uygulaması Güvenlik Duvarı
[Azure Web uygulaması güvenlik duvarı](../../web-application-firewall/overview.md) (WAF), Web UYGULAMALARıNıZ için SQL ekleme ve siteler arası betik oluşturma gibi sık kullanılan Web açıklarından ve güvenlik açıklarına karşı koruma sağlar. Azure WAF, yönetilen kurallar aracılığıyla OWASP ilk 10 güvenlik açığına karşı kullanıma hazır koruma sağlar. Ayrıca müşteriler, kaynak IP aralığına dayalı ek koruma sağlamak için müşteri tarafından yönetilen kurallar ve üstbilgiler, tanımlama bilgileri, form veri alanları veya sorgu dizesi parametreleri gibi özel kurallar da yapılandırabilir.

Müşteriler, ortak ve özel adres alanındaki varlıklara bölgesel koruma sağlayan [Application Gateway Azure WAF](../../web-application-firewall/ag/ag-overview.md) 'yi dağıtmayı seçebilirler. Müşteriler Ayrıca, ağ ucunda genel uç noktalara koruma sağlayan [ön kapılı Azure WAF](../../web-application-firewall/afds/afds-overview.md) 'i dağıtmayı tercih edebilir.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Web Uygulaması Güvenlik Duvarı":::

### <a name="network-security-groups"></a><a name="nsg"></a>Ağ güvenlik grupları
Ağ güvenlik grupları ile bir Azure sanal ağı içindeki Azure kaynaklarına gelen ve bu kaynaklardan giden ağ trafiğini filtreleyebilirsiniz. Daha fazla bilgi için bkz. [ağ güvenlik grupları](../../virtual-network/network-security-groups-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Hizmet uç noktaları
Sanal Ağ hizmet uç noktaları, sanal ağ özel adres alanınızı ve sanal ağınızın kimliğini doğrudan bağlantı üzerinden Azure hizmetlerine genişletir. Uç noktalar kritik Azure hizmeti kaynaklarınızı sanal ağlarınızla sınırlayarak güvenliğini sağlamanıza imkan verir. Sanal ağınızdan Azure hizmetine giden trafik her zaman Microsoft Azure omurga ağında kalır. Daha fazla bilgi için bkz. [sanal ağ hizmeti uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md).

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Sanal ağ hizmet uç noktaları":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Uygulama Teslim Hizmetleri

Bu bölümde, Azure 'da uygulamalar (Content Delivery Network, Azure ön kapı hizmeti, Traffic Manager, Load Balancer ve Application Gateway sunmaya yardımcı olan ağ hizmetleri açıklanmaktadır.

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Azure Content Delivery Network (CDN), dünya genelindeki stratejik olarak yerleştirilen fiziksel düğümlerde içeriği önbelleğe alarak, yüksek bant genişliği içeriğinin hızlı bir şekilde kullanıcılara teslimi konusunda geliştiricilere genel bir çözüm sunar. Azure CDN hakkında daha fazla bilgi için bkz. [Azure Content Delivery Network](../../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure CDN":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Hizmeti
Azure Front Door Service, yüksek kullanılabilirliğe yönelik en iyi performans ve anında genel yük devretme için iyileştirerek, web trafiğinizin genel yönlendirmesini tanımlamanızı, yönetmenizi ve izlemenizi sağlar. Front Door ile, genel (çok bölgeli) tüketici ve kurumsal uygulamalarınızı Azure ile küresel çapta bir hedef kitleye ulaşan sağlam, yüksek performanslı, kişiselleştirilmiş modern uygulamalara, API’lere ve içeriğe dönüştürebilirsiniz. Daha fazla bilgi için bkz. [Azure ön kapısı](../../frontdoor/front-door-overview.md).

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Ön kapı hizmetine genel bakış":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager, trafiği farklı Azure bölgelerindeki hizmetlere en uygun şekilde dağıtırken yüksek kullanılabilirlik ve yanıtlama hızı sağlayan DNS tabanlı bir trafik yük dengeleyicidir. Traffic Manager öncelik, ağırlıklı, performans, coğrafi, çok değerli veya alt ağ gibi trafiği dağıtmak için bir dizi trafik yönlendirme yöntemi sağlar. Trafik yönlendirme yöntemleri hakkında daha fazla bilgi için bkz. [Traffic Manager yönlendirme yöntemleri](../../traffic-manager/traffic-manager-routing-methods.md).

Aşağıdaki diyagramda Traffic Manager ile Endpoint Priority tabanlı yönlendirme gösterilmektedir:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Azure Traffic Manager ' Priority ' trafiği yönlendirme yöntemi":::

Traffic Manager hakkında daha fazla bilgi için bkz. [Azure Traffic Manager nedir?](../../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer, tüm UDP ve TCP protokolleri için yüksek performanslı ve düşük gecikmeli katman 4 yük dengelemesi sağlar. Gelen ve giden bağlantıları yönetir. Ortak ve iç yük dengeli uç noktaları yapılandırabilirsiniz. Hizmet kullanılabilirliğini yönetmek için TCP ve HTTP sistem durumu-yoklama seçeneklerini kullanarak, arka uç havuzu hedeflerine gelen bağlantıları eşlemek için kurallar tanımlayabilirsiniz. Load Balancer hakkında daha fazla bilgi edinmek için [Load Balancer genel bakış](../../load-balancer/load-balancer-overview.md) makalesini okuyun.

Aşağıdaki resimde hem dış hem de iç yük dengeleyicileri kullanan Internet 'e yönelik çok katmanlı bir uygulama gösterilmektedir:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure Load Balancer örneği":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway, web uygulamalarınıza trafiği yönetmenizi sağlayan bir web trafiği yük dengeleyicisidir. Bu bir hizmet olarak uygulama teslim denetleyicisi (ADC), uygulamalarınız için farklı katman 7 Yük Dengeleme özelliği sunar. Daha fazla bilgi için bkz. [Azure Application Gateway nedir?](../../application-gateway/overview.md).

Aşağıdaki diyagramda Application Gateway ile URL yolu tabanlı yönlendirme gösterilmektedir.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Application Gateway örneği":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Ağ izleme hizmetleri
Bu bölümde, Azure 'da ağ kaynaklarınızı izlemeye yardımcı olan ağ hizmetleri açıklanmaktadır-ağ Izleyicisi, Azure Izleyici ağ öngörüleri, Azure Izleyici, ExpressRoute Izleyicisi ve sanal ağ dokunma.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Ağ İzleyicisi
Azure Ağ İzleyicisi, Azure sanal ağındaki kaynaklarda izleme, tanılama, ölçümleri görüntüleme ve günlükleri etkinleştirme veya devre dışı bırakma işlemleri için araçlar sağlar. Daha fazla bilgi için bkz. [Ağ İzleyicisi nedir?](../../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="azure-monitor-network-insights"></a>Azure Izleyici ağ öngörüleri
Ağlar için Azure Izleyici, tüm dağıtılan ağ kaynakları için herhangi bir yapılandırma gerektirmeden sistem durumu ve ölçümlerinin kapsamlı bir görünümünü sağlar. Ayrıca [bağlantı izleyici](../../network-watcher/connection-monitor-overview.md), [ağ güvenlik grupları için akış günlüğü](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)ve [Trafik Analizi](../../network-watcher/traffic-analytics.md)gibi ağ izleme özelliklerine erişim sağlar. Daha fazla bilgi için bkz. [Azure Izleyici ağ öngörüleri](../../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute Izleyicisi
ExpressRoute devre ölçümlerini, kaynak günlüklerini ve uyarılarını görüntüleme hakkında bilgi edinmek için bkz. [ExpressRoute izleme, ölçümler ve uyarılar](../../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Izleyici
Azure İzleyici, bulut ve şirket içi ortamlardaki telemetriyi toplamak, analiz etmek ve bunlarda eylem gerçekleştirmek için kapsamlı bir çözüm sunarak uygulamalarınızın kullanılabilirliğini ve performansını en üst düzeye çıkarır. Uygulamalarınızın performansını anlamanıza ve uygulamalarla bağlı oldukları kaynakları etkileyen sorunları önceden tespit etmenize yardımcı olur. Daha fazla bilgi için bkz. [Azure Izleyiciye genel bakış](../../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Sanal ağ dokunma
Azure sanal ağ TAP (Terminal erişim noktası), sanal makine ağ trafiğinizi bir ağ paketi toplayıcısına veya analiz aracına sürekli olarak akışla kullanmanıza olanak sağlar. Toplayıcı veya Analiz Aracı bir [ağ sanal gereç](https://azure.microsoft.com/solutions/network-appliances/) ortağı tarafından sağlanır.

Aşağıdaki görüntüde, sanal ağ TAP 'ın nasıl çalıştığı gösterilmektedir:

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Sanal ağ dokunma çalışma şekli":::

Daha fazla bilgi için bkz. [sanal ağ dokunma nedir](../../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- İlk sanal ağınızı oluşturun ve [ilk sanal ağınızı oluşturma](../../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) makalesindeki adımları tamamlayarak birkaç VM 'ye bağlanın.
- [Noktadan siteye bağlantı yapılandırma makalesindeki](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)adımları tamamlayarak bilgisayarınızı bir sanal ağa bağlayın.
- [İnternet 'e yönelik yük dengeleyici oluşturma](../../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) makalesindeki adımları tamamlayarak, internet trafiğinin genel sunuculara yükünü dengeleyin.
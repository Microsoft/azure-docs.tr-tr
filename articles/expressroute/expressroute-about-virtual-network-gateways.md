---
title: ExpressRoute sanal ağ geçitleri hakkında-Azure | Microsoft Docs
description: ExpressRoute için sanal ağ geçitleri hakkında bilgi edinin. Bu makale, ağ geçidi SKU 'Ları ve türleri hakkında bilgi içerir.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 796e35be12939920f9392e6f3ce4cae660415f80
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504727"
---
# <a name="about-expressroute-virtual-network-gateways"></a>ExpressRoute sanal ağ geçitleri hakkında

Azure Sanal ağınızı ve şirket içi ağınızı ExpressRoute aracılığıyla bağlamak için önce bir sanal ağ geçidi oluşturmanız gerekir. Bir sanal ağ geçidi iki amaca hizmet eder: ağlar arasında Exchange IP yolları ve ağ trafiği yönlendirme. Bu makalede, SKU 'ya göre ağ geçidi türleri, ağ geçidi SKU 'Ları ve tahmini performans açıklanmaktadır. Bu makalede ayrıca, şirket içi ağınızdan gelen ağ trafiğinin performansı artırmak için sanal ağ geçidini atlaması sağlayan bir özellik olan ExpressRoute [FastPath](#fastpath)açıklanır.

## <a name="gateway-types"></a>Ağ geçidi türleri

Bir sanal ağ geçidi oluşturduğunuzda, birkaç ayar belirtmeniz gerekir. Gerekli ayarlardan biri olan '-GatewayType ', ağ geçidinin ExpressRoute veya VPN trafiği için kullanılıp kullanılmayacağını belirtir. İki ağ geçidi türü şunlardır:

* **VPN** -genel Internet üzerinden şifrelenmiş trafik göndermek Için ' VPN ' ağ geçidi türünü kullanırsınız. Bu, VPN ağ geçidi olarak da adlandırılır. Siteden Siteye, Noktadan Siteye ve Sanal Ağdan Sanal Ağa bağlantıların tümü VPN ağ geçidi kullanır.

* **ExpressRoute** -özel bir bağlantıda ağ trafiği göndermek Için ' ExpressRoute ' ağ geçidi türünü kullanırsınız. Bu, ExpressRoute ağ geçidi olarak da adlandırılır ve ExpressRoute yapılandırılırken kullanılan ağ geçidi türüdür.

Bir sanal ağın her ağ geçidi türü için yalnızca bir sanal ağ geçidi olabilir. Örneğin, GatewayType Vpn kullanan bir sanal ağ geçidiniz ve GatewayType ExpressRoute kullanan bir sanal ağ geçidiniz olabilir.

## <a name="gateway-skus"></a><a name="gwsku"></a>Ağ Geçidi SKU’ları
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ağ geçidinizin daha güçlü bir ağ geçidi SKU 'suna yükseltmek istiyorsanız, çoğu durumda ' Resize-AzVirtualNetworkGateway ' PowerShell cmdlet 'ini kullanabilirsiniz. Bu, standart ve HighPerformance SKU 'Larına yükseltmeler için çalışacaktır. Ancak, UltraPerformance SKU 'suna yükseltmek için, ağ geçidini yeniden oluşturmanız gerekecektir. Bir ağ geçidinin yeniden oluşturulması kapalı kalma süresini doğurur.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Ağ Geçidi SKU 'SU tarafından tahmini performanons
Aşağıdaki tabloda ağ geçidi türleri ve tahmini performanons gösterilmektedir. Bu tablo hem Resource Manager, hem de klasik dağıtım modellerine uygulanır.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> * Sanal ağdaki sanal makinelerin sayısı, uzak ExpressRoute ağ geçidini kullanan eşlenmiş sanal ağlardaki VM 'Leri de içerir.
> * Uygulama performansı, uçtan uca gecikme süresi ve uygulamanın açtığı trafik akışı sayısı gibi birden çok etkene bağlıdır. Tablodaki sayılar, uygulamanın teorik olarak ideal bir ortamda elde edilebileceği üst sınırı temsil eder.
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Ağ geçidi alt ağı

ExpressRoute ağ geçidi oluşturmadan önce bir ağ geçidi alt ağı oluşturmanız gerekir. Ağ geçidi alt ağı, sanal ağ geçidi VM 'lerinin ve hizmetlerinin kullandığı IP adreslerini içerir. Sanal ağ geçidinizi oluşturduğunuzda, ağ geçidi VM 'Leri ağ geçidi alt ağına dağıtılır ve gerekli ExpressRoute ağ geçidi ayarlarıyla yapılandırılır. Ağ geçidi alt ağına hiçbir şeyi başka hiçbir şekilde (örneğin, ek VM 'Ler) dağıtmayın. Düzgün çalışması için ağ geçidi alt ağının ' GatewaySubnet ' olarak adlandırılması gerekir. Ağ geçidi alt ağının ' GatewaySubnet ' olarak adlandırılması, Azure 'un sanal ağ geçidi VM 'lerini ve hizmetlerini dağıtmak için bu alt ağ olduğunu bilmesini sağlar.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Ağ geçidi alt ağı oluştururken, alt ağın içerdiği IP adresi sayısını belirtirsiniz. Ağ geçidi alt ağındaki IP adresleri ağ geçidi VM 'lerine ve ağ geçidi hizmetlerine ayrılır. Bazı yapılandırmalar için diğerlerinden daha fazla IP adresi gerekir. 

Ağ geçidi alt ağınızın boyutunu planlarken, oluşturmayı planladığınız yapılandırma için belgelere bakın. Örneğin, ExpressRoute/VPN Gateway bir arada bulunan yapılandırma, diğer birçok yapılandırmadan daha büyük bir ağ geçidi alt ağı gerektirir. Ayrıca, ağ geçidi alt ağınızın olası gelecekteki ek yapılandırmalara uyum sağlamak için yeterli IP adresi içerdiğinden emin olmak isteyebilirsiniz. /29 kadar küçük bir ağ geçidi alt ağı oluşturabileceğiniz gibi, kullanılabilir adres alanınız varsa/27 veya daha büyük (/27,/26 vb.) bir ağ geçidi alt ağı oluşturmanızı öneririz. Ağ geçidinize 16 ExpressRoute devreleri bağlamayı planlıyorsanız,/26 veya daha büyük bir ağ geçidi alt ağı oluşturmanız **gerekir** . Çift yığın ağ geçidi alt ağı oluşturuyorsanız,/64 ya da daha büyük bir IPv6 aralığı kullanmanızı öneririz. Bu, çoğu yapılandırmaya uyum sağlayacaktır.

Aşağıdaki Kaynak Yöneticisi PowerShell örneği, GatewaySubnet adlı bir ağ geçidi alt ağını göstermektedir. CıDR gösteriminin bir/27 olduğunu görebilirsiniz. Bu, şu anda mevcut olan çoğu yapılandırma için yeterli IP adresi sağlar.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Bölge yedekli ağ geçidi SKU 'Ları

Ayrıca, ExpressRoute ağ geçitlerini Azure Kullanılabilirlik Alanları de dağıtabilirsiniz. Bu fiziksel ve mantıksal olarak farklı Kullanılabilirlik Alanları ayırır ve şirket içi ağ bağlantınızı bölge düzeyindeki hatalardan Azure ile koruyabilirsiniz.

![Bölge yedekli ExpressRoute ağ geçidi](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Bölgesel olarak yedekli ağ geçitleri ExpressRoute ağ geçidi için özel yeni ağ geçidi SKU 'Ları kullanır.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Yeni ağ geçidi SKU 'Ları, gereksinimlerinize en iyi şekilde eşleşen diğer dağıtım seçeneklerini de destekler. Yeni ağ geçidi SKU 'Larını kullanarak bir sanal ağ geçidi oluştururken, ağ geçidini belirli bir bölgeye dağıtma seçeneğiniz de vardır. Bu, bir bölgesel ağ geçidi olarak adlandırılır. Bir bölgesel ağ geçidi dağıttığınızda, ağ geçidinin tüm örnekleri aynı Kullanılabilirlik alanına dağıtılır.

> [!IMPORTANT]
> ExpressRoute üzerinden IPv6 tabanlı özel eşleme kullanmayı planlıyorsanız, bir çift yığın ağ geçidi alt ağına dağıttığınız ağ geçidi için AZ SKU seçtiğinizden emin olun.
> 
>

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

ExpressRoute sanal ağ geçidi, ağ yollarını Exchange ve ağ trafiğini yönlendirme için tasarlanmıştır. FastPath, şirket içi ağınız ve sanal ağınız arasındaki veri yolu performansını geliştirmek için tasarlanmıştır. Etkin olduğunda, FastPath ağ trafiğini, ağ geçidini atlayarak sanal ağdaki sanal makinelere doğrudan gönderir.

Sınırlamalar ve gereksinimler de dahil olmak üzere FastPath hakkında daha fazla bilgi için bkz. [FastPath hakkında](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API 'leri ve PowerShell cmdlet 'leri
Sanal ağ geçidi yapılandırmalarına yönelik REST API 'Leri ve PowerShell cmdlet 'lerini kullanırken ek teknik kaynaklar ve özel sözdizimi gereksinimleri için aşağıdaki sayfalara bakın:

| **Klasik** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/servicemanagement/azure.service/#azure) |[PowerShell](/powershell/module/az.network#networking) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/virtual-network/) |

## <a name="next-steps"></a>Sonraki adımlar

Kullanılabilir bağlantı yapılandırması hakkında daha fazla bilgi için bkz. [ExpressRoute 'A genel bakış](expressroute-introduction.md).

ExpressRoute ağ geçitleri oluşturma hakkında daha fazla bilgi için bkz. [ExpressRoute için sanal ağ geçidi oluşturma](expressroute-howto-add-gateway-resource-manager.md).

Bölge yedekli ağ geçitlerini yapılandırma hakkında daha fazla bilgi için bkz. bölgesel olarak [yedekli sanal ağ geçidi oluşturma](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

FastPath hakkında daha fazla bilgi için bkz. [FastPath hakkında](about-fastpath.md).

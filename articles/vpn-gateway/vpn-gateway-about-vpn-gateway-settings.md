---
title: 'Azure VPN Gateway: yapılandırma ayarları'
description: Azure sanal ağ geçitleri için VPN Gateway ayarları hakkında bilgi edinin.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: c72b85bc978191744c55afb73973caa6d3b45db6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150954"
---
# <a name="about-vpn-gateway-configuration-settings"></a>VPN Gateway yapılandırma ayarları hakkında

VPN Gateway, sanal ağınız ile şirket içi konumunuz arasında ortak bir bağlantı arasında şifrelenmiş trafik gönderen bir sanal ağ geçidi türüdür. Ayrıca, Azure omurga genelinde sanal ağlar arasında trafik göndermek için bir VPN ağ geçidi kullanabilirsiniz.

Bir VPN Ağ Geçidi bağlantısı, her biri yapılandırılabilir ayarları içeren birden fazla kaynak yapılandırmasına dayanır. Bu makaledeki bölümler, Kaynak Yöneticisi dağıtım modelinde oluşturulan bir sanal ağın VPN ağ geçidiyle ilgili kaynakları ve ayarları tartışır. Her bağlantı çözümü için açıklamaları ve topoloji diyagramlarını [hakkında VPN Gateway](vpn-gateway-about-vpngateways.md) makalesine ulaşabilirsiniz.

Bu makaledeki değerler VPN ağ geçitleri (-GatewayType VPN kullanan sanal ağ geçitleri) uygular. Bu makale tüm ağ geçidi türlerini veya bölgesel olarak yedekli ağ geçitlerini kapsamaz.

* -GatewayType ' ExpressRoute ' öğesine uygulanan değerler için bkz. [ExpressRoute Için sanal ağ geçitleri](../expressroute/expressroute-about-virtual-network-gateways.md).

* Bölge yedekli ağ geçitleri için bkz. bölgesel olarak [yedekli ağ geçitleri hakkında](about-zone-redundant-vnet-gateways.md).

* Sanal WAN için bkz. [sanal WAN hakkında](../virtual-wan/virtual-wan-about.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gwtype"></a>Ağ geçidi türleri

Her sanal ağ, her türde yalnızca bir sanal ağ geçidine sahip olabilir. Bir sanal ağ geçidi oluştururken, yapılandırmanız için ağ geçidi türünün doğru olduğundan emin olmanız gerekir.

-GatewayType için kullanılabilir değerler şunlardır:

* VPN
* ExpressRoute

VPN ağ geçidi `-GatewayType` *VPN*gerektirir.

Örnek:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>Ağ Geçidi SKU'ları

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Ağ Geçidi SKU 'SU yapılandırma

#### <a name="azure-portal"></a>Azure portal

Kaynak Yöneticisi sanal ağ geçidi oluşturmak için Azure portal kullanırsanız, açılan menüyü kullanarak ağ geçidi SKU 'sunu seçebilirsiniz. Size sunulan seçenekler, seçtiğiniz ağ geçidi türü ve VPN türüne karşılık gelir.

#### <a name="powershell"></a>PowerShell

Aşağıdaki PowerShell örneği, `-GatewaySku` VpnGw1 olarak belirtir. Bir ağ geçidi oluşturmak için PowerShell 'i kullanırken, önce IP yapılandırmasını oluşturmanız ve ardından başvurmak için bir değişken kullanmanız gerekir. Bu örnekte, yapılandırma değişkeni $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>SKU 'YU yeniden boyutlandırma veya değiştirme

VPN Gateway 'niz varsa ve farklı bir ağ geçidi SKU 'SU kullanmak istiyorsanız, seçenekleriniz ağ geçidi SKU 'nuzu yeniden boyutlandırırlar ya da başka bir SKU 'ya geçiş yapılır. Başka bir ağ geçidi SKU 'SU olarak değiştirdiğinizde, var olan ağ geçidini tamamen siler ve yeni bir tane oluşturursunuz. Bir ağ geçidinin oluşturulması 45 dakika sürebilir. Karşılaştırmayla, bir ağ geçidi SKU 'sunu yeniden boyutlandırdığınızda, ağ geçidini silip yeniden derlemek zorunda olmadığınızdan çok kapalı kalma süresi yoktur. Ağ Geçidi SKU 'sunu değiştirmek yerine yeniden boyutlandırma seçeneğiniz varsa bunu yapmak isteyeceksiniz. Ancak yeniden boyutlandırmayla ilgili kurallar vardır:

1. Temel SKU 'nun dışında, bir VPN ağ geçidi SKU 'sunu aynı nesil (Generation1 veya Generation2) içinde başka bir VPN Gateway SKU 'SU olarak yeniden boyutlandırabilirsiniz. Örneğin, VpnGw1 of Generation1, Generation1 VpnGw2 olarak yeniden boyutlandırılabilir, ancak VpnGw2 Generation2.
2. Eski ağ geçidi SKU'larıyla çalışırken Temel, Standart ve Yüksek Performanslı SKU'lar arasında yeniden boyutlandırma yapabilirsiniz.
3. Temel/standart/HighPerformance SKU 'larından VpnGw SKU **'larına yeniden boyutlandırılamaz** . Bunun yerine yeni SKU 'Lara [geçiş](#change) yapmanız gerekir.

#### <a name="resizegwsku"></a>Bir ağ geçidini yeniden boyutlandırmak için

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>Eski (eski) bir SKU 'dan yeni bir SKU 'ya geçiş yapmak için

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Bağlantı türleri

Kaynak Yöneticisi dağıtım modelinde, her yapılandırma için belirli bir sanal ağ geçidi bağlantı türü gerekir. `-ConnectionType` için kullanılabilir Resource Manager PowerShell değerleri şunlardır:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

Aşağıdaki PowerShell örneğinde, *IPSec*bağlantı türünü GEREKTIREN bir S2S bağlantısı oluşturacağız.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>VPN türleri

Bir VPN ağ geçidi yapılandırması için sanal ağ geçidi oluşturduğunuzda, bir VPN türü belirtmeniz gerekir. Seçtiğiniz VPN türü, oluşturmak istediğiniz bağlantı topolojisine bağlıdır. Örneğin, bir P2S bağlantısı, RouteBased VPN türü gerektirir. VPN türü Ayrıca, kullanmakta olduğunuz donanıma de bağlı olabilir. S2S yapılandırmalarının bir VPN cihazı olması gerekir. Bazı VPN cihazları yalnızca belirli bir VPN türünü destekler.

Seçtiğiniz VPN türü, oluşturmak istediğiniz çözüm için tüm bağlantı gereksinimlerini karşılamalıdır. Örneğin, aynı sanal ağ için bir S2S VPN Ağ Geçidi bağlantısı ve bir P2S VPN Ağ Geçidi bağlantısı oluşturmak istiyorsanız, P2S, RouteBased VPN türü gerektirdiğinden *Routebased* VPN türünü kullanırsınız. Ayrıca, VPN cihazınızın bir RouteBased VPN bağlantısı destekliyordu de doğrulamanız gerekir. 

Bir sanal ağ geçidi oluşturulduktan sonra VPN türünü değiştiremezsiniz. Sanal ağ geçidini silip yeni bir tane oluşturmanız gerekir. İki VPN türü vardır:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Aşağıdaki PowerShell örneği, `-VpnType` *Routebased*olarak belirtir. Bir ağ geçidi oluştururken, -VpnType öğesinin yapılandırmanız için doğru olduğundan emin olmanız gerekir.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Ağ Geçidi gereksinimleri

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Ağ geçidi alt ağı

Bir VPN ağ geçidi oluşturmadan önce bir ağ geçidi alt ağı oluşturmanız gerekir. Ağ geçidi alt ağı, sanal ağ geçidi VM 'lerinin ve hizmetlerinin kullandığı IP adreslerini içerir. Sanal ağ geçidinizi oluşturduğunuzda, ağ geçidi VM 'Leri ağ geçidi alt ağına dağıtılır ve gerekli VPN Gateway ayarları ile yapılandırılır. Ağ geçidi alt ağına hiçbir şeyi başka hiçbir şekilde (örneğin, ek VM 'Ler) dağıtmayın. Düzgün çalışması için ağ geçidi alt ağının ' GatewaySubnet ' olarak adlandırılması gerekir. Ağ geçidi alt ağının ' GatewaySubnet ' olarak adlandırılması, Azure 'un sanal ağ geçidi VM 'lerini ve hizmetlerini dağıtmak için bu alt ağ olduğunu bilmesini sağlar.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Ağ geçidi alt ağı oluştururken, alt ağın içerdiği IP adresi sayısını belirtirsiniz. Ağ geçidi alt ağındaki IP adresleri ağ geçidi VM 'lerine ve ağ geçidi hizmetlerine ayrılır. Bazı yapılandırmalar için diğerlerinden daha fazla IP adresi gerekir. 

Ağ geçidi alt ağınızın boyutunu planlarken, oluşturmayı planladığınız yapılandırma için belgelere bakın. Örneğin, ExpressRoute/VPN Gateway bir arada bulunan yapılandırma, diğer birçok yapılandırmadan daha büyük bir ağ geçidi alt ağı gerektirir. Ayrıca, ağ geçidi alt ağınızın olası gelecekteki ek yapılandırmalara uyum sağlamak için yeterli IP adresi içerdiğinden emin olmak isteyebilirsiniz. /29 kadar küçük bir ağ geçidi alt ağı oluşturabileceğiniz gibi, kullanılabilir adres alanınız varsa/27 veya daha büyük (/27,/26 vb.) bir ağ geçidi alt ağı oluşturmanızı öneririz. Bu, çoğu yapılandırmaya uyum sağlayacaktır.

Aşağıdaki Kaynak Yöneticisi PowerShell örneği, GatewaySubnet adlı bir ağ geçidi alt ağını göstermektedir. CıDR gösteriminin bir/27 olduğunu görebilirsiniz. Bu, şu anda mevcut olan çoğu yapılandırma için yeterli IP adresi sağlar.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Yerel ağ geçitleri

 Yerel ağ geçidi, bir sanal ağ geçidinden farklıdır. Bir VPN ağ geçidi yapılandırması oluştururken, yerel ağ geçidi genellikle şirket içi konumunuzu temsil eder. Klasik dağıtım modelinde, yerel ağ geçidi için Yerel Site olara ifade edilir.

Yerel ağ geçidine bir ad, şirket içi VPN cihazının genel IP adresi verirsiniz ve şirket içi konumda bulunan adres öneklerini belirtebilirsiniz. Azure, ağ trafiği için hedef adres öneklerine bakar, yerel ağ geçidiniz için belirttiğiniz yapılandırmaya bakar ve paketleri buna göre yönlendirir. Ayrıca, bir VPN Ağ Geçidi bağlantısı kullanan VNet-VNet yapılandırmalarına yönelik yerel ağ geçitleri de belirtirsiniz.

Aşağıdaki PowerShell örneği yeni bir yerel ağ geçidi oluşturur:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Bazen yerel ağ geçidi ayarlarını değiştirmeniz gerekebilir. Örneğin, adres aralığını eklediğinizde veya değiştirdiğinizde ya da VPN cihazının IP adresi değişirse. Bkz. [PowerShell kullanarak yerel ağ geçidi ayarlarını değiştirme](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>REST API 'Leri, PowerShell cmdlet 'leri ve CLı

REST API 'leri, PowerShell cmdlet 'leri veya VPN Gateway yapılandırmalarına yönelik Azure CLı kullanırken ek teknik kaynaklar ve özel sözdizimi gereksinimleri için aşağıdaki sayfalara bakın:

| **Klasik** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [REST API](https://msdn.microsoft.com/library/jj154113) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Desteklenmiyor | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Sonraki adımlar

Kullanılabilir bağlantı konfigürasyonları hakkında daha fazla bilgi için bkz. [VPN Gateway](vpn-gateway-about-vpngateways.md).

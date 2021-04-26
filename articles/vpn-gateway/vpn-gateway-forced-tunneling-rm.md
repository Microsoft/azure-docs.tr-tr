---
title: Siteden siteye bağlantılar için zorlamalı tüneli yapılandırma
description: Internet 'e bağlı tüm trafiği şirket içi konumunuza geri yönlendirme (zorlama).
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cherylmc
ms.openlocfilehash: afd1c1d5312a9fbf39b401b0cbb4b9997f27407a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869047"
---
# <a name="configure-forced-tunneling"></a>Zorlamalı tünel yapılandırma

Zorlamalı tünel oluşturma, İnternet’e yönelik tüm trafiğin inceleme ve denetim amacıyla Siteden Siteye VPN tüneli aracılığıyla şirket içi konumunuza geri yönlendirilmesini veya “zorlanmasını” sağlamanızı mümkün kılar. Bu, çoğu kurumsal BT İlkesi için kritik bir güvenlik gereksinimidir. Zorlamalı tünel yapılandırmadıysanız, Azure 'daki sanal makinelerinizden Internet 'e yönelik trafik, trafiği incelemenize veya denetlemenizi sağlayacak seçeneği olmadan Azure ağ altyapısından doğrudan Internet 'e geçer. Yetkisiz Internet erişimi, bilgilerin açığa çıkmasına veya diğer güvenlik ihlallerine neden olabilir.

Zorlamalı tünel, Azure PowerShell kullanılarak yapılandırılabilir. Azure portal kullanılarak yapılandırılamaz. Bu makale, Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan sanal ağlar için zorlamalı tüneli yapılandırmanıza yardımcı olur. Klasik dağıtım modeli için Zorlamalı tünel yapılandırmak istiyorsanız bkz. [Zorlamalı tünel-klasik](vpn-gateway-about-forced-tunneling.md).

## <a name="about-forced-tunneling"></a>Zorlamalı tünel hakkında

Aşağıdaki diyagramda zorlamalı tünelin nasıl çalıştığı gösterilmektedir.

:::image type="content" source="./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png" alt-text="Diyagramda Zorlamalı tünel gösterilmektedir.":::

Bu örnekte, ön uç alt ağı Zorlamalı tünel değildir. Ön uç alt ağındaki iş yükleri doğrudan Internet 'ten gelen müşteri isteklerini kabul etmeye ve bunlara yanıt vermeye devam edebilir. Orta katman ve arka uç alt ağları Zorlamalı tünel. Bu iki alt ağın Internet 'e giden tüm bağlantıları, siteden siteye (S2S) VPN tünellerinden biri aracılığıyla şirket içi bir siteye yeniden yönlendirilir veya yeniden yönlendirilir.

Bu sayede, Azure 'daki sanal makinelerinizden veya bulut hizmetinizden Internet erişimini kısıtlayıp incelemenize olanak sağlarken çok katmanlı hizmet mimarinizi etkinleştirmeye devam edersiniz. Sanal ağlarınızda Internet 'e yönelik bir iş yükü yoksa, sanal ağların tamamına Zorlamalı tünel de uygulayabilirsiniz.

## <a name="requirements-and-considerations"></a>Gereksinimler ve önemli noktalar

Azure 'da Zorlamalı tünel, sanal ağ özel kullanıcı tanımlı rotalar kullanılarak yapılandırılır. Trafiği şirket içi siteye yeniden yönlendirmek, Azure VPN ağ geçidi için varsayılan rota olarak ifade edilir. Kullanıcı tanımlı yönlendirme ve sanal ağlar hakkında daha fazla bilgi için bkz. [Özel Kullanıcı tanımlı rotalar](../virtual-network/virtual-networks-udr-overview.md#user-defined).

* Her sanal ağ alt ağı yerleşik, sistem yönlendirme tablosuna sahiptir. Sistem yönlendirme tablosu aşağıdaki üç yol grubuna sahiptir:
  
  * **Yerel VNET yolları:** Aynı sanal ağdaki hedef VM 'lere doğrudan.
  * **Şirket içi rotalar:** Azure VPN ağ geçidine.
  * **Varsayılan yol:** Doğrudan Internet 'e. Önceki iki yolun kapsamadığı özel IP adreslerine gidecek paketler bırakılır.
* Bu yordam, bir varsayılan yol eklemek üzere bir yönlendirme tablosu oluşturmak için Kullanıcı tanımlı yollar (UDR) kullanır ve ardından bu alt ağlarda Zorlamalı tünel sağlamak için yönlendirme tablosunu VNet alt ağlarınıza ilişkilendirir.
* Zorlamalı tünelleme, rota tabanlı bir VPN ağ geçidine sahip bir VNet ile ilişkili olmalıdır. Sanal ağa bağlı şirketler arası yerel siteler arasında "varsayılan site" ayarlamanız gerekir. Ayrıca, şirket içi VPN cihazının trafik seçicileri olarak 0.0.0.0/0 kullanılarak yapılandırılması gerekir. 
* ExpressRoute Zorlamalı tünel bu mekanizma aracılığıyla yapılandırılmaz, ancak bunun yerine ExpressRoute BGP eşleme oturumları aracılığıyla varsayılan bir yol tanıtıarak etkinleştirilir. Daha fazla bilgi için bkz. [ExpressRoute belgeleri](https://azure.microsoft.com/documentation/services/expressroute/).
* Hem VPN Gateway hem de ExpressRoute Gateway 'i aynı VNet 'te dağıttığındaki ExpressRoute ağ geçidi, yapılandırılmış "varsayılan site" öğesini VNet 'e duyurtacak şekilde Kullanıcı tanımlı yollar (UDR) artık gerekli değildir.

## <a name="configuration-overview"></a>Yapılandırmaya genel bakış

Aşağıdaki yordam, bir kaynak grubu ve sanal ağ oluşturmanıza yardımcı olur. Daha sonra bir VPN Gateway oluşturacak ve zorlamalı tüneli yapılandıracaksınız. Bu yordamda, ' MultiTier-VNet ' adlı sanal ağın üç alt ağı vardır: ' ön uç ', ' orta katman ' ve ' arka uç ', dört şirket içi bağlantıyla: ' DefaultSiteHQ ' ve üç dal.

Yordam adımları, ' DefaultSiteHQ ' değerini Zorlamalı tünel için varsayılan site bağlantısı olarak ayarlar ve zorlamalı tüneli kullanmak için ' orta katman ' ve ' arka uç ' alt ağlarını yapılandırır.

## <a name="before-you-begin"></a><a name="before"></a>Başlamadan önce

Azure Resource Manager PowerShell cmdlet'lerinin en son sürümünü yükleyin. PowerShell cmdlet'lerini yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/).

> [!IMPORTANT]
> PowerShell cmdlet 'lerinin en son sürümünü yüklemek gereklidir. Aksi takdirde, cmdlet 'lerden bazılarını çalıştırırken doğrulama hataları alabilirsiniz.
>
>

### <a name="to-sign-in"></a>Oturum açmak için

[!INCLUDE [Sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Zorlamalı tünel yapılandırma

> [!NOTE]
> "Bu cmdlet 'in çıkış nesnesi türü gelecek bir sürümde değiştirilecek" uyarısı görebilirsiniz. Bu beklenen davranıştır ve bu uyarıları güvenle yoksayabilirsiniz.
>
>


1. Bir kaynak grubu oluşturun.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Bir sanal ağ oluşturun ve alt ağları belirtin.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Yerel ağ geçitleri oluşturun.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Yol tablosu ve yol kuralını oluşturun.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Yol tablosunu orta katman ve arka uç alt ağları ile ilişkilendirin.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Sanal ağ geçidini oluşturun. Ağ geçidini oluşturup yapılandırırken bu adımın tamamlanabilmesi için bazen 45 dakika veya daha fazla süre sürer. GatewaySKU değeriyle ilgili ValidateSet hataları görürseniz, [PowerShell cmdlet 'lerinin en son sürümünü](#before)yüklediğinizden emin olun. PowerShell cmdlet 'lerinin en son sürümü, en son ağ geçidi SKU 'Larının yeni doğrulanan değerlerini içerir.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Sanal ağ geçidine varsayılan bir site atayın. **-Gatewaydefaultsite** , zorunlu yönlendirme yapılandırmasının çalışmasına izin veren cmdlet parametresidir, bu yüzden bu ayarı düzgün şekilde yapılandırmayı unutmayın. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Siteden siteye VPN bağlantıları oluşturun.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```

---
title: 'Öğretici-Azure ExpressRoute: sanal ağa ağ geçidi ekleme-Azure PowerShell'
description: Bu öğretici, Azure PowerShell kullanarak ExpressRoute için zaten oluşturulmuş bir Kaynak Yöneticisi VNet 'e VNet ağ geçidi eklemenize yardımcı olur.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 120bfe2eefae3c1721073060231c6c2a1962b7c8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110283"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Öğretici: PowerShell kullanarak ExpressRoute için sanal ağ geçidi yapılandırma
> [!div class="op_single_selector"]
> * [Kaynak Yöneticisi Azure portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasik-PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video-Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Bu öğretici, önceden var olan VNet için bir sanal ağ (VNet) ağ geçidi eklemenize, yeniden boyutlandırmanıza ve kaldırmanıza yardımcı olur. Bu yapılandırmaya yönelik adımlar, bir ExpressRoute yapılandırması için Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan sanal ağlar için geçerlidir. Daha fazla bilgi için bkz. [ExpressRoute için sanal ağ geçitleri hakkında](expressroute-about-virtual-network-gateways.md).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Ağ geçidi alt ağı oluşturun.
> - Sanal Ağ Geçidi oluşturun.

## <a name="prerequisites"></a>Önkoşullar

### <a name="configuration-reference-list"></a>Yapılandırma başvuru listesi

Bu görevin adımları aşağıdaki yapılandırma başvurusu listesindeki değerleri temel alarak bir sanal ağ kullanır. Ek ayarlar ve adlar bu listede de özetlenmiştir. Bu liste, bu listedeki değerlere göre değişken eklediğimiz halde, bu listeyi doğrudan herhangi bir adımda kullanmıyoruz. Bir başvuru olarak kullanmak için listeyi kopyalayabilir ve değerleri kendi değerlerinizle değiştirin.

| Ayar                   | Değer                                              |
| ---                       | ---                                                |
| Sanal ağ adı | *TestVNet* |    
| Sanal ağ adres alanı | *192.168.0.0/16* |
| Kaynak Grubu | *TestRG* |
| Subnet1 adı | *FrontEnd* |   
| Subnet1 adres alanı | *192.168.1.0/24* |
| Subnet1 adı | *FrontEnd* |
| Ağ geçidi alt ağ adı | *GatewaySubnet* |    
| Ağ geçidi alt ağ adres alanı | *192.168.200.0/26* |
| Region | *Doğu ABD* |
| Ağ Geçidi Adı | *GW* |   
| Ağ geçidi IP adı | *GWıP* |
| Ağ geçidi IP yapılandırma adı | *gwipconf* |
| Tür | *ExpressRoute* |
| Ağ Geçidi genel IP adı  | *gwpıp* |

> [!IMPORTANT]
> Özel eşleme için IPv6 desteği şu anda **genel önizlemededir**. Sanal ağınızı IPv6 tabanlı özel eşleme ile bir ExpressRoute devresine bağlamak istiyorsanız, lütfen sanal ağınızın ikili bir yığın olduğundan ve [burada](../virtual-network/ipv6-overview.md)açıklanan yönergeleri izlediğinden emin olun.
> 
> 

## <a name="add-a-gateway"></a>Ağ geçidi ekleme

1. Azure ile bağlantı kurmak için çalıştırın `Connect-AzAccount` .

1. Bu alıştırma için değişkenlerinizi bildirin. Örneği, kullanmak istediğiniz ayarları yansıtacak şekilde düzenlemediğinizden emin olun.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Sanal ağ nesnesini bir değişken olarak depolayın.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Sanal ağınıza bir ağ geçidi alt ağı ekleyin. Ağ geçidi alt ağı "GatewaySubnet" olarak adlandırılmalıdır. Ağ geçidi alt ağı/27 veya daha büyük (/26,/25 vb.) olmalıdır. Ağ geçidinize 16 ExpressRoute devreleri bağlamayı planlıyorsanız,/26 veya daha büyük bir ağ geçidi alt ağı oluşturmanız **gerekir** .

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    Çift yığın sanal ağı kullanıyorsanız ve ExpressRoute üzerinden IPv6 tabanlı özel eşleme kullanmayı planlıyorsanız, bunun yerine bir çift yığın ağ geçidi alt ağı oluşturun.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. Yapılandırmayı ayarlayın.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Ağ geçidi alt ağını bir değişken olarak depolayın.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Genel bir IP adresi isteyin. Ağ Geçidi oluşturulmadan önce IP adresi istendi. Kullanmak istediğiniz IP adresini belirtemezsiniz; dinamik olarak atanır. Sonraki yapılandırma bölümünde bu IP adresini kullanacaksınız. AllocationMethod dinamik olmalıdır.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. Ağ geçidinizin yapılandırmasını oluşturun. Ağ geçidi yapılandırması, kullanılacak alt ağı ve genel IP adresini tanımlar. Bu adımda, ağ geçidini oluştururken kullanılacak yapılandırmayı belirteceğiz. Ağ geçidi yapılandırmanızı oluşturmak için aşağıdaki örneği kullanın.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Ağ geçidini oluşturun. Bu adımda **-gatewaytype** özellikle önemlidir. **ExpressRoute** değerini kullanmanız gerekir. Bu cmdlet 'leri çalıştırdıktan sonra ağ geçidinin oluşturulması 45 dakika veya daha fazla sürebilir.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> ExpressRoute üzerinden IPv6 tabanlı özel eşleme kullanmayı planlıyorsanız, **-gatewaysku** IÇIN az SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) seçtiğinizden emin olun.
> 
> 

## <a name="verify-the-gateway-was-created"></a>Ağ geçidinin oluşturulduğunu doğrulayın
Ağ geçidinin oluşturulduğunu doğrulamak için aşağıdaki komutları kullanın:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ağ geçidini yeniden boyutlandırma
Birçok [ağ geçidi SKU](expressroute-about-virtual-network-gateways.md)'su vardır. Ağ Geçidi SKU 'sunu dilediğiniz zaman değiştirmek için aşağıdaki komutu kullanabilirsiniz.

> [!IMPORTANT]
> Bu komut UltraPerformance ağ geçidi için çalışmıyor. Ağ geçidinizi bir UltraPerformance ağ geçidine dönüştürmek için, önce mevcut ExpressRoute Gateway 'i kaldırın ve ardından yeni bir UltraPerformance Ağ Geçidi oluşturun. Ağ geçidinizin bir UltraPerformance ağ geçidiyle indirgenmesini sağlamak için önce UltraPerformance ağ geçidini kaldırın ve ardından yeni bir ağ geçidi oluşturun.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
Ağ geçidini kaldırmak için aşağıdaki komutu kullanın:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>Sonraki adımlar
VNet ağ geçidini oluşturduktan sonra sanal ortamınızı bir ExpressRoute devresine bağlayabilirsiniz. 

> [!div class="nextstepaction"]
> [Sanal ağı bir ExpressRoute devresine bağlama](expressroute-howto-linkvnet-arm.md)

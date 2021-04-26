---
title: 'Azure ExpressRoute: Azure PowerShell kullanarak IPv6 desteği ekleme'
description: Azure PowerShell kullanarak Azure dağıtımlarına bağlanmak için IPv6 desteğinin nasıl ekleneceğini öğrenin.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 7a9ac98a9566986767016720fda245712197b27f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566549"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Azure PowerShell kullanarak özel eşleme için IPv6 desteği ekleme (Önizleme)

Bu makalede, Azure PowerShell kullanarak Azure 'daki kaynaklarınıza ExpressRoute aracılığıyla bağlanmak üzere IPv6 desteğinin nasıl ekleneceği açıklanmaktadır.

> [!Note]
> Bu özellik şu anda [kullanılabilirlik alanları olan Azure bölgelerinde](../availability-zones/az-region.md#azure-regions-with-availability-zones)önizleme için kullanılabilir. Bu nedenle, ExpressRoute bağlantı hattı herhangi bir eşleme konumu kullanılarak oluşturulabilir, ancak bağlandığı IPv6 tabanlı dağıtımlar Kullanılabilirlik Alanları bir bölgede olmalıdır.

## <a name="working-with-azure-powershell"></a>Azure PowerShell çalışma

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>Genel önizlemeye kaydolun
IPv6 desteği eklemeden önce aboneliğinizi kaydetmeniz gerekir. Kaydolmak için lütfen Azure PowerShell aracılığıyla şunları yapın:
1.  Azure 'da oturum açın ve aboneliği seçin. Bunu, ExpressRoute devrenizi içeren abonelik için ve Azure dağıtımlarınızı içeren aboneliğin (farklı olmaları durumunda) yapmanız gerekir.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Aşağıdaki komutu kullanarak aboneliğinizi genel önizleme için kaydetme isteği:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

İsteğiniz daha sonra ExpressRoute ekibi tarafından 2-3 iş günü içinde onaylanır.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute devrenizin IPv6 özel eşlemesi ekleme

1. [Bir ExpressRoute](./expressroute-howto-circuit-arm.md) bağlantı hattı oluşturun veya mevcut bir devreyi kullanın. **Get-Azexpressroutedevresi** komutunu çalıştırarak devreyi alın:

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. **Get-Azexpressroutedevpeeringconfig** komutunu çalıştırarak devre için özel eşleme yapılandırmasını alın:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Var olan IPv4 özel eşleme yapılandırmanıza IPv6 özel eşlemesi ekleyin. Birincil bağlantınız ve ikincil bağlantılarınız için sahip olduğunuz bir çift/126 IPv6 alt ağı sağlayın. Bu alt ağların her birinde, Microsoft 'un yönlendiricisi için kullanılabilen ikinci IP 'yi kullandığından, ilk kullanılabilir IP adresini yönlendiricinize atayacaksınız.

    > [!Note]
    > Eşdüzey ASN ve Vlanıd, IPv4 özel eşleme yapılandırmanızda olanlarla eşleşmelidir.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Yapılandırma başarıyla kaydedildikten sonra, **Get-Azexpressroutedevresi** komutunu çalıştırarak devreyi tekrar alın. Yanıt aşağıdaki örneğe benzer şekilde görünmelidir:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Mevcut bir sanal ağla bağlantınızı güncelleştirme

IPv6 özel eşliğini kullanmak istediğiniz Kullanılabilirlik Alanları bir bölgede Azure kaynakları 'nın mevcut bir ortamınıza sahipseniz aşağıdaki adımları izleyin.

1. ExpressRoute devrenizin bağlı olduğu sanal ağı alın.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Sanal ağınıza bir IPv6 adres alanı ekleyin.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Ağ geçidi alt ağına IPv6 adres alanı ekleyin. Ağ Geçidi IPv6 alt ağı/64 veya daha büyük olmalıdır.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Bölgesel olarak yedekli mevcut bir ağ geçidiniz varsa IPv6 bağlantısını etkinleştirmek için aşağıdakileri çalıştırın. Aksi takdirde, bölgesel olarak yedekli bir SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) kullanarak [sanal ağ geçidini oluşturun](./expressroute-howto-add-gateway-resource-manager.md) .

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Yeni bir sanal ağa bağlantı oluştur

IPv6 özel eşlemesini kullanarak Kullanılabilirlik Alanları bir bölgedeki yeni bir Azure kaynakları kümesine bağlanmayı planlıyorsanız aşağıdaki adımları izleyin.

1. Hem IPv4 hem de IPv6 adres alanı ile çift yığın sanal ağı oluşturun. Daha fazla bilgi için bkz. [sanal ağ oluşturma](../virtual-network/quick-create-portal.md#create-a-virtual-network).

2. [Çift Stack ağ geçidi alt ağını oluşturun](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. Bölgesel olarak yedekli bir SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) kullanarak [sanal ağ geçidini oluşturun](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) . FastPath kullanmayı planlıyorsanız, ErGw3AZ kullanın (bunun yalnızca ExpressRoute Direct kullanan devreler için kullanılabilir olduğunu unutmayın).

4. [Sanal ağınızı ExpressRoute devrenizi Ile ilişkilendirin](./expressroute-howto-linkvnet-arm.md).

## <a name="limitations"></a>Sınırlamalar
IPv6 desteği, Kullanılabilirlik Alanları bölgelerdeki dağıtımlara bağlantılar için kullanılabilir olsa da, aşağıdaki kullanım durumlarını desteklemez:

* AZ ExpressRoute ağ geçidi SKU 'SU aracılığıyla Azure 'daki dağıtımlara bağlantılar
* AZ olmayan bölgelerde dağıtımlara bağlantılar
* ExpressRoute devreleri arasında bağlantı Global Reach
* Sanal WAN ile ExpressRoute kullanımı
* ExpressRoute doğrudan devrelerine sahip FastPath
* Şu eşleme konumlarında devrelere sahip FastPath: Dubai
* VPN Gateway birlikte bulunma

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute sorunlarını gidermek için aşağıdaki makalelere bakın:

* [ExpressRoute bağlantısını doğrulama](expressroute-troubleshooting-expressroute-overview.md)
* [Ağ performansı sorunlarını giderme](expressroute-troubleshooting-network-performance.md)
---
title: 'ExpressRoute: sanal ağı bir devreye bağlama: Azure PowerShell'
description: Bu belge, PowerShell ve Resource Manager dağıtım modeli kullanarak ExpressRoute devresine bağlama sanal ağları (Vnet) genel bakış sağlar.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 22e235b16f834198f5edc2f9365d2b13e1e9c49f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031725"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Bir sanal ağı ExpressRoute devresine bağlama
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure portalı](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-linkvnet-classic.md)
>

Bu makale, Resource Manager dağıtım modeli ve PowerShell kullanarak, sanal ağlar (Vnet'ler) bağlantı Azure ExpressRoute bağlantı hatları için olur. Sanal ağlar aynı abonelikte veya başka bir abonelik parçası olarak ya da olabilir. Bu makalede ayrıca bir sanal ağ bağlantısı güncelleştirme gösterilmektedir.

* Standart bir ExpressRoute bağlantı hattı için en fazla 10 sanal ağlara bağlayabilirsiniz. Tüm sanal ağları, standart bir ExpressRoute bağlantı hattını kullanırken aynı jeopolitik bölgede olması gerekir. 

* En fazla dört ExpressRoute bağlantı hatları için tek bir sanal ağa bağlanabilir. Bağlanmakta olduğunuz her bir ExpressRoute bağlantı hattı için yeni bir bağlantı nesnesi oluşturmak için bu makaledeki adımları kullanın. ExpressRoute bağlantı hatları, aynı abonelik, farklı Aboneliklerde veya her ikisinin bir karışımı olabilir.

* ExpressRoute devresinin coğrafi bölge dışında sanal ağlara bağlantı ya da ExpressRoute premium eklentisi etkinleştirildiğinde, çok sayıda sanal ağları ExpressRoute devreniz bağlayın. Denetleme [SSS](expressroute-faqs.md) premium eklenti hakkında daha fazla ayrıntı için.


## <a name="before-you-begin"></a>Başlamadan önce

* Gözden geçirme [önkoşulları](expressroute-prerequisites.md), [yönlendirme gereksinimleri](expressroute-routing.md), ve [iş akışları](expressroute-workflows.md) yapılandırmaya başlamadan önce.

* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. 
  * Yönergelerini izleyin [ExpressRoute devresi oluşturma](expressroute-howto-circuit-arm.md) ve bağlantı hattı, bağlantı sağlayıcı tarafından etkin. 
  * Bağlantı hattınız için yapılandırılmış Azure özel eşleme olduğundan emin olun. Bkz: [yönlendirmeyi yapılandırma](expressroute-howto-routing-arm.md) makale için yönlendirme yönergeleri. 
  * Azure özel eşdüzey hizmet sağlama yapılandırılır ve uçtan uca bağlantıyı etkinleştirmek üzere ağınız ile Microsoft arasında BGP eşliği ayarlama olduğundan emin olun.
  * Bir sanal ağ ve oluşturulan ve tam olarak sağlanan sanal ağ geçidi olduğundan emin olun. Yönergelerini izleyin [ExpressRoute için sanal ağ geçidi oluşturma](expressroute-howto-add-gateway-resource-manager.md). ExpressRoute için sanal ağ geçidi, GatewayType 'ExpressRoute' VPN'değil kullanır.

### <a name="working-with-azure-powershell"></a>Azure PowerShell ile çalışma

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Bir sanal ağ ile aynı abonelikte devreye bağlama
Aşağıdaki cmdlet'i kullanarak ExpressRoute bağlantı hattına bir sanal ağ geçidine bağlanabilir. Sanal ağ geçidi oluşturulur ve cmdlet çalıştırılmadan önce bağlamak için hazır olduğundan emin olun:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Farklı abonelikteki bir sanal ağı devreye bağlama
Bir ExpressRoute bağlantı hattı birden çok farklı abonelikler arasında paylaşabilirsiniz. Aşağıdaki şekilde birden fazla aboneliği analiz basit bir ExpressRoute bağlantı hatları için nasıl paylaşım Works şematik gösterir.

Her küçük bulutların büyük bulut içinde bir kuruluştaki farklı departmanlara ait abonelikleri temsil etmek için kullanılır. Her kuruluş içindeki bölümlerin dağıtmak için kendi hizmetlerini--ancak kendi aboneliğini kullanabilirsiniz, şirket içi ağınıza bağlanmak için tek bir ExpressRoute bağlantı hattı paylaşabilirsiniz. Tek bir bölüm (Bu örnekte: BT) ExpressRoute bağlantı hattına sahip olabilir. Kuruluştaki diğer abonelikler, ExpressRoute bağlantı hattı kullanabilirsiniz.

> [!NOTE]
> Abonelik sahibi için ExpressRoute bağlantı hattı için bağlantı ve bant genişliği ücretleri uygulanır. Tüm sanal ağları, aynı bant genişliğini paylaşır.
> 
> 

![Çapraz abonelik bağlantısı](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Yönetim - bağlantı hattı sahibi ve bağlantı hattı kullanıcıları

'Bağlantı hattı sahibinden' ExpressRoute bağlantı hattı kaynak yetkili bir güç kullanıcıdır. Bağlantı hattı sahibinden 'devre kullanıcıları tarafından' ödenebilecek yetkilendirmeleri oluşturabilirsiniz. ExpressRoute bağlantı hattı aynı abonelik içinde olmayan sanal ağ geçitleri sahipleri bağlantı hattını kullanıcılardır. Devre kullanıcılarının, yetkilendirmeleri (sanal ağ başına bir yetkilendirme) kullanmak.

Bağlantı hattı sahibinden yetkilendirme dilediğiniz zaman iptal et ve değiştirmek için gücüne sahiptir. Bir yetkilendirme sonuçlarına tüm bağlantı erişimini iptal edildi abonelikten silinmesini iptal ediliyor.

### <a name="circuit-owner-operations"></a>Bağlantı hattı sahibi işlemleri

**Bir yetkilendirme oluşturmak için**

Bağlantı hattı sahibinden bir yetkilendirme oluşturur. ExpressRoute bağlantı hattına kendi sanal ağ geçitlerine bağlanmak için bir bağlantı hattı kullanıcısı tarafından kullanılan bir yetkilendirme anahtarına oluşturulmasını sonuçlanır. Bir yetkilendirme yalnızca bir bağlantı için geçerli değil.

Aşağıdaki cmdlet kod parçacığında, bir yetkilendirme oluşturma işlemi gösterilmektedir:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Bu yanıt durumu ve yetkilendirme anahtarını içerir:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Yetkilendirmeleri gözden geçirmek için**

Bağlantı hattı sahibinden belirli bir bağlantı hattı üzerinde aşağıdaki cmdlet'i çalıştırarak düzenlenen tüm yetkilendirmeleri gözden geçirebilirsiniz:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Yetkilendirmeleri eklemek için**

Bağlantı hattı sahibinden yetkilendirme, aşağıdaki cmdlet'i kullanarak ekleyebilirsiniz:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Yetkilendirmeleri silmek için**

Bağlantı hattı sahibinden iptal etme/yetkilendirmeleri kullanıcı için aşağıdaki cmdlet'i çalıştırarak ya da silebilir:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Bağlantı hattı kullanıcı işlemleri

Bağlantı hattı kullanıcısı eş kimliği ve bağlantı hattı sahibinden yetkilendirme anahtarı gerekir. Yetkilendirme anahtarı bir GUID'dir.

Aşağıdaki komutu eş kimliği denetlenebilir:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Bağlantı Yetkilendirme kullanmak için**

Bağlantı hattı kullanıcısı bağlantı yetkilendirme kullanmak için aşağıdaki cmdlet'i çalıştırabilirsiniz:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Bağlantı Yetkilendirme serbest bırakmak için**

Sanal ağı ExpressRoute bağlantı hattına bağlayan bağlantı silerek bir yetkilendirme serbest bırakabilirsiniz.

## <a name="modify-a-virtual-network-connection"></a>Sanal ağ bağlantısı değiştirme
Belirli bir sanal ağ bağlantısı özellikleri güncelleştirebilirsiniz. 

**Bağlantı ağırlığına güncelleştirmek için**

Sanal ağınız için birden çok ExpressRoute bağlantı hattına bağlı olabilir. Aynı öneke birden fazla ExpressRoute devresinden alabilirsiniz. Bu ön eki hedefleyen trafiği göndermek için hangi bağlantı seçmek için değiştirebileceğiniz *RoutingWeight* bağlantısı. Trafik, en yüksek bağlantı gönderilecek *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Aralığı *RoutingWeight* 0-32000. Varsayılan değer 0’dır.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath yapılandırma 
ExpressRoute bağlantı hattınızı [ExpressRoute Direct](expressroute-erdirect-about.md) üzerinde ise ve sanal ağ geçidiniz Ultra Performance veya ErGw3AZ Ise [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) 'i etkinleştirebilirsiniz. FastPath, şirket içi ağınız ve sanal ağınız arasında saniye başına paket ve bağlantı gibi veri yolu performansını geliştirir. 

> [!NOTE] 
> Zaten bir sanal ağ bağlantınız varsa ancak FastPath 'i etkinleştirmediyseniz, sanal ağ bağlantısını silmeniz ve yeni bir bağlantı oluşturmanız gerekir. 
> 
>  

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

## <a name="next-steps"></a>Sonraki adımlar
ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).

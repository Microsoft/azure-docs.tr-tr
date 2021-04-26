---
title: 'Öğretici: bir sanal ağı ExpressRoute devresine bağlama-Azure PowerShell'
description: Bu öğretici, Kaynak Yöneticisi dağıtım modelini ve Azure PowerShell kullanarak ExpressRoute bağlantı hattına sanal ağların (VNet 'ler) nasıl bağlanacağını gösteren bir genel bakış sunar.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: eda575e0f29abbe18750a24fbfc212a9d84819cf
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110368"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>Öğretici: bir sanal ağı ExpressRoute devresine bağlama
> [!div class="op_single_selector"]
> * [Azure portalı](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video-Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-linkvnet-classic.md)
>

Bu makale, Kaynak Yöneticisi dağıtım modelini ve PowerShell 'i kullanarak sanal ağları (VNet) Azure ExpressRoute devrelerine bağlamanıza yardımcı olur. Sanal ağlar aynı abonelikte ya da başka bir aboneliğin parçası olabilir. Bu makalede ayrıca bir sanal ağ bağlantısının nasıl güncelleştirilmesi gösterilmektedir.

* 10 adede kadar sanal ağı standart bir ExpressRoute devresine bağlayabilirsiniz. Standart bir ExpressRoute bağlantı hattı kullanılırken tüm sanal ağların aynı geopolitik bölgede olması gerekir. 

* Tek bir sanal ağ, en fazla 16 ExpressRoute devresine bağlanabilir. Bağlanmakta olduğunuz her ExpressRoute devresi için yeni bir bağlantı nesnesi oluşturmak üzere bu makaledeki adımları kullanın. ExpressRoute devreleri aynı abonelikte, farklı aboneliklerde veya her ikisinin karışımı olabilir.

* ExpressRoute Premium eklentisini etkinleştirirseniz, ExpressRoute devresinin coğrafi bölgesinin dışındaki sanal ağları bağlayabilirsiniz. Premium eklenti Ayrıca, seçilen bant genişliğine bağlı olarak ExpressRoute bağlantı hattına 10 ' dan fazla sanal ağ bağlamanıza olanak tanır. Premium eklenti hakkında daha fazla bilgi için [SSS bölümüne](expressroute-faqs.md) bakın.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Aynı abonelikteki bir sanal ağı bir devreye bağlama
> - Farklı abonelikteki bir sanal ağı devreye bağlama
> - Bir sanal ağ bağlantısını değiştirme
> - ExpressRoute FastPath yapılandırma

## <a name="prerequisites"></a>Önkoşullar

* Yapılandırmaya başlamadan önce [önkoşulları](expressroute-prerequisites.md), [yönlendirme gereksinimlerini](expressroute-routing.md)ve [iş akışlarını](expressroute-workflows.md) gözden geçirin.

* Etkin bir ExpressRoute bağlantı hattınızın olması gerekir. 
  * [ExpressRoute](expressroute-howto-circuit-arm.md) bağlantı hattı oluşturmak için yönergeleri izleyin ve bağlantı sağlayıcınız tarafından devre dışı bırakıldı. 
  * Devreniz için Azure özel eşlemesi 'nin yapılandırıldığından emin olun. Yönlendirme yönergeleri için [yönlendirmeyi yapılandırma](expressroute-howto-routing-arm.md) makalesine bakın. 
  * Azure özel eşlemesinin yapılandırıldığından ve uçtan uca bağlantı için ağınız ile Microsoft arasında BGP eşlemesi oluşturduğunuzdan emin olun.
  * Oluşturulmuş ve tam olarak sağlanmış bir sanal ağa ve sanal ağ geçidine sahip olduğunuzdan emin olun. [ExpressRoute için sanal ağ geçidi oluşturma](expressroute-howto-add-gateway-resource-manager.md)yönergelerini izleyin. ExpressRoute için bir sanal ağ geçidi, VPN değil GatewayType ' ExpressRoute ' kullanır.

### <a name="working-with-azure-powershell"></a>Azure PowerShell çalışma

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Aynı abonelikteki bir sanal ağı bir devreye bağlama
Aşağıdaki cmdlet 'i kullanarak bir ExpressRoute devresine bir sanal ağ geçidini bağlayabilirsiniz. Cmdlet 'ini çalıştırmadan önce, sanal ağ geçidinin oluşturulduğundan ve bağlamaya hazırlanarak emin olun:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Farklı abonelikteki bir sanal ağı devreye bağlama
Bir ExpressRoute devresini birden çok abonelik arasında paylaşabilirsiniz. Aşağıdaki şekilde, birden çok aboneliğin ExpressRoute devreleri için paylaşımın nasıl çalıştığına ilişkin basit bir şematik gösterilmektedir.

Büyük buluttaki küçük bulutların her biri, bir kuruluştaki farklı departmanlara ait olan abonelikleri temsil etmek için kullanılır. Kuruluştaki bölümlerin her biri, hizmetlerini dağıtmak için kendi aboneliğini kullanır, ancak şirket içi ağınıza geri bağlanmak için tek bir ExpressRoute bağlantı hattı paylaşabilir. Tek bir departman (Bu örnekte:) ExpressRoute devresine sahip olabilir. Kuruluştaki diğer abonelikler ExpressRoute devresini kullanabilir.

> [!NOTE]
> ExpressRoute devresine yönelik bağlantı ve bant genişliği ücretleri abonelik sahibine uygulanır. Tüm sanal ağlar aynı bant genişliğini paylaşır.
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="Çapraz abonelik bağlantısı":::

### <a name="administration---circuit-owners-and-circuit-users"></a>Yönetim-devre sahipleri ve devre kullanıcıları

' Devowner ', ExpressRoute bağlantı hattı kaynağının yetkili bir uzman kullanıcısı. Devre sahibi, ' devre kullanıcıları ' tarafından kullanılabilecek yetkilendirmeler oluşturabilir. Devre kullanıcıları ExpressRoute bağlantı hattı ile aynı abonelikte olmayan sanal ağ geçitlerinin sahiplerinden oluşur. Devre kullanıcıları yetkilendirmeleri (sanal ağ başına bir yetkilendirme) kullanabilir.

Devre sahibinin, her zaman yetkilendirmeleri değiştirme ve iptal etme gücü vardır. Erişim iptal edilen abonelikten tüm bağlantı bağlantılarında silinen bir yetkilendirme sonuçları iptal ediliyor.

### <a name="circuit-owner-operations"></a>Devre sahibi işlemleri

**Yetkilendirme oluşturmak için**

Devre sahibi, sanal ağ geçitlerini ExpressRoute devresine bağlamak için bir devre kullanıcısı tarafından kullanılacak bir yetkilendirme anahtarı oluşturan bir yetkilendirme oluşturur. Yetkilendirme yalnızca bir bağlantı için geçerlidir.

Aşağıdaki cmdlet kod parçacığında bir yetkilendirmeyi nasıl oluşturacağınız gösterilmektedir:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

Önceki komutlara olan yanıt, yetkilendirme anahtarını ve durumunu içerecektir:

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**Yetkilendirmeleri gözden geçirmek için**

Devre sahibi, aşağıdaki cmdlet 'i çalıştırarak belirli bir devreye göre verilen tüm yetkilendirmeleri gözden geçirebilir:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Yetkilendirmeler eklemek için**

Devre sahibi aşağıdaki cmdlet 'i kullanarak yetkilendirmeler ekleyebilir:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Yetkilendirmeleri silmek için**

Devre sahibi, aşağıdaki cmdlet 'i çalıştırarak kullanıcıya yetkilendirmeleri iptal edebilir/silebilir:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Devre Kullanıcı işlemleri

Devre kullanıcısının, devre sahibinden eş KIMLIĞI ve yetkilendirme anahtarı olması gerekir. Yetkilendirme anahtarı bir GUID 'dir.

Eş KIMLIĞI aşağıdaki komuttan denetlenebilir:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Bir bağlantı yetkilendirmesini kullanma**

Devre kullanıcısı bir bağlantı yetkilendirmesi kullanmak için aşağıdaki cmdlet 'i çalıştırabilir:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Bir bağlantı yetkilendirmesini serbest bırakmak için**

ExpressRoute bağlantı hattını sanal ağa bağlayan bağlantıyı silerek bir yetkilendirmeyi serbest bırakabilirsiniz.

## <a name="modify-a-virtual-network-connection"></a>Bir sanal ağ bağlantısını değiştirme
Bir sanal ağ bağlantısının belirli özelliklerini güncelleştirebilirsiniz. 

**Bağlantı ağırlığını güncelleştirmek için**

Sanal ağınız, birden fazla ExpressRoute devresine bağlanabilir. Birden fazla ExpressRoute devreninden aynı öneki alabilirsiniz. Bu önek için hangi trafiğin gönderileceğini seçmek üzere bir bağlantının *Routingweight* değerini değiştirebilirsiniz. Trafik en yüksek *Routingweight* bağlantısı üzerinden gönderilir.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

*Routingweight* aralığı 0 ile 32000 arasındadır. Varsayılan değer 0’dır.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath yapılandırma 
Sanal ağ geçidiniz Ultra Performance veya ErGw3AZ ise [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) 'i etkinleştirebilirsiniz. FastPath, şirket içi ağınız ve sanal ağınız arasında saniye başına paket ve bağlantı gibi veri yolu performansını geliştirir. 

**Yeni bir bağlantıda FastPath yapılandırma**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**FastPath 'i etkinleştirmek için mevcut bir bağlantı güncelleştiriliyor**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="clean-up-resources"></a>Kaynakları temizleme

ExpressRoute bağlantısına artık ihtiyacınız yoksa, ağ geçidinin bulunduğu abonelikte `Remove-AzVirtualNetworkGatewayConnection` ağ geçidi ve bağlantı hattı arasındaki bağlantıyı kaldırmak için komutunu kullanın.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>Sonraki adımlar
ExpressRoute hakkında daha fazla bilgi için, bkz. ExpressRoute SSS.

> [!div class="nextstepaction"]
> [ExpressRoute SSS](expressroute-faqs.md)

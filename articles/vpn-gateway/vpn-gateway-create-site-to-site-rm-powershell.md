---
title: 'Şirket içi ağınızı bir Azure sanal ağına bağlama: siteden siteye VPN: PowerShell'
description: PowerShell kullanarak, şirket içi ağınızdan bir Azure sanal ağına, genel İnternet üzerinden bir Azure sanal ağına bir IPSec siteden siteye VPN Gateway bağlantısı oluşturun.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 1488aa6f48c05a8c2dfa2c6162c1bd1df35d4f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380517"
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>PowerShell kullanarak Siteden Siteye VPN bağlantısı ile sanal ağ oluşturma

Bu makalede, PowerShell kullanarak şirket içi ağınızdan VNet’e Siteden Siteye VPN ağ geçidi bağlantısı oluşturma işlemi gösterilir. Bu makaledeki adımlar Resource Manager dağıtım modeli için geçerlidir. Ayrıca aşağıdaki listeden farklı bir seçenek belirtip farklı bir dağıtım aracı veya dağıtım modeli kullanarak da bu yapılandırmayı oluşturabilirsiniz:

> [!div class="op_single_selector"]
> * [Azure portalı](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure portal (klasik)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Siteden Siteye VPN ağ geçidi bağlantısı, şirket içi ağınızı bir IPsec/IKE (IKEv1 veya IKEv2) tüneli üzerinden Azure sanal ağına bağlamak için kullanılır. Bu bağlantı türü için, şirket içinde yer alan ve kendisine atanmış dışarıya yönelik bir genel IP adresi atanmış olan bir VPN cihazı gerekir. VPN ağ geçitleri hakkında daha fazla bilgi için bkz. [VPN ağ geçidi hakkında](vpn-gateway-about-vpngateways.md).

![Siteden Siteye şirket içi ve dışı karışık VPN Gateway bağlantısı diyagramı](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Başlamadan önce

Yapılandırmanıza başlamadan önce aşağıdaki ölçütleri karşıladığınızı doğrulayın:

* Uyumlu bir VPN cihazı ve bu cihazı yapılandırabilecek birinin bulunduğundan emin olun. Uyumlu VPN cihazları ve cihaz yapılandırması hakkında daha fazla bilgi için bkz.[VPN Cihazları Hakkında](vpn-gateway-about-vpn-devices.md).
* VPN cihazınız için dışarıya dönük genel bir IPv4 adresi olduğunu doğrulayın.
* Şirket içi ağ yapılandırmanızda bulunan IP adresi aralıklarıyla ilgili fazla bilginiz yoksa size bu ayrıntıları sağlayabilecek biriyle çalışmanız gerekir. Bu yapılandırmayı oluşturduğunuzda, Azure’un şirket içi konumunuza yönlendireceği IP adres aralığı ön eklerini oluşturmanız gerekir. Şirket içi ağınızın alt ağlarından hiçbiri, bağlanmak istediğiniz sanal ağ alt ağlarıyla çakışamaz.

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Örnek değerler

Bu makaledeki örneklerde aşağıdaki değerler kullanılır. Bu değerleri kullanarak bir test ortamı oluşturabilir veya bu makaledeki örnekleri daha iyi anlamak için bunlara bakabilirsiniz.

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="1-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>1. bir sanal ağ ve ağ geçidi alt ağı oluşturma

Sanal ağınız yoksa bir sanal ağ oluşturun. Sanal ağ oluştururken, belirlediğiniz adres alanlarının şirket içi ağınızdaki adres alanlarından herhangi biriyle çakışmadığından emin olun. 

>[!NOTE]
>Bu VNet’in bir şirket içi konuma bağlanması için şirket içi ağ yöneticinizle bu ağ için özellikle kullanabileceğiniz bir IP adresi aralığı ayırma işlemini koordine etmeniz gerekir. VPN bağlantısının her iki tarafında bir yinelenen adres aralığı varsa, trafik beklediğiniz şekilde yönlendirilmez. Ayrıca, bu sanal ağı başka bir sanal ağa bağlamak isterseniz adres alanı diğer sanal ağ ile örtüşemez. Ağ yapılandırmanızı uygun şekilde planlamaya dikkat edin.
>
>

### <a name="about-the-gateway-subnet"></a>Ağ geçidi alt ağı hakkında

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="create-a-virtual-network-and-a-gateway-subnet"></a><a name="vnet"></a>Sanal ağ ve ağ geçidi alt ağı oluşturma

Bu örnekte, sanal ağ ve ağ geçidi alt ağı oluşturulur. Ağ geçidi alt ağı eklemeniz gereken bir sanal ağınız zaten varsa, bkz. [Önceden oluşturduğunuz bir sanal ağa, bir ağ geçidi alt ağı eklemek için](#gatewaysubnet).

Kaynak grubu oluşturun:

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location 'East US'
```

Sanal ağınızı oluşturun.

1. Değişkenleri ayarlayın.

   ```azurepowershell-interactive
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
   ```
2. VNet'i oluşturun.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
   -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
   ```

### <a name="to-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>Önceden oluşturduğunuz bir sanal ağa, bir ağ geçidi alt ağı eklemek için

Zaten bir sanal ağınız varsa, ancak bir ağ geçidi alt ağı eklemeniz gerekiyorsa bu bölümdeki adımları kullanın.

1. Değişkenleri ayarlayın.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
   ```
2. Ağ geçidi alt ağını oluşturun.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
   ```
3. Yapılandırmayı ayarlayın.

   ```azurepowershell-interactive
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```

## <a name="2-create-the-local-network-gateway"></a>2. <a name="localnet"></a> yerel ağ geçidini oluşturma

Yerel ağ geçidi (LNG) genellikle şirket içi konumunuzu ifade eder. Bu, bir sanal ağ geçidi ile aynı değildir. Siteye Azure’un başvuruda bulunmak için kullanabileceği bir ad verir, ardından bağlantı oluşturacağınız şirket içi VPN cihazının IP adresini belirtirsiniz. Ayrıca, VPN ağ geçidi üzerinden VPN cihazına yönlendirilecek IP adresi ön eklerini de belirtirsiniz. Belirttiğiniz adres ön ekleri, şirket içi adresinizde yer alan ön eklerdir. Şirket içi ağınız değişirse, ön ekleri kolayca güncelleştirebilirsiniz.

Aşağıdaki değerleri kullanın:

* *Gatewayıpaddress* , ŞIRKET içi VPN cihazınızın IP adresidir.
* *AddressPrefix* şirket içi adres alanınızdır.

Tek bir adres ön ekine sahip bir yerel ağ geçidi eklemek için:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

Birden çok adres ön ekine sahip bir yerel ağ geçidi eklemek için:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

Yerel ağ geçidinizin IP adresi ön eklerini değiştirmek için:

Bazen yerel ağ geçidi ön ekleriniz değişir. IP adresi ön eklerinizi değiştirmek için uygulayacağınız adımlar bir VPN ağ geçidi bağlantısı oluşturup oluşturmadığınıza göre değişir. Bu makalenin [Yerel bir ağ geçidinin IP adresi ön eklerini değiştirme](#modify) bölümüne bakın.

## <a name="3-request-a-public-ip-address"></a><a name="PublicIP"></a>3. genel IP adresi isteyin

Bir VPN ağ geçidinin genel bir IP adresi olmalıdır. İlk olarak IP adresi kaynağını istemeniz, sonra sanal ağ geçidinizi oluştururken bu kaynağa başvurmanız gerekir. VPN ağ geçidi oluşturulurken, IP adresi kaynağa dinamik olarak atanır. 

VPN Gateway hizmeti şu anda yalnızca *Dinamik* Genel IP adresi ayırmayı desteklemektedir. Statik bir Genel IP adresi ataması isteğinde bulunamazsınız. Ancak bu, IP adresinin VPN ağ geçidinize atandıktan sonra değiştirileceği anlamına gelmez. Genel IP adresi, yalnızca ağ geçidi silinip yeniden oluşturulduğunda değişir. VPN ağ geçidiniz üzerinde gerçekleştirilen yeniden boyutlandırma, sıfırlama veya diğer iç bakım/yükseltme işlemleri sırasında değişmez.

Sanal ağ VPN ağ geçidinize bir Genel IP adresinin atanmasını isteyin.

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="4-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>4. ağ geçidi IP adresleme yapılandırması oluşturma

Ağ geçidi yapılandırması, alt ağı (' GatewaySubnet ') ve kullanılacak genel IP adresini tanımlar. Ağ geçidi yapılandırmanızı oluşturmak için aşağıdaki örneği kullanın:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="5-create-the-vpn-gateway"></a><a name="CreateGateway"></a>5. VPN Gateway oluşturma

Sanal ağ VPN ağ geçidini oluşturun.

Aşağıdaki değerleri kullanın:

* Siteden siteye yapılandırma için *-gatewaytype* *VPN*'dir. Ağ geçidi türü her zaman, uygulamakta olduğunuz yapılandırmaya özeldir. Örneğin, başka ağ geçidi yapılandırmalarında -GatewayType değerinin ExpressRoute olması gerekebilir.
* *-VpnType*, *RouteBased* (bazı belgelerde Dinamik Ağ Geçidi olarak adlandırılır) veya *PolicyBased* (bazı belgelerde Statik Ağ Geçidi olarak adlandırılır) olabilir. VPN ağ geçidi türleri hakkında daha fazla bilgi için bkz. [VPN Gateway Hakkında](vpn-gateway-about-vpngateways.md).
* Kullanmak istediğiniz Ağ Geçidi SKU'sunu seçin. Bazı SKU’larda yapılandırma sınırlamaları vardır. Daha fazla bilgi için bkz. [Ağ geçidi SKU'ları](vpn-gateway-about-vpn-gateway-settings.md#gwsku). VPN ağ geçidini oluştururken -GatewaySku ile ilgili bir hata alırsanız, PowerShell cmdlet'lerinin en son sürümünü yüklediğinizi doğrulayın.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

Bu komutu çalıştırdıktan sonra ağ geçidi yapılandırmasının tamamlanması 45 dakikaya kadar sürebilir.

## <a name="6-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>6. VPN cihazınızı yapılandırın

Bir şirket içi ağı ile Siteden Siteye bağlantılar için VPN cihazı gerekir. Bu adımda VPN cihazınızı yapılandıracaksınız. VPN cihazınızı yapılandırırken aşağıdaki öğeler gereklidir:

- Paylaşılan bir anahtar. Siteden Siteye VPN bağlantınızı oluştururken belirttiğiniz paylaşılan anahtarın aynısıdır. Bu örneklerde temel bir paylaşılan anahtar kullanılır. Kullanmak için daha karmaşık bir anahtar oluşturmanız önerilir.
- Sanal ağ geçidinizin Genel IP adresi. Azure Portal, PowerShell veya CLI kullanarak genel IP adresini görüntüleyebilirsiniz. PowerShell kullanarak sanal ağ geçidinizin genel IP adresini bulmak için aşağıdaki örneği kullanın. Bu örnekte, VNet1GWPIP, önceki bir adımda oluşturduğunuz genel IP adresi kaynağının adıdır.

  ```azurepowershell-interactive
  Get-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="7-create-the-vpn-connection"></a><a name="CreateConnection"></a>7. VPN bağlantısını oluşturun

Bir sonraki adımda, sanal ağ geçidiniz ile VPN cihazınız arasındaki Siteden Siteye VPN bağlantısını oluşturacaksınız. Değerlerin kendinizinkilerle değiştirildiğinden emin olun. Paylaşılan anahtar, VPN cihazınızın yapılandırması için kullandığınız değerin aynısı olmalıdır. Siteden Siteye bağlantı için ‘-ConnectionType’ değerinin **IPsec** olduğunu unutmayın.

1. Değişkenleri ayarlayın.
   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```

2. Bağlantıyı oluşturun.
   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
   -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

Kısa bir süre içerisinde bağlantı kurulur.

## <a name="8-verify-the-vpn-connection"></a><a name="toverify"></a>8. VPN bağlantısını doğrulayın

VPN bağlantınızı doğrulamanın birkaç farklı yolu vardır.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Sanal makineye bağlanma

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]


## <a name="to-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>Yerel bir ağ geçidinin IP adresi ön eklerini değiştirmek için

Şirket içi konumunuza yönlendirilmesini istediğiniz IP adresi ön ekleri değişirse, yerel ağ geçidini değiştirebilirsiniz. Bu örnekleri kullanırken, değerleri ortamınızla eşleşecek şekilde değiştirin.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>Yerel bir ağ geçidi için ağ geçidi IP adresini değiştirme

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="to-delete-a-gateway-connection"></a><a name="deleteconnection"></a>Bir ağ geçidi bağlantısını silmek için

Bağlantınızın adını bilmiyorsanız, ' Get-AzVirtualNetworkGatewayConnection ' cmdlet 'ini kullanarak bulabilirsiniz.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
-ResourceGroupName TestRG1
```

## <a name="next-steps"></a>Sonraki adımlar

*  Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](../index.yml).
* BGP hakkında bilgi edinmek için [BGP’ye Genel Bakış](vpn-gateway-bgp-overview.md) ve [BGP’yi yapılandırma](vpn-gateway-bgp-resource-manager-ps.md) makalelerine bakın.
* Azure Resource Manager şablonunu kullanarak siteden siteye VPN bağlantısı oluşturma hakkında bilgi için bkz. [Siteden Siteye VPN Bağlantısı Oluşturma](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Azure Resource Manager şablonunu kullanarak sanal ağlar arası VPN bağlantısı oluşturma hakkında bilgi için bkz. [HBase coğrafi çoğaltmayı dağıtma](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
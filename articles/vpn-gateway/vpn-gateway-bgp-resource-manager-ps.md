---
title: "Azure VPN Gateway: BGP 'yi yapılandırma: PowerShell"
description: Bu makalede, Azure Resource Manager ve PowerShell kullanarak Azure VPN ağ geçitleri ile BGP 'yi yapılandırma işlemi adım adım açıklanmaktadır.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: f4c5e3ddb5a7f5c9a21d29aa24fe28e095123836
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151383"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>PowerShell kullanarak Azure VPN ağ geçitlerinde BGP yapılandırma
Bu makalede, Kaynak Yöneticisi dağıtım modelini ve PowerShell 'i kullanarak şirket içi siteden siteye (S2S) VPN bağlantısı ve VNet-VNet bağlantısı üzerinde BGP 'yi etkinleştirme adımları anlatılmaktadır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-bgp"></a>BGP hakkında
BGP iki veya daha fazla ağ arasında yönlendirme ve ulaşılabilirlik bilgilerini takas etmek üzere İnternet’te yaygın olarak kullanılan standart yönlendirme protokolüdür. BGP, her iki ağ geçidini ön eklerin ilgili ağ geçitlerinden veya yönlendiricilerden geçmeye yönelik kullanılabilirliği ve ulaşılabilirliği konusunda bilgilendiren “yolları” değiştirmek amacıyla, Azure VPN Gateways’i ve BGP eşlikleri veya komşuları olarak adlandırılan şirket içi VPN cihazlarınızı etkinleştirir. BGP ayrıca bir BGP ağ geçidinin öğrendiği yolları bir BGP eşliğinden diğer tüm BGP eşliklerine yayarak birden fazla ağ arasında geçiş yönlendirmesi sağlayabilir.

BGP 'nin avantajları hakkında daha fazla bilgi için bkz. [Azure VPN ağ geçitleri Ile BGP 'ye genel bakış](vpn-gateway-bgp-overview.md) ve BGP kullanma hakkında teknik gereksinimleri ve konuları anlama.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Azure VPN ağ geçitlerinde BGP 'yi kullanmaya başlama

Bu makalede, aşağıdaki görevleri yapmanız gereken adımlarda adım adım gösterilmektedir:

* [1. Bölüm-Azure VPN ağ geçidinizdeki BGP 'yi etkinleştirme](#enablebgp)
* Bölüm 2-BGP ile şirketler arası bağlantı kurma
* [3. kısım-BGP ile VNet-VNet bağlantısı oluşturma](#v2vbgp)

Yönergelerin her bir bölümü, ağ bağlantınızda BGP 'yi etkinleştirmek için temel bir yapı bloğu oluşturur. Üç parçayı da tamamladıktan sonra, aşağıdaki diyagramda gösterildiği gibi topolojiyi oluşturun:

![BGP topolojisi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Gereksinimlerinizi karşılayan daha karmaşık, çok atlamalı, transit ağı oluşturmak için parçaları birlikte birleştirebilirsiniz.

## <a name ="enablebgp"></a>1. Bölüm-Azure VPN Gateway BGP 'yi yapılandırma
Yapılandırma adımları, aşağıdaki diyagramda gösterildiği gibi Azure VPN Gateway 'in BGP parametrelerini ayarlar:

![BGP ağ geçidi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Başlamadan önce
* Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
* Azure Resource Manager PowerShell cmdlet 'lerini yükler. PowerShell cmdlet'lerini yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview). 

### <a name="step-1---create-and-configure-vnet1"></a>1\. adım-VNet1 oluşturma ve yapılandırma
#### <a name="1-declare-your-variables"></a>1. değişkenlerinizi bildirin
Bu alıştırma için değişkenlerimizi bildirerek başlayacağız. Aşağıdaki örnek, bu alıştırma için değerleri kullanarak değişkenleri bildirir. Üretim için yapılandırma sırasında bu değerleri kendi değerlerinizle değiştirdiğinizden emin olun. Bu tür yapılandırmaları tanımaya başlamak için adımları gözden geçiriyorsanız bu değişkenleri kullanabilirsiniz. Değişkenleri değiştirin, daha sonra kopyalayın ve PowerShell konsolunuza yapıştırın.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. aboneliğinize bağlanın ve yeni bir kaynak grubu oluşturun
Kaynak Yöneticisi cmdlet 'lerini kullanmak için PowerShell moduna geçdiğinizden emin olun. Daha fazla bilgi için [Windows PowerShell’i Resource Manager ile kullanma](../powershell-azure-resource-manager.md) konusuna bakın.

PowerShell konsolunuzu açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 oluşturma
Aşağıdaki örnek, biri GatewaySubnet, biri ön uç adı ve diğeri arka uç olarak adlandırılan TestVNet1 adlı bir sanal ağ oluşturur. Kendi değerlerinizi yerleştirirken ağ geçidi alt ağınızı özellikle GatewaySubnet olarak adlandırmanız önem taşır. Başka bir ad kullanırsanız ağ geçidi oluşturma işleminiz başarısız olur.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2\. adım-BGP parametreleriyle TestVNet1 için VPN Gateway oluşturma
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. IP ve alt ağ yapılandırması oluşturma
Sanal ağınız için oluşturacağınız ağ geçidine ayrılacak genel IP adresi isteyin. Gerekli alt ağı ve IP yapılandırmasını da tanımlayacaksınız.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. AS numarasıyla VPN Gateway oluşturun
TestVNet1 için sanal ağ geçidini oluşturun. BGP, TestVNet1 için ASN (AS Number) ayarlamak için yol tabanlı bir VPN ağ geçidi ve ayrıca ek parametresi olan-ASN gerektirir. ASN parametresini ayarlarsanız, ASN 65515 atanır. Bir ağ geçidini oluşturmak biraz zaman alabilir (tamamlanması 30 dakika ya da daha fazla sürer).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Azure BGP eş IP adresini edinin
Ağ Geçidi oluşturulduktan sonra, Azure VPN Gateway BGP eşi IP adresini edinmeniz gerekir. Bu adres, Azure VPN Gateway şirket içi VPN cihazlarınız için BGP eşi olarak yapılandırmak için gereklidir.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Son komut, Azure VPN Gateway ilgili BGP yapılandırmalarının gösterir; Örneğin:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Ağ Geçidi oluşturulduktan sonra, BGP ile şirket içi bağlantı veya VNet-VNet bağlantısı kurmak için bu ağ geçidini kullanabilirsiniz. Aşağıdaki bölümler, Alıştırmayı tamamlamaya yönelik adımları adım adım göstermektedir.

## <a name ="crossprembbgp"></a>Bölüm 2-BGP ile şirketler arası bağlantı kurma

Şirketler arası bağlantı kurmak için, şirket içi VPN cihazınızı temsil eden bir yerel ağ geçidi oluşturmanız ve VPN ağ geçidini yerel ağ geçidine bağlamak için bir bağlantı oluşturmanız gerekir. Bu adımlarda size yol gösteren makaleler olsa da, bu makale BGP yapılandırma parametrelerini belirtmek için gereken ek özellikleri içerir.

![Şirketler arası için BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Devam etmeden önce, bu alıştırmanın [1. kısmını](#enablebgp) tamamladığınızdan emin olun.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1\. adım-yerel ağ geçidini oluşturma ve yapılandırma

#### <a name="1-declare-your-variables"></a>1. değişkenlerinizi bildirin

Bu alıştırma diyagramda gösterilen yapılandırmayı oluşturmaya devam eder. Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Yerel ağ geçidi parametreleriyle ilgili dikkat edilmesi gereken birkaç şey:

* Yerel ağ geçidi, VPN ağ geçidiyle aynı veya farklı bir konum ve kaynak grubunda olabilir. Bu örnekte, farklı konumlarda farklı kaynak gruplarında gösterilmektedir.
* Yerel ağ geçidi için bildirmeniz gereken ön ek, VPN cihazınızdaki BGP eşi IP adresinizin ana bilgisayar adresidir. Bu durumda, "10.52.255.254/32" öğesinin bir/32 ön eki olur.
* Bir anımsatıcı olarak, şirket içi ağlarınız ve Azure VNet arasında farklı BGP 'ler kullanmanız gerekir. Aynı ise, şirket içi VPN cihazınız zaten diğer BGP komşuları ile ASN 'yi kullanıyorsa VNet ASN 'nizi değiştirmeniz gerekir.

Devam etmeden önce 1. Abonelik’e hala bağlı olduğunuzdan emin olun.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. site5 için yerel ağ geçidi oluşturma

Yerel ağ geçidini oluşturmadan önce, oluşturulmadıysa kaynak grubunu oluşturmayı unutmayın. Yerel ağ geçidi için iki ek parametreye dikkat edin: ASN ve BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2\. adım-VNet ağ geçidini ve yerel ağ geçidini bağlama

#### <a name="1-get-the-two-gateways"></a>1. iki ağ geçidini alın

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. TestVNet1 to site5 Connection 'ı oluşturun

Bu adımda, TestVNet1 ile site5 arasındaki bağlantıyı oluşturacaksınız. Bu bağlantı için BGP 'yi etkinleştirmek üzere "-EnableBGP $True" belirtmelisiniz. Daha önce anlatıldığı gibi, aynı Azure VPN Gateway için hem BGP hem de BGP olmayan bağlantılara sahip olmak mümkündür. Bağlantı özelliğinde BGP etkin olmadığı takdirde, BGP parametreleri her iki ağ geçidi üzerinde zaten yapılandırılmış olsa bile Azure Bu bağlantı için BGP 'yi etkinleştirmeyecektir.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

Aşağıdaki örnekte, bu alıştırma için şirket içi VPN cihazınızda BGP yapılandırma bölümüne girdiğiniz parametreler listelenmektedir:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Bağlantı birkaç dakika sonra oluşturulur ve BGP eşleme oturumu IPSec bağlantısı kurulduktan sonra başlatılır.

## <a name ="v2vbgp"></a>3. kısım-BGP ile VNet-VNet bağlantısı oluşturma

Bu bölüm, aşağıdaki diyagramda gösterildiği gibi BGP ile VNet-VNet bağlantısı ekler:

![VNet-VNet için BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Aşağıdaki yönergeler önceki adımlardan devam eder. TestVNet1 ve VPN Gateway oluşturup yapılandırmak için bir [kısım ı](#enablebgp) ve BGP 'yi doldurmanız gerekir. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1\. adım-TestVNet2 ve VPN Gateway oluşturma

Yeni sanal ağın TestVNet2, IP adresi alanının VNet aralıklarından hiçbiriyle çakışmadığından emin olmak önemlidir.

Bu örnekte, sanal ağlar aynı aboneliğe aittir. Farklı abonelikler arasında VNet-VNet bağlantıları ayarlayabilirsiniz. Daha fazla bilgi için bkz. [VNET 'Ten VNET 'e bağlantı yapılandırma](vpn-gateway-vnet-vnet-rm-ps.md). BGP 'yi etkinleştirmek için bağlantıları oluştururken "-EnableBgp $True" öğesini eklediğinizden emin olun.

#### <a name="1-declare-your-variables"></a>1. değişkenlerinizi bildirin

Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. yeni kaynak grubunda TestVNet2 oluşturun

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. BGP parametreleriyle TestVNet2 için VPN Gateway oluşturun

VNet 'iniz için oluşturacağınız ağ geçidine ayrılacak genel IP adresi isteyin ve gerekli alt ağı ve IP yapılandırmasını tanımlayın.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

AS numarasıyla VPN ağ geçidini oluşturun. Azure VPN ağ geçitleriniz üzerindeki varsayılan ASN 'yi geçersiz kılmanız gerekir. Bağlı VNET 'ler için ASNs BGP ve geçiş yönlendirmeyi etkinleştirmek üzere farklı olmalıdır.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>2\. adım-TestVNet1 ve TestVNet2 ağ geçitlerini bağlama

Bu örnekte her iki ağ geçidi de aynı abonelikte bulunur. Bu adımı aynı PowerShell oturumunda tamamlayabilirsiniz.

#### <a name="1-get-both-gateways"></a>1. her iki ağ geçidini al

1 Abonelikle oturum açıp bağlandığınızdan emin olun.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. her iki bağlantı oluştur

Bu adımda, TestVNet1 ile TestVNet2 arasındaki bağlantıyı ve TestVNet2 'den TestVNet1 'e bağlantıyı oluşturacaksınız.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> HER IKI bağlantı için BGP 'yi etkinleştirmeyi unutmayın.
> 
> 

Bu adımları tamamladıktan sonra bağlantı birkaç dakika sonra oluşturulur. VNet-VNet bağlantısı tamamlandığında BGP eşleme oturumu çalışır.

Bu alıştırmanın üç bölümünü tamamladıysanız, aşağıdaki ağ topolojisini oluşturmuş olursunuz:

![VNet-VNet için BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Sonraki adımlar

Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Adımlar için bkz. [Sanal Makine Oluşturma](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

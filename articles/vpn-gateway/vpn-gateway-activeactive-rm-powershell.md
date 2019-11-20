---
title: 'VPN ağ geçitleri için etkin-etkin S2S VPN bağlantıları yapılandırma: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Bu makalede, Azure Resource Manager ve PowerShell kullanarak Azure VPN ağ geçitleri ile etkin-etkin bağlantıları yapılandırma işlemi adım adım açıklanmaktadır.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: 6d973d81e0de407893beb5c5808962562f091d4c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871833"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Azure VPN ağ geçitleri ile etkin-etkin S2S VPN bağlantılarını yapılandırma

Bu makale, Kaynak Yöneticisi dağıtım modelini ve PowerShell 'i kullanarak etkin-etkin şirketler arası ve VNet-VNet bağlantıları oluşturma adımlarında size kılavuzluk eder.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-highly-available-cross-premises-connections"></a>Yüksek oranda kullanılabilir şirket içi bağlantılar hakkında
Şirketler arası ve VNet 'ten VNet 'e bağlantı için yüksek kullanılabilirlik elde etmek üzere, birden fazla VPN ağ geçidi dağıtmanız ve ağlarınız ile Azure arasında birden çok paralel bağlantı oluşturmanız gerekir. Bağlantı seçeneklerine ve topolojisine genel bir bakış için bkz. [yüksek oranda kullanılabilir şirket içi ve VNET 'Ten VNET 'e bağlantı](vpn-gateway-highlyavailable.md) .

Bu makalede, etkin-etkin bir çapraz şirket içi VPN bağlantısı ve iki sanal ağ arasında etkin-etkin bağlantı ayarlama yönergeleri sağlanmaktadır.

* [1. Bölüm-Azure VPN ağ geçidinizi etkin-etkin modda oluşturma ve yapılandırma](#aagateway)
* [Bölüm 2-etkin-etkin şirketler arası bağlantılar oluşturma](#aacrossprem)
* [3. kısım-etkin-sanal VNet-VNet bağlantıları oluşturma](#aav2v)

Zaten bir VPN ağ geçidiniz varsa şunları yapabilirsiniz:
* [Mevcut bir VPN ağ geçidini etkin-bekleme modundan etkin-etkin olarak güncelleştirme veya tam tersi](#aaupdate)

Gereksinimlerinizi karşılayan daha karmaşık, yüksek oranda kullanılabilir bir ağ topolojisi oluşturmak için bunları birlikte birleştirebilirsiniz.

> [!IMPORTANT]
> Etkin-etkin mod yalnızca aşağıdaki SKU 'Ları kullanır: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * HighPerformance (eski eski SKU 'Lar için)

## <a name ="aagateway"></a>1. bölüm-etkin-etkin VPN ağ geçitleri oluşturma ve yapılandırma
Aşağıdaki adımlar, Azure VPN ağ geçidinizi etkin-etkin modlarda yapılandırır. Etkin-etkin ve etkin bekleme geçitleri arasındaki temel farklılıklar:

* İki genel IP adresi ile iki ağ geçidi IP yapılandırması oluşturmanız gerekir
* EnableActiveActiveFeature bayrağını ayarlamanız gerekiyor
* Ağ Geçidi SKU 'SU VpnGw1, VpnGw2, VpnGw3 veya HighPerformance (eski SKU) olmalıdır.

Diğer özellikler, etkin olmayan etkin ağ geçitleriyle aynıdır. 

### <a name="before-you-begin"></a>Başlamadan önce
* Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
* Azure Resource Manager PowerShell cmdlet’lerini yüklemelisiniz. PowerShell cmdlet 'lerini yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell genel bakış](/powershell/azure/overview) .

### <a name="step-1---create-and-configure-vnet1"></a>1\. adım-VNet1 oluşturma ve yapılandırma
#### <a name="1-declare-your-variables"></a>1. Değişkenlerinizi bildirme
Bu alıştırmaya değişkenlerimizi bildirerek başlayacağız. Aşağıdaki örnekte bu alıştırmada kullanılan değişkenler bildirilmektedir. Üretim için yapılandırma sırasında bu değerleri kendi değerlerinizle değiştirdiğinizden emin olun. Bu tür yapılandırmaları tanımaya başlamak için adımları gözden geçiriyorsanız bu değişkenleri kullanabilirsiniz. Değişkenleri değiştirin, daha sonra kopyalayın ve PowerShell konsolunuza yapıştırın.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
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
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Aboneliğinize bağlanın ve yeni bir kaynak grubu oluşturun
Resource Manager cmdlet’lerini kullanmak için PowerShell moduna geçtiğinizden emin olun. Daha fazla bilgi için [Windows PowerShell’i Resource Manager ile kullanma](../powershell-azure-resource-manager.md) konusuna bakın.

PowerShell konsolunuzu açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 oluşturma
Aşağıdaki örnekte TestVNet1 adlı bir sanal ağ ve üç alt ağ oluşturulmaktadır. Biri GatewaySubnet, biri FrontEnd, diğeri BackEnd’dir. Kendi değerlerinizi yerleştirirken ağ geçidi alt ağınızı özellikle GatewaySubnet olarak adlandırmanız önem taşır. Başka bir ad kullanırsanız ağ geçidi oluşturma işleminiz başarısız olur.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>2\. adım-etkin-etkin mod ile TestVNet1 için VPN ağ geçidini oluşturma
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Genel IP adreslerini ve ağ geçidi IP yapılandırmasını oluşturma
Sanal ağınız için oluşturacağınız ağ geçidine ayrılacak iki genel IP adresi isteyin. Ayrıca, gereken alt ağı ve IP yapılandırmasını da tanımlayacaksınız.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Etkin-etkin yapılandırmayla VPN Gateway oluşturma
TestVNet1 için sanal ağ geçidini oluşturun. İki GatewayIpConfig girişi olduğunu ve EnableActiveActiveFeature bayrağının ayarlandığını unutmayın. Bir ağ geçidini oluşturmak biraz zaman alabilir (tamamlanması 45 dakika ya da daha fazla sürer).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Ağ Geçidi genel IP adreslerini ve BGP eşi IP adresini alma
Ağ Geçidi oluşturulduktan sonra, Azure VPN Gateway BGP eşi IP adresini edinmeniz gerekecektir. Bu adres, Azure VPN Gateway şirket içi VPN cihazlarınız için BGP eşi olarak yapılandırmak için gereklidir.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

VPN Gateway 'niz için ayrılan iki genel IP adresini ve her bir ağ geçidi örneği için karşılık gelen BGP eşi IP adreslerini göstermek için aşağıdaki cmdlet 'leri kullanın:

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

Ağ Geçidi örnekleri ve karşılık gelen BGP eşleme adresleri için genel IP adreslerinin sırası aynıdır. Bu örnekte, 40.112.190.5 genel IP 'si olan ağ geçidi VM 'si BGP eşleme adresi olarak 10.12.255.4 kullanır ve 138.91.156.129 ile ağ geçidi 10.12.255.5 kullanır. Bu bilgiler, etkin-etkin ağ geçidine bağlanan şirket içi VPN cihazlarınızı ayarlarken gereklidir. Ağ Geçidi, tüm adreslerle aşağıdaki diyagramda gösterilir:

![etkin-etkin ağ geçidi](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Ağ Geçidi oluşturulduktan sonra, etkin-etkin şirketler arası veya VNet-VNet bağlantısı kurmak için bu ağ geçidini kullanabilirsiniz. Aşağıdaki bölümler, Alıştırmayı tamamlamaya yönelik adımları adım adım göstermektedir.

## <a name ="aacrossprem"></a>2. bölüm-etkin-etkin şirketler arası bağlantı kurma
Şirketler arası bağlantı kurmak için şirket içi VPN cihazınızı temsil eden bir yerel ağ geçidi oluşturmanız ve Azure VPN ağ geçidini yerel ağ geçidine bağlamak için bir bağlantı oluşturmanız gerekir. Bu örnekte, Azure VPN Gateway etkin-etkin modunda. Sonuç olarak, yalnızca bir şirket içi VPN cihazı (yerel ağ geçidi) ve bir bağlantı kaynağı olsa da, her iki Azure VPN ağ geçidi örneği de şirket içi cihazla S2S VPN tünellerini kurar.

Devam etmeden önce lütfen bu alıştırmanın 1. [kısmını](#aagateway) tamamladığınızdan emin olun.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1\. adım-yerel ağ geçidini oluşturma ve yapılandırma
#### <a name="1-declare-your-variables"></a>1. Değişkenlerinizi bildirme
Bu alıştırma diyagramda gösterilen yapılandırmayı oluşturmaya devam edecektir. Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Yerel ağ geçidi parametreleriyle ilgili dikkat edilmesi gereken birkaç şey:

* Yerel ağ geçidi, VPN ağ geçidiyle aynı veya farklı bir konum ve kaynak grubunda olabilir. Bu örnek, bunları farklı kaynak gruplarında, ancak aynı Azure konumunda gösterir.
* Yukarıda gösterildiği gibi yalnızca bir şirket içi VPN cihazı varsa, etkin-etkin bağlantı BGP protokolüyle veya olmadan çalışabilir. Bu örnek, şirket içi bağlantı için BGP kullanır.
* BGP etkinse, yerel ağ geçidi için bildirmeniz gereken önek, VPN cihazınızdaki BGP eşi IP adresinizin ana bilgisayar adresidir. Bu durumda, "10.52.255.253/32" öğesinin bir/32 ön eki olur.
* Bir anımsatıcı olarak, şirket içi ağlarınız ve Azure VNet arasında farklı BGP 'ler kullanmanız gerekir. Aynı ise, şirket içi VPN cihazınız zaten diğer BGP komşuları ile ASN 'yi kullanıyorsa VNet ASN 'nizi değiştirmeniz gerekir.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Site5 için yerel ağ geçidi oluşturma
Devam etmeden önce lütfen 1. Abonelik’e hala bağlı olduğunuzdan emin olun. Henüz oluşturulmadıysa kaynak grubunu oluşturun.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2\. adım-VNet ağ geçidini ve yerel ağ geçidini bağlama
#### <a name="1-get-the-two-gateways"></a>1. İki ağ geçidini alın

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. TestVNet1 to site5 Connection 'ı oluşturma
Bu adımda, TestVNet1 ile Site5_1 arasındaki bağlantıyı "EnableBGP" $True olarak ayarlayın.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Şirket içi VPN cihazınız için VPN ve BGP parametreleri
Aşağıdaki örnekte, bu alıştırma için şirket içi VPN cihazınızda BGP yapılandırması bölümüne gireceğiniz parametreler listelenmektedir:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Bağlantı birkaç dakika sonra oluşturulmalıdır ve IPSec bağlantısı kurulduktan sonra BGP eşleme oturumu başlatılır. Bu örnek şu ana kadar tek bir şirket içi VPN cihazını yapılandırmıştır ve bu nedenle aşağıda gösterildiği gibi diyagramda sonuç verilmiştir:

![etkin-etkin-çapraz spkal](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>3\. adım-şirket içi VPN cihazlarını etkin-etkin VPN ağ geçidine bağlama
Aynı şirket içi ağda iki VPN aygıtınız varsa, Azure VPN ağ geçidini ikinci VPN cihazına bağlayarak çift artıklık elde edebilirsiniz.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Site5 için ikinci yerel ağ geçidini oluşturma
İkinci yerel ağ geçidi için ağ geçidi IP adresi, adres ön eki ve BGP eşleme adresi, aynı şirket içi ağ için önceki yerel ağ geçidi ile çakışmamalıdır.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. VNet ağ geçidini ve ikinci yerel ağ geçidini bağlama
"EnableBGP" $True olarak ayarlanan TestVNet1 to Site5_2 ile bağlantı oluştur

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. İkinci şirket içi VPN cihazınız için VPN ve BGP parametreleri
Benzer şekilde, aşağıdaki ikinci VPN cihazına gireceğiniz parametreleri listeler:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Bağlantı (tüneller) kurulduktan sonra, şirket içi ağınızı ve Azure 'u bağlayan çift yedekli VPN cihazlarından ve tünellere sahip olursunuz:

![Çift yedeklilik-çapraz eğri](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>3. kısım-etkin-etkin VNet-VNet bağlantısı oluşturma
Bu bölümde BGP ile etkin bir VNet-VNet bağlantısı oluşturulur. 

Aşağıdaki yönergeler, yukarıda listelenen geçmiş adımların devamı niteliğindedir. TestVNet1 ve BGP ile VPN Gateway oluşturmak ve yapılandırmak için [Bölüm 1](#aagateway) ' i tamamlamalısınız. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1\. adım-TestVNet2 ve VPN Gateway oluşturma
Yeni sanal ağın TestVNet2, IP adresi alanının VNet aralıklarından hiçbiriyle çakışmadığından emin olmak önemlidir.

Bu örnekte, sanal ağlar aynı aboneliğe aittir. Farklı abonelikler arasında VNet-VNet bağlantıları ayarlayabilirsiniz; daha fazla bilgi edinmek için lütfen [VNET 'Ten VNET 'e bağlantı yapılandırma](vpn-gateway-vnet-vnet-rm-ps.md) bölümüne bakın. BGP 'yi etkinleştirmek için bağlantıları oluştururken "-EnableBgp $True" öğesini eklediğinizden emin olun.

#### <a name="1-declare-your-variables"></a>1. Değişkenlerinizi bildirme
Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
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
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Yeni kaynak grubunda TestVNet2 oluştur

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. TestVNet2 için etkin-etkin VPN Gateway oluşturma
Sanal ağınız için oluşturacağınız ağ geçidine ayrılacak iki genel IP adresi isteyin. Ayrıca, gereken alt ağı ve IP yapılandırmasını da tanımlayacaksınız.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

AS numarası ve "EnableActiveActiveFeature" bayrağıyla VPN ağ geçidini oluşturun. Azure VPN ağ geçitlerinizin varsayılan ASN 'yi geçersiz kılmalısınız. Bağlı VNET 'ler için ASNs BGP ve geçiş yönlendirmeyi etkinleştirmek üzere farklı olmalıdır.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>2\. adım-TestVNet1 ve TestVNet2 ağ geçitlerini bağlama
Bu örnekte her iki ağ geçidi de aynı abonelikte bulunur. Bu adımı aynı PowerShell oturumunda tamamlayabilirsiniz.

#### <a name="1-get-both-gateways"></a>1. Her iki ağ geçidini al
1 Abonelikle oturum açıp bağlandığınızdan emin olun.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Her iki bağlantı oluştur
Bu adımda, TestVNet1 ile TestVNet2 arasında bağlantı ve TestVNet2 'den TestVNet1 'e bağlantı oluşturacaksınız.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> HER IKI bağlantı için BGP 'yi etkinleştirmeyi unutmayın.
> 
> 

Bu adımları tamamladıktan sonra, bağlantı birkaç dakika içinde oluşturulur ve sanal ağdan VNet bağlantısı çift yedeklilik ile tamamlandığında BGP eşleme oturumu çalışır:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Mevcut bir VPN ağ geçidini güncelleştirme

Bu bölüm, var olan bir Azure VPN ağ geçidini etkin-bekleme modundan etkin-etkin moda değiştirmenize yardımcı olur veya tam tersi de geçerlidir.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Etkin bekleme ağ geçidini etkin-etkin bir ağ geçidine değiştirme

Aşağıdaki örnek etkin bir bekleme ağ geçidini etkin-etkin bir ağ geçidine dönüştürür. Etkin bekleme bir ağ geçidini etkin-etkin olarak değiştirdiğinizde, başka bir genel IP adresi oluşturun ve ardından ikinci bir ağ geçidi IP yapılandırması ekleyin.

#### <a name="1-declare-your-variables"></a>1. Değişkenlerinizi bildirme

Örnekler için kullanılan aşağıdaki parametreleri kendi yapılandırmanız için gerekli olan ayarlarla değiştirin, ardından bu değişkenleri bildirin.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Değişkenleri bildirdikten sonra bu örneği PowerShell konsolunuza kopyalayabilir ve yapıştırabilirsiniz.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Genel IP adresini oluşturun, sonra ikinci ağ geçidi IP yapılandırmasını ekleyin

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Etkin-etkin modu etkinleştirme ve ağ geçidini güncelleştirme

Bu adımda, etkin-etkin modu etkinleştirir ve ağ geçidini güncelleştirebilirsiniz. Örnekte, VPN Gateway Şu anda eski bir standart SKU kullanıyor. Ancak etkin-etkin, standart SKU 'YU desteklemez. Eski SKU 'YU desteklenmiş bir şekilde yeniden boyutlandırmak için (Bu durumda, HighPerformance), yalnızca kullanmak istediğiniz desteklenen eski SKU 'YU belirtirsiniz.

* Bu adımı kullanarak eski SKU 'YU yeni SKU 'Lardan birine değiştiremezsiniz. Eski bir SKU 'yu yalnızca, desteklenen başka bir eski SKU için yeniden boyutlandırabilirsiniz. Örneğin, standart bir eski SKU olduğundan ve VpnGw1 geçerli bir SKU olduğundan, SKU 'YU standart olarak VpnGw1 (VpnGw1 etkin-etkin için desteklenir) olarak değiştiremezsiniz. SKU 'Ları boyutlandırma ve geçirme hakkında daha fazla bilgi için bkz. [ağ geçidi SKU 'ları](vpn-gateway-about-vpngateways.md#gwsku).

* Geçerli bir SKU 'yu yeniden boyutlandırmak istiyorsanız, örneğin VpnGw1 to VpnGw3, SKU 'Lar aynı SKU ailesinden olduğundan bu adımı kullanabilirsiniz. Bunu yapmak için şu değeri kullanırsınız:```-GatewaySku VpnGw3```

Bunu ortamınızda kullanırken, ağ geçidini yeniden boyutlandırmanıza gerek yoksa-GatewaySku belirtmeniz gerekmez. Bu adımda, PowerShell 'de ağ geçidi nesnesini gerçek güncelleştirmeyi tetikleyecek şekilde ayarlamanız gerektiğini unutmayın. Bu güncelleştirme, ağ geçidinizi yeniden boyutlandırsanız bile 30 ila 45 dakika sürebilir.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Etkin-etkin bir ağ geçidini etkin-bekleme ağ geçidine değiştirme
#### <a name="1-declare-your-variables"></a>1. Değişkenlerinizi bildirme

Örnekler için kullanılan aşağıdaki parametreleri kendi yapılandırmanız için gerekli olan ayarlarla değiştirin, ardından bu değişkenleri bildirin.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Değişkenleri bildirdikten sonra, kaldırmak istediğiniz IP yapılandırmasının adını alın.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Ağ geçidi IP yapılandırmasını kaldırın ve etkin-etkin modunu devre dışı bırakın

Ağ geçidi IP yapılandırmasını kaldırmak ve etkin-etkin modunu devre dışı bırakmak için bu örneği kullanın. Gerçek güncelleştirmeyi tetiklemek için PowerShell 'de ağ geçidi nesnesini ayarlamanız gerektiğini unutmayın.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Bu güncelleştirme 30 ila 45 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar
Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Adımlar için bkz. [Sanal Makine Oluşturma](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

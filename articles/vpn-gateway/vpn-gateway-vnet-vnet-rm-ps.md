---
title: "VNet 'ten VNet 'e bağlantı kullanarak bir Azure sanal ağını başka bir VNet 'e bağlama: PowerShell | Microsoft Docs"
description: Sanal ağlar arası bağlantı ve PowerShell kullanarak sanal ağları birbirine bağlayın.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: cherylmc
ms.openlocfilehash: dbf59740af64bf8d403b6596a17646304c0f1eb0
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385780"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>PowerShell kullanarak sanal ağlar arası VPN ağ geçidi bağlantısı yapılandırma

Bu makale, sanal ağlar arası bağlantı türünü kullanarak sanal ağları bağlamanıza yardımcı olur. Sanal ağlar aynı ya da farklı bölgelerde ve aynı ya da farklı aboneliklerde bulunuyor olabilirler. Farklı aboneliklerden sanal ağları bağlarken aboneliklerin aynı Active Directory kiracısıyla ilişkilendirilmiş olması gerekmez.

Bu makaledeki adımlar Resource Manager dağıtım modeli için geçerlidir ve PowerShell kullanır. Ayrıca aşağıdaki listeden farklı bir seçenek belirtip farklı bir dağıtım aracı veya dağıtım modeli kullanarak da bu yapılandırmayı oluşturabilirsiniz:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure portal (klasik)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Farklı dağıtım modellerini bağlama - Azure portalı](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Farklı dağıtım modellerini bağlama - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

## <a name="about"></a>Sanal ağları bağlama hakkında

Sanal ağları bağlamanın birden çok yolu vardır. Aşağıdaki bölümlerde, sanal ağları bağlamak için farklı yollar açıklanmaktadır.

### <a name="vnet-to-vnet"></a>Sanal Ağdan Sanal Ağa

Sanal ağlar arası bağlantı yapılandırma, sanal ağları kolayca bağlamanın güzel bir yoludur. Sanal ağlar arası bağlantı türünü (VNet2VNet) kullanarak bir sanal ağı başka bir sanal ağa bağlamak, bir şirket içi konumuna Siteden Siteye IPsec bağlantısı oluşturmaya benzerdir.  Her iki bağlantı türü de IPsec/IKE kullanarak güvenli bir tünel sunmak üzere bir VPN ağ geçidi kullanır ve her ikisi de iletişim kurarken aynı şekilde çalışır. Bağlantı türleri arasındaki fark, yerel ağ geçidini yapılandırma şeklidir. Sanal ağlar arası bağlantı oluşturduğunuzda yerel ağ geçidi adres alanını görmezsiniz. Bu alan otomatik olarak oluşturulup doldurulur. Bir sanal ağın adres alanını güncelleştirirseniz, diğer sanal ağ güncelleştirilmiş adres alanına yönlendireceğini otomatik olarak bilir. Sanal ağlar arası bağlantı oluşturma, genellikle sanal ağlar arasında Siteden Siteye bağlantı oluşturmadan daha hızlı ve kolaydır.

### <a name="site-to-site-ipsec"></a>Siteden Siteye (IPsec)

Karmaşık bir ağ yapılandırmasıyla çalışıyorsanız, sanal ağlarınızı, sanal ağlar arası bağlantı adımları yerine [Siteden Siteye](vpn-gateway-create-site-to-site-rm-powershell.md) adımlarını kullanarak bağlamayı tercih edebilirsiniz. Siteden Siteye adımlarını kullandığınızda, yerel ağ geçitlerini kendiniz oluşturup yapılandırırsınız. Her sanal ağa ait yerel ağ geçidi, diğer sanal ağa yerel bir site gibi davranır. Bunun yapılması, trafiği yönlendirmek için yerel ağ geçidine ait ek bir adres alanı belirtmenize olanak sağlar. Bir sanal ağın adres alanı değiştiğinde, değişimi yansıtmak için ona karşılık gelen yerel ağ geçidini güncelleştirmeniz gerekir. Otomatik olarak güncelleştirilmez.

### <a name="vnet-peering"></a>VNet eşlemesi

Sanal ağlarınızı, Sanal Ağ Eşleme kullanarak bağlamayı düşünebilirsiniz. Sanal ağ eşleme, bir VPN gateway kullanmadığından farklı kısıtlamaları vardır. Ayrıca, [sanal ağ eşleme fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-network), [Sanal Ağlar Arası VPN Gateway fiyatlandırmasından](https://azure.microsoft.com/pricing/details/vpn-gateway) farklı olarak hesaplanır. Daha fazla bilgi için bkz. [VNet eşlemesi](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Neden sanal ağdan sanal ağa bağlantı oluşturmalısınız?

Sanal ağları şu sebeplerden dolayı sanal ağlar arası bağlantıyı kullanarak bağlamak isteyebilirsiniz:

* **Çapraz bölge coğrafi artıklığı ve coğrafi-durum**

  * Kendi coğrafi çoğaltma veya eşitlemenizi, güvenli bağlantıyla İnternet’te uç noktalara gitmeden ayarlayabilirsiniz.
  * Azure Traffic Manager ve Load Balancer ile birçok Azure bölgesinde coğrafi yedeklilik imkanıyla yüksek oranda kullanılabilir iş yükü oluşturabilirsiniz. Buna önemli bir örnek olarak SQL Always On ile birden fazla Azure bölgesine yayılan Kullanılabilirlik Grupları’nı birlikte kurmak verilebilir.
* **Yalıtım veya yönetim sınır bölgesel çok katmanlı uygulamalar**

  * Yalıtım ve yönetim gereksinimlerinden dolayı aynı bölge içinde birbirlerine bağlı birden fazla sanal ağ ile çok katmanlı uygulamalar kurabilirsiniz.

Hatta Sanal Ağdan Sanal Ağa iletişim çok siteli yapılandırmalarla bile birleştirilebilir. Bu özellik şirket içi ve şirket dışı bağlantıyla ağ içi bağlantıyı birleştiren ağ topolojileri kurabilmenize olanak sağlar.

## <a name="steps"></a>Hangi sanal ağlar arası bağlantı adımlarını kullanmalıyım?

Bu makalede iki farklı adım kümesi görürsünüz. Bir adım kümesi [Aynı abonelikte bulunan sanal ağlar](#samesub), biri ise [Farklı aboneliklerde bulunan sanal ağlar](#difsub) içindir.
Kümeler arasındaki temel fark, farklı aboneliklerde bulunan sanal ağlar için bağlantıları yapılandırırken ayrı PowerShell oturumları kullanmanızın gerekmesidir. 

Bu alıştırma için, yapılandırmaları birleştirebilir veya yalnızca birlikte çalışmak istediğiniz yapılandırmayı seçebilirsiniz. Tüm yapılandırmalar VNet-VNet bağlantı türünü kullanır. Ağ trafiği, birbirine doğrudan bağlı sanal ağlar arasında akar. Bu alıştırmada TestVNet4 trafiği TestVNet5’e yönlendirilmez.

* [Aynı abonelikte bulunan sanal](#samesub)ağlar: Bu yapılandırmanın adımları TestVNet1 ve TestVNet4'ü kullanır.

  ![v2v diyagramı](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [Farklı aboneliklerde bulunan sanal](#difsub)ağlar: Bu yapılandırmanın adımları TestVNet1 ve TestVNet5 kullanır.

  ![v2v diyagramı](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="samesub"></a>Aynı abonelikte olan Vnet'ler bağlanma

### <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Bir ağ geçidi oluşturmak için 45 dakika sürdüğü için Azure Cloud Shell bu alıştırma sırasında düzenli aralıklarla zaman aşımına uğrayacaktır. Cloud Shell, terminalin sol üst kısmındaki ' a tıklayarak yeniden başlatabilirsiniz. Terminali yeniden başlattığınızda herhangi bir değişkeni yeniden bildirdiğinizden emin olun.

* Azure PowerShell modülünün en son sürümünü yerel olarak yüklemeyi tercih ediyorsanız, bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/overview).

### <a name="Step1"></a>1. adım -, IP adres aralıklarını planlama

Aşağıdaki adımlarda kendi ağ geçidi alt ağları ve yapılandırmalarıyla birlikte iki sanal ağ oluşturursunuz. Daha sonra iki sanal ağ arasında bir VPN bağlantısı oluşturursunuz. Ağ yapılandırmanız için IP adres aralıklarını planlamanız önemlidir. Sanal ağ aralıklarınızın ya da yerel ağ aralıklarınızın hiçbir şekilde çakışmadığından emin olmalısınız. Bu örneklerde bir DNS sunucusu eklemiyoruz. Sanal ağlarınız için ad çözümlemesi istiyorsanız bkz. [Ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Örneklerde aşağıdaki değerler kullanılmaktadır:

**Değerler TestVNet1 için:**

* VNET Adı: TestVNet1
* Kaynak Grubu: TestRG1
* Konum: East US
* TestVNet1 10.11.0.0/16 & 10.12.0.0/16
* Uçta 10.11.0.0/24
* Sunucusundan 10.12.0.0/24
* GatewaySubnet 10.12.255.0/27
* GatewayName VNet1GW
* Genel IP: VNet1GWIP
* VPNType RouteBased
* Bağlantı (1to4): VNet1toVNet4
* Bağlantı (1to5): VNet1toVNet5 (farklı aboneliklerdeki sanal ağlar Için)
* Belirtildi VNet2VNet

**Değerler TestVNet4 için:**

* VNET Adı: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* Uçta 10.41.0.0/24
* Sunucusundan 10.42.0.0/24
* GatewaySubnet 10.42.255.0/27
* Kaynak Grubu: TestRG4
* Konum: Batı ABD
* GatewayName VNet4GW
* Genel IP: VNet4GWIP
* VPNType RouteBased
* Bağlantı: VNet4toVNet1
* Belirtildi VNet2VNet


### <a name="Step2"></a>Adım 2 - oluşturma ve TestVNet1 yapılandırma

1. Abonelik ayarlarınızı doğrulayın.

   Bilgisayarınızda PowerShell 'i yerel olarak çalıştırıyorsanız hesabınıza bağlanın. Azure Cloud Shell kullanıyorsanız, otomatik olarak bağlanırsınız.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Hesapla ilişkili abonelikleri kontrol edin.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Birden fazla aboneliğiniz varsa, kullanmak istediğiniz aboneliği belirtin.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName nameofsubscription
   ```
2. Değişkenlerinizi bildirin. Bu örnekte bu alıştırmaya yönelik değerleri kullanan değişkenler bildirilmektedir. Çoğu durumda değerleri kendi değerlerinizle değiştirmeniz gerekir. Ancak, bu tür yapılandırmaları tanımaya başlamak için adımları gözden geçiriyorsanız bu değişkenleri kullanabilirsiniz. Gerekirse değişkenleri değiştirin, daha sonra kopyalayın ve PowerShell konsolunuza yapıştırın.

   ```azurepowershell-interactive
   $RG1 = "TestRG1"
   $Location1 = "East US"
   $VNetName1 = "TestVNet1"
   $FESubName1 = "FrontEnd"
   $BESubName1 = "Backend"
   $VNetPrefix11 = "10.11.0.0/16"
   $VNetPrefix12 = "10.12.0.0/16"
   $FESubPrefix1 = "10.11.0.0/24"
   $BESubPrefix1 = "10.12.0.0/24"
   $GWSubPrefix1 = "10.12.255.0/27"
   $GWName1 = "VNet1GW"
   $GWIPName1 = "VNet1GWIP"
   $GWIPconfName1 = "gwipconf1"
   $Connection14 = "VNet1toVNet4"
   $Connection15 = "VNet1toVNet5"
   ```
3. Bir kaynak grubu oluşturun.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
4. TestVNet1 için alt ağ yapılandırmalarını oluşturun. Bu örnekte TestVNet1 adlı bir sanal ağ ve üç alt ağ oluşturulmaktadır. Biri GatewaySubnet, biri FrontEnd, diğeri BackEnd’dir. Kendi değerlerinizi yerleştirirken ağ geçidi alt ağınızı özellikle GatewaySubnet olarak adlandırmanız önem taşır. Başka bir ad kullanırsanız ağ geçidi oluşturma işleminiz başarısız olur. Bu nedenle, aşağıdaki değişken aracılığıyla atanmaz.

   Aşağıdaki örnekte daha önce belirlediğiniz değişkenler kullanılmaktadır. Bu örnekte ağ geçidi alt ağı bir /27 kullanmaktadır. /29 kadar küçük bir ağ geçidi alt ağı oluşturmak mümkün olsa da en az /28 veya /27’yi seçerek daha fazla adres içeren büyük bir alt ağ oluşturmanızı öneririz. Bu, gelecekte isteyebileceğiniz ek yapılandırmaları da içerecek yeteri kadar adres sağlayacaktır.

   ```azurepowershell-interactive
   $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
   $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
   $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix1
   ```
5. TestVNet1’i oluşturun.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
   ```
6. Sanal ağınız için oluşturacağınız ağ geçidine ayrılacak genel IP adresi isteyin. AllocationMethod değerinin Dinamik olduğuna dikkat edin. Kullanmak istediğiniz IP adresini belirtemezsiniz. IP adresi, ağ geçidinize dinamik olarak ayrılır. 

   ```azurepowershell-interactive
   $gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AllocationMethod Dynamic
   ```
7. Ağ geçidi yapılandırmasını oluşturun. Ağ geçidi yapılandırması, kullanılacak alt ağı ve genel IP adresini tanımlar. Ağ geçidi yapılandırmanızı oluşturmak için örneği kullanın.

   ```azurepowershell-interactive
   $vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
   $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
   -Subnet $subnet1 -PublicIpAddress $gwpip1
   ```
8. TestVNet1 için ağ geçidini oluşturun. Bu adımda TestVNet1’iniz için sanal ağ geçidi oluşturursunuz. Sanal Ağdan Sanal Ağa yapılandırmaları, RouteBased bir VPNType gerektirir. Bir ağ geçidinin oluşturulması, seçili ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
   -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

Komutları tamamladıktan sonra, bu ağ geçidinin oluşturulması 45 dakikaya kadar zaman alır. Azure Cloud Shell kullanıyorsanız, Cloud Shell terminalinin sol üst kısmında bulunan CloudShell oturumunuzu yeniden başlatabilir ve ardından TestVNet4 ' yi yapılandırabilirsiniz. TestVNet1 ağ geçidi tamamlanana kadar beklemeniz gerekmez.

### <a name="step-3---create-and-configure-testvnet4"></a>3\. Adım - TestVNet4’ü oluşturma ve yapılandırma

TestVNet1 yapılandırıldıktan sonra TestVNet4’ü oluşturun. Aşağıdaki adımları, verilen değerleri gerektiğinde kendi değerlerinizle değiştirerek takip edin.

1. Değişkenlerinizi bağlayın ve bildirin. Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun.

   ```azurepowershell-interactive
   $RG4 = "TestRG4"
   $Location4 = "West US"
   $VnetName4 = "TestVNet4"
   $FESubName4 = "FrontEnd"
   $BESubName4 = "Backend"
   $VnetPrefix41 = "10.41.0.0/16"
   $VnetPrefix42 = "10.42.0.0/16"
   $FESubPrefix4 = "10.41.0.0/24"
   $BESubPrefix4 = "10.42.0.0/24"
   $GWSubPrefix4 = "10.42.255.0/27"
   $GWName4 = "VNet4GW"
   $GWIPName4 = "VNet4GWIP"
   $GWIPconfName4 = "gwipconf4"
   $Connection41 = "VNet4toVNet1"
   ```
2. Bir kaynak grubu oluşturun.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG4 -Location $Location4
   ```
3. TestVNet4 için alt ağ yapılandırmalarını oluşturun.

   ```azurepowershell-interactive
   $fesub4 = New-AzVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
   $besub4 = New-AzVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
   $gwsub4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix4
   ```
4. TestVNet4’ü oluşturun.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
   ```
5. Genel bir IP adresi isteyin.

   ```azurepowershell-interactive
   $gwpip4 = New-AzPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AllocationMethod Dynamic
   ```
6. Ağ geçidi yapılandırmasını oluşturun.

   ```azurepowershell-interactive
   $vnet4 = Get-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
   $subnet4 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
   $gwipconf4 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
   ```
7. TestVNet4 ağ geçidini oluşturun. Bir ağ geçidinin oluşturulması, seçili ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
   -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-4---create-the-connections"></a>4\. Adım - Bağlantıları oluşturma

Her iki ağ geçidinin de tamamlanmasını bekleyin. Azure Cloud Shell oturumunuzu yeniden başlatın ve değerlerini yeniden bildirmek için 2. adımdaki değişkenleri kopyalayıp konsola yapıştırın.

1. Her iki sanal ağ geçidini de alın.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   $vnet4gw = Get-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
   ```
2. TestVNet1 - TestVNet4 bağlantısını oluşturun. Bu adımda TestVNet1 - TestVNet4 arasında bağlantı oluşturursunuz. Örneklerde paylaşılan bir anahtar göreceksiniz. Paylaşılan anahtar için kendi değerlerinizi kullanabilirsiniz. Paylaşılan anahtarın her iki bağlantıyla da eşleşiyor olması önemlidir. Bir bağlantı oluşturmak çok zaman almaz.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
   -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
3. TestVNet4 - TestVNet1 bağlantısı oluşturun. Bu adım, bağlantıyı TestVNet4’ten TestVNet1’e yönüne kuracak olmanız dışında yukarıdaki adımla aynıdır. Paylaşılan anahtarların eşleştiğinden emin olun. Bağlantı birkaç dakika içerisinde kurulacaktır.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
   -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. Bağlantınızı doğrulayın. [Bağlantınızı doğrulama](#verify) bölümüne bakın.

## <a name="difsub"></a>Farklı abonelikleri olan Vnet'ler bağlanma

Bu senaryoda TestVNet1 ve TestVNet5’i bağlarsınız. TestVNet1 ve TestVNet5 farklı aboneliklerde bulunur. Aboneliklerin aynı Active Directory kiracısıyla ilişkilendirilmiş olması gerekmez.

Bu adımlar ve önceki adım kümesi arasındaki fark, yapılandırma adımlarının bir kısmının ikinci abonelik bağlamında farklı bir PowerShell oturumunda tamamlanması gerektiğidir. Bu durum özellikle iki aboneliğin farklı kuruluşlara ait olduğu durumlarda geçerlidir.

Bu alıştırmada değişen abonelik bağlamı nedeniyle, 8. adıma geldiğinizde Azure Cloud Shell kullanmak yerine PowerShell 'i yerel olarak kullanmayı daha kolay bulabilirsiniz.

### <a name="step-5---create-and-configure-testvnet1"></a>5\. Adım - TestVNet1'i oluşturma ve yapılandırma

TestVNet1'i ve TestVNet1 için VPN Ağ Geçidini oluşturup yapılandırmak için önceki bölümde bulunan [1. Adımı](#Step1) ve [2. Adımı](#Step2) tamamlamanız gerekir. Bu yapılandırma için önceki bölümdeki TestVNet4'ü oluşturmanız gerekmez, ancak oluştursanız dahi bu adımlarla herhangi bir çakışma ortaya çıkmaz. 1\. Adım'ı ve 2. Adım'ı tamamladıktan sonra 6. Adım'a geçerek TestVNet5'i oluşturun.

### <a name="step-6---verify-the-ip-address-ranges"></a>6\. Adım - IP adresi aralıklarını doğrulama

Yeni sanal ağ olan TestVNet5’in IP adresi alanının kendi Sanal Ağ aralıklarınız veya yerel ağ geçidi aralıkları ile çakışmaması önemlidir. Bu örnekte sanal ağlar farklı kuruluşlara ait olabilir. Bu alıştırmada TestVNet5 için aşağıdaki değerleri kullanabilirsiniz:

**Değerler TestVNet5 için:**

* VNET Adı: TestVNet5
* Kaynak Grubu: TestRG5
* Konum: Japonya Doğu
* TestVNet5 10.51.0.0/16 & 10.52.0.0/16
* Uçta 10.51.0.0/24
* Sunucusundan 10.52.0.0/24
* GatewaySubnet 10.52.255.0.0/27
* GatewayName VNet5GW
* Genel IP: VNet5GWIP
* VPNType RouteBased
* Bağlantı: VNet5toVNet1
* Belirtildi VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>7\. Adım - TestVNet5'i oluşturma ve yapılandırma

Bu adım, yeni abonelik bağlamında tamamlanmalıdır. Bu kısım, aboneliğin sahibi olan farklı bir kuruluşun yöneticisi tarafından tamamlanabilir.

1. Değişkenlerinizi bildirin. Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun.

   ```azurepowershell-interactive
   $Sub5 = "Replace_With_the_New_Subscription_Name"
   $RG5 = "TestRG5"
   $Location5 = "Japan East"
   $VnetName5 = "TestVNet5"
   $FESubName5 = "FrontEnd"
   $BESubName5 = "Backend"
   $GWSubName5 = "GatewaySubnet"
   $VnetPrefix51 = "10.51.0.0/16"
   $VnetPrefix52 = "10.52.0.0/16"
   $FESubPrefix5 = "10.51.0.0/24"
   $BESubPrefix5 = "10.52.0.0/24"
   $GWSubPrefix5 = "10.52.255.0/27"
   $GWName5 = "VNet5GW"
   $GWIPName5 = "VNet5GWIP"
   $GWIPconfName5 = "gwipconf5"
   $Connection51 = "VNet5toVNet1"
   ```
2. 5 aboneliğe bağlanın. PowerShell konsolunuzu açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Hesapla ilişkili abonelikleri kontrol edin.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Kullanmak istediğiniz aboneliği belirtin.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName $Sub5
   ```
3. Yeni bir kaynak grubu oluşturun.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG5 -Location $Location5
   ```
4. TestVNet5 için alt ağ yapılandırmalarını oluşturun.

   ```azurepowershell-interactive
   $fesub5 = New-AzVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
   $besub5 = New-AzVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
   $gwsub5 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
   ```
5. TestVNet5’i oluşturun.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
   -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
   ```
6. Genel bir IP adresi isteyin.

   ```azurepowershell-interactive
   $gwpip5 = New-AzPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
   -Location $Location5 -AllocationMethod Dynamic
   ```
7. Ağ geçidi yapılandırmasını oluşturun.

   ```azurepowershell-interactive
   $vnet5 = Get-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
   $subnet5  = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
   $gwipconf5 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
   ```
8. TestVNet5 ağ geçidini oluşturun.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
   -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-8---create-the-connections"></a>8\. Adım - Bağlantıları oluşturma

Bu örnekte ağ geçitleri farklı aboneliklerde olduğundan bu adımı [1. Abonelik] ve [5. Abonelik] olarak iki PowerShell oturumuna ayıracağız.

1. **[1. Abonelik]** 1. Abonelik için sanal ağ geçidini alın. Aşağıdaki örneği çalıştırmadan önce oturum açın ve 1. aboneliğe bağlanın:

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   ```

   Aşağıdaki öğelerin çıktısını kopyalayın ve 5. Aboneliğin yöneticisine e-posta veya başka bir yöntemle gönderin.

   ```azurepowershell-interactive
   $vnet1gw.Name
   $vnet1gw.Id
   ```

   Bu iki öğenin değerleri aşağıdaki çıktı örneği ile benzer değerlere sahip olmalıdır:

   ```
   PS D:\> $vnet1gw.Name
   VNet1GW
   PS D:\> $vnet1gw.Id
   /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```
2. **[5. Abonelik]** 5. Abonelik için sanal ağ geçidini alın. Aşağıdaki örneği çalıştırmadan önce oturum açın ve 5. aboneliğe bağlanın:

   ```azurepowershell-interactive
   $vnet5gw = Get-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   ```

   Aşağıdaki öğelerin çıktısını kopyalayın ve 1. Abonelik’in yöneticisine e-posta veya başka bir yöntemle gönderin

   ```azurepowershell-interactive
   $vnet5gw.Name
   $vnet5gw.Id
   ```

   Bu iki öğenin değerleri aşağıdaki çıktı örneği ile benzer değerlere sahip olmalıdır:

   ```
   PS C:\> $vnet5gw.Name
   VNet5GW
   PS C:\> $vnet5gw.Id
   /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```
3. **[1. Abonelik]** TestVNet1 - TestVNet5 bağlantısını oluşturun. Bu adımda TestVNet1 - TestVNet5 arasında bağlantı oluşturursunuz. Burada farklı olan, $vnet5gw’nun farklı bir abonelikte bulunmasından dolayı doğrudan alınamıyor olmasıdır. Yukarıdaki adımlarda belirtilen 1. Abonelik’ten iletişim sağlanan yeni bir PowerShell nesnesi oluşturmanız gerekecektir. Aşağıdaki örneği kullanın. Ad, Kimlik ve paylaşılan anahtar değerlerini kendi değerlerinizle değiştirin. Paylaşılan anahtarın her iki bağlantıyla da eşleşiyor olması önemlidir. Bir bağlantı oluşturmak çok zaman almaz.

   Aşağıdaki örneği çalıştırmadan önce 1. Abonelik'e bağlanın:

   ```azurepowershell-interactive
   $vnet5gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet5gw.Name = "VNet5GW"
   $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
   $Connection15 = "VNet1toVNet5"
   New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. **[5. Abonelik]** TestVNet5 - TestVNet1 bağlantısını oluşturun. Bu adım, bağlantıyı TestVNet5’ten TestVNet1’e doğru kuracak olmanızın dışında yukarıdaki adımla aynıdır. 1 Abonelikten elde edilen değerleri temel alan bir PowerShell nesnesi oluşturma işlemi burada da geçerlidir. Bu adımda, paylaşılan anahtarların eşleştiğinden emin olun.

   Aşağıdaki örneği çalıştırmadan önce 5. Abonelik'e bağlanın:

   ```azurepowershell-interactive
   $vnet1gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet1gw.Name = "VNet1GW"
   $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
   $Connection51 = "VNet5toVNet1"
   New-AzVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```

## <a name="verify"></a>Nasıl bir bağlantıyı doğrulamak için

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="faq"></a>Sanal ağlar arası bağlantılar hakkında SSS

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Virtual Machines belgeleri](https://docs.microsoft.com/azure/).
* BGP hakkında bilgi edinmek için [BGP’ye Genel Bakış](vpn-gateway-bgp-overview.md) ve [BGP’yi yapılandırma](vpn-gateway-bgp-resource-manager-ps.md) makalelerine bakın.

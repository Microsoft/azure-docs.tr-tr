---
title: "Bir Azure sanal ağını başka bir sanal ağa bağlama: PowerShell | Microsoft Docs"
description: "Bu makalede Azure Resource Manager ve PowerShell kullanarak sanal ağları birbirine bağlama konusu incelenmektedir."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a4cbc4cd1c48da1120c643892b19692ac583d4c3
ms.lasthandoff: 03/31/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-powershell"></a>PowerShell’i kullanarak sanal ağdan sanal ağa bağlantı yapılandırma
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Klasik - Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Klasik - Klasik Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
>

Bu makalede VPN Gateway kullanılarak Resource Manager dağıtım modelinde sanal ağlar arası bağlantı oluşturma işlemi adım adım açıklanmaktadır. Sanal ağlar aynı ya da farklı bölgelerde ve aynı ya da farklı aboneliklerde bulunuyor olabilirler.

![v2v diyagramı](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Sanal ağdan sanal ağa bağlantılar için dağıtım modelleri ve yöntemleri
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Aşağıdaki tabloda, sanal ağdan sanal ağa bağlantı yapılandırmaları için şu anda kullanılabilen dağıtım modelleri ve yöntemleri gösterilmektedir. Yapılandırma adımlarını içeren bir makale olduğunda, bu tablodan makaleye yönelik doğrudan bağlantı oluştururuz. 

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Sanal ağ eşlemesi**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Sanal Ağdan Sanal Ağa bağlantıları hakkında
Bir sanal ağı başka bir sanal ağa bağlamak (VNet'ten VNet'e), bir VNet'i şirket içi site konumuna bağlamakla aynıdır. Her iki bağlantı türü de Azure VPN ağ geçidini kullanarak IPsec/IKE ile güvenli bir tünel sunar. Bağlandığınız Sanal Ağlar farklı bölgelerde, ve farklı aboneliklerde bulunabilir. Hatta Sanal Ağdan Sanal Ağa iletişimini çok siteli yapılandırmalarla bile birleştirebilirsiniz. Bunun yapılması aşağıdaki diyagramda da görüldüğü gibi şirket içi ve şirket dışı bağlantıyla sanal ağ içi bağlantıyı birleştiren ağ topolojileri kurabilmenize olanak sağlar:

![Bağlantılar hakkında](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a>Sanal ağları neden bağlamalıyız?
Sanal ağları aşağıdaki sebeplerden dolayı bağlamak isteyebilirsiniz:

* **Çapraz bölge coğrafi artıklığı ve coğrafi-durum**
  
  * Kendi coğrafi çoğaltma veya eşitlemenizi, güvenli bağlantıyla İnternet’te uç noktalara gitmeden ayarlayabilirsiniz.
  * Azure Traffic Manager ve Load Balancer ile birçok Azure bölgesinde coğrafi yedeklilik imkanıyla yüksek oranda kullanılabilir iş yükü oluşturabilirsiniz. Buna önemli bir örnek olarak SQL Always On ile birden fazla Azure bölgesine yayılan Kullanılabilirlik Grupları’nı birlikte kurmak verilebilir.
* **Yalıtım veya yönetim sınır bölgesel çok katmanlı uygulamalar**
  
  * Yalıtım ve yönetim gereksinimlerinden dolayı aynı bölge içinde birbirlerine bağlı birden fazla sanal ağ ile çok katmanlı uygulamalar kurabilirsiniz.

### <a name="vnet-to-vnet-considerations"></a>Sanal Ağdan Sanal Ağa dikkat edilecek noktalar
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="which-set-of-steps-should-i-use"></a>Hangi adımları tamamlamalıyım? 
Bu makalede iki farklı adım kümesi görürsünüz. Bir adım kümesi [Aynı abonelikte bulunan sanal ağlar](#samesub), diğer adım kümesi ise [Farklı aboneliklerde bulunan sanal ağlar](#difsub) içindir. Kümeler arasındaki temel farklılık, tüm sanal ağ ve ağ geçidi kaynaklarını oluşturma ve yapılandırma işlemini aynı PowerShell oturumunda yapıp yapamayacağınızdadır.

Bu makaledeki adımlar her bölümün başında bildirilen değişkenleri kullanır. Zaten var olan Vnet'ler ile çalışıyorsanız değişkenleri kendi ortamınızdaki ayarları yansıtacak şekilde değiştirin. 

![Her iki bağlantı](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

## <a name="samesub"></a>Aynı abonelikte olan Vnet'ler bağlanma
![v2v diyagramı](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Başlamadan önce
Başlamadan önce Azure Resource Manager PowerShell cmdlet’lerini yüklemeniz gerekir. PowerShell cmdlet'lerini yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azureps-cmdlets-docs).

### <a name="Step1"></a>1. adım -, IP adres aralıklarını planlama
Aşağıdaki adımlarda kendi ağ geçidi alt ağları ve yapılandırmalarıyla birlikte iki sanal ağ oluşturulur. Daha sonra iki sanal ağ arasında bir VPN bağlantısı oluşturulur. Ağ yapılandırmanız için IP adres aralıklarını planlamanız önemlidir. Sanal ağ aralıklarınızın ya da yerel ağ aralıklarınızın hiçbir şekilde çakışmadığından emin olmalısınız.

Örneklerde aşağıdaki değerler kullanılmaktadır:

**Değerler TestVNet1 için:**

* VNet Name: TestVNet1
* Resource Group: TestRG1
* Location: East US
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* DNS Server: 8.8.8.8
* GatewayName: VNet1GW
* Public IP: VNet1GWIP
* VPNType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5
* ConnectionType: VNet2VNet

**Değerler TestVNet4 için:**

* VNet Name: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Resource Group: TestRG4
* Location: West US
* DNS Server: 8.8.8.8
* GatewayName: VNet4GW
* Public IP: VNet4GWIP
* VPNType: RouteBased
* Connection: VNet4toVNet1
* ConnectionType: VNet2VNet

### <a name="Step2"></a>Adım 2 - oluşturma ve TestVNet1 yapılandırma
1. Değişkenlerinizi bildirme
   
    Değişkenleri bildirerek başlayın. Bu örnekte bu alıştırmaya yönelik değerleri kullanan değişkenler bildirilmektedir. Çoğu durumda değerleri kendi değerlerinizle değiştirmeniz gerekir. Ancak, bu tür yapılandırmaları tanımaya başlamak için adımları gözden geçiriyorsanız bu değişkenleri kullanabilirsiniz. Gerekirse değişkenleri değiştirin, daha sonra kopyalayın ve PowerShell konsolunuza yapıştırın.
   
        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
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
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"
2. Aboneliğinize bağlanma
   
    Resource Manager cmdlet’lerini kullanmak için PowerShell moduna geçin. PowerShell konsolunuzu açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:
   
        Login-AzureRmAccount
   
    Hesapla ilişkili abonelikleri kontrol edin.
   
        Get-AzureRmSubscription 
   
    Kullanmak istediğiniz aboneliği belirtin.
   
        Select-AzureRmSubscription -SubscriptionName $Sub1
3. Yeni bir kaynak grubu oluşturma
   
        New-AzureRmResourceGroup -Name $RG1 -Location $Location1
4. TestVNet1 için alt ağ yapılandırmalarını oluşturma
   
    Bu örnekte TestVNet1 adlı bir sanal ağ ve üç alt ağ oluşturulmaktadır. Biri GatewaySubnet, biri FrontEnd, diğeri BackEnd’dir. Kendi değerlerinizi yerleştirirken ağ geçidi alt ağınızı özellikle GatewaySubnet olarak adlandırmanız önem taşır. Başka bir ad kullanırsanız ağ oluşturma işleminiz başarısız olur. 
   
    Aşağıdaki örnekte daha önce belirlediğiniz değişkenler kullanılmaktadır. Bu örnekte ağ geçidi alt ağı bir /27 kullanmaktadır. /29 kadar küçük bir ağ geçidi alt ağı oluşturmak mümkün olsa da en az /28 veya /27’yi seçerek daha fazla adres içeren büyük bir alt ağ oluşturmanızı öneririz. Bu, gelecekte isteyebileceğiniz ek yapılandırmaları da içerecek yeteri kadar adres sağlayacaktır. 
   
        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
5. TestVNet1 oluşturma
   
        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
6. Genel bir IP adresi talep etme
   
    Sanal ağınız için oluşturacağınız ağ geçidine ayrılacak genel IP adresi isteyin. AllocationMethod değerinin Dinamik olduğuna dikkat edin. Kullanmak istediğiniz IP adresini belirtemezsiniz. IP adresi, ağ geçidinize dinamik olarak ayrılır. 
   
        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic
7. Ağ geçidi yapılandırmasını oluşturma
   
    Ağ geçidi yapılandırması, kullanılacak alt ağı ve genel IP adresini tanımlar. Ağ geçidi yapılandırmanızı oluşturmak için örneği kullanın. 
   
        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1
8. TestVNet1 için ağ geçidi oluşturma
   
    Bu adımda TestVNet1’iniz için sanal ağ geçidi oluşturursunuz. Sanal Ağdan Sanal Ağa yapılandırmaları, RouteBased bir VPNType gerektirir. Bir ağ geçidini oluşturmak biraz zaman alabilir (tamamlanması 45 dakika ya da daha fazla sürer).
   
        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>3. Adım - TestVNet4’ü oluşturma ve yapılandırma
TestVNet1 yapılandırıldıktan sonra TestVNet4’ü oluşturun. Aşağıdaki adımları, verilen değerleri gerektiğinde kendi değerlerinizle değiştirerek takip edin. Bu adım aynı PowerShell oturumunda tamamlanabilir, çünkü bağlantı aynı abonelik içindedir.

1. Değişkenlerinizi bildirme
   
    Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun.
   
        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"
   
    Devam etmeden önce 1. Abonelik’e hala bağlı olduğunuzdan emin olun.
2. Yeni bir kaynak grubu oluşturma
   
        New-AzureRmResourceGroup -Name $RG4 -Location $Location4
3. TestVNet4 için alt ağ yapılandırmaları oluşturma
   
        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
4. TestVNet4’ü oluşturma
   
        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
5. Genel bir IP adresi talep etme
   
        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic
6. Ağ geçidi yapılandırmasını oluşturma
   
        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
7. TestVNet4 ağ geçidini oluşturma
   
        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>Adım 4 - Ağ geçitlerini bağlama
1. Her iki sanal ağ geçidini de alma
   
    Bu örnekte her iki ağ geçidi de aynı abonelikte bulunduğundan bu adım aynı PowerShell oturumunda tamamlanabilir.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
2. TestVNet1 - TestVNet4 bağlantısını oluşturma
   
    Bu adımda TestVNet1 - TestVNet4 arasında bağlantı oluşturursunuz. Örneklerde paylaşılan bir anahtar göreceksiniz. Paylaşılan anahtar için kendi değerlerinizi kullanabilirsiniz. Paylaşılan anahtarın her iki bağlantıyla da eşleşiyor olması önemlidir. Bir bağlantı oluşturmak çok zaman almaz.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
3. TestVNet4 - TestVNet1 bağlantısı oluşturma 
   
    Bu adım, bağlantıyı TestVNet4’ten TestVNet1’e yönüne kuracak olmanız dışında yukarıdaki adımla aynıdır. Paylaşılan anahtarların eşleştiğinden emin olun.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   
    Bağlantı birkaç dakika içerisinde kurulacaktır.
4. Bağlantınızı doğrulayın. [Bağlantınızı doğrulama](#verify) bölümüne bakın.

## <a name="difsub"></a>Farklı abonelikleri olan Vnet'ler bağlanma
![v2v diyagramı](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

Bu senaryoda TestVNet1 ve TestVNet5 bağlanır. TestVNet1 ve TestVNet5 farklı bir abonelikte bulunur. Bu yapılandırmanın adımları TestVNet1’i TestVNet5’e bağlamak için Sanal Ağdan Sanal Ağa bir bağlantı daha ekler. 

Burada farklı olan, yapılandırma adımlarının bir kısmının ikinci abonelik bağlamında farklı bir PowerShell oturumunda tamamlanması gerektiğidir. Bu durum özellikle iki aboneliğin farklı kuruluşlara ait olduğu durumlarda geçerlidir. 

Yönergeler yukarıda listelenen geçmiş adımların devamı niteliğindedir. TestVNet1'i ve TestVNet1 için VPN ağ geçidini oluşturup yapılandırmak için [1. Adımı](#Step1) ve [2. Adımı](#Step2) tamamlamalısınız. 1. Adım ve 2. Adımı tamamladıktan sonra 5. Adıma geçerek TestVNet5’i oluşturun.

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>5. Adım - Ek IP adresi aralıklarını doğrulama
Yeni sanal ağ olan TestVNet5’in IP adresi alanının kendi Sanal Ağ aralıklarınız veya yerel ağ geçidi aralıkları ile çakışmaması önemlidir. 

Bu örnekte sanal ağlar farklı kuruluşlara ait olabilir. Bu alıştırmada TestVNet5 için aşağıdaki değerleri kullanabilirsiniz:

**Değerler TestVNet5 için:**

* VNet Name: TestVNet5
* Resource Group: TestRG5
* Location: Japan East
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* DNS Server: 8.8.8.8
* GatewayName: VNet5GW
* Public IP: VNet5GWIP
* VPNType: RouteBased
* Connection: VNet5toVNet1
* ConnectionType: VNet2VNet

**TestVNet1 için ek değerler:**

* Connection: VNet1toVNet5

### <a name="step-6---create-and-configure-testvnet5"></a>6. Adım - TestVNet5’i oluşturma ve yapılandırma
Bu adım, yeni abonelik bağlamında tamamlanmalıdır. Bu kısım, aboneliğin sahibi olan farklı bir kuruluşun yöneticisi tarafından tamamlanabilir.

1. Değişkenlerinizi bildirme
   
    Değerleri, yapılandırma için kullanmak istediğiniz değerlerle değiştirdiğinizden emin olun.
   
        $Sub5 = "Replace_With_the_New_Subcription_Name"
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
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"
2. 5 Abonelik’e bağlanma
   
    PowerShell konsolunuzu açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:
   
        Login-AzureRmAccount
   
    Hesapla ilişkili abonelikleri kontrol edin.
   
        Get-AzureRmSubscription 
   
    Kullanmak istediğiniz aboneliği belirtin.
   
        Select-AzureRmSubscription -SubscriptionName $Sub5
3. Yeni bir kaynak grubu oluşturma
   
        New-AzureRmResourceGroup -Name $RG5 -Location $Location5
4. TestVNet4 için alt ağ yapılandırmaları oluşturma
   
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
5. TestVNet5’i oluşturma
   
        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
6. Genel bir IP adresi talep etme
   
        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic
7. Ağ geçidi yapılandırmasını oluşturma
   
        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
8. TestVNet5 ağ geçidini oluşturma
   
        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>7 Adım - Ağ geçitlerini bağlama
Bu örnekte ağ geçitleri farklı aboneliklerde olduğundan bu adımı [1. Abonelik] ve [5. Abonelik] olarak iki PowerShell oturumuna ayıracağız.

1. **[1 Abonelik ]** 1 Abonelik için sanal ağ geçidini alma
   
    1 Abonelikle oturum açıp bağlandığınızdan emin olun.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   
    Aşağıdaki öğelerin çıktısını kopyalayın ve 5. Aboneliğin yöneticisine e-posta veya başka bir yöntemle gönderin. 
   
        $vnet1gw.Name
        $vnet1gw.Id
   
    Bu iki öğenin değerleri aşağıdaki çıktı örneği ile benzer değerlere sahip olmalıdır:
   
        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
2. **[5 Abonelik]** 5 Abonelik için sanal ağ geçidini edinme
   
    5 Abonelikle oturum açıp bağlandığınızdan emin olun.
   
        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   
    Aşağıdaki öğelerin çıktısını kopyalayın ve 1. Abonelik’in yöneticisine e-posta veya başka bir yöntemle gönderin
   
        $vnet5gw.Name
        $vnet5gw.Id
   
    Bu iki öğenin değerleri aşağıdaki çıktı örneği ile benzer değerlere sahip olmalıdır:
   
        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
3. **[1 Abonelik]** TestVNet1 - TestVNet5 bağlantısını oluşturma
   
    Bu adımda TestVNet1 - TestVNet5 arasında bağlantı oluşturursunuz. Burada farklı olan, $vnet5gw’nun farklı bir abonelikte bulunmasından dolayı doğrudan alınamıyor olmasıdır. Yukarıdaki adımlarda belirtilen 1. Abonelik’ten iletişim sağlanan yeni bir PowerShell nesnesi oluşturmanız gerekecektir. Aşağıdaki örneği kullanın. Ad, Kimlik ve paylaşılan anahtar değerlerini kendi değerlerinizle değiştirin. Paylaşılan anahtarın her iki bağlantıyla da eşleşiyor olması önemlidir. Bir bağlantı oluşturmak çok zaman almaz.
   
    1 Abonelik’e bağlandığınızdan emin olun. 
   
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
4. **[5. Abonelik]**TestVNet5 - TestVNet1 bağlantısını oluşturma
   
    Bu adım, bağlantıyı TestVNet5’ten TestVNet1’e doğru kuracak olmanızın dışında yukarıdaki adımla aynıdır. 1 Abonelikten elde edilen değerleri temel alan bir PowerShell nesnesi oluşturma işlemi burada da geçerlidir. Bu adımda, paylaşılan anahtarların eşleştiğinden emin olun.
   
    5 Aboneliğe bağlandığınızdan emin olun.
   
        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>Nasıl bir bağlantıyı doğrulamak için
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Virtual Machines belgeleri](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* BGP hakkında bilgi edinmek için [BGP’ye Genel Bakış](vpn-gateway-bgp-overview.md) ve [BGP’yi yapılandırma](vpn-gateway-bgp-resource-manager-ps.md) makalelerine bakın. 



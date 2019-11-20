---
title: Azure PowerShell kullanarak hibrit bir ağda Azure Güvenlik Duvarı'nı dağıtma ve yapılandırma
description: Bu makalede, Azure Güvenlik Duvarı 'Nı Azure PowerShell kullanarak dağıtmayı ve yapılandırmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 10/18/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: a5a008a795b88dbcb72ed9fba869e5251fd93567
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163525"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Azure PowerShell kullanarak hibrit bir ağda Azure Güvenlik Duvarı'nı dağıtma ve yapılandırma

Karma ağ oluşturmak için şirket içi ağınızı bir Azure sanal ağına bağladığınızda, Azure ağ kaynaklarınıza erişimi denetleme özelliği, genel bir güvenlik planının önemli bir parçasıdır.

Azure Güvenlik Duvarı 'nı, izin verilen ve reddedilen ağ trafiğini tanımlayan kuralları kullanarak bir karma ağdaki ağ erişimini denetlemek için kullanabilirsiniz.

Bu makalede üç sanal ağ oluşturursunuz:

- **VNET-hub** -güvenlik duvarı bu sanal ağda.
- **VNET-kol** -bağlı bileşen sanal ağı, Azure üzerinde bulunan iş yükünü temsil eder.
- **VNET-Onpred** -şirket içi sanal ağ, bir şirket içi ağı temsil eder. Gerçek bir dağıtımda, bir VPN veya ExpressRoute bağlantısı ile bağlanabilir. Kolaylık olması için bu makalede bir VPN Ağ Geçidi bağlantısı ve bir şirket içi ağı temsil etmek için Azure 'da bulunan bir sanal ağ kullanılır.

![Hibrit ağda güvenlik duvarı](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Değişkenleri tanımlama
> * Güvenlik Duvarı hub 'ı sanal ağını oluşturma
> * Bağlı bileşen sanal ağını oluşturma
> * Şirket içi sanal ağı oluşturma
> * Güvenlik duvarını yapılandırma ve dağıtma
> * VPN ağ geçitlerini oluşturma ve bağlama
> * Hub ve bağlı bileşen sanal ağlarını eşler
> * Yolları oluşturma
> * Sanal makineleri oluşturma
> * Güvenlik duvarını test etme

Bu öğreticiyi tamamlayabilmeniz için Azure portal kullanmak istiyorsanız, bkz. [öğretici: Azure Güvenlik duvarını Azure Portal kullanarak karma ağda dağıtma ve yapılandırma](tutorial-hybrid-portal.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makale, PowerShell 'i yerel olarak çalıştırmanızı gerektirir. Azure PowerShell modülünün yüklü olması gerekir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](https://docs.microsoft.com/powershell/azure/install-Az-ps). PowerShell sürümünü doğruladıktan sonra, Azure ile bağlantı oluşturmak için `Login-AzAccount` komutunu çalıştırın.

Bu senaryonun doğru çalışması için üç önemli gereksinimi vardır:

- Alt ağda, varsayılan ağ geçidi olarak Azure Güvenlik Duvarı IP adresini işaret eden bir Kullanıcı tanımlı yol (UDR). Bu yol tablosunda BGP yol yaymanın **Devre Dışı** olması gerekir.
- Hub ağ geçidi alt ağındaki UDR, bağlı olan ağların sonraki atlaması olarak güvenlik duvarı IP adresini göstermelidir.

   Azure Güvenlik Duvarı alt ağında BGP 'deki yolları öğrendiğinden dolayı UDR gerekmez.
- VNet-Hub'ı VNet-Spoke'a eşlerken **AllowGatewayTransit** ayarladığınızdan ve VNet-Spoke'u VNet-Hub'a eşlerken de **UseRemoteGateways** ayarladığınızdan emin olun.

Bu yolların nasıl oluşturulduğunu görmek için bu makaledeki [yolları oluşturma](#create-the-routes) bölümüne bakın.

>[!NOTE]
>Azure Güvenlik duvarının doğrudan Internet bağlantısı olmalıdır. AzureFirewallSubnet, BGP aracılığıyla şirket içi ağınıza varsayılan bir yol öğrenirse, doğrudan Internet bağlantısını sürdürmek için **Nexthoptype** değeri **Internet** olarak ayarlanmış bir 0.0.0.0/0 UDR ile geçersiz kılmanız gerekir.
>
>Azure Güvenlik Duvarı şu anda zorlamalı tüneli desteklemiyor. Yapılandırmanız şirket içi bir ağa Zorlamalı tünel gerektiriyorsa ve Internet hedefleriniz için hedef IP öneklerini belirleyebiliyorsanız, bu aralıkları şirket içi ağ ile bir Kullanıcı tanımlı yol aracılığıyla sonraki atlama olarak yapılandırabilirsiniz. AzureFirewallSubnet. Ya da bu yolları tanımlamak için BGP kullanabilirsiniz.

>[!NOTE]
>Doğrudan eşlenmiş sanal ağlar arasındaki trafik, bir UDR varsayılan ağ geçidi olarak Azure Güvenlik Duvarı 'na işaret ediyorsa doğrudan yönlendirilir. Alt ağ trafiğine Bu senaryodaki güvenlik duvarının alt ağını göndermek için, her iki alt ağda de bir UDR 'nin hedef alt ağ önekini açıkça içermesi gerekir.

İlgili Azure PowerShell başvuru belgelerini gözden geçirmek için, bkz. [Azure PowerShell Başvurusu](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="declare-the-variables"></a>Değişkenleri tanımlama

Aşağıdaki örnek, bu makale için değerleri kullanarak değişkenleri bildirir. Bazı durumlarda, aboneliğinizde çalışmak için bazı değerleri kendi değerlerinizle değiştirmeniz gerekebilir. Gerekirse değişkenleri değiştirin, daha sonra kopyalayın ve PowerShell konsolunuza yapıştırın.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>Güvenlik Duvarı hub 'ı sanal ağını oluşturma

İlk olarak, bu makalenin kaynaklarını içerecek kaynak grubunu oluşturun:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Sanal ağa dahil edilecek alt ağları tanımlayın:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Şimdi, güvenlik duvarı hub 'ı sanal ağını oluşturun:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Sanal ağınız için oluşturacağınız VPN ağ geçidine ayrılacak genel IP adresi isteyin. *AllocationMethod* değerinin **Dynamic** olduğuna dikkat edin. Kullanmak istediğiniz IP adresini belirtemezsiniz. IP adresi, VPN ağ geçidinize dinamik olarak ayrılır.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Bağlı bileşen sanal ağını oluşturma

Bağlı olan sanal ağına dahil edilecek alt ağları tanımlayın:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Bağlı bileşen sanal ağını oluşturun:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Şirket içi sanal ağı oluşturma

Sanal ağa dahil edilecek alt ağları tanımlayın:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Şimdi, şirket içi sanal ağı oluşturun:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Sanal ağ için oluşturacağınız ağ geçidine ayrılacak genel IP adresi isteyin. *AllocationMethod* değerinin **Dynamic** olduğuna dikkat edin. Kullanmak istediğiniz IP adresini belirtemezsiniz. IP adresi, ağ geçidinize dinamik olarak ayrılır.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Güvenlik duvarını yapılandırma ve dağıtma

Şimdi güvenlik duvarını hub sanal ağına dağıtın.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Ağ kurallarını yapılandırma

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>VPN ağ geçitlerini oluşturma ve bağlama

Hub ve şirket içi sanal ağlar VPN ağ geçitleri aracılığıyla bağlanır.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Hub sanal ağı için bir VPN ağ geçidi oluşturma

VPN ağ geçidi yapılandırmasını oluşturun. VPN ağ geçidi yapılandırması, kullanılacak alt ağı ve genel IP adresini tanımlar.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Şimdi hub sanal ağı için VPN ağ geçidini oluşturun. Ağdan ağa yapılandırma, RouteBased bir VpnType gerektirir. VPN ağ geçidinin oluşturulması, seçili VPN ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Şirket içi sanal ağ için bir VPN ağ geçidi oluşturma

VPN ağ geçidi yapılandırmasını oluşturun. VPN ağ geçidi yapılandırması, kullanılacak alt ağı ve genel IP adresini tanımlar.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Şimdi, şirket içi sanal ağ için VPN ağ geçidini oluşturun. Ağdan ağa yapılandırma, RouteBased bir VpnType gerektirir. VPN ağ geçidinin oluşturulması, seçili VPN ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>VPN bağlantılarını oluşturma

Artık hub ve şirket içi ağ geçitleri arasında VPN bağlantıları oluşturabilirsiniz

#### <a name="get-the-vpn-gateways"></a>VPN ağ geçitlerini alma

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Bağlantıları oluşturma

Bu adımda, hub sanal ağından şirket içi sanal ağa bağlantı oluşturursunuz. Örneklerde paylaşılan bir anahtar göreceksiniz. Paylaşılan anahtar için kendi değerlerinizi kullanabilirsiniz. Paylaşılan anahtarın her iki bağlantıyla da eşleşiyor olması önemlidir. Bir bağlantı oluşturmak çok zaman almaz.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Şirket içinden hub 'a sanal ağ bağlantısını oluşturun. Bu adım, VNet-Onpree ile VNet hub 'ına bağlantı oluşturmanız dışında öncekiyle benzerdir. Paylaşılan anahtarların eşleştiğinden emin olun. Bağlantı birkaç dakika içerisinde kurulacaktır.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Bağlantıyı doğrulama

*Get-AzVirtualNetworkGatewayConnection* cmdlet 'ini kullanarak başarılı bir bağlantıyı, *hata ayıklama*ile veya olmayan bir şekilde doğrulayabilirsiniz. Aşağıdaki cmdlet örneğini kullanın ve değerleri, kendi değerlerinizle eşleşecek şekilde yapılandırın. İstendiğinde, **Tümü**'nü çalıştırmak için **A** seçin. Örnekte bulunan *-Name*, test etmek istediğiniz bağlantının adıdır.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Cmdlet tamamlandıktan sonra değerleri görüntüleyin. Aşağıdaki örnekte, bağlantı durumu *Bağlandı* olarak gösterilir; ayrıca giriş ve çıkış baytlarını görebilirsiniz.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Hub ve bağlı bileşen sanal ağlarını eşler

Artık hub ve bağlı bileşen sanal ağlarını eşler.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Yolları oluşturma

Şimdi birkaç yol oluşturun:

- Güvenlik duvarı IP adresi üzerinden hub ağ geçidi alt ağından uç alt ağına giden bir yol
- Güvenlik duvarı IP adresi üzerinden uç alt ağından gelen varsayılan yol

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Artık bağlı olan iş yükünü ve şirket içi sanal makineleri oluşturun ve uygun alt ağlara yerleştirin.

### <a name="create-the-workload-virtual-machine"></a>İş yükü sanal makinesi oluşturma

Genel IP adresi olmadan IIS çalıştıran ve ' de ping yapılmasına izin veren sanal ağda sanal makine oluşturun.
İstendiğinde, sanal makine için bir kullanıcı adı ve parola yazın.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Şirket içi sanal makine oluşturma

Bu, Uzak Masaüstü kullanarak genel IP adresine bağlanmak için kullandığınız basit bir sanal makinedir. Buradan, daha sonra güvenlik duvarı aracılığıyla şirket içi sunucuya bağlanırsınız. İstendiğinde, sanal makine için bir kullanıcı adı ve parola yazın.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Güvenlik duvarını test etme

İlk olarak, **VM-ışınsal-01** sanal makinesi IÇIN özel IP adresini alın ve ardından aklınızda yapın.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Azure portalından, **VM-Onprem** sanal makinesine bağlanın.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
**VM-Onpred**üzerinde bir Web tarayıcısı açın ve http://\<VM-bağlı-01 özel IP \> gidin.

Internet Information Services varsayılan sayfasını görmelisiniz.

**VM-Onprem** sanal makinesinden özel IP adresinde **VM-spoke-01**'e uzak masaüstü açın.

Bağlantınız başarılı olmalı ve seçtiğiniz kullanıcı adıyla parolayı kullanarak oturum açabilmelisiniz.

Artık Güvenlik Duvarı kurallarının çalıştığını doğruladınız:

<!---- You can ping the server on the spoke VNet.--->
- Web sunucusuna bağlı bileşen sanal ağı üzerinde gezinebilirsiniz.
- RDP kullanarak, bağlı olan sanal ağdaki sunucuya bağlanabilirsiniz.

Ardından, güvenlik duvarı kurallarının beklendiği gibi çalıştığını doğrulamak için güvenlik duvarı ağ kuralı koleksiyonu eylemini **Reddet** olarak değiştirin. Kural koleksiyonu eylemini **Reddet** olarak değiştirmek için aşağıdaki betiği çalıştırın.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Şimdi testleri yeniden çalıştırın. Bu kez tümü başarısız olmalıdır. Değişen kuralları test etmeden önce var olan tüm uzak masaüstlerini kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Güvenlik duvarı kaynaklarını bir sonraki öğretici için tutabilirsiniz veya artık gerekli değilse **FW-Hybrid-Test** kaynak grubunu silerek güvenlik duvarıyla ilgili tüm kaynakları silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi Azure Güvenlik Duvarı günlüklerini izleyebilirsiniz.

[Öğretici: Azure Güvenlik Duvarı günlüklerini izleme](./tutorial-diagnostics.md)

---
title: "Öğretici: Azure PowerShell kullanarak sanal hub 'ınızı güvenli hale getirme"
description: Bu makalede, hub 'da Azure Güvenlik Duvarı 'Nın etkin olduğu tek bir bölgede Azure sanal WAN oluşturma açıklanır.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: 5ef39118d16d47aeb46294658adf616515547ea6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729533"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Öğretici: Azure PowerShell kullanarak sanal hub 'ınızı güvenli hale getirme

Bu öğreticide, bir bölgede bir sanal hub ile sanal bir WAN örneği oluşturursunuz ve bağlantıyı güvenli hale getirmek için sanal hub 'da bir Azure Güvenlik Duvarı dağıtırsınız. Bu örnekte, sanal ağlar arasında güvenli bağlantı gösterilmektedir. Sanal ağlar ile siteden siteye, Noktadan siteye veya ExpressRoute dalları arasındaki trafik de sanal güvenli hub tarafından desteklenir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal WAN 'ı dağıtma
> * Azure Güvenlik duvarını dağıtma ve özel yönlendirmeyi yapılandırma
> * Bağlantıyı test etme

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- PowerShell 7

   Bu öğretici, Azure PowerShell PowerShell 7 ' de yerel olarak çalıştırmanızı gerektirir. PowerShell 7 ' yi yüklemek için bkz. [Windows powershell 5,1 ' den PowerShell 7 ' ye geçme](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7&preserve-view=true).
- Az. Network Version 3.2.0

    Az. Network Version 3.4.0 veya daha yeni bir sürüme sahipseniz, bu öğreticideki bazı komutları kullanmak için düşürme yapmanız gerekir. Komutunu kullanarak az. Network modülünüzün sürümünü kontrol edebilirsiniz `Get-InstalledModule -Name Az.Network` . Az. Network modülünü kaldırmak için öğesini çalıştırın `Uninstall-Module -name az.network` . Az. Network 3.2.0 modülünü yüklemek için öğesini çalıştırın `Install-Module az.network -RequiredVersion 3.2.0 -force` .

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>İlk sanal WAN dağıtımı

İlk adım olarak, bazı değişkenler ayarlamanız ve kaynak grubunu, sanal WAN örneğini ve sanal hub 'ı oluşturmanız gerekir:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

İki sanal ağ oluşturun ve bunları hub 'a bağlı bileşen olarak bağlayın:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

Bu noktada, her türlü bağlantı sağlayan tam işlevli bir sanal WAN vardır. Güvenliği artırmak için, her bir sanal hub 'a bir Azure Güvenlik Duvarı dağıtmanız gerekir. Güvenlik Duvarı Ilkeleri, sanal WAN Azure Güvenlik Duvarı örneğini verimli bir şekilde yönetmek için kullanılabilir. Bu nedenle, bu örnekte de bir güvenlik duvarı ilkesi oluşturulur:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Azure güvenlik duvarından Azure Izleyicisine günlük kaydetmenin etkinleştirilmesi isteğe bağlıdır, ancak bu örnekte trafiğin güvenlik duvarından geçme olduğunu kanıtlamak için güvenlik duvarı günlükleri kullanılır:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Azure Güvenlik duvarını dağıtma ve özel yönlendirmeyi yapılandırma

Artık hub 'da bir Azure Güvenlik duvarınız var, ancak sanal WAN 'ın trafiği sanal ağlardan ve dallardan güvenlik duvarı üzerinden göndermesi için yönlendirmeyi değiştirmeniz gerekir. Bunu iki adımda yapabilirsiniz:

1. Yol tablosuna yayılacak tüm sanal ağ bağlantılarını (ve varsa dal bağlantılarını) yapılandırın `None` . Bu yapılandırmanın etkisi, diğer sanal ağların ve dalların ön eklerini öğrenmemesi ve bu nedenle onlara ulaşmak için bir yönlendirmeye sahip olmayacaktır.
1. Artık `Default` , tüm trafiğin Azure Güvenlik Duvarı 'na gönderilmesi için yol tablosuna (tüm sanal ağların ve dalların varsayılan olarak ilişkilendirildiği) statik yollar ekleyebilirsiniz.

> [!NOTE]
> Bu, Azure Portal ile Azure Güvenlik duvarı yöneticisi arasındaki bağlantıyı güvenli hale getirirken dağıtılan yapılandırmadır

İlk adımla başlayarak, sanal ağ bağlantılarınızı yol tablosuna yaymaya yönelik yapılandırmak için `None` :

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

Artık, statik yolları yönlendirme tablosuna eklemek için ikinci adımla devam edebilirsiniz `Default` . Bu örnekte, Azure Güvenlik Duvarı Yöneticisi 'nin bir sanal WAN 'da bağlantıyı güvenli hale getirirken oluşturabileceği varsayılan yapılandırmayı uygularsınız, ancak statik yoldaki ön eklerin listesini özel gereksinimlerinize uyacak şekilde değiştirebilirsiniz:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Bağlantıyı test etme

Artık tamamen işletimsel bir güvenli hub 'ınız var. Bağlantıyı test etmek için, hub 'a bağlı her bir bağlı ağ sanal ağında bir sanal makineye ihtiyacınız vardır:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

Güvenlik duvarı ilkesinde varsayılan yapılandırma her şeyi düşürülemiyor. Bu nedenle bazı kuralları yapılandırmanız gerekir. Test sanal makinelerinin güvenlik duvarının genel IP adresi üzerinden erişilebilir olması için DNAT kuralları ile başlayın:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Artık bazı örnek kuralları yapılandırabilirsiniz. SSH trafiğine izin veren bir ağ kuralı ve ayrıca tam etki alanı adına Internet erişimine izin veren bir uygulama kuralı tanımlayın `ifconfig.co` . Bu URL, HTTP isteğinde gördüğü kaynak IP adresini döndürür:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

Herhangi bir trafiği gerçekten göndermeden önce, sanal makinelerin etkin yollarını inceleyebilirsiniz. Sanal WAN (Plus RFC1918) tarafından öğrenilen ön ekleri içermelidir `0.0.0.0/0` , ancak diğer bağlı bileşen öneki değil:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Şimdi bir sanal makineden diğerine trafik oluşturun ve Azure Güvenlik duvarında bırakılmış olduğunu doğrulayın. Aşağıdaki SSH komutlarında, parmak izleri sanal makinelerini kabul etmeniz ve sanal makineleri oluştururken tanımladığınız parolayı sağlamanız gerekir. Bu örnekte, spoke1 ve spoke2 ' de sanal makineden beş ıCMP yankı isteği paketi ve Linux yardımcı programını kullanarak 22 numaralı bağlantı noktasında `nc` ( `-vz` yalnızca bir bağlantı isteği göndermesi ve sonucu gösteren) bir TCP bağlantı girişimi göndereceğiz. Daha önce yapılandırdığınız ağ kuralının izin verdiği için ping başarısız oldu ve bağlantı noktası 22 ' de TCP bağlantı denemesi ' ni görmeniz gerekir:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

Internet trafiğini de doğrulayabilirsiniz. `curl`Güvenlik duvarı ilkesinde () izin VERILEN FQDN 'ye yardımcı olan http istekleri `ifconfig.co` çalışır, ancak başka bir hedefe yönelik http isteklerinin başarısız olması gerekir (Bu örnekte, ile test edersiniz `bing.com` ):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

Paketleri doğrulamak için en kolay yol güvenlik duvarı tarafından bırakılır ve Günlükler denetlenir. Azure Güvenlik duvarını Günlükler gönderecek şekilde yapılandırdığınıza göre, Azure Izleyici 'deki ilgili günlükleri almak için kusto sorgu dilini kullanabilirsiniz:

> [!NOTE]
> Günlüklerin Azure Izleyici 'ye gönderilmesi 1 dakika sürebilir

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

Önceki komutta farklı girdiler görmeniz gerekir:

* SSH bağlantınız Dnated olarak açıldı
* Bağlı bileşen (10.1.1.4 ve 10.1.2.4) içindeki VM 'Ler arasında ıCMP paketleri bırakıldı
* Bağlı uçlardaki VM 'Ler arasında SSH bağlantıları izin verildi

Yukarıdaki komutla oluşturulan örnek bir çıktı aşağıda verilmiştir:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Uygulama kuralları için günlükleri (izin verilen ve reddedilen HTTP bağlantılarını açıklayan) görmek veya günlüklerin görüntülenme şeklini değiştirmek isterseniz, diğer KQL sorgularını deneyebilirsiniz. Azure [güvenlik duvarı Için Azure izleyici günlüklerinde](../firewall/firewall-workbook.md)bazı örnekler bulabilirsiniz.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Test ortamını silmek için, kaynak grubunu içerilen tüm nesnelerle kaldırabilirsiniz:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Güvenilen güvenlik iş ortakları hakkında bilgi edinin](trusted-security-partners.md)
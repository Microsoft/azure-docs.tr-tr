---
title: 'Hızlı başlangıç: ortak yük dengeleyici oluşturma-Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Bu hızlı başlangıçta Azure PowerShell kullanarak yük dengeleyici oluşturma gösterilmektedir
services: load-balancer
documentationcenter: na
author: asudbring
ms.author: allensu
manager: KumudD
ms.date: 11/22/2020
ms.assetid: ''
ms.topic: quickstart
ms.service: load-balancer
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: 0ddaf0eede59053cd8022fef24d37a37c6d7db5a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529575"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak VM 'Lerin yükünü dengelemek için ortak yük dengeleyici oluşturma

Ortak yük dengeleyici ve üç sanal makine oluşturmak için Azure PowerShell kullanarak Azure Load Balancer kullanmaya başlayın.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell yerel olarak veya Azure Cloud Shell yüklendi

PowerShell'i yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure PowerShell modülü 5.4.1 veya sonraki bir sürümünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için öğesini de çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun:

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePubLBQS-rg' -Location 'eastus'

```
---

# <a name="standard-sku"></a>[**Standart SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standart SKU yük dengeleyici, üretim iş yükleri için önerilir. SKU 'lar hakkında daha fazla bilgi için bkz. **[Azure Load Balancer SKU 'lar](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Hızlı başlangıç için oluşturulan Standart yük dengeleyici kaynakları." border="false":::

## <a name="create-a-public-ip-address---standard"></a>Genel IP adresi oluşturma-standart

Bir genel IP adresi oluşturmak için [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) komutunu kullanın.

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1,2,3
}
New-AzPublicIpAddress @publicip

```

Bölge 1 ' de genel bir IP adresi oluşturmak için aşağıdaki komutu kullanın:

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1
}
New-AzPublicIpAddress @publicip

```

## <a name="create-standard-load-balancer"></a>Standart yük dengeleyici oluştur

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:

* Ön uç IP havuzu için [New-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) ile bir ön uç IP 'si oluşturun. Bu IP, yük dengeleyicide gelen trafiği alır

* Yük dengeleyicinin ön uçınızdan gönderilen trafik için [New-Azloadbalancerbackendadddresspoolconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) ile arka uç adres havuzu oluşturun. Bu havuz, arka uç sanal makinelerinizin dağıtıldığı yerdir.

* [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) ile arka uç sanal makine örneklerinin sistem durumunu belirleyen bir sistem durumu araştırması oluşturun.

* Trafiğin VM 'lere nasıl dağıtıldığını tanımlayan [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) ile bir yük dengeleyici kuralı oluşturun.

* [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)ile bir genel yük dengeleyici oluşturun.


```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset -DisableOutboundSNAT

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---standard"></a>Sanal ağ yapılandırma-standart

VM 'Leri dağıtmadan ve yük dengeleyicinizi test etmeden önce destekleyici sanal ağ kaynaklarını oluşturun.

Arka uç sanal makineleri için bir sanal ağ oluşturun.

Sanal ağınıza gelen bağlantıları tanımlamak için bir ağ güvenlik grubu oluşturun.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Sanal ağ, ağ güvenlik grubu ve savunma Konağı oluşturma

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun.

* [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)ile bir ağ güvenlik grubu kuralı oluşturun.

* [Yeni aztasyon](/powershell/module/az.network/new-azbastion)Ile bir Azure savunma ana bilgisayarı oluşturun.

* [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)ile bir ağ güvenlik grubu oluşturun.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---standard"></a>Sanal makine oluşturma-standart

Bu bölümde, yük dengeleyicinin arka uç havuzu için üç sanal makine oluşturacaksınız.

* [New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface)ile üç ağ arabirimi oluşturun.

* VM 'Ler için [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)ile bir Yönetici Kullanıcı adı ve parola ayarlayın.

* Sanal makineleri şu şekilde oluşturun:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-Azvmsourceımage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## For loop with variable to create virtual machines for load balancer backend pool. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
    Zone = "$i"
}
New-AzVM @vm -AsJob
}

```

Sanal makinelerin ve savunma konağının dağıtımları, PowerShell işleri olarak gönderilir. İşlerin durumunu görüntülemek için, [Get-Job](/powershell/module/microsoft.powershell.core/get-job)kullanın:

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-outbound-rule-configuration"></a>Giden kuralı yapılandırması oluştur
Yük dengeleyici giden kuralları arka uç havuzundaki VM 'Ler için giden kaynak ağ adresi çevirisini (SNAT) yapılandırır. 

Giden bağlantılar hakkında daha fazla bilgi için bkz. [Azure 'Da giden bağlantılar](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address"></a>Giden genel IP adresi oluştur

[New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) ' i kullanarak **Mypublicıpoıb utbağlanmadı** adlı standart bölge yedekli genel IP adresini oluşturun.

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1,2,3
}
New-AzPublicIpAddress @publicipout

```

Bölge 1 ' de genel bir IP adresi oluşturmak için aşağıdaki komutu kullanın:

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1
}
New-AzPublicIpAddress @publicipout

```

### <a name="create-outbound-configuration"></a>Giden yapılandırma oluştur

* [Add-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig)ile yeni bir ön uç IP yapılandırması oluşturun.

* [Add-Azloadbalancerbackendadddresspoolconfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig)ile yeni bir giden arka uç adres havuzu oluşturun. 

* [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer)ile yük dengeleyicisine havuzu ve ön uç IP adresini uygulayın.
*  [Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig)ile giden arka uç havuzu için yeni bir giden kuralı oluşturun. 

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$pubip = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$publicIp = Get-AzPublicIpAddress @pubip

## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

## Create the frontend configuration ##
$fe = @{
    Name = 'myFrontEndOutbound'
    PublicIPAddress = $publicIP
}
$lb | Add-AzLoadBalancerFrontendIPConfig @fe | Set-AzLoadBalancer

## Create the outbound backend address pool ##
$be = @{
    Name = 'myBackEndPoolOutbound'
}
$lb | Add-AzLoadBalancerBackendAddressPoolConfig @be | Set-AzLoadBalancer

## Apply the outbound rule configuration to the load balancer. ##
$rule = @{
    Name = 'myOutboundRule'
    AllocatedOutboundPort = '10000'
    Protocol = 'All'
    IdleTimeoutInMinutes = '15'
    FrontendIPConfiguration = $lb.FrontendIpConfigurations[1]
    BackendAddressPool = $lb.BackendAddressPools[1]
}
$lb | Add-AzLoadBalancerOutBoundRuleConfig @rule | Set-AzLoadBalancer

```

### <a name="add-virtual-machines-to-outbound-pool"></a>Sanal makineleri giden havuzuna Ekle

Sanal makine ağ arabirimlerini [Add-Aznetworkınterfaceipconfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)ile yük dengeleyicinin giden havuzuna ekleyin:

```azurepowershell-interactive
## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

# For loop with variable to add virtual machines to backend outbound pool. ##
for ($i=1; $i -le 3; $i++)
{
$nic = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = "myNicVM$i"
}
$nicvm = Get-AzNetworkInterface @nic

## Apply the backend to the network interface ##
$be = @{
    Name = 'ipconfig1'
    LoadBalancerBackendAddressPoolId = $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id
}
$nicvm | Set-AzNetworkInterfaceIpConfig @be | Set-AzNetworkInterface
}

```

# <a name="basic-sku"></a>[**Temel SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Standart SKU yük dengeleyici, üretim iş yükleri için önerilir. SKU 'lar hakkında daha fazla bilgi için bkz. **[Azure Load Balancer SKU 'lar](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Hızlı başlangıçta oluşturulan temel yük dengeleyici kaynakları." border="false":::

## <a name="create-a-public-ip-address---basic"></a>Genel IP adresi oluşturma-temel

Bir genel IP adresi oluşturmak için [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) komutunu kullanın.

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Basic'
    AllocationMethod = 'static'
}
New-AzPublicIpAddress @publicip

```

## <a name="create-basic-load-balancer"></a>Temel yük dengeleyici oluşturma

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:

* Ön uç IP havuzu için [New-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) ile bir ön uç IP 'si oluşturun. Bu IP, yük dengeleyicide gelen trafiği alır

* Yük dengeleyicinin ön uçınızdan gönderilen trafik için [New-Azloadbalancerbackendadddresspoolconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) ile arka uç adres havuzu oluşturun. Bu havuz, arka uç sanal makinelerinizin dağıtıldığı yerdir.

* [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) ile arka uç sanal makine örneklerinin sistem durumunu belirleyen bir sistem durumu araştırması oluşturun.

* Trafiğin VM 'lere nasıl dağıtıldığını tanımlayan [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) ile bir yük dengeleyici kuralı oluşturun.

* [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)ile bir genel yük dengeleyici oluşturun.

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Basic'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---basic"></a>Sanal ağı yapılandırma-temel

VM 'Leri dağıtmadan ve yük dengeleyicinizi test etmeden önce destekleyici sanal ağ kaynaklarını oluşturun.

Arka uç sanal makineleri için bir sanal ağ oluşturun.

Sanal ağınıza gelen bağlantıları tanımlamak için bir ağ güvenlik grubu oluşturun.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Sanal ağ, ağ güvenlik grubu ve savunma Konağı oluşturma

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun.

* [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)ile bir ağ güvenlik grubu kuralı oluşturun.

* [Yeni aztasyon](/powershell/module/az.network/new-azbastion)Ile bir Azure savunma ana bilgisayarı oluşturun.

* [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)ile bir ağ güvenlik grubu oluşturun.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---basic"></a>Sanal makineler oluşturma-temel

Bu bölümde, yük dengeleyicinin arka uç havuzu için sanal makineler oluşturacaksınız.

* [New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface)ile üç ağ arabirimi oluşturun.

* VM 'Ler için [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)ile bir Yönetici Kullanıcı adı ve parola ayarlayın.

* Sanal makineler için bir kullanılabilirlik kümesi oluşturmak üzere [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) kullanın.

* Sanal makineleri şu şekilde oluşturun:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-Azvmsourceımage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## Create availability set for the virtual machines. ##
$set = @{
    Name = 'myAvSet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Aligned'
    PlatformFaultDomainCount = '2'
    PlatformUpdateDomainCount =  '2'
}
$avs = New-AzAvailabilitySet @set

## For loop with variable to create virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'
    AvailabilitySetId = $avs.Id   
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm -AsJob
}

```

Sanal makinelerin ve savunma konağının dağıtımları, PowerShell işleri olarak gönderilir. İşlerin durumunu görüntülemek için, [Get-Job](/powershell/module/microsoft.powershell.core/get-job)kullanın:

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

---

## <a name="install-iis"></a>IIS yükleme

Özel Betik uzantısını yüklemek için [set-Azvmexgerkomutunu](/powershell/module/az.compute/set-azvmextension) kullanın. 

Uzantı, IIS web sunucusunu yüklemek için `PowerShell Add-WindowsFeature Web-Server` komutunu çalıştırır ve ardından VM’nin ana bilgisayar adını göstermek için Default.htm sayfasını güncelleştirir:

> [!IMPORTANT]
> Devam etmeden önce sanal makine dağıtımlarının önceki adımlardan tamamlandığından emin olun.  `Get-Job`Sanal makine dağıtım işlerinin durumunu denetlemek için kullanın.

```azurepowershell-interactive
## For loop with variable to install custom script extension on virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
$ext = @{
    Publisher = 'Microsoft.Compute'
    ExtensionType = 'CustomScriptExtension'
    ExtensionName = 'IIS'
    ResourceGroupName = 'CreatePubLBQS-rg'
    VMName = "myVM$i"
    Location = 'eastus'
    TypeHandlerVersion = '1.8'
    SettingString = '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
}
Set-AzVMExtension @ext -AsJob
}
```

Uzantılar, PowerShell işleri olarak dağıtılır. Yükleme işlerinin durumunu görüntülemek için [Get-Job](/powershell/module/microsoft.powershell.core/get-job)kullanın:


```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
8      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
9      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
10     Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
```

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Yük dengeleyicinin genel IP adresini almak için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) kullanın:

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```

Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. IIS Web sunucusunun varsayılan sayfası, tarayıcıda görüntülenir.

   ![IIS Web sunucusu](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Yük dengeleyicinin trafiği üç VM 'ye dağıtmasını görmek için, her bir sanal makinenin IIS Web sunucusunun varsayılan sayfasını özelleştirebilir ve sonra Web tarayıcınızı istemci makinesinden yenileyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, yük dengeleyiciyi ve kalan kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePubLBQS-rg'

```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta:

* Standart veya temel bir genel yük dengeleyici oluşturdunuz
* Bağlı sanal makineler. 
* Yük dengeleyici trafik kuralı ve sistem durumu araştırması yapılandırıldı.
* Yük dengeleyici test edildi.

Azure Load Balancer hakkında daha fazla bilgi edinmek için devam edin:
> [!div class="nextstepaction"]
> [Azure Load Balancer nedir?](load-balancer-overview.md)

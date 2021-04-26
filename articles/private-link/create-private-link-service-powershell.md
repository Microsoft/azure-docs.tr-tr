---
title: 'Hızlı başlangıç: Azure PowerShell kullanarak bir Azure özel bağlantı hizmeti oluşturma'
description: Bu hızlı başlangıçta Azure PowerShell kullanarak bir Azure özel bağlantı hizmeti oluşturmayı öğrenin
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/24/2021
ms.author: allensu
ms.openlocfilehash: 366be37135808a6d3d5cc1a277e2de3e3d6da8ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566422"
---
# <a name="quickstart-create-a-private-link-service-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak özel bağlantı hizmeti oluşturma

Hizmetinize başvuran bir özel bağlantı hizmeti oluşturmaya başlayın.  Azure Standart Load Balancer arkasında dağıtılan hizmetinize veya kaynağa özel bağlantı erişimi verin.  Hizmetinizin kullanıcılarının sanal ağından özel erişimi vardır.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell yerel olarak veya Azure Cloud Shell yüklendi

PowerShell'i yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure PowerShell modülü 5.4.1 veya sonraki bir sürümünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için öğesini de çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun:

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```

## <a name="create-an-internal-load-balancer"></a>İç yük dengeleyici oluşturma

Bu bölümde, bir sanal ağ ve bir iç Azure Load Balancer oluşturacaksınız.

### <a name="virtual-network"></a>Sanal ağ

Bu bölümde, özel bağlantı hizmetinize erişen yük dengeleyiciyi barındırmak için bir sanal ağ ve alt ağ oluşturursunuz.

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    PrivateLinkServiceNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig
}
$vnet = New-AzVirtualNetwork @net

```

### <a name="create-standard-load-balancer"></a>Standart yük dengeleyici oluştur

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:

* Ön uç IP havuzu için [New-Azloadbalancerfrontendıpconfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) ile bir ön uç IP 'si oluşturun. Bu IP, yük dengeleyicide gelen trafiği alır

* Yük dengeleyicinin ön uçınızdan gönderilen trafik için [New-Azloadbalancerbackendadddresspoolconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) ile arka uç adres havuzu oluşturun. Bu havuz, arka uç sanal makinelerinizin dağıtıldığı yerdir.

* [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) ile arka uç sanal makine örneklerinin sistem durumunu belirleyen bir sistem durumu araştırması oluşturun.

* Trafiğin VM 'lere nasıl dağıtıldığını tanımlayan [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) ile bir yük dengeleyici kuralı oluşturun.

* [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)ile bir genel yük dengeleyici oluşturun.


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

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
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus2'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-a-private-link-service"></a>Özel bağlantı hizmeti oluşturma

Bu bölümde, önceki adımda oluşturulan standart Azure Load Balancer kullanan bir özel bağlantı hizmeti oluşturun.

* [New-AzPrivateLinkServiceIpConfig](/powershell/module/az.network/new-azprivatelinkserviceipconfig)ile özel bağlantı hizmeti IP yapılandırması oluşturun.

* [New-AzPrivateLinkService](/powershell/module/az.network/new-azprivatelinkservice)ile özel bağlantı hizmeti oluşturun.

```azurepowershell
## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create the IP configuration for the private link service. ##
$ipsettings = @{
    Name = 'myIPconfig'
    PrivateIpAddress = '10.1.0.5'
    Subnet = $vnet.subnets[0]
}
$ipconfig = New-AzPrivateLinkServiceIpConfig @ipsettings

## Place the load balancer frontend configuration into a variable. ##
$par = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$fe = Get-AzLoadBalancer @par | Get-AzLoadBalancerFrontendIpConfig

## Create the private link service for the load balancer. ##
$privlinksettings = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    LoadBalancerFrontendIpConfiguration = $fe
    IpConfiguration = $ipconfig
}
New-AzPrivateLinkService @privlinksettings

```

Özel bağlantı hizmetiniz oluşturulur ve trafik alabilir. Trafik akışlarını görmek istiyorsanız uygulamanızı standart yük dengeleyicinizin arkasında yapılandırın.

## <a name="create-private-endpoint"></a>Özel uç nokta oluştur

Bu bölümde özel bağlantı hizmetini özel bir uç nokta ile eşlersiniz. Bir sanal ağ, özel bağlantı hizmetinin özel uç noktasını içerir. Bu sanal ağ, özel bağlantı hizmetinize erişecek kaynakları içerir.

### <a name="create-private-endpoint-virtual-network"></a>Özel uç nokta sanal ağı oluştur

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile bir sanal ağ oluşturun.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnetPE'
    AddressPrefix = '11.1.0.0/24'
    PrivateEndpointNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '11.1.0.0/16'
    Subnet = $subnetConfig
}
$vnetpe = New-AzVirtualNetwork @net

```

### <a name="create-endpoint-and-connection"></a>Uç nokta ve bağlantı oluştur

* Daha sonra kullanmak üzere daha önce oluşturduğunuz özel bağlantı hizmetinin yapılandırmasını bir değişkene yerleştirmek için [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) komutunu kullanın.

* Bağlantı yapılandırmasını oluşturmak için [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) komutunu kullanın.

* Uç noktayı oluşturmak için [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) komutunu kullanın.



```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

## Create the private link configuration and place in variable. ##
$par2 = @{
    Name = 'myPrivateLinkConnection'
    PrivateLinkServiceId = $pls.Id
}
$plsConnection = New-AzPrivateLinkServiceConnection @par2

## Place the virtual network into a variable. ##
$par3 = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$vnetpe = Get-AzVirtualNetwork @par3

## Create private endpoint ##
$par4 = @{
    Name = 'MyPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    Subnet = $vnetpe.subnets[0]
    PrivateLinkServiceConnection = $plsConnection
}
New-AzPrivateEndpoint @par4 -ByManualRequest
```

### <a name="approve-the-private-endpoint-connection"></a>Özel uç nokta bağlantısını onaylama

Bu bölümde, önceki adımlarda oluşturduğunuz bağlantıyı onaylayacaksınız.

* Bağlantıyı onaylamak için [Onayla-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection) komutunu kullanın.

```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

$par2 = @{
    Name = $pls.PrivateEndpointConnections[0].Name
    ServiceName = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Description = 'Approved'
}
Approve-AzPrivateEndpointConnection @par2

```

### <a name="ip-address-of-private-endpoint"></a>Özel uç noktanın IP adresi

Bu bölümde, yük dengeleyici ve özel bağlantı hizmetine karşılık gelen özel uç noktanın IP adresini bulacaksınız.

* IP adresini almak için [Get-AzPrivateEndpoint](/powershell/module/az.network/get-azprivateendpoint) komutunu kullanın.

```azurepowershell-interactive
## Get private endpoint and the IP address and place in a variable for display. ##
$par1 = @{
    Name = 'myPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    ExpandResource = 'networkinterfaces'
}
$pe = Get-AzPrivateEndpoint @par1

## Display the IP address by expanding the variable. ##
$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
```

```bash
❯ $pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
11.1.0.4
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, yük dengeleyiciyi ve kalan kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePrivLinkService-rg'
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta:

* Bir sanal ağ ve dahili Azure Load Balancer oluşturulur.
* Özel bir bağlantı hizmeti oluşturuldu

Azure özel uç noktası hakkında daha fazla bilgi edinmek için devam edin:
> [!div class="nextstepaction"]
> [Hızlı başlangıç: Azure PowerShell kullanarak özel uç nokta oluşturma](create-private-endpoint-powershell.md)


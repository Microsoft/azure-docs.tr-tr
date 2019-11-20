---
title: Azure PowerShell kullanarak bir Azure özel bağlantı hizmeti oluşturun | Microsoft Docs
description: Azure PowerShell kullanarak bir Azure özel bağlantı hizmeti oluşturmayı öğrenin
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: d8d40ed83d7f8234092ca6354642a76aaa83bc12
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173075"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Azure PowerShell kullanarak özel bir bağlantı hizmeti oluşturma
Bu makalede, Azure 'da Azure PowerShell kullanarak özel bir bağlantı hizmeti oluşturma gösterilmektedir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz, bu makalede en son Azure PowerShell modülü sürümü gerekir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Özel bağlantıyı oluşturabilmeniz için, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturmanız gerekir. Aşağıdaki örnek *WestCentralUS* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Sanal ağ oluşturun
[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile özel bağlantınız için bir sanal ağ oluşturun. Aşağıdaki örnek, ön uç (*Frontendsubnet*), arka uç (*backendsubnet*), özel bağlantı (*othersubnet*) için alt ağ ile *myvnet* adlı bir sanal ağ oluşturur:

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>Iç Load Balancer oluştur
[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)ile iç standart Load Balancer oluşturun. Aşağıdaki örnek, önceki adımlarda oluşturduğunuz ön uç IP yapılandırması, araştırma, kural ve arka uç havuzunu kullanarak bir iç Standart Load Balancer oluşturur:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Özel bağlantı hizmeti oluşturma
[New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer)ile özel bir bağlantı hizmeti oluşturun.  Bu örnek, *Myresourcegroup*adlı kaynak grubunda standart Load Balancer kullanarak *mypls* adlı bir özel bağlantı hizmeti oluşturur. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Özel bağlantı hizmeti al
[New-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) ile özel bağlantı hizmetinize ilişkin ayrıntıları aşağıdaki gibi alın:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

Bu aşamada, özel bağlantı hizmetiniz başarıyla oluşturulur ve trafik almaya hazırdır. Yukarıdaki örneğin yalnızca PowerShell kullanarak özel bağlantı hizmeti oluşturmayı gösterdiğine unutmayın.  Trafiği dinlemek için yük dengeleyici arka uç havuzlarını veya arka uç havuzlarındaki herhangi bir uygulamayı yapılandırmadınız. Uçtan uca trafik akışlarını görmek istiyorsanız uygulamanızı standart yük dengeleyicinizin arkasında yapılandırmanız önemle önerilir. 

Ardından, PowerShell kullanarak bu hizmeti farklı VNet 'teki özel bir uç noktaya nasıl eşleneceğini göstereceğiz. Bu örnek, Özel uç nokta oluşturma ve yukarıda oluşturulan özel bağlantı hizmetine bağlanma ile sınırlıdır. Senaryonuzu oluşturmak için özel uç noktaya trafik göndermek/almak için sanal ağda sanal makineler oluşturabilirsiniz. 

## <a name="create-a-private-endpoint"></a>Özel Uç Nokta oluşturma
### <a name="create-a-virtual-network"></a>Sanal ağ oluşturun
[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile özel uç noktanız için bir sanal ağ oluşturun. Bu örnek, *Myresourcegroup*adlı kaynak grubunda *vnetpe* adlı bir sanal ağ oluşturur:
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Özel uç nokta oluşturma
Sanal ağınızda yukarıda oluşturulan özel bağlantı hizmeti için özel bir uç nokta oluşturun:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Özel uç nokta al
`Get-AzPrivateEndpoint` aşağıdaki gibi özel uç noktanın IP adresini alın:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Özel uç nokta bağlantısını onaylama
Özel bağlantı hizmetine özel uç noktası bağlantısını ' Onayla-AzPrivateEndpointConnection ' ile onaylayın.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Sonraki adımlar
- [Azure özel bağlantısı](private-link-overview.md) hakkında daha fazla bilgi edinin
 

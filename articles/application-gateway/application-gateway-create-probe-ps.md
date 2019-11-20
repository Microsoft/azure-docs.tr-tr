---
title: PowerShell kullanarak özel bir araştırma oluşturma
titleSuffix: Azure Application Gateway
description: PowerShell kullanarak Application Gateway için özel bir araştırma oluşturmayı öğrenin Kaynak Yöneticisi
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 1fef24f4065ca6fc749f35a07143487e049ee6ea
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075269"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Azure Resource Manager için PowerShell 'i kullanarak Azure Application Gateway için özel bir araştırma oluşturma

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Klasik PowerShell](application-gateway-create-probe-classic-ps.md)

Bu makalede, var olan bir Application Gateway 'e PowerShell ile özel bir araştırma eklersiniz. Özel yoklamalar, belirli bir sistem durumu denetimi sayfası veya varsayılan Web uygulamasında başarılı bir yanıt sağlamayan uygulamalar için yararlıdır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Özel araştırmasına sahip bir uygulama ağ geçidi oluşturma

### <a name="sign-in-and-create-resource-group"></a>Oturum aç ve kaynak grubu oluştur

1. Kimlik doğrulamak için `Connect-AzAccount` kullanın.

   ```powershell
   Connect-AzAccount
   ```

1. Hesap için abonelikleri alın.

   ```powershell
   Get-AzSubscription
   ```

1. Hangi Azure aboneliğinizin kullanılacağını seçin.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. Bir kaynak grubu oluşturun. Mevcut bir kaynak grubunuz varsa, bu adımı atlayabilirsiniz.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Azure Resource Manager, tüm kaynak gruplarının bir konum belirtmesini gerektirir. Bu konum, kaynak grubundaki kaynaklar için varsayılan konum olarak kullanılır. Uygulama ağ geçidi oluşturmak için tüm komutların aynı kaynak grubunu kullanmasını sağlayın.

Yukarıdaki örnekte, **Batı ABD**konumunda **APPGW-RG** adlı bir kaynak grubu oluşturduk.

### <a name="create-a-virtual-network-and-a-subnet"></a>Sanal ağ ve alt ağ oluşturma

Aşağıdaki örnek, uygulama ağ geçidi için bir sanal ağ ve bir alt ağ oluşturur. Application Gateway 'in kullanım için kendi alt ağı gerekir. Bu nedenle, uygulama ağ geçidi için oluşturulan alt ağ, diğer alt ağların oluşturulup kullanılmasına izin vermek için VNET 'in adres alanından daha küçük olmalıdır.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Ön uç yapılandırma için genel bir IP adresi oluşturun

Batı ABD bölgesi için **appgw-rg** kaynak grubunda **publicIP01** genel bir IP kaynağı oluşturun. Bu örnek, uygulama ağ geçidinin ön uç IP adresi için genel bir IP adresi kullanır.  Application Gateway genel IP adresinin dinamik olarak oluşturulan bir DNS adına sahip olmasını gerektirir, bu nedenle genel IP adresi oluşturma sırasında `-DomainNameLabel` belirtilemez.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

Uygulama ağ geçidini oluşturmadan önce tüm yapılandırma öğelerini ayarlarsınız. Aşağıdaki örnek, bir uygulama ağ geçidi kaynağı için gereken yapılandırma öğelerini oluşturur.

| **Bileşen** | **Açıklama** |
|---|---|
| **Ağ geçidi IP yapılandırması** | Uygulama ağ geçidi için bir IP yapılandırması.|
| **Arka uç havuzu** | Web uygulamasını barındıran uygulama sunucularına yönelik IP adresleri, FQDN 'ler veya NIC 'ler havuzu|
| **Durum araştırması** | Arka uç havuzu üyelerinin durumunu izlemek için kullanılan özel bir araştırma|
| **HTTP ayarları** | Bağlantı noktası, protokol, tanımlama bilgisi tabanlı benzeşim, araştırma ve zaman aşımı dahil olmak üzere ayarların koleksiyonu.  Bu ayarlar trafiğin arka uç havuzu üyelerine nasıl yönlendirildiğini belirleme|
| **Ön uç bağlantı noktası** | Uygulama ağ geçidinin trafiği dinlediği bağlantı noktası|
| **Oluşturulurken** | Protokol, ön uç IP yapılandırması ve ön uç bağlantı noktası birleşimi. Bu, gelen istekleri dinler.
|**Kural**| Trafiği HTTP ayarlarına bağlı olarak uygun arka uca yönlendirir.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Mevcut bir Application Gateway 'e araştırma ekleme

Aşağıdaki kod parçacığı, var olan bir uygulama ağ geçidine bir araştırma ekler.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Mevcut bir Application Gateway 'ten bir araştırma kaldırma

Aşağıdaki kod parçacığı, var olan bir Application Gateway 'ten bir araştırma kaldırır.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Uygulama ağ geçidi DNS adını alma

Ağ geçidi oluşturulduktan sonraki adım, iletişim için ön uç yapılandırması yapmaktır. Genel IP kullanırken uygulama ağ geçidi için dinamik olarak atanan DNS adı gerekir ve bu durum çok kullanışlı değildir. Son kullanıcıların uygulama ağ geçidine ulaşmasını sağlamak için uygulama ağ geçidinin genel uç noktasını işaret edecek bir CNAME kaydı kullanılabilir. [Azure’da özel etki alanı adı yapılandırma](../cloud-services/cloud-services-custom-domain-name-portal.md). Bunu yapmak için uygulama ağ geçidinin ayrıntılarını ve onunla ilişkilendirilmiş olan IP/DNS adını uygulama ağ geçidine eklenmiş PublicIPAddress öğesini kullanarak alın. Uygulama ağ geçidinin DNS adı, iki web uygulamasını bu DNS adına götüren bir CNAME kaydı oluşturmak için kullanılmalıdır. Uygulama ağ geçidi yeniden başlatıldığında VIP değişebileceğinden A kaydı kullanımı önerilmez.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Sonraki adımlar

SSL boşaltma yapılandırmasını ziyaret ederek yapılandırmayı öğrenin: [SSL yük boşaltma yapılandırma](application-gateway-ssl-arm.md)


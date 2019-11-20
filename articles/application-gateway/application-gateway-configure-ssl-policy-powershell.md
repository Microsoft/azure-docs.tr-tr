---
title: PowerShell kullanarak SSL ilkesini yapılandırma
titleSuffix: Azure Application Gateway
description: Bu makalede, Azure Application Gateway SSL Ilkesini yapılandırma yönergeleri sağlanır
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 105b0b3e40e6e9433ee456914cd5babc1d17d036
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075242"
---
# <a name="configure-ssl-policy-versions-and-cipher-suites-on-application-gateway"></a>Application Gateway SSL ilkesi sürümlerini ve şifre paketlerini yapılandırma

Application Gateway üzerindeki SSL ilkesi sürümlerinin ve şifre paketlerinin nasıl yapılandırılacağını öğrenin. SSL ilkesi sürümlerinin ve etkin şifreleme paketlerinin farklı yapılandırmalarını içeren önceden tanımlanmış ilkelerin listesinden seçim yapabilirsiniz. Gereksinimlerinize göre [Özel BIR SSL ilkesi](#configure-a-custom-ssl-policy) tanımlama olanağınız da vardır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-available-ssl-options"></a>Kullanılabilir SSL seçeneklerini al

`Get-AzApplicationGatewayAvailableSslOptions` cmdlet 'i, yapılandırılabilen kullanılabilir önceden tanımlanmış ilkelerin, kullanılabilir şifre paketlerinin ve protokol sürümlerinin bir listesini sağlar. Aşağıdaki örnekte cmdlet 'ini çalıştırmanın örnek bir çıkışı gösterilmektedir.

```
DefaultPolicy: AppGwSslPolicy20150501
PredefinedPolicies:
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20150501
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401S

AvailableCipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
    TLS_RSA_WITH_AES_128_GCM_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA256
    TLS_RSA_WITH_AES_128_CBC_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA
    TLS_RSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_3DES_EDE_CBC_SHA
    TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

AvailableProtocols:
    TLSv1_0
    TLSv1_1
    TLSv1_2
```

## <a name="list-pre-defined-ssl-policies"></a>Önceden tanımlanmış SSL Ilkelerini listeleyin

Application Gateway, kullanılabilecek üç önceden tanımlı ilke ile gelir. `Get-AzApplicationGatewaySslPredefinedPolicy` cmdlet 'i bu ilkeleri alır. Her ilkenin farklı protokol sürümleri ve şifre paketleri etkinleştirilmiştir. Önceden tanımlanmış bu ilkeler, uygulama Ağ geçidinizde bir SSL ilkesini hızlıca yapılandırmak için kullanılabilir. Varsayılan olarak, belirli bir SSL ilkesi tanımlanmamışsa **AppGwSslPolicy20150501** seçilidir.

Aşağıdaki çıktı `Get-AzApplicationGatewaySslPredefinedPolicy`çalıştırmanın bir örneğidir.

```
Name: AppGwSslPolicy20150501
MinProtocolVersion: TLSv1_0
CipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
 ...
Name: AppGwSslPolicy20170401
MinProtocolVersion: TLSv1_1
CipherSuites:
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
...
```

## <a name="configure-a-custom-ssl-policy"></a>Özel bir SSL ilkesi yapılandırma

Özel bir SSL ilkesi yapılandırılırken şu parametreleri geçirirsiniz: PolicyType, MinProtocolVersion, CipherSuite ve ApplicationGateway. Diğer parametreleri geçirmeye çalışırsanız, Application Gateway oluştururken veya güncelleştirirken bir hata alırsınız. 

Aşağıdaki örnek, bir uygulama ağ geçidinde özel bir SSL ilkesi ayarlıyor. En düşük protokol sürümünü `TLSv1_1` olarak ayarlar ve aşağıdaki şifre paketlerini sağlar:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

> [!IMPORTANT]
> Özel bir SSL ilkesi yapılandırılırken TLS_RSA_WITH_AES_256_CBC_SHA256 seçilmelidir. Application Gateway, arka uç yönetimi için bu şifre paketini kullanır. Bunu diğer herhangi bir paket ile birlikte kullanabilirsiniz, ancak bunun de seçilmesi gerekir. 

```powershell
# get an application gateway resource
$gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroup AdatumAppGatewayRG

# set the SSL policy on the application gateway
Set-AzApplicationGatewaySslPolicy -ApplicationGateway $gw -PolicyType Custom -MinProtocolVersion TLSv1_1 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"

# validate the SSL policy locally
Get-AzApplicationGatewaySslPolicy -ApplicationGateway $gw

# update the gateway with validated SSL policy
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="create-an-application-gateway-with-a-pre-defined-ssl-policy"></a>Önceden tanımlanmış SSL ilkesiyle bir uygulama ağ geçidi oluşturma

Önceden tanımlanmış bir SSL ilkesi yapılandırılırken şu parametreleri geçirirsiniz: PolicyType, PolicyName ve ApplicationGateway. Diğer parametreleri geçirmeye çalışırsanız, Application Gateway oluştururken veya güncelleştirirken bir hata alırsınız.

Aşağıdaki örnek, önceden tanımlanmış bir SSL ilkesiyle yeni bir uygulama ağ geçidi oluşturur.

```powershell
# Create a resource group
$rg = New-AzResourceGroup -Name ContosoRG -Location "East US"

# Create a subnet for the application gateway
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network with a 10.0.0.0/16 address space
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for later use
$subnet = $vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location "East US" -AllocationMethod Dynamic

# Create an ip configuration object
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool for backend web servers
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

# Define the backend http settings to be used.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

# Create a new port for SSL
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 443

# Upload an existing pfx certificate for SSL offload
$password = ConvertTo-SecureString -String "P@ssw0rd" -AsPlainText -Force
$cert = New-AzApplicationGatewaySslCertificate -Name cert01 -CertificateFile C:\folder\contoso.pfx -Password $password

# Create a frontend IP configuration for the public IP address
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener with the certificate, port, and frontend ip.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a new rule for backend traffic routing
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the size of the application gateway
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Configure the SSL policy to use a different pre-defined policy
$policy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Create the application gateway.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName $rg.ResourceGroupName -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

## <a name="update-an-existing-application-gateway-with-a-pre-defined-ssl-policy"></a>Mevcut bir uygulama ağ geçidini önceden tanımlanmış SSL ilkesiyle güncelleştirme

Özel bir SSL ilkesi ayarlamak için şu parametreleri geçirin: **PolicyType**, **MinProtocolVersion**, **ciphersuite**ve **applicationgateway**. Önceden tanımlanmış bir SSL ilkesi ayarlamak için şu parametreleri geçirin: **PolicyType**, **PolicyName**ve **applicationgateway**. Diğer parametreleri geçirmeye çalışırsanız, Application Gateway oluştururken veya güncelleştirirken bir hata alırsınız.

Aşağıdaki örnekte, hem özel Ilke hem de önceden tanımlanmış Ilke için kod örnekleri mevcuttur. Kullanmak istediğiniz ilkenin açıklamasını kaldırın.

```powershell
# You have to change these parameters to match your environment.
$AppGWname = "YourAppGwName"
$RG = "YourResourceGroupName"

$AppGw = get-Azapplicationgateway -Name $AppGWname -ResourceGroupName $RG

# Choose either custom policy or predefined policy and uncomment the one you want to use.

# SSL Custom Policy
# Set-AzApplicationGatewaySslPolicy -PolicyType Custom -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_RSA_WITH_AES_128_CBC_SHA256" -ApplicationGateway $AppGw

# SSL Predefined Policy
# Set-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S" -ApplicationGateway $AppGW

# Update AppGW
# The SSL policy options are not validated or updated on the Application Gateway until this cmdlet is executed.
$SetGW = Set-AzApplicationGateway -ApplicationGateway $AppGW
```

## <a name="next-steps"></a>Sonraki adımlar

HTTP trafiğini bir HTTPS uç noktasına yeniden yönlendirmeyi öğrenmek için [Application Gateway yeniden yönlendirmeye genel bakış ' ı](application-gateway-redirect-overview.md) ziyaret edin.

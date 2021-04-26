---
title: PowerShell aracılığıyla Azure Application Gateway karşılıklı kimlik doğrulamasını yapılandırma
description: Application Gateway PowerShell aracılığıyla karşılıklı kimlik doğrulamasına sahip olacak şekilde yapılandırmayı öğrenin
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231617"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>PowerShell aracılığıyla Application Gateway karşılıklı kimlik doğrulamasını yapılandırma (Önizleme)
Bu makalede, Application Gateway karşılıklı kimlik doğrulamasını yapılandırmak için PowerShell 'in nasıl kullanılacağı açıklanır. Karşılıklı kimlik doğrulaması, Application Gateway karşıya yüklediğiniz istemci sertifikasını kullanarak isteği gönderen istemcinin kimlik doğrulamasını Application Gateway anlamına gelir. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makale Azure PowerShell Module sürümü 1.0.0 veya üstünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Login-AzAccount` Azure ile bir bağlantı oluşturmak için öğesini de çalıştırmanız gerekir.

## <a name="before-you-begin"></a>Başlamadan önce

Application Gateway karşılıklı kimlik doğrulamasını yapılandırmak için, ağ geçidine yüklemek üzere bir istemci sertifikasına ihtiyacınız vardır. İstemci sertifikası, istemcinin Application Gateway için sunacağı sertifikayı doğrulamak için kullanılacaktır. Sınama amacıyla, kendinden imzalı bir sertifika kullanabilirsiniz. Ancak, bu, üretim iş yükleri için önerilmez çünkü yönetilmesi zordur ve tamamen güvende değildir.

Daha fazla bilgi edinmek için, özellikle ne tür istemci sertifikaları karşıya yükleyebilir, bkz. [Application Gateway ile karşılıklı kimlik doğrulamaya genel bakış](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Önce aboneliğinizde yeni bir kaynak grubu oluşturun. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Application Gateway dağıtılması için bir sanal ağ dağıtın.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>Genel IP oluşturma

Application Gateway ile kullanmak için bir genel IP oluşturun. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>Application Gateway IP yapılandırması oluşturma

IP yapılandırmasını ve ön uç bağlantı noktasını oluşturun. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>Ön uç SSL 'yi yapılandırma 

Application Gateway için SSL sertifikalarını yapılandırın.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>İstemci kimlik doğrulamasını yapılandırma 

Application Gateway istemci kimlik doğrulamasını yapılandırın. Burada kullanmak üzere güvenilen istemci CA sertifikası zincirlerinin nasıl ayıklanacağı hakkında daha fazla bilgi için bkz. [Güvenilen ISTEMCI CA sertifika zincirlerini ayıklama](./mutual-authentication-certificate-management.md).

> [!IMPORTANT]
> Lütfen tüm istemci CA sertifika zincirini tek bir dosyaya ve dosya başına yalnızca bir zincir karşıya yüklediğinizden emin olun.  

> [!NOTE]
> TLS 1,2, gelecekte uygulanan olacağı için TLS 1,2 ' i karşılıklı kimlik doğrulaması ile kullanmanızı öneririz. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>Arka uç havuzunu ve ayarlarını yapılandırma

Application Gateway için arka uç havuzu ve ayarlarını ayarlayın. İsteğe bağlı olarak, uçtan uca SSL şifrelemesi için arka uç güvenilen kök sertifikayı ayarlayın.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>Kuralı yapılandırma

Application Gateway bir kural ayarlayın.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>Gelecekteki dinleyiciler için varsayılan SSL ilkesini ayarlama

Karşılıklı kimlik doğrulaması kurarken bir dinleyiciye özgü SSL ilkesi ayartık. Bu adımda, isteğe bağlı olarak varsayılan SSL ilkesini oluşturduğunuz daha sonraki dinleyiciler için ayarlayabilirsiniz. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>Application Gateway oluşturun

Yukarıda oluşturduğumuz her şeyi kullanarak Application Gateway dağıtın.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)komutunu kullanarak kaynak grubunu, uygulama ağ geçidini ve ilgili tüm kaynakları kaldırın.

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>Vadesi geçen istemci CA sertifikalarını Yenile

İstemci CA sertifikanızın süresi dolmuşsa, ağ geçidinizdeki sertifikayı aşağıdaki adımlarla güncelleştirebilirsiniz: 

1. Azure'da oturum açma
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. Application Gateway yapılandırmanızı alın
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. Güvenilen istemci sertifikasını ağ geçidinden kaldır 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. Yeni sertifikayı ağ geçidine ekleyin 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. Ağ geçidini yeni sertifikayla Güncelleştir 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLI kullanarak bir uygulama ağ geçidi ile web trafiğini yönetme](./tutorial-manage-web-traffic-cli.md)
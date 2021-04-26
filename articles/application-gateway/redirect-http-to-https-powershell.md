---
title: PowerShell kullanarak HTTP 'den HTTPS 'ye yönlendirme-Azure Application Gateway
description: Azure PowerShell kullanarak HTTP 'den HTTPS 'ye yeniden yönlendirilen trafiğe sahip bir uygulama ağ geçidi oluşturmayı öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/28/2020
ms.author: victorh
ms.openlocfilehash: c9118d0e5b314f05e89334991c68ec1b3b5751e2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866314"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-azure-powershell"></a>Azure PowerShell kullanarak HTTP ile HTTPS yönlendirmesi arasında bir uygulama ağ geçidi oluşturma

Azure PowerShell, TLS/SSL sonlandırma sertifikasıyla bir [uygulama ağ geçidi](overview.md) oluşturmak için kullanabilirsiniz. Yönlendirme kuralı, HTTP trafiğini uygulama ağ geçidinizdeki HTTPS bağlantı noktasına yönlendirmek için kullanılır. Bu örnekte, iki sanal makine örneği içeren uygulama ağ geçidinin arka uç havuzu için bir [sanal makine ölçek kümesi](../virtual-machine-scale-sets/overview.md) de oluşturacaksınız. 

Bu makalede şunları öğreneceksiniz:

* Otomatik olarak imzalanan sertifika oluşturma
* Ağ ayarlama
* Sertifikalı bir uygulama ağ geçidi oluşturma
* Dinleyici ve yeniden yönlendirme kuralı ekleme
* Varsayılan arka uç havuzuyla bir sanal makine ölçek kümesi oluşturma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). Bu öğreticideki komutları çalıştırmak için, `Login-AzAccount` Azure ile bağlantı oluşturmak için komutunu da çalıştırmanız gerekir.

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

Üretim kullanımı için, güvenilir bir sağlayıcı tarafından imzalanmış geçerli bir sertifikayı içeri aktarmanız gerekir. Bu öğretici için [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) komutunu kullanarak otomatik olarak imzalanan bir sertifika oluşturursunuz. Sertifikadan pfx dosyası dışarı aktarmak için döndürülen Parmak izi ile [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) komutunu kullanabilirsiniz.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Bu sonuca benzer bir şey görmeniz gerekir:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

pfx dosyasını oluşturmak için parmak izini kullanın:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)kullanarak *MyResourceGroupAG* adlı bir Azure Kaynak grubu oluşturun. 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)kullanarak *mybackendsubnet* ve *myagsubnet* için alt ağ yapılandırmalarını oluşturun. Alt ağ yapılandırmalarına sahip [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) kullanarak *myvnet* adlı sanal ağı oluşturun. Son olarak, [New-azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)kullanarak *Myagpublicıpaddress* adlı genel IP adresini oluşturun. Bu kaynaklar, uygulama ağ geçidi ve ilişkili kaynakları ile ağ bağlantısı sağlamak için kullanılır.

```powershell
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP yapılandırmaları ve ön uç bağlantı noktası oluşturma

[Yeni-Azapplicationgatewayıp](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)'leri kullanarak daha önce oluşturduğunuz *Myagsubnet* 'i uygulama ağ geçidine ilişkilendirin. [New-Azapplicationgatewayfrontendıpconfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)kullanarak Application Gateway 'e *Myagpublicıpaddress* atayın. Ardından, [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport)kullanarak HTTPS bağlantı noktasını oluşturabilirsiniz.

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendPort = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Arka uç havuzu ve ayarları oluşturma

[New-Azapplicationgatewaybackendirddresspool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)kullanarak Application Gateway Için *Appgatewaybackendpool* adlı arka uç havuzunu oluşturun. [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)kullanarak arka uç havuzunun ayarlarını yapılandırın.

```powershell
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Varsayılan dinleyici ve kural oluşturma

Uygulama ağ geçidinin trafiği arka uç havuzuna uygun şekilde yönlendirmesini sağlamak içn bir dinleyici gereklidir. Bu örnekte, kök URL’deki HTTPS trafiğini dinleyen temel bir dinleyici oluşturacaksınız. 

[New-AzApplicationGatewaySslCertificate](/powershell/module/az.network/new-azapplicationgatewaysslcertificate) kullanarak bir sertifika nesnesi oluşturun ve ardından ön uç yapılandırması, ön uç bağlantı noktası ve daha önce oluşturduğunuz sertifikayla [New-Azapplicationgatewayhttplistener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) kullanarak *appgatewayhttplistener* adlı bir dinleyici oluşturun. Dinleyicinin gelen trafik için kullanacağı arka uç havuzunu bilmesi için bir kural gerekir. [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)kullanarak *rule1* adlı temel bir kural oluşturun.

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -SslCertificate $cert
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

Artık gerekli destekleyici kaynakları oluşturduğunuza göre, [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)kullanarak *myappgateway* adlı uygulama ağ geçidi için parametreleri belirtin ve ardından sertifikayla [New-azapplicationgateway](/powershell/module/az.network/new-azapplicationgateway) kullanarak oluşturun.

```powershell
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="add-a-listener-and-redirection-rule"></a>Dinleyici ve yeniden yönlendirme kuralı ekleme

### <a name="add-the-http-port"></a>HTTP bağlantı noktasını ekleme

[Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport)kullanarak http bağlantı noktasını uygulama ağ geçidine ekleyin.

```powershell
$appgw = Get-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG
Add-AzApplicationGatewayFrontendPort `
  -Name httpPort  `
  -Port 80 `
  -ApplicationGateway $appgw
```

### <a name="add-the-http-listener"></a>HTTP dinleyicisini ekleyin

[Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener)kullanarak Application Gateway 'e *MYLISTENER* adlı HTTP dinleyicisini ekleyin.

```powershell
$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
$fp = Get-AzApplicationGatewayFrontendPort `
  -Name httpPort `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -Name myListener `
  -Protocol Http `
  -FrontendPort $fp `
  -FrontendIPConfiguration $fipconfig `
  -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Yeniden yönlendirme yapılandırmasını ekleyin

[Add-AzApplicationGatewayRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration)kullanarak http-https yeniden yönlendirme yapılandırmasını uygulama ağ geçidine ekleyin.

```powershell
$defaultListener = Get-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration -Name httpToHttps `
  -RedirectType Permanent `
  -TargetListener $defaultListener `
  -IncludePath $true `
  -IncludeQueryString $true `
  -ApplicationGateway $appgw
```

### <a name="add-the-routing-rule"></a>Yönlendirme kuralını ekleme

[Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)kullanarak uygulama ağ geçidine yönlendirme kuralını yeniden yönlendirme kuralını ekleyin.

```powershell
$myListener = Get-AzApplicationGatewayHttpListener `
  -Name myListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name httpToHttps `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $myListener `
  -RedirectConfiguration $redirectConfig `
  -ApplicationGateway $appgw
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi oluşturma

Bu örnekte uygulama ağ geçidinde arka uç havuzu için sunucu sağlayan bir sanal makine ölçek kümesi oluşturacaksınız. IP ayarlarını yapılandırırken ölçek kümesini arka uç havuzuna atayın.

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic
Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>IIS yükleme

```powershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama ağ geçidinin genel IP adresini almak için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) komutunu kullanabilirsiniz. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. Örneğin, http://52.170.203.149

```powershell
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Güvenli uyarı](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

Otomatik olarak imzalanan bir sertifika kullandıysanız güvenlik uyarısını kabul etmek için **Ayrıntılar** ' ı seçin ve ardından **Web sayfasına gidin**. Güvenli IIS siteniz, sonra aşağıdaki örnekte olduğu gibi görüntülenir:

![Temel URL’yi uygulama ağ geçidinde test etme](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Sonraki adımlar

- [HTTP üstbilgilerini ve URL 'YI Application Gateway yeniden yazın](rewrite-http-headers-url.md)

---
title: PowerShell kullanarak dış yeniden yönlendirme
titleSuffix: Azure Application Gateway
description: Azure PowerShell kullanarak Web trafiğini dış siteye yönlendiren bir uygulama ağ geçidi oluşturmayı öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 6596cdb2df0a916c49086f80466db60b02a81467
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047750"
---
# <a name="create-an-application-gateway-with-external-redirection-using-azure-powershell"></a>Azure PowerShell kullanarak dış yeniden yönlendirmeye sahip bir uygulama ağ geçidi oluşturma

[Uygulama ağ geçidi](overview.md)oluştururken [Web trafiği yeniden yönlendirmeyi](multiple-site-overview.md) yapılandırmak için Azure PowerShell kullanabilirsiniz. Bu öğreticide, uygulama ağ geçidine gelen Web trafiğini bir dış siteye yönlendiren bir dinleyici ve kural yapılandırırsınız.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Ağı ayarlama
> * Dinleyici ve yeniden yönlendirme kuralı oluşturma
> * Uygulama ağ geçidi oluşturma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modülü sürüm 1.0.0 veya üstünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Login-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)kullanarak bir Azure Kaynak grubu oluşturun.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)kullanarak alt ağ yapılandırmasını *myagsubnet* oluşturun. Alt ağ yapılandırması ile [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) kullanarak *myvnet* adlı sanal ağı oluşturun. Son olarak, [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)kullanarak genel IP adresini oluşturun. Bu kaynaklar, uygulama ağ geçidi ve ilişkili kaynakları ile ağ bağlantısı sağlamak için kullanılır.

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP yapılandırmaları ve ön uç bağlantı noktası oluşturma

[Yeni-Azapplicationgatewayıp](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)'leri kullanarak daha önce oluşturduğunuz *Myagsubnet* 'i uygulama ağ geçidine ilişkilendirin. [New-Azapplicationgatewayfrontendıpconfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)kullanarak genel IP adresini uygulama ağ geçidine atayın. Ardından [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport)kullanarak http bağlantı noktasını oluşturabilirsiniz.

```azurepowershell-interactive
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
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Arka uç havuzu ve ayarları oluşturma

[New-Azapplicationgatewaybackenbaddresspool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)kullanarak Application Gateway Için *defaultpool* adlı arka uç havuzunu oluşturun. [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)kullanarak havuzun ayarlarını yapılandırın.

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name defaultPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-rule"></a>Dinleyiciyi ve kuralı oluşturma

Uygulama ağ geçidinin trafiği uygun şekilde yönlendirebilmesi için bir dinleyici gerekir. [Yeni-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) kullanarak daha önce oluşturduğunuz ön uç yapılandırması ve ön uç bağlantı noktasıyla dinleyiciyi oluşturun. Dinleyicinin gelen trafiğin nereye gönderileceğini bilmesini sağlamak için bir kural gerekir. [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)kullanarak *redirectrule* adlı bir temel kural oluşturun.

```azurepowershell-interactive
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$redirectConfig = New-AzApplicationGatewayRedirectConfiguration `
  -Name myredirect `
  -RedirectType Temporary `
  -TargetUrl "https://bing.com"
$redirectRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name redirectRule `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -RedirectConfiguration $redirectConfig
```

### <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

Artık gerekli destekleyici kaynakları oluşturduğunuza göre, [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)kullanarak *myappgateway* adlı uygulama ağ geçidi için parametreler belirtin ve ardından [New-azapplicationgateway](/powershell/module/az.network/new-azapplicationgateway)kullanarak oluşturun.

```azurepowershell-interactive
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
  -FrontendPorts $frontendport `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $redirectRule `
  -RedirectConfigurations $redirectConfig `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama ağ geçidinin genel IP adresini almak için [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) komutunu kullanabilirsiniz. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

Tarayıcınızda *Bing.com* göründüğünü görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Ağı ayarlama
> * Dinleyici ve yeniden yönlendirme kuralı oluşturma
> * Uygulama ağ geçidi oluşturma

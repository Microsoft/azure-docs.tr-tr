---
title: 'Hızlı başlangıç: PowerShell kullanarak doğrudan web trafiği'
titleSuffix: Azure Application Gateway
description: Web trafiğini bir arka uç havuzundaki sanal makinelere yönlendiren bir Azure Application Gateway oluşturmak için Azure PowerShell nasıl kullanacağınızı öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d5b0ebc2d1b64dd4be677c38de30af7f7a954637
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075097"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak Azure Application Gateway ile doğrudan web trafiği

Bu hızlı başlangıçta, uygulama ağ geçidini hızlıca oluşturmak için Azure PowerShell nasıl kullanılacağı gösterilmektedir.  Uygulama ağ geçidini oluşturduktan sonra, doğru çalıştığından emin olmak için test edin. Azure Application Gateway ile, bağlantı noktalarına dinleyici atayarak, kurallar oluşturarak ve bir arka uç havuzuna kaynak ekleyerek uygulama Web trafiğinizi belirli kaynaklara yönlendirebilirsiniz. Kolaylık sağlaması için bu makalede genel ön uç IP 'si olan basit bir kurulum, bu uygulama ağ geçidinde tek bir siteyi barındırmak için temel bir dinleyici, arka uç havuzu için kullanılan iki sanal makine ve temel bir istek yönlendirme kuralı kullanılır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Önkoşullar

### <a name="azure-powershell-module"></a>Azure PowerShell modülü

Azure PowerShell yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modülü sürüm 1.0.0 veya üstünü gerektirir.

1. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). 
2. Azure ile bağlantı oluşturmak için `Login-AzAccount`çalıştırın.

### <a name="resource-group"></a>Kaynak grubu

Azure 'da, ilgili kaynakları bir kaynak grubuna ayırabilirsiniz. Var olan bir kaynak grubunu kullanabilir ya da yeni bir tane oluşturabilirsiniz. Bu örnekte, [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) cmdlet 'ini kullanarak şu şekilde yeni bir kaynak grubu oluşturacaksınız: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Gerekli ağ kaynakları

Azure 'un, oluşturduğunuz kaynaklar arasında iletişim kurması için bir sanal ağa ihtiyacı vardır.  Application Gateway alt ağı yalnızca uygulama ağ geçitleri içerebilir. Başka hiçbir kaynağa izin verilmez.  Application Gateway için yeni bir alt ağ oluşturabilir veya var olan bir alt ağı kullanabilirsiniz. Bu örnekte, bu örnekte iki alt ağ oluşturursunuz: uygulama ağ geçidi için bir diğeri, arka uç sunucuları için bir diğeri. Application Gateway ön uç IP 'sini kullanım çalışmanıza göre genel veya özel olacak şekilde yapılandırabilirsiniz. Bu örnekte, genel ön uç IP 'si seçersiniz.

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig)' i çağırarak alt ağ yapılandırmalarını oluşturun.
2. [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork)' i çağırarak alt ağ yapılandırmalarına sahip sanal ağ oluşturun. 
3. [New-Azpublicıpaddress](/powershell/module/Az.network/new-Azpublicipaddress)öğesini ÇAĞıRARAK genel IP adresini oluşturun. 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
### <a name="backend-servers"></a>Arka uç sunucuları

Arka uç, NIC 'Ler, sanal makine ölçek kümeleri, genel IP 'Ler, iç IP 'Ler, tam etki alanı adları (FQDN) ve Azure App Service gibi çok kiracılı arka uçlar olabilir. Bu örnekte, Azure için uygulama ağ geçidi için arka uç sunucular olarak kullanılacak iki sanal makine oluşturursunuz. Ayrıca, Azure 'un uygulama ağ geçidini başarıyla oluşturduğunu doğrulamak için sanal makinelere IIS yüklersiniz.

#### <a name="create-two-virtual-machines"></a>İki sanal makine oluşturma

1. [New-Aznetworkınterface](/powershell/module/Az.network/new-Aznetworkinterface)ile bir ağ arabirimi oluşturun. 
2. [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig)ile bir sanal makine yapılandırması oluşturun.
3. [New-AzVM](/powershell/module/Az.compute/new-Azvm)ile sanal makineyi oluşturun.

Sanal makineleri oluşturmak için aşağıdaki kod örneğini çalıştırdığınızda Azure sizden kimlik bilgilerini ister. Kullanıcı adı için *azureuser* ve parola için *Azure123456!* parola için:
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP yapılandırmaları ve ön uç bağlantı noktası oluşturma

1. Uygulama ağ geçidiyle oluşturduğunuz alt ağı ilişkilendiren yapılandırmayı oluşturmak için [New-Azapplicationgatewayıpıp](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) ' i kullanın. 
2. Daha önce oluşturduğunuz genel IP adresini uygulama ağ geçidine atayan yapılandırmayı oluşturmak için [New-Azapplicationgatewayfrontendıpconfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) komutunu kullanın. 
3. Application Gateway 'e erişmek için 80 numaralı bağlantı noktasını atamak üzere [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) komutunu kullanın.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>Arka uç havuzunu oluşturma

1. Application Gateway için arka uç havuzu oluşturmak üzere [New-Azapplicationgatewaybackendadddresspool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) komutunu kullanın. 
2. [Yeni-AzApplicationGatewayBackendHttpSetting](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsetting)ile arka uç havuzu ayarlarını yapılandırın.

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Dinleyiciyi oluşturma ve kural ekleme

Azure, arka uç havuzuna uygun trafiği yönlendirmek için Application Gateway 'i etkinleştirmek üzere bir dinleyici gerektirir. Azure Ayrıca, bir dinleyicinin gelen trafik için hangi arka uç havuzunun kullanılacağını bilmesi için bir kural gerektirir. 

1. [Yeni-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) kullanarak daha önce oluşturduğunuz ön uç yapılandırması ve ön uç bağlantı noktasıyla bir dinleyici oluşturun. 
2. *Rule1*adlı bir kural oluşturmak için [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) kullanın. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

Gerekli destekleyici kaynakları oluşturduğunuza göre, uygulama ağ geçidini oluşturun:

1. Application Gateway için parametreleri belirtmek üzere [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) kullanın.
2. Application Gateway oluşturmak için [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) komutunu kullanın.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama ağ geçidini oluşturmak için IIS gerekli olmasa da, Azure 'un uygulama ağ geçidini başarıyla oluşturup oluşturmadığını doğrulamak için bu hızlı başlangıçta yüklediniz. Uygulama ağ geçidini test etmek için IIS kullanın:

1. Uygulama ağ geçidinin genel IP adresini almak için [Get-Azpublicıpaddress](/powershell/module/Az.network/get-Azpublicipaddress) komutunu çalıştırın. 
2. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. Tarayıcıyı yenilediğinizde, sanal makinenin adını görmeniz gerekir. Geçerli bir yanıt, uygulama ağ geçidinin başarıyla oluşturulduğunu ve arka uca başarıyla bağlanabildiğini doğrular.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Uygulama ağ geçidini test etme](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulama ağ geçidiyle oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu kaldırın. Kaynak grubunu kaldırarak, uygulama ağ geçidini ve ilgili tüm kaynakları da kaldırırsınız. 

Kaynak grubunu kaldırmak için, [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) cmdlet 'ini aşağıdaki gibi çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure PowerShell kullanarak bir uygulama ağ geçidi ile web trafiğini yönetme](./tutorial-manage-web-traffic-powershell.md)


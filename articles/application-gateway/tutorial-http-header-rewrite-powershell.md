---
title: Azure Application Gateway oluşturma & HTTP üstbilgilerini yeniden yazma
description: Bu makale, Azure Application Gateway oluşturma ve Azure PowerShell kullanarak HTTP üstbilgilerini yeniden yazma hakkında bilgi sağlar
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 2663c049245a7025b5948a64fc5008bb9e7dee90
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173728"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Uygulama ağ geçidi oluşturma ve HTTP üstbilgilerini yeniden yazma

Yeni [Otomatik ölçeklendirme ve bölgesel olarak yedekli uygulama ağ geçidi SKU 'su](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) oluştururken [http istek ve yanıt üst bilgilerini yeniden yazma kurallarını](rewrite-http-headers.md) yapılandırmak için Azure PowerShell kullanabilirsiniz.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
>
> * Otomatik ölçeklendirme sanal ağı oluşturma
> * Ayrılmış genel IP adresi oluşturma
> * Uygulama ağ geçidi altyapınızı ayarlama
> * Http üst bilgisi yeniden yazma kuralı yapılandırmanızı belirtin
> * Otomatik ölçeklendirmeyi belirtme
> * Uygulama ağ geçidi oluşturma
> * Uygulama ağ geçidini test etme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu makale, Azure PowerShell yerel olarak çalıştırmanızı gerektirir. Az Module Version 1.0.0 veya daha yeni bir sürümün yüklü olması gerekir. `Import-Module Az` çalıştırıp sürümü bulmak için`Get-Module Az`. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](https://docs.microsoft.com/powershell/azure/install-az-ps). PowerShell sürümünü doğruladıktan sonra, Azure ile bağlantı oluşturmak için `Login-AzAccount` komutunu çalıştırın.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kullanılabilir konumlardan birinde bir kaynak grubu oluşturun.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Otomatik ölçeklendirme uygulama ağ geçidi için bir ayrılmış alt ağa sahip bir sanal ağ oluşturun. Şu anda her ayrılmış alt ağda yalnızca bir otomatik ölçeklendirme yapan uygulama ağ geçidi dağıtılabilir.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Ayrılmış genel IP adresi oluşturma

Publicıpaddress 'in ayırma yöntemini **static**olarak belirtin. Otomatik ölçeklendirme yapan uygulama ağ geçidi VIP’si yalnızca statik olabilir. Dinamik IP’ler desteklenmez. Yalnızca standart PublicIpAddress SKU’su desteklenir.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Ayrıntıları alma

Uygulama ağ geçidinin IP yapılandırma ayrıntılarını oluşturmak için yerel bir nesnede kaynak grubunun, alt ağın ve IP 'nin ayrıntılarını alın.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Altyapıyı yapılandırma

IP yapılandırması, ön uç IP yapılandırması, arka uç havuzu, HTTP ayarları, sertifika, bağlantı noktası ve dinleyiciyi aynı biçimde mevcut standart uygulama ağ geçidine göre yapılandırın. Yeni SKU, standart SKU ile aynı nesne modelini izler.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>HTTP üst bilgisi yeniden yazma kuralı yapılandırmanızı belirtin

HTTP üstbilgilerini yeniden yazmak için gereken yeni nesneleri yapılandırın:

- **Requestheaderconfiguration**: Bu nesne, yeniden yazmayı düşündüğünüz istek üst bilgisi alanlarını ve özgün üstbilgilerin yeniden yazılması gereken yeni değeri belirtmek için kullanılır.
- **Responseheaderconfiguration**: Bu nesne, yeniden yazmayı düşündüğünüz yanıt üst bilgisi alanlarını ve özgün üstbilgilerin yeniden yazılması gereken yeni değeri belirtmek için kullanılır.
- **Actionset**: Bu nesne, yukarıda belirtilen istek ve yanıt üst bilgilerinin yapılandırmasını içerir. 
- **Yeniden Writerule**: Bu nesne yukarıda belirtilen tüm *actionsets gruplarını* içerir. 
- **Rewriterutaset**-bu nesne tüm *yeniden writerules* 'leri içerir ve bir istek yönlendirme kuralına (temel veya yol tabanlı) eklenmelidir.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Yönlendirme kuralını belirtin

İstek yönlendirme kuralı oluşturun. Oluşturulduktan sonra, bu yeniden yazma yapılandırması, kaynak dinleyicisine yönlendirme kuralı aracılığıyla eklenir. Temel bir yönlendirme kuralı kullanırken, üst bilgi yeniden yazma yapılandırması bir kaynak dinleyicisi ile ilişkilendirilir ve genel üst bilgi yeniden yazma işlemi olur. Yol tabanlı bir yönlendirme kuralı kullanıldığında, üstbilgi yeniden yazma yapılandırması URL yol eşlemesinde tanımlanmıştır. Bu nedenle, yalnızca bir sitenin belirli yol alanı için geçerlidir. Aşağıda temel bir yönlendirme kuralı oluşturulur ve yeniden yazma kuralı kümesi iliştirilir.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Otomatik ölçeklendirmeyi belirtme

Artık uygulama ağ geçidi için otomatik ölçeklendirme yapılandırmasını belirtebilirsiniz. İki otomatik ölçeklendirme yapılandırması türü desteklenir:

* **Sabit kapasite modu**. Bu modda, uygulama ağ geçidi otomatik ölçeklendirme yapmaz ve sabit bir Ölçek Birimi kapasitesinde çalışır.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Otomatik ölçeklendirme modu**. Bu modda, uygulama ağ geçidi uygulamanın trafik desenine bağlı olarak otomatik ölçeklendirme yapar.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

Uygulama ağ geçidini oluşturun ve artıklık bölgelerini ve otomatik ölçeklendirme yapılandırmasını ekleyin.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama ağ geçidinin genel IP adresini almak için Get-Azpublicıpaddress komutunu kullanın. Genel IP adresini veya DNS adını kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Kaynakları temizleme

İlk olarak, uygulama ağ geçidiyle oluşturulan kaynakları araştırın. Daha sonra, artık gerekli olmadığında, kaynak grubunu, uygulama ağ geçidini ve tüm ilgili kaynakları kaldırmak için `Remove-AzResourceGroup` komutunu kullanabilirsiniz.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Sonraki adımlar

- [URL yolu tabanlı yönlendirme kuralları ile bir uygulama ağ geçidi oluşturma](./tutorial-url-route-powershell.md)

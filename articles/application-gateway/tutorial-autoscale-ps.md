---
title: 'Öğretici: Web uygulaması erişimini geliştirme-Azure Application Gateway'
description: Bu öğreticide, Azure PowerShell kullanarak, ayrılmış IP adresine sahip, bir otomatik ölçeklendirme, bölgesel olarak yedekli uygulama ağ geçidi oluşturma hakkında bilgi edinin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8196267ff7a71fb3910848fd0fef11a40a3c1c32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869950"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Öğretici: Web uygulaması erişimini geliştiren bir uygulama ağ geçidi oluşturma

Web uygulaması erişimini iyileştirmeye yönelik bir BT yöneticisiyseniz, uygulama ağ geçidinizin, müşteri taleplerine göre ölçeklendirilmesi ve birden çok kullanılabilirlik alanını yaymak için iyileştirebilirler. Bu öğretici, Azure Application Gateway özelliklerini yapılandırmanıza yardımcı olur: otomatik ölçeklendirme, bölge artıklığı ve ayrılmış VIP 'ler (statik IP). Azure PowerShell cmdlet 'lerini ve Azure Resource Manager dağıtım modelini kullanarak sorunu çözebilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Otomatik olarak imzalanan sertifika oluşturma
> * Otomatik ölçeklendirme sanal ağı oluşturma
> * Ayrılmış genel IP adresi oluşturma
> * Uygulama ağ geçidi altyapınızı ayarlama
> * Otomatik ölçeklendirmeyi belirtme
> * Uygulama ağ geçidi oluşturma
> * Uygulama ağ geçidini test etme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğretici, yönetici Azure PowerShell oturumunu yerel olarak çalıştırmanızı gerektirir. Azure PowerShell Module sürümü 1.0.0 veya daha yeni bir sürümün yüklü olması gerekir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell sürümünü doğruladıktan sonra, Azure ile bağlantı oluşturmak için `Connect-AzAccount` komutunu çalıştırın.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kullanılabilir konumlardan birinde bir kaynak grubu oluşturun.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

Üretim sırasında kullanım için, güvenilen bir sağlayıcı tarafından imzalanan geçerli bir sertifikayı içeri aktarmalısınız. Bu öğretici için [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) komutunu kullanarak otomatik olarak imzalanan bir sertifika oluşturursunuz. Sertifikadan pfx dosyası dışarı aktarmak için döndürülen Parmak izi ile [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) komutunu kullanabilirsiniz.

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

Pfx dosyasını oluşturmak için parmak izini kullanın. *\<password>* İstediğiniz parolayla değiştirin:

```powershell
$pwd = ConvertTo-SecureString -String "<password>" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
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

Publicıpaddress 'in ayırma yöntemini **static** olarak belirtin. Otomatik ölçeklendirme yapan uygulama ağ geçidi VIP’si yalnızca statik olabilir. Dinamik IP’ler desteklenmez. Yalnızca standart PublicIpAddress SKU’su desteklenir.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard -Zone 1,2,3
```

## <a name="retrieve-details"></a>Ayrıntıları alma

Uygulama ağ geçidinin IP yapılandırma ayrıntılarını oluşturmak için yerel bir nesnede kaynak grubunun, alt ağın ve IP 'nin ayrıntılarını alın.

```azurepowershell
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="create-web-apps"></a>Web uygulamaları oluşturma

Arka uç havuzu için iki Web uygulaması yapılandırın. *\<site1-name>* Ve ' i *\<site-2-name>* etki alanındaki benzersiz adlarla değiştirin `azurewebsites.net` .

```azurepowershell
New-AzAppServicePlan -ResourceGroupName $rg -Name "ASP-01"  -Location $location -Tier Basic `
   -NumberofWorkers 2 -WorkerSize Small
New-AzWebApp -ResourceGroupName $rg -Name <site1-name> -Location $location -AppServicePlan ASP-01
New-AzWebApp -ResourceGroupName $rg -Name <site2-name> -Location $location -AppServicePlan ASP-01
```

## <a name="configure-the-infrastructure"></a>Altyapıyı yapılandırma

IP yapılandırması, ön uç IP yapılandırması, arka uç havuzu, HTTP ayarları, sertifika, bağlantı noktası, dinleyici ve kuralı aynı biçimde varolan standart uygulama ağ geçidine göre yapılandırın. Yeni SKU, standart SKU ile aynı nesne modelini izler.

$Pool değişken tanımındaki iki Web uygulamanızı FQDN 'larınızı (örneğin: `mywebapp.azurewebsites.net` ) değiştirin.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses <your first web app FQDN>, <your second web app FQDN>
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled -PickHostNameFromBackendAddress
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Otomatik ölçeklendirmeyi belirtme

Artık uygulama ağ geçidi için otomatik ölçeklendirme yapılandırmasını belirtebilirsiniz. 

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
Bu modda, uygulama ağ geçidi uygulamanın trafik desenine bağlı olarak otomatik ölçeklendirme yapar.

## <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

Uygulama ağ geçidini oluşturun ve artıklık bölgelerini ve otomatik ölçeklendirme yapılandırmasını ekleyin.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama ağ geçidinin genel IP adresini almak için Get-AzPublicIPAddress kullanın. Genel IP adresini veya DNS adını kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.

```azurepowershell
$pip = Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
$pip.IpAddress
```


## <a name="clean-up-resources"></a>Kaynakları temizleme

İlk olarak, uygulama ağ geçidiyle oluşturulan kaynakları araştırın. Ardından, artık gerekli olmadığında, `Remove-AzResourceGroup` komutunu kullanarak kaynak grubunu, uygulama ağ geçidini ve tüm ilgili kaynakları kaldırabilirsiniz.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [URL yolu tabanlı yönlendirme kuralları ile bir uygulama ağ geçidi oluşturma](./tutorial-url-route-powershell.md)

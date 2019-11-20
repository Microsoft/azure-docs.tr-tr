---
title: Application Gateway ile sanal ağda Azure API Management kullanma | Microsoft Docs
description: Azure API Management 'yi ön uç olarak Application Gateway (WAF) ile Iç sanal ağda ayarlama ve yapılandırma hakkında bilgi edinin
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: d1ab7089ba76890488aa73d03e0fd9fc8efbe4d5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176749"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Application Gateway ile iç VNET 'te API Management tümleştirme

## <a name="overview"></a> Genel bakış

API Management hizmeti, Sanal Ağa gelen iç modunda yapılandırılabilir ve bu, yalnızca sanal ağ içinden erişilebilir hale gelir. Azure Application Gateway, katman 7 yük dengeleyici sağlayan bir PAAS hizmetidir. Bir ters proxy hizmeti işlevi görür ve bir Web uygulaması güvenlik duvarı (WAF) teklifi arasında sağlar.

İç VNET 'te sağlanan API Management Application Gateway ön uç ile birleştirmek aşağıdaki senaryolara izin vermez:

* Hem iç tüketicilere hem de dış tüketicilere göre tüketim için aynı API Management kaynağını kullanın.
* Tek bir API Management kaynağı kullanın ve dış tüketiciler için kullanılabilir API Management tanımlanmış API 'lerin bir alt kümesine sahip olmanız gerekir.
* Genel Internet 'ten API Management erişimi açmak ve kapatmak için bir anahtar değiştirme yöntemi sağlayın.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalede açıklanan adımları izlemek için, şunları yapmanız gerekir:

* Etkin bir Azure aboneliği.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Sertifikalar-API ana bilgisayar adının PFX ve cer ve geliştirici portalının konak adı için PFX.

## <a name="scenario"></a> Senaryo

Bu makalede hem iç hem de dış tüketiciler için tek bir API Management hizmetinin nasıl kullanılacağı ele alınmaktadır ve hem şirket içi hem de bulut API 'Lerinde tek bir ön uç işlevi görür. Ayrıca, Application Gateway ' de kullanılabilen yönlendirme işlevlerini kullanarak, API 'nizin yalnızca bir alt kümesini kullanıma sunma (yeşil renkle vurgulandığı örnekte) görürsünüz.

İlk kurulum örneğinde, tüm API 'leriniz yalnızca sanal ağınız içinden yönetilir. Dahili tüketiciler (Turuncu renkle vurgulanır), tüm iç ve dış API 'lerinize erişebilir. Trafik hiçbir şekilde internet 'e gitmez. Yüksek performanslı bağlantı hızlı rota devreleri aracılığıyla dağıtılır.

![URL yolu](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a> Başlamadan önce

* Azure PowerShell’in en yeni sürümünü kullandığınızdan emin olun. Yükleme [Azure PowerShell](/powershell/azure/install-az-ps)yükleme yönergelerine bakın. 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>API Management ve Application Gateway arasında bir tümleştirme oluşturmak için ne gerekir?

* **Arka uç sunucu havuzu:** Bu, API Management hizmetinin iç sanal IP adresidir.
* **Arka uç sunucu havuzu ayarları**: Her havuzun bağlantı noktası, protokol ve tanımlama bilgisi temelli benzeşim gibi ayarları vardır. Bu ayarlar, havuzdaki tüm sunuculara uygulanır.
* **Ön uç bağlantı noktası:** Bu, uygulama ağ geçidinde açılan genel bağlantı noktasıdır. Giden trafik, arka uç sunucularından birine yönlendirilir.
* **Dinleyici:** Dinleyicide bir ön uç bağlantı noktası, bir protokol (Http veya Https, bu değerler büyük/küçük harfe duyarlıdır) ve SSL sertifika adı (SSL yük boşaltımı yapılandırılıyorsa) vardır.
* **Kural:** Kural bir dinleyiciyi arka uç sunucu havuzuna bağlar.
* **Özel durum araştırması:** Application Gateway, varsayılan olarak, BackendAddressPool içindeki hangi sunucuların etkin olduğunu anlamak için IP adresi tabanlı araştırmaları kullanır. API Management hizmeti yalnızca doğru ana bilgisayar üst bilgisine sahip isteklere yanıt verir, bu nedenle varsayılan yoklamalar başarısız olur. Application Gateway 'in hizmetin etkin olduğunu ve istekleri iletmeli olduğunu belirlemesine yardımcı olmak için özel bir sistem durumu araştırması tanımlanmalıdır.
* **Özel etki alanı sertifikaları:** API Management internet 'ten erişmek için, ana bilgisayar adının bir CNAME eşlemesini Application Gateway ön uç DNS adına oluşturmanız gerekir. Bu, API Management iletilen Application Gateway ana bilgisayar başlığı ve sertifikasının bir APıM 'in geçerli olarak tanıyabilmesini sağlar. Bu örnekte, arka uç ve geliştirici portalı için iki sertifika kullanacağız.  

## <a name="overview-steps"></a> API Management ve Application Gateway tümleştirmek için gereken adımlar

1. Resource Manager için kaynak grubu oluşturun.
2. Application Gateway için bir sanal ağ, alt ağ ve genel IP oluşturun. API Management için başka bir alt ağ oluşturun.
3. Yukarıda oluşturulan VNET alt ağı içinde bir API Management hizmeti oluşturun ve Iç modu kullandığınızdan emin olun.
4. API Management hizmetinde özel bir etki alanı adı ayarlayın.
5. Application Gateway yapılandırma nesnesi oluşturun.
6. Application Gateway kaynağı oluşturun.
7. Application Gateway genel DNS adından API Management proxy ana bilgisayar adına bir CNAME oluşturun.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Geliştirici portalını Application Gateway dışarıdan gösterme

Bu kılavuzda, Application Gateway aracılığıyla **Geliştirici Portalını** dış izleyiciler için de kullanıma sunacağız. Geliştirici portalının dinleyicisini, araştırmasını, ayarlarını ve kurallarını oluşturmak için ek adımlar gerektirir. Tüm ayrıntılar ilgili adımlarda sunulmaktadır.

> [!WARNING]
> Azure AD veya üçüncü taraf kimlik doğrulaması kullanıyorsanız, lütfen Application Gateway için [tanımlama bilgisi tabanlı oturum benzeşimi](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) özelliğini etkinleştirin.

> [!WARNING]
> Application Gateway WAF 'nin Geliştirici Portalında Openapı belirtiminin indirilmesini bozmasını engellemek için, `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`güvenlik duvarı kuralını devre dışı bırakmanız gerekir.

## <a name="create-a-resource-group-for-resource-manager"></a>Resource Manager için kaynak grubu oluşturun

### <a name="step-1"></a>Adım 1

Azure'da oturum açma

```powershell
Connect-AzAccount
```

Kimlik bilgilerinizle kimlik doğrulaması yapın.

### <a name="step-2"></a>Adım 2

İstediğiniz aboneliği seçin.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Adım 3

Bir kaynak grubu oluşturun (mevcut bir kaynak grubu kullanıyorsanız bu adımı atlayın).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager, tüm kaynak gruplarının bir konum belirtmesini gerektirir. Bu, kaynak grubunda kaynaklar için varsayılan konum olarak kullanılır. Uygulama ağ geçidi oluşturmak için tüm komutların aynı kaynak grubunu kullanmasını sağlayın.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Uygulama ağ geçidi için bir sanal ağ ve alt ağ oluşturma

Aşağıdaki örnek, Kaynak Yöneticisi kullanarak nasıl sanal ağ oluşturulacağını gösterir.

### <a name="step-1"></a>Adım 1

Bir sanal ağ oluştururken Application Gateway için kullanılacak alt ağ değişkenine 10.0.0.0/24 adres aralığını atayın.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Adım 2

Bir sanal ağ oluştururken API Management için kullanılacak alt ağ değişkenine 10.0.1.0/24 adres aralığını atayın.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Adım 3

Batı ABD bölgesi için **APIM-appGw-RG** kaynak grubunda **appgwvnet** adlı bir sanal ağ oluşturun. 10.0.0.0/24 ve 10.0.1.0/24 alt ağları ile 10.0.0.0/16 önekini kullanın.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4\. Adım

Sonraki adımlar için bir alt ağ değişkeni atama

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>İç modda yapılandırılmış bir VNET içinde API Management hizmeti oluşturma

Aşağıdaki örnek, yalnızca iç erişim için yapılandırılmış bir sanal ağda API Management bir hizmetin nasıl oluşturulacağını gösterir.

### <a name="step-1"></a>Adım 1

Yukarıda oluşturulan alt ağ $apimsubnetdata kullanarak API Management bir sanal ağ nesnesi oluşturun.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Adım 2

Sanal ağ içinde bir API Management hizmeti oluşturun.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Yukarıdaki komut başarılı olduktan sonra, bu [hizmete erişmek için Iç VNET API Management hizmetine erişmek için gereken DNS yapılandırmasına](api-management-using-with-internal-vnet.md#apim-dns-configuration) başvurun. Bu adım, bir saatten fazla sürebilir.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>API Management bir özel etki alanı adı ayarlama

### <a name="step-1"></a>Adım 1

Aşağıdaki değişkenleri, etki alanları için özel anahtarlarla sertifikaların ayrıntılarıyla başlatın. Bu örnekte `api.contoso.net` ve `portal.contoso.net`kullanacağız.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Adım 2

Proxy ve Portal için ana bilgisayar adı yapılandırma nesneleri oluşturun ve ayarlayın.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Eski geliştirici portalı bağlantısını yapılandırmak için `-HostnameType DeveloperPortal` `-HostnameType Portal`ile değiştirmeniz gerekir.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Ön uç yapılandırma için genel bir IP adresi oluşturun

Kaynak grubunda genel bir IP kaynağı **publicıp01** oluşturun.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Hizmet başlatıldığında uygulama ağ geçidine bir IP adresi atanır.

## <a name="create-application-gateway-configuration"></a>Uygulama ağ geçidi yapılandırması oluştur

Tüm yapılandırma öğeleri, uygulama ağ geçidi oluşturulmadan önce ayarlanmalıdır. Aşağıdaki adımlar uygulama ağ geçidi kaynağı için gerekli yapılandırma öğelerini oluşturur.

### <a name="step-1"></a>Adım 1

**gatewayIP01** adlı bir uygulama ağ geçidi IP yapılandırması oluşturun. Application Gateway başladığında, yapılandırılan alt ağdan bir IP adresi alır ve ağ trafiğini arka uç IP havuzundaki IP adreslerine yönlendirir. Her örneğin bir IP adresi aldığını göz önünde bulundurun.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Adım 2

Genel IP uç noktası için ön uç IP bağlantı noktasını yapılandırın. Bu bağlantı noktası, son kullanıcıların bağlanacağı bağlantı noktasıdır.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Adım 3

Ön uç IP’sini genel IP uç noktası ile yapılandırın.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4\. Adım

İle yapılan trafiğin şifresini çözmek ve yeniden şifrelemek için kullanılacak Application Gateway için sertifikaları yapılandırın.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>5\. Adım

Application Gateway için HTTP dinleyicileri oluşturun. Ön uç IP yapılandırması, bağlantı noktası ve SSL sertifikalarını bunlara atayın.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>6\. Adım

API Management hizmeti `ContosoApi` proxy etki alanı uç noktası için özel yoklamalar oluşturun. Yol `/status-0123456789abcdef`, tüm API Management hizmetlerinde barındırılan varsayılan bir sistem durumu uç noktasıdır. SSL sertifikasıyla güvenli hale getirmek için `api.contoso.net` özel bir araştırma ana bilgisayar adı olarak ayarlayın.

> [!NOTE]
> Ana bilgisayar adı `contosoapi.azure-api.net`, genel Azure 'da `contosoapi` adlı bir hizmet oluşturulduğunda yapılandırılan varsayılan proxy ana bilgisayar adıdır.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7\. Adım

SSL özellikli arka uç havuzu kaynaklarında kullanılacak sertifikayı karşıya yükleyin. Bu, yukarıda 4. adımda girdiğiniz sertifikadır.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>8\. Adım

Application Gateway için HTTP arka uç ayarlarını yapılandırın. Bu, arka uç isteği için bir zaman aşımı sınırı ayarlamayı, sonrasında iptal edilmeleri içerir. Bu değer, araştırma zaman aşımı durumundan farklıdır.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9\. Adım

Yukarıda oluşturulan API Management hizmetinin iç sanal IP adresiyle **apımarka uç** adlı bir arka uç IP adresi havuzu yapılandırın.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>10. adım

Temel yönlendirmeyi kullanmak için Application Gateway kurallar oluşturun.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Geliştirici portalının belirli sayfalarına erişimi kısıtlamak için-RuleType ve Routing öğesini değiştirin.

### <a name="step-11"></a>Adım 11

Application Gateway örnek sayısını ve boyutunu yapılandırın. Bu örnekte, API Management kaynağının güvenliği arttığı için [WAF SKU 'su](../application-gateway/application-gateway-webapplicationfirewall-overview.md) kullanıyoruz.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>12. adım

WAF 'yi "önleme" modunda olacak şekilde yapılandırın.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Application Gateway oluştur

Yukarıdaki adımlardan tüm yapılandırma nesneleriyle bir Application Gateway oluşturun.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME API Management proxy ana bilgisayar adı Application Gateway kaynağının genel DNS adına

Ağ geçidi oluşturulduktan sonraki adım, iletişim için ön uç yapılandırması yapmaktır. Genel IP kullanırken Application Gateway dinamik olarak atanan bir DNS adı gerektirir ve bu, kullanılması kolay olmayabilir.

Application Gateway DNS adı, APıM proxy ana bilgisayar adını (örneğin, Yukarıdaki örneklerde `api.contoso.net`) bu DNS adına işaret eden bir CNAME kaydı oluşturmak için kullanılmalıdır. Ön uç IP CNAME kaydını yapılandırmak için, Application Gateway ayrıntılarını ve ilgili IP/DNS adını Publicıpaddress öğesini kullanarak alın. Ağ geçidinin yeniden başlatılması sırasında VIP değişebileceğinizden, A kayıtlarının kullanılması önerilmez.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a> Özet
Bir sanal ağda yapılandırılan Azure API Management, şirket içinde veya bulutta barındırılıp barındırılmayacağı tüm yapılandırılmış API 'Ler için tek bir ağ geçidi arabirimi sağlar. Application Gateway API Management ile tümleştirmek, belirli API 'Lerin Internet üzerinde erişilebilir olmasını seçmeli olarak etkinleştirme esnekliği sağlar ve bir Web uygulaması güvenlik duvarını API Management örneğiniz için ön uç olarak sağlar.

## <a name="next-steps"></a> Sonraki adımlar
* Azure Application Gateway hakkında daha fazla bilgi
  * [Application Gateway genel bakış](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway Web uygulaması güvenlik duvarı](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Yol tabanlı yönlendirme kullanarak Application Gateway](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* API Management ve sanal ağlar hakkında daha fazla bilgi edinin
  * [Yalnızca VNET içinde kullanılabilir API Management kullanma](api-management-using-with-internal-vnet.md)
  * [VNET 'te API Management kullanma](api-management-using-with-vnet.md)

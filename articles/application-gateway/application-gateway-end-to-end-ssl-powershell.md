---
title: Azure Application Gateway ile uçtan uca SSL 'yi yapılandırma
description: Bu makalede, PowerShell kullanarak Azure Application Gateway ile uçtan uca SSL 'nin nasıl yapılandırılacağı açıklanır.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 7ba273cddb6cf41872c4db1c34560c104b992787
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286460"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>PowerShell ile Application Gateway kullanarak uçtan uca SSL 'yi yapılandırma

## <a name="overview"></a>Genel Bakış

Azure Application Gateway trafiğin uçtan uca şifrelenmesini destekler. Application Gateway, uygulama ağ geçidinde SSL bağlantısını sonlandırır. Ağ Geçidi daha sonra trafiğe yönlendirme kuralları uygular, paketi yeniden şifreler ve tanımlanan yönlendirme kurallarına göre paketi uygun arka uç sunucusuna iletir. Web sunucusundan alınan herhangi bir yanıt, son kullanıcıya dönerken aynı süreci izler.

Application Gateway özel SSL seçeneklerini tanımlamayı destekler. Ayrıca, şu protokol sürümlerinin devre dışı bırakılmasını destekler: **tlsv 1.0**, **Tlsv 1.1**ve **tlsv 1.2**, hangi şifre paketlerinin kullanılacağını ve tercih sırasını tanımlar. Yapılandırılabilir SSL seçenekleri hakkında daha fazla bilgi edinmek için bkz. [SSL ilkesine genel bakış](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2,0 ve SSL 3,0 varsayılan olarak devre dışıdır ve etkinleştirilemez. Bunlar güvenli değil olarak kabul edilir ve Application Gateway kullanılamaz.

![Senaryo görüntüsü][scenario]

## <a name="scenario"></a>Senaryo

Bu senaryoda, PowerShell ile uçtan uca SSL kullanarak uygulama ağ geçidi oluşturmayı öğreneceksiniz.

Bu senaryo şunları olacaktır:

* **Appgw-RG**adlı bir kaynak grubu oluşturun.
* **10.0.0.0/16**adres alanı ile **appgwvnet** adlı bir sanal ağ oluşturun.
* **Appgwsubnet** ve **appsubnet**adlı iki alt ağ oluşturun.
* SSL protokolü sürümlerini ve şifre paketlerini sınırlayan uçtan uca SSL şifrelemesini destekleyen küçük bir uygulama ağ geçidi oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bir Application Gateway ile uçtan uca SSL yapılandırmak için, ağ geçidi için bir sertifika gerekir ve arka uç sunucular için sertifikalar gerekir. Ağ Geçidi sertifikası, her SSL protokol belirtimine göre bir simetrik anahtar türetmek için kullanılır. Simetrik anahtar daha sonra, ağ geçidine gönderilen trafiğin şifrelemesini ve şifresini çözün. Ağ Geçidi sertifikasının kişisel bilgi değişimi (PFX) biçiminde olması gerekir. Bu dosya biçimi, trafiğin şifrelemesini ve şifresini çözmeyi gerçekleştirmek üzere uygulama ağ geçidi için gereken özel anahtarı dışarı aktarma olanağı sağlar.

Uçtan uca SSL şifrelemesi için, arka uca uygulama ağ geçidi tarafından açıkça izin verilmelidir. Arka uç sunucularının genel sertifikasını uygulama ağ geçidine yükleyin. Sertifikayı eklemek, Application Gateway 'in yalnızca bilinen arka uç örnekleriyle iletişim kuracağını sağlar. Bu, uçtan uca iletişimin güvenliğini sağlar.

Yapılandırma işlemi aşağıdaki bölümlerde açıklanmıştır.

## <a name="create-the-resource-group"></a>Kaynak grubunu oluşturma

Bu bölümde, uygulama ağ geçidini içeren bir kaynak grubu oluşturma işlemi adım adım açıklanmaktadır.

1. Azure hesabınızda oturum açın.

   ```powershell
   Connect-AzAccount
   ```

2. Bu senaryo için kullanılacak aboneliği seçin.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Bir kaynak grubu oluşturun. (Mevcut bir kaynak grubunu kullanıyorsanız bu adımı atlayın.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Uygulama ağ geçidi için bir sanal ağ ve bir alt ağ oluşturun

Aşağıdaki örnek, bir sanal ağ ve iki alt ağ oluşturur. Uygulama ağ geçidini tutmak için bir alt ağ kullanılır. Diğer alt ağ, Web uygulamasını barındıran arka uçlar için kullanılır.

1. Uygulama ağ geçidi için kullanılacak alt ağ için bir adres aralığı atayın.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Bir uygulama ağ geçidi için yapılandırılmış alt ağlar düzgün şekilde boyutlandırılmalıdır. Uygulama ağ geçidi, en fazla 10 örnek için yapılandırılabilir. Her örnek alt ağdan bir IP adresi alır. Bir alt ağ için çok küçük bir uygulama ağ geçidinin ölçeğini olumsuz etkileyebilir.
   >

2. Arka uç adres havuzu için kullanılacak bir adres aralığı atayın.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Önceki adımlarda tanımlanan alt ağlarla bir sanal ağ oluşturun.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Aşağıdaki adımlarda kullanılacak sanal ağ kaynağını ve alt ağ kaynaklarını alın.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Ön uç yapılandırma için genel bir IP adresi oluşturun

Uygulama ağ geçidi için kullanılacak bir genel IP kaynağı oluşturun. Bu genel IP adresi, izleyen adımlardan birinde kullanılır.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway, tanımlı bir etki alanı etiketiyle oluşturulan genel IP adresinin kullanımını desteklemez. Yalnızca dinamik olarak oluşturulan bir etki alanı etiketine sahip bir genel IP adresi desteklenir. Uygulama ağ geçidi için kolay bir DNS adı gerekliyse, bir CNAME kaydını diğer ad olarak kullanmanızı öneririz.

## <a name="create-an-application-gateway-configuration-object"></a>Uygulama ağ geçidi yapılandırma nesnesi oluşturun

Tüm yapılandırma öğeleri, uygulama ağ geçidi oluşturulmadan önce ayarlanır. Aşağıdaki adımlar uygulama ağ geçidi kaynağı için gerekli yapılandırma öğelerini oluşturur.

1. Uygulama ağ geçidi IP yapılandırması oluşturun. Bu ayar, uygulama ağ geçidinin kullandığı alt ağları yapılandırır. Application Gateway başladığında, yapılandırılan alt ağdan bir IP adresi alır ve ağ trafiğini arka uç IP havuzundaki IP adreslerine yönlendirir. Her örneğin bir IP adresi aldığını göz önünde bulundurun.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Ön uç IP yapılandırması oluşturun. Bu ayar özel veya genel bir IP adresini uygulama ağ geçidinin ön ucuna eşler. Aşağıdaki adım, önceki adımdaki genel IP adresini ön uç IP yapılandırması ile ilişkilendirir.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Arka uç IP adresi havuzunu arka uç Web sunucularının IP adresleriyle yapılandırın. Bu IP adresleri ön uç IP uç noktasından gelen ağ trafiğinin yönlendirildiği IP adresleridir. Örnekteki IP adreslerini kendi uygulamanızın IP adresi uç noktalarınızla değiştirin.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Tam etki alanı adı (FQDN) Ayrıca, arka uç sunucuları için bir IP adresi yerine kullanılacak geçerli bir değerdir. **-Backendfqdn** anahtarını kullanarak etkinleştirin. 

4. Genel IP uç noktası için ön uç IP bağlantı noktasını yapılandırın. Bu bağlantı noktası, son kullanıcıların bağlanacağı bağlantı noktasıdır.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Uygulama ağ geçidi için sertifikayı yapılandırın. Bu sertifika, uygulama ağ geçidinde trafiğin şifresini çözmek ve yeniden şifrelemek için kullanılır.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Bu örnek, SSL bağlantısı için kullanılan sertifikayı yapılandırır. Sertifikanın. pfx biçiminde olması gerekir ve parola 4 ile 12 karakter arasında olmalıdır.

6. Uygulama ağ geçidi için HTTP dinleyicisi oluşturun. Kullanılacak ön uç IP yapılandırmasını, bağlantı noktasını ve SSL sertifikasını atayın.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. SSL özellikli arka uç havuzu kaynaklarında kullanılacak sertifikayı karşıya yükleyin.

   > [!NOTE]
   > Varsayılan araştırma, arka ucun IP adresinde *varsayılan* SSL bağlamasındaki ortak anahtarı alır ve aldığı ortak anahtar değerini burada sağladığınız ortak anahtar değerine karşılaştırır. 
   > 
   > Arka uçta ana bilgisayar üst bilgileri ve Sunucu Adı Belirtme (SNı) kullanıyorsanız, alınan ortak anahtar trafiğin akabileceği hedeflenen site olmayabilir. Şüpheniz varsa, *varsayılan* SSL bağlaması için hangi sertifikanın kullanıldığını doğrulamak üzere arka uç sunucularındaki https://127.0.0.1/ adresini ziyaret edin. Bu bölümdeki bu istekten ortak anahtarı kullanın. HTTPS bağlamalarında ana başlıklar ve SNı kullanıyorsanız ve arka uç sunucularında bir el ile tarayıcı isteğinden https://127.0.0.1/ ' a bir yanıt ve sertifika almazsanız, bunlar üzerinde varsayılan bir SSL bağlaması ayarlamanız gerekir. Bunu yapmazsanız, yoklamalar başarısız olur ve arka uç daha fazla listede değildir.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Önceki adımda belirtilen sertifika, arka uçta mevcut. PFX sertifikasının ortak anahtarı olmalıdır. Arka uç sunucusunda yüklü sertifikayı (kök sertifikayı değil) talep, kanıt ve düşünme (CER) biçiminde dışarı aktarın ve bu adımda kullanın. Bu adım, Application Gateway ile arka ucu beyaz listeler.

   Application Gateway v2 SKU 'SU kullanıyorsanız, kimlik doğrulama sertifikası yerine güvenilen bir kök sertifika oluşturun. Daha fazla bilgi için bkz. [Application Gateway ile uçtan uca SSL 'ye genel bakış](ssl-overview.md#end-to-end-ssl-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Uygulama ağ geçidi arka ucu için HTTP ayarlarını yapılandırın. Önceki adımda karşıya yüklenen sertifikayı HTTP ayarlarına atayın.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Application Gateway v2 SKU 'SU için aşağıdaki komutu kullanın:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Yük dengeleyici davranışını yapılandıran bir yük dengeleyici yönlendirme kuralı oluşturun. Bu örnekte, temel bir hepsini bir kez deneme kuralı oluşturulur.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Uygulama ağ geçidinin örnek boyutunu yapılandırın. Kullanılabilir boyutlar **Standart @ no__t-1Küçük**, **Standart @ No__t-3medium**ve **Standart @ no__t-5büyük**' dir.  Kapasite için, kullanılabilir değerler **1** ile **10**arası değerlerdir.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Test amaçlı olarak 1 örnek sayısı seçilebilir. İki örnek altındaki herhangi bir örnek sayısının SLA tarafından kapsanmayan ve bu nedenle önerilmediğinden emin olmak önemlidir. Küçük ağ geçitleri geliştirme testi için kullanılır ve üretim amacıyla desteklenmez.

11. Uygulama ağ geçidinde kullanılacak SSL ilkesini yapılandırın. Application Gateway, SSL protokolü sürümleri için en düşük sürümü ayarlama yeteneğini destekler.

    Aşağıdaki değerler tanımlanabilir protokol sürümlerinin listesidir:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    Aşağıdaki örnek, en düşük protokol sürümünü **TLSv1_2** olarak ayarlar ve **TLS @ NO__T-2ecdhe @ NO__T-3ecdsa @ no__t-4' i @ NO__T-5aes @ no__t-6128 @ NO__T-7gcm @ NO__T-8SHA256**, **TLS @ NO__T-10ecdhe @ no__t-11ECDSA @ no__t-12ile @ No ile etkinleştirilir __T-13AES @ no__t-14256 @ no__t-15GCM @ no__t-16SHA384**ve **TLS @ NO__T-18rsa @ NO__T-19with @ NO__T-20aes @ no__t-21128 @ NO__T-22gcm @ NO__T-23sha256** .

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

Yukarıdaki adımların tümünü kullanarak uygulama ağ geçidini oluşturun. Ağ geçidinin oluşturulması, çalışması uzun süren bir işlemdir.

V1 SKU 'SU için aşağıdaki komutu kullanın
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

V2 SKU 'SU için aşağıdaki komutu kullanın
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Arka uç sertifikasının geçerliliği dolmuşsa yeni bir sertifika Uygula

Arka uç sertifikasının kullanım zamanı dolmuşsa yeni bir sertifika uygulamak için bu yordamı kullanın.

1. Güncelleştirilecek uygulama ağ geçidini alın.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Sertifikanın ortak anahtarını içeren. cer dosyasındaki yeni sertifika kaynağını ekleyin ve uygulama ağ geçidinde SSL sonlandırmasına yönelik dinleyiciye eklenen sertifika da aynı olabilir.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Yeni kimlik doğrulama sertifikası nesnesini bir değişkene (TypeName: Microsoft. Azure. Commands. Network. modeller. PSApplicationGatewayAuthenticationCertificate) alın.

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Yeni sertifikayı **Backendhttp** ayarına atayın ve $AuthCert değişkeniyle birlikte başvurun. (Değiştirmek istediğiniz HTTP ayar adını belirtin.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Değişikliği uygulama ağ geçidine işleyin ve $out değişkenine dahil olan yeni yapılandırmayı geçirin.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Kullanılmayan kullanılmamış bir sertifikayı HTTP ayarlarından kaldır

Kullanılmayan bir zaman aşımına uğradı sertifikayı HTTP ayarlarından kaldırmak için bu yordamı kullanın.

1. Güncelleştirilecek uygulama ağ geçidini alın.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Kaldırmak istediğiniz kimlik doğrulama sertifikasının adını listeleyin.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Uygulama ağ geçidindeki kimlik doğrulama sertifikasını kaldırın.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Değişikliği yürütün.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Mevcut bir uygulama ağ geçidinde SSL protokolü sürümlerini sınırla

Yukarıdaki adımlar, uçtan uca SSL ile bir uygulama oluşturma ve belirli SSL protokolü sürümlerini devre dışı bırakma konusunda sizi gerçekleştirmiyor. Aşağıdaki örnek, mevcut bir uygulama ağ geçidinde bazı SSL ilkelerini devre dışı bırakır.

1. Güncelleştirilecek uygulama ağ geçidini alın.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Bir SSL ilkesi tanımlayın. Aşağıdaki örnekte, **tlsv 1.0** ve **tlsv 1.1** devre dışıdır ve şifre paketleri **TLS @ no__t-3ecdhe @ no__t-4ECDSA @ no__t-5ILE @ no__t-6AES @ no__t-7128 @ no__t-8GCM @ NO__T-9sha256**, **TLS @ no__t-11ECDHE @ no__t-12ecdsa @ no__ t-13WITH @ no__t-14AES @ no__t-15256 @ no__t-16GCM @ no__t-17SHA384**ve **TLS @ NO__T-19rsa @ NO__T-20with @ NO__T-21aes @ no__t-22128 @ NO__T-23gcm @ NO__T-24SHA256** yalnızca izin verilen tek alanlardır.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Son olarak, ağ geçidini güncelleştirin. Bu son adım uzun süredir çalışan bir görevdir. İşlem tamamlandığında, uygulama ağ geçidinde uçtan uca SSL yapılandırılır.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Uygulama Ağ Geçidi DNS adı Al

Ağ Geçidi oluşturulduktan sonra, bir sonraki adım, iletişim için ön ucu yapılandırmaktır. Application Gateway, genel IP kullanılırken dinamik olarak atanan bir DNS adı gerektirir, bu da kolay değildir. Son kullanıcıların uygulama ağ geçidine isabet açabildiğinden emin olmak için, uygulama ağ geçidinin genel uç noktasını işaret etmek üzere bir CNAME kaydı kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure 'da için özel etki alanı adı yapılandırma](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Bir diğer ad yapılandırmak için uygulama ağ geçidinin ayrıntılarını ve ilgili IP/DNS adını uygulama ağ geçidine eklenmiş **Publicıpaddress** öğesini kullanarak alın. Bu DNS adına iki Web uygulamasını işaret eden bir CNAME kaydı oluşturmak için Application Gateway 'in DNS adını kullanın. Uygulama ağ geçidinin yeniden başlatılması sırasında VIP değiştirebildiğinden, A kayıtlarının kullanımını önermeyiz.

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

Web uygulaması güvenlik duvarı ile Web uygulamalarınızın güvenliğini Application Gateway aracılığıyla sağlamlaştırma hakkında daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı 'na genel bakış](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png

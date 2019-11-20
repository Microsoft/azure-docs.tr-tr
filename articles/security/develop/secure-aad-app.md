---
title: Güvenli bir Azure AD Web uygulaması geliştirme | Microsoft Docs
description: Bu basit örnek uygulama, Azure 'da geliştirme yaparken uygulamanızı ve kuruluşunuzun güvenlik duruşunu geliştiren en iyi güvenlik uygulamalarını uygular.
keywords: yana
services: security
documentationcenter: na
author: fehase
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: v-fehase
ms.openlocfilehash: 88ef0874d760fb87700eac83c0d615be5887ddee
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159842"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Azure AD uygulaması için güvenli uygulama geliştirme
## <a name="overview"></a>Genel Bakış

Bu örnek, Azure 'da uygulama geliştirmeye yönelik güvenlik kaynaklarına bağlanan Web uygulaması ile basit bir Azure Active Directory. Uygulama, Azure 'da uygulama geliştirirken uygulamanızın ve kuruluşunuzun güvenlik duruşunuzun artırılmasına yardımcı olabilecek en iyi güvenlik uygulamalarını uygular.

Dağıtım betikleri altyapıyı ayarlar. Dağıtım betiklerini çalıştırdıktan sonra, bileşenleri ve Hizmetleri birbirine bağlamak için Azure portal el ile yapılandırma yapmanız gerekir. Bu örnek, perakende sektörde çalışan ve güvenli bir Azure altyapısıyla güvenli bir Azure Active Directory oluşturmak isteyen Azure 'da deneyimli geliştiricilere yöneliktir. 


Bu uygulamayı geliştirirken ve dağıttığınızda, nasıl yapılacağını öğreneceksiniz 
- Azure Key Vault bir örnek oluşturun, depolayın ve parolaları alın.
- Ön uç güvenlik duvarı erişimiyle yalıtılmış olarak ayrılmış olan Azure Web uygulamasını dağıtın. 
- OWASP Top 10 RuleSet kullanan bir güvenlik duvarı ile Azure Application Gateway örneği oluşturun ve yapılandırın. 
- Azure hizmetlerini kullanarak geçişte ve bekleyen verilerin şifrelenmesini etkinleştirin. 
- Karmaşıklıkları değerlendirmek için Azure ilkesi ve Güvenlik Merkezi 'ni ayarlayın. 

Bu uygulamayı geliştirip dağıttıktan sonra, açıklanan yapılandırma ve güvenlik ölçüleriyle birlikte aşağıdaki örnek Web uygulamasını ayarlamış olursunuz.

## <a name="architecture"></a>Mimari
Uygulama, üç katman içeren tipik bir n katmanlı uygulamadır. İzleme ve gizli yönetim bileşenleriyle tümleşik olan ön uç, arka uç ve veritabanı katmanı burada gösterilmektedir:

![Uygulama mimarisi](./media/secure-aad-app/architecture.png)

Bu çözüm aşağıdaki Azure hizmetlerini kullanır. Dağıtım mimarisinin ayrıntıları dağıtım mimarisi bölümünde bulunur. 

Mimari aşağıdaki bileşenlerden oluşur

- [Azure Application Gateway](../../application-gateway/index.yml). Uygulama mimarimiz için ağ geçidini ve güvenlik duvarını sağlar.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Birden çok platformda genişletilebilir bir uygulama performans yönetimi (APM) hizmeti sağlar.
- [Azure Key Vault](../../key-vault/index.yml). Uygulamanın gizli dizilerini depolar ve şifreler ve bunların çevresindeki erişim ilkelerinin oluşturulmasını yönetir.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Bulut tabanlı kimlik ve erişim yönetimi hizmeti sağlar, oturum açın ve kaynaklara erişin.
- [Azure etki alanı adı sistemi](../../dns/dns-overview.md). Etki alanını barındıracak hizmeti sağlayın.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). , Uygulamalarınızı ölçeklendirmenize ve hizmetleriniz için yüksek kullanılabilirlik oluşturmanıza olanak sağlar.
- [Azure Web App](../../app-service/overview.md).  Web uygulamalarını barındırmak için HTTP tabanlı bir hizmet sağlar.
- [Azure Güvenlik Merkezi](../../security-center/index.yml). buluttaki karma iş yükleriniz genelinde gelişmiş tehdit koruması sağlar.
- [Azure ilkesi](../../governance/policy/overview.md). , Atanan ilkelerle uyumlu olmayan kaynaklarınızı değerlendiriyor.

## <a name="threat-model"></a>Tehdit modeli
Tehdit modellemesi, işletmenizin ve uygulamanızın olası güvenlik tehditlerini tanımlama ve daha sonra uygun bir risk azaltma planının yerinde olmasını sağlama işlemidir.

Bu örnek, güvenli örnek uygulama için tehdit modellemesini uygulamak üzere [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) kullandı. Bileşenleri ve veri akışlarını diyagram oluşturarak, geliştirme sürecinde sorunları ve tehditleri erkenden ayırt edebilirsiniz. Zaman ve para, daha sonra bu kullanılarak kaydedilir.

Örnek uygulama için tehdit modeli aşağıda verilmiştir

![Tehdit modeli](./media/secure-aad-app/threat-model.png)

Tehdit modelleme aracının ürettiği bazı örnek tehditler ve olası güvenlik açıkları aşağıdaki ekran görüntüsünde gösterilmiştir. Tehdit modeli, sunulan saldırı yüzeyine genel bir bakış sunar ve geliştiricilerin sorunları nasıl azaltacağını düşündüğünü ister.

![Tehdit modeli çıkışı](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Önkoşullar
Uygulamayı çalışır duruma getirmek için şu araçları yüklemeniz gerekir:

- Uygulama kodunu değiştirmek ve görüntülemek için bir kod Düzenleyicisi. [Visual Studio Code](https://code.visualstudio.com/) açık kaynaklı bir seçenektir.
- Geliştirme bilgisayarınızda [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) .
- Sisteminizde [Git](https://git-scm.com/) . Git, kaynak kodu yerel olarak kopyalamak için kullanılır.
- [JQ](https://stedolan.github.io/jq/), JSON 'u Kullanıcı dostu bir şekilde sorgulamak IÇIN bir UNIX aracıdır.

Örnek uygulamanın kaynaklarını dağıtmak için bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliğiniz yoksa, örnek uygulamayı test etmek için [ücretsiz bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/) .

Bu araçları yükledikten sonra, uygulamayı Azure 'da dağıtmaya hazırsınız demektir.

### <a name="implementation-guidance"></a>Uygulama kılavuzu
Dağıtım betiği dört aşamaya bölünebilir bir betiktir. Her aşama, [mimari diyagramında](#architecture)olan bir Azure kaynağını dağıtır ve yapılandırır.

Dört aşama

- Azure Key Vault dağıtın.
- Azure Web Apps 'yi dağıtın.
- Web uygulaması güvenlik duvarı ile Application Gateway dağıtın.
- Dağıtılan uygulamayla bir Azure AD yapılandırın.

Her aşama, daha önce dağıtılan kaynaklardaki yapılandırmayı kullanarak bir önceki sürümünden sonra oluşturulur.

Uygulama adımlarını tamamlayabilmeniz için, [Önkoşullar](#prerequisites)altında listelenen araçları yüklediğinizden emin olun.

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault dağıt
Bu bölümde, gizli dizileri ve sertifikaları depolamak için kullanılan bir Azure Key Vault örneği oluşturup dağıtırsınız.

Dağıtımı tamamladıktan sonra, Azure üzerinde dağıtılan bir Azure Key Vault örneğiniz vardır.

PowerShell kullanarak Azure Key Vault dağıtmak için
 
1. Azure Key Vault değişkenlerini bildirin.
2. Azure Key Vault sağlayıcıyı kaydedin.
3. Örnek için kaynak grubu oluşturun.
4. Adım 3 ' te oluşturulan kaynak grubunda Azure Key Vault örneğini oluşturun.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Aşağıdaki Azure AD kullanıcısının Key Vault yönetici izinleri olacaktır
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Az sağlayıcıyı Kaydet
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Azure Key Vault örneğini oluşturma
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Yönetici ilkelerini Key Vault ekleyin
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Kullanıcının asıl adını biliyorsanız şifreleme anahtarlarını, sertifikaları ve gizli dizileri almasını ve listelebilmesini sağlayan bir erişim ilkesi oluşturmak için:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Kaynaklara erişmek için Key Vault kullanan uygulamalarda Azure kaynakları için Yönetilen kimlikler kullanmak en iyi uygulamadır. Key Vault erişim tuşları kodda veya yapılandırmada depolanmıyorsa güvenlik sonrası artar.

Kapsayıcıda bir kök sertifika bulunur. Sertifikayı elde etmek için uygulanan adımlar şunlardır

1. Sertifika [yetkilisinden](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)sertifika dosyasını indirin.
2. Sertifika dosyanızın kodunu çözün:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Bu betik, kod veya yapılandırmada gizli dizileri kodlamadan Azure Key Vault etkileşimde bulunmak için MSI ile birlikte kullanılabilecek App Service örneği için atanmış bir kimlik oluşturur.

Erişim ilkesi sekmesinde atanan kimliği yetkilendirmek için portalda Azure Key Vault örneğine gidin. **Yeni erişim Ilkesi Ekle**' yi seçin. **Asıl seçin**altında, oluşturulan App Service örneğinin adına benzer bir uygulama adı arayın.
Uygulamaya bağlı bir hizmet sorumlusu görünür olmalıdır. Aşağıdaki ekran görüntüsünde gösterildiği gibi, seçin ve erişim ilkesi sayfasını kaydedin.

Uygulamanın yalnızca anahtarları alması gerektiğinden gizli dizi seçeneklerinde **Al** iznini seçin ve verilen ayrıcalıkları azaltırken erişime izin verir.

![Key Vault erişim Ilkesi](./media/secure-aad-app/kv-access-policy.png)

*Key Vault erişim ilkesi oluşturma*

Erişim ilkesini kaydedin ve sonra ilkeleri güncelleştirmek için **erişim ilkeleri** sekmesindeki yeni değişikliği kaydedin.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Web uygulaması güvenlik duvarı etkinken Application Gateway dağıtma
Web Apps 'te, Hizmetleri doğrudan Internet 'teki dış dünya için kullanıma sunmanın önerilmez.
Yük Dengeleme ve güvenlik duvarı kuralları, gelen trafik üzerinde daha fazla güvenlik ve denetim sağlar ve bunu yönetmenize yardımcı olur.

Application Gateway örneği dağıtmak için

1. Uygulama ağ geçidini barındırmak için kaynak grubu oluşturun.
2. Ağ geçidine iliştirilecek bir sanal ağ sağlayın.
3. Sanal ağda ağ geçidi için bir alt ağ oluşturun.
4. Genel bir IP adresi sağlayın.
5. Uygulama ağ geçidini sağlayın.
6. Ağ geçidinde Web uygulaması güvenlik duvarını etkinleştirin.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.0.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.0.0

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 

#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Azure Web Apps dağıtma
Azure App Service, Python, Ruby, C#ve Java gibi dilleri kullanarak Web uygulamaları oluşturmanıza ve barındırmanıza olanak sağlar. Azure Ayrıca, Azure App Service platformunda neredeyse tüm programlama dillerinin çalıştırılmasına izin veren özel kapsayıcıları destekler.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Ücretsiz katmanda App Service planı oluşturma
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Web uygulaması oluşturma
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Devam etmeden önce, özel etki alanınız için Azure etki alanı adı sistem yapılandırma kullanıcı arabirimine gidin ve "www" ana bilgisayar adı için bir CNAME kaydı yapılandırmak ve Web uygulamanızın varsayılan etki alanı adını göstermek için https://aka.ms/appservicecustomdns adresindeki yönergeleri izleyin

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>App Service planını paylaşılan katmana yükselt (özel etki alanları için gereken en düşük)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Web uygulamasına özel bir etki alanı adı ekleme
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Kılavuz ve öneriler

### <a name="network"></a>Ağ
Dağıtımı tamamladıktan sonra, Web uygulaması güvenlik duvarı 'nın etkin olduğu bir uygulama ağ geçidiniz vardır.

Ağ Geçidi örneği, HTTPS için 443 bağlantı noktasını kullanıma sunar. Bu yapılandırma, uygulamamıza yalnızca HTTPS üzerinden 443 numaralı bağlantı noktası üzerinden erişilebilmesini sağlar.

Kullanılmayan bağlantı noktalarını engelleme ve saldırı yüzeyi pozlamasını sınırlandırma en iyi güvenlik yöntemidir.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>App Service örneğine ağ güvenlik grupları ekleme

App Service örnekleri, sanal ağlarla tümleştirilebilir. Bu tümleştirme, uygulamanın gelen ve giden trafiği yöneten ağ güvenlik grubu ilkeleriyle yapılandırılmasını sağlar.

1. Bu özelliği etkinleştirmek için, Azure App Service örneği dikey penceresinde, **Ayarlar**altında **ağ**' ı seçer. Sağ bölmede, **VNET tümleştirmesi**altında yapılandırın.

   ![Yeni sanal ağ tümleştirmesi](./media/secure-web-app/app-vnet-menu.png)

    *App Service için yeni sanal ağ tümleştirmesi*
1. Sonraki sayfada **VNET Ekle (Önizleme)** öğesini seçin.

1. Sonraki menüde, dağıtımda oluşturulan `aad-vnet`ile başlayan sanal ağı seçin. Yeni bir alt ağ oluşturabilir veya var olan bir alt ağı seçebilirsiniz.
   Bu durumda yeni bir alt ağ oluşturun. **Adres aralığını** **10.0.3.0/24** olarak ayarlayın ve alt ağ **App-subnet**olarak adlandırın.

   ![App Service sanal ağ yapılandırması](./media/secure-web-app/app-vnet-config.png)

    *App Service için sanal ağ yapılandırması*

Sanal ağ tümleştirmesini etkinleştirmiş olduğunuza göre, uygulamamıza ağ güvenlik grupları ekleyebilirsiniz.

1. Arama kutusunu kullanarak **ağ güvenlik grupları**' nı arayın. Sonuçlarda **ağ güvenlik grupları** ' nı seçin.

    ![Ağ güvenlik grupları ara](./media/secure-web-app/nsg-search-menu.png)

    *Ağ güvenlik grupları ara*

2. Sonraki menüde **Ekle**' yi seçin. NSG ve bulunduğu **kaynak grubunun** **adını** girin. Bu NSG, uygulama ağ geçidinin alt ağına uygulanır.

    ![NSG oluşturma](./media/secure-web-app/nsg-create-new.png)

    *NSG oluşturma*

3. NSG oluşturulduktan sonra, bunu seçin. Dikey penceresinde, **Ayarlar**altında **gelen güvenlik kuralları**' nı seçin. 443 numaralı bağlantı noktası üzerinden Application Gateway 'e gelen bağlantılara izin vermek için bu ayarları yapılandırın.

   ![NSG 'yi yapılandırma](./media/secure-web-app/nsg-gateway-config.png)

   *NSG 'yi yapılandırma*

4. Ağ Geçidi NSG için giden kurallarında, hizmet etiketini hedefleyen bir kural oluşturarak App Service örneğine giden bağlantılara izin veren bir kural ekleyin `AppService`

   ![NSG için giden kuralları ekleme](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *NSG için giden kuralları ekleme*

    Ağ geçidinin bir sanal ağa giden kuralları göndermesini sağlamak için başka bir giden kuralı ekleyin.

   ![Başka bir giden kuralı ekle](./media/secure-web-app/nsg-outbound-vnet.png)

    *Başka bir giden kuralı ekle*

5. NSG alt ağları dikey penceresinde **ilişkilendir**' i seçin, dağıtımda oluşturulan sanal ağı seçin ve **GW-subnet**adlı ağ geçidi alt ağını seçin. NSG alt ağa uygulanır.

6. App Service örneği için bu kez, önceki adımda olduğu gibi başka bir NSG oluşturun. Bir ad verin. Application Gateway NSG için yaptığınız gibi 443 numaralı bağlantı noktası için gelen kuralını ekleyin.

   Bu uygulama için durum bilgisi olmayan bir App Service Ortamı örneği üzerinde dağıtılan bir App Service örneğiniz varsa, App Service NSG 'nizin gelen güvenlik gruplarında 454-455 bağlantı noktalarını açarak Azure hizmet durumu araştırmalarını sağlamak için gelen kuralları ekleyebilirsiniz. Yapılandırma şu şekildedir:

   ![Azure hizmet durumu araştırmaları için kurallar ekleme](./media/secure-web-app/nsg-create-healthprobes.png)

    *Azure hizmet durumu araştırmaları için kurallar ekleme (yalnızca App Service Ortamı)*

Saldırı yüzeyini sınırlandırmak için App Service ağ ayarlarını yalnızca uygulama ağ geçidinin uygulamaya erişmesine izin verecek şekilde değiştirin.
Ayarları uygulamak için, App Service ağ sekmesi ' ne gidin, **IP kısıtlamaları** sekmesini seçin ve yalnızca uygulama ağ geçidinin IP 'sini doğrudan erişime izin veren bir izin verme kuralı oluşturun. Ağ geçidinin IP adresini genel bakış sayfasından alabilirsiniz. **IP adresı CIDR** sekmesinde IP adresini şu biçimde girin: `<GATEWAY_IP_ADDRESS>/32`.

![Yalnızca ağ geçidine izin ver](./media/secure-web-app/app-allow-gw-only.png)

*Yalnızca ağ geçidi IP 'nin App Service erişmesine izin ver*

### <a name="azure-domain-name-system"></a>Azure etki alanı adı sistemi 
Azure etki alanı adı sistemi veya Azure etki alanı adı sistemi, IP adresine bir Web sitesi veya hizmet adı çevirmekten (veya çözümlemeden) sorumludur. Azure etki alanı adı sistemi (https://docs.microsoft.com/azure/dns/dns-overview), Azure altyapısı kullanılarak ad çözümlemesi sağlayan etki alanı adı sistemi etki alanları için bir barındırma hizmetidir. Kullanıcılar, Azure 'da etki alanlarını barındırarak, diğer Azure hizmetleriyle aynı kimlik bilgilerini, API 'Leri, araçları ve faturalandırmayı kullanarak etki alanı adı sistem kayıtlarını yönetebilir. Azure etki alanı adı sistemi, özel etki alanı adı sistemi etki alanlarını da destekler.

### <a name="azure-disk-encryption"></a>Azure Disk Şifrelemesi
Azure disk şifrelemesi, veri diskleri için birim şifrelemesi sağlamak üzere Windows 'un BitLocker özelliğinden yararlanır. Çözüm, disk şifreleme anahtarlarının denetlenmesi ve yönetilmesine yardımcı olmak için Azure Key Vault ile tümleşir.

### <a name="identity-management"></a>Kimlik yönetimi
Aşağıdaki teknolojiler, Azure ortamındaki cardş verilerine erişimi yönetmek için yetenekler sağlar
- Azure Active Directory, Microsoft 'un çok kiracılı bulut tabanlı dizin ve kimlik yönetimi hizmetidir. Bu çözüme yönelik tüm kullanıcılar Azure WebApp 'e erişen kullanıcılar da dahil olmak üzere Azure Active Directory oluşturulur.
- Azure rol tabanlı erişim denetimi, yöneticilerin yalnızca kullanıcıların işlerini gerçekleştirmesi için ihtiyaç duyduğu erişim miktarını vermek üzere hassas erişim izinleri tanımlamasına olanak sağlar. Yöneticiler, her kullanıcıya Azure kaynakları için Kısıtlanmamış izin vermek yerine, kart sahibi verilerine erişim için yalnızca belirli eylemlere izin verebilir. Abonelik erişimi, abonelik yöneticisiyle sınırlıdır.
- Azure Active Directory Privileged Identity Management, müşterilerin cardş verileri gibi belirli bilgilere erişimi olan kullanıcı sayısını en aza indirmesine olanak sağlar. Yöneticiler, ayrıcalıklı kimlikleri ve kaynaklara erişimlerini bulma, kısıtlama ve izleme işlemleri için Azure Active Directory Privileged Identity Management kullanabilir. Bu işlev, gerektiğinde isteğe bağlı, tam zamanında yönetim erişimi sağlamak için de kullanılabilir.
- Azure Active Directory Kimlik Koruması bir kuruluşun kimliklerini etkileyen olası güvenlik açıklarını algılar, bir kuruluşun kimlikleriyle ilişkili şüpheli eylemler için otomatik yanıtlar yapılandırır ve kuşkulu araştırır olayları çözmek için uygun eylemi gerçekleştirin.
### <a name="secrets-management"></a>Gizli dizi yönetimi
Çözüm, anahtarların ve parolaların yönetimi için Azure Key Vault kullanır. Azure Anahtar Kasası, bulut uygulamaları ve hizmetleri tarafından kullanılan şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olur. Aşağıdaki Azure Key Vault özellikleri müşterilerin bu verileri korumalarına ve bu verilere erişmesine yardımcı olur
   - Gelişmiş erişim ilkeleri, gereksinim temelinde yapılandırılır.
   - Key Vault erişim ilkeleri anahtarlar ve gizlilikler için gereken en düşük izinlerle tanımlanır.
   - Key Vault tüm anahtarlar ve gizli dizileri için süre sonu tarihleri vardır.
   - Key Vault tüm anahtarlar özel donanım güvenliği modülleri tarafından korunur. Anahtar türü, bir donanım güvenlik modülü (HSM) korunan 2048-bit RSA anahtarıdır.
   - Key Vault, anahtarları ve gizli dizileri (kimlik doğrulaması anahtarları, depolama hesabı anahtarları, veri şifreleme anahtarları,) şifreleyebilirsiniz. PFX dosyaları ve parolalar), donanım güvenlik modülleri (HSM 'ler) tarafından korunan anahtarları kullanarak. 
   - Belirli bir kapsamdaki kullanıcılara, gruplara ve uygulamalara izinler atamak için rol tabanlı Access Control (RBAC) kullanın.     
   - Otomatik yenileme ile TLS sertifikalarınızı yönetmek için Key Vault kullanın. 
   - Key Vault için tanılama günlükleri, en az 365 günlük bir bekletme dönemi ile etkinleştirilir.
   - Anahtarlar için izin verilen şifreleme işlemleri, gerekli olanlarla kısıtlıdır.
### <a name="azure-security-center"></a>Azure Güvenlik Merkezi
Azure Güvenlik Merkezi ile, müşteriler iş yükleri genelinde güvenlik ilkelerini merkezi olarak uygulayabilir ve yönetebilir, tehditlere maruz kalma olasılığını sınırlayabilir, saldırıları algılayıp yanıtlayabilir. Ayrıca 
   - Azure Güvenlik Merkezi, güvenlik duruşunu artırmaya ve verileri korumaya yardımcı olmak üzere yapılandırma ve hizmet önerileri sağlamak için mevcut Azure hizmetleri yapılandırmalarına erişir.
   - Azure Güvenlik Merkezi, ortamlarını hedefleyen olası saldırılardan müşterileri uyarmak için çeşitli algılama özellikleri kullanır. Bu uyarılar uyarıyı neyin tetiklediği, hedeflenen kaynaklar ve saldırının kaynağı hakkındaki değerli bilgileri içerir. Azure Güvenlik Merkezi, bir tehdit ya da şüpheli etkinlik gerçekleştiğinde tetiklenen bir dizi önceden tanımlanmış güvenlik uyarısı içerir. Azure Güvenlik Merkezi 'ndeki özel uyarı kuralları, müşterilerin ortamlarından zaten toplanmış olan verileri temel alarak yeni güvenlik uyarıları tanımlamasına olanak tanır.
   - Azure Güvenlik Merkezi, öncelik veren güvenlik uyarıları ve olayları sunarak müşterilerin olası güvenlik sorunlarını bulmasını ve adreslerini daha kolay hale getirmesini sağlar. Algılanan her tehdit için tehdit zekası raporu, tehditleri İnceleme ve düzeltme konusunda olay yanıtı ekiplerine yardımcı olacak şekilde oluşturulmuştur.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   Mimari, bir Web uygulaması güvenlik duvarı yapılandırılmış bir Azure Application Gateway kullanan güvenlik açıklarına karşı risk düzeyini azaltır ve OWASP kural kümesi etkindir. Ek yetenekler şunlardır
   - Uçtan uca SSL.
   - TLS v 1.0 ve v 1.1 'yi devre dışı bırakın.
   - TLSv 1.2 'yi etkinleştirin.
   - Web uygulaması güvenlik duvarı (önleme modu).
   - OWASP 3,0 RuleSet ile önleme modu.
   - Tanılama günlüğünü etkinleştirin.
   - Özel durum araştırmaları.
   - Azure Güvenlik Merkezi ve Azure Danışmanı, ek koruma ve bildirim sağlar. Azure Güvenlik Merkezi ayrıca bir saygınlık sistem sağlar.
### <a name="logging-and-auditing"></a>Günlük kaydı ve denetim
Azure Hizmetleri, sistem durumunun yanı sıra sistem durumu ve Kullanıcı etkinliklerini de yoğun olarak günlüğe kaydeder:
   - Etkinlik günlükleri: [etkinlik günlükleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) , bir abonelikteki kaynaklarda gerçekleştirilen işlemlerle ilgili öngörüler sağlar. Etkinlik günlükleri, bir işlemin başlatıcısının, oluşma süresinin ve durumunun belirlenmesine yardımcı olabilir.
   - Tanılama günlükleri: [tanılama günlükleri](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) her kaynak tarafından yayılan tüm günlükleri içerir. Bu Günlükler Windows olay sistemi günlükleri, Azure depolama günlükleri, Key Vault denetim günlükleri ve Application Gateway erişim ve güvenlik duvarı günlükleri içerir. Tüm tanılama günlükleri, arşivleme için merkezi ve şifrelenmiş bir Azure depolama hesabına yazar. Bekletme, kuruluşa özgü saklama gereksinimlerini karşılamak için Kullanıcı tarafından yapılandırılabilir ve 730 güne kadar.
### <a name="azure-monitor-logs"></a>Azure İzleyici günlükleri
   Bu Günlükler, işleme, depolama ve Pano raporlama için [Azure izleyici günlüklerinde](https://azure.microsoft.com/services/log-analytics/) birleştirilir. Toplandıktan sonra veriler, özgün kaynağından bağımsız olarak tüm verilerin birlikte çözümlenme olanağı sağlayan Log Analytics çalışma alanları içindeki her bir veri türü için ayrı tablolar halinde düzenlenir. Ayrıca, Azure Güvenlik Merkezi, müşterilerin güvenlik olay verilerine erişmek ve diğer hizmetlerdeki verilerle birleştirmek için kusto sorguları kullanmasına izin veren Azure Izleyici günlükleri ile tümleşir.

   Aşağıdaki Azure [izleme çözümleri](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) bu mimarinin bir parçası olarak dahil edilmiştir

   - [Active Directory değerlendirmesi](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory durum denetimi çözümü, düzenli aralıklarla sunucu ortamlarının riskini ve sistem durumunu değerlendirir ve dağıtılan Sunucu altyapısına özgü önerilerin öncelikli bir listesini sağlar.
   - [Aracı durumu](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Aracı durumu çözümü, kaç aracının dağıtıldığını ve coğrafi dağıtımını, yanıt vermeyen aracı sayısını ve işletimsel verileri gönderen aracıların sayısını bildirir.
   - [Etkinlik günlüğü Analizi](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): etkinlik günlüğü analizi çözümü, bir müşterinin tüm Azure aboneliklerinde Azure etkinlik günlüklerinin analizine yardımcı olur.
### <a name="azure-monitor"></a>Azure İzleyici
   [Azure izleyici](https://docs.microsoft.com/azure/monitoring-and-diagnostics/), kuruluşların Azure kaynaklarında API çağrılarını izleme de dahil olmak üzere performansı izlemelerine, uyarıları oluşturmalarına ve verileri arşivlemesini sağlayarak eğilimleri belirlemesine yardımcı olur.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) , birden çok platformda Web geliştiricileri için genişletilebilir bir uygulama performansı yönetim hizmetidir. Application Insights, performans bozuklularını algılar ve müşteriler Canlı Web uygulamasını izlemek için kullanabilir. Müşterilerin sorunları tanılamasına ve hangi kullanıcıların uygulamayla gerçekten ne yaptığını anlamalarına yardımcı olan güçlü analiz araçları içerir. Müşterilerin performansı ve kullanılabilirliği sürekli gelişmesine yardımcı olmak için tasarlanmıştır.

### <a name="azure-key-vault"></a>Azure Key Vault
   Anahtar depolamak istediğiniz kuruluş için bir kasa oluşturun ve aşağıdaki gibi işletimsel görevler için sorumlulukları koruyun

   - Key Vault depolanan veriler şunlardır   
   - Application Insight anahtarı
   - Veri depolama erişim anahtarı
   - Bağlantı dizesi
   - Veri tablosu adı
   - Kullanıcı kimlik bilgileri
   - Gelişmiş erişim ilkeleri bir gereksinim temelinde yapılandırılır
   - Key Vault erişim ilkeleri anahtarlar ve gizlilikler için gereken en düşük izinlerle tanımlanır
   - Key Vault tüm anahtarlar ve gizli dizileri sona erme tarihlerine sahiptir
   - Key Vault tüm anahtarlar donanım güvenlik modülü (HSM) tarafından korunuyor [anahtar türü = donanım güvenlik modülü (HSM) korumalı       
     2048 bit RSA anahtarı]
   - Tüm kullanıcılara/kimliklere rol tabanlı Access Control (RBAC) kullanarak gerekli olan en düşük izinler verilir
   - Uygulamalar birbirlerine güvenmedikleri ve çalışma zamanında aynı gizli anahtarlara erişmesi gereken bir Key Vault paylaşmaz
   - Key Vault için tanılama günlükleri, en az 365 günlük bir bekletme dönemi ile etkinleştirilir.
   - Anahtarlar için izin verilen şifreleme işlemleri, gerekli olanlarla kısıtlıdır

### <a name="vpn-and-expressroute"></a>VPN ve ExpressRoute
   Güvenli bir VPN tüneli veya [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) , bu PaaS Web uygulaması başvuru mimarisinin bir parçası olarak dağıtılan kaynaklarla güvenli bir şekilde bağlantı kurarak yapılandırılması gerekir. Bir VPN veya ExpressRoute 'u uygun şekilde ayarlayarak, müşteriler aktarım sırasında veriler için bir koruma katmanı ekleyebilir.

   Azure ile güvenli bir VPN tüneli uygulayarak, şirket içi ağ ve Azure sanal ağı arasında sanal bir özel bağlantı oluşturulabilir. Bu bağlantı Internet üzerinden gerçekleşir ve müşterilerin, müşterinin ağı ile Azure arasındaki şifrelenmiş bir bağlantı içindeki bilgileri güvenli bir şekilde "tünele" tüneletmesine olanak tanır. Siteden siteye VPN, delikler için tüm boyutlardaki kuruluşlar tarafından dağıtılan güvenli, Olgun bir teknolojidir. IPSec tünel modu Bu seçenekte bir şifreleme mekanizması olarak kullanılır.

   VPN tünelinin içindeki trafik, siteden siteye VPN ile Internet 'Te gezintiğinden, Microsoft başka bir daha bile daha güvenli bağlantı seçeneği sunar. Azure ExpressRoute, Azure ile şirket içi bir konum veya bir Exchange barındırma sağlayıcısı arasındaki adanmış bir WAN bağlantıdır. ExpressRoute bağlantıları Internet üzerinden gitmediğinden, bu bağlantılar daha fazla güvenilirlik, daha hızlı hız, daha düşük gecikme süreleri ve Internet üzerinden tipik bağlantılardan daha yüksek güvenlik sunar. Ayrıca, bu müşterinin iletişim sağlayıcısının doğrudan bir bağlantısı olduğundan, veriler Internet üzerinden hareket etmez ve bu nedenle bu açık değildir.

   Şirket içi bir ağı Azure 'a genişleten güvenli bir karma ağ uygulamak için en iyi yöntemler [mevcuttur](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

#### <a name="implement-azure-active-directory-oidc"></a>OıDC Azure Active Directory Uygula

1. Kaynak kodu deposunu kopyalamak için bu git komutunu kullanın

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Yeniden yönlendirme URL 'Lerini Güncelleştir
1.  Azure portal geri gidin. Sol taraftaki Gezinti bölmesinde Azure Active Directory hizmetini seçin ve Uygulama kayıtları ' ı seçin.
2.  Sonuç ekranında WebApp-Openıdconnect-DotNet-Code-v2 uygulamasını seçin.
3.  Yeniden yönlendirme URI 'Leri bölümündeki o kimlik doğrulama sekmesinde, açılan kutudan Web ' i seçin ve aşağıdaki yeniden yönlendirme URI 'Lerini ekleyin.
    Gelişmiş ayarlar bölümünde https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc için Logout URL 'sini ayarlayın
4.  Marka sekmesinde o ana sayfa URL 'sini App Service 'nizin adresine güncelleştirin, örneğin https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net.
        o yapılandırmayı kaydedin.
5.  Uygulamanız bir Web API 'si çağırırsa, gerekli değişiklikleri bu proje appSettings. json ' da uyguladığınızdan emin olun, bu nedenle localhost yerine yayımlanmış API URL 'sini çağırır.
Örnek yayımlanıyor
    1.  App Service genel bakış sekmesinden yayımlama profilini al bağlantısına tıklayarak yayımlama profilini indirin ve kaydedin. Kaynak denetimindeki gibi diğer dağıtım mekanizmaları de kullanılabilir.
    2.  Visual Studio 'ya geçin ve WebApp-Openıdconnect-DotNet-Code-v2 projesine gidin. Çözüm Gezgini projeye sağ tıklayın ve Yayımla ' yı seçin. Alt çubukta profili Içeri Aktar ' a tıklayın ve daha önce indirdiğiniz yayımlama profilini içeri aktarın.
    3.  Yapılandır ' a tıklayın ve bağlantı sekmesinde hedef URL 'yi giriş sayfası URL 'sinde https olacak şekilde güncelleştirin, örneğin https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net. İleri'ye tıklayın.
    4.  Ayarlar sekmesinde, kurumsal kimlik doğrulamasının etkinleştir ' in seçili olmadığından emin olun. Kaydet’e tıklayın. Ana ekranda Yayımla ' ya tıklayın.
    5.  Visual Studio projeyi yayımlayacak ve projenin URL 'sine otomatik olarak bir tarayıcı açacak. Projenin varsayılan Web sayfasını görürseniz, yayın başarılı olmuştur.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Azure Active Directory için Multi-Factor Authentication uygulama
   Yöneticiler, portaldaki abonelik hesaplarının korunduğundan emin olmalıdır. Abonelik, oluşturduğunuz kaynakları yönettiği için saldırılara açıktır. Aboneliği korumak için, aboneliğin **Azure Active Directory** sekmesinde Multi-Factor Authentication etkinleştirin.

   Bir Azure AD, belirli ölçütlere uyan bir kullanıcıya veya kullanıcı grubuna uygulanan ilkelere göre çalışır.
Azure, portalda oturum açmak için yöneticilerin iki öğeli kimlik doğrulamasının gerekli olduğunu belirten bir varsayılan ilke oluşturur.
Bu ilkeyi etkinleştirdikten sonra, Azure portal oturumunuzu kapatıp yeniden açmanız istenebilir.

Yönetici oturum açma işlemleri için MFA 'yı etkinleştirmek için

   1. Azure portal **Azure Active Directory** sekmesine gidin
   2. Güvenlik kategorisi altında koşullu erişim ' i seçin. Bu ekranı görürsünüz

       ![Koşullu erişim-Ilkeler](./media/secure-aad-app/ad-mfa-conditional-add.png)

Yeni bir ilke oluşturamıyoruz

   1. **MFA** sekmesine gidin.
   2. Ücretsiz denemeye abone olmak için Azure AD Premium **ücretsiz bir deneme** bağlantısı seçin.

   ![Azure AD Premium ücretsiz deneme sürümü](./media/secure-aad-app/ad-trial-premium.png)

Koşullu erişim ekranına geri dönün.

   1. Yeni ilke sekmesini seçin.
   2. İlke adını girin.
   3. MFA 'yı etkinleştirmek istediğiniz kullanıcıları veya grupları seçin.
   4. **Erişim denetimleri**altında, **izin** sekmesini seçin ve ardından **Multi-Factor Authentication** (isterseniz diğer ayarlar) iste ' yi seçin.

   ![MFA gerektirme](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Ekranın üst kısmındaki onay kutusunu seçerek ilkeyi etkinleştirebilir veya **koşullu erişim** sekmesinden bunu yapabilirsiniz. İlke etkinleştirildiğinde, kullanıcıların portalda oturum açması için MFA gerekir.

   Tüm Azure yöneticileri için MFA gerektiren bir temel ilke vardır. Bunu portalda hemen etkinleştirebilirsiniz. Bu ilkeyi etkinleştirmek, geçerli oturumu geçersiz kılabilir ve yeniden oturum açmanızı zorlayabilir.

   Temel ilke etkinleştirilmemişse
   1.   **Yöneticiler IÇIN MFA gerektir**' i seçin.
   2.   **İlkeyi hemen kullan**' ı seçin.

   ![İlkeyi hemen kullan ' ı seçin](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel kullanarak uygulama ve kaynakları izleme

   Bir uygulama büyüdükçe, kaynaklardan alınan tüm güvenlik sinyallerinin ve ölçümlerin toplanmasını ve bunları eyleme dayalı bir şekilde yararlı hale getirmek zor olur.

   Azure Sentinel, verileri toplamak, olası tehdit türlerini algılamak ve güvenlik olaylarına görünürlük sağlamak için tasarlanmıştır.
El ile müdahale beklerken, Azure Sentinel, uyarıları ve olay yönetimi süreçlerini açmak için önceden yazılmış PlayBook 'ları kullanabilir.

   Örnek uygulama, Azure Sentinel 'in izleye, birkaç kaynaktan oluşur.
Azure Sentinel 'i ayarlamak için, önce çeşitli kaynaklardan toplanan tüm verileri depolayan bir Log Analytics çalışma alanı oluşturmanız gerekir.

Bu çalışma alanını oluşturmak için

   1. Azure portal arama kutusunda, **Log Analytics**aratın. **Log Analytics çalışma alanlarını**seçin.

   ![Log Analytics çalışma alanlarını ara](./media/secure-aad-app/sentinel-log-analytics.png)

   *Log Analytics çalışma alanlarını ara*

   2. Sonraki sayfada **Ekle** ' yi seçin ve ardından çalışma alanı için bir ad, kaynak grubu ve konum sağlayın.
   ![bir Log Analytics çalışma alanı oluşturun](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Log Analytics çalışma alanı oluşturma*

   3. **Azure Sentinel**aramak için arama kutusunu kullanın.

   ![Azure Sentinel 'i arayın](./media/secure-aad-app/sentinel-add.png)

   *Azure Sentinel 'i arayın*

   4. **Ekle** ' yi seçin ve daha önce oluşturduğunuz Log Analytics çalışma alanını seçin.

   ![Log Analytics çalışma alanı ekleme](./media/secure-aad-app/sentinel-workspace-add.png)

   *Log Analytics çalışma alanı ekleme*

   5. **Azure Sentinel-veri bağlayıcıları** sayfasında, **yapılandırma**altında, **veri bağlayıcıları**' nı seçin. Azure Sentinel 'de analize yönelik Log Analytics depolama örneğine bağlayabileceğiniz bir Azure hizmetleri dizisi görürsünüz.

   ![Log Analytics veri bağlayıcıları](./media/secure-aad-app/sentinel-connectors.png)

      *Azure Sentinel 'e veri Bağlayıcısı ekleme*

   Örneğin, uygulama ağ geçidini bağlamak için şu adımları uygulayın:

   1. Azure Application Gateway örneği dikey penceresini açın.
   2. **İzleme**altında **Tanılama ayarları**' nı seçin.
   3. **Tanılama ayarı Ekle**' yi seçin.

   ![Application Gateway tanılamayı Ekle](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Application Gateway tanılamayı Ekle*

   4. **Tanılama ayarları** sayfasında, oluşturduğunuz Log Analytics çalışma alanını seçin ve ardından toplamak istediğiniz tüm ölçümleri seçin ve Azure Sentinel 'e gönderin. **Kaydet**’i seçin.

   ![Azure Sentinel bağlayıcı ayarları](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Maliyetle ilgili konular
   Henüz bir Azure hesabınız yoksa, ücretsiz bir hesap oluşturabilirsiniz. Kullanmaya başlamak için [ücretsiz hesap sayfasına](https://azure.microsoft.com/free/) gidin, ücretsiz bir Azure hesabıyla neler yapabileceğinizi öğrenin ve 12 ay boyunca hangi ürünlerin ücretsiz olduğunu öğrenebilirsiniz.

   Örnek uygulamadaki kaynakları güvenlik özellikleriyle dağıtmak için bazı Premium özellikler için ödeme yapmanız gerekir. Uygulamanın ölçeklendirilen ve Azure tarafından sunulan ücretsiz katmanların ve denemelerin uygulama gereksinimlerini karşılayacak şekilde yükseltilmesi gerekir, maliyetleriniz artırılabilir. Maliyetlerinizi tahmin etmek için Azure [Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/) ' nı kullanın.

## <a name="next-steps"></a>Sonraki adımlar
   Aşağıdaki makaleler güvenli uygulamalar tasarlamanıza, geliştirmenize ve dağıtmanıza yardımcı olabilir.

- [Tasarıma](secure-design.md)
- [Geliştirme](secure-develop.md)
- [Dağıtma](secure-deploy.md)

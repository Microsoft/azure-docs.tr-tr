---
title: Özel kök CA ile otomatik olarak imzalanan sertifika oluşturma
titleSuffix: Azure Application Gateway
description: Özel bir kök CA ile Azure Application Gateway otomatik olarak imzalanan sertifika oluşturmayı öğrenin
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: fb3d2e70d9485c63d6de156abe9d192afa818814
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075081"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Özel kök CA ile otomatik olarak imzalanan bir Azure Application Gateway sertifikası oluşturma

Application Gateway v2 SKU 'SU, arka uç sunucularına izin vermek için güvenilen kök sertifikaların kullanımını tanıtır. Bu, v1 SKU 'sunda gerekli olan kimlik doğrulama sertifikalarını kaldırır. *Kök sertifika* , Base-64 ile kodlanmış bir X. 509.440 (. CER) arka uç sertifika sunucusundan kök sertifikayı biçimlendirin. Sunucu sertifikasını veren kök sertifika yetkilisini (CA) belirler ve sunucu sertifikası daha sonra SSL iletişimi için kullanılır.

Application Gateway, iyi bilinen bir CA tarafından imzalanmışsa (örneğin, GoDaddy veya DigiCert) Web sitenizin sertifikasına güvenir. Bu durumda kök sertifikayı açıkça karşıya yüklemeniz gerekmez. Daha fazla bilgi için bkz. [Application Gateway Ile SSL sonlandırmasına ve uçtan uca SSL 'ye genel bakış](ssl-overview.md). Ancak, bir geliştirme/test ortamınız varsa ve doğrulanmış CA imzalı bir sertifika satın almak istemiyorsanız, kendi özel CA 'nizi oluşturabilir ve bununla birlikte kendinden imzalı bir sertifika oluşturabilirsiniz. 

> [!NOTE]
> Otomatik olarak imzalanan sertifikalara varsayılan olarak güvenilmez ve bunların bakımını yapmak zor olabilir. Ayrıca, süresi geçmiş karma ve şifre paketlerinin kullanımı güçlü olmayabilir. Daha iyi güvenlik için, iyi bilinen bir sertifika yetkilisi tarafından imzalanmış bir sertifika satın alın.

Bu makalede, öğreneceksiniz nasıl yapılır:

- Kendi özel sertifika yetkilinizi oluşturma
- Özel sertifika YETKILINIZ tarafından imzalanan kendinden imzalı bir sertifika oluşturma
- Arka uç sunucusunun kimliğini doğrulamak için bir Application Gateway otomatik olarak imzalanan kök sertifika yükleme

## <a name="prerequisites"></a>Önkoşullar

- **Windows veya Linux çalıştıran bir bilgisayarda [OpenSSL](https://www.openssl.org/)** 

   Bu öğretici, sertifika yönetimi için kullanılabilen başka araçlar da olabilir, ancak bu öğretici OpenSSL kullanır. Ubuntu gibi birçok Linux dağıtımı ile paketlenmiş OpenSSL bulabilirsiniz.
- **Bir Web sunucusu**

   Örneğin, bu sertifikaları sınamak için Apache, IIS veya NGıNX.

- **Application Gateway v2 SKU 'SU**
   
  Mevcut bir uygulama ağ geçidiniz yoksa, bkz. [hızlı başlangıç: Azure Application Gateway Ile doğrudan web trafiği-Azure Portal](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Kök CA sertifikası oluşturma

OpenSSL kullanarak kök CA sertifikanızı oluşturun.

### <a name="create-the-root-key"></a>Kök anahtarı oluşturma

1. OpenSSL 'nin yüklü olduğu bilgisayarınızda oturum açın ve aşağıdaki komutu çalıştırın. Bu, parola korumalı bir anahtar oluşturur.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. İstemde, güçlü bir parola yazın. Örneğin, büyük harf, küçük harf, sayı ve simge kullanan en az dokuz karakter.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Kök sertifika oluşturma ve kendi kendine imzalama

1. CSR ve sertifika oluşturmak için aşağıdaki komutları kullanın.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Önceki komutlar kök sertifikayı oluşturur. Bunu, sunucu sertifikanızı imzalamak için kullanacaksınız.

1. İstendiğinde, kök anahtar için parolayı ve ülke, eyalet, kuruluş, OU ve tam etki alanı adı (Bu veren etki alanı) gibi özel CA için kuruluş bilgilerini yazın.

   ![kök sertifikası oluştur](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Sunucu sertifikası oluşturma

Ardından, OpenSSL kullanarak bir sunucu sertifikası oluşturacaksınız.

### <a name="create-the-certificates-key"></a>Sertifikanın anahtarını oluşturma

Sunucu sertifikası için anahtar oluşturmak üzere aşağıdaki komutu kullanın.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>CSR (sertifika Imzalama Isteği) oluşturma

CSR, sertifika isteğinde bulunan bir CA 'ya verilen ortak anahtardır. CA, bu belirli istek için sertifikayı yayınlar.

> [!NOTE]
> Sunucu sertifikası için CN (ortak ad), verenin etki alanından farklı olmalıdır. Örneğin, bu durumda veren için CN `www.contoso.com` ve sunucu sertifikasının CN 'si `www.fabrikam.com`.


1. CSR 'yi oluşturmak için aşağıdaki komutu kullanın:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. İstendiğinde, kök anahtarın parolasını ve özel CA için kuruluş bilgilerini yazın: ülke, eyalet, kuruluş, OU ve tam etki alanı adı. Bu, Web sitesinin etki alanıdır ve veren 'den farklı olmalıdır.

   ![Sunucu sertifikası](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Sertifikayı CSR ve anahtar ile oluşturun ve CA 'nın kök anahtarıyla imzalayın

1. Sertifikayı oluşturmak için aşağıdaki komutu kullanın:

   ```
   openssl x509 -req -in fabrikam.csr -CA public.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Yeni oluşturulan sertifikayı doğrulayın

1. CRT dosyasının çıkışını yazdırmak ve içeriğini doğrulamak için aşağıdaki komutu kullanın:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Sertifika doğrulama](media/self-signed-certificates/verify-cert.png)

1. Dizininizdeki dosyaları doğrulayın ve aşağıdaki dosyalara sahip olduğunuzdan emin olun:

   - contoso. CRT
   - contoso. Key
   - fabrikam. CRT
   - fabrikam. Key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>Web sunucunuzun SSL ayarlarındaki sertifikayı yapılandırın

Web sunucunuzda, fabrikam. CRT ve fabrikam. Key dosyalarını kullanarak SSL 'yi yapılandırın. Web sunucunuz iki dosya alamaz, OpenSSL komutlarını kullanarak bunları tek bir. ped veya. pfx dosyası ile birleştirebilirsiniz.

### <a name="iis"></a>IIS

Sertifikayı içeri aktarma ve IIS 'ye sunucu sertifikası olarak yükleme yönergeleri için bkz. [nasıl yapılır: Windows Server 'Da Içeri aktarılan sertifikaları bir Web sunucusuna yükleme 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

SSL bağlama yönergeleri için bkz. [IIS 7 ' de SSL ayarlama](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

Aşağıdaki yapılandırma, Apache 'de [SSL için yapılandırılmış örnek bir sanal ana bilgisayar](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) örneğidir:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

Aşağıdaki yapılandırma, SSL yapılandırmasına sahip bir [NGINX sunucu bloğu](https://nginx.org/docs/http/configuring_https_servers.html) örneğidir:

![SSL ile NGıNX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Yapılandırmayı doğrulamak için sunucuya erişin

1. Kök sertifikayı makinenizin güvenilen kök deposuna ekleyin. Web sitesine eriştiğinizde, tüm Sertifika zincirinin tarayıcıda görüldüğünü doğrulayın.

   ![Güvenilen kök sertifikalar](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > DNS 'nin Web sunucusu adını (Bu örnekte, www.fabrikam.com) Web sunucunuzun IP adresine işaret etmek üzere yapılandırıldığı varsayılır. Aksi takdirde, adı çözümlemek için [Hosts dosyasını](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) düzenleyebilirsiniz.
1. Web sitenize gidin ve site ve sertifika bilgilerini doğrulamak için tarayıcınızın adres kutusundaki kilit simgesine tıklayın.

## <a name="verify-the-configuration-with-openssl"></a>OpenSSL ile yapılandırmayı doğrulama

Ya da, sertifikayı doğrulamak için OpenSSL 'yi de kullanabilirsiniz.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL sertifikası doğrulaması](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Kök sertifikayı Application Gateway HTTP ayarlarına yükleyin

Application Gateway sertifikayı karşıya yüklemek için. CRT sertifikasını. cer biçiminde bir temel-64 kodlanmış olarak dışarı aktarmanız gerekir. . CRT zaten Base-64 kodlu biçimde ortak anahtarı içerdiğinden, dosya uzantısını. CRT 'den. cer 'e yeniden adlandırmanız yeterlidir. 

### <a name="azure-portal"></a>Azure portalında

Portaldan güvenilen kök sertifikayı karşıya yüklemek için **http ayarları** ' nı seçin ve **https** protokolünü seçin.

![Portalı kullanarak sertifika ekleme](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Ya da, kök sertifikayı karşıya yüklemek için Azure CLı veya Azure PowerShell kullanabilirsiniz. Aşağıdaki kod Azure PowerShell bir örnektir.

> [!NOTE]
> Aşağıdaki örnek, uygulama ağ geçidine güvenilen bir kök sertifika ekler, yeni bir HTTP ayarı oluşturur ve arka uç havuzunun ve dinleyicinin zaten mevcut olduğunu varsayarak yeni bir kural ekler.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don’t forget to set the “hostname” field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server’s certificate. Note that SSL handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```
### <a name="verify-the-application-gateway-backend-health"></a>Uygulama ağ geçidi arka uç durumunu doğrulama

1. Araştırmanın sağlıklı olup olmadığını denetlemek için uygulama ağ geçidinizin **arka uç sistem durumu** görünümüne tıklayın.
1.  Durumun HTTPS araştırması için **sağlıklı** olduğunu görmeniz gerekir.

    ![HTTPS araştırması](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Sonraki adımlar

Application Gateway 'de SSL\TLS hakkında daha fazla bilgi edinmek için bkz. [Application Gateway Ile SSL sonlandırmasına ve uçtan uca SSL 'ye genel bakış](ssl-overview.md).


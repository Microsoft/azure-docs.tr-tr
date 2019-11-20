---
title: PowerShell kullanarak SSL yük boşaltma-Azure Application Gateway
description: Bu makalede, Azure klasik dağıtım modelini kullanarak SSL yük boşaltma ile uygulama ağ geçidi oluşturma yönergeleri sağlanır
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047912"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Klasik dağıtım modelini kullanarak SSL yük boşaltması için uygulama ağ geçidi yapılandırma

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure klasik PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Azure Application Gateway, web grubunda maliyetli SSL şifre çözme görevlerinin oluşmasından kaçınmak için Güvenli Yuva Katmanı (SSL) oturumunu sonlandırmak amacıyla yapılandırılabilir. SSL yük boşaltımı ön uç sunucusunun kurulumunu ve web uygulamasının yönetimini de basitleştirir.

## <a name="before-you-begin"></a>Başlamadan önce

1. Web Platformu Yükleyicisi’ni kullanarak Azure PowerShell cmdlet’lerin en son sürümünü yükleyin. **İndirmeler sayfası**’ndaki [Windows PowerShell](https://azure.microsoft.com/downloads/) bölümünden en son sürümü indirip yükleyebilirsiniz.
2. Geçerli bir alt ağla çalışan bir sanal ağa sahip olduğunuzu doğrulayın. Ağ geçidi hiçbir sanal makinenin veya bulut dağıtımının kullanmadığından emin olun. Uygulama ağ geçidi tek başına bir sanal ağ alt ağında olmalıdır.
3. Application Gateway 'i kullanacak şekilde yapılandırdığınız sunucuların var olması veya sanal ağda veya bir genel IP adresi ya da sanal IP adresi (VIP) atanmış uç noktalarına sahip olması gerekir.

Bir uygulama ağ geçidinde SSL yük boşaltma yapılandırmak için, aşağıdaki adımları listelenen sırayla doldurun:

1. [Uygulama ağ geçidi oluşturma](#create-an-application-gateway)
2. [SSL sertifikalarını karşıya yükleme](#upload-ssl-certificates)
3. [Ağ geçidini yapılandırma](#configure-the-gateway)
4. [Ağ Geçidi yapılandırmasını ayarlama](#set-the-gateway-configuration)
5. [Ağ geçidini başlatma](#start-the-gateway)
6. [Ağ Geçidi durumunu doğrulama](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

Ağ geçidini oluşturmak için, değerleri kendi değerlerinizle değiştirerek `New-AzureApplicationGateway` cmdlet 'ini girin. Ağ geçidinin faturalanması bu aşamada başlamaz. Daha sonra ağ geçidi başarıyla başlatıldığında faturalama da başlar.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Ağ geçidinin oluşturulduğunu doğrulamak için `Get-AzureApplicationGateway` cmdlet 'ini girebilirsiniz.

Örnek, **Açıklama**, **InstanceCount**ve **gatewaysize** isteğe bağlı parametrelerdir. **InstanceCount** için varsayılan değer, en fazla **10**değeri olan **2**' dir. **Gatewaysize** varsayılan değeri **Orta**' dir. Küçük ve büyük diğer kullanılabilir değerlerdir. Ağ Geçidi henüz başlatılmadığından **Virtualıp 'leri** ve **DnsName** boş olarak gösterilir. Bu değerler, ağ geçidi çalışır durumda olduktan sonra oluşturulur.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>SSL sertifikalarını karşıya yükleme

Sunucu sertifikasını PFX biçiminde uygulama ağ geçidine yüklemek için `Add-AzureApplicationGatewaySslCertificate` girin. Sertifika adı, Kullanıcı tarafından seçilen bir addır ve uygulama ağ geçidi içinde benzersiz olmalıdır. Bu sertifika, uygulama ağ geçidinde tüm sertifika yönetimi işlemlerinde bu adla adlandırılır.

Aşağıdaki örnek cmdlet 'ini gösterir. Örnekteki değerleri kendi değerlerinizle değiştirin.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Sonra, sertifika karşıya yüklemeyi doğrulayın. `Get-AzureApplicationGatewayCertificate` cmdlet 'ini girin.

Aşağıdaki örnek, ilk satırdaki cmdlet 'ini ve sonra çıktıyı gösterir:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> Sertifika parolası, harf veya sayıdan oluşan 4 ila 12 karakter arasında olmalıdır. Özel karakterler kabul edilmez.

## <a name="configure-the-gateway"></a>Ağ geçidini yapılandırma

Uygulama ağ geçidi yapılandırması birden çok değerden oluşur. Yapılandırma oluşturmak için değerler birlikte bağlanabilir.

Değerler şunlardır:

* **Arka uç sunucu havuzu**: arka uç sunucularının IP adreslerinin listesi. Listelenen IP adresleri, sanal ağ alt ağına ait olmalıdır veya bir genel IP veya VIP adresi olmalıdır.
* **Arka uç sunucu havuzu ayarları**: her havuzun bağlantı noktası, protokol ve tanımlama bilgisi tabanlı benzeşim gibi ayarları vardır. Bu ayarlar bir havuza bağlıdır ve havuzdaki tüm sunuculara uygulanır.
* **Ön uç bağlantı noktası**: Bu bağlantı noktası, uygulama ağ geçidinde açılan genel bağlantı noktasıdır. Bu bağlantı noktasında trafik olursa arka uç sunuculardan birine yönlendirilir.
* **Dinleyici**: dinleyicide bir ön uç bağlantı noktası, bir protokol (http veya https; bu değerler büyük/küçük harfe duyarlıdır) ve SSL sertifika adı (SSL yük boşaltma yapılandırılıyorsa) vardır.
* **Kural**: kural dinleyiciyi ve arka uç sunucu havuzunu bağlar ve belirli bir dinleyiciye rastlarsa trafiğin hangi arka uç sunucu havuzuna yönlendirileceğini belirtir. Şu anda yalnızca *temel* kural desteklenmektedir. *Temel* kural hepsini bir kez deneme yöntemiyle yük dağıtımıdır.

**Ek yapılandırma notları**

SSL sertifikaları yapılandırmada **HttpListener**’daki protokol **Https** (küçük/büyük harf duyarlı) ile değiştirilmelidir. Değer kümesi, [SSL sertifikalarını karşıya yükle](#upload-ssl-certificates) bölümünde kullanılan adla **HttpListener** ' a **sslcert** öğesini ekleyin. Ön uç bağlantı noktası **443**olarak güncellenmelidir.

**Tanımlama bilgisi tabanlı benzeşimi etkinleştirmek için**: bir istemci oturumundan gelen isteğin, her zaman Web GRUBUNDAKI aynı VM 'ye yönlendirildiğinden emin olmak için bir uygulama ağ geçidi yapılandırabilirsiniz. Bunu gerçekleştirmek için, ağ geçidinin trafiği uygun şekilde yönlendirmesine izin veren bir oturum tanımlama bilgisi ekleyin. Tanımlama bilgisi temelli benzeşimi etkinleştirmek için, **CookieBasedAffinity**’yi **BackendHttpSetting** öğesindeki **Enabled**’a ayarlayın.

Yapılandırmanızı bir yapılandırma nesnesi oluşturarak ya da bir yapılandırma XML dosyası kullanarak oluşturabilirsiniz.
Yapılandırma XML dosyası kullanarak yapılandırmanızı oluşturmak için aşağıdaki örneği girin:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Ağ Geçidi yapılandırmasını ayarlama

Sonra, uygulama ağ geçidini kurun. `Set-AzureApplicationGatewayConfig` cmdlet 'ini ya bir yapılandırma nesnesi ya da bir yapılandırma XML dosyası ile girebilirsiniz.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Ağ geçidini başlatma

Ağ Geçidi yapılandırıldıktan sonra, ağ geçidini başlatmak için `Start-AzureApplicationGateway` cmdlet 'ini girin. Uygulama ağ geçidinin faturalanması ağ geçidi başarıyla başlatıldıktan sonra başlar.

> [!NOTE]
> `Start-AzureApplicationGateway` cmdlet 'inin tamamlanması 15-20 dakika sürebilir.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Ağ geçidi durumunu doğrulama

Ağ geçidinin durumunu denetlemek için `Get-AzureApplicationGateway` cmdlet 'ini girin. Önceki adımda başarılı `Start-AzureApplicationGateway`, **durum** **çalışıyor**olmalıdır ve **virtualıp 'leri** ve **DnsName** , geçerli girdilere sahip olmalıdır.

Bu örnek, çalışır durumda olan, çalışan ve trafik almaya yönelik bir uygulama ağ geçidini gösterir:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Sonraki adımlar

Genel olarak yük dengeleme seçenekleri hakkında daha fazla bilgi için bkz.:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

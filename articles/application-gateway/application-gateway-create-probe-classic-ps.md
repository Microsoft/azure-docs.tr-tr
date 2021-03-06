---
title: Klasik dağıtım modelini kullanarak özel bir araştırma oluşturma-Azure Application Gateway
description: Klasik dağıtım modelinde PowerShell kullanarak Application Gateway için özel bir araştırma oluşturma hakkında bilgi edinin
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 13441899eeb5ca2b7c60977ab2858fe40a398d1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397867"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>PowerShell kullanarak Azure Application Gateway (klasik) için özel bir araştırma oluşturma

> [!div class="op_single_selector"]
> * [Azure portalı](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Klasik PowerShell](application-gateway-create-probe-classic-ps.md)

Bu makalede, var olan bir Application Gateway 'e PowerShell ile özel bir araştırma eklersiniz. Özel yoklamalar, belirli bir sistem durumu denetimi sayfası veya varsayılan Web uygulamasında başarılı bir yanıt sağlamayan uygulamalar için yararlıdır.

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../azure-resource-manager/management/deployment-models.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir. [Bu adımları Resource Manager modeli kullanarak gerçekleştirmeyi](application-gateway-create-probe-ps.md) öğrenin.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

Bir uygulama ağ geçidi oluşturmak için:

1. Uygulama ağ geçidi kaynağı oluşturun.
2. Bir XML yapılandırma dosyası veya yapılandırma nesnesi oluşturun.
3. Yapılandırmayı, yeni oluşturulmuş uygulama ağ geçidi kaynağına uygulayın.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Özel araştırmasına sahip bir uygulama ağ geçidi kaynağı oluşturma

Ağ geçidini oluşturmak için, `New-AzureApplicationGateway` cmdlet’ini kullanın ve değerleri kendi değerlerinizle değiştirin. Ağ geçidinin faturalanması bu aşamada başlamaz. Daha sonra ağ geçidi başarıyla başlatıldığında faturalama da başlar. 

Aşağıdaki örnek, "testvnet1" adlı sanal ağı ve "subnet-1" aklı alt ağı kullanarak bir uygulama ağ geçidi oluşturur.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Ağ geçidinin oluşturulduğunu doğrulamak için `Get-AzureApplicationGateway` cmdlet’ini kullanabilirsiniz.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> *InstanceCount* için varsayılan değer 2 ile 10 arasıdır. *GatewaySize* için varsayılan değer Medium’dur. Küçük, orta ve büyük arasında seçim yapabilirsiniz.
> 
> 

Ağ geçidi daha başlatılmadığından dolayı *VirtualIPs* ve *DnsName* boş görünür. Bu değerler, ağ geçidi çalışır duruma getirildikten sonra oluşturulur.

### <a name="configure-an-application-gateway-by-using-xml"></a>XML kullanarak uygulama ağ geçidi yapılandırma

Aşağıdaki örnekte, tüm uygulama ağ geçidi ayarlarını yapılandırmak için bir XML dosyası kullanır ve bu ayarları uygulama ağ geçidi kaynağına uygularsınız.  

Aşağıdaki metni Notepad’a kopyalayın.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Parantez içindeki değerleri yapılandırma öğeleri için düzenleyin. Dosyası .xml. uzantısıyla kaydedin.

Aşağıdaki örnek, bir yapılandırma dosyasının genel bağlantı noktası 80 ' deki HTTP trafiğinin yükünü dengelemek ve özel bir araştırma kullanarak iki IP adresi arasında 80 arka uç bağlantı noktasına ağ trafiği göndermek için nasıl kullanılacağını gösterir.

> [!IMPORTANT]
> Http veya Https protokol öğesi büyük/küçük harf duyarlıdır.

\<Probe\>Özel araştırmaları yapılandırmak için yeni bir yapılandırma öğesi eklenir.

Yapılandırma parametreleri şunlardır:

|Parametre|Açıklama|
|---|---|
|**Ad** |Özel araştırma için başvuru adı. |
| **Protokol** | Protokol kullanıldı (olası değerler HTTP veya HTTPS 'DIR).|
| **Ana bilgisayar** ve **yol** | Örnek sistem durumunu öğrenmek için uygulama ağ geçidi tarafından çağrılan URL yolunu doldurun. Örneğin, http:/contoso.com/Web siteniz varsa \/ , \/ araştırma denetimlerinin başarılı bir HTTP yanıtına sahip olması için "http:/contoso.com/path/custompath.htm" için özel araştırma yapılandırılabilir.|
| **Aralık** | Yoklama aralığı denetimlerini saniye cinsinden yapılandırır.|
| **Aş** | Bir HTTP yanıt denetimi için araştırma zaman aşımını tanımlar.|
| **Unhealthyıthreshold** | Arka uç örneğini *sağlıksız* olarak işaretlemek için gereken başarısız http yanıtlarının sayısı.|

Yoklama adına, \<BackendHttpSettings\> hangi arka uç havuzunun özel araştırma ayarlarını kullanacağını atamak için yapılandırmada başvurulur.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Mevcut bir Application Gateway 'e özel bir araştırma ekleyin

Bir uygulama ağ geçidinin geçerli yapılandırmasını değiştirmek için üç adım gerekir: geçerli XML yapılandırma dosyasını alın, özel bir araştırmasını olacak şekilde değiştirin ve Application Gateway 'i yeni XML ayarlarıyla yapılandırın.

1. Kullanarak XML dosyasını alın `Get-AzureApplicationGatewayConfig` . Bu cmdlet, araştırma ayarı eklemek için değiştirilecek yapılandırma XML 'sini dışa aktarır.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. XML dosyasını bir metin düzenleyicisinde açın. `<probe>`Öğesinden sonra bir bölüm ekleyin `<frontendport>` .

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   XML 'nin backendHttpSettings bölümüne aşağıdaki örnekte gösterildiği gibi araştırma adını ekleyin:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   XML dosyasını kaydedin.

1. Kullanarak, yeni XML dosyası ile uygulama ağ geçidi yapılandırmasını güncelleştirin `Set-AzureApplicationGatewayConfig` . Bu cmdlet, uygulama ağ geçidinizi yeni yapılandırmayla güncelleştirir.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Sonraki adımlar

Daha önce Güvenli Yuva Katmanı (SSL) yük boşaltma olarak bilinen Aktarım Katmanı Güvenliği 'ni (TLS) yapılandırmak istiyorsanız bkz. [TLS boşaltması için uygulama ağ geçidi yapılandırma](./tutorial-ssl-powershell.md).

İç yük dengeleyiciyle kullanacağınız uygulama ağ geçidi yapılandırmak istiyorsanız, bkz. [İç yük dengeleyici (ILB) ile uygulama ağ geçidi oluşturma](./application-gateway-ilb-arm.md).
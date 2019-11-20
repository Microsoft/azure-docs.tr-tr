---
title: Hatalı ağ geçidi hatalarında sorun giderme-Azure Application Gateway
description: 'Application Gateway sunucu hatası giderme hakkında bilgi edinin: 502-Web sunucusu bir ağ geçidi veya proxy sunucusu görevi gören geçersiz bir yanıt aldı.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130479"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Application Gateway 'de hatalı ağ geçidi hatalarıyla ilgili sorunları giderme

Azure Application Gateway kullanılırken hatalı ağ geçidi (502) ile ilgili sorunların nasıl giderileceği hakkında bilgi edinin.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış

Bir uygulama ağ geçidini yapılandırdıktan sonra, görebileceğiniz hatalardan biri "sunucu hatası: 502-Web sunucusu bir ağ geçidi veya proxy sunucusu görevi gören geçersiz bir yanıt aldı" olarak belirlenir. Bu hata şu ana nedenlerle oluşabilir:

* NSG, UDR veya özel DNS, arka uç havuzu üyelerine erişimi engelliyor.
* Arka uç VM 'Leri veya sanal makine ölçek kümesinin örnekleri, varsayılan sistem durumu araştırmasına yanıt vermiyor.
* Özel sistem durumu araştırmalarının yapılandırması geçersiz veya hatalı.
* Azure Application Gateway [arka uç havuzu yapılandırılmamış veya boş](#empty-backendaddresspool).
* [Sanal makine ölçek kümesindeki](#unhealthy-instances-in-backendaddresspool)VM 'lerin veya örneklerin hiçbiri sağlıklı değil.
* Kullanıcı istekleriyle [zaman aşımı veya bağlantı sorunları isteyin](#request-time-out) .

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Ağ güvenlik grubu, Kullanıcı tanımlı yol veya özel DNS sorunu

### <a name="cause"></a>Nedeni

Bir NSG, UDR veya özel DNS nedeniyle arka uca Erişim engellenirse, Application Gateway örnekleri arka uç havuzuna erişemez. Bu, araştırma hatalarına neden olur ve 502 hataya yol açar.

NSG/UDR, Application Gateway alt ağında veya uygulama VM 'lerinin dağıtıldığı alt ağda bulunabilir.

Benzer şekilde, VNet 'te özel bir DNS bulunması da sorunlara yol açabilir. Arka uç havuzu üyeleri için kullanılan bir FQDN, VNet için Kullanıcı tarafından yapılandırılan DNS sunucusu tarafından doğru şekilde çözümlenmeyebilir.

### <a name="solution"></a>Çözüm

Aşağıdaki adımlara giderek NSG, UDR ve DNS yapılandırmasını doğrulayın:

* Application Gateway alt ağıyla ilişkili NSG 'leri denetleyin. Arka uca iletişimin engellenmediğinden emin olun.
* Application Gateway alt ağıyla ilişkili UDR 'yi denetleyin. UDR 'nin trafiği arka uç alt ağından yönlendirmediğinden emin olun. Örneğin, ExpressRoute/VPN aracılığıyla Application Gateway alt ağına tanıtılan ağ sanal gereçlerine veya varsayılan yollara yönlendirmeyi denetleyin.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Etkin NSG 'yi denetleyin ve arka uç VM ile yönlendirin

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* VNet 'te özel DNS varlığını denetleyin. DNS, çıktıda VNet özelliklerinin ayrıntılarına bakılarak denetlenebilir.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Varsa, DNS sunucusunun arka uç havuzu üyesinin FQDN 'sini doğru şekilde çözümleyebildiğinden emin olun.

## <a name="problems-with-default-health-probe"></a>Varsayılan sistem durumu araştırmasıyla ilgili sorunlar

### <a name="cause"></a>Nedeni

502 hata, varsayılan sistem durumu araştırmasının arka uç VM 'lerine ulaşamamasına yönelik sık kullanılan göstergeler de olabilir.

Bir uygulama ağ geçidi örneği sağlandığında, BackendHttpSetting 'in özelliklerini kullanarak her BackendAddressPool için varsayılan bir sistem durumu araştırması otomatik olarak yapılandırılır. Bu araştırmayı ayarlamak için Kullanıcı girişi gerekmez. Özellikle, bir yük dengeleme kuralı yapılandırıldığında, BackendHttpSetting ve Backendavkaspool kuyruğu arasında bir ilişkilendirme yapılır. Bu ilişkilerin her biri için varsayılan bir araştırma yapılandırılır ve Application Gateway, BackendHttpSetting öğesinde belirtilen bağlantı noktasındaki BackendAddressPool içindeki her bir örneğe düzenli bir sistem durumu denetim bağlantısı başlatır. 

Aşağıdaki tabloda, varsayılan sistem durumu araştırmasıyla ilişkili değerler listelenmektedir:

| Araştırma özelliği | Değer | Açıklama |
| --- | --- | --- |
| Araştırma URL 'SI |`http://127.0.0.1/` |URL yolu |
| Interval |30 |Saniye cinsinden yoklama aralığı |
| Zaman aşımı |30 |Saniye cinsinden araştırma zaman aşımı |
| Sağlıksız eşik |3 |Araştırma yeniden deneme sayısı. Arka uç sunucusu, ardışık araştırma hatası sayısı uygun olmayan eşiğe ulaştığında aşağı olarak işaretlenir. |

### <a name="solution"></a>Çözüm

* Varsayılan bir sitenin yapılandırıldığından ve 127.0.0.1 ' de dinleme yapıldığından emin olun.
* BackendHttpSetting, 80 dışında bir bağlantı noktası belirtiyorsa, varsayılan site bu bağlantı noktasını dinlemek üzere yapılandırılmalıdır.
* `http://127.0.0.1:port` çağrısı, 200 HTTP sonuç kodunu döndürmelidir. Bu, 30 saniyelik zaman aşımı süresi içinde döndürülmelidir.
* Yapılandırılmış bağlantı noktasının açık olduğundan ve yapılandırılmış bağlantı noktasındaki gelen veya giden trafiği engelleyen güvenlik duvarı kuralları veya Azure ağ güvenlik grupları olmadığından emin olun.
* Azure klasik VM 'Ler veya bulut hizmeti bir FQDN veya genel IP ile kullanılıyorsa, karşılık gelen [bitiş noktasının](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) açık olduğundan emin olun.
* VM, Azure Resource Manager aracılığıyla yapılandırıldıysa ve uygulama ağ geçidinin dağıtıldığı VNet dışındaysa, istenen bağlantı noktasında erişime izin verecek şekilde bir [ağ güvenlik grubu](../virtual-network/security-overview.md) yapılandırılmalıdır.

## <a name="problems-with-custom-health-probe"></a>Özel durum araştırmasıyla ilgili sorunlar

### <a name="cause"></a>Nedeni

Özel sistem durumu araştırmaları, varsayılan yoklama davranışına ek esneklik sağlar. Özel yoklamalar kullandığınızda, arka uç havuzu örneğini sağlıksız olarak işaretlemeden önce araştırma aralığını, URL 'yi, test edilecek yolu ve kaç başarısız yanıt kabul edeceğini yapılandırabilirsiniz.

Aşağıdaki ek özellikler eklenmiştir:

| Araştırma özelliği | Açıklama |
| --- | --- |
| Ad |Araştırmanın adı. Bu ad, arka uç HTTP ayarlarındaki araştırmayı ifade etmek için kullanılır. |
| Protokol |Araştırmayı göndermek için kullanılan protokol. Araştırma, arka uç HTTP ayarlarında tanımlanan protokolü kullanır |
| Host |Araştırmanın gönderileceği ana bilgisayar adı. Yalnızca uygulama ağ geçidinde birden çok site yapılandırıldığında geçerlidir. Bu, VM ana bilgisayar adından farklıdır. |
| Yol |Araştırmanın göreli yolu. Geçerli yol '/' öğesinden başlar. Araştırma \<protokol\>://\<ana bilgisayar\>:\<bağlantı noktası\>\<yol\> |
| Interval |Saniye cinsinden yoklama aralığı. Bu iki ardışık yoklama arasındaki zaman aralığıdır. |
| Zaman aşımı |Saniye cinsinden araştırma zaman aşımı. Bu zaman aşımı süresi içinde geçerli bir yanıt alınmadıysa, araştırma başarısız olarak işaretlenir. |
| Sağlıksız eşik |Araştırma yeniden deneme sayısı. Arka uç sunucusu, ardışık araştırma hatası sayısı uygun olmayan eşiğe ulaştığında aşağı olarak işaretlenir. |

### <a name="solution"></a>Çözüm

Özel durum araştırmasının önceki tablo olarak doğru şekilde yapılandırıldığını doğrulayın. Yukarıdaki sorun giderme adımlarına ek olarak, aşağıdakileri de doğrulayın:

* Araştırmanın [kılavuza](application-gateway-create-probe-ps.md)göre doğru belirtildiğinden emin olun.
* Uygulama ağ geçidi, tek bir site için yapılandırılmışsa, varsayılan olarak, özel araştırmada yapılandırılmadığı sürece konak adı `127.0.0.1`olarak belirtilmelidir.
* Http://\<Host\>:\<bağlantı noktası\>\<Path\> için bir çağrının bir 200 HTTP sonuç kodu döndürdüğünden emin olun.
* Aralık, zaman aşımı ve Unhealtrivı eşiğinin kabul edilebilir aralıklar içinde olduğundan emin olun.
* HTTPS araştırması kullanıyorsanız, arka uç sunucusunun arka uç sunucusu üzerinde bir geri dönüş sertifikası yapılandırarak SNı gerektirmediğinden emin olun.

## <a name="request-time-out"></a>İstek zaman aşımı

### <a name="cause"></a>Nedeni

Bir Kullanıcı isteği alındığında, uygulama ağ geçidi, yapılandırılan kuralları isteğe uygular ve bir arka uç havuz örneğine yönlendirir. Arka uç örneğinden yanıt için yapılandırılabilir bir zaman aralığı bekler. Varsayılan olarak, bu Aralık **20** saniyedir. Uygulama ağ geçidi, bu aralıkta arka uç uygulamasından bir yanıt almazsa, Kullanıcı isteği bir 502 hatası alır.

### <a name="solution"></a>Çözüm

Application Gateway, bu ayarı, daha sonra farklı havuzlara uygulanabilen BackendHttpSetting aracılığıyla yapılandırmanıza olanak tanır. Farklı arka uç havuzlarında farklı BackendHttpSetting ve farklı bir istek zaman aşımı yapılandırılabilir.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Boş Backendavddresspool

### <a name="cause"></a>Nedeni

Uygulama ağ geçidinde arka uç adres havuzunda yapılandırılmış VM veya sanal makine ölçek kümesi yoksa, herhangi bir müşteri isteğini yönlendirebilir ve hatalı bir ağ geçidi hatası gönderir.

### <a name="solution"></a>Çözüm

Arka uç adres havuzunun boş olmadığından emin olun. Bu, PowerShell, CLı veya Portal aracılığıyla gerçekleştirilebilir.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Yukarıdaki cmdlet 'in çıktısı boş olmayan arka uç adres havuzu içermelidir. Aşağıdaki örnek, arka uç VM 'Leri için bir FQDN veya IP adresi ile yapılandırılan iki havuzu gösterir. Backendadddresspool sağlama durumu ' başarılı ' olmalıdır.

Backendavddresspoolstext:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Backendadddresspool içinde sağlıksız örnekler

### <a name="cause"></a>Nedeni

Backendadddresspool 'ın tüm örnekleri uygun değilse, uygulama ağ geçidi, Kullanıcı isteğini yönlendiren bir arka uca sahip olmaz. Bu, arka uç örneklerinin sağlıklı olması ancak gerekli Uygulamanın dağıtılmadığı durumlar da olabilir.

### <a name="solution"></a>Çözüm

Örneklerin sağlıklı olduğundan ve uygulamanın düzgün yapılandırıldığından emin olun. Arka uç örneklerinin aynı VNet 'teki başka bir VM 'den ping işlemine yanıt verebildiğini kontrol edin. Ortak bir uç noktasıyla yapılandırıldıysa, Web uygulamasına yönelik bir tarayıcı isteğinin serviceable olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Yukarıdaki adımlar sorunu çözmezse, bir [destek bileti](https://azure.microsoft.com/support/options/)açın.


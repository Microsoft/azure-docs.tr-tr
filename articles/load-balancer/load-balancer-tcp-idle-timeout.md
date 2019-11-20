---
title: Azure 'da yük dengeleyici TCP boşta kalma zaman aşımını yapılandırma
titleSuffix: Azure Load Balancer
description: Bu makalede, Azure Load Balancer TCP boşta kalma zaman aşımını nasıl yapılandıracağınızı öğrenin.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 530bfbe85a564b3dd517e14df819586dee332a78
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076976"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Azure Load Balancer için TCP boşta kalma zaman aşımı ayarlarını yapılandırma

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Varsayılan yapılandırmasında Azure Load Balancer, 4 dakikalık bir boşta kalma zaman aşımı ayarına sahiptir. İşlem yapılmayan bir süre, zaman aşımı değerinden uzunsa, TCP veya HTTP oturumunun istemci ile bulut hizmetiniz arasında korunduğundan emin olmaz.

Bağlantı kapalıyken, istemci uygulamanız şu hata iletisini alabilir: "temel alınan bağlantı kapatıldı: etkin tutulması beklenen bir bağlantı sunucu tarafından kapatıldı."

Ortak bir uygulama, TCP etkin tutma özelliğini kullanmaktır. Bu uygulama, bağlantının daha uzun bir süre için etkin kalmasını önler. Daha fazla bilgi için, bkz. bu [.NET örnekleri](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Etkin tut özelliği etkinken, paketler bağlantı üzerinde işlem yapılmayan dönemler sırasında gönderilir. Bu etkin tutma paketleri, boşta kalma zaman aşımı değerine ulaşılmamasını ve bağlantının uzun bir süre boyunca korunmasını güvence altına aldığından emin olur.

Bu ayar yalnızca gelen bağlantılar için geçerlidir. Bağlantıyı kaybetmekten kaçınmak için, boşta kalma zaman aşımı ayarından daha az bir aralığa sahip TCP etkin tutmayı yapılandırmanız veya boşta kalma zaman aşımı değerini artırmanız gerekir. Bu tür senaryoları desteklemek için yapılandırılabilir bir boşta kalma zaman aşımı desteği ekledik. Şimdi, 4 ila 30 dakikalık bir süre ayarlayabilirsiniz.

TCP etkin tutma, pil ömrünün kısıtlama olmadığı senaryolar için iyi bir şekilde çalışıyor. Mobil uygulamalar için önerilmez. Bir mobil uygulamada TCP etkin tutma kullanmak cihaz pilinin daha hızlı tükenmesini sağlayabilir.

![TCP zaman aşımı](./media/load-balancer-tcp-idle-timeout/image1.png)

Aşağıdaki bölümlerde, sanal makinelerde ve bulut hizmetlerinde boştaki zaman aşımı ayarlarının nasıl değiştirileceği açıklanır.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Örnek düzeyi genel IP 'niz için TCP zaman aşımını 15 dakikaya yapılandırın

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` isteğe bağlıdır. Ayarlanmamışsa, varsayılan zaman aşımı 4 dakikadır. Kabul edilebilir zaman aşımı aralığı 4 ila 30 dakikadır.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Bir sanal makinede Azure uç noktası oluştururken boşta kalma zaman aşımını ayarlama

Bir uç nokta için zaman aşımı ayarını değiştirmek için aşağıdakileri kullanın:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Boşta kalma zaman aşımı yapılandırmanızı almak için aşağıdaki komutu kullanın:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Yük dengeli bir uç nokta kümesindeki TCP zaman aşımını ayarlama

Uç noktalar yük dengeli bir uç nokta kümesinin parçasıysa, yük dengeli uç nokta kümesinde TCP zaman aşımı ayarlanmış olmalıdır. Örneğin:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Bulut hizmetleri için zaman aşımı ayarlarını değiştirme

Bulut hizmetinizi güncelleştirmek için Azure SDK kullanabilirsiniz. . Csdef dosyasında bulut hizmetleri için uç nokta ayarları yaparsınız. Bulut hizmeti dağıtımı için TCP zaman aşımının güncelleştirilmesi bir dağıtım yükseltmesi gerektirir. TCP zaman aşımı yalnızca genel bir IP için belirtilmişse özel durum geçerlidir. Genel IP ayarları. cscfg dosyasında bulunur ve bunları dağıtım güncelleştirme ve yükseltme ile güncelleştirebilirsiniz.

Uç nokta ayarları için. csdef değişiklikleri şunlardır:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Genel IP 'lerde zaman aşımı ayarı için. cscfg değişiklikleri şunlardır:

```xml
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
    </InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="rest-api-example"></a>REST API örneği

Hizmet yönetimi API 'sini kullanarak TCP boşta kalma zaman aşımını yapılandırabilirsiniz. `x-ms-version` üstbilgisinin sürüm `2014-06-01` veya üzeri olarak ayarlandığından emin olun. Bir dağıtımdaki tüm sanal makinelerde, belirtilen yük dengeli giriş uç noktalarının yapılandırmasını güncelleştirin.

### <a name="request"></a>İstek

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Yanıt

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>Sonraki adımlar

[İç Yük Dengeleyiciye genel bakış](load-balancer-internal-overview.md)

[Internet 'e yönelik yük dengeleyiciyi yapılandırmaya başlama](load-balancer-get-started-internet-arm-ps.md)

[Yük dengeleyici dağıtım modu yapılandırma](load-balancer-distribution-mode.md)

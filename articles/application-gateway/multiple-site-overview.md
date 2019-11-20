---
title: Azure Application Gateway 'de birden çok site barındırma
description: Bu makalede Azure Application Gateway çoklu site desteğine genel bir bakış sunulmaktadır.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/17/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 61184fc7415de3c142b460710cceefed61a25af3
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835333"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway birden çok site barındırma

Birden çok site barındırma, aynı uygulama ağ geçidi örneğinde birden fazla web uygulaması yapılandırmanızı sağlar. Bu özellik, bir Application Gateway 'e en çok 100 web sitesi ekleyerek dağıtımlarınız için daha verimli bir topoloji yapılandırmanıza olanak tanır. Her web sitesi, kendi arka uç havuzuna yönlendirilebilir. Aşağıdaki örnekte, uygulama ağ geçidi ContosoServerPool ve FabrikamServerPool adlı iki arka uç sunucu havuzundan contoso.com ve fabrikam.com için trafik sunmaktadır.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Kurallar, portalda listelendikleri sırayla işlenir. Temel dinleyiciyi yapılandırmadan önce çok siteli dinleyicileri yapılandırmanız önerilir.  Bu işlem, trafiğin doğru arka uca yönlendirilmesini güvence altına alır. Temel dinleyici listede ilk sıradaysa ve gelen bir istekle eşleşiyorsa, o dinleyici tarafından işlenir.

`http://contoso.com` için istekler ContosoServerPool’a ve `http://fabrikam.com` için istekler FabrikamServerPool’a yönlendirilir.

Benzer şekilde aynı üst etki alanının iki alt etki alanı, aynı uygulama ağ geçidi dağıtımında barındırılabilir. Alt etki alanı kullanım örnekleri, tek bir uygulama ağ geçidi dağıtımında barındırılan `http://blog.contoso.com` ve `http://app.contoso.com` öğelerini içerebilir.

## <a name="host-headers-and-server-name-indication-sni"></a>Barındırma üstbilgileri ve Sunucu Adı Belirtme (SNI)

Aynı altyapıda birden çok site barındırmayı etkinleştirmek için üç yaygın mekanizma bulunur.

1. Birden çok web uygulamasının her birini benzersiz bir IP adresinde barındırın.
2. Birden çok web uygulamasını aynı IP adresinde barındırmak için ana bilgisayar adını kullanın.
3. Birden çok web uygulamasını aynı IP adresinde barındırmak için farklı bağlantı noktalarını kullanın.

Şu anda bir uygulama ağ geçidi, üzerinde trafiği dinlediği tek bir genel IP adresi almaktadır. Bu nedenle, her biri kendi IP adresine sahip olan birden çok uygulama şu anda desteklenmemektedir. Application Gateway, her biri farklı bağlantı noktasında dinleme yapan birden çok uygulamanın barındırılmasını destekler, ancak bu senaryo uygulamaların standart olmayan bağlantı noktalarında trafiği kabul etmesini gerekli kılar ve çoğu zaman istenen bir yapılandırma değildir. Application Gateway, aynı genel IP adresinde ve bağlantı noktasında birden çok web sitesini barındırmak için HTTP 1.1 barındırma bilgilerini kullanır. Uygulama ağ geçidinde barındırılan siteler, Sunucu Adı Belirtme (SNI) uzantısına sahip SSL yük boşaltmayı da destekleyebilir. Bu senaryo, istemci tarayıcısının ve arka uç web grubunun RFC 6066’da belirtildiği gibi HTTP/1.1 ve TLS uzantısını desteklemesi gerektiği anlamına gelir.

## <a name="listener-configuration-element"></a>Dinleyici yapılandırma öğesi

Mevcut HTTPListener yapılandırma öğesi, ana bilgisayar adını ve sunucu adı belirtme öğelerini destekleyecek şekilde geliştirilmiştir. Bu öğe, uygulama ağ geçidi tarafından trafiği uygun arka uç havuzuna yönlendirmek için kullanılır. Aşağıdaki kod örneği, şablon dosyasındaki HttpListeners öğesinin kod parçacığıdır.

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Uçtan uca şablon tabanlı dağıtım için [birden çok site barındırma kullanan Resource Manager şablonunu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) ziyaret edebilirsiniz.

## <a name="routing-rule"></a>Yönlendirme kuralı

Yönlendirme kuralında yapılması gereken bir değişiklik yoktur. Uygun site dinleyicisini ilgili arka uç adres havuzuna bağlamak için 'Temel' yönlendirme kuralını kullanmaya devam etmeniz gerekir.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Sonraki adımlar

Birden çok site barındırma hakkında bilgi aldıktan sonra birden fazla web uygulamasını destekleyebilen uygulama ağ geçidi oluşturmak için [birden çok site barındırma kullanan uygulama ağ geçidi oluşturma](tutorial-multiple-sites-powershell.md) bölümüne gidin.


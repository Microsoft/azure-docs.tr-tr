---
title: Azure Application Gateway WebSocket desteği
description: Application Gateway, tüm ağ geçidi boyutlarında WebSocket için yerel destek sağlar. Kullanıcı tarafından yapılandırılabilir ayarlar yoktur.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130340"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Application Gateway 'de WebSocket desteğine genel bakış

Application Gateway, tüm ağ geçidi boyutlarında WebSocket için yerel destek sağlar. WebSocket desteğini isteğe bağlı olarak etkinleştirmek veya devre dışı bırakmak için kullanıcı tarafından yapılandırılabilen bir ayar yoktur. 

[RFC6455](https://tools.ietf.org/html/rfc6455) ' de standartlaştırılmış WebSocket protokolü, uzun süre çalışan bir TCP bağlantısı üzerinden sunucu ile istemci arasında tam çift yönlü iletişim olanağı sunar. Bu özellik, HTTP tabanlı uygulamalarda gerektiği şekilde yoklamaya gerek kalmadan çift yönlü olabilecek Web sunucusu ve istemcisi arasında daha etkileşimli bir iletişim sağlar. WebSocket 'in HTTP 'den farklı olarak düşük yükü vardır ve birden çok istek/yanıt için aynı TCP bağlantısını yeniden kullanabilir ve kaynakların daha verimli bir şekilde kullanılmasına neden olur. WebSocket protokolleri, 80 ve 443 geleneksel HTTP bağlantı noktalarında çalışmak üzere tasarlanmıştır.

WebSocket trafiğini almak için 80 veya 443 numaralı bağlantı noktasında standart HTTP dinleyicisini kullanmaya devam edebilirsiniz. WebSocket trafiği daha sonra uygulama ağ geçidi kurallarında belirtilen uygun arka uç havuzu kullanılarak WebSocket etkinleştirilmiş arka uç sunucusuna yönlendirilir. Arka uç sunucusu, [sistem durumu araştırması genel bakış](application-gateway-probe-overview.md) bölümünde açıklanan Application Gateway araştırmasına yanıt vermelidir. Application Gateway durum araştırmaları yalnızca HTTP/HTTPS. Her arka uç sunucusu, WebSocket trafiğini sunucuya yönlendirmek üzere uygulama ağ geçidi için HTTP araştırmasına yanıt vermelidir.

Bu, sohbet, pano ve oyun uygulamaları gibi hızlı, gerçek zamanlı iletişimden faydalanabilir uygulamalarda kullanılır.

## <a name="how-does-websocket-work"></a>WebSocket nasıl çalışır?

WebSocket bağlantısı kurmak için, istemci ve sunucu arasında belirli bir HTTP tabanlı el sıkışma alışverişi yapılır. Başarılı olursa, önceden oluşturulmuş TCP bağlantısı kullanılarak uygulama katmanı Protokolü HTTP 'den WebSockets 'e "yükseltilir". Bu gerçekleştiğinde, HTTP tamamen resmi değildir; WebSocket bağlantısı kapatılana kadar, veriler WebSocket protokolü ile her iki uç nokta tarafından gönderilebilir veya alınabilir. 

![WebSocket](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Dinleyici yapılandırma öğesi

Mevcut bir HTTP dinleyicisi, WebSocket trafiğini desteklemek için kullanılabilir. Örnek şablon dosyasından bir httpListeners öğesinin kod parçacığı aşağıda verilmiştir. WebSocket ve güvenli WebSocket trafiğini desteklemek için hem HTTP hem de HTTPS dinleyicilerine ihtiyacınız vardır. Benzer şekilde, WebSocket trafiğini desteklemek için bağlantı noktası 80/443 üzerinde dinleyicilerine sahip bir uygulama ağ geçidi oluşturmak için portalını veya Azure PowerShell kullanabilirsiniz.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Backendadddresspool, BackendHttpSetting ve yönlendirme kuralı yapılandırması

Bir Backendadddresspool, WebSocket etkin sunucularla bir arka uç havuzu tanımlamak için kullanılır. BackendHttpSetting, arka uç bağlantı noktası 80 ve 443 ile tanımlanmıştır. HTTP ayarlarındaki istek zaman aşımı değeri, WebSocket oturumu için de geçerlidir. Yönlendirme kuralında, ilgili dinleyiciyi ilgili arka uç adres havuzuna bağlamak için kullanılan bir değişiklik yoktur. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>WebSocket etkin arka uç

Arka ucunuzun, WebSocket 'in çalışması için yapılandırılmış bağlantı noktasında (genellikle 80/443) çalışan bir HTTP/HTTPS Web sunucusuna sahip olması gerekir. Bu gereksinim, WebSocket protokolünün bir başlık alanı olarak WebSocket protokolüne yükseltme ile ilk el sıkışma 'nın HTTP olmasını gerektirmesidir. Aşağıda bir üst bilgi örneği verilmiştir:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Bunun başka bir nedeni de uygulama ağ geçidi arka uç durumu araştırmasının yalnızca HTTP ve HTTPS protokollerini desteklemeleridir. Arka uç sunucusu HTTP veya HTTPS araştırmalara yanıt vermezse, arka uç havuzu kullanıma alınır.

## <a name="next-steps"></a>Sonraki adımlar

WebSocket desteği hakkında bilgi aldıktan sonra, WebSocket özellikli bir Web uygulamasını kullanmaya başlamak için [uygulama ağ geçidi oluşturma](quick-create-powershell.md) bölümüne gidin.
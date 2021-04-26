---
title: Bir giriş uç noktası için iç yönlendirme için özel IP adresi kullan
description: Bu makalede, iç yönlendirme için özel IP 'Lerin nasıl kullanılacağı ve bu nedenle bir küme içindeki giriş uç noktasının, VNet 'in geri kalanına nasıl yapılacağı hakkında bilgi sağlanır.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be37ed1da0da4da3db43ef4c1cd01ed962f24ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397317"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Bir giriş uç noktası için iç yönlendirme için özel IP kullan 

Bu özellik `Virtual Network` Özel BIR IP kullanarak giriş uç noktasını kullanıma sunmasına olanak tanır.

## <a name="pre-requisites"></a>Ön koşullar  
[Özel BIR IP yapılandırmasıyla](./configure-application-gateway-with-private-frontend-ip.md) Application Gateway

Denetleyiciyi, giriş için özel IP kullanmak üzere yapılandırmanın iki yolu vardır,

## <a name="assign-to-a-particular-ingress"></a>Belirli bir giriş için ata
Özel IP üzerinden belirli bir girişi göstermek için, giriş bölümünde ek açıklama kullanın [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) .

### <a name="usage"></a>Kullanım
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Özel IP olmayan uygulama ağ geçitleri için, ile ek açıklama eklenmiş olarak `appgw.ingress.kubernetes.io/use-private-ip: "true"` yoksayılır. Bu, giriş olayında ve AGIC Pod günlüğünde gösterilir.

* Giriş olayında gösterildiği gibi hata

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* AGIC günlüklerinde gösterildiği gibi hata

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Küresel olarak ata
Bu durumda, tüm giriş ve özel IP üzerinden gösterilmesini sınırlamak için gereksinim, `appgw.usePrivateIP: true` config içinde kullanın `helm` .

### <a name="usage"></a>Kullanım
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Bu, giriş denetleyicisini, Application Gateway ön uç dinleyicilerini yapılandırırken özel bir IP için IP adresi yapılandırmalarını filtreleyecek hale getirir.
AGIC, `usePrivateIP: true` özel IP atanmadığı takdirde, ve çöker.

> [!NOTE]
> Application Gateway v2 SKU 'SU için genel bir IP gerekir. Application Gateway özel olması gerekir, [`Network Security Group`](../virtual-network/network-security-groups-overview.md) trafiği kısıtlamak için Application Gateway alt ağına bağlayın.
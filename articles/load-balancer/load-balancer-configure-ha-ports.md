---
title: Azure Load Balancer için yüksek kullanılabilirliğe sahip bağlantı noktalarını yapılandırma
titlesuffix: Azure Load Balancer
description: Tüm bağlantı noktalarındaki iç trafik Yük Dengelemesi için yüksek kullanılabilirliğe sahip bağlantı noktalarını kullanmayı öğrenin
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c0cf1eb62c8e01988c9014478ff72816e45ea64c
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275625"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Bir iç load balancer için yüksek kullanılabilirliğe sahip bağlantı noktalarını yapılandırma

Bu makalede, bir iç yük dengeleyici üzerinde yüksek kullanılabilirlik bağlantı noktaları dağıtımının bir örneği sağlar. Ağ sanal Gereçleri (Nva) için belirli yapılandırmalar hakkında daha fazla bilgi için karşılık gelen sağlayıcı Web sitelerine bakın.

>[!NOTE]
>Azure Load Balancer iki farklı türü destekler: Temel ve Standart. Bu makalede, standart yük dengeleyici anlatılmaktadır. Temel Load Balancer hakkında daha fazla bilgi için bkz: [Load Balancer'a genel bakış](load-balancer-overview.md).

Çizim, bu makalede açıklanan dağıtım örneği aşağıdaki yapılandırmasını gösterir:

- Yüksek kullanılabilirlik bağlantı noktaları yapılandırmanın arkasındaki iç yük dengeleyici arka uç havuzu Nva'lara dağıtılır. 
- Kullanıcı tanımlı yol (UDR) üzerinde DMZ alt ağ yollarını tüm trafiği Nva'lar için sonraki atlama olarak iç yük dengeleyici sanal IP yaparak uygulanır. 
- İç yük dengeleyici, yük dengeleyici algoritmasının göre etkin Nva'lardan birine trafiği dağıtır.
- NVA, trafiği işler ve arka uç alt ağı özgün hedef iletir.
- Karşılık gelen bir UDR arka uç alt ağında yapılandırılmışsa dönüş yolu aynı yol alabilir. 

![Yüksek kullanılabilirlik bağlantı noktaları örnek dağıtım](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Yüksek kullanılabilirlik bağlantı noktalarını yapılandırma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Yüksek kullanılabilirliğe sahip bağlantı noktalarını yapılandırmak için nva'ları ile iç yük dengeleyici arka uç havuzunda ayarlayın. NVA sistem durumu ve yüksek kullanılabilirlik bağlantı noktaları ile yük dengeleyici kuralı algılamak için bir karşılık gelen yük dengeleyici sistem durumu araştırması yapılandırması ayarlayın. Genel yük dengeleyici ile ilgili yapılandırma bölümünde ele alınmıştır [başlama](load-balancer-get-started-ilb-arm-portal.md). Bu makalede, yüksek kullanılabilirliğe sahip bağlantı noktalarını yapılandırma vurgulanır.

Ön uç bağlantı noktası ve arka uç bağlantı noktası değerine ayarlayarak yapılandırmayı temelde içerir **0**. Protokol değerine **tüm**. Bu makalede Azure portalı, PowerShell ve Azure CLI kullanarak yüksek kullanılabilirliğe sahip bağlantı noktalarını yapılandırma.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Azure portalı ile yüksek kullanılabilirliğe sahip bağlantı noktalarını yük dengeleyici kuralı yapılandırma

Azure portalını kullanarak yüksek kullanılabilirliğe sahip bağlantı noktalarını yapılandırmak için seçin **HA bağlantı noktaları** onay kutusu. Bu onay kutusu seçildiğinde, ilgili bağlantı noktası ve protokol yapılandırma otomatik olarak doldurulur. 

![Azure portal aracılığıyla yüksek kullanılabilirliğe sahip bağlantı noktalarını yapılandırma](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Resource Manager şablonu aracılığıyla yüksek kullanılabilirlik bağlantı noktaları Yük Dengeleme kuralı yapılandırma

Yük Dengeleyici kaynağı için Microsoft.Network/loadBalancers 2017-08-01 API sürümünü kullanarak yüksek kullanılabilirliğe sahip bağlantı noktalarını yapılandırabilirsiniz. Aşağıdaki JSON kod parçacığında, yüksek kullanılabilirlik bağlantı noktaları için REST API aracılığıyla yük dengeleyici yapılandırmasında değişiklik gösterir:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>PowerShell ile yüksek kullanılabilirliğe sahip bağlantı noktalarını yük dengeleyici kuralı yapılandırma

PowerShell ile iç Yük Dengeleyiciyi oluşturma sırasında yüksek kullanılabilirlik bağlantı noktaları yük dengeleyici kuralı oluşturmak için aşağıdaki komutu kullanın:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Azure CLI ile yüksek kullanılabilirliğe sahip bağlantı noktalarını yük dengeleyici kuralı yapılandırma

4\. adımda [iç yük dengeleyici kümesi oluşturma](load-balancer-get-started-ilb-arm-cli.md), yüksek kullanılabilirlik bağlantı noktaları yük dengeleyici kuralı oluşturmak için aşağıdaki komutu kullanın:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinin [yüksek kullanılabilirlik bağlantı noktaları](load-balancer-ha-ports-overview.md).
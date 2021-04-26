---
title: Azure Load Balancer Dağıtım modunu yapılandırma
titleSuffix: Azure Load Balancer
description: Bu makalede, kaynak IP benzeşimini desteklemek üzere Azure Load Balancer için Dağıtım modunu yapılandırmaya başlayın.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 0c6b845a8176054dc5ec6cfc239e609f568c925d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483648"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Azure Load Balancer için Dağıtım modunu yapılandırın

Azure Load Balancer, uygulamalarınıza trafik dağıtmak için iki dağıtım modunu destekler:

* Karma tabanlı
* Kaynak IP benzeşimi

Bu makalede, Azure Load Balancer için Dağıtım modunu yapılandırmayı öğreneceksiniz.


## <a name="configure-distribution-mode"></a>Dağıtım modunu yapılandırma

---

# <a name="azure-portal"></a>[**Azure portal**](#tab/azure-portal)

Portalda Yük Dengeleme kuralını değiştirerek dağıtım modunun yapılandırmasını değiştirebilirsiniz.

1. Azure portal oturum açın ve **kaynak grupları**' na tıklayarak değiştirmek istediğiniz yük dengeleyiciyi içeren kaynak grubunu bulun.
2. Yük Dengeleyiciye genel bakış ekranında **Ayarlar**' ın altında **Yük Dengeleme kuralları** ' nı seçin.
3. Yük Dengeleme kuralları ekranında, Dağıtım modunu değiştirmek istediğiniz yük dengeleme kuralını seçin.
4. Kural altında, **oturum kalıcılığı** açılan kutusu değiştirilerek dağıtım modu değiştirilir. 

Aşağıdaki seçenekler kullanılabilir: 

* **Hiçbiri (karma tabanlı)** -aynı istemciden gelen ardışık isteklerin herhangi bir sanal makine tarafından işlenebileceğini belirtir.
* **ISTEMCI IP (kaynak IP benzeşimi iki demet)** -aynı istemci IP adresinden gelen isteklerin aynı sanal makine tarafından işleneceğini belirtir.
* **İstemci IP 'si ve Protokolü (kaynak IP benzeşimi üç demet)** -aynı istemci IP adresi ve protokol birleşimlerinden gelen ardışık isteklerin aynı sanal makine tarafından işleneceğini belirtir.

5. Dağıtım modunu seçin ve ardından **Kaydet**' i seçin.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Yük dengeleyici kuralında oturum kalıcılığını değiştirin." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mevcut bir yük dengeleme kuralında yük dengeleyici dağıtım ayarlarını değiştirmek için PowerShell 'i kullanın. Aşağıdaki komut Dağıtım modunu güncelleştirir: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

`LoadDistribution`Gerekli Yük Dengeleme türü için öğesinin değerini ayarlayın. 

* İki demet (kaynak IP ve hedef IP) yük dengelemesi için **SourceIP** 'yi belirtin. 

* Üç demet için **Sourceıpprotocol** (kaynak IP, hedef IP ve protokol türü) yük dengelemesi belirleyin. 

* Beş demet yük dengelemenin varsayılan davranışı için **varsayılan değer** belirtin.

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Mevcut bir yük dengeleme kuralında yük dengeleyici dağıtım ayarlarını değiştirmek için Azure CLı 'yi kullanın.  Aşağıdaki komut Dağıtım modunu güncelleştirir:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
`--load-distribution`Gereken Yük Dengeleme türü için değerini ayarlayın.

* İki demet (kaynak IP ve hedef IP) yük dengelemesi için **SourceIP** 'yi belirtin. 

* Üç demet için **Sourceıpprotocol** (kaynak IP, hedef IP ve protokol türü) yük dengelemesi belirleyin. 

* Beş demet yük dengelemenin varsayılan davranışı için **varsayılan değer** belirtin.

Bu makalede kullanılan komutla ilgili daha fazla bilgi için bkz. [az Network lb Rule Update](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Load Balancer’a genel bakış](load-balancer-overview.md)
* [İnternet 'e yönelik yük dengeleyici yapılandırma ile çalışmaya başlama](quickstart-load-balancer-standard-public-powershell.md)
* [Yük dengeleyiciniz için boşta TCP zaman aşımı ayarlarını yapılandırma](load-balancer-tcp-idle-timeout.md)

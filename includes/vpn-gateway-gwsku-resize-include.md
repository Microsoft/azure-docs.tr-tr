---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495761"
---
Bir Generation1 veya Generation2 SKU 'sunu yükseltmek veya indirgeyebilmeniz için `Resize-AzVirtualNetworkGateway` PowerShell cmdlet 'ini kullanabilirsiniz (tüm VpnGw SKU 'Ları temel SKU 'Lar dışında yeniden boyutlandırılabilir). Temel ağ geçidi SKU 'SU kullanıyorsanız, ağ geçidinizi yeniden boyutlandırmak [yerine bu yönergeleri kullanın](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) .

Aşağıdaki PowerShell örneği, VpnGw2 için yeniden boyutlandırılan bir ağ geçidi SKU 'SU gösterir.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Ayrıca, sanal ağ geçidinizin **yapılandırma** sayfasına gidip açılan listeden farklı bir SKU seçerek Azure Portal bir ağ geçidini yeniden boyutlandırabilirsiniz.

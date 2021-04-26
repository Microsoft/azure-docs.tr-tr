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
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010859"
---
`Resize-AzVirtualNetworkGateway`PowerShell cmdlet 'ini kullanarak bir Generation1 veya Generation2 SKU 'su yükseltebilir veya indirgeyede (tüm VpnGw SKU 'Ları temel SKU 'lar dışında yeniden boyutlandırılabilir). Temel ağ geçidi SKU 'SU kullanıyorsanız, ağ geçidinizi yeniden boyutlandırmak [yerine bu yönergeleri kullanın](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) .

Aşağıdaki PowerShell örneği, VpnGw2 için yeniden boyutlandırılan bir ağ geçidi SKU 'SU gösterir.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```
---
title: 'Azure VPN Gateway: P2S VPN istemcileri için özel yollar tanıtma'
description: Özel yolları Noktadan siteye istemcilerinize tanıtma adımları
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: a02bd5519b776a063646c11be2a34366fe429f99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89392400"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>P2S VPN istemcileri için özel yolları tanıtma

Tüm Noktadan siteye VPN istemcilerinize özel yollar tanıtmak isteyebilirsiniz. Örneğin, VNet 'iniz üzerinde depolama uç noktalarını etkinleştirdiyseniz ve uzak kullanıcıların bu depolama hesaplarına VPN bağlantısı üzerinden erişmesini istiyorsanız. Depolama hesabı trafiğinin, genel Internet değil VPN tünelinin üzerinden aktarılması için, depolama uç noktasının IP adresini tüm uzak kullanıcılarınıza tanıtabilirsiniz.

![Azure VPN Gateway Çok Siteli bağlantı örneği](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Özel yolları tanıtmak için

Özel yolları tanıtmak için öğesini kullanın `Set-AzVirtualNetworkGateway cmdlet` . Aşağıdaki örnekte, [contoso depolama hesabı tabloları](https://contoso.table.core.windows.net)için IP 'nin nasıl tanıtılyapılacağı gösterilmektedir.

1. *Contoso.Table.Core.Windows.net* ping YAPıN ve IP adresini aklınızda edin. Örnek:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Aşağıdaki PowerShell komutlarını çalıştırın:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Birden çok özel yol eklemek için, adresleri ayırmak için virgül ve boşluk kullanın. Örnek:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Özel yolları görüntülemek için

Özel yolları görüntülemek için aşağıdaki örneği kullanın:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Özel yolları silmek için

Özel yolları silmek için aşağıdaki örneği kullanın:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Sonraki adımlar

Ek P2S yönlendirme bilgileri için bkz. [Noktadan siteye yönlendirme hakkında](vpn-gateway-about-point-to-site-routing.md).

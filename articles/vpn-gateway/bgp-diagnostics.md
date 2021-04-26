---
title: BGP durumunu ve ölçümlerini görüntüleme
titleSuffix: Azure VPN Gateway
description: Sorun giderme için BGP ile ilgili önemli bilgileri görüntüleyin.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103148944"
---
# <a name="view-bgp-metrics-and-status"></a>BGP ölçümlerini ve durumunu görüntüleme

Azure portal veya Azure PowerShell kullanarak BGP ölçümlerini ve durumunu görüntüleyebilirsiniz.

## <a name="azure-portal"></a>Azure portalı

Azure portal BGP eşlerini, öğrenilen yolları ve tanıtılan yolları görüntüleyebilirsiniz. Ayrıca, bu verileri içeren. csv dosyalarını da indirebilirsiniz.

1. [Azure Portal](https://portal.azure.com), sanal ağ geçidinize gidin.
1. **İzleme** altında **BGP eşleri ' nı seçerek BGP** eşleri sayfasını açın.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Azure portal ölçümlerin ekran görüntüsü.":::

### <a name="learned-routes"></a>Öğrenilen rotalar

1. Portalda en fazla 50 öğrenilen yolu görüntüleyebilirsiniz.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Öğrenilen yolların ekran görüntüsü.":::

1. Öğrenilmiş rotalar dosyasını da indirebilirsiniz. 50 'den fazla öğrenilen yol varsa, bunların tümünü görüntülemenin tek yolu. csv dosyasını indirip görüntülemektir. İndirmek için, **öğrenilen yolları indir**' i seçin.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Öğrenilen yolların indirileceğini ekran görüntüsü.":::
1. Sonra dosyayı görüntüleyin.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="İndirilen öğrenilen yolların ekran görüntüsü.":::

### <a name="advertised-routes"></a>Tanıtılan yollar

1. Tanıtılan yolları görüntülemek için, görüntülemek istediğiniz ağın sonundaki **...** öğesini seçin ve ardından **tanıtılan yolları görüntüle**' ye tıklayın.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="Tanıtılan yolların nasıl görüntüleneceğini gösteren ekran görüntüsü." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. **Eşe tanıtılan yollar** sayfasında, en fazla 50 yol görüntüleyebilirsiniz.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Tanıtılan yolların ekran görüntüsü.":::
1. Ayrıca tanıtılan yollar dosyasını da indirebilirsiniz. 50 'den fazla tanıtılan yol varsa, bunların tümünü görüntülemenin tek yolu. csv dosyasını indirip görüntülemektir. İndirmek için **tanıtılan yolları indir**' i seçin.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="İndirilen tanıtılan rotaları seçme ekran görüntüsü.":::
1. Sonra dosyayı görüntüleyin.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="İndirilen tanıtılan yolların ekran görüntüsü.":::

### <a name="bgp-peers"></a>BGP eşleri

1. Portalda en fazla 50 BGP eşi görüntüleyebilirsiniz.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="BGP eşlerinin ekran görüntüsü.":::
1. BGP eşleri dosyasını da indirebilirsiniz. 50 ' den fazla BGP eşi varsa, bunların tümünü görüntülemenin tek yolu. csv dosyasını indirip görüntülemektir. İndirmek için portal sayfasında **BGP eşlerini indir** ' i seçin.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="BGP eşlerini indirme ekran görüntüsü.":::
1. Sonra dosyayı görüntüleyin.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="İndirilen BGP eşlerinin ekran görüntüsü.":::

## <a name="powershell"></a>PowerShell

Tüm BGP eşlerini ve durumunu görüntülemek için **Get-AzVirtualNetworkGatewayBGPPeerStatus** komutunu kullanın.

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

Ağ geçidinin BGP üzerinden öğrendiğini tüm yolları görüntülemek için **Get-AzVirtualNetworkGatewayLearnedRoute** komutunu kullanın.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

Ağ geçidinin BGP üzerinden eşlerine duyurdığı tüm yolları görüntülemek için **Get-AzVirtualNetworkGatewayAdvertisedRoute** komutunu kullanın.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="next-steps"></a>Sonraki adımlar

BGP hakkında daha fazla bilgi için bkz. [VPN Gateway BGP 'Yi yapılandırma](bgp-howto.md).
